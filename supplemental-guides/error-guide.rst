.. |br| raw:: html

   <br />

.. _errguide:

################################
Understand common error messages
################################

In this Guide we provide information about the different types of errors
that your GEOS-Chem simulation might encounter.

.. important::

   **Warnings** are non-fatal informational messages.  Usually you do
   not have to take any action when encountering a warning.
   Nevertheless, you should always try to investigate why the warning
   was generated in the first place.

   **Errors** are fatal and will halt GEOS-Chem compilation or
   execution. Looking at the error message will give you some clues as
   to why the error occurred.

We strongly encourage that you try to debug the issue using the info
both in this Guide and in our :ref:`debug-guide` Guide.  Please see
our `Support Guidelines
<https://geos-chem.readthedocs.io/en/latest/help-and-reference/SUPPORT.html>`_
for more information.

.. _errguide-where:

====================================
Where does error output get printed?
====================================

`GEOS-Chem Classic <https://geos-chem.readthedocs.io>`_  `GCHP
<https://gchp.readthedocs.io>`_, and `HEMCO
<https://hemco.readthedocs.io>`_, like all Linux-based programs,
send output to two streams: **stdout** and **stderr**.

Most output will go to the **stdout** stream, which takes I/O from the
Fortran :code:`WRITE` and :code:`PRINT` commands. If you run
e.g. GEOS-Chem Classic by just typing the executable name at the Unix
prompt:

.. code-block:: console

   $ ./gcclassic

then the stdout stream will be printed to the terminal window. You can
also redirect the stdout stream to a log file with the redirect command:

.. code-block:: console

   $ ./gcclassic > GC.log 2>&1

The :command:`2>&1` tells the bash script to append the stderr stream
(noted by :literal:`2`) to the stdout stream (noted by :literal:`1`).
This will make sure that any error output also shows up in the log file.

You can also use the Linux :command:`tee` command, which will send
output both to a log file as well as to the terminal window:

.. code-block:: console

   $ ./gcclassic | tee GC.log 2>&1

.. note::

   GCHP sends output to several log files, in addition to stdout and
   stderr.  Please see `gchp.readthedocs.io
   <https://gchp.readthedocs.io>`_ for more information.

.. _errguide-compile:

===================
Compile-time errors
===================

In this section we discuss some compilation warnings that you may
encounter when building GEOS-Chem.

.. _errguide-compile-ncinc:

Cannot open include file netcdf.inc
-----------------------------------

.. code-block:: console

   error #5102: Cannot open include file 'netcdf.inc'

**Problem:** The :program:`netcdf-fortran` library cannot be found.

**Solution:** Make sure that :ref:`all software dependencies have been
installed <spackguide>` and :ref:`loaded into your Linux environment
<libguide>`.

.. _errguide-compile-flex:

KPP error: Cannot find -lfl
---------------------------

.. code-block:: console

   /usr/bin/ld: cannot find -lfl
   error: ld returned exit 1 status

**Problem:**: The `Kinetic PreProcessor (KPP)
<https://kpp.readthedocs.io>`_ cannot find the :program:`flex`
library, which is one of its dependencies.

**Solution:** Make sure that :ref:`all software dependencies have been
installed <spackguide>` and :ref:`loaded into your
Linux environment <libguide>`.

GNU Fortran internal compiler error
-----------------------------------

.. code-block:: console

   f951: internal compiler error: in ___ at ___

**Problem:** Compilation halted due to a compiler issue.  This may
indicate that your compiler version is too old to compile GEOS-Chem
Classic, GCHP, and/or HEMCO.

**Solution:** Try switching to a newer compiler:

- For GCHP: Use GNU Compiler Collection 9.3 and later
- For GEOS-Chem Classic and HEMCO: Use GNU Compiler Collection 7.0 and later

.. _errguide-runtime:

===============
Run-time errors
===============

Forced exit from Rosenbrock
---------------------------

.. code-block:: none

   Forced exit from Rosenbrock due to the following error:
   --> Step size too small: T + 10*H = T or H < Roundoff
   T=   3044.21151383269      and H=  1.281206877135470E-012
   ### INTEGRATE RETURNED ERROR AT:          40          68           1

   Forced exit from Rosenbrock due to the following error:
   --> Step size too small: T + 10*H = T or H < Roundoff
   T=   3044.21151383269      and H=  1.281206877135470E-012
   ### INTEGRATE FAILED TWICE ###

   ###############################################################################
   ### KPP DEBUG OUTPUT
   ### Species concentrations at problem box           40          68          1
   ###############################################################################
   ... printout of species concentrations ...

   ###############################################################################
   ### KPP DEBUG OUTPUT
   ### Species concentrations at problem box           40          68          1
   ###############################################################################
   ... printout of reaction rates ...

**Problem:** The KPP Rosenbrock integrator could not converge to a
solution at a particular grid box.

If the non-convergence only happens once, then GEOS-Chem will revert
to prior concentrations and reset the saved KPP internal timestep
(:code:`Hnew`) to zero before calling the Rosenbrock integrator again.
In many instances, this is sufficient for the chemistry to converge to
a soluiton.

In the case that the Rosenbrock integrator fails to converge to a
solution twice in a row, all of the concentrations and
reaction rates at the grid box will be printed to :ref:`stdout
<errguide-where>` and the simulation will terminate.

This error can be caused by:

- A particular species has numerically underflowed or overflowed.
- A division by zero in the reaction rate computations has occurred.
- A species has been set to a very low value in another operation
  (e.g. wet scavenging), thus causing the non-convergence.
- The initial conditions of the simulation may non-physical.
- A data file (meteorology or emissions) may be corrupted.

**Solution:** Look at the error printout.  You will likely notice
species concentrations or reaction rates that are extremely high or
low compared to the others. This will give you a clue as to where to
start looking.

Try performing some short test simulations, turning each operation
(e.g. transport, PBL mixing, convection, etc). off one at a time.
This should isolate the location of the error.  Make sure to turn on
verbose output in both :file:`geoschem_config.yml` and
:file:`HEMCO_Config.rc`; this will send additional printout to the
:ref:`stdout <errguide-where>` stream.  The clue to finding the error
may become obvious by looking at this output.

Check your restart file to make sure that the initial concentrations
make sense.  For certain simulations, using initial conditions from a
simulation that has been sufficiently spun-up makes a difference.

Use a netCDF file viewer like :program:`ncview` to open the
meteorology files on the day that the error occurred.  If a file
does not open properly, it is probably corrupted.  If you suspect that
the file may have been corrupted during download, then download the
file again from its original source.  If this still does not fix the
error, then the file may have been corrupted at its source.  Please
open a new Github issue to alert the GEOS-Chem Support Team.

HEMCO Error: Cannot find field
------------------------------

.. code-block:: console

   HEMCO Error: Cannot find field ___.  Please check the name in the config file.

**Problem:** A GEOS-Chem Classic or HEMCO standalone simulation halts
because HEMCO cannot find a certain input field.

**Solution:** Most of the time, this error indicates that a species is
missing from the `GEOS-Chem restart file
<https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/restart-files-gc.html/restart-files-gc.html>`_.

By default, the GEOS-Chem restart file (entry :literal:`SPC_` in
the `HEMCO configuration file
<https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/hemco-config.html>`_
:literal:`EFYO`.  This setting tells HEMCO to halt if it cannot find a
species in the restart file.  Changing this time cycle flag
to :literal:`CYS` will allow the simulation to proceed.  Missing species
will then be assigned a default background value.

List-directed I/O syntax error
------------------------------

.. code-block:: console

   forrtl: severe (59): list-directed I/O syntax error, unit -5, file Internal List-Directed Read

**Problem:** This error indicates that the wrong type of data was read
from a text file.  This can happen when:

- Numeric input is expected but character input was read from disk (or
  vice-versa);
- A :command:`READ` statement in your code has been omitted or deleted.

**Solution:** Check configuration files (:file:`geoschem_config.yml`,
:file:`HEMCO_Config.rc`, :file:`HEMCO_Diagn.rc`, etc.) to make sure
that the actual input matches what GEOS-Chem or HEMCO is actually
reading.


Nf_Def_Var: can not define variable
-----------------------------------

.. code-block:: console

   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

   Nf_Def_var: can not define variable: ____

   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

   Code stopped from DO_ERR_OUT (in module NcdfUtil/m_do_err_out.F90)

   This is an error that was encountered in one of the netCDF I/O modules,
   which indicates an error in writing to or reading from a netCDF file!

   !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

**Problem:** GEOS-Chem or HEMCO could not write a variable to a netCDF
file.  This error may be caused by:

#. The netCDF file is write-protected and cannot be overwritten.
#. The path to the netCDF file is incorrect (i.e. directory does not exist).
#. The netCDF file already contains a variable with the same name.

** Solution:** Try the following:

#. If overwriting a netCDF file, make sure it has write permission
   (i.e. do :literal`chmod 644 myfile.nc`). |br|
   |br|

#. Make sure that the path where you intend to write the netCDF file
   exists. |br|
   |br|

#. Check your :file:`HISTORY.rc` and :file:`HEMCO_Diagn.rc` diagnostic
   configuration files to make sure that you are not writing more than
   one diagnostic variable with the same name.

NetCDF: HDF Error
-----------------

.. code-block:: console

   NetCDF: HDF error

**Problem:** The netCDF library routines in GEOS-Chem or HEMCO cannot
read a netCDF file.  The error is occurring in the HDF5 library (upon
which netCDF depends).  This may indicate that the netCDF file is
either incomplete or corrupted.

**Solution:** Try re-downloading the file from the WashU data portal.
This is usually sufficient to fix the issue.  If the error persists,
please open a new GitHub issue to alert the GEOS-Chem Support team, as
the corruption may have occured at the original source of te data.

Permission denied error
-----------------------

If you receive this error:

``v11-01.run: Permission denied. ``

after having submitted a `run script to a queue
system <Running_GEOS-Chem#Running_as_a_Batch_Job>`__ (such as SLURM or
Grid Engine), then doublecheck the Unix permissions of your
``v11-01.run`` script. If the script does not have the Unix "execute"
permission then the queue system will not be able to run it.

Use the Unix chmod command to make your script executable

``chmod 755 v11-01.run``

and then re-submit the script to the queue system.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 22:17, 6
January 2017 (UTC)

Floating invalid or floating-point exception error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can check for several common floating-point math errors by compiling
with the ``FPEX=y`` option. This will halt the simulation with an error
message such as:

| `` forrtl: error (65): floating invalid    # Error message from Intel Fortran Compiler``
| `` ``
| `` Floating point exception (core dumped)  # Error message from GNU Fortran Compiler``

This error typically means that a division-by-zero occurred, or a NaN
value was encountered in one of your variables.

A common way to prevent these types of errors is to ensure "safe"
divisions (i.e. to make sure that the denominator is nonzero). You can
do this manually with an ``IF`` statement, or use the routines
``SAFE_DIV`` or ``IS_SAFE_DIV`` in ``GeosCore/error_mod.F``.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 14:58, 11
October 2017 (UTC)


Mixed file access modes error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This error is particular to the `Intel Fortran
Compiler <Intel_Fortran_Compiler>`__. You might encounter this in
conjunction with the ND49 timeseries diagnostic.

According to the Intel website:

| ``severe (31): Mixed file access modes``
| ``FOR$IOS_MIXFILACC. An attempt was made to use any of the following combinations:``
| ``* Formatted and unformatted operations on the same unit``
| ``* An invalid combination of access modes on a unit, such as direct and sequential``
| ``* An Intel® Fortran RTL I/O statement on a logical unit that was opened by a program coded in another language``

Here are a few suggestions to try if you haven’t already:

#. Make sure you don’t already have a ND49 file in the location that
   you’re writing. In other words, make sure GEOS-Chem isn’t trying to
   write to a file that already exists.
#. Do “make realclean”, recompile, and run again to see if the error is
   persistent.
#. Are you using an out-of-the-box version of the code or a modified
   version? If the latter, do you still get this error with a “clean”
   copy of the code?
#. Is there a particular READ/WRITE format statement that is causing the
   problem? You could try compiling with BOUNDS=y TRACEBACK=y FPE=y
   DEBUG=y and running in totalview (do “module load totalview” first on
   Odyssey) to locate the problematic line.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 19:07, 12 April
2017 (UTC)

Negative tracer found in WETDEP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If your simulation encounters negative (or NaN) tracer concentrations in
the WETDEP routine, then this can be an indication of a problem further
upsteam, perhaps in the aerosol routines (highly probable if the tracer
is SO4, SO4s, HNO3, SO2, or NH3). We have fixed some of these bugs by
making the code more robust. If you are using a GEOS-Chem version prior
to v8-01-01, then you should get
`ftp://ftp.as.harvard.edu/pub/geos-chem/patches/v8-01-01/ these
patches <ftp://ftp.as.harvard.edu/pub/geos-chem/patches/v8-01-01/>`__.
(These patches have been added to the standard GEOS-Chem code in
versions higher than v8-01-01.) Please see the following links for more
information:

-  `Values of ``F_PRIME`` > 1 in routine
   ``WETDEP`` <Wet_deposition#Values_of_F_PRIME_greater_than_1_in_WETDEP>`__
-  `Negative tracer due to negative RH values in the met field
   data <GEOS-5_issues#Small_negative_RH_value_in_20060206.a6.2x25_file>`__
-  `Negative tracer in routine
   WETDEP <Wet_deposition#Negative_tracer_in_routine_WETDEP>`__
-  `Negative tracer in routine WETDEP
   #2 <Wet_deposition#Negative_tracer_in_routine_WETDEP_.232>`__
-  `A bug in
   RPMARES <Aerosol_thermodynamical_equilibrium#Run_dies_in_RPMARES_unexpectedly>`__
   was also leading to a crash in WETDEP.

If the fixes above do not solve your problem, you will need to debug.
The first step is to use few calls to CHECK_STT (from ``tracer_mod.f``)
to isolate the part of the code where negative tracers are created. This
can be done quite fast if the code dies early enough in the run.

--`Bob Y. <User:Bmy>`__ 12:50, 15 July 2011 (EDT)

Run time errors originating in the HEMCO emissions component
------------------------------------------------------------

HEMCO Error: Cannot find file for current simulation time
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you see an error such as this in your ``HEMCO.log`` file:

| ``HEMCO ERROR: Cannot find file for current simulation time: ./GEOSChem.Restart.``\ \ ``1712``\ \ ``0701_0000z.nc4 - Cannot get field SPC_NO. ``
| ``Please check file name and time (incl. time range flag) in the config. file``

Then this can have a couple of causes:

#. HEMCO cannot find the file because it is missing on disk.

   -  HEMCO will try to look back in time starting with the current year
      and going all the way back to the year 1712 or 1713. So if you see
      1712 or 1713 in the error message, that is a tip-off that the file
      is missing.

#. HEMCO cannot find an expected variable name within a file.

   -  This can happen, for example, if you are using `GEOS_Chem
      12.1.0 <GEOS-Chem_12#12.1.0>`__ with a restart file from an older
      version. (Note that the variable names in the GEOS-Chem 12.1.0
      restart files were changed, `please see this post for more
      information <GEOS-Chem_restart_files#Restart_files_in_GEOS-Chem_12>`__.)

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 20:28, 20
December 2018 (UTC)

HEMCO Run Error
~~~~~~~~~~~~~~~

Errors messages containing "HCO" originate in `the HEMCO emissions
component <HEMCO>`__. For example:

| `` ===============================================================================``
| `` GEOS-CHEM ERROR: HCO_RUN``
| `` STOP at HCOI_GC_RUN (hcoi_gc_main_mod.F90)``
| `` ===============================================================================``

Additional helpful diagnostic information can be found in the HEMCO log
file, which is usually named ``HEMCO.log``.

--`Chris Holmes <User:Chris_Holmes>`__
(`talk <User_talk:Chris_Holmes>`__) 15:42, 24 June 2015 (UTC)

Updated error message for v11-01
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In `GEOS-Chem v11-01 <GEOS-Chem_v11-01>`__ and higher versions,
`additional text instructs the user to also check the HEMCO log
file <GEOS-Chem_v11-01#Error_message_output_now_advises_users_to_check_the_HEMCO_log_file>`__.

| `` ===============================================================================``
| `` GEOS-CHEM ERROR: HCO_RUN ``
| `` ``
| `` ``\ \ ``HEMCO ERROR: Please check the HEMCO log file for error messages!``\
| `` ``
| `` STOP at HCOI_GC_RUN (GeosCore/hcoi_gc_main_mod.F90)``
| `` ===============================================================================``

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 20:50, 6
January 2017 (UTC)

HEMCO time stamps may be wrong
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| ``HEMCO reads the files but gives zero emissions and shows the following time step error:``
| ``HEMCO WARNING: ncdf reference year is prior to 1901 - time stamps may be wrong!``
| ``--> LOCATION: GET_TIMEIDX (hco_read_std_mod.F90)``

`Lizzie Lundgren <User:Lizzie_Lundgren>`__\ **\ wrote:**

   That HEMCO error occurs if the reference time for the netCDF file
   time dimension is prior to 1901. If you do ``ncdump –c filename`` you
   will be able to see the metadata for the time dimension as well as
   the time variable values. The time units should include the reference
   date.

   You can get around this issue by changing the reference time within
   the file. You can do this with CDO (climate data operators) using the
   ``setreftime`` command.

   Here is a bash script example (by GCST member `Melissa
   Sulprizio <User:Melissa_Payer>`__) that updates the calendar and
   reference time for all files ending in ``*.nc`` within a directory.
   Support team member developed this for a user very recently who ran
   into the same issue. In that case the first file was for Jan 1, 1950,
   so that was made the new reference time. I would recommend doing the
   same for your dataset so that the first time variable value would be
   0. This script also compresses the file which we recommend doing.

| ``     #!/bin/bash``
| ``     ``
| ``     for file in *nc; do``
| ``        echo "Processing $file"``
| ``        cdo setcalendar,standard $file tmp.nc``
| ``        mv tmp.nc $file``
| ``        cdo setreftime,1950-01-01,0 $file tmp.nc``
| ``        mv tmp.nc $file``
| ``        nccopy -d1 -c "time/1" $file tmp.nc``
| ``        mv tmp.nc $file``
| ``     done``

   After you update the file you can then again do
   ``ncdump –c filename`` to check the time dimension. For the case
   above it looks like this after processing.

| ``             double time(time) ;``
| ``                     time:standard_name = "time" ;``
| ``                     time:long_name = "time" ;``
| ``                     time:bounds = "time_bnds" ;``
| ``                     time:units = "days since 1950-01-01 00:00:00" ;``
| ``                     time:calendar = "standard" ;``
| ``             . . .``
| ``     time = 0, 31, 59, 90, 120, 151, 181, 212, 243, 273, 304, 334, 365, 396, 424, ``
| ``          455, 485, 516, 546, 577, 608, 638, 669, 699, 730, 761, 790, 821, 851,``
| ``          882, 912, 943, 974, 1004, 1035, 1065, 1096, 1127, 1155, 1186, 1216, 1247, etc``

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 21:33, 11 April
2018 (UTC)

.. _overview-3:

Overview
--------

If your simulation dies with a **segmentation fault** error, this means
that GEOS-Chem tried to access an `invalid memory
location <http://stackoverflow.com/questions/2346806/what-is-segmentation-fault>`__.
A segmentation fault error message looks similar to this:

``forrtl: severe (174): SIGSEGV, segmentation fault occurred``

but may differ depending on the compiler version you are using.
Segmentation faults can be due to several causes, as shown in the
following sections.

Array-out-of-bounds error
~~~~~~~~~~~~~~~~~~~~~~~~~

Most often, a segmentation fault indicates an array out-of-bounds
condition. To find out more information about where this error is
occurring, recompile GEOS-Chem with the following Makefile options:

| ``make realclean``
| ``make BOUNDS=yes TRACEBACK=yes``

The ``BOUNDS=yes`` option will turn on **Array Out-of-Bounds** error
checking. The ``TRACEBACK=yes`` option will print out the **Error
Stack**, as `described above <#Traceback_error_stack>`__. These options
will provide more detailed error output.

After recompiling, you should receive an error message such as:

``forrtl: severe (408): fort: (3): Subscript #1 of the array PBL_THICK has value -1000000 which is less than the lower bound of 1``

This tells you that there is a problem with a certain array. Use the
Unix ``grep`` command to search for all instances of this array in the
GEOS-Chem source code:

``grep -i PBL_THICK *.f*``

and search for the problem.

NOTE: In the above example, we manually forced an out-of-bounds error
with this line of code:

| ``        !### FORCE OOB error for testing``
| ``        PBL_THICK(-1000000,J)   = BLTHIK``

Removing this line will fix the error.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 22:19, 6
January 2017 (UTC)

Invalid memory access
~~~~~~~~~~~~~~~~~~~~~

A segmentation fault can also happen if GEOS-Chem makes an reference to
a memory location that is invalid. You may see an error message such as
this:

| ``severe (174): SIGSEGV, segmentation fault occurred``
| ``This message indicates that the program attempted an invalid memory reference.``
| ``Check the program for possible errors.``

This can happen if you are trying to read data from a file into an
array, but the array is too small to hold all of the data. You can use a
debugger (such as Totalview or IDB) to try to diagnose the situation.
You may receive an error message from the debugger similar to this one:

| `` Thread received signal SEGV``
| `` stopped at [``\ \ `` for_read_seq_xmit(...) 0x40000000006b6500] ``
| `` ``
| `` Information:  An ``\ \ `` type was presented during execution of ``
| `` the previous command.  For complete type information on this symbol,``
| `` recompilation of the program will be necessary.  Consult the compiler``
| `` man pages for details on producing full symbol table information using   ``
| `` the '-g' (and '-gall' for cxx) flags.``

Usually, increasing the size of the array (i.e. until it is large enough
to contain all of the data) will fix this problem.

--`Bob Y. <User:Bmy>`__ 15:57, 22 June 2012 (EDT)

Stack overflow
~~~~~~~~~~~~~~

Finally, a segmentation fault can happen if GEOS-Chem uses up all of the
available `stack
memory <http://en.wikipedia.org/wiki/Stack-based_memory_allocation>`__
on your system. The stack memory is a special part of the memory where
short-term variables get stored.

The compiler will typically place into the stack memory all local
temporary variables, such as:

-  variables that are local to a given subroutine
-  variables that are NOT located within a ``COMMON`` block
-  variables that are NOT declared with the ``SAVE`` attribute
-  variables that are NOT declared as an ``ALLOCATABLE`` array
-  variables that are NOT declared as a ``POINTER`` variable or array

Therefore, it is important to make sure that your computational
environment is set up to use the maximum amount of stack memory. You can
do this by placing the following line in your ``.cshrc`` file:

``limit stacksize unlimited``

or ``.bashrc`` file:

`` ulimit -s unlimited``

If you encounter a ``SIGSEGV(174)`` message due to a stacksize memory
error, you may see the following error text:

| ``severe (174): SIGSEGV, possible program stack overflow occurred``
| ``Program requirements exceed current stacksize resource limit.``

--`Bob Y. <User:Bmy>`__ 15:57, 22 June 2012 (EDT)

forrtl: error (76): IOT trap signal
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Xun Jiang <mailto:xun@gps.caltech.edu>`__\ **\ wrote:**

   We met the following error message

| ``   forrtl: severe (174): SIGSEGV, segmentation fault occurred``
| ``   Stack trace terminated abnormally.``
| ``   forrtl: error (76): IOT trap signal``
| ``   Note: The error appears after``
| ``   - RDSOIL: Reading``
| ``   Data/GEOS_2x2.5/soil_NOx_200203/climatprep2x25.dat``
| ``   ### MAIN: a DAILY DATA``

   I have the following lines in ``.cshrc``

| ``   setenv KMP_STACKSIZE 329033024``
| ``   limit cputime     unlimited``
| ``   limit datasize    unlimited``
| ``   limit stacksize   unlimited``
| ``   limit filesize    unlimited``
| ``   limit memoryuse   unlimited``
| ``   limit descriptors unlimited``

   However, it still doesn't work. Any suggestion is really appreciated.

`Bob Yantosca <mailto:yantosca@seas.harvard.edu>`__\ **\ replied:**

   I found `this internet
   post <http://xtechnotes.blogspot.com/2006/01/1001-most-idiotic-error-messages.html>`__
   which has an explanation:

| ``   Cause: ``
| ``   The stack size for child threads are overflowing.  The main stack size for the program ``
| ``   is changed by the ulimit command (in Bash shell) or limit command (in C shell). ``
| ``   However this environment variable does not set the size for the child thread stack size. ``
| ``   Thus the child thread stack overflow.``
| ``   Solution:``
| ``   Set the environment variables to increase the child thread stack size.``
| ``   #for intel, using bash shell``
| ``   export KMP_STACKSIZE=500000000``
| ``   # for intel, using csh or tcsh shell``
| ``   setenv KMP_STACKSIZE 500000000``

   For more information, please see our wiki post on `Resetting the
   stack size for
   Linux <Intel_Fortran_Compiler#Resetting_stacksize_for_Linux>`__.

--`Bob Y. <User:Bmy>`__ 11:20, 26 June 2012 (EDT)

Segmentation fault encountered after TPCORE initialization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You may encounter a segmentation fault right after the following text is
printed.

``NASA-GSFC Tracer Transport Module successfully initialized``

This error usually occurs when:

#. You are running GEOS-Chem at sufficiently fine resolution, such as 2°
   x 2.5° or finer. (Many users have reported that this error does not
   occur at 4° x 5° resolution.)
#. You are using a large number of advected tracers.
#. Both #1 and #2

If you are using the `Intel Fortran
Compiler <Intel_Fortran_Compiler>`__, the cause of this error can likely
be traced to a known issue with the the ``glibc`` library. This will
cause GEOS-Chem to think that it has used up all of the available
memory, when in fact there is plenty of memory still available. However,
you may also encounter this same error even if you have compiled
GEOS-Chem with a different compiler.

You can usually correct this error by manually telling your system to
use the maximum amount of stack memory when running GEOS-Chem. For
detailed instructions, `please follow this
link <https://geos-chem.readthedocs.io/en/latest/gcc-guide/01-startup/login-env-parallel.html>`__.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 14:29, 20
September 2022 (UTC)

.. _overview-4:

Overview
========

=

The errors listed below, which occur infrequently, are related to
invalid memory operations. These can especially occur with
``POINTER``-based variables.

Bus Error
~~~~~~~~~

A bus error means that you are trying to reference memory that cannot
possibly be there. The website StackOverflow.com has a `definition of
bus error and how it differs from a segmentation
fault <http://stackoverflow.com/questions/212466/what-is-a-bus-errornice>`__.

One cause of a bus error can be if you are trying to call a subroutine
with the wrong number of arguments (i.e. usually too many arguments).

--`Bob Y. <User:Bmy>`__ 12:27, 19 October 2012 (EDT)

Dwarf subprogram entry error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The error message:

| `` Dwarf subprogram entry L_ROUTINE-NAME__LINE-NUMBER__par_loop2_2_576 has high_pc < low_pc. ``
| `` This warning will not be repeated for other occurrences.``

can occur when you try to use a pointer variable that is unassociated
(i.e. that is not currently pointing to any other variable) from within
an OpenMP parallel loop, where:

#. ``ROUTINE-NAME`` is the name of the routine where the error occurred,
   and
#. ``LINE-NUMBER`` is the line where the error occurred.

We recently discovered that this error can be caused if you have a
pointer declaration such as this:

`` TYPE(Species), POINTER :: ThisSpc => NULL()``

where the pointer ``ThisSpc`` is later used to point to another variable
from within an OpenMP parallel loop. As it turns out, the above
declaration statement will inadvertently cause pointer ``ThisSpc`` to be
declared with the ``SAVE`` attribute. This can cause a segmentation
fault, because all pointers used within an OpenMP parallel region must
be created and destroyed on the same thread.

This type of problem can usually be fixed by removing the nullification
from the declaration statement. In other words, you can rewrite the
above line of code with:

| `` TYPE(Species), POINTER :: ThisSpc``
| `` . . .``
| `` ThisSpc => NULL()``

For more information, `please see this
article <http://www.cs.rpi.edu/~szymansk/OOF90/bugs.html#4>`__.

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 19:27, 29 April
2016 (UTC)

Relocation truncated to fit
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Please see `this wiki post on our Intel Fortran Compiler
page <Intel_Fortran_Compiler#Relocation_truncated_to_fit_error>`__ which
describes how to work around an ``Relocation truncated to fit`` error
message.

--`Bob Y. <User:Bmy>`__ 10:46, 24 February 2012 (EST)

Out of memory asking for NNNNN
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is not a common error message, but it may occur if you are
compiling a version of GEOS-Chem for a high-resolution horizontal grid,
or with one of the available microphysics packages (i,e.
`APM <APM_aerosol_microphysics>`__ or
`TOMAS <TOMAS_aerosol_microphysics>`__). Please see `this wiki post on
our Intel Fortran Compiler
page <Intel_Fortran_Compiler#Out_of_memory_asking_for_NNNNN>`__ which
describes this error in detail.

--`Bob Y. <User:Bmy>`__ 10:42, 26 July 2013 (EDT)

Memory error: "munmap_chunk: invalid pointer"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following error is not common but can happen:

+-----------------------------------+-----------------------------------+
| Error                             | ``*** glibc detected *** ./geos:  |
|                                   | munmap_chunk(): invalid pointer:  |
|                                   | 0x00000000059aac30 ***``          |
+===================================+===================================+
| Reference                         | http://stackoverflow.com/question |
|                                   | s/6199729/how-to-solve-munmap-chu |
|                                   | nk-invalid-pointer-error-in-c     |
+-----------------------------------+-----------------------------------+
| Explanation                       | This happens when the pointer     |
|                                   | passed to (C-library language     |
|                                   | routine ``free()``, which is      |
|                                   | called from Fortran routine       |
|                                   | ``NULLIFY()``) is not valid or    |
|                                   | has been modified somehow. I      |
|                                   | don't really know the details     |
|                                   | here. The bottom line is that the |
|                                   | pointer passed to free() must be  |
|                                   | the same as returned by           |
|                                   | (C-library routines) malloc(),    |
|                                   | realloc() and their friends.      |
|                                   |                                   |
|                                   | | ``The free() function frees the |
|                                   |  memory space pointed  to  by  pt |
|                                   | r,  which``                       |
|                                   | | ``must  have  been  returned  b |
|                                   | y a previous call to malloc(), ca |
|                                   | lloc() or``                       |
|                                   | | ``realloc().  Otherwise, or if  |
|                                   | free(ptr) has already been called |
|                                   |   before,``                       |
|                                   | | ``undefined behavior occurs.  I |
|                                   | f ptr is NULL, no operation is pe |
|                                   | rformed.``                        |
|                                   | | ``GNU                           |
|                                   |      2012-05-10                   |
|                                   |        MALLOC(3)``                |
+-----------------------------------+-----------------------------------+
| Simpler explanation               | This can happen if you are trying |
|                                   | to deallocate or nullify a        |
|                                   | pointer variable that has already |
|                                   | been deallocated or modified.     |
+-----------------------------------+-----------------------------------+

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 21:32, 6
January 2017 (UTC)

Memory error: "free: invalid size"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following error is not common but can happen:

+-----------------------------------+-----------------------------------+
| Error                             | :literal:`\*** Error in `./geos': |
|                                   |  free(): invalid size: 0x00000000 |
|                                   | 0662e090 **\*`                    |
+===================================+===================================+
| Reference                         | http://stackoverflow.com/question |
|                                   | s/4729395/error-free-invalid-next |
|                                   | -size-fast                        |
+-----------------------------------+-----------------------------------+
| Explanation                       | It means that you have a memory   |
|                                   | error. You may be trying to free  |
|                                   | a pointer that wasn't allocated   |
|                                   | (or delete an object that wasn't  |
|                                   | created) or you may be trying to  |
|                                   | nullify/delete such an object     |
|                                   | more than once. You may be        |
|                                   | overflowing a buffer or otherwise |
|                                   | writing to memory to which you    |
|                                   | shouldn't be writing, causing     |
|                                   | heap corruption.                  |
|                                   |                                   |
|                                   | Any number of programming errors  |
|                                   | can cause this problem. You need  |
|                                   | to use a debugger, get a          |
|                                   | backtrace, and see what your      |
|                                   | program is doing when the error   |
|                                   | occurs. If that fails and you     |
|                                   | determine you have corrupted the  |
|                                   | heap at some previous point in    |
|                                   | time, you may be in for some      |
|                                   | painful debugging (it may not be  |
|                                   | too painful if the project is     |
|                                   | small enough that you can tackle  |
|                                   | it piece by piece).               |
+-----------------------------------+-----------------------------------+

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 21:32, 6
January 2017 (UTC)

Memory error: "double free or corruption"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following error is not common, but can occur under some
circumstances:

+-----------------------------------+-----------------------------------+
| Error                             | ``*** glibc detected *** ./geos:  |
|                                   | double free or corruption (out):` |
|                                   | `                                 |
+===================================+===================================+
| Reference                         | http://stackoverflow.com/question |
|                                   | s/2902064/how-to-track-down-a-dou |
|                                   | ble-free-or-corruption-error-in-c |
|                                   | -with-gdb                         |
+-----------------------------------+-----------------------------------+
| Explanation                       | There are at least two possible   |
|                                   | situations:                       |
|                                   |                                   |
|                                   | #. You are deleting the same      |
|                                   |    entity twice                   |
|                                   | #. You are deleting something     |
|                                   |    that wasn't allocated (or that |
|                                   |    has since been deallocated)    |
|                                   |                                   |
|                                   | For the first one I strongly      |
|                                   | suggest NULL-ing all deleted      |
|                                   | pointers.                         |
|                                   |                                   |
|                                   | You have [some] options:          |
|                                   |                                   |
|                                   | #. Overload new and delete and    |
|                                   |    track the allocations          |
|                                   | #. Use a debugger -- then you'll  |
|                                   |    get a backtrace from your      |
|                                   |    crash, and that'll probably be |
|                                   |    very helpful                   |
|                                   |                                   |
|                                   | Three basic rules:                |
|                                   |                                   |
|                                   | #. Set pointer to NULL after free |
|                                   | #. Check for NULL before freeing. |
|                                   | #. Initialize pointer to NULL in  |
|                                   |    the start.                     |
|                                   |                                   |
|                                   | Combination of these three works  |
|                                   | quite well.                       |
|                                   |                                   |
|                                   | This error can also occur if a    |
|                                   | library that GEOS-Chem needs      |
|                                   | (e.g. netCDF or netCDF-Fortran)   |
|                                   | is not installed on your system.  |
|                                   | GEOS-Chem will try to make        |
|                                   | function calls to the missing     |
|                                   | library, which will result in     |
|                                   | this error. In this case, the     |
|                                   | solution is to install the        |
|                                   | missing library.                  |
+-----------------------------------+-----------------------------------+

--`Bob Yantosca <User:Bmy>`__ (`talk <User_talk:Bmy>`__) 21:02, 10 June
2019 (UTC)
