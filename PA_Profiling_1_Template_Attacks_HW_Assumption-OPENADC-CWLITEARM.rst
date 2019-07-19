
.. raw:: html

   <h1>

Table of Contents

.. raw:: html

   </h1>

.. raw:: html

   <div class="toc">

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

1  Signals, Noise, and Statistics

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

1.1  Noise Distributions

.. raw:: html

   </li>

.. raw:: html

   <li>

1.2  Multivariate Statistics

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

2  Creating the Template

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

2.1  Number of Traces

.. raw:: html

   </li>

.. raw:: html

   <li>

2.2  Points of Interest

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

2.2.1  Picking POIs

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

2.3  Analyzing the Data

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

3  Using the Template

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

3.1  Applying the Template

.. raw:: html

   </li>

.. raw:: html

   <li>

3.2  Combining the Results

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

4  Capturing Power Traces

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

4.1  Capturing Template (Rand Key, Rand Text)

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

5  Building the Templates

.. raw:: html

   <ul class="toc-item">

.. raw:: html

   <li>

5.1  Opening & Grouping Traces

.. raw:: html

   </li>

.. raw:: html

   <li>

5.2  Points of Interest

.. raw:: html

   </li>

.. raw:: html

   <li>

5.3  Generating a Template

.. raw:: html

   </li>

.. raw:: html

   <li>

5.4  Applying the Template

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </li>

.. raw:: html

   <li>

6  Conclusions

.. raw:: html

   </li>

.. raw:: html

   <li>

7  Tests

.. raw:: html

   </li>

.. raw:: html

   </ul>

.. raw:: html

   </div>

Template Attack with Hardware Assumption
========================================

*Template attacks* are a powerful type of side-channel attack. These
attacks are a subset of *profiling attacks*, where an attacker creates a
"profile" of a sensitive device and applies this profile to quickly find
a victim's secret key.

Template attacks require more setup than CPA attacks. To perform a
template attack, the attacker must have access to another copy of the
protected device that they can fully control. Then, they must perform a
great deal of pre-processing to create the template - in practice, this
may take dozens of thousands of power traces. However, the advantages
are that template attacks require a very small number of traces from the
victim to complete the attack. With enough pre-processing, the key may
be able to be recovered from just a single trace.

There are four steps to a template attack: 1. Using a copy of the
protected device, record a large number of power traces using many
different inputs (plaintexts and keys). Ensure that enough traces are
recorded to give us information about each subkey value. 2. Create a
template of the device's operation. This template notes a few "points of
interest" in the power traces and a multivariate distribution of the
power traces at each point. 3. On the victim device, record a small
number of power traces. Use multiple plaintexts. (We have no control
over the secret key, which is fixed.) 4. Apply the template to the
attack traces. For each subkey, track which value is most likely to be
the correct subkey. Continue until the key has been recovered.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'TINYAES128C'
    num_traces = 50
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



Signals, Noise, and Statistics
------------------------------

Before looking at the details of the template attack, it is important to
understand the statistics concepts that are involved. A template is
effectively a multivariate distribution that describes several key
samples in the power traces. This section will describe what a
multivariate distribution is and how it can be used in this context.

Noise Distributions
~~~~~~~~~~~~~~~~~~~

Electrical signals are inherently noisy. Any time we take a voltage
measurement, we don't expect to see a perfect, constant level. For
example, if we attached a multimeter to a 5 V source and took 4
measurements, we might expect to see a data set like (4.95, 5.01, 5.06,
4.98). One way of modelling this voltage source is:

.. math:: \mathbf{X} = X_{actual} + \mathbf{N}

A simple model for these random variables uses a Gaussian distribution
(read: a bell curve). The probability density function (PDF) of a
Gaussian distribution is

.. math:: f(x) = \frac{1}{\sigma \sqrt{2\pi}} e^{-(x - \mu)^2 / 2\sigma^2}

where :math:`\mu` is the mean and :math:`\sigma` is the standard
deviation. For instance, our voltage source might have a mean of 5 and a
standard deviation of 0.5. We can plot this curve:


**In [3]:**

.. code:: ipython3

    mu = 5
    sigma = 0.5

What's this graph all about? This graph shows the *probability density*
of this random variable. Note it does NOT give you an actual
probability, instead you need to integrate to find the probability the
variable is lieing in some range. This is important because the density
function won't always return just < 1, and with multivariate (to be
discussed) the output is frequently larger than one. You can find
probabilities using the CDF, which integrates from negative infinity to
the given random variable value.

For example, what is the chance that we will observe a value in the
range 6.0?


**In [4]:**

.. code:: ipython3

    from scipy import stats
    pval = stats.norm.cdf(6.0, mu, sigma)
    print("%.2f %%"%(pval*100))


**Out [4]:**



.. parsed-literal::

    97.72 %
    


PDFs help us answer a simple question (that will be relevant to us
shortly). Let's say we have the following experiment setup:

We have two batteries and are trying to figure out which one is
connected behind the curtain. There is a *lot* of noise in our
measurement setup and we don't have much time to waste, so can only
measure a few points. The following will simulate a number of
measurements from one or the other battery, but not tell us which one is
which.


**In [5]:**

.. code:: ipython3

    from random import randint
    import numpy as np
    
    noise = 2.0
    
    def do_measure():
        if randint(0,1):
            return (1.5, np.random.normal(1.5, noise))
        else:
            return (3.0, np.random.normal(3.0, noise))
     
    N = 10
    
    measure_array = [0]*N
    answer = [0]*N
    
    for n in range(0, N):
        answer[n], measure_array[n] = do_measure()
        print("%.02f"%measure_array[n])
        


**Out [5]:**



.. parsed-literal::

    6.78
    2.52
    2.34
    3.86
    3.71
    1.55
    1.73
    5.59
    2.89
    2.66
    


Let's see how you stack up to a computer! Try entering your guess of the
measurements here by editing the values of the array to reflect the
printed measurements. Select if you think the source was 1.5V or 3.0V.


**In [6]:**

.. code:: ipython3

    guess = [1.5, 1.5, 3.0, 3.0, 1.5, 1.5, 3.0, 1.5, 3.0, 3.0]

How does the PDF and CDF information help us? We could use the PDF to
provide some information on the liklihood of a specific input. Note this
should be done with another function (remember the PDF is a density
function, not a probability function), but we will use PDF for now as
the template attack to be discussed will continue this method.


**In [7]:**

.. code:: ipython3

    import pandas as pd
    
    results = []*N
    
    for n in range(0, N):
        p15 = stats.norm.pdf(measure_array[n], 1.5, noise)
        p30 = stats.norm.pdf(measure_array[n], 3.0, noise)
    
        if p15 > p30:
            bguess = 1.5
        else:
            bguess = 3.0
            
        results.append([p15, p30, bguess, guess[n], answer[n]])
        
    pd.DataFrame(results, columns=["P|m=1.5", "P|m=3.0", "Best Guess", "Your Guess", "Answer"])


**Out [7]:**



.. raw:: html

    <div class="data_html">
        <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>P|m=1.5</th>
          <th>P|m=3.0</th>
          <th>Best Guess</th>
          <th>Your Guess</th>
          <th>Answer</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>0.006149</td>
          <td>0.033565</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>1</th>
          <td>0.175154</td>
          <td>0.193804</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>2</th>
          <td>0.182572</td>
          <td>0.188948</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>3</th>
          <td>0.099505</td>
          <td>0.181906</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>4</th>
          <td>0.108440</td>
          <td>0.187352</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>5</th>
          <td>0.199408</td>
          <td>0.153390</td>
          <td>1.5</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>6</th>
          <td>0.198187</td>
          <td>0.162912</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>7</th>
          <td>0.024589</td>
          <td>0.086113</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>8</th>
          <td>0.156907</td>
          <td>0.199146</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>9</th>
          <td>0.168528</td>
          <td>0.196631</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
      </tbody>
    </table>
    </div>
    </div>



Multivariate Statistics
~~~~~~~~~~~~~~~~~~~~~~~

The 1-variable Gaussian distribution works well for one measurement.
What if we're working with more than one random variable?

Suppose we're measuring two voltages that have some amount of noise on
them. We'll call them :math:`\mathbf{X}` and :math:`\mathbf{Y}`. As a
first attempt, we could write down a model for :math:`\mathbf{X}` using
a normal distribution and a separate model for :math:`\mathbf{Y}` using
a different distribution. However, this might not always make sense. If
we write two separate distributions, what we're saying is that the two
variables are independent: when :math:`\mathbf{X}` goes up, there's no
guarantee that :math:`\mathbf{Y}` will follow it.

Multivariate distributions let us model multiple random variables that
may or may not be correlated. In a multivariate distribution, instead of
writing down a single variance :math:`\sigma`, we keep track of a whole
matrix of covariances. For example, to model three random variables
(:math:`\mathbf{X}, \mathbf{Y}, \mathbf{Z}`), this matrix would be

.. math::


   \mathbf{\Sigma} = 
   \begin{bmatrix}
   Var(\mathbf{X})             & Cov(\mathbf{X}, \mathbf{Y}) & Cov(\mathbf{X}, \mathbf{Z}) \\
   Cov(\mathbf{Y}, \mathbf{X}) & Var(\mathbf{Y})             & Cov(\mathbf{Y}, \mathbf{Z}) \\
   Cov(\mathbf{Z}, \mathbf{X}) & Cov(\mathbf{Z}, \mathbf{Y}) & Var(\mathbf{Z}) 
   \end{bmatrix}

Also, note that this distribution needs to have a mean for each random
variable:

.. math::


   \mathbf{\mu} = 
   \begin{bmatrix}
   \mu_X \\
   \mu_Y \\
   \mu_Z
   \end{bmatrix}

The PDF of this distribution is more complicated: instead of using a
single number as an argument, it uses a vector with all of the variables
in it (:math:`\mathbf{x} = [x, y, z, \dots]^T`). The equation for
:math:`k` random variables is

.. math::


   f(\mathbf{x})
   = \frac{1}{\sqrt{(2\pi)^k |\mathbf{\Sigma}|}} 
     e^{-(\mathbf{(x - \mu)}^T \mathbf{\Sigma}^{-1} \mathbf{(x - \mu)} / 2}

Don't worry if this looks crazy - the SciPy package in Python will do
all the heavy lifting for us. As with the single-variable distributions,
we're going to use this to find how likely a certain observation is. In
other words, if we put :math:`k` points of our power trace into
:math:`\mathbf{x}` and we find that :math:`f(\mathbf{x})` is very high,
then we've probably found a good guess.

Creating the Template
---------------------

A template is a set of probability distributions that describe what the
power traces look like for many different keys. Effectively, a template
says: "If you're going to use key :math:`k`, your power trace will look
like the distribution :math:`f_k(\mathbf{x})`". We can use this
information to find subtle differences between power traces and to make
very good key guesses for a single power trace.

Number of Traces
~~~~~~~~~~~~~~~~

One of the downsides of template attacks is that they require a great
number of traces to be preprocessed before the attack can begin. This is
mainly for statistical reasons. In order to come up with a good
distribution to model the power traces for *every key*, we need a large
number of traces for *every key*. For example, if we're going to attack
a single subkey of AES-128, then we need to create 256 power consumption
models (one for every number from 0 to 255). In order to get enough data
to make good models, we need tens of thousands of traces.

Note that we don't have to model every single key. One good alternative
is to model a sensitive part of the algorithm, like the substitution box
in AES. We can get away with a much smaller number of traces here; if we
make a model for every possible Hamming weight, then we would end up
with 9 models, which is an order of magnitude smaller. However, then we
can't recover the key from a single attack trace - we need more
information to recover the secret key.

Points of Interest
~~~~~~~~~~~~~~~~~~

Our goal is to create a multivariate probability describing the power
traces for every possible key. If we modeled the entire power trace this
way (with, say, 3000 samples), then we would need a 3000-dimension
distribution. This is insane, so we'll find an alternative.

Thankfully, not every point on the power trace is important to us. There
are two main reasons for this:

-  We might be taking more than one sample per clock cycle. (Through
   most of the ChipWhisperer tutorials, our ADC runs four times faster
   than the target device.) There's no real reason to use all of these
   samples - we can get just as much information from a single sample at
   the right time.
-  Our choice of key doesn't affect the entire power trace. It's likely
   that the subkeys only influence the power consumption at a few
   critical times. If we can pick these important times, then we can
   ignore most of the samples.

These two points mean that we can usually live with a handful (3-5) of
*points of interest*. If we can pick out good points and write down a
model using these samples, then we can use a 3D or 5D distribution - a
great improvement over the original 3000D model.

Picking POIs
^^^^^^^^^^^^

There are several ways to pick the most important points in each of the
traces. Generally, the aim is to find points that vary strongly between
different operations (subkeys or Hamming weights). The simplest method
-- the one that we'll use here -- is the *sum of differences* method.

The algorithm for the sum of difference method is:

-  For every operation :math:`k` and every sample :math:`i`, find the
   average power :math:`M_{k, i}`. For instance, if there are
   :math:`T_k` traces where we performed operation :math:`k`, then this
   average is

:math:`M_{k, i} = \frac{1}{T_k} \sum_{j=1}^{T_k} t_{j, i}`

-  After finding all of the means, calculate all of their absolute
   pairwise differences. Add these up. This will give one "trace" which
   has peaks where the samples are usually different. The calculation
   looks like

:math:`D_{i} = \sum_{k_1, k_2} |M_{k_1, i} - M_{k_2, i}|`

An example of this sum of differences is:

.. figure:: Template-Sum-Of-Difference.png
   :alt: Template-Sum-Of-Difference.png

-  The peaks of :math:`D_i` show the most important points, but we need
   to satisfy point 1 from above - we need to pick some peaks that
   aren't too close. One algorithm to do this is:

1. Pick the highest point in :math:`D_i` and save this value of
   :math:`i` as a point of interest. (ie: :math:`i = argmax(D_i)`)
2. Throw out the nearest :math:`N` points (where :math:`N` is the
   minimum spacing between POIs).
3. Repeat until enough POIs have been selected.

Analyzing the Data
~~~~~~~~~~~~~~~~~~

Suppose that we've picked :math:`I` points of interest, which are at
samples :math:`s_i` (:math:`0 \le i < I`). Then, our goal is to find a
mean and covariance matrix for every operation (every choice of subkey
or intermediate Hamming weight). Let's say that there are :math:`K` of
these operations (maybe 256 subkeys or 9 possible Hamming weights).

For now, we'll look at a single operation :math:`k`
(:math:`0 \le k < K`). The steps are:

-  Find every power trace :math:`t` that falls under the category of
   "operation :math:`k`". (ex: find every power trace where we used a
   subkey of 0x01.) We'll say that there are :math:`T_k` of these, so
   :math:`t_{j, s_i}` means the value at trace :math:`j` and POI
   :math:`i`.
-  Find the average power :math:`\mu_i` at every point of interest. This
   calculation will look like:

:math:`\mu_i = \frac{1}{T_k} \sum_{j=1}^{T_k} t_{j, s_i}`

-  Find the variance :math:`v_i` of the power at each point of interest.
   One way of calculating this is:

:math:`v_i = \frac{1}{T_k} \sum_{j=1}^{T_k} (t_{j, s_i} - \mu_i)^2`

-  Find the covariance :math:`c_{i, i^*}` between the power at every
   pair of POIs (:math:`i` and :math:`i^*`). One way of calculating this
   is:

:math:`c_{i, i^*} = \frac{1}{T_k} \sum_{j=1}^{T_k} (t_{j, s_i} - \mu_i) (t_{j, s_{i^*}} - \mu_{i^*})`

-  Put together the mean and covariance matrices as:

:math:`\mathbf{\mu} = \begin{bmatrix} \mu_1 \\ \mu_2 \\ \mu_3 \\ \vdots \end{bmatrix}`

:math:`\mathbf{\Sigma} = \begin{bmatrix} v_1 & c_{1,2} & c_{1,3} & \dots \\ c_{2,1} & v_2 & c_{2,3} & \dots \\ c_{3,1} & c_{3,2} & v_3 & \dots \\ \vdots & \vdots & \vdots & \ddots \end{bmatrix}`

These steps must be done for every operation :math:`k`. At the end of
this preprocessing, we'll have :math:`K` mean and covariance matrices,
modelling each of the :math:`K` different operations that the target can
do.

Using the Template
------------------

With a template in hand, we can finish our attack. For the attack, we
need a smaller number of traces - we'll say that we have :math:`A`
traces. The sample values will be labeled :math:`a_{j, s_i}`
(:math:`1 \le j \le A`).

Applying the Template
~~~~~~~~~~~~~~~~~~~~~

First, let's apply the template to a single trace. Our job is to decide
how likely all of our key guesses are. We need to do the following:

-  Put our trace values at the POIs into a vector. This vector will be

:math:`\mathbf{a_j} = \begin{bmatrix} a_{j,1} \\ a_{j,2} \\ a_{j,3} \\ \vdots \end{bmatrix}`

-  Calculate the PDF for every key guess and save these for later. This
   might look like:

:math:`p_{k, j} = f_k(\mathbf{a_j})`

-  Repeat these two steps for all of the attack traces.

This process gives us an array of :math:`p_{k, j}`, which says: "Looking
at trace :math:`j`, how likely is it that key :math:`k` is the correct
one?"

Combining the Results
~~~~~~~~~~~~~~~~~~~~~

The very last step is to combine our :math:`p_{k, j}` values to decide
which key is the best fit. The easiest way to do this is to combine them
as

:math:`P_k = \prod_{j=1}^{A} p_{k,j}`

For example, if we guessed that a subkey was equal to 0x00 and our PDF
results in 3 traces were (0.9, 0.8, 0.95), then our overall result would
be 0.684. Having one trace that doesn't match the template can cause
this number to drop quickly, helping us eliminate the wrong guesses.
Finally, we can pick the highest value of :math:`P_k`, which tells us
which guess fits the templates the best, and we're done!

This method of combining our per-trace results can suffer from precision
issues. After multiplying many large or small numbers together, we could
end up with numbers that are too large or small to fit into a floating
point variable. An easy fix is to work with logarithms. Instead of using
:math:`P_k` directly, we can calculate

:math:`\log P_k = \sum_{j=1}^A \log p_{k,j}`

Comparing these logarithms will give us the same results without the
precision issues.

{width="600"}

Capturing Power Traces
----------------------


**In [8]:**

.. code:: ipython3

    %run "Helper_Scripts/Setup.ipynb"


**In [9]:**

.. code:: ipython3

    fw_path = "../hardware/victims/firmware/simpleserial-aes/simpleserial-aes-{}.hex".format(PLATFORM)


**In [10]:**

.. code:: ipython3

    # program the target
    cw.program_target(scope, prog, fw_path)


**Out [10]:**



.. parsed-literal::

    Detected known STMF32: STM32F302xB(C)/303xB(C)
    Extended erase (0x44), this can take ten seconds or more
    Attempting to program 5879 bytes at 0x8000000
    STM32F Programming flash...
    STM32F Reading flash...
    Verified flash OK, 5879 bytes
    


Capturing Template (Rand Key, Rand Text)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first capture will be for the template. This template requires us to
have full control of the device, both the key and plaintext. We will be
using a **random key**, meaning the key will change on **every**
encryption.

Why would we do that? The reason is that if we used a **fixed key**
during the profile, there is a strong chance that we will "overfit" the
templates. That is the templates will only be valid for the fixed key we
used during the profiling phase. Obviously this won't make for a useful
attack in practice, since we don't know the actual key in use.

Our "overfit" model would also mean our results appear to work
increadily well, but they won't work in a real situation. That is,
should you perform profiling and evaluation with the same fixed key, you
will get very good results due to the overfit but they are meaningless
in practice.

The capture phase is otherwise similar to before. The difference here is
that we will have to tell the acquisition model that we want to use a
random key. The following simple block will create a new project and
save 2000 traces.

Remember that the resulting group of Hamming weights won't be
uniformally distributed. This will affect our template results, as for
example the group with a Hamming weight of 8 will have only about
:math:`1/256 * 2000 = 8` traces. This is because the Hamming weight of 8
only exists for 1/256 possible values of the S-Box output. Hamming
weights of 3,4,5 are much more likely for example and will contain much
larger trace groups.


**In [11]:**

.. code:: ipython3

    #Capture Traces
    from tqdm import tnrange
    import chipwhisperer as cw
    
    #Make a project to save template traces
    project = cw.create_project("projects/tutorial_template_templatedata.cwp", overwrite = True)
    
    ktp = cw.ktp.Basic()
    ktp.fixed_key = False #RANDOM KEY in addition to RANDOM TEXT
    
    N = 6000  # Number of traces
    for i in tnrange(N, desc='Capturing traces'):
        key, text = ktp.next()
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        project.traces.append(trace)
    
    #Save the project
    project.save()


**Out [11]:**



While we're here - why not capture the traces for the attack too. These
traces will be much shorter, we'll only capture 20 traces and probably
not even need all of them.


**In [12]:**

.. code:: ipython3

    #Capture Traces
    from tqdm import tnrange
    import chipwhisperer as cw
    import numpy as np
    
    #Make a project to save test traces
    project = cw.create_project("projects/tutorial_template_validate.cwp", overwrite = True)
    
    ktp = cw.ktp.Basic()
    
    N = 20  # Number of traces
    for i in tnrange(N, desc='Capturing traces'):
        key, text = ktp.next()
        trace = cw.capture_trace(scope, target, text, key)
        if trace is None:
            continue
        project.traces.append(trace)
    
    #Save the project
    project.save()


**Out [12]:**







**In [13]:**

.. code:: ipython3

    # cleanup the connection to the target and scope
    scope.dis()
    target.dis()

Building the Templates
----------------------

Opening & Grouping Traces
~~~~~~~~~~~~~~~~~~~~~~~~~


**In [14]:**

.. code:: ipython3

    %run "Helper_Scripts/bokeh.ipynb"


**In [15]:**

.. code:: ipython3

    import chipwhisperer as cw
    import chipwhisperer.analyzer as cwa
    project_template = cw.open_project("projects/tutorial_template_templatedata.cwp")


**In [16]:**

.. code:: ipython3

    #Let's confirm that we get random keys
    for i in range(0, 4):
        print("%d: "%i + " ".join(["%02x"%project_template.keys[i][j] for j in range(0, 16)]))


**Out [16]:**



.. parsed-literal::

    0: 19 6f 0a 10 8e 7f d4 7b 67 46 9f 31 8a f8 3b aa
    1: 70 45 b9 1c b3 15 02 98 ab 4d c4 ee 5b 94 35 7b
    2: 6c 86 92 ee 95 96 c8 9c e0 8e 2a 14 14 c3 ae 93
    3: f9 04 87 1b 81 50 7a 03 f3 e9 23 95 95 27 05 2b
    



**In [17]:**

.. code:: ipython3

    bokeh_plot(project_template.waves[0])


**Out [17]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1001">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="cfd22f8d-af4b-4b68-9ae3-4fd47bea272e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#cfd22f8d-af4b-4b68-9ae3-4fd47bea272e');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="c43d5dec-d09e-4c42-bfb8-4a3a44af0d58" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="7f64cd5d-35c8-4904-ad32-9fb2a7227d85"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7f64cd5d-35c8-4904-ad32-9fb2a7227d85');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"2c07e562-ae7f-46e3-8aa8-2006ee605259":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1040","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"text":""},"id":"1040","type":"Title"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"formatter":{"id":"1044","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1042","type":"BasicTickFormatter"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{},"id":"1044","type":"BasicTickFormatter"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{"formatter":{"id":"1042","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1045","type":"Selection"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"2c07e562-ae7f-46e3-8aa8-2006ee605259","roots":{"1002":"c43d5dec-d09e-4c42-bfb8-4a3a44af0d58"}}];
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

intermediate = sbox[plaintext ^ key]


**In [18]:**

.. code:: ipython3

    sbox=(
        0x63,0x7c,0x77,0x7b,0xf2,0x6b,0x6f,0xc5,0x30,0x01,0x67,0x2b,0xfe,0xd7,0xab,0x76,
        0xca,0x82,0xc9,0x7d,0xfa,0x59,0x47,0xf0,0xad,0xd4,0xa2,0xaf,0x9c,0xa4,0x72,0xc0,
        0xb7,0xfd,0x93,0x26,0x36,0x3f,0xf7,0xcc,0x34,0xa5,0xe5,0xf1,0x71,0xd8,0x31,0x15,
        0x04,0xc7,0x23,0xc3,0x18,0x96,0x05,0x9a,0x07,0x12,0x80,0xe2,0xeb,0x27,0xb2,0x75,
        0x09,0x83,0x2c,0x1a,0x1b,0x6e,0x5a,0xa0,0x52,0x3b,0xd6,0xb3,0x29,0xe3,0x2f,0x84,
        0x53,0xd1,0x00,0xed,0x20,0xfc,0xb1,0x5b,0x6a,0xcb,0xbe,0x39,0x4a,0x4c,0x58,0xcf,
        0xd0,0xef,0xaa,0xfb,0x43,0x4d,0x33,0x85,0x45,0xf9,0x02,0x7f,0x50,0x3c,0x9f,0xa8,
        0x51,0xa3,0x40,0x8f,0x92,0x9d,0x38,0xf5,0xbc,0xb6,0xda,0x21,0x10,0xff,0xf3,0xd2,
        0xcd,0x0c,0x13,0xec,0x5f,0x97,0x44,0x17,0xc4,0xa7,0x7e,0x3d,0x64,0x5d,0x19,0x73,
        0x60,0x81,0x4f,0xdc,0x22,0x2a,0x90,0x88,0x46,0xee,0xb8,0x14,0xde,0x5e,0x0b,0xdb,
        0xe0,0x32,0x3a,0x0a,0x49,0x06,0x24,0x5c,0xc2,0xd3,0xac,0x62,0x91,0x95,0xe4,0x79,
        0xe7,0xc8,0x37,0x6d,0x8d,0xd5,0x4e,0xa9,0x6c,0x56,0xf4,0xea,0x65,0x7a,0xae,0x08,
        0xba,0x78,0x25,0x2e,0x1c,0xa6,0xb4,0xc6,0xe8,0xdd,0x74,0x1f,0x4b,0xbd,0x8b,0x8a,
        0x70,0x3e,0xb5,0x66,0x48,0x03,0xf6,0x0e,0x61,0x35,0x57,0xb9,0x86,0xc1,0x1d,0x9e,
        0xe1,0xf8,0x98,0x11,0x69,0xd9,0x8e,0x94,0x9b,0x1e,0x87,0xe9,0xce,0x55,0x28,0xdf,
        0x8c,0xa1,0x89,0x0d,0xbf,0xe6,0x42,0x68,0x41,0x99,0x2d,0x0f,0xb0,0x54,0xbb,0x16) 
    
    hw = [bin(x).count("1") for x in range(256)]
    
    for n in range(0, 10):
        tin = project_template.textins[n][0]
        key = project_template.keys[n][0]
        
        intermediate = sbox[tin ^ key]
        
        print("Trace %d: Textin[0] = %02x  Key[0] = %02x --> SBox Output = %02x --> HW = %d"%(
            n, tin, key, intermediate, hw[intermediate]))
        


**Out [18]:**



.. parsed-literal::

    Trace 0: Textin[0] = c3  Key[0] = 19 --> SBox Output = 57 --> HW = 5
    Trace 1: Textin[0] = 7f  Key[0] = 70 --> SBox Output = 76 --> HW = 5
    Trace 2: Textin[0] = ad  Key[0] = 6c --> SBox Output = 78 --> HW = 4
    Trace 3: Textin[0] = ca  Key[0] = f9 --> SBox Output = c3 --> HW = 4
    Trace 4: Textin[0] = 1b  Key[0] = 56 --> SBox Output = e3 --> HW = 5
    Trace 5: Textin[0] = 7b  Key[0] = 59 --> SBox Output = 93 --> HW = 4
    Trace 6: Textin[0] = 46  Key[0] = a6 --> SBox Output = e1 --> HW = 4
    Trace 7: Textin[0] = 73  Key[0] = 9d --> SBox Output = 28 --> HW = 2
    Trace 8: Textin[0] = ae  Key[0] = 57 --> SBox Output = 99 --> HW = 4
    Trace 9: Textin[0] = 8c  Key[0] = fb --> SBox Output = f5 --> HW = 6
    



**In [19]:**

.. code:: ipython3

    def cov(x, y):
        # Find the covariance between two 1D lists (x and y).
        # Note that var(x) = cov(x, x)
        return np.cov(x, y)[0][1]

Next, we apply that across all the traces in the profiling run. This
will let us "split" each trace into a different group according to the
resulting Hamming weights:


**In [20]:**

.. code:: ipython3

    # 2: Find HW(sbox) to go with each input
    # Note - we're only working with ONE byte here
    target_byte = 0
    tempSbox = [sbox[project_template.textins[n][target_byte] ^ project_template.keys[n][target_byte]] for n in range(len(project_template.traces))] 
    tempHW   = [hw[s] for s in tempSbox]
    
    # 2.5: Sort traces by HW
    # Make 9 blank lists - one for each Hamming weight
    tempTracesHW = [[] for _ in range(9)]
    
    # Fill them up
    for i in range(len(project_template.traces)):
        HW = tempHW[i]
        tempTracesHW[HW].append(project_template.waves[i])
    
    # Switch to numpy arrays
    tempTracesHW = [np.array(tempTracesHW[HW]) for HW in range(9)]
    
    #print len(tempTracesHW[8])

Points of Interest
~~~~~~~~~~~~~~~~~~

After sorting the traces by their Hamming weights, we need to find an
"average trace" for each weight. We can make an array to hold 9 of these
averages:

::

    tempMeans = np.zeros((9, len(tempTraces[0])))

Then, we can fill up each of these traces one-by-one. NumPy's average()
function makes this easy by including an "axis" input. We can tell NumPy
to find the average at each point in time and repeat this for all 9
weights:

::

    for i in range(9):
        tempMeans[i] = np.average(tempTracesHW[i], 0)

Once again, it's a good idea to plot one of these averages to make sure
that the average traces look okay:


**In [21]:**

.. code:: ipython3

    # 3: Find averages
    tempMeans = np.zeros((9, len(project_template.waves[0])))
    for i in range(9):
        tempMeans[i] = np.average(tempTracesHW[i], 0)
        
    bokeh_plot(tempMeans[2])


**Out [21]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1102">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="23f10308-68ea-481b-b4a9-9162f05d861c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#23f10308-68ea-481b-b4a9-9162f05d861c');
        
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
        var el = document.getElementById("1102");
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
      };var element = document.getElementById("1102");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1102' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1102")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="2eac6f84-7994-483c-a4c4-f401d9d00380" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="348b6efd-0b0a-461b-84b8-b89b7d540682"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#348b6efd-0b0a-461b-84b8-b89b7d540682');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"9d834d9a-a1c7-46cb-b8cd-eb02c990d4d8":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1150","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"text":""},"id":"1150","type":"Title"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{},"id":"1152","type":"BasicTickFormatter"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1155","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"formatter":{"id":"1152","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{},"id":"1155","type":"Selection"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1154","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{},"id":"1154","type":"BasicTickFormatter"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"9d834d9a-a1c7-46cb-b8cd-eb02c990d4d8","roots":{"1103":"2eac6f84-7994-483c-a4c4-f401d9d00380"}}];
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

We can use these average traces to find points of interest using the
''sum of differences'' method. The first step is to create a "trace"
that stores these differences:

::

    tempSumDiff = np.zeros(len(tempTraces[0]))

Then, we want to look at all of the pairs of traces, subtract them, and
add them to the sum of differences. This is simple to do with 2 loops:

::

    for i in range(9):
        for j in range(i):
            tempSumDiff += np.abs(tempMeans[i] - tempMeans[j])

This sum of differences will have peaks where the average traces showed
a lot of variance. Make sure that there are a handful of high peaks that
we can use as points of interest:


**In [22]:**

.. code:: ipython3

    # 4: Find sum of differences
    tempSumDiff = np.zeros(len(project_template.waves[0]))
    for i in range(9):
        for j in range(i):
            tempSumDiff += np.abs(tempMeans[i] - tempMeans[j])
            
    bokeh_plot(tempSumDiff)


**Out [22]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1212">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="7c477677-63ec-4904-ad7b-588e41e881ab"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7c477677-63ec-4904-ad7b-588e41e881ab');
        
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
        var el = document.getElementById("1212");
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
      };var element = document.getElementById("1212");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1212' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1212")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="83b8c0ca-6e6d-4745-8783-fda43d28fbdf" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="47fea33a-7e4b-42c9-9f9f-b115c493ab29"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#47fea33a-7e4b-42c9-9f9f-b115c493ab29');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"d58ab6a6-c9b4-4ea3-af5b-e1313ddb563a":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1269","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1269","type":"Title"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{},"id":"1274","type":"Selection"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1276","type":"BoxAnnotation"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{},"id":"1275","type":"UnionRenderers"},{"attributes":{},"id":"1273","type":"BasicTickFormatter"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{"formatter":{"id":"1271","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"formatter":{"id":"1273","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{},"id":"1271","type":"BasicTickFormatter"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{},"id":"1220","type":"LinearScale"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1274","type":"Selection"},"selection_policy":{"id":"1275","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{},"id":"1236","type":"ResetTool"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{"overlay":{"id":"1276","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"d58ab6a6-c9b4-4ea3-af5b-e1313ddb563a","roots":{"1213":"83b8c0ca-6e6d-4745-8783-fda43d28fbdf"}}];
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

Now, the most interesting points of interest are the highest peaks in
this sum of differences plot. However, we can't just sort the array and
pick the top points. Remember (because you read the theory page, right?
right?) that we need to make sure our points have some space between
them. For instance, it would be a bad idea to pick both 1950 and 1951 as
points of interest.

We can use the algorithm from the theory page to pick out some POIs:

-  Make an empty list of POIs
-  Find the biggest peak in the sum of differences trace and add it to
   the list of POIs
-  Zero out some of the surrounding points
-  Repeat until we have enough POIs

Try to code this on your own - it's a good exercise. If you get stuck,
here's our implementation:


**In [23]:**

.. code:: ipython3

    # 5: Find POIs
    POIs = []
    numPOIs = 5
    POIspacing = 5
    for i in range(numPOIs):
        # Find the max
        nextPOI = tempSumDiff.argmax()
        POIs.append(nextPOI)
        
        # Make sure we don't pick a nearby value
        poiMin = max(0, nextPOI - POIspacing)
        poiMax = min(nextPOI + POIspacing, len(tempSumDiff))
        for j in range(poiMin, poiMax):
            tempSumDiff[j] = 0
        
    #print POIs


**In [24]:**

.. code:: ipython3

    bokeh_plot(tempSumDiff)


**Out [24]:**


.. raw:: html

    <div class="data_html">
        
        <div class="bk-root">
            <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
            <span id="1331">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="b7d2cb35-0b60-4f21-bc6b-8cca294f8689"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#b7d2cb35-0b60-4f21-bc6b-8cca294f8689');
        
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
        var el = document.getElementById("1331");
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
      };var element = document.getElementById("1331");
      if (element == null) {
        console.error("Bokeh: ERROR: autoload.js configured with elementid '1331' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1331")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="7309311d-107e-4d71-a4b7-61e76ac6e972" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="30630169-4562-4ec2-83de-4602ff20ee55"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#30630169-4562-4ec2-83de-4602ff20ee55');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"b8a79a73-fab8-42a2-8594-6983468fab16":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1397","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{},"id":"1403","type":"UnionRenderers"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1404","type":"BoxAnnotation"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1402","type":"Selection"},"selection_policy":{"id":"1403","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{},"id":"1402","type":"Selection"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{},"id":"1401","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{},"id":"1347","type":"BasicTicker"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{},"id":"1399","type":"BasicTickFormatter"},{"attributes":{"text":""},"id":"1397","type":"Title"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{"formatter":{"id":"1399","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{"overlay":{"id":"1404","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{},"id":"1342","type":"BasicTicker"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{"formatter":{"id":"1401","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"b8a79a73-fab8-42a2-8594-6983468fab16","roots":{"1332":"7309311d-107e-4d71-a4b7-61e76ac6e972"}}];
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


**In [25]:**

.. code:: ipython3

    print(POIs)


**Out [25]:**



.. parsed-literal::

    [949, 954, 977, 1801, 1817]
    


Generating a Template
~~~~~~~~~~~~~~~~~~~~~

With 5 (or numPOIs) POIs picked out, we can build our multivariate
distributions at each point for each Hamming weight. We need to write
down two matrices for each weight:

-  A mean matrix (1 x numPOIs) which stores the mean at each POI
-  A covariance matrix (numPOIs x numPOIs) which stores the variances
   and covariances between each of the POIs

The mean matrix is easy to set up because we've already found the mean
at every point. All we need to do is grab the right points:

::

    meanMatrix = np.zeros((9, numPOIs))
    for HW in range(9):
        for i in range(numPOIs):
            meanMatrix[HW][i] = tempMeans[HW][POIs[i]]

The covariance matrix is a bit more complex. We need a way to find the
covariance between two 1D arrays. Helpfully, NumPy has the cov(a, b)
function, which returns the matrix

::

    np.cov(a, b) = [[cov(a, a), cov(a, b)],
                    [cov(b, a), cov(b, b)]]

We can use this to define our own covariance function:

::

    def cov(x, y):
        # Find the covariance between two 1D lists (x and y).
        # Note that var(x) = cov(x, x)
        return np.cov(x, y)[0][1]

As mentioned in the comments, this function can also calculate the
variance of an array by passing the same array in for both x and y.

We'll use our function to build the covariance matrices, as below:


**In [26]:**

.. code:: ipython3

    # 6: Fill up mean and covariance matrix for each HW
    meanMatrix = np.zeros((9, numPOIs))
    covMatrix  = np.zeros((9, numPOIs, numPOIs))
    for HW in range(9):
        for i in range(numPOIs):
            # Fill in mean
            meanMatrix[HW][i] = tempMeans[HW][POIs[i]]
            for j in range(numPOIs):
                x = tempTracesHW[HW][:,POIs[i]]
                y = tempTracesHW[HW][:,POIs[j]]
                covMatrix[HW,i,j] = cov(x, y)

Finally - let's confirm these matricies look A-OK. Basically you need to
ensure there isn't zeros in them, which normally indiciates you didn't
have enough data.


**In [27]:**

.. code:: ipython3

    print(meanMatrix)
    print(covMatrix[0])


**Out [27]:**



.. parsed-literal::

    [[-0.14041941 -0.03947368 -0.10135691  0.07015831  0.0443051 ]
     [-0.14718192 -0.04195463 -0.10275906  0.06903009  0.04274876]
     [-0.1536841  -0.04501133 -0.10429986  0.06762434  0.04110508]
     [-0.15827512 -0.04827282 -0.1061443   0.06656939  0.04023361]
     [-0.16541145 -0.05218373 -0.10797722  0.06498965  0.03892896]
     [-0.1725221  -0.05609251 -0.10965497  0.06373511  0.0377454 ]
     [-0.17554769 -0.05892219 -0.11194705  0.06241242  0.03651234]
     [-0.18285886 -0.06311652 -0.11382872  0.061119    0.03570372]
     [-0.19018555 -0.06621094 -0.11621094  0.06035156  0.034375  ]]
    [[ 1.20352583e-05  4.48673092e-06  5.46550193e-06 -3.51632548e-06
      -1.50859007e-06]
     [ 4.48673092e-06  3.42430427e-06  1.20185272e-06 -1.07358074e-06
       1.26877723e-06]
     [ 5.46550193e-06  1.20185272e-06  3.05789256e-05 -5.26472839e-06
       1.35243288e-06]
     [-3.51632548e-06 -1.07358074e-06 -5.26472839e-06  8.61095406e-06
       9.38337449e-06]
     [-1.50859007e-06  1.26877723e-06  1.35243288e-06  9.38337449e-06
       3.04339225e-05]]
    


Applying the Template
~~~~~~~~~~~~~~~~~~~~~

The very last step is to apply our template to these traces. We want to
keep a running total of :math:`\log P_k = \sum_j \log p_{k,j}`, so we'll
make space for our 256 guesses:

::

    P_k = np.zeros(256)

Then, we want to do the following for every attack trace:

-  Grab the samples from the points of interest and store them in a list
   :math:`\mathbf{a}`
-  For all 256 of the subkey guesses...
-  Figure out which Hamming weight we need, according to our known
   plaintext and guessed subkey
-  Build a multivariate\_normal object using the relevant mean and
   covariance matrices
-  Calculate the log of the PDF (:math:`\log f(\mathbf{a})`) and add it
   to the running total
-  List the best guesses we've seen so far

Make sure you've included the multivariate stats from SciPy too - it's
normally an extra import.


**In [28]:**

.. code:: ipython3

    import chipwhisperer as cw
    project_validate = cw.open_project("projects/tutorial_template_validate.cwp")


**In [29]:**

.. code:: ipython3

    from scipy.stats import multivariate_normal
    # 2: Attack
    # Running total of log P_k
    P_k = np.zeros(256)
    for j in range(len(project_validate.traces)):
        # Grab key points and put them in a small matrix
        a = [project_validate.waves[j][POIs[i]] for i in range(len(POIs))]
        
        # Test each key
        for k in range(256):
            # Find HW coming out of sbox
            HW = hw[sbox[project_validate.textins[j][target_byte] ^ k]]
        
            # Find p_{k,j}
            rv = multivariate_normal(meanMatrix[HW], covMatrix[HW])
            p_kj = rv.logpdf(a)
       
            # Add it to running total
            P_k[k] += p_kj
    
        # Print our top 5 results so far
        # Best match on the right
        print(" ".join(["%02x"%j for j in P_k.argsort()[-5:]]))
        
    guess = P_k.argsort()[-1]
    print(hex(guess))


**Out [29]:**



.. parsed-literal::

    da db 1d 45 59
    5a 89 e5 29 c8
    ac be fe c9 29
    07 ac 85 9f 29
    2b 8b 07 85 29
    b1 85 8b 07 29
    c1 8b 85 29 2b
    d9 c1 8b 85 2b
    b6 d9 8b 85 2b
    b6 07 d9 85 2b
    31 c1 b6 85 2b
    31 c1 b6 85 2b
    c1 5b b6 85 2b
    03 23 85 5b 2b
    29 85 23 5b 2b
    b5 29 23 5b 2b
    8b b5 23 5b 2b
    85 23 b5 5b 2b
    1b b5 23 5b 2b
    8b 23 b5 5b 2b
    0x2b
    


With any luck you will have found the correct key byte! You can try
extending this to attack multiple bytes instead. The traces you already
recorded will work just great, you'll need to make the following
changes:

-  Build a new POI for each byte number
-  Build new matricies for each byte number
-  Apply those matricies for each byte

Conclusions
-----------

That's it! This template attack must have raised some new questions for
you. A few things to try beyond the above:

-  Get rid of the Hamming weight assumption. You can build templates
   based on each key byte value itself directly, which means a SINGLE
   power trace would result in the secret key(!).
-  Doing this requires a *lot* more data, since you need to build
   information on each key byte.
-  Make your life easier by using a FIXED plaintext. The same plaintext
   is used during building the template as when you apply the attack.

Tests
-----


**In [30]:**

.. code:: ipython3

    assert guess == 0x2b, "Failed to break key byte 0, got {}".format(hex(guess))


**In [ ]:**

