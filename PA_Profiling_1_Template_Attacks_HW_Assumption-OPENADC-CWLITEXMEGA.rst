
.. raw:: html

   <h1>

Table of Contents

.. raw:: html

   </h1>

.. container:: toc

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

Template Attack with Hardware Assumption
========================================

*Template attacks* are a powerful type of side-channel attack. These
attacks are a subset of *profiling attacks*, where an attacker creates a
“profile” of a sensitive device and applies this profile to quickly find
a victim’s secret key.

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
template of the device’s operation. This template notes a few “points of
interest” in the power traces and a multivariate distribution of the
power traces at each point. 3. On the victim device, record a small
number of power traces. Use multiple plaintexts. (We have no control
over the secret key, which is fixed.) 4. Apply the template to the
attack traces. For each subkey, track which value is most likely to be
the correct subkey. Continue until the key has been recovered.


**In [1]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEXMEGA'
    CRYPTO_TARGET = 'AVRCRYPTOLIB'
    num_traces = 50
    CHECK_CORR = False


**In [2]:**

.. code:: bash

    %%bash -s "$PLATFORM" "$CRYPTO_TARGET"
    cd ../hardware/victims/firmware/simpleserial-aes
    make PLATFORM=$1 CRYPTO_TARGET=$2


**Out [2]:**



.. parsed-literal::

    rm -f -- simpleserial-aes-CWLITEXMEGA.hex
    rm -f -- simpleserial-aes-CWLITEXMEGA.eep
    rm -f -- simpleserial-aes-CWLITEXMEGA.cof
    rm -f -- simpleserial-aes-CWLITEXMEGA.elf
    rm -f -- simpleserial-aes-CWLITEXMEGA.map
    rm -f -- simpleserial-aes-CWLITEXMEGA.sym
    rm -f -- simpleserial-aes-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes-independant.s aes_enc.s aes_keyschedule.s aes_sbox.s aes128_enc.s
    rm -f -- simpleserial-aes.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes-independant.d aes_enc.d aes_keyschedule.d aes_sbox.d aes128_enc.d
    rm -f -- simpleserial-aes.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes-independant.i aes_enc.i aes_keyschedule.i aes_sbox.i aes128_enc.i
    .
    -------- begin --------
    avr-gcc (GCC) 5.4.0
    Copyright (C) 2015 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_enc.o.d .././crypto/avrcryptolib//aes/aes_enc.c -o objdir/aes_enc.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_keyschedule.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_keyschedule.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_keyschedule.o.d .././crypto/avrcryptolib//aes/aes_keyschedule.c -o objdir/aes_keyschedule.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes_sbox.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes_sbox.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes_sbox.o.d .././crypto/avrcryptolib//aes/aes_sbox.c -o objdir/aes_sbox.o 
    .
    Compiling C: .././crypto/avrcryptolib//aes/aes128_enc.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes128_enc.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/aes128_enc.o.d .././crypto/avrcryptolib//aes/aes128_enc.c -o objdir/aes128_enc.o 
    .
    Assembling: .././crypto/avrcryptolib//gf256mul/gf256mul.S
    avr-gcc -c -mmcu=atxmega128d3 -I. -x assembler-with-cpp -DF_CPU=7372800 -Wa,-gstabs,-adhlns=objdir/gf256mul.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul .././crypto/avrcryptolib//gf256mul/gf256mul.S -o objdir/gf256mul.o
    .
    Linking: simpleserial-aes-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DAVRCRYPTOLIB -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/avrcryptolib//aes -I.././crypto/avrcryptolib//gf256mul -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEXMEGA.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes-independant.o objdir/aes_enc.o objdir/aes_keyschedule.o objdir/aes_sbox.o objdir/aes128_enc.o objdir/gf256mul.o --output simpleserial-aes-CWLITEXMEGA.elf -Wl,-Map=simpleserial-aes-CWLITEXMEGA.map,--cref   -lm  
    .
    Creating load file for Flash: simpleserial-aes-CWLITEXMEGA.hex
    avr-objcopy -O ihex -R .eeprom -R .fuse -R .lock -R .signature simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.hex
    .
    Creating load file for EEPROM: simpleserial-aes-CWLITEXMEGA.eep
    avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
    --change-section-lma .eeprom=0 --no-change-warnings -O ihex simpleserial-aes-CWLITEXMEGA.elf simpleserial-aes-CWLITEXMEGA.eep || exit 0
    .
    Creating Extended Listing: simpleserial-aes-CWLITEXMEGA.lss
    avr-objdump -h -S -z simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.lss
    .
    Creating Symbol Table: simpleserial-aes-CWLITEXMEGA.sym
    avr-nm -n simpleserial-aes-CWLITEXMEGA.elf > simpleserial-aes-CWLITEXMEGA.sym
    Size after:
       text	   data	    bss	    dec	    hex	filename
       3234	     32	    228	   3494	    da6	simpleserial-aes-CWLITEXMEGA.elf
    +--------------------------------------------------------
    + Built for platform CW-Lite XMEGA
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
measurement, we don’t expect to see a perfect, constant level. For
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

What’s this graph all about? This graph shows the *probability density*
of this random variable. Note it does NOT give you an actual
probability, instead you need to integrate to find the probability the
variable is lieing in some range. This is important because the density
function won’t always return just < 1, and with multivariate (to be
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
shortly). Let’s say we have the following experiment setup:

We have two batteries and are trying to figure out which one is
connected behind the curtain. There is a *lot* of noise in our
measurement setup and we don’t have much time to waste, so can only
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

    5.40
    2.63
    1.29
    4.93
    2.79
    4.31
    6.67
    2.64
    2.21
    1.24
    


Let’s see how you stack up to a computer! Try entering your guess of the
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
          <td>0.029728</td>
          <td>0.096953</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>1</th>
          <td>0.169967</td>
          <td>0.196116</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>2</th>
          <td>0.198413</td>
          <td>0.138619</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>3</th>
          <td>0.046006</td>
          <td>0.125479</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>4</th>
          <td>0.162245</td>
          <td>0.198327</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>5</th>
          <td>0.074319</td>
          <td>0.160938</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>6</th>
          <td>0.007076</td>
          <td>0.037098</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>7</th>
          <td>0.169518</td>
          <td>0.196282</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>8</th>
          <td>0.187392</td>
          <td>0.184389</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>9</th>
          <td>0.197810</td>
          <td>0.135514</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
      </tbody>
    </table>
    </div>
    </div>



Multivariate Statistics
~~~~~~~~~~~~~~~~~~~~~~~

The 1-variable Gaussian distribution works well for one measurement.
What if we’re working with more than one random variable?

Suppose we’re measuring two voltages that have some amount of noise on
them. We’ll call them :math:`\mathbf{X}` and :math:`\mathbf{Y}`. As a
first attempt, we could write down a model for :math:`\mathbf{X}` using
a normal distribution and a separate model for :math:`\mathbf{Y}` using
a different distribution. However, this might not always make sense. If
we write two separate distributions, what we’re saying is that the two
variables are independent: when :math:`\mathbf{X}` goes up, there’s no
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

Don’t worry if this looks crazy - the SciPy package in Python will do
all the heavy lifting for us. As with the single-variable distributions,
we’re going to use this to find how likely a certain observation is. In
other words, if we put :math:`k` points of our power trace into
:math:`\mathbf{x}` and we find that :math:`f(\mathbf{x})` is very high,
then we’ve probably found a good guess.

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

|image0|

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

.. |image0| image:: Template-Sum-Of-Difference.png
   :width: 600px

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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 3265 bytes
    


Capturing Template (Rand Key, Rand Text)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first capture will be for the template. This template requires us to
have full control of the device, both the key and plaintext. We will be
using a **random key**, meaning the key will change on **every**
encryption.

Why would we do that? The reason is that if we used a **fixed key**
during the profile, there is a strong chance that we will “overfit” the
templates. That is the templates will only be valid for the fixed key we
used during the profiling phase. Obviously this won’t make for a useful
attack in practice, since we don’t know the actual key in use.

Our “overfit” model would also mean our results appear to work
increadily well, but they won’t work in a real situation. That is,
should you perform profiling and evaluation with the same fixed key, you
will get very good results due to the overfit but they are meaningless
in practice.

The capture phase is otherwise similar to before. The difference here is
that we will have to tell the acquisition model that we want to use a
random key. The following simple block will create a new project and
save 2000 traces.

Remember that the resulting group of Hamming weights won’t be
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






While we’re here - why not capture the traces for the attack too. These
traces will be much shorter, we’ll only capture 20 traces and probably
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

    0: 15 96 df 95 07 f4 52 c9 48 4d b4 46 a5 12 98 fe
    1: 7e 76 af e8 d7 02 69 f5 da 30 09 e6 b4 e7 ec 50
    2: e9 be 1c 64 25 25 dd 8e f6 26 51 dd 9a 35 a8 42
    3: 52 45 24 ac b5 9e b8 7b 48 75 a1 00 c2 aa 52 00
    



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

    

    <div id="698b011b-0dc4-40c9-b6c0-d6e938fc5ff4"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#698b011b-0dc4-40c9-b6c0-d6e938fc5ff4');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="d2b9ea15-a430-4550-9d08-4b04448131ed"></div>
    
    </div>



.. raw:: html

    

    <div id="98a54d42-8f61-4334-a170-1c2a5d2dc978"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#98a54d42-8f61-4334-a170-1c2a5d2dc978');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"95ceab88-5213-4344-898d-55b0e96aa443":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1011","type":"LinearAxis"},{"id":"1015","type":"Grid"},{"id":"1016","type":"LinearAxis"},{"id":"1020","type":"Grid"},{"id":"1029","type":"BoxAnnotation"},{"id":"1039","type":"GlyphRenderer"}],"title":{"id":"1042","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"dimension":1,"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{},"id":"1045","type":"BasicTickFormatter"},{"attributes":{"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{},"id":"1047","type":"Selection"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{},"id":"1048","type":"UnionRenderers"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1037","type":"Line"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1038","type":"Line"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"data_source":{"id":"1036","type":"ColumnDataSource"},"glyph":{"id":"1037","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1038","type":"Line"},"selection_glyph":null,"view":{"id":"1040","type":"CDSView"}},"id":"1039","type":"GlyphRenderer"},{"attributes":{"overlay":{"id":"1029","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1047","type":"Selection"},"selection_policy":{"id":"1048","type":"UnionRenderers"}},"id":"1036","type":"ColumnDataSource"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{"formatter":{"id":"1045","type":"BasicTickFormatter"},"plot":{"id":"1002","subtype":"Figure","type":"Plot"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1029","type":"BoxAnnotation"},{"attributes":{"source":{"id":"1036","type":"ColumnDataSource"}},"id":"1040","type":"CDSView"},{"attributes":{"plot":null,"text":""},"id":"1042","type":"Title"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"95ceab88-5213-4344-898d-55b0e96aa443","roots":{"1002":"d2b9ea15-a430-4550-9d08-4b04448131ed"}}];
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

    Trace 0: Textin[0] = ad  Key[0] = 15 --> SBox Output = 6c --> HW = 4
    Trace 1: Textin[0] = 71  Key[0] = 7e --> SBox Output = 76 --> HW = 5
    Trace 2: Textin[0] = 83  Key[0] = e9 --> SBox Output = 02 --> HW = 1
    Trace 3: Textin[0] = 87  Key[0] = 52 --> SBox Output = 03 --> HW = 2
    Trace 4: Textin[0] = 0d  Key[0] = 60 --> SBox Output = 3c --> HW = 4
    Trace 5: Textin[0] = 24  Key[0] = a7 --> SBox Output = ec --> HW = 5
    Trace 6: Textin[0] = ee  Key[0] = 73 --> SBox Output = 5e --> HW = 5
    Trace 7: Textin[0] = fb  Key[0] = 47 --> SBox Output = 65 --> HW = 4
    Trace 8: Textin[0] = bb  Key[0] = f8 --> SBox Output = 1a --> HW = 3
    Trace 9: Textin[0] = 0e  Key[0] = 61 --> SBox Output = a8 --> HW = 3
    



**In [19]:**

.. code:: ipython3

    def cov(x, y):
        # Find the covariance between two 1D lists (x and y).
        # Note that var(x) = cov(x, x)
        return np.cov(x, y)[0][1]

Next, we apply that across all the traces in the profiling run. This
will let us “split” each trace into a different group according to the
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
“average trace” for each weight. We can make an array to hold 9 of these
averages:

::

   tempMeans = np.zeros((9, len(tempTraces[0])))

Then, we can fill up each of these traces one-by-one. NumPy’s average()
function makes this easy by including an “axis” input. We can tell NumPy
to find the average at each point in time and repeat this for all 9
weights:

::

   for i in range(9):
       tempMeans[i] = np.average(tempTracesHW[i], 0)

Once again, it’s a good idea to plot one of these averages to make sure
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
            <span id="1104">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="7334a4f1-9442-44a5-b2ab-3cf21298cd0a"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#7334a4f1-9442-44a5-b2ab-3cf21298cd0a');
        
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
        var el = document.getElementById("1104");
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
      };var element = document.getElementById("1104");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1104' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1104")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="602c191b-fe93-430a-a266-29f930e5def8"></div>
    
    </div>



.. raw:: html

    

    <div id="64f6a37e-09fb-4b06-9420-807ff4aa1217"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#64f6a37e-09fb-4b06-9420-807ff4aa1217');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"8e6da06d-b35e-400b-aaa2-be2a45eb3acf":{"roots":{"references":[{"attributes":{"below":[{"id":"1114","type":"LinearAxis"}],"left":[{"id":"1119","type":"LinearAxis"}],"renderers":[{"id":"1114","type":"LinearAxis"},{"id":"1118","type":"Grid"},{"id":"1119","type":"LinearAxis"},{"id":"1123","type":"Grid"},{"id":"1132","type":"BoxAnnotation"},{"id":"1142","type":"GlyphRenderer"}],"title":{"id":"1154","type":"Title"},"toolbar":{"id":"1130","type":"Toolbar"},"x_range":{"id":"1106","type":"DataRange1d"},"x_scale":{"id":"1110","type":"LinearScale"},"y_range":{"id":"1108","type":"DataRange1d"},"y_scale":{"id":"1112","type":"LinearScale"}},"id":"1105","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1128","type":"ResetTool"},{"attributes":{},"id":"1157","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1139","type":"ColumnDataSource"},"glyph":{"id":"1140","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1141","type":"Line"},"selection_glyph":null,"view":{"id":"1143","type":"CDSView"}},"id":"1142","type":"GlyphRenderer"},{"attributes":{"formatter":{"id":"1157","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1119","type":"LinearAxis"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1124","type":"PanTool"},{"id":"1125","type":"WheelZoomTool"},{"id":"1126","type":"BoxZoomTool"},{"id":"1127","type":"SaveTool"},{"id":"1128","type":"ResetTool"},{"id":"1129","type":"HelpTool"}]},"id":"1130","type":"Toolbar"},{"attributes":{},"id":"1159","type":"Selection"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{},"id":"1129","type":"HelpTool"},{"attributes":{"source":{"id":"1139","type":"ColumnDataSource"}},"id":"1143","type":"CDSView"},{"attributes":{},"id":"1120","type":"BasicTicker"},{"attributes":{"dimension":1,"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1120","type":"BasicTicker"}},"id":"1123","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1141","type":"Line"},{"attributes":{},"id":"1115","type":"BasicTicker"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1132","type":"BoxAnnotation"},{"attributes":{"plot":null,"text":""},"id":"1154","type":"Title"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1140","type":"Line"},{"attributes":{},"id":"1124","type":"PanTool"},{"attributes":{"overlay":{"id":"1132","type":"BoxAnnotation"}},"id":"1126","type":"BoxZoomTool"},{"attributes":{"callback":null},"id":"1108","type":"DataRange1d"},{"attributes":{},"id":"1127","type":"SaveTool"},{"attributes":{"formatter":{"id":"1155","type":"BasicTickFormatter"},"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1114","type":"LinearAxis"},{"attributes":{},"id":"1125","type":"WheelZoomTool"},{"attributes":{},"id":"1112","type":"LinearScale"},{"attributes":{},"id":"1155","type":"BasicTickFormatter"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1159","type":"Selection"},"selection_policy":{"id":"1160","type":"UnionRenderers"}},"id":"1139","type":"ColumnDataSource"},{"attributes":{},"id":"1160","type":"UnionRenderers"},{"attributes":{"plot":{"id":"1105","subtype":"Figure","type":"Plot"},"ticker":{"id":"1115","type":"BasicTicker"}},"id":"1118","type":"Grid"}],"root_ids":["1105"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"8e6da06d-b35e-400b-aaa2-be2a45eb3acf","roots":{"1105":"602c191b-fe93-430a-a266-29f930e5def8"}}];
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
‘’sum of differences’’ method. The first step is to create a “trace”
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
            <span id="1216">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="63b9f47b-7d2b-4553-8f05-9d8bc48ef796"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#63b9f47b-7d2b-4553-8f05-9d8bc48ef796');
        
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
        var el = document.getElementById("1216");
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
      };var element = document.getElementById("1216");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1216' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1216")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="0df81e24-21a5-457c-8c30-f28f95d55e9c"></div>
    
    </div>



.. raw:: html

    

    <div id="3112cc04-923b-4a17-9397-c73cc28cb8ef"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3112cc04-923b-4a17-9397-c73cc28cb8ef');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"624c1aca-8b59-4294-a4b5-863de4597f54":{"roots":{"references":[{"attributes":{"below":[{"id":"1226","type":"LinearAxis"}],"left":[{"id":"1231","type":"LinearAxis"}],"renderers":[{"id":"1226","type":"LinearAxis"},{"id":"1230","type":"Grid"},{"id":"1231","type":"LinearAxis"},{"id":"1235","type":"Grid"},{"id":"1244","type":"BoxAnnotation"},{"id":"1254","type":"GlyphRenderer"}],"title":{"id":"1275","type":"Title"},"toolbar":{"id":"1242","type":"Toolbar"},"x_range":{"id":"1218","type":"DataRange1d"},"x_scale":{"id":"1222","type":"LinearScale"},"y_range":{"id":"1220","type":"DataRange1d"},"y_scale":{"id":"1224","type":"LinearScale"}},"id":"1217","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1280","type":"Selection"},{"attributes":{},"id":"1232","type":"BasicTicker"},{"attributes":{},"id":"1281","type":"UnionRenderers"},{"attributes":{"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1230","type":"Grid"},{"attributes":{"dimension":1,"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1235","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1253","type":"Line"},{"attributes":{"formatter":{"id":"1278","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1232","type":"BasicTicker"}},"id":"1231","type":"LinearAxis"},{"attributes":{"data_source":{"id":"1251","type":"ColumnDataSource"},"glyph":{"id":"1252","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1253","type":"Line"},"selection_glyph":null,"view":{"id":"1255","type":"CDSView"}},"id":"1254","type":"GlyphRenderer"},{"attributes":{},"id":"1236","type":"PanTool"},{"attributes":{},"id":"1237","type":"WheelZoomTool"},{"attributes":{},"id":"1227","type":"BasicTicker"},{"attributes":{"overlay":{"id":"1244","type":"BoxAnnotation"}},"id":"1238","type":"BoxZoomTool"},{"attributes":{},"id":"1239","type":"SaveTool"},{"attributes":{},"id":"1240","type":"ResetTool"},{"attributes":{"formatter":{"id":"1276","type":"BasicTickFormatter"},"plot":{"id":"1217","subtype":"Figure","type":"Plot"},"ticker":{"id":"1227","type":"BasicTicker"}},"id":"1226","type":"LinearAxis"},{"attributes":{},"id":"1241","type":"HelpTool"},{"attributes":{},"id":"1224","type":"LinearScale"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1236","type":"PanTool"},{"id":"1237","type":"WheelZoomTool"},{"id":"1238","type":"BoxZoomTool"},{"id":"1239","type":"SaveTool"},{"id":"1240","type":"ResetTool"},{"id":"1241","type":"HelpTool"}]},"id":"1242","type":"Toolbar"},{"attributes":{"callback":null},"id":"1218","type":"DataRange1d"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1244","type":"BoxAnnotation"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"sHCrhJ7qmj+AsXzKeaSaPyBWghCCE5o/MEQChyp9mj+uohGpNv+RPwD/3ru0Io8/QMHPgQethz9AV/5RgKSOP3BM2sOc+nw/wIa2W0zVhT/AI5DIpHaOPwAZlPGuFok/oEEXIk1mhz/gXvLBIcWbP5DZ4GNc2JQ/gLqWWd9JiD/SF4psyCSTP7BL5/V81Js/JFO2oHbvlj9AL91sVcOTPyCQWPT61I8/YH8V4dpblT9g9NGQY22VP7gl6d7spZE/aH5imPXpiD/QQDW0uwmRPyS8d95WPIw/DnrYpeGWlD+AvwQcNzaIP+g3DVlaIJ4/cCuyDhucmD8yKnUMztCaPyiQFp9mY5o/8G1+hwNjlj/wfhNiIe+XP7Ah+KdEe40/0EKWT4dRkD/oMzo2kVSLP9Bd4fAU1oQ/oNyDe8Suij8AYrSExTCCP+QMH59zMZA/wKWdIVeTiz84nPM+032KP4CvjhucTYA/gA0bSBmgnz84SjgJCDugP/B3qpwta5k/INdxiJBbkz80x7ESm6CSP5DMLBh6m5M/QD6+3fBYdz9AI3zdup2NP5Z9qUKSU5M/iKN4yH2hlj+gnW1P9wqWP6BQ3QArQpE/oNyO9TYeoj9iNQUtsPugP8zS6u6pz5M/QCQGh6+Mjj8wDs1uLaGSP2g+kSYgjow/mBtqUFURhT/gTSN77XaLP6Cgx0cZmYc/EMZHcgkjgj/4xmx9y+6MP4DDBSd+vYY/QKBXl70SiD9CUPrZHkqDP0iWKP+GBHU/8CCHF6tVgD+QoTVgMV6MP/x7jAPD94k/mp2QyOvAkD9AUldKOQiKP/DlSOfx05g/OidE7W9nlD9QZk/2RBSTPxDGboGgFow/oBzun+OXhD+o4oQp47OHP3B4sRDiAog/0OIs5iutjD+QzDi8b6WTP1hR7WjbBHo/5Nx7vn2xgz+g2vIdrmF1PxBj73vEUJw/3B0jrleFkj8IXX9aPhmLP5BS2rJA8ok/WAUcNBgSqT/mmKdBXcGgP0iRWU8FCaE/3MxYTS16oD+IYsBD/kmnP2wGf1jIN6E/XEwRlExzmT8IrVvAaqiZP8CkYEh+4o0/De6sGtpRhz+Y1hFR796FPzDjtkFXMIU/INcxnPnGmD+giHRd2f+JP5QCta4vb4A/4CSc69uZhj/A9DEXHMqbP8jnOII8tpM/MKstPEJ9kT/Q2Hk2Nm+MP9CPsFLJ3ZY/jGgSeug+hD/QM6cx62mCP6DdCDDp13k/4IFKcxwbiD/QMmM4fjmAP3jDzx66JIM/oAJ2o2A8gz/AKYG85n2MP7xW0zOt2ZA/kGTZ3WPYfz+Q/2CRSqCAPwi1e7xrqqA/+JqtCQc4mT/Y8d6/uUCXP/ABhhlTBI8/AAAAAAAAAAAAAAAAAAAAAIDTkt9CU4E/cO/D22tplD8AAAAAAAAAAEDnk+MzApM/gO5cjYFVjT8AM73OxcqJPwAAAAAAAAAAAAAAAAAAAABApE968CiOP8AY+stUIZI/IN/LT4jxmj8QNHE3MjSaP7iBqDcQJpc/WH/xuGn/lT8AAAAAAAAAAED7xxJSzYs/QMptexUykD9g5sz019OCP0AYkrgJspo/2Dc8V0X1lz8AJA2L7HOXP6APNl6UfIc/wIPKyiaWiT8AZ4ABiCGCP0BfG7ko5oA/cGS9tNwaij8AO75ay+mNPw4OLw5nXZU/JLBQQtv6iD/wWnxUHluPPwAAAAAAAAAAADt6rLqbiT8Afm8VTlWOP4AEWnRNG5E/QFSD+pFOij9wpvQycvWEP3AHeSMyKIc/qJRlqwX/iD/gpF1uk7mPPwBZqAIE54s/CDCTAvxrhT/g2NU5IYiEP6DuZg9OD5E/ZHO+QuvOjz/6w8UTzISVP9AEIvVHDpE/QM5qhn6WkT/kM4QRl9yEP2H2v5G2CoE/oHnqD9/bgT+QYHTLXX+cP1akehqC5ZE/7KfkpL3Kkj+wpg2L9/CRPwCcFEfheIw/0E7PPbVUez/Q2VfqVgZ7P2A2z++lV3M/WGO8c9HRoD+4fhogEY2fPyycCUMjq5M/MOrNL4zImj9AP1j+xwCbP/CIIvZIFZk/oBkDpaZykD/K0/y0P66QP2BJTSrFJoM/OtxKoxDOfj80v06tAwuOPzAFm2DiZYk/ADV2Rb3Lkz9o5+/HlIuZPyh5euP5EY4/IGBTKko7hT+GlKzjrBacP6BaVlzk8ZE/KIiSmR4Mkz9Ahp53KwCDP3h7RSpiGZM/AGV7GAVNkT/kKN5CReCLPwCoozKBRYs/ALhLpMjFkj+AWo82yJeIP2BvwkEe/IE/iKfm9gCzdz/IhyfNGHSmP9ygyB3VH6A/kAoO5lZMlj8QRVUpOCWRP/DMcGINq7E/vMvWy4PUrj/g8Wgij/WmP0AAw5hAJaI/oHWbNuETlT+ga1FLUNGRP2g3qOLncII/AGDWGzfJjT9o1vfSYhG4P7SaAxgLE7Y/6J0dprCZsD9Cj6AWONStPzapMtFiTtE/ngGOQ3pRzD/6XSOrNIvFP/nvKmXGx8A/5F2Bz8DSyj+k8x8/0MzGP577/5jvTsE/RJ5z/3QLvD82AabWlpS5P75yhJHrkLM/yPLQgP8ksD8gapvjs/yqPzRuCW2s0Kw/1OezLRuhpD/ItUsO+qGdP5DC/RNJmJM///hNaUXOoD/Qmaugw7WZP+itbviCIJM/gMqwCInNkz8M1rXBjm7APwbnDNN30rs/Ml3jzsEGtT/a4O67XWSsP7BSBiwFtrU/Cg570k8Yrz8wiyyrXCapP7w4wNg+8KA/4FnbKftPsj+oidXtdNCtPxhbkJLo96g/qLZStCV7nz900AIZ0V25P8T3REtg4LQ/GkRlZZ53rj8gPfGCXjemP7B+59CrNrM/rDmaSYoXsT/wY0eafuCmP9Z/UzwGZ6E/aNEVAuUNsD8wctvo7AmrP/jaVtSUG6E/lKsGT5WGmD/QEr4tgt6oP/A2YKCWmaE/oAAeQLAmmD/QWzbeZg+HP5Ba4k51dI8/FNqygqCMkT/8IKLnrZiRP7DGMNhpIZA/PCRdwjEppz8gCg68VCWiP3APyDaC6KQ/cP0E+K9Xmj+6UmWyRE6nP/gL+3C9n6U/6HQxQpD+oT+gPuOOuTudP8Bl8zDBcos/IKuTlBuDiz+AMT/j85+GP/hDLD9L/IQ/MB9bghPtpj/sWf8gZ1CjP5AgnKSNaJ0/IDJosyhDmD9gCKEMRNe0PxiP2yoh8rE/IAYUL1/+qz9IlPoPsDKlPzAw0NioArE/c2a+7CHIqj9d5qRfMUemP1yKawmJnqA/gD3m3E7UtD8yG/hXzEixPyRQyzryaa4/lkvBJ+dnpz+Awy3MGb6yP3jVyx1qBa4/BCYDoNvtpT+MLm+BpBCkP0jr28aIALQ/eOb2mMnErj9sSgsJSWiqP2YwOQF6iqM/5FgM4KYJqT90ljL3H42gP7wvIqE/Upw/8OEuQYZNlz9EjEv7MyOUP4intFpWDZQ/sKs6LPpsej8A3BXaO0SBPwAuiI/baI8/YAEfzgAwkT8gisp3a32MP2CYzLNe/Yw/9ElWNPwNsj8kURt5zcOpPyw8XY7PvaM/QLkxt/lrmj8QwKd+VfOzPxqFgOvpBq8/pgSZ5BeMqD+wGA7YzACjP9jtmSuATbM/jnHIx4nfsD8wf/gdMo+rP5A8Afhnxqc/6LQk95DatT9UnjhugCixP7a3q2LXh6k/xO2A3px8pj9YyJXVeIG8PyAGPKz0orU/uIa94X3OsT9IlPK79detP+A0OnGz2q0/JEx/6m+AqT8oGY88FQSmPyjQZ3MrYKQ/0KbrwjxcoT9YpeYAPnydP1heLUs97JU/8KKIp1EAkj+QgAK7eXODP5CItcTpU4M/WLSE/wWzhz9gldFYBHSTP4yXlex+epA/cFjVDc9+kT8oaHel2kaCPwASBBhkA5A/yF0KTYEshz9A18EC8SeNP4BsBXeDy4w/wLna54pYiD8wiD+emu6mP7QW5NPNe6c/RDVtI7hRoD+cHULrfdaVP3Dssh2B/rI/0OEDlBobsD/syvrvP7mnP8zwk71kDKE/MLyicmQmoD8IjFGFq4agPxATcnoFvKA/CMPDVYemlz/81R5Gj7y7P/HJk6XYBbg/NKglF5bTsj/uAVy8MSWwP8hB/4fOFbE/gEr8Ta9QrD/QG6XjPeylP+jeZ/0d9J4/EEOoyUPNpT+APVuXKNmiP/gqpSeTGpw/3PojPOqZlz9YpkTzfCOxP5CJMlNCY6s/eM9Xku3SqD9iyWgqLMykPxz4JjVJYrE/fPeXw6oTrD/uk60R9+2nP/hut9T4CqE/gL9ia/Mfpj+wGvjAxWymP6jQVn2k1aM/EApL15Q/oD84IaT0qEOYPyDnwU4rHYw/YOUGmp7flD/QCxTq7CCRP5A7j+J1T54/SBItr3lolj8QZbnXUhWSP4Te/zVc+pA/7CFXFStRsT/CueAcF+awPxLkgmSCBa0/PMpNj9+mpT9wn+g0pmmyP3hCZU2p8K8/VOji0WW2qj9A6l0aVSigPygXJhPMcLE/aNgb2G8wqz+c9wlhyOGpP2RSVLEqEKU/YOWJw6p/vD8EUlgfMrS2P1ToLEL0S7I/DryCJhoxqT8Q9ib4nX6yP3iBUCnRLKw/vF4FMgvqoz8kQFemzkSgP6C3D3yWFqg/MDSoO3KepD/AMmhamGufP3DEynte8Zo/8FeeckRgmj/gR+NZ86agP+TmhxBTpp4/0PYZTbNnlz8gBfhcm4SWP8B8MlZixY0/ZF44Eb0ckD+ALVwla7eLPyQL2HlkBpI/AMNnMkAtkT+Ar1CYAX2IPyCsODzfUYI/gD5v0rNvmT+4Z1+wSNGXPzARGKxaEZE/mHHrcnpgkj+Q/2RDa5ymP8o9zEmVGKU/WOx678KYnj949fYf8uGdP9BDnqV8cKw/uJrnfXTDpT/wPZRZQ0SiPwhtaUgesZg/gIvcjKCinz+SZy10aOKYPyB6nXXbnJA/eAH3jFuVkj8IPPzSXom0PwTCwf0TRbM/1J2tttsOsD8Iq3kA/TCpP9DVaDXTGqA/yP8DMa+4lz9YrtN/9bSYPzCrqV+1240/IAm8WDyKrj/UfpcB8lKkPxghTalfhpw/SJCefX/tkz88Y9qeSmGkP9i161eLFpg/dFzBBTevlj9Q5/7JqmKVP1AmSXyzZ4A/EGxuI0bIiz9wEkz2PG2KP+DuAgaX+oI/aucfvZuNmz9AGExdgjGWPzAa1difi4U/YHg3mMgekj+gibt3FiiKP9CZ3H81tpQ/kGJ6I7LGjD94IOE+KXSDP5CEVmQvY5s/CNjEQl9pnj9UrIyTNY+YP3BrNNnD+JU/4OF2DIuHqD8YRKQOQ6iiPwwmc9fsdKA/EINeWbwzlz/wv2WFscyaP/RoRXvug5o/wrljr37Ilz9Imi/U2DeSP2AOZXUf9Ls/RPkc+I/Rtz8cQyl/HVeyP7ju11Jjhq4/wHMg7c/nvj/Qg+okR/63P9ydUWdsGrM/qAhmjiKKqz+AIUbdgHO1PyhiRyPEtq8/6BCGVXsOrD9AmX6XE9SlP5jbc1ATsZ0/yPhKlOLimT9wvQGqz1yRPwAXNgfRbYo/zL8NuFXAlj/YXzrr/1aVP5hCtjWf3JY/ELSbtQsDlz+OZg41XlqfPyAo0nR4xpg/eLaRAS+BlT9AKiPZ9uqLP5hKZZsQ5KM/AH7Chikbnz+gb8oYOjygP5TZOXbDuZ0/RLdN9dbGuT9CDAcl4Lu1P3qevzrimLE/bAP8nwj7qT8Yijxvvha4P7awvlOyzrM/eGe/DQPMqT9AfTo179SlPxDcCrvpvqY/JomPYYUxmz+GCg0QUYaSPzAU6FBcgo4/aDZbLK7VsT+A1KxqAWWuP1S/tkY3Rqc/1AArVmqpoj/4n8KFMiOxP5BKphMetqo/CM+yDjAIoj9oqcgoSGifPwjlkDJDDbE/CHc5R9nIqT/MRLbJs2GlP/CEY2Galp0/sONQcx4tpz8EFO9jzJehP0g7/D++M5s/QNx0wGSblj/ezoBT99avP1il51QRIKk/Vh5Ngkduoj/AUe0uR32hPxgdILu/NZo/kCZ7CHCkkz+4xf2AFY2UPwDJEWeKiI4/SOzb93kcqj943yl7svmiPzBTbv5epp4/8EMvtBlomD/4lT5ywhWzP0ps8qolZbA/DJ23hDW0qz/suo9MWdKkPyD2iRoZwKQ/FGYybEAHoz9YEevyZgiaPwicNHoHuZc/eE7gjQUzvz8D7pWFZFW7P55Ys43NTbQ/kB/2/WPerz8US+1ML9LAP8TMm+MfRLw/bju3MRditD9uo9P3gYWwP5gfVuVBZ70/ZuuSu3fotj8U0H8/NdOxPwyToCu5s6o/wP6Kvt17sT/I4/Tr4KmsPzQmERC5f6Q/6GEyr+kPoj/ANFgfMvKaP9AR5s4xEpU/hEBzhdCYkj8g5o8rDxqCPwgTL1Oh+aA/nAUdVjd8oj9Amt75YyiWP+D0VCFFf5M/7scvges8kT8gd7C5WB2TP7AY0GdHu5A/wHiNz+uSeD8Q1fztElSiP8zOIkx8z6I/6EeBqB9/nz+EFxR3Ae2aP5grWfaHELk/wuBtg07/sj/cscVe8kasP9jB3rORy6Q/qNZWR1FTtj+QPaQi/cCwP3x2vnaGMa0/TPEpr3Ctoz98ZVF7ovOwP5rwX3FjV6k/hAHUibu+pj8wPQKL6tKdP6DasBqVr6Y/vDCck45Goj8o0xyOGKKfP6A6kvKrY5Y/AIWFXkejsj+MosoIV2OsP0SvuItXM6U/0CXDSE7poT/A364iUgCuP/wWDsC4Nac/yBNKoA48oD/wa7HLpnObP1jN58fPaqE/+JigYvJXmj+g5d2m2pKYP3DxA6msb5Y/MFXi9ToRkT8AGDBXJH6GP4ivYR24QYs/wHdpqUfihz94QCY/OAaDP2DcGuTAAIs/cFzWcHBwhD/ASim/HsiLP/D1xDLarJw/jEESeOl6oD8YgBe93HCZP6AQOlSsAZI/AO7PjWx/rz/PyiulJIqsPwqDpPOIK6g/HECtZ+EdoT+gTXbmd72wPyjpMlLmj6w//Pc8R/puqT9o8BKD87GjP5hD/vpNGaY/2KPAlpTIpD+qtY/P/ISaPyj6+sKUS5Y/cGtIjWcOuz++RvNdDrC2P4puiIfamrI/CLhjmqJpqj+IqRSzlYu2P8adI0UAuLI/LMHavNOBqj9E8T2pEK6kP1BIK9NPTao/ONahXGTqpT/EqfFsC8GiPxxVLQrLKKA/wgvwWPldoT8omhFDZSmUPzBi5YbYOps/8JGcyZLtkD8YGeFCAO+GP1CBYqx6b44/GLmtmflvjD8ALAWypS6KP2hoZIXg8IA/IKhqq0ZKjz+QX9xxaGGIP+Ap+ouCEoU/iNQwP0+hqj/wV0JLe22nPxgjb9Ave6I/AFB6VFXjlz9Y2oduO8SvPzK9tK9yxao/yQU2pJMHoj+QxntbwF6fPxgnyc1TNaw/3M6+5xwOpz+c8mXTGPmiPwADaqAmOJ4/GJC4mPyirD9A76UIzqKqPz7JZkUFPqQ/YD9iWkuflz8AiieUVIu6P4rZqDOMMLU/OGUA8S1AsD9EViLaV5amP+A0unRMJ7Q/FhqyvVA4sD9Ij1adxBKoP4C5skkXSJ0/EDh5kt5osD+scgD3M9yqP3wJNSnQt6I/8ObSwVgenT8wQkZGXS6gPzgT+os03Jc/0IbeayrCkD+AHbR0OxeGP9CDqlEI95I/8OT3PTthkj9YnznF4ZeOP2ADix/mVYo/AF0XF/ceiD/gMyzzF26QP8DnDTNGw40/gLb1o9wTij+gdZn6RzekP3jBuK8HY58/CCKdyx+NnT9wMdCrvFyTP0gHGKshrqc/e7zBh0t9pT/z4aLkOB+hP3hVx8+buZ4/oNISgc7gpj+2L7SNxcyhP4hWVfoc8pk/YPww66sfmz+okzA38LyyP4o8qBOxlrA/iuuG84X4qD9ApdhVmXijP9DcJuGWqLI/HDa3HjD/rj9QUN8vY0apPwDtadU3K6I/gAO7El+erT9ku5IjaUSmP2Qyl13ZpqU/sDvRyikqnT/o0FSzII2zP1RnplFt9q8/WBElULmfqj/wh+EwSWemP5z/ne32PaQ/CFFyBOevoD+YNoMsl/ieP9Dhmxri/pg/MNrbWr4qmj8Alpvpak6bP7jL0OGnepI/EEK1uRUhlT9FjfrNQomgPwAWrUY21JM/2IlcfgBilD8gU2qzZtqMP2iQah5B3aw/SAiHSSsdqD8YO8+XVeKiPywhjFriU6E/OK+OMOaAoj8gKXjvb9yhP6rxV+o/X5o/sFUEjQzFlz84YewMRzqlP6x6THjpOaA/JAJLkDpdmj8w74ywupKaP8AMvNoNaaA/yHF8eUezoT9SgpgXvSWZP7AmUO06hpc/GH7Eils4sz9CkD9QH8awPyD5Sz0vC6k/vOY7fbaEoT/ghQbnaTGzPwj8rv0vU7A/sAWG+7KBqD84kb+/GoKeP8ByuSv3sa4/iDGH8kEHpT/k3hD6saSgPziQlySGAZs/xNEdBZjXpT94PCiacZ+eP0D1eNPdVpI/cGRfnu1fkT8oF5qNg0aJP8BsLxUvLIY/4IGUiKfZkj8g5WSaA0KJP7Dj+E+Ax5I/EL9Z8GLQkT9gsTm1YIeRP4DvnW5gnJI/+PVqtS0Dpj80W27rIp2jP1RZunP24aA/0HIlIgE8mD/8XNjlCIawP4d6C+pWT6w/xTVmAoM4qT+QpF6snVOlP8iS26glwaY/rjGaRHSYoj+qujqM/FqiP1ARS5+wtZ8/sMIRYfuwqj8oF0tBVc6oP8iSiWpAVKU/yIagcNhYmz+wQzuYy9axP9KHkUeDSrA/VEIdiGEEqT8MJtP+Y2KkP9CtNZsSyaE/TIcSFrAJoT+gSN8N1+2cP6j6jl33NpQ/QBzh2R6SlT8wTF1v/1mVP6AEuI4pyZI/mBBPSTgskD+UIGabJBSXP0BQRxkn1pQ/+JsnFcI/kj9ALK0BouyIP4CI7vihbJM/cCqq4JVqjj9wvRHZF0eOPyDXgsJej4c/YM2tJ2q+hj/Q0xP1tfWNPyTFiynCYYQ/QODTXhIhiz+MtYlkvjufP1hhoOKfr5Y/4DHoXI5emT+QjhczmxeQP6jO1IhfXKM/+CA6V5vQmD+Yw+CaXMeXP4Dy6UAiT5M/nstf+2qdoz9wlhrsZMWcP3giG/5JbJg/kIoj3h0Xmj8cnnhE8TimP1ggnQqljqE/UGYsJCHamj+A/bj5fvGRP5xJjqKnMJw/eAazXcFTlj+gWnShBw6SPyDAam0ACIw/ILM+neyvlj/cEs3Gb4SVP6rgtmTfW5M/wA8JtyvUhz8gSfBamo2IP3CJ2SgexoU/UDKboqybiD9QqVwzwUCBP2DFYVf9oIo/5NoBXvtyhz8EgT+nq3GJP+CiqjVI7oM/EP4fBTKPnT94+FP8tFeYP+KPQsO2+o8/EOWu6esTkz+UHrVmX1qZPxDTesDo25Y/KGSJ6OzQkT8gs4BkoFuDP87abmSYZJ0/cCHp+z6vlj98kwtkw9+YPzCrDt0fx5A/InsiCBsomj8AL3Q4B4yYPxgou81aHY4/IK/Xg4CWjj/1m4vzJ36VP3h4xBiICpg/OOE/qbDokz/giPdCAXSPP+SqL7CIAZc/0FHQLKuxlD+AoymyfPeNP6AgVy0yQoI/rDmaYsSolj8IJ0kXu2GRP+jJNYILfZM/AJCJKrwEiD+s2uVoht+TP/C0Qw6iRoc/ALiQYIFGjz9A0MeyONKLPxxqHy8FvJ8/eIl4gMxRoD/YcdyPA5SRP3DNfmVn2JE/INsYMX2mmD80/22hSgySP/Cz7t9qio0/oOW/7XgdjT+gbzmmjDmEPyDSolr6wo4/gObbH8K1hz+gk5f4H1qCP3ijTzLQko4/gLw7zHDMij+wfIt+H0SBP8BhXox9q3k/QDji+xGaiT8grb8DrAuHP0AkDQ1Idno/oOaBvUVtXT+sHEPpMDO1P4Qj4SbqabE/0CpwByiTqT+8pJ3o8tejP2jRlylr1cg/ahGcsOBSxT/aJcBGUEDAPz770WYyhLk/wFlj4G6bwD8GWffITfe7P/RjrE0KtbU/umVC5gXwsT/iUouFAC3GPw9yRgSe08E/iz22Dg5huj9ULuvqMZu0P+5zLEBgAcI/AKoe0rKUvj/KYnMcima4P/xw9efcd7M/SpKCG5ut0D+mdqIKBxXMPwerTl5tS8U/nB42eJ6dvz8MMOJBOyC7P+qLQ/HzbrY/5UNodVINsT/oZDJWdN+qP/7Wa40t0LA/xrNI3yCXwz8sEmz/0nLWP8a0NZmr/9s/LrlgU/uzxD/Ampye5+6+PzyP3DCp8rY/NFl1incHsj8cL/bt4z7hP+nlBKyznNw/DkVvROjy1T90jhUZ8fDQP6L2CFcKBuI//2NOieBf3T8beLEzAqzWP+ntMXidUtE/ILVVR42wyj/4NCO82B/EPymj7+1Ru74/TPgXrv0+tz+IL8xKWQKxPzRTTM1yrqo/eylDIEX1oj/gDZmKJNSTP629FtrqEqA/QIsS2dTrmD+w3dSBHbKVPwAPBcZmBoc/pyb3NDEz1z8WGcDKOe3UP57y+x4MLtA/AlSsSM5YyT8o9K7omUjFP7Yj8QVTdsE/NKW7NPhMvD8SssDYIYK1P3BT6gSMTbc/0IMWICR9tD8WvxuJJ0WwP/C1QHQ706k/MIldnoWqsD8s6mii70qwP5gq54aIEKk/ihwr9cPhoD/ApyNvq2+cP2De8ngoxZo/0EFjfsj3kj9gOYggJ8GJPzgelHJLA6Y/kaGpPuJepD8SnDFriziaP1Bpm2Dn3Jg/OA2vVGfwnT888YAQKduaPxySw7mG95E/wOZHrLwLjz+0U/VwGNecP1BcQanyaZw/aOrAjRP2mj9wbZYaL02WPyBcf6DlW6E/GJLkiS4bnj/qceTn/mOaP/DeSrj21Zc/CRLB+gVFiz9AiDNK38qTP5Rzw1bldqA/sA0Mb5hopT9y5QNwY2aiP6imywfGmp4/eG9WlKe7mj8wmX8ZNBOZPwDmeBPFlqE/mD2wO28kmj9ovUHkUUaUPxi5vb0yrpA/YLZalNLwlT9IYp9ywNWTP2C2WmcOBpI/MN7ZjLrtlD8gM4XlpIicPxjRMEorzZc/mDO/wxcclT9AwsZChKuSPxBLRuOkYI4/QMhc/koZkD+UqYYcUumKPzBL6ITybpA/gtp5iQGOoj84aSP3ELCeP/hToxBHPp4/gCnvMQNtkz9oCohgAQKlPy668oQtSqI/2ocj9To4nT/w6ZNalhOgP0D6Drfrzpw/ICen4XAynz+QiZt6PZ2WP8lm+L8/iJQ/IMvlW2Bjoz9aohKSeJqgP2hIUfKOtJo/mD01mwG4kT9g0axPkkalP4D2ZPHNfqQ/YFqJInNdmz/0mrqYSvyXP6Da4OSPKZ4/SG1oakHDmj+oI+94QCmUP8iqPouNxog/MNCYgb0/nD/wc0ItRmOZP9UiztGtx48/ANPOh0a3fz9YUjOVZ+aWP8RaZ3ZGtZY/ot+e/ymBkj9QujJaAFaVPytH0hB0o7U/QNmYd+LHsT+WXmc3OQmqP5ha1hdCUKM/XKDjBwSMrT+0dfbLZXqmP2lVPo1N/KA/wOJSCub8lj+lUjvykk6mP1Q/hyb3XaI/OCp6SZZRnD8QI/oXJaidP9I1eNXAWKo/mDy3GYqUoz8AwA7aS0WgP6A+fTxHKZM/AItR9q2ilT84gTr9uVmQP9A4Nrku9oc/wD+rcJUQhz/ghUoyrWqXPwgaSJxD/ZA/uDcDsJMrlD94qOvhp4yMP1jEKjmAnZQ/4AiMGGWdkz9QiKbrNmCVPxDrW1dM4ZA/GMn24AK8kj+gaM88gyKMP6hfA/yQq4s/oDwLWn5Bjz/5H8Bkcn2eP2ApSLRGtJc/KFDrKk3ckT/g7Z2D2mmQP1hIKUXlfqY/5i8C3CQOpD+i5tfaq7WgP6CxrbBeOKA/ID0z1GrGmT9A8g8I02uXP0CkM786vJU/OAaSHw7+iT/4poiTQ/+sP54ianzTJqk/ouZf/irQoz+gSvWOLSKfP+DUIrKdMJ4/4Gt7AO6PlT/wGZlPsGyUP/caZZC8V5I/IG9nC5ZFmT8w9fxHVqyWP7C7WvIYIJQ/OMUsv0takz+c1wkpyAuyP7yi4u5+nas/8KJda173pz+Avz0pvDOfPzhUxc+3V6k/fLlwUCdFqD8T3opN8ZCiPyBPKdBAVZk/4B3egtSxqz/gTGAf9R6jP0iKoIdWJaM/4PLX8pRroT8MtKUk0GGtP6Q6y4opxag/esAuKBJtoz8wdhZBVRSfP2d5mnPnJZ0/yEZsKxIakT/QwMS5DVSZPzCAMqYHSqM/EkAL3765mD+oGhbOErmWP0CCA41rhZQ/wCLC1U2/kT9Y+Qaja+SqPzTVRxcO+Kc/QEAePqACpD+AmpKToj2dP6AIvNGf5qE/5J34A1/woT/IpghcUxGcP2hAM854x5s/hCjPlKsCoj/ojXKYGF6iP3Sq8iJ18KA/0DEBAplYnT/gBMqfag6bP6zwgQ3YC5Y/AkeMn4mjkz8gsxbX7VyQPzhKX5NrYZw/+DqauVHwkT+YVVMwXYeTP2DJLm17yo8/1H4YhE1zqT8WRrmSwOWiP8bEL9IMM6E/4GhQi7jpoD+Ay5XQIyGSP6Cns7rD54k/wBxFR5oceT88BACVa3p6P2BtVko66Jw/7OelOnzmlD/4hgZ+PmCNP+DnyGyp84g/UOA6z5KLoz+w2IGgb2KfPyBSKDdwN5o/EiUxQ2r6kj9QR5sWto+kPzyfdcJ/laM/xP/KuJlOoj9gG0KWSPKeP4jUoJEqvKQ/tGJoFJ4toD/UXw1SQGafP6hETc0At5Q/ALO0vpxPlD+S8PevHjCYP+QlC3eh7os/EH9TQNJSkD98r3jgdvuSP9DBM4wmfI0/CGSXxw0wiz9Ag/C0WkaIP3DaLAkosZ0/ALPeZ36ulj/cpivFzRiYPyBsPOsmAJY/dM6Ahwp+sD/gXFC6GGyqP6hn/6Hhh6M/kE6BITtLoz+FpyG/z3C2P1gSojiAeLI/bM0Ltfnwrj8oTkp0z56kP+gKp7MMP6c/bAiPKB/woj8EsrJQTx6iPwgeb3MBf5Y/8PkUPWG8oT8Avaz2NU+dP6BMNS9jhZg/CPQJX6dAiz9g5y1RHxikP7SvmOUnfpw/gBJF12awmD/w/I5L7rOSPwBoRaAjHZE/UNfmgzjwjz8AzhA5ehmAP2CPccm7nIM/vnYrgdRgpj8oGvjxZ/OiPwhUt4V7k50/cAB7Beynlz/+A0+SxvqyP1IFQbuPm6k/tpVhTnWGpT/g3KAgJHiiP+C+tsZ0oJk/4AG8emCSlj9gbNeQhNmWP1jZUijrsZI/WJSSkQVZqT9eyAsN//2mPxA9fvS3p50/8JgOQflxmT9w3n/flmSoPxjRJ753naY/CGk392y0oz+ERwkaz3CZPwCPTFTKkZ8/wFm5In91mz9IApqiIr2dP6zEePhXipI/APAa67dmmT+7aeEOEsOSP+wrw3biJog/wKJwAY2fiT8wXPnWpGOcPwIe7Ci3h5M/HItBXAB/jT9QGXMKJhuQP9hSlfBz2pY/EB/cwKLhlT9QL9YF5tSSP8BVnWxH+48/WNHd3+4umD/UAYF0IcecP0FJUKXmIJA/QLwhSVsKhT8wnONND2uOP0CqJ8a57Is/OCMaQXCqlj8gz5l2E9ycP/xlAb6JiqU/qMsfdqJ/nz/YCfdKmaKbP6DYgTeXMpg/mAicZKhRpD+4E02D1J6jPwyWbPMM1qI/6K8WyT75nz9groX9DMCRP7DYmwKUFJE/YEM1h6l0ij+Yqql1IliCP3jYV0CljJ0/CC3vbneskD/80u4JFTuQPyA7nLyxqIg/mD5pE6VUkj+ElCZ0cRCQP6bF5Blf04A/4LoXsN2tiT+E947p+U2WP/CStyG4AZQ/AD+0TaXwiz+gr/0sZhGEP4hquGngVZs/UE3gVBqcmz96T/LBMl+ZP/ClyMb1oIo/gNSs6gXMnD9wQhrc8mOQP6DvoOJru5E/JnpPPNjZij8wdZ+/ZmSnP/q0XLVmvKg/3su+bUy6oD8YBqjCfuOZPyDy++EsHpg/wHO6prlvlz/gjCAkpwSPP84hs8g9ZpA/sORTmnVBoD8oB1QL+siXP0grKmYnqJA/OHIhmMAoiT/g+O4YEBynPxD/pnWzxaE/mwxqMNpooT9A7wqSykmYP3hye/LgpqE/SEBA9kEOoT+Qp80FTg2IP9ACm7Q6nJE/3Ggx44Z+rj9sFpB4zoapPxhVQitO66I/gKWlPQotmz+gWRjkNVuqP2paH629FKg/4J/j/h61oT+A5wuAvDmXP8j8rKWz15A/WFIor/r9kz8Yc7TIwlycP6BOVyHHQqQ/QDDTfaOwpD8sQRtQHGGlPya8Tzb8sKA/YPn1jb0Ymj9Q50cBOFuYP9jdEBrj8Jc/UP6/XBI2kj/QqrzeiDuNPyC2ZB/bYZ8/yPgS94JanT8IM+ZSFP2VPwzkfN717pA/ZOeXJNPYoT+0Ec0GoK2YP/7XCUs2ipE/QGDKvyHOkz/Qfdk+yf2GPzBV9EsYiYI/krW92Elchj8gcx2sVzGCP2oHBbdSKZg/ICIYpG0jmj+YcX8zhdmbP+ATu3s+d44/EIC5vNhMqz9spBclDVqmP1oMojggBqE/SEolbbGVoD9AUfTT37+GP4B3zO2XzpI/QAELrqXugz9GfV0QqLaCP3Aoj0d0iKs/6n4ou47UrD8Y1ozFN1CkP9jdzKM2CJ0/KDX/JBbXsj9QtRYp+zauPxCQhgyUMqQ/zTiJfEY9nz8AvXuE3i2jP8C1XecFz50/WEa8O+/plj8QffkWS2eRP0BERLXeGK8/lPAconlOqD89S1GZzr2iP+CWccgwiJw/OPuCT5MHsT8gqsYIyzKsPwdqK1nTk6U/kD5m0N3zoD+wXS/jdoOXP5C8iv7x+ow/pG8b+zdRkz8g7XeAIXaCPwguRlluDJM/bBq6nWPpkT+07sUvHC2QPwDdb4GVQH8/qPNihqfvmT9Afa3woxKDP7yn5WJEKJw/mA+bwqmpoD+SVaxubL6RP8gH3jWCuJQ/uNW0QLImkj9gnDMfc/mNP/CPae8LU6o/wPW+EZTooz+kvbfQpqagP4CRi3e8MpY/ECGWg6dPrT9swoVJ0SGkPywfx771K6E/pF4T/iHXnD8Y1JGrjbKoP6BdczxRN6M/nzMWBJwuoD/Aw7vMGryVP0B24Fafdow/6Nb4e+uqjD8C4re8UzKCPyDHbQIhi4M/5Ikd6+P6nz/YAfFqKxiaP9iKkdqKh5g/ICBDKUovkz9UM43hdfCyP0DS8lm0Cq4/pvn7uAgsqj/gLi9fAR+gP+D1N0KzV5w/oN1qB0UwkD9wuXUOAwqSP8l+KBA2aIQ/mCbzjVfkoj9siBW2L3uhP3iTj/W9f6I/UMvh8tEQmz9gcWaRKgyXP6CtzpPsNZw/oOzaXxBdjz/ktgShKdOKP+BqT6pIGZE/oNgBzDHCjj8oQB4HEQmSP+DSNHNr04Q/IKc7iivPoT/QKyvHl6WcP8YqHWAYoZQ/MNTfS1scjz9Ij9qPG4SkP1YLAKHhR58/l5OlnOfnnj+QKRc5WVKbPyT/zoUg9aE/MNcb3IHFmT8A2Dg6RoGTP+BTRDmWzpE/RK5xo+gzoz+QJxFi832aP9h2pk0PF5Q/gCU5zzxCjz+W4AyQJ3SYPxhArOAJl5I/ZMiwraUJmj8oUjR7qsmiPx5ZU+dGaaU/3NlE0i/hoj8EtPLnAOyeP4D4ndOG2Y8/EGQuP268pT9MF2KyxmClP6CrceDtrJs/RP1uyTNSlD+gB1AskgGdP0BUuE0AQ5k/MPKvKKD3kT8QgA4sHfuPPzBUQKYF3pE/WJsbxlULkj+Qe9vMqHKLPyD5TkjaY4o/4O3CzWulgz+gPOKoZuqHP8GqNxn2K4M/wIGC+k9Xhj+6VnoBNAmYP6iuqpqbYJA/OCYbrnKCiT/Ahhi/ct+SPwDyVjNWz6M/pInmtDVznD9IUHv2CAiWP4AMr7v+Qo4/4Ny44RqZoj/4u16LXkWhP7Bk+vGGpp0/tf04VzAmlD9I5p9P1PioP5xgLjP9Tqg/RCcj6X/2oT+0ii29E0ygP4DtTlBL6J0/wJW5/SHImj+Q+52GXSaXP0WbFXNQGIk/IBVUq8VHkz+gFXc8RvCRP6gjHxfbJJM/SAvn4vpriD+okLZEEqCmP5y+B+Px7po/xGwDImeznD8otvl0sneWP7BghDHmopg/nGuZpNxqmj9E8C8JblaVP0D2J2rKfJE/5MKfdsCQoT9o1kYNtXSWP4BSsgbaepQ/8Na4obujkD/AetMA6w6dP+BsjYDbkJI/TYMdqDkvkD+gyvd6oX+QP/ZtXyDMmZQ/IP6IC35clz9c2rGPWBehPyCCA5TDFpo/uMtJFVeLqT/sktR5eMijPwrluP4obqE/AJkC7yHalT8g7ktnosedPwDCaMVW8pg/QIfHmdsAkT/ATySYeYmRP+DEZ0F1kKc/eBUz+JINpz9QrlDXUHegP0RE0K8NfpY/6BtLx9qooD9sNWDsdIKgP8pjPw9Y+pQ/kKYqDV+/kj94g19z9TWWP8Cjsv7nyZQ/akDgCQX+hT9A6UCc+lmGP4K5SvyZ05g/ML9jACfIij+4PvT64sqDP4DKE7WYnIM/ZOOIjA3goT/w6VHuWHWdP1aZSu1o0pU/0OXybOaAkD/AlmF5FhebP/AXEx8CoJ4/wA+37JW9mj/FJ5lZENaMP9C1zmTvha0/9E5QKPU2pz8oLeF6YUuhP2CjGA1zyZg/cHyYrKyPqD+oqGFUXGCiP/CpBubFo54/DHgGYcBClj+gz/pvgiafP0DSOendf5w/iNEqcFGplT+sF90c4YKRP8CWXMLhTLA/j7pIuZ3Upj/Y8hSaubiiP0jA5lvS6aI/ZCNzUlzBsz8UGbUd6FGxP1EsNIcmda0/CN/F/NMOpz8w1v1FrfGrPyhOIFyRWag/hFkzwiUgoj8Q9gHvXbSZP+BfBlwBtaQ/cM0AzeIanT/blmdxoPOXP/A8oyNjV5M/kvbU+XIRrT8Q8d4VdNynP+YRGJOp4Kc/GFGTDNrGpT/2WhxtssStP4S8v6WT2qY/GnbGYnlvpD9A/wXzSoKiP2hLIz7jtKk/kIxDz+fupT9k5SFWSsqgP7iQyvQvoZ0/EAx7Ws4zqj+gAyTfNrGlP5CMR61bp54/5nBsbfismD8Q8ZOmeaKjP06REiZMSKA/oqjCKrFvnD/wodT18W+VP9CKbvi22Jo/jE0L53jhkj/e/okCnSGWP6A3cQNbeI0/dpmYJKt5oz+44RlT+2yePzyYb63pZps/oGy1ozYYkT+AcE9RknaiP5AohT1kZaA/AhMCXd8jlD+gv+VranSQP4C/K4U6IKA/EOQgP8gqmj+QM648LImVP5DGOJtDLYk/QJWmQxfmoz8W6kDcgOCgPyDv5BUIMJs/eEEaaCL6mD/gLOaAQr6pPxjqCbOfOac/2NuH9Kkmoj94vFJHRaOhPzAtmmClvas/PGmOpUDtpj+wijiPXY6dP7j46mJQmZk/1GCq6teMsD+2Tihgq7apP7aF8pMUs6M/WAgbid+doT/IMYpQzeKoP5Jc2M4S/Kc/q+tI2LMonj8QWtq5bpuZPxjHoZnj16M/FCHFrPfhpT9kt7IiBiOcP4Dy71gm65E/8NQ5FNH9lj8g6vQSxICZP+ge3Ya1wpM/8BuKChxUkz9o2XtK37OxP0Q7W1J2iag/4MOEbDB3nD/4H1F5qFKgP6Y5hzD2dKs/BLJ1Woivpz8WFAQfw5egPxAsLnLuMpc/cOOos15qmz8gaOGKxviNPxD2vPbXyYo/gMwKeSI6hT8A0/x8m/yPP4Ca2gjKZIc/MF1W2zhsjT+IoMRJEWiJPyRZxb5LY6I/gI/hXTkxmz/eJEchV7uZP9AXB76D75I/aJGQ6tfbnT8kgAhaWyqdPyg3ED9LgYg/wGo86Wbckz/ICC6H92SCP/CelxXT74E/KMFCsFjFhj8guVE5xGeHP7j3erud0Ko/nKJlAFkMqD8Ou9Q3wMudPzjq7PUUNZI/0EkAIasNpT9Q5Kw9L2+ZP4Cudqjfm5U/RrpWNE4NiT+wJddk8uKfP3AWlb3mF6E/qG9M1xG0mz+4rXATk16WP6B947E//pE/YB+Urf/qjD/A97Db+aGNP1oMc4H69oo/YHBtteVDkj9wkiOvI4ySPyAEtTK7kpQ/gNruc9A0iT8geFBSznGaP8gjcGrDdZc/pCdy2452jj+gqYLK1hOIP3BNE69GjZc/stMIlXKLmT/xZ5vcygGTPyAMqjnF5JQ/CMJnv87mnT+w8VLBruueP1bERcb5HZs/gJHbIi2UiT/w+m4FcZeNP4jd0Gi57oc/FEjvpAsdiD/gPvc+w2iFP/floDsy/qE/vBN/r51foT/cJYNcCj+kP+DKDS2Diqk/RBPpFGLvoz8Anv6JH+ubP/hU7+koVJY/QEpioLLkkD8QuzEZl0qoPxg0/FlKLqU/JBLHU2groj+QRkRt24ucP2AClX69+qQ/YEeulDWomj8QJHjXlGKbP2CugnmJ648/eOpnTXFGmz/ESA/ur4idP8425OeD4ZM/MA6Hw1Cxkj+wil3v2MuEPxCixCHVpIk/I20tK1YEgj/ADGrKC5d+P4DoZ5pkbJ0/oFQORe9wlz+4F+1j61SNPwCfcK/94Y4/AE0SRJmsnj8MQQnD7CqfPwkbiLquYpc/gOYw0fsFjT8AQcMQ5WuEP+D1kDxVbos/QB1P/mvHiz/M2PUxdZiIP/ALV1Jh/6U/4JZO58bGpT9chKXiBGWgP9gq4EXidpQ/UGMY1yStpD/wihLOocmhP4Cd3eiDUJ8/ZHIm+prTnD9gmJmn7K2XP0DC8gSDC5E/OGUaJ5ICkz/4eYQnHkuSP+xfB8EX3LE/bLdX2FYfrz/ARHyrp3GoPzBiNJfvB5s/YAfGt2LKpj8peiWbT/amP0MSBeJfjZ8/QHbKGsEBmz+4051eBkabP+jCtInj+JU/UNjih6Q5kD9gnzxwQgeIPyCXVrJA6o0/rJyc+/x7kD8YR7WQ+3mQPyAbYUu5coo/OAhtgDjcqz9Y5skNxSCtP84GqQcJ3rA/UFdDWp7Ssz+ipQdP+TCwP9yiQMJ09qk/6OUwe1LGpT+YFFXH2EuhPyAd3ZFsYqQ/wDpXu9QxoT9IX5O6VAaYP1SRZF42kZA/YG+fPVfglT+gbzhcQkaFP6Bs4FtQE5I/EP7KzznLjD9oOPbAqYyRP+j4WXNYU5k/ZutSFGOwkz9AdjphjH2SPzBa4xCTCY4/+EkKewrxhD/V2Au8xKyBP0Dg6JlDiYg/JCSkxGYTpD+wUaMjM5+ZP6iazPBg9Zk/MO74xhlXkD9sdze71RKpP8b6UVU+Dqc/zD36RvMLnj8ouLg+jBqcPyAvY/+jeps/AIJMXEmznD/gFMdS0f+XP/W7Owvg6pA/qAC/GYa0oz8IMB6arHmcP1DeDivj6pI/2A5kuDwGkj9gSk9jHwCmP7A8HlEXVZ0/MJoxFkRElT/Yls8VrFSRPxDuZXBt0aA/NILAH20KoD+YmD4pwgeXP/C8siRXiJE/AIpJPPOHqT+gssBrxOGfP3CfUwTeq6E/IDYMevDomz+oLRca8ECnP7QpNxPmVqA/H/QUq6p4mz8w886VsSqSP1DqsI3sqZs/OFaw1c0jlT/WtBnJrXeRP+AFNXADWZQ/4JKwxqoZlz/wtiz+AuaTP0rShfiY54s/wK4bKr21hj82SvSk7DSQP1jrKHws0ps/Ss/qoM3lqD+g+ZlV3/2uP/sJrN6gpKE/yGjilA14mT9IRqUDeB2aP4DebPfVwIg/4CjPmny6rT9UNWEUtMKrP2CTc45VHqY/fCzg4XPFmT8wlk7zoummP4Qn55xbA6Q/gPTP0pANoD9MTvf9rq+bPwACcdSbGp8/XIqTEvvamj/qh6ziF2aVP0ARqh+wkYs/qHV9cH3ElD/YX5lbxviRP/t9rbzYFZE/QDnZV5GJhT+qXyZaNQWeP6AZR8GfN48/QLsiyJV+iD8A/hAujcN+PwAt5F+wkaU/cO48eBdpnT8y6n+dJ/eXP/DaNcj1kJM/AAQkLOjYlz+QfbxK7DORP4Cd42+WHIo/BSidpFB1jz8APR5y8o+bP4Bi+UgaipQ/uAwbY3O7kj+AWjKTZrCFP7BEc1pjYKM/8OypTA2Ulz+gKe5PiviVP0QvhzbvlIs/gE97xGYTez9A8UQBxGuHPwCh5bhVUIc/AL+qB7T3hT+wPaYB8m2gP7YbP1EiP5k/eK7J6ATslj8w09e6PRaSPyB/Adrq7YQ/ECAhCebdij85SIUkuvaRP+BOQir4VZI/cDmWMn+InT/4wBbaArKbP1IF428AP5M/MBkpGVo7lT+QkrFHPzigP0Q+QrNmqZg/iJBejJZclD8AcE0tuyKLP2w9KWzSxpo/QO6IKnFBmz82HIOeYBGgP4i4e1EVfaI/zsweTGnTnD+geM/2Md6bP8Sd6JnVDpU/0PS5lb9klT8wgANxZ6edP7jzO4yS+pM/YDm6Ul4UkT+QDklUOQ2KP+DoA+2fbKE/wNX/S/N2mj/oOvLiPoeQP+AwE1wNYY0/fAwCWUDqoD/cErWtYzyYPzWJFpZvSZI/MFaMYJ+EkD9YL6UBhNiQP3BsB7rmX4g/kjrizi4dfz9Ao3GI1kB8P7BfPIZ/95k/KOxRpL/5kz9Qtg2qqleNP8CZSxmT6JA/gKzdFsqRhj8K/w6RhmmEPwCTM/6+gYI/ICnMMXodhD9gzn0E7X+GP282MzpJ6ZA/AE8Imhv5jD8A7yyU562IP8jY2FKeepQ/2Cz3SVJHkj8oBQOx6YaHP8BzQfHYDXs/AC5+K/ptlD/oD1QArf+SP8CDkuQqj4U/UOmD0OqQhj/A9LazVzqIP2Cw+iEqqYQ/YLgSYeNUkT+AM/2DvwqHP7Bl7lsUxpk/qJ+6iNS3mj84U9BO65eRPzDbsNTLbpQ/ENAVgrX4mD8sFmfcZfuaP57i4nOjLpE/YArbPUzYhD8At6MifXWPP0AwFPDLF5M/gG5A/TaljT+M2Q/n7A2EP2DuazkZBJo/4Hvohp9rlT9Q5dHLCCSQP6jQmineVYs/AAAAAAAAAABAhdgPJi2OPwBG2WIkIIM/AE94YFErjz9wD0QGp+KiP6ANMo9DcJs/wDrtJiX4ij/MCy1KFGCPP7D/hzp/sJI//Dg+n4xZkD8IdM3bKRSQP0hrcmlnTpI/QBdGA/+rjT8QdfREJ8KQPwAT0kmKuJA/uOdpprHfej8AAAAAAAAAAAAAAAAAAAAAAHzsQRXObj9A0sRNjC+JPwAAAAAAAAAAAAAAAAAAAAAAl1QCVaJ+P8BvgLXIhIQ/AAAAAAAAAAAAAAAAAAAAAOAzebo2vpI/MFxSXut2kD8AAAAAAAAAAGAKbTYUb5k/IP+lm3JplT+QWtiqpLCDPxBYsSv6oqM/zJqZRAQNpT+A2q/WckuhP6bllu55YKA/MJhynEjDpT9YTUZaRlGiP9jKb/RRx50/eGk0cHYJkz8gi5snJtqZP/mHkDuy9pI/pmu5GpoNkD+gs0HbInGNPwjfu4mj86A/0MxInFYnoD/YcfkoqFOWP1BQJ06yeos/pucIrEyuoD8Yi/0NqjmgP+R110+oq5o/QIiRTemimD9wiRVx0NOhP4DYt8RNAJ4/IBl0yUPUjz8UaNgzEZ+LP3gRQUy72qI/yMWFspwZnz/IdreusWKcPyiLQFTMo5E/AF6gYv3Yqj+wb4nA40SoP8AnYDh6QaA/yJKh+KPrnD8gOqpUnTabP2i7ixjqUJM/KHZX8LEejT/gpTYSzu+RP9A09OQbapQ/sEw78iR2lj/KK+BfyuaQP+Ca8LlMkIc/aJDecfmDnj+gRRgUG+eZP3iuBGttYpE/UNydyxZRlT+QiPbXNHSiP8AWbTpPYZ0/8DcKyuIBlD8QNlptuAqPP242y6tJsaM/0C+3284CoD/oiVCqgK+UP8BSZSNCSY8/gCjFp2Almj8wii7q/HCaP0CO95jI55M/sgQTObI+jD+oV9ksKX6gP+hfBvLmFZw/mBE08osVkz/QAlzQ9GWRP8AIzDyh2aI/2BfYf0kvoD9gnljxvNObP1in+jtgZ5U/MNzF3iPHiD9A7GA0HAGMP4AriSQCAoU/oO+Uahp/ij/AIxq+kNuJP3wEPQDPeJU/doznmC22gT/g0GZ04paFP+CSdKFvEKc/MCZzZJ/Zmz9aeI08FkKXP8Amvam6DY8/UJ8BQ/VdoD9YQhs5WgSbPygRqo3yQpQ/mD8SsYRwkj/0gY/g7emgPxBfdE8z2Jw/mFseZNgemD9Qla4LBqqQP4DAl/j8JJ0/ILHzcFsljj9w0TGJETGWP/QoRJSAZIw/SA9D1+6hoz+E0+I1DFyfP6gDUB5Ag5c/0DMbkIuAjz/waPj03gyxPwgltn+Pdqc/YCld7eVzoD8CgKpi2IigP2zJl2sDXaA/kHhLGaJQlD+A/YzGYuGSP6CvxYvVyYg/KMQanQVgoT9Y9GoH8QaaPw41oP3oSJo/MLSz0GOMkT9u0hBg/SOwP9gCgJXJQKY/yjdLvqWGpj/o7C48iFqjPyCIEf6JNb0/KiVp2Qh2tj9+jsSWd3ixP1jZD2iqz64/jG701km2rz8smoZ0hOquP6A9s+x6aac/sJC8nN1WpT/KCVbK4SqjPwAQZCPCW5w/+N72MS3Hkz9AS0XTejmRP7bDHHenBqM/kHAna1gNnT84C9xC9G+dP2DLtGA6VJQ/mkKqWHEVlj/whEGvS2CUP9AyQy7iW44/wG6raK5QdD9Ve8tBjaqRP6At6PsYEYI/QL5I8KvohT8AK0anmOqFPzyOpWyJPJQ/0Fq1YXu4jj8gKmPXUQiNP8BOROv9xo4/qaR5XoUvnD8g9CALFy+cPwBqSsU13Ig/QGrob3eBgD8A89+NxDmQP9DX/vzrOok/gA8nz5V9eD/gYjrj0H1rP+DPyeZkcaA/H8yG/db6pD/0XV9B0m2gP/B2Q8xplp4/wDSlL3Qbmj9gQFujYuKNPyAcu0R3BYk/FIfVQb+EkT/QwqI8ifinP9jY/kosRaI/MC978JMfmj/w25TOzICZPyBQWdaQ4ZY/6IuAUma9iz843btPX0OOPxA5Ks7jP4E/8KBJ2gd9jj84MoPWioiKPzTlcbzsiYg/YGWJdyHEgz8MJiUDWcGQPyD1helJn4g/0D0kKeKchT+Az1KEL0WFP1ivr8SqtYc/IKhB7aalfz+A/8MAPy55P8CzNthsXIU//ClUMG4OlD8IxRYDrBuSP0BHkmMROY4/gBbuHV6wkj8sFjefdtKhP+i28oX+hJs/pscI1TtdmD/gUPQ40R+QP5cAvqY1EJk/8OLF5qwYkT8AY3tdC7OFP4AQHI5hDoQ/om1ep3IApD+QBOfHHyqVP0BMLnf9Wpk/oKHd1qIInD86CbSFIJeaPwi99V5jKpU/0NlPcgM2kj8AE8ImqvqHP+BvwVOZdIc/4MAkoideiz+wAViqbymBP4Bz7B+0h4E/KAf1HVZNmT9YOMq/2teYP/C9MkwJgY0/AH2O0qFwij9Axk03m/2KP8DK/XxZPYc/YGxttMDbhD8k0A+AuD+DP9A6h/CpApg/8EXQO7JYnT/YPa0x/xmYP4ALo6YexZc/kJpdMG5XoT8wF5rqIpaZP3C2pH1P6Zk/ZDrarp54lT8QZ9pOPoWVP6DDZ2UNjJI/9Iz+uteRjT9A4NCveTF8P8DaHJEdsK4/VFgawWnPoj/wWJzfuPeXP9ijoXdQS5E/AFWcVWCEqD9glloOc6yhPyBYP3ZHH54/xtHeEtrymD/wTU0AZjScP9jzMj48Wpk/NEl6dTralD+QMUz7OBqTPzji1H8khZk/Ov0myv3Hlz/AlWTYUKGRP0C6MdoI7ZA/2FiSn5JomD+EoHu0sseVPzLFZG1R05E/oPlqDUwkhT+I7LNrDCOOP/D4bJpBhJE/gFmgNccaiD8gtZx1sIaFPxjitPFv3pA/eK8FMZU9kT/AKnpt8HSEPwAvBp8E3o8/wBFJVTm6hz/A7RdwiFuEP4zs7U2xnIQ/wNfzWfFsiz8YWe9X9veZPyAJBHsO3ZA/aHaoqPIghz+gPiAiuKCNPwa18CVAIpc/sEpYBnLGjT+Qo2/3OGWCP0AN0ygTDH0/0G1KYssojz/IUo5LQ5eTPwjCogsb2oA/4DIYIF+Fgz+A5dzFnYOSP2gDxcLZjZE/oMaIIsuFkj/AyL45WUSKP+AvBZq7PXk/AC6x2Zyjhj9Ise9xWr6LP8Ai3RaYt3o/AF3Yak3Rkz/gYxqBS7mDPxRQJZ0bl5A/AAe7mSgfhT/w+aG3FDmhP3DY6oGVwZQ/MNBX9otlnT82ECSmE2qUP0CTeeMT5Zw/3L6qdZtXnT+4Qu0Du1KYP1BvCOGpZJU/mKa/HTfysT+wdpRHByOrP1BzvwsHG6U/MNh+mALxnz+IRpuNZZWWP6CRH2cbK5M/GA6xPIFigz8gAgW0CKWVP0Avu117j6A/8B4v754xnT+Qam2T2sWTP7A+VfHo/Y4/oOsA0dTQpz9w+k8toeyjPwAg1KvBEJw/ThY3vQbsjz+Q1PJRNwKDP1ixJlFu4IM/0JLbhT+eez8AcYDN1lqFP+D2jkX+zZo/bj3TgY5nkT/4KHy6PwKQP8BxcVM+roc/wJlP9Lv4mz8wiWOCrx+dPwZfoHFSLYs/IFr280vokD/MH7WasEmPP+B7CY6XA4Y/MNZ3cBgjhj+AtvdLmeGAPyBK3UHoh5E/mAO5+/7+lz8seodu29+QP6Ay4CM5joc/TIrAggGKlD9g/hlV0nOMP/iQGUyc+ZA/gMNUgtqkhD8A16s6K6x6P1D+MGwUVIE/XraptuqWhD+AvEPb3e50P1SS2Z+4lpY/GJjpRbkSkz94EB5UnReLP6CdcRRCxYQ/qMXdyDiMmj8AWDYwqYGOP6AXwQA/rps/sAck2X9dlT8ARrz4S9+FPwDsja6xdow/2CQUYHYfgj+AF/IlmKF+P3wyIBmW76Q/MBBTSoZkmD8kCNu1vzGQPyCtI7LGHpI/ADrERflsnD+gFcGxl0qVP7gQeoE4RY0/IOXx3e/jkj9AU+ahz9OQP1CXdrLVb5E/wE5cPYWSfj8KY8ntHGeCP2AKYdXyCKs/cBcHR6u4pT8cXVmBHhikP7jDPRWY4pk/QLfAIsTToD9AVu7HuGSiP2CU/JRVGaA/xEx545p2lz8YIu8E4EeXP8AVtgfKdJE/yuysdhOSkz8AT6mIjLyKP4AQzEOIJpw/oOcXNAvVmD/g/Ix9f9GSP3gAXgqF3Is/oC6GGUcwnT8AaMS6AZmcP8B+2VWu0ZA/fAZyuHPTjD9Yo8dPcCmZPzyvuTlvP5w/YseYZdT+lD/A/6jZcSyOPwAAAAAAAAAAqMGU4JE4uz8MQu2YR1zBPwRCcAUkPL0/lOlymC5qtT+SXFZnKV+wP+Je7bjoV6o/EGRftMdtoj+Aq869XiWWP3B+9vOxdKM/APxp228elD8MoX4pSluTP5C7b6b3g5A/6kJUU16UkT8l8f0RBwaDP8CsCheBuHg/AAAAAAAAAAAApA5JAu9APwCWjKpr6Iw/MCpTT7jtkT9Q8x8MtrihP7ilim+9KqE/oPkjpEr/mz94cTrGyCCZP8C/hvHcEpM/gCT2MHyhjT+Ao5e16hGGPzCj5AKecIg/UJgbxX30pz/uwT8SxEmnP5CD8fgXkJo/KBHJTHoTmT9AW/gmtX6SPzCJWODLIpQ/gGb3mS0uiz8wk+DBSVuBPyCN0B57cZU/0aSCeFBqlz8G8iKzlwKYP8hQqjkG4ZA/cEKaM+mokD+o1dCrTvqIPxTm6d3SD48/AMVE6//9hT+gVRf8ANmKP/gdWdwT0YQ/OG6vUsV3jT9wr0gEbWeJP6wt4yrQD5E/sP9B0/cbiT9Q7uBijteIP4CegQtujn8/xsKuE46Xoz/4V9WP0jGTP/wybLXF7JE/IJw1g0atiD+osMQJkuOSP/D2sqKO35M/qKNfYo4xiz8QiyeBA46RP3BZT47mfpw/dgDtbYZnlz8qJvdBVD+SP4CG4jMibIo/SuRl0WxNkz/Q/tMyl6uSP5BpvHVZQYw/gMZm0vKRdj8AaLWBkPuQPyCJOnRO1Yo/PNJsojrihT+g8Ow04wyFP/Ak4VkvDZc/sAOVEkyDmD9whq/7dmSUP9B3tfSV/Yk/4KNatm87iT+sx+NqRcGNP3xp5BTYvoU/oF2LH0u+jD/AwQJkTFuOP0z4XA6VK5I/KE4HS3k3fj/AM9DUmfZ3P0Bf+rpBbYY/wB34i31Qij/AdvcOQv19P5LYiXdOuYU/AI8XRmFxjT+AEEq3aIaFPwDHykjupYY/OGWSlKPfjz8AAAAAAAAAACDxNSr+I54/AJLAzM8HlT/wUqYYzHKAPwDDmYxxRY0/gDp958gthz+wBcu04LSGP9AnKyMxf4E/UO1lCdIsnD+YMPdR0VaaP9FItl4sQ50/gNunPgq6iT9gw7DYGKyUPwCYhqf9D5E/QDQCidrlhz+nJ4gTosuAPwAAAAAAAAAAAAAAAAAAAAAA9DrsckZkP4DEHXzhr4c/AAAAAAAAAAAAAAAAAAAAAAABjtNp63o/0DGKk1CBkj8AAAAAAAAAAADwvxK8/y0/QIXGVIg9hj8Ar/yscgN8P0Bnr/CfhIs/AKdEUF6VjD+AjsCpB/eNP/COHYZPr48/MOM22AMjrz9AsDab9/CtP8j4Y75ylqc/aI54PKqYoz/QuFG7ERqiP5Q+ndhDcaI/QBRa5abumD+oFIOXEjyVP+AsJ2MVco4/IjNnEE4Qkj8S67F2P06GP0DNsRmwxYs/OGepfdlaqj8+kAJwzPOkP7B4g5+HGJU/oDY0IXLPmz+EMgIpgMOjP2gMUFSc+Z8/cDBkm6YjlT9gF2DQX6aTP1BPmS0tRKA/EJd8gS9Wjz+IyxUaQ4KQPyjQ5S+WAZM/sIt6d48Tpz8dLpRQrZGkP8xV9eN1WqI/oG9gPNRElD9gFAO6OfyuP+SVIDFYwKs/LCaNK8mgoT/wQKYdSDWdP8Qm2liq85E/6M0yc5U+kT+w5mYlPZiWP8B6VRMCdn8/EMY6UY5khj9wd9RJw6qIP2B54NFBb4Y/IEU260rfiz8ox5iE6XWYP3DaqFLof4s/fCCtk+AIkD/AdbnU21qWP2C43eLRDJI/aIFdUzG1kD8wPgmuFpaCP3BxiPeLN4w/WGbT9DHMlT/wzlU/3yqJP8BSNtsSGoU/gPxFQAVfhT+AWTg7uLKEP2AkneamPYs/YEKP/cOgfz9ImJyXy9GLP3iTB56GSas/+0gHJw4eqj++hE9Mz5+lP6Aur/CLB6I/oH7ZNlfHsj8gb2bu6kOrP7hEyrX/VqU/ZubfVTQfoT/EvNjHBuSmPzRKkDL5iqI/lHpCiLIgnj9QzSfkkNCaP7CVt6KfuYs/wGRFve9xgT8QTKRHAWGNP0B/B7s5H3w/9IIyy36NqD+cGmGHZQCmP+q4bySYd6M/cE+OY3hMmj8YEznya0SzP6KIvW+Ebas/gsKyE7ccqD8cJhljZr6jP8zcc+97OZk/IJqE2m3Xlz8QBZ4G3gWOP0AZRmJE3I4/4IaKTOltoz8ALylvYNGTPyCFzCmNmYs/TA+mmQt+kT9oquguOWSoP9DF6AxPHKc/SrI3SXehnj/Qlqww7qWhPzgmrC0AdbE/3LDXZf7xqD/AQnPMiF+nP77P3FujY6M/cMfzUJO+rD98ywLGRj+lP2YTB4svqqE/EL+or1yknj8QdkbgMwOXP4TC7WP9/pA/QJGUtX7hkD8AgfXxaDuUPzgTt73HAaU/1Kd1RycaoD8AJAx/l02ePwAc87D3VJ0/FLONlbjisD99MiNJGxCnP9pBKiXdAqA/+F1+bxf2nz/IL5eQMSuJPyD31pOokZE/EBdy1aPthz8gdo5h0gqRP/isMRpIdow/IBS0h7fFij/gnQQmlw6GPyBxFNtkkoI//JzRYOHVmz/AemyVrFucP0AfTdUmQZw/oMya3pmOkj8oKS/mT1KWP+D6SMFkxYc/QF4sUv0OjT/AWPU7pRuGPygfiNphiY4/oLo2WBAkkT/w0SKhGDmKP8BjVAW7Vn8/sy3TozlijT/AHRocjluHP0BrKKBciYs/YJAKwOjDgj+E4l51K26hP8D5ykUg4ZQ/IEv8anumjD9AJBDqlbyDPyA9ycAeHZ4/0LD/wE0Jkz8A7oAz98GOP+Sm3UaDBJU/aPyXn19NrD/CHFdHty6nP0bp4qD2GqE/wPKpVcm0mz9gWXC7FwmqP1AWmY8OOaU/cOWuXPwimz8oxPyoGBCWP2h/hWUpq7I/5FbOt82FsD8ga5N6IVinP2YHCfGLRaM/OGfv+d3Poz8aAQ2ZlKOcP/4sVFQkX5E/IP9o6k7XlT/AH+rpVwCgP9jP+5YU+ps/1IAJCOAnlz/AzePENLOZPwD65DRC2pM/8HR+AaoIkT9gFE0CvPyNPwBIciPhbpA/aMOF3tpVoT/g12x8Ou2eP5hF5LiPZ5I/sADLoJufkD+emzuAAD2kP3Adp0hmaZc/TPIXOA7Xlj9AGDvqqOKLP0ZwXrMjrqU/eErD1/jznT8QN37ZJtuTP3Dsa6XFsJA/GaGMAaVQnj9wUc70YO2YP0hvbI7QJZY/oIu2T65djj9g9qE0TaKWP6iI2CUdDJc/4C/3YX0Cmj+QObMJKXCVP4oh9kfAJ5c/IBohByjtlD843BP0SHCUP+DPmww5kYs/CMbFYYSIpD98yc0zXtegPwzNRkOPF6I/oFG4FQYflz+coaIfCnOPP5C5OvDN8Yo/CEEw+CN3hD8g7ixELjyUP+CIBNP/GJg/8K8xREoymj8gWLPkV76IP2iOYD1OdYc/cKk4sQ7blz/9D+7fuUuUP7Rl7cwodZY/SBXMIozvmT+QiuauQ3aqP6BwuYLNLqM/MEjx6MLDnT/4+fgoYv+UP2CuC/YfFY8/CIlGiQQ3kz+IcpNUweWLP2AalSd1sIs/IMvLBEccpj8sfhZ/1GegP7AQZazxSJo/GBYhJ2tUmD+gLrBhxj6RP9B6kn7+SZQ/UGfEl7SuhD8AkIEaC0SEP7CpS6Ok3KY/DNXXurdbpD8wzExZiJGYPzDPNK9IQZc/+IKik/xLnT9AZkH4dzaLPxw58UrX+og/oMDUfNjdij84LU5762aSPwCdKllGsIM/MDyFNzCihz8gwg2c+JqBP8hJ74UxF4g/AFet2Dxpkj9AM146U+l6P0BQeU6u/YE/rBNWY5IckT/gvPYHCs98P1DyITXZdYk/oKQN2mkhhT+oa0VWM8KRP6DaPtNLDIc/CMBBWYxTgz8AAy22iWWOPyAbIGaONJU/cHJ43PiOmj9qrY80spqXP0BsbDnGhpg/s2WgyQ5bnT/QCfEIxFmUPxjTrw0tkJE/YI4elK3rhD8AtgV6EzyXP5Aur4wJ95I/qIUdsNgKhz9AtAp7XeSDPwzV7cZ3XpQ/sC9/5ivzgT9AY7z1XkCMPyCo/UCD34M/KmyJbGUSoz+4aHHPygelP4JXe3mlrKE/wBtZO1+Gjj/mx378TuGjP8grEpTZ6Zo/oLah0KGwlz+gdEsdGzKMP8Bap7Tn1oU/0LzE1W62lz9AjmsMKiSMPyjwkiVu9oA/4MNl/IkIpz+qMb1rvaiiP/TSGKWiuKQ/8DGifiEYnD+QONvdr6+uPzCdaw7Di6c/YFO0puYxoT/uMOmP4LOfPwDrhb88EpM/nH9K6u1AmT8s2InBwaKNP2Cpc+1mBYs/QPg08hqfoz/4HYYBTxekP4xL/ujW26A/cDPqSEaWlj/Ab2w5ssiiP3gHgxXz6KA/MOu8R4VRmz9yVS+eyT+VPxBSJiDlkZo/oFL8PetEij9cpjI53JGJPwD97VrT+Ic/gPUniDXnkD+guAI0M0uIP8GWq2tiiYQ/oNm3tAi6kz+QnuJOL06kP6BiRFlq2J0/uLaHpPrVnD8g3RkU8JuVPwinZWZ9Hpc/gJpm3Zfuiz+AX5Htr2mRPyA7M5pLApE/oNFzhsUcmz84NYDZz2uYP3ja6uxTios/YKqpFXUQkz+AlFomSzGXP3A9OQM3L4U/sBOqm5O2eT/ABX6Pj6F/PySpwloBwqE/rAJS/HZfpj+gjpLJamSXP8DCUnXdPYk/Zu5+VCwpoT8oMXHaFnmaP6h32UzUwJs/4GEv4tKxhz9wDejIH9WQPzDDRgyBl40//IRquzYhkz/ATFMoVsWDPxzXwbTYEJM/oAjvSN7oiz84L9K/Tv+HPyCTVwb9XYs/IBs9Rk5ohj+Ay2Bx3rCPP1hAlrKEgII/gLoL63DRfz8oZoXIEHyJP2ApzVUOrn8/+JnfQ2spgT9gKQF7A+GEP+Cy2WJZe5g/YPKSp/4uiz8A1VSkEVSNP8pOs9BcP4I/8Gc5o/cKpj/sNxjhPnujP5CuOkZakJc/GJZ0yVM5lT+gO+wf1ueYP4D8s4f6VpM/wHi+h5mQkT9MCKjbMDKNP/AM55jqmoM/KLZj16wpjj+tiNYlvsR7PyBfAIMUQow/wDTnXHM+qj9A0JN9GKikP8CakaCfWJ8/jEye+h6cnT8QGVvRKs6kPyiMM515UKM/2Hb4eNgJoz/IbipJStaZP7iNzxACF5w/RJAvdxnBlD+cRQlLWgyUP8AHflkLW38/AAAAAAAAAAA4pxlBXIm9P+jkgtMbv7o/3KsaA2N0tT+IYnpKWdiyP4gxcRsp86o/5nBaEq0+pz9Qsu6k1GuhP0Duw4CMIZk/YA3kSg9AmT/g2711BdeTPyBNM13PSZU/AEhhCEHOlT+g20lEZJ6RP16LcWhwcZA/QC9Rk2SziD8AAAAAAAAAAAAeWqXMSVc/gAXev+MAhD/Adrh1QyGCP+DNf2rUm7A/uMi9W5ygpj/Y1nDKrkKjP0yqN/T6NJw/0B2lTnAzmz/IkGqvOG2YPwD+6vBwjYk/oOUby+eBjz/gHqeLLeCeP7gDlJB04aA/IOBASrL1oD+ABFUw57ePP8Ac2+pRXpA/8NM6cuGMiz+wBlCoyHiCP/jHI6+IBJI/WLuOo2RHoj9G+r2UnJCgP9pSa6c1Z5Q/UCdYyzhZiT9g7ZXQcKiUP3ijz/zSjJA/4ARmIANFhz/Af2bJYaqDP6CygQ3pnYo/yAPcB19gjj/eLQ+NucqSP6iFl7HBJJI/YtEpEh+klD/gnPKFDTWVP2AeuOFf+48/IMoDnaScgz8AwoH5dzuWP/A1+SWo8ok/8DaeHH4lfj/ArriO65R4P6Bl+NUKLo0/SDMQSoEZkD+o3mEArIiEP0AlNcpzio4/cLiduO7fiD+Q1c8fK0yFP8iTDUDtu2w/AI6PVEgxhz+eF/yNMsuTP7gJepRbO5A/cN7de0k/lT/Aj3xZTouFP/DQyZ6js4k/gHJ0VFALjj98zGyEEQSCP9DMTQiYcJE/kL3FU9lmkz8wYze9LMiVP7CmrhIUu4s/YCCgNLA3jT9wT2vrd3idP65V3thSd5Y/kHK/+HBimj8gEAifRVyNP3hOcjMVFZk/GN8jKEcNhj+k+Swdh0eCP0CZ1tpOEos/ULd5s4gioj/g9HR23oiUP/Dl8ZcwgJA/6g3hHxdPmT/oOurEw4OiP5ACIFGR0ZY/QOaVJj+9gj8gR5Gd4teMPwAAAAAAAAAAABboEybZij9A/K247OOIP3B60Loa+IM/YMFUD3t/kD+gxQyt8JiVP/CZL/ZmtpA/ECggEMHFhT9AH9pzDDiRP6guPVDw+Ik/PoklXpVykT/A9C7OUK1yPwClj9H7tXs/4KxO/+2DiT/AmYjDl8t8P3WcY6od15I/AAAAAAAAAAAAAAAAAAAAAIAvE5yFtYw/kD8I0vebkz8AAAAAAAAAAAAAAAAAAAAAgN/QcGZkjz8goUp41XeFPwAAAAAAAAAAAKBxUKLeFj8AF7oCPJaNP8AFEsTglI8/YIohikLckT+A+9hWNoeQPyA47rvT0IY/wIIXE1gAiT+EP1DLo/6wPwjH1MGfKq4/JjQCznXjpj/UVi5BsCWkP9A8KrOXFaY/sIQrSsZ/oT9YD6VkKdugP0D2q6uBU5k/cEgAyLa3pj8LUypEjW6gPzfH8LOnfpw/4J83B9temD9IoDYjel2mP+6uE3WY+aQ/klaTqCADoz+YXrsUeVyZP2YCBGAbepQ/UCoDT7dyjz9ATO4n3Ml+P6Dv85jXP5A/AMpPVIB1lD/A7lk4ZlyHP/CvQGyaFIw/4GnLUrlnjj+Yls0tm1iiP6kGDp2Nhps/4H0QqkVUoD/QSqkL60KTP5A3KJSu6a8/fJTXPLpUqD+AiV1cmfOgP/AlF/pCJ5s/0DAc+WZKlj/YzdIVTTmZP2yqsaI8EZU/QMTxbto2kz/4kDDfFB6cP6ifWQIPuJE/KOeNrEYtkz+AvTa1ByR3PxhBD9D5P5g/gPkovd6BmT90/dQw8kCbP+AuGu9yvok/8FE/gTitqj+CmLGnHcSoP+6uh1aMEqE/eLFuF3QXmj8GEmjZnG+sPyCncXYSb6M/8IFYVYT0mD+w3z5S/KGaPxD1tY6fw6I/oCFeXdQnlz8gkanoz4GUP5iIUyIv1JY/QEfOUhsroT9w2O+KLb+ePxhW8wbXBZQ/YAblMMY7jT+QtRwREbqzP8i6YEj3IK0/LCb3HBKgqj/as9AQVwOgP/wxGFyQw50/AFdIKjCynj/AanuOl1CWPzAY8aYZJpI/gPA064DWmD+wK6EBRAydP7C3GVCo+Yc/4ImqveW9iT9QxV63is2NP3Bi4OcBE4c/SPMvmdeshT/AUmKjHuh7P/A7sLonBJo/MNf6c+A4kj88EnydW3CQP2BzQA4ipYE/Zj6AWLQanT8QxT65DgOZPwBVFaQ++Ic/gKRZKYZ/jT9guUOrAJeZP6Bf8jX3KI4/4Mq7GoUWij9QTtnxrdaSP7hpO0OVYqs/6noK1Dcvrj9K0u05WuqqP+AdY8Rh2KI/kIsop5zfuT/OevMS5Sy3PxTIAiScd68/GqmBjJcdqD8EcJ/VtcCfP+jsaMB4rp4/6NsN1E6aij/ANcCK+KeSP9ALlYrBy54/sJOJIRSajz/Y1mhIadOMPyBqg3f5T4M/iDyCkwpqnD+ARFGTIEWTPzixMr0/x5Y/UM8W5nsemz+wKGQsbrmbP/J3Y0ZaHZk/Rq9n6V2qlD/gKfY+O2uPPxCNzN8xXa8/gOo4LX6Ypj/UMijSSqWaPxAviB5LZZU/Awy86EpIoT/gQprLBGGfP5hBiedsZZA/oGAaNODFjD+QYnbQi8yJPyDqYQjAppQ/gNS5CFEgjj9Agnm3ECWKP/qm9tcm+JA/wFd02pj6iz9QO+YUQrOKP0Dc8rD6en8/zqJoXNAHgz/AXS98WFFyP6ARgUlKe4Y/INshgSQPhz8Qv3FStuuKP0BozpVwz4k/8Oq2Iu/pgz8gbRWcZ+uEPz7p84Jjm5k/IPHGFIocjj9AiukW/zONP8DA3Fhsx4U/cER512XXkD9Ap2RSfHCFP8AAwueevXo/4GnMO/t6hz+AI79BbTqxP3g3kFBPRq8/6jWxw1V3qD/A+aFa5jSgPwjej29jZqs/pHJtG3w3oT+IxIefILCUP9BnAhg3TJA/kGjl1bd0oT9w++nFG+WfP9ATirPY5ps/6MzTVESLkz9ISVrbg42gP3WlXh5GVaI/LBwXRmAynT/wIJFDHbyQP4AgNYuFzo0/OBDGXmPUgj+YPyEf3J2DP4D0qP6DQn0/gEVB6ne6lT/ArutloOGPP7gGEnhm5pE/UG1x0lSnkT+oql6SiyCOP6D2CAe1OH0/QIbts7SHjT/gfqtS23qQP9BuB7TJJY4/0HW4DJeKgD9w0nHItzSIP8AniFZ6zXM/cIQy8h4GmT/wlOE7dm2aPxhXn7W4SYw/ALdqn/kzhT8UrN5JOiWTP2AjQ7ujzJY/CIenhKVgkD8AFISvGiOPPzzMDGr3sZU/SKgUcmwrmj8w9vQ1fhKgP9DZ0Nl+ipM/pDmpCaabmD9wWwZi66KUP4hNBJ03XZA/wO+siP+Whj8oZsdcu92TP/DFlhy1t5E/0PU8R/40jj+gfbmwa1GaP6+i4At8cZQ/+CfufJnymj94jOdZKQSJPwDD1W04L4I/oHgUy6a6oD8g3dfBekWfP1A5OOP6i5A/UIG4vK30fT8U3H7/suSxP9uZn7nRQbE/dsU9GWtdqD/MqIceQomhP6AvAQP1f6c/hOc0AI+3oz+krBAeSeCiP5AnopF7nJU/qCD5iPB2mz/QMUpIfLWZPwjLCttwCIk/YPS5CFrLgz8gbhBS8nOXP+jsF/AGxJk/IBD6JY6YkD/01pDg+peUPyDKESUL3Jw/gFrWLFMhmT+wnxbS+ICaP/B/KFTLJI8/KHG+u7AnkD/Ak8NifySEP0CJpl0tjIk/YPW7gA5fhT+gTCHNp2iVP5CZT6vWk5Q/Alik8yNhkz9QsuTqtXmUPzhwqy848Zk/OBeU3C/6lz/e6Wt3y1KQP8DYwD2tOYc/7Gt2BZcWiD+gLod7KpeOP2hZ4ke0/5c/ULfi4Fsskz8wcipDCTudP4B3+FrlOps/8L3XHq+PlT/ghgoMLjaLP/gu2vYXQJA/kLmL8fRClz+WGCoosKCTP+AYBd+VDos/0EqNmgnHnz+Y0lE2q4iYP8BssfgSnpw/AB4rloy6lD+zVV+8G5WiP7jEqgBnU58/8MySOMHYkz+QIdI0igWZP7D1DI/1Ln0/oH2hFQ8Yez/QOWlujIN5PwAlOO6lsYY/2LsIlLZtnT+ILfaxBuaZP3hwLiATCY0/YE73tvW2lT/IY5UYRiycP1Ai/pcy15M/eH6ki4YOhz+gDgbGjVaHP7jLsjmVXIg/kCEwr4AXhT+I1AFB1++HP+DdmHXWqIo/QPrK+Gqzlj8Ah/MtWyN6P+BmiDHdFIE/NhiltsIshD8Q0QdiGPOhP1i18RnarJk/CNG3cZB2kT/4kpi/5d+WP7AHCFsv6aE/AOlhFckslD+YnkLn53yQPwgOk5NPqpM/YLJh/BvZiD8YvJgHNkKOP2B42xK/jok/gMu2EodMgj/ALIEoHi6bP6CCvy87hZ4/MHD/wU4Xlj98YZQSq6KRP6AFIA7Vmao/COYqmlw+pD/gvCSZtoShPzzhmrxOtY0/0Ii6XG8Rnj+w/cJnbTSWP7AkBya3AJM/gCmLvYdMkD/o+x5M48GdP/KtEIHXzZ4/exihUDAflj+AKIeeZWqOP9D/SL5yF50/3LwcMgIamD+iWciFF4CZP2A9rGkJ9oU/0ZZ4D/ZpkD+QirjkRFyWP8hyZi7MBpo/4AihC43clT8IEQwdc26VPwDoe3IKR40/AOEeWumSgT+gJYllPy2QP9gTQ29NIoo/gC0ahAgdjD/sPwAdTViDP4CnE9oZ8n4/2OTvOIPZmz/oibryBV+UPxZrlu1iPYw/oKqzIw+6hj9olyTwlQOYP+DOjisCrJU/cGsZCi0GiD+QS+mecE2RP6Bz7GZ1c40/EB2yDjHqjz/4eJXYPnqQP0DU9eQAU4E/iDl0q4qXmj/w54r2ND6KP8CzK/FGh4Y/IGxp9KoOjT+AXZiaXZycP0AEnHNSw4k/3AeBAiDHkT8Adq8SMJyTP/BWe/HNrIo/IHZqX/imiT+gKaTHc1aGP4BJVhnT2YY/8FHKuTC9qD8A2OSuLNaePyAchbZ1e5g/lm0bODNokj9onXubkeyoP9zKLKL2mKM/OPedpTQomz9wnpgm6EGRP0D4cXelX6M/eEPgaX2iqD8YXbv8aRuhP8ICBR/IP5c/gA1WLLkdoj9o/zk2gf+dP3yc+Nk3d5A/gA1GPmsxhT8gxGuhGJCUP9Dd70CiJKA/AHUUfq7ijD/iCRqcteaRP7BVqQCbBqA/ILXDfu3imz8wlKNNiWeWPxQ7iFA+5ZU/YGwys8p3pz/cZ+zka0+hP64BsJxLBJ4/kO4nLHO0mj8AAAAAAAAAAHizh0TO47U/GN9h6fyOuj+0wVFXOh+2P9Cgx33ML6o/hlTy4WgWoj8Y2ewn/+OjPzDL/qAp25c/QBGQ0Nzfhj8ArZRYWVWHP1DTRh66W5M/eC8IDGZFhz8wXL/hMv+TP8SJhQLuLY0//klGbEILgT9g2+Wb8FOBPwAAAAAAAAAAAHzVWJOBQj8AWmcOc7CNP2Dut+PNL44/YEAYGWHBqj/QeKtkj0OrP5h76wiCfak/fLoPh2I6oz9gnwojBiufP3DrVA8JSZc/eEUO+z69nT8g2S8xPkaOP8S+TG/iT7M/sYOxD1hwsj8GJyVm1taoP9w8MD0J3aA/QC1MeEHhkD+whjdJrqCVPyAjbtMdDII/+K9LEN6ykj/YMeLV48KhPwVIn+zxH50/sGpgkCubkT8w08KEKyqBP3j9hrd8VZw/zNaQ35eBmz9mfTJTc3ORPwBGqxcoqpI/YCDmfEd4ij94jGppqKl5P0Rqpp28oYI/IClLR3dJgT8gABZ9wu54PwiaBzYnL5A/0CGbeVTXfz9gi7Y1IJCNPxg4EQXL2Jc/oFdYd3fdjj9EZEkwmEaIPyAEhbRjio8/AEo6sQUNhz/A7hVbwwZ8PxAzWnUunpE/QKdd/jDtgz8QC37h5LegPwCD5MNFtpc/QCyJbtdXkD/IJXKRcBaOPyNq3vo+VuM/wNjrK3Ie4j+J0+fJC0fcPyJk7DSoeNY/j0Cs6Mbo9j8dnGwR0EXzP5V+wnK9Ze0/S6pBdyld5j+8arB8AGreP6FlS5oxa9c/3pI5csHX0T+EQkZVXGPLP8Tk+yrTd8Q/9C1+PkykvD98ElBIHY2xP+BcNVhwmKc/SHqEm+5Nqj+ymKFu0POnPxTj5mIA/KA/CF0rSSnQlz+QuPMM3YWgP5D/DHg4x5g/cMfh6eqBnz/qgS287t+gP2T/4X3/zaA/VBqREf4Vmz9y1VruZeucP5AxNwGEp58/mOJGEJWd0D8cDDlwDErLP0RKJvsaY8Q/IFy9Ae62vT/cuRijL8DNP+qJPeRy0cY/7NcZ7Do5wD/iQ2dQSEO2P5QEKmbQHLA/eKv0oPHspD+QPRUaJnOfPwCEYKIVQ5Q/QAr3BxErkD/QioatCc+ZP7isvobLJpU/LFKCZC5Doj9g5gzeVbm7P3qoTOQrqrU/hhfMnOBNrz+s0qVhbX2lP5yz9dYNsMI/eHrC6vDxuz/Yl6igxfCwP1C+steQhqY/tNKXdPFArT8Ms0m+AK6jP/jU9wcT3pw/cEyTWWkhnz80QQq7IdCgPwwAxKnAvKA/kgS0hkz2oj/ICUHBwGOkP9zUa3ZZMps/MHZ133oHnT9wuxqoCgCfP8B1kM8mipo/QDVi8qc/pD/YD3YU2TeiP4hGF7UlD6I/ePmODCbknz8ovuOnL0ynP55Osy99G6g/VL7iJbPkpT/cu4SUA56nP9Ce8rkMha4/aIiK4V2LsD8k+E1lFVmnP1zIFThItp8/iL5o2kgqpj/KV3eTXgylP9Y5lKeDHKY/YDtErq6fpz/Me8HBd42lPyj9hEWlq54/EJ2b1Sammz+g83GMwrecPxdGkaHFZqY/7BojkMK/oj86axgXRh6oP8A+U0Q2LKY/SGhDOqFWqT/sHZxnLdSjP67jn/UjM6I/WEpqIQnuoT+Ag1+ruMumP5LkAuxWRag/sAw4d3DtpT/kGjkQNzKkP9DEW+CGWZw/PpYyeIWDnj/vsV7cwpqhP8BowtE10pw/3C5Tk1OEqj94Pu99gfSrP47BFT4ZKqo/iHknLoogpT9AOTKMPdOlP2Qf9sjAkKg/zOM/J7jxpT+kY5T+mSqnP9hxIHOd6ac/xIQyQ+ALpT9oDp3YWO2jP8CrUIUiMZ0/uIK2H/8ilj+4LPB9da6WPxC7LdgbjJo/0MGkfQAZnz84UVru90ilP/n1NIbEuKI/lBTq9LNAmz/QzxhWP/6fP/YXLDr2mKY/0EdQu/6Lpj/QH7eOFV2pPxiGmzJuaaY/hlNADm+yoj/AFNwE6M+hP5xDxq1LpKE/cInOtcjKoD8A5vWJfBefP3g38+fh9Js/dD7PqOLRnj/QYc6WPBSdPyitzd6EvqY/jDKUIYFroj+UxfrBEkygPwDFCW/Bb5s/jCeuX8uRzz++jLecOrrKP+dtzGrrpsQ/uIDcQyoFwD8a0vaKE4nTP32jxnhQCs8/33PFhbwkyD/yY67GONzBPxmH/Mke67k/xO3dUbz7sz8ERKVKmcGtP8B/iN3UVKk/LRNq/TBaqT9M+Ek/KNSnP0wHYHnveqI/UCfGhXLRoz9wRrQEBaytP6TFom2Mu6c/I89+TQCGoD/Ian+JuT2kP7FMWeqwnKc/+PB7+tWAqj/s3VGqcGCrP7hFgPAZuak/bWJIfURGrD/IJZ895ZilP9gT0fIcBqQ/4AQ5EvgYpj8GaUpFhOSXP2BZvHPnl5s/aAy7mpkInz9gAc6odP6aP5BBiE8ED6E/UBKDC1LzmT/gTB4VvcyZP2D4cUbse5c/KFK9Y5c5qD+OlCD12/6pP18PN6803aU/CEB5NXzwnj8UHGD8de6yP2IA998KVLA/sRr22FaxqT9g/PLZNryZPxDFVwsn7JI/IGODEA4vlD/YkKFpXwyPP2CoW31UUI8/WIU4CMcGmT/YSK4gKmqYP1y25kq8RJ0/oDABgoMbnT+oRweOo8iuPyQ9iVaQPas/LUzReEavqT/YqiLqhLylP2BrP6z8OZ0/wDQARlfDoj8A/8dgKeqmP7mdMxyvZ6c/j7VixvdT4T91rdLV7YPgP3KgOQ59l9g/4VQdNO2j0j/ycns4tAnuP+JfNeZt1eg/gWPnX2GY4j/fYXt8OOLcP5tH3MZHJeI/Sfb4gbIW3D8CyYtzOwXUP9aXVfe4UM0/HP/HqZKbvT/f4q+glyyzP4Jrf/7WMao/sPEpo/T+oz8cNVevs+XGP6x+Ko1Tqr8/VAAYrxzOtT+Yvn6ouuWnP4Ca5mW2E6Y/EHMWp0PLmz8we+yCByiTP2ySQmDPNow/wFCqaDQkuj/wookpTHW8P/QiH3aypbs/53utJ7YOuj8stua3j03CP/0+gMwwvMA/NJWGIA9fvD8+1vRc7MK5P4DJU8y5qKs/qP1Rjk0Goz/QX6Sq6niiP4Rsozbel6M/0m4woJmW0D+E7wBPEqvOP6RY30E01cQ/4mc8jYJbvT8iPtN7yBPTP8wesyxPdss/rrWWM8riwj/wtXDT1e26P5RUI44yM9A/lKXGisenxz/8IBSqk+u/P/wMoS7xGbY/XOyb38ATsT8aZzHk0FmsPzy4cEA78qM/qNPwS7WSmj++zGqio62sP0CC30pnjrA/Q6XCkZlJsD/Mw1uuKHezP5pJLYqY3LM/ZmH0/tvLsj8SLMN1h72wP2TwGZ2A2LA/0NLizf8Arz94xX/49w6uP55K8l+TwrE/si3dL+eGsT+sLVeaFTOyP9jpBrqff68/xDzcNczvrT9Uyw1eosOvPywiJuX7t7I/HIs/tF+dtD+hBGOygbi1P0gdOZbmELQ/EMdKb2eDtj/uvoWn0mayP+YVJSpLJ7I/aicgpJtEsj9CTzmjgZq1PyaLP8N+TrU/o78nJ/MXtD+8kodP3MKxP1RhQXrMmrA/IPxLLckQsT87I8Gy+xOxPzi16AWQl68/SDERckPcrz9+168RFnKwP6qX7m6F+qo/1JAcPcgeqj+OXo7DWliqP3x4LU/egqk/+GE49tPWqj9QvloAWcytP7iaKjw7Yq0/6ELCeY3Yqj9MJlG3iDymPxAa+3ZG8Kc/26iJHOaMuT9STPTsdLS1P+YFBRvXdrQ/ABd+8tDQsz++OnDu6GK5P7jkw18cALU/OIzH/ppDsz8kOjuUyZGxP4iX0ulWEc0/RPa2Xdx4yT8YSkWKpjfFPzRusgF018E/HMtEUirkyj975PUuUHbFP5a1EB7p6sE/6Eub1+7AvD+b5NfO6T67PzZy3OCzJbo/0rZMMd2Ktj9M8+GegZezPxqDhAWBYK0/KAD4Snkcrz8CFmr7uv6tP1h5KcCvO64/UKUAvE/PqT84FyloCeipP14XyPKRQqY/mENYFvjzpD9EQP8Odj2lP8yWhPqn5aQ/NObbkNWupj84dq6dtuSqP8AKTD3S7KI/ICunAWYnnD/YfMkr2BCcP0B99RZwep0/sVevISVRvT+WdIIXrw66P0jp+rlM3bA/0OvtDvQAoz+gRktatniWP+AIoTGpr6A/1P9CawQnoD/cZXui3tibP1DQ6n74AZs/ymNO/1QYnj8jcULg042jP7BTPPVtGaM/AL4XCKP6sj92IpZLfmuvP7B1B1OHJqg/yDnz0di1oT9gO0oacgyWP4guR4Ahupo/jAcVhovAoD8A+11MYBifP1x4DqO6faI/QFdiu8ZioD9qnrE6fr+iPwBOmhlsb6Y/wDep9aW9mT/YYjZELLCeP+UVHtjnHJc/sIFMeY1mlz8G41wJKb7dP3yrFyL78dg/cjCqdeU/0j/vTgRvkRzKP9jIFzFhkO4/umD9CIdZ6j8Q0RfrPizkP4rlI5F/RN4/2bZx9t3T2z99nOPTm7rVP8aIBqHXDc4/GErh2hwrxj/cH/rNgx3LP9wJe4a8LsQ/3IZZuGbzuj/YpOPF0zewP3hOnPK88qU/bCS1Z0l3oD9KsiU8uBeiP0h4siWeSZ8/oE7+lxvjnD/w9sKMhOWdPwgBSuUdYaE/YOc28EJWoj9waKmLuaKnPyn34ZOVaqw/DuEpDSjdqj8Midsgf5urP0bDwD+ltqg//E10HLuvqT9W/iFnvlqpP1CowcMkUK8/8CFEOhI1tT8s/2h6Ogy2P0hSfkCaWrI/vrDRhk8WsD/gIHeBgF6nPziEoMJovao/2PaG2CPfqj9ak8yVSLaxP9C8dZJxpas/WJunSJ7WrT9AOHjFxBWtPzBSBHTEp68/YDV2r0ZIsz9iz/f7QAqxP+yKYB5N/KQ/tJaPjf9YoT9QVW8l01ihP3AyIP4M2Jc/wFNqXtC3mj9goPshkcCiP5DyPaE2YrQ//Ciy29FCtz8cMLSCA7m1P9MUUeiwyrI//I+WN2s5tT9konO6zFe1PyZacB1JcbU/gIwLvyXmsj+gX87vTrC3P1QArWcinLY/AEsHv21etD/G/ZjBCOCyP7T0nWX9/7U/kA9aXg4utj8cYizhZGqzP0IFLjDCP7Q/NPASRoeCtD8EXOtQRE+yP4bwcDROMLA/2LcsCnHZqj8sFs6TIy2tPzKwohL+HKs/gcniR171rj/ANn4bcRmxPyjh93rfE6w/wDCQNbL0qT8QfEtYA3upP0CRlUhFAqo/SLnNo+F6rj9W6ndyF5SsP6yHoZQOC7E/fPhirwhbrj/gf8BIANKtPwz5dXcDwKo/to7IJZkXqz+wa72Sh9OsP7jhRI08mrc/dIvIpHKjtT9IHgud8Dy0P8AOGBUawq8/9DyMAjcBsD/0SLKVuHGuP3Uj1UKSaKw/+O8fJqGHrD/ARaF1WRK0P44k4MEYkLM/NxS3o5elsD8I9m59WqKsPyD0AmzBl60/YoMekHq1qz+c8KZbq/eqP2w4n6ECsa8/tU/fhef3sj8w1KkYgDuxP+xGojcBbK4/SHWvcydjqT8+5kJn0iKtP6ib24ySu6k/1KGXZEbZrT/41lfU08OvP5KDlHXB6rE/OMcrkmODrz8wZf6WKcmpP4BuhfUPw6o/RkAf3CBOsT/9FesdGNWxP+npIgqb0rI/KO/sc0+irz+udxQJCsrHP8tCAtcoscI/sJGd9hmkvT+EQilp77O4PzKuwwOgAsw/OpSPmlN5xj+YO1JA4V3CP4g5Lg+1Xbo/5De1m1GJuj/OczhxgRy1P7J6L4W3NbM/AEfYzU0jqj9CPkcX2GGwP2jKg3+SDa4/1OB+Aqx6qT/oLXT/7wKiP5gZhmwTFqU/9GWryw0Wpj86+k8XWI2lPxRpkMUeEKs/+kWVHZPVqj84Ox49DM6oP3wtR0kBkqY/OBZ/75QvqT8Lw3ztxziwP3BKZtRl76w/nEmEX+vkrz9Y5K3tPTyuP/wD2LKMobU/COB37X1ysT82H/ltv7GsP7Dby50Gxas/uIsxA+cQsT+as7aHA4OxPwrz1Dj29rE/VAc86ADmrj8cC2+mSf+wP7daRX0C3Ks/MD2gbbsirD/Mz2Bov/+qP9pCGGGBQ7M/aLrraCh/rD9F8zoH1+WjP0DXjKf2rqI/sMrvnRlypD84rOYQbjucP4x2efAXEZY/8KxBZ5jonT/6akOFYkiiP8Bp9AH6J50/9H7jBGilmz/4rgG+8YahP5jh607FAJ8/MIcLpLTznD/p3J5Jv96gP4CNgiDT9qU/kFo17E60rz9iJh6D5rmpP+ASkxiU6KQ/WHo3qP3foz+Ajq/gX0apP7BKrMKoDqg/EKSl+bZwqT96a6oi9xGqP1i275JVWaU/mDA2m6N6pT/AojB3UV2cP3DbGFJIKZQ/KK28tvMysT+4Rd+dCgSnP1Cdhmfegp0/TE06lH1yoD9ooxZf+n6lP5iwQFIDlJk/TEaKbJq1mj+AXDXLRxGbPxzHMQgsnK8/ZoHk4HAlsD8nZUXHUQmqP1wNCxWy+6c/SA+jlGQ1rj9SahE5Az2uP8hyzJM7Vaw/oA/I22fwqz9tIRlePF+rP6RovH3wxac/JEPnasLWoz+YdChe5mShP+BF4WYxeKM/4I9GCqYupT/AwroWNYKkP1aFe4u1Jqg/wOQJYiNSoz/Qyvz0cq+gP8DxNpnwTJ8/KiSEPQR3nz+wmS00H+2pP+C2XB8nu60/eEIxKXWYrD8izIgLaLaqP3AJCVrv+qM/ECrG6w+Zoj9AuPBB66mjP8J95jhXv6A/oLQW8zVopD8Q91zRhgauP9DRbAN8xa0/aCmsuW6XqT8AsddZNAW0P8AciCpJsa0/ZBjhfSxNpj/Ilc3pDsCdP24SRTBbDuA/FFP2s6aU2j9EuqFXlNnTPw+t/0UAAc8/PHFuuwHp2T8Nsz6mr5fTPztW/dhHF80/cSmAZHKtxT+OKf2078LBP5au83544Lg/y4P1x9v4sj/4EWfa0pGtP1DgmbqK1NA/amxLLGvDyT8ZZPx6tavCPzAtgi9vD7g/kC3pOm3Jqz/Q/frp+7uhPyDs7r3fTZ8/WEF+pMGHkz/wtZNSxFaTP+awzFZYnpc/VueFhBp8pD/QFstHMJikPwSRp6qyUqE/2E3pUleMmD8UUkN8eqaGPyBRrNxymYY/QKqS906CrD/Q24EUntusP1BAfVHBua0/XF1jbmvXqD/g/M6wDNOjPz6voT7g8KQ/BG2Az7F1pD8Q7p983zmkP8RMvCqXeKQ/lLbS4CILqT8yee0bADamPzC/zcamHqU/yJX3/oEDoz/gbFfwxJanP2jtAIhA6KU/3EfBgLICqj96WMGtqaysP+gcE27z1ak/tiWcm3C2pj/gW5ZmJ/mjP7ppelr0Sas/MAwWSy4Ipz/w85hX58iqP/DJiNsvd6s/wqZgi5BjsT8QJEHMyrivPzQMKYRKra8/0PeKslKHqz8SIr4aPr6zP+IenH9FO7I/Oip9Y3qQsj9cKMe65NOwP0CM1H6rRsA/hEPHywl1uT+2/PfPPjG0PwRGdLKVKbE/o7EkjT8lwD/8umZVFae7P8fyWqIVKLU/EIhz/JMJsj9WkAcGf8aqP7T2YyVKXKs/EJrnQou5pz9AA8vezLCoP+gQxQDHB60/8KpMVDCnrD+EnoFF0hWpP5BLqtDM/KU/gIgVZe5UqT84wA2gZyWoPyAlH24hP6Y/+BSX+11upz8/uUxtZhOwP1R6qqaM0Ko//PFM+mEkpz8IhXg1qOqmPwXqeVEaS6k/OAC4AIjTpj+cK9vmwrGmPxAEz7C+K6k/1nwzH21fqD8Qm2ofd1OhP5Bo7PsQkZc/gB6xQ/Xtmj8gpmoQ0sOtP+ieCbS76Ks/+PTLc7XWsD8Ol+sEogWtP9Rx6hcGe7E//C17Vm5Aqj8s+ccJSyyqPywHbB7nTqs/nnNKyTTi6D/L8bHkbQ3lP4JkfSL/Wd8/08KENd4Y2D8cudD+7afRP/AftxvJh8k/biLhQLfVwT9oY+AsRPO4P8Sf9oY3A6w/IPLz+iKGmT9QVw2nRMGIP6DYkaZ/V40/1hvjEYrQsT9WDpMGy1usPxACqq29I68/qFpBm7Hjqj+QtF6WbsaaP4A7/uieKJY/QNAGPXb8mz+k9wBPrAagP2gATHDHa7A/GK9ByYibqj8oibBU1H6gPz8hfMVpAJo/EBDrDrJ6nz8g2PrdsG2hP1ybe7e7pKM/yCsfxku3oj9QeyD9qHOvP+gV4IftOLE/lKjcivcDsj8NgWAMCjCwP9C6H02cDLE/kLP0+wANsj8MMGc3yNSuPyy9PpOuvao/ZFKRHPlmtD+cQtWy+Pu1P2IRiBjAwbM/2IN3OULBrz+g/G9xtOimP6w3qsN/Yqc/fq9tuf44qj/E602rvQWoP3wmGIiUhas/xApa5cQoqj/KMaqMfWmoP3hmHg4gs6I/oAK5BH0WsT8sxjDL14avP8hBWyHcqKo/kP7t3T26rD/IJc5E6CS4P/SymEYaz7Q/ILKEGugLrz+iEPXxR6qoP/juYnBpFqw/iVDijQmRqD8zGyc6C1mqP/BO6E2R8aw/MBnV9DI5qz9ktCkEbcimP0fMTznlwKI/KNKVV8MqpD+4FBWb9pqlP9zDEMSAM6U/WOJbO/3Xqz8s2fmZ39mnP2wVEqwamq8/IDFmSONWqj999pJGhP+qPyAyBlgsdKg/EL6hhylvpj/ctenQI2mpP9e4Ka9ZXac/CDSXhjw+oz8UeQHa74WpPxweiK1bsKU/rObJPdY8oj/IifbC75ynP2Z/4Kt3xKU/0NvvOT9MpD8gf3331JmfP1C45c6ycpw/AOADgJQcrj9OfbNnkh2sP+/9V38fNqY/wEQ3LCz5pj9wRJAWR4SsPxg8CSKVRqg/koqlJATHoj8wAi/db7KfP0Cdq9diHp0/CP2kpLRaoD897AFpipGiP9TDLj8CW6U/oM+IhkO2oD94dUyV31ChP/CEfkLPfaQ/SCDOXe0hoD9ITAwAvBi1PyQ2hZvcxrM/QN4ejebFsz/exrvnoFatP/DqpsaQvrI/HMkNv44hsD9w4ckrmMWuP2WvW+REMKs/OLo0/EoZsz/RPfdK9G+yP7YZxlmm+K0/nDMmNo68qT8gySJdDC2tPxhhEfYtAas/mL82HjRFqD9k/50gg6eoP+T7bQ/VmrE/POXmdUQ6rT8soulU62ioP/DFaW8nmaM/MA6TwgXEqj84FGDOG/WqP9gEIwlItqk/O33SZYI3qz8QbXgVZVKlPygOBKpvkqE/Ho77RDmYmD8oh0oIgAadP1Da9D8JJp0/AFPPt04DoT8UfRKZfzmjPzyXHdWiFKI/aKlZ8LwBrj+YzSyZIuakP3xZMoDgDJ4/+FQ2iAIznT/cMRts2nObP+g9RDXP3Js/ds1I4RYuoz9orR/wEuigP1B3OEu2Rqs/kE0MCi/ppD8YiSe8g42iPw6cN0NM46A/yE2Y2EGQuD+iRsLhs8S4PyDdgqEkTrM/OI8mzwKOrT/4lFyVJLK4P9ydP2pz/LU/GDouyUuTsz9viFABs06uP3zBYkxsnLQ/1jQM+gNnsD+gHHCMan2oP/Czy6drI6I/7Jtnl8/Voj8g/yV4lHKiPxOkE9YZK6E/cKCdbhMnoz/WthjfjlGnP6xbgCrJIaA/sBikwuHZmD8Abipl4KiXP8A9UCBFe6c/MJ/gCkNDnz8AZJGe3C+cP9tUY3BoyKE/wGoEHY11oz/wmn4Kr96cP2AZJHfHkp0/ALrjGxzOlD/wsLmbz7GpP0hOo7/KHKY/iIR77D/HoT9aUuUGBnaeP2DYZyXzzZ0/1DA3mGKQlj+c7K9NzE+TP/Cy38mJEZQ/oPbxB0PjqD/qHlfXGbajP6Zv1aOBN6U/UG8/XTdYnz9YH+YzuUKdPxgSA+FxS5s/GKMH1ZFomj8gseuWy8GOP5japXgMEaA/wEcxXU01lj8Vrk+EeWeVP/A3edBj6pE/EBVHRdQ1oz+427Z2hsOWP4Cq84k0vZ8/wIR8cZBWmz8wfHv8mS+jP5h1ZjcjVJg/5MV33t2Nkz9oLIao7YKSP/z0GpyMxaU/YFr5rYW/oz9sB/8VsVSiP1AW4xj6u6E/EOQ1fVlcpD+wgVTRQWGjPyCA6rCnG6A/cIzPH1aHlT8QKyZ32vejPyomwxdXjaA/li9FVy8smT8AMd/DEJ+SP5D6MjT1cKQ/HJB8AZ7/oj+8rgy6ZDGbP+ACUONuapw/gE6vEzVioT+IludBmgWVP6S6adNHE4o/SPuiur7PlD/qKkqIYculPyCPQ1vadJ0/1LuSqkpumD+ww/I3beWVP8RKcF/3+ZU/kEmeKm/okz9QnEJLamOOPyBTrMi5TY4/hNTumgydlz/orC2DH9+dP85NILz4qpM/QMrkmJK0nz+YYhr2m3qjP0xBom6fE50/uKfTNlGtkD9g/VJRFZaRP8IrCEOde80/6c/Z8H8Fyz9z03UAmV3EP7QhaKf81Lw/pb6K+neHzz9ZiC6QgpDLP0CGBM1pjMQ/eOpBWKSbvj/qtDQJtou2PyBFBDbNfLA/TOd5OcD2oj8QLiUU/gadPxAfbWz/VpQ/+DiHpXWilD+Q7EITEu2KP0D9lj3MMZQ/4BajiL+olj8QmojLROueP75UswhlM5g/+GlYGTtlkj/eWPEo4TCSP6h18mqkOZE/4DlwtatElD/gAgZoOVmYP4IF9K44kp4/ICvXPp9Zlz94VdZq0EyNP4C0E3mhZIY/FJjjEOLonz+wWFwPcX2dP3QEK7J6F5k/6HY+WTWkoD8AsTw4PESlP7QIGpjgUKA/MMJUuqr9oT/Us8NwbI2bPxB1aqkUS58/ZM7iQBjxnz/0fCdiUT2eP+gq71nB3J4/NP7GOJaPtz9wQGKUkWeyP2xzQGhn6q8/mHbVsB1Vqz9IIFVksFGoPyCNmIJkAqU/DLnZokkvpD9Q6pYqCNigP+hUmPX9UpA/gGDRRwegjj9k9AEFfh+PPyAaW9fuyJE/jGsga5nYqD/ArshqOOKpP3kGUyRET6Y/vDOqcOBQoD8wl++6SAatP/APhaMMFak/OG+8+aXGpD/C55lvj/ykP6B0dz/Aa7U/1h/we0b1sD/IS2G09ImrP3ZsbPg386I/aMP2LDyQwD+EQbdgFtK8Pwwb//bderY/kbYSERpRsj+4udn9af2tP7+3y3Vnaao/EF3V4erYpj/kPqfZkEegP0gFYTLejKk/JoCr0uiCoj/AnEc6nkCfP6x6N1+3uaA/kC27YCVirz/Yhs1ZV4KkP4CECqk30KM/3hX6xWJyoD9QdTRjn0mlPxBGCztJSZc/cBqn6CKblT96B/gVPTSSP6A7xtZ7fJQ/AG+tDLFnlj8AlB9i9O2TP3iS8vk2R5Y/iMGzh4dwqz/kDCiz1SqtP/h9aKbctqY/eNx57zc5oD9ARfdGxV+YP6C3TYj7T5c/oPqbxcUzkz828pHPywWVPwDS3QywypY/wGGqYI7ojT9AkZAHdMaQP1j488m8lJQ/iG63CXgirz9MY5Wm9BWwPzAULP00Ras/sMGX3hQDqj9Ia7HnTte0P6DaFBqAhbA/eAPHRfF9qD/a4h8z9simP0QgUWQhobc/G5UPM9Fosz8Qu1pkOzKyPwy2+sQFd6s/3Myz/uxEoz+4vx41jUGXPzKU9Q1PnJc/cOdXLBtYlT84Qgt9pPioP4LF0oLKlKs/uGe41Utxpz8gARphc2CgP1C0K32FdJo/aJDbbDxilj+ALBY2JhKXP8iSR6v6t40/UEcJ7qs8lj+EzvXwpNedPwhsrRHtyJU/kBQxdxL1hj/Mb691YTatP66c3k9536c/38cmX6eWpT/gWM+o4uGjPxi1D85y9bQ/xLFDrY13sD+gzeLTGAilP/5KmjNqs58/4E1yNt+npT/shPqUqFygP6qI5A2kxaA/oEBJXnyYoD8uw3Gbe6i1PxnUFNRLTbA/4sNL/1gwpz/4YJtDDu+jP5AikDk57KI/On8O4uBbnj+UEXP+sbmhP8j1svdvqJY/SFQPKlRMnT+I13GdkhugP4i4++4JFZU/wMYbJAz2jj9orpI9dfCJP2BcSKvjGpI/gLgszU7EjT+gCPRR5h+CP1sI8l+QwbE/hCgZ/aOqrj/pg9iAPnunP/CZXWAUQKM/vjbHK/WKtj/MdJ0ODuyxP3CbkpCJ5qk/oNXO02DCnz/qW1yuoA/IPzZ0QE/nGsQ/pg6Jr4eDwT/MWSguQnO5Py/OLp08l8c/Jjzcw+Bcwz+rX+m9M1y+P5Q1MlGOBbc/bSMH909asT+gQHHHCUOuPxSrs5uCAaM/EOt+ziwCmz/APBDpn36TP+hsM4Is45c/ZGBLi4bElz8AkHUBl+eLP/AQD1y1bZo/lWB5xvdnkz/69sWMsfmTP/CHGNt03JM/GgYM+zoLnj8oyWfmW6GUP0C2SMoqCZY/EFkOYezIlj+QtKAdumqRP5jeHNLe2JQ/qN9DXSrslT9gPwvvA3GNPxhPgqu1KKQ/fCsmjiWkoz9ErbNj8vGfP7Bn4pOkBZY/oOt/A8k1rz+ACE/pPgqpP1BhZDpTUJc/cJ/3tBJthz9YG18hxqypP53/pZ63l6k/xCJlGSRFnj+YiFsL4AugPzDl8Y5aY7g/yLaTb/Nosj/5azCUPFypPxhJSVVOJqQ/qE4NiFudoD9wynVDgO2XP1hCDE58/pc/aFOUysbpmj+0/rc1zEyTPxBsc/duBYs/AITAZMxKjT/g4R+i6DKTPwyPdErsvqU/OHY+lt4rpT8XbKT8ezCjP9hY1y0PzaI/oEr6n9hpmj9ohxeID0GaP1gPuH8TlZE/4A0C7/kLij84MViLwXOwP2ifXlHTpaY/mBFXLUkDoD8se3qitF2gPxjOLRpz26g/YCv1dxkVpT/IPF4UI0yiP5jM9APkC50/4G3pVxnSsT9I7QugLEawP5AlFMkA/6k/22lG7KDZoj9UbhsvkYCjP2ROGALDr6M/wXn4XYYWkz/g/Mlh9AKUP+jjBA5sv5o/AAgKEq8bnT/NOkAMMoidP1Cma/2eyJw/2KcWT/gIoj+4vOw528mkPwjsOA99ZZk/ILmKjMd/jT9UonW+726bP5CuYy6nJI8/2ItrXjS6hz+AnVy1pteEPzCNvurfr6U/kEB9H8lynj9gY8pT3k+dPwpB47uSnJU/iC02aFQLsj/Y2qqAJoqvP9AC1mJnJ6U/xFPM+Gfdoz9ALRjLz3mqP2jPT9Sxj6k/+LmrKLC8oz/uvVC6BqCWP5jYilIvVag/jHTFUQKLqD/428lUQdijPxgu8y7xHJ8/0ME/qPsEoz9gucwHqYCXP+ANj8M1Eow/qJtFHL64kT/ApZq2ISqMP8C2ZtHSpoo/gKV2J0F7iT84jXoVbZOEP+BTKDpVj58/YBsFpOAcoj9gyhXh0o2fP0iFPEPZcZU/oK2ldwkNrz8Qt6AHlWaqP6j8loBsSaY/1o/37rQnoz+Q2ELzET6nP77WsQE4958/1AUiCefHmT/QCAeTM5mbP8BCPqQ4mKg/oFraaZ+5nT92rnKH+HOUPwATcmQpu4g/6Aiz3oeVoD+ww5ozKJieP1g3HARUkZE/QErUOvfIkz+Qy8qH0+SRP+D7GdAyHpM/OFyhJR6Dlz9QGCUy2UKGPyCrjsEEYps/GAAxazzokj/4p+TrDzaHPyAgdc0Yv44/cImueXfCmT+grHOan4eRPwOMjphPV4Y/sEwqE1W5kj+QeBl4jeOgP4Am/mPOApw/oBbBWeC9lT8w7LNa2CaXP7h/0cZDBZU/fF8A1HDukT/UaqL8VOqOP+Bok/SJxI4/CHzB/UD0lz/I7RYsPD+ZP9zK8i2W3IQ/8NY97vwlkD/gOUhPEhObPzPZa8Lxz5g/YCXQp0velT9A5jiQoamSPzigIna465Y/iArVv6YwkD+E6xzBiXKSPxBZ2qnMUpM/GrR/DEH3pT/wQamUs/CgP8T3qqh1t6M/YOX9hd1zlj9E+A2cST+dPyhObPFw/ps/gJ98Wefrmz9gr2cdy/qOPwjLTomAuJg/SPzxJK5BkT+t6IKztAiTP4CL5b5xmoo/4TCfl7OTxj8SUwu5mYnEP54BSGX6B8E/2Jr6GBCKuz8SG6n2mHXLPwqorfFPr8Y/MuoFxdO+wT/E1cEkLoK5P7yJz62DX7Q/BPMW7XL8sD9kAdA1V7epP6B3QHjki6M/FCbVLXpfoT+ooR1nZVibP7iYg52rb4Y/gBH9WQsjjj9AfHnFQN+dP4aORJFeGZU/5L6XWJZ7lT+Ih3lN7qiQP2ZItvko458/2Ey7uY/Rlj9A3oRvwNmaP3DR6hftQJU/huofxx+IlD8wh3rQa4ySP7BGtznhZIs/gMQ+MU9oiT+Q2GWgUbKcP2htszLg05E/QFRHDDf3kz9AjhMFKweZP8BTR5XK1p8/yJtGdM5Wlj/Q7T6K3vONP1ANViD8cX8/OBHM4g0Jqj904mSLNlinP4CU98dLuJ4/gGTUp9iukz/kablspPG2P56FuMGbM7M/xdiP5hL3rD9YoDt8vTmrP2jLY7VTuag/PDwk+0yNnD8suFDb/pKSPwAl2nXZ8ZA/bD5IdIfvkD+QPUX5EY2QP3yhwAnJm4k/4B3KqEe2ij/QGQ2y/+qUP9CtxEacspQ/yvauowBYkj9AWDGmSbSRP5DaHGknl54/IIqkgps0mz+YfQGJsR6eP+x0i/VQH5Q/YOnsLVLhqD8ozqb/3pWlP0BTTUTdp6I/DeqGMOXSlT/Ap/sIXvKuP0jOZDlwyq8/VN8GTeAHpD9ILkIurvadP0xKn/ARK8A/mMSMPhScuz8A1IzDhpq1P9mz3vDh8LA/MGe3uRBRtD9dYqe0RzurP4ht1A87CZ0/MIvLcKNXlj/c05IJPOOxP1e+pYMF3LA/Ih2MnXydpj+8R8E6eCGkPyBZHUJVGaA/R/QylUfsoj/EXP3H7K+gPwjZtcAQtpA/2Ot2MXKpkz8AaRP2EciUPwDQ8xb6mpQ/sI4U/H4pmz8gM4hhFN+fP7j+RX54j5o/MP9hapO9lj+0ECmAf+SSP4Dz4QRunJA/gBh0Etddmj9g7wrCWX2QP6DHAa5bNYI/AHT57qtYmz+QT5/JWjGeP7BDj2+jFpY/APvK5yEDmT+gw+f9vQ2ZP7BMvQuTZqA/eNagJ7l0nz9U9O4DiqafPxARcwSJG7Q/+CEX9DuruD9kfZqsOaezPw5xE6WLjKw/gL6ifFCrvT+qz3eRTnK4P/4/mpEX7bA/AlWKvLU6qT+wYvmEtj+1PxAHA4ScxLA/8Mfcpl1frT/OtcjYwgepP0g1lyYIgrE/wWnwSPA+qD/WIeOoR5elP8jGBW8Y36E/cNU7QjURmT+c42pVxhWWP3kqG9NMe5Q/EOf6DXNfmD+wndaMreePP1hGU8hOuY8/peZEWH+TiT9A5Rd968B+P4Aj0uVopIw/AB4yh5boiD9gGFozLo6DPyB+lKyAvY8/cCJCDcw1nj8YyJ1ZnmGdP1Qv6Xm/r50/EJlD3EjAkz84evyxOPeVP/ArMk5PupU/dCDlkZokkD9gSH2W5amMP+BeObBteqI/8DsjOWHKnz/wsYkT/jOfPzA9zDB7e5E/0CIc/Wmpjz/E/6qZJq+WPwrVYCrWAqA/AAO7uhwfmD/YtoiFJsqbPxzfobw8R5k/cNCMFNM6kT8AGW5nsPaQP6AUKoXVXoc/ooWMfO+nkj8QX+DrnziUP7AJX9adW4o/7qqk28Ksoj9gNvYLTc2dP7j5Eyn2M48/0JjwLClLkj+q5hEcj0WhP0Cy98rxdZ4/wDgTkR9zkT9AAtYIwoKMP4QnSdmk0po/KG03hueCmT8EWHBsCGeaPyDjxCl6+I8/hC+w9LoOpD+a5cvGwiihPw0hUI/SqpQ/0INFog4Nlz9Y3/VbDJ+8P3CeoXTD6Lk/5gkVPCv/tT+sIYCfzYGwPxBmATPtb8I/M8HlnTigvT8Oc5BHzWS3P2yGMlpTubA/+tR5yHpftT9saBf35/ivP7gj9zxnUak/uP0lwXp1oz/YwQFfNRunP4gO7E/llZ8/6HVSJ6Ommj9gSkvIlWucP3Ak8119OZQ/6k9YFayulj/wJLNOSOmNP/gOmP7EfZE/NkmOmUMcnD/wk/rBf0WSP+gBqB7KIYk/gKulEM0Fhj97Oc2Wo5OWP5B9tBh9OJI/wMS6Nar6jT+wlwJMOmuSP1yMAzUY3JM/yBtD3eOHkD8YHLBtf6SJPwD+WDYeGI0/8Ng18ZZgoD+YQJyvtaOcP2CIuvuLG5Y/TJ4Po72Hkz+Q3/xYMEKePwoN5QuDLJo/TBlHjGZ5lT+QTO5Hlc2UPzipaNLrGaE/FAQDjcMTnj83RuQnvhWVP/DVmovjwpI/AFxST4hzjz9w7wb9ASySP8B07MuNIJM/oGEst4v8iz9U8nHchHiXP8if4yw2IpI/ZFaPUKZKjj8gjiHkcfWIP7xLv8kunaU/2LVml/pkpD+JfTSe5UaeP5DJeK46Ipg/GBQsUCtfpj+mWV7Sq3mjPySOYV4KJ6M/6F3dTAF7nD/40WgJhtywP+gEgbOt+ac/wJ71V7wbpD8SJglQ0nudP1Dho9xaLb0/QE6DzpQUvD+KKEL3WcCyP0xMsCnLDKo/oIfECBN1qD8QvafMH7ChP8jgId9wIaA/IVf5rA0olj+At9ucPxWqP7qJYvxhG6Y/YOjGlW6XnD9wiarTxrmVPyRoYuTJ2bE/3inXqI99rj9AQcnTF/emP7huB2o8fKE/sJw5Qgllqj8yw4Q9DKChPxCNy+Jdo5o/uPkr9VbSkz/4sWaVVWKJPxBayxtBGII/2C4yAfIKgz9g87/fSIGJP6CEOu0wU6o/CO/b0TiOoz+IXRFNd52hPwx50qeKXpk/0L/jMqHppT8AqoXZ2jGiPzBgn9UiUZs/aKfL6c8amz/Q9EOI9Q2jPwhyYXUSI6M/PAMp0AcZmj+oxeIa+DmaP5h/6ew27pg/xAS9viATkj/SL9G2h1eVP4CMbN2H8Js/iAdEVIYboT8qvzmsVTKeP1N9nsYnnJA/gMXqVeFskj8gK3WCzkqyP1jbr+Cx3a8/MfkMVK5Loz9A1mVR4+WdP/guHHQWSJI/VJ8WiwH2hT9IEybaW9uDPw==","dtype":"float64","shape":[5000]}},"selected":{"id":"1280","type":"Selection"},"selection_policy":{"id":"1281","type":"UnionRenderers"}},"id":"1251","type":"ColumnDataSource"},{"attributes":{"source":{"id":"1251","type":"ColumnDataSource"}},"id":"1255","type":"CDSView"},{"attributes":{"callback":null},"id":"1220","type":"DataRange1d"},{"attributes":{},"id":"1222","type":"LinearScale"},{"attributes":{"plot":null,"text":""},"id":"1275","type":"Title"},{"attributes":{},"id":"1276","type":"BasicTickFormatter"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1252","type":"Line"},{"attributes":{},"id":"1278","type":"BasicTickFormatter"}],"root_ids":["1217"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"624c1aca-8b59-4294-a4b5-863de4597f54","roots":{"1217":"0df81e24-21a5-457c-8c30-f28f95d55e9c"}}];
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
this sum of differences plot. However, we can’t just sort the array and
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

Try to code this on your own - it’s a good exercise. If you get stuck,
here’s our implementation:


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
            <span id="1337">Loading BokehJS ...</span>
        </div>
    </div>



.. raw:: html

    

    <div id="3f65ffd5-9226-4be7-896c-166f7c9f0558"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#3f65ffd5-9226-4be7-896c-166f7c9f0558');
        
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
        var el = document.getElementById("1337");
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
      };var element = document.getElementById("1337");
      if (element == null) {
        console.log("Bokeh: ERROR: autoload.js configured with elementid '1337' but no matching script tag was found. ")
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
          var cell = $(document.getElementById("1337")).parents('.cell').data().cell;
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
        
    
    
    
    
    
      <div class="bk-root" id="94791245-2045-4d3e-adbf-af8288f84e30"></div>
    
    </div>



.. raw:: html

    

    <div id="cb15e3e1-e4aa-4b3d-acec-cdcea5bd21e3"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#cb15e3e1-e4aa-4b3d-acec-cdcea5bd21e3');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"c66516b4-de41-4e88-b6a5-2626abbe6e1b":{"roots":{"references":[{"attributes":{"below":[{"id":"1347","type":"LinearAxis"}],"left":[{"id":"1352","type":"LinearAxis"}],"renderers":[{"id":"1347","type":"LinearAxis"},{"id":"1351","type":"Grid"},{"id":"1352","type":"LinearAxis"},{"id":"1356","type":"Grid"},{"id":"1365","type":"BoxAnnotation"},{"id":"1375","type":"GlyphRenderer"}],"title":{"id":"1405","type":"Title"},"toolbar":{"id":"1363","type":"Toolbar"},"x_range":{"id":"1339","type":"DataRange1d"},"x_scale":{"id":"1343","type":"LinearScale"},"y_range":{"id":"1341","type":"DataRange1d"},"y_scale":{"id":"1345","type":"LinearScale"}},"id":"1338","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1348","type":"BasicTicker"},{"attributes":{"plot":null,"text":""},"id":"1405","type":"Title"},{"attributes":{},"id":"1357","type":"PanTool"},{"attributes":{},"id":"1406","type":"BasicTickFormatter"},{"attributes":{},"id":"1408","type":"BasicTickFormatter"},{"attributes":{},"id":"1410","type":"Selection"},{"attributes":{"callback":null},"id":"1341","type":"DataRange1d"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1410","type":"Selection"},"selection_policy":{"id":"1411","type":"UnionRenderers"}},"id":"1372","type":"ColumnDataSource"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"plot":null,"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1365","type":"BoxAnnotation"},{"attributes":{},"id":"1411","type":"UnionRenderers"},{"attributes":{"callback":null},"id":"1339","type":"DataRange1d"},{"attributes":{},"id":"1343","type":"LinearScale"},{"attributes":{},"id":"1358","type":"WheelZoomTool"},{"attributes":{"formatter":{"id":"1408","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1352","type":"LinearAxis"},{"attributes":{},"id":"1361","type":"ResetTool"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1373","type":"Line"},{"attributes":{},"id":"1360","type":"SaveTool"},{"attributes":{"dimension":1,"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1353","type":"BasicTicker"}},"id":"1356","type":"Grid"},{"attributes":{"source":{"id":"1372","type":"ColumnDataSource"}},"id":"1376","type":"CDSView"},{"attributes":{},"id":"1353","type":"BasicTicker"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1374","type":"Line"},{"attributes":{"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1351","type":"Grid"},{"attributes":{"formatter":{"id":"1406","type":"BasicTickFormatter"},"plot":{"id":"1338","subtype":"Figure","type":"Plot"},"ticker":{"id":"1348","type":"BasicTicker"}},"id":"1347","type":"LinearAxis"},{"attributes":{},"id":"1345","type":"LinearScale"},{"attributes":{},"id":"1362","type":"HelpTool"},{"attributes":{"data_source":{"id":"1372","type":"ColumnDataSource"},"glyph":{"id":"1373","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1374","type":"Line"},"selection_glyph":null,"view":{"id":"1376","type":"CDSView"}},"id":"1375","type":"GlyphRenderer"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1357","type":"PanTool"},{"id":"1358","type":"WheelZoomTool"},{"id":"1359","type":"BoxZoomTool"},{"id":"1360","type":"SaveTool"},{"id":"1361","type":"ResetTool"},{"id":"1362","type":"HelpTool"}]},"id":"1363","type":"Toolbar"},{"attributes":{"overlay":{"id":"1365","type":"BoxAnnotation"}},"id":"1359","type":"BoxZoomTool"}],"root_ids":["1338"]},"title":"Bokeh Application","version":"1.0.1"}};
      var render_items = [{"docid":"c66516b4-de41-4e88-b6a5-2626abbe6e1b","roots":{"1338":"94791245-2045-4d3e-adbf-af8288f84e30"}}];
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

    [3453, 3809, 3649, 4137, 977]
    


Generating a Template
~~~~~~~~~~~~~~~~~~~~~

With 5 (or numPOIs) POIs picked out, we can build our multivariate
distributions at each point for each Hamming weight. We need to write
down two matrices for each weight:

-  A mean matrix (1 x numPOIs) which stores the mean at each POI
-  A covariance matrix (numPOIs x numPOIs) which stores the variances
   and covariances between each of the POIs

The mean matrix is easy to set up because we’ve already found the mean
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

We’ll use our function to build the covariance matrices, as below:


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

Finally - let’s confirm these matricies look A-OK. Basically you need to
ensure there isn’t zeros in them, which normally indiciates you didn’t
have enough data.


**In [27]:**

.. code:: ipython3

    print(meanMatrix)
    print(covMatrix[0])


**Out [27]:**



.. parsed-literal::

    [[-0.31381836 -0.2925293  -0.29775391 -0.08867187 -0.22832031]
     [-0.32799072 -0.30189819 -0.30874634 -0.09345093 -0.23168945]
     [-0.34043471 -0.31050108 -0.31671798 -0.09967438 -0.23927702]
     [-0.3529535  -0.31865852 -0.3253754  -0.10592614 -0.24388895]
     [-0.36504889 -0.32681082 -0.33223663 -0.11262358 -0.24870513]
     [-0.37755837 -0.33486365 -0.33948135 -0.11908047 -0.25315025]
     [-0.3877338  -0.34239707 -0.34782305 -0.12656393 -0.25644389]
     [-0.39961611 -0.35063084 -0.35459882 -0.13179665 -0.26077025]
     [-0.40927734 -0.35537109 -0.36162109 -0.14038086 -0.26601562]]
    [[ 1.72941308e-05 -1.14691885e-06  2.17688711e-05 -1.43553081e-06
      -5.75216193e-06]
     [-1.14691885e-06  4.16078066e-05 -9.07998336e-06  7.48885305e-06
      -3.85485197e-06]
     [ 2.17688711e-05 -9.07998336e-06  7.33726903e-05 -5.13980263e-06
      -6.29425049e-06]
     [-1.43553081e-06  7.48885305e-06 -5.13980263e-06  4.36280903e-05
      -9.52670449e-06]
     [-5.75216193e-06 -3.85485197e-06 -6.29425049e-06 -9.52670449e-06
       4.03153269e-05]]
    


Applying the Template
~~~~~~~~~~~~~~~~~~~~~

The very last step is to apply our template to these traces. We want to
keep a running total of :math:`\log P_k = \sum_j \log p_{k,j}`, so we’ll
make space for our 256 guesses:

::

   P_k = np.zeros(256)

Then, we want to do the following for every attack trace:

-  Grab the samples from the points of interest and store them in a list
   :math:`\mathbf{a}`
-  For all 256 of the subkey guesses…
-  Figure out which Hamming weight we need, according to our known
   plaintext and guessed subkey
-  Build a multivariate_normal object using the relevant mean and
   covariance matrices
-  Calculate the log of the PDF (:math:`\log f(\mathbf{a})`) and add it
   to the running total
-  List the best guesses we’ve seen so far

Make sure you’ve included the multivariate stats from SciPy too - it’s
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

    5c 56 53 8f ff
    24 5e 0e b2 48
    10 2d 88 29 2b
    0b 21 2d 2f 2b
    58 eb 21 2d 2b
    59 3b 2d 58 2b
    c7 fa 70 b4 2b
    3b 8a 70 b4 2b
    b1 ee 8a b4 2b
    b1 ee 8a b4 2b
    3e ee 8a b4 2b
    54 87 8a b4 2b
    87 54 8a b4 2b
    b4 54 87 8a 2b
    bc b4 87 8a 2b
    54 b4 87 8a 2b
    54 b4 87 8a 2b
    54 b4 87 8a 2b
    bc 87 b4 8a 2b
    54 87 b4 8a 2b
    0x2b
    


With any luck you will have found the correct key byte! You can try
extending this to attack multiple bytes instead. The traces you already
recorded will work just great, you’ll need to make the following
changes:

-  Build a new POI for each byte number
-  Build new matricies for each byte number
-  Apply those matricies for each byte

Conclusions
-----------

That’s it! This template attack must have raised some new questions for
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

