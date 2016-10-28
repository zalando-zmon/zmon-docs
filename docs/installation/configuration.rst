***********************
Component Configuration
***********************

In this section we assume that you want to use Docker as means of deployment.
The ZMON Dockerimages in Zalando's Open Source registry are exactly the ones we use ourselves, injecting all configuartion via environment variables.

If this does not fit your needs you can run the artifacts directly and decide to use environment variables or modify the example config files.

At this point we also assume the requirements in terms of PostgreSQL, Redis and KairosDB are available and you have the credentials at hand.
If not see :ref:`requirements`. The minimal configuration options below are taken from the Demo's Bootstrap_ script!

Authentication
==============

For the ZMON controller we assume that it is publicly accessible.
Thus the UI always requires users to login and the REST API, too.
The REST API relies on tokens via the ``Authorization: Bearer <token>`` header to allow access.
For environments where you have no OAauth2 setup you can configure pre-shared keys for API access.

.. note::

   Feel free to look at Zalando's `Plan-B <http://planb.readthedocs.io/en/latest/>`_, which is a freely available OAuth2 provider we use for our platform to secure service to service communication.

Creating a preshared token can be achieved like this and adding them to the Controller configuration.

.. code-block:: bash

  SCHEDULER_TOKEN=$(makepasswd --string=0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ --chars 32)

.. warning::

    Due to magic in matching env vars token must be ALL UPPERCASE

Scheduler and worker both at times call the controller's REST API thus you need to configure tokens for them.
For the scheduler, KairosDB, eventlog-service and metric-cache if deployed we assume for now they are private.
Theses services are accessed only by worker and controller and do not need to be public.
Same is true for Redis, PostgreSQL and Cassandra.
However in general we advise you to setup proper credentials and roles where possible.

Running Docker
==============

First we need to figure out what tags to run.
Belows bash snippet helps you to retrieve and set the latest available tags.

.. code-block:: bash

  function get_latest () {
      name=$1
      # REST API returns tags sorted by time
      tag=$(curl --silent https://registry.opensource.zalan.do/teams/stups/artifacts/$name/tags | jq .[].name -r | tail -n 1)
      echo "$name:$tag"
  }

  echo "Retrieving latest versions.."
  REPO=registry.opensource.zalan.do/stups
  POSTGRES_IMAGE=$REPO/postgres:9.4.5-1
  REDIS_IMAGE=$REPO/redis:3.2.0-alpine
  CASSANDRA_IMAGE=$REPO/cassandra:2.1.5-1
  ZMON_KAIROSDB_IMAGE=$REPO/$(get_latest kairosdb)
  ZMON_EVENTLOG_SERVICE_IMAGE=$REPO/$(get_latest zmon-eventlog-service)
  ZMON_CONTROLLER_IMAGE=$REPO/$(get_latest zmon-controller)
  ZMON_SCHEDULER_IMAGE=$REPO/$(get_latest zmon-scheduler)
  ZMON_WORKER_IMAGE=$REPO/$(get_latest zmon-worker)
  ZMON_METRIC_CACHE=$REPO/$(get_latest zmon-metric-cache)

To run the selected images use Docker's run command together with the options explained below.
We use the following wrapper for this:

.. code-block:: bash

  function run_docker () {
      name=$1
      shift 1
      echo "Starting Docker container ${name}.."
      # ignore non-existing containers
      docker kill $name &> /dev/null || true
      docker rm -f $name &> /dev/null || true
      docker run --restart "on-failure:10" --net zmon-demo -d --name $name $@
  }

  run_docker zmon-controller \
              # -e ......... \
              # -e ......... \
             $ZMON_CONTROLLER_IMAGE

Controller
==========

Configure your Github application:

.. code-block:: bash

    -e SPRING_PROFILES_ACTIVE=github \
    -e ZMON_OAUTH2_SSO_CLIENT_ID=64210244ddd8378699d6 \
    -e ZMON_OAUTH2_SSO_CLIENT_SECRET=48794a58705d1ba66ec9b0f06a3a44ecb273c048 \

Make everyone admin for now:

.. code-block:: bash

    -e ZMON_AUTHORITIES_SIMPLE_ADMINS=* \


Configure PostgreSQL access:

.. code-block:: bash

    -e POSTGRES_URL=jdbc:postgresql://$PGHOST:5432/local_zmon_db \
    -e POSTGRES_PASSWORD=$PGPASSWORD \

Setup Redis connection:

.. code-block:: bash

    -e REDIS_HOST=zmon-redis \
    -e REDIS_PORT=6379 \
    -e ENDPOINTS_CORS_ALLOWED_ORIGINS=https://demo.zmon.io \

Setup URLs for other services:

.. code-block:: bash

    -e ZMON_EVENTLOG_URL=http://zmon-eventlog-service:8081/ \
    -e ZMON_KAIROSDB_URL=http://zmon-kairosdb:8083/ \
    -e ZMON_METRICCACHE_URL=http://zmon-metric-cache:8086/ \
    -e ZMON_SCHEDULER_URL=http://zmon-scheduler:8085/ \

And last but not least, configure a preshared token, to allow the scheduler and worker to access the REST API. Remember tokens need to all uppercase here.

.. code-block:: bash

    -e PRESHARED_TOKENS_${SCHEDULER_TOKEN}_UID=zmon-scheduler \
    -e PRESHARED_TOKENS_${SCHEDULER_TOKEN}_EXPIRES_AT=1758021422 \
    -e PRESHARED_TOKENS_${SCHEDULER_TOKEN}_AUTHORITY=user

Scheduler
=========

Specify the Redis server you want to use:

.. code-block:: bash

   -e SCHEDULER_REDIS_HOST=zmon-redis \
   -e SCHEDULER_REDIS_PORT=6379 \

Setup access to the controller and entity service (both provided by the controller):
Not the reuse of the above defined pre shared key!

.. code-block:: bash

   -e SCHEDULER_OAUTH2_STATIC_TOKEN=$SCHEDULER_TOKEN \
   -e SCHEDULER_URLS_WITHOUT_REST=true \
   -e SCHEDULER_ENTITY_SERVICE_URL=http://zmon-controller:8080/ \
   -e SCHEDULER_CONTROLLER_URL=http://zmon-controller:8080/ \

Worker
======

The worker configuration is split into essential configuration options, like Redis and KairosDB and the plugin configuration, e.g. PostgreSQL credentials, ...

Essential Options
-----------------

Configure Redis Access:

.. code-block:: bash

  -e WORKER_REDIS_SERVERS=zmon-redis:6379 \

Configure KairosDB:

.. code-block:: bash

  -e WORKER_KAIROSDB_HOST=zmon-kairosdb \

Configure EventLog service:

.. code-block:: bash

  -e WORKER_EVENTLOG_HOST=zmon-eventlog-service \
  -e WORKER_EVENTLOG_PORT=8081 \

Configure Worker token to access controller API: (relying on Python tokens library here)

.. code-block:: bash

  -e  OAUTH2_ACCESS_TOKENS=uid=$WORKER_TOKEN \


Configure Metric Cache (optional):

.. code-block:: bash

  -e WORKER_METRICCACHE_URL=http://zmon-metric-cache:8086/api/v1/rest-api-metrics/ \
  -e WORKER_METRICCACHE_CHECK_ID=9 \

.. _notification-options-label:

Notification Options
--------------------

Hipchat
^^^^^^^
``WORKER_NOTIFICATIONS_HIPCHAT_TOKEN``
    Access token for HipChat notifications.
``WORKER_NOTIFICATIONS_HIPCHAT_URL``
    URL of HipChat server.

HTTP
^^^^
``WORKER_NOTIFICATIONS_HTTP_DEFAULT_URL``
    HTTP endpoint default URL.
``WORKER_NOTIFICATIONS_HTTP_WHITELIST_URLS``
    List of whitelist URL endpoints. If URL is not in this list, then exception will be raised.
``WORKER_NOTIFICATIONS_HTTP_ALLOW_ALL``
    Allow any URL to be used in HTTP notification.
``WORKER_NOTIFICATIONS_HTTP_HEADERS``
    Default headers to be used in HTTP requests.

Mail
^^^^
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

Slack
^^^^^
``WORKER_NOTIFICATIONS_SLACK_TOKEN``
    Slack access token for notifications to Slack channels.

Twilio
^^^^^^
``WORKER_NOTIFICATIONS_SERVICE_URL``
    URL of notification service (needs to be publicly accessible)
``WORKER_NOTIFICATIONS_SERVICE_KEY``
    Preshared key to call notification service if not using oauth2



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


.. _Bootstrap: https://github.com/zalando-zmon/zmon-demo


Notification Service
====================

Optional component to service mobile API, push notifications and Twilio notifications.

Options
-------

``NOTIFICATIONS_TWILIO_API_KEY``
    API Key
``NOTIFICATIONS_TWILIO_USER``
    User
``NOTIFICATIONS_TWILIO_PHONE_NUMBER``
    Phone number to use
``NOTIFICATIONS_DOMAIN``
    Domain under which notification service is reachable
``SPRING_APPLICATION_JSON``
    Use this to define preshared keys if not using oauth2. Specify key and max validity.

    .. code-block:: json

        {"notifications":{"shared_keys":{"<your random key>": 1504981053654}}}'
