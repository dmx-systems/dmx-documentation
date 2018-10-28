############
Installation
############

.. _installation-label:

************
Requirements
************

You first need to install a Java Runtime Environment on your computer. Any version from 6 to 9 will work.

.. warning:: Java 10 is not supported, yet.

.. _installation-zip-label:

Install DMX from a zip archive
==============================

Download
--------

You can download DMX in a zip file and run the installer manually. This is suitable for installations on laptops and desktops, or on servers that do not have an apt-based package system.

Go to our website (LINK) and download our latest stable release or our unstable (!) development version. Save the .zip file to a location of your choice and unpack it. A new folder is created. Enter it.

Windows
^^^^^^^
Double-click the ``deepamehta-windows.bat`` file.

Mac
^^^
Double-click the ``deepamehta-macosx.command`` file.

Linux
^^^^^
Execute the ``deepamehta-linux.sh`` file.

Visit the web interface
^^^^^^^^^^^^^^^^^^^^^^^^

Open your browser and go to http://localhost:8080/de.deepamehta.webclient/. There is a login button in the upper right corner of the screen.

.. hint:: Log in as "admin" without a password.

.. _installation-apt-label:

Install DMX from our APT repository
-----------------------------------

.. warning:: The repository currently contains only the unstable development version DMX 5.x. To try out the stable version, install the software from a zip file as described above.

For apt-based Linux distributions we provide a repository. It is intended for installations on servers but can obviously be used on Debian/Ubuntu laptops as well. The package has a built-in dependency to default-jre. If the default Java Runtime Environment of your distribution is newer than Java 9 (like in Ubuntu 18.04), you cannot use the repository for the moment. Here is how to add it:

.. code:: bash

    # add-apt-repository "deb https://download.dmx.systems/repos/ubuntu/ xenial/"  
    # apt update  
    # apt install deepamehta  

Configure DMX
^^^^^^^^^^^^^^
By default, DeepaMehta will listen on port 8080. To change this setting, edit ``/etc/deepamehta/deepamehta.conf``. Adjust the settings in /etc/deepamehta/deepamehta.conf and make sure your computer is protected by a firewall. To secure your installation with iptables see LINK.

To configure the webserver, please see ``/usr/share/doc/deepamehta/examples/deepamehta.apache`` for a sample Apache2 configuration file. For advanced configuration see the `Admin Documentation`_.

.. _Admin Documentation: LINK zu proxy

DMX is not started automatically after installation. Enable it by changing the following line in the file ``/etc/default/deepamehta``:

.. code::

    START_DEEPAMEHTA=yes

Invoke this command to start the daemon:

.. code::

    /etc/init.d/deepamehta start

Uninstall DMX
-------------

When installed from a zip file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you started DMX from a zip file nothing is installed on your computer (except the Java Runtime Enviroment that you installed separately). The DMX database is located in the folder where you started it. You can get rid of it by deleting the whole folder.

When installed from the repo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``dpkg -P deepamehta`` will remove all installed files but not delete your database.

