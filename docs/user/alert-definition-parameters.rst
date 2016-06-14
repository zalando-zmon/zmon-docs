.. _alert-definition-parameters:


Alert Definition Parameters
---------------------------

Alert definition *parameters* allows one to decouple alert condition from constants that are used inside it.

Use Case: Technical alert condition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If your alert condition is highly technical with a lot of Python code in it, it is often makes sense to split actual calculation from threshold values and move such constant values into parameters.

The same may apply in certain cases to alert definitions created by technical staff, which later need to be adjusted by non-technical people - if you split calculation from variable definition, you may let non-technical people just change values without touching calculation logic.

Use Case: Same alert, different priorities
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Another use case where we recommend to use parameters is when you need to have the same alert come up with a different priority depending on threshold values.

In such case, refer to :ref:`alert inheritance <alert-definition-inheritance>` for configuring inherited alerts.

Proposed structure would look like:

* Base alert "A" with alert condition and parameters, check *template* box
* Alert "B1" inherits from "A" specifying *priority* RED and associated parameter values
* Alert "B2" inherits from "A" specifying *priority* YELLOW and associated parameter values

An example: Setting a simple parameter in trial run
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the zmon2 web interface click on the trial run button.

1. In the **Check Command** text box enter::

    normalvariate(50, 20)

This is a simple normal probability function that produce a float number 50% of the time over 50.0, so it's good to test things.

2. In the **Alert Condition** enter::

    value>capture(threshold=threshold) + len(capture(params=params))

3. In the **Parameters** selector enter two values (by clicking the plus sign):

    +------------+------------+-----------+
    | Name       | Value      | Type      |
    +============+============+===========+
    | threshold  | 50.0       | Float     |
    +------------+------------+-----------+
    | anything   | Kartoffel  | String    |
    +------------+------------+-----------+

4. In the **Entity Filter** text box enter:

    .. code-block:: json

        [
            {
                "type": "GLOBAL"
            }
        ]

5. In the **Interval** enter: 10

If you run this Trial you can get an Alert or an 'OK', but the interesting thing will be in the **Captures** column.
See how the parameters that you entered are evaluated in the alert condition with the value that you provided.
Notice also that there is a special parameter called **params** that holds a dict with all the parameters that you entered, this is done so the user can iterate over all the parameters and take conditional decisions, providing a kind of introspection capability, but this is only for advanced users.

Last but not least: *Most of the time you don't need to capture the parameter values*, we did it like this so you can visually see that the parameters are evaluated, this means that you can run exactly the same check with this **Alert Condition**::

    value>threshold + len(params)
