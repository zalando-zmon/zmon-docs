.. _check-kairosdb:

KairosDB
--------

Provides read access to the target KairosDB


.. py:function:: kairosdb(url, oauth2=False)


Methods of KairosDB
^^^^^^^^^^^^^^^^^^^

.. py:function:: query(name, group_by = None, tags = None, start = -5, end = 0, time_unit='seconds', aggregators = None, start_absolute = None, end_absolute = None)

    Query kairosdb.

    :param name: Metric name.
    :type name: str

    :param group_by: List of fields to group by.
    :type group_by: list

    :param tags: Filtering tags. Example of `tags` object:

        .. code-block:: python

            {
                "key": ["max"]
            }

    :type tags: dict

    :param start: Relative start time. Default is 5. Should be greater than or equal 1.
    :type start: int

    :param end: End time. Default is 0. If not 0, then it should be greater than or equal to 1.
    :type end: int

    :param time_unit: Time unit ('seconds', 'minutes', 'hours'). Default is 'minutes'.
    :type time_unit: str

    :param aggregators: List of aggregators. Aggregator is an object that looks like

        .. code-block:: python

            {
                "name": "max",
                "sampling": {
                    "value": "1",
                    "unit": "minutes"
                },
                "align_sampling": true
            }

    :type aggregators: list

    :param start_absolute: Absolute start time in milliseconds, overrides the start parameter which is relative
    :type start_absolute: long

    :param end_absolute: Absolute end time in milliseconds, overrides the end parameter which is relative
    :type end_absolute: long

    :return: Result queries.
    :rtype: dict


.. py:function:: query_batch(self, metrics, start=5, end=0, time_unit='minutes', start_absolute=None, end_absolute=None)

    Query kairosdb for several checks at once.

    :param metrics: list of KairosDB metric queries, one query per metric name, e.g.

        .. code-block:: python

            [
                {
                    'name': 'metric_name',      # name of the metric
                    'group_by': ['foo'],        # list of fields to group by
                    'aggregators': [            # list of aggregator objects
                        {                       # structure of a single aggregator
                            'name': 'max',
                            'sampling': {
                                'value': '1',
                                'unit': 'minutes'
                            },
                            'align_sampling': True
                        }
                    ],
                    'tags': {                   # dict with filtering tags
                        'key': ['max']          # a key is a tag name, list of values is used to filter
                                                # all the records with given tag and given values
                    }
                }
            ]

    :type metrics: dict

    :param start: Relative start time. Default is 5.
    :type start: int

    :param end: End time. Default is 0.
    :type end: int

    :param time_unit: Time unit ('seconds', 'minutes', 'hours'). Default is 'minutes'.
    :type time_unit: str

    :param start_absolute: Absolute start time in milliseconds, overrides the start parameter which is relative
    :type start_absolute: long

    :return: Array of results for each queried metric
    :rtype: list
