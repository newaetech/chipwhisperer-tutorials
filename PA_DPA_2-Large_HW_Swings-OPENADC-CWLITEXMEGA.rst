
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
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
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
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEXMEGA.sym
    avr-nm -n simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.sym
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

    

    <div id="39a5fff6-d0ed-4f6e-8db5-0c7d4bcb0ab5"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#39a5fff6-d0ed-4f6e-8db5-0c7d4bcb0ab5');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="b7b8d1bf-588e-411f-9dec-f44794781e80"></div>
    
    </div>



.. raw:: html

    

    <div id="afbb98d3-7298-4e50-8965-318dcc47b71f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#afbb98d3-7298-4e50-8965-318dcc47b71f');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"f5fd29e1-af00-4772-beaf-276835f8155e":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1041","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{"formatter":{"id":"1045","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1049","type":"Selection"},"selection_policy":{"id":"1048","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1041","type":"Title"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1045","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1048","type":"UnionRenderers"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1049","type":"Selection"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1007","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"f5fd29e1-af00-4772-beaf-276835f8155e","roots":{"1002":"b7b8d1bf-588e-411f-9dec-f44794781e80"}}];
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

    

    <div id="b50f82aa-bf70-43da-a859-50e228288cb8"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b50f82aa-bf70-43da-a859-50e228288cb8');
        
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

    Number of 0xFF: 28
    Number of 0x00: 22
    



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
        
    
    
    
    
    
      <div class="bk-root" id="96b3550b-8c6e-4ef4-b7ad-9a2b50abd927"></div>
    
    </div>



.. raw:: html

    

    <div id="5f4b95aa-73b2-4eb5-b633-3b0d26dc6e39"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#5f4b95aa-73b2-4eb5-b633-3b0d26dc6e39');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"43c05d25-69d7-42cb-a98d-a5e60e677f9a":{"roots":{"references":[{"attributes":{"below":[{"id":"1114","type":"LinearAxis"}],"left":[{"id":"1119","type":"LinearAxis"}],"renderers":[{"id":"1114","type":"LinearAxis"},{"id":"1118","type":"Grid"},{"id":"1119","type":"LinearAxis"},{"id":"1123","type":"Grid"},{"id":"1132","type":"BoxAnnotation"},{"id":"1142","type":"GlyphRenderer"}],"title":{"id":"1153","type":"Title"},"toolbar":{"id":"1130","type":"Toolbar"},"x_range":{"id":"1106","type":"DataRange1d"},"x_scale":{"id":"1110","type":"LinearScale"},"y_range":{"id":"1108","type":"DataRange1d"},"y_scale":{"id":"1112","type":"LinearScale"}},"id":"1105","subtype":"Figure","type":"Plot"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1140","type":"Line"},{"attributes":{},"id":"1120","type":"BasicTicker"},{"attributes":{"dimension":1,"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1123","type":"Grid"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1161","type":"Selection"},"selection_policy":{"id":"1160","type":"UnionRenderers"}},"id":"1139","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1139","type":"ColumnDataSource"}},"id":"1143","type":"CDSView"},{"attributes":{},"id":"1161","type":"Selection"},{"attributes":{},"id":"1124","type":"PanTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{},"id":"1160","type":"UnionRenderers"},{"attributes":{},"id":"1125","type":"WheelZoomTool"},{"attributes":{},"id":"1112","type":"LinearScale"},{"attributes":{"overlay":{"id":"1132","type":"BoxAnnotation"}},"id":"1126","type":"BoxZoomTool"},{"attributes":{"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1118","type":"Grid"},{"attributes":{},"id":"1155","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1108","type":"DataRange1d"},{"attributes":{},"id":"1127","type":"SaveTool"},{"attributes":{"formatter":{"id":"1157","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1114","type":"LinearAxis"},{"attributes":{},"id":"1157","type":"BasicTickFormatter"},{"attributes":{},"id":"1128","type":"ResetTool"},{"attributes":{},"id":"1129","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1124","type":"PanTool"},{"id":"1125","type":"WheelZoomTool"},{"id":"1126","type":"BoxZoomTool"},{"id":"1127","type":"SaveTool"},{"id":"1128","type":"ResetTool"},{"id":"1129","type":"HelpTool"}]},"id":"1130","type":"Toolbar"},{"attributes":{},"id":"1115","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1153","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1132","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"formatter":{"id":"1155","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1119","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1139","type":"ColumnDataSource"},"glyph":{"id":"1140","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1141","type":"Line"},"selection_glyph":null,"view":{"id":"1143","type":"CDSView"}},"id":"1142","type":"GlyphRenderer"}],"root_ids":["1105"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"43c05d25-69d7-42cb-a98d-a5e60e677f9a","roots":{"1105":"96b3550b-8c6e-4ef4-b7ad-9a2b50abd927"}}];
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

