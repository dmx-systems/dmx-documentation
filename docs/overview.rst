.. _overview:

############
DMX Overview
############

************
What is DMX?
************

DMX is a knowledge building platform for individuals and work groups.

DMX can't store or display **knowledge**. No machine or software ever can. Knowledge is created in heads. In the heads of human beings (and other creatures) who constantly make sense of the situation around, driven by intent, mood, preconceptions etc. First of all knowledge creation is an *individual* process -- and the design of the DMX UI accommodates that.

One DMX specialty is the Topic Maps based user interface which puts the user in direct contact with the objects of discourse -- instead with applications and windows, and the borders they create. The DMX UI allows the user to handle **information** in a way that *supports* her individual knowledge building, memorization, and retrieval. DMX strives to become a work environment for knowledge workers e.g. students, authors, journalists, researchers, archivists.

Technically DMX is a web application server written in Java. Its service-oriented architecture is OSGi-based (Apache Felix); DMX application developers need no OSGi knowledge; JAX-RS knowledge is recommended though. DMX comes with web server (Jetty), database (Neo4j), the "DMX Webclient" (built in Vue.js, Vuex, Element UI) included, and offers a plugin development framework. DMX plugins are full-stack (from data model to front end) and hot deployable. A DMX default installation is *single-user*; its web server will reject any request not from localhost. From 5.0-beta-1 (Jan 30, 2019) on to current 5.2 the size of the DMX download zip file is unchanged at 7.5 MB.

Driven by ... DMX follows special approaches at every level: data model, user interface, and application model.

***********************
Topics and Associations
***********************

DMX represents information as *topics* and *associations*:

.. figure:: _static/organization-association.png

    3 topics connected by 2 associations. The topics represent a person, an organization, and a note respectively. One of the 2 associations is distinguished by color and label.

A **Topic** can represent *any* type of object. What object types are available is decided by the DMX user. A DMX user can create new types, as well as modify existing ones, interactively in the DMX Webclient. Another way of obtaining new types is by installing DMX plugins.

An **Association** represents a relationship between two objects. The relationship's *meaning* can be expressed by the association's *type* and *value*. Both are optional. A generic association still expresses *some* relationship between 2 objects.

DMX comes with a collection of basic types for Personal Information Management.

**********
Role Types
**********

At this point you may wonder: "Are associations directed?" Answer: "More than that!"

In DMX the 2 ends of an association are not just attributable by "source" and "target", but by *arbitrary* roles, e.g. "parent", "child", "trainer", "trainee", or "trigger" etc.

Each of the 2 ends of a DMX association is defined by:

- The *Player Object*, either a Topic, or an Association
- A *Role Type*

The **Role Type** expresses what role the player plays in the association.

You can use role type "default" at both ends to express undirectedness.

*************************
Associative Model of Data
*************************

The above image suggests the DMX storage model is *graph* based: *nodes* and *edges*. Actually DMX is based on an extension of the graph model: the *Associative Model of Data*.

.. figure:: _static/dmx-assoc-data-model.svg
   :width: 240px
   :align: left

While in the graph model an edge always connects 2 *nodes*, in the Associative Model of Data an edge can connect *edges* too. Either at one end (A2), or both (A3).

Basically DMX makes associations objects of discourse too. Associations can be associated with other topics/associations, just like topics. This results in expressive highly-connected structures at both levels, data model and content (= *instances*).

.. figure:: _static/create-assoc-with-assoc.png

    2 notes, one is connected to a topic, and one to an association. The dark gray association connects a topic with an association. You can immediately say what the author want to express here, can't you?

... and yes, an association can also connect 2 associations. Not shown here.

.. hint::

    Find out more about the `Associative Model of Data <devel.html#associative-model-of-data>`_ in the DMX Developer Guide.

*****
Types
*****

The above examples show topics of type "Person", "Organization", and "Note". Other *topic types* could be e.g. "Person", "Organization", "File", "Book", or "Planet". Also an idea or an emotion can be expressed as a topic.

DMX comes with a type collection pre-installed, suitable for basic Personal Information Management tasks. More types can be created interactively, or by installing plugins.

.. figure:: _static/person-model.png



Role Types, Semantic Storage, Immutability, Value Integration

**************
User Interface
**************

.. figure:: _static/detail-panel.png

    The typical DMX user interface presents a **Topic Map**: objects of discourse as well as their relationships are represented by **Topics** and **Associations**. Different **types** of topics are differentiated by icon. Different types of associations are differentiated by color.

Of course there is more to a topic than just an icon and a label. Their is *detail* information behind it. To see it just click the topic. Its details are displayed in-map:

.. figure:: _static/in-map-details-pinning.png

And yes, associations also have detail information. To see them in-map just click the association. Now we can see: the green ones are of type "Organization Involvement". A certain person is involved in a certain organization.

.. figure:: _static/create-organization-association.png

     A selected association (thicker line) and its detail information displayed in-map. Details comprise the association's type and value (not shown), the 2 player topics the association connects, each one qualified by a **Role Type**.

.. hint::

    This DMX documentation rewrite effort is very young. It started with the advent of DMX 5.2.

    In contrast the DMX 5.0 documentation is much more complete. Click the bottom/left corner and choose version ``stable``.
