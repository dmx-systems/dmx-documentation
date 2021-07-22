.. _installation:

############
Installation
############

DMX is a web application. It comes with a web server and database included. The *DMX Webclient* acts as the user interface. It runs in a regular web browser.

The default DMX installation is a *single-user* installation. Web server and web browser are running on the same machine.

.. hint::

    | DMX requires **Java 8**.
    | DMX is not yet adapted to Java 9 or later.

    | In case you intend a *networked* DMX installation: Java is only required at server side then.
    | A Java browser plugin is *not* required.

*****************************
Installation and first launch
*****************************

Although DMX is a client-server application, installation and launching the DMX Webclient for the first time, is as easy as buttering a toast:

1. `Download <https://dmx.berlin/download/>`_ the distribution zip file from DMX website
2. Unzip it. Result is a folder named ``dmx-5.2``
3. Open the folder and run the respective starter script for your platform:

   .. figure:: _static/starter-scripts.png

   The starter script runs without any user interaction. The DMX server is launched, a lot of information is logged, and finally a browser window opens, presenting the *DMX Webclient*:

   .. figure:: _static/webclient-launch.png

      The pristine DMX Webclient when launched for the 1st time. The actual UI takes the back seat and gives way to your content. At this moment you have none though -- you start with a clean slate.

At this moment you've possibly some questions:

What is installed on my computer?
    DMX does not install any files on your computer. Everything needed exists in the DMX folder, and stays there.

    At 1st run the starter script creates an empty Corporate Memory: a folder ``dmx-db`` now exists inside the DMX folder.

Are any ports open?
    Yes, the DMX web server now listens on port 8080. Your computer is still safe though. The web server comes with a request filter and by default it accepts requests only from localhost.

What is the default DMX installation type?
    There are 2 DMX installation types:

    - *local* -- DMX client and server on *same* machine. Single-user.
    - *networked* -- DMX clients and server on *different* machines. Multi-user.

    | A DMX default installation is a *local* installation.
    | The web server request filter accepts requests only from localhost.
