.. |br| raw:: html

   <br />

.. _spack:

###################################
Build required libraries with Spack
###################################

This page has instructions for building :term:`dependencies` for
GEOS-Chem Classic, GCHP, and HEMCO. These are the **software
libraries** that are needed to compile and execute these programs.


Before proceeding, please also check if the dependencies for
GEOS-Chem, GCHP, and HEMCO are already found on your computational
cluster or cloud environment. If this is the case, you may use the
pre-installed versions of these software libraries and won't have
to install your own versions.  You may find more information about
which software libraries are required at these links:

- `GEOS-Chem Classic software dependencies <https://geos-chem.readthedocs.io/en/stable/gcc-guide/01-startup/system-req-soft.html>`_
- `GCHP software dependencies <https://gchp.readthedocs.io/en/stable/getting-started/requirements.html#software-requirements>`_
- `HEMCO software dependencies <https://hemco.readthedocs.io/en/stable/hco-sa-guide/software.html>`_

.. _spack-intro:

============
Introduction
============

In the sections below, we will show you how to **build a single
software environment containing all dependencies for GEOS-Chem
Classic, GCHP, and HEMCO**.  This will be especially of use for those
users working on a computational cluster where these dependencies are
not installed.

We will be using the `Spack <https://spack.readthedocs.io>`_ package
manager to download and build all required dependencies for GEOS-Chem
Classic, GCHP and HEMCO.

.. note::

   Spack is not the only way to build the dependencies.
   It is possible to download and compile the source code for each
   library manually.  Spack automates this process, thus it is the
   recommended method.

You will be using this workflow:

#. :ref:`spack-setup`
#. :ref:`spack-model`
#. :ref:`spack-compiler`
#. :ref:`spack-build`
#. :ref:`spack-loader`
#. :ref:`spack-envfile`
#. :ref:`spack-cleanup`

.. _spack-setup:

=====================================
Install Spack and do first-time setup
=====================================

Decide where you want to install Spack. A few details you should consider are:

* This directory will be ~5-20 GB (keep in mind that some clusters
  limit :file:`$HOME` to a few GB).
* This directory cannot be moved (needs a rebuild if you need to move it in
  the future).
* If other people are going to use these dependencies, this directory
  should be in a shared location.

Once you choose an install location, proceed with the commands below.
You can copy-paste these commands, but lookout for lines marked with a
:literal:`# (modifiable) ...` comment as they might require
modification.

.. important::

   All commands in this tutorial are executed in the same directory.

Install Spack and perform the following first-time setup.

.. code-block:: console

   $ cd $HOME  # (modifiable) cd to the install location you chose

   $ git clone -c feature.manyFiles=true https://github.com/spack/spack.git  # download Spack

   $ source spack/share/spack/setup.env   # Load Spack

   $ spack external find                  # Tell Spack to look for existing software

   $ spack compiler find                  # Tell Spack to look for existing complilers

.. _spack-model:

=========================================
Clone a copy of GCClassic, GCHP, or HEMCO
=========================================

The GCClassic, GCHP, and HEMCO repositories contain YAML files named
:file:`spack/modules.yaml` and :file:`spack/packages.yaml`.  We have
updated these YAML files with the proper settings in order to ensure a
smooth build process with Spack.

Define the :envvar:`model`, :envvar:`scope_dir`, and
:envvar:`scope_args` environment variables as shown below.

.. code-block:: console

   $ model=GCClassic   # Use this if you will be working with GEOS-Chem Classic
   $ model=GCHP        # Use this if you will be working with GCHP
   $ model=HEMCO       # Use this if you will be working with HEMCO standalone

   $ scope_dir="${model}/spack"
   $ scope_args="-C ${scope_dir}"

We will be referring to these environment variables in the steps
below.

When you have completed this step, download the source code for your
preferred model (e.g. GEOS-Chem Classic, GCHP, or HEMCO standalone):

.. code-block:: console

   $ git clone --recurse-submodules https://github.com/geoschem/${model}.git

.. _spack-compiler:

================================
Install the recommended compiler
================================

Next, install the recommended compiler, :literal:`gcc` (aka the GNU
Compiler Collection).  Use the :envvar:`scope_args` environment
variable that you defined in the :ref:`previous step <spack-model>`.

.. code-block:: console

   $ spack ${scope_args} install gcc

.. note::

   The compiler version that will be installed is included in the
   :literal:`${scope_dir}/packages.yaml` file.

   As of this writing, the above command will install the `GNU
   Compiler Collection 10.2.0
   <https://gcc.gnu.org/onlinedocs/10.2.0/>`_ (includes C, C++, and
   Fortran compilers).  This may change in the future as necessary.

The compiler installation should take several minutes or longer,
depending on the speed of your internet connection.

Once the compiler has finished installing, register it with
Spack. This will allow Spack to use this compiler to build other
software packages.  Use this command:

.. code-block:: console

   $ spack compiler add $(spack location -i gcc)

You will then see output similar to this:

.. code-block:: console

   ==> Added 1 new compiler to /path/to/home/.spack/linux/compilers.yaml
       gcc@X.Y.Z
   ==> Compilers are defined in the following files:
       /path/to/home/.spack/linux/compilers.yaml

where

- :file:`/path/to/home` indicates the absolute path of your home
  directory folder
- :literal:`X.Y.Z` indicates the version of the GCC compiler that you
  just built with Spack.

.. tip::

   Use this command to view the list of compilers that have been
   registered with Spack:

   .. code-block:: console

      $ spack compiler list

.. _spack-build:

=============================================
Build GEOS-Chem dependencies and useful tools
=============================================

Once the compiiler has been built and registered, you may proceed to
building the dependencies for GEOS-Chem Classic, GCHP, and HEMCO.

The Spack installation commands that you will use take the form:

.. code-block:: console

   $ spack ${scope_args} install <package-name>%gcc^openmpi

where

- :literal:`${scope_args}` is the environment variable you defined in
  :ref:`Step 2 above <spack-model>`;
- :literal:`<package-name>` is a placeholder for the name of the
  software package that you wish to install;
- :literal:`%gcc` tells Spack that it should use the compiler that
  we just built;
- :literal:`^openmpi` tells Spack to use OpenMPI for packages that
  require it (e.g. HDF5, netCDF, ESMF, etc.).

Spack will download and build :literal:`<package-name>` plus all other
packages upon which :literal:`<package-name>` depends.

.. note::

   Use this command to find out what other packages will be built
   along with :literal:`<package-name>`:

   .. code-block:: console

      $ spack spec <package-name>

   This step is not required, but can be useful for informational purposes.

Use the following commands to build dependencies for GEOS-Chem
Classic, GCHP, and HEMCO, as well as some useful tools for working
with GEOS-Chem data:

#. Build the :program:`esmf` (Earth System Model Framework),
   :program:`hdf5`, :program:`netcdf-c`, :program:`netcdf-fortran`,
   and :program:`openmpi` packages:

   .. code-block:: console

      $ spack ${scope_args} install esmf%gcc^openmpi

   The above command will build all of the above-mentioned packages in
   a single step.

   .. note::

      GEOS-Chem Classic does not require :program:`esmf`.  However, we
      recommend that you build ESMF anyway so that it will already be
      installed in case you decide to use GCHP in the future.

   |br|

#. Build the :program:`cdo` (Climate Data Operators) and :program:`nco`
   (netCDF operators) packages.  These are command-line tools for
   editing and manipulating data contained in netCDF files.

   .. code-block:: console

      $ spack ${scope_args} install cdo%gcc^openmpi

      $ spack ${scope_args} install nco%gcc^openmpi

   |br|

#. Build the :program:`ncview` package, which is a quick-and-dirty
   netCDF file viewer.

   .. code-block:: console

      $ spack ${scope_args} install ncview%gcc^openmpi

   |br|

#. Build the :program:`flex` (Fast Lexical Analyzer) package.  This is
   a dependency of the `Kinetic PreProcessor (KPP)
   <https://kpp.readthedocs.io>`_, with which you can update GEOS-Chem
   chemical mechanisms.

   .. code-block:: console

      $ spack ${scope_args} install flex%gcc

   .. note::

      The :program:`flex` package does not use OpenMPI.  Therefore, we
      can omit :literal:`^openmpi` from the above command.

At any time, you may see a list of installed packages by using this
command:

.. code-block:: console

   $ spack find

This will show all of the packages installed to date.

.. _spack-loader:

======================
Generate a load script
======================

Once you have built the required packages for GEOS-Chem Classic, GCHP,
and HEMCO, you can create a script that will load them into your login
environment.

First, define the :envvar:`load_script_name` and :envvar:`loads_args:`
environment variables.  You'll need these for subsequent Spack commands.

.. code-block:: console

   $ load_script="geoschem_deps-$(date +%Y.%m)"

   $ load_cmd="loads --dependencies -r -p $(pwd)/spack/share/spack/modules/linux-*-x86_64"

Next, check if you have a module management system already installed
on your system.  If you do, you can use this to load your Spack-built
packages.  If not, we will show you how to install a module manager.

.. _spack-loader-envmod:

For environment-modules
-----------------------

To check if the `environment-modules module manager
<https://modules.sourceforge.net>`_ has been already installed on
your system, type:

.. code_block:: console

   $ echo ${MODULEPATH} | grep environment-modules

If the above-listed command returns a list of directories separated by
:file:`:` characters, then you have a version of
:program:`environment-modules` installed.

If on the other hand, the above-listed command returns a
blank line, then :program:`environment-modules` has not been
installed on your system.  Skip ahead to the :ref:`next section
<spack-loader-lmod>`.

You may now create a script that will use
:program:`environment-modules` to load your Spack-built packages.  Use
the following command to remove any pre-existing modules:

.. code-block:: console

   $ spack ${scope_args} module tcl refresh -y     # Removes all module files

Then use the commands listed below to create a script that will load
all of the Spack-built modules with :program`environment-modules`.

.. code-block:: console

   $ spack ${scope_args} module tcl ${load_cmd} gcc                >  ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} cmake%gcc          >> ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} esmf%gcc^openmpi   >> ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} cdo%gcc^openmpi    >> ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} nco%gcc^openmpi    >> ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} flex%gcc           >> ${load_script}
   $ spack ${scope_args} module tcl ${load_cmd} ncview%gcc^openmpi >> ${load_script}

This will create a load script entitled :file:`geoschem_deps.YYYY.MM`,
where :literal:`YYYY` and :file:`MM` are placeholders for the current
year (e.g. :literal:`2003`) and month (e.g. :literal:`04`).

You may now proceed to the section entitled: :ref:`spack-envfile`.

For Lmod
--------

To check if the `Lmod module manager
<https://lmod.readthedocs.io/en/latest/>`_ has been already installed
on your system, type:

.. code_block:: console

   $ echo ${MODULEPATH} | grep lmod

If the above-listed command returns a list of directories separated by
:file:`:` characters, then you have a version of the :program:`Lmod` module
manager installed.

If on the other hand, the above-listed command returns a blank line,
then :program:`Lmod` has not been installed on your system.  Skip
ahead to the :ref:`next section <spack-loader-lmod>`.

You may now create a script that will use :program:`Lmod` to load
your Spack-built packages.  Use the following command to remove any
pre-existing modules:

.. code-block:: console

   $ spack ${scope_args} module lmod refresh -y     # Removes all module files

Then use these commands to create a load script with the commands to
load each Spack-built package plus its dependencies:

.. code-block:: console

   $ spack ${scope_args} module lmod ${load_cmd} gcc                >  ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} cmake%gcc          >> ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} esmf%gcc^openmpi   >> ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} cdo%gcc^openmpi    >> ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} nco%gcc^openmpi    >> ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} flex%gcc           >> ${load_script}
   $ spack ${scope_args} module lmod ${load_cmd} ncview%gcc^openmpi >> ${load_script}

This will create a load script entitled :file:`geoschem_deps.YYYY.MM`,
where :literal:`YYYY` and :file:`MM` are placeholders for the current
year (e.g. :literal:`2003`) and month (e.g. :literal:`04`).

You may now proceed to the section entitled: :ref:`spack-envfile`.

.. _spack-loader-nomods:

Install a module manager if you don't have one already
------------------------------------------------------

If your system does not have a module manager installed, you can use
Spack to build one.

Install the :program:`environment-modules` module manager with the
following commands:

.. code-block:: console

   $ spack ${scope_args} install environment-modules

   $ spack load environment-modules

Next, return to the :ref:`spack-loader-envmod` section and follow the
directions there.  Then you may proceed to the :ref:`last section
<spack-envfile>`.

.. _spack-envfile:

===============================================
Source the load script from an environment file
===============================================

We recommend "sourcing" the load_script that you created in the
:ref:`previous section <spack-loader>` from within an **environment
file**.  This is a file that not only loads the required modules but
also defines settings that you need to run GEOS-Chem Classic, GCHP, or
the HEMCO standalone.

For more information about environment files, please see:

- `GEOS-Chem Classic environment files <https://geos-chem.readthedocs.io/en/stable/gcc-guide/01-startup/login-env-files.html>`_
- `HEMCO environment files <https://hemco.readthedocs.io/en/stable/hco-sa-guide/login-env.html>`_

Here is a sample environment file for GEOS-Chem Classic that uses our
load script.

.. code-block:: bash

   # Echo message if we are in a interactive (terminal) session
   if [[ $- = *i* ]] ; then
     echo "Loading modules for GEOS-Chem, please wait ..."
   fi

   #==============================================================================
   # Modules for GEOS-Chem Classic
   #==============================================================================

   # Activate environment-modules
   source /etc/profile.d/modules.sh

   # Remove previously-loaded modules
   module purge

   # Load modules created in April 2023
   source geoschem_deps.2023.04

   #==============================================================================
   # Environment variables
   #==============================================================================

   # Parallelization settings for GEOS-Chem Classic
   export OMP_NUM_THREADS=8
   export OMP_STACKSIZE=500m

   # Make all files world-readable by default
   umask 022

   # Specify compilers
   export CC=gcc
   export CXX=g++
   export FC=gfortran

   # Set memory limits to max allowable
   ulimit -c unlimited              # coredumpsize
   ulimit -l unlimited              # memorylocked
   ulimit -u 50000                  # maxproc
   ulimit -v unlimited              # vmemoryuse
   ulimit -s unlimited              # stacksize

   # List modules loaded
   module list

Copy and paste the code above into an environent file and save it to
the path :file:`/gcclassic_gnu.env`.  This denotes that this
environment file will load settings for GEOS-Chem Classic with the GNU
compilers.

.. tip::

   Keeping the module load commands (in :file:`geoschem_deps.2023.04`)
   separate from the :file:`gcclassic_gnu.env` will allow you to quickly
   switch to an updated load script while preserving the rest of your
   settings.

To load the Spack-built packages and apply the settings into your
login environment, use this command:

.. code-block:: $console

   $ source ~/gcclassic_gnu.env

.. _spack-cleanup:

========
Clean up
========

At this point, you can remove the :file:`${model}` directory as it is
not needed.  (Unless you would like to keep it to build the executable)

The :file:`spack` directory needs to remain.  As mentioned above, this
directory cannot be moved.  If you need to relocate the :file:`spack`
folder, do a fresh installation.
