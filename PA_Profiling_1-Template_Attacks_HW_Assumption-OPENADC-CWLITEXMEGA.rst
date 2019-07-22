
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
    PLATFORM = 'CWLITEXMEGA'
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

    rm -f -- simpleserial-aes-CWLITEXMEGA.hex
    rm -f -- simpleserial-aes-CWLITEXMEGA.eep
    rm -f -- simpleserial-aes-CWLITEXMEGA.cof
    rm -f -- simpleserial-aes-CWLITEXMEGA.elf
    rm -f -- simpleserial-aes-CWLITEXMEGA.map
    rm -f -- simpleserial-aes-CWLITEXMEGA.sym
    rm -f -- simpleserial-aes-CWLITEXMEGA.lss
    rm -f -- objdir/\*.o
    rm -f -- objdir/\*.lst
    rm -f -- simpleserial-aes.s simpleserial.s XMEGA_AES_driver.s uart.s usart_driver.s xmega_hal.s aes.s aes-independant.s
    rm -f -- simpleserial-aes.d simpleserial.d XMEGA_AES_driver.d uart.d usart_driver.d xmega_hal.d aes.d aes-independant.d
    rm -f -- simpleserial-aes.i simpleserial.i XMEGA_AES_driver.i uart.i usart_driver.i xmega_hal.i aes.i aes-independant.i
    .
    -------- begin --------
    avr-gcc (WinAVR 20100110) 4.3.3
    Copyright (C) 2008 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    
    .
    Compiling C: simpleserial-aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes.o.d simpleserial-aes.c -o objdir/simpleserial-aes.o 
    .
    Compiling C: .././simpleserial/simpleserial.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial.o.d .././simpleserial/simpleserial.c -o objdir/simpleserial.o 
    .
    Compiling C: .././hal/xmega/XMEGA_AES_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/XMEGA_AES_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/XMEGA_AES_driver.o.d .././hal/xmega/XMEGA_AES_driver.c -o objdir/XMEGA_AES_driver.o 
    .
    Compiling C: .././hal/xmega/uart.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/uart.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/uart.o.d .././hal/xmega/uart.c -o objdir/uart.o 
    .
    Compiling C: .././hal/xmega/usart_driver.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/usart_driver.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/usart_driver.o.d .././hal/xmega/usart_driver.c -o objdir/usart_driver.o 
    .
    Compiling C: .././hal/xmega/xmega_hal.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/xmega_hal.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/xmega_hal.o.d .././hal/xmega/xmega_hal.c -o objdir/xmega_hal.o 
    .
    Compiling C: .././crypto/tiny-AES128-C/aes.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes.o.d .././crypto/tiny-AES128-C/aes.c -o objdir/aes.o 
    .
    Compiling C: .././crypto/aes-independant.c
    avr-gcc -c -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/aes-independant.lst -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/aes-independant.o.d .././crypto/aes-independant.c -o objdir/aes-independant.o 
    .
    Linking: simpleserial-aes-CWLITEXMEGA.elf
    avr-gcc -mmcu=atxmega128d3 -I. -DNO_EXTRA_OPTS -fpack-struct -gdwarf-2 -DSS_VER=SS_VER_1_1 -DHAL_TYPE=HAL_xmega -DPLATFORM=CWLITEXMEGA -DTINYAES128C -DF_CPU=7372800UL -Os -funsigned-char -funsigned-bitfields -fshort-enums -Wall -Wstrict-prototypes -Wa,-adhlns=objdir/simpleserial-aes.o -I.././simpleserial/ -I.././hal -I.././hal/xmega -I.././crypto/ -I.././crypto/tiny-AES128-C -std=gnu99 -MMD -MP -MF .dep/simpleserial-aes-CWLITEXMEGA.elf.d objdir/simpleserial-aes.o objdir/simpleserial.o objdir/XMEGA_AES_driver.o objdir/uart.o objdir/usart_driver.o objdir/xmega_hal.o objdir/aes.o objdir/aes-independant.o --output simpleserial-aes-CWLITEXMEGA.elf -Wl,-Map=simpleserial-aes-CWLITEXMEGA.map,--cref   -lm  
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
       4064	    556	    248	   4868	   1304	simpleserial-aes-CWLITEXMEGA.elf
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

    2.53
    4.08
    2.95
    0.68
    4.38
    7.22
    5.24
    3.93
    1.73
    0.74
    


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
          <td>0.174572</td>
          <td>0.194102</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>1</th>
          <td>0.086538</td>
          <td>0.172190</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>2</th>
          <td>0.153543</td>
          <td>0.199401</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>3</th>
          <td>0.183268</td>
          <td>0.101594</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>4</th>
          <td>0.070705</td>
          <td>0.157190</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>5</th>
          <td>0.003353</td>
          <td>0.021599</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>6</th>
          <td>0.034820</td>
          <td>0.106725</td>
          <td>3.0</td>
          <td>3.0</td>
          <td>3.0</td>
        </tr>
        <tr>
          <th>7</th>
          <td>0.095205</td>
          <td>0.178926</td>
          <td>3.0</td>
          <td>1.5</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>8</th>
          <td>0.198211</td>
          <td>0.162801</td>
          <td>1.5</td>
          <td>3.0</td>
          <td>1.5</td>
        </tr>
        <tr>
          <th>9</th>
          <td>0.185461</td>
          <td>0.105148</td>
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

    XMEGA Programming flash...
    XMEGA Reading flash...
    Verified flash OK, 4619 bytes
    


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

    0: c3 10 d2 40 40 40 ca 35 c3 93 70 03 da 4b db 7d
    1: a7 0a bd 84 57 94 62 ae 1b 1b 66 03 54 6c 5d 99
    2: a6 d6 46 dc 05 94 8b a8 08 26 0f eb 19 c8 bd f4
    3: a5 78 1c 19 2f b2 be 28 63 55 3a d3 c0 16 f4 6a
    



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

    

    <div id="d3bc7399-dc6f-4fff-bacc-bfac1304e45c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#d3bc7399-dc6f-4fff-bacc-bfac1304e45c');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="86434496-04a2-43c3-9106-3cf46e4140c3" data-root-id="1002"></div>
    
    </div>



.. raw:: html

    

    <div id="43118584-43c8-4b87-b448-7d2849a93e54"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#43118584-43c8-4b87-b448-7d2849a93e54');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"22bc83ee-3437-44ea-8c9c-ed764d8cd525":{"roots":{"references":[{"attributes":{"below":[{"id":"1011","type":"LinearAxis"}],"center":[{"id":"1015","type":"Grid"},{"id":"1020","type":"Grid"}],"left":[{"id":"1016","type":"LinearAxis"}],"renderers":[{"id":"1037","type":"GlyphRenderer"}],"title":{"id":"1039","type":"Title"},"toolbar":{"id":"1027","type":"Toolbar"},"x_range":{"id":"1003","type":"DataRange1d"},"x_scale":{"id":"1007","type":"LinearScale"},"y_range":{"id":"1005","type":"DataRange1d"},"y_scale":{"id":"1009","type":"LinearScale"}},"id":"1002","subtype":"Figure","type":"Plot"},{"attributes":{"text":""},"id":"1039","type":"Title"},{"attributes":{},"id":"1046","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1043","type":"BasicTickFormatter"},"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1011","type":"LinearAxis"},{"attributes":{},"id":"1045","type":"Selection"},{"attributes":{"source":{"id":"1034","type":"ColumnDataSource"}},"id":"1038","type":"CDSView"},{"attributes":{},"id":"1012","type":"BasicTicker"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1047","type":"BoxAnnotation"},{"attributes":{"formatter":{"id":"1041","type":"BasicTickFormatter"},"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1016","type":"LinearAxis"},{"attributes":{},"id":"1041","type":"BasicTickFormatter"},{"attributes":{},"id":"1024","type":"SaveTool"},{"attributes":{},"id":"1026","type":"HelpTool"},{"attributes":{"data_source":{"id":"1034","type":"ColumnDataSource"},"glyph":{"id":"1035","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1036","type":"Line"},"selection_glyph":null,"view":{"id":"1038","type":"CDSView"}},"id":"1037","type":"GlyphRenderer"},{"attributes":{},"id":"1025","type":"ResetTool"},{"attributes":{"overlay":{"id":"1047","type":"BoxAnnotation"}},"id":"1023","type":"BoxZoomTool"},{"attributes":{},"id":"1022","type":"WheelZoomTool"},{"attributes":{},"id":"1009","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1045","type":"Selection"},"selection_policy":{"id":"1046","type":"UnionRenderers"}},"id":"1034","type":"ColumnDataSource"},{"attributes":{},"id":"1021","type":"PanTool"},{"attributes":{},"id":"1007","type":"LinearScale"},{"attributes":{"callback":null},"id":"1003","type":"DataRange1d"},{"attributes":{"dimension":1,"ticker":{"id":"1017","type":"BasicTicker"}},"id":"1020","type":"Grid"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1035","type":"Line"},{"attributes":{},"id":"1043","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1005","type":"DataRange1d"},{"attributes":{},"id":"1017","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1012","type":"BasicTicker"}},"id":"1015","type":"Grid"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1021","type":"PanTool"},{"id":"1022","type":"WheelZoomTool"},{"id":"1023","type":"BoxZoomTool"},{"id":"1024","type":"SaveTool"},{"id":"1025","type":"ResetTool"},{"id":"1026","type":"HelpTool"}]},"id":"1027","type":"Toolbar"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1036","type":"Line"}],"root_ids":["1002"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"22bc83ee-3437-44ea-8c9c-ed764d8cd525","roots":{"1002":"86434496-04a2-43c3-9106-3cf46e4140c3"}}];
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

    Trace 0: Textin[0] = fd  Key[0] = c3 --> SBox Output = b2 --> HW = 4
    Trace 1: Textin[0] = 99  Key[0] = a7 --> SBox Output = b2 --> HW = 4
    Trace 2: Textin[0] = 24  Key[0] = a6 --> SBox Output = 13 --> HW = 3
    Trace 3: Textin[0] = b4  Key[0] = a5 --> SBox Output = 82 --> HW = 2
    Trace 4: Textin[0] = df  Key[0] = b1 --> SBox Output = 9f --> HW = 6
    Trace 5: Textin[0] = b9  Key[0] = 46 --> SBox Output = 16 --> HW = 3
    Trace 6: Textin[0] = 23  Key[0] = 30 --> SBox Output = 7d --> HW = 6
    Trace 7: Textin[0] = 8a  Key[0] = ab --> SBox Output = fd --> HW = 7
    Trace 8: Textin[0] = b2  Key[0] = 36 --> SBox Output = 5f --> HW = 6
    Trace 9: Textin[0] = a4  Key[0] = fc --> SBox Output = 6a --> HW = 4
    



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

    

    <div id="0a58a2d0-a6de-43f3-9e09-52e78938926e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#0a58a2d0-a6de-43f3-9e09-52e78938926e');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="87032426-e2d1-402d-9b3e-544f436c22b0" data-root-id="1103"></div>
    
    </div>



.. raw:: html

    

    <div id="a6072e80-2617-4001-8052-721f04b9bb00"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#a6072e80-2617-4001-8052-721f04b9bb00');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"5ff9574c-1f8d-4d71-8985-0f7f5fee022a":{"roots":{"references":[{"attributes":{"below":[{"id":"1112","type":"LinearAxis"}],"center":[{"id":"1116","type":"Grid"},{"id":"1121","type":"Grid"}],"left":[{"id":"1117","type":"LinearAxis"}],"renderers":[{"id":"1138","type":"GlyphRenderer"}],"title":{"id":"1149","type":"Title"},"toolbar":{"id":"1128","type":"Toolbar"},"x_range":{"id":"1104","type":"DataRange1d"},"x_scale":{"id":"1108","type":"LinearScale"},"y_range":{"id":"1106","type":"DataRange1d"},"y_scale":{"id":"1110","type":"LinearScale"}},"id":"1103","subtype":"Figure","type":"Plot"},{"attributes":{"source":{"id":"1135","type":"ColumnDataSource"}},"id":"1139","type":"CDSView"},{"attributes":{"data_source":{"id":"1135","type":"ColumnDataSource"},"glyph":{"id":"1136","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1137","type":"Line"},"selection_glyph":null,"view":{"id":"1139","type":"CDSView"}},"id":"1138","type":"GlyphRenderer"},{"attributes":{},"id":"1126","type":"ResetTool"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1137","type":"Line"},{"attributes":{},"id":"1125","type":"SaveTool"},{"attributes":{"overlay":{"id":"1157","type":"BoxAnnotation"}},"id":"1124","type":"BoxZoomTool"},{"attributes":{},"id":"1110","type":"LinearScale"},{"attributes":{},"id":"1118","type":"BasicTicker"},{"attributes":{"formatter":{"id":"1153","type":"BasicTickFormatter"},"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1112","type":"LinearAxis"},{"attributes":{},"id":"1108","type":"LinearScale"},{"attributes":{"dimension":1,"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1121","type":"Grid"},{"attributes":{},"id":"1113","type":"BasicTicker"},{"attributes":{"ticker":{"id":"1113","type":"BasicTicker"}},"id":"1116","type":"Grid"},{"attributes":{"formatter":{"id":"1151","type":"BasicTickFormatter"},"ticker":{"id":"1118","type":"BasicTicker"}},"id":"1117","type":"LinearAxis"},{"attributes":{},"id":"1122","type":"PanTool"},{"attributes":{},"id":"1123","type":"WheelZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1155","type":"Selection"},"selection_policy":{"id":"1156","type":"UnionRenderers"}},"id":"1135","type":"ColumnDataSource"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1122","type":"PanTool"},{"id":"1123","type":"WheelZoomTool"},{"id":"1124","type":"BoxZoomTool"},{"id":"1125","type":"SaveTool"},{"id":"1126","type":"ResetTool"},{"id":"1127","type":"HelpTool"}]},"id":"1128","type":"Toolbar"},{"attributes":{},"id":"1127","type":"HelpTool"},{"attributes":{},"id":"1153","type":"BasicTickFormatter"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1157","type":"BoxAnnotation"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1136","type":"Line"},{"attributes":{},"id":"1151","type":"BasicTickFormatter"},{"attributes":{"callback":null},"id":"1106","type":"DataRange1d"},{"attributes":{"text":""},"id":"1149","type":"Title"},{"attributes":{"callback":null},"id":"1104","type":"DataRange1d"},{"attributes":{},"id":"1156","type":"UnionRenderers"},{"attributes":{},"id":"1155","type":"Selection"}],"root_ids":["1103"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"5ff9574c-1f8d-4d71-8985-0f7f5fee022a","roots":{"1103":"87032426-e2d1-402d-9b3e-544f436c22b0"}}];
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

    

    <div id="77d05931-361b-443e-9ea2-3e142e7d8daa"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#77d05931-361b-443e-9ea2-3e142e7d8daa');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="30c03143-3e39-4f00-be78-3402106607e8" data-root-id="1213"></div>
    
    </div>



.. raw:: html

    

    <div id="6285c1d2-2ba2-4cd4-9573-19a8eda9ed6e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#6285c1d2-2ba2-4cd4-9573-19a8eda9ed6e');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"8b02772d-f6bb-4746-83bb-b7a084034910":{"roots":{"references":[{"attributes":{"below":[{"id":"1222","type":"LinearAxis"}],"center":[{"id":"1226","type":"Grid"},{"id":"1231","type":"Grid"}],"left":[{"id":"1227","type":"LinearAxis"}],"renderers":[{"id":"1248","type":"GlyphRenderer"}],"title":{"id":"1268","type":"Title"},"toolbar":{"id":"1238","type":"Toolbar"},"x_range":{"id":"1214","type":"DataRange1d"},"x_scale":{"id":"1218","type":"LinearScale"},"y_range":{"id":"1216","type":"DataRange1d"},"y_scale":{"id":"1220","type":"LinearScale"}},"id":"1213","subtype":"Figure","type":"Plot"},{"attributes":{},"id":"1237","type":"HelpTool"},{"attributes":{},"id":"1228","type":"BasicTicker"},{"attributes":{},"id":"1235","type":"SaveTool"},{"attributes":{"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1226","type":"Grid"},{"attributes":{"text":""},"id":"1268","type":"Title"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1232","type":"PanTool"},{"id":"1233","type":"WheelZoomTool"},{"id":"1234","type":"BoxZoomTool"},{"id":"1235","type":"SaveTool"},{"id":"1236","type":"ResetTool"},{"id":"1237","type":"HelpTool"}]},"id":"1238","type":"Toolbar"},{"attributes":{"formatter":{"id":"1272","type":"BasicTickFormatter"},"ticker":{"id":"1223","type":"BasicTicker"}},"id":"1222","type":"LinearAxis"},{"attributes":{},"id":"1220","type":"LinearScale"},{"attributes":{},"id":"1272","type":"BasicTickFormatter"},{"attributes":{},"id":"1270","type":"BasicTickFormatter"},{"attributes":{"data_source":{"id":"1245","type":"ColumnDataSource"},"glyph":{"id":"1246","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1247","type":"Line"},"selection_glyph":null,"view":{"id":"1249","type":"CDSView"}},"id":"1248","type":"GlyphRenderer"},{"attributes":{},"id":"1275","type":"UnionRenderers"},{"attributes":{"formatter":{"id":"1270","type":"BasicTickFormatter"},"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1227","type":"LinearAxis"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1276","type":"BoxAnnotation"},{"attributes":{"callback":null},"id":"1216","type":"DataRange1d"},{"attributes":{},"id":"1223","type":"BasicTicker"},{"attributes":{},"id":"1274","type":"Selection"},{"attributes":{"dimension":1,"ticker":{"id":"1228","type":"BasicTicker"}},"id":"1231","type":"Grid"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1247","type":"Line"},{"attributes":{},"id":"1218","type":"LinearScale"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1246","type":"Line"},{"attributes":{"overlay":{"id":"1276","type":"BoxAnnotation"}},"id":"1234","type":"BoxZoomTool"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1274","type":"Selection"},"selection_policy":{"id":"1275","type":"UnionRenderers"}},"id":"1245","type":"ColumnDataSource"},{"attributes":{},"id":"1232","type":"PanTool"},{"attributes":{"callback":null},"id":"1214","type":"DataRange1d"},{"attributes":{"source":{"id":"1245","type":"ColumnDataSource"}},"id":"1249","type":"CDSView"},{"attributes":{},"id":"1233","type":"WheelZoomTool"},{"attributes":{},"id":"1236","type":"ResetTool"}],"root_ids":["1213"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"8b02772d-f6bb-4746-83bb-b7a084034910","roots":{"1213":"30c03143-3e39-4f00-be78-3402106607e8"}}];
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

    

    <div id="309c04d1-5b9d-4d87-b749-55e712f3140c"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#309c04d1-5b9d-4d87-b749-55e712f3140c');
        
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
        
    
    
    
    
    
      <div class="bk-root" id="1efebb78-8be9-47b7-810f-843908f05934" data-root-id="1332"></div>
    
    </div>



.. raw:: html

    

    <div id="421f3245-3948-41de-aa75-a5e18c52425e"></div>
    <div class="output_subarea output_javascript ">
    <script type="text/javascript">
    var element = $('#421f3245-3948-41de-aa75-a5e18c52425e');
        (function(root) {
      function embed_document(root) {
        
      var docs_json = {"8fbcb4e1-638c-41c4-95db-d1f65746489f":{"roots":{"references":[{"attributes":{"below":[{"id":"1341","type":"LinearAxis"}],"center":[{"id":"1345","type":"Grid"},{"id":"1350","type":"Grid"}],"left":[{"id":"1346","type":"LinearAxis"}],"renderers":[{"id":"1367","type":"GlyphRenderer"}],"title":{"id":"1396","type":"Title"},"toolbar":{"id":"1357","type":"Toolbar"},"x_range":{"id":"1333","type":"DataRange1d"},"x_scale":{"id":"1337","type":"LinearScale"},"y_range":{"id":"1335","type":"DataRange1d"},"y_scale":{"id":"1339","type":"LinearScale"}},"id":"1332","subtype":"Figure","type":"Plot"},{"attributes":{"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1345","type":"Grid"},{"attributes":{"dimension":1,"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1350","type":"Grid"},{"attributes":{},"id":"1339","type":"LinearScale"},{"attributes":{"callback":null,"data":{"x":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,215,216,217,218,219,220,221,222,223,224,225,226,227,228,229,230,231,232,233,234,235,236,237,238,239,240,241,242,243,244,245,246,247,248,249,250,251,252,253,254,255,256,257,258,259,260,261,262,263,264,265,266,267,268,269,270,271,272,273,274,275,276,277,278,279,280,281,282,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,298,299,300,301,302,303,304,305,306,307,308,309,310,311,312,313,314,315,316,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,507,508,509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608,609,610,611,612,613,614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713,714,715,716,717,718,719,720,721,722,723,724,725,726,727,728,729,730,731,732,733,734,735,736,737,738,739,740,741,742,743,744,745,746,747,748,749,750,751,752,753,754,755,756,757,758,759,760,761,762,763,764,765,766,767,768,769,770,771,772,773,774,775,776,777,778,779,780,781,782,783,784,785,786,787,788,789,790,791,792,793,794,795,796,797,798,799,800,801,802,803,804,805,806,807,808,809,810,811,812,813,814,815,816,817,818,819,820,821,822,823,824,825,826,827,828,829,830,831,832,833,834,835,836,837,838,839,840,841,842,843,844,845,846,847,848,849,850,851,852,853,854,855,856,857,858,859,860,861,862,863,864,865,866,867,868,869,870,871,872,873,874,875,876,877,878,879,880,881,882,883,884,885,886,887,888,889,890,891,892,893,894,895,896,897,898,899,900,901,902,903,904,905,906,907,908,909,910,911,912,913,914,915,916,917,918,919,920,921,922,923,924,925,926,927,928,929,930,931,932,933,934,935,936,937,938,939,940,941,942,943,944,945,946,947,948,949,950,951,952,953,954,955,956,957,958,959,960,961,962,963,964,965,966,967,968,969,970,971,972,973,974,975,976,977,978,979,980,981,982,983,984,985,986,987,988,989,990,991,992,993,994,995,996,997,998,999,1000,1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1011,1012,1013,1014,1015,1016,1017,1018,1019,1020,1021,1022,1023,1024,1025,1026,1027,1028,1029,1030,1031,1032,1033,1034,1035,1036,1037,1038,1039,1040,1041,1042,1043,1044,1045,1046,1047,1048,1049,1050,1051,1052,1053,1054,1055,1056,1057,1058,1059,1060,1061,1062,1063,1064,1065,1066,1067,1068,1069,1070,1071,1072,1073,1074,1075,1076,1077,1078,1079,1080,1081,1082,1083,1084,1085,1086,1087,1088,1089,1090,1091,1092,1093,1094,1095,1096,1097,1098,1099,1100,1101,1102,1103,1104,1105,1106,1107,1108,1109,1110,1111,1112,1113,1114,1115,1116,1117,1118,1119,1120,1121,1122,1123,1124,1125,1126,1127,1128,1129,1130,1131,1132,1133,1134,1135,1136,1137,1138,1139,1140,1141,1142,1143,1144,1145,1146,1147,1148,1149,1150,1151,1152,1153,1154,1155,1156,1157,1158,1159,1160,1161,1162,1163,1164,1165,1166,1167,1168,1169,1170,1171,1172,1173,1174,1175,1176,1177,1178,1179,1180,1181,1182,1183,1184,1185,1186,1187,1188,1189,1190,1191,1192,1193,1194,1195,1196,1197,1198,1199,1200,1201,1202,1203,1204,1205,1206,1207,1208,1209,1210,1211,1212,1213,1214,1215,1216,1217,1218,1219,1220,1221,1222,1223,1224,1225,1226,1227,1228,1229,1230,1231,1232,1233,1234,1235,1236,1237,1238,1239,1240,1241,1242,1243,1244,1245,1246,1247,1248,1249,1250,1251,1252,1253,1254,1255,1256,1257,1258,1259,1260,1261,1262,1263,1264,1265,1266,1267,1268,1269,1270,1271,1272,1273,1274,1275,1276,1277,1278,1279,1280,1281,1282,1283,1284,1285,1286,1287,1288,1289,1290,1291,1292,1293,1294,1295,1296,1297,1298,1299,1300,1301,1302,1303,1304,1305,1306,1307,1308,1309,1310,1311,1312,1313,1314,1315,1316,1317,1318,1319,1320,1321,1322,1323,1324,1325,1326,1327,1328,1329,1330,1331,1332,1333,1334,1335,1336,1337,1338,1339,1340,1341,1342,1343,1344,1345,1346,1347,1348,1349,1350,1351,1352,1353,1354,1355,1356,1357,1358,1359,1360,1361,1362,1363,1364,1365,1366,1367,1368,1369,1370,1371,1372,1373,1374,1375,1376,1377,1378,1379,1380,1381,1382,1383,1384,1385,1386,1387,1388,1389,1390,1391,1392,1393,1394,1395,1396,1397,1398,1399,1400,1401,1402,1403,1404,1405,1406,1407,1408,1409,1410,1411,1412,1413,1414,1415,1416,1417,1418,1419,1420,1421,1422,1423,1424,1425,1426,1427,1428,1429,1430,1431,1432,1433,1434,1435,1436,1437,1438,1439,1440,1441,1442,1443,1444,1445,1446,1447,1448,1449,1450,1451,1452,1453,1454,1455,1456,1457,1458,1459,1460,1461,1462,1463,1464,1465,1466,1467,1468,1469,1470,1471,1472,1473,1474,1475,1476,1477,1478,1479,1480,1481,1482,1483,1484,1485,1486,1487,1488,1489,1490,1491,1492,1493,1494,1495,1496,1497,1498,1499,1500,1501,1502,1503,1504,1505,1506,1507,1508,1509,1510,1511,1512,1513,1514,1515,1516,1517,1518,1519,1520,1521,1522,1523,1524,1525,1526,1527,1528,1529,1530,1531,1532,1533,1534,1535,1536,1537,1538,1539,1540,1541,1542,1543,1544,1545,1546,1547,1548,1549,1550,1551,1552,1553,1554,1555,1556,1557,1558,1559,1560,1561,1562,1563,1564,1565,1566,1567,1568,1569,1570,1571,1572,1573,1574,1575,1576,1577,1578,1579,1580,1581,1582,1583,1584,1585,1586,1587,1588,1589,1590,1591,1592,1593,1594,1595,1596,1597,1598,1599,1600,1601,1602,1603,1604,1605,1606,1607,1608,1609,1610,1611,1612,1613,1614,1615,1616,1617,1618,1619,1620,1621,1622,1623,1624,1625,1626,1627,1628,1629,1630,1631,1632,1633,1634,1635,1636,1637,1638,1639,1640,1641,1642,1643,1644,1645,1646,1647,1648,1649,1650,1651,1652,1653,1654,1655,1656,1657,1658,1659,1660,1661,1662,1663,1664,1665,1666,1667,1668,1669,1670,1671,1672,1673,1674,1675,1676,1677,1678,1679,1680,1681,1682,1683,1684,1685,1686,1687,1688,1689,1690,1691,1692,1693,1694,1695,1696,1697,1698,1699,1700,1701,1702,1703,1704,1705,1706,1707,1708,1709,1710,1711,1712,1713,1714,1715,1716,1717,1718,1719,1720,1721,1722,1723,1724,1725,1726,1727,1728,1729,1730,1731,1732,1733,1734,1735,1736,1737,1738,1739,1740,1741,1742,1743,1744,1745,1746,1747,1748,1749,1750,1751,1752,1753,1754,1755,1756,1757,1758,1759,1760,1761,1762,1763,1764,1765,1766,1767,1768,1769,1770,1771,1772,1773,1774,1775,1776,1777,1778,1779,1780,1781,1782,1783,1784,1785,1786,1787,1788,1789,1790,1791,1792,1793,1794,1795,1796,1797,1798,1799,1800,1801,1802,1803,1804,1805,1806,1807,1808,1809,1810,1811,1812,1813,1814,1815,1816,1817,1818,1819,1820,1821,1822,1823,1824,1825,1826,1827,1828,1829,1830,1831,1832,1833,1834,1835,1836,1837,1838,1839,1840,1841,1842,1843,1844,1845,1846,1847,1848,1849,1850,1851,1852,1853,1854,1855,1856,1857,1858,1859,1860,1861,1862,1863,1864,1865,1866,1867,1868,1869,1870,1871,1872,1873,1874,1875,1876,1877,1878,1879,1880,1881,1882,1883,1884,1885,1886,1887,1888,1889,1890,1891,1892,1893,1894,1895,1896,1897,1898,1899,1900,1901,1902,1903,1904,1905,1906,1907,1908,1909,1910,1911,1912,1913,1914,1915,1916,1917,1918,1919,1920,1921,1922,1923,1924,1925,1926,1927,1928,1929,1930,1931,1932,1933,1934,1935,1936,1937,1938,1939,1940,1941,1942,1943,1944,1945,1946,1947,1948,1949,1950,1951,1952,1953,1954,1955,1956,1957,1958,1959,1960,1961,1962,1963,1964,1965,1966,1967,1968,1969,1970,1971,1972,1973,1974,1975,1976,1977,1978,1979,1980,1981,1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040,2041,2042,2043,2044,2045,2046,2047,2048,2049,2050,2051,2052,2053,2054,2055,2056,2057,2058,2059,2060,2061,2062,2063,2064,2065,2066,2067,2068,2069,2070,2071,2072,2073,2074,2075,2076,2077,2078,2079,2080,2081,2082,2083,2084,2085,2086,2087,2088,2089,2090,2091,2092,2093,2094,2095,2096,2097,2098,2099,2100,2101,2102,2103,2104,2105,2106,2107,2108,2109,2110,2111,2112,2113,2114,2115,2116,2117,2118,2119,2120,2121,2122,2123,2124,2125,2126,2127,2128,2129,2130,2131,2132,2133,2134,2135,2136,2137,2138,2139,2140,2141,2142,2143,2144,2145,2146,2147,2148,2149,2150,2151,2152,2153,2154,2155,2156,2157,2158,2159,2160,2161,2162,2163,2164,2165,2166,2167,2168,2169,2170,2171,2172,2173,2174,2175,2176,2177,2178,2179,2180,2181,2182,2183,2184,2185,2186,2187,2188,2189,2190,2191,2192,2193,2194,2195,2196,2197,2198,2199,2200,2201,2202,2203,2204,2205,2206,2207,2208,2209,2210,2211,2212,2213,2214,2215,2216,2217,2218,2219,2220,2221,2222,2223,2224,2225,2226,2227,2228,2229,2230,2231,2232,2233,2234,2235,2236,2237,2238,2239,2240,2241,2242,2243,2244,2245,2246,2247,2248,2249,2250,2251,2252,2253,2254,2255,2256,2257,2258,2259,2260,2261,2262,2263,2264,2265,2266,2267,2268,2269,2270,2271,2272,2273,2274,2275,2276,2277,2278,2279,2280,2281,2282,2283,2284,2285,2286,2287,2288,2289,2290,2291,2292,2293,2294,2295,2296,2297,2298,2299,2300,2301,2302,2303,2304,2305,2306,2307,2308,2309,2310,2311,2312,2313,2314,2315,2316,2317,2318,2319,2320,2321,2322,2323,2324,2325,2326,2327,2328,2329,2330,2331,2332,2333,2334,2335,2336,2337,2338,2339,2340,2341,2342,2343,2344,2345,2346,2347,2348,2349,2350,2351,2352,2353,2354,2355,2356,2357,2358,2359,2360,2361,2362,2363,2364,2365,2366,2367,2368,2369,2370,2371,2372,2373,2374,2375,2376,2377,2378,2379,2380,2381,2382,2383,2384,2385,2386,2387,2388,2389,2390,2391,2392,2393,2394,2395,2396,2397,2398,2399,2400,2401,2402,2403,2404,2405,2406,2407,2408,2409,2410,2411,2412,2413,2414,2415,2416,2417,2418,2419,2420,2421,2422,2423,2424,2425,2426,2427,2428,2429,2430,2431,2432,2433,2434,2435,2436,2437,2438,2439,2440,2441,2442,2443,2444,2445,2446,2447,2448,2449,2450,2451,2452,2453,2454,2455,2456,2457,2458,2459,2460,2461,2462,2463,2464,2465,2466,2467,2468,2469,2470,2471,2472,2473,2474,2475,2476,2477,2478,2479,2480,2481,2482,2483,2484,2485,2486,2487,2488,2489,2490,2491,2492,2493,2494,2495,2496,2497,2498,2499,2500,2501,2502,2503,2504,2505,2506,2507,2508,2509,2510,2511,2512,2513,2514,2515,2516,2517,2518,2519,2520,2521,2522,2523,2524,2525,2526,2527,2528,2529,2530,2531,2532,2533,2534,2535,2536,2537,2538,2539,2540,2541,2542,2543,2544,2545,2546,2547,2548,2549,2550,2551,2552,2553,2554,2555,2556,2557,2558,2559,2560,2561,2562,2563,2564,2565,2566,2567,2568,2569,2570,2571,2572,2573,2574,2575,2576,2577,2578,2579,2580,2581,2582,2583,2584,2585,2586,2587,2588,2589,2590,2591,2592,2593,2594,2595,2596,2597,2598,2599,2600,2601,2602,2603,2604,2605,2606,2607,2608,2609,2610,2611,2612,2613,2614,2615,2616,2617,2618,2619,2620,2621,2622,2623,2624,2625,2626,2627,2628,2629,2630,2631,2632,2633,2634,2635,2636,2637,2638,2639,2640,2641,2642,2643,2644,2645,2646,2647,2648,2649,2650,2651,2652,2653,2654,2655,2656,2657,2658,2659,2660,2661,2662,2663,2664,2665,2666,2667,2668,2669,2670,2671,2672,2673,2674,2675,2676,2677,2678,2679,2680,2681,2682,2683,2684,2685,2686,2687,2688,2689,2690,2691,2692,2693,2694,2695,2696,2697,2698,2699,2700,2701,2702,2703,2704,2705,2706,2707,2708,2709,2710,2711,2712,2713,2714,2715,2716,2717,2718,2719,2720,2721,2722,2723,2724,2725,2726,2727,2728,2729,2730,2731,2732,2733,2734,2735,2736,2737,2738,2739,2740,2741,2742,2743,2744,2745,2746,2747,2748,2749,2750,2751,2752,2753,2754,2755,2756,2757,2758,2759,2760,2761,2762,2763,2764,2765,2766,2767,2768,2769,2770,2771,2772,2773,2774,2775,2776,2777,2778,2779,2780,2781,2782,2783,2784,2785,2786,2787,2788,2789,2790,2791,2792,2793,2794,2795,2796,2797,2798,2799,2800,2801,2802,2803,2804,2805,2806,2807,2808,2809,2810,2811,2812,2813,2814,2815,2816,2817,2818,2819,2820,2821,2822,2823,2824,2825,2826,2827,2828,2829,2830,2831,2832,2833,2834,2835,2836,2837,2838,2839,2840,2841,2842,2843,2844,2845,2846,2847,2848,2849,2850,2851,2852,2853,2854,2855,2856,2857,2858,2859,2860,2861,2862,2863,2864,2865,2866,2867,2868,2869,2870,2871,2872,2873,2874,2875,2876,2877,2878,2879,2880,2881,2882,2883,2884,2885,2886,2887,2888,2889,2890,2891,2892,2893,2894,2895,2896,2897,2898,2899,2900,2901,2902,2903,2904,2905,2906,2907,2908,2909,2910,2911,2912,2913,2914,2915,2916,2917,2918,2919,2920,2921,2922,2923,2924,2925,2926,2927,2928,2929,2930,2931,2932,2933,2934,2935,2936,2937,2938,2939,2940,2941,2942,2943,2944,2945,2946,2947,2948,2949,2950,2951,2952,2953,2954,2955,2956,2957,2958,2959,2960,2961,2962,2963,2964,2965,2966,2967,2968,2969,2970,2971,2972,2973,2974,2975,2976,2977,2978,2979,2980,2981,2982,2983,2984,2985,2986,2987,2988,2989,2990,2991,2992,2993,2994,2995,2996,2997,2998,2999,3000,3001,3002,3003,3004,3005,3006,3007,3008,3009,3010,3011,3012,3013,3014,3015,3016,3017,3018,3019,3020,3021,3022,3023,3024,3025,3026,3027,3028,3029,3030,3031,3032,3033,3034,3035,3036,3037,3038,3039,3040,3041,3042,3043,3044,3045,3046,3047,3048,3049,3050,3051,3052,3053,3054,3055,3056,3057,3058,3059,3060,3061,3062,3063,3064,3065,3066,3067,3068,3069,3070,3071,3072,3073,3074,3075,3076,3077,3078,3079,3080,3081,3082,3083,3084,3085,3086,3087,3088,3089,3090,3091,3092,3093,3094,3095,3096,3097,3098,3099,3100,3101,3102,3103,3104,3105,3106,3107,3108,3109,3110,3111,3112,3113,3114,3115,3116,3117,3118,3119,3120,3121,3122,3123,3124,3125,3126,3127,3128,3129,3130,3131,3132,3133,3134,3135,3136,3137,3138,3139,3140,3141,3142,3143,3144,3145,3146,3147,3148,3149,3150,3151,3152,3153,3154,3155,3156,3157,3158,3159,3160,3161,3162,3163,3164,3165,3166,3167,3168,3169,3170,3171,3172,3173,3174,3175,3176,3177,3178,3179,3180,3181,3182,3183,3184,3185,3186,3187,3188,3189,3190,3191,3192,3193,3194,3195,3196,3197,3198,3199,3200,3201,3202,3203,3204,3205,3206,3207,3208,3209,3210,3211,3212,3213,3214,3215,3216,3217,3218,3219,3220,3221,3222,3223,3224,3225,3226,3227,3228,3229,3230,3231,3232,3233,3234,3235,3236,3237,3238,3239,3240,3241,3242,3243,3244,3245,3246,3247,3248,3249,3250,3251,3252,3253,3254,3255,3256,3257,3258,3259,3260,3261,3262,3263,3264,3265,3266,3267,3268,3269,3270,3271,3272,3273,3274,3275,3276,3277,3278,3279,3280,3281,3282,3283,3284,3285,3286,3287,3288,3289,3290,3291,3292,3293,3294,3295,3296,3297,3298,3299,3300,3301,3302,3303,3304,3305,3306,3307,3308,3309,3310,3311,3312,3313,3314,3315,3316,3317,3318,3319,3320,3321,3322,3323,3324,3325,3326,3327,3328,3329,3330,3331,3332,3333,3334,3335,3336,3337,3338,3339,3340,3341,3342,3343,3344,3345,3346,3347,3348,3349,3350,3351,3352,3353,3354,3355,3356,3357,3358,3359,3360,3361,3362,3363,3364,3365,3366,3367,3368,3369,3370,3371,3372,3373,3374,3375,3376,3377,3378,3379,3380,3381,3382,3383,3384,3385,3386,3387,3388,3389,3390,3391,3392,3393,3394,3395,3396,3397,3398,3399,3400,3401,3402,3403,3404,3405,3406,3407,3408,3409,3410,3411,3412,3413,3414,3415,3416,3417,3418,3419,3420,3421,3422,3423,3424,3425,3426,3427,3428,3429,3430,3431,3432,3433,3434,3435,3436,3437,3438,3439,3440,3441,3442,3443,3444,3445,3446,3447,3448,3449,3450,3451,3452,3453,3454,3455,3456,3457,3458,3459,3460,3461,3462,3463,3464,3465,3466,3467,3468,3469,3470,3471,3472,3473,3474,3475,3476,3477,3478,3479,3480,3481,3482,3483,3484,3485,3486,3487,3488,3489,3490,3491,3492,3493,3494,3495,3496,3497,3498,3499,3500,3501,3502,3503,3504,3505,3506,3507,3508,3509,3510,3511,3512,3513,3514,3515,3516,3517,3518,3519,3520,3521,3522,3523,3524,3525,3526,3527,3528,3529,3530,3531,3532,3533,3534,3535,3536,3537,3538,3539,3540,3541,3542,3543,3544,3545,3546,3547,3548,3549,3550,3551,3552,3553,3554,3555,3556,3557,3558,3559,3560,3561,3562,3563,3564,3565,3566,3567,3568,3569,3570,3571,3572,3573,3574,3575,3576,3577,3578,3579,3580,3581,3582,3583,3584,3585,3586,3587,3588,3589,3590,3591,3592,3593,3594,3595,3596,3597,3598,3599,3600,3601,3602,3603,3604,3605,3606,3607,3608,3609,3610,3611,3612,3613,3614,3615,3616,3617,3618,3619,3620,3621,3622,3623,3624,3625,3626,3627,3628,3629,3630,3631,3632,3633,3634,3635,3636,3637,3638,3639,3640,3641,3642,3643,3644,3645,3646,3647,3648,3649,3650,3651,3652,3653,3654,3655,3656,3657,3658,3659,3660,3661,3662,3663,3664,3665,3666,3667,3668,3669,3670,3671,3672,3673,3674,3675,3676,3677,3678,3679,3680,3681,3682,3683,3684,3685,3686,3687,3688,3689,3690,3691,3692,3693,3694,3695,3696,3697,3698,3699,3700,3701,3702,3703,3704,3705,3706,3707,3708,3709,3710,3711,3712,3713,3714,3715,3716,3717,3718,3719,3720,3721,3722,3723,3724,3725,3726,3727,3728,3729,3730,3731,3732,3733,3734,3735,3736,3737,3738,3739,3740,3741,3742,3743,3744,3745,3746,3747,3748,3749,3750,3751,3752,3753,3754,3755,3756,3757,3758,3759,3760,3761,3762,3763,3764,3765,3766,3767,3768,3769,3770,3771,3772,3773,3774,3775,3776,3777,3778,3779,3780,3781,3782,3783,3784,3785,3786,3787,3788,3789,3790,3791,3792,3793,3794,3795,3796,3797,3798,3799,3800,3801,3802,3803,3804,3805,3806,3807,3808,3809,3810,3811,3812,3813,3814,3815,3816,3817,3818,3819,3820,3821,3822,3823,3824,3825,3826,3827,3828,3829,3830,3831,3832,3833,3834,3835,3836,3837,3838,3839,3840,3841,3842,3843,3844,3845,3846,3847,3848,3849,3850,3851,3852,3853,3854,3855,3856,3857,3858,3859,3860,3861,3862,3863,3864,3865,3866,3867,3868,3869,3870,3871,3872,3873,3874,3875,3876,3877,3878,3879,3880,3881,3882,3883,3884,3885,3886,3887,3888,3889,3890,3891,3892,3893,3894,3895,3896,3897,3898,3899,3900,3901,3902,3903,3904,3905,3906,3907,3908,3909,3910,3911,3912,3913,3914,3915,3916,3917,3918,3919,3920,3921,3922,3923,3924,3925,3926,3927,3928,3929,3930,3931,3932,3933,3934,3935,3936,3937,3938,3939,3940,3941,3942,3943,3944,3945,3946,3947,3948,3949,3950,3951,3952,3953,3954,3955,3956,3957,3958,3959,3960,3961,3962,3963,3964,3965,3966,3967,3968,3969,3970,3971,3972,3973,3974,3975,3976,3977,3978,3979,3980,3981,3982,3983,3984,3985,3986,3987,3988,3989,3990,3991,3992,3993,3994,3995,3996,3997,3998,3999,4000,4001,4002,4003,4004,4005,4006,4007,4008,4009,4010,4011,4012,4013,4014,4015,4016,4017,4018,4019,4020,4021,4022,4023,4024,4025,4026,4027,4028,4029,4030,4031,4032,4033,4034,4035,4036,4037,4038,4039,4040,4041,4042,4043,4044,4045,4046,4047,4048,4049,4050,4051,4052,4053,4054,4055,4056,4057,4058,4059,4060,4061,4062,4063,4064,4065,4066,4067,4068,4069,4070,4071,4072,4073,4074,4075,4076,4077,4078,4079,4080,4081,4082,4083,4084,4085,4086,4087,4088,4089,4090,4091,4092,4093,4094,4095,4096,4097,4098,4099,4100,4101,4102,4103,4104,4105,4106,4107,4108,4109,4110,4111,4112,4113,4114,4115,4116,4117,4118,4119,4120,4121,4122,4123,4124,4125,4126,4127,4128,4129,4130,4131,4132,4133,4134,4135,4136,4137,4138,4139,4140,4141,4142,4143,4144,4145,4146,4147,4148,4149,4150,4151,4152,4153,4154,4155,4156,4157,4158,4159,4160,4161,4162,4163,4164,4165,4166,4167,4168,4169,4170,4171,4172,4173,4174,4175,4176,4177,4178,4179,4180,4181,4182,4183,4184,4185,4186,4187,4188,4189,4190,4191,4192,4193,4194,4195,4196,4197,4198,4199,4200,4201,4202,4203,4204,4205,4206,4207,4208,4209,4210,4211,4212,4213,4214,4215,4216,4217,4218,4219,4220,4221,4222,4223,4224,4225,4226,4227,4228,4229,4230,4231,4232,4233,4234,4235,4236,4237,4238,4239,4240,4241,4242,4243,4244,4245,4246,4247,4248,4249,4250,4251,4252,4253,4254,4255,4256,4257,4258,4259,4260,4261,4262,4263,4264,4265,4266,4267,4268,4269,4270,4271,4272,4273,4274,4275,4276,4277,4278,4279,4280,4281,4282,4283,4284,4285,4286,4287,4288,4289,4290,4291,4292,4293,4294,4295,4296,4297,4298,4299,4300,4301,4302,4303,4304,4305,4306,4307,4308,4309,4310,4311,4312,4313,4314,4315,4316,4317,4318,4319,4320,4321,4322,4323,4324,4325,4326,4327,4328,4329,4330,4331,4332,4333,4334,4335,4336,4337,4338,4339,4340,4341,4342,4343,4344,4345,4346,4347,4348,4349,4350,4351,4352,4353,4354,4355,4356,4357,4358,4359,4360,4361,4362,4363,4364,4365,4366,4367,4368,4369,4370,4371,4372,4373,4374,4375,4376,4377,4378,4379,4380,4381,4382,4383,4384,4385,4386,4387,4388,4389,4390,4391,4392,4393,4394,4395,4396,4397,4398,4399,4400,4401,4402,4403,4404,4405,4406,4407,4408,4409,4410,4411,4412,4413,4414,4415,4416,4417,4418,4419,4420,4421,4422,4423,4424,4425,4426,4427,4428,4429,4430,4431,4432,4433,4434,4435,4436,4437,4438,4439,4440,4441,4442,4443,4444,4445,4446,4447,4448,4449,4450,4451,4452,4453,4454,4455,4456,4457,4458,4459,4460,4461,4462,4463,4464,4465,4466,4467,4468,4469,4470,4471,4472,4473,4474,4475,4476,4477,4478,4479,4480,4481,4482,4483,4484,4485,4486,4487,4488,4489,4490,4491,4492,4493,4494,4495,4496,4497,4498,4499,4500,4501,4502,4503,4504,4505,4506,4507,4508,4509,4510,4511,4512,4513,4514,4515,4516,4517,4518,4519,4520,4521,4522,4523,4524,4525,4526,4527,4528,4529,4530,4531,4532,4533,4534,4535,4536,4537,4538,4539,4540,4541,4542,4543,4544,4545,4546,4547,4548,4549,4550,4551,4552,4553,4554,4555,4556,4557,4558,4559,4560,4561,4562,4563,4564,4565,4566,4567,4568,4569,4570,4571,4572,4573,4574,4575,4576,4577,4578,4579,4580,4581,4582,4583,4584,4585,4586,4587,4588,4589,4590,4591,4592,4593,4594,4595,4596,4597,4598,4599,4600,4601,4602,4603,4604,4605,4606,4607,4608,4609,4610,4611,4612,4613,4614,4615,4616,4617,4618,4619,4620,4621,4622,4623,4624,4625,4626,4627,4628,4629,4630,4631,4632,4633,4634,4635,4636,4637,4638,4639,4640,4641,4642,4643,4644,4645,4646,4647,4648,4649,4650,4651,4652,4653,4654,4655,4656,4657,4658,4659,4660,4661,4662,4663,4664,4665,4666,4667,4668,4669,4670,4671,4672,4673,4674,4675,4676,4677,4678,4679,4680,4681,4682,4683,4684,4685,4686,4687,4688,4689,4690,4691,4692,4693,4694,4695,4696,4697,4698,4699,4700,4701,4702,4703,4704,4705,4706,4707,4708,4709,4710,4711,4712,4713,4714,4715,4716,4717,4718,4719,4720,4721,4722,4723,4724,4725,4726,4727,4728,4729,4730,4731,4732,4733,4734,4735,4736,4737,4738,4739,4740,4741,4742,4743,4744,4745,4746,4747,4748,4749,4750,4751,4752,4753,4754,4755,4756,4757,4758,4759,4760,4761,4762,4763,4764,4765,4766,4767,4768,4769,4770,4771,4772,4773,4774,4775,4776,4777,4778,4779,4780,4781,4782,4783,4784,4785,4786,4787,4788,4789,4790,4791,4792,4793,4794,4795,4796,4797,4798,4799,4800,4801,4802,4803,4804,4805,4806,4807,4808,4809,4810,4811,4812,4813,4814,4815,4816,4817,4818,4819,4820,4821,4822,4823,4824,4825,4826,4827,4828,4829,4830,4831,4832,4833,4834,4835,4836,4837,4838,4839,4840,4841,4842,4843,4844,4845,4846,4847,4848,4849,4850,4851,4852,4853,4854,4855,4856,4857,4858,4859,4860,4861,4862,4863,4864,4865,4866,4867,4868,4869,4870,4871,4872,4873,4874,4875,4876,4877,4878,4879,4880,4881,4882,4883,4884,4885,4886,4887,4888,4889,4890,4891,4892,4893,4894,4895,4896,4897,4898,4899,4900,4901,4902,4903,4904,4905,4906,4907,4908,4909,4910,4911,4912,4913,4914,4915,4916,4917,4918,4919,4920,4921,4922,4923,4924,4925,4926,4927,4928,4929,4930,4931,4932,4933,4934,4935,4936,4937,4938,4939,4940,4941,4942,4943,4944,4945,4946,4947,4948,4949,4950,4951,4952,4953,4954,4955,4956,4957,4958,4959,4960,4961,4962,4963,4964,4965,4966,4967,4968,4969,4970,4971,4972,4973,4974,4975,4976,4977,4978,4979,4980,4981,4982,4983,4984,4985,4986,4987,4988,4989,4990,4991,4992,4993,4994,4995,4996,4997,4998,4999],"y":{"__ndarray__":"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","dtype":"float64","shape":[5000]}},"selected":{"id":"1402","type":"Selection"},"selection_policy":{"id":"1403","type":"UnionRenderers"}},"id":"1364","type":"ColumnDataSource"},{"attributes":{"formatter":{"id":"1400","type":"BasicTickFormatter"},"ticker":{"id":"1342","type":"BasicTicker"}},"id":"1341","type":"LinearAxis"},{"attributes":{"line_alpha":0.1,"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1366","type":"Line"},{"attributes":{},"id":"1337","type":"LinearScale"},{"attributes":{},"id":"1352","type":"WheelZoomTool"},{"attributes":{},"id":"1403","type":"UnionRenderers"},{"attributes":{},"id":"1400","type":"BasicTickFormatter"},{"attributes":{"line_color":"#1f77b4","x":{"field":"x"},"y":{"field":"y"}},"id":"1365","type":"Line"},{"attributes":{"callback":null},"id":"1333","type":"DataRange1d"},{"attributes":{"active_drag":"auto","active_inspect":"auto","active_multi":null,"active_scroll":"auto","active_tap":"auto","tools":[{"id":"1351","type":"PanTool"},{"id":"1352","type":"WheelZoomTool"},{"id":"1353","type":"BoxZoomTool"},{"id":"1354","type":"SaveTool"},{"id":"1355","type":"ResetTool"},{"id":"1356","type":"HelpTool"}]},"id":"1357","type":"Toolbar"},{"attributes":{},"id":"1356","type":"HelpTool"},{"attributes":{},"id":"1342","type":"BasicTicker"},{"attributes":{"source":{"id":"1364","type":"ColumnDataSource"}},"id":"1368","type":"CDSView"},{"attributes":{"callback":null},"id":"1335","type":"DataRange1d"},{"attributes":{},"id":"1355","type":"ResetTool"},{"attributes":{"formatter":{"id":"1398","type":"BasicTickFormatter"},"ticker":{"id":"1347","type":"BasicTicker"}},"id":"1346","type":"LinearAxis"},{"attributes":{},"id":"1354","type":"SaveTool"},{"attributes":{},"id":"1398","type":"BasicTickFormatter"},{"attributes":{"overlay":{"id":"1404","type":"BoxAnnotation"}},"id":"1353","type":"BoxZoomTool"},{"attributes":{},"id":"1347","type":"BasicTicker"},{"attributes":{"data_source":{"id":"1364","type":"ColumnDataSource"},"glyph":{"id":"1365","type":"Line"},"hover_glyph":null,"muted_glyph":null,"nonselection_glyph":{"id":"1366","type":"Line"},"selection_glyph":null,"view":{"id":"1368","type":"CDSView"}},"id":"1367","type":"GlyphRenderer"},{"attributes":{"text":""},"id":"1396","type":"Title"},{"attributes":{"bottom_units":"screen","fill_alpha":{"value":0.5},"fill_color":{"value":"lightgrey"},"left_units":"screen","level":"overlay","line_alpha":{"value":1.0},"line_color":{"value":"black"},"line_dash":[4,4],"line_width":{"value":2},"render_mode":"css","right_units":"screen","top_units":"screen"},"id":"1404","type":"BoxAnnotation"},{"attributes":{},"id":"1351","type":"PanTool"},{"attributes":{},"id":"1402","type":"Selection"}],"root_ids":["1332"]},"title":"Bokeh Application","version":"1.2.0"}};
      var render_items = [{"docid":"8fbcb4e1-638c-41c4-95db-d1f65746489f","roots":{"1332":"1efebb78-8be9-47b7-810f-843908f05934"}}];
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

    [2485, 2869, 1185, 1221, 2861]
    


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

    [[-0.0133322  -0.30774457 -0.30506963 -0.28086787 -0.23951257]
     [-0.01961877 -0.31495983 -0.31418135 -0.29625774 -0.2424731 ]
     [-0.0257372  -0.31937445 -0.32579912 -0.30445029 -0.24859369]
     [-0.03247205 -0.32659898 -0.32960947 -0.30918169 -0.25417405]
     [-0.03959258 -0.33316515 -0.33666557 -0.31670526 -0.2598411 ]
     [-0.04702045 -0.33953802 -0.34470356 -0.32007355 -0.26532669]
     [-0.05448386 -0.34625389 -0.34767785 -0.32902597 -0.27020852]
     [-0.06246074 -0.35190601 -0.34624293 -0.33009285 -0.2749048 ]
     [-0.0682373  -0.35910034 -0.35900879 -0.33273315 -0.28216553]]
    [[ 7.66935085e-05  6.25975990e-05  1.96426753e-05 -5.21542998e-05
      -2.85197624e-05]
     [ 6.25975990e-05  1.78276786e-04  2.44412026e-05  4.90595701e-06
      -3.47563401e-05]
     [ 1.96426753e-05  2.44412026e-05  1.21007324e-04 -1.24431882e-04
      -2.52440984e-05]
     [-5.21542998e-05  4.90595701e-06 -1.24431882e-04  1.22033372e-03
       1.06913299e-04]
     [-2.85197624e-05 -3.47563401e-05 -2.52440984e-05  1.06913299e-04
       6.72095378e-05]]
    


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

    c2 5b 62 4b 7f
    64 8e c5 b4 7f
    b4 c5 36 a6 7f
    b1 ed a6 36 7f
    2b ff 1d bc b1
    1b aa a6 36 2b
    b7 ed 39 d3 2b
    fc ed ae d3 2b
    a1 8f b7 ed 2b
    2e 21 b7 ed 2b
    46 a6 b7 ed 2b
    a1 a6 ed b7 2b
    1b ed a1 b7 2b
    ed 1b a1 b7 2b
    ed a1 1b b7 2b
    b7 19 1c a1 2b
    b7 a1 19 1c 2b
    c4 19 1c a1 2b
    8f 19 43 a1 2b
    8f 43 19 a1 2b
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

