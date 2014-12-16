.. _python-tutorial:

***********************
A Short Python Tutorial
***********************

This tutorial explains by example how to process a :py:obj:`dict` using Python's list comprehension facilities.

Suppose we're interested in the total number or order failures.

#. First, we need to query the appropriate endpoint to get the data, and call the :py:meth:`json` method. ::

        http('http://www.example.com/foo/bar/data.json').json()

   This endpoint returns JSON data that is structured as follows (with much of the data omitted)::

        {
            ...
            "itr-http04_orderfails": [1, 0],
            "itr-http05_addtocart": [0.05, 0.0875],
            "http17_addtocart": [0.075, 0.066667],
            "http27_requests": [14.666667, 12.195833],
            "http13_orderfails": [null, 2],
            ...
        }

   The parsed object will therefore be a :py:obj:`dict` mapping strings to lists of numbers, which may contain :py:obj:`None` values.

#. We need to find all entries ending in :samp:`_orderfails`. In Python, we can transform a :py:obj:`dict` in a list of tuples :samp:`({key}, {value})` using the :py:meth:`items` method::

        http(...).json().items()

   We now need to filter this list to include only order failure information. Using a loop and an if statement, this could be accomplished like this::

        result = []
        for key, value in http(...).json().items():
            if key.endswith('_orderfails'):
                result.append(value)

   (Note how the tuples in the list returned by :py:meth:`items` are automatically "unpacked", their elements being assigned to :py:obj:`key` and :py:obj:`value`, respectively.)

   Since the check command needs to be a single expression, not a series of statements, this is unfortunately not an option. Fortunately, Python provides a feature called list comprehension, which allows us to express the code above as follows::

        [value for key, value in http(...).json().items() if key.endswith('_orderfails')]

   That is, code of the form ::

        result = []
        for ELEMENT in LIST:
            if CONDITION:
                result.append(RESULT_ELEMENT)

   becomes ::

        [RESULT_ELEMENT for ELEMENT in LIST if CONDITION]

   (The ``if CONDITION`` part is optional.)

   We now have a list of lists ``[[1, 0], [None, 2]]``.

#. In order to sum the list, we'd need to flatten it first, so that it has the form ``[1, 0, None, 2]``. This can be accomplished with the :py:func:`chain` function. Given one or more iterable objects (such as lists), :py:func:`chain` returns a new iterable object produced by concatenating the given objects. That is ::

        chain([1, 0], [None, 2])

   would return ::

        [1, 0, None, 2]

   Unfortunately, the lists we want to chain are themselves elements of a list, and calling ``chain([[1, 0], [None, 2]])`` would just concatenate the list with nothing and return the it unchanged. We therefore need to tell Python to unpack the list, so that each of its elements becomes a new argument for the invocation of :py:func:`chain`.

   This can be accomplished by the ``*`` operator::

        chain(*[[1, 0], [None, 2]])

   That is, out expression is now ::

        chain(*[value for key, value in http(...).json().items() if key.endswith('_orderfails')])

#. Now we need to remove that pesky :py:obj:`None` from the list. This could be accomplished with another list comprehension::

        [value for value in chain(...) if value is not None]

   For didactic reasons, we shall use the :py:func:`filter` function instead. :py:func:`filter` takes two arguments: a function that is called for each element in the filtered list and indicates whether that element should be in the resulting list, and the list that is to be filtered itself. We can create an anonymous function for this purpose using a lambda expression::

        filter(lambda element: element is not None, chain(...))

   In this case, we can use a somewhat obscure shortcut, though. If the function given to :py:func:`filter` is :py:obj:`None`, the identity function is used. Therefore, objects will be included in the resulting list if and only if they are "truthy", which :py:obj:`None` isn't. The integer :py:obj:`0` isn't truthy either, but this isn't a problem in this case since the presence or absence of zeros does not affect the sum. Therefore, we can use the expression ::

        filter(None, chain(*[value for key, value in http(...).json().items() if key.endswith('_orderfails')]))

#. Finally, we need to sum the elements of the list. For that, we can just use the :py:func:`sum` function, so that the expression is now ::

        sum(filter(None, chain(*[value for key, value in http(...).json().items() if key.endswith('_orderfails')])))


Python Recipes
==============

.. describe:: Merging Data Into One Result

    You can merge heterogeneous data into a single result object:

    .. code-block:: python

        {
            'http_data': http(...).json()[...],
            'jmx_data':  jmx().query(...).results()[...],
            'sql_data':  sql().execute(...)[...],
        }


.. describe:: Mapping SQL Results by ID

    The SQL ``results()`` methods returns a list of maps (``[{'id': 1, 'data': 1000}, {'id': 2, 'data': 2000}]``). You can convert this to a single map (``{1: 1000, 2: 2000}``) like this:

    .. code-block:: python

        { row['id']: row['data'] for row in sql().execute(...).results() }


.. describe:: Using Multiple Captures

    If you have a alert condition such as

    .. code-block:: python

        FOO > 10 or BAR > 10

    adding capures is a bit tricky. If you use

    .. code-block:: python

        capture(foo=FOO) > 10 or capture(bar=BAR) > 10

    and both ``FOO`` and ``BAR`` are greater than 10, only ``foo`` will be captured because the ``or`` uses short-circuit evaluation (``True or X`` is true for all ``X``, so ``X`` doesn't need to be evaluated). Instead, you can use

    .. code-block:: python

        any([capture(foo=FOO) > 10, capture(bar=BAR) > 10])

    which will always evaluate both comparisons and thus capture both values.


.. describe:: Defining Temporary Variables

    You aren't supposed to be able to do define variables, but you can work around this restriction as follows:

    .. code-block:: python

        (lambda x:
            # Some complex operation using x multiple times
        )(
            x = sql().execute(...)  # Some complex or expensive query
        )


.. describe:: Defining Functions

    Since you can define variables with the trick above, you can also define functions:

    .. code-block:: python

        (lambda f:
            # Some complex operation calling f multiple times
        )(
            f = lambda a, b, c: sql().execute(...)  # Some code using the arguments a, b, and c
        )

