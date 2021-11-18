# Flasing an LED on a remote Hiveboard

Now that a [LED is flashing on a local Hiveboard](led-flash-buzz.md), let us use Buzz to interact between two HiveBoards. The goal here is to create a swarm of two agents using two HiveBoards. The first HiveBoard will send a command to the second, asking it to display some information using its LEDs. This will help you familiarize with the networking capabilities of the SwarmUS platform and show some more features of the software stack.

!!! Note
    **To follow this user guide, you will need:**

    * A computer running Linux
    * Two HiveBoards and their power supply (wall adapter). The HiveBoards must have their ESP32 chip flashed with the latest [HiveConnect](https://github.com/SwarmUS/HiveConnect) firmware.
    * A Wi-Fi antenna for each HiveBoard
    * A micro-USB to USB type A cable (to plug the HiveBoards to the computer)
    * An Android device running HiveAR (to setup the Wi-Fi network)
    * A micro-USB to USB type C cable (to plug the HiveBoards to the Android device; any series of adapters will do)

## Prerequesites

Before you go on with this tutorial, make sure that you have set up you environment and that you can flash a HiveMind firmware to your HiveBoards. Refer to the [previous User Guide](led-flash-buzz.md) for the procedure.

This User Guide will make use of the Wi-Fi networking capabilities of the SwarmUS platform. You will need to set up a Wi-Fi network between your two HiveBoards. Follow this [procedure](../../reference/Networking/configure-wifi.md) from the Reference section to set up the Wi-Fi network. You can use any network topology, *as long as both HiveBoards are on the same Wi-Fi network*.

## Writing an Enhanced `blinky.bzz`

Let's say that the two HiveBoards we use have IDs 2 and 5. We will write a single Buzz script which will set a "leader" using the virtual stigmergy (a most important feature from the [Buzz language](https://github.com/buzz-lang/Buzz/blob/1f6eda7bb1d4ad83d9d8e6ff46e130078e2c3a52/doc/api.md#vstig)) to set HiveBoard #2 as the leader. The leader will increment its own 7-segment display and then "ask" the HiveBoard #5 to do the same using a function call.

!!! note
    You might want to double check that the two HiveBoards have their IDs set to 2 and 5. The `UUID_OVERRIDE` CMake flag in HiveMind is used to set the ID of a board. See [HiveMind's README](https://github.com/SwarmUS/HiveMind#readme) for more on this.

!!! hint
    The virtual stigmergy is an array of information which is propagated across the swarm agents. It can be used to synchronize the behaviour of each angent. In this example, it is simply used to decide which HiveBoard is the leader. 
    
    There are many ways to exchange information between HiveBoards. Here we present two: the virtual stigmergy, and the use of function calls, where HiveBoard #2 will call a function on HiveBoard #5.

Create a new file called `blinky_swarm.bzz` and copy the following script. This code is meant to be run **identically on both HiveBoards**. Notice how this script looks a lot like the one from the [previous User Guide](led-flash-buzz.md), apart from a few additions. 

!!! attention
    Do not forget to include the new `blinky_swarm.bzz` file at the top of `main.bzz` so that it can be built.

```cpp
include "utils/executor.bzz"

ctx = {
    .hex = 0
}

function tick(context){
    var leader = stig.get("leader");

    if(leader == id){
        call_buzz_function(5, "blinky_set_hex", {.0 = context.hex});
        ui.set_hex(context.hex);
        
        if(context.hex > 15){
            context.hex = 0;
        } else {
            context.hex = context.hex + 1;
        }
    }
}

function blinky_set_hex(arg_int) {
    ui.set_hex(arg_int);
}

var args_description = {
    .0 = {.arg_int=0}
}

function create_exec() {
  stig = stigmergy.create(1);
  stig.put("leader", 2);

  register_closure("blinky_set_hex", blinky_set_hex, args_description, nil)

  exec = executor.new(200, tick, ctx);
  return exec;
}

```

### Creating a New Virtual Stigmergy

In function `create_exec()`, we start by creating a new virtual stigmergy.

!!! note
    The virtual stigmergy is a feature from Buzz itself and will therefore not be extensively documented here. You might want to refer to the [Buzz documentation](https://github.com/buzz-lang/Buzz/blob/1f6eda7bb1d4ad83d9d8e6ff46e130078e2c3a52/doc/api.md#vstig) for more on the matter.

For our use-case, we use the stigmergy to set the leader. We choose the HiveBoard #2:

```cpp
stig = stigmergy.create(1);
stig.put("leader", 2);
```

Notice in the `tick()` function how the value for the leader is accessed at every loop. This ensures that if the leader changes, the behaviour will adapt:

```cpp
var leader = stig.get("leader");
```

### Registering a Closure

In our use-case, we want the leader to increment the value shown on the 7-segment display of HiveBoard #5. To do that, we expose a closure to the swarm, called `blinky_set_hex`. The leader will then call this custom function to set the value.

We start by defining the function:

```cpp
function blinky_set_hex(arg_int) {
    ui.set_hex(arg_int);
}

var args_description = {
    .0 = {.arg_int=0}
}
```

Then, in the `create_exec()` function, we register the closure:

```cpp
register_closure("blinky_set_hex", blinky_set_hex, args_description, nil)
```

The reference documentation for the registration of closures, as well as everything involving user callbacks is linked [here](../../reference/Buzz/available-apis.md#bittybuzzuserfunctions-managing-user-functions).

### Calling a Remote Closure

In the function `tick()`, notice how the execution of the incrementation code is conditional. The `if (leader == id)` statement verifies whether or not the agent running this code is the leader, based on the value retreived from the virtual stigmergy.

When this code is run by the leader, it increments the 7-segment value and calls the function `blinky_set_hex` on the agent #5 with the hex value passed as argument.

The reference documentation for the calling of closures, as well as everything involving user callbacks is linked [here](../../reference/Buzz/available-apis.md#bittybuzzuserfunctions-managing-user-functions).

### Flash and Test

You can now flash the code to the two HiveBoards. Make sure that the Wi-Fi network is correctly set up and that both HiveBoards are connected to the same SSID (refer to the instructions [here](../../reference/Networking/configure-wifi.md)).

When both HiveBoards are flashed with the code, you should see the 7-segment display incrementing on both HiveBoards.

## More examples

### Controlling Multiple Slaves

An alternate version of the script presented earlier can be found [here](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/buzz_scripts/behaviors/hex_leader.bzz) in the HiveMind repository. This script uses some other features from Buzz, namely the `broadcast` and `listen` functions, which allow to communicate in a pub/sub manner using topics (see [Buzz documentation](https://github.com/buzz-lang/Buzz/blob/master/doc/api.md#neighbors) for more on the matter). This allows for multiple agents to be controlled by the Leader, without the need to make a function call for each agent.

### Synchronizing LEDs Without a Leader

One of the most interesting advantage of swarm robotics is the ability of a swarm to self-govern, without the need for a centralized manager. As such, the HiveMind repository provides an [example script](https://github.com/SwarmUS/HiveMind/blob/master/src/bittybuzz/buzz_scripts/behaviors/led_sync.bzz) where each agent will synchronize its LEDs with the rest of the swarm, just like fireflies do.
