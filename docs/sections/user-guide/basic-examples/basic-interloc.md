# Using Interlocalisation Between Swarm Agents

One of the key features of the SwarmUS platform is the ability for a given Hiveboard to determine the position of a second Hiveboard with respect to its own position. This page shows how to use this *interlocalisation* feature. SwarmUS' interlocalisation feature provides the necessary data to the [neighbor feature of the Buzz language](https://the.swarming.buzz/wiki/doku.php?id=buzz_syntax_cheatsheet&s[]=neighbors). This means that the HiveMind firmware merely updates the position of the other agents and makes it available to Buzz.

<!-- TODO 
!!! Note
    The interlocalisation module is a rather complex feature of the SwarmUS platform. For extensive documentation about the design and implementation of the feature, refer to TODO.

-->

!!! attention
    Before you start using the interlocalisation feature, refer to the instructions on [how to calibrate](../../reference/Interlocalisation/how-to-calibrate-interloc.md) the BeeBoards with a HiveBoard. Since the interlocalisation feature relies on very sensitive hardware, it is crucial that the HiveBoard/BeeBoard assembly be adequately calibrated, otherwise the readings will be rubbish.

## Using the Visualisation Tool

TODO

## Accessing Neighbors' Position in a Buzz Script

Like said earlier, Buzz has a neighbor management feature, which allows to access the position of the other agents' position, among other things.

!!! note
    Since neighbor management is a feature of Buzz, it will not be extensively documented here. Refer to the [official documentation](https://the.swarming.buzz/wiki/doku.php?id=buzz_syntax_cheatsheet&s[]=neighbors).

The SwarmUS platform feeds the interlocalisation data to Buzz, which itself makes it available to the user via the `neighbors` structure. The following snippet shows how the `neighbors` structure might be used. This Buzz script performs a _follow the leader_ example in which the agents will move towards the agent #1.

```python
include "utils/executor.bzz"
include "vec2.bzz"

function tick(context){
    var leader = 1;

    if(leader != id and leader != nil){
        log("Trying to get leader: ", leader)
        var neighbor = neighbors.get(leader);

        if(is_table(neighbor)){
            var goal = math.vec2.newp(neighbor.distance - 0.5, neighbor.azimuth);
            log("Moving to leader: ", leader, " at x:", goal.x, " y:", goal.y);
            call_host_function(id, "moveBy", {.0 = goal.x, .1 = goal.y});
        }
        else{
            log("Neighbor not found!");
        }
    }
}

function create_exec(step_count) {
  counter = 0;
  stig = stigmergy.create(1);

  stig.put("leader", 1);

  exec = executor.new(step_count, tick, nil);
  return exec;
}
```

!!! hint
    The Buzz documentation has [some other examples](https://the.swarming.buzz/wiki/doku.php?id=buzz_examples) of scripts that make use of the `neighbors` structure. The HiveMind repository also has a number of [examples](https://github.com/SwarmUS/HiveMind/tree/master/src/bittybuzz/buzz_scripts/behaviors).

## Accessing Neighbors outside of Buzz

The interlocalisation data can also be accessed outside of Buzz. The [HiveMind API](../../reference/HiveMind/hivemind-api.md) exposes neighbor requests (`GetNeighborsListRequest`, `GetNeighborRequest`) that can be called by any host.

The calls to the HiveMind API for updating neighbors have been convieniently wrapped in the [HiveMindBridge library](https://swarmus.github.io/HiveMindBridge/classIHiveMindBridge.html).