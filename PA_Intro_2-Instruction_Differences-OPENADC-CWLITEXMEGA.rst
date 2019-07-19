
Instruction Power Differences
=============================

This tutorial will introduce you to measuring the power consumption of a
device under attack. It will demonstrate how the power consumption of a
target changes based on what operations it’s doing.

If you haven’t yet, you should probably complete Tutorial B1, which
introduces building firmware, programming the target, and scripting.


**In [ ]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'

Setting up Firmware
-------------------

In this tutorial, we will once again be working off of the
simpleserial-base firmware.

Let’s start by creating a new project and building our firmware:


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

    rm -f -- simpleserial-base-CWLITEXMEGA.hex
    rm -f -- simpleserial-base-CWLITEXMEGA.eep
    rm -f -- simpleserial-base-CWLITEXMEGA.cof
    rm -f -- simpleserial-base-CWLITEXMEGA.elf
    rm -f -- simpleserial-base-CWLITEXMEGA.map
    rm -f -- simpleserial-base-CWLITEXMEGA.sym
    rm -f -- simpleserial-base-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- simpleserial-base.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- simpleserial-base.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: simpleserial-base-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEXMEGA.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output simpleserial-base-CWLITEXMEGA.elf -Wl,-Map=simpleserial-base-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEXMEGA.sym
    avr-nm -n simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       1722	     16	     52	   1790	    6fe	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    


As in the previous tutorial, we’ll need to modify our firmware. Navigate
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

To start off, we’ll add a simple ``for`` loop. We’ll start off by
looking at how the power trace changes based on how long the loop is.
Start with a loop (make sure your variables are volatile) that runs from
0 to 4:

.. code:: c

   for(volatile int i = 0; i < 5; i++);

Next, we’ll move on to actually capturing and displaying the power
trace.

ChipWhisperer Setup
-------------------

Setup for this tutorial will be pretty similar to Tutorial B1, so we’ll
skip most of it by calling some helper scripts. This setup should work
for most targets, but if you’re using a target other than the XMEGA or
STM32F3 (CWLite w/ Arm), you may need to call a different script or do
additional setup (like programming the target with an external
programmer). See the wiki page for your target for more information.

If you’re curious about what’s happening in these helper scripts,
they’re typically located in the ``Helper_Scripts`` folder.


**In [5]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [5]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEXMEGA.hex
    rm -f -- simpleserial-base-CWLITEXMEGA.eep
    rm -f -- simpleserial-base-CWLITEXMEGA.cof
    rm -f -- simpleserial-base-CWLITEXMEGA.elf
    rm -f -- simpleserial-base-CWLITEXMEGA.map
    rm -f -- simpleserial-base-CWLITEXMEGA.sym
    rm -f -- simpleserial-base-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- simpleserial-base.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- simpleserial-base.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: simpleserial-base-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEXMEGA.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output simpleserial-base-CWLITEXMEGA.elf -Wl,-Map=simpleserial-base-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEXMEGA.sym
    avr-nm -n simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       1722	     16	     52	   1790	    6fe	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    



**In [6]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"

By default, the scope will capture many more traces than we need, so
we’ll reduce that to 1000.


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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 1737 bytes
    


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
        trig_count = 7353514
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
We’ll start by programming the target, then capturing a trace, and
finally displaying it using bokeh. We don’t really care about what the
target responds with this time, so we won’t do anything with what we
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

    

    <div id="1610925f-d69f-4bc7-ba41-a1c2248aa2ed"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#1610925f-d69f-4bc7-ba41-a1c2248aa2ed');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof (root._bokeh_onload_callbacks) === "undefined" || force === true) {
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
          root._bokeh_onload_callbacks.forEach(function(callback) { callback() });
        }
        finally {
          delete root._bokeh_onload_callbacks
        }
        console.info("Bokeh: all callbacks have finished");
      }
    
      function load_libs(js_urls, callback) {
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.log("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.log("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = js_urls.length;
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var s = document.createElement('script');
          s.src = url;
          s.async = false;
          s.onreadystatechange = s.onload = function() {
            root._bokeh_is_loading--;
            if (root._bokeh_is_loading === 0) {
              console.log("Bokeh: all BokehJS libraries loaded");
              run_callbacks()
            }
          };
          s.onerror = function() {
            console.warn("failed to load library " + url);
          };
          console.log("Bokeh: injecting script tag for BokehJS library: ", url);
          document.getElementsByTagName("head")[0].appendChild(s);
        }
      };var element = document.getElementById("1001");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1001' but no matching script tag was found. ")
        return false;
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.0.1.min.js"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
        }
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
        console.log("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(js_urls, function() {
          console.log("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="1427c1d5-7969-4324-9967-d892ccbb9015"></div>
    
    </div>



.. raw:: html

    

    <div id="40d0c20b-ceab-4fdb-b780-690cfa13120a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#40d0c20b-ceab-4fdb-b780-690cfa13120a');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"b3f40c85-1008-443c-912a-ece88e7fa263":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1042","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"AAAAAABAvT8AAAAAAPDTvwAAAAAAYMK/AAAAAACgwr8AAAAAAACMPwAAAAAA4Nq/AAAAAABgzb8AAAAAAKDLvwAAAAAAAKe/AAAAAABg2b8AAAAAACDLvwAAAAAAoMi/AAAAAAAAmb8AAAAAANDevwAAAAAA8NK/AAAAAAAA0b8AAAAAAEC0vwAAAAAAAMi/AAAAAAAAlL8AAAAAAAClvwAAAAAAgLk/AAAAAABAvr8AAAAAAACcPwAAAAAAAIa/AAAAAACAvD8AAAAAAEC8vwAAAAAAAJ0/AAAAAAAAkL8AAAAAAIC8PwAAAAAAANm/AAAAAACgy78AAAAAAGDKvwAAAAAAAKS/AAAAAAAQ0r8AAAAAAIC7vwAAAAAAwLu/AAAAAACAqT8AAAAAAEDCvwAAAAAAAGA/AAAAAAAAnL8AAAAAAMC8PwAAAAAAANy/AAAAAACg0L8AAAAAAEDMvwAAAAAAgKK/AAAAAADgzr8AAAAAAAC0vwAAAAAAQLW/AAAAAAAAsj8AAAAAAEDEvwAAAAAAAHS/AAAAAAAAmL8AAAAAAMC+PwAAAAAAQLq/AAAAAAAAoD8AAAAAAAB0vwAAAAAAYMA/AAAAAABAxr8AAAAAAACZvwAAAAAAgKa/AAAAAAAAuD8AAAAAAADCvwAAAAAAAHQ/AAAAAAAAlb8AAAAAAEC/PwAAAAAAwMO/AAAAAAAAdL8AAAAAAACdvwAAAAAAgL0/AAAAAABAu78AAAAAAACXPwAAAAAAAJS/AAAAAACAvD8AAAAAAODTvwAAAAAAAMO/AAAAAABgw78AAAAAAACGPwAAAAAAQMO/AAAAAAAAaD8AAAAAAACavwAAAAAAgLs/AAAAAACAur8AAAAAAACfPwAAAAAAAIy/AAAAAABAvD8AAAAAAODWvwAAAAAAAMe/AAAAAADAxL8AAAAAAABoPwAAAAAAoM6/AAAAAADAtL8AAAAAAAC5vwAAAAAAAKo/AAAAAADgwb8AAAAAAABQvwAAAAAAgKG/AAAAAACAuD8AAAAAAFDUvwAAAAAAoMO/AAAAAAAgxL8AAAAAAAB0PwAAAAAAoNe/AAAAAAAgxr8AAAAAACDDvwAAAAAAAJw/AAAAAAAA4L8AAAAAADDVvwAAAAAAsNC/AAAAAAAAr78AAAAAAKDCvwAAAAAAAII/AAAAAAAAl78AAAAAAMC8PwAAAAAAAKS/AAAAAABAtz8AAAAAAICiPwAAAAAAgMM/AAAAAABg0b8AAAAAAAC/vwAAAAAAQL6/AAAAAAAAoz8AAAAAAIDfvwAAAAAA0NS/AAAAAACQ0b8AAAAAAEC0vwAAAAAA0NK/AAAAAABAvr8AAAAAAAC+vwAAAAAAAKY/AAAAAACgxb8AAAAAAACVvwAAAAAAAKS/AAAAAABAuT8AAAAAAGDHvwAAAAAAgKK/AAAAAAAAq78AAAAAAAC4PwAAAAAAgKy/AAAAAADAtj8AAAAAAACnPwAAAAAAwMU/AAAAAACAtL8AAAAAAACuPwAAAAAAAJc/AAAAAAAgwz8AAAAAAAChvwAAAAAAgLg/AAAAAACAqT8AAAAAAADGPwAAAAAAALe/AAAAAACArj8AAAAAAACZPwAAAAAA4MM/AAAAAAAAqb8AAAAAAAC1PwAAAAAAAKI/AAAAAACAxD8AAAAAAIClvwAAAAAAgLc/AAAAAACApz8AAAAAAGDFPwAAAAAAAHg/AAAAAADgwT8AAAAAAIC2PwAAAAAA4Mo/AAAAAAAAdL8AAAAAAEC/PwAAAAAAQLE/AAAAAAAAxz8AAAAAAACnvwAAAAAAALc/AAAAAACApz8AAAAAAMDFPwAAAAAAAJo/AAAAAACAwz8AAAAAAMC3PwAAAAAAwMo/AAAAAACw0b8AAAAAAEC+vwAAAAAAwL2/AAAAAAAApj8AAAAAACDGvwAAAAAAAJ2/AAAAAACApL8AAAAAAEC6PwAAAAAAkNG/AAAAAADAuL8AAAAAAMC4vwAAAAAAgK8/AAAAAAAgy78AAAAAAICivwAAAAAAAKa/AAAAAABAuz8AAAAAAIC+vwAAAAAAAKE/AAAAAAAAUD8AAAAAAADBPwAAAAAAAKa/AAAAAACAtz8AAAAAAACkPwAAAAAAoMQ/AAAAAACAor8AAAAAAIC5PwAAAAAAgKw/AAAAAADgxT8AAAAAAACUPwAAAAAAYME/AAAAAAAAsT8AAAAAAKDGPwAAAAAAYMW/AAAAAAAAlb8AAAAAAACovwAAAAAAwLc/AAAAAADAwr8AAAAAAABgPwAAAAAAAJS/AAAAAAAAvj8AAAAAAODIvwAAAAAAAKi/AAAAAABAsr8AAAAAAMCxPwAAAAAA0Ni/AAAAAABgyb8AAAAAAMDGvwAAAAAAAIC/AAAAAAAgz78AAAAAAAC0vwAAAAAAgLi/AAAAAAAArD8AAAAAAMDBvwAAAAAAAHA/AAAAAAAAnr8AAAAAAMC5PwAAAAAAQNO/AAAAAABgwb8AAAAAAODBvwAAAAAAAJM/AAAAAACg078AAAAAAIC9vwAAAAAAALm/AAAAAACAsD8AAAAAAFDevwAAAAAAgNG/AAAAAADAy78AAAAAAACZvwAAAAAAgL+/AAAAAAAAoD8AAAAAAACCvwAAAAAAAL8/AAAAAAAAmb8AAAAAAEC6PwAAAAAAgKc/AAAAAADAxD8AAAAAANDRvwAAAAAAgL6/AAAAAABAvr8AAAAAAACkPwAAAAAAAOC/AAAAAACQ1b8AAAAAAEDSvwAAAAAAwLW/AAAAAABQ078AAAAAAIC/vwAAAAAAgL6/AAAAAACApj8AAAAAAKDGvwAAAAAAAJa/AAAAAACApL8AAAAAAIC5PwAAAAAA4MS/AAAAAAAAkb8AAAAAAICjvwAAAAAAQLs/AAAAAACAo78AAAAAAAC6PwAAAAAAAKw/AAAAAADgxT8AAAAAAACwvwAAAAAAALU/AAAAAACApz8AAAAAACDGPwAAAAAAYNK/AAAAAACAvb8AAAAAAEC7vwAAAAAAAK4/AAAAAACgxL8AAAAAAAB8vwAAAAAAAIq/AAAAAADgwD8AAAAAAIDQvwAAAAAAgLa/AAAAAADAtr8AAAAAAECxPwAAAAAAgLu/AAAAAAAApT8AAAAAAAB4PwAAAAAAYME/AAAAAAAAqb8AAAAAAAC3PwAAAAAAgKo/AAAAAADAxj8AAAAAAACXPwAAAAAA4ME/AAAAAAAAsj8AAAAAAKDGPwAAAAAA4MW/AAAAAAAAlr8AAAAAAIClvwAAAAAAALo/AAAAAACgwb8AAAAAAACGPwAAAAAAAIy/AAAAAAAAvz8AAAAAAIDHvwAAAAAAAJ6/AAAAAAAArL8AAAAAAMC1PwAAAAAAwNi/AAAAAABgyL8AAAAAAODEvwAAAAAAAJA/AAAAAABAz78AAAAAAIC0vwAAAAAAALi/AAAAAACArj8AAAAAAIDAvwAAAAAAAIY/AAAAAAAAmL8AAAAAAIC9PwAAAAAAINK/AAAAAAAAv78AAAAAAADAvwAAAAAAAKA/AAAAAAAw1b8AAAAAAGDBvwAAAAAAwLu/AAAAAACArz8AAAAAACDcvwAAAAAAAM6/AAAAAADAx78AAAAAAABQvwAAAAAAgLq/AAAAAAAAqj8AAAAAAACMPwAAAAAAYMI/AAAAAAAAeL8AAAAAAAC/PwAAAAAAAK0/AAAAAAAAxj8AAAAAAHDQvwAAAAAAgLi/AAAAAACAuL8AAAAAAICuPwAAAAAAkN+/AAAAAACw1L8AAAAAAFDRvwAAAAAAgLK/AAAAAADw0L8AAAAAAAC3vwAAAAAAALe/AAAAAADAsT8AAAAAAIDCvwAAAAAAAAAAAAAAAAAAlb8AAAAAAMC/PwAAAAAAgMS/AAAAAAAAhr8AAAAAAICgvwAAAAAAQL0/AAAAAAAAp78AAAAAAIC7PwAAAAAAALM/AAAAAAAAyj8AAAAAAACgvwAAAAAAALs/AAAAAAAArj8AAAAAAKDGPwAAAAAAAJ0/AAAAAADAwz8AAAAAAIC5PwAAAAAAQMs/AAAAAACAp78AAAAAAAC3PwAAAAAAAKc/AAAAAAAAxj8AAAAAAACzvwAAAAAAwLI/AAAAAACAoz8AAAAAAMDFPwAAAAAAgKa/AAAAAABAtT8AAAAAAICiPwAAAAAAwMQ/AAAAAAAApL8AAAAAAAC5PwAAAAAAAKs/AAAAAACAxj8AAAAAAACcPwAAAAAAAMQ/AAAAAADAuj8AAAAAAGDMPwAAAAAAAHg/AAAAAADAwD8AAAAAAAC0PwAAAAAAwMg/AAAAAAAAnb8AAAAAAMC7PwAAAAAAgK8/AAAAAACAxz8AAAAAAACiPwAAAAAA4MQ/AAAAAACAuz8AAAAAAODLPwAAAAAAwMy/AAAAAAAAs78AAAAAAMC0vwAAAAAAwLI/AAAAAAAAxb8AAAAAAACTvwAAAAAAAKO/AAAAAABAuz8AAAAAAKDQvwAAAAAAgLW/AAAAAADAtb8AAAAAAACyPwAAAAAAYMe/AAAAAAAAkb8AAAAAAACbvwAAAAAAAL8/AAAAAADAtL8AAAAAAICwPwAAAAAAAJw/AAAAAADgwz8AAAAAAACUvwAAAAAAwLw/AAAAAACArT8AAAAAAKDGPwAAAAAAAJi/AAAAAABAvD8AAAAAAACwPwAAAAAAwMc/AAAAAAAAoT8AAAAAAADDPwAAAAAAgLQ/AAAAAAAgyD8AAAAAAAC9vwAAAAAAAJs/AAAAAAAAgL8AAAAAAMC+PwAAAAAAAL2/AAAAAAAAnD8AAAAAAAAAAAAAAAAAAME/AAAAAACgw78AAAAAAAB4vwAAAAAAAKe/AAAAAAAAtz8AAAAAAEDXvwAAAAAAAMe/AAAAAAAAxb8AAAAAAABwPwAAAAAAoMq/AAAAAAAArr8AAAAAAAC0vwAAAAAAwLE/AAAAAADAv78AAAAAAACQPwAAAAAAAJW/AAAAAABAvT8AAAAAAKDSvwAAAAAAQMC/AAAAAAAAwb8AAAAAAACaPwAAAAAAkNK/AAAAAAAAu78AAAAAAIC2vwAAAAAAQLM/AAAAAAAg2b8AAAAAACDIvwAAAAAAgMO/AAAAAAAAmz8AAAAAAGDEvwAAAAAAAFC/AAAAAAAAnb8AAAAAAAC7PwAAAAAAAJ+/AAAAAACAuj8AAAAAAICrPwAAAAAAAMY/AAAAAAAAgD8AAAAAAIDAPwAAAAAAQLA/AAAAAADAxj8AAAAAAADFvwAAAAAAAIC/AAAAAAAAjL8AAAAAAEDBPwAAAAAAUNa/AAAAAAAAxL8AAAAAAGDAvwAAAAAAgKc/AAAAAABgwL8AAAAAAACaPwAAAAAAAIC/AAAAAACAvz8AAAAAAACEvwAAAAAAgL4/AAAAAACAsD8AAAAAAADHPwAAAAAAAIw/AAAAAABAwT8AAAAAAECyPwAAAAAAwMY/AAAAAACAxr8AAAAAAACQvwAAAAAAAJS/AAAAAACAwD8AAAAAAHDWvwAAAAAAwMO/AAAAAABgwL8AAAAAAICkPwAAAAAAoMC/AAAAAAAAlj8AAAAAAACEvwAAAAAAAL8/AAAAAAAAiL8AAAAAAEC+PwAAAAAAgK0/AAAAAABgxj8AAAAAAAAAAAAAAAAAAL8/AAAAAACArz8AAAAAAGDGPwAAAAAAQMa/AAAAAAAAlb8AAAAAAACZvwAAAAAAwL8/AAAAAABg1r8AAAAAAKDDvwAAAAAAQMC/AAAAAACApz8AAAAAAODAvwAAAAAAAJM/AAAAAAAAkL8AAAAAAEC+PwAAAAAAAKC/AAAAAACAuT8AAAAAAICpPwAAAAAAYMQ/AAAAAAAAkb8AAAAAAMC7PwAAAAAAgKo/AAAAAACAxT8AAAAAAGDGvwAAAAAAAJC/AAAAAAAAmL8AAAAAAIC/PwAAAAAAgNa/AAAAAADgw78AAAAAAIDAvwAAAAAAgKU/AAAAAAAgwb8AAAAAAACRPwAAAAAAAJa/AAAAAAAAvT8AAAAAAACMvwAAAAAAQL0/AAAAAACArj8AAAAAAIDGPwAAAAAAAIA/AAAAAADAvz8AAAAAAACwPwAAAAAAoMY/AAAAAACAxr8AAAAAAACQvwAAAAAAAJa/AAAAAAAgwD8AAAAAALDXvwAAAAAA4MW/AAAAAAAgwr8AAAAAAACiPwAAAAAAoMK/AAAAAAAAfD8AAAAAAACZvwAAAAAAwLo/AAAAAAAAlb8AAAAAAIC8PwAAAAAAAK0/AAAAAABAxj8AAAAAAAB4PwAAAAAAIMA/AAAAAACArT8AAAAAAADGPwAAAAAAAMe/AAAAAAAAlb8AAAAAAACavwAAAAAAwL8/AAAAAACQ1r8AAAAAAIDEvwAAAAAAgMG/AAAAAACAoz8AAAAAAEDBvwAAAAAAAJE/AAAAAAAAkr8AAAAAAMC9PwAAAAAAAIq/AAAAAABAvD8AAAAAAICsPwAAAAAAAMY/AAAAAAAAhL8AAAAAAAC9PwAAAAAAAKs/AAAAAABgxT8AAAAAACDIvwAAAAAAAJ2/AAAAAACAoL8AAAAAAEC+PwAAAAAA8Na/AAAAAADAxL8AAAAAAEDBvwAAAAAAAKA/AAAAAABgwr8AAAAAAACAPwAAAAAAAJm/AAAAAADAuz8AAAAAAACdvwAAAAAAgLo/AAAAAAAApj8AAAAAAODEPwAAAAAAAHy/AAAAAADAvT8AAAAAAICrPwAAAAAAgMU/AAAAAACgxr8AAAAAAACYvwAAAAAAAJ6/AAAAAABAvj8AAAAAAODWvwAAAAAAgMS/AAAAAACAwb8AAAAAAICiPwAAAAAAoMK/AAAAAAAAaD8AAAAAAACdvwAAAAAAwLo/AAAAAAAAmr8AAAAAAIC6PwAAAAAAAKo/AAAAAABAxT8AAAAAAAB0vwAAAAAAwL0/AAAAAAAArD8AAAAAAIDFPwAAAAAAgMa/AAAAAAAAlL8AAAAAAACbvwAAAAAAwL0/AAAAAABA178AAAAAAIDFvwAAAAAA4MG/AAAAAACAoD8AAAAAAADCvwAAAAAAAIY/AAAAAAAAnr8AAAAAAMC6PwAAAAAAAJW/AAAAAACAuz8AAAAAAICqPwAAAAAAgMU/AAAAAAAAdL8AAAAAAEC8PwAAAAAAAKo/AAAAAAAAxT8AAAAAAKDJvwAAAAAAgKO/AAAAAACApb8AAAAAAIC7PwAAAAAAcNi/AAAAAADAx78AAAAAAMDDvwAAAAAAAJU/AAAAAADgwr8AAAAAAAB0PwAAAAAAAJy/AAAAAAAAuz8AAAAAAACYvwAAAAAAwLo/AAAAAAAAqj8AAAAAAEDFPwAAAAAAAIC/AAAAAAAAvT8AAAAAAICqPwAAAAAAgMQ/AAAAAABgx78AAAAAAACZvwAAAAAAAJ+/AAAAAAAAvj8AAAAAAODWvwAAAAAAAMW/AAAAAAAgwr8AAAAAAACgPwAAAAAAAMK/AAAAAAAAhj8AAAAAAACYvwAAAAAAwLs/AAAAAAAAnr8AAAAAAAC4PwAAAAAAgKU/AAAAAABgxD8AAAAAAACKvwAAAAAAwLw/AAAAAAAAqj8AAAAAAADFPwAAAAAAoMe/AAAAAAAAmr8AAAAAAICgvwAAAAAAAL4/AAAAAABQ178AAAAAAKDFvwAAAAAAYMK/AAAAAAAAnj8AAAAAACDEvwAAAAAAAGi/AAAAAAAAor8AAAAAAIC5PwAAAAAAAJ+/AAAAAACAuT8AAAAAAACnPwAAAAAAQMQ/AAAAAAAAgL8AAAAAAIC9PwAAAAAAgKo/AAAAAAAAxT8AAAAAAADHvwAAAAAAAJi/AAAAAACAor8AAAAAAIC8PwAAAAAAINi/AAAAAAAAx78AAAAAACDDvwAAAAAAAJk/AAAAAADAw78AAAAAAAB0vwAAAAAAgKK/AAAAAAAAuT8AAAAAAACbvwAAAAAAgLo/AAAAAACAqD8AAAAAAADFPwAAAAAAAIa/AAAAAADAvD8AAAAAAICpPwAAAAAAAMU/AAAAAAAAyL8AAAAAAACavwAAAAAAgKC/AAAAAADAvD8AAAAAAHDXvwAAAAAAoMW/AAAAAABAwr8AAAAAAACfPwAAAAAAQMK/AAAAAAAAgD8AAAAAAACavwAAAAAAwLk/AAAAAAAAoL8AAAAAAAC5PwAAAAAAAKY/AAAAAACAxD8AAAAAAACVvwAAAAAAwLo/AAAAAACAoz8AAAAAAODDPwAAAAAAIMm/AAAAAACAor8AAAAAAICkvwAAAAAAALw/AAAAAACA178AAAAAAIDGvwAAAAAA4MK/AAAAAAAAmz8AAAAAAKDCvwAAAAAAAHw/AAAAAAAAnL8AAAAAAMC6PwAAAAAAAKG/AAAAAACAuD8AAAAAAIClPwAAAAAAYMQ/AAAAAAAAgr8AAAAAAAC9PwAAAAAAAKo/AAAAAABgxD8AAAAAAADIvwAAAAAAAJy/AAAAAACAob8AAAAAAAC9PwAAAAAAQNe/AAAAAACAxb8AAAAAAIDCvwAAAAAAAJw/AAAAAABgxb8AAAAAAACEvwAAAAAAAKa/AAAAAAAAuD8AAAAAAICmvwAAAAAAgLY/AAAAAAAAnz8AAAAAAGDDPwAAAAAAAIy/AAAAAADAuz8AAAAAAACoPwAAAAAAoMQ/AAAAAAAgyL8AAAAAAIChvwAAAAAAgKS/AAAAAABAvD8AAAAAAADYvwAAAAAAwMa/AAAAAABAw78AAAAAAACYPwAAAAAAIMS/AAAAAAAAcL8AAAAAAACjvwAAAAAAQLk/AAAAAAAAnL8AAAAAAMC5PwAAAAAAgKc/AAAAAADgwz8AAAAAAACCvwAAAAAAgLw/AAAAAAAAqT8AAAAAAMDEPwAAAAAA4Mi/AAAAAAAAor8AAAAAAACmvwAAAAAAALs/AAAAAAAA2L8AAAAAAKDGvwAAAAAA4MK/AAAAAAAAmj8AAAAAAODCvwAAAAAAAHA/AAAAAACAoL8AAAAAAAC6PwAAAAAAAJq/AAAAAABAuj8AAAAAAACpPwAAAAAA4MQ/AAAAAAAAhL8AAAAAAMC6PwAAAAAAAKc/AAAAAAAgxD8AAAAAAADIvwAAAAAAAJy/AAAAAACAob8AAAAAAEC9PwAAAAAAsNe/AAAAAAAgxr8AAAAAAKDCvwAAAAAAAJw/AAAAAACAw78AAAAAAABQPwAAAAAAgKC/AAAAAABAuD8AAAAAAACpvwAAAAAAgLU/AAAAAAAAoD8AAAAAAIDDPwAAAAAAAJy/AAAAAAAAuT8AAAAAAAChPwAAAAAAYMM/AAAAAADAyL8AAAAAAICgvwAAAAAAgKK/AAAAAACAvD8AAAAAAIDXvwAAAAAAIMa/AAAAAAAAw78AAAAAAACaPwAAAAAAwMO/AAAAAAAAAAAAAAAAAAChvwAAAAAAgLk/AAAAAAAAnb8AAAAAAIC4PwAAAAAAgKU/AAAAAABgxD8AAAAAAACCvwAAAAAAwLw/AAAAAACAqT8AAAAAAODEPwAAAAAAQMi/AAAAAAAAn78AAAAAAACivwAAAAAAgLw/AAAAAAAQ2L8AAAAAAADHvwAAAAAAIMO/AAAAAAAAkz8AAAAAACDEvwAAAAAAAHC/AAAAAAAAor8AAAAAAAC5PwAAAAAAAJ6/AAAAAACAuT8AAAAAAACkPwAAAAAA4MM/AAAAAAAAkL8AAAAAAAC7PwAAAAAAgKY/AAAAAAAgxD8AAAAAAKDJvwAAAAAAgKa/AAAAAAAAqL8AAAAAAMC6PwAAAAAAkNi/AAAAAADAx78AAAAAACDEvwAAAAAAAJI/AAAAAAAgxL8AAAAAAAB0vwAAAAAAAKO/AAAAAADAuD8AAAAAAACgvwAAAAAAALk/AAAAAAAApj8AAAAAAIDEPwAAAAAAAJ+/AAAAAAAAuD8AAAAAAACiPwAAAAAAQMM/AAAAAAAgyr8AAAAAAIClvwAAAAAAgKe/AAAAAAAAuT8AAAAAAADYvwAAAAAAoMa/AAAAAAAgw78AAAAAAACYPwAAAAAAAMO/AAAAAAAAcD8AAAAAAICivwAAAAAAALk/AAAAAAAAor8AAAAAAIC4PwAAAAAAgKQ/AAAAAAAgxD8AAAAAAACIvwAAAAAAQLo/AAAAAAAApj8AAAAAAADEPwAAAAAAQMi/AAAAAAAAnr8AAAAAAACivwAAAAAAgLw/AAAAAAAQ2L8AAAAAAADHvwAAAAAAgMO/AAAAAAAAlz8AAAAAACDFvwAAAAAAAIS/AAAAAACApb8AAAAAAIC3PwAAAAAAAKi/AAAAAACAtT8AAAAAAACgPwAAAAAAYMM/AAAAAAAAkL8AAAAAAEC7PwAAAAAAAKc/AAAAAADgwz8AAAAAAGDIvwAAAAAAAJ+/AAAAAACAo78AAAAAAEC8PwAAAAAAANi/AAAAAACgxr8AAAAAAODDvwAAAAAAAJU/AAAAAADAw78AAAAAAABQvwAAAAAAgKG/AAAAAACAuT8AAAAAAACcvwAAAAAAgLg/AAAAAACApD8AAAAAAADEPwAAAAAAAIS/AAAAAABAvD8AAAAAAACoPwAAAAAAgMQ/AAAAAACAyr8AAAAAAACnvwAAAAAAgKm/AAAAAACAuT8AAAAAAGDYvwAAAAAAoMe/AAAAAADAw78=","dtype":"float64","shape":[1000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1048","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1042","type":"Title"},{"attributes":{},"id":"1046","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{"formatter":{"id":"1046","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1048","type":"UnionRenderers"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"b3f40c85-1008-443c-912a-ece88e7fa263","roots":{"1002":"1427c1d5-7969-4324-9967-d892ccbb9015"}}];
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
let’s modify it and see how the trace changes. Change when the loop ends
and save the file. Rebuild the file:


**In [11]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-base-lab2
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [11]:**



.. parsed-literal::

    rm -f -- simpleserial-base-CWLITEXMEGA.hex
    rm -f -- simpleserial-base-CWLITEXMEGA.eep
    rm -f -- simpleserial-base-CWLITEXMEGA.cof
    rm -f -- simpleserial-base-CWLITEXMEGA.elf
    rm -f -- simpleserial-base-CWLITEXMEGA.map
    rm -f -- simpleserial-base-CWLITEXMEGA.sym
    rm -f -- simpleserial-base-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- simpleserial-base.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- simpleserial-base.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: simpleserial-base-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEXMEGA.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output simpleserial-base-CWLITEXMEGA.elf -Wl,-Map=simpleserial-base-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEXMEGA.sym
    avr-nm -n simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       1722	     16	     52	   1790	    6fe	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    


Reprogram the target:


**In [12]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [12]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 1737 bytes
    


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
            <span id="1104">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="06b0f7f6-4fe1-48ca-b0b5-20a6900ad7d6"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#06b0f7f6-4fe1-48ca-b0b5-20a6900ad7d6');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof (root._bokeh_onload_callbacks) === "undefined" || force === true) {
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
        var el = document.getElementById("1104");
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
          root._bokeh_onload_callbacks.forEach(function(callback) { callback() });
        }
        finally {
          delete root._bokeh_onload_callbacks
        }
        console.info("Bokeh: all callbacks have finished");
      }
    
      function load_libs(js_urls, callback) {
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.log("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.log("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = js_urls.length;
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var s = document.createElement('script');
          s.src = url;
          s.async = false;
          s.onreadystatechange = s.onload = function() {
            root._bokeh_is_loading--;
            if (root._bokeh_is_loading === 0) {
              console.log("Bokeh: all BokehJS libraries loaded");
              run_callbacks()
            }
          };
          s.onerror = function() {
            console.warn("failed to load library " + url);
          };
          console.log("Bokeh: injecting script tag for BokehJS library: ", url);
          document.getElementsByTagName("head")[0].appendChild(s);
        }
      };var element = document.getElementById("1104");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1104' but no matching script tag was found. ")
        return false;
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.0.1.min.js"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
        }
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
          var cell = $(document.getElementById("1104")).parents('.cell').data().cell;
          cell.output_area.append_execute_result(NB_LOAD_WARNING)
        }
    
      }
    
      if (root._bokeh_is_loading === 0) {
        console.log("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(js_urls, function() {
          console.log("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="fd5a453e-7542-4d2f-82b9-657fc793dad4"></div>
    
    </div>



.. raw:: html

    

    <div id="074c1393-9b3c-4642-8317-8266bc5500d0"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#074c1393-9b3c-4642-8317-8266bc5500d0');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"e67ea339-2ad1-48cb-9381-0dd5fa4b7e31":{"roots":{"references":[{"attributes":{"below":[{"id":"1114","type":"LinearAxis"}],"left":[{"id":"1119","type":"LinearAxis"}],"renderers":[{"id":"1114","type":"LinearAxis"},{"id":"1118","type":"Grid"},{"id":"1119","type":"LinearAxis"},{"id":"1123","type":"Grid"},{"id":"1132","type":"BoxAnnotation"},{"id":"1142","type":"GlyphRenderer"}],"title":{"id":"1154","type":"Title"},"toolbar":{"id":"1130","type":"Toolbar"},"x_range":{"id":"1106","type":"DataRange1d"},"x_scale":{"id":"1110","type":"LinearScale"},"y_range":{"id":"1108","type":"DataRange1d"},"y_scale":{"id":"1112","type":"LinearScale"}},"id":"1105","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1124","type":"PanTool"},{"attributes":{},"id":"1159","type":"Selection"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1124","type":"PanTool"},{"id":"1125","type":"WheelZoomTool"},{"id":"1126","type":"BoxZoomTool"},{"id":"1127","type":"SaveTool"},{"id":"1128","type":"ResetTool"},{"id":"1129","type":"HelpTool"}]},"id":"1130","type":"Toolbar"},{"attributes":{},"id":"1128","type":"ResetTool"},{"attributes":{},"id":"1160","type":"UnionRenderers"},{"attributes":{"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1118","type":"Grid"},{"attributes":{},"id":"1115","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1129","type":"HelpTool"},{"attributes":{"formatter":{"id":"1156","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1114","type":"LinearAxis"},{"attributes":{"plot":null,"text":""},"id":"1154","type":"Title"},{"attributes":{"source":{"id":"1139","type":"ColumnDataSource"}},"id":"1143","type":"CDSView"},{"attributes":{},"id":"1120","type":"BasicTicker"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1159","type":"Selection"},"selection_policy":{"id":"1160","type":"UnionRenderers"}},"id":"1139","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1108","type":"DataRange1d"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1119","type":"LinearAxis"},{"attributes":{},"id":"1112","type":"LinearScale"},{"attributes":{"dimension":1,"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1123","type":"Grid"},{"attributes":{"data_source":{"id":"1139","type":"ColumnDataSource"},"glyph":{"id":"1140","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1141","type":"Line"},"selection_glyph":null,"view":{"id":"1143","type":"CDSView"}},"id":"1142","type":"GlyphRenderer"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1140","type":"Line"},{"attributes":{},"id":"1156","type":"BasicTickFormatter"},{"attributes":{},"id":"1127","type":"SaveTool"},{"attributes":{"overlay":{"id":"1132","type":"BoxAnnotation"}},"id":"1126","type":"BoxZoomTool"},{"attributes":{},"id":"1125","type":"WheelZoomTool"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1132","type":"BoxAnnotation"}],"root_ids":["1105"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"e67ea339-2ad1-48cb-9381-0dd5fa4b7e31","roots":{"1105":"fd5a453e-7542-4d2f-82b9-657fc793dad4"}}];
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
loop is run. Now let’s take a look at how different operations affect
power traces:

Comparing Operations
--------------------

To make things easy on us, let’s select operations that we would expect
to have very different power traces. One easy metric to base your
decision on is how long the associated instructions take to execute
(which is typically documented either in the datasheet, or in the core’s
documentation). For example, on XMEGA we’ll compare adding (1 cycle)
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

    rm -f -- simpleserial-base-CWLITEXMEGA.hex
    rm -f -- simpleserial-base-CWLITEXMEGA.eep
    rm -f -- simpleserial-base-CWLITEXMEGA.cof
    rm -f -- simpleserial-base-CWLITEXMEGA.elf
    rm -f -- simpleserial-base-CWLITEXMEGA.map
    rm -f -- simpleserial-base-CWLITEXMEGA.sym
    rm -f -- simpleserial-base-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- simpleserial-base.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- simpleserial-base.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: simpleserial-base-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEXMEGA.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output simpleserial-base-CWLITEXMEGA.elf -Wl,-Map=simpleserial-base-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEXMEGA.sym
    avr-nm -n simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       1722	     16	     52	   1790	    6fe	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    



**In [17]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [17]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 1737 bytes
    



**In [18]:**

.. code:: ipython3

    trace3 = cw.capture_trace(scope, target, text)
    bokeh_plot(trace3.wave)


**Out [18]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1216">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="a697efcc-9d07-4831-8caa-63b10f7652f2"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a697efcc-9d07-4831-8caa-63b10f7652f2');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof (root._bokeh_onload_callbacks) === "undefined" || force === true) {
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
        var el = document.getElementById("1216");
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
          root._bokeh_onload_callbacks.forEach(function(callback) { callback() });
        }
        finally {
          delete root._bokeh_onload_callbacks
        }
        console.info("Bokeh: all callbacks have finished");
      }
    
      function load_libs(js_urls, callback) {
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.log("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.log("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = js_urls.length;
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var s = document.createElement('script');
          s.src = url;
          s.async = false;
          s.onreadystatechange = s.onload = function() {
            root._bokeh_is_loading--;
            if (root._bokeh_is_loading === 0) {
              console.log("Bokeh: all BokehJS libraries loaded");
              run_callbacks()
            }
          };
          s.onerror = function() {
            console.warn("failed to load library " + url);
          };
          console.log("Bokeh: injecting script tag for BokehJS library: ", url);
          document.getElementsByTagName("head")[0].appendChild(s);
        }
      };var element = document.getElementById("1216");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1216' but no matching script tag was found. ")
        return false;
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.0.1.min.js"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
        }
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
          var cell = $(document.getElementById("1216")).parents('.cell').data().cell;
          cell.output_area.append_execute_result(NB_LOAD_WARNING)
        }
    
      }
    
      if (root._bokeh_is_loading === 0) {
        console.log("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(js_urls, function() {
          console.log("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="d2a0c40c-7a8a-4217-b0b9-9522517cc69e"></div>
    
    </div>



.. raw:: html

    

    <div id="a87edea7-97da-47a3-ac61-acd2ffc2347e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a87edea7-97da-47a3-ac61-acd2ffc2347e');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"f1ecbfaf-c56b-4345-93ae-7eb8faa8111b":{"roots":{"references":[{"attributes":{"below":[{"id":"1226","type":"LinearAxis"}],"left":[{"id":"1231","type":"LinearAxis"}],"renderers":[{"id":"1226","type":"LinearAxis"},{"id":"1230","type":"Grid"},{"id":"1231","type":"LinearAxis"},{"id":"1235","type":"Grid"},{"id":"1244","type":"BoxAnnotation"},{"id":"1254","type":"GlyphRenderer"}],"title":{"id":"1275","type":"Title"},"toolbar":{"id":"1242","type":"Toolbar"},"x_range":{"id":"1218","type":"DataRange1d"},"x_scale":{"id":"1222","type":"LinearScale"},"y_range":{"id":"1220","type":"DataRange1d"},"y_scale":{"id":"1224","type":"LinearScale"}},"id":"1217","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1227","type":"BasicTicker"},{"attributes":{"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1230","type":"Grid"},{"attributes":{"formatter":{"id":"1279","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1231","type":"LinearAxis"},{"attributes":{},"id":"1277","type":"BasicTickFormatter"},{"attributes":{},"id":"1232","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1218","type":"DataRange1d"},{"attributes":{"dimension":1,"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1235","type":"Grid"},{"attributes":{},"id":"1279","type":"BasicTickFormatter"},{"attributes":{"plot":null,"text":""},"id":"1275","type":"Title"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1253","type":"Line"},{"attributes":{},"id":"1280","type":"Selection"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1252","type":"Line"},{"attributes":{"callback":null},"id":"1220","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1277","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1226","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1251","type":"ColumnDataSource"},"glyph":{"id":"1252","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1253","type":"Line"},"selection_glyph":null,"view":{"id":"1255","type":"CDSView"}},"id":"1254","type":"GlyphRenderer"},{"attributes":{},"id":"1236","type":"PanTool"},{"attributes":{},"id":"1237","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1244","type":"BoxAnnotation"}},"id":"1238","type":"BoxZoomTool"},{"attributes":{},"id":"1239","type":"SaveTool"},{"attributes":{},"id":"1240","type":"ResetTool"},{"attributes":{},"id":"1241","type":"HelpTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1280","type":"Selection"},"selection_policy":{"id":"1281","type":"UnionRenderers"}},"id":"1251","type":"ColumnDataSource"},{"attributes":{},"id":"1281","type":"UnionRenderers"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1236","type":"PanTool"},{"id":"1237","type":"WheelZoomTool"},{"id":"1238","type":"BoxZoomTool"},{"id":"1239","type":"SaveTool"},{"id":"1240","type":"ResetTool"},{"id":"1241","type":"HelpTool"}]},"id":"1242","type":"Toolbar"},{"attributes":{},"id":"1224","type":"LinearScale"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1244","type":"BoxAnnotation"},{"attributes":{},"id":"1222","type":"LinearScale"},{"attributes":{"source":{"id":"1251","type":"ColumnDataSource"}},"id":"1255","type":"CDSView"}],"root_ids":["1217"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"f1ecbfaf-c56b-4345-93ae-7eb8faa8111b","roots":{"1217":"d2a0c40c-7a8a-4217-b0b9-9522517cc69e"}}];
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

    rm -f -- simpleserial-base-CWLITEXMEGA.hex
    rm -f -- simpleserial-base-CWLITEXMEGA.eep
    rm -f -- simpleserial-base-CWLITEXMEGA.cof
    rm -f -- simpleserial-base-CWLITEXMEGA.elf
    rm -f -- simpleserial-base-CWLITEXMEGA.map
    rm -f -- simpleserial-base-CWLITEXMEGA.sym
    rm -f -- simpleserial-base-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-base.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- simpleserial-base.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- simpleserial-base.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-base.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base.o.d simpleserial-base.c -o objdir/simpleserial-base.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: simpleserial-base-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-base.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial-base-CWLITEXMEGA.elf.d objdir/simpleserial-base.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output simpleserial-base-CWLITEXMEGA.elf -Wl,-Map=simpleserial-base-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-base-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-base-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-base-CWLITEXMEGA.elf simpleserial-base-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-base-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-base-CWLITEXMEGA.sym
    avr-nm -n simpleserial-base-CWLITEXMEGA.elf > simpleserial-base-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       1722	     16	     52	   1790	    6fe	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    



**In [20]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [20]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 1737 bytes
    



**In [21]:**

.. code:: ipython3

    trace4 = cw.capture_trace(scope, target, text)
    bokeh_plot(trace4.wave)


**Out [21]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1337">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="d9b9d8e9-de62-4e5f-b641-52af022bafc2"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#d9b9d8e9-de62-4e5f-b641-52af022bafc2');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof (root._bokeh_onload_callbacks) === "undefined" || force === true) {
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
        var el = document.getElementById("1337");
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
          root._bokeh_onload_callbacks.forEach(function(callback) { callback() });
        }
        finally {
          delete root._bokeh_onload_callbacks
        }
        console.info("Bokeh: all callbacks have finished");
      }
    
      function load_libs(js_urls, callback) {
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.log("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.log("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = js_urls.length;
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var s = document.createElement('script');
          s.src = url;
          s.async = false;
          s.onreadystatechange = s.onload = function() {
            root._bokeh_is_loading--;
            if (root._bokeh_is_loading === 0) {
              console.log("Bokeh: all BokehJS libraries loaded");
              run_callbacks()
            }
          };
          s.onerror = function() {
            console.warn("failed to load library " + url);
          };
          console.log("Bokeh: injecting script tag for BokehJS library: ", url);
          document.getElementsByTagName("head")[0].appendChild(s);
        }
      };var element = document.getElementById("1337");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1337' but no matching script tag was found. ")
        return false;
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.0.1.min.js"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
        }
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
          var cell = $(document.getElementById("1337")).parents('.cell').data().cell;
          cell.output_area.append_execute_result(NB_LOAD_WARNING)
        }
    
      }
    
      if (root._bokeh_is_loading === 0) {
        console.log("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(js_urls, function() {
          console.log("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="798564a2-4cb6-48e7-b153-6b73897048ad"></div>
    
    </div>



.. raw:: html

    

    <div id="491b488d-37ca-4285-a017-55a7bac0bfba"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#491b488d-37ca-4285-a017-55a7bac0bfba');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"1f7d179b-5f15-4787-abeb-95a63342a7ce":{"roots":{"references":[{"attributes":{"below":[{"id":"1347","type":"LinearAxis"}],"left":[{"id":"1352","type":"LinearAxis"}],"renderers":[{"id":"1347","type":"LinearAxis"},{"id":"1351","type":"Grid"},{"id":"1352","type":"LinearAxis"},{"id":"1356","type":"Grid"},{"id":"1365","type":"BoxAnnotation"},{"id":"1375","type":"GlyphRenderer"}],"title":{"id":"1405","type":"Title"},"toolbar":{"id":"1363","type":"Toolbar"},"x_range":{"id":"1339","type":"DataRange1d"},"x_scale":{"id":"1343","type":"LinearScale"},"y_range":{"id":"1341","type":"DataRange1d"},"y_scale":{"id":"1345","type":"LinearScale"}},"id":"1338","subtype":"Figure","type":"Plot"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1374","type":"Line"},{"attributes":{},"id":"1345","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1373","type":"Line"},{"attributes":{"data_source":{"id":"1372","type":"ColumnDataSource"},"glyph":{"id":"1373","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1374","type":"Line"},"selection_glyph":null,"view":{"id":"1376","type":"CDSView"}},"id":"1375","type":"GlyphRenderer"},{"attributes":{"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1351","type":"Grid"},{"attributes":{},"id":"1357","type":"PanTool"},{"attributes":{},"id":"1358","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1365","type":"BoxAnnotation"}},"id":"1359","type":"BoxZoomTool"},{"attributes":{},"id":"1410","type":"Selection"},{"attributes":{},"id":"1360","type":"SaveTool"},{"attributes":{},"id":"1411","type":"UnionRenderers"},{"attributes":{},"id":"1361","type":"ResetTool"},{"attributes":{},"id":"1362","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1357","type":"PanTool"},{"id":"1358","type":"WheelZoomTool"},{"id":"1359","type":"BoxZoomTool"},{"id":"1360","type":"SaveTool"},{"id":"1361","type":"ResetTool"},{"id":"1362","type":"HelpTool"}]},"id":"1363","type":"Toolbar"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1410","type":"Selection"},"selection_policy":{"id":"1411","type":"UnionRenderers"}},"id":"1372","type":"ColumnDataSource"},{"attributes":{},"id":"1353","type":"BasicTicker"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1365","type":"BoxAnnotation"},{"attributes":{"dimension":1,"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1356","type":"Grid"},{"attributes":{"callback":null},"id":"1341","type":"DataRange1d"},{"attributes":{"source":{"id":"1372","type":"ColumnDataSource"}},"id":"1376","type":"CDSView"},{"attributes":{"plot":null,"text":""},"id":"1405","type":"Title"},{"attributes":{},"id":"1343","type":"LinearScale"},{"attributes":{},"id":"1407","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1407","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1347","type":"LinearAxis"},{"attributes":{"formatter":{"id":"1409","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1352","type":"LinearAxis"},{"attributes":{},"id":"1409","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1339","type":"DataRange1d"},{"attributes":{},"id":"1348","type":"BasicTicker"}],"root_ids":["1338"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"1f7d179b-5f15-4787-abeb-95a63342a7ce","roots":{"1338":"798564a2-4cb6-48e7-b153-6b73897048ad"}}];
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

Now that we’re done with the tutorial, we’ll need to disconnect from
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
