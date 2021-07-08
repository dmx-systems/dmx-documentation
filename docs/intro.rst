.. _intro:

############
DMX Overview
############

************
What is DMX?
************

DMX is a knowledge building platform for individuals and work groups.
The DMX UI is designed to support memorization, retrieval, and collaboration.
DMX strives to become a work environment for knowledge workers e.g. students, authors, journalists, researchers, archivists.

One DMX specialty is the Topic Maps based user interface which puts the user in direct contact with the objects of discourse -- instead with applications and windows, and the borders they create.

Technically DMX is a web application server written in Java. Its service-oriented architecture is OSGi-based (Apache Felix); DMX application develeopers need no OSGi knowledge; JAX-RS knowledge is recommended though. DMX comes with web server (Jetty), database (Neo4j), the "DMX Webclient" (built in Vue.js, Vuex, Element UI) included, and offers a plugin development framework. DMX plugins are full-stack (from data model to frontend) and hot deployable. A DMX default installation is *single-user*; its web server will reject any request not from localhost. From 5.0-beta-1 (Jan 30, 2019) on to current 5.2 the size of the DMX download zip file is unchanged at 7.5 MB.

Driven by ... DMX follows special approaches at every level: data model, user interface, and application model.

**********
Data Model
**********

In DMX **information** is represented as *things* and *relationships* between things.

.. figure:: _static/organization-association.png

    A person, a organization, and a note, connected by relationships.

Things in DMX are called *Topics*, and relationships are basically called *Associations*. A topic can represent e.g. a person, an organization, a file, a book, or a planet. Also an idea or an emotion can be stored as a topic.

One specialty of the DMX data model is that associations can not only connect topics, but associations as well!

.. figure:: _static/create-assoc-with-assoc.png

    2 notes, one is connected to a topic, and one to an association. The dark gray association connects a topic with an association. You can immediately say what the author want to express here, can't you?

... and yes, an association can also connect 2 associations. Not shown here.

Semantic Storage, Immutability, Value Integration

**************
User Interface
**************

DMX can't store or display **knowledge**. No machine or software ever can. Knowledge is created in heads, in heads of living creatures who constantly make sense of the situation around, driven by intent, mood, preconceptions etc. Knowledge creation is an *individual* process first of all -- and the design of the DMX UI accomodates that.

The DMX UI handles **information** in a way that *supports* the individual users with knowledge building, memorization, and retrieval.

.. hint::

    This DMX documentation rewrite effort is very young. It started with the advent of DMX 5.2.

    In contrast the DMX 5.0 documentation is much more complete. Click the bottom/left corner and choose version ``stable``.
