.. _check-commands:

***********************
Check Command Reference
***********************

To give an overview of available commands, we divided them into several
categories.

.. include:: check-ref/appdynamics_wrapper.rst
.. include:: check-ref/cassandra_wrapper.rst
.. include:: check-ref/cloudwatch_wrapper.rst
.. include:: check-ref/counter_wrapper.rst
.. include:: check-ref/dns_wrapper.rst
.. include:: check-ref/elastic_search_wrapper.rst
.. include:: check-ref/entities_wrapper.rst
.. include:: check-ref/eventlog_wrapper.rst
.. include:: check-ref/history_wrapper.rst
.. include:: check-ref/http_wrapper.rst
.. include:: check-ref/jmx_wrapper.rst
.. include:: check-ref/kairosdb_wrapper.rst
.. include:: check-ref/kubernetes_wrapper.rst
.. include:: check-ref/ldap_wrapper.rst
.. include:: check-ref/mongodb_wrapper.rst
.. include:: check-ref/nagios_wrapper.rst
.. include:: check-ref/ping_wrapper.rst
.. include:: check-ref/redis_wrapper.rst
.. include:: check-ref/s3_wrapper.rst
.. include:: check-ref/scalyr_wrapper.rst
.. include:: check-ref/snmp_wrapper.rst
.. include:: check-ref/sql_wrappers.rst
.. include:: check-ref/tcp_wrapper.rst
.. include:: check-ref/zomcat_wrapper.rst

Helper Functions
----------------

The following general-purpose functions are available in check commands:

.. py:function:: abs(number)

    Returns the absolute value of the argument. Does not have overflow issues. ::

        >>> abs(-1)
        1
        >>> abs(1)
        1
        >>> abs(-2147483648)
        2147483648


.. py:function:: all(iterable)

    Returns ``True`` if none of the elements of `iterable` are falsy. ::

        >>> all([4, 2, 8, 0, 3])
        False

        >>> all([])
        True


.. py:function:: any(iterable)

    Returns ``True`` if at least one element of `iterable` is truthy. ::

        >>> any([None, [], '', {}, 0, 0.0, False])
        False

        >>> any([])
        False


.. py:function:: avg(results)

    Returns the arithmetic mean of the values in `results`. Returns ``None`` if there are no values. `results` must not be an iterator. ::

        >>> avg([1, 2, 3])
        2.0

        >>> avg([])
        None


.. py:function:: basestring

    Superclass of ``str`` and ``unicode`` useful for checking whether a value is a string of some sort. ::

        >>> isinstance('foo', basestring)
        True
        >>> isinstance(u'ˈ', basestring)
        True


.. py:function:: bin(n)

    Returns a string of the given integer in binary representation.

        >>> bin(1000)
        '0b1111101000'


.. py:function:: bool(x)

    Returns ``True`` if `x` is truthy, and ``False`` otherwise. Does not parse strings. Also usable to check whether a value is Boolean. ::

        >>> bool(None)
        False

        >>> bool('False')
        True

        >>> isinstance(False, bool)
        True


.. py:function:: chain(*iterables)

    Returns an iterator that that yields the elements of the first iterable, followed by the elements of the second iterable, and so on. ::

        >>> list(chain([1, 2, 3], 'abc'))
        [1, 2, 3, 'a', 'b', 'c']

        >>> list(chain())
        []


.. py:function:: chr(n)

    Returns the character for the given ASCII code.

        >>> chr(65)
        'A'


.. py:class:: Counter([iterable-or-mapping])

    Creates a specialized ``dict`` for counting things. See `the official Python documentation <http://docs.python.org/2/library/collections.html#collections.Counter>`__ for details.


.. py:function:: dict([mapping], [**kwargs])

    Creates a new ``dict``. Usually, using a literal will be simpler, but the function may be useful to copy ``dict``\ s, to covert a list of key/value pairs to a ``dict``, or to check whether some object is a ``dict``.  ::

        >>> dict(a=1, b=2, c=3)
        {'a': 1, 'c': 3, 'b': 2}

        >>> dict({'a': 1, 'b': 2, 'c': 3})
        {'a': 1, 'c': 3, 'b': 2}   # This is a copy of the original dict.

        >>> dict([['a', 1], ['b', 2], ['c', 3]])
        {'a': 1, 'c': 3, 'b': 2}

        >>> isinstance({}, dict)
        True


.. py:function:: divmod(x, y):

    Performs integer division and modulo as a single operation. ::

        >>> divmod(23, 5)
        (4, 3)


.. py:function:: empty(v)

    Indicates whether `v` is falsy. Equivalent to ``not v``. ::

        >>> empty([])
        True

        >>> empty([0])
        False


.. py:function:: enumerate(iterable, [start=0])

    Generates tuples ``(start + 0, iterable[0]), (start + 1, iterable[1]), ...``. Useful to have access to the index in a loop. ::

        >>> list(enumerate(['a', 'b', 'c'], start=1))
        [(1, 'a'), (2, 'b'), (3, 'c')]


.. py:function:: filter(function, iterable)

    Returns a list of all objects in `iterable` for which `function` returns a truthy value. If function is ``None``, the returned list contains all truthy objects in `iterable`. ::

        >>> filter(lambda n: n % 3, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
        [1, 2, 4, 5, 7, 8, 10]

        >>> filter(None, [False, None, 0, 0.0, '', [], {}, 1000])
        [1000]


.. py:function:: float(x)

    Returns `x` as a floating-point number. Parses stings. ::

        >>> float('2.5')
        2.5

        >>> float('-inf')
        -inf

        >>> float(2)
        2.0

    This is useful to force proper division::

        >>> 2 / 5
        0

        >>> float(2) / 5
        0.4

    Also usable to check whether a value is a floating-point number::

        >>> isinstance(2.5, float)
        True

        >>> isinstance(2, float)
        False


.. py:function:: groupby(iterable, [key])

    A somewhat obscure function for grouping consecutive equal elements in an iterable. See `the official Python documentation <http://docs.python.org/2/library/itertools.html#itertools.groupby>`__ for more details. ::

        >>> [(k, list(v)) for k, v in groupby('abba')]
        [('a', ['a']), ('b', ['b', 'b']), ('a', ['a'])]


.. py:function:: hex(n)

    Returns a string of the given integer in hexadecimal representation.

        >>> hex(1000)
        '0x3e8'


.. py:function:: int(x, [base])

    Returns `x` as an integer. Truncates floating-point numbers and parses strings. Also usable to check whether a value is an integer. ::

        >>> int(2.5)
        2

        >>> int(-2.5)
        2

        >>> int('2')
        2

        >>> int('abba', 16)
        43962

        >>> isinstance(2, int)
        True


.. py:function:: isinstance(object, classinfo)

    Indicates whether `object` is an instance of the given class or classes. ::

        >>> isinstance(2, int)
        True

        >>> isinstance(2, (int, float))
        True

        >>> isinstance('2', int)
        False


.. py:function:: json(s)

    Converts the given :term:`JSON` string to a Python object.

        >>> json('{"list": [1, 2, 3, 4]}')
        {u'list': [1, 2, 3, 4]}


.. py:function:: jsonpath_flat_filter(obj, path)

    Executes json path expression using `jsonpath_rw`_ and returns a flat dict of (full_path, value). ::

        >>> data = {"timers":{"/api/v1/":{"m1.rate": 12, "99th": "3ms"}}}
        >>> jsonpath_flat_filter(data, "timers.*.*.'m1.rate'")
        {"timers./api/v1/.m1.rate": 12}

.. py:function:: jsonpath_parse(path)

    Creates a json path parse object from the `jsonpath_rw`_ to be used in your check command.

.. _jsonpath_rw: https://github.com/kennknowles/python-jsonpath-rw

.. py:function:: len(s)

    Returns the length of the given collection. ::

        >>> len('foo')
        3

        >>> len([0, 1, 2])
        3

        >>> len({'a': 1, 'b': 2, 'c': 3})
        3


.. py:function:: list(iterable)

    Creates a new list. Usually, using a literal will be simpler, but the function may be useful to copy lists, to covert some other iterable to a list, or to check whether some object is a list. ::

        >>> list({'a': 1, 'b': 2, 'c': 3})
        ['a', 'c', 'b']

        >>> list(chain([1, 2, 3], 'abc'))
        [1, 2, 3, 'a', 'b', 'c']   # Without the list call, this would be a chain object.

        >>> isinstance([1, 2, 3], list)
        True


.. py:function:: long(x, [base])

    Converts a number or string to a long integer. ::

        >>> long(2.5)
        2L

        >>> long(-2.5)
        -2L

        >>> long('2')
        2L

        >>> long('abba', 16)
        43962L


.. py:function:: map(function, iterable)

    Calls `function` on each element of `iterable` and returns the results as a list. ::

        >>> map(lambda n: n**2, [0, 1, 2, 3, 4, 5])
        [0, 1, 4, 9, 16, 25]


.. py:function:: max(iterable)

    Returns the greatest element of `iterable`. With two or more arguments, returns the greatest argument instead. ::

        >>> max([2, 4, 1, 3])
        4

        >>> max(2, 4, 1, 3)
        4


.. py:function:: min(iterable)

    Returns the smallest element of `iterable`. With two or more arguments, returns the smallest argument instead. ::

        >>> min([2, 4, 1, 3])
        1

        >>> min(2, 4, 1, 3)
        1


.. py:function:: normalvariate(mu, sigma)

    Returns a normally distributed random variable with the given mean and standard derivation. ::

        >>> normalvariate(0, 1)
        -0.1711153439880709


.. py:function:: oct(n)

    Returns a string of the given integer in octal representation.

        >>> oct(1000)
        '01750'


.. py:function:: ord(n)

    Returns the ASCII code of the given character.

        >>> ord('A')
        65


.. py:function:: pow(x, y, [z])

    Computes `x` to the power of `y`. The result is modulo `z`, if `z` is given, and the function is much, much faster than ``(x ** y) % z`` in that case.

        >>> pow(56876845793546543243783543735425734536873, 12425445412439354354394354397364398364378, 10)
        9L


.. py:function:: range([start], stop, [step])

    Returns a list of integers ``[start, start + step * 1, start + step * 2, ...]`` where all integers are less than `stop`, or greater than `stop` if `step` is negative.

        >>> range(10)
        [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
        >>> range(1, 11)
        [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
        >>> range(1, 1)
        []
        >>> range(11, 1)
        []
        >>> range(0, 10, 3)
        [0, 3, 6, 9]
        >>> range(10, -1, -1)
        [10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0]


.. py:function:: reduce(function, iterable, [initializer])

    Calls ``function(r, e)`` for each element ``e`` in `iterable`, where ``r`` is the result of the last such call, or `initializer` for the first such call. If `iterable` has no elements, returns `initializer`.

    If `initializer` is ommitted, the first element of `iterable` is removed and used in place of `initializer`. In that case, an error is raised if `iterable` has no elements. ::

        >>> reduce(lambda a, b: a * b, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 1)
        3628800  # 10!

    Note: Because of a Python bug, ``reduce`` used to be unreliable. This issue should now be fixed.


.. py:function:: reversed(iterable)

    Returns an iterator that iterates over the elements in `iterable` in reverse order.

        >>> list(reversed([1, 2, 3]))
        [3, 2, 1]


.. py:function:: round(n, [digits=0])

    Rounds the given number to the given number of digits, rounding half away from zero.

        >>> round(23.4)
        23.0
        >>> round(23.5)
        24.0
        >>> round(-23.4)
        -23.0
        >>> round(-23.5)
        -24.0
        >>> round(0.123456789, 3)
        0.123
        >>> round(987654321, -3)
        987654000.0


.. py:function:: set(iterable)

    Returns a set built from the elements of `iterable`. Useful to remove duplicates from some collection. ::

        >>> set([1, 2, 1, 4, 3, 2, 2, 3, 4, 1])
        set([1, 2, 3, 4])


.. py:function:: sorted(iterable, [reverse])

    Returns a sorted list containing the elements of `iterable`. ::

        >>> sorted([2, 4, 1, 3])
        [1, 2, 3, 4]

        >>> sorted([2, 4, 1, 3], reverse=True)
        [4, 3, 2, 1]


.. py:function:: str(object)

    Returns the string representation of `object`. Also usable to check whether a value is a string. If the result would contain Unicode characters, the :py:func:`unicode` function must be used instead. ::

        >>> str(2)
        '2'

        >>> str({'a': 1, 'b': 2, 'c': 3})
        "{'a': 1, 'c': 3, 'b': 2}"

        >>> isinstance('foo', str)
        True


.. py:function:: sum(iterable)

    Returns the sum of the elements of `iterable`, or ``0`` if `iterable` is empty. ::

        >>> sum([1, 2, 3, 4])
        10

        >>> sum([])
        0

.. _time-helper-function:
.. py:function:: time([spec], [utc])

    Given a time specification such as ``'-10m'`` for "ten minutes ago" or ``'+3h'`` for "in three hours", returns an object representing that timestamp. Valid units are ``s`` for seconds, ``m`` for minutes, ``h`` for hours, and ``d`` for days.

    The time specification `spec` can also be a Unix epoch/timestamp or a valid ISO timestamp in of the following formats:
    ``YYYY-MM-DD HH:MM:SS.mmmmm``,
    ``YYYY-MM-DD HH:MM:SS``,
    ``YYYY-MM-DD HH:MM`` or
    ``YYYY-MM-DD``.

    If `spec` is omitted, the current time is used. If `utc` is True the timestamp uses UTC, otherwise it uses local time.

    The returned object has two methods:

    .. py:method:: isoformat([sep])

        Returns the timestamp as a string of the form ``YYYY-MM-DD HH:MM:SS.mmmmmm``. The default behavior is to omit the ``T`` between date and time. This can be overridden by passing the optional `sep` parameter to the method. ::

            >>> time('+4d').isoformat()
            '2014-03-29 18:05:50.098919'

            >>> time(1396112750).isoformat()
            '2014-03-29 18:05:50'

            >>> time('+4d').isoformat('T')
            '2014-03-29T18:05:50.098919'

    .. py:method:: format(fmt)

        Returns the timestamp as a string formatted according to the given format. See `the official Python documentation <http://docs.python.org/2/library/datetime.html#strftime-strptime-behavior>`__ for an incomplete list of supported format directives.

    Additionally, the subtraction operator is overloaded and returns the time difference in seconds::

        >>> time('2014-01-01 01:13') - time('2014-01-01 01:01')
        12

.. py:function:: timestamp()

    Returns Unix time stamp. This wraps time.time()


.. py:function:: tuple(iterable)

    Returns the given iterable as a tuple (an immutable list, basically). Also usable to check whether a value is a tuple. ::

        >>> tuple([1, 2, 3])
        (1, 2, 3)
        >>> isinstance((1, 2, 3), tuple)
        True


.. py:function:: unicode(object)

    Returns the string representation of `object` as a Unicode string. Also usable to check whether a value is a Unicode string. ::

        >>> unicode({u'α': 1, u'β': 2, u'γ': 3})
        u"{u'\\u03b1': 1, u'\\u03b3': 3, u'\\u03b2': 2}"

        >>> isinstance(u'ˈ', unicode)
        True


.. py:function:: unichr(n)

    Returns the unicode character with the given code point. Might be limited to code points less than 0x10000.

        >>> unichr(0x2a13)  # LINE INTEGRATION WITH SEMICIRCULAR PATH AROUND POLE
        u'⨓'


.. py:function:: xrange([start], stop, [step])

    As :py:func:`range`, but returns an iterator rather than a list.


.. py:function:: zip(*iterables)

    Returns a list of tuples where the `i`\ -th tuple contains the `i`\ -th element from each of the given iterables. Uses the lowest length if the iterables have different lengths.

        >>> zip(['a', 'b', 'c'], [1, 2, 3])
        [('a', 1), ('b', 2), ('c', 3)]
        >>> zip(['A', 'B', 'C'], ['a', 'b', 'c'], [1, 2, 3])
        [('A', 'a', 1), ('B', 'b', 2), ('C', 'c', 3)]
        >>> zip([], [1, 2, 3])
        []

.. py:function:: re

    ::

    Python regex ``re`` module for all regex operations.

    .. code-block:: python

        >>> re.match(r'^ab.*', 'a123b') != None
        False

        >>> re.match(r'^ab.*', 'ab123') != None
        True

.. py:function:: math

    ::

    Python ``math`` module for all math operations.

    .. code-block:: python

        >>> math.log(4, 2)
        2.0
