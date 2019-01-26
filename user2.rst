##############
DMX User Guide
##############

Please note we have a :ref:`glossary-label` for the terms used in this documentation.

.. _user-dmx-user-interface-label:

**********************
The DMX user interface
**********************

The Toolbar
===========

The upper toolbar contains some of the crucial steering tools for DMX.

.. figure:: _static/upper-toolbar.jpg
    :alt: Tool bar with workspace and topic map selector


The Workspace Selector
----------------------

In the upper left corner there is a drop-down menu called :ref:`"Workspace"<glossary-workspace-label>`.
This is the workspace selector.
When you start to work on a blank DMX installation, the only existing workspace is called "DMX".

Topic Map Selector
------------------

Next to the workspace selector there is another drop-down menu called :ref:`"Topicmap"<glossary-topic-map-label>`.
This is the topic map selector.
In the beginning, there is only one topic map, it's called "untitled".

The Login Button
----------------

The "Login" button is located in the upper right corner of the toolbar.


The Topicmap Panel
==================

The main area displaying the currently selected topic map is called topic map panel.



Context menus
-------------

The Detail Panel
================

* In the upper right corner of the toolbar there is a button with an arrow. It is greyed out if nothing is selected. Later you can open the detail panel by pressing it.

    
The Search/Create Dialogue
==========================


*****************
Content authoring
*****************

Creating a topic
================

Right-click into the emptiness.
A window appears that offers you to search something or to create something.

.. image:: _static/search-create.jpg

DMX wants to make sure that you do not create something that already exists.
That's why you enter whatever you want to create into the search field.
Let's assume you want to enter all persons and organizations involved in a project.
Start by entering a given name.
DMX will answer "No match".
Select "Person" from the predefined :ref:`topic types<glossary-topics-and-topic-types-label>` and click "Create".

.. image:: _static/create-person.jpg

You will see a rectangle on your topic map.
It contains the name you entered and it states that this is the person's first name.
Oh no!
You entered the given name and the surname?
Let's correct this:
Long-press the rectangle and select "edit" from the context menu.

.. image:: _static/context-menu-edit.jpg

On the right-hand side there is a :ref:`detail panel<glossary-detail-panel-label>` now.
It contains many fields you might need for all persons.
Correct the name and click the save button at the very bottom.
Add a second topic, e.g. "Organization No. 1".
Select the topic type "institution" this time.

.. image:: _static/person-organization.jpg


        "Included topic types" -> for PIM

Creating an association
=======================

        "Auto typing"

Editing content
===============
        "Edit in detail panel"
        "Inline edit (in Detail panel or in Topicmap Panel)"

Deleting things
===============
        "The difference between Hide and Delete" (info box?)
        "Bulk deletion"


****************
View arrangement
****************

    The Topicmap <-> Database illustration is a good introduction
 
Creating a topicmap
===================

Moving things around
====================

Moving topics
-------------
        
Panning/zooming the topicmap
----------------------------

Hiding things
=============

The difference between Hide and Delete" (same info box?)
--------------------------------------------------------

Bulk operations
===============

Moving topic clusters
---------------------

Hide multiple
-------------


**********
Navigation
**********

Searching the database
======================

Associative navigation (what's related?)
========================================


Switching between topicmaps
===========================

Switching between workspaces
============================

Browser history
===============

*********
Modelling
*********

.. _workspaces-collaboration-acl-label:

*************************
Collaboration and Sharing
*************************

.. _user-creating-user-accounts-label:

Creating user accounts
======================

In DMX, you create user accounts just the way you create everything else, too:
Enter a user name into the search field.
If the name does not exist yet you create it by selecting the topic type "User Account".
After that, a password field appears. 
Only privileged accounts (like admin) can create user accounts.

.. image:: _static/user-account-creation.jpg

.. image:: _static/user-account-password.jpg

What is displayed after account creation is just the *user name*.
The *user account* consists of the user name and the password.
Investigate the newly created user name by revealing "What's related".
The user name is associated with some information:

* disk quota: how much space the user can use on the computer
* if the account owner can share workspaces with others
* if the account owner is allowed to log in at all

It is important that every user account is tied to the "System" workspace (see below). In short, this allows others to read their user name (only the name) to share content.

.. image:: _static/user-name-related.jpg
    :width: 400

.. _user-introduction-to-workspaces-and-sharing-modes-label:

Introduction to workspaces and sharing modes
============================================

In DMX workspaces are the highest level content is organized in.
Read and write permissions are tied to workspaces.
Every topic or association is assigned to exactly *one* workspace.
That is why workspaces are the key to the configuration of access control.
There are five sharing modes:

* **private**: In a private workspace just the owner of the workspace can read and write.
* **confidential**: In a confidential workspace the owner can read and write. Group members can read, but not change anything.
* **collaborative**: A collaborative workspace can be read and edited by the owner and by all group members.
* **public**: A public workspace is world-readable. The default "DMX" workspace is an example of a public workspace.
* **common**: In a common workspace everyone can read and write. No login is required.

Every workspace has an owner, usually the creator, and optional members.
When you are logged in you can access the different workspaces via the drop-down menu in the upper left corner.
Once you log out DMX will switch back to a public (world-readable) workspace like the default workspace called "DMX".
All items that are publicly readable stay visible, the rest disappears from the view.
You are no longer able to edit but you still have a customizable view of the topic map, which means that you can move item and reveal other world-readable items.

.. image:: _static/workspace-selection.jpg

DMX comes with four default workspaces with the following sharing modes:

* **DMX**: This workspace a public, it is the one that is displayed publicly when people come to the site.
* **Private Workspace**: This is the private workspace of the respective logged in user. Only this user can see and and edit their map as it is private.
* **Administration**: Only admins can view and edit this workspace. Unprivileged user accounts do not have this entry in the menu.
* **System**: The System Workspace is readable by everyone who is logged in. It contains all user names that exist in this DMX installation. The user names are readable to all users. This is needed for sharing content with others as you will see below.

.. image:: _static/system-workspace.jpg

.. _user-sharing-a-workspace-label:

Sharing a workspace
===================

Here is how creating a shared workspace works:

* Log in as an unprivileged user and go to your private workspace where you can edit.
* Open the search field and **create a workspace**. Make it a collaborative workspace to give others write permission.

.. image:: _static/workspace-creation.jpg

* The new workspace automatically opens. Click onto the blue information icon next to the workspace selection to reveal the workspace topic itself on the topic map.
* To add members to the workspace you can just enter user names and click them to reveal them on the map. As mentioned above, all user names are visible to all other logged in users via the system workspace. In DMX, membership is tied to user names that is why you need read permission on the user names.
* If you don't know their user names you can go to the System workspace and investigate.
* Now that you have the workspace itself and a user name on your topic map you can just **drag the user name onto the workspace** to create an association between them.
* Next you have to qualify this association as a membership: Edit the association.

.. image:: _static/edit-ws-assoc.jpg
    :width: 300

* In the detail panel you can now select the association type "Membership". You are done!

.. image:: _static/edit-ws-assoc2.png

* The user you shared your workspace with can now log in, select your collaborative workspace and add something, e.g. a note. It will automatically appear in the workspace, visible to all workspace members.

.. note:: You can create memberships in every workspace that you have write permission in.

