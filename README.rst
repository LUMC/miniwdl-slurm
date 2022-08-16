miniwdl-slurm
=============
Extends miniwdl to run workflows on SLURM clusters in singularity containers.

This `SLURM backend
<https://miniwdl.readthedocs.io/en/latest/runner_backends.html>`_ plugin for
`miniwdl <https://github.com/chanzuckerberg/miniwdl>`_ runs WDL task containers
by creating a job script that is submitted to a SLURM cluster. In case the job
description has a container, singularity will be used as container runtime.

Installation
------------

.. code-block::

    pip install git+https://github.com/LUMC/miniwdl-slurm.git

Configuration
--------------
The following information should be set in the `miniwdl configuration
<https://miniwdl.readthedocs.io/en/latest/runner_reference.html#configuration>`_:

.. code-block:: ini

    [scheduler]
    container_backend=slurm_singularity
    # task_concurrency defaults to the number of processors on the system.
    # since we submit the jobs to SLURM this is not necessary.
    # higher numbers means miniwdl has to monitor more processes simultaneously
    # which might impact performance.
    task_concurrency=200

    [singularity]
    # This plugin wraps the singularity backend. Make sure the settings are
    # appropriate for your cluster.
    exe = ["singularity"]

    # the miniwdl default options contain options to run as a fake root, which
    # is not available on most clusters.
    run_options = [
            "--containall"
        ]

    # If image_cache is not set, the miniwdl-slurm plugin will make sure it
    # is set to a directory inside $PWD to ensure availability on the cluster.
    image_cache = "$PWD/miniwdl_singularity_cache"

    [slurm]
    # Which partition to submit to. If omitted the "--partition" flag will
    # be omitted in the srun command, and the SLURM default will be used.
    #partition="heavy_users,gpu"