## Associate april tags to an agent in the swarm: <a name="robot-april-tags"></a>
To use augmented reality, april tags must be use to identify swarm agents.
[April tags](https://april.eecs.umich.edu/software/apriltag) are incoded integers. 

By default, a tag's id will be associated to the same agent id. Hence, an april tag #1 will be considered attached to the swarm agent with id 1.
It is possible to override this behaviour by associating manually april tags with agent id.

In the settings tab (lower right), one can make any wanted associations for the session,

!!! Tip
    One agent can have multiple different april tags associated with or can have multiple same april tag on the agent.

!!! Attention
    It is not possible to associate the same april tag to different agents.