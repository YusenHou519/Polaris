Quickstart
==========

Installation
------------

Polaris is developed and tested on Linux machines with python3.9 and relies on several libraries including pytorch, scipy, etc.
We ​**strongly recommend** that you install Polaris in a virtual environment.

We suggest users using `conda <https://anaconda.org/>`_ to create a virtual environment for it (It should also work without using conda, i.e. with pip). You can run the command snippets below to install Polaris:

.. code-block:: bash

   git clone https://github.com/ai4nucleome/Polaris.git
   cd Polaris
   conda create -n polaris python=3.9
   conda activate polaris

-------

Install Polaris:

.. code-block:: bash

   ./setup.sh

It will automatically download Polaris model's weights from `Hugging Face <https://huggingface.co/rr-ss/Polaris>`_ and install Polaris.

You can also download model's weights file manually from `there <https://huggingface.co/rr-ss/Polaris/resolve/main/polaris/model/sft_loop.pt?download=true>`_ and put it in ``Polaris/polaris/model`` and change the file name to ``sft_loop.pt``.

The installation requires network access to download libraries. Usually, the installation will finish within 3 minutes. The installation time is longer if network access is slow and/or unstable.


Quick Usage
-----------

**See** `Jupyter Notebook CLI walkthrough <https://github.com/ai4nucleome/Polaris/blob/master/example/CLI_walkthrough.ipynb>`_ **and the** `CLI Reference <https://nucleome-polaris.readthedocs.io/en/latest/CLI_reference.html#>`_ **for more information.**

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