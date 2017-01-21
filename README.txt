--------------------------------------
USB Keyboard Driver: LED functionality
--------------------------------------

Usage:
With root privileges:
a) make clean
b) make
c) insmod usbkbd.ko

In KVM/VirtualBox/VMWare, add the USB Keyboard device. That is, assign the
'Host Device' to the Guest. Then, we need to discover the USB number of this
device and write a small shell script that unbinds the device from USBHID and
binds to USBKBD. This needs to be done manually as the USB number is allocated
dynamically and not known apriori.

-------------
INITIAL STATE
-------------
The initial state of the USB keyboard will be the Num lock off and Caps lock off.
This should be the state before we unbind the USBHID driver and bind the USBKBD
driver.

-----------
TRANSITIONS
-----------
Initially, we are in Mode 1. Whenever Num lock is pressed, there is a possibility
of a mode change. If CAPS is off and NUM lock is pressed, CAPS led is turned on
and we are in Mode 2. In Mode 2, whenever NUM lock is pressed, CAPS led is toggled
and we come back to Mode 1. On a switch from Mode 2 to Mode 2, the expected 
behavior is preserved. The new functionality is mostly implemented inside the
usb_kbd_event function, from lines 185 to 205, and the addition of a new boolean
value called mode inside struct usb_kbd.