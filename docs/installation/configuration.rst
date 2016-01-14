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

The ZMON worker has many configuration options (via environment variables), most of them are specific to function plugins.

Global Options
--------------

``WORKER_REDIS_SERVERS``
    Comma separated list of Redis servers (default: localhost:6379).
``WORKER_KAIROSDB_HOST``
    Host of KairosDB for time series storage.
``WORKER_KAIROSDB_PORT``
    Port of KairosDB for time series storage.
``WORKER_DATASERVICE_URL``
    Optional URL of data endpoint for workers deployed in cloud environments (AWS).
``OAUTH2_ACCESS_TOKEN_URL``
    URL of OAuth 2 provider to get access tokens.
``WORKER_TEAM``
    Optional team ID for team-specific workers.
``WORKER_NOTIFICATIONS_PUSH_URL``
    Optional URL to the ZMON Notification Service endpoint.
``WORKER_NOTIFICATIONS_PUSH_KEY``
    Optional API Key for the ZMON Notification Service.

Notification Options
--------------------

``WORKER_NOTIFICATIONS_MAIL_HOST``
    SMTP host for email notifications.
``WORKER_NOTIFICATIONS_MAIL_PORT``
    SMTP port for email notifications.
``WORKER_NOTIFICATIONS_MAIL_SENDER``
    Sender address for email notifications.
``WORKER_NOTIFICATIONS_MAIL_USER``
    SMTP user for email notifications.
``WORKER_NOTIFICATIONS_MAIL_PASSWORD``
    SMTP password for email notifications.
``WORKER_NOTIFICATIONS_HIPCHAT_TOKEN``
    Access token for HipChat notifications.
``WORKER_NOTIFICATIONS_HIPCHAT_URL``
    URL of HipChat server.
``WORKER_NOTIFICATIONS_SLACK_TOKEN``
    Slack access token for notifications to Slack channels.


Plug-In Options
---------------

All plug-in options have the prefix ``WORKER_PLUGIN_<plugin-name>_``, i.e. if you want to set option "bar" of the plugin "foo" to "123" via environment variable:

.. code-block:: bash

    WORKER_PLUGIN_FOO_BAR=123

If you plan to access your PostgreSQL cluster specify the credentials below. We suggest to use a distinct user for ZMON with limited read only privileges.

.. code-block:: bash

   WORKER_PLUGIN_SQL_USER
   WORKER_PLUGIN_SQL_PASS

If you need to access MySQL specify the user credentials below, again we suggest to use a user with limited privileges only.

.. code-block:: bash

   WORKER_PLUGIN_MYSQL_USER
   WORKER_PLUGIN_MYSQL_PASS
