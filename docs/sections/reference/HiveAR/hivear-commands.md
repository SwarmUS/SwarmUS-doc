## Swarm agents commands: <a name="hivear-commands-swarm"></a>
Agents in the same swarm than the android device are listed in this tab. (picture)
If selected, commands specific to this agents are then listed and can be used to directly send command to this agent.

!!! Tip
    If an agent doesn't have its comands listed, it is possible to force to refresh its commands with the refresh button.

## Swarm broadcast commands: <a name="hivear-commands-broadcast"></a>
It is possible to send commands to all agents in the swarm. 
In order to register commands to broadcast, the user has to long click any commands from the agents commands view or device commands view.
Registered commands would then be separated between host and buzz command types and can both be send to any agents.

!!! Attention
    Not every sent broadcast command will be handled be every agents depending on whether the agent can handle this command.
    If an agent can't handle the command, it will simply discard it.

## Device buzz commands: <a name="hivear-commands-device-buzz"></a>
As any other agent in the swarm, it is possible that the hiveboard connected to the android device has its own buzz commands registered.
It is from this view that the user can use those commands.