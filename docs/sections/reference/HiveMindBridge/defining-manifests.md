# Defining Manifests

The manifest is a set of information that describes the different input arguments for each registered callback. Since the other swarm agents can ask a robot to list every function it can manage, there must be a way to describe the arguments that each function takes. This is the role of the manifest.

The manifest is simply an vector of arguments and their name that is typedefed as `CallbackArgsManifest`.

Let's say we have a function `moveBy` that takes two aguments: `float x` and `float y`. Resigtering arguments in a manifest is done thusly:

```cpp
CallbackArgsManifest moveByManifest;
moveByManifest.push_back(
    UserCallbackArgumentDescription("x", FunctionDescriptionArgumentTypeDTO::Float));

moveByManifest.push_back(
    UserCallbackArgumentDescription("y", FunctionDescriptionArgumentTypeDTO::Float));
```

> The user must pay close attention to make sure that the order in which the arguments are registered in the manifest match the order of the arguments used in the corresponding callback function. Here, the callback expects that `x` is at position [0] and that `y` is at position [1] in the input arguments. Make sure to validate the order of the arguments across all the elements of the swarm when you debug.