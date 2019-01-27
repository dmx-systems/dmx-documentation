.. _user-beginners-tutorial:

###################
Beginners' Tutorial
###################

To get started with DMX you can

* visit our `public demo site`_ and play with DMX. Do not enter any private or sensitive information on the demo site!
* run DMX on your computer by choosing one of the following ways:

  * :ref:`installation-zip` (Windows, Mac, Linux)
  * :ref:`installation-apt` (Linux only)

.. _public demo site: https://demo.dmx.systems

.. _tutorial-a-first-look-at-dmx:

*******************
A first look at DMX
*******************

When you first look at a new installation of DMX, there is not much there, yet.
We have a detailed guide to the :ref:`user interface<user-the-dmx-user-interface>` below but here are some essentials:

* In the upper right corner there is a "Login" button. Log in with the user name "admin" and no password.
* In the upper left corner there is a workspace selector. The only existing workspace is called "DMX". This is your general context everything you are about to do is going to be in (for the moment). Later you will be able to select one of all your workspaces here.
* Next to it there is another drop-down menu. This is the topicmap selector. Right now, there is only one topicmap, it's called "untitled".

.. figure:: _static/upper-toolbar.png
    :alt: Tool bar with workspace and topicmap selection

* Both have a context information button. Click them to see more information. For the moment the most interesting information is the access control. **The first default workspace and the default topicmap are in SharingMode "public" that is: They are not private, but world-readable.** This is important to keep in mind if you are trying DMX on our public demo site or if you installed it on a server that is connected to the internet. You can find out more about access control in the section about :ref:`Collaboration and Sharing<user-collaboration-and-sharing>`.
* In the upper right corner there is a button with an arrow. It is grayed out right now.

.. _tutorial-adding-your-first-topic:

***********************
Adding your first topic
***********************

:ref:`Content Authoring<user-content-authoring>` is described in-depth below.
This is just a short walkthrough to help you create your first items in DMX.

Right-click into the emptiness.
A window appears that offers you to search something.
This is the search/create dialog.
Let's create something!

.. image:: _static/search-create.jpg

DMX wants to make sure that you do not create something that already exists.
That's why you enter whatever you want to create into the search field.
Let's assume you want to enter all persons and organizations involved in a project.
Enter a person's first name.
DMX will answer "No match".
Select "Person" from the predefined topic types and click "Create".

.. image:: _static/create-person.jpg

You will see a rectangle on your topicmap.
It contains the name you entered and it states that this is the person's first name.
Oh no!
You entered the given name and the surname?
Let's correct this:
Long-press the rectangle and select "edit" from the context menu.

.. image:: _static/context-menu-edit.jpg

On the right-hand side the detail panel opens.
It contains many fields you might need for all persons.
Correct the name and click the save button at the very bottom.
Add a second topic, e.g. "Notes for the interview".
Select the topic type "Note".

.. image:: _static/person-and-note.png

Add a third topic e.g. "Organization No. 1".
Select the topic type "Organization" this time.

.. image:: _static/person-note-organization.png

.. _tutorial-adding-your-first-association:

*****************************
Adding your first association
*****************************

Next, you want to visualize that the note is related to the person.

To create an association between two topics you grab the little dot at the upper border of one of the two topics.

.. image:: _static/create-simple-association-1.png

Drag it onto the other topic until that topic is highlighted by a blue border.

.. image:: _static/create-simple-association-2.png

A rectangle appears.
These are the in-map details.
They tell you that this is an "Association", that the association type is "Association".
You can think of it as a very simple association:
You just drew a line between two topics.
Neither does the line have a dedicated meaning nor is it machine-readable.
Click somewhere onto the topicmap to close the in-map-details.

**********************************
A glimpse into the PIM application
**********************************

You can see the difference between a simple drawn line and a qualified association with the following example:
DMX comes with a few predefined topic types that you just selected from:

- person,
- organization,
- note,
- event,
- bookmark.

These topic types all come from classical Personal Information Management (PIM).
As using DMX for PIM is a popular use case, there are also some predefined association types.
One of those association types describes the role a person has in an organization.

We will now give the person an organizational role to see that this is a different association type:
Grab the little dot of the person's rectangle and create an association to the organization.
The in-map details show at once that the association type is "Organization Association".

.. image:: _static/create-organization-association.png

Next, we have to assign the exact role.
Right-click onto the rectangle or directly onto the association.
The context menu opens.
Select "Edit".

.. image:: _static/edit-organization-association.png

The detail panel opens.
You can now select an "Organizational Role" from a predefined set of possible values, e.g. "Member".

.. image:: _static/select-role.png

Click "Save".

.. image:: _static/organization-association.png

This is a machine-readable association:
You can open the context menu of the organization with a right-click and select "What's related".
If you have entered more than one person, you can scroll down in the detail panel to see all persons and their respective roles in the organization.

.. image:: _static/organizational-roles.png

In DMX you are free to define your own topic types and association types.
Find out more about it in the section about :ref:`Modelling<user-modelling>`.