
Introduction to Jupyter
=======================


**In [ ]:**


Running Tutorials
-----------------

Jupyter notebooks are documents that combine Python code, rich text, and
additional code (such as Bash), organized into executable blocks.
Notebooks allow users to run blocks out of order and multiple times,
giving greater control over what's being run than a Python script. To
run these notebooks, you'll need to install the Jupyter Notebook
application (see `Installing
ChipWhisperer <https://chipwhisperer.readthedocs.io/en/latest/installing.html>`__),
which also includes file browser and text editor capabilities.

Below is an example of a Python block. First select the block by
clicking on it. A green or blue border should appear around the block,
indicating that the block is selected. Next, hit the ``>| Run`` button
on the toolbar at the top of your screen. The next block should now be
highlighted.


**In [1]:**

.. code:: ipython3

    i = 0

State is kept between Python blocks. Try running the next block multiple
times to see how the printed number changes:


**In [2]:**

.. code:: ipython3

    i += 1
    print(i)


**Out [2]:**




Notebooks also allow special blocks to be run. Two special blocks used
throughout the ChipWhisperer tutorials are ``bash`` blocks, which allow
bash commands to be run (note: if you're not using a bash-like terminal
to run Jupyter, these blocks won't work for you):


**In [3]:**

.. code:: bash

    %%bash
    echo "Test bash"
    i=0
    for j in *; do let "i=i+1"; done
    echo "there are" $i "files/folders in this directory"


**Out [3]:**



.. parsed-literal::

    Test bash
    there are 36 files/folders in this directory
    


And ``run`` blocks, which run other notebooks:


**In [4]:**

.. code:: ipython3

    %run "Helper_Scripts/Test_Run.ipynb"


**Out [4]:**



.. parsed-literal::

    Hi, I'm another Notebook!
    


Code blocks can also be edited by clicking within the code portion of
the block (the grey area). You can tell if you're in edit mode by the
presence of a green border around the block. If you're not in edit mode,
the border will be blue instead.

Completing Tutorials
--------------------

ChipWhisperer tutorials are designed to be run from start to finish,
with few changes needed. One change you will likely need to make is to
the ``Paramater`` block. These appear as the first code block in every
tutorial and are used to allow tutorials to work with multiple hardware
setups. An example ``Parameter`` block is shown below:


**In [5]:**

.. code:: ipython3

    SCOPETYPE = 'OPENADC'
    PLATFORM = 'CWLITEARM'
    CRYPTO_TARGET = 'NONE'

These are typically used as follows in the tutorial:

-  ``SCOPETYPE`` - Indicates the capture hardware to use. If you have a
   ChipWhisperer Lite or Pro, use ``'OPENADC'``. If you have a
   ChipWhisperer Nano, use ``'CWNANO'``.
-  ``PLATFORM`` - Selects the target we're attacking. As of
   ChipWhisperer v5.1.0, tutorials only support at most ``'CWLITEARM'``
   (STM32F3 target), ``'CWLITEXMEGA'`` (XMEGA target), and ``'CWNANO'``
   (Nano STM32F0 target).
-  ``CRYPTO_TARGET`` - Selects the cryptographic library to use on the
   target. If unsure, use ``'AVRCRYPTOLIB'`` for the XMEGA target and
   ``'TINYAES128C'`` otherwise. Tutorials that don't have this block or
   use ``'NONE'`` don't require a crypto library.

The parameter blocks should always be the first one run during a
tutorial.

Conclusion
----------

You should now be ready to complete some tutorials! If you'd like to
start off immediately with some side channel success, try completing
``PA_CPA_1``. Otherwise, you can see our suggested completion order in
``!!Suggested_Completion_Order.ipynb!!``, which does ``PA_CPA_1`` later.
