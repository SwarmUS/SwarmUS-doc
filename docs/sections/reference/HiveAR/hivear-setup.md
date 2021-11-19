In order to use HiveAR, first download the application from the [GitHub](https://github.com/SwarmUS/HiveAR) page and install accordingly to the instructions in the same page.

<!-- TODO ask if hiveboard<->android connection should be documented here instead of inside "configuring the Wi-Fi network" from networking section.-->


## Swarm Connection
In the case of connecting HiveAR to a HiveBoard, whether a real or a simulated one, the user has access to connection settings in the Connection tab on the lower-left corner.

### Connection Status
As the Android device must be connected either by USB or TCP to communicate to the swarm, it is possible to keep track of the state of the connection with the swarm.

A small round icon has been add to the connection tab icon and can be observed as followed:
The round icon can represent four possible states:

- Red: Android device not connected
- Blue: Android device trying to establish connection with HiveMind
- Yellow: Android device connected to HiveMind, but HiveAR is not registered as part of the swarm
- Green: Android device is connected to HiveMind **And** HiveAR registered as part of the swarm

![Connection not connected](img/connection_tab_not_connected.jpg){: style="height:700px"}"
*Not connected status indicator*

![Connection connecting](img/connection_tab_connecting.jpg){: style="height:700px"}"
*Connecting to HiveMind status indicator*

<!-- missing yellow state-->

![Connection connected](img/connection_tab_connected_logs.jpg){: style="height:700px"}"
*Connected indicator with logs*

!!! Attention
    Always make sure connection with the swarm is established and working correctly (green round icon) before trying to use any swarm features. 
    
    In the case where there is a problem with the connection, one can always return to the connection page to try to establish the connection again.
