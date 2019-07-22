
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
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
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
       3440	     32	    228	   3700	    e74	simpleserial-aes-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 3471 bytes
    


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

    

    <div id="86021fa3-8034-4bd5-9116-37d44ef326cd"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#86021fa3-8034-4bd5-9116-37d44ef326cd');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="cb41a438-710e-4a12-8442-280d2bd4442c" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="25844d1a-8457-4c04-bbd6-d8e0e8ea90c4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#25844d1a-8457-4c04-bbd6-d8e0e8ea90c4');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"e2ee42af-5ba5-4062-a133-3ad1aa4dacf8":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"AAAAAABAtz8AAAAAAJDSvwAAAAAAoMC/AAAAAACAwr8AAAAAAACEPwAAAAAAANy/AAAAAACgz78AAAAAACDNvwAAAAAAgKm/AAAAAACAy78AAAAAAICrvwAAAAAAALO/AAAAAACAsT8AAAAAALDcvwAAAAAAoNC/AAAAAACgzL8AAAAAAIClvwAAAAAA8NG/AAAAAAAAvL8AAAAAAAC9vwAAAAAAAKY/AAAAAABAw78AAAAAAAB4vwAAAAAAAKK/AAAAAAAAuT8AAAAAAMDSvwAAAAAAgMC/AAAAAADgwL8AAAAAAACbPwAAAAAAoMC/AAAAAAAAlD8AAAAAAACYvwAAAAAAALw/AAAAAABgwL8AAAAAAACOPwAAAAAAAJO/AAAAAAAAvT8AAAAAAKDYvwAAAAAAAMu/AAAAAABAyb8AAAAAAACdvwAAAAAAANO/AAAAAAAgwL8AAAAAAODAvwAAAAAAAJs/AAAAAAAgyL8AAAAAAICnvwAAAAAAALK/AAAAAAAAsz8AAAAAANDUvwAAAAAA4MO/AAAAAADAw78AAAAAAACGPwAAAAAAIMi/AAAAAAAAnL8AAAAAAACqvwAAAAAAQLc/AAAAAACQ3L8AAAAAAMDQvwAAAAAAoM2/AAAAAAAArr8AAAAAAIDQvwAAAAAAgLW/AAAAAACAtr8AAAAAAECxPwAAAAAA4MG/AAAAAAAAgD8AAAAAAACVvwAAAAAAgL4/AAAAAACw2r8AAAAAACDOvwAAAAAAgMq/AAAAAAAAmb8AAAAAAEDNvwAAAAAAALG/AAAAAABAs78AAAAAAAC0PwAAAAAAQL+/AAAAAAAAlz8AAAAAAAB4vwAAAAAA4MA/AAAAAAAAv78AAAAAAACZPwAAAAAAAFC/AAAAAACAwT8AAAAAAEC2vwAAAAAAAKg/AAAAAAAAfD8AAAAAAADCPwAAAAAAwL+/AAAAAAAAlj8AAAAAAACCvwAAAAAAgMA/AAAAAAAAur8AAAAAAACkPwAAAAAAAHw/AAAAAABAwT8AAAAAAKDEvwAAAAAAAIK/AAAAAAAAnb8AAAAAAMC9PwAAAAAAgMC/AAAAAAAAkD8AAAAAAACUvwAAAAAAgL8/AAAAAAAgw78AAAAAAABoPwAAAAAAAJW/AAAAAADAvj8AAAAAAAC8vwAAAAAAAJ4/AAAAAAAAYD8AAAAAAMDBPwAAAAAAwMW/AAAAAAAAjr8AAAAAAACfvwAAAAAAwL0/AAAAAAAAxL8AAAAAAACCvwAAAAAAAKK/AAAAAAAAuz8AAAAAAODHvwAAAAAAAJ6/AAAAAACAqL8AAAAAAMC3PwAAAAAAAMK/AAAAAAAAhD8AAAAAAACIvwAAAAAA4MA/AAAAAACAvL8AAAAAAACgPwAAAAAAAFA/AAAAAADgwD8AAAAAAEC8vwAAAAAAgKA/AAAAAAAAcD8AAAAAAKDCPwAAAAAAAOC/AAAAAACw378AAAAAAFDavwAAAAAAgMa/AAAAAAAA4L8AAAAAAADfvwAAAAAAgNm/AAAAAABAxr8AAAAAAADgvwAAAAAAAOC/AAAAAABQ3r8AAAAAACDNvwAAAAAAENK/AAAAAACAs78AAAAAAICvvwAAAAAAALk/AAAAAAAA4L8AAAAAAEDfvwAAAAAA4Ni/AAAAAABAw78AAAAAABDQvwAAAAAAgKi/AAAAAAAAob8AAAAAAAC+PwAAAAAAsN2/AAAAAAAw078AAAAAAGDQvwAAAAAAQLC/AAAAAADAwr8AAAAAAACQPwAAAAAAAIK/AAAAAAAgwD8AAAAAAADgvwAAAAAAsNu/AAAAAADA1r8AAAAAAIDBvwAAAAAAIM+/AAAAAAAArL8AAAAAAECwvwAAAAAAwLc/AAAAAACAwr8AAAAAAACCPwAAAAAAAJK/AAAAAADAvj8AAAAAAICxvwAAAAAAwLI/AAAAAAAAoz8AAAAAAGDFPwAAAAAAAJU/AAAAAACAwj8AAAAAAEC1PwAAAAAAgMk/AAAAAACAoL8AAAAAAMC5PwAAAAAAgKs/AAAAAACgxj8AAAAAAACrPwAAAAAAoMY/AAAAAACAvj8AAAAAAODMPwAAAAAAAJi/AAAAAAAAvD8AAAAAAMCxPwAAAAAAAMk/AAAAAAAAhj8AAAAAAODCPwAAAAAAgLk/AAAAAACAzD8AAAAAAICoPwAAAAAAAMU/AAAAAACAuj8AAAAAAODLPwAAAAAAINK/AAAAAABgwr8AAAAAAKDBvwAAAAAAAJo/AAAAAACgyb8AAAAAAICqvwAAAAAAgK6/AAAAAADAtT8AAAAAAFDRvwAAAAAAwLe/AAAAAAAAt78AAAAAAMCxPwAAAAAAwMS/AAAAAAAAcL8AAAAAAACOvwAAAAAAYMA/AAAAAACAz78AAAAAAMCyvwAAAAAAwLG/AAAAAAAAtj8AAAAAAFDTvwAAAAAAQL2/AAAAAADAt78AAAAAAICxPwAAAAAAUNS/AAAAAACgwL8AAAAAAAC8vwAAAAAAALA/AAAAAACAsL8AAAAAAICzPwAAAAAAAJs/AAAAAADAwz8AAAAAAICmvwAAAAAAwLc/AAAAAAAAqD8AAAAAAADGPwAAAAAAAHw/AAAAAACAwT8AAAAAAMC4PwAAAAAAAMw/AAAAAAAAlL8AAAAAAEC7PwAAAAAAAKs/AAAAAABAxj8AAAAAAACKPwAAAAAAIMI/AAAAAACAtz8AAAAAAKDKPwAAAAAAAJG/AAAAAAAAvj8AAAAAAACzPwAAAAAAAMk/AAAAAAAAgD8AAAAAAGDCPwAAAAAAwLk/AAAAAACgzD8AAAAAAACcPwAAAAAA4MI/AAAAAACAtj8AAAAAAIDJPwAAAAAAgNG/AAAAAACAvr8AAAAAAAC/vwAAAAAAAKM/AAAAAAAAyL8AAAAAAAClvwAAAAAAgK6/AAAAAABAtT8AAAAAAIDPvwAAAAAAALK/AAAAAABAs78AAAAAAAC0PwAAAAAA4Mu/AAAAAAAArr8AAAAAAACuvwAAAAAAgLc/AAAAAABQ1b8AAAAAAKDCvwAAAAAAQMC/AAAAAAAApT8AAAAAACDWvwAAAAAA4MK/AAAAAABAv78AAAAAAICqPwAAAAAAQNS/AAAAAACAwL8AAAAAAIC8vwAAAAAAAKs/AAAAAAAAs78AAAAAAECxPwAAAAAAAJk/AAAAAABgwz8AAAAAAACpvwAAAAAAgLY/AAAAAACApD8AAAAAAODEPwAAAAAAAGA/AAAAAADAwT8AAAAAAIC4PwAAAAAAIMw/AAAAAAAAlb8AAAAAAMC6PwAAAAAAAKg/AAAAAABgxT8AAAAAAACIPwAAAAAAAMI/AAAAAADAtj8AAAAAAIDKPwAAAAAAAJy/AAAAAAAAuj8AAAAAAICuPwAAAAAAoMc/AAAAAAAAYD8AAAAAAODBPwAAAAAAgLg/AAAAAADgyz8AAAAAAACZPwAAAAAAYMI/AAAAAABAtT8AAAAAAGDJPwAAAAAAIM2/AAAAAAAAtb8AAAAAAEC4vwAAAAAAgKk/AAAAAAAgyb8AAAAAAACsvwAAAAAAwLC/AAAAAAAAtD8AAAAAAFDRvwAAAAAAQLe/AAAAAAAAub8AAAAAAACvPwAAAAAAAM2/AAAAAACAr78AAAAAAICvvwAAAAAAwLY/AAAAAAAA1b8AAAAAAKDCvwAAAAAAAMG/AAAAAAAAoj8AAAAAAHDTvwAAAAAAQL2/AAAAAADAuL8AAAAAAECxPwAAAAAAENO/AAAAAABAv78AAAAAAAC7vwAAAAAAQLA/AAAAAAAAsb8AAAAAAMCyPwAAAAAAAJs/AAAAAABgwz8AAAAAAACtvwAAAAAAQLQ/AAAAAAAAoj8AAAAAAGDEPwAAAAAAAIC/AAAAAABgwD8AAAAAAIC2PwAAAAAAQMo/AAAAAAAAnb8AAAAAAAC5PwAAAAAAAKc/AAAAAAAAxT8AAAAAAACQPwAAAAAAQMI/AAAAAACAtT8AAAAAAMDJPwAAAAAAAKi/AAAAAAAAtz8AAAAAAACqPwAAAAAAoMY/AAAAAAAAl78AAAAAAMC8PwAAAAAAQLM/AAAAAACgyT8AAAAAAABQvwAAAAAAAMA/AAAAAAAAsT8AAAAAAGDHPwAAAAAAcNC/AAAAAABAvL8AAAAAAIC9vwAAAAAAAKM/AAAAAAAgxb8AAAAAAACYvwAAAAAAAKa/AAAAAADAtz8AAAAAADDRvwAAAAAAQLe/AAAAAADAt78AAAAAAACwPwAAAAAAQMS/AAAAAAAAdL8AAAAAAACTvwAAAAAAwL0/AAAAAAAg0L8AAAAAAIC0vwAAAAAAALW/AAAAAAAAsz8AAAAAAFDRvwAAAAAAwLa/AAAAAACAtb8AAAAAAMCzPwAAAAAAwNK/AAAAAADAvL8AAAAAAAC6vwAAAAAAgLA/AAAAAACAsL8AAAAAAACxPwAAAAAAAJQ/AAAAAACAwj8AAAAAAICqvwAAAAAAwLQ/AAAAAACAoT8AAAAAACDEPwAAAAAAAJS/AAAAAADAvj8AAAAAAAC0PwAAAAAAwMk/AAAAAAAApr8AAAAAAAC1PwAAAAAAAKA/AAAAAADAwj8AAAAAAABgvwAAAAAAIMA/AAAAAAAAsz8AAAAAAGDIPwAAAAAAgKS/AAAAAAAAuD8AAAAAAICpPwAAAAAA4MU/AAAAAAAAlr8AAAAAAMC9PwAAAAAAQLM/AAAAAABgyT8AAAAAAACAPwAAAAAAQMA/AAAAAABAsD8AAAAAAADHPwAAAAAAoM+/AAAAAACAur8AAAAAAEC9vwAAAAAAAKQ/AAAAAACgx78AAAAAAICpvwAAAAAAgLC/AAAAAADAsz8AAAAAADDRvwAAAAAAwLe/AAAAAACAuL8AAAAAAICtPwAAAAAAAMq/AAAAAACApr8AAAAAAACqvwAAAAAAALg/AAAAAAAA0r8AAAAAAEC7vwAAAAAAQLq/AAAAAACAqT8AAAAAAIDTvwAAAAAAwL6/AAAAAACAur8AAAAAAICvPwAAAAAAINa/AAAAAACgxL8AAAAAAMDCvwAAAAAAAJw/AAAAAAAAvb8AAAAAAAChPwAAAAAAAHy/AAAAAACAvz8AAAAAAICzvwAAAAAAgK0/AAAAAAAAij8AAAAAACDCPwAAAAAAAJe/AAAAAABAvj8AAAAAAECzPwAAAAAAYMk/AAAAAAAApb8AAAAAAIC0PwAAAAAAAJ0/AAAAAAAgwz8AAAAAAABgvwAAAAAAAMA/AAAAAAAAsz8AAAAAAIDIPwAAAAAAAKq/AAAAAACAtT8AAAAAAACnPwAAAAAAoMU/AAAAAAAAlb8AAAAAAMC9PwAAAAAAgLM/AAAAAACAyD8AAAAAAACMvwAAAAAAwLs/AAAAAAAArD8AAAAAAADGPwAAAAAAENK/AAAAAAAAwb8AAAAAAADCvwAAAAAAAJI/AAAAAABAyr8AAAAAAICvvwAAAAAAALO/AAAAAAAAsT8AAAAAAGDSvwAAAAAAAL6/AAAAAABAvb8AAAAAAICnPwAAAAAAgMq/AAAAAAAAqL8AAAAAAICrvwAAAAAAwLc/AAAAAACQ1L8AAAAAAODBvwAAAAAAoMC/AAAAAAAAoj8AAAAAAJDUvwAAAAAA4MC/AAAAAABAvb8AAAAAAICqPwAAAAAAYNW/AAAAAADgwr8AAAAAAKDAvwAAAAAAgKU/AAAAAABAub8AAAAAAACmPwAAAAAAAFA/AAAAAACAvz8AAAAAAEC1vwAAAAAAgKw/AAAAAAAAjD8AAAAAAADCPwAAAAAAAJa/AAAAAACAvj8AAAAAAMCxPwAAAAAA4Mg/AAAAAAAApb8AAAAAAEC1PwAAAAAAAJ8/AAAAAACAwz8AAAAAAABQvwAAAAAAgL8/AAAAAADAsT8AAAAAAADIPwAAAAAAAKO/AAAAAABAuD8AAAAAAICqPwAAAAAAQMY/AAAAAAAAmr8AAAAAAMC8PwAAAAAAgLI/AAAAAADgyD8AAAAAAABQPwAAAAAAAL8/AAAAAAAAsD8AAAAAACDGPwAAAAAAgNK/AAAAAADAwb8AAAAAACDCvwAAAAAAAI4/AAAAAABgz78AAAAAAAC5vwAAAAAAwLq/AAAAAAAApT8AAAAAAGDUvwAAAAAAAMG/AAAAAABAwL8AAAAAAAChPwAAAAAAAM+/AAAAAACAs78AAAAAAMC0vwAAAAAAALI/AAAAAACA1b8AAAAAAGDDvwAAAAAAoMG/AAAAAAAAnz8AAAAAAHDUvwAAAAAAYMG/AAAAAACAvb8AAAAAAACqPwAAAAAAYNW/AAAAAADAwr8AAAAAAIDAvwAAAAAAgKU/AAAAAACAuL8AAAAAAACoPwAAAAAAAHA/AAAAAADAwD8AAAAAAIC1vwAAAAAAgKs/AAAAAAAAiD8AAAAAAODAPwAAAAAAAKa/AAAAAACAuT8AAAAAAICvPwAAAAAAwMc/AAAAAACAp78AAAAAAAC1PwAAAAAAAJc/AAAAAADAwj8AAAAAAAAAAAAAAAAAQMA/AAAAAABAsz8AAAAAAGDIPwAAAAAAAKq/AAAAAABAtT8AAAAAAICjPwAAAAAAIMU/AAAAAAAAo78AAAAAAIC6PwAAAAAAQLA/AAAAAAAAyD8AAAAAAACRvwAAAAAAQLo/AAAAAAAAqD8AAAAAAGDFPwAAAAAAcNK/AAAAAACgwb8AAAAAACDCvwAAAAAAAJE/AAAAAADgzb8AAAAAAAC1vwAAAAAAgLe/AAAAAAAArD8AAAAAAADSvwAAAAAAQLq/AAAAAADAur8AAAAAAACoPwAAAAAAIMe/AAAAAAAAmb8AAAAAAACivwAAAAAAwLo/AAAAAAAQ078AAAAAAMC+vwAAAAAAwL6/AAAAAACApT8AAAAAACDVvwAAAAAAAMK/AAAAAAAAv78AAAAAAACoPwAAAAAA8NW/AAAAAACAxL8AAAAAAADCvwAAAAAAAKE/AAAAAADAub8AAAAAAICmPwAAAAAAAFA/AAAAAABgwD8AAAAAAMCxvwAAAAAAQLA/AAAAAAAAlD8AAAAAAKDCPwAAAAAAAKK/AAAAAABAuz8AAAAAAECwPwAAAAAAIMg/AAAAAACAsL8AAAAAAMCwPwAAAAAAAJI/AAAAAADgwT8AAAAAAACAvwAAAAAAgL4/AAAAAACAsT8AAAAAAODGPwAAAAAAAKi/AAAAAACAtj8AAAAAAICnPwAAAAAAwMU/AAAAAAAAlr8AAAAAAAC+PwAAAAAAwLE/AAAAAACgyD8AAAAAAABQPwAAAAAAAL8/AAAAAABAsD8AAAAAAMDGPwAAAAAAYNK/AAAAAAAAw78AAAAAAADDvwAAAAAAAIY/AAAAAABgzL8AAAAAAEC0vwAAAAAAwLa/AAAAAAAArD8AAAAAABDUvwAAAAAAYMC/AAAAAADAv78AAAAAAICiPwAAAAAAAM+/AAAAAAAAtL8AAAAAAMCzvwAAAAAAwLE/AAAAAAAQ1L8AAAAAAEDBvwAAAAAAwL+/AAAAAAAApD8AAAAAANDUvwAAAAAAoMG/AAAAAADAvr8AAAAAAACmPwAAAAAAgNW/AAAAAABgw78AAAAAACDBvwAAAAAAgKM/AAAAAADAuL8AAAAAAICnPwAAAAAAAGi/AAAAAADAvz8AAAAAAECzvwAAAAAAAK8/AAAAAAAAkD8AAAAAAADCPwAAAAAAAJe/AAAAAAAAvD8AAAAAAICxPwAAAAAAYMg/AAAAAABAsb8AAAAAAECwPwAAAAAAAI4/AAAAAACAwT8AAAAAAIChvwAAAAAAQLk/AAAAAACAqj8AAAAAAEDGPwAAAAAAAK2/AAAAAADAtD8AAAAAAACkPwAAAAAAYMQ/AAAAAAAAmb8AAAAAAEC9PwAAAAAAwLI/AAAAAAAAyT8AAAAAAACIvwAAAAAAALw/AAAAAAAAqT8AAAAAAEDFPwAAAAAAgNK/AAAAAADgwb8AAAAAACDCvwAAAAAAAJA/AAAAAAAgyr8AAAAAAICuvwAAAAAAwLO/AAAAAADAsD8AAAAAALDRvwAAAAAAgLm/AAAAAAAAur8AAAAAAICqPwAAAAAAIMq/AAAAAAAAqr8AAAAAAICtvwAAAAAAQLY/AAAAAACg1r8AAAAAAGDFvwAAAAAAAMO/AAAAAAAAlD8AAAAAAHDWvwAAAAAAwMO/AAAAAADAwL8AAAAAAACkPwAAAAAAENS/AAAAAADAwL8AAAAAAIC9vwAAAAAAAKc/AAAAAAAAuL8AAAAAAICoPwAAAAAAAHA/AAAAAADgwD8AAAAAAECyvwAAAAAAgLA/AAAAAAAAjD8AAAAAACDCPwAAAAAAAJW/AAAAAAAAvj8AAAAAAACzPwAAAAAAAMk/AAAAAACApb8AAAAAAEC0PwAAAAAAAJs/AAAAAADAwj8AAAAAAACKvwAAAAAAAL4/AAAAAACAsD8AAAAAAGDHPwAAAAAAAK6/AAAAAADAsj8AAAAAAIChPwAAAAAAgMQ/AAAAAAAAm78AAAAAAEC9PwAAAAAAQLI/AAAAAADAyD8AAAAAAAB4vwAAAAAAAL0/AAAAAACArT8AAAAAACDGPwAAAAAAwNC/AAAAAAAAvr8AAAAAACDAvwAAAAAAAJg/AAAAAACgyb8AAAAAAACuvwAAAAAAALO/AAAAAADAsT8AAAAAACDSvwAAAAAAALu/AAAAAADAvL8AAAAAAACmPwAAAAAAoM6/AAAAAABAs78AAAAAAECzvwAAAAAAwLI/AAAAAABQ1L8AAAAAACDCvwAAAAAAoMC/AAAAAACAoT8AAAAAADDSvwAAAAAAwLm/AAAAAABAt78AAAAAAACyPwAAAAAA0NK/AAAAAABAvr8AAAAAAIC7vwAAAAAAAK0/AAAAAAAAtL8AAAAAAACuPwAAAAAAAIg/AAAAAABAwT8AAAAAAMCyvwAAAAAAALA/AAAAAAAAkj8AAAAAACDCPwAAAAAAAJ2/AAAAAABAvD8AAAAAAICxPwAAAAAAwMc/AAAAAAAAp78AAAAAAIC0PwAAAAAAAJs/AAAAAADAwj8AAAAAAABQvwAAAAAAwL8/AAAAAAAAsT8AAAAAAGDHPwAAAAAAQLO/AAAAAAAAsD8AAAAAAACaPwAAAAAAYMM/AAAAAAAAq78AAAAAAEC1PwAAAAAAgKg/AAAAAABgxj8AAAAAAACgvwAAAAAAQLg/AAAAAACApT8AAAAAAKDEPwAAAAAAQNG/AAAAAACAv78AAAAAAKDAvwAAAAAAAJo/AAAAAABgy78AAAAAAMCxvwAAAAAAQLW/AAAAAAAArD8AAAAAAHDTvwAAAAAAQL+/AAAAAACAvr8AAAAAAACkPwAAAAAAAMu/AAAAAACAqr8AAAAAAICuvwAAAAAAQLU/AAAAAABQ0r8AAAAAAEC8vwAAAAAAQLu/AAAAAACAqT8AAAAAAKDUvwAAAAAAQMG/AAAAAACAv78AAAAAAICnPwAAAAAAcNW/AAAAAADgwr8AAAAAAKDAvwAAAAAAAKQ/AAAAAADAub8AAAAAAACjPwAAAAAAAHS/AAAAAACAvz8AAAAAAICyvwAAAAAAALA/AAAAAAAAkj8AAAAAAEDCPwAAAAAAAKO/AAAAAACAuj8AAAAAAICvPwAAAAAAAMg/AAAAAAAArb8AAAAAAECyPwAAAAAAAJU/AAAAAABgwT8AAAAAAACMvwAAAAAAgL0/AAAAAAAAsD8AAAAAAADHPwAAAAAAAKy/AAAAAADAtD8AAAAAAACgPwAAAAAAQMQ/AAAAAAAAqL8AAAAAAEC4PwAAAAAAgKw/AAAAAAAgxz8AAAAAAAB0vwAAAAAAAL0/AAAAAAAArD8AAAAAAODFPwAAAAAA8NG/AAAAAAAgwb8AAAAAAMDBvwAAAAAAAJM/AAAAAADgxr8AAAAAAICnvwAAAAAAgLC/AAAAAAAAsz8AAAAAAJDQvwAAAAAAQLa/AAAAAAAAuL8AAAAAAICuPwAAAAAAoMW/AAAAAAAAkb8AAAAAAICgvwAAAAAAALs/AAAAAACgz78AAAAAAAC0vwAAAAAAgLW/AAAAAACArz8AAAAAALDSvwAAAAAAgLy/AAAAAACAub8AAAAAAICvPwAAAAAA0NS/AAAAAACgwr8AAAAAAEDBvwAAAAAAgKI/AAAAAACAu78AAAAAAIChPwAAAAAAAIC/AAAAAACAvj8AAAAAAEC1vwAAAAAAAKg/AAAAAAAAcD8AAAAAAKDAPwAAAAAAgKG/AAAAAACAuj8AAAAAAICwPwAAAAAAwMc/AAAAAAAAq78AAAAAAICyPwAAAAAAAJM/AAAAAAAAwj8AAAAAAACIvwAAAAAAwL0/AAAAAACAsD8AAAAAAADHPwAAAAAAALG/AAAAAAAAsj8AAAAAAACfPwAAAAAAwMM/AAAAAACApL8AAAAAAEC5PwAAAAAAAK4/AAAAAADAxj8AAAAAAACfvwAAAAAAgLg/AAAAAACApD8AAAAAAGDEPwAAAAAAMNO/AAAAAACAw78AAAAAAIDEvwAAAAAAAAAAAAAAAAAAy78AAAAAAMCwvwAAAAAAQLS/AAAAAACArz8AAAAAAADPvwAAAAAAwLO/AAAAAAAAtr8AAAAAAACwPwAAAAAAgMe/AAAAAAAAob8AAAAAAICnvwAAAAAAALg/AAAAAADg1L8AAAAAACDDvwAAAAAAoMG/AAAAAAAAmj8AAAAAAIDXvwAAAAAAoMa/AAAAAABAw78AAAAAAACRPwAAAAAAQNe/AAAAAACAxr8AAAAAAIDDvwAAAAAAAJQ/AAAAAAAAv78AAAAAAACaPwAAAAAAAJG/AAAAAACAuz8AAAAAAEC4vwAAAAAAAKY/AAAAAAAAaD8AAAAAAIDAPwAAAAAAgKG/AAAAAADAuj8AAAAAAACvPwAAAAAAYMc/AAAAAAAAqb8AAAAAAECzPwAAAAAAAJc/AAAAAABgwj8AAAAAAACAvwAAAAAAgLw/AAAAAACArz8AAAAAAADHPwAAAAAAAKq/AAAAAAAAtT8AAAAAAICkPwAAAAAAwMQ/AAAAAACApL8AAAAAAAC5PwAAAAAAAK8/AAAAAACAxz8AAAAAAAAAAAAAAAAAwL4/AAAAAAAArz8AAAAAAEDFPwAAAAAAUNW/AAAAAACAx78AAAAAAMDGvwAAAAAAAIy/AAAAAACA0b8AAAAAAAC/vwAAAAAAQMC/AAAAAAAAmT8AAAAAAMDSvwAAAAAAAL2/AAAAAABAvb8AAAAAAIClPwAAAAAAgMm/AAAAAAAApr8AAAAAAICsvwAAAAAAQLY/AAAAAACw0r8AAAAAAMC9vwAAAAAAAL2/AAAAAACApz8AAAAAAPDTvwAAAAAAwMC/AAAAAACAvb8AAAAAAICpPwAAAAAA4NS/AAAAAACAwr8AAAAAAIDAvwAAAAAAAKU/AAAAAACAur8AAAAAAACjPwAAAAAAAHS/AAAAAABAvz8AAAAAAIC2vwAAAAAAAKo/AAAAAAAAfD8AAAAAAIDAPwAAAAAAAKa/AAAAAAAAuT8AAAAAAICtPwAAAAAAIMc/AAAAAACAqL8AAAAAAECzPwAAAAAAAJI/AAAAAADgwT8AAAAAAACAvwAAAAAAgL4/AAAAAABAsT8AAAAAAGDHPwAAAAAAAK+/AAAAAABAsT8AAAAAAACdPwAAAAAA4MM/AAAAAAAAq78AAAAAAMC2PwAAAAAAAKo/AAAAAADAxj8AAAAAAACXvwAAAAAAgLk/AAAAAACApj8AAAAAAIDEPwAAAAAAwNO/AAAAAACgxL8AAAAAAKDEvwAAAAAAAGC/AAAAAAAg0b8AAAAAAIC8vwAAAAAAAL6/AAAAAACAoT8AAAAAAHDVvwAAAAAAwMK/AAAAAADgwb8AAAAAAACRPwAAAAAA4M2/AAAAAABAsr8AAAAAAACzvwAAAAAAwLI/AAAAAABA1L8AAAAAAKDBvwAAAAAAAMG/AAAAAAAAnz8AAAAAACDWvwAAAAAAgMO/AAAAAADgwL8AAAAAAACjPwAAAAAAYNa/AAAAAACAxb8AAAAAAMDCvwAAAAAAAJs/AAAAAADAu78AAAAAAACjPwAAAAAAAHy/AAAAAABAvz8AAAAAAAC1vwAAAAAAgKw/AAAAAAAAiD8AAAAAAMDBPwAAAAAAAJ+/AAAAAABAuz8AAAAAAMCwPwAAAAAAoMc/AAAAAAAAiL8AAAAAAMC7PwAAAAAAAKk/AAAAAADgxD8AAAAAAACjvwAAAAAAwLc/AAAAAAAApT8AAAAAACDEPwAAAAAAgLa/AAAAAACAqT8AAAAAAACAPwAAAAAAgME/AAAAAAAgxL8AAAAAAABwvwAAAAAAgKK/AAAAAABAuj8AAAAAAACjvwAAAAAAQLg/AAAAAAAAqD8AAAAAAGDFPwAAAAAAQLe/AAAAAAAAqD8AAAAAAACGPwAAAAAAYMI/AAAAAADgwb8AAAAAAACAPwAAAAAAAJO/AAAAAABAvj8AAAAAAIDLvwAAAAAAgK6/AAAAAADAs78AAAAAAACyPwAAAAAAYMm/AAAAAAAApL8AAAAAAICtvwAAAAAAwLQ/AAAAAADgzL8AAAAAAECyvwAAAAAAALe/AAAAAACArT8AAAAAAEC1vwAAAAAAgK8/AAAAAAAAkD8AAAAAAEDCPwAAAAAAAK2/AAAAAADAtT8AAAAAAACoPwAAAAAAIMY/AAAAAAAAn78AAAAAAMC3PwAAAAAAgKM/AAAAAAAAxD8AAAAAAACovwAAAAAAgLU/AAAAAACAoT8AAAAAAADEPwAAAAAAgLK/AAAAAACAqz8AAAAAAACGPwAAAAAAgME/AAAAAACArb8AAAAAAEC2PwAAAAAAgKk/AAAAAACgxj8AAAAAAMCwvwAAAAAAALE/AAAAAAAAlD8AAAAAACDCPwAAAAAAAKC/AAAAAAAAuD8AAAAAAACkPwAAAAAAQMM/AAAAAAAAtr8AAAAAAACpPwAAAAAAAHg/AAAAAADgwD8AAAAAAACsvwAAAAAAQLI/AAAAAAAAhD8AAAAAAMDAPwAAAAAAALW/AAAAAACArD8AAAAAAACIPwAAAAAAwME/AAAAAAAAkL8AAAAAAEC7PwAAAAAAgKs/AAAAAAAAxj8AAAAAAAC2vwAAAAAAgK0/AAAAAAAAmD8AAAAAAKDDPwAAAAAAwMm/AAAAAACAqr8AAAAAAMCzvwAAAAAAwLA/AAAAAACAt78AAAAAAACnPwAAAAAAAHC/AAAAAABAvj8AAAAAAIC7vwAAAAAAgKE/AAAAAAAAdL8AAAAAAMC/PwAAAAAAAJm/AAAAAACAuj8AAAAAAICqPwAAAAAAAMU/AAAAAAAAur8AAAAAAICmPwAAAAAAAIo/AAAAAACgwj8AAAAAACDGvwAAAAAAAJu/AAAAAABAsL8AAAAAAACzPwAAAAAAALC/AAAAAADAsD8AAAAAAACMPwAAAAAAwMA/AAAAAACAt78AAAAAAACkPwAAAAAAAFC/AAAAAAAgwD8AAAAAAAChvwAAAAAAALk/AAAAAACApj8AAAAAAMDEPwAAAAAAwLu/AAAAAAAAoz8AAAAAAACAPwAAAAAAwME/AAAAAAAgx78AAAAAAACivwAAAAAAQLC/AAAAAABAsT8AAAAAAMCxvwAAAAAAAK4/AAAAAAAAeD8AAAAAAADAPwAAAAAAgLq/AAAAAACAoT8AAAAAAAB4vwAAAAAAwL0/AAAAAACAp78AAAAAAMC1PwAAAAAAgKE/AAAAAADgwz8AAAAAAEC7vwAAAAAAgKI/AAAAAAAAYD8AAAAAAADBPwAAAAAAYMi/AAAAAAAAp78AAAAAAICyvwAAAAAAwLA/AAAAAABAw78AAAAAAAB4vwAAAAAAAKK/AAAAAADAuT8AAAAAAIC3vwAAAAAAgKM/AAAAAAAAir8AAAAAAIC8PwAAAAAAALS/AAAAAACAqT8AAAAAAAAAAAAAAAAAAL8/AAAAAAAAlr8AAAAAAEC6PwAAAAAAAKY/AAAAAACAwz8AAAAAAIC3vwAAAAAAAKY/AAAAAAAAYD8AAAAAAGDAPwAAAAAAAMC/AAAAAAAAmj8AAAAAAACEvwAAAAAAwL8/AAAAAACAor8AAAAAAMC1PwAAAAAAAJg/AAAAAADgwT8AAAAAAKDUvwAAAAAAgMW/AAAAAACgxb8AAAAAAACAvwAAAAAAANG/AAAAAAAAvb8AAAAAAEC9vwAAAAAAgKI/AAAAAACA2b8AAAAAAGDLvwAAAAAAYMi/AAAAAAAAlL8AAAAAAODNvwAAAAAAgKy/AAAAAACAr78AAAAAAEC2PwAAAAAAgL6/AAAAAAAAlj8AAAAAAACQvwAAAAAAQLw/AAAAAAAAw78AAAAAAAB0vwAAAAAAgKS/AAAAAABAtj8AAAAAAICsvwAAAAAAwLQ/AAAAAAAApT8AAAAAAIDEPwAAAAAAAKa/AAAAAACAtD8AAAAAAACSPwAAAAAAQME/AAAAAAAgxr8AAAAAAACcvwAAAAAAAK2/AAAAAAAAtD8AAAAAAEC2vwAAAAAAAKg/AAAAAAAAaD8AAAAAAGDAPwAAAAAA4MC/AAAAAAAAkj8AAAAAAACCvwAAAAAAIMA/AAAAAAAAtb8AAAAAAACnPwAAAAAAAGA/AAAAAADAvz8AAAAAAEC2vwAAAAAAgKo/AAAAAAAAjj8AAAAAAADCPwAAAAAAgKi/AAAAAADAtD8AAAAAAIChPwAAAAAAwMM/AAAAAABAz78AAAAAAMC4vwAAAAAAgL2/AAAAAAAAlz8AAAAAAJDVvwAAAAAAAMa/AAAAAADgxr8AAAAAAACVvwAAAAAAQL6/AAAAAAAAlj8AAAAAAACevwAAAAAAQLg/AAAAAADgwb8AAAAAAAAAAAAAAAAAgKS/AAAAAAAAtz8AAAAAAMCyvwAAAAAAALA/AAAAAAAAmz8AAAAAAGDDPwAAAAAAQLi/AAAAAAAAoz8AAAAAAACCvwAAAAAAwL0/AAAAAAAAq78AAAAAAICzPwAAAAAAAJs/AAAAAABgwj8AAAAAAMC9vwAAAAAAAJg/AAAAAAAAjL8AAAAAAIC8PwAAAAAAQMK/AAAAAAAAgD8AAAAAAACRvwAAAAAAQL4/AAAAAAAArr8AAAAAAMCwPwAAAAAAAIY/AAAAAACAvz8AAAAAADDUvwAAAAAAwMO/AAAAAADgw78AAAAAAAB0PwAAAAAAUNC/AAAAAACAur8AAAAAAEC9vwAAAAAAAKM/AAAAAADQ2b8AAAAAACDLvwAAAAAAgMi/AAAAAAAAk78AAAAAAMDOvwAAAAAAALG/AAAAAAAAsr8AAAAAAMC0PwAAAAAAALW/AAAAAACAqz8AAAAAAACAPwAAAAAAwMA/AAAAAADAwL8AAAAAAACAPwAAAAAAAKC/AAAAAABAuT8AAAAAAICuvwAAAAAAgLQ/AAAAAAAApT8AAAAAACDEPwAAAAAAgK6/AAAAAAAAsT8AAAAAAACOPwAAAAAAAME/AAAAAACgxr8AAAAAAACfvwAAAAAAQLC/AAAAAADAsj8AAAAAAIC2vwAAAAAAAKs/AAAAAAAAfD8AAAAAAODAPwAAAAAAAL6/AAAAAAAAnj8AAAAAAABwvwAAAAAAgMA/AAAAAABAtL8AAAAAAACrPwAAAAAAAHg/AAAAAABgwD8AAAAAAIC3vwAAAAAAgKU/AAAAAAAAdD8AAAAAACDBPwAAAAAAgKq/AAAAAADAtD8AAAAAAACgPwAAAAAAgMM/AAAAAABAz78AAAAAAAC4vwAAAAAAQL2/AAAAAAAAoD8AAAAAABDVvwAAAAAAYMW/AAAAAABAxr8AAAAAAACWvwAAAAAAQL6/AAAAAAAAlz8AAAAAAACZvwAAAAAAwLk/AAAAAAAAw78AAAAAAAB4vwAAAAAAgKq/AAAAAADAtD8AAAAAAAC2vwAAAAAAAK4/AAAAAAAAlj8AAAAAACDDPwAAAAAAgLm/AAAAAAAAnz8AAAAAAACQvwAAAAAAQLw/AAAAAACArL8AAAAAAICzPwAAAAAAAJs/AAAAAABgwj8AAAAAAMC+vwAAAAAAAJA/AAAAAAAAk78AAAAAAMC8PwAAAAAAYMK/AAAAAAAAgD8AAAAAAACSvwAAAAAAgL4/AAAAAAAAs78AAAAAAACrPwAAAAAAAFC/AAAAAACAvj8AAAAAAIDQvwAAAAAAALu/AAAAAAAAvr8AAAAAAACfPwAAAAAAoMm/AAAAAACArr8AAAAAAACzvwAAAAAAALE/AAAAAAAQ2L8AAAAAAEDIvwAAAAAA4Ma/AAAAAAAAhr8AAAAAACDOvwAAAAAAAK+/AAAAAADAsL8AAAAAAIC1PwAAAAAAgLi/AAAAAAAAoz8AAAAAAAB4vwAAAAAAgL4/AAAAAACAwb8AAAAAAABoPwAAAAAAAKK/AAAAAAAAuD8AAAAAAACyvwAAAAAAALA/AAAAAAAAjj8AAAAAAEDBPwAAAAAAwLG/AAAAAAAArj8AAAAAAAB8PwAAAAAAwL8/AAAAAACAx78AAAAAAIChvwAAAAAAQLC/AAAAAACAsj8AAAAAAIC4vwAAAAAAAKc/AAAAAAAAAAAAAAAAAMC+PwAAAAAAoMC/AAAAAAAAlD8AAAAAAACAvwAAAAAAAMA/AAAAAABAtb8AAAAAAICoPwAAAAAAAGi/AAAAAAAAvz8AAAAAAMC4vwAAAAAAAKY/AAAAAAAAeD8AAAAAAEDBPwAAAAAAgKy/AAAAAAAAsj8AAAAAAACZPwAAAAAAwMI/AAAAAAAAz78AAAAAAIC4vwAAAAAAQL2/AAAAAAAAnj8AAAAAAIDWvwAAAAAAgMe/AAAAAAAAyL8AAAAAAACcvwAAAAAAoMC/AAAAAAAAiD8AAAAAAIChvwAAAAAAALY/AAAAAAAgw78AAAAAAACEvwAAAAAAAKi/AAAAAAAAtT8AAAAAAMC0vwAAAAAAgLA/AAAAAAAAlz8AAAAAAMDCPwAAAAAAwLm/AAAAAAAAoD8AAAAAAACKvwAAAAAAgLw/AAAAAAAAq78AAAAAAICzPwAAAAAAAJM/AAAAAACgwT8AAAAAAEC/vwAAAAAAAJE/AAAAAAAAlL8AAAAAAAC8PwAAAAAAgMK/AAAAAAAAAAAAAAAAAACYvwAAAAAAgLw/AAAAAACAs78AAAAAAACqPwAAAAAAAGi/AAAAAACAvT8AAAAAABDSvwAAAAAAgMC/AAAAAABgwb8AAAAAAACSPwAAAAAAQMu/AAAAAADAsb8AAAAAAEC1vwAAAAAAgKs/AAAAAADg1r8AAAAAAGDGvwAAAAAA4MS/AAAAAAAAAAAAAAAAAKDMvwAAAAAAgKm/AAAAAADAsL8AAAAAAAC1PwAAAAAAQLu/AAAAAAAAnz8AAAAAAACGvwAAAAAAgL0/AAAAAACgwr8AAAAAAACAvwAAAAAAgKe/AAAAAADAtT8AAAAAAICuvwAAAAAAgLM/AAAAAACAoD8AAAAAAEDDPwAAAAAAAKe/AAAAAAAAsj8AAAAAAACRPwAAAAAAAME/AAAAAACAx78AAAAAAACkvwAAAAAAQLG/AAAAAACAsT8AAAAAAMC4vwAAAAAAAKY/AAAAAAAAAAAAAAAAAMC/PwAAAAAAwL+/AAAAAAAAlz8AAAAAAAB8vwAAAAAAAL8/AAAAAADAtb8AAAAAAACnPwAAAAAAAFC/AAAAAAAAvz8AAAAAAEC5vwAAAAAAgKU/AAAAAAAAAAAAAAAAAIDAPwAAAAAAAK+/AAAAAAAAsj8AAAAAAACYPwAAAAAAgMI/AAAAAAAQ0L8AAAAAAEC8vwAAAAAAIMC/AAAAAAAAlT8AAAAAADDWvwAAAAAAgMe/AAAAAAAAyL8AAAAAAACcvwAAAAAAgMC/AAAAAAAAhj8AAAAAAIChvwAAAAAAgLc/AAAAAAAgw78AAAAAAACCvwAAAAAAgKi/AAAAAAAAtT8AAAAAAEC2vwAAAAAAgK0/AAAAAAAAlT8AAAAAAMDCPwAAAAAAwLm/AAAAAAAAoD8AAAAAAACKvwAAAAAAgLs/AAAAAAAArb8AAAAAAMCyPwAAAAAAAJg/AAAAAADgwT8AAAAAAAC/vwAAAAAAAJI/AAAAAAAAmb8AAAAAAAC7PwAAAAAAwMK/AAAAAAAAeD8AAAAAAACVvwAAAAAAgL0/AAAAAACAsL8AAAAAAICsPwAAAAAAAAAAAAAAAABAvj8AAAAAABDSvwAAAAAAoMC/AAAAAACgwb8AAAAAAACSPwAAAAAAQM2/AAAAAADAtb8AAAAAAAC4vwAAAAAAgKk/AAAAAACg2L8AAAAAAIDJvwAAAAAAIMe/AAAAAAAAlL8AAAAAAFDQvwAAAAAAALS/AAAAAACAtL8AAAAAAACyPwAAAAAAQLq/AAAAAAAAoj8AAAAAAAB4vwAAAAAAwLw/AAAAAABgwb8AAAAAAABwPwAAAAAAgKK/AAAAAAAAuD8AAAAAAACwvwAAAAAAwLI/AAAAAAAAmD8AAAAAAKDCPwAAAAAAAK6/AAAAAABAsD8AAAAAAACIPwAAAAAAoMA/AAAAAACAx78AAAAAAICmvwAAAAAAALK/AAAAAABAsT8AAAAAAMC8vwAAAAAAAKA/AAAAAAAAhr8AAAAAAEC+PwAAAAAAoMK/AAAAAAAAgD8AAAAAAACRvwAAAAAAAL4/AAAAAACAtr8AAAAAAIClPwAAAAAAAFC/AAAAAADAvT8AAAAAAIC5vwAAAAAAgKU/AAAAAAAAeD8AAAAAAODAPwAAAAAAgK+/AAAAAACAsj8AAAAAAACTPwAAAAAAYMI/AAAAAAAg0b8AAAAAAEC+vwAAAAAAwMC/AAAAAAAAkT8AAAAAAFDXvwAAAAAAAMm/AAAAAADgyb8AAAAAAICjvwAAAAAAwMG/AAAAAAAAdD8AAAAAAICjvwAAAAAAwLY/AAAAAAAgw78AAAAAAACIvwAAAAAAgKm/AAAAAADAtD8AAAAAAIC1vwAAAAAAgK8/AAAAAAAAmT8AAAAAAADDPwAAAAAAwLq/AAAAAAAAnD8AAAAAAACQvwAAAAAAALw/AAAAAAAArL8AAAAAAMCyPwAAAAAAAJg/AAAAAABgwT8AAAAAAEC/vwAAAAAAAJA/AAAAAAAAk78AAAAAAAC8PwAAAAAAwMK/AAAAAAAAcD8AAAAAAACcvwAAAAAAQLw/AAAAAACAsr8AAAAAAICrPwAAAAAAAFC/AAAAAABAvj8AAAAAAFDSvwAAAAAAQMG/AAAAAAAAwr8AAAAAAACOPwAAAAAAQMy/AAAAAABAs78AAAAAAIC2vwAAAAAAgKw/AAAAAADw2b8AAAAAAADMvwAAAAAAIMm/AAAAAAAAmr8AAAAAAGDQvwAAAAAAgLO/AAAAAABAtL8AAAAAAACyPwAAAAAAQLq/AAAAAAAAoj8AAAAAAAB8vwAAAAAAwL4/AAAAAABAwb8AAAAAAAB0PwAAAAAAgKG/AAAAAAAAtz8AAAAAAECwvwAAAAAAgLI/AAAAAAAAnD8AAAAAACDDPwAAAAAAgK6/AAAAAADAsD8AAAAAAAB4PwAAAAAAQMA/AAAAAABAx78AAAAAAICivwAAAAAAQLC/AAAAAACAsj8AAAAAAEC3vwAAAAAAAKU/AAAAAAAAUL8AAAAAAADAPwAAAAAAAMC/AAAAAAAAmD8AAAAAAAB0vwAAAAAAIMA/AAAAAAAAtr8AAAAAAICnPwAAAAAAAFC/AAAAAABAvz8AAAAAAAC5vwAAAAAAAKU/AAAAAAAAdD8AAAAAAKDAPwAAAAAAALC/AAAAAACAsj8AAAAAAACYPwAAAAAAgMI/AAAAAABg0b8AAAAAAMC+vwAAAAAAIMG/AAAAAAAAiD8AAAAAAFDXvwAAAAAAQMm/AAAAAAAgyb8AAAAAAIChvwAAAAAA4MC/AAAAAAAAhD8AAAAAAICkvwAAAAAAwLY/AAAAAAAAxL8AAAAAAACOvwAAAAAAgKq/AAAAAABAtD8AAAAAAMC2vwAAAAAAgKs/AAAAAAAAkz8AAAAAAIDCPwAAAAAAQLq/AAAAAAAAoD8AAAAAAACKvwAAAAAAwLw/AAAAAAAArr8AAAAAAECyPwAAAAAAAJU/AAAAAADgwT8AAAAAAEC/vwAAAAAAAJI/AAAAAAAAkr8AAAAAAMC6PwAAAAAAIMK/AAAAAAAAhD8AAAAAAACRvwAAAAAAAL4/AAAAAADAsL8AAAAAAICuPwAAAAAAAGA/AAAAAAAAvj8AAAAAAIDSvwAAAAAAIMG/AAAAAADAwb8AAAAAAACQPwAAAAAAYMu/AAAAAAAAsr8AAAAAAAC2vwAAAAAAgK0/AAAAAAAQ1r8AAAAAACDFvwAAAAAAwMO/AAAAAAAAgD8AAAAAAADLvwAAAAAAAKe/AAAAAACArL8AAAAAAIC3PwAAAAAAgLm/AAAAAACAoj8AAAAAAAB4vwAAAAAAgL4/AAAAAABAwr8AAAAAAABQvwAAAAAAAKS/AAAAAACAtz8AAAAAAACwvwAAAAAAALE/AAAAAAAAjj8AAAAAAADAPwAAAAAAQLG/AAAAAACArj8AAAAAAAB8PwAAAAAAQMA/AAAAAACAxr8AAAAAAACdvwAAAAAAALG/AAAAAABAsj8AAAAAAMC5vwAAAAAAAKU/AAAAAAAAUL8AAAAAAMC/PwAAAAAAgMC/AAAAAAAAkD8AAAAAAACKvwAAAAAAAL8/AAAAAAAAtr8AAAAAAACoPwAAAAAAAFA/AAAAAADAvz8AAAAAAIC4vwAAAAAAgKQ/AAAAAAAAcD8AAAAAACDBPwAAAAAAQLK/AAAAAACArz8AAAAAAACSPwAAAAAAAMI/AAAAAAAQ0b8AAAAAAIC9vwAAAAAAoMC/AAAAAAAAkz8AAAAAACDXvwAAAAAAoMi/AAAAAACgyL8AAAAAAACjvwAAAAAAoMK/AAAAAAAAAAAAAAAAAIClvwAAAAAAALY/AAAAAACAw78AAAAAAACGvwAAAAAAAKy/AAAAAADAsz8AAAAAAEC2vwAAAAAAAK0/AAAAAAAAlT8AAAAAAODCPwAAAAAAwLm/AAAAAAAAmz8AAAAAAACQvwAAAAAAALw/AAAAAAAArb8AAAAAAACzPwAAAAAAAJc/AAAAAAAgwj8AAAAAAMC/vwAAAAAAAIo/AAAAAAAAl78AAAAAAMC7PwAAAAAAQMK/AAAAAAAAgD8AAAAAAACSvwAAAAAAAL4/AAAAAABAs78AAAAAAACrPwAAAAAAAGC/AAAAAADAvT8AAAAAANDTvwAAAAAAgMO/AAAAAADAw78AAAAAAABQvwAAAAAAUNC/AAAAAADAub8AAAAAAIC7vwAAAAAAAKU/AAAAAADQ1r8AAAAAAODFvwAAAAAAIMW/AAAAAAAAAAAAAAAAAEDNvwAAAAAAAKu/AAAAAACAr78AAAAAAAC2PwAAAAAAgLe/AAAAAACAoz8AAAAAAAB4vwAAAAAAwL4/AAAAAACgwb8AAAAAAABoPwAAAAAAgKK/AAAAAAAAuD8AAAAAAACwvwAAAAAAALE/AAAAAAAAkz8AAAAAAKDBPwAAAAAAAK+/AAAAAADAsD8AAAAAAACKPwAAAAAAIMA/AAAAAABAyL8AAAAAAICkvwAAAAAAgLG/AAAAAACAsT8AAAAAAIC4vwAAAAAAgKY/AAAAAAAAUD8AAAAAAAC/PwAAAAAAYMC/AAAAAAAAlT8AAAAAAAB8vwAAAAAAwL8/AAAAAABAtb8AAAAAAICoPwAAAAAAAHS/AAAAAABAvj8AAAAAAMC3vwAAAAAAAKc/AAAAAAAAfD8AAAAAAEDBPwAAAAAAgLG/AAAAAAAArz8AAAAAAACQPwAAAAAA4ME/AAAAAADAz78AAAAAAAC6vwAAAAAAQL6/AAAAAAAAmz8AAAAAAGDWvwAAAAAAAMi/AAAAAACAyL8AAAAAAACevwAAAAAAYMC/AAAAAAAAjD8AAAAAAACgvwAAAAAAALY/AAAAAABAw78AAAAAAACCvwAAAAAAgKi/AAAAAADAtD8AAAAAAIC0vwAAAAAAgK8/AAAAAAAAlj8AAAAAAIDCPwAAAAAAALq/AAAAAAAAnz8AAAAAAACOvwAAAAAAgLw/AAAAAACAq78AAAAAAACzPwAAAAAAAJQ/AAAAAADAwT8AAAAAAIC+vwAAAAAAAJQ/AAAAAAAAkr8AAAAAAIC8PwAAAAAAQMK/AAAAAAAAYD8AAAAAAACYvwAAAAAAwLw/AAAAAAAAr78AAAAAAMCwPwAAAAAAAIA/AAAAAAAAwD8AAAAAAPDTvwAAAAAAIMS/AAAAAABAxL8AAAAAAABgPwAAAAAAIMy/AAAAAACAs78AAAAAAEC2vwAAAAAAgKk/AAAAAACg1b8AAAAAAIDEvwAAAAAAYMO/AAAAAAAAhD8AAAAAACDLvwAAAAAAAKW/AAAAAAAArb8AAAAAAAC3PwAAAAAAALi/AAAAAACApT8AAAAAAABgvwAAAAAAwL4/AAAAAABAwb8AAAAAAABgvwAAAAAAAKS/AAAAAACAtj8AAAAAAACyvwAAAAAAAK4/AAAAAAAAfD8AAAAAAGDAPwAAAAAAgLS/AAAAAACApz8AAAAAAABgvwAAAAAAwL4/AAAAAACAx78AAAAAAICivwAAAAAAgLC/AAAAAAAAsj8AAAAAAIC6vwAAAAAAgKI/AAAAAAAAdL8AAAAAAAC/PwAAAAAAQMC/AAAAAAAAlj8AAAAAAAB4vwAAAAAAwL4/AAAAAADAtr8AAAAAAICmPwAAAAAAAGC/AAAAAABAvz8AAAAAAEC3vwAAAAAAgKg/AAAAAAAAdD8AAAAAAODAPwAAAAAAQLG/AAAAAAAAsT8AAAAAAACUPwAAAAAAQMI/AAAAAABA0L8AAAAAAEC8vwAAAAAAQMC/AAAAAAAAlz8AAAAAAFDXvwAAAAAAAMm/AAAAAAAgyb8AAAAAAACivwAAAAAAIMK/AAAAAAAAUL8AAAAAAIClvwAAAAAAwLU/AAAAAABgw78AAAAAAACEvwAAAAAAAKm/AAAAAADAtD8AAAAAAEC2vwAAAAAAgK0/AAAAAAAAlj8AAAAAAODCPwAAAAAAALq/AAAAAAAAoT8AAAAAAACKvwAAAAAAALs/AAAAAACArr8AAAAAAECyPwAAAAAAAJY/AAAAAADgwT8AAAAAAEC/vwAAAAAAAJE/AAAAAAAAmb8AAAAAAEC7PwAAAAAAAMO/AAAAAAAAYD8AAAAAAACWvwAAAAAAAL0/AAAAAADAsL8AAAAAAACsPwAAAAAAAAAAAAAAAABAvj8AAAAAAFDTvwAAAAAAwMK/AAAAAAAAw78AAAAAAACAPwAAAAAAoM+/AAAAAABAub8AAAAAAEC7vwAAAAAAgKU/AAAAAABw2b8AAAAAAKDKvwAAAAAAAMi/AAAAAAAAmL8AAAAAAODOvwAAAAAAQLC/AAAAAADAsb8AAAAAAEC0PwAAAAAAgLu/AAAAAAAAoT8AAAAAAACGvwAAAAAAAL0/AAAAAADgwr8AAAAAAAB4vwAAAAAAgKW/AAAAAACAtj8AAAAAAECwvwAAAAAAwLI/AAAAAAAAmz8AAAAAAEDDPwAAAAAAALG/AAAAAAAArj8AAAAAAAB8PwAAAAAAIMA/AAAAAADgyL8AAAAAAACqvwAAAAAAQLO/AAAAAAAAsD8AAAAAAAC4vwAAAAAAAKY/AAAAAAAAUD8AAAAAAEDAPwAAAAAAgL+/AAAAAAAAmT8AAAAAAABwvwAAAAAAIMA/AAAAAABAtb8AAAAAAICoPwAAAAAAAGA/AAAAAACAvj8AAAAAAMC4vwAAAAAAAKY/AAAAAAAAdD8AAAAAAADBPwAAAAAAALG/AAAAAABAsT8AAAAAAACMPwAAAAAA4ME/AAAAAACQ0b8AAAAAAEC/vwAAAAAAIMG/AAAAAAAAjj8AAAAAAEDXvwAAAAAAoMm/AAAAAACgyb8AAAAAAICivwAAAAAAIMG/AAAAAAAAhD8AAAAAAICivwAAAAAAQLc/AAAAAACgxL8AAAAAAACVvwAAAAAAAK6/AAAAAAAAsz8AAAAAAMC3vwAAAAAAAKo/AAAAAAAAkT8AAAAAAGDCPwAAAAAAALu/AAAAAAAAnz8AAAAAAACQvwAAAAAAgLw/AAAAAACArL8AAAAAAICzPwAAAAAAAJg/AAAAAABgwT8AAAAAAAC/vwAAAAAAAJI/AAAAAAAAk78AAAAAAMC8PwAAAAAA4MG/AAAAAAAAhj8AAAAAAACUvwAAAAAAwL0/AAAAAACAsb8AAAAAAICuPwAAAAAAAHA/AAAAAAAAvz8AAAAAAPDQvwAAAAAAAMC/AAAAAADgwL8AAAAAAACVPwAAAAAAwMq/AAAAAABAsb8AAAAAAEC0vwAAAAAAAK8/AAAAAACA1b8AAAAAACDEvwAAAAAAAMO/AAAAAAAAij8AAAAAAKDJvwAAAAAAgKC/AAAAAACAp78AAAAAAAC5PwAAAAAAQLm/AAAAAACAoz8AAAAAAAB0vwAAAAAAgL4/AAAAAACAwr8AAAAAAABwvwAAAAAAAKS/AAAAAADAtT8AAAAAAICuvwAAAAAAwLM/AAAAAAAAoj8AAAAAAADEPwAAAAAAAKm/AAAAAACAsj8AAAAAAACIPwAAAAAAwMA/AAAAAABgxr8AAAAAAACfvwAAAAAAAK+/AAAAAAAAsz8AAAAAAIC4vwAAAAAAAKQ/AAAAAAAAcL8AAAAAAEC/PwAAAAAAgL+/AAAAAAAAmT8AAAAAAAB0vwAAAAAAYMA/AAAAAABAt78AAAAAAAClPwAAAAAAAGi/AAAAAADAvj8AAAAAAAC4vwAAAAAAgKc/AAAAAAAAgj8AAAAAAIDAPwAAAAAAALO/AAAAAACArz8AAAAAAACRPwAAAAAA4ME/AAAAAACg0b8AAAAAAADAvwAAAAAAwMG/AAAAAAAAfD8AAAAAABDZvwAAAAAAAMy/AAAAAACAy78AAAAAAICovwAAAAAAIMO/AAAAAAAAYL8AAAAAAACpvwAAAAAAQLQ/AAAAAAAAxL8AAAAAAACKvwAAAAAAAKq/AAAAAACAtD8AAAAAAIC1vwAAAAAAgKs/AAAAAAAAlD8AAAAAAKDCPwAAAAAAALq/AAAAAAAAoT8AAAAAAACIvwAAAAAAgLw/AAAAAAAArr8AAAAAAMCyPwAAAAAAAJc/AAAAAAAgwj8AAAAAAEC/vwAAAAAAAJE/AAAAAAAAk78AAAAAAMC6PwAAAAAAAMO/AAAAAAAAfD8AAAAAAACUvwAAAAAAwL0/AAAAAABAsr8AAAAAAICsPwAAAAAAAHC/AAAAAADAvT8AAAAAAMDSvwAAAAAAoMG/AAAAAAAgwr8AAAAAAACMPwAAAAAAQM6/AAAAAAAAt78AAAAAAEC5vwAAAAAAgKc/AAAAAACg178AAAAAAEDHvwAAAAAAgMW/AAAAAAAAYL8AAAAAAADPvwAAAAAAwLG/AAAAAABAsr8AAAAAAAC0PwAAAAAAQLq/AAAAAAAAoj8AAAAAAAB8vwAAAAAAQL4/AAAAAACgwb8AAAAAAABwPwAAAAAAAKK/AAAAAABAuD8AAAAAAACvvwAAAAAAALM/AAAAAAAAnz8AAAAAAMDCPwAAAAAAgKy/AAAAAADAsT8AAAAAAACQPwAAAAAAAME/AAAAAABAyL8AAAAAAICkvwAAAAAAQLO/AAAAAACAsD8AAAAAAMC4vwAAAAAAAKY/AAAAAAAAAAAAAAAAAEDAPwAAAAAAAL6/AAAAAAAAmD8AAAAAAAB0vwAAAAAAYMA/AAAAAABAtb8AAAAAAICoPwAAAAAAAGA/AAAAAAAAwD8AAAAAAEC5vwAAAAAAgKU/AAAAAAAAcD8AAAAAAODAPwAAAAAAgLO/AAAAAAAArj8AAAAAAACOPwAAAAAAgME/AAAAAABw0b8AAAAAAMC+vwAAAAAAIMG/AAAAAAAAkT8AAAAAAMDWvwAAAAAAIMi/AAAAAACAyL8AAAAAAIChvwAAAAAAAMG/AAAAAAAAhD8AAAAAAIChvwAAAAAAgLc/AAAAAABAw78AAAAAAACAvwAAAAAAgKq/AAAAAACAtD8AAAAAAMC1vwAAAAAAgK4/AAAAAAAAmD8AAAAAAEDDPwAAAAAAgLm/AAAAAAAAnT8AAAAAAACQvwAAAAAAALw/AAAAAACArL8AAAAAAACzPwAAAAAAAJk/AAAAAABAwj8AAAAAAEC/vwAAAAAAAJM/AAAAAAAAk78AAAAAAIC8PwAAAAAAAMK/AAAAAAAAhD8AAAAAAACRvwAAAAAAgLw/AAAAAAAAsL8AAAAAAACwPwAAAAAAAHw/AAAAAAAAwD8AAAAAAKDSvwAAAAAA4MG/AAAAAADgwr8AAAAAAACAPwAAAAAAgMy/AAAAAACAs78AAAAAAEC2vwAAAAAAgKw/AAAAAABQ178AAAAAACDHvwAAAAAAAMa/AAAAAAAAeL8AAAAAAMDNvwAAAAAAgKy/AAAAAABAsL8AAAAAAMC1PwAAAAAAwLa/AAAAAAAApj8AAAAAAABovwAAAAAAAL8/AAAAAACAwL8AAAAAAACEPwAAAAAAAKC/AAAAAADAuD8AAAAAAECxvwAAAAAAgLI/AAAAAAAAnz8AAAAAAKDDPwAAAAAAAK6/AAAAAABAsT8AAAAAAACKPwAAAAAAIMA/AAAAAABAyL8AAAAAAAClvwAAAAAAwLG/AAAAAACAsT8AAAAAAEC7vwAAAAAAAKI/AAAAAAAAjL8AAAAAAIC9PwAAAAAAIMG/AAAAAAAAkT8AAAAAAACEvwAAAAAAgL8/AAAAAACAtr8AAAAAAICkPwAAAAAAAHi/AAAAAACAvj8AAAAAAMC2vwAAAAAAgKk/AAAAAAAAhD8AAAAAAKDBPwAAAAAAALO/AAAAAAAArD8AAAAAAACKPwAAAAAAoME/AAAAAADw0r8AAAAAAODBvwAAAAAAIMO/AAAAAAAAaD8AAAAAABDavwAAAAAAwM2/AAAAAADAzL8AAAAAAACtvwAAAAAAIMS/AAAAAAAAgr8AAAAAAACpvwAAAAAAwLI/AAAAAACAxL8AAAAAAACOvwAAAAAAAKu/AAAAAACAtD8AAAAAAEC2vwAAAAAAgK4/AAAAAAAAkj8AAAAAAIDCPwAAAAAAALq/AAAAAACAoD8AAAAAAACIvwAAAAAAwLw/AAAAAACAqr8AAAAAAMCxPwAAAAAAAJU/AAAAAADgwT8AAAAAAEC/vwAAAAAAAJI/AAAAAAAAlL8AAAAAAMC8PwAAAAAAAMO/AAAAAAAAAAAAAAAAAACXvwAAAAAAAL0/AAAAAABAsL8AAAAAAICvPwAAAAAAAHA/AAAAAACAvz8AAAAAABDTvwAAAAAAQMK/AAAAAADgwr8AAAAAAACGPwAAAAAAgM2/AAAAAABAtb8AAAAAAMC3vwAAAAAAAKg/AAAAAAAA2r8AAAAAAADMvwAAAAAAAMm/AAAAAAAAmL8AAAAAAMDQvwAAAAAAQLW/AAAAAADAtr8AAAAAAMCwPwAAAAAAQL+/AAAAAAAAlD8AAAAAAACUvwAAAAAAQLw/AAAAAAAgwr8AAAAAAAB0vwAAAAAAAKa/AAAAAADAtj8AAAAAAICwvwAAAAAAwLI/AAAAAAAAnT8AAAAAACDDPwAAAAAAQLC/AAAAAABAsD8AAAAAAACGPwAAAAAAoMA/AAAAAACgxr8AAAAAAICgvwAAAAAAgK+/AAAAAAAAsj8AAAAAAEC3vwAAAAAAgKg/AAAAAAAAdD8AAAAAAGDAPwAAAAAAgL2/AAAAAAAAoD8AAAAAAABgPwAAAAAAYMA/AAAAAAAAtb8AAAAAAACpPwAAAAAAAGA/AAAAAADAvz8AAAAAAMC2vwAAAAAAgKk/AAAAAAAAfD8AAAAAAEDBPwAAAAAAwLG/AAAAAABAsD8AAAAAAACUPwAAAAAAQMI/AAAAAAAQ0b8AAAAAAMC/vwAAAAAAYMG/AAAAAAAAjD8AAAAAAMDWvwAAAAAAoMi/AAAAAACgyL8AAAAAAICgvwAAAAAAYMG/AAAAAAAAgD8AAAAAAICivwAAAAAAALc/AAAAAADgw78AAAAAAACIvwAAAAAAAKq/AAAAAADAsj8AAAAAAMC3vwAAAAAAAKs/AAAAAAAAkz8AAAAAAMDCPwAAAAAAwLm/AAAAAACAoT8AAAAAAACQvwAAAAAAALw/AAAAAAAArL8AAAAAAICzPwAAAAAAAJo/AAAAAABgwj8AAAAAAMC9vwAAAAAAAJU/AAAAAAAAlb8AAAAAAEC8PwAAAAAAIMG/AAAAAAAAkT8AAAAAAACIvwAAAAAAQL8/AAAAAACArL8AAAAAAICwPwAAAAAAAII/AAAAAAAAwD8AAAAAAMDSvwAAAAAA4MG/AAAAAABgwr8AAAAAAACKPwAAAAAA4Mu/AAAAAADAsr8AAAAAAIC1vwAAAAAAAK0/AAAAAABA1b8AAAAAAKDDvwAAAAAAoMK/AAAAAAAAiD8AAAAAAGDLvwAAAAAAgKW/AAAAAACAqr8AAAAAAAC4PwAAAAAAQLm/AAAAAACAoz8AAAAAAACEvwAAAAAAwL0/AAAAAACgwb8AAAAAAABwPwAAAAAAgKK/AAAAAAAAuD8AAAAAAACnvwAAAAAAwLQ/AAAAAACAoD8AAAAAAGDDPwAAAAAAAKO/AAAAAADAtT8AAAAAAACaPwAAAAAAQMI/AAAAAADAxb8AAAAAAACdvwAAAAAAgK+/AAAAAACAsz8AAAAAAIC4vwAAAAAAAKY/AAAAAAAAAAAAAAAAACDAPwAAAAAAQMC/AAAAAAAAmD8AAAAAAAB0vwAAAAAAQMA/AAAAAADAtb8AAAAAAACoPwAAAAAAAFA/AAAAAADAvj8AAAAAAIC3vwAAAAAAgKg/AAAAAAAAhj8AAAAAAKDBPwAAAAAAQLS/AAAAAACArT8AAAAAAACEPwAAAAAAQME/AAAAAAAA0r8AAAAAACDAvwAAAAAAwMG/AAAAAAAAiD8AAAAAAADYvwAAAAAAoMq/AAAAAAAgyr8AAAAAAICkvwAAAAAAAMO/AAAAAAAAcL8AAAAAAICmvwAAAAAAwLU/AAAAAACgxL8AAAAAAACMvwAAAAAAAKq/AAAAAADAtD8AAAAAAAC2vwAAAAAAAK4/AAAAAAAAlz8AAAAAAIDCPwAAAAAAwLq/AAAAAAAAnz8AAAAAAACMvwAAAAAAwLw/AAAAAAAArL8AAAAAAECzPwAAAAAAAJo/AAAAAADgwT8AAAAAAEC+vwAAAAAAAJY/AAAAAAAAjr8AAAAAAAC9PwAAAAAAwMG/AAAAAAAAiD8AAAAAAACUvwAAAAAAwL0/AAAAAAAAs78AAAAAAICrPwAAAAAAAFC/AAAAAABAvj8AAAAAAJDUvwAAAAAAQMW/AAAAAAAAxb8AAAAAAABovwAAAAAAUNG/AAAAAABAvb8AAAAAAEC+vwAAAAAAAKE/AAAAAABA2L8AAAAAAIDIvwAAAAAAgMa/AAAAAAAAeL8AAAAAAODNvwAAAAAAgK2/AAAAAACAsL8AAAAAAAC0PwAAAAAAAL2/AAAAAAAAnT8AAAAAAACIvwAAAAAAgL0/AAAAAAAAwr8AAAAAAABgPwAAAAAAgKS/AAAAAADAtj8AAAAAAACpvwAAAAAAALU/AAAAAACAoD8AAAAAAIDDPwAAAAAAAJy/AAAAAACAtz8AAAAAAACdPwAAAAAAYMI/AAAAAADgxL8AAAAAAACVvwAAAAAAAKu/AAAAAAAAtT8AAAAAAEC2vwAAAAAAAKg/AAAAAAAAaD8AAAAAAIDAPwAAAAAAwL2/AAAAAAAAoD8AAAAAAABQPwAAAAAAAME/AAAAAACAtb8AAAAAAACoPwAAAAAAAAAAAAAAAAAAwD8AAAAAAAC2vwAAAAAAAKo/AAAAAAAAij8AAAAAACDBPwAAAAAAALK/AAAAAACAsD8AAAAAAACUPwAAAAAAQMI/AAAAAAAw0L8AAAAAAIC6vwAAAAAAIMC/AAAAAAAAlj8AAAAAAADWvwAAAAAAYMe/AAAAAADgx78AAAAAAACbvwAAAAAAQMC/AAAAAAAAhj8AAAAAAACivwAAAAAAQLc/AAAAAACAw78AAAAAAACGvwAAAAAAAKm/AAAAAAAAtT8AAAAAAACzvwAAAAAAALE/AAAAAAAAmz8AAAAAAIDDPwAAAAAAAKq/AAAAAADAsj8AAAAAAACUPwAAAAAAgME/AAAAAAAAub8AAAAAAAChPwAAAAAAAI6/AAAAAAAAvD8AAAAAAGDEvwAAAAAAAJC/AAAAAAAAqr8AAAAAAAC0PwAAAAAAAK2/AAAAAADAsj8AAAAAAACTPwAAAAAAYME/AAAAAADAvr8AAAAAAACMPwAAAAAAAKG/AAAAAABAuD8AAAAAAIDDvwAAAAAAAIa/AAAAAACAqL8AAAAAAAC1PwAAAAAAANS/AAAAAACgw78AAAAAAODDvwAAAAAAAGA/AAAAAABQ0b8AAAAAAMC5vwAAAAAAwLq/AAAAAACAqT8AAAAAAEDEvwAAAAAAAIK/AAAAAACAob8AAAAAAAC7PwAAAAAA8Ny/AAAAAABA0b8AAAAAACDOvwAAAAAAAKu/AAAAAACQ0b8AAAAAAMC9vwAAAAAA4MG/AAAAAAAAgD8AAAAAAADgvwAAAAAAENq/AAAAAABA1r8AAAAAACDDvwAAAAAAgNO/AAAAAABAwL8AAAAAAADBvwAAAAAAAJg/AAAAAAAAw78AAAAAAACAvwAAAAAAAKe/AAAAAABAtz8AAAAAAIDdvwAAAAAA0NG/AAAAAABAzr8AAAAAAACpvwAAAAAAAOC/AAAAAAAA4L8AAAAAAPDfvwAAAAAA0NC/AAAAAAAA4L8AAAAAAADgvwAAAAAAoNy/AAAAAACAzL8AAAAAAADgvwAAAAAAAOC/AAAAAABg3b8AAAAAAGDNvwAAAAAAAOC/AAAAAABg178AAAAAALDTvwAAAAAAgLy/AAAAAAAA078AAAAAACDAvwAAAAAAQL2/AAAAAACApT8AAAAAAIDTvwAAAAAAgL+/AAAAAABAv78AAAAAAICjPwAAAAAAoMW/AAAAAAAAiL8AAAAAAICgvwAAAAAAwLs/AAAAAADAz78AAAAAAIC2vwAAAAAAALq/AAAAAAAArD8AAAAAAICwvwAAAAAAALQ/AAAAAAAAoj8AAAAAAIDEPwAAAAAAwNa/AAAAAABAyb8AAAAAACDGvwAAAAAAAGA/AAAAAABgyr8AAAAAAICqvwAAAAAAgKu/AAAAAAAAuD8AAAAAAIDWvwAAAAAAQMW/AAAAAABAw78AAAAAAACSPwAAAAAAALe/AAAAAAAArj8AAAAAAACXPwAAAAAAoMI/AAAAAABAvL8AAAAAAICiPwAAAAAAAHQ/AAAAAADAwT8AAAAAAAC4vwAAAAAAgKc/AAAAAAAAeD8AAAAAAKDBPwAAAAAAUNK/AAAAAACAvr8AAAAAAEC+vwAAAAAAAKY/AAAAAAAAuL8AAAAAAICrPwAAAAAAAJA/AAAAAACgwj8AAAAAANDZvwAAAAAAgM2/AAAAAABAyb8AAAAAAACRvwAAAAAAIMq/AAAAAACAq78AAAAAAACsvwAAAAAAgLg/AAAAAABQ1b8AAAAAAMDCvwAAAAAAoMC/AAAAAACAoj8AAAAAAEC2vwAAAAAAQLA/AAAAAAAAoD8AAAAAAGDEPwAAAAAAQMG/AAAAAAAAkz8AAAAAAAB0vwAAAAAAIMA/AAAAAADAub8AAAAAAACnPwAAAAAAAJA/AAAAAAAAwz8AAAAAACDOvwAAAAAAALS/AAAAAABAt78AAAAAAMCwPwAAAAAAAK+/AAAAAAAAtT8AAAAAAIClPwAAAAAAYMU/AAAAAADA2b8AAAAAAADOvwAAAAAAAMm/AAAAAAAAir8AAAAAAODPvwAAAAAAALa/AAAAAABAs78AAAAAAAC1PwAAAAAAMNe/AAAAAABAxr8AAAAAAODDvwAAAAAAAJI/AAAAAACAuL8AAAAAAICsPwAAAAAAAJY/AAAAAACAwz8AAAAAAMC9vwAAAAAAAKE/AAAAAAAAYD8AAAAAAIDBPwAAAAAAgLe/AAAAAAAAqj8AAAAAAACSPwAAAAAAgMI/AAAAAACQ0L8AAAAAAMC2vwAAAAAAwLa/AAAAAADAsj8AAAAAAMDDvwAAAAAAAIY/AAAAAAAAjL8AAAAAAIDAPwAAAAAAwLW/AAAAAACArz8AAAAAAACdPwAAAAAAYMQ/AAAAAAAAjD8AAAAAAGDBPwAAAAAAwLQ/AAAAAABgyT8AAAAAAICmvwAAAAAAALo/AAAAAABAsT8AAAAAACDJPwAAAAAAgKy/AAAAAADAtj8AAAAAAICtPwAAAAAAQMg/AAAAAAAAfD8AAAAAAGDBPwAAAAAAALY/AAAAAACAyT8AAAAAAACkvwAAAAAAALo/AAAAAACAsT8AAAAAACDJPwAAAAAAAI6/AAAAAADAuj8AAAAAAACoPwAAAAAAYMQ/AAAAAABg0b8AAAAAAMC8vwAAAAAAALy/AAAAAAAAqz8AAAAAAGDFvwAAAAAAAJS/AAAAAACAob8AAAAAAMC7PwAAAAAAoM+/AAAAAABAs78AAAAAAECzvwAAAAAAQLQ/AAAAAABA1r8AAAAAAKDGvwAAAAAAYMS/AAAAAAAAiD8AAAAAAEDJvwAAAAAAAJu/AAAAAAAApL8AAAAAAMC7PwAAAAAAAIC/AAAAAABgwD8AAAAAAMC0PwAAAAAAoMk/AAAAAACAr78AAAAAAICzPwAAAAAAAKI/AAAAAAAAxD8AAAAAAABoPwAAAAAAQME/AAAAAADAtD8AAAAAAIDJPwAAAAAAAHC/AAAAAADAwD8AAAAAAMC1PwAAAAAAoMo/AAAAAAAAhj8AAAAAAMC/PwAAAAAAAK8/AAAAAAAAxj8AAAAAAFDUvwAAAAAAIMS/AAAAAABgw78AAAAAAACQPwAAAAAAoMu/AAAAAAAAsb8AAAAAAMCxvwAAAAAAwLQ/AAAAAABg1r8AAAAAAEDFvwAAAAAAIMO/AAAAAAAAlD8AAAAAAAC3vwAAAAAAgK8/AAAAAAAAmD8AAAAAAMDDPwAAAAAAwNO/AAAAAACgwb8AAAAAACDAvwAAAAAAgKM/AAAAAABQ2L8AAAAAAADIvwAAAAAAwMO/AAAAAAAAlT8AAAAAAIC6vwAAAAAAAK8/AAAAAACAoj8AAAAAAADGPwAAAAAAQLm/AAAAAACArD8AAAAAAACfPwAAAAAAwMU/AAAAAAAAhj8AAAAAAODBPwAAAAAAwLY/AAAAAACAyj8AAAAAAICpvwAAAAAAALU/AAAAAACApj8AAAAAAADGPwAAAAAAQLK/AAAAAACAsT8AAAAAAACfPwAAAAAAQMQ/AAAAAAAAdD8AAAAAAMDAPwAAAAAAwLQ/AAAAAACAyT8AAAAAAABgPwAAAAAAYME/AAAAAACAtz8AAAAAAIDLPwAAAAAAAJe/AAAAAACAuT8AAAAAAICkPwAAAAAA4MM/AAAAAAAA078AAAAAACDBvwAAAAAAgMC/AAAAAAAAmz8AAAAAAADKvwAAAAAAAKq/AAAAAACArr8AAAAAAIC2PwAAAAAAANa/AAAAAABAxL8AAAAAAODCvwAAAAAAAJU/AAAAAACAu78AAAAAAICnPwAAAAAAAI4/AAAAAADAwj8AAAAAAFDVvwAAAAAAwMS/AAAAAADAwr8AAAAAAACWPwAAAAAAkNm/AAAAAABAyb8AAAAAAKDEvwAAAAAAAJY/AAAAAAAAu78AAAAAAACvPwAAAAAAAKQ/AAAAAADAxj8AAAAAAEC6vwAAAAAAAKw/AAAAAACAoT8AAAAAAKDFPwAAAAAAAGA/AAAAAADgwD8AAAAAAAC0PwAAAAAAIMk/AAAAAAAApb8AAAAAAAC3PwAAAAAAAKY/AAAAAADgxD8AAAAAAACIPwAAAAAAwME/AAAAAAAAtT8AAAAAAGDJPwAAAAAAAJa/AAAAAAAAvT8AAAAAAMCwPwAAAAAAYMg/AAAAAAAAYD8AAAAAAEDAPwAAAAAAwLI/AAAAAABgyD8AAAAAAICyvwAAAAAAgKo/AAAAAAAAaD8AAAAAAEDAPwAAAAAAENO/AAAAAABgwb8AAAAAAKDAvwAAAAAAgKE/AAAAAAAgyb8AAAAAAACpvwAAAAAAgKy/AAAAAABAtz8AAAAAAMDWvwAAAAAAoMW/AAAAAACAw78AAAAAAACCPwAAAAAAwLi/AAAAAACAqj8AAAAAAACRPwAAAAAA4MI/AAAAAADQ1b8AAAAAAKDFvwAAAAAA4MO/AAAAAAAAfD8AAAAAAIDavwAAAAAAoMu/AAAAAACAxr8AAAAAAACAPwAAAAAAAMC/AAAAAACApD8AAAAAAACVPwAAAAAAgMQ/AAAAAAAA4L8AAAAAANDcvwAAAAAAYNa/AAAAAACAv78AAAAAAGDMvwAAAAAAgKC/AAAAAAAAmb8AAAAAAIDAPwAAAAAAIN+/AAAAAACQ1b8AAAAAAMDRvwAAAAAAwLK/AAAAAACgxr8AAAAAAABQvwAAAAAAAIa/AAAAAADgwD8AAAAAAADgvwAAAAAAAOC/AAAAAADg2r8AAAAAACDIvwAAAAAAUNW/AAAAAAAAwr8AAAAAAIC7vwAAAAAAAK8/AAAAAACgyb8AAAAAAACivwAAAAAAgK6/AAAAAADAtj8AAAAAAKDCvwAAAAAAAGi/AAAAAAAAlb8AAAAAAAC+PwAAAAAAgM+/AAAAAACAsr8AAAAAAICyvwAAAAAAQLY/AAAAAAAAwr8AAAAAAACCPwAAAAAAAIK/AAAAAABgwT8AAAAAAEC6vwAAAAAAAKM/AAAAAAAAiD8AAAAAAKDDPwAAAAAAgMa/AAAAAAAAmb8AAAAAAIChvwAAAAAAgL0/AAAAAAAAm78AAAAAAMC8PwAAAAAAwLE/AAAAAAAgyD8AAAAAAICkvwAAAAAAwLc/AAAAAAAAqj8AAAAAAMDFPwAAAAAAQLm/AAAAAACAqD8AAAAAAACOPwAAAAAA4MI/AAAAAAAAcD8AAAAAACDBPwAAAAAAALM/AAAAAADgyD8AAAAAAACvvwAAAAAAwLM/AAAAAAAAoj8AAAAAAODEPwAAAAAAwLG/AAAAAACArz8AAAAAAACYPwAAAAAAgMM/AAAAAACAzL8AAAAAAACwvwAAAAAAwLG/AAAAAADAtD8AAAAAAMDHvwAAAAAAAJi/AAAAAAAAn78AAAAAAEC/PwAAAAAAQLW/AAAAAADAsD8AAAAAAICjPwAAAAAAAMY/AAAAAABg2b8AAAAAAMDKvwAAAAAAYMa/AAAAAAAAgj8AAAAAAODLvwAAAAAAgK+/AAAAAABAtb8AAAAAAICuPwAAAAAAAOC/AAAAAABg178AAAAAAEDTvwAAAAAAgLi/AAAAAABw0L8AAAAAAAC0vwAAAAAAALa/AAAAAACAsj8AAAAAAAC9vwAAAAAAAJ4/AAAAAAAAaD8AAAAAACDCPwAAAAAAINu/AAAAAACgzr8AAAAAAODIvwAAAAAAAFC/AAAAAAAA4L8AAAAAAADgvwAAAAAAYN6/AAAAAACgzL8AAAAAAADgvwAAAAAAkN+/AAAAAAAg2r8AAAAAAKDGvwAAAAAAAOC/AAAAAADQ3r8AAAAAABDZvwAAAAAAIMW/AAAAAACg3r8AAAAAADDSvwAAAAAAoM2/AAAAAAAAo78AAAAAAKDMvwAAAAAAAKy/AAAAAACAqb8AAAAAAAC6PwAAAAAAoM6/AAAAAAAAsL8AAAAAAICuvwAAAAAAgLg/AAAAAAAAvL8AAAAAAICnPwAAAAAAAJE/AAAAAAAgxD8AAAAAAEDJvwAAAAAAgKS/AAAAAAAAqL8AAAAAAEC7PwAAAAAAAJO/AAAAAADAvj8AAAAAAEC0PwAAAAAAIMo/AAAAAACgzr8AAAAAAMC0vwAAAAAAwLK/AAAAAADAtj8AAAAAAIC/vwAAAAAAAJs/AAAAAAAAkT8AAAAAAEDEPwAAAAAAINC/AAAAAABAtL8AAAAAAACzvwAAAAAAwLQ/AAAAAAAAqb8AAAAAAEC5PwAAAAAAgLA/AAAAAACAyD8AAAAAAMCyvwAAAAAAwLI/AAAAAAAAoz8AAAAAAODFPwAAAAAAAKi/AAAAAABAtz8AAAAAAICtPwAAAAAAAMg/AAAAAAAgzr8AAAAAAICyvwAAAAAAgLO/AAAAAADAtT8AAAAAAICuvwAAAAAAALc/AAAAAAAArT8AAAAAAODHPwAAAAAAsNS/AAAAAACgxL8AAAAAACDBvwAAAAAAgKQ/AAAAAACAxL8AAAAAAABgvwAAAAAAAHi/AAAAAADgwT8AAAAAAADSvwAAAAAAQLm/AAAAAAAAtr8AAAAAAIC0PwAAAAAAgKm/AAAAAABAuT8AAAAAAACxPwAAAAAAYMg/AAAAAACAu78AAAAAAACoPwAAAAAAAJo/AAAAAAAAxT8AAAAAAACwvwAAAAAAwLQ/AAAAAACApz8AAAAAACDHPwAAAAAAwMu/AAAAAAAArb8AAAAAAICvvwAAAAAAgLg/AAAAAACAor8AAAAAAEC6PwAAAAAAwLA/AAAAAACgyD8AAAAAABDVvwAAAAAAoMO/AAAAAAAgwL8AAAAAAICpPwAAAAAAIMa/AAAAAAAAjr8AAAAAAACMvwAAAAAAgME/AAAAAADg0b8AAAAAAEC5vwAAAAAAALe/AAAAAAAAsz8AAAAAAACvvwAAAAAAQLc/AAAAAAAArD8AAAAAAODHPwAAAAAAgLO/AAAAAABAsj8AAAAAAACkPwAAAAAAgMU/AAAAAAAArr8AAAAAAIC1PwAAAAAAgKk/AAAAAAAgxz8AAAAAACDMvwAAAAAAgKm/AAAAAACArb8AAAAAAAC6PwAAAAAAoMG/AAAAAAAAnD8AAAAAAACKPwAAAAAAIMQ/AAAAAACArL8AAAAAAAC2PwAAAAAAgKs/AAAAAADAxz8AAAAAAACkPwAAAAAAgMU/AAAAAADAvD8AAAAAAGDNPwAAAAAAAJu/AAAAAADAvj8AAAAAAMC2PwAAAAAAYMw/AAAAAACAob8AAAAAAMC8PwAAAAAAQLU/AAAAAABgyz8AAAAAAACYPwAAAAAA4MM/AAAAAAAAuz8AAAAAAADNPwAAAAAAAHi/AAAAAABAwT8AAAAAAIC5PwAAAAAAYMw/AAAAAAAAlz8AAAAAAADCPwAAAAAAgLQ/AAAAAAAgyT8AAAAAAADRvwAAAAAAwLq/AAAAAAAAur8AAAAAAICwPwAAAAAAgMO/AAAAAAAAdL8AAAAAAACIvwAAAAAA4MA/AAAAAAAAy78AAAAAAICnvwAAAAAAAKm/AAAAAAAAuz8AAAAAAFDSvwAAAAAAgLy/AAAAAACAur8AAAAAAICvPwAAAAAAIMa/AAAAAAAAUL8AAAAAAACIvwAAAAAAAME/AAAAAAAAkj8AAAAAAODDPwAAAAAAALs/AAAAAAAAzD8AAAAAAACpvwAAAAAAQLc/AAAAAACAqz8AAAAAAGDHPwAAAAAAAIQ/AAAAAACAwj8AAAAAAEC3PwAAAAAAIMs/AAAAAAAAaL8AAAAAAGDBPwAAAAAAQLk/AAAAAADgzD8AAAAAAACUPwAAAAAAQME/AAAAAABAsT8AAAAAAEDHPwAAAAAAkNG/AAAAAAAAvb8AAAAAAEC7vwAAAAAAgKs/AAAAAABgwb8AAAAAAAB0PwAAAAAAAIS/AAAAAACAwD8AAAAAALDSvwAAAAAAgLy/AAAAAABAur8AAAAAAICuPwAAAAAAALK/AAAAAACAtD8AAAAAAICnPwAAAAAAgMY/AAAAAADAzL8AAAAAAICvvwAAAAAAgLC/AAAAAAAAtj8AAAAAAADTvwAAAAAAQLy/AAAAAAAAuL8AAAAAAACzPwAAAAAAALK/AAAAAABAtj8AAAAAAICsPwAAAAAAYMg/AAAAAAAAtb8AAAAAAACzPwAAAAAAAKw/AAAAAACAyD8AAAAAAACjPwAAAAAAwMQ/AAAAAABAuz8AAAAAAMDMPwAAAAAAgKC/AAAAAACAuj8AAAAAAECwPwAAAAAAQMg/AAAAAAAApr8AAAAAAMC2PwAAAAAAgKg/AAAAAABgxj8AAAAAAACdPwAAAAAAAMQ/AAAAAAAAuj8AAAAAAADMPwAAAAAAAJw/AAAAAACAxD8AAAAAAAC9PwAAAAAAgM0/AAAAAAAAiD8AAAAAAGDAPwAAAAAAwLA/AAAAAAAgxj8AAAAAAGDQvwAAAAAAQLm/AAAAAAAAur8AAAAAAICtPwAAAAAAQMS/AAAAAAAAkb8AAAAAAAChvwAAAAAAALw/AAAAAABw078AAAAAAADAvwAAAAAAQL2/AAAAAAAAqT8AAAAAAECyvwAAAAAAwLE/AAAAAACAoj8AAAAAAADFPwAAAAAAgNO/AAAAAADgwL8AAAAAAEC/vwAAAAAAgKY/AAAAAACA1r8AAAAAAODDvwAAAAAAQMC/AAAAAACAqT8AAAAAAAC1vwAAAAAAgLQ/AAAAAAAArT8AAAAAAGDIPwAAAAAAwLq/AAAAAAAAqz8AAAAAAACiPwAAAAAAYMY/AAAAAAAAeD8AAAAAAGDBPwAAAAAAALU/AAAAAABAyT8AAAAAAACovwAAAAAAQLY/AAAAAAAApj8AAAAAAKDFPwAAAAAAAI4/AAAAAABAwj8AAAAAAMC0PwAAAAAAgMk/AAAAAAAAkb8AAAAAAMC+PwAAAAAAQLQ/AAAAAADgyT8AAAAAAACAPwAAAAAAoMA/AAAAAABAsz8AAAAAAMDIPwAAAAAAALG/AAAAAAAAsD8AAAAAAACKPwAAAAAAgME/AAAAAACg078AAAAAAMDBvwAAAAAAoMC/AAAAAACAoT8AAAAAAIDIvwAAAAAAAKO/AAAAAAAAqL8AAAAAAAC4PwAAAAAAYNa/AAAAAACgxL8AAAAAAGDCvwAAAAAAAJg/AAAAAACAuL8AAAAAAACtPwAAAAAAAJY/AAAAAADAwj8AAAAAANDSvwAAAAAAAMC/AAAAAABAvr8AAAAAAACmPwAAAAAAINa/AAAAAACgw78AAAAAAMDAvwAAAAAAAKc/AAAAAACAu78AAAAAAICtPwAAAAAAgKM/AAAAAABgxj8AAAAAAADgvwAAAAAA0Nm/AAAAAAAQ1L8AAAAAAMC3vwAAAAAAQMm/AAAAAAAAeL8AAAAAAABwvwAAAAAAoMI/AAAAAADw3r8AAAAAANDUvwAAAAAAENG/AAAAAACAsL8AAAAAAIDEvwAAAAAAAIo/AAAAAAAAUD8AAAAAAKDBPwAAAAAAAOC/AAAAAADw378AAAAAAEDavwAAAAAAQMa/AAAAAABg1b8AAAAAAIDBvwAAAAAAgLy/AAAAAAAAsD8AAAAAAIDJvwAAAAAAAKC/AAAAAAAAq78AAAAAAIC4PwAAAAAAwMC/AAAAAAAAgD8AAAAAAACMvwAAAAAAAMA/AAAAAADgzr8AAAAAAICwvwAAAAAAQLC/AAAAAAAAuD8AAAAAAADBvwAAAAAAAIg/AAAAAAAAdL8AAAAAAADCPwAAAAAAwLi/AAAAAACAqD8AAAAAAACVPwAAAAAAQMQ/AAAAAABgxL8AAAAAAACCvwAAAAAAAJe/AAAAAAAAwD8AAAAAAACCvwAAAAAAwL8/AAAAAADAsz8AAAAAAODIPwAAAAAAAJ+/AAAAAADAuT8AAAAAAACsPwAAAAAA4MY/AAAAAAAAsr8AAAAAAECzPwAAAAAAAKE/AAAAAAAgxT8AAAAAAACbPwAAAAAAoMM/AAAAAADAuT8AAAAAAKDLPwAAAAAAAKa/AAAAAAAAtj8AAAAAAACnPwAAAAAAAMY/AAAAAAAAs78AAAAAAMCwPwAAAAAAAJk/AAAAAACgwz8AAAAAAIDMvwAAAAAAQLC/AAAAAADAsL8AAAAAAIC2PwAAAAAAgMS/AAAAAAAAAAAAAAAAAACKvwAAAAAAQME/AAAAAADAsr8AAAAAAECyPwAAAAAAgKY/AAAAAAAAxz8AAAAAAJDYvwAAAAAAQMm/AAAAAAAgxb8AAAAAAACCPwAAAAAAIMy/AAAAAACAr78AAAAAAMC1vwAAAAAAgLA/AAAAAAAA4L8AAAAAABDXvwAAAAAAgNO/AAAAAADAuL8AAAAAAJDQvwAAAAAAALS/AAAAAAAAtb8AAAAAAACzPwAAAAAAwLu/AAAAAAAAnT8AAAAAAABQPwAAAAAA4ME/AAAAAAAw278AAAAAAODNvwAAAAAAgMi/AAAAAAAAAAAAAAAAAADgvwAAAAAAAOC/AAAAAAAg3r8AAAAAACDMvwAAAAAAAOC/AAAAAABQ378AAAAAALDZvwAAAAAAIMa/AAAAAAAA4L8AAAAAALDevwAAAAAA4Ni/AAAAAACAxL8AAAAAAPDevwAAAAAAANO/AAAAAADgzr8AAAAAAICovwAAAAAAQM6/AAAAAABAsb8AAAAAAICuvwAAAAAAQLk/AAAAAACQ0L8AAAAAAAC0vwAAAAAAQLS/AAAAAAAAtT8AAAAAAEC/vwAAAAAAAKI/AAAAAAAAjj8AAAAAAODDPwAAAAAAwMq/AAAAAACArL8AAAAAAICtvwAAAAAAQLk/AAAAAACAor8AAAAAAEC8PwAAAAAAgLI/AAAAAACgyT8AAAAAAFDUvwAAAAAAYMO/AAAAAABAwL8AAAAAAICoPwAAAAAAYMK/AAAAAAAAfD8AAAAAAABwPwAAAAAAYMI/AAAAAACQ0L8AAAAAAAC1vwAAAAAAQLO/AAAAAAAAtj8AAAAAAAChvwAAAAAAAL0/AAAAAABAsj8AAAAAAADJPwAAAAAAgK6/AAAAAABAtT8AAAAAAICqPwAAAAAAoMc/AAAAAACApr8AAAAAAEC4PwAAAAAAAK0/AAAAAADgxz8AAAAAAADLvwAAAAAAgKq/AAAAAACArL8AAAAAAMC5PwAAAAAAgKK/AAAAAAAAuz8AAAAAAECxPwAAAAAAIMk/AAAAAACA1r8AAAAAACDHvwAAAAAAIMO/AAAAAAAAoD8AAAAAAKDFvwAAAAAAAIi/AAAAAAAAiL8AAAAAAIDBPwAAAAAA0NK/AAAAAADAu78AAAAAAIC3vwAAAAAAgLE/AAAAAACAqr8AAAAAAMC5PwAAAAAAgLE/AAAAAABAyT8AAAAAAEC5vwAAAAAAAKw/AAAAAAAAmD8AAAAAAODEPwAAAAAAAK2/AAAAAACAtj8AAAAAAACtPwAAAAAAYMg/AAAAAACAyL8AAAAAAACmvwAAAAAAgKq/AAAAAACAuj8AAAAAAAChvwAAAAAAwLw/AAAAAACAsj8AAAAAAKDJPwAAAAAAwNS/AAAAAADAw78AAAAAACDAvwAAAAAAAKo/AAAAAAAgxb8AAAAAAACCvwAAAAAAAHi/AAAAAABgwj8AAAAAAGDSvwAAAAAAwLq/AAAAAADAt78AAAAAAECyPwAAAAAAAKa/AAAAAACAuj8AAAAAAICxPwAAAAAAYMg/AAAAAABAsr8AAAAAAECzPwAAAAAAgKY/AAAAAADAxj8AAAAAAECwvwAAAAAAQLQ/AAAAAACApD8AAAAAAGDGPwAAAAAAwM6/AAAAAACAsb8AAAAAAMCwvwAAAAAAALk/AAAAAAAgxL8AAAAAAACEPwAAAAAAAFC/AAAAAADAwj8AAAAAAMCzvwAAAAAAwLI/AAAAAACApj8AAAAAAODGPwAAAAAAAJc/AAAAAADAwz8AAAAAAEC6PwAAAAAAgMw/AAAAAAAAlr8AAAAAAEDAPwAAAAAAQLg/AAAAAABgzD8AAAAAAACavwAAAAAAwL4/AAAAAAAAtz8AAAAAACDMPwAAAAAAgKI/AAAAAABAxT8AAAAAAIC9PwAAAAAAQM0/AAAAAAAAgL8AAAAAAEDBPwAAAAAAwLk/AAAAAAAAzT8AAAAAAACYPwAAAAAAQMI/AAAAAAAAtD8AAAAAAODIPwAAAAAAENC/AAAAAADAtr8AAAAAAEC1vwAAAAAAwLM/AAAAAAAAwr8AAAAAAABgPwAAAAAAAIC/AAAAAABgwT8AAAAAAODHvwAAAAAAAJS/AAAAAAAAnL8AAAAAAIC/PwAAAAAAENO/AAAAAADAv78AAAAAAAC9vwAAAAAAgKs/AAAAAACgxr8AAAAAAABwvwAAAAAAAJG/AAAAAAAAwD8AAAAAAACEPwAAAAAA4MI/AAAAAACAuT8AAAAAAGDMPwAAAAAAgKS/AAAAAAAAuT8AAAAAAICsPwAAAAAAYMc/AAAAAAAAoD8AAAAAAMDEPwAAAAAAALw/AAAAAAAgzT8AAAAAAACaPwAAAAAAgMQ/AAAAAAAAvT8AAAAAACDOPwAAAAAAAKE/AAAAAADAwj8AAAAAAAC1PwAAAAAAAMk/AAAAAACg0L8AAAAAAAC7vwAAAAAAALq/AAAAAAAArj8AAAAAAEDDvwAAAAAAAHi/AAAAAAAAk78AAAAAAMC/PwAAAAAAoNS/AAAAAACgwb8AAAAAAIC/vwAAAAAAgKc/AAAAAACAsr8AAAAAAEC0PwAAAAAAAKY/AAAAAACAxT8AAAAAAJDSvwAAAAAAQL6/AAAAAABAu78AAAAAAACtPwAAAAAAkNa/AAAAAACgxL8AAAAAAIDBvwAAAAAAAKU/AAAAAAAAt78AAAAAAACzPwAAAAAAgKs/AAAAAABgyD8AAAAAAMC2vwAAAAAAgLA/AAAAAAAApz8AAAAAAODHPwAAAAAAAJk/AAAAAADgwz8AAAAAAMC6PwAAAAAAgMw/AAAAAAAAoL8AAAAAAIC5PwAAAAAAAK8/AAAAAAAAyD8AAAAAAICqvwAAAAAAgLY/AAAAAACAqD8AAAAAAIDGPwAAAAAAAJc/AAAAAACAwz8AAAAAAAC6PwAAAAAA4Ms/AAAAAAAAmj8AAAAAAEDEPwAAAAAAwLw/AAAAAAAAzT8AAAAAAABQvwAAAAAAwL0/AAAAAACArT8AAAAAAEDGPwAAAAAAUNG/AAAAAACAvL8AAAAAAAC9vwAAAAAAgKk/AAAAAABgw78AAAAAAABwvwAAAAAAAJS/AAAAAABAvz8AAAAAADDTvwAAAAAAgMC/AAAAAACAvb8AAAAAAACpPwAAAAAAwLS/AAAAAADAsT8AAAAAAAChPwAAAAAA4MQ/AAAAAABQ1b8AAAAAAADEvwAAAAAAwMG/AAAAAAAAnj8AAAAAAMDXvwAAAAAAoMa/AAAAAAAgwr8AAAAAAACjPwAAAAAAQLa/AAAAAACAsz8AAAAAAICsPwAAAAAAgMg/AAAAAABAt78AAAAAAMCwPwAAAAAAgKc/AAAAAADAxj8AAAAAAACMPwAAAAAAIMI/AAAAAACAtj8AAAAAAGDKPwAAAAAAgKG/AAAAAACAuD8AAAAAAICnPwAAAAAA4MU/AAAAAAAAkT8AAAAAAGDCPwAAAAAAwLY/AAAAAAAgyj8AAAAAAACIvwAAAAAAgL4/AAAAAAAAtD8AAAAAACDKPwAAAAAAAI4/AAAAAADAwT8AAAAAAMC1PwAAAAAAoMk/AAAAAABAsL8AAAAAAACxPwAAAAAAAJE/AAAAAAAAwj8AAAAAAIDUvwAAAAAAIMS/AAAAAABAwr8AAAAAAACUPwAAAAAAwMu/AAAAAACAsL8AAAAAAECwvwAAAAAAALY/AAAAAAAQ178AAAAAAODFvwAAAAAAoMO/AAAAAAAAij8AAAAAAMC7vwAAAAAAAKg/AAAAAAAAjj8AAAAAAODCPwAAAAAAINO/AAAAAACAwL8AAAAAACDAvwAAAAAAAKI/AAAAAAAg2L8AAAAAAGDHvwAAAAAAIMO/AAAAAAAAoD8AAAAAAIC7vwAAAAAAAKo/AAAAAAAAoj8AAAAAAEDGPwAAAAAAAOC/AAAAAAAA3L8AAAAAALDVvwAAAAAAgLy/AAAAAABgy78AAAAAAACVvwAAAAAAAIq/AAAAAAAAwj8AAAAAAKDevwAAAAAAoNS/AAAAAADQ0L8AAAAAAACxvwAAAAAAgMS/AAAAAAAAjj8AAAAAAABoPwAAAAAAgMI/AAAAAAAA4L8AAAAAANDfvwAAAAAAYNq/AAAAAABAxr8AAAAAAFDUvwAAAAAAwL+/AAAAAAAAuL8AAAAAAMCyPwAAAAAAwMi/AAAAAAAAnr8AAAAAAACrvwAAAAAAQLg/AAAAAABAw78AAAAAAACCvwAAAAAAAJi/AAAAAABAvj8AAAAAAMDPvwAAAAAAALO/AAAAAADAsb8AAAAAAAC3PwAAAAAAQMG/AAAAAAAAkD8AAAAAAABovwAAAAAAYMI/AAAAAAAAub8AAAAAAACpPwAAAAAAAJc/AAAAAADgxD8AAAAAAKDEvwAAAAAAAIK/AAAAAAAAlr8AAAAAAMC/PwAAAAAAAIi/AAAAAABAwD8AAAAAAIC1PwAAAAAAYMo/AAAAAAAAq78AAAAAAAC1PwAAAAAAAJ8/AAAAAACAxD8AAAAAAECzvwAAAAAAQLI/AAAAAAAAoz8AAAAAAMDFPwAAAAAAAK6/AAAAAAAAtD8AAAAAAACoPwAAAAAA4MY/AAAAAAAAkz8AAAAAAGDCPwAAAAAAALY/AAAAAADgyT8AAAAAAACsvwAAAAAAgLU/AAAAAAAAqD8AAAAAAIDGPwAAAAAAAKO/AAAAAAAAuT8AAAAAAICpPwAAAAAAgMY/AAAAAADgwL8AAAAAAACWPwAAAAAAAHC/AAAAAABAwT8AAAAAAABgPwAAAAAAIME/AAAAAACAtT8AAAAAACDJPwAAAAAAgNK/AAAAAAAAwb8AAAAAAEC/vwAAAAAAgKY/AAAAAAAAxr8AAAAAAACZvwAAAAAAgKK/AAAAAACAvD8AAAAAAIDWvwAAAAAAIMW/AAAAAADgwr8AAAAAAACXPwAAAAAAwLa/AAAAAACArD8AAAAAAACYPwAAAAAAwMM/AAAAAACA078AAAAAAEDCvwAAAAAAgMC/AAAAAAAApD8AAAAAAIDLvwAAAAAAAKy/AAAAAAAArL8AAAAAAMC4PwAAAAAAwNO/AAAAAACAv78AAAAAAEC8vwAAAAAAgKk/AAAAAAAAy78AAAAAAAClvwAAAAAAgKq/AAAAAADAuD8AAAAAAACYvwAAAAAAgL0/AAAAAACAsT8AAAAAAKDHPwAAAAAAcNW/AAAAAACAxb8AAAAAAIDCvwAAAAAAAJ8/AAAAAAAgyr8AAAAAAICnvwAAAAAAgKi/AAAAAADAuj8AAAAAAADVvwAAAAAAgMK/AAAAAACgwL8AAAAAAICiPwAAAAAAwLS/AAAAAACAsD8AAAAAAACePwAAAAAAoMQ/AAAAAAAAr78AAAAAAMC0PwAAAAAAAKU/AAAAAACAxT8AAAAAAACRPwAAAAAAgMI/AAAAAABAtz8AAAAAAKDKPwAAAAAAENK/AAAAAACgwL8AAAAAAAC/vwAAAAAAgKM/AAAAAABgxb8AAAAAAACQvwAAAAAAAJy/AAAAAADAvT8AAAAAADDTvwAAAAAAgL6/AAAAAABAvL8AAAAAAACqPwAAAAAAgLq/AAAAAAAAqD8AAAAAAACGPwAAAAAAgMI/AAAAAABAtL8AAAAAAMCxPwAAAAAAAKI/AAAAAABgxT8AAAAAAACRvwAAAAAAgL0/AAAAAADAsD8AAAAAAADIPwAAAAAAALS/AAAAAACAqz8AAAAAAACKPwAAAAAAYMI/AAAAAADAzr8AAAAAAACyvwAAAAAAwLG/AAAAAADAtT8AAAAAAKDGvwAAAAAAAJS/AAAAAAAAoL8AAAAAAAC+PwAAAAAAgLe/AAAAAAAAqz8AAAAAAACYPwAAAAAAoMM/AAAAAADA0r8AAAAAAGDAvwAAAAAAwL+/AAAAAACApD8AAAAAAIC9vwAAAAAAgKc/AAAAAAAAkj8AAAAAAADEPwAAAAAAAKq/AAAAAACAtj8AAAAAAICoPwAAAAAAgMY/AAAAAACAub8AAAAAAACkPwAAAAAAAIQ/AAAAAADgwT8AAAAAAACMvwAAAAAAwL4/AAAAAAAAsz8AAAAAAODIPwAAAAAAAKw/AAAAAADgxT8AAAAAAAC+PwAAAAAAYM0/AAAAAAAAgr8AAAAAAIC9PwAAAAAAgLA/AAAAAADAxz8AAAAAAACuvwAAAAAAwLQ/AAAAAAAApT8AAAAAAGDFPwAAAAAAAKM/AAAAAACAxT8AAAAAAEC/PwAAAAAAwM0/AAAAAAAAnb8AAAAAAIC5PwAAAAAAAKo/AAAAAAAAxj8AAAAAAACePwAAAAAA4MM/AAAAAAAAuT8AAAAAAGDLPwAAAAAAAAAAAAAAAADAvz8AAAAAAICyPwAAAAAAQMg/AAAAAAAAu78AAAAAAACfPwAAAAAAAHC/AAAAAACAwD8AAAAAAABwvwAAAAAAgL8/AAAAAADAsT8AAAAAAODHPwAAAAAAAIw/AAAAAABgwT8AAAAAAMC0PwAAAAAAIMk/AAAAAAAAkb8AAAAAAIC6PwAAAAAAgKk/AAAAAAAAxT8AAAAAAADNvwAAAAAAQLG/AAAAAACAsr8AAAAAAAC0PwAAAAAA4MK/AAAAAAAAcL8AAAAAAACdvwAAAAAAQLs/AAAAAADAv78AAAAAAACRPwAAAAAAAIi/AAAAAAAAwD8AAAAAAEDFvwAAAAAAAJa/AAAAAACAqL8AAAAAAMC4PwAAAAAAQLC/AAAAAACAtD8AAAAAAAClPwAAAAAAYMU/AAAAAABAuL8AAAAAAACiPwAAAAAAAHi/AAAAAABAvz8AAAAAAADXvwAAAAAAwMe/AAAAAADAxb8AAAAAAAAAAAAAAAAAgMy/AAAAAACAsL8AAAAAAICxvwAAAAAAwLQ/AAAAAACQ1L8AAAAAAODBvwAAAAAAoMC/AAAAAAAAmT8AAAAAAEC/vwAAAAAAAJ0/AAAAAAAAdL8AAAAAAGDAPwAAAAAAALu/AAAAAAAApj8AAAAAAACCPwAAAAAAwME/AAAAAADw0L8AAAAAAEC6vwAAAAAAwLu/AAAAAACApz8AAAAAAKDXvwAAAAAAgMi/AAAAAACgx78AAAAAAACRvwAAAAAAgNa/AAAAAACgxr8AAAAAAEDFvwAAAAAAAFC/AAAAAADgyb8AAAAAAACtvwAAAAAAQLG/AAAAAADAsz8AAAAAAFDRvwAAAAAAgLi/AAAAAAAAuL8AAAAAAICvPwAAAAAAENW/AAAAAADgw78AAAAAAADCvwAAAAAAAJs/AAAAAABAyL8AAAAAAACjvwAAAAAAgKa/AAAAAACAuD8AAAAAAIDTvwAAAAAAoMC/AAAAAABAwL8AAAAAAAChPwAAAAAAwMW/AAAAAAAAcL8AAAAAAACdvwAAAAAAwL0/AAAAAADAsr8AAAAAAMCxPwAAAAAAAJw/AAAAAADgwz8AAAAAAIC0vwAAAAAAAKw/AAAAAAAAgD8AAAAAAGDBPwAAAAAAAMy/AAAAAAAArL8AAAAAAACuvwAAAAAAQLc/AAAAAACAxb8AAAAAAACRvwAAAAAAgKG/AAAAAADAvD8AAAAAAIC5vwAAAAAAAKc/AAAAAAAAjD8AAAAAACDDPwAAAAAAQNO/AAAAAAAgwb8AAAAAAADBvwAAAAAAAKA/AAAAAABAvr8AAAAAAIClPwAAAAAAAJA/AAAAAADAwj8AAAAAAICxvwAAAAAAgLI/AAAAAAAAoT8AAAAAAKDEPwAAAAAAgMC/AAAAAAAAlD8AAAAAAACRvwAAAAAAAL8/AAAAAACApb8AAAAAAMC4PwAAAAAAgKo/AAAAAABgxj8AAAAAAAClPwAAAAAAQMQ/AAAAAAAAuj8AAAAAAGDLPwAAAAAAAJe/AAAAAAAAuj8AAAAAAACrPwAAAAAAQMY/AAAAAACAs78AAAAAAICvPwAAAAAAAJo/AAAAAACAwz8AAAAAAACcPwAAAAAAQMQ/AAAAAABAvD8AAAAAAADNPwAAAAAAgKG/AAAAAACAtz8AAAAAAACmPwAAAAAAQMU/AAAAAAAAlz8AAAAAAADDPwAAAAAAALk/AAAAAABgyj8AAAAAAACRvwAAAAAAALw/AAAAAACArT8AAAAAAMDGPwAAAAAAgLy/AAAAAAAAnT8AAAAAAACKvwAAAAAAAL8/AAAAAAAAaL8AAAAAAIC/PwAAAAAAALE/AAAAAABAxz8AAAAAAACOPwAAAAAAoMA/AAAAAABAsz8AAAAAACDIPwAAAAAAAKC/AAAAAACAtz8AAAAAAICiPwAAAAAA4MM/AAAAAAAgz78AAAAAAAC1vwAAAAAAQLa/AAAAAADAsD8AAAAAAEDFvwAAAAAAAJW/AAAAAAAAp78AAAAAAMC3PwAAAAAAwMG/AAAAAAAAaD8AAAAAAACZvwAAAAAAAL0/AAAAAACAyL8AAAAAAICmvwAAAAAAQLC/AAAAAAAAtD8AAAAAAEC0vwAAAAAAALE/AAAAAAAAnT8AAAAAAADEPwAAAAAAwLS/AAAAAACAqj8AAAAAAABoPwAAAAAAYMA/AAAAAADgwr8AAAAAAABgPwAAAAAAAJu/AAAAAACAvD8AAAAAAIDPvwAAAAAAwLm/AAAAAAAAu78AAAAAAICpPwAAAAAAgMe/AAAAAACApb8AAAAAAACsvwAAAAAAwLY/AAAAAACA0r8AAAAAAEC+vwAAAAAAAL6/AAAAAACApD8AAAAAAMC9vwAAAAAAAJ8/AAAAAAAAdL8AAAAAAIC9PwAAAAAAQMC/AAAAAAAAkj8AAAAAAACTvwAAAAAAAL0/AAAAAAAgxb8AAAAAAACCvwAAAAAAAKK/AAAAAACAuj8AAAAAAACSvwAAAAAAQL0/AAAAAAAAsD8AAAAAAEDHPwAAAAAAgNK/AAAAAADAv78AAAAAAKDAvwAAAAAAAJs/AAAAAAAQ2b8AAAAAAEDLvwAAAAAAwMm/AAAAAAAAn78AAAAAANDYvwAAAAAAIMy/AAAAAADgyb8AAAAAAICgvwAAAAAA4M2/AAAAAABAtb8AAAAAAIC3vwAAAAAAAKw/AAAAAAAA1L8AAAAAAODAvwAAAAAAQMC/AAAAAACAoT8AAAAAAODUvwAAAAAAAMS/AAAAAADAwr8AAAAAAACQPwAAAAAAQM6/AAAAAADAtL8AAAAAAIC0vwAAAAAAALI/AAAAAACQ1L8AAAAAAGDCvwAAAAAAoMK/AAAAAAAAkT8AAAAAAMDJvwAAAAAAAKC/AAAAAAAAp78AAAAAAMC5PwAAAAAAgLW/AAAAAAAAqz8AAAAAAACKPwAAAAAAIMI/AAAAAABAt78AAAAAAICnPwAAAAAAAAAAAAAAAABgwD8AAAAAACDOvwAAAAAAALS/AAAAAABAtL8AAAAAAECzPwAAAAAA4Ma/AAAAAAAAmr8AAAAAAACmvwAAAAAAwLo/AAAAAABAv78AAAAAAACaPwAAAAAAAHC/AAAAAABAwT8AAAAAALDUvwAAAAAAAMS/AAAAAAAgw78AAAAAAACEPwAAAAAAwMG/AAAAAAAAmj8AAAAAAAAAAAAAAAAAwME/AAAAAABAs78AAAAAAACxPwAAAAAAAJY/AAAAAABgwz8AAAAAAEDBvwAAAAAAAJA/AAAAAAAAkL8AAAAAAMC+PwAAAAAAAKG/AAAAAACAuD8AAAAAAICpPwAAAAAAIMY/AAAAAACApD8AAAAAAODEPwAAAAAAALs/AAAAAACgyz8AAAAAAICovwAAAAAAwLQ/AAAAAAAAoD8AAAAAAADEPwAAAAAAgLu/AAAAAACAoz8AAAAAAABQPwAAAAAAAME/AAAAAABAt78AAAAAAICuPwAAAAAAAKE/AAAAAABAxT8AAAAAAIDCvwAAAAAAAHA/AAAAAAAAnL8AAAAAAIC7PwAAAAAAAJi/AAAAAABAvT8AAAAAAMCxPwAAAAAAYMg/AAAAAAAAnL8AAAAAAMC5PwAAAAAAAKc/AAAAAABgxT8AAAAAAMC/vwAAAAAAAJU/AAAAAAAAjr8AAAAAAMC9PwAAAAAAAHi/AAAAAADAvT8AAAAAAICvPwAAAAAAgMY/AAAAAAAAgD8AAAAAAODAPwAAAAAAwLI/AAAAAAAAyD8AAAAAAICivwAAAAAAQLY/AAAAAAAAoD8AAAAAAIDDPwAAAAAAwM6/AAAAAACAtL8AAAAAAEC2vwAAAAAAAK8/AAAAAAAAxb8AAAAAAACUvwAAAAAAAKa/AAAAAACAuT8AAAAAACDBvwAAAAAAAHg/AAAAAAAAm78AAAAAAAC8PwAAAAAAoMe/AAAAAAAApL8AAAAAAACuvwAAAAAAALY/AAAAAACAsL8AAAAAAAC0PwAAAAAAgKA/AAAAAAAgxD8AAAAAAEC1vwAAAAAAgKk/AAAAAAAAaD8AAAAAAGDAPwAAAAAAwMO/AAAAAAAAgL8AAAAAAICivwAAAAAAwLk/AAAAAABg0b8AAAAAAAC9vwAAAAAAAL6/AAAAAAAApD8AAAAAAADNvwAAAAAAALO/AAAAAAAAtL8AAAAAAACyPwAAAAAAQNW/AAAAAABAw78AAAAAAADCvwAAAAAAAJA/AAAAAADAv78AAAAAAACbPwAAAAAAAIK/AAAAAABAvz8AAAAAAEC9vwAAAAAAAJs/AAAAAAAAkb8AAAAAAAC+PwAAAAAAAMa/AAAAAAAAjr8AAAAAAICivwAAAAAAwLo/AAAAAAAApr8AAAAAAMC2PwAAAAAAAKY/AAAAAAAgxT8AAAAAAKDNvwAAAAAAALS/AAAAAACAuL8AAAAAAICpPwAAAAAAENm/AAAAAAAAzL8AAAAAAADKvwAAAAAAAKC/AAAAAACAzr8AAAAAAIC0vwAAAAAAQLa/AAAAAACArj8AAAAAAGDTvwAAAAAAIMC/AAAAAAAAwL8AAAAAAACiPwAAAAAAwNa/AAAAAABAx78AAAAAACDFvwAAAAAAAFA/AAAAAAAgzr8AAAAAAIC0vwAAAAAAgLS/AAAAAACAsT8AAAAAANDUvwAAAAAAAMO/AAAAAAAgw78AAAAAAACKPwAAAAAAoMe/AAAAAAAAk78AAAAAAICivwAAAAAAQLs/AAAAAACAtL8AAAAAAICrPwAAAAAAAIo/AAAAAADgwT8AAAAAAEC9vwAAAAAAAJo/AAAAAAAAkb8AAAAAAAC9PwAAAAAA4NC/AAAAAACAuL8AAAAAAEC4vwAAAAAAgK8/AAAAAABgyL8AAAAAAACivwAAAAAAgKm/AAAAAACAuD8AAAAAAEC8vwAAAAAAgKE/AAAAAAAAUD8AAAAAAMDBPwAAAAAAwNO/AAAAAACgwr8AAAAAAIDCvwAAAAAAAIw/AAAAAACgwr8AAAAAAACSPwAAAAAAAHy/AAAAAADAwD8AAAAAAMC1vwAAAAAAgK0/AAAAAAAAij8AAAAAAEDCPwAAAAAAAMG/AAAAAAAAjj8AAAAAAACRvwAAAAAAAL4/AAAAAAAApb8AAAAAAAC3PwAAAAAAAKY/AAAAAABAxT8AAAAAAACaPwAAAAAAQMM/AAAAAAAAuD8AAAAAAGDKPwAAAAAAgKi/AAAAAADAtD8AAAAAAICgPwAAAAAAwMM/AAAAAABAt78AAAAAAACqPwAAAAAAAIg/AAAAAAAAwT8AAAAAAMCyvwAAAAAAwLI/AAAAAAAApT8AAAAAAODFPwAAAAAAgMK/AAAAAAAAAAAAAAAAAACevwAAAAAAwLk/AAAAAAAAnL8AAAAAAAC8PwAAAAAAQLA/AAAAAACgxz8AAAAAAACdvwAAAAAAALk/AAAAAAAApT8AAAAAAMDEPwAAAAAAgMG/AAAAAAAAgD8AAAAAAACavwAAAAAAgLs/AAAAAAAAn78AAAAAAEC4PwAAAAAAAKY/AAAAAADAxD8AAAAAAACAvwAAAAAAAL4/AAAAAACArz8AAAAAAKDGPwAAAAAAgKG/AAAAAADAtT8AAAAAAICgPwAAAAAAQMM/AAAAAACAzr8AAAAAAIC0vwAAAAAAwLa/AAAAAACArT8AAAAAAKDFvwAAAAAAAJm/AAAAAAAAqb8AAAAAAMC3PwAAAAAAIMC/AAAAAAAAhj8AAAAAAACcvwAAAAAAALw/AAAAAACAxr8AAAAAAACivwAAAAAAAK2/AAAAAABAtj8AAAAAAECwvwAAAAAAgLI/AAAAAAAAnj8AAAAAAKDDPwAAAAAAwLe/AAAAAAAApj8AAAAAAABgPwAAAAAAYMA/AAAAAACgxb8AAAAAAACWvwAAAAAAgKi/AAAAAADAtz8AAAAAABDSvwAAAAAAYMC/AAAAAABAwL8AAAAAAICgPwAAAAAAAMq/AAAAAAAArr8AAAAAAMCwvwAAAAAAwLM/AAAAAAAA1r8AAAAAACDFvwAAAAAAIMS/AAAAAAAAUL8AAAAAACDCvwAAAAAAAIY/AAAAAAAAmr8AAAAAAAC8PwAAAAAAIMK/AAAAAAAAfD8AAAAAAACdvwAAAAAAALs/AAAAAAAgyL8AAAAAAACgvwAAAAAAgKy/AAAAAADAtT8AAAAAAMCyvwAAAAAAgK4/AAAAAAAAkj8AAAAAAGDCPwAAAAAAgNO/AAAAAABgwb8AAAAAACDBvwAAAAAAAJo/AAAAAACA178AAAAAAEDIvwAAAAAAQMa/AAAAAAAAdL8AAAAAAODDvwAAAAAAAIA/AAAAAAAAkb8AAAAAAAC/PwAAAAAAIMO/AAAAAAAAiD8AAAAAAABwvwAAAAAAgME/AAAAAAAAp78AAAAAAAC3PwAAAAAAAKc/AAAAAACgxD8AAAAAAACMvwAAAAAAgL4/AAAAAADAsj8AAAAAAODIPwAAAAAAAHy/AAAAAADAvD8AAAAAAICoPwAAAAAA4MQ/AAAAAAAgwL8AAAAAAACRPwAAAAAAAIy/AAAAAADAvT8AAAAAACDRvwAAAAAAQL6/AAAAAAAAv78AAAAAAACjPwAAAAAAgMu/AAAAAAAAsb8AAAAAAICyvw==","dtype":"float64","shape":[5000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1040","type":"Title"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"e2ee42af-5ba5-4062-a133-3ad1aa4dacf8","roots":{"1002":"cb41a438-710e-4a12-8442-280d2bd4442c"}}];
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

    

    <div id="4522d19d-9b2d-4208-8e4f-710a9d4eff9e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4522d19d-9b2d-4208-8e4f-710a9d4eff9e');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="e9f25480-4a0d-4f23-bdcf-b4335d2a4e50" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="06b8997a-ba52-4dd2-9280-e73dc7debedf"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#06b8997a-ba52-4dd2-9280-e73dc7debedf');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"a59f4437-af20-42cd-944a-5ed229750cd8":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1154","type":"BasicTickFormatter"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{},"id":"1155","type":"Selection"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"formatter":{"id":"1152","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{"formatter":{"id":"1154","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1155","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1152","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"a59f4437-af20-42cd-944a-5ed229750cd8","roots":{"1103":"e9f25480-4a0d-4f23-bdcf-b4335d2a4e50"}}];
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

