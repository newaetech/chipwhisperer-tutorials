
Large HW Swings
===============


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'
    CRYPTO_TARGET = 'AVRCRYPTOLIB'
    num_traces = 50


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**


.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEXMEGA.hex
    rm -f -- simpleserial-aes-CWLITEXMEGA.eep
    rm -f -- simpleserial-aes-CWLITEXMEGA.cof
    rm -f -- simpleserial-aes-CWLITEXMEGA.elf
    rm -f -- simpleserial-aes-CWLITEXMEGA.map
    rm -f -- simpleserial-aes-CWLITEXMEGA.sym
    rm -f -- simpleserial-aes-CWLITEXMEGA.lss
    rm -f -- objdir/*.o
    rm -f -- objdir/*.lst
    rm -f -- simpleserial-aes.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes-independant.s aes_enc.s aes_keyschedule.s aes_sbox.s aes128_enc.s
    rm -f -- simpleserial-aes.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes-independant.d aes_enc.d aes_keyschedule.d aes_sbox.d aes128_enc.d
    rm -f -- simpleserial-aes.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes-independant.i aes_enc.i aes_keyschedule.i aes_sbox.i aes128_enc.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_enc.o.d .././crypto/avrcryptolib//aes/aes_enc.c -o objdir/aes_enc.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_keyschedule.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_keyschedule.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_keyschedule.o.d .././crypto/avrcryptolib//aes/aes_keyschedule.c -o objdir/aes_keyschedule.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_sbox.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_sbox.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_sbox.o.d .././crypto/avrcryptolib//aes/aes_sbox.c -o objdir/aes_sbox.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes128_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes128_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes128_enc.o.d .././crypto/avrcryptolib//aes/aes128_enc.c -o objdir/aes128_enc.o 
    .
    Assembling: .././crypto/avrcryptolib//gf256mul/gf256mul.S
    avr-gcc -c -mmcu=atxmega128d3 -I. -x assembler-with-cpp -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/gf256mul.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul .././crypto/avrcryptolib//gf256mul/gf256mul.S -o objdir/gf256mul.o
    .
    Linking: simpleserial-aes-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEXMEGA.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes-independant.o objdir/aes_enc.o objdir/aes_keyschedule.o objdir/aes_sbox.o objdir/aes128_enc.o objdir/gf256mul.o --output simpleserial-aes-CWLITEXMEGA.elf -Wl,-Map=simpleserial-aes-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom=&#34;alloc,load&#34; \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-aes-CWLITEXMEGA.elf &gt; simpleserial-aes-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEXMEGA.sym
    avr-nm -n simpleserial-aes-CWLITEXMEGA.elf &gt; simpleserial-aes-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       3234	     32	    228	   3494	    da6	simpleserial-aes-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    


Data Tracing Theory
-------------------

In the last tutorial, we saw how the power measurement of a device is
related to the Hamming weight. Let’s use this to see where some
arbitrary data is processed by a device. We’ll later expand on this to
perform a test that also takes into account noise.

Our objective is simple - we’ll send in some data with all 1’s in one
location, and then some data with all 0’s.

Capturing Power Traces
----------------------

Capturing power traces will be very similar to previous tutorials,
except this time we’ll be using a loop to capture multiple traces, as
well as numpy to store them.

Setup
~~~~~

We’ll use some helper scripts to make setup and programming easier. If
you’re using an XMEGA or STM (CWLITEARM) target, binaries with the
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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 3265 bytes
    


Capturing Traces
~~~~~~~~~~~~~~~~

Below you can see the capture loop. The main body of the loop loads some
new plaintext, arms the scope, sends the key and plaintext, then finally
records and appends our new trace to the ``traces[]`` list. At the end,
we convert the trace data to numpy arrays, since that’s what we’ll be
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

    

    <div id="ad4d9cbf-c8f7-446e-afe6-2d7e68093296"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ad4d9cbf-c8f7-446e-afe6-2d7e68093296');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="8834b175-e61a-4fee-9d26-8e26c7dc5c25"></div>
    
    </div>



.. raw:: html

    

    <div id="6bc4eb60-1647-4c45-b5de-21882c19964d"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#6bc4eb60-1647-4c45-b5de-21882c19964d');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"35af5806-4d0f-4b3a-8f8b-1c55b0f4fd7b":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1042","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1042","type":"Title"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1046","type":"BasicTickFormatter"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1048","type":"Selection"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1049","type":"UnionRenderers"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1048","type":"Selection"},"selection_policy":{"id":"1049","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"formatter":{"id":"1046","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"35af5806-4d0f-4b3a-8f8b-1c55b0f4fd7b","roots":{"1002":"8834b175-e61a-4fee-9d26-8e26c7dc5c25"}}];
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

Now that we have some traces, let’s look at what we’ve actually
recorded. We’ll be doing the following tasks:

1. Seperate traces into two groups: 0x00, and 0xFF
2. Make an average of each group.
3. Subtract the two averages and see the difference.

This will be shown in the following two cells. Note the number of 0xFF
and 0x00 isn’t exactly 50/50. That is why we need to ensure we average
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
            <span id="1104">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="3fae1ac1-803e-4ed5-b3c9-0e3aea428bdd"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3fae1ac1-803e-4ed5-b3c9-0e3aea428bdd');
        
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


.. parsed-literal::

    Number of 0xFF: 27
    Number of 0x00: 23
    



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
        
    
    
    
    
    
      <div class="bk-root" id="07847096-d1ae-4e0d-a0bd-63ca52d037e5"></div>
    
    </div>



.. raw:: html

    

    <div id="b87189be-d858-4d8f-9ba7-33696ebcfea7"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b87189be-d858-4d8f-9ba7-33696ebcfea7');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"18bc7cef-e958-44c7-8690-31cab0e6bc0f":{"roots":{"references":[{"attributes":{"below":[{"id":"1114","type":"LinearAxis"}],"left":[{"id":"1119","type":"LinearAxis"}],"renderers":[{"id":"1114","type":"LinearAxis"},{"id":"1118","type":"Grid"},{"id":"1119","type":"LinearAxis"},{"id":"1123","type":"Grid"},{"id":"1132","type":"BoxAnnotation"},{"id":"1142","type":"GlyphRenderer"}],"title":{"id":"1154","type":"Title"},"toolbar":{"id":"1130","type":"Toolbar"},"x_range":{"id":"1106","type":"DataRange1d"},"x_scale":{"id":"1110","type":"LinearScale"},"y_range":{"id":"1108","type":"DataRange1d"},"y_scale":{"id":"1112","type":"LinearScale"}},"id":"1105","subtype":"Figure","type":"Plot"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1140","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1160","type":"Selection"},"selection_policy":{"id":"1161","type":"UnionRenderers"}},"id":"1139","type":"ColumnDataSource"},{"attributes":{"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1118","type":"Grid"},{"attributes":{"formatter":{"id":"1156","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1119","type":"LinearAxis"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1139","type":"ColumnDataSource"},"glyph":{"id":"1140","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1141","type":"Line"},"selection_glyph":null,"view":{"id":"1143","type":"CDSView"}},"id":"1142","type":"GlyphRenderer"},{"attributes":{},"id":"1120","type":"BasicTicker"},{"attributes":{},"id":"1160","type":"Selection"},{"attributes":{"source":{"id":"1139","type":"ColumnDataSource"}},"id":"1143","type":"CDSView"},{"attributes":{"dimension":1,"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1123","type":"Grid"},{"attributes":{"plot":null,"text":""},"id":"1154","type":"Title"},{"attributes":{},"id":"1161","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1114","type":"LinearAxis"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1132","type":"BoxAnnotation"},{"attributes":{},"id":"1124","type":"PanTool"},{"attributes":{},"id":"1125","type":"WheelZoomTool"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"overlay":{"id":"1132","type":"BoxAnnotation"}},"id":"1126","type":"BoxZoomTool"},{"attributes":{},"id":"1127","type":"SaveTool"},{"attributes":{"callback":null},"id":"1108","type":"DataRange1d"},{"attributes":{},"id":"1128","type":"ResetTool"},{"attributes":{},"id":"1115","type":"BasicTicker"},{"attributes":{},"id":"1129","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1124","type":"PanTool"},{"id":"1125","type":"WheelZoomTool"},{"id":"1126","type":"BoxZoomTool"},{"id":"1127","type":"SaveTool"},{"id":"1128","type":"ResetTool"},{"id":"1129","type":"HelpTool"}]},"id":"1130","type":"Toolbar"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{},"id":"1156","type":"BasicTickFormatter"},{"attributes":{},"id":"1112","type":"LinearScale"},{"attributes":{},"id":"1110","type":"LinearScale"}],"root_ids":["1105"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"18bc7cef-e958-44c7-8690-31cab0e6bc0f","roots":{"1105":"07847096-d1ae-4e0d-a0bd-63ca52d037e5"}}];
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

