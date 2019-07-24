
Manual CPA Attack
=================

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
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'TINYAES128C'
    num_traces = 100
    CHECK_CORR = False


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEARM.hex
    rm -f -- simpleserial-aes-CWLITEARM.eep
    rm -f -- simpleserial-aes-CWLITEARM.cof
    rm -f -- simpleserial-aes-CWLITEARM.elf
    rm -f -- simpleserial-aes-CWLITEARM.map
    rm -f -- simpleserial-aes-CWLITEARM.sym
    rm -f -- simpleserial-aes-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i aes.i aes-independant.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: simpleserial-aes-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DNO_EXTRA_OPTS -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEARM.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/aes.o objdir/aes-independant.o objdir/stm32f3_startup.o --output simpleserial-aes-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=simpleserial-aes-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEARM.elf simpleserial-aes-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEARM.sym
    arm-none-eabi-nm -n simpleserial-aes-CWLITEARM.elf > simpleserial-aes-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       5348	    532	   1484	   7364	   1cc4	simpleserial-aes-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



The CPA Attack Theory
---------------------

As a background on the CPA attack, please see the section `Correlation
Power Analysis <https://wiki.newae.com/Correlation_Power_Analysis>`__.
It's also suggested that you complete Tutorial B1, since that will
introduce Jupyter and show how to interface with ChipWhipserer using
Python. It's assumed you've read that section and completed the tutorial
and come back to this. Ok, you've done that? Good let's continue.

Assuming you **actually** read that, it should be apparent that there is
a few things we need to accomplish:

1. Getting some power traces of our target while it's performing AES
   encryption.
2. Reading the data, which consists of the analog waveform (trace) and
   input text sent to the encryption core
3. Making the power leakage model, where it takes a known input text
   along with a guess of the key byte
4. Implementing the correlation equation, and then looping through all
   the traces
5. Ranking the output of the correlation equation to determine the most
   likely key.

This tutorial will deal with both recording power traces using
ChipWhisperer and breaking them using a CPA attack.

Capturing Power Traces
----------------------

Capturing power traces will be very similar to previous tutorials,
except this time we'll be using a loop to capture multiple traces, as
well as numpy to store them. It's not necessary, but we'll also plot the
trace we get using ``bokeh``.

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

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5879 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5879 bytes
    


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
        key, text = ktp.next()  # manual creation of a key, text pair can be substituted here
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        traces.append(trace)
    
    #Convert traces to numpy arrays
    trace_array = np.asarray([trace.wave for trace in traces])  # if you prefer to work with numpy array for number crunching
    textin_array = np.asarray([trace.textin for trace in traces])
    known_keys = np.asarray([trace.key for trace in traces])  # for fixed key, these keys are all the same


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

    

    <div id="ceb8a5b3-be4f-4e20-93dd-abf80b268e4c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#ceb8a5b3-be4f-4e20-93dd-abf80b268e4c');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="91d2e579-ec92-4806-bc42-cf662c7337a6" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="a7527db2-5b71-479a-b477-24f514dd51e8"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a7527db2-5b71-479a-b477-24f514dd51e8');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c2a142af-9293-4469-bd5c-34ece671af3f":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1045","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"AAAAAAAAjj8AAAAAAIDJvwAAAAAAoMK/AAAAAADAub8AAAAAAACAvwAAAAAAINK/AAAAAACAub8AAAAAAICvvwAAAAAAAJI/AAAAAAAAwL8AAAAAAMCwvwAAAAAAgKa/AAAAAAAAoT8AAAAAAMDIvwAAAAAA4MO/AAAAAADAvL8AAAAAAACTvwAAAAAAAMK/AAAAAADAtL8AAAAAAACqvwAAAAAAAJw/AAAAAADAtr8AAAAAAICkvwAAAAAAAJW/AAAAAAAAqT8AAAAAAODMvwAAAAAAIMW/AAAAAACAvL8AAAAAAACOvwAAAAAAIM2/AAAAAAAgxL8AAAAAAEC5vwAAAAAAAHC/AAAAAAAAyr8AAAAAAIC/vwAAAAAAALe/AAAAAAAAUL8AAAAAAKDPvwAAAAAA4Ma/AAAAAABAvb8AAAAAAACAvwAAAAAAgMS/AAAAAAAApL8AAAAAAABovwAAAAAAALU/AAAAAACAx78AAAAAAIDAvwAAAAAAgLW/AAAAAAAAiD8AAAAAAJDRvwAAAAAAQMW/AAAAAABAur8AAAAAAAAAAAAAAAAA4NC/AAAAAADgxb8AAAAAAMC9vwAAAAAAAI6/AAAAAACgx78AAAAAAEC5vwAAAAAAgK6/AAAAAAAAoD8AAAAAAIDCvwAAAAAAAJm/AAAAAAAAjj8AAAAAAEC5PwAAAAAAAHg/AAAAAACAtD8AAAAAAIC3PwAAAAAAYMM/AAAAAADAsT8AAAAAAEC/PwAAAAAAAMA/AAAAAADAxT8AAAAAAMC2PwAAAAAA4MA/AAAAAADAwD8AAAAAAGDGPwAAAAAAQLc/AAAAAAAAwT8AAAAAAMDAPwAAAAAAIMY/AAAAAACAtT8AAAAAAIDAPwAAAAAAgL8/AAAAAADAxT8AAAAAAEC3PwAAAAAA4MA/AAAAAABAwD8AAAAAAKDFPwAAAAAAQLg/AAAAAAAgwT8AAAAAAIDAPwAAAAAAgMU/AAAAAABAsT8AAAAAAAC8PwAAAAAAgLs/AAAAAACgwz8AAAAAAACMPwAAAAAAwLE/AAAAAAAAtD8AAAAAAKDAPwAAAAAAgKm/AAAAAACApL8AAAAAAACbvwAAAAAAAKU/AAAAAACAvr8AAAAAAICtvwAAAAAAgKG/AAAAAAAApD8AAAAAAEDCvwAAAAAAQLS/AAAAAACArL8AAAAAAACZPwAAAAAAgM6/AAAAAAAgxr8AAAAAAAC9vwAAAAAAAIa/AAAAAAAgzr8AAAAAAMC4vwAAAAAAAKa/AAAAAACAqT8AAAAAAACUvwAAAAAAgKo/AAAAAADAsD8AAAAAAEC/PwAAAAAAwLK/AAAAAAAAqL8AAAAAAACavwAAAAAAgKY/AAAAAACAu78AAAAAAACEvwAAAAAAAJE/AAAAAADAtz8AAAAAAACKvwAAAAAAgK4/AAAAAABAsT8AAAAAAEDAPwAAAAAAAJa/AAAAAAAAqT8AAAAAAICuPwAAAAAAQL4/AAAAAAAAuT8AAAAAAEDBPwAAAAAA4MA/AAAAAADgxT8AAAAAAIC4PwAAAAAAIMA/AAAAAABAvj8AAAAAAEDEPwAAAAAAAKk/AAAAAAAAtz8AAAAAAAC4PwAAAAAA4ME/AAAAAADAtj8AAAAAACDAPwAAAAAAgL4/AAAAAAAgxD8AAAAAAEC9PwAAAAAAYMI/AAAAAACgwD8AAAAAAEDFPwAAAAAAAKo/AAAAAAAApj8AAAAAAICgPwAAAAAAwLM/AAAAAAAAnr8AAAAAAICgPwAAAAAAAKY/AAAAAABAuT8AAAAAAABoPwAAAAAAAIA/AAAAAAAAiD8AAAAAAACwPwAAAAAAgKy/AAAAAAAAjD8AAAAAAACZPwAAAAAAQLY/AAAAAAAAo78AAAAAAACfPwAAAAAAAKM/AAAAAABAuD8AAAAAAABoPwAAAAAAAK8/AAAAAABAsD8AAAAAAEC9PwAAAAAAAK8/AAAAAACAuT8AAAAAAEC4PwAAAAAAYME/AAAAAADAtz8AAAAAAIC/PwAAAAAAgLw/AAAAAACgwj8AAAAAAEC5PwAAAAAAgL8/AAAAAADAvD8AAAAAAMDCPwAAAAAAAK4/AAAAAACAtz8AAAAAAMC0PwAAAAAAQL8/AAAAAAAgw78AAAAAAEC+vwAAAAAAALm/AAAAAAAAjr8AAAAAAODQvwAAAAAAoMW/AAAAAABAv78AAAAAAACfvwAAAAAAwMu/AAAAAABAwr8AAAAAAAC8vwAAAAAAAJm/AAAAAAAA0r8AAAAAAODGvwAAAAAA4MC/AAAAAACAo78AAAAAAGDHvwAAAAAAALK/AAAAAAAAn78AAAAAAACpPwAAAAAAAHg/AAAAAACAsD8AAAAAAACxPwAAAAAAwL0/AAAAAACAsj8AAAAAAMC8PwAAAAAAwLk/AAAAAAAAwj8AAAAAAIC3PwAAAAAAgL8/AAAAAADAvD8AAAAAAODCPwAAAAAAgK0/AAAAAABAuD8AAAAAAIC1PwAAAAAAIMA/AAAAAABgwL8AAAAAAMC6vwAAAAAAQLW/AAAAAAAAeL8AAAAAAKDPvwAAAAAAgMS/AAAAAABAvb8AAAAAAACZvwAAAAAAwMq/AAAAAABgwb8AAAAAAMC6vwAAAAAAAJK/AAAAAADA0b8AAAAAAADGvwAAAAAAgMC/AAAAAAAAn78AAAAAAADGvwAAAAAAgK6/AAAAAAAAmL8AAAAAAACtPwAAAAAAAIY/AAAAAACAsT8AAAAAAECyPwAAAAAAAL8/AAAAAACAtD8AAAAAAAC+PwAAAAAAwLs/AAAAAACgwj8AAAAAAEC5PwAAAAAAQMA/AAAAAABAvj8AAAAAAEDDPwAAAAAAALA/AAAAAABAuT8AAAAAAEC2PwAAAAAAYMA/AAAAAADAvr8AAAAAAIC4vwAAAAAAQLS/AAAAAAAAYL8AAAAAAKDOvwAAAAAAQMO/AAAAAAAAvL8AAAAAAACUvwAAAAAAIMq/AAAAAAAgwb8AAAAAAAC6vwAAAAAAAJG/AAAAAACQ0b8AAAAAAEDGvwAAAAAAQMC/AAAAAAAAn78AAAAAACDGvwAAAAAAQLC/AAAAAAAAmL8AAAAAAICsPwAAAAAAAI4/AAAAAAAAsj8AAAAAAMCyPwAAAAAAgL8/AAAAAABAtD8AAAAAAMC9PwAAAAAAwLs/AAAAAAAAwz8AAAAAAAC5PwAAAAAAYMA/AAAAAADAvT8AAAAAAMDDPwAAAAAAAK8/AAAAAACAuD8AAAAAAIC2PwAAAAAAgMA/AAAAAABAur8AAAAAAIC1vwAAAAAAgK+/AAAAAAAAgD8AAAAAAADOvwAAAAAAAMS/AAAAAADAvL8AAAAAAACZvwAAAAAAIMO/AAAAAADAtr8AAAAAAICwvwAAAAAAAIo/AAAAAACAy78AAAAAAEDBvwAAAAAAwLm/AAAAAAAAjL8AAAAAAGDMvwAAAAAAYMK/AAAAAABAur8AAAAAAACKvwAAAAAAwMK/AAAAAACAtb8AAAAAAACsvwAAAAAAAJQ/AAAAAACAwL8AAAAAAAC0vwAAAAAAgKy/AAAAAAAAkz8AAAAAAAC1vwAAAAAAAGC/AAAAAAAAlT8AAAAAAIC2PwAAAAAAAGC/AAAAAAAAfD8AAAAAAACIPwAAAAAAALE/AAAAAACAr78AAAAAAACKPwAAAAAAAJw/AAAAAADAtz8AAAAAAICivwAAAAAAgKE/AAAAAACApj8AAAAAAIC6PwAAAAAAAIQ/AAAAAADAsD8AAAAAAICyPwAAAAAAAL8/AAAAAABAsj8AAAAAAEC8PwAAAAAAALs/AAAAAABAwj8AAAAAAMC5PwAAAAAAwMA/AAAAAABAvz8AAAAAAMDDPwAAAAAAQLs/AAAAAAAAwT8AAAAAAEC/PwAAAAAA4MM/AAAAAACAsD8AAAAAAMC5PwAAAAAAgLY/AAAAAADAwD8AAAAAAIDBvwAAAAAAgLu/AAAAAACAtr8AAAAAAACAvwAAAAAAENC/AAAAAADAxL8AAAAAAEC9vwAAAAAAAJi/AAAAAADAyr8AAAAAAKDBvwAAAAAAQLq/AAAAAAAAkb8AAAAAAKDRvwAAAAAAgMa/AAAAAABAwL8AAAAAAACgvwAAAAAAQMe/AAAAAABAsb8AAAAAAACdvwAAAAAAAKw/AAAAAAAAjD8AAAAAAMCyPwAAAAAAALM/AAAAAAAAwD8AAAAAAMC0PwAAAAAAAL4/AAAAAABAvD8AAAAAAADDPwAAAAAAQLk/AAAAAABgwD8AAAAAAMC+PwAAAAAAoMM/AAAAAABAsD8AAAAAAEC5PwAAAAAAALc/AAAAAADAwD8AAAAAAEDAvwAAAAAAALq/AAAAAADAtL8AAAAAAAB0vwAAAAAAwM+/AAAAAAAgxL8AAAAAAMC8vwAAAAAAAJa/AAAAAACAyr8AAAAAAADBvwAAAAAAQLq/AAAAAAAAjL8AAAAAAMDRvwAAAAAAIMa/AAAAAABgwL8AAAAAAACfvwAAAAAAoMa/AAAAAACAsL8AAAAAAACavwAAAAAAAKw/AAAAAAAAhD8AAAAAAMCxPwAAAAAAQLI/AAAAAABAvz8AAAAAAIC1PwAAAAAAQL4/AAAAAABAvD8AAAAAACDDPwAAAAAAgLk/AAAAAACgwD8AAAAAAIC+PwAAAAAAAMQ/AAAAAACArz8AAAAAAEC5PwAAAAAAwLY/AAAAAADAwD8AAAAAAMC/vwAAAAAAwLm/AAAAAACAtL8AAAAAAABQvwAAAAAAQM+/AAAAAADgw78AAAAAAMC7vwAAAAAAAJS/AAAAAABgyr8AAAAAACDBvwAAAAAAQLm/AAAAAAAAjr8AAAAAABDSvwAAAAAAoMa/AAAAAACAwL8AAAAAAACgvwAAAAAAYMe/AAAAAABAsb8AAAAAAACevwAAAAAAgKw/AAAAAAAAij8AAAAAAICyPwAAAAAAQLM/AAAAAAAgwD8AAAAAAIC1PwAAAAAAAL8/AAAAAABAvD8AAAAAAEDDPwAAAAAAwLg/AAAAAAAAwD8AAAAAAAC+PwAAAAAAoMM/AAAAAAAArz8AAAAAAIC4PwAAAAAAwLY/AAAAAADAwD8AAAAAAAC+vwAAAAAAQLi/AAAAAADAsr8AAAAAAABwPwAAAAAAUNC/AAAAAABgxb8AAAAAAIC+vwAAAAAAAJm/AAAAAAAAxL8AAAAAAAC3vwAAAAAAgLC/AAAAAAAAjD8AAAAAAMDLvwAAAAAAYMG/AAAAAADAub8AAAAAAACKvwAAAAAAYMy/AAAAAACAwr8AAAAAAEC5vwAAAAAAAIy/AAAAAADAwr8AAAAAAMC0vwAAAAAAgKq/AAAAAAAAlj8AAAAAAMDAvwAAAAAAwLO/AAAAAAAArL8AAAAAAACVPwAAAAAAALa/AAAAAAAAUL8AAAAAAACVPwAAAAAAQLc/AAAAAAAAUL8AAAAAAACEPwAAAAAAAIo/AAAAAADAsT8AAAAAAACvvwAAAAAAAJA/AAAAAAAAnT8AAAAAAAC4PwAAAAAAgKW/AAAAAAAAmz8AAAAAAACkPwAAAAAAALo/AAAAAAAAfD8AAAAAAICvPwAAAAAAALI/AAAAAAAAvz8AAAAAAACzPwAAAAAAwLw/AAAAAADAuj8AAAAAAODCPwAAAAAAgLo/AAAAAAAgwT8AAAAAAEC/PwAAAAAAIMQ/AAAAAABAuz8AAAAAAGDBPwAAAAAAwL8/AAAAAABgxD8AAAAAAACxPwAAAAAAgLo/AAAAAACAtz8AAAAAAADBPwAAAAAAIMG/AAAAAACAu78AAAAAAMC1vwAAAAAAAHy/AAAAAADAz78AAAAAAGDEvwAAAAAAgLy/AAAAAAAAl78AAAAAAEDLvwAAAAAAwMG/AAAAAADAur8AAAAAAACRvwAAAAAAENK/AAAAAABgxr8AAAAAAKDAvwAAAAAAgKC/AAAAAACAx78AAAAAAECxvwAAAAAAAJ6/AAAAAACArD8AAAAAAACIPwAAAAAAQLI/AAAAAABAsj8AAAAAAIC/PwAAAAAAALQ/AAAAAABAvT8AAAAAAMC7PwAAAAAAoMI/AAAAAADAuD8AAAAAACDAPwAAAAAAQL4/AAAAAACAwz8AAAAAAICvPwAAAAAAALk/AAAAAADAtj8AAAAAAKDAPwAAAAAAIMG/AAAAAABAu78AAAAAAIC1vwAAAAAAAHC/AAAAAABQ0L8AAAAAAGDEvwAAAAAAgLy/AAAAAAAAlL8AAAAAAODKvwAAAAAAYMG/AAAAAAAAur8AAAAAAACOvwAAAAAAoNG/AAAAAAAAxr8AAAAAAEC/vwAAAAAAAJ2/AAAAAADAxb8AAAAAAICuvwAAAAAAAJO/AAAAAAAArz8AAAAAAACQPwAAAAAAwLI/AAAAAACAsz8AAAAAAMC/PwAAAAAAwLU/AAAAAAAAvz8AAAAAAMC8PwAAAAAAYMM/AAAAAADAuT8AAAAAAADBPwAAAAAAwL4/AAAAAAAAxD8AAAAAAMCwPwAAAAAAALo/AAAAAABAtz8AAAAAACDBPwAAAAAAQMC/AAAAAADAur8AAAAAAIC0vwAAAAAAAFC/AAAAAABgz78AAAAAAADEvwAAAAAAALy/AAAAAAAAlL8AAAAAAEDKvwAAAAAAAMG/AAAAAAAAur8AAAAAAACMvwAAAAAAoNG/AAAAAAAAxr8AAAAAAIC/vwAAAAAAAJq/AAAAAADAxb8AAAAAAACsvwAAAAAAAJS/AAAAAACArz8AAAAAAACQPwAAAAAAALM/AAAAAADAsz8AAAAAACDAPwAAAAAAwLU/AAAAAADAvj8AAAAAAMC8PwAAAAAAYMM/AAAAAACAuT8AAAAAAKDAPwAAAAAAwL4/AAAAAADAwz8AAAAAAACvPwAAAAAAwLg/AAAAAACAtj8AAAAAAADBPwAAAAAAALy/AAAAAABAtr8AAAAAAECxvwAAAAAAAIA/AAAAAABAz78AAAAAACDEvwAAAAAAAL2/AAAAAAAAlb8AAAAAAADDvwAAAAAAgLa/AAAAAAAAsL8AAAAAAACKPwAAAAAA4Mu/AAAAAADgwb8AAAAAAIC5vwAAAAAAAIq/AAAAAABAzL8AAAAAAKDCvwAAAAAAQLq/AAAAAAAAir8AAAAAAIDCvwAAAAAAQLS/AAAAAACAq78AAAAAAACYPwAAAAAAwMC/AAAAAADAs78AAAAAAACsvwAAAAAAAJc/AAAAAADAtr8AAAAAAABQvwAAAAAAAJQ/AAAAAADAtj8AAAAAAABQvwAAAAAAAII/AAAAAAAAjD8AAAAAAECxPwAAAAAAAK6/AAAAAAAAkD8AAAAAAACfPwAAAAAAwLc/AAAAAACAor8AAAAAAIChPwAAAAAAAKc/AAAAAABAuj8AAAAAAACRPwAAAAAAQLI/AAAAAACAsz8AAAAAACDAPwAAAAAAALM/AAAAAABAvT8AAAAAAIC7PwAAAAAAAMM/AAAAAADAuz8AAAAAAODBPwAAAAAAAMA/AAAAAABgxD8AAAAAAMC8PwAAAAAA4ME/AAAAAAAgwD8AAAAAAEDEPwAAAAAAwLE/AAAAAACAuj8AAAAAAIC3PwAAAAAAAME/AAAAAAAAv78AAAAAAMC5vwAAAAAAQLS/AAAAAAAAYL8AAAAAACDOvwAAAAAAgMO/AAAAAADAu78AAAAAAACTvwAAAAAAYMq/AAAAAADgwL8AAAAAAEC6vwAAAAAAAIi/AAAAAADw0b8AAAAAAEDGvwAAAAAAYMC/AAAAAAAAnr8AAAAAAKDHvwAAAAAAQLG/AAAAAAAAm78AAAAAAICrPwAAAAAAAIw/AAAAAAAAsj8AAAAAAECzPwAAAAAAwL8/AAAAAABAtT8AAAAAAAC/PwAAAAAAQL0/AAAAAAAgwz8AAAAAAEC4PwAAAAAAYMA/AAAAAABAvj8AAAAAAKDDPwAAAAAAgK4/AAAAAABAuT8AAAAAAEC2PwAAAAAAgMA/AAAAAABgwb8AAAAAAIC6vwAAAAAAQLW/AAAAAAAAUL8AAAAAAADQvwAAAAAAAMS/AAAAAACAvL8AAAAAAACXvwAAAAAAoMq/AAAAAABgwb8AAAAAAIC5vwAAAAAAAIy/AAAAAACg0b8AAAAAAEDGvwAAAAAAAMC/AAAAAAAAnr8AAAAAAGDGvwAAAAAAALC/AAAAAAAAmL8AAAAAAACvPwAAAAAAAIw/AAAAAABAsj8AAAAAAACzPwAAAAAAAMA/AAAAAACAtT8AAAAAAEC/PwAAAAAAgLw/AAAAAABgwz8AAAAAAMC5PwAAAAAAoMA/AAAAAABAvz8AAAAAAADEPwAAAAAAALA/AAAAAABAuT8AAAAAAAC3PwAAAAAA4MA/AAAAAABAwL8AAAAAAEC6vwAAAAAAQLS/AAAAAAAAYL8AAAAAAKDPvwAAAAAAIMS/AAAAAACAvL8AAAAAAACTvwAAAAAA4Mq/AAAAAAAAwb8AAAAAAAC6vwAAAAAAAIi/AAAAAACw0b8AAAAAAADGvwAAAAAAQMC/AAAAAAAAnb8AAAAAAMDGvwAAAAAAQLC/AAAAAAAAl78AAAAAAACuPwAAAAAAAI4/AAAAAABAsj8AAAAAAMCzPwAAAAAAYMA/AAAAAAAAtj8AAAAAAMC+PwAAAAAAgL0/AAAAAACgwz8AAAAAAIC5PwAAAAAAgMA/AAAAAABAvj8AAAAAACDEPwAAAAAAAK0/AAAAAADAuD8AAAAAAIC2PwAAAAAAAME/AAAAAACAvL8AAAAAAIC2vwAAAAAAgLG/AAAAAAAAgD8AAAAAAADPvwAAAAAAYMS/AAAAAADAvL8AAAAAAACXvwAAAAAAYMO/AAAAAACAt78AAAAAAACwvwAAAAAAAIo/AAAAAABAxL8AAAAAAAC5vwAAAAAAgLG/AAAAAAAAdD8AAAAAACDJvwAAAAAAIMC/AAAAAABAtr8AAAAAAABgvwAAAAAAQM6/AAAAAABgw78AAAAAAIC8vwAAAAAAAJG/AAAAAACQ0L8AAAAAAIDEvwAAAAAAALy/AAAAAAAAir8AAAAAAADBvwAAAAAAAKC/AAAAAAAAUD8AAAAAAICzPwAAAAAAAJ8/AAAAAACAtT8AAAAAAAC3PwAAAAAAwME/AAAAAACAqz8AAAAAAMC4PwAAAAAAgLk/AAAAAAAgwj8AAAAAAEC7PwAAAAAAAMI/AAAAAADgwD8AAAAAAIDFPwAAAAAAgLE/AAAAAACArD8AAAAAAACpPwAAAAAAQLc/AAAAAACApL8AAAAAAACavwAAAAAAAJa/AAAAAAAAoj8AAAAAAGDAvwAAAAAAQLO/AAAAAACAq78AAAAAAACRPwAAAAAA4Mi/AAAAAABAwr8AAAAAAMC4vwAAAAAAAIK/AAAAAACAyL8AAAAAAIC+vwAAAAAAgLS/AAAAAAAAAAAAAAAAAGDBvwAAAAAAALO/AAAAAACArL8AAAAAAACXPwAAAAAA0NO/AAAAAABAzL8AAAAAAADEvwAAAAAAAKW/AAAAAADw2b8AAAAAAADTvwAAAAAAIMq/AAAAAAAAsr8AAAAAAKDCvwAAAAAAAKG/AAAAAAAAfD8AAAAAAEC1PwAAAAAAAMG/AAAAAADAtb8AAAAAAACovwAAAAAAgKA/AAAAAADAxr8AAAAAAICuvwAAAAAAAJK/AAAAAACAsT8AAAAAAACGPwAAAAAAwLM/AAAAAAAAtT8AAAAAAKDBPwAAAAAAgKA/AAAAAACAtj8AAAAAAIC3PwAAAAAAQMI/AAAAAAAAhD8AAAAAAICyPwAAAAAAQLQ/AAAAAAAAwT8AAAAAAIC2PwAAAAAAAMA/AAAAAACAvz8AAAAAAKDEPwAAAAAA4ME/AAAAAABAxT8AAAAAAODDPwAAAAAAoMc/AAAAAAAAqj8AAAAAAACnPwAAAAAAgKI/AAAAAADAtD8AAAAAAMC3vwAAAAAAAHy/AAAAAAAAkj8AAAAAAEC2PwAAAAAAAIA/AAAAAACAsD8AAAAAAICxPwAAAAAAgL4/AAAAAADAwb8AAAAAAAC7vwAAAAAAwLS/AAAAAAAAUL8AAAAAAODFvwAAAAAAwLi/AAAAAADAsb8AAAAAAACGPwAAAAAA4MW/AAAAAACArb8AAAAAAACTvwAAAAAAALA/AAAAAAAAiD8AAAAAAICxPwAAAAAAALM/AAAAAABAwD8AAAAAAIC6vwAAAAAAQLO/AAAAAACAqb8AAAAAAACbPwAAAAAAgL2/AAAAAAAArb8AAAAAAIChvwAAAAAAAKM/AAAAAABAs78AAAAAAIChvwAAAAAAAJW/AAAAAACApT8AAAAAAICzvwAAAAAAAHQ/AAAAAAAAkz8AAAAAAEC2PwAAAAAAQLu/AAAAAABAsr8AAAAAAIClvwAAAAAAAJ0/AAAAAADAwL8AAAAAAAChvwAAAAAAAHC/AAAAAABAsj8AAAAAAEC7vwAAAAAAAIq/AAAAAAAAgD8AAAAAAMC0PwAAAAAAAIw/AAAAAACAsj8AAAAAAMCzPwAAAAAAYMA/AAAAAAAAhD8AAAAAAICxPwAAAAAAgLI/AAAAAADAvz8AAAAAAECwPwAAAAAAQLs/AAAAAAAAuj8AAAAAAGDCPwAAAAAAwLO/AAAAAAAAr78AAAAAAICkvwAAAAAAAJ0/AAAAAABAu78AAAAAAICsvwAAAAAAAKK/AAAAAACAoT8AAAAAAMCzvwAAAAAAgKS/AAAAAAAAm78AAAAAAACkPwAAAAAAALa/AAAAAAAAUL8AAAAAAACMPwAAAAAAQLU/AAAAAADAvr8AAAAAAIC0vwAAAAAAAKq/AAAAAAAAlz8AAAAAACDBvwAAAAAAAKO/AAAAAAAAgL8AAAAAAMCwPwAAAAAAgLq/AAAAAAAAir8AAAAAAACCPwAAAAAAwLM/AAAAAAAAnT8AAAAAAAC1PwAAAAAAQLU/AAAAAACgwD8AAAAAAACZPwAAAAAAgLM/AAAAAACAsz8AAAAAACDAPwAAAAAAwLA/AAAAAACAuz8AAAAAAIC5PwAAAAAAQMI/AAAAAACAtL8AAAAAAACvvwAAAAAAgKe/AAAAAAAAmz8AAAAAAIC8vwAAAAAAAK+/AAAAAACApL8AAAAAAACcPwAAAAAAgLS/AAAAAAAAqL8AAAAAAACevwAAAAAAgKE/AAAAAABAtb8AAAAAAAB0vwAAAAAAAIg/AAAAAABAtD8AAAAAAIC9vwAAAAAAgLS/AAAAAAAAq78AAAAAAACYPwAAAAAAAMG/AAAAAACAob8AAAAAAACEvwAAAAAAQLE/AAAAAAAAur8AAAAAAACGvwAAAAAAAII/AAAAAAAAtD8AAAAAAACZPwAAAAAAwLM/AAAAAABAtD8AAAAAACDAPwAAAAAAAI4/AAAAAACAsT8AAAAAAECyPwAAAAAAgL4/AAAAAACArD8AAAAAAIC4PwAAAAAAwLc/AAAAAABAwT8AAAAAAIChvwAAAAAAAJu/AAAAAAAAk78AAAAAAICkPwAAAAAAYMC/AAAAAADAsr8AAAAAAACsvwAAAAAAAJI/AAAAAABAy78AAAAAAADDvwAAAAAAgLu/AAAAAAAAkb8AAAAAAFDUvwAAAAAAYM2/AAAAAABgxL8AAAAAAACpvwAAAAAAYMa/AAAAAACAur8AAAAAAICxvwAAAAAAAIw/AAAAAAAgwL8AAAAAAEC0vwAAAAAAgKu/AAAAAAAAlj8AAAAAAICnvwAAAAAAAJw/AAAAAACApD8AAAAAAMC6PwAAAAAAgKW/AAAAAAAAmb8AAAAAAACRvwAAAAAAAKk/AAAAAABgwL8AAAAAAACgvwAAAAAAAFA/AAAAAABAsz8AAAAAAAB4vwAAAAAAgKw/AAAAAACArz8AAAAAAMC9PwAAAAAAgKi/AAAAAAAAlz8AAAAAAIChPwAAAAAAgLg/AAAAAAAAnz8AAAAAAIC0PwAAAAAAQLU/AAAAAACgwD8AAAAAAACTPwAAAAAAgLI/AAAAAAAAsz8AAAAAAMC/PwAAAAAAQLA/AAAAAADAuj8AAAAAAEC5PwAAAAAAAMI/AAAAAACAtL8AAAAAAECwvwAAAAAAgKe/AAAAAAAAmD8AAAAAAIC8vwAAAAAAALC/AAAAAAAApb8AAAAAAACbPwAAAAAAALW/AAAAAACAqL8AAAAAAACfvwAAAAAAgKA/AAAAAAAAtr8AAAAAAAB8vwAAAAAAAII/AAAAAABAtD8AAAAAACDAvwAAAAAAALa/AAAAAAAArb8AAAAAAACTPwAAAAAAYMK/AAAAAACApL8AAAAAAACOvwAAAAAAgLA/AAAAAAAAu78AAAAAAACOvwAAAAAAAHw/AAAAAABAsz8AAAAAAACKPwAAAAAAgLE/AAAAAACAsj8AAAAAAEC/PwAAAAAAAIQ/AAAAAAAAsD8AAAAAAMCxPwAAAAAAAL4/AAAAAACArT8AAAAAAEC5PwAAAAAAALg/AAAAAABgwT8AAAAAAIC2vwAAAAAAwLC/AAAAAAAAqr8AAAAAAACVPwAAAAAAgL6/AAAAAAAAsL8AAAAAAICnvwAAAAAAAJs/AAAAAADAtb8AAAAAAACpvwAAAAAAgKG/AAAAAAAAnj8AAAAAAEC4vwAAAAAAAIa/AAAAAAAAdD8AAAAAAMCyPwAAAAAAAMC/AAAAAABAt78AAAAAAACuvwAAAAAAAJA/AAAAAABAwr8AAAAAAACnvwAAAAAAAJC/AAAAAACArj8AAAAAAIC6vwAAAAAAAIy/AAAAAAAAeD8AAAAAAAC0PwAAAAAAAJA/AAAAAACAsj8AAAAAAICyPwAAAAAAwL8/AAAAAAAAhj8AAAAAAICwPwAAAAAAgLE/AAAAAAAAvj8AAAAAAACtPwAAAAAAALk/AAAAAABAuD8AAAAAACDBPwAAAAAAwLa/AAAAAADAsb8AAAAAAACpvwAAAAAAAJM/AAAAAACAvr8AAAAAAMCwvwAAAAAAgKi/AAAAAAAAlj8AAAAAAEC3vwAAAAAAAKu/AAAAAACAo78AAAAAAACcPwAAAAAAQLi/AAAAAAAAhr8AAAAAAABoPwAAAAAAQLI/AAAAAADAv78AAAAAAEC2vwAAAAAAAK+/AAAAAAAAjD8AAAAAACDCvwAAAAAAgKe/AAAAAAAAkb8AAAAAAICuPwAAAAAAwLy/AAAAAAAAmb8AAAAAAABQvwAAAAAAQLI/AAAAAAAAgD8AAAAAAACwPwAAAAAAQLE/AAAAAABAvj8AAAAAAABgPwAAAAAAgK0/AAAAAACArz8AAAAAAIC9PwAAAAAAgKg/AAAAAABAtz8AAAAAAEC2PwAAAAAAwMA/AAAAAACApL8AAAAAAACfvwAAAAAAAJW/AAAAAAAAoz8AAAAAAKDAvwAAAAAAgLS/AAAAAAAArr8AAAAAAACKPwAAAAAAYMu/AAAAAACgw78AAAAAAIC8vwAAAAAAAJa/AAAAAABw1L8AAAAAAKDNvwAAAAAAgMS/AAAAAAAAqr8AAAAAAGDHvwAAAAAAwLq/AAAAAABAsr8AAAAAAACCPwAAAAAAIMG/AAAAAACAtL8AAAAAAACuvwAAAAAAAJI/AAAAAAAAqb8AAAAAAACZPwAAAAAAgKQ/AAAAAACAuT8AAAAAAICmvwAAAAAAAJ2/AAAAAAAAlr8AAAAAAICmPwAAAAAAQMC/AAAAAACAob8AAAAAAABQvwAAAAAAwLI/AAAAAAAAeL8AAAAAAICqPwAAAAAAAK4/AAAAAAAAvT8AAAAAAACovwAAAAAAAJg/AAAAAAAAoT8AAAAAAAC5PwAAAAAAAKM/AAAAAACAtj8AAAAAAAC2PwAAAAAAIME/AAAAAAAAnD8AAAAAAICzPwAAAAAAwLM/AAAAAADAvz8AAAAAAMCwPwAAAAAAgLo/AAAAAABAuT8AAAAAAMDBPwAAAAAAgLS/AAAAAAAAsb8AAAAAAICnvwAAAAAAAJU/AAAAAACAvb8AAAAAAECwvwAAAAAAgKa/AAAAAAAAmT8AAAAAAAC3vwAAAAAAgKu/AAAAAAAApL8AAAAAAACbPwAAAAAAwLe/AAAAAAAAfL8AAAAAAAB0PwAAAAAAwLI/AAAAAAAAwL8AAAAAAAC3vwAAAAAAAK+/AAAAAAAAjD8AAAAAAADCvwAAAAAAAKe/AAAAAAAAkb8AAAAAAICtPwAAAAAAQL2/AAAAAAAAmr8AAAAAAAAAAAAAAAAAQLI/AAAAAAAAjj8AAAAAAMCxPwAAAAAAALI/AAAAAADAvj8AAAAAAACAPwAAAAAAQLA/AAAAAADAsD8AAAAAAEC+PwAAAAAAgKw/AAAAAAAAuT8AAAAAAMC3PwAAAAAAIME/AAAAAADAtb8AAAAAAICxvwAAAAAAgKm/AAAAAAAAkj8AAAAAAEC+vwAAAAAAQLG/AAAAAAAAp78AAAAAAACXPwAAAAAAALa/AAAAAACAqr8AAAAAAACivwAAAAAAAJs/AAAAAAAAub8AAAAAAACIvwAAAAAAAFA/AAAAAABAsj8AAAAAAADBvwAAAAAAgLi/AAAAAAAAsb8AAAAAAACMPwAAAAAAQMK/AAAAAAAApb8AAAAAAACRvwAAAAAAAK8/AAAAAACAur8AAAAAAACRvwAAAAAAAGA/AAAAAACAsj8AAAAAAAAAAAAAAAAAgKw/AAAAAAAAsD8AAAAAAEC9PwAAAAAAAHi/AAAAAACAqj8AAAAAAACuPwAAAAAAQLw/AAAAAACAqj8AAAAAAAC4PwAAAAAAgLY/AAAAAADAwD8AAAAAAEC4vwAAAAAAgLK/AAAAAAAArL8AAAAAAACTPwAAAAAAAL+/AAAAAAAAsb8AAAAAAICovwAAAAAAAJc/AAAAAAAAt78AAAAAAICrvwAAAAAAgKK/AAAAAAAAmz8AAAAAAAC4vwAAAAAAAIq/AAAAAAAAdD8AAAAAAECyPwAAAAAAQMC/AAAAAACAt78AAAAAAICvvwAAAAAAAIo/AAAAAAAgw78AAAAAAICovwAAAAAAAJa/AAAAAACAqz8AAAAAAMC9vwAAAAAAAJe/AAAAAAAAcL8AAAAAAMCxPwAAAAAAAIg/AAAAAACAsT8AAAAAAICxPwAAAAAAgL4/AAAAAAAAfD8AAAAAAECwPwAAAAAAQLA/AAAAAACAvT8AAAAAAACqPwAAAAAAQLc/AAAAAACAtj8AAAAAAKDAPwAAAAAAgKK/AAAAAACAoL8AAAAAAACXvwAAAAAAgKQ/AAAAAADgwL8AAAAAAIC0vwAAAAAAgK6/AAAAAAAAjD8AAAAAACDMvwAAAAAAwMO/AAAAAACAvL8AAAAAAACTvwAAAAAA8NS/AAAAAACgzb8AAAAAAMDEvwAAAAAAAKq/AAAAAACgx78AAAAAAMC6vwAAAAAAALK/AAAAAAAAgD8AAAAAACDBvwAAAAAAQLW/AAAAAACArL8AAAAAAACRPwAAAAAAAKm/AAAAAAAAmT8AAAAAAAClPwAAAAAAgLk/AAAAAACAqL8AAAAAAACcvwAAAAAAAJa/AAAAAAAApz8AAAAAAODAvwAAAAAAAKK/AAAAAAAAaL8AAAAAAMCyPwAAAAAAAJO/AAAAAAAAqT8AAAAAAICrPwAAAAAAgLw/AAAAAACArr8AAAAAAACOPwAAAAAAAJw/AAAAAAAAtz8AAAAAAAChPwAAAAAAALU/AAAAAABAtT8AAAAAAIDAPwAAAAAAAJ0/AAAAAACAsz8AAAAAAAC0PwAAAAAAIMA/AAAAAAAAsD8AAAAAAAC7PwAAAAAAALk/AAAAAAAAwj8AAAAAAIC1vwAAAAAAQLC/AAAAAAAAqb8AAAAAAACVPwAAAAAAwL2/AAAAAABAsL8AAAAAAICmvwAAAAAAAJo/AAAAAADAtb8AAAAAAACpvwAAAAAAgKG/AAAAAAAAnT8AAAAAAMC2vwAAAAAAAIK/AAAAAAAAgD8AAAAAAMCyPwAAAAAAQL+/AAAAAADAtb8AAAAAAACtvwAAAAAAAJE/AAAAAADAw78AAAAAAICqvwAAAAAAAJi/AAAAAAAArT8AAAAAAEC+vwAAAAAAAJW/AAAAAAAAaL8AAAAAAACyPwAAAAAAAJU/AAAAAACAsz8AAAAAAICzPwAAAAAAwL8/AAAAAAAAkj8AAAAAAACyPwAAAAAAgLI/AAAAAACAvj8AAAAAAACvPwAAAAAAQLk/AAAAAACAuD8AAAAAAGDBPwAAAAAAgLW/AAAAAABAsb8AAAAAAACpvwAAAAAAAJU/AAAAAACAvr8AAAAAAACxvwAAAAAAAKi/AAAAAAAAmT8AAAAAAMC2vwAAAAAAAKq/AAAAAACAob8AAAAAAACdPwAAAAAAwLi/AAAAAAAAir8AAAAAAABQPwAAAAAAQLI/AAAAAABgwL8AAAAAAIC3vwAAAAAAAK+/AAAAAAAAij8AAAAAAIDCvwAAAAAAAKi/AAAAAAAAk78AAAAAAACuPwAAAAAAAL6/AAAAAAAAmb8AAAAAAABwvwAAAAAAQLE/AAAAAAAAAAAAAAAAAICtPwAAAAAAALA/AAAAAACAvT8AAAAAAABwvwAAAAAAAKs/AAAAAACArD8AAAAAAEC8PwAAAAAAgKk/AAAAAAAAuD8AAAAAAIC2PwAAAAAA4MA/AAAAAAAAt78AAAAAAMCyvwAAAAAAgKu/AAAAAAAAkD8AAAAAAIC+vwAAAAAAwLG/AAAAAACAqL8AAAAAAACWPwAAAAAAwLa/AAAAAACArL8AAAAAAACjvwAAAAAAAJo/AAAAAACAuL8AAAAAAACIvwAAAAAAAGA/AAAAAABAsj8AAAAAACDAvwAAAAAAgLa/AAAAAACAr78AAAAAAACRPwAAAAAAYMO/AAAAAAAAqb8AAAAAAACXvwAAAAAAgKw/AAAAAACAvb8AAAAAAACZvwAAAAAAAGC/AAAAAACAsT8AAAAAAABwPwAAAAAAAK8/AAAAAADAsD8AAAAAAIC9PwAAAAAAAAAAAAAAAACAqz8AAAAAAACvPwAAAAAAgLw/AAAAAACApz8AAAAAAMC2PwAAAAAAALY/AAAAAACgwD8AAAAAAAClvwAAAAAAAJ+/AAAAAAAAmL8AAAAAAICjPwAAAAAAAMG/AAAAAADAtL8AAAAAAICvvwAAAAAAAIY/AAAAAACgy78AAAAAAODDvwAAAAAAgLy/AAAAAAAAlb8AAAAAAIDPvwAAAAAAYMe/AAAAAADAv78AAAAAAACevwAAAAAA8NG/AAAAAADAyL8AAAAAAIDBvwAAAAAAgKC/AAAAAACw0b8AAAAAAGDGvwAAAAAAwL+/AAAAAAAAnL8AAAAAAEDEvwAAAAAAAKm/AAAAAAAAjL8AAAAAAICxPwAAAAAAgKY/AAAAAACAuD8AAAAAAIC5PwAAAAAAgMI/AAAAAAAAoz8AAAAAAACUPwAAAAAAAJA/AAAAAAAArz8AAAAAAEC1vwAAAAAAgKe/AAAAAAAAn78AAAAAAACiPwAAAAAAIMO/AAAAAABAt78AAAAAAICvvwAAAAAAAI4/AAAAAADAwb8AAAAAAIC0vwAAAAAAgK2/AAAAAAAAkz8AAAAAAEDGvwAAAAAAgLu/AAAAAACAtb8AAAAAAABgPwAAAAAAgNG/AAAAAADAyb8AAAAAAODAvwAAAAAAAJy/AAAAAABgx78AAAAAAECwvwAAAAAAAJi/AAAAAAAArz8AAAAAAMDFvwAAAAAAwL2/AAAAAADAsr8AAAAAAACCPwAAAAAAwLq/AAAAAAAAhL8AAAAAAACOPwAAAAAAALc/AAAAAAAAeL8AAAAAAACwPwAAAAAAQLE/AAAAAAAAwD8AAAAAAICjPwAAAAAAQLc/AAAAAACAtj8AAAAAAKDBPwAAAAAAwLM/AAAAAABAvj8AAAAAAAC8PwAAAAAAYMM/AAAAAAAAqT8AAAAAAAC4PwAAAAAAALc/AAAAAABAwT8AAAAAAACSPwAAAAAAwLI/AAAAAACAsj8AAAAAAEC/PwAAAAAAwLS/AAAAAACArr8AAAAAAACpvwAAAAAAAJU/AAAAAACAz78AAAAAAIDEvwAAAAAAwL6/AAAAAAAAmb8AAAAAAIDPvwAAAAAAIMO/AAAAAACAvb8AAAAAAACTvwAAAAAAQM2/AAAAAACAt78AAAAAAACpvwAAAAAAAKc/AAAAAACAr78AAAAAAACWPwAAAAAAgKI/AAAAAACAuT8AAAAAAICjvwAAAAAAgKE/AAAAAACApj8AAAAAAAC7PwAAAAAAAJA/AAAAAAAAsz8AAAAAAMCyPwAAAAAAAMA/AAAAAAAAeL8AAAAAAACuPwAAAAAAgK8/AAAAAABAvj8AAAAAAEC0vwAAAAAAAK2/AAAAAACAqL8AAAAAAACZPwAAAAAAAMy/AAAAAACAwr8AAAAAAMC7vwAAAAAAAJO/AAAAAAAAzL8AAAAAAGDAvwAAAAAAQLe/AAAAAAAAcL8AAAAAAKDJvwAAAAAAALO/AAAAAAAAob8AAAAAAACsPwAAAAAAAKa/AAAAAACAoj8AAAAAAICnPwAAAAAAALw/AAAAAAAAkL8AAAAAAACrPwAAAAAAgK0/AAAAAADAvT8AAAAAAACMPwAAAAAAALM/AAAAAACAsj8AAAAAACDAPwAAAAAAAIS/AAAAAAAAqz8AAAAAAICtPwAAAAAAAL0/AAAAAADAt78AAAAAAECwvwAAAAAAAKi/AAAAAAAAmj8AAAAAAJDQvwAAAAAAoMW/AAAAAAAAv78AAAAAAACYvwAAAAAAcNS/AAAAAADAyb8AAAAAAODBvwAAAAAAgKC/AAAAAADw0L8AAAAAAADEvwAAAAAAQLq/AAAAAAAAcL8AAAAAAGDJvwAAAAAAwLy/AAAAAADAs78AAAAAAACEPwAAAAAAgM+/AAAAAADgxr8AAAAAAAC8vwAAAAAAAIC/AAAAAABAxL8AAAAAAIClvwAAAAAAAGC/AAAAAAAAtD8AAAAAAECwvwAAAAAAAJU/AAAAAAAAqT8AAAAAAIC9PwAAAAAAAK8/AAAAAACAvD8AAAAAAEC8PwAAAAAAAMQ/AAAAAACAtj8AAAAAAMDAPwAAAAAAgL4/AAAAAAAAxT8AAAAAAICuPwAAAAAAQLs/AAAAAABAuT8AAAAAAADDPwAAAAAAgKO/AAAAAAAAmb8AAAAAAACRvwAAAAAAAKk/AAAAAADAwr8AAAAAAIC2vwAAAAAAgLG/AAAAAAAAhj8AAAAAAIC/vwAAAAAAAJq/AAAAAAAAiD8AAAAAAMC1PwAAAAAAgKE/AAAAAACAtj8AAAAAAEC3PwAAAAAAIMI/AAAAAABAuj8AAAAAAKDBPwAAAAAAYMA/AAAAAACAxT8AAAAAACDBPwAAAAAAwMQ/AAAAAAAAwz8AAAAAAEDHPwAAAAAAgKE/AAAAAAAAnT8AAAAAAACZPwAAAAAAQLM/AAAAAACAv78AAAAAAICzvwAAAAAAgKu/AAAAAAAAlT8AAAAAAIC+vwAAAAAAQLC/AAAAAAAApb8AAAAAAACfPwAAAAAAQLi/AAAAAAAAqL8AAAAAAAChvwAAAAAAgKE/AAAAAAAAqb8AAAAAAACdPwAAAAAAgKQ/AAAAAACAuj8AAAAAAACWvwAAAAAAAIK/AAAAAAAAgL8AAAAAAICqPwAAAAAAAJW/AAAAAAAApj8AAAAAAICrPwAAAAAAQLw/AAAAAAAAtD8AAAAAAMC9PwAAAAAAALw/AAAAAAAAwz8AAAAAAACdPwAAAAAAAI4/AAAAAAAAeD8AAAAAAACsPwAAAAAAwLq/AAAAAACAsb8AAAAAAACsvwAAAAAAAJI/AAAAAABAvb8AAAAAAACwvwAAAAAAAKS/AAAAAACAoD8AAAAAAEC0vwAAAAAAgKO/AAAAAAAAmr8AAAAAAICjPwAAAAAAQLm/AAAAAAAArb8AAAAAAAClvwAAAAAAAJs/AAAAAADAsb8AAAAAAACEPwAAAAAAAJo/AAAAAADAtj8AAAAAAECwvwAAAAAAgKe/AAAAAACAor8AAAAAAACgPwAAAAAAoMS/AAAAAACAq78AAAAAAACZvwAAAAAAgK4/AAAAAAAgzr8AAAAAAEDFvwAAAAAAgL6/AAAAAAAAk78AAAAAAIDEvwAAAAAAgLa/AAAAAACAr78AAAAAAACVPwAAAAAAQLm/AAAAAACAqr8AAAAAAICgvwAAAAAAAKM/AAAAAAAApb8AAAAAAACgPwAAAAAAgKc/AAAAAABAuz8AAAAAAACQvwAAAAAAAHi/AAAAAAAAYL8AAAAAAICtPwAAAAAAAJK/AAAAAAAAqT8AAAAAAICtPwAAAAAAgL0/AAAAAACAtD8AAAAAAIC/PwAAAAAAgL0/AAAAAAAAxD8AAAAAAACdPwAAAAAAAJI/AAAAAAAAgj8AAAAAAACtPwAAAAAAQLi/AAAAAACArr8AAAAAAACmvwAAAAAAAJk/AAAAAACAu78AAAAAAACwvwAAAAAAAKS/AAAAAAAAnT8AAAAAAEC4vwAAAAAAgKi/AAAAAAAAob8AAAAAAIChPwAAAAAAgLm/AAAAAACArL8AAAAAAICmvwAAAAAAAJs/AAAAAADAt78AAAAAAABovwAAAAAAAIo/AAAAAACAtT8AAAAAACDBvwAAAAAAQLe/AAAAAACAsL8AAAAAAACOPwAAAAAA8NK/AAAAAACgyL8AAAAAAMDAvwAAAAAAAJ6/AAAAAABAxL8AAAAAAIC2vwAAAAAAgKy/AAAAAAAAlz8AAAAAAAC4vwAAAAAAAKi/AAAAAAAAnb8AAAAAAIClPwAAAAAAAKa/AAAAAAAAoT8AAAAAAICnPwAAAAAAQLw/AAAAAAAAjL8AAAAAAAAAAAAAAAAAAGg/AAAAAABAsD8AAAAAAACMvwAAAAAAAKs/AAAAAACArz8AAAAAAIC+PwAAAAAAwLU/AAAAAAAgwD8AAAAAAIC+PwAAAAAAQMQ/AAAAAACAoD8AAAAAAACXPwAAAAAAAI4/AAAAAAAArz8AAAAAAAC2vwAAAAAAgKi/AAAAAACAob8AAAAAAAChPwAAAAAAALq/AAAAAACArL8AAAAAAICkvwAAAAAAAJ8/AAAAAACAtr8AAAAAAICkvwAAAAAAAJy/AAAAAACApT8AAAAAAACwvwAAAAAAAJA/AAAAAAAAnz8AAAAAAMC4PwAAAAAAgK2/AAAAAACAo78AAAAAAACfvwAAAAAAgKI/AAAAAACgxL8AAAAAAICrvwAAAAAAAJS/AAAAAAAArz8AAAAAAODLvwAAAAAAIMS/AAAAAABAu78AAAAAAACEvwAAAAAA4MK/AAAAAABAtL8AAAAAAICpvwAAAAAAAJ0/AAAAAAAAuL8AAAAAAICmvwAAAAAAAJy/AAAAAAAApj8AAAAAAIChvwAAAAAAgKM/AAAAAACAqT8AAAAAAIC8PwAAAAAAAIC/AAAAAAAAAAAAAAAAAAB4PwAAAAAAAK8/AAAAAAAAhr8AAAAAAACrPwAAAAAAQLA/AAAAAAAAvj8AAAAAAMC2PwAAAAAAQMA/AAAAAADAvj8AAAAAAGDEPwAAAAAAgKI/AAAAAAAAlj8AAAAAAACIPwAAAAAAgK8/AAAAAADAtr8AAAAAAACovwAAAAAAAKK/AAAAAACAoj8AAAAAAGDAvwAAAAAAwLO/AAAAAAAAqr8AAAAAAACZPwAAAAAAQL6/AAAAAABAsb8AAAAAAACpvwAAAAAAAJg/AAAAAABAsb8AAAAAAACKPwAAAAAAAJ8/AAAAAACAuD8AAAAAAACrvwAAAAAAgKS/AAAAAACAor8AAAAAAICgPwAAAAAAQL2/AAAAAAAAk78AAAAAAAB8PwAAAAAAALU/AAAAAACAs78AAAAAAACKPwAAAAAAAJ8/AAAAAABAuT8AAAAAAEDCvwAAAAAAQLu/AAAAAADAsb8AAAAAAACKPwAAAAAAIMC/AAAAAADAsb8AAAAAAACmvwAAAAAAAJ4/AAAAAAAAtL8AAAAAAICivwAAAAAAAIy/AAAAAAAAqj8AAAAAAAC1vwAAAAAAAHA/AAAAAAAAlT8AAAAAAEC3PwAAAAAAALy/AAAAAADAs78AAAAAAICuvwAAAAAAAJM/AAAAAABgwL8AAAAAAACZvwAAAAAAAFC/AAAAAAAAtD8AAAAAAACAvwAAAAAAgK8/AAAAAACAsD8AAAAAAAC/PwAAAAAAAKk/AAAAAACAuD8AAAAAAEC5PwAAAAAAgMI/AAAAAABAuD8AAAAAAKDAPwAAAAAAQL8/AAAAAACgxD8AAAAAAEDAPwAAAAAAoMM/AAAAAADgwT8AAAAAACDGPwAAAAAAQMI/AAAAAAAgxT8AAAAAAGDDPwAAAAAAQMc/AAAAAACAoz8AAAAAAACfPwAAAAAAAJg/AAAAAAAAsz8AAAAAAEC/vwAAAAAAALS/AAAAAACArb8AAAAAAACMPwAAAAAAQMC/AAAAAACAsr8AAAAAAACovwAAAAAAAJc/AAAAAADAub8AAAAAAACtvwAAAAAAAKO/AAAAAAAAmz8AAAAAAICqvwAAAAAAAJU/AAAAAAAAoT8AAAAAAIC4PwAAAAAAAJu/AAAAAAAAir8AAAAAAACIvwAAAAAAgKg/AAAAAAAAnb8AAAAAAACkPwAAAAAAAKg/AAAAAACAuj8AAAAAAMCyPwAAAAAAwLw/AAAAAACAuj8AAAAAAEDCPwAAAAAAAJE/AAAAAAAAdD8AAAAAAABgvwAAAAAAgKY/AAAAAADAu78AAAAAAACzvwAAAAAAAK6/AAAAAAAAhj8AAAAAAEC+vwAAAAAAQLG/AAAAAAAAp78AAAAAAACbPwAAAAAAgLa/AAAAAAAAp78AAAAAAACgvwAAAAAAgKE/AAAAAAAAu78AAAAAAACuvwAAAAAAAKe/AAAAAAAAmT8AAAAAAMCyvwAAAAAAAHQ/AAAAAAAAlj8AAAAAAIC1PwAAAAAAALO/AAAAAACAqr8AAAAAAAClvwAAAAAAAJo/AAAAAACAxL8AAAAAAACsvwAAAAAAAJW/AAAAAACArD8AAAAAAIDNvwAAAAAA4MS/AAAAAADAvb8AAAAAAACUvwAAAAAAoMS/AAAAAADAtr8AAAAAAICvvwAAAAAAAJI/AAAAAACAur8AAAAAAICrvwAAAAAAAKS/AAAAAAAAnz8AAAAAAACpvwAAAAAAAJk/AAAAAAAAoz8AAAAAAIC5PwAAAAAAAJy/AAAAAAAAkr8AAAAAAACCvwAAAAAAAKo/AAAAAAAAn78AAAAAAICiPwAAAAAAAKg/AAAAAACAuz8AAAAAAACyPwAAAAAAgLw/AAAAAACAuj8AAAAAAADDPwAAAAAAAJE/AAAAAAAAfD8AAAAAAABgvwAAAAAAgKo/AAAAAADAu78AAAAAAECxvwAAAAAAgKu/AAAAAAAAlT8AAAAAAIC8vwAAAAAAALG/AAAAAAAAp78AAAAAAACZPwAAAAAAgLq/AAAAAACAq78AAAAAAICjvwAAAAAAAJ0/AAAAAADAu78AAAAAAECwvwAAAAAAgKe/AAAAAAAAlj8AAAAAAEC5vwAAAAAAAIS/AAAAAAAAfD8AAAAAAAC0PwAAAAAAAMG/AAAAAACAt78AAAAAAMCwvwAAAAAAAI4/AAAAAAAg078AAAAAAGDIvwAAAAAAIMG/AAAAAAAAnb8AAAAAAKDEvwAAAAAAALe/AAAAAAAArr8AAAAAAACXPwAAAAAAwLi/AAAAAAAAqb8AAAAAAACgvwAAAAAAAKQ/AAAAAACApb8AAAAAAACePwAAAAAAgKY/AAAAAAAAuz8AAAAAAACVvwAAAAAAAIC/AAAAAAAAYL8AAAAAAICtPwAAAAAAAJq/AAAAAACApj8AAAAAAACsPwAAAAAAgL0/AAAAAADAsj8AAAAAAEC+PwAAAAAAwLw/AAAAAADAwz8AAAAAAACTPwAAAAAAAIQ/AAAAAAAAdD8AAAAAAICrPwAAAAAAQLa/AAAAAACAqb8AAAAAAIChvwAAAAAAAKE/AAAAAADAub8AAAAAAACsvwAAAAAAAKO/AAAAAAAAnz8AAAAAAIC2vwAAAAAAAKS/AAAAAAAAnb8AAAAAAAClPwAAAAAAgKq/AAAAAAAAmT8AAAAAAACiPwAAAAAAALo/AAAAAAAArb8AAAAAAICgvwAAAAAAAJ2/AAAAAACApD8AAAAAAMDDvwAAAAAAgKe/AAAAAAAAkb8AAAAAAICwPwAAAAAAIMu/AAAAAABgw78AAAAAAAC6vwAAAAAAAIC/AAAAAABgwr8AAAAAAEC0vwAAAAAAgKi/AAAAAAAAnT8AAAAAAAC4vwAAAAAAgKe/AAAAAAAAnb8AAAAAAIClPwAAAAAAgKO/AAAAAAAAoz8AAAAAAICoPwAAAAAAQLw/AAAAAAAAiL8AAAAAAAAAAAAAAAAAAHA/AAAAAAAArz8AAAAAAACKvwAAAAAAgKo/AAAAAACArz8AAAAAAEC+PwAAAAAAALY/AAAAAAAAwD8AAAAAAEC+PwAAAAAAQMQ/AAAAAAAAoj8AAAAAAACXPwAAAAAAAI4/AAAAAACArj8AAAAAAMC2vwAAAAAAAKm/AAAAAAAAor8AAAAAAICiPwAAAAAAQMC/AAAAAAAAs78AAAAAAICpvwAAAAAAAJs/AAAAAADAvr8AAAAAAICwvwAAAAAAgKi/AAAAAAAAlz8AAAAAAMCwvwAAAAAAAIo/AAAAAAAAoD8AAAAAAMC4PwAAAAAAAKy/AAAAAACApL8AAAAAAICgvwAAAAAAAJ8/AAAAAABAu78AAAAAAACQvwAAAAAAAIQ/AAAAAADAtD8AAAAAAMCxvwAAAAAAAJA/AAAAAAAAoD8AAAAAAEC5PwAAAAAAIMO/AAAAAADAur8AAAAAAACyvwAAAAAAAIw/AAAAAACgwL8AAAAAAECxvwAAAAAAgKa/AAAAAAAAnT8AAAAAAIC0vwAAAAAAAKO/AAAAAAAAjr8AAAAAAICpPwAAAAAAgLW/AAAAAAAAUD8AAAAAAACWPwAAAAAAwLY/AAAAAADAvr8AAAAAAMC1vwAAAAAAgK+/AAAAAAAAjD8AAAAAAMDAvwAAAAAAAJm/AAAAAAAAUL8AAAAAAMCzPwAAAAAAAHC/AAAAAAAAsD8AAAAAAECwPwAAAAAAAL8/AAAAAACApT8AAAAAAIC4PwAAAAAAwLg/AAAAAABAwj8AAAAAAAC3PwAAAAAAIMA/AAAAAABAvj8AAAAAAEDEPwAAAAAAQL8/AAAAAAAAwz8AAAAAAKDBPwAAAAAAwMU/AAAAAACgwT8AAAAAAGDEPwAAAAAAAMM/AAAAAADAxj8AAAAAAACfPwAAAAAAAJk/AAAAAAAAlD8AAAAAAACyPwAAAAAAYMC/AAAAAADAtL8AAAAAAACvvwAAAAAAAI4/AAAAAADAwL8AAAAAAECyvwAAAAAAgKm/AAAAAAAAlj8AAAAAAAC6vwAAAAAAAK2/AAAAAACApL8AAAAAAACbPwAAAAAAgKq/AAAAAAAAlD8AAAAAAAChPwAAAAAAgLc/AAAAAAAAnL8AAAAAAACTvwAAAAAAAIa/AAAAAACApj8AAAAAAACdvwAAAAAAAKM/AAAAAACApj8AAAAAAEC6PwAAAAAAgLI/AAAAAAAAvT8AAAAAAEC6PwAAAAAAYMI/AAAAAAAAjj8AAAAAAAB4PwAAAAAAAHi/AAAAAAAApj8AAAAAAEC7vwAAAAAAQLO/AAAAAACArr8AAAAAAACCPwAAAAAAwL2/AAAAAACAsb8AAAAAAICmvwAAAAAAAJk/AAAAAAAAtb8AAAAAAICnvwAAAAAAAKC/AAAAAAAAoD8AAAAAAMC5vwAAAAAAgK2/AAAAAACAp78AAAAAAACZPwAAAAAAQLK/AAAAAAAAgD8AAAAAAACVPwAAAAAAgLY/AAAAAADAsb8AAAAAAICovwAAAAAAgKS/AAAAAAAAnD8AAAAAAMDDvwAAAAAAgKq/AAAAAAAAlb8AAAAAAACtPwAAAAAA4My/AAAAAADgxL8AAAAAAIC8vwAAAAAAAJa/AAAAAABAxL8AAAAAAEC3vwAAAAAAAK+/AAAAAAAAkD8AAAAAAMC6vwAAAAAAgKy/AAAAAAAApb8AAAAAAACfPwAAAAAAgKq/AAAAAAAAmj8AAAAAAICiPwAAAAAAwLk/AAAAAAAAnb8AAAAAAACRvwAAAAAAAIq/AAAAAACAqD8AAAAAAICivwAAAAAAAKA/AAAAAAAApz8AAAAAAIC6PwAAAAAAgLE/AAAAAABAuz8AAAAAAMC6PwAAAAAAoMI/AAAAAAAAij8AAAAAAABoPwAAAAAAAGC/AAAAAACAqD8AAAAAAIC6vwAAAAAAQLC/AAAAAAAAqb8AAAAAAACXPwAAAAAAAL2/AAAAAADAsL8AAAAAAICnvwAAAAAAAJs/AAAAAABAur8AAAAAAICqvwAAAAAAAKO/AAAAAAAAnj8AAAAAAMC7vwAAAAAAQLC/AAAAAACAqL8AAAAAAACVPwAAAAAAgLm/AAAAAAAAhr8AAAAAAACCPwAAAAAAwLM/AAAAAACgwb8AAAAAAMC4vwAAAAAAALG/AAAAAAAAij8AAAAAADDTvwAAAAAAoMi/AAAAAABAwb8AAAAAAACevwAAAAAA4MS/AAAAAABAtr8AAAAAAICuvwAAAAAAAJc/AAAAAAAAur8AAAAAAICpvwAAAAAAAKG/AAAAAACAoj8AAAAAAICnvwAAAAAAAJs/AAAAAACApT8AAAAAAMC6PwAAAAAAAJe/AAAAAAAAhL8AAAAAAABwvwAAAAAAgKw/AAAAAAAAmb8AAAAAAIClPwAAAAAAAKs/AAAAAADAvD8AAAAAAACyPwAAAAAAQL0/AAAAAABAvD8AAAAAAMDDPwAAAAAAAJE/AAAAAAAAiD8AAAAAAABwPwAAAAAAgKw/AAAAAAAAtr8AAAAAAACovwAAAAAAgKC/AAAAAAAAoj8AAAAAAIC5vwAAAAAAgK2/AAAAAACAo78AAAAAAACePwAAAAAAALe/AAAAAACApb8AAAAAAACZvwAAAAAAAKQ/AAAAAACArb8AAAAAAACTPwAAAAAAAKE/AAAAAAAAuT8AAAAAAACtvwAAAAAAgKC/AAAAAAAAnr8AAAAAAACkPwAAAAAAwMG/AAAAAACAor8AAAAAAACCvwAAAAAAQLI/AAAAAACgyb8AAAAAACDCvwAAAAAAALm/AAAAAAAAdL8AAAAAAEDCvwAAAAAAwLO/AAAAAACAqb8AAAAAAACdPwAAAAAAwLe/AAAAAACAp78AAAAAAACcvwAAAAAAgKQ/AAAAAAAAo78AAAAAAAChPwAAAAAAAKg/AAAAAABAvD8AAAAAAACKvwAAAAAAAGC/AAAAAAAAAAAAAAAAAACvPwAAAAAAAI6/AAAAAACAqj8AAAAAAICuPwAAAAAAgL4/AAAAAADAtD8AAAAAAIC/PwAAAAAAwL0/AAAAAAAAxD8AAAAAAICgPwAAAAAAAJU/AAAAAAAAij8AAAAAAICtPwAAAAAAgLa/AAAAAACAqL8AAAAAAAChvwAAAAAAAKI/AAAAAADAwL8AAAAAAMCzvwAAAAAAAKq/AAAAAAAAlz8AAAAAAIC+vwAAAAAAgLG/AAAAAACAqb8AAAAAAACXPwAAAAAAgLG/AAAAAAAAjj8AAAAAAACePwAAAAAAALk/AAAAAAAAq78AAAAAAACkvwAAAAAAgKK/AAAAAAAAnz8AAAAAAEC5vwAAAAAAAIa/AAAAAAAAij8AAAAAAIC1PwAAAAAAgKu/AAAAAAAAlz8AAAAAAACkPwAAAAAAQLo/AAAAAADAwb8AAAAAAIC6vwAAAAAAALK/AAAAAAAAij8AAAAAAKDAvwAAAAAAALK/AAAAAACAp78AAAAAAACfPwAAAAAAwLS/AAAAAACAor8AAAAAAACQvwAAAAAAgKo/AAAAAACAtL8AAAAAAABgPwAAAAAAAJU/AAAAAADAtj8AAAAAAIC4vwAAAAAAwLG/AAAAAAAAq78AAAAAAACUPwAAAAAAIMC/AAAAAAAAmr8AAAAAAABgPwAAAAAAgLM/AAAAAAAAfL8AAAAAAACvPwAAAAAAQLA/AAAAAABAvz8AAAAAAIClPwAAAAAAQLg/AAAAAAAAuD8AAAAAAEDCPwAAAAAAwLY/AAAAAAAgwD8AAAAAAIC+PwAAAAAAQMQ/AAAAAACAvj8AAAAAAADDPwAAAAAAgME/AAAAAACgxT8AAAAAAEDBPwAAAAAAQMQ/AAAAAACgwj8AAAAAAKDGPwAAAAAAAKE/AAAAAAAAmD8AAAAAAACTPwAAAAAAQLI/AAAAAACAwL8AAAAAAEC1vwAAAAAAALC/AAAAAAAAij8AAAAAAMDAvwAAAAAAgLK/AAAAAAAAqr8AAAAAAACWPwAAAAAAwLq/AAAAAACArb8AAAAAAACmvwAAAAAAAJw/AAAAAACArL8AAAAAAACSPwAAAAAAgKA/AAAAAAAAuD8AAAAAAIChvwAAAAAAAJe/AAAAAAAAkL8AAAAAAAClPwAAAAAAAKO/AAAAAAAAnT8AAAAAAICkPwAAAAAAwLk/AAAAAACArT8AAAAAAIC6PwAAAAAAQLk/AAAAAADAwT8AAAAAAAB8PwAAAAAAAAAAAAAAAAAAhL8AAAAAAICmPwAAAAAAALy/AAAAAACAsr8AAAAAAICuvwAAAAAAAIQ/AAAAAAAAv78AAAAAAECxvwAAAAAAAKe/AAAAAAAAmj8AAAAAAIC1vwAAAAAAgKe/AAAAAAAAoL8AAAAAAAChPwAAAAAAwLq/AAAAAAAAsL8AAAAAAICmvwAAAAAAAJg/AAAAAABAtL8AAAAAAAAAAAAAAAAAAJE/AAAAAADAtT8AAAAAAICzvwAAAAAAgKq/AAAAAAAApr8AAAAAAACcPwAAAAAA4MS/AAAAAACAq78AAAAAAACXvwAAAAAAAK0/AAAAAADgzb8AAAAAAIDFvwAAAAAAwL2/AAAAAAAAlr8AAAAAAEDEvwAAAAAAwLa/AAAAAACArb8AAAAAAACSPwAAAAAAQLq/AAAAAAAAq78AAAAAAAChvwAAAAAAAKE/AAAAAACAp78AAAAAAACePwAAAAAAAKU/AAAAAACAuj8AAAAAAACVvwAAAAAAAHi/AAAAAAAAcL8AAAAAAACsPwAAAAAAAJO/AAAAAACAqD8AAAAAAICsPwAAAAAAwLw/AAAAAABAtD8AAAAAAMC+PwAAAAAAwLw/AAAAAABgwz8AAAAAAACWPwAAAAAAAIo/AAAAAAAAdD8AAAAAAACrPwAAAAAAwLm/AAAAAABAsL8AAAAAAACovwAAAAAAAJc/AAAAAACAvL8AAAAAAICwvwAAAAAAgKa/AAAAAAAAnD8AAAAAAMC6vwAAAAAAAKu/AAAAAAAApL8AAAAAAACgPwAAAAAAgLu/AAAAAAAAr78AAAAAAACovwAAAAAAAJk/AAAAAADAuL8AAAAAAACAvwAAAAAAAIQ/AAAAAACAtD8AAAAAAODAvwAAAAAAQLe/AAAAAAAAsL8AAAAAAACQPwAAAAAAwNK/AAAAAABgyL8AAAAAAKDAvwAAAAAAAJy/AAAAAADAxL8AAAAAAEC2vwAAAAAAAK2/AAAAAAAAmD8AAAAAAEC5vwAAAAAAAKi/AAAAAAAAn78AAAAAAICkPwAAAAAAgKa/AAAAAACAoT8AAAAAAICmPwAAAAAAwLs/AAAAAAAAl78AAAAAAACAvwAAAAAAAFC/AAAAAACArT8AAAAAAACavwAAAAAAAKY/AAAAAACArD8AAAAAAAC9PwAAAAAAALM/AAAAAADAvT8AAAAAAMC8PwAAAAAAwMM/AAAAAAAAmD8AAAAAAACQPwAAAAAAAHw/AAAAAAAArj8AAAAAAAC2vwAAAAAAgKi/AAAAAACAoL8AAAAAAACjPwAAAAAAgLm/AAAAAACAq78AAAAAAICivwAAAAAAgKA/AAAAAADAtb8AAAAAAICjvwAAAAAAAJm/AAAAAACApT8AAAAAAACrvwAAAAAAAJc/AAAAAACAoz8AAAAAAIC5PwAAAAAAAKy/AAAAAACAoL8AAAAAAACYvwAAAAAAAKU/AAAAAAAgwr8AAAAAAICjvwAAAAAAAIS/AAAAAAAAsj8AAAAAAGDKvwAAAAAAYMK/AAAAAACAub8AAAAAAAB0vwAAAAAAQMK/AAAAAAAAs78AAAAAAACpvwAAAAAAAJ4/AAAAAADAtr8AAAAAAACnvwAAAAAAAJu/AAAAAAAApT8AAAAAAICjvwAAAAAAAKI/AAAAAAAAqT8AAAAAAAC8PwAAAAAAAIK/AAAAAAAAYL8AAAAAAABwPwAAAAAAAK8/AAAAAAAAiL8AAAAAAICrPwAAAAAAAK8/AAAAAACAvj8AAAAAAEC3PwAAAAAAoMA/AAAAAABAvz8AAAAAAMDEPwAAAAAAAKM/AAAAAAAAmz8AAAAAAACQPwAAAAAAQLA/AAAAAADAtr8AAAAAAICovwAAAAAAgKC/AAAAAACAoT8AAAAAAMC/vwAAAAAAALO/AAAAAACAp78AAAAAAACaPwAAAAAAAL6/AAAAAADAsL8AAAAAAACovwAAAAAAAJg/AAAAAABAsb8AAAAAAACQPwAAAAAAAKA/AAAAAADAuD8AAAAAAICsvwAAAAAAgKK/AAAAAAAAor8AAAAAAICgPwAAAAAAgLq/AAAAAAAAhr8AAAAAAACGPwAAAAAAgLU/AAAAAABAsL8AAAAAAACVPwAAAAAAAKI/AAAAAACAuT8AAAAAAIC1vwAAAAAAAK+/AAAAAAAAo78AAAAAAAChPwAAAAAAgLq/AAAAAABAsL8AAAAAAACnvwAAAAAAAJo/AAAAAAAAu78AAAAAAACtvwAAAAAAAKW/AAAAAAAAnz8AAAAAAIC0vwAAAAAAAIA/AAAAAAAAnT8AAAAAAMC4PwAAAAAAALm/AAAAAACAsb8AAAAAAACovwAAAAAAAJw/AAAAAAAAwL8AAAAAAACwvwAAAAAAgKK/AAAAAAAAoj8AAAAAAIDCvwAAAAAAALW/AAAAAAAArL8AAAAAAACXPwAAAAAAQM6/AAAAAAAgxr8AAAAAAAC8vwAAAAAAAIS/AAAAAABAzr8AAAAAAMC3vwAAAAAAAKa/AAAAAACAqj8AAAAAAACXvwAAAAAAgK0/AAAAAACAsD8AAAAAAADAPwAAAAAAALG/AAAAAAAAor8AAAAAAACWvwAAAAAAgKk/AAAAAACAub8AAAAAAAB0vwAAAAAAAJk/AAAAAAAAuD8AAAAAAAB0vwAAAAAAAK8/AAAAAACAsj8AAAAAAKDAPwAAAAAAAJG/AAAAAAAAqT8AAAAAAECwPwAAAAAAAL8/AAAAAAAAuj8AAAAAAMDBPwAAAAAAIME/AAAAAAAgxj8AAAAAAEC4PwAAAAAA4MA/AAAAAAAAvz8AAAAAAGDEPwAAAAAAAKw/AAAAAADAuD8AAAAAAEC5PwAAAAAAgMI/AAAAAADAtz8AAAAAAIDAPwAAAAAAAL8/AAAAAABAxD8AAAAAAEC9PwAAAAAAAMI/AAAAAACgwD8AAAAAAADFPwAAAAAAgKc/AAAAAAAApD8AAAAAAACePwAAAAAAQLM/AAAAAACAob8AAAAAAACgPwAAAAAAgKQ/AAAAAACAuT8AAAAAAAB4PwAAAAAAAIY/AAAAAAAAgj8AAAAAAACwPwAAAAAAgKm/AAAAAAAAkz8AAAAAAACcPwAAAAAAALc/AAAAAAAApb8AAAAAAACZPwAAAAAAAKI/AAAAAABAuD8AAAAAAABQvwAAAAAAgKs/AAAAAACArj8AAAAAAEC8PwAAAAAAAK4/AAAAAABAuT8AAAAAAAC4PwAAAAAAIME/AAAAAAAAuD8AAAAAAIC/PwAAAAAAgLw/AAAAAADgwj8AAAAAAIC4PwAAAAAAIMA/AAAAAABAvT8AAAAAAODCPwAAAAAAgK0/AAAAAADAtz8AAAAAAMC0PwAAAAAAQL8/AAAAAACAw78AAAAAACDAvwAAAAAAQLm/AAAAAAAAlr8AAAAAAODQvwAAAAAAIMa/AAAAAADAv78AAAAAAIChvwAAAAAAAMy/AAAAAACgwr8AAAAAAMC8vwAAAAAAAJq/AAAAAADw0r8AAAAAACDIvwAAAAAAAMK/AAAAAACApb8AAAAAAEDIvwAAAAAAwLK/AAAAAACAob8AAAAAAICoPwAAAAAAAGA/AAAAAACArz8AAAAAAACwPwAAAAAAgL0/AAAAAABAsj8AAAAAAIC7PwAAAAAAALo/AAAAAADAwT8AAAAAAAC3PwAAAAAAwL4/AAAAAABAvD8AAAAAAODCPwAAAAAAAK4/AAAAAADAtz8AAAAAAIC1PwAAAAAAAMA/AAAAAADAwL8AAAAAAAC7vwAAAAAAwLW/AAAAAAAAfL8AAAAAADDQvwAAAAAAYMS/AAAAAACAvb8AAAAAAACZvwAAAAAAgMu/AAAAAADAwb8AAAAAAEC7vwAAAAAAAJO/AAAAAACw0r8AAAAAAKDHvwAAAAAAQMG/AAAAAACApL8AAAAAAKDHvwAAAAAAgLK/AAAAAAAAnr8AAAAAAICpPwAAAAAAAHA/AAAAAABAsD8AAAAAAECxPwAAAAAAQL4/AAAAAAAAsz8AAAAAAIC8PwAAAAAAQLo/AAAAAACgwj8AAAAAAMC3PwAAAAAAIMA/AAAAAABAvT8AAAAAAGDDPwAAAAAAAK8/AAAAAAAAuT8AAAAAAAC2PwAAAAAAYMA/AAAAAAAgwL8AAAAAAEC6vwAAAAAAALW/AAAAAAAAeL8AAAAAAMDPvwAAAAAAgMS/AAAAAADAvL8AAAAAAACXvwAAAAAAoMq/AAAAAABgwb8AAAAAAEC6vwAAAAAAAJG/AAAAAACQ0r8AAAAAAEDHvwAAAAAAQMG/AAAAAAAAob8AAAAAAKDHvwAAAAAAwLC/AAAAAAAAnL8AAAAAAICsPwAAAAAAAHg/AAAAAAAAsT8AAAAAAECyPwAAAAAAAL8/AAAAAAAAtj8AAAAAAMC+PwAAAAAAAL0/AAAAAAAAwz8AAAAAAAC5PwAAAAAAYMA/AAAAAABAvj8AAAAAAMDDPwAAAAAAALA/AAAAAACAuT8AAAAAAIC2PwAAAAAAwMA/AAAAAABAu78AAAAAAAC1vwAAAAAAALG/AAAAAAAAgj8AAAAAAKDOvwAAAAAAoMO/AAAAAABAvb8AAAAAAACWvwAAAAAAQMO/AAAAAABAtr8AAAAAAECwvwAAAAAAAIg/AAAAAABgy78AAAAAAGDBvwAAAAAAALm/AAAAAAAAjL8AAAAAAGDMvwAAAAAAoMK/AAAAAAAAur8AAAAAAACKvwAAAAAAAMO/AAAAAACAtb8AAAAAAACtvwAAAAAAAJU/AAAAAADgwL8AAAAAAICzvwAAAAAAgKu/AAAAAAAAlz8AAAAAAIC1vwAAAAAAAGA/AAAAAAAAlT8AAAAAAMC2PwAAAAAAAHC/AAAAAAAAgD8AAAAAAACIPwAAAAAAwLA/AAAAAACAr78AAAAAAACMPwAAAAAAAJ4/AAAAAACAtz8AAAAAAICjvwAAAAAAAKA/AAAAAAAApj8AAAAAAIC5PwAAAAAAAIQ/AAAAAACAsD8AAAAAAECyPwAAAAAAQL8/AAAAAABAsT8AAAAAAMC7PwAAAAAAwLk/AAAAAABAwj8AAAAAAIC6PwAAAAAAIME/AAAAAADAvj8AAAAAACDEPwAAAAAAwLs/AAAAAABAwT8AAAAAAIC/PwAAAAAAIMQ/AAAAAACAsT8AAAAAAMC5PwAAAAAAQLc/AAAAAAAAwT8AAAAAAIDAvwAAAAAAgLu/AAAAAABAtb8AAAAAAAB4vwAAAAAAgM+/AAAAAAAgxL8AAAAAAMC8vwAAAAAAAJa/AAAAAADgyr8AAAAAAEDBvwAAAAAAwLq/AAAAAAAAkL8AAAAAAPDSvwAAAAAA4Me/AAAAAABgwb8AAAAAAICivwAAAAAAYMi/AAAAAADAsr8AAAAAAACfvwAAAAAAAKo/AAAAAAAAgD8AAAAAAACxPwAAAAAAQLI/AAAAAACAvz8AAAAAAEC0PwAAAAAAQL0/AAAAAADAuz8AAAAAAKDCPwAAAAAAgLg/AAAAAABAwD8AAAAAAMC9PwAAAAAAoMM/AAAAAAAArz8AAAAAAEC5PwAAAAAAALY/AAAAAACgwD8AAAAAAKDBvwAAAAAAwLu/AAAAAAAAtr8AAAAAAAB8vwAAAAAAUNC/AAAAAADgxL8AAAAAAAC9vwAAAAAAAJe/AAAAAACgyr8AAAAAAMDBvwAAAAAAQLq/AAAAAAAAkL8AAAAAAMDSvwAAAAAAwMe/AAAAAABAwb8AAAAAAIChvwAAAAAAoMe/AAAAAADAsb8AAAAAAACdvwAAAAAAgKw/AAAAAAAAgD8AAAAAAMCxPwAAAAAAQLI/AAAAAADAvz8AAAAAAIC0PwAAAAAAwL4/AAAAAACAvD8AAAAAAGDDPwAAAAAAgLk/AAAAAACAwD8AAAAAAMC+PwAAAAAAAMQ/AAAAAABAsD8AAAAAAMC5PwAAAAAAgLc/AAAAAADAwD8AAAAAAEDAvwAAAAAAwLm/AAAAAACAs78AAAAAAABQvwAAAAAAoM+/AAAAAACgw78AAAAAAAC8vwAAAAAAAJG/AAAAAABgyr8AAAAAAMDAvwAAAAAAgLm/AAAAAAAAiL8AAAAAAKDSvwAAAAAAAMe/AAAAAADAwL8AAAAAAACgvwAAAAAAwMe/AAAAAAAAsr8AAAAAAACbvwAAAAAAAKw/AAAAAAAAiD8AAAAAAACyPwAAAAAAQLM/AAAAAADAvz8AAAAAAIC1PwAAAAAAgL4/AAAAAADAvD8AAAAAAEDDPwAAAAAAALk/AAAAAACgwD8AAAAAAEC+PwAAAAAAAMQ/AAAAAACArj8AAAAAAEC5PwAAAAAAgLY/AAAAAAAgwT8AAAAAAMC9vwAAAAAAwLa/AAAAAADAsb8AAAAAAAB8PwAAAAAAENC/AAAAAADgxL8AAAAAAEC9vwAAAAAAAJi/AAAAAAAgw78AAAAAAMC2vwAAAAAAgK+/AAAAAAAAjD8AAAAAAODKvwAAAAAAIMG/AAAAAACAuL8AAAAAAACIvwAAAAAAwMu/AAAAAAAgwr8AAAAAAMC5vwAAAAAAAIS/AAAAAABgwr8AAAAAAAC0vwAAAAAAgKq/AAAAAAAAmT8AAAAAAEDAvwAAAAAAgLK/AAAAAAAAq78AAAAAAACZPwAAAAAAQLS/AAAAAAAAcD8AAAAAAACYPwAAAAAAALc/AAAAAAAAaD8AAAAAAACEPwAAAAAAAI4/AAAAAACAsT8AAAAAAACvvwAAAAAAAIY/AAAAAAAAnj8AAAAAAAC4PwAAAAAAgKa/AAAAAAAAnT8AAAAAAICkPwAAAAAAgLo/AAAAAAAAgj8AAAAAAACxPwAAAAAAALM/AAAAAADAvz8AAAAAAECzPwAAAAAAgL0/AAAAAAAAvD8AAAAAAEDDPwAAAAAAQLw/AAAAAADAwT8AAAAAAEDAPwAAAAAAoMQ/AAAAAABAvT8AAAAAAADCPwAAAAAAoMA/AAAAAACAxD8AAAAAAMCyPwAAAAAAgLs/AAAAAACAuD8AAAAAAEDBPwAAAAAAQL+/AAAAAAAAuL8AAAAAAICzvwAAAAAAAGA/AAAAAABgzr8AAAAAAODCvwAAAAAAQLu/AAAAAAAAkr8AAAAAAADKvwAAAAAAoMC/AAAAAACAub8AAAAAAACMvwAAAAAAINK/AAAAAABgx78AAAAAAODAvwAAAAAAAKG/AAAAAAAgx78AAAAAAMCxvwAAAAAAAJu/AAAAAAAArD8AAAAAAACMPwAAAAAAgLE/AAAAAACAsj8AAAAAAIC/PwAAAAAAALU/AAAAAABAvj8AAAAAAAC8PwAAAAAAQMM/AAAAAACAuT8AAAAAAMDAPwAAAAAAgL4/AAAAAADgwz8AAAAAAACwPwAAAAAAwLk/AAAAAAAAtz8AAAAAAMDAPwAAAAAAQMC/AAAAAACAur8AAAAAAEC0vwAAAAAAAGi/AAAAAAAgz78AAAAAAODDvwAAAAAAwLu/AAAAAAAAlb8AAAAAACDKvwAAAAAAQMG/AAAAAAAAur8AAAAAAACMvwAAAAAAANK/AAAAAABgxr8AAAAAAMDAvwAAAAAAAJ+/AAAAAABgx78AAAAAAMCwvwAAAAAAAJu/AAAAAAAArT8AAAAAAACRPwAAAAAAwLM/AAAAAADAsz8AAAAAAEDAPwAAAAAAALY/AAAAAADAvj8AAAAAAAC9PwAAAAAAQMM/AAAAAACAuT8AAAAAAIDAPwAAAAAAwL4/AAAAAADgwz8AAAAAAICvPwAAAAAAQLk/AAAAAADAtj8AAAAAAODAPwAAAAAAIMG/AAAAAABAu78AAAAAAMC1vwAAAAAAAGi/AAAAAAAA0L8AAAAAAADEvwAAAAAAQLy/AAAAAAAAkb8AAAAAAODKvwAAAAAAIMG/AAAAAADAub8AAAAAAACMvwAAAAAAoNG/AAAAAAAgxr8AAAAAAMC/vwAAAAAAAJ6/AAAAAAAgxr8AAAAAAICuvwAAAAAAAJW/AAAAAACArj8AAAAAAACVPwAAAAAAgLM/AAAAAAAAtD8AAAAAAGDAPwAAAAAAwLY/AAAAAAAAwD8AAAAAAMC9PwAAAAAAwMM/AAAAAAAAuj8AAAAAAADBPwAAAAAAAL8/AAAAAAAgxD8AAAAAAECwPwAAAAAAwLk/AAAAAABAtz8AAAAAAADBPwAAAAAAQLq/AAAAAABAtb8AAAAAAACwvwAAAAAAAIQ/AAAAAABgzr8AAAAAAADEvwAAAAAAQLy/AAAAAAAAlL8AAAAAAODCvwAAAAAAwLa/AAAAAAAAsL8AAAAAAACOPwAAAAAAoMu/AAAAAACAwb8AAAAAAEC5vwAAAAAAAIa/AAAAAABgzL8AAAAAAGDCvwAAAAAAALq/AAAAAAAAhL8AAAAAAIDCvwAAAAAAwLS/AAAAAACAqr8AAAAAAACWPwAAAAAAwMC/AAAAAADAs78AAAAAAICqvwAAAAAAAJU/AAAAAABAtb8AAAAAAAAAAAAAAAAAAJg/AAAAAACAtj8AAAAAAABQPwAAAAAAAIY/AAAAAAAAij8AAAAAAICxPwAAAAAAALC/AAAAAAAAjj8AAAAAAACdPwAAAAAAQLg/AAAAAAAAp78AAAAAAACdPwAAAAAAAKQ/AAAAAAAAuj8AAAAAAACKPwAAAAAAQLE/AAAAAACAsj8AAAAAAMC/PwAAAAAAwLI/AAAAAACAvD8AAAAAAIC7PwAAAAAAwMI/AAAAAABAuz8AAAAAAEDBPwAAAAAAAMA/AAAAAABgxD8AAAAAAAC8PwAAAAAAgME/AAAAAADAvz8AAAAAAGDEPwAAAAAAgLA/AAAAAABAuj8AAAAAAAC3PwAAAAAAIME/AAAAAABgwb8AAAAAAIC6vwAAAAAAQLW/AAAAAAAAaL8AAAAAAODPvwAAAAAA4MO/AAAAAAAAvL8AAAAAAACWvwAAAAAAwMq/AAAAAAAgwb8AAAAAAIC5vwAAAAAAAJC/AAAAAAAw0r8AAAAAAMDGvwAAAAAAYMC/AAAAAAAAoL8AAAAAAADHvwAAAAAAgLC/AAAAAAAAmb8AAAAAAICsPwAAAAAAAI4/AAAAAACAsj8AAAAAAACzPwAAAAAAAMA/AAAAAAAAtT8AAAAAAEC/PwAAAAAAwLw/AAAAAAAgwz8AAAAAAEC5PwAAAAAAoMA/AAAAAADAvj8AAAAAAODDPwAAAAAAQLA/AAAAAAAAuT8AAAAAAMC2PwAAAAAAwMA/AAAAAACAwL8AAAAAAIC6vwAAAAAAgLS/AAAAAAAAYL8AAAAAAGDPvwAAAAAAIMS/AAAAAABAvL8AAAAAAACTvwAAAAAAAMu/AAAAAAAgwb8AAAAAAAC6vwAAAAAAAIq/AAAAAAAQ0r8AAAAAAGDGvwAAAAAAYMC/AAAAAAAAnb8AAAAAAADHvwAAAAAAQLC/AAAAAAAAmL8AAAAAAICtPwAAAAAAAI4/AAAAAABAsj8AAAAAAICzPwAAAAAAwL8/AAAAAACAtT8AAAAAAAC/PwAAAAAAQL0/AAAAAAAAwz8AAAAAAEC6PwAAAAAA4MA/AAAAAADAvj8AAAAAAODDPwAAAAAAgLA/AAAAAADAuT8AAAAAAMC2PwAAAAAA4MA/AAAAAACgwL8AAAAAAMC5vwAAAAAAALW/AAAAAAAAYL8AAAAAAGDPvwAAAAAAoMO/AAAAAAAAvL8AAAAAAACTvwAAAAAAIMq/AAAAAAAgwb8AAAAAAIC5vwAAAAAAAIi/AAAAAACQ0b8AAAAAAEDGvwAAAAAAAMC/AAAAAAAAnb8AAAAAAGDGvwAAAAAAALC/AAAAAAAAl78AAAAAAICuPwAAAAAAAJA/AAAAAABAsz8AAAAAAMCzPwAAAAAAQMA/AAAAAAAAtz8AAAAAACDAPwAAAAAAAL4/AAAAAADgwz8AAAAAAEC6PwAAAAAAAME/AAAAAABAvz8AAAAAAADEPwAAAAAAgK8/AAAAAAAAuT8AAAAAAMC2PwAAAAAAwMA/AAAAAABAub8AAAAAAMC0vwAAAAAAAK+/AAAAAAAAgj8AAAAAAMDNvwAAAAAAgMO/AAAAAACAvL8AAAAAAACVvwAAAAAAYMO/AAAAAACAtr8AAAAAAICwvwAAAAAAAIw/AAAAAABAxL8AAAAAAEC4vwAAAAAAgLK/AAAAAAAAdD8AAAAAAODIvwAAAAAAIMC/AAAAAACAtr8AAAAAAABgvwAAAAAA4M2/AAAAAADgw78AAAAAAMC8vwAAAAAAAJS/AAAAAACAz78AAAAAAMDDvwAAAAAAgLq/AAAAAAAAhL8AAAAAAMC/vwAAAAAAAJq/AAAAAAAAdD8AAAAAAEC0PwAAAAAAAJ0/AAAAAADAtT8AAAAAAEC2PwAAAAAAgME/AAAAAACArT8AAAAAAMC6PwAAAAAAwLk/AAAAAABgwj8AAAAAAIC8PwAAAAAAQMI/AAAAAABAwT8AAAAAAIDFPwAAAAAAwLE/AAAAAAAAqj8AAAAAAIClPwAAAAAAwLQ/AAAAAAAAqL8AAAAAAACWvwAAAAAAAI6/AAAAAAAApD8AAAAAAGDBvwAAAAAAgLa/AAAAAACAsb8AAAAAAAB0PwAAAAAAYM+/AAAAAADAxb8AAAAAAKDAvwAAAAAAgKK/AAAAAACgxb8AAAAAAMC4vwAAAAAAQLG/AAAAAAAAiD8AAAAAACDIvwAAAAAAgMG/AAAAAAAAuL8AAAAAAABgvwAAAAAAoMe/AAAAAABAvb8AAAAAAICzvwAAAAAAAHQ/AAAAAABgwL8AAAAAAECyvwAAAAAAgKi/AAAAAAAAnT8AAAAAAKDTvwAAAAAAAMy/AAAAAACgw78AAAAAAACjvwAAAAAAINq/AAAAAADw0r8AAAAAAKDJvwAAAAAAQLC/AAAAAABAwr8AAAAAAACdvwAAAAAAAIQ/AAAAAABAtj8AAAAAAEC/vwAAAAAAALO/AAAAAACApL8AAAAAAACjPwAAAAAAYMa/AAAAAACArL8AAAAAAACMvwAAAAAAALI/AAAAAAAAjD8AAAAAAEC0PwAAAAAAQLY/AAAAAADgwT8AAAAAAICgPwAAAAAAQLY/AAAAAABAtz8AAAAAAGDCPwAAAAAAAIo/AAAAAACAsz8AAAAAAIC0PwAAAAAAIME/AAAAAAAAtj8AAAAAAIDAPwAAAAAAgL8/AAAAAADAxD8AAAAAAEDCPwAAAAAAgMU/AAAAAADgwz8AAAAAAMDHPwAAAAAAgKk/AAAAAACApj8AAAAAAICiPwAAAAAAwLU/AAAAAADAtL8AAAAAAABgvwAAAAAAAJk/AAAAAABAtz8AAAAAAACGPwAAAAAAQLE/AAAAAAAAsj8AAAAAAIC/PwAAAAAAIMO/AAAAAADAvL8AAAAAAAC2vwAAAAAAAFA/AAAAAADAxr8AAAAAAEC6vwAAAAAAwLK/AAAAAAAAhD8AAAAAAODFvwAAAAAAgK2/AAAAAAAAk78AAAAAAICvPwAAAAAAAIQ/AAAAAACAsT8AAAAAAMCzPwAAAAAAQMA/AAAAAABAub8AAAAAAICyvwAAAAAAgKi/AAAAAAAAmj8AAAAAAEC8vwAAAAAAAKy/AAAAAAAAob8AAAAAAICjPwAAAAAAgLK/AAAAAAAAob8AAAAAAACWvwAAAAAAgKY/AAAAAABAs78AAAAAAAB8PwAAAAAAAJY/AAAAAADAtj8AAAAAAMC8vwAAAAAAwLO/AAAAAAAAqL8AAAAAAACcPwAAAAAAIMC/AAAAAAAAnr8AAAAAAABQvwAAAAAAQLM/AAAAAACAtr8AAAAAAABgvwAAAAAAAJI/AAAAAABAtj8AAAAAAACaPwAAAAAAQLU/AAAAAADAtT8AAAAAAEDBPwAAAAAAAJc/AAAAAADAsz8AAAAAAAC0PwAAAAAAwMA/AAAAAADAsT8AAAAAAEC8PwAAAAAAALs/AAAAAACgwj8AAAAAAACzvwAAAAAAgKy/AAAAAAAAo78AAAAAAACePwAAAAAAQLu/AAAAAACArL8AAAAAAAChvwAAAAAAgKE/AAAAAACAs78AAAAAAAClvwAAAAAAAJq/AAAAAACApD8AAAAAAIC1vwAAAAAAAFC/AAAAAAAAij8AAAAAAIC0PwAAAAAAwL+/AAAAAABAtb8AAAAAAICsvwAAAAAAAJY/AAAAAABAv78AAAAAAACZvwAAAAAAAGi/AAAAAABAsj8AAAAAAEC3vwAAAAAAAHS/AAAAAAAAjj8AAAAAAIC1PwAAAAAAAIg/AAAAAACAsT8AAAAAAICyPwAAAAAAAMA/AAAAAAAAgD8AAAAAAACwPwAAAAAAgLE/AAAAAADAvj8AAAAAAICtPwAAAAAAgLk/AAAAAACAuD8AAAAAAODBPwAAAAAAQLW/AAAAAABAsL8AAAAAAACnvwAAAAAAAJo/AAAAAAAAvb8AAAAAAACvvwAAAAAAAKS/AAAAAAAAnT8AAAAAAMC0vwAAAAAAgKa/AAAAAAAAm78AAAAAAIChPwAAAAAAgLa/AAAAAAAAeL8AAAAAAACKPwAAAAAAALQ/AAAAAACAv78AAAAAAMC1vwAAAAAAAKy/AAAAAAAAkz8AAAAAAKDBvwAAAAAAAKS/AAAAAAAAir8AAAAAAACxPwAAAAAAwLi/AAAAAAAAeL8AAAAAAACIPwAAAAAAALU/AAAAAAAAlj8AAAAAAECzPwAAAAAAALQ/AAAAAABAwD8AAAAAAACRPwAAAAAAgLE/AAAAAABAsj8AAAAAAEC/PwAAAAAAgK0/AAAAAABAuT8AAAAAAIC4PwAAAAAAoME/AAAAAAAAnb8AAAAAAACXvwAAAAAAAI6/AAAAAAAApz8AAAAAAEDAvwAAAAAAALO/AAAAAACAq78AAAAAAACTPwAAAAAAQMu/AAAAAAAAw78AAAAAAAC7vwAAAAAAAI6/AAAAAABA1L8AAAAAAADNvwAAAAAAAMS/AAAAAAAAqL8AAAAAAGDGvwAAAAAAwLm/AAAAAADAsL8AAAAAAACMPwAAAAAAIMC/AAAAAAAAtL8AAAAAAICpvwAAAAAAAJU/AAAAAACApr8AAAAAAACdPwAAAAAAAKY/AAAAAABAuj8AAAAAAAClvwAAAAAAAJe/AAAAAAAAkr8AAAAAAACpPwAAAAAAwL+/AAAAAAAAnL8AAAAAAABgPwAAAAAAQLQ/AAAAAAAAaL8AAAAAAICuPwAAAAAAgLA/AAAAAABAvj8AAAAAAICkvwAAAAAAAJ0/AAAAAACApD8AAAAAAEC5PwAAAAAAgKg/AAAAAAAAuD8AAAAAAAC4PwAAAAAAoME/AAAAAAAApj8AAAAAAMC2PwAAAAAAQLY/AAAAAABAwT8AAAAAAMCxPwAAAAAAALw/AAAAAAAAuj8AAAAAAIDCPwAAAAAAwLO/AAAAAACArr8AAAAAAICmvwAAAAAAAJw/AAAAAADAvL8AAAAAAACwvwAAAAAAgKS/AAAAAAAAnD8AAAAAAMC0vwAAAAAAAKi/AAAAAAAAnb8AAAAAAACgPwAAAAAAQLa/AAAAAAAAfL8AAAAAAACGPwAAAAAAwLM/AAAAAACAv78AAAAAAMC1vwAAAAAAgK2/AAAAAAAAkj8AAAAAAKDDvwAAAAAAgKi/AAAAAAAAlr8AAAAAAACuPwAAAAAAAMC/AAAAAAAAmr8AAAAAAAB4vwAAAAAAgLE/AAAAAAAAdD8AAAAAAECwPwAAAAAAgLE/AAAAAABAvj8AAAAAAABQPwAAAAAAAK0/AAAAAABAsD8AAAAAAEC9PwAAAAAAgK0/AAAAAADAuD8AAAAAAIC4PwAAAAAAgME/AAAAAADAtb8AAAAAAACxvwAAAAAAgKi/AAAAAAAAmD8AAAAAAMC+vwAAAAAAgLC/AAAAAACApr8AAAAAAACaPwAAAAAAALa/AAAAAAAAqb8AAAAAAACgvwAAAAAAAKE/AAAAAADAt78AAAAAAACEvwAAAAAAAHg/AAAAAAAAsz8AAAAAAODAvwAAAAAAQLi/AAAAAAAAr78AAAAAAACMPwAAAAAAYMO/AAAAAAAAqr8AAAAAAACTvwAAAAAAgK0/AAAAAADAvL8AAAAAAACUvwAAAAAAAFA/AAAAAAAAsz8AAAAAAACQPwAAAAAAwLI/AAAAAAAAsz8AAAAAAMC/PwAAAAAAAIg/AAAAAACAsT8AAAAAAICxPwAAAAAAwL4/AAAAAAAArj8AAAAAAMC5PwAAAAAAQLg/AAAAAACAwT8AAAAAAMC1vwAAAAAAALG/AAAAAACAqL8AAAAAAACVPwAAAAAAQL2/AAAAAACAsL8AAAAAAICmvwAAAAAAAJg/AAAAAABAtr8AAAAAAICovwAAAAAAgKG/AAAAAAAAoD8AAAAAAEC4vwAAAAAAAIK/AAAAAAAAeD8AAAAAAECzPwAAAAAAoMC/AAAAAADAtr8AAAAAAACuvwAAAAAAAJE/AAAAAABgwr8AAAAAAICmvwAAAAAAAJG/AAAAAACArj8AAAAAAIC8vwAAAAAAAJW/AAAAAAAAaD8AAAAAAICyPwAAAAAAAIQ/AAAAAABAsT8AAAAAAECyPwAAAAAAgL4/AAAAAAAAcD8AAAAAAACvPwAAAAAAgLA/AAAAAACAvT8AAAAAAACqPwAAAAAAQLg/AAAAAADAtj8AAAAAAADBPwAAAAAAAKO/AAAAAAAAmb8AAAAAAACSvwAAAAAAgKU/AAAAAADAwL8AAAAAAAC0vwAAAAAAAKy/AAAAAAAAkD8AAAAAAIDLvwAAAAAAgMO/AAAAAACAu78AAAAAAACSvwAAAAAAQNS/AAAAAAAgzb8AAAAAAADEvwAAAAAAAKi/AAAAAAAgx78AAAAAAAC6vwAAAAAAALG/AAAAAAAAjD8AAAAAAEDBvwAAAAAAQLS/AAAAAACArL8AAAAAAACVPwAAAAAAgKi/AAAAAAAAmz8AAAAAAICkPwAAAAAAQLo/AAAAAACAp78AAAAAAACbvwAAAAAAAJG/AAAAAACApj8AAAAAAIC/vwAAAAAAAJ2/AAAAAAAAeD8AAAAAAAC0PwAAAAAAAGC/AAAAAAAArj8AAAAAAICwPwAAAAAAAL4/AAAAAAAApL8AAAAAAACfPwAAAAAAgKM/AAAAAACAuT8AAAAAAACUPwAAAAAAALM/AAAAAACAsz8AAAAAAADAPwAAAAAAAIw/AAAAAADAsT8AAAAAAECyPwAAAAAAwL4/AAAAAAAArj8AAAAAAMC5PwAAAAAAQLk/AAAAAADAwT8AAAAAAIC1vwAAAAAAwLC/AAAAAACAp78AAAAAAACXPwAAAAAAQL2/AAAAAABAsL8AAAAAAIClvwAAAAAAAJw/AAAAAADAtb8AAAAAAICnvwAAAAAAgKC/AAAAAACAoD8AAAAAAEC3vwAAAAAAAHy/AAAAAAAAgD8AAAAAAEC0PwAAAAAA4MC/AAAAAABAt78AAAAAAACvvwAAAAAAAJE/AAAAAADgwb8AAAAAAICkvwAAAAAAAIq/AAAAAABAsD8AAAAAAMC5vwAAAAAAAIi/AAAAAAAAhD8AAAAAAICzPwAAAAAAAHg/AAAAAABAsD8AAAAAAICxPwAAAAAAgL4/AAAAAAAAcD8AAAAAAACvPwAAAAAAwLA/AAAAAADAvT8AAAAAAACtPwAAAAAAALk/AAAAAADAtz8AAAAAAGDBPwAAAAAAALa/AAAAAAAAsb8AAAAAAICpvwAAAAAAAJU/AAAAAAAAvr8AAAAAAMCwvwAAAAAAAKa/AAAAAAAAmT8AAAAAAEC1vwAAAAAAAKm/AAAAAACAoL8AAAAAAACePwAAAAAAwLe/AAAAAAAAhr8AAAAAAAB0PwAAAAAAALM/AAAAAAAAwb8AAAAAAIC3vwAAAAAAALC/AAAAAAAAkD8AAAAAAODEvwAAAAAAgKu/AAAAAAAAmr8AAAAAAICsPwAAAAAAwL2/AAAAAAAAmL8AAAAAAABQPwAAAAAAALI/AAAAAAAAgj8AAAAAAMCwPwAAAAAAwLE/AAAAAABAvj8AAAAAAACCPwAAAAAAALA/AAAAAACAsT8AAAAAAMC9PwAAAAAAgKw/AAAAAADAuD8AAAAAAAC4PwAAAAAAQME/AAAAAAAAt78AAAAAAECxvwAAAAAAgKq/AAAAAAAAlT8AAAAAAMC+vwAAAAAAQLC/AAAAAACAp78AAAAAAACZPwAAAAAAgLa/AAAAAAAAqb8AAAAAAACivwAAAAAAAJ0/AAAAAABAt78AAAAAAACEvwAAAAAAAHg/AAAAAAAAsz8AAAAAAODAvwAAAAAAgLi/AAAAAAAAsL8AAAAAAACKPwAAAAAAIMK/AAAAAAAAp78AAAAAAACRvwAAAAAAgK8/AAAAAABAvb8AAAAAAACWvwAAAAAAAFC/AAAAAADAsj8AAAAAAAB0vwAAAAAAAK0/AAAAAACArz8AAAAAAMC9PwAAAAAAAIC/AAAAAACAqj8AAAAAAICtPwAAAAAAgLw/AAAAAACApz8AAAAAAIC2PwAAAAAAALY/AAAAAACAwD8AAAAAAACkvwAAAAAAAJ+/AAAAAAAAkr8AAAAAAICjPwAAAAAAQMG/AAAAAABAtL8AAAAAAACuvwAAAAAAAIw/AAAAAAAgzL8AAAAAAEDDvwAAAAAAwLy/AAAAAAAAlL8AAAAAALDUvwAAAAAAgM2/AAAAAACAxL8AAAAAAICovwAAAAAAAMe/AAAAAAAAur8AAAAAAICxvwAAAAAAAIY/AAAAAAAAwb8AAAAAAIC0vwAAAAAAgKu/AAAAAAAAkj8AAAAAAACnvwAAAAAAAJk/AAAAAAAApT8AAAAAAEC6PwAAAAAAgKe/AAAAAAAAnb8AAAAAAACVvwAAAAAAgKc/AAAAAACAwL8AAAAAAACevwAAAAAAAGg/AAAAAADAsz8AAAAAAACCvwAAAAAAAKw/AAAAAACArj8AAAAAAMC9PwAAAAAAgKm/AAAAAAAAlz8AAAAAAIChPwAAAAAAwLg/AAAAAACAoj8AAAAAAEC2PwAAAAAAgLY/AAAAAADgwD8AAAAAAICgPwAAAAAAgLQ/AAAAAACAtD8AAAAAAGDAPwAAAAAAALE/AAAAAAAAuz8AAAAAAEC5PwAAAAAAAMI/AAAAAADAtb8AAAAAAECwvwAAAAAAAKm/AAAAAAAAmD8AAAAAAIC9vwAAAAAAAK+/AAAAAAAAp78AAAAAAACaPwAAAAAAwLW/AAAAAACAqL8AAAAAAAChvwAAAAAAAJ8/AAAAAACAt78AAAAAAACGvwAAAAAAAHw/AAAAAAAAsz8AAAAAAMC+vwAAAAAAALa/AAAAAAAArb8AAAAAAACSPwAAAAAA4MK/AAAAAACAqL8AAAAAAACTvwAAAAAAgK4/AAAAAAAAvL8AAAAAAACRvwAAAAAAAHA/AAAAAACAsz8AAAAAAAB8PwAAAAAAwLA/AAAAAABAsT8AAAAAAIC+PwAAAAAAAFA/AAAAAAAArj8AAAAAAECwPwAAAAAAQL0/AAAAAACAqz8AAAAAAMC4PwAAAAAAgLc/AAAAAAAgwT8AAAAAAAC2vwAAAAAAgLG/AAAAAAAAqb8AAAAAAACUPwAAAAAAgL6/AAAAAADAsL8AAAAAAACovwAAAAAAAJc/AAAAAACAtr8AAAAAAACpvwAAAAAAAKO/AAAAAAAAnj8AAAAAAMC4vwAAAAAAAIq/AAAAAAAAYD8AAAAAAECyPwAAAAAA4MG/AAAAAACAub8AAAAAAECxvwAAAAAAAIQ/AAAAAABAwr8AAAAAAACovwAAAAAAAJG/AAAAAACArj8AAAAAAAC6vwAAAAAAAJC/AAAAAAAAcD8AAAAAAECzPwAAAAAAAHw/AAAAAABAsD8AAAAAAACxPwAAAAAAQL4/AAAAAAAAaL8AAAAAAICsPwAAAAAAAK8/AAAAAABAvT8AAAAAAACpPwAAAAAAwLc/AAAAAABAtj8AAAAAAODAPwAAAAAAQLe/AAAAAADAsr8AAAAAAICrvwAAAAAAAJE/AAAAAACAvr8AAAAAAMCxvwAAAAAAAKi/AAAAAAAAlT8AAAAAAEC2vwAAAAAAgKq/AAAAAAAAor8AAAAAAACcPwAAAAAAQLi/AAAAAAAAiL8AAAAAAABoPwAAAAAAQLI/AAAAAACgwb8AAAAAAMC4vwAAAAAAgLG/AAAAAAAAhj8AAAAAAEDCvwAAAAAAgKa/AAAAAAAAkb8AAAAAAICuPwAAAAAAAL2/AAAAAAAAmL8AAAAAAABQvwAAAAAAALI/AAAAAAAAhD8AAAAAAACwPwAAAAAAgLE/AAAAAAAAvj8AAAAAAAB8PwAAAAAAgK4/AAAAAABAsD8AAAAAAEC9PwAAAAAAAKk/AAAAAACAtz8AAAAAAEC2PwAAAAAAwMA/AAAAAACAo78AAAAAAACdvwAAAAAAAJe/AAAAAAAApT8AAAAAAEDBvwAAAAAAgLS/AAAAAACAr78AAAAAAACKPwAAAAAAwMu/AAAAAADAw78AAAAAAIC8vwAAAAAAAJW/AAAAAADAz78AAAAAACDHvwAAAAAAgL+/AAAAAAAAnr8AAAAAAPDRvwAAAAAAYMi/AAAAAAAgwb8AAAAAAAChvwAAAAAAkNK/AAAAAACAx78AAAAAAKDAvwAAAAAAAJ2/AAAAAABgxb8AAAAAAICrvwAAAAAAAJK/AAAAAACAsD8AAAAAAICmPwAAAAAAALk/AAAAAAAAuT8AAAAAAIDCPwAAAAAAgKQ/AAAAAAAAkz8AAAAAAACQPwAAAAAAgK8/AAAAAADAtL8AAAAAAICnvwAAAAAAAJy/AAAAAAAAoz8AAAAAAODCvwAAAAAAQLe/AAAAAABAsL8AAAAAAACSPwAAAAAAgMG/AAAAAACAtL8AAAAAAICsvwAAAAAAAJU/AAAAAAAAxr8AAAAAAEC7vwAAAAAAgLS/AAAAAAAAdD8AAAAAAGDRvwAAAAAA4Mm/AAAAAAAgwb8AAAAAAACbvwAAAAAAgMe/AAAAAACAsL8AAAAAAACXvwAAAAAAgK8/AAAAAACAxb8AAAAAAIC9vwAAAAAAwLK/AAAAAAAAgj8AAAAAAMC6vwAAAAAAAIK/AAAAAAAAkT8AAAAAAMC2PwAAAAAAAHi/AAAAAAAAsD8AAAAAAACxPwAAAAAAAMA/AAAAAAAAoT8AAAAAAAC3PwAAAAAAwLU/AAAAAACgwT8AAAAAAEC0PwAAAAAAQL4/AAAAAACAuz8AAAAAAIDDPwAAAAAAgKk/AAAAAAAAuD8AAAAAAMC2PwAAAAAAYME/AAAAAAAAhD8AAAAAAICwPwAAAAAAgLA/AAAAAACAvj8AAAAAAACzvwAAAAAAAK2/AAAAAAAAqL8AAAAAAACZPwAAAAAAgM6/AAAAAABAxL8AAAAAAMC+vwAAAAAAAJe/AAAAAABgzr8AAAAAAIDCvwAAAAAAALy/AAAAAAAAjr8AAAAAAIDKvwAAAAAAwLO/AAAAAAAAo78AAAAAAICqPwAAAAAAgKm/AAAAAAAAnT8AAAAAAIClPwAAAAAAQLo/AAAAAAAAmb8AAAAAAACoPwAAAAAAgKw/AAAAAADAvD8AAAAAAACWPwAAAAAAALQ/AAAAAACAsz8AAAAAAIDAPwAAAAAAAHy/AAAAAACArD8AAAAAAACuPwAAAAAAgL0/AAAAAABAtr8AAAAAAICvvwAAAAAAgKu/AAAAAAAAlD8AAAAAAEDPvwAAAAAAwMS/AAAAAAAAv78AAAAAAACbvwAAAAAAgM6/AAAAAABAwr8AAAAAAAC6vwAAAAAAAIK/AAAAAADgzL8AAAAAAAC3vwAAAAAAgKa/AAAAAACAqD8AAAAAAICsvwAAAAAAAJw/AAAAAAAApD8AAAAAAIC7PwAAAAAAAIa/AAAAAACArT8AAAAAAECwPwAAAAAAwL4/AAAAAAAAjj8AAAAAAECzPwAAAAAAQLM/AAAAAABgwD8AAAAAAACOvwAAAAAAgKo/AAAAAACArT8AAAAAAIC9PwAAAAAAwLe/AAAAAAAAsL8AAAAAAACnvwAAAAAAAJw/AAAAAABg0b8AAAAAACDGvwAAAAAAwL+/AAAAAAAAmb8AAAAAABDVvwAAAAAAYMq/AAAAAAAgwr8AAAAAAICgvwAAAAAAcNG/AAAAAACgxL8AAAAAAAC7vwAAAAAAAHC/AAAAAABAyb8AAAAAAAC8vwAAAAAAQLO/AAAAAAAAij8AAAAAAODOvwAAAAAAgMa/AAAAAADAu78AAAAAAAB0vwAAAAAAIMS/AAAAAACApL8AAAAAAABQvwAAAAAAwLQ/AAAAAABAtL8AAAAAAACEPwAAAAAAgKU/AAAAAADAvD8AAAAAAICtPwAAAAAAQLw/AAAAAACAvD8AAAAAAGDEPwAAAAAAALc/AAAAAADAwD8AAAAAAMC/PwAAAAAAYMU/AAAAAABAsT8AAAAAAEC9PwAAAAAAgLs/AAAAAACgwz8AAAAAAICivwAAAAAAAJW/AAAAAAAAir8AAAAAAICrPwAAAAAAoMK/AAAAAACAtb8AAAAAAECwvwAAAAAAAI4/AAAAAABAvr8AAAAAAACRvwAAAAAAAJA/AAAAAAAAtz8AAAAAAIClPwAAAAAAALk/AAAAAACAuD8AAAAAAKDCPwAAAAAAwLo/AAAAAABAwj8AAAAAAADBPwAAAAAAAMY/AAAAAABgwT8AAAAAAEDFPwAAAAAAgMM/AAAAAADAxz8AAAAAAICkPwAAAAAAgKI/AAAAAAAAnz8AAAAAAIC0PwAAAAAAQL2/AAAAAAAAsr8AAAAAAICovwAAAAAAAJk/AAAAAACAvb8AAAAAAICuvwAAAAAAAKO/AAAAAAAAoj8AAAAAAEC3vwAAAAAAAKe/AAAAAAAAnb8AAAAAAACkPwAAAAAAgKW/AAAAAACAoD8AAAAAAACnPwAAAAAAgLs/AAAAAAAAkb8AAAAAAAB8vwAAAAAAAGC/AAAAAAAArT8AAAAAAACTvwAAAAAAAKk/AAAAAACArD8AAAAAAAC9PwAAAAAAgLU/AAAAAABAvz8AAAAAAMC9PwAAAAAAwMM/AAAAAACAoD8AAAAAAACUPwAAAAAAAIg/AAAAAAAArT8AAAAAAIC5vwAAAAAAwLC/AAAAAAAAqr8AAAAAAACUPwAAAAAAQLy/AAAAAACArb8AAAAAAACjvwAAAAAAgKE/AAAAAABAs78AAAAAAICgvwAAAAAAAJa/AAAAAACApD8AAAAAAEC4vwAAAAAAAKq/AAAAAACAo78AAAAAAACgPwAAAAAAgLC/AAAAAAAAjD8AAAAAAACfPwAAAAAAQLg/AAAAAACArb8AAAAAAICkvwAAAAAAAKC/AAAAAACAoj8AAAAAAKDBvwAAAAAAgKO/AAAAAAAAgr8AAAAAAECyPwAAAAAAoMu/AAAAAABgw78AAAAAAMC6vwAAAAAAAIC/AAAAAABgw78AAAAAAMC0vwAAAAAAAKu/AAAAAAAAmj8AAAAAAEC5vwAAAAAAgKm/AAAAAACAoL8AAAAAAACjPwAAAAAAAKW/AAAAAACAoD8AAAAAAICnPwAAAAAAgLs/AAAAAAAAiL8AAAAAAABgvwAAAAAAAFA/AAAAAAAArj8AAAAAAACMvwAAAAAAAKs/AAAAAACArT8AAAAAAMC9PwAAAAAAALU/AAAAAABAvz8AAAAAAEC9PwAAAAAAAMQ/AAAAAAAAnT8AAAAAAACUPwAAAAAAAIA/AAAAAACArT8AAAAAAAC5vwAAAAAAALC/AAAAAAAAp78AAAAAAACZPwAAAAAAgLq/AAAAAAAAsL8AAAAAAACkvwAAAAAAAJ0/AAAAAADAt78AAAAAAACovwAAAAAAAJ+/AAAAAACAoj8AAAAAAEC4vwAAAAAAAKy/AAAAAAAApL8AAAAAAACfPwAAAAAAwLW/AAAAAAAAYD8AAAAAAACRPwAAAAAAwLY/AAAAAAAAvr8AAAAAAEC0vwAAAAAAgKu/AAAAAAAAmD8AAAAAAGDSvwAAAAAAwMe/AAAAAADAv78AAAAAAACZvwAAAAAA4MO/AAAAAACAtb8AAAAAAACqvwAAAAAAAJw/AAAAAAAAuL8AAAAAAACnvwAAAAAAAJq/AAAAAAAApj8AAAAAAIClvwAAAAAAAKE/AAAAAACApz8AAAAAAIC8PwAAAAAAAJO/AAAAAAAAcL8AAAAAAAAAAAAAAAAAgK8/AAAAAAAAlr8AAAAAAICoPwAAAAAAgK4/AAAAAABAvj8AAAAAAIC0PwAAAAAAQL8/AAAAAABAvT8AAAAAACDEPwAAAAAAAJk/AAAAAAAAjj8AAAAAAACEPwAAAAAAAK8/AAAAAAAAtL8AAAAAAICmvwAAAAAAAJ6/AAAAAACAoz8AAAAAAMC4vwAAAAAAAKq/AAAAAACAob8AAAAAAACiPwAAAAAAwLW/AAAAAACAor8AAAAAAACXvwAAAAAAgKc/AAAAAAAArL8AAAAAAACYPwAAAAAAAKM/AAAAAAAAuj8AAAAAAACsvwAAAAAAgKG/AAAAAAAAmr8AAAAAAAClPwAAAAAA4MK/AAAAAAAApr8AAAAAAACEvwAAAAAAgLE/AAAAAAAgyr8AAAAAACDCvwAAAAAAgLi/AAAAAAAAYL8AAAAAAADCvwAAAAAAQLK/AAAAAAAAqL8AAAAAAACgPwAAAAAAwLi/AAAAAAAApb8AAAAAAACavwAAAAAAgKY/AAAAAAAAor8AAAAAAACkPwAAAAAAgKk/AAAAAADAvD8AAAAAAACQvwAAAAAAAHS/AAAAAAAAUD8AAAAAAACwPwAAAAAAAJO/AAAAAAAAqT8AAAAAAICuPwAAAAAAQL4/AAAAAAAAtD8AAAAAAIC/PwAAAAAAgL0/AAAAAABAxD8AAAAAAACZPwAAAAAAAJE/AAAAAAAAhD8AAAAAAICvPwAAAAAAgLW/AAAAAAAApb8AAAAAAACdvwAAAAAAAKU/AAAAAABgwL8AAAAAAICyvwAAAAAAAKe/AAAAAAAAnT8AAAAAAIC+vwAAAAAAwLC/AAAAAAAAqL8AAAAAAACYPwAAAAAAQLC/AAAAAAAAjj8AAAAAAICiPwAAAAAAgLk/AAAAAACAqr8AAAAAAACivwAAAAAAAKC/AAAAAACAoT8AAAAAAMC5vwAAAAAAAHy/AAAAAAAAjD8AAAAAAIC2PwAAAAAAgK+/AAAAAAAAlz8AAAAAAACiPwAAAAAAQLo/AAAAAADAwb8AAAAAAEC5vwAAAAAAwLC/AAAAAAAAjj8AAAAAAADAvwAAAAAAgLG/AAAAAAAApr8AAAAAAACgPwAAAAAAgLO/AAAAAAAAor8AAAAAAACKvwAAAAAAgKo/AAAAAADAtL8AAAAAAABgPwAAAAAAAJY/AAAAAACAtz8AAAAAAAC7vwAAAAAAQLO/AAAAAACArb8AAAAAAACVPwAAAAAA4MC/AAAAAAAAnL8AAAAAAAAAAAAAAAAAgLQ/AAAAAAAAfL8AAAAAAACwPwAAAAAAwLA/AAAAAACAvz8AAAAAAICoPwAAAAAAwLg/AAAAAABAuT8AAAAAAKDCPwAAAAAAQLc/AAAAAAAgwD8AAAAAAAC/PwAAAAAAQMQ/AAAAAACAvz8AAAAAACDDPwAAAAAAoME/AAAAAAAAxj8AAAAAAKDBPwAAAAAA4MQ/AAAAAADgwj8AAAAAAODGPwAAAAAAAKE/AAAAAAAAnT8AAAAAAACWPwAAAAAAQLI/AAAAAAAgwL8AAAAAAEC0vwAAAAAAAK+/AAAAAAAAjj8AAAAAAADAvwAAAAAAQLK/AAAAAACAqL8AAAAAAACWPwAAAAAAQLm/AAAAAAAArb8AAAAAAICjvwAAAAAAAJ0/AAAAAAAAq78AAAAAAACUPwAAAAAAgKA/AAAAAADAuD8AAAAAAACavwAAAAAAAJC/AAAAAAAAir8AAAAAAICoPwAAAAAAAJy/AAAAAAAApD8AAAAAAICnPwAAAAAAgLs/AAAAAABAsT8AAAAAAIC8PwAAAAAAQLo/AAAAAABgwj8AAAAAAACIPwAAAAAAAGA/AAAAAAAAdL8AAAAAAIClPwAAAAAAwLu/AAAAAADAsr8AAAAAAACtvwAAAAAAAIY/AAAAAACAvr8AAAAAAECxvwAAAAAAAKe/AAAAAAAAmj8AAAAAAEC1vwAAAAAAAKW/AAAAAAAAn78AAAAAAIChPwAAAAAAQLq/AAAAAACArb8AAAAAAICnvwAAAAAAAJg/AAAAAAAAsr8AAAAAAACAPwAAAAAAAJc/AAAAAACAtj8AAAAAAACxvwAAAAAAgKi/AAAAAAAApL8AAAAAAACdPwAAAAAAYMO/AAAAAAAAqr8AAAAAAACSvw==","dtype":"float64","shape":[5000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1047","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1045","type":"BoxAnnotation"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{},"id":"1047","type":"UnionRenderers"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1007","type":"LinearScale"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"c2a142af-9293-4469-bd5c-34ece671af3f","roots":{"1002":"91d2e579-ec92-4806-bc42-cf662c7337a6"}}];
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

Using the Trace Data
~~~~~~~~~~~~~~~~~~~~

Now that we have some traces, let's look at what we've actually
recorded. Looking at the earlier parts of the script, we can see that
the trace data is in ``trace_array``, while ``textin_array`` stores what
we sent to our target to be encrypted. For now, let's get some basic
information (the total number of traces, as well as the number of sample
points in each trace) about the traces, since we'll need that later:


**In [9]:**

.. code:: ipython3

    numtraces = np.shape(trace_array)[0] #total number of traces
    numpoints = np.shape(trace_array)[1] #samples per trace

For the analysis, we'll need to loop over every byte in the key we want
to attack, as well as every trace:

.. code:: python

    for bnum in range(0, 16):
        for tnum in range(0, numtraces):
            pass

Though we didn't loop over them, note that each trace is made up of a
bunch of sample points. Let's take a closer look at AES so that we can
replace that ``pass`` with some actual code.

Calculating Correlation
~~~~~~~~~~~~~~~~~~~~~~~

Now that we have some power traces of our target that we can use, we can
move on to the next steps of our attack. Looking way back to how AES
works, remember we are effectively attemping to target the position at
the bottom of this figure:

.. figure:: https://wiki.newae.com/images/7/71/Sbox_cpa_detail.png
   :alt: title

   title

The objective is thus to determine the output of the S-Box, where the
S-Box is defined as follows:


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

Thus we need to write a function taking a single byte of input, a single
byte of the guessed key, and return the output of the S-Box:


**In [11]:**

.. code:: ipython3

    def intermediate(pt, keyguess):
        return sbox[pt ^ keyguess]

Finally, remember we want the Hamming Weight of the guess. Our
assumption is that the system is leaking the Hamming Weight of the
output of that S-Box. As a dumb solution, we could first convert every
number to binary and count the 1's:

.. code:: python

    >>> bin(0x1F)
    '0b11111'
    >>> bin(0x1F).count('1')
    5

This will ultimately be fairly slow. Instead we make a lookup table
using this idea:


**In [12]:**

.. code:: ipython3

    HW = [bin(n).count("1") for n in range(0, 256)]

Performing the Check
~~~~~~~~~~~~~~~~~~~~

Remember the objective is to calculate the following:

.. math:: r_{i,j} = \frac{\sum_{d=1}^{D}[(h_{d,i} - \bar{h_i})(t_{d,j}-\bar{t_j})]}{\sqrt{\sum_{d=1}^D(h_{d,i}-\bar{h_i})^2\sum_{d=1}^D(t_{d,j}-\bar{t_j})^2}}

Where

+----------------+-----------------------+-------------------------------+
| **Equation**   | **Python Variable**   | **Value**                     |
+================+=======================+===============================+
| d              | tnum                  | trace number                  |
+----------------+-----------------------+-------------------------------+
| i              | kguess                | subkey guess                  |
+----------------+-----------------------+-------------------------------+
| j              | j index trace point   | sample point in trace         |
+----------------+-----------------------+-------------------------------+
| h              | hypint                | guess for power consumption   |
+----------------+-----------------------+-------------------------------+
| t              | traces                | traces                        |
+----------------+-----------------------+-------------------------------+

It can be noticed there is effectively three sums, all sums are done
over all traces. For this initial implementation we'll be explicitly
calculating some of these sums, although it's faster to use NumPy to
calculate across large arrays. We'll convert those three summations into
variables, turning the equation into this format:

.. math:: r_{i,j}=\frac{sumnum}{\sqrt{snumden1 * sumden2}}

Where:

.. math:: sumnum = \sum_{d=1}^{D}[(h_{d,i} - \bar{h_i})(t_{d,j}-\bar{t_j})]

.. math:: sumden1 = \sum_{d=1}^D(h_{d,i}-\bar{h_i})^2

.. math:: sumden2 = \sum_{d=1}^D(t_{d,j}-\bar{t_j})^2

Looking at this, we can see that we'll need :math:`\bar{h_i}` and
:math:`\bar{t_j}`, so let's start by building some code that will give
us those. Looking at the CPA tutorial, we can see that :math:`h_{d,i}`
is just our guess for the power consumption in trace :math:`d` for
subkey :math:`i`. We can get that easily using the ``HW`` array and
``intermediate()`` function we defined earlier:

.. code:: python

    for bnum in range(0, 16):
        cpaoutput = [0]*256
        maxcpa = [0]*256
        for kguess in range(0, 256):
            hyp = np.zeros(numtraces)
            for tnum in range(0, numtraces):
                hyp[tnum] = HW[intermediate(pt[tnum][bnum], kguess)]

Now we can get :math:`\bar{h_i}`:

.. code:: python

    meanh = np.mean(hyp, dtype=np.float64)

and :math:`\bar{t_j}` is just the mean of all of our traces:

.. code:: python

    meant = np.mean(traces, axis=0, dtype=np.float64)

Next, let's move on to calculating the whole sums using :math:`h_{d,i}`
and :math:`t_{d,j}` and the values we just calculated:

.. code:: python

    #For each trace, do the following
    for tnum in range(numtraces):
        hdiff = (hyp[tnum] - meanh)
        tdiff = traces[tnum,:] - meant

        sumnum = sumnum + (hdiff*tdiff)
        sumden1 = sumden1 + hdiff*hdiff 
        sumden2 = sumden2 + tdiff*tdiff

We can now get the correlation for each of our subkey guesses, which
we'll call ``cpaoutput[]``:

.. code:: python

    cpaoutput[kguess] = sumnum / np.sqrt( sumden1 * sumden2 )

We're almost done! All that's left is to use that correlation to figure
out which subkey best matches our power traces. First off, we only care
about absolute value of the correlation (that there is a linear
correlation), not sign. Additionally, though this didn't factor into our
correlation calculation, remember that each trace was actually made up
of a bunch of sample points. This means that what we actually have is
the correlation of each subkey guess to each sample point. Typically
only a few points in the trace are correlating, and it's the maximum
across the entire trace we are concerned with, so we can pick our
correlation for each subkey by:

.. code:: python

    maxcpa[kguess] = max(abs(cpaoutput[kguess]))

Finally, we can find the subkey that best matches our data by finding
the one with the biggest correlation:

.. code:: python

    bestguess[bnum] = np.argmax(maxcpa)

Putting it all together:

Finished Script
^^^^^^^^^^^^^^^


**In [13]:**

.. code:: ipython3

    import numpy as np
    from tqdm import tqdm
    
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
    
    def intermediate(pt, keyguess):
        return sbox[pt ^ keyguess]
    
    HW = [bin(n).count("1") for n in range(0, 256)]
    
    numtraces = np.shape(trace_array)[0] #total number of traces
    numpoint = np.shape(trace_array)[1] #samples per trace
    
    pt = textin_array
    knownkey = traces[0].key
    cparefs = [0] * 16
    bestguess = [0]*16
    
    for bnum in tqdm(range(0, 16), desc='Attacking subkeys'):
        cpaoutput = [0] * 256
        maxcpa = [0] * 256
        for kguess in range(0, 256):
    
            # Initialize arrays &amp; variables to zero
            sumnum = np.zeros(numpoint)
            sumden1 = np.zeros(numpoint)
            sumden2 = np.zeros(numpoint)
    
            hyp = np.zeros(numtraces)
            for tnum in range(0, numtraces):
                hyp[tnum] = HW[intermediate(pt[tnum][bnum], kguess)]
    
            # Mean of hypothesis
            meanh = np.mean(hyp, dtype=np.float64)
    
            # Mean of all points in trace
            meant = np.mean(trace_array, axis=0, dtype=np.float64)
    
            # For each trace, do the following
            for tnum in range(0, numtraces):
                hdiff = (hyp[tnum] - meanh)
                tdiff = trace_array[tnum, :] - meant
    
                sumnum = sumnum + (hdiff * tdiff)
                sumden1 = sumden1 + hdiff * hdiff
                sumden2 = sumden2 + tdiff * tdiff
    
            cpaoutput[kguess] = sumnum / np.sqrt(sumden1 * sumden2)
            maxcpa[kguess] = max(abs(cpaoutput[kguess]))
    
        bestguess[bnum] = np.argmax(maxcpa)
        cparefs[bnum] = np.argsort(maxcpa)[::-1]
    
    print("Best Key Guess: ", end="")
    for b in bestguess: print("%02x " % b, end="")


**Out [13]:**



.. parsed-literal::

    Attacking subkeys: 100%|██████████| 16/16 [00:22<00:00,  1.41s/it]
    




.. parsed-literal::

    Best Key Guess: 2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c 


Checking Our Guess
~~~~~~~~~~~~~~~~~~

Since we were the ones who decided the key (and kept track of it in
knownkey), we can check if our guess was right:


**In [14]:**

.. code:: ipython3

    for b in knownkey: print("%02x "%b, end="")
    print("\n")
    if all([known_byte == guess_byte for known_byte, guess_byte in zip(knownkey, bestguess)]):
        print("Guess was right")
    else:
        print("Guess was wrong")


**Out [14]:**



.. parsed-literal::

    2b 7e 15 16 28 ae d2 a6 ab f7 15 88 09 cf 4f 3c 
    
    Guess was right
    


We can do even better by calculating the Partial Guessing Entropy.

The Partial Guessing Entropy (PGE) is a useful metric of where the
correct answer is ranked. This requires us to know the actual encryption
key used during operation, which we do know!

Certain attacks will use different keys during the acqusition period,
meaning the a list of all the known keys is required since there isn't a
constant key. In our case, we already have access in ``knownkey``.

Previously, we simply printed the maximum output for each subkey as
follows:

.. code:: python

    #Find maximum value of key
    bestguess[bnum] = np.argmax(maxcpa)

To sort the list of CPA output's, we'll use the ``argsort()`` function
from NumPy. This will output a list where the first element is the index
of the lowest value, next element is the index of the next-highest
element, etc. Because in our input list the ``maxcpa`` vector's indexes
correspond to the key guess, this allows us to know where the keys are.
We reverse that sorted list to put the first element as the maximum CPA
output:

.. code:: python

    cparefs = np.argsort(maxcpa)[::-1]

Finally, the Partial Guessing Entropy is simply the location of the
known correct key byte inside that array. We can find that with the
``.index()`` function:

.. code:: python

    print cparefs.index(0x2B)

Where the correct key should of course come from our ``knownkey``
variable instead of being hard-coded. Pulling it all together:


**In [15]:**

.. code:: ipython3

    pge = [0]*16
    for bnum in range(0, 16):
        #Find maximum value of key
        #Find PGE
        pge[bnum] = list(cparefs[bnum]).index(knownkey[bnum])
        
    print("PGE: ", end="")
    for i in pge:
        print(i, end="")
    print("")


**Out [15]:**



.. parsed-literal::

    PGE: 0000000000000000
    


Improvements
~~~~~~~~~~~~

The implementation of the correlation function runs as a loop over all
traces. Ideally we'd like to implement this as a 'online' calculation;
that is, we can add a trace in, observe the output, add another trace
in, observe the output, etc. When generating plots of the Partial
Guessing Entropy (PGE) vs. number of traces this is greatly preferred,
since otherwise we need to run the loop many times!

We can use an alternate form of the correlation equation, which
explicitly stores sums of the variables. This is easier to perform
online calculation with, since when adding a new trace it's simple to
update these sums. This form of the equation looks like:

.. math:: r_{i,j} = \frac{D\sum_{d=1}^{D}h_{d,i}t_{d,j}-\sum_{d=1}^{D}h_{d,i}\sum_{d=1}^{D}t_{d,j}}{\sqrt{((\sum_{d=1}^Dh_{d,i})^2-D\sum_{d=1}^Dh_{d,i}^2)-((\sum_{d=1}^Dt_{d,j})^2-D\sum_{d=1}^Dh_{d,j}^2)}}

Tests
-----


**In [16]:**

.. code:: ipython3

    assert all([known_byte == guess_byte for known_byte, guess_byte in zip(knownkey, bestguess)]), "Failed to break encryption key\nGot: {}\nExpected: {}\n".format(knownkey, bestguess)


**In [17]:**

.. code:: ipython3

    assert not (np.all(pge)), "PGE not zero for byte: {}".format(pge)


**In [ ]:**

