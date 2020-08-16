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
   
   # The URL the WebSocket clients use to connect to the WebSocket server
   dmx.websockets.url = ws://localhost:8080
   
.. _admin-request-filters:
   
Request Filters
===============

Every incoming request has to pass two filters: The request level filter and the resource level filter.

#. The request level filter checks on the HTTP level if the request is a read or a write request and whether it comes from a logged in user with a valid session.
#. The resource level filter checks the classical ACL: Which workspace is the requested object assigned to? What are the sharing mode and memberships (resp. the ownership) of that workspace? Does the user have any permissions on the requested resource?

In the Request Filters section you can control if anonymous users, that is people who are not logged in, can read and/or write content.

.. warning:: By default, anyone can read but not write content! Consider to change this!

.. code:: bash

   dmx.security.anonymous_read_allowed = ALL
   dmx.security.anonymous_write_allowed = NONE

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

In a default installation, requests are only allowed from localhost for security reasons.
This is configured in the subnet filter setting.
If you do not change this setting and you run DMX on your laptop without any other web server your instance is not accessible from outside our computer.

.. code:: bash

   # Requests are allowed from this subnet only.
   # An address range denoted by network number and netmask.
   # To allow local access only set "127.0.0.1/32". This is the default.
   # To allow global access set "0.0.0.0/0".
   dmx.security.subnet_filter = 127.0.0.1/32

If you want your instance to be accessible from the internet you have to change the subnet filter setting depending on your web server configuration and IP address.
To allow global access enable the subnet filter like so:

.. code:: bash

    dmx.security.subnet_filter = 0.0.0.0/0

.. _admin-user-accounts-and-admin-password:

User Accounts and Admin Password
================================

In the section about user accounts you can set the initial password for the admin account.
It is only evaluated when the database is initialized for the first time, after that it is saved in the database.
The admin password can be changed via the web client just like :ref:`user passwords<user-changing-a-password>`.

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

.. _admin-ldap-configuration:

LDAP Configuration
==================

You can use an existing LDAP or Active Directory backend to manage DMX users.
The configuration file contains a section with the following options.
Just leave them empty if you do not have any such backend.

Specify your LDAP/AD server and port.
The following protocols are supported:

1. StartTLS ``ldap://<hostname>:389``. The default port is 389.
2. LDAPS ``ldaps://<hostname>:636``. The default port is 636.
3. LDAP ``ldap://<hostname>:389``. The default port is 389.

.. code:: bash

   dmx.ldap.server = 127.0.0.1
   dmx.ldap.port = 389

For an unencrypted connection the ``dmx.ldap.server`` can be a fully-qualified domain name or an IP address. For an encrypted connection ``dmx.ldap.server`` must contain the hostname that is specified in the CN of the certificate. The IP address is not sufficient.

There are two additional settings for self-signed certificates used with the Java keystore.
The first one is the path to your keystore file.
The second one is the keystore password.

.. code:: bash

   javax.net.ssl.trustStore = /path/to/keystore.jks
   javax.net.ssl.trustStorePassword = changeit

The manager and password are your LDAP bind account and bind password.

.. code:: bash

   dmx.ldap.manager = cn=admin,dc=example,dc=org
   dmx.ldap.password = secret

Configure where DMX shall start the search for users in the LDAP/AD tree, e.g.

.. code:: bash

   dmx.ldap.user_base = ou=users,dc=example,dc=com

Which attribute is used to identify a user, e.g.

.. code:: bash

   dmx.ldap.user_attribute = uid

In the user filter you can add an additional filter to verify if the user is authorized to log in to DMX via LDAP, e.g.

.. code:: bash

   dmx.ldap.user_filter = (&(objectclass=inetOrgPerson)(memberof=cn=dmxusers,ou=groups,dc=example,dc=com))

The member group option is only needed if the DMX LDAP plugin is used to *create* new users.
In that case, new users can be automatically added to a group.
This is usually the group that is also used in the ``dmx.ldap.filter``, e.g.

.. code:: bash

   dmx.ldap.user_member_group = cn=dmxusers,ou=groups,dc=example,dc=com

The last option specifies DMX's loglevel for everything related to the LDAP plugin.
Currently two loglevels are supported:

1.  ``INFO`` (default): Only warnings and errors are logged including possible misconfigurations.
2.  ``DEBUG``: Hints, warning and errors are extensively logged during configuration and runtime phase.

.. code:: bash

   dmx.ldap.logging = INFO

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
       RewriteRule /(.*)           ws://127.0.0.1:8080/$1 [NE,P,L]
       
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

.. _admin-debian-package:

**********************
The DMX Debian Package
**********************

Installing DMX from our apt repository
======================================

We provide a Debian/Ubuntu package for apt-based Linux distributions.
You can add the repository and install DMX in one go with a script:

.. code:: bash

    ~$ curl -sS https://download.dmx.systems/repos/dmx-install.sh | sudo bash

Alternatively, you can run the steps manually:

.. code:: bash

    # Add the file /etc/apt/sources.list.d/dmx-repo.list
    ~$ sudo bash -c 'echo "deb https://download.dmx.systems/repos/ubuntu/ xenial/" >/etc/apt/sources.list.d/dmx-repo.list'

    # Add the key:
    ~$ curl -fsSL https://download.dmx.systems/repos/gpg | sudo apt-key add -

    # Update the packet sources and install DMX
    ~$ sudo apt update && sudo apt install dmx

    # Adjust the configuration
    ~$ sudo nano /etc/dmx/config.properties 

    # Start DMX
    ~$ sudo systemctl start dmx

    # Browse to the local instance
    http://localhost:8080/systems.dmx.webclient/

Overview of directories
=======================

The DMX Debian package uses Linux' File System Hierarchy Standard.
You find the respective components in the directories listed below:

==========================  ===================================
File type                   Path
==========================  ===================================
Configuration files         ``/etc/dmx/``
Java files                  ``/usr/share/dmx/``
DMX database and file repo  ``/var/lib/dmx/``
Log files                   ``/var/log/dmx/``
Bundle cache                ``/var/cache/dmx/``
Examples                    ``/usr/share/doc/dmx/``
Systemd unit file           ``/etc/systemd/system/dmx.service``
==========================  ===================================

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

Plugin Troubleshooting
======================

You may get Java error messages if you try to deploy a plugin and you are running DMX from the ``./dmx-linux.sh`` start script on a Linux computer with a GNOME desktop and you have ``openjdk-8-jre-headless`` installed. Edit the file ``/etc/java-8-openjdk/accessibility.properties`` with root privileges and comment out the following line:

.. code:: bash

    #assistive_technologies=org.GNOME.Accessibility.AtkWrapper
