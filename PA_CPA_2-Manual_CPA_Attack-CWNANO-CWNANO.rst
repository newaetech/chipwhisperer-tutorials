
Manual CPA Attack
=================

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
    num_traces = 100
    CHECK_CORR = False


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



The CPA Attack Theory
---------------------

As a background on the CPA attack, please see the section `Correlation
Power Analysis <https://wiki.newae.com/Correlation_Power_Analysis>`__.
It's also suggested that you complete Tutorial B1, since that will
introduce Jupyter and show how to interface with ChipWhipserer using
Python. It's assumed you've read that section and completed the tutorial
and come back to this. Ok, you've done that? Good let's continue.

Assuming you **actually** read that, it should be apparent that there is
a few things we need to accomplish:

1. Getting some power traces of our target while it's performing AES
   encryption.
2. Reading the data, which consists of the analog waveform (trace) and
   input text sent to the encryption core
3. Making the power leakage model, where it takes a known input text
   along with a guess of the key byte
4. Implementing the correlation equation, and then looping through all
   the traces
5. Ranking the output of the correlation equation to determine the most
   likely key.

This tutorial will deal with both recording power traces using
ChipWhisperer and breaking them using a CPA attack.

Capturing Power Traces
----------------------

Capturing power traces will be very similar to previous tutorials,
except this time we'll be using a loop to capture multiple traces, as
well as numpy to store them. It's not necessary, but we'll also plot the
trace we get using ``bokeh``.

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
        key, text = ktp.next()  # manual creation of a key, text pair can be substituted here
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        traces.append(trace)
    
    #Convert traces to numpy arrays
    trace_array = np.asarray([trace.wave for trace in traces])  # if you prefer to work with numpy array for number crunching
    textin_array = np.asarray([trace.textin for trace in traces])
    known_keys = np.asarray([trace.key for trace in traces])  # for fixed key, these keys are all the same


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

    

    <div id="e7a9726f-5cfd-42da-b4d3-4114b6edcffb"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#e7a9726f-5cfd-42da-b4d3-4114b6edcffb');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="a59a6431-7cda-4e56-9965-b9b9a9e22996" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="440a1c18-db46-49ae-b6ce-6eb9d17daef7"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#440a1c18-db46-49ae-b6ce-6eb9d17daef7');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"97f12438-2318-4fc7-80b1-da5bdd79bca2":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"97f12438-2318-4fc7-80b1-da5bdd79bca2","roots":{"1002":"a59a6431-7cda-4e56-9965-b9b9a9e22996"}}];
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

Using the Trace Data
~~~~~~~~~~~~~~~~~~~~

Now that we have some traces, let's look at what we've actually
recorded. Looking at the earlier parts of the script, we can see that
the trace data is in ``trace_array``, while ``textin_array`` stores what
we sent to our target to be encrypted. For now, let's get some basic
information (the total number of traces, as well as the number of sample
points in each trace) about the traces, since we'll need that later:


**In [9]:**

.. code:: ipython3

    numtraces = np.shape(trace_array)[0] #total number of traces
    numpoints = np.shape(trace_array)[1] #samples per trace

For the analysis, we'll need to loop over every byte in the key we want
to attack, as well as every trace:

.. code:: python

    for bnum in range(0, 16):
        for tnum in range(0, numtraces):
            pass

Though we didn't loop over them, note that each trace is made up of a
bunch of sample points. Let's take a closer look at AES so that we can
replace that ``pass`` with some actual code.

Calculating Correlation
~~~~~~~~~~~~~~~~~~~~~~~

Now that we have some power traces of our target that we can use, we can
move on to the next steps of our attack. Looking way back to how AES
works, remember we are effectively attemping to target the position at
the bottom of this figure:

.. figure:: https://wiki.newae.com/images/7/71/Sbox_cpa_detail.png
   :alt: title

   title

The objective is thus to determine the output of the S-Box, where the
S-Box is defined as follows:


**In [10]:**

.. code:: ipython3

    sbox = (
        0x63, 0x7c, 0x77, 0x7b, 0xf2, 0x6b, 0x6f, 0xc5, 0x30, 0x01, 0x67, 0x2b, 0xfe, 0xd7, 0xab, 0x76,
        0xca, 0x82, 0xc9, 0x7d, 0xfa, 0x59, 0x47, 0xf0, 0xad, 0xd4, 0xa2, 0xaf, 0x9c, 0xa4, 0x72, 0xc0,
        0xb7, 0xfd, 0x93, 0x26, 0x36, 0x3f, 0xf7, 0xcc, 0x34, 0xa5, 0xe5, 0xf1, 0x71, 0xd8, 0x31, 0x15,
        0x04, 0xc7, 0x23, 0xc3, 0x18, 0x96, 0x05, 0x9a, 0x07, 0x12, 0x80, 0xe2, 0xeb, 0x27, 0xb2, 0x75,
        0x09, 0x83, 0x2c, 0x1a, 0x1b, 0x6e, 0x5a, 0xa0, 0x52, 0x3b, 0xd6, 0xb3, 0x29, 0xe3, 0x2f, 0x84,
        0x53, 0xd1, 0x00, 0xed, 0x20, 0xfc, 0xb1, 0x5b, 0x6a, 0xcb, 0xbe, 0x39, 0x4a, 0x4c, 0x58, 0xcf,
        0xd0, 0xef, 0xaa, 0xfb, 0x43, 0x4d, 0x33, 0x85, 0x45, 0xf9, 0x02, 0x7f, 0x50, 0x3c, 0x9f, 0xa8,
        0x51, 0xa3, 0x40, 0x8f, 0x92, 0x9d, 0x38, 0xf5, 0xbc, 0xb6, 0xda, 0x21, 0x10, 0xff, 0xf3, 0xd2,
        0xcd, 0x0c, 0x13, 0xec, 0x5f, 0x97, 0x44, 0x17, 0xc4, 0xa7, 0x7e, 0x3d, 0x64, 0x5d, 0x19, 0x73,
        0x60, 0x81, 0x4f, 0xdc, 0x22, 0x2a, 0x90, 0x88, 0x46, 0xee, 0xb8, 0x14, 0xde, 0x5e, 0x0b, 0xdb,
        0xe0, 0x32, 0x3a, 0x0a, 0x49, 0x06, 0x24, 0x5c, 0xc2, 0xd3, 0xac, 0x62, 0x91, 0x95, 0xe4, 0x79,
        0xe7, 0xc8, 0x37, 0x6d, 0x8d, 0xd5, 0x4e, 0xa9, 0x6c, 0x56, 0xf4, 0xea, 0x65, 0x7a, 0xae, 0x08,
        0xba, 0x78, 0x25, 0x2e, 0x1c, 0xa6, 0xb4, 0xc6, 0xe8, 0xdd, 0x74, 0x1f, 0x4b, 0xbd, 0x8b, 0x8a,
        0x70, 0x3e, 0xb5, 0x66, 0x48, 0x03, 0xf6, 0x0e, 0x61, 0x35, 0x57, 0xb9, 0x86, 0xc1, 0x1d, 0x9e,
        0xe1, 0xf8, 0x98, 0x11, 0x69, 0xd9, 0x8e, 0x94, 0x9b, 0x1e, 0x87, 0xe9, 0xce, 0x55, 0x28, 0xdf,
        0x8c, 0xa1, 0x89, 0x0d, 0xbf, 0xe6, 0x42, 0x68, 0x41, 0x99, 0x2d, 0x0f, 0xb0, 0x54, 0xbb, 0x16)

Thus we need to write a function taking a single byte of input, a single
byte of the guessed key, and return the output of the S-Box:


**In [11]:**

.. code:: ipython3

    def intermediate(pt, keyguess):
        return sbox[pt ^ keyguess]

Finally, remember we want the Hamming Weight of the guess. Our
assumption is that the system is leaking the Hamming Weight of the
output of that S-Box. As a dumb solution, we could first convert every
number to binary and count the 1's:

.. code:: python

    >>> bin(0x1F)
    '0b11111'
    >>> bin(0x1F).count('1')
    5

This will ultimately be fairly slow. Instead we make a lookup table
using this idea:


**In [12]:**

.. code:: ipython3

    HW = [bin(n).count("1") for n in range(0, 256)]

Performing the Check
~~~~~~~~~~~~~~~~~~~~

Remember the objective is to calculate the following:

.. math:: r_{i,j} = \frac{\sum_{d=1}^{D}[(h_{d,i} - \bar{h_i})(t_{d,j}-\bar{t_j})]}{\sqrt{\sum_{d=1}^D(h_{d,i}-\bar{h_i})^2\sum_{d=1}^D(t_{d,j}-\bar{t_j})^2}}

Where

+----------------+-----------------------+-------------------------------+
| **Equation**   | **Python Variable**   | **Value**                     |
+================+=======================+===============================+
| d              | tnum                  | trace number                  |
+----------------+-----------------------+-------------------------------+
| i              | kguess                | subkey guess                  |
+----------------+-----------------------+-------------------------------+
| j              | j index trace point   | sample point in trace         |
+----------------+-----------------------+-------------------------------+
| h              | hypint                | guess for power consumption   |
+----------------+-----------------------+-------------------------------+
| t              | traces                | traces                        |
+----------------+-----------------------+-------------------------------+

It can be noticed there is effectively three sums, all sums are done
over all traces. For this initial implementation we'll be explicitly
calculating some of these sums, although it's faster to use NumPy to
calculate across large arrays. We'll convert those three summations into
variables, turning the equation into this format:

.. math:: r_{i,j}=\frac{sumnum}{\sqrt{snumden1 * sumden2}}

Where:

.. math:: sumnum = \sum_{d=1}^{D}[(h_{d,i} - \bar{h_i})(t_{d,j}-\bar{t_j})]

.. math:: sumden1 = \sum_{d=1}^D(h_{d,i}-\bar{h_i})^2

.. math:: sumden2 = \sum_{d=1}^D(t_{d,j}-\bar{t_j})^2

Looking at this, we can see that we'll need :math:`\bar{h_i}` and
:math:`\bar{t_j}`, so let's start by building some code that will give
us those. Looking at the CPA tutorial, we can see that :math:`h_{d,i}`
is just our guess for the power consumption in trace :math:`d` for
subkey :math:`i`. We can get that easily using the ``HW`` array and
``intermediate()`` function we defined earlier:

.. code:: python

    for bnum in range(0, 16):
        cpaoutput = [0]*256
        maxcpa = [0]*256
        for kguess in range(0, 256):
            hyp = np.zeros(numtraces)
            for tnum in range(0, numtraces):
                hyp[tnum] = HW[intermediate(pt[tnum][bnum], kguess)]

Now we can get :math:`\bar{h_i}`:

.. code:: python

    meanh = np.mean(hyp, dtype=np.float64)

and :math:`\bar{t_j}` is just the mean of all of our traces:

.. code:: python

    meant = np.mean(traces, axis=0, dtype=np.float64)

Next, let's move on to calculating the whole sums using :math:`h_{d,i}`
and :math:`t_{d,j}` and the values we just calculated:

.. code:: python

    #For each trace, do the following
    for tnum in range(numtraces):
        hdiff = (hyp[tnum] - meanh)
        tdiff = traces[tnum,:] - meant

        sumnum = sumnum + (hdiff*tdiff)
        sumden1 = sumden1 + hdiff*hdiff 
        sumden2 = sumden2 + tdiff*tdiff

We can now get the correlation for each of our subkey guesses, which
we'll call ``cpaoutput[]``:

.. code:: python

    cpaoutput[kguess] = sumnum / np.sqrt( sumden1 * sumden2 )

We're almost done! All that's left is to use that correlation to figure
out which subkey best matches our power traces. First off, we only care
about absolute value of the correlation (that there is a linear
correlation), not sign. Additionally, though this didn't factor into our
correlation calculation, remember that each trace was actually made up
of a bunch of sample points. This means that what we actually have is
the correlation of each subkey guess to each sample point. Typically
only a few points in the trace are correlating, and it's the maximum
across the entire trace we are concerned with, so we can pick our
correlation for each subkey by:

.. code:: python

    maxcpa[kguess] = max(abs(cpaoutput[kguess]))

Finally, we can find the subkey that best matches our data by finding
the one with the biggest correlation:

.. code:: python

    bestguess[bnum] = np.argmax(maxcpa)

Putting it all together:

Finished Script
^^^^^^^^^^^^^^^


**In [13]:**

.. code:: ipython3

    import numpy as np
    from tqdm import tqdm
    
    sbox = (
        0x63, 0x7c, 0x77, 0x7b, 0xf2, 0x6b, 0x6f, 0xc5, 0x30, 0x01, 0x67, 0x2b, 0xfe, 0xd7, 0xab, 0x76,
        0xca, 0x82, 0xc9, 0x7d, 0xfa, 0x59, 0x47, 0xf0, 0xad, 0xd4, 0xa2, 0xaf, 0x9c, 0xa4, 0x72, 0xc0,
        0xb7, 0xfd, 0x93, 0x26, 0x36, 0x3f, 0xf7, 0xcc, 0x34, 0xa5, 0xe5, 0xf1, 0x71, 0xd8, 0x31, 0x15,
        0x04, 0xc7, 0x23, 0xc3, 0x18, 0x96, 0x05, 0x9a, 0x07, 0x12, 0x80, 0xe2, 0xeb, 0x27, 0xb2, 0x75,
        0x09, 0x83, 0x2c, 0x1a, 0x1b, 0x6e, 0x5a, 0xa0, 0x52, 0x3b, 0xd6, 0xb3, 0x29, 0xe3, 0x2f, 0x84,
        0x53, 0xd1, 0x00, 0xed, 0x20, 0xfc, 0xb1, 0x5b, 0x6a, 0xcb, 0xbe, 0x39, 0x4a, 0x4c, 0x58, 0xcf,
        0xd0, 0xef, 0xaa, 0xfb, 0x43, 0x4d, 0x33, 0x85, 0x45, 0xf9, 0x02, 0x7f, 0x50, 0x3c, 0x9f, 0xa8,
        0x51, 0xa3, 0x40, 0x8f, 0x92, 0x9d, 0x38, 0xf5, 0xbc, 0xb6, 0xda, 0x21, 0x10, 0xff, 0xf3, 0xd2,
        0xcd, 0x0c, 0x13, 0xec, 0x5f, 0x97, 0x44, 0x17, 0xc4, 0xa7, 0x7e, 0x3d, 0x64, 0x5d, 0x19, 0x73,
        0x60, 0x81, 0x4f, 0xdc, 0x22, 0x2a, 0x90, 0x88, 0x46, 0xee, 0xb8, 0x14, 0xde, 0x5e, 0x0b, 0xdb,
        0xe0, 0x32, 0x3a, 0x0a, 0x49, 0x06, 0x24, 0x5c, 0xc2, 0xd3, 0xac, 0x62, 0x91, 0x95, 0xe4, 0x79,
        0xe7, 0xc8, 0x37, 0x6d, 0x8d, 0xd5, 0x4e, 0xa9, 0x6c, 0x56, 0xf4, 0xea, 0x65, 0x7a, 0xae, 0x08,
        0xba, 0x78, 0x25, 0x2e, 0x1c, 0xa6, 0xb4, 0xc6, 0xe8, 0xdd, 0x74, 0x1f, 0x4b, 0xbd, 0x8b, 0x8a,
        0x70, 0x3e, 0xb5, 0x66, 0x48, 0x03, 0xf6, 0x0e, 0x61, 0x35, 0x57, 0xb9, 0x86, 0xc1, 0x1d, 0x9e,
        0xe1, 0xf8, 0x98, 0x11, 0x69, 0xd9, 0x8e, 0x94, 0x9b, 0x1e, 0x87, 0xe9, 0xce, 0x55, 0x28, 0xdf,
        0x8c, 0xa1, 0x89, 0x0d, 0xbf, 0xe6, 0x42, 0x68, 0x41, 0x99, 0x2d, 0x0f, 0xb0, 0x54, 0xbb, 0x16)
    
    def intermediate(pt, keyguess):
        return sbox[pt ^ keyguess]
    
    HW = [bin(n).count("1") for n in range(0, 256)]
    
    numtraces = np.shape(trace_array)[0] #total number of traces
    numpoint = np.shape(trace_array)[1] #samples per trace
    
    pt = textin_array
    knownkey = traces[0].key
    cparefs = [0] * 16
    bestguess = [0]*16
    
    for bnum in tqdm(range(0, 16), desc='Attacking subkeys'):
        cpaoutput = [0] * 256
        maxcpa = [0] * 256
        for kguess in range(0, 256):
    
            # Initialize arrays &amp; variables to zero
            sumnum = np.zeros(numpoint)
            sumden1 = np.zeros(numpoint)
            sumden2 = np.zeros(numpoint)
    
            hyp = np.zeros(numtraces)
            for tnum in range(0, numtraces):
                hyp[tnum] = HW[intermediate(pt[tnum][bnum], kguess)]
    
            # Mean of hypothesis
            meanh = np.mean(hyp, dtype=np.float64)
    
            # Mean of all points in trace
            meant = np.mean(trace_array, axis=0, dtype=np.float64)
    
            # For each trace, do the following
            for tnum in range(0, numtraces):
                hdiff = (hyp[tnum] - meanh)
                tdiff = trace_array[tnum, :] - meant
    
                sumnum = sumnum + (hdiff * tdiff)
                sumden1 = sumden1 + hdiff * hdiff
                sumden2 = sumden2 + tdiff * tdiff
    
            cpaoutput[kguess] = sumnum / np.sqrt(sumden1 * sumden2)
            maxcpa[kguess] = max(abs(cpaoutput[kguess]))
    
        bestguess[bnum] = np.argmax(maxcpa)
        cparefs[bnum] = np.argsort(maxcpa)[::-1]
    
    print("Best Key Guess: ", end="")
    for b in bestguess: print("%02x " % b, end="")


**Out [13]:**



.. parsed-literal::

    Attacking subkeys: 100%\|██████████\| 16/16 [00:22<00:00,  1.41s/it]
    




.. parsed-literal::

    Best Key Guess: 2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c 


Checking Our Guess
~~~~~~~~~~~~~~~~~~

Since we were the ones who decided the key (and kept track of it in
knownkey), we can check if our guess was right:


**In [14]:**

.. code:: ipython3

    for b in knownkey: print("%02x "%b, end="")
    print("\n")
    if all([known_byte == guess_byte for known_byte, guess_byte in zip(knownkey, bestguess)]):
        print("Guess was right")
    else:
        print("Guess was wrong")


**Out [14]:**



.. parsed-literal::

    2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c 
    
    Guess was right
    


We can do even better by calculating the Partial Guessing Entropy.

The Partial Guessing Entropy (PGE) is a useful metric of where the
correct answer is ranked. This requires us to know the actual encryption
key used during operation, which we do know!

Certain attacks will use different keys during the acqusition period,
meaning the a list of all the known keys is required since there isn't a
constant key. In our case, we already have access in ``knownkey``.

Previously, we simply printed the maximum output for each subkey as
follows:

.. code:: python

    #Find maximum value of key
    bestguess[bnum] = np.argmax(maxcpa)

To sort the list of CPA output's, we'll use the ``argsort()`` function
from NumPy. This will output a list where the first element is the index
of the lowest value, next element is the index of the next-highest
element, etc. Because in our input list the ``maxcpa`` vector's indexes
correspond to the key guess, this allows us to know where the keys are.
We reverse that sorted list to put the first element as the maximum CPA
output:

.. code:: python

    cparefs = np.argsort(maxcpa)[::-1]

Finally, the Partial Guessing Entropy is simply the location of the
known correct key byte inside that array. We can find that with the
``.index()`` function:

.. code:: python

    print cparefs.index(0x2B)

Where the correct key should of course come from our ``knownkey``
variable instead of being hard-coded. Pulling it all together:


**In [15]:**

.. code:: ipython3

    pge = [0]*16
    for bnum in range(0, 16):
        #Find maximum value of key
        #Find PGE
        pge[bnum] = list(cparefs[bnum]).index(knownkey[bnum])
        
    print("PGE: ", end="")
    for i in pge:
        print(i, end="")
    print("")


**Out [15]:**



.. parsed-literal::

    PGE: 0000000000000000
    


Improvements
~~~~~~~~~~~~

The implementation of the correlation function runs as a loop over all
traces. Ideally we'd like to implement this as a 'online' calculation;
that is, we can add a trace in, observe the output, add another trace
in, observe the output, etc. When generating plots of the Partial
Guessing Entropy (PGE) vs. number of traces this is greatly preferred,
since otherwise we need to run the loop many times!

We can use an alternate form of the correlation equation, which
explicitly stores sums of the variables. This is easier to perform
online calculation with, since when adding a new trace it's simple to
update these sums. This form of the equation looks like:

.. math:: r_{i,j} = \frac{D\sum_{d=1}^{D}h_{d,i}t_{d,j}-\sum_{d=1}^{D}h_{d,i}\sum_{d=1}^{D}t_{d,j}}{\sqrt{((\sum_{d=1}^Dh_{d,i})^2-D\sum_{d=1}^Dh_{d,i}^2)-((\sum_{d=1}^Dt_{d,j})^2-D\sum_{d=1}^Dh_{d,j}^2)}}

Tests
-----


**In [16]:**

.. code:: ipython3

    assert all([known_byte == guess_byte for known_byte, guess_byte in zip(knownkey, bestguess)]), "Failed to break encryption key\nGot: {}\nExpected: {}\n".format(knownkey, bestguess)


**In [17]:**

.. code:: ipython3

    assert not (np.all(pge)), "PGE not zero for byte: {}".format(pge)


**In [ ]:**

