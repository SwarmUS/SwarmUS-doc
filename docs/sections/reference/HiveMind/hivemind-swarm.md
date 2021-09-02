# HiveMind and the Swarm

The HiveMind makes uses of function calls as way to interact with different components and those functions are called by name. As a user, you define the functions in the buzz script or on the host and use them between the components. The functions are registered with a description of it arguments (name and type) and a callback (the actual function), it is then possible to call this function from another component in the swarm. 

Those functions calls does not have return values, so instead it's possible to call another function and the "return value" will be an argument of the function call. 
For example: The buzz script calls `getStatus()` on it's local host, the message is rooted by the HiveMind and sent to the host. The host could then call `returnGetStatus(status)` to "return" the value via the arguemnt. Again the message is rooted by the HiveMind to the buzz script which would have a registered `returnGetStatus` function that uses the argument value as the "return value". This makes sure all messages are treated as event since function calls cannot directly return and so nothing is blocking.

Here by "user" we means someone either someone that wants to implement swarm behavior, or someone that implement the code for a robot to be a host for the HiveMind. The swarm behavior will mostly write buzz script, while the host robot will implement the required interface to communicate with the HiveMind (Generally the [HiveMindBridge](https://github.com/SwarmUS/HiveMindBridge) will be used). <!-- TODO: Change link to HiveMindBridge peface?-->
