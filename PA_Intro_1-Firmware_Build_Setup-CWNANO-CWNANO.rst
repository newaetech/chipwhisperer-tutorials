
Firmware Build Setup
====================

Supported setups:

SCOPES:

-  OPENADC
-  CWNANO

PLATFORMS:

-  CWLITEARM
-  CWLITEXMEGA
-  CWNANO

This tutorial will introduce you to the software side of ChipWhisperer,
including the tutorials themselves. It will also show you how to perform
different operations on data based on input from the ChipWhisperer
software. This can be used for building your own system which you wish
to 'break'. All the ``%%bash`` blocks can be run either in Jupyter or in
your favourite command line environment (note that Jupyter resets your
path between blocks).

If you haven't run through ``!!Introduction_to_Jupyter!!.ipynb`` do that
now.

Assuming you've done that, we can get started on the tutorial.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'CWNANO'
    PLATFORM = 'CWNANO'

What is SimpleSerial
--------------------

SimpleSerial is the communications protocol used for almost all of the
ChipWhisperer demo project. It's a very basic serial protocol which can
be easily implemented on most systems. This system communicates using a
standard asyncronous serial protocol, 38400 baud, 8-N-1.

All messages are sent in ASCII-text, and are normally terminated with a
line-feed (``'\n'``). This allows you to interact with the simpleserial
system over a standard terminal emulator.

``x``

    Sending a 'x' resets the buffers. This does not require a line-feed
    termination. It is suggested to always send a stream of x's to
    initilize the system in case the device was already in some other
    mode due to noise/corruption.

``k00112233445566778899AABBCCDDEEFF\n``

    Loads the encryption key ``00112233445566778899AABBCCDDEEFF`` into
    the system. If not called the system may use some default key.

``pAABBCCDDEEFF00112233445566778899\n``

    Encrypts the data ``AABBCCDDEEFF00112233445566778899`` with the key
    loaded with the 'k' command. The system will respond with a string
    starting with r, as shown next.

``rCBBD4A2B34F2571758FF6A797E09859D\n``

    This is the response from the system. If data has been encrypted
    with a 'p' for example, the system will respond with the 'r'
    sequence automatically. So sending the earlier example means the
    result of the encryption was ``cbbd4a2b34f2571758ff6a797e09859d``.

Building the Basic Example
--------------------------

To bulid the basic example, you'll need an appropriate compiler for your
target. For the ChipWhisperer Lite/Xmega platform, you'll need
``avr-gcc`` and ``avr-libc``, while if you're using an ARM target (like
the ChipWhisperer Lite/STM32 platform), your need the GNU Toolchain for
ARM devices. If you're using a target with a different architecture,
you'll need to install the relevant compiler. If you're unsure, you can
run the block below. If you've got the right stuff installed, you should
see some version and copyright info printed for the relevant compiler:


**In [2]:**

.. code:: bash

    %%bash
    #check for avr-gcc
    avr-gcc --version
    
    #check for ARM gcc
    arm-none-eabi-gcc --version


**Out [2]:**



.. parsed-literal::

    avr-gcc.exe (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    arm-none-eabi-gcc.exe (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    



Now that you have the relevant toolchain installed, you should be able
to build firmware for your desired platform. We'll begin by creating a
new project based on simpleserial-base by making a new firmware and
copying the files from the project we want to work on:


**In [3]:**

.. code:: bash

    %%bash
    cd ../hardware/victims/firmware/
    mkdir -p simpleserial-base-lab1 && cp -r simpleserial-base/* $_
    cd simpleserial-base-lab1

Next we'll build the firmware. You'll need to specify the ``PLATFORM``
and ``CRYPTO_TARGET`` for your target. To save you from having to
re-enter this info in every make block, you can edit the python below
with your platform and crypto\_target.

Common platforms are CWLITEXMEGA and CWLITEARM. To see a list of
platforms leave ``PLATFORM`` as is.

This tutorial doesn't use any crypto, so we can leave ``CRYPTO_TARGET``
as ``NONE``.


**In [4]:**

.. code:: ipython3

    CRYPTO_TARGET = "NONE"

Provided you completed the fields above, you should be able to
successfully run the block below.


**In [5]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab1
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [5]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWNANO.hex
    rm -f -- simpleserial-base-CWNANO.eep
    rm -f -- simpleserial-base-CWNANO.cof
    rm -f -- simpleserial-base-CWNANO.elf
    rm -f -- simpleserial-base-CWNANO.map
    rm -f -- simpleserial-base-CWNANO.sym
    rm -f -- simpleserial-base-CWNANO.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f0_hal_nano.s stm32f0_hal_lowlevel.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f0_hal_nano.d stm32f0_hal_lowlevel.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f0_hal_nano.i stm32f0_hal_lowlevel.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f0_nano/stm32f0_hal_nano.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_nano.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_nano.o.d .././hal/stm32f0_nano/stm32f0_hal_nano.c -o objdir/stm32f0_hal_nano.o 
    .
    Compiling C: .././hal/stm32f0/stm32f0_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_lowlevel.o.d .././hal/stm32f0/stm32f0_hal_lowlevel.c -o objdir/stm32f0_hal_lowlevel.o 
    .
    Assembling: .././hal/stm32f0/stm32f0_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f0_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ .././hal/stm32f0/stm32f0_startup.S -o objdir/stm32f0_startup.o
    .
    Linking: simpleserial-base-CWNANO.elf
    arm-none-eabi-gcc -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWNANO.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f0_hal_nano.o objdir/stm32f0_hal_lowlevel.o objdir/stm32f0_startup.o --output simpleserial-base-CWNANO.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f0_nano/LinkerScript.ld -Wl,--gc-sections -lm -mthumb -mcpu=cortex-m0  -Wl,-Map=simpleserial-base-CWNANO.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWNANO.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWNANO.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep \|\| exit 0
    .
    Creating Extended Listing: simpleserial-base-CWNANO.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWNANO.elf > simpleserial-base-CWNANO.lss
    .
    Creating Symbol Table: simpleserial-base-CWNANO.sym
    arm-none-eabi-nm -n simpleserial-base-CWNANO.elf > simpleserial-base-CWNANO.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4324	     12	   1292	   5628	   15fc	simpleserial-base-CWNANO.elf
    +--------------------------------------------------------
    + Built for platform CWNANO STM32F030
    +--------------------------------------------------------



Modifying the Basic Example
---------------------------

At this point we want to modify the system to perform 'something' with
the data, such that we can confirm the system is working. To do so, open
the file ``simpleserial-base.c`` in the simpleserial-base-lab1 folder
with a code editor such as Programmer's Notepad (which ships with
WinAVR).

Find the following code block towards the end of the file:

.. code:: c

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

Now modify it to increment the value of each data byte:

.. code:: c

    /**********************************
     * Start user-specific code here. */
    trigger_high();

    //16 hex bytes held in 'pt' were sent
    //from the computer. Store your response
    //back into 'pt', which will send 16 bytes
    //back to computer. Can ignore of course if
    //not needed

    for(int i = 0; i < 16; i++){
        pt[i]++;
    }

    trigger_low();
    /* End user-specific code here. *
     ********************************/

Then rebuild the file with ``make``:


**In [6]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab1
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [6]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWNANO.hex
    rm -f -- simpleserial-base-CWNANO.eep
    rm -f -- simpleserial-base-CWNANO.cof
    rm -f -- simpleserial-base-CWNANO.elf
    rm -f -- simpleserial-base-CWNANO.map
    rm -f -- simpleserial-base-CWNANO.sym
    rm -f -- simpleserial-base-CWNANO.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s stm32f0_hal_nano.s stm32f0_hal_lowlevel.s
    rm -f -- simpleserial-base.d simpleserial.d stm32f0_hal_nano.d stm32f0_hal_lowlevel.d
    rm -f -- simpleserial-base.i simpleserial.i stm32f0_hal_nano.i stm32f0_hal_lowlevel.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f0_nano/stm32f0_hal_nano.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_nano.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_nano.o.d .././hal/stm32f0_nano/stm32f0_hal_nano.c -o objdir/stm32f0_hal_nano.o 
    .
    Compiling C: .././hal/stm32f0/stm32f0_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_lowlevel.o.d .././hal/stm32f0/stm32f0_hal_lowlevel.c -o objdir/stm32f0_hal_lowlevel.o 
    .
    Assembling: .././hal/stm32f0/stm32f0_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f0_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ .././hal/stm32f0/stm32f0_startup.S -o objdir/stm32f0_startup.o
    .
    Linking: simpleserial-base-CWNANO.elf
    arm-none-eabi-gcc -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWNANO.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/stm32f0_hal_nano.o objdir/stm32f0_hal_lowlevel.o objdir/stm32f0_startup.o --output simpleserial-base-CWNANO.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f0_nano/LinkerScript.ld -Wl,--gc-sections -lm -mthumb -mcpu=cortex-m0  -Wl,-Map=simpleserial-base-CWNANO.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWNANO.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWNANO.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep \|\| exit 0
    .
    Creating Extended Listing: simpleserial-base-CWNANO.lss
    arm-none-eabi-objdump -h -S -z simpleserial-base-CWNANO.elf > simpleserial-base-CWNANO.lss
    .
    Creating Symbol Table: simpleserial-base-CWNANO.sym
    arm-none-eabi-nm -n simpleserial-base-CWNANO.elf > simpleserial-base-CWNANO.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4324	     12	   1292	   5628	   15fc	simpleserial-base-CWNANO.elf
    +--------------------------------------------------------
    + Built for platform CWNANO STM32F030
    +--------------------------------------------------------



Python Script
-------------

We'll end by uploading the firmware onto the target and communicating
with it via a python script. Depending on your target, uploading
firmware will be different. For the XMega and STM32 targets, you can use
ChipWhisperer's interface. Otherwise, you'll likely need to use and
external programmer. If you have a CW1173/Xmega board, you can run the
following blocks without modification. After running the final block,
you should see two sets of hexadecimal numbers, with the second having
values one higher than the first.

We'll begin by importing the ChipWhisperer module. This will allow us to
connect to and communicate with the ChipWhisperer hardware. The
ChipWhisperer module also includes analysis software, which we'll be
looking at in later tutorials.


**In [7]:**

.. code:: ipython3

    import chipwhisperer as cw

Documentation is available on
`ReadtheDocs <https://chipwhisperer.readthedocs.io/en/latest/api.html>`__
or by calling ``help()`` on the module, submodule, function, etc.:


**In [8]:**

.. code:: ipython3

    help(cw)


**Out [8]:**



.. parsed-literal::

    Help on package chipwhisperer:
    
    NAME
        chipwhisperer
    
    DESCRIPTION
        .. module:: chipwhisperer
           :platform: Unix, Windows
           :synopsis: Test
        
        .. moduleauthor:: NewAE Technology Inc.
        
        Main module for ChipWhisperer.
    
    PACKAGE CONTENTS
        analyzer (package)
        capture (package)
        common (package)
        hardware (package)
    
    SUBMODULES
        key_text_patterns
        ktp
        programmers
        project
        scopes
        targets
        util
    
    FUNCTIONS
        captureTrace(scope, target, plaintext, key=None)
            Deprecated: Use capture_trace instead.
        
        capture_trace(scope, target, plaintext, key=None)
            Capture a trace, sending plaintext and key
            
            Does all individual steps needed to capture a trace (arming the scope
            sending the key/plaintext, getting the trace data back, etc.)
            
            Args:
                scope (ScopeTemplate): Scope object to use for capture.
                target (TargetTemplate): Target object to read/write text from.
                plaintext (bytearray): Plaintext to send to the target. Should be
                    unencoded bytearray (will be converted to SimpleSerial when it's
                    sent). If None, don't send plaintext.
                key (bytearray, optional): Key to send to target. Should be unencoded
                    bytearray. If None, don't send key. Defaults to None.
            
            Returns:
                :class:`Trace <chipwhisperer.common.traces.Trace>` or None if capture
                timed out.
            
            Raises:
                Warning or OSError: Error during capture.
            
            Example:
                Capturing a trace::
            
                    import chipwhisperer as cw
                    scope = cw.scope()
                    scope.default_setup()
                    target = cw.target()
                    ktp = cw.ktp.Basic()
                    key, pt = ktp.new_pair()
                    trace = cw.capture_trace(scope, target, pt, key)
            
            .. versionadded:: 5.1
                Added to simplify trace capture.
        
        createProject(filename, overwrite=False)
            Deprecated: Use create_project instead.
        
        create_project(filename, overwrite=False)
            Create a new project with the path <filename>.
            
            If <overwrite> is False, raise an OSError if this path already exists.
            
            Args:
               filename (str): File path to create project file at. Must end with .cwp
               overwrite (bool, optional): Whether or not to overwrite an existing
                   project with <filename>. Raises an OSError if path already exists
                   and this is false. Defaults to false.
            
            Returns:
               A chipwhisperer project object.
            
            Raises:
               OSError: filename exists and overwrite is False.
        
        import_project(filename, file_type='zip', overwrite=False)
            Import and open a project.
            
            Will import the \*\*filename\*\* by extracting to the current working
            directory.
            
            Currently support file types:
             \* zip
            
            Args:
                filename (str): The file name to import.
                file_type (str): The type of file that is being imported.
                    Default is zip.
                overwrite (bool): Whether or not to overwrite the project given as
                    the \*\*import_as\*\* project.
            
            .. versionadded:: 5.1
                Add \*\*import_project\*\* function.
        
        openProject(filename)
            Deprecated: Use open_project instead.
        
        open_project(filename)
            Load an existing project from disk.
            
            Args:
               filename (str): Path to project file.
            
            Returns:
               A chipwhisperer project object.
            
            Raises:
               OSError: filename does not exist.
        
        programTarget(scope, prog_type, fw_path, \*\*kwargs)
            Deprecated: Use program_target instead.
        
        program_target(scope, prog_type, fw_path, \*\*kwargs)
            Program the target using the programmer <type>
            
            Programmers can be found in the programmers submodule
            
            Args:
               scope (ScopeTemplate): Connected scope object to use for programming
               prog_type (Programmer): Programmer to use. See chipwhisperer.programmers
                   for available programmers
               fw_path (str): Path to hex file to program
            
            .. versionadded:: 5.0.1
                Simplified programming target
        
        scope(scope_type=None, sn=None)
            Create a scope object and connect to it.
            
            This function allows any type of scope to be created. By default, the
            object created is based on the attached hardware (OpenADC for
            CWLite/CW1200, CWNano for CWNano).
            
            Scope Types:
             \* :class:`scopes.OpenADC` (Pro and Lite)
             \* :class:`scopes.CWNano` (Nano)
            
            If multiple chipwhisperers are connected, the serial number of the one you
            want to connect to can be specified by passing sn=<SERIAL_NUMBER>
            
            Args:
               scope_type (ScopeTemplate, optional): Scope type to connect to. Types
                   can be found in chipwhisperer.scopes. If None, will try to detect
                   the type of ChipWhisperer connected. Defaults to None.
               sn (str, optional): Serial number of ChipWhisperer that you want to
                   connect to. Required if more than one ChipWhisperer
                   of the same type is connected (i.e. two CWNano's or a CWLite and
                   CWPro). Defaults to None.
            
            Returns:
                Connected scope object.
            
            Raises:
                OSError: Can be raised for issues connecting to the chipwhisperer, such
                    as not having permission to access the USB device or no ChipWhisperer
                    being connected.
                Warning: Raised if multiple chipwhisperers are connected, but the type
                    and/or the serial numbers are not specified
            
            .. versionchanged:: 5.1
                Added autodetection of scope_type
        
        target(scope, target_type=<class 'chipwhisperer.capture.targets.SimpleSerial.SimpleSerial'>, \*\*kwargs)
            Create a target object and connect to it.
            
            Args:
               scope (ScopeTemplate): Scope object that we're connecting to the target
                   through.
               target_type (TargetTemplate, optional): Target type to connect to.
                   Defaults to targets.SimpleSerial. Types can be found in
                   chipwhisperer.targets.
               \*\*kwargs: Additional keyword arguments to pass to target setup. Rarely
                   needed.
            
            Returns:
                Connected target object specified by target_type.
    
    FILE
        c:\users\user\code\term3\chipwhisperer\software\chipwhisperer\__init__.py
    
    
    


Next we'll need to connect to the scope end of the hardware. Starting
with ChipWhisperer 5.1, ``cw.scope`` will attempt to autodetect which
scope type you have (though if you have multiple ChipWhisperers
connected, you'll need to specify the serial number). If you'd like, you
can still specify the scope type.


**In [9]:**

.. code:: ipython3

    scope = cw.scope()


**In [10]:**

.. code:: ipython3

    help(scope)


**Out [10]:**



.. parsed-literal::

    Help on CWNano in module chipwhisperer.capture.scopes.cwnano object:
    
    class CWNano(chipwhisperer.capture.scopes.base.ScopeTemplate, chipwhisperer.common.utils.util.DisableNewAttr)
     \|  CWNano scope object.
     \|  
     \|  This class contains the public API for the CWNano hardware. It includes
     \|  specific settings for each of these devices.
     \|  
     \|  To connect to one of these devices, the easiest method is::
     \|  
     \|      import chipwhisperer as cw
     \|      scope = cw.scope(type=scopes.CWNano)
     \|  
     \|  Some sane default settings can be set using::
     \|  
     \|      scope.default_setup()
     \|  
     \|  For more help about scope settings, try help() on each of the ChipWhisperer
     \|  scope submodules (scope.adc, scope.io, scope.glitch):
     \|  
     \|    \* :attr:`scope.adc <.CWNano.adc>`
     \|    \* :attr:`scope.io <.CWNano.io>`
     \|    \* :attr:`scope.glitch <.CWNano.glitch>`
     \|    \* :meth:`scope.default_setup <.CWNano.default_setup>`
     \|    \* :meth:`scope.con <.CWNano.con>`
     \|    \* :meth:`scope.dis <.CWNano.dis>`
     \|    \* :meth:`scope.get_last_trace <.CWNano.get_last_trace>`
     \|    \* :meth:`scope.arm <.CWNano.arm>`
     \|    \* :meth:`scope.capture <.CWNano.capture>`
     \|  
     \|  Method resolution order:
     \|      CWNano
     \|      chipwhisperer.capture.scopes.base.ScopeTemplate
     \|      chipwhisperer.common.utils.util.DisableNewAttr
     \|      builtins.object
     \|  
     \|  Methods defined here:
     \|  
     \|  __init__(self)
     \|      Initialize self.  See help(type(self)) for accurate signature.
     \|  
     \|  __repr__(self)
     \|      Return repr(self).
     \|  
     \|  __str__(self)
     \|      Return str(self).
     \|  
     \|  arm(self)
     \|      Arm the ADC, the trigger will be GPIO4 rising edge (fixed trigger).
     \|  
     \|  capture(self)
     \|      Raises IOError if unknown failure, returns 'True' if timeout, 'False' if no timeout
     \|  
     \|  default_setup(self)
     \|      Sets up sane capture defaults for this scope
     \|      
     \|        \* 7.5MHz ADC clock
     \|        \* 7.5MHz output clock
     \|        \* 5000 capture samples
     \|        \* tio1 = serial rx
     \|        \* tio2 = serial tx
     \|        \* glitch module off
     \|      
     \|      .. versionadded:: 5.1
     \|          Added default setup for CWNano
     \|  
     \|  getCurrentScope(self)
     \|  
     \|  getLastTrace(self)
     \|      Deprecated: Use get_last_trace instead.
     \|  
     \|  get_last_trace(self)
     \|      Return the last trace captured with this scope.
     \|  
     \|  get_possible_devices(self, idProduct)
     \|  
     \|  usbdev(self)
     \|  
     \|  ----------------------------------------------------------------------
     \|  Data and other attributes defined here:
     \|  
     \|  REQ_ARM = 41
     \|  
     \|  REQ_SAMPLES = 42
     \|  
     \|  ----------------------------------------------------------------------
     \|  Methods inherited from chipwhisperer.capture.scopes.base.ScopeTemplate:
     \|  
     \|  con(self, sn=None)
     \|  
     \|  dcmTimeout(self)
     \|  
     \|  dis(self)
     \|  
     \|  getName(self)
     \|      Deprecated: Use get_name instead.
     \|  
     \|  getStatus(self)
     \|  
     \|  get_name(self)
     \|  
     \|  newDataReceived(self, channelNum, data=None, offset=0, sampleRate=0)
     \|  
     \|  setAutorefreshDCM(self, enabled)
     \|  
     \|  setCurrentScope(self, scope)
     \|  
     \|  ----------------------------------------------------------------------
     \|  Data descriptors inherited from chipwhisperer.capture.scopes.base.ScopeTemplate:
     \|  
     \|  __dict__
     \|      dictionary for instance variables (if defined)
     \|  
     \|  __weakref__
     \|      list of weak references to the object (if defined)
     \|  
     \|  ----------------------------------------------------------------------
     \|  Methods inherited from chipwhisperer.common.utils.util.DisableNewAttr:
     \|  
     \|  __setattr__(self, name, value)
     \|      Implement setattr(self, name, value).
     \|  
     \|  disable_newattr(self)
     \|  
     \|  enable_newattr(self)
    
    


We'll also need to setup the interface to the target (typically what we
want to attack). Like with scopes, there's a few different interfaces we
can use, which are available through ``scope.targets.<target_type>``.
The default, SimpleSerial, communicates over UART and is almost always
the correct choice.


**In [11]:**

.. code:: ipython3

    target = cw.target(scope, cw.targets.SimpleSerial)

Next, we'll do some basic setup. Most of these settings don't matter for
now, but take note of the ``scope.clock`` and ``scope.io``, which setup
the clock and serial io lines, which needs to be done before programming
the target.

**Some targets require settings different than what's below. Check the
relevant wiki article for your target for more information**


**In [12]:**

.. code:: ipython3

    # setup scope parameters
    if SCOPETYPE == "OPENADC":
        scope.gain.db = 45
        scope.adc.samples = 3000
        scope.adc.offset = 1250
        scope.adc.basic_mode = "rising_edge"
        scope.clock.clkgen_freq = 7370000
        scope.clock.adc_src = "clkgen_x4"
        scope.trigger.triggers = "tio4"
        scope.io.tio1 = "serial_rx"
        scope.io.tio2 = "serial_tx"
        scope.io.hs2 = "clkgen"
    elif SCOPETYPE == "CWNANO":
        scope.io.clkout = 7370000
        scope.adc.clk_freq = 7370000
        scope.io.tio1 = "serial_rx"
        scope.io.tio2 = "serial_tx"

Or, more simply:


**In [13]:**

.. code:: ipython3

    scope.default_setup()

Now that the clock and IO lines are setup, we can program the target.
ChipWhisperer includes a generic programming function,
``cw.program_target(scope, type, fw_path)``. Here ``type`` is one of the
programmers available in the cw.programmers submodule
(``help(cw.programmers)`` for more information). ``fw_path`` is the path
to the hex file that you want to flash onto the device.

The final part of the binary path should match your platform
(``<path>/simpleserial-base-CWLITEARM.hex`` for CWLITEARM)


**In [14]:**

.. code:: ipython3

    if "STM" in PLATFORM or PLATFORM == "CWLITEARM" or PLATFORM == "CWNANO":
        prog = cw.programmers.STM32FProgrammer
    elif PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
        prog = cw.programmers.XMEGAProgrammer
    else:
        prog = None
        
    fw_path = '../hardware/victims/firmware/simpleserial-base-lab1/simpleserial-base-{}.hex'.format(PLATFORM)

And finally actually programming the device:


**In [15]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [15]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x445
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4335 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4335 bytes
    


Finally, we'll load some text, send it to the target, and read it back.
We also capture a trace here, but don't do anything with it yet (that
will come in later tutorials). You should see your original text with
the received text below it.


**In [16]:**

.. code:: ipython3

    ktp = cw.ktp.Basic() # object to generate fixed/random key and text (default fixed key, random text)
    key, text = ktp.next()  # get our fixed key and random text
    
    target.simpleserial_write('k', key)
    target.simpleserial_wait_ack()
    scope.arm()
    
    target.simpleserial_write('p', text)
        
    ret = scope.capture()
    trace = scope.get_last_trace()
    output = target.simpleserial_read('r', 16)
    
    from binascii import hexlify
    print(hexlify(output))
    print(hexlify(text))


**Out [16]:**



.. parsed-literal::

    b'cd408f8596792aecc60d57bd6b95aa8d'
    b'cd408f8596792aecc60d57bd6b95aa8d'
    


You can also just run:


**In [17]:**

.. code:: ipython3

    ret = cw.capture_trace(scope, target, text, key)
    if ret:
        trace = ret
        print(hexlify(ret.textout))
        print(hexlify(text))


**Out [17]:**



.. parsed-literal::

    b'cd408f8596792aecc60d57bd6b95aa8d'
    b'cd408f8596792aecc60d57bd6b95aa8d'
    


Now that we're done with this tutorial, we'll need to disconnect from
the ChipWhisperer. This will prevent this session from interferening
from later ones (most notably with a ``USB can't claim interface``
error). Don't worry if you forget, unplugging and replugging the
ChipWhipserer should fix it.


**In [18]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Future Tutorials
----------------

The next tutorials that you run will start using helper scripts to make
setup a little faster and more consistent between tutorials. Those
scripts run mostly the same setup code that we did here, but if you'd
like to see exactly what they're doing, they're all included in the
``Helper_Scripts`` folder.

For example, the scope setup (gain, clock, etc) is taken care of by
``Helper Scripts/Setup_Generic.ipynb``.
