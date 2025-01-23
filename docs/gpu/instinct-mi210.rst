====================
Instinct - AMD MI210
====================

Acknowledgments
===============
We greatly appreciate the donation of this test platform from AMD to `Dr. Spencer Bryngelson <https://comp-physics.group/>`__, a CRNCH faculty member working on computational fluid dynamics (CFD) codes including `MFC <https://mflowcode.github.io/>`__.

Current Status
==============

BUGS / Feature Requests
-----------------------

- N/A

System Specifications
=====================

.. list-table:: 
    :widths: auto
    :header-rows: 1
    :stub-columns: 1

    * - Queues
      - CPU
      - Memory (GB)
      - Network
      - Cards
      - Notes
    * - rg-nextgen-hpc
      - 2x `AMD EPYC 7713 (Milan) <https://www.amd.com/en/products/cpu/amd-epyc-7713>`__
      - 512 GB DDR4, 3200 MHz, 32 GB DIMMs
      - Connect-X 6 (MT28908), 10 GE
      - 2x `MI210 <https://www.amd.com/en/products/server-accelerators/amd-instinct-mi210>`__
      -       

Instinct is a single node server with two AMD 210 GPUs, which are very similar to the
MI250x GPUs deployed in `ORNL's Frontier <https://www.olcf.ornl.gov/frontier/>`__. This
server has two Milan CPUs, 512 GB of DDR4 memory, and a Connect-X 6 networking card.

Software and Tools
==================

.. list-table::
    :widths: auto
    :header-rows: 1
    :stub-columns: 1

    * - Distro
      - Kernel
      - Standard Compilers
      - Other Compilers
      - MPI
      - Miscellaneous
    * - RHEL8
      - 4.18.0
      - GCC 8.5
      - ROCm 6.1.0, `AOCC 4.0.0 <https://developer.amd.com/wp-content/resources/57222_AOCC_UG_Rev_3.2.pdf>`__
      - 
      - AOCL 3.2.0, `ROCm libraries 6.1.0 <https://rocblas.readthedocs.io/en/rocm-6.1.0/>`__, `uProf 4.2 <https://developer.amd.com/amd-uprof/>`__

How do I get to Instinct?
=========================

As with most CRNCH resources, you need to either log in via the gateway
node, rg-login, or access the system from the campus network via VPN or
an on-campus connection. 

To request an allocation on Instinct using slurm:

.. code::

    //Request an allocation of 1 hr, partition rg-nextgen-hpc, and specify the node name for the server with -w
    salloc -t 1:00:00 -p rg-nextgen-hpc -w instinct
   
To request an entire node (and all the memory) you can run:


.. code::

    //Request an allocation of with 2 sockets, all cores in each socket, and 2 threads per core, partition rg-nextgen-hpc, 
    //specify the node name for the server with -w, and request unlimited memory (otherwise cgroups limits to 1 GB per core)
    salloc --sockets-per-node=2 --cores-per-socket=64 --threads-per-core=2 -p rg-nextgen-hpc -w instinct --mem=0 

Compiling for the MI210 GPUs
----------------------------

Currently we suggest using the ROCm compiler stack to compile or GCC 12+.

Modules should be available but you may need to update your module path:

.. code::
      
    []$ module avail

    -------------------------------------------- /projects/tools/x86_64/rhel-8/modulefiles -------------------------------------  
   aocc/3.2.0    aocl/aocc-3.2.0    aocl/gcc-3.2.0 (D)    rocm/6.1.0
    
    []$ module load rocm/6.1.0
    []$ which hipcc
    /opt/rocm-6.1.0/bin/hipcc
   
Useful ROCm tools include hipcc, amdclang, aompcc, amdflang, rocgdb, and rocprof. 

Running OpenACC with GCC13
~~~~~~~~~~~~~~~~~~~~~~~~~~
Alex Woods has compiled GCC13 for testing OpenACC on the Instinct GPU, and we have put his script in the internal docs `here <https://github.gatech.edu/crnch-rg/rogues-docs/blob/master/hpc/amd-gpu/build-gcc13-amdgpu.sh>`__ When compiling with the new gcc install, add the flags :code:`-fopenacc -foffload-options=-march=gfx90a` for the Instinct MI2XX GPUs. Note that performance will likely be slow. 

Running jobs
------------

Once you have compiled your code, you can request a longer job to do
testing.

Useful training material
------------------------

- `OmniTrace <https://amdresearch.github.io/omnitrace/>`__ - AMD's newest tracing and profiling tool.
- `Introduction to Profiling Tools for AMD Hardware <https://gpuopen.com/learn/amd-lab-notes/amd-lab-notes-profilers-readme/>`__ - this article has a great breakdown of when to use specific profilers. OmniTrace and uProf are the newest profiling tools.

Vendor-provided Documents and Resources
---------------------------------------

- `AMD Training Center (Getting Started, pointers to docs) <https://developer.amd.com/resources/rocm-learning-center/>`__
