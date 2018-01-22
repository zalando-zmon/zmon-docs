S3
---

Allows data to be pulled from S3 Objects.


.. py:function:: s3()


Methods of S3
^^^^^^^^^^^^^^

.. py:function:: get_object_metadata(bucket_name, key)

    ::

    Get the metadata associated with the given ``bucket_name`` and ``key``. The metadata allows you to check for the 
    existance of the key within the bucket and to check how large the object is without reading the whole object into
    memory.

    :param bucket_name: the name of the S3 Bucket
    :param key: the key that identifies the S3 Object within the S3 Bucket
    :return: an ``S3ObjectMetadata`` object

    .. py:class:: S3ObjectMetadata

        .. py:method:: exists()

            Will return True if the object exists.

        .. py:method:: size()

            Returns the size in bytes for the object. Will return -1 for objects that do not exist.

    Example usage:

    .. code-block:: python

        s3().get_object_metadata('my bucket', 'mykeypart1/mykeypart2').exists()
        s3().get_object_metadata('my bucket', 'mykeypart1/mykeypart2').size()


.. py:function:: get_object(bucket_name, key)

    ::

    Get the S3 Object associated with the given ``bucket_name`` and ``key``. This method will cause the object to be
    read into memory.

    :param bucket_name: the name of the S3 Bucket
    :param key: the key that identifies the S3 Object within the S3 Bucket
    :return: an ``S3Object`` object

    .. py:class:: S3Object

        .. py:method:: text()

            Get the S3 Object data

        .. py:method:: json()

            If the object exists, parse the object as JSON.

            :return: a dict containing the parsed JSON or None if the object does not exist.

        .. py:method:: exists()

            Will return True if the object exists.

        .. py:method:: size()

            Returns the size in bytes for the object. Will return -1 for objects that do not exist.

    Example usage:

    .. code-block:: python

        s3().get_object('my bucket', 'mykeypart1/my_text_doc.txt').text()

        s3().get_object('my bucket', 'mykeypart1/my_json_doc.json').json()


.. py:function:: list_bucket(bucket_name, prefix, max_items)

    ::

    List the S3 Object associated with the given ``bucket_name``, matching ``prefix``.
    By default, listing is possible for up to 1000 keys, so we use pagination internally to overcome this.

    :param bucket_name: the name of the S3 Bucket
    :param prefix: the prefix to search under
    :param max_items: the maximum number of objects to list.  Defaults to 100.
    :return: an ``S3FileList`` object

    .. py:class:: S3FileList

        .. py:method:: files()

            Returns a list of dicts like

            .. code-block:: json

               {
                   "file_name": "foo",
                   "size": 12345,
                   "last_modified": "2017-07-17T01:01:21Z"
               }

    Example usage:

    .. code-block:: python

       s3().list_bucket('my bucket', 'some_prefix').files()

       files = s3().list_bucket('my bucket', 'some_prefix', 10000).files()  # for listing a lot of keys
       last_modified = files[0]["last_modified"].isoformat()  # returns a string that can be passed to time()
       age = time() - time(last_modified) 
