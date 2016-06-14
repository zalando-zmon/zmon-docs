.. _downtimes:


Downtimes
---------

This functionality allows the user to acknowledge an existing alert or create a downtime schedule for an anticipated service
interruption. When acknowleding an existing alert, the user has to provide the predicted duration, and when creating
a scheduled downtime - start and end date. If the downtime is currently active, meaning an alert occured within the
downtime period, the alert notification won't be shown in the dashboard and it'll be greyed out in alert details page.
Please note that the downtime will not be evaluated immediately after creation, meaning that the alert might appear
as active until it's evaluated again by the worker. E.g. if the user defined a downtime for an alert which is evaluated
every minute and the last evaluation was 5 seconds ago, it would take approximately one more minute for the alert to
appear in "downtime state".

To acknowledge an alert or to schedule a new downtime, the user has to go to the specific alert details page and click
on a downtime button next to the desired alert.
