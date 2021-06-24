# Defining Callbacks

Defining a callback should always be done by using the provided typedefs. The signature of the callback is the following:

```cpp
CallbackFunction myCallback = [&](CallbackArgs args,
                                        int argsLength) -> std::optional<CallbackReturn> {
    
}
```

Notice how the function takes an argument of type `CallbackArgs`. This typedef is an array of arguments with a maximum size of 8. The arguments are wrapped in `FunctionCallArgumentDTO` objects, which can hold either `int`s or `float`s (this is implemented using `std::variant`).

The `argsLength` indicate the number of arguments that were provided by the caller, and should be used for checks.

The return value of the callbacks is always a type `std::optional<CallbackReturn>`. Why optional? Because some functions do not need to return any payload to the caller. In those cases, the user will simply `return {}` at the end of the function's body.

When a function should return some payload, the data should be wrapped in a `CallbackReturn` object.

## `CallbackReturn` objects

`CallbackReturn` objects should be used to return some payload to the caller of a callback. When the payload is returned, _it is wrapped in a function call request_, and the payload is passed as arguments. For example, let's say we have registered a function `getStatus()` on the robot that returns two values of type int. The two values indicate arbitrary states for elements in the robot (e.g. is the Roboclaw controller ok, is a particular sensor working properly, etc.).

```cpp
CallbackFunction getStatus = [&](CallbackArgs args,
                                     int argsLength) -> std::optional<CallbackReturn> {
        // Example values
        int64_t isRoboclawOk = 1;
        int64_t isSensorXOk = 0;

        CallbackArgs returnArgs;
        returnArgs[0] = FunctionCallArgumentDTO(isRoboclawOk);
        returnArgs[1] = FunctionCallArgumentDTO(isSensorXOk);

        CallbackReturn cbReturn("getStatusReturn", returnArgs);

        return cbReturn;
    };
```

When the data is returned, it is wrapped by HiveMindBridge in a function call request that will be sent to the original caller of the function. The user needs to provide the name of the function to call that will handle the return payload. In the example above, the returned data is sent to the caller by calling the `getStatusReturn` function. This function must be registered on the caller's side in order to process the reception of the return payload.

> Notice how the return payload values are placed in a `CallbackArgs` object: this is because they will be passed to the `getStatusReturn` function as arguments.

Here is a summary of what happens :

```
Remote caller                                               Robot
|                      getStatus()                          |
|---------------------------------------------------------->|
|                                                           |
|                                                           |
|     getStatusReturn(isRoboclawOk, isSensorXOk)            |
|<----------------------------------------------------------|

```

> When you implement you swarm solution, make sure to check that the return function calls are implemented on the remote caller, this will be a frequent cause of bugs. Also, do make sure that the arguments match with the return values of the callback.

## Asynchronicity

Callbacks are _always_ run asynchronously. This allows the user to make blocking calls in the callbacks, without any impact on the rest of the execution flow. Do not hesitate to implement complex logic that takes lots of time to be processed in your callbacks: they were designed for that.

```cpp
CallbackFunction myCallback = [&](CallbackArgs args,
                                        int argsLength) -> std::optional<CallbackReturn> {
    // some function logic...

    std::this_thread::sleep_for(std::chrono::milliseconds(2000)); // This is perfectly fine, even in a callback!
}
```