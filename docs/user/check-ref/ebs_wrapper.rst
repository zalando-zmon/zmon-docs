EBS
---

Allows to describe EBS objects (currently, only Snapshots are supported).


.. py:function:: ebs()


Methods of EBS
^^^^^^^^^^^^^^

.. py:function:: list_snapshots(account_id, max_items)

    ::

    List the EBS Snapshots owned by the given account_id.
    By default, listing is possible for up to 1000 items, so we use pagination internally to overcome this.

    :param account_id: AWS account id number (as a string).  Defaults to the AWS account id where the check is running.
    :param max_items: the maximum number of snapshots to list.  Defaults to 100.
    :return: an ``EBSSnapshotsList`` object

    .. py:class:: EBSSnapshotsList

        .. py:method:: items()

            Returns a list of dicts like

            .. code-block:: json

               {
                   "id": "snap-12345",
                   "description": "Snapshot description...",
                   "size": 123,
                   "start_time": "2017-07-16T01:01:21Z",
                   "state": "completed"
               }

    Example usage:

    .. code-block:: python

       ebs().list_snapshots().items()

       snapshots = ebs().list_snapshots(max_items=1000).items()  # for listing more than the default of 100 snapshots
       start_time = snapshots[0]["start_time"].isoformat()  # returns a string that can be passed to time()
       age = time() - time(start_time)
