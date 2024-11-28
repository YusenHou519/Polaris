What is Polaris?
================

A Universal Tool for Chromatin Loop Annotation
----------------------------------------------

.. raw:: html

   <div style="float: right; margin-right: 10px;">
     <img src="_static/logo02.png" alt="Polaris Logo" width="130" height="130">
   </div>

The name **Polaris** reflects its role as the "North Star" in the analysis of chromatin loops. In this analogy, chromatin loops represent Polaris, the central focus, while the surrounding structural patterns—such as architectural stripes, TAD boundaries, and co-occurring loops—act as guiding constellations like the Big Dipper and Cassiopeia. Polaris identifies chromatin loops by capturing and interpreting these structural cues, akin to navigating the night sky by observing constellations.

- Polaris detects chromatin loops by combining axial attention and a U-Net backbone to capture **local and global structural features**, including:
    - Co-occurring loops.
    - Architectural stripes.
    - TAD boundaries.

- Polaris uses knowledge distillation to address limited training labels, ensuring robust performance **without extensive fine-tuning**.

- Polaris **outperforms** existing tools in accuracy while being **computationally efficient** for large-scale analyses.

- Polaris works seamlessly with both **single-cell and bulk data** across various assays, sequencing depths, and resolutions.

--------------------------------------------------------------------------------

.. figure:: _static/logo03.png
   :alt: Polaris Logo
   :width: 700px
   :height: 175px
   :align: center


   Pre-training, fine-tuning, and application on bulk contact maps of Polaris.

-------------------------------------------------------------------------------------

.. figure:: _static/logo04.png
   :alt: Polaris Logo
   :width: 509px
   :height: 90px
   :align: center


   Application on single-cell contact maps of Polaris.

--------------------------------------------------------------------------------------

Polaris Usage
-------------

Input files
~~~~~~~~~~~

Polaris requires a `.mcool` file as input. You can obtain `.mcool` files in the following ways:

1. Download from the 4DN Database

- Visit the `4DN Data Portal <https://data.4dnucleome.org/>`_.
- Search for and download `.mcool` files suitable for your study.

2. Convert Files Using `cooler`

If you have data in formats such as `.pairs` or `.cool`, you can convert them to `.mcool` format using the Python library `cooler <https://cooler.readthedocs.io/en/latest/index.html#>`_. Follow these steps:

- Install cooler

   Ensure you have installed `cooler` using the following command:

   .. code-block:: bash

      pip install cooler

- Convert `.pairs` to `.cool`

   If you are starting with a `.pairs` file (e.g., normalized contact data with columns for chrom1, pos1, chrom2, pos2), use this command to create a `.cool` file:

   .. code-block:: bash

      cooler cload pairs --assembly <genome_version> -c1 chrom1 -p1 pos1 -c2 chrom2 -p2 pos2 <pairs_file> <resolution>.cool

   Replace ``<genome_version>`` with the appropriate genome assembly (e.g., `hg38`) and ``<resolution>`` with the desired bin size in base pairs.

- Generate a Multiresolution `.mcool` File

   To convert a single-resolution `.cool` file into a multiresolution `.mcool` file, use the following command:

   .. code-block:: bash

      cooler zoomify <input.cool>

The resulting `.mcool` file can be directly used as input for Polaris.

polaris loop
~~~~~~~~~~~~~~~~~

Polaris provides two methods to generate loop annotations for input `.mcool` file. Both methods ultimately yield consistent loop results. Below is a detailed explanation of each method:

Method 1: polaris loop pred
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is the simplest approach, allowing you to directly predict loops in a single step:

.. code-block:: bash

   polaris loop pred -i [input.mcool] -o [save_path.bedpe] [options]

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Option
     - Description
     - Default Value
   * - ``--batchsize INTEGER``
     - Batch size for processing data. Adjust this based on available memory.
     - ``128``
   * - ``--cpu BOOLEAN``
     - Use CPU for computation. Set to ``True`` to force CPU usage.
     - ``False``
   * - ``--gpu TEXT``
     - Comma-separated GPU indices to use. If not specified, GPUs will be auto-selected.
     - ``auto select``
   * - ``--chrom TEXT``
     - Comma-separated list of chromosomes for loop calling.
     - None
   * - ``-t INTEGER``
     - Number of CPU threads to use. Adjust for optimal performance on your system.
     - ``16``
   * - ``--max_distance INTEGER``
     - Maximum genomic distance (in base pairs) between contact pairs to consider.
     - None
   * - ``--resol INTEGER``
     - Resolution of the Hi-C contact map (in base pairs).
     - None
   * - ``--modelstate TEXT``
     - Path to the trained model state file.
     - None
   * - ``--dc INTEGER``
     - Distance cutoff (in bins) for local density calculation. Larger values may account for more dispersed loops.
     - ``5``
   * - ``--minscore FLOAT``
     - Minimum loopScore threshold to consider a pixel as a loop candidate.
     - ``0.6``
   * - ``--radius INTEGER``
     - Radius for KDTree to remove outliers (in bins). Use larger values for sparser datasets.
     - ``2``
   * - ``--mindelta FLOAT``
     - Minimum distance allowed between two predicted loops.
     - ``5``
   * - ``--refine BOOLEAN``
     - Refine the predicted loops. It is recommended to always set this to ``True``.
     - ``True``
   * - ``-i, --input TEXT``
     - Path to the input Hi-C contact map file.
     - **Required**
   * - ``-o, --output TEXT``
     - Path to the ``.bedpe`` file where the predicted loops will be saved.
     - **Required**
   * - ``--help``
     - Display help information about this command and exit.
     - None

Key Parameters:

- ``chrom``: Specifies the chromosomes for loop calling, provided as a comma-separated string.
- ``batchsize``: Defines the batch size used for prediction. Adjust based on available computational resources.
- ``resol``: Resolution of the input contact map.

This command processes the input .mcool file and outputs the identified chromatin loops directly.

Method 2: polaris loop score and polaris loop pool
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This method involves two steps: generating loop scores for each pixel in the contact map and clustering these scores to call loops.

**Step 1: Generate Loop Scores**

Run the following command to calculate the loop score for each pixel in the input contact map:

.. code-block:: bash

   polaris loop score -i [input.mcool] -o [loopscore.bedpe] [options]

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Option
     - Description
     - Default Value
   * - ``--batchsize INTEGER``
     - Batch size for processing data. Adjust this based on available memory.
     - ``128``
   * - ``--cpu BOOLEAN``
     - Use CPU for computation. Set to ``True`` to force CPU usage.
     - ``False``
   * - ``--gpu TEXT``
     - Comma-separated GPU indices to use. If not specified, GPUs will be auto-selected.
     - ``auto select``
   * - ``--chrom TEXT``
     - Comma-separated list of chromosomes for loop candidate scoring.
     - None
   * - ``-t INTEGER``
     - Number of CPU threads to use. Adjust for optimal performance on your system.
     - ``16``
   * - ``--max_distance INTEGER``
     - Maximum genomic distance (in base pairs) between contact pairs to consider.
     - None
   * - ``--resol INTEGER``
     - Resolution of the Hi-C contact map (in base pairs).
     - None
   * - ``--modelstate TEXT``
     - Path to the trained model state file.
     - None
   * - ``-i, --input TEXT``
     - Path to the input Hi-C contact map file.
     - **Required**
   * - ``-o, --output TEXT``
     - Path to the ``.bedpe`` file where the loop candidates (with loop scores) will be saved.
     - **Required**
   * - ``--help``
     - Display help information about this command and exit.
     - None

**Step 2: Call Loops from Loop Candidates**

Use the generated loop score file to identify loops by clustering:

.. code-block:: bash

   polaris loop pool -i [loopscore.bedpe] -o [loops.bedpe] [options]

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Option
     - Description
     - Default Value
   * - ``--dc INTEGER``
     - Distance cutoff (in bins) for local density calculation. Larger values may account for more dispersed loops.
     - ``5``
   * - ``--minscore FLOAT``
     - Minimum loopScore threshold to consider a loop candidate as a valid loop.
     - ``0.6``
   * - ``--resol INTEGER``
     - Resolution of the Hi-C contact map (in base pairs).
     - ``5000``
   * - ``--radius INTEGER``
     - Radius for KDTree to remove outliers (in bins). Use larger values for sparser datasets.
     - ``2``
   * - ``--mindelta FLOAT``
     - Minimum distance allowed between two predicted loops.
     - ``5``
   * - ``--refine BOOLEAN``
     - Refine the predicted loops. It is recommended to always set this to ``True``.
     - ``True``
   * - ``-i, --candidates TEXT``
     - Path to the input loop candidates file (usually the output from `polaris loop score`).
     - **Required**
   * - ``-o, --output TEXT``
     - Path to the ``.bedpe`` file where the final loops will be saved.
     - **Required**
   * - ``--help``
     - Display help information about this command and exit.
     - None

Key Parameters:

- ``chrom``: Specifies the chromosomes for loop calling, provided as a comma-separated string.
- ``batchsize``: Defines the batch size used for prediction. Adjust based on available computational resources.
- ``resol``: Resolution of the input contact map.

polaris util
~~~~~~~~~~~~~~~~~

The ``polaris util`` command provides various utilities for working with Hi-C data. Below is a detailed explanation of each utility and its options.

polaris util cool2bcool
^^^^^^^^^^^^^^^^^^^^^^^^^

The `cool2bcool` utility converts a `.mcool` file to a `.bcool` file. The `.bcool` file is compatible with `.mcool` files and requires less storage space.

.. code-block:: bash

   polaris util cool2bcool [OPTIONS] COOL BCOOL

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Option
     - Description
     - Default Value
   * - ``-u INTEGER``
     - Distance upper bound in base pairs.
     - ``3000000``
   * - ``--resol TEXT``
     - Comma-separated resolutions for the output.
     - None
   * - ``--help``
     - Show help message and exit.
     - None

polaris util pileup
^^^^^^^^^^^^^^^^^^^^^^^

The `pileup` utility generates 2D pileup contact maps around given foci.

.. code-block:: bash

   polaris util pileup [OPTIONS] FOCI MCOOL

.. list-table::
   :header-rows: 1
   :widths: auto

   * - Option
     - Description
     - Default Value
   * - ``-w INTEGER``
     - Window size in bins: (2w+1)x(2w+1).
     - ``10``
   * - ``--savefig TEXT``
     - Save pileup plot to file.
     - ``FOCI_pileup.png``
   * - ``--p2ll BOOLEAN``
     - Compute p2ll.
     - ``False``
   * - ``--mindistance INTEGER``
     - Minimum distance in bins to skip, only for bedpe foci.
     - ``2w+1``
   * - ``--maxdistance INTEGER``
     - Maximum distance in bins to skip, only for bedpe foci.
     - ``1e9``
   * - ``--resol INTEGER``
     - Resolution.
     - ``5000``
   * - ``--oe BOOLEAN``
     - O/E normalized.
     - ``True``
   * - ``--help``
     - Show help message and exit.
     - None
