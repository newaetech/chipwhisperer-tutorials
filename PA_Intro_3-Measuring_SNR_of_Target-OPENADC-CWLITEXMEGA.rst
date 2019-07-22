
Measuring SNR of Target
-----------------------


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'
    CRYPTO_TARGET = 'TINYAES128C'


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
    rm -f -- simpleserial-aes.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes.i aes-independant.i
    .
    -------- begin --------
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Linking: simpleserial-aes-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEXMEGA.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes.o objdir/aes-independant.o --output simpleserial-aes-CWLITEXMEGA.elf -Wl,-Map=simpleserial-aes-CWLITEXMEGA.map,--cref   -lm  
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
       4064	    556	    248	   4868	   1304	simpleserial-aes-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------



Signal to Noise Ratio (SNR)
---------------------------

In general, the "Signal to Noise Ratio" is defined as:

.. math:: SNR = \frac{Var(Signal)}{Var(Noise)}

This is to say the variance in the signal measured compared to the
variance in the noise measured. You will often see SNR expressed in dB,
which is a logarithmic scale. In which case the conversion is simply
done as:

.. math:: SNR_{dB} = 20log(SNR)

Note this assumes our measurements were *voltages* -- the 20 infront of
the log is done to represent the fact that SNR is typically referencing
the power difference between signal and noise. The power across a
resistor would be equal to the square of the voltage, so we should
actually have:

.. math:: SNR_{dB} = 10log\left(  \left( \frac{Var(Signal)}{Var(Noise)}\right)^2\right) = 20log\left( \frac{Var(Signal)}{Var(Noise)}\right)

What's the Signal?
~~~~~~~~~~~~~~~~~~

The above was very easy to right out. But what is the signal, and what
is the noise? The signal is going to be the leakage we measured *based
on some leakage function*, and the noise will be the *noise inherent in
the measurement not caused by the leakage*.

The easiest way to do this will be to find the average trace for each
leakage "group". If using the Hamming weight leakage model, this means
we have 9 traces (one for each HW). If we used classic DPA we would have
two groups (one for each bit).

Within each group, we can measure the noise. We don't actually measure
across *all* groups since then we would have the leakage contributing to
our "noise". We want to get a measure of only the noise, not variance
being caused by the signal.

Outline of this Tutorial
~~~~~~~~~~~~~~~~~~~~~~~~

As usual, we're going to first go through a detailed example. We'll then
give you some quick cheater functions to calculate the SNR based on
leakage models built into ChipWhisperer (yay!). This makes it easy to
quickly compare leakage models.

Capturing Power Traces
----------------------

The capture part is the same as previous tutorials. We include it here
to make it interactive.

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

    fw_path = "../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex".format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 4619 bytes
    


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
    N = 1000  # Number of traces
    if PLATFORM == "CWNANO":
        N = 1500
    
    for i in tnrange(N, desc='Capturing traces'):
        key, text = ktp.next()
        
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        traces.append(trace)


**Out [6]:**






Now that we have our traces, we can also plot them using Bokeh:


**In [7]:**

.. code:: ipython3

    %run "Helper_Scripts/bokeh.ipynb"


**In [8]:**

.. code:: ipython3

    bokeh_plot(traces[0].wave)


**Out [8]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1001">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="4efd16ea-1726-4990-b5b9-324ee4387701"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4efd16ea-1726-4990-b5b9-324ee4387701');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="d6db79e1-0a5b-4b12-bb36-69fd1fd686b7" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="190042f7-1775-4b8f-8c2e-64ebcf7a11b6"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#190042f7-1775-4b8f-8c2e-64ebcf7a11b6');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"b180f5ef-5e79-4435-b084-8bd3bf24ca88":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1047","type":"UnionRenderers"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1047","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"b180f5ef-5e79-4435-b084-8bd3bf24ca88","roots":{"1002":"d6db79e1-0a5b-4b12-bb36-69fd1fd686b7"}}];
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


**In [9]:**

.. code:: ipython3

    # cleanup the connection to the target and scope
    scope.dis()
    target.dis()

SNR Calculation
---------------

The first thing we'll do is spread the traces into the "groups".
Remembering the examples of plotting Hamming Weight, we went over how
the leakage model is used. All we need to do here is perform the same
sort of operation, except we'll take the mean of each group as well. The
following will end up making an array, ``hwmean[]`` that contains a mean
for HW=0, HW=1, etc.:


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
    
    HW = [bin(n).count("1") for n in range(0, 256)]
    
    def intermediate(pt, key):
        return sbox[pt ^ key]
    
    #SNR of byte 0
    bnum = 0
    
    #Length of one trace
    npoints = len(traces[0].wave)
    
    hwarray = [[], [], [], [], [], [], [], [], []]
    
    #For each byte we are looking at - let's split into multiple groups
    for tnum in range(0, len(traces)):
        hw_of_byte = HW[intermediate(traces[tnum].textin[bnum], traces[tnum].key[bnum])]
        hwarray[hw_of_byte].append(traces[tnum].wave)
        
    hwmean = np.zeros((9, npoints))
        
    for i in range(0, 9):
        hwmean[i] = np.mean(hwarray[i], axis=0)

We could plot those figures, and see the same sort of information from
the HW plot lab too. We've done this a slightly different way, so you
might find it even easier to see the difference here.


**In [11]:**

.. code:: ipython3

    %matplotlib notebook
    import matplotlib.pyplot as plt
    
    plt.plot(hwmean.T)
    plt.title("Average Hamming Weight Measurement")
    plt.xlabel("Sample No.")
    plt.ylabel("Average Value")
    plt.legend(['HW=%d'%d for d in range(0, 9)])


**Out [11]:**


.. raw:: html

    

    <div id="4b3530fc-5ee5-42b2-902b-0751c9e3174d"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4b3530fc-5ee5-42b2-902b-0751c9e3174d');
        /* Put everything inside the global mpl namespace */
    window.mpl = {};
    
    
    mpl.get_websocket_type = function() {
        if (typeof(WebSocket) !== 'undefined') {
            return WebSocket;
        } else if (typeof(MozWebSocket) !== 'undefined') {
            return MozWebSocket;
        } else {
            alert('Your browser does not have WebSocket support. ' +
                  'Please try Chrome, Safari or Firefox ≥ 6. ' +
                  'Firefox 4 and 5 are also supported but you ' +
                  'have to enable WebSockets in about:config.');
        };
    }
    
    mpl.figure = function(figure_id, websocket, ondownload, parent_element) {
        this.id = figure_id;
    
        this.ws = websocket;
    
        this.supports_binary = (this.ws.binaryType != undefined);
    
        if (!this.supports_binary) {
            var warnings = document.getElementById("mpl-warnings");
            if (warnings) {
                warnings.style.display = 'block';
                warnings.textContent = (
                    "This browser does not support binary websocket messages. " +
                        "Performance may be slow.");
            }
        }
    
        this.imageObj = new Image();
    
        this.context = undefined;
        this.message = undefined;
        this.canvas = undefined;
        this.rubberband_canvas = undefined;
        this.rubberband_context = undefined;
        this.format_dropdown = undefined;
    
        this.image_mode = 'full';
    
        this.root = $('<div/>');
        this._root_extra_style(this.root)
        this.root.attr('style', 'display: inline-block');
    
        $(parent_element).append(this.root);
    
        this._init_header(this);
        this._init_canvas(this);
        this._init_toolbar(this);
    
        var fig = this;
    
        this.waiting = false;
    
        this.ws.onopen =  function () {
                fig.send_message("supports_binary", {value: fig.supports_binary});
                fig.send_message("send_image_mode", {});
                if (mpl.ratio != 1) {
                    fig.send_message("set_dpi_ratio", {'dpi_ratio': mpl.ratio});
                }
                fig.send_message("refresh", {});
            }
    
        this.imageObj.onload = function() {
                if (fig.image_mode == 'full') {
                    // Full images could contain transparency (where diff images
                    // almost always do), so we need to clear the canvas so that
                    // there is no ghosting.
                    fig.context.clearRect(0, 0, fig.canvas.width, fig.canvas.height);
                }
                fig.context.drawImage(fig.imageObj, 0, 0);
            };
    
        this.imageObj.onunload = function() {
            fig.ws.close();
        }
    
        this.ws.onmessage = this._make_on_message_function(this);
    
        this.ondownload = ondownload;
    }
    
    mpl.figure.prototype._init_header = function() {
        var titlebar = $(
            '<div class="ui-dialog-titlebar ui-widget-header ui-corner-all ' +
            'ui-helper-clearfix"/>');
        var titletext = $(
            '<div class="ui-dialog-title" style="width: 100%; ' +
            'text-align: center; padding: 3px;"/>');
        titlebar.append(titletext)
        this.root.append(titlebar);
        this.header = titletext[0];
    }
    
    
    
    mpl.figure.prototype._canvas_extra_style = function(canvas_div) {
    
    }
    
    
    mpl.figure.prototype._root_extra_style = function(canvas_div) {
    
    }
    
    mpl.figure.prototype._init_canvas = function() {
        var fig = this;
    
        var canvas_div = $('<div/>');
    
        canvas_div.attr('style', 'position: relative; clear: both; outline: 0');
    
        function canvas_keyboard_event(event) {
            return fig.key_event(event, event['data']);
        }
    
        canvas_div.keydown('key_press', canvas_keyboard_event);
        canvas_div.keyup('key_release', canvas_keyboard_event);
        this.canvas_div = canvas_div
        this._canvas_extra_style(canvas_div)
        this.root.append(canvas_div);
    
        var canvas = $('<canvas/>');
        canvas.addClass('mpl-canvas');
        canvas.attr('style', "left: 0; top: 0; z-index: 0; outline: 0")
    
        this.canvas = canvas[0];
        this.context = canvas[0].getContext("2d");
    
        var backingStore = this.context.backingStorePixelRatio ||
    	this.context.webkitBackingStorePixelRatio ||
    	this.context.mozBackingStorePixelRatio ||
    	this.context.msBackingStorePixelRatio ||
    	this.context.oBackingStorePixelRatio ||
    	this.context.backingStorePixelRatio || 1;
    
        mpl.ratio = (window.devicePixelRatio || 1) / backingStore;
    
        var rubberband = $('<canvas/>');
        rubberband.attr('style', "position: absolute; left: 0; top: 0; z-index: 1;")
    
        var pass_mouse_events = true;
    
        canvas_div.resizable({
            start: function(event, ui) {
                pass_mouse_events = false;
            },
            resize: function(event, ui) {
                fig.request_resize(ui.size.width, ui.size.height);
            },
            stop: function(event, ui) {
                pass_mouse_events = true;
                fig.request_resize(ui.size.width, ui.size.height);
            },
        });
    
        function mouse_event_fn(event) {
            if (pass_mouse_events)
                return fig.mouse_event(event, event['data']);
        }
    
        rubberband.mousedown('button_press', mouse_event_fn);
        rubberband.mouseup('button_release', mouse_event_fn);
        // Throttle sequential mouse events to 1 every 20ms.
        rubberband.mousemove('motion_notify', mouse_event_fn);
    
        rubberband.mouseenter('figure_enter', mouse_event_fn);
        rubberband.mouseleave('figure_leave', mouse_event_fn);
    
        canvas_div.on("wheel", function (event) {
            event = event.originalEvent;
            event['data'] = 'scroll'
            if (event.deltaY < 0) {
                event.step = 1;
            } else {
                event.step = -1;
            }
            mouse_event_fn(event);
        });
    
        canvas_div.append(canvas);
        canvas_div.append(rubberband);
    
        this.rubberband = rubberband;
        this.rubberband_canvas = rubberband[0];
        this.rubberband_context = rubberband[0].getContext("2d");
        this.rubberband_context.strokeStyle = "#000000";
    
        this._resize_canvas = function(width, height) {
            // Keep the size of the canvas, canvas container, and rubber band
            // canvas in synch.
            canvas_div.css('width', width)
            canvas_div.css('height', height)
    
            canvas.attr('width', width * mpl.ratio);
            canvas.attr('height', height * mpl.ratio);
            canvas.attr('style', 'width: ' + width + 'px; height: ' + height + 'px;');
    
            rubberband.attr('width', width);
            rubberband.attr('height', height);
        }
    
        // Set the figure to an initial 600x600px, this will subsequently be updated
        // upon first draw.
        this._resize_canvas(600, 600);
    
        // Disable right mouse context menu.
        $(this.rubberband_canvas).bind("contextmenu",function(e){
            return false;
        });
    
        function set_focus () {
            canvas.focus();
            canvas_div.focus();
        }
    
        window.setTimeout(set_focus, 100);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items) {
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) {
                // put a spacer in here.
                continue;
            }
            var button = $('<button/>');
            button.addClass('ui-button ui-widget ui-state-default ui-corner-all ' +
                            'ui-button-icon-only');
            button.attr('role', 'button');
            button.attr('aria-disabled', 'false');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
    
            var icon_img = $('<span/>');
            icon_img.addClass('ui-button-icon-primary ui-icon');
            icon_img.addClass(image);
            icon_img.addClass('ui-corner-all');
    
            var tooltip_span = $('<span/>');
            tooltip_span.addClass('ui-button-text');
            tooltip_span.html(tooltip);
    
            button.append(icon_img);
            button.append(tooltip_span);
    
            nav_element.append(button);
        }
    
        var fmt_picker_span = $('<span/>');
    
        var fmt_picker = $('<select/>');
        fmt_picker.addClass('mpl-toolbar-option ui-widget ui-widget-content');
        fmt_picker_span.append(fmt_picker);
        nav_element.append(fmt_picker_span);
        this.format_dropdown = fmt_picker[0];
    
        for (var ind in mpl.extensions) {
            var fmt = mpl.extensions[ind];
            var option = $(
                '<option/>', {selected: fmt === mpl.default_extension}).html(fmt);
            fmt_picker.append(option);
        }
    
        // Add hover states to the ui-buttons
        $( ".ui-button" ).hover(
            function() { $(this).addClass("ui-state-hover");},
            function() { $(this).removeClass("ui-state-hover");}
        );
    
        var status_bar = $('<span class="mpl-message"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    }
    
    mpl.figure.prototype.request_resize = function(x_pixels, y_pixels) {
        // Request matplotlib to resize the figure. Matplotlib will then trigger a resize in the client,
        // which will in turn request a refresh of the image.
        this.send_message('resize', {'width': x_pixels, 'height': y_pixels});
    }
    
    mpl.figure.prototype.send_message = function(type, properties) {
        properties['type'] = type;
        properties['figure_id'] = this.id;
        this.ws.send(JSON.stringify(properties));
    }
    
    mpl.figure.prototype.send_draw_message = function() {
        if (!this.waiting) {
            this.waiting = true;
            this.ws.send(JSON.stringify({type: "draw", figure_id: this.id}));
        }
    }
    
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        var format_dropdown = fig.format_dropdown;
        var format = format_dropdown.options[format_dropdown.selectedIndex].value;
        fig.ondownload(fig, format);
    }
    
    
    mpl.figure.prototype.handle_resize = function(fig, msg) {
        var size = msg['size'];
        if (size[0] != fig.canvas.width || size[1] != fig.canvas.height) {
            fig._resize_canvas(size[0], size[1]);
            fig.send_message("refresh", {});
        };
    }
    
    mpl.figure.prototype.handle_rubberband = function(fig, msg) {
        var x0 = msg['x0'] / mpl.ratio;
        var y0 = (fig.canvas.height - msg['y0']) / mpl.ratio;
        var x1 = msg['x1'] / mpl.ratio;
        var y1 = (fig.canvas.height - msg['y1']) / mpl.ratio;
        x0 = Math.floor(x0) + 0.5;
        y0 = Math.floor(y0) + 0.5;
        x1 = Math.floor(x1) + 0.5;
        y1 = Math.floor(y1) + 0.5;
        var min_x = Math.min(x0, x1);
        var min_y = Math.min(y0, y1);
        var width = Math.abs(x1 - x0);
        var height = Math.abs(y1 - y0);
    
        fig.rubberband_context.clearRect(
            0, 0, fig.canvas.width / mpl.ratio, fig.canvas.height / mpl.ratio);
    
        fig.rubberband_context.strokeRect(min_x, min_y, width, height);
    }
    
    mpl.figure.prototype.handle_figure_label = function(fig, msg) {
        // Updates the figure title.
        fig.header.textContent = msg['label'];
    }
    
    mpl.figure.prototype.handle_cursor = function(fig, msg) {
        var cursor = msg['cursor'];
        switch(cursor)
        {
        case 0:
            cursor = 'pointer';
            break;
        case 1:
            cursor = 'default';
            break;
        case 2:
            cursor = 'crosshair';
            break;
        case 3:
            cursor = 'move';
            break;
        }
        fig.rubberband_canvas.style.cursor = cursor;
    }
    
    mpl.figure.prototype.handle_message = function(fig, msg) {
        fig.message.textContent = msg['message'];
    }
    
    mpl.figure.prototype.handle_draw = function(fig, msg) {
        // Request the server to send over a new figure.
        fig.send_draw_message();
    }
    
    mpl.figure.prototype.handle_image_mode = function(fig, msg) {
        fig.image_mode = msg['mode'];
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Called whenever the canvas gets updated.
        this.send_message("ack", {});
    }
    
    // A function to construct a web socket function for onmessage handling.
    // Called in the figure constructor.
    mpl.figure.prototype._make_on_message_function = function(fig) {
        return function socket_on_message(evt) {
            if (evt.data instanceof Blob) {
                /* FIXME: We get "Resource interpreted as Image but
                 * transferred with MIME type text/plain:" errors on
                 * Chrome.  But how to set the MIME type?  It doesn't seem
                 * to be part of the websocket stream */
                evt.data.type = "image/png";
    
                /* Free the memory for the previous frames */
                if (fig.imageObj.src) {
                    (window.URL || window.webkitURL).revokeObjectURL(
                        fig.imageObj.src);
                }
    
                fig.imageObj.src = (window.URL || window.webkitURL).createObjectURL(
                    evt.data);
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
            else if (typeof evt.data === 'string' && evt.data.slice(0, 21) == "data:image/png;base64") {
                fig.imageObj.src = evt.data;
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
    
            var msg = JSON.parse(evt.data);
            var msg_type = msg['type'];
    
            // Call the  "handle_{type}" callback, which takes
            // the figure and JSON message as its only arguments.
            try {
                var callback = fig["handle_" + msg_type];
            } catch (e) {
                console.log("No handler for the '" + msg_type + "' message type: ", msg);
                return;
            }
    
            if (callback) {
                try {
                    // console.log("Handling '" + msg_type + "' message: ", msg);
                    callback(fig, msg);
                } catch (e) {
                    console.log("Exception inside the 'handler_" + msg_type + "' callback:", e, e.stack, msg);
                }
            }
        };
    }
    
    // from http://stackoverflow.com/questions/1114465/getting-mouse-location-in-canvas
    mpl.findpos = function(e) {
        //this section is from http://www.quirksmode.org/js/events_properties.html
        var targ;
        if (!e)
            e = window.event;
        if (e.target)
            targ = e.target;
        else if (e.srcElement)
            targ = e.srcElement;
        if (targ.nodeType == 3) // defeat Safari bug
            targ = targ.parentNode;
    
        // jQuery normalizes the pageX and pageY
        // pageX,Y are the mouse positions relative to the document
        // offset() returns the position of the element relative to the document
        var x = e.pageX - $(targ).offset().left;
        var y = e.pageY - $(targ).offset().top;
    
        return {"x": x, "y": y};
    };
    
    /*
     * return a copy of an object with only non-object keys
     * we need this to avoid circular references
     * http://stackoverflow.com/a/24161582/3208463
     */
    function simpleKeys (original) {
      return Object.keys(original).reduce(function (obj, key) {
        if (typeof original[key] !== 'object')
            obj[key] = original[key]
        return obj;
      }, {});
    }
    
    mpl.figure.prototype.mouse_event = function(event, name) {
        var canvas_pos = mpl.findpos(event)
    
        if (name === 'button_press')
        {
            this.canvas.focus();
            this.canvas_div.focus();
        }
    
        var x = canvas_pos.x * mpl.ratio;
        var y = canvas_pos.y * mpl.ratio;
    
        this.send_message(name, {x: x, y: y, button: event.button,
                                 step: event.step,
                                 guiEvent: simpleKeys(event)});
    
        /* This prevents the web browser from automatically changing to
         * the text insertion cursor when the button is pressed.  We want
         * to control all of the cursor setting manually through the
         * 'cursor' event from matplotlib */
        event.preventDefault();
        return false;
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        // Handle any extra behaviour associated with a key event
    }
    
    mpl.figure.prototype.key_event = function(event, name) {
    
        // Prevent repeat events
        if (name == 'key_press')
        {
            if (event.which === this._key)
                return;
            else
                this._key = event.which;
        }
        if (name == 'key_release')
            this._key = null;
    
        var value = '';
        if (event.ctrlKey && event.which != 17)
            value += "ctrl+";
        if (event.altKey && event.which != 18)
            value += "alt+";
        if (event.shiftKey && event.which != 16)
            value += "shift+";
    
        value += 'k';
        value += event.which.toString();
    
        this._key_event_extra(event, name);
    
        this.send_message(name, {key: value,
                                 guiEvent: simpleKeys(event)});
        return false;
    }
    
    mpl.figure.prototype.toolbar_button_onclick = function(name) {
        if (name == 'download') {
            this.handle_save(this, null);
        } else {
            this.send_message("toolbar_button", {name: name});
        }
    };
    
    mpl.figure.prototype.toolbar_button_onmouseover = function(tooltip) {
        this.message.textContent = tooltip;
    };
    mpl.toolbar_items = [["Home", "Reset original view", "fa fa-home icon-home", "home"], ["Back", "Back to previous view", "fa fa-arrow-left icon-arrow-left", "back"], ["Forward", "Forward to next view", "fa fa-arrow-right icon-arrow-right", "forward"], ["", "", "", ""], ["Pan", "Pan axes with left mouse, zoom with right", "fa fa-arrows icon-move", "pan"], ["Zoom", "Zoom to rectangle", "fa fa-square-o icon-check-empty", "zoom"], ["", "", "", ""], ["Download", "Download plot", "fa fa-floppy-o icon-save", "download"]];
    
    mpl.extensions = ["eps", "jpeg", "pdf", "png", "ps", "raw", "svg", "tif"];
    
    mpl.default_extension = "png";var comm_websocket_adapter = function(comm) {
        // Create a "websocket"-like object which calls the given IPython comm
        // object with the appropriate methods. Currently this is a non binary
        // socket, so there is still some room for performance tuning.
        var ws = {};
    
        ws.close = function() {
            comm.close()
        };
        ws.send = function(m) {
            //console.log('sending', m);
            comm.send(m);
        };
        // Register the callback with on_msg.
        comm.on_msg(function(msg) {
            //console.log('receiving', msg['content']['data'], msg);
            // Pass the mpl event to the overridden (by mpl) onmessage function.
            ws.onmessage(msg['content']['data'])
        });
        return ws;
    }
    
    mpl.mpl_figure_comm = function(comm, msg) {
        // This is the function which gets called when the mpl process
        // starts-up an IPython Comm through the "matplotlib" channel.
    
        var id = msg.content.data.id;
        // Get hold of the div created by the display call when the Comm
        // socket was opened in Python.
        var element = $("#" + id);
        var ws_proxy = comm_websocket_adapter(comm)
    
        function ondownload(figure, format) {
            window.open(figure.imageObj.src);
        }
    
        var fig = new mpl.figure(id, ws_proxy,
                               ondownload,
                               element.get(0));
    
        // Call onopen now - mpl needs it, as it is assuming we've passed it a real
        // web socket which is closed, not our websocket->open comm proxy.
        ws_proxy.onopen();
    
        fig.parent_element = element.get(0);
        fig.cell_info = mpl.find_output_cell("<div id='" + id + "'></div>");
        if (!fig.cell_info) {
            console.error("Failed to find cell for figure", id, fig);
            return;
        }
    
        var output_index = fig.cell_info[2]
        var cell = fig.cell_info[0];
    
    };
    
    mpl.figure.prototype.handle_close = function(fig, msg) {
        var width = fig.canvas.width/mpl.ratio
        fig.root.unbind('remove')
    
        // Update the output cell to use the data from the current canvas.
        fig.push_to_output();
        var dataURL = fig.canvas.toDataURL();
        // Re-enable the keyboard manager in IPython - without this line, in FF,
        // the notebook keyboard shortcuts fail.
        IPython.keyboard_manager.enable()
        $(fig.parent_element).html('<img src="' + dataURL + '" width="' + width + '">');
        fig.close_ws(fig, msg);
    }
    
    mpl.figure.prototype.close_ws = function(fig, msg){
        fig.send_message('closing', msg);
        // fig.ws.close()
    }
    
    mpl.figure.prototype.push_to_output = function(remove_interactive) {
        // Turn the data on the canvas into data in the output cell.
        var width = this.canvas.width/mpl.ratio
        var dataURL = this.canvas.toDataURL();
        this.cell_info[1]['text/html'] = '<img src="' + dataURL + '" width="' + width + '">';
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Tell IPython that the notebook contents must change.
        IPython.notebook.set_dirty(true);
        this.send_message("ack", {});
        var fig = this;
        // Wait a second, then push the new image to the DOM so
        // that it is saved nicely (might be nice to debounce this).
        setTimeout(function () { fig.push_to_output() }, 1000);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items){
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) { continue; };
    
            var button = $('<button class="btn btn-default" href="#" title="' + name + '"><i class="fa ' + image + ' fa-lg"></i></button>');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
            nav_element.append(button);
        }
    
        // Add the status bar.
        var status_bar = $('<span class="mpl-message" style="text-align:right; float: right;"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    
        // Add the close button to the window.
        var buttongrp = $('<div class="btn-group inline pull-right"></div>');
        var button = $('<button class="btn btn-mini btn-primary" href="#" title="Stop Interaction"><i class="fa fa-power-off icon-remove icon-large"></i></button>');
        button.click(function (evt) { fig.handle_close(fig, {}); } );
        button.mouseover('Stop Interaction', toolbar_mouse_event);
        buttongrp.append(button);
        var titlebar = this.root.find($('.ui-dialog-titlebar'));
        titlebar.prepend(buttongrp);
    }
    
    mpl.figure.prototype._root_extra_style = function(el){
        var fig = this
        el.on("remove", function(){
    	fig.close_ws(fig, {});
        });
    }
    
    mpl.figure.prototype._canvas_extra_style = function(el){
        // this is important to make the div 'focusable
        el.attr('tabindex', 0)
        // reach out to IPython and tell the keyboard manager to turn it's self
        // off when our div gets focus
    
        // location in version 3
        if (IPython.notebook.keyboard_manager) {
            IPython.notebook.keyboard_manager.register_events(el);
        }
        else {
            // location in version 2
            IPython.keyboard_manager.register_events(el);
        }
    
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        var manager = IPython.notebook.keyboard_manager;
        if (!manager)
            manager = IPython.keyboard_manager;
    
        // Check for shift+enter
        if (event.shiftKey && event.which == 13) {
            this.canvas_div.blur();
            event.shiftKey = false;
            // Send a "J" for go to next cell
            event.which = 74;
            event.keyCode = 74;
            manager.command_mode();
            manager.handle_keydown(event);
        }
    }
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        fig.ondownload(fig, null);
    }
    
    
    mpl.find_output_cell = function(html_output) {
        // Return the cell and output element which can be found *uniquely* in the notebook.
        // Note - this is a bit hacky, but it is done because the "notebook_saving.Notebook"
        // IPython event is triggered only after the cells have been serialised, which for
        // our purposes (turning an active figure into a static one), is too late.
        var cells = IPython.notebook.get_cells();
        var ncells = cells.length;
        for (var i=0; i<ncells; i++) {
            var cell = cells[i];
            if (cell.cell_type === 'code'){
                for (var j=0; j<cell.output_area.outputs.length; j++) {
                    var data = cell.output_area.outputs[j];
                    if (data.data) {
                        // IPython >= 3 moved mimebundle to data attribute of output
                        data = data.data;
                    }
                    if (data['text/html'] == html_output) {
                        return [cell, data, j];
                    }
                }
            }
        }
    }
    
    // Register the function which deals with the matplotlib target/channel.
    // The kernel may be null if the page has been refreshed.
    if (IPython.notebook.kernel != null) {
        IPython.notebook.kernel.comm_manager.register_target('matplotlib', mpl.mpl_figure_comm);
    }
    
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        <div id='cd7a189e-c990-49d5-8cf4-f65faae28429'></div>
    </div>




.. parsed-literal::

    <matplotlib.legend.Legend at 0xfd57f10>



An important thing to note - the number of traces in each group will not
be uniform! This is beacuse many values have a HW of 4 (11110000,
10101010, etc) but only one option has a HW of 0 or 8.


**In [12]:**

.. code:: ipython3

    for t in range(0, 9):
        print("HW %d has %d traces"%(t, len(hwarray[t])))


**Out [12]:**



.. parsed-literal::

    HW 0 has 6 traces
    HW 1 has 27 traces
    HW 2 has 97 traces
    HW 3 has 222 traces
    HW 4 has 277 traces
    HW 5 has 211 traces
    HW 6 has 126 traces
    HW 7 has 31 traces
    HW 8 has 3 traces
    


That last point is important, as we're going to use only one group for
now to calculate the noise. Here I've selected ``hwarray[4]`` for
example. We don't want to calculate across the entire trace set as it
will include the signal variance (hint - you can test this by changing
the variance calculation to be done over the ``traces`` variable).

We also need to REMOVE any groups with zero traces. They will ruin our
variance calculation (since there is no data to calculate variance
over). This can also be done by simply adding traces to the capture side
too.


**In [13]:**

.. code:: ipython3

    inc_list = []
    for i in range(0, len(hwarray)):
        if len(hwarray[i]) > 0:
            inc_list.append(i)
            
    hwmean_valid = hwmean[inc_list]
    
    signal_var = np.var(hwmean_valid, axis=0)
    noise_var_onehw = np.var(hwarray[4], axis=0)
    
    snr = signal_var / noise_var_onehw


**In [14]:**

.. code:: ipython3

    %matplotlib notebook
    import matplotlib.pyplot as plt
    
    plt.plot(20 * np.log(snr))
    plt.title("SNR of Measurement")
    plt.xlabel("Sample No.")
    plt.ylabel("SNR (dB)")
    plt.show()


**Out [14]:**


.. raw:: html

    

    <div id="3e60249a-d904-4a98-841d-1e35c687f155"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3e60249a-d904-4a98-841d-1e35c687f155');
        /* Put everything inside the global mpl namespace */
    window.mpl = {};
    
    
    mpl.get_websocket_type = function() {
        if (typeof(WebSocket) !== 'undefined') {
            return WebSocket;
        } else if (typeof(MozWebSocket) !== 'undefined') {
            return MozWebSocket;
        } else {
            alert('Your browser does not have WebSocket support. ' +
                  'Please try Chrome, Safari or Firefox ≥ 6. ' +
                  'Firefox 4 and 5 are also supported but you ' +
                  'have to enable WebSockets in about:config.');
        };
    }
    
    mpl.figure = function(figure_id, websocket, ondownload, parent_element) {
        this.id = figure_id;
    
        this.ws = websocket;
    
        this.supports_binary = (this.ws.binaryType != undefined);
    
        if (!this.supports_binary) {
            var warnings = document.getElementById("mpl-warnings");
            if (warnings) {
                warnings.style.display = 'block';
                warnings.textContent = (
                    "This browser does not support binary websocket messages. " +
                        "Performance may be slow.");
            }
        }
    
        this.imageObj = new Image();
    
        this.context = undefined;
        this.message = undefined;
        this.canvas = undefined;
        this.rubberband_canvas = undefined;
        this.rubberband_context = undefined;
        this.format_dropdown = undefined;
    
        this.image_mode = 'full';
    
        this.root = $('<div/>');
        this._root_extra_style(this.root)
        this.root.attr('style', 'display: inline-block');
    
        $(parent_element).append(this.root);
    
        this._init_header(this);
        this._init_canvas(this);
        this._init_toolbar(this);
    
        var fig = this;
    
        this.waiting = false;
    
        this.ws.onopen =  function () {
                fig.send_message("supports_binary", {value: fig.supports_binary});
                fig.send_message("send_image_mode", {});
                if (mpl.ratio != 1) {
                    fig.send_message("set_dpi_ratio", {'dpi_ratio': mpl.ratio});
                }
                fig.send_message("refresh", {});
            }
    
        this.imageObj.onload = function() {
                if (fig.image_mode == 'full') {
                    // Full images could contain transparency (where diff images
                    // almost always do), so we need to clear the canvas so that
                    // there is no ghosting.
                    fig.context.clearRect(0, 0, fig.canvas.width, fig.canvas.height);
                }
                fig.context.drawImage(fig.imageObj, 0, 0);
            };
    
        this.imageObj.onunload = function() {
            fig.ws.close();
        }
    
        this.ws.onmessage = this._make_on_message_function(this);
    
        this.ondownload = ondownload;
    }
    
    mpl.figure.prototype._init_header = function() {
        var titlebar = $(
            '<div class="ui-dialog-titlebar ui-widget-header ui-corner-all ' +
            'ui-helper-clearfix"/>');
        var titletext = $(
            '<div class="ui-dialog-title" style="width: 100%; ' +
            'text-align: center; padding: 3px;"/>');
        titlebar.append(titletext)
        this.root.append(titlebar);
        this.header = titletext[0];
    }
    
    
    
    mpl.figure.prototype._canvas_extra_style = function(canvas_div) {
    
    }
    
    
    mpl.figure.prototype._root_extra_style = function(canvas_div) {
    
    }
    
    mpl.figure.prototype._init_canvas = function() {
        var fig = this;
    
        var canvas_div = $('<div/>');
    
        canvas_div.attr('style', 'position: relative; clear: both; outline: 0');
    
        function canvas_keyboard_event(event) {
            return fig.key_event(event, event['data']);
        }
    
        canvas_div.keydown('key_press', canvas_keyboard_event);
        canvas_div.keyup('key_release', canvas_keyboard_event);
        this.canvas_div = canvas_div
        this._canvas_extra_style(canvas_div)
        this.root.append(canvas_div);
    
        var canvas = $('<canvas/>');
        canvas.addClass('mpl-canvas');
        canvas.attr('style', "left: 0; top: 0; z-index: 0; outline: 0")
    
        this.canvas = canvas[0];
        this.context = canvas[0].getContext("2d");
    
        var backingStore = this.context.backingStorePixelRatio ||
    	this.context.webkitBackingStorePixelRatio ||
    	this.context.mozBackingStorePixelRatio ||
    	this.context.msBackingStorePixelRatio ||
    	this.context.oBackingStorePixelRatio ||
    	this.context.backingStorePixelRatio || 1;
    
        mpl.ratio = (window.devicePixelRatio || 1) / backingStore;
    
        var rubberband = $('<canvas/>');
        rubberband.attr('style', "position: absolute; left: 0; top: 0; z-index: 1;")
    
        var pass_mouse_events = true;
    
        canvas_div.resizable({
            start: function(event, ui) {
                pass_mouse_events = false;
            },
            resize: function(event, ui) {
                fig.request_resize(ui.size.width, ui.size.height);
            },
            stop: function(event, ui) {
                pass_mouse_events = true;
                fig.request_resize(ui.size.width, ui.size.height);
            },
        });
    
        function mouse_event_fn(event) {
            if (pass_mouse_events)
                return fig.mouse_event(event, event['data']);
        }
    
        rubberband.mousedown('button_press', mouse_event_fn);
        rubberband.mouseup('button_release', mouse_event_fn);
        // Throttle sequential mouse events to 1 every 20ms.
        rubberband.mousemove('motion_notify', mouse_event_fn);
    
        rubberband.mouseenter('figure_enter', mouse_event_fn);
        rubberband.mouseleave('figure_leave', mouse_event_fn);
    
        canvas_div.on("wheel", function (event) {
            event = event.originalEvent;
            event['data'] = 'scroll'
            if (event.deltaY < 0) {
                event.step = 1;
            } else {
                event.step = -1;
            }
            mouse_event_fn(event);
        });
    
        canvas_div.append(canvas);
        canvas_div.append(rubberband);
    
        this.rubberband = rubberband;
        this.rubberband_canvas = rubberband[0];
        this.rubberband_context = rubberband[0].getContext("2d");
        this.rubberband_context.strokeStyle = "#000000";
    
        this._resize_canvas = function(width, height) {
            // Keep the size of the canvas, canvas container, and rubber band
            // canvas in synch.
            canvas_div.css('width', width)
            canvas_div.css('height', height)
    
            canvas.attr('width', width * mpl.ratio);
            canvas.attr('height', height * mpl.ratio);
            canvas.attr('style', 'width: ' + width + 'px; height: ' + height + 'px;');
    
            rubberband.attr('width', width);
            rubberband.attr('height', height);
        }
    
        // Set the figure to an initial 600x600px, this will subsequently be updated
        // upon first draw.
        this._resize_canvas(600, 600);
    
        // Disable right mouse context menu.
        $(this.rubberband_canvas).bind("contextmenu",function(e){
            return false;
        });
    
        function set_focus () {
            canvas.focus();
            canvas_div.focus();
        }
    
        window.setTimeout(set_focus, 100);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items) {
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) {
                // put a spacer in here.
                continue;
            }
            var button = $('<button/>');
            button.addClass('ui-button ui-widget ui-state-default ui-corner-all ' +
                            'ui-button-icon-only');
            button.attr('role', 'button');
            button.attr('aria-disabled', 'false');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
    
            var icon_img = $('<span/>');
            icon_img.addClass('ui-button-icon-primary ui-icon');
            icon_img.addClass(image);
            icon_img.addClass('ui-corner-all');
    
            var tooltip_span = $('<span/>');
            tooltip_span.addClass('ui-button-text');
            tooltip_span.html(tooltip);
    
            button.append(icon_img);
            button.append(tooltip_span);
    
            nav_element.append(button);
        }
    
        var fmt_picker_span = $('<span/>');
    
        var fmt_picker = $('<select/>');
        fmt_picker.addClass('mpl-toolbar-option ui-widget ui-widget-content');
        fmt_picker_span.append(fmt_picker);
        nav_element.append(fmt_picker_span);
        this.format_dropdown = fmt_picker[0];
    
        for (var ind in mpl.extensions) {
            var fmt = mpl.extensions[ind];
            var option = $(
                '<option/>', {selected: fmt === mpl.default_extension}).html(fmt);
            fmt_picker.append(option);
        }
    
        // Add hover states to the ui-buttons
        $( ".ui-button" ).hover(
            function() { $(this).addClass("ui-state-hover");},
            function() { $(this).removeClass("ui-state-hover");}
        );
    
        var status_bar = $('<span class="mpl-message"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    }
    
    mpl.figure.prototype.request_resize = function(x_pixels, y_pixels) {
        // Request matplotlib to resize the figure. Matplotlib will then trigger a resize in the client,
        // which will in turn request a refresh of the image.
        this.send_message('resize', {'width': x_pixels, 'height': y_pixels});
    }
    
    mpl.figure.prototype.send_message = function(type, properties) {
        properties['type'] = type;
        properties['figure_id'] = this.id;
        this.ws.send(JSON.stringify(properties));
    }
    
    mpl.figure.prototype.send_draw_message = function() {
        if (!this.waiting) {
            this.waiting = true;
            this.ws.send(JSON.stringify({type: "draw", figure_id: this.id}));
        }
    }
    
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        var format_dropdown = fig.format_dropdown;
        var format = format_dropdown.options[format_dropdown.selectedIndex].value;
        fig.ondownload(fig, format);
    }
    
    
    mpl.figure.prototype.handle_resize = function(fig, msg) {
        var size = msg['size'];
        if (size[0] != fig.canvas.width || size[1] != fig.canvas.height) {
            fig._resize_canvas(size[0], size[1]);
            fig.send_message("refresh", {});
        };
    }
    
    mpl.figure.prototype.handle_rubberband = function(fig, msg) {
        var x0 = msg['x0'] / mpl.ratio;
        var y0 = (fig.canvas.height - msg['y0']) / mpl.ratio;
        var x1 = msg['x1'] / mpl.ratio;
        var y1 = (fig.canvas.height - msg['y1']) / mpl.ratio;
        x0 = Math.floor(x0) + 0.5;
        y0 = Math.floor(y0) + 0.5;
        x1 = Math.floor(x1) + 0.5;
        y1 = Math.floor(y1) + 0.5;
        var min_x = Math.min(x0, x1);
        var min_y = Math.min(y0, y1);
        var width = Math.abs(x1 - x0);
        var height = Math.abs(y1 - y0);
    
        fig.rubberband_context.clearRect(
            0, 0, fig.canvas.width / mpl.ratio, fig.canvas.height / mpl.ratio);
    
        fig.rubberband_context.strokeRect(min_x, min_y, width, height);
    }
    
    mpl.figure.prototype.handle_figure_label = function(fig, msg) {
        // Updates the figure title.
        fig.header.textContent = msg['label'];
    }
    
    mpl.figure.prototype.handle_cursor = function(fig, msg) {
        var cursor = msg['cursor'];
        switch(cursor)
        {
        case 0:
            cursor = 'pointer';
            break;
        case 1:
            cursor = 'default';
            break;
        case 2:
            cursor = 'crosshair';
            break;
        case 3:
            cursor = 'move';
            break;
        }
        fig.rubberband_canvas.style.cursor = cursor;
    }
    
    mpl.figure.prototype.handle_message = function(fig, msg) {
        fig.message.textContent = msg['message'];
    }
    
    mpl.figure.prototype.handle_draw = function(fig, msg) {
        // Request the server to send over a new figure.
        fig.send_draw_message();
    }
    
    mpl.figure.prototype.handle_image_mode = function(fig, msg) {
        fig.image_mode = msg['mode'];
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Called whenever the canvas gets updated.
        this.send_message("ack", {});
    }
    
    // A function to construct a web socket function for onmessage handling.
    // Called in the figure constructor.
    mpl.figure.prototype._make_on_message_function = function(fig) {
        return function socket_on_message(evt) {
            if (evt.data instanceof Blob) {
                /* FIXME: We get "Resource interpreted as Image but
                 * transferred with MIME type text/plain:" errors on
                 * Chrome.  But how to set the MIME type?  It doesn't seem
                 * to be part of the websocket stream */
                evt.data.type = "image/png";
    
                /* Free the memory for the previous frames */
                if (fig.imageObj.src) {
                    (window.URL || window.webkitURL).revokeObjectURL(
                        fig.imageObj.src);
                }
    
                fig.imageObj.src = (window.URL || window.webkitURL).createObjectURL(
                    evt.data);
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
            else if (typeof evt.data === 'string' && evt.data.slice(0, 21) == "data:image/png;base64") {
                fig.imageObj.src = evt.data;
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
    
            var msg = JSON.parse(evt.data);
            var msg_type = msg['type'];
    
            // Call the  "handle_{type}" callback, which takes
            // the figure and JSON message as its only arguments.
            try {
                var callback = fig["handle_" + msg_type];
            } catch (e) {
                console.log("No handler for the '" + msg_type + "' message type: ", msg);
                return;
            }
    
            if (callback) {
                try {
                    // console.log("Handling '" + msg_type + "' message: ", msg);
                    callback(fig, msg);
                } catch (e) {
                    console.log("Exception inside the 'handler_" + msg_type + "' callback:", e, e.stack, msg);
                }
            }
        };
    }
    
    // from http://stackoverflow.com/questions/1114465/getting-mouse-location-in-canvas
    mpl.findpos = function(e) {
        //this section is from http://www.quirksmode.org/js/events_properties.html
        var targ;
        if (!e)
            e = window.event;
        if (e.target)
            targ = e.target;
        else if (e.srcElement)
            targ = e.srcElement;
        if (targ.nodeType == 3) // defeat Safari bug
            targ = targ.parentNode;
    
        // jQuery normalizes the pageX and pageY
        // pageX,Y are the mouse positions relative to the document
        // offset() returns the position of the element relative to the document
        var x = e.pageX - $(targ).offset().left;
        var y = e.pageY - $(targ).offset().top;
    
        return {"x": x, "y": y};
    };
    
    /*
     * return a copy of an object with only non-object keys
     * we need this to avoid circular references
     * http://stackoverflow.com/a/24161582/3208463
     */
    function simpleKeys (original) {
      return Object.keys(original).reduce(function (obj, key) {
        if (typeof original[key] !== 'object')
            obj[key] = original[key]
        return obj;
      }, {});
    }
    
    mpl.figure.prototype.mouse_event = function(event, name) {
        var canvas_pos = mpl.findpos(event)
    
        if (name === 'button_press')
        {
            this.canvas.focus();
            this.canvas_div.focus();
        }
    
        var x = canvas_pos.x * mpl.ratio;
        var y = canvas_pos.y * mpl.ratio;
    
        this.send_message(name, {x: x, y: y, button: event.button,
                                 step: event.step,
                                 guiEvent: simpleKeys(event)});
    
        /* This prevents the web browser from automatically changing to
         * the text insertion cursor when the button is pressed.  We want
         * to control all of the cursor setting manually through the
         * 'cursor' event from matplotlib */
        event.preventDefault();
        return false;
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        // Handle any extra behaviour associated with a key event
    }
    
    mpl.figure.prototype.key_event = function(event, name) {
    
        // Prevent repeat events
        if (name == 'key_press')
        {
            if (event.which === this._key)
                return;
            else
                this._key = event.which;
        }
        if (name == 'key_release')
            this._key = null;
    
        var value = '';
        if (event.ctrlKey && event.which != 17)
            value += "ctrl+";
        if (event.altKey && event.which != 18)
            value += "alt+";
        if (event.shiftKey && event.which != 16)
            value += "shift+";
    
        value += 'k';
        value += event.which.toString();
    
        this._key_event_extra(event, name);
    
        this.send_message(name, {key: value,
                                 guiEvent: simpleKeys(event)});
        return false;
    }
    
    mpl.figure.prototype.toolbar_button_onclick = function(name) {
        if (name == 'download') {
            this.handle_save(this, null);
        } else {
            this.send_message("toolbar_button", {name: name});
        }
    };
    
    mpl.figure.prototype.toolbar_button_onmouseover = function(tooltip) {
        this.message.textContent = tooltip;
    };
    mpl.toolbar_items = [["Home", "Reset original view", "fa fa-home icon-home", "home"], ["Back", "Back to previous view", "fa fa-arrow-left icon-arrow-left", "back"], ["Forward", "Forward to next view", "fa fa-arrow-right icon-arrow-right", "forward"], ["", "", "", ""], ["Pan", "Pan axes with left mouse, zoom with right", "fa fa-arrows icon-move", "pan"], ["Zoom", "Zoom to rectangle", "fa fa-square-o icon-check-empty", "zoom"], ["", "", "", ""], ["Download", "Download plot", "fa fa-floppy-o icon-save", "download"]];
    
    mpl.extensions = ["eps", "jpeg", "pdf", "png", "ps", "raw", "svg", "tif"];
    
    mpl.default_extension = "png";var comm_websocket_adapter = function(comm) {
        // Create a "websocket"-like object which calls the given IPython comm
        // object with the appropriate methods. Currently this is a non binary
        // socket, so there is still some room for performance tuning.
        var ws = {};
    
        ws.close = function() {
            comm.close()
        };
        ws.send = function(m) {
            //console.log('sending', m);
            comm.send(m);
        };
        // Register the callback with on_msg.
        comm.on_msg(function(msg) {
            //console.log('receiving', msg['content']['data'], msg);
            // Pass the mpl event to the overridden (by mpl) onmessage function.
            ws.onmessage(msg['content']['data'])
        });
        return ws;
    }
    
    mpl.mpl_figure_comm = function(comm, msg) {
        // This is the function which gets called when the mpl process
        // starts-up an IPython Comm through the "matplotlib" channel.
    
        var id = msg.content.data.id;
        // Get hold of the div created by the display call when the Comm
        // socket was opened in Python.
        var element = $("#" + id);
        var ws_proxy = comm_websocket_adapter(comm)
    
        function ondownload(figure, format) {
            window.open(figure.imageObj.src);
        }
    
        var fig = new mpl.figure(id, ws_proxy,
                               ondownload,
                               element.get(0));
    
        // Call onopen now - mpl needs it, as it is assuming we've passed it a real
        // web socket which is closed, not our websocket->open comm proxy.
        ws_proxy.onopen();
    
        fig.parent_element = element.get(0);
        fig.cell_info = mpl.find_output_cell("<div id='" + id + "'></div>");
        if (!fig.cell_info) {
            console.error("Failed to find cell for figure", id, fig);
            return;
        }
    
        var output_index = fig.cell_info[2]
        var cell = fig.cell_info[0];
    
    };
    
    mpl.figure.prototype.handle_close = function(fig, msg) {
        var width = fig.canvas.width/mpl.ratio
        fig.root.unbind('remove')
    
        // Update the output cell to use the data from the current canvas.
        fig.push_to_output();
        var dataURL = fig.canvas.toDataURL();
        // Re-enable the keyboard manager in IPython - without this line, in FF,
        // the notebook keyboard shortcuts fail.
        IPython.keyboard_manager.enable()
        $(fig.parent_element).html('<img src="' + dataURL + '" width="' + width + '">');
        fig.close_ws(fig, msg);
    }
    
    mpl.figure.prototype.close_ws = function(fig, msg){
        fig.send_message('closing', msg);
        // fig.ws.close()
    }
    
    mpl.figure.prototype.push_to_output = function(remove_interactive) {
        // Turn the data on the canvas into data in the output cell.
        var width = this.canvas.width/mpl.ratio
        var dataURL = this.canvas.toDataURL();
        this.cell_info[1]['text/html'] = '<img src="' + dataURL + '" width="' + width + '">';
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Tell IPython that the notebook contents must change.
        IPython.notebook.set_dirty(true);
        this.send_message("ack", {});
        var fig = this;
        // Wait a second, then push the new image to the DOM so
        // that it is saved nicely (might be nice to debounce this).
        setTimeout(function () { fig.push_to_output() }, 1000);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items){
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) { continue; };
    
            var button = $('<button class="btn btn-default" href="#" title="' + name + '"><i class="fa ' + image + ' fa-lg"></i></button>');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
            nav_element.append(button);
        }
    
        // Add the status bar.
        var status_bar = $('<span class="mpl-message" style="text-align:right; float: right;"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    
        // Add the close button to the window.
        var buttongrp = $('<div class="btn-group inline pull-right"></div>');
        var button = $('<button class="btn btn-mini btn-primary" href="#" title="Stop Interaction"><i class="fa fa-power-off icon-remove icon-large"></i></button>');
        button.click(function (evt) { fig.handle_close(fig, {}); } );
        button.mouseover('Stop Interaction', toolbar_mouse_event);
        buttongrp.append(button);
        var titlebar = this.root.find($('.ui-dialog-titlebar'));
        titlebar.prepend(buttongrp);
    }
    
    mpl.figure.prototype._root_extra_style = function(el){
        var fig = this
        el.on("remove", function(){
    	fig.close_ws(fig, {});
        });
    }
    
    mpl.figure.prototype._canvas_extra_style = function(el){
        // this is important to make the div 'focusable
        el.attr('tabindex', 0)
        // reach out to IPython and tell the keyboard manager to turn it's self
        // off when our div gets focus
    
        // location in version 3
        if (IPython.notebook.keyboard_manager) {
            IPython.notebook.keyboard_manager.register_events(el);
        }
        else {
            // location in version 2
            IPython.keyboard_manager.register_events(el);
        }
    
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        var manager = IPython.notebook.keyboard_manager;
        if (!manager)
            manager = IPython.keyboard_manager;
    
        // Check for shift+enter
        if (event.shiftKey && event.which == 13) {
            this.canvas_div.blur();
            event.shiftKey = false;
            // Send a "J" for go to next cell
            event.which = 74;
            event.keyCode = 74;
            manager.command_mode();
            manager.handle_keydown(event);
        }
    }
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        fig.ondownload(fig, null);
    }
    
    
    mpl.find_output_cell = function(html_output) {
        // Return the cell and output element which can be found *uniquely* in the notebook.
        // Note - this is a bit hacky, but it is done because the "notebook_saving.Notebook"
        // IPython event is triggered only after the cells have been serialised, which for
        // our purposes (turning an active figure into a static one), is too late.
        var cells = IPython.notebook.get_cells();
        var ncells = cells.length;
        for (var i=0; i<ncells; i++) {
            var cell = cells[i];
            if (cell.cell_type === 'code'){
                for (var j=0; j<cell.output_area.outputs.length; j++) {
                    var data = cell.output_area.outputs[j];
                    if (data.data) {
                        // IPython >= 3 moved mimebundle to data attribute of output
                        data = data.data;
                    }
                    if (data['text/html'] == html_output) {
                        return [cell, data, j];
                    }
                }
            }
        }
    }
    
    // Register the function which deals with the matplotlib target/channel.
    // The kernel may be null if the page has been refreshed.
    if (IPython.notebook.kernel != null) {
        IPython.notebook.kernel.comm_manager.register_target('matplotlib', mpl.mpl_figure_comm);
    }
    
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        <div id='1dc73813-8ea0-4c3e-9f26-b736b694b049'></div>
    </div>


Automated SNR Plotting
----------------------

Luckily, you can do this SNR plotting pretty easily. The following code
shows off the ChipWhisperer function for doing so, based on a leakage
model (same one as used by CPA attack).


**In [15]:**

.. code:: ipython3

    %matplotlib notebook
    
    import chipwhisperer.analyzer as cwa
    import matplotlib.pyplot as plt
    
    leak_model = cwa.leakage_models.sbox_output
    
    snrdb = cwa.calculate_snr(traces, leak_model=leak_model)
    
    plt.plot(snrdb)
    plt.title("SNR of Byte 0: " + leak_model.modelobj.name)
    plt.xlabel("Sample No.")
    plt.ylabel("SNR (dB)")
    plt.show()
    


**Out [15]:**


.. raw:: html

    

    <div id="11cf3c94-bd25-4974-a7dd-f297285032f5"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#11cf3c94-bd25-4974-a7dd-f297285032f5');
        /* Put everything inside the global mpl namespace */
    window.mpl = {};
    
    
    mpl.get_websocket_type = function() {
        if (typeof(WebSocket) !== 'undefined') {
            return WebSocket;
        } else if (typeof(MozWebSocket) !== 'undefined') {
            return MozWebSocket;
        } else {
            alert('Your browser does not have WebSocket support. ' +
                  'Please try Chrome, Safari or Firefox ≥ 6. ' +
                  'Firefox 4 and 5 are also supported but you ' +
                  'have to enable WebSockets in about:config.');
        };
    }
    
    mpl.figure = function(figure_id, websocket, ondownload, parent_element) {
        this.id = figure_id;
    
        this.ws = websocket;
    
        this.supports_binary = (this.ws.binaryType != undefined);
    
        if (!this.supports_binary) {
            var warnings = document.getElementById("mpl-warnings");
            if (warnings) {
                warnings.style.display = 'block';
                warnings.textContent = (
                    "This browser does not support binary websocket messages. " +
                        "Performance may be slow.");
            }
        }
    
        this.imageObj = new Image();
    
        this.context = undefined;
        this.message = undefined;
        this.canvas = undefined;
        this.rubberband_canvas = undefined;
        this.rubberband_context = undefined;
        this.format_dropdown = undefined;
    
        this.image_mode = 'full';
    
        this.root = $('<div/>');
        this._root_extra_style(this.root)
        this.root.attr('style', 'display: inline-block');
    
        $(parent_element).append(this.root);
    
        this._init_header(this);
        this._init_canvas(this);
        this._init_toolbar(this);
    
        var fig = this;
    
        this.waiting = false;
    
        this.ws.onopen =  function () {
                fig.send_message("supports_binary", {value: fig.supports_binary});
                fig.send_message("send_image_mode", {});
                if (mpl.ratio != 1) {
                    fig.send_message("set_dpi_ratio", {'dpi_ratio': mpl.ratio});
                }
                fig.send_message("refresh", {});
            }
    
        this.imageObj.onload = function() {
                if (fig.image_mode == 'full') {
                    // Full images could contain transparency (where diff images
                    // almost always do), so we need to clear the canvas so that
                    // there is no ghosting.
                    fig.context.clearRect(0, 0, fig.canvas.width, fig.canvas.height);
                }
                fig.context.drawImage(fig.imageObj, 0, 0);
            };
    
        this.imageObj.onunload = function() {
            fig.ws.close();
        }
    
        this.ws.onmessage = this._make_on_message_function(this);
    
        this.ondownload = ondownload;
    }
    
    mpl.figure.prototype._init_header = function() {
        var titlebar = $(
            '<div class="ui-dialog-titlebar ui-widget-header ui-corner-all ' +
            'ui-helper-clearfix"/>');
        var titletext = $(
            '<div class="ui-dialog-title" style="width: 100%; ' +
            'text-align: center; padding: 3px;"/>');
        titlebar.append(titletext)
        this.root.append(titlebar);
        this.header = titletext[0];
    }
    
    
    
    mpl.figure.prototype._canvas_extra_style = function(canvas_div) {
    
    }
    
    
    mpl.figure.prototype._root_extra_style = function(canvas_div) {
    
    }
    
    mpl.figure.prototype._init_canvas = function() {
        var fig = this;
    
        var canvas_div = $('<div/>');
    
        canvas_div.attr('style', 'position: relative; clear: both; outline: 0');
    
        function canvas_keyboard_event(event) {
            return fig.key_event(event, event['data']);
        }
    
        canvas_div.keydown('key_press', canvas_keyboard_event);
        canvas_div.keyup('key_release', canvas_keyboard_event);
        this.canvas_div = canvas_div
        this._canvas_extra_style(canvas_div)
        this.root.append(canvas_div);
    
        var canvas = $('<canvas/>');
        canvas.addClass('mpl-canvas');
        canvas.attr('style', "left: 0; top: 0; z-index: 0; outline: 0")
    
        this.canvas = canvas[0];
        this.context = canvas[0].getContext("2d");
    
        var backingStore = this.context.backingStorePixelRatio ||
    	this.context.webkitBackingStorePixelRatio ||
    	this.context.mozBackingStorePixelRatio ||
    	this.context.msBackingStorePixelRatio ||
    	this.context.oBackingStorePixelRatio ||
    	this.context.backingStorePixelRatio || 1;
    
        mpl.ratio = (window.devicePixelRatio || 1) / backingStore;
    
        var rubberband = $('<canvas/>');
        rubberband.attr('style', "position: absolute; left: 0; top: 0; z-index: 1;")
    
        var pass_mouse_events = true;
    
        canvas_div.resizable({
            start: function(event, ui) {
                pass_mouse_events = false;
            },
            resize: function(event, ui) {
                fig.request_resize(ui.size.width, ui.size.height);
            },
            stop: function(event, ui) {
                pass_mouse_events = true;
                fig.request_resize(ui.size.width, ui.size.height);
            },
        });
    
        function mouse_event_fn(event) {
            if (pass_mouse_events)
                return fig.mouse_event(event, event['data']);
        }
    
        rubberband.mousedown('button_press', mouse_event_fn);
        rubberband.mouseup('button_release', mouse_event_fn);
        // Throttle sequential mouse events to 1 every 20ms.
        rubberband.mousemove('motion_notify', mouse_event_fn);
    
        rubberband.mouseenter('figure_enter', mouse_event_fn);
        rubberband.mouseleave('figure_leave', mouse_event_fn);
    
        canvas_div.on("wheel", function (event) {
            event = event.originalEvent;
            event['data'] = 'scroll'
            if (event.deltaY < 0) {
                event.step = 1;
            } else {
                event.step = -1;
            }
            mouse_event_fn(event);
        });
    
        canvas_div.append(canvas);
        canvas_div.append(rubberband);
    
        this.rubberband = rubberband;
        this.rubberband_canvas = rubberband[0];
        this.rubberband_context = rubberband[0].getContext("2d");
        this.rubberband_context.strokeStyle = "#000000";
    
        this._resize_canvas = function(width, height) {
            // Keep the size of the canvas, canvas container, and rubber band
            // canvas in synch.
            canvas_div.css('width', width)
            canvas_div.css('height', height)
    
            canvas.attr('width', width * mpl.ratio);
            canvas.attr('height', height * mpl.ratio);
            canvas.attr('style', 'width: ' + width + 'px; height: ' + height + 'px;');
    
            rubberband.attr('width', width);
            rubberband.attr('height', height);
        }
    
        // Set the figure to an initial 600x600px, this will subsequently be updated
        // upon first draw.
        this._resize_canvas(600, 600);
    
        // Disable right mouse context menu.
        $(this.rubberband_canvas).bind("contextmenu",function(e){
            return false;
        });
    
        function set_focus () {
            canvas.focus();
            canvas_div.focus();
        }
    
        window.setTimeout(set_focus, 100);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items) {
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) {
                // put a spacer in here.
                continue;
            }
            var button = $('<button/>');
            button.addClass('ui-button ui-widget ui-state-default ui-corner-all ' +
                            'ui-button-icon-only');
            button.attr('role', 'button');
            button.attr('aria-disabled', 'false');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
    
            var icon_img = $('<span/>');
            icon_img.addClass('ui-button-icon-primary ui-icon');
            icon_img.addClass(image);
            icon_img.addClass('ui-corner-all');
    
            var tooltip_span = $('<span/>');
            tooltip_span.addClass('ui-button-text');
            tooltip_span.html(tooltip);
    
            button.append(icon_img);
            button.append(tooltip_span);
    
            nav_element.append(button);
        }
    
        var fmt_picker_span = $('<span/>');
    
        var fmt_picker = $('<select/>');
        fmt_picker.addClass('mpl-toolbar-option ui-widget ui-widget-content');
        fmt_picker_span.append(fmt_picker);
        nav_element.append(fmt_picker_span);
        this.format_dropdown = fmt_picker[0];
    
        for (var ind in mpl.extensions) {
            var fmt = mpl.extensions[ind];
            var option = $(
                '<option/>', {selected: fmt === mpl.default_extension}).html(fmt);
            fmt_picker.append(option);
        }
    
        // Add hover states to the ui-buttons
        $( ".ui-button" ).hover(
            function() { $(this).addClass("ui-state-hover");},
            function() { $(this).removeClass("ui-state-hover");}
        );
    
        var status_bar = $('<span class="mpl-message"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    }
    
    mpl.figure.prototype.request_resize = function(x_pixels, y_pixels) {
        // Request matplotlib to resize the figure. Matplotlib will then trigger a resize in the client,
        // which will in turn request a refresh of the image.
        this.send_message('resize', {'width': x_pixels, 'height': y_pixels});
    }
    
    mpl.figure.prototype.send_message = function(type, properties) {
        properties['type'] = type;
        properties['figure_id'] = this.id;
        this.ws.send(JSON.stringify(properties));
    }
    
    mpl.figure.prototype.send_draw_message = function() {
        if (!this.waiting) {
            this.waiting = true;
            this.ws.send(JSON.stringify({type: "draw", figure_id: this.id}));
        }
    }
    
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        var format_dropdown = fig.format_dropdown;
        var format = format_dropdown.options[format_dropdown.selectedIndex].value;
        fig.ondownload(fig, format);
    }
    
    
    mpl.figure.prototype.handle_resize = function(fig, msg) {
        var size = msg['size'];
        if (size[0] != fig.canvas.width || size[1] != fig.canvas.height) {
            fig._resize_canvas(size[0], size[1]);
            fig.send_message("refresh", {});
        };
    }
    
    mpl.figure.prototype.handle_rubberband = function(fig, msg) {
        var x0 = msg['x0'] / mpl.ratio;
        var y0 = (fig.canvas.height - msg['y0']) / mpl.ratio;
        var x1 = msg['x1'] / mpl.ratio;
        var y1 = (fig.canvas.height - msg['y1']) / mpl.ratio;
        x0 = Math.floor(x0) + 0.5;
        y0 = Math.floor(y0) + 0.5;
        x1 = Math.floor(x1) + 0.5;
        y1 = Math.floor(y1) + 0.5;
        var min_x = Math.min(x0, x1);
        var min_y = Math.min(y0, y1);
        var width = Math.abs(x1 - x0);
        var height = Math.abs(y1 - y0);
    
        fig.rubberband_context.clearRect(
            0, 0, fig.canvas.width / mpl.ratio, fig.canvas.height / mpl.ratio);
    
        fig.rubberband_context.strokeRect(min_x, min_y, width, height);
    }
    
    mpl.figure.prototype.handle_figure_label = function(fig, msg) {
        // Updates the figure title.
        fig.header.textContent = msg['label'];
    }
    
    mpl.figure.prototype.handle_cursor = function(fig, msg) {
        var cursor = msg['cursor'];
        switch(cursor)
        {
        case 0:
            cursor = 'pointer';
            break;
        case 1:
            cursor = 'default';
            break;
        case 2:
            cursor = 'crosshair';
            break;
        case 3:
            cursor = 'move';
            break;
        }
        fig.rubberband_canvas.style.cursor = cursor;
    }
    
    mpl.figure.prototype.handle_message = function(fig, msg) {
        fig.message.textContent = msg['message'];
    }
    
    mpl.figure.prototype.handle_draw = function(fig, msg) {
        // Request the server to send over a new figure.
        fig.send_draw_message();
    }
    
    mpl.figure.prototype.handle_image_mode = function(fig, msg) {
        fig.image_mode = msg['mode'];
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Called whenever the canvas gets updated.
        this.send_message("ack", {});
    }
    
    // A function to construct a web socket function for onmessage handling.
    // Called in the figure constructor.
    mpl.figure.prototype._make_on_message_function = function(fig) {
        return function socket_on_message(evt) {
            if (evt.data instanceof Blob) {
                /* FIXME: We get "Resource interpreted as Image but
                 * transferred with MIME type text/plain:" errors on
                 * Chrome.  But how to set the MIME type?  It doesn't seem
                 * to be part of the websocket stream */
                evt.data.type = "image/png";
    
                /* Free the memory for the previous frames */
                if (fig.imageObj.src) {
                    (window.URL || window.webkitURL).revokeObjectURL(
                        fig.imageObj.src);
                }
    
                fig.imageObj.src = (window.URL || window.webkitURL).createObjectURL(
                    evt.data);
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
            else if (typeof evt.data === 'string' && evt.data.slice(0, 21) == "data:image/png;base64") {
                fig.imageObj.src = evt.data;
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
    
            var msg = JSON.parse(evt.data);
            var msg_type = msg['type'];
    
            // Call the  "handle_{type}" callback, which takes
            // the figure and JSON message as its only arguments.
            try {
                var callback = fig["handle_" + msg_type];
            } catch (e) {
                console.log("No handler for the '" + msg_type + "' message type: ", msg);
                return;
            }
    
            if (callback) {
                try {
                    // console.log("Handling '" + msg_type + "' message: ", msg);
                    callback(fig, msg);
                } catch (e) {
                    console.log("Exception inside the 'handler_" + msg_type + "' callback:", e, e.stack, msg);
                }
            }
        };
    }
    
    // from http://stackoverflow.com/questions/1114465/getting-mouse-location-in-canvas
    mpl.findpos = function(e) {
        //this section is from http://www.quirksmode.org/js/events_properties.html
        var targ;
        if (!e)
            e = window.event;
        if (e.target)
            targ = e.target;
        else if (e.srcElement)
            targ = e.srcElement;
        if (targ.nodeType == 3) // defeat Safari bug
            targ = targ.parentNode;
    
        // jQuery normalizes the pageX and pageY
        // pageX,Y are the mouse positions relative to the document
        // offset() returns the position of the element relative to the document
        var x = e.pageX - $(targ).offset().left;
        var y = e.pageY - $(targ).offset().top;
    
        return {"x": x, "y": y};
    };
    
    /*
     * return a copy of an object with only non-object keys
     * we need this to avoid circular references
     * http://stackoverflow.com/a/24161582/3208463
     */
    function simpleKeys (original) {
      return Object.keys(original).reduce(function (obj, key) {
        if (typeof original[key] !== 'object')
            obj[key] = original[key]
        return obj;
      }, {});
    }
    
    mpl.figure.prototype.mouse_event = function(event, name) {
        var canvas_pos = mpl.findpos(event)
    
        if (name === 'button_press')
        {
            this.canvas.focus();
            this.canvas_div.focus();
        }
    
        var x = canvas_pos.x * mpl.ratio;
        var y = canvas_pos.y * mpl.ratio;
    
        this.send_message(name, {x: x, y: y, button: event.button,
                                 step: event.step,
                                 guiEvent: simpleKeys(event)});
    
        /* This prevents the web browser from automatically changing to
         * the text insertion cursor when the button is pressed.  We want
         * to control all of the cursor setting manually through the
         * 'cursor' event from matplotlib */
        event.preventDefault();
        return false;
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        // Handle any extra behaviour associated with a key event
    }
    
    mpl.figure.prototype.key_event = function(event, name) {
    
        // Prevent repeat events
        if (name == 'key_press')
        {
            if (event.which === this._key)
                return;
            else
                this._key = event.which;
        }
        if (name == 'key_release')
            this._key = null;
    
        var value = '';
        if (event.ctrlKey && event.which != 17)
            value += "ctrl+";
        if (event.altKey && event.which != 18)
            value += "alt+";
        if (event.shiftKey && event.which != 16)
            value += "shift+";
    
        value += 'k';
        value += event.which.toString();
    
        this._key_event_extra(event, name);
    
        this.send_message(name, {key: value,
                                 guiEvent: simpleKeys(event)});
        return false;
    }
    
    mpl.figure.prototype.toolbar_button_onclick = function(name) {
        if (name == 'download') {
            this.handle_save(this, null);
        } else {
            this.send_message("toolbar_button", {name: name});
        }
    };
    
    mpl.figure.prototype.toolbar_button_onmouseover = function(tooltip) {
        this.message.textContent = tooltip;
    };
    mpl.toolbar_items = [["Home", "Reset original view", "fa fa-home icon-home", "home"], ["Back", "Back to previous view", "fa fa-arrow-left icon-arrow-left", "back"], ["Forward", "Forward to next view", "fa fa-arrow-right icon-arrow-right", "forward"], ["", "", "", ""], ["Pan", "Pan axes with left mouse, zoom with right", "fa fa-arrows icon-move", "pan"], ["Zoom", "Zoom to rectangle", "fa fa-square-o icon-check-empty", "zoom"], ["", "", "", ""], ["Download", "Download plot", "fa fa-floppy-o icon-save", "download"]];
    
    mpl.extensions = ["eps", "jpeg", "pdf", "png", "ps", "raw", "svg", "tif"];
    
    mpl.default_extension = "png";var comm_websocket_adapter = function(comm) {
        // Create a "websocket"-like object which calls the given IPython comm
        // object with the appropriate methods. Currently this is a non binary
        // socket, so there is still some room for performance tuning.
        var ws = {};
    
        ws.close = function() {
            comm.close()
        };
        ws.send = function(m) {
            //console.log('sending', m);
            comm.send(m);
        };
        // Register the callback with on_msg.
        comm.on_msg(function(msg) {
            //console.log('receiving', msg['content']['data'], msg);
            // Pass the mpl event to the overridden (by mpl) onmessage function.
            ws.onmessage(msg['content']['data'])
        });
        return ws;
    }
    
    mpl.mpl_figure_comm = function(comm, msg) {
        // This is the function which gets called when the mpl process
        // starts-up an IPython Comm through the "matplotlib" channel.
    
        var id = msg.content.data.id;
        // Get hold of the div created by the display call when the Comm
        // socket was opened in Python.
        var element = $("#" + id);
        var ws_proxy = comm_websocket_adapter(comm)
    
        function ondownload(figure, format) {
            window.open(figure.imageObj.src);
        }
    
        var fig = new mpl.figure(id, ws_proxy,
                               ondownload,
                               element.get(0));
    
        // Call onopen now - mpl needs it, as it is assuming we've passed it a real
        // web socket which is closed, not our websocket->open comm proxy.
        ws_proxy.onopen();
    
        fig.parent_element = element.get(0);
        fig.cell_info = mpl.find_output_cell("<div id='" + id + "'></div>");
        if (!fig.cell_info) {
            console.error("Failed to find cell for figure", id, fig);
            return;
        }
    
        var output_index = fig.cell_info[2]
        var cell = fig.cell_info[0];
    
    };
    
    mpl.figure.prototype.handle_close = function(fig, msg) {
        var width = fig.canvas.width/mpl.ratio
        fig.root.unbind('remove')
    
        // Update the output cell to use the data from the current canvas.
        fig.push_to_output();
        var dataURL = fig.canvas.toDataURL();
        // Re-enable the keyboard manager in IPython - without this line, in FF,
        // the notebook keyboard shortcuts fail.
        IPython.keyboard_manager.enable()
        $(fig.parent_element).html('<img src="' + dataURL + '" width="' + width + '">');
        fig.close_ws(fig, msg);
    }
    
    mpl.figure.prototype.close_ws = function(fig, msg){
        fig.send_message('closing', msg);
        // fig.ws.close()
    }
    
    mpl.figure.prototype.push_to_output = function(remove_interactive) {
        // Turn the data on the canvas into data in the output cell.
        var width = this.canvas.width/mpl.ratio
        var dataURL = this.canvas.toDataURL();
        this.cell_info[1]['text/html'] = '<img src="' + dataURL + '" width="' + width + '">';
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Tell IPython that the notebook contents must change.
        IPython.notebook.set_dirty(true);
        this.send_message("ack", {});
        var fig = this;
        // Wait a second, then push the new image to the DOM so
        // that it is saved nicely (might be nice to debounce this).
        setTimeout(function () { fig.push_to_output() }, 1000);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items){
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) { continue; };
    
            var button = $('<button class="btn btn-default" href="#" title="' + name + '"><i class="fa ' + image + ' fa-lg"></i></button>');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
            nav_element.append(button);
        }
    
        // Add the status bar.
        var status_bar = $('<span class="mpl-message" style="text-align:right; float: right;"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    
        // Add the close button to the window.
        var buttongrp = $('<div class="btn-group inline pull-right"></div>');
        var button = $('<button class="btn btn-mini btn-primary" href="#" title="Stop Interaction"><i class="fa fa-power-off icon-remove icon-large"></i></button>');
        button.click(function (evt) { fig.handle_close(fig, {}); } );
        button.mouseover('Stop Interaction', toolbar_mouse_event);
        buttongrp.append(button);
        var titlebar = this.root.find($('.ui-dialog-titlebar'));
        titlebar.prepend(buttongrp);
    }
    
    mpl.figure.prototype._root_extra_style = function(el){
        var fig = this
        el.on("remove", function(){
    	fig.close_ws(fig, {});
        });
    }
    
    mpl.figure.prototype._canvas_extra_style = function(el){
        // this is important to make the div 'focusable
        el.attr('tabindex', 0)
        // reach out to IPython and tell the keyboard manager to turn it's self
        // off when our div gets focus
    
        // location in version 3
        if (IPython.notebook.keyboard_manager) {
            IPython.notebook.keyboard_manager.register_events(el);
        }
        else {
            // location in version 2
            IPython.keyboard_manager.register_events(el);
        }
    
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        var manager = IPython.notebook.keyboard_manager;
        if (!manager)
            manager = IPython.keyboard_manager;
    
        // Check for shift+enter
        if (event.shiftKey && event.which == 13) {
            this.canvas_div.blur();
            event.shiftKey = false;
            // Send a "J" for go to next cell
            event.which = 74;
            event.keyCode = 74;
            manager.command_mode();
            manager.handle_keydown(event);
        }
    }
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        fig.ondownload(fig, null);
    }
    
    
    mpl.find_output_cell = function(html_output) {
        // Return the cell and output element which can be found *uniquely* in the notebook.
        // Note - this is a bit hacky, but it is done because the "notebook_saving.Notebook"
        // IPython event is triggered only after the cells have been serialised, which for
        // our purposes (turning an active figure into a static one), is too late.
        var cells = IPython.notebook.get_cells();
        var ncells = cells.length;
        for (var i=0; i<ncells; i++) {
            var cell = cells[i];
            if (cell.cell_type === 'code'){
                for (var j=0; j<cell.output_area.outputs.length; j++) {
                    var data = cell.output_area.outputs[j];
                    if (data.data) {
                        // IPython >= 3 moved mimebundle to data attribute of output
                        data = data.data;
                    }
                    if (data['text/html'] == html_output) {
                        return [cell, data, j];
                    }
                }
            }
        }
    }
    
    // Register the function which deals with the matplotlib target/channel.
    // The kernel may be null if the page has been refreshed.
    if (IPython.notebook.kernel != null) {
        IPython.notebook.kernel.comm_manager.register_target('matplotlib', mpl.mpl_figure_comm);
    }
    
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        <div id='c1fee8c0-ef6d-4802-b5c2-0ba366c2af8a'></div>
    </div>


The following shows usage with a project file for example:


**In [16]:**

.. code:: ipython3

    %matplotlib notebook
    import chipwhisperer as cw
    import chipwhisperer.analyzer as cwa
    
    project = cw.create_project("projects/snr")
    project.traces.extend(traces)
    
    leak_model = cwa.leakage_models.sbox_output
    
    snrdb = cwa.calculate_snr(project.traces, leak_model=leak_model)
    plt.plot(snrdb)
    plt.title("SNR of Byte 0: " + leak_model.modelobj.name)
    plt.xlabel("Sample No.")
    plt.ylabel("SNR (dB)")
    plt.show()


**Out [16]:**


.. raw:: html

    

    <div id="1e8245bf-2f58-49ea-98d4-4158be59ed61"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#1e8245bf-2f58-49ea-98d4-4158be59ed61');
        /* Put everything inside the global mpl namespace */
    window.mpl = {};
    
    
    mpl.get_websocket_type = function() {
        if (typeof(WebSocket) !== 'undefined') {
            return WebSocket;
        } else if (typeof(MozWebSocket) !== 'undefined') {
            return MozWebSocket;
        } else {
            alert('Your browser does not have WebSocket support. ' +
                  'Please try Chrome, Safari or Firefox ≥ 6. ' +
                  'Firefox 4 and 5 are also supported but you ' +
                  'have to enable WebSockets in about:config.');
        };
    }
    
    mpl.figure = function(figure_id, websocket, ondownload, parent_element) {
        this.id = figure_id;
    
        this.ws = websocket;
    
        this.supports_binary = (this.ws.binaryType != undefined);
    
        if (!this.supports_binary) {
            var warnings = document.getElementById("mpl-warnings");
            if (warnings) {
                warnings.style.display = 'block';
                warnings.textContent = (
                    "This browser does not support binary websocket messages. " +
                        "Performance may be slow.");
            }
        }
    
        this.imageObj = new Image();
    
        this.context = undefined;
        this.message = undefined;
        this.canvas = undefined;
        this.rubberband_canvas = undefined;
        this.rubberband_context = undefined;
        this.format_dropdown = undefined;
    
        this.image_mode = 'full';
    
        this.root = $('<div/>');
        this._root_extra_style(this.root)
        this.root.attr('style', 'display: inline-block');
    
        $(parent_element).append(this.root);
    
        this._init_header(this);
        this._init_canvas(this);
        this._init_toolbar(this);
    
        var fig = this;
    
        this.waiting = false;
    
        this.ws.onopen =  function () {
                fig.send_message("supports_binary", {value: fig.supports_binary});
                fig.send_message("send_image_mode", {});
                if (mpl.ratio != 1) {
                    fig.send_message("set_dpi_ratio", {'dpi_ratio': mpl.ratio});
                }
                fig.send_message("refresh", {});
            }
    
        this.imageObj.onload = function() {
                if (fig.image_mode == 'full') {
                    // Full images could contain transparency (where diff images
                    // almost always do), so we need to clear the canvas so that
                    // there is no ghosting.
                    fig.context.clearRect(0, 0, fig.canvas.width, fig.canvas.height);
                }
                fig.context.drawImage(fig.imageObj, 0, 0);
            };
    
        this.imageObj.onunload = function() {
            fig.ws.close();
        }
    
        this.ws.onmessage = this._make_on_message_function(this);
    
        this.ondownload = ondownload;
    }
    
    mpl.figure.prototype._init_header = function() {
        var titlebar = $(
            '<div class="ui-dialog-titlebar ui-widget-header ui-corner-all ' +
            'ui-helper-clearfix"/>');
        var titletext = $(
            '<div class="ui-dialog-title" style="width: 100%; ' +
            'text-align: center; padding: 3px;"/>');
        titlebar.append(titletext)
        this.root.append(titlebar);
        this.header = titletext[0];
    }
    
    
    
    mpl.figure.prototype._canvas_extra_style = function(canvas_div) {
    
    }
    
    
    mpl.figure.prototype._root_extra_style = function(canvas_div) {
    
    }
    
    mpl.figure.prototype._init_canvas = function() {
        var fig = this;
    
        var canvas_div = $('<div/>');
    
        canvas_div.attr('style', 'position: relative; clear: both; outline: 0');
    
        function canvas_keyboard_event(event) {
            return fig.key_event(event, event['data']);
        }
    
        canvas_div.keydown('key_press', canvas_keyboard_event);
        canvas_div.keyup('key_release', canvas_keyboard_event);
        this.canvas_div = canvas_div
        this._canvas_extra_style(canvas_div)
        this.root.append(canvas_div);
    
        var canvas = $('<canvas/>');
        canvas.addClass('mpl-canvas');
        canvas.attr('style', "left: 0; top: 0; z-index: 0; outline: 0")
    
        this.canvas = canvas[0];
        this.context = canvas[0].getContext("2d");
    
        var backingStore = this.context.backingStorePixelRatio ||
    	this.context.webkitBackingStorePixelRatio ||
    	this.context.mozBackingStorePixelRatio ||
    	this.context.msBackingStorePixelRatio ||
    	this.context.oBackingStorePixelRatio ||
    	this.context.backingStorePixelRatio || 1;
    
        mpl.ratio = (window.devicePixelRatio || 1) / backingStore;
    
        var rubberband = $('<canvas/>');
        rubberband.attr('style', "position: absolute; left: 0; top: 0; z-index: 1;")
    
        var pass_mouse_events = true;
    
        canvas_div.resizable({
            start: function(event, ui) {
                pass_mouse_events = false;
            },
            resize: function(event, ui) {
                fig.request_resize(ui.size.width, ui.size.height);
            },
            stop: function(event, ui) {
                pass_mouse_events = true;
                fig.request_resize(ui.size.width, ui.size.height);
            },
        });
    
        function mouse_event_fn(event) {
            if (pass_mouse_events)
                return fig.mouse_event(event, event['data']);
        }
    
        rubberband.mousedown('button_press', mouse_event_fn);
        rubberband.mouseup('button_release', mouse_event_fn);
        // Throttle sequential mouse events to 1 every 20ms.
        rubberband.mousemove('motion_notify', mouse_event_fn);
    
        rubberband.mouseenter('figure_enter', mouse_event_fn);
        rubberband.mouseleave('figure_leave', mouse_event_fn);
    
        canvas_div.on("wheel", function (event) {
            event = event.originalEvent;
            event['data'] = 'scroll'
            if (event.deltaY < 0) {
                event.step = 1;
            } else {
                event.step = -1;
            }
            mouse_event_fn(event);
        });
    
        canvas_div.append(canvas);
        canvas_div.append(rubberband);
    
        this.rubberband = rubberband;
        this.rubberband_canvas = rubberband[0];
        this.rubberband_context = rubberband[0].getContext("2d");
        this.rubberband_context.strokeStyle = "#000000";
    
        this._resize_canvas = function(width, height) {
            // Keep the size of the canvas, canvas container, and rubber band
            // canvas in synch.
            canvas_div.css('width', width)
            canvas_div.css('height', height)
    
            canvas.attr('width', width * mpl.ratio);
            canvas.attr('height', height * mpl.ratio);
            canvas.attr('style', 'width: ' + width + 'px; height: ' + height + 'px;');
    
            rubberband.attr('width', width);
            rubberband.attr('height', height);
        }
    
        // Set the figure to an initial 600x600px, this will subsequently be updated
        // upon first draw.
        this._resize_canvas(600, 600);
    
        // Disable right mouse context menu.
        $(this.rubberband_canvas).bind("contextmenu",function(e){
            return false;
        });
    
        function set_focus () {
            canvas.focus();
            canvas_div.focus();
        }
    
        window.setTimeout(set_focus, 100);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items) {
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) {
                // put a spacer in here.
                continue;
            }
            var button = $('<button/>');
            button.addClass('ui-button ui-widget ui-state-default ui-corner-all ' +
                            'ui-button-icon-only');
            button.attr('role', 'button');
            button.attr('aria-disabled', 'false');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
    
            var icon_img = $('<span/>');
            icon_img.addClass('ui-button-icon-primary ui-icon');
            icon_img.addClass(image);
            icon_img.addClass('ui-corner-all');
    
            var tooltip_span = $('<span/>');
            tooltip_span.addClass('ui-button-text');
            tooltip_span.html(tooltip);
    
            button.append(icon_img);
            button.append(tooltip_span);
    
            nav_element.append(button);
        }
    
        var fmt_picker_span = $('<span/>');
    
        var fmt_picker = $('<select/>');
        fmt_picker.addClass('mpl-toolbar-option ui-widget ui-widget-content');
        fmt_picker_span.append(fmt_picker);
        nav_element.append(fmt_picker_span);
        this.format_dropdown = fmt_picker[0];
    
        for (var ind in mpl.extensions) {
            var fmt = mpl.extensions[ind];
            var option = $(
                '<option/>', {selected: fmt === mpl.default_extension}).html(fmt);
            fmt_picker.append(option);
        }
    
        // Add hover states to the ui-buttons
        $( ".ui-button" ).hover(
            function() { $(this).addClass("ui-state-hover");},
            function() { $(this).removeClass("ui-state-hover");}
        );
    
        var status_bar = $('<span class="mpl-message"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    }
    
    mpl.figure.prototype.request_resize = function(x_pixels, y_pixels) {
        // Request matplotlib to resize the figure. Matplotlib will then trigger a resize in the client,
        // which will in turn request a refresh of the image.
        this.send_message('resize', {'width': x_pixels, 'height': y_pixels});
    }
    
    mpl.figure.prototype.send_message = function(type, properties) {
        properties['type'] = type;
        properties['figure_id'] = this.id;
        this.ws.send(JSON.stringify(properties));
    }
    
    mpl.figure.prototype.send_draw_message = function() {
        if (!this.waiting) {
            this.waiting = true;
            this.ws.send(JSON.stringify({type: "draw", figure_id: this.id}));
        }
    }
    
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        var format_dropdown = fig.format_dropdown;
        var format = format_dropdown.options[format_dropdown.selectedIndex].value;
        fig.ondownload(fig, format);
    }
    
    
    mpl.figure.prototype.handle_resize = function(fig, msg) {
        var size = msg['size'];
        if (size[0] != fig.canvas.width || size[1] != fig.canvas.height) {
            fig._resize_canvas(size[0], size[1]);
            fig.send_message("refresh", {});
        };
    }
    
    mpl.figure.prototype.handle_rubberband = function(fig, msg) {
        var x0 = msg['x0'] / mpl.ratio;
        var y0 = (fig.canvas.height - msg['y0']) / mpl.ratio;
        var x1 = msg['x1'] / mpl.ratio;
        var y1 = (fig.canvas.height - msg['y1']) / mpl.ratio;
        x0 = Math.floor(x0) + 0.5;
        y0 = Math.floor(y0) + 0.5;
        x1 = Math.floor(x1) + 0.5;
        y1 = Math.floor(y1) + 0.5;
        var min_x = Math.min(x0, x1);
        var min_y = Math.min(y0, y1);
        var width = Math.abs(x1 - x0);
        var height = Math.abs(y1 - y0);
    
        fig.rubberband_context.clearRect(
            0, 0, fig.canvas.width / mpl.ratio, fig.canvas.height / mpl.ratio);
    
        fig.rubberband_context.strokeRect(min_x, min_y, width, height);
    }
    
    mpl.figure.prototype.handle_figure_label = function(fig, msg) {
        // Updates the figure title.
        fig.header.textContent = msg['label'];
    }
    
    mpl.figure.prototype.handle_cursor = function(fig, msg) {
        var cursor = msg['cursor'];
        switch(cursor)
        {
        case 0:
            cursor = 'pointer';
            break;
        case 1:
            cursor = 'default';
            break;
        case 2:
            cursor = 'crosshair';
            break;
        case 3:
            cursor = 'move';
            break;
        }
        fig.rubberband_canvas.style.cursor = cursor;
    }
    
    mpl.figure.prototype.handle_message = function(fig, msg) {
        fig.message.textContent = msg['message'];
    }
    
    mpl.figure.prototype.handle_draw = function(fig, msg) {
        // Request the server to send over a new figure.
        fig.send_draw_message();
    }
    
    mpl.figure.prototype.handle_image_mode = function(fig, msg) {
        fig.image_mode = msg['mode'];
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Called whenever the canvas gets updated.
        this.send_message("ack", {});
    }
    
    // A function to construct a web socket function for onmessage handling.
    // Called in the figure constructor.
    mpl.figure.prototype._make_on_message_function = function(fig) {
        return function socket_on_message(evt) {
            if (evt.data instanceof Blob) {
                /* FIXME: We get "Resource interpreted as Image but
                 * transferred with MIME type text/plain:" errors on
                 * Chrome.  But how to set the MIME type?  It doesn't seem
                 * to be part of the websocket stream */
                evt.data.type = "image/png";
    
                /* Free the memory for the previous frames */
                if (fig.imageObj.src) {
                    (window.URL || window.webkitURL).revokeObjectURL(
                        fig.imageObj.src);
                }
    
                fig.imageObj.src = (window.URL || window.webkitURL).createObjectURL(
                    evt.data);
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
            else if (typeof evt.data === 'string' && evt.data.slice(0, 21) == "data:image/png;base64") {
                fig.imageObj.src = evt.data;
                fig.updated_canvas_event();
                fig.waiting = false;
                return;
            }
    
            var msg = JSON.parse(evt.data);
            var msg_type = msg['type'];
    
            // Call the  "handle_{type}" callback, which takes
            // the figure and JSON message as its only arguments.
            try {
                var callback = fig["handle_" + msg_type];
            } catch (e) {
                console.log("No handler for the '" + msg_type + "' message type: ", msg);
                return;
            }
    
            if (callback) {
                try {
                    // console.log("Handling '" + msg_type + "' message: ", msg);
                    callback(fig, msg);
                } catch (e) {
                    console.log("Exception inside the 'handler_" + msg_type + "' callback:", e, e.stack, msg);
                }
            }
        };
    }
    
    // from http://stackoverflow.com/questions/1114465/getting-mouse-location-in-canvas
    mpl.findpos = function(e) {
        //this section is from http://www.quirksmode.org/js/events_properties.html
        var targ;
        if (!e)
            e = window.event;
        if (e.target)
            targ = e.target;
        else if (e.srcElement)
            targ = e.srcElement;
        if (targ.nodeType == 3) // defeat Safari bug
            targ = targ.parentNode;
    
        // jQuery normalizes the pageX and pageY
        // pageX,Y are the mouse positions relative to the document
        // offset() returns the position of the element relative to the document
        var x = e.pageX - $(targ).offset().left;
        var y = e.pageY - $(targ).offset().top;
    
        return {"x": x, "y": y};
    };
    
    /*
     * return a copy of an object with only non-object keys
     * we need this to avoid circular references
     * http://stackoverflow.com/a/24161582/3208463
     */
    function simpleKeys (original) {
      return Object.keys(original).reduce(function (obj, key) {
        if (typeof original[key] !== 'object')
            obj[key] = original[key]
        return obj;
      }, {});
    }
    
    mpl.figure.prototype.mouse_event = function(event, name) {
        var canvas_pos = mpl.findpos(event)
    
        if (name === 'button_press')
        {
            this.canvas.focus();
            this.canvas_div.focus();
        }
    
        var x = canvas_pos.x * mpl.ratio;
        var y = canvas_pos.y * mpl.ratio;
    
        this.send_message(name, {x: x, y: y, button: event.button,
                                 step: event.step,
                                 guiEvent: simpleKeys(event)});
    
        /* This prevents the web browser from automatically changing to
         * the text insertion cursor when the button is pressed.  We want
         * to control all of the cursor setting manually through the
         * 'cursor' event from matplotlib */
        event.preventDefault();
        return false;
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        // Handle any extra behaviour associated with a key event
    }
    
    mpl.figure.prototype.key_event = function(event, name) {
    
        // Prevent repeat events
        if (name == 'key_press')
        {
            if (event.which === this._key)
                return;
            else
                this._key = event.which;
        }
        if (name == 'key_release')
            this._key = null;
    
        var value = '';
        if (event.ctrlKey && event.which != 17)
            value += "ctrl+";
        if (event.altKey && event.which != 18)
            value += "alt+";
        if (event.shiftKey && event.which != 16)
            value += "shift+";
    
        value += 'k';
        value += event.which.toString();
    
        this._key_event_extra(event, name);
    
        this.send_message(name, {key: value,
                                 guiEvent: simpleKeys(event)});
        return false;
    }
    
    mpl.figure.prototype.toolbar_button_onclick = function(name) {
        if (name == 'download') {
            this.handle_save(this, null);
        } else {
            this.send_message("toolbar_button", {name: name});
        }
    };
    
    mpl.figure.prototype.toolbar_button_onmouseover = function(tooltip) {
        this.message.textContent = tooltip;
    };
    mpl.toolbar_items = [["Home", "Reset original view", "fa fa-home icon-home", "home"], ["Back", "Back to previous view", "fa fa-arrow-left icon-arrow-left", "back"], ["Forward", "Forward to next view", "fa fa-arrow-right icon-arrow-right", "forward"], ["", "", "", ""], ["Pan", "Pan axes with left mouse, zoom with right", "fa fa-arrows icon-move", "pan"], ["Zoom", "Zoom to rectangle", "fa fa-square-o icon-check-empty", "zoom"], ["", "", "", ""], ["Download", "Download plot", "fa fa-floppy-o icon-save", "download"]];
    
    mpl.extensions = ["eps", "jpeg", "pdf", "png", "ps", "raw", "svg", "tif"];
    
    mpl.default_extension = "png";var comm_websocket_adapter = function(comm) {
        // Create a "websocket"-like object which calls the given IPython comm
        // object with the appropriate methods. Currently this is a non binary
        // socket, so there is still some room for performance tuning.
        var ws = {};
    
        ws.close = function() {
            comm.close()
        };
        ws.send = function(m) {
            //console.log('sending', m);
            comm.send(m);
        };
        // Register the callback with on_msg.
        comm.on_msg(function(msg) {
            //console.log('receiving', msg['content']['data'], msg);
            // Pass the mpl event to the overridden (by mpl) onmessage function.
            ws.onmessage(msg['content']['data'])
        });
        return ws;
    }
    
    mpl.mpl_figure_comm = function(comm, msg) {
        // This is the function which gets called when the mpl process
        // starts-up an IPython Comm through the "matplotlib" channel.
    
        var id = msg.content.data.id;
        // Get hold of the div created by the display call when the Comm
        // socket was opened in Python.
        var element = $("#" + id);
        var ws_proxy = comm_websocket_adapter(comm)
    
        function ondownload(figure, format) {
            window.open(figure.imageObj.src);
        }
    
        var fig = new mpl.figure(id, ws_proxy,
                               ondownload,
                               element.get(0));
    
        // Call onopen now - mpl needs it, as it is assuming we've passed it a real
        // web socket which is closed, not our websocket->open comm proxy.
        ws_proxy.onopen();
    
        fig.parent_element = element.get(0);
        fig.cell_info = mpl.find_output_cell("<div id='" + id + "'></div>");
        if (!fig.cell_info) {
            console.error("Failed to find cell for figure", id, fig);
            return;
        }
    
        var output_index = fig.cell_info[2]
        var cell = fig.cell_info[0];
    
    };
    
    mpl.figure.prototype.handle_close = function(fig, msg) {
        var width = fig.canvas.width/mpl.ratio
        fig.root.unbind('remove')
    
        // Update the output cell to use the data from the current canvas.
        fig.push_to_output();
        var dataURL = fig.canvas.toDataURL();
        // Re-enable the keyboard manager in IPython - without this line, in FF,
        // the notebook keyboard shortcuts fail.
        IPython.keyboard_manager.enable()
        $(fig.parent_element).html('<img src="' + dataURL + '" width="' + width + '">');
        fig.close_ws(fig, msg);
    }
    
    mpl.figure.prototype.close_ws = function(fig, msg){
        fig.send_message('closing', msg);
        // fig.ws.close()
    }
    
    mpl.figure.prototype.push_to_output = function(remove_interactive) {
        // Turn the data on the canvas into data in the output cell.
        var width = this.canvas.width/mpl.ratio
        var dataURL = this.canvas.toDataURL();
        this.cell_info[1]['text/html'] = '<img src="' + dataURL + '" width="' + width + '">';
    }
    
    mpl.figure.prototype.updated_canvas_event = function() {
        // Tell IPython that the notebook contents must change.
        IPython.notebook.set_dirty(true);
        this.send_message("ack", {});
        var fig = this;
        // Wait a second, then push the new image to the DOM so
        // that it is saved nicely (might be nice to debounce this).
        setTimeout(function () { fig.push_to_output() }, 1000);
    }
    
    mpl.figure.prototype._init_toolbar = function() {
        var fig = this;
    
        var nav_element = $('<div/>');
        nav_element.attr('style', 'width: 100%');
        this.root.append(nav_element);
    
        // Define a callback function for later on.
        function toolbar_event(event) {
            return fig.toolbar_button_onclick(event['data']);
        }
        function toolbar_mouse_event(event) {
            return fig.toolbar_button_onmouseover(event['data']);
        }
    
        for(var toolbar_ind in mpl.toolbar_items){
            var name = mpl.toolbar_items[toolbar_ind][0];
            var tooltip = mpl.toolbar_items[toolbar_ind][1];
            var image = mpl.toolbar_items[toolbar_ind][2];
            var method_name = mpl.toolbar_items[toolbar_ind][3];
    
            if (!name) { continue; };
    
            var button = $('<button class="btn btn-default" href="#" title="' + name + '"><i class="fa ' + image + ' fa-lg"></i></button>');
            button.click(method_name, toolbar_event);
            button.mouseover(tooltip, toolbar_mouse_event);
            nav_element.append(button);
        }
    
        // Add the status bar.
        var status_bar = $('<span class="mpl-message" style="text-align:right; float: right;"/>');
        nav_element.append(status_bar);
        this.message = status_bar[0];
    
        // Add the close button to the window.
        var buttongrp = $('<div class="btn-group inline pull-right"></div>');
        var button = $('<button class="btn btn-mini btn-primary" href="#" title="Stop Interaction"><i class="fa fa-power-off icon-remove icon-large"></i></button>');
        button.click(function (evt) { fig.handle_close(fig, {}); } );
        button.mouseover('Stop Interaction', toolbar_mouse_event);
        buttongrp.append(button);
        var titlebar = this.root.find($('.ui-dialog-titlebar'));
        titlebar.prepend(buttongrp);
    }
    
    mpl.figure.prototype._root_extra_style = function(el){
        var fig = this
        el.on("remove", function(){
    	fig.close_ws(fig, {});
        });
    }
    
    mpl.figure.prototype._canvas_extra_style = function(el){
        // this is important to make the div 'focusable
        el.attr('tabindex', 0)
        // reach out to IPython and tell the keyboard manager to turn it's self
        // off when our div gets focus
    
        // location in version 3
        if (IPython.notebook.keyboard_manager) {
            IPython.notebook.keyboard_manager.register_events(el);
        }
        else {
            // location in version 2
            IPython.keyboard_manager.register_events(el);
        }
    
    }
    
    mpl.figure.prototype._key_event_extra = function(event, name) {
        var manager = IPython.notebook.keyboard_manager;
        if (!manager)
            manager = IPython.keyboard_manager;
    
        // Check for shift+enter
        if (event.shiftKey && event.which == 13) {
            this.canvas_div.blur();
            event.shiftKey = false;
            // Send a "J" for go to next cell
            event.which = 74;
            event.keyCode = 74;
            manager.command_mode();
            manager.handle_keydown(event);
        }
    }
    
    mpl.figure.prototype.handle_save = function(fig, msg) {
        fig.ondownload(fig, null);
    }
    
    
    mpl.find_output_cell = function(html_output) {
        // Return the cell and output element which can be found *uniquely* in the notebook.
        // Note - this is a bit hacky, but it is done because the "notebook_saving.Notebook"
        // IPython event is triggered only after the cells have been serialised, which for
        // our purposes (turning an active figure into a static one), is too late.
        var cells = IPython.notebook.get_cells();
        var ncells = cells.length;
        for (var i=0; i<ncells; i++) {
            var cell = cells[i];
            if (cell.cell_type === 'code'){
                for (var j=0; j<cell.output_area.outputs.length; j++) {
                    var data = cell.output_area.outputs[j];
                    if (data.data) {
                        // IPython >= 3 moved mimebundle to data attribute of output
                        data = data.data;
                    }
                    if (data['text/html'] == html_output) {
                        return [cell, data, j];
                    }
                }
            }
        }
    }
    
    // Register the function which deals with the matplotlib target/channel.
    // The kernel may be null if the page has been refreshed.
    if (IPython.notebook.kernel != null) {
        IPython.notebook.kernel.comm_manager.register_target('matplotlib', mpl.mpl_figure_comm);
    }
    
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        <div id='03ca9397-2d80-447d-9de0-3ebc3b49a5d5'></div>
    </div>


Tests
-----


**In [17]:**

.. code:: ipython3

    max_snr = -100
    good_snr = 40
    if PLATFORM == "CWLITEARM":
        good_snr = 40
    elif PLATFORM == "CWNANO":
        good_snr = -5
    elif PLATFORM == "CWLITEXMEGA":
        good_snr = 25
    for point in snrdb:
        if point > max_snr:
            max_snr = point
    assert max_snr > good_snr, "Failed to get high SNR: Max = {}".format(max_snr)


**In [ ]:**

