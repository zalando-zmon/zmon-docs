.. _entities:

********
Entities
********

Entities are in general describing what you want to monitor in your infrastructure. This may be as basic as a host, with its attributes hostname and ip, or it may be a bit more complex like a PostgreSQL sharded cluster consisting of an identifier and a set of connection strings.

Internally the entities are stored in the entity service that is part of the controller or they are discovered by adapters in the scheduler. Thus ZMON gives you two options for automation/integration into your platform. At Zalando we use both, connecting ZMON to tools like our CMDB but also pushing entities via the REST API.

Using the entity service, entities are described using a single JSON document.

Any entity must contain an ID that is unique within your ZMON deployment. We often use a pattern like <hostname>(:<port>) to create uniqueness on host and application level, but this is up to you.

You can find some examples for entities in:

  https://github.com/zalando/zmon/tree/master/examples/entities


Examples
--------

Describing the Vagrant Box one could for example use the following entities:

The scheduler instance:

.. code-block:: json

    {
        "id":"localhost:3421",
        "type":"instance",
        "host":"localhost",
        "project":"zmon-scheduler-ng",
        "ports": {"3421":3421},
    }

We use the "ports" dictionary to also describe additional open ports, often a second port is added exposing management features, like with spring-boot.

The PostgreSQL instance:

.. code-block:: json

    {
        "id":"localhost:5432",
        "type":"database",
        "name":"zmon-cluster",
        "shards": {"zmon":"localhost:5432/local_zmon_db"}
    }

The usage of the property "shards" is given by how the worker exposes PostgreSQL clusters to the sql() function.