==================
ZMON Documentation
==================

.. image:: https://readthedocs.org/projects/zmon/badge/?version=latest
   :target: https://readthedocs.org/projects/zmon/?badge=latest
   :alt: Documentation Status

First install the Sphinx_ documentation generator.

.. code-block:: bash

    $ sudo pip install sphinx sphinx_rtd_theme


Generate the ZMON HTML documentation locally:

.. code-block:: bash

    $ cd docs; make html

.. _Sphinx: http://sphinx-doc.org/

Run docs locally:

.. code-block:: bash

    $ python -m SimpleHTTPServer 8888

If you are using Python3:

.. code-block:: bash

    $ python3 -m http.server 8888

Docs at:
    `http://localhost:8888/_build/html/`
