
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

    -2.73
    3.93
    2.75
    2.64
    4.17
    0.70
    3.59
    1.20
    3.75
    1.97
    


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
          <td>0.021227</td>
          <td>0.003275</td>
          <td>1.5</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>1</th>
          <td>0.095131</td>
          <td>0.178873</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>2</th>
          <td>0.164209</td>
          <td>0.197888</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>3</th>
          <td>0.169651</td>
          <td>0.196233</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>4</th>
          <td>0.081732</td>
          <td>0.168018</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>5</th>
          <td>0.184023</td>
          <td>0.102788</td>
          <td>1.5</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>6</th>
          <td>0.115591</td>
          <td>0.190999</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>7</th>
          <td>0.197176</td>
          <td>0.132789</td>
          <td>1.5</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>8</th>
          <td>0.105787</td>
          <td>0.185839</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>9</th>
          <td>0.194029</td>
          <td>0.174717</td>
          <td>1.5</td>
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

[][1]

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

.. figure:: https://wiki.newae.com/images/9/97/Template-Sum-Of-Difference.png
   :alt: 1

   1

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

    0: 47 ff 9d d9 19 76 80 7e d9 9b e5 27 de 46 39 ef
    1: a8 ca 49 9f 1a 32 b3 f5 48 65 0c a6 77 01 79 bc
    2: 0a 37 21 12 6e 11 6a 3a 8b 34 9f 84 95 c0 e8 0e
    3: ae a5 a6 48 38 0f ac f7 3f 25 8e 09 1a 3a 57 0b
    



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

    

    <div id="c96800af-e331-4f3c-8672-291c8f9dcd79"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#c96800af-e331-4f3c-8672-291c8f9dcd79');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="08a24c00-f584-4cca-847f-2b784a0f869f" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="eb538efe-6ddf-4ce7-b2f7-407bed1f1a35"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#eb538efe-6ddf-4ce7-b2f7-407bed1f1a35');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"5a81adf3-b5d8-49af-922a-50732b165c4e":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1045","type":"UnionRenderers"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1046","type":"Selection"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1046","type":"Selection"},"selection_policy":{"id":"1045","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"5a81adf3-b5d8-49af-922a-50732b165c4e","roots":{"1002":"08a24c00-f584-4cca-847f-2b784a0f869f"}}];
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

    Trace 0: Textin[0] = 05  Key[0] = 47 --> SBox Output = 2c --> HW = 3
    Trace 1: Textin[0] = 13  Key[0] = a8 --> SBox Output = ea --> HW = 5
    Trace 2: Textin[0] = 5b  Key[0] = 0a --> SBox Output = d1 --> HW = 4
    Trace 3: Textin[0] = 7c  Key[0] = ae --> SBox Output = b5 --> HW = 5
    Trace 4: Textin[0] = 48  Key[0] = c0 --> SBox Output = c4 --> HW = 3
    Trace 5: Textin[0] = 7a  Key[0] = 45 --> SBox Output = 75 --> HW = 5
    Trace 6: Textin[0] = 39  Key[0] = 65 --> SBox Output = 4a --> HW = 3
    Trace 7: Textin[0] = e5  Key[0] = cd --> SBox Output = 34 --> HW = 3
    Trace 8: Textin[0] = 85  Key[0] = a5 --> SBox Output = b7 --> HW = 6
    Trace 9: Textin[0] = b8  Key[0] = 68 --> SBox Output = 70 --> HW = 3
    



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

    

    <div id="3b59611b-d976-48dd-b8f6-bca917b83447"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3b59611b-d976-48dd-b8f6-bca917b83447');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="a507a9fb-c9e4-472a-b5bc-25519764bdb9" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="1891fc62-1c97-4214-9598-600385b0bb94"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#1891fc62-1c97-4214-9598-600385b0bb94');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"7838deb2-01b3-458a-acca-fcecc46ec6a5":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1149","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{},"id":"1155","type":"UnionRenderers"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1156","type":"Selection"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{"text":""},"id":"1149","type":"Title"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1156","type":"Selection"},"selection_policy":{"id":"1155","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"7838deb2-01b3-458a-acca-fcecc46ec6a5","roots":{"1103":"a507a9fb-c9e4-472a-b5bc-25519764bdb9"}}];
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

    

    <div id="7f34874b-3f48-4995-afc8-72c1a73a610b"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7f34874b-3f48-4995-afc8-72c1a73a610b');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="09b00d89-f6bd-479a-aa9e-21441541186a" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="4bc25924-72e3-43f6-856c-accf89a385db"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#4bc25924-72e3-43f6-856c-accf89a385db');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"4025c372-f873-42df-b203-f25da0365dfd":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1268","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{},"id":"1275","type":"Selection"},{"attributes":{"formatter":{"id":"1272","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"formatter":{"id":"1270","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"overlay":{"id":"1276","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{"text":""},"id":"1268","type":"Title"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1276","type":"BoxAnnotation"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"},{"attributes":{},"id":"1236","type":"ResetTool"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{},"id":"1274","type":"UnionRenderers"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{},"id":"1272","type":"BasicTickFormatter"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{},"id":"1220","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1275","type":"Selection"},"selection_policy":{"id":"1274","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{},"id":"1270","type":"BasicTickFormatter"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"4025c372-f873-42df-b203-f25da0365dfd","roots":{"1213":"09b00d89-f6bd-479a-aa9e-21441541186a"}}];
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

    

    <div id="5056676a-dd51-4ab6-a249-0e8f160a34bb"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#5056676a-dd51-4ab6-a249-0e8f160a34bb');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="2b93de02-50e7-4d89-9929-710a56ad21f9" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="45ba7d56-147e-4e09-b38d-9b94d148eebe"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#45ba7d56-147e-4e09-b38d-9b94d148eebe');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"8707e274-fffc-4793-af17-32a770bd16f2":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1396","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1404","type":"BoxAnnotation"},{"attributes":{},"id":"1402","type":"UnionRenderers"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{"overlay":{"id":"1404","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{},"id":"1347","type":"BasicTicker"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{"text":""},"id":"1396","type":"Title"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1403","type":"Selection"},"selection_policy":{"id":"1402","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{"formatter":{"id":"1400","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{},"id":"1398","type":"BasicTickFormatter"},{"attributes":{},"id":"1400","type":"BasicTickFormatter"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{"formatter":{"id":"1398","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{},"id":"1403","type":"Selection"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{},"id":"1342","type":"BasicTicker"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"8707e274-fffc-4793-af17-32a770bd16f2","roots":{"1332":"2b93de02-50e7-4d89-9929-710a56ad21f9"}}];
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

    [[-0.1373402  -0.03915128 -0.09841087  0.07013494  0.04314631]
     [-0.14386018 -0.04210236 -0.09962941  0.06884515  0.04171675]
     [-0.15073972 -0.04554934 -0.10094105  0.06720526  0.04042578]
     [-0.15545285 -0.04868808 -0.10331123  0.06625787  0.03965979]
     [-0.1620823  -0.05256426 -0.1046058   0.06451434  0.03825606]
     [-0.16933769 -0.05667199 -0.10668161  0.06345468  0.03719762]
     [-0.17221012 -0.05924125 -0.1089034   0.06197533  0.03614343]
     [-0.1792074  -0.06359113 -0.11085894  0.06049777  0.03506896]
     [-0.18822181 -0.06606658 -0.11328125  0.05965523  0.03316067]]
    [[ 2.16661594e-05  3.48442045e-06  1.11344573e-05  2.20046931e-06
      -9.28903555e-07]
     [ 3.48442045e-06  2.71652684e-06  9.74316618e-07  1.11881273e-06
       1.33349266e-06]
     [ 1.11344573e-05  9.74316618e-07  2.33361215e-05  2.76606836e-06
      -3.53809043e-06]
     [ 2.20046931e-06  1.11881273e-06  2.76606836e-06  6.86975785e-06
       1.16835425e-06]
     [-9.28903555e-07  1.33349266e-06 -3.53809043e-06  1.16835425e-06
       2.64882009e-05]]
    


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

    78 75 73 8a ff
    75 53 a2 78 98
    98 2b 24 23 75
    2b c4 9b ea 75
    c0 83 2b ea 75
    99 f0 18 2b 75
    f0 94 18 75 2b
    94 99 75 18 2b
    a6 94 75 18 2b
    83 a6 94 75 2b
    4c 94 83 75 2b
    4c 83 94 75 2b
    4c 83 94 75 2b
    4c 83 94 75 2b
    75 83 4c 94 2b
    6f 4c 75 94 2b
    a7 75 3c 94 2b
    a7 75 3c 94 2b
    03 a7 3c 94 2b
    03 a7 3c 94 2b
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

