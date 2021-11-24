In order to use HiveAR, first download the application from the [GitHub](https://github.com/SwarmUS/HiveAR) page and install accordingly to the instructions in the same page.

## Connect the Android Device to a physical HiveBoard

### Wire Things Up

To connect the HiveAR app running on an Android device to a HiveBoard, **you must first disconnect the Ethernet cable from the HiveBoard**. Then, plug the micro-USB cable from the HiveBoard to the Android Device as shown in the image below.

Also, make sure that the DIP switches for the WROOM ESP-32 module are at the correct position to enable Wi-Fi capabilities. Refer to the [HiveBoard UI page](../../hardware/HiveBoard/hiveboard-ui.md#dip-switches) for more on this.

!!! hint 
    **Why must the Ethernet cable be disconnected?**

    A HiveBoard can only be connected to one host at a time (be it a robot runnung [HiveMindBridge](https://github.com/SwarmUS/HiveMindBridge), usually connected vie Ethernet, or an Android device running HiveAR). If the HiveBoard and the robot's embedded computer already have a connection established, it will be impossible to establish a second connection to the Android device. Thus, we advise that you unplug the Ethernet cable prior to using HiveAR with a given HiveBoard.

![Connecting a HiveBoard to the HiveAR app](img/hiveboard-connect-hivear.png)

### Establish the connection between HiveAR and HiveMind via USB

Launch the HiveAR application on the Android device. Accept the different permission requests.

Navigate to the Connection tab (first icon on the left at the base of the screen).

<figure markdown>
![Connection Page](img/hivear-connection-page.png){: style="height:700px" }
</figure>

Click on the circular button containing two arrows pointing at each other in the lower left side of the screen. A selection bar will appear at the top of the screen:

<figure markdown>
![Connection Page](img/hivear-connection-page-selection-bar.png){: style="height:700px;align:left" }
</figure>

Tap on the bar and select option `HiveBoard: HiveMind`. Then tap Connect. The logs (under `Incoming Data`) should show that the greet messages have been exchanged and you should see a green dot appear near the Connection button at the lower left of the screen. HiveAR and HiveMind are now connected.

<figure markdown>
![Connection Page](img/hivear-connection-page-connected.png){: style="height:700px;align:left" }
</figure>

## Connect the Android Device to a Simulated HiveBoard

The HiveMind firmware can be run on a Linux computer to simulate a HiveBoard. Connection to the HiveAR application is then done using TCP/IP.

As the HiveMind running on a HiveBoard is considered client, the Android device running HiveAR would be server. Different implementation can be done to achieve this. 
An example can be found in the [SwarmUS-ROS project](https://github.com/SwarmUS/SwarmUS-ROS), where an Android device can be associated to a simulated HiveBoard running in a ROS node. 
The launch file associated to this implementation can be found [here](https://github.com/SwarmUS/SwarmUS-ROS/blob/bab1924ca7b4c97093df48d5c54c7470b45b5155/src/swarmus_ros_simulation/launch/teleop_from_phone_multi_robot.launch#L54).

Having the Android device being the server, the client must know what IP address and port to connect to: this is found in the connection tab when switching to remote mode.
There, the current network IP adress of the Android device is show and it is possible to choose which port to listen for incoming connection.

To have the remote connection view, click on the round button beside button "Disconnect". In remote view, a signal bar icon is displayed on that round button.

<figure markdown>
  ![Connection not connected](img/connection_tab_not_connected.jpg){: style="height:700px"}

  <figcaption>Remote connection view</figcaption>

</figure>

After port is set, press connect to start listening on that port for incoming connection whenever the client is trying to connect. 

!!! Attention
    When listening for an incoming connection in remote mode, HiveAR is listening for an incoming connection for 10 seconds. Just click on connect button again if desired.

## Connection Status
As the Android device must be connected either by USB or TCP to communicate to the swarm, it is possible to keep track of the state of the connection with the swarm.

A small coloured dot has been add to the connection tab icon and can be observed as followed:
The dot can have four states:

- Red: Android device not connected
- Blue: Android device trying to establish connection with HiveMind
- Yellow: Android device connected to HiveMind, but HiveAR is not registered as part of the swarm
- Green: Android device is connected to HiveMind **And** HiveAR registered as part of the swarm

<figure markdown>
  ![Connection not connected](img/connection_tab_not_connected.jpg){: style="height:700px"}

  <figcaption>Not connected status indicator</figcaption>

</figure>
<figure markdown>
  ![Connection not connected](img/connection_tab_connecting.jpg){: style="height:700px"}

  <figcaption>Connecting to HiveMind status indicator</figcaption>

</figure>
<figure markdown>
  ![Connection not connected](img/connection_tab_connected_no_swarm.jpg){: style="height:700px"}

  <figcaption>Connected, HiveAR not registered in swarm</figcaption>

</figure>
<figure markdown>
  ![Connection not connected](img/connection_tab_connected_logs.jpg){: style="height:700px"}

  <figcaption>Connected to swarm, with logs</figcaption>

</figure>

!!! Attention
    Always make sure connection with the swarm is established and working correctly (green round icon) before trying to use any swarm features. 
    
    In the case where there is a problem with the connection, one can always return to the connection page to try to establish the connection again.
