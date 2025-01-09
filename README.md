# Unreal-Reaper OSC connection
This project serves to create an OSC connection between Unreal and Reaper. For this purpose, blueprints were created and settings were made to Reaper, which are explained below.


## Software Requirements
This project was created using:
- Unreal Engine 5.3.2
- Reaper V7.26 with the IEM Plugins v1.3.0


## Project structure

### Unreal Engine

A separate OSC client has to be created for each receiving OSC server. For a localhost connection, only the port has to be set accordingly to the server. This client then serves as a target for the functions that send the data (see image). For a connection via the network, the IP address of the PC must also be entered. Due to the high data rate when sending the head rotations, the OSCclient is restarted regularly to prevent an overload.

<p float="center">
  <img src="/Doc/Unreal_OSCClient.png" width="500" />
</p>

#### Send headrotations

When called, this function receives an OSCClient as a target and three OSC addresses, one for each rotation variable. The addresses must match the addresses expected by the IEM plugin, as this is used to assign the variables. The corresponding addresses can be found in the [IEM documentation](https://plugins.iem.at/docs/osc/).
Furthermore, the camera rotations are converted into yaw, pitch and roll angle and the individual values are appended as argument to the corresponding address (see image).

<p float="center">
  <img src="/Doc/Unreal_send_coordinat.png" width="500" />
</p>

Unreal and reaper use [different coordinate systems](https://techarthub.com/a-practical-guide-to-unreal-engines-coordinate-system/). Unreal a left-handed one and reaper a right-handed one, both with the Z vector upwards. In addition, the default viewing direction in unreal is in the negative X direction. Therefore, the y-axis has not to be flipped and only the yaw angle is rotated by 180 and normalized to -180 to 180 again (see image).

<p float="center">
  <img src="/Doc/Unreal_yaw_rotation.png" width="500" />
</p>

#### Send track

When called, the send track function receives an OSC client as a target, as well as a string containing the respective button pressed and an OSC address. In the function, the button is then appended to the address as an argument. Only the address is important for control in Reaper itself and must be unique.

<p float="center">
  <img src="/Doc/Unreal_call_send_track.png" width="500" />
  <img src="/Doc/Unreal_send_track.png" width="500" />
</p>

### Reaper setup

#### OSC Server

To receive OSC messages in Reaper, go to *Options->Preferences->Control/OSC/web* and click on Add. Select OSC as Control surface mode, enter a device name, select Local port mode and enter the local port and the IP of the OSC client of Unreal that you want to listen to.

#### Actions

To be able to play a desired number of tracks exclusively, create new user-defined actions. To do this, go to *Actions->Show action list->New action*. Drag and drop from left to right, first the Track: Unmute all tracks and then the action Track: Toggle mute for track X for every track except the ones you want to be played (see image).

<p float="center">
  <img src="/Doc/Reaper_custom_action.png" width="500" />
</p>

Then click *add shortcut* and send the OSCMessage. This is now recognized by reaper as a “hotkey” for the action.

#### IEM plugin

In the plugin that you want to control, click on the two circles and OSC at the bottom left. Under listen to port, enter the port of the corresponding OSC client from Unreal and click on open. For a non-local connection, enter the Ip address of the PC here as well.

## Run the project 

Clone the repo into the Plugins folder of your project. If there isn't one yet, simply create one directly in the project folder. In Unreal go to *edit->plugins* and search for OSC_rotations. Set the check mark and restart your project. Place the VRPawn from the content Bowser in your scene/on your camera.
To use the plugin with your own UI, copy the event graph from the TestUI into yours and adjust the On Pressed events.

## Troubleshooting

## Contact
Robert Schmitt, B.Sc. 
Institute for Hearing Technology and Acoustics
robert.josef.schmitt@rwth-aachen.de
