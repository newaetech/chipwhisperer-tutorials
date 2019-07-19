
Tutorial: Recovering an AES key by Differential Fault Analysis attack
=====================================================================

This tutorial will introduce you to the Differential Fault Analysis
(DFA) attack. It will show you how to configure the XMEGA target to
perform AES encryptions, how to glitch the encryption operations and
introduce errors in the computed ciphertext and finally how to process
these glitched ciphertexts to extract the AES key. This can be used as
how-to for attacking other targets as well.

| Original author: [@doegox](https://twitter.com/doegox)
| License: CC-BY-SA
| Improvements are welcome!


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'TINYAES128C'

Prerequisites
-------------

On giants' shoulders
~~~~~~~~~~~~~~~~~~~~

This tutorial is the first to deal with DFA, nevertheless it was not
designed from scratch. It relies on multiple sources we strongly
encourage your to read as well for a better understanding of the
details.

-  The ``simpleserial-aes`` target firmware, which contains the software
   AES implementation used in other side-channel analysis tutorials such
   as `Using CW Analyzer for CPA
   Attack <PA_CPA_1-Using_CW-Analyzer_for_CPA_Attack.ipynb>`__. This is
   the implementation we will attack.
-  The glitching tutorials:
-  `Introduction to Glitch
   Attacks <Fault_1-Introduction_to_Clock_Glitch_Attacks.ipynb>`__,
   useful to understand the hardware implementation of the glitching
   module
-  `Tutorial CW305-3 Clock Glitching
   (wiki) <https://wiki.newae.com/Tutorial_CW305-3_Clock_Glitching>`__
   which also presented briefly the principles of glitching AES, but
   without exploiting the faults
-  The DFA attack itself: there is no DFA cryptanalysis code in the
   Chipwhisperer but we'll re-use a Python library the author of this
   tutorial wrote for attacking white-box implementations. It's called
   ``phoenixAES`` and it is available on
   `Github <https://github.com/SideChannelMarvels/JeanGrey>`__ and on
   PyPI.

Brief introduction to Differential Fault Analysis
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We'll only describe the general principle and the operational
constraints.

The principle is to repeat the same AES operation over and over and to
glitch its intermediate operations to get an output cryptographically
incorrect. There are many DFA algorithms which, depending on the nature
of the fault (single bit?, single byte?, how many faulted outputs can we
collect?...), are able to recover the last round key of the AES with
various computations that may be quite intensive.

This tutorial covers the recovering of the key of a simple AES-128
encryption.

We'll use a quite simple DFA published initially by Dusart, Letourneux
and Vivolo in 2002 which has nice properties. For a mathematical
deep-dive of the DFA we're using in this tutorial, you can read
`Differential Fault Analysis on White-box AES
Implementations <https://blog.quarkslab.com/differential-fault-analysis-on-white-box-aes-implementations.html>`__
as we will use the exact same DFA library. Moreover, the blog post
explains how to tackle DFA against AES decryption and how to attack more
than one round, which is required to attack AES-192 or AES-256.

AES-128 is made of 10 rounds, the last one is missing the *MixColumn*
operation, the only operation which brings diffusion, i.e. it's an
operation which makes a single byte of one round state affecting
multiple bytes in the next round, 4 bytes to be exact.

|aes\_operations.png| (source:
http://www.iis.ee.ethz.ch/~kgf/acacia/fig/aes.png)

So, if we inject a fault which affects a single byte between the last
two *MixColumn* operations, it will propagate and 4 of the 16 output
bytes will be wrong. We don't need to know precisely where we inject our
faults, we can simply observe the output and look for a 4-byte fault
with one of the 4 possible patterns. The attack is *differential*
because we observe the difference between the correct output and the
faulty outputs. We'll save you the maths but with 2 such faults on the
same column, there is a high probability to recover a quarter of the
round key, so with 4\*2 faults we can recover the entire round key. And
because the AES keyschedule is invertible, we can compute it backwards
and recover the first round key, which is by definition equal to the
AES-128 key.

So, our operational constraints are quite simple: be able to run several
times the same AES encryption, with the same key (doh!) and the same
plaintext input and be able to collect the ciphertexts. Note that
technically we don't need to know the value of the plaintext, we only
need it to be constant.

.. |aes\_operations.png| image:: img/aes_operations.png

Installing dependencies
~~~~~~~~~~~~~~~~~~~~~~~

Firstly, let's install ``phoenixAES`` in the current kernel environment:


**In [2]:**

.. code:: ipython3

    import sys
    !{sys.executable} -m pip install phoenixAES


**Out [2]:**



.. parsed-literal::

    Requirement already satisfied: phoenixAES in c:\users\user\appdata\local\programs\python\python37-32\lib\site-packages (0.0.2)
    


Target
------

Which target?
~~~~~~~~~~~~~

Let's recap. This tutorial is specifically focusing on: \* AES-128
encryption \* the Chipwhisperer Lite ARM XMEGA target \* the AVR crypto
lib software implementation of AES \* clock glitching

Other targets and AES implementations should be equally working as well
as power glitching. Obviously the glitching parameters will have to be
adapted to the corresponding target, which is often not that
straightforward.

Even if you run this tutorial on the same Chipwhisperer Lite target
hardware, you might have to alter slightly the glitching parameters to
be able to get working glitches. Glitching is so sensitive that running
twice the exact same attack hardly produce the exact same results.

Building the target firmware
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have the ``avr-gcc`` toolchain installed, you should be able to
build the ``simpleserial-aes-CW303`` firmware:


**In [3]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [3]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEARM.hex
    rm -f -- simpleserial-aes-CWLITEARM.eep
    rm -f -- simpleserial-aes-CWLITEARM.cof
    rm -f -- simpleserial-aes-CWLITEARM.elf
    rm -f -- simpleserial-aes-CWLITEARM.map
    rm -f -- simpleserial-aes-CWLITEARM.sym
    rm -f -- simpleserial-aes-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i aes.i aes-independant.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-aes-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEARM.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/aes.o objdir/aes-independant.o objdir/stm32f3_startup.o --output simpleserial-aes-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-aes-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5348	    532	   1484	   7364	   1cc4	simpleserial-aes-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



Attack setup
------------

CW-lite connection and target flashing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Connect to the Chipwhisperer:


**In [4]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"

Flash the target:


**In [5]:**

.. code:: ipython3

    fw_path = "../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex".format(PLATFORM)
    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5879 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5879 bytes
    


First execution
~~~~~~~~~~~~~~~

For the DFA attack, we need a constant plaintext (and constant key of
course). We could just use two bytearrays but let's use the CW API to
demonstrate its usage.


**In [6]:**

.. code:: ipython3

    ktp = cw.ktp.Basic()
    ktp.fixed_text = True
    ktp.fixed_key = True
    key, text = ktp.next()

| Assuming we want to record traces, let's capture the entire AES.
| It's useful to see which round(s) we'll glitch by tuning
  ``scope.glitch.ext_offset`` later.


**In [7]:**

.. code:: ipython3

    scope.clock.adc_src = "clkgen_x1"
    if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        scope.adc.samples = 20000
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        scope.adc.samples = 8000

Let's test our setup with a first execution, without fault. It will give
us the golden reference output.


**In [8]:**

.. code:: ipython3

    # make sure glitches are disabled (in case cells are re-run)
    scope.io.hs2 = "clkgen"
    
    trace = cw.capture_trace(scope, target, text, key)
    goldciph = trace.textout
    print("Plaintext: {}".format(text.hex()))
    print("Key:       {}".format(key.hex()))
    print("Ciphertext:{}".format(goldciph.hex()))


**Out [8]:**



.. parsed-literal::

    Plaintext: 000102030405060708090a0b0c0d0e0f
    Key:       2b7e151628aed2a6abf7158809cf4f3c
    Ciphertext:50fe67cc996d32b6da0937e99bafec60
    



**In [9]:**

.. code:: ipython3

    reset_target(scope)

Just to be sure, let's check...


**In [10]:**

.. code:: ipython3

    from Crypto.Cipher import AES
    aes = AES.new(bytes(key), AES.MODE_ECB)
    goldciph2 = aes.encrypt(bytes(text))
    print("Expected ciphertext:  {}".format(goldciph2.hex()))


**Out [10]:**



.. parsed-literal::

    Expected ciphertext:  50fe67cc996d32b6da0937e99bafec60
    


Let's draw the full AES execution


**In [11]:**

.. code:: ipython3

    %matplotlib inline
    import matplotlib.pyplot as plt
    plt.figure(figsize=(16,6))
    plt.plot(trace.wave)
    
    # add boxes around last rounds
    
    from matplotlib.lines import Line2D
    if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        plt.plot([12000, 12000, 13600, 13600, 12000], [-0.25, 0.25, 0.25, -0.25, -0.25], 'r:')
        plt.plot([13650, 13650, 15250, 15250, 13650], [-0.25, 0.25, 0.25, -0.25, -0.25], 'g:')
        plt.plot([15300, 15300, 16000, 16000, 15300], [-0.25, 0.25, 0.25, -0.25, -0.25], 'b:')
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        plt.plot([5200, 5200, 5900, 5900, 5200], [-0.25, 0.25, 0.25, -0.25, -0.25], 'r:')
        plt.plot([5900, 5900, 6600, 6600, 5900], [-0.25, 0.25, 0.25, -0.25, -0.25], 'g:')
        plt.plot([6600, 6600, 6800, 6800, 6600], [-0.25, 0.25, 0.25, -0.25, -0.25], 'b:')
    custom_lines = [Line2D([0], [0], color='r', ls=':'),
                        Line2D([0], [0], color='g', ls=':'),
                        Line2D([0], [0], color='b', ls=':'),]
    plt.legend(custom_lines, ['8th round', '9th round', '10th round'])
    
    plt.show()


**Out [11]:**


.. image:: output_25_0.png


We see clearly the 10 AES-128 rounds, the 10th round being smaller than
the others as there is no *MixColumn*.

First glitches
~~~~~~~~~~~~~~

To check the actual clock glitches with an oscilloscope, you can probe
the XTal pad which is connected to R72 and run the following function to
glitch all clock cycles during 2 seconds. Beware that the probe
influences slightly the signal and it's enough to require a different
tuning of the glitching parameters, so when you're attacking a target,
do it all with or all without the oscilloscope but avoid messing up your
setup! In this tutorial, parameters were tuned without attached probe.
Still, your board might require slightly different values.


**In [12]:**

.. code:: ipython3

    import time
    
    def test_glitches():
        if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
            scope.io.hs2 = "glitch"
            scope.glitch.clk_src = 'clkgen'
            scope.glitch.width=3.5
            scope.glitch.offset=34
            scope.glitch.trigger_src='continuous'
        elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
            scope.io.hs2 = "glitch"
            scope.glitch.clk_src = "clkgen"
            scope.glitch.width = -10.15625
            scope.glitch.offset = -39.84
            scope.glitch.trigger_src='continuous'
    
    def stop_test_glitches():
        scope.glitch.trigger_src='ext_single'
    
    test_glitches()
    time.sleep(2)
    stop_test_glitches()

Here is an example of five glitched clock cycles as seen with an
oscilloscope: |clock\_glitches.png|

.. |clock\_glitches.png| image:: img/clock_glitches.png

See how the actual width and offset values are rounded to the internal
step values.


**In [13]:**

.. code:: ipython3

    print(scope.glitch)


**Out [13]:**



.. parsed-literal::

    clk_src     = clkgen
    width       = -10.15625
    width_fine  = 0
    offset      = -39.84375
    offset_fine = 0
    trigger_src = ext_single
    arm_timing  = after_scope
    ext_offset  = 0
    repeat      = 1
    output      = clock_xor
    
    


Let's define a ``MIN_STEP`` equal to the internal step value, it'll be
our "unit" width and offset step and other values will be rounded to the
closest multiple.


**In [14]:**

.. code:: ipython3

    MIN_STEP=25/64

Let's see the effect of clock glitches on the AES execution.


**In [15]:**

.. code:: ipython3

    # Initial glitch parameters
    if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        scope.io.hs2 = "glitch"
        scope.glitch.clk_src = 'clkgen'
        scope.glitch.width=3.5
        scope.glitch.offset=34
        scope.glitch.trigger_src='ext_single'
        scope.glitch.ext_offset = 13400
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        scope.io.hs2 = "glitch"
        scope.glitch.clk_src = "clkgen"
        scope.glitch.width = -10.15625 + 1
        scope.glitch.offset = -39.84
        scope.glitch.ext_offset = 5400
        scope.glitch.repeat = 3
        scope.glitch.trigger_src='ext_single'
    
    # reset target
    reset_target(scope)
    time.sleep(0.1)
    
    trace = cw.capture_trace(scope, target, text, key)


**In [16]:**

.. code:: ipython3

    %matplotlib inline
    import matplotlib.pyplot as plt
    plt.figure(figsize=(16,6))
    plt.plot(trace.wave)
    plt.plot([scope.glitch.ext_offset, scope.glitch.ext_offset], [-0.5, 0.25], 'r:')
    plt.show()
    


**Out [16]:**


.. image:: output_37_0.png


| You should see a glitch in the power trace (blue) when the clock was
  glitched (red dotted line).
| As said earlier, glitch parameters may have to be adapted to your
  specific hardware. Our experience is that a good
  ``scope.glitch.width`` is one just a bit smaller than one producing a
  clearly visible glitch in the power trace. E.g. the trace above was
  created with ``scope.glitch.width=6*MIN_STEP`` and we'll use
  ``scope.glitch.width=5*MIN_STEP`` in our attack. If this is not
  precise enough, consider tuning ``scope.glitch.width_fine`` too.

Campain setup
~~~~~~~~~~~~~

Now, we'll prepare a campain of clock glitches to induce faults.

| The following code is a bit more complex than strictly needed but we
  want to be able to compute exactly how many executions will be
  performed depending on the ranges and steps of the variables we want
  to tune. This allows us to get a nice progress bar.
| We'll sample three different axes: \* ``scope.glitch.width``: clock
  glitch width \* ``scope.glitch.offset``: clock glitch offset \*
  ``scope.glitch.ext_offset``: offset since the initial trigger (to
  target the last rounds)


**In [17]:**

.. code:: ipython3

    from collections import namedtuple
    # named tuples to make it easier to change the scope of the test
    Range = namedtuple('Range', ['min', 'max', 'step'])
    
    import math
    
    # get control over logging in order to be able to mask target execution errors,
    # which can easily happen when glitching the target!
    import logging
    logging.basicConfig(level=logging.WARN)
    
    # Let's be prepared for user-provided ranges: rounding and checking consistency by ourselves.
    def apply_ranges():
        global width_range, width_range_steps
        global offset_range, offset_range_steps
        global extoffset_range, extoffset_range_steps
        width_step_sign = width_range.step/abs(width_range.step)
        offset_step_sign = offset_range.step/abs(offset_range.step)
        width_range = Range(width_range.min, width_range.max, round(width_range.step / MIN_STEP) * MIN_STEP)
        offset_range = Range(offset_range.min, offset_range.max, round(offset_range.step / MIN_STEP) * MIN_STEP)
        if abs(width_range.step) < MIN_STEP:
            step = width_step_sign*MIN_STEP
            logging.error('width_range.step too small, adjusting to {}'.format(step))
            width_range = Range(width_range.min, width_range.max, step)
        if abs(offset_range.step) < MIN_STEP:
            step = offset_step_sign*MIN_STEP
            logging.error('offset_range.step too small, adjusting to {}'.format(step))
            offset_range = Range(offset_range.min, offset_range.max, step)
        width_range_steps = math.ceil((width_range.max-width_range.min)/width_range.step)
        offset_range_steps = math.ceil((offset_range.max-offset_range.min)/offset_range.step)
        extoffset_range_steps = math.ceil((extoffset_range.max-extoffset_range.min)/extoffset_range.step)
        if width_range_steps < 0:
            step = -width_range.step
            logging.error('width_range.step has wrong sign, adjusting to {}'.format(step))
            width_range = Range(width_range.min, width_range.max, step)
            width_range_steps = -width_range_steps
        if offset_range_steps < 0:
            step = -offset_range.step
            logging.error('offset_range.step has wrong sign, adjusting to {}'.format(step))
            offset_range = Range(offset_range.min, offset_range.max, step)
            offset_range_steps = -offset_range_steps
        if extoffset_range_steps < 0:
            step = -extoffset_range.step
            logging.error('extoffset_range.step has wrong sign, adjusting to {}'.format(step))
            extoffset_range = Range(extoffset_range.min, extoffset_range.max, step)
            extoffset_range_steps = -extoffset_range_steps

This is not strictly required for the tutorial but here are few global
variables that you can tune to decide if, besides the DFA attack, you
want also to: \* ``GLITCH_RESULTS_FILEPATH``: record the ciphertexts in
a CSV file (string or None) \* ``TRACES_FILEPATH``: record the
consumption traces in a Numpy file (string or None)

The goal is to demonstrate various parts of the CW API that might help
you debugging real-life DFA campains.


**In [18]:**

.. code:: ipython3

    GLITCH_RESULTS_FILEPATH='/tmp/glitch_outputs.csv'
    TRACES_FILEPATH='/tmp/glitch_traces.npy'


**In [19]:**

.. code:: ipython3

    GLITCH_RESULTS_FILEPATH=None
    TRACES_FILEPATH=None

| The next cell defines the glitches campain.
| ``traces`` is the list of recorded traces, ``output`` the list of
  outputs, either errors (e.g. if the target crashed) or (faulty or
  correct) ciphertexts. To be able to display a table of the glitch
  results and the faulty ciphertexts, we'll store the interesting
  information in the list ``results``.


**In [20]:**

.. code:: ipython3

    def campaign():
        import time
        global traces, outputs, results
        traces = []
        outputs = []
        results = [['#', 'target output', 'width', 'offset', 'extoffset', 'interesting']]
    
        # Initial glitch parameters
        scope.io.hs2 = "glitch"
        scope.glitch.clk_src = 'clkgen'
        scope.glitch.trigger_src = 'ext_single'
        scope.glitch.repeat = glitch_repeat
        scope.glitch.width = width_range.min
        scope.glitch.offset = offset_range.min
        scope.glitch.ext_offset = extoffset_range.min
    
        if GLITCH_RESULTS_FILEPATH is not None:
            import csv
            f = open(GLITCH_RESULTS_FILEPATH, 'w')
            writer = csv.writer(f)
    
        # campain loop with progress bar
        from tqdm import tnrange, tqdm
        for i in tnrange(width_range_steps*offset_range_steps*extoffset_range_steps, desc='Capturing traces', file=sys.stdout):
    
            # reset target
            reset_target(scope)
    
            # not very useful in this case as we're using fixed key & text, but this demonstrates the API.
            key, text = ktp.next()
            logging.getLogger().setLevel(logging.ERROR)
    
            trace = cw.capture_trace(scope, target, text, key)
            if trace:
                # shall we acquire the trace?
                if TRACES_FILEPATH is not None:
                    traces.append(trace.wave)
    
            # read target output from the target's buffer
            # we know it can fail, so let's silent warnings for now
            
            output = trace.textout
            logging.getLogger().setLevel(logging.WARN)
    
            # at this stage, we consider any 32b output different from the reference as potentially interesting
            interesting = output is not None and len(output) == 16 and output != goldciph
    
            # let's record it
            if output is not None and len(output) == 16:
                r = bytes(output).hex()
            else:
                r = repr(output)
            data = [i, r, scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset, interesting]
            results.append(data)
    
            if GLITCH_RESULTS_FILEPATH is not None:
                writer.writerow(data)
            if interesting:
                outputs.append(output)
    
            # loop update: compute next set of parameters
            scope.glitch.ext_offset += extoffset_range.step
            if scope.glitch.ext_offset >= extoffset_range.max:
                scope.glitch.ext_offset = extoffset_range.min
                scope.glitch.offset += offset_range.step
                if scope.glitch.offset >= offset_range.max:
                    scope.glitch.offset = offset_range.min
                    scope.glitch.width += width_range.step
    
        # we're done
        if GLITCH_RESULTS_FILEPATH is not None:
            f.close()
    
        # optionally save traces to a file for later processing
        if TRACES_FILEPATH is not None:
            import numpy as np
            trace_array = np.asarray(traces)
            print()
            print('Saving traces to {}'.format(TRACES_FILEPATH))
            np.save(TRACES_FILEPATH, trace_array)

Attacking the 9th round
-----------------------

R9: Collecting faulty outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this attack, we'll try to glitch the 9th round:


**In [21]:**

.. code:: ipython3

    # for scope.glitch.width:
    if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        width_range = Range(5*MIN_STEP, 6*MIN_STEP, MIN_STEP)
        offset_range = Range(34.5, 35.5, MIN_STEP)
        extoffset_range = Range(13400, 14300, 10)
        glitch_repeat = 10
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        width_range = Range(-10, -10.5, MIN_STEP)
        offset_range = Range(-39.84, -40.5, MIN_STEP)
        extoffset_range = Range(5400, 6500, 3)
        glitch_repeat = 3
    
    # Example when applying an oscilloscope probe, its capacitance is cushioning glitches so we need to beef them
    #width_range = Range(9*MIN_STEP, 10*MIN_STEP, MIN_STEP)
    
    # for scope.glitch.offset:
    
    
    # for scope.glitch.ext_offset:
    
    
    # for scope.glitch.repeat:
    
    
    apply_ranges()


**Out [21]:**



.. parsed-literal::

    ERROR:root:width_range.step has wrong sign, adjusting to -0.390625
    ERROR:root:offset_range.step has wrong sign, adjusting to -0.390625
    


Yes, I know, ``width_range`` contains a single value in our setup, but
at least you're ready for scanning more values!

| The next cell runs the glitches campain. Till you don't disconnect the
  Chipwhisperer, you can re-run the campain and analyze its results
  several times.
| Even when the parameters are perfectly maintained, the glitch effects
  are never twice exactly the same and the results of our campains may
  vary quite a lot.
| Adjust these parameters if you don't get proper results. Roughly: \*
  Increase ``scope.glitch.width`` width and/or vary
  ``scope.glitch.offset`` if the output is never faulted \* Decrease
  ``scope.glitch.width`` width and/or vary ``scope.glitch.offset`` if
  there is no output (target crashed) \* Play also with
  ``scope.glitch.width_fine`` if needed \* Avoid increasing too much
  ``scope.glitch.repeat`` as you don't want to inject mutiple faults
  affecting several bytes at once \* Beware of the effect of an
  oscilloscope probe if you're monitoring the glitches

The goal is to collect as many *interesting* outputs as possible. An
*interesting* output at this stage is simply a 16-byte output different
from the reference output.


**In [22]:**

.. code:: ipython3

    campaign()


**Out [22]:**






Let's see the results:


**In [23]:**

.. code:: ipython3

    from terminaltables import AsciiTable
    table = AsciiTable(results)
    print(table.table)


**Out [23]:**



.. parsed-literal::

    +-----+----------------------------------+-----------+-----------+-----------+-------------+
    | #   | target output                    | width     | offset    | extoffset | interesting |
    +-----+----------------------------------+-----------+-----------+-----------+-------------+
    | 0   | 4cfe67cc996d3243da09b2e99b7bec60 | -10.15625 | -39.84375 | 5400      | True        |
    | 1   | 334ff9db1915f48ddbc6f09bb45b882b | -10.15625 | -39.84375 | 5403      | True        |
    | 2   | 06fecef7996656b56827afe92ea3ecb6 | -10.15625 | -39.84375 | 5406      | True        |
    | 3   | f0fe67b0996d5099dabbffe913a9ec60 | -10.15625 | -39.84375 | 5409      | True        |
    | 4   | None                             | -10.15625 | -39.84375 | 5412      | False       |
    | 5   | 50fe6766996db1b6da5637e908afec60 | -10.15625 | -39.84375 | 5415      | True        |
    | 6   | None                             | -10.15625 | -39.84375 | 5418      | False       |
    | 7   | None                             | -10.15625 | -39.84375 | 5421      | False       |
    | 8   | None                             | -10.15625 | -39.84375 | 5424      | False       |
    | 9   | 50fe24cc99a432b6cd0937e99bafeced | -10.15625 | -39.84375 | 5427      | True        |
    | 10  | 5cc9d9e6ab481f563ce19db4d6147f6e | -10.15625 | -39.84375 | 5430      | True        |
    | 11  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5433      | False       |
    | 12  | 50fe05cc99f732b61f0937e99bafecb5 | -10.15625 | -39.84375 | 5436      | True        |
    | 13  | 50ae67cc396d32b6da0937959bafd460 | -10.15625 | -39.84375 | 5439      | True        |
    | 14  | None                             | -10.15625 | -39.84375 | 5442      | False       |
    | 15  | 50f367cc4c6d32b6da0937f49baf7660 | -10.15625 | -39.84375 | 5445      | True        |
    | 16  | 501267cc896d32b6da0937689baf1f60 | -10.15625 | -39.84375 | 5448      | True        |
    | 17  | fb17f36cb2ad231274b681f49ba38ea7 | -10.15625 | -39.84375 | 5451      | True        |
    | 18  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5454      | False       |
    | 19  | None                             | -10.15625 | -39.84375 | 5457      | False       |
    | 20  | f543b8622da46cd6c12b97a9859074f2 | -10.15625 | -39.84375 | 5460      | True        |
    | 21  | None                             | -10.15625 | -39.84375 | 5463      | False       |
    | 22  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5466      | False       |
    | 23  | 50336f7911ff2e10d6789244f3035fe3 | -10.15625 | -39.84375 | 5469      | True        |
    | 24  | 50b967cc0e6d32b6da09375f9baf7760 | -10.15625 | -39.84375 | 5472      | True        |
    | 25  | 501567ccb46d32b6da0937869bafc560 | -10.15625 | -39.84375 | 5475      | True        |
    | 26  | a6e5673b926d9f8cda82b2b8961f0860 | -10.15625 | -39.84375 | 5478      | True        |
    | 27  | 76fe67cc996d32d8da0990e99b3eec60 | -10.15625 | -39.84375 | 5481      | True        |
    | 28  | None                             | -10.15625 | -39.84375 | 5484      | False       |
    | 29  | b7fe67cc996d321fda09c7e99ba6ec60 | -10.15625 | -39.84375 | 5487      | True        |
    | 30  | 6cfe679d996dfd26da86f9e9cf5aec60 | -10.15625 | -39.84375 | 5490      | True        |
    | 31  | None                             | -10.15625 | -39.84375 | 5493      | False       |
    | 32  | None                             | -10.15625 | -39.84375 | 5496      | False       |
    | 33  | 50fe6799996d44b6da0a37e9dcafec60 | -10.15625 | -39.84375 | 5499      | True        |
    | 34  | None                             | -10.15625 | -39.84375 | 5502      | False       |
    | 35  | 50fec6cc991e32b6210937e99bafecd9 | -10.15625 | -39.84375 | 5505      | True        |
    | 36  | 50fe6722996d59b6dac737e9e0afec60 | -10.15625 | -39.84375 | 5508      | True        |
    | 37  | None                             | -10.15625 | -39.84375 | 5511      | False       |
    | 38  | None                             | -10.15625 | -39.84375 | 5514      | False       |
    | 39  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5517      | False       |
    | 40  | 50fee5cc99bc32b6e80937e99bafeca5 | -10.15625 | -39.84375 | 5520      | True        |
    | 41  | None                             | -10.15625 | -39.84375 | 5523      | False       |
    | 42  | bb8978de6f98cd2be2a49654b350caff | -10.15625 | -39.84375 | 5526      | True        |
    | 43  | None                             | -10.15625 | -39.84375 | 5529      | False       |
    | 44  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5532      | False       |
    | 45  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5535      | False       |
    | 46  | 50fecbcc999b32b6340937e99bafecc8 | -10.15625 | -39.84375 | 5538      | True        |
    | 47  | debc98cd7fd27ddf9c399907a0f4257f | -10.15625 | -39.84375 | 5541      | True        |
    | 48  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5544      | False       |
    | 49  | 504177cc4b5232b60309379b9baf31c2 | -10.15625 | -39.84375 | 5547      | True        |
    | 50  | e46f05cc0df13213bb0930049be1bc8a | -10.15625 | -39.84375 | 5550      | True        |
    | 51  | 500a67cc396d32b6da0937c89bafab60 | -10.15625 | -39.84375 | 5553      | True        |
    | 52  | None                             | -10.15625 | -39.84375 | 5556      | False       |
    | 53  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5559      | False       |
    | 54  | None                             | -10.15625 | -39.84375 | 5562      | False       |
    | 55  | None                             | -10.15625 | -39.84375 | 5565      | False       |
    | 56  | None                             | -10.15625 | -39.84375 | 5568      | False       |
    | 57  | 4ffe67cc996d322ada09f4e99bdaec60 | -10.15625 | -39.84375 | 5571      | True        |
    | 58  | None                             | -10.15625 | -39.84375 | 5574      | False       |
    | 59  | 50fe67f5996d0eb6daec37e943afec60 | -10.15625 | -39.84375 | 5577      | True        |
    | 60  | None                             | -10.15625 | -39.84375 | 5580      | False       |
    | 61  | 50fe67df996d0ab6dac437e9ccafec60 | -10.15625 | -39.84375 | 5583      | True        |
    | 62  | 9fce270440cde8c4cfe4f46fe552d118 | -10.15625 | -39.84375 | 5586      | True        |
    | 63  | None                             | -10.15625 | -39.84375 | 5589      | False       |
    | 64  | 50fe6795996d8eb6da9337e924afec60 | -10.15625 | -39.84375 | 5592      | True        |
    | 65  | None                             | -10.15625 | -39.84375 | 5595      | False       |
    | 66  | 4075aafc2af5e7bacf142876ff5e94f4 | -10.15625 | -39.84375 | 5598      | True        |
    | 67  | 01680b97c420c4849425b3a1ae4a9b3a | -10.15625 | -39.84375 | 5601      | True        |
    | 68  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5604      | False       |
    | 69  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5607      | False       |
    | 70  | 50fe67d0996d40b6daff37e9d2afec60 | -10.15625 | -39.84375 | 5610      | True        |
    | 71  | 7f5115d94a83b68fdaa9218645a061f3 | -10.15625 | -39.84375 | 5613      | True        |
    | 72  | 50fe6702996de4b6dacc37e999afec60 | -10.15625 | -39.84375 | 5616      | True        |
    | 73  | 50fed541993ec8b607b337e98dafec18 | -10.15625 | -39.84375 | 5619      | True        |
    | 74  | None                             | -10.15625 | -39.84375 | 5622      | False       |
    | 75  | 50fe18cc999b32b6d10937e99bafece7 | -10.15625 | -39.84375 | 5625      | True        |
    | 76  | None                             | -10.15625 | -39.84375 | 5628      | False       |
    | 77  | None                             | -10.15625 | -39.84375 | 5631      | False       |
    | 78  | None                             | -10.15625 | -39.84375 | 5634      | False       |
    | 79  | 50ff67cc4b6d32b6da0937a79bafb260 | -10.15625 | -39.84375 | 5637      | True        |
    | 80  | None                             | -10.15625 | -39.84375 | 5640      | False       |
    | 81  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5643      | False       |
    | 82  | 50ad67cce06d32b6da0937f19bafed60 | -10.15625 | -39.84375 | 5646      | True        |
    | 83  | 29fe67cc996d3265da0969e99bf1ec60 | -10.15625 | -39.84375 | 5649      | True        |
    | 84  | None                             | -10.15625 | -39.84375 | 5652      | False       |
    | 85  | aefe67cc996d32d1da091fe99bebec60 | -10.15625 | -39.84375 | 5655      | True        |
    | 86  | 43fe67cc996d32d0da09e3e99b16ec60 | -10.15625 | -39.84375 | 5658      | True        |
    | 87  | None                             | -10.15625 | -39.84375 | 5661      | False       |
    | 88  | 7d1775a2f355abb59fa32980576d908e | -10.15625 | -39.84375 | 5664      | True        |
    | 89  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5667      | False       |
    | 90  | None                             | -10.15625 | -39.84375 | 5670      | False       |
    | 91  | None                             | -10.15625 | -39.84375 | 5673      | False       |
    | 92  | None                             | -10.15625 | -39.84375 | 5676      | False       |
    | 93  | 09dfb0a3bfe0d50ffa0a03c111af6bf8 | -10.15625 | -39.84375 | 5679      | True        |
    | 94  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5682      | False       |
    | 95  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5685      | False       |
    | 96  | None                             | -10.15625 | -39.84375 | 5688      | False       |
    | 97  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5691      | False       |
    | 98  | None                             | -10.15625 | -39.84375 | 5694      | False       |
    | 99  | None                             | -10.15625 | -39.84375 | 5697      | False       |
    | 100 | None                             | -10.15625 | -39.84375 | 5700      | False       |
    | 101 | None                             | -10.15625 | -39.84375 | 5703      | False       |
    | 102 | None                             | -10.15625 | -39.84375 | 5706      | False       |
    | 103 | None                             | -10.15625 | -39.84375 | 5709      | False       |
    | 104 | None                             | -10.15625 | -39.84375 | 5712      | False       |
    | 105 | None                             | -10.15625 | -39.84375 | 5715      | False       |
    | 106 | None                             | -10.15625 | -39.84375 | 5718      | False       |
    | 107 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5721      | False       |
    | 108 | None                             | -10.15625 | -39.84375 | 5724      | False       |
    | 109 | None                             | -10.15625 | -39.84375 | 5727      | False       |
    | 110 | None                             | -10.15625 | -39.84375 | 5730      | False       |
    | 111 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5733      | False       |
    | 112 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5736      | False       |
    | 113 | None                             | -10.15625 | -39.84375 | 5739      | False       |
    | 114 | None                             | -10.15625 | -39.84375 | 5742      | False       |
    | 115 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5745      | False       |
    | 116 | None                             | -10.15625 | -39.84375 | 5748      | False       |
    | 117 | None                             | -10.15625 | -39.84375 | 5751      | False       |
    | 118 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5754      | False       |
    | 119 | None                             | -10.15625 | -39.84375 | 5757      | False       |
    | 120 | None                             | -10.15625 | -39.84375 | 5760      | False       |
    | 121 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5763      | False       |
    | 122 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5766      | False       |
    | 123 | None                             | -10.15625 | -39.84375 | 5769      | False       |
    | 124 | None                             | -10.15625 | -39.84375 | 5772      | False       |
    | 125 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5775      | False       |
    | 126 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5778      | False       |
    | 127 | None                             | -10.15625 | -39.84375 | 5781      | False       |
    | 128 | None                             | -10.15625 | -39.84375 | 5784      | False       |
    | 129 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5787      | False       |
    | 130 | None                             | -10.15625 | -39.84375 | 5790      | False       |
    | 131 | None                             | -10.15625 | -39.84375 | 5793      | False       |
    | 132 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5796      | False       |
    | 133 | None                             | -10.15625 | -39.84375 | 5799      | False       |
    | 134 | None                             | -10.15625 | -39.84375 | 5802      | False       |
    | 135 | db13bab1a09f74b13a59a2c5c08f6eb6 | -10.15625 | -39.84375 | 5805      | True        |
    | 136 | None                             | -10.15625 | -39.84375 | 5808      | False       |
    | 137 | None                             | -10.15625 | -39.84375 | 5811      | False       |
    | 138 | None                             | -10.15625 | -39.84375 | 5814      | False       |
    | 139 | None                             | -10.15625 | -39.84375 | 5817      | False       |
    | 140 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5820      | False       |
    | 141 | None                             | -10.15625 | -39.84375 | 5823      | False       |
    | 142 | None                             | -10.15625 | -39.84375 | 5826      | False       |
    | 143 | None                             | -10.15625 | -39.84375 | 5829      | False       |
    | 144 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5832      | False       |
    | 145 | None                             | -10.15625 | -39.84375 | 5835      | False       |
    | 146 | None                             | -10.15625 | -39.84375 | 5838      | False       |
    | 147 | None                             | -10.15625 | -39.84375 | 5841      | False       |
    | 148 | None                             | -10.15625 | -39.84375 | 5844      | False       |
    | 149 | 5e11b7f239325dcd689509ab8e6cbd42 | -10.15625 | -39.84375 | 5847      | True        |
    | 150 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5850      | False       |
    | 151 | None                             | -10.15625 | -39.84375 | 5853      | False       |
    | 152 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5856      | False       |
    | 153 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5859      | False       |
    | 154 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5862      | False       |
    | 155 | None                             | -10.15625 | -39.84375 | 5865      | False       |
    | 156 | None                             | -10.15625 | -39.84375 | 5868      | False       |
    | 157 | None                             | -10.15625 | -39.84375 | 5871      | False       |
    | 158 | 41395c721dd01184d5ef3f35ac968a25 | -10.15625 | -39.84375 | 5874      | True        |
    | 159 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5877      | False       |
    | 160 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5880      | False       |
    | 161 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5883      | False       |
    | 162 | None                             | -10.15625 | -39.84375 | 5886      | False       |
    | 163 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5889      | False       |
    | 164 | None                             | -10.15625 | -39.84375 | 5892      | False       |
    | 165 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5895      | False       |
    | 166 | None                             | -10.15625 | -39.84375 | 5898      | False       |
    | 167 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5901      | False       |
    | 168 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5904      | False       |
    | 169 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5907      | False       |
    | 170 | None                             | -10.15625 | -39.84375 | 5910      | False       |
    | 171 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5913      | False       |
    | 172 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5916      | False       |
    | 173 | None                             | -10.15625 | -39.84375 | 5919      | False       |
    | 174 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5922      | False       |
    | 175 | None                             | -10.15625 | -39.84375 | 5925      | False       |
    | 176 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5928      | False       |
    | 177 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5931      | False       |
    | 178 | None                             | -10.15625 | -39.84375 | 5934      | False       |
    | 179 | None                             | -10.15625 | -39.84375 | 5937      | False       |
    | 180 | None                             | -10.15625 | -39.84375 | 5940      | False       |
    | 181 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5943      | False       |
    | 182 | None                             | -10.15625 | -39.84375 | 5946      | False       |
    | 183 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5949      | False       |
    | 184 | None                             | -10.15625 | -39.84375 | 5952      | False       |
    | 185 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5955      | False       |
    | 186 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5958      | False       |
    | 187 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5961      | False       |
    | 188 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5964      | False       |
    | 189 | None                             | -10.15625 | -39.84375 | 5967      | False       |
    | 190 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5970      | False       |
    | 191 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5973      | False       |
    | 192 | None                             | -10.15625 | -39.84375 | 5976      | False       |
    | 193 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5979      | False       |
    | 194 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5982      | False       |
    | 195 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5985      | False       |
    | 196 | None                             | -10.15625 | -39.84375 | 5988      | False       |
    | 197 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5991      | False       |
    | 198 | None                             | -10.15625 | -39.84375 | 5994      | False       |
    | 199 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 5997      | False       |
    | 200 | None                             | -10.15625 | -39.84375 | 6000      | False       |
    | 201 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6003      | False       |
    | 202 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6006      | False       |
    | 203 | None                             | -10.15625 | -39.84375 | 6009      | False       |
    | 204 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6012      | False       |
    | 205 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6015      | False       |
    | 206 | None                             | -10.15625 | -39.84375 | 6018      | False       |
    | 207 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6021      | False       |
    | 208 | None                             | -10.15625 | -39.84375 | 6024      | False       |
    | 209 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6027      | False       |
    | 210 | None                             | -10.15625 | -39.84375 | 6030      | False       |
    | 211 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6033      | False       |
    | 212 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6036      | False       |
    | 213 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6039      | False       |
    | 214 | None                             | -10.15625 | -39.84375 | 6042      | False       |
    | 215 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6045      | False       |
    | 216 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6048      | False       |
    | 217 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6051      | False       |
    | 218 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6054      | False       |
    | 219 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6057      | False       |
    | 220 | None                             | -10.15625 | -39.84375 | 6060      | False       |
    | 221 | None                             | -10.15625 | -39.84375 | 6063      | False       |
    | 222 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6066      | False       |
    | 223 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6069      | False       |
    | 224 | 421967c4ff6dfadfda8318213d9ab060 | -10.15625 | -39.84375 | 6072      | True        |
    | 225 | 50800601a3d984b64cc6374cceafe436 | -10.15625 | -39.84375 | 6075      | True        |
    | 226 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6078      | False       |
    | 227 | 50fe329b999a88b6c4ee37e9c5afecd2 | -10.15625 | -39.84375 | 6081      | True        |
    | 228 | 501c67cc8c6d32b6da0937479baf9d60 | -10.15625 | -39.84375 | 6084      | True        |
    | 229 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6087      | False       |
    | 230 | None                             | -10.15625 | -39.84375 | 6090      | False       |
    | 231 | None                             | -10.15625 | -39.84375 | 6093      | False       |
    | 232 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6096      | False       |
    | 233 | None                             | -10.15625 | -39.84375 | 6099      | False       |
    | 234 | dbfe67cc996d32bdda0917e99b86ec60 | -10.15625 | -39.84375 | 6102      | True        |
    | 235 | 5afe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6105      | True        |
    | 236 | None                             | -10.15625 | -39.84375 | 6108      | False       |
    | 237 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6111      | False       |
    | 238 | 46fe67cc996d32b6da0937e99b3eec60 | -10.15625 | -39.84375 | 6114      | True        |
    | 239 | dafe67cc996d32b6da0938e99b2fec60 | -10.15625 | -39.84375 | 6117      | True        |
    | 240 | 50fe67cc996d32b6da0937e99b8fec60 | -10.15625 | -39.84375 | 6120      | True        |
    | 241 | None                             | -10.15625 | -39.84375 | 6123      | False       |
    | 242 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6126      | False       |
    | 243 | None                             | -10.15625 | -39.84375 | 6129      | False       |
    | 244 | None                             | -10.15625 | -39.84375 | 6132      | False       |
    | 245 | None                             | -10.15625 | -39.84375 | 6135      | False       |
    | 246 | 50fe67cc996d32b6da0958e99bafec60 | -10.15625 | -39.84375 | 6138      | True        |
    | 247 | 50fe67cc996d32b6da09e8e99bafec60 | -10.15625 | -39.84375 | 6141      | True        |
    | 248 | 50fe67cc996d3229da0937e99bafec60 | -10.15625 | -39.84375 | 6144      | True        |
    | 249 | None                             | -10.15625 | -39.84375 | 6147      | False       |
    | 250 | 50fe67cc996d323dda0937e99bafec60 | -10.15625 | -39.84375 | 6150      | True        |
    | 251 | None                             | -10.15625 | -39.84375 | 6153      | False       |
    | 252 | None                             | -10.15625 | -39.84375 | 6156      | False       |
    | 253 | 50fe67cc996d32d2da0937e99bafec60 | -10.15625 | -39.84375 | 6159      | True        |
    | 254 | None                             | -10.15625 | -39.84375 | 6162      | False       |
    | 255 | 505c67cce16d32b6da09377a9baf3760 | -10.15625 | -39.84375 | 6165      | True        |
    | 256 | 50c667cc5b6d32b6da0937c09baf1760 | -10.15625 | -39.84375 | 6168      | True        |
    | 257 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6171      | False       |
    | 258 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6174      | False       |
    | 259 | 50fe67ccd46d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6177      | True        |
    | 260 | 170e4b2fc822819c01add50fd5f4e6ff | -10.15625 | -39.84375 | 6180      | True        |
    | 261 | 501967ccf36d32b6da0937e99bafdd60 | -10.15625 | -39.84375 | 6183      | True        |
    | 262 | 500167cce26d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6186      | True        |
    | 263 | None                             | -10.15625 | -39.84375 | 6189      | False       |
    | 264 | 50e767cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6192      | True        |
    | 265 | None                             | -10.15625 | -39.84375 | 6195      | False       |
    | 266 | None                             | -10.15625 | -39.84375 | 6198      | False       |
    | 267 | None                             | -10.15625 | -39.84375 | 6201      | False       |
    | 268 | 50fe67cc996d32b6da0937e99baf3a60 | -10.15625 | -39.84375 | 6204      | True        |
    | 269 | None                             | -10.15625 | -39.84375 | 6207      | False       |
    | 270 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6210      | False       |
    | 271 | 50fe67cc996d32b6da0937e99bafda60 | -10.15625 | -39.84375 | 6213      | True        |
    | 272 | 50fe67cc996d32b6da0937ce9bafec60 | -10.15625 | -39.84375 | 6216      | True        |
    | 273 | None                             | -10.15625 | -39.84375 | 6219      | False       |
    | 274 | 50fe67cc996d32b6da09370c9bafec60 | -10.15625 | -39.84375 | 6222      | True        |
    | 275 | 50fe67cc996d32b6da0937d79bafec60 | -10.15625 | -39.84375 | 6225      | True        |
    | 276 | None                             | -10.15625 | -39.84375 | 6228      | False       |
    | 277 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6231      | False       |
    | 278 | None                             | -10.15625 | -39.84375 | 6234      | False       |
    | 279 | 50fe76cc998932b6f50937e99bafec55 | -10.15625 | -39.84375 | 6237      | True        |
    | 280 | None                             | -10.15625 | -39.84375 | 6240      | False       |
    | 281 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6243      | False       |
    | 282 | 849385ee0bcc30c88835624e018d85a0 | -10.15625 | -39.84375 | 6246      | True        |
    | 283 | 50fe67cc996d32b6410937e99bafec60 | -10.15625 | -39.84375 | 6249      | True        |
    | 284 | 50fe67cc996d32b6b90937e99bafec60 | -10.15625 | -39.84375 | 6252      | True        |
    | 285 | 50fee2cc993b32b6940937e99bafec60 | -10.15625 | -39.84375 | 6255      | True        |
    | 286 | 50fe67cc999e32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6258      | True        |
    | 287 | None                             | -10.15625 | -39.84375 | 6261      | False       |
    | 288 | 50fe67cc993332b6da0937e99bafec60 | -10.15625 | -39.84375 | 6264      | True        |
    | 289 | None                             | -10.15625 | -39.84375 | 6267      | False       |
    | 290 | None                             | -10.15625 | -39.84375 | 6270      | False       |
    | 291 | None                             | -10.15625 | -39.84375 | 6273      | False       |
    | 292 | 50fea3cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6276      | True        |
    | 293 | None                             | -10.15625 | -39.84375 | 6279      | False       |
    | 294 | 50fe67cc996d32b6da0937e99bafecc7 | -10.15625 | -39.84375 | 6282      | True        |
    | 295 | 50fe1ecc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6285      | True        |
    | 296 | 50fe67cc996d32b6da0937e99bafec13 | -10.15625 | -39.84375 | 6288      | True        |
    | 297 | None                             | -10.15625 | -39.84375 | 6291      | False       |
    | 298 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6294      | False       |
    | 299 | 50fe67cc996d32b6da0937e99bafecdf | -10.15625 | -39.84375 | 6297      | True        |
    | 300 | b166af4d2cebd6e838ac6d1bcc91d9be | -10.15625 | -39.84375 | 6300      | True        |
    | 301 | 50fe67dc996de0b6dac137e9e1afec60 | -10.15625 | -39.84375 | 6303      | True        |
    | 302 | None                             | -10.15625 | -39.84375 | 6306      | False       |
    | 303 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6309      | False       |
    | 304 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6312      | False       |
    | 305 | 50fe67cc996d32b6da0937e94dafec60 | -10.15625 | -39.84375 | 6315      | True        |
    | 306 | bfd4fb9fe99212f2bdd3fbcc78c02cfc | -10.15625 | -39.84375 | 6318      | True        |
    | 307 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6321      | False       |
    | 308 | 50fe67cc996d32b6da5e37e922afec60 | -10.15625 | -39.84375 | 6324      | True        |
    | 309 | 50fe67cc996d22b6da3f37e9ffafec60 | -10.15625 | -39.84375 | 6327      | True        |
    | 310 | 50fe67cc996d32b6da1037e99bafec60 | -10.15625 | -39.84375 | 6330      | True        |
    | 311 | None                             | -10.15625 | -39.84375 | 6333      | False       |
    | 312 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6336      | False       |
    | 313 | None                             | -10.15625 | -39.84375 | 6339      | False       |
    | 314 | None                             | -10.15625 | -39.84375 | 6342      | False       |
    | 315 | None                             | -10.15625 | -39.84375 | 6345      | False       |
    | 316 | 50fe67cc996d44b6da0937e99bafec60 | -10.15625 | -39.84375 | 6348      | True        |
    | 317 | 50fe67cc996d1fb6da0937e99bafec60 | -10.15625 | -39.84375 | 6351      | True        |
    | 318 | 50fe6743996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6354      | True        |
    | 319 | None                             | -10.15625 | -39.84375 | 6357      | False       |
    | 320 | 50fe673b996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6360      | True        |
    | 321 | None                             | -10.15625 | -39.84375 | 6363      | False       |
    | 322 | None                             | -10.15625 | -39.84375 | 6366      | False       |
    | 323 | None                             | -10.15625 | -39.84375 | 6369      | False       |
    | 324 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6372      | False       |
    | 325 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6375      | False       |
    | 326 | None                             | -10.15625 | -39.84375 | 6378      | False       |
    | 327 | None                             | -10.15625 | -39.84375 | 6381      | False       |
    | 328 | None                             | -10.15625 | -39.84375 | 6384      | False       |
    | 329 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6387      | False       |
    | 330 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6390      | False       |
    | 331 | None                             | -10.15625 | -39.84375 | 6393      | False       |
    | 332 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6396      | False       |
    | 333 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6399      | False       |
    | 334 | None                             | -10.15625 | -39.84375 | 6402      | False       |
    | 335 | None                             | -10.15625 | -39.84375 | 6405      | False       |
    | 336 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6408      | False       |
    | 337 | None                             | -10.15625 | -39.84375 | 6411      | False       |
    | 338 | None                             | -10.15625 | -39.84375 | 6414      | False       |
    | 339 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6417      | False       |
    | 340 | None                             | -10.15625 | -39.84375 | 6420      | False       |
    | 341 | None                             | -10.15625 | -39.84375 | 6423      | False       |
    | 342 | 50111ca2fcecc1b65dec371b21afaefa | -10.15625 | -39.84375 | 6426      | True        |
    | 343 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6429      | False       |
    | 344 | 50fe67cc656d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6432      | True        |
    | 345 | None                             | -10.15625 | -39.84375 | 6435      | False       |
    | 346 | None                             | -10.15625 | -39.84375 | 6438      | False       |
    | 347 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6441      | False       |
    | 348 | None                             | -10.15625 | -39.84375 | 6444      | False       |
    | 349 | None                             | -10.15625 | -39.84375 | 6447      | False       |
    | 350 | 5074bb7ed2a5d6b6e46f376749aff03d | -10.15625 | -39.84375 | 6450      | True        |
    | 351 | None                             | -10.15625 | -39.84375 | 6453      | False       |
    | 352 | None                             | -10.15625 | -39.84375 | 6456      | False       |
    | 353 | 500ff80c154699b6ea2837ceb6afe010 | -10.15625 | -39.84375 | 6459      | True        |
    | 354 | None                             | -10.15625 | -39.84375 | 6462      | False       |
    | 355 | None                             | -10.15625 | -39.84375 | 6465      | False       |
    | 356 | 5016bb7ed2a5d6b6e46f376749afab3d | -10.15625 | -39.84375 | 6468      | True        |
    | 357 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6471      | False       |
    | 358 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6474      | False       |
    | 359 | None                             | -10.15625 | -39.84375 | 6477      | False       |
    | 360 | None                             | -10.15625 | -39.84375 | 6480      | False       |
    | 361 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6483      | False       |
    | 362 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6486      | False       |
    | 363 | None                             | -10.15625 | -39.84375 | 6489      | False       |
    | 364 | None                             | -10.15625 | -39.84375 | 6492      | False       |
    | 365 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625 | -39.84375 | 6495      | False       |
    | 366 | None                             | -10.15625 | -39.84375 | 6498      | False       |
    +-----+----------------------------------+-----------+-----------+-----------+-------------+
    


R9: Cryptanalysis of the faulty outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We'll use ``phoenixAES`` to perform the DFA against the collected
ciphertexts.

All it requires is the list of *interesting* outputs and the reference
output.


**In [24]:**

.. code:: ipython3

    import phoenixAES
    r10=phoenixAES.crack_bytes(outputs, goldciph, encrypt=True, verbose=2)


**Out [24]:**



.. parsed-literal::

    4cfe67cc996d3243da09b2e99b7bec60: group 0
    334ff9db1915f48ddbc6f09bb45b882b: group None
    06fecef7996656b56827afe92ea3ecb6: group None
    f0fe67b0996d5099dabbffe913a9ec60: group None
    50fe6766996db1b6da5637e908afec60: group 3
    50fe24cc99a432b6cd0937e99bafeced: group 2
    5cc9d9e6ab481f563ce19db4d6147f6e: group None
    50fe05cc99f732b61f0937e99bafecb5: group 2
    Round key bytes recovered:
    ....F9....EE....E1............A6
    50ae67cc396d32b6da0937959bafd460: group 1
    50f367cc4c6d32b6da0937f49baf7660: group 1
    Round key bytes recovered:
    ..14F9..C9EE....E1....C8....0CA6
    501267cc896d32b6da0937689baf1f60: group 1
    fb17f36cb2ad231274b681f49ba38ea7: group None
    f543b8622da46cd6c12b97a9859074f2: group None
    50336f7911ff2e10d6789244f3035fe3: group None
    50b967cc0e6d32b6da09375f9baf7760: group 1
    501567ccb46d32b6da0937869bafc560: group 1
    a6e5673b926d9f8cda82b2b8961f0860: group None
    76fe67cc996d32d8da0990e99b3eec60: group 0
    Round key bytes recovered:
    D014F9..C9EE..89E1..0CC8..630CA6
    b7fe67cc996d321fda09c7e99ba6ec60: group 0
    6cfe679d996dfd26da86f9e9cf5aec60: group None
    50fe6799996d44b6da0a37e9dcafec60: group 3
    Round key bytes recovered:
    D014F9A8C9EE2589E13F0CC8B6630CA6
    Last round key #N found:
    D014F9A8C9EE2589E13F0CC8B6630CA6
    


| In this first attack, we assume the fault was injected *between* the
  last two *MixColumn* operations and we look for ciphertexts only
  partially (25%) corrupted.
| We hope you managed to recover the full 10th round key. If not, you
  may try again `from here <#R9:-Collecting-faulty-outputs>`__ :) If you
  got very few or no "interesting" ciphertexts, better to tune
  ``width_range``.
| Once the last round key is recovered, you can revert the AES
  keyscheduling and reveal the actual AES key.


**In [25]:**

.. code:: ipython3

    key=None
    if r10 is not None:
        from chipwhisperer.analyzer.attacks.models.aes.key_schedule import key_schedule_rounds
        r9_key = key_schedule_rounds(bytearray.fromhex(r10), 10, 0)
        print("AES Key:")
        print(''.join("%02x" % x for x in r9_key))
    else:
        print("Sorry, no key found, try another campain, maybe with different parameters...")


**Out [25]:**



.. parsed-literal::

    AES Key:
    2b7e151628aed2a6abf7158809cf4f3c
    


R9: Plotting inner states differences
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once the AES key is known, we can display where the actual faults were
injected, here plotting the first 10 outputs.


**In [26]:**

.. code:: ipython3

    %run "Helper_Scripts/AES_differential_plotter.ipynb"
    if key is not None:
        ad=AesDiff(intext=text, key=r9_key, encrypt=True)
        for c in outputs[:10]:
            ad.add_glitch(c)
        plt=ad.plot_diff_bits()
        plt.show()

| This graph shows how many bits were flipped at each round. Of course
  the plot only makes sense from the lowest points of the curve to the
  right, there is no fault diffusion from the fault to the left.
| We're more interested in the number of bytes which are faulted before
  the last *MixColumn*:


**In [27]:**

.. code:: ipython3

    if key is not None:
        plt=ad.plot_diff_bytes()
        plt.show()

We managed to break the key because indeed a number of executions was
properly faulted on a single byte before the last *MixColumn*, cf the
lowest curves at position 8.

Attacking the 8th round
-----------------------

To reduce the number of required faults, we can inject glitches one
round earlier. If the faults are injected one *MixColumn* earlier, in
the 8th round, the ciphertext will be completely corrupted. But we can
still apply the same cryptanalysis! The trick is to convert one such
fault into four faults on the 9th round.

R8: Collecting faulty outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let's change our parameters to attack one round earlier and launch our
attack.


**In [28]:**

.. code:: ipython3

    # for scope.glitch.ext_offset:
    extoffset_range = Range(4700, 5400, 3)
    apply_ranges()
    campaign()


**Out [28]:**



Let's see the results:


**In [29]:**

.. code:: ipython3

    from terminaltables import AsciiTable
    table = AsciiTable(results)
    print(table.table)


**Out [29]:**



.. parsed-literal::

    +-----+----------------------------------+------------+-----------+-----------+-------------+
    | #   | target output                    | width      | offset    | extoffset | interesting |
    +-----+----------------------------------+------------+-----------+-----------+-------------+
    | 0   | f1da829fbdd3236c2e7fc9fcf9634519 | -10.15625  | -39.84375 | 4700      | True        |
    | 1   | ce406dbc915c9acc9e9101e9e2f15cec | -10.15625  | -39.84375 | 4703      | True        |
    | 2   | efbb2d463718da264c224967ef64dcda | -10.15625  | -39.84375 | 4706      | True        |
    | 3   | None                             | -10.15625  | -39.84375 | 4709      | False       |
    | 4   | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4712      | False       |
    | 5   | None                             | -10.15625  | -39.84375 | 4715      | False       |
    | 6   | None                             | -10.15625  | -39.84375 | 4718      | False       |
    | 7   | None                             | -10.15625  | -39.84375 | 4721      | False       |
    | 8   | 6af6130858d16c17ed74282a7f40edd9 | -10.15625  | -39.84375 | 4724      | True        |
    | 9   | ea45c4864db78ff31926a614e5415e44 | -10.15625  | -39.84375 | 4727      | True        |
    | 10  | e407cd4dbb375578b43cacccfc81e284 | -10.15625  | -39.84375 | 4730      | True        |
    | 11  | None                             | -10.15625  | -39.84375 | 4733      | False       |
    | 12  | 0a1c7b2e8a67e947cc77c13d1a874552 | -10.15625  | -39.84375 | 4736      | True        |
    | 13  | d3e1b1001f33a8b7d16525f78ac0dac7 | -10.15625  | -39.84375 | 4739      | True        |
    | 14  | None                             | -10.15625  | -39.84375 | 4742      | False       |
    | 15  | 3e3fc6746cf1c87c5d523d99fac87e7c | -10.15625  | -39.84375 | 4745      | True        |
    | 16  | None                             | -10.15625  | -39.84375 | 4748      | False       |
    | 17  | None                             | -10.15625  | -39.84375 | 4751      | False       |
    | 18  | None                             | -10.15625  | -39.84375 | 4754      | False       |
    | 19  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4757      | False       |
    | 20  | 07dbc730c1d91cf115af99e194dc1726 | -10.15625  | -39.84375 | 4760      | True        |
    | 21  | 3078760acbd1c26439a8793be67c06d0 | -10.15625  | -39.84375 | 4763      | True        |
    | 22  | e2093052fc69c8aaed278514f7d58fb9 | -10.15625  | -39.84375 | 4766      | True        |
    | 23  | be924a43e8c0db57c5bf203dbb8c38a4 | -10.15625  | -39.84375 | 4769      | True        |
    | 24  | fa957bfe9e22579a8dda7cf18a76090e | -10.15625  | -39.84375 | 4772      | True        |
    | 25  | None                             | -10.15625  | -39.84375 | 4775      | False       |
    | 26  | eb33686b4eaa2211b5bc589801bfcb29 | -10.15625  | -39.84375 | 4778      | True        |
    | 27  | af694be7a64768066d185be173186214 | -10.15625  | -39.84375 | 4781      | True        |
    | 28  | None                             | -10.15625  | -39.84375 | 4784      | False       |
    | 29  | None                             | -10.15625  | -39.84375 | 4787      | False       |
    | 30  | 0e603da4d331b8c906c960e8dd6fb7c6 | -10.15625  | -39.84375 | 4790      | True        |
    | 31  | None                             | -10.15625  | -39.84375 | 4793      | False       |
    | 32  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4796      | False       |
    | 33  | cb3f92712fc6c5bc0a4413503ba8ab02 | -10.15625  | -39.84375 | 4799      | True        |
    | 34  | 52ec1d7b66ea022283d5cc79fbdd5186 | -10.15625  | -39.84375 | 4802      | True        |
    | 35  | None                             | -10.15625  | -39.84375 | 4805      | False       |
    | 36  | d5e73bc993ebbc2cbd94ddeac6a088fe | -10.15625  | -39.84375 | 4808      | True        |
    | 37  | e3225df70e02183f1788ad9e0d4da61e | -10.15625  | -39.84375 | 4811      | True        |
    | 38  | None                             | -10.15625  | -39.84375 | 4814      | False       |
    | 39  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4817      | False       |
    | 40  | None                             | -10.15625  | -39.84375 | 4820      | False       |
    | 41  | 4e561ab586f22021ba5277850d6b3ba9 | -10.15625  | -39.84375 | 4823      | True        |
    | 42  | None                             | -10.15625  | -39.84375 | 4826      | False       |
    | 43  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4829      | False       |
    | 44  | f1d0990369fcda782ba273fd5888cb82 | -10.15625  | -39.84375 | 4832      | True        |
    | 45  | 893f3619969621eee104fcee3ed24dec | -10.15625  | -39.84375 | 4835      | True        |
    | 46  | bff7c23b31e0d65d12b0cbab2398b595 | -10.15625  | -39.84375 | 4838      | True        |
    | 47  | dd591aded17c5b6fb47e5f954c188520 | -10.15625  | -39.84375 | 4841      | True        |
    | 48  | c8caa53e5a170d7f0e80f801ac341b2e | -10.15625  | -39.84375 | 4844      | True        |
    | 49  | None                             | -10.15625  | -39.84375 | 4847      | False       |
    | 50  | 7b593005d6e3a833eed39dbf2921ffca | -10.15625  | -39.84375 | 4850      | True        |
    | 51  | None                             | -10.15625  | -39.84375 | 4853      | False       |
    | 52  | None                             | -10.15625  | -39.84375 | 4856      | False       |
    | 53  | None                             | -10.15625  | -39.84375 | 4859      | False       |
    | 54  | 1f0c0ea8d8d5ee9f8dd34e3b5a25e9a3 | -10.15625  | -39.84375 | 4862      | True        |
    | 55  | 53f1b5a3c33c7dc55139b58b51654385 | -10.15625  | -39.84375 | 4865      | True        |
    | 56  | b0c92aede57b9c8b87839d349a3ebc6e | -10.15625  | -39.84375 | 4868      | True        |
    | 57  | 596130fe526b122edb10d48db07996d7 | -10.15625  | -39.84375 | 4871      | True        |
    | 58  | 795baaa8daf598bfcfa0aae229cf89f4 | -10.15625  | -39.84375 | 4874      | True        |
    | 59  | None                             | -10.15625  | -39.84375 | 4877      | False       |
    | 60  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4880      | False       |
    | 61  | 94783d97f531c4d306259c12ae7736c6 | -10.15625  | -39.84375 | 4883      | True        |
    | 62  | 59174e956846af3a3a55d191557d20a1 | -10.15625  | -39.84375 | 4886      | True        |
    | 63  | 692023104c47fa4d8c33ac25101318f9 | -10.15625  | -39.84375 | 4889      | True        |
    | 64  | None                             | -10.15625  | -39.84375 | 4892      | False       |
    | 65  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4895      | False       |
    | 66  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4898      | False       |
    | 67  | 8d6803c1c4a594da97d931a1ac039b58 | -10.15625  | -39.84375 | 4901      | True        |
    | 68  | 5414304c8a95038af2889605b34e9983 | -10.15625  | -39.84375 | 4904      | True        |
    | 69  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4907      | False       |
    | 70  | f958af6f1d17a848e9a492fc2458cf72 | -10.15625  | -39.84375 | 4910      | True        |
    | 71  | 4ad0394eb2026b5af85f2977fc7d5861 | -10.15625  | -39.84375 | 4913      | True        |
    | 72  | 9f43cb852e9b51d434ff7b7cce7fa8c8 | -10.15625  | -39.84375 | 4916      | True        |
    | 73  | None                             | -10.15625  | -39.84375 | 4919      | False       |
    | 74  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4922      | False       |
    | 75  | None                             | -10.15625  | -39.84375 | 4925      | False       |
    | 76  | None                             | -10.15625  | -39.84375 | 4928      | False       |
    | 77  | None                             | -10.15625  | -39.84375 | 4931      | False       |
    | 78  | 47745216494521df8e510fa7c791316d | -10.15625  | -39.84375 | 4934      | True        |
    | 79  | 0828c8b9707aeebd3e15176a9c4434c6 | -10.15625  | -39.84375 | 4937      | True        |
    | 80  | 83472b2cde263e5335691c86ffaa482e | -10.15625  | -39.84375 | 4940      | True        |
    | 81  | None                             | -10.15625  | -39.84375 | 4943      | False       |
    | 82  | 26654ef4c6c7b4810b7f79e7a1b27b30 | -10.15625  | -39.84375 | 4946      | True        |
    | 83  | None                             | -10.15625  | -39.84375 | 4949      | False       |
    | 84  | c971b80d301624436e89b70abac72e6d | -10.15625  | -39.84375 | 4952      | True        |
    | 85  | ffdc1d29d99bc8da88269620976a737a | -10.15625  | -39.84375 | 4955      | True        |
    | 86  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4958      | False       |
    | 87  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4961      | False       |
    | 88  | None                             | -10.15625  | -39.84375 | 4964      | False       |
    | 89  | None                             | -10.15625  | -39.84375 | 4967      | False       |
    | 90  | None                             | -10.15625  | -39.84375 | 4970      | False       |
    | 91  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4973      | False       |
    | 92  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4976      | False       |
    | 93  | None                             | -10.15625  | -39.84375 | 4979      | False       |
    | 94  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4982      | False       |
    | 95  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4985      | False       |
    | 96  | None                             | -10.15625  | -39.84375 | 4988      | False       |
    | 97  | None                             | -10.15625  | -39.84375 | 4991      | False       |
    | 98  | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 4994      | False       |
    | 99  | None                             | -10.15625  | -39.84375 | 4997      | False       |
    | 100 | None                             | -10.15625  | -39.84375 | 5000      | False       |
    | 101 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5003      | False       |
    | 102 | None                             | -10.15625  | -39.84375 | 5006      | False       |
    | 103 | None                             | -10.15625  | -39.84375 | 5009      | False       |
    | 104 | 01ff028e9e9465ac0be27fac4140d600 | -10.15625  | -39.84375 | 5012      | True        |
    | 105 | None                             | -10.15625  | -39.84375 | 5015      | False       |
    | 106 | None                             | -10.15625  | -39.84375 | 5018      | False       |
    | 107 | None                             | -10.15625  | -39.84375 | 5021      | False       |
    | 108 | fac121f50c5bc2733f8de9b8b287aa67 | -10.15625  | -39.84375 | 5024      | True        |
    | 109 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5027      | False       |
    | 110 | None                             | -10.15625  | -39.84375 | 5030      | False       |
    | 111 | None                             | -10.15625  | -39.84375 | 5033      | False       |
    | 112 | None                             | -10.15625  | -39.84375 | 5036      | False       |
    | 113 | None                             | -10.15625  | -39.84375 | 5039      | False       |
    | 114 | None                             | -10.15625  | -39.84375 | 5042      | False       |
    | 115 | None                             | -10.15625  | -39.84375 | 5045      | False       |
    | 116 | None                             | -10.15625  | -39.84375 | 5048      | False       |
    | 117 | None                             | -10.15625  | -39.84375 | 5051      | False       |
    | 118 | 054ab0555735328e1479b2021a1e2d5f | -10.15625  | -39.84375 | 5054      | True        |
    | 119 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5057      | False       |
    | 120 | None                             | -10.15625  | -39.84375 | 5060      | False       |
    | 121 | None                             | -10.15625  | -39.84375 | 5063      | False       |
    | 122 | None                             | -10.15625  | -39.84375 | 5066      | False       |
    | 123 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5069      | False       |
    | 124 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5072      | False       |
    | 125 | None                             | -10.15625  | -39.84375 | 5075      | False       |
    | 126 | None                             | -10.15625  | -39.84375 | 5078      | False       |
    | 127 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5081      | False       |
    | 128 | None                             | -10.15625  | -39.84375 | 5084      | False       |
    | 129 | None                             | -10.15625  | -39.84375 | 5087      | False       |
    | 130 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5090      | False       |
    | 131 | None                             | -10.15625  | -39.84375 | 5093      | False       |
    | 132 | None                             | -10.15625  | -39.84375 | 5096      | False       |
    | 133 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5099      | False       |
    | 134 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5102      | False       |
    | 135 | None                             | -10.15625  | -39.84375 | 5105      | False       |
    | 136 | None                             | -10.15625  | -39.84375 | 5108      | False       |
    | 137 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5111      | False       |
    | 138 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5114      | False       |
    | 139 | None                             | -10.15625  | -39.84375 | 5117      | False       |
    | 140 | None                             | -10.15625  | -39.84375 | 5120      | False       |
    | 141 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5123      | False       |
    | 142 | None                             | -10.15625  | -39.84375 | 5126      | False       |
    | 143 | None                             | -10.15625  | -39.84375 | 5129      | False       |
    | 144 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5132      | False       |
    | 145 | None                             | -10.15625  | -39.84375 | 5135      | False       |
    | 146 | None                             | -10.15625  | -39.84375 | 5138      | False       |
    | 147 | None                             | -10.15625  | -39.84375 | 5141      | False       |
    | 148 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5144      | False       |
    | 149 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5147      | False       |
    | 150 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5150      | False       |
    | 151 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5153      | False       |
    | 152 | None                             | -10.15625  | -39.84375 | 5156      | False       |
    | 153 | None                             | -10.15625  | -39.84375 | 5159      | False       |
    | 154 | None                             | -10.15625  | -39.84375 | 5162      | False       |
    | 155 | None                             | -10.15625  | -39.84375 | 5165      | False       |
    | 156 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5168      | False       |
    | 157 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5171      | False       |
    | 158 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5174      | False       |
    | 159 | None                             | -10.15625  | -39.84375 | 5177      | False       |
    | 160 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5180      | False       |
    | 161 | None                             | -10.15625  | -39.84375 | 5183      | False       |
    | 162 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5186      | False       |
    | 163 | None                             | -10.15625  | -39.84375 | 5189      | False       |
    | 164 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5192      | False       |
    | 165 | None                             | -10.15625  | -39.84375 | 5195      | False       |
    | 166 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5198      | False       |
    | 167 | None                             | -10.15625  | -39.84375 | 5201      | False       |
    | 168 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5204      | False       |
    | 169 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5207      | False       |
    | 170 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5210      | False       |
    | 171 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5213      | False       |
    | 172 | None                             | -10.15625  | -39.84375 | 5216      | False       |
    | 173 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5219      | False       |
    | 174 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5222      | False       |
    | 175 | None                             | -10.15625  | -39.84375 | 5225      | False       |
    | 176 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5228      | False       |
    | 177 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5231      | False       |
    | 178 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5234      | False       |
    | 179 | None                             | -10.15625  | -39.84375 | 5237      | False       |
    | 180 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5240      | False       |
    | 181 | None                             | -10.15625  | -39.84375 | 5243      | False       |
    | 182 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5246      | False       |
    | 183 | None                             | -10.15625  | -39.84375 | 5249      | False       |
    | 184 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5252      | False       |
    | 185 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5255      | False       |
    | 186 | None                             | -10.15625  | -39.84375 | 5258      | False       |
    | 187 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5261      | False       |
    | 188 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5264      | False       |
    | 189 | None                             | -10.15625  | -39.84375 | 5267      | False       |
    | 190 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5270      | False       |
    | 191 | 643fedff32459d3ff14dc3b3e230c7e5 | -10.15625  | -39.84375 | 5273      | True        |
    | 192 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5276      | False       |
    | 193 | None                             | -10.15625  | -39.84375 | 5279      | False       |
    | 194 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5282      | False       |
    | 195 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5285      | False       |
    | 196 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5288      | False       |
    | 197 | None                             | -10.15625  | -39.84375 | 5291      | False       |
    | 198 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5294      | False       |
    | 199 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5297      | False       |
    | 200 | None                             | -10.15625  | -39.84375 | 5300      | False       |
    | 201 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5303      | False       |
    | 202 | None                             | -10.15625  | -39.84375 | 5306      | False       |
    | 203 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5309      | False       |
    | 204 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5312      | False       |
    | 205 | None                             | -10.15625  | -39.84375 | 5315      | False       |
    | 206 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5318      | False       |
    | 207 | None                             | -10.15625  | -39.84375 | 5321      | False       |
    | 208 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5324      | False       |
    | 209 | None                             | -10.15625  | -39.84375 | 5327      | False       |
    | 210 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5330      | False       |
    | 211 | None                             | -10.15625  | -39.84375 | 5333      | False       |
    | 212 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5336      | False       |
    | 213 | None                             | -10.15625  | -39.84375 | 5339      | False       |
    | 214 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5342      | False       |
    | 215 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5345      | False       |
    | 216 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5348      | False       |
    | 217 | None                             | -10.15625  | -39.84375 | 5351      | False       |
    | 218 | None                             | -10.15625  | -39.84375 | 5354      | False       |
    | 219 | c95d4b6f50a0af9ed3012769e35d882d | -10.15625  | -39.84375 | 5357      | True        |
    | 220 | fe1092952cf295b9523531881b021d8e | -10.15625  | -39.84375 | 5360      | True        |
    | 221 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5363      | False       |
    | 222 | 333f6ac4f95cd0ddbffe4d67c7dd1c96 | -10.15625  | -39.84375 | 5366      | True        |
    | 223 | 228897378e2fd8065916e680f4f7063b | -10.15625  | -39.84375 | 5369      | True        |
    | 224 | 7fd87b8f0f03d2ed6ec1833f31ee8479 | -10.15625  | -39.84375 | 5372      | True        |
    | 225 | b8e0bdb356d6db0112ba037495093e75 | -10.15625  | -39.84375 | 5375      | True        |
    | 226 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5378      | False       |
    | 227 | None                             | -10.15625  | -39.84375 | 5381      | False       |
    | 228 | None                             | -10.15625  | -39.84375 | 5384      | False       |
    | 229 | dccd600aea7ca7a65f42e20e8f0b20ee | -10.15625  | -39.84375 | 5387      | True        |
    | 230 | None                             | -10.15625  | -39.84375 | 5390      | False       |
    | 231 | 49ca9c107ae5a4ee070f0a5e0c7cd187 | -10.15625  | -39.84375 | 5393      | True        |
    | 232 | 50fe67cc996d32b6da0937e99bafec60 | -10.15625  | -39.84375 | 5396      | False       |
    | 233 | 64cdf96ff6d03c8896388a80719825f8 | -10.15625  | -39.84375 | 5399      | True        |
    | 234 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4700      | False       |
    | 235 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4703      | False       |
    | 236 | None                             | -10.546875 | -39.84375 | 4706      | False       |
    | 237 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4709      | False       |
    | 238 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4712      | False       |
    | 239 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4715      | False       |
    | 240 | None                             | -10.546875 | -39.84375 | 4718      | False       |
    | 241 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4721      | False       |
    | 242 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4724      | False       |
    | 243 | None                             | -10.546875 | -39.84375 | 4727      | False       |
    | 244 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4730      | False       |
    | 245 | None                             | -10.546875 | -39.84375 | 4733      | False       |
    | 246 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4736      | False       |
    | 247 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4739      | False       |
    | 248 | None                             | -10.546875 | -39.84375 | 4742      | False       |
    | 249 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4745      | False       |
    | 250 | None                             | -10.546875 | -39.84375 | 4748      | False       |
    | 251 | None                             | -10.546875 | -39.84375 | 4751      | False       |
    | 252 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4754      | False       |
    | 253 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4757      | False       |
    | 254 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4760      | False       |
    | 255 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4763      | False       |
    | 256 | None                             | -10.546875 | -39.84375 | 4766      | False       |
    | 257 | 7e3564aca1224c42f4309a7fecfddc8d | -10.546875 | -39.84375 | 4769      | True        |
    | 258 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4772      | False       |
    | 259 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4775      | False       |
    | 260 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4778      | False       |
    | 261 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4781      | False       |
    | 262 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4784      | False       |
    | 263 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4787      | False       |
    | 264 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4790      | False       |
    | 265 | None                             | -10.546875 | -39.84375 | 4793      | False       |
    | 266 | e2d649f8eb2b14b990621a24db06bfcf | -10.546875 | -39.84375 | 4796      | True        |
    | 267 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4799      | False       |
    | 268 | None                             | -10.546875 | -39.84375 | 4802      | False       |
    | 269 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4805      | False       |
    | 270 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4808      | False       |
    | 271 | 198d078bbfa1165e5e1ed876b0ac0075 | -10.546875 | -39.84375 | 4811      | True        |
    | 272 | d4e10ab07d0fde9c2b83406c9271b8d3 | -10.546875 | -39.84375 | 4814      | True        |
    | 273 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4817      | False       |
    | 274 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4820      | False       |
    | 275 | None                             | -10.546875 | -39.84375 | 4823      | False       |
    | 276 | 2044580cd0ab3a9e7ab61a21d2aaa491 | -10.546875 | -39.84375 | 4826      | True        |
    | 277 | 5ba97720f9aef055e97676c8728a4f4c | -10.546875 | -39.84375 | 4829      | True        |
    | 278 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4832      | False       |
    | 279 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4835      | False       |
    | 280 | None                             | -10.546875 | -39.84375 | 4838      | False       |
    | 281 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4841      | False       |
    | 282 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4844      | False       |
    | 283 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4847      | False       |
    | 284 | 7b593005d6e3a833eed39dbf2921ffca | -10.546875 | -39.84375 | 4850      | True        |
    | 285 | 3b820941b6621e45a062bf41e8453d08 | -10.546875 | -39.84375 | 4853      | True        |
    | 286 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4856      | False       |
    | 287 | None                             | -10.546875 | -39.84375 | 4859      | False       |
    | 288 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4862      | False       |
    | 289 | None                             | -10.546875 | -39.84375 | 4865      | False       |
    | 290 | None                             | -10.546875 | -39.84375 | 4868      | False       |
    | 291 | None                             | -10.546875 | -39.84375 | 4871      | False       |
    | 292 | e652df0b4dc0ff917b9956ef3857aaf0 | -10.546875 | -39.84375 | 4874      | True        |
    | 293 | None                             | -10.546875 | -39.84375 | 4877      | False       |
    | 294 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4880      | False       |
    | 295 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4883      | False       |
    | 296 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4886      | False       |
    | 297 | None                             | -10.546875 | -39.84375 | 4889      | False       |
    | 298 | 03fbb28b234aeb807f96af98775a51b3 | -10.546875 | -39.84375 | 4892      | True        |
    | 299 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4895      | False       |
    | 300 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4898      | False       |
    | 301 | 8d6803c1c4a594da97d931a1ac039b58 | -10.546875 | -39.84375 | 4901      | True        |
    | 302 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4904      | False       |
    | 303 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4907      | False       |
    | 304 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4910      | False       |
    | 305 | None                             | -10.546875 | -39.84375 | 4913      | False       |
    | 306 | 9f43cb852e9b51d434ff7b7cce7fa8c8 | -10.546875 | -39.84375 | 4916      | True        |
    | 307 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4919      | False       |
    | 308 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4922      | False       |
    | 309 | e4e61e01b5df541369b2301bb4e14c9e | -10.546875 | -39.84375 | 4925      | True        |
    | 310 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4928      | False       |
    | 311 | None                             | -10.546875 | -39.84375 | 4931      | False       |
    | 312 | None                             | -10.546875 | -39.84375 | 4934      | False       |
    | 313 | None                             | -10.546875 | -39.84375 | 4937      | False       |
    | 314 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4940      | False       |
    | 315 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4943      | False       |
    | 316 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4946      | False       |
    | 317 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4949      | False       |
    | 318 | None                             | -10.546875 | -39.84375 | 4952      | False       |
    | 319 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4955      | False       |
    | 320 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4958      | False       |
    | 321 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4961      | False       |
    | 322 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4964      | False       |
    | 323 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4967      | False       |
    | 324 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4970      | False       |
    | 325 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4973      | False       |
    | 326 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4976      | False       |
    | 327 | None                             | -10.546875 | -39.84375 | 4979      | False       |
    | 328 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4982      | False       |
    | 329 | None                             | -10.546875 | -39.84375 | 4985      | False       |
    | 330 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4988      | False       |
    | 331 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4991      | False       |
    | 332 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 4994      | False       |
    | 333 | None                             | -10.546875 | -39.84375 | 4997      | False       |
    | 334 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5000      | False       |
    | 335 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5003      | False       |
    | 336 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5006      | False       |
    | 337 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5009      | False       |
    | 338 | 01ff028e9e9465ac0be27fac4140d600 | -10.546875 | -39.84375 | 5012      | True        |
    | 339 | None                             | -10.546875 | -39.84375 | 5015      | False       |
    | 340 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5018      | False       |
    | 341 | None                             | -10.546875 | -39.84375 | 5021      | False       |
    | 342 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5024      | False       |
    | 343 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5027      | False       |
    | 344 | None                             | -10.546875 | -39.84375 | 5030      | False       |
    | 345 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5033      | False       |
    | 346 | a52640c37d32fa9140a2d93fa48b31ba | -10.546875 | -39.84375 | 5036      | True        |
    | 347 | ca3981b1ca78ba769c2244f20d1738dd | -10.546875 | -39.84375 | 5039      | True        |
    | 348 | None                             | -10.546875 | -39.84375 | 5042      | False       |
    | 349 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5045      | False       |
    | 350 | None                             | -10.546875 | -39.84375 | 5048      | False       |
    | 351 | None                             | -10.546875 | -39.84375 | 5051      | False       |
    | 352 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5054      | False       |
    | 353 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5057      | False       |
    | 354 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5060      | False       |
    | 355 | None                             | -10.546875 | -39.84375 | 5063      | False       |
    | 356 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5066      | False       |
    | 357 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5069      | False       |
    | 358 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5072      | False       |
    | 359 | 4f3cb243c80fef1c8fcaea5ecc1de246 | -10.546875 | -39.84375 | 5075      | True        |
    | 360 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5078      | False       |
    | 361 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5081      | False       |
    | 362 | c2e38b82eeaeb762bc7e28b12559dd46 | -10.546875 | -39.84375 | 5084      | True        |
    | 363 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5087      | False       |
    | 364 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5090      | False       |
    | 365 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5093      | False       |
    | 366 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5096      | False       |
    | 367 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5099      | False       |
    | 368 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5102      | False       |
    | 369 | None                             | -10.546875 | -39.84375 | 5105      | False       |
    | 370 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5108      | False       |
    | 371 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5111      | False       |
    | 372 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5114      | False       |
    | 373 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5117      | False       |
    | 374 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5120      | False       |
    | 375 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5123      | False       |
    | 376 | d23ffe1bfc68594b4c6bc9f74fc259aa | -10.546875 | -39.84375 | 5126      | True        |
    | 377 | None                             | -10.546875 | -39.84375 | 5129      | False       |
    | 378 | None                             | -10.546875 | -39.84375 | 5132      | False       |
    | 379 | None                             | -10.546875 | -39.84375 | 5135      | False       |
    | 380 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5138      | False       |
    | 381 | None                             | -10.546875 | -39.84375 | 5141      | False       |
    | 382 | None                             | -10.546875 | -39.84375 | 5144      | False       |
    | 383 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5147      | False       |
    | 384 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5150      | False       |
    | 385 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5153      | False       |
    | 386 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5156      | False       |
    | 387 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5159      | False       |
    | 388 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5162      | False       |
    | 389 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5165      | False       |
    | 390 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5168      | False       |
    | 391 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5171      | False       |
    | 392 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5174      | False       |
    | 393 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5177      | False       |
    | 394 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5180      | False       |
    | 395 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5183      | False       |
    | 396 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5186      | False       |
    | 397 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5189      | False       |
    | 398 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5192      | False       |
    | 399 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5195      | False       |
    | 400 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5198      | False       |
    | 401 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5201      | False       |
    | 402 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5204      | False       |
    | 403 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5207      | False       |
    | 404 | None                             | -10.546875 | -39.84375 | 5210      | False       |
    | 405 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5213      | False       |
    | 406 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5216      | False       |
    | 407 | None                             | -10.546875 | -39.84375 | 5219      | False       |
    | 408 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5222      | False       |
    | 409 | None                             | -10.546875 | -39.84375 | 5225      | False       |
    | 410 | None                             | -10.546875 | -39.84375 | 5228      | False       |
    | 411 | 92b54e463ec76afa0b94191710e0ff30 | -10.546875 | -39.84375 | 5231      | True        |
    | 412 | None                             | -10.546875 | -39.84375 | 5234      | False       |
    | 413 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5237      | False       |
    | 414 | 55ab499d403dfd1dd3863164cf0fd358 | -10.546875 | -39.84375 | 5240      | True        |
    | 415 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5243      | False       |
    | 416 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5246      | False       |
    | 417 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5249      | False       |
    | 418 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5252      | False       |
    | 419 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5255      | False       |
    | 420 | None                             | -10.546875 | -39.84375 | 5258      | False       |
    | 421 | None                             | -10.546875 | -39.84375 | 5261      | False       |
    | 422 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5264      | False       |
    | 423 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5267      | False       |
    | 424 | f68619d8f25cc36248582d49fe9ddafc | -10.546875 | -39.84375 | 5270      | True        |
    | 425 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5273      | False       |
    | 426 | 5f08c0c242b9ebd250e44c6a9cc8a2ef | -10.546875 | -39.84375 | 5276      | True        |
    | 427 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5279      | False       |
    | 428 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5282      | False       |
    | 429 | 611fa862d83e5e14271991c459f2deb4 | -10.546875 | -39.84375 | 5285      | True        |
    | 430 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5288      | False       |
    | 431 | a2bb27c55349bc9bed72f00510a36f8f | -10.546875 | -39.84375 | 5291      | True        |
    | 432 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5294      | False       |
    | 433 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5297      | False       |
    | 434 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5300      | False       |
    | 435 | None                             | -10.546875 | -39.84375 | 5303      | False       |
    | 436 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5306      | False       |
    | 437 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5309      | False       |
    | 438 | 7fc79d0f09b21b9e68d8958f1bec08f1 | -10.546875 | -39.84375 | 5312      | True        |
    | 439 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5315      | False       |
    | 440 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5318      | False       |
    | 441 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5321      | False       |
    | 442 | d7f5a1798c6c61baa71d3cd13e98a7c9 | -10.546875 | -39.84375 | 5324      | True        |
    | 443 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5327      | False       |
    | 444 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5330      | False       |
    | 445 | None                             | -10.546875 | -39.84375 | 5333      | False       |
    | 446 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5336      | False       |
    | 447 | None                             | -10.546875 | -39.84375 | 5339      | False       |
    | 448 | None                             | -10.546875 | -39.84375 | 5342      | False       |
    | 449 | None                             | -10.546875 | -39.84375 | 5345      | False       |
    | 450 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5348      | False       |
    | 451 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5351      | False       |
    | 452 | b854808bf7d454e7e136ecb719f1dbbe | -10.546875 | -39.84375 | 5354      | True        |
    | 453 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5357      | False       |
    | 454 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5360      | False       |
    | 455 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5363      | False       |
    | 456 | c7356bcea67e0839de87389f5670221e | -10.546875 | -39.84375 | 5366      | True        |
    | 457 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5369      | False       |
    | 458 | None                             | -10.546875 | -39.84375 | 5372      | False       |
    | 459 | None                             | -10.546875 | -39.84375 | 5375      | False       |
    | 460 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5378      | False       |
    | 461 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5381      | False       |
    | 462 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5384      | False       |
    | 463 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5387      | False       |
    | 464 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5390      | False       |
    | 465 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5393      | False       |
    | 466 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5396      | False       |
    | 467 | 50fe67cc996d32b6da0937e99bafec60 | -10.546875 | -39.84375 | 5399      | False       |
    | 468 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4700      | False       |
    | 469 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4703      | False       |
    | 470 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4706      | False       |
    | 471 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4709      | False       |
    | 472 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4712      | False       |
    | 473 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4715      | False       |
    | 474 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4718      | False       |
    | 475 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4721      | False       |
    | 476 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4724      | False       |
    | 477 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4727      | False       |
    | 478 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4730      | False       |
    | 479 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4733      | False       |
    | 480 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4736      | False       |
    | 481 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4739      | False       |
    | 482 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4742      | False       |
    | 483 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4745      | False       |
    | 484 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4748      | False       |
    | 485 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4751      | False       |
    | 486 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4754      | False       |
    | 487 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4757      | False       |
    | 488 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4760      | False       |
    | 489 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4763      | False       |
    | 490 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4766      | False       |
    | 491 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4769      | False       |
    | 492 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4772      | False       |
    | 493 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4775      | False       |
    | 494 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4778      | False       |
    | 495 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4781      | False       |
    | 496 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4784      | False       |
    | 497 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4787      | False       |
    | 498 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4790      | False       |
    | 499 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4793      | False       |
    | 500 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4796      | False       |
    | 501 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4799      | False       |
    | 502 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4802      | False       |
    | 503 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4805      | False       |
    | 504 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4808      | False       |
    | 505 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4811      | False       |
    | 506 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4814      | False       |
    | 507 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4817      | False       |
    | 508 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4820      | False       |
    | 509 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4823      | False       |
    | 510 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4826      | False       |
    | 511 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4829      | False       |
    | 512 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4832      | False       |
    | 513 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4835      | False       |
    | 514 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4838      | False       |
    | 515 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4841      | False       |
    | 516 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4844      | False       |
    | 517 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4847      | False       |
    | 518 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4850      | False       |
    | 519 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4853      | False       |
    | 520 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4856      | False       |
    | 521 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4859      | False       |
    | 522 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4862      | False       |
    | 523 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4865      | False       |
    | 524 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4868      | False       |
    | 525 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4871      | False       |
    | 526 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4874      | False       |
    | 527 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4877      | False       |
    | 528 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4880      | False       |
    | 529 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4883      | False       |
    | 530 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4886      | False       |
    | 531 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4889      | False       |
    | 532 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4892      | False       |
    | 533 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4895      | False       |
    | 534 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4898      | False       |
    | 535 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4901      | False       |
    | 536 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4904      | False       |
    | 537 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4907      | False       |
    | 538 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4910      | False       |
    | 539 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4913      | False       |
    | 540 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4916      | False       |
    | 541 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4919      | False       |
    | 542 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4922      | False       |
    | 543 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4925      | False       |
    | 544 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4928      | False       |
    | 545 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4931      | False       |
    | 546 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4934      | False       |
    | 547 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4937      | False       |
    | 548 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4940      | False       |
    | 549 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4943      | False       |
    | 550 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4946      | False       |
    | 551 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4949      | False       |
    | 552 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4952      | False       |
    | 553 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4955      | False       |
    | 554 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4958      | False       |
    | 555 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4961      | False       |
    | 556 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4964      | False       |
    | 557 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4967      | False       |
    | 558 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4970      | False       |
    | 559 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4973      | False       |
    | 560 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4976      | False       |
    | 561 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4979      | False       |
    | 562 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4982      | False       |
    | 563 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4985      | False       |
    | 564 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4988      | False       |
    | 565 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4991      | False       |
    | 566 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4994      | False       |
    | 567 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 4997      | False       |
    | 568 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5000      | False       |
    | 569 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5003      | False       |
    | 570 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5006      | False       |
    | 571 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5009      | False       |
    | 572 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5012      | False       |
    | 573 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5015      | False       |
    | 574 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5018      | False       |
    | 575 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5021      | False       |
    | 576 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5024      | False       |
    | 577 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5027      | False       |
    | 578 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5030      | False       |
    | 579 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5033      | False       |
    | 580 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5036      | False       |
    | 581 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5039      | False       |
    | 582 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5042      | False       |
    | 583 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5045      | False       |
    | 584 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5048      | False       |
    | 585 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5051      | False       |
    | 586 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5054      | False       |
    | 587 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5057      | False       |
    | 588 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5060      | False       |
    | 589 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5063      | False       |
    | 590 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5066      | False       |
    | 591 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5069      | False       |
    | 592 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5072      | False       |
    | 593 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5075      | False       |
    | 594 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5078      | False       |
    | 595 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5081      | False       |
    | 596 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5084      | False       |
    | 597 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5087      | False       |
    | 598 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5090      | False       |
    | 599 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5093      | False       |
    | 600 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5096      | False       |
    | 601 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5099      | False       |
    | 602 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5102      | False       |
    | 603 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5105      | False       |
    | 604 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5108      | False       |
    | 605 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5111      | False       |
    | 606 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5114      | False       |
    | 607 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5117      | False       |
    | 608 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5120      | False       |
    | 609 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5123      | False       |
    | 610 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5126      | False       |
    | 611 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5129      | False       |
    | 612 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5132      | False       |
    | 613 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5135      | False       |
    | 614 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5138      | False       |
    | 615 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5141      | False       |
    | 616 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5144      | False       |
    | 617 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5147      | False       |
    | 618 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5150      | False       |
    | 619 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5153      | False       |
    | 620 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5156      | False       |
    | 621 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5159      | False       |
    | 622 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5162      | False       |
    | 623 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5165      | False       |
    | 624 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5168      | False       |
    | 625 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5171      | False       |
    | 626 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5174      | False       |
    | 627 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5177      | False       |
    | 628 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5180      | False       |
    | 629 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5183      | False       |
    | 630 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5186      | False       |
    | 631 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5189      | False       |
    | 632 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5192      | False       |
    | 633 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5195      | False       |
    | 634 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5198      | False       |
    | 635 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5201      | False       |
    | 636 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5204      | False       |
    | 637 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5207      | False       |
    | 638 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5210      | False       |
    | 639 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5213      | False       |
    | 640 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5216      | False       |
    | 641 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5219      | False       |
    | 642 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5222      | False       |
    | 643 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5225      | False       |
    | 644 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5228      | False       |
    | 645 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5231      | False       |
    | 646 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5234      | False       |
    | 647 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5237      | False       |
    | 648 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5240      | False       |
    | 649 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5243      | False       |
    | 650 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5246      | False       |
    | 651 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5249      | False       |
    | 652 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5252      | False       |
    | 653 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5255      | False       |
    | 654 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5258      | False       |
    | 655 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5261      | False       |
    | 656 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5264      | False       |
    | 657 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5267      | False       |
    | 658 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5270      | False       |
    | 659 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5273      | False       |
    | 660 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5276      | False       |
    | 661 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5279      | False       |
    | 662 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5282      | False       |
    | 663 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5285      | False       |
    | 664 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5288      | False       |
    | 665 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5291      | False       |
    | 666 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5294      | False       |
    | 667 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5297      | False       |
    | 668 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5300      | False       |
    | 669 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5303      | False       |
    | 670 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5306      | False       |
    | 671 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5309      | False       |
    | 672 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5312      | False       |
    | 673 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5315      | False       |
    | 674 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5318      | False       |
    | 675 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5321      | False       |
    | 676 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5324      | False       |
    | 677 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5327      | False       |
    | 678 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5330      | False       |
    | 679 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5333      | False       |
    | 680 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5336      | False       |
    | 681 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5339      | False       |
    | 682 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5342      | False       |
    | 683 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5345      | False       |
    | 684 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5348      | False       |
    | 685 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5351      | False       |
    | 686 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5354      | False       |
    | 687 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5357      | False       |
    | 688 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5360      | False       |
    | 689 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5363      | False       |
    | 690 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5366      | False       |
    | 691 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5369      | False       |
    | 692 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5372      | False       |
    | 693 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5375      | False       |
    | 694 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5378      | False       |
    | 695 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5381      | False       |
    | 696 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5384      | False       |
    | 697 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5387      | False       |
    | 698 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5390      | False       |
    | 699 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5393      | False       |
    | 700 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5396      | False       |
    | 701 | 50fe67cc996d32b6da0937e99bafec60 | -10.9375   | -39.84375 | 5399      | False       |
    | 702 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4700      | False       |
    | 703 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4703      | False       |
    | 704 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4706      | False       |
    | 705 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4709      | False       |
    | 706 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4712      | False       |
    | 707 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4715      | False       |
    | 708 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4718      | False       |
    | 709 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4721      | False       |
    | 710 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4724      | False       |
    | 711 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4727      | False       |
    | 712 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4730      | False       |
    | 713 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4733      | False       |
    | 714 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4736      | False       |
    | 715 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4739      | False       |
    | 716 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4742      | False       |
    | 717 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4745      | False       |
    | 718 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4748      | False       |
    | 719 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4751      | False       |
    | 720 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4754      | False       |
    | 721 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4757      | False       |
    | 722 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4760      | False       |
    | 723 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4763      | False       |
    | 724 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4766      | False       |
    | 725 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4769      | False       |
    | 726 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4772      | False       |
    | 727 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4775      | False       |
    | 728 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4778      | False       |
    | 729 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4781      | False       |
    | 730 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4784      | False       |
    | 731 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4787      | False       |
    | 732 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4790      | False       |
    | 733 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4793      | False       |
    | 734 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4796      | False       |
    | 735 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4799      | False       |
    | 736 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4802      | False       |
    | 737 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4805      | False       |
    | 738 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4808      | False       |
    | 739 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4811      | False       |
    | 740 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4814      | False       |
    | 741 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4817      | False       |
    | 742 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4820      | False       |
    | 743 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4823      | False       |
    | 744 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4826      | False       |
    | 745 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4829      | False       |
    | 746 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4832      | False       |
    | 747 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4835      | False       |
    | 748 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4838      | False       |
    | 749 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4841      | False       |
    | 750 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4844      | False       |
    | 751 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4847      | False       |
    | 752 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4850      | False       |
    | 753 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4853      | False       |
    | 754 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4856      | False       |
    | 755 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4859      | False       |
    | 756 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4862      | False       |
    | 757 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4865      | False       |
    | 758 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4868      | False       |
    | 759 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4871      | False       |
    | 760 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4874      | False       |
    | 761 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4877      | False       |
    | 762 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4880      | False       |
    | 763 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4883      | False       |
    | 764 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4886      | False       |
    | 765 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4889      | False       |
    | 766 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4892      | False       |
    | 767 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4895      | False       |
    | 768 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4898      | False       |
    | 769 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4901      | False       |
    | 770 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4904      | False       |
    | 771 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4907      | False       |
    | 772 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4910      | False       |
    | 773 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4913      | False       |
    | 774 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4916      | False       |
    | 775 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4919      | False       |
    | 776 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4922      | False       |
    | 777 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4925      | False       |
    | 778 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4928      | False       |
    | 779 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4931      | False       |
    | 780 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4934      | False       |
    | 781 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4937      | False       |
    | 782 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4940      | False       |
    | 783 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4943      | False       |
    | 784 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4946      | False       |
    | 785 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4949      | False       |
    | 786 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4952      | False       |
    | 787 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4955      | False       |
    | 788 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4958      | False       |
    | 789 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4961      | False       |
    | 790 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4964      | False       |
    | 791 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4967      | False       |
    | 792 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4970      | False       |
    | 793 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4973      | False       |
    | 794 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4976      | False       |
    | 795 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4979      | False       |
    | 796 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4982      | False       |
    | 797 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4985      | False       |
    | 798 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4988      | False       |
    | 799 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4991      | False       |
    | 800 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4994      | False       |
    | 801 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 4997      | False       |
    | 802 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5000      | False       |
    | 803 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5003      | False       |
    | 804 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5006      | False       |
    | 805 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5009      | False       |
    | 806 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5012      | False       |
    | 807 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5015      | False       |
    | 808 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5018      | False       |
    | 809 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5021      | False       |
    | 810 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5024      | False       |
    | 811 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5027      | False       |
    | 812 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5030      | False       |
    | 813 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5033      | False       |
    | 814 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5036      | False       |
    | 815 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5039      | False       |
    | 816 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5042      | False       |
    | 817 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5045      | False       |
    | 818 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5048      | False       |
    | 819 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5051      | False       |
    | 820 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5054      | False       |
    | 821 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5057      | False       |
    | 822 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5060      | False       |
    | 823 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5063      | False       |
    | 824 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5066      | False       |
    | 825 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5069      | False       |
    | 826 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5072      | False       |
    | 827 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5075      | False       |
    | 828 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5078      | False       |
    | 829 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5081      | False       |
    | 830 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5084      | False       |
    | 831 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5087      | False       |
    | 832 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5090      | False       |
    | 833 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5093      | False       |
    | 834 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5096      | False       |
    | 835 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5099      | False       |
    | 836 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5102      | False       |
    | 837 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5105      | False       |
    | 838 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5108      | False       |
    | 839 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5111      | False       |
    | 840 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5114      | False       |
    | 841 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5117      | False       |
    | 842 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5120      | False       |
    | 843 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5123      | False       |
    | 844 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5126      | False       |
    | 845 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5129      | False       |
    | 846 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5132      | False       |
    | 847 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5135      | False       |
    | 848 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5138      | False       |
    | 849 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5141      | False       |
    | 850 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5144      | False       |
    | 851 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5147      | False       |
    | 852 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5150      | False       |
    | 853 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5153      | False       |
    | 854 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5156      | False       |
    | 855 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5159      | False       |
    | 856 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5162      | False       |
    | 857 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5165      | False       |
    | 858 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5168      | False       |
    | 859 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5171      | False       |
    | 860 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5174      | False       |
    | 861 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5177      | False       |
    | 862 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5180      | False       |
    | 863 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5183      | False       |
    | 864 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5186      | False       |
    | 865 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5189      | False       |
    | 866 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5192      | False       |
    | 867 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5195      | False       |
    | 868 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5198      | False       |
    | 869 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5201      | False       |
    | 870 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5204      | False       |
    | 871 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5207      | False       |
    | 872 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5210      | False       |
    | 873 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5213      | False       |
    | 874 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5216      | False       |
    | 875 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5219      | False       |
    | 876 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5222      | False       |
    | 877 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5225      | False       |
    | 878 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5228      | False       |
    | 879 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5231      | False       |
    | 880 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5234      | False       |
    | 881 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5237      | False       |
    | 882 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5240      | False       |
    | 883 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5243      | False       |
    | 884 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5246      | False       |
    | 885 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5249      | False       |
    | 886 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5252      | False       |
    | 887 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5255      | False       |
    | 888 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5258      | False       |
    | 889 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5261      | False       |
    | 890 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5264      | False       |
    | 891 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5267      | False       |
    | 892 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5270      | False       |
    | 893 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5273      | False       |
    | 894 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5276      | False       |
    | 895 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5279      | False       |
    | 896 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5282      | False       |
    | 897 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5285      | False       |
    | 898 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5288      | False       |
    | 899 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5291      | False       |
    | 900 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5294      | False       |
    | 901 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5297      | False       |
    | 902 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5300      | False       |
    | 903 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5303      | False       |
    | 904 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5306      | False       |
    | 905 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5309      | False       |
    | 906 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5312      | False       |
    | 907 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5315      | False       |
    | 908 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5318      | False       |
    | 909 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5321      | False       |
    | 910 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5324      | False       |
    | 911 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5327      | False       |
    | 912 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5330      | False       |
    | 913 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5333      | False       |
    | 914 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5336      | False       |
    | 915 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5339      | False       |
    | 916 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5342      | False       |
    | 917 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5345      | False       |
    | 918 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5348      | False       |
    | 919 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5351      | False       |
    | 920 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5354      | False       |
    | 921 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5357      | False       |
    | 922 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5360      | False       |
    | 923 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5363      | False       |
    | 924 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5366      | False       |
    | 925 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5369      | False       |
    | 926 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5372      | False       |
    | 927 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5375      | False       |
    | 928 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5378      | False       |
    | 929 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5381      | False       |
    | 930 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5384      | False       |
    | 931 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5387      | False       |
    | 932 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5390      | False       |
    | 933 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5393      | False       |
    | 934 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5396      | False       |
    | 935 | 50fe67cc996d32b6da0937e99bafec60 | -11.328125 | -39.84375 | 5399      | False       |
    +-----+----------------------------------+------------+-----------+-----------+-------------+
    


R8: Cryptanalysis of the faulty outputs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this second attack, we assume the fault was injected *before* the
last two *MixColumn* operations. First, we convert each 100% faulty
output into four 25% faulty outputs and then we apply the same attack as
before.


**In [30]:**

.. code:: ipython3

    outputs2=phoenixAES.convert_r8faults_bytes(outputs, goldciph, encrypt=True)
    r10=phoenixAES.crack_bytes(outputs2, goldciph, encrypt=True, verbose=2)


**Out [30]:**



.. parsed-literal::

    f1fe67cc996d326cda09c9e99b63ec60: group 0
    50da67ccbd6d32b6da0937fc9baf4560: group 1
    50fe82cc99d332b62e0937e99bafec19: group 2
    50fe679f996d23b6da7f37e9f9afec60: group 3
    cefe67cc996d32ccda0901e99bf1ec60: group 0
    504067cc916d32b6da0937e99baf5c60: group None
    50fe6dcc995c32b69e0937e99bafecec: group 2
    50fe67bc996d9ab6da9137e9e2afec60: group 3
    effe67cc996d3226da0949e99b64ec60: group 0
    50bb67cc376d32b6da0937679bafdc60: group 1
    50fe2dcc991832b64c0937e99bafecda: group 2
    50fe6746996ddab6da2237e9efafec60: group 3
    6afe67cc996d3217da0928e99b40ec60: group 0
    Round key bytes recovered:
    D0............89....0C....63....
    50f667cc586d32b6da09372a9bafed60: group 1
    50fe13cc99d132b6ed0937e99bafecd9: group 2
    Round key bytes recovered:
    D0..F9....EE..89E1..0C....63..A6
    50fe6708996d6cb6da7437e97fafec60: group 3
    Round key bytes recovered:
    D0..F9A8..EE2589E13F0C..B663..A6
    eafe67cc996d32f3da09a6e99b41ec60: group 0
    504567cc4d6d32b6da0937149baf5e60: group 1
    Round key bytes recovered:
    D014F9A8C9EE2589E13F0CC8B6630CA6
    Last round key #N found:
    D014F9A8C9EE2589E13F0CC8B6630CA6
    


We hope you managed to recover the full 10th round key. If not, you may
try again `from here <#R8:-Collecting-faulty-outputs>`__. Once the last
round key is recovered, you can revert the AES keyscheduling and reveal
the actual AES key.


**In [31]:**

.. code:: ipython3

    if r10 is not None:
        from chipwhisperer.analyzer.attacks.models.aes.key_schedule import key_schedule_rounds
        key = key_schedule_rounds(bytearray.fromhex(r10), 10, 0)
        print("AES Key:")
        print(''.join("%02x" % x for x in key))
    else:
        print("Sorry, no key found, try another campain, maybe with different parameters...")


**Out [31]:**



.. parsed-literal::

    AES Key:
    2b7e151628aed2a6abf7158809cf4f3c
    


R8: Plotting inner states differences
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let's plot the fault diffusion of the first 10 outputs.


**In [32]:**

.. code:: ipython3

    %run "Helper_Scripts/AES_differential_plotter.ipynb"
    if key is not None:
        ad=AesDiff(intext=text, key=key, encrypt=True)
        for c in outputs[:10]:
            ad.add_glitch(c)
        plt=ad.plot_diff_bits()
        plt.show()


**Out [32]:**


.. image:: output_74_0.png


And grouped by faulty bytes. We now see that we get single byte faults
one round earlier:


**In [33]:**

.. code:: ipython3

    if key is not None:
        plt=ad.plot_diff_bytes()
        plt.show()


**Out [33]:**


.. image:: output_76_0.png


The end
-------

| Once you're done, clean up the connection to the scope and target.
| **Warning**, once disconnected, you'll have to run the cells since the
  `CW-lite connection and target
  flashing <#CW-lite-connection-and-target-flashing>`__ section to be
  connected again.


**In [34]:**

.. code:: ipython3

    scope.dis()
    target.dis()

| This tutorial is over.
| You might now try to attack other instances by yourself, e.g.
  recompile the target with ``CRYPTO_TARGET = "TINYAES128C"`` and try to
  break it!

Tests
-----


**In [35]:**

.. code:: ipython3

    assert key == [43, 126, 21, 22, 40, 174, 210, 166, 171, 247, 21, 136, 9, 207, 79, 60], "Failed to break r8"


**In [36]:**

.. code:: ipython3

    assert r9_key == [43, 126, 21, 22, 40, 174, 210, 166, 171, 247, 21, 136, 9, 207, 79, 60], "Failed to break r9"
