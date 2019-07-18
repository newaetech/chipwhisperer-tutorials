
Instruction Power Differences
=============================

This tutorial will introduce you to measuring the power consumption of a
device under attack. It will demonstrate how the power consumption of a
target changes based on what operations it’s doing.

If you haven’t yet, you should probably complete Tutorial B1, which
introduces building firmware, programming the target, and scripting.


**In [1]:**

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
        trig_count = 7459002
    clock = 
        adc_src       = clkgen_x4
        adc_phase     = 0
        adc_freq      = 29538471
        adc_rate      = 29538471.0
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

    

    <div id="8f97b516-711e-4c37-adda-f0effec1d91a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#8f97b516-711e-4c37-adda-f0effec1d91a');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="b1ba47ee-0367-46ad-a52c-a5448d29d4f5"></div>
    
    </div>



.. raw:: html

    

    <div id="060e64e7-848c-4997-828d-371e7dc816cc"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#060e64e7-848c-4997-828d-371e7dc816cc');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"2e431641-3877-4ae5-b3b6-c2360ef4f792":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1041","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1046","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1046","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1048","type":"Selection"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1049","type":"UnionRenderers"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"plot":null,"text":""},"id":"1041","type":"Title"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1048","type":"Selection"},"selection_policy":{"id":"1049","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"2e431641-3877-4ae5-b3b6-c2360ef4f792","roots":{"1002":"b1ba47ee-0367-46ad-a52c-a5448d29d4f5"}}];
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

    

    <div id="d43ef6d7-7d1b-4c06-992e-ed09c17794ae"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#d43ef6d7-7d1b-4c06-992e-ed09c17794ae');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="f9b796af-6b9a-4c60-b3f0-70aea2dbe026"></div>
    
    </div>



.. raw:: html

    

    <div id="71f2574d-e0d4-460a-add7-0bd64c839d72"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#71f2574d-e0d4-460a-add7-0bd64c839d72');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"d191169a-1588-4494-821f-bcecfaf7dc07":{"roots":{"references":[{"attributes":{"below":[{"id":"1114","type":"LinearAxis"}],"left":[{"id":"1119","type":"LinearAxis"}],"renderers":[{"id":"1114","type":"LinearAxis"},{"id":"1118","type":"Grid"},{"id":"1119","type":"LinearAxis"},{"id":"1123","type":"Grid"},{"id":"1132","type":"BoxAnnotation"},{"id":"1142","type":"GlyphRenderer"}],"title":{"id":"1153","type":"Title"},"toolbar":{"id":"1130","type":"Toolbar"},"x_range":{"id":"1106","type":"DataRange1d"},"x_scale":{"id":"1110","type":"LinearScale"},"y_range":{"id":"1108","type":"DataRange1d"},"y_scale":{"id":"1112","type":"LinearScale"}},"id":"1105","subtype":"Figure","type":"Plot"},{"attributes":{"dimension":1,"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1123","type":"Grid"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1132","type":"BoxAnnotation"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1140","type":"Line"},{"attributes":{},"id":"1128","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{},"id":"1127","type":"SaveTool"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1160","type":"Selection"},"selection_policy":{"id":"1161","type":"UnionRenderers"}},"id":"1139","type":"ColumnDataSource"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{},"id":"1129","type":"HelpTool"},{"attributes":{"callback":null},"id":"1108","type":"DataRange1d"},{"attributes":{},"id":"1115","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1153","type":"Title"},{"attributes":{"formatter":{"id":"1156","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1114","type":"LinearAxis"},{"attributes":{},"id":"1112","type":"LinearScale"},{"attributes":{},"id":"1156","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1139","type":"ColumnDataSource"},"glyph":{"id":"1140","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1141","type":"Line"},"selection_glyph":null,"view":{"id":"1143","type":"CDSView"}},"id":"1142","type":"GlyphRenderer"},{"attributes":{},"id":"1120","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1119","type":"LinearAxis"},{"attributes":{"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1118","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1124","type":"PanTool"},{"id":"1125","type":"WheelZoomTool"},{"id":"1126","type":"BoxZoomTool"},{"id":"1127","type":"SaveTool"},{"id":"1128","type":"ResetTool"},{"id":"1129","type":"HelpTool"}]},"id":"1130","type":"Toolbar"},{"attributes":{"source":{"id":"1139","type":"ColumnDataSource"}},"id":"1143","type":"CDSView"},{"attributes":{"overlay":{"id":"1132","type":"BoxAnnotation"}},"id":"1126","type":"BoxZoomTool"},{"attributes":{},"id":"1161","type":"UnionRenderers"},{"attributes":{},"id":"1125","type":"WheelZoomTool"},{"attributes":{},"id":"1124","type":"PanTool"},{"attributes":{},"id":"1160","type":"Selection"}],"root_ids":["1105"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"d191169a-1588-4494-821f-bcecfaf7dc07","roots":{"1105":"f9b796af-6b9a-4c60-b3f0-70aea2dbe026"}}];
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

    

    <div id="0f743587-712e-4ec3-97cd-5ee7c4703d8f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0f743587-712e-4ec3-97cd-5ee7c4703d8f');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="dcd309b7-160b-4e68-891e-38ed7f531a2d"></div>
    
    </div>



.. raw:: html

    

    <div id="8fe77dcc-31ce-45f7-bea3-ccef85e65614"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#8fe77dcc-31ce-45f7-bea3-ccef85e65614');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"babccd82-5951-4c6c-b0f1-3f92fde78d4f":{"roots":{"references":[{"attributes":{"below":[{"id":"1226","type":"LinearAxis"}],"left":[{"id":"1231","type":"LinearAxis"}],"renderers":[{"id":"1226","type":"LinearAxis"},{"id":"1230","type":"Grid"},{"id":"1231","type":"LinearAxis"},{"id":"1235","type":"Grid"},{"id":"1244","type":"BoxAnnotation"},{"id":"1254","type":"GlyphRenderer"}],"title":{"id":"1274","type":"Title"},"toolbar":{"id":"1242","type":"Toolbar"},"x_range":{"id":"1218","type":"DataRange1d"},"x_scale":{"id":"1222","type":"LinearScale"},"y_range":{"id":"1220","type":"DataRange1d"},"y_scale":{"id":"1224","type":"LinearScale"}},"id":"1217","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1224","type":"LinearScale"},{"attributes":{"callback":null},"id":"1220","type":"DataRange1d"},{"attributes":{},"id":"1227","type":"BasicTicker"},{"attributes":{},"id":"1222","type":"LinearScale"},{"attributes":{"formatter":{"id":"1277","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1226","type":"LinearAxis"},{"attributes":{"plot":null,"text":""},"id":"1274","type":"Title"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1281","type":"Selection"},"selection_policy":{"id":"1282","type":"UnionRenderers"}},"id":"1251","type":"ColumnDataSource"},{"attributes":{"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1230","type":"Grid"},{"attributes":{"formatter":{"id":"1279","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1231","type":"LinearAxis"},{"attributes":{"source":{"id":"1251","type":"ColumnDataSource"}},"id":"1255","type":"CDSView"},{"attributes":{},"id":"1277","type":"BasicTickFormatter"},{"attributes":{},"id":"1232","type":"BasicTicker"},{"attributes":{},"id":"1281","type":"Selection"},{"attributes":{"dimension":1,"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1235","type":"Grid"},{"attributes":{"callback":null},"id":"1218","type":"DataRange1d"},{"attributes":{},"id":"1282","type":"UnionRenderers"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1253","type":"Line"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1252","type":"Line"},{"attributes":{"data_source":{"id":"1251","type":"ColumnDataSource"},"glyph":{"id":"1252","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1253","type":"Line"},"selection_glyph":null,"view":{"id":"1255","type":"CDSView"}},"id":"1254","type":"GlyphRenderer"},{"attributes":{},"id":"1279","type":"BasicTickFormatter"},{"attributes":{},"id":"1236","type":"PanTool"},{"attributes":{},"id":"1237","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1244","type":"BoxAnnotation"}},"id":"1238","type":"BoxZoomTool"},{"attributes":{},"id":"1239","type":"SaveTool"},{"attributes":{},"id":"1240","type":"ResetTool"},{"attributes":{},"id":"1241","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1236","type":"PanTool"},{"id":"1237","type":"WheelZoomTool"},{"id":"1238","type":"BoxZoomTool"},{"id":"1239","type":"SaveTool"},{"id":"1240","type":"ResetTool"},{"id":"1241","type":"HelpTool"}]},"id":"1242","type":"Toolbar"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1244","type":"BoxAnnotation"}],"root_ids":["1217"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"babccd82-5951-4c6c-b0f1-3f92fde78d4f","roots":{"1217":"dcd309b7-160b-4e68-891e-38ed7f531a2d"}}];
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

    

    <div id="37b8d973-770f-4efc-8c67-4b367b1599ab"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#37b8d973-770f-4efc-8c67-4b367b1599ab');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="569e9343-f3fd-4be9-aad6-fddcf92e5e0b"></div>
    
    </div>



.. raw:: html

    

    <div id="bf98bec0-b331-4b3c-881b-53553f6ef533"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#bf98bec0-b331-4b3c-881b-53553f6ef533');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"1cd710e7-040c-4480-9397-e3391e2d1804":{"roots":{"references":[{"attributes":{"below":[{"id":"1347","type":"LinearAxis"}],"left":[{"id":"1352","type":"LinearAxis"}],"renderers":[{"id":"1347","type":"LinearAxis"},{"id":"1351","type":"Grid"},{"id":"1352","type":"LinearAxis"},{"id":"1356","type":"Grid"},{"id":"1365","type":"BoxAnnotation"},{"id":"1375","type":"GlyphRenderer"}],"title":{"id":"1404","type":"Title"},"toolbar":{"id":"1363","type":"Toolbar"},"x_range":{"id":"1339","type":"DataRange1d"},"x_scale":{"id":"1343","type":"LinearScale"},"y_range":{"id":"1341","type":"DataRange1d"},"y_scale":{"id":"1345","type":"LinearScale"}},"id":"1338","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1353","type":"BasicTicker"},{"attributes":{"dimension":1,"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1356","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1374","type":"Line"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1373","type":"Line"},{"attributes":{"data_source":{"id":"1372","type":"ColumnDataSource"},"glyph":{"id":"1373","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1374","type":"Line"},"selection_glyph":null,"view":{"id":"1376","type":"CDSView"}},"id":"1375","type":"GlyphRenderer"},{"attributes":{},"id":"1357","type":"PanTool"},{"attributes":{"formatter":{"id":"1409","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1352","type":"LinearAxis"},{"attributes":{},"id":"1358","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1365","type":"BoxAnnotation"}},"id":"1359","type":"BoxZoomTool"},{"attributes":{},"id":"1343","type":"LinearScale"},{"attributes":{},"id":"1360","type":"SaveTool"},{"attributes":{},"id":"1345","type":"LinearScale"},{"attributes":{},"id":"1361","type":"ResetTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999],"y":{"__ndarray__":"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","dtype":"float64","shape":[1000]}},"selected":{"id":"1411","type":"Selection"},"selection_policy":{"id":"1412","type":"UnionRenderers"}},"id":"1372","type":"ColumnDataSource"},{"attributes":{},"id":"1362","type":"HelpTool"},{"attributes":{"callback":null},"id":"1341","type":"DataRange1d"},{"attributes":{"source":{"id":"1372","type":"ColumnDataSource"}},"id":"1376","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1357","type":"PanTool"},{"id":"1358","type":"WheelZoomTool"},{"id":"1359","type":"BoxZoomTool"},{"id":"1360","type":"SaveTool"},{"id":"1361","type":"ResetTool"},{"id":"1362","type":"HelpTool"}]},"id":"1363","type":"Toolbar"},{"attributes":{},"id":"1412","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1365","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1339","type":"DataRange1d"},{"attributes":{},"id":"1348","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1404","type":"Title"},{"attributes":{"formatter":{"id":"1407","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1347","type":"LinearAxis"},{"attributes":{},"id":"1411","type":"Selection"},{"attributes":{},"id":"1407","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1351","type":"Grid"},{"attributes":{},"id":"1409","type":"BasicTickFormatter"}],"root_ids":["1338"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"1cd710e7-040c-4480-9397-e3391e2d1804","roots":{"1338":"569e9343-f3fd-4be9-aad6-fddcf92e5e0b"}}];
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
