Twilio
------

Use Twilio to receive phone calls if alerts pop up. This includes basic ACK and escalation. Requires account at Twilio and the notifiction service deployed. Low investment to get going though. WORK IN PROGRESS.

.. py:function:: notifiy_twilio(numbers=[], message="ZMON Alert Up: Some Alert")

    ::

    Make phone call to supplied numbers. First number will be called immediately. After two minutes, another call is made to that number if no ACK. Other numbers follow at 5min interval without ACK.

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str

    :param numbers: Numbers to call
    :type numers: list
