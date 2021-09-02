# Bitty Buzz virtual machine

[Buzz](https://github.com/MISTLab/Buzz) is a programming language made by [MISTLAB](https://mistlab.ca/) for a swarm made of heterogeneous robots.
Buzz code is compiled to byte code, which then runs on the Buzz Virtual Machine (BVM) which executes the bytecode. 
All robots in the swarm will then run the same byte code. Buzz provides multiple way to manage the swarm of robot.
You can learn more about buzz [here](https://the.swarming.buzz/wiki/doku.php?id=start).

  
Since the BVM was designed to run on Linux, another implementation was necessary to run on an MCU. A smaller and lighter virtual machine.
This project is called [BittyBuzz](https://github.com/MISTLab/BittyBuzz) whic provides the BittyBuzz Virtual Machine (BBVM).
The BittyBuzz virtual machine has some limitation. Here are the most important:
- Floating points are actually fixed point on 16 bits.
- Some mathematical operation are disabled, like raising a number by a decimal point.
- Strings are actually simply string id. This removes the need to store the string itself in the MCU
    - This prevent all string manipulation or dynamic creation of strings.
- The swarmlist broadcast is not available on the BBZVM for now.

<!-- TODO: Change link to HiveConnect page-->
In this implementation, if you use [HiveConnect](https://github.com/SwarmUS/HiveConnect) to send messages in the swarm, the neighbors broadcast message will have a different range than the actual neighbor list. So it's possible to broadcast a message to a neighbor that is not in your neighbor when a foreach, map or reduce is applied (i.e. broadcast message have more range than the current neighbors).

## Added functionalities to the BBZVM
Some functionalities were added to the BBZVM on the HiveMind.

### Strings
On compilation, the strings in the script are stored in the program data, this allows to log with actual strings and call remote functions by name instead of only having the ID and registering function by its name. **Dynamic string manipulation is still impossible**. Note that strings in the BBVM are more than just characters between "". All global variable names (namespace, constant, etc) are also stored with their associated strings.

Since strings uses memory on the MCU, you can disable the storing of the string. To do so, you can provide a BST file with the name of the strings to disable. You can get an example of a BST file for the provided functions [here](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/bittybuzz.bst). Also note that symbols reserved by the VM don't have a string representation, you can see them [here](https://github.com/buzz-lang/BittyBuzz/blob/master/src/bittybuzz/util/BittyBuzzStrings.bst).

### Functions
#### Global namespace
Some functions were added to the BBVM, you can use those functions in your buzz scripts. You can get the list of the functions [here](https://swarmus.github.io/HiveMind/namespaceBittyBuzzUserFunctions.html).

To interact with the host, you will mostly use `call_host_function(id, "move", {.0 = 42, .1 = 42});`. Note that using the `id` variable is the local id, thus calling the localhost robot. It is still possible to call a remote host function, just change the id with the robot you want to call. Note that only int and float can be used as arguments, string, tables and closures are not supported. 

#### Math

In addition to the functions added to the global namespace, a math library was added to make some calculation possible. You can get the documentation of the available symbols [here](https://swarmus.github.io/HiveMind/namespaceBittyBuzzMathFunctions.html). All symbols are in the math namespace, so to call the max function you need to call it via the namespace such as `math.max(0, 1); # returns 1`.

## Compilation configuration

You may need to edit the bittybuzz config if you need more/less stack or heap memory in the BBVM, you can check the available options [here](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/bittybuzz_config.cmake). The ones you may need are `BBZHEAP_SIZE`, `BBZSTACK_SIZE` and `BBZ_CLOSURE_REGISTER_LENGTH`. The closure register is use to register the buzz functions in the script, so you may need to edit this value if you have a lot of registered functions.

## Tips

- Log as much as possible. While it may take some space on the MCU flash caused by the added strings representation, you should still log a lot. The language has no type check at the compilation and when a error occurs (i.e. calling a member on a `nil` value) there is no line number on the occurence since it was made to run on an MCU.

- Double check your variable name and type when writing code. In buzz if a value was assigned, it's null, so it's perfectly valid for the compiler to pass a variable with a typo in it, which will have a `nil` value. Thus `let fizz; function(fiz);` would compile, but not have the desired outcome since the typo `fiz` is `nil`.

- Don't log on every step. The step function is called every 100ms (may change to 10ms). Logging everytime would pollute your logs and making it impossible to read anything.

- Don't use sleep as much as possible. While a sleep function is available, you shouldn't use it unless you understand the implications. Between every step function call, some processing is done. Mainly receive the messages from other swarm agent and send the message created during the execution to other swarm agents. If you sleep for too long, a queue may fill up and you will loose some messages. You should use the provided [executor](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/buzz_scripts/utils/executor.bzz) if you want to execute slower than the step.
