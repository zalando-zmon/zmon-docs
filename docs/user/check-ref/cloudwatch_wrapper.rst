.. _cloudwatch:

CloudWatch
----------

If running on AWS you can use ``cloudwatch()`` to access AWS metrics easily.

.. py:function:: cloudwatch(region=None, assume_role_arn=None)

    Initialize CloudWatch wrapper.

    :param region: AWS region for CloudWatch queries. Will be auto-detected if not supplied.
    :type region: str

    :param assume_role_arn: AWS IAM role ARN to be assumed. This can be useful in cross-account CloudWatch queries.
    :type assume_role_arn: str


Methods of Cloudwatch
^^^^^^^^^^^^^^^^^^^^^

.. py:method:: query_one(dimensions, metric_name, statistics, namespace, period=60, minutes=5, start=None, end=None, extended_statistics=None)

    Query a single AWS CloudWatch metric and return a single scalar value (float).
    Metric will be aggregated over the last five minutes using the provided aggregation type.

    This method is a more low-level variant of the ``query`` method: all parameters, including all dimensions need to be known.

    :param dimensions: Cloudwatch dimensions. Example ``{'LoadBalancerName': 'my-elb-name'}``
    :type dimensions: dict

    :param metric_name: Cloudwatch metric. Example ``'Latency'``.
    :type metric_name: str

    :param statistics: Cloudwatch metric statistics. Example ``'Sum'``
    :type statistics: list

    :param namespace: Cloudwatch namespace. Example ``'AWS/ELB'``
    :type namespace: str

    :param period: Cloudwatch statistics granularity in seconds. Default is 60.
    :type period: int

    :param minutes: Used to determine ``start`` time of the Cloudwatch query. Default is 5. Ignored if ``start`` is supplied.
    :type minutes: int

    :param start: Cloudwatch start timestamp. Default is ``None``.
    :type start: int

    :param end: Cloudwatch end timestamp. Default is ``None``. If not supplied, then end time is now.
    :type end: int

    :param extended_statistics: Cloudwatch ExtendedStatistics for percentiles query. Example ``['p95', 'p99']``.
    :type extended_statistics: list

    :return: Return a float if single value, dict otherwise.
    :rtype: float, dict


    Example query with percentiles for AWS ALB:

    .. code-block:: python

        cloudwatch().query_one({'LoadBalancer': 'app/my-alb/1234'}, 'TargetResponseTime', 'Average', 'AWS/ApplicationELB', extended_statistics=['p95', 'p99'])
        {
            'TargetResponseTime': 0.224,
            'p95': 0.245,
            'p99': 0.300
        }

.. note::

   In very rare cases, e.g. for ELB metrics, you may see only 1/2 or 1-2/3 of the value in ZMON due to a race condition of what data is already present in cloud watch.
   To fix this click "evaluate" on the alert, this will trigger the check and move its execution time to a new start time.

.. py:method:: query(dimensions, metric_name, statistics='Sum', namespace=None, period=60, minutes=5)

  Query AWS CloudWatch for metrics. Metrics will be aggregated over the last five minutes using the provided aggregation type (default "Sum").

  *dimensions* is a dictionary to filter the metrics to query. See the `list_metrics boto documentation`_.
  You can provide the special value "NOT_SET" for a dimension to only query metrics where the given key is not set.
  This makes sense e.g. for ELB metrics as they are available both per AZ ("AvailabilityZone" has a value) and aggregated over all AZs ("AvailabilityZone" not set).
  Additionally you can include the special "*" character in a dimension value to do fuzzy (shell globbing) matching.

  *metric_name* is the name of the metric to filter against (e.g. "RequestCount").

  *namespace* is an optional namespace filter (e.g. "AWS/EC2).

  To query an ELB for requests per second:

  .. code-block:: python

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
