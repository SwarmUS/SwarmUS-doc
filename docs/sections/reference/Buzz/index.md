# BittyBuzz virtual machine

[Buzz](https://github.com/buzz-lang/Buzz) is a programming language made by [MISTLAB](https://mistlab.ca/) for a swarm made of heterogeneous robots.
Buzz code is compiled to byte code, which then runs on the Buzz Virtual Machine (BVM) which executes the bytecode. 
All robots in the swarm will then run the **same** byte code. Buzz provides multiple ways to manage the swarm of robots.
You can learn more about Buzz [here](https://the.swarming.buzz/wiki/doku.php?id=start). The language reference is hosted [here](https://github.com/buzz-lang/Buzz/blob/master/doc/api.md).

  
Since the BVM was designed to run on Linux, another implementation was necessary to run on an MCU. A smaller and lighter virtual machine.
This project is called [BittyBuzz](https://github.com/buzz-lang/BittyBuzz) and it provides the BittyBuzz Virtual Machine (BBVM).
The BittyBuzz virtual machine has some limitations. Here are the most important:

* Floating points are actually on 16 bits. See [here](https://github.com/buzz-lang/BittyBuzz/blob/master/src/bittybuzz/bbzfloat.c)
* Some mathematical operations are disabled, like raising a number by a decimal point.
* Strings are actually simply string id. This removes the need to store the string itself in the MCU
    * This prevents all string manipulation or dynamic creation of strings.
* The swarmlist broadcast is not available on the BBZVM for now.
    * Thus, `neighbors.kin()`, `neighbors.nonkin()` and `swarm.others()` are not available

In this implementation, if you use [HiveConnect](../../Networking/hiveconnect/) to send messages in the swarm, the neighbors broadcast message will have a different range than the actual neighbor list. So it's possible to broadcast a message to a neighbor that is not in your neighbor when a foreach, map or reduce is applied (i.e. broadcast message have more range than the current neighbors).

## Strings
On compilation, the strings in the script are stored in the program data. This allows to log with actual strings and call remote functions by name instead of only having the ID and registering function by its name. **Dynamic string manipulation is impossible**. Note that strings in the BBVM are more than just characters between "". All global variable names (namespace, constant, etc) are also stored with their associated strings.

Since strings use memory on the MCU, you can disable the storing of the string. To do so, you can provide a BST file with the name of the strings to disable. You can add your BST files to the `USER_BUZZ_INCLUDE_DIRS` in [this file](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/buzz_scripts/user_bst_files.cmake).

You can get an example of a BST file for the provided functions [here](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/bittybuzz.bst). Also note that symbols reserved by the VM don't have a string representation, you can see them [here](https://github.com/buzz-lang/BittyBuzz/blob/master/src/bittybuzz/util/BittyBuzzStrings.bst). If you see `UNKNOWN STRID: XX` in the logs, where `XX` is a string ID, those strings are declared in a BST file (internal or user defined). You can see their values in the HiveMind build directory in `src/bittybuzz/main_bytecode.h`, in this file you can see the internal id of every strings.

## Available APIs in Buzz Scripts
User-defined Buzz scripts can use some APIs to interact with some parts of the SwarmUS platform. This page regroups all these APIs and provides some links to their respective documentation.

### `BittyBuzzUIFunctions`: Interacting with the HiveBoard's UI

Link to the [documentation](https://swarmus.github.io/HiveMind/namespaceBittyBuzzUIFunctions.html#details).

This API provides some functions that can be used to interact with some UI elements on the HiveBoard. Users have access to one LED and one seven-segment display:

![UI for Buzz](img/buzz-ui.png)

### `BittyBuzzMathFunctions`: Useful Math Tools

Link to the [documentation](https://swarmus.github.io/HiveMind/namespaceBittyBuzzMathFunctions.html)

This API provides some math functions to be used in Buzz scripts.

### `BittyBuzzUserFunctions`: Managing User Functions

Link to the [documentation](https://swarmus.github.io/HiveMind/namespaceBittyBuzzUserFunctions.html)

This API provides the necessary tools to register and call some custom functions.

## Compilation configuration

You may need to edit the Bittybuzz config if you need more/less stack or heap memory in the BBVM. Check the available options [here](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/bittybuzz_config.cmake). The ones you may need are `BBZHEAP_SIZE`, `BBZSTACK_SIZE` and `BBZ_CLOSURE_REGISTER_LENGTH`. The closure register is used to register the Buzz functions in the script, so you may need to edit this value if you have a lot of registered functions.

## Tips

* Log as much as possible. While it may take some space on the MCU flash caused by the added strings representation, you should still log a lot. The language has no type check at the compilation and when a error occurs (i.e. calling a member on a `nil` value) there is no line number on the occurence since it was made to run on an MCU.

* Double check your variable names and types when writing code. In Buzz if a value was assigned, it's null, so it's perfectly valid for the compiler to pass a variable with a typo in it, which will have a `nil` value. Thus `let fizz; function(fiz);` would compile, but not have the desired outcome since the typo `fiz` is `nil`.

* Don't log on every step. The step function is called every 100ms (may change to 10ms). Logging everytime would pollute your logs, making it impossible to read anything.

* Use sleep as little as possible. While a sleep function is available, you shouldn't use it unless you understand the implications. Between every step function call, some processing is done. Mainly receive the messages from other swarm agent and send the message created during the execution to other swarm agents. If you sleep for too long, a queue may fill up and you will loose some messages. You should use the provided [executor](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/buzz_scripts/utils/executor.bzz) if you want to execute slower than the step.
