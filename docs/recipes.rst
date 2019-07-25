**************************
Contributing DFTB+ recipes
**************************

There are a number of more detailed examples for the use of DFTB+ in the
`recipes <https://github.com/dftbplus/recipes>`_ repository, which is generated
on `readthedocs <https://dftbplus-recipes.readthedocs.io/en/stable/>`_. The
recipes are written in the ReStructuredText language, and in most cases include
downloadable input files for the examples.

Sphinx version 1.8 or above is required to locally generate the recipes. We find
that using a `virtual environment
<https://www.docslikecode.com/learn/01-sphinx-python-rtd/>`_ for python is
convenient.

Repository structure
====================

The main directories in the repository are

* `docs/` Contains sub-folders for examples (i.e. `basics/`)
* `docs/_archives/recipes/` Contains sub-folders for downloadable input files
   and scripts
* `docs/_figures/` Contains sub-folders for images in the recipes

Downloadable files
==================

Constructions like ::

  [Input: `recipes/basics/bandstruct/1_density/`]

are used to specify the path of files inside the `_archives` directory when HTML
documents are generated. This is independent of the actual packaging of the
files in `docs/_archives/recipes/` into a downloadable file, so should be
manually checked.
