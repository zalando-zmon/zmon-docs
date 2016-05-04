.. _grafana:

********************
Grafana2 and KairosDB
********************

Grafana is a powerful open-source tool for creating dashboards to visualize metric data. ZMON deploys Grafana 2.x along with our plugin to read metric data from KairosDB. Grafana is served directly from the ZMON controller. Read requests are proxied through the controller so as not to expose the write/delete API from KairosDB. Dashboards are also saved via the controller, so there's no need for any additional data store.

  http://www.grafana.org

Check data
==========

Workers will send all their data to KairosDB. Depending on the KairosDB setting, data is stored forever or you may set a TTL in KairosDB. ZMON will not clean up or roll up any data.

Serialization
-------------

For checks retrieving only numeric values data storage in KairosDB is easy. But the worker will also flatten more complex result types and persist them in KairosDB. At Zalando checks that yield only single numeric values have become quite rare.

If the check returns dictionary the worker will try to flatten its structure and persist all entries with a numeric value.

.. code-block:: json

    {
        "load": {"1min":1,"5min":3,"15min":2}
        "memory_free": 16000
    }

Will be flattened to an equivalent of

.. code-block:: json

    {
        "load.1min": 1,
        "load.5min": 3,
        "load.15min": 2,
        "memory_free": 16000
    }

Always yielding a simple dictionary with (key, value) pairs.

Tagging
-------

KairosDB creates timer series with a name and allows us to tag data points with additional (tagname, tagvalue) pairs.

ZMON stores all data to a single check in a time series named: "zmon.check.<checkid>".

Single data points are then tagged as follows to describe their contents:

 * entity: containing the entity id (some character replace rules are applied)
 * key: containing the dict key after serialization of check value (see above)
 * metric: contains the last segment of "key" split by "." (making selection easier in tooling)
 * hg: host group(hg) will contain a substring of the entity id, to try to group e.g. cassandra01 and cassandra02 into hg=cassandra

For a certrain set of metrics additional tags may be deployed(REST metrics/actuator)

 * sc: HTTP status code
 * sg: first digit of HTTP status code

Some of the tagging may seem strange, but as KairosDB does not allow real operations on tags they are basically precreated to allow easier filtering in the tools/charts. This is also fine from a storage/performance point of view during writes, as KairosDB's cassandra implementation createds a new row for each unique tuple (time series name, set of tags) thus this is only stored once.
