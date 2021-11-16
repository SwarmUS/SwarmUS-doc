# Synchronization

Synchronization of all agents between each other is used in order to have each agent send the correct message over UWB at the correct time. The [Distance Measurement](distance.md) and [Angle Measurement](angle.md) sections should be read before continuing in order to understand all the messages and their sequencing required to calculate distances and angles.

## Time-Slotting Overview

As introduced in [Distance Measurement](distance.md), each agent must, in turns, become the initiator in a two-way ranging (TWR) sequence for all other agents to update their knowledge of the initiator's position. In order to accumulate enough messages to calculate an angle as described in [Angle Measurement](angle.md), an *Angle* section is added to each frame in which the initiator sends messages at a constant rate until enough messages are sent for the responders to be able to calculate an angle.

!!!note ""
    The following figure is not to scale in regards to timings. See [Timings](#timings) for more information.
<figure markdown>
  ![Timeslots](img/time_slots.png)
  <figcaption>Time Slotting Overview</figcaption>
</figure>

Once all agents have become an intiator, a dead time (SYNC) is used to elect the new *SuperFrame Leader* (the agent who will be the initiator in the first timeslot) timeslots are then alloted based on agent IDs (modulo the max number of agents in the swarm).

!!!example
    Given a swarm of 5 agents with a *SuperFrame Leader* 3, slots (TWR Frames) would be alloted the following way:

     - **TWR Frame 1**: Agent 3
     - **TWR Frame 2**: Agent 4
     - **TWR Frame 3**: Agent 5
     - **TWR Frame 4**: Agent 1
     - **TWR Frame 5**: Agent 2

!!!note
    Timeslots are alloted based on the maximum number of agents in the swarm and the indivual agents IDs (configurable through the HiveMind build system, [see README](https://github.com/SwarmUS/HiveMind)). If there are less agents present than the maximum allowed value, some slots will remain empty. The same way, if an agent has an ID exceeding the maximum number of agents, it will not be able to interact properly with the other agents in the timeslots and will probably even break the slots for everyone.

    :warning: **TLDR: Make sure the max number of agents in the swarm is greater or equal to the agent with the highest ID.**

### Definitions
The following section contains most definitions used within the code and documention relating to the timeslotting mechanism and the state machine implementing it.

**SuperFrame**: Highest level of the timeslots, allows every agent to have it's own **TWR Frame**

**TWR Frame**: Slot in which an agent is the initiator in a TWR sequence

**SuperFrame Initiator**: ID of the agent selected to use TWR Frame 1 during the SYNC state

**Frame Leader**: ID of the agent that is the initiator (sends the poll, final and angle messages) in the current TWR Frame

### Timings

## State Machine Implementation


### Idle
### Sync
### Send Poll
### Wait Response
### Send Final
### Send Angle
### Wait Poll
### Send Response
### Wait Final
### Receive Angle
### Update Distance + Angle

