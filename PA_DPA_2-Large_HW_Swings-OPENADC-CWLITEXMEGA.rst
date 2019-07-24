
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep \|\| exit 0
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

    

    <div id="4525224c-af46-4d63-a756-abc017fd1d2e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4525224c-af46-4d63-a756-abc017fd1d2e');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="ce07eddd-2164-45f4-9502-d0795caed4a8" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="b02f9f7b-c3cf-432f-bdd7-9454aaa6ca35"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b02f9f7b-c3cf-432f-bdd7-9454aaa6ca35');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"6e7d3f98-9311-4965-8887-8cc7b0f27ad1":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"6e7d3f98-9311-4965-8887-8cc7b0f27ad1","roots":{"1002":"ce07eddd-2164-45f4-9502-d0795caed4a8"}}];
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

    

    <div id="61a9a652-caec-48d1-81cb-4570b025952d"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#61a9a652-caec-48d1-81cb-4570b025952d');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="bf399b80-f9fd-457e-8bff-7829ed7c1bd3" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="ab890d66-79d0-4fab-8ee0-34139bacb062"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ab890d66-79d0-4fab-8ee0-34139bacb062');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"0628e1ca-c6a5-4660-a5c4-1ab4106348ee":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"formatter":{"id":"1154","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1154","type":"BasicTickFormatter"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"overlay":{"id":"1155","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1157","type":"Selection"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1152","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1155","type":"BoxAnnotation"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"ABERERERQb8AZGZmZmY2vwAjIiIiIkq/AFBVVVVVFT8AAAAAAABQvwAAAAAAAEg/AFZVVVVVNT8A7+7u7u5WPwARERERESG/AM3MzMzMTD9AMzMzMzNDPwAQEREREQE/gHd3d3d3Tz8Adnd3d3dPvwAwMzMzMzM/AM3MzMzMUL8gIiIiIiJSvwDv7u7u7lq/wLu7u7u7W78AzczMzMxEv0BERERERFS/ACIiIiIiUr+4u7u7u7tTv+Dd3d3d3Vm/IDMzMzMzYb8ARERERERqv+Du7u7u7mS/QBERERERZb/Au7u7u7tfv4CIiIiIiGa/0MzMzMzMZL8QERERERFjvwAAAAAAAFS/QDMzMzMzZb+oqqqqqqpivxAREREREWO/wLu7u7u7U78AiYiIiIhUvwAREREREVW/ADIzMzMzM7/Au7u7u7tDvwAQERERESG/ADMzMzMzMz8AVlVVVVVNPwCamZmZmQk/ABERERERUb8AmZmZmZkpvwCrqqqqqkq/AKqqqqqqOr8AiIiIiIg4P4BmZmZmZmI/ALy7u7u7Kz+AmZmZmZkJPwAAAAAAAEA/QEREREREND9AVVVVVVVVP4AiIiIiIkK/ADQzMzMzQz8A3N3d3d09vwAAAAAAAEi/AAAAAAAAWL8AIyIiIiJCvwAREREREUE/ALy7u7u7Q78AmpmZmZkpv4CIiIiIiGC/REREREREXL9IRERERERgv0BVVVVVVVG/AFRVVVVVNT8AIiIiIiJCvwAREREREUG/QCIiIiIiSr8AERERERFBP4B3d3d3d0e/ALy7u7u7Kz+AmZmZmZlJvwDMzMzMzDw/gGZmZmZmNr9gVVVVVVUlPwC8u7u7uzu/AFRVVVVVFT+Ad3d3d3dPP+Dd3d3d3S0/AEREREREUD8AmJmZmZkJv4CZmZmZmTk/MDMzMzMzIz8AREREREREvwDv7u7u7j6/YHd3d3d3W7/f3d3d3d0tvwB3d3d3d0+/AKCZmZmZCb9gVVVVVVVVvyAiIiIiIl6/ABERERERXb+AVVVVVVVjv3h3d3d3d1u/4N3d3d3dYb8A3t3d3d1RvwAAAAAAAAAAEBERERERUb/g3d3d3d1Nv4BVVVVVVUW/QBERERERYz8AAAAAAABIPxAREREREVE/AJmZmZmZOT8AAAAAAABiP6CZmZmZmVU/EBERERERUT8AIiIiIiJKPwDv7u7u7kY/AAAAAAAAQD8AEhERERERv4CIiIiIiEC/AODd3d3dLb+AVVVVVVUlP4CZmZmZmUG/ADMzMzMzQ7+AZmZmZmZgvxAREREREVm/IBERERERXb/Au7u7u7thvwDe3d3d3UU/UFVVVVVVRb+AmZmZmZkZvwBWVVVVVTW/ABERERERXT9gVVVVVVVFPxARERERESG/AKCZmZmZCT+AqqqqqqpWP8DMzMzMzFQ/oJmZmZmZCb8AVVVVVVVVPwAAAAAAAAAAALy7u7u7Oz8AkJmZmZkZPwBmZmZmZja/AAAAAAAAAAAAd3d3d3dTvwDd3d3d3VG/gEREREREWL8AAAAAAAAAAAAAAAAAAAAAAERERERERL8AEBERERERPwDMzMzMzDy/AM3MzMzMPL+AMzMzMzNLvwBmZmZmZk6/AAAAAAAAAAAANDMzMzNLvwBWVVVVVV2/gEREREREWL8AAAAAAABiv2BVVVVVVWO/AAAAAAAAYr9gd3d3d3djvwAREREREVW/AAAAAAAAbr8A3t3d3d1lv4B3d3d3d2O/gJmZmZmZXb+IiIiIiIhYv5iZmZmZmVm/AAAAAAAASL8AAAAAAAAAAADe3d3d3U2/AN7d3d3dUb8AvLu7u7s7PwAzMzMzM0O/wMzMzMzMVL+AmZmZmZlJv4CqqqqqqlK/QGZmZmZmYD9ERERERERMP3B3d3d3d1s/AJiZmZmZGT8AzczMzMw8PwAzMzMzM0O/gHd3d3d3N78AvLu7u7tLP8C7u7u7uzu/AKqqqqqqOr8ANDMzMzMzvwB4d3d3d0e/MDMzMzMzU7/AzMzMzMxQvwDe3d3d3R2/AMzMzMzMPL8AAAAAAAAAAACJiIiIiEC/AHh3d3d3J78AVlVVVVVNv3h3d3d3d2E/AAAAAAAAUD+Au7u7u7tDPwAiIiIiIjI/vru7u7u7ZT8A3t3d3d1ZPwDe3d3d3U0/gN3d3d3dUT+AZmZmZmZGPwAREREREUE/AN7d3d3dPT8Ad3d3d3dPPwA0MzMzM0M/ALy7u7u7Q78AEhERERExP4CIiIiIiEi/4N3d3d0dlT8AAAAAAECSP/Du7u7ubo4/ODMzMzMzhj8AqKqqqqo6vwDg3d3d3R2/ABARERERAb+AiIiIiIhUP6yqqqqqiqo/NDMzMzNzpj9mZmZmZoagP/z//////5g/ICIiIiLCoj9ERERERESePxARERERkZc/eHd3d3c3kT9gVVVVVVWQP4B3d3d394g/ICIiIiKigj+YmZmZmZl6P4B3d3d3N5M/4N3d3d1djz9QVVVVVdWFP8jMzMzMzH8/oJmZmZmZeT/AzMzMzMxuP1BVVVVVVWE/AO/u7u7uYj/g3d3d3d1hP4B3d3d3d0c/gBERERERMT8AEhERERExP2hmZmZmZja/gHd3d3d3W7+AiIiIiIhIv4C7u7u7u1u/ICIiIiIiZL9gZmZmZmZiv+Dd3d3d3V2/gGZmZmZmYr8QERERERFjv4B3d3d3d1+/QEREREREYr+Au7u7u7tbv6iqqqqqqmy/gIiIiIiIWL+AiIiIiIhqvwAAAAAAAGq/AAAAAAAAYr8A7+7u7u5kv8C7u7u7u1e/gIiIiIiIYL9QVVVVVVVVvwAiIiIiImK/gIiIiIiIar9AZmZmZmZmvwAiIiIiImI/YEREREREaj+AqqqqqqpKP2BEREREREw/AO7u7u7uPj/AqqqqqqpCvyAzMzMzM1e/ADMzMzMzQ78A7+7u7u5GPwDg3d3d3R2/gMzMzMzMRL8AIiIiIiJSvwDe3d3d3U2/QEREREREYr/g3d3d3d1Fv0BERERERFi/AJqZmZmZQb8AmpmZmZlJv4BERERERES/oJmZmZmZXb8A4N3d3d0dvwA0MzMzMzM/ACIiIiIiQr8AIiIiIiJKvwAiIiIiIkI/AJqZmZmZWT8A3t3d3d0tvyAzMzMzM1O/AAAAAAAAMD/AqqqqqqpKv4CIiIiIiDi/gIiIiIiIUL+AzMzMzMw8v8CZmZmZmVW/AAAAAAAAZL+AZmZmZmZevzMzMzMzM2G/AHd3d3d3T7+AVVVVVVVZvwDd3d3d3U2/wLu7u7u7X79gVVVVVVVhv6CZmZmZmWO/QFVVVVVVYb+EiIiIiIhovwDe3d3d3W2/gIiIiIiIZr/A3d3d3d1jv+Dd3d3d3WW/4N3d3d3dZb+AmZmZmZlhv0AiIiIiImC/VVVVVVVVY79ARERERERgv0AzMzMzM1e/ALy7u7u7X79ARERERERcvwCamZmZmUm/wN3d3d3dVb8A7+7u7u5SvwDv7u7u7mY/wKqqqqqqaj/AzMzMzMxYP8Cqqqqqqko/ABERERERST+AiIiIiIg4vwBVVVVVVTU/ADMzMzMzM78AERERERFwP2BVVVVVVWM/AAAAAAAAVD/AqqqqqqpKP8C7u7u7u22/AAAAAAAAYr9gZmZmZmZqv4CZmZmZmV2/gIiIiIiIYL+AmZmZmZlVv0AREREREVm/4N3d3d3dWb+Au7u7u7tpP0AiIiIiIl4/gKqqqqqqWj8AAAAAAABAPwAjIiIiIla/gKqqqqqqUr+Au7u7u7tLvwDNzMzMzDy/wKqqqqqqXr8A3t3d3d1Vv/Du7u7u7l6/gHd3d3d3W78gIiIiIiJav4C7u7u7u0O/gHd3d3d3N78AVVVVVVVNv4CZmZmZmTm/AGZmZmZmNr9ARERERERUvwDv7u7u7l6/ABERERERUT8AEBERERHxPgAAAAAAAEC/AHh3d3d3J78A7+7u7u4+vwDe3d3d3VG/ACIiIiIiWr8AeHd3d3dPvyAiIiIiImS/QDMzMzMzV79gZmZmZmZgvwDNzMzMzES/vLu7u7u7Yb8AAAAAAABiv0AzMzMzM1+/AO/u7u7uWr/c3d3d3d1Nv4BmZmZmZka/ADIzMzMzIz8AMzMzMzNTvwDNzMzMzGo/YGZmZmZmbj/Au7u7u7tfP7CqqqqqqmY/YEREREREcD8AAAAAAABwP8DMzMzMzGA/wLu7u7u7Vz+AmZmZmZljPyAREREREWc/gHd3d3d3ZT8AERERERFVPwBVVVVVVVm/gHd3d3d3V78gIiIiIiJgvwAAAAAAAFy/AAAAAAAASD9AIiIiIiJSPwAQERERERG/ABARERERIT8A3t3d3d1FPwCIiIiIiDg/gHd3d3d3U78AERERERExPwDu7u7u7k4/wJmZmZmZWT+AiIiIiIhIP4CIiIiIiEA/QEREREREWL9gVVVVVVVlv/Du7u7u7l6/QEREREREZL/g3d3d3d1dv4CIiIiIiFy/ICIiIiIiQr+AIiIiIiJev0BEREREREy/gHd3d3d3R7/Au7u7u7tTvwDw7u7u7j6/sKqqqqqqZr8A3t3d3d1RvwAAAAAAAGK/wKqqqqqqYL9WVVVVVVVNv4BVVVVVVVG/AAAAAAAAAAAAVlVVVVVFv0BmZmZmZka/AGZmZmZmTr9ARERERERYv4CIiIiIiGK/eHd3d3d3Z79AIiIiIiJevyAREREREWO/AN7d3d3dVb8gIiIiIiJgvwAiIiIiIlq/4O7u7u7uZL+AZmZmZmZWvwDe3d3d3UW/AAAAAAAAAAAAAAAAAABAv2BmZmZmZk6/QDMzMzMzdD9wd3d3d3dvP0BVVVVVVWk/wO7u7u7uVj+AmZmZmZlvP0BVVVVVVWc/gGZmZmZmZD+AqqqqqqpCP6CZmZmZmXg/yMzMzMzMdT+Ad3d3d3dnPyAzMzMzM2U/AO/u7u7uWj+AZmZmZmZWPwBnZmZmZjY/wLu7u7u7Q78A3t3d3d1lv4CIiIiIiGi/wJmZmZmZXb8AERERERFlvwBERERERGS/QEREREREaL/g3d3d3d1nv0BERERERGi/4N3d3d3dcL+QiIiIiIhkv9zd3d3d3Wm/QDMzMzMzab8gMzMzMzNtvyAiIiIiImy/4N3d3d3db7/AiIiIiIhmvyAiIiIiImC/QEREREREYr/gzMzMzMxgvwAREREREWG/AHd3d3d3Jz+Ad3d3d3dTv4B3d3d3dze/AHd3d3d3T7+YmZmZmZkpvwAzMzMzM0O/gIiIiIiISL8A3t3d3d1Vv4B3d3d3d1u/gIiIiIiIVL/w7u7u7u5gv4B3d3d3d1u/ICIiIiIiWr8AVVVVVVVNvyAiIiIiImC/AO/u7u7uYr/w7u7u7u5sv4B3d3d3d2+/kIiIiIiIaL/AmZmZmZlpv4DMzMzMzGK/wMzMzMzMbL/gzMzMzMxov8jMzMzMzGi/ADMzMzMzZb/AzMzMzMxivwAAAAAAAGq/ABERERERYb8AVVVVVVVpP4B3d3d3d2E/AO/u7u7uTj8A3d3d3d0tP6C7u7u7u30/SEREREREdD8wMzMzMzNtP4B3d3d3d2E/AN7d3d3dXT8AZmZmZmZGPwBEREREREQ/AGZmZmZmNj8AoJmZmZkpPwA0MzMzMyO/gGZmZmZmUr9AVVVVVVVRvwAAAAAAAEC/AERERERENL+AZmZmZmZSv8C7u7u7u1O/YGZmZmZmZr+AiIiIiIhiv7y7u7u7u2W/QGZmZmZmZL+AiIiIiIhqv+Du7u7u7m6/mJmZmZmZZb8AAAAAAABmvygiIiIiImq/wMzMzMzMbr9AMzMzMzNwv0BERERERG6/4N3d3d3dbb+gmZmZmZllv2hmZmZmZma/gKqqqqqqWr9WVVVVVVVjv0BVVVVVVWO/wKqqqqqqZL+Ad3d3d3djv+Dd3d3d3Vm/IBERERERY7/g3d3d3d1Zv0BVVVVVVWG/ALy7u7u7K78AAAAAAABAv4B3d3d3d0e/ABARERERIb+gmZmZmZkpvwAQERERERG/QFVVVVVVRb+AMzMzMzNTvwC8u7u7u1u/gHd3d3d3U78AERERERFJvxAREREREV2/ADMzMzMzV7+AiIiIiIhcv8C7u7u7u1O/8O7u7u7uYr8AvLu7u7tLPwBWVVVVVSU/ACIiIiIiMr+Au7u7u7s7v8CIiIiIiG4/oJmZmZmZYT8AIiIiIiJSP4Cqqqqqqko/gHd3d3d3YT8AvLu7u7tfPwCIiIiIiEg/sKqqqqqqUj8A4N3d3d0tvwAAAAAAAFS/wLu7u7u7V79gd3d3d3dfvwAAAAAAAGq/QFVVVVVVbb/AmZmZmZlhvzAzMzMzM2e/wLu7u7u7a79gZmZmZmZqv4mIiIiIiGa/QIiIiIiIWL8QERERERFyv6CZmZmZmWm/vLu7u7u7ab+Ad3d3d3drv4iIiIiIiGq/oLu7u7u7Z78AERERERFZv4B3d3d3d2G/EBERERERa7+Ad3d3d3drv1BVVVVVVWe/ACIiIiIiZr8kIiIiIiJSv4BmZmZmZla/QEREREREYL+AqqqqqqpWvyAiIiIiIlI/ALq7u7u7Kz8AmpmZmZkpvwAAAAAAAEA/AN7d3d3dHT8AEBERERHxPgAiIiIiIjK/AAAAAAAAML8AAAAAAABIPwBYVVVVVRW/AJqZmZmZGb8Aq6qqqqpKvwA0MzMzM0O/gJmZmZmZVb8AzMzMzMw8v4yIiIiIiFy/gLu7u7u7X7/Au7u7u7tlv+Du7u7u7mK/wLu7u7u7V78AzczMzMxYvwDe3d3d3UW/gFVVVVVVVb/g7u7u7u5ev6C7u7u7u3S/4N3d3d3dc7+QiIiIiIhsvyAREREREW2/gEREREREaL8AERERERFrv0AzMzMzM2e/YGZmZmZmar9ARERERER5vwAREREREXW/oKqqqqqqcr8gIiIiIiJuv4CqqqqqqnW/ICIiIiIic79gZmZmZmZyv+Dd3d3d3Wm/AAAAAAAAXL9gVVVVVVVdv3h3d3d3d0e/gJmZmZmZSb/Aqqqqqqpev6C7u7u7u1+/wMzMzMzMTL8AAAAAAABcv4iIiIiIiGa/4N3d3d3dYb/AiIiIiIhQv4AREREREVm/cGZmZmZmbr/Au7u7u7trvwAAAAAAAGq/gIiIiIiIYL8QERERERFrv4CZmZmZmV2/QDMzMzMzY7+AZmZmZmZgv0BERERERFy/gGZmZmZmXr+AVVVVVVVFvwAAAAAAAFi/ABERERERMT8AAAAAAABQv4B3d3d3d0e/ABERERERUb+rqqqqqqpKvwCZmZmZmTk/gKqqqqqqQr8AWFVVVVUlvwAREREREWm/QFVVVVVVab9ARERERERov1ZVVVVVVWW/oIiIiIiId7+wqqqqqqp7v8C7u7u7u3O/AAAAAAAAcb/g3d3d3V2Ev+Dd3d3d3YG/MDMzMzMzdr8AAAAAAABzv4B3d3d3d1O/QDMzMzMzS7+AVVVVVVVFvwAAAAAAAFS/AKqqqqqqWr+ARERERERUvwBERERERFi/YGZmZmZmVr8A7u7u7u5GvwAAAAAAAAAAAJqZmZmZOb+Ad3d3d3dPvwCsqqqqqjo/ADQzMzMzIz8AVlVVVVU1P4CIiIiIiEC/AImIiIiIOD9AERERERFJvwAAAAAAAEi/AN7d3d3dRb8A4N3d3d0dP0BEREREREw/AAAAAAAAAAAAeHd3d3cnP+Dd3d3d3UW/ADMzMzMzS7+AiIiIiIhUvwAAAAAAAFC/AKuqqqqqOr9AVVVVVVVZv9DMzMzMzFC/AKuqqqqqSr9UVVVVVVVRv8Du7u7u7l6/ACIiIiIiQr8AzMzMzMw8v+Dd3d3d3W2/gHd3d3d3Y7/Au7u7u7tjvwAAAAAAAFy/AAAAAAAAZr8A7+7u7u5Wv4Dd3d3d3U2/gGZmZmZmVr/Aqqqqqqo6P4CIiIiIiEg/gGZmZmZmNr8AVVVVVVVRvwCYmZmZmTm/AEREREREUL8Ad3d3d3dHv7CqqqqqqkK/gHd3d3d3c7/w7u7u7u50v2BmZmZmZnG/EBERERERbb/A3d3d3d1wv2B3d3d3d2u/YGZmZmZmaL8AAAAAAABcv0BVVVVVVWm/AAAAAAAAaL+gqqqqqqpgv2BERERERGC/ABIRERERSb8AZmZmZmZSvwDNzMzMzES/gIiIiIiISL8AEBERERExPwDg3d3d3S2/AJiZmZmZKb+QiIiIiIhQPwCqqqqqqko/AO/u7u7uRj8ANDMzMzMjvwDe3d3d3R2/wMzMzMzMUD8AERERERFVP4qIiIiIiFA/AAAAAAAAAACAd3d3d3dPvwAAAAAAAFi/wLu7u7u7S78AMzMzMzNXv8DMzMzMzFC/ACIiIiIiWr/Au7u7u7tTvwDMzMzMzES/wKqqqqqqUr9AVVVVVVVdv0AzMzMzM1+/gEREREREUL+qqqqqqqpqv2BVVVVVVW2/4N3d3d3dZb8Aq6qqqqpWv8C7u7u7u2W/QFVVVVVVY78AAAAAAABgv8C7u7u7u2G/SEREREREZL9gd3d3d3djv4BmZmZmZlK/AFVVVVVVWb/QzMzMzMxUvwAREREREVW/gIiIiIiIUL8AAAAAAAAAAAC8u7u7u1M/ADMzMzMzSz8AWFVVVVUVP4AzMzMzMyO/ACIiIiIiYL8A7+7u7u5ev4CIiIiIiEC/QEREREREUL9AZmZmZmZ7P7CqqqqqqnQ/MDMzMzMzcD/g3d3d3d1lPwBmZmZmZk6/ADMzMzMzS7/AmZmZmZlVv0AiIiIiIlK/AAAAAAAAMD8AkJmZmZkJPwDNzMzMzES/ABERERERIT+A7u7u7u5gP4AiIiIiIlY/AN7d3d3dWT9gVVVVVVVVPwA0MzMzMzO/gMzMzMzMUL8AREREREQ0v0AzMzMzM0O/ABIRERERIT8AVVVVVVU1P0REREREREw/AODd3d3dHb9AMzMzMzNTP4C7u7u7uzs/AFVVVVVVFb8AmJmZmZkZPwAzMzMzMyM/AAAAAAAAUD8AERERERFBvwAyMzMzMzO/wLu7u7u7Qz+Au7u7u7tDP6CqqqqqqlY/AHZ3d3d3Nz9AMzMzMzMzPwAREREREUG/ADQzMzMzM78A3d3d3d1Nv8DMzMzMzFC/AHh3d3d3Jz+AZmZmZmZGvwCgmZmZmQm/gHd3d3d3W7/Au7u7u7tXvwDe3d3d3Vm/AJqZmZmZQb9oZmZmZmZiv2B3d3d3d2O/oIiIiIiIVL8A3t3d3d1RvwB4d3d3d08/ABARERERAb8AREREREQ0P8C7u7u7uyu/ACIiIiIiZD8AIiIiIiJWPwAAAAAAAGI/gIiIiIiIUD9AMzMzMzN/v1BVVVVVVX2/YGZmZmZmdb/w7u7u7u5wvwBnZmZmZkY/4O7u7u7uUj8A3t3d3d0dPwAQEREREQG/AO/u7u7uYL/g3d3d3d1lv8C7u7u7u1e/IBERERERXb8AERERERFhvwAREREREWe/ICIiIiIiZL/w7u7u7u5mv4BmZmZmZmC/gEREREREVL/Au7u7u7tbv4BmZmZmZlK/gGZmZmZmTj8AeHd3d3cnP+Dd3d3d3R2/AHh3d3d3Jz9ARERERERkP8C7u7u7u0s/8O7u7u7uUj8A8O7u7u4+PyAiIiIiIl4/gFVVVVVVTT9ARERERERQPwCZmZmZmUk/YFVVVVVVUb9AVVVVVVVVv4Cqqqqqqjq/gIiIiIiIUL8AERERERERPwAREREREUE/ADQzMzMzIz8A3t3d3d09vyAiIiIiIlI/gJmZmZmZST8AmpmZmZkpvwAiIiIiIkq/IBERERERVb/AiIiIiIhcv2BmZmZmZlK/gDMzMzMzU784MzMzMzNLv4CZmZmZmVm/wKqqqqqqVr+Ad3d3d3dbvwAREREREWu/QFVVVVVVY78A7+7u7u5kv/Du7u7u7k6/QDMzMzMzZ7+gmZmZmZlnvyAzMzMzM2G/sKqqqqqqYL8AVVVVVVVRv4CZmZmZmV2/AAAAAAAAUL+AZmZmZmZWv8CZmZmZmWM/QEREREREWD8AAAAAAABgPwDv7u7u7l4/AO/u7u7uUj/Au7u7u7tXP8CqqqqqqlI/AN7d3d3dRT8A7+7u7u5xv4CIiIiIiGi/IBERERERZb9wZmZmZmZmv4B3d3d3d3G/IDMzMzMzcL9ARERERERqvyAiIiIiImy/gIiIiIiIZL+gqqqqqqpgv87MzMzMzFi/gGZmZmZmTr+AiIiIiIhAP4CZmZmZmUG/QERERERENL8AZmZmZmY2v8C7u7u7u1M/AAAAAAAAWD+A3d3d3d1FP4BmZmZmZl4/EBERERERZz+AqqqqqqpeP9DMzMzMzFA/AM3MzMzMTD9ERERERERkP8CqqqqqqlY/QEREREREYD+AmZmZmZlRP4Cqqqqqqko/AGZmZmZmNj8AAAAAAAAAAICIiIiIiFQ/AFZVVVVVFT8AmZmZmZk5PwAREREREUm/AODd3d3dHb8UERERERFlv8DMzMzMzFS/QERERERERL8A3t3d3d1RvwDv7u7u7nq/AAAAAAAAc7/Au7u7u7tvv+Dd3d3d3Wm/AODd3d3dHT8AAAAAAABYPwARERERETE/oIiIiIiIUD8AREREREQ0PwBmZmZmZja/AIiIiIiIOL+AZmZmZmY2P0AiIiIiImI/AAAAAAAAYj/Au7u7u7tjP6CZmZmZmWc/AAAAAAAAUD8ARUREREREPwAREREREUE/AHh3d3d3J7+Ad3d3d3dhP0BERERERGA/ABERERERZz/g7u7u7u5WP4CIiIiIiGY/QDMzMzMzYz8AAAAAAABUPyAiIiIiImQ/ABIRERERIb9ARERERERUPwCZmZmZmQm/AEREREREND8AVlVVVVUlvwAAAAAAADA/cGZmZmZmTj8ARkREREQ0P0AzMzMzM0O/AN7d3d3dRb8AEBERERERvwCIiIiIiEC/gJmZmZmZST8AERERERFJP4BVVVVVVSW/AKuqqqqqSj+AiIiIiIhIPwBnZmZmZjY/AO/u7u7uPr8A2N3d3d0dP0BVVVVVVU2/QDMzMzMzV7/Au7u7u7tLvwBYVVVVVRW/AM3MzMzMPL8A3t3d3d09v8C7u7u7u0O/AFhVVVVVFT+IiIiIiIhIvwCcmZmZmRm/ICIiIiIiUj8AREREREQ0vwCrqqqqql6/gFVVVVVVUb8A7+7u7u5Wv8CZmZmZmQm/gIiIiIiIfb+QmZmZmZlzv7CqqqqqqnO/kJmZmZmZa78AMzMzMzNTvwDe3d3d3T2/AMzMzMzMPD/AqqqqqqpKvwDg3d3d3R2/wMzMzMzMVL8A3t3d3d09vwCrqqqqqjq/AFRVVVVVTT+AmZmZmZlVPwBWVVVVVTU/AN7d3d3dRT8A3t3d3d1rv0BERERERGi/YGZmZmZmYr+AVVVVVVVNvwBmZmZmZlK/gJmZmZmZVb8AzszMzMw8v8CqqqqqqkK/AFVVVVVVNb8AzczMzMxMvwAREREREQE/ACIiIiIiQj8gIiIiIiJiv0BEREREREy/UFVVVVVVRb+AMzMzMzNTv6Cqqqqqqla/gBERERERSb/Au7u7u7tDvwAjIiIiIkK/gERERERENL+Ad3d3d3dPPwBWVVVVVRW/AODd3d3dLb/g3d3d3d1FPwCamZmZmSk/ACIiIiIiSj8AREREREQ0P3BmZmZmZmo/ABERERERXT9gRERERERcPwC8u7u7u0M/AJqZmZmZOT/AmZmZmZlZP4BERERERDS/AMzMzMzMTD/AzMzMzMxQPwBEREREREw/gKqqqqqqQj+AqqqqqqpSPwCamZmZmWO/wMzMzMzMZr/AiIiIiIhgv0RERERERFy/AN7d3d3db79gVVVVVVVtv8Cqqqqqql6/QGZmZmZmTr8AEBERERFBP4B3d3d3d18/QDMzMzMzVz8AAAAAAABAPwAREREREV2/gGZmZmZmRr8AVVVVVVVFv4Dd3d3d3U2/ACIiIiIiQr8AzszMzMw8PwASERERETG/AO/u7u7uPr8AmpmZmZlBPwAQERERESG/AN7d3d3dPT+Ad3d3d3c3P4DMzMzMzGA/AAAAAAAAUD8AmpmZmZk5PwCamZmZmRk/wKqqqqqqWj94d3d3d3dhP2hmZmZmZlY/wMzMzMzMXD8AMzMzMzNDP0BEREREREw/QFVVVVVVJb8AkJmZmZkJPwDv7u7u7k4/AIiIiIiIOL8Aq6qqqqpCPwA0MzMzMyO/VVVVVVVVFb8AVlVVVVUlvwBERERERDQ/AKuqqqqqSj/AzMzMzMxEPwAiIiIiIlY/4MzMzMzMUD8AMzMzMzNLP4Cqqqqqqko/QEREREREVD92d3d3d3dTPwCgmZmZmQm/gEREREREXD8QERERERFZP0BVVVVVVVE/AKqqqqqqOj+AMzMzMzMzvwCYmZmZmRk/AFZVVVVVFT8AvLu7u7s7PwBEREREREQ/ABERERERIT+wqqqqqqpCvwAQEREREQE/AO/u7u7uTr8wMzMzMzMjv4CIiIiIiDi/ABERERERQT9AMzMzMzNnv+Dd3d3d3V2/4MzMzMzMYL/AiIiIiIhYv4BmZmZmZmK/oKqqqqqqYL+gu7u7u7tTv8DMzMzMzFS/gEREREREXL/AzMzMzMxcv8CZmZmZmVW/AFZVVVVVFT8A7+7u7u5GvwDd3d3d3S0/ABERERERIb8AEBERERERv8Cqqqqqqko/gFVVVVVVVT9gd3d3d3dXPwDNzMzMzEQ/ABARERER8T4Auru7u7srPwAAAAAAAEC/ADMzMzMzQ7+wqqqqqqpgP4B3d3d3d2E/4N3d3d3dUT8A7+7u7u5WP2BVVVVVVWM/oIiIiIiIVD9AEREREREhvwDNzMzMzEQ/ABERERERQb8AZmZmZmY2vwAiIiIiIjI/AIiIiIiIQD+Au7u7u7s7PwCYmZmZmQk/QDMzMzMzM78AIiIiIiIyv4CIiIiIiDi/ALy7u7u7K78A3t3d3d0dvwDe3d3d3T2/gHd3d3d3Uz8AGBERERHxvuDd3d3d3R0/AGZmZmZmRj+AiIiIiIhQP0AiIiIiIl4/ICIiIiIiQj8AvLu7u7srPwARERERETE/gGZmZmZmNj/w7u7u7u5OPwAQERERESG/oKqqqqqqXj9AMzMzMzNXP+Du7u7u7lI/AJqZmZmZQT+AqqqqqqpCP8C7u7u7u0s/ADMzMzMzI78AvLu7u7s7PwAAAAAAAEC/AO/u7u7uTr9ARERERERQvwAQEREREQE/gLu7u7u7S79gVVVVVVVVvwAREREREQE/AFZVVVVVNT/A3d3d3d1dvwAAAAAAAFy/3t3d3d3dVb+AVVVVVVVRv8DMzMzMzEy/ABQRERERET8AREREREQ0PwAUERERESE/ABERERERXT8gMzMzMzNXPxAREREREUk/AM3MzMzMWD8AAAAAAABYP9DMzMzMzFw/QFVVVVVVTT+Ad3d3d3dHPwDe3d3d3S0/gHd3d3d3N7/w7u7u7u5OPwAiIiIiIjK/ACIiIiIiQj8AMzMzMzMjv1BVVVVVVSW/AGZmZmZmNr+oqqqqqqpmv4Du7u7u7ka/IDMzMzMzV78AzczMzMxQvwDe3d3d3UW/AN7d3d3dPb/Aqqqqqqo6vwBQVVVVVRW/ADMzMzMzQz8AmpmZmZkpP4AiIiIiIkI/AODd3d3dHT8AAAAAAABIP4B3d3d3dzc/AAAAAAAAUD+AiIiIiIhIPwAREREREVE/YEREREREXD/OzMzMzMxgP4DMzMzMzEw/YGZmZmZmXr+Ad3d3d3dXv8C7u7u7u1O/AO/u7u7uTr8AeHd3d3c3PwAiIiIiIjI/ABERERERAT8AMDMzMzMjvwAjIiIiIkK/gHd3d3d3R78AWFVVVVUVvwCamZmZmTm/ACMiIiIiSr9WVVVVVVVdv0BEREREREy/gGZmZmZmVr+AZmZmZmZgv8C7u7u7u0O/YGZmZmZmTr8Ad3d3d3c3v0BERERERGq/0MzMzMzMZL8AAAAAAABkvwAzMzMzM1u/IBERERERQb+AZmZmZmZWvwDe3d3d3T2/gIiIiIiIUL8AZmZmZmY2v4B3d3d3d0e/7u7u7u7uPr8A3N3d3d0dPwAiIiIiIkq/gIiIiIiIOL8AEREREREhvwASERERESG/gHd3d3d3Rz/Au7u7u7tXP+Dd3d3d3U0/AODd3d3dHT8AZmZmZmZiPwAAAAAAAFw/AKqqqqqqQj+Ad3d3d3cnvwDNzMzMzGa/wLu7u7u7Z7+Ad3d3d3dfvyAzMzMzM1e/AN7d3d3dVb9AERERERFjvwAREREREWG/zMzMzMzMYL8AmpmZmZlJP0BEREREREw/gERERERERD9AIiIiIiJaP+Dd3d3dXZK/end3d3d3j78BAAAAAACIv5iZmZmZGYO/oJmZmZmZib93d3d3d3eHv7Cqqqqqqn6/sKqqqqqqer/Au7u7u7ttvxAREREREYU/3d3d3d2dmj8kIiIiIqKgP4iIiIiICIU/MDMzMzMzfT9GRERERERzPwDv7u7u7m4/QFVVVVVVbT8AAAAAAABoPzBERERERGY/wLu7u7u7Uz8AERERERExvwCamZmZmRm/cHd3d3d3T78Aq6qqqqpKvwAQERERESG/gLu7u7u7K7+IiIiIiIhAvwCcmZmZmSk/AO/u7u7uTr9AVVVVVVVZv5qZmZmZmV2/AN7d3d3dWb8AVVVVVVVNv0B3d3d3d0e/QDMzMzMzQ78A4N3d3d0tP5CZmZmZmWc/wKqqqqqqXj/Au7u7u7tDPwAQEREREQE/YGZmZmYmkr80MzMzM3OQv2hmZmZm5oa/VFVVVVVVg7+QiIiIiMiXvzgzMzMzs5S/8O7u7u7uj7+IiIiIiAiHv0hERERERIa/EhERERERgb94d3d3d3d6v4CIiIiIiHW/QDMzMzMzcL8SERERERFnv+Dd3d3d3VG/gGZmZmZmXr8AvLu7u7srPwDNzMzMzES/gHd3d3d3N78AeHd3d3cnvwA0MzMzMzM/gHd3d3d3Rz8A3t3d3d0dP4BmZmZmZk4/QDMzMzMzYT/AzMzMzMxQP2BVVVVVVUU/gEREREREUD8AAAAAAABQv+Dd3d3d3U2/ABARERER8T4AVlVVVVUlPwDv7u7u7mC/dnd3d3d3Y79gVVVVVVVdv0BERERERFS/gLu7u7u7Qz9AERERERFJP0BEREREREw/AFVVVVVVRT8A7+7u7u5SPwB2d3d3dzc/ACIiIiIiMr+amZmZmZlVP4BmZmZmZlK/AODd3d3dHT8A7+7u7u4+vwBVVVVVVSW/gGZmZmZmYD8AIiIiIiJWP0BERERERGA/AAAAAAAASD+AZmZmZmZePwAAAAAAAFA/AKuqqqqqSj8A7+7u7u4+P4B3d3d3d0c/AO/u7u7uUj9AMzMzMzMjP8CqqqqqqlY/AGdmZmZmRr9GRERERERMv4CIiIiIiEC/ANzd3d3dHT9QVVVVVVVnPwAREREREWM/AAAAAAAAZj9AZmZmZmZkP/Du7u7u7nU/oKqqqqqqbj8QERERERFrPwAREREREWc/ABERERERVT9ARERERERcP0AzMzMzM1s/gMzMzMzMRD/Au7u7u7tXv8C7u7u7u1u/qKqqqqqqVr8AmJmZmZkpvwAUERERERE/AAAAAAAAQD9VVVVVVVUlPwCamZmZmTk/AJiZmZmZGb+AmZmZmZk5v0REREREREw/ADMzMzMzQz+gmZmZmZljP4CIiIiIiFg/IiIiIiIiVj+AMzMzMzNTP2BmZmZmZmw/QDMzMzMzbz9QVVVVVVVjP8C7u7u7u2c/AAAAAAAAYD9gVVVVVVVlPwDe3d3d3Vk/sKqqqqqqYj/Au7u7u7t9P4BmZmZmZnM/wKqqqqqqcj9YVVVVVVVxPxAREREREXI/eHd3d3d3aT9IRERERERiP6CqqqqqqmQ/ACIiIiIiSj8AAAAAAABYPwAAAAAAAFg/AERERERERD8AEBEREREBPwCZmZmZmTm/gN7d3d3dLb8AAAAAAAAAAAAAAAAAAFi/IDMzMzMzU7+YmZmZmZlRv0AzMzMzM1O/4N3d3d3dVb+AiIiIiIhYvyAiIiIiIkK/AImIiIiIQL8AERERERFdP0BEREREREw/4O7u7u7uRj8AIyIiIiIyvwBVVVVVVUU/vLu7u7u7Xz8AvLu7u7srP0BERERERFQ/YGZmZmZmYD9ARERERERYP5qZmZmZmVE/gIiIiIiIUD+Au7u7u7tjPwC8u7u7uys/ACIiIiIiSj8AEREREREhvzAiIiIiooA/eHd3d3d3ej+gqqqqqqp4P2BmZmZmZnM/gIiIiIgIij8gIiIiIqKFP7CqqqqqqoE/3t3d3d3ddz9wZmZmZuaAP5iZmZmZmXs/4N3d3d3ddz/AzMzMzMxxP0BERERERHs/mJmZmZmZdD8AAAAAAABuP2B3d3d3d2M/AM3MzMzMaD8iIiIiIiJoP7C7u7u7u2E/oJmZmZmZYT8gIiIiIiJSvwAAAAAAAEi/AN7d3d3dHT8ARUREREREP0AzMzMzM2u/0MzMzMzMYr+sqqqqqqpevwC7u7u7u0u/ICIiIiIicr9QVVVVVVVrv8C7u7u7u22/IDMzMzMzab/g3d3d3d1vv1BVVVVVVW+/3t3d3d3dY78gAAAAAABkv6CIiIiIiGi/SEREREREZL9oZmZmZmZevwDMzMzMzDy/gIiIiIiIYr8AAAAAAABcv0RERERERFi/AO/u7u7uTr/AqqqqqqpWvwDv7u7u7ka/iIiIiIiIQD8A3t3d3d1Nv6CqqqqqqlY/QFVVVVVVWT8AmpmZmZkJvwB4d3d3dyc/YGZmZmZmdL8QERERERFwv4B3d3d3d22/EBERERERXb8ARERERERmv8Cqqqqqqmi/AO/u7u7uZr94d3d3d3djvwA0MzMzMyO/ABERERERAT8AAAAAAABAPwAiIiIiIkI/AHh3d3d3J78AAAAAAABIv8CqqqqqqkK/wN3d3d3dWb8AVFVVVVUVP4B3d3d3d0+/wKqqqqqqOj8AvLu7u7srvwAiIiIiIla/sKqqqqqqXr8AAAAAAABUvwDe3d3d3S0/ACIiIiIiMr8AAAAAAABIP4Dd3d3d3S2/AODd3d3dHb8AmpmZmZk5P+DMzMzMzDw/QEREREREVD8A3N3d3d0dPwAREREREUE/VFVVVVVVFT+Aqqqqqqo6vwC6u7u7uyu/wLu7u7u7Vz+Ad3d3d3dbP2BVVVVVVU0/AM3MzMzMUD8AICIiIiIyPwCsqqqqqjq/AKuqqqqqQr+QmZmZmZlBv4BmZmZmZmo/YFVVVVVVYT9ARERERERkPxAREREREWE/gJmZmZmZcz9AMzMzMzNrPwAREREREV0/IiIiIiIiXj8AvLu7u7tbP4BmZmZmZlY/ABERERERWT8AIiIiIiJCPwAREREREUm/cHd3d3d3V7+4u7u7u7tTvwCJiIiIiDi/AAAAAAAAQD/w7u7u7u5GPwB4d3d3dyc/ANzd3d3dHT+QiIiIiIhkPwAAAAAAAFg/EBERERERYT8AIBERERHxPgCYmZmZmQk/ICIiIiIiVr94d3d3d3dPvwAREREREVG/QEREREREYL9gZmZmZmZSv8C7u7u7u1u/QFVVVVVVUb+gmZmZmZllvxAREREREWe/mZmZmZmZYb8AmpmZmZlZvwCamZmZmSk/gIiIiIiISL9AMzMzMzMzPwAQEREREQG/AM3MzMzMTD8AmpmZmZkZvwAREREREfE+ABARERER8T7Au7u7u7tTP8DMzMzMzEw/ZmZmZmZmUj8AiIiIiIhIP8DMzMzMzFQ/AO/u7u7uPj/AmZmZmZk5PwDMzMzMzDw/YGZmZmZmdr/AzMzMzMxyv6C7u7u7u2+/sKqqqqqqar/g7u7u7m6Av4BmZmZmZnu/ABERERERb79IRERERERuv4BVVVVVVWG/ZGZmZmZmYr9AMzMzMzNXv0BERERERFy/gHd3d3d3Z7+oqqqqqqo6v0AzMzMzM1e/gGZmZmZmRr9gVVVVVVVhvwAREREREV2/0MzMzMzMWL8AMzMzMzNLv0BVVVVVVWO/8O7u7u7uaL84MzMzMzNbv8Dd3d3d3Vm/4N3d3d3dYb8AAAAAAABYvwAQEREREfG+AKCZmZmZCT/Au7u7u7tTvwASEREREfE+ABIRERERAb8AzczMzMw8vwC8u7u7u1O/6O7u7u7uTr8AMzMzMzMjvwBUVVVVVSW/ICIiIiIiYj8gIiIiIiJgPzMzMzMzM1c/AN7d3d3dTT8AZmZmZmZGPwDu7u7u7j4/gIiIiIiIVD/g3d3d3d1FP8DMzMzMzHa/ICIiIiIidb+QiIiIiIhwv9Dd3d3d3W2/QEREREREer/Au7u7u7twv0BERERERGy/zMzMzMzMYL9AIiIiIiJivyAREREREVG/QFVVVVVVVb8AzczMzMxMv2BmZmZmZm6/JCIiIiIicb9ERERERERkvwAAAAAAAGC/AN7d3d3dVb/v7u7u7u5Wv4BVVVVVVUW/gKqqqqqqSj/Q3d3d3d1rP0BERERERHM/aGZmZmZmcz8AERERERFzPxAREREREXQ/iIiIiIiIcD80MzMzMzNpP+Du7u7u7mI/gCIiIiIiVr9gRERERERYvwAAAAAAAFi/gHd3d3d3V7+AZmZmZmZqvzAiIiIiImi/ZmZmZmZmWr9AIiIiIiJWv4C7u7u7u0u/wN3d3d3dPb/MzMzMzMxQvwAREREREVm/gHd3d3d3Yb8AIiIiIiJCvzQzMzMzM0O/AKCZmZmZCT8A3N3d3d0dvwBERERERDS/3t3d3d3dRb8AVFVVVVU1vxAREREREW0/ABERERERXT/g3d3d3d1lPwAREREREVE/AAAAAAAASL+AIiIiIiJKvwDg3d3d3S2/QERERERERD8ARERERERqPwDNzMzMzGo/QCIiIiIiZj/Au7u7u7tXP4BERERERFw/u7u7u7u7Xz8QERERERFhP4AREREREUE/AERERERERD9AVVVVVVU1P4ARERERETE/ADIzMzMzIz8AmJmZmZkZPwAREREREUk/wN3d3d3dPT+AVVVVVVVRPwDe3d3d3S0/gJmZmZmZKb/Au7u7u7s7v4BmZmZmZka/ABIREREREb8Aq6qqqqpKP2B3d3d3d08/AAAAAAAAVD8AEBERERERv4CIiIiIiDi/IBERERERSb8AZmZmZmY2v4BVVVVVVVW/REREREREUL8AEBERERHxvoBmZmZmZka/gKqqqqqqQr9AVVVVVVVFvwAAAAAAADC/gIiIiIiISD8AZmZmZmZOvwB4d3d3dyc/AMzMzMzMPL9AREREREQ0vwDv7u7u7mA/4N3d3d3dYz8gIiIiIiJmP6C7u7u7u1M/QDMzMzMzdT8gMzMzMzNxPwAAAAAAAGY/QkREREREYD8A7u7u7u4+vwAiIiIiIjI/AM3MzMzMRL8AAAAAAAAAAGBmZmZmZm6/GBERERERa78AAAAAAABqv2BERERERGK/AO/u7u7uXr94d3d3d3dbvwDv7u7u7ka/AODd3d3dHb/Au7u7u7tbP0BERERERFw/0MzMzMzMWD8AmpmZmZlJP8C7u7u7u18/4O7u7u7uVj9ERERERERiPwC8u7u7u0s/gHd3d3d3Uz8AERERERFBPwBWVVVVVSU/AAAAAAAAUD9ARERERERYv0AiIiIiIkK/iIiIiIiIVL+Ad3d3d3dPv4BmZmZmZlK/oKqqqqqqVr8QERERERExP4CIiIiIiFC/ACIiIiIiMr/AmZmZmZlJv4CZmZmZmQm/gJmZmZmZQb+ARERERERMPwAREREREVk/ZGZmZmZmRj8A3t3d3d1RP8C7u7u7u2s/YEREREREYD/w7u7u7u5aP4BmZmZmZlY/ALy7u7u7S79AIiIiIiJavwAAAAAAAEC/gFVVVVVVNb8ANDMzMzMzvwCamZmZmTm/AFRVVVVVNb8AeHd3d3cnvwDv7u7u7ka/8O7u7u7uPj8AIiIiIiIyPwAAAAAAAAAAAM3MzMzMVL+IiIiIiIhQv2BmZmZmZlK/AImIiIiIOL8AmpmZmZkpv4AzMzMzM0s/AN7d3d3dLb8AoJmZmZkJP0AzMzMzM2+/0MzMzMzMaL9oZmZmZmZgv8C7u7u7u1e/oIiIiIiIUL+Ad3d3d3dTv6CZmZmZmUG/AFVVVVVVRb8AmpmZmZk5v2BmZmZmZlY/AJqZmZmZGT+AZmZmZmZGP4BmZmZmZlI/2N3d3d3dTT8AERERERFJP4B3d3d3d1c/gGZmZmZmZj+gmZmZmZlZP6uqqqqqqmA/QEREREREXD8AvLu7u7s7PwAzMzMzM0s/AKuqqqqqSj9IRERERERMP0AiIiIiIm6/ICIiIiIiaL8AERERERFhv2BVVVVVVV2/QERERETEib/QzMzMzEyFv9Dd3d3dXYC/ZmZmZmZmd7+Ad3d3d3d2v2BVVVVVVW2/AO/u7u7uZr/Aqqqqqqpkv2BmZmZmZmI/IBERERERWT+IiIiIiIhiP6CZmZmZmWU/wLu7u7u7cz8AAAAAAABsPwAAAAAAAGQ/QEREREREVD8AAAAAAABovwAiIiIiIla/YFVVVVVVRb8A3t3d3d09PwAAAAAAAHS/ICIiIiIicr8yMzMzMzNrv8C7u7u7u2e/AFhVVVVVFb/g3d3d3d1VvwDe3d3d3S0/gBERERERSb/g3d3d3d1lv8C7u7u7u2e/AAAAAAAAXL+ARERERERMvyAiIiIiImC/ICIiIiIiVr9UVVVVVVVVvwAiIiIiIla/oJmZmZmZYb8gERERERFVv767u7u7u1O/wO7u7u7uVr8A4N3d3d0dv4B3d3d3dze/ABERERER8T4A3t3d3d09vwAiIiIiIkI/ADMzMzMzIz9gREREREQ0P4BVVVVVVVE/AKqqqqqqUr8AAAAAAABYv4AzMzMzM1e/QEREREREXL8AeHd3d3dbvwAAAAAAAFS/AEZERERENL8ANDMzMzMjP4BERERERFS/SERERERERL+Ad3d3d3dbvwAREREREVG/AGZmZmZmNr9gVVVVVVVNvwAzMzMzMyO/gIiIiIiIQL8A3N3d3d0dPwB4d3d3dye/gHd3d3d3Jz+ARERERERUP0BERERERFC/QDMzMzMzQ7+gmZmZmZlBvwCgmZmZmQm/wLu7u7u7Vz/AqqqqqqpWPzAzMzMzM2E/ABERERERST8AAAAAAABiP9jd3d3d3VU/kJmZmZmZUT8AzczMzMxEP4AREREREVU/AAAAAAAAUD+giIiIiIhAPwDv7u7u7kY/QEREREREZj+Ad3d3d3dbP2hmZmZmZlY/AHh3d3d3Jz8AIiIiIiJCPwBWVVVVVTU/AM3MzMzMTD9VVVVVVVVVP0AiIiIiImq/4N3d3d3dab9gVVVVVVVjv4B3d3d3d1+/AKuqqqqqXr8AIiIiIiJavwDw7u7u7j6/4N3d3d3dVb/g7u7u7u5wv0BERERERGy/QEREREREbL8AERERERFVvwDg3d3d3R2/QERERERERD8A3t3d3d0tPwAQEREREQE/ABERERERXT94d3d3d3dbP+Dd3d3d3WU/gFVVVVVVTT9gVVVVVVVjP4B3d3d3d1c/gHd3d3d3Jz8AAAAAAABAvwDe3d3d3U2/AJyZmZmZCb+gmZmZmZlBvwAREREREUk/AN7d3d3dTT8AAAAAAAAwPwAiIiIiIkI/AGdmZmZmNj9AERERERFZv4CIiIiIiFi/zczMzMzMRL8ANDMzMzMzPwCZmZmZmTm/gIiIiIiISL9IREREREREvwCIiIiIiEC/ABERERERUT+gqqqqqqpSP1ZVVVVVVVE/AM3MzMzMRD+AVVVVVVVVP8CZmZmZmUE/4N3d3d3dTT8A3t3d3d1VP0BERERERGQ/YGZmZmZmZD/w7u7u7u5SPwDv7u7u7lI/gGZmZmZmXr+AqqqqqqpevwBWVVVVVSU/IDMzMzMzQ78AdHd3d3c3vwDu7u7u7j6/AGZmZmZmNr/AmZmZmZk5vwA0MzMzMzO/EBERERERST8AEBERERERPwBEREREREQ/gJmZmZmZVb/u7u7u7u5Sv8C7u7u7u0u/AGZmZmZmNr+Au7u7u7tDv4CZmZmZmUm/AJqZmZmZGb8AWFVVVVUVvyAiIiIiImS/2N3d3d3dY7/w7u7u7u5ev4BmZmZmZlq/wLu7u7u7U78AEBERERHxPgAiIiIiIjI/AJiZmZmZGb+AZmZmZmZGv4B3d3d3d0e/IDMzMzMzQ78AMzMzMzMzvwB3d3d3d0c/AAAAAAAAVD8gIiIiIiJCPwA0MzMzMyO/gIiIiIiIXD9AZmZmZmZOP6qqqqqqqmA/wN3d3d3dVT8Aq6qqqqpiPwAzMzMzM1s/AAAAAAAAUD8gIiIiIiJKP4AiIiIiIlK/AODd3d3dHT8Aqqqqqqo6v4CqqqqqqkI/AO/u7u7uaL9AMzMzMzNpv4BVVVVVVWO/aGZmZmZmXr8Ad3d3d3dHv8CqqqqqqlK/gIiIiIiIQL8AmpmZmZk5v6Cqqqqqqmw/WFVVVVVVaT8kIiIiIiJmP+Dd3d3d3Wk/wLu7u7u7aT80MzMzMzNpP3B3d3d3d2k/gGZmZmZmWj94d3d3d3d0P6CZmZmZmXQ/eHd3d3d3cT9ARERERERwP0RERERExIA/WFVVVVVVfD+8u7u7u7tzP6CIiIiIiG4/AAAAAAAAUD+AZmZmZmZOPwAREREREVU/QHd3d3d3Wz8AMzMzMzMzv6CZmZmZmVG/rKqqqqqqUr8AAAAAAABQvwAAAAAAAGC/QDMzMzMzQz9EREREREQ0vwA0MzMzMyO/wKqqqqqqVr+AiIiIiIhYv2dmZmZmZla/AN7d3d3dRb8A3t3d3d09P8C7u7u7u0O/oJmZmZmZOT8AREREREQ0P6CqqqqqqmQ/AAAAAAAAYD+IiIiIiIhgP4BVVVVVVWM/gBERERERaz8gMzMzMzNtP0BmZmZmZmA/kIiIiIiIXD8AzczMzMxuP4BVVVVVVW0/QEREREREcD/g3d3d3d1jP0BERERERGQ/mpmZmZmZXT+Ad3d3d3dTP4CZmZmZmUk/AERERERERL8AAAAAAAAwvwBmZmZmZjY/AEREREREND+Ad3d3d3dXP8D//////08/wIiIiIiIOD8AEBEREREBP4BERERERES/wLu7u7u7Oz9AREREREREPwAiIiIiIko/AO/u7u7uPj+AqqqqqqpCP4CZmZmZmTk/AFBVVVVVFb8AvLu7u7s7PwDe3d3d3R2/wIiIiIiIOD8AEBEREREBv4CZmZmZmVW/VlVVVVVVTb+gqqqqqqpSvwAAAAAAAEA/ANzd3d3dHT/Au7u7u7tTP5mZmZmZmUk/AM3MzMzMPD8AeHd3d3cnPwCIiIiIiDg/AO/u7u7uTj9IREREREQ0vwAAAAAAAFi/wJmZmZmZXb8AERERERFVvyAREREREVm/AAAAAAAAdr8gERERERFxvwDv7u7u7m6/AAAAAAAAZr8AIiIiIiIyPwBnZmZmZja/ADMzMzMzM78AAAAAAABIvyAiIiIiImC/4MzMzMzMUL8AAAAAAABQvwCrqqqqqjo/ALy7u7u7O7+IiIiIiIg4v2B3d3d3d1O/QDMzMzMzV78AmJmZmZkJvwDv7u7u7k4/YGZmZmZmXj+Au7u7u7tbPwCJiIiIiDg/AAAAAAAAAAAAAAAAAABAvwAiIiIiIko/AEVERERERD+AqqqqqqpKPwDv7u7u7j4/ABIRERERIT/Au7u7u7tjv+Dd3d3d3Vm/AAAAAAAAML8AAAAAAABIvwBYVVVVVRU/AN7d3d3dLb8iIiIiIiIyvwDc3d3d3S2/gJmZmZmZQT9AZmZmZmZOP6CZmZmZmSk/AM3MzMzMRD+AmZmZmZldP4CIiIiIiFQ/vLu7u7u7Sz8AMzMzMzNLP0BVVVVVVW0/gHd3d3d3YT8wMzMzMzNjPwDNzMzMzFw/AO/u7u7uWj+AiIiIiIhAPwAYERERERE/wJmZmZmZOb8AVlVVVVVZPwAREREREVE/AAAAAAAAUD8AEhERERERvwBUVVVVVSW/8O7u7u7uRr/Au7u7u7tLvwAyMzMzMyO/AO/u7u7uRr9gVVVVVVUlvwAREREREUm/gERERERETL/AiIiIiIhQvwAAAAAAAFS/AFVVVVVVJT8A8O7u7u4+vwAAAAAAAGS/0MzMzMzMYr+oqqqqqqpWv6CZmZmZmWO/4N3d3d3dVb8AAAAAAAAAAGBVVVVVVU2/ALy7u7u7K78AERERERFRP0BEREREREQ/AN7d3d3dLb8AmpmZmZkpPwAiIiIiImI/YGZmZmZmTj8AAAAAAABYPwAREREREVE/gHd3d3d3YT8gERERERFRP6uqqqqqqko/gGZmZmZmTj8AmpmZmZlRPwDNzMzMzFA/gGZmZmZmWj+giIiIiIhAPwBVVVVVVVE/AN7d3d3dLT8AVFVVVVUVvwDNzMzMzEw/QEREREREfT+giIiIiIh4P0BERERERHI/EhERERERaT8gMzMzMzNzPzAiIiIiImg/wLu7u7u7bT/Au7u7u7tlPyAiIiIiInA/oJmZmZmZYz9oZmZmZmZWP4CIiIiIiEA/gIiIiIiIWD+JiIiIiIhkP8CqqqqqqlY/QEREREREWD9AMzMzMzNbv2BERERERGi/6O7u7u7ubr/Au7u7u7twvyAiIiIiImC/kJmZmZmZYb9YVVVVVVVVvwAAAAAAAFi/gIiIiIiIYL+gmZmZmZljvwAAAAAAAFC/gHd3d3d3U78QERERERFwv1BVVVVVVWO/MzMzMzMzYb+AiIiIiIhcv8DMzMzMzGS/AAAAAAAAYr/NzMzMzMxYv4AzMzMzM1O/QDMzMzMzW7+Ad3d3d3dTv5mZmZmZmVW/QFVVVVVVXb+AiIiIiIhcvyAzMzMzM1+/ICIiIiIiQr8A3t3d3d09v8DMzMzMzFg/AO/u7u7uPj/Aqqqqqqo6PwCrqqqqqkq/AFRVVVVVRb8AnJmZmZkZPwAAAAAAADA/AAAAAAAAML8AvLu7u7tfPwDv7u7u7kY/ALy7u7u7Qz8AmpmZmZkZP4B3d3d3d1s/qKqqqqqqQj8A3d3d3d0tPwAQERERERG/AIqIiIiIOL/w7u7u7u5SvwBWVVVVVRW/ACMiIiIiMj8AEBERERERv4B3d3d3d0c/AAAAAAAAML8AIiIiIiJCvwDNzMzMzFi/8O7u7u7uUr8AAAAAAABQvwBVVVVVVU2/wKqqqqqqQj8AERERERExPwDe3d3d3S0/AKyqqqqqOr8AvLu7u7s7P2BVVVVVVUU/gIiIiIiIOD8AAAAAAABAP4AiIiIiIlY/QFVVVVVVJb9gZmZmZmZGv4BEREREREy/gFVVVVVVRT+AiIiIiIhUP4SIiIiIiEg/AN7d3d3dUT8AvLu7u7tDvwAAAAAAAEi/ABERERERWb8EAAAAAABUv6CqqqqqqnK/MCIiIiIicb9gVVVVVVVlv3B3d3d3d2G/QCIiIiIidL9AMzMzMzNwv8DMzMzMzGy/REREREREXL8gMzMzMzN6v0BERERERHW/gIiIiIiIcb+AmZmZmZlpv0BVVVVVVVk/ICIiIiIiXj8QERERERFdP8DMzMzMzFA/gGZmZmZmcT8iIiIiIiJqPzAzMzMzM2c/wO7u7u7uWj/Au7u7u7tLPwAREREREVU/cHd3d3d3Xz9AVVVVVVVlP4CIiIiIiFS/gIiIiIiIWL+YmZmZmZlRv4CZmZmZmVW/wMzMzMzMYr/AzMzMzMxUv8DMzMzMzFC/ABQRERERET8gIiIiIiJiv4B3d3d3d2O/REREREREYL9AVVVVVVVjvwCJiIiIiDi/ABIREREREb80MzMzMzMjPwAAAAAAADC/AJqZmZmZST8AAAAAAAAAAGBVVVVVVRU/QEREREREUD9AMzMzMzNfPyAREREREV0/aGZmZmZmUj8A7+7u7u5OP4CZmZmZmV0/ABERERERUT84MzMzMzNhPwAzMzMzM0s/AKuqqqqqVr8A7+7u7u5kv4CZmZmZmVG/ICIiIiIiWr8AmpmZmZlVPwAiIiIiIlI/AHh3d3d3Nz9AREREREREPwCIiIiIiDg/AAAAAAAAQD8AnJmZmZkJPwCamZmZmTm/ABERERERVb94d3d3d3dXv0AREREREUm/AJqZmZmZQb/AqqqqqqpWv2BmZmZmZmK/AAAAAAAAXL8AAAAAAABYv2BVVVVVVW2/SEREREREZr8AAAAAAABUvwAiIiIiIlq/QFVVVVVVWb8gERERERFhv9DMzMzMzFi/AHh3d3d3J78Adnd3d3cnvwAQEREREfE+wJmZmZmZOb8AvLu7u7s7vwCrqqqqqkq/ICIiIiIiSr8gMzMzMzNDPwCamZmZmTm/gHd3d3d3Xz+gmZmZmZlVP6qqqqqqqlI/AO/u7u7uPj8AEBERERFJP4BERERERFg/ABIRERERMT/w7u7u7u5GPwAAAAAAAAAAAFRVVVVVFT8AAAAAAABAvwBWVVVVVRU/MDMzMzMzkL+gmZmZmZmLv3B3d3d394S/iIiIiIiIf7+wqqqqqiqEv2hmZmZm5oC/AAAAAAAAeb9wZmZmZmZxvwA0MzMzMyO/QERERERETD8YERERERFRPwBVVVVVVTU/AAAAAAAAbD/e3d3d3d1jP0AzMzMzM18/IBERERERYz9AZmZmZmZOP4DMzMzMzEQ/gJmZmZmZOb8A7+7u7u5Gv4CIiIiIiGq/IBERERERbb+4u7u7u7tLv4AREREREVW/AN7d3d3dPT8AREREREQ0PwCYmZmZmRk/gHd3d3d3R7+Ad3d3d3dfvwDe3d3d3S2/iIiIiIiISL8A3N3d3d0tvwDMzMzMzDy/ADMzMzMzM793d3d3d3dHvwDe3d3d3T2/ADMzMzMzM79ARERERERMvwAREREREfG+gIiIiIiIQL8A3t3d3d09P4CZmZmZmTm/gFVVVVVVFb8ANDMzMzNLP8CqqqqqqmY/wMzMzMzMZD8gIiIiIiJiPwDNzMzMzEw/AAAAAAAAVL8A3t3d3d1VvwB4d3d3d0+/4N3d3d3dPb8AmpmZmZlBvwCgmZmZmQm/ABARERERAb+AERERERExvwBmZmZmZja/gHd3d3d3Jz8A3t3d3d09P0BERERERFQ/AN7d3d3dPT+Ad3d3d3cnvwBWVVVVVRW/AERERERERL8AvLu7u7srvyAREREREVE/AAAAAAAASD8AiYiIiIhIPwCqqqqqqjq/AAAAAAAAML8wMzMzMzNDvwBmZmZmZja/QEREREREXD+AZmZmZmZGP2BVVVVVVVE/AAAAAAAAQD8AmpmZmZkpPwCamZmZmRk/AN7d3d3dHT+AqqqqqqpKPwAzMzMzM0u/wN3d3d3dHb+AmZmZmZk5vwAQEREREfE+ABARERER8T4AmpmZmZkpP6qqqqqqqjo/AImIiIiIOD8AqqqqqqpKPwCIiIiIiDg/AJmZmZmZQT8gMzMzMzMjv4BVVVVVVWm/wKqqqqqqYr/AzMzMzMxYv0AzMzMzM0O/QEREREREcb9ARERERERxv4B3d3d3d2u/d3d3d3d3a7/g3d3d3d10vxAREREREWe/QEREREREYL+AmZmZmZlJv0BmZmZmZl4/8O7u7u7uWj/w7u7u7u5OP4BmZmZmZkY/IDMzMzMzcD8AAAAAAABoP5CIiIiIiGQ/wLu7u7u7Xz8AERERERFZPwDv7u7u7lI/YGZmZmZmWj/A3d3d3d1jP2B3d3d3d1M/AO/u7u7uVj9IRERERERQPwAiIiIiIkI/ADMzMzMzUz+AiIiIiIhQP4BmZmZmZlo/gHd3d3d3Rz+A7u7u7u5Ov4B3d3d3d0e/JCIiIiIiQr8AVlVVVVVNvwAQEREREfG+gHd3d3d3Nz+6u7u7u7s7vwBUVVVVVTU/AN7d3d3dRT8AeHd3d3cnv+/u7u7u7j6/gIiIiIiIQL8AMzMzMzNLP6CqqqqqqlI/0MzMzMzMVD+AiIiIiIhcP+DMzMzMzGo/gHd3d3d3Yz9wd3d3d3dXP4CIiIiIiFQ/AN7d3d3dTT+A7u7u7u5WPwDe3d3d3VE/gHd3d3d3Tz8AvLu7u7thv0BERERERGS/QEREREREYL9AREREREREvwDv7u7u7la/EBERERERIb9AZmZmZmZGvwDe3d3d3T2/gN3d3d3dUb94d3d3d3dTv8CZmZmZmUk/AAAAAAAAML8AIiIiIiIyPwCamZmZmSm/AJqZmZmZCT8AmJmZmZkpvyAiIiIiImS/YFVVVVVVVb9oZmZmZmZgvwBERERERES/QERERERERL8AERERERExvyAiIiIiIkK/ALy7u7u7Q7/AmZmZmZlZP4B3d3d3d08/4N3d3d3dVT8ARERERERMP4CZmZmZmV0/QERERERETD/Au7u7u7srPwBWVVVVVSU/QDMzMzMzYz+QiIiIiIhkPxIREREREWE/ABERERERVT+AiIiIiIhgPwBERERERDQ/AJiZmZmZGb9oZmZmZmZGP4CZmZmZmVG/AM3MzMzMUL+AIiIiIiJKv4B3d3d3d1e/gEREREREZr9AVVVVVVVpvwDv7u7u7lK/AAAAAAAAXL/AmZmZmZlhv2BmZmZmZma/AO/u7u7uXr/Aqqqqqqpav4CqqqqqqkI/ABERERERST8QERERERExPwCrqqqqqjo/AIiIiIiIOD9oZmZmZmY2P4B3d3d3dze/AN7d3d3dPT9ARERERERoP6CqqqqqqmQ/2N3d3d3dbT/AzMzMzMxqP1BVVVVVVXA/QFVVVVVVYz8wMzMzMzNhP4C7u7u7u1c/gGZmZmZmWr/g3d3d3d1VvwDv7u7u7k6/QDMzMzMzU7+gmZmZmZlrv3B3d3d3d2m/IiIiIiIiZL+Au7u7u7tXv6CqqqqqqmC/AAAAAAAASL+amZmZmZlRvwAAAAAAAFS/oKqqqqqqYr/w7u7u7u5gv6uqqqqqqkq/AKuqqqqqOr8AAAAAAAAwPwBUVVVVVRU/YFVVVVVVNb8A8O7u7u4+v4CIiIiIiFA/8O7u7u7uYD9wZmZmZmZGP4BERERERFQ/ADMzMzMzZb+AiIiIiIhiv4AiIiIiIlq/8O7u7u7uTr8ArKqqqqpKPwCQmZmZmQm/AO7u7u7uPj8AMzMzMzMjPwDv7u7u7mA/UFVVVVVVRT/AzMzMzMxQP8CZmZmZmVE/ANjd3d3dHT+QmZmZmZlJP8DMzMzMzEQ/AJqZmZmZKT8AAAAAAABQP8C7u7u7u0M/oKqqqqqqUj8AvLu7u7tLPwCIiIiIiDg/4O7u7u7uRj9gVVVVVVU1PwAjIiIiIjK/IDMzMzMzUz8AvLu7u7tDP3B3d3d3d1c/AHh3d3d3Tz8AAAAAAABiP/Du7u7u7lY/gHd3d3d3Rz8AERERERExvwDe3d3d3UU/oKqqqqqqSj8QERERERFRP0BERERERFg/4N3d3d3dYT+AZmZmZmZWP7i7u7u7uzs/AJmZmZmZOT8Aq6qqqqpWPwAREREREUk/AHd3d3d3Tz+4u7u7u7tDP8Dd3d3d3WU/QDMzMzMzZT9gRERERERiPxAREREREWM/QDMzMzOzgb8gIiIiIiJ8v0BERERERHe/IyIiIiIicL9ARERERESAv1BVVVVVVXe/wLu7u7u7b78gIiIiIiJsv4C7u7u7u0u/AO/u7u7uTr8AEREREREBv0BERERERFi/wLu7u7u7bT9mZmZmZmZuP2BVVVVVVWk/AAAAAAAAaj/AqqqqqqpKP4BVVVVVVVG/4N3d3d3dY79AVVVVVVVvvwAAAAAAAHK/gIiIiIiIYr+gmZmZmZlZvwAAAAAAAEC/AO7u7u7uPj8AIiIiIiIyP4CIiIiIiEC/ABARERERAb/AiIiIiIhQv4B3d3d3d0+/ABERERER8T4ANDMzMzMjvwAAAAAAAEC/AAAAAAAAML9AMzMzMzMjvwAiIiIiIlY/ALy7u7u7Sz+giIiIiIhUP3d3d3d3d1M/gLu7u7u7Sz9AZmZmZmZWP8C7u7u7u1c/JCIiIiIiYj8AVVVVVVVdPwAREREREWc/AAAAAAAAYj/w7u7u7u5gPwBEREREREw/ALy7u7u7Q78AEBEREREhPwBUVVVVVRU/oJmZmZmZST8AVVVVVVVdPwAREREREWE/ABERERERVT/g3d3d3d1NPwAREREREUE/EBERERERST/AqqqqqqpCP4CIiIiIiFA/AMzMzMzMTL9UVVVVVVVRv8Dd3d3d3U2/AERERERERL8AZmZmZmZOvwDc3d3d3R2/ALy7u7u7K78AZmZmZmY2vzAzMzMzM2m/IDMzMzMzab9YVVVVVVVlvwDv7u7u7ka/gMzMzMzMTL8AnJmZmZkJv6CZmZmZmTm/ACIiIiIiQr8ARERERERMv+Dd3d3d3UW/wN3d3d3dRT8Ad3d3d3c3PwAAAAAAAAAAAERERERENL8AERERERERvwAQEREREQG/ALy7u7u7Xz94d3d3d3djPxgREREREWE/ICIiIiIiYD+AZmZmZmZWP9DMzMzMzFg/QDMzMzMzQz+Ad3d3d3dPPwA0MzMzMzM/AImIiIiIQD+Ad3d3d3dXPwAAAAAAAFQ/AAAAAAAAML8AmZmZmZk5vwBWVVVVVSU/ABARERERET8AzczMzMxQP/Du7u7u7lI/wLu7u7u7Xz8AzczMzMw8PwA0MzMzM0s/AJiZmZmZOT8AMzMzMzNDP+D//////1c/ADMzMzMzaT/AqqqqqqpsP4BmZmZmZmQ/MzMzMzMzWz8AAAAAAAAAAACZmZmZmVE/AN7d3d3dYT+AiIiIiIhUPwAiIiIiIlo/gJmZmZmZVT8AiIiIiIhIP0BERERERDQ/ABARERERIb9gVVVVVVVFPwBmZmZmZjY/gJmZmZmZQT8AvLu7u7tDvwCqqqqqqkK/AGZmZmZmTr8AEBEREREBvwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAwvwC8u7u7u0u/AAAAAAAAAAAAAAAAAAAAAACIiIiIiFi/AO/u7u7uWr8AAAAAAAAAAAAAAAAAAAAAAKCZmZmZCb8AiIiIiIhAvwAAAAAAAAAAAJqZmZmZXT8AiYiIiIhYP8C7u7u7u1M/gEREREREbL9AMzMzMzNrv8C7u7u7u2e/4N3d3d3dYb+AMzMzMzNjv4CZmZmZmWG/AAAAAAAAML+AZmZmZmY2vwCJiIiIiEg/wKqqqqqqQj8A3t3d3d0tvwARERERETG/AFBVVVVVJb/A3d3d3d1RPwC8u7u7uzs/AAAAAAAASD9AREREREREPwAiIiIiIkI/gHd3d3d3Rz8A8O7u7u4+PwAiIiIiIlY/ADAzMzMzIz8AzczMzMxEPzgzMzMzMyM/ABERERERWb8gIiIiIiJkv8CZmZmZmVW/ACIiIiIiUr8AiYiIiIhcvwBWVVVVVUW/ADMzMzMzQ79ARERERERMv4B3d3d3d1e/wMzMzMzMWL/QzMzMzMxUvwAiIiIiIjK/ABERERERYT+Ad3d3d3dbP2dmZmZmZlY/AFRVVVVVJT8AERERERFJvwAiIiIiIkq/gJmZmZmZGT8AIiIiIiIyPwDv7u7u7mK/ABERERERY7/AmZmZmZlZv2BmZmZmZl6/YGZmZmZmcL+Ad3d3d3dhv+Du7u7u7la/AJqZmZmZUb+AmZmZmZlrv8CZmZmZmWm/gKqqqqqqZL+gmZmZmZldv4CqqqqqqmA/AAAAAAAAYj9AMzMzMzNhPwAzMzMzM0s/gHd3d3d3Yz8A3t3d3d1ZP4CIiIiIiFg/8O7u7u7uWj+Ad3d3d3dbPyBERERERFQ/cHd3d3d3Uz8AVVVVVVVNPwCJiIiIiEC/wJmZmZmZKT/g3d3d3d1FPwBQVVVVVRU/AERERERENL8AERERERFBv+Du7u7u7j6/gIiIiIiIUL8AvLu7u7tDv4Cqqqqqqkq/ABARERER8T4ANDMzMzMjP8C7u7u7u0M/AN7d3d3dRT8AVlVVVVUVPwAiIiIiIkK/AODd3d3dLT8AAAAAAABAPwAyMzMzMzM/0MzMzMzMTD9AZmZmZmZkPwAAAAAAAFw/AO/u7u7uTj8AERERERFRPwDv7u7u7mY/AO/u7u7uXj+Ad3d3d3djP9DMzMzMzFA/gGZmZmZmRj8AMjMzMzMjv8Du7u7u7j4/ACIiIiIiVj+AZmZmZmZePwAAAAAAAGI/aGZmZmZmUj8AZmZmZmZOPwBUVVVVVRU/AN7d3d3dLT+QiIiIiIhIPwCcmZmZmSk/gN3d3d3dUb8AzczMzMxYv0BERERERFC/QBERERERUb+ARERERERUvwAAAAAAAFC/ERERERERQb8AaGZmZmY2v4CZmZmZmVm/YHd3d3d3V7/g3d3d3d1dvwDNzMzMzFy/0MzMzMzMUL8AMzMzMzNDvwB4d3d3dyc/AJiZmZmZKb/AzMzMzMxEPwCrqqqqqjo/AFhVVVVVFb8AvLu7u7srv4CIiIiIiFw/gGZmZmZmTj8AAAAAAABUPwDc3d3d3S0/IiIiIiIiWj8AVVVVVVVNPwAAAAAAAFA/AM3MzMzMWD+Ad3d3d3dTP0BERERERFA/gGZmZmZmRj8AVlVVVVU1P5iZmZmZmUm/ABARERERIb/AiIiIiIhIPwBGRERERDQ/AJiZmZmZKb8AERERERFRv4CIiIiIiEi/wMzMzMzMUL8AAAAAAABIP+Dd3d3d3U0/ALy7u7u7Kz+Au7u7u7tLP4CZmZmZmWe/IDMzMzMzY78AERERERFdv0BERERERFi/AN7d3d3db79AIiIiIiJqvwAREREREWG/YFVVVVVVVb9AZmZmZmZkv9DMzMzMzGC/IBERERERVb/AmZmZmZlVv7Cqqqqqqlq/ACIiIiIiVr8AmZmZmZk5vwAiIiIiIkK/wKqqqqqqVr9ARERERERcvxAREREREVW/AODd3d3dHT8gIiIiIiJmvwAzMzMzM1u/AN7d3d3dXb8Aq6qqqqpav3Z3d3d3d2W/gJmZmZmZXb8AmpmZmZk5vwDNzMzMzES/0MzMzMzMVL8AAAAAAABYv6CZmZmZmVW/gLu7u7u7W78AVVVVVVVdvwB3d3d3d0e/ADQzMzMzS78AMzMzMzMzvwA0MzMzMyO/AAAAAAAAMD9AERERERFBvwAQERERERE/AHh3d3d3R78AERERERFJvwB3d3d3d0e/QFVVVVVVNb/g3d3d3d1hP+DMzMzMzFA/8O7u7u7uUj+A3d3d3d1RPwCqqqqqqko/AN7d3d3dTT+AiIiIiIhQP8C7u7u7u0M/ANjd3d3dLT8AeHd3d3cnPwCYmZmZmRk/wMzMzMzMTD9AIiIiIiJWP2BmZmZmZlI/gIiIiIiIOD8AEBERERExP0BERERERFS/gGZmZmZmRr8Au7u7u7srvwBQVVVVVRU/REREREREUD8AkJmZmZkJP4C7u7u7u0s/AFhVVVVVFb9AMzMzMzNTP8DMzMzMzFw/QERERERERD+ARERERERcP8CZmZmZmVE/AM3MzMzMPD9AZmZmZmY2PwDNzMzMzEQ/vLu7u7u7Zz8AAAAAAABYP8C7u7u7u1c/gIiIiIiIUD+8u7u7u7tLPwBUVVVVVSW/ADQzMzMzIz8AIiIiIiJCPzAzMzMzMzO/AODd3d3dHT+AVVVVVVVFPwBERERERDQ/AFRVVVVVTb+AiIiIiIhUvwAiIiIiIlK/QBERERERQb8AIiIiIiJuv0BERERERG6/oJmZmZmZY7+gmZmZmZllvwAQERERETG/gIiIiIiIUL8A3N3d3d0tPwCamZmZmRk/wLu7u7u7ZT+gqqqqqqpaP8C7u7u7u0s/ADMzMzMzQz8AIiIiIiJeP0BVVVVVVWU/QDMzMzMzYT+oqqqqqqpiPwBWVVVVVUU/AJiZmZmZKT8AVlVVVVU1vyAzMzMzMzM/gGZmZmZmTj/AqqqqqqpKP2BVVVVVVVE/ACARERER8T6Ad3d3d3dbvyAiIiIiIl6/oIiIiIiIQL8AeHd3d3c3PwAREREREQG/AFVVVVVVRT8AAAAAAABAPwAQEREREQG/QHd3d3d3Tz/A7u7u7u5SP+Du7u7u7lI/AERERERETD80MzMzMzNhPwDNzMzMzGA/wKqqqqqqXj8AqqqqqqpKP/Du7u7u7l4/QDMzMzMzVz/AiIiIiIhcPwDv7u7u7lo/AAAAAAAAAAAAEBEREREhv4Cqqqqqqkq/ADMzMzMzQ78AEBERERERv8C7u7u7u0s/VFVVVVVVUT8AIiIiIiJWPwCamZmZmUE/AJyZmZmZKT8AvLu7u7tLvwARERERESG/YGZmZmZmc79QVVVVVVVzv1BVVVVVVW2/YGZmZmZmar8AERERERFdv8Dd3d3d3WG/gJmZmZmZVb9AMzMzMzNDv4CIiIiIiGq/AAAAAAAAXL+wqqqqqqpavwBVVVVVVVm/ALy7u7u7O78AiYiIiIhIPwBEREREREQ/MDMzMzMzMz8AIyIiIiJaP4BERERERFg/AAAAAAAAVD8AEREREREBPwC8u7u7uzs/ABERERERUT+QmZmZmZlRPwA0MzMzM0s/AAAAAAAAAAAA3t3d3d11P0BVVVVVVXk/AAAAAAAAcj8AAAAAAAByP8DMzMzMzGw/0MzMzMzMaD8gMzMzMzNjPwBmZmZmZk4/AICZmZmZCb8AJCIiIiIyPwC8u7u7uyu/AFhVVVVVFT8wMzMzMzMzv2BmZmZmZjY/AJqZmZmZOb8AAAAAAAAAAAAgEREREQE/AEREREREND8A3t3d3d09PwAiIiIiIl4/AO/u7u7uXj9AVVVVVVVRP4AiIiIiIjI/QDMzMzMzYz/g3d3d3d1dP9C7u7u7u2E/QCIiIiIiVj/AiIiIiIhiP8jMzMzMzFA/4MzMzMzMRD8Aq6qqqqpCPwCJiIiIiFA/QEREREREUD8AiIiIiIg4PwAzMzMzM0s/ABARERERET8QERERERERv0BERERERES/ACIiIiIiQr+AiIiIiIhgvyAiIiIiIla/gHd3d3d3T78AMzMzMzNLvwAyMzMzMzO/YGZmZmZmVr/g3d3d3d1Vv4CZmZmZmUm/4N3d3d3dVb8AAAAAAABQv4C7u7u7u0O/gGZmZmZmUr9AREREREREv4Du7u7u7k6/AO/u7u7uRr8AEBERERERP0BVVVVVVWU/0MzMzMzMYD/w7u7u7u5WPwCqqqqqqkI/UEREREREND8AREREREREPwAiIiIiIlI/AHd3d3d3Rz+wu7u7u7thP4Du7u7u7k4/QEREREREUD8AvLu7u7srPwDe3d3d3VE/AO/u7u7uVj/AzMzMzMw8PwB3d3d3d0c/ADQzMzMzM7+Au7u7u7s7vwDe3d3d3UW/ALy7u7u7K78AAAAAAABIP4BVVVVVVSW/QERERERERD+AREREREREPwCJiIiIiDi/wKqqqqqqQr+AmZmZmZlBvwBUVVVVVSW/ADIzMzMzSz8AiYiIiIhIP4BmZmZmZlI/rKqqqqqqQj8AMjMzMzMzv4AzMzMzM0u/ALy7u7u7S78AVVVVVVU1PwAAAAAAAAAAALy7u7u7O78A3t3d3d1Rv4CIiIiIiEi/AKCZmZmZCT8Aqqqqqqo6vwBmZmZmZkY/AHZ3d3d3Jz+AZmZmZmZSP0BVVVVVVTW/MDMzMzMzM7+AqqqqqqpSvwBYVVVVVSW/ACIiIiIiQr8A3t3d3d1FvxARERERERE/AAAAAAAAAAAAAAAAAAAAAAAREREREVG/AJqZmZmZSb8AAAAAAAAAAACamZmZmUm/ABARERERMb8ARERERERMvwAAAAAAAAAAAN7d3d3dRb8A4N3d3d0dvwAgEREREfG+AGRmZmZmNj8AMzMzMzNXP4B3d3d3d1M/4O7u7u7uUj9AMzMzMzNpv4CIiIiIiGq/cHd3d3d3Zb+AVVVVVVVNv0AiIiIiImK/oJmZmZmZZb+AVVVVVVVZv8DMzMzMzFy/4N3d3d3dcL+4u7u7u7tpv1BVVVVVVV2/gCIiIiIiVr8AAAAAAAB5v2BmZmZmZna/UFVVVVVVcb/g3d3d3d1vvyAiIiIiIma/ABERERERWb+Ad3d3d3dTvwCqqqqqqkK/wN3d3d3da7/Au7u7u7tjvyAiIiIiImK/gIiIiIiISL8AAAAAAABUP1hVVVVVVVE/3N3d3d3dUT8AqqqqqqpKP4C7u7u7u2E/gIiIiIiIVD/AzMzMzMxYP4CZmZmZmWE/AO/u7u7uTj9AZmZmZmZSP8CZmZmZmVE/AO/u7u7uRj9gZmZmZmZkP8C7u7u7u18/cHd3d3d3Yz+AzMzMzMxYPwDNzMzMzDy/AERERERENL8AVVVVVVUlvwAzMzMzM0O/ACIiIiIiaL+Ad3d3d3dfvwAzMzMzM0u/AIiIiIiIOL8AmpmZmZkZvwDe3d3d3S0/AAAAAAAAML8AZmZmZmY2v4B3d3d3d2+/wO7u7u7uZL/A3d3d3d1hv0BVVVVVVU2/AAAAAAAAcL9YVVVVVVVpv1hVVVVVVWe/AM3MzMzMWL8AEhERERFJvwCamZmZmTm/AKuqqqqqOj8AvLu7u7tDvwAAAAAAAFA/AFVVVVVVTT+AmZmZmZlVPwAREREREV0/YEREREREaj9ARERERERoPzAzMzMzM2M/gHd3d3d3Vz8AAAAAAABiP2BmZmZmZmA/QDMzMzMzYz+AiIiIiIhUP4AREREREV0/wLu7u7u7Vz+gmZmZmZlRPwAQERERERG/AAAAAAAAMD8A3t3d3d1NPwBmZmZmZjY/AO/u7u7uRj8A3t3d3d1lP0BERERERGQ/ACIiIiIiWj8A3t3d3d1NP4B3d3d3d2M/zMzMzMzMZD80MzMzMzNlP0BERERERGA/ADMzMzMzYT8AAAAAAABYPwAREREREUk/AKuqqqqqSj/g3d3d3d1VP8Dd3d3d3VE/AAAAAAAAWD8AzczMzMxEPwC8u7u7uyu/gHd3d3d3R78A3t3d3d0tvwDg3d3d3S0/wLu7u7u7Zb/AmZmZmZlRv2BERERERFi/gFVVVVVVUb/AzMzMzMx5v2hmZmZmZnC/wLu7u7u7Z79gRERERERiv0BERERERGa/YHd3d3d3W78IAAAAAABUv4AREREREVW/oJmZmZmZYb8A7+7u7u5Ov+Du7u7u7lK/AJiZmZmZOb+AVVVVVVU1vwCamZmZmUE/AHh3d3d3J78AEBERERExP6CZmZmZmUk/gHd3d3d3Vz+gqqqqqqpgP4DMzMzMzFw/AJqZmZmZGT8AmJmZmZkZvwA0MzMzMyO/AKqqqqqqQr/Au7u7u7thP4B3d3d3d2E/wMzMzMzMXD+AiIiIiIhYP6CZmZmZmUE/AJqZmZmZQT8Aq6qqqqo6PwDNzMzMzFg/AFVVVVVVJb8ANDMzMzMzPwCamZmZmSm/AHh3d3d3J7+AMzMzMzNrvwDv7u7u7l6/gDMzMzMzS78Aq6qqqqo6vwDe3d3d3VE/AAAAAAAAVD9gZmZmZmZOPwAzMzMzM0M/gGZmZmZmXj+wqqqqqqpoP8DMzMzMzFw/AO/u7u7uYD8AvLu7u7tvP0AzMzMzM20/oLu7u7u7Yz+gqqqqqqpiP8Dd3d3d3Wk/VVVVVVVVXT/w7u7u7u5eP4CZmZmZmV0/mJmZmZmZYz8AIiIiIiJaPwAREREREVE/gBERERERWT/Au7u7u7tDP4CIiIiIiFA/QFVVVVVVVT8A3t3d3d1FP8C7u7u7u0O/AKuqqqqqUr+AZmZmZmZGvwBERERERDQ/wMzMzMzMTL8AiYiIiIhAvwCJiIiIiDi/AERERERETL/Au7u7u7thv4BVVVVVVV2/gIiIiIiIQL8AMzMzMzNLvwAiIiIiImA/AERERERERD8AVlVVVVU1P4AiIiIiIjI/gKqqqqqqUj/g3d3d3d1RP/Du7u7u7lY/ABERERERWT8AvLu7u7thv4CIiIiIiFy/wMzMzMzMVL/AqqqqqqpKvwCJiIiIiDg/AKuqqqqqOr9AREREREREPwCYmZmZmRk/QEREREREYr+Ad3d3d3dfv0AzMzMzM1u/ADMzMzMzS7+Ad3d3d3dvv0AzMzMzM2m/ABERERERZb/AzMzMzMxcv4BmZmZmZla/QFVVVVVVVb+AMzMzMzNDvwB4d3d3dyc/QEREREREUD+AiIiIiIhAPwCamZmZmTk/AKCZmZmZGb9ARERERERQPwBYVVVVVSU/gIiIiIiIVD8AZmZmZmZOP3h3d3d3d2U/gIiIiIiIWD8A3t3d3d1NPwAAAAAAAEA/gIiIiIiIVL8AmJmZmZkpvwAzMzMzMzO/ACIiIiIiMj9AVVVVVVVFvwCamZmZmTm/AKuqqqqqOr8AmpmZmZlBv4B3d3d3dye/APDu7u7uPr8A3t3d3d0tPwC7u7u7u0O/EBERERERXb+AMzMzMzNbv4Du7u7u7ka/AIiIiIiIOL8gIiIiIiJyv+DMzMzMzGq/QEREREREaL/Au7u7u7tjvwBVVVVVVVm/8O7u7u7uRr8AAAAAAAAAAADNzMzMzES/gHd3d3d3b7+AZmZmZmZsv4B3d3d3d1+/ICIiIiIiZr9gd3d3d3dlv0AiIiIiIlq/QCIiIiIiQr8AREREREQ0vwC8u7u7u2G/gLu7u7u7X7+AZmZmZmZavyAzMzMzM1e/AAAAAAAASL8AVlVVVVVFPwAREREREUE/gJmZmZmZOT+AREREREREPwCamZmZmTk/ABAREREREb8ArKqqqqo6vwC7u7u7uzs/AFVVVVVVJT/AzMzMzMxEPwDY3d3d3R0/4N3d3d3dTb8AERERERFRvwBFRERERDS/ABARERERIT8AAAAAAAAAAADe3d3d3S0/AFRVVVVVFT8AMDMzMzMjv9DMzMzMzEw/AM3MzMzMUD9ARERERERYPwCqqqqqqkI/eHd3d3d3az+gmZmZmZllPwDv7u7u7lo/AN7d3d3dRT+sqqqqqqpWP4CqqqqqqlY/gHd3d3d3Tz8A3d3d3d1NP8Dd3d3d3VE/ACIiIiIiSj8A3t3d3d0tvwCYmZmZmSm/AO/u7u7udr+Ad3d3d3dvv8DMzMzMzGS/iIiIiIiIYr+AIiIiIiJSvwAAAAAAAFS/YEREREREVL8A7+7u7u5Sv4CZmZmZmW+/wN3d3d3dab9AVVVVVVVnv3h3d3d3d2e/ICIiIiIibL/Au7u7u7tnv9DMzMzMzGK/gKqqqqqqUr8AzczMzMxYvwDv7u7u7k6/AN7d3d3dUb8AERERERFBvwBQVVVVVSW/AO7u7u7uPj8Auru7u7s7PwAQEREREfG+QFVVVVVVUT8AmZmZmZkpPwB3d3d3dyc/ABARERERAb8AAAAAAAAAAACamZmZmVE/AHh3d3d3Rz+AiIiIiIhUPwAzMzMzM1M/oIiIiIiISD8AEhEREREBvwAgEREREfE+AICZmZmZCb8AAAAAAAAAAACcmZmZmTk/ADMzMzMzMz8AIiIiIiJSPyARERERETE/4N3d3d3dHT8AdHd3d3cnPwAAAAAAAAAAAGBVVVVVFb8ARkREREREPwAAAAAAADA/gGZmZmZmZL9AZmZmZmZqv+Dd3d3d3WG/ABERERERUb8AIiIiIiJKvwCZmZmZmSm/gJmZmZmZSb+AmZmZmZlJv0BERERERHW/7+7u7u7ucb/w7u7u7u5kv8C7u7u7u2W/ALy7u7u7O78AvLu7u7tLvwDe3d3d3S2/gGZmZmZmTr8A3d3d3d1FPwCZmZmZmQm/wN3d3d3dLb8AABERERHxvgC8u7u7uyu/AN7d3d3dRb9gZmZmZmZGvwAgEREREfE+gKqqqqqqUr8QERERERFdv3B3d3d3d0+/AERERERETL/w7u7u7u5Wv4C7u7u7u1+/QDMzMzMzV7+AiIiIiIhUv9Dd3d3d3VG/gHd3d3d3T78AVlVVVVUVPwAyMzMzMzO/AM3MzMzMPD8AEBERERHxPgCcmZmZmQk/gDMzMzMzVz+wu7u7u7tXP4BVVVVVVVE/gJmZmZmZST8AiYiIiIhAP4B3d3d3dzc/AImIiIiIOD/g3d3d3d1ZPwDe3d3d3VU/gJmZmZmZVT+gmZmZmZlRPwDe3d3d3S0/AKCZmZmZGb+AmZmZmZlRvwARERERESG/wMzMzMzMRL8A3t3d3d0tPwC8u7u7uzs/ICIiIiIiMj8AmpmZmZkJvwAAAAAAAEA/gHd3d3d3Vz8AIiIiIiJKP8C7u7u7u1c/AMzMzMzMTD8ARERERERcP4CZmZmZmV0/gKqqqqqqXj8wMzMzMzNhP0AzMzMzM28/oKqqqqqqbD/gzMzMzMxoP+Dd3d3d3WE/AAAAAAAAAAAAmJmZmZk5PwBWVVVVVUU/wMzMzMzMUD8AAAAAAABgPwAREREREWE/gHd3d3d3Wz8AIiIiIiJCPwAzMzMzMzM/AN7d3d3dLT/v7u7u7u5SPwB4d3d3d08/AAAAAAAAAAAAdHd3d3cnvwDMzMzMzDy/+P//////T78AAAAAAAAAAAAAAAAAAAAAABARERERET8AIBERERHxPgAAAAAAAAAAACMiIiIiUr+AiIiIiIhgvwAREREREVW/AAAAAAAAAAAAABEREREBPwCgmZmZmRm/ADQzMzMzM78ANDMzMzNLPwDs7u7u7j4/AImIiIiISD+AmZmZmZk5P8C7u7u7u2E/oIiIiIiIYj8AAAAAAABUPwDNzMzMzFQ/AGZmZmZmRj8AzczMzMw8P4Dd3d3d3UU/ABERERERST8AAAAAAABkv/Du7u7u7mK/SEREREREVL8AAAAAAABcv0BERERERHe/wLu7u7u7dL8AAAAAAABqv6CZmZmZmWG/2N3d3d3dYb/Aqqqqqqpev8C7u7u7u1u/AFVVVVVVXb9AMzMzMzNzvwAAAAAAAGi/ICIiIiIiZL+AiIiIiIhUvwAREREREUG/VFVVVVVVUb/w7u7u7u5GvwB2d3d3dze/AM3MzMzMYL9gZmZmZmZiv8CZmZmZmVG/wIiIiIiIVL8QERERERFhv4CZmZmZmV2/AM3MzMzMPL8AmZmZmZlBv4C7u7u7u0u/gDMzMzMzQ7/AqqqqqqpCvwDe3d3d3T2/AAAAAAAAYL+Ad3d3d3dXv4BVVVVVVU2/AFZVVVVVRb8AMzMzMzNbvwAAAAAAAFS/QEREREREVL9AIiIiIiJSvyAiIiIiIl6/gGZmZmZmUr8AMzMzMzNDvwAgEREREfE+AODd3d3dHb8AvLu7u7tDv4CIiIiIiFi/gIiIiIiIVL9AIiIiIiJsv0BERERERGi/mJmZmZmZYb8A7+7u7u5WvwDNzMzMzFy/QDMzMzMzYb+AiIiIiIhYv4CqqqqqqlK/QEREREREUL8AAAAAAABIvwBUVVVVVRW/AM7MzMzMPL8A7+7u7u5gP8DMzMzMzFA/YGZmZmZmUj8AvLu7u7tbPyAzMzMzM2U/oLu7u7u7YT+AmZmZmZlVPwDNzMzMzEw/AAAAAAAAWL8AERERERExv4CIiIiIiEA/AJiZmZmZCb8A3t3d3d09PwAQEREREfE+AODd3d3dHb8AAAAAAAAwvwAAAAAAAEC/ABARERERMT8A4N3d3d0tv4CIiIiIiFA/gMzMzMzMWD+YmZmZmZldP4iIiIiIiFQ/gFVVVVVVUT+AZmZmZmZiP0BERERERGQ/IDMzMzMzYT9gZmZmZmZgP6Cqqqqqqlo/QEREREREUD8AERERERFBP4CIiIiIiFQ/QEREREREaj/AzMzMzMxoP/Du7u7u7mQ/QEREREREYD+ARERERERMPwAREREREUE/gIiIiIiIOD8AAAAAAABcPwC8u7u7uyu/QEREREREVD8AZmZmZmY2PwCYmZmZmRk/ADQzMzMzI78AVVVVVVUVv4CIiIiIiEg/AKuqqqqqQj9ARERERERUv8CqqqqqqlK/gIiIiIiIUL8AqqqqqqpKvwAzMzMzMzO/AHh3d3d3Rz8AzczMzMxEPwBVVVVVVUU/aGZmZmZmZj8A7+7u7u5ePwAREREREVk/AFVVVVVVWT9oZmZmZmZmP0BERERERGA/oJmZmZmZYz+AqqqqqqpkP7C7u7u7u18/gGZmZmZmUj+AiIiIiIhIPwDd3d3d3U0/QEREREREUD8AiYiIiIhIPwAAAAAAAFA/AM3MzMzMUD/Au7u7u7tbv4AzMzMzM1+/gIiIiIiIUL8AvLu7u7srvwAREREREVU/wMzMzMzMWD+Ad3d3d3dPP4CIiIiIiEg/wMzMzMzMYD+cmZmZmZlhPzMzMzMzM2E/AO/u7u7uWj/AzMzMzMxqP+Dd3d3d3WE/AAAAAAAAYj8AIiIiIiJaP4DMzMzMzGI/gIiIiIiIZD/AzMzMzMxcP6Cqqqqqql4/ABQRERERIb+JiIiIiIhAv4CZmZmZmSm/AODd3d3dHT/g3d3d3d1hP4B3d3d3d1s/wLu7u7u7Vz8A3t3d3d1NP4BmZmZmZk4/gLu7u7u7Qz8AERERERFBPwCamZmZmVE/gIiIiIiIQL8AzMzMzMw8vwDg3d3d3R0/ADIzMzMzMz8gIiIiIiJavwCamZmZmUm/ALy7u7u7Q78AvLu7u7srv8C7u7u7u2G/gFVVVVVVWb/AqqqqqqpWv4C7u7u7u1e/AJiZmZmZOb8AVFVVVVUVv4CIiIiIiEg/gGZmZmZmRj8AvLu7u7srv8C7u7u7u0u/wLu7u7u7S7/Au7u7u7tTvwBVVVVVVW2/gCIiIiIiXr+Ad3d3d3dXv4B3d3d3dze/AJqZmZmZOT8Aq6qqqqo6PwC8u7u7uys/ACIiIiIiQj8AERERERFlv4B3d3d3d2G/QEREREREUL9AIiIiIiJKvwDv7u7u7na/IBERERERdL8gERERERFtv8C7u7u7u1u/AAAAAAAAZL9AMzMzMzNXvyAREREREVW/AGdmZmZmTr+A3d3d3d1FvwAzMzMzMzO/AJqZmZmZKb8AERERERFBP2BmZmZmZmI/gHd3d3d3Yz/AzMzMzMxgP4CIiIiIiFA/ICIiIiIiaj+AiIiIiIhmP8C7u7u7u2M/QGZmZmZmZD9QVVVVVVVnP0AREREREV0/gHd3d3d3Wz8AERERERFJP2BEREREREQ/gJmZmZmZUT+AmZmZmZlRPwAAAAAAAFw/QERERERENL8AEhERERExv4B3d3d3d0+/AFBVVVVVFT/IzMzMzMxYvwC8u7u7u1O/AERERERENL8Ad3d3d3dHv2BERERERHW/MDMzMzMzcr8AERERERFtv+DMzMzMzFy/wKqqqqqqZL+IiIiIiIhiv7CqqqqqqmC/QBERERERXb+AERERERFpvwAAAAAAAGa/AAAAAAAAUL9gVVVVVVVVvwDv7u7u7mA/AAAAAAAAYj/Q3d3d3d1ZPwDMzMzMzDw/AFVVVVVVVT8A7+7u7u5SPwAiIiIiIlI/wLu7u7u7Uz8AIBEREREBPwB4d3d3d0+/AO/u7u7uTr8AMzMzMzNDvwC8u7u7uyu/gLu7u7u7Qz8AAAAAAABIPwCrqqqqqko/ALy7u7u7O78AMzMzMzNLv4CIiIiIiFS/AN7d3d3dPb8gIiIiIiJCPwDNzMzMzEQ/ABERERERUT8AVlVVVVU1P0BERERERES/gDMzMzMzQ78A7+7u7u4+vwDNzMzMzEQ/SEREREREWD8A3t3d3d1VP4Cqqqqqqko/AKqqqqqqOj+gqqqqqqo6P4C7u7u7u0s/gGZmZmZmUj8AERERERFRP8C7u7u7uzu/ABERERERQb8AAAAAAABIvwBGRERERDS/AN7d3d3dPT9AVVVVVVVRP6CZmZmZmUk/AEREREREVD8AAAAAAABUvwCamZmZmUm/gFVVVVVVVb/g7u7u7u5GvwAiIiIiIl6/IBERERERUb8AERERERFBvwCYmZmZmRk/gKqqqqqqdr9gZmZmZmZzv0BVVVVVVWu/AAAAAAAAZL/g3d3d3d1lv6CZmZmZmV2/EBERERERVb8A7+7u7u5OvwCZmZmZmVG/ADMzMzMzQ78AUFVVVVUVv4CIiIiIiEA/AAAAAAAAYD8A7+7u7u5iP4BmZmZmZl4/gGZmZmZmTj8AVVVVVVU1P8DMzMzMzFA/4N3d3d3dWT8AzczMzMxUPwAAAAAAAAAAAJiZmZmZQT8AIiIiIiJSP8DMzMzMzFA/gHd3d3d3Uz/QzMzMzMxcP+Dd3d3d3VU/gEREREREUD8ARkRERERMvwAyMzMzM0u/AFZVVVVVRb+A7u7u7u5GvwCsqqqqqjo/QEREREREND+IiIiIiIhIPwBFREREREw/AAAAAAAAAAAAUFVVVVUVvwCamZmZmUk/AJqZmZmZOT8AAAAAAAByPwAREREREXE/4O7u7u7ubj+QiIiIiIhoP4CIiIiIiHE/0MzMzMzMaD+AiIiIiIhmP6CZmZmZmWU/ACIiIiIibD/e3d3d3d1lPygiIiIiImA/gFVVVVVVTT8A7+7u7u5WPwDv7u7u7lY/wKqqqqqqXj/AmZmZmZlZPwAREREREVE/0MzMzMzMRD+AmZmZmZkJvwDg3d3d3R0/gHd3d3d3R78AEhERERERP4BVVVVVVSU/ABARERERAb8AnJmZmZkpP5iZmZmZmSk/gJmZmZmZOb8AEBERERERP+Dd3d3d3Vm/wIiIiIiIXL/Au7u7u7tTvwC8u7u7u0u/gIiIiIiIUL8AzczMzMxEv8CZmZmZmUm/AKuqqqqqQj9ARERERERUP8Du7u7u7lY/QDMzMzMzSz8AERERERFJPyBERERERFg/ABERERERUT8AERERERFVPwDe3d3d3VE/iIiIiIiIbD+AmZmZmZlnP8CqqqqqqmA/gFVVVVVVUT/AzMzMzMxQP0AiIiIiIlI/wMzMzMzMVD8AREREREREP4CIiIiIiEg/AHh3d3d3Nz8A3t3d3d0tvwAAAAAAADC/AJqZmZmZKT+Ad3d3d3cnP9DMzMzMzDw/AGdmZmZmRj/e3d3d3d1RPwBEREREREw/AM3MzMzMRD8AERERERFJP4CZmZmZmWU/AFVVVVVVWT/AmZmZmZlZP0BERERERFQ/ABAREREREb8AERERERFRvwBVVVVVVSW/AMzMzMzMPD+AZmZmZmZkv8CIiIiIiGK/AN7d3d3dWb9gZmZmZmZWv0AzMzMzM1+/oJmZmZmZWb8gIiIiIiJCvwCgmZmZmQm/AJqZmZmZST8ARURERERMPwAQEREREQG/AAAAAAAAQL/IzMzMzAyXv/Du7u7uLpa/AAAAAACAkL8wMzMzM7OIv2BmZmZmZpa/0MzMzMzMkr/QzMzMzMyNvwwRERERkYe/ICIiIiKilb/OzMzMzIyQvyAiIiIiIoq/yMzMzMzMgb80MzMzMzN7v8C7u7u7u3e/kJmZmZmZb79AIiIiIiJovwCamZmZmUk/AKqqqqqqQj8Aq6qqqqpWP+Dd3d3d3VE/gMzMzMzMWL/AzMzMzMxMv4BVVVVVVTW/gGZmZmZmVj+AVVVVVVV1PyAiIiIiInM/IBERERERcT+wqqqqqqpmPwDv7u7u7mY/oJmZmZmZYz8wMzMzMzNnP0AzMzMzM2U/8O7u7u7uaD8gMzMzMzNlPwAAAAAAAGI/AN7d3d3dWT+gmZmZmZldP0BVVVVVVWE/AAAAAAAAYD/Au7u7u7thP6CqqqqqKoe/AAAAAAAAhL/w7u7u7u5/vyAiIiIiIni/AO/u7u7udb+QiIiIiIhkvwAAAAAAAFi/gIiIiIiISL8AZmZmZmZWv8Dd3d3d3VW/AAAAAAAAVL8AVFVVVVUVv4DMzMzMzEw/IBERERERUT8AAAAAAABUPwAzMzMzM1M/wKqqqqqqaj9ARERERERsP5CZmZmZmWU/gIiIiIiIaj/IzMzMzMxoP2BmZmZmZmg/gIiIiIiIZD8AIiIiIiJaP4B3d3d3d1s/ICIiIiIiYD/w7u7u7u5kP0AzMzMzM2E/AO/u7u7uTj9gRERERERUP0AzMzMzM0M/gMzMzMzMRD8AAAAAAABIv5CZmZmZmUk/ICIiIiIiQj8AAAAAAABUPwAiIiIiIjK/gDMzMzMzMz+Au7u7u7srvwCamZmZmTk/ACIiIiIiSj/Au7u7u7tXPwAREREREWE/gHd3d3d3YT8AAAAAAABoP0AzMzMzM2U/kIiIiIiIXD+AmZmZmZldP4CIiIiIiGw/YEREREREaj/g3d3d3d1rPwAAAAAAAGA/gHd3d3d3Uz8gERERERFRP3h3d3d3d1M/gGZmZmZmWj/Au7u7u7srvwBERERERDQ/ABERERERMT8AWFVVVVUVvwCcmZmZmQm/ABARERERIb8AERERERFVPwBVVVVVVU0/0MzMzMzMWD8AzczMzMxYP0BEREREREQ/ADIzMzMzMz8QERERERFwP1BVVVVVVXY/AAAAAAAAcD/Au7u7u7tpP3d3d3d3t5Q/NDMzMzMzkT8AAAAAAICKP1BVVVVVVYU/EhERERFRkz94d3d3d/ePPzQzMzMzs4g/sKqqqqoqhD94d3d3d3d+P6CZmZmZmXU/8O7u7u7ucD8gIiIiIiJwPxAREREREWU/QDMzMzMzZz+gmZmZmZljP4CZmZmZmVU/gN3d3d3dTb+AiIiIiIhIvwAREREREQG/AJCZmZmZCb8oIiIiIiJCvwBoZmZmZja/AAAAAAAASL8AzczMzMxEv9zd3d3d3Wu/QGZmZmZmYr/AqqqqqqpWvwDv7u7u7la/ICIiIiIiVr9AVVVVVVVRv4B3d3d3d1O/gHd3d3d3U78AmpmZmZlJPwDNzMzMzFw/wLu7u7u7Uz8A3t3d3d1FPwDe3d3d3UU/EhERERERMT8A3t3d3d0tPwAQEREREfG+4N3d3d3deT+8u7u7u7t2PwAAAAAAAHI/AAAAAAAAbD/AmZmZmZlhP/Du7u7u7lI/wMzMzMzMUD/A3d3d3d1VP8C7u7u7u0M/AM3MzMzMRD8gIiIiIiJWPwB4d3d3dyc/QFVVVVVVZ7+gu7u7u7tfvyIiIiIiIlK/AAAAAAAASL8AWFVVVVUlPwBVVVVVVUU/AN7d3d3dPT8iIiIiIiIyv1BVVVVVVYC/0MzMzMzMf79ARERERER5vwAAAAAAAHK/MDMzMzPzmr9oZmZmZqaWv/z//////5G/EhERERERjL+IiIiIiIiHv4qIiIiIiIG/mZmZmZmZd79AMzMzMzNzvwAiIiIiIl6/eHd3d3d3Yb8QERERERFZvwC8u7u7u0u/AN7d3d3dRb8AzczMzMxEvwAAAAAAAEi/AN7d3d3dPb+AqqqqqqpwPwAAAAAAAGw/QCIiIiIiYj8AAAAAAABqPwBWVVVVVV0/QGZmZmZmYD+AZmZmZmZWP0RERERERFA/8O7u7u7uhL/g3d3d3d1+v2BVVVVVVXW/IBERERERbb+AMzMzMzNjv4Cqqqqqql6/QBERERERXb9gRERERERcvwCIiIiIiFQ/AEREREREND+AiIiIiIhQP7CqqqqqqlI/gFVVVVVVZz8gIiIiIiJiP8DMzMzMzFg/QDMzMzMzUz8AREREREREPwAQEREREQE/gLu7u7u7Sz+AiIiIiIhQPwDe3d3d3T2/AAAAAAAAUL9AVVVVVVVNvwBWVVVVVTW/ADMzMzMzM78AmpmZmZk5v0AiIiIiIjI/AJiZmZmZGT8A7+7u7u5WPwAAAAAAAFA/qKqqqqqqVj+Ad3d3d3dbPwAREREREV0/oKqqqqqqUj8AVVVVVVVNP4AiIiIiIkI/AM3MzMzMUD8gIiIiIiJKP8DMzMzMzFw/wKqqqqqqUj8AAAAAAABcP8DMzMzMzFw/gGZmZmZmNj8AAAAAAAAwvwAAAAAAADC/ALy7u7u7Q78ARUREREREPyAREREREUE/IEREREREZD8wMzMzMzNhP0BERERERFA/AN7d3d3dUT/Au7u7u7tpP+DMzMzMzGY/UFVVVVVVYz9ARERERERkPwAREREREUk/AN7d3d3dLT/g3d3d3d0tvwB0d3d3dyc/YGZmZmZmWr/Au7u7u7tTvwDNzMzMzES/AM3MzMzMRL/w7u7u7u5iv0AiIiIiImC/gLu7u7u7Q78AoJmZmZkJv8C7u7u7u1O/gIiIiIiISL8AEREREREhvwAREREREUm/AAAAAAAAcT/Q3d3d3d1rP7i7u7u7u2s/AAAAAAAAZD8gERERERExvwCYmZmZmQk/AHh3d3d3J78AeHd3d3c3v8C7u7u7u2G/gGZmZmZmRr8A3t3d3d0tvwAQEREREQE/mpmZmZmZKT8AEBERERERvwAREREREUG/ALy7u7u7K78QERERERFdv0AiIiIiIlK/wKqqqqqqSr8AWFVVVVUVvwCYmZmZmQm/gKqqqqqqOr9AERERERERvwBYVVVVVSU/ZGZmZmZmTj8A7+7u7u5OP4BVVVVVVVE/ADMzMzMzQz/c3d3d3d1dPwCamZmZmVk/wMzMzMzMYD+A7u7u7u5aP3x3d3d3d3A/AAAAAAAAbD/Au7u7u7tjP4BERERERFg/gKqqqqqqaD+gqqqqqqpqP2BVVVVVVWc/YGZmZmZmYj8AmpmZmZlZP9DMzMzMzEw/oIiIiIiIUD8Aq6qqqqo6PxAREREREXI/jIiIiIiIcD80MzMzMzNpP0BERERERGA/gIiIiIiIXD8A3t3d3d09PwCYmZmZmQk/AGdmZmZmNj8A3t3d3d09PwDe3d3d3T0/AAAAAAAAAAAAiYiIiIhIP0BERERERGK/YGZmZmZmZL9oZmZmZmZgv4CZmZmZmVG/wLu7u7u7Yb+YmZmZmZlRvwAAAAAAAEC/AAAAAAAAUL+Ad3d3d3drv2BVVVVVVWW/gJmZmZmZXb/Au7u7u7tXv4B3d3d3d1e/wMzMzMzMRL+AZmZmZmZOv4CIiIiIiFC/gLu7u7u7bz9gVVVVVVVpP6CZmZmZmWk/ICIiIiIiZD8A7+7u7u5WP4CZmZmZmUk/ODMzMzMzQz8AmpmZmZk5PwAzMzMzM0O/QFVVVVVVJT8AmpmZmZkJPwB4d3d3dye/AGZmZmZmRj80MzMzMzNLv2BERERERES/ALy7u7u7Kz8wMzMzMzNLP8Dd3d3d3VU/ADMzMzMzSz8AZ2ZmZmZGPwB4d3d3dze/AAAAAAAAUL8AIiIiIiJKvwARERERETE/AFRVVVVVTb8Ad3d3d3dHvwDMzMzMzDy/ADMzMzMzS78AiYiIiIhYv4BVVVVVVVm/AERERERETL8AMzMzMzMzv4CIiIiIiFS/gFVVVVVVVb/A7u7u7u5Wv6C7u7u7u1u/ACIiIiIiXr8Aq6qqqqpev4B3d3d3d1O/gIiIiIiIUL8A7+7u7u5mv4CqqqqqqmC/wJmZmZmZZb9wd3d3d3dfvwDMzMzMzEw/YGZmZmZmUj+A7u7u7u5OP0BERERERFA/gEREREREYj8A7+7u7u5WPwAREREREUk/gN3d3d3dLT8AAAAAAABQPwBVVVVVVRU/ABERERERST8Aq6qqqqpKP8DMzMzMzFg/AO/u7u7uTj/g3d3d3d1RPwDv7u7u7lY/gKqqqqqqUj9AREREREREP3h3d3d3d1c/AGZmZmZmNj+Ad3d3d3djP4CZmZmZmV0/QDMzMzMzYT+Ad3d3d3dfPwAREREREWc/iIiIiIiIaD+Ad3d3d3dbPwDe3d3d3T0/AJqZmZmZOb8A3t3d3d0tv6CZmZmZmRk/ACMiIiIiSj8AuLu7u7srvwDe3d3d3UW/AM3MzMzMTL8AAAAAAABIvwDNzMzMzEy/oJmZmZmZSb84MzMzMzMzvwDe3d3d3T0/ABARERERIT8AIiIiIiJCv8DMzMzMzDy/ADQzMzMzMz8AMzMzMzNhP+Dd3d3d3V0/0N3d3d3dWT+A3d3d3d1NP0BEREREREw/ABERERERUT8AvLu7u7srP4AzMzMzM1c/ICIiIiIiWj+AiIiIiIhcP0BmZmZmZlI/ACMiIiIiSj9ARERERERiv4Cqqqqqqlq/QERERERETL8AMzMzMzNDvwAREREREUm/QGZmZmZmRr+IiIiIiIhUvwDv7u7u7lK/8O7u7u7uVj+AiIiIiIhYPwAREREREVk/gEREREREVD8AAAAAAABqPwAAAAAAAFg/VFVVVVVVTT8AIyIiIiJKP7i7u7u7u1M/AAAAAAAAVD/AzMzMzMxUPwDv7u7u7lI/EBERERERaz9AZmZmZmZaP2BmZmZmZlI/AO/u7u7uWj8A7+7u7u5oPxAREREREWU/UFVVVVVVYz9AIiIiIiJaP0BERERERFw/AEREREREND8A7+7u7u5OPwDd3d3d3U0/IiIiIiIiSj8AAAAAAABAPwBUVVVVVSU/AN7d3d3dPb8QERERERFjvwDe3d3d3Vm/gHd3d3d3R7+AmZmZmZlRvwDg3d3d3R2/AJyZmZmZGT8AMzMzMzMzv8Cqqqqqqkq/QEREREREYD9wd3d3d3dXP6CZmZmZmVU/gKqqqqqqUj8AAAAAAAB8PzQzMzMzM3A//P//////aT9gRERERERkPwAzMzMzM20/MDMzMzMzZz9wZmZmZmZkP2BVVVVVVWU/AN7d3d3dHb/AzMzMzMxUv4B3d3d3dze/ACIiIiIiQr+AqqqqqqpKP0BVVVVVVU0/vLu7u7u7Sz8Aq6qqqqpCP4DMzMzMzFy/YFVVVVVVWb8A7+7u7u5SvwAREREREUm/AAAAAAAAc7/Au7u7u7twv8Cqqqqqqmi/UFVVVVVVZ78AIiIiIiJavwAiIiIiIkK/AODd3d3dHb8AvLu7u7srvwDe3d3d3UU/AJqZmZmZQT8AIBERERHxvsCqqqqqqkK/wJmZmZmZZz8wMzMzMzNlP3h3d3d3d2E/4O7u7u7uYD9AIiIiIiJiPxQREREREVU/gLu7u7u7Kz+AzMzMzMxEP4C7u7u7u18/wLu7u7u7Wz8AAAAAAABUP4CZmZmZmVk/YFVVVVVVZz/Au7u7u7thP+DMzMzMzFg/gHd3d3d3Vz+AzMzMzMxkP8DMzMzMzGA/YGZmZmZmYD9AZmZmZmZOPwBmZmZmZlK/AJmZmZmZSb8AqqqqqqpCvwBUVVVVVRW/gJmZmZmZVT8AzczMzMxMPwCqqqqqqjo/AN7d3d3dHT8A7+7u7u5oP8C7u7u7u2c/AO/u7u7uXj8AAAAAAABcP4AzMzMzM2G/gHd3d3d3V78AMzMzMzNXvyAiIiIiIla/IBEREREReD/Au7u7u7t4P9DMzMzMzHM/YEREREREbD8AoJmZmZkJvwAAAAAAAFS/AO/u7u7uVr8AAAAAAABIv4BmZmZmZmK/EBERERERZb/gzMzMzMxYv8CZmZmZmVG/AAAAAAAAYr9wZmZmZmZkv5iZmZmZmV2/gN3d3d3dUb/AzMzMzMxQv4CZmZmZmVG/mJmZmZmZQb/AzMzMzMxcvwDv7u7u7la/wMzMzMzMVL8AVVVVVVVFvwCamZmZmSm/AJqZmZmZOb8AmJmZmZkJv8C7u7u7u0u/ACIiIiIiVr/AqqqqqqpsvwAAAAAAAGi/iYiIiIiIYr+AmZmZmZlZvwCrqqqqqmY/gFVVVVVVYT8AZ2ZmZmZOPwAAAAAAAEA/AAAAAAAAUD8QERERERFdPyQiIiIiIlI/ALy7u7u7Sz8Ad3d3d3c3v0AzMzMzM0u/AAAAAAAASL8AIiIiIiJCvwAAAAAAAEg/YFVVVVVVNT/AiIiIiIg4PwDv7u7u7kY/wIiIiIiIQL+AZmZmZmZGv4BmZmZmZja/ALy7u7u7Oz8AVVVVVVUVPwBmZmZmZkY/AERERERETD8Ad3d3d3dHv4B3d3d3d22/AAAAAAAAaL/Y3d3d3d1hvwDe3d3d3VG/wLu7u7u7U78AvLu7u7s7vzAzMzMzM0u/gHd3d3d3V78AAAAAAICNv1BVVVVVVYm/vLu7u7u7gr/g7u7u7u58v1hVVVVV1Y6/RkRERETEir8jIiIiIiKFv7CqqqqqKoC/NDMzMzMzd79AMzMzMzNyv+Dd3d3d3WW/AJqZmZmZXb+AiIiIiIhgv4CIiIiIiGC/AAAAAAAAXL8AvLu7u7tLvwB3d3d3d0+/AO/u7u7uPr9AREREREQ0vwBWVVVVVSW/8O7u7u7uYL/gzMzMzMxgv2BmZmZmZlK/AImIiIiISL9ERERERERcv4B3d3d3d1O/wO7u7u7uTr8ARERERERMv2BmZmZmZlq/ADMzMzMzU78AERERERERvwAgEREREfE+AAAAAAAAML8AMzMzMzMzPwCYmZmZmSm/gHd3d3d3N78AZmZmZmY2P8C7u7u7u0M/AAAAAAAAVD+AVVVVVVVFP4CIiIiIiIy/ZmZmZmZmir8iIiIiIqKEvwAAAAAAAH6/ICIiIiIidL9wZmZmZmZqv1BVVVVVVWG/gLu7u7u7S7+Ad3d3d3dpv4B3d3d3d2e/UFVVVVVVXb+Ad3d3d3dXvwBUVVVVVSW/wN3d3d3dRT+4u7u7u7srPwBERERERDQ/gMzMzMzMUD9gZmZmZmZWP0AzMzMzM1M/gIiIiIiIXD8A7+7u7u5oP8DMzMzMzGw/ABERERERZT9kZmZmZmZgP6Cqqqqqqns/sKqqqqqqeD8QERERERF1P/Du7u7u7nE/AN7d3d3dez9AMzMzMzN2P+Du7u7u7nE/iIiIiIiIZD/gzMzMzMxyP3h3d3d3d3U/4N3d3d3dcD9gVVVVVVVrPwAREREREXE/VlVVVVVVZT/g3d3d3d1ZP8CqqqqqqlY/AAAAAAAAZj+IiIiIiIhoP2BmZmZmZmQ/IBERERERYz9AZmZmZmZSPwB2d3d3dyc/ICIiIiIiSj8AiYiIiIhIPwBQVVVVVSU/AGdmZmZmRj+Ad3d3d3dTP0BVVVVVVUU/ABERERERXT8AIiIiIiJaPwDv7u7u7lY/GBERERERVT9AZmZmZmZsP87MzMzMzGY/eHd3d3d3Yz/A7u7u7u5WP0AzMzMzM2E/QkREREREYD9ERERERERgPwAzMzMzM1c/4N3d3d3dYz/g7u7u7u5iP8DMzMzMzFg/gEREREREVD9gZmZmZmZOP8Dd3d3d3VU/gERERERETD8AvLu7u7tLPwAAAAAAAEA/AGdmZmZmNr8AMzMzMzNDvwAjIiIiIkK/AAAAAAAAML+Au7u7u7srP4CZmZmZmRm/AM3MzMzMPD8AZmZmZmZOvwAAAAAAAFC/AGZmZmZmNr8AAAAAAAAwv8Dd3d3d3WM/YFVVVVVVYT+gmZmZmZljPw==","dtype":"float64","shape":[5000]}},"selected":{"id":"1157","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"formatter":{"id":"1152","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"0628e1ca-c6a5-4660-a5c4-1ab4106348ee","roots":{"1103":"bf399b80-f9fd-457e-8bff-7829ed7c1bd3"}}];
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

