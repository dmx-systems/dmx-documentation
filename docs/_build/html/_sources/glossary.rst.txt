.. _glossary-label:

===============
DMX Glossary
===============

The glossary entries are in alphabetical order. You might want to start to read about Topics, Associations, and Topic Types though.

Aggregation Definition
----------------------

DO WE NEED THIS? loose dependency of the Child Type to its Parent Type

Association
-----------

Associations represent the relationships between your Topics. The most important characteristics of associations in DMX is that you can qualify them so that they get the meaning you need (see Association Types).

Association Type
----------------

Just like Topics Associations have specific types, too. With Association Types you can tell more about the links between your Topics. Timelines or authorship information are examples of Association Types.

Composite
---------

A composite is a data type that allows you to store complex types of data. FIXME
a type which composes or aggregates other types in a cardinality of one or many

Composition Definition
------------------------

DO WE NEED THIS? strong, direct dependency of the Child Type to the Parent Type

Data model
----------


Data type
----------

Topic Types can be of different data types which means they can have different characteristics. They can be numbers, text, Boolean (yes/no), or composites. With composites you can model more complex contexts (see above).

Detail panel
------------
The detail panel is located the right side of the screen. It shows details about a selected item (Topic, Topic Type or association). It has an edit button at the very bottom.

Graph database
---------------

the storage layer structuring your data as nodes and edges

Instance
--------

In DMX instance is a very technical term. The idea is that you have a Topic Type, e.g. "colour" and many individual Topics, e.g. "red", "green", "black". We call each of these Topics an instance of the Topic Type "colour". Technically this means that all instances are connected to their Topic Type. When you create your Data Model, and you add more colours, DMX actually creates Associations in the underlying DAta Model: It links "yellow" to the Topic Type "colour". This also means you can build a Data Model without knowing about Instances but it helps to understand DMX.


Sharing Mode
------------

The Sharing Mode is the access control setting for Workspaces. Each Workspace can be defined as private, confidential, collaborative, public or common.

Toolbar
-------
The toolbar is the top bar in the DMX user interface. It has menus for workspace selection, topic map selection, a search field, a help button and the login/logout buttons.


Topic
-----
In the DMX data model, the whole world consists of topics and associations. You can think of them as things and how they are related to each other. Thus, a *topic* can be anything, a contact, a location, a file, a website, a thought etc. *Topics* are grouped into *Topic Types* and they can be shown on a *Topic Map*.


Topic Map
---------
A Topic Map is a visual context you work in. You place all your Topics and Associations into Topic Maps to display them.

Topic Map Panel
---------------

The topic map panel is the main area of the DMX user interface. It displays the currently chosen topic map with all its Topics, Topic Types, and associations.

Topic Type
----------
*Topic Types* are the categories you choose to put your *Topics* into. DMX comes with a few predefined *Topic Types*, e.g. person, institution, or note. You can add you own Topic Types and by doing so you define your own data model.


Workspace
---------

A Workspace is the larger context you are working in: All you content belongs to one (and only one) Workspace. A Workspace can have one or many members who contribute content, it has one or more Topic Maps and a specific SharingMode.



