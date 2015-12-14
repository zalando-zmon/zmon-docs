************************
Configuration options
************************

If you decide not to run the supplied Docker images, you will find the relevant configuration options in the project specific configuration files ("application.yaml","web.conf","application.properties"). We suggest to run Docker images for now and use the following environment variables to specify your configuration options.

Controller
==========



Scheduler
=========

Specify the Redis server you want to use:

.. code-block:: bash

   SCHEDULER_REDIS_HOST
   SCHEDULER_REDIS_PORT

Specify where you run the controller hosting the entity service:

.. code-block:: bash

   SCHEDULER_ENTITY_SERVICE_URL

Specify the base URL to the controller for getting checks and alerts:

.. code-block:: bash

  SCHEDULER_CONTROLLER_URL

Worker
======

Specify the Redis server used:

.. code-block:: bash

   WORKER_REDIS_HOST
   WORKER_REDIS_PORT

If you plan to access your PostgreSQL cluster specify the credentials below. We suggest to use a distinct user for ZMON with limited read only privileges.

.. code-block:: bash

   WORKER_POSTGRESQL_USER
   WORKER_POSTGRESQL_PASSWORD

If you need to access MySQL specify the user credentials below, again we suggest to use a user with limited privileges only.

.. code-block:: bash

   WORKER_MYSQL_USER
   WORKER_MYSQL_PASSWORD