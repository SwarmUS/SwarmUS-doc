# HiveMind preface

The HiveMind (HM) is the firmware that runs on the HiveBoard STM32 MCU or on Linux with ROS. The HiveMind is connected to a host robot and the rest of the swarm. It then orchestrates the logic of the swarm and sends messages to the host or other members in the swarm accordingly.

The HiveMind has multiple roles in the swarm:

- It runs the [BBVM (BittyBuzz Virtual Machine)](bbzvm/index.md), which executes the user code that defines the behavior of the swarm.
- Routes the messages from one component to the other which includes
    - The host
    - The BBVM
    - Other agents in the swarm
        - The communication between the components of the swarm is generally handled by the [HiveConnect](https://github.com/SwarmUS/HiveConnect) <!-- TODO: change to the hive connect link to the reference section instead of repo-->
- Provides an API for the host robot. The API allows to :
    - Get the list of robots currently in the whole swarm network
    - Get the current neighbors of the robot
    - Get information on a neighbor such as distance, angle and if the neighbor is in the line of sight (LOS)
    - Send bytes to another robot in the swarm
    - Call a function of another robot in the swarm
    - Call a buzz function in the swarm

