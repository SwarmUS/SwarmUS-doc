# Available UI

The HiveBoard has multiple indicators to help the user debug its configuration or its application. 

## Hardware

These are the UI elements that are worth mentioning on a physical HiveBoard:

| ID on board | Description                        |
| ----------- | ---------------------------------- |
| LED RGB     | HiveMind comm interface            |
| LED HB      | HiveMind heartbeat                 |
| LED MCU_0   | HiveMind and HiveConnect handshake |
| LED MCU_1   | HiveMind and Host handshake        |
| LED MCU_2   | User LED                           |
| LED 3.3v    | 3.3V is properly powered           |
| LED 5V      | 5V is properly powered             |
| 7 segment 1 | BBVM status                        |
| 7 segment 2 | User 7 segment                     |

![HiveBoard UI](img/hiveboard-ui.png)

### LED `RGB` - HiveMind Comm Interface

Displays the status of the comm interface. Note that this means the comm interface is opened, not that the two devices communicate. They need to do a handshake after opening the interface. Thus a connection is fully established when the RGB is of color teal or blue **and** LED MCU_1 is on. If you plug the Ethernet or USB cable and the LED is not changing, make sure you have the right TCP port for Ethernet or for USB that you open the port on the host.

The RGB LED can take the following colours:

| Colour | State                  |
| ------ | ---------------------- |
| White  | Booting                |
| Violet | Unconnected            |
| Blue   | Connected via Ethernet |
| Teal   | Connected via USB      |
| Red    | Error                  |

### LED `HB` - HiveMind Heartbeat

The heartbeat of the HiveMind indicates that the firmware runs normally. In normal operation, this LED flashes at 1 Hz. If this LED is _not_ flashing, HiveMind is probably in a hard fault state. This should not happen in theory, but things can always go wrong. Make sure everything is up to date and then you can consider raising an issue in the [HiveMind repo](https://github.com/SwarmUS/HiveMind/issues) if the issue wasn't already raised.

### LED `MCU_0` - HiveMind and HiveConnect Handshake

Shows if the HiveMind and the HiveConnect completed their handshake successfully. If the LED is not turned on after bootup, make sure everything is up to date (check versions of HiveMind and HiveConnect) and that the ESP power DIP switch is enabled.

> **Warning** The DIP switches are inverted in the first version of the HiveBoard. So ON actually disables it.

### LED `MCU_1` - HiveMind and Host Handshake

Shows if the HiveMind and the host completed their handshake successfully. If the RGB LED is on, but not `MCU_1`, make sure everything is up to date. This may be caused by an incompatible version of [Pheromones](https://github.com/SwarmUS/Pheromones) (or [Propolis](https://github.com/SwarmUS/Propolis) that used the wrong Pheromones version). These two libraries are used by various elements of the stack (HiveMind, HiveMindBridge, HiveAR), and they **must** imperatively have matching versions.

### LED `MCU_2` - User LED

LED that can be set in the Buzz script via `ui.set_led(x)`. Use this to know the state of you application for easier debugging. You can get more information in the [led blink user guide](../user-guide/basic-examples/led-flash-buzz.md). The complete API documentation can be found [here](https://swarmus.github.io/HiveMind/namespaceBittyBuzzUIFunctions.html#details).

> The `User` and `BBVM` 7 segments are inverted between the first version of the HiveBoard and the ROS build.

### LED `3.3V`

Is on if the 3.3V power supply is working properly. Check the power requirements if it's not on.

### LED `5V`

Is on if the 5V power supply is working properly. Check the power requirements if it's not on.

### 7 segment 1 - BBVM status

The status of the BBVM, in which your Buzz code is run.

If it's 0, the VM, is running fine. If it's a non zero value. The value represent the error code via an enumeration.

The values of the enumeration can be found [here](https://swarmus.github.io/HiveMind/SystemStates_8h.html#a0c10345a5a61ea917f59a0437ad481a0). If you want more information about the error code meaning, you can check the [BittyBuzz repo](https://github.com/buzz-lang/BittyBuzz/blob/c7b6294230819b58a2420cfd6f9847c4a927bba9/src/bittybuzz/bbzenums.h#L25).

### 7 segment 2 - User segment

A segment that can be set in the Buzz script via `ui.set_hex(x)`. Use this to have information about the state of your swarm application. You can get more information in the [led blink user guide](../user-guide/basic-examples/led-flash-buzz). The complete API documentation can be found [here](https://swarmus.github.io/HiveMind/namespaceBittyBuzzUIFunctions.html#details).

## ROS

When simulating the usage of a HiveBoard on a computer, HiveMind will simulate the lower-level hardware stack using ROS (see [HiveMind's readme](https://github.com/SwarmUS/HiveMind#readme)). This configuration replicates the interface of the HiveBoard via ROS logging system. Everytime something is written on the rosconsole, a string is prepended with the state of the HiveMind. The prepended string should be similar to:

`[HM: Id] [UI: rgb: V led: 000 hex: 01]`

* The `rgb` is expressed via the first letter of its color. 

* The `led` represents the MCU 0, 1 and 2 (handshake HiveConnect, handshake Host, user LED). 

* The `hex` represents the 7 segments (VM state at the left and user 7 segment at the right).
