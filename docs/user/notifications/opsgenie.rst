Opsgenie
--------

Notify `Opsgenie <https://www.opsgenie.com/>`_ of a new alert status. If alert is **active**, then a new opsgenie alert will be created. If alert is **inactive** then the alert will be closed.


.. py:function:: notify_opsgenie(message='', per_entity=False, include_alert=True, **kwargs)

    ::

    Send notifications to Opsgenie.

    :param message: Alert message. If empty, then a message will be generated from the alert data.
    :type message: str

    :param per_entity: Send new alert per entity. This affects the ``alias`` value and impacts how de-duplication is handled in Opsgenie. Default is ``False``.
    :type per_entity: bool

    :param include_alert: Include alert data in alert body ``details``. Default is ``True``.
    :type include_alert: bool

    Example:

    .. code-block:: python

        notify_opsgenie(message='Number of failed requests is too high!', include_alert=True)
