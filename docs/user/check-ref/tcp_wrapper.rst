TCP
---

This function opens a TCP connection to a host on a given port. If the
connection succeeds, it returns ‘OK’. The host can be provided directly for global checks or resolved from
entities filter. Assuming that we have an entity filter type=host, the
example below will try to connect to every host on port 22::

    tcp().open(22)
