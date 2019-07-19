
Large HW Swings
===============


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'TINYAES128C'
    num_traces = 50


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEARM.hex
    rm -f -- simpleserial-aes-CWLITEARM.eep
    rm -f -- simpleserial-aes-CWLITEARM.cof
    rm -f -- simpleserial-aes-CWLITEARM.elf
    rm -f -- simpleserial-aes-CWLITEARM.map
    rm -f -- simpleserial-aes-CWLITEARM.sym
    rm -f -- simpleserial-aes-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i aes.i aes-independant.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-aes-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEARM.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/aes.o objdir/aes-independant.o objdir/stm32f3_startup.o --output simpleserial-aes-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-aes-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5348	    532	   1484	   7364	   1cc4	simpleserial-aes-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



Data Tracing Theory
-------------------

In the last tutorial, we saw how the power measurement of a device is
related to the Hamming weight. Let's use this to see where some
arbitrary data is processed by a device. We'll later expand on this to
perform a test that also takes into account noise.

Our objective is simple - we'll send in some data with all 1's in one
location, and then some data with all 0's.

Capturing Power Traces
----------------------

Capturing power traces will be very similar to previous tutorials,
except this time we'll be using a loop to capture multiple traces, as
well as numpy to store them.

Setup
~~~~~

We'll use some helper scripts to make setup and programming easier. If
you're using an XMEGA or STM (CWLITEARM) target, binaries with the
correct should be setup for you:


**In [3]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = '../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex'.format(PLATFORM)


**In [5]:**

.. code:: ipython3

    # program the target
    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5879 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5879 bytes
    


Capturing Traces
~~~~~~~~~~~~~~~~

Below you can see the capture loop. The main body of the loop loads some
new plaintext, arms the scope, sends the key and plaintext, then finally
records and appends our new trace to the ``traces[]`` list. At the end,
we convert the trace data to numpy arrays, since that's what we'll be
using for analysis.


**In [6]:**

.. code:: ipython3

    #Capture Traces
    from tqdm import tnrange
    import numpy as np
    import time
    
    ktp = cw.ktp.Basic()
    
    traces = []
    
    for i in tnrange(num_traces, desc='Capturing traces'):
        key, text = ktp.next()
        
        #Currently ALL bits are random. Let's extend bit 0 to a full byte to give us a random 0xFF or 0x00
        if text[0] & 0x01:
            text[0] = 0xFF
        else:
            text[0] = 0x00
        
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        traces.append(trace)


**Out [6]:**






Now that we have our traces, we can also plot them using Bokeh:


**In [7]:**

.. code:: ipython3

    from bokeh.plotting import figure, show
    from bokeh.io import output_notebook
    
    output_notebook()
    p = figure()
    
    xrange = range(len(traces[0].wave))
    p.line(xrange, traces[2].wave, line_color="red")
    show(p)


**Out [7]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1001">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="ccbbb948-9894-4668-8525-9350d0f217a7"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ccbbb948-9894-4668-8525-9350d0f217a7');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="b6552087-e1d6-49e5-8914-24fd91df65e3" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="3404848d-f740-4a13-8063-726a2de8c406"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3404848d-f740-4a13-8063-726a2de8c406');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"85ef4db8-2707-4a59-8e42-9299698b2b0d":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"text":""},"id":"1039","type":"Title"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"85ef4db8-2707-4a59-8e42-9299698b2b0d","roots":{"1002":"b6552087-e1d6-49e5-8914-24fd91df65e3"}}];
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


**In [8]:**

.. code:: ipython3

    # cleanup the connection to the target and scope
    scope.dis()
    target.dis()

Trace Analysis
--------------

Comparing 0xFF to 0x00
~~~~~~~~~~~~~~~~~~~~~~

Now that we have some traces, let's look at what we've actually
recorded. We'll be doing the following tasks:

1. Seperate traces into two groups: 0x00, and 0xFF
2. Make an average of each group.
3. Subtract the two averages and see the difference.

This will be shown in the following two cells. Note the number of 0xFF
and 0x00 isn't exactly 50/50. That is why we need to ensure we average
them.


**In [9]:**

.. code:: ipython3

    from bokeh.plotting import figure, show
    from bokeh.io import output_notebook
    import numpy as np
    
    output_notebook()
    p = figure()
    
    one_list = []
    zero_list = []
    
    for trace in traces:
        if trace.textin[0] == 0x00:
            one_list.append(trace.wave)
        else:
            zero_list.append(trace.wave)
    
    print("Number of 0xFF: " + str(len(one_list)))
    print("Number of 0x00: " + str(len(zero_list)))


**Out [9]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1102">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="ac96b867-27ea-4610-9598-84cb30e75bf2"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ac96b867-27ea-4610-9598-84cb30e75bf2');
        
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



.. parsed-literal::

    Number of 0xFF: 26
    Number of 0x00: 24
    



**In [10]:**

.. code:: ipython3

    one_avg = np.asarray(one_list).mean(axis=0)
    zero_avg = np.asarray(zero_list).mean(axis=0)
    
    diff = one_avg - zero_avg
    
    p.line(range(0, len(traces[0].wave)), diff)
    
    show(p)


**Out [10]:**


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="b895b262-892e-486f-b86c-ecdc4c4bfa59" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="42055683-a7b0-4e1d-a807-3882c025902b"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#42055683-a7b0-4e1d-a807-3882c025902b');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"25558589-0762-4be4-a1f9-ce3048114f04":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1149","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"text":""},"id":"1149","type":"Title"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1157","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1157","type":"Selection"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"25558589-0762-4be4-a1f9-ce3048114f04","roots":{"1103":"b895b262-892e-486f-b86c-ecdc4c4bfa59"}}];
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

Notice the large spike as the data is handled. You could also try some
of the following if you have time:

-  Force the OUTPUT data to all 0xFFs or all 0x00s
-  Force some intermediate value of 0xFFs or all 0x00s
-  Set multiple bytes to 0xFF vs 0x00
-  Plot each byte in sequence to see the data movement

Tests
-----


**In [11]:**

.. code:: ipython3

    assert (max(abs(diff)) > 0.01), "Low max difference of {} between 0x00 and 0xFF".format(max(abs(diff)))


**In [ ]:**

