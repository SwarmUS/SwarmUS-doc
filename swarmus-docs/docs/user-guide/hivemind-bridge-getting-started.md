# Getting Started with HiveMindBridge

This page presents an overview of the steps necessary in order to start a minimal implementation of HiveMindBridge. Let us first describe the scenario that this quickstart guide aims to accomplish.

We will instantiate HiveMindBridge on a robot that has moving capabilities in 2D. The robot will expose two simple functions to the swarm. Both functions are callable by other swarm agents or by the robot's HiveBoard:
* `moveBy(float x, float y)`: this function will forward moving commands to the robot's navigtion stack. `moveBy` is the entrypoint to control the robot's position remotely. The function does not return any payload.
* `getStatus()`: this function will return some payload containing some information about the robot's state.

This (minimal) configuration will yield a simple robot that can be remote-controlled and that can inform on its state.

## `HiveMindBridge`: the unique entrypoint to the library

HiveMindBridge is a simple library that supports registering asynchronous callbacks to serve as a bridge between the communication services encapsulated in the swarm services and the local ROS environment. The user will mainly use the `HiveMindBridge` class to register custom actions, send some data over the messaging service and control the execution flow.

Instantiating a `HiveMindBridge` object is done using the class' only constructor : 

```cpp
// HiveMindBridge.h
HiveMindBridge(int tcpPort, ILogger& logger)
```

HiveMindBridge uses a TCP socket underneath and requires that the user provide a port for the connection. The second input argument is a logger that must be injected. The logger must implement the `ILogger` interface (a basic working implementation is provided [here](../reference/HiveMindBridge/logger.md)).

## Full example

The following listing shows the basic example, where the two aforementioned functions are registered (`moveBy(x, y)` and `getStatus()`). This example is used within a ROS application context. We left the ROS-specific calls in the example to ease understanding. Note however that HiveMindBridge is completely ROS-agnostic, and could be used in a plain C++ project.

We will break everything down after this listing.

```cpp
#include "hivemind-bridge/Callback.h"
#include "hivemind-bridge/HiveMindBridge.h"
#include "ros/ros.h"
#include "swarmus_ros_navigation/MoveByMessage.h"
#include <cpp-common/ILogger.h>
#include <cstdarg>
#include <optional>
#include <pheromones/FunctionCallArgumentDTO.h>

int main(int argc, char** argv) {
    ros::init(argc, argv, "hive_mind_bridge");
    ros::NodeHandle nodeHandle("~");
    
    // ROS publisher: this will publish the moveBy command to the navigation stack
    std::string moveByTopic =
        nodeHandle.param("moveByTopic", std::string("/agent1/navigation/moveBy"));
    ros::Publisher moveByPublisher =
        nodeHandle.advertise<swarmus_ros_navigation::MoveByMessage>(moveByTopic, 1000);
    
    int port = 7001;
    Logger logger; // See above for more info on loggers. For this example, 
    // let's just assume the class is accessible within the scope of this file.

    // Create a HiveMindBridge object
    HiveMindBridge bridge(port, logger);

    // Register the first custom action: moveBy(x, y). This function will simply forward the x and y values 
    // to the navigation stack using a ROS publisher.
    CallbackFunction moveByCallback = [&](CallbackArgs args,
                                          int argsLength) -> std::optional<CallbackReturn> {
        swarmus_ros_navigation::MoveByMessage moveByMessage;

        moveByMessage.distance_x = std::get<float>(args[0].getArgument()); // The arguments are contained 
        // in `args`. The body of the function is where users should define the position of the arguments.
        moveByMessage.distance_y = std::get<float>(args[1].getArgument());

        // Publish on moveby topic
        moveByPublisher.publish(moveByMessage);

        return {}; // This function does not return any payload, so we return an empty struct
    };

    // Register the function's manifest, name and pointer in our `HiveMindBridge` object.
    CallbackArgsManifest moveByManifest;
    moveByManifest.push_back(
        UserCallbackArgumentDescription("x", FunctionDescriptionArgumentTypeDTO::Float));
    moveByManifest.push_back(
        UserCallbackArgumentDescription("y", FunctionDescriptionArgumentTypeDTO::Float));
    bridge.registerCustomAction("moveBy", moveByCallback, moveByManifest);

    
    // Register the second custom action: getStatus()
    CallbackFunction getStatus = [&](CallbackArgs args,
                                     int argsLength) -> std::optional<CallbackReturn> {
        int64_t isRobotOk = 1; // Let's assume there is some complex logic here 
        // to check the status of the robot's components...

        // Build the return payload and return. 
        CallbackArgs returnArgs;
        returnArgs[0] = FunctionCallArgumentDTO(isRobotOk);
        CallbackReturn cbReturn("getStatusReturn", returnArgs);
        return cbReturn;
    };

    // Register the name and pointer for the getStatus() function. 
    // Notice that no manifest is passed, since this function takes no input argument.
    bridge.registerCustomAction("getStatus", getStatus);

    // Register event hooks. HiveMindBridge provides a few event hooks that allow 
    // the user to control the execution flow.
    bridge.onConnect([]() { ROS_INFO("Client connected."); });

    bridge.onDisconnect([]() { ROS_INFO("Client disconnected."); });

    // The main application loop
    ros::Rate loopRate(RATE_HZ);
    while (ros::ok()) {
        ros::spinOnce();

        bridge.spin(); 

        loopRate.sleep();
    }

    return 0;
}
```

### Creating a `HiveMindBridge` object

```cpp
int port = 7001;
Logger logger; 

HiveMindBridge bridge(port, logger);
```

To create a `HiveMindBridge` instance, the user must provide a logger that implements the `ILogger` interface (see [here](../reference/HiveMindBridge/logger.md) for a full example). The `port` input argument is needed in order to bind the TCP socket used by HiveMindBridge. 

> HiveMindBridge acts as a TCP _server_ and will wait for clients to initiate a connection.

### Registering custom actions

The example above shows the registration of two functions, `moveBy(x, y)` and `getStatus()`. Both have their own characteristics which help illustrate the capabilities of HiveMindBridge.

Registering custom actions is always done by following three steps : 

1. Define the callback that describes the behaviour of the custom action (see [Defining Callbacks](../reference/HiveMindBridge/defining-callbacks.md)).
2. Define the manifest that describes the input arguments of the callback (see [Defining Manifests](../reference/HiveMindBridge/defining-manifests.md). This is optional, as some callbacks will not require any input arguments)
3. Register the custom action in the `HiveMindBridge` object (see [Registering Custom Actions](../reference/HiveMindBridge/registering-custom-actions.md)).

**Registering `moveBy(float x, float y)`**

The `moveBy(float x, float y)` function takes two arguments and returns no payload. This functon simply forwards the two input arguments to the navigation stack by publishing on a ROS topic.

```cpp
CallbackFunction moveByCallback = [&](CallbackArgs args,
                                        int argsLength) -> std::optional<CallbackReturn> {
    swarmus_ros_navigation::MoveByMessage moveByMessage;

    moveByMessage.distance_x = std::get<float>(args[0].getArgument());
    moveByMessage.distance_y = std::get<float>(args[1].getArgument());

    moveByPublisher.publish(moveByMessage);

    return {};
};

CallbackArgsManifest moveByManifest;
moveByManifest.push_back(
    UserCallbackArgumentDescription("x", FunctionDescriptionArgumentTypeDTO::Float));
moveByManifest.push_back(
    UserCallbackArgumentDescription("y", FunctionDescriptionArgumentTypeDTO::Float));
bridge.registerCustomAction("moveBy", moveByCallback, moveByManifest);
```

Since the function returns no payload, the user must simply return an empty struct.

> Notice that the order of the arguments match between the manifest and the callback's body. This allows the other swarm element to send the arguments correctly. For more information on this, see [Defining Manifests](../reference/HiveMindBridge/defining-manifests.md).

**Registering `getStatus()`**

The `getStatus()` function takes no input arguments, but will return some payload. The return payload is wrapped in  a CallbackReturn type that takes the return values in an array. This is because the return payload is wrapped in a function call request that will be placed on the remote caller. See [Defining Callbacks](../reference/HiveMindBridge/defining-callbacks.md) for more details.

```cpp
    CallbackFunction getStatus = [&](CallbackArgs args,
                                     int argsLength) -> std::optional<CallbackReturn> {
        int64_t isRobotOk = 1; 

        // Build the return payload and return. 
        CallbackArgs returnArgs;
        returnArgs[0] = FunctionCallArgumentDTO(isRobotOk);
        CallbackReturn cbReturn("getStatusReturn", returnArgs);
        return cbReturn;
    };

    bridge.registerCustomAction("getStatus", getStatus);
```

> This example does not have any real logic. In some cases, there could be a need to implement some complex logic, and even some blocking calls to check the state of some robot peripherals. Since all callbacks are run asynchronously, the user can use blocking calls in the body of the callback. See [Defining Callbacks](../reference/HiveMindBridge/defining-callbacks.md) for more information on this.

### Registering event hooks

HiveMindBridge provides some events that the user can attach hooks to, in order to control the execution flow. For example, the `onConnect` and `onDisconnect` event can fire user-defined callbacks when the TCP socket's state changes. This can prove useful to halt a robot in the event of a connection error.

```cpp
bridge.onConnect([]() { ROS_INFO("Client connected."); });

bridge.onDisconnect([]() { ROS_INFO("Client disconnected."); });
```

### Spinning the Bridge

HiveMindBridge has a main thread that must attach to the main application loop. The `HiveMindBridge` object must be spun for it to process the incoming data.

```cpp
// The main application loop
ros::Rate loopRate(RATE_HZ);
while (ros::ok()) {
    ros::spinOnce();

    bridge.spin(); // Spinning the bridge

    loopRate.sleep();
}
```