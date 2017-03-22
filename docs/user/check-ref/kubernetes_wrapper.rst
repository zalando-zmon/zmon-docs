Kubernetes
----------

Provides a wrapper for querying Kubernetes cluster resources.


.. py:function:: kubernetes(namespace='default')

    ::

    If ``namespace`` is ``None`` then **all** namespaces will be queried. This however will increase the number of calls to Kubernetes API server.

.. note::

    Kubernetes wrapper will authenticate using service account, which assumes the wroker is running in a Kubernetes cluster.

.. _labelSelectors:

Label Selectors
^^^^^^^^^^^^^^^

Kubernetes API provides a way to filter resources using `labelSelector <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`_. Kubernetes wrapper provides a friendly syntax for filtering.

The following examples show different usage of Kubernetes wrapper with label filtering:

.. code-block:: python

    # Get all pods with label ``application`` equal to ``zmon-worker``
    kubernetes().pods(application='zmon-worker')
    kubernetes().pods(application__eq='zmon-worker')


    # Get all pods with label ``application`` **not equal to** ``zmon-worker``
    kubernetes().pods(application__neq='zmon-worker')


    # Get all pods with label ``application`` **any of** ``zmon-worker`` or ``zmon-agent``
    kubernetes().pods(application__in=['zmon-worker', 'zmon-agent'])

    # Get all pods with label ``application`` **not any of** ``zmon-worker`` or ``zmon-agent``
    kubernetes().pods(application__notin=['zmon-worker', 'zmon-agent'])


Methods of Kubernetes
^^^^^^^^^^^^^^^^^^^^^

.. py:function:: pods(name=None, phase=None, ready=None, **kwargs)

        ::

        Return list of Pods.

        :param name: Pod name.
        :type name: str

        :param phase: Pod status phase. Valid values are: Pending, Running, Failed, Succeeded or Unknown.
        :type phase: str

        :param ready: Pod ready status. If None then all pods are returned.
        :type ready: bool

        :param **kwargs: Pod :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of pods. Typical pod has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: nodes(name=None, **kwargs)

        ::

        Return list of nodes. Namespace is ignored.

        :param name: Node name.
        :type name: str

        :param **kwargs: Node :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of nodes. Typical pod has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: svc(**kwargs)

        ::

        Alias for :func:`services`

.. py:function:: services(name=None, **kwargs)

        ::

        Return list of Services.

        :param name: Service name.
        :type name: str

        :param **kwargs: Service :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of services. Typical service has "metadata", "status" and "spec".
        :rtype: list


.. py:function:: ep(**kwargs)

        ::

        Alias for :func:`endpoints`

.. py:function:: endpoints(name=None, **kwargs)

        ::

        Return list of Endpoints.

        :param name: Endpoint name.
        :type name: str

        :param **kwargs: Endpoint :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Endpoints. Typical Endpoint has "metadata", and "subsets".
        :rtype: list

.. py:function:: ing(**kwargs)

        ::

        Alias for :func:`ingresses`

.. py:function:: ingresses(name=None, **kwargs)

        ::

        Return list of Ingresses.

        :param name: Ingress name.
        :type name: str

        :param **kwargs: Ingress :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Ingresses. Typical Ingress has "metadata", "spec" and "status".
        :rtype: list

.. py:function:: statefulsets(name=None, replicas=None, **kwargs)

        ::

        Return list of Statefulsets.

        :param name: Statefulset name.
        :type name: str

        :param replicas: Statefulset replicas.
        :type replicas: int

        :param **kwargs: Statefulset :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Statefulsets. Typical Statefulset has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: rs(**kwargs)

        ::

        Alias for :func:`replicasets`

.. py:function:: replicasets(name=None, replicas=None, **kwargs)

        ::

        Return list of ReplicaSets.

        :param name: ReplicaSet name.
        :type name: str

        :param replicas: ReplicaSet replicas.
        :type replicas: int

        :param **kwargs: ReplicaSet :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of ReplicaSets. Typical ReplicaSet has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: deployments(name=None, replicas=None, ready=None, **kwargs)

        ::

        Return list of Deployments.

        :param name: Deployment name.
        :type name: str

        :param replicas: Deployment replicas.
        :type replicas: int

        :param ready: Deployment ready status.
        :type ready: bool

        :param **kwargs: Deployment :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Deployments. Typical Deployment has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: cm(**kwargs)

        ::

        Alias for :func:`configmaps`

.. py:function:: configmaps(name=None, **kwargs)

        ::

        Return list of ConfigMaps.

        :param name: ConfigMap name.
        :type name: str

        :param **kwargs: ConfigMap :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of ConfigMaps. Typical ConfigMap has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: pvc(**kwargs)

        ::

        Alias for :func:`persistentvolumeclaims`

.. py:function:: persistentvolumeclaims(name=None, phase=None, **kwargs)

        ::

        Return list of PersistentVolumeClaims.

        :param name: PersistentVolumeClaim name.
        :type name: str

        :param phase: Volume phase.
        :type phase: str

        :param **kwargs: PersistentVolumeClaim :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of PersistentVolumeClaims. Typical PersistentVolumeClaim has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: pv(**kwargs)

        ::

        Alias for :func:`persistentvolumes`

.. py:function:: persistentvolumes(name=None, phase=None, **kwargs)

        ::

        Return list of PersistentVolumes.

        :param name: PersistentVolume name.
        :type name: str

        :param phase: Volume phase.
        :type phase: str

        :param **kwargs: PersistentVolume :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of PersistentVolumes. Typical PersistentVolume has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: metrics()

        ::

        Return API server metrics in prometheus format.

        :return: Cluster metrics.
        :rtype: dict
