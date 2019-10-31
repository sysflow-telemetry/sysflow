# SysFlow Documentation
[![Documentation Status](https://readthedocs.org/projects/sysflow/badge/?version=latest)](https://sysflow.readthedocs.io/en/latest/?badge=latest)

This repository aggregates the [documentation](https://sysflow.readthedocs.io/) from the different SysFlow projects and hosts SysFlow's [issue tracker](https://github.com/sysflow-telemetry/sf-docs/issues).

## Online documentation
SysFlow documentation is automatically built on pushes and is available at [sysflow.readthedocs.io](https://sysflow.readthedocs.io/). 

## Offline build
This documentation depends on Sphinx (http://www.sphinx-doc.org/en/master/), which must be installed to do builds. The project also requires the following Sphinx plugins:

* http://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html
* https://pypi.org/project/m2r/

To build the site as HTML go to the base directory and type:
```
make html
```
