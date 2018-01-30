**************************
Python components of DFTB+
**************************

The `PEP 8 <https://www.python.org/dev/peps/pep-0008/>`_ guidelines are the
basis of the python style used in the code.

`Pylint <https://www.pylint.org/>`_ files are suplied (`utils/srccheck/pylint/`)
for code checking. In many cases, python unit tests are also implemented (see
`test/tools/dptools/` for examples).

For example to test the straingen script for Python3 compliance, from the top of
the repository type::
  
  env PYTHONPATH=$PWD/tools/dptools/src pylint3 --rcfile \
  utils/srccheck/pylint/pylintrc-3.ini tools/dptools/bin/straingen

while to test for correct performance::

  make test_dptools
