# HiveBoard V1.000
The HiveBoard is a STM32H7 based development board. It harbors 6 USB-C channels 
to which up to 6 [Beeboards]('sections\hardware\HiveBoard\hardware\BeeBoard\BeeBoard_V1.000.md' ) can be connected. 

![Figure HiveBoard](img/HB_plain.PNG)


!!! important
    The USB-C at the top end of the HiveBoard are not actual USB-C. The connector was used because it houses five differential 
    pairs which were required.

The HiveBoard is pluggable by three methods and requires X Amps of current plus X Amps per BeeBoard connected: 

1. The 5V jack 

2. The screw terminal block

3. The u-USB

The main switch located on the left bottom has three positions : 

Down: power is taken from the u-USB connector.

Middle: Off state

Up: power is taken from either 5V Jack or screw terminal block.

!!! important 
    If both 5V jack and screw terminal are connected, they will fight each other. Meaning that the one with lower voltage will receive 
    current from the other one. It is recommended to use only on of theses 2 methods.


!!! note
    Altough the board can be powered by u-USB, not all the components on the board will be activated if powered by that method. 
    Refer to the [schematics](https://www.github.com/swarmus/electrical) and the [Available UI](hiveboard-ui.md) for more info
    on the switches.

## Circuits


## Functionalities

## Connection to BeeBoards

## Problems with V1.000

