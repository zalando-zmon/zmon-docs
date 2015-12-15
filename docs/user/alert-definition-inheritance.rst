.. _alert-definition-inheritance:

****************************
Alert Definition Inheritance
****************************

Alert definition *inheritance* allows one to create an alert definition based on another alert whereby a child reuses attributes from the parent.
Each alert definition can only inherit from a single alert definition (``single inheritance``).

Template
--------

A Template is basically an alert definition with a subset of attributes that **is not evaluated and can only be used for extension**.

To create a template:

#. Select the check definition
#. click **Add New Alert Definition**
#. Set attributes to reuse and activate checkbox ``template``

Extending
---------

In general one can inherit from any alert definition/template. One should open the alert definition details and click ``inherit`` on the top right corner.
To override a field, just type in a new value. An icon should appear on the left side, meaning that the field will be overridden.
To rollback the change and keep the value defined on the parent, one should click in ``override`` icon.

Overriding
----------

By default the child alert retains all attributes of the parent alert with the exception of the following mandatory attributes:
 - team
 - responsible team
 - status

These attributes are used for ``authorization`` (see :ref:`permissions` for details) therefore, they cannot be reused. If one changes these attributes on the parent alert definition, child alerts are not affected and you don't loose access rights.
All the remaining attributes can be overridden, replacing the parent alert definition with its own values.
