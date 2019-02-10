.. _installation:

############
Installation
############

DMX runs on several different operating systems.
Below you can find how to get started on :ref:`Windows <installation-windows>`, :ref:`Mac OS <installation-macos>` and :ref:`Linux <installation-linux>`.

.. _installation-windows:

**********************
Running DMX on Windows
**********************

Install a Java Runtime Environment
==================================

Go to `Oracle's website <https://java.com/en/download/>`_ and download the recommended Java version.
Currently, this is Java 8.
Any version from Java 6 to 8 will work out of the box with DMX.
Execute the JavaSetup*.exe with a double-click and click through the installer.

Use a suitable web browser
==========================

The browser Microsoft Edge is *not* able to run Java applications like DMX.
Thus you have to use a different browser, e.g. Firefox, or Google Chrome.

Download DMX
============

Currently we recommend you to download the `beta-1 release <https://download.dmx.systems/dmx-5.0-beta-1.zip>`_.
To try out the latest state of development you can download the `latest snapshot <https://download.dmx.systems/ci/dmx-latest.zip>`_ instead.
All previous snapshots are available for download on https://download.dmx.systems/ci/.

Save the according file to a location of your choice.
Extract the zip archive.

.. image:: _static/windows-extract-zip.png

.. image:: _static/windows-choose-destination.png

A new folder is created.
It has the name of the zip file.

Start DMX
=========

Enter the folder that was created when you unpacked the zip archive.
Double-click the file ``deepamehta-windows.bat``.
If your Explorer does not display file extensions, you only see ``deepamehta-windows`` as a file name.
That is the same file.

A black terminal window opens to display what is happening.
Just leave it there.
The Windows Defender Firewall might ask you to allow Java access.
Choose "Private networks, such as my home or work network".

Your default browser opens the DMX webclient at http://localhost:8080/systems.dmx.webclient/.
You are ready to go.

.. hint:: Log in as "admin" without a password.

Stop DMX
========

To stop DMX, bring the black terminal windows to the foreground.
Press CTRL + C.
In the window you will now see the question "Terminate batch job (Y/N)?"
Answer with a "Y" for yes.
DMX is now stopped.

Remove DMX
==========

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-1.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-macos:

*********************
Running DMX on Mac OS
*********************

Download DMX
============

Currently we recommend you to download the `beta-1 release <https://download.dmx.systems/dmx-5.0-beta-1.zip>`_.
To try out the latest state of development you can download the `latest snapshot <https://download.dmx.systems/ci/dmx-latest.zip>`_ instead.
All previous snapshots are available for download on https://download.dmx.systems/ci/.

Save the according file to a location of your choice.
Extract the zip archive.
A new folder is created.
It has the name of the zip file.

Start DMX
=========

Enter the folder that was created when you unpacked the zip archive.
Double-click the file ``deepamehta-macosx.command``.
A terminal window opens to display what is happening.
Just leave it there.

Your default browser opens the DMX webclient at http://localhost:8080/systems.dmx.webclient/.
You are ready to go.

.. hint:: Log in as "admin" without a password.

Stop DMX
========

To stop DMX, press CTRL + C in the terminal window.

Remove DMX
==========

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-1.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-linux:

********************
Running DMX on Linux
********************

On a Linux machine, you have two options to run DMX:
You can start it from an unpacked zip file *or* you can install it from our APT repository.

Install a Java Runtime Environment
==================================

Use your package manager to install the default Java Runtime Environment for your Linux distribution.

For example, on Debian / Ubuntu, you would run the command

.. code:: bash

    sudo apt install default-jre

to install OpenJDK/JRE. On CentOS / Fedora, this would be

.. code:: bash

    sudo yum install java-<version>-openjdk

.. _installation-linux-zip:

Run DMX from a zip archive
==========================

If you choose to run DMX from a zip archive you do not install any other packages on your computer.
The binaries and your database are all right in the DMX folder.

Download DMX
------------

Currently we recommend you to download the `beta-1 release <https://download.dmx.systems/dmx-5.0-beta-1.zip>`_.
To try out the latest state of development you can download the `latest snapshot <https://download.dmx.systems/ci/dmx-latest.zip>`_ instead.
All previous snapshots are available for download on https://download.dmx.systems/ci/.

Save the according file to a location of your choice, e.g. your "Downloads" folder.
Open a terminal and go to that location.
Extract the zip archive.

.. code:: bash

    ~ $ cd Downloads
    # to get the beta-1 release:
    ~/Downloads $ wget https://download.dmx.systems/dmx-5.0-beta-1.zip
    ~/Downloads $ unzip dmx-5.0-beta-1.zip
    # to get the latest nightly build:
    ~/Downloads $ wget https://download.dmx.systems/ci/dmx-latest.zip
    ~/Downloads $ unzip dmx-latest.zip

Start DMX
---------

Go into the newly created folder (in this example "dmx-5.0-beta-1") and run the start script ``./deepamehta-linux.sh``.

.. code:: bash

    ~/Downloads $ cd dmx-5.0-beta-1
    ~/Downloads/cd dmx-5.0-beta-1 $ ./deepamehta-linux.sh

.. hint:: Log in as "admin" without a password.

Stop DMX
--------

To stop DMX, press CTRL + C in the terminal you started it in.

Remove DMX
----------

You can get rid of DMX by deleting the whole folder that got created when you unpacked the zip file, e.g. dmx-5.0-beta-1.
The Java Runtime Environment has to be uninstalled separately.

.. _installation-linux-apt:

Install DMX from our APT repository
===================================

For apt-based Linux distributions (like Debian / Ubuntu) we provide a repository.
It is intended for installations on servers but can obviously be used on Debian/Ubuntu laptops as well.
The package has a built-in dependency to default-jre.

.. warning:: If your Java Runtime Environment is newer than Java 9, you cannot use the repository for the moment. Check your version by running the command ``java -version``.

Here is how to add the repository:

.. code:: bash

    $ sudo add-apt-repository "deb https://download.dmx.systems/repos/ubuntu/ xenial/"
    $ sudo apt update
    $ sudo apt install deepamehta

.. note:: The package will be renamed to DMX. DeepaMehta is DMX's predecessor's name.

Configure DMX
-------------

For server installations and advanced configuration we will soon provide instructions in the :ref:`Admin Documentation <admin>`.
By default, DMX will listen on port 8080.
You can set the admin passphrase in ``/etc/deepamehta/deepamehta.conf``.

.. hint:: Log in as "admin" with the default passphrase "YOUR_SECRET_PASSWORD_HERE".

Start DMX
---------

When installed from the repo, DMX is not started automatically after installation.
Enable it by changing the following line in the file ``/etc/default/deepamehta``:

.. code::

    START_DEEPAMEHTA=yes

Invoke this command to start the daemon:

.. code::

    /etc/init.d/deepamehta start

Stop DMX
--------

Stop the daemon by running

.. code::

    /etc/init.d/deepamehta stop
