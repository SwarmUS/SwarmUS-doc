# Flasing an LED on a remote Hiveboard

Now that a [LED is flashing on a local Hiveboard](led-flash-buzz.md), let us now light up a LED on a remote Hiveboard. This will help you familiarize with the networking capabilities of the SwarmUS platform and show some more features of the software stack.

The goal here is to create a swarm of two agents using two HiveBoards. The first HiveBoard will send a command to the second, asking it to display some information using its LEDs.

## Prerequesites

In this User Guide, we will need 2 HiveBoards.

Before you go on with this tutorial, make sure that you have set up you environment and that you can flash a HiveMind firmware to your HiveBoards. Refer to the [previous User Guide](led-flash-buzz.md) for the procedure.

This User Guide will make use of the Wi-Fi networking capabilities of the SwarmUS platform. You will need to set up a Wi-Fi network between your two HiveBoards. Follow this [procedure](../../reference/Networking/configure-wifi.md) from the Reference section to set up the Wi-Fi network. You can use any network topology, *as long as both HiveBoards are on the same Wi-Fi network*.

## Writing an Enhanced `blinky.bzz`



<!-- 
TODO

Instructions pour configurer le réseau wifi entre les 2 boards.

Présenter les différentes méthodes pour que 2 HB puissent s'échanger de l'information et requêtes. Stigmergie, functioncallrequest, RPC natif à Buzz(?)

Idea to introduce the concept of stigmergy and message propagation: stigmergy containing ID of board + LED on/off

checklist for each section of the required hardware and/or software set-up
 
 -->