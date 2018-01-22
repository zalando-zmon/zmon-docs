Elasticsearch
-------------

Provides search queries and health check against an Elasticsearch cluster.


.. py:function:: elasticsearch(url=None, timeout=10, oauth2=False)

.. note::

    If ``url`` is **None**, then the plugin will use the default Elasticsearch cluster set in worker configuration.

Methods of Elasticsearch
^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: search(indices=None, q='', body=None, source=True, size=DEFAULT_SIZE)

    Search ES cluster using URI or Request body search. If ``body`` is None then GET request will be used.

    :param indices: List of indices to search. Limited to only 10 indices. ['_all'] will search all available
                    indices, which effectively leads to same results as `None`. Indices can accept wildcard form.
    :type indices: list

    :param q: Search query string. Will be ignored if ``body`` is not None.
    :type  q: str

    :param body: Dict holding an ES query DSL.
    :type body: dict

    :param source: Whether to include `_source` field in query response.
    :type source: bool

    :param size: Number of hits to return. Maximum value is 1000. Set to 0 if interested in hits count only.
    :type size: int

    :return: ES query result.
    :rtype: dict

    Example query:

    .. code-block:: python

        elasticsearch('http://es-cluster').search(indices=['logstash-*'], q='client:192.168.20.* AND http_status:500', size=0, source=False)

        {
            "_shards": {
                "failed": 0,
                "successful": 5,
                "total": 5
            },
            "hits": {
                "hits": [],
                "max_score": 0.0,
                "total": 1
            },
            "timed_out": false,
            "took": 2
        }

.. py:function:: count(indices=None, q='', body=None)

    Return ES count of matching query.

    :param indices: List of indices to search. Limited to only 10 indices. ['_all'] will search all available
                    indices, which effectively leads to same results as `None`. Indices can accept wildcard form.
    :type indices: list

    :param q: Search query string. Will be ignored if ``body`` is not None.
    :type  q: str

    :param body: Dict holding an ES query DSL.
    :type body: dict

    :return: ES query result.
    :rtype: dict

    Example query:

    .. code-block:: python

        elasticsearch('http://es-cluster').count(indices=['logstash-*'], q='client:192.168.20.* AND http_status:500')

        {
            "_shards": {
                "failed": 0,
                "successful": 16,
                "total": 16
            },
            "count": 12
        }

.. py:method:: health()

    Return ES cluster health.

    :return: Cluster health result.
    :rtype: dict

    .. code-block:: python

        elasticsearch('http://es-cluster').health()

        {
            "active_primary_shards": 11,
            "active_shards": 11,
            "active_shards_percent_as_number": 50.0,
            "cluster_name": "big-logs-cluster",
            "delayed_unassigned_shards": 0,
            "initializing_shards": 0,
            "number_of_data_nodes": 1,
            "number_of_in_flight_fetch": 0,
            "number_of_nodes": 1,
            "number_of_pending_tasks": 0,
            "relocating_shards": 0,
            "status": "yellow",
            "task_max_waiting_in_queue_millis": 0,
            "timed_out": false,
            "unassigned_shards": 11
        }
