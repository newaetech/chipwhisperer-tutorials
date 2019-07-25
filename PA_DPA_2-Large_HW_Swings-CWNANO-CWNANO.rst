
Large HW Swings
===============

Supported setups:

SCOPES:

-  OPENADC
-  CWNANO

PLATFORMS:

-  CWLITEARM
-  CWLITEXMEGA
-  CWNANO


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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWNANO.elf simpleserial-aes-CWNANO.eep \|\| exit 0
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

    %run "Helper_Scripts/Setup_Generic.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = '../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex'.format(PLATFORM)


**In [5]:**

.. code:: ipython3

    # program the target
    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x445
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

    

    <div id="c95bc4fa-ecfe-4de2-b957-fbf8fade5031"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#c95bc4fa-ecfe-4de2-b957-fbf8fade5031');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="abb6cf47-3388-4b63-884e-3bf38ea0f1ad" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="2ea0e85f-e0ca-4bc2-bec0-19475cf2c0b8"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#2ea0e85f-e0ca-4bc2-bec0-19475cf2c0b8');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"503cbf4e-8d06-4506-bba6-43316835a4cc":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"503cbf4e-8d06-4506-bba6-43316835a4cc","roots":{"1002":"abb6cf47-3388-4b63-884e-3bf38ea0f1ad"}}];
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

    

    <div id="b6486e58-b0b6-4628-af85-0a6acedee3c7"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b6486e58-b0b6-4628-af85-0a6acedee3c7');
        
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

    Number of 0xFF: 30
    Number of 0x00: 20
    



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
        
    
    
    
    
    
      <div class="bk-root" id="c72be046-8a2a-4ae2-9c5b-153d6f3b603c" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="24c7be8a-7937-40b8-a8be-3af833a4249f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#24c7be8a-7937-40b8-a8be-3af833a4249f');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"03814b8a-699c-4912-8b2e-08a7da08d5de":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1149","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{},"id":"1155","type":"Selection"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"text":""},"id":"1149","type":"Title"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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zMxcP4AzMzMzM1M/gIiIiIiIaD+Ad3d3d3dHPwAUERERESE/gBERERERQT8AmJmZmZkpPwDNzMzMzFw/gFVVVVVVVT8AERERERExP0AiIiIiImq/ABERERERUb8AEBERERExPwDe3d3d3T2/ABERERERMT8AAAAAAAAAAAASERERESG/ABERERERQb8A3t3d3d1dvyAREREREWG/AM3MzMzMXL8AERERERExv6Cqqqqqqmq/IBERERERUb8AEhEREREhv8C7u7u7u2O/ABARERERET8AERERERFRP8C7u7u7u1u/gBERERERYb8AERERERExv6Cqqqqqqlq/AN7d3d3dPb8AVVVVVVVFv0AzMzMzM1M/EBERERERYb8Ad3d3d3dHvwDe3d3d3T0/wIiIiIiIYD8AmpmZmZkpP0AzMzMzM1M/gIiIiIiIYL+Ad3d3d3dXv4AREREREUG/AJqZmZmZKT9AMzMzMzNTP0BERERERGy/AAAAAAAAAAAAVVVVVVVFvwARERERETG/gHd3d3d3R78A3N3d3d09vwAQERERERG/wLu7u7u7W7+gqqqqqqpivwAzMzMzM1O/gIiIiIiIWL8AIiIiIiJSP0B3d3d3d2c/ALy7u7u7Sz8AEBERERERvwAzMzMzM0M/ABERERERMb9Ad3d3d3dXv2BmZmZmZla/wMzMzMzMXL8AERERERExvyAiIiIiImq/ABARERERIT+AqqqqqqpavwCamZmZmVm/gIiIiIiIaL8AERERERExvwDe3d3d3U2/AKuqqqqqWj9AVVVVVVVVPwDe3d3d3U2/AHh3d3d3Rz8AAAAAAAAAAAAAAAAAAAAAAFRVVVVVNT8AEBERERExPwASERERETG/QEREREREZD8A3d3d3d1NP2B3d3d3d1e/QCIiIiIiUr8AERERERExvwAQERERESE/ICIiIiIiar/Au7u7u7tbvyAiIiIiImq/wMzMzMzMZL/g3d3d3d1tvwAREREREXG/AAAAAAAAAAAAERERERExPwCcmZmZmSm/AFVVVVVVRT8AVVVVVVU1PwAREREREUG/gLu7u7u7S78AMzMzMzNDPwAAAAAAAGA/ADMzMzMzQz9Ad3d3d3dnPwAQERERESE/QFVVVVVVVT8AEhERERExP0AREREREVG/ABARERERIb+AmZmZmZlJPwAQERERERG/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u7tbvwDv7u7u7nK/ABQRERERET8AEBERERERvwARERERESE/AJqZmZmZKT8AERERERExvwAzMzMzM0O/AFZVVVVVNb8A3t3d3d09PwAAAAAAAFC/AN7d3d3dTb8AERERERExPwAREREREUG/gJmZmZmZOb8AmJmZmZkpvwARERERETG/gHd3d3d3V79AZmZmZmZWv0BERERERFS/AJiZmZmZOT8AEBERERERv2BVVVVVVVW/AAAAAAAAYL8A3t3d3d09v7CqqqqqqmK/QEREREREVL8AAAAAAABQP0BmZmZmZla/AN7d3d3dTT8AmpmZmZk5vwAQERERERG/ABARERERET/AqqqqqqpaPwCamZmZmTk/IDMzMzMzUz+AiIiIiIhYPwARERERETG/wN3d3d3dXb8AmJmZmZkpvwAQERERERE/ABERERERMb8AERERERExPwAREREREUG/AKuqqqqqYr8Ad3d3d3dHv2BERERERFS/AAAAAAAAYL8AFBEREREhPwAzMzMzM0M/gBERERERQb8AmpmZmZlJP4B3d3d3d1c/AJmZmZmZST8A3t3d3d1NvwAiIiIiIlK/wLu7u7u7Y78Ad3d3d3dXPwDNzMzMzFy/ABERERERMb8AAAAAAABQPwCamZmZmUm/gFVVVVVVbb8Ad3d3d3dHv4AzMzMzM1M/AAAAAAAAUD8AmpmZmZk5vwCZmZmZmUm/QGZmZmZmZr/Au7u7u7tbvwAREREREVG/wMzMzMzMZL8AEBEREREhPwDe3d3d3T0/gGZmZmZmVr9ARERERERkvwAQERERESE/ABARERERIb8AVFVVVVU1PwAREREREVG/wMzMzMzMXD8AVlVVVVU1PwBVVVVVVUU/AJmZmZmZST8AVlVVVVVFv4CIiIiIiFi/gKqqqqqqWr+AVVVVVVVFPwAREREREWk/wLu7u7u7Sz+AZmZmZmZWvwCZmZmZmTm/YEREREREZL/g3d3d3d1lv8Cqqqqqqlq/AAAAAAAAUL+AqqqqqqpaP8C7u7u7u2M/AJmZmZmZOT9AIiIiIiJiPyAREREREVG/oJmZmZmZYT8A7+7u7u5ePwAiIiIiImq/AAAAAAAAYL+Au7u7u7tLP4B3d3d3d0e/ABARERERET+AVVVVVVVVPwDd3d3d3T0/IBERERERaT/Au7u7u7tjPwAQERERERG/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","dtype":"float64","shape":[5000]}},"selected":{"id":"1155","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"03814b8a-699c-4912-8b2e-08a7da08d5de","roots":{"1103":"c72be046-8a2a-4ae2-9c5b-153d6f3b603c"}}];
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

