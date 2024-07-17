# 1. Project Name
Console Service for Customer Console Access in XYZ Cloud

# 2. Project Overview

The project provides XYZ cloud console access to customer virtual machines through the GUI and API.  The user can choose
between a graphical (VNC) console, or a serial (TTY) console depending on their needs.

The graphical and serial consoles can be configured within the boot image, with the boot-loader and the kernel boot
commands.  From there you can configure the kernel console and the console devices in use.

A console is provided through a standard websocket hosted by the regional API.

### GUI

From the UI of an instance, the user will select a Console task that delivers the HTML5-based console natively in their
web browser.  The user will choose the console type (VNC vs Serial) which will result in a new window with the
connected console.

Should the console connection fail (inactive timeout or other reason), an error messages with a Reconnect option will
be provided allowing the user to easily reconnect the console.

### API

The regional API will provide the websocket to the instance that is secured via an access_token query parameter, as 
well as POST URL to generate the access token and select the console configuration.

When errors occur establishing the connection, the standard API error reporting mechanisms will be used.  It is
possible for the websocket to disconnect in which case the user will need to reload the console.

### Console Lifecycle

The console will not live forever.  Indeed, the console connection may drop at any time, but can always be
re-established assuming the VSI is currently scheduled.  The console may drop due to:

- Power off of the VSI.  When the VSI is no longer scheduled, the compute agent will drop the console stunnel. The
user cannot re-establish the connection until the VSI is started again.
- A connection idle for longer than 10 minutes.  An idle connection is defined as no data passing into the console.
This setting is not configurable.
- A connection existing for longer than 60 minutes.  After 60 minutes a session will timeout regardless of activity.
This setting is not configurable.
- The VSI migrating to another host during compute node maintenance.

It should be noted that VSI reboot/reset, or any other VSI operation that doesn't result in a re-schedule of the VSI
will maintain the console.
