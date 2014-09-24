# HMVC

The H of HMVC stands for hierarchical. The principle is that software applications can be built up hierarchically, through the use of composition of other self contained MVC triads. Each MVC triad is only aware of a single incoming request, to a controller, and the request is dispatched as appropriate. The following diagram should help solidify the concept:

![HMVC Triads](../resources/images/hmvc.png)

The power of this concept lies in the simplicity of its implementation, small reusable blocks that make up a larger more complex system. By developing stand alone, self contained MVC triads and components one can build up a system out of predefined parts, in much the same way as you build a house out of lego. It also makes testing a great deal easier, and components can be developed independently and concurrently, thus speeding up development again.