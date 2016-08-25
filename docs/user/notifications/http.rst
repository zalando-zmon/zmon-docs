HTTP
----

Provides notification by invoking HTTP call to certain endpoint. HTTP notification uses ``POST`` method when invoking the call.


.. py:function:: notify_http(url=None, body=None, params=None, headers=None, timeout=5, oauth2=False, include_alert=True)

    ::

    Send HTTP notification to specified endpoint.

    :param url: HTTP endpoint URL. If not passed, then default URL will be used in worker configuration.
    :type url: str

    :param body: Request body.
    :type body: dict

    :param params: Request URL params.
    :type params: dict

    :param headers: HTTP headers.
    :type headers: dict

    :param timeout: Request timeout. Default is 5 seconds.
    :type timeout: int

    :param oauth2: Add OAUTH2 authentication headers. Default is False.
    :type oauth2: bool

    :param include_alert: Include alert data in request body. Default is ``True``.
    :type include_alert: bool

    Example:

    .. code-block:: python

        notify_http('https://some-notification-service/alert', body={'zmon': True}, headers={'X-TOKEN': 1234})


.. note::

    If ``include_alert`` is ``True``, then request body will include alert data. This is usually useful, since it provides valuable info like ``is_alert`` and ``changed`` which can indicate whether the alert has **started** or **ended**.

    .. code-block:: python

        {
            "body": null,
            "alert": {
                "is_alert": true,
                "changed": true,
                "duration": 2.33,
                "captures": {},
                "entity": {"type": "GLOBAL", "id": "GLOBAL"},
                "worker": "plocal.zmon",
                "value": {"td": 0.00037, "worker": "plocal.zmon", "ts": 1472032348.665247, "value": 51.67797677979191},
                "alert_def": {
                    "name": "Random Example Alert", "parameters": null, "check_id": 4, "entities_map": [], "responsible_team": "ZMON", "period": "", "priority": 1,
                    "notifications": ["notify_http()"], "team": "ZMON", "id": 3, "condition": ">40"
                }
            }
        }
