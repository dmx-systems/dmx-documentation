.. _admin:

#######################
DMX Admin Documentation
#######################

*****************
The Settings File
*****************

The DMX settings file is located in ``conf/config.properties``.
The settings are basically explained inline in the comments, too.

The Webserver
=============

Here you configure the ports DMX will be listening on.

.. code:: bash

   org.osgi.service.http.port = 8080
   org.apache.felix.http.enable = true

   # Session timeout in minutes. Set 0 for no timeout.
   org.apache.felix.http.session.timeout = 0
   
   # HTTPS
   org.osgi.service.http.port.secure = 8443
   
   # To enable HTTPS set this to "true"
   org.apache.felix.https.enable = false
   
   # The **Keystore Password** (as entered in the `keytool`)
   org.apache.felix.https.keystore.password = 
   
   # The **Key Password** (as entered in the `keytool`)
   org.apache.felix.https.keystore.key.password = 
   
   # WebSockets
   
   # The port the WebSocket server opens for accepting connections
   dmx.websockets.port = 8081
   
   # The URL the WebSocket clients use to connect to the WebSocket server
   dmx.websockets.url = ws://localhost:8081
   
.. _admin-request-filters:
   
Request Filters
===============

In this section you can control if anonymous users, that is people who are not logged in, can read and/or write content.

.. warning:: By default, anyone can read but not write content! Consider to change this!

======================================  ============================================
Available options                       Meaning
======================================  ============================================
``ALL``                                 allow **all** anonymous requests
``NONE``                                allow **no** anonymous requests
comma-separated list of URI-prefixes    allow **specific** anonymous requests,
                                        e.g. "/eu.crowd-literature, /crowd".
======================================  ============================================

Here is how you configure the third option, specific anonymous requests:
Each prefix must begin with slash but have *no* slash at the end, e.g. ``/eu.crowd-literature, /crowd``.
(To each prefix a slash is appended automatically.)
Spaces, tabs, and line breaks are allowed.
Line breaks must be escaped with a backslash (\\).

.. code:: bash

   dmx.security.anonymous_read_allowed = ALL
   dmx.security.anonymous_write_allowed = NONE


If you want you instance to be accessible from the internet you might need to change this setting depending on your web server configuration and IP address.
In a default installation, requests are only allowed from localhost.
Thus, if you do not change this setting and you run DMX on your laptop without any other web server your instance is not accessible from outside our computer.

.. code:: bash

   # Requests are allowed from this subnet only.
   # An address range denoted by network number and netmask.
   # To allow local access only set "127.0.0.1/32". This is the default.
   # To allow global access set "0.0.0.0/0".
   dmx.security.subnet_filter = 127.0.0.1/32

User Accounts and Admin Password
================================

In the section about user accounts you can set the initial password for the admin account.

.. warning:: By default, the admin password is empty! Please do not forget to change it if you are planning to give others access to your instance.
   
.. code:: bash

   # The initial password for the "admin" user account.
   # Note: this setting is only evaluated while the first DMX start.
   # Once started the admin password can be changed interactively (using the web client).
   dmx.security.initial_admin_password = 

You can control whether you want to enable new user accounts right away or not.

.. code:: bash

   # If true newly created user accounts are enabled instantly. This is the default.
   # If false user accounts must be manually enabled by an administrator (using the web client).
   dmx.security.new_accounts_are_enabled = true


DM5 does currently not support an LDAP backend, yet.
You can thus ignore the section in the settings file.
In future releases you will be able to use it.

.. code:: bash

   # LDAP
   dmx.ldap.server = 127.0.0.1
   dmx.ldap.port = 389
   dmx.ldap.manager = 
   dmx.ldap.password = 
   dmx.ldap.user_base = 
   dmx.ldap.user_attribute = 
   dmx.ldap.filter = 

.. _admin-workspace-sharing-modes:

Workspace Sharing Modes
=======================
   
When a user creates a workspace only the enabled :ref:`sharing modes<user-introduction-to-workspaces-and-sharing-modes>` will be available.
By default all sharing modes are enabled.
Note: individual (per-user) sharing modes can be configured by an administrator (using the web client).

.. code:: bash

   dmx.workspaces.private.enabled = true
   dmx.workspaces.confidential.enabled = true
   dmx.workspaces.collaborative.enabled = true
   dmx.workspaces.public.enabled = true
   dmx.workspaces.common.enabled = true


The File Repository
===================

The file repository's base path:
an absolute path leading to a directory, *no* slash at the end.
The directory must exist.
To enable the entire file system set "/".
This is the default.

.. code:: bash

   dmx.filerepo.path = /

If you want to put the file repository under DMX's access control, toggle the filerepo per workspace switch to ``true``.
DMX then creates a separate file repository for each workspace beneath the base path.
This allows for e.g. private and collaborative file repositories.
If set to "false" (the default) there is one big public file repository shared by all users.
Use this also for a single-user installation.

.. code:: bash

   dmx.filerepo.per_workspace = false


With the next option you control users' standard disk quota for file uploads (in MB).
Set ``-1`` for no disk quota (unlimited uploads). This is the default.
Individual (per-user) disk quotas can be configured by an administrator (using the web client).

.. code:: bash

   dmx.filerepo.disk_quota = -1


Hot Deployment
==============
   
DMX plugins (jar files) from this directory are hot deployed (like any other OSGi bundle).
You can add further directories here, separated by comma.
You can also remove the standard directory ("bundle-deploy") if you like.
Spaces, tabs, and line breaks are allowed.
Line breaks must be escaped with backslash (\\).

.. code:: bash

   felix.fileinstall.dir = bundle-deploy


Host
====
   
The URL (protocol, host, port) this DMX installation is accessible on from "outside".
Some DMX plugins make use of this value when creating public DMX URLs.
This setting is of particular interest when DMX runs behind a reverse proxy.

.. code:: bash

   dmx.host.url = http://localhost:8080/
   # running behind a reverse proxy:
   dmx.host.url = https://example.org/

Webservice
==========

DMX supports only two values here:

=================  ===============================
Available options  Meaning
=================  ===============================
``/``              use Apache Felix HTTP (default)
``/*``             use OPS4J Pax Web
=================  ===============================

.. code:: bash

   dmx.webservice.path = /


The path to the DMX Database
============================

Your DMX database will be created in a directory that you can configure.
In the self-contained default distribution, it is created in a folder called ``dmx-db`` in the working directory.

.. code:: bash

   dmx.database.path = dmx-db

Logging
=======

DMX comes with a separate configuration file for logging.
It is called ``logging.properties`` and you can find it in the same ``conf`` folder as the configuration file.
You can adjust the path to your needs with the following setting:

.. code:: bash

   java.util.logging.config.file = conf/logging.properties

You can also adapt the loglevel of the Apache Felix framework that comes bundled with DMX.

.. code:: bash

   # To see any Felix/File Install logging set the level to at least 4
   felix.log.level = 0
   felix.fileinstall.log.level = 0


Encoding
========

DMX's default file encoding is UTF-8.
You can change it to your needs here:

.. code:: bash

   file.encoding = UTF-8


OSGi Runtime
============
   
.. code:: bash

   org.osgi.framework.storage = bundle-cache
   felix.auto.deploy.action = install,start


******************************************
Running DMX behind an Apache Reverse Proxy
******************************************

Enable a few Apache modules before you start:

.. code:: bash

   a2enmod ssl
   a2enmod rewrite
   a2enmod proxy
   a2enmod proxy_http
   a2enmod proxy_wstunnel

This is an example configuration for Apache 2.4.
The web server handles SSL.

.. note:: Add a RewriteRule to ``https://%{HTTP_HOST}/systems.dmx.webclient/`` as described below.

.. code:: bash

   <VirtualHost *:80>
       ServerName dmx.example.org
       # This docroot is not used by DMX but for the Letsencrypt webroot challenge:
       DocumentRoot /var/www/dmx.example.org

       # Rewrite everything to https except for the URI required by Letsencrypt on port 80:
       RewriteEngine On
       RewriteCond %{HTTPS} off
       RewriteCond %{REQUEST_URI} !^/\.well-known/acme\-challenge/
       RewriteRule .* https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

       LogLevel error
       ErrorLog /var/log/apache2/dmx.example.org_error.log
       CustomLog /var/log/apache2/dmx.example.org_access.log combined
   </VirtualHost>

   <VirtualHost *:443>
       ServerName dmx.example.org
       DefaultType text/html
       SSLEngine On
       SSLCertificateFile /etc/letsencrypt/live/dmx.example.org/cert.pem
       SSLCertificateKeyFile /etc/letsencrypt/live/dmx.example.org/privkey.pem
       SSLCertificateChainFile /etc/letsencrypt/live/dmx.example.org/chain.pem

       ErrorLog /var/log/apache2/dmx.example.org-ssl-error.log
       CustomLog /var/log/apache2/dmx.example.org-ssl-access.log combined

       ProxyStatus On
       ProxyPreserveHost Off
       AllowEncodedSlashes NoDecode

       <Proxy *>
           Order deny,allow
           Allow from all
       </Proxy>

       # This is the forwarding for the websockets. Always keep it the first rule.
       # Do not forget to enable module proxy_wstunnel

       RewriteEngine On
       RewriteCond %{HTTP:Upgrade} =websocket
       # the internal IP address
       RewriteRule /(.*)           ws://127.0.0.1:8081/$1 [NE,P,L]
       
       # This is the default rewrite for the webclient
       RewriteRule ^/$ https://%{HTTP_HOST}/systems.dmx.webclient/ [R,L]
   
       <Location />
           ProxyPass http://127.0.0.1:8080/ nocanon
           ProxyPassReverse http://127.0.0.1:8080/
       </Location>
   </VirtualHost>

Your ``conf/config.properties`` file would then look like this:

.. code:: bash

   # the port you are proxying traffic to:
   org.osgi.service.http.port = 8080
   org.apache.felix.http.enable = true
   # HTTPS is handled by Apache2 beforehand:
   org.apache.felix.https.enable = false
   dmx.websockets.port = 8081
   # the external websocket url must be 'wss' for ssl encrypted connections
   dmx.websockets.url = wss://dmx.example.org
   # the IP address your internal traffic comes from via Apache2:
   dmx.security.subnet_filter = 127.0.0.1/32
   dmx.host.url = https://dmx.example.org/

************
Securing DMX
************

Drop incoming requests on port 8080
===================================

On a server installation port 8080 should only be reachable from localhost.
Drop all incoming requests on port 8080 like so:

.. code:: bash

    iptables -A INPUT ! -s 127.0.0.1 -p tcp --destination-port 8080 -j REJECT

.. _admin-plugin-installation:

*******
Plugins
*******

DMX is extensible through plugins some of which are available for download.
For a list of all our plugins please see the :ref:`Overview of Plugins <plugins-overview-of-plugins>` section in the Introduction.
You can find plugin releases in the `plugin directory of our download server <https://download.dmx.systems/plugins/>`_.

Plugin compatibility
====================

Some plugins are only compatible with DM5 or with its predecessor DM4.
You can tell if a plugin suits your version by looking at its file name:

===========  =======================  ========================
Plugin name  DM version               Example
===========  =======================  ========================
``dmx-*``    DM5 a.k.a. DMX           dmx-ldap-0.4.0.jar
``dm4*-*``   DM4.x a.k.a. DeepaMehta  dm49-thymeleaf-0.6.2.jar
===========  =======================  ========================

Plugin Installation
===================

You install a plugin by dropping the according ``.jar`` file into the ``bundle-deploy`` folder of a DMX installation.
Doing so will hot deploy the plugin: Restarting any services is not necessary.
Watch the DMX log file to see it happening:

.. code:: bash

    Jul 19, 2019 1:47:32 PM systems.dmx.core.osgi.PluginActivator start
    INFO: ========== Starting plugin "DMX LDAP" ==========
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl readConfigFile
    INFO: Reading config file "/plugin.properties" for plugin "DMX LDAP" SKIPPED -- file does not exist
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl pluginDependencies
    INFO: Tracking plugins for plugin "DMX LDAP" SKIPPED -- no plugin dependencies declared
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl createInjectedServiceTrackers
    INFO: Tracking 1 services for plugin "DMX LDAP" [systems.dmx.accesscontrol.AccessControlService]
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl addService
    INFO: Adding DMX core service to plugin "DMX LDAP"
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl publishWebResources
    INFO: Publishing web resources of plugin "DMX LDAP" SKIPPED -- no web resources provided
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl publishRestResources
    INFO: Publishing REST resources of plugin "DMX LDAP" SKIPPED -- no REST resources provided
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl publishRestResources
    INFO: Registering provider classes of plugin "DMX LDAP" SKIPPED -- no provider classes found
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl addService
    INFO: Adding Event Admin service to plugin "DMX LDAP"
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl addService
    INFO: Adding systems.dmx.accesscontrol.AccessControlService to plugin "DMX LDAP"
    Jul 19, 2019 1:47:32 PM systems.dmx.accesscontrol.AccessControlPlugin registerAuthorizationMethod
    INFO: Registering authorization method "LDAP"
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activating plugin "DMX LDAP" -----
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl createPluginTopicIfNotExists
    INFO: Installing plugin "DMX LDAP" in the database SKIPPED -- already installed
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.MigrationManager runPluginMigrations
    INFO: Running migrations for plugin "DMX LDAP" SKIPPED -- installed model is up-to-date (version 0)
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl registerListeners
    INFO: Registering event listeners of plugin "DMX LDAP" SKIPPED -- no event listeners implemented
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl registerProvidedService
    INFO: Registering service "systems.dmx.ldap.service.LDAPPluginService" at OSGi framework
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginImpl activate
    INFO: ----- Activation of plugin "DMX LDAP" complete -----
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginManager checkAllPluginsActivated
    INFO: ### Bundles total: 36, DMX plugins: 19, Activated: 19
    Jul 19, 2019 1:47:32 PM systems.dmx.core.impl.PluginManager activatePlugin
    INFO: ########## All DMX plugins active ##########
    Jul 19, 2019 1:47:32 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: DMX platform started in 67420.76 sec
    Jul 19, 2019 1:47:32 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    INFO: ### Launching DMX Webclient: http://localhost:8080/systems.dmx.webclient/
    Jul 19, 2019 1:47:32 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    WARNING: ### Launching DMX Webclient failed: java.awt.HeadlessException:
    No X11 DISPLAY variable was set, but this program performed an operation which requires it.
    Jul 19, 2019 1:47:32 PM systems.dmx.webclient.WebclientPlugin allPluginsActive
    WARNING: ### To launch it manually: http://localhost:8080/systems.dmx.webclient/

Plugin Deinstallation
=====================

To uninstall a plugin just remove the ``.jar`` file from the ``bundle-deploy`` directory.
Here is what the log tells you when you do so:

.. code:: bash

    Jul 19, 2019 1:47:12 PM systems.dmx.core.osgi.PluginActivator stop
    INFO: ========== Stopping plugin "DMX LDAP" ==========
    Jul 19, 2019 1:47:12 PM systems.dmx.core.impl.PluginImpl removeService
    INFO: Removing DMX core service from plugin "DMX LDAP"
    Jul 19, 2019 1:47:12 PM systems.dmx.core.impl.PluginImpl removeService
    INFO: Removing Event Admin service from plugin "DMX LDAP"
    Jul 19, 2019 1:47:12 PM systems.dmx.core.impl.PluginImpl removeService
    INFO: Removing systems.dmx.accesscontrol.AccessControlService from plugin "DMX LDAP"
    Jul 19, 2019 1:47:12 PM systems.dmx.accesscontrol.AccessControlPlugin unregisterAuthorizationMethod
    INFO: Unregistering authorization method "LDAP"

