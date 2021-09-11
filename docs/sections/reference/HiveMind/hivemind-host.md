# HiveMind and the Host

The host robot generally only provides functionalities to interact with the physical world and should not have swarm related decision. Once connected, the HiveMind will coordinate the swarm and use the calls provided by the robot to interact with the physical world. In other words, the robot is passive and the decisions are made by the Buzz script.

For example, the HiveMind could request the robot to move to an x,y position, relative to its current position, thus the "moveBy" function would be called on the host.

## Communication

The HiveMind needs to communicate with the host and this is generally done via TCP or USB. The communication is done with Google's Protocol Buffer, you can find the definition of the Protocol buffer in the [Pheromone repo](https://github.com/SwarmUS/Pheromones). Note that for every repeated field, there is a limit, which is configurable at configure time with CMake, see [here](https://github.com/SwarmUS/Pheromones/blob/master/src/proto/message.options.in). Changin the values will affect the ram usage.

### Scheme

The HiveMind communicates via protobuf messages with the host. One can send a Request and the other will respond with a Response message.
Note that this doesn't mean request are sequential. If you send a Request to the HiveMind, the next message will **not** necessarily be the Response you just requested, you should treat messages as events and handle them accordingly, not as an atomic transaction.
Only the Request and Response messages (and those composed in it) are for the user to call, the others are internal messages for swarm functionalities.

To be a host you need to implement all the functionalities in the Request and Response messages, since this can be time consuming, a C++ implementation is already provided [here](../../../user-guide/follow-the-leader/hivemind-bridge-getting-started/). Then you can register simply register your required functions.
<!-- TODO: Change link to HiveMindBridge peface?-->