Quickstart
==========

Installation
------------

Polaris is developed and tested on Linux machines with python3.9 and relies on several libraries including pytorch, scipy, etc. 
We **strongly recommend** that you install Polaris in a virtual environment.

We suggest users using `conda <https://anaconda.org/>`_ to create a virtual environment for it (It should also work without using conda, i.e. with pip). You can run the command snippets below to install Polaris:

.. code-block:: bash

   git clone https://github.com/BlanchetteLab/Polaris.git
   cd Polaris
   conda create -n polaris python=3.9
   conda activate polaris

Install `PyTorch <https://pytorch.org/get-started/locally/>`_ as described on their website. It might be the following command depending on your cuda version:

.. code-block:: bash

   pip install torch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2 --index-url https://download.pytorch.org/whl/cu121

Install Polaris:

.. code-block:: bash

   pip install --use-pep517 --editable .

If fail, please try ``python setup build`` and ``python setup install`` first.

The installation requires network access to download libraries. Usually, the installation will finish within 5 minutes. The installation time is longer if network access is slow and/or unstable.

Quick Usage
-----------

**See** `Jupyter Notebook CLI walkthrough <https://github.com/compbiodsa/Polaris/blob/master/example/CLI_walkthrough.ipynb>`_ **and the** `CLI Reference <https://polairs-doc.readthedocs.io/en/latest/CLI_reference.html#>`_ **for more information.**

Polaris takes submatrices of contact map as input and outputs predicted loops.

.. code-block:: bash

   polaris loop pred -i [input mcool file] -o [output path of annotated loops]

Output format:

It contains tab separated fields as follows:

.. csv-table:: 
   :header: "Field", "Detail"
   :widths: 20, 30

   "Chr1/Chr2", "chromosome names"
   "Start1/Start2", "start genomic coordinates"
   "End1/End2", "end genomic coordinates (i.e. End1=Start1+resol)"
   "Score", "Polaris's loop score [0~1]"