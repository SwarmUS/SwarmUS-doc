# HiveMind developer guide

The HiveMind(HM) is the firmware that runs on the HiveBoard STM32 MCU or on Linux with ROS. The HiveMind is connected to a host robot and the rest of the swarm. Then it can call functions on the host robot or send messages in to other members in the swarm.

The HiveMind have multiple role in the swarm:

- It runs the [BBVM (BittyBuzz Virtual Machine)](bbzvm/index.md), which executes the user code that defines the behavior of the swarm.
- It allows communication between the host robot, the BBVM and the rest of the Swarm
  - Routes the messages from one component to
    - The host
    - The BBVM
    - The rest of the swarm
      - The communication between the components of the swarm is generally handled by the [HiveConnect](https://github.com/SwarmUS/HiveConnect) <!-- TODO: change to the hive connect link -->
- Provides an API for the host robot. The API allows:
  - Get the list of robots currently in the whole swarm network
  - Get the current neighbors of the robot
  - Get information on a neighbor such as distance, angle and if the neighbor is in the line of sight (LOS)
  - Send bytes to another robot in the swarm
  - Call a function of another robot in the swarm
  - Call a buzz function of a in the swarm

## WIP

The HiveMind makes uses of function calls as way to interact with different components and those functions are called by name. As a user, you define the functions and uses them in the buzz script and in the host. The functions are registered with a description of it arguments (name and type) and a callback (the actual function), it is then possible to call this function from another component in the swarm. Those functions calls does not have return values, so instead it's possible to call another function and the "return value" will be an argument of the function call.

For example: The buzz script calls `getStatus()` on it's local host, the host could then call `returnGetStatus(status)`, the buzz script would have a registered `returnGetStatus` function and could obtain the return value this way. This makes sure all messages are treated as event and nothing is blocking.

Here the "user" means someone either someone that wants to implement swarm behavior, or someone that implement the code for a robot to be a host for the HiveMind. The swarm behavior will mostly write buzz script, while the host robot will implement the required interface to communicate with the HiveMind.

## The HiveMind and the Host

The host robot generally only provides functionalities to interact with the physical world and should not have swarm related decision. Once connected, the HiveMind will coordinate the swarm and use the calls provided by the robot to interact with the physical world.

For example, the HiveMind could request the robot to move to an x,y position, relative to its current position, thus the "moveBy" function will be called on the host. So in a swarm composed of 5 robots, 5 different HiveMind will be connected to those robots.

### Communication

The HiveMind needs to communicate with the host, generally via TCP or USB. The communication is done with Google's Protocol Buffer, you can find the definition of the Protocol buffer in the [Pheromone repo](https://github.com/SwarmUS/Pheromones). Note that for every repeated field, there is a limit, which is configurable at configure time with CMake, see [here](https://github.com/SwarmUS/Pheromones/blob/master/src/proto/message.options.in), this will affect the ram usage.

#### Scheme

The HiveMind can send message to the host and vice-versa. One can send a Request and the other will respond with a Response message.
Note that this doesn't mean if you send a Request to the HiveMind, the next message will necessarily be the Response you just requested, you should treat messages as event and handle them accordingly, not as an atomic transaction.
Only the Request and Response messages (and those composed in it) are for the user (you), the others are internal messages for swarm functionalities.

To be a host you need to implement all the functionalities in the Request and Response messages, since this can be time consuming, a C++ implementation is already provided [here](https://github.com/SwarmUS/HiveMindBridge). Then you can register simply register your required functions.

## API

The HiveMind provides an API via protobuf for the host which allows it to get information on the swarm (neighbors, swarmlist, distance of a neighbor, etc), act on it or more. Most of the swarm decisions should be done via the buzz script to separate the logic of the swarm and it's action on the world, but some can require more computing power, which can be done on the host if needed.

Here is a description of the Messages:

- Request and Response:
  Requests and Reponses available for the user.

  - GenericResponse:
    A generic response, can be used to indicate success or failure, the details field can be used to log on the other side. When there is no payload on a request, a GenericResponse is used as an ACK.

    - UserCall:
      Regroup multiple messages. All UserCall can be to/from Buzz or the host.

          - FunctionCall:
          Used to call a function to a host or buzz script. The function name needs to match the one that was registered, the number of arguments and their types needs to match.

          - FunctionListLength:
          Used to get the total number of registered functions. See [FunctionDescription](#FunctionDescription)

          - FunctionDescription:
          To obtain the description of a function, such as its name, number of arguments, arguments name and the arguments types. Note that you access function description by list index, which starts at 0. So if the function list length is 5, the valid list indexes are [0,1,2,3,4].

    - HiveMindHostAPI:
      API that the HiveMind expose to the host, this includes an API for the interlocalisation, bytes and swarmlist

          - Bytes:
          A request used to send bytes, ACK with a generic response. Note that each Bytes message is a packet of a bigger payload, you can reassemble the total packet with the packet id, packet no and if it's the last packet. You need to concatenate the data to get the original byte stream.

          - Neighbor:
          Get the information on a particular neighbor (distance, orientation, line of sight).

          - NeighborsList:
          Get a list of id containing the current neighbors, note that you only get their id, you need to make a [Neighbor Request](#Neighbor) to obtain the data.

          - SwarmList:
          Get a list of id containing the current agents in the swarm.

- Greeting:
  First message sent to the HiveMind needs to be a greet (can be with garbage value). The HiveMind will reply with a Greet with its ID, allowing the host to know it's Id in the swarm, which is required to make requests in the swarm. Should be the first thing to send on connection.

- NetworkAPI:
  Internal message to manage connections with other members in the swarm. Not relevant to the user.

  - IpDiscovery:
    Used to discover new IP addresses in the network.

- InterlocAPI:
  Messages to internal to the interlocalisation. You can calibrate your setup using the [CalibrationMessage](#CalibrationMessage) You can use [PythonTool](https://github.com/SwarmUS/PythonTool/tree/master) which is a small wrapper in python, allowing the calibration for the interlocalisation.

  - CalibrationMessage:
    Used for calibration of the physical setup for the interlocalisation.

  - SetCalibrationDistance:
    Set the calibration distance.

  - StartCalibration:
    Starts the calibration.

  - StopCalibration:
    Stops the calibration

  - CalibrationEnded:
    ACK on the ending of the calibration

- BuzzMessage:
  Internal message to buzz to share data between the virtual machines in the swarm.
