CloudWatch
----------

If running on AWS you can use ``cloudwatch()`` to access AWS metrics easily.

.. py:method:: query_one(dimensions, metric_name, statistics, namespace, period=60, minutes=5)

  Query a single AWS CloudWatch metric and return a single scalar value (float).
  Metric will be aggregated over the last five minutes using the provided aggregation type.

  This method is a more low-level variant of the ``query`` method: all parameters, including all dimensions need to be known.


.. py:method:: query(dimensions, metric_name, statistics='Sum', namespace=None, period=60, minutes=5)

  Query AWS CloudWatch for metrics. Metrics will be aggregated over the last five minutes using the provided aggregation type (default "Sum").

  *dimensions* is a dictionary to filter the metrics to query. See the `list_metrics boto documentation`_.
  You can provide the special value "NOT_SET" for a dimension to only query metrics where the given key is not set.
  This makes sense e.g. for ELB metrics as they are available both per AZ ("AvailabilityZone" has a value) and aggregated over all AZs ("AvailabilityZone" not set).
  Additionally you can include the special "*" character in a dimension value to do fuzzy (shell globbing) matching.

  *metric_name* is the name of the metric to filter against (e.g. "RequestCount").

  *namespace* is an optional namespace filter (e.g. "AWS/EC2).

  To query an ELB for requests per second:

  ::

        # both using special "NOT_SET" and "*" in dimensions here:
        val = cloudwatch().query({'AvailabilityZone': 'NOT_SET', 'LoadBalancerName': 'pierone-*'}, 'RequestCount', 'Sum')['RequestCount']
        requests_per_second = val / 60

You can find existing metrics with the AWS CLI tools:

.. code-block:: bash

    $ aws cloudwatch list-metrics --namespace "AWS/EC2"

Use the "dimensions" argument to select on what dimension(s) to aggregate over:

.. code-block:: bash

    $ aws cloudwatch list-metrics --namespace "AWS/EC2" --dimensions Name=AutoScalingGroupName,Value=my-asg-FEYBCZF

The desired metric can now be queried in ZMON:

.. code-block:: python

    cloudwatch().query({'AutoScalingGroupName': 'my-asg-*'}, 'DiskReadBytes', 'Sum')



.. _list_metrics boto documentation: http://boto.readthedocs.org/en/latest/ref/cloudwatch.html#boto.ec2.cloudwatch.CloudWatchConnection.list_metrics
