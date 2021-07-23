.. _installation:

############
Installation
############

**********************
DMX installation types
**********************

DMX is a web application. The **DMX Server** is a (Jetty based) web server. The **DMX Webclient** acts as the user interface. It runs in a regular web browser.

The DMX server is written in Java. It can be installed on any platform which supports Java, in particular Windows, macOS, and Linux.

There are 2 DMX installation types:

- *local* -- DMX client and server run on *same* machine. Single-user.
- *networked* -- DMX clients and server run on *different* machines. Multi-user.

A default DMX installation is a *local* installation.

The DMX server opens port ``8080``. Your computer is still safe though. The DMX server comes with a request filter and by default it accepts requests only from localhost.

.. warning::

    | DMX requires **Java 8**.
    | DMX is not yet adapted to Java 9 or later.

    | In case you intend a *networked* DMX installation: Java is only required at server side then.
    | A Java browser plugin is *not* required.

.. hint::

    **Linux users**: if your Linux distribution is apt-based (like Debian/Ubuntu) you can install DMX via apt. Skip to section :ref:`installation-linux-apt`.

    Alternatively you can run the generic zip based DMX installation. Just read on.

*****************************
Installation and first launch
*****************************

Although DMX is a client-server application, installation and first launch is as easy as buttering a toast:

1. Download `dmx-5.2.zip <https://download.dmx.systems/dmx-5.2.zip>`_
2. Unzip it. Result is a folder ``dmx-5.2``
3. Open the DMX folder and run the starter script suitable for your platform:

   .. figure:: _static/starter-scripts.png

   The starter script runs without user interaction. The DMX server is launched, a lot of information is logged in a terminal window, and finally a browser window opens, presenting the *DMX Webclient*:

   .. figure:: _static/webclient-launch.png

      A pristine DMX Webclient when launched for the 1st time. The actual UI takes the back seat and gives way to your content. At this moment you have none though -- you start with a clean slate.

   At 1st run the starter script creates an empty Corporate Memory: a folder ``dmx-db`` now exists inside the DMX folder.

DMX does not install any files on your computer. Everything needed, including the database, exists in the DMX folder, and stays there.

***********************
Starting the DMX Server
***********************

In case this is your first launch the DMX server is started already.
Otherwise start it exactly like first launch:

Open the DMX folder and run the starter script suitable for your platform:

.. figure:: _static/starter-scripts.png

The starter script runs without user interaction. While the DMX server is launched a lot of information is logged in a terminal window. Finally a browser window opens, presenting the *DMX Webclient*.

***********************
Stopping the DMX Server
***********************

To shutdown the DMX server, in the DMX terminal window enter ``stop 0`` and press ``Return``. While shutting down a lot of information is logged.

*************************
Opening the DMX Webclient
*************************

To open the DMX Webclient point your browser to `http://localhost:8080/systems.dmx.webclient/`.
The DMX Server must be running.

.. _installation-linux-apt:

************************************
Installation from our APT repository
************************************

For apt-based Linux distributions (like Debian/Ubuntu) we provide a repository.
It is intended for installations on servers but can obviously be used on Debian/Ubuntu laptops as well.
The package has a built-in dependency to ``default-jre``.

Here is how to download and execute our install script.
It adds the repository, installs DMX from it, prompts the user for the DMX admin passphrase and starts DMX as a daemon:

.. code:: bash

    $ curl -sS https://download.dmx.systems/repos/dmx-install.sh | sudo bash

Starting the DMX Server
=======================

Invoke this command to start the daemon:

.. code::

    systemctl start dmx

Stopping the DMX Server
=======================

Stop the daemon by running:

.. code::

    systemctl stop dmx
