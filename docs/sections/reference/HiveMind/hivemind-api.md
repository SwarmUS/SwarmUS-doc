# HiveMind API

The HiveMind provides an API via protobuf for the host which allows it to get information on the swarm (neighbors, swarmlist, distance of a neighbor, etc), act on it or more. Most of the swarm decisions should be done via the buzz script to separate the logic of the swarm and it's action on the world, but sometimes more computing power can be requried, which can be done on the host if needed. Thus the host have acces to an API.

Note that the host should only use a subset of the messages. The whole message hierarchy is provided for a better understanding of the inner workings. **Only the Request and Response messages (and those composed in it) are for the user/host to call, the others are internal messages for swarm functionalities.**

Here is the message hierarchy:

![Message hierarchy](img/message-hierarchy.png)

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