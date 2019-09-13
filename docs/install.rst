.. _installation:

############
Installation
############

DMX is built on the Java Platform which can be installed on many operating systems.
On this page we have covered how to run DMX on :ref:`Windows <installation-windows>`, :ref:`Mac OS <installation-macos>` and :ref:`Linux <installation-linux>`.

.. _installation-windows:

**********************
Running DMX on Windows
**********************

Install Java
============

DMX runs out of the box with Java versions 6 to 8.
To run DMX we recommend you to download **Java 8**.
If Java 8 is not already installed you can download it from the `AdoptOpenJDK <https://adoptopenjdk.net/>`_ or `Oracle's Java <https://java.com/en/download/>`_ website.
Double-click the downloaded file and click through the installer.

Download DMX
============

The latest stable release is the `DMX 5.0-beta-5 release <https://download.dmx.systems/dmx-5.0-beta-5.zip>`_.
Previous versions of DMX are available for download on https://download.dmx.systems/.

Save the according file to a location of your choice.
Extract the ``.zip`` archive.

.. image:: _static/windows-extract-zip.png

.. image:: _static/windows-choose-destination.png

A new folder is created and the folder has the name of the ``.zip`` file.
This is your *DMX Home* folder and you can now start DMX.

Start DMX
=========

Enter the folder that was created when you unpacked the zip archive.
Double-click the file ``dmx-windows.bat``.
If your Explorer does not display file extensions, you only see ``dmx-windows`` as a file name.
That is the same file.

A black terminal window opens to display what is happening.
Just leave it there.
The Windows Defender Firewall might ask you to allow Java access.
Choose "Private networks, such as my home or work network".

Your default browser opens the DMX webclient at ``http://localhost:8080/systems.dmx.webclient/``.
The path to the web client ``systems.dmx.webclient/`` is mandatory.
Without it your browser cannot find the DMX web client.
You are ready to go.

.. hint:: Log in as "admin" without a password.

Stop DMX
========

To stop DMX, bring the black terminal windows to the foreground.
Type `stop 0` into the terminal and press ENTER.
DMX is shut down.

Remove DMX
==========

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-5.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-macos:

*********************
Running DMX on Mac OS
*********************

On most current Mac OS versions Java 8 is already installed.
If Java 8 is not installed you can download it from the `AdoptOpenJDK <https://adoptopenjdk.net/>`_ or `Oracle's Java <https://java.com/en/download/>`_ website.
Double-click the downloaded file and click through the installer.

Download DMX
============

The latest stable release is the `DMX 5.0-beta-5 release <https://download.dmx.systems/dmx-5.0-beta-5.zip>`_. Previous versions of DMX are available for download on https://download.dmx.systems/.

Save the according file to a location of your choice.
Extract the ``.zip`` archive.

A new folder is created.
It has the name of the ``.zip`` file.
This is your *DMX Home* folder and you can now start DMX.

Start DMX
=========

Enter the folder that was created when you unpacked the zip archive.
Double-click the file ``dmx-macosx.command``.
A terminal window opens to display what is happening.
Just leave it there.

Your default browser opens the DMX webclient at ``http://localhost:8080/systems.dmx.webclient/``.
The path to the web client ``systems.dmx.webclient/`` is mandatory.
Without it your browser cannot find the DMX web client.
You are ready to go.

.. hint:: Log in as "admin" without a password.

Stop DMX
========
To stop DMX, type `stop 0` into the terminal window and press ENTER.
DMX is shut down.

Remove DMX
==========

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-5.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-linux:

********************
Running DMX on Linux
********************

On a Linux machine, you have two options to run DMX:
You can start it from an unpacked zip file *or* you can install it from a package.

Install Java
============

Use your package manager to install the Java 8 for your Linux distribution.

For example, on Debian / Ubuntu, you would run the command

.. code:: bash

    sudo apt install openjdk-8-jre

to install OpenJDK/JRE. Make sure that Java 8 (not 11) is used by running

.. code:: bash

    sudo update-alternatives --config java

and selecting the right version interactively.

.. _installation-linux-zip:

Run DMX from a zip archive
==========================

Download DMX
------------

Currently we recommend you to download the `beta-5 release <https://download.dmx.systems/dmx-5.0-beta-5.zip>`_.
To try out the latest state of development you can download the `latest snapshot <https://download.dmx.systems/ci/dmx-latest.zip>`_ instead.
All previous snapshots are available for download on https://download.dmx.systems/ci/.

Save the according file to a location of your choice, e.g. your "Downloads" folder.
Open a terminal and go to that location.
Extract the zip archive.

.. code:: bash

    ~ $ cd Downloads
    # to get the beta-5 release:
    ~/Downloads $ wget https://download.dmx.systems/dmx-5.0-beta-5.zip
    ~/Downloads $ unzip dmx-5.0-beta-5.zip

Start DMX
---------

Go into the newly created folder (in this example "dmx-5.0-beta-5") and run the start script ``./dmx-linux.sh``.

.. code:: bash

    ~/Downloads $ cd dmx-5.0-beta-5
    ~/Downloads/cd dmx-5.0-beta-5 $ ./dmx-linux.sh

Your default browser opens the DMX webclient at ``http://localhost:8080/systems.dmx.webclient/``.
The path to the web client ``systems.dmx.webclient/`` is mandatory.
Without it your browser cannot find the DMX web client.
You are ready to go.

.. hint:: Log in as "admin" without a password.

Stop DMX
--------

To stop DMX, press CTRL + C in the terminal you started it in.
DMX is shut down.

Remove DMX
----------

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-5.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-linux-apt:

Install DMX from our APT repository
===================================

For apt-based Linux distributions (like Debian / Ubuntu) we provide a repository.
It is intended for installations on servers but can obviously be used on Debian/Ubuntu laptops as well.
The package has a built-in dependency to ``default-jre``.

.. warning:: If your Java Runtime Environment is newer than Java 8, you cannot use the repository for the moment. Check your version by running the command ``java -version``.

Here is how to download and execute our install script.
It adds the repository, installs DMX from it, prompts the user for the DMX admin passphrase and starts DMX as a daemon.

.. code:: bash

    $ curl -sS https://download.dmx.systems/repos/dmx-install.sh | sudo bash

Configure DMX
-------------

For server installations and advanced configuration we will soon provide more instructions in the :ref:`Admin Documentation <admin>`.
By default, DMX listens on port 8080.
The main configuration file is located in ``/etc/dmx/config.properties``.

Start DMX
---------

Invoke this command to start the daemon:

.. code::

    systemctl start dmx

Stop DMX
--------

Stop the daemon by running

.. code::

    systemctl stop dmx
