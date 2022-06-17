Part 2, Topic 1: Introduction to Voltage Glitching (MAIN)
=========================================================



**SUMMARY:** *Similarly to clock glitching, inserting brief glitches
into the power line of an embedded device can result in skipped
instructions and corrupted results. Besides providing a more reliable
glitch on some targets when compared to clock glitching, voltage
glitching also has the advanatage that the Vcc pins on chips are always
accessable. This won’t be covered in this course, but it can also be
used to glitch a device asynchronous to its clock.*

**LEARNING OUTCOMES:**

-  Understanding voltage glitch settings
-  Building a voltage glitch and crash map.
-  Modifying glitch circuit to increase glitch success

Voltage Glitch Hardware
-----------------------

The ChipWhisperer uses the same hardware block for both voltage and
clock glitching, with the only difference being where the glitch output
is routed to. Instead of routing to HS2, voltage glitching is performed
by routing the glitch to either the ``glitch_hp`` transistor or the
``glitch_lp`` transistor. This can be done via the following API calls:

.. code:: python

   scope.io.glitch_hp = True #enable HP glitch
   scope.io.glitch_hp = False #disable HP glitch
   scope.io.glitch_lp = True #enable LP glitch
   scope.io.glitch_lp = False #disable LP glitch

While the hardware block are the same, you’ll need to change how it’s
configued. You wouldn’t want to try routing ``"clock_xor"`` to the
glitch transistor and oscillate Vcc like the device’s clock! Instead,
the following two output settings are best suited to voltage glitching:

1. ``"glitch_only"`` - insert a glitch for a portion of a clock cycle
   based on ``scope.glitch.width`` and ``scope.glitch.offset``
2. ``"enable_only"`` - insert a glitch for an entire clock cycle

Typically, the ``"enable_only"`` setting will be too powerful for most
devices. One situation where it outshines ``"glitch_only"`` is in
glitching asychronous to the target’s clock. An example of this is
glitching a target with an internal clock. In this case, the
ChipWhisperer’s clock can be boosted far above the target’s to insert a
precise glitch, with ``repeat`` functioning as ``width`` and
``ext_offset`` functioning as ``offset``.

Voltage Glitching vs. Clock Glitching
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Voltage glitching has some obvious benefits over clock glitching, such
as working for a wider varitey of targets, but its downsides are less
obvious. One of the biggest is how much it depends on the actual glitch
circuit itself. With clock glitching, it’s relatively easy to insert a
glitch - there’s nothing external trying to keep the clock at a certain
voltage level. This is very different for a target’s power pins. When we
try to drop the power pin to ground, there’s a lot of stuff fighting us
to keep the power pin at the correct voltage, such as decoupling
capacitors, bulk supply capacitors, and the voltage regulator supplying
the voltage. This means when we make small changes to the glitch
circuit, the glitch settings and even our ability to insert a glitch at
all completely change! Consider glitching a target on the CW308 UFO
board. If you switch your coaxial cable length from 20cm to 40 cm,
you’ll need to find entirely new glitch settings to repeat the attack
(if it’s still even possible). This is quite easy to see on an
oscilloscope or using the ChipWhisperer’s ADC: longer cables and lower
valued shunt resistors will make the glitch less sharp and increase
ringing.

While your first thought might be to go for as sharp a glitch as
possible, this often won’t result in a high glitch success rate. If
you’re unable to find any working glitches with your current setup, it
might be worth changing you hardware setup a bit. For example, on the
ChipWhisperer Lite 1 part, you can desolder SJ5 and solder header pins
to JP6. Even just connecting these pins with a jumper will have
different glitch behaviour than with a soldered SJ5.

You can refer to the training slides for more information about finding
good glitch settings, as well as more on the theory side of voltage
glitching.

The Lab
~~~~~~~

To introduce you to volatge glitching and find some settings, we’re
going to walk back through the clock glitching loop lab. You may want to
capture some power traces while you’re first experimenting with glitches
to see what effects different glitch widths have on the power trace.
Another thing to keep in mind is that targets often won’t tolerate the
Vcc pin dropping for an extended period of time without crashing - once
you see the target start to crash, you won’t see much else with larger
widths.

One thing you might have to change is the glitch repeat value. Depending
on how wide your glitch is, the voltage at the power pin may not recover
by the time the next glitch is inserted. This can have to effect of
increasing subsequent glitches’ strength, which may or may not be
desirable. Since glitches inserted with repeat > 1 have different
strength, it’s a good idea to scan through ext_offset as well.

Higher Frequency Glitching
~~~~~~~~~~~~~~~~~~~~~~~~~~

The XMEGA target, and to a lesser extent the STM32F3, is very difficult
to glitch with the default ChipWhisperer settings. Try bumping the clock
frequency to 24MHz for the STM32 or 32MHz for the XMEGA and use a repeat
5-10 with both the high power and low power glitches active. You’ll need
to adjust the baud rate by the same proportion as the clock.

Another setup that seems to work with the XMEGA is SJ5 unsoldered, JP6
jumpered, high+low power glitch, 32MHz, and repeat=5.


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
        time.sleep(0.1)
        def reboot_flush():            
            scope.io.nrst = False
            time.sleep(0.05)
            scope.io.nrst = "high_z"
            time.sleep(0.05)
            #Flush garbage too
            target.flush()


**Out [5]:**



.. parsed-literal::

    Serial baud rate = 166729.98643147896
    



**In [6]:**

.. code:: ipython3

    reboot_flush()
    scope.arm()
    target.write("g\n")
    scope.capture()
    val = target.simpleserial_read_witherrors('r', 4, glitch_timeout=10)#For loop check
    valid = val['valid']
    if valid:
        response = val['payload']
        raw_serial = val['full_response']
        error_code = val['rv']
    
    print(val)


**Out [6]:**



.. parsed-literal::

    {'valid': True, 'payload': CWbytearray(b'c4 09 00 00'), 'full\_response': 'rC4090000\n', 'rv': 0}
    



**In [7]:**

.. code:: ipython3

    import chipwhisperer.common.results.glitch as glitch
    gc = glitch.GlitchController(groups=["success", "reset", "normal"], parameters=["width", "offset", "ext_offset"])
    gc.display_stats()


**Out [7]:**














**In [8]:**

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

Some tips for finding good glitches:

1. There’s a lot of stuff fighting our glitch this time - unlike the
   clock line, the Vcc rail isn’t supposed to oscillate! As such shorter
   glitches will have no effect. A good strategy can often to be to
   increase the minimum glitch width until you start seeing consistant
   crashes, then backing off on the width.
2. The repeat parameter behaves very differently than with voltage
   glitching - at the boosted clock rate, the Vcc often won’t recover
   before the next glitch. Try different repeat values as well.
3. We’ve built in a success/reset measurement into the glitch loop. Once
   you’ve found some glitch spots, this will help you evaluate which
   ones are best for your target.


**In [9]:**

.. code:: ipython3

    from importlib import reload
    import chipwhisperer.common.results.glitch as glitch
    from tqdm.notebook import trange
    import struct
    
    g_step = 0.4
    if PLATFORM=="CWLITEXMEGA":
        gc.set_range("width", 45.7, 47.8)
        gc.set_range("offset", 2.8, 10)
        gc.set_range("ext_offset", 2, 4)
        scope.glitch.repeat = 10
    elif PLATFORM == "CWLITEARM":
        #should also work for the bootloader memory dump
        gc.set_range("width", 34.7, 36)
        gc.set_range("offset", -41, -30)
        gc.set_range("ext_offset", 6, 6)
        scope.glitch.repeat = 7
    elif PLATFORM == "CW308_STM32F3":
        #these specific settings seem to work well for some reason
        #also works for the bootloader memory dump
        gc.set_range("ext_offset", 9, 12)
        gc.set_range("width", 47.6, 49.6)
        gc.set_range("offset", -19, -21.5)
        scope.glitch.repeat = 5
    
    gc.set_global_step(g_step)
    
    scope.adc.timeout = 0.1
    
    reboot_flush()
    sample_size = 1
    loff = scope.glitch.offset
    lwid = scope.glitch.width
    total_successes = 0
    for glitch_setting in gc.glitch_values():
        scope.glitch.offset = glitch_setting[1]
        scope.glitch.width = glitch_setting[0]
        scope.glitch.ext_offset = glitch_setting[2]
        #print(scope.glitch.ext_offset)
        successes = 0
        resets = 0
        for i in range(10):
            target.flush()
            if scope.adc.state:
                # can detect crash here (fast) before timing out (slow)
                print("Trigger still high!")
                gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
    
                #Device is slow to boot?
                reboot_flush()
                resets += 1
                
            scope.arm()
            
            #Do glitch loop
            target.write("g\n")
            
            ret = scope.capture()
            
            
            val = target.simpleserial_read_witherrors('r', 4, glitch_timeout=10)#For loop check
            
            scope.io.glitch_hp = False
            scope.io.glitch_hp = True
            scope.io.glitch_lp = False
            scope.io.glitch_lp = True
            if ret:
                print('Timeout - no trigger')
                gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                resets += 1
    
                #Device is slow to boot?
                reboot_flush()
    
            else:
                if val['valid'] is False:
                    gc.add("reset", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                    reboot_flush()
                    resets += 1
                else:
                    gcnt = struct.unpack("<I", val['payload'])[0]
                    
                    if gcnt != 2500: #for loop check
                        gc.add("success", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
                        successes += 1
                    else:
                        gc.add("normal", (scope.glitch.width, scope.glitch.offset, scope.glitch.ext_offset))
        if successes > 0:                
            print("successes = {}, resets = {}, offset = {}, width = {}, ext_offset = {}".format(successes, resets, scope.glitch.offset, scope.glitch.width, scope.glitch.ext_offset))
            total_successes += successes
    print("Done glitching")


**Out [9]:**



.. parsed-literal::

    successes = 1, resets = 0, offset = 2.734375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 3.90625, width = 45.703125, ext\_offset = 2
    successes = 3, resets = 0, offset = 4.6875, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 4.6875, width = 45.703125, ext\_offset = 2
    successes = 2, resets = 0, offset = 5.078125, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 5.078125, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 5.46875, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 5.859375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 5.859375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 5.859375, width = 45.703125, ext\_offset = 3
    successes = 1, resets = 0, offset = 5.859375, width = 45.703125, ext\_offset = 3
    successes = 6, resets = 0, offset = 6.25, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 6.25, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 7.03125, width = 45.703125, ext\_offset = 2
    successes = 2, resets = 0, offset = 7.03125, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 7.421875, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 8.59375, width = 45.703125, ext\_offset = 2
    successes = 2, resets = 0, offset = 8.984375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 8.984375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 8.984375, width = 45.703125, ext\_offset = 2
    successes = 1, resets = 0, offset = 9.765625, width = 45.703125, ext\_offset = 2
    successes = 8, resets = 0, offset = 2.734375, width = 46.09375, ext\_offset = 2
    successes = 4, resets = 1, offset = 2.734375, width = 46.09375, ext\_offset = 2
    successes = 5, resets = 0, offset = 2.734375, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 2, offset = 2.734375, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 0, offset = 2.734375, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 0, offset = 2.734375, width = 46.09375, ext\_offset = 3
    successes = 7, resets = 0, offset = 3.125, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 1, offset = 3.125, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 1, offset = 3.125, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 2, offset = 3.125, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 0, offset = 3.125, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 2, offset = 3.125, width = 46.09375, ext\_offset = 3
    successes = 3, resets = 1, offset = 3.515625, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 0, offset = 3.515625, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 2, offset = 3.515625, width = 46.09375, ext\_offset = 2
    successes = 2, resets = 3, offset = 3.515625, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 2, offset = 3.515625, width = 46.09375, ext\_offset = 3
    successes = 6, resets = 0, offset = 3.90625, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 0, offset = 3.90625, width = 46.09375, ext\_offset = 2
    successes = 6, resets = 0, offset = 3.90625, width = 46.09375, ext\_offset = 2
    successes = 2, resets = 1, offset = 3.90625, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 0, offset = 3.90625, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 2, offset = 3.90625, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 1, offset = 4.296875, width = 46.09375, ext\_offset = 2
    successes = 8, resets = 1, offset = 4.296875, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 1, offset = 4.296875, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 0, offset = 4.296875, width = 46.09375, ext\_offset = 3
    successes = 6, resets = 1, offset = 4.6875, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 0, offset = 4.6875, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 3, offset = 4.6875, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 1, offset = 4.6875, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 1, offset = 4.6875, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 2, offset = 4.6875, width = 46.09375, ext\_offset = 3
    successes = 8, resets = 2, offset = 5.078125, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 3, offset = 5.078125, width = 46.09375, ext\_offset = 2
    Timeout - no trigger
    successes = 5, resets = 1, offset = 5.078125, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 1, offset = 5.078125, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 0, offset = 5.078125, width = 46.09375, ext\_offset = 3
    successes = 3, resets = 3, offset = 5.078125, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 7, resets = 1, offset = 5.46875, width = 46.09375, ext\_offset = 2
    successes = 7, resets = 0, offset = 5.46875, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 8, resets = 1, offset = 5.46875, width = 46.09375, ext\_offset = 2
    successes = 4, resets = 2, offset = 5.46875, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 0, offset = 5.46875, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 4, resets = 2, offset = 5.859375, width = 46.09375, ext\_offset = 2
    Timeout - no trigger
    successes = 5, resets = 1, offset = 5.859375, width = 46.09375, ext\_offset = 2
    successes = 7, resets = 1, offset = 5.859375, width = 46.09375, ext\_offset = 2
    successes = 2, resets = 1, offset = 5.859375, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 2, offset = 5.859375, width = 46.09375, ext\_offset = 3
    successes = 3, resets = 0, offset = 5.859375, width = 46.09375, ext\_offset = 3
    successes = 7, resets = 1, offset = 6.25, width = 46.09375, ext\_offset = 2
    successes = 6, resets = 0, offset = 6.25, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 1, offset = 6.25, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 1, offset = 6.25, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 1, offset = 6.25, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 2, offset = 6.640625, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 0, offset = 6.640625, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 7, resets = 2, offset = 6.640625, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 0, offset = 6.640625, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 0, offset = 6.640625, width = 46.09375, ext\_offset = 3
    successes = 8, resets = 0, offset = 7.03125, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 0, offset = 7.03125, width = 46.09375, ext\_offset = 2
    successes = 6, resets = 1, offset = 7.03125, width = 46.09375, ext\_offset = 2
    successes = 5, resets = 3, offset = 7.03125, width = 46.09375, ext\_offset = 3
    successes = 1, resets = 2, offset = 7.03125, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 1, offset = 7.421875, width = 46.09375, ext\_offset = 2
    Timeout - no trigger
    successes = 8, resets = 1, offset = 7.421875, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 7, resets = 3, offset = 7.421875, width = 46.09375, ext\_offset = 2
    Timeout - no trigger
    successes = 2, resets = 1, offset = 7.421875, width = 46.09375, ext\_offset = 3
    successes = 3, resets = 0, offset = 7.421875, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 1, resets = 1, offset = 7.421875, width = 46.09375, ext\_offset = 3
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 7, resets = 1, offset = 7.8125, width = 46.09375, ext\_offset = 2
    Timeout - no trigger
    successes = 8, resets = 1, offset = 7.8125, width = 46.09375, ext\_offset = 2
    successes = 9, resets = 0, offset = 7.8125, width = 46.09375, ext\_offset = 2
    successes = 6, resets = 0, offset = 7.8125, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 2, offset = 7.8125, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 1, offset = 7.8125, width = 46.09375, ext\_offset = 3
    successes = 6, resets = 4, offset = 8.59375, width = 46.09375, ext\_offset = 2
    successes = 8, resets = 0, offset = 8.59375, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 2, offset = 8.59375, width = 46.09375, ext\_offset = 2
    successes = 2, resets = 0, offset = 8.59375, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 0, offset = 8.59375, width = 46.09375, ext\_offset = 3
    successes = 2, resets = 1, offset = 8.59375, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 1, offset = 8.984375, width = 46.09375, ext\_offset = 2
    successes = 8, resets = 0, offset = 8.984375, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 2, offset = 8.984375, width = 46.09375, ext\_offset = 2
    successes = 5, resets = 0, offset = 8.984375, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 0, offset = 8.984375, width = 46.09375, ext\_offset = 3
    successes = 3, resets = 0, offset = 8.984375, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 9, resets = 1, offset = 9.375, width = 46.09375, ext\_offset = 2
    successes = 6, resets = 0, offset = 9.375, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 3, offset = 9.375, width = 46.09375, ext\_offset = 2
    successes = 1, resets = 0, offset = 9.375, width = 46.09375, ext\_offset = 3
    successes = 4, resets = 1, offset = 9.375, width = 46.09375, ext\_offset = 3
    successes = 6, resets = 0, offset = 9.765625, width = 46.09375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 1, offset = 9.765625, width = 46.09375, ext\_offset = 2
    successes = 4, resets = 0, offset = 9.765625, width = 46.09375, ext\_offset = 2
    successes = 3, resets = 0, offset = 9.765625, width = 46.09375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 2.734375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 4, offset = 2.734375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 2, resets = 7, offset = 2.734375, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    successes = 3, resets = 6, offset = 2.734375, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 6, offset = 2.734375, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 5, offset = 3.125, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 3.125, width = 46.484375, ext\_offset = 2
    successes = 4, resets = 6, offset = 3.125, width = 46.484375, ext\_offset = 2
    successes = 4, resets = 6, offset = 3.125, width = 46.484375, ext\_offset = 3
    successes = 3, resets = 7, offset = 3.125, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 7, offset = 3.125, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 3.515625, width = 46.484375, ext\_offset = 2
    successes = 7, resets = 3, offset = 3.515625, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 8, offset = 3.515625, width = 46.484375, ext\_offset = 2
    successes = 3, resets = 6, offset = 3.515625, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 4, offset = 3.515625, width = 46.484375, ext\_offset = 3
    successes = 7, resets = 3, offset = 3.515625, width = 46.484375, ext\_offset = 3
    successes = 3, resets = 7, offset = 3.90625, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 3.90625, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 4, offset = 3.90625, width = 46.484375, ext\_offset = 2
    successes = 5, resets = 5, offset = 3.90625, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 4, offset = 3.90625, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 3, offset = 4.296875, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 4.296875, width = 46.484375, ext\_offset = 2
    successes = 5, resets = 5, offset = 4.296875, width = 46.484375, ext\_offset = 2
    successes = 2, resets = 8, offset = 4.296875, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 7, offset = 4.296875, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 5, offset = 4.296875, width = 46.484375, ext\_offset = 3
    successes = 3, resets = 7, offset = 4.6875, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 2, resets = 8, offset = 4.6875, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 4.6875, width = 46.484375, ext\_offset = 2
    successes = 2, resets = 8, offset = 4.6875, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 4.6875, width = 46.484375, ext\_offset = 3
    successes = 3, resets = 6, offset = 4.6875, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 9, offset = 5.078125, width = 46.484375, ext\_offset = 2
    successes = 4, resets = 6, offset = 5.078125, width = 46.484375, ext\_offset = 2
    successes = 2, resets = 8, offset = 5.078125, width = 46.484375, ext\_offset = 2
    successes = 1, resets = 8, offset = 5.078125, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 8, offset = 5.078125, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 8, offset = 5.078125, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 5, offset = 5.46875, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 8, offset = 5.46875, width = 46.484375, ext\_offset = 2
    successes = 6, resets = 4, offset = 5.46875, width = 46.484375, ext\_offset = 2
    successes = 3, resets = 7, offset = 5.46875, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 9, offset = 5.46875, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 7, offset = 5.46875, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 9, offset = 5.859375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 5.859375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 4, offset = 5.859375, width = 46.484375, ext\_offset = 2
    successes = 5, resets = 5, offset = 5.859375, width = 46.484375, ext\_offset = 3
    successes = 6, resets = 4, offset = 5.859375, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 8, offset = 5.859375, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 5, offset = 6.25, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 6.25, width = 46.484375, ext\_offset = 2
    successes = 3, resets = 7, offset = 6.25, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 3, resets = 6, offset = 6.25, width = 46.484375, ext\_offset = 3
    Timeout - no trigger
    successes = 2, resets = 6, offset = 6.25, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 4, offset = 6.25, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 4, offset = 6.640625, width = 46.484375, ext\_offset = 2
    successes = 3, resets = 7, offset = 6.640625, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 5, offset = 6.640625, width = 46.484375, ext\_offset = 2
    successes = 3, resets = 5, offset = 6.640625, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 8, offset = 6.640625, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 8, offset = 6.640625, width = 46.484375, ext\_offset = 3
    successes = 5, resets = 5, offset = 7.03125, width = 46.484375, ext\_offset = 2
    successes = 2, resets = 8, offset = 7.03125, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 6, resets = 3, offset = 7.03125, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 5, offset = 7.03125, width = 46.484375, ext\_offset = 3
    Timeout - no trigger
    successes = 2, resets = 5, offset = 7.03125, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 3, offset = 7.03125, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.421875, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 7.421875, width = 46.484375, ext\_offset = 2
    successes = 6, resets = 4, offset = 7.421875, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 5, offset = 7.421875, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 4, offset = 7.421875, width = 46.484375, ext\_offset = 3
    successes = 5, resets = 5, offset = 7.421875, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 7.8125, width = 46.484375, ext\_offset = 2
    successes = 6, resets = 4, offset = 7.8125, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 7.8125, width = 46.484375, ext\_offset = 2
    successes = 5, resets = 5, offset = 7.8125, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 8, offset = 7.8125, width = 46.484375, ext\_offset = 3
    successes = 3, resets = 7, offset = 7.8125, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 8.59375, width = 46.484375, ext\_offset = 2
    successes = 4, resets = 6, offset = 8.59375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 8.59375, width = 46.484375, ext\_offset = 2
    successes = 8, resets = 2, offset = 8.59375, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 8, offset = 8.59375, width = 46.484375, ext\_offset = 3
    successes = 2, resets = 8, offset = 8.59375, width = 46.484375, ext\_offset = 3
    successes = 7, resets = 3, offset = 8.984375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 5, resets = 5, offset = 8.984375, width = 46.484375, ext\_offset = 2
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 1, resets = 9, offset = 8.984375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 5, resets = 4, offset = 8.984375, width = 46.484375, ext\_offset = 3
    successes = 5, resets = 5, offset = 8.984375, width = 46.484375, ext\_offset = 3
    successes = 4, resets = 4, offset = 8.984375, width = 46.484375, ext\_offset = 3
    successes = 5, resets = 5, offset = 9.375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 3, resets = 7, offset = 9.375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 4, resets = 6, offset = 9.375, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 7, resets = 2, offset = 9.375, width = 46.484375, ext\_offset = 3
    Timeout - no trigger
    successes = 4, resets = 5, offset = 9.375, width = 46.484375, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 4, resets = 3, offset = 9.375, width = 46.484375, ext\_offset = 3
    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 6, resets = 4, offset = 9.765625, width = 46.484375, ext\_offset = 2
    successes = 6, resets = 4, offset = 9.765625, width = 46.484375, ext\_offset = 2
    successes = 6, resets = 4, offset = 9.765625, width = 46.484375, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    successes = 6, resets = 4, offset = 9.765625, width = 46.484375, ext\_offset = 3
    Timeout - no trigger
    successes = 5, resets = 4, offset = 9.765625, width = 46.484375, ext\_offset = 3
    successes = 7, resets = 2, offset = 9.765625, width = 46.484375, ext\_offset = 3
    successes = 1, resets = 9, offset = 2.734375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 3.125, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 3.125, width = 46.875, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 8, offset = 3.125, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 3.125, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 3.125, width = 46.875, ext\_offset = 3
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 8, offset = 3.125, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 3.515625, width = 46.875, ext\_offset = 2
    successes = 2, resets = 8, offset = 3.515625, width = 46.875, ext\_offset = 3
    successes = 2, resets = 8, offset = 3.90625, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 3.90625, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 3.90625, width = 46.875, ext\_offset = 3
    successes = 2, resets = 8, offset = 3.90625, width = 46.875, ext\_offset = 3
    successes = 2, resets = 8, offset = 4.296875, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 4.296875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 4.6875, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 4.6875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 4.6875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 5.078125, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 5.078125, width = 46.875, ext\_offset = 3
    successes = 3, resets = 7, offset = 5.46875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 5.859375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 5.859375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 5.859375, width = 46.875, ext\_offset = 3
    successes = 2, resets = 8, offset = 6.25, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 6.25, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.03125, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 7.03125, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.03125, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.421875, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 7.421875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.421875, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 7.8125, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 7.8125, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 8.59375, width = 46.875, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 1, resets = 9, offset = 8.59375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 8.984375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 8.984375, width = 46.875, ext\_offset = 2
    successes = 3, resets = 7, offset = 8.984375, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 9.375, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 9.375, width = 46.875, ext\_offset = 2
    successes = 3, resets = 7, offset = 9.375, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 9.765625, width = 46.875, ext\_offset = 2
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout - no trigger
    successes = 2, resets = 8, offset = 9.765625, width = 46.875, ext\_offset = 2
    successes = 1, resets = 9, offset = 9.765625, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 9.765625, width = 46.875, ext\_offset = 3
    successes = 1, resets = 9, offset = 3.125, width = 47.265625, ext\_offset = 3
    successes = 1, resets = 9, offset = 3.515625, width = 47.265625, ext\_offset = 2
    successes = 1, resets = 9, offset = 8.984375, width = 47.265625, ext\_offset = 2
    successes = 1, resets = 9, offset = 8.984375, width = 47.265625, ext\_offset = 3
    successes = 1, resets = 9, offset = 9.375, width = 47.265625, ext\_offset = 2
    successes = 1, resets = 9, offset = 9.765625, width = 47.265625, ext\_offset = 2
    successes = 1, resets = 9, offset = 9.765625, width = 47.265625, ext\_offset = 3
    successes = 1, resets = 9, offset = 9.765625, width = 47.265625, ext\_offset = 3
    Done glitching
    



**In [10]:**

.. code:: ipython3

    %matplotlib inline
    gc.results.plot_2d(plotdots={"success":"+g", "reset":"xr", "normal":None})


**Out [10]:**


.. image:: img/OPENADC-CWLITEXMEGA-courses_fault101_SOLN_Fault2_1-IntroductiontoVoltageGlitching_14_0.png



**In [11]:**

.. code:: ipython3

    scope.dis()
    target.dis()


**In [12]:**

.. code:: ipython3

    assert total_successes >= 1
