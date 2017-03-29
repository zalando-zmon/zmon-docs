Kubernetes
----------

Provides a wrapper for querying Kubernetes cluster resources.


.. py:function:: kubernetes(namespace='default')

    ::

    If ``namespace`` is ``None`` then **all** namespaces will be queried. This however will increase the number of calls to Kubernetes API server.

.. note::

    - Kubernetes wrapper will authenticate using service account, which assumes the worker is running in a Kubernetes cluster.
    - All Kubernetes wrapper calls are scoped to the Kubernetes cluster hosting the worker. It is not intended to be used in querying multiple clusters.

.. _labelSelectors:

Label Selectors
^^^^^^^^^^^^^^^

Kubernetes API provides a way to filter resources using `labelSelector <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`_. Kubernetes wrapper provides a friendly syntax for filtering.

The following examples show different usage of the Kubernetes wrapper utilizing label filtering:

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

        Return list of `Pods <https://kubernetes.io/docs/user-guide/pods/>`_.

        :param name: Pod name.
        :type name: str

        :param phase: Pod status phase. Valid values are: Pending, Running, Failed, Succeeded or Unknown.
        :type phase: str

        :param ready: Pod readiness status. If ``None`` then all pods are returned.
        :type ready: bool

        :param **kwargs: Pod :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of pods. Typical pod has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: nodes(name=None, **kwargs)

        ::

        Return list of `Nodes <https://kubernetes.io/docs/admin/node/>`_. Namespace does not apply.

        :param name: Node name.
        :type name: str

        :param **kwargs: Node :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of nodes. Typical pod has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: services(name=None, **kwargs)

        ::

        Return list of `Services <https://kubernetes.io/docs/user-guide/services/>`_.

        :param name: Service name.
        :type name: str

        :param **kwargs: Service :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of services. Typical service has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: endpoints(name=None, **kwargs)

        ::

        Return list of Endpoints.

        :param name: Endpoint name.
        :type name: str

        :param **kwargs: Endpoint :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Endpoints. Typical Endpoint has "metadata", and "subsets".
        :rtype: list

.. py:function:: ingresses(name=None, **kwargs)

        ::

        Return list of `Ingresses <https://kubernetes.io/docs/user-guide/ingress/>`_.

        :param name: Ingress name.
        :type name: str

        :param **kwargs: Ingress :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Ingresses. Typical Ingress has "metadata", "spec" and "status".
        :rtype: list

.. py:function:: statefulsets(name=None, replicas=None, **kwargs)

        ::

        Return list of `Statefulsets <https://kubernetes.io/docs/user-guide/petset/>`_.

        :param name: Statefulset name.
        :type name: str

        :param replicas: Statefulset replicas.
        :type replicas: int

        :param **kwargs: Statefulset :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Statefulsets. Typical Statefulset has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: replicasets(name=None, replicas=None, **kwargs)

        ::

        Return list of `ReplicaSets <https://kubernetes.io/docs/user-guide/replicasets/>`_.

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

        Return list of `Deployments <https://kubernetes.io/docs/user-guide/deployments/>`_.

        :param name: Deployment name.
        :type name: str

        :param replicas: Deployment replicas.
        :type replicas: int

        :param ready: Deployment readiness status.
        :type ready: bool

        :param **kwargs: Deployment :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of Deployments. Typical Deployment has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: configmaps(name=None, **kwargs)

        ::

        Return list of `ConfigMaps <https://kubernetes.io/docs/user-guide/configmap/>`_.

        :param name: ConfigMap name.
        :type name: str

        :param **kwargs: ConfigMap :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of ConfigMaps. Typical ConfigMap has "metadata" and "data".
        :rtype: list

.. py:function:: persistentvolumeclaims(name=None, phase=None, **kwargs)

        ::

        Return list of `PersistentVolumeClaims <https://kubernetes.io/docs/user-guide/persistent-volumes/>`_.

        :param name: PersistentVolumeClaim name.
        :type name: str

        :param phase: Volume phase.
        :type phase: str

        :param **kwargs: PersistentVolumeClaim :ref:`labelSelectors` filters.
        :type **kwargs: dict

        :return: List of PersistentVolumeClaims. Typical PersistentVolumeClaim has "metadata", "status" and "spec".
        :rtype: list

.. py:function:: persistentvolumes(name=None, phase=None, **kwargs)

        ::

        Return list of `PersistentVolumes <https://kubernetes.io/docs/user-guide/persistent-volumes/>`_.

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
