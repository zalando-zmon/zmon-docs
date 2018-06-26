.. _zmon-cli:

*******************
Command Line Client
*******************

The command line client makes your life easier when interacting with the REST API. The ZMON scheduler will refresh modified data (checks, alerts, entities every 60 seconds).

Installation
------------

.. code-block:: bash

  pip3 install --upgrade zmon-cli

Configuration
^^^^^^^^^^^^^

Configure your zmon cli by running ``configure``-

.. code-block:: bash

  zmon configure

Authentication
^^^^^^^^^^^^^^

ZMON CLI tool must authenticate against ZMON. Internally it uses zign to obtain access token, but you can override that behaviour by exporting a variable ZMON_TOKEN.

.. code-block:: bash

  export ZMON_TOKEN=myfancytoken

If you are using github for authentication, have an unprivileged personal access token ready.

Entities
--------
.. _cli-entities:

Create or update
^^^^^^^^^^^^^^^^
Pushing entities with the zmon cli is as easy as:

.. code-block:: bash

  zmon entities push \
    '{"id":"localhost:3421","type":"instance","name":"zmon-scheduler-ng","host":"localhost","ports":{"3421":3421}}'

Existing entities with the same ID will be updated.

The client however also supports loading data from .json and .yaml files, both may contain a list for creating/updating many entities at once.

.. code-block:: bash

  zmon entities push your-entities.yaml


.. Note::
    Creating entity of type GLOBAL is not allowed. GLOBAL as an entity type is reserved for ZMON's internal use.


.. Tip::

    All commands and subcommands can be abbreviated, i.e. the following lines are equivalent:

        .. code-block:: bash

           $ zmon entities push my-data.yaml
           $ zmon ent pu my-data.yaml

Search and filter
^^^^^^^^^^^^^^^^^

Show all entities:

.. code-block:: bash

  zmon entities

Filter by type "instance"

.. code-block:: bash

  zmon entities filter type instance


Check Definitions
-----------------
.. _cli-cd:

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

Similar to check defintions you can also manage your alert definitions via the ZMON cli.

Keep in mind that for alerts the same constraints apply as in the UI. For creating/modifying an alert you need to be a member of the team selected for "team" (unlike the responsible team).

Init
^^^^

.. code-block:: bash

  zmon alert-definition init your-new-alert.yaml

Create
^^^^^^

.. code-block:: bash

  zmon alert-definition create your-new-alert.yaml

Get
^^^

.. code-block:: bash

  zmon alert-definition get 1999

Update
^^^^^^

.. code-block:: bash

  zmon alert-definition update host-load-5.yaml
