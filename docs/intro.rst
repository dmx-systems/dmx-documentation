.. _intro:

############
Introduction
############

DMX is a platform for knowledge management and collaboration.
It is developed by `DMX Systems <https://dmx.berlin>`_.
DMX is a web application.
It comes with a built-in Jetty web server and a default web client that brings the application to your browser.


.. _intro-license:

********************
DMX is Free Software
********************

DMX is dual licensed:

#.  The Community Edition is licensed under the `GNU Affero General Public License (GNU AGPLv3) <https://www.gnu.org/licenses/agpl-3.0.en.html>`_ . In short, this means that you are allowed to run the software of any purpose, to study and modify the source code, and to distribute copies or modified copies of the code. Note that you have to publish all modifications you make under the same license. Visit `git.dmx.systems <https://git.dmx.systems/explore>`_ to get the source code. 
#.  The Enterprise Edition is licensed under a proprietary license. With the Enterprise Edition you are entitled to develop or use proprietary plugins or other modifications of the software.

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

Find out more about our existing plugins in the :ref:`Plugin section <plugins-overview-of-plugins>`.

Of course, you are free to develop your own DMX plugins.
For more details on this, please check back our Developer Documentation in a little while.
To get started we recommend you to start with our `Plugin Template <https://git.dmx.systems/dmx-plugins/dmx-plugin-template>`_.

*****************************
About this documentation site
*****************************

This documentation is hosted by `Read the Docs <https://readthedocs.org/>`_.
As Read the Docs offers free hosting of documentation they kindly ask users to allow ads to be displayed at the bottom of the left sidebar. You can read more about it `here <https://docs.readthedocs.io/en/latest/advertising/ethical-advertising.html>`_.



