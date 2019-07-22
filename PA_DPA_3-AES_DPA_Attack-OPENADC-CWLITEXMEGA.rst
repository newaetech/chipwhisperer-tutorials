
PA\_DPA\_3-AES\_DPA\_Attack
===========================

Before starting this tutorial, it's recommended that you first complete
the earlier PA\_DPA tutorials since these will familiarize you with the
concept of Differental Power Analysis. With that out of the way, let's
look at how this attack works.

DPA Attack Theory
-----------------

As we explored in the earlier DPA tutorials, the Hamming Weight of the
result of the SBox operation in AES has a measurable effect on the power
consumed by the microcontroller. It turns out that just this effect (and
not anything stronger, such as its linearity) is enough information to
break an AES key. There's a few different ways we could go about this,
but for this tutorial, we'll be looking at difference of means. With
this technique, the goal is to separate the traces by a bit in the
result of the SBox output (it doesn't matter which one): if that bit is
1, its group of traces should, on average, have higher power consumption
during the SBox operation than the other set.

Whether or not we get a large difference in the means between these two
groups depends on whether they were properly sorted into these groups.
If not, there should be, on average, little difference between the two
and therefore a low difference of means. Recall the SBox operation:

.. figure:: https://wiki.newae.com/images/7/71/Sbox_cpa_detail.png
   :alt: title

   title

The SBox output depends on the subkey, which we don't know (and the
plaintext, which we do). However, since there's a large difference of
means for the correct key and small ones for the rest of the possible
subkeys, we have a method of checking whether a given subkey is correct.
If we calculate the difference of means for each subkey, the correct one
will have the largest difference of means.

Our plan looks as follows 1. Capture a bunch of power traces with
varying plaintext 1. Group each trace by the value of their SBox
output's lowest bit for a given key guess 1. Calculate the difference of
means 1. Repeat for each possible subkey 1. Select the largest
difference of means -> this is the correct subkey 1. Repeat for each
subkey in the key

At the end, we should get a correct AES key!


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'
    CRYPTO_TARGET = 'AVRCRYPTOLIB'


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEXMEGA.hex
    rm -f -- simpleserial-aes-CWLITEXMEGA.eep
    rm -f -- simpleserial-aes-CWLITEXMEGA.cof
    rm -f -- simpleserial-aes-CWLITEXMEGA.elf
    rm -f -- simpleserial-aes-CWLITEXMEGA.map
    rm -f -- simpleserial-aes-CWLITEXMEGA.sym
    rm -f -- simpleserial-aes-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes-independant.s aes_enc.s aes_keyschedule.s aes_sbox.s aes128_enc.s
    rm -f -- simpleserial-aes.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes-independant.d aes_enc.d aes_keyschedule.d aes_sbox.d aes128_enc.d
    rm -f -- simpleserial-aes.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes-independant.i aes_enc.i aes_keyschedule.i aes_sbox.i aes128_enc.i
    .
    -------- begin --------
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_enc.o.d .././crypto/avrcryptolib//aes/aes_enc.c -o objdir/aes_enc.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_keyschedule.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_keyschedule.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_keyschedule.o.d .././crypto/avrcryptolib//aes/aes_keyschedule.c -o objdir/aes_keyschedule.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_sbox.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_sbox.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_sbox.o.d .././crypto/avrcryptolib//aes/aes_sbox.c -o objdir/aes_sbox.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes128_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes128_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes128_enc.o.d .././crypto/avrcryptolib//aes/aes128_enc.c -o objdir/aes128_enc.o 
    .
    Assembling: .././crypto/avrcryptolib//gf256mul/gf256mul.S
    avr-gcc -c -mmcu=atxmega128d3 -I. -x assembler-with-cpp -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/gf256mul.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul .././crypto/avrcryptolib//gf256mul/gf256mul.S -o objdir/gf256mul.o
    .
    Linking: simpleserial-aes-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEXMEGA.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes-independant.o objdir/aes_enc.o objdir/aes_keyschedule.o objdir/aes_sbox.o objdir/aes128_enc.o objdir/gf256mul.o --output simpleserial-aes-CWLITEXMEGA.elf -Wl,-Map=simpleserial-aes-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEXMEGA.sym
    avr-nm -n simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       3440	     32	    228	   3700	    e74	simpleserial-aes-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------



Capturing Power Traces
----------------------

Capture and setup is similar to earlier tutorials. We'll have to capture
a fair number of traces (usually a few thousand here) since Difference
of Means isn't a super trace efficient method. As you'll find during the
CPA tutorials, CPA is much better in this regard - it can often break
AES implementations such as these in under 50 traces.

You may also find that you need to modify gain settings and the number
of traces you capture - this attack is much more sensitive to gain
settings and noise than a CPA attack would be.

Setup
~~~~~


**In [3]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = "../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex".format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 3471 bytes
    


Capture
~~~~~~~


**In [6]:**

.. code:: ipython3

    #Capture Traces
    from tqdm import tnrange
    import numpy as np
    import time
    
    ktp = cw.ktp.Basic()
    
    traces = []
    N = 2500  # Number of traces
    
    if PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        scope.adc.samples = 4000
    elif PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        scope.gain.db = 34 #works best with this gain for some reason
        scope.adc.samples = 1700 - 170
        scope.adc.offset = 500 + 700 + 170
        N = 5000
        
    print(scope)
    for i in tnrange(N, desc='Capturing traces'):
        key, text = ktp.next()  # manual creation of a key, text pair can be substituted here
    
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        print(trace)
    
        traces.append(trace)
    
    #Convert traces to numpy arrays
    trace_array = np.asarray([trace.wave for trace in traces])
    print(trace_array)# if you prefer to work with numpy array for number crunching
    textin_array = np.asarray([trace.textin for trace in traces])
    known_keys = np.asarray([trace.key for trace in traces])  # for fixed key, these keys are all the same


**Out [6]:**



.. parsed-literal::

    cwlite Device
    gain = 
        mode = high
        gain = 44
        db   = 33.859375
    adc = 
        state      = False
        basic_mode = rising_edge
        timeout    = 2
        offset     = 1370
        presamples = 0
        samples    = 1530
        decimate   = 1
        trig_count = 8621711
    clock = 
        adc_src       = clkgen_x4
        adc_phase     = 0
        adc_freq      = 29538459
        adc_rate      = 29538459.0
        adc_locked    = True
        freq_ctr      = 0
        freq_ctr_src  = extclk
        clkgen_src    = system
        extclk_freq   = 10000000
        clkgen_mul    = 2
        clkgen_div    = 26
        clkgen_freq   = 7384615.384615385
        clkgen_locked = True
    trigger = 
        triggers = tio4
        module   = basic
    io = 
        tio1       = serial_rx
        tio2       = serial_tx
        tio3       = high_z
        tio4       = high_z
        pdid       = high_z
        pdic       = high_z
        nrst       = high_z
        glitch_hp  = False
        glitch_lp  = False
        extclk_src = hs1
        hs2        = clkgen
        target_pwr = True
    glitch = 
        clk_src     = target
        width       = 10.15625
        width_fine  = 0
        offset      = 10.15625
        offset_fine = 0
        trigger_src = manual
        arm_timing  = after_scope
        ext_offset  = 0
        repeat      = 1
        output      = clock_xor
    
    






.. parsed-literal::

    Trace(wave=array([ 0.18164062,  0.04589844,  0.40136719, ..., -0.5       ,
           -0.39550781, -0.40527344]), textin=CWbytearray(b'67 eb 0b 2f 49 74 17 dc b4 dc db 9f 99 9c 17 06'), textout=CWbytearray(b'ce 0b 85 cf e2 64 34 13 a1 0a f7 3c 6c 75 30 f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.046875  ,  0.38964844, ..., -0.5       ,
           -0.42578125, -0.42578125]), textin=CWbytearray(b'ba 8e 58 d2 b1 eb 16 20 e9 c0 c8 9e 86 57 90 34'), textout=CWbytearray(b'c6 9e 78 3f b9 84 f1 76 cd dc 37 7e 10 c5 2a 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02246094,  0.3828125 , ..., -0.5       ,
           -0.42480469, -0.42773438]), textin=CWbytearray(b'27 f8 be f3 4e 54 70 f1 05 d9 8d 55 0d 36 44 d1'), textout=CWbytearray(b'62 8c ae 5e f4 03 55 f1 6c 7e 37 9c b1 63 52 a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04785156,  0.40332031, ..., -0.5       ,
           -0.38867188, -0.40332031]), textin=CWbytearray(b'0e c6 38 8b 53 f6 9c 12 7b 83 1e 76 08 ef ea 11'), textout=CWbytearray(b'e8 d3 c6 e0 10 5d 03 9c 0a 01 6d 28 6a 5e d3 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01269531,  0.37890625, ..., -0.5       ,
           -0.39941406, -0.42089844]), textin=CWbytearray(b'38 3e 26 72 a6 6f c3 e0 0b 0c e4 a9 9b 18 a3 64'), textout=CWbytearray(b'fe c7 16 2b 51 88 07 c1 ba 11 9b 3d 0b df 21 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.015625  ,  0.3828125 , ..., -0.5       ,
           -0.390625  , -0.41894531]), textin=CWbytearray(b'dc 7c 8f a2 a1 09 c3 25 53 62 02 34 c4 ff 47 76'), textout=CWbytearray(b'8c e5 e3 b0 8f ff 24 9b 0b 87 6b 31 d7 a8 10 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04199219,  0.40039062, ..., -0.5       ,
           -0.44628906, -0.44335938]), textin=CWbytearray(b'86 29 f5 6e 2c 8f 25 c7 e1 da 22 52 75 7f e7 12'), textout=CWbytearray(b'f7 f7 97 84 94 91 8b 96 70 c9 c7 e7 04 b1 e9 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03222656,  0.39160156, ..., -0.5       ,
           -0.42578125, -0.43847656]), textin=CWbytearray(b'93 87 e1 5f fc 3d e5 45 13 ff 79 23 97 13 3e 36'), textout=CWbytearray(b'85 da 4f 81 a0 e5 0c 76 bc b0 f1 c9 fa 6f 4f a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13183594,  0.01171875,  0.37597656, ..., -0.5       ,
           -0.38671875, -0.3984375 ]), textin=CWbytearray(b'ed 89 ee ed 83 7e 52 a8 fc 20 77 27 5b 17 20 96'), textout=CWbytearray(b'52 a6 a3 af ae 95 ab 06 54 5d 32 5a 36 ac d6 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.40917969, -0.41503906]), textin=CWbytearray(b'cd ac 81 ea 83 97 de 0b 14 54 fd d5 b9 46 38 61'), textout=CWbytearray(b'c6 c1 6a c7 87 69 1d 14 59 f6 65 c4 96 93 b4 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 ,  0.00683594,  0.37402344, ..., -0.5       ,
           -0.421875  , -0.42382812]), textin=CWbytearray(b'ac c5 32 7c 21 b7 7b 97 d1 32 dc 37 6c ea 08 b0'), textout=CWbytearray(b'a6 d5 33 2e 5f 48 b5 5c 2e d3 a0 a0 61 ef 75 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.0234375 ,  0.38574219, ..., -0.5       ,
           -0.38378906, -0.40820312]), textin=CWbytearray(b'9f cb b1 6b e5 c9 44 fc 93 b0 e9 33 7f 67 b9 52'), textout=CWbytearray(b'df f5 fc 5e 9b a6 4b ee e1 64 cd ed 21 e8 de 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.02441406,  0.38671875, ..., -0.5       ,
           -0.4296875 , -0.4296875 ]), textin=CWbytearray(b'ed b2 d5 8b 34 40 1e d2 3a fd 6e 55 f6 3d 73 ef'), textout=CWbytearray(b'45 9e e5 c0 91 90 2d 8a 83 1c 1c ab e8 1f f6 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.41015625, -0.41601562]), textin=CWbytearray(b'e2 55 07 ee 75 1b 4a 42 cf e1 9f b9 f5 06 2b 9a'), textout=CWbytearray(b'50 37 f3 8c cd 11 fd 33 51 d5 5a 6a 83 06 fe f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 ,  0.00878906,  0.37402344, ..., -0.5       ,
           -0.39355469, -0.40234375]), textin=CWbytearray(b'66 b9 a5 7c a6 5e 4e 3a d5 be 47 7f 48 13 78 97'), textout=CWbytearray(b'0c 5d 0a 49 66 a1 55 15 44 26 dd ca de 8d 94 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.40917969, ..., -0.5       ,
           -0.43066406, -0.4296875 ]), textin=CWbytearray(b'40 ed 1f 81 f7 02 d4 62 d6 04 c8 20 29 3d f1 fc'), textout=CWbytearray(b'10 b3 da 3d 89 8c 82 f3 ac c4 6a 8b 4d 2f 3c 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03613281,  0.39550781, ..., -0.5       ,
           -0.421875  , -0.42578125]), textin=CWbytearray(b'f0 a7 83 c9 95 50 fe b4 c4 75 97 5c 47 85 95 0f'), textout=CWbytearray(b'29 86 89 62 d4 ac 4f 96 9c 52 18 87 01 ad 48 af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04003906,  0.3984375 , ..., -0.5       ,
           -0.44140625, -0.43652344]), textin=CWbytearray(b'd4 bc 28 e7 70 01 7b 14 38 5f 10 56 98 9a 59 5d'), textout=CWbytearray(b'00 b2 0b 60 73 61 af b6 d6 97 06 56 45 31 4a 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.39453125, -0.40136719]), textin=CWbytearray(b'58 cb 7e 2a 96 6a dd cc ce df e6 db ce d1 38 ed'), textout=CWbytearray(b'4e 08 ca 70 f5 fd c0 44 ba d8 06 dc c9 13 4c ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.05371094,  0.40917969, ..., -0.5       ,
           -0.41113281, -0.43066406]), textin=CWbytearray(b'cd 34 4a 11 90 bb 70 f6 f4 72 3b 7d f5 45 3c f5'), textout=CWbytearray(b'93 79 17 c4 5c 3b b1 fb 81 8e 7c f5 90 b2 74 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.03417969,  0.39257812, ..., -0.5       ,
           -0.40332031, -0.41113281]), textin=CWbytearray(b'57 32 f8 9d c4 44 3e 57 4b 47 0b 35 c1 13 a9 f9'), textout=CWbytearray(b'9b 6f de 4c 3e 5c ef 07 1f e4 3d b7 88 4c fc 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03515625,  0.37890625, ..., -0.5       ,
           -0.42382812, -0.42578125]), textin=CWbytearray(b'fd 3f 82 10 98 47 7a 4d 53 9e 80 1a 74 9e bc 80'), textout=CWbytearray(b'3c 52 71 b5 0b 4a 30 23 08 cb e0 9c 47 80 7b 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.4140625 , -0.43457031]), textin=CWbytearray(b'35 3a d6 15 dd d6 d4 8f 8c e9 40 e8 d8 2c 79 72'), textout=CWbytearray(b'a1 f1 f1 db af f4 18 03 1f 72 40 01 f5 43 a0 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.43066406, -0.42871094]), textin=CWbytearray(b'60 b2 eb 7f ce 4f 59 3f 45 b3 09 a8 4c 1f f0 63'), textout=CWbytearray(b'48 d2 66 04 2e 5f 6f 9d b5 92 08 29 47 86 21 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02441406,  0.38574219, ..., -0.5       ,
           -0.39648438, -0.40625   ]), textin=CWbytearray(b'98 65 cc 70 4c 58 82 8c 5e 22 e8 8a b9 4a 74 10'), textout=CWbytearray(b'bf c8 3a 50 2a 36 78 9c 27 67 52 6f 7c 07 53 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02441406,  0.38964844, ..., -0.5       ,
           -0.39453125, -0.40136719]), textin=CWbytearray(b'bb 8d e1 49 3d a7 1b c8 16 b1 c4 ae cf f2 df 36'), textout=CWbytearray(b'0e 28 38 1e 30 93 85 ca 9c ed f1 98 3e 49 bb 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.046875  ,  0.40527344, ..., -0.5       ,
           -0.41113281, -0.41503906]), textin=CWbytearray(b'c0 97 4f 31 e0 5f 4f 14 15 f3 34 54 ba 1a 09 92'), textout=CWbytearray(b'd9 e7 66 04 da 69 3c 76 aa 73 b3 02 db 6d 6f e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03417969,  0.39550781, ..., -0.5       ,
           -0.40527344, -0.40917969]), textin=CWbytearray(b'c1 b9 58 f4 25 15 ea 94 2f 01 6d c3 8a 93 93 92'), textout=CWbytearray(b'e0 42 98 2d 50 06 db a8 65 94 1d 9b 2c 95 74 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.05761719,  0.41308594, ..., -0.5       ,
           -0.42285156, -0.42578125]), textin=CWbytearray(b'd0 6b 67 77 9a e0 76 7a 5b 30 2f 71 ed 4d 0c 72'), textout=CWbytearray(b'7e 3c ae 2e 37 0f 4c b8 cc 23 7a 84 e2 63 6c 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.0234375 ,  0.38769531, ..., -0.5       ,
           -0.40820312, -0.4140625 ]), textin=CWbytearray(b'99 7d 35 b1 12 ba 91 1e ad d5 a1 d2 8a 97 8d 58'), textout=CWbytearray(b'b1 7c 9d c8 0a f1 bb 86 3b ab 44 ad c8 06 44 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188,  0.01464844,  0.37890625, ..., -0.5       ,
           -0.40234375, -0.41015625]), textin=CWbytearray(b'20 fc 6e 23 e9 47 64 a9 9c 63 d6 0a a3 a1 f9 a1'), textout=CWbytearray(b'f7 b8 5a 3c f5 c6 ce 5f 65 68 b9 7b e1 6e fa 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.03222656,  0.39257812, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'9c 0c 09 29 66 db 8e 91 45 84 59 9d 82 64 80 6c'), textout=CWbytearray(b'16 5a e3 b9 7f b1 57 e0 07 c0 e2 a5 e8 49 e4 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.04003906,  0.3984375 , ..., -0.5       ,
           -0.42480469, -0.42480469]), textin=CWbytearray(b'38 ae ce ab 32 7a 3c cf 43 1b 38 36 b0 79 94 b8'), textout=CWbytearray(b'33 22 82 c3 c4 5e 04 1b e2 61 b1 83 f9 1e ca e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.39746094, ..., -0.5       ,
           -0.41601562, -0.41796875]), textin=CWbytearray(b'a3 2b 3d 74 4b 89 7c 8f aa 1d ad 63 16 47 06 da'), textout=CWbytearray(b'11 56 f4 4f 07 5c 90 13 cc f6 ca ed ea 33 24 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.37988281, -0.39453125]), textin=CWbytearray(b'b1 51 cc a5 75 c3 20 95 5f a5 7f 69 33 c9 7c 1f'), textout=CWbytearray(b'34 ef df 7e 4c 9a 74 5c 42 85 06 0c f0 2f 65 e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.39648438, -0.40332031]), textin=CWbytearray(b'04 3d 9b 80 2e e6 5a cc 51 8d 07 7a 3c db 3e d8'), textout=CWbytearray(b'f5 b9 d5 32 58 55 e1 3d 2b 38 fa 24 0a 97 be 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13671875,  0.015625  ,  0.37792969, ..., -0.5       ,
           -0.41699219, -0.41796875]), textin=CWbytearray(b'ba 95 2c bc da 17 e0 87 05 da fe 7b d8 bd bb 25'), textout=CWbytearray(b'a8 83 54 b6 59 61 aa e6 3c 70 3c eb 88 02 81 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.3828125 , -0.39550781]), textin=CWbytearray(b'5d 76 c8 48 42 31 0c bb 0e 0c ed d9 72 4a 8e f3'), textout=CWbytearray(b'1d 02 cc cc 7b 74 41 13 70 1b 3b 69 80 fc 5d 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.39941406, ..., -0.5       ,
           -0.40917969, -0.4140625 ]), textin=CWbytearray(b'e4 37 5b 2e 88 64 91 02 59 83 7f b7 6d 2f 3f 22'), textout=CWbytearray(b'21 0c e8 f9 4f 29 bb d0 d2 26 be 3f d0 40 f6 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04199219,  0.3984375 , ..., -0.5       ,
           -0.40039062, -0.41015625]), textin=CWbytearray(b'94 02 56 78 8d 76 dd c9 0e 59 bd 46 2f 4c c9 d2'), textout=CWbytearray(b'7e 5b c3 7c 71 ab 18 f2 cc 60 59 c6 04 c8 98 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19628906,  0.046875  ,  0.40136719, ..., -0.5       ,
           -0.41503906, -0.41601562]), textin=CWbytearray(b'ae df 61 62 aa d0 d8 bd 0c 95 ba bc ad 76 1f 23'), textout=CWbytearray(b'b3 9a e5 7f 79 36 a0 11 a0 2b a2 08 40 27 ac 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.01855469,  0.38574219, ..., -0.5       ,
           -0.4296875 , -0.43066406]), textin=CWbytearray(b'6c 78 34 6b db 2a ff 14 19 27 13 29 e3 44 9f 7a'), textout=CWbytearray(b'6d a1 ec 51 5f 54 62 9a 77 da e8 da 30 df b1 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03027344,  0.39257812, ..., -0.5       ,
           -0.41796875, -0.42285156]), textin=CWbytearray(b'a2 02 36 37 00 bd 70 87 04 bc 9c af b8 ad 74 22'), textout=CWbytearray(b'bc c7 fd 19 a6 de f7 7a 51 cd 5d d2 52 a5 ea 8f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03222656,  0.39160156, ..., -0.5       ,
           -0.43066406, -0.43359375]), textin=CWbytearray(b'ca 6f 42 df 85 42 b5 d1 d7 a0 65 d3 73 80 df da'), textout=CWbytearray(b'e0 5f 39 84 c2 6b 52 f7 7f 6a 92 bd 57 3e c7 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02246094,  0.38671875, ..., -0.5       ,
           -0.42382812, -0.42675781]), textin=CWbytearray(b'a7 f5 8d 68 38 b6 a0 fe 1d 1e fd ba 9a c1 2e 05'), textout=CWbytearray(b'08 4f ed 44 c1 7d a4 9c 74 3e b5 81 16 52 a5 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04296875,  0.39941406, ..., -0.5       ,
           -0.41113281, -0.41699219]), textin=CWbytearray(b'36 61 20 9c 10 5e ee 75 f2 2f 50 dd 27 65 ea 84'), textout=CWbytearray(b'92 29 10 8d d1 f9 a5 3d 4b a3 33 f4 35 8c 2b 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.03125   ,  0.390625  , ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'5d 1c 28 53 9a e5 8d 97 3c 05 5b 43 b3 ee 4e 02'), textout=CWbytearray(b'ca 72 a6 8f 32 f5 70 eb ac 59 ed de 71 19 2c 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.45507812, -0.44921875]), textin=CWbytearray(b'85 4e 35 36 37 dc 20 12 a1 48 a6 f1 d4 d5 27 61'), textout=CWbytearray(b'fb 1e 7c 5d 55 8d ec d7 b8 6f b4 43 72 a2 e1 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04199219,  0.40136719, ..., -0.5       ,
           -0.4453125 , -0.44042969]), textin=CWbytearray(b'57 98 17 5b 11 39 97 da ec 54 88 2a 4e 3f 9d 51'), textout=CWbytearray(b'6f e9 25 8a c8 51 b6 de 14 37 1a 5f 5a 4c 1c 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'56 3e 8f 32 f2 82 c3 ea 11 fb c7 1a 3b 43 6f 27'), textout=CWbytearray(b'95 d5 fa 4a ce ef 2e f5 eb d4 71 45 97 0c e4 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05175781,  0.40722656, ..., -0.5       ,
           -0.38867188, -0.40136719]), textin=CWbytearray(b'0e 39 7a 7b 2b d4 10 a1 e4 c6 81 57 06 05 3c 9f'), textout=CWbytearray(b'7f 25 c4 b7 2d 00 cb 6e 3a 54 a9 c0 fc 9c de ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39648438, ..., -0.5       ,
           -0.42773438, -0.44042969]), textin=CWbytearray(b'fa 99 85 23 77 17 49 f9 3a 98 ff 5e b4 86 34 37'), textout=CWbytearray(b'f0 d8 29 5b 6f 52 6e 12 40 82 56 9a c0 de df 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.05078125,  0.40722656, ..., -0.5       ,
           -0.37695312, -0.390625  ]), textin=CWbytearray(b'a6 16 b0 c2 db 0c e5 ae a1 ca f7 01 80 6a f6 8c'), textout=CWbytearray(b'e0 ca 73 b3 dc e3 bd 8e 39 32 97 f3 0d 7b af 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13964844,  0.01464844,  0.37695312, ..., -0.5       ,
           -0.4296875 , -0.43066406]), textin=CWbytearray(b'2e b7 62 9e 8c 5a 76 a3 21 c7 75 05 25 b3 57 1b'), textout=CWbytearray(b'32 d4 c0 60 f2 47 d3 b0 bc d4 44 30 ca 0f 21 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.02832031,  0.38769531, ..., -0.5       ,
           -0.38378906, -0.40820312]), textin=CWbytearray(b'0f db e9 1b 26 1b 35 7f 4f 01 98 3d 8e 0a 35 74'), textout=CWbytearray(b'0f 83 23 fc 54 ba d9 e3 41 f2 2b fb 32 88 c3 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04785156,  0.40527344, ..., -0.5       ,
           -0.43359375, -0.43359375]), textin=CWbytearray(b'df 1a ab 8a db ba 1b ef 7c d3 ec 74 a3 0e 5d 83'), textout=CWbytearray(b'c0 ef 05 04 2c 12 df 4e 94 f7 eb 1a b5 57 84 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.0234375 ,  0.38476562, ..., -0.5       ,
           -0.41699219, -0.421875  ]), textin=CWbytearray(b'18 93 6b 76 9b 55 3a cf 02 69 26 08 8d 0e 0b bd'), textout=CWbytearray(b'43 51 1c 3b 64 4e 07 10 2b 0c 97 07 21 b9 4e 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.3984375 , -0.40429688]), textin=CWbytearray(b'fc 16 a1 67 f0 c6 9a 8b a7 e3 1e a8 ba a5 8e b7'), textout=CWbytearray(b'ed ba b3 f3 de 8c a9 b6 f5 05 47 e5 39 b8 e1 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14257812,  0.015625  ,  0.38183594, ..., -0.5       ,
           -0.44628906, -0.4453125 ]), textin=CWbytearray(b'52 52 48 56 bf a0 3e 48 6b aa 3b 42 26 ec 65 ff'), textout=CWbytearray(b'a7 7a a5 22 65 68 62 03 4a 3a a3 77 5b 97 aa b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.41210938, -0.41992188]), textin=CWbytearray(b'c7 d2 df 6b 7d 50 37 a9 f2 f8 ec bf ae c3 da 37'), textout=CWbytearray(b'50 de b6 97 4d 59 b5 a0 27 34 e4 2f 29 46 8e 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.40722656, -0.4140625 ]), textin=CWbytearray(b'37 6a 6f 35 24 a2 e9 e5 a4 ff ed 89 95 15 a0 45'), textout=CWbytearray(b'ed 0a 42 1e 61 17 ba bb b3 e5 c2 21 e6 54 af 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04394531,  0.40527344, ..., -0.5       ,
           -0.41796875, -0.4375    ]), textin=CWbytearray(b'37 fa b5 ca 66 d0 f0 f9 08 6f 84 76 c4 e4 2f 99'), textout=CWbytearray(b'56 41 3f b0 2b f6 1a 36 2a 52 b4 61 00 3b e0 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.02929688,  0.390625  , ..., -0.5       ,
           -0.38476562, -0.39550781]), textin=CWbytearray(b'31 06 24 5d 57 51 c5 90 9b 39 7b 47 55 a1 3c 4a'), textout=CWbytearray(b'c0 1b c2 69 9e 09 06 41 5f 8d bd 47 28 6e e1 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.02050781,  0.38378906, ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'24 cd fe 53 ec f7 e2 eb c8 a1 9f ea a7 9b e6 28'), textout=CWbytearray(b'a8 52 c4 8d cf 7f df 2d a8 44 38 e3 e0 42 54 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02636719,  0.38867188, ..., -0.5       ,
           -0.42871094, -0.4296875 ]), textin=CWbytearray(b'3a 8d 2a cd de 7e f7 1c 01 db 4c b7 72 f5 5f d9'), textout=CWbytearray(b'7a fa d5 98 f3 fc b9 19 da a2 25 dc 6d 16 72 f9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.02246094,  0.38574219, ..., -0.5       ,
           -0.39355469, -0.40332031]), textin=CWbytearray(b'90 9f 15 2c f2 f6 61 50 07 d4 92 be cc df 50 05'), textout=CWbytearray(b'c2 42 d3 58 b1 75 9e a4 95 63 c3 b9 77 94 17 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.02929688,  0.39160156, ..., -0.5       ,
           -0.38378906, -0.39648438]), textin=CWbytearray(b'7e 68 42 3f 82 38 05 51 af f8 b3 38 b9 0f 9c ef'), textout=CWbytearray(b'4b 5c ed 9f 3d 49 83 c8 de 6b 60 14 f0 f0 cd ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12988281,  0.00683594,  0.35839844, ..., -0.5       ,
           -0.40429688, -0.41113281]), textin=CWbytearray(b'1e c6 b1 90 9b 75 5a 9a b7 3f b2 2e 1c 81 9d 07'), textout=CWbytearray(b'89 0c 7c e9 0e 9d 95 f7 b7 7e a3 11 2b e8 9b b0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.00683594,  0.37304688, ..., -0.5       ,
           -0.390625  , -0.40136719]), textin=CWbytearray(b'd5 b2 9e 40 83 c0 d8 67 36 d4 76 cb 2b 65 6e 25'), textout=CWbytearray(b'a0 07 77 a1 f1 be db 4a 00 ed bb 5f da ef 13 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03222656,  0.38964844, ..., -0.5       ,
           -0.39941406, -0.40722656]), textin=CWbytearray(b'4a bb 34 42 68 7c 7c ec be f9 bc fc 35 d3 44 13'), textout=CWbytearray(b'5e 57 41 61 3d 8f 73 68 33 9b 61 35 a6 16 f8 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01855469,  0.38085938, ..., -0.5       ,
           -0.4296875 , -0.4296875 ]), textin=CWbytearray(b'51 dc b8 8c 99 d9 4e 6c aa 68 8d 7c 82 04 22 2e'), textout=CWbytearray(b'2a 1d 87 cd ec 52 32 0a 09 2f d0 34 ff c1 0f a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04199219,  0.40234375, ..., -0.5       ,
           -0.40429688, -0.42480469]), textin=CWbytearray(b'08 4e 6f 44 e8 04 ef 93 1c ea d4 22 65 3c 84 59'), textout=CWbytearray(b'38 0e d6 3e 5b 82 25 f9 d1 ff 33 52 18 0e d3 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.39160156, -0.40039062]), textin=CWbytearray(b'bf 98 c1 ce 1e 66 ae 9f 02 e9 6a d6 31 03 fe 49'), textout=CWbytearray(b'66 2d dc 3c 08 c1 a8 3d f6 fe a3 09 bc f3 4e 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.05859375,  0.40136719, ..., -0.5       ,
           -0.40332031, -0.41015625]), textin=CWbytearray(b'6f 44 ca f1 d3 17 af c0 63 bc 8c d7 42 e1 92 70'), textout=CWbytearray(b'ab 04 32 58 8a 35 f5 f6 28 b6 9b 12 64 94 21 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04394531,  0.38574219, ..., -0.5       ,
           -0.40625   , -0.41796875]), textin=CWbytearray(b'c4 6b 96 5b f3 72 6c 31 e3 18 07 29 31 a1 71 01'), textout=CWbytearray(b'3b 43 b5 93 6f a8 e8 09 d9 42 24 d3 d8 c9 b0 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.05175781,  0.40722656, ..., -0.5       ,
           -0.42285156, -0.42285156]), textin=CWbytearray(b'79 f4 73 e7 55 4a 9b a8 6a a1 85 d4 f4 2c 33 0b'), textout=CWbytearray(b'7f cd c8 61 07 2f 40 45 24 41 85 ae b7 41 59 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39160156, ..., -0.5       ,
           -0.40625   , -0.41308594]), textin=CWbytearray(b'9b 7f 44 35 94 19 25 94 03 6a 03 44 22 8f cc ea'), textout=CWbytearray(b'0e 66 29 16 36 81 ce 1b dd 7e 74 30 a6 04 61 a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03027344,  0.39257812, ..., -0.5       ,
           -0.45410156, -0.44921875]), textin=CWbytearray(b'97 04 c6 64 1b ee 0b 4e 73 85 3a cb 79 8a ab 79'), textout=CWbytearray(b'04 0d ac b8 cd b7 ac de f5 02 f2 d7 99 6d 78 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01855469,  0.38085938, ..., -0.5       ,
           -0.40527344, -0.41113281]), textin=CWbytearray(b'3e b6 88 40 b2 b4 33 82 08 9e a4 31 e2 13 47 7b'), textout=CWbytearray(b'bb 68 8a 9b a1 02 51 44 0c 27 0d de 21 98 7a ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.02539062,  0.37304688, ..., -0.5       ,
           -0.41015625, -0.41308594]), textin=CWbytearray(b'fc 5c 96 57 7f 11 67 86 5f da 1b ff 16 84 69 b1'), textout=CWbytearray(b'82 e9 ff 79 00 7b e3 86 38 06 22 40 73 ce 81 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.41113281, -0.41503906]), textin=CWbytearray(b'e7 10 44 07 90 c2 f8 b3 ec 7c 28 b9 a1 49 fd 95'), textout=CWbytearray(b'0b 95 1e 65 7d 27 d4 e3 aa 83 33 1b e2 c4 39 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.01953125,  0.3828125 , ..., -0.5       ,
           -0.41894531, -0.42285156]), textin=CWbytearray(b'a7 98 ec bc 9d 30 45 14 29 aa c9 77 1d f5 c6 a2'), textout=CWbytearray(b'12 a8 ed 9e c7 38 45 e4 77 20 13 8d 11 74 d8 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.02929688,  0.39160156, ..., -0.5       ,
           -0.41894531, -0.42285156]), textin=CWbytearray(b'c6 50 8e b8 dc 63 38 79 41 68 71 36 88 9e e4 d4'), textout=CWbytearray(b'06 1d 38 76 34 bf 1c 78 4c 23 36 3f 14 01 31 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.40820312, ..., -0.5       ,
           -0.3984375 , -0.40722656]), textin=CWbytearray(b'c2 b6 ec be 59 c9 d7 8a 08 0e 16 5b 6f 85 79 89'), textout=CWbytearray(b'9f 8d ce 45 37 a7 c6 96 88 5d 4c 9a c7 e0 10 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.453125  , -0.46484375]), textin=CWbytearray(b'b7 72 56 b2 5e ef 2f 48 b1 aa 13 d4 cd 18 53 e6'), textout=CWbytearray(b'92 e2 80 54 00 c1 66 b1 32 19 6a b7 0d a2 fb cc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40136719, ..., -0.5       ,
           -0.44335938, -0.44140625]), textin=CWbytearray(b'8c ff 24 5a 2c 1b 82 fd 71 4e 13 3f dc 74 52 4a'), textout=CWbytearray(b'a7 43 c4 97 5b a9 58 41 23 e2 66 6a d5 f0 a2 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19824219,  0.06152344,  0.41503906, ..., -0.5       ,
           -0.39453125, -0.40136719]), textin=CWbytearray(b'40 d5 2e e4 fc 94 9a ee 1a d3 68 fa ac 9c 2a f9'), textout=CWbytearray(b'ae 15 48 a9 91 46 de 55 87 35 4e 12 04 04 9f 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19628906,  0.04492188,  0.40332031, ..., -0.5       ,
           -0.40332031, -0.40820312]), textin=CWbytearray(b'6f 83 0f d3 ef 1c 89 d1 58 e2 fa ef be 10 ba d3'), textout=CWbytearray(b'41 1a e5 16 ac 43 4a 7a e0 29 5f d1 6d 7c 3f 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.3984375 , -0.41699219]), textin=CWbytearray(b'5f 25 c2 42 f2 8b fd b7 64 73 d7 7f bb cb ad 8c'), textout=CWbytearray(b'a3 04 0d c1 5b 0e 97 b0 ad 31 c4 9f 75 d3 de 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.42480469, -0.43652344]), textin=CWbytearray(b'7d 3c c2 a3 4f ac d3 f7 eb 53 e5 69 34 51 6e 06'), textout=CWbytearray(b'04 1a 2e fe 96 23 80 31 81 4c b8 e9 7b 9a 2c 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.140625  ,  0.015625  ,  0.38085938, ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'0e 11 b3 10 4e e0 3b ff 9f d1 d4 87 99 6e c9 e4'), textout=CWbytearray(b'ff 9f 07 07 c1 1d 11 bd 42 79 13 2a 2b 53 5a 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40136719, ..., -0.5       ,
           -0.41113281, -0.41601562]), textin=CWbytearray(b'53 54 ad 79 b0 fa f1 fa e3 9c fa ca 4e 46 af 68'), textout=CWbytearray(b'7f d7 b4 a6 46 55 7f 22 b6 9c ca 64 e0 61 b3 c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.42089844, -0.4375    ]), textin=CWbytearray(b'72 a6 fd 60 bb 6b b8 c6 59 6e 94 80 35 5d e5 78'), textout=CWbytearray(b'2b 7a 8b 3e 55 fe 82 12 ef 27 b1 70 55 f0 04 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01367188,  0.37890625, ..., -0.5       ,
           -0.40332031, -0.40820312]), textin=CWbytearray(b'99 70 5d f9 13 93 f5 71 e0 79 87 cd 8e 57 91 d9'), textout=CWbytearray(b'a2 cc 03 4d 5e 14 8d 2c 5d f0 28 b2 67 03 52 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.36816406, -0.39648438]), textin=CWbytearray(b'fb 1b 2b ca 13 fb d9 b1 71 30 b4 ba 37 a1 f2 01'), textout=CWbytearray(b'fe 1c fb e5 c5 d0 38 20 1e cc c5 8f 1b 59 f8 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01855469,  0.38183594, ..., -0.5       ,
           -0.39550781, -0.40429688]), textin=CWbytearray(b'fe d9 a8 33 75 83 32 f7 36 36 01 96 c8 6c f4 bc'), textout=CWbytearray(b'57 3e 8f 3b 2b 8c af e3 e2 e1 ea 8f f4 1c 75 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.046875  ,  0.40234375, ..., -0.5       ,
           -0.43457031, -0.43359375]), textin=CWbytearray(b'84 e5 59 85 25 f7 b0 b2 4f 2a 4b 63 5e de 6c c7'), textout=CWbytearray(b'f4 eb 36 71 97 ad 7b 60 4b ae 73 b2 d3 c2 9a dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'19 86 ea 3a b2 6e f2 c1 e5 1a a8 dd 98 3d 7f 33'), textout=CWbytearray(b'fd b2 e5 60 cf 9d 39 ad 82 38 da f5 29 8a 7b d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.44140625, -0.44042969]), textin=CWbytearray(b'16 26 f4 3a da ac 27 e6 11 b7 4e 01 bb d5 6f 93'), textout=CWbytearray(b'2f fc fe 1d 29 92 c1 80 d8 dd cf 2e 09 c2 cb 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03417969,  0.3828125 , ..., -0.5       ,
           -0.39453125, -0.40429688]), textin=CWbytearray(b'42 60 b8 bc c2 f1 91 0b 00 65 08 28 59 d7 54 37'), textout=CWbytearray(b'e7 2b 3a da e0 7c aa d8 b0 6c a5 a5 e8 0a de 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04003906,  0.3984375 , ..., -0.5       ,
           -0.39648438, -0.40625   ]), textin=CWbytearray(b'3a 12 fd 3d c1 42 f5 2b 1c 9b 75 a7 4f 3b 1d 43'), textout=CWbytearray(b'e4 22 8a 35 97 20 96 f3 12 a3 ea 29 99 e0 a2 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12890625,  0.00683594,  0.37304688, ..., -0.5       ,
           -0.36914062, -0.3828125 ]), textin=CWbytearray(b'31 3b 40 e0 af 9f 52 7e cb 09 00 18 da 23 90 e0'), textout=CWbytearray(b'54 2d 12 26 58 a4 8a cf 8e f6 2c 29 74 e6 7e 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04199219,  0.39941406, ..., -0.5       ,
           -0.40917969, -0.41308594]), textin=CWbytearray(b'89 61 3d 1d 7d 9f 31 7b 7e 20 ec b3 2a 1b 4d 23'), textout=CWbytearray(b'b3 32 2b b6 e1 89 05 ac bb 6a 9e 68 b8 6d 44 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.03417969,  0.390625  , ..., -0.5       ,
           -0.43164062, -0.43066406]), textin=CWbytearray(b'62 7a 35 9a 30 74 37 72 f9 09 1c c3 8e de c0 ac'), textout=CWbytearray(b'e2 f5 fc 52 03 8e c9 08 7a 5a 34 0c 4f 6c 64 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.04296875,  0.40234375, ..., -0.5       ,
           -0.44238281, -0.44042969]), textin=CWbytearray(b'a0 20 a4 01 5c 66 4a bc 44 04 ce 51 16 8c fd 76'), textout=CWbytearray(b'c8 65 db 61 3b ab 64 f0 1b bb 8d bd 72 76 37 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.0234375 ,  0.38671875, ..., -0.5       ,
           -0.37597656, -0.40234375]), textin=CWbytearray(b'1e d2 17 eb 84 c9 9e 32 80 a7 d4 1e ea ab 35 16'), textout=CWbytearray(b'29 8e 73 71 9a 6b 4b 23 96 e7 19 fa ea f0 19 d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.02246094,  0.38574219, ..., -0.5       ,
           -0.40136719, -0.40722656]), textin=CWbytearray(b'06 57 1b be 44 50 a7 c8 97 b6 1f 79 7e b5 53 ed'), textout=CWbytearray(b'22 04 cf 10 ce f8 4b d3 d4 93 a9 a7 40 f5 85 b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.38183594, ..., -0.5       ,
           -0.41796875, -0.42285156]), textin=CWbytearray(b'45 e6 40 b9 bf 19 c1 27 01 57 6d cc 8e 44 6d 5c'), textout=CWbytearray(b'3c f5 76 8d 05 64 ad 64 ea 0a ca bf 55 57 74 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14257812,  0.015625  ,  0.38085938, ..., -0.5       ,
           -0.36621094, -0.38183594]), textin=CWbytearray(b'55 29 ba 76 bf 85 86 43 04 f7 93 b0 1e 1e e2 b1'), textout=CWbytearray(b'd7 b6 a0 ae 01 7d bd e2 a0 3e ba 21 07 50 c7 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19335938,  0.05664062,  0.39746094, ..., -0.5       ,
           -0.40722656, -0.4140625 ]), textin=CWbytearray(b'a0 0f d1 d4 71 be c2 e7 cb 89 c5 bb 7d f6 43 73'), textout=CWbytearray(b'2c f6 60 1c a1 2d d6 8b e0 e8 72 2e 74 6c b9 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.02734375,  0.390625  , ..., -0.5       ,
           -0.41894531, -0.421875  ]), textin=CWbytearray(b'50 fb a5 f3 d8 3e 05 28 f2 fc 74 4d 8c 59 43 8c'), textout=CWbytearray(b'46 da 8b a4 4e 38 5c 6f b3 46 13 7c 86 f2 1e 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04296875,  0.39941406, ..., -0.5       ,
           -0.41992188, -0.421875  ]), textin=CWbytearray(b'20 39 0a e2 68 74 21 66 24 04 f1 d5 06 83 96 10'), textout=CWbytearray(b'82 41 82 48 b4 13 8f 8f 75 fa 9b e2 b0 85 ca 29'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.40625   , -0.41210938]), textin=CWbytearray(b'5d 97 30 00 01 c1 f3 52 45 c5 92 9b cf 52 e7 2d'), textout=CWbytearray(b'61 fe 27 3d bd cb 2d 5f b4 dd 93 58 58 83 3f 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.40332031, -0.41015625]), textin=CWbytearray(b'ad bc 53 4c c2 09 04 b5 e6 c4 84 a8 15 0d a1 ff'), textout=CWbytearray(b'4e 9e c7 0d f0 87 b9 89 6c c0 75 5a 21 3e 65 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.43359375, -0.43359375]), textin=CWbytearray(b'41 8d 6a cb dc e7 5d a3 e9 46 86 6d 92 b6 b6 79'), textout=CWbytearray(b'8f 3e c0 1a 12 4f 6d 2b 4d 74 e9 13 b1 de a4 af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04492188,  0.40332031, ..., -0.5       ,
           -0.39746094, -0.40625   ]), textin=CWbytearray(b'3b bb 8a 75 cd 75 1f 25 24 2b 9c cc 03 f0 c4 1f'), textout=CWbytearray(b'66 9b 8f 61 aa 66 15 81 fa bb 5e 92 53 7a e4 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04199219,  0.39941406, ..., -0.5       ,
           -0.41601562, -0.41894531]), textin=CWbytearray(b'bb 0f 6f 21 59 2a ef 1a 32 ee 45 73 3a b1 af 28'), textout=CWbytearray(b'b3 f1 80 4a 47 c5 99 60 0b 35 3f 4a e6 8f f3 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03515625,  0.39453125, ..., -0.5       ,
           -0.43847656, -0.43554688]), textin=CWbytearray(b'f3 a0 bb 87 ff c9 64 d5 4f b4 80 09 9d ce 62 c5'), textout=CWbytearray(b'57 b7 c6 08 d7 ec 67 a5 e4 10 99 c5 ac 8e 9a 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04882812,  0.38964844, ..., -0.5       ,
           -0.41210938, -0.41796875]), textin=CWbytearray(b'dd a7 53 4b c5 a1 3b 41 ff c7 ad 5a 1f 70 37 a4'), textout=CWbytearray(b'eb 67 81 74 76 99 08 a1 64 d4 07 70 8d 4c 3f 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.40332031, -0.41015625]), textin=CWbytearray(b'b9 69 50 0d f0 ae a4 43 88 8c 32 17 aa 23 3b 5d'), textout=CWbytearray(b'4d 83 4c d9 59 58 80 66 69 5d db 6c 8a bb ce 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.00585938,  0.375     , ..., -0.5       ,
           -0.39160156, -0.40136719]), textin=CWbytearray(b'14 f5 11 0c b8 c0 77 7c 44 cc 6a 79 bd 8e 37 d6'), textout=CWbytearray(b'48 c1 0c 74 04 f0 70 eb 1b de 55 09 00 8f 82 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01855469,  0.38476562, ..., -0.5       ,
           -0.44433594, -0.44238281]), textin=CWbytearray(b'36 20 1f 20 28 c3 eb ed db e0 9a 8a d9 73 35 e0'), textout=CWbytearray(b'de a7 97 e2 b5 ee bf 3f 17 91 89 a7 06 18 da 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04199219,  0.39746094, ..., -0.5       ,
           -0.43261719, -0.43261719]), textin=CWbytearray(b'9f 84 7f 33 64 12 25 22 34 2c 76 58 c3 ab f3 19'), textout=CWbytearray(b'c0 15 20 a4 0f 1e d1 65 f7 8b d7 f2 0a 1e 7e 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.38378906, ..., -0.5       ,
           -0.40039062, -0.40722656]), textin=CWbytearray(b'7c a3 a6 e1 a0 20 db 3b f5 e3 bb 80 9f 92 6d 0d'), textout=CWbytearray(b'ec 90 5d 39 e8 86 aa 41 3e fe 25 2f 22 4d d0 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.01269531,  0.37890625, ..., -0.5       ,
           -0.38476562, -0.39746094]), textin=CWbytearray(b'0e ec 88 f9 01 44 cc 92 0b 3b 50 f3 68 aa 76 47'), textout=CWbytearray(b'35 be db 25 a1 24 33 01 9b f5 97 0f 9a a3 8c e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04589844,  0.40429688, ..., -0.5       ,
           -0.40722656, -0.42675781]), textin=CWbytearray(b'2c 16 d6 e5 48 10 bc a8 c7 3f 2b 51 4f 1d 6c f7'), textout=CWbytearray(b'9a 93 29 c4 45 85 5c 56 6f 15 ab 4d 69 eb 40 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.046875  ,  0.38867188, ..., -0.5       ,
           -0.40039062, -0.40820312]), textin=CWbytearray(b'6d c4 c1 09 d9 68 c2 67 ff 60 67 f8 56 85 31 84'), textout=CWbytearray(b'95 76 3b 7e 74 93 29 b1 ab 4b e7 ec 22 ba ac 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04492188,  0.390625  , ..., -0.5       ,
           -0.4375    , -0.43457031]), textin=CWbytearray(b'b0 c8 b5 86 06 7b c7 2d 02 c9 ed 1d ac bd 25 e1'), textout=CWbytearray(b'5f 94 89 7f d6 1b b4 5c 7b 9b 49 72 d4 4a 05 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04980469,  0.40625   , ..., -0.5       ,
           -0.42773438, -0.42675781]), textin=CWbytearray(b'e8 8a 5a a5 42 4b 49 22 0c be 6c e2 0a be dc 5b'), textout=CWbytearray(b'64 22 96 1e a4 dd 56 2c 73 0a 83 93 a5 d8 bd 64'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.41210938, -0.41503906]), textin=CWbytearray(b'38 98 b0 ae 29 b5 62 82 f8 98 9c 4d 85 1d 65 a3'), textout=CWbytearray(b'30 cc 0c 62 f9 e9 10 66 4b c0 a7 c0 77 44 0f 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.02929688,  0.390625  , ..., -0.5       ,
           -0.38671875, -0.40039062]), textin=CWbytearray(b'01 66 94 85 a1 ba 00 ba 63 52 a7 88 0a 5b b9 8a'), textout=CWbytearray(b'ec 40 08 ab 8e e4 8a 7d 54 3b 3a bc 24 1a 8c 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.43457031, -0.43457031]), textin=CWbytearray(b'0b 3f 22 6b 74 2d 86 d1 a7 9b 91 b9 7e cf 08 96'), textout=CWbytearray(b'6b 5f b9 b7 f6 79 65 46 24 8d e6 de 30 73 0f c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03222656,  0.39550781, ..., -0.5       ,
           -0.3828125 , -0.39257812]), textin=CWbytearray(b'3c a9 e4 53 0c 08 ea 51 64 63 b3 f0 b3 be ee d4'), textout=CWbytearray(b'ea 89 5a 44 29 02 69 95 78 64 c1 cd 17 82 62 c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.38769531, ..., -0.5       ,
           -0.40039062, -0.40820312]), textin=CWbytearray(b'67 e7 79 ff 8c 59 57 6a 98 32 e9 35 c7 17 b1 96'), textout=CWbytearray(b'4f 40 8e e9 d7 12 09 bb 9f 64 91 8c a9 c2 fc cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.04882812,  0.40429688, ..., -0.5       ,
           -0.40234375, -0.40917969]), textin=CWbytearray(b'21 75 b2 9d 70 e5 fa b0 57 49 fb a8 fa cc 95 f7'), textout=CWbytearray(b'a2 0b c0 b9 a1 64 4e 0e 98 ff ce c2 d1 27 d3 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.03710938,  0.39941406, ..., -0.5       ,
           -0.42871094, -0.42773438]), textin=CWbytearray(b'0f 1e 7f 6e 6c 89 c2 45 01 39 b3 ee f2 ef bd 7d'), textout=CWbytearray(b'06 ef a1 d3 2d 46 73 74 ff e4 3c 6a 4c 95 44 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19628906,  0.04296875,  0.40136719, ..., -0.5       ,
           -0.43554688, -0.43359375]), textin=CWbytearray(b'fc 10 e3 3b 65 6d 89 9a df 97 0f c0 f4 74 37 b5'), textout=CWbytearray(b'40 40 f5 6f e5 8d da 71 fc b7 4f f2 d5 af c7 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01855469,  0.3828125 , ..., -0.5       ,
           -0.38574219, -0.39746094]), textin=CWbytearray(b'f6 17 3c 3b 3a b9 cb 86 d4 5a 2c 20 4d 22 60 55'), textout=CWbytearray(b'5f 7c 7d 8f 16 3d 7c 25 c7 af 8f 88 e7 bd 08 c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.01953125,  0.38183594, ..., -0.5       ,
           -0.45214844, -0.44726562]), textin=CWbytearray(b'2e 4d 27 b9 10 fe a4 97 05 ce 19 11 26 de 75 7b'), textout=CWbytearray(b'f7 7e 70 43 48 7b 8d 0b 22 a0 b7 a1 a8 60 0a b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.01171875,  0.37695312, ..., -0.5       ,
           -0.43164062, -0.43164062]), textin=CWbytearray(b'd3 12 37 0b f3 bc 40 b6 2b ba 37 ed 91 b2 fc e4'), textout=CWbytearray(b'cb 42 2a 21 2b 09 1d 62 ad be b4 d6 ca d5 98 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.39453125, -0.41503906]), textin=CWbytearray(b'f2 47 b8 92 34 de 55 08 72 45 95 51 b6 19 2c f6'), textout=CWbytearray(b'b5 39 d3 c6 96 af e0 71 c9 b7 3d 12 a1 7b f9 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.046875  ,  0.40332031, ..., -0.5       ,
           -0.39746094, -0.40722656]), textin=CWbytearray(b'2b d2 b2 be 6c 38 08 39 2e 85 ac e9 90 14 13 30'), textout=CWbytearray(b'39 73 94 0d 76 1e 99 7b 39 1c 43 dd 5c 56 9f 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03515625,  0.39160156, ..., -0.5       ,
           -0.39746094, -0.40527344]), textin=CWbytearray(b'87 22 fe 8d 22 0a d8 e9 67 02 28 4d c0 56 c8 98'), textout=CWbytearray(b'3d 17 71 02 68 01 23 9e f8 b3 86 ae 96 b7 3a df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19140625,  0.0546875 ,  0.40917969, ..., -0.5       ,
           -0.45800781, -0.45117188]), textin=CWbytearray(b'ae 38 cf cf 0c 0e d1 a4 17 9e d9 9d 7f 34 9f 1d'), textout=CWbytearray(b'dc d8 52 49 f7 5f b7 96 86 ba 3e b6 5a 0b 9c b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 , -0.00097656,  0.36816406, ..., -0.5       ,
           -0.40917969, -0.4140625 ]), textin=CWbytearray(b'd6 82 53 ed 6f 2d ef 14 ef e4 b3 3d 07 6b 81 6c'), textout=CWbytearray(b'ed e3 cc 79 84 5c 4e 2f 88 44 bb 0d c2 5d 34 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.03417969,  0.38671875, ..., -0.5       ,
           -0.43359375, -0.43457031]), textin=CWbytearray(b'e6 73 08 56 30 33 2a 7d 6f a9 40 3d df ab f8 6f'), textout=CWbytearray(b'ae a3 4d 8a d0 a7 d3 4b 3d 08 46 79 fa a2 95 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04882812,  0.40429688, ..., -0.5       ,
           -0.44238281, -0.44726562]), textin=CWbytearray(b'c7 8f 9d 7c f5 6f fd 80 30 9d f7 70 6d 39 cd 71'), textout=CWbytearray(b'ef 8a 86 9e ef fa 57 71 a7 de 10 ea 3e ca 7a 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.0234375 ,  0.37402344, ..., -0.5       ,
           -0.38476562, -0.39941406]), textin=CWbytearray(b'58 f8 33 8d 4a a0 e3 ac 6b e4 95 9c 61 7e 69 ba'), textout=CWbytearray(b'a9 da 66 36 3d e2 c5 21 4d 8c 90 6b a2 bb f0 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.03417969,  0.39648438, ..., -0.5       ,
           -0.43066406, -0.43066406]), textin=CWbytearray(b'4e bd 0a a6 57 98 5a c5 68 39 0f b4 1b 28 7d 21'), textout=CWbytearray(b'92 10 86 20 ba 5c 43 1a 46 a0 8b a4 40 f4 c1 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.42382812, -0.42675781]), textin=CWbytearray(b'ee 9a 93 2a 16 74 af 39 5a 86 2b e4 f9 a5 cd 55'), textout=CWbytearray(b'e6 77 24 e9 90 7f 56 18 36 b9 e1 0b 12 85 02 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01464844,  0.37890625, ..., -0.5       ,
           -0.38183594, -0.39550781]), textin=CWbytearray(b'ac 5f 61 e9 ae bf 57 0b 61 eb 20 75 68 8a 0a 70'), textout=CWbytearray(b'8c da f2 3e a7 8a c9 e9 e6 9c 89 f9 19 25 53 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.3984375 , ..., -0.5       ,
           -0.41503906, -0.41992188]), textin=CWbytearray(b'b4 d3 f2 22 9f 4f b1 73 6a 00 1b 8d b4 b7 a0 fd'), textout=CWbytearray(b'7d 96 22 06 61 ee 40 c9 9f 26 50 cf 12 80 11 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03027344,  0.375     , ..., -0.5       ,
           -0.42675781, -0.4296875 ]), textin=CWbytearray(b'0e d6 3b 85 8a 23 96 ec a5 e8 28 80 a4 1e 63 25'), textout=CWbytearray(b'1c 1f 86 04 e1 bb 78 25 d6 03 4f 29 a0 4f 27 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05273438,  0.40917969, ..., -0.5       ,
           -0.37207031, -0.38671875]), textin=CWbytearray(b'0f 43 00 97 10 6b a5 1d 63 aa 0b 46 d3 2a 7c 0b'), textout=CWbytearray(b'de 6c 7e 28 4e a8 c8 64 d7 87 14 28 cd 98 a5 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.41210938, -0.41503906]), textin=CWbytearray(b'71 95 1f d4 f0 78 ef d4 3c bf 32 c9 e1 6c 32 b3'), textout=CWbytearray(b'20 91 ba 6e e0 3e 7d 85 4d ac d2 07 41 9a 30 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.42675781, -0.42578125]), textin=CWbytearray(b'cb fc 5a 23 8a cb 56 af d4 53 c0 6e 4f 7d 16 59'), textout=CWbytearray(b'db a7 6b ec 04 80 07 8f c9 99 ab dd 59 de e1 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.02148438,  0.38476562, ..., -0.5       ,
           -0.38769531, -0.39648438]), textin=CWbytearray(b'6f ca 79 bb a5 eb a7 7a 4a 28 a1 90 c2 74 37 5e'), textout=CWbytearray(b'01 e8 a7 46 e1 17 a2 86 f0 39 09 6b ff c9 9f 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13964844,  0.015625  ,  0.36230469, ..., -0.5       ,
           -0.39746094, -0.40625   ]), textin=CWbytearray(b'63 3e 82 18 08 f9 f1 c0 42 3c f0 88 07 bb 94 05'), textout=CWbytearray(b'55 21 c6 7c 9b 77 eb 3a c2 1e 55 d6 ce e4 19 b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.140625  ,  0.01757812,  0.38085938, ..., -0.5       ,
           -0.38085938, -0.40820312]), textin=CWbytearray(b'41 e0 f9 60 d4 c0 26 a8 7d 4f 32 e7 4c f5 21 b2'), textout=CWbytearray(b'b1 f6 b9 27 ab cb 7a 0e c5 3c 55 2a b8 11 51 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.38183594, ..., -0.5       ,
           -0.45019531, -0.44335938]), textin=CWbytearray(b'72 5e e3 0b a2 0a 32 05 22 11 9c 61 ce 26 8a 8e'), textout=CWbytearray(b'ec c2 e6 9b 60 ab fd c3 73 69 c4 5f 36 75 42 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02929688,  0.38964844, ..., -0.5       ,
           -0.39355469, -0.40332031]), textin=CWbytearray(b'ff 63 2e 4c 3a 2c c7 42 15 7b fa c9 13 db 5f 0a'), textout=CWbytearray(b'c8 38 7a dd 03 5d 9a 7f c6 d9 e7 32 f8 1f 7d de'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.20214844,  0.06054688,  0.39648438, ..., -0.5       ,
           -0.42578125, -0.42675781]), textin=CWbytearray(b'27 c9 76 bc a8 73 8d d2 a4 6a 03 b0 6f 33 f3 4d'), textout=CWbytearray(b'4a 97 05 a2 a2 a5 3e 88 9e a2 2e 01 d4 7a 6a f9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.43945312, -0.4375    ]), textin=CWbytearray(b'bb 6b 45 af d4 7d 24 39 01 46 fa e0 6a 2f 00 ad'), textout=CWbytearray(b'8a 83 98 35 77 c5 d6 41 c7 b6 0f 8c 39 be 46 cc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02734375,  0.390625  , ..., -0.5       ,
           -0.37597656, -0.40527344]), textin=CWbytearray(b'59 d8 9b 99 15 54 69 7b d1 e5 64 7d e9 27 80 93'), textout=CWbytearray(b'66 5f 1e 48 ba de b0 09 4b 33 02 50 30 ce 18 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.00488281,  0.37109375, ..., -0.5       ,
           -0.38769531, -0.3984375 ]), textin=CWbytearray(b'dd 6c f8 54 40 62 b4 7a 83 b1 d6 4d bc a2 48 2e'), textout=CWbytearray(b'69 be 7c 9d de 43 0d 0b 10 c7 e6 90 16 d0 cd 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.046875  ,  0.39355469, ..., -0.5       ,
           -0.42578125, -0.4296875 ]), textin=CWbytearray(b'f4 8d 07 b0 1f 59 b1 76 fa 85 9b 7a c5 7c 47 0d'), textout=CWbytearray(b'25 b8 fd ce ca e7 70 e2 ae 9f f9 c8 ed 19 8a c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.04199219,  0.39550781, ..., -0.5       ,
           -0.44726562, -0.44335938]), textin=CWbytearray(b'bf ce 91 7f 19 54 29 32 85 00 6b ad 4a 2c ea 77'), textout=CWbytearray(b'd9 74 47 e8 80 4e 68 00 3b 40 97 2a e7 c3 6e 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02832031,  0.390625  , ..., -0.5       ,
           -0.40625   , -0.41308594]), textin=CWbytearray(b'6b b2 2c 23 2f f8 4c fa c9 e7 1b 69 b5 fd fc 56'), textout=CWbytearray(b'23 8b 99 d3 2c 06 07 fd 14 b3 9e f9 c0 cc 03 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03125   ,  0.37402344, ..., -0.5       ,
           -0.45214844, -0.44921875]), textin=CWbytearray(b'e7 b0 bb 98 69 31 42 ff 2e f8 75 f9 58 3c ad 0b'), textout=CWbytearray(b'91 18 0a c2 1a 09 c2 da a2 ee c0 12 a5 85 d4 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.40820312, ..., -0.5       ,
           -0.41796875, -0.421875  ]), textin=CWbytearray(b'33 cb cf db f0 df 27 6b 83 80 ab d3 c8 31 e0 f3'), textout=CWbytearray(b'9f cf 1d f4 64 d3 9d 68 0f cd 47 c4 a3 5f 82 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.02734375,  0.375     , ..., -0.5       ,
           -0.44628906, -0.44628906]), textin=CWbytearray(b'05 13 1f 37 2c ae e5 16 53 a5 9c cc 02 59 81 bd'), textout=CWbytearray(b'95 56 e7 19 d3 0d f2 fa e9 94 de 8e 84 9d ba e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03613281,  0.38085938, ..., -0.5       ,
           -0.43261719, -0.42871094]), textin=CWbytearray(b'f3 80 ca aa bb ae a7 dd 10 ec c8 ad 0d 7c dd 41'), textout=CWbytearray(b'be 03 8d 08 78 72 b9 f1 ca a3 81 47 03 58 6d d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03027344,  0.37792969, ..., -0.5       ,
           -0.43847656, -0.4375    ]), textin=CWbytearray(b'bf 4c e8 f9 22 df a5 d5 2f 70 7e 3f ae c0 0d 41'), textout=CWbytearray(b'b4 3f 83 42 0a bf 58 9c 87 44 70 f0 3e 2c b9 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03027344,  0.39257812, ..., -0.5       ,
           -0.42285156, -0.44140625]), textin=CWbytearray(b'0d 1f 95 95 20 b5 b6 5e a0 0b f6 97 24 89 c9 e7'), textout=CWbytearray(b'2f 9b 26 46 6f 1e b8 12 8a f4 e3 e4 3a 7a cf b3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.03125   ,  0.390625  , ..., -0.5       ,
           -0.41699219, -0.41894531]), textin=CWbytearray(b'2d 90 62 57 16 09 fd ed 40 cf 67 c8 38 e8 8c a2'), textout=CWbytearray(b'86 71 8d eb fa 09 4d b1 23 4c cc 6c 3b af 61 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01855469,  0.36816406, ..., -0.5       ,
           -0.390625  , -0.40039062]), textin=CWbytearray(b'83 ca 76 e3 47 b7 9d 6a 46 42 e2 39 a1 e5 2d 57'), textout=CWbytearray(b'00 6d 39 ff 47 c5 e6 bf 9e 10 c6 2b 89 7c 9d 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.203125  ,  0.06347656,  0.40625   , ..., -0.5       ,
           -0.41308594, -0.41503906]), textin=CWbytearray(b'16 35 7b 53 54 61 b2 74 ff e5 96 e3 dc 55 d0 ce'), textout=CWbytearray(b'55 12 fa 0b 7e 8c 15 47 fa 1a a5 f4 be 6b b6 e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03222656,  0.39160156, ..., -0.5       ,
           -0.40625   , -0.41210938]), textin=CWbytearray(b'a7 15 b7 ac 0b 4f 40 71 f3 79 78 ea d2 d7 4e 97'), textout=CWbytearray(b'db 2b 2a 5f 1b d6 ba e3 2b 90 b6 db d7 64 1e 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.37792969, ..., -0.5       ,
           -0.39550781, -0.40429688]), textin=CWbytearray(b'e9 42 7a 34 b1 95 9e ad f8 fc 15 a7 bd 1a 30 59'), textout=CWbytearray(b'88 0d 94 bf f8 8a 03 e1 c5 1e 8e 9e 6b a0 31 d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03027344,  0.39257812, ..., -0.5       ,
           -0.42285156, -0.42578125]), textin=CWbytearray(b'91 64 d4 fe 66 bd 04 72 fe fd 02 6a 53 ce 09 d7'), textout=CWbytearray(b'c7 94 52 17 55 62 21 c6 10 39 5b 66 19 96 97 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    




.. parsed-literal::

    WARNING:root:Trigger not found in ADC data. No data reported!
    c:\users\user\code\term3\chipwhisperer\software\chipwhisperer\__init__.py:288: UserWarning: Timeout happened during capture
      warnings.warn("Timeout happened during capture")
    




.. parsed-literal::

    Trace(wave=array([ 0.19335938,  0.04589844,  0.39550781, ..., -0.5       ,
           -0.44726562, -0.44433594]), textin=CWbytearray(b'71 65 b2 e5 80 9e 13 2a 0a 65 69 cf db 28 dc fe'), textout=CWbytearray(b'54 d7 44 41 54 5a ba cf 60 04 99 c1 0d ba d3 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38671875, ..., -0.5       ,
           -0.39941406, -0.41992188]), textin=CWbytearray(b'2f c0 9e c7 7c 2e 4a 82 a6 3f 61 52 e6 0c 05 4f'), textout=CWbytearray(b'79 f6 c9 7b a9 2c 7f 8d 8e b7 cf 76 11 3f f5 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04003906,  0.40136719, ..., -0.5       ,
           -0.44628906, -0.44238281]), textin=CWbytearray(b'1d fe 80 f1 0e d6 b0 ef 31 a3 bf ce 48 46 6d 65'), textout=CWbytearray(b'5c 23 a8 f7 a3 e0 5c 06 6d 64 a1 97 9d 15 6e d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.38574219, -0.39550781]), textin=CWbytearray(b'c2 2d bf 76 4d df 51 d4 8f e8 25 62 d4 d5 4e 5c'), textout=CWbytearray(b'50 8e ae 32 a7 76 18 1c d4 fa c1 4b 5e 79 32 c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.01074219,  0.37695312, ..., -0.5       ,
           -0.48144531, -0.47070312]), textin=CWbytearray(b'a8 7f 39 d1 72 d5 71 58 30 a9 59 7d 45 51 6b e7'), textout=CWbytearray(b'40 46 3c bf fc f7 4e 75 10 21 d6 68 94 c1 21 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.04101562,  0.39746094, ..., -0.5       ,
           -0.45703125, -0.46386719]), textin=CWbytearray(b'2f a1 d9 cc ba e5 67 73 33 b6 0a 0d cd 65 e2 6a'), textout=CWbytearray(b'b6 cb ed 0e c9 a3 ad 60 2d 9e ee 02 bb 76 fe f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.01171875,  0.37792969, ..., -0.5       ,
           -0.41699219, -0.42578125]), textin=CWbytearray(b'8b 4b 64 ac dc 8a 47 ba 6c d9 c3 ed 29 0b 12 43'), textout=CWbytearray(b'b4 c5 5a 21 e2 06 f0 05 43 d1 1f 5b 84 bc 44 81'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03515625,  0.39355469, ..., -0.5       ,
           -0.40527344, -0.41210938]), textin=CWbytearray(b'cb d5 99 31 6a 7a bc a5 7e ee bc 45 53 fb 5d eb'), textout=CWbytearray(b'fc dc 82 27 be 0c 7b 65 ce 0e 14 0b bb 0d 23 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.37988281, ..., -0.5       ,
           -0.421875  , -0.42480469]), textin=CWbytearray(b'88 76 1c 41 3d dc 59 46 7e 82 65 62 1e a4 24 fd'), textout=CWbytearray(b'aa ab dc 4b d5 f3 19 b9 e0 b0 8f 01 84 81 3e 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04199219,  0.3984375 , ..., -0.5       ,
           -0.4296875 , -0.42675781]), textin=CWbytearray(b'73 f2 01 69 0c a2 b5 b0 bb 74 39 84 1c db da 94'), textout=CWbytearray(b'80 a7 f3 87 9c 50 eb 81 fb c4 dd c7 63 05 87 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.02148438,  0.38378906, ..., -0.5       ,
           -0.41015625, -0.4140625 ]), textin=CWbytearray(b'e4 ad 0a 00 ad 7d ae 0c 41 5c b7 84 29 c4 d6 03'), textout=CWbytearray(b'e1 fb b8 2c 88 1e 69 91 93 23 66 56 a2 e5 17 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03125   ,  0.37792969, ..., -0.5       ,
           -0.39746094, -0.40527344]), textin=CWbytearray(b'28 08 12 2b ce 8f 2a 5e ce f3 09 5a 56 44 a0 b0'), textout=CWbytearray(b'18 23 55 63 a6 0f 9f 3b 64 c9 da 33 39 47 02 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01855469,  0.38085938, ..., -0.5       ,
           -0.37890625, -0.40527344]), textin=CWbytearray(b'32 3c 20 7a c2 12 9e 2f 93 e3 3d bd 09 f9 99 a3'), textout=CWbytearray(b'8d 99 49 dd 6f 2f 1a 05 3f cb 8d 3b 21 8c 61 e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.40527344, ..., -0.5       ,
           -0.41503906, -0.41992188]), textin=CWbytearray(b'7f e3 c3 f1 db ac c2 4e 13 ea 9f 9c 00 47 d3 d1'), textout=CWbytearray(b'b7 c7 e8 ed f5 9b 8a 80 ed 27 49 71 05 e9 95 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.39941406, ..., -0.5       ,
           -0.38085938, -0.39257812]), textin=CWbytearray(b'05 12 59 37 30 6a 44 ac 06 cb f8 e3 5d 8b a8 db'), textout=CWbytearray(b'91 d0 55 5d 72 fe 1b cc be a2 93 1e b8 dc 43 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04589844,  0.40429688, ..., -0.5       ,
           -0.43945312, -0.43457031]), textin=CWbytearray(b'41 0a a1 88 cc 27 32 ba 64 3f 9d c4 9e c0 15 41'), textout=CWbytearray(b'7f 66 8c 7f 10 67 ba 46 5c 25 4c 58 92 51 dd ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03027344,  0.39160156, ..., -0.5       ,
           -0.42675781, -0.42578125]), textin=CWbytearray(b'13 15 68 87 f8 34 e0 35 b0 4c 0c 02 28 3b 06 0b'), textout=CWbytearray(b'3b f7 f8 4b 55 6d e6 ab d9 f2 25 50 45 99 d5 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04589844,  0.40039062, ..., -0.5       ,
           -0.39453125, -0.41894531]), textin=CWbytearray(b'6d ba d0 0e bf 31 0f 42 1e a0 41 12 08 0e 86 91'), textout=CWbytearray(b'7a b8 e0 6e 2d 8e 51 ab 73 28 27 55 ed 70 d0 d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.02539062,  0.38769531, ..., -0.5       ,
           -0.38476562, -0.39648438]), textin=CWbytearray(b'71 f5 d1 8b d6 9e a5 e6 62 02 a5 6d e8 1f 99 c9'), textout=CWbytearray(b'c6 44 21 c2 fb 93 ab 5c fd 41 b9 c7 fd ee 5d 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02636719,  0.375     , ..., -0.5       ,
           -0.40039062, -0.40722656]), textin=CWbytearray(b'09 1e a4 80 c2 81 ce ec 9c b4 1c df 2e 23 19 47'), textout=CWbytearray(b'7f a7 06 a7 15 31 5f 69 1d 47 60 05 16 83 21 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.37792969, -0.40527344]), textin=CWbytearray(b'62 da fa 76 13 c3 39 b2 00 b2 04 1b 9a 73 f6 0f'), textout=CWbytearray(b'e5 b8 7b 80 6c 94 25 a1 be 69 06 90 6c ed ad 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.046875  ,  0.40527344, ..., -0.5       ,
           -0.41210938, -0.43457031]), textin=CWbytearray(b'b8 6d f3 3e 5e 10 85 b5 d5 85 cb 13 1c da f9 d6'), textout=CWbytearray(b'2a 9d 37 c7 a5 aa aa 1a 0e 9d 16 3f 09 09 c3 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02246094,  0.38769531, ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'17 f9 1b fc 4e 28 6b 8c 23 11 23 fa eb 0a 4d 93'), textout=CWbytearray(b'6b 80 10 52 8f 26 84 8b 0d 40 00 e7 bd 12 4f a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.03808594,  0.39550781, ..., -0.5       ,
           -0.41699219, -0.41894531]), textin=CWbytearray(b'5d 48 74 1b d6 63 5f 4f 76 ed 49 d6 34 e9 f4 10'), textout=CWbytearray(b'43 86 ef 09 77 95 61 4e f6 31 bb ba 58 a8 67 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03417969,  0.38085938, ..., -0.5       ,
           -0.41894531, -0.42285156]), textin=CWbytearray(b'41 66 26 c6 8b ac 2a 5a 26 6a 3f 92 c8 dc 35 34'), textout=CWbytearray(b'e6 79 e8 6e d7 e3 03 e7 ec 5c cb 64 dd 92 b5 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02246094,  0.38769531, ..., -0.5       ,
           -0.37988281, -0.39355469]), textin=CWbytearray(b'4b de b3 80 f9 f0 fa c5 e6 13 16 ad ae f7 ed 8e'), textout=CWbytearray(b'60 9e bc 0d a1 20 07 61 14 61 c4 13 fb 74 0e 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04101562,  0.38085938, ..., -0.5       ,
           -0.40820312, -0.41210938]), textin=CWbytearray(b'4c fc 13 90 8c 31 9a 3e 18 3f e5 79 d6 69 be 79'), textout=CWbytearray(b'93 72 d3 59 aa 06 ee 98 94 61 17 94 d6 4a 32 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05371094,  0.40722656, ..., -0.5       ,
           -0.44628906, -0.43847656]), textin=CWbytearray(b'fa ae f5 70 1d e3 44 51 00 88 60 7f 03 2d a3 23'), textout=CWbytearray(b'55 b6 24 04 b9 5b e5 5c 80 f1 54 c8 c0 ca 97 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.046875  ,  0.40136719, ..., -0.5       ,
           -0.4140625 , -0.41992188]), textin=CWbytearray(b'1c 6c 92 f5 ef 5d 24 d1 4c 5b 62 a0 3e cf 61 40'), textout=CWbytearray(b'1f 43 af a5 f2 93 a1 77 f9 f3 99 3e 3e a4 28 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.42285156, -0.42773438]), textin=CWbytearray(b'2f c2 99 88 5e fe 40 1b 69 c2 58 b8 da 90 db bf'), textout=CWbytearray(b'24 a2 2d 3f 41 24 f0 54 a3 85 0f 7e 66 04 63 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.015625  ,  0.38085938, ..., -0.5       ,
           -0.40332031, -0.41210938]), textin=CWbytearray(b'31 69 7c ad 5a 9e 06 9e 1d 36 24 e0 a1 65 d8 cb'), textout=CWbytearray(b'61 9c 7a 30 95 68 a3 f5 65 70 5a 0b b0 87 53 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.38476562, ..., -0.5       ,
           -0.41015625, -0.41601562]), textin=CWbytearray(b'10 01 90 1e 80 73 d3 52 e8 43 94 9e 6e e6 c6 f3'), textout=CWbytearray(b'd1 90 0d 2f b1 44 44 3c 56 87 d2 4a fd e1 e4 a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.05273438,  0.39746094, ..., -0.5       ,
           -0.44238281, -0.44042969]), textin=CWbytearray(b'3b f1 19 ca 86 0e e6 5e bd 1d 47 97 3b 54 bf 43'), textout=CWbytearray(b'aa ad 16 6f c0 f7 69 5c 19 d9 f0 c6 0e 5b a1 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.41503906, -0.42089844]), textin=CWbytearray(b'7b 37 bd 27 8e a4 1a bd cb 3f bd 52 5d ac 1a 45'), textout=CWbytearray(b'9c 69 1c 04 13 c0 9b 0e 0f 73 2c e1 e0 b8 0c f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04101562,  0.40136719, ..., -0.5       ,
           -0.36035156, -0.37988281]), textin=CWbytearray(b'd3 44 06 d6 fc 94 c3 29 89 15 a6 73 dc df 76 05'), textout=CWbytearray(b'90 eb c6 2f 7d 93 d1 d1 63 dd a2 77 c6 8a 52 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04785156,  0.40625   , ..., -0.5       ,
           -0.41308594, -0.41796875]), textin=CWbytearray(b'4d e6 5f df eb f8 d3 fb f7 d9 0c cb a4 54 db 18'), textout=CWbytearray(b'43 0a d7 5c 18 67 a8 aa c1 81 49 bf 7a 29 3b 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19628906,  0.05859375,  0.41210938, ..., -0.5       ,
           -0.38964844, -0.41113281]), textin=CWbytearray(b'ae d3 18 75 fc 35 fb 96 6e 4a 42 b3 63 21 1a c1'), textout=CWbytearray(b'21 2f 83 6d 19 e4 e1 46 76 27 4e c8 f3 c2 72 ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.39160156, ..., -0.5       ,
           -0.37597656, -0.38867188]), textin=CWbytearray(b'3b fe c3 22 12 1d 1c 60 11 40 e5 4b c2 35 82 f8'), textout=CWbytearray(b'56 c2 1b 8d 48 ee 00 22 47 97 17 88 f4 d5 92 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 ,  0.00292969,  0.36914062, ..., -0.5       ,
           -0.40527344, -0.41113281]), textin=CWbytearray(b'a7 c1 61 8a 50 f0 ba ef 2a 3f 5a 21 57 96 0d 69'), textout=CWbytearray(b'e9 f7 8b 93 cb 2f 95 df 5d 90 f8 51 ed ae 03 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.38964844, -0.40039062]), textin=CWbytearray(b'4b 18 23 68 20 c9 2b 8a c6 ac bc 17 d0 9a 5b 01'), textout=CWbytearray(b'bb 87 6b 81 d8 49 fe ce fe 8c 08 9a 6a 61 e9 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.40136719, -0.40820312]), textin=CWbytearray(b'ca 5b ab d6 c6 35 75 b2 fc 68 d4 9f 20 a8 91 02'), textout=CWbytearray(b'69 ce 0f f0 20 dd 2a 34 4a fc c3 2e 37 5f 0d 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.10644531, -0.00976562,  0.36132812, ..., -0.5       ,
           -0.39453125, -0.41894531]), textin=CWbytearray(b'7a 10 76 4a 56 c4 87 e5 24 f7 67 a0 25 d3 0c 2a'), textout=CWbytearray(b'2b 55 b7 63 25 b5 4d 44 39 df 4e be cb 9a d2 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.01953125,  0.38378906, ..., -0.5       ,
           -0.39160156, -0.40039062]), textin=CWbytearray(b'93 bb a4 99 c2 21 ef 5d db 01 67 c3 25 2a 01 20'), textout=CWbytearray(b'ef 2e fe 6a 67 df 38 44 27 47 e2 fb ce 5c 16 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04785156,  0.40332031, ..., -0.5       ,
           -0.42089844, -0.42382812]), textin=CWbytearray(b'b6 43 fc 07 38 e0 6f e0 7c 98 5e b6 9e 26 10 3a'), textout=CWbytearray(b'53 02 4e 4c 0c f0 3d 7a 81 12 52 6f 80 92 07 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03613281,  0.39550781, ..., -0.5       ,
           -0.40136719, -0.42382812]), textin=CWbytearray(b'8e f6 21 be 6d c6 91 0e b8 43 e6 ea b4 84 4f 25'), textout=CWbytearray(b'bf f5 66 ba 53 4a 3e a6 36 1d 4c bb 37 55 1c 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.00390625,  0.37402344, ..., -0.5       ,
           -0.42675781, -0.42871094]), textin=CWbytearray(b'0b 3b fc 75 fe 17 a3 fc 47 43 30 dc 7f 8f df d1'), textout=CWbytearray(b'ff 41 91 ee e6 e3 9f 2c 69 51 e6 af 45 11 e4 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40722656, ..., -0.5       ,
           -0.40820312, -0.41503906]), textin=CWbytearray(b'4e a3 cb 07 0c 28 c3 7d 64 76 99 dc fd 6b e8 c7'), textout=CWbytearray(b'46 56 e9 cd 8d c8 7a 6e e8 98 6f fb 37 9f ad 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04003906,  0.38183594, ..., -0.5       ,
           -0.37109375, -0.38378906]), textin=CWbytearray(b'8b 7e e3 14 be 57 5d 13 a7 5d f0 d2 03 64 86 f5'), textout=CWbytearray(b'be f0 65 d1 e9 e4 6c 5e 1d c8 a6 48 36 09 51 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04003906,  0.39941406, ..., -0.5       ,
           -0.43164062, -0.43164062]), textin=CWbytearray(b'b2 a1 ef a5 a1 5b 4c f3 33 79 bc c0 c3 50 4b e2'), textout=CWbytearray(b'f1 cf 44 82 04 07 90 f5 7c 41 1a b5 e9 b0 75 29'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04199219,  0.40039062, ..., -0.5       ,
           -0.44238281, -0.43945312]), textin=CWbytearray(b'db a8 70 2a db be 32 4d db 2b 86 c4 2f b2 e9 95'), textout=CWbytearray(b'4e 8f a4 3e 88 0a 11 12 3c e1 9d 19 ab fa 9b b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.00976562,  0.375     , ..., -0.5       ,
           -0.40917969, -0.4140625 ]), textin=CWbytearray(b'e2 2e 09 8a c2 04 7a 96 89 af 58 5e 53 02 bd e3'), textout=CWbytearray(b'e8 6e 5a 5e 45 bf 89 e1 8d 6e 5b d7 9c 54 0e d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04882812,  0.40429688, ..., -0.5       ,
           -0.39550781, -0.40332031]), textin=CWbytearray(b'0e a9 3e 80 57 5a bf f5 01 52 d5 50 d9 74 ed 0a'), textout=CWbytearray(b'a4 03 e6 28 a3 ec 91 e3 d6 21 02 0c 4b eb 8e 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38574219, ..., -0.5       ,
           -0.41308594, -0.41796875]), textin=CWbytearray(b'b9 a1 53 06 e9 b0 74 8c 54 1f 64 21 28 46 82 be'), textout=CWbytearray(b'9c 2c c9 e6 0a 74 75 a5 5d 43 98 25 34 c9 9d b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.0078125 ,  0.359375  , ..., -0.5       ,
           -0.40039062, -0.40625   ]), textin=CWbytearray(b'56 1b 6d c2 21 70 15 0b e1 f3 ff f3 c1 66 bd d3'), textout=CWbytearray(b'e7 ec 18 51 bf 27 42 c8 12 a1 b4 12 03 e3 66 81'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.02050781,  0.38183594, ..., -0.5       ,
           -0.37597656, -0.38867188]), textin=CWbytearray(b'53 94 99 fd c4 fb 12 30 a5 25 c6 9d 97 cf 86 fc'), textout=CWbytearray(b'6a 6c 4a 6d 49 3f 0f 46 1b 58 76 f3 d1 21 15 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13378906,  0.01269531,  0.375     , ..., -0.5       ,
           -0.38671875, -0.39550781]), textin=CWbytearray(b'f6 c1 bd d9 a9 ac ea 04 ad 2a 5f c9 bf 5f 8e f0'), textout=CWbytearray(b'88 74 e2 ed 89 fa d5 3d c5 15 02 b4 10 3d 1e e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.39941406, -0.40722656]), textin=CWbytearray(b'26 15 92 76 2a cd e1 c4 7d 7c 11 c3 80 fb 62 23'), textout=CWbytearray(b'68 b4 62 14 23 00 a5 79 77 73 b9 fd ae 34 ea ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.35839844, -0.37695312]), textin=CWbytearray(b'e0 57 94 6f 5b 81 19 06 46 c9 25 d1 8a b1 a0 53'), textout=CWbytearray(b'00 7c 83 5c 3a e5 d6 39 a5 b5 33 07 0a 71 c1 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.38574219, -0.40332031]), textin=CWbytearray(b'2c 33 da 78 f9 11 7f 51 19 2e 80 47 b1 fe fe 0d'), textout=CWbytearray(b'5b d6 25 60 57 6e 26 13 a6 75 65 19 ba 9d 6d 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.02734375,  0.38964844, ..., -0.5       ,
           -0.42285156, -0.42675781]), textin=CWbytearray(b'49 98 08 82 16 33 c4 36 e4 4e 49 ca a3 7b b5 50'), textout=CWbytearray(b'24 3e cb 0a 72 22 03 00 b3 12 2e f9 b9 d2 ba 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.015625  ,  0.37988281, ..., -0.5       ,
           -0.38867188, -0.39941406]), textin=CWbytearray(b'17 94 5d d1 3e 09 5a fa c5 c9 3f f3 40 f2 f5 2c'), textout=CWbytearray(b'3f 42 73 22 f3 24 a0 0c cf 88 9f 7d 45 d1 92 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.03710938,  0.39648438, ..., -0.5       ,
           -0.4140625 , -0.41699219]), textin=CWbytearray(b'cd 70 a9 17 98 63 18 75 78 07 74 75 bb fa 99 df'), textout=CWbytearray(b'11 82 60 4d c5 5a 34 11 e4 ba e5 c8 50 a2 67 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.39648438, -0.40332031]), textin=CWbytearray(b'7d d5 d3 19 87 5c b3 12 e7 3f d2 77 f0 f6 d3 ba'), textout=CWbytearray(b'0d a6 70 09 e3 0d 78 21 b0 fd ef 56 77 52 f1 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02832031,  0.38964844, ..., -0.5       ,
           -0.39746094, -0.40625   ]), textin=CWbytearray(b'bb 8f 0f 77 a5 69 55 c2 53 2e ab ba 51 23 bd f2'), textout=CWbytearray(b'c0 5e a6 0b bf 51 ec 0a fd ad e3 af ac 39 d7 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01757812,  0.38085938, ..., -0.5       ,
           -0.4296875 , -0.43945312]), textin=CWbytearray(b'39 0a c9 d8 e4 26 6a 40 08 da 78 d1 24 f9 4b 47'), textout=CWbytearray(b'3a 71 f1 10 a5 c6 ac 6b d8 3d 85 0e 40 a9 e5 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.41503906, -0.41796875]), textin=CWbytearray(b'76 d0 c7 5a 9e 11 36 68 16 16 29 dc d5 89 ec 18'), textout=CWbytearray(b'00 07 70 9b 52 90 2f 05 00 4d a5 f8 0f 41 e3 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40136719, -0.40625   ]), textin=CWbytearray(b'80 51 8c 26 89 14 0a 4f b3 e9 9f 08 31 ad 86 69'), textout=CWbytearray(b'0d f4 9f 93 1b 09 df 47 cd a3 d7 aa 51 e0 2c a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.39746094, -0.40722656]), textin=CWbytearray(b'90 bb 1c 35 16 9d 85 60 db 9e 7e 37 59 b3 a3 99'), textout=CWbytearray(b'a4 8b bb 0d b2 a9 93 c6 9f 65 29 e0 d0 2a 77 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04882812,  0.40234375, ..., -0.5       ,
           -0.41601562, -0.41992188]), textin=CWbytearray(b'd1 fe d2 c6 52 88 f5 2c c5 73 b6 d0 84 20 ca 84'), textout=CWbytearray(b'63 9a e5 42 63 77 5c cb f8 7a cc ac 6c 0c b3 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04492188,  0.40039062, ..., -0.5       ,
           -0.390625  , -0.41308594]), textin=CWbytearray(b'fa 43 08 5b dd 4f 5b d4 08 4a bc 14 08 12 50 12'), textout=CWbytearray(b'a9 a7 0f c3 78 9f ae 37 68 54 ed 15 0d 08 50 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02441406,  0.38671875, ..., -0.5       ,
           -0.40429688, -0.42578125]), textin=CWbytearray(b'31 fd 4d 63 89 a5 90 b0 82 04 9b 77 33 96 26 20'), textout=CWbytearray(b'23 0c bf 86 40 11 08 3c 2c 2e 7c f3 1b 36 45 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.39550781, -0.40527344]), textin=CWbytearray(b'06 fc ef 48 0a 23 c7 cc e6 74 da 1e 39 a5 a2 c2'), textout=CWbytearray(b'be 92 2e c2 8c b8 8f ce fd c0 bb 89 84 d0 79 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188,  0.01171875,  0.37792969, ..., -0.5       ,
           -0.45019531, -0.45019531]), textin=CWbytearray(b'12 4e 25 a7 53 03 be 4e 3a d4 4d 4d a5 a8 7d 36'), textout=CWbytearray(b'02 03 f5 99 ec 4c 21 15 3c c7 b7 b5 1a 9d 9a 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04199219,  0.40039062, ..., -0.5       ,
           -0.43847656, -0.43847656]), textin=CWbytearray(b'ff 2c fb a0 b8 6b 1d f4 f1 9c 4b 80 e6 55 a2 6b'), textout=CWbytearray(b'd3 ee 23 ac 2a ea 43 0c 80 7f b8 40 2b 19 03 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04003906,  0.38574219, ..., -0.5       ,
           -0.41796875, -0.421875  ]), textin=CWbytearray(b'6e 43 47 61 45 2e a2 92 bf 57 86 0b 83 bc 5c 14'), textout=CWbytearray(b'de 6c e0 5d 18 f7 51 18 35 c7 9e 63 15 15 73 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04199219,  0.38867188, ..., -0.5       ,
           -0.41113281, -0.41601562]), textin=CWbytearray(b'2a f1 77 0b 7b a5 03 f8 59 02 ec 8f 28 c1 5a 60'), textout=CWbytearray(b'b5 21 89 ad 10 c3 e4 6d 39 33 2c bb 96 c7 b9 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.36132812, -0.37988281]), textin=CWbytearray(b'0e 06 74 7e d9 6c 5f 7c fb 57 01 36 7b a9 af d4'), textout=CWbytearray(b'4c 85 d2 8a f0 07 2e 30 e0 83 48 1d 2e 6d 6b 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05566406,  0.39550781, ..., -0.5       ,
           -0.41503906, -0.41992188]), textin=CWbytearray(b'c2 1e 16 36 91 ae 76 62 65 c4 61 7b 33 bf ff ca'), textout=CWbytearray(b'ab 1e 08 1a 11 9f 8d e6 71 ab 3f 5a e4 f5 eb 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40039062, ..., -0.5       ,
           -0.45605469, -0.44921875]), textin=CWbytearray(b'c9 8d 73 27 14 83 46 c8 b3 df 2a ab e1 ac c3 e1'), textout=CWbytearray(b'e7 38 f9 bb 8e 74 99 70 32 9e 0b eb a0 26 4f 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19433594,  0.05566406,  0.41210938, ..., -0.5       ,
           -0.42578125, -0.42675781]), textin=CWbytearray(b'f7 d8 69 df 24 8a 67 e3 0e 93 eb ad 9f 4d f9 3c'), textout=CWbytearray(b'02 32 30 ba 90 30 f0 8c a7 2b dd dd 71 01 c9 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.42675781, -0.42578125]), textin=CWbytearray(b'1f 44 84 37 62 84 2f 5f e9 31 fb 0d 7d 9a 17 ca'), textout=CWbytearray(b'6b 1e 27 71 89 8c ef 80 3c a1 ff e6 8a 59 d2 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04199219,  0.38574219, ..., -0.5       ,
           -0.41503906, -0.42089844]), textin=CWbytearray(b'ca 57 f8 ad 5b 40 cd 19 04 99 f0 38 28 9e b5 4a'), textout=CWbytearray(b'69 ad ce 70 1f 52 59 a7 4e 2a e0 39 f7 aa 84 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.00976562,  0.37011719, ..., -0.5       ,
           -0.46582031, -0.4609375 ]), textin=CWbytearray(b'82 24 3e 99 b7 39 dd 52 5a 1f 0e 9c 4c 70 6a 56'), textout=CWbytearray(b'80 37 90 98 13 0d 84 fc d0 fd 9d d5 a8 50 65 1d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.43457031, -0.43164062]), textin=CWbytearray(b'30 0d fa c4 7b 01 9f 1b 61 a0 7c 85 6c 2f bc fd'), textout=CWbytearray(b'9d cd 2f 47 2c 6b 91 04 19 52 61 c8 83 58 03 12'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.0234375 ,  0.38476562, ..., -0.5       ,
           -0.39160156, -0.40234375]), textin=CWbytearray(b'2e 0e 8d 55 63 df 0b 58 3f 15 0d 56 0f db 17 26'), textout=CWbytearray(b'12 7b 8a 4d 3a 64 c3 77 82 86 f4 d4 bc 66 93 b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.0234375 ,  0.37109375, ..., -0.5       ,
           -0.40136719, -0.40820312]), textin=CWbytearray(b'57 f3 c7 b9 74 25 bf a9 74 e4 77 ba 21 d8 69 2b'), textout=CWbytearray(b'de d3 17 57 bd 17 91 24 ca bf 2e ed cb 83 fd 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.015625  ,  0.37890625, ..., -0.5       ,
           -0.39941406, -0.40917969]), textin=CWbytearray(b'9e d4 96 2b 72 41 bb 3b 48 2a a8 06 9d 24 dd 36'), textout=CWbytearray(b'5c 47 72 b2 19 99 6e 58 28 e4 92 fe bc 86 b1 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03515625,  0.37988281, ..., -0.5       ,
           -0.44628906, -0.44238281]), textin=CWbytearray(b'93 88 69 88 7e 58 a7 fe 80 ca 02 b0 27 87 c4 00'), textout=CWbytearray(b'db 0b 9d 57 e1 9e 08 9d 08 35 df 78 cb 0d 30 8f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.38867188, -0.3984375 ]), textin=CWbytearray(b'a6 44 4e 22 8e 47 99 84 ee 6d 91 55 e8 2f 95 a5'), textout=CWbytearray(b'5b e9 39 a0 d7 fd c8 8a 13 98 05 09 35 47 04 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02246094,  0.38476562, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'ab 5b bc 4d e8 0e 4a ce 12 73 af f9 db 57 5d 67'), textout=CWbytearray(b'61 71 f3 2e 2a 88 05 ec 3d 82 2b 9b 14 42 99 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.41894531, -0.43359375]), textin=CWbytearray(b'f0 e9 8f 37 be 7b 26 d9 31 a2 73 16 5c 7d a9 e1'), textout=CWbytearray(b'47 59 29 7f 70 4d a9 94 5b ce 38 e8 dc ef fc f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05371094,  0.41015625, ..., -0.5       ,
           -0.43359375, -0.45117188]), textin=CWbytearray(b'fa 28 9e 7d ce ea 49 0d 19 f1 c3 ab 13 16 23 ed'), textout=CWbytearray(b'ae 1d 24 65 43 8b 73 63 f8 18 3b ed 23 b5 73 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03613281,  0.39257812, ..., -0.5       ,
           -0.38671875, -0.40917969]), textin=CWbytearray(b'37 18 96 a2 ee 9c 3e 4f 7c 45 a4 f1 79 16 42 f3'), textout=CWbytearray(b'52 ce 9a 9e af 1c de 33 f4 91 85 6e 96 00 91 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.40527344, ..., -0.5       ,
           -0.40820312, -0.41894531]), textin=CWbytearray(b'b4 db d3 4c b2 c5 3a a8 7b a8 0b f7 7d 8e 38 79'), textout=CWbytearray(b'58 a0 99 25 89 50 46 84 1e 5a 05 be d7 48 5e d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04492188,  0.40234375, ..., -0.5       ,
           -0.38476562, -0.41113281]), textin=CWbytearray(b'83 ea bb ac 39 36 ec 21 7c e2 78 95 ae 35 04 f7'), textout=CWbytearray(b'82 01 86 25 93 ad cf ea 34 22 7e b4 c3 a1 5a 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.44238281, -0.45605469]), textin=CWbytearray(b'b5 1c 59 22 45 d1 5e 68 a8 4d 75 15 04 09 83 5f'), textout=CWbytearray(b'26 ff f1 2e b6 fe 09 94 48 92 ea 2c 51 8e a3 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.39257812, ..., -0.5       ,
           -0.41503906, -0.41992188]), textin=CWbytearray(b'53 f6 f3 f8 ad 8c 0b 62 78 8a 92 c7 9e fd ad 51'), textout=CWbytearray(b'09 b6 93 48 cd 53 07 a1 4f 41 1b 3a bf 83 e1 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04785156,  0.39453125, ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'80 a4 ba 40 69 82 98 f1 fc 13 4b 73 63 5c 96 6c'), textout=CWbytearray(b'dc 2c 3f 00 42 3c 17 6f e3 0f 86 13 2f 00 86 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.03808594,  0.39453125, ..., -0.5       ,
           -0.42382812, -0.42285156]), textin=CWbytearray(b'66 8a 2a 40 25 6e 07 51 84 a4 31 de c5 16 bb 32'), textout=CWbytearray(b'07 4b 4b 26 92 db 8b f3 2d 1a 74 92 fa 67 b7 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.02246094,  0.3828125 , ..., -0.5       ,
           -0.41699219, -0.421875  ]), textin=CWbytearray(b'0a c8 65 65 4f af 47 b0 fe 7c bd e1 fb ab 35 80'), textout=CWbytearray(b'22 26 22 c5 fc ff 06 da ca c2 8b 13 6a 3c c4 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01855469,  0.38085938, ..., -0.5       ,
           -0.40917969, -0.42578125]), textin=CWbytearray(b'14 8b 3d d0 48 44 9e b5 77 8d e4 d2 83 e7 43 f7'), textout=CWbytearray(b'01 72 0e dd b2 0d 21 df 9f bf e8 61 fc 46 6c af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03613281,  0.3828125 , ..., -0.5       ,
           -0.39453125, -0.40136719]), textin=CWbytearray(b'3e 08 94 8b 42 8a a2 c5 1c d7 27 b7 77 93 f5 9c'), textout=CWbytearray(b'45 b8 20 14 11 96 84 39 62 03 96 50 b4 6b fe 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05273438,  0.40625   , ..., -0.5       ,
           -0.44238281, -0.43847656]), textin=CWbytearray(b'95 a3 3c 76 bd 4e 49 65 45 fb 46 04 05 c8 00 c3'), textout=CWbytearray(b'9d 05 20 59 ea 48 6a 12 59 9e 66 01 07 4d b7 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.00878906,  0.37597656, ..., -0.5       ,
           -0.41210938, -0.41503906]), textin=CWbytearray(b'bc 86 db e1 e0 e2 07 57 f3 40 0f 9a 09 79 82 9f'), textout=CWbytearray(b'9b f1 e0 d0 60 73 4f be b5 ca 9a 86 57 42 89 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03710938,  0.39648438, ..., -0.5       ,
           -0.39453125, -0.40332031]), textin=CWbytearray(b'28 24 76 0c 23 f9 77 f5 cd 31 e3 53 c8 ce 79 01'), textout=CWbytearray(b'5d 2e 47 1b d5 f8 68 af 8d ba 20 20 44 f3 ce 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04394531,  0.40039062, ..., -0.5       ,
           -0.38964844, -0.39941406]), textin=CWbytearray(b'a9 a2 aa 80 ba 03 5d db 26 2c c3 6e 72 41 52 2c'), textout=CWbytearray(b'45 e0 90 0a e6 a5 40 fd f3 e6 4c 9a 3e 1f 28 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01953125,  0.3828125 , ..., -0.5       ,
           -0.38769531, -0.39648438]), textin=CWbytearray(b'ec f0 db 02 8d 94 3c 35 2d 65 ea 57 a5 0c 35 cf'), textout=CWbytearray(b'cf 1c dc 12 e1 5f 47 d3 aa 34 1e 7e 90 82 b6 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18945312,  0.05078125,  0.40625   , ..., -0.5       ,
           -0.44921875, -0.44628906]), textin=CWbytearray(b'46 01 8a b7 0c 96 b1 a2 0a dd b0 51 01 d1 4c 08'), textout=CWbytearray(b'95 cc f6 11 ec 05 15 16 8a 8e eb 3c 48 76 53 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.03417969,  0.39160156, ..., -0.5       ,
           -0.43847656, -0.43554688]), textin=CWbytearray(b'cb b9 e9 65 ff 27 91 05 68 c2 e2 94 1c e9 9e 16'), textout=CWbytearray(b'71 06 46 f7 64 37 1f 5b 4b 09 6f 3f 88 c4 77 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40136719, ..., -0.5       ,
           -0.41308594, -0.41796875]), textin=CWbytearray(b'58 c2 91 a6 73 c9 f3 ec b6 5f 4e 51 27 5f bf 2d'), textout=CWbytearray(b'e0 7d 4c bb cb f9 0d 5d ad f3 b6 b3 2a f6 4f ca'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.02734375,  0.390625  , ..., -0.5       ,
           -0.41210938, -0.41796875]), textin=CWbytearray(b'c5 b1 a6 0e 36 a3 1d 7c a6 6a 64 5a 3a 2a b6 35'), textout=CWbytearray(b'e1 0f 3e 63 19 0e 87 a7 4b 83 16 df 9e 49 2f 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.046875  ,  0.40429688, ..., -0.5       ,
           -0.40625   , -0.42285156]), textin=CWbytearray(b'a6 6c 14 f2 e5 d0 6d a2 e8 9a 05 f0 e7 15 85 1f'), textout=CWbytearray(b'15 cd 96 49 5b 85 20 0c 28 69 b1 74 74 d9 96 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.39941406, ..., -0.5       ,
           -0.39941406, -0.40625   ]), textin=CWbytearray(b'c6 61 7c af 17 84 b6 71 45 e0 bc bd 61 aa bc 7d'), textout=CWbytearray(b'bc 99 6b ba 89 06 aa 98 6c 77 f9 a5 2e a2 3f 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.01074219,  0.37792969, ..., -0.5       ,
           -0.39648438, -0.40332031]), textin=CWbytearray(b'50 7b 76 95 50 6b b7 e1 6d ec 45 e3 8a 16 3c be'), textout=CWbytearray(b'f3 bb 8e 3b 56 0f 9f e6 c8 ab 56 5e 14 13 60 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.0390625 ,  0.39550781, ..., -0.5       ,
           -0.39257812, -0.40234375]), textin=CWbytearray(b'23 8d ce 37 1e d9 42 7a 60 60 5f cf 56 49 3f 6a'), textout=CWbytearray(b'c1 6a e1 a9 fa 1a 97 48 df 25 8b 8b 16 8b e0 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.04785156,  0.390625  , ..., -0.5       ,
           -0.41699219, -0.41894531]), textin=CWbytearray(b'30 10 4e 2e ce 66 63 a3 67 a1 80 1e da 1b 8c 65'), textout=CWbytearray(b'0f dd 5e b8 e8 62 de 4e b1 33 00 29 48 6c 6d 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.02832031,  0.37792969, ..., -0.5       ,
           -0.4140625 , -0.41503906]), textin=CWbytearray(b'dc da 1d ae 6d c5 c5 b8 12 51 42 75 fa d2 1e d4'), textout=CWbytearray(b'b5 8f 65 66 91 03 11 01 c4 09 63 dd 60 ac 03 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.43066406, -0.43066406]), textin=CWbytearray(b'75 52 af 97 aa 22 fe a0 e6 ff b3 94 c8 c9 df 7a'), textout=CWbytearray(b'be 3f 8c 79 07 56 43 6f c1 2f a6 83 0e ab 2f 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12597656,  0.00585938,  0.37402344, ..., -0.5       ,
           -0.37988281, -0.39160156]), textin=CWbytearray(b'ca 26 8a ca f5 1f 01 fd c9 89 0b ff 51 c0 96 e3'), textout=CWbytearray(b'85 9d 73 16 cf e5 d2 74 45 8b 76 db 64 f4 59 c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04394531,  0.40234375, ..., -0.5       ,
           -0.4375    , -0.43359375]), textin=CWbytearray(b'17 38 10 f9 1e 54 0d dd 6f 0a 70 54 b9 54 7c ad'), textout=CWbytearray(b'0d 32 c8 d0 7d 72 3a 04 d6 ea 49 ca 2e 60 37 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.03027344,  0.39160156, ..., -0.5       ,
           -0.44628906, -0.44335938]), textin=CWbytearray(b'c8 46 8a 9e 30 cd 20 12 f6 cc 70 9f 2e ef 28 81'), textout=CWbytearray(b'69 7e 9a 17 8b 4b 6f 29 6e 9b 75 32 ae c0 30 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.0234375 ,  0.38574219, ..., -0.5       ,
           -0.44433594, -0.44335938]), textin=CWbytearray(b'32 af 4c a9 fa b6 49 c3 2a 60 9d 6b 66 8d fd 73'), textout=CWbytearray(b'64 dc 78 dc 3f 58 6c ef f6 90 43 2e fc f3 73 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.38183594, -0.39160156]), textin=CWbytearray(b'29 8f ff bf 14 15 44 21 cc 2d e8 b1 cc 5a cc 35'), textout=CWbytearray(b'ac 48 d9 ea 32 7d 67 99 7c dc e1 8b b1 34 23 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.39257812, ..., -0.5       ,
           -0.4296875 , -0.4453125 ]), textin=CWbytearray(b'1d f2 2e 3c 9d 74 fa 79 c7 61 91 72 9b d3 5d de'), textout=CWbytearray(b'e2 75 ed 8c b0 18 6b 86 32 2c 0c 75 5d 64 a2 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04980469,  0.40625   , ..., -0.5       ,
           -0.421875  , -0.42285156]), textin=CWbytearray(b'13 c6 91 ef f6 f2 2b 16 4b ee 30 9e 30 2f fa c6'), textout=CWbytearray(b'92 90 18 00 a7 db d7 f9 80 ab 2c d2 d1 83 44 21'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04199219,  0.38183594, ..., -0.5       ,
           -0.37597656, -0.38867188]), textin=CWbytearray(b'7c 94 32 6a 06 3e 26 8a c4 c3 de 16 d5 ac 6b 1e'), textout=CWbytearray(b'8a 24 e3 7f 6c 2a c7 8a ad 26 6d fd 74 99 1d 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03417969,  0.39550781, ..., -0.5       ,
           -0.41894531, -0.43457031]), textin=CWbytearray(b'51 5a e5 e7 78 54 0f 19 94 d8 ea 6b e7 f0 36 db'), textout=CWbytearray(b'03 d0 d1 ea d0 b9 56 57 ce 0d bc 6c c5 11 43 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.40625   , -0.42773438]), textin=CWbytearray(b'ea 72 98 65 09 50 e2 be 73 7b 54 cd 70 ec 24 24'), textout=CWbytearray(b'c2 b5 47 19 de ff a6 cc bd be f4 9d 1d 97 8f 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.046875  ,  0.39746094, ..., -0.5       ,
           -0.43847656, -0.43652344]), textin=CWbytearray(b'c0 a3 5f 1e c6 80 3d b9 81 40 57 50 f4 98 f8 35'), textout=CWbytearray(b'6c d4 c5 75 16 47 49 0c f8 59 08 1a 48 f5 ef 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13964844,  0.01464844,  0.37988281, ..., -0.5       ,
           -0.40136719, -0.40722656]), textin=CWbytearray(b'4f 1d e9 95 96 8e b8 45 92 c9 ca 02 58 d6 23 fb'), textout=CWbytearray(b'2b e0 94 32 4e 59 6b 0b 0a 6a 8c 39 79 07 ad 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02539062,  0.37304688, ..., -0.5       ,
           -0.42382812, -0.42675781]), textin=CWbytearray(b'27 3c 36 3b fc a1 64 5d 06 58 f4 3e 63 b3 8e 04'), textout=CWbytearray(b'18 01 32 00 22 87 8b 7e f3 1f f5 95 aa 35 bf ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03613281,  0.39648438, ..., -0.5       ,
           -0.41113281, -0.41601562]), textin=CWbytearray(b'cf 4d e1 e7 b2 9f 9c f6 50 60 32 74 aa 96 07 e0'), textout=CWbytearray(b'66 f6 db 6a c2 f7 2b 71 e2 f0 78 d9 6a c3 53 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.0234375 ,  0.37207031, ..., -0.5       ,
           -0.45019531, -0.44921875]), textin=CWbytearray(b'2a 3a 8c 3e 20 f5 58 10 ae 46 3f 9d a8 32 d7 9e'), textout=CWbytearray(b'8b af cc 51 81 2d 4f 9f 99 9d ad 5f 78 e6 09 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12890625,  0.00292969,  0.37109375, ..., -0.5       ,
           -0.40917969, -0.4140625 ]), textin=CWbytearray(b'e2 af ce 6c 12 6f bd 71 78 6a b7 01 84 92 86 2f'), textout=CWbytearray(b'b1 24 19 bb 9d cd f5 38 91 49 81 7d 16 33 b3 d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.00585938,  0.37011719, ..., -0.5       ,
           -0.41308594, -0.41601562]), textin=CWbytearray(b'85 05 05 8d 84 8f 28 87 25 63 ab fc 0b 08 88 8e'), textout=CWbytearray(b'a5 dc 33 61 e2 aa 0e 0b b0 16 40 d6 ff 64 61 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03515625,  0.38183594, ..., -0.5       ,
           -0.39941406, -0.40917969]), textin=CWbytearray(b'11 ed 48 4e b0 fb e6 d7 36 01 51 e8 66 9d e1 1c'), textout=CWbytearray(b'88 21 b3 7e 00 56 82 ab 23 1b fb cd f0 17 4a 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.02832031,  0.38378906, ..., -0.5       ,
           -0.42285156, -0.42773438]), textin=CWbytearray(b'dc c7 47 f2 90 0b 25 5c 12 35 be 70 aa e9 92 d6'), textout=CWbytearray(b'26 a9 d0 f7 9d 0c 65 83 f1 63 ae a9 69 31 6f d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02539062,  0.37011719, ..., -0.5       ,
           -0.38769531, -0.3984375 ]), textin=CWbytearray(b'f6 ff 5b f1 12 85 99 e3 9f c5 80 04 e1 f4 3b e0'), textout=CWbytearray(b'27 18 82 99 5c 04 ff 35 14 25 02 0e 40 5f b7 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.00683594,  0.37597656, ..., -0.5       ,
           -0.43164062, -0.43359375]), textin=CWbytearray(b'f6 96 b2 fb 69 6e 6b 64 fe 49 3c 57 48 c8 86 5a'), textout=CWbytearray(b'39 47 e3 8f 49 ff 51 fc c2 1a e4 da d8 fb 15 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40136719, ..., -0.5       ,
           -0.42773438, -0.42578125]), textin=CWbytearray(b'72 97 e0 ef 00 a4 cf 9c f5 a3 43 78 13 75 b3 f6'), textout=CWbytearray(b'2d be 07 6e 33 e4 1c 22 d4 7f 0c 5e 67 3c 52 e5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12695312,  0.0078125 ,  0.37402344, ..., -0.5       ,
           -0.40332031, -0.41113281]), textin=CWbytearray(b'fb 71 99 b3 84 73 b9 14 a0 93 7f ac fd 33 77 76'), textout=CWbytearray(b'35 5a 59 2c 9d 9c 13 af 86 a8 ea 0c d6 71 54 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19335938,  0.05566406,  0.40820312, ..., -0.5       ,
           -0.43945312, -0.44335938]), textin=CWbytearray(b'a0 65 ca d5 a9 b2 9f 88 61 76 84 d0 88 ef c3 dd'), textout=CWbytearray(b'50 fe 17 1d 53 27 9b b0 07 73 34 93 71 42 6f 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03320312,  0.39257812, ..., -0.5       ,
           -0.37304688, -0.38769531]), textin=CWbytearray(b'03 45 ef 79 c4 36 a7 e2 32 f9 e3 0f 6b 84 50 32'), textout=CWbytearray(b'9d 23 8e 90 8b ed a3 ee 12 08 ca c7 4f 1d de b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04394531,  0.40039062, ..., -0.5       ,
           -0.40722656, -0.4140625 ]), textin=CWbytearray(b'0f 64 30 be 95 64 af 34 0d 22 06 57 6e a2 ad 6f'), textout=CWbytearray(b'50 b8 2c 06 df a8 41 73 51 7c 7d 31 ef a5 62 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04492188,  0.40429688, ..., -0.5       ,
           -0.38769531, -0.40039062]), textin=CWbytearray(b'49 6c e0 cd 05 8c 89 33 f4 c3 75 f5 40 23 18 e7'), textout=CWbytearray(b'9a 1c b7 49 f5 3a d8 70 d5 ab d3 05 3e 77 fa 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04785156,  0.40527344, ..., -0.5       ,
           -0.39355469, -0.40234375]), textin=CWbytearray(b'44 f7 13 12 77 d9 db 0a 5d dd d5 67 3e 4d 54 a7'), textout=CWbytearray(b'31 ad b0 71 c7 f7 00 9d f5 88 b8 6e 3b e7 fc f9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04785156,  0.390625  , ..., -0.5       ,
           -0.41015625, -0.41601562]), textin=CWbytearray(b'01 d5 3b 00 5e 29 09 82 23 59 23 76 52 92 85 f8'), textout=CWbytearray(b'36 16 ca f2 a3 87 3d e3 6c 76 d7 a4 88 b2 9a 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04199219,  0.3984375 , ..., -0.5       ,
           -0.41308594, -0.41699219]), textin=CWbytearray(b'34 e3 39 ea 2f dc 11 bc fd c9 80 f0 63 e2 a0 38'), textout=CWbytearray(b'5d e7 ce 48 48 32 9d 9d 3d f7 9b 7c e0 a2 7c ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.03515625,  0.39550781, ..., -0.5       ,
           -0.41699219, -0.41992188]), textin=CWbytearray(b'1c a3 06 5f 88 68 c8 3c ed f8 e5 59 7c bc de 63'), textout=CWbytearray(b'9f 21 e1 a6 61 c0 bf a3 6c e2 a9 23 f0 e3 5d aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.37402344, -0.40039062]), textin=CWbytearray(b'1e 7f 08 07 9d 0b 00 f8 42 d5 e6 c9 94 34 9c 27'), textout=CWbytearray(b'8e 4f 8d e9 b7 9b 42 c3 df fc 79 1e 6a 46 b5 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04394531,  0.40136719, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'85 0b d9 b6 1d 9d 44 24 c4 ab f1 c1 50 2d 96 9e'), textout=CWbytearray(b'7d a2 2c 07 eb e6 7a 02 ca 95 2d e0 36 0b de 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.02148438,  0.38476562, ..., -0.5       ,
           -0.38183594, -0.40527344]), textin=CWbytearray(b'8f d2 dd f6 3e 2e 4e d4 b4 dd bc e9 a1 d3 81 d7'), textout=CWbytearray(b'a2 d6 ea 74 bf 9b a7 10 4f 09 69 89 8e bf 95 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.40625   , ..., -0.5       ,
           -0.41699219, -0.42089844]), textin=CWbytearray(b'e9 45 7e fa b8 5c e2 85 cb 67 81 6b 3b 05 e6 ba'), textout=CWbytearray(b'08 f4 5a a3 91 91 8c a9 75 80 76 87 70 ae 2b 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.03808594,  0.39941406, ..., -0.5       ,
           -0.42285156, -0.42285156]), textin=CWbytearray(b'50 ad 54 b8 f0 f8 aa d0 03 fd e2 66 f5 92 c1 1a'), textout=CWbytearray(b'1a 51 e9 96 1c 73 94 dc a0 87 f1 35 3a 0d 3f 64'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.02148438,  0.38378906, ..., -0.5       ,
           -0.4453125 , -0.44042969]), textin=CWbytearray(b'6e c5 4a f8 36 05 ab d8 a3 aa a2 06 01 97 ca 66'), textout=CWbytearray(b'1b 45 55 13 d9 af 2e ec 94 41 ef fb 48 86 4e e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04394531,  0.40332031, ..., -0.5       ,
           -0.43457031, -0.43554688]), textin=CWbytearray(b'd8 24 d5 29 b4 a5 03 8c 61 e0 16 4b 2f 87 d4 90'), textout=CWbytearray(b'3b 37 db 99 fb d6 ba f1 fa 71 5e 14 ab 8a 67 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.00488281,  0.37304688, ..., -0.5       ,
           -0.40527344, -0.41308594]), textin=CWbytearray(b'88 6b 9b e5 5e a4 d4 ed 25 ab 80 63 67 09 25 67'), textout=CWbytearray(b'd2 d3 1f e4 b1 3a fc bb 9d 9d 7e 65 8e a0 61 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03515625,  0.39355469, ..., -0.5       ,
           -0.40039062, -0.40527344]), textin=CWbytearray(b'fa b1 36 1d a4 96 8c bf 42 50 01 c1 9e b1 1c c9'), textout=CWbytearray(b'9c 1a 2f 7f 3a e4 b0 7f 90 78 e3 5d e5 39 a6 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.42285156, -0.42382812]), textin=CWbytearray(b'64 a3 ba ec 36 8c 2b ce 48 40 57 18 49 27 9b 06'), textout=CWbytearray(b'51 27 6c fc 1a e3 76 6f 89 a4 55 2b 95 68 5a 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40136719, ..., -0.5       ,
           -0.38085938, -0.39355469]), textin=CWbytearray(b'30 78 d0 56 38 8f c3 f0 c5 11 0f 90 81 2a 1f c8'), textout=CWbytearray(b'81 7b ed 45 44 26 6b b3 6d ef 92 d9 40 e1 f9 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.04980469,  0.390625  , ..., -0.5       ,
           -0.42578125, -0.42871094]), textin=CWbytearray(b'fc a7 87 ab 87 1e a8 6a 46 f6 65 8d 68 25 a4 be'), textout=CWbytearray(b'81 6f f3 a6 a3 4c fb 22 52 22 3b 7f 11 84 55 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40039062, -0.41113281]), textin=CWbytearray(b'20 8c 14 7c 15 a5 6d be e1 df 6d cf 61 6d 78 42'), textout=CWbytearray(b'69 ce b4 aa 5d 9c 5e 9c fa 0a 6f 31 ae 15 37 b0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.40625   , -0.41210938]), textin=CWbytearray(b'c6 01 4c db b0 b1 30 a0 5e 50 33 38 c6 fe 2c b7'), textout=CWbytearray(b'fb c0 0b af 27 cd 47 f5 c2 c4 28 95 1f 1e 95 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03710938,  0.38183594, ..., -0.5       ,
           -0.41992188, -0.42480469]), textin=CWbytearray(b'f4 eb bf 72 84 58 3f f5 b8 20 18 d3 dd 72 7b 61'), textout=CWbytearray(b'10 6a 74 74 5d 13 8a 6d e5 b5 d0 f2 15 5f 9a af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40332031, -0.421875  ]), textin=CWbytearray(b'8b 1e 45 10 6d 5d 35 d4 f8 65 9c c1 8b b4 f6 ee'), textout=CWbytearray(b'a2 70 3f 54 d8 39 0d fb 4b 13 f9 e5 5c 70 d0 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.45800781, -0.44921875]), textin=CWbytearray(b'1e b7 41 db b2 14 72 70 1e e3 59 85 e1 44 8c 41'), textout=CWbytearray(b'15 84 04 1e fc d8 ea 93 e3 f1 8f bb e8 27 44 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03515625,  0.39648438, ..., -0.5       ,
           -0.38867188, -0.39941406]), textin=CWbytearray(b'ce 71 3c 49 ac 75 79 f6 db 15 c8 7b 41 6b d1 e6'), textout=CWbytearray(b'4a cf eb af 75 d5 3b 59 24 1d dc 4f 2b 09 2b f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04882812,  0.40332031, ..., -0.5       ,
           -0.38378906, -0.40136719]), textin=CWbytearray(b'c6 18 59 65 8c ed 1e 67 da eb 63 33 62 62 66 0d'), textout=CWbytearray(b'e0 6a b4 bb 94 3b 51 7f d6 29 f0 84 d0 35 07 d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04101562,  0.38671875, ..., -0.5       ,
           -0.42773438, -0.4296875 ]), textin=CWbytearray(b'21 a5 f3 71 95 62 4d 46 09 3e b7 c4 f2 62 5d 1d'), textout=CWbytearray(b'07 58 db 2d 7a 30 58 d2 f1 7b e9 1d 88 0a 8d a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.39550781, ..., -0.5       ,
           -0.42089844, -0.42382812]), textin=CWbytearray(b'b3 71 a1 4a 2a 3b ef 27 85 62 d0 5c 38 0b 80 18'), textout=CWbytearray(b'2b 0d 42 ff 6b 7d b8 f7 d5 9e c3 c7 77 ae 18 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.43847656, -0.4375    ]), textin=CWbytearray(b'e1 a3 c8 26 70 8b c5 65 ee 51 13 1a 1e eb 15 bb'), textout=CWbytearray(b'f3 72 9a b0 97 5e 49 24 66 45 f4 8b 59 f3 21 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'8e e9 97 ee f3 54 e3 33 2c f2 2e c8 38 2a 21 d6'), textout=CWbytearray(b'88 bf c2 db db ea 25 36 89 c9 71 56 d0 3c aa 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.03125   ,  0.390625  , ..., -0.5       ,
           -0.41992188, -0.42382812]), textin=CWbytearray(b'ed de 4f 11 2b cd 07 5e a9 50 cb fa bc f7 8a 5e'), textout=CWbytearray(b'ce d3 59 e7 78 1c 84 f2 ad f9 0d 39 dc 5b 30 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.03027344,  0.39160156, ..., -0.5       ,
           -0.38476562, -0.39746094]), textin=CWbytearray(b'f1 ed 33 6f 5b 93 78 dd 7c af a1 0d ee 09 f7 82'), textout=CWbytearray(b'77 15 0e f5 c0 c2 e0 ba a9 e8 87 09 fd 50 0c ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.03222656,  0.39355469, ..., -0.5       ,
           -0.41992188, -0.42285156]), textin=CWbytearray(b'a8 9e 1a b1 59 36 90 fe d6 29 ae 2c dc af f5 ed'), textout=CWbytearray(b'a9 5a e0 76 e8 2a 12 9c e1 ea 62 36 e9 91 ae 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19628906,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.41503906, -0.41894531]), textin=CWbytearray(b'6d 7d 86 40 04 c7 ca f1 34 1c 6e 25 3a 64 e8 eb'), textout=CWbytearray(b'dc 4d 27 b3 c7 bc 74 e3 55 5b dd 24 58 cc 51 ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.125     , -0.01269531,  0.35546875, ..., -0.5       ,
           -0.4140625 , -0.41992188]), textin=CWbytearray(b'e9 8e 08 84 b0 e7 d1 79 e4 e7 9a eb 31 cc 05 9d'), textout=CWbytearray(b'15 f2 2a be e1 b9 9d e9 4b 59 49 82 54 7d 17 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03613281,  0.39746094, ..., -0.5       ,
           -0.4296875 , -0.43066406]), textin=CWbytearray(b'd3 88 1c 92 31 1a 8d dd 5e 6b a8 66 6b ab 52 bd'), textout=CWbytearray(b'7a a2 3e 83 f1 52 ed 4f 86 88 ba bf 00 88 b0 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03320312,  0.39160156, ..., -0.5       ,
           -0.39941406, -0.40722656]), textin=CWbytearray(b'01 68 2b 54 55 f9 fb 92 8e d2 29 9f 1f 48 50 35'), textout=CWbytearray(b'f8 ce 2b aa 76 b1 3d 43 2d 56 1a b7 8b 64 60 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.41015625, -0.4296875 ]), textin=CWbytearray(b'3c 70 45 bb fa 48 91 b0 4d dc 48 f0 61 03 60 5f'), textout=CWbytearray(b'4b d6 3d 44 0d 19 4f b2 76 fe 89 7e 2c dc ff a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19335938,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.44238281, -0.43847656]), textin=CWbytearray(b'2f 6d 3a 2a 67 27 fa 2a 9f 99 8b ac a2 50 fb 53'), textout=CWbytearray(b'a1 41 c5 ee 4f be 70 00 b1 bb 81 54 56 91 fc 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04394531,  0.40039062, ..., -0.5       ,
           -0.41699219, -0.421875  ]), textin=CWbytearray(b'b8 95 72 39 93 4f 2c 6b 49 27 32 44 50 69 a1 31'), textout=CWbytearray(b'1f 46 40 e8 e9 1d 4a 2b d1 d9 6b d2 85 78 04 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.046875  ,  0.40234375, ..., -0.5       ,
           -0.40234375, -0.40917969]), textin=CWbytearray(b'8a 18 bc 5f bb cf 26 dc b1 21 b2 bf bf 88 14 0b'), textout=CWbytearray(b'd7 5f 75 98 b8 09 ee 5f 51 18 8f af a9 5e 68 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04980469,  0.40625   , ..., -0.5       ,
           -0.41210938, -0.41503906]), textin=CWbytearray(b'c0 b3 ee e7 92 13 95 4b 3a 98 2a 6a f4 4a 07 e2'), textout=CWbytearray(b'cb d6 ef 11 65 b9 6e f2 c0 37 80 64 fa 41 56 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.45214844, -0.44921875]), textin=CWbytearray(b'8a a0 e1 92 4b ef ac 65 a3 55 af f8 07 bb 4f 73'), textout=CWbytearray(b'95 c4 7e 35 eb 8c e5 aa 6d f7 4f aa 47 bb 96 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.42480469, -0.42675781]), textin=CWbytearray(b'52 c4 a3 11 41 75 61 b6 9a 33 e4 83 de 03 c5 31'), textout=CWbytearray(b'2f 53 57 04 d0 86 3c eb e4 90 2c 4b 54 63 58 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.41894531, -0.42285156]), textin=CWbytearray(b'10 51 b2 e0 dd 1f fb 5a 41 c8 1e fa 8e e1 71 ab'), textout=CWbytearray(b'e0 35 35 41 63 81 7b 50 26 d9 80 c6 f8 26 24 12'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40332031, -0.41015625]), textin=CWbytearray(b'10 09 2f 87 f8 dc 83 35 2d f1 89 1e eb 35 f7 54'), textout=CWbytearray(b'42 bf c0 9d 1d 8e 28 31 eb d4 51 bc 58 78 88 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12890625,  0.0078125 ,  0.37402344, ..., -0.5       ,
           -0.40039062, -0.42285156]), textin=CWbytearray(b'07 47 fe aa 12 58 3d d6 d6 c6 8f 40 60 a4 e1 a7'), textout=CWbytearray(b'4d ad 48 dd 15 fd 8c 84 7d c5 53 cf 70 77 25 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.42382812, -0.42578125]), textin=CWbytearray(b'07 86 81 db f5 a4 80 9c 93 d3 30 71 b6 dd 94 79'), textout=CWbytearray(b'01 d7 9c d3 10 9d a7 f8 15 29 1c f4 c7 b6 24 12'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03515625,  0.38085938, ..., -0.5       ,
           -0.40917969, -0.41210938]), textin=CWbytearray(b'e3 4e a1 fc df cc 76 7c e1 27 7d 66 08 8c 90 de'), textout=CWbytearray(b'0d 63 7e e1 42 37 8f e3 5e 94 32 20 13 ca a6 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39550781, ..., -0.5       ,
           -0.4140625 , -0.41992188]), textin=CWbytearray(b'f5 a1 55 a8 71 09 c8 8e d6 66 71 66 7b e6 f9 67'), textout=CWbytearray(b'99 09 d5 80 e8 c2 01 e0 25 c6 88 c5 d6 2a 3a dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.02929688,  0.39453125, ..., -0.5       ,
           -0.38867188, -0.40136719]), textin=CWbytearray(b'32 7a d3 26 a8 ef 1f 8a 14 82 44 de e1 9d 62 b1'), textout=CWbytearray(b'a8 a4 68 03 3f a8 0c c7 1c e5 08 8b 04 04 7f 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.05664062,  0.39550781, ..., -0.5       ,
           -0.41503906, -0.41796875]), textin=CWbytearray(b'cd c7 08 fc 16 01 88 05 9c e1 c2 57 5f e1 e9 22'), textout=CWbytearray(b'c4 64 ad 51 92 aa 91 5d 62 38 96 22 6b 39 a6 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.03222656,  0.39257812, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'78 75 52 fe 53 d6 fc f7 27 10 3e 6a 97 bf a2 c1'), textout=CWbytearray(b'24 79 2f 81 88 9c ab 3b d1 09 e0 ec 79 e3 91 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.4375    , -0.4375    ]), textin=CWbytearray(b'f4 20 05 39 76 3f 7d 42 80 8a 31 c2 bc 28 fa e4'), textout=CWbytearray(b'd0 df a9 1f 1d b7 74 2f 45 50 c3 f2 44 0e a6 fd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.04003906,  0.39648438, ..., -0.5       ,
           -0.41015625, -0.41503906]), textin=CWbytearray(b'c0 4b 47 d4 ef a0 44 72 5e de f7 a4 1d 13 df 36'), textout=CWbytearray(b'be c2 62 5b e8 10 f5 c9 02 44 77 c1 03 32 d3 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19433594,  0.04296875,  0.40136719, ..., -0.5       ,
           -0.4375    , -0.43554688]), textin=CWbytearray(b'c3 07 4f 03 cc 91 57 79 a1 e8 af 6c 00 47 a2 e2'), textout=CWbytearray(b'83 1a c5 3f 08 bb f3 c5 57 82 26 46 90 1b 55 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.41210938, -0.41601562]), textin=CWbytearray(b'e2 5d cf 5a 5c 83 40 b8 f6 5b 2d f0 d1 26 a5 a0'), textout=CWbytearray(b'18 c2 2a da e4 28 de a0 e8 22 b4 be 51 d3 64 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.39160156, -0.40039062]), textin=CWbytearray(b'0a 3c 64 19 df ed 08 1c 6d 2f c0 dd 50 2a 0b 71'), textout=CWbytearray(b'39 99 f0 cc 5d b1 13 59 f5 17 75 14 1b 92 94 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01953125,  0.3828125 , ..., -0.5       ,
           -0.39550781, -0.40527344]), textin=CWbytearray(b'c9 94 11 ce 49 e6 54 b4 7e 5e 7e dd e3 9d c9 99'), textout=CWbytearray(b'23 82 c3 d9 36 98 85 10 73 44 c6 9b 9a cf 00 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125, -0.00097656,  0.36816406, ..., -0.5       ,
           -0.37988281, -0.39453125]), textin=CWbytearray(b'76 a7 73 06 cc 5a 9f 96 05 6d 8d 30 7e 9b 48 7d'), textout=CWbytearray(b'bc c9 83 8f 01 f7 87 ad 5c fd bd 6f 9d 7e f1 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.41308594, -0.41894531]), textin=CWbytearray(b'5f 22 c0 35 f2 c2 66 18 f5 2f aa 65 8d 16 ae 25'), textout=CWbytearray(b'47 1e fa 69 30 87 84 90 ea 4b fa f2 91 fa d0 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.02246094,  0.38476562, ..., -0.5       ,
           -0.38378906, -0.40917969]), textin=CWbytearray(b'4f 82 b8 70 4b e1 25 ee 1a ae 46 90 5b c1 36 29'), textout=CWbytearray(b'ba 24 24 4c de a3 03 8a 71 3b 20 ec fb e5 e0 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03222656,  0.39160156, ..., -0.5       ,
           -0.38378906, -0.39257812]), textin=CWbytearray(b'86 6f f1 8c 6c 7c ec 73 12 82 0b da c9 b6 ec 87'), textout=CWbytearray(b'8a 28 bc 3d aa 53 62 96 a8 1a 59 59 07 a1 1d f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.39746094, ..., -0.5       ,
           -0.421875  , -0.42675781]), textin=CWbytearray(b'9c 5e 48 2c 27 8a 62 b0 51 2c 6a ab 4c c7 71 96'), textout=CWbytearray(b'2d b3 09 5a 25 f1 15 95 19 41 3a 96 dd 80 60 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.01171875,  0.37890625, ..., -0.5       ,
           -0.41699219, -0.41992188]), textin=CWbytearray(b'23 83 11 74 1f 79 df ac 61 0f c1 37 3b 97 86 6e'), textout=CWbytearray(b'ee d0 87 01 83 8c 10 c4 d2 99 8e f3 74 0e 91 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.42382812, -0.42675781]), textin=CWbytearray(b'99 69 6d 45 f1 f8 cf fc 9c 19 62 fd ef c8 af 6d'), textout=CWbytearray(b'e5 10 bb 34 d4 4e 97 3f 11 8c 13 74 20 02 52 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.04882812,  0.40039062, ..., -0.5       ,
           -0.43066406, -0.43261719]), textin=CWbytearray(b'62 87 36 a0 a8 9c 2f c4 d8 19 28 cb 4a 0c 53 7e'), textout=CWbytearray(b'd8 f6 5b d0 c7 e6 84 6f 9e e5 a8 86 8e 9e a6 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19140625,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.40136719, -0.40820312]), textin=CWbytearray(b'38 5c 0b 6a b6 75 70 dd 03 68 ae 6f 73 86 a8 97'), textout=CWbytearray(b'be 36 1c d8 63 f2 97 fc 00 71 92 2f 54 05 47 de'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.40917969, -0.43164062]), textin=CWbytearray(b'53 50 01 83 e2 25 c6 0d 08 0f 97 78 75 4c 56 70'), textout=CWbytearray(b'39 b6 1e 11 20 2c 8d 4d cb df f7 22 c7 93 d2 4b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.39160156, -0.40234375]), textin=CWbytearray(b'e4 d3 ee bf 31 42 42 08 ea 2d ab fb df 99 0e 35'), textout=CWbytearray(b'29 e6 fb c3 d1 a6 51 07 99 13 e6 e3 2f bd ba 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.37792969, -0.38867188]), textin=CWbytearray(b'01 25 fd 1a 9a ce 0e f1 04 94 93 3d 93 e5 bc 8a'), textout=CWbytearray(b'4f 53 8c 10 2e 6d 33 33 82 36 1c 91 3b 18 87 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.43554688, -0.43847656]), textin=CWbytearray(b'49 eb 11 27 f4 c2 eb ec de 4b bc 07 4d c6 31 91'), textout=CWbytearray(b'6b 18 36 0c 2e 37 c0 d6 61 05 a6 6a 92 68 b1 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13378906,  0.0078125 ,  0.37207031, ..., -0.5       ,
           -0.41113281, -0.41503906]), textin=CWbytearray(b'b9 1d e6 1d 2d 04 84 ba 99 95 84 93 d8 3a a3 75'), textout=CWbytearray(b'3f 29 e8 40 08 e8 69 bd 99 b5 77 3a 2e 0c 2d 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.02148438,  0.38671875, ..., -0.5       ,
           -0.41992188, -0.42285156]), textin=CWbytearray(b'ff e0 d8 94 3e 6d 47 8a 57 05 b6 07 72 a6 bb d7'), textout=CWbytearray(b'dd 0b bd 0e a4 69 74 98 6c 16 8f 03 61 f3 05 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03613281,  0.39648438, ..., -0.5       ,
           -0.3984375 , -0.40429688]), textin=CWbytearray(b'98 7e 4d 12 33 8a 58 70 2d 09 a5 e2 70 df cd 2a'), textout=CWbytearray(b'c7 dc f5 9f b3 25 06 91 30 37 0e 87 22 92 17 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12011719, -0.01660156,  0.35644531, ..., -0.5       ,
           -0.40234375, -0.41113281]), textin=CWbytearray(b'cd 7d 03 c0 5c 3e 49 a9 5b ed 39 0e ac c8 6e 53'), textout=CWbytearray(b'e4 b5 0e 84 c2 8b 6d f7 03 44 2d e1 32 89 f2 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.44433594, -0.44140625]), textin=CWbytearray(b'e8 8d 8d 5a 8c 13 e7 79 14 f0 30 90 43 9a 7a 70'), textout=CWbytearray(b'1c 3b 3f 49 57 e3 3e 67 c7 5c fb 68 58 17 fb ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.39550781, ..., -0.5       ,
           -0.41503906, -0.421875  ]), textin=CWbytearray(b'e4 79 d7 9a e9 42 13 f4 59 b2 c4 9b 03 34 95 07'), textout=CWbytearray(b'70 24 90 5f 12 ce 16 8d 69 7e cd 5c 4f 06 25 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.41796875, -0.42285156]), textin=CWbytearray(b'a4 83 ac c2 f5 aa c4 ae 16 67 33 1a 52 ff e1 dd'), textout=CWbytearray(b'80 7f 08 28 18 9d 8f 29 d5 6a 11 dc 83 31 ec 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.0234375 ,  0.38476562, ..., -0.5       ,
           -0.41015625, -0.41992188]), textin=CWbytearray(b'a3 83 83 b3 4b 54 9e d0 a5 63 f0 93 1d 0f 66 75'), textout=CWbytearray(b'b6 a9 e3 d0 3a a2 9c 2b 79 af 48 e8 fe 9b b1 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.03222656,  0.390625  , ..., -0.5       ,
           -0.45214844, -0.44824219]), textin=CWbytearray(b'75 4d e6 85 b1 aa 4d 5a aa 12 a5 6b 0c e3 3b a4'), textout=CWbytearray(b'79 a8 b2 68 bd cd 91 28 7c 96 03 ac e3 04 28 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.03222656,  0.390625  , ..., -0.5       ,
           -0.41015625, -0.41308594]), textin=CWbytearray(b'6e e3 0b 40 82 fc 5d 60 7c bf 32 98 90 7b 3f 74'), textout=CWbytearray(b'0f ce 35 dc 5a e0 0a 80 9b de 57 62 9e a4 f4 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03027344,  0.375     , ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'32 f9 5a dc 63 15 b9 51 0e 45 bf 8d 98 45 6d 6d'), textout=CWbytearray(b'89 a6 41 af 72 af 4a e8 b6 e2 bf 60 22 c5 e2 4b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.03027344,  0.37792969, ..., -0.5       ,
           -0.40820312, -0.41699219]), textin=CWbytearray(b'c8 e8 e7 88 18 6b 86 b8 b0 17 5c cc 10 48 db fb'), textout=CWbytearray(b'92 05 6d bb ba e9 6f 62 75 f6 7d 01 33 fd 5b b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.42675781, -0.42773438]), textin=CWbytearray(b'1b 1d f2 32 50 92 9a 01 02 82 0b 51 a6 12 e8 7d'), textout=CWbytearray(b'4c 1b fe a9 d8 05 22 af b7 17 50 83 dd 54 fb 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03125   ,  0.39453125, ..., -0.5       ,
           -0.47753906, -0.47070312]), textin=CWbytearray(b'c7 ff df fd 22 ae 56 72 e8 c2 4b f4 ae e9 a9 45'), textout=CWbytearray(b'fc 29 ee a1 e9 02 e9 d2 b9 94 b1 62 bd 93 8e 64'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.02832031,  0.39160156, ..., -0.5       ,
           -0.390625  , -0.39941406]), textin=CWbytearray(b'37 12 11 ec 07 ef be 55 63 f6 d5 8e 5e 7a 46 55'), textout=CWbytearray(b'0c 53 93 11 b4 bc de 38 c5 18 93 3c 04 95 91 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02148438,  0.38183594, ..., -0.5       ,
           -0.43945312, -0.43847656]), textin=CWbytearray(b'3d 53 da 0d 51 51 71 8a 42 d9 6c 77 b2 b4 79 77'), textout=CWbytearray(b'c1 4c a4 b3 92 b9 fd 64 3d e1 80 dd 60 dd bb 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.0390625 ,  0.39257812, ..., -0.5       ,
           -0.42675781, -0.43066406]), textin=CWbytearray(b'5d 00 87 eb c4 4b 35 0b 34 94 dd cd ee 3a f7 35'), textout=CWbytearray(b'f5 a3 11 d5 a8 cf 1a 12 3c d9 d5 6e a5 c5 97 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03417969,  0.39550781, ..., -0.5       ,
           -0.37207031, -0.39941406]), textin=CWbytearray(b'0e 24 dc b5 83 34 a6 17 b5 4c ce 3e ee 4b 87 f1'), textout=CWbytearray(b'9e 2b e8 5f 01 31 11 2d 11 49 23 e1 29 86 3d b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.00195312,  0.37304688, ..., -0.5       ,
           -0.36425781, -0.37988281]), textin=CWbytearray(b'82 04 95 9c 44 2f 7f 5c d0 2a 07 9f fe f2 4c 3b'), textout=CWbytearray(b'62 54 76 da 3f 49 d0 c9 2d 39 cb a2 67 64 4f 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.10449219, -0.01660156,  0.35449219, ..., -0.5       ,
           -0.41796875, -0.43261719]), textin=CWbytearray(b'21 5a fd 59 17 3f 20 5f dc 9d a6 47 a5 3c 48 d4'), textout=CWbytearray(b'42 4c 40 79 d8 c2 ec 27 93 88 4d fe e7 86 72 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.38867188, -0.40039062]), textin=CWbytearray(b'a6 35 ba bc 1f b7 5d 54 f9 9c 7f b1 eb 12 73 88'), textout=CWbytearray(b'65 88 88 e4 f6 dc b8 67 64 bf 47 93 5c 66 b1 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04101562,  0.3828125 , ..., -0.5       ,
           -0.39941406, -0.40625   ]), textin=CWbytearray(b'8f 8e b4 45 cb 8e 98 27 6d bb 9e 44 3a 79 61 86'), textout=CWbytearray(b'b2 33 e3 2c e0 cd ab 6a c2 43 24 ec b3 dc 54 da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.02832031,  0.38964844, ..., -0.5       ,
           -0.421875  , -0.42089844]), textin=CWbytearray(b'93 03 aa c9 e0 94 9b 05 00 79 a3 8c ad 71 8c 61'), textout=CWbytearray(b'46 3e 7c 6d 86 2b 38 04 45 5c ae 65 84 ed ab 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14160156,  0.01757812,  0.38183594, ..., -0.5       ,
           -0.42285156, -0.42285156]), textin=CWbytearray(b'ce 8d 02 51 eb ec 15 82 83 ed 23 c9 ea f5 2d 29'), textout=CWbytearray(b'51 a9 8d f9 bd 61 3d 4f d0 a1 83 7f bd 84 39 21'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03027344,  0.37304688, ..., -0.5       ,
           -0.42871094, -0.43359375]), textin=CWbytearray(b'9f 3d 69 00 4f 49 64 4c 01 e0 86 e0 04 23 31 4c'), textout=CWbytearray(b'd9 72 db f0 70 b0 38 b2 61 b7 d5 29 e9 4e c8 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04980469,  0.39160156, ..., -0.5       ,
           -0.44238281, -0.43945312]), textin=CWbytearray(b'3f e7 28 55 9b 7f 08 3d 3f 91 93 10 aa a9 a7 0a'), textout=CWbytearray(b'7a c5 d0 a5 dd 33 29 1a 42 4a f0 98 d3 c5 55 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18945312,  0.05371094,  0.39355469, ..., -0.5       ,
           -0.43164062, -0.43066406]), textin=CWbytearray(b'99 73 3d 27 0c b9 dc 85 0d d9 db 4f f6 d1 76 3e'), textout=CWbytearray(b'a3 6f 5e ea 86 78 ea c7 fe 02 b4 c3 97 97 df af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.42480469, -0.42773438]), textin=CWbytearray(b'c2 52 ff 1c fd a0 49 9e 63 2d dd be 0e 5a b4 48'), textout=CWbytearray(b'98 c3 66 86 68 47 fa 78 28 ed 84 01 a3 f4 0a 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0390625 ,  0.38964844, ..., -0.5       ,
           -0.38085938, -0.39550781]), textin=CWbytearray(b'4a 53 8a 44 c0 26 3e 9a 14 d1 80 2f 53 b9 e1 d6'), textout=CWbytearray(b'0e 0b 26 0f 96 d4 65 71 d8 b0 55 f0 46 44 bf a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04882812,  0.40429688, ..., -0.5       ,
           -0.43359375, -0.43359375]), textin=CWbytearray(b'cc da 6f c9 21 2b 28 09 b4 6b a6 6a bd b5 4a 40'), textout=CWbytearray(b'f5 5e 3a 91 ea d8 be 4b a1 9a 1d 4a cc 1d a2 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40527344, ..., -0.5       ,
           -0.41308594, -0.43066406]), textin=CWbytearray(b'9e dd 58 64 fd b4 97 1c cc d4 8a 77 2b 5f b6 ad'), textout=CWbytearray(b'df d3 65 38 6e 20 d3 8d 9b a1 a6 d1 4e 61 d0 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.01367188,  0.38085938, ..., -0.5       ,
           -0.38378906, -0.39257812]), textin=CWbytearray(b'be 43 fd e7 28 26 46 62 40 a0 8c f5 95 1b 0c de'), textout=CWbytearray(b'25 b1 42 28 3e 50 03 6c 9c fa 8d c0 1b 94 f6 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.01367188,  0.37890625, ..., -0.5       ,
           -0.40332031, -0.41113281]), textin=CWbytearray(b'2b ad b9 9e 89 ad 6b 61 e8 ed f2 0a 86 92 ad 8e'), textout=CWbytearray(b'd0 88 4d 85 ac 5f b0 cb 56 d7 bc 89 43 e9 e6 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.39746094, ..., -0.5       ,
           -0.39941406, -0.41015625]), textin=CWbytearray(b'07 98 45 87 18 5c 35 2b 07 3b 8f 6a 77 37 ae be'), textout=CWbytearray(b'0b 5f a0 ea c7 9a 34 99 06 b0 02 f5 25 e6 b2 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38671875, ..., -0.5       ,
           -0.38964844, -0.40234375]), textin=CWbytearray(b'86 35 19 49 81 5e 58 1f 0d bb 4f 75 8b 8c d9 9c'), textout=CWbytearray(b'3a 79 e2 39 c5 61 60 f7 2a ea bc 4a f2 c5 ce f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04296875,  0.38671875, ..., -0.5       ,
           -0.42871094, -0.42773438]), textin=CWbytearray(b'78 d0 c9 74 51 85 5a e1 73 f9 5a 81 37 9d 7f a3'), textout=CWbytearray(b'bf f8 19 cb 1f 7f cd 32 29 df dd e8 8e f4 4f c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01953125,  0.38574219, ..., -0.5       ,
           -0.421875  , -0.43457031]), textin=CWbytearray(b'b5 a3 66 d9 b3 31 d3 b0 6c c2 c0 30 e2 b7 0a 8f'), textout=CWbytearray(b'cb 4c d5 30 3e 29 1b dd 73 0e 1e 15 89 ae 9c 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.41699219, -0.421875  ]), textin=CWbytearray(b'98 b2 0a c1 a7 c3 52 eb 8e 42 05 71 80 a5 33 8c'), textout=CWbytearray(b'89 57 42 8e b7 d9 08 ef 88 02 d4 10 bf 96 53 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04296875,  0.39941406, ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'97 b9 bc 2a ae ce 1d 7f 55 67 90 91 5e b8 bf 9b'), textout=CWbytearray(b'49 e4 6e 00 04 a2 62 ec b9 7c f0 34 0d d6 aa 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.38671875, -0.41308594]), textin=CWbytearray(b'81 51 09 1c d8 57 a5 c6 f5 9c 19 40 a2 4b dd de'), textout=CWbytearray(b'8e 3e 12 81 33 69 90 98 3b 64 d0 03 27 24 a1 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188,  0.01171875,  0.37597656, ..., -0.5       ,
           -0.41113281, -0.41503906]), textin=CWbytearray(b'39 84 ab 1d ed 9e c5 a9 76 39 ff c7 e1 01 13 d1'), textout=CWbytearray(b'1d 9f ea da 5a 28 d8 a9 ba 46 5f eb a6 f6 a5 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.38769531, -0.3984375 ]), textin=CWbytearray(b'57 c1 04 78 1f 82 a0 2a 8d 5f 93 51 f8 ae 85 a6'), textout=CWbytearray(b'd2 28 34 99 79 7b c7 a8 a4 80 bb d2 c2 e7 7f 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.39453125, -0.40332031]), textin=CWbytearray(b'31 67 71 ff 10 5c 08 de 63 73 4f 34 82 42 01 e3'), textout=CWbytearray(b'd4 23 10 7a 69 d4 c8 76 3d 0b 1f 1f d9 f0 d9 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14355469,  0.02050781,  0.36621094, ..., -0.5       ,
           -0.421875  , -0.42285156]), textin=CWbytearray(b'5e 43 69 e6 b3 87 56 7e 04 df b7 c0 d0 0c aa 8e'), textout=CWbytearray(b'6d 04 2f 96 16 2d b1 22 2c 0b 55 a5 26 5f 63 d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02050781,  0.38574219, ..., -0.5       ,
           -0.375     , -0.38769531]), textin=CWbytearray(b'a6 60 70 5d 84 0e 06 59 76 e7 db 22 7f b4 6f 98'), textout=CWbytearray(b'e3 77 d4 ec 76 94 17 ae 81 5a 07 81 49 69 73 d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19140625,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.41992188, -0.42285156]), textin=CWbytearray(b'db ed 82 31 94 5b 1d 25 70 cf 99 2a f3 a2 a4 7a'), textout=CWbytearray(b'13 94 89 d0 8c 36 ec 5b 7b df 05 87 06 18 b6 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02832031,  0.38964844, ..., -0.5       ,
           -0.41699219, -0.43945312]), textin=CWbytearray(b'9f 2d da b1 a8 da 31 b8 51 34 c2 a9 7c d1 4e 6a'), textout=CWbytearray(b'd7 6f 2b 2f e0 b1 9f b1 58 91 cf dc d8 3e 64 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.0234375 ,  0.37304688, ..., -0.5       ,
           -0.41796875, -0.421875  ]), textin=CWbytearray(b'32 9c a3 a6 da db 61 77 c0 c2 c4 01 9c f7 99 96'), textout=CWbytearray(b'ed f1 16 f5 d7 e3 36 5b d6 25 0c f7 c8 9c 07 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.02441406,  0.38574219, ..., -0.5       ,
           -0.40429688, -0.41015625]), textin=CWbytearray(b'c9 88 3e 46 17 7b 89 d6 98 8d ae 4f c8 dc 96 73'), textout=CWbytearray(b'b1 f8 d1 86 a3 d6 39 15 9d 72 d1 70 58 be 4a 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04199219,  0.40136719, ..., -0.5       ,
           -0.47167969, -0.46191406]), textin=CWbytearray(b'ff 86 85 f9 83 eb 07 b1 f5 d1 d1 f1 22 96 10 d4'), textout=CWbytearray(b'57 96 ed d9 23 fa 79 63 1c 73 80 93 6e 71 56 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04492188,  0.40234375, ..., -0.5       ,
           -0.40527344, -0.42773438]), textin=CWbytearray(b'96 50 5d 85 5d 75 a1 7b 7d e3 ab 91 99 69 f2 1d'), textout=CWbytearray(b'22 e9 f7 86 12 71 5c 1d cc e3 1b 56 7f 47 f1 e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.42089844, -0.42285156]), textin=CWbytearray(b'49 64 b4 66 b8 ba 94 42 97 37 66 81 4e 8f e9 17'), textout=CWbytearray(b'59 0b 2d c0 e4 71 7a a7 fe e6 f7 b4 ca 73 b7 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188,  0.01367188,  0.37890625, ..., -0.5       ,
           -0.39257812, -0.40234375]), textin=CWbytearray(b'b5 4f 88 00 0e 24 3a 2c 96 0a 20 e4 f2 c9 23 79'), textout=CWbytearray(b'2b 73 ac e3 81 15 ad 73 6d 7d 33 f1 9f 58 5f e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04785156,  0.38964844, ..., -0.5       ,
           -0.4140625 , -0.41796875]), textin=CWbytearray(b'f3 e2 58 28 5a d5 5d e5 cf 5c ad b7 c6 20 6d 48'), textout=CWbytearray(b'05 25 1c 18 b3 8e 39 98 2c 8c 85 f9 4b b9 bf 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19140625,  0.05566406,  0.39355469, ..., -0.5       ,
           -0.41503906, -0.41894531]), textin=CWbytearray(b'8e ed 3a 76 ca 2e f2 c8 8b 65 8b 7a 7f 69 f7 91'), textout=CWbytearray(b'5f 72 f4 3f f4 72 f9 93 2e 91 46 58 79 0c 27 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04199219,  0.3984375 , ..., -0.5       ,
           -0.41601562, -0.41894531]), textin=CWbytearray(b'a5 f3 37 bc d3 c3 81 94 87 a9 ae 0a 7c 5d d1 7c'), textout=CWbytearray(b'40 63 cf 39 e0 aa 84 eb 6e fc cb e9 14 52 af 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.40917969, -0.41503906]), textin=CWbytearray(b'5c c1 a9 bc 50 78 94 b4 2e 0d ad 46 3a c9 ea 00'), textout=CWbytearray(b'4b 3c 57 d1 08 10 c6 50 59 86 58 26 12 8f 05 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04101562,  0.37988281, ..., -0.5       ,
           -0.41796875, -0.42089844]), textin=CWbytearray(b'48 6f ac 7f b4 a5 a3 2b 3c 2a 3a c9 f5 6d 6b 40'), textout=CWbytearray(b'fc e6 10 f1 0c 1b e3 45 5a 22 1f b6 21 12 e7 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12011719, -0.00195312,  0.36425781, ..., -0.5       ,
           -0.39257812, -0.40039062]), textin=CWbytearray(b'6f 08 59 3b c3 3e 0e 71 9e dd 34 43 5a f3 72 53'), textout=CWbytearray(b'c5 50 67 6d ea e7 19 46 63 46 d3 29 62 b5 23 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03808594,  0.38476562, ..., -0.5       ,
           -0.41992188, -0.421875  ]), textin=CWbytearray(b'c5 d3 de ab fb 28 ea 51 1b 8e 20 60 f2 23 1d dd'), textout=CWbytearray(b'ad 74 7d e8 21 c7 2e 89 3c 89 5a 01 9f 0b 9e 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188, -0.00585938,  0.36621094, ..., -0.5       ,
           -0.3984375 , -0.40429688]), textin=CWbytearray(b'f8 b9 da d6 f7 a4 b4 da 9f fe e9 26 ca 8e 98 4e'), textout=CWbytearray(b'5f 88 de 40 de d4 20 be 2b 42 1b 0a 7f 72 71 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04199219,  0.40039062, ..., -0.5       ,
           -0.39648438, -0.40527344]), textin=CWbytearray(b'14 83 40 94 b2 22 da ba b0 cc 56 eb b3 00 a0 13'), textout=CWbytearray(b'2d 71 17 03 8d c3 b1 43 b3 64 77 aa a1 f6 71 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04785156,  0.40429688, ..., -0.5       ,
           -0.43164062, -0.43164062]), textin=CWbytearray(b'47 41 e1 5e 14 5f e9 24 a0 51 b0 0d 91 70 a3 3d'), textout=CWbytearray(b'da f2 59 94 a2 05 55 e8 f3 5a 98 bc 49 5f df 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.00683594,  0.37402344, ..., -0.5       ,
           -0.46972656, -0.46191406]), textin=CWbytearray(b'6c 9d 45 94 76 69 cd e1 f9 cc 35 50 58 7d cd 60'), textout=CWbytearray(b'fc fc 9e 39 0b e4 f1 df 66 e3 3e 67 7c ed 58 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02636719,  0.38671875, ..., -0.5       ,
           -0.4296875 , -0.43359375]), textin=CWbytearray(b'cd 23 49 5c e4 cc 26 3a f5 3e 8a 8f 0e 54 eb 8e'), textout=CWbytearray(b'96 da af 06 44 62 a1 88 63 96 80 f4 5b 46 93 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01953125,  0.38183594, ..., -0.5       ,
           -0.38867188, -0.3984375 ]), textin=CWbytearray(b'6e dc 54 f5 36 d4 8f 52 3f 22 ae df 8c d4 e7 88'), textout=CWbytearray(b'10 22 ce 95 19 89 da 96 ce e0 00 5d 91 e5 a4 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03613281,  0.38867188, ..., -0.5       ,
           -0.38378906, -0.39355469]), textin=CWbytearray(b'a9 d5 c6 10 72 91 e9 23 0a aa 2a ac 20 de e4 b6'), textout=CWbytearray(b'4f 7c 47 be 1a 70 4a a3 f4 b0 39 e3 4a 23 f6 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04980469,  0.40625   , ..., -0.5       ,
           -0.39160156, -0.40136719]), textin=CWbytearray(b'76 c6 38 d5 b0 f1 db c8 90 fc a3 cf 7e 77 37 94'), textout=CWbytearray(b'51 39 ed 4d ea 64 9d 12 cf 1b 3e e7 e8 46 1c 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.03613281,  0.39550781, ..., -0.5       ,
           -0.39453125, -0.40234375]), textin=CWbytearray(b'11 2e ea 18 fb 31 84 f2 82 d7 d6 02 ad 31 e2 bb'), textout=CWbytearray(b'0a bf d3 3e fa fc ee 33 ae 2c c5 02 10 8c 11 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04492188,  0.40332031, ..., -0.5       ,
           -0.41992188, -0.41992188]), textin=CWbytearray(b'04 84 9a 32 dd 4b 92 7d 39 8f 8c bb 3b 42 a9 03'), textout=CWbytearray(b'90 84 a3 08 4a 0b 15 61 89 65 d6 62 45 10 64 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04785156,  0.40429688, ..., -0.5       ,
           -0.45996094, -0.45214844]), textin=CWbytearray(b'4e 8f a3 4a 1a 5e 83 0e 80 4c 05 3d 5c dc 96 16'), textout=CWbytearray(b'e8 9a 82 89 27 a0 ce 36 6e cc 38 88 4a 50 10 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04003906,  0.40136719, ..., -0.5       ,
           -0.4140625 , -0.41894531]), textin=CWbytearray(b'b8 04 63 d7 54 ab 31 0f ad 21 a2 73 37 8b 7d da'), textout=CWbytearray(b'a4 0a 48 0c a0 49 70 ca de fa c0 b2 cf 5b 43 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.38964844, -0.40234375]), textin=CWbytearray(b'c3 f3 3a 50 53 e0 9a 31 06 44 2a 79 d7 58 97 84'), textout=CWbytearray(b'5c c8 ff c6 1f f4 30 65 13 aa a1 3e 6b 20 f6 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19433594,  0.04492188,  0.40429688, ..., -0.5       ,
           -0.421875  , -0.42480469]), textin=CWbytearray(b'c6 c2 9a ab cd e6 85 b3 28 43 06 d1 76 b4 b7 35'), textout=CWbytearray(b'91 62 2e 15 a2 c8 37 01 72 49 e2 bf ca 70 f3 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.40527344, -0.41015625]), textin=CWbytearray(b'b6 d3 66 59 ff 56 cc 84 89 a0 85 6a 66 21 cf 93'), textout=CWbytearray(b'46 97 49 48 b2 19 ec fb b2 7f ba 8c 03 b6 69 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04492188,  0.39941406, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'4e 9a 1d 01 90 68 42 a4 d1 5e af ef f2 d5 66 31'), textout=CWbytearray(b'1d 1f f0 cb d5 b3 d4 43 71 15 f6 a8 93 99 d0 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.02246094,  0.38476562, ..., -0.5       ,
           -0.42578125, -0.43066406]), textin=CWbytearray(b'c8 d6 f4 b7 fe 54 7e 42 b4 2f 2c c9 37 ed 75 47'), textout=CWbytearray(b'3e af e2 95 47 9a fd a8 91 cf a3 7f e5 61 fa 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.02246094,  0.37304688, ..., -0.5       ,
           -0.37402344, -0.38671875]), textin=CWbytearray(b'3c 5b 49 8c 65 8d dd bd 4a 76 e2 f3 19 25 3d 42'), textout=CWbytearray(b'db 98 26 c7 38 a9 51 3e 3b 15 e1 d6 ba 85 fe e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03515625,  0.39746094, ..., -0.5       ,
           -0.375     , -0.39648438]), textin=CWbytearray(b'81 3f 39 ed 1c 01 3d 8a ad 07 cc ac bf 12 54 e3'), textout=CWbytearray(b'66 b3 2f 3e 2f bc 74 cd 27 aa 41 2c 34 7b f1 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04296875,  0.40136719, ..., -0.5       ,
           -0.42285156, -0.43847656]), textin=CWbytearray(b'bc 3a a4 be 8e e6 f3 55 32 cc 9d 07 cd 33 57 d8'), textout=CWbytearray(b'd5 c0 d6 7c 0b ea b7 a7 51 9d 64 8b b9 71 df 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03515625,  0.39453125, ..., -0.5       ,
           -0.44335938, -0.44140625]), textin=CWbytearray(b'5b 2b e6 80 d3 58 b1 2b 52 e6 5c fe 57 51 83 2f'), textout=CWbytearray(b'd6 cb 7d 18 56 62 57 9d d8 e6 64 16 27 19 5f 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.01757812,  0.38183594, ..., -0.5       ,
           -0.421875  , -0.42480469]), textin=CWbytearray(b'da 4f 00 f2 3d 42 3e 05 eb 35 05 61 19 78 71 51'), textout=CWbytearray(b'f7 22 a0 b1 01 12 20 9a 56 4b 7a b6 42 20 f8 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04785156,  0.40527344, ..., -0.5       ,
           -0.39941406, -0.42285156]), textin=CWbytearray(b'b0 c4 70 69 33 1c 09 25 7b ff 96 95 b0 f2 e6 f0'), textout=CWbytearray(b'92 13 98 44 03 f7 88 10 b0 9b a9 aa f8 ad 3d be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.01367188,  0.375     , ..., -0.5       ,
           -0.44238281, -0.43847656]), textin=CWbytearray(b'85 19 5e d8 6c 47 04 2d 5f b6 1d 53 a6 ef d7 5b'), textout=CWbytearray(b'be 47 37 c2 10 b1 ab ea e5 45 97 95 49 50 be 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40136719, ..., -0.5       ,
           -0.42871094, -0.42871094]), textin=CWbytearray(b'b9 ce b8 d8 86 47 dc 17 2c 0f 7d 43 49 91 ce 51'), textout=CWbytearray(b'36 26 90 03 5f 5b ec 3d 91 36 b7 8b 41 d0 61 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.03320312,  0.39453125, ..., -0.5       ,
           -0.38183594, -0.39257812]), textin=CWbytearray(b'73 64 ef 09 fd 81 68 70 04 9f 47 ca db a5 8a b8'), textout=CWbytearray(b'c6 62 17 a9 3b fa 73 f1 ce 50 a5 35 f7 d3 e7 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02148438,  0.37207031, ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'7f 4f 44 ce 13 0c 5e 1a 68 28 bf c9 db 4a ae ab'), textout=CWbytearray(b'49 23 a2 80 f0 b4 c5 9e b2 7e 38 6e 34 2f 35 a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.0390625 ,  0.39648438, ..., -0.5       ,
           -0.40820312, -0.4140625 ]), textin=CWbytearray(b'26 62 22 e8 16 3d ba 52 2a 98 6a 6a f6 20 ef ad'), textout=CWbytearray(b'a3 b7 96 fd eb fa df 62 da fe 37 4e 5e 7c b7 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04003906,  0.40039062, ..., -0.5       ,
           -0.375     , -0.38671875]), textin=CWbytearray(b'94 f6 db 94 2b bc c0 43 c7 60 2a fd 4a 48 d0 a9'), textout=CWbytearray(b'fa f0 7a df c7 2e 19 fb 77 2c 17 88 63 d0 14 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.39941406, ..., -0.5       ,
           -0.37988281, -0.39257812]), textin=CWbytearray(b'9c 91 9d 31 50 c9 85 c2 5f 8a c7 fd e3 e7 5b 24'), textout=CWbytearray(b'15 1e e4 d2 e5 68 e8 6b 5c 08 6d 3d e1 a3 ff 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.02539062,  0.38964844, ..., -0.5       ,
           -0.39746094, -0.40722656]), textin=CWbytearray(b'59 10 b8 59 e9 af f0 bb 0c e1 dc a1 62 6e a4 b7'), textout=CWbytearray(b'91 1f 18 c7 cd df 09 a5 06 15 48 6d 32 6d 6a 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.046875  ,  0.40527344, ..., -0.5       ,
           -0.44042969, -0.4375    ]), textin=CWbytearray(b'dc 09 b9 bd a7 5b 68 14 5f 82 f9 14 bd ae 04 8d'), textout=CWbytearray(b'06 20 6f be 77 59 ab ee a1 a8 b7 27 af 25 12 fd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.02246094,  0.3828125 , ..., -0.5       ,
           -0.39648438, -0.40429688]), textin=CWbytearray(b'70 c1 00 68 db bd 3b 82 d5 99 39 6f 0b 87 98 7e'), textout=CWbytearray(b'1c b7 d2 41 36 6d ac 62 82 72 67 47 cb bc 57 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.39160156, ..., -0.5       ,
           -0.42675781, -0.42675781]), textin=CWbytearray(b'15 f3 5a 61 0d 68 cb 51 cd 79 bf ac f6 6f 8d 33'), textout=CWbytearray(b'5b 8a 49 56 0f f4 16 18 3f f6 8e 52 5d 16 03 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18945312,  0.0546875 ,  0.39453125, ..., -0.5       ,
           -0.46679688, -0.45703125]), textin=CWbytearray(b'b3 10 31 8a 1e 61 69 c2 ad 35 ec 75 e8 f5 c2 4a'), textout=CWbytearray(b'49 2d 90 e5 b3 34 d0 3e 10 09 80 a3 c3 02 ce ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01953125,  0.3828125 , ..., -0.5       ,
           -0.37792969, -0.390625  ]), textin=CWbytearray(b'56 fc 90 98 ba e5 92 78 67 b6 84 e5 05 e0 36 6d'), textout=CWbytearray(b'fa 92 3b 9d c6 15 44 49 8c f0 2a e2 06 9f 1b c2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.01757812,  0.36523438, ..., -0.5       ,
           -0.45410156, -0.45117188]), textin=CWbytearray(b'7d 80 42 98 b8 9b b6 91 73 67 37 8a 0f 5d a3 e2'), textout=CWbytearray(b'87 3d 5a 1b 13 0b 7e 41 f1 d1 d7 f9 b5 6c 78 b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04101562,  0.3828125 , ..., -0.5       ,
           -0.421875  , -0.42285156]), textin=CWbytearray(b'7d a1 18 c6 06 f9 99 d7 38 05 c2 41 13 c4 7c 40'), textout=CWbytearray(b'3a 8d 0a a5 0e 08 80 7b da 7f ee 4c 8a 0c 2c 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12695312,  0.00683594,  0.37304688, ..., -0.5       ,
           -0.39257812, -0.41015625]), textin=CWbytearray(b'5a 98 38 53 5c 47 b4 f0 b2 6a 4c b8 c2 3d 89 6c'), textout=CWbytearray(b'98 8e 1a 20 42 18 8a 64 c5 97 1a b5 a4 51 1a a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04785156,  0.40234375, ..., -0.5       ,
           -0.4609375 , -0.45410156]), textin=CWbytearray(b'fb a5 0b 16 52 c7 62 3a 01 07 ba 49 95 ae ae 35'), textout=CWbytearray(b'42 fc 2c 48 b9 b6 29 22 1f 74 51 e4 b9 b7 c4 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.06152344,  0.39453125, ..., -0.5       ,
           -0.41601562, -0.41796875]), textin=CWbytearray(b'bf b6 92 a0 e7 3c 2d 52 4b 99 fb d3 b4 c4 a3 e3'), textout=CWbytearray(b'b4 3b 9c c4 31 36 33 5b 38 57 ce 5e 66 f3 8e 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.02734375,  0.38867188, ..., -0.5       ,
           -0.44433594, -0.44140625]), textin=CWbytearray(b'13 15 c0 7a df 99 c6 aa f1 96 33 c6 69 36 2b 8f'), textout=CWbytearray(b'cd 4b 02 4f 29 99 f8 58 59 9f 53 2a d6 66 92 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.0234375 ,  0.3828125 , ..., -0.5       ,
           -0.41113281, -0.42675781]), textin=CWbytearray(b'e1 63 9e 62 89 80 f2 84 23 89 27 cf 03 54 4a 07'), textout=CWbytearray(b'29 9c a3 7c 56 bf c7 85 bb af ad 6c bf e8 50 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.02636719,  0.38574219, ..., -0.5       ,
           -0.421875  , -0.42578125]), textin=CWbytearray(b'12 6f ec c4 fa 58 4f aa 06 74 bb b9 da bb 46 45'), textout=CWbytearray(b'a1 ca b3 41 5e 81 63 ac 33 57 59 f0 e3 dd 13 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.0234375 ,  0.38769531, ..., -0.5       ,
           -0.42480469, -0.42578125]), textin=CWbytearray(b'eb 6d a6 35 9f c6 ab 26 95 a5 f5 7e fa d1 93 ef'), textout=CWbytearray(b'8f 6a 60 68 ca 4e 44 14 02 31 fa 2e 22 6e fc 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.02246094,  0.38574219, ..., -0.5       ,
           -0.39160156, -0.40039062]), textin=CWbytearray(b'9e 4c 7c 0a ec b2 aa b0 b9 e3 c9 cb 29 d0 7c 9e'), textout=CWbytearray(b'6d e9 25 3b 3e 7d cd c6 fd a9 10 7a 2c be 74 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15136719,  0.01074219,  0.375     , ..., -0.5       ,
           -0.43847656, -0.43945312]), textin=CWbytearray(b'cb 8b 92 27 d1 8e ce 1c 62 e0 13 cf 18 c8 ae ee'), textout=CWbytearray(b'8b e4 01 81 53 c7 31 b0 61 49 a5 38 14 02 7e 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05273438,  0.40820312, ..., -0.5       ,
           -0.4140625 , -0.41796875]), textin=CWbytearray(b'a0 3d 3f 5f 83 48 83 6e 7e 0f 0d 7e ae a0 aa 55'), textout=CWbytearray(b'c2 b4 fe 51 f0 10 ca 16 7f 33 a4 13 1e 07 b1 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11523438, -0.00683594,  0.36621094, ..., -0.5       ,
           -0.36328125, -0.37988281]), textin=CWbytearray(b'af 0f 7c 50 e4 10 60 4e 3f 16 93 50 5c d9 9c cf'), textout=CWbytearray(b'f4 79 96 c6 7e 72 29 42 e8 a7 16 a4 cb 84 4a 21'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04492188,  0.40039062, ..., -0.5       ,
           -0.45703125, -0.45214844]), textin=CWbytearray(b'22 41 d9 40 77 fa 7c 79 4b 47 18 80 92 60 e6 e5'), textout=CWbytearray(b'1e 26 cf d4 ad 59 3c 30 ab f7 56 d2 fd 85 a3 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.38769531, -0.40234375]), textin=CWbytearray(b'63 b0 ff e0 6a 0b 52 cd 61 33 61 cb 5f 5b 64 93'), textout=CWbytearray(b'cd b8 82 87 6d 41 06 91 d0 03 6d cb 9b 29 9b 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03710938,  0.39355469, ..., -0.5       ,
           -0.43945312, -0.44921875]), textin=CWbytearray(b'b7 75 6b 04 1e 75 13 fb 7c 6a 84 97 e5 68 6a 66'), textout=CWbytearray(b'9c a3 cb 13 89 0e aa ad ce de 1b 07 75 e4 ad 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.140625  ,  0.015625  ,  0.37988281, ..., -0.5       ,
           -0.41796875, -0.41992188]), textin=CWbytearray(b'5c 88 5b 84 14 7a 8a 28 bb 9f c8 b6 19 ec 0c 24'), textout=CWbytearray(b'81 ea a4 ff 9e 18 32 3e 49 be 67 f0 19 08 07 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13378906,  0.00390625,  0.37109375, ..., -0.5       ,
           -0.40332031, -0.40820312]), textin=CWbytearray(b'64 94 af c9 c4 7a df fa 40 77 6e 2c 45 99 01 6f'), textout=CWbytearray(b'b8 af 52 d1 a5 12 dc c9 94 88 59 db 47 30 09 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.38964844, -0.39941406]), textin=CWbytearray(b'eb 4a 84 3e 52 53 2d 33 d4 a3 24 09 63 5d de f9'), textout=CWbytearray(b'5f 33 55 ab 71 f2 27 3d 48 c4 0f 08 87 fe 37 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.01367188,  0.37695312, ..., -0.5       ,
           -0.38867188, -0.3984375 ]), textin=CWbytearray(b'5f 1b fb 0b 46 fe 77 49 d8 d9 df 31 e1 47 c8 2f'), textout=CWbytearray(b'41 23 ef c8 4f 02 7f 5f b7 d5 d9 b5 4e 62 3d 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04785156,  0.40820312, ..., -0.5       ,
           -0.40917969, -0.42480469]), textin=CWbytearray(b'f5 2b a2 1c 4f 8a 33 97 5c d6 c5 ed d1 e1 c0 18'), textout=CWbytearray(b'65 31 45 e1 a1 ff f1 5d f0 c7 4a 5a 7f 76 46 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19433594,  0.05664062,  0.40917969, ..., -0.5       ,
           -0.421875  , -0.42382812]), textin=CWbytearray(b'26 eb 1f 30 71 4f 5a f8 cd 22 8c f3 11 1a 86 81'), textout=CWbytearray(b'5c 38 66 9c c9 10 dc 38 8b 69 ed 89 99 e0 42 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04980469,  0.40625   , ..., -0.5       ,
           -0.39355469, -0.40722656]), textin=CWbytearray(b'0a 27 73 cb 57 91 a5 f6 d1 ef ea e0 1a 28 16 04'), textout=CWbytearray(b'f6 25 69 ee a1 ef 65 77 14 56 ca 19 46 8f f3 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03417969,  0.39257812, ..., -0.5       ,
           -0.40917969, -0.42675781]), textin=CWbytearray(b'53 2a ac fa b1 0a 4f 78 ed e2 22 83 77 f7 1c c9'), textout=CWbytearray(b'e9 93 47 b9 da 7b 70 2b 4d 63 9d 8d 76 81 29 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03417969,  0.39550781, ..., -0.5       ,
           -0.40136719, -0.42285156]), textin=CWbytearray(b'97 ff 5e 67 c9 20 da 88 1c 99 28 49 f4 87 ac 99'), textout=CWbytearray(b'7b cc b4 a1 ba fb 85 2f 95 9b e0 22 82 36 b0 ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.02734375,  0.38867188, ..., -0.5       ,
           -0.43359375, -0.43457031]), textin=CWbytearray(b'5e d4 f0 a2 7b 7a 07 3a e6 88 b6 d4 97 0b f5 1b'), textout=CWbytearray(b'82 5b 54 11 5f a9 a4 9a 2d 8f d4 af de a7 6e cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.00878906,  0.375     , ..., -0.5       ,
           -0.40234375, -0.40917969]), textin=CWbytearray(b'b8 f6 bf 97 59 03 39 37 78 ba 94 42 a8 65 23 7b'), textout=CWbytearray(b'a4 f5 d2 98 f0 16 48 87 ab 31 3e da 70 dc e4 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02636719,  0.38671875, ..., -0.5       ,
           -0.40722656, -0.4140625 ]), textin=CWbytearray(b'2d 11 e4 eb e1 e8 06 5d 75 98 40 3c 56 29 cf a8'), textout=CWbytearray(b'0c 48 92 08 e9 ec ef 0c cf 87 96 c3 c6 73 f6 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.39257812, -0.40039062]), textin=CWbytearray(b'd1 4f 3c a7 37 5e fd 4a 3d c8 3a bb 8d 8f 7a 39'), textout=CWbytearray(b'49 d6 dd 2f 3e 53 f3 04 e0 8a 34 0c f0 bb ae f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.41308594, -0.41503906]), textin=CWbytearray(b'72 62 d6 b3 8e 1b 7d 89 50 56 a7 d5 02 90 d0 d0'), textout=CWbytearray(b'd6 e6 31 6a 9d db d8 b1 6b f7 b1 7c 46 c5 2c 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03320312,  0.39355469, ..., -0.5       ,
           -0.38769531, -0.3984375 ]), textin=CWbytearray(b'19 3d dc 22 e3 a3 59 7a 03 50 fd 41 7c 97 e0 97'), textout=CWbytearray(b'63 4a 83 05 da 8c b7 da 6a a5 08 04 9e 8a 15 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.04199219,  0.40136719, ..., -0.5       ,
           -0.39453125, -0.40429688]), textin=CWbytearray(b'a4 1d ba 5d bf 37 aa 78 6a 55 10 fd c1 94 b6 25'), textout=CWbytearray(b'54 43 e8 5a e7 6f c0 1c 93 81 78 c5 c5 0e 5a 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.45605469, -0.45117188]), textin=CWbytearray(b'83 4a 00 24 09 63 52 32 e3 6c 51 e6 79 3c 82 06'), textout=CWbytearray(b'c2 7f 89 ad 62 0b 1d b0 61 c6 a0 6d 48 2d 9d 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.40820312, -0.4140625 ]), textin=CWbytearray(b'38 6a 6d 76 20 aa 25 36 c6 bd 04 b0 ea d3 26 69'), textout=CWbytearray(b'ae 64 60 27 bf f0 0b a9 88 75 e8 52 e2 7f 78 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.40429688, -0.41210938]), textin=CWbytearray(b'7d 37 4e 56 95 be 40 78 18 6a e9 fb 6c 47 c9 5b'), textout=CWbytearray(b'7a 00 90 c4 19 31 64 f6 ff 90 18 eb ab f5 16 af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.02636719,  0.38964844, ..., -0.5       ,
           -0.39550781, -0.40429688]), textin=CWbytearray(b'b9 18 9f 5c ee 97 cd bf 29 3b a8 fc 7c cb 7d 4c'), textout=CWbytearray(b'da 89 ce 12 7d f3 94 94 8c 69 e4 14 dd 90 a5 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.42285156, -0.42382812]), textin=CWbytearray(b'43 9c 79 0d a1 c0 0b 85 11 31 39 48 6c c1 d8 53'), textout=CWbytearray(b'cf 63 b7 7d 78 e3 4f 48 48 95 ed a2 d5 2f f1 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.04882812,  0.39160156, ..., -0.5       ,
           -0.40820312, -0.41308594]), textin=CWbytearray(b'05 19 2e 4d 4b 7f 9c be cc be bb 63 be d2 66 bf'), textout=CWbytearray(b'3d 10 43 09 ec b1 5e 8b 84 c8 c4 b8 13 42 df ca'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.39453125, -0.40332031]), textin=CWbytearray(b'5b 1e 2f b4 f0 bf 9a b3 3c 73 73 41 e0 74 94 c4'), textout=CWbytearray(b'5e c2 35 a7 47 a1 c5 59 4e 0a b2 bb 61 4c cd 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.02050781,  0.3828125 , ..., -0.5       ,
           -0.43847656, -0.43945312]), textin=CWbytearray(b'10 2d c2 e7 14 db 85 82 d5 c7 84 b7 71 f0 0d 68'), textout=CWbytearray(b'48 b8 8c cb 98 ef 17 50 08 eb b3 12 0e 12 84 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.02734375,  0.38964844, ..., -0.5       ,
           -0.40429688, -0.41015625]), textin=CWbytearray(b'8d 4d c3 d5 a4 4a d8 8f 14 24 2d 7c 14 69 97 ca'), textout=CWbytearray(b'da 29 af f3 54 0b 08 c3 33 70 fa e2 c0 39 2e ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.02246094,  0.38574219, ..., -0.5       ,
           -0.39160156, -0.40234375]), textin=CWbytearray(b'c5 a8 20 93 b7 22 36 04 77 ee 2f e5 a4 99 81 7f'), textout=CWbytearray(b'e5 70 37 31 53 7f b0 9c f9 5a 43 3f 5b 9d df a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.39257812, -0.40234375]), textin=CWbytearray(b'78 5b b3 7e e8 0d 4e 3c 94 c8 81 d3 e2 94 99 2a'), textout=CWbytearray(b'52 61 c6 08 20 30 e7 57 c6 b0 e6 1b 00 da b6 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11621094, -0.00585938,  0.36230469, ..., -0.5       ,
           -0.40039062, -0.40722656]), textin=CWbytearray(b'7b 5c 68 03 45 ea 58 7e 3e 58 79 0c 74 cc 8e 1e'), textout=CWbytearray(b'c5 d8 1d 9c e8 cf 32 5f 18 b5 6d a4 cf 96 b2 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03710938,  0.39746094, ..., -0.5       ,
           -0.44433594, -0.44238281]), textin=CWbytearray(b'02 8b 39 6b df 4f 24 5a 6c 25 41 a5 40 1d fe f2'), textout=CWbytearray(b'61 ba 44 6e 07 fa c4 52 69 8d c2 8a 03 f9 6d fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.40039062, ..., -0.5       ,
           -0.43066406, -0.43066406]), textin=CWbytearray(b'21 1a 31 12 7b ea 73 22 13 33 8d b8 c7 96 bd 52'), textout=CWbytearray(b'32 06 b2 d5 f7 ab 15 ad f2 b6 a3 83 95 33 14 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03515625,  0.37890625, ..., -0.5       ,
           -0.43457031, -0.43164062]), textin=CWbytearray(b'19 dc 96 8e 6f da 86 f2 11 1b 80 6e eb d4 5a ce'), textout=CWbytearray(b'5e ee 33 e7 b8 55 21 43 7f de bb b2 fb 41 15 d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.04101562,  0.39648438, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'27 77 1a 24 55 c8 5c 31 54 d4 df 4a e8 9a 55 16'), textout=CWbytearray(b'31 09 2d d1 8d 2f 6c e3 4c 12 be 30 3c 0b 7e a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.0546875 ,  0.40917969, ..., -0.5       ,
           -0.39648438, -0.41113281]), textin=CWbytearray(b'd0 ef 7b ca 3c 0a 18 ef d7 d2 1f 20 c2 83 fb da'), textout=CWbytearray(b'1a 92 e2 d9 b0 86 9c 20 6c 9f b1 b0 bb d3 18 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0234375 ,  0.38867188, ..., -0.5       ,
           -0.41308594, -0.41894531]), textin=CWbytearray(b'24 2d c6 5c 24 67 15 10 37 8b 98 84 aa d5 45 03'), textout=CWbytearray(b'79 2d 30 da ca 7b dc bb d6 4d 2c df 3f 10 a0 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14257812,  0.015625  ,  0.37890625, ..., -0.5       ,
           -0.43359375, -0.4375    ]), textin=CWbytearray(b'2d 9d 9c c9 26 ce c3 4c c6 30 65 9d e0 ee a6 10'), textout=CWbytearray(b'de d7 2c b3 91 fc ec 81 19 7f f5 65 05 f1 6d d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.41015625, -0.41503906]), textin=CWbytearray(b'96 5a db fd 99 72 d1 27 14 16 58 f4 36 aa f7 ea'), textout=CWbytearray(b'a5 19 3b 7e 38 d9 23 71 fd f4 38 65 05 31 bb 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13476562,  0.0078125 ,  0.37695312, ..., -0.5       ,
           -0.40527344, -0.41015625]), textin=CWbytearray(b'49 64 9f e5 c2 e8 b2 75 4d 05 4b fe b2 5b 46 ec'), textout=CWbytearray(b'bf 06 24 94 7e 5e 68 f2 06 97 aa c5 04 99 38 4b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.43945312, -0.43847656]), textin=CWbytearray(b'a1 da 3b 30 a6 c1 dc 9a 14 ef 17 5e 6d a9 49 a7'), textout=CWbytearray(b'3f ee 8f a7 e8 09 e1 56 fd 82 90 da 1b 7f 3f 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04589844,  0.40429688, ..., -0.5       ,
           -0.40917969, -0.42773438]), textin=CWbytearray(b'f6 f1 61 1e d7 af 2d b4 ce 4e 37 92 ef 5d e2 77'), textout=CWbytearray(b'e5 11 a6 75 26 9c d4 67 29 33 87 21 c5 f9 d6 8f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03125   ,  0.390625  , ..., -0.5       ,
           -0.40234375, -0.40625   ]), textin=CWbytearray(b'0f de c9 95 2d 80 22 63 4f a6 5e 2e 55 0d 69 b3'), textout=CWbytearray(b'3b 26 6e de 93 ea b0 72 8d 65 9d 1f 43 03 76 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04589844,  0.40136719, ..., -0.5       ,
           -0.41601562, -0.43457031]), textin=CWbytearray(b'a5 13 08 c5 de d3 d8 b1 c6 d3 60 03 c8 06 68 ee'), textout=CWbytearray(b'0c c8 0b be 72 23 b0 d0 38 0e 09 e7 a5 c0 80 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.015625  ,  0.37890625, ..., -0.5       ,
           -0.40625   , -0.41113281]), textin=CWbytearray(b'e4 72 15 34 d6 1c f0 83 85 10 a3 fe a0 2e 88 09'), textout=CWbytearray(b'cb dc 3e dc bb 50 c3 f1 96 56 3b dc ab 86 49 a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04199219,  0.3984375 , ..., -0.5       ,
           -0.39550781, -0.40332031]), textin=CWbytearray(b'98 96 d7 6c 33 1f 44 42 04 73 61 04 c9 32 cc 76'), textout=CWbytearray(b'b5 3e 7c d5 b3 51 c7 f3 b6 2e 8c 4c 5d c8 09 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03515625,  0.39355469, ..., -0.5       ,
           -0.42382812, -0.44238281]), textin=CWbytearray(b'd5 9f 38 1a d7 14 79 be 03 1c 7e 07 90 88 8e 09'), textout=CWbytearray(b'ce aa ac 53 ff 1c f3 a2 82 84 4c 33 b3 2b a1 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.02734375,  0.375     , ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'42 28 6d 0b 7f 06 c5 4f 30 64 42 8e 21 c5 d9 37'), textout=CWbytearray(b'95 b1 8d ee 94 91 9d 60 01 bb 75 da 18 f5 e8 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.0234375 ,  0.38671875, ..., -0.5       ,
           -0.37890625, -0.40722656]), textin=CWbytearray(b'67 80 b4 57 66 ef 97 f8 0a 88 28 3f 8c 4d 16 e6'), textout=CWbytearray(b'd8 f1 34 63 fa e6 29 33 bf b0 15 29 4b 37 ac 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 ,  0.0078125 ,  0.375     , ..., -0.5       ,
           -0.37207031, -0.39941406]), textin=CWbytearray(b'90 51 5a c8 4e b6 4e 14 39 23 88 d0 13 fa 74 8f'), textout=CWbytearray(b'92 c3 bf 64 2a 6e 01 36 2b f1 63 c0 66 37 62 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03027344,  0.39257812, ..., -0.5       ,
           -0.43261719, -0.43261719]), textin=CWbytearray(b'3c 94 47 1a de 4e 55 64 bc 32 86 f0 df 50 79 e9'), textout=CWbytearray(b'af d6 33 c1 6b 13 db 05 63 0c a1 ad 94 8a db f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04589844,  0.38476562, ..., -0.5       ,
           -0.421875  , -0.42578125]), textin=CWbytearray(b'fb 16 32 ce a8 35 15 b2 c2 67 1a 98 1c ff e9 fc'), textout=CWbytearray(b'70 bf 04 0e fd fe d2 f5 8f b4 fc 97 04 6b f0 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04882812,  0.40332031, ..., -0.5       ,
           -0.4375    , -0.4375    ]), textin=CWbytearray(b'53 98 51 df fa cf ad 1f f6 31 27 50 37 5f 9b fb'), textout=CWbytearray(b'90 1d 6d 8c a6 bd 5c 4b a2 e4 d1 a1 fd 0b 54 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04394531,  0.40039062, ..., -0.5       ,
           -0.40429688, -0.41113281]), textin=CWbytearray(b'4d 15 6d 16 46 39 7a 32 60 b1 4c 37 6a d6 7e 42'), textout=CWbytearray(b'01 39 ee 49 20 fc 9c 7d a6 3d 8e 89 55 3e 27 fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.03027344,  0.39355469, ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'8e e1 00 30 b8 68 9c 92 87 06 74 84 db 93 78 e8'), textout=CWbytearray(b'63 18 f2 69 71 6b 95 ec 56 ee 37 a5 37 c6 62 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39648438, ..., -0.5       ,
           -0.4140625 , -0.41894531]), textin=CWbytearray(b'cb 01 ac 9f 0e 50 05 a2 f1 c0 c9 74 66 1a b4 4b'), textout=CWbytearray(b'39 30 f8 b6 36 c8 e6 df ac 7e d0 50 39 e3 98 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03515625,  0.39453125, ..., -0.5       ,
           -0.44921875, -0.44335938]), textin=CWbytearray(b'83 6c 27 42 a0 29 06 9b 0e 7a 4b 27 4f 27 da 7a'), textout=CWbytearray(b'66 61 f5 9f 93 ae 50 c7 83 ad 78 c1 40 87 e6 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15722656,  0.015625  ,  0.37402344, ..., -0.5       ,
           -0.40136719, -0.40820312]), textin=CWbytearray(b'4a 22 08 9b 52 8a 7f a6 e4 7b 9e 2b 9d ac c7 50'), textout=CWbytearray(b'0d 8a c5 58 3c 2c 3a 11 ab ac 67 01 d4 43 d2 fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.38476562, -0.39746094]), textin=CWbytearray(b'12 af 7b 5d d0 d0 21 6f 7a 69 ec 84 42 93 24 77'), textout=CWbytearray(b'4e 90 6b 07 61 b5 4c 71 4e 4f ce 5c 09 7e ff f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02734375,  0.38867188, ..., -0.5       ,
           -0.42285156, -0.42675781]), textin=CWbytearray(b'43 87 da 8a bf 0a 9b c6 76 a8 37 65 bf 8e 3b a9'), textout=CWbytearray(b'5f b2 7d 57 17 f8 7a 3c 49 56 f8 a5 f2 7d 08 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.0234375 ,  0.38085938, ..., -0.5       ,
           -0.44238281, -0.43847656]), textin=CWbytearray(b'12 49 4d 90 ef 06 76 d0 58 4b 6f 78 3b 72 33 37'), textout=CWbytearray(b'6b bc 15 7e c3 5b 62 cc e6 57 00 80 29 bf d6 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.04980469,  0.38769531, ..., -0.5       ,
           -0.421875  , -0.42382812]), textin=CWbytearray(b'01 af fb 07 26 45 75 69 cf fd ef 96 ba f3 a3 4b'), textout=CWbytearray(b'fe 11 23 48 59 d4 aa 52 2f ab 9b 4a 04 95 87 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03710938,  0.39648438, ..., -0.5       ,
           -0.40625   , -0.42382812]), textin=CWbytearray(b'42 9a f2 66 38 b3 81 1a 95 7c b9 3b bb 04 e4 44'), textout=CWbytearray(b'35 22 cf e3 91 8a 6b 4f 78 3c d0 7c c8 f4 22 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40332031, -0.40917969]), textin=CWbytearray(b'36 cc a5 6a e8 ed 36 59 8f 71 21 ec 41 3d 0a b3'), textout=CWbytearray(b'be 9d 3d cf f5 a1 84 c9 dd d5 71 23 fd 3b 52 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.03320312,  0.39746094, ..., -0.5       ,
           -0.39941406, -0.40820312]), textin=CWbytearray(b'74 cc 2b 97 d7 0e ef 8a 4d f0 39 5c 65 48 02 d4'), textout=CWbytearray(b'3f 9a 0b 07 a2 19 ef c8 75 b1 5e db fd 46 20 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03808594,  0.39550781, ..., -0.5       ,
           -0.4140625 , -0.43066406]), textin=CWbytearray(b'6f c7 48 23 0f a5 da 1b 56 76 91 e6 56 12 c7 7b'), textout=CWbytearray(b'ff 18 f8 7d c8 fd a6 28 94 b6 a5 7c aa 68 61 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.39453125, ..., -0.5       ,
           -0.44238281, -0.43945312]), textin=CWbytearray(b'0e a1 a7 4d 0e b5 58 1c 62 27 fa c7 2f ae ac a4'), textout=CWbytearray(b'd1 ea d4 cd bf 48 2f 11 4c 04 8a a2 3e 30 9b a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18945312,  0.04785156,  0.39746094, ..., -0.5       ,
           -0.40625   , -0.41308594]), textin=CWbytearray(b'8d 2e 01 ff a9 84 3e 83 1e 58 dd 4f aa 5f b4 f7'), textout=CWbytearray(b'6f e9 db 74 bd cc 07 64 64 ad a1 d3 57 b3 90 c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03515625,  0.39453125, ..., -0.5       ,
           -0.3984375 , -0.40625   ]), textin=CWbytearray(b'15 6f 55 86 ad ff f3 30 7b a3 de 6f c5 b2 eb 1c'), textout=CWbytearray(b'e4 ff 2b 66 f7 fb db f6 5f 73 39 eb 39 ec d4 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.00878906,  0.37695312, ..., -0.5       ,
           -0.37597656, -0.38964844]), textin=CWbytearray(b'5f 23 c9 93 97 d0 fd 19 b0 da 99 64 6b 08 6a 99'), textout=CWbytearray(b'3a 8b 0a 98 87 b6 9a bc 52 17 ca 29 3e 5e 81 f9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.0234375 ,  0.38769531, ..., -0.5       ,
           -0.39648438, -0.41992188]), textin=CWbytearray(b'b2 58 ad c6 66 cc c9 9e ed 70 63 7b bd 51 54 84'), textout=CWbytearray(b'be f4 3f bc 04 52 7b 4a 8e 5d 96 48 bd ca 90 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01757812,  0.37011719, ..., -0.5       ,
           -0.3984375 , -0.40625   ]), textin=CWbytearray(b'78 2f 10 14 bd 35 ea e5 77 53 b8 5a 49 45 91 03'), textout=CWbytearray(b'5e 26 6c c0 9f 22 22 34 4f a1 92 a7 18 e7 ae 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.02148438,  0.37011719, ..., -0.5       ,
           -0.39550781, -0.40429688]), textin=CWbytearray(b'1d ae 34 e2 79 87 b7 d3 c4 ea de 80 ac 3a 1a 5f'), textout=CWbytearray(b'77 04 60 b9 fd 18 fa e9 ab 18 3d 7a ae 78 62 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.38476562, ..., -0.5       ,
           -0.39550781, -0.40429688]), textin=CWbytearray(b'78 48 76 14 4b f5 26 17 37 cc 6c 19 16 28 b6 2d'), textout=CWbytearray(b'e5 4f bc bc 10 18 dd 69 c3 39 f0 af 8a fd a3 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.39941406, ..., -0.5       ,
           -0.4375    , -0.4375    ]), textin=CWbytearray(b'60 05 c1 00 be 3a 4f ca 1b e6 e5 18 cc e2 6f 6b'), textout=CWbytearray(b'd2 2b 24 bc be d1 34 84 73 69 98 95 71 37 e8 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.39648438, ..., -0.5       ,
           -0.3828125 , -0.39550781]), textin=CWbytearray(b'59 df 2e 77 2a e9 b0 d7 24 7b 11 72 67 1e 24 54'), textout=CWbytearray(b'66 c6 cd 48 83 7b 4e 4c d0 37 ef fc 12 8c 1a c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.40917969, -0.41699219]), textin=CWbytearray(b'cd 99 5f 4a 3a 5d 91 b9 e5 eb 36 47 6d 2b eb 52'), textout=CWbytearray(b'f2 f1 1d b7 a7 e9 55 00 71 66 3e e1 d7 b8 44 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.02832031,  0.38964844, ..., -0.5       ,
           -0.39160156, -0.40234375]), textin=CWbytearray(b'a2 a9 7b 33 9a 97 43 4c 6d 23 a4 08 33 68 df a6'), textout=CWbytearray(b'72 ff 87 ae 6a b6 bf ac fe 74 1f 4d 71 70 cb da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02539062,  0.38574219, ..., -0.5       ,
           -0.39453125, -0.40429688]), textin=CWbytearray(b'ea 35 49 bf b6 88 4b 30 85 b8 30 a0 51 92 e8 18'), textout=CWbytearray(b'bb 45 ef a6 6b 57 d4 9b e5 06 3d ae 16 a1 d4 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04882812,  0.40429688, ..., -0.5       ,
           -0.41015625, -0.41699219]), textin=CWbytearray(b'56 aa e0 38 63 f2 f0 27 a2 5c 98 05 3f c3 ec 0c'), textout=CWbytearray(b'5e b8 c5 e9 c8 e8 50 19 ac 4f 26 d0 e4 a1 c3 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.0234375 ,  0.38867188, ..., -0.5       ,
           -0.37109375, -0.38671875]), textin=CWbytearray(b'dc 5d 30 3e 94 35 d3 a3 50 2c 19 75 22 73 5f ac'), textout=CWbytearray(b'f0 9d c4 e4 4b 08 15 53 00 6b 01 23 af 86 eb 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03710938,  0.39648438, ..., -0.5       ,
           -0.38671875, -0.39648438]), textin=CWbytearray(b'c6 fd 56 49 e2 35 20 b0 de 22 d7 85 ba d5 e3 b5'), textout=CWbytearray(b'd2 97 f8 0d a2 8d 73 3d 08 5e 49 f3 78 f6 ab d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.01953125,  0.36425781, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'ac bd c9 f6 a5 ef 0b 9a 89 db 59 8f b4 4a 0a 7f'), textout=CWbytearray(b'17 76 5c 70 7f 55 3e 65 dd 67 e4 69 de 1f 58 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03222656,  0.39257812, ..., -0.5       ,
           -0.38476562, -0.40429688]), textin=CWbytearray(b'a4 cd d1 a8 94 e4 b7 32 f7 5b 49 46 d2 1a 8c 78'), textout=CWbytearray(b'8a b0 5d 42 84 b3 3e d1 5d c6 85 5b 34 07 6e 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.38476562, ..., -0.5       ,
           -0.40527344, -0.41113281]), textin=CWbytearray(b'd3 45 2d ed 18 ab f3 2b 39 d8 0c 48 76 87 0a 9b'), textout=CWbytearray(b'8b 29 d9 4f 68 36 24 22 cb 89 56 32 64 68 0f 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04394531,  0.40136719, ..., -0.5       ,
           -0.39941406, -0.40917969]), textin=CWbytearray(b'4a 5d 97 7c 71 62 9e b6 df 29 35 70 d8 bf 20 33'), textout=CWbytearray(b'ec 65 e9 ed 64 08 f4 7f e5 9e df f2 f2 c0 65 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.05273438,  0.40625   , ..., -0.5       ,
           -0.3984375 , -0.42285156]), textin=CWbytearray(b'1b 4c 8e a0 2a 93 c9 e6 ba 1a 28 44 4a 0d b2 91'), textout=CWbytearray(b'3c f4 ed 72 fa 20 44 70 18 80 cd 0f 8d 64 25 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02636719,  0.38769531, ..., -0.5       ,
           -0.37792969, -0.39453125]), textin=CWbytearray(b'd1 c3 99 9c 63 3e 59 c0 62 f6 58 b2 31 14 61 53'), textout=CWbytearray(b'e5 b2 06 5d 47 7f 80 76 ac 30 65 36 8a d4 e5 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.01953125,  0.38183594, ..., -0.5       ,
           -0.39550781, -0.40625   ]), textin=CWbytearray(b'ec 11 e7 c2 84 50 31 b1 f5 95 4e 88 04 0e 06 15'), textout=CWbytearray(b'a9 07 cc c7 5b c6 38 1b b1 b1 0a 13 b9 73 5d d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.41113281, -0.41699219]), textin=CWbytearray(b'26 e8 76 68 e0 fe 53 1c 7c 6d 88 b0 06 03 dc e7'), textout=CWbytearray(b'81 3d e9 0c ae 22 fc c9 f5 cb be 87 14 b9 2f a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04589844,  0.40429688, ..., -0.5       ,
           -0.40722656, -0.43164062]), textin=CWbytearray(b'96 ff 8a c1 c2 c0 95 ab a6 7d 10 0f eb 23 e2 d2'), textout=CWbytearray(b'bb 07 06 3a 5e 16 f9 67 10 0e 5b 1a 86 5c 3d 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.40820312, -0.41992188]), textin=CWbytearray(b'd1 45 0e 01 43 b2 0f bc b4 de 42 28 bd 2a 8c 13'), textout=CWbytearray(b'd8 95 6f 9b 4a c7 5c 81 e9 02 da 05 ae 60 79 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.02539062,  0.38769531, ..., -0.5       ,
           -0.41992188, -0.42773438]), textin=CWbytearray(b'70 e7 74 eb f5 bc cb a6 cb df 01 81 4d e0 43 80'), textout=CWbytearray(b'2c 4a ff 47 c7 c8 ec 6a 8e 90 16 90 ce 26 48 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.39941406, -0.41894531]), textin=CWbytearray(b'0e 11 14 1b 7e 88 e8 c3 60 ae 19 e7 19 7f 5a a0'), textout=CWbytearray(b'31 90 d9 80 eb 50 63 ef 32 1d 84 1c 0a da 08 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02734375,  0.390625  , ..., -0.5       ,
           -0.41503906, -0.41796875]), textin=CWbytearray(b'c1 d8 10 c9 e5 eb 94 36 b9 19 89 dd 05 5f a1 28'), textout=CWbytearray(b'38 0c 77 88 e7 b5 74 33 bb e6 d4 f4 a7 55 aa d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.01757812,  0.38085938, ..., -0.5       ,
           -0.42675781, -0.43261719]), textin=CWbytearray(b'29 03 09 63 eb c6 49 2b 00 0f 1e 70 6b cc ea d1'), textout=CWbytearray(b'26 f9 d0 05 f5 85 d6 5a d7 6e 1d d4 00 50 29 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.41894531, -0.43066406]), textin=CWbytearray(b'3c 8d 63 62 d8 78 ea a1 9c a0 25 90 ba 45 66 ab'), textout=CWbytearray(b'27 df a7 e8 49 08 80 82 86 96 c4 36 eb 70 84 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.04882812,  0.40820312, ..., -0.5       ,
           -0.40234375, -0.40722656]), textin=CWbytearray(b'da bf 06 4d 1c c9 07 b7 5a e5 1a 75 4a b4 26 db'), textout=CWbytearray(b'35 0b ac 7e af da 43 bb c7 7d cb 61 d0 83 83 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13964844,  0.01367188,  0.37792969, ..., -0.5       ,
           -0.40917969, -0.41601562]), textin=CWbytearray(b'1d 25 5a 51 a6 22 c8 5c d3 62 2a e8 9e 8e 81 35'), textout=CWbytearray(b'b6 fd c7 56 46 27 8f d9 22 43 85 fd cf 68 b7 db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.02050781,  0.3828125 , ..., -0.5       ,
           -0.38964844, -0.39746094]), textin=CWbytearray(b'06 51 c5 28 1c 63 ad 29 3b 37 8c ce 0a bb 30 c4'), textout=CWbytearray(b'dd 4b 71 1d 2e 84 1c 7a f3 8d 2f 88 01 db 16 56'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.390625  , -0.41601562]), textin=CWbytearray(b'33 40 0a 3d 4f 55 20 0d f5 4e 5c fd c6 61 c8 31'), textout=CWbytearray(b'6a dc d8 f3 18 36 99 a6 6b 2a a6 a0 6a 3b 5f 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02832031,  0.38964844, ..., -0.5       ,
           -0.41894531, -0.42285156]), textin=CWbytearray(b'1a 16 1c b4 a0 92 6e b8 bf af 8f 37 f1 b3 99 0e'), textout=CWbytearray(b'f7 b6 4f de 60 25 05 45 bb 68 3d ac c4 48 f6 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.40527344, ..., -0.5       ,
           -0.38671875, -0.39550781]), textin=CWbytearray(b'df 1e ce e3 8a 9b e8 e8 d6 f9 17 dd f8 5a e9 d8'), textout=CWbytearray(b'7a a9 39 fd 81 2d 78 9e 35 fe 14 73 f1 42 31 fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04199219,  0.39746094, ..., -0.5       ,
           -0.41015625, -0.41796875]), textin=CWbytearray(b'af 79 f8 82 9c b0 24 62 9a 11 57 1f fc 3b 9b 09'), textout=CWbytearray(b'ca b8 66 e1 92 c7 86 69 5c 69 cf 81 61 04 f8 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.01367188,  0.37792969, ..., -0.5       ,
           -0.38574219, -0.4140625 ]), textin=CWbytearray(b'1d dc a1 1f ac 79 fd 14 74 cb 87 45 ea f8 fa 84'), textout=CWbytearray(b'37 0a 7a e5 70 cf a8 5f ec 3e 8f 0f 35 6f d8 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.03027344,  0.39355469, ..., -0.5       ,
           -0.39550781, -0.41699219]), textin=CWbytearray(b'51 a1 4c fd 83 5e 8e 5b ec 6d 54 d7 6e 2a 1d 3d'), textout=CWbytearray(b'15 9b d8 4b 98 a2 a9 33 e8 59 e6 9a 04 1b ec 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'e2 63 7b ea 78 b4 ac fb f7 4e 0d 85 ae 85 12 64'), textout=CWbytearray(b'88 f0 10 23 62 bf d6 ed 73 a6 33 ba 1d d2 cc ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03125   ,  0.390625  , ..., -0.5       ,
           -0.390625  , -0.40039062]), textin=CWbytearray(b'46 53 1a 66 d0 9f 0c 64 ea 3d 77 90 d8 d5 19 4d'), textout=CWbytearray(b'c1 19 a1 2d 51 be a7 42 3e 3b e4 85 b5 b7 e6 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.0078125 ,  0.37402344, ..., -0.5       ,
           -0.39453125, -0.40136719]), textin=CWbytearray(b'72 50 40 99 5c db a7 65 e9 e0 c4 46 82 09 54 4e'), textout=CWbytearray(b'b6 3c f0 48 3d a7 98 03 33 50 95 99 e2 4b c0 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13867188,  0.01464844,  0.38183594, ..., -0.5       ,
           -0.38867188, -0.39941406]), textin=CWbytearray(b'b1 d0 89 d4 dd 88 cb a2 c4 55 75 e7 35 84 80 aa'), textout=CWbytearray(b'cf 9b 75 b5 71 a9 a7 90 4e 90 8e a6 0c f9 ab 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03710938,  0.39648438, ..., -0.5       ,
           -0.41113281, -0.41699219]), textin=CWbytearray(b'bc 9c 76 92 5f d0 ae 1a 41 34 49 9f 95 81 ea f3'), textout=CWbytearray(b'11 b2 76 d7 99 bf cb a5 42 00 8c 44 88 1c 11 43'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.01855469,  0.37890625, ..., -0.5       ,
           -0.38085938, -0.39550781]), textin=CWbytearray(b'0b 96 bd 55 5b 19 fc 1d ea 17 ec f0 13 74 17 70'), textout=CWbytearray(b'88 a8 91 46 fd 69 c7 82 67 f9 df 3a 24 6c 60 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.38378906, -0.39453125]), textin=CWbytearray(b'2d e3 85 0b 2c 40 c7 81 2d 2e 0d 13 92 71 06 36'), textout=CWbytearray(b'cc 69 23 04 53 07 86 a5 2b 3c 3f 63 70 9c 91 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03417969,  0.38085938, ..., -0.5       ,
           -0.40429688, -0.41113281]), textin=CWbytearray(b'ee b4 30 de c2 6d 56 c0 59 06 db e5 29 49 53 f4'), textout=CWbytearray(b'61 5d 9d 83 6b 4c 64 5c 8b 29 29 70 ee 1c c3 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.39257812, ..., -0.5       ,
           -0.40136719, -0.41015625]), textin=CWbytearray(b'd6 18 ae f4 39 90 f5 78 50 97 19 cf 82 04 49 6a'), textout=CWbytearray(b'f5 bf 62 c1 ce 64 60 e7 08 a5 6d e5 66 3d 85 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.41113281, -0.41601562]), textin=CWbytearray(b'60 df 0d d4 61 38 2e b2 c6 7c d6 d5 1d 90 22 bf'), textout=CWbytearray(b'57 1f 26 13 54 3c 2a 2a d1 91 c4 f8 09 91 57 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.0234375 ,  0.38671875, ..., -0.5       ,
           -0.40234375, -0.42285156]), textin=CWbytearray(b'd9 14 a3 ed 09 59 ef c3 c9 d3 5f 53 de e4 96 78'), textout=CWbytearray(b'09 e7 a9 42 9a 66 94 e4 d4 b6 da af 24 aa 04 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.0078125 ,  0.37402344, ..., -0.5       ,
           -0.40234375, -0.40917969]), textin=CWbytearray(b'65 b5 43 50 df ca aa 3b 50 66 8c c0 d5 1b 76 38'), textout=CWbytearray(b'79 a5 d0 02 31 e6 69 54 42 e6 6a 3e 53 93 e6 e7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.41894531, -0.41894531]), textin=CWbytearray(b'6b d3 f3 9e 76 7e c2 42 de 53 49 9e 8f 2c f4 5a'), textout=CWbytearray(b'06 85 50 45 97 4e fa 62 7d 63 77 71 26 27 d0 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04394531,  0.40136719, ..., -0.5       ,
           -0.43359375, -0.43164062]), textin=CWbytearray(b'9b 0c 72 7f 12 ea 50 da 8f 22 80 f6 7a 76 fe 20'), textout=CWbytearray(b'7a d1 97 df 3b 98 db 7a ae f8 e7 47 1e ed 35 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02636719,  0.38574219, ..., -0.5       ,
           -0.41210938, -0.41699219]), textin=CWbytearray(b'43 c1 34 c7 65 b7 d2 09 4a 92 83 cb 55 df 9f 7a'), textout=CWbytearray(b'61 9a d0 7d b6 f1 46 00 53 c2 e5 ee f2 d6 f1 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.40332031, ..., -0.5       ,
           -0.40136719, -0.40722656]), textin=CWbytearray(b'63 49 bb cd a7 78 eb 19 e4 25 d9 81 9f ad 8c d3'), textout=CWbytearray(b'5e 4c 33 5c ca ef 58 3a 2e e4 84 2e 1d 5e e9 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.40429688, -0.41015625]), textin=CWbytearray(b'82 c6 e2 6e 78 ff b7 e5 e7 d2 ea 8b 63 d9 ff 2a'), textout=CWbytearray(b'63 ad 82 83 ba 19 55 33 7d dc fa 30 5f b4 be f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14648438,  0.01171875,  0.37695312, ..., -0.5       ,
           -0.40527344, -0.41210938]), textin=CWbytearray(b'70 75 3d f7 09 3f 12 04 03 2e 79 b5 fc 79 d6 99'), textout=CWbytearray(b'1c 7e 37 e9 3b a0 fe 98 07 c8 1c 71 dc 1c fb d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.41210938, -0.4296875 ]), textin=CWbytearray(b'a4 1a f8 f8 61 22 4d ed eb 1b 5a 4c fc c8 0c b2'), textout=CWbytearray(b'c3 0b 85 02 f1 00 c2 c7 25 f6 3b 95 a4 a2 77 ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03808594,  0.3828125 , ..., -0.5       ,
           -0.42285156, -0.42285156]), textin=CWbytearray(b'42 ad 62 56 f3 0f 49 fe bc 27 5f a6 2a 7d e2 c8'), textout=CWbytearray(b'7c 1c 31 ae 91 b3 8d 82 b9 2b 59 ac 87 70 42 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.0234375 ,  0.38964844, ..., -0.5       ,
           -0.41308594, -0.41308594]), textin=CWbytearray(b'e0 9d 88 ce fa 64 67 cc 2b cd a1 84 49 3c d0 94'), textout=CWbytearray(b'80 50 a6 08 c4 7f ce e4 7b 1b 0a 7a b5 46 28 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04296875,  0.40234375, ..., -0.5       ,
           -0.39550781, -0.40625   ]), textin=CWbytearray(b'82 dd fb f1 a0 2b 98 82 49 00 5d 65 68 64 f6 bc'), textout=CWbytearray(b'86 4c 85 88 ac 6b 27 5c 99 b3 f6 0d d0 a6 41 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14941406,  0.0234375 ,  0.38671875, ..., -0.5       ,
           -0.40039062, -0.40917969]), textin=CWbytearray(b'b5 a5 e2 9c 0c 28 d1 77 6a 08 4c 8a fa d0 56 70'), textout=CWbytearray(b'54 de 98 7a 85 55 0d 57 66 74 4a 08 ce f9 50 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.02148438,  0.37011719, ..., -0.5       ,
           -0.38964844, -0.39941406]), textin=CWbytearray(b'ac 7f f0 8a c6 f4 0a 02 01 6b a8 ec f6 83 47 55'), textout=CWbytearray(b'c2 67 8c bb c8 52 c7 1b e2 95 9c b6 65 45 a1 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18945312,  0.05273438,  0.40625   , ..., -0.5       ,
           -0.42675781, -0.42871094]), textin=CWbytearray(b'b7 ea bd 3e 95 8d 63 c3 69 23 93 bc 94 29 d2 8c'), textout=CWbytearray(b'ec be 07 34 25 4f 10 f3 97 0d 69 b2 4d 6b 92 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.44433594, -0.43847656]), textin=CWbytearray(b'd1 10 27 8c a6 d8 e3 b0 64 af 97 4d 36 eb 6d c1'), textout=CWbytearray(b'd0 5e 40 d6 26 72 14 0c b7 2c 5b 23 dd 86 7d d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.46875   , -0.45800781]), textin=CWbytearray(b'36 00 8c 0b e1 ef 95 99 0a 7a ff ec b9 0b 3e 32'), textout=CWbytearray(b'71 81 b1 64 97 6e 25 d2 c0 08 4a 80 2e ca 8a 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04492188,  0.40039062, ..., -0.5       ,
           -0.38574219, -0.39746094]), textin=CWbytearray(b'4c 0c 6f a0 8d bb 3f 5b 1a 0d 48 9d 97 88 a6 f7'), textout=CWbytearray(b'96 d4 a1 14 d1 0b f9 a9 8e c3 1f 8a d8 93 c1 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13671875,  0.01464844,  0.37695312, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'30 b1 99 16 cc 90 1d 19 d6 5b fd 2c 0b d0 a7 c0'), textout=CWbytearray(b'cc 40 69 a5 20 d4 bb fe fb 31 7a 31 b8 ab fb 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.01171875,  0.37402344, ..., -0.5       ,
           -0.40820312, -0.41503906]), textin=CWbytearray(b'79 74 3e 78 03 56 3c d1 9b f7 26 11 6a 2f 65 bc'), textout=CWbytearray(b'4f c4 fd 8f b6 27 4b b9 13 fa dc e0 7f 8a 6b 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.02148438,  0.38183594, ..., -0.5       ,
           -0.39355469, -0.40234375]), textin=CWbytearray(b'ae e0 f3 73 45 bc 32 ed 13 5f e8 82 44 3c 66 dc'), textout=CWbytearray(b'c6 2a 43 8c 85 9e 51 e7 80 0a d4 81 24 36 2a 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04394531,  0.3984375 , ..., -0.5       ,
           -0.390625  , -0.40136719]), textin=CWbytearray(b'9b bb 91 76 b2 a3 35 ed a6 b2 1d 1e 46 16 f2 85'), textout=CWbytearray(b'a8 02 a6 54 88 45 6a 67 67 a3 f8 24 98 62 2f 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03808594,  0.39941406, ..., -0.5       ,
           -0.40039062, -0.40722656]), textin=CWbytearray(b'81 95 36 22 fe ae 32 df fa ff e4 58 d8 27 e8 2b'), textout=CWbytearray(b'f9 a4 ca 56 45 0d 20 88 b5 20 e9 b1 e2 8e ed cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.02636719,  0.375     , ..., -0.5       ,
           -0.42578125, -0.42382812]), textin=CWbytearray(b'94 01 0c e7 cf da f8 85 52 c6 95 81 1b 7a ea 22'), textout=CWbytearray(b'94 63 fa 7f d1 bc b1 73 e8 83 2c e6 87 19 38 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.02929688,  0.37207031, ..., -0.5       ,
           -0.44628906, -0.44335938]), textin=CWbytearray(b'98 c3 cc 62 74 10 db 92 4a 66 29 e4 ac 38 1c c8'), textout=CWbytearray(b'c3 d5 e0 07 ac c1 e2 b4 5c 67 66 8f 84 b1 b8 c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.02539062,  0.3828125 , ..., -0.5       ,
           -0.41601562, -0.41894531]), textin=CWbytearray(b'6d a0 a7 a1 8b d6 5f 7e e4 0a 6c 55 f4 f7 81 27'), textout=CWbytearray(b'89 69 b6 37 70 28 2d c0 3f 2e 56 64 d1 14 09 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02441406,  0.38671875, ..., -0.5       ,
           -0.40527344, -0.42480469]), textin=CWbytearray(b'45 d7 ca cd b7 32 e2 01 f0 15 ed f1 0f 35 17 14'), textout=CWbytearray(b'a3 9b 12 bb 7a 59 de 73 3b 1c 00 66 65 4c d9 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15332031,  0.02636719,  0.38769531, ..., -0.5       ,
           -0.43554688, -0.43554688]), textin=CWbytearray(b'ae f9 17 87 2c 7d cf 6c 3f a3 da d9 ea d5 22 be'), textout=CWbytearray(b'68 e0 2a 82 e3 d8 5a 45 3b 6f 75 39 67 eb e9 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04003906,  0.3984375 , ..., -0.5       ,
           -0.41015625, -0.41601562]), textin=CWbytearray(b'5c 9d 81 8b 6e ef b4 91 c7 c6 e6 b6 14 c7 1b 4e'), textout=CWbytearray(b'c0 83 10 a4 4c a6 db 59 d1 04 be c6 eb 27 d5 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03710938,  0.39550781, ..., -0.5       ,
           -0.4140625 , -0.41601562]), textin=CWbytearray(b'75 f2 fe a1 04 60 c7 3a 7f bc f8 8b f8 13 69 87'), textout=CWbytearray(b'58 7e 6d 81 d6 2f d1 5a a4 5f 50 ec 36 c4 31 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.04199219,  0.40039062, ..., -0.5       ,
           -0.40917969, -0.41113281]), textin=CWbytearray(b'ff e8 2f 5c be 9c 28 9d a2 32 67 a2 d7 b0 a1 bc'), textout=CWbytearray(b'4b dc 02 60 25 24 eb 3c 71 77 7f 1a 2a 2f 43 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.01074219,  0.37792969, ..., -0.5       ,
           -0.38183594, -0.39257812]), textin=CWbytearray(b'a8 31 13 b5 72 f5 45 87 c8 11 04 4a 50 33 1c 4c'), textout=CWbytearray(b'4e 89 04 bd 16 ab aa 37 b9 ca d6 d8 97 43 47 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.12109375, -0.00390625,  0.36328125, ..., -0.5       ,
           -0.40820312, -0.41308594]), textin=CWbytearray(b'b7 f2 16 9f 2e b8 2f fc 14 9f 90 ad df e6 52 97'), textout=CWbytearray(b'8f 04 b9 6b ec b0 0d 08 ef 7f 11 2a 38 bb 77 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.39453125, ..., -0.5       ,
           -0.41308594, -0.43359375]), textin=CWbytearray(b'4e 26 20 93 14 85 e9 be d0 bb 75 a8 14 36 1f 0b'), textout=CWbytearray(b'cb 2b 6d 73 82 82 c6 df 07 57 20 2b b0 3b 5a 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03515625,  0.38183594, ..., -0.5       ,
           -0.40136719, -0.40722656]), textin=CWbytearray(b'1b fc 5a 20 cc 5a d6 29 31 49 54 ef df 5d 91 a5'), textout=CWbytearray(b'b2 37 ae 26 56 dd c6 2f 0a e3 32 3a c5 c9 64 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.05078125,  0.40625   , ..., -0.5       ,
           -0.40820312, -0.41601562]), textin=CWbytearray(b'4e d5 29 5e eb eb 22 b5 3d a8 bc 44 6a 96 b6 91'), textout=CWbytearray(b'5d bf a6 fb 3c 28 31 4d d5 41 98 cb 42 ec f7 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.40722656, ..., -0.5       ,
           -0.45214844, -0.44628906]), textin=CWbytearray(b'58 4c 02 61 8b 1f 79 d9 90 10 8a e4 8c 64 0e 44'), textout=CWbytearray(b'37 af c1 3f 2b 31 f4 64 0c 67 1e 39 8f 3d 0c cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03125   ,  0.39160156, ..., -0.5       ,
           -0.41015625, -0.41308594]), textin=CWbytearray(b'cf 1a 9f 6e 30 2a e1 2c 8e 53 5d 21 27 2a 6b 20'), textout=CWbytearray(b'c8 29 2a 3f 88 74 3d 39 aa c8 a5 b7 32 ac 35 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03417969,  0.37890625, ..., -0.5       ,
           -0.41601562, -0.41894531]), textin=CWbytearray(b'3f ec 65 b7 23 60 99 da 7d 6b 95 36 4b 25 6e a1'), textout=CWbytearray(b'f5 b6 29 6a f4 6f 2c 41 a6 71 a4 e9 2a 07 c7 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15917969,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.3828125 , -0.40234375]), textin=CWbytearray(b'44 af 66 56 b7 92 03 3d 9e 88 56 9b 1c 60 3f 55'), textout=CWbytearray(b'62 d4 81 d6 09 5a 7b 23 d1 9c d7 f3 e4 fa ba c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03710938,  0.38183594, ..., -0.5       ,
           -0.40722656, -0.41308594]), textin=CWbytearray(b'03 5b e7 87 e5 b2 19 d8 61 de 7e 00 67 ee a7 72'), textout=CWbytearray(b'16 bb ef af d9 3d 13 7b c4 e6 94 fe 6a fa 4a bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03515625,  0.38378906, ..., -0.5       ,
           -0.421875  , -0.42480469]), textin=CWbytearray(b'82 02 0b 1c 4a 9b 60 71 ab 84 14 1d c5 8f d9 3d'), textout=CWbytearray(b'53 82 82 40 1f dc 2e 37 1a f8 bf 26 9f 6a c2 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.0078125 ,  0.37402344, ..., -0.5       ,
           -0.38183594, -0.39453125]), textin=CWbytearray(b'68 4d af 88 f2 5a 47 49 74 1b 32 36 89 60 83 90'), textout=CWbytearray(b'd2 99 c2 0f 23 17 d6 ad fa d8 d9 98 fd 13 cd 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.40722656, -0.41210938]), textin=CWbytearray(b'c6 72 65 b6 6b ff d5 9c 81 b3 70 2c 17 74 1b 75'), textout=CWbytearray(b'6c a0 63 a9 05 d9 1b 67 da dd 22 78 5b 5c fd b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11230469, -0.00585938,  0.36523438, ..., -0.5       ,
           -0.38183594, -0.39648438]), textin=CWbytearray(b'11 c3 6f 02 c9 70 2d cb ba f6 e0 0e 87 fe 9b ac'), textout=CWbytearray(b'8b f0 1d 11 77 3e d6 1a b4 f7 b8 ff 6d 11 fb 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.05273438,  0.40820312, ..., -0.5       ,
           -0.44238281, -0.44042969]), textin=CWbytearray(b'b3 8d d7 13 a1 ee 63 04 61 34 3c 3e a8 6b f7 c6'), textout=CWbytearray(b'4b 5b 2a f1 d0 1b 7e 60 31 57 99 1b 09 eb da 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01757812,  0.3828125 , ..., -0.5       ,
           -0.39257812, -0.40234375]), textin=CWbytearray(b'2f 49 c9 00 d8 f8 64 17 01 2b a2 0e 08 3a 59 4c'), textout=CWbytearray(b'4a 57 27 cc 3c 08 10 e0 63 14 e6 2e 86 73 1a b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05664062,  0.41210938, ..., -0.5       ,
           -0.4375    , -0.43652344]), textin=CWbytearray(b'b7 7a 58 5a ad c9 d0 a8 a0 aa 96 26 e5 11 f7 5f'), textout=CWbytearray(b'2a 32 01 31 60 ee 89 9e a8 e4 dd 57 d5 c1 09 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.38964844, ..., -0.5       ,
           -0.42382812, -0.42578125]), textin=CWbytearray(b'c7 79 14 e8 ec d8 68 84 8a 5d 34 5a 66 1a d7 62'), textout=CWbytearray(b'fb 8d 4b b9 c1 15 92 db 42 34 b4 fe 8b 68 4d 13'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04589844,  0.40332031, ..., -0.5       ,
           -0.42871094, -0.42773438]), textin=CWbytearray(b'ec dc 21 83 f3 76 b2 1a 47 d0 6c eb 20 7b 8d c9'), textout=CWbytearray(b'e8 d8 66 ce ec 19 76 70 fc 65 50 2d 21 2b e1 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.00683594,  0.37304688, ..., -0.5       ,
           -0.42480469, -0.42382812]), textin=CWbytearray(b'66 fd 5e 8a 0e 62 3d 47 e6 ea 95 3c a4 45 81 2f'), textout=CWbytearray(b'dc 7c 49 57 5d ba 93 c7 89 92 8a 40 52 8c 40 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.046875  ,  0.40332031, ..., -0.5       ,
           -0.43847656, -0.43457031]), textin=CWbytearray(b'dd 39 1c 14 a6 92 2c 2a 58 30 32 8d 94 c9 37 95'), textout=CWbytearray(b'b7 0a 3f 7f 83 3e 56 9e 06 bd e8 56 61 eb 8f 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11230469, -0.02441406,  0.35058594, ..., -0.5       ,
           -0.42480469, -0.42382812]), textin=CWbytearray(b'aa 9c 10 20 e3 e4 b4 b6 d9 18 0b d1 4e be 57 bb'), textout=CWbytearray(b'b6 f2 69 47 46 f7 c0 93 f6 62 11 7a 95 a0 0c 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.38574219, ..., -0.5       ,
           -0.41894531, -0.421875  ]), textin=CWbytearray(b'45 9f 12 a4 67 87 57 c1 2c c3 3a 2a 54 0e 43 dd'), textout=CWbytearray(b'6f b7 2a e3 fe 20 75 50 59 54 00 74 c7 e2 52 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.0546875 ,  0.39160156, ..., -0.5       ,
           -0.40722656, -0.41503906]), textin=CWbytearray(b'58 3c d8 94 1d cb ce dc 27 7d fb 22 62 72 a1 77'), textout=CWbytearray(b'4b 68 98 a5 a6 27 71 90 2c 89 98 06 14 30 10 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1171875 , -0.015625  ,  0.35644531, ..., -0.5       ,
           -0.40625   , -0.41210938]), textin=CWbytearray(b'9f 5f 27 4d e0 74 9c f6 93 49 9e 54 1f 85 8d 4e'), textout=CWbytearray(b'01 1f d6 c2 3c 16 10 4e 4a 18 16 0d 30 79 e8 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04101562,  0.38574219, ..., -0.5       ,
           -0.43847656, -0.44042969]), textin=CWbytearray(b'ff 68 50 7b 8f 1c 41 0a be e0 f6 b0 a3 e9 70 db'), textout=CWbytearray(b'ba c5 24 f9 ad 99 86 88 c9 db da 80 56 8c 30 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.41699219, -0.41894531]), textin=CWbytearray(b'0b bb dc 25 f4 4f 5f 02 d4 33 45 04 92 2c 84 98'), textout=CWbytearray(b'55 3e c5 6b fd 14 99 ef 3d fb 85 eb bb 06 d9 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.03125   ,  0.39160156, ..., -0.5       ,
           -0.39160156, -0.40136719]), textin=CWbytearray(b'b1 cb 89 74 0b 2b cd b1 49 76 b1 1e 3b 00 79 9a'), textout=CWbytearray(b'df be a6 c2 17 be bc 83 18 be fc e9 31 f5 ed 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39453125, ..., -0.5       ,
           -0.41601562, -0.42285156]), textin=CWbytearray(b'39 a8 48 00 51 0d 0b 09 37 c0 ec 05 ba e4 65 1b'), textout=CWbytearray(b'4c 1d 5f aa c2 6f 53 30 18 5f 3e 04 8b 1c 02 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.02929688,  0.38867188, ..., -0.5       ,
           -0.3828125 , -0.39257812]), textin=CWbytearray(b'9d 40 b1 01 c0 78 18 15 99 b9 18 88 2a 92 d3 28'), textout=CWbytearray(b'b9 19 05 5c e6 c0 01 a4 d9 95 0f 50 7f e4 82 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.0390625 ,  0.3984375 , ..., -0.5       ,
           -0.42089844, -0.42285156]), textin=CWbytearray(b'40 14 62 43 c6 a5 65 82 b0 a2 ff 1f ef b9 73 e7'), textout=CWbytearray(b'2e ea 28 ad 8f 5a 71 aa 02 71 91 ed 0e 71 a7 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19140625,  0.0546875 ,  0.41113281, ..., -0.5       ,
           -0.44238281, -0.45214844]), textin=CWbytearray(b'62 48 05 ac 79 f3 0e 17 2c e1 7a 12 97 91 f8 54'), textout=CWbytearray(b'bb d3 1b 7a d9 86 26 d4 d4 26 2e 8b 0a de 9d 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16894531,  0.03710938,  0.37695312, ..., -0.5       ,
           -0.39257812, -0.39941406]), textin=CWbytearray(b'68 ca 57 b7 b0 f4 ef e2 c3 07 60 ea cc aa 5c 8c'), textout=CWbytearray(b'fd 96 f5 9a e6 23 b6 f2 2e c1 8e f7 04 7a 81 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.05273438,  0.39160156, ..., -0.5       ,
           -0.39941406, -0.40625   ]), textin=CWbytearray(b'51 ed 7a 4f dd 15 3c 7a a1 4a 34 4c df 17 a5 17'), textout=CWbytearray(b'dd 40 0a ec 29 fc e2 79 53 c1 25 56 51 2a 37 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1796875 ,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.41015625, -0.41601562]), textin=CWbytearray(b'ef 60 8a dc e0 99 a6 5d 4b 35 fc c3 8b 40 dc 39'), textout=CWbytearray(b'f3 83 5a 05 88 25 8d 8b 38 bb 57 75 18 ea 9e 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.10644531, -0.00976562,  0.359375  , ..., -0.5       ,
           -0.36621094, -0.3828125 ]), textin=CWbytearray(b'88 52 2e 08 91 7a 9c d4 ff 32 80 6a 81 aa 71 22'), textout=CWbytearray(b'32 71 7d 56 b9 3c 52 5d 95 01 6d a5 d1 f3 3f 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04882812,  0.40332031, ..., -0.5       ,
           -0.41796875, -0.42089844]), textin=CWbytearray(b'b9 23 04 3a 55 7e 53 bb dc 0c 51 cc ab d4 2a bf'), textout=CWbytearray(b'f6 b2 b3 1f 47 1f e6 4d 9c c3 02 d0 81 b9 42 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40234375, ..., -0.5       ,
           -0.41894531, -0.41992188]), textin=CWbytearray(b'89 7f 16 97 12 28 ef 86 71 bd a3 28 5e dc de 5f'), textout=CWbytearray(b'b3 46 47 93 2e 09 62 bc 72 7b 9c 16 30 5a b8 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.03027344,  0.38964844, ..., -0.5       ,
           -0.40917969, -0.41699219]), textin=CWbytearray(b'bb d2 13 3a bf 86 8a cc d1 88 b8 9f e5 e8 95 76'), textout=CWbytearray(b'67 81 9c fa 48 90 74 15 2f 2a e7 40 75 4b 87 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05371094,  0.40917969, ..., -0.5       ,
           -0.40625   , -0.41113281]), textin=CWbytearray(b'38 a2 3d 1a e6 b1 da d1 c6 d7 f0 ea bd 38 48 25'), textout=CWbytearray(b'26 64 4a 30 f3 41 3d 86 07 11 35 b9 90 6a 5c d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03613281,  0.39550781, ..., -0.5       ,
           -0.38378906, -0.39550781]), textin=CWbytearray(b'33 37 50 80 22 90 ec c7 38 5d a7 03 1f 3a 7d ce'), textout=CWbytearray(b'd7 c1 97 58 59 bb 22 6d 0c a1 50 64 32 a9 1b 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04003906,  0.39746094, ..., -0.5       ,
           -0.46875   , -0.4609375 ]), textin=CWbytearray(b'04 9e e8 f6 56 36 8c 8d eb 71 44 0d 15 ea 24 aa'), textout=CWbytearray(b'd9 74 a2 b6 c8 48 ae 66 56 34 2d 73 a7 94 bb 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.39746094, -0.40429688]), textin=CWbytearray(b'2a d8 05 54 fa fc f7 91 61 e0 20 9a e6 8f e2 00'), textout=CWbytearray(b'29 26 67 c5 f0 bb 28 51 23 dd f8 4e 03 47 06 bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.09179688, -0.02441406,  0.34960938, ..., -0.5       ,
           -0.37109375, -0.40625   ]), textin=CWbytearray(b'2d 51 f4 e1 a7 9c 1d c3 6b 1d 56 78 34 db 4f 74'), textout=CWbytearray(b'21 a7 82 e4 31 c7 c1 49 db 2c 72 f4 fc e5 01 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13671875,  0.01464844,  0.37890625, ..., -0.5       ,
           -0.38769531, -0.41503906]), textin=CWbytearray(b'85 d2 07 b2 ec ff b1 06 95 8b f2 26 71 ae a9 b3'), textout=CWbytearray(b'ef e3 f5 71 35 62 6b 20 5e ff 81 13 06 52 0a a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.02734375,  0.39160156, ..., -0.5       ,
           -0.3828125 , -0.39550781]), textin=CWbytearray(b'6e 81 72 fb f6 03 70 0c e6 30 94 40 a9 fc ca f0'), textout=CWbytearray(b'b4 a0 0a 23 c1 e7 11 1d d1 8f 7b eb 23 92 b4 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04394531,  0.40136719, ..., -0.5       ,
           -0.44042969, -0.44628906]), textin=CWbytearray(b'7b 91 26 82 b0 44 0a c4 72 e8 6e 29 f3 13 70 f0'), textout=CWbytearray(b'49 ee b2 c2 4a 31 71 3e 67 62 71 55 48 56 fa 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05078125,  0.39257812, ..., -0.5       ,
           -0.42578125, -0.42578125]), textin=CWbytearray(b'f8 69 db 5d 22 6f be 3b a3 dd 17 73 29 14 4c 21'), textout=CWbytearray(b'4c 9f a7 21 9c 49 64 f2 b4 96 72 44 14 16 b2 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04980469,  0.40527344, ..., -0.5       ,
           -0.41308594, -0.41796875]), textin=CWbytearray(b'ee da 4b cc 3d 72 22 37 13 11 1a fb d5 c5 51 cc'), textout=CWbytearray(b'21 d3 e3 f1 8c ec 45 d0 95 d4 ad 5b 87 aa 32 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03710938,  0.39746094, ..., -0.5       ,
           -0.41503906, -0.41699219]), textin=CWbytearray(b'38 f0 dd b2 61 0e 35 a7 b8 f9 77 42 e2 f0 cb 17'), textout=CWbytearray(b'e1 79 f5 c1 5a b5 41 f9 5f b2 d4 10 49 2c d1 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04296875,  0.38769531, ..., -0.5       ,
           -0.41113281, -0.41503906]), textin=CWbytearray(b'27 8e df 24 f2 64 d5 26 6e 86 98 a9 32 50 5c 4e'), textout=CWbytearray(b'59 b1 b2 bd 0b 28 4f 59 9a 02 af fa a7 ca d1 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.19042969,  0.05273438,  0.3984375 , ..., -0.5       ,
           -0.44726562, -0.44238281]), textin=CWbytearray(b'8a 4d b1 3f 00 de 46 68 85 a8 38 e0 c5 f6 a9 29'), textout=CWbytearray(b'bf 86 e4 29 ff 33 28 25 61 ff a7 c2 83 be c6 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18554688,  0.05078125,  0.39550781, ..., -0.5       ,
           -0.42578125, -0.42773438]), textin=CWbytearray(b'4d 6f 80 ed f0 b6 98 76 c6 8d 43 60 fb af b0 17'), textout=CWbytearray(b'1f 15 18 77 de c9 62 c2 23 01 ce 82 91 54 40 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02734375,  0.38964844, ..., -0.5       ,
           -0.40917969, -0.41894531]), textin=CWbytearray(b'1f 58 8b 9a 6c ec 1d 5b 12 20 6c 55 00 98 8f 6f'), textout=CWbytearray(b'3c 77 a3 1b a2 a9 b1 2c b1 e8 54 e8 c6 57 fe 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04296875,  0.40039062, ..., -0.5       ,
           -0.41601562, -0.4375    ]), textin=CWbytearray(b'a5 a6 4c d5 e0 7b 8e ef d3 56 9a d5 61 ef 03 3e'), textout=CWbytearray(b'86 4f d8 79 83 d3 a0 f0 20 8c ba 08 59 be 11 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17285156,  0.0390625 ,  0.38085938, ..., -0.5       ,
           -0.38183594, -0.39257812]), textin=CWbytearray(b'21 c7 d8 ec 0a 81 4f 92 7c 0f fb e7 25 4f 78 2a'), textout=CWbytearray(b'b5 e8 b3 ee 39 a8 93 e0 de ad 12 bf 99 f9 83 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.390625  , ..., -0.5       ,
           -0.37890625, -0.40625   ]), textin=CWbytearray(b'6b 83 9e 6e d3 03 9e 54 52 53 8a 99 cd 7b 40 71'), textout=CWbytearray(b'5c 61 49 94 5b 54 1e cc d2 7b ee a5 c4 14 7c ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15039062,  0.01855469,  0.38183594, ..., -0.5       ,
           -0.37695312, -0.39648438]), textin=CWbytearray(b'1f 72 6f 88 a3 55 c7 1f cb bd f9 e4 75 fc 41 33'), textout=CWbytearray(b'39 21 ce 62 d7 a6 f2 b9 51 cf 66 d9 4c 1f 18 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.0546875 ,  0.40917969, ..., -0.5       ,
           -0.38378906, -0.41308594]), textin=CWbytearray(b'60 d4 5a e2 23 01 17 0e 29 be 06 e5 f6 a5 3c 5e'), textout=CWbytearray(b'c6 ff e8 fe ea 69 54 c5 ea a3 58 64 8f 38 a1 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.01855469,  0.38476562, ..., -0.5       ,
           -0.41699219, -0.41699219]), textin=CWbytearray(b'44 79 4b 3e 9e 14 e7 74 76 5d e2 ce 29 b4 36 9e'), textout=CWbytearray(b'17 77 de d8 12 e7 c7 c9 d8 bd 37 9b eb 86 ee 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.0546875 ,  0.40820312, ..., -0.5       ,
           -0.41796875, -0.43554688]), textin=CWbytearray(b'd0 2b 37 d7 3d da 98 22 51 2c 89 82 7a d8 4f 52'), textout=CWbytearray(b'9f 70 f8 75 a6 1f 53 9d de 54 c1 3d e5 ed 72 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.04003906,  0.3984375 , ..., -0.5       ,
           -0.40429688, -0.41210938]), textin=CWbytearray(b'7f b4 44 d1 4f b8 5c a1 4c 39 0f 0d 1c 60 70 04'), textout=CWbytearray(b'cc c7 56 c2 47 ed 61 ac 27 1b 14 22 16 12 7b eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04101562,  0.3984375 , ..., -0.5       ,
           -0.40332031, -0.41894531]), textin=CWbytearray(b'a4 59 7d a6 4a 30 2e 41 4f 8c c9 3e 79 81 01 24'), textout=CWbytearray(b'c8 ff 14 4a 80 72 21 b2 1c fd be a6 ba c4 1d b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03808594,  0.39746094, ..., -0.5       ,
           -0.38476562, -0.39648438]), textin=CWbytearray(b'42 f9 da 19 ee a3 4f 43 23 86 40 23 ff ff 89 d2'), textout=CWbytearray(b'ea 71 1b d8 a8 6b 5b 0f 22 c8 5b 10 ca 63 a4 a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11621094, -0.00683594,  0.36230469, ..., -0.5       ,
           -0.41113281, -0.4140625 ]), textin=CWbytearray(b'c1 65 77 06 7e de e8 35 69 c5 63 e6 39 97 af a6'), textout=CWbytearray(b'e9 7e 29 16 17 49 79 5c 59 86 ee 71 60 e6 92 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.171875  ,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.41992188, -0.43652344]), textin=CWbytearray(b'fc a9 3b e2 24 08 a5 9e c9 4d 29 a3 25 01 64 82'), textout=CWbytearray(b'b5 8a e9 5f 29 e7 bd 2f ea 5e 61 fd 15 77 80 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01464844,  0.37988281, ..., -0.5       ,
           -0.39648438, -0.40527344]), textin=CWbytearray(b'10 55 49 79 01 d6 b3 65 5d f0 3b 2e 64 8a 21 4e'), textout=CWbytearray(b'e9 83 60 76 c0 12 b4 4f 97 ac 80 b2 03 74 69 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.10839844, -0.00878906,  0.34277344, ..., -0.5       ,
           -0.43261719, -0.43066406]), textin=CWbytearray(b'a2 88 4c e6 1e ac 9a cf 43 29 b1 d2 81 da 55 59'), textout=CWbytearray(b'91 03 a3 42 36 d7 63 ed b0 79 79 2d 3d e8 6f e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18066406,  0.04589844,  0.40234375, ..., -0.5       ,
           -0.38476562, -0.39746094]), textin=CWbytearray(b'7c 24 27 ac 43 c5 6b f0 97 db 19 0b 68 6c 26 7d'), textout=CWbytearray(b'48 c6 19 bd 5f 1e b5 95 cd 55 08 2f 49 a5 62 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15820312,  0.02734375,  0.38769531, ..., -0.5       ,
           -0.44335938, -0.43847656]), textin=CWbytearray(b'4a d9 ed 93 74 02 c3 e5 40 f8 93 28 29 10 eb f5'), textout=CWbytearray(b'05 71 9a d7 dd 63 1f 66 e3 45 20 dd dc 0e 62 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04980469,  0.39160156, ..., -0.5       ,
           -0.41992188, -0.42382812]), textin=CWbytearray(b'18 05 2f ad 88 cc 29 6e ac 6d 42 71 72 f9 f1 d0'), textout=CWbytearray(b'b2 0f af 78 a7 c3 29 2d 1c f4 cd bc 05 76 c0 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.046875  ,  0.39160156, ..., -0.5       ,
           -0.40234375, -0.41113281]), textin=CWbytearray(b'b3 92 87 e1 10 18 d5 3d dc e9 7e 86 6e 9c a8 5a'), textout=CWbytearray(b'0c e9 3c 03 5c b6 a4 6e 3e 74 7e 3f 4c 73 a1 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03515625,  0.39257812, ..., -0.5       ,
           -0.40820312, -0.41601562]), textin=CWbytearray(b'40 85 42 22 34 0c 90 4a 7b 8c 5e 84 3b 60 4b 9a'), textout=CWbytearray(b'bd dd 57 fd 6c 59 44 f8 24 5d 7e d4 33 49 f6 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.0234375 ,  0.38476562, ..., -0.5       ,
           -0.4140625 , -0.41601562]), textin=CWbytearray(b'7a 88 90 6f e1 fc ff c6 59 24 11 32 b8 c4 b4 0a'), textout=CWbytearray(b'11 5d 01 cd d8 7d e7 48 f4 f0 9f 1c 5a 34 c7 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.45410156, -0.44824219]), textin=CWbytearray(b'33 26 15 c9 28 36 19 e7 38 3b 30 52 c0 26 6b e0'), textout=CWbytearray(b'31 16 83 f3 9e 81 f2 3c 12 44 d5 0b cd 9d 55 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04589844,  0.40429688, ..., -0.5       ,
           -0.42480469, -0.42871094]), textin=CWbytearray(b'c2 88 4a a1 2e df f2 0b ce d9 8e 8f 0f 34 c2 d0'), textout=CWbytearray(b'88 ed 66 cf bd 8e a3 4e 26 d5 af ef e3 dc 2a a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1328125 ,  0.00976562,  0.37402344, ..., -0.5       ,
           -0.35742188, -0.37695312]), textin=CWbytearray(b'55 ec 35 86 69 7e dc 04 81 30 57 a0 ce 12 34 43'), textout=CWbytearray(b'01 65 d9 96 23 30 97 27 f7 c6 22 da f2 84 c3 d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.42285156, -0.421875  ]), textin=CWbytearray(b'86 f3 1f 59 5c f1 9a aa e9 4f cb 15 2d dd fa e4'), textout=CWbytearray(b'f7 f3 46 bb 3a 82 7b 19 c7 6d 4e 84 b0 08 e8 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04492188,  0.38183594, ..., -0.5       ,
           -0.41894531, -0.42480469]), textin=CWbytearray(b'e5 d7 3c 32 e8 11 e0 b9 2d 3d f6 69 77 e8 64 85'), textout=CWbytearray(b'29 1f 14 d8 3d c9 66 29 06 45 fd e4 a2 24 96 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03222656,  0.390625  , ..., -0.5       ,
           -0.43847656, -0.44921875]), textin=CWbytearray(b'70 5a ed 78 bf 0c ff 32 e4 01 28 62 c2 47 68 e4'), textout=CWbytearray(b'a2 e5 32 08 6a a0 6c c6 3b 19 85 01 4b e8 26 c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14746094,  0.02148438,  0.38476562, ..., -0.5       ,
           -0.41210938, -0.41894531]), textin=CWbytearray(b'a1 b6 01 be bc 1c 30 20 37 f8 7b 75 54 64 c1 e5'), textout=CWbytearray(b'60 39 c2 3e 33 af 85 30 b1 f4 65 0d c2 e4 64 b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.04003906,  0.39746094, ..., -0.5       ,
           -0.44140625, -0.44042969]), textin=CWbytearray(b'd7 d7 f1 56 4a e2 c1 4f 12 f3 3c c1 c7 b8 7e d9'), textout=CWbytearray(b'71 5f f5 e4 c7 ec d6 90 e4 d1 5f f5 c5 35 b7 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02832031,  0.37207031, ..., -0.5       ,
           -0.42285156, -0.42480469]), textin=CWbytearray(b'1d de ac 4e 72 aa fe e7 57 6b 6e cd 2b e5 44 ce'), textout=CWbytearray(b'71 e2 23 c3 54 f8 dd 78 b7 1e 24 06 a5 64 dc 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17480469,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.45800781, -0.453125  ]), textin=CWbytearray(b'37 18 ea 14 e7 88 b5 de 08 72 c2 6c c3 21 3b 60'), textout=CWbytearray(b'43 9c f8 2f ee 22 8d 40 52 36 19 ff 3a db 5c 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.40917969, -0.4140625 ]), textin=CWbytearray(b'fb d1 c4 2d 50 8e 8d 2b 5a 8e 1c 2b af bc fd 7a'), textout=CWbytearray(b'9f d1 91 59 2e 52 a9 82 7c 53 76 8e e3 a3 82 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.4140625 , -0.41796875]), textin=CWbytearray(b'01 1d ea 24 c4 cd 4c 9b 18 30 08 eb 43 3c 1f c4'), textout=CWbytearray(b'2d 2c ef b5 e2 cb 6e 6d c8 2b 8a 45 d8 30 c1 d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14550781,  0.01855469,  0.38183594, ..., -0.5       ,
           -0.38769531, -0.39941406]), textin=CWbytearray(b'a4 f0 42 6f 88 30 b9 8a 4f d5 0e 40 b6 5e 4b 5c'), textout=CWbytearray(b'6d 07 ab 1a f8 5b e0 71 53 c7 25 71 4d 23 55 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.39355469, ..., -0.5       ,
           -0.3984375 , -0.40625   ]), textin=CWbytearray(b'42 e4 20 b2 0f 21 d8 09 f3 bb ac ca 34 73 44 29'), textout=CWbytearray(b'ae 18 73 f6 a1 44 b9 b2 ce c4 45 e2 be 06 89 d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17675781,  0.04394531,  0.38574219, ..., -0.5       ,
           -0.41308594, -0.41796875]), textin=CWbytearray(b'80 63 e7 04 b3 e6 94 ab d8 06 ad 61 9b 49 17 02'), textout=CWbytearray(b'dc 90 1e b4 65 ec b6 9b 68 6c fa 2e 67 74 6e f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03222656,  0.390625  , ..., -0.5       ,
           -0.42675781, -0.4296875 ]), textin=CWbytearray(b'f7 33 7f 3d b2 62 33 a2 cd 94 99 bf 0e 6f 0e a3'), textout=CWbytearray(b'f6 bb be f6 63 72 1b 5e 93 d7 a3 25 5f 94 55 c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16503906,  0.03417969,  0.37890625, ..., -0.5       ,
           -0.39160156, -0.40039062]), textin=CWbytearray(b'09 82 9d e6 66 23 e7 0c 5d f7 b8 c2 7e d6 db 9c'), textout=CWbytearray(b'14 3f 5d 6f 78 66 68 d7 07 2c 8e c8 3b d3 9e 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.03027344,  0.390625  , ..., -0.5       ,
           -0.3828125 , -0.39648438]), textin=CWbytearray(b'2f e3 c3 14 dc 7a 10 38 a7 0d 19 1c 69 2e 39 6a'), textout=CWbytearray(b'71 94 34 92 96 d9 06 03 7a 9e 53 89 cb 5b 32 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15527344,  0.02636719,  0.38671875, ..., -0.5       ,
           -0.43554688, -0.45019531]), textin=CWbytearray(b'64 65 8a d7 44 7b 34 2d 55 db 9d 0e 99 6e b1 76'), textout=CWbytearray(b'03 83 a7 a0 de a4 f6 b6 fe d8 9c 20 08 6f 46 d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17382812,  0.02832031,  0.38867188, ..., -0.5       ,
           -0.43652344, -0.43652344]), textin=CWbytearray(b'a3 a9 b4 06 80 87 95 68 95 c3 24 84 2b cc c9 7e'), textout=CWbytearray(b'43 12 a3 7b 53 a6 7a ae f5 43 69 91 53 e1 d8 b3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18261719,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.42285156, -0.42382812]), textin=CWbytearray(b'a8 4d e0 21 b6 f2 bd 21 2a 3c 83 9f 14 73 aa 47'), textout=CWbytearray(b'99 ee 1c cb f4 ef 1d 8f b2 a4 cb f1 c8 0a 24 81'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16210938,  0.03125   ,  0.39355469, ..., -0.5       ,
           -0.39453125, -0.40820312]), textin=CWbytearray(b'b1 94 08 28 3a 69 47 2b 1c ba 5c 5c 16 eb 8a 3d'), textout=CWbytearray(b'36 f0 d3 5c 8d 74 7f eb c9 5c e2 e9 96 88 b2 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.0390625 ,  0.39746094, ..., -0.5       ,
           -0.37988281, -0.39160156]), textin=CWbytearray(b'01 a3 ae 6b e7 a7 fa 67 bc ca 66 f5 b9 39 7f ac'), textout=CWbytearray(b'da 39 e9 a4 5e 91 f4 1c 03 9b cd c4 54 66 a5 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18164062,  0.04394531,  0.40234375, ..., -0.5       ,
           -0.3984375 , -0.40625   ]), textin=CWbytearray(b'15 01 14 97 d8 a9 18 cd 17 2d e3 ae 15 ff 9b 2c'), textout=CWbytearray(b'd7 1c 3f b7 da 31 f5 5b 87 2e 91 b4 8c b5 a1 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18652344,  0.05273438,  0.40820312, ..., -0.5       ,
           -0.42773438, -0.43066406]), textin=CWbytearray(b'c2 07 ec 77 4b 6d 62 ce c8 59 b4 01 81 aa e0 47'), textout=CWbytearray(b'03 a9 45 08 78 d8 17 03 b4 aa df 51 b2 b8 99 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11425781, -0.00585938,  0.36523438, ..., -0.5       ,
           -0.40625   , -0.41308594]), textin=CWbytearray(b'e5 2b 6b a0 22 8a b3 ac 2d 2d 45 1e 5d 29 53 91'), textout=CWbytearray(b'97 fa 1d 94 10 64 d1 a5 62 a7 85 21 54 39 d3 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.0234375 ,  0.38574219, ..., -0.5       ,
           -0.37890625, -0.40234375]), textin=CWbytearray(b'68 fb 17 48 ee 26 dd ef 3c 15 46 8c 1c 02 0a 30'), textout=CWbytearray(b'22 98 40 d2 0d 74 32 d6 17 12 e7 1e 4d ef 90 fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16601562,  0.03125   ,  0.39257812, ..., -0.5       ,
           -0.40039062, -0.40722656]), textin=CWbytearray(b'06 6c 56 41 97 64 94 7e 35 04 cd bd 13 47 e0 72'), textout=CWbytearray(b'fa e6 64 e5 80 d0 41 38 78 7f ce de 46 7a 02 ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13183594,  0.00585938,  0.37011719, ..., -0.5       ,
           -0.43847656, -0.43847656]), textin=CWbytearray(b'69 84 80 49 0d cc 24 e6 5f e4 06 5b 63 a6 ff 5b'), textout=CWbytearray(b'e2 b3 f2 e9 6f 6a ac e2 b0 94 f0 00 3c 6a 1b 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.03125   ,  0.39160156, ..., -0.5       ,
           -0.38964844, -0.4140625 ]), textin=CWbytearray(b'ed 69 88 00 80 27 19 cc 26 ff 22 7e 10 bb 3f 25'), textout=CWbytearray(b'c3 3f f2 92 d6 fe ff 11 c9 1b 0b c6 1e 24 bb cc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16113281,  0.03222656,  0.39355469, ..., -0.5       ,
           -0.40722656, -0.41113281]), textin=CWbytearray(b'a4 8e 8f d2 7f 65 c7 18 69 11 65 c5 e9 cf 50 88'), textout=CWbytearray(b'ae 05 4e e4 01 58 5f 29 72 7d 84 ff 33 46 38 f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02148438,  0.36816406, ..., -0.5       ,
           -0.39941406, -0.40820312]), textin=CWbytearray(b'11 ba dd a9 f1 38 c5 41 82 c4 45 9c 44 00 31 cd'), textout=CWbytearray(b'98 85 7f c8 8d 6b ed 53 11 35 58 17 dd ff 04 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15234375,  0.01171875,  0.37109375, ..., -0.5       ,
           -0.41992188, -0.42382812]), textin=CWbytearray(b'97 ba dd 9f 3a ec 40 94 1b e8 fc 57 6f a9 07 05'), textout=CWbytearray(b'47 69 76 d9 25 9e 95 0e 25 a2 59 f9 57 bc 76 c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03613281,  0.39453125, ..., -0.5       ,
           -0.40429688, -0.41015625]), textin=CWbytearray(b'fc 9b f7 d8 0c 30 ac 89 59 4e 6b ea 11 77 e7 82'), textout=CWbytearray(b'e4 64 1c c8 a5 c6 7d 9b 9d 8f 87 0d d1 87 66 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.37695312, ..., -0.5       ,
           -0.40625   , -0.40917969]), textin=CWbytearray(b'd8 a7 75 41 81 c8 9b aa e4 a1 0c 38 b0 5f 2b 2a'), textout=CWbytearray(b'c8 4a ca 87 83 08 3c b5 4f 73 53 1b 2d 26 1e da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17578125,  0.04394531,  0.3984375 , ..., -0.5       ,
           -0.38769531, -0.40136719]), textin=CWbytearray(b'93 e2 0e 66 e7 eb 1f f9 a4 64 4d dc 40 ab 84 e5'), textout=CWbytearray(b'df a6 16 7f 0e 04 3c 64 31 b1 a3 82 fd 05 a1 a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1640625 ,  0.03417969,  0.39355469, ..., -0.5       ,
           -0.42578125, -0.42871094]), textin=CWbytearray(b'08 2b b4 1d de 01 35 ab 56 f7 66 df 4b 82 81 38'), textout=CWbytearray(b'9b 74 52 1a 69 0a ca fe d1 72 bb c3 20 06 76 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.02636719,  0.38867188, ..., -0.5       ,
           -0.44140625, -0.4375    ]), textin=CWbytearray(b'e8 99 4d 49 83 fb fa e7 92 b3 b4 9d 15 b1 38 f4'), textout=CWbytearray(b'20 a3 97 8c 70 66 1d 59 bf a0 99 7a 1c a5 55 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.02539062,  0.38867188, ..., -0.5       ,
           -0.43261719, -0.43164062]), textin=CWbytearray(b'82 2c 88 a1 a2 9d 85 4c c4 8a 16 19 e3 f5 40 d8'), textout=CWbytearray(b'6e b4 b6 83 2e ac de ee 6e e8 a9 44 02 86 be 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04492188,  0.40039062, ..., -0.5       ,
           -0.421875  , -0.42578125]), textin=CWbytearray(b'50 0e 07 84 4b e3 9e 6f ec 72 c7 02 d8 f9 3b 22'), textout=CWbytearray(b'0c 37 96 02 68 4f 97 9d cc 44 d0 a8 4b fa 48 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18847656,  0.03417969,  0.39453125, ..., -0.5       ,
           -0.43847656, -0.43457031]), textin=CWbytearray(b'14 f3 f4 1d 4b 13 40 e7 d5 70 79 1a e8 86 b0 a8'), textout=CWbytearray(b'e9 47 51 ee 58 c1 6d 55 69 81 59 3f f3 f2 71 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15429688,  0.02734375,  0.38964844, ..., -0.5       ,
           -0.3984375 , -0.40527344]), textin=CWbytearray(b'3d 0b b7 b2 6d 80 a6 8a ee 3d 63 4c 5c 12 b3 f8'), textout=CWbytearray(b'eb 7d e2 80 2c 55 70 d4 ed 4c d6 a8 18 39 28 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03222656,  0.39355469, ..., -0.5       ,
           -0.41210938, -0.43066406]), textin=CWbytearray(b'56 09 08 98 c8 5f 9b 7d e5 85 7a f6 f6 6e ef 41'), textout=CWbytearray(b'2d 6c a5 96 dc 1e c7 4b 02 34 ba 05 da c4 a2 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16699219,  0.03808594,  0.39550781, ..., -0.5       ,
           -0.41601562, -0.42382812]), textin=CWbytearray(b'4e ed e2 0b 10 9e ae d1 c1 63 ce d5 b7 d5 e6 bb'), textout=CWbytearray(b'58 4a 06 9b 2a 61 cd 92 1a 1f 16 e3 cf bf 7a 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16308594,  0.03320312,  0.37890625, ..., -0.5       ,
           -0.40429688, -0.41015625]), textin=CWbytearray(b'f5 3b 19 f7 9d e1 50 04 05 96 68 33 42 f2 9f e3'), textout=CWbytearray(b'ca ba 13 18 7f 42 f5 47 61 b5 7c 93 48 bf d5 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.04199219,  0.39746094, ..., -0.5       ,
           -0.42089844, -0.42480469]), textin=CWbytearray(b'dc cf d8 d2 91 8b 60 39 b6 02 d6 8c d7 87 08 f8'), textout=CWbytearray(b'ab 30 3e 53 03 6a 3f 25 68 b9 aa 4a 82 6e fb 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.03222656,  0.38964844, ..., -0.5       ,
           -0.45019531, -0.4453125 ]), textin=CWbytearray(b'e9 55 01 e6 e0 13 47 35 d1 06 88 1d c4 6d a6 72'), textout=CWbytearray(b'76 e2 f0 35 49 3b 7f c8 c8 24 69 58 09 19 11 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1875    ,  0.05273438,  0.40820312, ..., -0.5       ,
           -0.40820312, -0.4140625 ]), textin=CWbytearray(b'fe a3 d0 9a 1e 47 70 0f 62 c4 db 09 ef 93 d3 c3'), textout=CWbytearray(b'1f b2 e9 e3 1d d3 d0 23 4a 0a 75 6e d7 be db 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.03808594,  0.3984375 , ..., -0.5       ,
           -0.41113281, -0.41796875]), textin=CWbytearray(b'59 8e 88 30 e7 d7 0e e4 3f ff e4 83 30 82 6f 78'), textout=CWbytearray(b'1d 7b ae 0f 76 23 dc 24 9a ca 14 4c 54 e1 e5 a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04589844,  0.40136719, ..., -0.5       ,
           -0.41113281, -0.43457031]), textin=CWbytearray(b'df 5a b1 90 54 25 bb 70 2c 23 14 61 b6 4b e1 e0'), textout=CWbytearray(b'29 3a 20 97 d4 7d 8f 03 1b 50 fe 43 a9 47 ac fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1484375 ,  0.0234375 ,  0.38476562, ..., -0.5       ,
           -0.37304688, -0.38671875]), textin=CWbytearray(b'12 3f 40 b4 64 c2 f7 9f 15 95 3b 51 54 f1 8a b5'), textout=CWbytearray(b'fa 7f 6c c3 3b 3e 8e c1 3f 7e 02 d7 96 b0 f3 d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.01074219,  0.375     , ..., -0.5       ,
           -0.41992188, -0.42578125]), textin=CWbytearray(b'2f 40 80 2d a4 5a 9a aa 8c ed 35 23 62 ec 72 7c'), textout=CWbytearray(b'41 9d c3 cd ad 2b b7 c6 ce 59 a4 96 49 6f dc 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.15625   ,  0.02734375,  0.38671875, ..., -0.5       ,
           -0.39453125, -0.40429688]), textin=CWbytearray(b'c9 3d cb 72 90 1d 44 83 d2 fd 85 1a 17 0b d7 35'), textout=CWbytearray(b'3b f4 8c 7c bf 43 be 03 c4 8d e6 64 07 45 a1 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.14453125,  0.01660156,  0.37402344, ..., -0.5       ,
           -0.3984375 , -0.40722656]), textin=CWbytearray(b'05 2f 4d 83 b1 8f 25 9e d3 b0 0d f1 9e d3 c9 ee'), textout=CWbytearray(b'ff 16 6e e9 4d e9 a7 74 9c 6c b9 db 3f 8f 44 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16992188,  0.0390625 ,  0.39941406, ..., -0.5       ,
           -0.38378906, -0.40722656]), textin=CWbytearray(b'ff bc fe 9b fe a1 54 f0 11 fd 00 eb 0d 3b 8e 74'), textout=CWbytearray(b'7b 2c 0f cf ef ea 3a e4 25 ec 6a dd 97 16 58 db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.13574219,  0.01074219,  0.37597656, ..., -0.5       ,
           -0.38964844, -0.39746094]), textin=CWbytearray(b'1a a4 59 62 96 65 68 9b 65 eb cd 8c 22 69 1c 36'), textout=CWbytearray(b'b5 ee 45 51 d6 23 2a a7 c9 2f 6e 16 b7 af cd 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17871094,  0.04589844,  0.390625  , ..., -0.5       ,
           -0.43359375, -0.43164062]), textin=CWbytearray(b'9a eb c1 b3 23 fd 43 ec 25 28 78 86 82 a6 ca 34'), textout=CWbytearray(b'24 63 e7 c9 0f 94 63 80 ed ec 9d e9 10 88 60 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17773438,  0.04492188,  0.40136719, ..., -0.5       ,
           -0.39941406, -0.40527344]), textin=CWbytearray(b'af b2 4e be 6f 43 93 f9 05 a1 d1 ba ca 6c a9 ea'), textout=CWbytearray(b'95 0a 22 3a a7 20 ed 56 6f 3b a1 30 7f 54 e4 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11621094, -0.00097656,  0.36621094, ..., -0.5       ,
           -0.39550781, -0.40332031]), textin=CWbytearray(b'00 9c ad c0 8a 5d 5d da 4e 66 2a c3 d1 53 88 55'), textout=CWbytearray(b'03 01 d1 ab 60 b2 e9 c9 ce 6c e3 a3 40 cf f6 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.17089844,  0.03808594,  0.39941406, ..., -0.5       ,
           -0.38769531, -0.39941406]), textin=CWbytearray(b'cf ab a4 38 51 ba 5d 74 39 e5 7e 1b e4 d2 be fc'), textout=CWbytearray(b'59 d8 b1 10 aa f2 fd 79 74 ac ba 91 65 1c 5e 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16015625,  0.02929688,  0.38964844, ..., -0.5       ,
           -0.44140625, -0.43847656]), textin=CWbytearray(b'2d 68 88 cd f8 37 f9 76 bc 10 21 ed 3d f3 bf 71'), textout=CWbytearray(b'81 11 c2 fd ad 7b b1 b6 a7 13 0f 6c c4 18 65 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.16796875,  0.03222656,  0.38476562, ..., -0.5       ,
           -0.39355469, -0.40332031]), textin=CWbytearray(b'36 c3 3a c5 e1 b8 5d 13 5d 89 48 7a fa 23 d3 f4'), textout=CWbytearray(b'bd 3a b6 45 0d 8e 43 91 e9 5d 52 ee 47 57 3b b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.11523438, -0.00488281,  0.34667969, ..., -0.5       ,
           -0.390625  , -0.40234375]), textin=CWbytearray(b'f2 98 bf 1d 90 e8 6b 8e 4b d0 91 98 49 30 09 50'), textout=CWbytearray(b'78 68 5f 38 f3 f7 85 b3 46 65 09 71 58 f8 59 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18359375,  0.04785156,  0.40332031, ..., -0.5       ,
           -0.39941406, -0.40722656]), textin=CWbytearray(b'd2 75 d0 59 c8 34 9f 3a dc 9f a0 83 56 1b 05 be'), textout=CWbytearray(b'37 04 a2 37 b9 cf d6 12 66 ef 4a 76 ed d1 57 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.18457031,  0.03808594,  0.39648438, ..., -0.5       ,
           -0.4140625 , -0.41796875]), textin=CWbytearray(b'60 7f 10 56 37 10 ef 3e 5e 43 e0 db 84 44 18 11'), textout=CWbytearray(b'62 f3 77 ae 62 97 4b 24 4a b6 94 b9 81 cb c5 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.1953125 ,  0.05859375,  0.39453125, ..., -0.5       ,
           -0.40820312, -0.41601562]), textin=CWbytearray(b'56 a0 94 7e 96 fb 3e c2 6d ed 8b 05 ab 46 24 96'), textout=CWbytearray(b'3d 67 12 26 37 e9 4f a0 b0 da 56 d1 6f c0 c3 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.09375   , -0.02246094,  0.3515625 , ..., -0.5       ,
           -0.40820312, -0.41503906]), textin=CWbytearray(b'9b df 2c e8 7d 92 62 af ea ca 00 d8 16 5f 82 56'), textout=CWbytearray(b'44 6b 58 2d d5 42 2b 1a 9b 1b f2 d3 65 10 14 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))


Analysis
--------

As we discussed above, our goal here is to find the biggest difference
of means out of the possible subkey values we could have. First, we'll
get some values and functions that will be useful for our calculations.
We'll be using ``intermediate()`` later to get the output of the SBox
from a plaintext and key input.


**In [7]:**

.. code:: ipython3

    numtraces = np.shape(trace_array)[0] #total number of traces
    numpoints = np.shape(trace_array)[1] #samples per trace
    
    sbox = (
        0x63, 0x7c, 0x77, 0x7b, 0xf2, 0x6b, 0x6f, 0xc5, 0x30, 0x01, 0x67, 0x2b, 0xfe, 0xd7, 0xab, 0x76,
        0xca, 0x82, 0xc9, 0x7d, 0xfa, 0x59, 0x47, 0xf0, 0xad, 0xd4, 0xa2, 0xaf, 0x9c, 0xa4, 0x72, 0xc0,
        0xb7, 0xfd, 0x93, 0x26, 0x36, 0x3f, 0xf7, 0xcc, 0x34, 0xa5, 0xe5, 0xf1, 0x71, 0xd8, 0x31, 0x15,
        0x04, 0xc7, 0x23, 0xc3, 0x18, 0x96, 0x05, 0x9a, 0x07, 0x12, 0x80, 0xe2, 0xeb, 0x27, 0xb2, 0x75,
        0x09, 0x83, 0x2c, 0x1a, 0x1b, 0x6e, 0x5a, 0xa0, 0x52, 0x3b, 0xd6, 0xb3, 0x29, 0xe3, 0x2f, 0x84,
        0x53, 0xd1, 0x00, 0xed, 0x20, 0xfc, 0xb1, 0x5b, 0x6a, 0xcb, 0xbe, 0x39, 0x4a, 0x4c, 0x58, 0xcf,
        0xd0, 0xef, 0xaa, 0xfb, 0x43, 0x4d, 0x33, 0x85, 0x45, 0xf9, 0x02, 0x7f, 0x50, 0x3c, 0x9f, 0xa8,
        0x51, 0xa3, 0x40, 0x8f, 0x92, 0x9d, 0x38, 0xf5, 0xbc, 0xb6, 0xda, 0x21, 0x10, 0xff, 0xf3, 0xd2,
        0xcd, 0x0c, 0x13, 0xec, 0x5f, 0x97, 0x44, 0x17, 0xc4, 0xa7, 0x7e, 0x3d, 0x64, 0x5d, 0x19, 0x73,
        0x60, 0x81, 0x4f, 0xdc, 0x22, 0x2a, 0x90, 0x88, 0x46, 0xee, 0xb8, 0x14, 0xde, 0x5e, 0x0b, 0xdb,
        0xe0, 0x32, 0x3a, 0x0a, 0x49, 0x06, 0x24, 0x5c, 0xc2, 0xd3, 0xac, 0x62, 0x91, 0x95, 0xe4, 0x79,
        0xe7, 0xc8, 0x37, 0x6d, 0x8d, 0xd5, 0x4e, 0xa9, 0x6c, 0x56, 0xf4, 0xea, 0x65, 0x7a, 0xae, 0x08,
        0xba, 0x78, 0x25, 0x2e, 0x1c, 0xa6, 0xb4, 0xc6, 0xe8, 0xdd, 0x74, 0x1f, 0x4b, 0xbd, 0x8b, 0x8a,
        0x70, 0x3e, 0xb5, 0x66, 0x48, 0x03, 0xf6, 0x0e, 0x61, 0x35, 0x57, 0xb9, 0x86, 0xc1, 0x1d, 0x9e,
        0xe1, 0xf8, 0x98, 0x11, 0x69, 0xd9, 0x8e, 0x94, 0x9b, 0x1e, 0x87, 0xe9, 0xce, 0x55, 0x28, 0xdf,
        0x8c, 0xa1, 0x89, 0x0d, 0xbf, 0xe6, 0x42, 0x68, 0x41, 0x99, 0x2d, 0x0f, 0xb0, 0x54, 0xbb, 0x16)
    
    def intermediate(pt, keyguess):
        return sbox[pt ^ keyguess]

Our first step here will be separating our traces into different groups
based on the SBox's output. As mentioned earlier, we're separating based
on the least significant bit, but really any bit would work (as a test,
you can change this and see if the attack still works):

.. code:: python

    one_list = []
    zero_list = []
    for tnum in range(numtraces):
        if (intermediate(textin_array[tnum][subkey], kguess) & 1):
            one_list.append(trace_array[tnum])
        else:
            zero_list.append(trace_array[tnum])

Then calculate the difference of means:

.. code:: python

    one_avg = np.asarray(one_list).mean(axis=0)
    zero_avg = np.asarray(zero_list).mean(axis=0)
    mean_diffs[kguess] = np.max(abs(one_avg - zero_avg))

We'll need to repeat this with each possible key guess and then pick the
one with the highest difference of means:

.. code:: python

    guess = np.argsort(mean_diffs)[-1]
    key_guess.append(guess)
    print(hex(guess))
    print(mean_diffs[guess])

Finally, altogether and attacking all of the subkeys:


**In [8]:**

.. code:: ipython3

    from tqdm import tnrange
    import numpy as np
    mean_diffs = np.zeros(255)
    key_guess = []
    known_key = known_keys[0]
    plots = []
    for subkey in tnrange(0, 16, desc="Attacking Subkey"):
        for kguess in tnrange(255, desc="Keyguess", leave=False):
            one_list = []
            zero_list = []
            
            for tnum in range(numtraces):
                if (intermediate(textin_array[tnum][subkey], kguess) & 1): #LSB is 1
                    one_list.append(trace_array[tnum])
                else:
                    zero_list.append(trace_array[tnum])
            one_avg = np.asarray(one_list).mean(axis=0)
            zero_avg = np.asarray(zero_list).mean(axis=0)
            mean_diffs[kguess] = np.max(abs(one_avg - zero_avg))
            if kguess == known_key[subkey]:
                plots.append(abs(one_avg - zero_avg))
        guess = np.argsort(mean_diffs)[-1]
        key_guess.append(guess)
        print(hex(guess) + "(real = 0x{:02X})".format(known_key[subkey]))
        #mean_diffs.sort()
        print(mean_diffs[guess])
        print(mean_diffs[known_key[subkey]])


**Out [8]:**







.. parsed-literal::

    0x2b(real = 0x2B)
    0.015967338624078675
    0.015967338624078675
    






.. parsed-literal::

    0x7e(real = 0x7E)
    0.015462321912760757
    0.015462321912760757
    






.. parsed-literal::

    0x15(real = 0x15)
    0.01683474530161816
    0.01683474530161816
    






.. parsed-literal::

    0x16(real = 0x16)
    0.016854545296045687
    0.016854545296045687
    






.. parsed-literal::

    0x28(real = 0x28)
    0.016965786864668098
    0.016965786864668098
    






.. parsed-literal::

    0xae(real = 0xAE)
    0.017114486425976183
    0.017114486425976183
    






.. parsed-literal::

    0xd2(real = 0xD2)
    0.01605757287721926
    0.01605757287721926
    




With that done, we should now have the correct key:


**In [9]:**

.. code:: ipython3

    print(key_guess)
    print(known_key)


**Out [9]:**



.. parsed-literal::

    [43, 126, 21, 22, 40, 174, 210, 166, 171, 247, 21, 136, 9, 207, 79, 60]
    [ 43 126  21  22  40 174 210 166 171 247  21 136   9 207  79  60]
    


We can also plot the difference of means for a few of the correct subkey
bytes:


**In [10]:**

.. code:: ipython3

    from bokeh.plotting import figure, show
    from bokeh.io import output_notebook
    
    output_notebook()
    p = figure()
    p.line(range(numpoints), plots[0], line_color='green')
    p.line(range(numpoints), plots[1], line_color='red')
    p.line(range(numpoints), plots[15], line_color='blue')
    show(p)


**Out [10]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1001">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="79da8da7-ca62-4c12-923b-55f7491b099d"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#79da8da7-ca62-4c12-923b-55f7491b099d');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof root._bokeh_onload_callbacks === "undefined" || force === true) {
        root._bokeh_onload_callbacks = [];
        root._bokeh_is_loading = undefined;
      }
    
      var JS_MIME_TYPE = 'application/javascript';
      var HTML_MIME_TYPE = 'text/html';
      var EXEC_MIME_TYPE = 'application/vnd.bokehjs_exec.v0+json';
      var CLASS_NAME = 'output_bokeh rendered_html';
    
      /**
       * Render data to the DOM node
       */
      function render(props, node) {
        var script = document.createElement("script");
        node.appendChild(script);
      }
    
      /**
       * Handle when an output is cleared or removed
       */
      function handleClearOutput(event, handle) {
        var cell = handle.cell;
    
        var id = cell.output_area._bokeh_element_id;
        var server_id = cell.output_area._bokeh_server_id;
        // Clean up Bokeh references
        if (id != null && id in Bokeh.index) {
          Bokeh.index[id].model.document.clear();
          delete Bokeh.index[id];
        }
    
        if (server_id !== undefined) {
          // Clean up Bokeh references
          var cmd = "from bokeh.io.state import curstate; print(curstate().uuid_to_server['" + server_id + "'].get_sessions()[0].document.roots[0]._id)";
          cell.notebook.kernel.execute(cmd, {
            iopub: {
              output: function(msg) {
                var id = msg.content.text.trim();
                if (id in Bokeh.index) {
                  Bokeh.index[id].model.document.clear();
                  delete Bokeh.index[id];
                }
              }
            }
          });
          // Destroy server and session
          var cmd = "import bokeh.io.notebook as ion; ion.destroy_server('" + server_id + "')";
          cell.notebook.kernel.execute(cmd);
        }
      }
    
      /**
       * Handle when a new output is added
       */
      function handleAddOutput(event, handle) {
        var output_area = handle.output_area;
        var output = handle.output;
    
        // limit handleAddOutput to display_data with EXEC_MIME_TYPE content only
        if ((output.output_type != "display_data") || (!output.data.hasOwnProperty(EXEC_MIME_TYPE))) {
          return
        }
    
        var toinsert = output_area.element.find("." + CLASS_NAME.split(' ')[0]);
    
        if (output.metadata[EXEC_MIME_TYPE]["id"] !== undefined) {
          toinsert[toinsert.length - 1].firstChild.textContent = output.data[JS_MIME_TYPE];
          // store reference to embed id on output_area
          output_area._bokeh_element_id = output.metadata[EXEC_MIME_TYPE]["id"];
        }
        if (output.metadata[EXEC_MIME_TYPE]["server_id"] !== undefined) {
          var bk_div = document.createElement("div");
          bk_div.innerHTML = output.data[HTML_MIME_TYPE];
          var script_attrs = bk_div.children[0].attributes;
          for (var i = 0; i < script_attrs.length; i++) {
            toinsert[toinsert.length - 1].firstChild.setAttribute(script_attrs[i].name, script_attrs[i].value);
          }
          // store reference to server id on output_area
          output_area._bokeh_server_id = output.metadata[EXEC_MIME_TYPE]["server_id"];
        }
      }
    
      function register_renderer(events, OutputArea) {
    
        function append_mime(data, metadata, element) {
          // create a DOM node to render to
          var toinsert = this.create_output_subarea(
            metadata,
            CLASS_NAME,
            EXEC_MIME_TYPE
          );
          this.keyboard_manager.register_events(toinsert);
          // Render to node
          var props = {data: data, metadata: metadata[EXEC_MIME_TYPE]};
          render(props, toinsert[toinsert.length - 1]);
          element.append(toinsert);
          return toinsert
        }
    
        /* Handle when an output is cleared or removed */
        events.on('clear_output.CodeCell', handleClearOutput);
        events.on('delete.Cell', handleClearOutput);
    
        /* Handle when a new output is added */
        events.on('output_added.OutputArea', handleAddOutput);
    
        /**
         * Register the mime type and append_mime function with output_area
         */
        OutputArea.prototype.register_mime_type(EXEC_MIME_TYPE, append_mime, {
          /* Is output safe? */
          safe: true,
          /* Index of renderer in `output_area.display_order` */
          index: 0
        });
      }
    
      // register the mime type if in Jupyter Notebook environment and previously unregistered
      if (root.Jupyter !== undefined) {
        var events = require('base/js/events');
        var OutputArea = require('notebook/js/outputarea').OutputArea;
    
        if (OutputArea.prototype.mime_types().indexOf(EXEC_MIME_TYPE) == -1) {
          register_renderer(events, OutputArea);
        }
      }
    
      
      if (typeof (root._bokeh_timeout) === "undefined" || force === true) {
        root._bokeh_timeout = Date.now() + 5000;
        root._bokeh_failed_load = false;
      }
    
      var NB_LOAD_WARNING = {'data': {'text/html':
         "<div style='background-color: #fdd'>\n"+
         "<p>\n"+
         "BokehJS does not appear to have successfully loaded. If loading BokehJS from CDN, this \n"+
         "may be due to a slow or bad network connection. Possible fixes:\n"+
         "</p>\n"+
         "<ul>\n"+
         "<li>re-rerun `output_notebook()` to attempt to load from CDN again, or</li>\n"+
         "<li>use INLINE resources instead, as so:</li>\n"+
         "</ul>\n"+
         "<code>\n"+
         "from bokeh.resources import INLINE\n"+
         "output_notebook(resources=INLINE)\n"+
         "</code>\n"+
         "</div>"}};
    
      function display_loaded() {
        var el = document.getElementById("1001");
        if (el != null) {
          el.textContent = "BokehJS is loading...";
        }
        if (root.Bokeh !== undefined) {
          if (el != null) {
            el.textContent = "BokehJS " + root.Bokeh.version + " successfully loaded.";
          }
        } else if (Date.now() < root._bokeh_timeout) {
          setTimeout(display_loaded, 100)
        }
      }
    
    
      function run_callbacks() {
        try {
          root._bokeh_onload_callbacks.forEach(function(callback) {
            if (callback != null)
              callback();
          });
        } finally {
          delete root._bokeh_onload_callbacks
        }
        console.debug("Bokeh: all callbacks have finished");
      }
    
      function load_libs(css_urls, js_urls, callback) {
        if (css_urls == null) css_urls = [];
        if (js_urls == null) js_urls = [];
    
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.debug("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.debug("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = css_urls.length + js_urls.length;
    
        function on_load() {
          root._bokeh_is_loading--;
          if (root._bokeh_is_loading === 0) {
            console.debug("Bokeh: all BokehJS libraries/stylesheets loaded");
            run_callbacks()
          }
        }
    
        function on_error() {
          console.error("failed to load " + url);
        }
    
        for (var i = 0; i < css_urls.length; i++) {
          var url = css_urls[i];
          const element = document.createElement("link");
          element.onload = on_load;
          element.onerror = on_error;
          element.rel = "stylesheet";
          element.type = "text/css";
          element.href = url;
          console.debug("Bokeh: injecting link tag for BokehJS stylesheet: ", url);
          document.body.appendChild(element);
        }
    
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var element = document.createElement('script');
          element.onload = on_load;
          element.onerror = on_error;
          element.async = false;
          element.src = url;
          console.debug("Bokeh: injecting script tag for BokehJS library: ", url);
          document.head.appendChild(element);
        }
      };var element = document.getElementById("1001");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1001' but no matching script tag was found. ")
        return false;
      }
    
      function inject_raw_css(css) {
        const element = document.createElement("style");
        element.appendChild(document.createTextNode(css));
        document.body.appendChild(element);
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.2.0.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.2.0.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.2.0.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.2.0.min.js"];
      var css_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.2.0.min.css", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.2.0.min.css", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.2.0.min.css"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {} // ensure no trailing comma for IE
      ];
    
      function run_inline_js() {
        
        if ((root.Bokeh !== undefined) || (force === true)) {
          for (var i = 0; i < inline_js.length; i++) {
            inline_js[i].call(root, root.Bokeh);
          }if (force === true) {
            display_loaded();
          }} else if (Date.now() < root._bokeh_timeout) {
          setTimeout(run_inline_js, 100);
        } else if (!root._bokeh_failed_load) {
          console.log("Bokeh: BokehJS failed to load within specified timeout.");
          root._bokeh_failed_load = true;
        } else if (force !== true) {
          var cell = $(document.getElementById("1001")).parents('.cell').data().cell;
          cell.output_area.append_execute_result(NB_LOAD_WARNING)
        }
    
      }
    
      if (root._bokeh_is_loading === 0) {
        console.debug("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(css_urls, js_urls, function() {
          console.debug("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="7dc1dcc0-c485-4ac9-8393-223d5c46e45e" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="e3309702-2604-4480-bb01-ff6d9b438ffc"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#e3309702-2604-4480-bb01-ff6d9b438ffc');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"fbc68a27-a8c8-409f-9864-0f36a079e2bf":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"},{"id":"1042","type":"GlyphRenderer"},{"id":"1047","type":"GlyphRenderer"}],"title":{"id":"1049","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"line_color":"green","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"AFe7POFgQj/AL7ishlxHPwAMKiIBGzc/ALxMpOpfTD8Azc3SZ8dFPwDAX7O5Jjc/ADDmlNuqID8AiCH/3JU8PwCo8KtqhhU/XF9QRmjPJz8AwG1+oOvwPgDsM2+C3zM/APTdXYQQKT8AhJlzB5w7PwB0HzrLtT0/AOjGzRrLLT8AIOG/d7oVPwB0sO3CYBI/ADAdYkDTFT8AAAAAAAAAAABYl+xokS8/AOQ2Ap3+OD8AqPcAPdsCPwAAAAAAAAAAAJiVVl2UJj8AwJe8kmIIPwCOv5b26C8/AIzd0mE2MD8A7JZLDZojPwBAhgayWuw+AAATbzl5CT8AWFYu3PVIPyxYCzJMcEg/ANf4V/5wSD8AeMrKsS0pPwC47Zd8eD8/ALyIJJV6NT8A6R17bMM8PwCo7TPaQyI/AICLmqpR4T4AHNQZPksbP4C8EvYoIjs/AGh0lBKgLT8AUoTWU+s1PwAoYZorsBw/AOS0Yb7rKj8AwOW4QVwNPwBwCDazwCA/ABiCHNalAT8A7vKhAgYRPwDESLbnEDE/AABBLsZuGT8A/YcLQmkjPwB4vUwAuQQ/AAC+cJkiCz8AKyeFkr9CPwA7MUNQW0U/4BSrB2YqRT8AwKwdt3UlPwAAAAAAAAAAADgk44RiKT8AgE2c+1UXP0CkWnIGowk/AAAAAAAAAAAAe2+DVxtbPwCyKLq7olg/gOqx5NzpUD8AAAAAAAAAAADg4acH0jI/AAC0UVNG0j5wwwzs5D9BPwAAAAAAAAAAwBF/AVR2ZD+AjziHcltfPwD8Op7oelo/gB8xQnR4ZT+A/EpouPdfPxCkjWd6eF8/APb6urB+WD8AgkSMB+JGPwCG2GA5+zM/AFhopNpTOT8AsBmeQOofPwDuQxl3wEQ/YNVsY+NWdT9QwCUU66aEP+D8nTN3HoU/QMcuSyc3ZT+As7K5tqRYP2D/ceUB1Eg/APbeojMjRD8AgBsKHQ8BPwAewmjadEg/AJY0xeFGOj8ArzgX1QxBPwAl2h3xD1M/AE4o6SpSUT9Ap9LbJ6tMPwDwwiWtE0Q/ALxOWVRASj+ASZPjjSNEP+iF/T5SFEo/AMB7OVQUQz8AxZGOiaRFPwB5OKKu9UI/QBGVtCW6Qj8ASMMc0T4pPwCY/VDvMzs/AADqEclDET9AEjFsynEYPwCA5MBc2BQ/AF4IYte4MD8AZJe0htkuPwB6Jtkc4iQ/AHB/QWnYEz8AAAAAAAAAAAD4e7e1qo8/oG+lw1vYhz8oeqHve+aCPwAAAAAAAAAAQK4IYYGhhj9AbaSGWaR4P7QUGYlznYU/IOIKBi1JgT9gCq/bRRx6P2DYoHrPA3Q/gMRW286Lbj+AYAILuF9qP4RaVwdjlmQ/QDgXKKB2XT8A2rBQNh9aPwDILh+1/FY/AAevvNH/Uj8AKuJgHKNIPwA4VGeu1CY/AJCZ/WPMMT8AreDiKKo9PwDQljlJWS8/ANhFl/AEPz8AQAfs70g/PwCj8IWwikQ/wPeAHq5GRT8AVusGeUlGPwBkCMwxLDs/APg7duF6QD8A3lnoEj8mPwAQ4y2XQSU/AImyxbG+XT8IZd7PES5YPwA7dJKB+0g/AFAbUX2hLD8AlOi3wUcyPwACo1lSgzs/AJ4K/QcDPj8AyPNLH0g7PwAAAAAAAAAAAJRfAWCYST8AJLdD60xIPwCVn5wN3T0/AKBdp4QOFT8AVmAXVwpFPwCXUdbA3kI/AAaFSVlwQT8AAAAAAAAAAADn/2vaL1E/AIgs1xdoRz+3R1vYv8c6PwAA5gf8ufg+AK69oUs0Mj8AFIJVRv4rPwBoERBsWjo/AJqJJS+yYT/A+vM2EFBgP8BTzAocGFg/AJvbNttUVj8AdG/8fatGP6xIb5230kY/AJV4b/sWTD8ACkLSn9VHPwC9Rl6zZE4/ACASORkpTz9AOKrm8FZUPwBUxetdfEo/ANA8ez7RRT8ADnE0UXBJPyB839mpCkc/AHi73StwSD8AoJXRxoYQPwA87U9FVEM/APr0kfh/PD8AgA9yF3o/PwBA/8LnEi0/AHw/9hnLIT+6QWg0el87PwDgI+c2mSA/AIBTCDuP7D4ANpUSXvIgPwB0xDYRGxk/ALCyWf6vFz8AGFxnKwxQP0CXhxGbfVA/gJ6SdQB/Qj8A1tq4fo9EPwD3X7Vx7FI/gAlzxlz0TD9y3kvin+BGPwDXR+s6cVE/AA+fWQBXTT8AZ9gI4CJGPwAUr5fMxUY/ANr56Yj6Qz8AAAAAAAAAAACQTE/GBTI/AL72Qxz6SD+AtV8jpbdDPwAAAAAAAAAAAIAMr8cBDz8AMK8ADvwQP4ARwsXoh0A/ALhHbrU9ND/Q2Et4RW1EPwDJzjX9VUE/AAm7DHT+RT8AoKaKL6A9P4AM8RMvBCw/ACDVYtedNj8AMEa0pwoiPwCcbc37Hjg/AJy1qkZWKD8AYAn8DqHhPgB4d6h1ESU/AKDiKSe2JT8AVNYEf4oUPwDoMCUy1iM/APAXS27lKT8A1Cabk9M0PwCk/L8ZbEQ/gG8tj+eJMj8AGMst6JE4PwDQ2/WAQxo/ALBrLhmeEz8AQMHMSfHrPgB0B8jtEjY/AGSdf6IkQT8wZXj7YqAnPwBXJwHYlDQ/AJASplWHNz8AntSVuyk+PwCwyORH+T0/QL8LSXJYST8AACATpelGPwAAAAAAAAAAAPoZnIPTQD8AjNS04QNCPwBL+51pizc/AAAASJTWMD4AXesHB+pPPwBpzYLRhko/AG0SpOsDRj8AAAAAAAAAAAAA1VT9nCI/ALDhnDsuLz8gKnnMUhtDPwAAAAAAAAAAAMC2fsp58z4AUGhk/fosPwAAad26vhU/AMrie+9wUT+ANYqenjNIP8B2l/Z/c0Y/AHBzVzcNSz8AOMpu+bE5P7BDbjzwQjg/AIQ2R0XLFD8AVALzYgEwPwD88m//rSg/AFgR7ebcLD8Alh4GeWQoPwBArMuPkBU/ALDodvLSQD8AiIYqYf0vP8Drn04D8Dc/AIih0bwLIz8AoPnW6NYPPwBA+lvI1ew+ALTjXNUtJD8AaHDxzoY3PwCKuHqU7EQ/AN5hIgPTPD8oo8EsOu8xPwDsNytbtTM/AL6M028yQT8AiA6ScRsQP4AWljwOxSg/AKDaF+Y9AD8A6CwdDCo9PwCVrxFVhEE/QO7y9JW7Lj8AuM4DhZonPwDcq+bhY04/AClnErRFST9AtyVTE75BPwDAFUXK6jk/AFQxc/oXMj8AYP/niNEdPwAARQy48bg+AKCe6s7YPD8AAAAAAAAAAAAcNYf0vE4/AORx8LyPRj+AdRHGOsVGPwAAAAAAAAAAAIwbBIzxPj8AsIWiFHcuPwBWoF18eVM/AG5dgKDtTT9AQUGNs2hEP4DVZ09+8EU/AJzfzYfPMj8AjmbWnlxBP0BBPgrzBio/ACg0Qd4qHT8A0PqtFtwnPwBUX+br9DQ/APAC2u8UIj8Amt1mYOUjPwCw59OmDBQ/AOTMCbBHRj+AniKxkP1GP0D/lVybHks/AJxUnxbYNj8AbGGqllVNPwAcgrCtM0o/wJCVi+OsQD8A4PlrEas5PwDap7G73Ec/wNUme7g3TD8AoudqjPc8PwDAgBJUAkA/AMoHB3XUST/goX/v/0c8PwDm0tkcUjc/AIzL7kVGSD+ABpcISQVWPwBwdvWBS08/kCZhqxMOST8A6Ob47fFHPwAAAAAAAAAAAJDqgdakOT8AhH5j37pHP8DMdj85LEI/AAB45Xohqj4AV2YTNk9EPwDAEvoSYkM/AGaUH3eaMT8AAAAAAAAAAABY+Tf3Yi0/ABgBIy2ZKD+gPzuWJeNDPwAAXN1vsc0+AGDpAf9zFD8AsHVxbUcEPwCAM5hgcfo+APCgr8p/Tj+Au0rigmFKP4BpPLmgvko/ACT+AqjsPD8Ahsgy7mpAPzBr2eNb4zE/ANq3BijoIj8A3JfoeQAwPwDbCP+WskI/AHsirTFaTT/AAJBdYDpFPwCQNSaCtUU/AFzB3X+RRj8AvMOYANs1PyCKs9RMGDU/ADgqeoEUQD8AuAvXQ2UmPwA6uVopYz4/ADD2jXkBLz8AqP6T92gkPwCd1xE2rFE/gOvmOTLKSj9I1S+7CCYzPwCYksC/tC8/ACx/Igv1Pj8AL6IDV5c4P4AUz0b2myM/ACj9++ASNj8AkEtFfGspP4DSjmJtWUQ/4IwUAk1SPz8A/L/87Gc5PwAOMUYQdkc/gMWysMnnQz8eedTYWJ00PwDQcctC8ig/AJSu1kfwQz8Aqn8e9UI0PwAN3yN8dTY/AADnx3g6ET8AAAAAAAAAAACc3VQRCDE/ABjdaej2JD8ACsSjQeIaPwAAAAAAAAAAAABVS7gn5z4AgC1AZTUAPwBiKgPFBy4/ALD2bXLzIz/wgOEsjfcqPwDiw/gtMzQ/ADixsCYdMD8AZPYMQpdCPyBLh3pWaEc/ADhaVQFbQD8AvCp3+QI/PwC7hzwbnE0/ADkV8dQGRT8A+VOrZIU1PwCAmcco8vI+AABsDnYLLD8AGj6vFm4qP4BmvZQ8LjA/AMC/U87YCj8AOPqXu+44PwB2cRq0XkU/gCwGcd46Qj8ASC0/OxAqPwAAHBxnmyQ/ADI2kYyZFT8AyISPBEEoPwCIUwWRrS8/AOiRamBNKT/k/q5E680qPwAO8gdAgSE/AJDNUhGRGD8ATcIjbwJFPwAOjyu1xjE/gAtXYpLCIT8AIPigWHRDPwAAAAAAAAAAAMyb+4GJNz8AiIL9DvYpP4D3k5wBHy4/AAAAAAAAAAAAynmeNLJDPwAEj2OXfiI/AIiGrMKMGj8AAAAAAAAAAAAsUYOn3VE/AGriPjauSj8gMZUuT05JPwAAAAAAAAAAAExPgmWKMj8A7AgK7vIkPwAQhWGuzhc/AMLHaETPQz+A43utxWlEPwA7o3aUJT0/ABBi9cWdND8AhtIsUd1IP4BtB4W0Gks/gJgqbkmgTj8AvgzENSRAPwAOArA/QkI/AFzqdBXhLT+AchlDPTA1PwBQ/0SX5D0/AMUgrIuLTj8ACIfc8pROP+C9UdfD4j0/ALgHXqSHOT8A+PyJPbwpPwDwhF9ES0Y/ANp5qYvyPT8A5N92G8JKPwDQgD97/ho/ADxh/vQFJT94wvOuDLsTPwAgMV3XMww/AHCIVUeMRD+AZb0TQh5EPwBsaiLwHPA+AABsah2fHT8AQCgaxyoDPwAAccOa5MI+AO2ePAz3CT8AOGXEb1oiPwCgHD6ASBQ/AAqzfJQVMz+Az2YJQT0pPwAw+Inf+DQ/AGg7ju3nFz8A+MqbkMAWPwAQ6YTduwo/AOAxtXn9AD8AAAAAAAAAAADXsJU9FlI/AH5lU+DvTj9AjVblrWRAPwAAAAAAAAAAAHRWgkuONj8AAK766vsgPwDTJYBh208/AMiAMGGfQz/gKZY6Z+dGPwBREA0aXTI/AOxJXbmbMj8AfIv88+pCP8AMmBEsIEQ/AMJDrhlJPz8AyCvAuXYlPwCrGvVl+0A/AEI0ikTFRj8AqnD/RAxAPwDw9orbtjA/AEAEPiQsHT8AUGXZaBnyPoBpV0N90DM/AEwWGrreMj8A9mOkAtwzPwDQ93/eTzM/AL5BKnNiNj8AAAFFJHbsPgD4y4hbXiU/APVCrjTTKz8AoKv+JdoWPwAARejQRew+AEBIiqFIFT8AaJt9hO4NPwB06+Io0R4/AHitigS8Iz8A2Zo+OodKPwCvJKy2lkE/gDn9vUYcQD8ATGpES3U3PwAAAAAAAAAAANDacM22Fz8AkK8tRuAZPwBuaWrSuDY/AAAAAAAAAAAA6BY1jawrPwDab3DZxzo/ALBFh/rCJz8AAAAAAAAAAAC6FBXGVEA/AHhw1/lrOj/AiSLTEncxPwAAWqZW/94+AGgDU+oyMz8ADlY1Ee0xPwAY7upuWCE/AGpqU7MWVT+Agt7B+tJTPyCsUWb8blE/AP7gICLLTz8AKE5qGs4qPyBOpD/oKSE/APaJpfpLJD8AoKf3P4wDPwCI2llDUyM/ALdiOEiHRj+gHHKf4Z9UPwBt0mIXVFI/AMyM3NFSSz8AwosimK1GP0Aa+3DNKys/APBXpowTGD8AIPNc5dUDP4AyQwlfrkE/AJZUJskmMD8AhN0/8ScmPwBwB8tJsjw/gAm6oKNwQz9gNJgexsJEPwCg9VTn2Ag/AOjF9xbrKz8AzPoghS0mP4DILdoF6iM/AIBsbttsEz8A0OwDcjI9PwCY6QJQujU/MA39eQogQj8AbC13uUw3PwAsjpRO4VA/AMm90+oQRz9Yd0vLefhFPwDw5f+y1jY/AJQGVUwJPj8AEQgX4HlCPwAxOo8Qxz4/ABZ/i4mFQT8AAAAAAAAAAACqCQ0I50M/AKTtgfUYNj8APL6Ba0cyPwAAAAAAAAAAAHDZ1GilFT8AAGC1W7PpPgD01p5XSCE/AKCYRN/oKT+AkxZB7EEoPwACBuv25jI/ACTBAPQmJj8A9rYrIYtIP8Bjl0EYiEA/APs+GBb5RD8ArpOvzbQ8PwDPv9/R5ko/ACHmiYRqTj8AHFX+FLpHPwAQmRoPOxY/ACDOyNYwDj8A0KygzcYkPwDy6ZZfURc/AKDuwVNsEj8AHO5UycsrPwDwDMdUaS4/ADYTBGJNMz8AEHZwZfIpPwBQXoUkZS4/AGd/0yQlKT8AHNodyu4XPwAoRHxO+i8/AOgLdYAXJD8ga97m2Os0PwA8b/0hhRU/AKD8e3n0Ij8A0tF5lSA0PwDCPZewJzY/AJosuPGNEz8AkD7PxZcQPwAAAAAAAAAAADhR4vhSJD8AEF234xwvPwDzzOafLQo/AAAAAAAAAAAAyQfeXjlePwAAXLh7X1c/ACR5QCjsTz8AAAAAAAAAAACs/bLDaTs/AALaiaQeQT+QI0geBHNQPwAAAAAAAAAAALuyJQY4Rz8AI+qtwpNAPwCMWPqoGCA/AFyjzu39Oj8AJqmvf8AoPwAQfI2GUjA/AIRt7Yh4Mj8ASGp4ag4zP0Cqp6GeeSU/AOi+WrreLD8AoEwbzo0hPwBIgeFklhA/AOAWRk7pND8A8st42n9HPwBgyfeUiTw/ADT5q1fQKD8APmI797lCP4D1cn8Smz4/AJTxuredNj8A+HP2EkgoPwBvqjT28kI/AJDddrqiLj8APPia7nckPwCQx+HO2jo/AFyE8/kIOz/EL+TQfow2PwCom4DKVDc/ALqTpVJXQj8A+nfPahpCP8CywCOlFj4/AAAku2DjBj8ABBjfk7U4PwBh15uB9EE/gPzdFSF6Mj8AtNy+EaI2PwAsK2UwcUM/AECMpM3KPj/gUE7pu0JDPwBEy2IxUEI/AKx9BKnPNT8AIPvOkAAAPwAd91e/ISA/AIArvtqPCj8AAAAAAAAAAACAuRlEtQc/AGgLAEkYKD8A/F2psTc2PwAAAAAAAAAAAEChWewSKD8AgKD9XwkTP4CsoJW9W0A/APj8m4yZPD8A6GOZq5sRPwBQTCAOZQI/AOCIzhqJIj8AJHUlJcJMP6DPs4A8qkM/AJxsdW+OSD8AK1j6Se5DPwAcutlhCzw/AL6zc/BJMT8Awyaa3hEwPwAA/1lYmhQ/ABSYTLi5OT8AECJmr/clPwAOt/yg8xE/ADz+7rExQD8AioYnBV5JPwBod9p4aUU/gKIQKUPCOz8AUPDVr5c6PwAAZFH6wtQ+AJCUe+HT6j4AQgDx9og2PwAAh0PKRy8/ADjik/vdMD/AhE41zL4pPwBwq1R5qi4/AKBhCV0uBT8AEL5kUMY7PwAbUhCTZUQ/kPX/N+K3QD8AlOJUC1BCPwAAAAAAAAAAAFRNQoY8Nj8AIOHbjzchP2AWoQiy+CU/AAAAAAAAAACA53m8j5FVPwDKD751OEw/AAp0syeSSz8AAAAAAAAAAAAgTm9EbC4/AKSF6ILtTD8AbOsXr+lUPwAAAAAAAAAAgOlDg6oSWD8ABZxB2sZUPwCWN2/DI1A/ADD7DKBaRz8ALxxZXbg7PwAJLOXPmDc/ABiMJyF2Rj8ASllCygNHP1Ro1A+ttEM/ALgAqLK+QD8AsFrzJ9RAPwCELp7QJSA/AI2D+8HxRD8An2mXbhhfPwDvEBMbjV4/AKIsS2KcUT8AdPzWHYRJP+AxjfECG0Q/ANDDAxAQNT8AgOgIEpsRPwCtWvvUMkY/ANKTsyxYPj8Aeo7a9GBAPwD8tJsKJzU/AJh5xCsILT8gAAe7Qog3PwAQD3wzrS0/AHRzxO0fND8A5F6IotQwP9CiKVqEqAM/AIBMdyffLD8AlPJZQEU2PwAUcE9aUiM/gDCdWrMjGj8AkPIFH+8uPwAcOS2UdUM/gIzUxclhRj8A///MR+YyPwDQdV+TzTI/AOCK6D/b9T4AwGLelRMlPwDw2QKkjfk+AFDW4xVOKj8AAAAAAAAAAACIfDToyi8/ANCQgQqMGT8AFDsfMc0APwAAAAAAAAAAAIC/Vo7z5D4AADD9DVfRPgD+8nmhLCY/AIyu85/LQj8Mh4IZAFI6PwAkvCnpWCk/ADRKDZNtOT8AvXzbOFtRP8DN6HnD1UA/AIHLu2YLQD8AnDp+rrQ5PwCKzzoMakg/AL9aCCGTQD8AB2xhCcFBPwC0fiW6Jzs/AABa+2QgJD8Av+HQqAkxPwCiHZAteTM/ABBvRnc3KT8Ats8fHhIzPwCYcxvgeCM/AFguG0VxHz8AXDg7Yt84PwDgd+cj9Cs/AEBjhvqPzz4Ajhtq/CQpPwCAqhBpIOs+AKyOMwP7Mj+Ydtie0/ExPwAoXo01Pwc/AECZKDd+ID8AWsHbinFGPwDso9sN7Do/kMUEBCHiOT8A2JRf2j4tPwAAAAAAAAAAAGRBzHeEOD8AxP1LOvk4P4D0cZeYvD4/AAAAAAAAAAAABnFzeQc5PwCwT0IOfTU/APCKoh+nDT8AAAAAAAAAAABs2myrbT4/AKBeUwvUOD8AVFLyb48rPwAAAAAAAAAAAKi7IAHtPz8ACB4QIDQlPwDAXuVipSo/ALqILELZUj+AmYxJxb9MPwBQ+63qaUU/AI6Fk6zVQD8AoG+wzFlEP1gB42AoOjc/AHYhRxkLKD8AfCEtHc8/PwDAeIXQuDs/ADPX4OxmTT+AHUmV3f5SPwBCn4NAF1M/gHJqk5BvUT8Ae9OwKIdLP6CJyNJcl04/ABQGj3Z0RD8AQJn5tuYJPwD0O2hVvCo/AKybEUZDKT8A+OHMgW8YPwA+zkbqBE0/ABi1cHTPPj9gZOHiv906PwDgaQQP8hY/AAqjM7+oRT8AZBtbbWJAP8r2s1XNc0A/AEi8jHJQPj8AkIiBBwkZPwBAgbW1AeI+gDwCo92oIj8AgMNJbZ4lPwDwqUaYYys/ADDlMq2bGz/g+NbcP4EVPwBAYlxg9vQ+AOh/sraXHz8AoCXSymsvPwCM/jH8EQk/AADxYfyW6T4AAAAAAAAAAAC44BUI5TU/AMh0nB4pJz8ARKJMlhAfPwAAAAAAAAAAAAAWL1Rz7j4AAKs3wqTrPgBuN6TU2CQ/AA6aVoNOQj8QE7hR0eRCPwB8WPKtdxQ/ACD93EXVKD8ATDcXzsZJP0DaXoMq9EY/AFYDfqerRD8ALHDLUeVGPwDO1okQR0s/AFxtzRFYNT8AYwbLenU2PwBIfADfaiE/AJDui/FwKD8AlM0XrBgmPwCQwkiFJOc+AEBsJSeQCz8AQu+pbiA+PwCwPsrCGjg/wBZ9ogAMQD8A0JCRi2ovPwBgeTKG9BQ/AMTUiiePIz8A+tjnmLYnPwCgz3o3BQs/ALhx9thJLz+Ae3gdgZ42PwCwb14Vh+Y+AOD/H9ubAD8AT/GxktdEPwBqOX3cczg/IFYAi9SXMj8ABG55YBRAPwAAAAAAAAAAAOwG/DgePD8AfMJrlT4zPwCBbVe8yjE/AABRdlt92z4AFif/+K05PwCR6VRVz0E/ABh2Y2fUNT8AAAAAAAAAAABSg0/5gFE/AL70snf1Sz+oc3wmDspXPwAA3ldBNe0+AL6LKFDsQz8AHFLafqxAPwBSoq3cpTU/ACZzXmwERD+Act2MflxAP4CgNFCl9i4/AOCpg6S5Hz8AAPWrG90fP0ANfnQarDE/AHz3REHOJz8A3EvUEyIxPwAULhV8SiQ/AMg9YioPLD8AmQX0q5oyPwDi9qkBkUQ/AMQ2i88JQT8AwR9oQ/hBPwC26oLZqDQ/ALRiU9JjMD8AAP6ddxcGPwCwKLN1wzk/AEAwQEygEz8A4OR3o6oNPwD45fvvt0E/ACI6polCRT/A0AXK8SU8PwCcHj8Q3j0/AKJWMUl9RT8Aqow+4+IzP6C9+eFmOB0/AHAXEDXfIT8ATrAHSjlBP4A90U/2NUI/oBnJcsZyPT8A4MrlmjQvPwD0t+/VjU8/ADKLIwR+UD+AkAVQUy5MPwDwf4g5fUE/AEZf5ytTQD8AAqvi64w/PwDgnTw9uiM/ALAR98BkEz8AAAAAAAAAAABoK2MlGCY/ALDqeZ6pFT8AAGpwhabUPgAAAAAAAAAAAADUgVyixj4AAAAAAAAAAABgWdbIsgI/AMCcmO7vIz8A0Jxhmtj8PgAAXNX6W/E+AED2p8N/7T4AJPzGi71NPwC/gCaY/0o/AFgeiX7NOT8A5nb1Lrg5PwAaQjXbikY/AFXpR23qQj8ACVXqgno+PwBgOQX2QUA/ACAzwDv/NT8A0OBvL7zwPgCgiaXCQts+ADjzblvUIT8AVNth1ScsPwB40UNJXhc/AGyZLSTCKz8A8Me9aCkuPwCAWm6n/zc/ACtt0TjyLT8AWN22rTQYPwCw0cD1sh8/AIAn0In+ED8AWUqoJT4aPwADFsFdAio/AKCwnSfPFz8AmnZ0I8A/PwDtwSUF+EA/gOmCbudRJD8AIEttpTsbPwAAAAAAAAAAADAeD1U5Ez8AkGrRn8kQP4C/wcmavi8/AAAAAAAAAAAA+HvInYhUPwCqxly30kw/AKwjTeQmQz8AAAAAAAAAAACMx+I1WkE/AEjM9LvZRT9Q9/HqckJBPwAAAAAAAAAAADZr6NaZVz8Ai39dz5dRPwDJpIxwDUQ/ACD+mE15Aj8ATi066lArPwBHGp6J2yY/AKj59VzzMT8AWAgHcIMiPwDBUUR69R4/AO1f3GtGMz8AuHzTZNswPwDewE+z1TA/ABgyl31IKz8AOXWCqDg+PwDMKsT7mko/ACaQlHf3Qz8APJTH9qA4P8BC34JpbzE/ABjIqDIQNj8AoAmEOVcXPwBLL2/AoEs/ACLTQzprOT8A0rxUZCYzPwDKSOCLTEI/ANeBeSETQj/A2D5TBg42PwDAHRWHLCE/AEDIsnATMD8AbE/y7J87P7g2O3HSZjE/ADAeyiYhKD8A4LqBO+sHPwAgXOZXHQc/ABDwOzYEMj8AoLEvslgzPwB4qEBkez0/AE1npgA3Oz8gqSS8EFQ8PwAAkv1Fv9Q+ADgoHJUpID8AmBSXsoAhPwC4ser4owk/ACABsOVoFT8AAAAAAAAAAADaQuVhyUc/AAjGECELNz8AuO+/VKorPwAAAAAAAAAAAEA9dpp+ET8AQBoUVMz7PoDb6srYw08/AESn02nIRD9gpDPG7uwzPwBvyc8zsDE/AIAzUn2XOj8AAJities+P8Dgeji63DI/AAb96+rQPj8ACvhNd3w3PwBwyEOIhkY/AEIcr5pPRj8A6VyNyn00PwCADn4G/Sk/AELUx62ZSD8ASc6sgVk6PwAoD61E6Tg/ANBo12b7Gz8AIR59JIlEPwDE6+1p2EM/gJxU2RTRQj8AugBhtqdAPwDAU/+bFDo/AKKeFdqTND8AcTWsWCEvPwBAVMsp6Pg+AMDBpG5jMD9AEFEvOEU4P0C/+8eTEkA/AMR9Fd9vMD8A3M9Mk1BMPwCF+tYbj0k/YF6t04CPMj8AEJzOkpY1PwAAAAAAAAAAABQeulcAPD8AfMEHcg82P8AZoKptKTE/AAAAAAAAAAAAYB7yccEFPwDgV5s10wU/AOwXGLbLLD8AAAAAAAAAAAD6aHNgS0M/AGAftibETD+g7ghQg4pGPwAAAAAAAAAAAHsqjUhZTz8AM+EItzNQPwDjnVZgF0c/ANy86750PT8AiG0b8NIgPwDSI1iJqRs/AJA3tTGaKj8ARD1qA+BDP1CUOrM0zTM/AMnoLOheMD8AoGmRj7gKPwBeUqmleTY/AHjXHr9mJD8AulEQNDs4PwBw/TmzEj0/AFJLtjL3Nz8AOj7WrEwzP8AZ3w9fmTY/AOCHUhdfAD8AoNCu/rkhPwBEgi1aiCI/AMCX4Fc+GT8AICmtQ9ALPwB+B4u6m0Y/AIaOQIy1NT+gTWMAYX4+PwD8xUgDwzc/AGjUaYZJQD8A1b3Jb7NEP2c8+Vb2Gz4/ADjrHD0DID8AxKPdZoc2PwAQ25xQ2Rs/gDDgy7R/Jj8AwLllLRArPwCQfeZN8Cs/ABBKMkplJz9AoRKiuYk9PwAwZlskgjE/ANxqF21qMz8AptfvjBE0PwCT4WD60iw/AHCwq/DnHT8AAAAAAAAAAABAkY8clgY/ACgVyJdKIj8AvJoqgSYOPwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcf/fYGCw/AIAJ46AFAz8AfYudyZYfPwBkgu5/MyE/AMArwLl2BT8A1jug6TFNPyAv0fhRH1E/AM3WLzeyTj8ALUbUgqZCPwBR+hBX4k0/AAUgSkdDRD8AI1MWGuFHPwD4M93Gkj4/AAB2bvtuAD8AAPVERConPwBQuW1UIxE/AJAkVeteHT8AJE+t1MBFPwAMHbkhRDc/AOjOtHepNj8A/EPAUu00PwA446fn9jY/AJSe9j5WJz8ATHeukcsFPwAgaaaNyBk/ACAJDiZ1Cz8AblLKNdcLPwCDImJyJCA/AGi6KaoJMT8AoDZb/isxPwBMA6WhBUE/MDIzUHePND8AnAqofy04PwAAAAAAAAAAACQbtVwwMj8AQNSQMmEGPwDgfn8vQdw+AAAAAAAAAAAA1FFEyDclPwCAunN3IwE/AGCwpepmCj8AAAAAAAAAAABCrkoM2UI/ACqChlc6Tz9Ysv6FkstRPwAAAAAAAAAAADL+kVXcXT+AfscMjFNUP4BnR1wuV1A/ADp1NltiUz8ARVWHWK1RPwA58VIaQUM/AMyMk+AbOz8A6mOR/jxYP8Npb1uB3lY/ABEOys2HUT8A2iox2c5OPwCA0b6yUPE+AHKbNLrYOD8AHFoosz1CPwBsMpXAMUQ/gOMqJWlRUD8AGe1jFO5MP+BQT3dcjEY/AFymqCtVMz8AIDxhq2EQPwBdHt2fIzE/AGCLq+DxGz8A4BgACYkxPwD04dW8bk8/APX5ldwHRj9QBOEVGc1LPwAwtXcI8UM/AFCEM4kfST8AcXuxAj1CP6x+kkkZ4To/AHDdENULMz8AGOwq/HkXPwB6wu/r7TU/wDaIJShsLj8AQL9SHmj1PgCA1YiMSOA+AKgdlaVZDT8AQDhhp3fwPgCAnXE9h/4+AJoGmnohOT8AYBTlMdE4PwCt+PosuCA/ACB+31ACDD8AAAAAAAAAAABQfLaGtCI/ADCq8dIzHT+AAnPgWDA1PwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABIvyIQMCU/AFR6qy1/MD9ghrlyaIgnPwCoX8ev4Ts/ALwLZ1jUOT8A+HMqMuFLPyAuZEzhDEc/AGZN0MysPD8AxAoqfUE/PwC62PqLbUk/AAi/DO1LRz+ABohSZaFGPwDQTEn4jTc/APCcEHGmKD8AaSKblDo0PwD4ma2yAfo+AKAwJUgPAT8AOI/Rd7YpPwDwfuZUNhs/ACCteFa0HD8AEP047WgaPwBgIcbQuAU/AOKQ/HN+Ez8AeBW+BrQCPwAQ+mRnUB8/ABiM44D+ND8Ai4A0YRghPwA4Q0dH5yU/AECAPsuNBT8AEiIykF46PwA837KeyDw/wIAtkJxLNz8AgJotQUHoPgAAAAAAAAAAALADIjghLD8A0EihY7UiPwCY+sjdEjI/AAAAAAAAAAAAsL9AfPcsPwBg7RwCngc/ALAq+TNxDj8AAAAAAAAAAABgRduVzQ8/ALgR+kMlJT8AUdhF7HgqPwAAAAAAAAAAAOSyTx/XMT8AhJfTb1kyPwAomz3k7xk/AOT5H8TUMj8AO575Yuw0PwDduwNFli0/ACDQ3+0lIT8A4NdnNukjPwCHcxCJOPE+AECTb5ExDD8AUBoFiK8aPwBOzzRqZEA/AIDCCwRrST8AmPRIqB5IPwCA2nvs7UA/gHWNJpobWD8Apl5NU5VTP4DIkRi/s0g/AHB5Ja/3Oz8AUPi17bcXPwC3+xJTSD0/ABay5HhuNT8AQLOjeDZCPwDWlIIRelA/AFYiW5DARD+kfMl0Gr1BPwAszi/8JT0/AFAfif/HQT8AmiefO2g9P8d0vu7k/0I/AGTLlsVMNz8ALmM7UZM/PwA4sB8qND4/QFS9xEpmQD8AiMZEFDI4PwAaJbGNoU8/AAPKIPQVQz8AVzW5CP08PwBQVMHVq0E/AKXkxJRaQz8Aax4NI79CPwBs2ws0FS4/ABhKQstDLT8AAAAAAAAAAACk2uPGpDQ/AMj4F/r2ID8AgB9zsHG5PgAAAAAAAAAAAIB75W8Y7D4AAI4Db3fDPgBYUZ7NPiQ/AJxpCt1pMT8Au59IiAsjPwDYoZpSux4/ADCZP7D5Bj8ASCzZIsFMP8B9gO6kX0w/AKbTlTovRj8AYkHFMaVJPwAWz6xmz1E/AIeqU7hRSz8Ak2YI8PY3PwBwVsIBxjc/ACBnSS9+Iz8AMJAL96kNP4CE2ottzDY/AHC9wTwJMD8ASLKTdl0cPwCwUE5NNyY/ANT4kG7JAj8AQLghOBAUPwC44bX3CzQ/gAs9tJjwQj8AGhxPbfc3PwAgSG+iCBI/AOC1bG8FEj8Aj1zGAs0lPwDsFItT6yQ/AKBJyl7GJT8AJETa6q0pPwDEeU9kGyM/QG7LKM/bMj8AwBfZyo4tPwAAAAAAAAAAADCbroRCGz8AgKLvYNzjPgDoDRppGSw/AAAAAAAAAAAAyi4cWV1APwDYSAR4STY/AJhznNqIHz8AAAAAAAAAAAA0rdoq6kQ/ALTi04WjVz9w11DCvoxhPwAAAAAAAAAAAH0ZaoXMUz8AHzV/R15JPwB/VHcZekc/ALgp5W55Sz8AvISe0F1JP8Cbx+2Mmjs/ALxEZakMPz8AOs0a0K5DP7hkdlnWPU4/ALZ2df+iPz8AbMx3IG07PwBkWH38wzc/ALowVGQrUD9AziMXRdFUPwCYJRZEwlE/ABQi8OQGUT8A0cvc4C9QP6CUY9q3akM/AFyiQkLUNz8AAKDlhF/tPgD4lOrrMBg/AEZXJJRQMD8ApHzZ9fgyPwBK7pO0CEM/ADZqvrFjNj+ABg1N7novPwBoZ0a9pT8/AArtPTPRSD8AVUt7zX41P2BgWt5ETi4/AECkBiqPED8ATuA5R3U3PwBWpwKG5Dc/cHoSepUKGz8AQAn2ut0XPwCQnaZfJEU/AOTyEe6WRT8gLofRXvpDPwAIKOiy6jw/ACzuBzyXIT8AoDPqZYb+PgBYA6uAZQk/AOguiPYyIT8AAAAAAAAAAACqOIEIX0g/ANL2FQPiQD8ACwPiEdc4PwAAAAAAAAAAANxUMeMMQj8A2GH6U7crPwAlbBFHDlA/AEbJeajWUD8AuNxci65EP4A90hrxF0A/AERSvgK0OT8AvNutqElCP+BxFIJrfzs/AALZVd1pMj8AEKjs4/oRPwDMz5kghT4/AKBN2cpRMz8ACDfRTmglPwA4GI8fRTk/AGjmITD/OT8ApjOQ2jNAP0ALdyxGdUA/ADwnMb+rOT8Aur8c3jxPPwDxXYe6Wko/gKZq8IhJND8A6ADpkhs4PwAY3gxb3jQ/AGOF0GrpPj8ApmU5WRc4PwACyWTscTk/ABRRd8L8OT8A7B6TQhwjPwBdMFjvQCA/AEAyR+K2MD8AKEielwNKP2BtCSySPEs/AH1uVtZFQz8A7IfeGm1APwCMivFp8jY/AOT+IkHdNT8AuvGPSGc7PwCAcvTInzo/AAAAAAAAAAAAlFWo0tE1PwAQ/klWwS4/QC8NdwcaOj8AAAAAAAAAAADY8X7VbDg/ACAFu2e0Ij8AgFnsnVQSPwAwCAZtfzc/AEI8EIIvPD8ABCtRUO8zPwDQAEcIrhY/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACQ2nmOWLj8AAAAAAAAAAAC8RNJfHzA/AEB7KmYnHj8AOuwc5R7bPgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACwQ8rjgzQ/AAAAAAAAAAAA3oNURe9APwB87BQih0A/","dtype":"float64","shape":[1530]}},"selected":{"id":"1058","type":"Selection"},"selection_policy":{"id":"1057","type":"UnionRenderers"}},"id":"1039","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1039","type":"ColumnDataSource"}},"id":"1043","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1041","type":"Line"},{"attributes":{"data_source":{"id":"1039","type":"ColumnDataSource"},"glyph":{"id":"1040","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1041","type":"Line"},"selection_glyph":null,"view":{"id":"1043","type":"CDSView"}},"id":"1042","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"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","dtype":"float64","shape":[1530]}},"selected":{"id":"1060","type":"Selection"},"selection_policy":{"id":"1059","type":"UnionRenderers"}},"id":"1044","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1044","type":"ColumnDataSource"}},"id":"1048","type":"CDSView"},{"attributes":{"data_source":{"id":"1044","type":"ColumnDataSource"},"glyph":{"id":"1045","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1046","type":"Line"},"selection_glyph":null,"view":{"id":"1048","type":"CDSView"}},"id":"1047","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1046","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"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","dtype":"float64","shape":[1530]}},"selected":{"id":"1056","type":"Selection"},"selection_policy":{"id":"1055","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"text":""},"id":"1049","type":"Title"},{"attributes":{},"id":"1052","type":"BasicTickFormatter"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1055","type":"UnionRenderers"},{"attributes":{},"id":"1054","type":"BasicTickFormatter"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1056","type":"Selection"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1040","type":"Line"},{"attributes":{"formatter":{"id":"1054","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1057","type":"UnionRenderers"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1058","type":"Selection"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1059","type":"UnionRenderers"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1060","type":"Selection"},{"attributes":{"formatter":{"id":"1052","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1061","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"overlay":{"id":"1061","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"line_color":"blue","x":{"field":"x"},"y":{"field":"y"}},"id":"1045","type":"Line"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"fbc68a27-a8c8-409f-9864-0f36a079e2bf","roots":{"1002":"7dc1dcc0-c485-4ac9-8393-223d5c46e45e"}}];
      root.Bokeh.embed.embed_items_notebook(docs_json, render_items);
    
      }
      if (root.Bokeh !== undefined) {
        embed_document(root);
      } else {
        var attempts = 0;
        var timer = setInterval(function(root) {
          if (root.Bokeh !== undefined) {
            embed_document(root);
            clearInterval(timer);
          }
          attempts++;
          if (attempts > 100) {
            console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
            clearInterval(timer);
          }
        }, 10, root)
      }
    })(window);
    </script>
    </div>

Conclusion
----------

Congratulations, you have (hopefully) broken AES using a DPA attack! As
you might have discovered during this tutorial, there can be quite a few
issues with the difference of means method for breaking AES keys:

-  It's quite susceptible to noise
-  The attack can easily pick up other parts of the AES operation
-  The attack typically requires a lot of traces. These software AES
   implementations are pretty weak against power analysis, but they
   still required thousands of traces to break
-  Some targets (such as the XMega) require fine tuning settings to make
   the attack work

Nevertheless, using a difference of means attack can still be very
useful. For example, a later tutorial, PA\_Multi\_1, uses a difference
of means attack similar in concept to this one to break the signature of
an AES256 bootloader.

Tests
-----


**In [11]:**

.. code:: ipython3

    assert (known_key == key_guess).all(), "Failed to break key.\nGot: {}\nExp: {}".format(key_guess, known_key)


**In [ ]:**

