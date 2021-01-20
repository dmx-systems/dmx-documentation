.. _developer-guide:

###################
DMX Developer Guide
###################

This guide describes how to develop DMX plugins.

************
Introduction
************

What a DMX plugin can do
========================

To give you an impression what a DMX plugin can do, these might be the effects once you install one:

* The database contains an additional **data model** resp. existing data models are changed/extended. In DMX a data model consists basically of *Topic Types* and *Association Types*. Some of the types may appear in the DMX Webclient menus. E.g. the `dmx-notes <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-notes>`_ plugin creates the "Note" data model and let the "Note" topic type appear in the DMX Webclient search/create dialog, allowing the user to create/edit/search notes.
* The **DMX Webclient** is customized:

    * Customized topic/association **detail renderings**. E.g. the `dmx-datetime <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-datetime>`_ plugin provides formatters/editors for date and time values.
    * Customized **topicmap renderings**. An additional *Topicmap Type* becomes available in the search/create dialog. E.g. once `dmx-geomaps <https://git.dmx.systems/dmx-plugins/dmx-geomaps>`_ plugin is installed the user can create geo maps (besides normal topicmaps).
    * New commands appear in the topic/association **context menu**. E.g. the `dmx-dita <https://git.dmx.systems/dmx-plugins/dmx-dita>`_ plugin defines a topic type "DITA Processor", and adds a "Run" command to the context menu of DITA Processor topics.

* A **custom web front-end**, completely independent from DMX Webclient, becomes available at a dedicated URL. E.g. once the `dmx-mobile <https://git.dmx.systems/dmx-plugins/dmx-mobile>`_ plugin is installed you can launch its front-end via ``http://localhost:8080/systems.dmx.mobile/``. You might recognize that some front-end widgets are recycled from the DMX Webclient, e.g. the detail/form renderer and the search/create dialog.
* At back-end an additional **OSGi service** becomes available, consumable by other plugins. E.g. the `dmx-workspaces <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-workspaces>`_ plugin provides the `WorkspacesService <https://apidocs.dmx.systems/index.html?systems/dmx/workspaces/WorkspacesService.html>`_. Any plugin can consume it and call e.g. its ``createWorkspace()`` method in order to programmatically create a workspace.
* An additional **RESTful service** (with an underlying OSGi service) becomes available at a dedicated namespace URI. E.g. when the `dmx-topicmaps <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-topicmaps>`_ plugin is installed its RESTful service for creating/manipulating topicmaps becomes available under ``http://localhost:8080/topicmaps``. A RESTful service is accessible by any DMX front-end as well as by any external application, regardless of programming language. E.g. there is a DMX-CLI written in Python: `py4dmx <https://git.dmx.systems/dmx-contrib/py4dmx>`_.

A DMX plugin contains one or more of these effects, in an arbitrary combination.

In every case a plugin is a single ``.jar`` file prefixed by ``dmx-``, e.g. ``dmx-geomaps-0.1.jar``. A plugin is hot-deployed/stopped/updated at runtime by (re)moving that .jar file to/from ``bundle-deploy/``.

.. hint::

    The DMX platform itself is built from (about 20) plugins, e.g. `dmx-webclient <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-webclient>`_, `dmx-topicmaps <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-topicmaps>`_, `dmx-contacts <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-contacts>`_, `dmx-search <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-search>`_. These plugins are in no way different than the plugins you're developing. Furthermore there are various external plugins available (via separate download), e.g. `dmx-geomaps <https://git.dmx.systems/dmx-plugins/dmx-geomaps>`_, `dmx-mobile <https://git.dmx.systems/dmx-plugins/dmx-mobile>`_.

    Whenever this guide mentions a plugin it links to its source code. This provides you a great learning resource as you can explore how "real" DMX plugins are made.

The DMX platform
================

The DMX platform is a web application server written in Java.
It provides a framework for application developers.

Traditionally a web application consists of 3 parts: *data model*, *business logic* (server-side), and a front-end. In DMX there are no *applications*. Instead there is the DMX platform on one hand, and *DMX plugins* on the other, nothing else. One specialty about a DMX plugin (green) is that it can contain both, a back-end portion and a front-end portion, in one single hot-deployable ``.jar`` file.

.. _plugin-types:
.. figure:: _static/dmx-plugin-types.svg

In its back-end portion (P1) a plugin can a) define a data model (creating *Types* and their relationships), and/or b) provide business logic in form of an OSGi service (consumable by other plugins or through a REST API). In its client-side portion a plugin either *creates* a front-end (P2, P3), or *extends* an existing front-end (P4).

All installed plugins operate on the same semantic storage. The platform features a 5-level access control system (`private`, `confidential`, `collaborative`, `public`, `common`). Every storage operation is controlled on the basis of the current HTTP request's authorization. Plugins can operate on the semantic storage exclusively by using the *DMX Core Service* (`CoreService API <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_).

The heart of the platform is the *DMX Core*. The Core provides the runtime environment for DMX plugins. The Core a) loads plugins and manages their life-cycle, and b) governs all access to the semantic storage, and provides this duty as *DMX Core Service*.

.. hint::

    The most prominent DMX plugin is probably `dmx-webclient <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-webclient>`_ (see P3). It creates an extensible web front-end: the well-known "DMX Webclient".

.. _semantic-storage:

Semantic Storage
================

* The *Semantic Storage* holds a network of topics and associations. Unconnected subnetworks can exist. Topics and associations together are referred to as *DMX Objects*.
* In DMX there are no properties. Every thing/concept that exists in the world is represented as an independent object, not as a property of another object. Objects have relationships to other objects. DMX represents *meaning* not as properties but as relationships.
* Both, topics and associations are *value holders*. A value is either simple or composite. A *simple value* has a data type (text, number, boolean, html). A *composite value* is represented as a tree made of topics and associations. The root is either a topic or an association, the children are topics.
* Both, topics and associations are semantically typed. A type is identified by its URI. Examples for type URIs are ``dmx.contacts.person`` and ``dmx.core.composition``, denoting a topic type and an association type respectively.
* Type URIs are mappable to public vocabularies like Dublin Core or schema.org.
* Uniqueness: everything that exists in reality only once exists in DMX only once as well, e.g. a city, a person, or a postal address. E.g. the city "Berlin" exists exactly once and is shared between all postal addresses within Berlin.
* Multi user support: every DMX object is assigned to a *Workspace* which imposes an access level: *private*, *confidential*, *collaborative*, *public*, and *common*. Access control is enforced by the DMX Core on a per-request basis, by inspecting the request's ``Authorization`` header.

.. figure:: _static/dmx-person-example.svg

    2 persons live at the same place. The corresponding Address topic is shared between 2 Person topics. "Person" and "Address" are composite types, at the leaves are values of simple types. Below each topic its type URI is shown.

.. hint::

    This guide refers to the *Semantic Storage* just by "database" as well.

Immutability
------------

Besides *representation* the semantic storage is also responsible for data *manipulation*. Manipulating of shared semantic data is a tricky thing. As everything is stored *only once* and is potentially shared by many parents, changing a shared object might have unintended semantic *side effects*.

Example: an Address topic is shared between many Person topics, the semantics being: these persons live/work together. Now consider one particular person is moving. We must not change the value of the Address topic, as this would express wrong semantics. Only one person has moved, not all together.

To solve the problem of side effects, in DMX values are *immutable*, they never change. Only the associations forming the composite values do.

When issuing the move-person request the DMX Core creates a *new* Address topic and associates it to the person moved. Not quite: actually DMX will first look if such an address exists already, that is an Address topic with exactly the 4 particular children ("Petersburger Straße 101", "10247", "Berlin", "Germany"), and if so associate that one.

When updating a composite topic you never maintain the hierarchy associations manually. You just give a (fragment of the) new value hierarchy, and the Core will maintain the associations. This Core responsibility is called *Value Integration*. This works for arbitrary hierarchy depth.

.. figure:: _static/dmx-person-example-2.svg

    After one person has moved the 2 persons do not share a common Address topic anymore; the City topic "Berlin" and Country topic "Germany" are still shared between the 2 Address topics though.

Value vs. Entity
----------------

We've seen values in DMX are immutable. When an address's (parent) street and postal code (children) change, a *new* Address topic is created. Now lets consider another change-request, Peter Meyer changes his phone number, and apply the very same rule as with the address. As the particular person (parent) phone (child) combination does not yet exist, a *new* Person topic would be created. That is we have now 2 "Peter Meyer" topics when in reality there is only one. The uniqueness criteria (see :ref:`semantic-storage`) is violated.

The solution is to introduce another concept -- **Entity** -- and categorize composite types either as value type or entity type.

Values are immutable. Simple topics are always immutable. Examples for values: "Person Name", "First Name", "Last Name", "Street", "Postal Code", "City", "Address", "Time", "Date", "Geo Coordinate".

Entities on the other hand are mutable. An entity topic's child hierarchy may change while the topic keeps its identity. When modeling an entity type, you have to configure whose of its children make up its identity. A person could be identified e.g. either by the Name/Birthday/City of Birth combination or by a synthetic attribute like Social Security Number. Examples for entities: "Person", "Note".

Associative Model of Data
-------------------------

The above images suggest the DMX storage model is *graph* based: *nodes* and *edges*. Actually DMX is based on the *Associative Model of Data*, an substantial extension of the graph model.

.. figure:: _static/dmx-assoc-data-model.svg
   :width: 240px
   :align: left

While in a graph an edge is always a connection between 2 *nodes*, in the Associative Model of Data an edge can connect *edges* too. That is an edge connects either 2 nodes (as traditionally, see A1), or a node and an edge (A2), or 2 edges (A3).

Basically DMX makes associations objects of discourse too. Associations can be associated with other topics/associations, just like topics. This results in expressive highly-connected structures at both levels, data model and content (= *instances*).

.. figure:: _static/dmx-bookstore.svg
   :width: 440px
   :align: left

Here an associative data model for a "bookstore" application is shown. Note that "Customer", "Order", and "Stock" are modeled as *associations* (not as topics). That is a "Customer" *instance* is an *association* (not a topic), e.g. between "Michael Peters" (a "Person") and "Bookpages" (a "Legal Entity").

Furthermore note that "Order" is an association between "Customer" and "Book", that is an association between a topic and an association. An order has a date and a price. With every purchase the customer's "Points" account grows. For every book in stock the store keeps the information with how many points its purchase is rewarded.

.. hint::

    Learn more about the Associative Model of Data:

    | Joseph V. Homan, Paul J. Kovacs -- A Comparison Of The Relational Database Model And The Associative Database Model (6 page article)
    | *Issues in Information Systems*, Volume X, No. 1, 2009
    | http://iacis.org/iis/2009/P2009_1301.pdf

    | Simon Williams -- The Associative Model Of Data (24 page article)
    | *Journal of Database Marketing*, Volume 8, 4, 2001
    | https://link.springer.com/content/pdf/10.1057/palgrave.jdm.3240049.pdf

    | Simon Williams -- The Associative Model Of Data (book, 284 pages)
    | *Lazy Software*, 2nd edition, 2002
    | https://web.archive.org/web/20181219134621/http://sentences.com/docs/amd.pdf

Hot code replacement
====================

TODO: revise/extend

Technically the DMX platform is a Java/OSGi based application server. OSGi is a service oriented component architecture to support modularity. A DMX plugin is also an *OSGi Bundle*. A DMX application consists of one or more plugins. Plugins provide services consumable by other plugins, and exposed via a REST API. Plugins can be installed/updated/uninstalled without restarting the server (Hot Deployment). When a service becomes unavailable all plugins depending on that service shutdown. When the service becomes available again, all depending plugins are activated again. This has great advantages for both administration and development.

The 4 plugin archetypes
=======================

To find out what type of plugin (see :ref:`P1-P4 <plugin-types>` figure above) you're about to develop, ask yourself these questions:

* Will it have a back-end portion?
* Will it have a front-end portion? If yes:

    * Will it extend the DMX Webclient (or a custom front-end)? Or:
    * Will it create a custom front-end

Note: the "plugin type" is nothing explicit. You effectively change a plugin's type by adding/removing the respective portions/assets to/from it.

The following list gives you an impression what it means when you're developing a DMX plugin of the respective type:

Back-end-only (P1)
    A plugin that acts purely at the back-end. It defines a data model (optionally) and/or provides business logic:

    * Defines a **data model**: creating *Topic Types*, *Association Types*, *Role Types*, and default instances. Your data model can build upon, and even change, the data models provided by the platform or by other plugins. To do so in a controlled manner the platform provides a migration facility that runs the migrations provided by a plugin.

      A purely passive plugin that has no program logic but solely defines a data model is nothing unusual. Often in this case no custom Java code is required at all; you define a data model declaratively in JSON.

      Examples are basically the `dmx-base <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-base>`_, `dmx-bookmarks <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-bookmarks>`_, `dmx-contacts <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-contacts>`_, `dmx-datetime <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-datetime>`_, `dmx-events <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-events>`_, `dmx-notes <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-notes>`_, and the `dmx-tags <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-tags>`_ plugins. These effectively create the included DMX applications (*Note Taking*, *Contact Management*, *Bookmark Management*, and *Calendar*), just by providing data models. All the functionality on the other hand (e.g. create, search, edit, navigate, share, delete) is generic platform functionality.

    * Has custom Java code:

        * Provides **business logic** as **OSGi service**. A service method can be made RESTful just by adding JAX-RS annotations. JAX-RS knowledge is useful.
        * Consumes OSGi services provided by other plugins, or by the platform.
        * Listens to Core **events**, and events fired by other plugins.
        * Implements **plugin life-cycle** hooks.

Front-end (P2)
    A plugin that creates a user interface:

    * Has all the assets (``index.html``, ``.vue``, ``.js``, ``.css``, ...) to create a SPA front-end.
    * Communicates with the back-end via `dmx-api <https://git.dmx.systems/nodejs-modules/dmx-api>`_ library.

    Examples are the `dmx-webclient <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-webclient>`_ and `dmx-mobile <https://git.dmx.systems/dmx-plugins/dmx-mobile>`_ plugins.

    Use case **Headless CMS**: You're relying basically on DMX back-end capabilities (see P1) and build a custom front-end. You can freely choose the 3rd party front-end libraries/frameworks then. You're not bound to Vue or anything. For convenience you'll probably use the `dmx-api <https://git.dmx.systems/nodejs-modules/dmx-api>`_ library to communicate with the DMX back-end. And *if* you're using Vue you can possibly re-use some of the Vue components the DMX Webclient is built from, e.g. the detail renderer/form generator (`dmx-object-renderer <https://git.dmx.systems/nodejs-modules/dmx-object-renderer>`_) or the topicmap rendering (`dmx-topicmap-panel <https://git.dmx.systems/nodejs-modules/dmx-topicmap-panel>`_). See `npm <https://www.npmjs.com/~jri>`_ for available components.

    Such a plugin can have a back-end part as well (see P1).

Front-end Host (P3)
    A plugin that creates a user interface (see P2) that is extensible by other plugins (see P4):

    * Manages loading the front-end parts of installed plugins.

    An example is the `dmx-webclient <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-webclient>`_ plugin. Other plugins can extend it e.g. with additional topic/topicmap renderers and menu items.

    Such a plugin can have a back-end part as well (see P1).

Front-end Extension (P4)
    A plugin that extends the user interface created by another plugin:

    * Has all the assets (e.g. ``.vue``, ``.js``) as expected by the Front-end Host (see P3).

    Examples are the `dmx-accesscontrol <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-accesscontrol>`_, `dmx-base <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-base>`_, `dmx-contacts <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-contacts>`_, `dmx-datetime <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-datetime>`_, `dmx-details <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-details>`_, `dmx-help-menu <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-help-menu>`_, `dmx-search <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-search>`_, `dmx-topicmaps <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-topicmaps>`_, `dmx-typeeditor <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-typeeditor>`_, `dmx-workspaces <https://git.dmx.systems/dmx-platform/dmx-platform/-/tree/master/modules/dmx-workspaces>`_, and the `dmx-geomaps <https://git.dmx.systems/dmx-plugins/dmx-geomaps>`_ plugins. All their front-end parts extend the DMX Webclient.

    When developing an extension for the DMX Webclient you'll get in touch with `Vue <https://vuejs.org>`_ (for reactivity), `Vuex <https://vuex.vuejs.org>`_ (state management), and possibly `Element UI <https://element.eleme.io>`_ (widgets). These are the libraries the DMX Webclient is built from.

    You can start developing a DMX Webclient extension by cloning `dmx-plugin-template <https://git.dmx.systems/dmx-plugins/dmx-plugin-template>`_.

    Such a plugin can have a back-end part as well (see P1).

.. important::

    Building a DMX plugin is possible only if the DMX platform components exist in your local Maven repository. To fulfill this requirement you're requested to build the DMX platform from source first.

Building DMX platform from source
=================================

Requirements:

* **Java 8** (other versions do *not* work)
* **Maven**
* **Node.js**
* **Git**

Build DMX platform from source:

.. code-block:: bash

    $ git clone https://git.dmx.systems/dmx-platform/dmx-platform.git
    $ cd dmx-platform
    $ mvn install -P all

This builds all components of the DMX platform and installs them in your local Maven repository. All tests are run; you'll see a lot of information logged, cumulating in:

.. code-block:: text

    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 02:41 min
    ...

****************************
The plugin turn-around cycle
****************************

This chapter illustrates how to set up a plugin project, how to build and deploy a plugin, and how to redeploy it once you've made changes.

We develop a very simple plugin from scratch called "DMX Bookstore". According to the above :ref:`P1-P4 <plugin-types>` figure the Bookstore plugin is of type *back-end-only* (P1). It has no logic (no Java or JavaScript code). The only thing the Bookstore plugin does is defining a 1st version of the "Bookstore" data model. Mainly JSON is in use. So this type of plugin is quite easy to create, even for non-programmers.

On the other hand in conjunction with the DMX Webclient installing even a data-model-only plugin like DMX Bookstore has quite an impact. You can instantly create/edit Book topics. You do so via forms which are auto-generated from the data model. All the generic features like search, delete, hide, navigate, associate are there immediately. Basically this means: data model goes in, basis of a bookstore CMS comes out.

.. hint::

    Instead of creating a plugin you could, of course, create the "Bookstore" data model interactively in the DMX Webclient. The result would be the same. However, if a data model is packaged as a plugin this means you can *distribute* it. Other DMX users can install your plugin and make use of your data model.

Develop the "Bookstore" plugin
==============================

Create a ``dmx-bookstore/`` directory inside DMX's ``modules-external/`` directory. Plugin directories have a ``dmx-`` prefix by convention. The directory content follows a certain file structure and naming standard. The files are text files (xml, json, properties, java, js, css) and resources like images.

To create the *DMX Bookstore* plugin setup a directory structure as follows:

.. code-block:: text

    dmx-bookstore/
        pom.xml
        src/
            main/
                resources/
                    migrations/
                        migration1.json
                    plugin.properties

Create the file ``pom.xml`` with this content:

.. code-block:: xml

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

        <modelVersion>4.0.0</modelVersion>

        <name>DMX Bookstore</name>
        <groupId>my.domain</groupId>
        <artifactId>dmx-bookstore</artifactId>
        <version>0.1-SNAPSHOT</version>
        <packaging>bundle</packaging>

        <parent>
            <groupId>systems.dmx</groupId>
            <artifactId>dmx-plugin</artifactId>
            <version>5.0-SNAPSHOT</version>
        </parent>
    </project>

Create the file ``migration1.json``:

.. _bookstore_data_model:
.. code-block:: js

    [
      {
        "assoc_types": [
          {
            "value":       "Author",
            "uri":         "bookstore.author",
            "dataTypeUri": "dmx.core.text",
            "viewConfigTopics": [
              {
                "typeUri": "dmx.webclient.view_config",
                "children": {
                  "dmx.webclient.color": "hsl(60, 80%, 53%)",
                  "dmx.webclient.color#dmx.webclient.background_color": "hsl(60, 80%, 96%)"
                }
              }
            ]
          },
          {
            "value":       "Publication",
            "uri":         "bookstore.publication",
            "dataTypeUri": "dmx.core.text"
          }
        ]
      },
      {
        "topic_types": [
          {
            "value":       "Book Title",
            "uri":         "bookstore.book_title",
            "dataTypeUri": "dmx.core.text"
          },
          {
            "value":       "Book",
            "uri":         "bookstore.book",
            "dataTypeUri": "dmx.core.entity",
            "compDefs": [
              {
                "childTypeUri":        "bookstore.book_title",
                "childCardinalityUri": "dmx.core.one"
              },
              {
                "childTypeUri":        "dmx.contacts.person",
                "childCardinalityUri": "dmx.core.many",
                "customAssocTypeUri":  "bookstore.author"
               },
              {
                "childTypeUri":        "dmx.datetime.year",
                "childCardinalityUri": "dmx.core.one",
                "customAssocTypeUri":  "bookstore.publication"
              }
            ],
            "viewConfigTopics": [
              {
                "typeUri": "dmx.webclient.view_config",
                "children": {
                  "dmx.webclient.icon": "\uf02d"
                }
              }
            ]
          }
        ]
      }
    ]

Create the file ``plugin.properties``:

.. code-block:: text

    dmx.plugin.model_version = 1
    dmx.plugin.dependencies = systems.dmx.webclient, systems.dmx.contacts, systems.dmx.datetime

.. hint::

    when creating a Git repo for your DMX plugin it is convention its name is prefixed with ``dmx-``, eg. ``dmx-bookstore``.

Starting the DMX server
=======================

Before building and hot-deploying the Bookstore plugin let's start the DMX server.

In home directory ``dmx-platform/``:

.. code-block:: bash

    $ mvn pax:run

You'll see a lot of information logged, cumulating with:

.. code-block:: text

    ...
    Jun 03, 2020 3:18:53 PM systems.dmx.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 37, DMX plugins: 17, Activated: 17
    Jun 03, 2020 3:18:53 PM systems.dmx.core.impl.PluginManager activatePlugin
    INFO: ########## All DMX plugins active ##########
    Jun 03, 2020 3:18:53 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: DMX platform started in 1.48 sec
    Jun 03, 2020 3:18:53 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching DMX Webclient: http://localhost:8080/systems.dmx.webclient/
    ...

Then a browser windows opens automatically and displays the DMX Webclient.

The terminal is now occupied by the *Gogo* shell. Press the return key some times and you'll see its ``g!`` prompt.

Type the ``lb`` command to get the list of activated bundles:

.. code-block:: bash

    g! lb

The output  looks like this:

.. code-block:: text

    START LEVEL 6
       ID|State      |Level|Name
        0|Active     |    0|System Bundle (4.4.1)
       ...
       18|Active     |    5|DMX Facets (5.0.0.SNAPSHOT)
       19|Resolved   |    5|DMX Storage - Neo4j (5.0.0.SNAPSHOT)
       20|Active     |    5|DMX Webservice (5.0.0.SNAPSHOT)
       21|Active     |    5|DMX Events (5.0.0.SNAPSHOT)
       22|Active     |    5|DMX Core (5.0.0.SNAPSHOT)
       23|Active     |    5|DMX Workspaces (5.0.0.SNAPSHOT)
       24|Active     |    5|DMX Contacts (5.0.0.SNAPSHOT)
       25|Active     |    5|DMX Base (5.0.0.SNAPSHOT)
       26|Active     |    5|DMX Files (5.0.0.SNAPSHOT)
       27|Active     |    5|DMX Bookmarks (5.0.0.SNAPSHOT)
       28|Active     |    5|DMX Webclient (5.0.0.SNAPSHOT)
       29|Active     |    5|DMX Caching (5.0.0.SNAPSHOT)
       30|Active     |    5|DMX Notes (5.0.0.SNAPSHOT)
       31|Active     |    5|DMX Topicmaps (5.0.0.SNAPSHOT)
       32|Active     |    5|DMX Date/Time (5.0.0.SNAPSHOT)
       33|Active     |    5|DMX Access Control (5.0.0.SNAPSHOT)
       34|Active     |    5|DMX Config (5.0.0.SNAPSHOT)
       35|Active     |    5|DMX Tags (5.0.0.SNAPSHOT)
       36|Active     |    5|DMX Timestamps (5.0.0.SNAPSHOT)

The *DMX Bookstore* plugin does not yet appear in that list as it is not yet build.

Build the plugin
================

In another terminal:

.. code-block:: bash

    $ cd dmx-bookstore
    $ mvn clean package

This builds the plugin. After some seconds you'll see:

.. code-block:: text

    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 4.276s
    ...

Once build, DMX hot-deploys the plugin automatically. In the terminal where you've started DMX the logging informs you about plugin activation:

.. code-block:: text

    Jun 03, 2020 3:40:28 PM systems.dmx.core.osgi.PluginActivator start
    INFO: ========== Starting plugin "DMX Bookstore" ==========
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl readConfigFile
    INFO: Reading config file "/plugin.properties" for plugin "DMX Bookstore"
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl pluginDependencies
    INFO: Tracking 3 plugins for plugin "DMX Bookstore" [systems.dmx.webclient, systems.dmx.contacts, systems.dmx.datetime]
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl createInjectedServiceTrackers
    INFO: Tracking services for plugin "DMX Bookstore" SKIPPED -- no services consumed
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl addService
    INFO: Adding DMX core service to plugin "DMX Bookstore"
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl publishWebResources
    INFO: Publishing web resources of plugin "DMX Bookstore" SKIPPED -- no web resources provided
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl publishRestResources
    INFO: Publishing REST resources of plugin "DMX Bookstore" SKIPPED -- no REST resources provided
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl publishRestResources
    INFO: Registering provider classes of plugin "DMX Bookstore" SKIPPED -- no provider classes found
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl addService
    INFO: Adding Event Admin service to plugin "DMX Bookstore"
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activating plugin "DMX Bookstore" -----
    Jun 03, 2020 3:40:28 PM systems.dmx.core.impl.PluginImpl createPluginTopicIfNotExists
    INFO: Installing plugin "DMX Bookstore" in the database
    Jun 03, 2020 3:40:29 PM systems.dmx.core.impl.MigrationManager runPluginMigrations
    INFO: Running 1 migrations for plugin "DMX Bookstore" (installed model: version 0, required model: version 1)
    Jun 03, 2020 3:40:29 PM systems.dmx.core.impl.MigrationManager$MigrationInfo readMigrationConfigFile
    INFO: Reading migration config file "/migrations/migration1.properties" SKIPPED -- file does not exist
    Jun 03, 2020 3:40:29 PM systems.dmx.core.impl.MigrationManager _runMigration
    INFO: Running migration 1 of plugin "DMX Bookstore" (runMode=ALWAYS, isCleanInstall=true)
    Jun 03, 2020 3:40:29 PM systems.dmx.core.impl.MigrationManager readMigrationFile
    INFO: Reading migration file "/migrations/migration1.json"
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.MigrationManager updateVersionNumber
    INFO: Updating installed model: version 1
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.DMXObjectModelImpl update
    INFO: Updating topic 4358 (typeUri="dmx.core.plugin")
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.DMXObjectModelImpl delete
    INFO: Deleting association 4366 (typeUri="dmx.core.instantiation")
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.DMXObjectModelImpl delete
    INFO: Deleting association 4365 (typeUri="dmx.core.composition")
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.PluginImpl registerListeners
    INFO: Registering event listeners of plugin "DMX Bookstore" SKIPPED -- no event listeners implemented
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.PluginImpl registerProvidedService
    INFO: Registering OSGi service of plugin "DMX Bookstore" SKIPPED -- no OSGi service provided
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activation of plugin "DMX Bookstore" complete -----
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 38, DMX plugins: 18, Activated: 18
    Jun 03, 2020 3:40:30 PM systems.dmx.core.impl.PluginManager activatePlugin
    INFO: ########## All DMX plugins active ##########
    Jun 03, 2020 3:40:30 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching DMX Webclient (http://localhost:8080/systems.dmx.webclient/) SKIPPED -- already launched
    ...

When you type again ``lb`` in the DMX terminal you'll see the *DMX Bookstore* plugin now appears in the list of activated bundles:

.. code-block:: text

    START LEVEL 6
       ID|State      |Level|Name
        0|Active     |    0|System Bundle (4.4.1)
       ...
       33|Active     |    5|DMX Access Control (5.0.0.SNAPSHOT)
       34|Active     |    5|DMX Config (5.0.0.SNAPSHOT)
       35|Active     |    5|DMX Tags (5.0.0.SNAPSHOT)
       36|Active     |    5|DMX Timestamps (5.0.0.SNAPSHOT)
       37|Active     |    5|DMX Bookstore (0.1.0.SNAPSHOT)

Try out the plugin
==================

Now you can try out the plugin. In the DMX Webclient login as user "admin" and leave the password field empty. The *Create* menu appears and when you open it you'll see the new type *Book* listed. Thus, you can create tags now. Additionally you can associate tags to your content topics, search for tags, and navigate along the tag associations, just as you do with other topics. TODO: update

The result so far: the *DMX Bookstore* plugin provides a new topic type definition or, in other words: a data model. All the active operations on the other hand like create, edit, search, delete, associate, and navigate are provided by the DMX Webclient at a generic level, and are applicable to your new topic type as well.

Modify the plugin and redeploy
==============================

Once you've modified the plugin you have to build it again (TODO: only required for back-end development). Just like before in the plugin terminal:

.. code-block:: bash

    $ mvn clean package

Once building is complete the changed plugin is redeployed automatically. You'll notice activity in the DMX terminal:

.. code-block:: text

    Jun 03, 2020 4:02:16 PM systems.dmx.core.osgi.PluginActivator stop
    INFO: ========== Stopping plugin "DMX Bookstore" ==========
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginImpl removeService
    INFO: Removing DMX core service from plugin "DMX Bookstore"
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginImpl removeService
    INFO: Removing Event Admin service from plugin "DMX Bookstore"
    ...
    ...
    Jun 03, 2020 4:02:16 PM systems.dmx.core.osgi.PluginActivator start
    INFO: ========== Starting plugin "DMX Bookstore" ==========
    ...
    ...
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activating plugin "DMX Bookstore" -----
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginImpl createPluginTopicIfNotExists
    INFO: Installing plugin "DMX Bookstore" in the database SKIPPED -- already installed
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.MigrationManager runPluginMigrations
    INFO: Running migrations for plugin "DMX Bookstore" SKIPPED -- installed model is up-to-date (version 1)
    ...
    ...
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activation of plugin "DMX Bookstore" complete -----
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 38, DMX plugins: 18, Activated: 18
    Jun 03, 2020 4:02:16 PM systems.dmx.core.impl.PluginManager activatePlugin
    INFO: ########## All DMX plugins active ##########
    Jun 03, 2020 4:02:16 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching DMX Webclient (http://localhost:8080/systems.dmx.webclient/) SKIPPED -- already launched
    ...

In contrast to the initial build of the plugin you can recognize some differences in this log:

* The old version of the plugin currently deployed is stopped.
* The new version of the plugin is deployed (that is *started* and *activated*) right away.
* The plugin is *not* installed again in the database as already done while initial build.
* The migration is *not* run again as already done while initial build.

To ensure the DMX Webclient is aware of the changed plugin press the browser's reload button.

Stopping the DMX server
=======================

To stop the DMX server, in the Gogo shell type:

.. code-block:: bash

    g! stop 0

This stops all bundles, shuts down the webserver, and the database.

**********
Migrations
**********

A *migration* is a sequence of database operations that is executed exactly once in the lifetime of a particular DMX installation. You as a developer are responsible for equipping your plugin with the required migrations. Migrations serve several purposes:

1. Define the plugin's data model. That is, storing new topic type definitions and association type definitions in the database. E.g. a *Books* plugin might define the types *Book*, *Title*, and *Author*.

2. A newer version of your plugin might extend or modify the data model defined by the previous version of your plugin. The migration of the updated plugin change the stored type definitions *and* transforms existing content if necessary.

3. The application logic of a newer version of your plugin changes in a way it is not compatible anymore with the existing database content. The migration must transform the existing content then.

So, the purpose expressed in points 2. and 3. is to make your plugin *upgradable*. That is, keeping existing database content *in-snyc* with the plugin logic. By providing the corresponding migrations you make your plugin *compatible* with the previous plugin version.

The migration machinery
=======================

Each plugin comes with its own data model. For each plugin DMX keeps track what data model version is currently installed. It does so by storing the version of the installed data model in the database as well. The data model version is an integer number that starts at 0 and is increased consecutively: 0, 1, 2, and so on. Each version number (except 0) corresponds with a particular migration. The migration with number *n* is responsible for transforming the database content from version *n-1* to version *n*.

You as the developer know 2 things about your plugin: a) Which plugin version relies on which data model version, and b) How to transform the database content in order to advance from a given data model version to the next. So, when you ship your plugin you must equip it with 2 things:

* The information what data model version the plugin relies on.
* All the migrations required to update to that data model version.

The relationship between plugin version and data model version might look as follows:

==============  ==================
Plugin Version  Data Model Version
==============  ==================
0.1             2
0.2             5
0.2.1           5
0.3             6
==============  ==================

If e.g. version 0.1 of the plugin is currently installed, the database holds "2" as the current data model version. When the user updates to version 0.3 of the plugin, DMX's migration machinery will recognize that data model version 2 is present but version 6 is required. As a consequence DMX will consecutively run migrations 3 through 6. Once completed, the database holds "6" as the current data model version.

Thus, the users database will always be compatible with the installed version of the plugin. Furthermore, the user is free to skip versions when upgrading the plugin.

Plugin configuration
====================

If your plugin comes with its own data model you must tell DMX the data model version it relies on. To do so, set the ``dmx.plugin.model_version`` configuration property in the ``plugin.properties`` file, e.g.:

.. code-block:: text

    dmx.plugin.model_version = 2

DMX's migration machinery takes charge of running the plugin's migrations up to that configured number. If your plugin comes with no data model, you can specify ``0`` resp. omit the ``dmx.plugin.model_version`` property as ``0`` is its default value.

Usually each plugin has its own ``plugin.properties`` file. It allows the developer to configure certain aspects of the plugin. The name of the ``plugin.properties`` file and its path within the plugin directory is fixed:

.. code-block:: text

    dmx-myplugin/src/main/resources/plugin.properties

If no ``plugin.properties`` file is present, the default configuration values apply.

The two kinds of migrations
===========================

As you've already learned, migrations serve different (but related) purposes: some just *create* new type definitions and others *modify* existing type definitions and/or transform existing database content. To support the developer with these different tasks DMX offers two kinds of migrations:

* A **Declarative Migration** is a JSON file that declares 4 kinds of things: topic types, association types, topics, associations. Use a declarative migration to let DMX create new types and instances in the database. Use a declarative migration to let your plugin setup the initial type definitions.

  With a declarative migration you can only create new things. You can't modify existing things. All you do with a declarative migration you could achieve with an imperative migration as well, but as long as you just want create new things, it is more convenient to do it declaratively.

* An **Imperative Migration** is a Java class that has access to the *DMX Core Service* (`CoreService API <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_). Thus, you can perform arbitrary database operations like creation, retrieval, update, deletion. Use an imperative migration when (a later version of) your plugin needs to modify existing type definitions and/or transform existing database content.

The developer can equip a plugin with an arbitrary number of both, declarative migrations and imperative migrations.

Directory structure
===================

In order to let DMX find the plugin's migration files, you must adhere to a fixed directory structure and file names. Each migration file must contain its number, so DMX can run them consecutively.

A declarative migration must be named ``migration<nr>.json`` and must be located in the plugin's ``src/main/resources/migrations/`` directory.

An imperative migration must be named ``Migration<nr>.java`` and must be located in the plugin's ``src/main/java/<your plugin package>/migrations/`` directory.

Example:

.. code-block:: text

    dmx-myplugin/
        src/
            main/
                java/
                    mydomain/
                        myplugin/
                            migrations/
                                Migration2.java
                                Migration5.java
                resources/
                    migrations/
                        migration1.json
                        migration3.json
                        migration4.json
                        migration6.json
                    plugin.properties

This example plugin would have set ``dmx.plugin.model_version`` to 6 (configured in ``plugin.properties``), so 6 migrations are involved. 4 are declarative and 2 are imperative here.

Important: for each number between 1 and ``dmx.plugin.model_version`` exactly one migration file must exist. That is *either* a declarative migration file *or* an imperative migration file.

It would be invalid if for a given number a) no migration file exists, or b) two migration files exist (one declarative and one imperative). In these cases the DMX migration machinery throws an error and the plugin is not activated.

Writing a declarative migration
===============================

A declarative migration is a JSON file with exactly one JSON Object in it. In a declarative migration you can define 4 things: topic types, association types, topics, associations. The general format is:

.. code-block:: js

    {
        topic_types: [
            ...
        ],
        assoc_types: [
            ...
        ],
        topics: [
            ...
        ],
        associations: [
            ...
        ]
    }

Each of the 4 sections is optional.

As an example see the (simplified) migration that defines the *Note* topic type. This migration is part of the *DMX Notes* plugin:

.. code-block:: js

    {
        topic_types: [
            {
                value:       "Title",
                uri:         "dmx.notes.title",
                dataTypeUri: "dmx.core.text"
            },
            {
                value:       "Text",
                uri:         "dmx.notes.text",
                dataTypeUri: "dmx.core.html"
            },
            {
                value:       "Note",
                uri:         "dmx.notes.note",
                dataTypeUri: "dmx.core.entity",
                compDefs: [
                    {
                        childTypeUri:        "dmx.notes.title",
                        childCardinalityUri: "dmx.core.one"
                    },
                    {
                        childTypeUri:        "dmx.notes.text",
                        childCardinalityUri: "dmx.core.one"
                    }
                ],
                viewConfigTopics: [
                    {
                        typeUri: "dmx.webclient.view_config",
                        children: {
                            dmx.webclient.icon: "\uf24a",
                            dmx.webclient.add_to_create_menu: true
                        }
                    }
                ]
            }
        ]
    }

As you see, this migration defines 3 topic types (and no other things): *Title* and *Text* are 2 simple types, and *Note* is a composite type. A Note is composed of one Title and one Text.

.. _writing_an_imperative_migration:

Writing an imperative migration
===============================

An imperative migration is a Java class that is derived from ``systems.dmx.core.service.Migration`` and that overrides the ``run()`` method. The ``run()`` method is called by DMX to run the migration.

Within the migration you have access to the *DMX Core Service* through the ``dmx`` object. By the means of the Core Service you can perform arbitrary database operations. Typically this involves importing further objects from the `systems.dmx.core <https://apidocs.dmx.systems/index.html?systems/dmx/core/package-summary.html>`_ API.

As an example see a migration that comes with the *DMX Topicmaps* plugin:

.. code-block:: java

    package systems.dmx.topicmaps.migrations;

    import systems.dmx.core.TopicType;
    import systems.dmx.core.service.Migration;

    public class Migration3 extends Migration {

        @Override
        public void run() {
            TopicType type = dmx.getTopicType("dmx.topicmaps.topicmap");
            type.addCompDef(mf.newCompDefModel(
                "dmx.topicmaps.topicmap", "dmx.topicmaps.state", "dmx.core.one")
            );
        }
    }

Here a *Composition Definition* is added to the *Topicmap* type subsequently.

************************
Writing custom Java code
************************

In the previous section you've seen how to manipulate a DMX data model with Java code. Were you wondering what these ``dmx`` and ``mf`` objects are? Well these are instances of `CoreService <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_ and `ModelFactory <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/ModelFactory.html>`_ respectively. But first things first.

What, besides manipulating a data model, can a DMX plugin do with custom Java code at the back-end:

* **Use the DMX Core Service**. The *DMX Core Service* provides generic database operations to deal with the DMX Core objects: *Topics*, *Associations*, *Topic Types*, *Association Types*.

* **Listen to DMX Core events**. In particular situations the DMX Core fires events, e.g. before and after it creates a new topic in the database. Your plugin can listen to these events and react in its own way. Thus, the *DMX Workspaces* plugin e.g. ensures that each new topic is assigned to a workspace. TODO: custom events

* **Provide a service**. Your plugin can make its business logic, that is its service methods, accessible by other plugins (via OSGi) and/or by external applications (via HTTP/REST). Example: the service provided by the *DMX Topicmaps* plugin includes methods to add a topic to a topicmap or to change the topic's coordinates within a topicmap.

* **Consume services provided by other plugins**. Example: in order to investigate a topic's workspace assignments and the current user's memberships the *DMX Access Control* plugin consumes the service provided by the *DMX Workspaces* plugin.

Whether you need to add custom Java code to a DMX plugin at all depends on the purpose of the plugin. Plugins without custom Java code include those who e.g. solely define a data model or provide (JavaScript) front-end code only.

.. _the-plugin-main-class:

The plugin main class
=====================

In case you want add custom Java code to your plugin you must first write a *plugin main class*.

By convention the plugin main class ends with ``Plugin``. The corresponding ``.java`` file must be located in the plugin's ``src/main/java/<your plugin package>/`` directory.

Example:

.. code-block:: text

    dmx-myplugin/
        src/
            main/
                java/
                    mydomain/
                        myplugin/
                            MyPlugin.java

Here the plugin package is ``mydomain.myplugin`` and the plugin main class is ``MyPlugin``.

The plugin main class must be derived from ``systems.dmx.core.osgi.PluginActivator``:

.. code-block:: java

    package mydomain.myplugin;

    import systems.dmx.core.osgi.PluginActivator;

    public class MyPlugin extends PluginActivator {
    }

3 things are illustrated here:

* The plugin's package name should relate to a domain under your control.
* The class ``PluginActivator`` needs to be imported.
* The plugin main class must be derived from ``PluginActivator`` and must be public.

When writing a plugin main class you must adapt your plugin's ``pom.xml`` accordingly:

* Add a ``<build>`` element to tell the *Maven Bundle Plugin* what your plugin main class is. Specify the fully-qualified class name. (DMX uses the Maven Bundle Plugin for packaging your plugin as a ``.jar`` bundle.)

.. code-block:: xml

    <project>
        <modelVersion>4.0.0</modelVersion>

        <name>My Plugin</name>
        <groupId>my.domain</groupId>
        <artifactId>dmx-myplugin</artifactId>
        <version>0.1-SNAPSHOT</version>
        <packaging>bundle</packaging>

        <parent>
            <groupId>systems.dmx</groupId>
            <artifactId>dmx-plugin</artifactId>
            <version>5.0-SNAPSHOT</version>
        </parent>

        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.felix</groupId>
                    <artifactId>maven-bundle-plugin</artifactId>
                    <configuration>
                        <instructions>
                            <Bundle-Activator>mydomain.myplugin.MyPlugin</Bundle-Activator>
                        </instructions>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>

DMX Java API
============

The DMX Java API consists of the ``systems.dmx`` package hierarchy. Most central is the `systems.dmx.core <https://apidocs.dmx.systems/index.html?systems/dmx/core/package-summary.html>`_ package and its sub-packages. These contain the basic DMX objects (`Topic <https://apidocs.dmx.systems/index.html?systems/dmx/core/Topic.html>`_, `Assoc <https://apidocs.dmx.systems/index.html?systems/dmx/core/Assoc.html>`_, `TopicType <https://apidocs.dmx.systems/index.html?systems/dmx/core/TopicType.html>`_, `AssocType <https://apidocs.dmx.systems/index.html?systems/dmx/core/AssocType.html>`_, ...) and services (most notably the `CoreService <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_).

Overview of the interfaces in package `systems.dmx.core <https://apidocs.dmx.systems/index.html?systems/dmx/core/package-summary.html>`_:

.. _dmx-core-classes:
.. figure:: _static/dmx-core-classes.svg

Note that both ``Topic`` and ``Assoc`` have a common base class: ``DMXObject``. The commonalities include a) both are typed (``getTypeUri()``), b) both are referable by-ID and by-URI, and, in particular c) both are *value holders*, be it a simple one (`SimpleValue <https://apidocs.dmx.systems/index.html?systems/dmx/core/model/SimpleValue.html>`_ (green), from `systems.dmx.core.model <https://apidocs.dmx.systems/index.html?systems/dmx/core/model/package-summary.html>`_ package) or a composite one (`ChildTopics <https://apidocs.dmx.systems/index.html?systems/dmx/core/ChildTopics.html>`_). Furthermore there are common traversal (``getAssocs()``, ``getRelatedTopics()``, ``getRelatedAssocs()``) and manipulation (``update()``, ``delete()``) methods.

Let's have a closer look at the 5 ``DMXObject`` fields:

===============  =====================================================  ===============
Field            Meaning                                                Type
===============  =====================================================  ===============
``id``           unique per DMX instance                                long
``uri``          globally unique, can be refer to a public vocabulary,  String
                 mandatory for types, often not used for instances
``typeUri``      URI of type of topic/assoc                             String
``value``        | for simple topic/assoc: the immutable value          ``SimpleValue``
                 | for composite topic/assoc: the calculated label
``childTopics``  for composite topic/assoc: hierarchy of child topics   ``ChildTopics``
===============  =====================================================  ===============

The fields are accessible only through the `DMXObject` getters (``getId()``, ``getUri()``, ...).

.. hint::

    The DMX Java API documentation is available at https://apidocs.dmx.systems.

DMXType
-------

``DMXType`` is derived from ``Topic`` and inherits the ``uri`` field from ``DMXObject``. Furhermore ``DMXType`` is an ``Iterable<String>``: it iterates over the type's ``compDefUri`` s.

.. _the-model-hierarchy:

The "Model" hierarchy
---------------------

.. figure:: _static/dmx-model-classes.svg
   :width: 240px
   :align: left

A peculiarity of the DMX Java API is that for the :ref:`hierarchy of Core classes <dmx-core-classes>` (see yellow boxes) an isomorph hierarchy of "Model" classes exists in the `systems.dmx.core.model <https://apidocs.dmx.systems/index.html?systems/dmx/core/model/package-summary.html>`_ package. That is e.g. for *Core class* ``Topic`` the corresponding *Model class* is ``TopicModel``.

Every *Core instance* (e.g. a ``Topic`` object) is associated (in the OO-sense) with a corresponding *Model instance*. While the core instance represents the "real thing" -- a database-attached object you can perform operations like ``update()`` and ``delete()`` on -- a model instance represents just the underlying (serializable) data of that object. When you call e.g. ``getId()`` on a ``Topic`` object, DMX actually delegates to the topic's model instance.

Why does the Core/Model duality exists in the first place? Consider a *create* operation. To create something you'll use the :ref:`DMX Core Service <using_the_dmx_core_service>` (see next section). The DMX Core Service allows you to create a complex composite structure in a single e.g. ``createTopic()`` call (which can be also be remote-triggered, by a single POST request to ``/core/topic``). The passed data basically has the same structure as a topic retrieved from DB. (The same basically applies to an *update* operation.) So you need a way to convey *topic data* independent from a topic itself. The vehicle for that is a ``TopicModel`` instance. (Note that *data* and *model* is used synonymously here.)

Every time you want *create* or *update* a Core instance (e.g. a ``Topic`` object) you have to construct a corresponding Model instance first (e.g. a ``TopicModel`` object) that holds the topic data. To create a Model instance you'll use the `ModelFactory <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/ModelFactory.html>`_ provided by the DMX Core. Within your plugin a ``ModelFactory`` instance is available as the ``mf`` object, which is available automatically in both the :ref:`plugin main class <the-plugin-main-class>`, and in an :ref:`imperative migration <writing_an_imperative_migration>` (through the `PluginActivator <https://apidocs.dmx.systems/index.html?systems/dmx/core/osgi/PluginActivator.html>`_ and `Migration <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/Migration.html>`_ base classes respectively).

This example constructs a Model instance usable for creating a "Book" topic according to the :ref:`"Bookstore" data model <bookstore_data_model>`:

.. code-block:: java

    import systems.dmx.core.model.TopicModel;
    ...
    TopicModel book1 = mf.newTopicModel("bookstore.book", mf.newChildTopicsModel()
        .set("bookstore.book_title", "Understanding Computers and Cognition")
        .add("dmx.contacts.person#bookstore.author", mf.newChildTopicsModel()
            .set("dmx.contacts.person_name", mf.newChildTopicsModel()
                .set("dmx.contacts.first_name", "Terry")
                .set("dmx.contacts.last_name", "Winograd")
            )
        )
        .add("dmx.contacts.person#bookstore.author", mf.newChildTopicsModel()
            .set("dmx.contacts.person_name", mf.newChildTopicsModel()
                .set("dmx.contacts.first_name", "Fernando")
                .set("dmx.contacts.last_name", "Flores")
            )
        )
        .set("dmx.datetime.year#bookstore.publication", 1986)
    )

.. _using_the_dmx_core_service:

Using the DMX Core Service
==========================

The *DMX Core Service* provides generic database operations (create, retrieve, update, delete) to deal with the DMX Core objects: *Topics*, *Associations*, *Topic Types*, *Association Types*.

Within your plugin you'll use the Core Service through the ``dmx`` object, which is an instance of `CoreService <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_ (API). The ``dmx`` object is available automatically in both the :ref:`plugin main class <the-plugin-main-class>`, and in an :ref:`imperative migration <writing_an_imperative_migration>` (through the `PluginActivator <https://apidocs.dmx.systems/index.html?systems/dmx/core/osgi/PluginActivator.html>`_ and `Migration <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/Migration.html>`_ base classes respectively).

The following provides an overview of the available Core Service methods.

Topics
------

.. code-block:: java

    Topic getTopic(long topicId);

    Topic getTopicByUri(String uri);

    List<Topic> getTopicsByType(String topicTypeUri);

    Iterable<Topic> getAllTopics();

You can retrieve topics by-ID, by-URI, or by-type. You can also retrieve *all* topics.

.. code-block:: java

    Topic getTopicByValue(String typeUri, SimpleValue value);

    List<Topic> getTopicsByValue(String typeUri, SimpleValue value);

    List<Topic> queryTopics(String typeUri, String query);

    QueryResult queryTopicsFulltext(String query, String typeUri, boolean searchChildTopics);

You can retrieve topics by-value. Use the singular ``getTopicByValue`` method for retrieving by *unique* value; it throws an exception if more than one topic is found. The ``query...`` methods support Lucene query syntax: most notably *wildcards* (``*`` and ``?`` for matching arbitrary characters or a single character respectively), *phrase search* (``"..."``), combination of search terms (``AND`` and ``OR``), and *escaping* (``\``). The ``queryTopicsFulltext`` method searches for matching words in entire (HTML) text values; furthermore this methods can search in composite values (topic trees). For details see the `CoreService API <https://apidocs.dmx.systems/index.html?systems/dmx/core/service/CoreService.html>`_.

.. code-block:: java

    Topic createTopic(TopicModel model);

    void updateTopic(TopicModel updateModel);

    void deleteTopic(long topicId);

To *create* a topic you need to construct a ``TopicModel`` object first to hold all the topic data needed. Typically you'll specify the topic's type and value. In case of a simple value you'll provide a ``SimpleValue`` object, in case of a composite value (that is a topic tree) you'll build a hierarchy of ``ChildTopicsModel`` objects. For how to construct a Model object see above section :ref:`the-model-hierarchy`.

Also for *updating* a topic you need to construct a ``TopicModel`` object first to hold the new topic data. *Partial updates* are supported: you're free to specify only data that actually change. If e.g. a composite topic's type and some child topics stay unchanged you're not required to specify these in the update model.

All these DMX Core Service methods are callable via DMX REST API as well.

Associations
------------

.. code-block:: java

    Assoc getAssoc(long assocId);

    List<Assoc> getAssocsByType(String assocTypeUri);

    List<Assoc> getAssocs(long topic1Id, long topic2Id);

    List<Assoc> getAssocs(long topic1Id, long topic2Id, String assocTypeUri);

    Assoc getAssocBetweenTopicAndTopic(String assocTypeUri, long topic1Id, long topic2Id,
                                       String roleTypeUri1, String roleTypeUri2);

    Assoc getAssocBetweenTopicAndAssoc(String assocTypeUri, long topicId, long assocId,
                                       String topicRoleTypeUri, String assocRoleTypeUri);

    Iterable<Assoc> getAllAssocs();

.. code-block:: java

    Assoc getAssocByValue(String typeUri, SimpleValue value);

    List<Assoc> queryAssocs(String typeUri, String query);

.. code-block:: java

    Assoc createAssoc(AssocModel model);

    void updateAssoc(AssocModel updateModel);

    void deleteAssoc(long assocId);

Topic Types
-----------

.. code-block:: java

    TopicType getTopicType(String topicTypeUri);

    List<TopicType> getAllTopicTypes();

.. code-block:: java

    TopicType createTopicType(TopicTypeModel model);

    void updateTopicType(TopicTypeModel updateModel);

    void deleteTopicType(String topicTypeUri);

Association Types
-----------------

.. code-block:: java

    AssocType getAssocType(String assocTypeUri);

    List<AssocType> getAllAssocTypes();

.. code-block:: java

    AssocType createAssocType(AssocTypeModel model);

    void updateAssocType(AssocTypeModel updateModel);

    void deleteAssocType(String assocTypeUri);

Listen to DMX Core events
=========================

In particular situations the DMX Core fires events, e.g. before and after it creates a new topic in the database. Your plugin can listen to these events and react in its own way.

Listening to a DMX Core event means implementing the corresponding listener interface. A listener interface consist of just one method: the *listener method*. That method is called by the DMX Core when the event is fired. The listener interfaces are located in package ``systems.dmx.core.service.event``.

To listen to a DMX Core event, in the plugin main class you must:

* Import the listener interface.
* Declare the plugin main class implements that interface.
* Implement the listener method. Use the ``@Override`` annotation.
* Import the classes appearing in the listener method arguments.

Example:

.. code-block:: java

    package mydomain.myplugin;

    import systems.dmx.core.Topic;
    import systems.dmx.core.model.TopicModel;
    import systems.dmx.core.osgi.PluginActivator;
    import systems.dmx.core.service.Directives;
    import systems.dmx.core.service.event.PostCreateTopic;
    import systems.dmx.core.service.event.PostUpdateTopic;

    import java.util.logging.Logger;



    public class MyPlugin extends PluginActivator implements PostCreateTopic, PostUpdateTopic {

        private Logger log = Logger.getLogger(getClass().getName());

        @Override
        public void postCreateTopic(Topic topic) {
            log.info("### Topic created: " + topic);
        }

        @Override
        public void postUpdateTopic(Topic topic, TopicModel newModel, TopicModel oldModel) {
            log.info("### Topic updated: " + topic + "\nOld topic: " + oldModel);
        }
    }

This example plugin listens to 2 DMX Core events: ``POST_CREATE_TOPIC`` and ``POST_UPDATE_TOPIC``.

These particular events are fired *after* the DMX Core has created resp. updated a topic. The DMX Core passes the created/updated topic to the respective listener method. In case of "update" the previous topic content (``oldModel``) is also passed to enable the plugin to investigate what exactly has changed.

The example plugin just logs the created resp. updated topic. In case of "update" the previous topic content is logged as well.

A [[DMXCoreEvents|list of all DMX Core events]] is available in the reference section.

Providing a service
===================

Your plugin can make its business logic, that is its service methods, accessible by other plugins (via OSGi) and/or by external applications (via HTTP/REST).

The service interface
---------------------

For a plugin to provide a service you must define a *service interface*. The service interface contains all the method signatures that make up the service. When other plugins consume your plugin's service they do so via the service interface.

To be recognized the service interface *must* end its name by ``...Service``. The service interface must be declared ``public`` and is a regular Java interface.

A DMX plugin can define *one* service interface at most.

As an example see the *Topicmaps* plugin (part of the DMX platform):

.. code-block:: text

    dmx-topicmaps/
        src/
            main/
                java/
                    systems/
                        dmx/
                            topicmaps/
                                TopicmapsService.java

The service interface of the *Topicmaps* plugin is named ``TopicmapsService``. The plugin package is ``systems.dmx.topicmaps``.

The *Topicmaps* service interface looks like this:

.. code-block:: java

    package systems.dmx.topicmaps.service;

    import systems.dmx.topicmaps.TopicmapRenderer;
    import systems.dmx.topicmaps.model.ClusterCoords;
    import systems.dmx.topicmaps.model.Topicmap;

    import systems.dmx.core.Topic;


    public interface TopicmapsService {

        Topic createTopicmap(String name,             String topicmapRendererUri);
        Topic createTopicmap(String name, String uri, String topicmapRendererUri);

        // ---

        Topicmap getTopicmap(long topicmapId);

        // ---

        void addTopicToTopicmap(long topicmapId, long topicId, int x, int y);

        void addAssociationToTopicmap(long topicmapId, long assocId);

        void moveTopic(long topicmapId, long topicId, int x, int y);

        void setTopicVisibility(long topicmapId, long topicId, boolean visibility);

        void removeAssociationFromTopicmap(long topicmapId, long assocId);

        void moveCluster(long topicmapId, ClusterCoords coords);

        void setTopicmapTranslation(long topicmapId, int trans_x, int trans_y);

        // ---

        void registerTopicmapRenderer(TopicmapRenderer renderer);
    }

You see the Topicmaps service consist of methods to create topicmaps, retrieve topicmaps, and manipulate topicmaps.

Implementing the service
------------------------

After defining the plugin's service interface you must implement the actual service methods. Implementation takes place in the plugin main class.

:ref:`the-plugin-main-class` must declare that it implements the plugin's service interface. (So you need to import the service interface.) Each service method implementation must be ``public``. Annotate each service method implementation with ``@Override``.

As an example see the implementation of the *Topicmaps* service:

.. code-block:: java

    package systems.dmx.topicmaps;

    import systems.dmx.topicmaps.model.Topicmap;
    import systems.dmx.topicmaps.TopicmapsService;

    import systems.dmx.core.Topic;
    import systems.dmx.core.osgi.PluginActivator;



    public class TopicmapsPlugin extends PluginActivator implements TopicmapsService {

        // *** TopicmapsService Implementation ***

        @Override
        public Topic createTopicmap(String name, String topicmapRendererUri) {
            ...
        }

        @Override
        public Topic createTopicmap(String name, String uri, String topicmapRendererUri) {
            ...
        }

        // ---

        @Override
        public Topicmap getTopicmap(long topicmapId) {
            ...
        }

        // ---

        @Override
        public void addTopicToTopicmap(long topicmapId, long topicId, int x, int y) {
            ...
        }

        ...

You see, the plugin main class ``TopicmapsPlugin`` implements the plugin's service interface ``TopicmapsService``.

Consuming a service
===================

Your plugin can consume the services provided by other plugins. To do so your plugin must get hold of the *service object* of the other plugin. Through the service object your plugin can call all the service methods declared in the other's plugin service interface.

To tell the DMX Core which plugin service your plugin wants to consume you need to declare an instance variable in your plugin like using the @Inject notation:

.. code-block:: java

    @Inject
    private AccessControlService acService;

Make sure to add your interest in building on the respective plugin service as dependencies to your ``pom.xml`` file. In the case of using the AccessControlService we would need to add the following:

.. code-block:: xml

    <dependencies>
        <dependency>
            <groupId>systems.dmx</groupId>
            <artifactId>dmx-accesscontrol</artifactId>
            <version>5.0-SNAPSHOT</version>
        </dependency>
    </dependencies>

Behind the scenes the DMX Core handles a plugin service as an OSGi service. Because of the dynamic nature of an OSGi environment DMX plugin services can arrive and go away at any time. Your plugin must deal with that. However, you as a plugin developer must not care about DMX's OSGi foundation. The DMX Core hides the details from you and provides an easy-to-use API for consuming plugin services.

To deal with other plugin services coming and going your plugin can override 2 hooks: ``serviceArrived`` and ``serviceGone``. These 2 hooks are called by the DMX Core as soon as a desired plugin becomes available resp. goes away.

The single argument of the 2 ``serviceArrived`` and ``serviceGone`` hooks is the respective service object, declared generically just as ``PluginService``. (Remember, ``PluginService`` is the common base interface for all plugin services.) So casting is required. In ``serviceArrived`` you typically store the service object in a private instance variable. In ``serviceGone`` you typically set the instance variable to ``null`` in order to release the service object.

As an example, see how the *Workspaces* plugin (part of the DMX platform) consumes the *Facets* service:

.. code-block:: java

    package systems.dmx.workspaces;

    import systems.dmx.facets.FacetsService;

    import systems.dmx.core.osgi.PluginActivator;
    import systems.dmx.core.service.PluginService;
    import systems.dmx.core.service.annotation.ConsumesService;



    public class WorkspacesPlugin extends PluginActivator {

        @Inject
        private FacetsService facetsService;

        // *** Hook Implementations ***

        @Override
        public void serviceArrived(PluginService service) {
            if (service instanceof FacetsService) {
                // do something when the facet service comes around
            }
        }

        @Override
        public void serviceGone(PluginService service) {
            // do something when a service goes away
        }

You see the Workspaces plugin consumes a plugin service: the *Facets* service.  The ``PluginService`` object passed to the 2 hooks needs not being further investigated.

In this way your plugin could also consume more than one service.

Providing a RESTful web service
===============================

Until here your plugin service is accessible from within the OSGi environment only. You can make the service accessible from *outside* the OSGi environment as well by promoting it to a RESTful web service. Your plugin service is then accessible from external applications via HTTP. (External application here means both, the client-side portion of a DMX plugin, or an arbitrary 3rd-party application).

To provide a RESTful web service you must provide a generic plugin service first (as described above in [[#Providingaservice|Providing a service]]) and then make it RESTful by using JAX-RS annotations. With JAX-RS annotations you basically control how HTTP requests will be mapped to your service methods.

To make your plugin service RESTful you must:

* Annotate the plugin main class with ``@Path`` to anchor the plugin service in URI space.

* Annotate the plugin main class with ``@Consumes`` and ``@Produces`` to declare the supported HTTP request and response media types. You can use these annotations also at a particular service method to override the class-level defaults.

* Annotate each service method with one of ``@GET``, ``@POST``, ``@PUT``, or ``@DELETE`` to declare the HTTP method that will invoke that service method.

* Annotate each service method with ``@Path`` to declare the URI template that will invoke that service method. The URI template can contain parameters, notated with curly braces ``{...}``.

* Annotate service method parameters with ``@PathParam`` to map URI template parameters to service method parameters.

As an example let's see how the *Topicmaps* plugin (part of the DMX platform) annotates its main class and service methods:

.. code-block:: java

    package systems.dmx.topicmaps;

    import systems.dmx.topicmaps.model.Topicmap;
    import systems.dmx.topicmaps.TopicmapsService;

    import systems.dmx.core.Topic;
    import systems.dmx.core.osgi.PluginActivator;

    import javax.ws.rs.GET;
    import javax.ws.rs.PUT;
    import javax.ws.rs.POST;
    import javax.ws.rs.DELETE;
    import javax.ws.rs.HeaderParam;
    import javax.ws.rs.Path;
    import javax.ws.rs.PathParam;
    import javax.ws.rs.Produces;
    import javax.ws.rs.Consumes;



    @Path("/topicmap")
    @Consumes("application/json")
    @Produces("application/json")
    public class TopicmapsPlugin extends PluginActivator implements TopicmapsService {

        // *** TopicmapsService Implementation ***

        @POST
        @Path("/{name}/{topicmap_renderer_uri}")
        @Override
        public Topic createTopicmap(@PathParam("name") String name,
                                    @PathParam("topicmap_renderer_uri") String topicmapRendererUri) {
            ...
        }

        @GET
        @Path("/{id}")
        @Override
        public Topicmap getTopicmap(@PathParam("id") long topicmapId) {
            ...
        }

        @POST
        @Path("/{id}/topic/{topic_id}/{x}/{y}")
        @Override
        public void addTopicToTopicmap(@PathParam("id") long topicmapId, @PathParam("topic_id") long topicId,
                                       @PathParam("x") int x, @PathParam("y") int y) {
            ...
        }

        ...

JAX-RS: Java API for RESTful Web Services[[BR]]
http://jsr311.java.net/nonav/releases/1.1/spec/spec.html

Extract values from a HTTP request
----------------------------------

This section describes in more detail how DMX (resp. the underlying JAX-RS implementation to be precise) extracts the service method argument values from the various parts of a HTTP request. As seen in the example above this is controlled by annotating the service method arguments. Besides ``@PathParam`` you can use further annotations:

================  ==============================================
Annotation        Semantics
================  ==============================================
``@PathParam``    Extracts the value of a URI template parameter
``@QueryParam``   Extracts the value of a URI query parameter
``@HeaderParam``  Extracts the value of a header
================  ==============================================

A value extracted from a HTTP request is inherently a string. So the JAX-RS implementation must know how to actually construct a Java object (resp. a primitive value) from it. That's why the type of a service method argument that is annotated with one of these annotations must satisfy one of these criteria:

1. The type is a primitive type like ``int``, ``long``, ``float``, ``double``, ``boolean``, ``char``.

2. The type has a constructor that accepts a single ``String`` argument.

3. The type has a static method named ``valueOf`` that takes a single ``String`` argument and returns an instance of the type.

    Enum types are special as they already have a static ``valueOf`` method. If this one does not fit your need add a ``fromString`` method to your enum type that has the same characteristics as the ``valueOf`` method mentioned above.

4. The type is ``List<T>``, ``Set<T>``, or ``SortedSet<T>``, where ``T`` satisfies criterion 2 or 3.

So, when you use a self-defined class (including enum classes) along with ``@PathParam``, ``@QueryParam``, or ``@HeaderParam`` make sure your class satisfies criterion 2 or 3.

As an example lets revisit the ``getTopicmap`` method from the previous section:

.. code-block:: java

    @GET
    @Path("/{id}")
    @Override
    public Topicmap getTopicmap(@PathParam("id") long topicmapId) {
        ...
    }

Now you know how exactly the JAX-RS implementation extracts the ``topicmapId`` parameter value from the HTTP request:

    The ``topicmapId`` value is extracted from the request's URI path and then converted to a ``long``. Here criterion 1 is satisfied and the conversion is straight-forward.

Parsing the HTTP request body
-----------------------------

Until here we talked about how to extract values from the HTTP request's path, the request's query string, or the request headers. This section describes how to feed the *HTTP request body* into your service methods. Feeding here refers to a) parsing the body's byte stream, b) constructing a Java object from it, and passing that Java object to a particular service method.

JAX-RS can't know how to construct arbitrary application objects from a sole byte stream. That's why JAX-RS comprises a extension facility called *Provider Classes*. A provider class is responsible to read the request body, parse it, and construct an particular application object from it. It is the duty of the application developer to implement the required provider classes for the application objects.

A service method that want to receive the constructed application object must have a dedicated parameter called (in JAX-RS speak) the *Entity Parameter*. The entity parameter stands for the entity that is represented in the request body. Unlike the other service method parameters the entity parameter has *no* annotation. A service method can have *one* entity parameter at most (a HTTP request has *one* body).

To feed the HTTP request body into a service method you must:

* Add an entity parameter to the service method. That is a parameter without any annotation.

* Implement a provider class for the type of the entity parameter, resp. make sure such a provider class already exists (as part of the DMX Core or one of the installed DMX plugins).

Using the platform services
===========================

TODO

Topicmaps Service
-----------------

TODO

Workspaces Service
------------------

TODO

Access Control Service
----------------------

TODO

Facets Service
--------------

TODO

Files Service
-------------

TODO

Config Service
--------------

TODO

Transactions
============

TODO

Client-Sync
===========

TODO

***************************
Extending the DMX Webclient
***************************

The plugin.js file
==================

TODO

Mounting Vue components
=======================

TODO

Providing detail renderers
==========================

TODO

Object renderers
----------------

TODO

Value renderers
---------------

TODO

Providing icon renderers
========================

TODO

Adding context menu commands
============================

TODO

Adding create menu items
========================

TODO

Providing a Vuex store module
=============================

TODO

Providing store watchers
========================

TODO

Implementing a Topicmap Type
============================

TODO

Demanding extra Element UI components
=====================================

TODO

*********
Reference
*********

TODO
