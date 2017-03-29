###############
System overview
###############

Almost all applications on Red Pitaya are made of two parts. We call them frontend and backend. You can see them on 
the picture below.

.. image:: Common.png

Everything that works in your browser and what you can see is the frontend. This is the part that can visualise 
data on the screen or change some parameters to adjust settings inside your applications. Everything else that is related
to the hardware on Red Pitaya's board is called backend. You can't see this application directly but this is the most 
important part of the application, which can help you to control the hardware. The backend has the ability to work with Digital PINS, 
control LEDs on the board, load an FPGA image, work with fast inputs and outputs and lots of other things.
Frontend and backend require communication between each other. This is mostly done with Red Pitaya network APIs which 
are technically based on extended websocket connections. When you're writing your application you don't need to think 
about communication and data transfer. Our network APIs take care of the data transfer. All you need is simply follow 
some rules. You can read about this rules in How to 
:ref:`add a button to control LED <ABCLED>`.

********
Frontend
********

.. image:: Frontend-1.png

The frontend is that thing that you can see on your screen. We prefer to use modern technologies for creating visually appealing applications with lots of possibilities. Thus, we use HTML5 for layout, CSS3 for element styles and JavaScript for
creating fast and reliable web applications. Using these technologies makes it easier to create innovative applications.

.. image:: Frontend-2.png

The basic idea of the frontend is to visualize data coming from the Red Pitaya. And this should be it! You don't need to do lots of 
calculations inside UI. Let your Red Pitaya do the heavy calculations. So here is typical workflow of an application:

    - User changes some settings in Web UI of the application
    - Web UI may apply them immediately on the screen or
    - Web UI may send them to the controller (= Backend) for some specific calculations on the device, for changing the device state or for 
      something else
    - Controller applies them to internal variabales and changes the device state (if necessary)
    - Controller does some calculations 
    - Controller sends parameters and signals to WebUI in JSON format
    - Web UI recieves these parameters and signals and then applies them on the screen
    
*******
Backend
*******

In general the backend is your Red Pitaya. But when we're talking about your application the backend is the controller of your
application. The controller is a shared library with a .so extension. It operates with specific members which are 
called Parameters and Signals. Former are needed for handling the state of important variables of your app. 
Latter are needed for collecting data inside a container. You can use lots of them at the same time. 
None of them are necessary, so if you don't need singlas in your application you may not use them.

  .. image:: Backend.png
  
The operating system is based on Nginx as a fast platform for Web applications. Nginx allows us to load modules during runtime without 
restarting system.

Here is typical workflow of executing application:

    - Nginx always works as a web server for providing Web UI.
    - When you click on your application in main menu Nginx will proceed with this steps:
        - It opens your application user interface
        - It loads specified FPGA image using APIs. If there wasn't any image specified it leaves current image. Make sure that you're using the correct image when you're developing your own application (## how do I know this##)
        - It loads the controller of your application
        - When the controller is loaded it starts a WebSocket connection. Also, it notifies the Web-UI that the application is loaded. This means that JavaScript code can establish a WebSocket connection
        - During the execution of the application the Web-UI (using JavaScript) and the Controller (C-code/shared library) can exchange data in JSON format
        - The controller can request data from RedPitaya APIs, i.e. receive signals coming from input or send signals to output devices.
        - APIs can manipulate data inside FPGA
