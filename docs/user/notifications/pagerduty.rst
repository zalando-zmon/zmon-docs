Pagerduty
---------

Notify `Pagerduty <https://www.pagerduty.com/>`_ of a new alert status. If alert is **active**, then a new pagerduty incident with type ``trigger`` will be sent. If alert is **inactive** then incident type will be updated to ``resolve``.

.. note::

    Pagerduty notification plugin uses API v2.


.. py:function:: notify_pagerduty(message='', per_entity=False, include_alert=True, routing_key=None, alert_class=None, alert_group=None, **kwargs)

    Send notifications to Pagerduty.

    :param message: Incident message. If empty, then a message will be generated from the alert data.
    :type message: str

    :param per_entity: Send new alert per entity. This affects the ``dedup_key`` value and impacts how de-duplication is handled in Pagerduty. Default is ``False``.
    :type per_entity: bool

    :param include_alert: Include alert data in incident payload ``custom_details``. Default is ``True``.
    :type include_alert: bool

    :param routing_key: Pagerduty service ``routing_key``. If not specified, then the :ref:`service key configured <notification-options-label>` for the worker will be used.
    :type routing_key: str

    :param alert_class: Set the Pagerduty incident class.
    :type alert_class: str

    :param alert_group: Set the Pagerduty incident group.
    :type alert_group: str

    Example:

    .. code-block:: python

        notify_pagerduty(message='Number of failed requests is too high!', include_alert=True, alert_class='API health', alert_group='production')
