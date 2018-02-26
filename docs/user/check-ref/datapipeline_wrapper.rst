.. _datapipeline:

Data Pipeline
-------------

If running on AWS you can use ``datapipeline()`` to access AWS Data Pipelines' health easily.

.. py:function:: datapipeline(region=None)

    Initialize Data Pipeline wrapper.

    :param region: AWS region for Data Pipeline queries. Eg. "eu-west-1". Defaults to the region in which the check is being executed. Note that Data Pipeline is not availabe in "eu-central-1" at time of writing.
    :type region: str


Methods of Data Pipeline
^^^^^^^^^^^^^^^^^^^^^^^^
.. py:method:: get_details(pipeline_ids)

    Query AWS Data Pipeline IDs supplied as a String (single) or list of Strings (multiple).
    Return a dict of ID(s) and status dicts as described in `describe_pipelines boto documentation`_.

    :param pipeline_ids: Data Pipeline IDs. Example ``df-0123456789ABCDEFGHI``
    :type pipeline_ids: Union[str, list]
    :rtype: dict

    Example query with single Data Pipeline ID supplied in a list:

    .. code-block:: python

        datapipeline().get_details(pipeline_ids=['df-exampleA'])
        {
            "df-exampleA": {
                "@lastActivationTime": "2018-01-30T14:23:52",
                "pipelineCreator": "ABCDEF:auser",
                "@scheduledPeriod": "24 hours",
                "@accountId": "0123456789",
                "name": "exampleA",
                "@latestRunTime": "2018-01-04T03:00:00",
                "@id": "df-0441325MB6VYFI6MUU1",
                "@healthStatusUpdatedTime": "2018-01-01T10:00:00",
                "@creationTime": "2018-01-01T10:00:00",
                "@userId": "0123456789",
                "@sphere": "PIPELINE",
                "@nextRunTime": "2018-01-05T03:00:00",
                "@scheduledStartTime": "2018-01-02T03:00:00",
                "@healthStatus": "HEALTHY",
                "uniqueId": "exampleA",
                "*tags": "[{\"key\":\"DataPipelineName\",\"value\":\"exampleA\"},{\"key\":\"DataPipelineId\",\"value\":\"df-exampleA\"}]",
                "@version": "2",
                "@firstActivationTime": "2018-01-01T10:00:00",
                "@pipelineState": "SCHEDULED"
            }
        }

.. _describe_pipelines boto documentation: http://boto3.readthedocs.io/en/latest/reference/services/datapipeline.html#DataPipeline.Client.describe_pipelines
