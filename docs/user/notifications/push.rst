Push
-----

Send push notification via ZMON `notification service <https://github.com/zalando-zmon/zmon-notification-service>`_.

.. py:function:: send_push(url=None, key=None, message=None)

    ::

    Send Push notification to mobile devices.

    :param url: Notification service base URL.
    :type url: str

    :param key: Notification service API key.
    :type key: str

    :param message: Message to be sent in notification.
    :type message: str

.. note::

    If Message is ``None`` then it will be generated from alert status.

