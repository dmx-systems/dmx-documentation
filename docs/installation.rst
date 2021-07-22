.. _installation:

############
Installation
############

DMX is a web application. The **DMX Server** is a (Jetty based) web server. The **DMX Webclient** acts as the user interface. It runs in a regular web browser.

The DMX server is written in Java. It can be installed on any platform which supports Java, in particular Windows, macOS, and Linux.

There are 2 DMX installation types:

- *local* -- DMX client and server run on *same* machine. Single-user.
- *networked* -- DMX clients and server run on *different* machines. Multi-user.

A default DMX installation is a *local* installation.

The DMX server opens port ``8080``. Your computer is still safe though. The DMX server comes with a request filter and by default it accepts requests only from localhost.

.. hint::

    | DMX requires **Java 8**.
    | DMX is not yet adapted to Java 9 or later.

    | In case you intend a *networked* DMX installation: Java is only required at server side then.
    | A Java browser plugin is *not* required.

*****************************
Installation and first launch
*****************************

Although DMX is a client-server application, installation and first launch of the DMX Webclient, is as easy as buttering a toast:

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
