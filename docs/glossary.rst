.. _glossary-label:

############
DMX Glossary
############

**************************
Structuring your knowledge
**************************

.. _glossary-topics-and-topic-types-label:

Topics and Topic Types
======================
In the DMX data model, the whole world consists of topics and associations.
You can think of them as things and how they are related to each other.
Thus, a topic can be anything, a contact, a location, a file, a website, a thought etc.

In contrast to topics, topic types are more general:
A topic type is the idea of a topic without its concrete shape.
On the level of types you describe models of what you want to create.

As an example, think of mapping your music collection:
The topic type could be called "album".
An album could have an author, a title, a year, a genre etc.
In DMX you create a topic type with these fields (which are also topic types).
Each concrete album you create then has a form with these fields to fill in.

DMX comes with a few predefined topic types, e.g. person, institution, or note.
You can add your own topic types and by doing so you define your own data model.

.. _glossary-associations-and-association-types-label:

Associations and Association Types
==================================

Associations represent the relationships between items.
They represent real-world semantics.
These can be relationships between topics or between associations or even between a topic and an association.
The most important characteristic of associations in DMX is that you can qualify them to give them the meaning *you* need.
They are not just drawn lines between two dots, but they can have association types.
While a line between two items is human-readable, an association that has an association type is machine-readable, too.

Just like topics associations have specific types, too.
With association types you can tell more about the links between your Topics.
Timelines, roles in an organization or authorship information are examples of Association Types.

To stick with the example of the music collection, authorship would be the association type you define.


.. _glossary-types-versus-instances-label:

Types versus Instances
======================

As seen, types are the ideas or abstract descriptions of the things you want to map.
Instances, as opposed to types, are the concrete things, your content.
If you have a topic type "colour", all individual topics like "red", "green", "blue" are instances of the topic type colour.
Instances are the concrete occurences of the type:
Topics are always instances of their topic type.
Associations are instances of their association type.

In DMX, this difference is important to understand as you *can* visualize both layers in the same topic map!

.. _glossary-data-model-label:

**************
The Data model
**************

.. _glossary-simple-data-types-label:

Simple Data types
=================

Every topic or association has a data type.
They are six different data types in DMX.
Four of them are so-called **simple** types:

* **text:** This is the default data type and it contains a text string.
* **number:** An example is "year".
* **boolean:** yes/no resp. true/false
* **html:**

.. _glossary-composites-and-composition-definitions-label:

Composites and Composition Definitions
======================================

The two other data types are **composites**.

tbd...

.. _glossary-sharing-mode-label:

Sharing Mode
============

The Sharing Mode is the access control setting for workspaces.
Each workspace can be defined as private, confidential, collaborative, public or common.
Read more about the implications of the five sharing modes in the :ref:`User Guide <workspaces-collaboration-acl-label>`.

.. _glossary-organization-of-data-label:

********************
Organization of Data
********************

.. _glossary-database-label:

Database
========
The database contains your knowledge at large. Everything you enter is saved in the database until you delete it. The predefined data structures DMX comes with are in there as well.



.. _glossary-workspace-label:

Workspace
=========
Although each topic and association is part of exactly *one* workspace you can display them in many topic maps.
A workspace can have one or many members who have access to its content.
This feature makes workspaces the basis of collaboration.