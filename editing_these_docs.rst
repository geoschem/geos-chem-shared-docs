.. |br| raw:: html

   <br />

.. _editing_this_user_guide:

#######################
Editing this User Guide
#######################

This user guide is generated with `Sphinx
<https://www.sphinx-doc.org/>`_.  Sphinx is an open-source Python
project designed to make writing software documentation easier.  The
documentation is written in :ref:`reStructuredText (reST)
<editing_this_user_guide_rest>`, a plaintext markup language that
Sphinx extends for software documentation. The source for the
documentation is the :file:`docs/source` directory in top-level of the
source code (and its subdirectories).

.. _editing_this_user_guide_quickstart:

===========
Quick start
===========

To build this user guide on your local machine, you need to install
Sphinx and its dependencies, which are listed in the table below.

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Package
     - Description
     - Version
   * - sphinx
     - Creates online user manual documentation from markup text files
     - 7.2.6
   * - `sphinx-autobuild <https://github.com/sphinx-doc/sphinx-autobuild>`_
     - Dynamically builds Sphinx documentation and displays it in a
       browser
     - 2021.3.14
   * - `sphinx_rtd_theme <https://github.com/readthedocs/sphinx_rtd_theme>`_
     - Sphinx theme for ReadTheDocs
     - 2.0.0
   * - `sphinxcontrib-bibtex <https://pypi.org/project/sphinxcontrib-bibtex/>`_
     - Inserts LaTeX-style bibliography citations into ReadTheDocs
       documentation
     - 2.6.2
   * - `docutils <https://docutils.sourceforge.io/>`_
     - Processes plaintext documentation into HTML and other formats
     - 0.20.1
   * - `recommonmark  <https://github.com/readthedocs/recommonmark>`_
     - Parses text for docutils
     - 0.7.1
   * - `jinja2 <https://jinja.palletsprojects.com/en/stable/>`_
     - Replaces tokenized strings with text
     - 3.1.6

These packages may be downloaded from the `Python Package Index (PyPI)
<https://pypi.org>`_.  Use the :command:`pip` package manager to
install them as described below.

.. code-block:: console

   $ cd docs
   $ pip install -r requirements.txt

This step only needs to be done once.

Next, run :command:`sphinx-autobuild` in a terminal window.  This will
parse the documentation files in :literal:`docs/source` and
generate HTML documentation in :literal:`docs/build/html`.

.. code-block:: console

   $ sphinx-autobuild source build/html

You may view the generated documentation by opening a web browser and
typing :literal:`localhost:8000` in the navigation bar.  As long as
sphinx-autobuild is left running, the documentation will be
regenerated automatically whenever edits to the documentation files
are saved.  Typing Control-C will quit quit sphinx-autobuild.

To remove the generated documentation files in the `docs/build/html`
folder, type:

.. code-block:: console

   $ make clean

.. _editing_this_user_guide_rest:

==========================
Learning reStructured Text
==========================

ReadTheDocs documentation is generated from text files in **reStructured
Text (reST)**, which is an easy-to-read, what-you-see-is-what-you-get
plaintext markup language. It is the default markup language used by
Sphinx.

Writing reST can be tricky at first. Whitespace matters, and some
directives can be easily miswritten. Two important things you should
know right away are:

- Indents are 3-spaces
- "Things" are separated by 1 blank line. For example, a list or
  code-block following a paragraph should be separated from the
  paragraph by 1 blank line.

You should keep these in mind when you're first getting
started. Dedicating an hour to learning reST will save you time in the
long-run. Below are some good resources for learning reST.

- `reStructuredText primer
  <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_:
  (single best resource; however, it's better read than skimmed)
- Official `reStructuredText reference
  <https://docutils.sourceforge.io/docs/user/rst/quickref.html>`_
  (there is *a lot* of information here)
- `Presentation by Eric Holscher
  <https://www.youtube.com/watch?v=eWNiwMwMcr4>`_ (co-founder of Read
  The Docs) at DjangoCon US 2015 (the entire presentation is good, but
  reST is described from 9:03 to 21:04)
- `YouTube tutorial by Audrey Tavares
  <https://www.youtube.com/watch?v=DSIuLnoKLd8>`_

A good starting point would be Eric Holscher's presentations followed
by the reStructuredText primer.

.. _editing_this_user_guide_style:

================
Style guidelines
================

.. important::

   This user guide is written in semantic markup. This is important so
   that the user guide remains maintainable. Before contributing to
   this documentation, please review our style guidelines
   (below). When editing the source, please refrain from using
   elements with the wrong semantic meaning for aesthetic
   reasons. Aesthetic issues can be addressed by changes to the theme.

Titles and headers
------------------

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Element
     - reST Markup
   * - Section header |br| (aka "Heading 1)
     - Overline by :literal:`#` and underline by :literal:`#`
   * - Sub-section header |br| (aka "Heading 2")
     - Overline by :literal:`=` and underline by :literal:`=`
   * - Sub-sub-section header |br| (aka "Heading 3")
     - Underline by :literal:`-`
   * - Sub-sub-sub-section header |br| (aka "Heading 4") [#A]_
     - Underline by :literal:`~`
   * - Sub-sub-sub-sub-section header |br| (aka "Heading 5") [#A]_
     - Underline by :literal:`^`

.. rubric:: Notes

.. [#A] It is good practice to try to avoid using sub-sub-sub-sections
	(Heading 4) or sub-sub-sub-sub-sections (Heading 5), as this
	reduces the complexity of the table of contents.

References and links
--------------------

.. list-table::
   :header-rows: 1
   :widths: 30 35 35

   * - Element
     - reST Markup Example
     - Rendered text
   * - Reference to a named anchor
     - ``:ref:`editing_this_user_guide_quickstart```
     - :ref:`editing_this_user_guide_quickstart`
   * - Renamed reference to a named anchor
     - ``:ref:`Getting Started <editing_this_user_guide_quickstart>``
     - :ref:`Getting Started <editing_this_user_guide_quickstart>`
   * - HTML link
     - ```ReadTheDocs <https://geos-chem.readthedocs.io>`_``
     - `GEOS-Chem Manual <https://geos-chem.readthedocs.io>`_

Other common style elements
---------------------------

.. list-table::
   :header-rows: 1
   :widths: 30 35 35

   * - Element
     - reST Markup Example
     - Rendered text
   * - File paths
     - ``:file:`myfile.nc```
     - :file:`myfile.nc`
   * - Directories
     - ``:file:`/usr/bin```
     - :file:`/usr/bin`
   * - Program names
     - ``:program:`cmake```
     - :program:`cmake`
   * - OS-level commands
     - ``:program:`rm -rf```
     - :program:`rm -rf`
   * - Environment variables
     - ``:envvar:`$HOME```
     - :envvar:`$HOME`
   * - Inline code or code variables
     - ``:code:`PRINT*, "HELLO!"```
     - :code:`PRINT*, "HELLO!"`
   * - Inline literal text
     - ``:literal:`$```
     - :literal:`$`

Indented code and text blocks
-----------------------------

Code snippets should use :literal:`.. code-block:: <language>`
directives:

Python
~~~~~~

.. code-block:: none

   .. code-block:: python

      import gcpy
      print("hello world")

Renders as:

.. code-block:: python

    import gcpy
    print("hello world")

Fortran
~~~~~~~

.. code-block:: None

   .. code-block:: Fortran

      DO I = 1, 10
         PRINT*, I
      ENDDO

Renders as:

.. code-block:: Fortran

   DO I = 1, 10
      PRINT*, I
   ENDDO

Bash
~~~~

.. code-block:: none

   .. code-block:: bash

      #!/bin/bash

      for f in *.nc; do
          echo $f
      done

Renders as:

.. code-block:: bash

   #!/bin/bash

   for f in *.nc; do
       echo $f
   done

Command line (aka "console")
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: none

   .. code-block:: console

      $ ls -l $HOME

Renders as:

.. code-block:: console

   $ ls -l $HOME

No formatting
~~~~~~~~~~~~~

.. code-block:: none

   .. code-block:: none

      This text renders without any special formatting.

Renders as:

.. code-block:: none

   This text renders without any special formatting.
