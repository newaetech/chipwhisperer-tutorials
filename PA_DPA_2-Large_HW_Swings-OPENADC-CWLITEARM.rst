
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

    

    <div id="ca7119a2-11ae-4e38-aa8e-2688df6bffa4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ca7119a2-11ae-4e38-aa8e-2688df6bffa4');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="549cc1c8-6be5-4ec7-b095-00e8a1134ca0" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="ac824bac-e9ef-4448-ab59-6a5363a275e4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ac824bac-e9ef-4448-ab59-6a5363a275e4');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"09732300-2641-4973-94e3-cb4017441df1":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1047","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{},"id":"1047","type":"UnionRenderers"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1009","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"09732300-2641-4973-94e3-cb4017441df1","roots":{"1002":"549cc1c8-6be5-4ec7-b095-00e8a1134ca0"}}];
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

    

    <div id="d0edce82-c3be-49a4-81ba-b38c4f7057b2"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#d0edce82-c3be-49a4-81ba-b38c4f7057b2');
        
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

    Number of 0xFF: 18
    Number of 0x00: 32
    



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
        
    
    
    
    
    
      <div class="bk-root" id="b377fa74-3ce1-437e-8cd6-6609fae7e63a" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="0d2bb397-3726-48cb-8e40-f8985cedfd7c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0d2bb397-3726-48cb-8e40-f8985cedfd7c');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c9c04eb7-454f-449e-95aa-8fb8b930e2a5":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{},"id":"1157","type":"UnionRenderers"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{},"id":"1156","type":"Selection"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"WFVVVVXVWr+A4ziO4zhSPwByHMdxHEq/gBzHcRzHRr+Q4ziO4zhAvwDIcRzHcTy/AAAAAAAANL8Ax3Ecx3FOv+A4juM4jlK/AAAAAACAUb8AAAAAAABJv4DjOI7juFK/QI7jOI5jUb8Ax3Ecx3FBvwByHMdxHD2/ABzHcRzHJb/gOI7jOI5MvwAgx3Ecxxk/AI7jOI7jSr+AHMdxHMc9vwAAAAAAgFG/AFVVVVVVP78AAAAAAABIv0CO4ziO4zy/wHEcx3GcU78AVlVVVVU/vwDkOI7jODC/AKuqqqqqML/gOI7jOI5CvwCO4ziO4zK/ADiO4ziOMb8AAAAAAABJvziO4ziO40u/AMdxHMdxUb8AjuM4juNFvwAAAAAAgFe/kOM4juM4Fr8Aqqqqqqo4PwCqqqqqqja/AAAAAAAAPL/gOI7jOI5MvwCQ4ziO4yy/QI7jOI7jS7+O4ziO4zhAvwDHcRzHcVW/AKuqqqqqSj8AAAAAAABYvwByHMdxHEC/qKqqqqqqUL8AOI7jOI41PwCrqqqqqkG/AKqqqqqqJr/kOI7jOI4TPwByHMdxHEa/AKqqqqqqPr8AOY7jOA5Rv4DjOI7jODy/AMdxHMfxUr8AOY7jOI5Bv0CO4ziO41G/wHEcx3EcO78A5DiO4zg+vwDHcRzHcRS/gOM4juM4Mj8AOY7jOI45vwDHcRzHcRw/ADmO4ziORL8Ax3Ecx3E0vwCrqqqqqk+/ADmO4ziOOb+AqqqqqqpOvwA4juM4jhO/AAAAAAAAQ78A5DiO4zg2vwAcx3Ecxz2/AKqqqqqqNL8A5DiO4zhGv4Cqqqqqqkq/AFRVVVVVJb8AchzHcRxOvwByHMdxHE2/AMdxHMdxTL8AchzHcRw7vwByHMdxHEO/AKuqqqqqQb8Ax3Ecx3E6vwA4juM4jj2/AI7jOI7jOr8AHMdxHMdGv4BVVVVVVU2/AFVVVVVVSr8AVlVVVVU7vwAAAAAAAEO/AHIcx3EcSr8AAAAAAABPvwCO4ziO40q/AOQ4juM4Qb9AVVVVVVUpvwDkOI7jODq/AI7jOI7jJD8AyHEcx3E6vwA5juM4jj+/gKqqqqqqNL+AHMdxHMdIvwDIcRzHcRy/ADmO4ziOQL+AHMdxHMc3vwDAcRzHcew+AOQ4juM4Hj8AQI7jOI4DvwBQVVVVVfU+AI7jOI7jNL8AchzHcRwnvwByHMdxHEg/AMdxHMdxRb8A5DiO4zgeP8BxHMdxHEy/AOQ4juM4Jj8Aq6qqqqo6vwCO4ziO4yw/gBzHcRzHS78Ax3Ecx3EcPwA5juM4ji8/AI7jOI7jSb8AyHEcx3EsvwCrqqqqqjq/AAAAAAAAGD8AAAAAAIBSv4DjOI7jOEa/AMhxHMdxLL/AcRzHcRwxv4DjOI7jOE2/AKuqqqqqNL9AVVVVVVUtvwBVVVVVVSG/ABzHcRzHJT+AVVVVVVVFvwByHMdxHAc/ADmO4ziOQL8Ax3Ecx3EgvwDHcRzHcTC/AKqqqqqqKj8AHMdxHMdFvwCsqqqqqiK/AMhxHMdxJL8AHMdxHMcRPwDIcRzHcTy/AFRVVVVVFb8AyHEcx3EwP4Acx3EcxzG/AMhxHMdxLL8Aq6qqqqoyvwCQ4ziO4yS/gOM4juM4UL8AHMdxHMcpv4Acx3Ecx0m/AOQ4juM4Qb8AjuM4juNMvwBwHMdxHCO/ACDHcRzHET8AsKqqqqoKv4Acx3EcxzG/AMdxHMdxMr8AOI7jOI4bvwDkOI7jOD6/gOM4juM4Lr8AVlVVVVUVvwByHMdxHDG/AOQ4juM4Nr+AHMdxHMc5P8BxHMdxHDc/AOQ4juM4Dr8AyHEcx3EcPwA5juM4jj8/sKqqqqqqQj+gqqqqqqpKvwCO4ziO4yC/AMBxHMdxzL4AVVVVVVUdPwAAAAAAAEK/AFZVVVVVLb8cx3EcxzFkP8BxHMdxHF8/AAAAAAAAUz+AVVVVVVVNPziO4ziOQ4s/yHEcx3F8hD9YVVVVVRWBP4DjOI7j+Hk/AAAAAADghD/AcRzHcXx7P5DjOI7juHk/oKqqqqrqcT/gOI7jOG59P8BxHMdxvHI/sKqqqqrqcD8Ax3EcxzFqP+A4juM4bng/QI7jOI4jcj+A4ziO4zhqP6CqqqqqqmE/cBzHcRw3hT9QVVVVVfV9P8BxHMdxnHE/HMdxHMfxZz+gqqqqqsqBP8BxHMdxXHc/gBzHcRzHbD/gOI7jOA5YP0CO4ziOo2o/gOM4juM4WD+AVVVVVVVKPwDHcRzHcRy/IMdxHMcRhz+gqqqqqop8PwAAAAAAoHM/IMdxHMfxYj8AAAAAANCHP0CO4ziOI38/yHEcx3E8cj8AAAAAAABkP+Q4juM4zmE/AMdxHMdxVT8AAAAAAAA2PwBwHMdxHB+/AJDjOI7jEL8AchzHcRw/PwA5juM4DlG/AAAAAAAAKL8AOY7jOI5Ev4BVVVVVVUi/gOM4juM4T78AchzHcRxRvwA5juM4jkG/gOM4juM4S7+A4ziO4zhKv0CO4ziO41K/AFVVVVVVTz8AchzHcRw1vwCsqqqqqhI/4DiO4ziOP78AOY7jOI5WPwAcx3EcxzU/AJDjOI7jGL+A4ziO4zhFvwA5juM4jkC/AFVVVVVVT7+AHMdxHEdWvyDHcRzHcVK/gOM4juOYgz8gx3Ecx3F7P4Acx3EcB3E/UFVVVVUVZT9AjuM4jnOHPwAAAAAAYH4/rKqqqqrqdT+Q4ziO4/hjP3Acx3EcB2c/gBzHcRzHUj+AHMdxHMdJPwBYVVVVVQW/ADiO4ziOLz8Ax3Ecx3FGvwAAAAAAADK/ADmO4ziOTL8AAAAAAAAovwAAAAAAACS/AAAAAAAAVL8AjuM4juNRv0BVVVVVVU6/gOM4juO4UL/AqqqqqqpQvwA5juM4DlS/ADiO4ziOG78Ax3Ecx3FHv8BxHMdxHFq/kOM4juO4Ub8AVVVVVVVPPwBWVVVVVTm/ABzHcRzHM79wHMdxHEdVvwCrqqqqqkG/AHIcx3GcWb/AcRzHcRxbv7CqqqqqKlq/gOM4juM4iD9gVVVVVZV9P4Acx3EcJ3Q/OI7jOI4jZT9wHMdxHGeKP6yqqqqqqoA/kOM4juP4cz9QVVVVVdVqP5DjOI7j+Gw/QI7jOI4jYj8Ax3Ecx3FYPwAcx3EcxzE/ADiO4ziOLz8AOI7jOI4bv4CqqqqqqkG/ADmO4ziOSb8Ax3Ecx3FCvwCO4ziO4za/AAAAAAAAQ7+A4ziO47hWv8CqqqqqqkS/gOM4juO4Ub+AHMdxHMdNvwDHcRzH8Vm/wKqqqqqqVj8AIMdxHMfxvgBQVVVVVfU+4DiO4ziOSb8AjuM4jmNXPwA5juM4jkg/AAAAAAAAEL+A4ziO4zhHv4Acx3Ecx1K/QFVVVVVVVr+AHMdxHMdZvziO4ziO41e/AHIcx3GcVb8AAAAAAIBRvwAAAAAAQGK/4DiO4zgOUb8AHMdxHMdCv4Acx3Ecx1S/gBzHcRzHVL/IcRzHcRxQv4BVVVVV1VG/gBzHcRzHVr8AAAAAAIBYvyDHcRzHcVa/AMhxHMdxJL/AcRzHcZxRv4DjOI7jOD6/IMdxHMdxQr+AqqqqqqpKvzmO4ziO41a/UFVVVVVVUL9AjuM4juNRvxzHcRzH8Ve/OI7jOI5jUL+oqqqqqipav0CO4ziOY1S/UFVVVVUVYr84juM4juNQv1BVVVVVVVm/ADmO4ziOUL84juM4juN8PziO4ziOI3U/AAAAAABAbj8AAAAAAIBYP8BxHMdxHCu/gOM4juM4Rb8AOY7jOA5QvwCO4ziO402/gBzHcRzHR7+A4ziO4zhEv4Acx3Ecx0O/ABzHcRzHSb+AHMdxHEdYv4Acx3EcB2C/wKqqqqoqWb+A4ziO4zhXvwDHcRzH8V+/gBzHcRwHYb+gqqqqqipkv4BVVVVVVV2/wHEcx3GcYb8gx3EcxzFiv4Acx3EcR2C/AAAAAACAVr+AHMdxHKd4v3Acx3EcR3a/4DiO4zgucb+qqqqqquprv6Cqqqqqana/YFVVVVW1cb/AcRzHcRxnv8hxHMdxnGS/ADmO4ziOYr8AOY7jOI5dvwCO4ziO402/UFVVVVXVV78AAAAAAIBXvwAAAAAAAEG/AAAAAAAARL9AVVVVVVVEvwA5juM4jka/AI7jOI7jQL8gx3Ecx3FNvwDHcRzHcUO/yHEcx3EcXr8Ax3Ecx3FVv0BVVVVVVVO/AAAAAAAAQb+AVVVVVVVAvwDHcRzHcUK/AFVVVVVVOb8Aq6qqqqpDv4CqqqqqqkW/ABzHcRzHJb8AOY7jOI45vwDHcRzH8VK/AHIcx3EcN7+AHMdxHMdPv4BVVVVVVUO/AFVVVVVVQr8AjuM4juMyvwAAAAAAAAA/ABzHcRzHM7/kOI7jOI5IvwA5juM4jka/AOQ4juM4PL+AqqqqqqpHvyDHcRzHcUS/ABzHcRzHRL8Aq6qqqqpCvwByHMdxHEO/gOM4juM4PL8AchzHcRxIPwCQ4ziO4xC/ADmO4ziOQr+A4ziO4zhIvwA4juM4jiO/QI7jOI7jRL8AkOM4juP4vgDHcRzHcUW/4DiO4ziOSD8AqqqqqqouvwAAAAAAADq/ABzHcRzHO78A4DiO4zgOPwA5juM4jjW/AAAAAAAAGL8AAAAAAAAsvwDHcRzHcUm/AAAAAAAARb8AAAAAAABDvwBYVVVVVRW/ABzHcRzHNb8AHMdxHMc5PwAgx3EcxwG/AAByHMdxzD4AHMdxHMc5vwCO4ziO4yi/AKyqqqqqGj9wHMdxHMdCvwAAAAAAAEU/AOQ4juM4QL8AOI7jOI4TP8BxHMdxHEe/AI7jOI7jPD8AHMdxHMctPwCQ4ziO4/i+AAAAAAAAQb8AVVVVVdVdPwByHMdxHE8/AI7jOI7jJD8AHMdxHMcZPwDkOI7jOEM/gBzHcRzHNz8gx3Ecx3FDvwCO4ziO4zw/YFVVVVVVRj8AAAAAAABEPwDkOI7jOC6/AMdxHMdxND8AjuM4juNBP4BVVVVVVUI/AKuqqqqqMj8AkOM4juMQvwCO4ziO4zg/AOQ4juM4ML8AkOM4juMYvwCA4ziO4/g+AMdxHMdxQz8AjuM4juM4PwBwHMdxHAc/ADiO4ziOOT8AjuM4juM4P4CqqqqqqkI/AHIcx3EcLz+A4ziO4zguPwAcx3Ecxze/AMhxHMdxPD+AqqqqqqpBvwDHcRzHcTI/ADmO4ziOQb8AVVVVVVU7PwCO4ziO4xC/wHEcx3EcOb8Ax3Ecx3FGPwBWVVVVVT8/AAAAAAAAQj8gx3Ecx3EwvwCrqqqqqkc/ABzHcRzHQ78AqqqqqqomP+A4juM4jj2/AHIcx3EcRj8AHMdxHMc5vwAAAAAAACw/AMdxHMdxID8AOI7jOI47vwAAAAAAAAAAwHEcx3EcS7/AqqqqqqoyvwAAAAAAAEa/5DiO4ziOM7+AHMdxHMdHvwDHcRzHcTC/AAAAAAAAPr/Aqqqqqqoiv+A4juM4jkK/AFZVVVVVIb8AVVVVVVUhvwDHcRzHcSi/AFVVVVVVHb8AOY7jOI5IvwA5juM4jkK/oKqqqqoqUr8AAAAAAAA6vwByHMdxHEa/AOQ4juM4Dr8AAAAAAABDv4DjOI7jOEC/ADmO4ziON7/gOI7jOE5hvwDHcRzH8Vq/gOM4juM4Ur8Ax3Ecx3FIvwDHcRzHcVe/ADmO4ziOSb8AAAAAAABNvwDHcRzHcUe/ADmO4ziOVb8AchzHcRwzvwCrqqqqqjq/AMdxHMdxSL8Ax3Ecx3FXvwAAAAAAAEe/AI7jOI7jOL8AVVVVVVVKvwDIcRzHcTy/ADmO4ziOQr8AcBzHcRwXv1hVVVVVVUa/AOQ4juM4Qj8AIMdxHMfxvgAcx3EcxxG/AI7jOI7jLD8AcBzHcRwfvwBUVVVVVSE/AMBxHMdx3D7AqqqqqqouPwAAchzHccy+AKqqqqqqOr8AHMdxHMcpvwAAAAAAACC/AAAAAAAAUb8AOY7jOI43vwAcx3EcxxG/gKqqqqqqOr+AVVVVVVUVvwAAAAAAACi/ACDHcRzHAb8AchzHcRw3vwA4juM4jis/AAAAAAAAIL8AyHEcx3EoPwDkOI7jOC6/AMBxHMdx7L4AjuM4juM0PwCO4ziO4yA/AHAcx3EcJ78AOI7jOI4bvwAAAAAAADI/AHAcx3EcB78AcBzHcRwjvwCQ4ziO4yC/gKqqqqqqSD8AchzHcRw1v7CqqqqqqiY/AFZVVVVVP78AkOM4juMYvwA4juM4jgM/wHEcx3EcM78AyHEcx3E8PwDAcRzHcfw+ADiO4ziOA7+wqqqqqqpAvwDIcRzHcSg/ABzHcRzHNb8AchzHcRxAP2BVVVVVVUu/AAAAAAAAND8AIMdxHMfxvgByHMdxHCe/gOM4juM4Or/AcRzHcRwzvwBVVVVVVTW/AOQ4juM4Hr8AqKqqqqoKvwDHcRzHcUK/AOQ4juM4Mj8AAAAAAABBvwDIcRzHcSS/ADiO4ziOGz8AcBzHcRwXvwAAAAAAADI/AFZVVVVVPb8AcBzHcRwXPwDkOI7jOB6/AMBxHMdx/D4AjuM4juMwP4DjOI7jOGA/QFVVVVXVUj+AHMdxHEdZPzmO4ziO40I/gKqqqqqqXj8AchzHcRxDP4BVVVVVVUc/YFVVVVVVRD8AjuM4juNJPwA5juM4jkg/AAAAAAAAID/AcRzHcRw7P4Acx3Ech2O/gBzHcRxHU7+AqqqqqqpUv8CqqqqqqjK/QI7jOI5jYL8AOY7jOI5Pv8BxHMdxHE2/wHEcx3EcSb9wHMdxHEddvwDHcRzH8VW/QFVVVVVVUL8AchzHcRxQv4Acx3Ecx0i/gKqqqqqqQL8AAAAAAABDvwByHMdxnFG/AAAAAAAAV78AHMdxHMdNvwDHcRzHcUu/AFZVVVVVN7+A4ziO4zhLv4Cqqqqqqkq/AOQ4juM4Mr8AOY7jOI5Ev8Cqqqqqqle/wKqqqqqqUL/AcRzHcRxXv0CO4ziO4yy/gOM4juO4WL8AOY7jOA5Tv0BVVVVVVVW/oKqqqqqqRr8Aq6qqqqpAPwAAAAAAADC/AAAAAAAAML8gx3Ecx3E6vwCrqqqqqko/AI7jOI7jSL8AyHEcx3EcP1BVVVVVVUa/gBzHcRxHVD8AqqqqqqowvwAAAAAAABg/AI7jOI7j+D4A5DiO4zg6PwA4juM4jhu/AHIcx3EcNb8AjuM4juMsPwA4juM4jjE/AFVVVVVVMz8AjuM4juMwvwByHMdxHAe/AI7jOI7jR78cx3Ecx3EyvwDHcRzHcSi/AMhxHMdxKL9VVVVVVVVBP0CO4ziO4zA/AMhxHMdx/D4A5DiO4zgWvwByHMdxHC8/wKqqqqqqJj8AchzHcRwnvwDkOI7jODi/gOM4juM4Vj9gVVVVVdVWPwDHcRzHcTY/ABzHcRzHEb+wqqqqqqpGPwDHcRzHcTI/ADiO4ziOA7+A4ziO4zhBv4CqqqqqqkE/gFVVVVVVQj8AVlVVVVUlvwDHcRzHcUE/AAAAAAAAOj8AchzHcRw7PwDIcRzHcRS/AFhVVVVVFb+A4ziO4zhBPwAcx3EcxyG/AMdxHMdxMD8AkOM4juMQv4Acx3Ecx1E/AOQ4juM4Mr+AVVVVVVVEPwBAjuM4jgM/AAAAAACAZj/AcRzHcRxWPwCO4ziO400/yHEcx3EcTT9AVVVVVVVhPwCO4ziO40k/AHIcx3EcQT+A4ziO4zhFPwCrqqqqqkY/AAAAAAAAQT8AkOM4juMIvwDHcRzHcSw/ABzHcRzHSL8AVVVVVVVHvwDkOI7jODC/gBzHcRzHOb+AVVVVVdVRvwA5juM4DlS/sKqqqqqqUL9AjuM4juNQvyDHcRzHcUI/AFZVVVVVLb8AOI7jOI4rP4BVVVVVVUe/AMhxHMdxHD8AAAAAAAAyvwDHcRzHcTK/AFhVVVVVFb8AkOM4juMIvwAAAAAAADi/AAAAAAAARb8AcBzHcRwXvwCO4ziO4yS/gBzHcRzHRj8AcBzHcRwfvwAAAAAAABg/AMhxHMdxML+A4ziO4zhBvwByHMdxHEa/AFVVVVVV5b4AcBzHcRwfPwDkOI7jOEe/ABzHcRzHOb9AjuM4juMwvwByHMdxHEk/ABzHcRzHQb8AqqqqqqoiP8BxHMdxHDe/AFRVVVVVM7+AqqqqqqpQvwDkOI7jOEO/gBzHcRzHPb8AOY7jOA5SvwCO4ziO4z6/AOQ4juM4Fr8AjuM4juMwvyDHcRzHcU+/AOQ4juM4Lr8A5DiO4zgWPwCO4ziO4zi/gFVVVVVVTL8AOY7jOI5GvwCrqqqqqji/AAAAAAAAR7+A4ziO47hRvwAAAAAAAEO/gBzHcRzHR78Ax3Ecx3FEvwDHcRzHcSS/AMdxHMdxPL8AOI7jOI4vvwByHMdxHEO/gOM4juP4YL+gqqqqqqpgv0CO4ziOY1C/5DiO4ziOTb8AOY7jOE5nvwByHMdxHFu/AMdxHMdxV78Ax3Ecx3Eyv4Acx3Ecx1i/AKyqqqqqLr+AHMdxHMdGvwDIcRzHcfw+AKuqqqqqUT8AchzHcRxSPwDkOI7jOEE/IMdxHMdxQT8Ax3Ecx7FkPyDHcRzHcVQ/4DiO4zgOVT8AOY7jOI5LPwAAAAAAAE0/ABzHcRzHNz+A4ziO4zhJPwBUVVVVVSk/gFVVVVVVTT8AAAAAAAAyPwCrqqqqqjQ/AKyqqqqqKj8AOY7jOI5NPwCO4ziO40c/AHIcx3EcRj8AVVVVVVVAPwCrqqqqqiI/AI7jOI7jOD8Ax3Ecx3E0PwByHMdxHDk/AAAAAACAVD9AVVVVVVVYPwAAAAAAAEI/oKqqqqqqOj8Ax3Ecx3FNPwA4juM4jjE/AI7jOI7jSD8AVVVVVVUdvwAcx3Ecx08/AI7jOI7jNj+AVVVVVVVKPyDHcRzHcUC/AOQ4juM4Rz8AOI7jOI4jPwCO4ziO40I/AAAAAAAAED8AcBzHcRwnPwDHcRzHcTI/AAAAAAAAAD8AAAAAAABBPwAcx3Ecx0o/AHIcx3EcTD8AkOM4juP4PoAcx3Ecx0M/gKqqqqqqar/AcRzHcZxjv0BVVVVV1V2/wHEcx3EcSb8AVlVVVVU/vwA5juM4jkO/5DiO4ziOIz8AVVVVVVU7vwA5juM4jjG/AAAAAAAARr8AHMdxHMclvwByHMdxHDU/AOQ4juM4Fr8AjuM4juMkvwDkOI7jOCo/AI7jOI7jMr8AOI7jOI4bPwDAcRzHcew+ABzHcRzHMb8A5DiO4zhAPwDHcRzHcTa/wKqqqqqqQT8AwHEcx3HcPgBVVVVVVTE/AHIcx3EcK78AchzHcRwnPwByHMdxHCe/gOM4juM4MD8AVVVVVVVLPwAAAAAAADw/ADmO4ziORj8AVFVVVVX1vgAcx3Ecx0w/AOQ4juM4Rj8AjuM4juNDPyDHcRzHcTy/AMdxHMdxQz+AVVVVVVVIPwA5juM4jjk/5DiO4ziOG78AyHEcx3E6P0CO4ziOY1A/AAAAAAAAAL8Ax3Ecx3EyPwDIcRzHcSQ/AI7jOI7jSD8AkOM4juMkPwAcx3Ecxxk/AFRVVVVVMb8AcBzHcRwzPwBQVVVVVQU/AMdxHMdxOL8AHMdxHMdAPwCsqqqqqgq/AAAAAAAAQT+AVVVVVVVGvwAAAAAAAFE/AOQ4juM4Ir8AAAAAAAA+PwA5juM4jjc/AAAAAAAAKD8AyHEcx3H8voDjOI7jOEg/AAAAAAAAKD+wqqqqqqpDPwAcx3Ecxxk/AMdxHMdxMD8AVVVVVVVDPwAAAAAAAEm/AFZVVVVVJT8AHMdxHMcZvwCQ4ziO4yi/QI7jOI7jPr8AkOM4juP4PgCqqqqqqia/AAAAAAAANr8AVVVVVVUzvwCO4ziO4yS/AMBxHMdx3L4AwHEcx3HsPgDkOI7jOCY/ACDHcRzHGb8AVFVVVVUlPwBUVVVVVSm/AOQ4juM4Fj8AAAAAAAA6PwDHcRzHcSQ/ABzHcRzHKT8AAAAAAABUP+Q4juM4Dlk/oKqqqqqqRT+AqqqqqqpGPzmO4ziOo4m/5DiO4zjeg7/IcRzHcYyAvyDHcRzH8XS/4DiO4zgOlr9AjuM4jhOPv6iqqqqq6oW/juM4juM4fr84juM4jjudv8hxHMdxXJW/WFVVVVVFi7/HcRzHcZyCvziO4ziOA5W/cBzHcRx3i78cx3Ecx5GBv+A4juM4rnW/q6qqqqrKcb+AHMdxHIdmvwA5juM4jli/AHIcx3EcSb8AAAAAAABCvwAcx3EcxyU/YFVVVVVVUT+Q4ziO47hUP4DjOI7jOFI/gOM4juM4XT8gx3Ecx3FUP4Acx3Ecx1Q/gOM4juM4XD9AjuM4jmNYP7CqqqqqKlQ/IMdxHMfxUz9AVVVVVVVSPziO4ziO41M/UFVVVVXVUT8AjuM4juNFP5DjOI7jeHa/AAAAAACAcr+wqqqqqmplv7Cqqqqqqlq/AAAAAACgeL9YVVVVVVVxv6qqqqqqKmW/ADmO4ziOUr9AjuM4juN1v1ZVVVVVVWW/VFVVVVUVYL8AchzHcRwvv8BxHMdxHFa/AMhxHMdxLL8AyHEcx3EoPwA4juM4jj0/AAAAAAAASj+A4ziO47hQPwA5juM4jkM/AMdxHMdxWD8Ax3Ecx/FVPwAAAAAAgFo/AAAAAACAWz+AqqqqqqpdP2BVVVVV1WM/gBzHcRzHUj8AAAAAAABhPwAAAAAAAFA/YFVVVVVVYz8AAAAAAABcP5DjOI7juGA/IMdxHMdxWD8AAAAAAABdP7CqqqqqKmA/4DiO4ziOWz/AcRzHcZxXPwA5juM4jlM/chzHcRwHYD+oqqqqqipWPwA5juM4DlU/ADmO4ziOVT+A4ziO47hQP+A4juM4DlM/wHEcx3EcVD8AchzHcZxTP6CqqqqqKlg/IMdxHMdxVz8AchzHcRxJP0CO4ziO41g/kOM4juO4VD+Q4ziO4zhUP0CO4ziOY1U/rKqqqqoqej+Q4ziO45hzP8BxHMdxXG0/QFVVVVVVaj+sqqqqqmp1P5DjOI7j2HA/IMdxHMfxaT8gx3EcxzFmP3Acx3Ecx2c/gOM4juN4YT/AqqqqqqpePwA5juM4jlo/QFVVVVVVXT+AHMdxHMdMPwAAAAAAgFM/kOM4juM4UD/AcRzHcRxeP0CO4ziO40w/wHEcx3GcWT9AjuM4juNLP0CO4ziO418/wKqqqqqqTT/gOI7jOI5XPwAAAAAAgFI/wHEcx3GcVj/IcRzHcRxHP6Cqqqqqqj4/AMdxHMdxTD/AcRzHcVxgPyDHcRzHMWA/gOM4juM4Wj/gOI7jOA5ZPwByHMdxHEI/gOM4juO4Uz9gVVVVVVU/PwAAAAAAADo/AAAAAAAANr8gx3Ecx3E2vyDHcRzHcSA/AAAAAAAANj/gOI7jOI5cP4Acx3Ecx1I/wHEcx3EcVD8AjuM4juNEP1hVVVVV1VY/ADmO4ziOSz8AAAAAAABUPwDkOI7jOD4/oKqqqqqqUj8Ax3Ecx3E0P4Cqqqqqqko/AKuqqqqqQT9gVVVVVVVXP5DjOI7juFQ/oKqqqqqqSj/AcRzHcRxSPwDHcRzH8VM/gBzHcRzHUj8Ax3Ecx3FAP4DjOI7jOEA/AOQ4juM4Tj8AAAAAAABBPwAcx3Ecxy0/YFVVVVVVSj8Ax3Ecx3FdPwBwHMdxHCs/AOQ4juM4TT8AOY7jOI5EPwDHcRzHcVg/gOM4juM4RD+A4ziO47hSPwAAAAAAADY/AFVVVVVVST+AqqqqqqpJPwByHMdxHD8/QI7jOI7jUD8AAAAAAABCP5DjOI7juFA/QI7jOI7jST8Ax3Ecx3E+P8BxHMdxHEA/kOM4juM4UT8AOY7jOI41PwDHcRzHcUs/IMdxHMfxYb+Q4ziO4zhQv5DjOI7jOEG/AFZVVVVVIb9AjuM4juMsvwCQ4ziO4/i+wHEcx3EcQT8AOY7jOI5Av0CO4ziOY1A/IMdxHMdxRz8AAAAAAABGPwCQ4ziO4xi/IMdxHMdxaL/AcRzHcZxhv8BxHMdxnGC/gOM4juM4Wb8AAAAAAIBsv6Cqqqqq6mG/gOM4juO4W78AAAAAAABVvwDHcRzHcUC/AI7jOI7jML+AHMdxHMdOvwBYVVVVVRU/AOQ4juM4Lr8AHMdxHMcZPwDHcRzHcSw/ADmO4ziOPz8Ax3Ecx/FVP4BVVVVVVT0/QFVVVVVVSj8AAAAAAABIPwDHcRzHcVA/QI7jOI7jST/AcRzHcRxJP0CO4ziO40Y/AKuqqqqqOD9AjuM4juM2P1BVVVVVVU0/gBzHcRzHRj9AjuM4jmNsv6CqqqqqKmS/YFVVVVXVV79gVVVVVVVAvwBVVVVVVUI/4DiO4ziOWj8AAAAAAABLPyDHcRzH8VI/oKqqqqoqYL/gOI7jOA5Wv+A4juM4jkm/AHIcx3EcNb/gOI7jOA5ev4DjOI7jOFS/AI7jOI7jTL8AchzHcRxNvxzHcRzHsWG/QI7jOI7jWL/AqqqqqqpYvwAAAAAAAEi/AFVVVVVVJb8AqqqqqqouvwAAAAAAABg/AMhxHMdxMj8Ax3Ecx3E0P4Acx3Ecx0I/AKyqqqqqCj/gOI7jOA5RPwDkOI7jODA/AFVVVVVVMz8AOY7jOI47P6Cqqqqqqk0/gOM4juM4Sj9AVVVVVVVCP8Cqqqqqqks/gBzHcRzHSD/AqqqqqipVP8BxHMdxHC8/HMdxHMdxVD8AchzHcRw5P4DjOI7juFK/gBzHcRzHVb8AOY7jOI5HvwA5juM4jie/AAAAAAAAMr8AAAAAAAAAPwDHcRzHcTo/wKqqqqqqSD+A4ziO4zheP3Acx3EcB2Q/HMdxHMfxXz8AOY7jOA5aP6iqqqqqKlo/AAAAAAAAWD8AAAAAAIBSPwDHcRzHcU8/qKqqqqqqRz/AcRzHcRxIP4DjOI7jOE8/AMdxHMdxST/AqqqqqqpGPwA5juM4jj8/AAAAAAAAUj8AchzHcRxDP0BVVVVV1VQ/gBzHcRzHSz+AHMdxHEdQP4DjOI7jODo/gOM4juM4ST+AqqqqqqpLPwBVVVVVVSE/4DiO4ziOQj8Ax3Ecx3FQP0BVVVVVVUM/AMhxHMdxHD9AjuM4jmNVPwDHcRzH8VE/gBzHcRzHMz/gOI7jOI5CPwA5juM4jjs/AHIcx3GcUz8Ax3Ecx3FHP4Cqqqqqqjo/YFVVVVVVNT8AjuM4jmNQPwAAAAAAACQ/AAAAAAAASz+Aqqqqqqo2P4Acx3Ecx1e/QI7jOI7jVr/kOI7jOI4xvwAAAAAAACC/HMdxHMfxYD9AjuM4juNePwA5juM4Dlg/wKqqqqoqUT+Q4ziO43hgP2BVVVVV1V4/gBzHcRzHWT/AcRzHcRxUP2BVVVVV1VI/AHIcx3EcTT8Ax3Ecx3FEPwAcx3Ecx0I/gOM4juM4TT/AcRzHcRw5PyDHcRzHcUs/AHIcx3EcPT8Aq6qqqqpNPwCO4ziO40k/QI7jOI7jSz9AVVVVVVUlvwCO4ziOY1k/AI7jOI7jVD8AjuM4juM8PyDHcRzHcUE/AAAAAAAAUj8AchzHcRxJPwCqqqqqqjC/QI7jOI7jRD8AOY7jOA5QPwAAAAAAAEc/AFVVVVVVNT+Q4ziO4zhAPwDIcRzHcTg/AMhxHMdxFD8AchzHcRwfP2BVVVVVVT8/QI7jOI7jRT8AAAAAAAAQPwByHMdxHD0/AOQ4juM4Nj9AjuM4juNHPwBVVVVVVR2/YFVVVVVVRz8AqqqqqqoaPwDIcRzHcSi/AMBxHMdxzD7kOI7jOI5FPwAgx3Ecx/E+gBzHcRzHOb8A5DiO4zguvwAcx3EcxwG/AHIcx3EcLz/gOI7jOI5dv1BVVVVVVVG/gOM4juM4Tb8Ax3Ecx3FCv+A4juM4Dls/QFVVVVXVXT8AAAAAAIBRP4BVVVVV1VA/IMdxHMdxSD8AAAAAAABKPwAAAAAAAEg/AOQ4juM4ND/AqqqqqqpCPwByHMdxHDs/ADmO4ziOQT8AVFVVVVUpv4DjOI7jOE8/AKuqqqqqND8AHMdxHMcZvwByHMdxHBe/gBzHcRzHRz8AAAAAAABEP0BVVVVVVUc/gOM4juM4SD8AAAAAAABOP4DjOI7jOD4/AHIcx3EcPz9AjuM4juM4P4CqqqqqqkU/IMdxHMdxPj8gx3Ecx3EkPwCrqqqqqjQ/AMdxHMdxWL+A4ziO47hRv4DjOI7jOE+/gOM4juM4Jr8Aq6qqqqpOvwAAAAAAAFC/gBzHcRzHPb+A4ziO4zhFvyDHcRzH8WO/sKqqqqqqWb8AAAAAAABQv4Acx3Ecx0O/wHEcx3EcQz+AHMdxHMdIPwAAAAAAgFM/AHIcx3EcSz84juM4jmNXP8BxHMdxnFM/ADmO4zgOVD+AVVVVVVVNP0BVVVVVVUA/AMdxHMdxQT8AOY7jOI5DPwAAAAAAAEM/AAAAAAAAQT8AAAAAAAAoPwDHcRzHcSQ/oKqqqqqqQT8AjuM4juNMP4Acx3Ecx0U/QI7jOI7jRD8AVFVVVVX1PgA5juM4jlU/AMdxHMdxLD9AVVVVVVVCPwAAAAAAACg/ADmO4ziOQz/AcRzHcRwzP1hVVVVVVSG/AAAAAAAAJD8AchzHcRxBvwA5juM4jkm/AHIcx3EcN78AAAAAAAAYPwByHMdxHFM/gOM4juO4Uz+gqqqqqqpOP4Acx3EcR1A/ADmO4ziOXj+Q4ziO47hRP3Icx3EcR1Q/gOM4juM4Qj8gx3Ecx3E4vwDHcRzHcUu/AOQ4juM4Ir+A4ziO4zhBv+A4juM4jj2/QI7jOI7jQr8AAAAAAAAQvwByHMdxHC8/AI7jOI7jKD8AAAAAAAAovwCQ4ziO4/g+AI7jOI7jNL+AVVVVVVVBvwCrqqqqqjw/AFZVVVVVFT+AHMdxHMc1PwByHMdxHDk/gOM4juM4Tz8AjuM4juM6P4DjOI7jOCo/AOQ4juM4ND8AAAAAAAAgv0CO4ziO40I/wKqqqqqqMr8Ax3Ecx3E4P4DjOI7jOB6/IMdxHMdxFD8AwHEcx3HsvoBVVVVVVVw/AAAAAAAAUz8AAAAAAAAyP0CO4ziO4z4/wKqqqqqqZb9AjuM4juNcvwAAAAAAAF+/AAAAAAAASL8AOY7jOI5DP+A4juM4jko/VFVVVVVVPT8AjuM4juMyP6qqqqqqKnE/gBzHcRwHaj8AAAAAAMBkP0BVVVVV1V4/x3Ecx3E8cD8gx3EcxzFlP4Acx3EcB2E/wKqqqqoqWz+A4ziO47hYPwA4juM4ji8/AAAAAAAAST8AHMdxHMc3P4DjOI7juFE/AMdxHMdxHD/AcRzHcRw7PwA4juM4jhs/ABzHcRzHTj8AOI7jOI4jP4BVVVVVVTO/gFVVVVVVHT8Aqqqqqqo8PwDHcRzHcUI/AFRVVVVVHT8AHMdxHMfxvgDIcRzHcSw/AFRVVVVVIT8AyHEcx3EkPwDIcRzHcQy/AFZVVVVVOb8AcBzHcRwHPwByHMdxHDG/AFVVVVVVBT8A5DiO4zhLPwAcx3EcxyW/AMdxHMdxNL8gx3Ecx3FLvwBWVVVVVR0/AAAAAAAAJD9AjuM4juNAvwDkOI7jODq/AI7jOI7jND9gVVVVVVVBP4Acx3EcxzW/AJDjOI7j+L4AchzHcRwxPwDAcRzHcey+HMdxHMdxOr8AqqqqqqouP8BxHMdxHEI/AMdxHMdxQD+A4ziO4zhBPwAAAAAAACy/cBzHcRwHab9YVVVVVZVpvwAAAAAAgFu/QFVVVVXVVr9YVVVVVZVov+A4juM4jmq/wHEcx3GcUL+AVVVVVVVPv1hVVVVVlWW/gBzHcRwHYL8Ax3Ecx3FRvwA5juM4jlO/wHEcx3GcVr+AqqqqqqpKvwCQ4ziO4/g+ADiO4ziOEz+AHMdxHMdHvwCrqqqqqjo/gBzHcRzHQD8AVVVVVVUVPwCO4ziO40a/AAAAAAAAND8AjuM4juMkvwDHcRzHcRS/ABzHcRzHPb8AyHEcx3EUvwAAAAAAADo/AKuqqqqqEr8Aq6qqqqowP4Acx3Ecxym/gFVVVVVVBb8AOY7jOI4xv4Acx3Ech2C/wHEcx3GcX79AjuM4jmNRv3Acx3Ecx0i/AHIcx3EcQL+Aqqqqqqo4vwAAAAAAABA/AHIcx3EcMT9AVVVVVdVVP1BVVVVV1VU/VFVVVVVVTD+AVVVVVVVHPwAAAAAAAEa/AKuqqqqqML+AHMdxHMdAvwAAAAAAAD6/VFVVVVXVUb+AHMdxHEdRv4Acx3Ecx0S/ABzHcRzHMb8AAAAAAAAyvwCO4ziO4yy/AKqqqqqqJj8AcBzHcRwnvwByHMdxHEA/gOM4juM4RT8AHMdxHMcZv8CqqqqqqjQ/AFhVVVVVBT8Aq6qqqqo0PwDAcRzHcew+4DiO4ziOQT8AHMdxHMcxPwA5juM4jj8/AOQ4juM4Lj8Ax3Ecx3E8PwBQVVVVVfU+QI7jOI7jQj8AAAAAAAA0PwDkOI7jODy/AAAAAAAAKD8AVVVVVVU/vwDgOI7jOA4/IMdxHMdxRr8AjuM4juNSPwA5juM4jkA/AAAAAAAAMj8AOY7jOI4xv0CO4ziOI2Q/kOM4juM4XT85juM4jmNQPwA5juM4jks/juM4juO4aL+AHMdxHMdfvwDHcRzH8Vi/gOM4juM4R785juM4jqNnvyDHcRzH8WK/YFVVVVVVW78AOY7jOA5Sv4Acx3EcR1y/AAAAAAAAQL8AyHEcx3EgvwAcx3Ecxz2/AI7jOI7jMr+AVVVVVVVJv8BxHMdxHEA/AKuqqqqqGr+AqqqqqqpHPwByHMdxHCO/gKqqqqqqPj+AVVVVVVUhv4Cqqqqqqkg/AHIcx3EcLz8AAAAAAABAPwDHcRzHcSA/AJDjOI7jCL+gqqqqqqo4P+A4juM4jhO/AMdxHMdxMD+AHMdxHMdYP4DjOI7juF4/wHEcx3GcUT8gx3Ecx3FLP4DjOI7jeG0/wHEcx3EcYz+wqqqqqqpbP0BVVVVVVU0/kOM4juNYcD9wHMdxHAdgPwAAAAAAgF8/gBzHcRzHQT/IcRzHcRxWP0CO4ziO41E/gBzHcRzHSz8AHMdxHMc5PyDHcRzHcU0/YFVVVVXVUD8AjuM4juNDPwBVVVVVVTc/gBzHcRzHQz8AkOM4juMQPwA4juM4jiO/AHIcx3EcQj8AHMdxHMctPwCO4ziO4yg/AAAAAAAAID8AVVVVVVUlPwBYVVVVVRU/AMdxHMdxML8AOY7jOI4rP0BVVVVVVSG/AFVVVVVVQT8AchzHcRw7vwBUVVVVVRU/AHIcx3EcI78AAAAAAAAyPwA5juM4jkO/AKuqqqqqQj9AVVVVVVU5vwDIcRzHcSC/AFVVVVVVQb8AchzHcRwxvwCO4ziO4xi/QI7jOI7jcT9AjuM4jqNpP4Acx3EcR2A/sKqqqqqqUT8AjuM4juNJPyDHcRzHcVg/IMdxHMdxQj8A5DiO4zgivwByHMdxHDs/AFVVVVVVMb+A4ziO4zhAvwByHMdxHD+/AAAAAAAAIL/AcRzHcRwxv8Cqqqqqqi6/AAAAAAAARb8AchzHcRxEPwA4juM4jhM/AFVVVVVVKb9AVVVVVVVEvwDkOI7jOEM/gKqqqqqqQr8AchzHcRxFv0CO4ziO4zS/AMhxHMdxNj8A5DiO4zguvwAAAAAAAE+/AMdxHMdxML8Aq6qqqqpJPwDkOI7jODK/ADmO4ziOQr/kOI7jOI4rvwAcx3EcR1A/AFVVVVVVQ78AchzHcRxAvyDHcRzHcUG/AMhxHMdxMr8AOY7jOI5MvyDHcRzHcUC/AHIcx3EcI78AyHEcx3E6P4DjOI7juFK/AAAAAAAARL/IcRzHcRxAvwBWVVVVVSU/YFVVVVVVNb+Q4ziO4zhHv4BVVVVVVUK/sKqqqqqqQr+A4ziO4zg4vwByHMdxHDW/AAAAAAAAMr/gOI7jOA5av0BVVVVV1VG/gBzHcRxHUr8A5DiO4zhEvwAAAAAAQG+/wHEcx3GcY7+A4ziO4zhkv0CO4ziOI2O/QI7jOI5jXb+A4ziO4zhSvwCO4ziO40q/AMdxHMfxV7/gOI7jOI49vwDIcRzHcSS/ABzHcRzHN78AchzHcRxAv4DjOI7juFU/AAAAAAAARz8A5DiO4zgOPwAAAAAAACC/gOM4juO4Wz8AOY7jOI5KP4CqqqqqqkG/ADmO4ziOIz+A4ziO4/hpP4Acx3EcR1o/AI7jOI7jRD+gqqqqqqpAPwBVVVVVVUu/AOQ4juM4Pr8AjuM4juM8v4DjOI7jOEi/IMdxHMdxWj/gOI7jOI5KP0BVVVVVVUA/AKuqqqqqOj/AqqqqqqpCPwA4juM4jgM/AAAAAAAAMj+AqqqqqqpBv5DjOI7jOFM/ABzHcRzHNT8A5DiO4zgyvwAcx3Ecxz0/OY7jOI5jXT+A4ziO47hbP4Acx3Ecx1Y/AOQ4juM4Pj8gx3EcxzFlPyDHcRzH8V4/AAAAAAAAVz9AjuM4juNDPwAAAAAAoHE/ADmO4zhOZD+A4ziO4zhYP+A4juM4jkM/AFVVVVVVQj8AyHEcx3EcPwAAAAAAACy/cBzHcRzHOb8AOY7jOI5lP4BVVVVVVU0/AFVVVVVVKT+AHMdxHMc1P6CqqqqqKlC/gOM4juM4Tb/AqqqqqqpJvwCO4ziO40S/4DiO4ziOQj8Aq6qqqqoiPwByHMdxHDm/AI7jOI7jID/gOI7jOI45PwAcx3Ecxzm/AFVVVVVVNb8AjuM4juMwvxzHcRzHMWW/UFVVVVWVY7/gOI7jOE5hvwAAAAAAgFe/sKqqqqoqcr8AAAAAAEBuvwAAAAAAQGS/kOM4juO4XL+AHMdxHMdov4Acx3EcR2m/gBzHcRxHXL8AAAAAAABVvwCsqqqqqjA/AOA4juM4Fr8A5DiO4zgmPwAAAAAAACy/ABzHcRzHUb8AAAAAAAA8vwDHcRzHcUa/wHEcx3EcRL8AVlVVVVUxvwCsqqqqqhK/AMdxHMdxPL+AHMdxHMc5vwAcx3Ecx0g/AMdxHMdxQb8AOY7jOI5Iv0CO4ziO40m/AGBVVVVV9T4AchzHcRwvv0CO4ziO4zK/QFVVVVXVVb/AcRzHcZxgvwAAAAAAgGK/QI7jOI5jWL/AqqqqqipUvwAAAAAAAE+/ADmO4ziORb+AqqqqqqpNvwByHMdxHEK/AMdxHMdxOL8AjuM4juNBv4Acx3Ecx0m/ADiO4ziOM7/gOI7jOE5iv+A4juM4DmK/QI7jOI4jYb8Aq6qqqqpNvwDHcRzHcUO/wHEcx3EcWr/AcRzHcRxHvwDHcRzHcUe/ADmO4ziOWb9AjuM4jqNgv4Acx3Ecx1W/wHEcx3EcR78AAAAAAAAAv4DjOI7jOES/AAAAAAAARb8AjuM4juM0vziO4ziOY3S/sKqqqqoKcL+A4ziO43hsv4DjOI7juGK/AAAAAACAaL8AAAAAAIBhv8BxHMdxHGC/gOM4juM4U7+AHMdxHMdZvwByHMdxHFS/gBzHcRxHUL8AchzHcZxQvwBWVVVVVRW/AFVVVVVVKb+A4ziO4zgwvwAcx3Ecxze/wHEcx3GcUT8AchzHcRxBPwCO4ziO4zQ/AAAAAAAAIL/AcRzHcZxTPwAcx3EcxyW/AI7jOI7jGL9AVVVVVVUxP4Acx3Ecx1Y/gOM4juM4SD+AHMdxHMc9P8BxHMdxHEM/QI7jOI7jUT8gx3Ecx3FQP4DjOI7jOEI/ADmO4ziORj/IcRzHcVxjP3Acx3EcR10/5DiO4zgOVT/gOI7jOI5UPyDHcRzHsWU/QI7jOI7jUj/gOI7jOI5XP4BVVVVVVUY/gOM4juN4aT/AqqqqqqpaPwAAAAAAAFg/AAAAAAAASz9YVVVVVdVhP6iqqqqqqlo/HMdxHMdxUz/AqqqqqqpPPwDkOI7jOBa/AOQ4juM4PD8AOY7jOI45P0CO4ziO4zw/AI7jOI7jQ78AHMdxHMcpv4BVVVVVVT8/wHEcx3EcQr8AyHEcx3EUv4DjOI7jODo/ADmO4ziOGz9AVVVVVVVJv0CO4ziOo2m/wHEcx3GcYb+AHMdxHMdev5DjOI7jOFy/AAAAAAAAT78AAAAAAABSvyDHcRzHcU2/AMdxHMdxSb8AjuM4juNIv8BxHMdxHEa/gOM4juM4R7+A4ziO4zgyv2BVVVVVFXK/sKqqqqpqaL84juM4jqNmvyDHcRzHcVu/AMdxHMfxbb8AOY7jOI5kv8BxHMdxHFu/wHEcx3EcT7+AHMdxHEdSv4DjOI7jOE+/ABzHcRzHKb8AAAAAAAAAAAA4juM4jhO/AKuqqqqqIr/AcRzHcRxAPwA5juM4jjG/AOQ4juM4Kj+AHMdxHMdJvwByHMdxHCe/AAByHMdxzD5AjuM4juM2PwDAcRzHccy+sKqqqqqqMj8Aq6qqqqoqvwAAAAAAAEo/gOM4juM4Rz8AwHEcx3HsvgAAAAAAADI/AAAAAAAAED+AVVVVVVVJPwByHMdxHDc/AFBVVVVVBb9AjuM4juNTPyDHcRzHcVA/VFVVVVXVUT/AcRzHcRxEPwA5juM4jje/AFRVVVVVBT8Aq6qqqqoiv0CO4ziO4z6/AOQ4juM4Lj8AyHEcx3EMPwBUVVVVVQW/AMdxHMdxMr+AqqqqqqpNv+A4juM4DlC/gBzHcRzHTr+gqqqqqqpQv2BVVVVVFWC/IMdxHMdxXb8gx3Ecx/FXv4Acx3Ecx0y/ADmO4ziOUL/gOI7jOI5Gv8hxHMdxHFC/AHIcx3EcOb+AqqqqqqpKvwByHMdxHEa/QFVVVVVVQb/gOI7jOI5CvwByHMdxHEm/gKqqqqqqUb8AHMdxHMc5vwAcx3Ecx/G+AOQ4juM4Kr8AOI7jOI4vPwAgx3Ecx/G+gBzHcRzHJT8AOI7jOI4TP4Acx3EcxzO/AAAAAAAAAD+A4ziO4zgyP4DjOI7jOEG/ABzHcRzHAT+AVVVVVVUxvwDAcRzHcew+IMdxHMdxS7+oqqqqqqo0vwAAAAAAADK/AAAAAAAAML9QVVVVVVVVv4DjOI7jOE2/wKqqqqqqQ78A5DiO4zg8vwDHcRzH8Va/gKqqqqqqTb8AAAAAAAA+v4DjOI7jOFK/QI7jOI5jWL9AjuM4juNSv+A4juM4jky/wKqqqqqqRL8AOY7jOI5bvyDHcRzH8VC/wKqqqqqqTL8AOY7jOI5FvwCO4ziO4yi/ABzHcRzHIb8Ax3Ecx3EgvwAcx3EcxwE/AFVVVVVVNb8A5DiO4zgiv4Acx3Ecxy2/AMhxHMdx/L6Aqqqqqqoyv4Acx3Ecxze/AMhxHMdxHL8AAAAAAAA2vwDAcRzHcew+AFVVVVVVKb/Aqqqqqqo+PwAAAAAAACi/AAAAAABAYb9gVVVVVdVcv1BVVVVVVUa/gOM4juO4VL8AjuM4jmNQvwDAcRzHcew+AKqqqqqqIj/gOI7jOI43P4DjOI7juFS/AAAAAAAAGL+A4ziO4zgyPwCrqqqqqhI/oKqqqqoqab8gx3Ecx3FXv+A4juM4jkm/AI7jOI7jPL/AcRzHcVxgv8BxHMdxHFW/ADmO4ziOQL8AjuM4juNIv+A4juM4TmO/yHEcx3EcUr9yHMdxHMdFv+A4juM4DlW/cBzHcRxHYL+gqqqqqqpTvwAAAAAAAEy/AHIcx3EcPb8AAAAAAIBdv8BxHMdxHFq/gBzHcRzHXL8AchzHcRxQv8hxHMdxnGO/QI7jOI7jUr9YVVVVVdVYvwDHcRzHcTa/gOM4juM4VL8Ax3Ecx3FGv4Acx3Ecx0G/AOQ4juM4Fj8AyHEcx3EsPwBwHMdxHBe/ABzHcRzHAb8AchzHcRwvvwCO4ziO40g/gOM4juM4Tj8AAAAAAABMP6CqqqqqqkM/ADiO4ziOJz8Ax3Ecx3EgP0BVVVVVVTk/AFVVVVVVPT/gOI7jOI5YP0CO4ziO40I/oKqqqqoqUj+AHMdxHMc9P4CqqqqqqkO/AHIcx3EcHz8AAAAAAABAvwCrqqqqqjC/AMdxHMfxUr/gOI7jOI5DvwCQ4ziO4/g+AAAAAAAAQL8AyHEcx3E4P4BVVVVVVUI/ABzHcRzHNz8A5DiO4zgOvwCrqqqqqkE/AI7jOI7jOD8AOY7jOI4jPwDHcRzHcRw/AKyqqqqqGj8AOI7jOI4bv0CO4ziO4zy/AJDjOI7j+L6AHMdxHMdOv5DjOI7jOEm/IMdxHMdxRr8AjuM4juNJvwByHMdxHE4/gBzHcRzHRD8A5DiO4zgivwA5juM4jjE/ADiO4ziONz+gqqqqqqpIv6qqqqqqqia/AFhVVVVVBT+wqqqqqqo+PwDAcRzHccy+AAAAAAAAGD8AVFVVVVUVPwCrqqqqqi4/AFZVVVVVKb8AHMdxHMclPwDIcRzHcSi/ADmO4ziOU78AchzHcRxNv4DjOI7jOEe/AHIcx3EcP78AchzHcRxHvwDkOI7jOEC/AKuqqqqqSL8A5DiO4zhFvwCqqqqqqji/AAAAAAAAIL8AsKqqqqoKPwDkOI7jOC6/ADmO4ziOPb8AqqqqqqoKvwDIcRzHcey+AHAcx3EcF78AqKqqqqoKPwAcx3Ecxzm/AKuqqqqqJr/AqqqqqqomvwByHMdxHEA/AMdxHMdxQT8AkOM4juP4voDjOI7jOCq/AOQ4juM4Lj8AchzHcRwXv4Acx3EcxzG/gOM4juM4Jr+AVVVVVVUzvwAAAAAAABC/gOM4juM4Mr8AAAAAAAAyv4Cqqqqqqi4/AMdxHMdxPj+AqqqqqqoSPwAcx3Ecxy0/AMhxHMdx/D4AAAAAAAAwvwBWVVVVVR0/AMdxHMdxNj8AjuM4juNOvwA5juM4jje/ABzHcRzHM78Aqqqqqqo0v7Cqqqqqqk+/VlVVVVVVWb/gOI7jOI5JvwA5juM4jk+/QFVVVVVVVb+A4ziO47hdv4Acx3Ecx02/UFVVVVVVR78AwHEcx3HcPgByHMdxHDW/gOM4juM4Qj+AHMdxHMchP4Acx3Ecx0A/gBzHcRzHVD8AAAAAAAAgPwBVVVVVVSk/gKqqqqqqRD8AOY7jOI5PP0CO4ziO40s/4DiO4ziORT+AqqqqqqpJvyDHcRzHcRy/AMdxHMdxNL8AOY7jOI41v4Acx3Ecx0E/AAAAAAAARz8Ax3Ecx3EsPwA5juM4jis/ADiO4ziOE7+A4ziO4zhIv0CO4ziO40C/gOM4juM4Mj8AyHEcx3EgPwBwHMdxHCu/gOM4juM4R78AjuM4juMgvwBWVVVVVTU/ADiO4ziOJz8AAAAAAAAQP4DjOI7jODw/gOM4juM4Q7+AHMdxHMczvwCO4ziO4yS/gKqqqqqqMj/AqqqqqqpOv2BVVVVVVU+/AHIcx3EcO78AjuM4juMkvwAAAAAAAFe/cBzHcRzHUb9AjuM4juMgvwAAAAAAAEK/AAAAAAAAWr9gVVVVVVVWvwDHcRzHcTq/AHIcx3EcK79AjuM4juNmv8BxHMdxXGK/gBzHcRxHWr8AAAAAAABSvyDHcRzHsWa/qKqqqqoqX79yHMdxHMdWv0CO4ziOY1O/AHIcx3EcKz8AOY7jOI5IPwA4juM4jhu/AJDjOI7j+L6AHMdxHMdEPwDgOI7jOA4/AAAAAAAAPj+AHMdxHMc1PwDHcRzH8VE/AHIcx3EcOz8AVVVVVVUtPwAAAAAAAEE/gBzHcRzHTT+A4ziO4zg8P4DjOI7jODA/gBzHcRzHNz8AOY7jOI5JPyDHcRzHcUA/IMdxHMdxRD8AqqqqqqomP8BxHMdxHGI/AMdxHMfxUz/gOI7jOA5aP3Acx3Ecx1M/AAAAAAAAZT8AchzHcRxXPwDHcRzHcUY/AAAAAAAAVz+AqqqqqipdPwA5juM4jlA/AHIcx3EcMT+A4ziO4zhGP4CqqqqqqkU/AOQ4juM4Dj9gVVVVVVVCP0BVVVVVVUo/gBzHcRzHQz+Aqqqqqqo6P4BVVVVVVT0/AOQ4juM4Fr/AcRzHcRwzP3Acx3Ecx0W/yHEcx3EcKz8AOY7jOI4xP5DjOI7juFK/AMhxHMdxDD8AOY7jOI4jvwCrqqqqqjS/AMhxHMdxHD8AwHEcx3HcvgAAAAAAAAAAAAAAAAAALD9wHMdxHMdTP4Acx3Ecx0Y/AAAAAAAATj8AchzHcRw5PwByHMdxHES/AKqqqqqqGr8AchzHcRw1vwCO4ziO4yy/AMhxHMdxLL+A4ziO4zgyv4DjOI7jODY/AI7jOI7jKL9AVVVVVVVRPwA5juM4jks/AAAAAAAAOD/AcRzHcRxGPyDHcRzHcVc/QI7jOI7jVD8AAAAAAABMP4Cqqqqqqkk/gOM4juM4VT8gx3Ecx3FTP5DjOI7juFA/gOM4juO4UD+AHMdxHMdhPyDHcRzHcVQ/OI7jOI5jUj+AqqqqqqpNPwAAAAAAQGM/AKuqqqqqRz+AHMdxHMdEP+A4juM4jkA/AMhxHMdxOD8AchzHcRwvP4Acx3Ecx0Q/AI7jOI7jKD8A5DiO4zg4P4Cqqqqqqji/ADmO4ziOKz8Ax3Ecx3EgPwAAAAAAgFa/ADmO4ziOJ7+Aqqqqqqo4vwBVVVVVVTG/4DiO4ziOXb+Q4ziO4zhVv+Q4juM4DlC/AI7jOI7jMr9wHMdxHMdfv0CO4ziO41C/wHEcx3EcSb+AqqqqqqpOvwAAAAAAgGq/wHEcx3FcZb+gqqqqqipgv4BVVVVVVV6/IMdxHMexb7+Q4ziO4zhnvziO4ziOo2K/4DiO4zgOV78Ax3Ecx3FUvwAAAAAAAEu/wKqqqqqqQ78AjuM4juMYvwCrqqqqqke/AI7jOI7jPL8AHMdxHMcZv2BVVVVVVUA/AHIcx3EcQ78AAAAAAAAwPwCO4ziO4yC/4DiO4ziOQj8AOI7jOI4vPwAAAAAAAAA/QI7jOI7jQz8AAAAAAAAoP4Acx3EcxzG/AAAAAAAARr8A5DiO4zgqv4DjOI7jOCq/IMdxHMcxa7+Q4ziO47hov+Q4juM4jlS/gBzHcRzHQr8gx3Ecx3Fqv6yqqqqqqmC/4DiO4zgOU78AyHEcx3EcvwAAAAAAAFC/AI7jOI7jRT8AOY7jOI4/P8BxHMdxHDc/AFRVVVVVKT8AOY7jOI5EP8Cqqqqqqkc/wHEcx3EcQT8AAAAAAABEPwAAAAAAAFE/gBzHcRzHST+Aqqqqqqo+PwAcx3EcxzM/OY7jOI7jTD8gx3Ecx3FBPwDHcRzHcUE/wHEcx3EcYz/AqqqqqqpcP0CO4ziO41g/sKqqqqoqVD+AVVVVVdVcPwAAAAAAgFI/x3Ecx3GcUz8AOY7jOI5LPziO4ziO40U/QFVVVVVVRj8AAAAAAABOPwAAAAAAAE8/QFVVVVVVQD8AchzHcRw1PwDHcRzHcT4/AFVVVVVVQD8AOY7jOA5SP4Acx3Ecx1E/gFVVVVVVTT8AAAAAAABQP4Acx3Ecx1Q/AI7jOI5jUT+A4ziO47hQPwAAAAAAAE4/AMhxHMdxOL8AyHEcx3E6vwBVVVVVVUK/AHIcx3EcN78AjuM4juMwP6Cqqqqqqkk/gOM4juM4TT+AHMdxHMdGPwAAAAAAAD6/AFVVVVVVNT8AyHEcx3H8PsBxHMdxHDs/AAAAAAAASj8Ax3Ecx3FCP4DjOI7jODo/AOQ4juM4Fj8AchzHcRxFP2BVVVVV1VU/gBzHcRzHSD8AOY7jOI4zP2BVVVVVVVE/oKqqqqqqRj+A4ziO4zhCPwDkOI7jOCq/YFVVVVVVTz/AcRzHcRxNPyDHcRzHcUQ/AMdxHMdxSD/gOI7jOA5eP4DjOI7jOFo/YFVVVVXVUj8A5DiO4zg6P0CO4ziOY18/AAAAAACAUj9AVVVVVdVUPwA5juM4DlY/5DiO4ziOYT8AAAAAAIBgPxzHcRzH8Vs/YFVVVVXVVz9AjuM4jiNoPwAAAAAAQGE/cBzHcRzHYT84juM4juNbP4DjOI7juFc/QFVVVVVVUj9AVVVVVVVKP4DjOI7jOEo/AOQ4juM4Lj8A5DiO4zgqPwDAcRzHcdy+ADmO4ziOMz+AHMdxHMdOP8BxHMdxHFc/ADmO4ziOSj+AHMdxHMcpPwBWVVVVVSk/rKqqqqqqSj9AjuM4juM+PwAAAAAAADA/ADmO4ziORj8AOY7jOI5PP0CO4ziO40Y/YFVVVVVVQD8Ax3Ecx7FlPyDHcRzHcVQ/4DiO4ziOWD/AqqqqqqpNPwA5juM4zmY/gFVVVVVVXj8AOY7jOI5UP2BVVVVVVUM/AHIcx3EcQz8AchzHcRxCPwByHMdxHC8/AAAAAAAAAL9AVVVVVVVSP8CqqqqqqkM/AAAAAAAANj8Ax3Ecx3Eov4Acx3Ecx0U/AAAAAAAATD+A4ziO4zg0PwAAAAAAACA/4DiO4ziOST+wqqqqqqpLP4DjOI7jOBY/QI7jOI7jQT9AjuM4juNRPwByHMdxHCc/ADmO4ziOOz8AVVVVVVU9P8BxHMdxnFM/AHIcx3EcRD8AAAAAAABFPwCqqqqqqj4/ADmO4ziOLz8gx3Ecx3FFPziO4ziO4zo/ABzHcRzHAb8AOY7jOI41PwByHMdxHD0/gKqqqqqqPj+A4ziO4zgivwDHcRzHcTg/AAAAAAAASz+gqqqqqipRP8CqqqqqqjY/AHIcx3EcJz8Ax3Ecx3FDPwAAAAAAADQ/AHIcx3EcK78AyHEcx3EsPwDkOI7jOCI/ADmO4ziOPT8A5DiO4zgOPwA4juM4jis/QI7jOI7jMD9AVVVVVVUdvwA5juM4jj2/gBzHcRxHWr+AHMdxHEdav0CO4ziOY1W/cBzHcRxHU78AOY7jOA5SvwBVVVVVVUG/AI7jOI7jQL8AHMdxHMcBvwDkOI7jOCq/AMBxHMdx/L4AwHEcx3HcvgA5juM4jj0/AHIcx3EcOb8A5DiO4zgOvwAAAAAAAD6/AHIcx3EcHz8AOY7jOI5Mv8BxHMdxHEa/AHAcx3EcB78AchzHcRw7v8Cqqqqqqj6/AAAAAAAATb9wHMdxHMc7vwBUVVVVVQW/oKqqqqqqT7+Aqqqqqqo+v4Cqqqqqqja/ADmO4ziOMb+AHMdxHEdbv4BVVVVVVU6/AKqqqqqqLr8Ax3Ecx3FHv7Cqqqqqqlu/cBzHcRxHVb+Q4ziO4zhRv0CO4ziO40e/AHIcx3EcSb8AOI7jOI4TvwDAcRzHccw+wKqqqqqqSL8AOY7jOI5CP4BVVVVVVTc/AKqqqqqqEr8AAAAAAAAAP4BVVVVVVUG/AMdxHMdxOL+A4ziO4zgwv0BVVVVVVUG/gOM4juM4Nj8AyHEcx3EMPwAcx3EcxxG/ABzHcRzHKb9gVVVVVdVbP+A4juM4jlI/ADmO4ziOPz/AqqqqqqpHP4CqqqqqKl0/IMdxHMfxVD/AcRzHcRxPP4Acx3EcR1A/gBzHcRwHZj+AVVVVVVVfP4Acx3Ecx1I/WFVVVVVVUT8Ax3Ecx/FgPwDkOI7jODQ/gOM4juM4PD+A4ziO4zhCP4DjOI7juFY/gFVVVVVVOz/Aqqqqqqo6P4CqqqqqqjY/AMdxHMdxJL/AcRzHcRxGvwCO4ziO4yQ/AMhxHMdxIL8AAAAAAAAsP8hxHMdxHEM/AAAAAAAAID8AOY7jOI4xPwAcx3EcxxG/AMdxHMdxNj8A5DiO4zgmPwAAAAAAAD4/ADmO4ziOOb8A5DiO4zgwPwByHMdxHDE/AI7jOI7jOr8AAAAAAAA4P+A4juM4jkc/gBzHcRzHGb8A5DiO4zgiP0CO4ziO41M/gKqqqqqqNj8AVVVVVVUlPwDkOI7jOCK/AI7jOI7jTz8A5DiO4zgyP4Acx3Ecx0M/ADmO4ziOKz8Ax3Ecx3FVP0CO4ziOY1M/gFVVVVVVOz+AHMdxHMcpP4CqqqqqqkQ/AMdxHMdxLD9AVVVVVVU1PwAAAAAAAAC/QI7jOI5jWD+gqqqqqqpSP4DjOI7jOEg/YFVVVVVVRD+wqqqqqspyPxzHcRzHMWU/VFVVVVUVYz/AcRzHcRxTP+A4juM4Tmw/4DiO4zhOYD8AAAAAAIBaP4BVVVVVVUo/ABzHcRzHTD8AAAAAAIBRP4Cqqqqqqkc/sKqqqqqqQT8AcBzHcRwjPwDHcRzHcUA/wHEcx3EcQz8Aq6qqqqoSvwCO4ziO4zg/wKqqqqqqRz+AHMdxHMc9PwBVVVVVVSU/AHIcx3EcPz8cx3Ecx3FCPwAAAAAAADY/AMhxHMdxFD+gqqqqqupvP4DjOI7jOGo/cBzHcRwHYD/gOI7jOI5VPwCO4ziOY1k/gBzHcRzHTj9yHMdxHMdCPwA4juM4jhO/HMdxHMdxUr9AjuM4juNBv4BVVVVVVU+/AMdxHMdxSL+A4ziO4zhZvwByHMdxHEm/gBzHcRzHVr8AOI7jOI4/v4DjOI7jOGC/AI7jOI7jWb9AjuM4jmNZv4BVVVVV1VO/ADmO4zgOVr8AchzHcRxXvwDHcRzH8VO/AOQ4juM4S78A5DiO4zhNvwAcx3Ecx0e/ADmO4ziOQr8AAAAAAABBvwDAcRzHcew+IMdxHMdxRb/AcRzHcRwxPwDkOI7jODK/AGBVVVVV5T4AVVVVVVU3vwBVVVVVVSk/gOM4juM4Oj8AOY7jOI5IPwAgx3Ecx/E+gOM4juM4ML+A4ziO4zgivwAAAAAAADQ/AFZVVVVVHT8Ax3Ecx3Egv4CqqqqqqiI/gKqqqqqqPL8AAAAAAAAwvwDHcRzHcSC/ABzHcRzHN78AAAAAAAAkPwA5juM4jhO/gBzHcRzHLb8AHMdxHMclv0BVVVVVVUO/wHEcx3EcTb/AqqqqqqpAvwCrqqqqqjS/AMdxHMdxPL+AHMdxHMdFv4BVVVVVVUa/AHAcx3EcL78gx3Ecx3FCP0BVVVVVVQU/wKqqqqqqEj8AOY7jOI4jPwAAAAAAAFc/ABzHcRzHOz8AchzHcRwrPyDHcRzHcT4/gFVVVVVVRT8Aq6qqqqo0PwAgx3Ecx/E+AAAAAAAAOj8AIMdxHMfxPgDIcRzHcRy/gOM4juM4MD+AVVVVVVUlv4DjOI7jOFA/AI7jOI7jGD/AqqqqqqpGPwAcx3EcxxG/AFVVVVVVPT+Q4ziO4zgivwAcx3Ecx/E+AI7jOI7jND8Aq6qqqqo8vwDkOI7jOB6/AI7jOI7jND+gqqqqqqpCvwByHMdxHEi/AAAAAAAAJL8Ax3Ecx3E0v4Cqqqqqqj6/gOM4juO4X78AHMdxHMdMv4DjOI7jOEa/AMdxHMdxLL8AyHEcx3EUvwDIcRzHcSS/AAAAAAAAJD8AchzHcRwjvwAcx3EcxzW/AFZVVVVVHb8Ax3Ecx3E0vwByHMdxHCe/AI7jOI7jOD8AjuM4juMkPwDkOI7jOCa/AAAAAAAAAD+A4ziO4zhDvwCrqqqqqgo/gOM4juM4Mj8A5DiO4zgmPwAAAAAAADy/AAAAAAAAGD8AOY7jOI45vwAcx3Ecxz0/AAAAAAAANL8Ax3Ecx3FAvwCO4ziO4yi/AMhxHMdxHD9AjuM4juNKP7CqqqqqqkE/HMdxHMdxNj8Ax3Ecx3EkPwBQVVVVVfU+ABzHcRzHGb8AOY7jOI4xPwCO4ziO4yi/ABzHcRzHKT8AOY7jOI4zvwAAAAAAADY/AHIcx3EcF78AcBzHcRwXPwCQ4ziO4wi/AFBVVVVV5T5AVVVVVVU1vwByHMdxHEW/AHIcx3EcHz8AOY7jOI4jvwDHcRzHcRw/ADmO4ziOQb+AHMdxHMchvyDHcRzHcUW/gFVVVVVVR7+AHMdxHEdbv0BVVVVVVVm/QI7jOI7jVL8AAAAAAAA8vwDkOI7jOFe/gFVVVVVVVb8Ax3Ecx3FQv4DjOI7jOEO/AFRVVVVVJT8AAAAAAAAYvwCrqqqqqi6/ADmO4ziONz+AHMdxHMdCP4CqqqqqqjI/AMdxHMdxKD8AOY7jOI5GP0CO4ziO40k/ADmO4ziOQD+AHMdxHMc1P4CqqqqqqkU/cBzHcRxHXT84juM4juNRP6uqqqqqqkQ/wHEcx3EcSD/gOI7jOE5gP0CO4ziOY1Y/YFVVVVXVUj8AjuM4juNLP8BxHMdxHFI/gBzHcRzHRj+A4ziO4zhJPwDHcRzHcUM/AAAAAACAWT/AcRzHcRxQP+A4juM4jkQ/AHIcx3EcK78AAAAAAAA0v0CO4ziO40O/ADiO4ziOEz+AqqqqqqoyvwDHcRzHcU4/QI7jOI7jQT8AkOM4juP4PgCO4ziO4xA/AMdxHMdxND+AHMdxHMc1P0CO4ziO4z4/gBzHcRzHMz+AHMdxHMdCP4BVVVVVVS0/AKqqqqqqEr8AIMdxHMfxPoCqqqqqqjo/ADmO4ziONz+A4ziO4zg8vwCQ4ziO4/i+QI7jOI6jYL8gx3Ecx/Fav+A4juM4jlK/AMdxHMdxR78AOY7jOA5YvwDHcRzH8VO/AAAAAAAASr8AAAAAAAA6vwCsqqqqqiK/ADiO4ziOE78AWFVVVVX1PgCrqqqqqho/AI7jOI7jSz8AOI7jOI4bP6CqqqqqqkI/gOM4juM4QT8AyHEcx3EcPwBYVVVVVfU+AAAAAAAART8A5DiO4zgwPwDHcRzHcRw/AAAAAAAAQr8cx3Ecx3EwvwDAcRzHcdw+AAAAAACAU7/AqqqqqqpGvwAAAAAAAEi/ABzHcRzHPb+A4ziO4zhIvwBVVVVVVTm/AFZVVVVVLb8AkOM4juMQPyDHcRzHcVg/UFVVVVXVWD8AAAAAAABIP0BVVVVVVUs/AMdxHMfxVj9AVVVVVVVJP0CO4ziO40g/AKuqqqqqKj8AchzHcZxTP4Cqqqqqqk4/AAAAAAAARj9gVVVVVVVDP8Cqqqqqqlk/ABzHcRzHKb8AjuM4juM+P4CqqqqqqiI/ADmO4ziOM78gx3Ecx3EyvyDHcRzHcUi/AAAAAAAAID8AAAAAAABSP8BxHMdxnFI/AAAAAAAAST9QVVVVVdVTP2BVVVVV1WI/AAAAAAAAWD8gx3Ecx3FTP0CO4ziO41M/oKqqqqrqZj9YVVVVVZVgP0CO4ziOY1M/ADmO4ziOSj9AjuM4juNTP0BVVVVVVUQ/YFVVVVVVUD8Ax3Ecx3FAPwCO4ziO4zA/AFVVVVVVOz8AOY7jOI5IPwBYVVVVVeW+ADmO4ziOTD8A5DiO4zgOvwDHcRzHcSw/4DiO4ziOSj8AjuM4juNDP3Acx3Ecx0M/wHEcx3EcPz8AVlVVVVUhPwAAAAAAgFQ/AAAAAAAAMj8AAAAAAABBP4Acx3Ecxzk/AI7jOI7jST+AHMdxHMdKPwAAAAAAADo/AMdxHMdxOj8AVVVVVVVOP4Acx3Ecx0I/wHEcx3EcQj8Ax3Ecx3E6P4CqqqqqKl8/AOQ4juM4Sj8AjuM4juNLP+A4juM4jjM/gOM4juO4Uz8A5DiO4zg2PwDkOI7jOCY/AMdxHMdxND8AHMdxHMcZP4DjOI7jODA/AOQ4juM4Lj8AHMdxHMcZP4DjOI7jOFA/AMdxHMdxQD8AAAAAAAAkPwA5juM4jj0/AAAAAAAART/AcRzHcRwfPwBVVVVVVRU/AHAcx3EcFz+Q4ziO4zhRP4DjOI7jOEA/wHEcx3GcUT8AAAAAAABBP+A4juM4jk8/AHIcx3EcOT8ArKqqqqoKPwDkOI7jODo/AJDjOI7j+D4Aqqqqqqo0PwAAAAAAADC/AOQ4juM4Nj8AyHEcx3EoPwCqqqqqqjw/AKuqqqqqMj8AjuM4juMyP4CqqqqqqjS/AHIcx3EcQD8AIMdxHMfxPgBwHMdxHBc/AFVVVVVVPz8AAHIcx3HcvgAcx3Ecxy0/AHAcx3EcIz8AchzHcRxCPwCO4ziO4zg/ADmO4ziOQj8AHMdxHMc3P8Cqqqqqqkk/gKqqqqqqNj8AAAAAAAAsPwAAAAAAADw/gOM4juM4ST8A5DiO4zgiPwBWVVVVVR0/gFVVVVVVRT/IcRzHcRxKP2BVVVVVVT8/IMdxHMdxMj8A5DiO4zg4P4Acx3Ecx0I/oKqqqqqqRj+AHMdxHMc1PwAcx3EcxzM/AAAAAAAASz8AOY7jOI4bPwByHMdxHDc/gOM4juM4RD8AAAAAAABNP0CO4ziO40U/4DiO4ziOUT8AVVVVVVU1P4Acx3Ecx0Y/ABzHcRzHGT8AVVVVVVU7PwByHMdxHEg/AI7jOI7jNj8AjuM4juNCP4DjOI7jOEQ/AOQ4juM4Lj8Ax3Ecx3EwvwCO4ziO4zw/AOQ4juM4Fj8AkOM4juMsP4Acx3Ecx0w/gOM4juM4QT+AHMdxHMdEP4Acx3Ecx0A/AGBVVVVV9b4A5DiO4zg2PwByHMdxHD8/AMhxHMdx7D4AyHEcx3FOPwCO4ziO4zg/AMBxHMdx/D4AWFVVVVXlPgA4juM4jj8/ACDHcRzHAb8AOI7jOI4Dv4Cqqqqqqio/AMdxHMfxWD8AjuM4jmNWPwCO4ziO40U/YFVVVVXVVD/AcRzHcRxgP8BxHMdxHFw/QI7jOI5jVD+gqqqqqqpSPwAAAAAAAE4/gBzHcRzHRj8AwHEcx3HsvoBVVVVVVUc/ABzHcRzHM7+AqqqqqqpCvwAgx3EcxwE/AMhxHMdxHL8AAAAAAAAsP4BVVVVVVUA/gKqqqqqqRD8AHMdxHMcxP4Acx3Ecx0Q/AAAAAAAATj8AAAAAAIBSPwA5juM4jj0/gBzHcRzHUT/AqqqqqipTP4Cqqqqqqkk/kOM4juM4Sj8AAAAAAEBiP4DjOI7juFw/QFVVVVXVUj8AOY7jOI43PwCO4ziO41U/gKqqqqoqXD/AcRzHcZxQP+A4juM4jkQ/ADmO4zgOWz8AchzHcZxcPwDHcRzHcU0/AAAAAAAAOj9AjuM4jqNiPwA5juM4jlg/4DiO4ziOUj8AOY7jOI5FP1hVVVVVVUo/gFVVVVVVSj8A5DiO4zg4PwAAAAAAABA/gBzHcRzHRT8AOI7jOI4nPwAAAAAAADg/AFVVVVVVRT8AchzHcRw/PwAAAAAAABC/AMdxHMdxOD8AchzHcRw5PwA5juM4jj0/AMBxHMdx7D4AjuM4juNMPwDkOI7jODA/AI7jOI7jRD8AAAAAAABMPwByHMdxHE8/IMdxHMdxQD8AkOM4juMkPwA5juM4jkM/ADmO4ziORz/AcRzHcRxHPwBgVVVVVfU+AOQ4juM4Mj/AcRzHcZxRPwAAAAAAAEU/AI7jOI5jUz8Ax3Ecx/FaP4DjOI7jOFE/AMdxHMdxPD8AOY7jOI5bP0BVVVVVVV0/sKqqqqqqVT9AVVVVVVVPP1BVVVVVVUE/AI7jOI7jQj+AqqqqqqpHPwByHMdxHEE/gFVVVVVVQD+AHMdxHMdBP4DjOI7jOEk/AHIcx3EcPz8AjuM4juNJPwDkOI7jODQ/AI7jOI7jTz8AchzHcRxFPwCO4ziO4zo/gBzHcRzHTT+AVVVVVVVCPwAAAAAAACw/gBzHcRxHZz/AqqqqqqpXPwAAAAAAAE8/qKqqqqqqTj9AjuM4jiNkPwA5juM4jlI/QFVVVVXVVD8AAAAAAABRP4Acx3EcR1M/gOM4juM4Sz8Ax3Ecx3E6P+A4juM4jkY/AHIcx3EcSj8AcBzHcRwXPwAAAAAAAEA/QFVVVVVVJT8Aq6qqqqpBPwA4juM4ji8/gBzHcRzHTD/gOI7jOI5BPwDHcRzHcUg/ADmO4ziOMT8AjuM4juM6PwAcx3Ecxxm/ADiO4ziOMb8AyHEcx3EgPwA4juM4jhs/AHIcx3EcJ78Aq6qqqqo0P1ZVVVVVVS0/AHIcx3EcBz8AVVVVVVUzP+Q4juM4jj2/AAAAAAAAQT+gqqqqqqo+PwCO4ziO4yy/AMdxHMdxQj8AAAAAAAAAPwBWVVVVVQW/AMhxHMdxFD8Ax3Ecx3E2PwCrqqqqqhI/AI7jOI7jOj8AOY7jOI41PziO4ziO41e/QI7jOI5jUL+AHMdxHMdQvwCO4ziO40W/gFVVVVVVT78A5DiO4zg8vwAcx3Ecxym/AFBVVVVVBT8AjuM4juM+vwCqqqqqqjC/AAAAAAAAPL8AAAAAAAAQvwCQ4ziO4xC/AFVVVVVVS78AyHEcx3EkvwByHMdxHDu/ADmO4ziOQL+AHMdxHMdBvwA4juM4jiO/ABzHcRzHLT8AHMdxHMdMv8BxHMdxHFm/AAAAAAAASL8gx3Ecx3FHvwCO4ziO41y/AMdxHMfxWr8AOY7jOI5Gv4DjOI7jOCa/AHIcx3EcQb8AIMdxHMcRvwDHcRzHcTa/gBzHcRzHQ78Ax3Ecx/FVvwCqqqqqqjq/AMhxHMdxNr8AWFVVVVX1vgAcx3Ecx0m/gFVVVVVVRL9AjuM4juM2v4Acx3Ecx0G/IMdxHMdxPL8AjuM4juMsvwA4juM4jie/AGBVVVVV5T4AAAAAAAAoPwBwHMdxHAc/AMhxHMdxJD8ArKqqqqoqPwDHcRzHcUA/AGBVVVVV9b4AAHIcx3HMvgByHMdxHEA/AAAAAAAANL8AOI7jOI4vvwAcx3Ecxzc/AKuqqqqqQT8Aqqqqqqo6vwCQ4ziO4wg/ADiO4ziOJz84juM4juNIPwA4juM4ji8/AAAAAAAAKL8AcBzHcRwXvwCrqqqqqho/AOQ4juM4QT8A5DiO4zgyPwA5juM4jkI/AAAAAAAAQj8AHMdxHMdSv4Acx3Ecx1C/AOQ4juM4Nr8AAAAAAAAAvwDkOI7jOEW/AMdxHMdxQ79gVVVVVVVAvwCrqqqqqiq/kOM4juM4T78AVVVVVVU3vwAgx3EcxwG/ADiO4ziOA78AwHEcx3HcvgAAchzHccw+AHIcx3EcJ78AkOM4juMovwByHMdxHD2/ADiO4ziOKz8AAAAAAAA0PwDHcRzHcUC/AAAAAAAAGD8AVlVVVVUhvwAgx3Ecx/G+AAAAAAAAAADAcRzHcZxVvwDHcRzHcVi/ADmO4ziOS7/IcRzHcRw9vwA5juM4jlu/gBzHcRxHWr8AjuM4juNOvyDHcRzHcUi/AAAAAAAAPr8AVlVVVVUxvwDHcRzHcUS/gBzHcRzHPz8Ax3EcxzFhv4CqqqqqKla/AKuqqqqqQ78A5DiO4zgOPwAAAAAAAFW/wHEcx3GcUL/AcRzHcRw5vwCO4ziO4zq/qKqqqqoqV78AOY7jOA5WvwCO4ziO40O/ADmO4ziOSr8AchzHcRxFvwA5juM4jju/AMhxHMdxJL8AOI7jOI4rvwDHcRzHcUa/ABzHcRzHQL8AOI7jOI4vvwByHMdxHDe/gBzHcRzHMb8A5DiO4zgqvwAcx3EcxzW/ACDHcRzHGT8AAAAAAABavwAAAAAAAFW/AMdxHMfxUr8AAAAAAAA4v4Acx3EcR1G/AI7jOI5jU78Ax3Ecx3FFv4Cqqqqqqi6/AHIcx3EcQT8AkOM4juMYvwDkOI7jOC4/IMdxHMdxOj8Ax3Ecx3FRPwA5juM4jkc/ADmO4ziONT8AAAAAAAA0P4Acx3EcR1U/ACDHcRzHET8A5DiO4zg2P8BxHMdxHEM/ADmO4ziOTT8AOY7jOI49PwDHcRzHcSw/gBzHcRzHSD8Aq6qqqqpFPwCO4ziO4zI/ADmO4ziORT8Ax3Ecx3E8P4DjOI7jOEc/WFVVVVVVJT/gOI7jOI5IPwA5juM4jjM/OI7jOI7jTz/gOI7jOI4zPyDHcRzHcVA/AAAAAAAATD/AqqqqqqpKP0CO4ziO4zQ/cBzHcRxHUj8AVlVVVVUlP4DjOI7jOFi/wHEcx3GcUr8AOY7jOI5Gv4Acx3Ecx0S/AAAAAACAUb+AHMdxHMdEvwCQ4ziO4xC/AHIcx3EcNT8Ax3Ecx3E8PwDHcRzHcUQ/AAAAAAAAQT8AkOM4juMsPwAAAAAAADY/AKqqqqqqOD+AqqqqqqpGPwCrqqqqqkE/gOM4juM4Qz8AAAAAAABIPwAAAAAAACA/AI7jOI7jOj+AqqqqqqpHPwDHcRzHcT4/AHIcx3EcTz8AVlVVVVU/PwDHcRzHsWk/AAAAAACAYz8AAAAAAIBUPziO4ziO408/AAAAAACAZj8AAAAAAABbP0CO4ziO41Q/QI7jOI7jUD+AqqqqqipbPwCO4ziO41Y/AAAAAAAAWD/IcRzHcRxRPwBAjuM4jhO/AHIcx3EcQ78AkOM4juMIPwBVVVVVVS2/AMdxHMdxSr8Ax3Ecx3FIvwBWVVVVVQU/gOM4juM4NL9gVVVVVVU1P4BVVVVVVUI/gOM4juM4RD8AyHEcx3EgPwA5juM4jjW/AJDjOI7jEL8AqqqqqqouvwCQ4ziO4yi/AFZVVVVVIb8AVlVVVVU5vwCrqqqqqji/AFRVVVVVLb8AAAAAAAAsvwCrqqqqqj4/AFVVVVVVMb8A5DiO4zg4PwDgOI7jOA4/AAAAAAAANr8AchzHcRwrvyDHcRzHcSi/AAAAAAAAJL8AkOM4juMsvwByHMdxHCu/AI7jOI7jCD8Aq6qqqqpGPwBUVVVVVS2/AOQ4juM4Nj8AAAAAAAAkPwCQ4ziO4z6/AMhxHMdxNL8A5DiO4zhFv4Acx3Ecxy2/gBzHcRxHYr9AjuM4jmNVv0CO4ziOY1G/ABzHcRzHAT+AHMdxHMc9PwByHMdxHDc/gBzHcRzHRj8AchzHcRwzPwAcx3EcxyU/AAAAAAAAPD8AOI7jOI4nPwAAAAAAADK/ADiO4ziOIz8AqKqqqqoavwCO4ziO40A/AKqqqqqqNL8AAHIcx3HMvgDkOI7jOCK/AMdxHMdxPj8A4DiO4zgWP8BxHMdxHFi/gBzHcRzHSr8AjuM4juNLvzmO4ziO40u/QFVVVVVVYL+A4ziO4zhRv4DjOI7juFK/AAAAAAAAIL+AqqqqqipRvwAAAAAAAAAAAMBxHMdx/L4AAAAAAABDvwByHMdxHFc/gOM4juM4Vz8AOI7jOI4/P+A4juM4jkc/AMdxHMdxRT8AAAAAAABAPwBYVVVVVeW+gBzHcRzHOb8AAAAAAIBbP4Acx3Ecx1U/AI7jOI7jTT8AqqqqqqoyP0CO4ziOY1A/gOM4juM4TD8AAAAAAABEPwDgOI7jOBa/AHIcx3EcQT8AkOM4juMIvwAcx3EcxzE/AKuqqqqqSD/AqqqqqipTP4BVVVVVVUQ/AMhxHMdxLD8A5DiO4zg2PwDIcRzHcSg/AFVVVVVVM78ArKqqqqoSv+A4juM4jkC/ADiO4ziOG78AVlVVVVU1vwAcx3Ecxzc/gKqqqqqqLj8A4DiO4zgOvwByHMdxHCe/ABzHcRzHAb/AqqqqqqomvwCQ4ziO4yi/ADiO4ziOLz8AkOM4juMIPwBVVVVVVQU/AKyqqqqqIj8AcBzHcRwjPwDkOI7jOBa/ADmO4ziOL78AyHEcx3EyP4BVVVVVVUc/AKuqqqqqLj8AOY7jOI4TPwDIcRzHcTA/gKqqqqqqQD+Aqqqqqqo6PwByHMdxHAc/AMdxHMdxQD/IcRzHcRw3PwByHMdxHCc/AFVVVVVVMb84juM4juM6PwAAAAAAADY/oKqqqqqqMj8AHMdxHMczv4Acx3EcxzE/AAAAAAAAAD8AchzHcRwvvwCqqqqqqiI/gOM4juM4Qz8gx3Ecx3FCP8BxHMdxHEQ/AHIcx3EcQD/gOI7jOI5EPwAcx3EcxzM/AKyqqqqqEj8Aq6qqqqo2PwCO4ziO4yS/ADmO4ziOM78AOI7jOI4nvwCO4ziO4za/AHAcx3EcF78AAAAAAAAYvwAAAAAAADY/AGBVVVVV9b4AAAAAAAAYvwCO4ziO4zS/AFZVVVVVJT8AIMdxHMcZvwByHMdxHD+/ABzHcRzHKb8AVVVVVVUzvwBgVVVVVfW+AI7jOI5jVr+AqqqqqqpMvwDHcRzHcUu/qKqqqqqqQr+A4ziO47hVvwDIcRzHcTC/ADmO4ziOQr9AVVVVVVUxvwAAAAAAAEG/AOQ4juM4Ir8AOY7jOI4xvwCO4ziO4xi/AHIcx3EcXD8AchzHcRw/PwCqqqqqqjA/gKqqqqqqNj+AHMdxHEdgP0BVVVVVVVE/YFVVVVVVQT8AchzHcRw5P2BVVVVVVTM/AI7jOI7jKD8AOY7jOI4zvwDAcRzHcew+AOQ4juM4Mr8AOY7jOI5Gv4CqqqqqqkK/AAAAAAAAGD8AjuM4juM4PwAcx3EcxyU/AHIcx3EcK78AQFVVVVXlvoAcx3Ecxz8/ADiO4ziOL78Aq6qqqqo0vwCO4ziO4zg/AMdxHMdxWj+AqqqqqqpEPwAAAAAAAFU/HMdxHMdxTj8AjuM4jmNVPwA5juM4DlI/AMdxHMdxPD8AVVVVVVUVPwCsqqqqqiY/AECO4ziOA78AOI7jOI4DP2BVVVVVVTW/AHIcx3EcWT+AHMdxHMdQPwDkOI7jOCa/AAAAAAAAAL+AHMdxHEdfPwA5juM4jks/QI7jOI7jTj8AOY7jOI4rv8BxHMdxHD8/AI7jOI7jPj8AjuM4juMkvwA5juM4jju/gFVVVVVVTT8AqKqqqqoKvwBVVVVVVTe/AFhVVVVVFT+AVVVVVVVAPwCQ4ziO4xi/AJDjOI7jGL8AcBzHcRwXPwByHMdxHDs/AOA4juM4Dr8AVVVVVVU7vwAcx3EcxzU/gFVVVVVVVD8AOI7jOI4nPwDIcRzHcSQ/VVVVVVVVSD+A4ziO4zhZPwBAjuM4jgO/AKqqqqqqIr8AwHEcx3HMvoCqqqqqKlQ/AFZVVVVVO78AVVVVVVUzv4Acx3Ecxxk/gKqqqqpqZz8AchzHcRxPPwByHMdxHEk/AI7jOI7jED+AVVVVVVVVP8Cqqqqqqkg/gOM4juM4OD8AjuM4juM6P7CqqqqqKlA/gOM4juM4RT8A5DiO4zgWPwCQ4ziO4yC/gFVVVVVVRz8Ax3Ecx3FEPwCO4ziO40U/AMhxHMdxFL8AchzHcRxLPwAcx3Ecxzk/AGBVVVVV5b4AyHEcx3E6v8BxHMdxHFM/AHIcx3EcQT8Ax3Ecx3E8PwCqqqqqqjw/wHEcx3Fccz/gOI7jOI5mP4Acx3EcR14/4DiO4ziOVz+gqqqqqqpzP8CqqqqqKmQ/wKqqqqoqVD+AHMdxHMdKPwA5juM4jlA/AFVVVVVVMz8AVFVVVVUdvwAAAAAAABi/AFVVVVVVTD8AchzHcRwvvwBVVVVVVTm/4DiO4ziOJz8AHMdxHMdAPwAcx3EcxzO/gFVVVVVVQb84juM4juNBvwDHcRzHcUM/AHIcx3EcM78AjuM4juMgP4DjOI7jOCq/AAAAAABAdD+AHMdxHAdhP8BxHMdxHF8/QI7jOI7jSD8AqqqqqqoyPwBVVVVVVRW/cBzHcRzHKb+AVVVVVVVBv5DjOI7juFC/AI7jOI7jRr8Ax3Ecx/FQvwCrqqqqqki/AAAAAAAAQT8AkOM4juMYPwDIcRzHcSQ/AMdxHMdxQ78Ax3Ecx3FAvwBwHMdxHB+/AI7jOI7jNr8AyHEcx3E8vwAcx3Ecxzc/AMdxHMdxIL+A4ziO4zhBv4DjOI7jOEe/QFVVVVVVTT8AHMdxHMfxvkCO4ziO4zS/QFVVVVVVQL8A4DiO4zgWPwByHMdxHCe/gOM4juM4R7/AcRzHcRw1vwAAAAAAAEU/ADiO4ziOM78AAAAAAAAovwA4juM4jhM/AAAAAAAAMj8AAAAAAAAyvwCO4ziO4zi/wHEcx3EcR78AkOM4juMkPwDkOI7jOD6/ADmO4ziOR7/IcRzHcRwnvwByHMdxHEI/AAAAAAAAOr8AAAAAAAA+vwAAAAAAADy/AI7jOI7jQD8AyHEcx3Eov4Cqqqqqqji/AMdxHMdxLL8AqqqqqqpGPwByHMdxHEa/AKyqqqqqLr8AOY7jOI5CvwDHcRzHcVU/AKiqqqqqLj8A5DiO4zgwvwByHMdxHEa/AOQ4juM4Ir9gVVVVVVVDv4DjOI7jODK/AFVVVVVVMb8AyHEcx3EkPwCqqqqqqiq/AMdxHMdxOL8AAAAAAABLvwDkOI7jOES/AAAAAAAAQr/AcRzHcRxDvwDHcRzHcTi/YFVVVVVVOb8AchzHcRw/vwDHcRzHcTa/ADmO4ziOQb+AHMdxHMc9vwAgx3EcxwE/AHIcx3EcM78AjuM4juM2v0CO4ziO4zq/AOQ4juM4Mr+AVVVVVVVBvwDkOI7jOCK/ABzHcRzHKb8AHMdxHMdKvwA5juM4jk+/AI7jOI7jNr8AjuM4juM+vwByHMdxHEC/AMdxHMdxRL8AchzHcRxBvwAAAAAAADy/AMdxHMdxJL8Ax3Ecx3EoPwDkOI7jOB4/AAAAAAAARD84juM4juMYPwDHcRzHcSw/AI7jOI7jSb9UVVVVVdVhP4Acx3EcR18/QI7jOI5jUz8AjuM4juNJPwDHcRzHsWc/QI7jOI5jZT8AAAAAAEBgP8dxHMdxnFM/gBzHcRxneT8AAAAAAOBxPyDHcRzH8Wk/4DiO4ziOWT8gx3EcxzFyP0CO4ziO42g/HMdxHMdxYT8AjuM4juNKP5DjOI7jOFM/ADmO4ziOTj8AkOM4juMYvwAAAAAAADa/AI7jOI7jMj8AIMdxHMcBPwCO4ziO4yS/AAAAAAAANL+AVVVVVVVAvwDkOI7jOCK/gFVVVVVVPb8AAAAAAAA4vwA4juM4jiu/AHIcx3EcL79AVVVVVVU7v4BVVVVVVT+/AMdxHMdxTr+sqqqqqipRv0CO4ziO40S/AI7jOI7jOr+AqqqqqqpDvwA5juM4jke/gOM4juM4RL8AAAAAAABEv2BVVVVV1XC/qKqqqqrqab/kOI7jOI5dvwDHcRzHcVa/oKqqqqpqZL9yHMdxHMdcv2BVVVVVVUW/QFVVVVVVU7/gOI7jOI5EvwA4juM4jhu/AHAcx3EcBz8A5DiO4zg8vwBYVVVVVeW+AHAcx3EcBz8AjuM4juMoPwAcx3Ecxze/AHIcx3EcRr8AAAAAAAA8vwAAAAAAACy/ADiO4ziOM78AAAAAAAA8vwAcx3EcxxG/AHIcx3EcOb9AjuM4juNKvwAAAAAAADg/ADmO4ziOKz+A4ziO4zgyPwDHcRzHcSi/ADiO4ziOIz/AqqqqqqpBv2BVVVVVVU2/AMdxHMdxOL8A5DiO4zgmPwAAAAAAADq/gBzHcRzHOb8Ax3Ecx3E+vwA5juM4Dlq/QI7jOI7jU78gx3Ecx/FSvwAAAAAAADy/AOQ4juM4Ir8AAAAAAAA8v1hVVVVVVUG/gKqqqqqqQL8AchzHcRw7vyDHcRzHcUu/gBzHcRzHIb+AHMdxHMdCvxzHcRzHsXC/IMdxHMcxab9gVVVVVVVkv0CO4ziOI2G/qqqqqqpKcr/gOI7jOM5pv4DjOI7jOGa/AMdxHMfxX79AjuM4jiNjvwA5juM4Dle/wKqqqqqqUL8AHMdxHMdJv0BVVVVVVVu/gKqqqqqqPL8ArKqqqqoKvwDHcRzHcTS/AAAAAAAAAAAA5DiO4zgWPwA4juM4jhu/4DiO4ziOQL8AOI7jOI4rvwDHcRzHcSg/gOM4juM4NL8AjuM4juMgPwDkOI7jOC4/AMdxHMdxDL/AcRzHcRwnPwCO4ziO4zq/gKqqqqqqR78Ax3Ecx3FPv4DjOI7jOEe/wKqqqqqqML+AVVVVVdVdvwAAAAAAAFm/IMdxHMdxWr8Ax3Ecx3FKvwDHcRzHcTY/ADmO4ziOKz+gqqqqqqo4vwDgOI7jOA6/kOM4juO4VL9AjuM4juNbv4Acx3EcR1S/AMdxHMdxUL9YVVVVVVVSv4DjOI7jOEq/AI7jOI7jRr8AHMdxHMc1vwAcx3Ecxxm/AMhxHMdxLL8AwHEcx3HcPgAcx3EcxyU/QI7jOI7jOL8AchzHcRwrPwDHcRzHcSC/gOM4juM4OL8AcBzHcRwXvwA5juM4jkA/wHEcx3EcRz+A4ziO4zg6PwBAjuM4jgO/AFVVVVVVRj8AOI7jOI4bPwAAAAAAADa/AIBVVVVV5T4AIMdxHMfxPgA4juM4jiM/AHIcx3EcMz8AOI7jOI4/vwCO4ziO40I/AFVVVVVVPT8AAAAAAAAkPwCqqqqqqja/AAAAAAAANL8AjuM4juMYv4DjOI7jOCa/AKqqqqqqEj8AyHEcx3EMvwDIcRzHcRy/AHIcx3EcMb8AOY7jOI45vwAAAAAAACy/gOM4juM4Hj8AOY7jOI41P4DjOI7jOGY/OI7jOI5jYD8AAAAAAABSPwByHMdxHEo/juM4juO4Yj+AHMdxHMdKPwAAAAAAAFg/gBzHcRzHSD8AjuM4juM4PwCrqqqqqhI/AAAAAAAAKD8AchzHcRwxv2BVVVVVVVu/gOM4juO4VL8AchzHcRxJvwCrqqqqqkW/sKqqqqoqXb8AAAAAAIBZvwCO4ziO40+/AI7jOI7jTL9AjuM4juNQvwA5juM4jj+/gFVVVVVVSr8AgOM4juP4vgAAAAAAAEO/AAAAAAAAMD8AchzHcRwfPwA5juM4jju/ADiO4ziOG78Ax3Ecx3E2vwDIcRzHcRy/AAAAAAAAQb8A5DiO4zgivwAcx3Ecxy0/gBzHcRzHNb+AHMdxHMcxPwBVVVVVVTk/YFVVVVVVIT+AHMdxHMc7vwCsqqqqqhK/gOM4juM4Qz8Ax3Ecx3FDPwA5juM4jje/AFRVVVVV9b6AqqqqqipTv4DjOI7jOEu/kOM4juM4QL8AOI7jOI4jvwA4juM4jhu/wHEcx3EcKz9wHMdxHMczvwAAAAAAABi/wKqqqqqqPr8AYFVVVVXlvgCO4ziO4yi/ABzHcRzHIT8gx3Ecx3E4PwDHcRzHcTI/AFVVVVVVOT8AjuM4juMoPwByHMdxHCc/AKqqqqqqIr8AOY7jOI4zPwDkOI7jOCY/AJDjOI7jGD8AVFVVVVUdPwCsqqqqqgo/gFVVVVVVLb8AyHEcx3EovwAAAAAAADw/AHAcx3EcB7+A4ziO4zg0PwDHcRzHcUs/gOM4juM4ND+AVVVVVVU9P0BVVVVVVTO/ADiO4ziOL78gx3Ecx3E0vwAAAAAAACi/AMhxHMdxLL8A5DiO4zhIvwAAAAAAAEC/AHIcx3EcNb9wHMdxHMdKvwCrqqqqqkq/QI7jOI7jR79YVVVVVVVYv8BxHMdxHEK/AI7jOI7jT7+A4ziO4zg6vziO4ziOY1a/AOQ4juM4OL/AcRzHcRwzvwBQVVVVVfW+AKyqqqqqGr8AOY7jOI4zv4Acx3EcxyG/AHIcx3EcOb8AAAAAAAAsvwBwHMdxHBe/AFZVVVVVHT8AjuM4juNIv4CqqqqqqkK/AKiqqqqqGr8AVVVVVVU3PwBVVVVVVTm/AMBxHMdx7L6AHMdxHMchvwDHcRzHcTQ/AFBVVVVV9T4AAAAAAAAyPwAcx3EcxxE/ABzHcRzHOb+AHMdxHMc7vwDAcRzHcdw+AMdxHMdxNL8AOY7jOI41v4BVVVVVVRW/YFVVVVVVJT8Ax3Ecx3E6vwByHMdxHDE/AFRVVVVVFT8A5DiO4zgqPwDIcRzHcdy+QI7jOI4jYT/AcRzHcRxLP0CO4ziO408/AFVVVVVVJT+gqqqqqupkPyDHcRzHcVk/HMdxHMdxWj8A5DiO4zg8P8hxHMdxHFw/AAAAAAAAOD8AqqqqqqouPwA4juM4jic/kOM4juO4Wz/gOI7jOI5QP4BVVVVVVUk/gKqqqqqqQD+AHMdxHMc1PwBgVVVVVeU+ADiO4ziOEz8AOI7jOI4nv4DjOI7jOEI/oKqqqqqqQb8AAAAAAAAkvwA5juM4ji+/ADmO4ziORz+AqqqqqqpBvwA5juM4jjO/oKqqqqqqML8AyHEcx3E0PwA4juM4jie/AFRVVVVVHb+AHMdxHMczvwAcx3Ecx1A/AOQ4juM4Nr8A5DiO4zg0PwCrqqqqqia/AI7jOI7jNj8AOI7jOI4DPwDkOI7jOC4/AHIcx3EcF78AyHEcx3E2vwAcx3Ecxzk/AAAAAAAAGD9AjuM4juM4vwA5juM4jjG/AKuqqqqqGr8AAAAAAAAyvwA5juM4jkq/AMdxHMdxRr9AVVVVVVU/vwByHMdxHCO/gOM4juM4Qb8AOY7jOI5WP0CO4ziO40Q/IMdxHMdxOD8Aqqqqqqouv2BVVVVVVTm/gBzHcRzHMb8AOY7jOI47vwDkOI7jOCa/yHEcx3Fcc7+oqqqqqiprvwAAAAAAgGi/QI7jOI7jX78AAAAAAIBlv8BxHMdxHF6/wHEcx3EcXb8AOY7jOI5Qv5DjOI7j+GK/ADmO4zgOXr9AVVVVVVVcvwDHcRzHcVG/AAAAAAAASb+AHMdxHMdTv4CqqqqqqkO/ABzHcRzHPb8Aq6qqqqoyvwCO4ziO40O/AKqqqqqqEj+AHMdxHMclPwDIcRzHcQw/AAAAAAAAAD8AjuM4juM0PwA5juM4jjO/AMdxHMdxNr8Ax3Ecx3EkvwAAAAAAACg/AI7jOI7jCD8AOI7jOI4Tv8hxHMdxHEE/cBzHcRzHNz8AYFVVVVXlvsBxHMdx3Gq/QI7jOI4jY79AjuM4jmNYv+A4juM4Dli/gOM4juP4ar/AcRzHcVxiv1hVVVVV1Vq/gBzHcRzHWb9AVVVVVVVWvyDHcRzH8VK/4DiO4ziOL78Ax3Ecx3Ewv8hxHMdxfHE/wHEcx3Ecaz+AHMdxHIdgPwDHcRzH8Vk/HMdxHMeRcj8AAAAAAMBsP8BxHMdxHGM/gOM4juM4YD+AHMdxHEdZP0CO4ziO41M/wHEcx3GcUT8Aq6qqqqpLP4DjOI7jOE8/ADmO4ziOOT+A4ziO4zhFPwAAAAAAAAA/AHIcx3EcI78AVVVVVVU7v4Cqqqqqqjy/ABzHcRzH8T4AchzHcRwvPwDHcRzHcSQ/AMdxHMdxRj+AHMdxHMclvwDkOI7jODC/AMdxHMdxHL9AVVVVVVUFPwCO4ziO4zo/gKqqqqoqWz9AjuM4juNSP0CO4ziO40Y/AAAAAAAARz9AVVVVVVVivyDHcRzHcVa/YFVVVVVVRb/AcRzHcZxVv3Acx3Ecx3a/cBzHcRxHcL/HcRzHcdxmvyDHcRzHsWW/HMdxHMcxYD9AVVVVVVVaPwA5juM4DlM/AI7jOI7jRz+qqqqqqupmPwAAAAAAgFc/gBzHcRzHSj8AOY7jOI5IP8BxHMdxHFo/AMdxHMdxSz8AwHEcx3H8vgAgx3Ecxxm/gFVVVVVVQT8AchzHcRw9P0BVVVVVVUS/AMhxHMdxDL+AqqqqqqpEPwBVVVVVVT8/ADmO4ziONT9AjuM4juM2PwCrqqqqqj4/AI7jOI7jJL8AAAAAAAAAvwAAAAAAAAAAAKqqqqqqIr/AcRzHcRwxv7CqqqqqqjI/AFVVVVVVM78ArKqqqqomPwAAAAAAACi/AAAAAAAAED8Ax3Ecx3EgP4DjOI7jOFo/4DiO4ziOWz/AcRzHcRxSPwA5juM4jkY/QFVVVVXVXD9QVVVVVVVYPwAAAAAAADw/ADmO4ziOVD9YVVVVVVVaPwAAAAAAAFo/AMdxHMdxUj8AAAAAAAA8PwAAAAAAQGY/IMdxHMfxWj/AcRzHcRxUPwAcx3Ecxz8/AMdxHMdxSD8AVVVVVVUzvwCQ4ziO4wg/AOA4juM4Dr8AOY7jOI5GP4CqqqqqqiY/AMdxHMdxFL8A5DiO4zgmvwByHMdxHEY/AJDjOI7j+L6A4ziO4zhBv6Cqqqqqqji/AAAAAAAAOj8Aqqqqqqo4PwA5juM4jke/AHIcx3EcL78AHMdxHMc1PwByHMdxHD+/AKyqqqqqKr8AchzHcRwXvwAcx3EcxzE/AMdxHMdxOr8Aq6qqqqo2v0BVVVVVVSm/AJDjOI7jIL8AchzHcRw/vwDHcRzHcTC/QFVVVVVVLb+AVVVVVVU1v4DjOI7jOCq/AHIcx3EcHz8AOY7jOI5AvwAAAAAAAEK/wHEcx3EcQb8AAAAAAABFv4Cqqqqqqji/AHIcx3EcVj8gx3Ecx3FWPwAAAAAAAEg/AFVVVVVVOT+AHMdxHMc3vwByHMdxHCu/AMdxHMdxIL8A5DiO4zguv+A4juM4zmm/kOM4juN4Zr84juM4juNiv6CqqqqqqmC/5DiO4zgOcr/gOI7jOE5svyDHcRzH8WW/AAAAAAAAYb8AAAAAAMBpv+A4juM4zmG/wHEcx3GcXb/AcRzHcZxav4DjOI7jOFi/wKqqqqqqVL9AjuM4juNRvwCrqqqqqkS/gBzHcRzHS78AchzHcRw3vwByHMdxHDW/QFVVVVVVN78AVVVVVVU9PwAAAAAAACS/gBzHcRzHMb8AAAAAAABFPwA4juM4jic/AAAAAAAAML8AchzHcRwnvwCO4ziO4yS/ABzHcRzHO7/AcRzHcRxIv+A4juM4jj+/AAAAAAAAOL+A4ziO47hgv0CO4ziOY2C/gBzHcRzHWL/gOI7jOI5JvwAAAAAAQGe/AAAAAAAAX78AAAAAAABavwAAAAAAgFO/QI7jOI7jbL+Q4ziO4/hivwAAAAAAQGC/AMdxHMfxV7+wqqqqqqpLv4DjOI7jOEe/gBzHcRzHRb+AHMdxHMdIvyDHcRzHcUe/AHIcx3EcL78AjuM4juMsvwAAAAAAACi/AAAAAAAAAL8ArKqqqqoSvwAAAAAAADA/ADiO4ziOK7+AHMdxHMdDPwAgx3Ecx/E+wKqqqqqqQD+AVVVVVVUhvwAAAAAAADA/AI7jOI7jNj8A5DiO4zgevwAAAAAAABi/AMhxHMdxLL8AAAAAAAAQPwCQ4ziO4/i+oKqqqqqqRz8AOY7jOI41PwA5juM4jic/AAAAAAAAMr8Ax3Ecx3EyvwCO4ziO41M/gKqqqqqqRT+AqqqqqqpIPyDHcRzHcUM/AFZVVVVVPb8A5DiO4zgmv4DjOI7jOC6/AKuqqqqqJr8AjuM4juM4v4DjOI7jODi/UFVVVVVVFb8AjuM4juNAvwAAAAAAgHC/4DiO4zgOZ79AjuM4juNjvwA5juM4Dle/HMdxHMexbb8gx3Ecx3Fhv2BVVVVV1WC/ADmO4zgOUL8gx3Ecx/FXv4Acx3Ecx1C/gFVVVVVVQb8Ax3Ecx3FKv4DjOI7jOEQ/AFVVVVVVOT+AHMdxHMdBP4DjOI7jODC/AI7jOI7jRD8AjuM4juM2vwBQVVVVVeW+gKqqqqqqKr8AjuM4juM2P8CqqqqqqkQ/AMBxHMdxzL4Ax3Ecx3E4P4CqqqqqqkE/QI7jOI7jST9AVVVVVVUFvwCrqqqqqjC/AKuqqqqqRT9AVVVVVdVXPwByHMdxHC8/IMdxHMdxRz/AqqqqqmpqP4Acx3EcR18/QI7jOI7jVz9AjuM4juNCP4DjOI7jOE8/gFVVVVVVIb8gx3Ecx3EMvwBwHMdxHB8/kOM4juM4Y7/gOI7jOI5mvwDHcRzHcVy/QI7jOI5jUL/HcRzHcdxhv8BxHMdxnFW/gOM4juM4Ub+AVVVVVVVNv0CO4ziO40S/AKuqqqqqNr+AHMdxHMdDvwAAAAAAABg/AAAAAAAANL8AOY7jOI4nPwAAAAAAABi/gKqqqqqqOD8AAAAAAAA0PwDHcRzHcUM/AFVVVVVVIT8AchzHcRwXPwBWVVVVVT8/AHIcx3EcOz+AqqqqqqpHPwAAAAAAACi/AKqqqqqqPj8AAAAAAAAkPwBWVVVVVTE/QI7jOI7jPj8A5DiO4zhQPwAAAAAAADo/AFZVVVVVNz8A5DiO4zguPwAAAAAAgFM/ABzHcRzHTj8AAAAAAAAoP8BxHMdxHEQ/AFVVVVVVTD/AcRzHcRxMP3Acx3Ecx0U/AMdxHMdxMD8Ax3Ecx3E6PwAAAAAAAAC/ADmO4ziOMz8AyHEcx3EoPwA5juM4jjG/gKqqqqqqKr8AAAAAAAAkPwDIcRzHcQy/AAAAAAAANj8AyHEcx3H8PgByHMdxHCM/AHIcx3EcNT8AqKqqqqoSPwDHcRzHcUY/ADiO4ziOAz8AHMdxHMcBPwDkOI7jOC6/ADmO4ziOQz8AjuM4juMwPwA5juM4jhM/AJDjOI7jED+AqqqqqqpEPwDAcRzHcfy+kOM4juM4ML8A5DiO4zg+PwA5juM4jkA/AAAAAAAAEL/Aqqqqqqo4vwDkOI7jOEA/AOQ4juM4Fj+AHMdxHMc1PwByHMdxHDu/AHIcx3EcMT8AchzHcRw7PwCQ4ziO4wg/AMhxHMdx7D4Ax3Ecx3EwP8BxHMdxHDc/AMdxHMdxLL8AUFVVVVX1vuA4juM4jkc/wHEcx3EcSz8AAAAAAAA6PwDHcRzHcTY/AMhxHMdx/L4AHMdxHMcZvwBQVVVVVfU+AOQ4juM4ND8AjuM4juNDv4DjOI7jOEO/AKqqqqqqIr8AcBzHcRwrvwA5juM4jiM/AHIcx3EcNb8AjuM4juM0vwBwHMdxHCu/gOM4juM4Kj8AVVVVVVUxPwCO4ziO4zQ/gFVVVVVVQ79gVVVVVZVgPwAAAAAAAFM/wHEcx3EcRz8AAAAAAABOP4DjOI7jOGM/gBzHcRwHYz8AOY7jOA5QP1BVVVVVVVQ/AAAAAACAXT8AOY7jOI5bPwByHMdxHEY/gBzHcRzHPT8AqKqqqqoaPwByHMdxHDe/QFVVVVVVQr8AAAAAAIBTvwBYVVVVVfU+ADmO4ziOI78AjuM4juMsPwDHcRzHcUS/kOM4juO4bj8AAAAAAEBiP+A4juM4Dls/AI7jOI7jSD/gOI7jOA5lPwDHcRzHcVo/AI7jOI7jRT8AHMdxHMc9P+Q4juM4jmI/AAAAAAAAVT/AcRzHcRxHPwBVVVVVVT8/4DiO4zgOYD/gOI7jOI5aPwA5juM4jks/gOM4juM4RT9AVVVVVRVuPwA5juM4Dlk/wKqqqqoqVj9AVVVVVVU7PwDkOI7jOEI/AOQ4juM4ML8AOY7jOI45P7Cqqqqqqjy/ADmO4zgObz9gVVVVVRVgP8BxHMdxHFM/gOM4juM4PD8AAAAAAIBgP3Acx3EcR14/wHEcx3EcUD8Ax3Ecx3E4P4DjOI7jOE6/AAAAAAAAR78AOY7jOI5Nv4Cqqqqqqkm/kOM4juN4bL8gx3EcxzFjvyDHcRzH8WG/AAAAAAAAXL+qqqqqqipyvyDHcRzHMW+/IMdxHMcxaL9gVVVVVVVovyDHcRzHsW6/4DiO4zjObb9AjuM4juNnv3Acx3EcR2O/ADiO4ziOSz8AVlVVVVU/vwAAAAAAABA/oKqqqqqqSL+AVVVVVdVkPwAAAAAAAFY/ADiO4ziOG78A5DiO4zguPwCoqqqqqiq/AMdxHMdxSb+AHMdxHEdTv5DjOI7jODS/AMhxHMdxNL8Aq6qqqqo2v0BVVVVVVVG/wHEcx3EcNb+AHMdxHMdVPwCrqqqqqke/AOQ4juM4Mr/gOI7jOI43vwAcx3Ecx0g/gFVVVVVVMb8AAAAAAAA4vwA4juM4jis/gBzHcRxHYD9YVVVVVVVQP8BxHMdxHEY/AAAAAAAAAAAAx3Ecx3E6vwByHMdxHDG/ADmO4ziOPb8AjuM4juMwvwAAAAAAADy/AHIcx3EcMb+AqqqqqqpHvwDHcRzHcUG/ADmO4ziOUj8AOY7jOI5VPwByHMdxHEc/AIDjOI7j+D6gqqqqqipWPwAAAAAAACA/QI7jOI7jQD9AjuM4juNBvwCrqqqqqkw/AAAAAAAAQL8Aq6qqqqo6P4CqqqqqqhI/gKqqqqqqTr+wqqqqqipYv5DjOI7jOEm/wKqqqqqqUL8AAAAAAABDPwBwHMdxHBc/gOM4juM4Tb8AAHIcx3HcPgCrqqqqqjg/AAAAAAAART8Aqqqqqqo0vwDIcRzHcSg/ADiO4ziOPb8A5DiO4zhHvwDHcRzHcUm/ADmO4ziOR78Ax3Ecx3EsP4Acx3Ecx0i/wHEcx3EcNb8AchzHcRxBvwBUVVVVVR0/gBzHcRzHV78AAAAAAABBvyDHcRzHcUi/gKqqqqqqQD/AcRzHcRxCv4Acx3Ecxzu/gOM4juM4Or8AHMdxHMclvwCO4ziO4yS/gFVVVVVVM78AAAAAAAA2v8Cqqqqqqke/gBzHcRzHNb+AHMdxHMcxvwBWVVVVVSm/AKqqqqqqCr9AVVVVVVUhv+A4juM4jiu/wHEcx3EcSL8AVFVVVVX1vgA5juM4ji+/AI7jOI7jJD8AjuM4juM+v4BVVVVVVU0/AHIcx3EcIz8AAAAAAAAsPwCoqqqqqhK/AMBxHMdx3D4AAAAAAABAvziO4ziO40m/AMdxHMdxML/AcRzHcRxRPwDHcRzHcUs/AMdxHMdxQr8Ax3Ecx3EUvwBUVVVVVRW/gBzHcRzHPT+A4ziO4zg6vwDHcRzHcSA/AMBxHMdx7L6A4ziO4zg6v4Acx3Ecx0K/AMdxHMdxRb9AVVVVVVVVPwDHcRzHcTC/AMdxHMdxJD8Ax3Ecx3E4vwByHMdxHEA/UFVVVVVVQr/AcRzHcRw3vwCO4ziO40O/AFZVVVVVKb+AHMdxHEdZv8CqqqqqqkW/oKqqqqqqSL8AchzHcZxVv0BVVVVVVUS/yHEcx3GcUL8A5DiO4zg+vwByHMdxHDu/AOQ4juM4PL8AOY7jOI5Jv4Acx3Ecxym/AAAAAAAAQ78AAAAAAAAYv0CO4ziO40C/AAAAAACAUL8AOY7jOI5HPwA4juM4jhO/AFZVVVVVHT9AVVVVVVVEvwByHMdxHB+/QI7jOI7jNL8AHMdxHMcpv4Cqqqqqqku/AAAAAACAVD8Ax3Ecx3EUv8BxHMdxHBe/ADmO4ziOK79AjuM4juNLP0CO4ziO40w/wKqqqqqqRb8AyHEcx3EcP8BxHMdxHFA/gBzHcRxHUj8AVlVVVVUpPwCsqqqqqi4/AAAAAAAAXT9AjuM4jmNUPxzHcRzH8VM/ABzHcRzHKT8AHMdxHMcxP0CO4ziOY1K/gBzHcRzHQ7+A4ziO4zhJvwByHMdxHDM/AAAAAAAAUr8Ax3Ecx3EovwDHcRzHcTy/gBzHcRzHQr+A4ziO4zhHvwBVVVVVVSG/AHAcx3EcB7+AHMdxHMdMv4BVVVVVVTW/AAAAAAAAQL8AjuM4juMIvwCrqqqqqjy/kOM4juM4NL/AqqqqqqomvwAAAAAAADI/QFVVVVVVUT8AOY7jOI5KPwA5juM4jkk/AHIcx3EcIz8AchzHcZxePwAAAAAAAEU/ADiO4ziONT9gVVVVVVVCvwByHMdxHEo/AMBxHMdx3L4AyHEcx3EcP2BVVVVVVUK/AJDjOI7jEL8AjuM4juMgvwAAAAAAAEK/gOM4juM4NL8A5DiO4zgOvwDkOI7jOBa/gBzHcRzHVr8Ax3Ecx3FAv4DjOI7jOCI/sKqqqqqqKj9VVVVVVVVBv4DjOI7jODq/QI7jOI7jMD8Ax3Ecx3FDvwAcx3EcxyG/ABzHcRzHN78AchzHcRxAvwCO4ziO40e/AFZVVVVVLb8Ax3Ecx3FEvwAAAAAAAFK/gBzHcRzHTL8AAAAAAAA8v4Cqqqqqqj6/AMdxHMdxXL8Ax3Ecx3FOv0CO4ziO406/gBzHcRzHR7+AHMdxHMdIvwDAcRzHccy+AAAAAAAAPr/Aqqqqqqo8vwCO4ziO40u/gBzHcRzHN7/AcRzHcRxEvwA5juM4jkO/AAAAAACAYT9wHMdxHMdXP0CO4ziO400/AKuqqqqqMj/gOI7jOI5Uv0CO4ziOY1S/AHIcx3EcK78AAAAAAIBSv4DjOI7juGG/wHEcx3GcXL9YVVVVVdVUv4DjOI7juFC/wKqqqqrqZ78AAAAAAIBXvwAAAAAAgFu/AAAAAAAAQb8AjuM4juNRvwByHMdxHC+/AOQ4juM4Dr8AVlVVVVUFPwA4juM4ji8/QFVVVVVVRj9AVVVVVVUxPwA4juM4jgO/ADmO4ziOPT8AOY7jOI4jvwDkOI7jOB6/AGBVVVVV5b6A4ziO4zgiv4Acx3EcxxG/HMdxHMdxQz8A5DiO4zgOP0CO4ziO4zA/AOQ4juM4Jr8AHMdxHMcZvwBWVVVVVSm/ABzHcRzHPb8AAAAAAABAvwBVVVVVVT2/AAAAAAAAML+wqqqqqqpVvwA5juM4ji+/wHEcx3EcRb8AjuM4juM8v4Cqqqqqqk+/gKqqqqqqNL+AHMdxHMdAvwA5juM4jjm/AKyqqqqqIr8AyHEcx3EgvwA5juM4ji8/QFVVVVVVN78AkOM4juMQPwCO4ziO4zC/AI7jOI7jIL+AHMdxHMc3v4Acx3EcB2C/cBzHcRzHWL/gOI7jOI5NvwCO4ziO40W/IMdxHMdxYL8AOY7jOI5Cv4Acx3Ecx1e/AAAAAAAAQL8AAAAAAIBnv3Acx3EcR12/cBzHcRyHYL+A4ziO4zhHv4Acx3EcR26/cBzHcRzHWL+gqqqqqqpYv4DjOI7jOFK/ADmO4zgOU79AVVVVVVVTvwA4juM4jhM/oKqqqqqqOL8AAAAAAAAAAAA5juM4jjG/AMBxHMdx3L4AOY7jOI4bv4CqqqqqqkA/gKqqqqqqOr8AjuM4juP4PgBVVVVVVSE/AKiqqqqqCj8AAAAAAAAYv8BxHMdxHD+/AAAAAAAALL8AUFVVVVX1vsCqqqqqKlI/AMdxHMdxQj+gqqqqqqpGPwAcx3Ecx0i/ABzHcRzHGT84juM4juMIvwCQ4ziO4xA/4DiO4ziOJz8AchzHcRwnPwDHcRzHcTY/AHAcx3EcH78AWFVVVVX1PgA5juM4jku/AHAcx3EcF78AkOM4juMkv4Acx3Ecx0u/AFVVVVVVT78A5DiO4zg+vwCrqqqqqkO/AKqqqqqqJr8AchzHcRxDvwCO4ziO40S/ADiO4ziOE78AjuM4juNGvwByHMdxHDm/AKuqqqqqT78AwHEcx3EMv4BVVVVVVTM/AAAAAAAAGL9AjuM4juM4vwByHMdxHCM/AOQ4juM4Kr8Ax3Ecx3FZv4BVVVVVVT+/sKqqqqqqQb8AAAAAAABNPwCrqqqqqjK/AFVVVVVVKb8AchzHcRwXP4CqqqqqqkA/gOM4juM4Qb8AOI7jOI4bv6Cqqqqqqka/QFVVVVVVSr/gOI7jOI5Gv4DjOI7jOEa/AMhxHMdxHL8AOY7jOI4zvwDIcRzHcey+IMdxHMdxQL8AcBzHcRwHv4DjOI7jOFK/QFVVVVVVQL8AjuM4juMgvwCqqqqqqia/gBzHcRzHSb8AOY7jOA5QvwAAAAAAAEO/AMdxHMfxUL+A4ziO4zguP6yqqqqqqki/IMdxHMdxMr9AjuM4juNDvwA5juM4jjE/gKqqqqqqS78AOY7jOI47v3Acx3Ecx0G/AHIcx3EcNb8AAAAAAAAYv0CO4ziO40e/gOM4juM4Kr8Aq6qqqqo6PwDIcRzHcRQ/YFVVVVVVSb+A4ziO4zg0vwDkOI7jODQ/AI7jOI7jMD8A5DiO4zgmvwByHMdxHB8/YFVVVVWVY78AAAAAAEBmv7CqqqqqKlW/QFVVVVVVWr8ArKqqqqoSvwA5juM4jki/AKqqqqqqGj8AOY7jOI4xvwDHcRzH8VE/QFVVVVVVRD8AjuM4juMIPw==","dtype":"float64","shape":[5000]}},"selected":{"id":"1156","type":"Selection"},"selection_policy":{"id":"1157","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"c9c04eb7-454f-449e-95aa-8fb8b930e2a5","roots":{"1103":"b377fa74-3ce1-437e-8cd6-6609fae7e63a"}}];
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

