
Instruction Power Differences
=============================

This tutorial will introduce you to measuring the power consumption of a
device under attack. It will demonstrate how the power consumption of a
target changes based on what operations it's doing.

If you haven't yet, you should probably complete Tutorial B1, which
introduces building firmware, programming the target, and scripting.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'CWNANO'
    PLATFORM = 'CWNANO'

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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep || exit 0
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep || exit 0
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

    Detected unknown STM32F ID: 0x444
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4335 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4335 bytes
    


Capturing Traces
----------------


**In [9]:**

.. code:: ipython3

    print(scope)


**Out [9]:**



.. parsed-literal::

    ChipWhisperer Nano Device
    io = 
        tio1   = None
        tio2   = None
        tio3   = None
        tio4   = None
        pdid   = True
        pdic   = False
        nrst   = True
        clkout = 7500000.0
    adc = 
        clk_src  = int
        clk_freq = 7500000.0
        samples  = 1000
    glitch = 
        repeat     = 0
        ext_offset = 0
    
    


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

    

    <div id="0b245ef0-c452-4545-bcf5-f567f1b0ff2b"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0b245ef0-c452-4545-bcf5-f567f1b0ff2b');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="1f625c34-90c1-44cc-b1eb-0327861c1677" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="15f81fe9-0a64-4baf-ac5c-df2de988c5af"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#15f81fe9-0a64-4baf-ac5c-df2de988c5af');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"3c8ccfdf-7cfe-4d24-94ca-e6d8586a7f8d":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"3c8ccfdf-7cfe-4d24-94ca-e6d8586a7f8d","roots":{"1002":"1f625c34-90c1-44cc-b1eb-0327861c1677"}}];
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep || exit 0
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



Reprogram the target:


**In [12]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [12]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x444
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4335 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4335 bytes
    


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

    

    <div id="eabfd119-912c-4f7d-8275-700f9ea0cab8"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#eabfd119-912c-4f7d-8275-700f9ea0cab8');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="6cd28994-f56f-4065-b731-629ec721491c" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="354f4532-eec2-47d9-8ae1-26d345fc084a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#354f4532-eec2-47d9-8ae1-26d345fc084a');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"49abc2a9-951e-4d2b-9b4e-559a283089b8":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1152","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1157","type":"Selection"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1154","type":"BasicTickFormatter"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"formatter":{"id":"1152","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1157","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"formatter":{"id":"1154","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"49abc2a9-951e-4d2b-9b4e-559a283089b8","roots":{"1103":"6cd28994-f56f-4065-b731-629ec721491c"}}];
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep || exit 0
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




**In [17]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [17]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x444
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4335 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4335 bytes
    



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

    

    <div id="e3d67538-761b-49a1-99bc-e4b1281b750f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#e3d67538-761b-49a1-99bc-e4b1281b750f');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="7270f548-688a-44ed-8ade-48b0847bbe39" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="cc64da1d-c525-459c-8e48-ab005c2715ba"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#cc64da1d-c525-459c-8e48-ab005c2715ba');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"f4063625-a9b2-479d-8867-4e0eaaebda18":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1269","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{},"id":"1273","type":"BasicTickFormatter"},{"attributes":{"overlay":{"id":"1274","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{},"id":"1236","type":"ResetTool"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1269","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1274","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{},"id":"1275","type":"UnionRenderers"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1273","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{},"id":"1220","type":"LinearScale"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{},"id":"1276","type":"Selection"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1276","type":"Selection"},"selection_policy":{"id":"1275","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1271","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{},"id":"1271","type":"BasicTickFormatter"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"f4063625-a9b2-479d-8867-4e0eaaebda18","roots":{"1213":"7270f548-688a-44ed-8ade-48b0847bbe39"}}];
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWNANO.elf simpleserial-base-CWNANO.eep || exit 0
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




**In [20]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [20]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x444
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4335 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4335 bytes
    



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

    

    <div id="73d681c1-1476-40b4-8a1b-05a623bafa6d"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#73d681c1-1476-40b4-8a1b-05a623bafa6d');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="a6e38649-efb8-4fe5-a6b9-428754b98adf" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="b4b9da7f-4f56-4fce-8d42-c1669f361210"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b4b9da7f-4f56-4fce-8d42-c1669f361210');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"afa9b665-e2f9-4d79-a1e3-a9c6979e1430":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1397","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{},"id":"1404","type":"Selection"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{"formatter":{"id":"1399","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{"overlay":{"id":"1402","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{"text":""},"id":"1397","type":"Title"},{"attributes":{},"id":"1399","type":"BasicTickFormatter"},{"attributes":{},"id":"1403","type":"UnionRenderers"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1404","type":"Selection"},"selection_policy":{"id":"1403","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{},"id":"1401","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1402","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{},"id":"1342","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1401","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{},"id":"1347","type":"BasicTicker"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"afa9b665-e2f9-4d79-a1e3-a9c6979e1430","roots":{"1332":"a6e38649-efb8-4fe5-a6b9-428754b98adf"}}];
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
