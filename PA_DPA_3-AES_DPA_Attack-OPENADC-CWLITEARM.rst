
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
        trig_count = 7873989
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

    Trace(wave=array([ 0.01757812, -0.18847656, -0.13867188, ...,  0.00878906,
            0.05957031,  0.06445312]), textin=CWbytearray(b'a2 a9 8a 3d 14 db c9 77 a1 77 89 40 22 b0 47 81'), textout=CWbytearray(b'23 1a 02 fe 15 74 4d a1 35 16 71 66 0d f2 f7 a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14355469, ...,  0.02441406,
            0.07226562,  0.07324219]), textin=CWbytearray(b'7d 77 42 0d b8 9f d9 a9 c0 9a d1 c4 42 b1 42 97'), textout=CWbytearray(b'60 d7 50 db a4 c7 4c e4 21 36 65 1f 9f f8 10 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19042969, -0.13867188, ...,  0.00292969,
            0.05859375,  0.06445312]), textin=CWbytearray(b'41 7a d6 cc 97 20 1d d4 81 32 21 97 23 2b b6 7d'), textout=CWbytearray(b'4b b7 1c 07 5d 73 77 6f f3 a7 0a 33 79 51 86 fa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14257812, ...,  0.0078125 ,
            0.05957031,  0.06445312]), textin=CWbytearray(b'3d 51 3c 5b 04 e7 e1 5d 20 10 8e 46 1e 27 02 10'), textout=CWbytearray(b'cc ab 9c 32 79 7b 45 77 5b 2f c6 7f 5a aa 23 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13867188, ...,  0.01757812,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'36 0d 15 7b bf 2f 6d bc 20 31 8a ab 96 87 3a c0'), textout=CWbytearray(b'd1 a3 35 f0 24 77 29 fa 43 e2 87 4f e0 c5 b5 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.14160156, ...,  0.00976562,
            0.06347656,  0.06445312]), textin=CWbytearray(b'51 1b 64 90 04 4c 18 f3 77 ab bb 27 ff 3c 44 eb'), textout=CWbytearray(b'3a ae 4b 12 89 dd 99 b7 cb fe e3 4d 6a 62 1d 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14355469, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'f7 9c ea b9 c3 23 82 36 b9 31 e8 50 86 80 6b 63'), textout=CWbytearray(b'da 40 3f e8 0d 41 7e fd 21 65 b0 9d b3 c7 1d 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18457031, -0.13378906, ...,  0.00292969,
            0.05761719,  0.06054688]), textin=CWbytearray(b'75 6e e3 3b 3c 17 80 aa a8 07 c1 69 17 e4 f1 b5'), textout=CWbytearray(b'19 5a de f6 87 62 68 3d 20 19 b3 1f 8b 0a 79 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.13769531, ...,  0.02050781,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'a1 63 cd 03 92 cd a3 21 1a 7c 8d 41 28 86 9d f8'), textout=CWbytearray(b'c7 7d b6 a3 24 9f 61 10 32 f6 ee 4d 46 01 f7 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.13867188, ...,  0.00292969,
            0.05761719,  0.06347656]), textin=CWbytearray(b'a3 52 2d 4e c0 c0 d1 1a 93 27 ab 7d e2 18 ab 19'), textout=CWbytearray(b'c4 c7 a4 8d 6f f0 4f 83 82 dd a1 7d 3c 59 08 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ...,  0.015625  ,
            0.06738281,  0.06738281]), textin=CWbytearray(b'8a 5e 10 07 72 6d 47 69 7d 6f ed 00 fc 87 fe 39'), textout=CWbytearray(b'd8 20 39 55 32 0b dc be 46 71 db d9 41 d4 ea 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18847656, -0.13378906, ...,  0.01757812,
            0.06640625,  0.06835938]), textin=CWbytearray(b'76 8e 05 a8 7e 3a ff cd 54 9b 76 cc e0 7a 5c c0'), textout=CWbytearray(b'ea 45 3d 56 3e 55 3d 37 23 0c a0 d3 ea 20 5b f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13671875, ...,  0.02539062,
            0.07128906,  0.07421875]), textin=CWbytearray(b'f1 52 6d 48 4a 18 4a b8 8a c1 87 62 43 6d be dd'), textout=CWbytearray(b'8f a7 b2 90 13 13 eb 95 1d 9d d7 c0 17 28 fb ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19140625, -0.14257812, ...,  0.0234375 ,
            0.07421875,  0.07421875]), textin=CWbytearray(b'29 32 ce 1d f7 d6 98 99 62 bf ae bf 4a a0 38 50'), textout=CWbytearray(b'c0 a1 10 7d 08 b9 51 b0 4d fa 20 76 8d bc ae e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.14257812, ...,  0.01367188,
            0.06445312,  0.06835938]), textin=CWbytearray(b'91 db 8d ea ea c0 a9 24 92 67 4e 59 da 5f 9b d1'), textout=CWbytearray(b'23 a9 7b a9 26 07 fd e5 d6 8f 00 d9 c3 1c 03 d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.13964844, ...,  0.01855469,
            0.06835938,  0.07128906]), textin=CWbytearray(b'63 46 c8 f1 8b 31 55 0a 4c f1 05 74 e0 c6 1e 04'), textout=CWbytearray(b'4e cc 79 a9 bf b9 58 7d c4 0e 9d e3 7b 80 ab 1a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.19238281, -0.13769531, ...,  0.01757812,
            0.06835938,  0.06933594]), textin=CWbytearray(b'1a 54 32 31 5f 69 cb 79 35 19 7e fb 6d e8 51 de'), textout=CWbytearray(b'bc 3b c3 d3 57 1d c6 5e 34 4f e3 73 1b 97 fe 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14257812, ...,  0.0234375 ,
            0.07128906,  0.07324219]), textin=CWbytearray(b'fe e1 da c6 1b 0b c4 92 97 c5 1d 92 f1 85 87 92'), textout=CWbytearray(b'50 92 b3 98 c5 8b b3 4c ab ea 15 f0 7f 5c 26 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13867188, ...,  0.0078125 ,
            0.05957031,  0.06445312]), textin=CWbytearray(b'b8 2c bb 17 ff 1e a6 37 c9 61 fb ac c5 a8 a5 70'), textout=CWbytearray(b'52 c0 a2 f9 78 06 1b ff f8 48 36 09 be 59 2d d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19628906, -0.14648438, ...,  0.01464844,
            0.06542969,  0.06640625]), textin=CWbytearray(b'c4 6e 1e a5 8d 97 45 53 9f ce 5f 7d c7 38 0c 4b'), textout=CWbytearray(b'74 5f 90 11 f5 4d c9 2a 13 55 5c 76 a9 0e 08 7c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.01464844,
            0.06738281,  0.06640625]), textin=CWbytearray(b'e7 6a 75 12 95 e9 0e 1d 46 af 98 f5 05 7c 41 f0'), textout=CWbytearray(b'e4 d5 02 24 3f 01 be 65 9c ce d7 df 8c e6 fb 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14550781, ...,  0.00488281,
            0.05761719,  0.06152344]), textin=CWbytearray(b'96 4e ad da 8e a6 94 33 09 e8 ca 02 f4 0a 72 8f'), textout=CWbytearray(b'de 66 a2 10 24 78 e9 83 df 8e 81 35 02 50 81 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18847656, -0.14160156, ...,  0.02050781,
            0.06933594,  0.07226562]), textin=CWbytearray(b'0a 81 47 c2 1c 98 77 8c c4 1d 3f 9f 7d 28 fc 9b'), textout=CWbytearray(b'34 b5 38 ae 26 36 fa ee 25 54 dd 60 00 03 26 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.13964844, ...,  0.01757812,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'e2 cf f6 5c c6 60 56 d3 d6 42 32 60 9c ab be cf'), textout=CWbytearray(b'a0 a4 e1 8f ed f8 ba 1f 46 3c f8 e3 03 ad 73 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.00585938,
            0.05761719,  0.06445312]), textin=CWbytearray(b'aa 93 7d 51 04 5e a5 4a 1c 2b bb 21 ac 78 0d 34'), textout=CWbytearray(b'f3 5a 5e 11 65 67 bc a2 5b 40 19 47 d7 5a 91 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13867188, ...,  0.0078125 ,
            0.06152344,  0.06347656]), textin=CWbytearray(b'd8 05 ed b4 12 eb ea 7b f6 0f e0 de 24 25 ac b0'), textout=CWbytearray(b'01 03 bd 8a 02 b4 37 1f 59 b0 ea 1b be bb aa 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.1953125 , -0.14355469, ...,  0.00683594,
            0.05957031,  0.06347656]), textin=CWbytearray(b'22 b8 b9 15 ff 14 05 03 b3 62 73 e9 78 3f 9f 7c'), textout=CWbytearray(b'71 31 41 9d 36 f5 02 7f 48 ac 7d 44 cc ed 51 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14550781, ...,  0.01171875,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'f9 df d6 1e c1 4d 8a 72 f6 0e 18 a1 28 59 e8 4d'), textout=CWbytearray(b'f6 66 fe ca 87 8b a4 14 aa 61 e5 59 f1 0d 6b 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19726562, -0.14257812, ...,  0.02636719,
            0.07617188,  0.07617188]), textin=CWbytearray(b'0f d2 b7 c1 b0 b0 86 e9 45 08 2a 32 02 a0 9e 81'), textout=CWbytearray(b'b8 7c 23 80 21 c7 eb 95 f4 35 29 5e a6 f0 6e e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14355469, ...,  0.00390625,
            0.05761719,  0.06152344]), textin=CWbytearray(b'd5 93 4c 9e 56 1f 28 cc 4f f6 47 dd aa 52 06 6f'), textout=CWbytearray(b'5b 6d f1 5b dd 63 4c 2d 2b 30 61 72 c0 be ee d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13769531, ...,  0.02246094,
            0.06933594,  0.07128906]), textin=CWbytearray(b'ad 68 85 66 a0 21 f1 eb e4 1f 55 cf db e3 c2 ca'), textout=CWbytearray(b'cb 7c 8a 80 20 13 9b ea 45 12 ac 07 dc 08 b3 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13769531, ...,  0.00878906,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'0e 45 e6 61 6e 25 ed 3d 5a 84 b3 9a 54 b3 8b a9'), textout=CWbytearray(b'01 1c eb f7 ce 2a 1d 29 65 40 18 29 2d 79 08 e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19140625, -0.140625  , ...,  0.00585938,
            0.05957031,  0.06445312]), textin=CWbytearray(b'fb 56 56 8d 2d 94 fd 90 f9 35 0b c2 b1 35 e9 96'), textout=CWbytearray(b'09 5b c1 03 5f ee d2 fa da 0e 71 70 4b 71 54 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13867188, ...,  0.02734375,
            0.07617188,  0.07519531]), textin=CWbytearray(b'a0 80 cf d1 69 f6 f6 7b ad a8 cf c2 96 d9 85 37'), textout=CWbytearray(b'84 e5 b6 e2 59 c3 86 80 85 39 c3 5f 7c 97 7d 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.14160156, ...,  0.02148438,
            0.06933594,  0.07128906]), textin=CWbytearray(b'51 fa d2 65 98 20 4d 34 b4 bc c2 69 32 a7 69 7c'), textout=CWbytearray(b'c7 c8 b8 d5 b7 50 70 7f 8c 1f e0 e1 2b 86 76 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.13964844, ...,  0.00488281,
            0.05761719,  0.06054688]), textin=CWbytearray(b'38 a2 e9 f5 13 e2 be 17 04 d3 67 c5 eb d5 80 6b'), textout=CWbytearray(b'37 bd ad f4 c3 d5 53 18 28 c5 05 03 e6 73 a8 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14355469, ..., -0.00488281,
            0.05175781,  0.05761719]), textin=CWbytearray(b'79 42 40 fb 17 96 37 0a ce a4 ea db af 4b 90 6a'), textout=CWbytearray(b'4a a6 7d 55 2d a2 f3 9f 8c 2f 11 81 0f f5 d2 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18652344, -0.13476562, ...,  0.00878906,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'df 24 c8 03 25 5d 37 ba fc ed af cb 03 07 ef b0'), textout=CWbytearray(b'13 5e 7d aa 73 5c 7f ee b9 8e 24 cc 38 44 68 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14355469, ...,  0.01074219,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'2a 85 c7 f1 8b 56 d7 dc 9f 55 88 fe 6c 2b 8d 4d'), textout=CWbytearray(b'7d 42 96 c5 2c b1 dc d0 1b 58 8c 22 e8 61 8c c2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13867188, ...,  0.00878906,
            0.06152344,  0.06347656]), textin=CWbytearray(b'0a 58 b3 85 8c 59 99 a5 0c 07 d1 0c ac 34 89 37'), textout=CWbytearray(b'5b 65 44 58 a8 ec f8 46 38 f9 35 70 69 15 98 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13769531, ...,  0.02050781,
            0.06933594,  0.07128906]), textin=CWbytearray(b'4d 1a 60 47 c0 26 a7 27 40 86 da 82 33 e4 9b 31'), textout=CWbytearray(b'c9 ad 97 ff c1 62 1a 18 59 31 e2 95 f5 bc 79 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19433594, -0.14550781, ...,  0.02929688,
            0.07617188,  0.07714844]), textin=CWbytearray(b'b4 23 df 2b 6c cc d7 7e 1f 5d de c7 c8 5c 9c 22'), textout=CWbytearray(b'55 4f 89 ad 44 c9 49 0c 35 41 f8 bf 3a 12 38 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18652344, -0.13769531, ...,  0.00195312,
            0.05859375,  0.06054688]), textin=CWbytearray(b'3b 8a 0e 11 d1 63 87 a4 36 9d cd 52 a7 34 33 aa'), textout=CWbytearray(b'a1 77 0d 8c 40 08 46 f1 35 f2 4d 90 5b 3a 23 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14160156, ...,  0.0234375 ,
            0.07226562,  0.07226562]), textin=CWbytearray(b'06 02 bc 1e 11 d8 a3 b7 aa a7 1b a0 f7 fc 11 73'), textout=CWbytearray(b'a6 cd 2c 66 5d a9 7d da d9 b4 fe 4a b4 a3 87 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19335938, -0.140625  , ...,  0.01171875,
            0.06347656,  0.06542969]), textin=CWbytearray(b'5d c2 65 2f 0a 67 a0 02 21 e2 2b 54 4b e7 ea 61'), textout=CWbytearray(b'ca a8 65 c5 7f bd a6 36 b5 2a 56 3c 20 7b db e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18066406, -0.13867188, ...,  0.01171875,
            0.06445312,  0.06542969]), textin=CWbytearray(b'10 b6 e2 16 4e 01 53 77 46 87 8d 33 f6 ba b6 f6'), textout=CWbytearray(b'30 c3 fb 3a 19 9e 93 72 f7 6b 10 99 85 41 28 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18554688, -0.13476562, ..., -0.00097656,
            0.0546875 ,  0.05957031]), textin=CWbytearray(b'99 9d a7 20 9e e1 cf 27 85 20 84 8c bb e8 fd b3'), textout=CWbytearray(b'53 4d d6 35 57 dc 71 0a 32 f3 40 fc 61 6b 19 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14257812, ...,  0.02148438,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'9b 44 9e 13 64 fb ce 4d fe 32 89 1c 04 29 b6 7b'), textout=CWbytearray(b'08 0b fd 3d 8c fa 76 10 7b 4c 8c d7 9f 4e 04 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.14257812, ...,  0.01269531,
            0.06347656,  0.06835938]), textin=CWbytearray(b'63 68 09 e2 e6 02 7e 36 0d 2f 30 ed 79 d8 ba 7c'), textout=CWbytearray(b'24 5f 56 37 5e fc fe 3b 10 cf d9 a5 64 a6 20 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13769531, ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'c4 58 0d 53 30 9c c5 45 94 a1 b3 72 60 80 2e c6'), textout=CWbytearray(b'40 68 9c 4f fd f3 59 f0 34 9a 89 2c 88 95 74 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18457031, -0.13769531, ...,  0.01367188,
            0.06445312,  0.06738281]), textin=CWbytearray(b'5a ae cd af 60 c2 dd 47 86 6e 81 a4 77 e3 24 cc'), textout=CWbytearray(b'6e ec 09 8c 79 c8 d3 48 e1 8c 77 25 78 b5 33 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13867188, ...,  0.01367188,
            0.06152344,  0.06542969]), textin=CWbytearray(b'36 2b ab 8c a9 58 6c 5d 0e 0c f2 97 17 93 3c c5'), textout=CWbytearray(b'51 25 16 40 af f0 a8 66 b6 fe 5a de 65 b5 e3 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18652344, -0.13769531, ...,  0.01757812,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'2b b6 25 9d e4 69 31 e0 05 b8 9a 53 18 42 04 c5'), textout=CWbytearray(b'9f 7d 48 2d 0a 63 31 4e ab 21 b8 79 05 55 b0 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18652344, -0.13574219, ...,  0.01074219,
            0.06445312,  0.06542969]), textin=CWbytearray(b'c7 e9 0e 60 0b b6 e8 77 9f 0a 14 3b e6 53 bc dd'), textout=CWbytearray(b'c7 4d a6 7b 3c f5 14 40 82 b4 91 58 6a 1b c0 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14257812, ...,  0.00683594,
            0.05859375,  0.06445312]), textin=CWbytearray(b'81 dc 2a f4 ce f2 76 b2 42 6a fb 58 36 87 00 2e'), textout=CWbytearray(b'cc 25 53 2f 6e ac a7 de 35 60 e8 7b b9 dc 4a ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13867188, ...,  0.00390625,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'd6 fb e9 6e 7e fb 06 a9 84 eb 9e e0 5f 7d 86 f8'), textout=CWbytearray(b'82 ae 4b 87 4d af d7 18 ea da a8 f6 8f a7 8a 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.00488281,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'19 9d 48 48 e2 4c 6a 39 a3 1a c7 dc 52 1e c6 63'), textout=CWbytearray(b'c0 50 f7 84 ee 90 9d 87 9b 13 98 ad 6b 66 8c 83'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.18847656, -0.14648438, ...,  0.01269531,
            0.06347656,  0.06738281]), textin=CWbytearray(b'd6 73 70 36 21 16 ef 05 ba e3 64 04 12 0f 59 1d'), textout=CWbytearray(b'37 3a 34 cb 8a ee 95 9c e2 08 92 7d 6e a0 ae a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.13867188, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'ff 81 8e 04 78 53 78 4d ad 03 97 b1 ea 3f 86 eb'), textout=CWbytearray(b'10 a9 a0 45 2f d4 1e 50 f5 ce 43 91 37 36 60 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.13964844, ...,  0.00390625,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'ba 00 c1 d2 1d 9c 2c c2 b1 39 30 8d 28 58 13 e0'), textout=CWbytearray(b'11 4b a5 51 6b 74 03 2e 64 e6 cf 49 ba 95 4f 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13964844, ...,  0.00292969,
            0.0546875 ,  0.06054688]), textin=CWbytearray(b'93 b3 09 2d ce 4a 40 77 8c 0c af 80 4b f7 90 c3'), textout=CWbytearray(b'c2 e9 77 71 57 3e 05 2f 30 50 a5 5c c3 c6 b2 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13769531, ...,  0.01074219,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'4e f7 f0 cc 99 dc 13 7f 74 05 01 a8 a8 a8 19 ee'), textout=CWbytearray(b'f7 3c 0d e7 31 97 52 59 cc 1c 87 7b d6 39 af dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.140625  , ...,  0.01269531,
            0.06445312,  0.06738281]), textin=CWbytearray(b'a9 d7 e6 18 df 6b ea f5 ba ee 45 b8 4a 94 47 3c'), textout=CWbytearray(b'97 ff d5 43 ae 9b 0f 39 d8 2b 36 6c e9 23 30 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13769531, ...,  0.01855469,
            0.07128906,  0.07128906]), textin=CWbytearray(b'1b f5 01 e9 44 12 5a 8c 24 63 70 a6 23 59 34 c3'), textout=CWbytearray(b'2c c1 55 c3 cc e8 f4 7a d5 3f ae 6b ef 7b 4c 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.01953125,
            0.06933594,  0.06835938]), textin=CWbytearray(b'ca 56 e8 4f 2f 64 6b a0 04 d8 b8 ba d6 07 41 0f'), textout=CWbytearray(b'f4 c2 2e 66 20 67 dd 9d 91 47 e3 0c 15 9f f6 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.14355469, ...,  0.01660156,
            0.06445312,  0.06738281]), textin=CWbytearray(b'e0 41 e7 74 04 03 13 9b 3d fc 86 f6 5e d5 9a 1a'), textout=CWbytearray(b'46 7d 1b 1d 63 b5 23 24 65 fb 72 f6 c3 28 65 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14160156, ...,  0.01074219,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'07 2e 53 b9 f8 e1 2b e8 87 ef 0f 83 29 1e 37 95'), textout=CWbytearray(b'e0 9c c7 99 ca d5 91 2d 62 25 7b d8 47 98 a5 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.14160156, ...,  0.00390625,
            0.05664062,  0.06152344]), textin=CWbytearray(b'1a ac cc c3 b1 ec 24 22 b2 40 41 09 2c 1f 73 73'), textout=CWbytearray(b'c8 92 a1 c6 a5 8b 18 12 a3 74 8e 82 2b df 42 ca'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13769531, ...,  0.02246094,
            0.06933594,  0.07226562]), textin=CWbytearray(b'7a 42 f4 97 90 fd e5 64 00 d3 66 39 13 87 c4 97'), textout=CWbytearray(b'27 6a 7b e1 26 ab e4 cb 62 ac 2c 43 2e f5 06 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14160156, ...,  0.02441406,
            0.06933594,  0.07128906]), textin=CWbytearray(b'ec 03 67 18 ee 9d c7 3b 08 0b 0f 4a 2e 7a ef 0f'), textout=CWbytearray(b'ea d9 de 1e 35 c5 61 86 bc 8c 75 d3 d5 46 cb 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18261719, -0.13476562, ...,  0.01269531,
            0.06347656,  0.06738281]), textin=CWbytearray(b'ef 6a 39 dc ff 45 2b 99 92 c0 cf ee a4 04 a7 d5'), textout=CWbytearray(b'03 67 f2 a1 59 2c 97 94 64 bb 72 b1 3b 74 a8 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14160156, ...,  0.00390625,
            0.05859375,  0.06054688]), textin=CWbytearray(b'05 9e aa 5b 98 bf a7 fc 49 21 47 30 1b 27 59 50'), textout=CWbytearray(b'de 45 9f 81 63 38 95 96 15 09 16 a4 15 00 66 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ...,  0.015625  ,
            0.06835938,  0.06933594]), textin=CWbytearray(b'86 64 aa e4 98 04 40 88 e4 51 64 e5 62 76 b0 4a'), textout=CWbytearray(b'3c dd 96 92 79 2c 1d 69 c6 88 20 33 3d 54 74 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18164062, -0.13378906, ...,  0.02050781,
            0.07128906,  0.07128906]), textin=CWbytearray(b'21 54 e2 3e 58 66 d8 fd 9c 16 bd c3 a4 31 f5 f3'), textout=CWbytearray(b'45 52 45 3e 08 44 c3 bf 29 7f 85 49 b7 40 d5 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13574219, ...,  0.02441406,
            0.07128906,  0.07519531]), textin=CWbytearray(b'b8 97 f6 f5 5b 54 f3 b8 e6 b9 64 ff 10 bf c1 a7'), textout=CWbytearray(b'fb f0 78 17 64 b8 a7 85 fd d3 76 e5 38 c0 5f f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18457031, -0.1328125 , ...,  0.00878906,
            0.06347656,  0.06445312]), textin=CWbytearray(b'85 55 a9 0d e0 a8 2a 61 cc af b8 f9 9a a2 a6 d9'), textout=CWbytearray(b'b4 b7 1f 91 f6 7a 0f 82 7e c6 5a 5b db aa ae 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14355469, ...,  0.00683594,
            0.05761719,  0.06054688]), textin=CWbytearray(b'02 cf 9d 5f 7f a9 ec 8a 5e 33 3d 4d 62 be c9 2e'), textout=CWbytearray(b'ba 0c db 89 73 ca 02 e0 63 43 10 9b 90 ac 95 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14160156, ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'3f 1c 0b 76 b1 4a 5d 24 16 7a 04 13 ae 1d 46 8c'), textout=CWbytearray(b'22 12 d4 0b db 35 2b 88 ec 7a d8 58 8e b0 f9 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.00195312,
            0.0546875 ,  0.06054688]), textin=CWbytearray(b'cc 71 27 3e 63 ae e5 31 d4 4c e0 61 85 67 d2 23'), textout=CWbytearray(b'7d da 43 6b d1 ee 7a 99 04 be 58 2f 56 b2 3d c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13476562, ..., -0.00097656,
            0.05371094,  0.06054688]), textin=CWbytearray(b'4a 1e 99 51 d4 0c 3e 51 f4 59 a9 34 3c e9 d1 e5'), textout=CWbytearray(b'2a d1 34 bc 44 52 d6 c1 6f 69 a9 dd 7d 5d d6 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.140625  , ...,  0.02832031,
            0.07421875,  0.07714844]), textin=CWbytearray(b'01 e7 a8 01 29 d3 79 7e f8 65 fb 68 71 65 89 b0'), textout=CWbytearray(b'2c a4 f7 75 03 f6 36 4e cf 56 62 15 cd b9 ad df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13769531, ...,  0.02636719,
            0.07519531,  0.078125  ]), textin=CWbytearray(b'c3 e8 ba 3a 2e 0d 32 1e 6d 39 31 a7 ef 51 dd fd'), textout=CWbytearray(b'a4 af e6 31 34 32 e1 a5 3e be ea 32 29 10 e1 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14550781, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'61 d7 29 38 3b 5e 86 94 36 2c f5 a7 7d 2e 67 6c'), textout=CWbytearray(b'99 26 1c 3f 06 43 2d 2c e6 41 4e 29 77 dc 03 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18261719, -0.13378906, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'd1 bb 7b a0 26 bd 71 ac 59 24 e9 0f ff 3e c9 e6'), textout=CWbytearray(b'19 ad b2 5f 58 08 96 df 64 d1 9b 8a 96 1b 0b 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13867188, ...,  0.02441406,
            0.07324219,  0.07519531]), textin=CWbytearray(b'c2 e6 f0 23 00 e5 de d1 9e f8 53 ba 56 f0 1e 44'), textout=CWbytearray(b'db 93 a2 0e 0d 7f 7d 81 96 4a b4 fa 6b 8a 41 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ..., -0.00097656,
            0.05664062,  0.06152344]), textin=CWbytearray(b'be 5e 1a 39 8a 2d af b7 72 f6 26 97 47 60 f8 06'), textout=CWbytearray(b'74 33 c0 2f f8 c1 b8 38 b8 b1 b7 0c da 52 b2 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13964844, ...,  0.00683594,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'35 75 a9 8a b1 7d 6f 4a e7 3f e1 f5 9b 89 94 e0'), textout=CWbytearray(b'd3 92 ec 64 3e df c6 92 9e 8f 9f 13 fd 56 53 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.14257812, ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'23 00 a4 17 d9 96 ec 9c 7b b4 9f a2 5f 7d d1 63'), textout=CWbytearray(b'ce 3b 3d a0 1e 56 d0 b9 76 10 13 5b 30 f9 2c 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14160156, ...,  0.01269531,
            0.06542969,  0.06738281]), textin=CWbytearray(b'e1 ab 1b d4 a6 27 d6 93 51 40 1c ff 67 cb 3e 28'), textout=CWbytearray(b'aa 72 b8 f4 2e 3d 7b 9a 06 52 74 86 b7 ff 7b b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18847656, -0.14160156, ...,  0.01367188,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'5d 04 19 09 ea d0 2c 7c 36 cf 25 c4 12 ce 3f 4e'), textout=CWbytearray(b'10 e5 c4 29 81 8d 74 2b fa 58 16 d5 23 97 1f bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13574219, ...,  0.01953125,
            0.07226562,  0.07324219]), textin=CWbytearray(b'ed 1c d0 2f 96 c5 9c c2 2c 19 87 29 5d f2 a2 a5'), textout=CWbytearray(b'12 cd 9a be 85 4d e2 88 f7 a7 a5 11 20 a0 4e 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13769531, ...,  0.01367188,
            0.06738281,  0.06933594]), textin=CWbytearray(b'c9 74 55 f5 b4 28 c6 00 89 97 2e 0c 0c a2 be d8'), textout=CWbytearray(b'96 62 da 6d 76 b2 4a 28 4e 4f 44 1c 82 7e ba 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18945312, -0.140625  , ...,  0.015625  ,
            0.06445312,  0.06738281]), textin=CWbytearray(b'63 50 da 3d 66 03 22 dd c7 cc ad 5b 74 73 15 ec'), textout=CWbytearray(b'fd ba c8 6f e0 4d c8 1c e7 05 49 ea 1d 0c d4 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18261719, -0.13769531, ...,  0.01660156,
            0.06835938,  0.06738281]), textin=CWbytearray(b'13 59 06 49 49 44 43 2a 67 8f 1d fa ad df 04 f8'), textout=CWbytearray(b'29 6c 40 03 52 b4 a1 6b f9 73 ee 4f 57 60 cf 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.14160156, ...,  0.02539062,
            0.07226562,  0.07519531]), textin=CWbytearray(b'f7 27 5a d4 6e 1c 4a c4 f3 9f b9 c5 63 ce 45 55'), textout=CWbytearray(b'8e 25 74 e3 df da a4 9b a1 a0 94 be 93 01 66 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14160156, ...,  0.02050781,
            0.06933594,  0.06933594]), textin=CWbytearray(b'8f 2c 13 41 7e 22 7f 9a 4f 22 20 f3 7a b3 5f 3a'), textout=CWbytearray(b'00 0f bf d7 1a f6 bf ae 30 98 2a a6 f2 26 67 f5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14453125, ...,  0.01171875,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'65 68 82 bc 31 69 46 ff fb ce 72 3b 10 61 79 63'), textout=CWbytearray(b'ab 33 7f b2 e7 ce 74 0e 3e eb 4e f1 f5 00 48 dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13574219, ...,  0.01660156,
            0.06542969,  0.06933594]), textin=CWbytearray(b'7d d6 bd 0e 3c a2 14 16 b9 aa 5d 93 a2 e8 f7 42'), textout=CWbytearray(b'6b bf 53 97 b2 50 02 72 1a 25 40 b6 c1 b7 e0 f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18652344, -0.13476562, ...,  0.00195312,
            0.05859375,  0.06054688]), textin=CWbytearray(b'82 58 9f f2 32 7f 00 b8 ef 28 c6 d3 f3 cc a7 79'), textout=CWbytearray(b'dc a3 da cb c6 94 0f f5 f7 ea 6a 91 d8 cb 14 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13769531, ...,  0.01269531,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'5c 11 4c 12 ae 88 9f 09 e9 19 05 df 10 d1 95 f7'), textout=CWbytearray(b'aa 08 75 ef 26 c7 51 d8 6e d2 4d 02 18 d3 72 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.00878906,
            0.06152344,  0.0625    ]), textin=CWbytearray(b'85 89 78 f4 28 0b c9 81 5d c6 03 77 9b 20 ef 79'), textout=CWbytearray(b'c4 bf d2 1d 42 9f b6 ed 52 f8 4d a3 57 8d f6 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.14453125, ...,  0.02636719,
            0.07226562,  0.07226562]), textin=CWbytearray(b'8b 2b 26 ad 8f 6c ff e8 36 75 d0 e3 48 22 b4 3c'), textout=CWbytearray(b'ed 58 50 da c4 d6 42 d3 8e 6e 95 9e 6d 3f e0 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.140625  , ...,  0.01074219,
            0.06542969,  0.06640625]), textin=CWbytearray(b'ad 20 2d 2d c2 f8 56 91 23 ec 90 b4 27 84 59 48'), textout=CWbytearray(b'e0 22 11 d4 7a 1b 4f 68 ab 14 19 2f 5b 45 84 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13867188, ...,  0.00878906,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'83 37 0b 02 50 2e f7 74 3c db d7 67 7d 58 42 af'), textout=CWbytearray(b'5a d7 5c 66 cd 0a fb 91 36 2b 2d 01 71 45 d1 e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14160156, ...,  0.01660156,
            0.06445312,  0.06738281]), textin=CWbytearray(b'a7 dc 3b 86 8e 89 fa 59 ba 93 af 60 29 b9 85 13'), textout=CWbytearray(b'3f 93 3e 04 4a d7 47 72 1f 9c 18 df 9b d8 9c 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18261719, -0.13671875, ...,  0.01074219,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'7b 2a 9f cf 35 37 fc 35 59 85 c7 0c f1 7f c9 fb'), textout=CWbytearray(b'b3 e0 a5 c1 c0 d7 10 60 b2 4e 74 d2 32 80 2c 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14746094, ...,  0.02050781,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'ec 6f 2d 08 f5 96 8e 8f ca 64 b3 25 1f 91 0c 62'), textout=CWbytearray(b'e0 d1 79 6d 4b 81 ac a0 b6 47 35 96 1d db aa 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19042969, -0.14453125, ...,  0.01171875,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'97 32 be df 23 e6 f4 48 86 a4 87 ae 21 b7 8a 6e'), textout=CWbytearray(b'e9 37 74 90 96 2c bb d0 bb a5 92 d3 7a 9f 3a 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.140625  , ...,  0.02246094,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'00 a1 9e 63 a4 c9 4a e4 fd c9 a6 c4 f9 46 27 3d'), textout=CWbytearray(b'ec bb 72 4e 5e e8 33 db 62 a6 99 dd 97 58 5c f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13964844, ...,  0.0234375 ,
            0.06933594,  0.07324219]), textin=CWbytearray(b'54 08 17 04 5f 17 11 e6 d1 ae 63 f8 c7 db bf 18'), textout=CWbytearray(b'db 83 c6 94 da 5c 2e 0d ed b5 9d 33 82 b3 82 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19042969, -0.13964844, ...,  0.01074219,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'54 52 f9 8c bc 9f 14 6f bf f1 89 d7 ab 77 ef 35'), textout=CWbytearray(b'62 65 cd 53 0a 9e 0c f3 31 aa 74 03 4c 39 f2 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13964844, ...,  0.00488281,
            0.05957031,  0.06054688]), textin=CWbytearray(b'27 be 65 8c 76 a8 0a 8a 40 3c 69 b5 29 86 f4 98'), textout=CWbytearray(b'26 99 f5 b9 ad 6a d8 57 9c b5 61 8c f4 f8 81 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.00292969,
            0.05761719,  0.06152344]), textin=CWbytearray(b'00 26 18 b5 ef 7a 5c 52 51 2c 87 19 14 1a 35 f0'), textout=CWbytearray(b'ad 3f d8 1b d3 bb 38 f8 ea 1a e7 a8 55 4c d2 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14355469, ...,  0.00683594,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'31 27 c8 84 75 2e 34 fa ff 67 65 a3 95 55 62 6b'), textout=CWbytearray(b'e3 32 72 c8 b4 6b 42 ea 9d a7 4f d0 6e f5 6d b3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19824219, -0.14550781, ...,  0.00976562,
            0.06054688,  0.06640625]), textin=CWbytearray(b'b8 e3 05 55 9b ec f2 9c ba 37 19 0f 0f 35 00 13'), textout=CWbytearray(b'78 41 c6 b3 f7 87 9f 5a 75 46 5f 9e 8a c5 06 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.140625  , ...,  0.01269531,
            0.06347656,  0.0703125 ]), textin=CWbytearray(b'94 76 51 36 4b a6 3e 53 5a d6 fa 9c 28 fa eb 73'), textout=CWbytearray(b'7f f5 ee 26 a5 9f 47 5c d8 e5 3f 22 4b d8 a0 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14355469, ...,  0.00878906,
            0.06152344,  0.06542969]), textin=CWbytearray(b'7c d8 d4 f7 a0 b6 14 57 c2 aa c1 2c d6 8a 32 4e'), textout=CWbytearray(b'f5 dc e7 6e c5 b8 ce f9 6b 8f 31 3f 01 af 44 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13867188, ...,  0.0234375 ,
            0.07324219,  0.07226562]), textin=CWbytearray(b'd0 44 8f 3b d6 6b e2 b6 6d 93 25 57 7b a0 8b d2'), textout=CWbytearray(b'e4 da c1 91 42 2e ae e3 ea 01 6d 5d e9 91 90 16'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.14257812, ...,  0.00976562,
            0.06054688,  0.06542969]), textin=CWbytearray(b'7d f3 7d 81 03 6e e2 b8 f3 6e a3 54 d0 9e c7 42'), textout=CWbytearray(b'cc bd 0b ef 10 e7 ed 2a 4f 8b e9 66 51 03 53 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19238281, -0.14355469, ...,  0.00390625,
            0.05761719,  0.06054688]), textin=CWbytearray(b'0e 5f cb 7e c1 18 0d 6e dd 7f b9 b1 46 5e 1c 93'), textout=CWbytearray(b'd9 c0 7c 5c 89 63 93 6c 33 22 fc c6 09 2d 2c 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.13964844, ...,  0.02050781,
            0.07128906,  0.07128906]), textin=CWbytearray(b'61 9a 75 97 86 35 6e a2 31 53 54 41 c5 b9 f6 1a'), textout=CWbytearray(b'73 2a 26 f6 b4 71 09 77 f7 9e dd 91 65 ef 38 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.14160156, ...,  0.02734375,
            0.07421875,  0.07519531]), textin=CWbytearray(b'96 1d bb a4 e9 1d 0f 50 a1 a5 f8 58 5a 37 fb 68'), textout=CWbytearray(b'73 d1 4f 35 45 10 8b 95 bd dd dc 88 ea 96 e1 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14160156, ...,  0.01171875,
            0.06640625,  0.06738281]), textin=CWbytearray(b'fe be a5 96 c1 bb b7 4a 42 23 41 cb 5f 05 12 cd'), textout=CWbytearray(b'13 82 8a ad 74 6b 5d 92 37 ac 62 48 65 de eb 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.01757812,
            0.06542969,  0.06835938]), textin=CWbytearray(b'70 db bf a7 59 90 e7 84 10 b2 07 84 30 18 a1 27'), textout=CWbytearray(b'3f 74 c4 02 9b 83 aa 22 65 f5 03 e7 0a 07 0f ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19140625, -0.14355469, ...,  0.01757812,
            0.06542969,  0.07128906]), textin=CWbytearray(b'11 dc 72 a6 63 68 f9 95 40 02 08 03 5f 10 68 cc'), textout=CWbytearray(b'52 56 3e 42 11 4f 1d 2e 1b b1 84 5f af a6 e2 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14257812, ...,  0.015625  ,
            0.06738281,  0.06738281]), textin=CWbytearray(b'19 78 87 01 9e 13 46 07 1e f2 af 93 ec 4a 37 30'), textout=CWbytearray(b'b4 60 a7 6d fc 46 ce 71 0c 88 9e aa 36 20 67 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14550781, ..., -0.00292969,
            0.05175781,  0.05859375]), textin=CWbytearray(b'30 95 a3 04 02 5f 73 23 82 aa a3 9b db 90 ce 9f'), textout=CWbytearray(b'f8 72 3e 1b b2 3d 2f 43 46 2a 3f a1 51 9e 03 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19628906, -0.14648438, ...,  0.00878906,
            0.05761719,  0.06347656]), textin=CWbytearray(b'8b f9 b2 25 aa 5c b1 82 59 67 3d 4c ae 70 79 9c'), textout=CWbytearray(b'8e a1 31 22 11 c7 db 80 50 ad a6 b0 2e e6 3f b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13769531, ...,  0.00488281,
            0.06054688,  0.06347656]), textin=CWbytearray(b'79 01 f2 c0 8d ef c5 f6 c3 c8 8d a5 04 5e 7d a6'), textout=CWbytearray(b'bc 8e bb 45 54 bc 87 9b 6c 98 0a 8b 86 6e 40 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18164062, -0.13574219, ...,  0.02050781,
            0.06835938,  0.07128906]), textin=CWbytearray(b'14 55 c0 1f 01 5a 50 76 b4 38 b0 71 71 c7 54 a4'), textout=CWbytearray(b'68 28 c0 f7 a8 d5 2a 2e 12 b9 1e dc 3d bb be fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14355469, ...,  0.01171875,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'df 74 da 27 9e e7 d6 e6 8f 55 12 8b 31 7a 06 72'), textout=CWbytearray(b'bc 5e 43 3b 78 52 ee d8 f8 ed e5 87 11 33 5c 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13769531, ...,  0.02148438,
            0.07128906,  0.07421875]), textin=CWbytearray(b'6a 21 5b 22 28 03 d2 0d 81 be c0 c1 11 0f a6 e3'), textout=CWbytearray(b'ee 42 29 e4 81 54 81 20 75 2f 91 07 43 15 44 2f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14257812, ...,  0.01269531,
            0.06542969,  0.06738281]), textin=CWbytearray(b'14 cc 08 9b 40 21 a7 b9 32 35 c6 26 16 b6 cb 4c'), textout=CWbytearray(b'36 cb 1a ed d6 28 cf c9 8d 20 ab 8c c7 f5 61 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13671875, ...,  0.00683594,
            0.06152344,  0.06445312]), textin=CWbytearray(b'42 4d 14 3e ec dd 91 93 cf c6 54 2e be bc 51 d5'), textout=CWbytearray(b'58 2b ef c2 70 ed 1f ea 95 a0 f7 00 48 37 ce 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18261719, -0.1328125 , ...,  0.02636719,
            0.07519531,  0.07421875]), textin=CWbytearray(b'e1 2f 65 d4 0c 2d bd 24 45 29 cc fc e3 ab 35 ed'), textout=CWbytearray(b'2c 34 f0 6b 26 2a 00 ca 47 1f bd a9 11 cb 2c 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13671875, ...,  0.01367188,
            0.06445312,  0.06542969]), textin=CWbytearray(b'8b 0e 56 9c 03 84 08 a2 0e b2 ce 9e 8c a3 14 fb'), textout=CWbytearray(b'fe 4a 0c d0 dc de 93 60 45 b8 c3 8b cd d5 2d f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13867188, ...,  0.0078125 ,
            0.06054688,  0.06347656]), textin=CWbytearray(b'2b 83 c7 ff d3 c7 9f bb f8 66 b0 2b 07 f4 42 02'), textout=CWbytearray(b'fb 87 4a 99 ae 7a 2f 70 e3 51 14 a0 3d 65 27 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.14257812, ...,  0.00878906,
            0.05859375,  0.06347656]), textin=CWbytearray(b'b0 c5 26 cb e8 41 76 b8 a8 05 ba ee 91 b5 cd 98'), textout=CWbytearray(b'bf dc b9 36 88 ab ba 7a 72 58 21 fb b7 48 98 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19335938, -0.14550781, ...,  0.00488281,
            0.05859375,  0.06152344]), textin=CWbytearray(b'0e 56 78 4f ab 87 56 53 72 78 ff 1f 5a 1e 32 8a'), textout=CWbytearray(b'd7 10 fc fb ee fd 66 2f 54 97 2f f8 b6 9a 1d 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.140625  , ...,  0.00878906,
            0.06054688,  0.06445312]), textin=CWbytearray(b'63 e0 6c ad 26 91 6d 4d f3 d8 5f 06 c2 92 15 c5'), textout=CWbytearray(b'0c 22 dc 77 92 f0 1a 5f 21 2b a5 01 99 a3 fb 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19433594, -0.14453125, ...,  0.00488281,
            0.05761719,  0.06445312]), textin=CWbytearray(b'e2 23 26 9a de 78 64 4b e5 58 bf 2e 2a d8 2f 3b'), textout=CWbytearray(b'51 23 8b b2 01 89 bb ba 41 8a e8 5e 7c 1d 3b c3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13769531, ...,  0.00585938,
            0.05761719,  0.06347656]), textin=CWbytearray(b'0e 34 e3 c1 f0 db 3b b2 f1 18 25 ac bc 86 f8 f7'), textout=CWbytearray(b'e8 fb 87 88 8b 02 ce 5e ab 45 eb 8f c0 58 07 2b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13964844, ...,  0.00878906,
            0.06347656,  0.06542969]), textin=CWbytearray(b'7c 23 5a 64 8f 50 b0 96 c7 74 f2 99 4a cd 18 a6'), textout=CWbytearray(b'94 c5 b0 d8 24 b4 c3 08 aa fc bd 68 01 38 8b c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14648438, ...,  0.0234375 ,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'14 89 a3 56 88 ba f0 c2 45 2f 07 fa 1c 58 5c 6d'), textout=CWbytearray(b'6e ba 0b 6e 91 f8 91 af f1 93 e5 39 00 54 85 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.140625  , ...,  0.01074219,
            0.06347656,  0.06542969]), textin=CWbytearray(b'84 35 67 45 26 10 ae bf 99 23 da 2e f9 5c 61 88'), textout=CWbytearray(b'a7 86 d0 7d 25 ef aa e6 93 f6 eb d7 ac c8 f1 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.01953125,
            0.06738281,  0.06738281]), textin=CWbytearray(b'20 4f 0d fd cb c4 fd 22 ec 81 b3 74 de 28 fb 65'), textout=CWbytearray(b'1a c0 e3 72 0a ae 4c ee b7 58 d8 41 69 44 a6 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13867188, ...,  0.02246094,
            0.06933594,  0.07226562]), textin=CWbytearray(b'f7 92 f3 6a 83 ea 4f 2e 89 cd bc cd 37 37 93 05'), textout=CWbytearray(b'd7 7a ec 8e 76 44 4f 60 a9 70 63 26 be 5f 34 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14160156, ...,  0.00390625,
            0.05761719,  0.06054688]), textin=CWbytearray(b'b8 d6 cd 58 1f de ee cc 36 39 54 05 51 fc 1e 7d'), textout=CWbytearray(b'8f 80 9f 82 7a a7 2f 15 a0 05 e5 e4 8c 32 47 d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14160156, ...,  0.01757812,
            0.06835938,  0.06933594]), textin=CWbytearray(b'b7 94 a3 84 47 1b c4 95 a8 b3 fe b6 16 d0 b6 64'), textout=CWbytearray(b'0b 7e 5f 87 de 0a fc a9 8d 53 8b bb e7 32 ef fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13867188, ...,  0.00878906,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'8e fd f4 26 6f 17 9d 16 7a 44 6f d3 00 8a c8 74'), textout=CWbytearray(b'99 d1 b1 59 a1 4e a7 ba a3 b9 24 ed 3c 56 cd e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14550781, ...,  0.0078125 ,
            0.05957031,  0.06152344]), textin=CWbytearray(b'2d 7a ef cd c9 aa 75 39 2b 86 c0 34 3f 60 e2 74'), textout=CWbytearray(b'5a a1 1f bf 24 88 33 1f c3 43 b2 b2 fc cf 19 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14355469, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'12 58 a7 6c ea f9 e9 80 8d 21 3a d8 5d 76 be 16'), textout=CWbytearray(b'4c 30 26 6f ab 9d 5e 52 b3 60 b5 6b 53 15 aa a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13867188, ...,  0.01855469,
            0.06738281,  0.06933594]), textin=CWbytearray(b'4f 06 2f c5 61 27 c3 c5 47 d0 6a bb 7b 6e 72 b1'), textout=CWbytearray(b'11 d0 a4 d9 06 31 78 cf 97 db 44 44 6e 9b d3 a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14453125, ...,  0.01757812,
            0.06835938,  0.06738281]), textin=CWbytearray(b'f1 1b ea 8f c9 8d d6 bc fc ab 09 1c 7d c7 cd 8d'), textout=CWbytearray(b'03 0f 96 60 3b b0 bc b7 f9 97 9f ea f6 29 c6 e2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13574219, ...,  0.01757812,
            0.06542969,  0.06933594]), textin=CWbytearray(b'00 1b e5 7a 79 3d 14 9e 82 5e 85 c7 19 82 a4 f6'), textout=CWbytearray(b'59 e1 fb fe 8b 0e 10 24 bf 1b 60 8c e0 26 d7 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14160156, ...,  0.00683594,
            0.05761719,  0.06347656]), textin=CWbytearray(b'e7 f8 2a 48 5e db 3e 0c 80 37 26 2b 4f 76 37 86'), textout=CWbytearray(b'5d 36 96 6b ce 49 df ba 9d 8f 7d 45 f2 d4 96 f8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13867188, ...,  0.01074219,
            0.06445312,  0.06640625]), textin=CWbytearray(b'58 ea 09 01 f1 11 e9 9a ef 5a 06 dc d6 f3 b0 57'), textout=CWbytearray(b'de 86 99 10 e9 ee c8 3b d1 cd 90 d0 16 ce b4 cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14257812, ...,  0.01269531,
            0.06347656,  0.06738281]), textin=CWbytearray(b'f5 37 a5 c5 f1 84 8b 9b 9b 04 ba e3 1d e0 38 63'), textout=CWbytearray(b'75 71 62 c4 50 cd 89 14 52 66 91 af a8 06 7f a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13867188, ...,  0.02246094,
            0.07324219,  0.0703125 ]), textin=CWbytearray(b'a4 bc 7d 18 0d 9a e9 22 15 4f 2a e2 c4 31 f5 fe'), textout=CWbytearray(b'47 bc 89 2c 55 0d 69 d7 69 26 4c 76 78 5a 5c 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'31 93 87 22 7f f2 b1 9d 02 c5 26 a9 e7 07 c0 20'), textout=CWbytearray(b'd4 08 44 9a 2f 6d bd ed 7d 95 79 09 93 65 57 aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14550781, ...,  0.00585938,
            0.05761719,  0.06152344]), textin=CWbytearray(b'00 c9 40 25 1d f1 d1 fd 70 a9 73 ac b6 a3 7b 7e'), textout=CWbytearray(b'77 8a ca a6 99 51 27 8a db 4f 7c 3e 8a 30 75 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13867188, ...,  0.03027344,
            0.07519531,  0.07617188]), textin=CWbytearray(b'b6 94 f8 75 f6 6e 76 39 82 b9 97 e3 a2 13 17 55'), textout=CWbytearray(b'aa 58 34 dd 74 3e e8 d0 ab 21 1d 21 bd 59 92 ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13964844, ...,  0.00585938,
            0.05859375,  0.06347656]), textin=CWbytearray(b'84 7e c9 64 29 70 84 72 88 2e 4d 25 45 8c 94 d9'), textout=CWbytearray(b'e0 55 70 7e 50 9b a6 c2 a4 51 8a e5 5e 32 32 4b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14355469, ...,  0.00195312,
            0.05566406,  0.06152344]), textin=CWbytearray(b'6d d5 14 05 6e c6 c3 4c 48 7b 9e 63 97 fa 8f 00'), textout=CWbytearray(b'24 c2 36 12 ce b9 35 61 0c 56 bd 24 f8 76 36 a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.140625  , ...,  0.03222656,
            0.07617188,  0.07519531]), textin=CWbytearray(b'90 9c b5 da e0 33 71 ff 66 6e 2b fb e5 35 49 78'), textout=CWbytearray(b'fe a3 aa 8f 1c 36 4c 37 48 ae 30 b4 79 29 37 bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.0078125 ,
            0.05957031,  0.06640625]), textin=CWbytearray(b'4d 84 37 0d e6 e3 14 6e b3 cf 41 c4 08 a3 12 ff'), textout=CWbytearray(b'e2 cf 77 01 93 a3 cf c7 c7 36 7c 25 0a 95 0c aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.13964844, ...,  0.00195312,
            0.05761719,  0.06054688]), textin=CWbytearray(b'ee 0c 20 23 80 51 70 7e 37 86 fe 64 f1 02 50 2f'), textout=CWbytearray(b'ad cf b4 3b 3e 9e 4f db 57 6b e2 59 a7 07 a2 f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19140625, -0.14160156, ...,  0.01953125,
            0.06933594,  0.07128906]), textin=CWbytearray(b'6f d1 33 11 15 11 55 26 89 c5 32 0c 7d 2a bd 90'), textout=CWbytearray(b'c5 ae 2b 2d 19 2b 6c 2f d4 80 d3 e9 d7 13 2e d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.140625  , ...,  0.02441406,
            0.07128906,  0.07226562]), textin=CWbytearray(b'61 1c 7f e5 2b 40 d6 73 b3 29 10 71 26 f8 a4 03'), textout=CWbytearray(b'08 02 3d 1e af 1e 0c 35 23 0d fb d9 7c 1f 2e a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19628906, -0.14550781, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'1c b8 aa c4 aa f1 80 38 5c 31 13 99 44 50 87 9a'), textout=CWbytearray(b'4a 38 89 19 55 a0 e9 21 48 13 f9 1e c7 2a 74 bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13964844, ...,  0.02148438,
            0.0703125 ,  0.06933594]), textin=CWbytearray(b'25 f3 2e 81 8d eb 54 68 c3 3f 22 1d 8a cc 90 fb'), textout=CWbytearray(b'85 1e 79 4f 05 e0 a9 c6 7d 96 91 0a 3d 60 2f d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.140625  , ...,  0.015625  ,
            0.06542969,  0.06738281]), textin=CWbytearray(b'0a 1e f5 2e 3c b4 f8 56 e1 5d b0 c7 0e be fa 24'), textout=CWbytearray(b'e5 dc 24 08 06 33 a9 68 0c 45 e7 15 d5 e0 a0 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.13964844, ...,  0.03222656,
            0.07617188,  0.07519531]), textin=CWbytearray(b'9a f8 d0 f8 87 3d fe b3 d9 82 2e f9 fc 47 0b 5e'), textout=CWbytearray(b'bd 5e 08 b6 64 3e d9 f2 71 57 c0 9e 41 b9 2f 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18261719, -0.13476562, ...,  0.01953125,
            0.06835938,  0.07128906]), textin=CWbytearray(b'de 78 a6 76 eb 52 f1 99 48 d0 f7 dd f1 1b ec b1'), textout=CWbytearray(b'84 a1 a8 f3 12 d4 16 b3 4b 90 ae c9 53 c8 92 ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18261719, -0.13476562, ...,  0.01367188,
            0.06347656,  0.06738281]), textin=CWbytearray(b'ea 5b 81 02 0d f7 6d 78 f2 13 f0 e7 7b cf f6 e1'), textout=CWbytearray(b'49 69 24 5c a9 8b 20 ca bc 3c cc 36 60 f9 4d d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14257812, ...,  0.00488281,
            0.05957031,  0.06445312]), textin=CWbytearray(b'7b 19 48 14 53 eb 49 c5 2d e3 f3 50 71 bd 40 49'), textout=CWbytearray(b'22 0f 01 17 88 b1 db 3c b0 48 78 1c 17 09 ce e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.140625  , ...,  0.01464844,
            0.06542969,  0.06835938]), textin=CWbytearray(b'c8 09 72 81 d9 ad 90 74 8e db 47 40 f0 97 ca aa'), textout=CWbytearray(b'4d 1e a8 66 c2 cf 2d 3c 71 d0 25 56 16 b0 0e 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.14160156, ...,  0.00976562,
            0.06347656,  0.06445312]), textin=CWbytearray(b'c4 16 94 0c c5 5d d9 25 80 b9 7c d8 2c 63 5d e6'), textout=CWbytearray(b'5d 65 e5 02 2e 58 7a 57 4f a8 df 72 b1 c2 2a a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14453125, ...,  0.01171875,
            0.06054688,  0.06445312]), textin=CWbytearray(b'c2 ee c4 46 91 29 2f 1e 35 bc 3e ee 25 2c 9f 1a'), textout=CWbytearray(b'04 e0 5a 00 e2 95 54 3e 13 be e2 f3 2c a6 01 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19238281, -0.14355469, ...,  0.01269531,
            0.06542969,  0.06933594]), textin=CWbytearray(b'00 1c 17 d4 38 e1 18 b9 5e e4 e0 4f f1 96 50 2e'), textout=CWbytearray(b'c6 1c 86 4a f7 7f 3f ba a4 11 57 25 3f 0d 4f 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14160156, ...,  0.00585938,
            0.06445312,  0.06445312]), textin=CWbytearray(b'fd ad 4d a7 17 e8 37 57 be 00 09 9e de f0 5c 52'), textout=CWbytearray(b'9a 8c 47 c4 ed 90 ff 59 ae 1e b0 0e f5 12 27 bd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13574219, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'c6 6f ac 55 74 44 ec eb 16 a8 79 79 5b e0 e3 f7'), textout=CWbytearray(b'd9 4f 0a a0 ba 44 1d 8d fa 43 0b 66 d7 74 45 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14355469, ...,  0.01074219,
            0.06542969,  0.06640625]), textin=CWbytearray(b'3e e0 ee 5f 52 4e 88 e9 65 51 78 95 b3 39 14 89'), textout=CWbytearray(b'f3 cd be c7 c5 ac a8 10 6f 94 ad 42 52 ad 75 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18457031, -0.13769531, ..., -0.00390625,
            0.05175781,  0.05664062]), textin=CWbytearray(b'8c 3e fa 50 ee 42 48 e6 c3 1e cd 23 96 76 2d c6'), textout=CWbytearray(b'20 e2 72 49 67 37 5a 3d 78 c0 16 04 d1 19 7c e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13769531, ...,  0.00878906,
            0.05957031,  0.06347656]), textin=CWbytearray(b'40 d4 73 db d1 98 c9 9d 90 83 64 57 a4 86 d8 e2'), textout=CWbytearray(b'fb 0e de 33 c6 ff c4 bb 50 76 b4 f4 83 c0 cf 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14160156, ...,  0.02441406,
            0.07324219,  0.07421875]), textin=CWbytearray(b'19 4f 00 55 75 4b e4 cb ed 0e 60 6b 85 cb 4c bf'), textout=CWbytearray(b'29 40 ca 94 9e d0 72 72 c9 6b 5b b8 64 c4 7f 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19140625, -0.140625  , ...,  0.01855469,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'75 4d be 9c 74 ce d0 40 f4 d4 c7 8d 71 1a 4e 90'), textout=CWbytearray(b'2a 14 d1 07 3a 84 61 5b 8a 43 8f 12 b3 d4 3c b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18652344, -0.13574219, ...,  0.01367188,
            0.06445312,  0.06835938]), textin=CWbytearray(b'56 53 70 7d ec 0e ab 9f 5d 28 d6 58 e7 6a e2 72'), textout=CWbytearray(b'8c 9d 2a b0 e7 ab 37 b6 0d 21 a2 00 e0 29 1e 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.14160156, ...,  0.03125   ,
            0.07910156,  0.07910156]), textin=CWbytearray(b'20 a0 ff 9c 08 e6 36 f5 52 40 01 1c ef 59 5f b9'), textout=CWbytearray(b'ce e8 f7 7f 95 30 64 62 71 e5 01 f3 59 01 48 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.01660156,
            0.06738281,  0.06933594]), textin=CWbytearray(b'b0 de ec e5 17 76 eb 11 06 30 f4 74 58 41 29 76'), textout=CWbytearray(b'f7 35 05 6d 9e 84 02 af 95 0c 3f 7c 12 fe ca 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ...,  0.0078125 ,
            0.05957031,  0.06640625]), textin=CWbytearray(b'74 69 00 ce dc fd 1b 87 5a 53 a0 c2 15 bc ac 93'), textout=CWbytearray(b'26 b4 9f 99 ca d7 d8 7c 2f ec 02 d6 10 8d 1d 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18652344, -0.13476562, ...,  0.00878906,
            0.06054688,  0.06542969]), textin=CWbytearray(b'40 6e a5 ff ed 04 a9 2e f8 d0 57 48 b1 a0 f3 ea'), textout=CWbytearray(b'5c 82 24 e6 d4 0a 8d bf ac 18 f4 6c 0b 46 f2 be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.140625  , ...,  0.00976562,
            0.06445312,  0.06738281]), textin=CWbytearray(b'4e e0 8f 1d 28 e2 95 42 95 fb 0c c5 c3 d5 31 4c'), textout=CWbytearray(b'36 42 cf 1d f3 e5 09 a4 cf e6 17 39 92 0f 80 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13574219, ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'9e d5 91 a1 ce 7a 2b f6 68 5a 32 6b 87 bd df a9'), textout=CWbytearray(b'dd 1d 9a e0 67 04 01 04 fa cb 25 ca e8 97 c4 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18457031, -0.13964844, ...,  0.02050781,
            0.06933594,  0.07128906]), textin=CWbytearray(b'91 42 07 57 4f da 2e f0 76 d3 ac dd a6 54 19 ba'), textout=CWbytearray(b'52 ed ee a3 f1 7b 21 b2 a5 3d 2a 06 84 6c 03 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.14355469, ...,  0.01269531,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'ac 17 ca ea d3 d2 9c d1 f2 75 ae 27 11 3c d7 69'), textout=CWbytearray(b'c5 90 c1 ca 37 45 38 31 73 45 df 28 85 df 6b 5a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14550781, ...,  0.02148438,
            0.07128906,  0.07324219]), textin=CWbytearray(b'fe 03 24 ea 6e b3 44 68 3c 81 7d 82 49 d8 2b 31'), textout=CWbytearray(b'65 88 31 85 53 94 9a 1e d2 d2 7d f4 95 4d 57 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13671875, ...,  0.01074219,
            0.06152344,  0.06640625]), textin=CWbytearray(b'5c b2 62 70 a6 e5 d0 2b 48 e9 2c 5e f1 48 65 c8'), textout=CWbytearray(b'dd 9c 0e 1a 94 d2 2c 40 75 41 88 e2 da 69 7b ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14160156, ..., -0.00292969,
            0.05273438,  0.05957031]), textin=CWbytearray(b'a7 bf fd ac 90 f7 0e 78 c5 ad e7 b0 0e 1f a8 34'), textout=CWbytearray(b'67 0d 74 f7 03 95 00 e5 4e a0 f9 94 50 42 42 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19628906, -0.14648438, ...,  0.00976562,
            0.06152344,  0.06738281]), textin=CWbytearray(b'95 ae ae 9a 4c 83 66 1c 0e 69 57 73 3f 40 6e 00'), textout=CWbytearray(b'6a 24 8a ed 52 03 ae 69 94 fb ae 60 d2 99 bc 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.1953125 , -0.14453125, ...,  0.02636719,
            0.07226562,  0.07519531]), textin=CWbytearray(b'1a d8 d4 f9 ad 1c 1e 46 96 f1 ac 9a 6f 22 05 7b'), textout=CWbytearray(b'68 eb ed 37 98 be d8 b9 c6 6f 40 da de 17 24 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.01855469,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'e1 dd 85 43 86 22 69 78 34 ff 7d 59 e8 de 69 03'), textout=CWbytearray(b'cb 18 d8 7b b5 50 39 24 3e a4 91 6e 2e e3 f1 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14453125, ...,  0.0234375 ,
            0.07226562,  0.07324219]), textin=CWbytearray(b'79 92 73 ac 34 2f 05 28 3a 36 ea f3 ae 4b 6b 18'), textout=CWbytearray(b'31 ff f6 b5 5d b3 23 8f 87 84 25 62 41 43 93 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.140625  , ...,  0.        ,
            0.05566406,  0.06152344]), textin=CWbytearray(b'ee 39 09 21 25 06 4a be 63 63 92 9f 17 12 05 23'), textout=CWbytearray(b'11 49 a0 c2 bb 13 1a 97 06 cb 76 16 c6 2b 96 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18652344, -0.14257812, ...,  0.01660156,
            0.06835938,  0.06835938]), textin=CWbytearray(b'0d 69 97 01 21 77 27 de c1 90 ee f3 29 d4 82 70'), textout=CWbytearray(b'89 8b bf 63 69 be 3d bb 48 db f0 7d d0 1e cf 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00683594, -0.19433594, -0.14550781, ...,  0.02441406,
            0.06933594,  0.07519531]), textin=CWbytearray(b'3d 2f fe b4 d8 c6 af 8f b3 ff 5b 3b c6 67 96 9a'), textout=CWbytearray(b'90 1d bd 6d c2 57 21 c0 a0 26 9f 7c c3 95 7d 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.13964844, ...,  0.01660156,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'88 92 75 fc 9f a3 f8 52 32 e5 94 a8 59 aa 37 e6'), textout=CWbytearray(b'f4 95 ba c9 64 e5 6c dd 1a 84 c0 c5 52 68 0e 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18554688, -0.1328125 , ...,  0.01074219,
            0.06054688,  0.06542969]), textin=CWbytearray(b'f5 8a dd 3d 85 89 3b ca 8d 47 03 fb 58 79 f8 f8'), textout=CWbytearray(b'a4 d7 34 60 82 14 44 63 07 76 d0 96 c6 fb 14 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13964844, ...,  0.01464844,
            0.06445312,  0.06738281]), textin=CWbytearray(b'85 96 19 e9 61 14 25 19 c1 5d 87 a7 15 e2 be d1'), textout=CWbytearray(b'0b 4a 1d 45 c3 3e bb a4 63 1b cd 83 93 71 08 af'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.140625  , ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'72 33 21 04 ce 49 fd 09 d3 cc 4b 39 ac f2 28 59'), textout=CWbytearray(b'90 73 be 07 56 00 d2 ea b5 3d 51 fc 7b b0 0c 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14257812, ...,  0.02441406,
            0.07128906,  0.07128906]), textin=CWbytearray(b'eb 5a 67 ff 0a ed 56 d2 5c fb 10 95 a2 81 6c 6f'), textout=CWbytearray(b'4e 69 e0 10 0b 4c 3a 3d 01 f8 46 5d 31 7a 71 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14550781, ...,  0.00976562,
            0.06347656,  0.06640625]), textin=CWbytearray(b'4f 00 21 ab 6f 31 cd ec 6f 5a 05 a8 1d 41 ab 70'), textout=CWbytearray(b'f8 5a ab d6 dc c2 a1 da 79 21 9d 3f 2b b8 83 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.14160156, ...,  0.015625  ,
            0.06738281,  0.06738281]), textin=CWbytearray(b'aa d7 80 df ba e4 be d9 eb 40 81 4f 34 66 2d 22'), textout=CWbytearray(b'92 ed 76 c6 6b 99 10 9c e2 5b 8e 9b ad 73 ed 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.14160156, ...,  0.00488281,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'09 03 e9 da 8f d5 92 fe 53 4a 74 1b 38 3e d2 a9'), textout=CWbytearray(b'34 3a 93 fb 69 a7 b7 03 b8 5b 5a 93 f4 01 4b 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13769531, ...,  0.02050781,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'85 39 1b e9 38 51 0c ac 41 22 14 23 5c db 13 d4'), textout=CWbytearray(b'79 fb b4 fd 50 65 ba dc 1a 5b b3 c0 a6 b0 ea 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18652344, -0.13378906, ...,  0.0234375 ,
            0.07421875,  0.07519531]), textin=CWbytearray(b'61 87 8f d5 2f 43 c0 bf 81 0e f2 07 5d ff 69 f1'), textout=CWbytearray(b'2d 22 9f 11 6e fc 14 fa e1 24 a6 a6 2e 42 0a 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18066406, -0.13671875, ...,  0.00683594,
            0.05761719,  0.06347656]), textin=CWbytearray(b'4a 60 d7 92 e5 17 82 41 3a 4b 1e 13 5b 0e be f1'), textout=CWbytearray(b'0b c8 34 7f 35 7f 36 22 6a 62 69 e3 2e 4d 2a b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19238281, -0.14355469, ...,  0.01367188,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'20 78 49 1b cd e0 0a 07 f8 b4 f9 4c 27 8f 59 4e'), textout=CWbytearray(b'95 78 c9 d7 06 a3 93 e3 5f 21 06 56 42 fe c4 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14257812, ...,  0.01855469,
            0.06933594,  0.06933594]), textin=CWbytearray(b'fa 5e 94 ec 92 a0 70 73 dc 0a be cf c6 90 4c ca'), textout=CWbytearray(b'83 5c ab 60 bc ea ad 6d 2b 71 83 0b 0d 3d 21 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14453125, ...,  0.01367188,
            0.06640625,  0.06835938]), textin=CWbytearray(b'6f 06 2d ab f2 bf 0b 0d fc f8 bf 31 1d 60 ff 97'), textout=CWbytearray(b'bf ec 34 e8 d4 59 9e b8 d4 70 11 5a c9 b6 e7 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19140625, -0.14257812, ...,  0.015625  ,
            0.06542969,  0.06640625]), textin=CWbytearray(b'4a 28 a8 6e 11 a5 05 08 62 cb 71 6a cc 69 61 92'), textout=CWbytearray(b'd7 5a c2 00 df 42 e1 28 c5 0a e5 b1 37 aa 54 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.19238281, -0.140625  , ...,  0.0078125 ,
            0.06445312,  0.06445312]), textin=CWbytearray(b'eb 33 1e d5 64 c5 61 0e 49 a2 97 6a eb f8 bf 4d'), textout=CWbytearray(b'85 44 12 a5 cb 2b be 75 3b 8c 5d 60 88 72 de 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19335938, -0.14355469, ...,  0.01855469,
            0.06542969,  0.06835938]), textin=CWbytearray(b'6b a3 2d 73 b5 c3 a5 a0 35 20 a2 e7 77 88 5e 70'), textout=CWbytearray(b'46 53 0d 08 d9 29 13 98 35 43 b6 02 c7 60 ea 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14355469, ...,  0.01464844,
            0.06738281,  0.07226562]), textin=CWbytearray(b'5b 5f 03 f3 b5 4c 9e ff 14 37 e2 75 1c 7d ac 9c'), textout=CWbytearray(b'30 4c 41 07 bb bb 24 07 7d a5 9e c3 24 17 96 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13867188, ...,  0.01074219,
            0.05957031,  0.06542969]), textin=CWbytearray(b'ae a8 29 9b f2 95 e4 c4 63 a3 e9 90 12 24 ec 14'), textout=CWbytearray(b'65 7e 22 8c c6 70 13 b8 2c 35 78 6d 1d 8a 57 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13671875, ...,  0.01464844,
            0.06445312,  0.06835938]), textin=CWbytearray(b'60 05 a0 32 7c 74 55 31 cf e6 de 0a d2 95 14 a2'), textout=CWbytearray(b'e4 7e a7 33 23 e7 02 da 1a 7b 2a dd cd 9a 96 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19238281, -0.14648438, ...,  0.01757812,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'5b 3f b0 4b e4 f2 ec 1d 3d 23 63 b3 74 10 5e 3e'), textout=CWbytearray(b'f2 bd f7 d5 16 b2 86 ca 69 59 f1 0b 00 47 40 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18554688, -0.1328125 , ...,  0.01953125,
            0.06640625,  0.06933594]), textin=CWbytearray(b'77 8e 3d 73 71 c1 09 23 19 c9 a9 2d 1c 37 d7 d2'), textout=CWbytearray(b'ad 3d de 10 b9 b6 b8 61 ac 63 c4 95 1e 4a 49 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14355469, ...,  0.01464844,
            0.06347656,  0.06933594]), textin=CWbytearray(b'd4 ce d3 6d 83 c8 41 db fa 8b 84 d1 ae ce 84 7d'), textout=CWbytearray(b'15 c2 51 b1 ee 75 61 e7 c4 3a a3 cc 4b 5c f2 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13769531, ...,  0.03125   ,
            0.07519531,  0.07617188]), textin=CWbytearray(b'df a0 52 1b 06 59 2f 25 95 c9 72 90 a5 d4 70 b5'), textout=CWbytearray(b'd8 c2 70 7f c3 25 e0 0d 67 0c 9a 9d c7 ae a7 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.1796875 , -0.13183594, ...,  0.01757812,
            0.06640625,  0.06835938]), textin=CWbytearray(b'a0 8e 5e 4a 05 11 fa 1d 9c bd 4d c4 58 e2 a2 f6'), textout=CWbytearray(b'35 14 a8 cb 93 f9 6d 45 6c e7 a7 a9 d3 ab 74 ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13574219, ...,  0.02636719,
            0.07421875,  0.07324219]), textin=CWbytearray(b'77 50 50 91 07 ce a3 82 31 d6 db be 2e 5e 76 c2'), textout=CWbytearray(b'53 0e 94 89 06 2b 0e 59 ca 6e 61 de b1 7c 3e 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14160156, ...,  0.00195312,
            0.05566406,  0.06054688]), textin=CWbytearray(b'36 28 b7 21 a6 b7 c7 58 6e 51 2c f2 19 dd 4e 63'), textout=CWbytearray(b'70 8a e4 15 04 7a 18 81 f3 54 f8 5d 10 23 62 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18261719, -0.14160156, ...,  0.01074219,
            0.06542969,  0.06542969]), textin=CWbytearray(b'66 3f 83 2c ef 5a f3 58 bb c3 07 9a f8 a5 9b b0'), textout=CWbytearray(b'e3 5e 4d cc 40 bf 05 25 77 0b 95 2e 60 63 3d ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19433594, -0.14257812, ...,  0.00683594,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'6c 67 f2 1a c7 98 6f f5 9b b0 53 d4 33 c6 03 6a'), textout=CWbytearray(b'16 20 59 5b 1a 74 c0 62 d2 e9 25 9a c7 97 01 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.14160156, ...,  0.01953125,
            0.06738281,  0.07226562]), textin=CWbytearray(b'f3 6a 5b fe 72 ff d3 fd 01 c8 36 aa 0a 80 e1 7d'), textout=CWbytearray(b'a3 9d 3b 9c 48 59 c0 5e b5 19 c4 f5 88 cc 7e 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.19238281, -0.13964844, ...,  0.01074219,
            0.06152344,  0.06542969]), textin=CWbytearray(b'4a 92 98 77 fd 6d 26 23 70 84 f2 c1 ea 67 ea f0'), textout=CWbytearray(b'75 f0 b7 02 30 c6 de bc 11 e7 6f d3 2e e2 99 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13574219, ...,  0.01074219,
            0.06054688,  0.06542969]), textin=CWbytearray(b'94 bf 97 4a 11 4e b5 61 1b 36 b6 cc f2 4b 80 b2'), textout=CWbytearray(b'05 b1 a7 50 43 53 91 dd 7a 5a ee d1 17 72 01 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13867188, ...,  0.00976562,
            0.06054688,  0.06542969]), textin=CWbytearray(b'63 75 61 67 f4 a3 92 ba d4 ec 74 d0 f2 9e fb 31'), textout=CWbytearray(b'72 e5 e5 1d 32 d4 45 f7 ee 23 f6 47 89 9c 2d 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.14257812, ...,  0.02734375,
            0.07519531,  0.07617188]), textin=CWbytearray(b'ce a0 7d 6e 48 ce 8b 49 6d f8 9a 24 6d b3 18 3d'), textout=CWbytearray(b'35 c8 d6 68 8f 78 23 af e6 31 29 bb 2d 94 3a 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13867188, ...,  0.01953125,
            0.06738281,  0.07128906]), textin=CWbytearray(b'47 6c ba 84 3a b0 f7 8a c0 0e db b6 af f9 43 10'), textout=CWbytearray(b'3c d6 0a 15 14 05 5d 12 32 0c 53 ce ab 31 36 00'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14453125, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'85 dd b8 7a b7 5b 36 2c 2b 5a 57 ce 00 91 de 7c'), textout=CWbytearray(b'73 90 b0 34 05 a7 fe 7f 7d 19 75 e5 21 fe a2 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14355469, ...,  0.01074219,
            0.06542969,  0.06542969]), textin=CWbytearray(b'ca ce dd e9 20 7c d4 e7 73 ca fc 5f 08 15 dd 95'), textout=CWbytearray(b'62 b9 c9 b5 17 48 53 7a 52 86 ba b8 d2 3f a0 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1875    , -0.13574219, ...,  0.01660156,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'61 3a 4f 94 64 e7 e7 65 11 12 7c d1 0d 8a 07 c3'), textout=CWbytearray(b'55 7a 5d 16 82 43 51 ee 2d 88 a6 65 60 ef bf 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13964844, ...,  0.03222656,
            0.07617188,  0.078125  ]), textin=CWbytearray(b'ef 36 26 17 d1 c8 85 6b 73 e2 de b0 3e dc b0 55'), textout=CWbytearray(b'5c 6e e8 83 ec 27 6f 0a 9f a5 81 3d bb 83 79 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14160156, ...,  0.01757812,
            0.06835938,  0.07128906]), textin=CWbytearray(b'a3 9c df 4b dd aa 6b fd fe d0 54 0b 48 2f 58 84'), textout=CWbytearray(b'45 90 35 6e 12 52 d3 4c 94 a0 6b e3 1e 3b 5d 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14355469, ...,  0.02441406,
            0.07128906,  0.07324219]), textin=CWbytearray(b'60 18 fd 47 21 60 3d 63 99 ba 57 25 8e 23 41 ef'), textout=CWbytearray(b'01 8e ce c1 6a 14 1e 38 1d 20 c3 5d ab 2a 05 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.14160156, ...,  0.01269531,
            0.06445312,  0.06542969]), textin=CWbytearray(b'6a 64 e9 67 49 75 07 8d 90 e8 c1 a2 ff 8f 70 0f'), textout=CWbytearray(b'ce e3 07 56 4f ff 66 97 08 a1 43 40 2e 8e dd b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.13964844, ...,  0.02929688,
            0.078125  ,  0.07617188]), textin=CWbytearray(b'ec 94 ef 2f fb 90 7d cd d4 52 d6 5f 26 5b 74 7b'), textout=CWbytearray(b'c9 b0 69 22 17 95 34 59 53 5e 9c f4 dd 44 8d b2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14550781, ...,  0.00390625,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'9c 46 d5 f5 70 72 c7 cc f2 19 78 d7 d5 c7 aa 6b'), textout=CWbytearray(b'65 e2 02 4b 49 04 92 60 7b 4a f4 c3 94 fe b2 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.140625  , ...,  0.02441406,
            0.07519531,  0.07519531]), textin=CWbytearray(b'f0 4f f0 33 fd fa c8 07 b7 a7 3a 38 90 af 66 15'), textout=CWbytearray(b'18 d2 72 05 fe 08 67 b2 f2 5d be cd fd 60 92 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13867188, ...,  0.01367188,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'90 f5 8d 71 18 07 73 0a 22 f4 e8 5a bd de 2b dc'), textout=CWbytearray(b'40 14 63 aa b6 1d b1 69 0f 43 f3 4a c5 31 25 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13867188, ...,  0.02441406,
            0.07226562,  0.07226562]), textin=CWbytearray(b'f2 d3 23 0d 07 72 6b 3c 8a 0f fc cc d5 54 67 f5'), textout=CWbytearray(b'60 86 2a 75 c5 bf 87 96 92 2d b6 55 d8 de e3 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.140625  , ...,  0.01953125,
            0.06835938,  0.07128906]), textin=CWbytearray(b'6f c4 8f d3 53 77 ba 7f 86 8f 60 6f 10 69 9e ca'), textout=CWbytearray(b'53 e6 97 22 2f 2c 0e 1b db d4 ba a4 df a1 35 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18261719, -0.13964844, ...,  0.02050781,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'97 25 23 1e 8e 34 d7 a6 56 98 0d fa 20 25 96 c0'), textout=CWbytearray(b'00 a6 90 ca 36 bd 93 02 af 63 e9 1c c2 0f 68 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13867188, ...,  0.01464844,
            0.06738281,  0.06835938]), textin=CWbytearray(b'ee 46 40 fe b7 00 99 63 36 b6 a3 6f 74 c2 af 95'), textout=CWbytearray(b'09 ef 89 43 9c 8d 09 cf 77 25 81 17 34 6f c1 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13378906, ...,  0.01171875,
            0.06347656,  0.06738281]), textin=CWbytearray(b'2b ab 15 88 37 cb 30 9a 89 66 84 24 ef 35 58 cc'), textout=CWbytearray(b'd8 c3 93 81 9c 84 e0 be 68 3f ad 67 c3 eb 1a 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14160156, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'2c d7 87 37 db 19 3a fe 80 9d 0c e3 f5 e1 31 7a'), textout=CWbytearray(b'11 7a 4b 0b 0a 11 7e 50 e2 31 5e 41 cf 0d 81 bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18359375, -0.13378906, ...,  0.00976562,
            0.06152344,  0.06445312]), textin=CWbytearray(b'b3 70 55 a5 bf c0 0e 90 43 7d 5c b2 58 c2 62 a1'), textout=CWbytearray(b'31 b3 73 68 6b bb bb 23 9e 6b db e0 3c f3 98 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14355469, ...,  0.01367188,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'37 74 7f 21 91 7a c7 1a 41 68 93 9a 07 88 ec 6b'), textout=CWbytearray(b'89 1e 8e c0 9a c5 a8 d4 3b 73 ea 76 37 cf 6a d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.140625  , ..., -0.00292969,
            0.05273438,  0.05566406]), textin=CWbytearray(b'92 74 f5 09 0a e4 90 5c 39 4c 58 9d 21 66 91 12'), textout=CWbytearray(b'bc 1b 9e 68 37 69 57 90 9a 07 8a 40 66 50 63 13'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13867188, ...,  0.02539062,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'5c 2b bf 0f 51 b5 6e 6b 5f 95 46 7f 92 35 57 af'), textout=CWbytearray(b'93 9b 16 6f 81 52 1b cb 64 f9 9f ea b1 de 29 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19335938, -0.14550781, ...,  0.01855469,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'ae ea 30 5a 46 7d c3 0b d2 32 38 d3 4d 00 40 4a'), textout=CWbytearray(b'5c 7e c4 19 0b ee d1 8e 09 18 82 3c 8e 79 51 fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14257812, ...,  0.01855469,
            0.06933594,  0.06933594]), textin=CWbytearray(b'cc 0e 31 02 dc 8d 87 45 85 b6 8e cb 44 43 3e 88'), textout=CWbytearray(b'e1 73 18 a6 8f 85 1e 29 04 2b 7b 7c ca 39 cf 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14160156, ...,  0.0078125 ,
            0.06152344,  0.06738281]), textin=CWbytearray(b'4a 2b 5b 7b 48 9b dc a9 d2 5c e7 ef 82 5b 01 94'), textout=CWbytearray(b'14 97 32 fc 5a 1f cc 1e ae 5f 27 27 5b ba 6d 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.140625  , ...,  0.01855469,
            0.06933594,  0.07128906]), textin=CWbytearray(b'99 b7 66 e4 5c 17 e2 ad 9e fb 8b 41 51 b5 1d 90'), textout=CWbytearray(b'fb be a6 e4 86 9a 22 6b 8e 34 6b 56 1e 53 a3 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19628906, -0.14648438, ...,  0.02148438,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'fe a0 d0 fe d4 32 56 54 f9 01 a6 71 4d 19 0e 3a'), textout=CWbytearray(b'4b ad f8 c5 1b 2e 0c d1 3e 16 9b ed f2 52 b3 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18359375, -0.13671875, ...,  0.01269531,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'4f 0d f4 51 dc 7d 8e f3 58 89 14 f7 e5 0a 5e e9'), textout=CWbytearray(b'2c 07 57 53 63 2a b5 6b ef 08 ec d7 62 d4 d5 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13964844, ...,  0.01953125,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'33 e0 20 1a ab bf bf 92 23 02 12 0a 86 46 3a ca'), textout=CWbytearray(b'14 bd 0c d2 40 6c da f5 37 bd be ce ae 64 bc df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.14160156, ...,  0.01757812,
            0.06640625,  0.06933594]), textin=CWbytearray(b'50 c8 e0 b5 a7 86 c6 67 c3 93 96 dc a8 85 c1 70'), textout=CWbytearray(b'98 70 90 54 2e b3 ac e1 46 04 ef f7 f2 98 77 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13769531, ...,  0.01660156,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'7c 81 3e 31 ac b1 10 88 d4 7c b1 d2 ee 28 cb 36'), textout=CWbytearray(b'8c 10 a3 6d a3 16 7c 0f fb c2 96 30 7f 7c 14 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.14160156, ...,  0.03027344,
            0.078125  ,  0.07910156]), textin=CWbytearray(b'b6 b3 92 3b 22 34 99 9c ba a3 8f 4f c2 4f b0 7b'), textout=CWbytearray(b'90 de d9 20 9e 23 91 b5 0d 2a bb 41 a3 c5 1a 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.02832031,
            0.07519531,  0.07421875]), textin=CWbytearray(b'e7 b4 7f b9 5f 1b 87 9c 6c 82 e0 aa 8a 7f 27 f3'), textout=CWbytearray(b'1b 46 a6 10 e9 a4 2f 07 f0 67 c0 1a 99 66 ea 44'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14453125, ...,  0.00390625,
            0.05761719,  0.06152344]), textin=CWbytearray(b'64 e1 1e 0f 81 e5 85 63 fa 2f 24 43 99 14 a6 2e'), textout=CWbytearray(b'80 cb 80 81 bb 22 a0 f2 da 2d b6 26 87 2a 46 43'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13867188, ...,  0.00976562,
            0.06152344,  0.06640625]), textin=CWbytearray(b'e3 3f 49 75 4a 85 de 82 2d b2 ed 10 20 1b e9 ab'), textout=CWbytearray(b'f6 f7 de 14 cf bc a9 eb ea 91 3d 64 8c a7 8a 6b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13671875, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'3a 87 71 09 4e 81 e3 85 3b 3e 12 99 c9 a2 b1 dc'), textout=CWbytearray(b'f3 4e 8d 25 b5 54 f0 3a f4 25 8a dc 04 55 da 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02246094, -0.18164062, -0.1328125 , ...,  0.01660156,
            0.06445312,  0.06738281]), textin=CWbytearray(b'67 e3 18 d0 6e 47 22 21 e6 0f e7 35 20 8e c7 b6'), textout=CWbytearray(b'9b d5 0c c8 92 4e d9 f8 a6 be 77 86 92 35 61 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18359375, -0.13769531, ...,  0.00878906,
            0.05957031,  0.06445312]), textin=CWbytearray(b'53 6a da ff 64 be 62 10 50 cd 94 9d 6f a7 52 d9'), textout=CWbytearray(b'4c 0b 19 96 ed 87 2f 12 55 0f d9 c1 64 83 b6 ed'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01171875,
            0.06152344,  0.06445312]), textin=CWbytearray(b'e0 93 e8 4f 77 60 ac 91 fd b8 b7 22 42 e3 fb 79'), textout=CWbytearray(b'7b ff 9e 36 01 d7 e8 a0 50 f6 f9 31 15 98 37 b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.140625  , ...,  0.02441406,
            0.07128906,  0.07421875]), textin=CWbytearray(b'86 df 2f 83 2d 58 32 fb 9d 59 34 cd ff 8d ee 5c'), textout=CWbytearray(b'7d 97 98 e4 c1 43 98 33 ab 0e a1 8a 6e 6b e0 c5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14453125, ...,  0.01660156,
            0.06738281,  0.07128906]), textin=CWbytearray(b'30 c8 01 ce 2f 75 79 d0 62 19 94 0c 5d bb 5e 37'), textout=CWbytearray(b'14 09 cf 18 a4 ea 17 94 90 ff e8 9a a8 f0 16 dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1875    , -0.13769531, ...,  0.01074219,
            0.06347656,  0.06835938]), textin=CWbytearray(b'4f 31 0f 18 cf 2f c7 f3 d2 d6 39 6e af 82 9a dd'), textout=CWbytearray(b'e2 23 54 90 a7 5b 72 cf d0 cd b3 83 41 4c cb e5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14257812, ...,  0.01074219,
            0.06152344,  0.06640625]), textin=CWbytearray(b'e7 c6 aa 19 3d d8 ca 15 6c 17 cd 38 8f 15 3d 56'), textout=CWbytearray(b'a9 bc 1e b6 6a 2e d4 e7 14 c6 05 5a a1 fa db 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13769531, ...,  0.00390625,
            0.05761719,  0.06152344]), textin=CWbytearray(b'b6 d2 e3 6b 76 c3 75 2d 49 89 14 42 b2 89 ea 6e'), textout=CWbytearray(b'b0 66 30 2f 42 19 d1 00 e0 ed 96 94 2e 50 d2 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.13964844, ...,  0.01757812,
            0.06640625,  0.06738281]), textin=CWbytearray(b'49 ec 96 eb 5d 03 3d 4a 34 ba 58 ce 2c e3 4a 35'), textout=CWbytearray(b'3f 38 08 8e 90 19 f9 84 47 62 d6 7b 8c b1 41 b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13867188, ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'79 44 84 6e e6 02 72 a6 54 32 e4 cd 66 ab d6 86'), textout=CWbytearray(b'ee 62 5d bf 3f 13 a3 0d 11 1c a9 9a 91 e1 d0 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14257812, ...,  0.01464844,
            0.06347656,  0.06640625]), textin=CWbytearray(b'97 6a ac 9a dc 8c 41 57 aa af e2 42 51 6b b1 45'), textout=CWbytearray(b'56 aa 18 92 cd 41 52 f9 c4 2f 10 22 e6 1a 50 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14648438, ...,  0.00488281,
            0.06152344,  0.0625    ]), textin=CWbytearray(b'e4 6b 80 08 c8 70 47 46 f4 e2 93 82 de 0f 54 0a'), textout=CWbytearray(b'85 29 b7 d8 bd 32 2f cc 88 f8 2d ca 27 78 39 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14160156, ...,  0.01171875,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'55 ce f8 b7 c1 60 c6 e6 93 36 27 ba 2e 6c a7 63'), textout=CWbytearray(b'0f fa b4 3d 07 a0 ff fc 3e af 67 45 03 b7 23 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13867188, ...,  0.00878906,
            0.06347656,  0.06445312]), textin=CWbytearray(b'62 65 31 a2 be d3 16 d6 d3 32 ab 3d d4 84 be dd'), textout=CWbytearray(b'02 7e 94 f2 6f df fa 93 2a 4b 47 d5 a5 f9 0c 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18554688, -0.13769531, ...,  0.00976562,
            0.06152344,  0.06445312]), textin=CWbytearray(b'e0 cf 6f 13 87 23 e8 2a b7 03 79 29 68 ec b3 eb'), textout=CWbytearray(b'13 49 e3 5c 74 ce 3a 1e 6f 88 6f 25 b2 d9 c9 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14257812, ...,  0.00878906,
            0.06347656,  0.06347656]), textin=CWbytearray(b'5e 63 c6 7d d0 fa 87 a9 a7 3c 4a 51 3c eb 76 39'), textout=CWbytearray(b'6e 97 ad 58 43 3b 29 f5 6f 2d cb 0f 28 60 0a 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.140625  , ...,  0.01171875,
            0.06542969,  0.06835938]), textin=CWbytearray(b'df ca fe 59 50 cb c2 25 9e fb 29 7e 05 a4 31 81'), textout=CWbytearray(b'8f eb 4c e7 69 82 9b c1 f8 d0 be 8f eb 5f f3 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13867188, ...,  0.0234375 ,
            0.07324219,  0.07421875]), textin=CWbytearray(b'26 78 a5 8f 81 b6 f0 8c f6 d1 83 cd 5d f0 77 02'), textout=CWbytearray(b'4c 02 4f 92 94 64 2d 71 4a ca 8c 87 7f 71 e5 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ..., -0.00390625,
            0.04980469,  0.05566406]), textin=CWbytearray(b'5d 8e a3 19 8c a9 ad b8 c5 8f 0a a4 67 68 38 a4'), textout=CWbytearray(b'26 50 80 e4 a6 ca 98 82 20 ad 5e 8d cf 20 49 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13476562, ...,  0.00292969,
            0.05566406,  0.06054688]), textin=CWbytearray(b'5b 7c fa bf e8 27 ff 89 cc f3 d9 bf b2 fe ce d8'), textout=CWbytearray(b'da e9 c9 1c 4e e2 c9 98 af f3 6e 3c 2b a8 22 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1875    , -0.13476562, ...,  0.0078125 ,
            0.06152344,  0.06640625]), textin=CWbytearray(b'dc 91 b1 f2 2d 78 5a d7 5c 07 62 8a 31 cf dc e6'), textout=CWbytearray(b'93 16 27 2f 69 0b cf 34 b8 dd 14 20 ef 50 2b 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1875    , -0.13769531, ...,  0.01367188,
            0.06347656,  0.06738281]), textin=CWbytearray(b'db b1 e3 aa a7 f4 ea 5b f0 c5 c1 b7 d9 1d 2f bc'), textout=CWbytearray(b'bb d7 af c2 a9 5d 17 2d 3e ab 77 10 0c 9c 32 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18554688, -0.13671875, ...,  0.01269531,
            0.06152344,  0.06542969]), textin=CWbytearray(b'54 c5 5d 39 f7 ef fa 9c b0 2c bb c4 f6 47 61 a7'), textout=CWbytearray(b'7a 47 1f d9 81 29 a7 a3 78 95 6e 0f d6 f4 8f 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18164062, -0.13671875, ...,  0.0234375 ,
            0.07128906,  0.07226562]), textin=CWbytearray(b'00 d5 47 22 4b 8c 3a 96 10 ae 27 eb 54 43 6f c5'), textout=CWbytearray(b'24 36 31 9e 72 a1 f8 2c f8 21 6b 34 86 c3 cb d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19042969, -0.14648438, ...,  0.01953125,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'c4 09 36 db 63 12 3f 78 0b 64 43 bf 0a 68 0b 2b'), textout=CWbytearray(b'b0 7e 03 75 d9 76 2a 93 47 01 fa 38 63 b0 87 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14257812, ...,  0.015625  ,
            0.06445312,  0.06933594]), textin=CWbytearray(b'd5 bb aa f6 2c b2 4c 84 66 9a c2 db ec 99 19 55'), textout=CWbytearray(b'76 4d 2f 24 89 b8 c2 43 f3 c5 2d 34 76 29 32 3c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ...,  0.01367188,
            0.06445312,  0.06738281]), textin=CWbytearray(b'ea aa 0f e8 d7 b0 6a 05 d3 b3 2d 92 b1 91 77 27'), textout=CWbytearray(b'73 c5 5b 03 e2 33 09 ed 05 ed 9e 39 6e cf a1 cb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14257812, ...,  0.00683594,
            0.06152344,  0.06347656]), textin=CWbytearray(b'33 8d ad eb bc ab bf ff c6 73 b8 c8 ec 7c 17 39'), textout=CWbytearray(b'4b df 86 c3 f7 10 46 ca 7f 3a ae 31 31 1c a0 bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14257812, ...,  0.01757812,
            0.06835938,  0.06933594]), textin=CWbytearray(b'e3 ae 31 0c 0f 2c 08 03 ec 7e 1d 1a 2d 1a 92 53'), textout=CWbytearray(b'f8 52 f2 54 53 15 f6 1b 44 d0 b1 5c 5e 19 bf ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19335938, -0.14257812, ...,  0.01464844,
            0.06542969,  0.06640625]), textin=CWbytearray(b'c5 13 a1 80 65 4b 14 db 6d 1d 16 9b 1b a6 1c 6f'), textout=CWbytearray(b'bc 0f 2b 6b 3c 68 0a a9 e4 20 b9 cb 75 5d e9 33'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13574219, ...,  0.00390625,
            0.05664062,  0.05957031]), textin=CWbytearray(b'71 a7 5f c8 64 ba 0a 94 26 89 bb 6d cb 7d 9f f3'), textout=CWbytearray(b'cc c9 c9 1e d7 10 1e ce fe 3c 50 d0 9e 01 d8 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14550781, ...,  0.00390625,
            0.05761719,  0.06152344]), textin=CWbytearray(b'62 43 cd 8c ec 71 be 4a 5a 8b 5a 77 34 5a 9c 57'), textout=CWbytearray(b'43 2e 04 45 c3 42 04 70 e0 4e ce 71 73 0b 88 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14355469, ...,  0.01660156,
            0.06542969,  0.06738281]), textin=CWbytearray(b'd4 e7 a6 58 b4 67 3a 51 44 8d ef 30 0a 40 83 36'), textout=CWbytearray(b'1a 36 27 2a 91 9d d7 95 73 be 67 cb 5f 1d e7 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13574219, ...,  0.01464844,
            0.06738281,  0.06933594]), textin=CWbytearray(b'c5 7f 9b 28 0f 30 cc 47 69 f7 36 5c d6 f7 58 ae'), textout=CWbytearray(b'e4 da 45 1d ba bb d1 9e a1 0f 9e e1 47 24 43 f9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14355469, ...,  0.01464844,
            0.06347656,  0.06640625]), textin=CWbytearray(b'31 1e e0 c4 2e e4 9b f1 87 6e 66 24 8a 69 b2 50'), textout=CWbytearray(b'ea c4 4b 79 88 ab 94 14 3a 97 f1 38 84 6e c8 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18164062, -0.13574219, ...,  0.01074219,
            0.06347656,  0.06445312]), textin=CWbytearray(b'1c bd 4a 8e e1 06 f7 d8 ab 03 e3 51 75 da 1b dd'), textout=CWbytearray(b'ff 23 e5 22 ca a2 23 41 26 9e 46 e4 f1 6a 29 59'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13867188, ...,  0.01660156,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'5a 79 9a f1 2a 46 67 9e 7d 0b 03 45 42 a7 55 75'), textout=CWbytearray(b'cc 96 d2 31 18 f7 81 f2 0d 3b 57 7f a8 b8 af 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.140625  , ...,  0.0078125 ,
            0.05761719,  0.06152344]), textin=CWbytearray(b'68 03 3b 49 d7 b7 42 b5 c1 f0 17 4d cf 29 14 fc'), textout=CWbytearray(b'56 4b f8 be 1b 0e 4e 9d 2f 00 28 b1 80 e6 81 08'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14257812, ...,  0.02050781,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'67 47 4b 00 e5 23 75 fb 52 c2 12 a0 ba 33 cd 8f'), textout=CWbytearray(b'e8 a3 40 d3 fd d4 28 77 67 2c 45 02 75 45 a7 b3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14550781, ...,  0.02441406,
            0.07324219,  0.07226562]), textin=CWbytearray(b'bf 11 d1 b5 42 9b fd 6d 65 14 86 ee 5e 95 09 30'), textout=CWbytearray(b'11 7a 84 d3 cd da 56 30 eb 5b b8 f0 50 42 59 62'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.140625  , ...,  0.015625  ,
            0.06640625,  0.06835938]), textin=CWbytearray(b'4a 2c 40 7d 21 d9 6e 8f 9f 10 bd ea 74 70 13 1b'), textout=CWbytearray(b'bc 99 c6 60 a4 e1 38 ff 1f 9b 85 61 be 52 22 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.140625  , ...,  0.01855469,
            0.06640625,  0.06738281]), textin=CWbytearray(b'81 07 27 44 f7 24 6b 59 c1 2a 6a 17 ff 79 b4 7d'), textout=CWbytearray(b'16 2b ef de ca 5d 36 1e bd 88 34 b3 f3 91 d5 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14355469, ...,  0.01269531,
            0.06640625,  0.06738281]), textin=CWbytearray(b'c6 b5 6e 06 03 86 e1 02 91 4d c9 bb 17 25 8a 5a'), textout=CWbytearray(b'64 8f 85 65 d7 55 be 66 80 ea bd e4 e4 63 8e f2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14355469, ...,  0.01269531,
            0.06542969,  0.06835938]), textin=CWbytearray(b'94 dd ce 03 54 3c 00 4b 79 f6 02 ef e1 f6 7a 07'), textout=CWbytearray(b'f0 5b ba 3e ed a6 cc a8 40 62 ba 2b c7 47 a5 be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01660156,
            0.06445312,  0.06738281]), textin=CWbytearray(b'14 cb ee b5 f1 42 2a 40 f0 0a 87 2c 87 e3 ae 78'), textout=CWbytearray(b'da 17 dd 00 42 f8 0f 4b b7 12 17 25 8f 20 78 ca'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14453125, ...,  0.00683594,
            0.05859375,  0.06152344]), textin=CWbytearray(b'b9 8d 24 50 9c 06 a6 b8 53 d0 78 c0 bd 2c 41 6d'), textout=CWbytearray(b'01 be 6c 14 3a bc 6f 14 56 67 20 e5 00 f4 3a 39'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14355469, ...,  0.00683594,
            0.05664062,  0.06347656]), textin=CWbytearray(b'22 95 2c 53 c3 38 17 b2 0e e1 21 ac ef 54 87 11'), textout=CWbytearray(b'f4 40 8b e6 5a 7f 38 2c 1c c7 9c 02 b1 45 f0 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.01660156,
            0.06835938,  0.07128906]), textin=CWbytearray(b'ab 47 47 65 49 33 50 74 fc 7c 91 09 75 ca 05 cf'), textout=CWbytearray(b'b9 55 93 e3 2d 7c a9 e6 db d9 2d 31 92 30 ae 43'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14160156, ...,  0.01367188,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'89 3f 99 df e6 b9 d9 5f 49 7c ec 0c 16 b7 05 09'), textout=CWbytearray(b'8f 18 4e 85 63 bd 31 b2 a4 3c 2e f4 e6 f8 c4 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18554688, -0.14257812, ...,  0.01367188,
            0.06542969,  0.06738281]), textin=CWbytearray(b'ea af ed f9 c9 b2 e3 12 41 fd b4 24 fa 94 bd 00'), textout=CWbytearray(b'd1 2c 15 d7 2b 64 5b bb ca 45 04 07 f2 bb 18 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14355469, ...,  0.01171875,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'72 b3 a7 67 18 bb 7f 8c 00 ab 8a bf 61 60 a5 57'), textout=CWbytearray(b'37 44 ef e7 39 72 49 5a 31 1f 48 4b d9 53 e1 c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.140625  , ...,  0.0078125 ,
            0.05957031,  0.06445312]), textin=CWbytearray(b'0a 11 41 e8 76 c3 86 5a 94 21 9f c0 25 fa c6 01'), textout=CWbytearray(b'a6 65 45 e4 52 53 d1 59 fc 74 99 41 67 f7 0e a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14257812, ...,  0.00683594,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'03 d4 ad 03 23 ec c2 82 a8 50 86 5e f5 01 b4 1e'), textout=CWbytearray(b'e7 f3 ef 11 21 77 93 ef d5 07 d5 27 83 d0 17 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13867188, ...,  0.00488281,
            0.05664062,  0.06445312]), textin=CWbytearray(b'3f 2c 10 ea 59 f8 c6 87 40 b5 3c 92 e1 55 75 11'), textout=CWbytearray(b'1f 71 bc 2a c0 2f 6e ca fd 46 02 f9 ca 82 c7 b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18554688, -0.14257812, ...,  0.00195312,
            0.05761719,  0.06152344]), textin=CWbytearray(b'62 ca 56 f8 af 1d 93 28 68 ba 99 4b 08 db 39 02'), textout=CWbytearray(b'6e ee 55 6b cf 8b cc 3f 9f b1 2e 60 35 2f e1 d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ...,  0.01367188,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'28 8b f8 a8 97 d1 a2 33 16 d7 f8 f4 19 47 5e e1'), textout=CWbytearray(b'9c 4e 6d 6c e8 02 73 d2 14 26 0e 26 40 8e 83 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19238281, -0.14453125, ...,  0.00683594,
            0.05957031,  0.06445312]), textin=CWbytearray(b'6b 16 b4 f7 16 33 3d bd 3a 70 cf 83 5a 3c 7e 94'), textout=CWbytearray(b'7c ea 7c a0 de 13 d7 a1 c0 8e 8f c7 87 38 36 ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14160156, ...,  0.01953125,
            0.06933594,  0.06933594]), textin=CWbytearray(b'ea 7e 6f 0b dd 87 79 7e 60 ae ec aa 7e df 82 26'), textout=CWbytearray(b'3e 58 ae 18 63 de 29 8b 74 4e 02 91 15 ca 7d 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.13964844, ...,  0.00878906,
            0.06152344,  0.06542969]), textin=CWbytearray(b'42 06 f5 84 ec 49 f9 c9 66 40 77 da 66 1d e1 df'), textout=CWbytearray(b'53 10 4c 29 ac 0b 01 c7 37 a7 99 9c 9c 4c ac 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13867188, ...,  0.01757812,
            0.06542969,  0.06835938]), textin=CWbytearray(b'5c 0a ff f9 30 52 79 33 19 7a 5e 19 a5 1d f2 30'), textout=CWbytearray(b'01 03 64 3f 05 84 8a af f3 36 bc 99 56 c2 97 9f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14648438, ...,  0.01269531,
            0.06445312,  0.06640625]), textin=CWbytearray(b'c7 46 c2 69 ad a2 9a 9f c5 8b ba 87 f3 3f 1f 0b'), textout=CWbytearray(b'92 62 ea 6c f9 1c bd 02 5b cd da 18 c1 34 26 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13964844, ...,  0.01269531,
            0.06542969,  0.06640625]), textin=CWbytearray(b'f4 50 32 0d 16 30 92 21 c3 1d 84 64 a2 cb ba 07'), textout=CWbytearray(b'c7 87 41 5e b6 db 46 7f 04 cc 3e 04 5b d9 c0 c9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18847656, -0.14453125, ...,  0.        ,
            0.05664062,  0.05957031]), textin=CWbytearray(b'5e 45 be 1b d2 69 87 5a 2d 77 41 e7 74 bb 31 4c'), textout=CWbytearray(b'74 fa c5 64 94 23 8c 5c 4a ea 6e cc 99 a4 a9 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14257812, ...,  0.01074219,
            0.06445312,  0.06640625]), textin=CWbytearray(b'f5 23 d0 27 72 42 38 06 d1 12 dc 28 c2 25 38 9f'), textout=CWbytearray(b'37 82 b6 54 68 c0 bc f9 2a f6 b7 e9 c1 ea 08 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19433594, -0.14257812, ...,  0.00976562,
            0.06347656,  0.06542969]), textin=CWbytearray(b'3e 47 f2 be e8 45 ca d3 ab e4 f1 65 21 9e 50 7d'), textout=CWbytearray(b'0b 43 b7 53 91 61 0e d2 f4 31 db 26 c9 78 d9 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14257812, ...,  0.0078125 ,
            0.05957031,  0.06542969]), textin=CWbytearray(b'5b 98 be 18 5c b1 45 55 ba 40 32 55 bf c7 ab 2c'), textout=CWbytearray(b'fc 27 6f 6f b7 0c ea 71 12 ab b2 eb ad a2 21 ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14160156, ...,  0.015625  ,
            0.06542969,  0.06738281]), textin=CWbytearray(b'3c ab e2 32 2f f6 be d4 cd a4 3f 93 b7 2a 68 03'), textout=CWbytearray(b'c1 38 3e 4a 95 c3 ba 68 55 7b 59 89 6a 0b a0 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.13671875, ...,  0.00292969,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'12 44 94 20 c9 4e 1e ab 4c 6c d6 f7 c2 e3 11 dc'), textout=CWbytearray(b'8b 30 59 52 7c 96 78 32 1c dd 3c 3e 17 ff b7 e6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13769531, ...,  0.0234375 ,
            0.07226562,  0.07421875]), textin=CWbytearray(b'd3 76 85 92 28 14 9e 66 64 7d ae af a9 b5 99 bb'), textout=CWbytearray(b'f8 a4 59 99 49 c9 f5 7c 6a 54 d5 cc 93 bc 96 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.00585938,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'81 b0 ae 07 08 ac 5a be ea 9c cb c2 79 12 f4 15'), textout=CWbytearray(b'cd 73 6b 8e e5 18 b7 42 6e 48 b3 45 bc ec a4 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14355469, ...,  0.02050781,
            0.06835938,  0.07226562]), textin=CWbytearray(b'8d 88 e9 2a 25 1d 49 01 ba 24 22 48 d1 d7 c2 1e'), textout=CWbytearray(b'f4 5e e2 3e 7b 3c 14 75 11 6b 66 81 12 33 12 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.14257812, ...,  0.02148438,
            0.0703125 ,  0.06835938]), textin=CWbytearray(b'18 62 a2 06 29 2d 03 70 2c 46 42 fc c7 4e 31 f4'), textout=CWbytearray(b'a6 81 72 f3 1e ba 99 3f d2 f1 bc 21 78 24 fd 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13769531, ...,  0.01855469,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'cf a0 6e be bc 46 40 e8 cc 8b d3 23 9b 83 8a eb'), textout=CWbytearray(b'dd 5e 27 c5 9c e9 d3 19 1e a2 6e 3a 45 f3 97 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.14160156, ...,  0.01757812,
            0.06738281,  0.07128906]), textin=CWbytearray(b'db 52 c7 79 d9 61 f8 41 1b 8f 0b 75 47 da 42 3c'), textout=CWbytearray(b'98 1e e8 52 d3 94 97 16 8e e0 93 0e f2 50 9f c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.14160156, ...,  0.02050781,
            0.06933594,  0.07226562]), textin=CWbytearray(b'b4 00 58 7d 1c 19 8b 20 76 a9 1b d6 b9 d6 83 1d'), textout=CWbytearray(b'af 96 c7 df b0 77 64 aa eb 49 e1 f6 5c 4a bf 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14257812, ...,  0.01660156,
            0.06542969,  0.06738281]), textin=CWbytearray(b'1c 0c 6e 8b 57 f6 64 9c 12 34 41 62 12 5e 87 49'), textout=CWbytearray(b'4b 2b 56 84 ce 7c 79 dc 3d 2a 58 a2 b8 8e b6 c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14453125, ...,  0.01464844,
            0.06445312,  0.06640625]), textin=CWbytearray(b'24 27 77 24 6b e5 7b f7 2b 8d 67 4a 9b 51 b4 6a'), textout=CWbytearray(b'cf 1e 23 7d aa ed 36 2b 7e 2e 78 3b 16 9b bb 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14453125, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'7e c9 e5 f5 05 f4 d7 69 74 ff 2b 0a 7b 09 a1 9d'), textout=CWbytearray(b'4c 12 67 75 7a 83 29 f1 0d d9 04 79 71 ab c8 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14257812, ...,  0.02148438,
            0.07128906,  0.07226562]), textin=CWbytearray(b'40 ae 1a a0 9a 1f 1d e2 ea e5 2b b1 f9 9b 90 0e'), textout=CWbytearray(b'e9 52 fe 14 e9 82 ac a2 39 b1 09 71 34 f6 8c c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14257812, ...,  0.01464844,
            0.06542969,  0.06738281]), textin=CWbytearray(b'e7 50 6b ea bb d9 0a 36 cf fa 81 9b 47 32 30 68'), textout=CWbytearray(b'13 3d 51 7c a1 42 72 ea 1d cc 6c 4b 69 dc 58 d8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13769531, ...,  0.        ,
            0.05566406,  0.05957031]), textin=CWbytearray(b'ce ab e9 4d c1 b8 15 16 74 97 db 8d e8 63 b9 e5'), textout=CWbytearray(b'43 64 42 99 6f 36 58 a9 6c 39 a2 f6 83 b7 c1 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14648438, ...,  0.02832031,
            0.07519531,  0.07714844]), textin=CWbytearray(b'89 14 ce 29 a6 ca 30 6b 65 35 a2 84 86 9a 39 63'), textout=CWbytearray(b'a0 3d 1f f2 60 36 7b c8 3f 0f 5e 38 b8 e2 71 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13964844, ...,  0.00878906,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'ed d1 3e bb 97 6e 00 bd b9 31 79 61 a6 bb 53 17'), textout=CWbytearray(b'bf 7b 3b cc 51 18 66 8d 26 7e 13 79 58 8c 71 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19238281, -0.14648438, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'1d cc 92 27 24 5e d0 60 db 85 8a 53 98 1b ff 5f'), textout=CWbytearray(b'b3 86 7f 08 14 40 c0 f2 50 de 51 09 91 39 ab 06'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19433594, -0.14355469, ...,  0.01464844,
            0.06640625,  0.06738281]), textin=CWbytearray(b'ab 60 36 71 e1 83 9e f3 10 38 de 1d 02 16 2f 7f'), textout=CWbytearray(b'db 00 33 61 c0 51 a8 88 03 06 1c 1f 8f 15 46 4d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13867188, ...,  0.00585938,
            0.05859375,  0.06152344]), textin=CWbytearray(b'1e 58 3c 4f 61 00 49 00 70 96 64 fe 41 34 cc a0'), textout=CWbytearray(b'99 bd 22 37 dc 9a 5a 31 88 f0 e3 bd 49 fd 00 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ...,  0.02148438,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'c7 37 d8 6e d7 33 32 c3 98 6b c9 d2 ac 0c 0f 69'), textout=CWbytearray(b'56 ae 0a 90 bb a8 4f 9e 59 1b 01 a2 89 5f 06 5b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14355469, ...,  0.00585938,
            0.05761719,  0.06347656]), textin=CWbytearray(b'32 5c 87 84 02 9a f0 19 1d 42 1f 22 87 d3 a8 4d'), textout=CWbytearray(b'12 02 95 f3 28 88 5d b0 b4 32 87 b0 65 c3 4c 7d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.13964844, ...,  0.00976562,
            0.05859375,  0.06347656]), textin=CWbytearray(b'64 03 37 a8 6e 79 1d 1d da b5 42 31 91 3f 49 7a'), textout=CWbytearray(b'c5 b3 29 f6 3a a4 69 62 8e 93 af dc eb 4f 03 f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13867188, ...,  0.03027344,
            0.07421875,  0.07714844]), textin=CWbytearray(b'34 68 5f b3 1b 1f 6a 9f 47 2d 4b aa 8d 8a 0c d1'), textout=CWbytearray(b'77 14 f4 6a 7f 81 b8 5c 39 cb db ed 4b bc e4 6d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14550781, ...,  0.01367188,
            0.06054688,  0.06640625]), textin=CWbytearray(b'd9 e8 8d 37 07 2f 28 95 d6 bb 02 45 89 54 34 59'), textout=CWbytearray(b'4f 10 c0 7c 62 a1 4d 44 ca 79 17 4b f7 c9 c3 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19238281, -0.14257812, ...,  0.0078125 ,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'e1 d8 88 41 af 05 d7 e3 d5 fa 21 dc f4 41 73 6f'), textout=CWbytearray(b'44 b5 30 b4 5e 2c 90 2a c7 05 48 0e be fc d2 77'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13769531, ...,  0.01464844,
            0.06347656,  0.06347656]), textin=CWbytearray(b'20 08 15 cb 64 3a 6e 9d 9b 56 64 51 72 f9 36 03'), textout=CWbytearray(b'c6 57 9d 02 e4 db b3 e6 e9 8e c0 11 28 c7 0c 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.140625  , ...,  0.00292969,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'a2 c0 85 26 31 97 1e f3 3b ec ff 84 a6 00 f3 3e'), textout=CWbytearray(b'62 16 1e 6d 42 ff 7c 1b 81 38 6b 83 93 83 04 a9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01074219,
            0.06347656,  0.06445312]), textin=CWbytearray(b'a0 6f 51 e2 9c 84 f1 f3 14 31 49 72 e0 68 a5 a2'), textout=CWbytearray(b'ac a2 cf 8e 65 89 45 1f 76 ec ae cb 9e 5d fa 43'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.140625  , ...,  0.00585938,
            0.05859375,  0.06445312]), textin=CWbytearray(b'a6 c4 4b 00 da 46 4f a0 bc d9 d3 6c db fe d5 4b'), textout=CWbytearray(b'38 69 ff 20 d8 7d 79 26 69 c7 bc d3 67 a7 64 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13671875, ...,  0.01269531,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'6d 1f 12 20 c2 4b 86 91 41 71 3a cc 00 83 0b af'), textout=CWbytearray(b'e2 4f fe ec 07 b1 ee 0f fd d0 fb 52 64 ab a9 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13671875, ...,  0.00683594,
            0.05957031,  0.06152344]), textin=CWbytearray(b'7b 7a c8 6f 36 00 75 c6 2e 34 73 74 70 06 c3 de'), textout=CWbytearray(b'da 08 79 b3 d3 f1 c3 25 52 e8 7c 65 c7 85 52 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.13769531, ...,  0.00390625,
            0.05761719,  0.06347656]), textin=CWbytearray(b'87 43 04 34 d9 8a 4c af 87 38 5d 14 94 b4 53 57'), textout=CWbytearray(b'e5 77 72 98 77 67 2f 01 fe d4 d5 81 45 b4 ec aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.1953125 , -0.14550781, ...,  0.01269531,
            0.06347656,  0.06542969]), textin=CWbytearray(b'47 f4 85 b4 a0 e4 c1 7f 1e aa 6b e1 77 72 53 4a'), textout=CWbytearray(b'1e 71 ad da ca 7f 6e f3 63 5a 13 c6 70 d0 ad e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13867188, ...,  0.02441406,
            0.07519531,  0.07421875]), textin=CWbytearray(b'74 b6 c3 b0 4b 68 e0 67 f9 57 4b 5d 3c 6b 7a d6'), textout=CWbytearray(b'65 92 ec 76 8f 69 0e 50 6e 1c c9 03 27 0d d8 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13867188, ...,  0.00878906,
            0.06054688,  0.06542969]), textin=CWbytearray(b'bc aa bd 3d 69 68 22 26 db 16 85 35 88 54 d7 cf'), textout=CWbytearray(b'99 4f 96 26 6e 44 db 24 1f 8e 2c d4 65 bf ac ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14160156, ...,  0.02539062,
            0.07324219,  0.07519531]), textin=CWbytearray(b'32 a3 03 6b 53 6b bd c0 72 73 3d b6 26 03 97 61'), textout=CWbytearray(b'19 db 98 0b c2 fd 71 0f 5c b5 26 d1 1c 9b 34 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14257812, ...,  0.01074219,
            0.06445312,  0.06835938]), textin=CWbytearray(b'90 83 e6 05 68 90 bb 1f 84 cb fd 36 66 79 f9 7f'), textout=CWbytearray(b'24 29 97 b5 b8 dc b5 7f b3 79 97 26 e5 f8 1e 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18359375, -0.1328125 , ...,  0.01367188,
            0.06152344,  0.06445312]), textin=CWbytearray(b'80 54 cc f4 9a 77 03 1a ed d8 36 aa 2f 78 cd ab'), textout=CWbytearray(b'a9 c9 b6 f1 48 67 7a 8b 6d 62 4d 48 cb b9 70 98'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18457031, -0.13574219, ...,  0.01074219,
            0.06347656,  0.06445312]), textin=CWbytearray(b'69 b7 fb fe cd 0c 0a 71 8a 0c 55 52 f6 c0 e6 db'), textout=CWbytearray(b'ef 33 4c 59 3f 66 01 67 13 0a a7 89 6d dc 93 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ..., -0.00390625,
            0.05371094,  0.05566406]), textin=CWbytearray(b'8d c5 e6 35 86 9c 3c 36 38 d6 41 8a 48 ea de 32'), textout=CWbytearray(b'c4 af e4 44 6b df 99 28 cb f0 80 23 d0 d7 c7 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.14160156, ...,  0.01171875,
            0.06054688,  0.06347656]), textin=CWbytearray(b'f7 38 e0 0d 93 fe 5a 1a 55 1a 63 31 30 05 6b 98'), textout=CWbytearray(b'85 4e 4d 89 0f af 4c c6 c9 a1 a7 96 de 98 76 bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.140625  , ...,  0.01074219,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'21 2d 05 e0 f5 2a db 5d 7b ac ae dc 20 a9 9e 40'), textout=CWbytearray(b'e6 e1 7e 90 92 1e b4 83 5d f2 dd ed 15 3f 5c 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18359375, -0.13574219, ...,  0.00976562,
            0.06445312,  0.06640625]), textin=CWbytearray(b'de 5e 5a 8b 4a 22 49 52 45 b8 32 52 cb 97 4d c4'), textout=CWbytearray(b'a7 fe 7f 9c 95 9c 9c 57 ca c7 fb 3a cd 7d 53 b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1875    , -0.13769531, ...,  0.00976562,
            0.06054688,  0.06445312]), textin=CWbytearray(b'9f 19 48 82 a8 3e 80 3d 67 f3 5c 8d 17 0a ba b8'), textout=CWbytearray(b'25 20 8a d6 44 f8 d2 58 94 ea c0 e5 ea 78 63 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14453125, ...,  0.01171875,
            0.06152344,  0.06640625]), textin=CWbytearray(b'58 13 ef 9d 47 cf 5f cd 89 ec bc 90 a0 20 3d 2a'), textout=CWbytearray(b'75 75 6b cd b2 65 9a ea 13 91 a4 25 56 66 d7 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13574219, ...,  0.02832031,
            0.07226562,  0.07519531]), textin=CWbytearray(b'52 cb 6b 04 52 85 9a 33 7b 9e da 37 83 6b 28 e9'), textout=CWbytearray(b'76 fe a4 0d df 03 98 32 73 7b 02 19 1f c1 52 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.140625  , ...,  0.02441406,
            0.06933594,  0.07226562]), textin=CWbytearray(b'4e 23 00 4f 89 25 72 72 5d 23 85 f9 30 da 1b 79'), textout=CWbytearray(b'05 5e 4b d3 04 5e e7 3c 3d 9e 8e 24 f2 71 85 d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14453125, ...,  0.00976562,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'a4 5d 28 91 7a 72 c7 af 70 ce 85 83 8c 51 98 46'), textout=CWbytearray(b'38 ec 6c 43 ae d5 11 79 49 42 1d a9 8d 53 58 a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.01660156,
            0.06933594,  0.06933594]), textin=CWbytearray(b'e0 94 c9 6e b4 51 24 70 60 42 58 5f a3 28 1a b7'), textout=CWbytearray(b'12 db bf b0 9c c8 8c 73 d4 8f 95 25 51 69 94 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14453125, ...,  0.01855469,
            0.06640625,  0.06835938]), textin=CWbytearray(b'2b 2c 5c 1c d2 8a 9b 0f 45 04 65 40 50 84 76 26'), textout=CWbytearray(b'e0 d5 b2 41 b4 9f 8e 23 45 4e 62 9e 6b 1c 80 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14160156, ...,  0.00292969,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'b1 e7 66 ad 48 c8 35 ce f0 4e 9e 7f 7a 6a 0c 16'), textout=CWbytearray(b'65 f9 f1 06 36 de 5c 6a bb 71 87 b5 f9 55 4d 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13964844, ...,  0.01953125,
            0.06738281,  0.06933594]), textin=CWbytearray(b'7c b1 2e 94 1b 05 80 43 56 88 99 e6 8c 8f 77 21'), textout=CWbytearray(b'f8 42 9f 2e 04 ad 87 dd 76 57 64 1b bf 6d 56 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13964844, ...,  0.02832031,
            0.07519531,  0.07519531]), textin=CWbytearray(b'ec 0a 24 cd 37 59 4b da 9f bd 9c 22 fa 7c eb 3b'), textout=CWbytearray(b'5a 7c d9 6d 86 c6 ac b5 45 6d 77 b1 1a 56 cc 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14453125, ...,  0.00976562,
            0.06347656,  0.06445312]), textin=CWbytearray(b'26 d7 87 b9 79 5a 19 e8 e1 e5 7d 4a 6b 4c 21 78'), textout=CWbytearray(b'3c b2 07 c5 27 87 33 ce b8 60 ae 80 09 e4 2b 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14257812, ...,  0.0078125 ,
            0.05957031,  0.06445312]), textin=CWbytearray(b'f4 dd e0 c0 db fe 4a d3 e1 67 ab e4 22 00 9b ea'), textout=CWbytearray(b'9b 73 49 42 ca c1 3f 19 b8 b9 19 c1 23 7c 57 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.14355469, ...,  0.00878906,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'ce 95 a4 c2 f4 7c 24 8d 6f aa 77 dc 62 76 e0 8b'), textout=CWbytearray(b'a4 68 73 84 de af 38 6d 07 5e 35 08 bb 30 f5 fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14160156, ...,  0.02050781,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'50 12 ac aa 96 5e 8e ee 73 e3 13 74 2c 8e dd 5d'), textout=CWbytearray(b'48 1f 53 a1 44 b6 ec 67 ec 03 2c 4b 66 b4 39 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13964844, ...,  0.01464844,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'71 47 58 26 15 98 9e 21 57 0a 1e 04 5c 90 ef ee'), textout=CWbytearray(b'19 9d f2 13 8b b4 75 29 5d af 91 73 ff 3c de cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19042969, -0.14648438, ...,  0.02050781,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'3a e7 e8 fe 10 e1 dc 20 21 80 c3 96 11 44 79 7d'), textout=CWbytearray(b'13 e7 ea 9e ed 02 06 e6 02 07 84 71 e8 62 7d 68'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14550781, ...,  0.00585938,
            0.05761719,  0.06347656]), textin=CWbytearray(b'f5 eb 69 d9 db 9d 41 6c 55 07 23 f0 5e 59 19 22'), textout=CWbytearray(b'be 92 f7 09 fa 94 56 c6 87 e4 77 81 3d 38 7f 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19140625, -0.14648438, ...,  0.01171875,
            0.06152344,  0.06542969]), textin=CWbytearray(b'17 35 3a ae 15 61 e0 7a c0 9a 68 96 91 62 1f 51'), textout=CWbytearray(b'5a aa c3 fb 28 e0 d6 6a b7 4b c3 9b 84 fa 22 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.13964844, ...,  0.00976562,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'28 e9 43 ee ee d6 44 62 a9 0b 19 92 e5 16 29 98'), textout=CWbytearray(b'99 3f 03 f3 dc 9a 5e 97 0b 15 83 26 1e 76 52 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18261719, -0.13574219, ...,  0.01367188,
            0.06445312,  0.06542969]), textin=CWbytearray(b'03 f3 aa 75 85 e6 01 11 f9 b8 18 10 1a f1 7d b7'), textout=CWbytearray(b'87 1d 22 1d dd 87 c3 05 18 14 45 b4 5d ef 71 3b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13964844, ..., -0.00292969,
            0.05371094,  0.05761719]), textin=CWbytearray(b'8e 1e 46 ab 03 3e cb f0 3b d1 56 42 76 ef c7 a0'), textout=CWbytearray(b'b3 f0 50 dc b6 5d 60 c0 c1 0c 7d 7b aa 2d be 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.14160156, ..., -0.00195312,
            0.0546875 ,  0.05761719]), textin=CWbytearray(b'69 4d c4 69 23 a4 0d 4a 0b d4 b1 cd 78 a5 70 04'), textout=CWbytearray(b'8b 0c b6 5a f9 0c 42 16 19 62 5a da eb 0a 07 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.17871094, -0.13671875, ...,  0.01855469,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'18 1f cc 59 be 8e 52 9d 39 b9 70 ef 6e 92 eb d5'), textout=CWbytearray(b'f9 18 5d ca 15 eb 7b fd eb 37 ce 35 f4 3d fc 76'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18261719, -0.13769531, ...,  0.01855469,
            0.06835938,  0.06933594]), textin=CWbytearray(b'e1 c4 b0 a9 2f b1 c6 f3 b2 91 9a 9e 3f 6b 96 d0'), textout=CWbytearray(b'f6 b4 44 e3 ab 7f 51 31 c4 62 19 12 5b ee f8 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02246094, -0.18066406, -0.13378906, ...,  0.02539062,
            0.07324219,  0.07226562]), textin=CWbytearray(b'fd 34 78 92 e1 cb 07 30 ef 3b 67 b4 b9 27 b1 b9'), textout=CWbytearray(b'48 18 5c c2 4b b9 4e d1 12 1d a0 4f 11 d6 13 45'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.01367188,
            0.06640625,  0.06738281]), textin=CWbytearray(b'4f a9 55 96 e6 58 7b f7 5a 91 a5 f6 6a af 9c 44'), textout=CWbytearray(b'2e 73 1a de 7e 77 b1 2c 1c a1 6e 41 00 90 24 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18359375, -0.13671875, ...,  0.01464844,
            0.06347656,  0.06738281]), textin=CWbytearray(b'5c 9c 3e b6 5b f5 3e da f6 16 65 db 39 5c 7d d3'), textout=CWbytearray(b'2e b8 d9 d9 21 ef f2 7d 53 39 53 5a 93 fa f5 78'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13671875, ...,  0.00195312,
            0.05566406,  0.06054688]), textin=CWbytearray(b'78 27 67 aa 9e 34 01 1a 6c 0b 79 29 39 64 d7 42'), textout=CWbytearray(b'a9 ac 0b 48 ae 58 e3 be ac 2d cd 70 52 14 07 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19238281, -0.13867188, ...,  0.00585938,
            0.05859375,  0.06347656]), textin=CWbytearray(b'67 e4 f6 50 b9 3f 96 1f 91 ae ad 58 92 23 f1 41'), textout=CWbytearray(b'98 91 e2 c2 e2 08 72 1f 6f b8 9b 48 9b f8 af 71'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13964844, ...,  0.01855469,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'63 1c ef fd 0e 72 06 d1 70 c2 ca 68 49 06 18 97'), textout=CWbytearray(b'ae 55 2d e9 f5 af 86 18 70 a1 c8 bd b3 a8 10 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.02539062,
            0.07324219,  0.07421875]), textin=CWbytearray(b'c8 05 25 ae ea 69 2b bd 7d 9b 6f 4d d2 63 d6 ea'), textout=CWbytearray(b'e1 41 41 9f 2e 8e 1b 86 e7 70 4a c6 9b 1b 5a df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.1875    , -0.14257812, ...,  0.        ,
            0.05371094,  0.05761719]), textin=CWbytearray(b'14 d1 4e 9b 01 9e b3 5a b4 88 33 89 9e c2 a3 61'), textout=CWbytearray(b'23 29 5d ba 64 b7 3a 69 99 42 8f 99 be a8 f3 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13867188, ...,  0.02539062,
            0.07324219,  0.07421875]), textin=CWbytearray(b'd2 c2 4f bd 21 72 8a 96 ca 49 c1 d3 58 c0 33 b6'), textout=CWbytearray(b'a4 23 b0 67 73 73 b0 16 10 54 8f b7 f0 64 c2 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.01757812,
            0.06640625,  0.06933594]), textin=CWbytearray(b'4c 9f f1 8f 26 c9 4e 02 7a 3a 41 83 e8 83 b2 e2'), textout=CWbytearray(b'0d 47 15 62 ba ff 9b 3f 79 95 57 f3 53 4c ac 86'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14355469, ...,  0.01855469,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'22 1c 8d 22 1c 48 87 fa 89 50 ea 3d 51 bc 0a 3f'), textout=CWbytearray(b'5b 3c 39 6b 43 4b 24 a1 7e 91 ff 44 78 16 e0 5c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13964844, ...,  0.01757812,
            0.06640625,  0.06933594]), textin=CWbytearray(b'b2 8a b0 f6 13 af bc 18 23 ba 87 92 8d 35 a1 10'), textout=CWbytearray(b'bb 01 87 d9 a3 fb 5d b2 59 f0 76 aa 52 91 34 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18066406, -0.13476562, ...,  0.00292969,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'96 92 04 13 dd aa bf 54 a2 6a 67 ac 5c fb f6 de'), textout=CWbytearray(b'44 97 8a 94 c0 61 e0 14 29 84 8a 43 07 d0 37 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19042969, -0.14257812, ...,  0.0234375 ,
            0.07226562,  0.07128906]), textin=CWbytearray(b'48 03 ff 36 91 96 83 ef 6d c6 dc f0 10 01 ce 54'), textout=CWbytearray(b'6e f1 3a ce 55 4d 17 4b 6e a9 b4 cc 7c ea f6 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18652344, -0.13671875, ...,  0.01074219,
            0.06054688,  0.06542969]), textin=CWbytearray(b'86 48 69 d8 7d 20 2b 8a 9c 13 ec 1c 57 9d ce 28'), textout=CWbytearray(b'8b fc a8 17 4c e3 76 3e 2a 06 12 60 93 a9 e2 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18554688, -0.14160156, ...,  0.00390625,
            0.05957031,  0.06054688]), textin=CWbytearray(b'03 93 c8 f2 fc f9 64 34 b7 f6 69 17 73 11 bd 41'), textout=CWbytearray(b'43 33 e0 a1 1f 60 6a bb df 38 37 8e d5 dc 85 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19042969, -0.14648438, ...,  0.00390625,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'2b ea fe b3 fd 50 b1 eb bd 54 54 0b ce b4 52 92'), textout=CWbytearray(b'f4 48 50 69 61 ff 0f e8 e8 b6 8d 35 2a 48 04 58'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.140625  , ...,  0.01660156,
            0.06640625,  0.06835938]), textin=CWbytearray(b'c0 dc a0 f3 21 69 97 bf 84 b5 8d 1d a2 30 b0 99'), textout=CWbytearray(b'7b 61 15 1c c3 cb 82 6d 5e 2e ea 2e cb 90 5f a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.1953125 , -0.14746094, ...,  0.00292969,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'2a 58 d6 75 f8 db d9 69 9e f4 dc 83 28 5d 77 6b'), textout=CWbytearray(b'49 9e 46 ab 68 17 36 e4 b9 24 b6 15 19 a8 1b dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14648438, ...,  0.02441406,
            0.07226562,  0.07324219]), textin=CWbytearray(b'a6 4a 6b 44 5e 2f ae fd f7 bd dc 86 53 51 75 22'), textout=CWbytearray(b'3a d7 59 d3 8f 98 6e a2 e0 56 6f 2e 4a f8 1d 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13769531, ...,  0.0078125 ,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'5e e3 cd b9 f7 c0 8b cf c1 46 9b ef 29 4d 81 bf'), textout=CWbytearray(b'1a 62 e8 bd fd ca 7c 03 c0 75 cd e3 8d 47 c7 81'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.02246094,
            0.0703125 ,  0.07226562]), textin=CWbytearray(b'00 c7 c0 76 05 97 22 47 c8 d2 a5 f0 d7 a0 05 61'), textout=CWbytearray(b'58 d9 11 00 f3 e0 4b 42 bb 2e 1a f6 a4 68 78 14'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19335938, -0.14160156, ...,  0.02441406,
            0.07421875,  0.07519531]), textin=CWbytearray(b'c3 eb d5 2d b3 bf 6d 14 d4 e5 d6 3e f4 f5 1a 2c'), textout=CWbytearray(b'9d 53 2d 2e 76 82 59 75 ac 72 44 3e a4 89 ac 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14550781, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'f8 55 c1 a2 9b 6c 20 07 c1 82 d4 76 62 3f 02 42'), textout=CWbytearray(b'31 0c 54 41 d1 0d 92 fa 4c fe 04 15 4b aa db 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14257812, ...,  0.01074219,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'44 72 6d a7 9b ea 7a 54 4c cb 97 f2 f8 ba b5 4f'), textout=CWbytearray(b'97 89 77 c4 81 d5 4e 6d f6 4b 63 da 13 91 97 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13867188, ...,  0.00292969,
            0.05761719,  0.06347656]), textin=CWbytearray(b'20 41 fe 3a 61 64 d4 0d 1d 42 a9 e9 bf c0 9b 91'), textout=CWbytearray(b'8e af 04 76 51 c1 f1 0d 25 ee 6e 5c c8 9b 5e 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.13769531, ..., -0.00488281,
            0.05175781,  0.05761719]), textin=CWbytearray(b'76 37 de 31 f5 87 83 5d 14 41 ac e8 3e 6a b8 fa'), textout=CWbytearray(b'3d 94 08 aa 03 02 01 25 02 71 65 e1 18 e6 30 a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18164062, -0.13867188, ...,  0.00683594,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'd7 ca f6 44 de 81 0a 0b 31 70 d7 f6 0c a0 20 e0'), textout=CWbytearray(b'c8 42 a4 04 44 5b 35 1c 97 92 82 a3 82 76 19 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14257812, ...,  0.00585938,
            0.05761719,  0.06152344]), textin=CWbytearray(b'7e 45 53 24 a0 8d c2 38 78 3a 6f da e0 98 6e 63'), textout=CWbytearray(b'73 ea e7 5f 21 56 17 1d af a2 f9 0c f9 ef 6f 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14453125, ...,  0.        ,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'33 5c 0f a4 3b 95 99 60 12 e8 12 5f f7 88 08 3e'), textout=CWbytearray(b'55 21 3d 6f bd 01 6f 49 67 89 82 14 bb b7 fe 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19042969, -0.14648438, ...,  0.02148438,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'69 7c b3 91 9c 47 a0 fe d0 4d ef 53 8f 6b 2b 43'), textout=CWbytearray(b'7a e4 b4 b2 3f 1b b0 66 97 8d 47 34 62 95 7b 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13671875, ...,  0.00488281,
            0.05761719,  0.06445312]), textin=CWbytearray(b'8c 91 a6 fb f1 d3 22 94 1d 01 ee da 01 ca fb c6'), textout=CWbytearray(b'6b 50 42 ce d1 86 cc e6 78 8f 21 71 da 88 d1 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14355469, ...,  0.01464844,
            0.06347656,  0.06738281]), textin=CWbytearray(b'2d 83 04 1e 43 f6 20 cf 39 aa 0d db 8b a9 60 7c'), textout=CWbytearray(b'0a fb ad 57 4d 55 92 98 c9 18 2b cd 40 03 bd d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19238281, -0.14355469, ..., -0.00195312,
            0.0546875 ,  0.05761719]), textin=CWbytearray(b'89 d7 99 30 41 0e 2e 71 dc 53 c1 e5 0e 34 8b 0d'), textout=CWbytearray(b'21 38 d1 a6 43 ab 66 24 f9 9d d5 12 b2 cd 9c 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18457031, -0.13964844, ...,  0.00976562,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'e3 2a 74 a0 a0 56 22 55 04 03 9f ab 67 6b 6a ba'), textout=CWbytearray(b'58 4a 1d f7 d7 ef 27 a8 ac 73 b3 66 a8 c9 5a d1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.14160156, ...,  0.03417969,
            0.07714844,  0.07714844]), textin=CWbytearray(b'57 70 c1 d7 b1 60 f3 4c dd 52 84 b5 ab 9a 89 af'), textout=CWbytearray(b'76 c9 fd 70 3b fd 93 ac fb db 7b 03 2e 00 e6 d3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14257812, ...,  0.0234375 ,
            0.07421875,  0.07324219]), textin=CWbytearray(b'b4 b4 b9 9e 30 22 80 8e 4c 76 28 39 80 a4 bd 9f'), textout=CWbytearray(b'd2 7e 75 06 a0 a7 ad 41 c3 95 04 6f 5d cd 46 6a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13769531, ...,  0.00585938,
            0.06054688,  0.06152344]), textin=CWbytearray(b'79 98 02 5b c8 f8 93 82 fa b6 9c aa f2 73 1f 72'), textout=CWbytearray(b'1a aa d1 bc 4d ff 0c 8b 84 2f 8a eb 2c 57 71 a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14355469, ...,  0.00878906,
            0.06152344,  0.06347656]), textin=CWbytearray(b'5b 9a 55 b4 0f e3 05 77 a7 21 9b aa ed 05 41 5e'), textout=CWbytearray(b'bb 8c 9b 91 69 75 7f 6d 0a a1 45 f3 c6 a3 46 d5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.13574219, ...,  0.00488281,
            0.05664062,  0.06152344]), textin=CWbytearray(b'3a 0f fe a1 f4 20 12 95 d2 1f 13 ad fb d0 b6 0e'), textout=CWbytearray(b'de 90 5f cf 79 b1 a3 de 9c 5b cc 0e 1d 0d a3 02'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13671875, ...,  0.01757812,
            0.06835938,  0.06835938]), textin=CWbytearray(b'd0 ea fc 2d 32 86 1d 8b 4a f8 0f 0e 86 23 47 d9'), textout=CWbytearray(b'f1 e8 af 94 aa 35 be 2f ee 01 56 4e 30 83 bc 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.13964844, ...,  0.01269531,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'26 fa a5 65 a7 cb dc 06 9c bf 15 e5 01 98 c7 30'), textout=CWbytearray(b'08 89 81 ce e9 16 f6 fe 14 83 35 07 6b d0 bf 32'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13867188, ...,  0.00097656,
            0.05664062,  0.05957031]), textin=CWbytearray(b'b6 38 91 1c 1c 0f f9 4e b3 11 ee ac 91 19 77 1d'), textout=CWbytearray(b'd3 dd f4 ca 03 09 7f fd c2 b4 73 d8 dd 7c f5 a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14257812, ...,  0.02050781,
            0.06933594,  0.07128906]), textin=CWbytearray(b'5b 62 f9 74 63 01 f3 fd c7 ff cc 25 d3 d1 10 63'), textout=CWbytearray(b'33 56 24 45 f8 2b e4 92 f6 d8 73 88 05 5c 34 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.13671875, ...,  0.01269531,
            0.06347656,  0.06835938]), textin=CWbytearray(b'75 eb a7 ca bb 20 9f 02 2b c9 9a 94 8a d4 8a a5'), textout=CWbytearray(b'09 2e 14 a3 45 d8 43 59 f1 21 09 71 98 c6 29 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18554688, -0.13671875, ...,  0.01660156,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'a7 f4 73 6d dd 35 36 78 19 f6 d9 2f 39 c4 8e f1'), textout=CWbytearray(b'5a ff ad 89 d9 7c 3d 5d 3c 9a d1 55 ea aa ec 13'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13769531, ...,  0.01367188,
            0.06445312,  0.06933594]), textin=CWbytearray(b'9e 5d 32 a7 f8 c3 62 85 70 98 77 1e 3e 07 cc d8'), textout=CWbytearray(b'0e 36 37 ea c0 58 4b fc 83 dc 1c ab 60 0b b4 d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13769531, ...,  0.01074219,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'11 d1 3d 7f 50 2a ed 9b c2 38 82 c6 5e f1 8f a7'), textout=CWbytearray(b'7f 44 6e 4b e0 89 86 50 c0 8a 21 67 07 37 dc 7a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.01757812,
            0.06445312,  0.06933594]), textin=CWbytearray(b'1a 85 a5 06 de 9e 79 24 74 68 b0 3f ae c8 e4 d1'), textout=CWbytearray(b'42 fa 63 71 35 7d 8c ed 57 54 6a 76 9f 4a 3f 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13769531, ...,  0.01660156,
            0.06835938,  0.06835938]), textin=CWbytearray(b'b6 8f cf 76 1f 74 91 b3 fb 95 7a 31 24 76 fe e5'), textout=CWbytearray(b'68 87 06 1a b6 d0 c1 f1 29 9f ee 00 33 ca 6e ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19433594, -0.14160156, ...,  0.0234375 ,
            0.07226562,  0.07324219]), textin=CWbytearray(b'9c 82 ef a6 59 8e 2d 0f 8b 2b 55 34 58 ea 7a 49'), textout=CWbytearray(b'8e 1c 96 9f 02 ba 61 74 48 75 f8 b3 d2 be f7 fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13476562, ...,  0.01660156,
            0.06738281,  0.06835938]), textin=CWbytearray(b'57 4d f6 a1 1c f1 ce 0d 15 5f 90 93 1c 43 98 d9'), textout=CWbytearray(b'7b 7e c9 37 a5 b0 33 1d 0a 5c bf b2 79 41 cb b4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18359375, -0.14160156, ...,  0.01074219,
            0.06347656,  0.06445312]), textin=CWbytearray(b'd1 b7 44 c8 cf 9b 96 90 35 d4 80 eb e1 53 fe 85'), textout=CWbytearray(b'ba a2 d5 73 f4 c1 fa 86 fb 1d 81 d0 06 f1 fd e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14257812, ...,  0.01171875,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'0f 0a cf 8f 16 c0 34 31 b8 31 9c 88 ac 05 dd 98'), textout=CWbytearray(b'6a 65 e1 67 6d 91 4c c8 95 a9 3f 67 78 45 eb 91'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18261719, -0.1328125 , ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'5e 04 6b c4 80 9f f7 ce 01 f0 8a 21 37 d0 df d1'), textout=CWbytearray(b'36 f5 80 12 f7 a2 22 9c fd e8 1d 41 6a e3 f6 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19238281, -0.14257812, ...,  0.01269531,
            0.06640625,  0.06640625]), textin=CWbytearray(b'cc 7e d3 1f a4 6f 57 03 6c b7 0e f5 0a ee 03 6c'), textout=CWbytearray(b'e9 d1 12 cf be b3 db 5f 33 2c 82 6e fb e2 15 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.140625  , ...,  0.00976562,
            0.06054688,  0.06445312]), textin=CWbytearray(b'12 94 c3 b6 0e a3 8d db fe f7 31 32 ee 46 3d 25'), textout=CWbytearray(b'5c 38 72 fb 77 d6 b2 78 5b 74 e7 f0 d7 92 2f 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.13964844, ...,  0.01660156,
            0.06445312,  0.06835938]), textin=CWbytearray(b'd3 16 3d d3 99 66 0e 00 33 5e 42 de 81 e3 1a 03'), textout=CWbytearray(b'4b 6d ca 1b b4 cc 3b 68 7c fb 36 0d 04 a7 ae 1e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.01757812,
            0.06738281,  0.06933594]), textin=CWbytearray(b'a6 31 f6 18 88 26 6c 4d f0 c1 02 24 7a 74 fe 50'), textout=CWbytearray(b'7d 44 b1 10 d9 c9 3e 56 ac f6 9c be b7 b0 7b 09'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.140625  , ...,  0.015625  ,
            0.06738281,  0.06835938]), textin=CWbytearray(b'3e 58 44 09 94 9d c8 9f 97 27 89 69 81 33 18 83'), textout=CWbytearray(b'd0 d8 af 67 51 54 ef 19 70 35 e5 b4 87 f6 31 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14355469, ...,  0.02441406,
            0.07226562,  0.07226562]), textin=CWbytearray(b'6d be c8 59 bd 8d a2 03 e2 b0 41 b8 e8 ae ba 7d'), textout=CWbytearray(b'b4 c5 7a 4f ad d4 f2 6a 5e 96 c6 10 2c 24 f5 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.13964844, ...,  0.01464844,
            0.06542969,  0.06835938]), textin=CWbytearray(b'00 32 e9 48 9e 6e a1 91 5b 07 aa 87 9c 9e 6f 91'), textout=CWbytearray(b'34 99 e7 ff a4 c9 a1 36 f1 72 61 03 2d 78 ab 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13769531, ...,  0.00878906,
            0.06152344,  0.06640625]), textin=CWbytearray(b'4e ef 92 ec 98 53 e3 c4 96 9d 48 50 9f 4f 91 a0'), textout=CWbytearray(b'b0 c8 2e 37 d5 f3 44 1c e9 af 85 54 c1 dd 47 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.13964844, ...,  0.02246094,
            0.06933594,  0.07226562]), textin=CWbytearray(b'c6 4a ee 9a 72 49 b4 11 82 89 b8 29 4d 1e 24 e0'), textout=CWbytearray(b'da f0 a0 48 71 ef 0c c4 65 42 29 e6 ac e3 46 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.140625  , ...,  0.01757812,
            0.06738281,  0.06738281]), textin=CWbytearray(b'8c ef 4e e2 13 01 c6 b3 50 3c 9c c8 dc 66 14 27'), textout=CWbytearray(b'49 43 a3 f2 4e 53 6c d7 27 89 5f a5 8a 3a 8e fe'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14550781, ...,  0.01464844,
            0.06640625,  0.06835938]), textin=CWbytearray(b'40 89 8d 24 a7 d4 c9 2b d0 cf 79 2b 61 4b c9 6e'), textout=CWbytearray(b'eb e6 6c 18 f5 7e 2a 98 a1 90 08 57 c9 48 fe 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19140625, -0.14160156, ...,  0.00097656,
            0.05761719,  0.06054688]), textin=CWbytearray(b'1c 0f 42 79 7a c9 dc 7e f1 07 c5 fa 05 b1 68 6c'), textout=CWbytearray(b'f8 57 36 ab 55 0e bd e4 95 ab e3 6f da 62 7f 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18261719, -0.13964844, ...,  0.01269531,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'53 c2 24 d4 0b 4e 76 8b 65 9c 8e 16 14 c5 a2 de'), textout=CWbytearray(b'c9 79 e8 3a e3 af 0d ce 98 85 12 ba 82 84 51 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13964844, ...,  0.0078125 ,
            0.05957031,  0.06347656]), textin=CWbytearray(b'81 e4 83 55 f0 3f 7d 40 b6 52 d3 a2 35 52 59 8f'), textout=CWbytearray(b'f6 24 4a 50 20 c9 ee cc d6 48 85 a1 49 c8 fc e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18164062, -0.13671875, ...,  0.00976562,
            0.05957031,  0.06347656]), textin=CWbytearray(b'3a 89 4b 5e 80 9c 2d 1e 63 fd 49 fb 9f 64 d7 e2'), textout=CWbytearray(b'85 a8 c8 be ba 47 01 7a 00 45 20 56 77 f2 2b 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13769531, ...,  0.00585938,
            0.06054688,  0.06347656]), textin=CWbytearray(b'32 2b a5 52 a6 9b 72 6d 07 f7 56 c3 89 1e c3 dc'), textout=CWbytearray(b'38 42 69 13 f4 3a ad 99 3d eb 73 df 80 7d 87 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14257812, ...,  0.02246094,
            0.07128906,  0.07128906]), textin=CWbytearray(b'35 67 05 79 e2 ae 39 34 5f b3 3f 6f a7 93 81 7d'), textout=CWbytearray(b'b9 0c ac 0e 4d 4c 42 f7 fc 74 b8 20 a4 f5 80 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14257812, ...,  0.00390625,
            0.05566406,  0.06152344]), textin=CWbytearray(b'20 6b 3b 2f e6 3d e3 71 77 53 1a b6 d0 92 7e 70'), textout=CWbytearray(b'c4 95 6b 5d 09 94 21 12 c0 4f 44 74 85 bb 84 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.140625  , ..., -0.00097656,
            0.05371094,  0.06152344]), textin=CWbytearray(b'2c a4 0c 7c f4 05 bb bc a8 ac 30 2d 50 6d d8 66'), textout=CWbytearray(b'dd 98 0a 9d e4 ee 48 a2 66 54 85 62 e0 fd 14 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.140625  , ...,  0.01660156,
            0.06640625,  0.06835938]), textin=CWbytearray(b'36 54 02 cf a5 cf b9 27 6d c0 52 db 80 57 b7 41'), textout=CWbytearray(b'f9 7f 20 51 23 47 06 ea 4f 26 60 b0 b7 08 9d 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.02441406,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'd2 b2 0b e0 03 3c e7 b2 9a 68 c8 f1 ac 34 ab cb'), textout=CWbytearray(b'9f e2 d2 9e eb 71 6d 76 3f b8 da e7 b6 58 b6 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13867188, ...,  0.02050781,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'fa 49 66 05 7a ea 8c 91 ec 02 01 fc a7 28 bf bd'), textout=CWbytearray(b'ec 43 48 d5 57 ac 17 a7 a2 34 b8 7e 1a 1e 48 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18554688, -0.14355469, ...,  0.01660156,
            0.06640625,  0.06835938]), textin=CWbytearray(b'31 05 49 6f d8 6f f8 a7 f9 06 3e b2 35 49 07 83'), textout=CWbytearray(b'1e 4d 32 88 be 06 c2 fa be d9 d5 f1 0e 5b bf 6e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18457031, -0.13574219, ...,  0.01367188,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'7f f8 bb f0 b7 8e 8c e7 7a da fc 74 82 1e a9 bc'), textout=CWbytearray(b'2e e4 a7 05 f7 af 00 9b 4d 17 21 e4 96 34 01 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13769531, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'66 58 c7 dc b1 85 61 5a b3 a5 72 09 1e 28 95 58'), textout=CWbytearray(b'15 9d 18 b1 04 ec c0 60 0d 8c 70 53 90 42 90 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14453125, ..., -0.00097656,
            0.05371094,  0.05859375]), textin=CWbytearray(b'6f ca bf 27 5a b5 de 5a 8e 94 00 27 4d 74 fb 02'), textout=CWbytearray(b'9a 6b 9b 36 12 65 21 c5 22 73 b4 1a 96 9d 31 8b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14355469, ...,  0.02050781,
            0.06835938,  0.07128906]), textin=CWbytearray(b'c1 9e 22 15 5b 4f f5 8b b8 aa fb d7 30 87 e7 8b'), textout=CWbytearray(b'73 40 d1 3f 9e 54 88 ad 4f 69 92 bb a7 0a fc a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14160156, ...,  0.02050781,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'35 f7 1a a9 bd e1 48 a6 2a c7 73 58 37 9e 5e 4c'), textout=CWbytearray(b'91 ec b6 96 43 32 1a fe 85 9d ea 5d 43 1c 91 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18652344, -0.13574219, ...,  0.02636719,
            0.07226562,  0.07421875]), textin=CWbytearray(b'4e b9 37 bc d1 01 7f ff 1b 76 d8 4b a8 9a af bd'), textout=CWbytearray(b'54 ff ab 72 e3 0f 81 e7 41 0b 75 95 4a cc 72 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13671875, ...,  0.00878906,
            0.06054688,  0.06445312]), textin=CWbytearray(b'38 e2 be c0 5b ee 62 25 59 20 0e 6e 7d 5b 88 b1'), textout=CWbytearray(b'7b d7 f3 0a ff bc 50 78 74 65 d6 69 fa 96 5a c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14453125, ...,  0.01367188,
            0.06445312,  0.06542969]), textin=CWbytearray(b'8b 11 63 68 17 82 88 6a f0 64 b0 58 b9 cd 2b 8d'), textout=CWbytearray(b'29 ca af ca b9 51 84 1d 8b 4b 81 d0 89 0b 81 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19140625, -0.14648438, ...,  0.00585938,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'04 1a 34 cc af 5a 80 e0 d2 f1 5a 7f 26 0d 46 1d'), textout=CWbytearray(b'3d e4 be f1 7f 04 ee 31 85 a6 b4 2f 6d 32 83 d7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14257812, ...,  0.02441406,
            0.07324219,  0.07421875]), textin=CWbytearray(b'6d e8 52 25 9b 99 d3 19 c3 d0 01 47 3a 0d bb 12'), textout=CWbytearray(b'a5 cd 7b a4 67 e9 8f 1c 05 35 fb 70 a8 d9 94 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13769531, ...,  0.00976562,
            0.05957031,  0.06445312]), textin=CWbytearray(b'0e 7d c3 ff d9 c3 2c 4e 27 9a 38 eb 68 f4 a0 69'), textout=CWbytearray(b'c3 7f 69 2a 99 8e 1f 67 ea 7a 51 21 25 c1 1b d0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13867188, ...,  0.01855469,
            0.06835938,  0.07128906]), textin=CWbytearray(b'1c d4 ac ce 11 1e 87 56 3d 1f 58 69 f3 da 52 49'), textout=CWbytearray(b'5d e8 b5 72 f9 69 39 2c 19 f3 9a 71 6c 9b 9a ec'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.02441406,
            0.07226562,  0.07128906]), textin=CWbytearray(b'e4 84 18 4c 73 f9 a4 e3 36 28 2b 1c a5 81 df 3e'), textout=CWbytearray(b'd4 56 2a 8a de a1 b8 e1 ab eb 2d 1d 21 57 0b b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01269531,
            0.06152344,  0.06640625]), textin=CWbytearray(b'8e 93 dc b9 fc 69 8c 4e 5c ba 14 4a 62 c4 9e bf'), textout=CWbytearray(b'a4 5d 56 31 43 d3 fe 99 7e 6b 37 f9 4e 30 66 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14355469, ...,  0.01660156,
            0.06738281,  0.06835938]), textin=CWbytearray(b'51 10 ba 63 cd 6c 78 2e 41 40 1f 54 09 57 ab 15'), textout=CWbytearray(b'eb b2 7f 28 95 7b ad 15 cd e4 46 8a 7c 0f ec 42'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.17871094, -0.13378906, ...,  0.01464844,
            0.06347656,  0.06542969]), textin=CWbytearray(b'1b 97 b2 d7 67 a0 b0 cd 4a fa 8d 97 dc ee 23 b6'), textout=CWbytearray(b'cb 34 d8 40 de c2 d2 ba 69 23 9e 67 00 b3 36 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.140625  , ...,  0.        ,
            0.05371094,  0.05859375]), textin=CWbytearray(b'a3 16 bc 32 b6 3c 2b af af 8f 97 66 31 38 43 6f'), textout=CWbytearray(b'a0 01 4c 2b 31 04 ef c2 9e 2b 1b b1 9e 62 d0 05'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14257812, ...,  0.01269531,
            0.06445312,  0.06542969]), textin=CWbytearray(b'c1 dc ce 33 93 3a 2e 02 32 94 3d 6b b3 cd 57 96'), textout=CWbytearray(b'42 5b 82 35 90 ed 8f 67 04 41 a3 2a 3c 76 20 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14453125, ...,  0.00976562,
            0.06347656,  0.06640625]), textin=CWbytearray(b'aa d3 88 cd c6 6f 85 e3 91 54 89 22 b0 35 1d 0a'), textout=CWbytearray(b'e7 95 bc df 19 ca 2c 8b 8c 10 28 bc a7 ff 9f e8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14257812, ...,  0.01757812,
            0.06738281,  0.06835938]), textin=CWbytearray(b'92 75 ef 4a 30 27 74 39 ca 36 2b ca 0d 76 73 66'), textout=CWbytearray(b'b8 38 b6 44 31 5f 99 19 48 81 d7 4a 6d 12 49 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.01171875,
            0.06347656,  0.06835938]), textin=CWbytearray(b'9b 29 1b f9 f2 e4 d0 70 44 8c 01 92 a9 80 2f e8'), textout=CWbytearray(b'ad ce 35 7a 0a 30 0f f6 7f d9 5e 6b 08 4f 57 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13574219, ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'1f e6 86 05 2e 0f eb 18 96 e2 90 5a 9b d9 37 f3'), textout=CWbytearray(b'd9 4b 87 33 27 ec 36 91 3e e5 87 54 6d d1 b1 a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14453125, ...,  0.02539062,
            0.07226562,  0.07324219]), textin=CWbytearray(b'01 44 9c a8 c6 44 7d f7 83 2d 9f c3 2a 4c 22 18'), textout=CWbytearray(b'7e 2b 01 c8 d2 d5 83 d3 c3 8e 61 4c 62 1f 3e 61'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13964844, ...,  0.01855469,
            0.06835938,  0.06933594]), textin=CWbytearray(b'93 ea 81 96 44 95 d0 30 fd 1d eb 1c 9f 1b 3f c0'), textout=CWbytearray(b'fa 02 65 dd cf 02 8e 47 7e a4 63 0f ab 59 f4 fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.14160156, ...,  0.01464844,
            0.06347656,  0.06738281]), textin=CWbytearray(b'41 92 4e 91 1c cc f4 9a cf b5 d2 5a 0b f2 32 3e'), textout=CWbytearray(b'25 ce 5f d0 8f 41 a1 ac 33 9f 98 76 a2 9e 78 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18652344, -0.14355469, ...,  0.01367188,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'16 fa 40 ae 71 83 50 a1 d3 79 d0 aa 35 2b 44 bb'), textout=CWbytearray(b'58 37 d1 40 21 e4 30 a2 f0 0c 3f ff 1e 20 41 c2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13574219, ...,  0.00488281,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'0e a6 cc f4 37 e5 f9 0c ae 32 bb ca fe 1b 26 c8'), textout=CWbytearray(b'5a 49 ec 92 90 74 94 2c 77 c7 c1 72 56 45 d8 46'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.140625  , ...,  0.01171875,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'b4 00 24 00 46 81 22 8f bf 73 3a 68 b8 27 8b 08'), textout=CWbytearray(b'57 48 25 aa 7b f4 ec 82 4c 6a 98 c3 29 64 8c 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14257812, ..., -0.00195312,
            0.05175781,  0.05761719]), textin=CWbytearray(b'7f 99 93 2f bc 1f e5 5f 3f 7b 33 93 bc 0a dd 64'), textout=CWbytearray(b'1b 4e d0 62 a9 2f 63 59 09 d1 8b 36 58 27 26 f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19238281, -0.14257812, ...,  0.01367188,
            0.06542969,  0.06738281]), textin=CWbytearray(b'20 7f e4 64 93 2d bb 74 43 b0 53 ec cf 3b 68 98'), textout=CWbytearray(b'71 ba 21 b1 f1 25 fe 90 ba 6c ee 43 a6 3d 7d bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18652344, -0.14257812, ...,  0.00585938,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'c8 11 b3 08 26 c2 8a 3e 1d f6 b7 5a f3 c7 b3 6e'), textout=CWbytearray(b'ad e3 00 9d ac 1c ba 52 db 30 0b 85 a2 4e 08 53'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19335938, -0.14160156, ...,  0.01464844,
            0.06542969,  0.06738281]), textin=CWbytearray(b'b6 f7 0a 0c f2 d5 f7 ed ce 47 c1 5c d8 6b 37 17'), textout=CWbytearray(b'23 f2 55 7b 20 25 bf 86 0b e6 18 ae c3 e9 02 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19433594, -0.14355469, ...,  0.01074219,
            0.06152344,  0.06640625]), textin=CWbytearray(b'58 82 1e 33 02 12 bc ed 15 45 6f 0f 57 14 07 3a'), textout=CWbytearray(b'fb b5 1c aa 0e c8 fc 30 b2 ff 09 92 a5 25 b8 e9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13867188, ...,  0.01074219,
            0.06152344,  0.06738281]), textin=CWbytearray(b'ff 0d af 89 18 d7 1b 2d 7b 26 06 a0 e9 65 ac 66'), textout=CWbytearray(b'92 eb 11 80 c6 92 f8 0b 30 b1 6f 78 4b e2 44 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14453125, ...,  0.01269531,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'c9 55 ce 89 54 35 9f 0c 38 b2 43 15 58 4c 22 3e'), textout=CWbytearray(b'26 fe 6c f2 33 d4 15 2c 6e 38 3c 1d 8b c9 2f 0e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14257812, ...,  0.01269531,
            0.06152344,  0.06542969]), textin=CWbytearray(b'f7 e6 b9 7f 58 6b 4e d9 96 7f e0 ca 24 a0 4f 32'), textout=CWbytearray(b'a4 a5 f8 37 fd 0e 44 ff 50 ef ca 35 bd f6 a0 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13769531, ...,  0.01855469,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'a5 a3 10 3e af 11 50 3a cf 07 42 2c 88 00 ae dc'), textout=CWbytearray(b'c1 4f ce 47 3e 8c 12 27 65 6e 43 33 e8 1e b0 e1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14160156, ...,  0.01464844,
            0.06445312,  0.06933594]), textin=CWbytearray(b'49 68 0e 9e 6e 1a 61 da d2 90 f4 53 e6 06 7c 08'), textout=CWbytearray(b'63 04 17 03 04 9d 4f 53 e7 7e e2 65 8f 73 91 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18554688, -0.140625  , ...,  0.02050781,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'5e a0 66 c5 46 36 e0 de dc 45 19 74 57 de bc 52'), textout=CWbytearray(b'5d 0b 43 f4 5f 78 c9 3e 52 11 8e 1f 61 43 c7 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14257812, ...,  0.00683594,
            0.06054688,  0.06542969]), textin=CWbytearray(b'9f 53 6e bd c9 8e 1b 4a 4f 93 39 18 c6 52 8e 19'), textout=CWbytearray(b'c6 8b 09 19 7b 04 2f eb cf f5 4a 9b 66 0d 7a a8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18457031, -0.13769531, ...,  0.00976562,
            0.06152344,  0.06542969]), textin=CWbytearray(b'95 2e 3d 2f be 87 b8 cb 0d 8a dd 21 fb 22 02 be'), textout=CWbytearray(b'ff 01 e0 39 61 f8 72 5c 40 ac d3 59 e7 88 ab 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14160156, ...,  0.015625  ,
            0.06542969,  0.06835938]), textin=CWbytearray(b'67 cc 6b 07 80 a1 43 1a 15 67 88 04 ba 93 62 0f'), textout=CWbytearray(b'f0 bf a9 ef 99 ea 69 ea f5 f3 1d 2f e0 5a 19 15'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.13964844, ...,  0.01367188,
            0.06542969,  0.06835938]), textin=CWbytearray(b'3c be 47 10 9b 2e 75 bb c0 60 b1 ab a5 65 25 86'), textout=CWbytearray(b'a5 8c c2 89 3c 3f 6b f7 f4 aa f5 bd 99 12 6a 94'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14550781, ...,  0.01269531,
            0.06347656,  0.06445312]), textin=CWbytearray(b'54 cb e6 d9 53 ad c1 34 9f 8b ed 4c f1 8f 2b 14'), textout=CWbytearray(b'6a c1 8c f3 aa 4c cf bd 48 59 db 13 9c ba ea 8e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13964844, ...,  0.0078125 ,
            0.05957031,  0.06542969]), textin=CWbytearray(b'2e 58 9e 84 b2 a9 44 50 62 1e 88 48 19 f4 b8 06'), textout=CWbytearray(b'54 9f 08 29 14 20 f9 ee fc 3d ed 9b 84 0e 13 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14453125, ...,  0.02636719,
            0.0703125 ,  0.07519531]), textin=CWbytearray(b'a1 11 da a2 2a 05 35 58 3b 4e d2 1c 28 24 51 1e'), textout=CWbytearray(b'4e ba cd 0a 74 97 bc 98 95 77 cb d1 c9 85 73 ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.19238281, -0.140625  , ...,  0.00878906,
            0.05859375,  0.06347656]), textin=CWbytearray(b'79 d2 52 ac b8 f7 96 78 dd 1a 0a b5 2e 5a 2d 73'), textout=CWbytearray(b'bc 51 14 03 46 6e 8a 4f 57 0a 84 92 3d 31 f8 dc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14160156, ...,  0.02636719,
            0.07226562,  0.07324219]), textin=CWbytearray(b'e6 46 95 78 d1 cc d6 b6 93 32 a3 ef 51 5d b5 98'), textout=CWbytearray(b'61 9c e9 b3 0f 7a 9b 94 0f 51 dc c0 ad db 72 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14257812, ...,  0.01855469,
            0.06933594,  0.07128906]), textin=CWbytearray(b'30 d0 1e ac 3a 41 38 8e 39 48 b6 b8 cf fd 22 9a'), textout=CWbytearray(b'5e 57 e9 78 a3 71 44 bf 78 18 22 b3 42 fe 56 8f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18261719, -0.13867188, ...,  0.00683594,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'7a 86 83 13 8b a9 26 20 5c 73 a6 21 24 b9 9e ed'), textout=CWbytearray(b'81 66 2c 00 35 6a fa e4 c0 dc 50 06 0e 2e 96 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18066406, -0.13476562, ...,  0.01269531,
            0.06738281,  0.06640625]), textin=CWbytearray(b'd5 6d f9 59 93 60 f9 5f 36 9c 78 f5 6a 25 70 d2'), textout=CWbytearray(b'ea ad 66 a1 b7 60 90 db 67 9f 48 48 f6 12 7a ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14160156, ...,  0.02246094,
            0.07128906,  0.06933594]), textin=CWbytearray(b'33 cc 8d 16 a8 e0 16 f4 68 82 41 c4 8d e6 e5 0a'), textout=CWbytearray(b'b8 13 22 f5 ca 9e 2f ae 48 1f 8b 89 93 d4 d8 95'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14355469, ...,  0.        ,
            0.0546875 ,  0.05859375]), textin=CWbytearray(b'ed 1b b0 13 ae 92 d5 28 3d 09 d4 f8 0b 23 b5 9a'), textout=CWbytearray(b'ab 1d d2 fb bf a4 d0 03 a7 d9 7a 6e 25 4d 00 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18945312, -0.13574219, ...,  0.01855469,
            0.06835938,  0.07128906]), textin=CWbytearray(b'6d 9b c5 6e 33 c8 36 4f af 9f c7 1e 9e 1e 45 fd'), textout=CWbytearray(b'52 52 8d ff 1a 33 c3 37 1e 2e e7 02 17 47 f8 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18847656, -0.140625  , ...,  0.03027344,
            0.07519531,  0.07617188]), textin=CWbytearray(b'2a bd f6 b1 33 05 ac 11 d0 fc 8d 9a 95 72 7d ae'), textout=CWbytearray(b'd2 28 8e f6 2f 86 1c d7 f5 63 bd c1 28 44 89 be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18359375, -0.13964844, ...,  0.01660156,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'd7 33 8d bc c5 a0 07 5f 4b 1b 45 68 6c dd c5 01'), textout=CWbytearray(b'74 91 bf 17 5a 70 58 73 51 8e 65 58 6b 5b f4 ba'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.03613281,
            0.08007812,  0.078125  ]), textin=CWbytearray(b'9d bc 1a e0 73 78 00 0c f2 7e 53 a8 55 c2 62 38'), textout=CWbytearray(b'44 d0 9f 3c f8 67 70 a6 1d 38 c2 70 85 2e e1 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14257812, ...,  0.00683594,
            0.06152344,  0.06347656]), textin=CWbytearray(b'95 1a eb 9d 6b d7 4f 93 1c 63 93 b3 20 a7 4d 26'), textout=CWbytearray(b'fa 83 bb da 27 7a 00 d4 4b f0 79 5b a8 cc fb eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19335938, -0.14648438, ...,  0.01757812,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'8d 84 78 6d d5 71 ab c4 1e a1 77 a0 0d 72 22 9a'), textout=CWbytearray(b'70 67 a1 7a 53 3f db a5 72 f4 4a 8e d6 7f 07 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14453125, ...,  0.01855469,
            0.06835938,  0.06835938]), textin=CWbytearray(b'44 cf f5 a3 3c ef 72 41 a4 31 e8 7c 67 f9 c3 5a'), textout=CWbytearray(b'ce 62 95 32 97 84 35 af 82 3b 52 9c 3b a6 1e 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18164062, -0.13574219, ...,  0.01660156,
            0.06445312,  0.06933594]), textin=CWbytearray(b'ff ee 5a d8 aa 35 e6 57 60 d9 08 92 46 61 b9 c4'), textout=CWbytearray(b'f0 53 10 de 46 c5 88 a1 9a a1 1e 07 8d ed 09 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14355469, ...,  0.00878906,
            0.06054688,  0.06445312]), textin=CWbytearray(b'64 e4 d6 ed 78 47 01 8d ca d4 e4 5f 6c ce 83 07'), textout=CWbytearray(b'3e 93 3c 75 1d 91 4d 18 26 c5 c9 ce 54 cc bc db'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14550781, ...,  0.00488281,
            0.05859375,  0.06445312]), textin=CWbytearray(b'7d 4b 00 01 d5 8c 9e 86 a6 cf 1f 33 f7 7f d9 4a'), textout=CWbytearray(b'e6 da a8 42 73 b9 d8 6d d2 cb 71 17 7b 0b b4 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14453125, ...,  0.01953125,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'68 bf 49 d5 7f 9b bb ec f0 4a 0e 7c 48 04 00 61'), textout=CWbytearray(b'3d d8 84 e7 ae 2f 0d 5f 80 fb 52 c1 fa fe 6c 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13671875, ...,  0.02050781,
            0.07226562,  0.07324219]), textin=CWbytearray(b'56 07 53 2d 86 fa 10 46 e5 be 09 7f ed ca 96 bb'), textout=CWbytearray(b'03 e4 c2 13 58 fa e1 e6 65 84 c0 14 d1 45 80 f7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ...,  0.00488281,
            0.05859375,  0.06054688]), textin=CWbytearray(b'92 77 1a 1d 7c a2 61 be b9 88 40 db 02 fa 36 0d'), textout=CWbytearray(b'04 e9 08 78 8e 66 aa 73 b2 26 0f f7 75 86 64 f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13671875, ...,  0.02050781,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'26 c0 62 e1 03 fb 13 29 a9 e5 18 eb bb a3 12 a8'), textout=CWbytearray(b'18 69 54 1d 70 bb 42 3a 7c b4 a8 6b e2 ff 74 e3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.0078125 ,
            0.0625    ,  0.0625    ]), textin=CWbytearray(b'08 0f 40 73 a2 60 68 a2 50 3f 38 29 e7 11 5c 82'), textout=CWbytearray(b'f7 69 7a 41 a7 cf 84 cf fd 71 91 a8 79 7e 7e 8c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13769531, ...,  0.01269531,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'e0 98 6b 44 d3 31 60 63 89 33 42 84 c4 27 e8 27'), textout=CWbytearray(b'67 36 8a aa f8 d1 10 28 7c d3 02 c8 6f f0 67 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.14355469, ...,  0.0234375 ,
            0.07226562,  0.07324219]), textin=CWbytearray(b'df 1e 81 23 6b 52 31 78 ad d7 5d 18 ce 36 23 6b'), textout=CWbytearray(b'65 14 e8 89 c3 fc d4 ee 1d 22 1c de 2d d5 60 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18261719, -0.13671875, ...,  0.01464844,
            0.06347656,  0.06542969]), textin=CWbytearray(b'5c a8 d5 c1 84 aa 05 da 70 1a 15 89 fd dc af d4'), textout=CWbytearray(b'77 e2 72 57 95 69 72 d9 f0 06 0c a2 59 f8 85 d6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.140625  , ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'45 3f ac 50 9f 31 3f 67 cf 0a 61 95 f8 7d 27 4c'), textout=CWbytearray(b'9f 2a 41 3e 36 1b b0 90 6f f9 59 94 64 96 34 b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19238281, -0.14355469, ...,  0.01855469,
            0.06933594,  0.06835938]), textin=CWbytearray(b'96 46 8a 82 45 b3 81 87 8e 41 01 d3 45 40 5a 4e'), textout=CWbytearray(b'91 d6 2e 41 e8 e0 1c 4b c6 e6 41 4f 9c 8c c3 ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.13769531, ...,  0.01953125,
            0.06738281,  0.07128906]), textin=CWbytearray(b'3a 40 4e bd f9 c2 c3 55 42 2f 61 78 42 2f a2 2d'), textout=CWbytearray(b'51 40 0a 0c 3e 19 29 f3 f7 c3 52 17 b7 d4 54 9a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13769531, ...,  0.0078125 ,
            0.06054688,  0.06347656]), textin=CWbytearray(b'6d e6 c1 74 65 eb 38 fe 52 2d 8a 41 f7 2c e9 71'), textout=CWbytearray(b'd1 04 7f 06 ac d8 d6 47 62 ac 3e c8 6b 54 7f 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14453125, ...,  0.01171875,
            0.06445312,  0.06445312]), textin=CWbytearray(b'9b be 30 a9 de 73 1a b4 3a 7a c7 14 88 68 5c 26'), textout=CWbytearray(b'0e 4b a4 2e 84 f4 93 cb 9f 11 5c b6 72 ef 43 a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.1875    , -0.13769531, ...,  0.01757812,
            0.06738281,  0.07226562]), textin=CWbytearray(b'e9 b6 ef ca ce b8 5e 5c 9a 98 b1 70 72 d4 ef 8f'), textout=CWbytearray(b'e1 c9 5a 8f eb 8a b2 d8 fa 20 a6 e2 52 0c 11 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14257812, ...,  0.01953125,
            0.06738281,  0.07128906]), textin=CWbytearray(b'ee f7 b2 83 c5 2b 48 a0 d7 35 43 f0 72 cc 61 79'), textout=CWbytearray(b'f9 ea 37 b1 a7 ca 40 c2 40 44 79 b7 38 b3 22 1f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14355469, ...,  0.00683594,
            0.06347656,  0.06347656]), textin=CWbytearray(b'd8 e4 11 47 5a 5b 88 ae 31 dc bd 16 33 30 12 0b'), textout=CWbytearray(b'eb 79 ef ce 06 c8 f4 aa 4d d9 13 7d eb cf 94 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.01367188,
            0.06152344,  0.06738281]), textin=CWbytearray(b'8c 00 d4 a6 fc 1c 75 02 8f b2 2b da c6 96 dc b6'), textout=CWbytearray(b'66 33 69 5c 30 75 ac c6 f4 d8 af 70 d6 6c 49 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13964844, ...,  0.01074219,
            0.06347656,  0.06542969]), textin=CWbytearray(b'8f 06 d4 43 ea 90 86 5a 26 17 e8 40 d8 5d 74 f2'), textout=CWbytearray(b'd7 5a 74 02 71 8b 39 53 61 e9 8e 51 61 3f 76 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14355469, ...,  0.01953125,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'92 d9 f4 2b d0 f0 93 74 6e 1d 2b 32 e6 e5 9b 69'), textout=CWbytearray(b'b6 08 96 0b 63 93 b3 8e 83 de ad df 4c 78 ed c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18457031, -0.14160156, ...,  0.00683594,
            0.05957031,  0.06347656]), textin=CWbytearray(b'38 5c fa e8 31 d3 7b ea 64 b8 5d c0 37 37 ce 67'), textout=CWbytearray(b'12 32 5a ff 21 25 23 4c 11 91 35 57 b5 c4 4e 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18945312, -0.13964844, ...,  0.01757812,
            0.06738281,  0.06835938]), textin=CWbytearray(b'b1 b5 b8 77 a1 0b 0c 8a f8 20 9d 90 8e 19 4c fd'), textout=CWbytearray(b'37 2e 0e 9d 2f 27 49 1f b9 3a 7f 4d 4a 3f 0e 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.140625  , ...,  0.01269531,
            0.06542969,  0.06542969]), textin=CWbytearray(b'a6 60 0f 9c 55 62 c5 66 ec 8d 9e eb 10 78 14 db'), textout=CWbytearray(b'a0 3a 39 e4 b4 04 4c bf 6b 92 a1 17 76 03 3d f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13867188, ...,  0.0078125 ,
            0.05761719,  0.06347656]), textin=CWbytearray(b'7b e7 b2 6b de 62 08 c0 d0 0b 69 8a 71 08 fd 62'), textout=CWbytearray(b'eb 8d ea 9f 29 a1 99 ac 4a eb 27 cd 1f 87 ef f3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.1875    , -0.14355469, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'a8 c8 3f 72 90 c9 a9 95 09 ab 2e 5e f4 52 f5 33'), textout=CWbytearray(b'7a d6 80 15 30 76 0e d3 4d f5 e2 3a 49 90 d0 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14355469, ...,  0.01855469,
            0.06738281,  0.06738281]), textin=CWbytearray(b'2a cd 08 23 ea ed 1c a6 20 9e d2 7b db 7e 06 05'), textout=CWbytearray(b'60 9d 7d 51 2a 78 1a 03 6c 1e 13 d5 96 72 85 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13964844, ...,  0.00585938,
            0.05957031,  0.06152344]), textin=CWbytearray(b'63 7d c4 b2 e5 1b 46 de d1 50 e8 74 13 b1 97 46'), textout=CWbytearray(b'ab ba 48 5c dc d0 6d f6 f1 20 fc 8d 87 5a bb 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18652344, -0.14550781, ...,  0.00195312,
            0.05566406,  0.0625    ]), textin=CWbytearray(b'aa 6a 50 53 f9 25 88 3c 02 b9 69 a1 c5 fa 6d 60'), textout=CWbytearray(b'e8 8c 5c ac f5 2f 53 f7 f4 e5 86 11 c6 ab d5 c1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.140625  , ...,  0.00683594,
            0.05566406,  0.05957031]), textin=CWbytearray(b'5c d7 ea e4 57 76 4e c2 b8 da 54 d9 73 05 cc ed'), textout=CWbytearray(b'3a 6a e9 02 67 4b 09 34 8b ea ca d8 4b 95 60 60'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14257812, ...,  0.01367188,
            0.06542969,  0.06738281]), textin=CWbytearray(b'66 1d 8b 6d 4d 02 3e 2b fd ec 58 35 59 ac 10 44'), textout=CWbytearray(b'8f d9 b6 e1 4b 27 3c e2 a7 a9 95 5d 3c 85 8e 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.14160156, ...,  0.01367188,
            0.06445312,  0.06933594]), textin=CWbytearray(b'68 d1 95 e4 3f 41 63 bb 48 46 ae 64 ec f7 0c d2'), textout=CWbytearray(b'a6 ea af 87 b6 a6 99 f3 02 d4 a5 54 30 7e 1c 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13574219, ...,  0.01855469,
            0.06835938,  0.06738281]), textin=CWbytearray(b'd1 6a 52 be 32 2e 53 51 e2 68 f7 0c 42 02 e0 de'), textout=CWbytearray(b'dc 27 85 7d fb 08 07 65 a1 89 c1 e7 9a b7 6e 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.14257812, ...,  0.00488281,
            0.05859375,  0.06347656]), textin=CWbytearray(b'9b 22 05 48 a0 46 3d 0e 9b 89 88 25 cf 1d b3 35'), textout=CWbytearray(b'59 a4 bd 89 53 c0 93 b5 10 18 28 74 8c 6e 99 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18164062, -0.13378906, ...,  0.01464844,
            0.06445312,  0.0703125 ]), textin=CWbytearray(b'e8 cd 36 78 f0 90 6f 5c 4b 07 82 2d bf 0f bb b8'), textout=CWbytearray(b'd0 e0 b8 07 24 24 57 44 48 70 a0 df c6 45 f6 20'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19628906, -0.14453125, ...,  0.01660156,
            0.06738281,  0.06738281]), textin=CWbytearray(b'1b 6a 11 19 db f6 87 8c 29 96 0b 79 16 f4 8c 8f'), textout=CWbytearray(b'e4 91 3c ef 7a 37 64 65 98 96 b2 fa 6a c2 f9 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13671875, ...,  0.01953125,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'dc a7 a9 7d 3c 9e 55 9e 35 80 af ff 13 1d 6f b3'), textout=CWbytearray(b'45 82 a9 22 54 d0 19 31 95 98 f0 7b 17 fa fe 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14257812, ...,  0.01269531,
            0.06347656,  0.06542969]), textin=CWbytearray(b'c2 90 cb bb 6c dc de 72 56 bc 76 44 25 8f e0 88'), textout=CWbytearray(b'c3 b2 90 33 e8 f5 73 e8 cd 5a 23 fa 4a 1c c6 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18652344, -0.13769531, ...,  0.00683594,
            0.05664062,  0.06152344]), textin=CWbytearray(b'08 7d aa 33 b8 b2 ce 82 09 a4 45 5a d2 4a 15 f2'), textout=CWbytearray(b'14 5c 94 8b 64 d9 f7 a6 39 07 42 27 67 b2 56 75'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13769531, ...,  0.        ,
            0.05371094,  0.05957031]), textin=CWbytearray(b'a1 d8 a2 ff 5c 5a 56 8c 54 06 87 22 a4 a3 35 70'), textout=CWbytearray(b'18 25 ce ad fc 40 ec 8d 15 a9 05 a4 bf f4 bd a3'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.00390625,
            0.05957031,  0.06152344]), textin=CWbytearray(b'8c 94 02 bf 88 17 bd 1c 75 f0 cd 47 10 9a d5 99'), textout=CWbytearray(b'1b 21 e4 ab 89 ef 5c 46 94 32 59 24 ed 92 93 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.13964844, ...,  0.00976562,
            0.06445312,  0.06542969]), textin=CWbytearray(b'c6 2d 4c 6f 5b 69 78 52 b8 3c 17 65 40 5f e8 47'), textout=CWbytearray(b'd2 99 69 0a f9 ff c2 cd 74 da 7b a4 bd 7d 48 e0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18359375, -0.140625  , ...,  0.01660156,
            0.06738281,  0.06933594]), textin=CWbytearray(b'92 2a ce 5a ed bd cd 6b 79 f5 c8 64 3f 5b 47 ca'), textout=CWbytearray(b'0f ea 48 64 b3 02 33 e6 28 1d 8d 9d aa be 97 37'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.14355469, ...,  0.01855469,
            0.06542969,  0.06933594]), textin=CWbytearray(b'fe 8e 81 54 91 6d f4 46 82 79 35 84 71 a7 75 32'), textout=CWbytearray(b'f8 7b f0 fa b0 96 24 bb 4f 14 f3 bb 55 e4 62 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13574219, ...,  0.0078125 ,
            0.05566406,  0.06152344]), textin=CWbytearray(b'82 fd f4 b3 0a f6 14 65 47 83 ff 38 a1 41 55 d7'), textout=CWbytearray(b'41 99 d4 21 1e 27 f0 fd 8a 2b d2 9f c8 44 04 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14160156, ...,  0.02832031,
            0.07617188,  0.07519531]), textin=CWbytearray(b'72 59 ae ce 93 b3 44 20 7c 34 9f b5 a3 20 b7 74'), textout=CWbytearray(b'34 e8 73 90 cb 6d b6 e6 b1 f5 fd 88 9b fb 07 84'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14160156, ...,  0.01269531,
            0.06445312,  0.06640625]), textin=CWbytearray(b'b4 13 d8 4e 32 79 c8 aa 0f 92 fb a1 12 9c 3b 61'), textout=CWbytearray(b'46 f9 75 62 f8 3b 55 ba 73 58 ef f2 a9 74 2f c8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13769531, ...,  0.01757812,
            0.06640625,  0.0703125 ]), textin=CWbytearray(b'3a 51 41 c2 c9 a0 e1 5e 7d dd 86 3c bb 8e 00 03'), textout=CWbytearray(b'a7 96 0c fc e3 27 f5 b2 74 6f 43 69 3e 46 ca cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14550781, ...,  0.01953125,
            0.06933594,  0.06933594]), textin=CWbytearray(b'db 09 70 ce 90 17 c0 e1 0d db 41 c2 68 87 5a 15'), textout=CWbytearray(b'88 7f ff 3e 5a e8 60 80 a6 d2 28 a9 74 97 8a 31'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14160156, ...,  0.00390625,
            0.0546875 ,  0.06347656]), textin=CWbytearray(b'e1 dc c3 25 25 0f 98 14 d0 87 e7 1e df af b7 2f'), textout=CWbytearray(b'72 c4 7e ac 42 9f 91 de ae 82 85 17 1d 26 60 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18945312, -0.13964844, ...,  0.01855469,
            0.06738281,  0.06738281]), textin=CWbytearray(b'3b 40 00 18 91 f7 e1 4d ad 64 1f 91 84 16 32 3b'), textout=CWbytearray(b'cf d7 14 65 54 6a f1 7b 56 a5 88 9f fd c1 0f ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19433594, -0.14550781, ...,  0.00097656,
            0.05664062,  0.06152344]), textin=CWbytearray(b'a5 78 c9 75 37 35 08 15 62 46 26 54 d1 e4 80 1a'), textout=CWbytearray(b'61 75 bd 9a 8f 10 5f d1 33 44 c1 08 b6 69 2f 0b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18457031, -0.13769531, ...,  0.01757812,
            0.06640625,  0.06933594]), textin=CWbytearray(b'9d 66 62 ba 1b 44 ae 49 67 31 67 7d 20 46 c0 a6'), textout=CWbytearray(b'44 28 55 5e c4 e8 09 16 0b 1b f7 81 16 44 66 1c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.14160156, ...,  0.00585938,
            0.05664062,  0.05957031]), textin=CWbytearray(b'1c 15 3a c3 35 52 da 8f 09 40 02 cc aa d1 1b 13'), textout=CWbytearray(b'7e a8 df b9 5c 15 d6 00 a1 05 00 c4 51 db 16 fb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.13867188, ...,  0.00488281,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'6f 1f 15 e4 b2 c6 81 23 49 1f ee 40 50 31 a5 eb'), textout=CWbytearray(b'16 48 7d ee 5b a5 92 58 7e da f7 37 4a ef 56 87'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14355469, ...,  0.00488281,
            0.05957031,  0.06152344]), textin=CWbytearray(b'd7 b2 7d 7c 64 dd 86 a2 fd 78 78 98 58 89 7e 00'), textout=CWbytearray(b'14 22 13 61 02 af 3f db 1f 0d aa f0 06 a1 e8 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.01953125,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'5d 7c 77 a8 d5 32 ce d8 16 4e 8e 7f 19 fb e6 be'), textout=CWbytearray(b'7f a0 38 3e ea a8 b1 ea 64 4d 2b 70 40 f8 0a 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13671875, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'd3 22 66 e9 70 fd e5 df 6c 19 58 54 2e 2b b0 f3'), textout=CWbytearray(b'1f 2a 90 57 51 fb e5 c8 03 aa f1 d0 90 74 72 9d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13964844, ...,  0.02441406,
            0.06933594,  0.07226562]), textin=CWbytearray(b'db f9 ad 5c af 29 40 fc 06 71 cd bb 98 2c 2d f9'), textout=CWbytearray(b'ef 0a 7a db 43 d5 d5 2a 65 eb 73 77 cf 12 ce 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13867188, ...,  0.01074219,
            0.06152344,  0.06542969]), textin=CWbytearray(b'ac 2e 41 ef 03 8d d0 38 98 9b 64 16 3e 78 c5 92'), textout=CWbytearray(b'36 06 65 b0 2c 83 f5 f2 43 9d ca d4 ec 3c 2c 6f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18554688, -0.14160156, ...,  0.00878906,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'e7 df 52 e3 b0 bd ea 5e e2 cf f6 c2 7c 3e 26 da'), textout=CWbytearray(b'8a d6 08 f7 a4 aa dc 0a ea 2e 6a f1 ee 9f 4b 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13476562, ...,  0.00585938,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'b9 26 0a f2 7b 53 1a ab 34 83 21 a7 56 15 9f e4'), textout=CWbytearray(b'0f 7c a9 d2 10 f2 f3 be 61 32 7b 9c 4d ee a0 79'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01855469,
            0.06738281,  0.06933594]), textin=CWbytearray(b'6a 39 ba 4e 82 64 a9 1d 30 c2 91 c3 be 33 d3 13'), textout=CWbytearray(b'4c d5 c0 b5 76 08 ab 92 e7 30 d2 a5 30 cb be be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13574219, ...,  0.00878906,
            0.06152344,  0.06347656]), textin=CWbytearray(b'54 fe a1 ff 5d 7c 09 29 c4 ff fc 11 da ea 61 69'), textout=CWbytearray(b'89 37 b8 94 06 48 76 e0 ab 78 70 79 63 2c 9a ad'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.14160156, ...,  0.00683594,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'd5 69 0e a5 34 ac c3 b2 23 ba e7 1c 11 de 63 08'), textout=CWbytearray(b'52 c4 47 97 1e 3a ce 69 04 be a5 7c b1 e1 b3 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18945312, -0.13964844, ...,  0.00878906,
            0.06054688,  0.06445312]), textin=CWbytearray(b'0e 24 3e f4 06 d9 a3 cf 36 d6 78 b1 5e 18 fe 7f'), textout=CWbytearray(b'f3 68 58 12 a1 07 6b 47 86 ae 3b e1 82 1f 01 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14355469, ...,  0.02246094,
            0.0703125 ,  0.07421875]), textin=CWbytearray(b'0a d9 23 bb 7a 28 f8 44 58 62 aa 02 ee b6 3b 22'), textout=CWbytearray(b'9d 8d a0 c1 31 29 a5 9f 65 dd 95 b8 e7 e1 1b fc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19238281, -0.14160156, ...,  0.00683594,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'34 aa fd 14 22 7b 6e 1e f5 19 fe f8 2a 32 5c 0a'), textout=CWbytearray(b'c6 09 04 db 57 64 97 44 26 76 4c 39 05 ad 24 eb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14355469, ...,  0.01660156,
            0.06445312,  0.06640625]), textin=CWbytearray(b'6a 66 77 cf 38 fe db ec 6a 48 e8 a4 32 51 6d 8a'), textout=CWbytearray(b'0b cc d6 9e e0 5d f8 f5 7e 9b 2c 4c dd b2 03 96'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13964844, ...,  0.00585938,
            0.05957031,  0.06152344]), textin=CWbytearray(b'a7 7d 7f c2 36 7c 2a 7d 3d e8 92 96 3a ee d7 2e'), textout=CWbytearray(b'17 77 25 f6 4f b3 70 aa d2 68 cf 80 09 05 4d b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.13964844, ...,  0.00488281,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'8a 9a cd 95 57 c4 2d f9 27 43 53 6c a7 18 65 65'), textout=CWbytearray(b'8a 9a f8 da 72 70 39 7b e7 40 b4 ef 87 7b d5 bb'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14257812, ...,  0.02441406,
            0.07324219,  0.07519531]), textin=CWbytearray(b'39 fa 89 bd 37 10 b3 ac ac 45 72 a4 0b 4d 91 4c'), textout=CWbytearray(b'01 2e ea b8 1e 21 4c a6 ff 0a b9 a7 ca 10 40 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14453125, ...,  0.00097656,
            0.05371094,  0.05957031]), textin=CWbytearray(b'c4 72 8d 19 71 7c fa 3a e8 ee 7c ca b6 ad a0 0a'), textout=CWbytearray(b'88 2a d0 9c 07 14 ab e8 d5 d1 aa 88 e8 d3 8e a7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13671875, ...,  0.01367188,
            0.06347656,  0.06738281]), textin=CWbytearray(b'77 4b 63 57 c1 9a dd dc dd 18 78 01 06 c6 b3 69'), textout=CWbytearray(b'6c 2a 6b 7b f2 5b 85 79 7c 09 a5 d1 6c ab 9b ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14355469, ...,  0.02734375,
            0.07519531,  0.07421875]), textin=CWbytearray(b'92 cc 9d 2a 7e 5a f4 eb 78 50 49 68 3e 29 07 5b'), textout=CWbytearray(b'aa fc 44 d0 40 9b 66 c0 8e 8b 69 f6 af b4 a5 ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19335938, -0.14257812, ...,  0.00976562,
            0.0625    ,  0.06835938]), textin=CWbytearray(b'26 ae e5 ef 85 cb 99 61 b4 bf 61 d8 6a a7 91 4a'), textout=CWbytearray(b'07 05 13 af 8e 17 ff 84 ec 88 1b f5 d2 e6 04 f6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13769531, ...,  0.00585938,
            0.05761719,  0.06152344]), textin=CWbytearray(b'44 26 10 f6 50 d2 31 7e 00 99 b2 8f b1 04 3f 08'), textout=CWbytearray(b'd9 35 00 ed 4a 75 f3 91 16 a3 62 64 c4 c1 9f ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.14160156, ...,  0.00878906,
            0.05761719,  0.06445312]), textin=CWbytearray(b'f4 f6 9d 87 99 9b b1 11 d6 3f d6 2b f4 fb 1e 41'), textout=CWbytearray(b'e0 f0 a0 66 7b eb 10 82 bb 5a e8 ef 54 40 3c c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.140625  , ...,  0.        ,
            0.05371094,  0.05859375]), textin=CWbytearray(b'9e 99 82 3e 42 34 91 17 7c a1 d1 f4 22 42 79 48'), textout=CWbytearray(b'c5 8c a8 8e cd e1 5c 59 49 21 22 0c 8c 9c c0 be'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14160156, ...,  0.01464844,
            0.06640625,  0.06835938]), textin=CWbytearray(b'e8 1d 0f d6 2c 2e b8 b4 d9 81 3f 0c f5 84 c5 2d'), textout=CWbytearray(b'f1 e2 be aa b6 0f e5 7a 92 15 86 6a 16 93 92 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19140625, -0.14257812, ...,  0.01074219,
            0.06054688,  0.06542969]), textin=CWbytearray(b'bf 3f ee 3e 57 57 55 48 88 0d 92 90 a5 38 77 8c'), textout=CWbytearray(b'fb 5b 18 33 a6 8e 3c ab ec 22 f5 e9 8e d0 ae 26'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19140625, -0.13867188, ...,  0.01074219,
            0.05859375,  0.06347656]), textin=CWbytearray(b'db a7 0e 75 ff a1 25 f0 b6 f2 73 c8 39 58 29 2f'), textout=CWbytearray(b'ca 17 6f a6 a2 be 3a 61 3f 50 9e 79 15 cf a4 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13964844, ...,  0.00488281,
            0.05664062,  0.06152344]), textin=CWbytearray(b'a6 36 1f 42 65 8e b0 d1 10 b6 67 73 2e 8f 9c ef'), textout=CWbytearray(b'7c 97 93 0f 58 0d 60 c5 ca 32 d2 07 c1 a8 41 b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18261719, -0.13769531, ...,  0.02050781,
            0.06835938,  0.07128906]), textin=CWbytearray(b'20 8b 02 15 f4 0c c7 54 c7 09 18 68 8a a7 35 b8'), textout=CWbytearray(b'a1 d1 71 92 16 fe 60 39 75 95 ea 5f e4 7a 0d b1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1796875 , -0.13476562, ...,  0.02050781,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'04 e0 01 e9 b2 7c 80 c3 02 8f ad 40 35 cd cd d6'), textout=CWbytearray(b'1d 43 57 44 2d 63 97 55 8e a4 1c af e5 31 e8 b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13867188, ...,  0.01269531,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'a3 31 28 ea f0 93 51 f5 f1 41 76 b6 ca b6 83 ea'), textout=CWbytearray(b'8d 06 13 64 65 c4 77 0b d0 51 a8 2a f9 fa 40 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13671875, ...,  0.00195312,
            0.05566406,  0.05957031]), textin=CWbytearray(b'7a b5 fe c2 59 4d 5e de d9 b0 23 39 af fd 48 b1'), textout=CWbytearray(b'dd 8f 12 b9 22 c0 39 49 97 bc 4b 06 04 47 f4 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18359375, -0.13476562, ...,  0.02441406,
            0.0703125 ,  0.07324219]), textin=CWbytearray(b'59 69 52 4b e2 7f 4d a9 da 36 c2 6a 17 06 ea bc'), textout=CWbytearray(b'eb da e1 d4 18 38 73 d9 e1 d1 6c 4a 2e dc 2b 9c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18261719, -0.13964844, ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'1e 95 38 50 e9 d2 ee fc d4 63 c0 ff e1 05 b2 04'), textout=CWbytearray(b'3d 58 dd b9 ec 47 ee 52 08 0d 1e 9b 11 11 2a 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13671875, ...,  0.01855469,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'ff 31 87 4c 56 e6 d3 1c 11 d9 3e 40 10 2e 8e d1'), textout=CWbytearray(b'93 fe 4c e9 88 ea 15 7e d2 0d 7b 0f 60 39 88 1b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.1875    , -0.14160156, ...,  0.02246094,
            0.06933594,  0.07226562]), textin=CWbytearray(b'51 86 3f d8 72 fe 54 1f d4 5c 2e d3 7e b1 34 1a'), textout=CWbytearray(b'e7 f4 3a b8 25 76 78 a9 12 d2 05 a3 1b fd f3 35'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.140625  , ...,  0.01464844,
            0.06542969,  0.06640625]), textin=CWbytearray(b'22 cb 15 32 41 d5 12 d3 ba 2f ce dc 10 98 97 66'), textout=CWbytearray(b'25 f4 09 eb de cb 4e 8f 10 d3 1c b9 5a 25 5e 34'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13867188, ...,  0.00097656,
            0.0546875 ,  0.05859375]), textin=CWbytearray(b'59 66 14 38 b3 c7 5c a4 26 c8 a7 9e 28 87 ab ff'), textout=CWbytearray(b'7a cd 11 d4 30 d3 58 d2 e1 5f 7e d1 22 4f 21 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13574219, ...,  0.01367188,
            0.06542969,  0.06640625]), textin=CWbytearray(b'ef ea d7 a3 8d 3a 42 57 b0 ee 6a c1 c4 d8 5e c0'), textout=CWbytearray(b'20 77 24 38 dd 85 f4 11 44 8b 25 47 a2 b9 65 2a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.140625  , ...,  0.02929688,
            0.07617188,  0.07617188]), textin=CWbytearray(b'd3 1d 9e 84 b5 47 bf 5d 87 c4 3d 50 32 70 4b 62'), textout=CWbytearray(b'f0 dc 2a ff 44 66 e7 1b 20 bb 34 28 ad 79 82 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18457031, -0.13867188, ...,  0.00097656,
            0.05664062,  0.06054688]), textin=CWbytearray(b'32 bb c8 b0 30 a3 3c f0 d9 4d 12 32 d3 8f 79 bf'), textout=CWbytearray(b'ba 14 7e f7 23 51 65 90 3f 94 dd 9e ee 88 5e 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18554688, -0.13867188, ...,  0.00683594,
            0.05957031,  0.06054688]), textin=CWbytearray(b'43 79 54 f4 ba ac 6f 18 7a 4c c2 0b 13 f4 94 ae'), textout=CWbytearray(b'83 e7 dd 66 fb 9d 32 44 2e 65 ce ea a8 91 5c 85'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18652344, -0.14453125, ...,  0.01269531,
            0.06640625,  0.06542969]), textin=CWbytearray(b'15 55 67 16 cf d1 01 a7 37 e1 b2 53 69 64 3e 11'), textout=CWbytearray(b'45 b5 ae 3b 0c 78 f2 ad 35 da b6 87 d9 49 ef cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.13964844, ...,  0.02148438,
            0.06738281,  0.06933594]), textin=CWbytearray(b'23 4d f4 58 e9 8a 6b c7 04 0c b1 8f 55 df 18 25'), textout=CWbytearray(b'27 46 35 4c 47 07 c8 bd 33 e2 89 42 7f ba 14 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18847656, -0.13769531, ...,  0.00878906,
            0.06054688,  0.06542969]), textin=CWbytearray(b'ab 7e 90 29 d2 30 b7 39 74 60 59 43 7b ec 21 4d'), textout=CWbytearray(b'ea 8c 53 b8 c4 e6 b7 d6 0a c0 5a f9 b2 f9 10 4c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19042969, -0.14160156, ...,  0.02246094,
            0.06933594,  0.07226562]), textin=CWbytearray(b'ab 90 26 02 a2 d2 54 d0 fa 87 ea df 57 ee cc 7e'), textout=CWbytearray(b'f3 9f 3b c5 da 7a df 5b 3a 36 ca 08 61 66 93 ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1796875 , -0.13378906, ...,  0.00390625,
            0.05664062,  0.06347656]), textin=CWbytearray(b'58 46 52 c9 25 92 55 d6 08 7b df 56 24 53 f1 b7'), textout=CWbytearray(b'8f 5c 7c f7 08 aa be 12 db 11 8d 28 af 74 08 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.13867188, ...,  0.00585938,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'09 a4 8c 4a dd e7 b0 5d 07 c0 36 ae a8 46 8d 27'), textout=CWbytearray(b'0e 39 d2 9f e7 98 3d 05 99 cd a2 79 b5 07 7b 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13671875, ...,  0.02832031,
            0.07421875,  0.07421875]), textin=CWbytearray(b'ee 39 30 df ef df 03 3d 32 6f ce 0e 57 90 f9 66'), textout=CWbytearray(b'56 a1 d0 e2 7f be 00 f2 aa 42 52 a5 09 93 7b 9b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13769531, ...,  0.01464844,
            0.06445312,  0.06640625]), textin=CWbytearray(b'75 f0 cd 33 94 6e a1 7d 48 d0 a8 9e 68 dc 9a c6'), textout=CWbytearray(b'5f 9f 73 0f 89 07 f8 7f bc 89 c0 9d 8c a3 6a 0c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18554688, -0.13769531, ...,  0.00488281,
            0.05859375,  0.06347656]), textin=CWbytearray(b'e4 28 03 b7 b2 15 d4 30 ea d8 e5 5b ad 02 22 d5'), textout=CWbytearray(b'9a 74 67 d9 69 79 dc 32 d0 89 e1 07 f3 25 40 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19335938, -0.14160156, ...,  0.00976562,
            0.06054688,  0.06445312]), textin=CWbytearray(b'a0 95 94 df 3b 99 37 89 aa 68 2f 93 ec 15 84 1a'), textout=CWbytearray(b'fb 8a 8d 65 84 d6 12 9c d0 13 b8 7a 3b 7d 44 72'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18847656, -0.14257812, ...,  0.01855469,
            0.06738281,  0.06933594]), textin=CWbytearray(b'02 a0 d8 58 b6 b8 7a 07 68 f9 9d 9f 16 86 08 98'), textout=CWbytearray(b'c3 37 ef 3a 93 c3 e5 f9 26 46 0f 5f d0 3e de b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.19042969, -0.13867188, ...,  0.01757812,
            0.06933594,  0.06933594]), textin=CWbytearray(b'1c 9a 7e 59 9a ec 78 3b 93 49 ec 1f e9 d6 bf 52'), textout=CWbytearray(b'ec c2 90 87 e0 4b f7 2f 4d c5 44 b2 9c 81 ba 25'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14355469, ...,  0.00683594,
            0.05859375,  0.06347656]), textin=CWbytearray(b'ec 14 03 cb b4 fd 49 89 c6 f6 f8 94 fc 34 50 8a'), textout=CWbytearray(b'89 9b e6 93 e0 00 41 d4 15 3f 20 08 eb 15 c8 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13867188, ...,  0.01464844,
            0.06542969,  0.06738281]), textin=CWbytearray(b'9b 77 b3 a1 32 22 af 41 47 92 57 8b 10 ab 9e cc'), textout=CWbytearray(b'fe b9 5e af ef e5 0b 2c 18 ec c8 62 d3 29 c1 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13671875, ...,  0.01171875,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'a9 63 01 4b af 9f 17 4b 28 2a d7 50 71 44 6c e0'), textout=CWbytearray(b'1c 6e 39 08 09 1e e1 c8 71 8f f2 57 15 a5 f1 1b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13671875, ...,  0.00390625,
            0.05566406,  0.06054688]), textin=CWbytearray(b'c1 34 d7 b3 89 81 4a 87 e0 32 48 cc 8a bc eb 37'), textout=CWbytearray(b'4f c9 87 a1 28 e1 8c df c4 14 f6 06 f2 83 1f 50'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18261719, -0.13574219, ...,  0.00488281,
            0.05761719,  0.06152344]), textin=CWbytearray(b'df ff 03 93 4c 03 49 b8 ca d1 cc fd e0 98 69 f6'), textout=CWbytearray(b'1c c6 80 41 d6 96 a5 80 47 38 80 26 80 e7 f0 ea'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.13769531, ...,  0.01855469,
            0.06933594,  0.06933594]), textin=CWbytearray(b'83 4a f0 4f e9 81 89 7b 03 82 af db d7 b2 75 58'), textout=CWbytearray(b'9d 62 e9 87 80 5f f8 27 d8 48 3f 92 72 6f dd 56'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.0078125 , -0.19238281, -0.14355469, ...,  0.01367188,
            0.06152344,  0.06445312]), textin=CWbytearray(b'75 3a 06 31 99 fa 27 85 82 98 f8 69 67 16 62 08'), textout=CWbytearray(b'a7 b9 91 91 ec c5 43 de bd 90 4a 4e e3 49 9a a0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.13964844, ...,  0.01171875,
            0.06152344,  0.06738281]), textin=CWbytearray(b'bb 4a c6 b2 c4 e8 24 60 af 28 c7 87 76 74 5b 09'), textout=CWbytearray(b'ae 33 bd 76 09 8d 5b 5f 21 8a e6 04 98 94 b4 0f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13476562, ...,  0.0078125 ,
            0.05957031,  0.06347656]), textin=CWbytearray(b'6a 24 0c fc 70 ab 92 5e 20 01 de 03 74 ec bd 35'), textout=CWbytearray(b'81 1a 90 c4 af 5e 6c 33 46 52 2b 44 82 99 45 ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.13964844, ...,  0.01464844,
            0.06542969,  0.06738281]), textin=CWbytearray(b'c8 be 76 a3 20 14 19 95 7f 2f 07 e8 7d d2 49 e0'), textout=CWbytearray(b'af 2c 3b 07 c4 94 51 e8 b3 ca 14 e8 88 19 34 47'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18457031, -0.14453125, ...,  0.01660156,
            0.06640625,  0.06933594]), textin=CWbytearray(b'eb bc 4f 04 1a b7 95 66 80 90 22 2f 52 57 7b 52'), textout=CWbytearray(b'da 08 65 8b 60 43 e1 fb 80 8e e2 f6 ef 81 22 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14160156, ...,  0.00292969,
            0.05859375,  0.06152344]), textin=CWbytearray(b'5a ac 6b 7e a4 c5 a4 59 78 06 35 ad 2e d2 7f 8a'), textout=CWbytearray(b'9a 1c 71 6a cf 17 53 2f 7a 62 fb 14 3e 76 bb c2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.18847656, -0.14355469, ...,  0.00585938,
            0.05664062,  0.06347656]), textin=CWbytearray(b'fa a6 82 2b f7 ea f3 78 56 df 9c 85 0f 49 3c 8d'), textout=CWbytearray(b'db d1 f2 be eb 5f 9f e9 d8 de d6 95 54 c1 7e 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19335938, -0.14160156, ...,  0.00976562,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'66 59 e9 ce fa a7 3a ef 49 e2 4c f9 90 f5 7a 48'), textout=CWbytearray(b'3b 95 b3 06 be 2e 75 46 f5 13 f8 75 c9 98 41 18'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14550781, ...,  0.015625  ,
            0.06542969,  0.06933594]), textin=CWbytearray(b'c0 50 34 ac f5 e4 20 f4 04 6b 89 61 d2 7d 0a 24'), textout=CWbytearray(b'3c c3 6f 89 fc d2 01 c9 23 0c 72 65 c7 5b 76 ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.13964844, ...,  0.01074219,
            0.06152344,  0.06445312]), textin=CWbytearray(b'92 e1 01 21 7f 57 05 79 d9 5f 45 2d 3b 32 ea 3c'), textout=CWbytearray(b'2a 25 a7 40 93 f3 b4 4c 6e 5c cb fc 4e df e7 74'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18359375, -0.13378906, ...,  0.01953125,
            0.06738281,  0.06933594]), textin=CWbytearray(b'7e 26 b9 aa 7a c5 6d a1 57 32 ed ee 3b c5 d0 37'), textout=CWbytearray(b'd7 52 ad 52 7a c5 b1 ec 65 5f 30 e8 2d de 70 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.1875    , -0.14160156, ...,  0.00097656,
            0.05566406,  0.06152344]), textin=CWbytearray(b'18 f0 93 2d f3 d2 af 5d 29 0c 06 ed 60 5d e5 23'), textout=CWbytearray(b'f6 37 da 4e 0f c3 c5 b4 d0 08 af d7 6c 48 0d 17'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.13769531, ...,  0.00878906,
            0.06054688,  0.06445312]), textin=CWbytearray(b'8f c9 41 25 21 a2 fe 3a 74 53 52 6b 2e 16 06 ce'), textout=CWbytearray(b'15 83 66 00 e7 bb 6b 16 2a 46 f5 dc e3 97 ad df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18457031, -0.13964844, ...,  0.0234375 ,
            0.07128906,  0.07324219]), textin=CWbytearray(b'c8 4c b4 0f 33 e9 be fc e7 10 f7 ce c5 5b 8b ea'), textout=CWbytearray(b'bd e5 d8 26 42 1b dd d7 4a 9f 11 33 7e 81 83 b5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19433594, -0.14648438, ...,  0.0234375 ,
            0.07128906,  0.07128906]), textin=CWbytearray(b'3b 14 26 49 00 9c 69 44 ee 1e 02 45 bd 40 1c 07'), textout=CWbytearray(b'6f d5 a4 81 64 22 7d a7 0b 34 0e ff 88 a3 0d 10'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19140625, -0.14355469, ...,  0.00878906,
            0.06054688,  0.0625    ]), textin=CWbytearray(b'60 82 51 7b c8 b3 64 11 9b ae 72 5c e0 09 6a 98'), textout=CWbytearray(b'fc 5e a8 54 13 42 05 eb a9 ee 3a ea 9f 8f 02 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18652344, -0.13574219, ...,  0.0078125 ,
            0.0625    ,  0.06347656]), textin=CWbytearray(b'6f 34 5b 81 1f 7f 02 32 d1 ae dd 26 2e bc 46 a1'), textout=CWbytearray(b'91 75 2f 9a 41 e7 47 4e c7 de 15 33 6f f6 ac fd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13964844, ...,  0.01953125,
            0.06835938,  0.0703125 ]), textin=CWbytearray(b'ae a8 0f e3 77 58 bc db 90 ae 52 84 a7 ce 18 56'), textout=CWbytearray(b'4c e5 49 0a 91 ad ed ec 31 7d 5b 76 9e 70 36 5d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18847656, -0.14160156, ...,  0.02148438,
            0.07128906,  0.07128906]), textin=CWbytearray(b'00 38 bd 19 80 cc 39 3a e2 2d 9a 14 6b 76 03 9d'), textout=CWbytearray(b'87 27 c6 e7 a6 94 1a d3 2f cc d1 6f 74 cb 48 4a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18164062, -0.13769531, ...,  0.01953125,
            0.06738281,  0.07128906]), textin=CWbytearray(b'01 70 a6 76 3f 54 25 30 f5 d0 43 cd b0 7f 9c e0'), textout=CWbytearray(b'bf 91 d2 17 6c 37 41 16 e2 43 06 10 7e e4 0d 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.14257812, ...,  0.0078125 ,
            0.06054688,  0.06152344]), textin=CWbytearray(b'03 10 18 25 ea a3 b4 02 0a 05 62 ca 7c d7 03 47'), textout=CWbytearray(b'e9 56 14 15 43 10 45 63 cf 40 08 db 31 52 3a 41'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.02050781,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'69 36 f1 78 42 a0 9a 88 a6 da 24 a1 15 b2 f4 cb'), textout=CWbytearray(b'19 12 b4 9e 14 6a 5b 44 df bc 29 d4 0f a6 b8 92'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14453125, ...,  0.01660156,
            0.06738281,  0.06933594]), textin=CWbytearray(b'98 da 47 43 6e b1 4f 48 11 3a a3 20 f9 41 8d 58'), textout=CWbytearray(b'96 69 d1 b5 8b fc 66 f6 09 b8 03 6e c9 6a 6a 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14355469, ...,  0.00683594,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'26 d3 47 c0 d7 b3 e7 30 62 81 af 3e ba 6a 20 7c'), textout=CWbytearray(b'64 19 d9 ab 1f f0 80 0a 95 8b 57 0c b9 ee e5 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.140625  , ...,  0.01660156,
            0.06835938,  0.06738281]), textin=CWbytearray(b'd4 1d 62 34 fd 24 1d f7 8c ba 0c ba da 21 08 4f'), textout=CWbytearray(b'21 b3 44 6b f3 2c 5a b4 55 dc 51 e3 54 f8 d5 c0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.140625  , ...,  0.00488281,
            0.05664062,  0.0625    ]), textin=CWbytearray(b'a7 fa 5f b0 cd b2 d3 7e 04 26 c5 2e 9f ab 1f bd'), textout=CWbytearray(b'32 f6 64 40 26 25 e8 34 bd 53 b5 ae 6a 68 7b b6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13769531, ...,  0.01269531,
            0.06347656,  0.06640625]), textin=CWbytearray(b'dc 44 8d 18 8f 7b af e3 31 75 1e 57 5f 8a 34 ec'), textout=CWbytearray(b'c9 bd be b9 ce 2e 94 9f 03 21 12 0a 55 2c 04 df'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13867188, ...,  0.0078125 ,
            0.06152344,  0.0625    ]), textin=CWbytearray(b'98 08 2b 9a 17 89 66 bd 97 76 c9 08 50 ec 13 c8'), textout=CWbytearray(b'bc da 45 91 19 28 65 b0 1d 3c cd 9d d5 b7 00 f1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14355469, ...,  0.        ,
            0.05566406,  0.05859375]), textin=CWbytearray(b'95 4f df bc ae db 58 70 49 23 64 b0 b7 89 7f 6c'), textout=CWbytearray(b'f2 f6 6d be 76 a3 33 f1 7b 74 43 10 1e cf 41 9e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.140625  , ...,  0.01269531,
            0.06347656,  0.06542969]), textin=CWbytearray(b'3c e6 eb 4f aa 33 ec dd 3e e4 62 b0 3a c5 e3 7e'), textout=CWbytearray(b'1b e2 c4 82 27 90 da 21 24 40 a1 e6 7f f7 b8 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.13964844, ...,  0.01269531,
            0.06542969,  0.06738281]), textin=CWbytearray(b'b7 a4 cf ce ef a8 71 a2 7f 1c 98 1e 6a 5e f5 8d'), textout=CWbytearray(b'67 0c b9 f9 8f 40 c1 a0 46 bb ef f7 05 3f c3 ac'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00878906, -0.19238281, -0.14355469, ...,  0.01074219,
            0.06152344,  0.06445312]), textin=CWbytearray(b'4d 76 32 c8 25 b8 25 ef 6e 83 a3 50 5f 35 6a 4c'), textout=CWbytearray(b'ef 09 47 16 92 f6 d3 bf a9 84 ab 36 e0 91 2d d4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.13867188, ...,  0.02539062,
            0.07324219,  0.07421875]), textin=CWbytearray(b'6e a8 ac fb c4 cb 8c 46 80 5a fc 8b ee e9 07 4c'), textout=CWbytearray(b'e2 13 2d 5c 98 7c 57 7e a2 4d 24 54 20 e6 db a4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14257812, ..., -0.00292969,
            0.05273438,  0.05566406]), textin=CWbytearray(b'e2 63 5c 0f ba da e8 74 c6 01 e5 ea 0d b2 36 88'), textout=CWbytearray(b'2a c5 45 39 9b b6 be 55 49 2c ff 31 6c bf 09 22'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18457031, -0.13574219, ...,  0.01269531,
            0.06152344,  0.06542969]), textin=CWbytearray(b'24 9b 67 3a 2e 00 94 30 aa ee 98 b0 88 e2 ef ad'), textout=CWbytearray(b'd3 3c 2f 39 c3 cc eb 41 9d aa 66 f6 80 56 dd b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18066406, -0.13378906, ...,  0.01660156,
            0.06542969,  0.06835938]), textin=CWbytearray(b'de 4b 30 2a 91 7a 8b 44 9e 80 64 6a 4f a7 3a eb'), textout=CWbytearray(b'44 ae ef 30 77 1b ec 0a 19 dd 17 62 47 a9 76 23'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14550781, ...,  0.01464844,
            0.06640625,  0.06738281]), textin=CWbytearray(b'42 85 c2 8e 1b d6 75 f7 3a d0 51 b6 07 a3 8d 42'), textout=CWbytearray(b'78 e5 d6 28 05 99 31 2d 04 be 9b 06 70 a5 b8 63'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13574219, ...,  0.01660156,
            0.06542969,  0.06933594]), textin=CWbytearray(b'80 f8 91 bd 42 11 7c 48 f6 e4 9e 4b b3 ac 59 a1'), textout=CWbytearray(b'55 8e 6f 87 d8 2e cd c3 86 52 d8 fa 1c 45 c4 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18359375, -0.13671875, ...,  0.00195312,
            0.05664062,  0.05859375]), textin=CWbytearray(b'7c dc 59 2f 7f f8 50 bb cd db 8f 6f 1e 90 93 ac'), textout=CWbytearray(b'46 97 47 3c a0 a3 70 ab bc 08 29 4e d1 29 a1 93'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.19042969, -0.14160156, ...,  0.00097656,
            0.05566406,  0.05957031]), textin=CWbytearray(b'1e 2f 5d 86 ee 14 e9 2e e7 03 01 80 5e f0 0f 4a'), textout=CWbytearray(b'b5 77 be 02 d0 6c ce db 95 3f 63 a1 30 f0 15 f4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18554688, -0.13671875, ...,  0.01367188,
            0.06445312,  0.06640625]), textin=CWbytearray(b'8d e5 81 b5 d1 57 36 1c 36 ad 3e 2a a6 19 11 63'), textout=CWbytearray(b'7a 96 43 56 fa 62 67 36 fa 02 96 aa ed b9 5e 49'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18164062, -0.13964844, ...,  0.02050781,
            0.06933594,  0.0703125 ]), textin=CWbytearray(b'2b 30 81 a8 9e 40 f6 64 5f c3 1b ac b1 9f b4 86'), textout=CWbytearray(b'a2 42 c9 3d 8a 5f 05 34 02 cf 58 70 26 b1 85 c7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13964844, ...,  0.01660156,
            0.06347656,  0.06933594]), textin=CWbytearray(b'36 71 76 1f 35 37 b9 3f b4 8f 1b 23 3f 7a d7 16'), textout=CWbytearray(b'93 29 85 97 6f 01 c4 37 76 98 28 38 14 91 b3 8d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18652344, -0.13964844, ...,  0.015625  ,
            0.06542969,  0.06738281]), textin=CWbytearray(b'13 84 00 cd b5 0d b8 4b 82 8b bb ae 93 77 81 61'), textout=CWbytearray(b'5c 3a bd 4c c1 b4 0c 3e 5d c4 e1 c0 62 53 0a ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.1875    , -0.13671875, ...,  0.015625  ,
            0.06347656,  0.06640625]), textin=CWbytearray(b'f2 80 43 97 72 91 60 9a 99 f6 d3 7b b0 c0 38 23'), textout=CWbytearray(b'9e 9c c8 e6 71 8b 4b 8b 01 50 16 3c f6 67 ba 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13574219, ...,  0.0078125 ,
            0.06054688,  0.06445312]), textin=CWbytearray(b'07 93 4a 59 cc 42 45 eb af f5 fb cb 8c 17 a9 bb'), textout=CWbytearray(b'53 96 d2 3e c6 63 fb 09 6c 86 dd 3e 10 ca 5d ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.17871094, -0.13769531, ...,  0.00097656,
            0.05566406,  0.05957031]), textin=CWbytearray(b'd3 85 72 9a ff 1e ee 01 a4 3f 8b 70 2e fe 87 f7'), textout=CWbytearray(b'f8 1b 45 27 28 87 76 3d 33 9b 3e 97 f0 44 52 24'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.13867188, ...,  0.0078125 ,
            0.06152344,  0.06445312]), textin=CWbytearray(b'45 33 1a 8e 0b 74 77 d9 a7 b6 e2 fc ce 54 2b 52'), textout=CWbytearray(b'a5 7a 01 14 7a 8e 7e ac f5 2c e6 18 7e 43 2a 8a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18261719, -0.13867188, ..., -0.00488281,
            0.05078125,  0.05664062]), textin=CWbytearray(b'c0 3f aa 28 56 67 64 62 3d 37 32 0a dd 5f d0 ec'), textout=CWbytearray(b'a3 05 ae 94 2a 33 b3 c6 07 04 4f 84 3e f3 e5 54'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18652344, -0.13867188, ...,  0.015625  ,
            0.06542969,  0.06738281]), textin=CWbytearray(b'6c f5 1a ab d7 80 36 b2 e0 7b b1 d8 5b 39 d2 fc'), textout=CWbytearray(b'61 d4 8f 99 44 84 02 1e f0 63 36 f5 40 3c 6e cd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.1796875 , -0.13574219, ...,  0.01464844,
            0.06640625,  0.06933594]), textin=CWbytearray(b'79 2a d6 d3 d1 a6 ee c6 97 9a fd d8 98 c7 b1 cc'), textout=CWbytearray(b'89 b2 73 7b c1 1d e9 68 af c8 d8 69 e0 56 ac 11'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18261719, -0.13378906, ...,  0.01367188,
            0.06347656,  0.06640625]), textin=CWbytearray(b'2a 9b ed f3 3e cf 34 0e fd c3 26 58 2c cf a6 d1'), textout=CWbytearray(b'd5 52 37 83 cb d1 e7 9d 67 6e 3c 31 e1 c8 1a ee'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14257812, ...,  0.01660156,
            0.06445312,  0.0703125 ]), textin=CWbytearray(b'fb ef b1 8c a5 0e f6 1e 94 f0 53 7b 87 02 e0 3a'), textout=CWbytearray(b'20 69 79 a4 a3 1a 0f b0 00 1b 05 40 3e 26 60 90'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18847656, -0.140625  , ...,  0.01074219,
            0.06347656,  0.06640625]), textin=CWbytearray(b'83 cd a2 6c c2 df 84 0a ce 67 52 9d 5c f1 dd 0b'), textout=CWbytearray(b'30 66 cb cb 4c b2 40 eb 10 d1 78 29 30 1d b5 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18164062, -0.14160156, ...,  0.01953125,
            0.06738281,  0.06835938]), textin=CWbytearray(b'd7 63 89 56 ce de 65 3b 40 af 15 7c 66 73 e8 54'), textout=CWbytearray(b'65 d6 4c 8c 46 67 0b 1d 2c ef 43 9b 64 b1 65 2d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13867188, ...,  0.01953125,
            0.06835938,  0.06933594]), textin=CWbytearray(b'86 f0 0a f2 26 ea f8 78 07 00 45 90 dd 72 0d a9'), textout=CWbytearray(b'6e 26 07 b8 d0 dc 73 ce d2 2a 50 25 77 10 09 4e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18457031, -0.140625  , ...,  0.00488281,
            0.05761719,  0.06152344]), textin=CWbytearray(b'84 39 69 b1 95 c1 8c bb 3a 7b f6 95 de 96 45 dc'), textout=CWbytearray(b'd6 ef c4 b4 a6 03 7d 94 2d c8 de c5 bd 10 c8 55'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18261719, -0.13574219, ...,  0.01464844,
            0.06542969,  0.06542969]), textin=CWbytearray(b'7f aa a5 a5 ff 13 15 e4 d2 73 0c 04 6d eb 35 be'), textout=CWbytearray(b'26 bb b9 be c3 f2 4f 3f 8e cb 0b cf dd 6c 0f 0d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13769531, ...,  0.01757812,
            0.06542969,  0.06835938]), textin=CWbytearray(b'86 95 a5 c4 b3 3c 2d b6 b1 20 c1 46 44 e1 90 4a'), textout=CWbytearray(b'99 8a 48 e9 50 c3 30 2e 2f 98 07 db dc d9 b7 c4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13574219, ...,  0.01757812,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'aa fb cd 65 01 b9 b5 58 17 6f da 23 00 b0 c1 fc'), textout=CWbytearray(b'48 87 46 18 12 70 24 c6 35 6d d6 03 83 c4 f4 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.13671875, ...,  0.00878906,
            0.05957031,  0.06542969]), textin=CWbytearray(b'74 51 db 29 4c e0 12 9e f2 af bf 0c 92 84 a5 62'), textout=CWbytearray(b'e0 c7 c3 96 13 79 37 25 cc b1 06 c0 92 57 4c a6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.140625  , ...,  0.01171875,
            0.06445312,  0.06640625]), textin=CWbytearray(b'e1 5c c2 1b 96 10 9d dd e1 e5 d9 4e 0c f5 84 fb'), textout=CWbytearray(b'51 4f da 23 ae ba 70 5e 7b 53 a3 a1 82 ab c0 27'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18847656, -0.13867188, ...,  0.00292969,
            0.05566406,  0.05957031]), textin=CWbytearray(b'ab 9f 04 b8 72 a0 30 2d 23 64 ad 63 83 28 c2 5e'), textout=CWbytearray(b'16 5c 50 e4 58 b1 b0 0e 1e b3 a9 92 e1 6a 03 70'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18261719, -0.1328125 , ...,  0.01074219,
            0.06054688,  0.06445312]), textin=CWbytearray(b'3d 9c b8 9f 11 91 36 65 b1 57 4e 6f fd d0 1c b9'), textout=CWbytearray(b'bd a5 a4 0c af 50 5a b8 1a 88 af 99 52 00 0f ce'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.1875    , -0.13964844, ...,  0.01074219,
            0.0625    ,  0.06640625]), textin=CWbytearray(b'50 3d 5b cf 43 78 18 f8 0e 4b 41 22 6d 78 0f 45'), textout=CWbytearray(b'97 7d e3 a2 fb e0 2a 65 60 31 e6 7c 92 c4 09 80'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.140625  , ...,  0.00683594,
            0.05859375,  0.06152344]), textin=CWbytearray(b'51 5d 9b 78 4e c4 6d e7 a1 80 51 b6 ef 73 17 0b'), textout=CWbytearray(b'5c 89 99 2b 29 a8 ee 29 b9 25 9a 96 5f 6a d5 e4'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18359375, -0.13867188, ...,  0.01367188,
            0.06445312,  0.06738281]), textin=CWbytearray(b'18 ad 18 0a 96 0a d1 67 2f 68 27 33 4a 1f dc da'), textout=CWbytearray(b'70 f8 18 47 a9 0e 92 9a 14 13 c2 4f 2d b3 0c 89'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13574219, ...,  0.02148438,
            0.06835938,  0.07128906]), textin=CWbytearray(b'fe 8d 81 81 35 2b bc 26 78 16 42 ae 9f f0 aa e7'), textout=CWbytearray(b'ce 2c da 2b 11 ff 6a ad 51 4f 6b ad 26 7f d3 2c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14355469, ...,  0.00878906,
            0.06152344,  0.06347656]), textin=CWbytearray(b'51 73 af 8c f8 60 0a e8 61 6b 31 d6 d5 55 fa 0e'), textout=CWbytearray(b'06 46 9f d0 db 80 9a 7f 6a 43 ff 4a 4a 13 d5 bc'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.17871094, -0.13769531, ...,  0.00878906,
            0.05957031,  0.06445312]), textin=CWbytearray(b'11 9d 94 a9 22 07 e4 eb 3b 13 bb 03 9e a2 af d2'), textout=CWbytearray(b'45 e3 cc ca b5 a1 05 f6 e1 f9 1b e6 f7 fc ba dd'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.1875    , -0.14355469, ...,  0.01660156,
            0.06445312,  0.06933594]), textin=CWbytearray(b'e1 cc 07 39 f6 1f 17 6f d6 74 70 85 50 f3 50 3d'), textout=CWbytearray(b'fa 31 d2 d8 18 e7 44 1c 6c ae 3c e9 3d ec 69 28'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01464844, -0.18359375, -0.13964844, ...,  0.01074219,
            0.0625    ,  0.06542969]), textin=CWbytearray(b'29 cf 87 dc 87 4d 4b f7 88 4e 86 ea 48 be aa cd'), textout=CWbytearray(b'5b 07 d8 13 1c 29 2f 85 80 43 98 1e 5c 8a ad b8'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14160156, ...,  0.01855469,
            0.06738281,  0.07128906]), textin=CWbytearray(b'00 db 81 6e d2 b6 ca 4a 52 f3 23 75 5c d8 15 22'), textout=CWbytearray(b'17 b3 fc 29 ee 9e de db af 34 20 ab fa 52 65 52'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13476562, ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'dc cb 02 5e bf 67 b8 8f 48 3a 02 6c 5d 70 fd f7'), textout=CWbytearray(b'33 73 30 86 03 5e 0d 71 07 fe ec 03 b5 03 46 d9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.18945312, -0.14160156, ...,  0.02050781,
            0.0703125 ,  0.0703125 ]), textin=CWbytearray(b'0d 54 71 c7 16 de 83 24 73 f7 04 36 4f 24 55 10'), textout=CWbytearray(b'a4 9e 1d cd a6 4a 7c f7 ec a5 f5 28 72 2b d5 67'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18652344, -0.13769531, ...,  0.0234375 ,
            0.06933594,  0.07128906]), textin=CWbytearray(b'7f 6a 4f 55 49 c7 7a fb 53 dc 1b 37 d4 10 9a 33'), textout=CWbytearray(b'78 ab f2 9d 32 39 af 56 8b 40 e7 bc ac 1a c9 3a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18945312, -0.14160156, ..., -0.00195312,
            0.05175781,  0.05957031]), textin=CWbytearray(b'a1 45 28 b2 c2 37 14 56 2b 7a c7 1e 7e e7 27 02'), textout=CWbytearray(b'0f 4c 8e b3 fc ad 20 38 d9 3d 19 34 ba 88 f6 57'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18652344, -0.14257812, ...,  0.01757812,
            0.06640625,  0.06933594]), textin=CWbytearray(b'36 59 2f 81 e5 79 c6 ce 9b 2a 86 01 d1 05 f2 82'), textout=CWbytearray(b'f1 ee 39 26 f6 2b 5f b1 6e 5f 66 95 91 b6 58 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18457031, -0.13574219, ...,  0.01269531,
            0.06054688,  0.06445312]), textin=CWbytearray(b'8f 96 72 be e7 77 d2 19 1a d4 60 88 35 53 8f d1'), textout=CWbytearray(b'fe 92 e7 f5 9c be 17 6c 05 3c 54 66 b0 aa 9f 82'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13964844, ...,  0.01660156,
            0.06835938,  0.06738281]), textin=CWbytearray(b'38 6a ea 6e fa 09 3e 06 d4 8d 58 e7 9e 21 f3 0c'), textout=CWbytearray(b'fe ba 4a a1 5f 47 a8 7a 6f c5 bc 84 76 10 c0 a2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.14453125, ...,  0.01464844,
            0.06445312,  0.06835938]), textin=CWbytearray(b'b7 bd 23 df 63 e5 4e 69 d8 7e 5e fa 15 27 ad 4e'), textout=CWbytearray(b'c7 a7 86 4d ac a8 ad 25 81 d4 e3 e6 9c a9 ab a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14160156, ...,  0.00878906,
            0.06152344,  0.06445312]), textin=CWbytearray(b'63 32 ec c1 b0 24 84 f0 87 ab 23 48 64 54 a7 84'), textout=CWbytearray(b'ce ad e8 a4 1d c5 cf 1c 06 14 2b 9e d8 6e 7e 5e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.14257812, ...,  0.01660156,
            0.06445312,  0.06738281]), textin=CWbytearray(b'81 e8 2b dd ee 07 3d 6c 26 fa 31 3d fb 07 44 31'), textout=CWbytearray(b'f1 36 e7 d5 aa 4b 3e fa b4 54 7e 52 a7 9b 63 5f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01855469, -0.18554688, -0.13769531, ...,  0.00585938,
            0.05566406,  0.06152344]), textin=CWbytearray(b'08 96 76 28 1a 29 58 0d 76 33 1e 59 41 e9 6d e3'), textout=CWbytearray(b'7a 18 40 a9 fe 42 ba a4 39 8e 25 87 88 ca 36 6c'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.00976562, -0.19140625, -0.14257812, ...,  0.02929688,
            0.07617188,  0.07421875]), textin=CWbytearray(b'3a 22 96 4b 15 74 5f 58 6c 7f 4b 69 1d ee 3e 5e'), textout=CWbytearray(b'32 88 00 ad 3c e4 dd 11 f3 85 f7 06 d2 d3 16 65'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18359375, -0.13671875, ...,  0.00976562,
            0.06152344,  0.06640625]), textin=CWbytearray(b'cd ef 8f c8 2b 19 79 ed 70 5a 99 8b 0c 6a 15 f0'), textout=CWbytearray(b'4b a8 45 87 f0 21 df 03 51 eb 75 8c a2 72 ca ff'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18164062, -0.13378906, ...,  0.01855469,
            0.06542969,  0.07128906]), textin=CWbytearray(b'ef 2d d3 95 8b 15 99 c0 48 bc 15 d0 c9 f8 34 e8'), textout=CWbytearray(b'ae 6e 9c 75 85 d0 59 64 d2 f0 5e 1b 94 18 9d b9'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13574219, ...,  0.01269531,
            0.06152344,  0.06738281]), textin=CWbytearray(b'46 79 3e 19 88 af fd 8c 72 d7 52 26 75 d1 a1 d4'), textout=CWbytearray(b'69 cb 94 4e 1f 2b dc ab 99 0a e8 7b db ab af 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18945312, -0.13867188, ...,  0.00878906,
            0.05957031,  0.06542969]), textin=CWbytearray(b'26 cb 10 6d 82 bf 31 6a f3 d3 0f 24 28 f5 71 6e'), textout=CWbytearray(b'c7 8c a9 d9 64 d6 65 2c 6d c8 44 a3 57 e2 6a 36'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18945312, -0.13964844, ...,  0.00976562,
            0.05859375,  0.06347656]), textin=CWbytearray(b'1c a9 2d fe 2f bc 4e 0f dc 48 0c f9 51 c9 02 50'), textout=CWbytearray(b'c3 68 36 e9 62 1b f7 8b 76 0c 73 2f 53 38 ee 30'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19042969, -0.14453125, ...,  0.01171875,
            0.05957031,  0.06445312]), textin=CWbytearray(b'b6 ba 97 9b 97 94 b0 9e 4b 66 04 e5 f5 f6 80 52'), textout=CWbytearray(b'e4 8f 4e 1b 4c 4b ae 77 92 db 5e b9 3c fc 64 07'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18261719, -0.13671875, ...,  0.00878906,
            0.05859375,  0.06640625]), textin=CWbytearray(b'4a ea 9d 32 c9 62 e5 79 bc f1 de 24 db bf b2 36'), textout=CWbytearray(b'b1 d2 b8 1e b1 88 fa 46 79 91 c6 9f 9c fd 0b ca'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18554688, -0.14257812, ...,  0.00585938,
            0.05761719,  0.0625    ]), textin=CWbytearray(b'ac 47 37 42 80 98 ab 8d bc b4 53 c2 f8 75 93 58'), textout=CWbytearray(b'3c 81 2f 99 a3 73 61 b9 a0 2b 03 ab fc 89 00 c6'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.18164062, -0.13574219, ...,  0.01367188,
            0.06445312,  0.06738281]), textin=CWbytearray(b'82 62 0a 0b fe a4 07 e8 bd 75 cf e8 df ee fb f3'), textout=CWbytearray(b'50 2e a5 8c da 5d d6 bd fb 46 d5 8a f2 78 32 b7'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02148438, -0.18164062, -0.13085938, ..., -0.00097656,
            0.0546875 ,  0.05761719]), textin=CWbytearray(b'b6 8b 49 00 1c 0f 14 8f 0d c9 e3 bf 6d ac 77 f7'), textout=CWbytearray(b'15 f0 b7 12 50 67 05 5d 0d 83 b3 01 20 70 3f d2'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18847656, -0.13964844, ...,  0.02246094,
            0.0703125 ,  0.07128906]), textin=CWbytearray(b'c6 64 c8 a9 34 a5 6f 05 30 cf a0 9e b9 d1 f2 8e'), textout=CWbytearray(b'ed 58 b1 ae 99 5d a0 0b ac f3 44 ce d1 0a f8 3f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.1875    , -0.14355469, ...,  0.01660156,
            0.06542969,  0.0703125 ]), textin=CWbytearray(b'e0 54 06 f0 da a7 e4 4c 61 55 ee 0b a3 8f 5c 4f'), textout=CWbytearray(b'27 6d f9 70 9c 0d 44 df dd 76 f7 c0 8b 8c ab 48'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18945312, -0.13867188, ...,  0.015625  ,
            0.06347656,  0.06640625]), textin=CWbytearray(b'cc f5 71 cd 7a da 99 d4 54 22 cb 2e 86 38 98 d8'), textout=CWbytearray(b'18 e4 61 43 9b 40 a1 35 14 df 3f ce 31 b6 26 a5'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18261719, -0.13476562, ...,  0.00488281,
            0.05957031,  0.06152344]), textin=CWbytearray(b'7f 03 ea c4 23 51 b2 85 e6 26 4c 81 df c1 c8 b3'), textout=CWbytearray(b'7c a9 27 7b 6a fe c1 a5 83 95 fb fa d7 ed ae 4f'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.13867188, ...,  0.02246094,
            0.07128906,  0.07128906]), textin=CWbytearray(b'b8 4d 24 e0 38 96 50 55 9c f6 ac 0f 11 e5 ee 79'), textout=CWbytearray(b'2a 44 f3 7c c4 a5 51 b9 fc 1d c1 1c 88 97 c4 97'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18847656, -0.14355469, ...,  0.00878906,
            0.06152344,  0.06347656]), textin=CWbytearray(b'91 5a 98 a9 2c 81 7a 80 d9 cf fa 5c 97 78 1b 78'), textout=CWbytearray(b'17 83 11 47 f6 e0 d4 3e 00 68 d7 22 50 e0 bd ef'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18554688, -0.14160156, ...,  0.00878906,
            0.06152344,  0.06152344]), textin=CWbytearray(b'3a d9 74 20 0b 9e 7c 83 a7 bf 3e 08 f9 5e e4 35'), textout=CWbytearray(b'd4 a4 91 8e 49 56 24 ff 52 68 2a 35 49 85 e4 7e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18652344, -0.13867188, ...,  0.03027344,
            0.078125  ,  0.07617188]), textin=CWbytearray(b'2d 71 a6 d8 d5 82 d4 4f 64 15 73 f7 f3 7a 84 cb'), textout=CWbytearray(b'89 65 c7 12 4c d8 b5 ef ee 52 b3 0c a2 a6 07 2e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.18554688, -0.14257812, ...,  0.01855469,
            0.06738281,  0.0703125 ]), textin=CWbytearray(b'c1 4b aa 01 a3 ab 2c db 04 a0 04 17 b9 db 48 34'), textout=CWbytearray(b'f4 06 f0 2c d0 89 6d f4 d6 47 9b 56 14 75 c3 19'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.17871094, -0.13183594, ...,  0.00390625,
            0.05273438,  0.05957031]), textin=CWbytearray(b'6f 19 50 08 93 b1 d3 b2 85 10 74 45 37 69 bb b6'), textout=CWbytearray(b'5d 44 9d 22 a4 23 f7 21 e8 6f c3 60 61 f5 4f f0'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01074219, -0.18847656, -0.14355469, ...,  0.015625  ,
            0.06835938,  0.06835938]), textin=CWbytearray(b'd5 df 09 e3 25 49 98 f2 6f 43 87 01 a0 e0 11 3d'), textout=CWbytearray(b'06 43 f2 46 14 e5 56 c2 5e 9c 8e 75 e8 e2 d8 99'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18457031, -0.14160156, ...,  0.01757812,
            0.06640625,  0.07128906]), textin=CWbytearray(b'24 89 51 f8 0b d9 1f 40 ff f5 bc 43 04 64 be 11'), textout=CWbytearray(b'ac a8 0c 64 8e be 1e 5c fd f2 7d 6d 2d 6b 9b 04'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.18457031, -0.13476562, ...,  0.015625  ,
            0.06347656,  0.06738281]), textin=CWbytearray(b'26 c3 50 46 43 2f 55 01 15 2a 1f ce 47 fd 19 d8'), textout=CWbytearray(b'a8 19 37 87 83 8b 15 b0 26 ad 78 0b ed 02 e4 01'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18164062, -0.13671875, ...,  0.00683594,
            0.05957031,  0.0625    ]), textin=CWbytearray(b'0c 57 00 ec 91 cd 7a 92 06 4e 14 b8 cc 20 cb f0'), textout=CWbytearray(b'1f 4b d3 59 cf 8c ca ff 14 be 7e d4 9c 95 ce ab'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13574219, ...,  0.01367188,
            0.0625    ,  0.06738281]), textin=CWbytearray(b'e7 86 7d 2a 3b 8c 4a 1f 38 8d 29 4d 45 ef 6a b1'), textout=CWbytearray(b'6a 62 e4 09 0c df c7 81 d1 39 42 61 6b 9c 08 0a'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01660156, -0.18457031, -0.13476562, ...,  0.00878906,
            0.06054688,  0.06347656]), textin=CWbytearray(b'a5 b2 37 bf 71 5e d9 88 54 2d 52 26 f6 89 05 ce'), textout=CWbytearray(b'eb 5c 46 fb c6 43 f3 4e 74 a8 a6 24 e1 ec 03 3d'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01953125, -0.1796875 , -0.1328125 , ...,  0.01464844,
            0.06542969,  0.06542969]), textin=CWbytearray(b'06 ab c9 13 cb c0 3f b1 f1 08 39 0c 4d 87 e1 cd'), textout=CWbytearray(b'86 a6 3d 07 7c 56 76 51 e5 d9 31 8a 8a 6a 61 cf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.18261719, -0.14160156, ...,  0.01269531,
            0.06347656,  0.06542969]), textin=CWbytearray(b'bd c4 4e 50 f8 16 5e 79 ed 77 63 d7 1b 89 32 da'), textout=CWbytearray(b'b9 15 6b db 19 e4 79 01 cf 49 2a f0 c6 b9 7d 40'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01171875, -0.19140625, -0.14160156, ...,  0.00976562,
            0.06347656,  0.06542969]), textin=CWbytearray(b'ce 6a 04 5b cc c3 92 36 38 9d 7d 66 a8 93 00 4c'), textout=CWbytearray(b'8f 9f 18 ce d2 44 d4 24 90 9a ee fc 4a be 65 73'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ...,  0.00585938,
            0.06054688,  0.06054688]), textin=CWbytearray(b'e7 e4 b9 ba 57 a8 54 0b 74 1b c0 38 53 82 2b c9'), textout=CWbytearray(b'f6 44 07 5e 70 4b 86 de 14 de 4a 0b 48 5b d1 38'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01269531, -0.19042969, -0.14355469, ...,  0.01953125,
            0.06738281,  0.07128906]), textin=CWbytearray(b'8e 63 f3 c7 9b 9a 51 17 0d 99 24 83 f4 a5 2d 4e'), textout=CWbytearray(b'b4 78 86 f0 e6 33 9b b1 bf 60 01 22 25 5d bd a1'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.02050781, -0.18164062, -0.13671875, ...,  0.0078125 ,
            0.05859375,  0.0625    ]), textin=CWbytearray(b'ce a8 c2 05 83 4e 40 b8 2a 77 b3 c1 e1 17 04 fa'), textout=CWbytearray(b'33 75 9b f2 b2 90 47 25 1a 4f 07 35 4c c3 5d 7b'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.18652344, -0.13964844, ...,  0.00683594,
            0.0625    ,  0.06445312]), textin=CWbytearray(b'13 9a b3 4a 56 9e ae 37 1b 53 34 e4 1b 24 f5 87'), textout=CWbytearray(b'32 56 3b f8 4b fc 0b 55 ef 9b 40 da 9f 5d e9 3e'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01757812, -0.1796875 , -0.13671875, ...,  0.02636719,
            0.07324219,  0.07324219]), textin=CWbytearray(b'28 b5 ca b8 c3 d1 7c 9d 6e 4a 4a fa 3d 02 c8 e4'), textout=CWbytearray(b'10 1f 9a d7 e1 6f 40 20 4b d3 9e ef eb a7 eb ae'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18554688, -0.13769531, ...,  0.01757812,
            0.06542969,  0.06835938]), textin=CWbytearray(b'99 37 99 ec e3 04 87 bc 3c df 91 3d 3c b8 d7 0e'), textout=CWbytearray(b'e9 8c 89 9d 18 b9 d8 24 48 41 69 27 48 b1 df aa'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.01367188, -0.1875    , -0.13964844, ...,  0.02539062,
            0.07324219,  0.07519531]), textin=CWbytearray(b'e5 30 31 64 9c fc 4c 2c 8c 00 ca 66 2a 98 6a b1'), textout=CWbytearray(b'71 a0 63 5b ae 4c 36 bc eb f4 f0 94 c7 dd 0a bf'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    Trace(wave=array([ 0.015625  , -0.18261719, -0.13769531, ...,  0.01269531,
            0.06445312,  0.06542969]), textin=CWbytearray(b'ad 1f 63 e8 91 2b 06 01 b9 56 98 36 78 f8 ec 80'), textout=CWbytearray(b'5c 5e 07 75 ed 8f a2 6e 45 8a 92 32 8e 96 93 64'), key=CWbytearray(b'2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c'))
    


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
    0.0066878140872365655
    0.0066878140872365655
    






.. parsed-literal::

    0x7e(real = 0x7E)
    0.004783589893082835
    0.004783589893082835
    






.. parsed-literal::

    0x15(real = 0x15)
    0.00462203833320474
    0.00462203833320474
    






.. parsed-literal::

    0x16(real = 0x16)
    0.004237567152938139
    0.004237567152938139
    






.. parsed-literal::

    0x28(real = 0x28)
    0.005072979974536918
    0.005072979974536918
    






.. parsed-literal::

    0xae(real = 0xAE)
    0.003304593040929185
    0.003304593040929185
    






.. parsed-literal::

    0xd2(real = 0xD2)
    0.0055669058318514875
    0.0055669058318514875
    




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

    

    <div id="7a8848b0-4691-4cbe-933b-56f47f747cbc"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7a8848b0-4691-4cbe-933b-56f47f747cbc');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="4d702191-52ab-481a-9385-818d123704d9" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="13b70d93-3011-4b61-abea-6f28cb4c6eb1"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#13b70d93-3011-4b61-abea-6f28cb4c6eb1');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"9e03330c-427a-4260-b164-66a0daf5d18d":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"},{"id":"1042","type":"GlyphRenderer"},{"id":"1047","type":"GlyphRenderer"}],"title":{"id":"1050","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1041","type":"Line"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1040","type":"Line"},{"attributes":{"source":{"id":"1039","type":"ColumnDataSource"}},"id":"1043","type":"CDSView"},{"attributes":{"data_source":{"id":"1039","type":"ColumnDataSource"},"glyph":{"id":"1040","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1041","type":"Line"},"selection_glyph":null,"view":{"id":"1043","type":"CDSView"}},"id":"1042","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"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","dtype":"float64","shape":[4000]}},"selected":{"id":"1059","type":"Selection"},"selection_policy":{"id":"1060","type":"UnionRenderers"}},"id":"1044","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1044","type":"ColumnDataSource"}},"id":"1048","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1046","type":"Line"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1044","type":"ColumnDataSource"},"glyph":{"id":"1045","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1046","type":"Line"},"selection_glyph":null,"view":{"id":"1048","type":"CDSView"}},"id":"1047","type":"GlyphRenderer"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1052","type":"BasicTickFormatter"},{"attributes":{"text":""},"id":"1050","type":"Title"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"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","dtype":"float64","shape":[4000]}},"selected":{"id":"1055","type":"Selection"},"selection_policy":{"id":"1056","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1056","type":"UnionRenderers"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1054","type":"BasicTickFormatter"},{"attributes":{},"id":"1055","type":"Selection"},{"attributes":{"formatter":{"id":"1052","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1057","type":"Selection"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1058","type":"UnionRenderers"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1059","type":"Selection"},{"attributes":{"formatter":{"id":"1054","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1060","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1061","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1061","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999],"y":{"__ndarray__":"wHVcFtc7LD8AAOCGZReKPgBkByzG+yk/ADptpiaUID9AkHqXAYooPwBAc1vsXvs+AKgV5snzIz8AMtVCk2UfPwBooYkHnSg/ABCIbqYZDz8AIx2OFGEnPwAFD4sjFyk/gKlhhVj7Kz8AuI67jOsePwCA35NwIQY/AJQGREP9Ij8ACD18bckkPwAkmD4kbyU/ACynFmhBIz8AIECy2QUoP4C3MnYaRCU/APDRGjMJID8AwjJoBwQsP4CNU5AnvCg/gOX0AYG3Mj8AMCo7ChsZPwCMmd8Riyc/APQAbvUGFD+AeEDWbLgwPwBgHUWBpCY/ACAe5LVbED8AOK+YZ+IXP2iaxA3Y/TE/ALDR4iQeCT8A7Kkw7BIiPwCszcJFyR0/0K4EJw+FJT8AlBFTXDcqPwAA0TcgdsU+APCUWLLUJT8AjzqtLrkpPwCMH4ywxyE/AFq6qR0BEj8wgw2g7yYxPwCuB8n1eDI/AADHLGtFxD4AOAIwJtkmPwA6uXsfQic/4EVHOCdFMD8AcJzKkpsaPwBIb/IYWCY/AFyvGRDqFD+InuJdCBEsPwAw9NyNIiM/AFBP1Vs5JD8AiGlSLqgQP2DsXIX7hDE/AIBv0YnM/D4AEBBuPQopPwBWmYZmVBw/APD5HmSQJj8AKDGrON0qP4CENYKL1So/YIHj/Y7qMT8AjyL4LBUwP0BmGsvxFi8/AAAgG9RkJj8ATLMPVYkbPwDAC32tSyI/ABL6Jct1KT8AKoM34A8oPwClf/KmGzE/ADJe/eziMz8AJgfojYolPwA2DnSW9zA/ALxmOP0LMD8AkDSZtrQ3PwCAm1NUVS0/AIwoGRpiNj8ANIYdnn8wPwAEfakfiTY/ANo4R7q5KT8AzP6MMjgxPwARsMsh4jc/AP6iEQoVMD8ARtFF7kArPwCOhuLRLTA/AG4kV/W5KT8ACvgWUKgxPwBOc7mtay8/AHB/nxCoLD8AcGH+QJs0PwCq+0H5oDM/AL4QhcPJKz8Aw6FjW9swPwD+h2fs/i0/ACCCmPDNMT+AOSNYp4YcPwAVkCGZlTA/AO5XKrCILD8AlDKevX8zPwDoUZ1BJAc/ADq8HQY2JD8AeRRhveIQPwBU7+e12is/AAhXO3NvFD8AOgLlm6IfPwAGQs8NfiU/AF8oLPBCKj8AoIL4Y98IPwCgOwMqSRY/AFD+1GxyID8A0bkFgD4hPwBIGeEdCR4/AOhmdXtiEz8A5pjPmBskPwARDstDVCA/AOBF07iS8z4AaOKmSJgOPwCTb1neLiA/AOBikmY5ND+AkeN2MhUnP4AkajPwKzE/AMnWqAAFKj8AlQmi/z82PwCUeEQJ1hg/ABES/qy0Jj8A98myd44XPwAoGausAio/AOAYCbOE4T6AWh1760oVPwAvHVhXhRo/AEghQibJKj+AYaZBHBkZPwBx/fEGqic/AKbV33YNLz8AJ0EPiFAwP4C/+yYe4yc/AIe6escKKD+A5n4yKE4xPwALMjCKYzE/ADR7oBp0Lj8AID1UHcM0PwCcL4rtezI/AKwYAadHND8AC08eQiszPwBmsDn0CDE/AIy3bAzKLT8ARjLgliEzPwAD5tKPiCU/ACYI5i8GMz8AcM450OQjPwAID0hEKDY/AIQKBB72Kj8APmuOYocvPwCyrdXdjTM/AJhG1cuMLz8ARMFj3mwyPwCQFCRNTCg/AAbRReCAMD8AEEiLM84tPwBd0H5bwyY/AByS80k3HD8AJDfvBcUkPwD6SVGziSY/gE9vXppvJT8AToyCvN0lPwCP0el9QyE/AFlqfttXMT8AUJrAhzkPPwDiC1UKECQ/QIPc/0k4JD8A9dJ2lpExPwCoHkQpbQc/ANY7EUtJKj8ASIUla1wdPwCeIytICik/AAK8MC9MFj+ANMlezQ0lPwBAChA6vC0/AL7WmpMvLT+gERxUmeYQPwBoDCFSbig/AIK2ifLWLT8AQ2s4+YwxPwBvoQqpRDA/AITa9UU2Lj8AnD0ao5MwPwAqFVCZBjA/ANzFDNP7ID8ATntCYMcvPwD0ULMhQS0/APC+ZuddMj8ANm3vGkYqPwAIn5J4hi0/AATUvYZULD8APtBI6rwyPwDIRcz+dyI/AA7noInrLD8AzCkRtdonPwAwIyKQFSw/AABSCXAGCD8AdarvvigyPwCYB59bwy0/gCbB6PfyLT8AtEWIejElPwA8qFdDQSc/AFy+rXxAJz8A2jF5yuojPwAAqyY93A4/AJRnmsEsIT8A6l8t9zslPwAin1eedCI/AIBcCGpmDz8A2DoU9I8jPwBApJUl+Q8/AKyOhXV6Hj8AAF3INAn4PgDwHEqJuhQ/AFBg8te0Bj8APfGYFQYsP2D1i4ceiSc/gOCufH93Mz8AbAXSbcIsPwClGX/y3TA/AKZcxjYvLD8AWLCQ+/ojPwAenCGMzSI/ANxjpN2CIz8ApWtb7kc1PwDkQdswpC0/ANpJbOF8MD8AWN5XBY0uPwAWxOHl2C0/AADYceSRKD8AsiUghhEqPwCEsGGlBCo/AOgcoT13CT8Ayl/wJbAjPwAwvUIHiw4/QACwZlOFJD8AkKmhIgsBPwCglb+Myx8/ACQGcfWuFD8A53rWyY8gPwB0JcctEC0/AOAgkmXgAT8AwHX7c4PjPgD0yaS3g/w+ADCv6LsZHz8AcNw1BxQRPwBguCcbLAE/AMDM7T4Wxz4AwKERxFQaPwCYmpqB4hM/AMHZ0KXWHD8A3FDbcUcdPwDndY9TYR0/AELHtCCdID8A9oBxpckiPwCEnp6GjCU/ACDsZrgHDD8AhPV2bu4VPwAwycNB1RQ/ACCLLLrtIz8AfBJzOfYgPwBQvKor+fw+AATETQ1bGT8AyEXtSI4oPwCkcJq5LhQ/ALQrBj+3ET8ABH9bJAwRPwBg40w+yh4/AF7H1XETLD8A8O/dl+4kPwBshQQophw/AL6E07dV8T4A8B3pyzE5PwAyrkrEZDE/APC3g9f+Iz8AHINRHdYOPwDwfU4dLi4/ADiKSVQlGz8AgEwZP98FPwBgQ34O2hA/ACijOrOdIT8AwM6ZNTYAPwCAUpMjOAA/ANA9XeN3DD8AwKTiu8ntPgACmf6pnBo/AM7G+7zfCj8Am66+BeQkPwD/zIMVbgc/AFQM1m2iHT8AcBy0WM0gPwAgeuxd1x0/AJ4yzXRrJj8ACrIXuhAqPwBot5sPiyk/AChj8suKID8AnusNUkYkPwBoYQRWXgs/AD4+dFt3JD8AbNEDYXQkPwCnpg1WtyI/AFTPc5c0Ez8AeM821GgcPwCw3ciA+iA/ADJXUEcqJD8AINiuCFP4PgAINt2HGwA/AK0dCgiIFD8A/HaohMUtPwBolZeBOhM/APgTIkGyET8AF70oFpoQPwD40hDXBys/APDKX81F9T4AiCDNMTICPwCAS6CviZ4+AMBmqUgkLz8AdKFhCswmPwCQCDknbyA/AF/KT65rFT8Adsb6Ccg1PwBsHbgPDCU/AHgs57RlCT8AkP78JM7OPgAMIFhLWyQ/AAjPV1yUID8AMBJJPkvzPgAoas57ZOE+AMqK2mfcID8A4DjBThHyPgCgOvEFNdU+ADqeU0HLAT8AuCccd9MGP0Bwo5O4aQA/AKA9Jxjc5D4AOLK5WfkePwBFtwfddxE/gMkr/zjHFz8AQGc6rxCzPgCAAUj9b+M+AAKSSqReHT8AEBdRTWn3PgAIpNnutBA/AMC3pG3qEj8AAJBmg9OHPgDoaMgg4eQ+ADBU632X4T4A4EAN5AsIPwDtt6QhFRo/ALxW4m2jFT8AeJONKgMPPwA0zI1/Lxc/ACQMNM65KD8AkBo5EBMxPwBKVg8gVSc/ADwsjlxkLD8AFE54TPkiPwAsbcfD3yQ/AIoWT5tkJD8APIYByzQsPwBudow7ZSA/AOiAzwxBIz8AcAcsc8YbPwAAjzASzRM/AJBc7oY1DD8AEL9FpKcRPwA8POtLUhQ/AOCIgLxtBT8AtExQVT00PwAgJGpxBfo+AICDpbmWxj4Ag6qXbHT4PgDIDIhx2iU/AIBOB3CW5z4AEDQ/BTEJPwB/OXoTxBA/AGYIOCiCMj8AAFg4d/P8PgDALJAOaR8/ACCUx+OS0z4A0ALiMHEkPwBclcbekCI/AMAs/OK17D4AeKdAY+wQPwAgNmpTSfU+AEAF2RkdEz8AEjolvtYQPwDYSLczs/w+AGSPMr1x8T4AGOUl55sJPwDIP4HRChs/AEi4BOH7CT8AvLL/Na8bPwBAdGaw7e4+AFCViWeaBD8AwE6P0rgFPwDGnoSquyc/AGCnFsk2DD8AjHTuEhAdPwDQSeV9RwA/ADBEKces+z4AhC0VWY8QPwBgk6Lw/QM/ALhjWUzsFj8AXKa0UOsTPwDw8LmkkfU+AABDZ0OlCj+A3onw9MP4PgAYmDC3mSg/APg687DZEj8AlLzb2V4WPwB4PU+7F/M+ABib4F2YMD8AUKtXRdcbPwCmnoSjWyI/gIp4c2YsGD8ARP/EoRgxPwCEXO7Zaio/ANjDq21yFD8AI4ufm4oQPwDQuR9qzwk/AH7D39k+Fz8AqIPpkBICPwBQ29+x7vA+AFKXJQHL/z4AcNz/76IAPwAo+S8u1wM/ACB6RVXj8T4AEBn7pqT9PgDoUmQuNw8/ALCh1Exe/D4AwB31QY0fPwDo5G4nIxw/AKAIMs5J+D4AKPaUp2gUPwAAdhdc7tc+AICuPbdx2z4A4DiS8bryPgCwcMlwGvc+AEBA4eqGDj8ADDLsX7I3PwDRPUP55jM/AP2SyTq3MD9A516p6RcwPwDMACnMaDw/ANgB8vvKMz8AuqWJoigxPwAT/ygX8yI/AEgIjjfnPD8AzM59D7YtPwCACHSuSy0/gCMgCFG5ID8AmD51Do85PwDouN5C+iw/AHgRo1bZLT8AjEOMgg/zPgB4/983YRk/ADDqL+0zFD8A+q4JpToMPwAUaOzHaBs/AP7DF/bpCD8AoI09OvUMPwAIpVCHkAE/AID9Bic64D4AUNWNJPwLPwBgwAeCRBY/AFjxT3vpBT8AAKh2iB28PgBMeN1D/x4/APChhAbnDz8ACNklR5kFPwAAuHAP3ro+AGoDm0hZIT8ArMVHU3gYPwB0kHr49hI/ACD93H7EAD+AjMCF9s9APwAyZ33hNDQ/ACAA3SbFMT+gSzuqd7IlPwBMdO4EUEI/APSVBbVWNT8A7ZMDr9ExPwAYEYfBoyc/AGjtXgBLLz8A0sTzTmIiPwB22loNMxw/gHZ5Ek8OKT8AwNJG4LgwPwA0cjtbdS0/AEAR0v8EJj/AP2IWGX0jPwBiAOXfzDA/ABQ3EKMQKT8AiOvCbXoZP4BvCtwZxRM/AO6q1o4PND8ARGrobFUfPwAfHBd8hSY/ABjXWGdY/z4AH53iYx00PwDIS77hoyc/ALBNdqFUJT8AgJLulFbcPgDC97bqViU/ID21Ekw7Ij8AXVaQbscQPwBY8jLoEQQ/0LLMWRrDKT+AuhHNpLkpP8C7dZb/uyM/APhUEMvBFD8ASKCRc4QsP8AHN24KiCk/AELP+ahHBz8AAL+9mxrnPgC5OEBT1DY/gAMFjoEmMT8AWmKx/konPwCIDEQ5aRE/4Jw2KdMpNz+AUm5a4zAxPwDKNTml4ys/ABiAjj8BCj8AuGulh1EZPwAAb6BzEeU+ACCRzkjiET8AANHzlM/SPgDYlAZugw0/APCHq2nlFT8AMJZ49+gKPwAAmK9oJ98+AHhetAbxJD8AJNMLwTEiPwBIahKtdRA/ACArpIhW+z4AJHqVqRoZPwDIHpYaiRE/APjgcYL+Fz8AEG9/1sUAP4DCHlUkVEo/AOMQZcvVQT+AdsKsaxRAP/AZsesFATQ/AOGYlQJsSj8AFj7ekt8+PwBML5URtTw/ANidFMz6Jz8AlsRWzK87PwBQ/1cv1C4/AE6HSQa6LT8APB4Tx9wGPwAc1L3g6T8/ADiZO9W9Lz8A2Nt1j6YmP4Bsl/GGPiI/ACAEEJAlKD8AhnnJBychPwC4blUuUAE/AKAZLqYZAT/AClD6Ts4jPwDmlhd/1SI/ALCrI9kKCT8AwJQnV6TqPgC8dg2YlxQ/AFBeBKf99D4AcEUHy8kCPwAA7/qEW/o+AOA9PEYs+D4AkC+XtfkUPwAImZLVTw0/ADRiTNZYID8AUPPfPckBPwA4+Y2crhk/AICRJ6hDBD8AsHcKQoYJPwAJ9P3Q2DM/AO5ZwX+4Jz8AJIYwe8AZP6AKu4PZlBg/ABIQMr6gMz8AiPVa7tgfPwDYXv+lRxw/AARiX6w5BD8A2ARkjdsoPwA0kBpAcCg/AHwvdHvJHT8A+iu9WMXyPgAUISAw+zQ/AFijtKjqIj8AnoLxVo8pPwABaw1g6h4/AGhtkbouLz8ASFEoVO3zPoDyRsQ/WyA/AFzXYWsiJT8AbAa8OnD4PgBgt2wFavg+AOCgjghT8T4AgJSmrpwdPwDAuEMDl+U+AAidRuBXCz8A0DMcF9YKPwAQ2g9u3AQ/AADzGgob8j4AQPaUp2j0PgDAHIUJN+w+AID5ohbU1T4A0H/ehXgGPwAUFGZywxA/AHDbZXcs/D4AuJZuf2cQPwATMy2O5zk/AK1LKlxbMT8AkldzPOUnP4D5zqCjewg/AK7bWqYoQD8ApLXXLLQjPwDyvVvQmSA/AOTsag5RBz8AQtG4IhM2PwAsoUwvsSE/AKrx7FcxID8AYYlhJFwCPwCs8+aybj8/AOxBThJBKj8ApIc9RIkePwDuESZQ8AQ/AABxZk1iET8AANB1UJnbPgBMM9Y6IA4/ACDSrhLn6T4AymD03O4HPwDgbSmBoPA+AJAGLyNt+j4A2KMUbzEYPwBwUMaPzAI/AKD/9Asc+T4AEKHsKlUOPwBUXS+nqiM/AIBYF47Szz4ASE9bGhcaPwDQ2MU73Qw/AAzxBDYoIj8AbCtBZZ4FPwAwJZG1qQM/AJgyvweWCT8A9HVGDHolPwDG88XC7Sw/AMRwycqvGj8AtAEnFE8WPwAYkzsrJ/o+AIjKoUxSMT8ASAIByYInPwDcOTjn7BI/AJQboujjBj8AzJTJScItPwB0ztRbHSQ/AACgQzCxjD4AwCpSiXrmPgDg6JwZuDA/AAjneDKFFz8AOOcokngHPwBiIFpJNQA/AI5pxMSCNj8AQFpC3Or7PgDIawpWrhw/gLghYP5NED8A0Ib3/30jPwDwM6t/6AI/ACDEC9PDAD8Ag6BkYN0RPwCKVkz4QC4/AASJh9AdGj8AoOPbAHIKPwBAbBob8/g+AHj3BjHOAT+gYNLmINIQPwDJaGhvuh8/AOCNTZ5EID/AljNLwAHzPgAcsopIeOg+gIQrT38+FD8A4O1gWLruPgC8YDH6TwI/AOeTZ9jWGj8ARH/HBhn5PgB4XT1uFQQ/AOg3IRttOj+AzYzbIp8tPwCCDNFXzCQ/ANA5lvsuBT8ASTofCgkzPwAod5Wu5Bk/ADiCJRaRGj8AcM52R9vxPgBwxnqz4jQ/ADBdV0rmIT8A0Ggm240TPwA4UcOFkBA/AOiGoKVWIj8AGGfHxhMhPwDgAPtmnwU/AADOgBny/z4AUAmuGwYZPwCg+/cMYhE/AMBRI1M32z4AcOms5VwCPwDkJrNSLRg/AJBcQB688j4AQIXvU+sMPwCAnp6GjBU/gLbQXsTERT8AjrdSIjk1PwCQe3pusjY/ANKsG20jJT8A0QQaoZxGPwDIHKiWnDE/ANqhVkAoMD8ABSbEwt4RPwBUXCNFJDs/AARAP57TJz8AIGVFxD4APwBIXa4FA+w+AAJkb8X+QD8AJrisLVI3PwAIfWZAmiM/AIQQn0xlCz8A2NR9X7cvPwA6U0U1MCM/ANjgksW0GD8A7BZrMJoKP4CzaGgVJRw/AJBCUmEq8D4AYNgHDh8HPwCwQbMlEyE/ACAmX6isBT8AAqsF88UoPwD8aaSHGRk/AKTzt/uCLD8AUMtaGGUVPwD4XhstvSc/AJJcpZKDIj8ACDt5yUwsPwAA6LJTaho/AOZc/kQaIz8AYF2uX5gfPwC484hL9x4/ACKLx0UmND8AWC3dpDktPwA4+6oqvAo/ANxY5RiAwz4Aak2qZ7Y7PwDkbxcFjTA/AKR5/3gtFT8A8sCdDMcNPwBQXXE71y8/AECOW3NvGz8AkCWtnRT4PgBGTt0hths/AFKU1QSTRz8AioD8GIg4PwCz4pQXDzU/AJwaaMf+8z4A0jTKZBpBPwAH0eyVPy4/ADp9yxVXHD8AFOBFe7kDP4AycZW4eCs/AEyt5we3Fz8AkKl34ur/PgB4ZVpMJBc/ALCxgT1O/T4AKGCGTz0SPwBwmhnZ2hY/AFgF2XOyFj8AsFoHai4fPwCgbVbnfBk/AFRRIaEyKD8AcKfNgU8kPwAOa+ViGR0/AJBc2/wpIT8AUJdBgeAFPwD4dRcCWSQ/AILu/zLcRD8AAWU9Epc2PwBi+8BJOTE/AORUq/xkAT8AlpDV3xxIPwCytjqdjDQ/AKiKt4DhJz8AZIrRXLL1PgAUQZROoT0/ALBrMqa0LD8AQBzauLnfPgCw3mfDceU+AMCpE20QPj8AoLG+reQlPwDwlA0onv4+AAigx89q4D4AIN8sUbUYPwBkKZwomQ0/APp8yweXET8AwwRd08AnPwAsOd02fAY/ALptPFeBJD8A+Ll/yMAgPwDiFYgWpyo/AFhOZdCtIj8AbzgQQ2YyPwDEyIt/vyY/AEAHrGi2Mz8AaOXqbX8nPwAEe0kFwjA/AERs5LBMKj8AqiQITToyPwB6Jh181Rc/ACRf8zZMKz8ARAFp5pAwPwDU+W5eMio/ABh+5OXFAz8AII9Yw8gcPwD8K3LOjis/QOudPNArHz8AkEI24RQKPwBwhzbWQwY/ACCiKI+JED8AFskTPHcYPwC4uYvkhhM/AJzfNbZ0Fz8ADFApWe8UPwBKzZGQAy8/ANi603mhGD8AZEC0fUogPwAgmVCbuBQ/wESx5FmmLT8AnGKegv8mPwAwga59tQk/AJypXaUkGT9QKaVx2AYtPwAEQHUIeiY/ABCvLN9qAz8A8FG+OAUfPwCSNyBoVQU/AD2OSx3gQj8A5A+CXq0zPwBkAwC9IBE/AK4nS9Qp9j4A8PvrnWYQPwDgljh2tto+AATBJbu+FD8APopXFDAmPwCxSEurOxg/ACKUJaWfJz8AEIm+MlExPwCc7lbu+C4/ADQS6ykJIT8AOHxVyD0yPwCIFcPi+Co/AGZr/ICDMT8ACGqkNOQqPwCAJdblpzA/AHTUiXQdLT8AZnxWxyowPwCUMdyabRs/AM7k2qHaFT8A25fz3q0hPwAoowRJ9yI/AJDKDc5p4D4ACD5KumEaPwB42LCz9xU/AMK7FKF2FT8AWAXnM70RPwBADlgW4uo+ACR/kZUSFT8ALwTA6LgiPwCwKr6w/CE/AAA4UcrlFT8AnFDUAwIVP4B3PiRoNRY/ALRzk650KT8AAH36voIEPwAADNvI7ck+UKMv2zlAIj8AoIInwTUIPwDwqQHi8SA/AFBz3OA74T6AUYV+YmghPwDQPFksOTg/AKDC6elqEz8AuELYgP0ePwCgCguF2w0/ABzS/7HgNz8AAEa51WEgPwAAdWNMHAk/AEg8Xtq5Aj8AMEOrzksNPwBwtcKrLvI+cAbVhgnsIT8AyDnFWIUkPwCQ85ZX1xI/AKj03pJcFD8AqDwHjlIMP4AQnvpCXyg/AGBJ9q/jGz8AVyVpEm4lPwCGOn53zRY/APTHWHKKKD9AZCAdfwkkPwDsFYFj7C4/APxZp0LyID8AnLA5Vf4pPwB4AbTRvCA/AJDdkHlvHz8Ael9aqe0mPwCQS4gWCCA/wGMhAOwxIj8AJLRQz5MmPwAEjCJXjCk/AJiS4sRlIj8AeLrm7owjPwDm0DC5kCI/AGTfGdVpKD8AfIISmkUqPwCsIJBuZis/AAyMveLEKT8AAL7oSJInPwCYZnR1FTA/APBexMs1ET8AArmH6NIbPwCom82c1ww/ADh3Fv1WIz8AjHDxE1YVP2CiRRXsySY/gBmnh1nELj8AeMybTfosP/A5i3YAwjk/AGXSgawKMT8AsZG80uM0PwBZecgIOjM/AIw5PfZiJj8AQGicJ1wLPwCYjpPb7xU/AH2swrqM9D6AxvfO6y1SPwC8Gh6P6kg/AKTGXUkAQT+gVxUvAxsxPwCaowg3608/AM+WCBpzPT+AdoKnY/AxPwCyN+oEDxw/AIotbLoWJz8AkJy3D/AUPwAoqintgg0/AGyezTYYIz8ANsXLueYuPwCwFkhCPww/AKwjzeCSCD8AQg43eZYGPwA2MVuRcCU/ANDLkjQQ5z4A9phCJ4MSPwCaudR+oxk/AIA3ztDO/j4AGL6DLmALPwDwsW4UOBs/AJimoSfVAT8A8KpxGkj0PsDhBxtBKhA/AF1yhpnWGz8AzJ8JA6IjPwAbby7klkQ/ALmnM/xjNz8AhKVtwR0yPwA8uYnfTBI/APDxX0eOJz8AyJlgFCgIPwCIBLQmiPM+AOYGxZ8vJz8AFO++srw2PyCz5v412CA/AGHvzXgUFT8AeEyFEywTPwB9W8LLxRA/AAAJfRML/D4AONyKxFYfPwBAKSLgFh4/AAVZAU3AID8AQHbHu+EXPwAAnuV1BP4+AACzFgHksT4AYEtXu9fkPgBIxVrIYxM/AIQjRdgFHj8AKAoeoDElPwBURAiE9ho/AIDOK2pvzD4AQM89gMPSPoBM04UJtCE/APBjndeSGT8AFENbc7QgPwCg40B1Ofo+APYO90qZJD8AGPDW65MOPwCUi2uJUxE/APB91nGQ4T4AVnkL6CgWPwAUJpVliBI/wKWUJ1ekGj/AR14Ep/0kPwAcuUWuOyM/ABAXxIE7Aj8AwEDQEoD2PgD4SI+Qd/4+ADhRw4WQED8AJmanPIo4PwCN3rCwwyk/wOiVBQiMIz8AAL3Rb53GPgAq9Mdtkio/AKSXwt0SCj8A4G1ydVK6PgBw6Z54hyU/AClpz44mHT8AEGZqa/72PgCgAC8tAfw+AIbc15+cID8A9CK2rQjkPgBA4SE8hxs/ANAjl31MDz8AOmbdAMYqPwDA5An/MBU/ACQxufjnFT8ApM6EuxAkPwAQQvdk5Co/ADRKCCDNFT8AIKBG243qPgCg2uIVwAY/ABTMtSJrBT8AxOkF6yghPwCMX6WNuRE/AIwLWmy7BT8Aw+CLC5oXPwDwOT+hByQ/AIALU7KgxD4AW2RBbvUUPwDT9zDZQyE/AJC0RGf4+j5AvhM1asgjPwBLQ1vUqQk/AEhEMIHHHD8A8Iaz3CwPPwDOOVNvdTA/ANwAvu+oJz8ALtMzGJgnPwDGaZUbVz4/gAfVa9SYMj+AmENIBSkbPwBMmixchhw/AIC419S01D4AttPClcoPPwC11mR8vhw/AFyKf8UrHz/AQIvp4V42PwDih/xbFDI/ANxhyeOhLj8ArLe46CIxP0DSOpqybTk/AD6wxcBJOD8AvPxaKgU0PwDr9UkPcjI/APTOSUL0IT8A3L/wr68qPwD4oglfzRs/AEzhIelRLT8ACBVx3LzwPgBsFWwn3BA/APADSxCi/z4AQlfD1ZESPwDgoQWhLhI/AEg6TclnHT8AXMVaIvkGPwAUTb2JbPg+APoH5TAZNT8AkLFgPw0gPwCgNZ5lgAQ/ANiTFyoK4D4AcB/ivyw8PwBMRA83sSY/AJAL2XfeDz8AwKg4WPrVPgBI3635vCU/AKAgYbd6CD8AIObntnjjPgBca9nzHQw/AKjUzf/DHz8AKHQPSAYTPwAomugqdf0+ABwDhNe5Hj8AdMR+b+sJP4DYkv5SOyM/APBiumpqCz8AvO99jDIcPwB2IEzjvxg/AKTCfRUe9j4AXtnqekcVPwDgH5N4ou0+AAxCYzkxGD8AyQPtRzUWP0DC6sV3ths/AFBU5NE8Gz/gc+kez2w2P4CqBiloPzc/APUP7oyPND8AkgWXQHszPwAYJtLcfgA/ANpVSpIRBD8A+nvv+vMQPwAYLVyinBw/iBk4jVCvaD/gSQmwBMBkP4C+4F819WA/ALYktcSGWD+QRnCq+p1wP4C9nu2dwWg/AM7obgDEYj/AsvNeXgxcP4DpB8Yi8lg/gBitxRHpUT8AnnN+NE9NPwAQTKgx3EQ/AMJX80b1Pz8AyaD55BI2PwDcZUB3sy4/APMc11ToGT8ANAUBF+4kPwDubh8lnxs/ALIQrcCaHT8AvPB1GesSPwB5X2hp+DE/AFwFX99aHj8A7ESS5PIkPwCv5Xnd8RQ/AGgXCMEMDD/AOdF0S5caPwDkH6/4t/M+AEgv5/twAT9AiVOwJhBYP8BVt/31QFM/QMZRtyRVSj9AqBCS3nw8PwCOUa8YGD0/AIXlLp+QJj8Atfst0Z0jPwBUQkkKKww/APBddYrABT8A2NwU0h3bPgAqcVhBgu0+AMhXln11FD8ALGLE1CsLPwCgLlaOJPg+ALTbbijBEz8AMJX6/ocMPwBgeGxSfAM/AGgKSO4RET8AgNjfauMYPwDwCBifQ/w+AOhLxUiJGj8AfI7j1ZEZPwD+J+FQ7CI/AIAisgvq3z4AHSTWmIc1PwDmDxU/ni8/AK07xgzoKz8AERPauVcnPwCbcGsCQzE/APCu+zdlLz8AUcS7MrcgPwCuJJstKy4/AFYFFj7eMj8AUMgXkXUxPwCV+VLQXCk/gO1q3vszKj8ATcypWdowP0B5KzNSXiw/EO4IujBsJj8AuKrwF6sTPwB7XlwHcjk/AA5V1KXtMj8AfCo9WyozPwCAz4ELaiU/AKg1uPzbHj8AEEgmEjz8PgDU3wYNSQ8/AECm9oqC7D4ASFNtOWEKPwDgBjF0fNQ+APCd+jv/4j4AgAt7VdzCPgCYu4U4ZP0+AKg5bAfkDD8AGPHHGMcHPwBAsuOZGdA+gKhkGX/kDT8A9Nr+/SoLPwD4f945ow0/ACBfWKsTGz8ALgXEn/cWPwAAee//vRE/ALRnoSgSFD8A4J66bvcJPwAPPqnGMDU/AGJepuxQJj8ApPWs3/QpPwCma0cZehM/ACnY3rfLMz8AzFaEBiwlPwBlQT6gMSw/AD5M7/D+GT8AJPLTJxgyPwAGzAwqXSg/AODdaiDjJT8AWuwDO7oSPwDK3yLgkyM/AE4h63EMJj8Ak7f7bXwQPwCEbvyCGRY/AEDn5BSSHz8AUCDE2jIePwAm4LgJIRI/gCa4mFiEFT8AlAYow+csPwB1T4UOYiI/AOWNnZ9GGT8AMDGYW5zxPgCvmLoXljI/AOuzhELAHj/sq6rwauAhPwAAZ9qjVPo+wLFZQH0bJz8AABZZZhv9PgDw7C3x7/w+APAvkgAZ9T6AQ30drd0SPwAAc3UbZfc+AGBRyplABT8AUGrc75nzPgCsNDXnRAI/ACysOv7UEj8AkCnuc0r7PgAAyfn+sOE+gLWsr5F2Ij+AIq3g8wYjPwAaoZV2mBk/AEgRFteAAT8AY7CDM30xPwDmzLklfyI/AIHnWe2oIj8AV2Hv2zgPPwDAH/jlCTg/AHhkcH92Gz8ADmG5tgckPwCoLrsC7Oc+AFAEYZAUPz8AVDyu27srPwCQ8tQ0xSo/AMJzgNEzED8A8O3hU/cpPwBG86kmWCE/AKiJpQmYGD8AC7xtpkIEPwDoJJbEHyc/AOTmhFWrHz8AUG0l34HsPoBahMqsKxY/ALpNaI4UHD8AdtO7J4UXPwB4Har8y/s+AED9Qae21z6AnA4X4pc1PwDNxvRcWh0/APtG3ta2Gj8AALxf5jfpPgCER2iK6BI/AAIy8xJtIz9QzbzIsEgUPwBM7BYY+xs/YM3Ec/h8MT+ApoaRjMk1PwDyMi2HhzQ/AGTAje3sLT+ARE0Oils/P4CYsW1hIDY/gF1FZTJBMz8ApqYnQEgrP4AYP6cVdyQ/ACyZePIeGj8A0E8uwvobPwAE4pFmHRQ/AOgh+z2xFz8AYAJf5CQfPwDgHN5a2BM/ANDdyIdaFj8Azn51racQPwC0N/jEGRc/ACA1jqA7CD8AqNNdx20cPwDJ+at7kzQ/APibankfBz8A6QXISHYgPwAv/6KyqhA/ADLDcbTiLz8AoMUK3IEKPwCYTQNmIvU+AEhPYnqc5z4ArIewJSYrPwBwpS9Smgs/AGB4lKni6D4AkJYcjkvWPgBA5LVicA0/AOjZSu5Y/D4AUKbHLSwNPwCQsgbiCSI/AMCvExBHPj8AuHeR+PA3P4BFAggpCDU/AFAckXHSJz8AoDsDKkk2PwAvKV/9dyg/AFFGHrJ+Hj8AABWgORPwPgAFjs37iUY/ABlIEzX7Mj/gWaNpHrQrPwD4zTBrJRU/QKL4QAbeOz8AxBA8zxciPwD0+MjClR0/ANAVzObCAD+AzL5yCoQaPwB4UGEbBfM+AKCcm4/a7j4AgK5lWq3JPgAukYrL+xk/AJAwsDmT8z4AQK0kJRgSPwAQH8w/ugw/ABZi0jqhIj8AdI+QK0kXPwDIw6sT3fA+ACDNTbIn7j4AQOt+iD4jPwDAdgY4Etc+ADROhhNkAz8AtJXpJoH0PgBc0kuVmSA/AB4p886VFz8AKIiR4En2PgA+gPrHeB4/ANBwoc3eCD+AnzzRduEbPxAQFKgG8Aw/AF7RK15FJj8AdbJc8W48PwAsCfCo0i8/AP5NajJZJD8A5W/FGtELPwBed6PP5D0/gN8wP6kFMT8AAPA0pkD9PgAs4SHi8Qc/AHnFxv2lOT9AIxGVLnk0P/jIb2CfqSY/ADDcYm3wGT/ghCsYytU+PwDbfhb7bTk/ABJzOKvOLj8AgKGegcIUP0CgaRwelzU/AGUs9RrbID8AUjozjKEWPwCQhTV8dgI/AJTJvoz0FD8ALKZBuyMgPwBwrptxHgo/AGSLuylgIT8ADtkelN4pPwCwlMLi3Po+AFARe0tI4T4AQBT6UaHqPgDsAvdXYSI/AECutaAk1j4Abqm7BTwUPwDM2uLJ6v0+APAWefCkBT8AuO2+Emf9PgBU5bsJyRI/AN7mDKoNEz8AtHbCYJYbPwD9X06b5x4/APqj3gvrHj8AjGBz2lEnP4CBb3eaM0A/AI1oEa09MD8AV6Ed2bonP4DV5taSnBI/AAdCtNgqRj+A44AZTLUzPwBp7+cPcC8/AFDQ9qzL/z4AqiysNOkhPwBAgLgzTPI+6MV8gBxrET8Ajj1PFa0mP5DTsE8hRkI/AHcc/flJPD8Ajp9YqyE7PwBjDkVAATc/EJxlCbSKPj8Aa/g/WiY8P4AOMdOI4zo/AFv8KXU/NT8Awfa+XZ4uPwCq9DX04yo/AGxyNvJpJj8ADDPptmsuPwAwroZDzvY+AFgZVKxwHD8Augho5boIPwCpRlYa/yg/AHAqbLiAIj8AsMwJGcEQPwDMMrqeihI/AKTrcnPY9T4ARC7At8wnPwAwagST1QE/AGDKOo7bDD8AoI3mhTgYPwBsMz1MzDA/AICYWrmkGz8AAHdt/ejgPgCA4UuKZwc/AABMTPJ+sD4AcAB4bYgOPwCcF6WW9CA/gFdi7sh2Ez8AgLc9qBPZPgCgKhW47hQ/AEyGo2O9Cz/AifsY/eIjPwCo12/mt/w+AOavtk0nKD8ASxY6bRQhPwCw6zy2/Cg/ALDJ5j3wDT+AS2WJohohPwAMmiXuQBQ/AL4ooc+5Ij8ADzphPUA2PwAVDLTKCTY/LLdzVy8LND8A3vjxXV40P0A9lCLLuEg/AFKgyNW3Qz+AgCmGxKZDPwDnVQ11sDk/gBmdVE0tSD9ACRs7FU1CP2CzRM4CZ0E/AAErPw0vNj8ALGiHrTb/PgByLZtqoiQ/ALBMtH5CHT8AWFoh7GkpPwCnaKUyhhY/AL5GeVQvID8A6OAFVBwXPwBGQGKMLiY/ADbG0QaqED8ARnfgP3smPwBsiYIbPRo/AIxob8F/Ij8AeIgEfXEfPwBApL9lGRE/ADDrtEUa8D4ABGiH+QsIPwAYKHw2uhY/AOgI2823Cj8AWk75+2AVPwD4BUcBZAw/AOCB9A2WDT8AbHvYe5QTPwCAqsFgv7A+AOXseM5bEj8AgGMonobNPsBGwvwLISA/gPFIfFmhET8ARpM2FVEhPwD1/55drDc/AEn8r4ZSOT8A65n7psAtP6Al0wvBMTI/AFD5L+IBCz8APDsBf6QYPwBgQ2KOxAo/AEKXzvJ4Fz8AQPU+Bm7rPgDydjwDrg4/OAgyX5SEEj8AhFD8UwglPwBYgL/tZuM+ANAor+lZ8T4A9Nv2iuMRPwDm2x6ItCM/gLN/obTsFD8AKkbA6REVPwDyAplDHyA/APBd6L6SID8AAPKcxeTaPgDoZgJHkAg/ABhFeK6MEz8AuM+kTPohPwBM5+sh4i4/ALCdyY2ZGT8AwNrbD9DcPgCS9g7+qh4/AJi5ThP7IT8AUEODKxAPPwCkkQ3FEgE/AA69zx6ODD8AYPybCxobPwCAegqJkQE/AChSM2RRAD8AMOYdzunjPgBg82wJ9+Y+ABgATsUSDz/zC3t4vK0TPwCMHdSWgRA/AHRalCecGT8AgA6jW6MOPwBAo6bhf/I+gDabD28ZFz8AoL9ASVz1PgDkKelk1Bc/ADp+ygKVAD8AR7KmfLglPwDeIdn7DTk/gMxDobBfJj+M/7VR1iscPwDo6c+H4gc/AAL5oMXECz8Ao20gfdYqPwA6o3fXXiE/AMhyt/tmLD+AmPWXEpovP4D2w9vJtTE/AMNuClHkKz8APHliQlAnPwDAFWyPMR8/AOjg/vOWGT8AqE1adHQdPwBwT7QYgyM/AMpXS/M+LT8A0GMq75UHPwBk31aflQQ/APgONBXF4D4A7JPFmeMuPwAgXixKOeM+ABAupIOM6j4AFwIUn2MbPwA0qOvBKSg/AJhJIP7DBz8AQMQEzdPmPgAoI+x4pBs/ACg4dF6rED/ATWp3zgciP6SyGhFt1xI/AOBnr5xHFj8ADKk/xj8ePwBkPFB0RBs/ANAnFXHjHD8AtEqlsD8HPwAkttneuCY/AD9X3b8iKz+Ascn0/fooPwDIAkelOCQ/ANj/MTc9Hj8AFngTTbAkP4CMK2kWmh4/AN4Eya5tKj+AaxSJurMSPwAiYfbayCM/AAQXIpZ9FD8AjGWe0GokP4CrsOmtkQQ/AJQOsXVDHT8AAAXLpeegPgDEb8UTcSY/ALwIETH+Az8AgApB6CEHPwAAJFWdSho/ACx/zgwJIz8AYN7YpjQmPwBcnTMR1xw/AAQPhMOR+z4AMHkly1npPgAETqepTyI/AGjWhQtKJj8AgAW4MPz1PgBcnSyxUQ8/AFiFwe4hNj8AeB+mQMMmPwCwxsyrXhQ/ALb2EJuPAT8AyiiMXPQ7PwBI0wRoDBo/AOTXeD23ID8AGON7jWDjPgAk7iw/Izo/AMD3NfZ5Dz8AYPNlAwcNPwCIGolkSgg/AAhhAqu5LT8AXideo6o0PwC2s0cXnyk/gI2bQHcUJD8A6BFcDcwRPwB8kX5cABk/AHH8ANp2Hj8AKNQ3z9YLPwBOotGO9xI/ADDx1TJnFj8A0NOOGz4iPwAg8kURKAY/ALBRsGo6CT8AaO6t7orsPgBSWrzK1wc/ACSWtRRKFT8AnMnfg9UsPwBgijbReeU+AMSTltuXFj8AXA+8KxIGPwBq+GhPhDY/AMQ00MwSFj8ALLdQd5QXPwDP1Th1eQg/AAgd7M4NNj8A4Ca6srIVPwDQ4GpuTvM+AAChjghTsT4AUCe8CiI1PwCkw+bt7hs/AGBDfg7aAD/4GKNNkN76PgAMV4QU7D8/AHi9bFVrKj8AiHjt+oMQPwCR7M0xCR0/AMxQaS6iNT8APlxM4OwhPwA67z4QAg0/AHjv1DIvBj8AQtzqG+hAPwDQQ6GwXxY/AAh5JRcvEj+AkX0RPuIBPwCqN8KtqCY/AKxC9KZ94T4A1ao0SbwCPwD+mYNVuCQ/AK2iKu5YJT8ALI0M2GQMPwBgciiFlOk+AEB5Eu4Y8D5Ak0BdfbgyPwDS/+iVwDI/ACaZDbzJMT8ABHmkdYcqPwAuoowSJE0/gNdIxfO9Rz8AD0VxoTxEPwC2h8sHRDw/YMUDFKcOUz+A9eQr9f5KPwBqwA6PlEU/ABlbUWuNQT+AS8bKALo2PwCqlbpvlTE/AFLMROUSIT8AoOCzrtUlPwAKNz8AZyg/AP5HVW+SGz8AIECreYAaPwCG1PwChQs/AKIPhxruOD8AjNma2jolPwD+tJ1zJC8/AOt9s5H1HT8AwApk1nzlPgDAJsbV2P0+ACAZdZWRCT8AgEIvJ/rYPiBNXyL1l3M/4E2dDu7saT+gMYh0wjxjPyA4YIndTlg/QMvmPjvgZD/gLNDAO8VbP8DFdr6OIlQ/ABTyplnmSj8goVLq3lRaP+DfNmmMXFI/QDqSPtvNSD8AEV7MRd0/P4C8aY9ZyTU/ADa1n2qeNT8AzKdjq9wSPwDQqMyDrRg/yBOcId8CET8AZCYdIkAUPwAGYYOflhM/AChqBOYKID8Ach2QZXAhPwANtkVZcCA/ACk2teTfIT8AkdkuBu4nPwAAx6sc0yo/AAz6VNWWKj8AMLl7cnclPwBXhcCjXy8/ABiXsr44Gj8A6Nf+qF8YPwAwuYkygiA/YAGPAQisIj8A/Ot0cbIhPwCgCox5uCM/AH0xzoDNLD8Ainlkk18hPwD4hUNRoS0/gNg76aCtJj+AFEh4qcIyPwB8zgpzjiQ/AEhY/aNBJz8AA5MFZ+snPwAq4FOVWRI/AACgnywvoj7A19tuLyEpPwBCS6cPDyw/AGrJgQ6eIT8ASJczFAspP9ryOq/ZXSc/AJm4PJyxMj8AkTthkcAmPwCaZgdWBiw/AP4XuxZnMD8AGjD3KAssPwDRreJZNi0/gDsEbqvHLz8AyMkXOCswPwCgPFJrvhE/AC6EpxhmKz+A7q/zxB0mPwC0tERuWFA/ADYMOKeNRT8AtG0yFrVCPwCxUNu9HDY/AM6+ZZU7Rj8AXr8WVRE9PwCPtJsbtT8/QKU53zu2Nz8AAA7p/1hAPwAJGwRg5Dw/AONw7LGqMz9QJN5KL08xPwCAgB4BlkM/AC4KuOCnPj8AgGWy+G00P2DAfo/qbTc/AOaezztSND8AuuSPvQ4rP6A9yLjX2yQ/AOTS4XJRJj8ANYehq6M1PwCvEqKkDCs/ADM3szMmMT8ApGLpuQAQPwD8EQWzpDA/AL5TQIevKD8AJIx0SKgjPwC4Kxl2jR4/APbTFI5GLz8AfsqFH3IpPwBKgB1V3iM/ACBq05iaHz8A8Z8NNgtEPwB4GL0boUE/AN1Q4tHMOj8AeCV32dg1P+Dr0xyMw0E/ANOXjXJZOT/ge9k1DN4xPwAe6eBRKTU/ADVchX4PQz8A5Kxepqc7PwC8WWK/vjU/ALKPCxkjMT8ARmsCj+ZCP8AS4V5SiDA/4DkjdCecMj8A7vJ4Hl0kPwDkfkWyWTw/AJyvVujVKz8AZ1eOvQ0wPwDwq3XRhig/AP7b/ExxOj8AADm6orYrPwC8mDnQgy4/AARqPxNSKT8A5HyAI8smPwDcHayncCk/ACA37wXFFD8ALGtFFEAiPwB6ENUJQSg/AKP2iOyXKj8ArytI0+MdPwA+JatMBS4/AD4ZdZzxLj8A0J9uJDQlPwDZPzEqniU/gHRH/FsGIj8ASB+5FqQ6PwDx6nXXqSs/gOv37VPqMT8AXABXydwkPwDepGNdcTU/ALsOK77FLD8AxrCZs+8wPwDXi1GtgiM/AO5DlEJ3Nz8A4xEKIxAtPwAb5qPf/Cc/AGJEJLHWIj+AK9lwhfozP2CMKsS+XzM/QMEw+3f0IT8Ad2WQtsolP0D4gEKbqDE/AF9p628xLD8A1E/lzUgSPwA2ivLynSQ/ADRIoFq+Gz8ANCLhDqsbPwBgqepikvM+ALAu8RldCD/AflZMS3Y8P4C0YOcNETA/yFiDK2q0MT8AD1CAunYrPwCPe6NjEEE/ADcGtXn1MT+AU6FueLQ1P0AkwSXCHio/ACGHVsfXOj8A92yPYr8vPwD0AKu/MjA/gOBza7gDLT8A026tNC8yPwArHdLr3DI/gN8hxSZAJz8AIgkT3KIhPwDoCB5acT8/APUKvDbxOT8AdQijC6IsP8AiXzdh/TQ/APR1fHYgND+ADH/ARZ4yP0CBJoPoKTA/AICEm6lqGj+AFdUoonQxPwC0FqE5SyA/AGlCtTgNLT8AemsWxakNPwCIv04JZyA/AICCVmphwD4A27CE7fQbPwDonbZkgwc/AKq5TSLOPj8AuGFye7oyPwAHRGbdrTA/AO7+eGTKJD8Aqhu3YgkzPwCpJ74I/DA/AL6zb24FHz+Awc7I7CEjPwBKwLydeCs/gCpUcjQCMD8ACJ7S6/giPwAJuewJZS0/ALozKn1LIj8AG/oleEAbPwAk86eJcx4/APCxKj28/z7AMNraqI0wP4ADW53m8Bs/gPxMr2/MKT8A8BqZz/kQP4ATGN0F1SA/APBT8Oai+D4AgNHG8N0LPwCYbYykWAY/AAAffJHt8T4A6Bz/UbkbPwAgxzMlYPU+AACY5X+Y7z4A8zB1bUETPwC7JXIdmAA/AHi3PagT2T4AVPhaNeQXPwCaGU/wLyc/AJzaJkBxMD8ANAercCkrP4Av0JiRKSg/ALv60RrgMz+AUzbl7e0wPwCGx1YaGyk/ANYMFerSHD8A2nCNpds4PwB+OIKGCyo/AOHnnnc8Mz8AcrHmo1USPwCrvzKQsT8/gAQKl+JmMT8ALI1rN2k1P4BTJPJ5kiQ/AKrFi30pMj/geKwZb0kpP4AUWwkV0xw/ANjCs+C5DT8Al1+Xenk4PwBik44bMDI/ADjy51Z7Jz8AbrIuhUUgPwBMnaVNHD8/AMZ3M5F5Nz8AvPShIsYrPwAiVeF0yyo/AL48Pp1QNT8A7R4JA4YzPwDoh3VSdDU/gGU2IcfsKT8AZRr7Rvo2PwD8SVgTDyQ/AEK8N52RLj8AlNnXUTEDPwC1a80x7Sw/AAwVDGj1ED8A2iLxhxofPwB4YbthrAE/4NXjXaEGMD+Q8ruV9kgkPxCR6IWoGC4/ACtIhsNiIT8AiUSJ2cgpPwDw0CkG1iY/APRx6WL5DT8AkCDnyI0cPwAYLrmjHBM/AESZL1gCFD8A8I6hA1D/PgAgwI3fLPM+gFb6+GSZMD8AliCXdFYlP4Bn6g4EEyc/ALC7mrKJGT8AVnE+CxwsPwDsSL7tzQ0/AC8mxHYJKT8A3KZfoWgAPwDqieqMyzM/AHgfaXaXGj+Amzcu1SoyPwDtFMHWXhQ/AN7O9/2iKT8ADoICgssfPwDMqQalkhs/AGDt+d64DT8AULwrINYSPwCdBmyaYxg/AGRXmzJWBD8AEKnaUXgOP4CZC+gvdjM/gF3msviJND8A1onInV0jPwAtgizJSyY/ACeONqO6Rj8Anav6iRc7P4B98eT4vjQ/AG7UK7MQKT8AM66rue1HPwBkc89yUzI/APi5eGg7Mz8AynLMG/cUPwDYBHhiqTo/AMTDI796HT8AssW6NBUlPwBq5N9WuwU/AP4HZYf+NT8AQ+RzzkMhPwBGC2aIgSg/AKrRTvlqFj8A3NQmq/oKPwAcBWYxIAE/APgd8NiB+D4AuOxcmhsFP4BFbnQg9xc/QOn/ueU0FT+ekdlDJn4QPwCEzjkqegc/AFt37lkbFT8AuK33cmbwPgDwYi2fPPY+ANwJI6+nGD8AwJfJly3rPgDIZHLQhRU/AIBoMkqJBD8AKG8TXA4XPwCyEK3Amh0/AGWV59VxGj8As7Osi2YZPwB2ttTPQhM/ACA3OPp2Lj8AeIMkEY8JPwA0rq6aNAw/AOCtoHLU8j4A/lzINAkoPwDAZswovyI/AKAKjHm40z4AvSARvdgUPwAYDTF4qC0/AKizbxRwGz8A1IBA/sMOPwDW0IfAghU/AMywyGrbEz8A7IBVeOnqPgCoRkha9P0+AGDIaN054T6Aj6+MpbE4PwA8jes6GRg/ANFb+J3BLT8AMFV1TUnvPoBOs0nrOEU/oKH/KnbCNz+AhUrvO4kwPwBYh8qnYRU/gAkhIIMwQz9gKpfpzTYzPwCJwEtgICc/AAA/fXvBHz8ANHaleT45PwDbyIwkFzE/AJiqEVthBD8AMOhwc2gVPwBGM5T5KCA/AGCiKJ1JGz8AMMnKoVoCPwCIsGgFiuc+AMoD34cqKz8AECmOADnkPgBdxVoi+RY/ADC3ACNdAD8A1L+ZTigkP8Chk7C+yBk/ABib0p2N9T4AOuHYmgogPwA4xJj4hhk/AELWGYPSIT8AsGMjNXvmPgB8Xl1SNCA/ALACYTU0CT8ACKP2gYwSP4AxLJxvpBU/AKYHfr53KT/gQQa8hkUhPwAaBi0eMxk/ACqacH/XID8AphGQgPgpPwBJqD0GeyA/AGgvCE3nHD8A/EsJzc8nPwD8CT82HSQ/AGj+l/zbBz8AtHlPJ/ofPwBeEgD+wyA/ALCyPFMQFj8AhBVlIewWPwDggSNr7Pw+AIAjuGZt/D4A6LW9UOMVPwBgqepikvM+ACB6AX5nFj8ACHzAnZ0RPwCg/IjiAwk/AB5i7NH8HD8AweMmkggXPwCoX+JeReM+ANByAjNo9T4A8EzXbJ0bPwBQcVreZgA/ABhXQi2KBT+An4+zvw4SPwD8k7kcKCM/ALT1Ck7MHz8A1Dpd6EENPwCUsNQzbPg+AOhfJpe2Jz8A/Edx76cBPwDYhFl9k/w+AJWymg29FD8AKEHE/RkZPwDaar1XiBA/ABgdWP3v9j4AqGOi5ggdPwBxvqZ2UB0/AHThbCezGz8AwA3Q/5TFPgBO01b/khA/ALBShRdY/D4AkvNzdzwfPwB6GFD8kR0/ACqNhmy8JD8AOD32YhYmPwBkMUFbChQ/ALTw7Re+LT8AXHJlT8AlP4AU1vbuDCc/gKekObxbKz+A1sVxR8MQPwD0GxBo1RE/AC4nmSMnLD8AmAFpTuYOPwDA/E1ih9E+AAe80hoKBD8AAhkjpHUvPwDABF0m9vU+AFCowx7u6T4AtH3w+isRPwAaIa5G6yA/AIzLxvpxDT8A0MHQc5HfPgCEF8aGdRM/AMTsO/3PED8AJFohi3QAPwCc7fR1rQY/AKIBu+VsBT8ADMDkOVQUP8B5lt+8v/Q+gAO4x1tFIT8AyKiIrDEdPwCAdn4hxcE+ABg/pxV3BD8AM3oPmAclPwBX6Nz73xk/AGite43/HT8AUx/AI/QpP4CMk7fEuCM/ADKzblr4MD8AkLZh9QUMPwCg/ajGIiU/AOwf108eGT8AKTTTinkfPwB4Clsl6B0/AJi0NqftDz8AmEg9kZv5PgAp0CWwjBs/AJQnlG5GHD8A7vQ++K0QPwCEwO2eEwM/AG2UVgDQEj8AHvToCt4ePwABnm3KZhE/AAHwNKZALT8A0ilvducrPwBe361TUgk/ABYyMDcuAz+AoUfNBRAoPwBGAsT+Vhs/gCvZcIX6Iz8Ay4mz0AIpPwDbd04gYio/AHwa58vBKD8AduuFSbkpPwD5GAkNGjU/AKDxk2AlLD8AMDH9z2MxP4AfuRakGiI/wG+Ew6Y7LD8gQvZzt+chPwBcezbjCyQ/AOZ3ak1CMj8AcPQZE9n7PgC43S2ijPI+AICCxwFPqD4A3DxgOYkXPwDYrk3IixA/AAg7rzPz+j4A1jp5aFcDPwA8dA+imxY/AKB6OZoSGD8A6EWkWzz0PgAw0hpByeo+AEAqf5XB2z4AWqRT62EXPwAMZSnqkxY/gLhOiMVoJj8AQKuNArPoPgB379uStCM/ADXcEhm5Aj8AGO6YZqUlPwCMdAqTJSM/AABoKYs00j4AYF2bKMLiPgBGOX98zxc/APRCTaxJMD8AyP4FfM0SPwCwRURQgAs/AIyfm4oQ/j4AoF7C1Lv6PgCwjyZOdgA/AJT91yN5FD8AZ2e7VxgQPwAQEot44vs+AEitYZwOAD8Aeutdnx0WPwBg/X54Qgk/ALQURzRHAT8AwDUYrgIUPwBijXqjKxA/AIbfJ5zUKD9A9zUtL4glP2CWp2L/JDM/IGBSzlB/KT8AUz2DiNkuP0Avce239zY/ADdLwpeXOT+AOwttOzw0PwCRZTlcozQ/AIoIqsW8PT8A2z0uhiE9PwCrqFmbsDY/ANwQ8Z52Lj8Aq/6L4BU1P8BhplY8qTE/YDq6QgxVLz8AiDsy2tQjPwAAHaj3kQo/AHrou7gpGT8AxHTUiXQdPwDw3fA4Vv8+AGqsjPZQIj8Atm5OzsoTPwAow8Px0/I+gFBoe4oQJz8AfuM5DlQnPwAAY49jXac+gFJ0UybiIz8AGJF1UdYNPwCbGkAdYyA/AOZkvWEcAj8AWMGmF/H4PgCE6rep6xU/AFR+VyhYGT8AgKFHzQXgPgCubvXWvg8/AEQofOrkHT8AYF5BeIkGPwBIK858oRM/gEZl0pbMKj8AiItwpokfPwCuzq1dOTA/AGBABNKBJz8AjG8VB7MUPwD//7k/ygg/ACgvr/TlLz8AzImLJmcVPwA0PZFBhBQ/ALLdhFZJBz8AzaaUE4I2PwAkKMANNgI/ADw4sYJsID8AoAjUX3ICP4D6NfGvHjA/AAK6cbWABz8ATJK/KUASPwBwHZBlcAE/YJT+prvTMD+grXJSgD8nP+BdFYZkohc/AOqBRqUcFD+APhLdFsknPwCwhrou8iE/ALBC2CZo+z4AAFBRVsDmPgBOQkldYCo/ABDvFrI7Ij8AKBfLO1YDPwDwpXUuUAg/wOmSaoEdND+AfUqQL7olP+CWa6zgdiE/ABC9IbYU8z4A14tYDQgxPwDgEUftO9k+AHbPPTTuGT8AJBw4c2YOPwBy3Iy70CU/AMCtTttN3D4AmBPCgcsBPwCOer+rJRw/AOSv5VdIGT8AYCo2oQ/yPgCIgq3SSAw/AHQoQWpoFj8A2NQMFJ8APwB+eJtj/Qk/AKMgb3eFEz8A8GSVZEsQPwA482VP3PU+AHwpPm7sDj8A3saDa9cRPwCQC4LDIQs/AHa18WIaNT/AkxFhHEI1P2BDYfDMZTI/APW45poMMz8AqAIlCQAyPwDg+zTlTTg/gP8n6LBxMD8ABvygbcUsPwAgnxrUSBY/AHDe0aBE/D4A4AuEFDEFPwC3VypPkxM/AMyOZDLEHT8A+Ajih9IbPwB6cIxF+RE/AJyYffPUAj8AGF5iBxUQPwBA6Yn3AeQ+AGJuqpH9Cz8AoG8jIVMDPwAYJ6eJnAM/gEwAoBDEDD8Abchad8QZPwBUsL8FHBU/ABDDrneuFj8AABAqE1nfPgD4ybJ3jhc/AJ+FTxPSHD8AmI6Me2oYPwCHyfRJ0CE/gCE8JiZk/z4AHO1X5TolPwD5NtPRhAc/AE64EqEGFT8AqJdkydAXPwCIT5MoAhE/ACBjyN6fHT8A/tAPyQ8gPwCobp51Nyk/AHAgHSzUFT8A2BS6HEQjPwAcn+S81xU/AFQTsh18Lj8AyAMqEmEiPwCoL6wvHwE/APgYdeFmEj8AUCjHIeYWPwDQaq+XfQU/ACDEWyf7Fz8AsMQNMpMlPwDA4cyMBBg/AEhwwGXwKz8AxdAN0pUpPwAoJ8/gAuk+AN76gIJGGz8ACFC9hKIHPwCzEZF4hTI/ALxorOygFz8ASMcLgiT3PgCIqV1Lj+U+ALLAjz78Jz8AMGUIp90FPwAAiY4wo+c+AC73BOC+Bz8A+AsYmnkLPwAiwj6Z7RY/AEA2hOqkzz4AwOojjPj9PgAvqI0AHSQ/ABC4dR5UHj8AIqD24OsGPwBA3zNlZe0+AATKdVotHT8AnFhBNkgfPwBo6EHKPO0+ADKcV1AJJT8AmCSiMxsYP4AaQFQY+SM/AGqGAxXkED8Asg/tMw0jPwBQO1PD9SA/AL7s6rALMT8AHtLyPJgjPwAITlW/kx0/AGjpJDdlGz9AFbdQHf8DPwCjKb8W9Ps+AHzhvr45Aj8A6jcHMdwxPwBAMxq4BiY/AJjPiMWEBj8AgLkdGcAfPwB+S3r8ZzE/ADZChs32Ij8AQAktzZPvPgDQkf+4Mv0+AGSkaLi8MT8AGhB8qt8VPwDO4wX1vCI/AAAFPD3VuD4A2QzLql48PwAaqg9WJyM/AP9V5SSqKT8ApMDF+9cEPwB4xpuqwyw/oERHTPwS8j4A0PTsBpLmPgDIyH2/tAs/AH690Xb9Kz8AAvqfsgIQPwCq+lEXMBE/AOXM6C+gEz8AAPrrCobPPgCY4G/XWfo+AFyH5D69Dz8AgEbVcff7PgAYeBNNsCQ/AEghV0ZZIz8AmEcWTfcPPwCAUXgCur4+ABDmozIyJj8AuPfJx5cOPwBDqMok3iM/AMCbCm5j3j4AIgt5Cy0uPwBAFHtGfuA+AE6PHUNMFT8AjFVkwRcAPwC8notddiM/ALTicoI2+D4AWNj5TRT8PgD83db7jxg/AATZF4eO+j4AclaJaNcGPwCsZZd3Rfw+AMiLFInB8z4AgEsvGJy2PgDUco/+lQo/AMiNWW41Cj8AIBZ1mlsKPwBcFmnRyhU/AIQjxsziEz8AwIMMf23QPgAA9++xbuQ+AOmij3DgHz8AwH+a+tGjPiAEWdcMoA8/AKDru2eK/z4Ar1pENFo7PwAg4SE1JyY/ALz+6U7tKj8A5+cXbpwWPwBdNRYDXjY/AICSA7XmJD8Ao6/Cae0qPwD2M52/3Rc/APDM4XzlNz8AwHldlM8sPwAgLRoOcCA/gAETRuHZIj8AJ2yhyv0xPwD5u/oX2yU/AF7uT4CzFj8A2pz0lKYNPwADeMJTITM/wAXv+oRbKj+ApP4mv4MTPwCAoE/mt+U+AHgX9ePLEj8AADzDAqyZPgAuo2lqiRQ/gE8OAby6GT8AlK63+JM1PwAgSjd9IwU/AIipVusJKD8A5INHsxQPPwCsxRhJVyc/AAS2AYL0FD8A5IfTZrYXPwAwDr2Kqfo+AGCM7uq/Fj8AGkj+FGsaPwC43REid/w+APSsN/SYAD8AoKgVHsrePgAiJ8iAfRs/ALDX8NqU4j4AhCtW38MRPwC8GU/3j/w+ADVDYtqZAz9Aa3vYe5QDPwCQFBY6DO8+ADz80W5gBD8AQLFqvu7fPgBA2f1XiN4+AIBHrG8kyT4AQHMl1e0aPwCwBpv+GS0/ALI2YOInID8A6L04QzQbPwDy3LYXcQw/AMH4i5d0CD9A47vlnbUZPwAaWQgH2xE/ABRIfwlIED8AAOZYTmb7PgC49MnMYf8+AHx+0XDaCD8AAF7INAmoPgA6TEalux4/AFAnXkkV4T4AzYl9ZlwaPwD4Txs/TwY/AFSHridMDz8AfuhkBG0EPwCUWfxLCgg/ANI7fR+WJz8AHH9bfqEUPwCtlKj4SyI/ABCToJ/uCT8A7pVVCY4cPwA8Ia5NSxY/gLKGdgRBKD8A+L9/a/fwPgDMRg0trSQ/AFjNSEkc9z4AtN/JO70NPwBAJPIf/fA+ACwQ4Xg8KT8AaOMdNKktPwBQLizmrgg/AN+vSswPCT8AQBshjLEiPwBGPo7y0i4/AKjXfabCBz8AEtPOnHASPwBME7JwsRw/ABSqWErZDD8AGluyYBYIPwDw72MDl+w+AJCnv8ikDj8AK9pMkp0kPwAU01tongc/AExyIct5GD+AZq5lWq0ZP0C3LUTEpSo/AB0fuWJ54z4AJMclZVUaP4AV+flpmzE/AAdWRDF5ND8As1eIY9UlPwDU8sG4eSo/AOUPMHTxPj8Abll1jj80PwCmDlRZjjM/ALDUR0HmKT8AJ/h0a0o5P0Auqkx66DI/gGh2x28MLz8AD0cwGwgjPwAcvp+udRE/AFyqMkSCET8AsMQUkhgjPwCQuDWPYeM+AKZK220bJD8AuKR9k9cGPwCukqVUzxk/AJiZm5Sk/z4ADDWAhpspPwDQFg3CwvQ+ANcMMWroIj8A42kXYlYQPwDInyPtMjw/wKKL6kFBLT+ARomVnugvPwDcMTwAvxc/gDZmQ8BPMT8AFqAXfjcrPwCYfg5CZgo/AHgxM/WUHD8AUeop39AyPwCZV9hddxk/QKwfwiHOFT8AqPIYuQsIPwBrFwKsSTU/wG/9DDz9Nj8AB9jdZaknPwCGUaL2BCc/AMjg6cymKz8AaNdauGcJPwCsnQ24SiM/ADCq+o8s/j4AF4Frl2YxPwD4rd1DYAQ/ANTcgKZqGD8AhNDxQ8D4PgAwg8sLwyo/AETzosbSEz8Arm4fF98QPwDokx7kJPE+AHz6hTcnGz+Aos9ZaC73PgAORny4AAY/AIBjjRJO/T4AOs4Iz0ksPwDkDp6mwi4/AEioKObqJz8AuaWdd/YSPwCQorD/awk/AHDsA5VPBj+g2wGovFYTPwCA/OacsAc/YAihG4irLT8Agwkhsc0sPwAY1ooawBk/ABa0oHYALD8AoHsORzAbPwCIP17jrxw/ALD78AZy9z4A4KT+6KklP8AluYIlMlE/AOD5+PXjTD8AmC0iKG1IPwA2ZQVzYUM/gIcfj+RDRD8AAjRZQvc/PwBMkOQvXz0/ABKrmhYGND8AHFVv3vA0PwCkY4j8dyQ/AICKm0yhKj8AGMjE+gEdPwAogs4HPxI/AGjvaARN9T4ANOZT5VoEPwCA8A5UFMk+AN+LlDk8OD8AVksvvgYjPwDPEucg4DA/gBb8XoZjEj8AaqubyR05PwDsfbOR9R0/AJmtEQNiJT8A4KyvRaHJPgAOyfJEljA/ANTDF0K/ET8A1E7tQJAbP4DgqIM8xiA/APx7sjDIJD8AtPUKTswfPwDn3PqUVxQ/AHCxiI8T8D4AsFRL8ajYPgC1um5RrxE/AJej+OBbBz8A0Dn7b/YUPwBoLWyztvE+AJDVolJM3z4AYES4L7/jPgAU9aMgoBc/ANBRP9NM8T4A2P2H3QEYPwDAO1UbZQA/AFQ2KM3cIz8AjllMoEEfP4DGzYdrt/I+wPRQ1gHcID8AoPaI7Jf6PgBkG+soayk/AAg4+hUpAT8AUCax810DPwByF1pYk/I+AInMRZHKMD8A9A8QImghPwBu+sm0DRM/ALBpzHYqAD8ABPAM/KQpPwCf5qWRASs/AOw3yRvuDj8AKfg+AaQaPwB9iNVyUD4/AHQ5bKbuMz8A39a9eiomPwATK2Cx2h8/AMniojGvQz85zZt3Oj00P+BO/uHhujQ/ANy5fdimHz8Ar1pLlN84PwArHqeY+iU/APLR3WjdIz8AYvdk63oAP4AUo8DEsFU/wPRZGOE/Tj/gKaxAE5hJP0BHsH+t3kI/AADgWkG0YD+AIHEGqvtWP0Co+OM8XlA/YBWOYh/KQT8Akjw3iaBAPwByUKtaeTM/ABCfyI/3LT9AMG2fGUQhP4DU3YwI8UA/AGH+K3vEOD+ATqU9C0UxP4Bq7GGpkSg/ADQHuTA0Jj8AjANXJQgdPwDQzMxV9fk+AOrGdViXKD8AwJIfQ7wFPwBZRcqmCCM/AGDkNgt4uj4AwDho/W/aPgAAZ7xq2ps+AJSBHP2mBD8AyHZKD44CPwC4NF0+qxc/AIz9eWJsID8AsLxvX6cMPwCQPQTeq/0+AIAAZZBHBT/Az5jPPoYwP4AavoqO5Rg/sOdFzAXYJz8A8Dk4QYIWPwBGDSxi0jQ/AHWap+/KMj8A5FLQAoQsP4A8fobY4yY/AMzWgFZpFj8ASKXRiS0SPwDwVI98T+s+ALAiBvR/4D4A9rsiwnYpPwCELRVZjxA/APLyBeqKGT8AwD+pztvsPsDofVdmbVc/wMhASL4dUz+AU21HISVLP4CuEtL570I/AF6u1a04QD/gY4yJdvg2P8C1tFN5JTI/AJyZS0BtGD+A1OsO/v1TP4CjqN9Zjkw/AASpOQSyRT+Aw2/Z6D44PwCVrhRv3lI/AKXGowk2SD8A/AzM/IA4P4DIWTrC7TM/AJA82HzRBT8A7EqLJ6QXPwDAl/+unvs+AMjW84o74T4A8df/oOwwPwDchrOClxs/AOlP12fTKj8AioVPuTwZPwAk9Tf5HSw/ALzV+1C4KD8AEPQz6EkUPwB0zfhOehM/wH2XXbUgMz9QT+6n0mcwPwhU4JdzNSM/AMcZayRwJD8ADC3BaZkqPwAbccRoBCk/AIIS2FqIEj8AsCPiACMRPwAgKOFXTCg/AKztUuSEHD8AENnoKTj7PgBI0xmInBI/QJDLkJDLPj9g4bOF4LczP8AVNYeT6zg/AL4r4wvnLz8Avwtojbs5PwAsvRqwJCk/ABm91iveKz8AxGtoavAePwDgPdfRZDg/AIYSZSa2Jz8A0am9sOAhPwCJTuYlgCE/AHDjl3XL9z4AYG+8W3zZPgAoGoBZIA0/AOD+/nXd+D4AQMPRZQnFPgAqzZ9JrhQ/AKAIv+VM1j4AdMCBcDESPwAdFk1D9SQ/ABx7IbW7ID8AtBdvhuMVPwAEQdeAxQ4/AAyg4WbGKj8AD7RrqlEyPwCbgKURliU/AM4qt1fXKT8A4M/MqsAcPwDQ3dZHZSE/APCv0noHAD8APG8aFikYPwCoMsbBsCo/AHCm+NQxIT8ADnqjD5AQPwAwBxdFdug+AChv8hH4ID8ASr467tgYPwD2o2ZgTQI/ALzFs4FaGT8AtBr8TEcaPwC5wn1vsxk/ACAoCQLo+z4AgEpFpYP+PoCsow1bgSM/AE79IAprAz9wq4f0T9cUPwDUrwb1kw0/gDDdU5ojIz8AIHsaAgHlPgAkRMT4Txg/AIAje5Xh+j4A6M8X4sElPwDQIkPFC/I+APgLi85LBj8AMDNqDD4NP4C8Ie3JezU/gJA4A9V9Iz8ApB24cAEePwD8iFgZMhc/AFr9/iFdOD8AihgAVSUoPwCyHazzRRI/AHNFZYzWJj8AHv8oxL00PwB+ByD2CjA/AGTRe19HHz8AXeXjYC8YPwAytu6rEzE/ANAWVrZ0Dj8AEpZQRu0RPwBmz8NFAQ4/AID6PEN18T5AVNVzOmsjPwCnId+v2xY/ABR3X+qoJz8AP2TH0j0nPwDIWF1qiAw/APS9HgZuBD8AdKXYnd0GPwD+PvcPGTg/wNvKABSsOD9wAyG0ARk0PwCwI57WcSc/AHDa5yuWPz+Ao5wBqfk4P8ACxlbGmjg/AMSsZf/MMz8AQHrnmjbzPgBUOkFMrBE/AHQAGllGHD8A3kaoZbAWPwDIB0+7ti0/ABCjsgSm+j4A/AiZkyACPwAc65EL6vg+AIJp2OyFNj8AhGZesC0NPwBo1bBeLCM/AGjoXUpS8z4ArPzFBsUoPwBrhQuIK+o+AOCa1waZ7D4AGFMprroHPwBqAN3hTy4/ALAEfsNBCj8AUEz9Cp/4PgAcscNbZQA/AMrWvSCVIj8A0EyM2wYPPwCw33KHAOk+ADBXIZA+4T4ANPkv26ElPwC4zPSl+yk/AJ5MZNB1Ej8AxBiwYeP5PgBG2DzTbTs/ADRcX70tKz8A3O0QBIMXPwAcXvYyyPI+ADj9cAQNFz8A5kHIppgiPwDI9KgvFhs/AH47K82EJD9A8zc9SE0yP4A9okorwjI/ALmqs01/Jz8A7HmVSCUwPwBK07tzWjA/AHEfNKroMD8A16kGUl0tPwBtwzbhMCo/APpLHu1fID+Aff27Sc4aP4A8tu5Y3iI/AMg9NYwRBz+cOzazqPBXP8Bi4rMQFlE/gC5WLS+PRT8AkQ77tLc9P4BeM2ypIiA/AIA4ieaQ9z4ABML6Z9wHPwBA+4mNcAY/AHC7wqGa8D4AAEUfXesLPwAYGU0+KxQ/APCKP5DOFz8AoEVLVnAFPwCQxQMiZwk/ALiwulA7BT8AGNvWWu8cPwAgqegRg+k+AJyP6Sm1ID8A9PYJScoePwBYLlQ9FR4/AGir5lNUED8AuNBDj3EWPwD8hvBTIx0/AECJbUeCGj8A+nvomm4jPwA4cHC3IyE/AJDjs/xAIz8AvAJAmOgkPwDgd+lYZdw+AIAB0BO9uD4AeKwnL1T0PgDw0u/fJhM/AABZZsGHAD8AVwemrYgQPwAYd8trwAY/AFzsiaZiGj+Aa1AW5AMaPwCqoo9iIPU+AAAkHyd6ZT4AABdy6rQLPwDYaEBy6Q0/AABg/kaw5z4As9vF3H0YPwAgPFBmhBA/ANjZlcvE8T4ANM1w7Ff1PgBo0UAroAA/APAA2slTET8UNTMTq7Y2P4Ap8lN+/TI/AFv7TWicND8AcBJebJsmPwAhsVeHGCM/AHKziZ0LKz8AUnKbDJwiPwDQ/HW57SY/ADz0Ovz5KD8AHXhr+fkxPwBc2GUiYRk/ACwFtt/sKz8A/IF1CdMoPwAwUI6B4Ss/AEqR9qaoLD8AoJx68o4qPwAe0RYw9SI/AB7sME5hLT8AQF9fEvktPwB60aX5/CM/AFYqeIhxPD8AJ8z4W+87PwDVw9p3kzU/gAJYPyquKD8AsBHN9+43PwAWOqUUvDE/AI5Cc6tAJj8AiMZEo9EZPwDAhkD0Lw0/AFCBAG/REz8AAG4wO7sBPwBcQ9XClhU/ACBlX67POD8AQGbRMNXgPgBQDqOneBc/AEARsWK54T4A/IQJMLwqPwB4gO7+5wk/AKAagl5a/j4AxjIDkzwcPwAVoewqVf4+AHBDu99l4j4AIimjel4QPwCcJ+YFzRI/AEjTn/NECj8AMNbv6Bz9PgAoT3xdzQo/AKhgKkA1ET8ApElIVSodPwCgA2U/qPs+AGwaWlPJET8ASBghkXsTPwCCJlrzyyU/AACQcUKYmj4AsOasnlH6PgDIfA3omAY/gI7bhN/oSz8A0WAq9F84PwB8ypn0Pzs/AHW16gKVJz8AvGaE0gROPwBw0kqkbD0/AE8wQ1/5Mj8Atn6WnSgjPwDcU1Rj3T8/AKBGLsOYIz8AkGiXGOYXPwAJMKd6PhE/AGijINfMIz8AAPnPIhvrPgAgXHQw8wE/AIjpb8j79z4AdP6Y9GgwPwBOSsRPsS8/AHEl9jcxLj8AjVi7HdUlP0Bo/cJPvhQ/ADhqOqpGAj8AQKNUSvnrPgA4qRIGzgE/AHi0FVZ3BD8A3Bi+e4MYPwCYiiOoYwM/ACD2huddCT8AIC/lXoz+PgAsMPeCoB8/AFBxJHTAIT8AsIyLxwchPwDmbRRhEBg/AGYpkP4SID8AnKSEmccgPwAgDe/jewE/AFSKXs5KFz8A8uqDl7QmPwC01bd5PC0/gFvSIVV5Hz8AoO742bYcPwAg1tyxRiA/AO6+nFEEIT8AT+itRPQWPwCYpaI6ly0/AAC7VCKp9T4AAJEXL9TQPgCA75dho6Q+AFhfj8FxOT8A+KlmA4QiPwDoIv/07ys/AIjFWtYj/j4AnDZSyIcxPwDsyKez/xM/AACHLcsZ+z4ACI2LL10PP8DRvGqcBiI/AGgSKFUqBj8AsB7ftKX3PgBQqa2RBvI+AAAVH0U2Cj8AIESHgVkKPwAASKCviY4+AAAFN9TJ0T4AcND/qTUQPwAQsL20DPs+AMCZvs7U5j4AQG1UPNj7PgDcPS6GIR0/AMhE8PHUET8A8CtY5P3yPgBATYIJ8OA+APgZ3rk3GD8AgBLKmn0HPwCwgcwCBRE/ACTAjd8s0z4AWFv9+AwqPwCgVqWcF/Q+ALAMsMHgFT8AWMUyy5LxPgB8zgMTCSc/AFAr6BP9HT8A+Be6y6QJPwBEDGPfOv8+AAD4D0NYMj8AUHbHFXcrPwBAQ+HlvA0/gO4RJlDwJD8AkgIyJLMyPwAgyvZVaig/AFglaRJuFT8AMoqN0QvzPgDgOYGIaS4/ADxKlev6Gj8AzNS6fBgaPwD4poDy3vs+ACDTi2pMMT8AqDZSdVITPwBLQn/HBik/AEDY2PydED8AZKAnj1EwP+BVUAFq3h0/AL1ecoCEIz8AgHHNeo7JPlQI79k6RSQ/wA/N4YNFHT8AmSwkhvEKPwBoD38OsRs/AOZNdgJKHj8AS5OGFlMaPwD8caBuR/Q+APgUknkIBT8AG6hXPOEhPwBObcBqugw/APoDpAeuEz8ABCDlab4XPwACsQWW/Ag/ABidmHfeIT8ACC3wc7obPwBsBsr6eiM/AMwx3pHnIT8A1H0fDK0XPwBkPF40TxY/AAC3i/cQHz8AzNZyll4bPwBQfB7Anw4/AOjIyFBLCD8AgNz/STgkPwD0EWonbBA/AHBe3APCBj8AYGh0hCANPwBAg7/onPI+APjBKcUyBz8AeCjc9aAGPwAgwFfIu/I+AMAojK8pCj8AxEROBhcUPwAAe5lZ+Rc/AIifczOqCD8AZ1B0+EUMPwAs3ZyO1Sw/AEDPUF0EHD8AqgEuxwkiPwBYAxr65gc/AFpstQDBPD8AXKheqiYqPwB0KSs3FjI/AKcs7sgVHj8A/PxhmEo8PwCQ76WCoyg/AKSzzSiyLT8AxJOPexIZPwCAlMmONyE/APA4ukghCD8AiBF0+YL+PgDEPLeTsAg/APy8Y/Cr+z4AYKMZynwEPwDorhCrKgY/ACgsbbi4Ej8AgHWPRaHCPgCkxp30chE/AHRqICGrEj8AIJp1Qnj7PgCYa28WSyU/ALAafUEkED8AyBPqjFwOPwCAGu7YEQg/AIaYb9k0JD8ATJRw4wAWPwDmh7IcoCE/AAAmgJHNwj4AdeFKkto+PwBXKq892jE/AKuWe0flMj/QcyQAQf0kPwAuHYABIT4/AA7C+hSnOT8AAM19AKswPwDX6F+iwSI/AKYVMAloND8A/Hur0EI3PwAigh5cdik/AOhZXF4mJj8AABMX17ghPwAsBBeddSc/AAByi6hM7z4ALFeuW2wWPwBA5r8TPQU/AGBA44drET8AwCKF/6LaPgB4Lx3HDPk+wD3N4830IT8AgJe7I/jYPgBwEBmHJwA/AODyeMTH4D4A8CdGGOkgPwBgOSgiqBY/ABBSECohGT8AAFgJGp3tPgAIT0bsxhY/AKCrZ1bxED8AAN9NlGu5PgCQIJAU0Qc/ANzNeQVCGz8AUO5+3QkWPwBg178sL+k+AEAPSUMVBD8AsiwK9vUlPwDK6Hnfhyk/AHbGI/8lID/AH8anbPQbPwACOOX1mDg/ACyWKPbmIT8A0qlyJqoqPwDds7OfFh4/AAhJbvMrOj8ArBZWAkonPwALwmDUMDA/ACWceO1UGT8AAOjhVivGPgCAmCSiMxs/AACSldtf9j4AwtYuv+IPPwDW13EwZzE/ALuqkgNpIT+AD/zkREEoPwBAxeftJsw+QPphWJ/pJD8ArOhRLowgPwD+VE1CuCI/AOjxAYaBEz8Acm13fWgoPwB4/gMrvhg/AHyIJ10MIz8AsNE52dodPwDMkuIlWxs/AOAv1DqwHT8AwPOda4cHPwAA1ylW1wg/AKa8VchLEj8AAGIB8ozFPgDA7B99ugo/AADV8pjDyz4A3MQpZtMSPwDwpoGQ1jA/AJyYhFNaED8AJH+KNY0HPwBE/6lsxSE/ALiEFUyCHT8A2NxY/M4kPwBA/iTICb0+ALjoDbGlKD8AWGcZv48gPwCmS7d6vhQ/QEtwrdvkID8AVksb6TgxPwAMtyFmEyE/AIggsbEcHD8As14ZiXgPPwAiwhWkjzw/AMg1rNm1Jj8Ajn/qoT4pP4CnAVZxpRU/APwaVVITOT8AHpl4RVQoP4A8BjwwYDA/ACx3N5qiBz8AUjzrpec3PwCy+cxrFCc/AAHpWPZmLD8AHmS5C9MWPwD+R04PDS4/v4+52zFeJT8AWAemrYjgPgBYjMaTWSE/cIxbZGROMD9Aw/2O4/EhP4DjdDubIBA/AFLEtNIxIz+AclCXhasxP4DLA8WdmSI/AIAVypWz1j4AaHkSokMHP4Ch3EN7STM/AAADJrwXJz8AfhuGDjktPwCgA2U/qBs/oGLsQLKwID8AQOpepB/HPgBAZWYVtQs/ALC0+8zbFD8ALCK5t0QWPwCgJ16xav8+AJyqR3LSFD8AABEUhnHHPgDQI82Uvf8+AIAJhh411z4AMCbZlpkBPwBA3JEk3Ow+AOD4yGgA+j4AAAA74spCPgAQADQutwQ/APDooyYIAD8AIB7+TLf6PgBgO+5VjuY+ACRjhkpzET8AkNXfb60JPwA+nS31szA/AMBjApgvAj8Azh7MMfohPwDY/XK9cf8+AOiAoAIgIj8AYDUdvXj3PgCgNdR88RQ/ADxtYvziBj8AVL22jX81PwDM/3wUqSM/AJzE0A3SJT8A1PVq//IUPwDgH0rXJSI/ACQ6TcIHKD8AaIebSgsWPwAHRF99KCM/AOgO2x25HD8APojcxBUhPwAAaLxq2ns+AGAzIcy26j4AGNdYZ1jfPgAAoi2e/8M+AKjQgKzSAD8A8J+W1S8OPwAg1seRtgc/AABXePglzT4ApLBVgt4RPwAASCRZ18M+AKbUESp1KT8AIIohoykSPwDqySX/lSA/AID9GQR72T4Agpb5UxsvPwBigM1aPCA/AOBnr5xHFj8AwD/RcRfrPgB4uL8fs0A/AAcTv9c5Nj8AyhWwZq0qP9CoSpdDaio/ANh/Q6cKOD8A4hTV/mE0PwBIWO/jNiw/gFUYp6nuLD8A/AKZ8OkxPwDM3BQlUyk/AFjP+QLdKj8AJh4FrTwYPwAgqUaAWg8/ACD2fy1DCD8AdCP1KTkjPwAs7FPbxgI/ABCgirIJBj8AGNksAbQmPwBA03ec3vQ+AIl5mv0FID8A28bo354BPwBQfIrugQ8/ABCFVBSIBT8AtCeISyAUPwAAShtJ4/c+ABiLwDjWBD8AdCL2PPsePwAAmtq2P+s+AERFeGK3Gj8AWKF77fwZPwCol8LdEgo/ABCmF3SjCT8AgIKmGC77PgBQMxoSnPk+AHhpNlT9Bj8ACIJ8FiMYPwBc0j3VjiU/ALKr5g7fLD8AQL1CB4vePgBqxEhYesk+AACs4rE1rD4A+Mrz+PgXPwDgwXm/1Ao/APQji1om9z4AngVhg582PwAQZTeqniE/AFo45/NyJD8Addqs9+4gPwBACfe1Iv8+AFCNSanwDT8AQLEhyjzmPgD0WHmeyAk/ABCcY3MvDT8AlFrR+CcbPwDA8fMRTAE/AJBQnuyQ5D5AFoFxrCkoPwAEL1iM/iM/ADBy1jnjGz8AxKxdVIUfPwDwktYCNQM/APTWCF/2ED8AwOZ9QfvqPgBQJTpbghI/AKC1VuWh7z4A0OU8GiYOPwCAl121IAM/AED1U4CTBz8AOHqVA7D8PgDYz48zyg4/AAB0bQKz4T4AcB9If7YSPwC433nnhRY/ABY7lfYsJD8AwL30ESP8PoDPl9BRSBw/AOwKtnRjMT8AOEzAORMnPwDgGTUUXwk/AFWM9Z16Ij8ACNG9ix4tPwBgq5scUxc/AIjMgRA09j4AbJU5bfjwPgBwG9cAaDk/ABAZCWevKD8ALpQECFQjPwDqbqAZfBE/AA7gokQ5Pz8AoChdnqgjPwA+uQGL6i4/AFifc39/ET8Ahv7p7fcxPwBEobGq2BY/AHIb2EsqED8A9V5KN94YPwBm157iGCM/AOB3s0H0Gz8AuHuwjIMMPwAA/DtMM+s+AEAZwCYo9j4A53Wkc/EFPwAqFwFTxxM/AAA0Rr9Lyj7gNfkA0YAUPwBAz9FR4bE+gEUxC+oDED8A8EavH8sHPwCd/52qlCI/AOAKr2cTAj8ASAK9ntENPwBgFX8EHRo/APprmWuLJj8AbJKCZnT7PgCA7hkdbb0+APDsQhGAFT+A6NNmyzcyPwB4YvUvXCY/AFRp3QJcHz8AMNNw4sPzPgCwH0Et8Q8/AES1T8MxED8AoMmIzxgYPwAAGocFexM/AMgDPZxsDT8AoKgOZK8NPwA2B4omEyU/AGSmINICIz8AAEXPrh7hPgAgf7KM8ww/ADDmsfmcFj8ABE+rYI4mPwAYu2I8SRQ/ALBj0Z30Dz8AAJgLam+lPgBQSAAM5RA/gJHnVmb3QT8AzERonXIuPwBVts1oXTA/QLXWeZxOFT8AjqU2uX8+PwCIa7H9rC8/AOBNJgFIJT8ACgeWnG4LPwDiqGgHczE/ADxUrg0BKT8AYBPH6tYIPwAAeOTvWZU+AKh1dBBOMz8AqLXCue4cPwA4GzRp8hs/AKAO/KxE9j4A7GoUZtooPwDgz8AtBeE+AHQ6GalwAz8AijsD0LMSPwDskTyKvh4/ADTxW54PLj8ABALsmjIkPwCosQKFYCE/APR2SsO4GT8AmFpshGArPwCQwMWhQiE/AJCIIFccKT8AmoQcpaclPwAwU0WIZSE/ANiLUa2CIz8AsCnGI0QrPwAbRJVBZCU/AKRPIE7FGT8APh0PEJ4iPwCQRurrHBg/AO4MP4SIIT8A4F7QSPHsPgAAcnER8fQ+AAq8X+Y32T4AeMURiNwgPwBwcEEO+Ag/APAeq+5DAT8AsYXeIU8RPwAcvI5K7i0/APDa1qbEFT8A3M2VhVcRPwDDJYy08wo/AJAJKL4dPD8AxCBONM8iPwCkCFrLGio/AAq8X+Y3CT8ADA1ogEYxPwBipwM/KyE/ABD25KEKCD8AwNeXifPKPgAxVzsnmgs/AAQNFzSCIT8AXnHGDEkhPwDcSwJm6iQ/AIDfBqXzED8A5FZHlpUcPwDMkQYZuBo/AEy4EqEGJT8AoMwxFpISPwAQKk6Nxh4/AKhgtwtjBj8A8O4+VXcgPwBA7oydFNE+ALCuvl95GD8AKAZVdZkOPwCwWiPqQyU/AMLEXisiRz8ARqkz/a45PwAh5U6JxDU/kFjGqWNuEj8AntrpdUVEPwCEk18YbzY/APgjY7CKIz8AaqPO7BAPPwAENQZFeS8/ADyx0SLQID8AKD3AntoDPwCwkZOKUMw+AACLckNu0j4AEGDdqWQDPwCgmXr3WBs/gOFPtnDyIj8API/ZvgUoPwC+IowM8yk/AOtNJq4SJz8A2MwXjfYyPwCGwWuXdCE/AICiPcQ5KT8AHpBXsAYhPwAocmNYRi8/ADTJSa19HD8AkVG3w18xPwCG5vzrKCw/AO5q+ntJMD8AADuOPBITPwBIIGYZJio/AGLwenuWJD8AFAJfKZoiPwB6WHeZjig/ANB8mrPGGz8AoGuJraYfPwDIwO2s0x0/AMa9RvzeID8AiPM9YMsOPwBgw5RIqAo/gOx91nGQET8ANqqJ8d4wPwAwgqYKbhA/AGgqGiH6Gz8A2P9UF9gBPwA0A6CxZCg/AMCFQ/Cr9D4ApEQK1QUVP4BVANYnNR0/ADCHQp/UGj8AgB8zubsNPwCwX+JeRfM+QH3pK/F/HD8AcHsV84oRPwAAEwK3KAk/AABhVOiq4D4gKzuO8DwaPwDA74Tstwk/AACe8zUPGT8AcK05+dIBPwC4INulZ+Q+AEvmRX/lTD8AZJWG4OhDPwA/hF7RfjM/gBilISo6Ej8Aqk0Y4EcxPwA4qy+U2wI/4KVh/+xSBD8ACnrSGbEhPwA+iyVhyAs/AGMTksvyMD8ArGyB5ykoPwC43oi6Ui0/4F/GklokXj9AaqKsHzhXP0CEcVQqmVI/ABrOXEgqST+AMikB6TVGPwBGq+S2bz0/AJ7ucm4ONT8AkKRvzGwmPwC8tjn/lC8/AJj2en/CLT8AuDd+MMIePwBEmZTMyRM/AILrhfaDGz8AHIYIJFoEPwCqn+Ybpwo/ABwA8LDQHD8A4OGrSU4XPwDgwDHe5Aw/AMBf6Rhg9D6gD+2GQpEkPwCot99H/C0/ACDN9v1qCT8AQPiqL4b7PgDgV3WN9CE/APj0vQPRGj8AGKvdor8YPwAgI7ZhMxs/wBMHq2nJJT8AwLh5GgjmPgCgtOasSww/ALAdl9O1GT+I6Iu922QkPwCA8fh6Vwg/APAhRnWyED8AzBIqAM8TP6CtT3LlSyA/AAA0OEsW+D4AMKAeOFL8PgCv//sYbCg/gDn2PaB2IT8AgOn20Zs0PwBAPSyB5+s+APBByFNjFD8ApC0jIPogPwA8ILCrzzg/AKDRfbBWCT8AcDNzYz0BPwCcXhLPXR4/AHgCgh5VFj8AdyB8OKMwP0ANNVF8eig/ADNuMedyMT8ATNASLeEVPwCwdVIoQAg/AC6ZBb5MHz8AAPp1cuIePwDCm3At7TQ/gB0UnIk0MT+Afk7fGDAyPwC2juXMCzA/QKCI1w81MT8APlpx5gstPwC4Xrt0Ni0/ANwiAJPnMD8AXTNCaQIvPwAg91wyczE/ALqu7RZlKz8AbP5HqKQwPwDdiFgS0iE/AARJuX1iIT8AKiEMtcImPwCIWAQShy8/AAqbMAUFJj8AzMpkkOYvPwAoMlHb2Sw/ANykoCedMT8AKEe9QMsrPwBQkTMenyo/AGbkUouNMD8AxPhVLc4pP4D4HHMr4zA/ACRm5Aa2ND8A0rgAfj02PwDmCv9oFSs/AIHj4Q7VOz/a4eySuOw6P8CDeepRPTc/AIidVqWcNz8AIBD1oD9JP4Ceyb45v0Y/AN2GI4PtQz+ANa9Sp6xAPwAaFygF1j4/AGPbFXYqQz8AqOKNCr81P4iGbqXOXDE/AKQ6pijJLz8A+DJxXgMAPwCA7Q5Z3tk+ANf198ogCj8AhiQvpbM5PwCEJI259Rs/AGhaUFAg3j4AQOpepB/nPgAYnKdKq+g+AMR4bv0gET8A0JisXuscPwDI/RuvHxc/AEbQmOu+Kz8A6CgbGDwCPwCcc340Tx0/AKUyR7aNED8AEOJGibEOPwCYyVK4pwc/AFA7e22R9D4Aag3H9GoaPwCyIdj8ICs/AIA+kJYXxz4AJhaRGnEQPwDM/aEayA4/AKZmFcPbKD8AgIfYG5fHPgC8Ny7cigc/ACyEKA1DIT8ASHUAfJkhPwBAgDCFVOs+AAi+mPRaAD8A+oy6jLMOPwDkh8wGMSo/AAEFoQsyHD8ASY6fSusGPwBgHhN7B+4+AMC4rzF51j4AJ9SqA6n2PoAewPmzeSA/ANAX/KumHj8A33SST90kPwDKeZOrQC0/AMYWDWgtIT8ABNI0dy8sPwBg3AQN2f4+AA7RdJdsIz8ASAUPMY4TPwDMCOkzLSI/AFi9RKRvAT8ATCpxeyEdPwAgx5E5ohc/ALDrLvbxHT8AKi7T50IvPwCUvM0ZVBs/ANDmH4dO7D4AtGh21S8HPwDU4q9Twhk/ANx6z7w/AT8AeLbNb70FPwDg8MyB0v4+ADx+O06tHz8ArITM/ToAPwAEXlTtdPE+AFwWVLE6DT8A0oCEKHUoP2jdK1F9GDA/wGUs7rpVIz8AwEcK15spPwAaIYQGyy8/AOCJrros4D4AAIUCfQHvPgCGGuAYBw0/ADDUiWZdEj8AIv/RDwEgP0BqNeelByc/AEQkUOEJJT8AZPwvN80dPwC5UdjBoA4/AM/iFMiJCT8A1NyHBvAVPwC38b362jA/APw2WT0tLz8ApiOeKaclPwBYY8N1lBY/wBfSXcYiOj8ABtw/2SovPwAYTfOg3Qg/AMB3pc2+2T4Apamo3CUiPwBAajqqRuI+ACCOx+0mBT8AcKKiizYXPwBehSwldz4/AHhQ4CYoHT8AqBee4zkFPwBk1CSmwPk+AB/7zGX/Mz8AeGf9RdoPPwCMLgv9jQs/AHyViRvF+z4APPRBXH8mPwBcXEWHxwk/AEYUCBKsBT8AsLashpzoPgBYMFCBDCk/gAruq0PmHj8AQM2Kg7P/PgBA2bRj1uQ+AJbxjFPVLD8AMDRNeWb7PgBw/RkEe+k+ABRA6JbhFD8AEtKD2jlAPwBQb2X69DI/4MSTpJuiMT8AwHRc3tYAPwB5EUJhUEc/YH8+Z/TuOj9gPQR9tpQxPwAYwSUVVBg/gEb0M0k/LT8A+JCd9BEMPwAANZxgRsM+AIg8NpETGD8AGPHHGMfnPgBQ8hZo/P0+ANAeHIYxCT8AIEZ8EpYZPwBI2w4PRfA+AKDhsLJZ/j4AfBvy4oUaPwCI/f8gShY/AOvxAYaBIz8ABMPrlA8BPwAMaMvQh+M+AJS7+Gw2CD8AEq4TCJwmPwAImpHCjQE/ACijub7A+z4Axpsm7ngUPwAcFj+D6jk/AMjqZ2N0GT8A2ANLtgwcPwDUNJoPN/k+AFBZMV0uNT8AgCYIXEUPPwAQilAAgAE/AMBeFGxCET8AMOrrwoIqPwCQVsY5Yxg/APRqX53bET8AFovAONb0PgCAFjrOCSo/AEg9ijuUGj8AMIJb02znPgDE4JnLpPI+APKDH7ZDHT8ANAiVPdcGPwD8lEM/Dw8/AERgITULFj8A/D47OsohPwC8fuaeKiw/ACVT5TDUHz8Ay+kfgoQrPwBgUyv/yfE+AHo42TrIHj9A9Ra2Z5sTPwBO+aIW1CU/AEPZLLXe/T4AhOZHdl8DPwCA1GqCdrY+ABhV74cLFD8AEGJtGQ8xP4DIKJoc/yY/AMlHQO4MKj8AFlDzmxMoP4B8HXw9bTE/ACiVdJPfJD8A9Hs/TysoPwBwAwBq6yI/ACqBvJD1Ij8A6EC2L08jPwCa+sloOCo/ANYvQA/9Kj8AKO/goSoXPwCE7teIQCE/AJYfwsc4Ij8AhL+lvSMlPwAu2EnuICw/ABgiwL00ED8AcGfV7nMaPwAUVVT80vM+AO6wyHE7KT8Agm78ghkWPwDSSvBB1hM/ACAkmc5b6T4AAAduRQgmPwAFzf1WkCE/ACIbyDoQCz8Abuj41YoTPwCm7snPlSs/gDlXjb4gIj8AnuQ4AvIQPwBg39WquO4+AIOfgfO0Mz8ArCuTXRolPwAcnDQW2R0/AGCPzUiq0T4AmjV1cCI6P4AuqlPabTA/QEvbLwYmKD8A+IEeqEsSPwAAx8A8YxM/AMSJTq9w9z6Aq7lHDQsIPwAIJbJLlSI/gLz9DUJKKj8AwAMHhfv8PgAgS+TS2hI/AEAZbo+h3z5AYUG/lA4iPwBmEYkSsyE/ABCJ+l6F+D4=","dtype":"float64","shape":[4000]}},"selected":{"id":"1057","type":"Selection"},"selection_policy":{"id":"1058","type":"UnionRenderers"}},"id":"1039","type":"ColumnDataSource"},{"attributes":{"line_color":"green","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"line_color":"blue","x":{"field":"x"},"y":{"field":"y"}},"id":"1045","type":"Line"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"9e03330c-427a-4260-b164-66a0daf5d18d","roots":{"1002":"4d702191-52ab-481a-9385-818d123704d9"}}];
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

