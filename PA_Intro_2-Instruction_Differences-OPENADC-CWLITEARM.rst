
Instruction Power Differences
=============================

This tutorial will introduce you to measuring the power consumption of a
device under attack. It will demonstrate how the power consumption of a
target changes based on what operations it's doing.

If you haven't yet, you should probably complete Tutorial B1, which
introduces building firmware, programming the target, and scripting.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'

Setting up Firmware
-------------------

In this tutorial, we will once again be working off of the
simpleserial-base firmware.

Let's start by creating a new project and building our firmware:


**In [2]:**

.. code:: bash

    %%bash
    cd ../hardware/victims/firmware/
    mkdir -p simpleserial-base-lab2 && cp -r simpleserial-base/* $_
    cd simpleserial-base-lab2


**In [3]:**

.. code:: ipython3

    CRYPTO_TARGET = "NONE"


**In [4]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [4]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEARM.hex
    rm -f -- simpleserial-base-CWLITEARM.eep
    rm -f -- simpleserial-base-CWLITEARM.cof
    rm -f -- simpleserial-base-CWLITEARM.elf
    rm -f -- simpleserial-base-CWLITEARM.map
    rm -f -- simpleserial-base-CWLITEARM.sym
    rm -f -- simpleserial-base-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    mkdir objdir 
    mkdir .dep
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-base-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEARM.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-base-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-base-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4664	      8	   1296	   5968	   1750	simpleserial-base-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



As in the previous tutorial, we'll need to modify our firmware. Navigate
to the ``get_pt()`` function:

.. code:: c

    uint8_t get_pt(uint8_t* pt)
    {
        /**********************************
        * Start user-specific code here. */
        trigger_high();
        
        //16 hex bytes held in 'pt' were sent
        //from the computer. Store your response
        //back into 'pt', which will send 16 bytes
        //back to computer. Can ignore of course if
        //not needed
        
        trigger_low();
        /* End user-specific code here. *
        ********************************/
        simpleserial_put('r', 16, pt);
        return 0x00;
    }

To start off, we'll add a simple ``for`` loop. We'll start off by
looking at how the power trace changes based on how long the loop is.
Start with a loop (make sure your variables are volatile) that runs from
0 to 4:

.. code:: c

    for(volatile int i = 0; i < 5; i++);

Next, we'll move on to actually capturing and displaying the power
trace.

ChipWhisperer Setup
-------------------

Setup for this tutorial will be pretty similar to Tutorial B1, so we'll
skip most of it by calling some helper scripts. This setup should work
for most targets, but if you're using a target other than the XMEGA or
STM32F3 (CWLite w/ Arm), you may need to call a different script or do
additional setup (like programming the target with an external
programmer). See the wiki page for your target for more information.

If you're curious about what's happening in these helper scripts,
they're typically located in the ``Helper_Scripts`` folder.


**In [5]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [5]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEARM.hex
    rm -f -- simpleserial-base-CWLITEARM.eep
    rm -f -- simpleserial-base-CWLITEARM.cof
    rm -f -- simpleserial-base-CWLITEARM.elf
    rm -f -- simpleserial-base-CWLITEARM.map
    rm -f -- simpleserial-base-CWLITEARM.sym
    rm -f -- simpleserial-base-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-base-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEARM.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-base-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-base-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4664	      8	   1296	   5968	   1750	simpleserial-base-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------




**In [6]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"

By default, the scope will capture many more traces than we need, so
we'll reduce that to 1000.


**In [7]:**

.. code:: ipython3

    scope.adc.samples = 1000
    fw_path = '../hardware/victims/firmware/simpleserial-base-lab2/simpleserial-base-{}.hex'.format(PLATFORM)

Programming is the same as in the last part:


**In [8]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [8]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4671 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4671 bytes
    


Capturing Traces
----------------


**In [9]:**

.. code:: ipython3

    print(scope)


**Out [9]:**



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
        samples    = 1000
        decimate   = 1
        trig_count = 8396058
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
    
    


Like before, most of this should look familiar from the last tutorial.
We'll start by programming the target, then capturing a trace, and
finally displaying it using bokeh. We don't really care about what the
target responds with this time, so we won't do anything with what we
read back.


**In [10]:**

.. code:: ipython3

    from bokeh.plotting import figure, show
    from bokeh.io import output_notebook
    import numpy as np
    output_notebook()
    ktp = cw.ktp.Basic()
    key, text = ktp.next()  # manual creation of a key, text pair can be substituted here
    
    trace = cw.capture_trace(scope, target, text)
    xrange = range(len(trace.wave))
    p = figure()
    p.line(xrange, trace.wave, line_color="red")
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

    

    <div id="7982c19b-2ee8-4526-8f57-01774ca4a20f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7982c19b-2ee8-4526-8f57-01774ca4a20f');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="de0e30e8-1fda-4d82-8832-8d469b140144" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="74933cbb-3940-4adc-b4a5-55d8e9519ba8"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#74933cbb-3940-4adc-b4a5-55d8e9519ba8');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"a27ba240-7b9c-4aae-bc76-52e83f17bd1f":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1026","type":"HelpTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"a27ba240-7b9c-4aae-bc76-52e83f17bd1f","roots":{"1002":"de0e30e8-1fda-4d82-8832-8d469b140144"}}];
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

Further Modifications
---------------------

You might be able to tell where our loop is happening, but to make sure,
let's modify it and see how the trace changes. Change when the loop ends
and save the file. Rebuild the file:


**In [11]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [11]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEARM.hex
    rm -f -- simpleserial-base-CWLITEARM.eep
    rm -f -- simpleserial-base-CWLITEARM.cof
    rm -f -- simpleserial-base-CWLITEARM.elf
    rm -f -- simpleserial-base-CWLITEARM.map
    rm -f -- simpleserial-base-CWLITEARM.sym
    rm -f -- simpleserial-base-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-base-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEARM.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-base-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-base-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4664	      8	   1296	   5968	   1750	simpleserial-base-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



Reprogram the target:


**In [12]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [12]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4671 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4671 bytes
    


And capture a new trace:


**In [13]:**

.. code:: ipython3

    trace2 = cw.capture_trace(scope, target, text)

Instead of doing all the plotting work we did earlier, we can use
another helper script:


**In [14]:**

.. code:: ipython3

    %run "Helper_Scripts/bokeh.ipynb"

Then use the ``bokeh_plot()`` function to plot:


**In [15]:**

.. code:: ipython3

    bokeh_plot(trace2.wave)


**Out [15]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1102">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="11e6b482-5d1f-4598-89a9-9dfbd86f85f9"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#11e6b482-5d1f-4598-89a9-9dfbd86f85f9');
        
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
        var el = document.getElementById("1102");
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
      };var element = document.getElementById("1102");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1102' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1102")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="9c0fc7b8-63cb-4bf3-9834-bec1e47c05b0" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="b65b63b5-bf6d-47c5-aa50-3afca18321e5"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b65b63b5-bf6d-47c5-aa50-3afca18321e5');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"a0481863-f6c2-4482-8ced-cb9ecb07ea3c":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1155","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{},"id":"1155","type":"Selection"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"a0481863-f6c2-4482-8ced-cb9ecb07ea3c","roots":{"1103":"9c0fc7b8-63cb-4bf3-9834-bec1e47c05b0"}}];
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

It should now be very obvious where our loop is happening. Verify that
the number of similar looking power spikes matches how many times the
loop is run. Now let's take a look at how different operations affect
power traces:

Comparing Operations
--------------------

To make things easy on us, let's select operations that we would expect
to have very different power traces. One easy metric to base your
decision on is how long the associated instructions take to execute
(which is typically documented either in the datasheet, or in the core's
documentation). For example, on XMEGA we'll compare adding (1 cycle)
with multiplying (2 cycles).

XMEGA
~~~~~

STM32F3
~~~~~~~

Our reference for the STM32F3 (which has an Arm Cortex M4 CPU) will the
`Cortex M4 Instruction Set
Summary <http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.ddi0439b/CHDDIGAC.html>`__.
As we can see, the multiply instruction takes 1 cycle to execute, while
the divide instruction takes between 2 and 12. These instructions also
have analogous C operations (\* and /) so it will be easy to get the
compiler to insert them.

This is a pretty drastic difference, so the two instructions should look
very different. Change your loop so that it preforms multiplications
each time through:

.. code:: c

    volatile int b = 0xAFFA;
    for (volatile int i = 0; i < 10; i++)
        b *= 11;

Capture: Operation 1
~~~~~~~~~~~~~~~~~~~~

Now rebuild and capture another trace:


**In [16]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [16]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEARM.hex
    rm -f -- simpleserial-base-CWLITEARM.eep
    rm -f -- simpleserial-base-CWLITEARM.cof
    rm -f -- simpleserial-base-CWLITEARM.elf
    rm -f -- simpleserial-base-CWLITEARM.map
    rm -f -- simpleserial-base-CWLITEARM.sym
    rm -f -- simpleserial-base-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-base-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEARM.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-base-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-base-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4664	      8	   1296	   5968	   1750	simpleserial-base-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------




**In [17]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [17]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4671 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4671 bytes
    



**In [18]:**

.. code:: ipython3

    trace3 = cw.capture_trace(scope, target, text)
    bokeh_plot(trace3.wave)


**Out [18]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1212">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="67d76038-8a0f-42db-93b7-2088fda893c0"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#67d76038-8a0f-42db-93b7-2088fda893c0');
        
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
        var el = document.getElementById("1212");
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
      };var element = document.getElementById("1212");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1212' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1212")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="bcf8eb40-f309-47a6-9cf8-515afbbf3a34" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="5c9bf6c0-6d50-47e1-81e0-47cf08e95b7e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#5c9bf6c0-6d50-47e1-81e0-47cf08e95b7e');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"401103b9-bc78-4efb-881b-504ba34aa836":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1269","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{},"id":"1220","type":"LinearScale"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1272","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{},"id":"1236","type":"ResetTool"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{"formatter":{"id":"1270","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1274","type":"Selection"},"selection_policy":{"id":"1275","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{"overlay":{"id":"1276","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{},"id":"1275","type":"UnionRenderers"},{"attributes":{},"id":"1272","type":"BasicTickFormatter"},{"attributes":{},"id":"1270","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1269","type":"Title"},{"attributes":{},"id":"1274","type":"Selection"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1276","type":"BoxAnnotation"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"401103b9-bc78-4efb-881b-504ba34aa836","roots":{"1213":"bcf8eb40-f309-47a6-9cf8-515afbbf3a34"}}];
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

Capture: Operation 2
~~~~~~~~~~~~~~~~~~~~

Replace your first operation with your second. Save the file, rebuild,
program, and capture another trace.


**In [19]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [19]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEARM.hex
    rm -f -- simpleserial-base-CWLITEARM.eep
    rm -f -- simpleserial-base-CWLITEARM.cof
    rm -f -- simpleserial-base-CWLITEARM.elf
    rm -f -- simpleserial-base-CWLITEARM.map
    rm -f -- simpleserial-base-CWLITEARM.sym
    rm -f -- simpleserial-base-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-base-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEARM.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output simpleserial-base-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-base-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEARM.elf simpleserial-base-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-base-CWLITEARM.elf > simpleserial-base-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4664	      8	   1296	   5968	   1750	simpleserial-base-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------




**In [20]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [20]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4671 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4671 bytes
    



**In [21]:**

.. code:: ipython3

    trace4 = cw.capture_trace(scope, target, text)
    bokeh_plot(trace4.wave)


**Out [21]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1331">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="05241a91-a0f3-422e-908f-37ea70d546a3"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#05241a91-a0f3-422e-908f-37ea70d546a3');
        
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
        var el = document.getElementById("1331");
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
      };var element = document.getElementById("1331");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1331' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1331")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="a384804a-3e67-4af2-8d65-79bfa296ba6b" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="ae807054-5b8c-49de-a0a0-437dffb2ff53"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ae807054-5b8c-49de-a0a0-437dffb2ff53');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"b36d4b89-337e-4289-9069-c6702a4b718c":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1397","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1402","type":"Selection"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{"formatter":{"id":"1400","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{},"id":"1400","type":"BasicTickFormatter"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"},{"attributes":{},"id":"1403","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1404","type":"BoxAnnotation"},{"attributes":{},"id":"1347","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1398","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{"text":""},"id":"1397","type":"Title"},{"attributes":{},"id":"1342","type":"BasicTicker"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"AAAAAACAtT8AAAAAAECzvwAAAAAAAKy/AAAAAAAApL8AAAAAAICgPwAAAAAAkNG/AAAAAACAt78AAAAAAICrvwAAAAAAAJc/AAAAAADgxb8AAAAAAAC/vwAAAAAAALW/AAAAAAAAaD8AAAAAAEC+vwAAAAAAQLG/AAAAAAAApb8AAAAAAACePwAAAAAAgLe/AAAAAAAAqb8AAAAAAACevwAAAAAAAKU/AAAAAAAAnr8AAAAAAICkPwAAAAAAAKo/AAAAAABAvD8AAAAAAABoPwAAAAAAAIg/AAAAAAAAiD8AAAAAAMCxPwAAAAAAoMC/AAAAAABAuL8AAAAAAICwvwAAAAAAAI4/AAAAAAAAub8AAAAAAACqvwAAAAAAAJy/AAAAAACApD8AAAAAAAC+vwAAAAAAQLG/AAAAAACApb8AAAAAAACePwAAAAAAQLa/AAAAAAAAqb8AAAAAAACbvwAAAAAAgKU/AAAAAAAAqr8AAAAAAACRvwAAAAAAAFC/AAAAAAAAsD8AAAAAAEC2vwAAAAAAgKW/AAAAAAAAoL8AAAAAAICkPwAAAAAAAMS/AAAAAABAvb8AAAAAAICzvwAAAAAAAIY/AAAAAAAgwb8AAAAAAICvvwAAAAAAgKC/AAAAAACApD8AAAAAAAC1vwAAAAAAAKG/AAAAAAAAjr8AAAAAAACrPwAAAAAAAJq/AAAAAAAApz8AAAAAAACtPwAAAAAAQL4/AAAAAAAAtj8AAAAAACDAPwAAAAAAQL8/AAAAAADAxD8AAAAAAACaPwAAAAAAAJY/AAAAAAAAkz8AAAAAAACzPwAAAAAAALO/AAAAAACAob8AAAAAAACVvwAAAAAAAKY/AAAAAACgyb8AAAAAAMC/vwAAAAAAQLa/AAAAAAAAYD8AAAAAAADCvwAAAAAAAKK/AAAAAAAAaL8AAAAAAACzPwAAAAAAAJO/AAAAAACAqj8AAAAAAECwPwAAAAAAQL8/AAAAAAAAiD8AAAAAAICyPwAAAAAAQLQ/AAAAAADgwD8AAAAAAACnPwAAAAAAQLg/AAAAAABAuD8AAAAAACDCPwAAAAAAgK8/AAAAAAAAuz8AAAAAAEC6PwAAAAAAoMI/AAAAAACAqD8AAAAAAMC3PwAAAAAAgLc/AAAAAABgwT8AAAAAAACWPwAAAAAAwLI/AAAAAADAsz8AAAAAAMC/PwAAAAAAwLM/AAAAAABAvT8AAAAAAIC7PwAAAAAAwMI/AAAAAAAAiD8AAAAAAACEPwAAAAAAAIA/AAAAAAAAsD8AAAAAAAC9vwAAAAAAgLC/AAAAAACAp78AAAAAAACZPwAAAAAAAMy/AAAAAAAgwr8AAAAAAMC6vwAAAAAAAI6/AAAAAABAx78AAAAAAEC8vwAAAAAAgLS/AAAAAAAAUL8AAAAAAMDAvwAAAAAAgKG/AAAAAAAAaL8AAAAAAACyPwAAAAAAAI6/AAAAAACAqT8AAAAAAICuPwAAAAAAQL0/AAAAAAAAaD8AAAAAAACvPwAAAAAAwLA/AAAAAABAvj8AAAAAAACYvwAAAAAAAKU/AAAAAAAAqT8AAAAAAIC7PwAAAAAAAKo/AAAAAAAAuD8AAAAAAIC3PwAAAAAAIME/AAAAAAAAkT8AAAAAAICwPwAAAAAAgK8/AAAAAABAvD8AAAAAAICuvwAAAAAAgKW/AAAAAAAAnL8AAAAAAIChPwAAAAAAIMq/AAAAAADAtb8AAAAAAACqvwAAAAAAgKE/AAAAAABgyL8AAAAAAODEvwAAAAAAwLy/AAAAAAAAkL8AAAAAAADLvwAAAAAAYMO/AAAAAAAAvL8AAAAAAACTvwAAAAAAIMe/AAAAAABAu78AAAAAAECyvwAAAAAAAHw/AAAAAADA0r8AAAAAAEDLvwAAAAAA4MK/AAAAAACApL8AAAAAAMDEvwAAAAAAgKm/AAAAAAAAjr8AAAAAAECwPwAAAAAAwL6/AAAAAACAtL8AAAAAAICqvwAAAAAAAJk/AAAAAACAwb8AAAAAAICgvwAAAAAAAFC/AAAAAACAsz8AAAAAAACQvwAAAAAAgKo/AAAAAAAAsD8AAAAAAMC+PwAAAAAAgKM/AAAAAADAtj8AAAAAAIC3PwAAAAAAYME/AAAAAACArz8AAAAAAMC6PwAAAAAAQLo/AAAAAACAwj8AAAAAAMCwPwAAAAAAALs/AAAAAACAuj8AAAAAAKDCPwAAAAAAgKk/AAAAAADAtz8AAAAAAEC3PwAAAAAAYME/AAAAAAAAhD8AAAAAAECwPwAAAAAAQLE/AAAAAADAvT8AAAAAAACdPwAAAAAAgLM/AAAAAABAsj8AAAAAAMC+PwAAAAAAAJI/AAAAAAAAsT8AAAAAAICyPwAAAAAAAL4/AAAAAACAsj8AAAAAAEC7PwAAAAAAQLo/AAAAAADgwT8AAAAAAIC3PwAAAAAAgL4/AAAAAABAvD8AAAAAAMDCPwAAAAAAAKU/AAAAAAAAoj8AAAAAAACZPwAAAAAAgLE/AAAAAAAAoL8AAAAAAACcPwAAAAAAgKE/AAAAAABAtz8AAAAAAACrvwAAAAAAAKa/AAAAAACAoL8AAAAAAACdPwAAAAAAgL+/AAAAAACAtL8AAAAAAICuvwAAAAAAAII/AAAAAACAxb8AAAAAAEC8vwAAAAAAQLS/AAAAAAAAeL8AAAAAACDDvwAAAAAAwLi/AAAAAACAsb8AAAAAAAB0PwAAAAAAIMa/AAAAAABAvL8AAAAAAAC1vwAAAAAAAGC/AAAAAACAxr8AAAAAAEC8vwAAAAAAALa/AAAAAAAAcL8AAAAAAEDKvwAAAAAAAMC/AAAAAABAt78AAAAAAAB4vwAAAAAAQMO/AAAAAAAAub8AAAAAAMCyvwAAAAAAAGA/AAAAAADAy78AAAAAAIDCvwAAAAAAALm/AAAAAAAAhr8AAAAAAGDAvwAAAAAAgLO/AAAAAACAqL8AAAAAAACZPwAAAAAAwL+/AAAAAABAtL8AAAAAAMCwvwAAAAAAAIY/AAAAAAAgxL8AAAAAAEC3vwAAAAAAQLC/AAAAAAAAkD8AAAAAACDDvwAAAAAAgKW/AAAAAAAAjr8AAAAAAECwPwAAAAAAAHQ/AAAAAADAsD8AAAAAAICyPwAAAAAAQL8/AAAAAAAAnL8AAAAAAACkPwAAAAAAgKc/AAAAAACAuj8AAAAAAAC4vwAAAAAAQLG/AAAAAACAp78AAAAAAACcPwAAAAAAIMu/AAAAAACgwL8AAAAAAAC3vwAAAAAAAGC/AAAAAABAw78AAAAAAIC1vwAAAAAAAK+/AAAAAAAAkz8AAAAAAACbvwAAAAAAAJ8/AAAAAACAoD8AAAAAAEC1PwAAAAAAAKa/AAAAAAAAmj8AAAAAAACkPwAAAAAAALo/AAAAAADAt78AAAAAAECwvwAAAAAAgKO/AAAAAAAAnz8AAAAAAMC3vwAAAAAAAIK/AAAAAAAAkT8AAAAAAMC1PwAAAAAAAIw/AAAAAABAsj8AAAAAAECyPwAAAAAAgL8/AAAAAAAAs78AAAAAAACrvwAAAAAAAKS/AAAAAAAAoj8AAAAAAKDJvwAAAAAAQL+/AAAAAADAtL8AAAAAAAB0PwAAAAAA4MC/AAAAAAAAsr8AAAAAAICmvwAAAAAAAJw/AAAAAAAAs78AAAAAAACEPwAAAAAAAJo/AAAAAAAAtz8AAAAAAACqPwAAAAAAgLk/AAAAAACAuD8AAAAAAADCPwAAAAAAAJ8/AAAAAABAtT8AAAAAAAC0PwAAAAAAgMA/AAAAAACAqD8AAAAAAEC4PwAAAAAAQLg/AAAAAACgwT8AAAAAAACnvwAAAAAAAJy/AAAAAAAAk78AAAAAAICmPwAAAAAAwL+/AAAAAADAtb8AAAAAAECwvwAAAAAAAIY/AAAAAABAur8AAAAAAACtvwAAAAAAgKG/AAAAAAAAoj8AAAAAAMC6vwAAAAAAAK+/AAAAAACAo78AAAAAAICgPwAAAAAAoMC/AAAAAACAsr8AAAAAAICrvwAAAAAAAJg/AAAAAAAAzb8AAAAAAODBvwAAAAAAgLu/AAAAAAAAjL8AAAAAAEDLvwAAAAAAwLW/AAAAAACAo78AAAAAAICoPwAAAAAAALm/AAAAAAAAdL8AAAAAAACXPwAAAAAAQLc/AAAAAAAAp78AAAAAAACcvwAAAAAAAJO/AAAAAACApz8AAAAAAICzvwAAAAAAAHw/AAAAAAAAnT8AAAAAAAC4PwAAAAAAAIa/AAAAAACArD8AAAAAAMCwPwAAAAAAwL4/AAAAAAAAdD8AAAAAAACIPwAAAAAAAIQ/AAAAAADAsD8AAAAAAECyvwAAAAAAAIA/AAAAAAAAmz8AAAAAAIC3PwAAAAAAALe/AAAAAADAsb8AAAAAAICovwAAAAAAAJo/AAAAAABgwL8AAAAAAACzvwAAAAAAAKm/AAAAAAAAmj8AAAAAAMDKvwAAAAAAgMC/AAAAAACAtr8AAAAAAABwPwAAAAAAoMK/AAAAAAAAo78AAAAAAACAvwAAAAAAQLI/AAAAAAAAnL8AAAAAAACoPwAAAAAAgK0/AAAAAADAvj8AAAAAAACTPwAAAAAAgLM/AAAAAAAAtT8AAAAAAODAPwAAAAAAgKU/AAAAAACAtz8AAAAAAMC3PwAAAAAAgME/AAAAAACApz8AAAAAAEC3PwAAAAAAALg/AAAAAADgwT8AAAAAAIChPwAAAAAAQLU/AAAAAAAAtj8AAAAAAODAPwAAAAAAAIy/AAAAAAAAqT8AAAAAAICrPwAAAAAAgLs/AAAAAAAAhD8AAAAAAACxPwAAAAAAQLI/AAAAAABAvz8AAAAAAACEPwAAAAAAAIo/AAAAAAAAjj8AAAAAAACxPwAAAAAAAIy/AAAAAACApj8AAAAAAICrPwAAAAAAALs/AAAAAAAAsT8AAAAAAMC6PwAAAAAAwLk/AAAAAADgwT8AAAAAAECxPwAAAAAAALo/AAAAAAAAuD8AAAAAACDBPwAAAAAAAJ8/AAAAAABAsz8AAAAAAICzPwAAAAAAQL8/AAAAAACAqD8AAAAAAMC2PwAAAAAAALU/AAAAAAAgwD8AAAAAAEC4PwAAAAAAwL8/AAAAAAAAvj8AAAAAAADDPwAAAAAAIMI/AAAAAACAxD8AAAAAACDDPwAAAAAA4MU/AAAAAACgwz8AAAAAAGDFPwAAAAAAoMM/AAAAAABgxj8AAAAAAAC/PwAAAAAAAMI/AAAAAABAvz8AAAAAAGDDPwAAAAAAAL8/AAAAAAAgwj8AAAAAACDAPwAAAAAAgMM/AAAAAACAvz8AAAAAAADCPwAAAAAAgL8/AAAAAABgwz8AAAAAAACpPwAAAAAAAJs/AAAAAAAAij8AAAAAAACpPwAAAAAAgLa/AAAAAACArr8AAAAAAICrvwAAAAAAAGg/AAAAAABAub8AAAAAAACcvwAAAAAAAIS/AAAAAAAAqz8AAAAAAACVPwAAAAAAAK8/AAAAAAAArz8AAAAAAEC6PwAAAAAAgKi/AAAAAAAAq78AAAAAAACrvwAAAAAAAGg/AAAAAABgwb8AAAAAAIC4vwAAAAAAQLS/AAAAAAAAir8AAAAAAMDLvwAAAAAA4MK/AAAAAABAvr8AAAAAAACjvwAAAAAAAMm/AAAAAABAt78AAAAAAACsvwAAAAAAAJU/AAAAAACApL8AAAAAAACWPwAAAAAAAKA/AAAAAAAAtT8AAAAAAACWPwAAAAAAALA/AAAAAABAsD8AAAAAAAC7PwAAAAAAgKI/AAAAAADAsj8AAAAAAICxPwAAAAAAQLw/AAAAAACAqT8AAAAAAMC1PwAAAAAAQLQ/AAAAAACAvT8AAAAAAICnPwAAAAAAQLQ/AAAAAADAsj8AAAAAAAC8PwAAAAAAwLE/AAAAAADAuD8AAAAAAAC3PwAAAAAAgL8/AAAAAAAAoD8AAAAAAACKPwAAAAAAAHg/AAAAAAAApz8AAAAAAICyvwAAAAAAAKm/AAAAAAAApr8AAAAAAACKPwAAAAAAIMK/AAAAAACAuL8AAAAAAIC1vwAAAAAAAJC/AAAAAABAtb8AAAAAAACKvwAAAAAAAFA/AAAAAAAArz8AAAAAAICivwAAAAAAgKC/AAAAAAAAnb8AAAAAAACWPwAAAAAAwLq/AAAAAAAAn78AAAAAAACQvwAAAAAAgKY/AAAAAACAxL8AAAAAAGDAvwAAAAAAwLq/AAAAAAAAnr8AAAAAAMDGvwAAAAAAwLO/AAAAAACAp78AAAAAAACePwAAAAAAAJa/AAAAAACAoj8AAAAAAICjPwAAAAAAgLY/AAAAAAAAtr8AAAAAAMCzvwAAAAAAALG/AAAAAAAAYL8AAAAAAAC/vwAAAAAAALS/AAAAAACAr78AAAAAAABQPwAAAAAA4MW/AAAAAADAvr8AAAAAAEC5vwAAAAAAAJe/AAAAAACAzb8AAAAAAEC8vwAAAAAAQLO/AAAAAAAAiD8AAAAAAMC1vwAAAAAAAIK/AAAAAAAAaD8AAAAAAECxPwAAAAAAgMO/AAAAAADAvr8AAAAAAIC4vwAAAAAAAJC/AAAAAABgw78AAAAAAAC4vwAAAAAAALK/AAAAAAAAUD8AAAAAAEC9vwAAAAAAALS/AAAAAACArr8AAAAAAAB0PwAAAAAAQMG/AAAAAAAAp78AAAAAAACWvwAAAAAAAKg/AAAAAADgwb8AAAAAAIC6vwAAAAAAwLK/AAAAAAAAYL8AAAAAAAC1vwAAAAAAAHS/AAAAAAAAij8AAAAAAACzPwAAAAAAAJc/AAAAAAAAsj8AAAAAAECxPwAAAAAAQL0/AAAAAAAAkj8AAAAAAAC0PwAAAAAAgLE/AAAAAACAvD8AAAAAAACbPwAAAAAAwLE/AAAAAACAsD8AAAAAAMC7PwAAAAAAgLe/AAAAAACAsr8AAAAAAACtvwAAAAAAAIA/AAAAAACgwL8AAAAAAICnvwAAAAAAAJS/AAAAAAAAqT8AAAAAAEDBvwAAAAAAALy/AAAAAABAtr8AAAAAAACEvwAAAAAAoMW/AAAAAAAAu78AAAAAAEC1vwAAAAAAAHy/AAAAAABAz78AAAAAACDEvwAAAAAAQL6/AAAAAAAAnb8AAAAAAKDGvwAAAAAAQLK/AAAAAACAo78AAAAAAACjPwAAAAAAgKq/AAAAAAAAkj8AAAAAAICgPwAAAAAAALY/AAAAAAAAcL8AAAAAAACqPwAAAAAAgKw/AAAAAADAuj8AAAAAAACKPwAAAAAAQLA/AAAAAACAsD8AAAAAAAC8PwAAAAAAAI4/AAAAAABAsD8AAAAAAECwPwAAAAAAALw/AAAAAAAAcD8AAAAAAACtPwAAAAAAAK0/AAAAAACAuj8AAAAAAICjvwAAAAAAAJY/AAAAAAAAnj8AAAAAAAC1PwAAAAAAAIa/AAAAAACApD8AAAAAAICoPwAAAAAAQLg/AAAAAAAAkr8AAAAAAACIvwAAAAAAAIa/AAAAAACApT8AAAAAAAClvwAAAAAAAJU/AAAAAAAAmz8AAAAAAMC0PwAAAAAAAKQ/AAAAAACAtD8AAAAAAICzPwAAAAAAgL0/AAAAAACAoz8AAAAAAMCzPwAAAAAAwLE/AAAAAAAAvD8AAAAAAAB4PwAAAAAAAKo/AAAAAACAqz8AAAAAAMC4PwAAAAAAAJs/AAAAAAAAsT8AAAAAAACwPwAAAAAAALo/AAAAAAAAsz8AAAAAAAC6PwAAAAAAQLg/AAAAAABAwD8AAAAAAMC9PwAAAAAAoME/AAAAAAAgwD8AAAAAACDDPwAAAAAAgMA/AAAAAACAwj8AAAAAAKDAPwAAAAAAgMM/AAAAAAAAuj8AAAAAAMC+PwAAAAAAgLo/AAAAAADAwD8AAAAAAAC6PwAAAAAAgL4/AAAAAADAuj8AAAAAAODAPwAAAAAAwLk/AAAAAACAvj8AAAAAAMC6PwAAAAAAoMA/AAAAAAAAnT8AAAAAAAB4PwAAAAAAAHy/AAAAAAAAoD8AAAAAAEC8vwAAAAAAQLS/AAAAAABAs78AAAAAAACOvwAAAAAA4MG/AAAAAAAArr8AAAAAAAClvwAAAAAAAJ0/AAAAAAAAfL8AAAAAAAClPwAAAAAAAKQ/AAAAAACAtD8AAAAAAACvvwAAAAAAgK2/AAAAAACArL8AAAAAAABQvwAAAAAAIM+/AAAAAAAgyr8AAAAAAADEvwAAAAAAALK/AAAAAABA0b8AAAAAAIDKvwAAAAAAoMS/AAAAAADAsb8AAAAAAGDIvwAAAAAAYMG/AAAAAADAvL8AAAAAAACjvwAAAAAAoMW/AAAAAABAv78AAAAAAMC4vwAAAAAAAJu/AAAAAABAzr8AAAAAAADHvwAAAAAAoMG/AAAAAAAAqb8AAAAAACDLvwAAAAAAQMK/AAAAAADAu78AAAAAAACfvwAAAAAAIMi/AAAAAABgwL8AAAAAAIC5vwAAAAAAAJW/AAAAAADAsL8AAAAAAACIvwAAAAAAAIK/AAAAAAAApj8AAAAAAAC5vwAAAAAAAJq/AAAAAAAAgr8AAAAAAACtPwAAAAAAgMO/AAAAAACAvb8AAAAAAAC2vwAAAAAAAIi/AAAAAABAwb8AAAAAAACnvwAAAAAAAJS/AAAAAAAAqD8AAAAAAACWvwAAAAAAAKM/AAAAAAAApD8AAAAAAEC2PwAAAAAAwL+/AAAAAABAu78AAAAAAIC1vwAAAAAAAIy/AAAAAACgz78AAAAAAKDEvwAAAAAAQL6/AAAAAACAoL8AAAAAAKDMvwAAAAAAoMO/AAAAAADAvb8AAAAAAICgvwAAAAAAoMO/AAAAAAAAq78AAAAAAACdvwAAAAAAgKg/AAAAAACAxr8AAAAAAEDBvwAAAAAAgLm/AAAAAAAAkb8AAAAAAGDFvwAAAAAAwLq/AAAAAABAsr8AAAAAAAB0PwAAAAAAQMO/AAAAAADAtr8AAAAAAECwvwAAAAAAAII/AAAAAABAxb8AAAAAAMCwvwAAAAAAAJ2/AAAAAAAAqj8AAAAAAACcvwAAAAAAAKQ/AAAAAACApz8AAAAAAMC5PwAAAAAAALI/AAAAAACAvD8AAAAAAIC6PwAAAAAAQMI/AAAAAAAAwT8AAAAAAODDPwAAAAAAoMI/AAAAAADgxT8AAAAAAGDDPwAAAAAAYMU/AAAAAADAwz8AAAAAAMDGPwAAAAAAwL8/AAAAAAAAwj8AAAAAAEDAPwAAAAAAoMM/AAAAAADAvz8AAAAAAEDCPwAAAAAAoMA/AAAAAAAAxD8AAAAAAMC/PwAAAAAAQMI/AAAAAABAwD8AAAAAAKDDPwAAAAAAgKg/AAAAAAAAnz8AAAAAAACQPwAAAAAAgKs/AAAAAADAtr8AAAAAAACtvwAAAAAAgKq/AAAAAAAAdD8AAAAAAIC9vwAAAAAAgKO/AAAAAAAAkb8AAAAAAICoPwAAAAAAAJM/AAAAAACArz8AAAAAAACwPwAAAAAAgLo/AAAAAACAo78AAAAAAICivwAAAAAAgKG/AAAAAAAAlj8AAAAAACDNvwAAAAAAQMe/AAAAAADgwb8AAAAAAICpvwAAAAAAUNC/AAAAAACgx78AAAAAACDCvwAAAAAAgKi/AAAAAAAgxr8AAAAAAIC+vwAAAAAAwLe/AAAAAAAAlL8AAAAAAEDDvwAAAAAAwLq/AAAAAAAAtL8AAAAAAACCvwAAAAAAAMy/AAAAAAAgxb8AAAAAAIC+vwAAAAAAgKC/AAAAAADAyL8AAAAAAEDAvwAAAAAAwLe/AAAAAAAAjr8AAAAAAGDGvwAAAAAAQLy/AAAAAAAAtr8AAAAAAAB4vwAAAAAAAKu/AAAAAAAAfD8AAAAAAAB0PwAAAAAAgK0/AAAAAACAsL8AAAAAAAB4PwAAAAAAAJI/AAAAAADAsz8AAAAAAAC7vwAAAAAAQLW/AAAAAACArb8AAAAAAACAPwAAAAAAAL2/AAAAAAAAm78AAAAAAAB0vwAAAAAAALA/AAAAAAAAeL8AAAAAAACpPwAAAAAAAKk/AAAAAABAuj8AAAAAAAC4vwAAAAAAgLS/AAAAAACAsL8AAAAAAAB4PwAAAAAAAM6/AAAAAADgwr8AAAAAAEC7vwAAAAAAAJO/AAAAAAAAy78AAAAAAADCvwAAAAAAgLu/AAAAAAAAl78AAAAAACDCvwAAAAAAgKa/AAAAAAAAkb8AAAAAAICsPwAAAAAAIMW/AAAAAAAgwL8AAAAAAIC2vwAAAAAAAIK/AAAAAAAgxL8AAAAAAMC3vwAAAAAAQLC/AAAAAAAAjD8AAAAAAGDCvwAAAAAAgLS/AAAAAAAArr8AAAAAAACRPwAAAAAAoMS/AAAAAACAq78AAAAAAACVvwAAAAAAAK4/AAAAAAAAkr8AAAAAAICoPwAAAAAAAKs/AAAAAACAuz8AAAAAAMC0PwAAAAAAAL4/AAAAAAAAvT8AAAAAAEDDPwAAAAAAQMI/AAAAAAAgxT8AAAAAAODDPwAAAAAAAMc/AAAAAABgxD8AAAAAAIDGPwAAAAAAoMQ/AAAAAACgxz8AAAAAAKDAPwAAAAAAIMM/AAAAAADgwD8=","dtype":"float64","shape":[1000]}},"selected":{"id":"1402","type":"Selection"},"selection_policy":{"id":"1403","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{"overlay":{"id":"1404","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{},"id":"1398","type":"BasicTickFormatter"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"b36d4b89-337e-4289-9069-c6702a4b718c","roots":{"1332":"a384804a-3e67-4af2-8d65-79bfa296ba6b"}}];
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

Compare the traces. Is the one that you expected to be longer actually
longer?

The longer operation is typically more complicated than the other
operation, which means we would expect it to have higher power
consumptions as well. Do you also see this behaviour in the traces you
captured?

Disconnecting from ChipWhisperer
--------------------------------

Now that we're done with the tutorial, we'll need to disconnect from
ChipWhisperer, so that we can connect to it in a different tutorial:


**In [22]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Conclusion
----------

In this tutorial you have learned how power analysis can tell you the
operations being performed on a microcontroller. In future work we will
move towards using this for breaking various forms of security on
devices. In particular, Tutorial B3-1 will examine how we can use this
information to exploit a password check.
