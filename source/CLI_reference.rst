CLI Reference
=============

Quick reference
---------------

.. code-block:: bash

   polaris COMMAND [ARGS]...

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - command
     - 
   * - polaris loop
     - Loop annotation commands
   * - polaris util
     - Analysis and visualization utilities

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
------------------

    This is the simplest approach, allowing to directly predict loops in a single step
    Bin any text file or stream of pairs.

    Pairs data need not be sorted. Accepts compressed files.
    To pipe input from stdin, set PAIRS_PATH to '-'.


    BINS : One of the following

        <TEXT:INTEGER> : 1. Path to a chromsizes file, 2. Bin size in bp

        <TEXT> : Path to BED file defining the genomic bin segmentation.

    PAIRS_PATH : Path to contacts (i.e. read pairs) file.

    COOL_PATH : Output COOL file path or URI.

.. program:: cooler cload pairs
.. code-block:: shell

    cooler cload pairs [OPTIONS] BINS PAIRS_PATH COOL_PATH

.. rubric:: Arguments

.. option:: BINS

    Required argument

.. option:: PAIRS_PATH

    Required argument

.. option:: COOL_PATH

    Required argument

.. rubric:: Options

.. option:: --metadata <metadata>

    Path to JSON file containing user metadata.

.. option:: --assembly <assembly>

    Name of genome assembly (e.g. hg19, mm10)

.. option:: -c1, --chrom1 <chrom1>

    chrom1 field number (one-based)  [required]

.. option:: -p1, --pos1 <pos1>

    pos1 field number (one-based)  [required]

.. option:: -c2, --chrom2 <chrom2>

    chrom2 field number (one-based)  [required]

.. option:: -p2, --pos2 <pos2>

    pos2 field number (one-based)  [required]

.. option:: --chunksize <chunksize>

    Number of input lines to load at a time

.. option:: -0, --zero-based

    Positions are zero-based  [default: False]

.. option:: --comment-char <comment_char>

    Comment character that indicates lines to ignore.  [default: #]

.. option:: -N, --no-symmetric-upper

    Create a complete square matrix without implicit symmetry. This allows for distinct upper- and lower-triangle values

.. option:: --input-copy-status <input_copy_status>

    Copy status of input data when using symmetric-upper storage. | `unique`: Incoming data comes from a unique half of a symmetric map, regardless of how the coordinates of a pair are ordered. `duplex`: Incoming data contains upper- and lower-triangle duplicates. All input records that map to the lower triangle will be discarded! | If you wish to treat lower- and upper-triangle input data as distinct, use the ``--no-symmetric-upper`` option.   [default: unique]

.. option:: --field <field>

    Specify quantitative input fields to aggregate into value columns using the syntax ``--field <field-name>=<field-number>``. Optionally, append ``:`` followed by ``dtype=<dtype>`` to specify the data type (e.g. float), and/or ``agg=<agg>`` to specify an aggregation function different from sum (e.g. mean). Field numbers are 1-based. Passing 'count' as the target name will override the default behavior of storing pair counts. Repeat the ``--field`` option for each additional field.

.. option:: --temp-dir <temp_dir>

    Create temporary files in a specified directory. Pass ``-`` to use the platform default temp dir.

.. option:: --no-delete-temp

    Do not delete temporary files when finished.

.. option:: --max-merge <max_merge>

    Maximum number of chunks to merge before invoking recursive merging  [default: 200]

.. option:: --storage-options <storage_options>

    Options to modify the data filter pipeline. Provide as a comma-separated list of key-value pairs of the form 'k1=v1,k2=v2,...'. See http://docs.h5py.org/en/stable/high/dataset.html#fi