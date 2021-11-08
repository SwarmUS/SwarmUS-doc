# HiveBoard V1.000
The HiveBoard is a STM32H7 based development board. It harbors 6 USB-C channels 
to which up to 6 [Beeboards]('sections\hardware\HiveBoard\hardware\BeeBoard\BeeBoard_V1.000.md' ) can be connected. 

![Figure HiveBoard](img/HB_plain.PNG)


!!! important
    The USB-C at the top end of the HiveBoard are not actual USB-C. The connector was used because it houses five differential 
    pairs which were required.

The HiveBoard is pluggable by three methods and requires X Amps of current plus X Amps per BeeBoard connected: 

1. The 5V jack 

2. The screw terminal block (+ terminal on the right side)

3. The Micro-USB

The HiveBoard has the three basic protections of a complex circuit. It is protected in reverse polarity, over-voltage and overcurrent.
The absolute maximum current consumption of a HiveBoard with 6 BeeBoard attached is below 5A. 
The main switch located on the left bottom has three positions : 

Down: power is taken from the Micro-USB connector.

Middle: Off state

Up: power is taken from either 5V Jack or screw terminal block.

!!! important 
    If both 5V jack and screw terminal are connected, they will fight each other. Meaning that the one with lower voltage will receive 
    current from the other one. It is recommended to use only one of these 2 methods at a time.


!!! note
    Altough the board can be powered by Micro-USB, not all the components on the board will be activated if powered by that method. 
    Refer to the [schematics](https://www.github.com/swarmus/electrical) and the [Available UI](hiveboard-ui.md#power-switch) for more info
    on the switches.

## System operation

The top 6 USB-C are used to connect BeeBoards. The connectors are separated in pairs, (0A-1A/0B-1B/0C-1C) is supported by a single SPI channel.
In the swarmUS original code, only 3 Beeboards were connected and each of them was on a diffent SPI bus.

!!! error
    If the V1.000 version of the HiveBoard is used, reworks will be necessary for correct operation of the system.
    For more details, see the [schematics](https://www.github.com/swarmus/electrical).

The Ethernet connector is used to connect the HiveBoard to a host (be it a robot's embedded computer or any device that is to be swarm-enabled using the SwarmUS platform). The right side 100 mil connector is for exposing the unused I/Os of the STM32H7 in the case the user wants to supplement the HiveBoard with some other expansion card.

The Hiveboard has an external debugger. For proper system operation and debugging via the on-board debugger, the 100 mil header shall remain in place.
Otherwise, an external debugger can be plugged on the right side of the headers to access the STM32H7 directly.

# V2.000
The HiveBoard has a 2.000 version adressing the mistakes and bugs from V1.000, but with the new DW3220 IC on the market. The entire HiveBoard-BeeBoard design should be revised.
