# HiveMind API

The HiveMind provides an API via protobuf for the host which allows it to get information on the swarm (neighbors, swarmlist, distance of a neighbor, etc), act on it or more. Most of the swarm decisions should be made via the Buzz script to separate the logic of the swarm and its action on the world. However, sometimes more computing power can be requried, which can be done on the host if needed. Thus the host has acces to an API.

!!! note
    The HiveMind API is implemented as a set of [Protocol Buffer](https://developers.google.com/protocol-buffers) messages. This means that any program could interact with the HiveMind API, as long as it uses the same message definition. For instance, in the SwarmUS platform, [HiveMindBridge](https://github.com/SwarmUS/HiveMindBridge), [HiveConnect](https://github.com/SwarmUS/HiveConnect), [HiveAR](https://github.com/SwarmUS/HiveAR), and the [Python testbench](https://github.com/SwarmUS/TestBench-Python) use the HiveMind API.
    
    The messages for HiveMind API are defined in the [Pheromones repository](https://github.com/SwarmUS/Pheromones).

    In the SwarmUS codebase, the Pheromones message definitions have been further wrapped for C++ specifically, since this is the language used in most of the stack. The C++ wrapping is included in the [Propolis repository](https://github.com/SwarmUS/Propolis) as the `propolis-pheromones` library.

Note that the host should only use a subset of the messages. The whole message hierarchy is provided for a better understanding of the inner workings. **Only the Request and Response messages (and those composed in it) are for the user/host to call, the others are internal messages for swarm functionalities.**

Here is the message hierarchy:

![Message hierarchy](img/message-hierarchy.png)

Here is a description of the Messages:

- Request and Response: <a name="request"></a>
  Requests and Reponses available for the user.

    - GenericResponse: <a name="gen-request"></a>
      A generic response can be used to indicate success or failure. The details field can be used to log on the other side. When there is no payload on a request, a GenericResponse is used as an ACK.

    - UserCall: <a name="user-call"></a>
      Regroup multiple messages. All UserCalls can be to/from Buzz or the host.

        - FunctionCall: <a name="function-call"></a>
          Used to call a function to a host or Buzz script. The function name needs to match the one that was registered, the number of arguments and their types needs to match. 

        - FunctionListLength: <a name="function-list-length"></a>
          Used to get the total number of registered functions. See [FunctionDescription](#FunctionDescription)

        - FunctionDescription: <a name="function-description"></a>
          To obtain the description of a function, such as its name, number of arguments, arguments name and the arguments types. Note that you access function descriptions by list index, which starts at 0. So if the function list length is 5, the valid list indexes are [0,1,2,3,4].

    - HiveMindHostAPI: <a name="hivemind-host"></a>
      API that the HiveMind exposes to the host, this includes an API for the interlocalisation, bytes and swarmlist

          - Bytes: <a name="bytes"></a>
            A request used to send bytes, ACK with a generic response. Note that each Bytes message is a packet of a bigger payload, you can reassemble the total packet with the packet id, packet number and a boolean that identifies the last packet. You need to concatenate the data to get the original byte stream.

          - Neighbor: <a name="neighbor"></a>
            Get the information on a particular neighbor (distance, orientation, line of sight).

          - NeighborsList: <a name="neighbors-list"></a>
            Get a list of ids containing the current neighbors. Note that you only get their id, you need to make a [Neighbor Request](#Neighbor) to obtain the data.

          - SwarmList: <a name="swarm-list"></a>
            Get a list of ids containing the current agents in the swarm.

- Greeting: <a name="greeting"></a>
  The first message sent to the HiveMind needs to be a greet (can be with garbage value). The HiveMind will reply with a Greet with its ID, allowing the host to know its id in the swarm. This is required to make requests in the swarm. The greeting should be the first thing to be sent on connection.

- NetworkAPI: <a name="network-api"></a>
  Internal message to manage connections with other members in the swarm. Not relevant to the user.

    - IpDiscovery: <a name="ip-discovery"></a>
      Used to discover new IP addresses in the network.

- InterlocAPI: <a name="interloc-api"></a>
  Messages internal to the interlocalisation. You can calibrate your setup using the [CalibrationMessage](#CalibrationMessage) You can use [PythonTool](https://github.com/SwarmUS/PythonTool/tree/master) which is a small wrapper in python, allowing the calibration for the interlocalisation.

    - CalibrationMessage: <a name="calibration"></a>
      Used for calibration of the physical setup for the interlocalisation.

    - SetCalibrationDistance: <a name="calib-distance"></a>
      Set the calibration distance.

    - StartCalibration: <a name="start-calib"></a>
      Starts the calibration.

    - StopCalibration: <a name="stop-calib"></a>
      Stops the calibration

    - CalibrationEnded: <a name="calib-ended"></a>
      ACK on the ending of the calibration

- BuzzMessage: <a name="buzz-msg"></a>
  Internal message to Buzz to share data between the virtual machines in the swarm.