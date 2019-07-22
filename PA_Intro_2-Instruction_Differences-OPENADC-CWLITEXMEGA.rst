
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
    PLATFORM = 'CWLITEXMEGA'

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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       1798	     16	     52	   1866	    74a	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       1798	     16	     52	   1866	    74a	simpleserial-base-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 1813 bytes
    


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
        trig_count = 16017882
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

    

    <div id="a44ecae9-58c5-450d-82e2-f1c43c53bc0c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a44ecae9-58c5-450d-82e2-f1c43c53bc0c');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="c025824b-a20d-4f49-8f71-581903bafd93" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="73898e19-41a7-46e1-bcbb-c349264e0de2"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#73898e19-41a7-46e1-bcbb-c349264e0de2');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"e0fec5c9-eff7-4cef-be97-781df49a3fdf":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{},"id":"1047","type":"UnionRenderers"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1047","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1026","type":"HelpTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"e0fec5c9-eff7-4cef-be97-781df49a3fdf","roots":{"1002":"c025824b-a20d-4f49-8f71-581903bafd93"}}];
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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       1798	     16	     52	   1866	    74a	simpleserial-base-CWLITEXMEGA.elf
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
    Verified flash OK, 1813 bytes
    


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

    

    <div id="f5ef9af3-f0a5-4878-81e2-520244e55e0e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#f5ef9af3-f0a5-4878-81e2-520244e55e0e');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="ff7d7757-61a3-41f4-825e-62d9f49efdf7" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="75ca23fc-4fe6-4ad6-b4d5-e025a5d3db04"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#75ca23fc-4fe6-4ad6-b4d5-e025a5d3db04');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"f7669c9e-c938-4716-b83d-3a00e4b45009":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1152","type":"BasicTickFormatter"},{"attributes":{},"id":"1154","type":"BasicTickFormatter"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{},"id":"1156","type":"Selection"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1156","type":"Selection"},"selection_policy":{"id":"1157","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"formatter":{"id":"1154","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{},"id":"1157","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1152","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1126","type":"ResetTool"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"f7669c9e-c938-4716-b83d-3a00e4b45009","roots":{"1103":"ff7d7757-61a3-41f4-825e-62d9f49efdf7"}}];
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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       1798	     16	     52	   1866	    74a	simpleserial-base-CWLITEXMEGA.elf
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
    Verified flash OK, 1813 bytes
    



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

    

    <div id="4379dc3a-a63d-4c7b-9156-bc508af8bbac"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4379dc3a-a63d-4c7b-9156-bc508af8bbac');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="bbbc3e74-589f-4bee-8c90-7f7428629abc" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="663231f0-c182-4f40-8be5-e1bcdbca76be"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#663231f0-c182-4f40-8be5-e1bcdbca76be');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"559b2837-1d71-45a7-9746-4d0b61c1a308":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1269","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1271","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1273","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1275","type":"Selection"},"selection_policy":{"id":"1276","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{},"id":"1271","type":"BasicTickFormatter"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{},"id":"1273","type":"BasicTickFormatter"},{"attributes":{},"id":"1275","type":"Selection"},{"attributes":{"overlay":{"id":"1274","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{},"id":"1236","type":"ResetTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1274","type":"BoxAnnotation"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{"text":""},"id":"1269","type":"Title"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{},"id":"1276","type":"UnionRenderers"},{"attributes":{},"id":"1220","type":"LinearScale"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"559b2837-1d71-45a7-9746-4d0b61c1a308","roots":{"1213":"bbbc3e74-589f-4bee-8c90-7f7428629abc"}}];
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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       1798	     16	     52	   1866	    74a	simpleserial-base-CWLITEXMEGA.elf
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
    Verified flash OK, 1813 bytes
    



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

    

    <div id="11df46fe-7af8-495f-bcec-d829e0bc4ecb"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#11df46fe-7af8-495f-bcec-d829e0bc4ecb');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="051db9df-58f3-456b-b5f5-a1f1e130fc80" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="71b52138-1ed2-4fe7-8fc7-23576dc87d99"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#71b52138-1ed2-4fe7-8fc7-23576dc87d99');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"676ec9d6-612d-4d6c-a285-29e96561c088":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1397","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{},"id":"1404","type":"UnionRenderers"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1403","type":"Selection"},"selection_policy":{"id":"1404","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{"formatter":{"id":"1401","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{},"id":"1403","type":"Selection"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{},"id":"1399","type":"BasicTickFormatter"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{"overlay":{"id":"1402","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{"text":""},"id":"1397","type":"Title"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1399","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1402","type":"BoxAnnotation"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{},"id":"1401","type":"BasicTickFormatter"},{"attributes":{},"id":"1347","type":"BasicTicker"},{"attributes":{},"id":"1342","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"676ec9d6-612d-4d6c-a285-29e96561c088","roots":{"1332":"051db9df-58f3-456b-b5f5-a1f1e130fc80"}}];
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
