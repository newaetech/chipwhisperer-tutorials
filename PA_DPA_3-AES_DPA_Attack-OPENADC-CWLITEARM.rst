
.. raw:: html

   <h1>

Table of Contents

.. raw:: html

   </h1>

.. raw:: html

   <div class="toc">

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

1  DPA Attack Theory

.. raw:: html

   </li>

.. raw:: html

   <li>

2  Capturing Power Traces

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

2.1  Setup

.. raw:: html

   </li>

.. raw:: html

   <li>

2.2  Capture

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

3  Analysis

.. raw:: html

   </li>

.. raw:: html

   <li>

4  Conclusion

.. raw:: html

   </li>

.. raw:: html

   <li>

5  Tests

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </div>

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
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'TINYAES128C'


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



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

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5879 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5879 bytes
    


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
    N = 2000  # Number of traces
    
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
        gain = 30
        db   = 24.8359375
    adc = 
        state      = False
        basic_mode = rising_edge
        timeout    = 2
        offset     = 0
        presamples = 0
        samples    = 4000
        decimate   = 1
        trig_count = 8604268
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

    Trace(wave=array([ 0.01660156, -0.203125  , -0.14257812, ...,  0.00390625,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'ed 75 22 f7 03 36 52 36 fe 37 e2 4a 80 1a 15 84'), textout=CWbytearray(b'45 4c d9 c5 06 4d ad 53 c7 3c 28 4b cb 90 ed 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.14160156, ...,  0.00585938,
            0.06445312,  0.06640625]), textin=CWbytearray(b'53 43 14 54 fa 53 b4 e8 d1 11 20 a0 a9 fa 5b a7'), textout=CWbytearray(b'f9 b5 58 79 4a 81 87 fd a3 6e 53 06 be 77 9c 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19921875, -0.140625  , ...,  0.01953125,
            0.06835938,  0.07128906]), textin=CWbytearray(b'fe 76 db 1c a3 31 a8 38 cc 91 78 7d 2c fb 9c be'), textout=CWbytearray(b'44 8a e2 63 ff 4b 4f a8 cf 34 ee 11 47 ec 93 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19726562, -0.140625  , ...,  0.01757812,
            0.07128906,  0.07324219]), textin=CWbytearray(b'a9 1c 4c 10 2b 79 9e f9 ac eb 7c c4 10 21 e4 a0'), textout=CWbytearray(b'4c 2a ab 93 8c 60 56 59 f5 ba ce 35 1a b9 0c a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20605469, -0.14746094, ...,  0.02148438,
            0.0703125 ,  0.07421875]), textin=CWbytearray(b'fa 23 2c f0 cb dd 32 63 bd 15 04 96 c2 16 85 52'), textout=CWbytearray(b'3a a4 0c 53 b0 8d 82 10 98 63 a8 fe 2b b4 5b b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19921875, -0.140625  , ...,  0.00585938,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'fb 73 97 50 10 02 8e ca 32 e5 93 20 c6 57 ee 17'), textout=CWbytearray(b'ec 22 ed d6 03 3b f9 09 8c 42 81 ae 08 e0 b2 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14648438, ...,  0.01660156,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'a9 d6 27 99 c3 a3 12 06 6a 47 30 f8 37 0a d6 8a'), textout=CWbytearray(b'b5 23 7a 8c d6 2e 8f 54 1e 1a 35 0a c2 49 6b f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.203125  , -0.14257812, ...,  0.01757812,
            0.07226562,  0.07421875]), textin=CWbytearray(b'3d 7b 29 a1 04 22 c4 13 23 57 60 4e d6 8b 77 74'), textout=CWbytearray(b'89 eb 67 39 6a f1 2c 4a df 02 16 a8 fa 20 de 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.140625  , ...,  0.00878906,
            0.06054688,  0.06542969]), textin=CWbytearray(b'70 72 01 bb de b6 6f 58 01 76 a8 7e f3 de 84 f6'), textout=CWbytearray(b'77 76 25 92 e5 69 bd 1c 13 c4 83 67 c9 ac a3 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19824219, -0.13769531, ...,  0.00878906,
            0.06445312,  0.06445312]), textin=CWbytearray(b'de cf 08 99 5f b2 17 a1 21 98 6e d3 df 83 4d f5'), textout=CWbytearray(b'6d bd 29 a5 96 8b 4f cb 31 c4 ee 2e 7c 9a e3 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.203125  , -0.14257812, ...,  0.00683594,
            0.06152344,  0.06542969]), textin=CWbytearray(b'c1 94 c7 02 c9 cd c9 43 f8 db f6 29 18 4b 71 98'), textout=CWbytearray(b'57 9e f7 c3 5e 04 dd 8f 31 d1 57 01 cc 71 88 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.14453125, ...,  0.02441406,
            0.07421875,  0.07714844]), textin=CWbytearray(b'0b 62 f4 fe ad a6 6d eb ff 60 18 8d 75 bb cf 4f'), textout=CWbytearray(b'af 22 26 36 bc 57 ae fe 23 bb 78 d8 ff e7 f1 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.20019531, -0.14160156, ...,  0.00585938,
            0.06054688,  0.06640625]), textin=CWbytearray(b'f8 eb a7 9e 26 2c 32 33 78 c4 ac b7 b8 1d 78 fb'), textout=CWbytearray(b'cb ce 1b a2 bb 4f 4d 7b a7 11 1d db 9e 67 af 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20703125, -0.14550781, ...,  0.01367188,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'b4 da ef 98 ad b5 51 5a 6f be 7b 75 3b 5c 5f 7c'), textout=CWbytearray(b'03 78 d8 17 33 21 3b ad e0 96 81 8c cf 94 37 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14648438, ...,  0.01171875,
            0.06445312,  0.06835938]), textin=CWbytearray(b'31 09 06 39 c8 90 15 d8 37 c2 b1 12 6d 5e 38 33'), textout=CWbytearray(b'e9 d4 a6 aa a2 b9 5f b6 c3 6d 77 06 d0 de f8 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19921875, -0.13769531, ...,  0.01660156,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'04 10 65 62 c1 30 8f e8 37 89 f7 5f ac 82 06 be'), textout=CWbytearray(b'ee 09 ef 86 dd ce 04 cd 65 95 da e7 e7 bf bb 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14550781, ...,  0.03808594,
            0.08300781,  0.08203125]), textin=CWbytearray(b'6f ae ba 1a 3f 0a 31 a7 6d c9 cf 06 53 08 1c ab'), textout=CWbytearray(b'6b f0 5c 9b 70 7a 1e eb 92 53 69 b0 db ab d8 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14257812, ...,  0.00976562,
            0.06445312,  0.06640625]), textin=CWbytearray(b'56 b9 fe 37 79 08 24 c1 d7 c8 10 b0 49 1b 23 4a'), textout=CWbytearray(b'e2 d0 c1 6c 84 e2 e1 01 bf 6f d4 51 69 1f 2b 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14550781, ...,  0.01757812,
            0.06933594,  0.07128906]), textin=CWbytearray(b'08 f9 63 e5 ca d1 ee 57 35 74 77 f1 ca 7d 95 95'), textout=CWbytearray(b'b5 f7 7c 00 cc 44 bd cd b4 b0 07 7a c7 85 4a 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14453125, ...,  0.01855469,
            0.0703125 ,  0.07421875]), textin=CWbytearray(b'7f 34 34 e2 b0 c2 be d2 84 03 19 ec f0 87 26 8f'), textout=CWbytearray(b'e2 4f 04 94 24 e4 27 14 4b 7d 52 fb dc d9 08 af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.02050781,
            0.07128906,  0.07421875]), textin=CWbytearray(b'8a 81 58 88 8c fa 56 59 84 25 74 56 a5 27 39 a0'), textout=CWbytearray(b'4b f7 69 e2 9c e1 4d 55 d8 0a ff 3d 4b 6b ce 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1953125 , -0.13769531, ...,  0.01171875,
            0.06738281,  0.06835938]), textin=CWbytearray(b'42 13 82 d4 9e 3b 4b 4e e5 e6 dc 9c 0a 24 a4 d6'), textout=CWbytearray(b'a8 78 ca 32 aa db 67 8f 48 61 80 98 5c 73 17 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.14257812, ...,  0.01269531,
            0.06445312,  0.06835938]), textin=CWbytearray(b'd3 07 45 0c 2c e9 a7 36 aa b2 6f 9a 9d 76 77 ed'), textout=CWbytearray(b'9f 6a 21 c5 b7 af 7b ed 23 e3 26 21 a5 3b 01 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13769531, ..., -0.00195312,
            0.05566406,  0.06152344]), textin=CWbytearray(b'29 9f 32 65 dd 2d b7 de da 46 64 1c 20 1a 21 a7'), textout=CWbytearray(b'ed 60 a5 4d 05 8c 24 ad e5 63 47 60 be 60 e4 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.20214844, -0.140625  , ...,  0.01367188,
            0.06738281,  0.06933594]), textin=CWbytearray(b'19 d4 20 37 62 ce ee fe 2f 42 31 d9 a3 b8 d6 38'), textout=CWbytearray(b'a3 f5 36 11 69 5e 00 62 12 05 19 fd a9 ae f4 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20507812, -0.14355469, ...,  0.02246094,
            0.07421875,  0.07617188]), textin=CWbytearray(b'75 db 12 1b e4 84 f7 ba a5 be 61 58 ee 12 7b 9b'), textout=CWbytearray(b'22 9e 18 9a 29 c4 ab 95 33 cd a8 60 ae 0a 86 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.140625  , ...,  0.00292969,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'75 75 42 d3 b2 30 9c 81 72 ab bc d2 49 c0 4c fc'), textout=CWbytearray(b'11 80 fc 4d fd a2 f0 b3 7f d0 56 a2 3e 49 6f a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.140625  , ...,  0.02148438,
            0.07519531,  0.07421875]), textin=CWbytearray(b'0c 07 7f 23 ee 01 b6 f8 a0 58 9d 30 42 27 75 f4'), textout=CWbytearray(b'ab f5 f1 0f 71 3f d5 0a d2 56 ed 85 3d a5 52 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14355469, ...,  0.02050781,
            0.0703125 ,  0.07421875]), textin=CWbytearray(b'1f d7 f7 47 dc 7b c9 6c 3e 80 be b4 8a 7b 74 72'), textout=CWbytearray(b'd5 87 34 5c ec 53 5f 1a f3 28 2b c4 5c 36 85 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13867188, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'53 c7 65 cd 4a 11 08 4f bd ae f6 ee a4 09 68 e5'), textout=CWbytearray(b'fd 16 fd 6b 2f e2 43 a2 3e df f8 71 e9 2c 18 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.20019531, -0.14160156, ...,  0.01074219,
            0.06445312,  0.06835938]), textin=CWbytearray(b'15 d0 7d 11 ac de e6 2e 05 9d f4 4c 3d 56 48 fb'), textout=CWbytearray(b'd5 16 7b 6e d9 af 59 d4 30 16 7c 8a 17 ba 4c 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.140625  , ...,  0.01367188,
            0.06542969,  0.06933594]), textin=CWbytearray(b'80 65 80 66 a0 02 78 20 51 47 7b dd 94 f0 5b af'), textout=CWbytearray(b'c3 cd c0 03 be 9b e7 62 38 53 f4 8a fa 88 58 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14746094, ...,  0.015625  ,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'3f 68 c9 1e 12 33 be d9 1c ce 65 9c 4b 76 06 85'), textout=CWbytearray(b'94 e8 6c ba 82 dc 4a 74 3f 0a db d8 8a 54 8e eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.14160156, ...,  0.01367188,
            0.06738281,  0.06933594]), textin=CWbytearray(b'6c 47 5b fc dc 07 72 4e 71 16 7e b6 3f a0 dd 65'), textout=CWbytearray(b'7c b0 7a 21 a5 c4 1f a7 00 91 49 4f 7f 1b 3e 1a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14550781, ...,  0.015625  ,
            0.06933594,  0.07128906]), textin=CWbytearray(b'a0 91 a0 7e 2d 58 9e 2d 7c 9d ff dd 59 fe 24 94'), textout=CWbytearray(b'2e 02 19 6b 0f bc 39 d8 33 2b e3 84 7e 62 97 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02246094, -0.19726562, -0.13769531, ...,  0.02832031,
            0.07714844,  0.07714844]), textin=CWbytearray(b'5c 85 0f 9b ec 08 e6 da 2c 7f 3d ba 63 9a f2 e7'), textout=CWbytearray(b'f7 a1 67 ae 9e 11 f6 47 83 32 6b a5 47 c0 fb fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.13964844, ...,  0.01757812,
            0.06933594,  0.07324219]), textin=CWbytearray(b'da 22 fa 13 d8 30 3e 7d 66 29 c1 8f 33 fa 88 bc'), textout=CWbytearray(b'75 b8 39 27 2d 30 02 5c cb 49 8c d3 f6 20 d6 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.14160156, ...,  0.00097656,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'73 c6 ab 02 18 ab 51 e2 68 e3 79 11 4a 94 73 e5'), textout=CWbytearray(b'4d 0c fb 49 2e 26 44 19 7c 2b 56 f9 90 81 22 f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19921875, -0.13964844, ...,  0.02050781,
            0.07128906,  0.07421875]), textin=CWbytearray(b'e7 b6 1b 36 06 74 6c d8 0d 0b 29 8b 5d b9 d4 21'), textout=CWbytearray(b'87 46 2b 56 69 5a bf d1 36 9d d1 1a 4a 24 b3 fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19726562, -0.13769531, ...,  0.02148438,
            0.07324219,  0.07617188]), textin=CWbytearray(b'a5 1a 09 e7 d1 f8 1e 60 d3 d9 ee ce 16 41 a3 aa'), textout=CWbytearray(b'bc ed 25 73 69 dc 5c cb 1e 4c aa 35 43 de 4b 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14355469, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'fc 46 46 0a 11 49 5e 59 0a fb 00 ab 65 10 fb 73'), textout=CWbytearray(b'bb ac 65 99 a0 13 97 25 98 31 dc 70 4c a7 2a 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14453125, ..., -0.00097656,
            0.05664062,  0.06152344]), textin=CWbytearray(b'af f2 60 c7 b6 81 03 ed 05 e8 e0 65 d9 7a 4d 92'), textout=CWbytearray(b'1d f6 b5 65 22 62 bb 27 fc 55 dd 4a ac bc 58 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19433594, -0.13671875, ...,  0.03320312,
            0.08007812,  0.08007812]), textin=CWbytearray(b'd2 c5 6d cf b0 99 a0 0c 92 ec f7 b9 3c e6 50 e5'), textout=CWbytearray(b'7b bb 83 ba 6c ca 58 4a ab cb 05 83 35 cf 18 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13867188, ...,  0.00292969,
            0.05957031,  0.06347656]), textin=CWbytearray(b'27 64 2b 52 68 48 a1 77 34 f3 5c 66 3e b0 53 c0'), textout=CWbytearray(b'92 7a 4a 93 a9 a8 81 5a 0c 49 20 89 88 fc b7 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14648438, ...,  0.01953125,
            0.07128906,  0.07519531]), textin=CWbytearray(b'6a e5 14 2c a5 55 6b f5 9c 82 9c 19 ce 7c 38 8d'), textout=CWbytearray(b'e1 d7 1c 1b 67 7c 97 2c 26 e4 35 4a 9c 22 53 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20214844, -0.14453125, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'14 60 ca c5 e1 57 6e a6 28 3d d7 25 43 fb 5c 0d'), textout=CWbytearray(b'85 70 ef de 54 31 f5 1d 3b 0d 77 3a 90 75 4b 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.14160156, ...,  0.02050781,
            0.07421875,  0.07617188]), textin=CWbytearray(b'25 90 9d 15 85 88 48 73 8c d0 cc 74 a1 5a a0 31'), textout=CWbytearray(b'f5 7f e8 ee a1 10 e3 e6 14 8d 6f c1 2b 72 e7 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14355469, ...,  0.00878906,
            0.06542969,  0.06738281]), textin=CWbytearray(b'28 29 f3 89 c8 0f 77 31 ae f7 3f 8c 28 c1 7b 68'), textout=CWbytearray(b'67 e8 e1 88 2e a7 6e 86 c1 e4 68 c1 53 7c 7b e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19824219, -0.13671875, ...,  0.01757812,
            0.07226562,  0.07421875]), textin=CWbytearray(b'c7 a2 c1 6f 12 e2 d2 0b bb 11 1e a4 41 b8 10 b3'), textout=CWbytearray(b'97 0a bb fb 47 f1 f6 81 50 37 84 3c 7b e9 25 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13964844, ...,  0.01171875,
            0.06542969,  0.06933594]), textin=CWbytearray(b'e6 e3 fa 5a b9 cb e1 9a 0d c4 93 49 8e 69 7a b1'), textout=CWbytearray(b'a2 dc 7a ba ff 01 c1 a2 9e d9 c8 44 04 db f1 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.14160156, ...,  0.00976562,
            0.06445312,  0.06835938]), textin=CWbytearray(b'54 4c 31 8c e9 b7 04 9c c7 41 7d 24 15 e6 87 39'), textout=CWbytearray(b'3b 69 8e b6 33 a4 e7 1b 5d 1e 7f 55 f3 32 74 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.13964844, ...,  0.0234375 ,
            0.07421875,  0.07519531]), textin=CWbytearray(b'62 bd 14 10 76 70 90 d6 72 1c 53 2c 5e d2 60 ca'), textout=CWbytearray(b'ab 64 67 c8 2a 68 9b 4a 78 4c 36 6e bf 06 60 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.14257812, ...,  0.01660156,
            0.06933594,  0.07226562]), textin=CWbytearray(b'5e 17 07 40 e0 5a 85 c0 08 f1 e2 3d 06 f3 70 29'), textout=CWbytearray(b'85 09 90 6c 14 bc e4 96 a0 4e 1a 42 3c 9c 17 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14160156, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'2a d8 4c 57 65 62 c4 b4 10 ef cc bc f0 e0 f7 1c'), textout=CWbytearray(b'98 99 30 4f f7 4b 05 6f c7 30 fc 25 60 26 fd 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19726562, -0.13867188, ...,  0.00585938,
            0.06347656,  0.06738281]), textin=CWbytearray(b'bc 9f 13 4d 36 b4 46 fc 00 61 87 93 d1 07 e0 f7'), textout=CWbytearray(b'98 ef e8 a0 c8 21 ee 2f c1 cc be 20 83 4c 37 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.13964844, ...,  0.00292969,
            0.06152344,  0.0625    ]), textin=CWbytearray(b'7e 22 76 62 b6 9e 87 0d ea 07 b4 8a 53 a0 10 e5'), textout=CWbytearray(b'75 71 6f 41 79 bf ef a0 8a aa bd 5b b3 33 7a d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.13867188, ...,  0.01953125,
            0.06835938,  0.07421875]), textin=CWbytearray(b'98 58 89 53 94 10 3e bf d5 66 7d 29 0f 9a 53 e6'), textout=CWbytearray(b'60 3e d0 46 d9 3d c6 04 91 a2 31 f5 82 b9 f7 d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.01367188,
            0.06835938,  0.06835938]), textin=CWbytearray(b'cc f2 45 24 ce 68 e2 ea b7 82 1a b5 d3 3b 87 c5'), textout=CWbytearray(b'bf c1 36 f1 65 71 ef d0 80 64 51 8b 77 99 4b 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19921875, -0.13964844, ...,  0.00292969,
            0.06152344,  0.06445312]), textin=CWbytearray(b'91 6c d5 5f ef 28 e7 28 65 22 5d 7a b1 ae bf 32'), textout=CWbytearray(b'48 a2 0c 85 83 81 df 55 ef 0d 26 30 b9 a3 7d 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19824219, -0.14648438, ...,  0.01855469,
            0.07128906,  0.07226562]), textin=CWbytearray(b'b4 df 7c e7 49 58 26 87 be b4 40 1a 00 b5 10 5a'), textout=CWbytearray(b'ab 2e bc 90 8d ca 85 a7 92 d0 77 46 e6 5d 26 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.140625  , ...,  0.01269531,
            0.06542969,  0.07128906]), textin=CWbytearray(b'24 48 ed 66 58 a9 63 b7 89 9a fb 54 e5 b0 e5 71'), textout=CWbytearray(b'c8 e5 96 d2 eb 9e 80 ff 58 9f a6 44 7e f9 cd cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14453125, ...,  0.02148438,
            0.06933594,  0.07226562]), textin=CWbytearray(b'ab 3e 06 be ad 68 d0 d7 3a bd 99 bb da 89 bb 60'), textout=CWbytearray(b'f0 3c f8 03 4e c2 06 8e 59 99 fe f2 f3 2f c7 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.14257812, ...,  0.0078125 ,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'84 19 12 39 b1 b7 e1 8b 5f 65 76 9b 5f f2 d6 90'), textout=CWbytearray(b'37 d3 b8 db aa 22 55 c1 25 39 0e 70 e0 32 c4 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14550781, ...,  0.00683594,
            0.06347656,  0.06542969]), textin=CWbytearray(b'66 82 9f 81 24 08 b8 58 c2 fb 46 2a ac d6 36 3f'), textout=CWbytearray(b'0a 34 22 d2 52 58 ec 40 ff f0 f2 9b 2a a9 12 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.14257812, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'be ec 21 29 0d b8 c8 2e 64 99 0f 2e 82 a4 55 53'), textout=CWbytearray(b'54 af 29 b4 6a 70 4a ec ec 83 5d a5 67 bd 22 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19628906, -0.13574219, ...,  0.0234375 ,
            0.07519531,  0.07519531]), textin=CWbytearray(b'44 92 cb 20 a1 b8 6b 91 83 7e 32 b1 b1 de 1d e8'), textout=CWbytearray(b'01 ea aa 34 5c f7 79 88 9c 4c b4 f9 e7 db 28 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.14160156, ...,  0.01953125,
            0.07128906,  0.07324219]), textin=CWbytearray(b'dd 1a 86 04 87 e6 11 6e 5e 8b 58 e7 72 d2 73 31'), textout=CWbytearray(b'6f 2f ba 35 95 e6 94 f0 19 ce d7 34 d3 7a 72 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.00292969,
            0.05957031,  0.06347656]), textin=CWbytearray(b'07 d2 dc cc 7c e9 44 21 cf b4 e1 97 52 a3 43 d5'), textout=CWbytearray(b'c3 f9 7c e1 7b 90 36 4d 4b aa aa 1b 54 c3 d1 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20019531, -0.14160156, ...,  0.01269531,
            0.06738281,  0.06933594]), textin=CWbytearray(b'01 f6 f7 6a 42 e2 7b 75 2f 64 75 76 c7 59 e9 5d'), textout=CWbytearray(b'0e dd 7c 3d ee 01 66 7f 24 b7 76 e3 7f 14 81 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14550781, ...,  0.00585938,
            0.06347656,  0.06640625]), textin=CWbytearray(b'0f 42 ef 35 86 55 e2 dd 02 16 60 94 48 a2 69 2c'), textout=CWbytearray(b'e8 fc 4c 0e da bc 7b e0 fd d3 41 5e 7f 9f c6 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13671875, ...,  0.01269531,
            0.06640625,  0.06835938]), textin=CWbytearray(b'f8 f7 e4 81 88 f9 52 c9 e1 82 4f f2 ac b8 f6 e7'), textout=CWbytearray(b'5d 61 a9 d8 d0 2d 68 6e cb 5b e8 60 a2 0c d8 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.14257812, ...,  0.01660156,
            0.06738281,  0.06933594]), textin=CWbytearray(b'45 7b 85 cb f2 12 db ab 99 da c6 af fe fd 9d 77'), textout=CWbytearray(b'24 31 cf 86 cb de e4 eb b6 7f 20 c9 d1 a1 91 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14355469, ...,  0.00878906,
            0.06152344,  0.06640625]), textin=CWbytearray(b'a1 14 2a 99 f7 07 68 64 5b 96 d9 3f 0e ec d7 07'), textout=CWbytearray(b'a7 ae 08 d9 1c 4a a9 b7 b1 84 91 3a 72 6e ef e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14257812, ...,  0.01269531,
            0.06640625,  0.06738281]), textin=CWbytearray(b'00 f2 d0 7a 89 97 78 81 79 9d 33 8f 71 04 cd 49'), textout=CWbytearray(b'9b 15 d7 6f ae 22 c6 18 3c 73 b7 f8 b8 71 b2 a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.20507812, -0.14550781, ...,  0.00878906,
            0.06640625,  0.06933594]), textin=CWbytearray(b'c2 09 13 59 c0 d1 d1 32 8b 26 7c ba 38 1b b2 2a'), textout=CWbytearray(b'fd 25 4f 06 5c 63 63 69 94 a8 99 ea 86 a2 90 e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14257812, ...,  0.00585938,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'6b a4 0d 13 96 85 de 85 be da 32 31 c1 73 17 05'), textout=CWbytearray(b'c8 2e ef be 3f bc a3 48 93 fc c2 70 d9 6c e8 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14355469, ...,  0.01074219,
            0.06445312,  0.06738281]), textin=CWbytearray(b'36 9b 41 00 21 e3 89 ec 10 2d 2d f0 98 0c e2 79'), textout=CWbytearray(b'1e ba 3f b5 94 3e 51 53 a1 78 3b 7b 30 35 74 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13769531, ..., -0.00097656,
            0.05761719,  0.06152344]), textin=CWbytearray(b'04 cd a8 c9 b8 e3 bf 28 04 5b 39 c2 f1 33 23 f1'), textout=CWbytearray(b'b8 40 75 06 ed bd 88 6e 3e ab 32 e5 77 50 c8 d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13867188, ...,  0.01855469,
            0.07128906,  0.07421875]), textin=CWbytearray(b'ee 2f ae 19 9f 35 aa 46 f8 00 36 ad 91 5a d4 f4'), textout=CWbytearray(b'85 a8 55 43 8d 1a 46 d6 dd 7e e9 83 f0 b9 29 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.03417969,
            0.08300781,  0.08300781]), textin=CWbytearray(b'18 9c d5 7d 5f 08 86 41 d4 99 f1 50 75 69 36 cd'), textout=CWbytearray(b'1a c4 31 3f 02 bc 57 05 a6 01 7a 58 64 c8 e5 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.14160156, ...,  0.01367188,
            0.06542969,  0.06933594]), textin=CWbytearray(b'b0 b6 59 dd 24 30 57 af dc bd 17 67 51 ba 71 cc'), textout=CWbytearray(b'3a e1 2b 30 ee 7f cf 37 42 5d 3d 8d fb d8 bf 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14550781, ...,  0.015625  ,
            0.06738281,  0.07128906]), textin=CWbytearray(b'8b 9b a8 b9 44 7e cb 72 56 19 41 c9 a2 b9 1f 31'), textout=CWbytearray(b'ce 96 15 0a 24 21 63 e7 9b c5 17 58 3d 23 5f 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14355469, ...,  0.01074219,
            0.06542969,  0.06738281]), textin=CWbytearray(b'50 5f 42 c2 2e de 39 59 1e 6c d8 88 a7 11 d1 3a'), textout=CWbytearray(b'92 1d 95 29 be 53 51 70 c2 c7 98 de 55 41 55 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.01464844,
            0.06738281,  0.06835938]), textin=CWbytearray(b'f6 5b ca c7 63 22 ee 2a 8d 21 ca ce 07 c0 c1 ab'), textout=CWbytearray(b'f6 55 58 90 f3 7f 85 61 ed 0c 82 29 56 14 c9 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14160156, ...,  0.01464844,
            0.06640625,  0.06933594]), textin=CWbytearray(b'1b d6 ac 15 7f a3 e8 a9 73 4d 94 c4 e9 c0 97 44'), textout=CWbytearray(b'9c 13 83 56 6e 6f 53 4a 53 85 0e e0 05 14 1e 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.20019531, -0.13867188, ...,  0.01855469,
            0.07226562,  0.07324219]), textin=CWbytearray(b'fe e3 bd ba 1f 6d ec 71 21 de a8 d4 bb d2 f9 20'), textout=CWbytearray(b'6c 88 97 9e 91 dc 0e b2 1d c0 02 05 97 4e 25 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14257812, ...,  0.00976562,
            0.06347656,  0.06835938]), textin=CWbytearray(b'4d 2b 97 1a 50 31 44 8e 56 67 89 c9 57 8e 45 aa'), textout=CWbytearray(b'60 d1 24 68 0d d6 be 2b f4 b9 68 c3 e0 34 72 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.140625  , ...,  0.00390625,
            0.06152344,  0.06445312]), textin=CWbytearray(b'5d 43 93 62 ab 5f 52 cf 3d 56 f0 f0 db e0 ba 1d'), textout=CWbytearray(b'34 25 49 48 d7 43 99 ad ff d1 13 b1 70 b5 11 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13964844, ...,  0.00878906,
            0.06152344,  0.06542969]), textin=CWbytearray(b'79 27 1a 24 2a 30 12 87 31 3e d7 85 a6 72 03 e6'), textout=CWbytearray(b'fc 6f 64 f7 92 9b 5e 13 94 35 23 86 48 b8 42 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.13964844, ...,  0.01367188,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'01 9a 7c 4b 8b f6 1d 62 02 6b ab 77 ff 35 bb 08'), textout=CWbytearray(b'4b f7 83 59 e1 f2 54 b8 30 0b f6 40 e4 50 0b 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.13964844, ...,  0.0078125 ,
            0.06152344,  0.06835938]), textin=CWbytearray(b'd0 9b a4 f4 06 64 b2 64 dd 59 76 2b 64 5e 03 df'), textout=CWbytearray(b'31 37 cb cc 3b 55 0e 5f 7b 7f d9 ad 9e 1b 4d 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19824219, -0.14648438, ...,  0.01269531,
            0.06738281,  0.06933594]), textin=CWbytearray(b'b1 4b aa 4d e3 c6 9d a0 dc 6d b3 0a 97 91 28 39'), textout=CWbytearray(b'b8 35 79 6e f1 1a 45 85 23 d6 89 6a 6d 8d d2 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.20019531, -0.14355469, ...,  0.01757812,
            0.06933594,  0.07128906]), textin=CWbytearray(b'2f 40 76 64 3e 55 e7 69 47 3e 03 14 c1 d1 95 7a'), textout=CWbytearray(b'a0 43 22 8d 4d d2 04 a4 66 77 c5 f7 cf 26 88 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14160156, ...,  0.00683594,
            0.06347656,  0.06738281]), textin=CWbytearray(b'75 05 0b c3 07 b0 ae ff 94 0e 12 8e 3b 13 b6 0f'), textout=CWbytearray(b'f0 61 5a 4d ad 39 fe db 02 32 5a a1 f0 bc 7a d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.14160156, ...,  0.02832031,
            0.08007812,  0.08007812]), textin=CWbytearray(b'3b 08 60 15 66 90 28 b8 ed d1 a3 10 dd c5 61 61'), textout=CWbytearray(b'36 10 99 15 dd 7e 6c 22 3f 55 da 7f 76 68 bd b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.13964844, ...,  0.00683594,
            0.06347656,  0.06640625]), textin=CWbytearray(b'11 73 d4 0b e1 a0 62 5e 17 bc 13 43 f2 01 f6 71'), textout=CWbytearray(b'50 0b 79 c1 f8 b9 cd 32 07 23 47 37 7c 14 c7 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20117188, -0.14355469, ...,  0.01269531,
            0.06347656,  0.06933594]), textin=CWbytearray(b'17 93 7a 48 d5 5a 4f 98 98 43 f5 8e 5b f8 1c 8f'), textout=CWbytearray(b'c7 86 5b 83 c1 0b e1 1a ea d6 77 f2 fa b0 cb b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.140625  , ...,  0.01464844,
            0.06835938,  0.07324219]), textin=CWbytearray(b'aa bf de f7 75 a5 a2 84 14 bd 75 a6 90 f9 06 06'), textout=CWbytearray(b'66 b4 ce 4b 9f fc 86 06 ef d8 38 21 e3 fa 1b 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14453125, ...,  0.00878906,
            0.06347656,  0.06542969]), textin=CWbytearray(b'6d 28 f3 41 0d c2 89 32 36 dd e4 b2 1f c2 90 00'), textout=CWbytearray(b'1c 9f 51 27 d3 ef 21 bb 15 81 5a b6 cf 36 55 fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14453125, ...,  0.01074219,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'f8 87 06 2a a6 ca 65 d4 35 cb 65 c2 5f 0e b6 9d'), textout=CWbytearray(b'86 6c 90 25 c8 9d c0 be cd 12 a0 b2 b0 e4 d4 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19726562, -0.13671875, ...,  0.01464844,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'f8 32 8e c4 49 be d2 ba cc 00 01 f3 97 18 6e b7'), textout=CWbytearray(b'96 3d 1b ef ae b6 40 b3 2f 8d 18 c7 ee a8 44 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.140625  , ...,  0.01074219,
            0.06542969,  0.06640625]), textin=CWbytearray(b'a5 98 47 28 0d 63 14 89 99 05 b8 79 60 a7 ae 36'), textout=CWbytearray(b'74 b1 6d c3 16 4e b6 1a 27 d8 88 19 07 66 01 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.13964844, ...,  0.        ,
            0.05664062,  0.06152344]), textin=CWbytearray(b'1b 17 cf f4 52 37 8f 55 aa 25 4f ab d0 f4 fb 70'), textout=CWbytearray(b'8d 44 74 07 6a 57 84 1c 92 14 fc 29 af 38 6b 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14257812, ...,  0.00292969,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'6a 35 8e de 60 bc bb f3 91 d9 77 69 5a 09 66 36'), textout=CWbytearray(b'e6 ad f7 23 f4 9c ca dc 96 07 78 26 31 93 eb 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13867188, ...,  0.00292969,
            0.06152344,  0.06445312]), textin=CWbytearray(b'c0 29 35 9f 68 bd 0c e4 62 dd 03 68 30 b9 e2 ba'), textout=CWbytearray(b'e3 5e a4 9f 69 ca 0a cd 16 2a b3 61 03 04 3b 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20214844, -0.14355469, ...,  0.01074219,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'51 fa 84 8a da 59 5f ab c9 d4 3c b1 5e 8c 93 24'), textout=CWbytearray(b'79 9b 4f 1e f8 81 33 95 2f 8c dd f1 9e 38 b3 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.13964844, ...,  0.02832031,
            0.07714844,  0.07714844]), textin=CWbytearray(b'69 19 98 cc 0b 56 8c ff 7e 7b 8a 2d 56 6a b2 f5'), textout=CWbytearray(b'87 30 26 83 5b 9b 2a 81 54 b2 ac 70 ac 37 04 db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.14160156, ...,  0.0234375 ,
            0.07519531,  0.07519531]), textin=CWbytearray(b'2b ce f5 c4 53 d4 e6 bd dc 8a a1 9a 89 52 78 c0'), textout=CWbytearray(b'b0 89 24 a0 21 21 e0 df 63 f6 9d 96 fc 9f 2f 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.140625  , ...,  0.0078125 ,
            0.06054688,  0.06640625]), textin=CWbytearray(b'9b c9 48 c4 cd 5c 00 ea 10 86 cd 84 6c 80 9e b5'), textout=CWbytearray(b'd0 22 95 df 39 06 b1 b5 aa 2b f3 10 8e 40 33 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13867188, ...,  0.00878906,
            0.06542969,  0.06640625]), textin=CWbytearray(b'04 8f 2f 27 a8 4e 80 ab d7 ad ac 92 c4 8e 3c eb'), textout=CWbytearray(b'04 2a 30 2f 8c e3 8a 56 11 d0 f3 3b 18 73 75 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.01269531,
            0.06738281,  0.06835938]), textin=CWbytearray(b'c1 af ad d6 05 a7 39 01 6e 88 67 de 59 ae 6f 75'), textout=CWbytearray(b'67 45 78 f3 d5 46 86 c0 71 b5 0c 15 bd dc f2 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14453125, ...,  0.03222656,
            0.07714844,  0.078125  ]), textin=CWbytearray(b'6b e1 85 b4 8c 7f 7f 07 f7 e8 f8 d4 91 e4 0b 92'), textout=CWbytearray(b'2c 50 f3 4c 15 f2 ad 5c 79 3c 6e 26 e9 10 68 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14355469, ...,  0.0078125 ,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'cf fe 28 0f 0d 8a 35 16 76 49 5d a2 ac e1 01 43'), textout=CWbytearray(b'a4 9c 67 2b 4a 1b 83 2b b5 86 5e ed 1a 47 10 e5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13867188, ...,  0.01269531,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'f3 77 2d 35 11 10 d5 48 d5 d1 a6 b8 45 4d dc c9'), textout=CWbytearray(b'bc 2b 83 2a f2 89 02 57 4c ee 58 e4 77 7c a2 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14648438, ...,  0.02148438,
            0.07226562,  0.07519531]), textin=CWbytearray(b'8b 61 58 73 87 ef 5d 0c 8e 7f c7 8c 9e fd 01 30'), textout=CWbytearray(b'4c 99 69 b3 b6 2f 16 be 4f 5d 45 81 d4 39 e3 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14550781, ...,  0.02246094,
            0.07226562,  0.07519531]), textin=CWbytearray(b'89 08 6a 6a 59 33 a8 5c eb 74 f1 83 c3 6d 26 89'), textout=CWbytearray(b'e3 41 f3 94 6c 4e 7d c6 37 a1 a8 4b ba f5 40 a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14550781, ...,  0.01367188,
            0.06445312,  0.06933594]), textin=CWbytearray(b'8f 66 57 07 48 1c ea fa 84 66 40 09 88 c4 82 3c'), textout=CWbytearray(b'9a 70 11 03 7a 45 44 af f3 dc e0 68 df 00 39 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14550781, ...,  0.0234375 ,
            0.07519531,  0.07617188]), textin=CWbytearray(b'6f ae dd 58 88 e8 36 d6 bf 95 74 aa 35 3a b5 1f'), textout=CWbytearray(b'bb 6f 87 17 8e 0e c6 0c 86 36 3a 1a 86 ea 7c 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19921875, -0.1484375 , ...,  0.01367188,
            0.06445312,  0.0703125 ]), textin=CWbytearray(b'a6 2b bc 3e a4 a1 64 94 2f 15 42 0e e4 f4 8f 9b'), textout=CWbytearray(b'e0 82 42 f2 01 95 51 89 fa 16 97 bd 18 25 28 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19628906, -0.13671875, ...,  0.01757812,
            0.06933594,  0.07128906]), textin=CWbytearray(b'a1 71 ff de ac 6e 96 54 a6 8f c5 ef a2 33 19 f7'), textout=CWbytearray(b'c6 63 16 84 d0 72 41 e0 9b a7 4e f4 04 da a0 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20507812, -0.14550781, ...,  0.00878906,
            0.06054688,  0.06542969]), textin=CWbytearray(b'd1 c3 c1 36 c1 72 61 f0 95 84 70 0c 8c 05 24 0f'), textout=CWbytearray(b'f6 87 ae 69 2b cb c3 a9 72 ad c2 9f b6 ab 4f 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.13964844, ...,  0.00878906,
            0.06445312,  0.06835938]), textin=CWbytearray(b'7c a8 d2 1a 23 0d 1e 64 a3 6c 27 ea bb 64 05 d1'), textout=CWbytearray(b'cf d8 9c 03 9b 7e 58 1e b6 1f c2 31 3e c0 32 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14355469, ...,  0.01953125,
            0.06738281,  0.07226562]), textin=CWbytearray(b'd9 37 72 4e cd ea 55 52 6a 66 cd 14 a5 41 26 85'), textout=CWbytearray(b'd4 b4 4b 34 e1 ff 63 07 89 5a d1 3c 2c 65 e9 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.140625  , ...,  0.01074219,
            0.06445312,  0.06933594]), textin=CWbytearray(b'48 2a 64 2e 2c e3 54 74 a9 18 da e9 c5 83 b0 97'), textout=CWbytearray(b'f0 50 32 25 fd a5 4a 8e 70 06 66 d9 fd 70 25 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14648438, ...,  0.00488281,
            0.05761719,  0.06445312]), textin=CWbytearray(b'23 b0 6f 09 d5 25 f2 ae 37 84 36 2d 99 94 2e 26'), textout=CWbytearray(b'27 7d bf 4e 9b 2e fe c4 06 f2 99 21 ff 67 8e 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19921875, -0.14160156, ...,  0.00390625,
            0.05859375,  0.06347656]), textin=CWbytearray(b'b5 81 fe a0 05 4f b5 71 cb 54 7c 29 3b 83 8f be'), textout=CWbytearray(b'04 e9 e9 71 34 80 f1 db 73 36 1e 38 e7 c0 c7 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19042969, -0.13769531, ...,  0.02246094,
            0.07324219,  0.07324219]), textin=CWbytearray(b'2d 9f de 1b f2 50 5b 18 15 69 97 da f8 e1 e4 e1'), textout=CWbytearray(b'5e a7 b9 ca 1f ab ee b5 2f 9e 18 a8 52 73 bd be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.13964844, ...,  0.01953125,
            0.07324219,  0.07421875]), textin=CWbytearray(b'29 ce bc 55 25 10 d4 6e eb 97 84 93 cc 06 1f a8'), textout=CWbytearray(b'8a 19 e8 bf 04 a4 8f 78 78 0c e6 72 cc d6 04 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14355469, ...,  0.01660156,
            0.06738281,  0.07128906]), textin=CWbytearray(b'b6 25 fb 7f ae 8b 3e 62 67 2a 5c c1 8c 0f af 01'), textout=CWbytearray(b'ff f5 73 0c 24 0e 51 34 7a 03 2e dd 97 fd 67 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.20117188, -0.14746094, ...,  0.00878906,
            0.06347656,  0.06640625]), textin=CWbytearray(b'fb 27 26 06 27 44 67 d8 77 03 d5 7a 42 0b 91 5c'), textout=CWbytearray(b'1d b4 46 27 0c de ef e1 79 6b 76 75 84 ff 1d d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14453125, ...,  0.01367188,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'a1 a9 05 4e c3 ac 2c 0a 32 ac e4 82 a3 eb db 5b'), textout=CWbytearray(b'ec e3 96 cf c8 1e 4a 09 46 8b 99 c0 cf 26 c0 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14355469, ...,  0.01269531,
            0.06738281,  0.06933594]), textin=CWbytearray(b'7e b8 54 95 0c e7 fa 67 53 63 02 f8 8c 82 a4 90'), textout=CWbytearray(b'ae 6c 28 0f 44 bb d0 65 45 7f da 3e ba 3e 3c a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14746094, ...,  0.00976562,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'8c 9c 8e 41 96 62 84 f4 3c 4f 45 1a 72 ef 49 6c'), textout=CWbytearray(b'e9 6c a5 47 6b 6b 35 d6 1c 32 ff 78 25 1c 43 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.14160156, ...,  0.00195312,
            0.05859375,  0.06347656]), textin=CWbytearray(b'd9 f1 94 06 7b 01 f9 be 4c b2 b2 c1 7d d6 f4 82'), textout=CWbytearray(b'79 16 e5 05 70 c0 47 e1 e5 14 19 bd 66 c1 ad 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.14257812, ...,  0.0078125 ,
            0.06445312,  0.06738281]), textin=CWbytearray(b'df 92 65 13 63 bc 6d 41 49 e7 01 70 8a 5d ce 51'), textout=CWbytearray(b'33 80 14 2b f4 69 d3 c0 f3 d5 1b a2 8f e6 f6 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13769531, ...,  0.00390625,
            0.06054688,  0.06640625]), textin=CWbytearray(b'96 25 af d1 28 18 6e 68 05 c8 49 bb bb c4 df df'), textout=CWbytearray(b'90 4f 93 54 7d 31 f5 64 37 46 99 df ca 7b 6d be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13769531, ...,  0.015625  ,
            0.06835938,  0.06933594]), textin=CWbytearray(b'dc 20 4c 3f 23 fe 22 5d 7b 91 ae 28 c3 65 57 eb'), textout=CWbytearray(b'68 f4 64 0e 27 ca 90 6a 54 2e 46 34 03 a9 ce af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19238281, -0.13671875, ...,  0.01757812,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'10 dc 6c 60 be 2e 84 e8 a1 c4 78 a2 dd e3 41 d0'), textout=CWbytearray(b'73 7b 5e cb bf 13 de 8c 16 9d 9c c6 6e 9f ec 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19726562, -0.13964844, ..., -0.00097656,
            0.05371094,  0.06152344]), textin=CWbytearray(b'cb 97 e7 dc ae 5e 64 f1 39 2a d7 66 63 f9 82 e1'), textout=CWbytearray(b'3a 00 a5 e2 f7 2b ec 36 4b 68 b2 be f9 85 76 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14648438, ...,  0.02441406,
            0.07421875,  0.07519531]), textin=CWbytearray(b'70 6a b2 01 69 3a 43 0d 45 98 6c c1 8f 02 92 94'), textout=CWbytearray(b'e6 9f d6 40 9b d9 c9 ff 5e 7d 41 c6 aa 8e ee 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.14160156, ...,  0.01367188,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'36 11 42 04 57 8f 2c 5a 7c 33 17 e1 77 77 6d 9d'), textout=CWbytearray(b'04 c9 4b a6 db 6f 78 1e fe c9 d5 ef 98 e3 2c d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14257812, ...,  0.00488281,
            0.06054688,  0.06542969]), textin=CWbytearray(b'5f 0c 1d 34 d4 29 41 6c 24 dc 8b ef 59 62 24 82'), textout=CWbytearray(b'81 5d 10 6d 6b 09 1c d5 90 7e 39 66 68 ae e0 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.140625  , ...,  0.00878906,
            0.06347656,  0.06835938]), textin=CWbytearray(b'4e 4f 1c fb a0 c8 48 38 40 5a 89 c0 23 40 97 fb'), textout=CWbytearray(b'9e 3e 72 52 ba 38 09 d1 f9 74 ce 49 b1 a3 e2 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14648438, ...,  0.02539062,
            0.07324219,  0.07714844]), textin=CWbytearray(b'c2 bd 00 f8 bf e3 75 c7 f3 36 ab a0 9e da 28 19'), textout=CWbytearray(b'28 cb e6 b7 55 81 c9 cf 91 1e 3b ba 53 65 6c 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13769531, ...,  0.00976562,
            0.06738281,  0.06835938]), textin=CWbytearray(b'33 6c ea 80 69 d6 ae a3 53 dc fc d2 77 58 50 df'), textout=CWbytearray(b'65 62 5b 6c 3a e2 0f 8a 6b c8 23 4c 8f 97 13 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14355469, ...,  0.01757812,
            0.06835938,  0.07324219]), textin=CWbytearray(b'b6 57 2d d8 2b e1 f3 80 81 a0 d7 ad 40 29 47 47'), textout=CWbytearray(b'b7 78 43 70 59 70 17 9b 9b 89 d4 9f 53 12 98 db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13867188, ...,  0.00097656,
            0.05859375,  0.06445312]), textin=CWbytearray(b'fb 7f a7 3c 07 af 6b 7c 2f 98 aa eb 81 49 1d f1'), textout=CWbytearray(b'17 b1 48 a0 d2 c2 08 89 11 03 5f 5e 8e f7 0b d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19921875, -0.14746094, ...,  0.0078125 ,
            0.06445312,  0.06542969]), textin=CWbytearray(b'f1 88 b2 17 d9 f2 4a 93 81 4b 13 3b a2 89 65 1f'), textout=CWbytearray(b'6a 9c 8e 1c b1 c8 57 ef 4d 32 76 5e 52 14 57 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.20019531, -0.140625  , ...,  0.01269531,
            0.06738281,  0.06933594]), textin=CWbytearray(b'd1 96 e0 35 10 a8 0a ac d5 d6 e3 98 6c c9 0b ed'), textout=CWbytearray(b'ce a5 60 ad cf 8e 13 d5 9c 42 06 25 ae 54 42 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.14160156, ...,  0.01757812,
            0.06835938,  0.07324219]), textin=CWbytearray(b'fd 70 d7 0b 0c 1d d9 a4 b0 e0 3d 0a 24 46 30 fa'), textout=CWbytearray(b'ca fa a1 59 61 c2 59 c8 a5 8a cb d4 d1 41 b2 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.20117188, -0.13964844, ...,  0.01269531,
            0.06640625,  0.06933594]), textin=CWbytearray(b'37 5e a9 72 98 de 12 9c 72 dd 38 59 61 4a c1 97'), textout=CWbytearray(b'a1 76 6a c7 49 d4 9e ae c0 38 df 78 8b 56 5b 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.01757812,
            0.06738281,  0.07226562]), textin=CWbytearray(b'8f 78 0e 49 59 72 44 5c de b0 d5 32 3a c1 44 59'), textout=CWbytearray(b'a6 cc 8d 07 25 6c c7 a3 fb be 5e 66 3c d3 0e 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13867188, ...,  0.02050781,
            0.07226562,  0.07617188]), textin=CWbytearray(b'64 f7 1c 1f 83 00 15 d8 e5 46 cd ea b6 09 7a a8'), textout=CWbytearray(b'be a0 3c 3c 14 c4 8c 06 6d 41 77 b8 7e 49 80 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.140625  , ...,  0.01660156,
            0.06933594,  0.07324219]), textin=CWbytearray(b'e9 84 4b e0 06 3f fc 5c 76 61 85 97 70 1a cc d0'), textout=CWbytearray(b'a1 a7 62 e6 2d 26 9f ce 57 6d aa a2 d2 ce c7 ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14160156, ...,  0.00683594,
            0.06054688,  0.06738281]), textin=CWbytearray(b'30 e8 5f b9 41 4e 5a 26 ea e8 3a bd ff 63 c7 7b'), textout=CWbytearray(b'd9 b4 1e 2e 53 ac 76 ac ca 31 fe 6e 28 06 93 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19433594, -0.140625  , ...,  0.00683594,
            0.06445312,  0.06835938]), textin=CWbytearray(b'05 ec e2 f3 13 44 dd af 7d b3 77 c4 b2 46 d2 d3'), textout=CWbytearray(b'1f 43 37 a0 64 ac 17 01 fe 74 0f 00 a1 80 7a 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14355469, ...,  0.00878906,
            0.06445312,  0.06738281]), textin=CWbytearray(b'84 7e 6d 5e 09 24 5d 9a 00 79 ad 8f 04 69 52 68'), textout=CWbytearray(b'56 5b d9 fd 05 3f b8 16 11 2c cd 70 ab 93 32 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14257812, ...,  0.01953125,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'c3 dd db d1 67 97 3c 9d 49 79 30 67 d5 85 4e 59'), textout=CWbytearray(b'94 71 9a 4e f9 cf c9 63 ab 90 aa 64 2d 66 15 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19433594, -0.13769531, ...,  0.02832031,
            0.07714844,  0.07714844]), textin=CWbytearray(b'07 fd 47 82 1c ea d0 90 32 1d ac 6b a8 0b ef d2'), textout=CWbytearray(b'ea b6 81 54 3e 85 a6 96 cb f8 9e 68 3e cb b5 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14453125, ...,  0.02441406,
            0.07519531,  0.078125  ]), textin=CWbytearray(b'28 8c 45 1c 41 d2 94 d4 37 ea 2f bb 4e cc 1e 3c'), textout=CWbytearray(b'a6 a4 24 25 07 25 5e d9 c9 5d e1 c7 25 d6 63 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20117188, -0.14453125, ...,  0.01757812,
            0.07226562,  0.0703125 ]), textin=CWbytearray(b'18 55 17 c7 62 8b eb b0 49 d4 dc dd 02 99 2f 9f'), textout=CWbytearray(b'64 17 14 00 57 a4 12 ae b8 7d 8e e5 05 54 44 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14355469, ...,  0.02929688,
            0.07617188,  0.078125  ]), textin=CWbytearray(b'e8 27 31 ea a3 b6 07 0d ee 43 40 22 e8 69 fd 7c'), textout=CWbytearray(b'5c 7d 3f 7c ea e0 d5 94 9c de 1e d0 ac 39 6d 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14453125, ...,  0.00878906,
            0.06347656,  0.06542969]), textin=CWbytearray(b'e3 53 71 e0 54 0e 23 50 01 25 99 39 10 1f 3b 48'), textout=CWbytearray(b'8c ce 44 11 27 bb 63 29 5b bd 73 04 2f 36 b0 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.00976562,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'84 77 bb 56 a4 3b cb 5c d0 ed e7 6d 78 e6 b2 3b'), textout=CWbytearray(b'ba ef fd 79 8e 9c 55 02 60 35 2f 9e d3 42 00 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.140625  , ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'd9 ff 2d 4e c0 49 d7 7a c2 93 36 72 e3 37 b7 fa'), textout=CWbytearray(b'f0 6c 7d 09 e6 4e 02 62 ca 86 40 be ca dc 49 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.140625  , ...,  0.0078125 ,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'da f2 87 bf 80 6b 85 d5 c8 35 ea e2 5c 69 94 a7'), textout=CWbytearray(b'5b 1a c3 c9 9c c4 81 5a 91 91 60 bc a6 f9 eb 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19238281, -0.13476562, ...,  0.00976562,
            0.06152344,  0.06640625]), textin=CWbytearray(b'76 6e e2 7b 86 2d 1f d8 c6 82 7c b6 15 bc bf e7'), textout=CWbytearray(b'6c c2 3d 6f 8a 5f d9 c6 ee 03 58 19 92 91 c8 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14648438, ...,  0.00878906,
            0.06152344,  0.06542969]), textin=CWbytearray(b'8b 92 e7 46 09 c3 95 aa ce d0 a1 13 43 c5 26 3a'), textout=CWbytearray(b'79 31 07 78 2c 57 09 66 8a ae dc 0e 5c a6 d9 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.14160156, ...,  0.01269531,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'da 43 42 55 06 0d 3f 34 5a c2 e4 b5 ca 77 9a e3'), textout=CWbytearray(b'3c 55 8d ba af a1 ee 02 7d 8e 49 d7 59 3b 41 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13867188, ...,  0.01757812,
            0.07128906,  0.07324219]), textin=CWbytearray(b'90 d2 91 09 46 7a 30 b9 1c 80 f1 88 d7 d2 05 e6'), textout=CWbytearray(b'f1 b3 03 12 00 96 2d 23 c7 63 be 6b fc bf 71 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.14160156, ...,  0.01757812,
            0.07128906,  0.07421875]), textin=CWbytearray(b'30 61 69 63 1c b5 9c b9 4b 79 c2 ba 31 25 b4 83'), textout=CWbytearray(b'5b 92 e5 0f b5 00 50 01 2d 82 e9 6e 40 bd a1 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.13964844, ...,  0.01269531,
            0.06542969,  0.06933594]), textin=CWbytearray(b'12 b2 4f f7 30 6e f4 d4 58 81 b6 1f 80 17 53 ea'), textout=CWbytearray(b'59 fd fb c3 99 87 71 44 8b 96 d9 ea 4b 07 d3 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.20117188, -0.14160156, ...,  0.01660156,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'96 9e 7f 57 5d 8a 00 a2 75 10 e5 d2 b3 38 ab 23'), textout=CWbytearray(b'22 94 15 73 e0 45 cb bc c9 d8 79 2a 3b 8e 0b 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14648438, ...,  0.01171875,
            0.06347656,  0.06738281]), textin=CWbytearray(b'cc db 5e d4 63 12 b4 c8 15 17 50 c3 ec 01 1e 4c'), textout=CWbytearray(b'56 75 b0 56 7a 8a 64 b2 ad 37 8e 0c 59 6a ce c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19921875, -0.13964844, ...,  0.01855469,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'8e 76 5a b7 93 94 d6 24 c8 ac 40 53 b6 4d 28 ea'), textout=CWbytearray(b'65 28 cc 7a de 5c af ad df 8d fc 02 6b b7 7b 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18847656, -0.13769531, ...,  0.02246094,
            0.07324219,  0.07519531]), textin=CWbytearray(b'50 2f 4b dc 92 a4 4a 99 05 06 79 45 82 cb fd c7'), textout=CWbytearray(b'b0 2f 99 d3 9a 48 42 37 00 9a 54 d3 c4 8a e7 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.14160156, ...,  0.01660156,
            0.06933594,  0.07226562]), textin=CWbytearray(b'20 a6 e7 ae 34 e9 39 f6 d9 cd 13 42 61 b1 db 39'), textout=CWbytearray(b'b5 bf e4 47 b9 9c b6 c4 b0 29 51 bb 67 d8 c5 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14160156, ...,  0.02246094,
            0.07421875,  0.07421875]), textin=CWbytearray(b'02 71 5a ae 88 37 6d d2 86 3c d9 66 20 f2 95 41'), textout=CWbytearray(b'df c2 ad b4 d9 3b 78 e0 fc 11 3d 67 75 02 cd 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14257812, ...,  0.00683594,
            0.06054688,  0.06738281]), textin=CWbytearray(b'76 6d 9b ad 22 a5 4e e3 0c 27 ce 30 d5 e0 10 5c'), textout=CWbytearray(b'd5 06 a8 60 bc a7 ab 36 26 fa a7 ef bb 1c fb 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19824219, -0.13769531, ...,  0.01074219,
            0.06445312,  0.06738281]), textin=CWbytearray(b'd3 b9 66 a5 f1 85 25 0b 96 e6 7a 3c 63 ec e4 bd'), textout=CWbytearray(b'92 94 29 78 6b 76 eb dc 06 ae 6f 19 d9 8d 71 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.1953125 , -0.13867188, ...,  0.01171875,
            0.06738281,  0.06933594]), textin=CWbytearray(b'78 20 09 bf 94 46 d3 0f 14 6e 1b 03 f6 61 c3 b8'), textout=CWbytearray(b'55 1c a2 96 04 f7 cc 79 8b 92 e1 b5 1c b4 46 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.140625  , ...,  0.01757812,
            0.07128906,  0.07226562]), textin=CWbytearray(b'54 05 d3 c8 b1 9f 08 f1 f2 97 a9 b8 d2 62 13 ec'), textout=CWbytearray(b'9d ed 53 14 26 0f 8c fe 1f 47 0f ab 66 3d cb 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13769531, ...,  0.01660156,
            0.07128906,  0.07324219]), textin=CWbytearray(b'a2 25 44 be f6 12 83 cb a3 24 23 1b 75 83 97 ea'), textout=CWbytearray(b'26 7d 2c 62 97 76 14 7f 83 a6 86 17 e9 3f 4c 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13964844, ...,  0.015625  ,
            0.06933594,  0.06933594]), textin=CWbytearray(b'c6 7b a5 78 bf 4e 6e 63 0b 85 76 e5 27 7b a4 dd'), textout=CWbytearray(b'd9 f9 6c 1b 93 77 f3 c0 f6 4d c6 8b f5 7c db 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19726562, -0.13769531, ...,  0.03417969,
            0.07910156,  0.08007812]), textin=CWbytearray(b'9e 27 92 25 61 19 22 36 75 32 31 04 5d ce 54 cc'), textout=CWbytearray(b'ce 1a 88 69 20 58 52 91 7e f2 29 63 b4 ef a4 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.20117188, -0.14355469, ...,  0.00878906,
            0.06347656,  0.06835938]), textin=CWbytearray(b'c3 b6 79 8e 5c cf eb 5f 38 b1 85 5f f8 b4 54 05'), textout=CWbytearray(b'35 25 a2 21 7c ee 33 3a 75 db a5 13 6e 2f 33 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14257812, ...,  0.02050781,
            0.07128906,  0.07324219]), textin=CWbytearray(b'0d 68 70 18 86 33 b6 bb 31 f2 02 1f 7d 05 72 7e'), textout=CWbytearray(b'c9 39 82 4b 9c d5 c7 c7 78 5e 25 93 ef 04 c3 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.13964844, ...,  0.0078125 ,
            0.06347656,  0.06640625]), textin=CWbytearray(b'49 02 b3 79 61 03 8c 8f 57 77 9d 08 85 a2 37 04'), textout=CWbytearray(b'4b a3 21 f3 b7 11 e0 c1 fe ba 9d 95 79 e0 71 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13867188, ...,  0.0234375 ,
            0.07421875,  0.07519531]), textin=CWbytearray(b'47 7f 05 21 b9 5d a2 95 37 4e 73 78 cf 0c c4 c6'), textout=CWbytearray(b'1a fa 3d e3 39 57 9f 3d f7 d6 fc 82 24 9e 77 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.14160156, ...,  0.01074219,
            0.06542969,  0.06738281]), textin=CWbytearray(b'61 65 22 96 00 16 7e 29 92 cc 79 c2 6b 06 cf 29'), textout=CWbytearray(b'b8 06 b4 9c 6f 33 4c 67 01 75 2c ff 00 5a 3d ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14648438, ...,  0.015625  ,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'4f 99 92 ac fe 54 ef b3 b8 1c 59 cc 60 05 8d 18'), textout=CWbytearray(b'10 55 96 5c 74 3a 60 38 7d db 08 76 6e 5e 8b 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13964844, ...,  0.01660156,
            0.06933594,  0.07226562]), textin=CWbytearray(b'21 9a 2a 61 03 5d 5c a8 a4 12 58 62 09 b1 c9 92'), textout=CWbytearray(b'e4 a7 39 ed 07 39 c8 b6 9e 62 ac b4 0c d8 d7 e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20605469, -0.14453125, ...,  0.02148438,
            0.07226562,  0.07421875]), textin=CWbytearray(b'f5 9b 08 87 c4 fd 32 21 3b 64 d6 8f 79 2b 50 84'), textout=CWbytearray(b'44 aa 4f 73 16 f8 df b5 20 12 10 d7 ec f6 79 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14453125, ...,  0.00683594,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'8e 04 6b 6c 16 4f fd 6a 3b 65 d9 28 b8 a9 5c 5e'), textout=CWbytearray(b'65 7d 3a 9d ed f0 13 81 3f 2d 5a 6e 15 36 e8 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14355469, ...,  0.01074219,
            0.06542969,  0.06933594]), textin=CWbytearray(b'41 75 25 55 96 43 d4 55 48 1d 26 f5 46 38 74 46'), textout=CWbytearray(b'b8 6b 3c 3a 85 ea 16 63 11 b1 26 9a 55 7d c3 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.140625  , ...,  0.02148438,
            0.07226562,  0.07421875]), textin=CWbytearray(b'09 f8 c2 c4 8c 29 4d a1 28 79 57 d0 5f 40 ef ef'), textout=CWbytearray(b'01 ca 87 ff c6 08 fa 83 39 c2 6b 80 b3 e4 82 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14160156, ...,  0.01855469,
            0.07128906,  0.07421875]), textin=CWbytearray(b'dc 7f 8e 5b e4 b0 28 5b c1 2f 91 61 f4 4f c9 5b'), textout=CWbytearray(b'5d dd 57 62 26 5a d0 c4 e5 94 a0 95 41 30 bb 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14257812, ...,  0.00390625,
            0.06152344,  0.06542969]), textin=CWbytearray(b'85 7e 79 24 f4 8e 01 60 a8 3b 45 f6 d5 77 8f 33'), textout=CWbytearray(b'e8 39 f0 cd ac 26 3a 49 4a b7 5f 6b 43 e5 70 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14648438, ...,  0.00976562,
            0.06347656,  0.06933594]), textin=CWbytearray(b'd6 04 42 43 f6 a8 ef 88 91 c9 a0 ea 1a fc 38 7a'), textout=CWbytearray(b'b2 75 c4 89 a6 6a e9 c4 23 1e 69 bf 18 64 cd ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19726562, -0.13769531, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'a5 7f e1 d9 30 51 d3 66 13 5c f2 80 0b a4 3e d0'), textout=CWbytearray(b'e2 a0 3b 87 2c 14 36 7c 19 37 da e6 93 74 ab fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19726562, -0.13964844, ...,  0.01269531,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'c0 88 5f 3b c7 fa 00 c1 86 3b f1 90 bc 7b 66 ab'), textout=CWbytearray(b'b1 7a 3e 72 75 17 72 41 7a 65 65 69 9d 20 12 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.1953125 , -0.13964844, ...,  0.01074219,
            0.06542969,  0.06640625]), textin=CWbytearray(b'd1 90 60 55 a9 8b 02 be fb 96 81 1c f4 99 05 fa'), textout=CWbytearray(b'f5 0e 2a 70 1a dc 10 4e 0b 2b bf e0 26 de 64 e5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.13867188, ...,  0.01855469,
            0.07226562,  0.07226562]), textin=CWbytearray(b'5b 5c 52 40 5a 92 bc 6c b3 cb 49 e6 b7 33 f7 86'), textout=CWbytearray(b'39 51 c9 1f 99 47 83 7c 22 be 8f 64 5e 04 7b 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.00195312,
            0.05664062,  0.06347656]), textin=CWbytearray(b'79 0d 33 72 00 0c 85 86 1a 21 4f 4e 93 33 f9 59'), textout=CWbytearray(b'75 79 ee 45 35 ed 04 88 7a 82 16 7c 54 bb 28 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14648438, ...,  0.0234375 ,
            0.07226562,  0.07617188]), textin=CWbytearray(b'35 75 53 76 a7 71 38 2f 56 87 c2 bc d2 07 ba 0f'), textout=CWbytearray(b'7d 56 6c 21 2b 0f 80 43 20 bc 08 58 aa b2 35 d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02246094, -0.1953125 , -0.13574219, ...,  0.01269531,
            0.06445312,  0.0703125 ]), textin=CWbytearray(b'3f bb 15 ba 48 73 3f 0b ca 1e a6 92 3e 21 fa c3'), textout=CWbytearray(b'9f a5 14 8d 93 0b 58 c3 8d cb 68 12 2e fd c1 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13671875, ...,  0.00292969,
            0.06152344,  0.06347656]), textin=CWbytearray(b'cb b5 7f 62 90 0f 1c 3f a9 38 85 03 ef 53 62 e0'), textout=CWbytearray(b'24 8a 4b 3c 15 40 18 89 7f 4a 88 15 5e 7f f1 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19726562, -0.14746094, ...,  0.00976562,
            0.06152344,  0.06835938]), textin=CWbytearray(b'39 13 14 b3 d2 e6 df d0 9f 3e ca 72 3e 76 29 7d'), textout=CWbytearray(b'52 a7 6c 3d 31 9f 18 b9 55 ed e6 c7 b4 2e 72 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14648438, ...,  0.0234375 ,
            0.07324219,  0.07714844]), textin=CWbytearray(b'3c bc 86 8a a7 34 de 03 b0 1d bd 12 35 ea 32 3e'), textout=CWbytearray(b'52 93 d2 f0 51 3a 70 46 81 56 3f 8c f8 79 f8 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'5b 0b 83 d7 24 37 d1 90 4e 9d ba 29 6c c7 56 fa'), textout=CWbytearray(b'89 4c f7 ce 51 fa c1 74 84 3e a1 19 8d 28 cf 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13769531, ...,  0.00878906,
            0.06445312,  0.06738281]), textin=CWbytearray(b'54 82 2d 17 0e 49 3c 60 1b d0 8a b4 75 48 22 c5'), textout=CWbytearray(b'c3 12 df e9 fa c0 2f 2e d6 ce a3 60 e6 28 e3 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ..., -0.00195312,
            0.05371094,  0.06152344]), textin=CWbytearray(b'f6 9a 40 4b 05 2a 3f f7 07 f1 74 3d ed 21 34 e0'), textout=CWbytearray(b'68 35 d0 2f 09 c2 49 c8 9e 71 2c 0d 49 22 81 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.1953125 , -0.13867188, ...,  0.00585938,
            0.06152344,  0.06347656]), textin=CWbytearray(b'a6 f0 eb bc 3f 1d 0f 8a ba ee 58 44 4a 60 57 e9'), textout=CWbytearray(b'f5 aa cb 0a b1 93 a3 e6 7d fe 04 3b 1f 70 77 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.1953125 , -0.13867188, ...,  0.01464844,
            0.06738281,  0.07128906]), textin=CWbytearray(b'23 0e 8a db f9 37 06 54 57 e4 0f 21 af 26 72 fe'), textout=CWbytearray(b'45 74 27 18 7f 51 6f d1 a3 64 c7 72 b7 5c 3a 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19335938, -0.13769531, ...,  0.00292969,
            0.05957031,  0.06640625]), textin=CWbytearray(b'd9 28 29 94 33 a3 76 b7 ce 06 29 8c 7e da 37 e8'), textout=CWbytearray(b'20 97 a9 9a 54 29 c0 c1 97 c7 a4 0e 06 62 d1 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.14160156, ...,  0.01660156,
            0.07128906,  0.07128906]), textin=CWbytearray(b'ea 9a 01 dd c3 0c 33 3c 78 f8 97 f2 5e ae 00 ea'), textout=CWbytearray(b'4a 67 a1 7d c8 a5 38 86 d1 17 d2 7b 68 db 89 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19042969, -0.13867188, ...,  0.00683594,
            0.06054688,  0.06738281]), textin=CWbytearray(b'f0 31 0e e6 62 04 39 0a e6 b7 c7 eb ef cf f2 41'), textout=CWbytearray(b'13 05 2b ae e2 cb 53 ef f2 8e e3 d0 15 2e ee d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13867188, ...,  0.01660156,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'e2 b9 c1 6d cb fc 8e 03 cc 81 c9 0b 51 fd d4 86'), textout=CWbytearray(b'99 ac 56 57 76 67 7d 19 6a f4 fd f6 68 15 1e e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19238281, -0.13769531, ...,  0.01269531,
            0.06542969,  0.06933594]), textin=CWbytearray(b'ad b3 8c c3 de d1 81 a4 12 da 59 07 cd 60 52 e4'), textout=CWbytearray(b'ae b0 b0 5b 00 2c f9 6f 4f 8d 31 8b 86 8b 48 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.140625  , ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'59 94 3f e8 29 f5 1f be e0 21 95 cc 35 de 7a 33'), textout=CWbytearray(b'cd d0 24 1a 91 73 fa 5f 86 7c a9 b1 2e 02 97 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14550781, ...,  0.00292969,
            0.06152344,  0.06445312]), textin=CWbytearray(b'fe da 07 90 f5 18 8b a3 be 09 50 2d 8e 35 20 75'), textout=CWbytearray(b'2d d7 41 cc aa 7c a0 31 a5 b4 5f 5f 36 9c 2d 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19921875, -0.14257812, ...,  0.00390625,
            0.06054688,  0.06445312]), textin=CWbytearray(b'd8 2a 32 92 0c 36 f2 e9 58 05 2a f9 45 85 d9 74'), textout=CWbytearray(b'77 26 c9 70 a8 53 b7 38 82 3f b9 24 3a 8a 2a 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.01757812,
            0.06835938,  0.07226562]), textin=CWbytearray(b'e0 03 f6 60 fa 56 4b f6 b2 92 f3 60 bc 5b e2 36'), textout=CWbytearray(b'e0 83 fd 52 f1 e0 b1 ba 2d 4a f9 18 ce f7 c2 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.14160156, ...,  0.02441406,
            0.07519531,  0.07617188]), textin=CWbytearray(b'00 23 e1 96 f5 ea 59 f5 29 b0 f8 89 0e ea c0 40'), textout=CWbytearray(b'c8 a9 ed ad fa e2 62 d8 f1 88 7f dc d5 9d 8c 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13964844, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'74 b2 53 f5 df c9 7d 91 fe 4b 8b 25 29 80 ab bb'), textout=CWbytearray(b'76 cc 49 09 77 12 6b 75 77 41 7f 3c 9a da 79 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.140625  , ...,  0.01953125,
            0.07324219,  0.07519531]), textin=CWbytearray(b'1a 3f b1 da 0f e7 a6 c5 60 06 99 5f a6 24 fa 05'), textout=CWbytearray(b'81 fc 46 66 52 b8 63 d0 22 23 cf 97 38 e2 9c 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14355469, ...,  0.00683594,
            0.06445312,  0.06640625]), textin=CWbytearray(b'e3 7c a6 f4 61 4f da 7f 15 14 6d 0b f3 7f 71 55'), textout=CWbytearray(b'54 10 2d 1a 84 62 6c 01 f8 0e 4c a5 db b0 e1 b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.14160156, ...,  0.00292969,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'5e dc 9e c8 77 56 92 62 b9 0e 06 6f 70 ff fe bb'), textout=CWbytearray(b'f2 1f 41 6d 30 48 23 30 76 97 ef c4 7e 4e 0d 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19433594, -0.13867188, ...,  0.03222656,
            0.078125  ,  0.07910156]), textin=CWbytearray(b'29 6c ce 2c 8e 16 65 09 dc 8a 83 3d 17 f6 c4 dd'), textout=CWbytearray(b'64 d7 de 09 99 99 90 5c 6c 4e 51 5c da 24 ac ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20117188, -0.14453125, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'1e 92 9c b9 89 fd ea f2 6c c0 de 02 96 70 29 52'), textout=CWbytearray(b'8a 26 15 0d 2b 44 81 0f 00 da 2f 43 a3 9b ad e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19726562, -0.140625  , ..., -0.00195312,
            0.05664062,  0.06347656]), textin=CWbytearray(b'a7 73 f5 40 b2 aa 49 8d 96 4e a3 96 da 8f 2a f6'), textout=CWbytearray(b'e1 20 ed 3b d8 dd f3 44 1f d9 f4 ec 9c f9 58 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14550781, ...,  0.02832031,
            0.078125  ,  0.078125  ]), textin=CWbytearray(b'6c f8 e7 14 1a 76 ad d7 19 60 10 5e 72 24 14 94'), textout=CWbytearray(b'92 00 36 12 f5 22 cf dd d4 e9 d4 55 82 e7 1a 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.13964844, ...,  0.01074219,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'f1 f7 68 35 d5 f1 56 3e e9 35 17 e6 f1 ca e0 93'), textout=CWbytearray(b'e0 29 7e 8b f5 7a 98 00 ef e8 73 36 cd 05 ac c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14257812, ...,  0.02636719,
            0.07324219,  0.07714844]), textin=CWbytearray(b'97 b8 1d 82 5d 1c 5a c9 a2 83 3f ba b2 e3 46 5a'), textout=CWbytearray(b'cf 28 1a 72 22 e1 02 d6 e2 f9 c1 af e0 c4 88 dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20117188, -0.140625  , ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'0f ba af 9e 6d 61 70 ec d6 a4 1b 16 fa 9b ca 21'), textout=CWbytearray(b'ca 69 2b e5 ba 4f 20 62 e1 87 64 a2 b4 76 c1 b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.00976562,
            0.06347656,  0.06933594]), textin=CWbytearray(b'18 67 de 1b f1 8a 1f d0 36 09 fd 40 2a 31 77 93'), textout=CWbytearray(b'25 a4 41 23 43 a6 70 ae b1 15 13 17 80 85 2e 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19921875, -0.14453125, ...,  0.00390625,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'bd d0 94 ae 7d 58 c7 f6 c3 de 6b bb 68 86 40 42'), textout=CWbytearray(b'b5 a5 a4 41 b9 ea 41 2b 0a 85 85 fc 90 37 e1 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19921875, -0.140625  , ...,  0.01171875,
            0.06738281,  0.06933594]), textin=CWbytearray(b'41 7d b9 4f 06 ef e6 25 00 6e 77 9c b3 5e e6 95'), textout=CWbytearray(b'19 e1 d1 dd 4f ea c1 60 5f 15 b8 78 bb de 47 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19042969, -0.13867188, ...,  0.01464844,
            0.06835938,  0.06835938]), textin=CWbytearray(b'97 f0 88 a3 41 33 84 c1 f5 a7 49 95 cb d7 3d c9'), textout=CWbytearray(b'24 67 4d 29 07 86 bc a3 f5 32 b8 2e d2 1b c7 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13769531, ...,  0.0234375 ,
            0.07519531,  0.07714844]), textin=CWbytearray(b'9b 0e d7 39 12 ba 53 4d 0f 42 65 e4 2c ad 27 c7'), textout=CWbytearray(b'94 08 1b 79 6e 72 7f 3b ba 9e a1 50 a2 e2 2e c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14160156, ..., -0.00292969,
            0.05566406,  0.05859375]), textin=CWbytearray(b'b4 e5 7d 5a 28 a4 1c de cf 70 d8 ee ff 91 06 7d'), textout=CWbytearray(b'a2 fe 03 0a 09 79 64 24 68 bc 0a 11 7c fc fc 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.14160156, ...,  0.00292969,
            0.05761719,  0.06542969]), textin=CWbytearray(b'd9 1e ef 06 9e 90 44 5b 7a 17 ff 67 60 47 98 da'), textout=CWbytearray(b'e0 51 d5 de 47 e6 be c4 8a 5a 65 cd 1a 75 97 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14160156, ...,  0.01074219,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'c6 f7 e5 2e f0 61 27 0b 9c cd 27 60 77 dd bc 73'), textout=CWbytearray(b'b2 1f 6d 05 cf af 28 ca ce a1 40 42 0f 2b b9 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14550781, ...,  0.00878906,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'53 07 82 cb 83 f9 ca 46 62 b5 ec 53 cc ee 05 68'), textout=CWbytearray(b'1e 61 a0 b7 1a e2 96 c2 47 e6 2b 80 5e 01 88 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.13964844, ..., -0.00488281,
            0.05273438,  0.06054688]), textin=CWbytearray(b'5a 3e cc 35 6a e2 54 4a 57 e4 d6 dc b7 61 27 a4'), textout=CWbytearray(b'ca 60 51 03 2f 1d ca a6 0e f2 4a 87 72 70 2d b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13867188, ...,  0.02441406,
            0.07421875,  0.07617188]), textin=CWbytearray(b'56 da c5 8c 68 7a 5d 91 71 05 38 08 8a 69 ce b4'), textout=CWbytearray(b'28 c3 35 d0 af da 48 78 4a 99 8a 29 ec f0 d5 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.14355469, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'13 5d db f4 9b d5 9f 09 ad d8 40 47 84 f2 75 74'), textout=CWbytearray(b'b9 64 7b 6f d5 fd 22 26 a4 41 37 97 f5 11 96 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.13769531, ...,  0.00683594,
            0.06347656,  0.06738281]), textin=CWbytearray(b'03 42 20 2e 5c 2b 40 63 a5 2a 43 f6 90 cb 2c f3'), textout=CWbytearray(b'cc 3a 35 a1 da 67 42 4a e9 2f 7a b6 e8 69 2d 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ...,  0.01855469,
            0.07226562,  0.07519531]), textin=CWbytearray(b'8c 5f fb f0 be 13 d0 ac 10 56 17 62 be 88 07 68'), textout=CWbytearray(b'4b b5 8f fe c6 84 1f 2e 95 8c f0 d9 b9 d3 9d 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14550781, ...,  0.02246094,
            0.07226562,  0.07324219]), textin=CWbytearray(b'0f 94 49 62 00 be cd 79 e8 7b d7 a1 a6 29 09 8a'), textout=CWbytearray(b'30 9e ab 76 06 9a c0 11 bb f5 83 39 e2 51 9e b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19726562, -0.13769531, ...,  0.01367188,
            0.06738281,  0.06835938]), textin=CWbytearray(b'25 35 d4 8b 0c ce 0e 3f 6a 5f bc ae 79 5b 94 d1'), textout=CWbytearray(b'ff c9 2d 7e aa 65 ad f4 bb 5a 26 cd 2c 08 4d 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13964844, ...,  0.00878906,
            0.05957031,  0.06738281]), textin=CWbytearray(b'93 c3 42 24 97 1a 93 1f 84 81 dc 3b 86 6b db b9'), textout=CWbytearray(b'8a 34 ca 50 b8 69 a8 b9 73 f3 97 a5 f5 d6 a4 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14257812, ...,  0.01464844,
            0.06933594,  0.07128906]), textin=CWbytearray(b'42 31 21 20 0a b9 1d ae 2c 5b fb 0e b4 74 ee 9a'), textout=CWbytearray(b'89 46 1a 5a 30 e6 aa 30 37 12 4d 20 5c 6d d5 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13671875, ...,  0.02832031,
            0.07617188,  0.07714844]), textin=CWbytearray(b'78 91 2a e0 73 51 e9 96 54 d3 51 85 be 9d 6f e7'), textout=CWbytearray(b'45 01 8e f8 bd b9 96 c1 a5 49 4e 88 e9 ca 18 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.14355469, ...,  0.01855469,
            0.06835938,  0.07128906]), textin=CWbytearray(b'81 8a b7 7f e4 1b 01 14 4c 24 94 fe b0 72 c3 91'), textout=CWbytearray(b'c5 2b f8 0f 6c da 4a 5f 69 6e 78 c7 e4 66 0f bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18945312, -0.13574219, ...,  0.01757812,
            0.07226562,  0.07324219]), textin=CWbytearray(b'cd 60 b5 6f 91 4b 85 db 92 23 ab 2c 37 a4 a9 c5'), textout=CWbytearray(b'24 34 e6 37 36 dd 59 35 fc 84 52 1c 63 51 f4 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.14160156, ...,  0.02050781,
            0.07128906,  0.07324219]), textin=CWbytearray(b'd2 9a a6 52 58 44 f0 3b 32 3f 3c 31 a1 1a 44 f9'), textout=CWbytearray(b'79 c2 a0 11 fe 2f 07 43 e9 ba d3 10 7d 6c 12 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14550781, ...,  0.00878906,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'd5 b9 89 00 55 60 77 d9 06 ed ed 30 98 2a ac 32'), textout=CWbytearray(b'72 93 72 c0 7a 75 98 bb b4 32 8e 4f 53 6c 5d 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13867188, ...,  0.01464844,
            0.06835938,  0.06738281]), textin=CWbytearray(b'99 35 f8 e1 95 ea f9 8e cc 43 3e ab ec 2f ac a8'), textout=CWbytearray(b'ac 15 69 25 bc 81 57 1e ef 5f f2 32 96 f4 32 56'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13671875, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'7d 10 df d6 eb 63 a9 c6 ed 82 76 c3 fb 9a bb f9'), textout=CWbytearray(b'47 26 41 4f c3 d7 82 63 f6 36 41 26 5e 07 6f 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14550781, ...,  0.01269531,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'd5 bc d2 91 5c 8f 36 8c fa f1 d3 4a 1b b4 09 49'), textout=CWbytearray(b'c2 7a 38 36 67 de 98 fa 84 fc a5 d4 1a f6 51 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14355469, ...,  0.01855469,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'70 c0 bb e8 75 7b e3 24 4b 16 6d 77 d4 59 e8 6a'), textout=CWbytearray(b'a8 df ec 43 cd df 3a e9 16 7f 98 90 e5 44 20 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19921875, -0.14648438, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'c7 8d 7f b3 81 f4 a7 0b 3b d6 bb aa c1 39 0a 6c'), textout=CWbytearray(b'10 ca 35 88 51 a2 71 1e bb 32 00 82 08 74 be a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.0078125 ,
            0.06152344,  0.06542969]), textin=CWbytearray(b'0c c8 7e ba 60 e4 73 69 3d 94 bd a1 76 4a f4 fd'), textout=CWbytearray(b'eb 4c 51 b3 f6 26 7a 52 d2 11 a0 0b c1 ea 0d 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13964844, ...,  0.015625  ,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'8e 72 5a 72 58 27 4a e1 45 12 c0 5d 6e 36 49 a9'), textout=CWbytearray(b'8d 8d c3 f0 a0 df bf f6 54 a2 3a bc 29 e1 87 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.20214844, -0.14746094, ...,  0.01660156,
            0.06738281,  0.07128906]), textin=CWbytearray(b'2d f7 f2 13 6e 90 b5 d8 dd 4e 6d 1b 21 7a 02 3b'), textout=CWbytearray(b'61 6a 54 e3 33 d8 9b ba 6d 2a d4 c9 14 3f 83 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14355469, ...,  0.01367188,
            0.06542969,  0.06738281]), textin=CWbytearray(b'4a bf 14 c8 97 8f ec fb 59 5b 79 1d 7e d8 a1 7d'), textout=CWbytearray(b'd0 00 71 2a cb 41 30 68 a8 b6 76 2e 63 91 ad 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14355469, ...,  0.01074219,
            0.06640625,  0.06933594]), textin=CWbytearray(b'94 22 99 67 2f ec 18 aa 08 3b ac ef f7 57 ed 2e'), textout=CWbytearray(b'a6 37 c0 66 4d 84 eb f2 7b 02 fd cc 9e 58 6a b3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14453125, ...,  0.00976562,
            0.06347656,  0.06542969]), textin=CWbytearray(b'19 9c db 59 7a e4 69 51 48 c1 d9 20 8f 5e 61 8e'), textout=CWbytearray(b'42 57 75 41 17 9d 71 42 d5 cd 3b 22 1a 18 ba 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19335938, -0.13671875, ...,  0.01074219,
            0.06542969,  0.06835938]), textin=CWbytearray(b'62 c1 cc 17 3b 0d df 92 8c 53 52 8c ea d8 20 e8'), textout=CWbytearray(b'2e 5e 53 cb 6e 90 12 62 c1 9c ad 3f e7 b2 9e 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14453125, ...,  0.02148438,
            0.07128906,  0.07324219]), textin=CWbytearray(b'52 ba eb 85 3e ab 1f c5 fc f7 70 71 19 0c 4f 75'), textout=CWbytearray(b'ce 84 f1 ed ae 2b a7 86 41 34 70 c2 6e 1a cf c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13867188, ...,  0.01757812,
            0.06835938,  0.07128906]), textin=CWbytearray(b'18 1d c1 44 b8 24 0e 4c 4d e0 31 52 86 7e fa f3'), textout=CWbytearray(b'a8 c7 c1 1f 57 b5 3a 8e 71 db af 6d a4 59 83 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13964844, ...,  0.0234375 ,
            0.07324219,  0.07421875]), textin=CWbytearray(b'6d 58 4f ae 17 ff 09 2a 68 b2 04 46 a9 e1 55 83'), textout=CWbytearray(b'20 e5 be 91 a6 03 2f f1 fa a7 44 bb 8c b3 48 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.13964844, ...,  0.01464844,
            0.06933594,  0.06933594]), textin=CWbytearray(b'8a 8a 05 c0 53 0e c4 de 60 ca 62 72 76 65 2a ee'), textout=CWbytearray(b'e3 4e b9 80 04 4b da 0c cf 6c 4f 45 6f 53 7e 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14550781, ...,  0.02441406,
            0.07421875,  0.07714844]), textin=CWbytearray(b'c6 07 3b fb b0 ea d6 10 bc 60 6b d3 0e 79 f3 0e'), textout=CWbytearray(b'70 4b 02 aa d5 64 5d ec d1 a2 ac 7e d3 70 40 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.13964844, ...,  0.0234375 ,
            0.07226562,  0.07324219]), textin=CWbytearray(b'97 3f a5 1f ac a2 bc 21 da ef 7a dd cf c6 4a 16'), textout=CWbytearray(b'92 e7 d1 c5 f4 16 26 49 9c 09 ca b0 d0 4b 90 a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.14160156, ...,  0.02734375,
            0.07617188,  0.07910156]), textin=CWbytearray(b'a9 0d 0d 5f af 15 78 fc 2d a9 6d c6 5f ad 96 a7'), textout=CWbytearray(b'f7 9a f3 a3 25 b9 e0 5a ec a4 d4 48 c7 61 ac e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14550781, ...,  0.00488281,
            0.06054688,  0.06542969]), textin=CWbytearray(b'66 24 3e c5 ce 72 1a 25 ad 83 63 c3 e3 f6 1a 70'), textout=CWbytearray(b'67 6e 94 5d 94 27 21 38 63 7c f3 30 ca fb b1 da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14355469, ...,  0.01367188,
            0.06542969,  0.06933594]), textin=CWbytearray(b'0e 15 45 8f df 05 91 a3 d4 51 cb 58 ba 3f 70 2c'), textout=CWbytearray(b'59 36 f0 87 e8 4a 20 9c 89 5d 3c 69 47 21 94 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19628906, -0.14648438, ...,  0.01367188,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'9f bc ba ce c0 6c 5c ea 9b 07 9b 93 cc 13 06 41'), textout=CWbytearray(b'c6 40 c6 3c d5 b8 3e 67 56 68 d8 75 07 3c 50 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.14160156, ...,  0.00292969,
            0.06054688,  0.06445312]), textin=CWbytearray(b'b2 e8 20 96 60 0d 77 50 1f cd bb d6 d5 62 d8 fa'), textout=CWbytearray(b'26 45 6d b9 eb 04 5e 2a 92 f0 24 73 98 c8 ec 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14257812, ...,  0.03125   ,
            0.07910156,  0.07910156]), textin=CWbytearray(b'2c e9 3b 87 f2 a4 09 80 18 cb fc 3a ec 3a d1 75'), textout=CWbytearray(b'ad fe 0c 4d 0a da 03 7e 51 cb 08 36 b6 63 eb ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19042969, -0.13964844, ...,  0.00195312,
            0.05859375,  0.06347656]), textin=CWbytearray(b'f7 03 86 c7 17 b5 10 51 b6 73 85 c3 47 07 6d ab'), textout=CWbytearray(b'81 6c 85 42 5c b1 a0 d0 54 c3 30 96 ec c9 a9 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20214844, -0.1484375 , ...,  0.00488281,
            0.06152344,  0.06445312]), textin=CWbytearray(b'39 76 01 16 df b6 9b 7f 51 cf 56 96 58 40 40 0b'), textout=CWbytearray(b'29 8a 5c 1f af d4 ca d7 ff 9c 01 8a b9 39 69 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19921875, -0.14648438, ...,  0.0078125 ,
            0.06445312,  0.06738281]), textin=CWbytearray(b'26 ed 56 bb ea 56 17 97 6a b9 63 46 9d 0b 1e 88'), textout=CWbytearray(b'64 73 b0 1c cc 1c ec ac 61 2f 86 a6 c3 49 e2 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.140625  , ...,  0.00878906,
            0.06152344,  0.06640625]), textin=CWbytearray(b'4d 85 ca be 4a eb 1d 72 5e 65 35 92 6a 9b e6 fc'), textout=CWbytearray(b'be d3 4e 4f f2 74 79 17 7d 12 0c 90 38 f6 ec 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14257812, ...,  0.02539062,
            0.07519531,  0.078125  ]), textin=CWbytearray(b'6a 05 83 b2 10 76 81 39 85 53 8c 9a eb 98 e1 3e'), textout=CWbytearray(b'3c 3f e7 d6 b1 15 94 05 2a e9 e5 40 67 43 c5 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14355469, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'40 c3 fa 2e c0 52 7a d2 8a 92 77 41 f3 5b 13 35'), textout=CWbytearray(b'0f 73 76 33 ae d5 9b 21 d3 27 de f2 c8 13 9e 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.140625  , ...,  0.01464844,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'14 c6 45 bd 19 ac fc a1 e4 ba 88 8c 13 09 fe 3b'), textout=CWbytearray(b'37 43 81 5a 56 31 29 fc 17 86 d2 41 2e 58 4a 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14160156, ...,  0.01269531,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'1c 6a 36 1d 4e 3c 2b a3 af a9 ed 09 e6 54 40 ca'), textout=CWbytearray(b'c9 80 46 b4 7b f3 c5 f7 c2 99 e0 86 ba 9d 5c 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14257812, ...,  0.00878906,
            0.06152344,  0.06738281]), textin=CWbytearray(b'1e fb fd 18 65 02 22 88 60 44 6a 71 b8 05 fd 08'), textout=CWbytearray(b'cc 5f 8d f7 18 22 11 24 ad de 19 ca 19 b2 50 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14257812, ...,  0.0078125 ,
            0.06347656,  0.06542969]), textin=CWbytearray(b'32 1c 45 f4 bc cb 6d c0 bf ee 3f 72 ec c3 42 3c'), textout=CWbytearray(b'46 56 ec 15 a2 4d a4 41 2e 56 ab 81 25 02 a6 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14746094, ...,  0.00976562,
            0.0625    ,  0.06933594]), textin=CWbytearray(b'f9 74 94 3b 9e 8e 4f e2 bf c6 e7 c9 01 84 02 62'), textout=CWbytearray(b'f6 0a 64 41 09 d1 c6 b3 0d 35 35 fa 44 c0 dc 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.14257812, ...,  0.00292969,
            0.06054688,  0.06445312]), textin=CWbytearray(b'f2 8a 80 3c dc 53 47 6b 35 ae db d3 5f e8 bd 36'), textout=CWbytearray(b'fa db b6 e0 5f 64 23 0a d3 60 a4 ab d0 ca 14 b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19042969, -0.13867188, ...,  0.00683594,
            0.06152344,  0.06640625]), textin=CWbytearray(b'7a 36 da a0 ac ca f3 04 98 70 50 3d 31 d2 d4 a3'), textout=CWbytearray(b'dd e8 3a c7 92 4c 5c 1b bc 41 b1 f6 66 2a f5 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.13964844, ...,  0.015625  ,
            0.0703125 ,  0.06933594]), textin=CWbytearray(b'41 92 21 d6 89 0f 76 51 2a 41 c2 d1 d4 ff 90 b4'), textout=CWbytearray(b'56 19 17 a7 32 0d f1 0f 07 f1 f7 9b 98 4d 16 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13769531, ...,  0.01171875,
            0.06542969,  0.06738281]), textin=CWbytearray(b'28 be 31 1a ab 00 8d d0 7d fe c2 ed 65 98 1d b8'), textout=CWbytearray(b'df fb 22 c5 cd f8 81 94 81 41 dd 63 7c 2e d7 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.14355469, ...,  0.02050781,
            0.07128906,  0.07324219]), textin=CWbytearray(b'48 74 b2 74 dd fd c6 f7 03 9d 65 7c d7 51 b6 9b'), textout=CWbytearray(b'8a 4b e5 97 39 a3 5f 47 e1 cc db 7a 89 af ec 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19433594, -0.13574219, ...,  0.03222656,
            0.078125  ,  0.07910156]), textin=CWbytearray(b'34 49 b0 bb 73 fb d9 a3 b3 90 90 73 ac 68 cf e1'), textout=CWbytearray(b'6f d2 17 38 1a e8 50 0b c9 65 6c f6 c2 b1 b8 13'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ...,  0.01953125,
            0.07128906,  0.07421875]), textin=CWbytearray(b'93 40 34 9e 1c c6 b0 0e 5a 8a c1 9d ea 5f 28 21'), textout=CWbytearray(b'1c 91 c9 39 80 b6 03 53 9b b4 a0 9c 4d c9 e9 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.14257812, ...,  0.01171875,
            0.06347656,  0.06933594]), textin=CWbytearray(b'62 56 5d 1b 4f f3 76 29 4c 8e 57 eb 9d 4a 4e b4'), textout=CWbytearray(b'99 d7 60 33 9d 35 af ff fb 9f b0 43 c9 41 62 e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14550781, ...,  0.01367188,
            0.06445312,  0.06835938]), textin=CWbytearray(b'7b 5c 87 3d c5 24 a8 54 de 2f a2 14 38 5a 43 8f'), textout=CWbytearray(b'bb 60 e6 ba b3 92 6d 18 ed f0 72 9a 80 17 c5 da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14648438, ...,  0.01855469,
            0.07128906,  0.07421875]), textin=CWbytearray(b'f3 84 c4 d8 b3 c3 28 d6 b8 e7 92 9f 6d 2d 60 4a'), textout=CWbytearray(b'4b 7e 98 64 49 e4 0f 8f 87 4b 5b cc 8d a7 e8 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13769531, ...,  0.00976562,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'cf da 64 f3 1c eb 22 bd 78 ee 79 05 55 34 e4 a9'), textout=CWbytearray(b'4b 73 b3 91 f9 61 f7 0b 75 97 31 b6 d3 d5 66 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.20019531, -0.14257812, ...,  0.02148438,
            0.07324219,  0.07324219]), textin=CWbytearray(b'b8 3c 44 4b 84 84 fc 03 c6 34 fe e9 ab b1 8e 2b'), textout=CWbytearray(b'9b 1e 2f 80 66 66 2f 75 d7 44 19 93 84 da 59 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13769531, ...,  0.01757812,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'94 42 d2 fe ed 4a 14 f8 44 3e 70 61 2f b5 33 a7'), textout=CWbytearray(b'24 ef a9 6a df fd 27 8b 96 69 ee b7 ac 66 50 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13964844, ...,  0.01367188,
            0.06445312,  0.06835938]), textin=CWbytearray(b'66 57 6c ba 0c 82 96 c7 55 4e 1c b6 3c ea 79 dc'), textout=CWbytearray(b'88 50 10 83 39 80 cd eb 2a 4a 8c 40 e6 48 11 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.01367188,
            0.06738281,  0.07226562]), textin=CWbytearray(b'24 ee ab 04 ed 6b b9 39 2c 91 dc 1c 13 f6 14 83'), textout=CWbytearray(b'3a 44 a1 e7 ab 0e 93 44 c2 08 cb 8b ec 87 9d 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14257812, ...,  0.00683594,
            0.06347656,  0.06542969]), textin=CWbytearray(b'03 39 65 50 94 49 c7 7e 13 e6 4d 18 b3 1b 8c 7b'), textout=CWbytearray(b'f0 17 56 1b 4e f4 00 53 6c b4 33 3a b5 10 e8 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14257812, ...,  0.01464844,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'29 16 65 f5 b5 60 6c c7 76 64 af 70 d7 ad c9 16'), textout=CWbytearray(b'5c 49 20 35 93 c2 26 8e cb 75 4d 17 1b fa 96 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'a6 38 b9 07 2e 43 66 33 aa ca 93 6d 78 c0 c1 1f'), textout=CWbytearray(b'41 5b d5 f2 62 c9 62 92 b1 02 a4 46 6d ea a5 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.140625  , ...,  0.01464844,
            0.06445312,  0.06835938]), textin=CWbytearray(b'cd fd 0f aa 47 53 92 c3 da 87 bf 61 aa 81 8c dc'), textout=CWbytearray(b'3b 8a 66 b5 38 5b e8 0b c2 d0 8b a5 47 ed fe 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13769531, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'28 5e cf f0 d8 dc 1e 23 f0 04 f3 11 bc 66 ab ac'), textout=CWbytearray(b'65 d7 2d 73 d7 55 0e 29 28 ca ae 81 dc 29 5a 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19921875, -0.14648438, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'dc 86 3c 8c d7 b9 45 cd 6c 7a 88 4d f2 2a 4e 9a'), textout=CWbytearray(b'18 01 09 7d b9 ae d0 ab 20 ef 75 ea 9b a9 0f 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14550781, ...,  0.01074219,
            0.06445312,  0.06835938]), textin=CWbytearray(b'9e 0c 4f 34 9e 56 1f aa a9 3d 48 f9 b6 62 9c 0a'), textout=CWbytearray(b'c0 68 64 bb 8b e9 92 c3 4d 17 cb 03 57 b6 b2 12'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.140625  , ...,  0.015625  ,
            0.06835938,  0.07128906]), textin=CWbytearray(b'e3 65 b3 aa 51 69 5d ea 2e 90 72 76 73 5d bb e7'), textout=CWbytearray(b'a9 ff b2 71 b0 2c 4e c0 76 48 0d ca 15 e5 8e c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14453125, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'12 03 0d cf 6b d9 c1 08 59 3a bb a1 c9 dd 63 0c'), textout=CWbytearray(b'59 60 c2 09 db 19 c5 18 7c 93 3b 87 fc 6e 26 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.13769531, ...,  0.00195312,
            0.05957031,  0.06347656]), textin=CWbytearray(b'31 3b 37 c1 30 84 92 c5 00 ad d9 a2 41 02 09 b6'), textout=CWbytearray(b'1d 66 6a d2 82 33 65 79 2d a4 59 80 20 be b1 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.13964844, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'6e 5c a6 29 3b 48 8c 6d 28 50 bf 54 ac 27 0f f4'), textout=CWbytearray(b'78 dd 6c 78 d7 8b a4 fc c3 59 d4 2c 0d cb c3 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14453125, ...,  0.01269531,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'8f 27 ce 86 5d 3b aa d7 64 fe f5 4a 8c 52 24 26'), textout=CWbytearray(b'48 37 b4 69 99 cb 33 75 b1 58 69 3c 2c c0 81 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.14257812, ...,  0.015625  ,
            0.06835938,  0.07128906]), textin=CWbytearray(b'd4 40 0f d1 0e c0 2d d4 39 8b 6c 44 d3 88 a5 5d'), textout=CWbytearray(b'34 c2 6f b5 32 60 ab 91 44 63 7f f5 84 f5 73 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14550781, ...,  0.00585938,
            0.06054688,  0.06445312]), textin=CWbytearray(b'f7 78 22 91 36 dc 75 32 48 8f 81 96 a6 48 d2 6d'), textout=CWbytearray(b'50 21 9d 37 76 9c 16 77 d2 92 a1 07 83 bb 92 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14648438, ...,  0.01660156,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'cd d1 72 77 6e 07 df 37 52 4b 61 ee d6 3c a4 9a'), textout=CWbytearray(b'74 d7 a0 c7 84 7e 8c c7 42 6f 65 30 1f b9 4f b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14550781, ...,  0.02050781,
            0.07421875,  0.07324219]), textin=CWbytearray(b'c1 e8 4d e2 80 2e 32 da 1e ae 6a 37 42 a9 4d 4e'), textout=CWbytearray(b'73 93 be 70 48 65 62 58 42 1a 77 cd 91 ae fe a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14453125, ...,  0.00097656,
            0.05566406,  0.06152344]), textin=CWbytearray(b'9d 0b e8 71 2c 55 88 60 ac de 1d 27 6e 68 6f 32'), textout=CWbytearray(b'0b 23 e7 f4 fa 18 68 d4 78 c5 8a 95 2f bb 82 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13671875, ...,  0.00488281,
            0.06347656,  0.06640625]), textin=CWbytearray(b'bf a7 e2 65 a8 d9 64 e0 33 5d b6 a1 d9 0b aa a5'), textout=CWbytearray(b'cd 22 25 79 f8 ea cc 8f f5 51 f0 56 57 ae 01 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19921875, -0.1484375 , ...,  0.00390625,
            0.05859375,  0.06445312]), textin=CWbytearray(b'c5 dc 52 16 0b 3b 4c 2b da 9b f1 8a d6 b4 0c 0a'), textout=CWbytearray(b'9d 46 18 cc c0 9e 1f d1 cf 53 ba 50 a8 b0 c9 43'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14257812, ...,  0.01464844,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'88 d2 f8 33 f5 53 f3 43 5d f6 3d 35 32 0b 32 79'), textout=CWbytearray(b'ad e2 4b 0d 0d 85 07 2f e3 1e 25 e3 4c 3d 41 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13671875, ...,  0.01171875,
            0.06738281,  0.06835938]), textin=CWbytearray(b'c8 ed eb 65 08 02 b3 8f 43 93 65 86 28 81 ee c2'), textout=CWbytearray(b'12 3e 21 73 fe a7 9f 49 e4 5b 55 f3 41 b3 ba fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13769531, ...,  0.00390625,
            0.06054688,  0.06445312]), textin=CWbytearray(b'08 8d 9e 8e 0a 19 f9 d7 17 1e e8 8c 43 db b6 a4'), textout=CWbytearray(b'9f 89 25 fc 04 96 04 66 1a f9 1f 4a 69 c6 48 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14257812, ...,  0.01171875,
            0.06542969,  0.06933594]), textin=CWbytearray(b'68 77 6b 5e 63 a8 5e 19 6e 62 df ba 0c 3c cb 9f'), textout=CWbytearray(b'43 12 13 3b 26 ce ce 27 b8 64 26 98 5c e1 e0 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.13769531, ...,  0.01660156,
            0.06933594,  0.06933594]), textin=CWbytearray(b'ee 23 aa e9 14 05 34 bf d6 ea d2 e5 58 e6 44 ce'), textout=CWbytearray(b'9c 4f ab 45 76 fe 0c 64 ef c6 ed f0 9c 6f 68 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.01171875,
            0.06445312,  0.06933594]), textin=CWbytearray(b'05 48 bb 3f 4d 6a 67 2a b2 79 26 d4 b6 d1 8d a4'), textout=CWbytearray(b'89 ff 2c 89 2d f3 97 18 96 11 d5 76 44 2a aa 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19433594, -0.13769531, ..., -0.00097656,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'fa 9c 21 5a 78 6f d8 28 62 39 22 6d 53 e3 69 a2'), textout=CWbytearray(b'5c 1c 5d d3 c5 99 2f 49 56 79 5b ab 38 a5 53 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14355469, ...,  0.01464844,
            0.06835938,  0.07226562]), textin=CWbytearray(b'6c 9d cc 1c cb 51 f8 a7 f5 9f ce 05 a9 4f 88 42'), textout=CWbytearray(b'89 02 a8 40 74 44 9a b8 ba cf b0 01 e9 6c 5d e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14453125, ...,  0.00390625,
            0.06054688,  0.06542969]), textin=CWbytearray(b'dd 9a 06 e1 a8 f8 2b 64 ab c3 6b 16 d0 c0 2e 2f'), textout=CWbytearray(b'ce 7b 48 66 65 c1 d9 67 f9 53 32 60 71 f9 b4 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13671875, ...,  0.01269531,
            0.06640625,  0.06835938]), textin=CWbytearray(b'23 b0 1a c3 d2 56 90 89 92 fe ef dd 39 db ec e2'), textout=CWbytearray(b'31 ae ff b4 ee 94 77 e6 39 cb a9 e2 7c 44 9d ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14355469, ...,  0.01074219,
            0.06738281,  0.06933594]), textin=CWbytearray(b'8b 9e d9 b9 75 0a a2 fd 89 c9 d4 71 8c a9 5f 3b'), textout=CWbytearray(b'20 73 cf e9 16 df c2 4e fd 6e 61 52 aa 31 f0 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.14160156, ...,  0.00390625,
            0.06152344,  0.06445312]), textin=CWbytearray(b'56 68 9c 46 69 cc fe 65 2c a9 a4 11 e7 14 81 ba'), textout=CWbytearray(b'0c cd 02 ee d3 6d be d6 c2 f1 cc ed 68 a9 d2 b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13574219, ...,  0.01269531,
            0.06445312,  0.06835938]), textin=CWbytearray(b'4d 41 25 00 c3 99 0d e2 a1 67 08 5c d8 17 f1 c0'), textout=CWbytearray(b'41 e8 3f 6a 53 e8 e9 8c 70 7e 21 f9 7c e0 61 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14550781, ...,  0.02246094,
            0.07324219,  0.07519531]), textin=CWbytearray(b'53 80 9a d4 69 7d 5f cd 81 65 31 59 d7 6b 01 3e'), textout=CWbytearray(b'0f 1f b4 53 50 c4 3f a4 51 ea 5c 82 33 0f 79 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19042969, -0.13476562, ...,  0.00585938,
            0.06152344,  0.06445312]), textin=CWbytearray(b'0c 63 7d 82 30 15 8a 66 c9 df e9 d1 e0 66 bb a7'), textout=CWbytearray(b'33 89 f5 c9 50 5e e5 e0 67 52 44 14 48 50 28 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14648438, ...,  0.01074219,
            0.06445312,  0.06640625]), textin=CWbytearray(b'c6 4d f8 d9 e9 8f e1 89 a8 b9 b1 4c 66 8d 49 24'), textout=CWbytearray(b'71 25 8a b6 c0 94 f0 d5 f4 4c d3 3e 7c 28 99 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19824219, -0.14648438, ...,  0.0078125 ,
            0.06152344,  0.06445312]), textin=CWbytearray(b'cb 95 18 80 55 34 83 8a 41 af af ec 5d 6a 57 4d'), textout=CWbytearray(b'8b 1b 34 65 1d 57 e4 b1 3e 02 c6 1a 2f 96 67 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20117188, -0.14257812, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'a2 a8 4b b2 5e 4d 34 08 f1 e2 fa ac 59 e6 e8 3e'), textout=CWbytearray(b'cb fc 3c eb e0 e4 5c 3c 2a 29 35 d7 6b f4 73 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14453125, ...,  0.02539062,
            0.07226562,  0.07519531]), textin=CWbytearray(b'12 9b f3 cd 7c 24 fd d5 3a 41 38 d7 cd ff 52 8c'), textout=CWbytearray(b'92 8e 3a 00 eb bd 25 ba 07 01 30 a7 1f 4e 52 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20410156, -0.14355469, ...,  0.00097656,
            0.05761719,  0.06054688]), textin=CWbytearray(b'0b 06 ce da f9 e4 2c af fd cd aa c6 0e 30 32 1d'), textout=CWbytearray(b'6e 17 c6 88 ff cb a7 e3 45 98 41 5f 1f 31 fd 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.14257812, ...,  0.01074219,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'ec 62 ff d5 6c 46 74 dd e6 fc a5 de 15 05 d5 6e'), textout=CWbytearray(b'5e 7c e3 f6 50 90 7c 20 0f 7f 63 7b 46 39 59 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.01367188,
            0.06542969,  0.06933594]), textin=CWbytearray(b'66 65 eb 63 df 6a 32 01 79 ec 19 23 6e 19 ae f1'), textout=CWbytearray(b'5d 92 9a 68 76 49 03 4c 7b 04 9e 56 d5 4c 10 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.14160156, ...,  0.00683594,
            0.06054688,  0.06445312]), textin=CWbytearray(b'b6 d9 6a 63 86 49 46 c6 4c 2b 94 d2 b1 76 c6 7d'), textout=CWbytearray(b'30 e2 b9 f9 d7 45 de aa 3a cd 7b 1a 2f 58 1a 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14355469, ...,  0.02050781,
            0.07128906,  0.07128906]), textin=CWbytearray(b'88 9e 74 74 3e 5e d4 3c 39 42 48 4f 84 eb 94 6d'), textout=CWbytearray(b'1c f5 10 d0 4c 93 53 51 29 ef 49 01 72 b1 99 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19921875, -0.14160156, ...,  0.02246094,
            0.07324219,  0.07714844]), textin=CWbytearray(b'78 7a 33 43 92 5c 69 00 cc 54 25 5a 4d d1 c0 23'), textout=CWbytearray(b'94 96 7e f7 3a 25 19 7c 39 e1 57 e5 1c ee d7 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13964844, ...,  0.00195312,
            0.05761719,  0.06445312]), textin=CWbytearray(b'23 d4 41 8f bf ea da a1 3f 73 54 a1 df 1e d2 05'), textout=CWbytearray(b'81 1d 08 fb ac 9a 82 9b 6c 06 4e 07 83 a4 74 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14257812, ...,  0.00097656,
            0.05664062,  0.06445312]), textin=CWbytearray(b'1f d6 2d cf d3 cf 16 50 7e 7b f4 dc 71 9a 45 08'), textout=CWbytearray(b'90 85 47 8e b0 c3 66 2d 79 be 96 2c ed de 4d 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14648438, ...,  0.01171875,
            0.06347656,  0.06738281]), textin=CWbytearray(b'7d ea d6 b4 b4 41 0a c0 ea af fb 59 57 9d 34 05'), textout=CWbytearray(b'95 bd 58 92 8d 8a bf 52 47 ca cc 0f 90 01 a2 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.14160156, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'4a 60 b6 66 31 a2 09 24 c9 2a aa 99 d4 53 fc 92'), textout=CWbytearray(b'ce 3a a9 9e fb d3 ac ef 33 29 4d d5 cc 9c 9d 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13574219, ...,  0.015625  ,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'4d 21 c7 f5 7c 94 01 68 27 12 1d b7 1f b9 c6 db'), textout=CWbytearray(b'84 d1 74 aa 11 0c df 74 68 c2 95 06 fc 3b c2 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14257812, ...,  0.00585938,
            0.06054688,  0.06640625]), textin=CWbytearray(b'1c b6 87 f1 8e 14 23 92 78 83 09 4f 72 7f 4b df'), textout=CWbytearray(b'39 08 e7 a9 5d 1f 52 8a 44 35 6b b6 4e 5f 61 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14550781, ...,  0.01074219,
            0.06347656,  0.06738281]), textin=CWbytearray(b'51 4e a1 0d fe 7f 0f b8 79 4e a9 e1 ba fb 0c 4f'), textout=CWbytearray(b'69 64 b0 c9 66 bc b7 48 9c 23 00 3d 8f 73 6e be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13964844, ...,  0.01953125,
            0.0703125 ,  0.07421875]), textin=CWbytearray(b'a1 2e dd 1c 6b 76 fd 5f 98 75 6d 1d 8e 0b cb cb'), textout=CWbytearray(b'66 7f fd 87 f2 35 5e 07 54 eb 80 39 bf 42 9e 21'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13671875, ...,  0.01367188,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'fb 6d 01 49 d9 6a ee a8 03 d7 3e f6 78 93 ad d5'), textout=CWbytearray(b'18 36 78 a3 dd af 08 b5 d2 a4 2d 3a 4f e9 51 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'a2 af ea cb 11 0d 30 9e 16 6b 8a f2 ff 79 c3 8e'), textout=CWbytearray(b'64 29 e2 55 66 45 0d 97 0b 93 b6 03 88 3b b1 e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19140625, -0.13867188, ...,  0.03125   ,
            0.078125  ,  0.078125  ]), textin=CWbytearray(b'fb 6f 45 31 c4 9d 63 89 0e 9c 25 72 44 aa d6 a9'), textout=CWbytearray(b'ca 5c 9f 46 02 4f 7b 54 7e 1b 01 03 ed 7d ec 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.14160156, ...,  0.015625  ,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'87 b0 35 3d 68 86 45 b2 43 36 be a1 9b 79 65 07'), textout=CWbytearray(b'bb e0 6c e7 48 6e d8 a0 de 09 c4 a1 09 87 be 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19335938, -0.13769531, ...,  0.01269531,
            0.06640625,  0.06835938]), textin=CWbytearray(b'f3 5e 7b 0d ec cb c5 ea 56 7e 99 31 c9 a5 a3 de'), textout=CWbytearray(b'07 90 48 cc 43 d4 cf 78 eb 57 87 29 d7 d0 a1 ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13574219, ...,  0.0078125 ,
            0.06152344,  0.06640625]), textin=CWbytearray(b'19 cd a9 21 2f 48 ce 76 fa d5 9c 53 f6 f6 fb b1'), textout=CWbytearray(b'ee 20 81 50 f9 bc 2d 4c 93 7c 73 da f4 c8 4b 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.140625  , ...,  0.01660156,
            0.07128906,  0.07128906]), textin=CWbytearray(b'67 b8 db f9 ae 4b 0c 86 86 f6 b9 c9 53 24 c6 6d'), textout=CWbytearray(b'4b dd 33 bd 42 be 8f 97 b1 9d fe 1f c3 01 c3 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14453125, ...,  0.01660156,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'd2 b3 88 a2 bf b8 6d 0f 17 0d da f1 90 ab 98 29'), textout=CWbytearray(b'b3 e0 d1 50 4d 74 94 11 a5 d4 0c 87 c9 9d 1b 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14355469, ...,  0.01855469,
            0.06933594,  0.07324219]), textin=CWbytearray(b'61 07 13 2d 18 cb 5a de 5e b9 62 2d 19 0a 8e ef'), textout=CWbytearray(b'2e bc 92 b2 62 50 2c 9d bf ea cc b0 d6 ca 8b c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.140625  , ...,  0.01074219,
            0.06542969,  0.06738281]), textin=CWbytearray(b'91 c4 52 45 04 f1 6d 1f 7e 73 7d 45 11 cf 1f a9'), textout=CWbytearray(b'08 c2 a1 c6 04 17 58 65 13 36 a7 fc 8d 0c d4 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.02148438,
            0.07324219,  0.07617188]), textin=CWbytearray(b'4e ab 3e 80 71 65 6b 36 54 c5 3c 04 e2 78 9c d5'), textout=CWbytearray(b'4d 14 9d 8f 63 c3 76 93 3d 6f 6e fd c7 a9 01 a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13867188, ...,  0.00390625,
            0.05859375,  0.06445312]), textin=CWbytearray(b'd9 28 bc f8 5a 13 c8 8a b5 b0 51 3b b7 2c c4 55'), textout=CWbytearray(b'5f 30 af ef aa 7c 3e 9c a6 30 e3 1c dd 6b b9 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.14257812, ...,  0.01367188,
            0.06835938,  0.07128906]), textin=CWbytearray(b'80 4f cf 7d 93 fb a7 d3 d0 95 d7 36 1e fc 72 26'), textout=CWbytearray(b'7e 55 09 88 f1 94 11 5d 62 39 4b a4 2d ff c2 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14550781, ...,  0.01464844,
            0.06835938,  0.06835938]), textin=CWbytearray(b'0e cf 30 7d 60 1f 3d ad 17 25 1a f0 cd 25 a4 9f'), textout=CWbytearray(b'ad 39 15 b3 6f d7 0a 27 9c fe ae 8b 08 00 3c 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14453125, ...,  0.01953125,
            0.06933594,  0.07226562]), textin=CWbytearray(b'53 ba ba 9a a8 46 88 99 5f 0b 49 24 70 65 03 66'), textout=CWbytearray(b'8a de d8 8b b6 3c 48 f3 30 2a 2d 85 c1 79 c0 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14746094, ...,  0.01660156,
            0.06835938,  0.07324219]), textin=CWbytearray(b'5a 8b f8 2b ea 66 28 27 0f f8 46 fe 82 58 8f 03'), textout=CWbytearray(b'1c 1c 8c 6d ed c3 f2 71 72 fc 92 a1 1d 3c 1e c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13964844, ...,  0.01953125,
            0.0703125 ,  0.07519531]), textin=CWbytearray(b'f0 0d 0c 4e 96 3d 61 2e 48 4f cd 7b 07 9a db 35'), textout=CWbytearray(b'2d 93 23 dd 29 16 9f a2 fc ec 41 0e f2 35 6d 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1953125 , -0.13671875, ...,  0.01660156,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'28 2e fc 58 23 eb 0d 7b c6 02 10 d2 bb 88 c7 ca'), textout=CWbytearray(b'3f 3e 87 9a 8d ad d3 8e 05 8a 98 51 7b b2 a4 c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.14160156, ...,  0.01464844,
            0.06835938,  0.07128906]), textin=CWbytearray(b'08 d5 ce fc 12 01 5f 0a 40 ad f1 51 71 d5 e8 08'), textout=CWbytearray(b'7d 25 1a 6b ba 26 1a a1 e6 97 08 bb 91 82 33 a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13867188, ...,  0.01757812,
            0.06933594,  0.07324219]), textin=CWbytearray(b'4b 4d 24 12 0e 0a 0a a3 73 f8 cf 48 0c e8 7f f0'), textout=CWbytearray(b'd6 2b e9 93 d5 d3 c3 06 f3 23 8b f4 c1 ac a4 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'fd fc 2e 87 8a a4 ee d6 41 e6 00 1d 7c 7f da a5'), textout=CWbytearray(b'68 d7 51 2d df ac 87 05 a5 2a 31 4e 9d 16 df e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19042969, -0.13671875, ...,  0.01464844,
            0.06738281,  0.07226562]), textin=CWbytearray(b'8e a5 4b 8a cc 05 d3 80 ec 81 db 57 fd 33 b5 f1'), textout=CWbytearray(b'79 8f 9b 45 58 5c db 4c 0b ee 77 5b d0 31 b1 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19140625, -0.13671875, ...,  0.00976562,
            0.06445312,  0.06835938]), textin=CWbytearray(b'6f e2 e3 ff 84 9e 22 a3 50 61 ca 27 86 a9 cb c5'), textout=CWbytearray(b'6e 5c 4b e0 7a 38 f2 3d ea 28 10 58 7e fb c3 d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13867188, ...,  0.02441406,
            0.07324219,  0.07519531]), textin=CWbytearray(b'83 ec 0f f8 b2 d7 8e 92 a7 d3 47 30 f6 12 ac cd'), textout=CWbytearray(b'dc a7 80 38 19 08 a1 b7 f9 35 6d 56 54 87 fd f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14453125, ...,  0.015625  ,
            0.06445312,  0.0703125 ]), textin=CWbytearray(b'22 9b 07 26 81 bc 53 76 71 84 c9 ca 6a 31 2f 0f'), textout=CWbytearray(b'c5 b9 49 0d d4 25 62 0d 0f 6d af 08 ed a1 d5 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.14160156, ...,  0.02832031,
            0.07714844,  0.07617188]), textin=CWbytearray(b'b1 7c 0a e9 f0 4e 08 c6 4e 8a 26 c0 1d ae c4 95'), textout=CWbytearray(b'09 4b 76 8e 2f 01 0f 0b 8f 57 ae 15 61 a9 3d 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14648438, ...,  0.00195312,
            0.05566406,  0.0625    ]), textin=CWbytearray(b'e5 13 19 71 bd 7e 3a af b2 cf db 18 b3 8d 99 38'), textout=CWbytearray(b'20 15 10 4c 25 d2 3f a8 71 1e ae 44 1b 96 6e 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14257812, ...,  0.015625  ,
            0.06542969,  0.06835938]), textin=CWbytearray(b'c8 16 6f 20 e4 e0 a4 19 86 7e 7a e5 16 08 f2 1f'), textout=CWbytearray(b'43 03 57 48 ca 97 2c 29 eb 50 28 ae b5 8c 28 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19921875, -0.14550781, ...,  0.02441406,
            0.07421875,  0.07519531]), textin=CWbytearray(b'b8 29 15 22 aa 6e ff 8b 83 e6 ea d4 6c 85 96 0c'), textout=CWbytearray(b'd8 43 e3 3a 52 41 c1 2f b4 1f 29 2d 56 a8 3a 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19335938, -0.13476562, ...,  0.01074219,
            0.06347656,  0.06933594]), textin=CWbytearray(b'58 ed d8 26 f6 b2 cd 46 11 5e 65 f2 d8 60 ac f1'), textout=CWbytearray(b'b7 22 79 34 38 4f 77 1d cd a8 17 1f b4 b6 de 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.140625  , ...,  0.        ,
            0.05859375,  0.06347656]), textin=CWbytearray(b'cf 22 0c e6 f3 c8 fc 30 a4 3c 61 7b f2 b4 7a b7'), textout=CWbytearray(b'73 5c 95 e9 0f b6 c2 c5 80 a3 82 cf 7d 45 29 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19433594, -0.13769531, ...,  0.01367188,
            0.06738281,  0.07128906]), textin=CWbytearray(b'1d e8 a7 06 1c 1a d9 d4 1f 6e 88 44 ca f1 1f ae'), textout=CWbytearray(b'f1 30 fc 2d 66 89 6a e0 26 5d b9 9a 10 84 18 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14355469, ...,  0.00488281,
            0.06152344,  0.06542969]), textin=CWbytearray(b'3f 0b c7 7f 29 03 8f 81 10 a9 3a 03 ce 43 cc 11'), textout=CWbytearray(b'6e ac 7e 37 34 2c ab ce 23 22 31 0f 76 e8 fc 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19921875, -0.14648438, ...,  0.00585938,
            0.06152344,  0.06542969]), textin=CWbytearray(b'3c d2 99 4b 1d ac 7a 78 31 00 3c 2e 11 86 8a 7c'), textout=CWbytearray(b'88 26 9e a1 66 5b e0 fe 21 65 9e 29 ae 37 79 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.13964844, ...,  0.01855469,
            0.07128906,  0.07128906]), textin=CWbytearray(b'9d e4 5e 9d 27 d3 97 f7 cd 37 f3 c8 60 e8 5a bb'), textout=CWbytearray(b'90 f0 93 85 60 92 a7 80 72 47 4e ac f4 7b 02 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.203125  , -0.14550781, ...,  0.02148438,
            0.07128906,  0.07324219]), textin=CWbytearray(b'c5 98 60 4f b2 b3 fb e4 36 5c 32 bb e8 86 0a 9f'), textout=CWbytearray(b'ac e0 06 7a 9b 85 01 19 d9 2e 1e 8b e3 5b 73 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.13964844, ...,  0.00195312,
            0.05859375,  0.06152344]), textin=CWbytearray(b'b1 fb 63 8b 02 ea 89 38 d4 58 55 2f 8a 0f 1a e8'), textout=CWbytearray(b'ff a5 97 b9 6b d0 16 bc 40 5b 3e da 92 69 88 1d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13964844, ...,  0.01953125,
            0.07226562,  0.07128906]), textin=CWbytearray(b'37 c2 b6 72 b9 3f ad 9e 71 95 73 d8 e0 46 b0 69'), textout=CWbytearray(b'38 49 49 a9 82 73 31 a2 e2 18 ee bb cc c3 1c d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.13964844, ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'61 ea 7a 27 50 d4 9c 49 9c 9f 6a c8 ec ec 60 66'), textout=CWbytearray(b'f1 3c 87 12 6b 7c 89 4e 3e 57 6f ae d3 6b 56 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.140625  , ...,  0.015625  ,
            0.06933594,  0.06933594]), textin=CWbytearray(b'b9 e7 06 d3 22 0c b0 be 6b 0a 17 70 2f 5c ad 17'), textout=CWbytearray(b'6e cd 06 c9 4e 5c 39 72 51 7b aa fd f9 b3 3e 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14257812, ...,  0.02050781,
            0.07324219,  0.07421875]), textin=CWbytearray(b'0e 8f 31 59 d7 b8 43 3b d6 06 e8 25 33 a2 04 6c'), textout=CWbytearray(b'29 c9 a3 c7 ff 67 31 53 42 fe 9c f2 9a 73 95 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13867188, ...,  0.03027344,
            0.078125  ,  0.07714844]), textin=CWbytearray(b'32 4f 06 3d c3 36 0d 27 7a a7 b1 b3 ae 42 03 ba'), textout=CWbytearray(b'97 4e 7f 66 4f 9b b0 65 c5 3a a3 f0 36 0b 35 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.14160156, ...,  0.01171875,
            0.06542969,  0.07226562]), textin=CWbytearray(b'a6 93 08 68 93 ad 06 9e ec ee 8a f5 ff 2e e0 53'), textout=CWbytearray(b'1b ff 29 cc 7c ff 16 65 13 41 50 54 ad 45 94 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13964844, ...,  0.02148438,
            0.07226562,  0.07226562]), textin=CWbytearray(b'32 f6 13 12 71 c7 24 cb f7 4a 1f 5d eb 94 cd bc'), textout=CWbytearray(b'4b 82 7d 19 ef f9 20 a1 d1 0d a6 14 b6 f0 7e 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14355469, ...,  0.00976562,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'24 fc 2b db f2 b5 32 4b c4 f9 03 00 25 2b 12 47'), textout=CWbytearray(b'b6 ff 09 b0 34 b1 4c 64 0d 10 1f c5 b3 1f 7c b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13867188, ...,  0.00878906,
            0.06347656,  0.06738281]), textin=CWbytearray(b'53 7e 8d c7 f5 5c a8 10 1e 59 94 0d a2 d1 f7 ce'), textout=CWbytearray(b'17 4e 60 1c 56 5a 30 a0 fb 31 0d 22 c6 9a 8c 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.14160156, ...,  0.02148438,
            0.07421875,  0.07421875]), textin=CWbytearray(b'a6 04 a2 da 44 61 89 96 f6 96 6f fc d8 0f cd 56'), textout=CWbytearray(b'a1 55 08 da 08 9e aa 1d b8 ed c2 b0 72 bb d3 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.13769531, ...,  0.02148438,
            0.07128906,  0.07617188]), textin=CWbytearray(b'1c a4 ce 51 2d 3a 55 2c 3c 5f 3b e7 b1 b0 aa 65'), textout=CWbytearray(b'8c 92 73 eb 9f b8 2d 56 16 37 a2 a7 f2 27 a7 e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14257812, ...,  0.00292969,
            0.06054688,  0.06445312]), textin=CWbytearray(b'96 db 75 75 1a 66 c2 38 f9 c3 2e 76 cf 0f b8 ab'), textout=CWbytearray(b'54 9e 78 3c 91 f3 8b c1 31 87 99 bc 08 fe 66 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.14453125, ...,  0.02050781,
            0.07226562,  0.07226562]), textin=CWbytearray(b'7b bc f6 de 62 b2 76 f3 a4 54 bd b3 f5 66 74 10'), textout=CWbytearray(b'68 04 e0 33 db b5 5b 01 7a 21 a4 82 01 00 3c d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14550781, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'f2 49 7f 10 d0 89 94 c2 e0 d4 f9 35 e6 4d da 1b'), textout=CWbytearray(b'de e3 8e 6a 11 61 d3 e0 70 9f ee cf f1 2c 9a 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14355469, ...,  0.02246094,
            0.07128906,  0.07226562]), textin=CWbytearray(b'3c 3d 48 27 f8 29 d0 29 4a c9 1a ea 75 18 7f 74'), textout=CWbytearray(b'62 aa e3 85 82 54 f8 46 25 4f a6 8d 4a 4e bd 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13867188, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'e2 e4 5f 73 a6 d2 5c 4e 74 f6 52 f5 82 09 18 cb'), textout=CWbytearray(b'f6 bc 4b 37 31 12 4b 57 a6 85 fd fe f5 5a 97 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19628906, -0.13867188, ...,  0.        ,
            0.05664062,  0.06054688]), textin=CWbytearray(b'51 dd 17 bd 91 69 f9 e6 86 9a 6a 64 89 8d c8 c8'), textout=CWbytearray(b'96 10 d3 1a 46 d1 44 64 63 fe 42 a8 e3 e6 6c 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.1875    , -0.13769531, ...,  0.02148438,
            0.07226562,  0.07226562]), textin=CWbytearray(b'01 27 5c 26 45 fc 74 c5 b5 da 0d 88 7b b3 db a3'), textout=CWbytearray(b'ab 14 c6 4b 0b f7 6f f7 97 e3 af 5c df 3f e3 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14160156, ...,  0.01171875,
            0.06738281,  0.06835938]), textin=CWbytearray(b'eb 9a 04 8f 30 26 e5 22 9b 83 d3 f1 f3 83 7b 49'), textout=CWbytearray(b'74 e2 29 d9 9a a6 4e 9c 6f bd d1 92 f0 34 74 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.00976562,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'59 8a 73 dd 27 7c da 29 f5 36 6d 35 e3 75 99 8e'), textout=CWbytearray(b'34 f1 83 c2 38 e9 d7 89 f7 a7 72 6d f4 f3 a0 c2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.140625  , ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'7a 40 c1 31 48 2b ce 2c ef a4 b7 a5 69 16 e9 84'), textout=CWbytearray(b'ad b8 5c 47 0b e6 6a 8b 77 86 a3 62 48 d4 9c cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.13964844, ...,  0.01269531,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'97 31 dd ab c2 6b 8a 9a 1f 9a ab a3 a5 e2 ec 54'), textout=CWbytearray(b'e8 ad 28 dd a3 0d 20 05 42 4b 8b ec ae 9d 9d f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19921875, -0.14160156, ...,  0.01464844,
            0.06542969,  0.07128906]), textin=CWbytearray(b'de e9 a6 d2 41 85 cc 72 99 6a fe c4 fa 45 3c e0'), textout=CWbytearray(b'97 cb db 93 d7 99 f8 92 b7 90 5c 04 c5 5e eb fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1953125 , -0.13574219, ...,  0.02441406,
            0.07519531,  0.07519531]), textin=CWbytearray(b'63 16 51 7f 31 ae a9 77 26 f9 58 e7 6e f2 7a b9'), textout=CWbytearray(b'3d 8c ab f4 d1 04 b1 2d 63 27 82 b9 ad af 97 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20117188, -0.14355469, ...,  0.02246094,
            0.07324219,  0.07324219]), textin=CWbytearray(b'28 c7 c9 d0 9b e0 52 f6 04 bd dd 62 86 88 19 42'), textout=CWbytearray(b'a4 0e 45 ac 70 b0 b5 70 d0 97 58 17 42 bb 74 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.203125  , -0.14355469, ...,  0.0078125 ,
            0.06445312,  0.06640625]), textin=CWbytearray(b'ca b7 63 cc ca f1 66 8b 0e 24 eb 18 6a 9a 55 9b'), textout=CWbytearray(b'77 98 73 59 ff 52 ea 59 13 27 df 35 66 49 b0 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1953125 , -0.13769531, ...,  0.02050781,
            0.06835938,  0.07226562]), textin=CWbytearray(b'1d 56 c6 2b df f1 9a c6 89 1a 1f 9b 84 70 e1 a0'), textout=CWbytearray(b'cc fa 98 b8 d0 65 88 61 a9 f6 93 26 8f 07 02 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.0234375 ,
            0.07226562,  0.07617188]), textin=CWbytearray(b'b5 ad 42 f9 df ee e7 1a f5 9d 11 1e 4f 1f 01 d8'), textout=CWbytearray(b'71 ae 6a de 2f 37 12 76 84 91 5a 39 03 56 29 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19335938, -0.13671875, ...,  0.01269531,
            0.06640625,  0.06933594]), textin=CWbytearray(b'01 9e b3 14 ba 5c f6 2e 39 3b 57 de 95 43 dc e9'), textout=CWbytearray(b'6d 6a d9 2e b8 eb 6a b3 8e c6 66 1f 2b 28 1a 1a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18847656, -0.13867188, ...,  0.01757812,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'07 48 8c 33 9c 21 56 e1 32 fe 7d 38 97 28 e0 b2'), textout=CWbytearray(b'28 a6 f3 6b d6 cd 7f 08 76 5d ac 92 bf b4 e2 da'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.14160156, ...,  0.01464844,
            0.06933594,  0.07128906]), textin=CWbytearray(b'c3 2a 2a 0b b0 c8 17 3b ab 15 57 c0 08 68 df 76'), textout=CWbytearray(b'ef d9 49 60 d6 77 aa fa e2 e1 2f 66 2f c0 6d d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14746094, ...,  0.00390625,
            0.06054688,  0.06640625]), textin=CWbytearray(b'75 95 e4 43 43 fd c0 35 e6 46 ff f2 2a 55 68 5c'), textout=CWbytearray(b'74 38 e3 1c 1c 39 df b1 a1 05 b4 22 1f e9 c1 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.140625  , ...,  0.00683594,
            0.06152344,  0.06640625]), textin=CWbytearray(b'8c 52 b9 ac 02 97 5b 17 88 e6 e5 ad 75 90 a6 3b'), textout=CWbytearray(b'57 a1 bd ca 30 cb 77 7b 4a ce 6c ce 47 00 23 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13964844, ...,  0.01367188,
            0.06835938,  0.07324219]), textin=CWbytearray(b'49 c4 97 d1 78 88 aa f4 a0 8a fd b6 6f 07 b7 18'), textout=CWbytearray(b'80 4c 3c 49 39 44 31 e0 dc df 89 d5 55 c0 fa c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13964844, ...,  0.01171875,
            0.06542969,  0.06933594]), textin=CWbytearray(b'06 e7 30 ab 2e 57 cc 2b 5a 11 25 a1 d3 07 4b a1'), textout=CWbytearray(b'dd 89 a5 ae 8c 9c 9c 37 4d b6 77 08 f1 2d 70 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14453125, ...,  0.02148438,
            0.07128906,  0.07324219]), textin=CWbytearray(b'c1 0a e2 5a 25 8d 8f 14 d1 15 6d 4a c0 fe a7 0a'), textout=CWbytearray(b'b9 3c 80 9b 34 bf 2c be ed 12 f2 e4 9e 66 39 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19238281, -0.14550781, ...,  0.00585938,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'd5 4e 20 ad a1 95 9f 1c a6 0e 8a 4f bb 7a e5 4d'), textout=CWbytearray(b'4c 35 1f 0e f5 92 d6 42 96 54 76 87 2b e8 b4 d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14160156, ...,  0.01367188,
            0.06347656,  0.06835938]), textin=CWbytearray(b'76 c1 c6 0d b7 80 b5 1e e3 27 f1 40 73 64 51 36'), textout=CWbytearray(b'02 bb f8 b5 b0 1e 1a b3 5d 7e 7f a5 45 d9 37 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19921875, -0.140625  , ...,  0.015625  ,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'70 b0 93 5a df e0 57 5f 2b 17 28 23 91 05 1a a2'), textout=CWbytearray(b'60 4a 3b 2b c7 03 f7 c0 46 d4 91 c0 2d ab 92 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14355469, ...,  0.02148438,
            0.07226562,  0.07226562]), textin=CWbytearray(b'd4 99 13 d2 22 3d 75 76 89 7c 2f e8 86 77 32 39'), textout=CWbytearray(b'0f fb 51 bd 14 9f 0e 68 5b 0a 8a a1 a9 e3 df 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19042969, -0.13769531, ...,  0.02050781,
            0.06738281,  0.07128906]), textin=CWbytearray(b'df af 8b 26 ae c2 96 1d 1c 77 5b 68 87 77 df c4'), textout=CWbytearray(b'c3 a3 15 f5 5e 09 10 36 f2 aa 28 d6 70 97 b3 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13964844, ...,  0.00683594,
            0.06347656,  0.06738281]), textin=CWbytearray(b'8c b9 90 d5 1e ce ae af 45 98 2f 45 e7 11 4e b7'), textout=CWbytearray(b'71 39 f8 4b d4 b8 df b7 56 25 b1 bb 9d e0 6d 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19433594, -0.13574219, ...,  0.01757812,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'fb 42 b0 11 3b 85 63 ef be c1 ac ef 4d 1c 44 a1'), textout=CWbytearray(b'd9 13 62 97 27 97 cb 62 48 a3 3f d7 01 ed de 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14355469, ...,  0.01464844,
            0.06835938,  0.06835938]), textin=CWbytearray(b'6b cb 79 50 40 0d 6b 16 04 0e 44 77 f2 e9 1e 22'), textout=CWbytearray(b'9f 95 25 22 51 a7 58 38 1f 1d 15 40 74 92 3a b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.01171875,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'ef b6 09 9f ba d8 51 e6 a2 d4 05 4e c0 9e 35 b9'), textout=CWbytearray(b'f2 cd 39 94 a6 c1 4e 8a 2a e7 17 f2 64 7b 9c 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14355469, ...,  0.0078125 ,
            0.06152344,  0.06738281]), textin=CWbytearray(b'0f 23 ef 48 e6 0e d2 64 c4 35 10 18 f5 96 48 53'), textout=CWbytearray(b'e8 a5 cd 7a 30 32 95 35 3f 36 7a b6 ea 6c 8b 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20410156, -0.14550781, ...,  0.02050781,
            0.06933594,  0.07128906]), textin=CWbytearray(b'79 47 f3 15 58 24 76 f5 94 b2 6d ba 06 d1 3e 8a'), textout=CWbytearray(b'17 fd d6 2b 70 1d dc 36 64 db 9c d5 8e 40 60 fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ...,  0.01464844,
            0.06640625,  0.06933594]), textin=CWbytearray(b'54 36 9f a5 de f2 85 3a f0 d9 2f 94 a1 95 be 8d'), textout=CWbytearray(b'67 bb c0 5c 32 56 db 9b 9b 34 1c 92 63 60 21 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.140625  , ...,  0.01953125,
            0.06933594,  0.07226562]), textin=CWbytearray(b'11 74 a2 2a 43 0a 01 6d a2 3d 72 25 7a ff d8 88'), textout=CWbytearray(b'cf c2 d1 73 98 5a 57 19 4e f6 0f 1b b8 94 2a a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14160156, ..., -0.00195312,
            0.05566406,  0.06054688]), textin=CWbytearray(b'dd 88 e0 87 0c dc 3a c3 df 79 7c 3f eb ab 21 21'), textout=CWbytearray(b'00 96 fd c7 37 bb 7d 2b aa 0f 4c 4b c4 f6 d6 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14355469, ...,  0.00976562,
            0.06152344,  0.06640625]), textin=CWbytearray(b'ef 3f 4c fa 26 cf 64 af 0a c0 b6 89 dc ae 00 46'), textout=CWbytearray(b'6b 55 69 0b 94 fe 37 07 9e b1 21 06 89 98 1d 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14453125, ...,  0.00488281,
            0.05957031,  0.06347656]), textin=CWbytearray(b'49 02 10 d4 0e 91 7b b2 85 8a b3 43 4e 08 d4 6c'), textout=CWbytearray(b'7f fc 9d a8 45 9b ff 02 21 f9 c1 68 dc d9 0a a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13964844, ...,  0.02148438,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'91 65 ce f5 a2 5b 16 d2 0e ba 38 54 91 85 76 d7'), textout=CWbytearray(b'7d 6b e1 f3 45 9f 73 2e 60 56 1e f2 9a 52 70 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13867188, ...,  0.01855469,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'51 51 8d 86 e7 bf 14 a8 d3 78 e2 e1 b2 48 5e f2'), textout=CWbytearray(b'53 94 1f c2 da e7 ac 14 d8 84 da 94 94 2f b3 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13769531, ...,  0.0078125 ,
            0.06152344,  0.06542969]), textin=CWbytearray(b'cc ad d1 6b ba 60 f4 56 14 34 49 a3 0c 5c 17 ad'), textout=CWbytearray(b'd7 ec ef 42 c4 92 a4 dc ae 43 7b c0 22 a6 44 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19433594, -0.13574219, ...,  0.01464844,
            0.06835938,  0.06933594]), textin=CWbytearray(b'1e fd b1 7f f1 6c 82 aa 83 d5 fc ef af e3 d3 ed'), textout=CWbytearray(b'66 04 a4 d5 86 ed 53 88 05 d1 ef 3f d8 1f 1f 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.13964844, ...,  0.02246094,
            0.07324219,  0.07421875]), textin=CWbytearray(b'2c 87 e3 0e d9 cf eb 01 fe 7e d8 3a 39 46 c6 54'), textout=CWbytearray(b'55 4e f2 29 52 d1 44 f6 cf 0e b2 11 83 7e af 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14257812, ...,  0.00878906,
            0.06347656,  0.0703125 ]), textin=CWbytearray(b'a4 5e 69 13 35 fa 5b 8a ec cd ed 27 0d dc 58 7b'), textout=CWbytearray(b'81 f0 a2 27 04 23 15 7a 57 49 0f 21 2c 0c 41 12'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14160156, ...,  0.015625  ,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'0f 2f 8e e8 e9 52 c0 e8 44 4d b2 90 28 14 27 21'), textout=CWbytearray(b'33 1a 7c ad 49 34 50 af 26 6d 96 ae 1f 3f a1 ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20117188, -0.14160156, ...,  0.01855469,
            0.06933594,  0.07128906]), textin=CWbytearray(b'2e 30 eb 9d 2a 9d 00 b3 f8 ef fe 44 58 e0 0c 37'), textout=CWbytearray(b'78 ef b6 d8 5a 6a 6c 4f 49 18 34 ac fc 0d 7c 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13964844, ...,  0.01269531,
            0.06347656,  0.06835938]), textin=CWbytearray(b'b1 40 2e d5 cc 18 80 5e fc 9b 8a 5c d1 15 ea e1'), textout=CWbytearray(b'd2 32 ad 8f 2d d4 01 a3 db ef 8a 05 57 23 7b a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14453125, ...,  0.00390625,
            0.05761719,  0.06445312]), textin=CWbytearray(b'29 7c 13 76 3f 20 21 ee 1b f0 0d 50 73 4a 8a 98'), textout=CWbytearray(b'5f d6 10 1b 4c 98 1f 7a 72 7a f9 92 38 a3 63 db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.140625  , ...,  0.02050781,
            0.07226562,  0.07226562]), textin=CWbytearray(b'd7 92 07 17 87 c6 ff 87 b0 f3 46 60 db 89 59 4f'), textout=CWbytearray(b'cb 2a 7a 98 d6 c7 31 f8 e1 5b 2f 92 e0 1d 30 bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.03125   ,
            0.07714844,  0.07714844]), textin=CWbytearray(b'1b 68 e0 1e 00 60 35 da 1f 7a aa d0 da 0d 08 51'), textout=CWbytearray(b'cc 6b 5b 6c db 71 3d 7e 0a 96 34 21 9e c2 e1 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14257812, ...,  0.00390625,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'89 a3 00 a7 df db d5 fe 42 53 1b 3f 45 bc cd 9d'), textout=CWbytearray(b'75 57 f7 49 d2 e1 e4 62 85 5d 0a 6f 01 f2 83 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13867188, ...,  0.01074219,
            0.06542969,  0.06640625]), textin=CWbytearray(b'99 e6 7b bf e5 56 d0 01 8c 23 45 c4 0b f0 95 e4'), textout=CWbytearray(b'48 f5 42 7b f4 16 37 7f ff 89 0f 95 18 a4 72 e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.13769531, ...,  0.02050781,
            0.07128906,  0.07226562]), textin=CWbytearray(b'cd 14 28 b9 a3 21 60 fe bf 1c 20 a3 52 48 ad 71'), textout=CWbytearray(b'a4 5d 37 b2 0d dd 5b c7 3f 05 83 53 e3 2f e0 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19140625, -0.13867188, ...,  0.00488281,
            0.05859375,  0.06445312]), textin=CWbytearray(b'2c f6 b6 a0 74 38 06 04 50 d7 81 b8 de af 7a cf'), textout=CWbytearray(b'ef 15 d4 5b 8b 7e 94 c4 4d 05 7c 74 82 93 82 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19335938, -0.13671875, ...,  0.00683594,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'45 7a a4 73 85 6a 75 a6 9d 5f f6 93 37 8a cf b9'), textout=CWbytearray(b'10 b9 49 6c d6 b7 0f 90 31 7a 89 b2 86 e5 ef bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13964844, ...,  0.01660156,
            0.07128906,  0.07226562]), textin=CWbytearray(b'43 7a 79 b6 c1 de 72 88 d1 c9 2b 69 a9 82 45 cf'), textout=CWbytearray(b'6e 61 21 fc e3 87 4a 68 48 c9 57 c7 c9 b7 57 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.140625  , ...,  0.00585938,
            0.06152344,  0.06640625]), textin=CWbytearray(b'83 98 72 17 31 8b 6f 97 b8 3f e2 eb 14 d2 b8 7e'), textout=CWbytearray(b'27 d8 2d a0 83 52 53 29 96 44 bd 29 8d d7 a0 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19140625, -0.13867188, ..., -0.00390625,
            0.05566406,  0.0625    ]), textin=CWbytearray(b'96 17 ef 44 13 b8 70 3d 8a 19 0c 86 05 78 80 c5'), textout=CWbytearray(b'cb 2f 31 d2 0b cf 01 3b 48 e3 ac f5 d3 bf 9d 81'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14257812, ...,  0.01074219,
            0.06445312,  0.06738281]), textin=CWbytearray(b'a9 cc 09 c7 66 2b d8 3c 2d 4a d4 d5 3f 23 66 6d'), textout=CWbytearray(b'23 4a 39 00 69 1a 0a b5 1f 1f a1 5a e5 20 d1 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14355469, ...,  0.01269531,
            0.06640625,  0.06738281]), textin=CWbytearray(b'a7 9b 93 17 02 4e a2 27 0e 24 ff 1b 13 dc 07 5d'), textout=CWbytearray(b'c2 25 1b 71 df 0e b1 d5 91 65 a6 0e 56 ef 6a 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13574219, ...,  0.00976562,
            0.06152344,  0.06640625]), textin=CWbytearray(b'a1 9e 9a 48 f3 e0 40 63 68 a9 fb 4e 04 8f b3 dd'), textout=CWbytearray(b'67 5e 09 ad e3 06 ad 8f 62 ea 63 3e 60 0b 9a 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.20117188, -0.14453125, ...,  0.02246094,
            0.07421875,  0.07617188]), textin=CWbytearray(b'77 76 f0 48 22 5c 0a 35 96 f9 c2 78 b1 5e 2c 9d'), textout=CWbytearray(b'2b ff 44 06 45 60 b9 f2 ec 92 5e b3 d1 8a d3 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13671875, ...,  0.02050781,
            0.07128906,  0.07226562]), textin=CWbytearray(b'e1 6f f3 ad a8 56 c2 90 3a 66 58 7b 92 c3 c0 ec'), textout=CWbytearray(b'87 99 13 1c 28 50 8b 45 06 99 82 2a 98 5b aa 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14355469, ...,  0.02050781,
            0.0703125 ,  0.07617188]), textin=CWbytearray(b'dc 9c 59 05 10 27 5e d9 9c b9 1f 50 11 96 45 8a'), textout=CWbytearray(b'6f 26 41 82 8a 87 d7 0d 0a ed a4 e3 1a 8b 47 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19726562, -0.14648438, ...,  0.0078125 ,
            0.05761719,  0.06542969]), textin=CWbytearray(b'b2 9c 48 a0 26 cc 03 30 84 a3 82 e0 5a 90 d2 6a'), textout=CWbytearray(b'a1 c3 72 d5 6e 0b a2 98 2b 04 6a c5 e1 51 88 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13769531, ...,  0.01367188,
            0.06835938,  0.06835938]), textin=CWbytearray(b'f4 29 dc ee 0b ab f5 3d 2b 27 29 6c 83 cc c0 ae'), textout=CWbytearray(b'74 90 a1 f9 bd 24 0b 3f 38 29 9f fd b8 fc 14 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14453125, ...,  0.01757812,
            0.06835938,  0.07128906]), textin=CWbytearray(b'01 1e da de 8e 9f 9a d9 6d 52 51 e9 dc ed 29 7d'), textout=CWbytearray(b'7b 57 8e 43 f2 d3 e6 69 4b e4 4d 83 a6 06 fa 42'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13964844, ...,  0.015625  ,
            0.06640625,  0.07128906]), textin=CWbytearray(b'f0 8d 8f 69 97 11 de 8f cc 46 03 7b 1f 50 c1 ea'), textout=CWbytearray(b'b2 eb 8e de 3d ee 3d 52 9c c0 ee 3f ad 41 62 fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14355469, ...,  0.01171875,
            0.06542969,  0.06933594]), textin=CWbytearray(b'70 99 d3 73 af ad c1 cc fe ce f5 27 6b e6 57 9b'), textout=CWbytearray(b'a9 49 62 6b 54 8c ab db 50 72 7d 70 3b c9 e5 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ...,  0.01464844,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'9a 35 c6 0e 81 d7 a9 a1 26 db de dd f8 65 01 7d'), textout=CWbytearray(b'2c 46 3c 60 c9 3f a1 5a c7 41 3e 1f 63 94 b2 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14550781, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'ff 53 e6 82 fb 15 5c fd 5c 97 05 e0 c3 a2 78 7e'), textout=CWbytearray(b'b5 37 f6 33 0d c4 28 da 4e 9c 74 14 40 ce 28 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14453125, ...,  0.02050781,
            0.07226562,  0.07324219]), textin=CWbytearray(b'df b0 a5 a4 8f ad 94 89 a7 c3 b1 49 3b 26 3e 80'), textout=CWbytearray(b'd3 73 7e b4 11 22 00 ae e0 a6 64 bd 40 f1 ad 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.01660156,
            0.06933594,  0.06835938]), textin=CWbytearray(b'83 b9 7e 07 14 6d fc cf 36 d3 fa d2 c0 a9 d3 53'), textout=CWbytearray(b'76 fc 93 de 0f 07 7d fc fc 8a 67 cd ed 75 63 b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19140625, -0.140625  , ...,  0.01464844,
            0.06542969,  0.06933594]), textin=CWbytearray(b'91 ca 9d cc 89 20 7c 44 b7 43 71 a9 27 84 99 ba'), textout=CWbytearray(b'd9 f5 96 fd 41 e4 ef d7 5a fe 53 0e 33 ba 33 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14355469, ...,  0.01074219,
            0.06542969,  0.06933594]), textin=CWbytearray(b'84 49 27 41 8d 9b 30 14 3a 89 11 73 aa 68 2a 60'), textout=CWbytearray(b'6f 10 6f 3c 14 21 b1 30 44 cf 9b f4 ac 19 e5 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14355469, ...,  0.00292969,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'09 23 c7 a8 29 95 e0 a2 c2 a7 ac c8 09 dd 7c 7d'), textout=CWbytearray(b'bd 04 e8 20 83 7f d3 a0 8f c6 93 2d 31 46 00 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.203125  , -0.14355469, ...,  0.02050781,
            0.07226562,  0.07226562]), textin=CWbytearray(b'02 72 5c d2 58 2a cd 5d 85 3e 9b 0f 4e 80 12 79'), textout=CWbytearray(b'bd 4e 11 f0 3a 4b 98 1d 72 08 9d df 54 58 3d 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14648438, ...,  0.00878906,
            0.06152344,  0.06640625]), textin=CWbytearray(b'9f bc 7a 65 6a 56 b9 59 6f f5 b8 bc 24 df 91 3a'), textout=CWbytearray(b'a5 85 3b c1 90 fe 8a a0 b8 f0 fd c5 b4 6b a2 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19140625, -0.13671875, ...,  0.01855469,
            0.07128906,  0.07421875]), textin=CWbytearray(b'0b fa ca b6 d5 fb 63 17 37 30 18 19 69 d7 d5 a9'), textout=CWbytearray(b'09 2d f1 5f 06 10 41 7f 77 6c e4 b0 f8 14 cb 42'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.20117188, -0.14550781, ...,  0.01074219,
            0.06347656,  0.06738281]), textin=CWbytearray(b'7c 48 8c 48 b7 95 cd 46 db 8c 91 64 f5 2f 26 2c'), textout=CWbytearray(b'c1 43 93 b4 45 a7 30 51 34 1f ca 3e 5c 15 18 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19238281, -0.13964844, ...,  0.02246094,
            0.07421875,  0.07324219]), textin=CWbytearray(b'ff 1b a0 6a 09 c1 bc 57 2c 8b d7 21 76 18 af ed'), textout=CWbytearray(b'7d 4c 56 48 b0 1d 66 88 d4 e5 22 9b 37 78 d2 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14355469, ...,  0.        ,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'7b f2 1e d4 d6 31 75 49 6d 6e ff 14 03 b2 e9 5f'), textout=CWbytearray(b'04 02 61 aa 22 f0 b4 77 d9 54 f0 5f 9f b4 e5 be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.140625  , ...,  0.00878906,
            0.06445312,  0.06738281]), textin=CWbytearray(b'41 68 78 c0 4c 08 e7 c8 1a 39 7c 57 41 8c fb 7d'), textout=CWbytearray(b'46 d9 6b 45 c9 5d 89 6a b6 25 31 31 cb e1 03 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14355469, ...,  0.015625  ,
            0.06835938,  0.06933594]), textin=CWbytearray(b'0c 24 ca cf e9 db 6f e2 33 2a e1 d3 03 3f a5 7f'), textout=CWbytearray(b'42 f5 25 79 63 f7 bb e5 99 f3 04 cd be 09 90 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13867188, ...,  0.015625  ,
            0.06738281,  0.07128906]), textin=CWbytearray(b'bf aa 7a ab ea a9 bc 74 cb 39 bb 86 52 11 ba d9'), textout=CWbytearray(b'79 3a e6 7d 4c 0c d3 4d 57 03 90 86 a1 63 d3 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13769531, ...,  0.00195312,
            0.05957031,  0.06445312]), textin=CWbytearray(b'ae c0 d5 99 0b e7 e1 9e 2a 00 ac 3a b4 d5 d5 df'), textout=CWbytearray(b'e2 30 7a bf 2c 86 d4 31 4a ff 9c 1c 32 6a f2 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19921875, -0.14453125, ...,  0.01464844,
            0.06835938,  0.07226562]), textin=CWbytearray(b'3e 22 ed 1b 56 25 4a da 98 ad ff 6f 49 00 9f 4f'), textout=CWbytearray(b'ce 98 94 c7 e2 dd 5b 60 0f 9d f5 8e 2e ae 19 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.140625  , ...,  0.02539062,
            0.07617188,  0.07910156]), textin=CWbytearray(b'c5 43 c2 06 ae e8 5d 3b 83 a5 3f 01 34 e4 40 67'), textout=CWbytearray(b'78 6c 08 dc c2 75 a4 e2 bb ea 64 46 ea 61 61 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14355469, ...,  0.01367188,
            0.06445312,  0.06933594]), textin=CWbytearray(b'88 9e db 7c 11 0b 8c 4f 50 7c 42 c4 86 01 55 72'), textout=CWbytearray(b'2e a5 48 40 fc 69 51 39 40 8b 81 27 fb 0e 9e e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14453125, ...,  0.01367188,
            0.06738281,  0.06835938]), textin=CWbytearray(b'02 ef 54 a8 75 28 0f 12 70 ab 0f ac 52 ee fa 6f'), textout=CWbytearray(b'0b d5 9c 07 9f 53 31 ef a4 ed ca 3d 51 5a f8 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.13964844, ...,  0.01660156,
            0.06933594,  0.07421875]), textin=CWbytearray(b'a7 cb 19 63 85 09 ba 64 a7 4b 00 fb 8b 4d 48 72'), textout=CWbytearray(b'ab 55 dc a0 40 42 2f f6 03 66 42 99 36 b7 09 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14257812, ...,  0.01855469,
            0.06933594,  0.07226562]), textin=CWbytearray(b'33 d5 e4 fc 5f d3 32 1a df dd ae 09 17 28 91 6c'), textout=CWbytearray(b'7a ec dc 21 a5 05 5b 26 c8 f4 3d 83 3f e9 9b 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14160156, ...,  0.01464844,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'7e 5e c3 63 a0 9b 45 13 41 d2 66 2e c2 61 a1 69'), textout=CWbytearray(b'fe ca 3d f5 37 ae fc f7 05 16 fa 9f bb 62 7d 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13964844, ...,  0.00488281,
            0.06054688,  0.06347656]), textin=CWbytearray(b'11 63 55 23 ad 8c f4 c4 4c d1 db fc 92 bc c1 ea'), textout=CWbytearray(b'14 40 1a b3 37 93 78 91 dc c7 b0 61 bd 35 8d 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19824219, -0.14355469, ...,  0.00683594,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'd7 37 96 7c 3c da 13 73 b6 e9 d8 09 47 7e 2a 69'), textout=CWbytearray(b'65 d9 38 b3 9a f7 a8 72 49 4d 93 12 7c b7 5a 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19921875, -0.13964844, ...,  0.02441406,
            0.07421875,  0.07617188]), textin=CWbytearray(b'42 3a 74 85 1a a5 3a cc d0 c5 a1 8f 2e fd b0 46'), textout=CWbytearray(b'0d 64 30 3d 4c d3 27 66 2e fd 04 1e eb 07 4a f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19433594, -0.14453125, ...,  0.015625  ,
            0.06738281,  0.07128906]), textin=CWbytearray(b'd7 bd 0f 49 64 11 ed 47 a4 6e e3 e2 d0 7b c6 6f'), textout=CWbytearray(b'cc 15 b5 88 1b 91 d4 6e 6c 86 ab 83 14 57 24 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13671875, ...,  0.01367188,
            0.06738281,  0.06933594]), textin=CWbytearray(b'24 98 2c 31 2b 30 5e b1 96 09 2a 91 9e 84 b3 e8'), textout=CWbytearray(b'97 9a 66 a9 c4 1a 27 9f 8c bf 84 63 d9 5b e8 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13769531, ...,  0.0234375 ,
            0.07226562,  0.07519531]), textin=CWbytearray(b'b7 2d 54 a4 39 51 02 52 e9 9b 01 4a 68 eb 33 fa'), textout=CWbytearray(b'e5 6b 1e d4 42 8a 3b 6a 95 72 93 6b 7b 4d 08 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14355469, ...,  0.01660156,
            0.06542969,  0.07128906]), textin=CWbytearray(b'1b 9a a6 90 c1 16 30 6c c9 ad 77 f0 35 54 5b 8a'), textout=CWbytearray(b'39 3a 74 17 2c 26 53 93 2c 42 56 94 a0 e6 c2 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14453125, ...,  0.01953125,
            0.07128906,  0.07324219]), textin=CWbytearray(b'04 19 76 9f 91 0b 5c b8 22 be 85 cf 2c 47 39 16'), textout=CWbytearray(b'17 dd aa 6d 09 40 2a 0b bd 6e 05 77 81 6e d2 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19238281, -0.13769531, ...,  0.00390625,
            0.06152344,  0.06445312]), textin=CWbytearray(b'0a f4 71 b0 52 a6 b6 3a 59 b0 43 fe 5b 68 b1 db'), textout=CWbytearray(b'a6 e5 3b 07 29 cd 34 be cc 44 69 0d ec 89 a0 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1953125 , -0.13769531, ...,  0.00097656,
            0.05957031,  0.06347656]), textin=CWbytearray(b'68 13 28 d9 06 2b 6b ec 32 61 56 14 ed 80 6d bf'), textout=CWbytearray(b'2f e9 92 cf 8b 84 b7 28 6e 07 59 5a e9 64 b0 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13769531, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'31 72 20 11 85 d1 69 fc 1e 9f d5 3e 2d f3 a5 01'), textout=CWbytearray(b'a1 ae e5 eb 9a 10 db 25 ba a4 97 56 d5 1f 20 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19726562, -0.14257812, ...,  0.01953125,
            0.07128906,  0.07324219]), textin=CWbytearray(b'fd 21 3c a7 98 da 53 c7 fd ee fa 0b d0 3f af 0b'), textout=CWbytearray(b'38 27 4f 38 2b 8d 74 97 b5 d1 69 3d 74 eb ad d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.13964844, ...,  0.01953125,
            0.07128906,  0.07128906]), textin=CWbytearray(b'1c 7d 2f d9 00 b2 bc 0f c3 3b f5 cc 97 e3 ab dc'), textout=CWbytearray(b'c8 e6 b5 e7 d8 2f 7d dc 01 44 9d af 2c 07 00 cc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19238281, -0.14160156, ...,  0.01660156,
            0.06640625,  0.07128906]), textin=CWbytearray(b'7f a9 4a ed aa 4c 1c dc 5f 8c a0 2b cf 3a c2 68'), textout=CWbytearray(b'b2 54 06 c7 f9 64 6e 5a 37 50 71 93 97 da 03 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.13964844, ...,  0.00292969,
            0.06054688,  0.06347656]), textin=CWbytearray(b'06 be df aa 20 ea 4d ce 5b f9 cc c6 b3 d0 24 67'), textout=CWbytearray(b'ce 19 80 ba ab 3f dc 2c d6 0c 7f b8 e0 61 47 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.14160156, ...,  0.01757812,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'aa 57 2d 19 e4 c2 cc c8 df 39 ce d4 03 fb 10 23'), textout=CWbytearray(b'18 40 d1 cf 72 01 bb e2 62 ae 1d 8f 93 5c cc 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14257812, ...,  0.01855469,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'84 34 67 a1 e8 46 92 67 c7 4a b9 34 fa 74 b4 77'), textout=CWbytearray(b'9a 3a 1a 91 74 30 61 bd 96 9e 73 08 a2 54 19 a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13964844, ...,  0.01171875,
            0.06738281,  0.06835938]), textin=CWbytearray(b'5d 98 98 8d 6a 25 4a 6c fc 70 10 79 f7 2a cc fd'), textout=CWbytearray(b'36 c4 e9 bb 30 f0 ee 75 dc a3 d1 1a ea 93 f5 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19042969, -0.13769531, ...,  0.01660156,
            0.06835938,  0.07226562]), textin=CWbytearray(b'1c f8 2b ab 4a d2 38 55 57 76 8f 0a 78 29 53 b6'), textout=CWbytearray(b'9e 7a da 9e 63 94 70 2f e9 0c 56 01 6e 77 ce af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13769531, ...,  0.00292969,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'56 39 5b 26 30 54 cc 18 a7 77 4b 84 7b 9d dc b4'), textout=CWbytearray(b'7e bd f1 12 92 ae b5 f9 93 4a d1 be ec 80 c3 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.13964844, ...,  0.00488281,
            0.06054688,  0.06347656]), textin=CWbytearray(b'8c 51 a4 fd 85 a5 7c 27 b5 81 c9 7a 92 29 44 fa'), textout=CWbytearray(b'37 10 12 2c 27 15 8c bf b0 a5 48 6f c7 77 cf 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13769531, ...,  0.02148438,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'59 e2 bb 50 79 f4 cc 83 c0 4c 5d 67 e5 7e 5d da'), textout=CWbytearray(b'a3 34 89 44 44 d2 a5 88 19 94 68 91 8c 3f 94 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13964844, ...,  0.01074219,
            0.06445312,  0.06933594]), textin=CWbytearray(b'62 c3 57 42 46 b3 4a 02 ed 24 2a f5 25 bd 6a eb'), textout=CWbytearray(b'59 10 74 21 8b a9 8c 66 39 a2 79 ee d2 9b 4b 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20214844, -0.14355469, ...,  0.015625  ,
            0.06542969,  0.06835938]), textin=CWbytearray(b'c7 31 de 6e a1 c2 c3 e1 ff 53 1b 23 ba 0a 2a 4c'), textout=CWbytearray(b'98 a9 47 94 8d 31 47 c5 6a 46 a1 1e 67 12 ff 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.13964844, ...,  0.02050781,
            0.07519531,  0.07421875]), textin=CWbytearray(b'b2 69 de fe 2f 39 0f 2d 60 3c a8 aa 94 12 b9 68'), textout=CWbytearray(b'f1 e6 a8 8d 9c bc d5 f8 41 96 f3 2e dd 7f 07 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.14160156, ...,  0.015625  ,
            0.06835938,  0.06933594]), textin=CWbytearray(b'bd a0 8c e0 7f f3 e9 d8 09 d9 7e 51 67 09 cd 94'), textout=CWbytearray(b'22 c4 03 e7 e4 c5 4b ea 14 1d a4 74 f1 f1 c0 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19921875, -0.14648438, ...,  0.00976562,
            0.06445312,  0.06640625]), textin=CWbytearray(b'dc cc 2a 86 d0 8c 8d db e7 2c b1 b3 4f 12 45 4b'), textout=CWbytearray(b'5e b1 63 fb bb ee f2 5c fd 73 22 91 29 a8 82 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14160156, ...,  0.01269531,
            0.06640625,  0.06835938]), textin=CWbytearray(b'ce 1a b1 9f aa ab c7 5e 15 30 11 bf 85 ff 13 5d'), textout=CWbytearray(b'12 de db 21 3e 66 24 e3 f6 66 9e 03 02 f8 60 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14160156, ...,  0.00878906,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'ee bb 47 14 92 39 01 70 9e 73 df a5 a0 a2 2c 17'), textout=CWbytearray(b'a7 16 68 1d bc fc 9c ee e1 d6 6b ad bc 9d 6b 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13867188, ...,  0.01660156,
            0.0703125 ,  0.06933594]), textin=CWbytearray(b'f6 9d 0e dc ef 46 11 3e fb 32 0e 80 c6 16 f1 83'), textout=CWbytearray(b'3b f9 38 64 77 59 2e bc f8 f7 c8 32 08 82 fc 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14355469, ...,  0.01855469,
            0.06542969,  0.06933594]), textin=CWbytearray(b'86 ca bc 6b 33 33 a4 dd d5 aa 9b 60 21 91 34 05'), textout=CWbytearray(b'ad 28 bc 33 e3 fc 21 ee 6e 14 4e a6 12 8e 94 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14355469, ...,  0.01757812,
            0.06933594,  0.07226562]), textin=CWbytearray(b'4b 4a 34 cf 02 7e de 5a 71 dd 72 97 9b 7f 2a 4c'), textout=CWbytearray(b'3b d2 8c 87 8b 6d 22 96 91 e3 5b 23 39 f5 9f 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.13964844, ...,  0.00292969,
            0.06152344,  0.06347656]), textin=CWbytearray(b'69 8e 98 b4 3f 24 49 1f c1 22 02 ce 57 ce c4 42'), textout=CWbytearray(b'4d b0 7c 17 c2 fc f8 7e cc 4e 87 b1 ac cd 2f ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13964844, ...,  0.015625  ,
            0.06933594,  0.07128906]), textin=CWbytearray(b'bb 92 6c 09 db f3 f0 da ff e2 d0 ab a8 f4 a5 8b'), textout=CWbytearray(b'4e b2 fe b7 14 3f 3f 1d f0 5c 58 fe ee e0 4c 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.13769531, ...,  0.03125   ,
            0.08105469,  0.07910156]), textin=CWbytearray(b'58 dc 7b b5 b4 d4 13 6c f1 fd 63 25 cf 24 27 f4'), textout=CWbytearray(b'f0 cd d4 37 83 67 04 28 e5 57 95 7a 12 bf ba f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14257812, ...,  0.02246094,
            0.07519531,  0.07519531]), textin=CWbytearray(b'7c 31 fc bb 92 21 0a 52 6c a8 0a b5 4d 71 3f 03'), textout=CWbytearray(b'1b c5 db 59 b7 ab 48 02 fb 16 0a fc 8f 07 3f c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19140625, -0.13769531, ...,  0.01367188,
            0.06933594,  0.07128906]), textin=CWbytearray(b'a8 24 48 97 dd 6e 91 58 dd 84 71 32 c1 f7 12 df'), textout=CWbytearray(b'76 65 93 06 c7 8a 96 a2 9e 8a 63 c3 87 29 d1 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.13964844, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'5f f0 f0 7d 27 c0 cb 34 66 02 3a 0a 25 12 dd 67'), textout=CWbytearray(b'77 be 4c 39 d7 ac ea a1 84 4d 40 67 cf d5 f3 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14257812, ...,  0.01757812,
            0.07226562,  0.07226562]), textin=CWbytearray(b'b1 2b 7f 9e c8 61 eb 4d 30 6b 0e d5 c2 c3 9c 0b'), textout=CWbytearray(b'cb f0 a3 13 c6 76 00 33 e4 17 7c bb e7 08 8e ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1953125 , -0.14257812, ...,  0.00585938,
            0.05957031,  0.06347656]), textin=CWbytearray(b'b2 71 4d d2 1d 9a 78 96 0c bd a8 97 e3 ab 45 48'), textout=CWbytearray(b'88 d3 7e bd ed ec f5 21 c0 6a fb ba d0 49 73 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13769531, ...,  0.00488281,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'8c 61 6d 08 fb b1 27 05 83 f6 8a f0 79 ed ea df'), textout=CWbytearray(b'47 c0 48 26 0f 7f 42 72 53 27 af 6b c6 1c 1d e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13671875, ...,  0.00976562,
            0.06445312,  0.06933594]), textin=CWbytearray(b'f8 b9 c4 1a ce 7c ff e8 2b c2 7e 18 78 ae 52 d3'), textout=CWbytearray(b'ac 33 db d4 49 d9 e5 0d 86 21 2d b9 a8 a2 ed 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.19140625, -0.13574219, ...,  0.0078125 ,
            0.06347656,  0.06738281]), textin=CWbytearray(b'86 9b 37 b5 fc e8 c0 c2 a0 69 32 23 27 c0 eb d3'), textout=CWbytearray(b'ed 4a 35 ca 17 9f 61 d9 b3 35 a9 e0 6b 8b 66 ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14550781, ...,  0.01464844,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'94 ec 3d c3 7f 70 f6 55 d1 30 94 f9 09 83 3f 15'), textout=CWbytearray(b'83 62 08 4f be 09 dc 62 fd 14 0e d4 11 b9 be a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13769531, ...,  0.01269531,
            0.06445312,  0.06738281]), textin=CWbytearray(b'35 85 65 aa b6 27 eb 49 3a b0 41 d0 9b c8 8f a1'), textout=CWbytearray(b'ee 0e f0 1d fb 74 d5 5e 45 0f 77 55 15 cd f1 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19335938, -0.14550781, ...,  0.00878906,
            0.06640625,  0.06738281]), textin=CWbytearray(b'b5 4d e9 95 1a 03 aa ff 9f 03 07 47 57 22 81 65'), textout=CWbytearray(b'8b 80 59 c5 14 82 69 af b4 74 ea 60 9c 10 eb ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.13964844, ...,  0.00585938,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'26 c3 67 03 a6 ff c9 71 c0 02 27 b9 6c 75 21 b0'), textout=CWbytearray(b'6c 0d 64 20 88 03 e2 be fa b5 4f 34 dd b8 b6 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13867188, ...,  0.01269531,
            0.06542969,  0.06933594]), textin=CWbytearray(b'cf c9 26 a1 a4 36 8c 4f f7 6a cb 74 9d a8 08 ed'), textout=CWbytearray(b'26 33 72 d2 75 c3 e7 dd d4 42 d5 08 09 fd a4 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.140625  , ...,  0.015625  ,
            0.06835938,  0.07128906]), textin=CWbytearray(b'28 ee 70 c3 d7 80 86 c0 80 e8 50 74 03 f3 ea 70'), textout=CWbytearray(b'd7 80 4d f6 c3 b6 2c 6b c5 62 72 a1 2f 54 4a f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13769531, ...,  0.00976562,
            0.06445312,  0.06445312]), textin=CWbytearray(b'fc 57 bb 0a 96 cf 28 46 9a dd 0e bf ba f0 e9 ff'), textout=CWbytearray(b'df 6f a5 9a 67 30 80 d9 80 59 f5 10 12 77 9e 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14160156, ...,  0.        ,
            0.06054688,  0.06445312]), textin=CWbytearray(b'24 ac 34 67 4f 7a 06 9a e1 e0 41 51 c7 8e 3e 1e'), textout=CWbytearray(b'40 8f e9 17 37 83 b4 91 d8 75 e9 c0 2b b2 11 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19140625, -0.13671875, ...,  0.00390625,
            0.05761719,  0.06347656]), textin=CWbytearray(b'50 eb d6 bb 67 2e d8 c4 e5 54 65 38 27 b4 30 bc'), textout=CWbytearray(b'42 67 57 f6 7d 16 1a 70 2d 6b e7 d0 d8 e9 28 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.140625  , ...,  0.02050781,
            0.07128906,  0.07324219]), textin=CWbytearray(b'20 2d 27 8d 55 28 56 c2 d0 95 5d 06 e4 a5 77 96'), textout=CWbytearray(b'0e ea 3f 79 d7 6c a2 bd 4a ed 3c 16 86 fe ed 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ...,  0.01953125,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'5f 42 d6 5a e3 23 50 ba b6 9f bb 20 48 e1 fb 8b'), textout=CWbytearray(b'58 37 48 ed 71 c9 41 65 bd fa da d2 22 47 18 1a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19042969, -0.140625  , ...,  0.01269531,
            0.06445312,  0.06933594]), textin=CWbytearray(b'ed cd 90 81 96 9f 77 8d f0 e7 dc 69 4d dd a1 94'), textout=CWbytearray(b'77 66 05 29 ff 68 e3 5f 57 f6 bd 15 f0 73 c8 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14355469, ...,  0.0234375 ,
            0.07421875,  0.07324219]), textin=CWbytearray(b'f0 47 39 7b e7 bd e5 c0 36 04 7d ae f2 bc c2 79'), textout=CWbytearray(b'43 29 ee 9f f8 fe 92 6b 30 e5 03 46 a6 f3 f1 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14257812, ...,  0.01367188,
            0.06640625,  0.06933594]), textin=CWbytearray(b'89 92 9a bf ca 7d 7b 8d 3f 49 81 a0 3a c7 cb 7c'), textout=CWbytearray(b'bd 17 1e 2e 08 c9 36 7c 65 8a 48 5a 9a 27 bd d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14257812, ...,  0.01269531,
            0.06347656,  0.06542969]), textin=CWbytearray(b'14 7c 40 d8 53 57 ce c8 f4 4d 0e 2c 55 f7 39 3e'), textout=CWbytearray(b'd9 85 60 ec ee f7 72 00 d8 cf fa 2f 26 95 9b bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19824219, -0.14550781, ...,  0.00292969,
            0.06054688,  0.06347656]), textin=CWbytearray(b'e1 4a b8 92 fa 16 9e 24 fd 69 52 b7 29 04 3a 98'), textout=CWbytearray(b'07 51 ff 44 b7 6a 41 3a 64 22 ed 0b ce aa ae f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.13867188, ...,  0.01855469,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'77 1d 58 75 ae 44 6f 4c a2 f2 86 b8 87 a4 e9 28'), textout=CWbytearray(b'15 8b d1 84 cf 2e c5 37 42 bd f0 1c bc 83 c0 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13867188, ...,  0.00878906,
            0.06152344,  0.06835938]), textin=CWbytearray(b'db 8d 71 31 ab 86 33 71 a4 19 35 18 c8 98 0e de'), textout=CWbytearray(b'be 9a 5d 27 02 f1 37 a5 67 07 68 a6 75 4b e0 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13769531, ...,  0.01660156,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'ed db 82 e3 8f c0 49 c5 ea d8 7e c3 7e 0c ca c7'), textout=CWbytearray(b'2d c1 96 d3 85 7b 37 68 32 21 93 b7 c1 a4 e5 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20117188, -0.14453125, ...,  0.00878906,
            0.06054688,  0.06640625]), textin=CWbytearray(b'03 d4 a3 02 e6 54 77 8f 5a dc 58 e1 f7 80 8c 5e'), textout=CWbytearray(b'67 ce d9 9f f2 5b 1d c4 e5 c6 8b 73 70 b2 f1 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19042969, -0.13476562, ...,  0.0078125 ,
            0.06152344,  0.06445312]), textin=CWbytearray(b'c6 39 cf 4f 2e 9d 06 81 8d c3 e9 60 00 e8 e0 f4'), textout=CWbytearray(b'60 05 5c ee 69 3d 28 4e f0 df dd b7 97 99 50 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19238281, -0.13671875, ...,  0.01855469,
            0.07128906,  0.07128906]), textin=CWbytearray(b'81 51 5c cd 3d 9c 74 f1 ff 1e a8 20 75 a5 ae e6'), textout=CWbytearray(b'33 5c bc 58 35 78 c0 d5 98 25 2b 32 4a db b6 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.14648438, ...,  0.02441406,
            0.07226562,  0.07421875]), textin=CWbytearray(b'5a 41 68 d5 67 fc 4f 22 b8 91 10 91 4a 99 72 1b'), textout=CWbytearray(b'6c d8 11 f7 37 f6 b8 ef da b6 91 d5 da ec 08 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19824219, -0.1484375 , ...,  0.01660156,
            0.06542969,  0.07128906]), textin=CWbytearray(b'05 b6 27 27 d8 21 f5 f4 05 6f 0c 2e 00 03 62 2f'), textout=CWbytearray(b'ea 10 0d 59 de 74 43 e8 80 90 53 11 ff f1 06 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19921875, -0.14257812, ...,  0.02246094,
            0.07226562,  0.07617188]), textin=CWbytearray(b'a2 08 13 ad 3c 9c b8 9f b4 18 db f4 1f bf 6a 57'), textout=CWbytearray(b'52 fc 2d de 1d 14 d7 a3 59 4c 18 1d 6c d2 2c e5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19433594, -0.13867188, ...,  0.03027344,
            0.07910156,  0.07910156]), textin=CWbytearray(b'5f c1 5d 96 ac 0c fd 64 d0 0c 7e 69 eb 64 44 d4'), textout=CWbytearray(b'99 a7 12 be 0f cb af e5 4d 0f 10 da 4c fd ef 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19726562, -0.14355469, ...,  0.01953125,
            0.07226562,  0.07226562]), textin=CWbytearray(b'9f 69 a3 d1 ac ca 7c 2b 5e b9 4a e2 7b 8a 22 6a'), textout=CWbytearray(b'b0 73 44 d9 e5 47 84 a1 e9 8f f7 89 65 0b 82 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13574219, ...,  0.01269531,
            0.06445312,  0.06933594]), textin=CWbytearray(b'3e d1 21 ab 48 46 b9 a0 9f f8 07 50 49 06 fa d1'), textout=CWbytearray(b'67 e1 65 3f fa e3 b6 1a 64 12 3f 2b a8 15 3f ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14160156, ...,  0.01855469,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'57 bb 92 a7 41 b6 7e d0 6e b7 de 57 ad 10 e6 0c'), textout=CWbytearray(b'e0 5d fd 1f f2 6d 26 5f 5f 1f a7 3b 06 7d c4 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.13964844, ...,  0.015625  ,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'84 da 1c 31 35 a0 07 32 3d 2b 3c f5 1c bf 15 52'), textout=CWbytearray(b'bf 06 31 4c d6 70 ea e6 b5 28 3c ed 6e f2 46 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19335938, -0.13476562, ...,  0.00585938,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'f0 d2 8e 96 57 10 99 c9 e5 47 96 c8 f8 f8 69 c9'), textout=CWbytearray(b'1a 49 5f c1 2f 84 54 ca 97 cd d2 64 a4 14 ae d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.140625  , ...,  0.01269531,
            0.06542969,  0.06933594]), textin=CWbytearray(b'c1 df fc 5a 51 c0 d9 ae fe 33 f9 bd 30 c1 ae 34'), textout=CWbytearray(b'af 96 de 45 1e 35 fa 14 bb 2e 77 71 01 d7 cf f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14550781, ...,  0.01660156,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'b7 f6 69 f3 35 26 e8 cf 7a ab 27 31 f6 bf 03 9b'), textout=CWbytearray(b'24 be be 4c c5 70 67 9d 54 2b e9 12 6e 54 50 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19726562, -0.14648438, ...,  0.01953125,
            0.06933594,  0.07128906]), textin=CWbytearray(b'6c 77 ff 7f 97 e8 0b 17 9f 3a 3f 1a 3b 12 73 94'), textout=CWbytearray(b'8a b9 58 1d 1c 3b 1c 29 9c 2e 32 8b 57 ed db ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19238281, -0.13378906, ...,  0.00976562,
            0.06640625,  0.06835938]), textin=CWbytearray(b'2f 3e 52 4a 1b a9 70 74 86 97 14 d4 4f 75 f9 e3'), textout=CWbytearray(b'd7 47 a3 32 d2 be 51 5e af 64 a2 13 0b 60 bd 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13964844, ...,  0.01074219,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'dd 9a 82 c1 d8 13 90 13 0c 46 e6 42 a5 4e f1 db'), textout=CWbytearray(b'bd aa 16 84 33 c2 5a 83 b2 20 c3 b3 d8 f7 15 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19921875, -0.14355469, ...,  0.0078125 ,
            0.06054688,  0.06640625]), textin=CWbytearray(b'34 07 05 8d bd 74 e8 b0 a9 35 66 16 c6 6c 03 6b'), textout=CWbytearray(b'ac 2c 04 55 f6 a0 89 dd 65 db e0 1a 18 39 34 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20117188, -0.14355469, ...,  0.01269531,
            0.06542969,  0.06738281]), textin=CWbytearray(b'ff 05 3c 4b ae 86 1d f3 28 f2 87 d7 33 1a 1b 7f'), textout=CWbytearray(b'6a 5c d6 6b 70 00 70 2d 1e c1 9c 72 e3 28 f6 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13867188, ..., -0.00488281,
            0.0546875 ,  0.05957031]), textin=CWbytearray(b'4c f3 39 dc 8d 69 24 e0 ec 2d e9 de 64 a2 20 77'), textout=CWbytearray(b'a5 e2 5a 3d 3f e3 86 67 54 eb 12 bd f9 eb 1f 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13769531, ...,  0.00683594,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'7e 52 12 ed 09 49 22 4c 62 d5 67 4d fb f9 70 f5'), textout=CWbytearray(b'7f 16 eb 2b 93 d7 a3 f9 ff b0 02 ad 55 91 2d eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19042969, -0.13671875, ..., -0.00488281,
            0.05371094,  0.05761719]), textin=CWbytearray(b'79 2e 54 69 48 b2 4b b9 b7 f7 07 f1 da b7 18 f9'), textout=CWbytearray(b'11 28 20 7e 0d 83 8a c8 c8 24 b2 6f 0c ce e7 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20019531, -0.14257812, ...,  0.00195312,
            0.05957031,  0.06347656]), textin=CWbytearray(b'ad 57 0e a3 13 50 6b 9e 0c d3 27 eb dd 0a 9f 72'), textout=CWbytearray(b'99 24 d5 83 24 1f 5a 7d d5 3a 44 df 44 1d b3 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13671875, ...,  0.00292969,
            0.05957031,  0.06347656]), textin=CWbytearray(b'c1 dc fb 9a 5f bb e2 9e 43 98 7c 34 74 65 fe b6'), textout=CWbytearray(b'cb 59 92 d1 03 ab 20 79 9b d4 1f 73 23 92 db 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13769531, ...,  0.01757812,
            0.06933594,  0.07226562]), textin=CWbytearray(b'37 93 19 97 60 74 e3 2f c3 6c fb aa 37 2e 3e c7'), textout=CWbytearray(b'e8 d5 9f 47 1a 6e cf f2 f6 ca dd 94 f6 2c 5f 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13769531, ...,  0.01855469,
            0.06933594,  0.07421875]), textin=CWbytearray(b'f3 25 07 8f 13 06 e8 5d 14 d2 a6 a4 72 e2 b3 b4'), textout=CWbytearray(b'95 60 98 72 5c 04 be 19 05 82 da 4a c6 e5 58 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20019531, -0.14453125, ...,  0.0078125 ,
            0.06152344,  0.06445312]), textin=CWbytearray(b'ab 06 54 fb a4 21 bd be db a8 b5 2b 74 2a 25 42'), textout=CWbytearray(b'cf d1 06 07 3e 9d ae 73 a9 70 bd 24 5f 63 80 f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19433594, -0.13671875, ...,  0.02636719,
            0.07421875,  0.07617188]), textin=CWbytearray(b'68 7b a3 10 1c ff 3f bd 01 16 83 80 a5 02 6e c6'), textout=CWbytearray(b'a9 2c 38 a2 c8 a4 7d 7f 59 a8 45 5d 2e 0b 65 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18847656, -0.13867188, ...,  0.015625  ,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'56 ff 1b 7d 30 73 26 77 46 49 51 59 43 7e fc f0'), textout=CWbytearray(b'c4 85 95 b9 6a 22 07 9b 2d 5b 5e bb ee b5 ce 51'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.20214844, -0.14648438, ...,  0.03417969,
            0.08007812,  0.08300781]), textin=CWbytearray(b'7c ed 0b d1 74 d9 ae 67 0c 8e 12 f4 08 2c 55 9f'), textout=CWbytearray(b'8e d7 eb 41 2b 70 44 41 cf 66 fd af 9f 08 91 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20214844, -0.14355469, ...,  0.02636719,
            0.07617188,  0.07714844]), textin=CWbytearray(b'1d 6c c8 43 76 b5 1f b7 df 22 03 f2 ef 4e 1b 4a'), textout=CWbytearray(b'2d d7 c3 02 d3 ac bf 5e bf 4e 9e 4b 85 07 24 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.13964844, ...,  0.01757812,
            0.06933594,  0.07128906]), textin=CWbytearray(b'57 61 ba c3 c3 0d 2a 86 70 6e 5a ec 75 67 cb bd'), textout=CWbytearray(b'a8 e1 f8 20 7f 42 67 1b 31 2e 5f 31 7b 23 3e c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.140625  , ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'58 cd 3c 1e 38 02 5e fb 5e 71 fd d6 e8 4f dd bb'), textout=CWbytearray(b'30 c1 32 5e 88 c8 19 cf 1a b3 ca 53 41 77 1e f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19140625, -0.14355469, ...,  0.00683594,
            0.06054688,  0.06445312]), textin=CWbytearray(b'10 4e 52 cf 1f 88 4c 96 ce e2 8b a8 dc e5 94 38'), textout=CWbytearray(b'03 d7 98 db ca 76 a0 cf d6 21 ee e6 18 16 4e 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14257812, ...,  0.02636719,
            0.07421875,  0.07617188]), textin=CWbytearray(b'd0 59 d7 16 4d 7f b9 5d 6c 59 e5 27 98 7c 74 9d'), textout=CWbytearray(b'59 a1 37 6d d2 12 48 cb 1a e8 f0 25 51 0b 08 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.140625  , ...,  0.02148438,
            0.07128906,  0.07226562]), textin=CWbytearray(b'b6 21 5b 27 92 12 ec 22 20 3c 4a e1 97 21 f4 4e'), textout=CWbytearray(b'65 88 1b 17 79 7f 52 c6 b2 b7 4c f9 8f e8 07 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20019531, -0.14355469, ...,  0.00097656,
            0.05664062,  0.06152344]), textin=CWbytearray(b'26 98 ad 8d f0 f2 66 5e 5a 61 98 cf 38 2c 31 90'), textout=CWbytearray(b'01 f7 37 b8 f9 d4 e0 15 99 4d 8e 52 a5 7c 2f f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13769531, ...,  0.00878906,
            0.06152344,  0.06738281]), textin=CWbytearray(b'96 9f 57 58 e1 53 d6 16 8c 7c e4 c0 04 b5 a2 bd'), textout=CWbytearray(b'17 6d e4 fb f1 0c b5 a2 8e ef fd cc fc 27 af 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19335938, -0.140625  , ...,  0.02148438,
            0.06933594,  0.07421875]), textin=CWbytearray(b'dd 57 9f 1d 65 09 eb 38 0e c7 e0 d8 d7 55 65 60'), textout=CWbytearray(b'99 5f 0b b9 be 7c d0 45 0d aa 13 21 b5 be 5f a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.00390625,
            0.05761719,  0.06445312]), textin=CWbytearray(b'88 05 a7 92 38 8b a5 2d 4c b5 7c 96 ae 7b 90 64'), textout=CWbytearray(b'33 c4 60 50 7f f6 d5 2f 69 9e 0f 86 4b db 61 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13867188, ...,  0.01855469,
            0.06933594,  0.07128906]), textin=CWbytearray(b'3f f0 79 0b ea e0 df 1a 50 04 b0 3c a4 94 70 db'), textout=CWbytearray(b'54 de 53 66 ef 8d 44 2d 13 77 c9 7b 3c 64 13 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14648438, ...,  0.01464844,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'05 c1 1a 47 5e 96 8f 07 cd af 7f 10 cb e9 52 3d'), textout=CWbytearray(b'12 0b 34 b4 97 4a d5 1d 59 a6 a3 96 61 2e 37 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19921875, -0.14160156, ...,  0.        ,
            0.05761719,  0.06152344]), textin=CWbytearray(b'd8 83 3e f2 52 b4 96 bc bf db 17 bb 12 06 ea 81'), textout=CWbytearray(b'b8 13 49 67 00 c2 35 c6 32 26 8b 11 23 de b5 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13671875, ...,  0.01367188,
            0.06542969,  0.06933594]), textin=CWbytearray(b'80 40 5d 71 85 18 ef 44 fc ef c1 f5 c5 58 b6 85'), textout=CWbytearray(b'a0 43 6a c1 92 a0 59 e0 ad e3 07 a4 18 8a de 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14257812, ...,  0.02050781,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'b3 14 cb 20 8c 45 25 83 b0 47 78 06 64 d3 05 63'), textout=CWbytearray(b'00 f2 02 a2 35 64 fe 0b 97 43 ab 4d 8c 99 c2 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13964844, ...,  0.02734375,
            0.07421875,  0.07714844]), textin=CWbytearray(b'6a 50 f9 fe 15 4f 15 6e 52 fa bb 0d 33 bf 9e be'), textout=CWbytearray(b'7b e0 04 65 08 32 d8 a9 7d 3d 2e 1c db fb 36 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13671875, ...,  0.02832031,
            0.07714844,  0.07617188]), textin=CWbytearray(b'71 8a 21 b5 af af 90 98 e1 07 51 18 04 c1 b3 ff'), textout=CWbytearray(b'5b 19 3f 74 0e 94 4a be b3 59 3e 67 da f9 67 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14257812, ...,  0.00195312,
            0.06152344,  0.06445312]), textin=CWbytearray(b'0f 5f 0f da d1 e8 62 fd 5d a3 57 e4 b0 60 6e 4a'), textout=CWbytearray(b'eb 70 a2 b6 8d 6f 35 08 30 65 d0 98 30 c5 6c 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19140625, -0.13671875, ...,  0.01953125,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'1f ba ac e0 65 f5 84 08 01 1a f7 cd 4d a5 08 be'), textout=CWbytearray(b'4c 77 7a bd ab 8d 90 aa 83 90 89 c5 2d ce 05 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19921875, -0.14160156, ...,  0.00878906,
            0.06347656,  0.06738281]), textin=CWbytearray(b'f8 8e c8 07 07 bf a1 7c 5c 5e ab 4d d0 45 25 94'), textout=CWbytearray(b'da 3b 11 ae 97 69 24 97 53 ad f3 d7 4a 4f 9d d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14257812, ...,  0.01464844,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'67 62 72 19 ae 53 49 77 c6 2e 12 d0 96 1c 4e 4d'), textout=CWbytearray(b'd0 52 1e f2 12 01 20 02 0f c8 7c 6f 79 03 52 a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.140625  , ...,  0.01269531,
            0.06445312,  0.06835938]), textin=CWbytearray(b'9b c4 9b 28 9c 33 cc df 4d 0b d1 7f 74 0d e3 fc'), textout=CWbytearray(b'4d 22 2f 9d 9d 95 db 96 cf fe 70 50 0f ea df 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19238281, -0.13867188, ..., -0.00292969,
            0.05273438,  0.06054688]), textin=CWbytearray(b'a7 17 75 2c f7 2e e0 d9 5a a1 23 39 71 09 ed d9'), textout=CWbytearray(b'2c b2 49 a0 22 bd b0 e3 d8 a7 f6 87 06 17 28 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13574219, ...,  0.015625  ,
            0.06738281,  0.06933594]), textin=CWbytearray(b'ea 43 50 0d 74 dc 85 e5 aa fb 2b bc 1c c7 fc ca'), textout=CWbytearray(b'7d f0 0a 7c 43 b3 ad 8d fb 8a 61 5f c3 96 4a 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19921875, -0.14550781, ...,  0.03027344,
            0.07714844,  0.07714844]), textin=CWbytearray(b'70 d6 4f d3 da c7 18 b7 84 cd 10 bf 65 bd 1e 0b'), textout=CWbytearray(b'02 d4 c7 c4 1d 50 9f 8c 64 0b 6b ee 16 bb 76 bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1953125 , -0.14355469, ...,  0.01269531,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'fd e3 4f 71 ba 42 b4 de bb 50 47 e6 dc be 9a 4b'), textout=CWbytearray(b'27 f6 bf 1a 63 69 56 4b b0 4b 65 3d 9d d3 9c 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14453125, ...,  0.01757812,
            0.06835938,  0.07226562]), textin=CWbytearray(b'45 0f 82 8b 43 dd 48 68 84 cf 54 12 bd 16 61 0c'), textout=CWbytearray(b'bb af b8 72 0f ef ba ae e0 73 2e 9a 21 8c 47 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14453125, ...,  0.01171875,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'63 da 12 26 6a 99 22 d5 64 81 b0 c4 ec 76 7b 92'), textout=CWbytearray(b'3a af 2e 6b bc 6e 2b f5 bb c8 61 71 67 d5 3e 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.20019531, -0.14355469, ..., -0.00488281,
            0.05175781,  0.05957031]), textin=CWbytearray(b'30 01 cf c9 4d f8 26 32 f8 9e 2e fb 42 85 24 6e'), textout=CWbytearray(b'2b a4 e0 7e f9 ef 1e 3b f8 e2 66 0c 1e 94 40 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19335938, -0.13671875, ...,  0.01074219,
            0.06445312,  0.06835938]), textin=CWbytearray(b'e7 ab 31 f3 1b a2 39 27 22 8e d7 34 3f 16 d4 dd'), textout=CWbytearray(b'e0 8e 8f 19 51 5f 8e a2 99 6a 67 5a 31 b4 75 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14160156, ...,  0.02050781,
            0.07226562,  0.07226562]), textin=CWbytearray(b'38 08 5b c0 55 9e 5e c4 60 d5 c1 6c b4 ef 84 89'), textout=CWbytearray(b'd1 93 2e 3f 23 5d 67 55 dc 45 79 71 e1 2e 7c 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.14160156, ...,  0.01269531,
            0.06640625,  0.06738281]), textin=CWbytearray(b'09 ce 16 7b 5d 55 01 52 55 a8 0e a3 af c3 15 10'), textout=CWbytearray(b'aa 05 d7 06 18 7f 74 d0 ed a3 ff 2a e0 65 29 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19824219, -0.14550781, ...,  0.015625  ,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'b4 9e e0 3e 26 a8 d0 57 0b 1a d2 74 3d b2 0b 8c'), textout=CWbytearray(b'7a 92 da 30 40 ec 85 5c 88 fa c7 24 8e c0 41 c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19824219, -0.13769531, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'f1 a4 ef 45 61 fd 37 cc e4 20 78 79 01 71 f9 cf'), textout=CWbytearray(b'c9 64 29 30 d7 4f 7a 23 b7 10 85 5a 86 41 c5 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.00488281,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'39 44 ce 87 38 86 44 de 09 c5 31 b4 43 e9 ae 91'), textout=CWbytearray(b'7f b3 69 87 1f 60 71 f2 43 3c cc 85 60 bc 5b c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14257812, ...,  0.0078125 ,
            0.06445312,  0.06445312]), textin=CWbytearray(b'85 54 63 c8 71 3d b3 03 ed bc b1 9f 1a af 8f 50'), textout=CWbytearray(b'8f 86 47 2c d2 85 ca 5b 1d 68 88 ae 00 65 c7 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14257812, ...,  0.01367188,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'04 58 7a bd 44 32 49 9c 49 e9 f7 cd ba 4e 16 21'), textout=CWbytearray(b'92 75 8a 1a fd 01 97 df 9e cf 42 7f 16 ca a6 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19140625, -0.13867188, ...,  0.02636719,
            0.07421875,  0.07714844]), textin=CWbytearray(b'91 5e d4 52 58 30 5a b9 36 08 f1 b0 01 e9 62 07'), textout=CWbytearray(b'45 6c f8 20 df c8 a8 e5 ed ff c4 bf 3d 4b 9b 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14160156, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'bc dc 88 6b b8 8a 5b 6f bd 39 d8 48 03 fc 5f 32'), textout=CWbytearray(b'90 ab f2 30 a9 13 23 e1 54 bc e0 7e 85 f2 97 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.140625  , ...,  0.00097656,
            0.05859375,  0.06347656]), textin=CWbytearray(b'42 21 b1 8f d2 cb 00 a9 c1 4e ce 2b fa a7 cd 0a'), textout=CWbytearray(b'47 e3 3f 4b 3b 4f 16 2c 30 7e 4e 31 6b 55 a7 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.20117188, -0.14453125, ..., -0.00195312,
            0.05371094,  0.06054688]), textin=CWbytearray(b'4d cd 26 ea 5c d9 f4 b3 16 b8 64 23 a6 6f 25 43'), textout=CWbytearray(b'eb 29 71 13 6e 3d e3 ad 32 e3 74 12 1a da 21 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13769531, ...,  0.02636719,
            0.07421875,  0.07714844]), textin=CWbytearray(b'aa 61 d3 bd 39 70 4c 2f 8d eb 15 33 28 bc 7b b4'), textout=CWbytearray(b'85 58 a9 57 94 1b d2 d4 2e b6 0b 43 76 63 c7 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19238281, -0.1328125 , ...,  0.01367188,
            0.06835938,  0.06933594]), textin=CWbytearray(b'ca 9c 8c a8 3b 69 26 ae 9e 96 ce d4 90 64 a9 f7'), textout=CWbytearray(b'22 d9 4c f0 50 e2 18 87 02 20 de 00 b5 31 c4 d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18945312, -0.14160156, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'0d cf db 85 59 ef 4e ea 9e 54 22 a5 95 8b f4 ac'), textout=CWbytearray(b'1a 7d 67 3e dc 10 65 4c cd 00 71 77 00 ed 26 f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19042969, -0.13769531, ...,  0.00585938,
            0.06054688,  0.06445312]), textin=CWbytearray(b'a5 ec 65 f2 3d 4c 82 83 3c 27 52 39 cc 84 b0 f3'), textout=CWbytearray(b'b2 7e ef df e1 51 2a de be bd 9f 09 b0 0b e4 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.02050781,
            0.06933594,  0.07421875]), textin=CWbytearray(b'ba 00 df 14 c7 cf d5 b7 d1 d6 1e d2 9e 6f 90 06'), textout=CWbytearray(b'74 de 8d b2 9c c3 a0 a0 71 77 84 10 c1 03 38 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.140625  , ...,  0.00292969,
            0.05566406,  0.06347656]), textin=CWbytearray(b'ab 97 21 f8 8a e5 1b 51 5b c0 3b 8b 6f 65 ea 16'), textout=CWbytearray(b'db 97 18 44 2e 1b 37 48 64 ad ac 93 79 de a1 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.140625  , ...,  0.        ,
            0.05566406,  0.0625    ]), textin=CWbytearray(b'c0 17 de 93 2d bd ae a3 04 49 95 0a 90 33 59 b8'), textout=CWbytearray(b'22 d9 89 6e d6 d0 da c4 20 4e b5 4d 28 73 de a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14355469, ...,  0.02148438,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'4c d3 df 7e fa f9 81 4d 28 39 1e 43 ba 5f 54 05'), textout=CWbytearray(b'fe f6 75 bb a0 c1 f3 67 2d 2b d8 8d 30 47 74 fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.13671875, ...,  0.00488281,
            0.06152344,  0.06347656]), textin=CWbytearray(b'84 6c a7 8d 97 72 34 9a 55 36 1a f6 a1 88 17 55'), textout=CWbytearray(b'15 1f fb 42 58 80 ec 7f c7 5b 4a 3f 6a d9 12 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13964844, ...,  0.01464844,
            0.06738281,  0.07128906]), textin=CWbytearray(b'0d a4 cd 51 b1 94 ce e4 93 c1 89 60 58 3a 0c e7'), textout=CWbytearray(b'd1 a1 f0 10 b7 c4 1d 63 62 61 96 d4 81 4b 4f c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.00976562,
            0.06542969,  0.06835938]), textin=CWbytearray(b'df dd a5 b0 2a 18 4c c4 28 27 14 95 d4 53 50 6b'), textout=CWbytearray(b'23 2c 93 11 5e 2c 89 36 a5 61 f3 7d c9 54 96 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19140625, -0.13769531, ...,  0.01953125,
            0.07128906,  0.07226562]), textin=CWbytearray(b'bc 59 c6 94 c9 f6 67 3e 10 e8 d9 a2 2c 3b 35 d7'), textout=CWbytearray(b'e5 2f b6 6e e2 f7 bb 74 b5 57 47 d3 18 8b ec 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14355469, ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'1d 6f 11 90 1d 27 95 b9 44 d6 1f 69 41 74 f9 8c'), textout=CWbytearray(b'22 0c bc 28 09 ea f6 2e 56 8c c5 29 29 56 50 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14160156, ...,  0.01367188,
            0.06835938,  0.07128906]), textin=CWbytearray(b'23 ad 90 f4 93 39 16 0c ec 71 7a 64 4a 60 b6 60'), textout=CWbytearray(b'8f c3 33 07 6a 8f c0 06 93 4e dd 4c ee b7 58 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1953125 , -0.14453125, ...,  0.01757812,
            0.06933594,  0.07128906]), textin=CWbytearray(b'b5 5e 0f e2 86 ce da 57 b4 d5 f2 99 ad 35 91 4a'), textout=CWbytearray(b'09 61 8f 35 69 7a 05 5f 12 12 a4 0b 2a a3 a6 d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14355469, ...,  0.01367188,
            0.06835938,  0.07128906]), textin=CWbytearray(b'c9 37 3f f7 90 18 3f fc cd c1 d9 57 9e 8f 94 9c'), textout=CWbytearray(b'ef be 28 0b 9a 53 16 92 23 00 54 24 ab 1a 6b f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14355469, ...,  0.01855469,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'f1 95 a2 ed a4 48 f0 8f b4 56 39 3c e0 00 60 69'), textout=CWbytearray(b'0e 09 d5 59 cb 77 57 51 8f 04 bb 95 7a 58 be c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19238281, -0.14257812, ...,  0.01464844,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'4b 8a a7 b6 42 9e f2 17 bd 57 3d a3 d5 3c fe 55'), textout=CWbytearray(b'55 51 42 69 a7 24 4a f2 e2 72 08 ff 45 e1 f4 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1953125 , -0.14355469, ...,  0.01464844,
            0.06542969,  0.06933594]), textin=CWbytearray(b'9e 1f fd 0b 14 1b 2e 13 91 4c de 19 93 99 13 52'), textout=CWbytearray(b'bc 7e 41 f1 ac 80 96 0c 77 32 d7 29 f6 88 d8 ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13867188, ...,  0.02539062,
            0.07421875,  0.07617188]), textin=CWbytearray(b'8e 49 5e ea 2d 43 00 18 4e b9 ba 8b 09 d4 c2 a5'), textout=CWbytearray(b'78 7b 7b 86 30 10 7f b4 7d b8 cf f4 3a fd 0d d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19140625, -0.140625  , ...,  0.01074219,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'bf 5f 4e 25 98 35 cc 19 0f 4d ea 67 d8 c2 71 f1'), textout=CWbytearray(b'd0 1f c2 4d d7 9e 0e 89 bb b2 40 cb 7f 49 a5 bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13769531, ...,  0.015625  ,
            0.06738281,  0.07128906]), textin=CWbytearray(b'f1 9c 21 f3 a5 32 54 da 0e 57 5a ab 14 93 87 f4'), textout=CWbytearray(b'87 6a d5 ba 4c 2c a3 7f 4d 24 5c 2c 0a 27 ed cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19140625, -0.13769531, ...,  0.01464844,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'c6 33 9e 90 ee a5 0d 0f 64 b3 92 44 a7 2b f3 cd'), textout=CWbytearray(b'3c 30 d0 1a 7b bb 3c 33 85 ff 34 09 67 6b fd 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18945312, -0.13867188, ...,  0.01367188,
            0.06738281,  0.06835938]), textin=CWbytearray(b'f7 c0 82 98 92 b4 9a 51 a8 e0 08 91 6e 20 ce e5'), textout=CWbytearray(b'24 f0 eb 0d e3 e1 a1 41 ab ee 1c 14 42 5b 8e 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14257812, ...,  0.00390625,
            0.05957031,  0.06445312]), textin=CWbytearray(b'31 e1 fa 7e e6 15 67 e0 1d c8 f0 77 1e 23 3d 23'), textout=CWbytearray(b'd9 0d ba 9a 40 ce 1e 4a ca 10 11 0c f4 8e f8 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13964844, ...,  0.01660156,
            0.06640625,  0.07128906]), textin=CWbytearray(b'45 ea 9c ab 7d 84 0d a1 8d ff de 46 b1 af 83 e0'), textout=CWbytearray(b'38 7e d9 61 03 b3 19 a6 88 6f 81 01 61 f4 a8 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13476562, ...,  0.00683594,
            0.06054688,  0.06640625]), textin=CWbytearray(b'b9 80 9b fe 0b 5d b0 b2 96 99 88 de 0d 01 f1 e9'), textout=CWbytearray(b'be 53 d7 94 03 31 d2 bf ad d6 d4 13 21 e3 3e d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14453125, ...,  0.02050781,
            0.06933594,  0.07226562]), textin=CWbytearray(b'76 c6 06 2b 90 8e 9f ba 02 25 88 90 82 1f 50 36'), textout=CWbytearray(b'05 96 cb c9 6b 3b 43 02 41 4d 8f 03 22 e0 ed 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ...,  0.00195312,
            0.05859375,  0.06347656]), textin=CWbytearray(b'71 07 06 ef be c2 a2 a9 39 a0 cb b6 2f c1 3c f3'), textout=CWbytearray(b'd9 a7 8c f0 8c 80 bb 0d 34 e9 e5 47 df 99 1d 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19628906, -0.13964844, ...,  0.00878906,
            0.06347656,  0.06738281]), textin=CWbytearray(b'7c 45 72 2b 84 06 77 a9 80 bf 87 09 20 7f 2b c2'), textout=CWbytearray(b'd6 13 e0 49 98 12 c4 d3 93 c7 aa 1c 64 83 94 ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1953125 , -0.14160156, ...,  0.01269531,
            0.06738281,  0.06933594]), textin=CWbytearray(b'65 34 ba 64 8c 8f 73 96 39 92 12 aa d1 84 0a 84'), textout=CWbytearray(b'41 cd 22 8a eb de a4 53 29 16 76 6c d3 41 6e 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14160156, ...,  0.02050781,
            0.07128906,  0.07421875]), textin=CWbytearray(b'43 44 4e e7 1a e6 b7 5b 24 54 f3 f4 a3 9f e3 12'), textout=CWbytearray(b'5c a2 53 f3 7a f9 56 e2 bb 45 31 52 d5 33 53 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13769531, ...,  0.00878906,
            0.06347656,  0.06738281]), textin=CWbytearray(b'a6 35 10 1b 3a 6d 15 1b b3 7b 29 2d 82 3d be c4'), textout=CWbytearray(b'b8 eb 9b ca c2 68 69 18 e5 6e 61 99 2a b9 0e 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14257812, ...,  0.01464844,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'be 4a e0 33 c9 e3 ac 92 82 92 7d 51 89 ac 8b a8'), textout=CWbytearray(b'10 f2 9f 70 7e 1a a9 0f fc 42 ef 51 55 69 94 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13769531, ...,  0.0078125 ,
            0.05957031,  0.06445312]), textin=CWbytearray(b'd2 be 4b 1e 8c bd 0c 6a bd e1 89 a8 c3 94 b8 bb'), textout=CWbytearray(b'27 b7 9e 93 12 77 ae 76 e5 92 ff b1 3d 9e 96 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14257812, ...,  0.00488281,
            0.06152344,  0.06542969]), textin=CWbytearray(b'1e 8c 23 82 c5 cd 88 7b 1f 67 c9 90 5a 47 79 09'), textout=CWbytearray(b'69 0e f1 cb fa b6 38 fe 99 0d 72 5a 85 02 db c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13769531, ...,  0.01171875,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'44 0e 35 92 f6 6e 2b 1c 96 97 2e c0 4d 8c 87 d9'), textout=CWbytearray(b'a7 34 53 c8 29 90 bd 4d dc 71 dd 5a 29 4c 63 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.20019531, -0.14355469, ...,  0.01464844,
            0.06835938,  0.06835938]), textin=CWbytearray(b'89 9e 3e a9 da 3a 45 96 0e 18 98 59 71 71 0e 85'), textout=CWbytearray(b'fb 25 49 c2 36 a8 02 40 f7 52 82 b1 25 a5 fe fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'05 4b 7f e9 c9 c5 0a 62 0e 99 a8 8f 18 3d 35 56'), textout=CWbytearray(b'1a 99 0a 65 c7 2a 90 99 77 79 b0 66 af 39 50 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19921875, -0.14160156, ...,  0.0078125 ,
            0.06445312,  0.06542969]), textin=CWbytearray(b'55 00 f1 e6 b9 d6 45 24 e5 d9 4b dd 64 70 1f 19'), textout=CWbytearray(b'b9 fd 7e a4 53 00 c1 21 32 6b 38 7e b0 b9 82 a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.140625  , ...,  0.01074219,
            0.06347656,  0.06835938]), textin=CWbytearray(b'6e 7c bc d7 1b b7 d1 d4 1f 85 1c 90 d3 e2 07 e9'), textout=CWbytearray(b'f3 38 19 3b c7 c9 bf 17 a6 c2 da bc d5 74 f6 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.20019531, -0.14550781, ...,  0.02441406,
            0.07519531,  0.07421875]), textin=CWbytearray(b'6f 26 5b 92 59 f3 f5 04 ca bf 2f 8e 3d 12 66 8a'), textout=CWbytearray(b'dd 91 69 11 2b 71 d0 a3 12 d8 5a 54 22 ed 3f 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'd5 a0 79 44 16 07 4e bf 71 4a 2b e6 20 05 31 2f'), textout=CWbytearray(b'c6 4d fb 42 ca f9 7e 14 59 64 15 24 76 a3 b9 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13671875, ...,  0.0390625 ,
            0.08496094,  0.08203125]), textin=CWbytearray(b'49 b7 e5 f3 02 bd 3d 73 14 7a 7d d4 b5 d3 eb 95'), textout=CWbytearray(b'20 e7 46 8e e6 9b b9 d6 9c a8 25 e9 ed 7a 21 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13671875, ...,  0.01953125,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'81 07 84 cf 3a d5 47 17 52 bd ec a1 9f a3 34 ee'), textout=CWbytearray(b'59 c9 0b 63 45 7f 08 b9 c6 8b b5 0a 8c da c3 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13574219, ...,  0.01171875,
            0.06542969,  0.06640625]), textin=CWbytearray(b'0d 18 76 74 4f 92 ae d1 47 f9 a7 4b d3 1d 1f b6'), textout=CWbytearray(b'26 e0 1b 1e 31 56 45 84 1a b6 b8 8f c4 a0 72 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19140625, -0.13964844, ...,  0.01464844,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'27 17 d1 ab 63 b5 df 3f 5c b9 ae 13 44 c6 af 5e'), textout=CWbytearray(b'99 fe eb a3 32 ba 72 d5 d2 c3 35 ec 59 24 4c 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14257812, ...,  0.02050781,
            0.07128906,  0.07421875]), textin=CWbytearray(b'32 e2 18 13 ce 8e 5f 4f 6e 0b ce cc db 4c 58 11'), textout=CWbytearray(b'9e 09 d7 79 b3 07 79 0c b7 5b 81 75 3f 29 49 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14648438, ...,  0.00195312,
            0.05664062,  0.06347656]), textin=CWbytearray(b'38 33 6c 5b c3 9c 17 55 c2 63 25 48 50 4c 1f 5d'), textout=CWbytearray(b'e7 8c 77 a3 63 09 14 04 57 64 2c 82 ca 85 0a 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14160156, ...,  0.01464844,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'8a 3e 64 21 c4 ce 3b 9c 99 70 df 90 77 fa fe 24'), textout=CWbytearray(b'1c 85 e4 0c 32 42 85 01 b1 8a c9 84 03 4f 2c 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14257812, ...,  0.00488281,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'f2 11 3f cf 51 07 39 d1 c3 4b 96 56 ad 51 ae 02'), textout=CWbytearray(b'29 64 58 86 44 7c 5f b6 9a fd d7 3b 69 d1 ac b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13671875, ...,  0.01464844,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'88 a3 9b 56 9d 2e e2 a1 92 b1 a7 55 06 5a e2 de'), textout=CWbytearray(b'13 7f 4c 4e 9f 6e ec 67 1f 86 c6 9f 2c 64 53 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19726562, -0.14355469, ...,  0.00585938,
            0.06054688,  0.06542969]), textin=CWbytearray(b'08 a8 dd f6 0c 15 85 e8 8a 94 5f 79 c8 ab 82 06'), textout=CWbytearray(b'df 10 94 ff 09 d0 23 4c fa 6b e2 64 bd 75 3f 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19628906, -0.13964844, ..., -0.00097656,
            0.0546875 ,  0.06152344]), textin=CWbytearray(b'35 c3 39 3e 6e df e5 19 9b 73 cb 90 8d 22 3f df'), textout=CWbytearray(b'5f 7c 40 63 dc aa 0b 4d 2a 32 cd e5 42 4b f0 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1953125 , -0.13964844, ...,  0.01757812,
            0.07128906,  0.07324219]), textin=CWbytearray(b'fa fe fa 0c 3e 6d 1f 5b f6 59 0b 6b e2 d5 e8 9d'), textout=CWbytearray(b'b9 40 d8 a3 ec 53 2f 8c 74 38 b6 7d 56 f7 53 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13769531, ...,  0.02050781,
            0.07128906,  0.07519531]), textin=CWbytearray(b'd2 ed f1 01 57 76 9c 58 73 bc 7d 67 2b 9b ee ae'), textout=CWbytearray(b'88 d8 44 68 18 c9 cc a0 c7 97 92 6c 21 48 44 c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.140625  , ...,  0.00683594,
            0.06152344,  0.06738281]), textin=CWbytearray(b'4f 66 b9 c3 6b 3c aa be 87 07 4b c6 c6 cd f9 ac'), textout=CWbytearray(b'a2 21 3a a9 aa 44 50 cf 9e 75 9a 92 92 65 69 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.14160156, ...,  0.02050781,
            0.07226562,  0.07324219]), textin=CWbytearray(b'ab c4 10 c6 be 60 4b ef 12 3c 2c 04 52 47 0c c2'), textout=CWbytearray(b'b2 0e 58 1c 35 84 b7 5c 76 d5 ff 68 4f e1 1d 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19335938, -0.14160156, ...,  0.00878906,
            0.06347656,  0.06640625]), textin=CWbytearray(b'24 67 6c 33 c8 29 02 cc 32 75 0f 86 34 ce fa 79'), textout=CWbytearray(b'69 cb 46 46 bd f2 ea 6f 0f dd 8d de 2a 5f e8 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14355469, ...,  0.01074219,
            0.06445312,  0.06835938]), textin=CWbytearray(b'dd 50 b2 47 94 f0 84 e8 77 00 05 99 ea 0f 47 33'), textout=CWbytearray(b'20 a6 15 e4 88 48 81 9e 1b d6 65 8b cf dd 93 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13964844, ...,  0.015625  ,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'ae e7 1e a7 21 70 c3 0f c5 ed 24 05 0d e0 9b a9'), textout=CWbytearray(b'6a fd 32 55 e3 ab ca 7d 1f 99 1f 04 1b 33 d8 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19824219, -0.13964844, ...,  0.01171875,
            0.06445312,  0.06640625]), textin=CWbytearray(b'80 dd 51 98 d2 a3 86 a5 05 3f b2 d8 4a 31 fa 94'), textout=CWbytearray(b'd8 5c 94 29 5a 04 e1 30 b7 56 9d fb 05 98 77 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.140625  , ...,  0.01660156,
            0.06933594,  0.07128906]), textin=CWbytearray(b'31 a9 55 0e 79 a8 f6 75 56 0d be 5b 5d 09 bb 26'), textout=CWbytearray(b'bd a2 d7 9b 41 25 42 5d c2 86 a4 5c 18 93 51 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.14257812, ...,  0.01855469,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'b9 db c0 95 f6 e3 7b 5e eb fe 8c 8f fe 85 3c 0d'), textout=CWbytearray(b'5b 1d c5 c8 a6 b4 0e 95 8c e7 74 ed fb 2f a0 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.13671875, ...,  0.01171875,
            0.06542969,  0.06835938]), textin=CWbytearray(b'2f 45 d2 82 6e 6e 85 1d 73 ea a6 2a 73 10 dd 42'), textout=CWbytearray(b'92 3b 22 74 17 c0 3e 0c 23 a4 42 f6 6d 68 eb d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.01953125,
            0.07226562,  0.07226562]), textin=CWbytearray(b'73 17 e8 8e 2d 38 1e 61 fb b4 3f 17 14 53 96 3c'), textout=CWbytearray(b'18 9c 68 e1 f8 85 70 d5 04 25 75 ee 0d d9 e9 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.203125  , -0.14355469, ...,  0.00976562,
            0.06445312,  0.06640625]), textin=CWbytearray(b'25 a0 97 0f 1b 38 1f 7b 06 6d f3 98 71 1a 78 1c'), textout=CWbytearray(b'a5 3e 38 cd 22 56 99 a2 5b aa d7 94 11 ac 82 b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19921875, -0.14257812, ...,  0.01953125,
            0.07226562,  0.07226562]), textin=CWbytearray(b'4b 7a d5 45 7f 7a 13 9d 77 7a 16 da 31 5a 6d 5b'), textout=CWbytearray(b'07 dd b0 f5 87 5f 89 08 b9 14 13 d3 2d 24 56 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19726562, -0.14453125, ...,  0.02636719,
            0.07617188,  0.07714844]), textin=CWbytearray(b'c0 5c af d0 fc 61 10 aa 9c a3 e2 63 26 51 74 5d'), textout=CWbytearray(b'b7 3e 10 c4 70 af d6 49 c5 35 db ef 17 e4 f4 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14160156, ...,  0.015625  ,
            0.06835938,  0.06835938]), textin=CWbytearray(b'4f f4 66 08 9d c1 9a 4b c2 3c 79 b5 e6 a5 12 15'), textout=CWbytearray(b'b6 f0 da 60 60 03 66 6e 0e 8f c4 33 c5 42 f7 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14160156, ...,  0.00878906,
            0.06542969,  0.06738281]), textin=CWbytearray(b'8c a2 a7 bb dc 3b bc ac 49 0c fe 2d b5 51 45 51'), textout=CWbytearray(b'ab ef 8a 06 09 88 8f c4 48 28 80 36 bc 7b 4d c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.1875    , -0.13574219, ...,  0.00292969,
            0.05761719,  0.06152344]), textin=CWbytearray(b'4a 0b 24 ad b4 a8 8c 22 b5 be 0a 6d fd b2 e2 c5'), textout=CWbytearray(b'b3 a1 74 78 38 e3 21 10 d1 af 03 76 8e 78 79 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14453125, ...,  0.02246094,
            0.07324219,  0.07617188]), textin=CWbytearray(b'd2 19 af 79 5b ef 8f 87 bb 4d 5f eb 94 cb 8a 23'), textout=CWbytearray(b'69 1a d0 eb 7a 81 39 45 0d f4 42 e8 c6 9d 90 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19042969, -0.13964844, ...,  0.015625  ,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'c0 45 5e e8 ad d8 73 54 d3 52 c2 7b 15 ac b2 d2'), textout=CWbytearray(b'48 11 c0 e9 9a 5e 49 2f 1c 39 89 4f b8 d2 2d 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.20019531, -0.14453125, ...,  0.02539062,
            0.07421875,  0.07421875]), textin=CWbytearray(b'c7 82 31 75 74 1c 8c fe 68 0c 78 ea e1 18 30 9d'), textout=CWbytearray(b'c9 55 57 12 b2 89 36 12 79 eb 1c 67 37 99 82 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.14160156, ...,  0.01855469,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'a6 9f 8f e1 f1 58 80 fa b4 d7 43 92 23 7a f1 6d'), textout=CWbytearray(b'18 44 16 7f f4 61 29 b5 7c 81 7c 01 ed 85 6d 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.140625  , ...,  0.00878906,
            0.06152344,  0.06542969]), textin=CWbytearray(b'1a 71 0c 3e 8e 96 82 aa e4 f0 5f b4 23 f0 16 c5'), textout=CWbytearray(b'83 68 65 e6 6a 45 db e8 c7 e9 e8 a9 bb 09 65 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18847656, -0.13867188, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'28 2d 11 53 c7 9c d9 72 ba 7c c2 59 a2 52 35 c7'), textout=CWbytearray(b'11 07 19 c8 52 79 ee 3e b4 1e 4f 72 e5 6f 58 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14257812, ...,  0.00976562,
            0.06445312,  0.06445312]), textin=CWbytearray(b'f3 54 b7 4c 66 cd 80 cb 46 8c 33 78 aa 4d 34 64'), textout=CWbytearray(b'df eb 27 b1 90 c3 68 88 86 b0 fe 22 b5 f7 ca 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19238281, -0.14355469, ...,  0.01660156,
            0.06640625,  0.07226562]), textin=CWbytearray(b'f7 4d 02 e7 9a a1 a7 08 a3 32 5c c8 58 97 e6 34'), textout=CWbytearray(b'e1 ac 37 d4 ab 5d 78 b7 6e 66 fa 62 74 77 00 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13964844, ...,  0.02148438,
            0.07421875,  0.07324219]), textin=CWbytearray(b'34 bc 22 cb e7 81 35 5a c3 47 a4 8a 9d ac 49 ca'), textout=CWbytearray(b'cd ff 61 3b 97 31 55 9e 7b d0 43 0e 76 06 fe db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19824219, -0.14257812, ...,  0.00195312,
            0.05664062,  0.06347656]), textin=CWbytearray(b'68 f6 89 2b 51 21 18 25 cb 48 05 b2 9f 65 bf 52'), textout=CWbytearray(b'ce 9e 4c fd fd 49 8f be 96 83 c2 5f 5f 2a aa 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13867188, ...,  0.01269531,
            0.06445312,  0.06835938]), textin=CWbytearray(b'00 61 d4 1c e6 1d 2c 6f 6d 7e 6e fc 4e 03 f6 a8'), textout=CWbytearray(b'82 9d 34 7c 2b cc 17 ab fb 65 92 5d 12 68 60 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19433594, -0.13574219, ...,  0.02246094,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'c7 fd d2 4f fb be 39 4a 6f da 22 14 34 ab 68 ce'), textout=CWbytearray(b'81 a7 ed eb 80 9d 51 3a 58 ec 1b b2 3c 3b ca 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.14160156, ...,  0.00878906,
            0.05957031,  0.06445312]), textin=CWbytearray(b'e7 5e fd 00 81 b9 d1 7d 94 ef 66 b7 ff 98 5c ac'), textout=CWbytearray(b'0a 65 1c 65 3e 56 04 89 de 60 3a 7d ee 08 d6 c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.14257812, ...,  0.01855469,
            0.06738281,  0.07128906]), textin=CWbytearray(b'45 52 21 bc a8 8a 6a 61 c8 b4 a1 75 e5 2e ed 94'), textout=CWbytearray(b'4e 34 68 a5 72 ab c8 60 53 a7 37 be 5a be fb 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.14257812, ...,  0.00585938,
            0.05957031,  0.06542969]), textin=CWbytearray(b'f5 78 81 e5 7c de 2b d0 2a d4 f6 83 32 b3 73 7c'), textout=CWbytearray(b'55 43 8f e5 32 7c c2 c4 78 47 59 e2 e3 ab 26 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1953125 , -0.14160156, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'af 54 5f cb ab 26 b0 b8 73 6a f2 45 08 b8 15 87'), textout=CWbytearray(b'ab b4 bb 5b 88 20 dd 52 75 95 7f 7c e7 98 25 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.140625  , ...,  0.00976562,
            0.06640625,  0.06835938]), textin=CWbytearray(b'65 00 6c d0 94 30 48 a3 d3 b0 64 3e cb 5e 75 4e'), textout=CWbytearray(b'83 4b 4e 1c 4a 41 e2 7d a9 5a 4c 04 cb aa ab 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.13964844, ...,  0.00390625,
            0.05859375,  0.06542969]), textin=CWbytearray(b'ec 63 24 da 86 23 16 db b1 45 e3 75 13 1e 00 ab'), textout=CWbytearray(b'5f 1d 6a 6c 00 a7 77 88 e9 29 d8 13 f7 f2 ea 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14355469, ...,  0.00390625,
            0.06054688,  0.06347656]), textin=CWbytearray(b'30 21 cb c1 4e 31 d3 57 78 4c d0 78 bd 51 2c 40'), textout=CWbytearray(b'e3 b3 d3 19 1f c0 7d ab ea 4e 51 c1 31 2c 17 a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19433594, -0.13671875, ...,  0.01855469,
            0.07128906,  0.07324219]), textin=CWbytearray(b'd0 76 cb ae 5a 4e 67 cd 66 ad 8e 59 a8 a7 72 cd'), textout=CWbytearray(b'e1 98 d5 cb d4 0c 18 16 ab 34 00 68 d9 ba ae 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14550781, ...,  0.03027344,
            0.07714844,  0.07910156]), textin=CWbytearray(b'83 db 85 b0 68 c8 8b cf 28 fe ad cd 5f eb 6a 50'), textout=CWbytearray(b'48 0d ca 2e 28 f7 4c 66 89 9d 4a 18 73 05 2c 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14453125, ...,  0.01757812,
            0.07128906,  0.07128906]), textin=CWbytearray(b'96 d1 83 96 7b 6d fc bc 06 47 de bf c4 20 7d 30'), textout=CWbytearray(b'dc 41 c4 83 24 ff e4 f3 86 16 e7 80 97 05 22 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19726562, -0.14257812, ..., -0.00488281,
            0.0546875 ,  0.05957031]), textin=CWbytearray(b'df f7 16 3b 86 78 c6 1a 76 ad b4 6e 10 d4 46 79'), textout=CWbytearray(b'52 a0 22 e4 37 7d 23 d0 e5 b4 60 3f ad 59 84 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.13964844, ...,  0.02539062,
            0.07324219,  0.07519531]), textin=CWbytearray(b'53 e8 29 03 fb e5 c0 cd 65 2b c4 a8 a2 87 b7 6e'), textout=CWbytearray(b'c4 4e a1 c8 0e 43 82 c1 1d e7 38 08 35 a6 ad a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19335938, -0.14160156, ...,  0.00195312,
            0.05761719,  0.06054688]), textin=CWbytearray(b'b9 90 fc 53 ed 14 6c 44 a0 b0 5d 29 6f f0 5e 18'), textout=CWbytearray(b'17 aa b0 f9 86 2c 33 3d 20 0e 14 1c 2e 99 32 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.14160156, ...,  0.015625  ,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'47 3e 7a 6c 88 42 07 c8 2b dd 3e d4 46 6d 94 69'), textout=CWbytearray(b'2f e6 1b e4 10 dc cd e0 0b 42 7a 0c 71 86 01 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13769531, ...,  0.02148438,
            0.07324219,  0.07519531]), textin=CWbytearray(b'77 1a 4e b6 12 c8 29 1b 30 66 2d 2a e0 a8 f6 36'), textout=CWbytearray(b'e2 52 33 02 f0 3a 2d 9a 80 a3 92 d3 2a 23 b1 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18652344, -0.13671875, ...,  0.01464844,
            0.06640625,  0.07128906]), textin=CWbytearray(b'f0 b9 a5 20 1e 85 d2 b3 b8 64 ef 60 96 f1 ed a3'), textout=CWbytearray(b'69 de b9 11 57 44 26 c3 4f 71 9b d3 13 e3 fd f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19628906, -0.140625  , ...,  0.00390625,
            0.05859375,  0.06640625]), textin=CWbytearray(b'21 fe ff 89 42 b6 09 aa d6 33 50 a4 2c 13 31 73'), textout=CWbytearray(b'85 01 08 b6 aa ce 72 98 8f 6f 87 66 b4 27 ea 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19726562, -0.14160156, ...,  0.015625  ,
            0.06640625,  0.06835938]), textin=CWbytearray(b'fd 48 0a 77 03 18 dd ca 7a 49 5e 4c 05 90 e2 5d'), textout=CWbytearray(b'03 78 f5 53 c4 7b 79 c3 8e 1b 5f c9 ed f3 fb ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19628906, -0.14746094, ...,  0.00488281,
            0.05664062,  0.06152344]), textin=CWbytearray(b'a1 a4 76 e0 8b 7b 8a 79 53 36 3d b5 2d 18 28 4f'), textout=CWbytearray(b'c8 c5 24 04 01 95 88 9a c4 b3 53 30 6a 86 6f 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1953125 , -0.13671875, ..., -0.00097656,
            0.0546875 ,  0.06445312]), textin=CWbytearray(b'ae 71 15 dd de 72 51 48 8f 0e 19 07 a1 0f e7 ac'), textout=CWbytearray(b'73 4d 27 51 b9 0f 48 b0 27 a5 8e 13 9e 30 36 88'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19824219, -0.13867188, ...,  0.00488281,
            0.06054688,  0.06347656]), textin=CWbytearray(b'fd 3d 2a ef 49 d6 42 38 bc 80 77 66 7a f3 82 46'), textout=CWbytearray(b'0e 90 03 7a 64 04 4a 1b d8 51 22 ba 56 a7 cb 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.14160156, ...,  0.00878906,
            0.06054688,  0.06640625]), textin=CWbytearray(b'7a 52 99 02 32 dc 92 71 b7 5d 7d 67 68 0f 9d b5'), textout=CWbytearray(b'24 80 bc 15 a9 a5 89 e1 c9 f5 d0 f2 a2 6e 85 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.19140625, -0.13476562, ...,  0.00878906,
            0.06542969,  0.06640625]), textin=CWbytearray(b'46 20 a9 af b3 7f 40 28 2b be d6 22 ff 7c e5 e4'), textout=CWbytearray(b'28 3b dd bf 3e e2 42 3c 66 ce 42 ee 19 ca c4 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.14160156, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'45 34 64 91 f8 79 ec a2 0e 93 31 9d 17 6f 2f f4'), textout=CWbytearray(b'b6 f5 4a 55 88 ea fb 15 5a 8c 93 c3 25 5c 24 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19628906, -0.140625  , ...,  0.01660156,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'37 64 e6 b9 8e f2 42 a0 4f 25 1a f1 32 2e f8 27'), textout=CWbytearray(b'15 d7 c7 7b 7d ff c4 2f 1f 68 ee f0 37 49 0f 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14257812, ...,  0.01367188,
            0.06445312,  0.06738281]), textin=CWbytearray(b'd8 ed fe 4d 7b 5e 1e f9 4a c8 31 ab ea 70 e0 2f'), textout=CWbytearray(b'72 3b 04 35 e9 14 e5 a6 3e 44 03 22 5d d5 80 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14355469, ..., -0.00488281,
            0.0546875 ,  0.05957031]), textin=CWbytearray(b'11 be c9 9a 09 c5 12 b3 63 08 2f e5 53 16 89 25'), textout=CWbytearray(b'2d d9 2e 02 a4 13 84 5e 08 0e 23 9a 09 d3 dd 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13671875, ...,  0.02148438,
            0.07421875,  0.07421875]), textin=CWbytearray(b'45 75 af 33 56 df 73 f5 80 d9 9a 97 86 97 05 c3'), textout=CWbytearray(b'84 ca a6 1f de b7 c0 ab 86 10 8f 5f 70 72 be 4b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13671875, ...,  0.02148438,
            0.06933594,  0.07421875]), textin=CWbytearray(b'04 12 92 3b 61 24 40 07 a3 c2 50 e9 42 c6 a4 dd'), textout=CWbytearray(b'cd 60 ec 56 94 12 d2 c4 60 27 93 19 de 0a b2 1a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1953125 , -0.13964844, ...,  0.00683594,
            0.06054688,  0.06445312]), textin=CWbytearray(b'b7 87 81 ab e4 60 cd 55 e4 9d 62 0d 55 60 16 c0'), textout=CWbytearray(b'73 9f 54 56 bf 5f b2 b4 36 a2 6d 41 e4 6d 94 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13671875, ...,  0.00976562,
            0.06347656,  0.06738281]), textin=CWbytearray(b'a1 fb 05 61 a1 3e b1 19 8e 1a 00 fb d8 e7 fd 66'), textout=CWbytearray(b'a9 69 c3 5d 76 49 ed 58 88 a3 5d b9 2e b6 22 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1953125 , -0.13964844, ...,  0.02148438,
            0.07128906,  0.07324219]), textin=CWbytearray(b'ec 73 53 26 82 a7 2f 04 ab 67 85 e4 3d 5a dc 62'), textout=CWbytearray(b'e4 c9 f8 7a 03 dc 43 e6 1a 02 29 91 fb 68 1b 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.13964844, ...,  0.00292969,
            0.06054688,  0.06347656]), textin=CWbytearray(b'96 17 ae ef 5e 85 9d c2 c9 82 53 05 74 49 5c a1'), textout=CWbytearray(b'85 b1 3c 08 a7 f0 c9 83 30 e5 11 a0 2a 94 82 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19824219, -0.14160156, ...,  0.01464844,
            0.06738281,  0.06835938]), textin=CWbytearray(b'71 bf 5c f3 93 a6 4a 82 43 e8 ab 7f ba 43 42 89'), textout=CWbytearray(b'db 7a 12 39 b2 cd f1 32 7e 84 ca 1d 0c bc f8 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14453125, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'21 88 cb b1 8b 20 3b 4b f5 70 56 f7 1c d7 1a 51'), textout=CWbytearray(b'6f 5d 1e 64 db 1d 4e 81 b3 8b a6 ff fb b0 00 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19921875, -0.14355469, ..., -0.00195312,
            0.05664062,  0.06054688]), textin=CWbytearray(b'f2 3b e3 1f f0 f1 e2 d0 1a 1e e2 3a 89 70 35 9e'), textout=CWbytearray(b'69 72 72 3e 42 ee 31 0e f7 da 11 00 01 01 66 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.19140625, -0.13769531, ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'9d 33 c8 d4 4d b3 21 3c 60 49 02 e5 d6 87 5c ce'), textout=CWbytearray(b'20 71 d2 ce dd 89 44 b7 55 c0 37 e9 ae e5 97 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19335938, -0.13964844, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'60 70 f1 c5 39 ee 90 bf b9 df 73 72 7c e0 40 49'), textout=CWbytearray(b'51 f3 d9 c9 b5 bb 6c d0 a4 83 b5 f2 3f 01 97 66'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14453125, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'65 e8 b8 c6 37 09 05 fd 6e 68 70 0c 41 59 da 4c'), textout=CWbytearray(b'63 19 62 b8 5a 16 d3 10 99 ae c8 a1 52 f9 12 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13769531, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'8d 58 7c 19 09 01 8b 3d 39 2e a7 53 83 08 df da'), textout=CWbytearray(b'ca 1d 37 d2 2b 9e c0 50 d5 ff 60 63 49 0d 61 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19628906, -0.14160156, ...,  0.02929688,
            0.07617188,  0.07714844]), textin=CWbytearray(b'6d d7 cc 33 f7 79 ae e2 20 e3 cb 30 a9 bb 39 3c'), textout=CWbytearray(b'ce 07 4b ad ab 10 da e3 3d 53 ac f2 d2 e4 83 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19335938, -0.13964844, ...,  0.02246094,
            0.07226562,  0.07519531]), textin=CWbytearray(b'03 82 3b 80 f1 69 f6 c6 98 b3 20 0d 51 41 8a f0'), textout=CWbytearray(b'd2 32 a7 56 4b f9 39 6c 3f ec 8e df 34 dc aa cc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1953125 , -0.14257812, ...,  0.01367188,
            0.06738281,  0.06933594]), textin=CWbytearray(b'3f bf 48 fc 60 3e e8 0f 9f 2e 5a 9c 6d 23 55 22'), textout=CWbytearray(b'ef f2 55 c9 8e 58 39 ff 7d a0 a7 f3 6d 6d e6 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19726562, -0.13867188, ...,  0.00683594,
            0.06347656,  0.06542969]), textin=CWbytearray(b'a6 3d a6 32 fe 2c cd 2b 83 80 9c df a0 c4 21 4d'), textout=CWbytearray(b'06 a3 ed 20 7f ba 56 49 62 b0 ce 82 5f b2 e3 f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.140625  , ...,  0.00390625,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'4c 25 39 be da 86 7f 0c 40 9f 46 a4 0d e0 77 03'), textout=CWbytearray(b'30 18 3b cf 76 23 6d 3f 6d 86 35 e7 36 e9 2e 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.140625  , ...,  0.01171875,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'38 b4 c8 10 c1 bb 40 9d 6f eb 15 74 60 cb ad 04'), textout=CWbytearray(b'45 f5 92 ea d2 e0 f4 e8 ba 7f 24 93 d9 47 a9 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19433594, -0.14160156, ...,  0.00878906,
            0.06347656,  0.06640625]), textin=CWbytearray(b'ae 96 bc eb 01 6e 52 94 8a bd f7 28 5c b6 3f db'), textout=CWbytearray(b'ac 18 f1 47 a0 e5 a4 71 72 95 74 ea ab 85 d6 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19140625, -0.13964844, ...,  0.015625  ,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'bf 6f 4c 87 20 33 af e0 ca 70 96 e2 62 df ba c4'), textout=CWbytearray(b'83 55 db 11 7e 5a d5 fc 48 94 8e e5 ec 37 9a 56'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19238281, -0.140625  , ...,  0.02441406,
            0.07324219,  0.07421875]), textin=CWbytearray(b'f2 43 ac 03 54 73 fe 16 e9 4d 6d 4a c6 eb da 61'), textout=CWbytearray(b'37 20 f7 c2 d2 0f 6b 57 61 f4 63 cd 9b 68 23 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1953125 , -0.14160156, ...,  0.01855469,
            0.06933594,  0.07128906]), textin=CWbytearray(b'3a ee db 31 a3 91 22 54 d5 07 c1 b9 f3 06 f7 90'), textout=CWbytearray(b'60 b3 ea 04 4e 3e 57 bb f7 60 12 dc 74 a1 9d d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13964844, ...,  0.01269531,
            0.06738281,  0.06835938]), textin=CWbytearray(b'1c 1b b1 71 2c 09 03 85 3f b5 20 4b 64 85 b7 47'), textout=CWbytearray(b'c9 cc 54 0a 58 f4 86 56 9c ee 42 d8 76 74 2f 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14453125, ...,  0.00585938,
            0.06054688,  0.06445312]), textin=CWbytearray(b'c7 88 aa b5 5a 34 99 92 1d a2 a9 18 ea 53 23 8e'), textout=CWbytearray(b'23 ee 76 64 3c 8a 23 4b 0a 4e d0 a4 6b 0b 10 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13964844, ...,  0.00683594,
            0.06445312,  0.06542969]), textin=CWbytearray(b'22 3e ec 58 d5 b2 8d 38 23 68 ec 9f 8b 29 2f a7'), textout=CWbytearray(b'5e c7 8b e7 04 cb 76 7b f9 45 87 49 5b 78 31 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19140625, -0.13476562, ...,  0.01171875,
            0.06640625,  0.06738281]), textin=CWbytearray(b'bb 11 70 c8 d6 10 f2 48 94 1c 0c f1 f5 cb da a0'), textout=CWbytearray(b'c2 4a db 3a b3 25 fe 3b 4c 49 ca 14 ae c6 4f 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14355469, ...,  0.0078125 ,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'69 24 1c 38 27 c8 11 fa 43 b8 41 5f 8a b8 3d 42'), textout=CWbytearray(b'49 3f 22 a4 bd 17 b0 70 4d 6b 1b 34 78 7c 8e 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19335938, -0.13867188, ...,  0.01074219,
            0.06347656,  0.06738281]), textin=CWbytearray(b'18 48 dc bc 1c f1 58 eb 4a 90 ee 6c 82 b6 2f d0'), textout=CWbytearray(b'c3 a9 a4 d2 61 6d 0d e0 48 1a 02 bb a6 d5 aa 1d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19628906, -0.13964844, ...,  0.01171875,
            0.06640625,  0.06933594]), textin=CWbytearray(b'74 f7 c6 00 c4 8b 62 67 59 6e 30 57 47 29 f3 8e'), textout=CWbytearray(b'52 d8 3b 5a 6d 98 2a 74 91 c3 f5 2d 20 8b 14 69'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19433594, -0.13964844, ...,  0.01171875,
            0.06542969,  0.06835938]), textin=CWbytearray(b'a8 e8 04 89 e1 ac 33 fd 95 3d 91 4f 3c 76 ff 81'), textout=CWbytearray(b'dc 39 27 7f dd e4 84 41 83 55 7d 3e 0a 35 d4 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19433594, -0.140625  , ...,  0.0234375 ,
            0.07421875,  0.07519531]), textin=CWbytearray(b'7a 8a 50 39 12 38 f3 8f 00 2c e6 38 bb a7 fd 59'), textout=CWbytearray(b'4b 8a 0c b0 b4 69 42 92 ef ae 25 0e 14 2c ae 03'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18847656, -0.13964844, ...,  0.01269531,
            0.06152344,  0.06738281]), textin=CWbytearray(b'5b 9b 6d 43 9b 98 2e 42 a8 6f 6e fa b1 b7 d7 43'), textout=CWbytearray(b'01 64 8a 4a 5c 0a cd d3 73 ea 28 71 ed 5c 00 e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.19335938, -0.13867188, ...,  0.01464844,
            0.06542969,  0.06933594]), textin=CWbytearray(b'4c 63 13 f9 cc 25 f4 d8 b1 63 81 fa ec b2 0c ae'), textout=CWbytearray(b'6d b0 de f5 52 da df 0d a3 69 7b 46 c7 9a 78 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19335938, -0.13867188, ...,  0.00292969,
            0.05761719,  0.06445312]), textin=CWbytearray(b'fb cb 59 4c 82 d7 78 51 f5 c7 9e 3e ee 4a 0f ba'), textout=CWbytearray(b'42 bc d4 a3 87 7f 7a 44 4d 8a ec 8f f6 1e 5c 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19824219, -0.140625  , ...,  0.02148438,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'19 47 d4 08 bd 71 dd 88 f8 62 e9 7a f2 02 2f 56'), textout=CWbytearray(b'05 d2 16 ad 8e 1f 90 b9 c8 76 6b 6e b5 55 7c 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19042969, -0.140625  , ...,  0.015625  ,
            0.06640625,  0.07128906]), textin=CWbytearray(b'e2 11 fc 59 de 4b 6d fe 8c 8e 54 d5 30 d3 d4 29'), textout=CWbytearray(b'86 21 1b ff b5 a7 de 72 f4 56 92 00 7f f9 cc 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19628906, -0.14257812, ...,  0.01464844,
            0.06933594,  0.07226562]), textin=CWbytearray(b'd5 08 6e 1f e6 59 6c 16 eb 96 f4 8d c7 d8 30 8f'), textout=CWbytearray(b'14 e1 1c af 0e ac b0 6f c4 87 39 5c eb 96 e5 7f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    


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
    0.006238169886615907
    0.006238169886615907
    






.. parsed-literal::

    0x7e(real = 0x7E)
    0.004857162197431902
    0.004857162197431902
    






.. parsed-literal::

    0x15(real = 0x15)
    0.004162968463556363
    0.004162968463556363
    






.. parsed-literal::

    0x16(real = 0x16)
    0.004566896398972474
    0.004566896398972474
    






.. parsed-literal::

    0x28(real = 0x28)
    0.005290881529104462
    0.005290881529104462
    






.. parsed-literal::

    0xae(real = 0xAE)
    0.003680931669183367
    0.003680931669183367
    






.. parsed-literal::

    0xd2(real = 0xD2)
    0.0050522843491593306
    0.0050522843491593306
    






.. parsed-literal::

    0xa6(real = 0xA6)
    0.006789775401730397
    0.006789775401730397
    




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

    

    <div id="05a89b85-e23e-4b90-ad48-f8029eeefd18"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#05a89b85-e23e-4b90-ad48-f8029eeefd18');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="256e0115-dd8b-4c2c-8eae-8f3f9061237e" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="44d3ce52-7369-492e-a8a3-01ceeff41f08"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#44d3ce52-7369-492e-a8a3-01ceeff41f08');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"1c1dc18b-bf6a-47a0-ac7d-d64eb6ba1a62":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"},{"id":"1042","type":"GlyphRenderer"},{"id":"1047","type":"GlyphRenderer"}],"title":{"id":"1050","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"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","dtype":"float64","shape":[4000]}},"selected":{"id":"1060","type":"Selection"},"selection_policy":{"id":"1061","type":"UnionRenderers"}},"id":"1044","type":"ColumnDataSource"},{"attributes":{"line_color":"green","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"source":{"id":"1044","type":"ColumnDataSource"}},"id":"1048","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1046","type":"Line"},{"attributes":{"data_source":{"id":"1044","type":"ColumnDataSource"},"glyph":{"id":"1045","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1046","type":"Line"},"selection_glyph":null,"view":{"id":"1048","type":"CDSView"}},"id":"1047","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"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","dtype":"float64","shape":[4000]}},"selected":{"id":"1056","type":"Selection"},"selection_policy":{"id":"1057","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1040","type":"Line"},{"attributes":{"source":{"id":"1039","type":"ColumnDataSource"}},"id":"1043","type":"CDSView"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1039","type":"ColumnDataSource"},"glyph":{"id":"1040","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1041","type":"Line"},"selection_glyph":null,"view":{"id":"1043","type":"CDSView"}},"id":"1042","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1050","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1055","type":"BoxAnnotation"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1041","type":"Line"},{"attributes":{},"id":"1054","type":"BasicTickFormatter"},{"attributes":{},"id":"1056","type":"Selection"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1057","type":"UnionRenderers"},{"attributes":{},"id":"1058","type":"Selection"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1059","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1052","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1060","type":"Selection"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1061","type":"UnionRenderers"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1054","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"line_color":"blue","x":{"field":"x"},"y":{"field":"y"}},"id":"1045","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"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","dtype":"float64","shape":[4000]}},"selected":{"id":"1058","type":"Selection"},"selection_policy":{"id":"1059","type":"UnionRenderers"}},"id":"1039","type":"ColumnDataSource"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{},"id":"1052","type":"BasicTickFormatter"},{"attributes":{"overlay":{"id":"1055","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"1c1dc18b-bf6a-47a0-ac7d-d64eb6ba1a62","roots":{"1002":"256e0115-dd8b-4c2c-8eae-8f3f9061237e"}}];
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

