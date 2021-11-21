## Hiveboard Network Configuration
Please refer to [this section](../Networking/configure-wifi.md#setup-hiveboard-network-hivear) for information about setting the network configuration (such as SSID and password) of a Hiveboard.

## Associate AprilTags to an Agent in the Swarm: <a name="robot-april-tags"></a>
To use augmented reality, AprilTags must be useed to identify swarm agents.
[AprilTags](https://april.eecs.umich.edu/software/apriltag) are incoded integers that are used as unique identifiers inside of HiveAR. 

By default, a tag's id will be associated to the same agent id. Hence, an AprilTag #1 will be considered attached to the swarm agent with id 1.
It is possible to override this behaviour by associating manually AprilTags with agent ids.

In the settings tab, one can make any wanted associations for the session.

<figure markdown>
  ![April tag association](img/settings_tab_april_tags.jpg){: style="height:700px"}

  <figcaption>Example of association</figcaption>

</figure>

!!! Tip
    One agent can have multiple different AprilTags associated with, or can have multiple physical AprilTag with same value on top of the agent.

!!! Attention
    It is **not** possible to associate the **same** AprilTag to **different** agents.