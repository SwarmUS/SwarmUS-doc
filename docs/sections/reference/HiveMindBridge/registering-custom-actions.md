# Registering Custom Actions

Registering custom actions is always done by following three steps: 

1. Define the callback that describes the behaviour of the custom action (see [Defining Callbacks](defining-callbacks.md)).
2. Define the manifest that describes the input arguments of the callback (see [Defining Manifests](defining-manifests.md). This is optional, as some callbacks will not require any input arguments)
3. Register the custom action in the `HiveMindBridge` object.

This page explains the registration of the custom action in the `HiveMindBridge` object. 

!!! note
    The full API documentation for HiveMindBridge is hosted [here](https://swarmus.github.io/HiveMindBridge/classIHiveMindBridge.html).

## Custom Actions Are Discoverable

Swarm agents can discover the capabilities of the other agents by querying the list of their registered functions. To ensure the discoverability of a robot's function, users must [define a manifest](defining-manifests.md) and register the callback in the `HiveMindBridge` object. The registration of custom actions is the process of associating the function's name with a function pointer and a manifest. Let's say we have a `moveBy(float x, float y)` function:

```cpp
CallbackArgsManifest moveByManifest;
moveByManifest.push_back(
    UserCallbackArgumentDescription("x", FunctionDescriptionArgumentTypeDTO::Float));
moveByManifest.push_back(
    UserCallbackArgumentDescription("y", FunctionDescriptionArgumentTypeDTO::Float));

bridge.registerCustomAction("moveBy", moveByCallback, moveByManifest);
```

The last line shows the association between the name, the function pointer, and the manifest. The registration also allows HiveMindBridge to handle the incoming function call requests.