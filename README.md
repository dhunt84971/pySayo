# Capture Sayo Device keystrokes.
This example program captures keystrokes from a Sayo 2x6 keypad and then executes a shell script that uses xdotool to send keystrokes to the object with focus.  It is intended to provide a complete example of how this is done on Xubuntu 22.04 including the necessary permissions and XServer modifications required to both access the device and prevent its default keyboard interface from being processed by the OS.  URLs are listed for websites that provided information that was used to create the instructions below.  These instructions pertain specifically to the use of a 2x6 SayoDevice on Xubuntu 22.04 using python and xdotool.

# SAYO Device
A Sayo  Device is a small HID USB keyboard used to execute macros.

## Steps to Setting Up
### Kernel
On Ubunutu, plug the device into the computer then run:
```
cat /var/log/kern.log
```
This will show if the device has been recognized by the system.

### LSUSB
Use the lsusb command to list the USB devices and get the Bus and ID numbers.
```
lsusb -v
```

### USBHID
Use the usbhid-dump command to capture the keypresses.
```
sudo usbhid-dump -s 3:9 -f -e stream
```
NOTE: In the above example, 3 is the BUS number and 9 is the ID.

### Get X Server to Ignore Device
Use the following command to get the device name:
```
DISPLAY:=0.0 xinput list
```
Then execute this script to ignore the input from the device:
```
DISPLAY=:0.0 xinput disable keyboard:"SayoDevice SayoDevice 2x6F RGB Keyboard"
DISPLAY=:0.0 xinput disable keyboard:"SayoDevice SayoDevice 2x6F RGB"
```

### Python
Install the evdev package.
```
pip3 install evdev
```

### Grant User Permissions to /dev/input
```
sudo groupadd uinput
sudo usermod -a -G uinput $USER
echo 'KERNEL=="uinput", GROUP="uinput", MODE:="0660", OPTIONS+="static_node=uinput"' | sudo tee -a /etc/udev/rules.d/99-uinput.rules > /dev/null
```
Log Out and back in for the permissions to take effect.

Run the evdev dump tool to get the input device:
```
python3 -m evdev.evtest
```
Select the event and then press the keys to get the code and value for each key.

## References
https://cgarethc.medium.com/adding-a-two-button-keyboard-to-my-up-cycled-raspberry-pi-photo-frame-ffda59fb979b

https://hackage.haskell.org/package/evdev

https://unix.stackexchange.com/questions/242222/read-from-dev-input-devices-without-root-privileges

https://github.com/Sayobot (DOES NOT WORK)


# Sayo 2x6
This device has the following key codes:

| | |
| :-: | :-: |
| 7 | 48 |
| 6 | 30 |
| 5 | 11 |
| 4 | 10 |
| 3 | 9  |
| 2 | 8  |

This devices has the following values:
| Value | Description |
| --- | --- |
| 1 | Pressed |
| 0 | Released |
| 2 | Held Down |
