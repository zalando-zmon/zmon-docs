Pagerduty
---------

Notify `Pagerduty <https://www.pagerduty.com/>`_ of a new alert status. If the alert ended then notification will ``resolve`` pagerduty incident.


.. py:function:: notify_pagerduty(message='', per_entity=False, include_alert=True, **kwargs)

    ::

    Send notifications to Pagerduty.

    :param message: Incident message. If empty, then a message will be generated from the alert data.
    :type message: str

    :param per_entity: Send new alert per entity. This affects the ``incident_key`` value and impacts how de-duplication is handled in Pagerduty. Default is ``False``.
    :type per_entity: bool

    :param include_alert: Include alert data in incident body ``details``. Default is ``True``.
    :type include_alert: bool

    Example:

    .. code-block:: python

        notify_pagerduty(message='Number of failed requests is too high!', include_alert=True)
