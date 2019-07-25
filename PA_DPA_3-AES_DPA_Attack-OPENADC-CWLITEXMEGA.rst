
PA\_DPA\_3-AES\_DPA\_Attack
===========================

Supported setups:

SCOPES:

-  OPENADC
-  CWNANO

PLATFORMS:

-  CWLITEARM
-  CWLITEXMEGA - **NOTE: this attack is less reliable on the CWLITEXMEGA
   than other targets**
-  CWNANO

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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep \|\| exit 0
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

    %run "Helper_Scripts/Setup_Generic.ipynb"


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
        traces.append(trace)
    
    #Convert traces to numpy arrays
    trace_array = np.asarray([trace.wave for trace in traces])
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
        trig_count = 7704571
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

    WARNING:root:Trigger not found in ADC data. No data reported!
    c:\users\user\code\term3\chipwhisperer\software\chipwhisperer\__init__.py:289: UserWarning: Timeout happened during capture
      warnings.warn("Timeout happened during capture")
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    WARNING:root:Trigger not found in ADC data. No data reported!
    


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
    0.015737342194881515
    0.015737342194881515
    






.. parsed-literal::

    0x7e(real = 0x7E)
    0.016910595086521485
    0.016910595086521485
    






.. parsed-literal::

    0x15(real = 0x15)
    0.016639102921090443
    0.016639102921090443
    






.. parsed-literal::

    0x16(real = 0x16)
    0.01759248774259964
    0.01759248774259964
    






.. parsed-literal::

    0x28(real = 0x28)
    0.017090791381838888
    0.017090791381838888
    






.. parsed-literal::

    0xae(real = 0xAE)
    0.01660081764218313
    0.01660081764218313
    






.. parsed-literal::

    0xd2(real = 0xD2)
    0.016924318518190817
    0.016924318518190817
    




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

    

    <div id="14da8a7d-df27-461d-ba50-8a38bc450b86"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#14da8a7d-df27-461d-ba50-8a38bc450b86');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="3edae99c-1c80-4e45-96e3-4d587cac6f6e" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="5307f9ed-0861-43d1-a658-29897cfee4b0"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#5307f9ed-0861-43d1-a658-29897cfee4b0');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"02da1492-9fbc-41ef-9a18-87a13d9aa77f":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"},{"id":"1042","type":"GlyphRenderer"},{"id":"1047","type":"GlyphRenderer"}],"title":{"id":"1050","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1056","type":"Selection"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1057","type":"UnionRenderers"},{"attributes":{},"id":"1058","type":"Selection"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1059","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1053","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1060","type":"Selection"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1061","type":"UnionRenderers"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"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","dtype":"float64","shape":[1530]}},"selected":{"id":"1056","type":"Selection"},"selection_policy":{"id":"1057","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"data_source":{"id":"1039","type":"ColumnDataSource"},"glyph":{"id":"1040","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1041","type":"Line"},"selection_glyph":null,"view":{"id":"1043","type":"CDSView"}},"id":"1042","type":"GlyphRenderer"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1051","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1051","type":"BasicTickFormatter"},{"attributes":{"line_color":"blue","x":{"field":"x"},"y":{"field":"y"}},"id":"1045","type":"Line"},{"attributes":{"source":{"id":"1039","type":"ColumnDataSource"}},"id":"1043","type":"CDSView"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"overlay":{"id":"1055","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"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","dtype":"float64","shape":[1530]}},"selected":{"id":"1058","type":"Selection"},"selection_policy":{"id":"1059","type":"UnionRenderers"}},"id":"1039","type":"ColumnDataSource"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1041","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529],"y":{"__ndarray__":"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","dtype":"float64","shape":[1530]}},"selected":{"id":"1060","type":"Selection"},"selection_policy":{"id":"1061","type":"UnionRenderers"}},"id":"1044","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1044","type":"ColumnDataSource"}},"id":"1048","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1046","type":"Line"},{"attributes":{"line_color":"green","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"data_source":{"id":"1044","type":"ColumnDataSource"},"glyph":{"id":"1045","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1046","type":"Line"},"selection_glyph":null,"view":{"id":"1048","type":"CDSView"}},"id":"1047","type":"GlyphRenderer"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1040","type":"Line"},{"attributes":{"text":""},"id":"1050","type":"Title"},{"attributes":{},"id":"1053","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1055","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"02da1492-9fbc-41ef-9a18-87a13d9aa77f","roots":{"1002":"3edae99c-1c80-4e45-96e3-4d587cac6f6e"}}];
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

