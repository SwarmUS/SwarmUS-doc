One of the main goal of the HiveAR app is to visualize the state of the Swarm directly from an Android device.

## Augmented Reality (AR): <a name="ar"></a>

Augmented Reality is a quick diagnostic tool that is used mostly to get the immediate state of visible agents, but also to send commands to specific agents.
At a close range, if the device is pointed towards the [AprilTag](https://april.eecs.umich.edu/software/apriltag) on top of a specific agent, details of this agent will appear on top of it.

!!! Attention
  April tags are not detected from afar, which is why to scan it, it should be better to direct the android device directly at the tag from a distance about 0.8 to 1.2 meters. (Results might vary from the device's camera resolution)

Information contained in that view are:

- Last time agent was updated
- Status of the agent (User must define the comportement as it can differ from one agent to the next)
- Last commands sent from the Android device to the agent
- Commands associated to the selected agent that can be sent

<figure markdown>
  ![AR View](img/ar_tab_robot_command.jpg){: style="height:700px"}

  <figcaption>AR agent selected example</figcaption>

</figure>

!!! note
    It is possible to have an AR marker in the space without being on top of an agent if this agent has moved and the Android device hasn't detected it.
    It will still monitor the agent even if it is not on top of it, but if one desire to remove the marker, simply long click it and agree to remove it.

!!! Tip
    If multiple agents are detected, it is possible to focus on one by clicking its corresponding AR marker. (arrow)

## Messages Logging: <a name="logs"></a>

Logs are a great way to know what information came from or to the Android device.
They can be found in the Connection tab.
They can help to see if a command came through, or what were the arguments of the incoming function call.

<figure markdown>
  ![Logs](img/connection_tab_connected_logs.jpg){: style="height:700px"}

</figure>

!!! Tip
    Logs can be shortened or detailed whenever needed with the "Detailed logs" checkbox.