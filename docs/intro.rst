.. _intro:

############
Introduction
############

DMX is a platform for knowledge management and collaboration.
It is developed by `DMX Systems <https://dmx.berlin>`_.

.. _intro-license:

********************
DMX is Free Software
********************

DMX is licensed under the `GPLv3 <https://www.gnu.org/licenses/gpl.html>`_ or later.
Visit `git.dmx.systems <https://git.dmx.systems/explore>`_ to explore the code.

.. _intro-data-sovereignty:

****************
Data Sovereignty
****************

DMX lets you keep control of your data.
All data you enter are stored in the DMX database.
Where this database is stored depends on the way you decided to run DMX.

DMX on your local computer
==========================

If you downloaded DMX to your local computer and started it on there the database is stored on that same computer.
It does not leave it unless you copy it elsewhere.
After the initial download of the software, you can use DMX locally and without an internet connection unless you do need :ref:`Geomaps<user-geodata>`.
Geomaps are rendered in DMX with the help of `OpenStreetMap <https://www.openstreetmap.org>`_.
OpenStreetMap is also used to transform the addresses you entered into geographical coordinates.
Those features require an internet connection to work.

DMX as a self-hosted service
============================

If you are running DMX on your own server or if someone shares their self-hosted instance with you, your data are on that person's server.
To work with DMX on a remote server you need an internet connection to communicate with the server.

DMX as a service hosted by us
=============================

If you use DMX on one of our demo sites or if you have a hosting contract with DMX Systems your data is stored on our server.
In this case you also need an internet connection to work with it.

.. _intro-entensibility-though-plugins:

*****************************
Extensibility through Plugins
*****************************

The functionalities of DMX can be extended through plugins.
That can happen in different ways:

#. A plugin can extend the default :ref:`data model <user-modeling>` of a DMX installation by defining specific topic types and association types for your needs. If your data model is implemented in a plugin you can easily install it in new DMX installations and have your own topic types and association types ready for use.
#. A plugin can add new functionalities to the DMX web client that is to the web interface you see.
#. A plugin can also add functionalities to the DMX core part.

For more detail on this, please check back our Developer Documentation in a little while.
You are free to develop your own DMX plugins. To get started we recommend you to start with our `Plugin Template <https://git.dmx.systems/dmx-plugins/dmx-plugin-template>`_.

.. _intro-overview-of-plugins:

Overview of Plugins
===================

There is a variety of existing plugins.
Some of them are Free Software (see the table below).
All freely available plugins can be found in our `download section <https://download.dmx.systems/plugins/>`_.
Their source code is available in our the `plugin repositories <https://git.dmx.systems/dmx-plugins>`_.
The installation of plugins is explained in our :ref:`Admin Documentation <admin-plugin-installation>`.

Some plugins are licensed under a proprietary license and can be purchased from us.

==========================  ===========================================================================================  ==========
Plugin Name                 Functionality                                                                                License
==========================  ===========================================================================================  ==========
``dmx-plugin-template``     A starter project for writing a DMX plugin
``dmx-littlehelpers``       Utilities for DMX plugin developers                                                          GNU AGPLv3
``dmx-performance``         Creates a dummy topicmap with 150 topics/associations for performance testing
``dmx-tableview``           A standalone table view for DMX based on VueJS and ElementUI                                 GNU AGPLv3
``dmx-tableview-command``   Provides an "Open Tableview" topic command                                                   GNU AGPLv3
``dmx-ldap``                LDAP Authentication for DMX
``dmx-sendmail``            A service to send mails using either SMTP or the HTTP Web API v3 of sendgrid.com             GNU AGPLv3
``dmx-geospatial``          Spatial database capabilities for DMX                                                        GNU AGPLv3
``dmx-thymeleaf``           A DMX plugin to enable server-side HTML generation based on the Thymeleaf template engine    GNU AGPLv3
``dmx-tags``                Tagging of topics or associations
``dmx-entity-mapper``       Mapping database fields related to concepts across IT systems
``dm4-sign-up``             DeepaMehta4 plugin providing a simple, template based, registration dialog for users
``dm4-mail``                DeepaMehta4 mail plugin                                                                      GNU GPLv3
``dm4-notifications``       DeepaMehta4 plugin providing persistent notifications for users across sessions and screens
``dm4-typedef``
==========================  ===========================================================================================  ==========

*****************************
About this documentation site
*****************************

This documentation is hosted by `Read the Docs <https://readthedocs.org/>`_.
As Read the Docs offers free hosting of documentation they kindly ask users to allow ads to be displayed at the bottom of the left sidebar. You can read more about it `here <https://docs.readthedocs.io/en/latest/advertising/ethical-advertising.html>`_.
