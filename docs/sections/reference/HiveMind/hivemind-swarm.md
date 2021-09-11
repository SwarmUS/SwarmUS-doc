# HiveMind and the Swarm

The HiveMind makes uses of function calls as ways to interact with different components and those functions are called by name. As a user, you define the functions in the Buzz script or on the host and use them between the components. The functions are registered with a description of arguments (name and type) and a callback (the actual function). It is then possible to call this function from another component in the swarm. 

Those functions calls do not have return values, so instead it's possible to call another function and the "return value" will be an argument of the function call. 
For example: If the Buzz script calls `getStatus()` on its local host, the message is rooted by the HiveMind and sent to the host. The host could then call `returnGetStatus(status)` to "return" the value via the arguemnt. Again the message is rooted by the HiveMind to the Buzz script which would have a registered `returnGetStatus` function that uses the argument value as the "return value". This makes sure all messages are treated as events since function calls cannot directly return. This creates a system that is never blocking.
