.. _cloudwatch:

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


.. py:method:: alarms(alarm_names=None, alarm_name_prefix=None, state_value=STATE_ALARM, action_prefix=None, max_records=50)

    Retrieve cloudwatch alarms filtered by state value.

    See `describe_alarms boto documentation`_ for more details.

    :param alarm_names: List of alarm names.
    :type alarm_names: list

    :param alarm_name_prefix: Prefix of alarms. Cannot be specified if ``alarm_names`` is specified.
    :type alarm_name_prefix: str

    :param state_value: State value used in alarm filtering. Available values are ``OK``, ``ALARM`` (default) and ``INSUFFICIENT_DATA``.
    :type state_value: str

    :param action_prefix: Action name prefix. Example ``arn:aws:autoscaling:`` to filter results for all autoscaling related alarms.
    :type action_prefix: str

    :param max_records: Maximum records to be returned. Default is 50.
    :type max_records: int

    :return: List of MetricAlarms.
    :rtype: list


.. _describe_alarms boto documentation: http://boto3.readthedocs.io/en/latest/reference/services/cloudwatch.html#CloudWatch.Client.describe_alarms

.. code-block:: python

    cloudwatch().alarms(state_value='ALARM')[0]
    {
        'ActionsEnabled': True,
        'AlarmActions': ['arn:aws:autoscaling:...'],
        'AlarmArn': 'arn:aws:cloudwatch:...',
        'AlarmConfigurationUpdatedTimestamp': datetime.datetime(2016, 5, 12, 10, 44, 15, 707000, tzinfo=tzutc()),
        'AlarmDescription': 'Scale-down if CPU < 50% for 10.0 minutes (Average)',
        'AlarmName': 'metric-alarm-for-service-x',
        'ComparisonOperator': 'LessThanThreshold',
        'Dimensions': [
            {
                'Name': 'AutoScalingGroupName',
                'Value': 'service-x-asg'
            }
        ],
        'EvaluationPeriods': 2,
        'InsufficientDataActions': [],
        'MetricName': 'CPUUtilization',
        'Namespace': 'AWS/EC2',
        'OKActions': [],
        'Period': 300,
        'StateReason': 'Threshold Crossed: 1 datapoint (36.1) was less than the threshold (50.0).',
        'StateReasonData': '{...}',
        'StateUpdatedTimestamp': datetime.datetime(2016, 5, 12, 10, 44, 16, 294000, tzinfo=tzutc()),
        'StateValue': 'ALARM',
        'Statistic': 'Average',
        'Threshold': 50.0
    }
