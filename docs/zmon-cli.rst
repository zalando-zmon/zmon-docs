.. _zmon-cli:

************************
Command Line Client
************************

The command line client makes your life easier when interacting with the ZMON REST API.

Installation
------------

.. code-block:: bash

  pip3 install --upgrade zmon-cli

Entities
--------

Pushing entities with the zmon cli is as easy as:

.. code-block:: bash

  zmon entities push '{"id":"localhost:3421","name":"zmon-scheduler-ng","host":"localhost","ports":{"3421":3421}}'

Existing entities with the same ID will be updated.

The client however also supports loading data from .json and .yaml files, both may contain a list for creating/updating many entities at once.

Check Definitions
-----------------

Initializing
^^^^^^^^^^^^

When starting from scratch use:

.. code-block:: bash

  zmon check-definition init your-new-check.yaml


Get
^^^

Retrieve an existing check defintion as YAML.

.. code-block:: bash

  zmon check-definition get 1234

Create and Update
^^^^^^^^^^^^^^^^^

Create or update from file, existing check with same "owning_team" and "name" will be updated.

.. code-block:: bash

  zmon check-definition update your-check.yaml

Alert Definitions
-----------------