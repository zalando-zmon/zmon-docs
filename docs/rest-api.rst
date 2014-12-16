.. _rest-api:

********
Rest API
********

Authentication & Authorization
------------------------------

ZMON API only supports basic authentication.

In order to make authorized calls to ZMON's API, one should first obtain a robot user.

Currently, there are 2 roles available:

* api-reader
* api-writer

As the name suggests, role api-reader allows an user to execute read-only calls while api-writer allows one to write
to ZMON.

Your application should always examine the HTTP status of the response. Any value other than 200 indicates a failure.

Here are some examples:

*Request with invalid credentials*::

    HTTP/1.1 401 Unauthorized
    Content-Type: application/json;charset=UTF-8
    Content-Length: 29
    Date: Thu, 21 Aug 2014 10:28:10 GMT

    {"message":"Bad credentials"}

*Request without proper authentication*::

    HTTP/1.1 401 Unauthorized
    Content-Type: application/json;charset=UTF-8
    Content-Length: 69
    Date: Thu, 21 Aug 2014 10:29:14 GMT

    {"message":"Full authentication is required to access this resource"}

*Request without proper authorization*::

    HTTP/1.1 403 Forbidden
    Content-Type: application/json;charset=UTF-8
    Content-Length: 30
    Date: Thu, 21 Aug 2014 10:31:20 GMT

    {"message":"Access is denied"}

Downtimes
---------
For more info about this feature, please check :ref:`this <downtimes>`

Scheduling a downtime
^^^^^^^^^^^^^^^^^^^^^

**Resource URL:** POST /rest/api/v1/downtimes

**Description**

Create a new downtime, returning the id of the newly created resource. If none of the
alert definition entities match this request it will succeed and return an empty list of entities/alert definitions.
Any attempt to execute this method without proper authentication will result in a 401. If the user does not have enough
permissions (role: api-writer) this method will return an HTTP 403. In case of malformed syntax or missing mandatory
fields this method will return an HTTP 400 and the client SHOULD NOT repeat the request without modifications. In case
of success this method will return HTTP 200.

    .. note::

        Alerts and checks with hard-coded entity identifiers in the check command are not covered.

**Parameters:**

+-------------------+-----------+-----------+-------------------------------------------+
| Name              | Data Type | Mandatory | Description                               |
+===================+===========+===========+===========================================+
| comment           | String    | yes       | Downtime comment                          |
+-------------------+-----------+-----------+-------------------------------------------+
| start_time        | Number    | no        | The start time in seconds since epoch.    |
|                   |           |           |                                           |
|                   |           |           | **Default:** current time                 |
+-------------------+-----------+-----------+-------------------------------------------+
| end_time          | Number    | yes       | The end time in seconds since epoch.      |
|                   |           |           |                                           |
|                   |           |           | **Precondition:** end_time > start_time   |
+-------------------+-----------+-----------+-------------------------------------------+
| entities          | Array     | yes       | Array of entities to set in downtime.     |
|                   |           |           | (e.g. htt01:4420)                         |
|                   |           |           |                                           |
|                   |           |           | **Precondition:** The array should have   |
|                   |           |           | at least one element                      |
+-------------------+-----------+-----------+-------------------------------------------+
| alert_definitions | Array     | no        | Alert definition ids. If specified, only  |
|                   |           |           | entities belonging to these alert         |
|                   |           |           | definitions will be set in downtime.      |
+-------------------+-----------+-----------+-------------------------------------------+

**Example:**

.. code-block:: bash

    curl -v --user hjacobs:test 'https://zmon.example.com/rest/api/v1/downtimes' \
       -H 'Content-Type: application/json' \
       --data-binary $'{"comment":"Cities downtime","end_time":1408665600,"entities":["cd-kinshasa", "cn-peking"]}'

*Request*::

    POST /rest/api/v1/downtimes HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*
    Content-Type: application/json
    Content-Length: 91

    {"comment":"Cities downtime","end_time":1408665600,"entities":["cd-kinshasa", "cn-peking"]}

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Date: Thu, 21 Aug 2014 14:26:02 GMT

    {"comment":"Cities downtime","start_time":1408631162,"end_time":1408665600,"created_by":"hjacobs",
    "id":"cf6ada50-3eb2-4c17-8d09-4eb03dc19cf5","entities":["cn-peking","cd-kinshasa"],"alert_definitions":[704]}

Deleting a downtime
^^^^^^^^^^^^^^^^^^^

**Resource URL:** DELETE /rest/api/v1/downtimes/{id}

**Description**

Attempt to delete the downtime with the specified id. If the downtime ID doesn't exist, the request will succeed and
return an empty list of entities/alert definitions. Any attempt to execute this method without
proper authentication will result in a 401. If the user doesn’t have enough permissions (role: api-writer) this method
will return an HTTP 403. In case of malformed syntax or missing mandatory fields this method will return an HTTP 400
and the client SHOULD NOT repeat the request without modifications. In case of success this method will return HTTP 200.

**Parameters:**

+-------------------+-----------+-----------+-------------------------------------------+
| Name              | Data Type | Mandatory | Description                               |
+===================+===========+===========+===========================================+
| id                | String    | yes       | Id of the downtime to delete              |
+-------------------+-----------+-----------+-------------------------------------------+

**Example:**

.. code-block:: bash

    curl -v --user hjacobs:test 'https://zmon.example.com/rest/api/v1/downtimes/cf6ada50-3eb2-4c17-8d09-4eb03dc19cf5' \
      -H 'Content-Type: application/json' \
      -X DELETE

*Request*::

    DELETE /rest/api/v1/downtimes/cf6ada50-3eb2-4c17-8d09-4eb03dc19cf5 HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*
    Content-Type: application/json

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Date: Thu, 21 Aug 2014 15:16:51 GMT

    {"comment":"Cities downtime","start_time":1408633908,"end_time":1408665600,"created_by":"hjacobs",
    "id":"0ff6ed67-9521-42a7-8132-5ab837193af9","entities":["cn-peking","cd-kinshasa"],"alert_definitions":[704]}


Alert Definitions
-----------------

For more info about this feature, please check :ref:`this <alert-definitions>`

.. _api-create-alert-definition:

Creating a new Alert Definition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Resource URL:** POST /rest/api/v1/alert-definitions

**Description**

Create a new alert definition, returning the id of the newly created resource. Alert definitions can be created based
on another alert definition whereby a child reuses attributes from the parent. Each alert definition can only inherit
from a single alert definition (single inheritance).

One can also create templates. A Template is basically an alert definition with a subset of mandatory attributes that
is not evaluated and is only used for extension.

Any attempt to execute this method without proper authentication will result in a 401. In case of success this method
will return HTTP 200.

**Parameters:**

+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| Name                | Data Type | Mandatory | Inherited | Description                                                   |
+=====================+===========+===========+===========+===============================================================+
| name                | String    | yes       | yes       | The alert’s display name on the dashboard. This field can     |
|                     |           |           |           | contain curly-brace variables like {mycapture} that are       |
|                     |           |           |           | replaced by capture’s value when the alert is triggered. It’s |
|                     |           |           |           | also possible to format decimal precision (e.g. “My alert     |
|                     |           |           |           | {mycapture:.2f}” would show as “My alert 123.45” if mycapture |
|                     |           |           |           | is 123.456789). To include a comma separated list of entities |
|                     |           |           |           | as part of the alert’s name, just use the special placeholder |
|                     |           |           |           | {entities}. This field can be omitted if the new definition   |
|                     |           |           |           | extends an existing one with this field defined (templates    |
|                     |           |           |           | might not have all fields).                                   |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| description         | String    | yes       | yes       | Meaningful text for people trying to handle the alert. This   |
|                     |           |           |           | field can be omitted if the new definition extends an         |
|                     |           |           |           | existing one with this field defined.                         |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| team                | String    | yes       | no        | Team dashboard to show the alert on.                          |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| responsible_team    | String    | yes       | no        | Additional team field that allows one to delegate alert       |
|                     |           |           |           | monitoring to other teams. The responsible team’s name will   |
|                     |           |           |           | be shown on the dashboard. This team is responsible for       |
|                     |           |           |           | fixing the problem in case the alert is triggered.            |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| entities            | Array     | yes       | yes       | Filter used to select a subset of check definition entities.  |
|                     |           |           |           | If empty, the condition will be evaluated in all entities     |
|                     |           |           |           | defined in check definition. This field can be omitted if the |
|                     |           |           |           | new definition extends an existing one with this fields       |
|                     |           |           |           | defined.                                                      |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| entities_exclude    | Array     | yes       | yes       | This filter is useful to exclude entities from the final      |
|                     |           |           |           | entity set. If empty, none of the  entities will be excluded. |
|                     |           |           |           | This field can be omitted if the new definition extends an    |
|                     |           |           |           | existing one with this fields defined                         |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| condition           | String    | yes       | yes       | Valid Python expression to return true when alert should be   |
|                     |           |           |           | triggered. This field can be omitted if the new definition    |
|                     |           |           |           | extends an existing one with this fields defined.             |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| notifications       | String    | no        | yes       | List of notification commands. One could either send emails   |
|                     |           |           |           | (send_mail) or sms (send_sms).                                |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| check_definition_id | Number    | yes       | yes       | Id of the check definition. This field can be omitted if the  |
|                     |           |           |           | new definition extends an existing one with this fields       |
|                     |           |           |           | defined.                                                      |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| status              | String    | yes       | no        | Alert definition status. Possible values are:                 |
|                     |           |           |           |                                                               |
|                     |           |           |           | - ACTIVE                                                      |
|                     |           |           |           |                                                               |
|                     |           |           |           | - INACTIVE                                                    |
|                     |           |           |           |                                                               |
|                     |           |           |           | - REJECTED                                                    |
|                     |           |           |           |                                                               |
|                     |           |           |           | Alerts are only triggered if the alert definition is active.  |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| priority            | Number    | yes       | yes       | Alert priority. Possible values are:                          |
|                     |           |           |           |                                                               |
|                     |           |           |           | - 1: red                                                      |
|                     |           |           |           |                                                               |
|                     |           |           |           | - 2: orange                                                   |
|                     |           |           |           |                                                               |
|                     |           |           |           | - 3: yellow                                                   |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| period              | String    | no        | yes       | Notification time period.                                     |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| template            | Boolean   | yes       | no        | A template is an alert definition that is not evaluated and   |
|                     |           |           |           | can only be used for extension.                               |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| parent_id           | Number    | no        | no        | Id of the parent alert definition. All fields defined on the  |
|                     |           |           |           | parent will be inherited.                                     |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| parameters          | Object    | no        | yes       | Alert definition parameters allows one to decouple alert      |
|                     |           |           |           | condition from constants that are used inside it. One can     |
|                     |           |           |           | define parameters in the python condition and specify its     |
|                     |           |           |           | values in this field. e.g. {“KEY1”: 1, “KEY2”, “foo”}         |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+
| tags                | Array     | no        | yes       | keyword assigned to a alert definition. This metadata helps   |
|                     |           |           |           | describe an alert definition and allows it to be found by     |
|                     |           |           |           | searching.                                                    |
+---------------------+-----------+-----------+-----------+---------------------------------------------------------------+

**Example:**

.. code-block:: bash

    curl --user hjacobs:test 'https://zmon.example.com/rest/api/v1/alert-definitions' -H 'Content-Type: application/json' \
        --data-binary $'{"name": "City Longitude >0", "description": "Test whether a city lies east or west", "team": "Platform/Software", "responsible_team": "Platform/Software", "entities": [{"type": "city"}], "entities_exclude": [], "condition": "capture(longitude=float(value)) > longitude_param", "notifications": [], "check_definition_id": 20, "status": "ACTIVE", "priority": 2, "period": "", "template": false, "parameters": {"longitude_param": {"comment": "Longitude parameter","type": "float", "value": 0}}, "tags": ["CITY"]}'

*Request*::

    POST /rest/api/v1/alert-definitions HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*
    Content-Type: application/json

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Date: Tue, 26 Aug 2014 18:02:29 GMT

    {"id":788,"name":"City Longitude >0","description":"Test whether a city lies east or west",
    "team":"Platform/Software","responsible_team":"Platform/Software","entities":[{"type":"city"}],
    "entities_exclude":[],"condition":"capture(longitude=float(value)) > longitude_param","notifications":[],
    "check_definition_id":20,"status":"ACTIVE","priority":2,"last_modified":1409076149956,"last_modified_by":"hjacobs",
    "period":"","template":false,"parent_id":null,
    "parameters":{"longitude_param":{"value":0,"comment":"Longitude parameter","type":"float"}},"tags":["CITY"]}

Updating an Alert Definition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Resource URL:** PUT /rest/api/v1/alert-definitions/{id}

**Description**

Updates an existing alert definition. If the alert definintion doesn't exist, this method will return a 404.

For more info about the parameters, please check :ref:`how to create a new Alert Definition <api-create-alert-definition>`

**Example:**

.. code-block:: bash

    curl --user hjacobs:test 'https://zmon.example.com/rest/api/v1/alert-definitions/788' \
    -H 'Content-Type: application/json' \
    --data-binary $'{"name": "City Longitude >0", "description": "Checks whether a city lies east or west", "team": "Platform/Software", "responsible_team": "Platform/Software", "entities": [{"type": "city"}], "entities_exclude": [], "condition": "capture(longitude=float(value)) > longitude_param", "notifications": [], "check_definition_id": 20, "status": "ACTIVE", "priority": 2, "period": "", "template": false, "parameters": {"longitude_param": {"comment": "Longitude parameter","type": "float", "value": 0}}, "tags": ["CITY"]}' \
    -X PUT

*Request*::

    PUT /rest/api/v1/alert-definitions/788 HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*
    Content-Type: application/json

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Date: Tue, 26 Aug 2014 18:47:00 GMT

    {"id":788,"name":"City Longitude >0","description":"Checks whether a city lies east or west",
    "team":"Platform/Software","responsible_team":"Platform/Software","entities":[{"type":"city"}],
    "entities_exclude":[],"condition":"capture(longitude=float(value)) > longitude_param","notifications":[],
    "check_definition_id":20,"status":"ACTIVE","priority":2,"last_modified":1409078820694,"last_modified_by":"hjacobs",
    "period":"","template":false,"parent_id":null,
    "parameters":{"longitude_param":{"value":0,"comment":"Longitude parameter","type":"float"}},"tags":["CITY"]}

Find an Alert Defintion by ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Resource URL:** GET /rest/api/v1/alert-definitions/{id}

**Description**

Find an existing alert definition by id. If the alert definintion doesn't exist, this method will return a 404.

**Example:**

.. code-block:: bash

    curl -v --user hjacobs:test 'https://zmon.example.com/rest/api/v1/alert-definitions/788' \
    -H 'Content-Type: application/json'

*Request*::

    GET /rest/api/v1/alert-definitions/788 HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*
    Content-Type: application/json

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Date: Tue, 26 Aug 2014 18:47:00 GMT

    {"id":788,"name":"City Longitude >0","description":"Checks whether a city lies east or west",
    "team":"Platform/Software","responsible_team":"Platform/Software","entities":[{"type":"city"}],
    "entities_exclude":[],"condition":"capture(longitude=float(value)) > longitude_param","notifications":[],
    "check_definition_id":20,"status":"ACTIVE","priority":2,"last_modified":1409078820694,"last_modified_by":"hjacobs",
    "period":"","template":false,"parent_id":null,
    "parameters":{"longitude_param":{"value":0,"comment":"Longitude parameter","type":"float"}},"tags":["CITY"]}

.. _api-get-alert-status:

Retrieving Alert Status
^^^^^^^^^^^^^^^^^^^^^^^

**Resource URL:** GET /rest/api/v1/status/alert/{alert ids}/

**Description**

Returns current status of the given alert IDs. The information comes directly from Redis and represents results of the last alert evaluation

The results are returned in the following format (so basically for each alert and entity you get information

* when alert started (**ts**)
* how long has evaluation taken (**td**)
* are there any downtimes (**downtimes**)
* capture values, if available (**captures**)
* which worker has processed the value (**worker**)
* the latest check value (**value**)

**NOTE** Please keep in mind that this request will only work if you specify trailing slash (as in the example below).

.. code-block:: json

    {"alert id":
    	{
    		"entity name":
    		{
    			"td":0.013866,
    			"downtimes":[],
    			"captures":{"count":1},
    			"start_time":1.416391418749185E9,
    			"worker":"p3426.itr-monitor01",
    			"ts":1.4164876292204E9,
    			"value":1
    		}
    	}
    }

Any attempt to execute this method without proper authentication will result in a 401. In case of success this method
will return HTTP 200.

**Example:**

.. code-block:: bash

    curl --user hjacobs:test 'https://zmon.example.com/rest/api/v1/status/alert/69,3454/'

*Request*::

    GET https://zmon.example.com/rest/api/v1/status/alert/69,3454/ HTTP/1.1
    Authorization: Basic aGphY29iczp0ZXN0
    User-Agent: curl/7.30.0
    Host: zmon.example.com
    Accept: */*

*Response*::

    HTTP/1.1 200 OK
    Content-Type: application/json;charset=UTF-8
    Transfer-Encoding: chunked
    Vary: Accept-Encoding
    Date: Thu, 20 Nov 2014 12:47:37 GMT

    {"69":{"itr-elsn02:5827":{"td":0.013866,"downtimes":[],"captures":{"count":1},"start_time":1.416391418749185E9,"worker":"p3426.itr-monitor01","ts":1.4164876292204E9,"value":1},"elsn03:5827":{"td":0.015576,"downtimes":[],"captures":{"count":8},"start_time":1.416391397741839E9,"worker":"p3426.monitor02","ts":1.416487629218565E9,"value":8},"elsn02:5827":{"td":0.024973,"downtimes":[],"captures":{"count":9},"start_time":1.416330457394862E9,"worker":"p3426.itr-monitor01","ts":1.416487629223615E9,"value":9},"itr-elsn03:5827":{"td":0.020491,"downtimes":[],"captures":{"count":1},"start_time":1.416255229204794E9,"worker":"p3426.itr-monitor01","ts":1.41648762923005E9,"value":1},"elsn01:5827":{"td":0.019912,"downtimes":[],"captures":{"count":8},"start_time":1.416391418966269E9,"worker":"p3426.monitor03","ts":1.416487629216758E9,"value":8},"itr-elsn01:5827":{"td":0.015741,"downtimes":[],"captures":{"count":2},"start_time":1.416391429438217E9,"worker":"p3426.itr-monitor01","ts":1.416487629224237E9,"value":2}},"3454":{"monitor02":{"td":0.027714,"downtimes":[],"captures":{},"start_time":1.414754929626809E9,"worker":"p3426.monitor02","ts":1.416487578812573E9,"value":{"load1":8.71,"load15":9.73,"load5":10.22}},"monitor03":{"td":0.028951,"downtimes":[],"captures":{},"start_time":1.41475492971822E9,"worker":"p3426.monitor02","ts":1.41648757881069E9,"value":{"load1":9.25,"load15":11.17,"load5":10.9}}}}
