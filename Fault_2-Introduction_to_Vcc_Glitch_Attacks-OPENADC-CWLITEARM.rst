
Introduction to Vcc Glitch Attacks
==================================

Supported setups:

SCOPES:

-  OPENADC

PLATFORMS:

-  CWLITEARM


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    sample_size = 5

Background on Vcc (Power) Glitching
-----------------------------------

The previous clock glitching tutorials looked into the assumption of a
constant clock. But instead we can modify the voltage of the device,
causing for example a failure to correctly read a memory location or
otherwise cause havoc with the proper functioning.

An example of a successful Vcc glitch is shown in the following figures
(Vcc in blue, clock in red):

.. figure:: https://wiki.newae.com/images/4/4f/Vccglitch_working.png
   :alt: 

.. figure:: https://wiki.newae.com/images/6/60/Vccglitch_working_zoom.png
   :alt: 

Even more so than clock glitching, Vcc glitching is highly sensitive to
glitch offset and width. While the above glitch was successful, the
following was not:

.. figure:: https://wiki.newae.com/images/b/b6/Vccglitch_notworking_zoom.png
   :alt: 

Vcc glitching is also very sensitive to the shape of the glitch: things
like board layout and the distance between where the glitch is inserted
and the target can make the difference between successful and
unsuccessful glitches.

Despite these additional complications, Vcc glitching is an extremely
useful tool as it allows attacks on targets that do not run off of
external clock inputs.

Background on Glitch Generation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For more details, please see
`Fault\_1-Introduction\_to\_Clock\_Glitching <Fault_1-Introduction_to_Clock_Glitch_Attacks.ipynb>`__,
this tutorials assumes you have already performed the clock glitching
tutorial.

The glitch generation hardware is the same as used in the clock
glitching attack. The generated glitches are synchronous to the device
clock, and inserted at a precise offset from the clock edge.

Glitches can be inserted continuously or triggered by some event. The
following figure shows the generation of two glitches:

.. figure:: https://wiki.newae.com/images/9/95/Glitchgen-mux-glitchonly.png
   :alt: 

The VCC glitching method here uses an electronic switch (a MOSFET) to
short the power line to GND at specific instances. The following figure
shows the basic function of this system:

.. figure:: https://wiki.newae.com/images/8/82/Glitch-vccglitcher.png
   :alt: 

This method allows use with the standard side-channel analysis
development board, which has resistors inserted into the VCC lines
already. The downside of this method is that it can only generate short
glitches, since the power consumption through the shunt resistor will
short out the resistor.

The MOSFET glitching hardware is built into the ChipWhisperer-Lite (both
CW1173 and CW1180) board.

Setting up Firmware
-------------------

During this tutorial, we will once again be working off the
``glitch-simple`` project.

Now navigate to the ``glitch-simple`` folder and open ``glitchsimple.c``
in a code editor. Find the ``glitch_infinite()`` function:

.. code:: c

    void glitch_infinite(void)
    {
        char str[64];
        unsigned int k = 0;
        //Declared volatile to avoid optimizing away loop.
        //This also adds lots of SRAM access
        volatile uint16_t i, j;
        volatile uint32_t cnt;
        while(1){
            cnt = 0;
            trigger_high();
            trigger_low();
            for(i=0; i<200; i++){
                for(j=0; j<200; j++){
                    cnt++;
                }
            }
            sprintf(str, "%lu %d %d %d\n", cnt, i, j, k++);
            uart_puts(str);
        }
    }

As you can see, this function enters into an infinite loop with two
inner loops that increment three variables (``cnt``, ``i``, and ``j``).
These are sent back over serial along with an overall loop counter.
During normal operation, we should receive ``40000 200 200 $k`` (where
``$k`` is the value of the loop counter ``k``). Our objective will be to
insert a Vcc glitch such that one or more of the numbers that we get
back are incorrect.

Then build the firmware:


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM"
    cd ../hardware/victims/firmware/glitch-simple
    make PLATFORM=$1 CRYPTO_TARGET=NONE FUNC_SEL=GLITCH_INF


**Out [2]:**



.. parsed-literal::

    rm -f -- glitchsimple-CWLITEARM.hex
    rm -f -- glitchsimple-CWLITEARM.eep
    rm -f -- glitchsimple-CWLITEARM.cof
    rm -f -- glitchsimple-CWLITEARM.elf
    rm -f -- glitchsimple-CWLITEARM.map
    rm -f -- glitchsimple-CWLITEARM.sym
    rm -f -- glitchsimple-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- glitchsimple.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- glitchsimple.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- glitchsimple.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: glitchsimple.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple.o.d glitchsimple.c -o objdir/glitchsimple.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: glitchsimple-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DGLITCH_INF -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple-CWLITEARM.elf.d objdir/glitchsimple.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output glitchsimple-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=glitchsimple-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: glitchsimple-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.hex
    .
    Creating load file for EEPROM: glitchsimple-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: glitchsimple-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.lss
    .
    Creating Symbol Table: glitchsimple-CWLITEARM.sym
    arm-none-eabi-nm -n glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       6488	    108	   1188	   7784	   1e68	glitchsimple-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



Attack Script
-------------

Setup
~~~~~

Now that we've studied the code and have an objective, we can start
building our attack script. We'll start by connecting to and setting up
the ChipWhisperer, then programming it. As usual, make sure you modify
``fw_path`` with the path to the file you built in the last step.


**In [3]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup_Generic.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = "../hardware/victims/firmware/glitch-simple/glitchsimple-{}.hex".format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 6603 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 6603 bytes
    


Like with clock glitching, Vcc glitching may crash the target, requiring
a reset. Like with the previous tutorial, we'll use ``reset_target()``
from ``Helper_Scripts/Setup.ipynb``.

Now that we have some of the basic setup done, let's make sure the
firmware works as we expect. If we reset the target and wait a second,
then print the serial data we got back, we should see a number of lines
of the form ``40000 200 200 $k``.


**In [6]:**

.. code:: ipython3

    reset_target(scope)
    target.flush()
    time.sleep(1)
    resp = target.read()
    print(resp)


**Out [6]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    40000 200 200 0
    40000 200 200 1
    40000 200 200 2
    40000 200 200 3
    40000 200 200 4
    40000 200 200 5
    40000 200 200 6
    40000 200 200 7
    


Glitch Setup
~~~~~~~~~~~~

First, we'll setup the glitch module itself. Most of these settings
should look familiar from the previous tutorial with a few new
additions:

-  Instead of setting the clock source for the target to be the glitch
   module, we instead set the low power MOSFET's input to be the glitch
   module by setting ``scope.io.glitch_lp`` to ``True``. The
   ChipWhisperer-Lite also has a high power MOSFET, but we won't be
   using that in this tutorial.
-  Instead of setting the glitch output to something like "clock\_xor",
   we instead set it to "glitch\_only", since we don't want Vcc of the
   target to be oscillating with our clock.

For the more specific settings (offset, width, repeat, etc), this will
depend on both the target and when you got your CW-Lite: Newer versions
of the CW-Lite use a different glitch MOSFET, which changes the settings
required for getting a glitch.


**In [7]:**

.. code:: ipython3

    from collections import namedtuple
    Range = namedtuple('Range', ['min', 'max', 'step'])
    if PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        scope.glitch.clk_src = "clkgen"
        scope.glitch.output = "glitch_only"
        scope.glitch.trigger_src = "ext_single"
        scope.glitch.width = 35
        scope.glitch.offset = -17.4
        scope.glitch.repeat = 1
        
        width_range = Range(38.5, 39.1, 0.4)
        offset_range = Range(-28.4, -28.125, 0.4)
        scope.glitch.offset_fine = 24
        def glitch_on(scope):
            scope.io.glitch_lp = False
            scope.io.glitch_hp = True
        def glitch_off(scope):
            scope.gio.glitch_hp = False
        glitch_on(scope)
        scope.glitch.ext_offset = 2186
        print(scope.glitch)
    elif PLATFORM == "CWNANO" and SCOPETYPE == "CWNANO":
        scope.glitch.ext_offset = 546
        scope.adc.clk_freq = 7.5E6
        scope.glitch.repeat = 6
        repeat_range = range(4, 7)
        offset_range = range(475, 510)
        def glitch_on(scope):
            pass
        def glitch_off(scope):
            pass
        pass #later


**Out [7]:**



.. parsed-literal::

    clk_src     = clkgen
    width       = 35.15625
    width_fine  = 0
    offset      = -17.578125
    offset_fine = 24
    trigger_src = ext_single
    arm_timing  = after_scope
    ext_offset  = 2186
    repeat      = 1
    output      = glitch_only
    
    



**In [8]:**

.. code:: ipython3

    print(scope)


**Out [8]:**



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
        samples    = 5000
        decimate   = 1
        trig_count = 8469380
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
        nrst       = high
        glitch_hp  = True
        glitch_lp  = False
        extclk_src = hs1
        hs2        = clkgen
        target_pwr = True
    glitch = 
        clk_src     = clkgen
        width       = 35.15625
        width_fine  = 0
        offset      = -17.578125
        offset_fine = 24
        trigger_src = ext_single
        arm_timing  = after_scope
        ext_offset  = 2186
        repeat      = 1
        output      = glitch_only
    
    


Glitching a Single Point
~~~~~~~~~~~~~~~~~~~~~~~~

Unlike with the previous tutorial, we don't control when the device
sends serial data back to us. This means we'll need to parse the data we
get back.

We start our attack off by flushing the ChipWhipserer's serial buffer:

.. code:: python

    target.flush()

Next, we'll set our trigger source to be "ext\_continuous". This differs
from "ext\_single" in that the ChipWhisperer doesn't need to be armed to
insert a glitch, making our loop a little simpler:

.. code:: python

    scope.glitch.trigger_src = "ext_continuous"

A key part of parsing the serial data is to be able to read a line of
data (data terminated with ":raw-latex:`\n`"). We can do that by reading
back data until we get a newline character (":raw-latex:`\n`"):

.. code:: python

    while "\n" not in line:
        time.sleep(0.1)
        line += target.read()

This needs to be repeated twice in our loop: once at the start to make
sure we're on a newline (so we don't look at the wrong numbers for our
glitch) and again to actually read the line. For the first read, we also
need to make sure we keep any characters after the newline, as this will
be the start of the actual line we parse. All together, this looks like:

.. code:: python

    line = ""
    while "\n" not in line:
        time.sleep(0.1)
        line += target.read()
    lines = line.split("\n") 
    if len(lines) > 1:
        line = lines[-1]
    else:
        line = ""

    while "\n" not in line:
        time.sleep(0.1)
        line += target.read(num_char)

Now that we have our line of data we can parse it by splitting it up via
spaces to get each number.

After the loop ends, we'll need to set our trigger back to "ext\_single"
to stop the glitches from continuing.

All together (with some additional error checking), this looks like:


**In [9]:**

.. code:: ipython3

    from tqdm import tnrange
    reset_target(scope)
    target.flush()
    
    if SCOPETYPE == "OPENADC":
        scope.glitch.trigger_src = "ext_continuous"
    
    for j in tnrange(20):
        line = ""
        
        while "\n" not in line:
            time.sleep(0.1)
            line += target.read()
        lines = line.split("\n") 
        if len(lines) > 1:
            line = lines[-1]
        else:
            line = ""
        
        while "\n" not in line:
            time.sleep(0.1)
            line += target.read()
            
        if "hello" in line:
            print("Target crashed")
        nums = line.split(" ")
        try:
            if int(nums[0]) != 40000:
                print(line)
            print(line)
        except ValueError as e:
            continue
    
    if SCOPETYPE == "OPENADC":
        scope.glitch.trigger_src = "ext_single"


**Out [9]:**





.. parsed-literal::

    40000 200 200 2
    
    40000 200 200 4
    
    40000 200 200 6
    
    40000 200 200 8
    
    40000 200 200 10
    40000 200 200 11
    
    40000 200 200 13
    
    40000 200 200 15
    
    40000 200 200 17
    
    40000 200 200 19
    40000 200 200 20
    
    40000 200 200 22
    
    40000 200 200 24
    
    40000 200 200 26
    
    40000 200 200 28
    
    40000 200 200 31
    
    40000 200 200 33
    
    40000 200 200 35
    
    40000 200 200 37
    
    40000 200 200 39
    40000 200 200 40
    
    40000 200 200 42
    
    40000 200 200 44
    
    
    


You should hopefully see some glitched lines printed after running the
above block. If not, don't worry -- we'll be adjusting the settings of
the glitch which should make them more likely.

Improving Glitch Settings
~~~~~~~~~~~~~~~~~~~~~~~~~

One more thing we can do to improve our glitch success rate is to modify
our glitch settings (width and offset) while holding the ext offset
constant.

Similar to
`Fault\_1 <Fault_1-Introduction_to_Clock_Glitch_Attacks.ipynb>`__, let's
also measure our success rate with each glitch setting, as well as our
crash rate, then print them at the end.

If you found an ext offset that worked well for you, be sure to fill it
in below:


**In [10]:**

.. code:: ipython3

    from tqdm import tnrange, tqdm_notebook
    reset_target(scope)
    glitches = []
    glitch_text = []
    
    if SCOPETYPE == "OPENADC":
        target.flush()
        scope.glitch.trigger_src = "ext_continuous"
        scope.glitch.offset_fine = 24
        scope.glitch.repeat = 1
        scope.glitch.ext_offset = 2186
        scope.glitch.offset = offset_range.min
        t_offset = tqdm_notebook(total=int((offset_range.max-offset_range.min)/offset_range.step) + 1, desc="Offset")
        while scope.glitch.offset < offset_range.max:
            scope.glitch.width = width_range.min
            t_width = tqdm_notebook(total=int((width_range.max-width_range.min)/width_range.step), leave=False, desc="Width")
            while scope.glitch.width < width_range.max:
                successes = 0
                crashes = 0
                for j in tnrange(sample_size, leave=False, desc="Attempt"):
                    line = ""
                    while "\n" not in line:
                        time.sleep(0.1)
                        num_char = target.in_waiting()
                        if num_char == 0:
                            glitch_off(scope)
                            time.sleep(0.01)
                            glitch_on(scope)
                            break
                        line += target.read()
                    lines = line.split("\n") 
                    if len(lines) > 1:
                        line = lines[-1]
                    else:
                        line = ""
    
                    while "\n" not in line:
                        time.sleep(0.1)
                        num_char = target.in_waiting()
                        if num_char == 0:
                            glitch_off(scope)
                            time.sleep(0.01)
                            glitch_on(scope)
                            break
                        line += target.read()
    
                    nums = line.split(" ")
                    if "hello" in line:
                        crashes += 1
                        #print("Target crashed")
                    #print(line)
                    try:
                        if nums[0] == "":
                            continue
                        if int(nums[0]) != 40000:
                            glitch_text += line
                            successes += 1
                    except ValueError as e:
                        continue
                glitches.append([scope.glitch.width, scope.glitch.offset, successes / sample_size, crashes / sample_size])
                if successes > 0:
                    print([scope.glitch.width, scope.glitch.offset, successes / sample_size, crashes / sample_size])
                scope.glitch.width += width_range.step
                t_width.update()
    
            scope.glitch.offset += offset_range.step
            t_offset.update()
            t_width.close()
        t_width.close()
        t_offset.close()
        scope.glitch.trigger_src = "ext_single"
    elif SCOPETYPE == "CWNANO":
        for offset in tqdm_notebook(offset_range, desc="Offset"):
            scope.glitch.ext_offset = offset
            for repeat in tqdm_notebook(repeat_range, desc="Repeat", leave=False):
                scope.glitch.repeat = repeat
                successes = 0
                crashes = 0
                for j in tnrange(sample_size, leave=False, desc="Attempt"):
                    line = ""
                    scope.arm()
                    while "\n" not in line:
                        time.sleep(0.1)
                        num_char = target.in_waiting()
                        if num_char == 0:
                            break
                        line += target.read()
                    lines = line.split("\n") 
                    if len(lines) > 1:
                        line = lines[-1]
                    else:
                        line = ""
    
                    while "\n" not in line:
                        time.sleep(0.1)
                        num_char = target.in_waiting()
                        if num_char == 0:
                            break
                        line += target.read(num_char)
                    
                    nums = line.split(" ")
                    if "hello" in line:
                        crashes += 1
                    try:
                        if nums[0] == "":
                            continue
                        if int(nums[0]) != 40000:
                            glitch_text += line
                            successes += 1
                    except ValueError as e:
                        continue
                glitches.append([scope.glitch.repeat, scope.glitch.ext_offset, successes / sample_size, crashes / sample_size])
                if successes > 0:
                    print([scope.glitch.repeat, scope.glitch.ext_offset, successes / sample_size, crashes / sample_size])
            
        pass


**Out [10]:**









.. parsed-literal::

    [38.671875, -28.515625, 0.4, 0.0]
    






.. parsed-literal::

    [39.0625, -28.515625, 0.4, 0.0]
    
    


Then, sorting by success rate:


**In [11]:**

.. code:: ipython3

    def sort_glitch(glitch):
        return glitch[2]
    
    glitches.sort(key=sort_glitch,reverse=True)
    for glitch in glitches:
        print(glitch)


**Out [11]:**



.. parsed-literal::

    [38.671875, -28.515625, 0.4, 0.0]
    [39.0625, -28.515625, 0.4, 0.0]
    


Going Further
~~~~~~~~~~~~~

There's a lot more you can do with this attack:

-  If you still weren't able to get any glitches, create an attack loop
   that scans a larger range of offsets and ranges. You may also have to
   scan the offset\_fine and width\_fine glitch settings to find
   glitches.
-  Glitching different instructions will produce different results. Try
   using your best glitch settings from the previous part and scanning
   ext offset ranges to produce different glitches

   -  During our attack loop, we only checked for glitches in the first
      number (40000). You may want to examine the other numbers for
      glitches as well
   -  Open the listing file (``.lss``) and view the assembly of the
      ``glitch_infinite()`` function. Can you explain the different
      glitch effects you saw?

-  Try using the glitch settings you found in this tutorial to glitch
   other functions in the ``glitchsimple.c`` file.

Otherwise, we're done with the tutorial. You can now disconnect from the
ChipWhisperer:


**In [12]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Conclusion
----------

With the tutorial now finished, you should have some Vcc glitching
experience under your belt. If you're interested in doing more Vcc
glitching, you may want to try `Tutorial
A9 <https://wiki.newae.com/Tutorial_A9_Bypassing_LPC1114_Read_Protect>`__
from the ChipWhisperer Wiki, which uses Vcc glitching to bypass code
readout protection on an LPC1114 (requires an LPC1114 dev board). You
may also want to try glitching some of the other functions in
``glitchsimple.c``. If you have a Raspberry Pi, you can also attempt the
attack described
`here <https://wiki.newae.com/Tutorial_A3_VCC_Glitch_Attacks#Glitching_More_Advanced_Targets:_Raspberry_Pi>`__
(though you'll need to transfer the steps from the old GUI over to
Jupyter).

Tests
-----


**In [13]:**

.. code:: ipython3

    # Test we got at least one success
    # Should be sorted by success rate, so just check first one
    success = glitches[0][2] > 0
    assert success, "Failed to glitch target"
