
Large HW Swings
===============


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'CWNANO'
    PLATFORM = 'CWNANO'
    CRYPTO_TARGET = 'TINYAES128C'
    num_traces = 50


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWNANO.hex
    rm -f -- simpleserial-aes-CWNANO.eep
    rm -f -- simpleserial-aes-CWNANO.cof
    rm -f -- simpleserial-aes-CWNANO.elf
    rm -f -- simpleserial-aes-CWNANO.map
    rm -f -- simpleserial-aes-CWNANO.sym
    rm -f -- simpleserial-aes-CWNANO.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s stm32f0_hal_nano.s stm32f0_hal_lowlevel.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d stm32f0_hal_nano.d stm32f0_hal_lowlevel.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i stm32f0_hal_nano.i stm32f0_hal_lowlevel.i aes.i aes-independant.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f0_nano/stm32f0_hal_nano.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_nano.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_nano.o.d .././hal/stm32f0_nano/stm32f0_hal_nano.c -o objdir/stm32f0_hal_nano.o 
    .
    Compiling C: .././hal/stm32f0/stm32f0_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_lowlevel.o.d .././hal/stm32f0/stm32f0_hal_lowlevel.c -o objdir/stm32f0_hal_lowlevel.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Assembling: .././hal/stm32f0/stm32f0_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f0_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C .././hal/stm32f0/stm32f0_startup.S -o objdir/stm32f0_startup.o
    .
    Linking: simpleserial-aes-CWNANO.elf
    arm-none-eabi-gcc -mcpu=cortex-m0 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWNANO.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/stm32f0_hal_nano.o objdir/stm32f0_hal_lowlevel.o objdir/aes.o objdir/aes-independant.o objdir/stm32f0_startup.o --output simpleserial-aes-CWNANO.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f0_nano/LinkerScript.ld -Wl,--gc-sections -lm -mthumb -mcpu=cortex-m0  -Wl,-Map=simpleserial-aes-CWNANO.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWNANO.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWNANO.elf simpleserial-aes-CWNANO.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWNANO.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWNANO.elf simpleserial-aes-CWNANO.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWNANO.lss
    arm-none-eabi-objdump -h -S -z simpleserial-aes-CWNANO.elf > simpleserial-aes-CWNANO.lss
    .
    Creating Symbol Table: simpleserial-aes-CWNANO.sym
    arm-none-eabi-nm -n simpleserial-aes-CWNANO.elf > simpleserial-aes-CWNANO.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5012	    536	   1480	   7028	   1b74	simpleserial-aes-CWNANO.elf
    +--------------------------------------------------------
    + Built for platform CWNANO STM32F030
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

    Detected unknown STM32F ID: 0x444
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5547 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5547 bytes
    


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

    

    <div id="7dd91366-bf3b-4cfb-aa0e-32501ddc8174"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7dd91366-bf3b-4cfb-aa0e-32501ddc8174');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="0aa3607a-ba6e-4fb9-b8c2-023bf62f535e" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="7dad04fc-2cfd-40fb-a7fc-d6595232ffeb"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7dad04fc-2cfd-40fb-a7fc-d6595232ffeb');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"02cf0ecd-0915-49bb-9ea0-3df3f3f12e4d":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"02cf0ecd-0915-49bb-9ea0-3df3f3f12e4d","roots":{"1002":"0aa3607a-ba6e-4fb9-b8c2-023bf62f535e"}}];
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

    

    <div id="ca79f544-20bb-4d1b-844c-461cf53978af"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ca79f544-20bb-4d1b-844c-461cf53978af');
        
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

    Number of 0xFF: 23
    Number of 0x00: 27
    



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
        
    
    
    
    
    
      <div class="bk-root" id="b99009bb-4809-4f37-8519-951a8bb0535f" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="f5b5d7ba-c8e5-4ec6-b077-5b3640ba911c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#f5b5d7ba-c8e5-4ec6-b077-5b3640ba911c');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"3d08cf25-2d1f-447d-bdfc-f64383104db7":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1157","type":"Selection"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1157","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{},"id":"1108","type":"LinearScale"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"3d08cf25-2d1f-447d-bdfc-f64383104db7","roots":{"1103":"b99009bb-4809-4f37-8519-951a8bb0535f"}}];
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

