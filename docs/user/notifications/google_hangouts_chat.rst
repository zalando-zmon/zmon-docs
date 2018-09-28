Google Hangouts Chat
-------

Notify Google Hangouts Chat room with alert status.

.. py:function:: send_google_hangouts_chat(webhook_link=None, message=None, color='red')

    Send Google Hangouts Chat notification.
    
    :param webhook_link: Webhook Link in Google Hangouts Chat Room. Create a `Google Hangouts Chat Webhook`_ and copy the link here.
    :type webhook_link: str
    
    :param multiline: Should the Text in the notification span multiple lines or not? Default is ``True``.
    :type multiline: bool

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str

    :param color: Message color. Default is ``red`` if alert is raised.
    :type color: str

.. note::

    Message color will be determined based on alert status. If alert has ended, then ``color`` will be ``green``, otherwise ``color`` argument will be used.

.. _Google Hangouts Chat Webhook: https://developers.google.com/hangouts/chat/how-tos/webhooks
