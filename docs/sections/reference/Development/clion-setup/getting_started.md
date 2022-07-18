To help future developers to join the project, this page shares how the embedded SwarmUS's team set up its development environment. 

## IDE

!!!info
    This setup is not the only that could be used and you can used your favorite tools.

Since the embedded project is written in C and C++, the SwarmUS team decided to use the [CLion](https://www.jetbrains.com/clion/) IDE in linux. Furthermore, this IDE has a plug-in for the OpenOCD debugger used in the project and has a lot of [support](https://www.jetbrains.com/help/clion/embedded-overview.html#hardware) for STM32 and Xtensa MCUs that can be found on the [HiveBoard](../../../hardware/HiveBoard/HiveBoard_V1.000/). So the firt step of this guide will be to download this IDE.

!!! Attention
    The CLion IDE is inherently not free. However, there is a free version for students.

## Install repositories and dependencies

The embedded parts of the SwarmUS project includes the [HiveMind](../../HiveMind/hivemind-preface/) software that runs on a STM32H7 and the [HiveConnect](../../Networking/hiveconnect/) that runs on a ESP-32. Before starting to configure the IDE, clone both of the repositories, and install and setup all the required packages as describe in the [HiveMind's readme](https://github.com/SwarmUS/HiveMind) and [HiveConnect's readme](https://github.com/SwarmUS/HiveConnect). Make sure that you are able to build the two repositories before moving on to building the repositories in CLion.

Once done, the next step is to configure CLion for the repository you want to work on:  [HiveMind](../hive_mind_setup) or [HiveConnect](../hive_connect_setup). Since a part of debugging is done via the serial port of the MCUs, an [additional section](../logging) is given to setup the logging of those channels in CLion