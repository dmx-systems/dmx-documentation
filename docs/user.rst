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
* In the upper right corner there is a button with an arrow. It is greyed out if nothing is selected. Later you can open the detail panel by pressing it.

Adding your first topic
=======================

Right-click into the emptiness. A window appears that offers you to search something or to create something. Let's create something!

.. image:: _static/search-create.jpg

DMX wants to make sure that you do not create something that already exists. That's why you enter whatever you want to create into the search field. Let's assume you want to enter all persons and organizations involved in a project. Start by entering a name. DMX will answer "No match". Select "Person" from the prefined topic types and click "Create".

.. image:: _static/create-person.jpg

You will see a square on your topic map. It contains the name you entered and it states that this is the person's first name. Oh no! You entered the given name and the surname? Let's correct this: Long-press the square and select "edit" from the context menu. On the right-hand side there is a detail panel now. It contains many fields you might need to edit any topic you categorized as a person. Correct the name and click the save button at the very bottom. Add a second topic, e.g. "Organization No. 1". Select the topic type "institution" this time.

.. image:: _static/person-organization.jpg

Adding your first associations
==============================

Next you want to enter how the person is related to the organization. You can establish a connection between the two topics by draging the person onto the organization. A square will appear that tell you this is an "Organization Association". You can view and edit details of associations the same way you can with topics. If you do so you will notice that an "Organization Association" has a specific drop-down menu called "organizational role". It lets you choose the person's role from a predefined set of common roles people have in organizations. Later you will learn how to create custom relationships ("associations") and how to predefine their properties.

.. image:: _static/diff-roles-in-organization.jpg

Thinking of real-world social networks, you will find that often people have more than one role. DMX can represent this: Simply drag the person onto the organization again and specify their second role.

.. image:: _static/multiple-assocs.jpg
    :width: 300

Inline editing
==============

******************************
Getting to know the data model
******************************

DMX's default topic types
====================================

As you have seen, DMX comes with a few predefined topic types:

- event,
- institution,
- note,
- web resource,
- person.

Each topic type brings certain fields you can fill in: While events have dates and locations, persons can have telephone numbers and web resources have a URL. As you probably need more than these presets to map what you have in mind you will now learn how to create your own topic types.

One of the core characteristics of DMX is that you can look at the underlying data model and modify it in the same interface.

Exploring your first topic
==========================

.. image:: _static/context-menu.png
    :width: 220

To look at the data model of a person, click onto the person you created and select "What's related". The detail panel on the right side will open. It has four tabs. The first one ("person") shows all the content you entered. You are now looking at the second one, called "Related". It displays a list of all items that are logically related to this concrete person:

- the institution you linked the person to
- the name of the person (because so far this is the only information you added to the person)
- the topic type "person". Your concrete person is an instance of the general idea of persons, so it is linked to this general idea, the topic type. You can think of the topic type as the definition of what a person is.
- the topic map this topic is associated with
- the workspace the topic is in

You can now click on each of the list items and they will appear on the topic map. Click onto the topic type "person". The topic type "person" is displayed with an association to the instance "Cathy Jones". The link between both has a different colour and you can again click onto the link, show "What's related" and you can see that this association is an "instantiation": The topic is one instance of the topic type. To see if there are more instances (more persons), show the "What's related" of the topic type "person". Among other information about how the topic type is integrated into the rest of the context you can see all existing persons you entered so far.

Here you are looking at your data and at a part of the data model it is based upon. Again, you can hide what you do not want to see in your map when you are done exploring.

.. image:: _static/intro-data-model.jpg

Create your first topic type
===================================

To create your own topic type with a few properties here is how to procede. Let's say you want to add a topic type "publication". Each publication shall have a title and a year.

- Open the search field. Enter "Publication", select "Topic Type" and press "create".
- Go into editing mode via the context menu. Change the data type from "Text" to "Identity" and hit "Save". Click somewhere into your map to close the detail panel.
- Open the search field and enter "Title". You will find that two entries already exist. They come from the default topics types "Event" and "Note" which also have titles. Create a new topic type "Title".
- Grab the title item and pull it on the publication item. DMX will display what you just created: You created an association of the type "Composition Definition". Composition Definition means that you are defining a more complex context between item on your map: The relationship between a publication, a title and a year. "Cardinality: One" means that each publication has exactly one title, not more. The rest of the information refers to the role types: The publication is called the parent, the title is the child. These are technical terms to define that a publication has a title, but a title does not have a publication.

.. image:: _static/composition-definition.jpg

- Again, click somewhere onto the map and reopen the search field. Search for the year and open the existing topic type. Pull it onto the publication.

You are now ready to use this data model you just built to add content.

- Open the search field and enter the title of a publication. From the Topic Type menu you can now select "Publication".
- As the title was the first property you added to the composition, it is automatically filled in from the search field.
- Edit your new publication and add a year.

*******************
Organizing your map
*******************

Moving the map and the items
==============================
Note that you can drag the whole topic map into any direction. You can also grab every item and drag it where you want it to be. Zooming in or out is done by scrolling up or down.

Bulk selection
==============
To move more than one item at a time you can bulk select several items by keeping the CTRL key pressed and clicking them. The selected topics now have a blue border. Drag the whole selection where you want to place it.

.. image:: _static/bulk-select.jpg
    :width: 600

.. image:: _static/bulk-move.jpg
    :width: 600

Hiding items
============

You can hide items from the topic map by long-clicking onto them and using the "Hide" button in the context menu. If you bring them back to the map later by searching them, they will reappear in the same spot in your map.

Assigning icons to topic types
==============================

You can assign icons from the Font Awesome collection to your topic types. Let's say you have a topic type "Publication" and you want all publications in your map to have a book icon.

- Click onto the topic type "Publication", //not// onto an individual publication you already added. You are about to modify the general concept of all your publications, not an existing instance of it.
- Use the little arrow in the upper right corner to open the detail panel.
- Go to the fourth tab called "View". Here you can view and edit the configuration of the topic type. Click "Edit".
- Click into the white field labeled "Icon".

.. image:: _static/open-icon-selection.jpg

- You can either select an icon directly or use the search box.
- Hit save to apply the icon to all topics that are publications.

.. image:: _static/new-icon.jpg

Assigning colours to association types
======================================


**************
Access Control
**************

tbd...

**********
Topic maps
**********

tbd...

Geodata
=======
DMX comes with built-in support for geodata. The feature is still under construction. Right now every topic with an address can be shown on a map. The so-called geomaps are a special type of topic map in DMX. Geomaps are based on openstreetmap.org. Here is an example of how to create and populate them: Edit a person or an institution and add an address.

.. image:: _static/add-address.jpg
    :width: 800

Open the search and create dialogue. Enter a name for the new topic map, e.g. "Our Geomap". In the topic type selection choose "Topicmap". Underneath it you can now choose the type of topic map you want to add. Select "Geomap" and press "Create".

.. image:: _static/add-geomap.jpg

Open the topic map selection in the upper toolbar and select your newly created geomap. The map is displayed with all items you assigned an address to. Reload the map in your browser after any changes to address data to have them updated.

.. image:: _static/topic-map-selection.jpg

If you click onto an item the in-map details show you what is there.

.. image:: _static/display-map-item.jpg

You return to the normal topic map via the same "Topicmap" drop-down menu.