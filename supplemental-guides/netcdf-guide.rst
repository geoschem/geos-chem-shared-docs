.. _ncguide:

#########################
Working with netCDF files
#########################

On this page we provide some useful information about working with data
files in netCDF format.

.. _ncguide-useful-tools:

============
Useful tools
============


There are many free and open-source software packages readily available
for visualizing and manipulating netCDF files.

.. option:: ncdump

   Gemerates a text representation of netCDF  data and can be used to
   quickly view the variables contained in a netCDF file.
   :program:`ncdump` is installed to the :file:`bin/` folder of your
   netCDF library distribution.

   See: https://www.unidata.ucar.edu/software/netcdf/workshops/2011/utilities/Ncdump.html

.. option:: ncview

   Visualization package for netCDF files. :program:`Ncview` has limited
   features, but is great for a quick look at the contents of netCDF
   files.

   See: http://meteora.ucsd.edu/~pierce/ncview_home_page.html

.. option:: Panoply

   Data viewer for netCDF files.  This package offers an alternative
   to ncview. From our experience, Panoply works nicely when installed
   on the desktop, but is slow to respond in the Linux environment.

   See: https://www.giss.nasa.gov/tools/panoply/

.. option:: nco and cdo

   Command-line tools for manipulating and analyzing netCDF files.
   Useful for renaming variables, attributes, and for regridding.

   See: http://nco.sourceforge.net and https://code.zmaw.de/projects/cdo

.. option:: xarray

   Python package that lets you read the contents of a netCDF file
   into a data structure.  The data can then be further manipulated or
   converted to numpy or dask arrays for further procesing.

   See: https://xarray.readthedocs.io

.. option:: GCPy

   Python package for visualizing and analyzing GEOS-Chem output.
   Used for creating the GEOS-Chem benchmark plots.  Also contains
   some useful routines for creating single-panel plots and
   multi-panel difference plots.

   See: https://gcpy.readthedocs.io

Some of the tools listed above, such as **ncdump** and **ncview**, may
come pre-installed on your system. Others may need to be installed or
loaded (e.g. via the :command:`module load` command). Check with your system
administrator or IT staff to see what is available on your system.

.. _ncguide-bpch-to-nc:

===================================================
Converting files from binary punch format to netCDF
===================================================

Older GEOS-Chem versions used a file format known as **binary punch
format** (or **bpch** for short) which was written as Fortran unformatted
data with some identifying metadata.  These files could be read with
the now-unsupported :program:`GAMAP` package (written in the IDL language).

If you are working with binary punch data files from older GEOS-Chem
versions, or from the GEOS-Chem Classic adjoint model (which is based
on, then you have a couple of options for converting these to netCDF
format.

.. _ncguide-bpch-to-nc-w-python:

Using Python
------------

Perhaps the simplest way to create a netCDF file from a bpch file is
to use the `xbpch <https://xbpch.readthedocs.io/en/latest/>`__ and
`xarray <http://xarray.pydata.org/en/stable/>`__ Python packages. (If
you would like to change the variable names, then you will also need
our `gcpy <https://github.com/geoschem/gcpy>`__ package.) This can be
done in only a few lines of Python! Please see our example script
`bpch2nc.py <https://github.com/geoschem/gcpy/blob/main/examples/bpch_to_nc/bpch2nc.py>`_.

.. _ncguide-bpch-to-nc-w-idl:

Using IDL
---------

You can use the `GAMAP routine :program:`bpch2coards` to create netCDF
files from a `GEOS-Chem binary punch file
<http://acmg.seas.harvard.edu/gamap/doc/Chapter_6.html#6.2>`__. For
example, start IDL and then type this command at the IDL prompt:

.. code-block:: console

   IDL> bpch2coards, 'uvalbedo.geos.2x25', 'uvalbedo.geos.2x25.%DATE%.nc'

will create the following netCDF files:

.. code-block:: console

   uvalbedo.geos.2x25.19850101.nc
   uvalbedo.geos.2x25.19850201.nc
   uvalbedo.geos.2x25.19850301.nc
   uvalbedo.geos.2x25.19850401.nc
   uvalbedo.geos.2x25.19850501.nc
   uvalbedo.geos.2x25.19850601.nc
   uvalbedo.geos.2x25.19850701.nc
   uvalbedo.geos.2x25.19850801.nc
   uvalbedo.geos.2x25.19850901.nc
   uvalbedo.geos.2x25.19851001.nc
   uvalbedo.geos.2x25.19851101.nc
   uvalbedo.geos.2x25.19851201.nc

Note that :program:`bpch2coards` will create a new file for each time
slice. The :code:`%DATE%` token in the output file name will be
replaced with the year-month-day value for each time stamp. In the
above example, the binary punch file :file:`uvalbedo.geos.2x25`
contains monthly data, therefore :program:`bpch2coards` will create 12
individual netCDF files.

.. note::

   You might sometimes have better luck using the :program:`bpch_sep`
   routine to split the bpch files into smaller bpch files (e.g. one
   per month) band then using :program:`bpch2coards` on the smaller
   files.

   **Special note for timeseries data:** To use :program:`bpch2coards` to
   convert timeseries (e.g. hourly, 3-hourly, etc) data to netCDF
   format, add the :code:`%TIME%` token to the netCDF file name. For example:

.. code-block:: console

   IDL> bpch2coards, 'timeseries.geos.2x25', 'timeseries.geos.2x25.%DATE%.%TIME%.nc'

This will create one new netCDF file for each timestamp in the bpch
file. See :ref:`ncguide-concat-files` for instructions on how you can
concatenate these into a single netCDF file.

.. _ncguide-bpch-to-nc-edit-attrs:

Further Edit variable names and attributes
------------------------------------------

Whether you use Python or IDL to create a netCDF file from a bpch file,
you will still need to edit the variable attributes in order to make the
file COARDS-compliant (cf.:ref:`ncguide-edit-vars-attrs`).

.. _ncguide-examine-contents:

=======================================
Examining the contents of a netCDF file
=======================================

An easy way to examine the contents of a netCDF file is to use this
command:

.. code-block:: console

   ncdump -cts EMEP.geos.1x1

You will see output similar to this:

.. code-block:: console

   netcdf EMEP.geos.1x1 {
   dimensions:
           lon = 360 ;
           lat = 181 ;
           time = UNLIMITED ; // (17 currently)
   variables:
           float lon(lon) ;
                   lon:standard_name = "longitude" ;
                   lon:long_name = "Longitude" ;
                   lon:units = "degrees_east" ;
                   lon:axis = "X" ;
                   lon:_Storage = "chunked" ;
                   lon:_ChunkSizes = 360 ;
                   lon:_DeflateLevel = 1 ;
           float lat(lat) ;
                   lat:standard_name = "latitude" ;
                   lat:long_name = "Latitude" ;
                   lat:units = "degrees_north" ;
                   lat:axis = "Y" ;
                   lat:_Storage = "chunked" ;
                   lat:_ChunkSizes = 181 ;
                   lat:_DeflateLevel = 1 ;
           double time(time) ;
                   time:standard_name = "time" ;
                   time:units = "hours since 1985-01-01 00:00:00" ;
                   time:calendar = "standard" ;
                   time:_Storage = "chunked" ;
                   time:_ChunkSizes = 524288 ;
                   time:_DeflateLevel = 1 ;
           float PRPE(time, lat, lon) ;
                   PRPE:long_name = "Propene" ;
                   PRPE:units = "kgC/m2/s" ;
                   PRPE:gamap_category = "ANTHSRCE" ;
                   PRPE:_Storage = "chunked" ;
                   PRPE:_ChunkSizes = 1, 181, 360 ;
                   PRPE:_DeflateLevel = 1 ;
           float ALK4(time, lat, lon) ;
                   ALK4:long_name = "Alkanes(>C4)" ;
                   ALK4:units = "kgC/m2/s" ;
                   ALK4:gamap_category = "ANTHSRCE" ;
                   ALK4:_Storage = "chunked" ;
                   ALK4:_ChunkSizes = 1, 181, 360 ;
                   ALK4:_DeflateLevel = 1 ;
           ... etc ...
   // global attributes:
                   :CDI = "Climate Data Interface version 1.5.5 (http://code.zmaw.de/projects/cdi)" ;
                   :Conventions = "COARDS" ;
                   :history = "Wed Apr 23 17:36:28 2014: cdo mulc,10000 tmptmp.nc EMEP.geos.1x1.nc\n",
                   :Title = "COARDS/netCDF file created by BPCH2COARDS (GAMAP v2-03+)" ;
                   :Model = "GEOS3" ;
                   :Grid = "GEOS_1x1" ;
                   :Delta_Lon = 1.f ;
                   :Delta_Lat = 1.f ;
                   :NLayers = 48 ;
                   :Start_Date = 19800101 ;
                   :Start_Time = 0 ;
                   :End_Date = 19810101 ;
                   :End_Time = 0 ;
                   :Delta_Time = 240000 ;
                   :Temp_Res = "CONSTANT" ;
                   :CDO = "Climate Data Operators version 1.5.5 (http://code.zmaw.de/projects/cdo)" ;
   data:
    lon = 180.5, 181.5, 182.5 ... etc... ;
    lat = -89.75, -89, -88, -87 ... etc ... ;
    time = "1980-01-01", "1985-01-01", "1986-01-01", "1987-01-01", "1988-01-01",
       "1989-01-01", "1990-01-01", "1991-01-01", "1992-01-01", "1993-01-01",
       "1994-01-01", "1995-01-01", "1996-01-01", "1997-01-01", "1998-01-01",
       "1999-01-01", "2000-01-01" ;
   }

You can also use ncdump to display the data values for a given variable
in the netCDF file. This command will display the values in the
SpeciesRst_NO variable to the screen:

.. code-block:: console

   ncdump -v SpeciesRst_NO GEOSChem_restart.20160701_0000z.nc4 | less

Or you can redirect the output to a file:

.. code-block:: console

   ncdump -v SpeciesRst_NO GEOSChem_restart.20160701_0000z.nc4

.. _ncguide-reading-files:

=====================================
Reading the contents of a netCDF file
=====================================

.. _ncguide-reading-w-python:

Reading data in Python
----------------------

The easiest way to read a netCDF file is to use the `xarray Python
package <https://xarray.readthedocs.io>`_.

.. code-block::  python

   #!/usr/bin/env python

   # Imports
   import numpy as np
   import xarray as xr

   # Read a restart file into an xarray Dataset object
   ds = xr.open_dataset("GEOSChem.Restart.20160101_0000z.nc4")

   # Print the contents of the DataSet
   print(ds)

   # Print the units of the SpeciesRst_O3 field
   print(ds["SpeciesRst_O3"].units)

   # Convert the SpeciesRst_O3 (O3 concentration) to
   # a numpy array so that we can take the sum
   O3_values = ds["SpeciesRst_O3"].values

   # Take the sum of SpeciesRst_O3
   sum_O3 = np.sum(O3_values)
   print("Sum of SpeciesRst_O3: {}".format(sum_O3))
   ... etc ...

This above script will print the following output:

.. code-block:: console

   <xarray.Dataset>
   Dimensions:              (lat: 46, lev: 72, lon: 72, time: 1)
   Coordinates:
     * lon                  (lon) float64 -180.0 -175.0 -170.0 -165.0 -160.0 ...
     * lat                  (lat) float64 -89.0 -86.0 -82.0 -78.0 -74.0 -70.0 ...
     * lev                  (lev) float64 1.0 2.0 3.0 4.0 5.0 6.0 7.0 8.0 9.0 ...
     * time                 (time) datetime64[ns] 2016-07-01
   Data variables:
       AREA                 (lat, lon) float64 ...
       SpeciesRst_RCOOH     (time, lev, lat, lon) float32 ...
       SpeciesRst_O2        (time, lev, lat, lon) float32 ...
       ... etc...
       SpeciesRst_O3        (time, lev, lat, lon) float32 ...
       SpeciesRst_NO        (time, lev, lat, lon) float32 ...
   Attributes:
       title:        GEOSChem  restart
       history:      Created by routine NC_CREATE (in ncdf_mod.F90)
       format:       NetCDF-4
       conventions:  COARDS
   Units of SpeciesRst_O3: mol/mol
   Sum of SpeciesRst_O3: 0.40381380915641785

.. _ncguide-reading-multiple-files-w-python:

Reading data from multiple files in Python
------------------------------------------

The xarray package will also let you read data from multiple files into
a single Dataset object. This is done with the open_mfdataset (open
multi-file-dataset) function as shown below:

.. code-block:: python

   #!/usr/bin/env python

   # Imports
   import xarray as xr

   # Create a list of files to open
   filelist = ['GEOSChem.SpeciesConc.20160101_0000z.nc4', 'GEOSChem.SpeciesConc_20160201_0000z.nc4', ...]

   # Read a restart file into an xarray Dataset object
   ds = xr.open_mfdataset(filelist)

.. _ncguide-coards-compliant:

================================================
Determining if a netCDF file is COARDS-compliant
================================================

Please see `The COARDS conventions for earth science
data
<The_COARDS_netCDF_conventions_for_earth_science_data#Determining_if_a_netCDF_file_is_COARDS-compliant>`_
on the GEOS-Chem wiki.

.. _ncguide-edit-vars-attrs:

==================================
Edit variable names and attributes
==================================

If you have obtained a netCDF file from a data archive (or have
:ref:`converted data in bpch format to netCDF <ncguide-bpch-to-nc>`,
you will probably have to further edit certain attributes and variable
names in order to make your file COARDS-compliant. You can use `the isCoards
script
<https://github.com/geoschem/geos-chem/blob/main/NcdfUtil/perl/isCoards>`_
to determine which elements of your netCDF file need to be edited.

**Christoph Keller** has provided these several useful commands for editing
netCDF files.

#. Display the header and coordinate variables of a netCDF file, with
   the time variable dipslayed in human-readable format:

   .. code-block:: console

      ncdump -cts file.nc

#. Compress a netCDF file.  This can considerably reduce the file
   size! (cf. :ref:`ncguide-chunk-deflate`)

   .. code-block:: console

      # No deflation
      nccopy -d0 in.nc out.nc
      mv out.nc in.nc

      # Minimum deflation (good for most applications)
      nccopy -d1 in.nc out.nc
      mv out.nc in.nc

      # Medium deflation
      nccopy -d5 in.nc out.nc
      mv out.nc in.nc

   # Maximum deflation
   nccopy -d9 in.nc out.nc
   mv out.nc in.nc

#. Change variable name from :code:`SpeciesConc_NO` to :code:`NO`

   .. code-block:: console

      ncrename -v SpeciesConc_NO,NO file.nc

#. Change the timestamp in the file from 1 Jan 1985 to 1 Jan 2000

   .. code-block:: console

      cdo settime,2000-01-01 in.nc out.nc
      mv out.nc in.nc

#. Set all missing values to zero:

   .. code-block:: console

      cdo setemisstoc,0 in.nc out.nc
      mv out.nc in.nc

#. Add/change the long-name attribute of the vertical coordinates
   (lev) to "GEOS-Chem levels".  This will ensure that `HEMCO
   <https://hemco.readthedocs.io>`_ recognizes the vertical levels of
   the input file as GEOS-Chem model levels.

   .. code-block:: console

      ncatted -a long_name,lev,o,c,"GEOS-Chem levels" file.nc

#. Add/change the axis and positive attributes to the vertical
   coordinate (lev):

   .. code-block:: console

      ncatted -a axis,lev,o,c,"Z" file.nc
      ncatted -a positive,lev,o,c,"up" file.nc

#. Add/change the :code:`units` attribute of the latitude (lat) coordinate to
   :code:`degrees_north`:

   .. code-block:: console

      ncatted -a units,lat,o,c,"degrees_north" file.nc

#. Add/change the :code:`references`, :code:`title`, and
   :code:`history` global attributes

   .. code-block:: console

      ncatted -a references,global,o,c,"www.geos-chem.org; wiki.geos-chem.org" file.nc
      ncatted -a history,global,o,c,"Tue Mar  3 12:18:38 EST 2015" file.nc
      ncatted -a title,global,o,c,"XYZ data from ABC source" file.nc

#. Remove the :code:`references` global attribute:

   .. code-block:: console

      ncatted -a references,global,d,, file.nc

#. Add a :code:`time` dimension to a file with a missing time dimension

   .. code-block:: console

      ncap2 -h -s 'defdim(“time”,1);time[time]=0.0;time@long_name=“time”;time@calendar=“standard”;time@units=“days since 2007-01-01 00:00:00”' -O in.nc out.nc
      mv out.nc in.nc

#. Convert the :code:`units` attribute of the CHLA variable from
   :code:`mg/m3` to :code:`kg/m3`

   .. code-block:: console

       ncap2 -v -s "CHLA=CHLA/1000000.0f" in.nc out.nc
       ncatted -a units,CHLA,o,c,"kg/m3" out.nc
       mv out.nc in.nc

.. _ncguide-concat-files:

==========================
Concatenating netCDF files
==========================

There are a couple of ways to concatenate multiple netCDF files into a
single netCDF file, as shown in the sections below.

.. _ncguide-concat-nco:

Concatenating with the netCDF operators
---------------------------------------

You can use the ncrcat commmand of the `netCDF Operators
(nco) <http://research.jisao.washington.edu/data_sets/nco/>`__ to
concatenate the 12 individual files created by :program:`bpch2coards`
into a single netCDF file. Make sure you have exited IDL, and then type the
following command at the Unix prompt:

.. code-block:: console

   ncrcat -hO uvalbedo.geos.2x25.1985*.nc uvalbedo.geos.2x25.nc

You can then discard the :file:`uvalbedo.geos.2x25.1985*.nc` files that were
created directly by IDL :program:`bpch2coards`,

.. _ncguide-concat-python:

Concatenating with Python
-------------------------

You can use the `xarray <http://xarray.pydata.org/en/stable/>`__
Python package to create a single netCDF file from multiple files. `Click
HERE
<https://github.com/geoschem/gcpy/blob/main/examples/working_with_files/concatenate_files.py>`__ to view a sample Python script that does this.

.. _ncguide-regridding:

=======================
Regridding netCDF files
=======================

The following tools can be used to regrid netCDF data files (such as
GEOS-Chem restart files and GEOS-Chem diagnostic files.

.. _ncguide-regrid-cdo:

Regridding with cdo
-------------------
The Climate Data Operators include tools for regridding netCDF
files. For example:

   .. code-block:: console

      # Apply conservative regridding
      cdo remapcon,gridfile infile.nc outfile.nc

For :file:`gridfile`, you can use the files `here
<https://geoschemdata.wustl.edu/ExtData/HEMCO/grids/>`_.  Also see
`this reference
<http://www.climate-cryosphere.org/wiki/index.php?title=Regridding_with_CDO%7Cthis>`_.

.. _ncguide-regrid-cdo-issue:

Issue with CDO remapdis regridding tool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GEOS-Chem user **Bram Maasakkers** wrote:

   I have noticed a problem regridding GEOS-Chem diagnostic file to
   2x2.5 using :program:`cdo` version 1.9.4. When I use:

   .. code-block:: console

      cdo remapdis,geos.2x25.grid GEOSChem.Restart.4x5.nc GEOSChem.Restart.2x25.nc

   The last latitudinal band (-89.5) remains empty and gets filled with
   the standard missing value of cdo, which is really large. This leads
   to immediate problems in the methane simulation as enormous
   concentrations enter the domain from the South Pole. For now I’ve
   solved this problem by just using bicubic interpolation

   .. code-block:: console

      cdo remapbic,geos.2x25.grid GEOSChem.Restart.4x5.nc GEOSChem.Restart.2x25.nc

You can also use conservative regridding:

.. code-block:: console

   cdo remapcon,geos.2x25.grid GEOSChem.Restart.4x5.nc GEOSChem.Restart.2x25.nc

.. _ncguide-regrid-nco:

Regridding with nco
-------------------
The netCDF Operators also include tools for regridding. See the
`Regridding section of the NCO User Guide
<http://nco.sourceforge.net/nco.html#Regridding>`_ for more information.

.. _ncguide-regrid-xesmf:

Regridding with xESMF
---------------------

`xESMF <https://xesmf.readthedocs.io>`_ is a universal regridding tool
for geospatial data, which is written in Python. It can be used to
regrid data not only on cartesian grids, but also on cubed-sphere and
unstructured grids.

.. note::

   :program:`xESMF` only handles horizontal regridding.

.. _ncguide-regrid-xarray:

Regridding with xarray
----------------------

The `xarray <https://xarray.readthedocs.io>`_ Python package has a
built-in capability for 1-D interpolation. It wraps the `SciPy
interpolation module
<https://docs.scipy.org/doc/scipy/reference/interpolate.html>`_. This
functionality can also be used for vertical regridding.

.. _ncguide-cropping:

=====================
Cropping netCDF files
=====================

If needed, regrid a coarse netCDF file (such as a restart file) can be
cropped to a subset of the globe with the :program:`nco` or
:program:`cdo` utilities (cf. :ref:`ncguide-useful-tools`). 

For example, :program:`cdo` has a :program:`SELBOX` operator for
selecting a box by specifying the lat/lon bounds:

.. code-block:: console

   cdo sellonlatbox,lon1,lon2,lat1,lat2 in.nc out.nc
   mv out.nc in.nc

See page 44 of the `CDO
guide <https://code.zmaw.de/projects/cdo/embedded/cdo.pdf>`__ for more
information.

.. _ncguide-adding-new-var:

======================================
Adding a new variable to a netCDF file
======================================

You have a couple of options for adding a new variable to a netCDF file
(for example, when having to add a new species to an existing GEOS-Chem
restart file).

#. You can use :program:`cdo` and :program:`*nco` to copy the the
   data from one variable to another variable. For example:

   .. code-block:: bash

      # Extract field SpeciesRst_PMN from the original restart file
      cdo selvar,SpeciesRst_PMN initial_GEOSChem_rst.4x5_standard.nc NPMN.nc4

      # Rename selected field to SpeciesRst_NPMN
      ncrename -h -v SpeciesRst_PMN,Species_Rst_NPMN NMPN.nc4

      # Append new species to existing restart file
      ncks -h -A -M NMPN.nc4 initial_GEOSChem_rst.4x5_standard.nc

#. **Sal Farina** wrote a simple Python script for adding a new
   species to a netCDF restart file:

   .. code-block:: python

      #!/usr/bin/env python

      import netCDF4 as nc
      import sys
      import os

      for nam in sys.argv[1:]:
          f = nc.Dataset(nam,mode='a')
          try:
              o = f['SpeciesRst_OCPI']
          except:
              print "SpeciesRst_OCPI not defined"
          f.createVariable('SpeciesRst_SOAP',o.datatype,dimensions=o.dimensions,fill_value=o._FillValue)
          soap = f['SpeciesRst_SOAP']
          soap[:] = 0.0
          soap.long_name= 'SOAP species'
          soap.units =  o.units
          soap.add_offset = 0.0
          soap.scale_factor = 1.0
          soap.missing_value = 1.0e30
          f.close()

#. Bob Yantosca wrote this Python script to insert a fake species into
   GEOS-Chem Classic and GCHP restart files (13.3.0)

   .. code-block:: python

      #!/usr/bin/env python
      """
      Adds an extra DataArray for into restart files:
      Calling sequence:
          ./append_species_into_restart.py
      """
      # Imports
      import gcpy.constants as gcon
      import xarray as xr
      from xarray.coding.variables import SerializationWarning
      import warnings

      # Suppress harmless run-time warnings (mostly about underflow or NaNs)
      warnings.filterwarnings("ignore", category=RuntimeWarning)
      warnings.filterwarnings("ignore", category=UserWarning)
      warnings.filterwarnings("ignore", category=SerializationWarning)

      def main():
          """
          Appends extra species to restart files.
          """
          # Data vars to skip
          skip_vars = gcon.skip_these_vars
          # List of dates
          file_list = [
              'GEOSChem.Restart.fullchem.20190101_0000z.nc4',
              'GEOSChem.Restart.fullchem.20190701_0000z.nc4',
              'GEOSChem.Restart.TOMAS15.20190701_0000z.nc4',
              'GEOSChem.Restart.TOMAS40.20190701_0000z.nc4',
              'GCHP.Restart.fullchem.20190101_0000z.c180.nc4',
              'GCHP.Restart.fullchem.20190101_0000z.c24.nc4',
              'GCHP.Restart.fullchem.20190101_0000z.c360.nc4',
              'GCHP.Restart.fullchem.20190101_0000z.c48.nc4',
              'GCHP.Restart.fullchem.20190101_0000z.c90.nc4',
              'GCHP.Restart.fullchem.20190701_0000z.c180.nc4',
              'GCHP.Restart.fullchem.20190701_0000z.c24.nc4',
              'GCHP.Restart.fullchem.20190701_0000z.c360.nc4',
              'GCHP.Restart.fullchem.20190701_0000z.c48.nc4',
              'GCHP.Restart.fullchem.20190701_0000z.c90.nc4'
          ]
          # Keep all netCDF attributes
          with xr.set_options(keep_attrs=True):
              # Loop over dates
              for f in file_list:
                  # Input and output files
                  infile = '../' + f
                  outfile = f
                  print("Creating " + outfile)

                  # Open input file
                  ds = xr.open_dataset(infile, drop_variables=skip_vars)
                  # Create a new DataArray from a given species (EDIT ACCORDINGLY)
                  if "GCHP" in infile:
                      dr = ds["SPC_ETO"]
                      dr.name = "SPC_ETOO"
                  else:
                      dr = ds["SpeciesRst_ETO"]
                      dr.name = "SpeciesRst_ETOO"

                  # Update attributes (EDIT ACCORDINGLY)
                  dr.attrs["FullName"] = "peroxy radical from ethene"
                  dr.attrs["Is_Gas"] = "true"
                  dr.attrs["long_name"] = "Dry mixing ratio of species ETOO"
                  dr.attrs["MW_g"] = 77.06
                  # Merge the new DataArray into the Dataset
                  ds = xr.merge([ds, dr], compat="override")

                  # Create a new file
                  ds.to_netcdf(outfile)

                  # Free memory by setting ds to a null dataset
                  ds = xr.Dataset()

      if __name__ == "__main__":
          main()

.. _ncguide-chunk-deflate:

===================================================
Chunking and deflating a netCDF file to improve I/O
===================================================

We recommend that you **chunk** the data in your netCDF file. Chunking
specifies the order in along which the data will be read from
disk. The Unidata web site has `a good overview of why chunking a
netCDF file matters
<https://www.unidata.ucar.edu/blogs/developer/entry/chunking_data_why_it_matters>`_.

For `GEOS-Chem with the high-performance option (aka GCHP)
<https://gchp.readthedocs.io>`_, the best file I/O performance occurs
when the file is split into one chunk per level (assuming your data
has a lev dimension). This allows each individual vertical level of
data to be read in parallel.

You can use the :command:`nccopy` command of :option:`nco` to do the
chunking. For example, say you have a netCDF file called
:file:`myfile.nc` with these dimensions:

.. code-block:: console

   dimensions:
           time = UNLIMITED ; // (12 currently)
           lev = 72 ;
           lat = 181 ;
           lon = 360 ;

Then you can issue this command to apply the optimal chunking along
levels:

.. code-block:: console

   nccopy -c lon/360,lat/181,lev/1,time/1\ -d1 myfile.nc tmp.nc
    mv tmp.nc myfile.nc

This will create a new file called :file:`tmp.nc` that has the proper
chunking. We then replace :file:`myfile.nc` with this temporary file.

You can specify the chunk sizes that will be applied to the variables
in the netCDF file with the :command:`-c`  argument to
:command:`nccopy`. To obtain the optimal chunking, the :code:`lon` chunksize
must be identical to the number of values along the longitude
dimension (e.g. :code:`lon/360` and the :code:`lat` chunksize must be
equal to the number of points in the latitude dimension
(e.g. :code:`lat/181`).

We also recommend that you :command:`deflate` (i.e. compress) the
netCDF data variables at the same time you apply the
chunking. Deflating can substantially reduce the file size, especially
for emissions data that are only defined over the land but not over
the oceans. You can deflate the data in a netCDF file by specifying
the \ -d\  argumetnt to nccopy. There are 10 possible deflation
levels, ranging from 0 (no deflation) to 9 (max deflation). For most
purposes, a deflation level of 1 (:command:`d1`) is sufficient.

The `GEOS-Chem Support Team
<https://wiki.geos-chem.org/GEOS-Chem_Support_Team>`_ has created a
script named :file:`nc_chunk.pl` that will automatically chunk and
compress data for you. You may obtain this script from our
:program:`NcdfUtilities` repository. We also recommend that you copy
:program:`nc_chunk.pl` into a folder that is in your search path (such
as :file:`~/bin`) so that it will be available to you in whatever
directory you are working in.

.. code-block:: console

   git clone https://github.com/geoschem/ncdfutil NcdfUtil
   cp NcdfUtil/perl/nc_chunk.pl ~/bin

To use the script, type:

.. code-block:: console

   nc_chunk.pl myfile.nc    # Chunk netCDF file
   nc_chunk.pl myfile.nc 1  # Chunk and compress file using deflate level 1

You can use the :command:`ncdump -cts myfile.nc` command to view the chunk size
and deflation level in the file. After applying the chunking and
compression to myfile.nc, you would see output such as this:

.. code-block:: console

    dimensions:
            time = UNLIMITED ; // (12 currently)
            lev = 72 ;
            lat = 181 ;
            lon = 360 ;
    variables:
            float PRPE(time, lev, lat, lon) ;
                    PRPE:long_name = "Propene" ;
                    PRPE:units = "kgC/m2/s" ;
                    PRPE:add_offset = 0.f ;
                    PRPE:scale_factor = 1.f ;
                    PRPE:_FillValue = 1.e+15f ;
                    PRPE:missing_value = 1.e+15f ;
                    PRPE:gamap_category = "ANTHSRCE" ;
                    PRPE:_Storage = "chunked" ;
                    PRPE:_ChunkSizes = 1, 1, 181, 360 ;
                    PRPE:_DeflateLevel = 1 ;
                    PRPE:_Endianness = "little" ;\
            float CO(time, lev, lat, lon) ;
                    CO:long_name = "CO" ;
                    CO:units = "kg/m2/s" ;
                    CO:add_offset = 0.f ;
                    CO:scale_factor = 1.f ;
                    CO:_FillValue = 1.e+15f ;
                    CO:missing_value = 1.e+15f ;
                    CO:gamap_category = "ANTHSRCE" ;
                    CO:_Storage = "chunked" ;
                    CO:_ChunkSizes = 1, 1, 181, 360 ;
                    CO:_DeflateLevel = 1 ;
                    CO:_Endianness = "little" ;\

The attributes that begin with a :code:`_` character are "hidden"
netCDF attributes. They represent file properties instead of
user-defined properties (like the long name, units, etc.). The
"hidden" attributes can be shown by adding the :command:`-s` argument
to :command:`ncdump`.
