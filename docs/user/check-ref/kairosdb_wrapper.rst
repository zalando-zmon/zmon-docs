KairosDB
--------

Provides read access to the target KairosDB


.. py:function:: kairosdb(url, oauth2=False)


Methods of KairosDB
^^^^^^^^^^^^^^^^^^^

.. py:function:: query(name, group_by = None, tags = None, start = -5, end = 0, time_unit='seconds', aggregators = None)

    ::

    Query kairosdb.

    :param name: Metric name.
    :type name: str

    :param group_by: List of fields to group by. Currently ignored.
    :type group_by: list

    :param tags: Filtering tags.
    :type tags: dict

    :param start: Relative start time. Default is 5. Should be greater than or equal 1.
    :type start: int

    :param end: End time. Default is 0. If not 0, then it should be greater than or equal to 1.
    :type end: int

    :param time_unit: Time unit ('seconds', 'minutes', 'hours'). Default is 'minutes'.
    :type time_unit: str.

    :param aggregators: List of aggregators.
    :type aggregators: list

    :return: Result queries.
    :rtype: dict
