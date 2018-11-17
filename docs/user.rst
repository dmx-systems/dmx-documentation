######################
DMX User Documentation
######################

Please note we have a :ref:`glossary-label` for the terms used in this documentation.

************************
Getting started with DMX
************************

To get started with DMX you can

* visit our `public demo site`_ and play with DMX. Do not enter any private or sensitive information on the demo site!
* run DMX on your computer by choosing one of the following ways:

  * :ref:`installation-zip-label` (Windows, Mac, Linux)
  * :ref:`installation-apt-label` (Linux only)

.. _public demo site: https://demo.dmx.systems

Introduction to the web interface
=================================
When you first look at a blank DMX interface, there is not much there, yet. Here is what you see in the upper tool bar:

* In the upper right corner there is a "Login" button. Log in with the username "admin" and no password.
* In the upper left corner there is a drop-down menu called "Workspace". The only existing workspace is called "DMX". This is your general context everything you are about to do is going to be in (for the moment). Later you will be able to select one of all your workspaces here.
* Next to it there is another drop-down menu called "Topicmap". There is only one topic map, it's called "untitled".

.. figure:: _static/upper-toolbar.jpg
    :alt: Tool bar with workspace and topic map selection

* Both have a context information button. Click them to see more information. For the moment the most interesting information is the access control. **The first default workspace and the default topic map are in SharingMode "public" that is: They are not private, but world-readable.**

Adding your first topic
=======================

Right-click anywhere into the empty space. You will notice that there are two items somewhere in your topic map: "DMX" and "untitled" with two different icons. Where do they come from? You uncovered your workspace and your topic map by retrieving their extra information. Do you want to know more about them? Yes, but not now. Let's hide (not delete!) them! Long-click one of them and hold the mouse button until a dark circle with four buttons appears. Pull the mouse onto "Hide". Hide the other one as well.

Right-click into the emptiness. A window appears that offers you to search something or to create something. Let's create something!

.. image:: _static/search-create.jpg

DMX wants to make sure that you do not create something that already exists. That's why you enter whatever you want to create into the search field. Let's assume you want to enter all persons and organizations involved in a project. Start by entering a name. DMX will answer "No match". Select "Person" from the prefined topic types and click "Create".

.. image:: _static/create-person.jpg

You will see a square on your topic map. It contains the name you entered and it states that this is the person's first name. Oh no! You entered the given name and the surname? Let's correct this: Long-press the square and select "edit" from the context menu. On the right-hand side there is a detail panel now. It contains many fields you might need to edit any topic you categorized as a person. Correct the name and click the save button at the very bottom. Add a second topic, e.g. "Organization No. 1". Select the topic type "institution" this time.

.. image:: _static/person-organization.jpg

Adding your first association
=============================

You probably chose your second topic because it is somehow related to the author you entered. We will now establish a connection between the two topics by drawing one onto the other. You can click onto the association between them to see more information about it in the detail panel. Don't bother if it looks weird, you will learn how to qualify the association later on.

Moving the map and the items
==============================
Note that you can drag the whole topic map into any direction. You can also grab every item and drag it where you want it to be.

Hiding items
============

You can hide items from the topic map by long-clicking onto them and using the "Hide" button in the context menu. If you bring them back to the map later by searching them, they will reappear in the same spot in your map.


DMX's default topic types
====================================

As you have seen, DMX comes with a few predefined topic types: Event, institution, note, web resource, and person. Each topic type brings certain fields you can fill in: While events have dates and locations, persons can have numbers and web resources have a URL. As you probably need more to map what you have in mind you will now learn how to create your own topic types.

******************************
Getting to know the data model
******************************

Exploring your first topic
==========================

After your edit the detail panel is still open. It has four tabs. The first one ("person") shows all the content you entered. Go to the second tab ("Related"). Do be confused by what you see now: It displays four topics that you can sort. Why four? Well, there is the concrete person, the topic type "person"... ZU KOPMLIZIERT; DAS MUSS SPÄTER KOMMEN.


Create your first topic type
===================================

You just added your favourite author so next you need a topic type called "book" with certain properties all books have, e.g. a publisher, a year etc.

**************
Access Control
**************


