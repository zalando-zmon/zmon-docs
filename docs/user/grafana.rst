.. _grafana:

*********************
Grafana3 and KairosDB
*********************

Grafana is a powerful open-source tool for creating dashboards to visualize metric data.
ZMON deploys Grafana 3.x along with the new KairosDB plugin to read metric data from KairosDB.
Grafana is served directly from the ZMON controller.
Read requests are proxied through the controller so as not to expose the write/delete API from KairosDB.
Dashboards are also saved via the controller, so there's no need for any additional data store.

  http://grafana.org

 Example of latency and requests charted via Grafana:

 .. image:: /images/grafana-example1.png

Check data
==========

Workers will send all their data to KairosDB. Depending on the KairosDB setting, data is stored forever or you may set a TTL in KairosDB. ZMON will not clean up or roll up any data.

Serialization
-------------

In the simplest case you would have a check producing a single numeric value.
In Zalando's experience this is very rare.

Zmon also supports arbitrarily nested dictionaries of numeric values.
Anything that is not a dictionary or a number will be silently dropped.
The value is flattened into a single-level dictionary such that the elements can be stored in KairosDB (key-value storage).

.. code-block:: json

    {
        "load": {"1min":1,"5min":3,"15min":2},
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

You might also want to output a list. The simple workaround is to generate a dictionary whose
keys are some identifier extracted from the elements.

e.g. transform this list:

.. code-block:: json

{
  "partitions": [
    {
      "count": 2254839,
      "partition": "0",
      "stream_id": "55491eb8-3ccc-40c5-b7c6-69bf38df3e16"
    },
    {
      "count": 2029956,
      "partition": "1",
      "stream_id": "aa938451-d115-4e90-a5da-1ac4b435a4e9"
    },

into the following dictionary:

.. code-block:: json

{
  "partitions": {
    "0": {
      "count": 2254839,
      "partition": "0",
      "stream_id": "55491eb8-3ccc-40c5-b7c6-69bf38df3e16"
    },
    "1": {
      "count": 2029956,
      "partition": "1",
      "stream_id": "aa938451-d115-4e90-a5da-1ac4b435a4e9"
    },

this will be stored the same way as the value (remember that strings are dropped):

.. code-block:: json

    {
        "partitions.0.count": 2254839,
        "partitions.1.count": 2029956
    }

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

Some of the tagging may seem strange, but as KairosDB does not allow real operations on tags they are basically precreated to allow easier filtering in the tools/charts.
This is also fine from a storage/performance point of view during writes, as KairosDB's Cassandra implementation creates a new row for each unique tuple (time series name, set of tags) thus this is only stored once.
