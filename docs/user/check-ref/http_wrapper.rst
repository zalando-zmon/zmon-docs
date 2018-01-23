HTTP
----

Access to HTTP (and HTTPS) endpoints is provided by the :py:func:`http` function.

.. py:function:: http(url, [method='GET'], [timeout=10], [max_retries=0], [verify=True], [oauth2=False], [allow_redirects=None], [headers=None])

    :param str url: The URL that is to be queried. See below for details.
    :param str method: The HTTP request method. Allowed values are ``GET`` or ``HEAD``.
    :param float timeout: The timeout for the HTTP request, in seconds. Defaults to :py:obj:`10`.
    :param int max_retries: The number of times the HTTP request should be retried if it fails. Defaults to :py:obj:`0`.
    :param bool verify: Can be set to :py:obj:`False` to disable SSL certificate verification.
    :param bool oauth2: Can be set to :py:obj:`True` to inject a OAuth 2 ``Bearer`` access token in the outgoing request
    :param str oauth2_token_name: The name of the OAuth 2 token. Default is ``uid``.
    :param bool allow_redirects: Follow request redirects. If ``None`` then it will be set to :py:obj:`True` in case of ``GET`` and :py:obj:`False` in case of ``HEAD`` request.
    :param dict headers: The headers to be used in the HTTP request.
    :return: An object encapsulating the response from the server. See below.

        For checks on entities that define the attributes :py:attr:`url` or :py:attr:`host`, the given URL may be relative. In that case, the URL :samp:`http://<{value}><{url}>` is queried, where :samp:`<{value}>` is the value of that attribute, and :samp:`<{url}>` is the URL passed to this function. If an entity defines both :py:attr:`url` and :py:attr:`host`, the former is used.

    This function cannot query URLs using a scheme other than HTTP or HTTPS; URLs that do not start with :samp:`http://` or :samp:`https://` are considered to be relative.

    Example:

        .. code-block:: python

            http('http://www.example.org/data?fetch=json').json()

            # avoid raising error in case the response error status (e.g. 500 or 503)
            # but you are interested in the response json
            http('http://www.example.org/data?fetch=json').json(raise_error=False)


HTTP Responses
^^^^^^^^^^^^^^

The object returned by the :py:func:`http` function provides methods: :py:meth:`json`, :py:meth:`text`, :py:meth:`headers`, :py:meth:`cookies`, :py:meth:`content_size`, :py:meth:`time` and :py:meth:`code`.

.. py:method:: json(raise_error=True)

    This method returns an object representing the content of the JSON response from the queried endpoint. Usually, this will be a map (represented by a Python :py:obj:`dict`), but, depending on the endpoint, it may also be a list, string, set, integer, floating-point number, or Boolean.

.. py:method:: text(raise_error=True)

    Returns the text response from queried endpoint::

        http("/heartbeat.jsp", timeout=5).text().strip()=='OK: JVM is running'

    Since we’re using a relative url, this check has to be defined for
    specific entities (e.g. type=zomcat will run it on all zomcat
    instances). The strip function removes all leading and trailing
    whitespace.

.. py:method:: headers(raise_error=True)

    Returns the response headers in a case-insensitive dict-like object::

        http("/api/json", timeout=5).headers()['content-type']=='application/json'

.. py:method:: cookies(raise_error=True)

    Returns the response cookies in a dict like object::

        http("/heartbeat.jsp", timeout=5).cookies()['my_custom_cookie'] == 'custom_cookie_value'

.. py:method:: content_size(raise_error=True)

    Returns the length of the response content::

        http("/heartbeat.jsp", timeout=5).content_size() > 1024

.. py:method:: time(raise_error=True)

    Returns the elapsed time in seconds until response was received::

        http("/heartbeat.jsp", timeout=5).time() > 1.5

.. py:method:: code()

    Return HTTP status code from the queried endpoint.::

        http("/heartbeat.jsp", timeout=5).code()

.. _http-actuator:

.. py:method:: actuator_metrics(prefix='zmon.response.', raise_error=True)

    Parses the json result of a metrics endpoint into a map ep->method->status->metric

        http("/metrics", timeout=5).actuator_metrics()

.. _http-prometheus:

.. py:method:: prometheus()

    Parse the resulting text result according to the Prometheus specs using their prometheus_client.

        http("/metrics", timeout=5).prometheus()

.. _http-jolokaia:

.. py:method:: jolokaia(read_requests, raise_error=False)

    Does a POST request to the endpoint given in the wrapper, with validating the endpoint and setting
    the request to be read-only.

    :param read_requests: see https://jolokia.org/reference/html/protocol.html#post-request
    :type read_requests: list
    :param raise_error: bool
    :return: Jolokia response

    Example:

        .. code-block:: python

            requests = [
                {'mbean': 'org.apache.cassandra.metrics:type=ClientRequest,scope=Read,name=Latency'},
                {'mbean': 'org.apache.cassandra.metrics:type=ClientRequest,scope=Write,name=Latency'},
            ]
            results = http('http://{}:8778/jolokia/'.format(entity['ip']), timeout=15).jolokia(requests)
