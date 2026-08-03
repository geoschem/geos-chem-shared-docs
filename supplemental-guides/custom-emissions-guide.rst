.. |br| raw:: html

   <br />

.. _custom-emis-guide:

##############################
Customize emissions with HEMCO
##############################

In this Guide, we will walk you through the process of adding **your
own emissions file** (a new regional inventory, a replacement for an
existing species, a locally-produced dataset, etc.) to the `Base Emissions
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#base-emissions>`_
section of :file:`HEMCO_Config.rc`.

.. attention::

   If the emissions inventory that you wish to add depends on external
   meteorology, you will need to implement it
   as a new `HEMCO Extension
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/extensions.html>`_.
   This is a customized Fortran module that computes emissions using
   meteorology inputs supplied from HEMCO.

For the full, authoritative reference on every :file:`HEMCO_Config.rc`
attribute, see the `HEMCO configuration file reference guide
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html>`_
and the `GEOS-Chem summary of it
<https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/hemco-config.html>`_.
This Guide does not replace that reference, but rather walks through
the specific decisions and mistakes that come up most often when
adding a new emissions field.

.. _custom-emis-anatomy:

=================================
Anatomy of a Base Emissions entry
=================================

Every entry under the :literal:`BASE EMISSIONS` section of
:file:`HEMCO_Config.rc` (see :ref:`cfg-hco-base`) has the same eleven
columns:

.. code-block:: kconfig

   # ExtNr Name sourceFile sourceVar sourceTime C/R/E SrcDim SrcUnit Species ScalIDs Cat Hier

   0 CEDS_NO_ENE  $ROOT/CEDS/v2024-06/$YYYY/CEDS_NO_0.1x0.1_$YYYY.nc  NO_ene  1980-2019/1-12/1/0 C xyL* kg/m2/s NO  2401/706/315  1 5

.. list-table::
   :header-rows: 1
   :widths: 17 83

   * - Column
     - What it means
   * - `ExtNr <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#extnr>`_
     - :literal:`0` for a file-based (Base) entry. A nonzero value
       assigns the entry to a HEMCO extension instead (not covered
       here).
   * - `Name <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#name>`_
     - A label you choose for this entry. Used in log/verbose output
       and in scale-factor bookkeeping---it does not need to match
       anything in the file.
   * - `sourceFile <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcefile>`_
     - Path to your netCDF file. May contain :literal:`$ROOT`,
       :literal:`$YYYY`, :literal:`$MM`, :literal:`$DD`,
       :literal:`$HH` tokens.
   * - `sourceVar <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcevar>`_
     - The exact netCDF variable name inside the file (case-sensitive
       -- check with :program:`ncdump -h`).
   * - `sourceTime <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcetime>`_
     - The time range/frequency covered by the file(s), plus a cycling
       mode letter -- see :ref:`custom-emis-timecycle` below.
   * - `C/R/E <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#cre>`_
     - The **time cycle flag**: what HEMCO should do when the
       simulation date falls outside what the file(s) provide. See
       :ref:`custom-emis-timecycle`.
   * - `SrcDim <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#srcdim>`_
     - The spatial/vertical shape of the data in the file, and how to
       map it onto model levels. See :ref:`custom-emis-srcdim`.
   * - `SrcUnit <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#srcunit>`_
     - The units HEMCO should assume the data is in. See
       :ref:`custom-emis-units`.
   * - `Species <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#hco-cfg-base-species>`_
     - The GEOS-Chem species this data will be emitted as.
   * - `ScalIDs <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#scalids>`_
     - Scale factor ID(s) (from the :literal:`SCALE FACTORS` section,
       :ref:`cfg-hco-scalefac`) to apply to this field, separated by
       :literal:`/`. Use :literal:`-` for none.
   * - `Cat  <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#cat>`_  / `Hier <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#hier>`_
     - Emission category and hierarchy, used to combine/override this
       field against other inventories. Only meaningful for
       :literal:`ExtNr = 0` entries. See :ref:`custom-emis-cathier`.

.. important::

   **GCHP users:** GCHP does not use HEMCO for file I/O, but instead
   relies upon the MAPL ExtData mechanism.  Thus, GCHP will **ignore
   entirely** the following columns of :file:`HEMCO_Config.rc`:
   `sourceFile
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcefile>`_,
   `sourceVar
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcevar>`_,
   `sourceTime <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#sourcetime>`_,
   `C/R/E <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#cre>`_,
   `SrcDim
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#srcdim>`_, and
   `SrcUnit <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#srcunit>`_.

   To tell GCHP to read a variable from disk, you must **also** add a
   corresponding entry to `ExtData.rc
   <https://gchp.readthedocs.io/en/stable/user-guide/config-files/ExtData_rc.html>`_
   specifying the file path, variable name, and read frequency.  GCHP only
   uses the masking and scaling fields from :file:`HEMCO_Config.rc`
   (`ExtNr <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#extnr>`_,
   `Species <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#hco-cfg-base-species>`_
   `ScalIDs <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#scalids>`_,
   `Cat
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#cat>`_,
   and
   `Hier
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#hier>`_.).

   Updating only :file:`HEMCO_Config.rc` without making corresponding
   changes to :file:`ExtData.rc` is a common source of error.  Please
   see `GCHP vs. GC-Classic usage differences
   <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/hemco-config.html#usage-differences-between-gchp-and-geos-chem-classic>`_
   for more information.

.. _custom-emis-srcdim:

=======================================
Getting SrcDim right (2-D vs. 3-D data)
=======================================

.. attention::

   If your emissions come out entirely at the surface level when you
   expected a vertical profile, this is almost always a
   :literal:`SrcDim` mistake, not a bug in HEMCO or GEOS-Chem.

:literal:`SrcDim` tells HEMCO both the shape of your input data **and**
which model levels to put it into. The most common settings are:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - ``SrcDim`` setting
     - Behavior
   * - ``xy``
     - 2-D data (one horizontal field, no vertical dimension). This
       is what almost all surface emissions files use.
   * - ``xyz``
     - 3-D data whose vertical dimension already matches (or can be
       vertically regridded to) the model's vertical levels.
   * - ``xyL*``
     - Copies a 2-D field into **every** model level uniformly. You
       then apply a **scale factor** (see below) to redistribute the
       total vertically -- e.g. to spread stack/plume-rise emissions
       across several levels. This is how CEDS energy/industry/ship
       emissions are vertically allocated in the default configuration.
   * - ``xyL=5``
     - Puts a 2-D field entirely into model level 5.
   * - ``xyL=1:PBL``
     - Distributes a 2-D field from the surface up to the boundary
       layer top.
   * - ``xy5`` / ``xy-5``
     - The input file itself has (at least) 5 levels; read the lowest
       (or, with the minus sign, the topmost) 5 of them into model
       levels 1-5.

If your file has one value per model column with no vertical
structure, use :literal:`xy`. If it has real 3-D structure that should
land on the model grid as-is, use :literal:`xyz`. Use :literal:`xyL*`
(plus a scale factor) only when you want to take a 2-D total and
spread it across levels according to some vertical profile.

See the full `SrcDim reference table
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#srcdim>`_
for every variant (fixed heights in meters, ensemble dimensions, etc.).

Worked example: vertically distributing a 2-D emission field
------------------------------------------------------------

Suppose you have a file :file:`vert_profile.nc` with a variable
:literal:`frac` giving the fraction of your emissions to place in each
model level. First define it as a scale factor:

.. code-block:: kconfig

   ###############################################################################
   ### BEGIN SECTION SCALE FACTORS
   ###############################################################################

   # Vertical profile definition
   400 MY_VERT_PROFILE vert_profile.nc frac 2020/1/1/0 C xyz 1 1

Then reference it from your Base Emissions entry, using
:literal:`xyL*`.  This will copy the 2-D field to every level,
and then apply the scale factor that you just defined.

.. code-block:: kconfig

   0 MY_INVENTORY_CO  $ROOT/MY_INVENTORY/v2024-01/co_$YYYY.nc  CO  2015-2024/1-12/1/0 C xyL* kg/m2/s CO 400 1 5

.. _custom-emis-timecycle:

=========================================
Time cycle flags (C/R/E) -- which to pick
=========================================

The `C/R/E time cycle flag
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#cre>`_
controls what HEMCO does when your simulation date and time does not
does not line up exactly with the date and time in the netCDF file
containing your emissions data.

.. list-table::
   :header-rows: 1
   :widths: 22 78

   * - :literal:`C/R/E` setting
     - Behavior
   * - ``C``
     - **Cycling.** If the simulation date lies outside the file's date
       range, reuse the nearest available year (or day/hour, depending
       on frequency). Use this for most emission inventories that only
       cover a fixed set of years.
   * - ``R``
     - **Range-restricted.** Only use this data within its exact valid
       date range; HEMCO does not know what to do outside it. Use this
       for fields that should never be silently substituted).
   * - ``E``
     - **Exact match required.** HEMCO halts if the simulation date
       does not exactly match a time slice in the file.
   * - ``EFYO`` / ``EY`` / ``CYS``
     - Most often used with the GEOS-Chem Classic restart-file entry
       (aka :literal:`SPC_`).

If you should encounter an error such as:

- "Cannot find field"
- "Not enough time slices"
- "Time stamps may be wrong"

this usually means that HEMCO believes that the netCDF variable
timestamp for your emissions field lies lies outside of the
time range defined by your choice of :literal:`C/R/E` and
:literal:`sourceTime`.  Our :ref:`errguide` supplemental guide
contains detailed information on how to resolve these type of errors.

As a starting point when authoring a new entry:

- If the file covers exactly the years/dates you'll simulate, use
  :literal:`E` or :literal:`R`. |br|
  |br|

- If the file covers a fixed historical period and you may run outside
  it (spin-up years, future scenarios, etc.), use :literal:`C`.

.. _custom-emis-units:

================================
Units: A source of silent errors
================================

Unlike a missing file or a bad date, a **units mismatch does not
always stop your run**.  By default HEMCO's `unit tolerance
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#unit-tolerance>`_.
setting only prints a warning.  This makes it very easy to end up with
incorrect emissions without generating any error or warning at all.

To check if your unit conversion is correct, set `Verbose
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#verbose>`_
to :literal:`true` in the `Settings
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#settings>`_
section of (:file:`HEMCO_Config.rc`) and run a short (~1 hour)
simulation. The unit conversion factor that HEMCO applies will be
written to the log file.  If the unit conversion factor is not
1.0, and you didn't expect units to be converted, then that indicates
an error.

Other unit pitfalls to check:

- **Negative or implausible totals right after adding a new file**.
  Check the netCDF file for a :literal:`_FillValue` or
  :literal:`missing_value` attribute that doesn't match what's
  actually used as the fill value in the data.  HEMCO does not always
  catch a mismatched fill value, which can then be summed into your
  emissions as a huge negative or positive number. |br|
  |br|

- **Only unitless scale factors**. Tightening the `unit tolerance
  <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#unit-tolerance>`_
  to :literal:`0` while testing a new inventory will cause HEMCO to halt
  on any units mismatch instead of generating warnings.

See the `full HEMCO units reference
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/units.html>`_ for
the complete list of recognized unit strings and for more unit
conversion examples.

.. _custom-emis-cathier:

===================================
Category and hierarchy (Cat / Hier)
===================================

:literal:`Cat` and :literal:`Hier` only matter for Base Emissions
(:literal:`ExtNr = 0`) entries, and only if you want to combine your
new field with (or have it override) an existing inventory.

- **Category** (:literal:`Cat`) groups independent emission sources
  that should be **added together** (e.g. anthropogenic, biofuel,
  biomass burning. etc. are typically different categories). |br|
  |br|

- **Hierarchy** (:literal:`Hier`) ranks fields **within the same
  category**. A higher hierarchy value overrides a lower one, but only
  inside the new field's own spatial mask. This is how a
  fine-resolution regional inventory (high hierarchy) can override a
  coarse global inventory (low hierarchy) only within the region it
  covers, while the global inventory still applies everywhere else.

If you're not sure what category/hierarchy an existing inventory uses, then:

- Set `Verbose
  <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#verbose>`_
  to :literal:`true`, and run a short (~1 hour) simulation.  The
  category and hierarchy of each emissions entry read by HEMCO will be
  reflected in the logfile. |br|
  |br|

- Search for the category/hierarchy values in each of the emission
  field entries in :file:`HEMCO_Config.rc` that you wish to replace
  with your own emissions.  Then use those same category/hierarchy
  values when you :ref:`swap in your own emissions
  <custom-emis-swap-example>`.

.. _custom-emis-swap-example:

=============================================
Worked example: replacing one species' source
=============================================

Here is how you can replace a default emissions for a species with an
emissions field from your own inventory:

#. Find the existing Base Emissions entries for the species that you
   wish to replace:

   .. code-block:: kconfig

      (((XIAO_C3H8
      0 XIAO_C3H8  $ROOT/XIAO/v2014-09/C3H8_C2H6_ngas.geos.1x1.nc  C3H8  1985/1/1/0 C xy kgC/m2/s C3H8 6/7/26/22 1 5
      )))

   The brackets allow the emissions entry to be toggled on and off in
   the `Extension Switches
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#extension-switches>`_
   section of :literal:`HEMCO_Config.rc`. |br|
   |br|

#. Add a new entry for your inventory.  Edit  :literal:`sourceFile`
   and :literal:`sourceVar` to point at your replacement data:

   .. code-block:: kconfig

      (((MY_ANTHRO_C3H8
      0 MY_C3H8  $ROOT/MY_ANTHRO_C3H8/v2024-01/c3h8_$YYYY.nc  C3H8  2015-2026/1-12/1/0 C xy kg/m2/s C2H6 - 1 5
      )))

#. (Optional) Add a scale factor under the `Scale Factors
   <https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#scale-factors>`_
   section of :literal:`HEMCO_Config.rc` and reference its ID number
   in the :literal:`ScalIDs` column. |br|
   |br|

#. Disable the old emissions inventory and enable your new inventory.
   This will prevent double-counting emissions.

   .. code-block:: kconfig

      ###############################################################################
      ### BEGIN SECTION EXTENSION SWITCHES
      ###############################################################################
      # ExtNr ExtName                on/off  Species  Years avail.
      0       Base                   : on    *
      ...
          --> XIAO_C3H8              :       false    # 1985
          --> MY_ANTHRO_C3H8         :       true     # 2015-2026
      ...

#. :ref:`Update HEMCO_Diagn.rc <custom-emis-diagn>` so that the
   diagnostic output reflects the swap.

.. _custom-emis-diagn:

=========================================
Wiring your new field into HEMCO_Diagn.rc
=========================================

Adding a Base Emissions entry does **not** automatically create a
diagnostic for it.  You must separately update `HEMCO_Diagn.rc
<https://hemco.readthedocs.io/en/latest/hco-ref-guide/diagnostics.html#configuration-file-for-the-default-collection>`_
with the combination of species/extension/category/hierarchy to
sum into each diagnostic output variable.

.. important::

   **GCHP users:** For each diagnostic entry in
   :file:`HEMCO_Diagn.rc`, you must also make a corresponding entry in
   the Emissions collection of :file:`HISTORY.rc`.  This is because
   GCHP relies upon the MAPL HISTORY component to archive diagnostic
   data.  If you forget to update :file:`HISTORY.rc`, then your HEMCO
   diagnostic output will not be saved to disk.  Please
   see `GCHP vs. GC-Classic usage differences
   <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/hemco-config.html#usage-differences-between-gchp-and-geos-chem-classic>`_
   for more information.

For example, this :file:`HEMCO_Diagn.rc` entry:

.. code-block:: kconfig

   # Name            Spec ExtNr  Cat Hier Dim Unit     LongName
   EmisC3H8_Total    C3H8 -1     -1  -1   2   kg/m2/s  C3H8_emission_flux_from_all_sectors

will result in this behavior:

.. list-table::
   :header-rows: 1
   :widths: 10 10 80

   * - Setting
     - Value
     - Behavior
   * - :literal:`ExtNr`
     - -1
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions over **all** extensions.
       This not only includes the Base Emissions (:literal:`ExtNr =
       0`) but also any HEMCO extensions (e.g. `MEGAN
       <https://hemco.readthedocs.io/en/stable/hco-ref-guide/extensions.html#megan>`_)
       that have been activated.
   * - :literal:`Cat`
     - -1
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions over **all** categories.
       This will sum together anthropogenic, biomass, biogenic
       emissions. etc.
   * - :literal:`Hier`
     - -1
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions over **all** hierarchies.

Thus, if you wish to obtain the absolute total emissions of a given species,
it is OK to use :literal:`ExtNr = -1`, :literal:`Cat = -1` and
:literal:`Hier = -1`.  But if you wish to obtain a sectoral total,
you should explicitly specify the :literal:`ExtNr`, :literal:`Cat` and
:literal:`Hier` values.

For example, here is how you can add an entry to obtain anthropogenic
emissions of C3H8 from the :ref:`example inventory in the previous
section <custom-emis-swap-example>`:

.. code-block:: kconfig

   # Name            Spec ExtNr  Cat Hier Dim Unit     LongName
   EmisC3H8_Anthro   C3H8  0      1   5   2   kg/m2/s  C3H8_emission_flux_from_anthropogenic_sources

which will result in the following behavior:

.. list-table::
   :header-rows: 1
   :widths: 10 10 80

   * - Setting
     - Value
     - Behavior
   * - :literal:`ExtNr`
     - 0
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions only from the Base
       Emissions (:literal:`ExtNr = 0`).  This will only include the
       emissions that we have read from disk and not any extensions.
   * - :literal:`Cat`
     - 1
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions only from :literal:`Cat
       = 1`, which specifies anthropogenic emissions.  This matches
       the category in the example above.
   * - :literal:`Hier`
     - 5
     - Sums C\ :sub:`3`\H\ :sub:`8` emissions only from :literal:`Hier
       = 5`.  This matches the hierarchy in the example above.

If you're not sure what :literal:`ExtNr`, :literal:`Cat`, and
:literal:`Hier` your :file:`HEMCO_Diagn.rc` entry actually uses, set
`Verbose
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#verbose>`_
to :literal:`true` (under `Settings
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/hemco-config.html#settings>`_
in :file:`HEMCO_Config.rc` and run a short (~1 hour) test
simulation.  The HEMCO log file will contain exactly what was read for
each container, which you can then match against your diagnostic definition.

.. _custom-emis-validate:

=============================
Validating your new emissions
=============================

Before trusting a new inventory in a full simulation:

#. **Check the raw file** with :program:`ncdump -cts` (or
   :program:`ncview`) to confirm the variable name, units attribute,
   fill value, and number of vertical levels match what you put in
   :file:`HEMCO_Config.rc`. |br|
   |br|

#. **Run a short test simulation** (~1 hour) with
   :literal:`Verbose: true` and inspect the HEMCO log for the
   container name, unit conversion factor, and
   :literal:`ExtNr`/:literal:`Cat`/:literal:`Hier` that were actually
   assigned. |br|
   |br|

#. **Compare total mass** between your input file and the
   corresponding :file:`HEMCO_diagnostics.*.nc` output (e.g. with
   :program:`cdo fldsum` or a quick GCPy/xarray script) for a single
   time step, to catch unit or regridding errors before running a full
   simulation. |br|
   |br|

#. **Watch for cross-resolution surprises**: HEMCO regrids
   conservatively (preserving the weighted global average), so a
   coarse- or fine-resolution run's total mass from the same file
   should match; if it doesn't, suspect a masking or category/hierarchy
   overlap issue rather than regridding itself.

.. _custom-emis-troubleshoot:

===============================
Quick-reference troubleshooting
===============================

.. list-table::
   :header-rows: 1
   :widths: 35 30 35

   * - Symptom
     - Likely cause
     - See
   * - Emissions all show up at the surface level only
     - Wrong ``SrcDim`` (e.g. ``xy`` used for data that needed ``xyz``
       or ``xyL*`` + a scale factor)
     - :ref:`custom-emis-srcdim`
   * - "Cannot find field", "Not enough time slices", "Time stamps may
       be wrong"
     - :literal:`C/R/E` flag or date range doesn't match the file
     - :ref:`custom-emis-timecycle` |br|
       |br|
       :ref:`HEMCO Error: Cannot find field <errguide-runtime-nofield>`
   * - Emissions are a suspicious multiple (e.g. ~2x) too high/low, or
       negative, with no error message
     - Species-mass vs. carbon-mass mismatch, or an unrecognized fill
       value
     - :ref:`custom-emis-units`
   * - New field runs fine but the diagnostic output is zero,
       mismatched, or double-counted
     - ``ExtNr``/``Cat``/``Hier`` in :file:`HEMCO_Diagn.rc` don't match
       (or are left at ``-1``)
     - :ref:`custom-emis-diagn`
   * - New regional inventory doesn't override the global one where
       expected (or overrides it everywhere)
     - ``Hier`` too low, or mask boundaries not restricting the field
       as expected
     - :ref:`custom-emis-cathier`
   * - New field has no effect at all under **GCHP**
     - Forgot to add the file to :file:`ExtData.rc` -- GCHP ignores
       :file:`HEMCO_Config.rc`'s file-I/O columns
     - `GCHP vs. GC-Classic usage differences
       <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/hemco-config.html#usage-differences-between-gchp-and-geos-chem-classic>`_
   * - New field runs and diagnoses correctly, but never appears in
       output files under **GCHP**
     - Emissions diagnostics in GCHP must also be listed in
       :file:`HISTORY.rc`, not just :file:`HEMCO_Diagn.rc`
     - `GCHP vs. GC-Classic usage differences
       <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/hemco-config.html#usage-differences-between-gchp-and-geos-chem-classic>`_
       |br|
       |br|
       :ref:`custom-emis-diagn`
