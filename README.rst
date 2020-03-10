ZMON source code on GitHub is no longer in active development. Zalando will no longer actively review issues or merge pull-requests.

ZMON is still being used at Zalando and serves us well for many purposes. We are now deeper into our observability journey and understand better that we need other telemetry sources and tools to elevate our understanding of the systems we operate. We support the `OpenTelemetry <https://opentelemetry.io>`_ initiative and recommended others starting their journey to begin there.

If members of the community are interested in continuing developing ZMON, consider forking it. Please review the licence before you do.

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
