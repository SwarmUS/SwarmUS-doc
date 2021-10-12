# Available UI

The HiveBoard have multiple indicators to help the user debug it's configuration or it's application. 


## Hardware
| LED RGB     | HiveMind comm interface            |
| LED HB      | HiveMind heartbeat                 |
| LED MCU_0   | HiveMind and HiveConnect handshake |
| LED MCU_1   | HiveMind and Host handshake        |
| LED MCU_2   | User LED                           |
| LED 3.3v    | 3.3V is properly powered           |
| LED 5V      | 5V is properly powered             |
| 7 segment 1 | BBVM status                        |
| 7 segment 2 | User 7 segment                     |
    
#### HiveMind comm interface
Display's the status of the comm interface. Note that this means the comm interface is opened, not that the two device communicates. They need to do an handshake after opening the interface. Thus a connection is fully established when the RGB is teal or blue **and** the MCU_1 is on. If you plug the Ethernet or USB cable and the LED is not changing, make sure you have the right TCP port for Ethernet or for USB that you open the port on the host.

| White  | Booting                |
| Violet | Unconnected            |
| Blue   | Connected via Ethernet |
| Teal   | Connected via USB      |
| Red    | Error                  |

#### HiveMind heartbeat

The heartbeat of the HiveMind, if this LED is not flashing at 1Hz, you HiveMind is probably in a hard fault state. This should not happen in theory, but things can always go wrong. Make sure everything is up to date and then you can consider raising an issue in the [HiveMind repo](https://github.com/SwarmUS/HiveMind/issues) if the issue wasn't already raised.

#### HiveMind and HiveConnect handshake
Shows if the HiveMind and the HiveConnect completed their handshake successfully. If the LED is not on after bootup, make sure everything is up to date and that the ESP power DIP switch is enabled.

> The DIP switches are inverted in the first version of the board. So ON actually disables it.


#### HiveMind and Host handshake
Shows if the HiveMind and the host completed their handshake successfully. If the RGB LED is on, but not this one. Make sure everything is up to date. This may be an incompatible version of [Pheromones](https://github.com/SwarmUS/Pheromones) (or [Propolis](https://github.com/SwarmUS/Propolis) that used the wrong Pheromones version).


#### User LED
LED that can be set in the buzz script via `ui.set_led(x)`. Use this to know the state of you application for easier debugging. You can get more information in the [led blink user guide](../user-guide/basic-examples/led-flash-buzz).

> The user and vm 7 segment are inverted between the first version of the HiveBoard and the ROS build 

#### 3.3V
Is on if the 3.3V is working properly. Check the power requirements if it's not on.

#### 5V
Is on if the 5V is working properly. Check the power requirements if it's not on.

#### BBVM status
The status of the BBVM, thus your buzz code.
If it's 0, the vm, is running fine. If it's a non zero value. The value represent the error code via an enumeration.

The values of the enumeration can be found [here](https://swarmus.github.io/HiveMind/SystemStates_8h.html#a0c10345a5a61ea917f59a0437ad481a0). If you want more information about the error code meaning, you can check the [BittyBuzz repo](https://github.com/buzz-lang/BittyBuzz/blob/c7b6294230819b58a2420cfd6f9847c4a927bba9/src/bittybuzz/bbzenums.h#L25).


#### User segment
A segment that can be set in the buzz script via `ui.set_hex(x)`. Use this to have information about the state of your swarm application. You can get more information in the [led blink user guide](../user-guide/basic-examples/led-flash-buzz).


## ROS
ROS replicates the interface of the HiveBoard via ROS logging system. Everytime something is written on the rosconsole, a string is prepended with the state of the HiveMind. The prepended string should be similar to:

`[HM: Id] [UI: rgb: V led: 000 hex: 01]`

The `rgb` is expressed via the first letter of it's color. The `led` represents the MCU_0, 1 and 2 (handshake HC, handhsake Host, user led). The `hex` represents the 7 segment (Vm state and user 7 segment).
