###########################
DMX Developer Documentation
###########################

***********************************
Data Format of the DMX core objects
***********************************

see https://trac.deepamehta.de/wiki/DataFormat

****************
The DMX REST API
****************

The DMX application service is accessible via a REST API.

All requests return application/json data.

For requests that take data in the request body: The data is expected to be of type application/json as well. So, don’t forget to set the request’s Content-Type header accordingly. Otherwise an error is returned (as application/x-www-form-urlencoded is used as the default).

About the curl examples:

The curl examples are ready for being pasted into a terminal. However, the IDs must be adjusted to your actual DB content. Hint: to determine a topic's or association's ID click it in the webclient and read the console log.

In order to make the responses more readable the curl examples make use of the jsonpretty tool. `Jsonpretty`_ is installable as a Ruby gem.

.. _Jsonpretty: https://github.com/nicksieger/jsonpretty

Topics
======

Get topic by ID
---------------

.. code:: bash

  ``GET /core/topic/<topic ID>[?include_childs=true/false]``

The default for include_childs is true. 

Example 1: Getting a topic with its composite value
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topic/2676 | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "id": 2676,
    "uri": "",
    "type_uri": "dm4.notes.note",
    "value": "What is DeepaMehta?",
    "composite": {
      "dm4.notes.title": "What is DeepaMehta?",
      "dm4.notes.text": "<p>DeepaMehta is a think tool.</p>"
    }
  }

For details about the topic format see DataFormat#Topic.

Example 2: Getting a topic without its composite value
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topic/55?include_childs=false | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 2676,
    "uri": "",
    "type_uri": "dm4.notes.note",
    "value": "What is DeepaMehta?",
    "composite": {
    }
  }

If the topic doesn’t exist an error 500 Retrieving topic xy failed is returned. (The error response might become more HTTP/REST conform in the furure.) 

Get topics by type
------------------

.. code:: bash

  GET /core/topic/by_type/<Type URI>[?include_childs=true/false]

 The default for include_childs is false.

Example: Getting all "Web Resource" topics without their composites
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topic/by_type/dm4.webbrowser.web_resource | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "total_count": 2,
    "items": [
      {
        "id": 2436,
        "uri": "",
        "type_uri": "dm4.webbrowser.web_resource",
        "value": "JSON Format - your online JSON Formatter",
        "composite": {
        }
      },
      {
        "id": 2259,
        "uri": "",
        "type_uri": "dm4.webbrowser.web_resource",
        "value": "DeepaMehta 4.0.5",
        "composite": {
        }
      }
    ]
  }

Create topic
------------

.. code:: bash

  POST /core/topic

  <the topic to create>

For details about the topic format see DataFormat#Topic.

For a create operation not all of the topic's properties are involved:

=========  =========================================================================================================================
Property   Mandatory / Optional
=========  =========================================================================================================================
uri        Optional. Default URI is an empty string.
type_uri   Mandatory.
value      | For a **simple** topic: Optional. Default value is an empty string.
           | You can change the value later on through an update operation.
           | For a **complex** topic: Do not specify a value. The value will be system-generated.
composite  | For a **complex** topic: Optional. Default composite is an empty object.
           | You can change the composite later on through an update operation.
           | For a **simple** topic: Do not specify a composite.
=========  =========================================================================================================================

Example 1: Creating a simple topic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topic -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{type_uri: "dm4.contacts.city",
       value: "Port-au-Prince"}' \
  | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json

.. code:: bash

  {
    "id": 3448,
    "uri": "",
    "type_uri": "dm4.contacts.city",
    "value": "Port-au-Prince",
    "composite": {
    }
  }

Example 2: Creating a complex topic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topic -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{type_uri: "dm4.notes.note",
       composite: {
         dm4.notes.title: "My title",
         dm4.notes.text: "<p>My test text here<p>"}}' \
  | jsonpretty

Response:

.. code:: bash

  curl localhost:8080/core/topic -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{type_uri: "dm4.notes.note",
       composite: {
         dm4.notes.title: "My title",
         dm4.notes.text: "<p>My test text here<p>"}}' \
  | jsonpretty

Associations
==============

Topic Types
===========

Get all topic type URIs
-----------------------

.. code:: bash

  GET /core/topictype

Example
^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topictype -i | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  [
    "dm4.contacts.address",
    "dm4.contacts.address_entry",
    "dm4.contacts.address_label",
    "dm4.contacts.city",
    "dm4.contacts.country",
    "dm4.contacts.email_address",
    "dm4.contacts.first_name",
    "dm4.contacts.institution",
    "dm4.contacts.institution_name",
    "dm4.contacts.last_name",
    "dm4.contacts.person",
    "dm4.contacts.person_name",
    "dm4.contacts.phone_entry",
    "dm4.contacts.phone_label",
    "dm4.contacts.phone_number",
    "dm4.contacts.postal_code",
    "dm4.contacts.street",
    "dm4.core.assoc_type",
    "dm4.core.cardinality",
    "dm4.core.data_type",
    "dm4.core.index_mode",
    "dm4.core.meta_meta_type",
    "dm4.core.meta_type",
    "dm4.core.plugin",
    "dm4.core.plugin_migration_nr",
    "dm4.core.plugin_name",
    "dm4.core.plugin_symbolic_name",
    "dm4.core.role_type",
    "dm4.core.topic_type",
    "dm4.files.content",
    "dm4.files.file",
    "dm4.files.file_name",
    "dm4.files.folder",
    "dm4.files.folder_name",
    "dm4.files.media_type",
    "dm4.files.path",
    "dm4.files.size",
    "dm4.notes.note",
    "dm4.notes.text",
    "dm4.notes.title",
    "dm4.topicmaps.description",
    "dm4.topicmaps.name",
    "dm4.topicmaps.topicmap",
    "dm4.topicmaps.visibility",
    "dm4.topicmaps.x",
    "dm4.topicmaps.y",
    "dm4.webbrowser.url",
    "dm4.webbrowser.web_resource",
    "dm4.webbrowser.web_resource_description",
    "dm4.webbrowser.webpage",
    "dm4.webclient.add_to_create_menu",
    "dm4.webclient.color",
    "dm4.webclient.editable",
    "dm4.webclient.icon",
    "dm4.webclient.is_searchable_unit",
    "dm4.webclient.js_field_renderer_class",
    "dm4.webclient.js_page_renderer_class",
    "dm4.webclient.rows",
    "dm4.webclient.search",
    "dm4.webclient.search_result",
    "dm4.webclient.search_term",
    "dm4.webclient.view_config",
    "dm4.webclient.viewable",
    "dm4.workspaces.description",
    "dm4.workspaces.name",
    "dm4.workspaces.workspace"
  ]

Get a topic type
-----------------

.. code:: bash

  GET /core/topictype/<Topic Type URI>

Example
^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topictype/dm4.webbrowser.webpage -i | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "id": 1008,
    "uri": "dm4.webbrowser.webpage",
    "type_uri": "dm4.core.topic_type",
    "value": "Webpage",
    "composite": {
    },
    "data_type_uri": "dm4.core.composite",
    "index_mode_uris": [
    ],
    "assoc_defs": [
      {
        "id": 1013,
        "uri": "dm4.webbrowser.url",
        "assoc_type_uri": "dm4.core.aggregation_def",
        "whole_topic_type_uri": "dm4.webbrowser.webpage",
        "whole_role_type_uri": "dm4.core.whole",
        "whole_cardinality_uri": "dm4.core.one",
        "part_topic_type_uri": "dm4.webbrowser.url",
        "part_role_type_uri": "dm4.core.part",
        "part_cardinality_uri": "dm4.core.one",
        "view_config_topics": [
        ]
      }
    ],
    "view_config_topics": [
      {
        "id": 1025,
        "uri": "",
        "type_uri": "dm4.webclient.view_config",
        "value": "WebpageRenderer",
        "composite": {
          "dm4.webclient.js_page_renderer_class": "WebpageRenderer"
        }
      }
    ]
  }

For details about the topic type format see DataFormat#TopicType.

Create a topic type
-------------------

.. code:: bash

  POST /core/topictype

  <the topic type to create>

For details about the topic type format see DataFormat#TopicType. 

For a create operation not all of the topic type's properties are involved:

==================  ==================================================================================================================
Property            Mandatory / Optional
==================  ==================================================================================================================
uri                 | Optional. Default URI is domain.project.topic_type_<ID>.
                    | You can change the URI later on through an update operation.
                    | However, the URI is typically specified at creation time.
value               | Optional. Default topic type name is an empty string.
                    | You can change the name later on through an update operation.
                    | However, the name is typically specified at creation time.
data_type_uri       Mandatory.
index_mode_uris     Optional. Default is no indexing.
 assoc_defs         | For **complex** topic types: Optional. Default is no child types.
                    | You can add child types later on through an update operation.
                    | However, the child types are typically specified at creation time.
                    | For **simple** topic types: Do not specify child types.
view_config_topics  Optional. Default is no view models.
==================  ==================================================================================================================

When creating a complex topic type: For details about the association definition format see DataFormat#AssociationDefinition.

For a create operation not all of the association definition's properties are involved:

=====================  =================================================================================================================
Property               Mandatory / Optional
=====================  =================================================================================================================
assoc_type_uri         Mandatory.
whole_cardinality_uri  | For a **"composition"**: Do not specify the parent cardinality ("one" is assumed).
                       | For an **"aggregation"**: Mandatory.
part_topic_type_uri    Mandatory.
part_cardinality_uri   Mandatory.
view_config_topics     Optional. Default is no view models.
=====================  =================================================================================================================

Example 1: Creating a simple topic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/topictype -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.planet",
       value: "Planet",
       data_type_uri: "dm4.core.text"}' \
  | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 3505,
    "uri": "spaceorg.planetarium.planet",
    "type_uri": "dm4.core.topic_type",
    "value": "Planet",
    "composite": {
    },
    "data_type_uri": "dm4.core.text",
    "index_mode_uris": [
    ],
    "assoc_defs": [
    ],
    "view_config_topics": [
    ]
  }

Example 2: Creating a complex topic type
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Before creating the complex topic type we create its (simple) child types:

.. code:: bash

  curl localhost:8080/core/topictype -X POST -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.planet_name",
       value: "Name",
       data_type_uri: "dm4.core.text"}'
  
  curl localhost:8080/core/topictype -X POST -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.mass",
       value: "Mass",
       data_type_uri: "dm4.core.number"}'
  
  curl localhost:8080/core/topictype -X POST -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.distance_to_earth",
       value: "Distance to Earth",
       data_type_uri: "dm4.core.number"}'

Then we create the complex topic type:

.. code:: bash

  curl localhost:8080/core/topictype -i -X POST -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.planet",
       value: "Planet",
       data_type_uri: "dm4.core.composite",
       assoc_defs: [
         {
           assoc_type_uri: "dm4.core.composition_def",
           part_topic_type_uri: "spaceorg.planetarium.planet_name",
           part_cardinality_uri: "dm4.core.one"
         }, {
           assoc_type_uri: "dm4.core.composition_def",
           part_topic_type_uri: "spaceorg.planetarium.mass",
           part_cardinality_uri: "dm4.core.one"
         }, {
           assoc_type_uri: "dm4.core.composition_def",
           part_topic_type_uri: "spaceorg.planetarium.distance_to_earth",
           part_cardinality_uri: "dm4.core.one"
         }]}' \
  | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 3697,
    "uri": "spaceorg.planetarium.planet",
    "type_uri": "dm4.core.topic_type",
    "value": "Planet",
    "composite": {
    },
    "data_type_uri": "dm4.core.composite",
    "assoc_defs": [
      {
        "id": 3702,
        "uri": "spaceorg.planetarium.planet_name",
        "assoc_type_uri": "dm4.core.composition_def",
        "whole_topic_type_uri": "spaceorg.planetarium.planet",
        "whole_role_type_uri": "dm4.core.whole",
        "whole_cardinality_uri": "dm4.core.one",
        "part_topic_type_uri": "spaceorg.planetarium.planet_name",
        "part_role_type_uri": "dm4.core.part",
        "part_cardinality_uri": "dm4.core.one",
        "view_config_topics": [
  
        ]
      },
      {
        "id": 3712,
        "uri": "spaceorg.planetarium.mass",
        "assoc_type_uri": "dm4.core.composition_def",
        "whole_topic_type_uri": "spaceorg.planetarium.planet",
        "whole_role_type_uri": "dm4.core.whole",
        "whole_cardinality_uri": "dm4.core.one",
        "part_topic_type_uri": "spaceorg.planetarium.mass",
        "part_role_type_uri": "dm4.core.part",
        "part_cardinality_uri": "dm4.core.one",
        "view_config_topics": [
  
        ]
      },
      {
        "id": 3722,
        "uri": "spaceorg.planetarium.distance_to_earth",
        "assoc_type_uri": "dm4.core.composition_def",
        "whole_topic_type_uri": "spaceorg.planetarium.planet",
        "whole_role_type_uri": "dm4.core.whole",
        "whole_cardinality_uri": "dm4.core.one",
        "part_topic_type_uri": "spaceorg.planetarium.distance_to_earth",
        "part_role_type_uri": "dm4.core.part",
        "part_cardinality_uri": "dm4.core.one",
        "view_config_topics": [
  
        ]
      }
    ],
    "index_mode_uris": [
    ],
    "view_config_topics": [
    ]
  }

Association Types
=================

Get all association type URIs
-------------------------------

.. code:: bash

  GET /core/assoctype

Example
^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/assoctype -i | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  [
    "dm4.core.aggregation",
    "dm4.core.aggregation_def",
    "dm4.core.association",
    "dm4.core.composition"
    "dm4.core.composition_def",
    "dm4.core.instantiation",
    "dm4.core.sequence",
    "dm4.topicmaps.association_mapcontext",
    "dm4.topicmaps.topic_mapcontext",
    "dm4.webclient.search_result_item",
    "dm4.workspaces.workspace_context",
  ]

Get an association type
------------------------

.. code:: bash

  GET /core/assoctype/<Association Type URI>

Example
^^^^^^^

.. code:: bash

  curl localhost:8080/core/assoctype/dm4.core.instantiation -i | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 10,
    "uri": "dm4.core.instantiation",
    "type_uri": "dm4.core.assoc_type",
    "value": "Instantiation",
    "composite": {
    },
    "data_type_uri": "dm4.core.text",
    "index_mode_uris": [
    ],
    "assoc_defs": [
    ],
    "label_config": [
    ],
    "view_config_topics": [
      {
        "id": 792,
        "uri": "",
        "type_uri": "dm4.webclient.view_config",
        "value": "",
        "composite": {
          "dm4.webclient.color": "rgb(41, 194, 225)"
        }
      }
    ]
  }

For details about the association type format see DataFormat#AssociationType.

Create an association type
----------------------------

.. code:: bash

  POST /core/assoctype

  <the association type to create>

For details about the association type format see DataFormat#AssociationType.

For a create operation not all of the association type's properties are involved:

==================  ==================================================================================================================
Property            Mandatory / Optional
==================  ==================================================================================================================
uri                 | Optional. Default URI is domain.project.assoc_type_<ID>.
                    | You can change the URI later on through an update operation.
                    | However, the URI is typically specified at creation time.
value               | Optional. Default association type name is an empty string.
                    | You can change the name later on through an update operation.
                    | However, the name is typically specified at creation time.
data_type_uri       Mandatory.
index_mode_uris     Optional. Default is no indexing.
 assoc_defs         | For **complex** association types: Optional. Default is no child types.
                    | You can add child types later on through an update operation.
                    | However, the child types are typically specified at creation time.
                    | For **simple** association types: Do not specify child types.
view_config_topics  Optional. Default is no view models.
==================  ==================================================================================================================

When creating a complex topic type: For details about the association definition format see DataFormat#AssociationDefinition.

For a create operation not all of the association definition's properties are involved:

=====================  =================================================================================================================
Property               Mandatory / Optional
=====================  =================================================================================================================
assoc_type_uri         Mandatory.
whole_cardinality_uri  | For a **"composition"**: Do not specify the parent cardinality ("one" is assumed).
                       | For an **"aggregation"**: Mandatory.
part_topic_type_uri    Mandatory.
part_cardinality_uri   Mandatory.
view_config_topics     Optional. Default is no view models.
=====================  =================================================================================================================

Example 1: Creating a simple association type
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/assoctype -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.discoverer",
       value: "Discoverer",
       data_type_uri: "dm4.core.text"}' \
  | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 2784,
    "uri": "spaceorg.planetarium.discoverer",
    "type_uri": "dm4.core.assoc_type",
    "value": "Discoverer",
    "composite": {
    },
    "data_type_uri": "dm4.core.text",
    "index_mode_uris": [
    ],
    "assoc_defs": [
    ],
    "label_config": [
    ],
    "view_config_topics": [
    ]
  }

Example 2: Creating a simple association type with a custom color
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

  curl localhost:8080/core/assoctype -i \
  -X POST \
  -H Content-Type:application/json \
  -d '{uri: "spaceorg.planetarium.satellite",
       value: "Satellite",
       data_type_uri: "dm4.core.text",
       view_config_topics: [{
         "type_uri": "dm4.webclient.view_config",
         "composite": {
           "dm4.webclient.color": "rgb(12, 70, 164)"
       }}]}' \
  | jsonpretty

Response:

.. code:: bash

  HTTP/1.1 200 OK
  Content-Type: application/json
  
  {
    "id": 2804,
    "uri": "spaceorg.planetarium.satellite",
    "type_uri": "dm4.core.assoc_type",
    "value": "Satellite",
    "composite": {
    },
    "data_type_uri": "dm4.core.text",
    "index_mode_uris": [
    ],
    "assoc_defs": [
    ],
    "label_config": [
    ],
    "view_config_topics": [
      {
        "id": 2809,
        "uri": "",
        "type_uri": "dm4.webclient.view_config",
        "value": "",
        "composite": {
          "dm4.webclient.color": "rgb(12, 70, 164)"
        }
      }
    ]
  }

Commands
========

Plugins
=======


