CLI Reference
=============

Quick reference
---------------

.. code-block:: bash

   polaris COMMAND [ARGS]...
   
.. list-table::
    :widths: 20 80
    :align: left
    :header-rows: 1

    * - Command
      -
    * - `polaris loop`_
      - Loop annotation commands.
    * - `polaris util`_
      - Analysis and visualization utilities.

-------------------------------------------------

polaris loop 
-------------

Annotate loops from input `.mcool` file.

Choose a subcommand to predict loops directly or obtain loop score file first.

.. code-block:: bash

   polaris loop COMMAND [ARGS]...

.. rubric:: Commands

.. hlist::
  :columns: 3

  * .. object:: pred
  * .. object:: score
  * .. object:: pool


----

polaris loop pred
^^^^^^^^^^^^^^^^^^

    This is the simplest approach, allowing to directly predict loops in a single step

    .. program:: cooler cload pairs
    .. code-block:: shell

        polaris loop pred -i [input.mcool] -o [save_path.bedpe] [options]

    .. rubric:: Required Options
            
    .. option:: -i, --input: 
        
        Path to a ``.mcool`` contact map file.

    .. option:: -o, --output: 
    
        Path to the ``.bedpe`` file where the predicted loops will be saved.

    .. rubric:: Options

    .. option:: --batchsize <int> 

        Default: ``128``

        Batch size for processing data. Adjust this based on available memory.

    .. option:: --cpu <boolean> 

        Default: ``False``

        Use CPU for computation. Set to ``True`` to force CPU usage.

    .. option:: --gpu <text> 

        Default: ``None``

        Comma-separated GPU indices to use. If not specified, GPUs will be auto-selected.

    .. option:: --chrom <text>

        Default: ``None``

        Comma-separated list of chromosomes for loop calling. If not specified, all autosomes and chromosome X will be annotated.

    .. option:: -t <int> 

        Default: ``16``

        Number of CPU threads to use. Adjust for optimal performance on your system.

    .. option:: --max_distance <int>

        Default: ``3000000``

        Maximum genomic distance (in base pairs) between contact pairs to consider.

    .. option:: --resol <int>

        Default: ``5000``

        Resolution of the input contact map.

    .. option:: --dc <int> 

        Default: ``5``

        Distance cutoff (in bins) for local density calculation. Larger values may account for more dispersed loops.

    .. option:: --minscore <float> 

        Default: ``0.6``

        Minimum loopScore threshold to consider a pixel as a loop candidate. Smaller values for more loops (Minimum value: 0.5).

    .. option:: --radius <int> 

        Default: ``2``

        Radius for KDTree to remove outliers (in bins). Use larger values for sparser datasets.

    .. option:: --mindelta <float> 

        Default: ``5``

        Minimum distance allowed between two predicted loops.

    .. option:: --help

        Display help information about this command and exit.

----

polaris loop score
^^^^^^^^^^^^^^^^^^^
    Calculate the loop score for each pixel in the input contact map.

    .. code-block:: bash

        polaris loop score -i [input.mcool] -o [loopscore.bedpe] [options]

    .. rubric:: Required Options

    .. option:: -i, --input: 

        Path to the input Hi-C contact map file.

    .. option:: -o, --output: 

        Path to the ``.bedpe`` file where the loop scores will be saved.

    .. rubric:: Options

    .. option:: --batchsize <int> 

        Default: ``128``

        Batch size for processing data. Adjust this based on available memory.

    .. option:: --cpu <boolean> 

        Default: ``False``

        Use CPU for computation. Set to ``True`` to force CPU usage.

    .. option:: --gpu <text> 

        Default: ``None``

        Comma-separated GPU indices to use. If not specified, GPUs will be auto-selected.

    .. option:: --chrom <text>

        Default: ``None``

        Comma-separated list of chromosomes for loop candidate scoring. If not specified, all autosomes and chromosome X will be annotated.

    .. option:: -t <int> 

        Default: ``16``

        Number of CPU threads to use. Adjust for optimal performance on your system.

    .. option:: --max_distance <int>

        Default: ``3000000``

        Maximum genomic distance (in base pairs) between contact pairs to consider.

    .. option:: --resol <int>

        Default: ``5000``

        Resolution of the Hi-C contact map (in base pairs).

    .. option:: --help

        Display help information about this command and exit.



----

polaris loop pool
^^^^^^^^^^^^^^^^^^^
    Identify loops from loop candidates by clustering.

    .. code-block:: bash

        polaris loop pool -i [loopscore.bedpe] -o [loops.bedpe] [options]

    .. rubric:: Required Options

    .. option:: -i, --candidates: 

        Path to the input loop candidates file.

    .. option:: -o, --output: 

        Path to the ``.bedpe`` file where the final loops will be saved.

    .. rubric:: Options

    .. option:: --dc <int> 

        Default: ``5``

        Distance cutoff (in bins) for local density calculation. Larger values may account for more dispersed loops.

    .. option:: --minscore <float> 

        Default: ``0.6``

        Minimum loopScore threshold to consider a loop candidate as a valid loop.

    .. option:: --resol <int>

        Default: ``5000``

        Resolution of the Hi-C contact map (in base pairs).

    .. option:: --radius <int> 

        Default: ``2``

        Radius for KDTree to remove outliers (in bins). Use larger values for sparser datasets.

    .. option:: --mindelta <float> 

        Default: ``5``

        Minimum distance allowed between two predicted loops.

    .. option:: --refine <boolean> 

        Default: ``True``

        Refine the predicted loops. It is recommended to always set this to ``True``.

    .. option:: --help

        Display help information about this command and exit.


polaris util 
-------------

Utilities for analysis and visualization with ``.mcool`` files.

.. code-block:: bash

   polaris util COMMAND [ARGS]...

.. rubric:: Commands

.. hlist::
  :columns: 2

  * .. object:: cool2bcool
  * .. object:: pileup

----

polaris util cool2bcool
^^^^^^^^^^^^^^^^^^^^^^^^

The `cool2bcool` utility converts a `.mcool` file to a `.bcool` file. The `.bcool` file is compatible with `.mcool` files but requires less storage space.

    .. code-block:: bash

        polaris util cool2bcool [OPTIONS] MCOOL BCOOL

    .. rubric:: Required Arguments

    .. option:: MCOOL: 

        Path to the input ``.mcool`` file.

    .. option:: BCOOL: 

        Path of the ``.bcool`` file to save.

    .. rubric:: Options

    .. option:: -u <INTEGER> 

        Default: ``3000000``

        Distance upper bound in base pairs.

    .. option:: --resol <TEXT>

        Default: ``None``

        Comma-separated resolutions for the output. If not specified, the resolutions of input file will be used.

    .. option:: --help

        Display help information about this command and exit.

polaris util pileup
^^^^^^^^^^^^^^^^^^^^^^^^

The `pileup` utility generates 2D pileup contact maps around given foci.

    .. code-block:: bash

        polaris util pileup [OPTIONS] FOCI MCOOL

    .. rubric:: Required Arguments

    .. option:: FOCI: 

        Path to the ``.bedpe`` file in the same format as Polaris output, containing loop loci.

    .. option:: MCOOL: 

        Path of the ``.mcool`` file.

    .. rubric:: Options

    .. option:: -w <INTEGER> 

        Default: ``10``

        Window size in bins: (2w+1)x(2w+1).

    .. option:: --savefig <TEXT>

        Default: ``FOCI_pileup.png``

        Path to save pileup plot.

    .. option:: --p2ll <BOOLEAN>

        Default: ``False``

        Compute p2ll value.

    .. option:: --mindistance <INTEGER>

        Default: ``2w+1``

        Minimum distance in bins to skip, only for bedpe foci.

    .. option:: --maxdistance <INTEGER>

        Default: ``1e9``

        Maximum distance in bins to skip, only for bedpe foci.

    .. option:: --resol <INTEGER>

        Default: ``5000``

        Resolution.

    .. option:: --oe <BOOLEAN>

        Default: ``True``

        Use O/E normalized contact map or not.

    .. option:: --help

        Display help information about this command and exit.
