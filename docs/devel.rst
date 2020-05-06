###################
DMX Developer Guide
###################

This guide describes how to develop plugins for the DMX platform.

A DMX plugin can contain both, a backend part, and/or a frontend part. The frontend part can either extend the DMX Webclient, or can be a proprietary web frontend (possibly exposing its own extension mechanism).

To see how a DMX plugin fits in the overall DMX architecture go to [[ArchitectureOverview|Architecture Overview]].

At the backend you'll work with Java; JAX-RS knowledge is useful. At the frontend you'll work with `Vue <https://vuejs.org>`_, `Vuex <https://vuex.vuejs.org>`_; `Element UI <https://element.eleme.io>`_ knowledge is useful.

*********************
Build DMX from source
*********************

The best way to develop DMX plugins is to build the DMX platform from source first. This gives you a development environment which features hot-deployment: the DMX platform automatically redeploys your plugin once you make changes. This is very handy while development.

Requirements:

* **Java 8** (other versions do *not* work)
* **Maven**
* **Node.js**
* **Git**

Build DMX from source:

.. code-block:: bash

    $ git clone https://git.dmx.systems/dmx-platform/dmx-platform.git
    $ cd dmx-platform
    $ mvn install -P all

This builds all components of the DMX platform and installs them in your local Maven repository. Tests are run; you'll see a lot of information logged, cumulating in:

.. code-block:: text

    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:07 min
    ...

****************************
The plugin turn-around cycle
****************************

This section illustrates how to begin a plugin project, how to build and how to deploy a plugin, and how to redeploy the plugin once you made changes in its source code. In other words, this section illustrates the plugin development turn-around cycle.

Let's start with a very simple plugin called *DMX Tagging*. This plugin will just create a new topic type called ``Tag``. Once the plugin is activated the topic type will appear in the DMX Webclient's *Create* menu, so you can create tag topics and associate them with arbitrary topics. And you will be able to fulltext search for tags.

Developing a plugin whose only purpose is to provide new topic type definitions requires no Java or JavaScript coding. All is declarative, mainly in JSON format.

Of course the topic type could be created interactively as well, by using the DMX Webclient's type editor. However, being packaged as a plugin means you can distribute it. When other DMX users install your plugin they can use your type definitions.

Begin a plugin project
======================

Naming Conventions
------------------

.. hint::

    It is convention to have prefix ``dmx-`` when creating a Git repo for your DMX plugin, eg. ``dmx-tagging``.

From the developer's view a DMX plugin is a directory on your hard disc. The directory can have an arbitrary name and exist at an arbitrary location. By convention the plugin directory begins with ``dmx-`` as it is aimed to the DMX platform. The directory content adheres to a certain directory structure and file name conventions. The files are text files (xml, json, properties, java, js, css) and resources like images.

To create the *DMX Tagging* plugin setup a directory structure as follows:

.. code-block:: text

    dmx-tagging/
        pom.xml
        src/
            main/
                resources/
                    migrations/
                        migration1.json
                    plugin.properties

Create the file ``pom.xml`` with this content:

.. code-block:: xml

    <project>
        <modelVersion>4.0.0</modelVersion>

        <name>DMX Tagging</name>
        <groupId>org.mydomain</groupId>
        <artifactId>dmx-tagging</artifactId>
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
                            <Bundle-SymbolicName>
                                org.mydomain.dmx-tagging
                            </Bundle-SymbolicName>
                        </instructions>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>

Create the file ``migration1.json``:

.. code-block:: js

    {
        topic_types: [
            {
                value: "Tag",
                uri: "domain.tagging.tag",
                dataTypeUri: "dmx.core.text",
                viewConfigTopics: [
                    {
                        typeUri: "dmx.webclient.view_config",
                        children: {
                            dmx.webclient.add_to_create_menu: true
                        }
                    }
                ]
            }
        ]
    }

Create the file ``plugin.properties``:

.. code-block:: text

    dmx.plugin.model_version = 1
    dmx.plugin.dependencies = systems.dmx.webclient

Setup for Hot-Deployment
========================

The easiest way to let DMX hot-deploy the plugin is to develop it within the ``bundle-dev/`` directory. To do so move the plugin directory on your hard disc into DMX's hot-deployment folder called ``bundle-dev/``. The next step is then to build your plugin.

But lets first start DMX in development mode, that is with hot-deployment activated.

In the home directory ``dmx-platform``:

.. code-block:: bash

    $ mvn pax:run

You'll see a lot of information logged, cumulating with:

.. code-block:: text

    ...
    Apr 6, 2013 11:21:20 PM de.deepamehta.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 32, DeepaMehta plugins: 16, Activated: 16
    Apr 6, 2013 11:21:20 PM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ########## All Plugins Activated ##########
    Apr 6, 2013 11:21:20 PM de.deepamehta.plugins.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching webclient (url="http://localhost:8080/de.deepamehta.webclient/")
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
        0|Active     |    0|System Bundle (3.2.1)
       ...
       14|Active     |    5|DeepaMehta 4 Help (4.1.1.SNAPSHOT)
       15|Active     |    5|DeepaMehta 4 Topicmaps (4.1.1.SNAPSHOT)
       16|Active     |    5|DeepaMehta 4 Webservice (4.1.1.SNAPSHOT)
       17|Active     |    5|DeepaMehta 4 Files (4.1.1.SNAPSHOT)
       18|Active     |    5|DeepaMehta 4 Geomaps (4.1.1.SNAPSHOT)
       19|Active     |    5|DeepaMehta 4 Storage - Neo4j (4.1.1.SNAPSHOT)
       20|Active     |    5|DeepaMehta 4 Core (4.1.1.SNAPSHOT)
       21|Active     |    5|DeepaMehta 4 Access Control (4.1.1.SNAPSHOT)
       22|Active     |    5|DeepaMehta 4 Webclient (4.1.1.SNAPSHOT)
       23|Active     |    5|DeepaMehta 4 Webbrowser (4.1.1.SNAPSHOT)
       24|Active     |    5|DeepaMehta 4 Type Search (4.1.1.SNAPSHOT)
       25|Active     |    5|DeepaMehta 4 Workspaces (4.1.1.SNAPSHOT)
       26|Active     |    5|DeepaMehta 4 Notes (4.1.1.SNAPSHOT)
       27|Active     |    5|DeepaMehta 4 Type Editor (4.1.1.SNAPSHOT)
       28|Active     |    5|DeepaMehta 4 Contacts (4.1.1.SNAPSHOT)
       29|Active     |    5|DeepaMehta 4 Facets (4.1.1.SNAPSHOT)
       30|Active     |    5|DeepaMehta 4 File Manager (4.1.1.SNAPSHOT)
       31|Active     |    5|DeepaMehta 4 Icon Picker (4.1.1.SNAPSHOT)

The *DMX Tagging* plugin does not yet appear in that list as it is not yet build.

Build the plugin
================

In another terminal:

.. code-block:: bash

    $ cd dmx-tagging
    $ mvn clean package

This builds the plugin. After some seconds you'll see:

.. code-block:: text

    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 3.988s
    ...

Once build, DMX hot-deploys the plugin automatically. In the terminal where you've started DMX the logging informs you about plugin activation:

.. code-block:: text

    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl readConfigFile
    INFO: Reading config file "/plugin.properties" for plugin "DeepaMehta 4 Tagging"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.osgi.PluginActivator start
    INFO: ========== Starting plugin "DeepaMehta 4 Tagging" ==========
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl createPluginServiceTrackers
    INFO: Tracking plugin services for plugin "DeepaMehta 4 Tagging" ABORTED -- no consumed services declared
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl addService
    INFO: Adding DeepaMehta 4 core service to plugin "DeepaMehta 4 Tagging"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl addService
    INFO: Adding Web Publishing service to plugin "DeepaMehta 4 Tagging"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl registerWebResources
    INFO: Registering Web resources of plugin "DeepaMehta 4 Tagging" ABORTED -- no Web resources provided
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl registerRestResources
    INFO: Registering REST resources of plugin "DeepaMehta 4 Tagging" ABORTED -- no REST resources provided
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl registerRestResources
    INFO: Registering provider classes of plugin "DeepaMehta 4 Tagging" ABORTED -- no provider classes provided
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl addService
    INFO: Adding Event Admin service to plugin "DeepaMehta 4 Tagging"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ----- Activating plugin "DeepaMehta 4 Tagging" -----
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl createPluginTopicIfNotExists
    INFO: Installing plugin "DeepaMehta 4 Tagging" in the database
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.MigrationManager runPluginMigrations
    INFO: Running 1 migrations for plugin "DeepaMehta 4 Tagging" (migrationNr=0, requiredMigrationNr=1)
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.MigrationManager$MigrationInfo readMigrationConfigFile
    INFO: Reading migration config file "/migrations/migration1.properties" ABORTED -- file does not exist
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.MigrationManager runMigration
    INFO: Running migration 1 of plugin "DeepaMehta 4 Tagging" (runMode=ALWAYS, isCleanInstall=true)
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.util.DeepaMehtaUtils readMigrationFile
    INFO: Reading migration file "/migrations/migration1.json"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.MigrationManager runMigration
    INFO: Completing migration 1 of plugin "DeepaMehta 4 Tagging"
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.MigrationManager runMigration
    INFO: Updating migration number (1)
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl registerListeners
    INFO: Registering listeners of plugin "DeepaMehta 4 Tagging" at DeepaMehta 4 core service ABORTED -- no listeners implemented
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginImpl registerPluginService
    INFO: Registering OSGi service of plugin "DeepaMehta 4 Tagging" ABORTED -- no OSGi service provided
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ----- Activation of plugin "DeepaMehta 4 Tagging" complete -----
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 33, DeepaMehta plugins: 17, Activated: 17
    Apr 6, 2013 11:38:40 PM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ########## All Plugins Activated ##########
    Apr 6, 2013 11:38:40 PM de.deepamehta.plugins.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching webclient (url="http://localhost:8080/de.deepamehta.webclient/") ABORTED -- already launched
    ...

When you type again ``lb`` in the DMX terminal you'll see the *DMX Tagging* plugin now appears in the list of activated bundles:

.. code-block:: text

    START LEVEL 6
       ID|State      |Level|Name
        0|Active     |    0|System Bundle (3.2.1)
       ...
       30|Active     |    5|DeepaMehta 4 File Manager (4.1.1.SNAPSHOT)
       31|Active     |    5|DeepaMehta 4 Icon Picker (4.1.1.SNAPSHOT)
       32|Active     |    5|DeepaMehta 4 Tagging (0.1.0.SNAPSHOT)

Try out the plugin
==================

Now you can try out the plugin. In the DMX Webclient login as user "admin" and leave the password field empty. The *Create* menu appears and when you open it you'll see the new type *Tag* listed. Thus, you can create tags now. Additionally you can associate tags to your content topics, search for tags, and navigate along the tag associations, just as you do with other topics.

The result so far: the *DMX Tagging* plugin provides a new topic type definition or, in other words: a data model. All the active operations on the other hand like create, edit, search, delete, associate, and navigate are provided by the DMX Webclient at a generic level, and are applicable to your new topic type as well.

Redeploy the plugin
===================

Once you've made any changes to the plugin files, you have to build the plugin again. Just like before in the plugin terminal:

.. code-block:: bash

    $ mvn clean package

Once building is complete the changed plugin is redeployed automatically. You'll notice activity in the DMX terminal:

.. code-block:: text

    Apr 8, 2013 1:10:40 AM de.deepamehta.core.osgi.PluginActivator stop
    INFO: ========== Stopping plugin "DeepaMehta 4 Tagging" ==========
    Apr 8, 2013 1:10:40 AM de.deepamehta.core.impl.PluginImpl removeService
    INFO: Removing DeepaMehta 4 core service from plugin "DeepaMehta 4 Tagging"
    Apr 8, 2013 1:10:40 AM de.deepamehta.core.impl.PluginImpl removeService
    INFO: Removing Web Publishing service from plugin "DeepaMehta 4 Tagging"
    Apr 8, 2013 1:10:40 AM de.deepamehta.core.impl.PluginImpl removeService
    INFO: Removing Event Admin service from plugin "DeepaMehta 4 Tagging"
    ...
    ...
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.osgi.PluginActivator start
    INFO: ========== Starting plugin "DeepaMehta 4 Tagging" ==========
    ...
    ...
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ----- Activating plugin "DeepaMehta 4 Tagging" -----
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.PluginImpl createPluginTopicIfNotExists
    INFO: Installing plugin "DeepaMehta 4 Tagging" in the database ABORTED -- already installed
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.MigrationManager runPluginMigrations
    INFO: Running migrations for plugin "DeepaMehta 4 Tagging" ABORTED -- everything up-to-date (migrationNr=1)
    ...
    ...
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ----- Activation of plugin "DeepaMehta 4 Tagging" complete -----
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 33, DeepaMehta plugins: 17, Activated: 17
    Apr 8, 2013 1:10:44 AM de.deepamehta.core.impl.PluginManager activatePlugin
    INFO: ########## All Plugins Activated ##########
    Apr 8, 2013 1:10:44 AM de.deepamehta.plugins.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching webclient (url="http://localhost:8080/de.deepamehta.webclient/") ABORTED -- already launched
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

* An **Imperative Migration** is a Java class that has access to the *DMX Core Service*. Thus, you can perform arbitrary database operations like creation, retrieval, update, deletion. Use an imperative migration when (a later version of) your plugin needs to modify existing type definitions and/or transform existing database content.

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
                    org/
                        mydomain/
                            dmx/
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

Writing an imperative migration
===============================

An imperative migration is a Java class that is derived from ``systems.dmx.core.service.Migration`` and that overrides the ``run()`` method. The ``run()`` method is called by DMX to run the migration.

Within the migration you have access to the DMX *Core Service* through the ``dmx`` object. By the means of the Core Service you can perform arbitrary database operations. Typically this involves importing further objects from the ``systems.dmx.core`` API.

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

Here an association definition is added to the *Topicmap* type subsequently.

***************
The server side
***************

What a DMX plugin can do at the server side:

* **Listen to DMX Core events**. In particular situations the DMX Core fires events, e.g. before and after it creates a new topic in the database. Your plugin can listen to these events and react in its own way. Thus, the *DMX Workspaces* plugin e.g. ensures that each new topic is assigned to a workspace.

* **Providing a service**. Your plugin can make its business logic, that is its service methods, accessible by other plugins (via OSGi) and/or by external applications (via HTTP/REST). Example: the service provided by the *DMX Topicmaps* plugin includes methods to add a topic to a topicmap or to change the topic's coordinates within a topicmap.

* **Consuming services provided by other plugins**. Example: in order to investigate a topic's workspace assignments and the current user's memberships the *DMX Access Control* plugin consumes the service provided by the *DMX Workspaces* plugin.

* **Access the DMX Core Service**. The DMX *Core Service* provides the basic database operations (create, retrieve, update, delete) to deal with the DMX Core objects: Topics, Associations, Topic Types, Association Types.

Weather a DMX plugin has a server side part at all depends on the nature of the plugin. Plugins without a server side part include those which e.g. just define a data model or just provide a custom (JavaScript) renderer.

The plugin main file
====================

You must write a *plugin main file* if your plugin needs to a) listen to DMX Core events and/or b) provide a service. The plugin main file contains the event handlers resp. the service implementation then.

The plugin main file must be located directly in the plugin's ``src/main/java/<your plugin package>/`` directory. By convention the plugin main class ends with ``Plugin``.

Example:

.. code-block:: text

    dmx-mycoolplugin/
        src/
            main/
                java/
                    org/
                        mydomain/
                            dmx/
                                mycoolplugin/
                                    MyCoolPlugin.java

Here the plugin package is ``org.mydomain.dmx.mycoolplugin`` and the plugin main class is ``MyCoolPlugin``.

A plugin main file is a Java class that is derived from ``systems.dmx.core.osgi.PluginActivator``. The smallest possible plugin main file looks like this:

.. code-block:: java

    package org.mydomain.dmx.mycoolplugin;

    import systems.dmx.core.osgi.PluginActivator;

    public class MyCoolPlugin extends PluginActivator {
    }

3 things are illustrated here:

* The plugin should be packaged in an unique namespace.
* The ``PluginActivator`` class needs to be imported.
* The plugin main class must be derived from ``PluginActivator`` and must be public.

Furthermore when writing a plugin main file you must add 2 entries in the plugin's ``pom.xml``:

1. a <parent> element to declare the artifactId ``dmx-plugin``. This brings you necessary dependenies and the ``PluginActivator`` class.
2. a <build> element to configure the Maven Bundle Plugin. It needs to know what your plugin main class is. You must specify the fully-qualified class name.

.. code-block:: xml

    <project>
        <modelVersion>4.0.0</modelVersion>

        <name>My Cool Plugin</name>
        <groupId>org.mydomain</groupId>
        <artifactId>dmx-mycoolplugin</artifactId>
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
                            <Bundle-SymbolicName>
                                org.mydomain.dmx-mycoolplugin
                            </Bundle-SymbolicName>
                            <Bundle-Activator>
                                org.mydomain.dmx.mycoolplugin.MyCoolPlugin
                            </Bundle-Activator>
                        </instructions>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>

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

    package org.mydomain.dmx.mycoolplugin;

    import systems.dmx.core.Topic;
    import systems.dmx.core.model.TopicModel;
    import systems.dmx.core.osgi.PluginActivator;
    import systems.dmx.core.service.Directives;
    import systems.dmx.core.service.event.PostCreateTopic;
    import systems.dmx.core.service.event.PostUpdateTopic;

    import java.util.logging.Logger;



    public class MyCoolPlugin extends PluginActivator implements PostCreateTopic, PostUpdateTopic {

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

To be recogbized the service interface *must* by convention end its name on ``...Service``. The service interface must be declared ``public`` and is a regular Java interface.

A DMX plugin can define *one* service interface at most. More than one service interface is not supported.

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

After defining the plugin's service interface you must implement the actual service methods. Implementation takes place in the plugin main file.

The plugin main class must declare that it implements the plugin's service interface. (So you need to import the service interface.) Each service method implementation must be ``public``. Annotate each service method implementation with ``@Override``.

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
