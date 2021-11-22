# About HiveMind

The HiveMind (HM) is the firmware that runs on the HiveBoard STM32 MCU or on Linux with ROS. The HiveMind is connected to a host robot and the rest of the swarm. It then orchestrates the logic of the swarm and sends messages to the host or other members in the swarm accordingly.

The codebase for HiveMind is hosted on [GitHub](https://github.com/SwarmUS/HiveMind).

The HiveMind has multiple roles in the swarm:

- It runs the [BBVM (BittyBuzz Virtual Machine)](bbzvm/index.md), which executes the user code that defines the behavior of the swarm.
- Routes the messages from one component to the other which includes
    - The host
    - The BBVM
    - Other agents in the swarm
        - The communication between the components of the swarm is generally handled by the [HiveConnect](../Networking/hiveconnect/)
- Provides an [API for the host robot](hivemind-api.md). The API allows to:
    - [Get the list of robots currently in the whole swarm network](hivemind-api.md#swarm-list)
    - [Get the current neighbors of a robot](hivemind-api.md#neighbors-list)
    - [Get information on a neighbor such as distance, angle and if the neighbor is in the line of sight (LOS)](hivemind-api.md#neighbor)
    - [Send bytes to another robot in the swarm](hivemind-api.md#bytes)
    - [Call a function of another robot in the swarm](hivemind-api.md#function-call)
    - [Call a buzz function in the swarm](hivemind-api.md#function-call)

