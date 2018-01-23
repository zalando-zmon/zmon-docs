Slack
-----

Notify Slack channel with alert status. A ``webhook`` is required for notifications.

.. py:function:: notify_slack(webhook=None, channel='#general', message=None)

    Send Slack notification to specified channel.

    :param webhook: Slack webhook. If not set, then webhook set in configuration will be used.
    :type webhook: str

    :param channel: Channel to be notified. Default is ``#general``.
    :type channel: str

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str
