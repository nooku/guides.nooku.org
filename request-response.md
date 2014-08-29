# Request and Response

The entire request and response cycle in Nooku is pretty straightforward, but there are many parts to the puzzle and it is important to understand how all of the bits fit together.

Think about the request/response cycle within HTTP:

1. HTTP request sent
2. Web server recieves the request and routes the request to the corresponding file
3. File type handler is invoked (e.g. PHP) and file is executed/loaded
4. Handler returns a response to the web server
5. Web server returns a response to the browser

We can think about the above in terms of MVC:

1. Request
2. Controller
3. Model & View
4. Controller
5. Response

![request / response cycle](http://motda.be/nooku/mvc2.jpg)

Nooku is built to model this same process flow through the entire framework.

Now think about how in Apache you can load in modules. Modules allow you to extend the existing functionality of Apache, without modifying its core underlying code. Modules can do all sorts of things, from handling routing (mod_rewrite) to compressing the output sent to the browser (mod_deflate). Nooku employs the same techniques by using behaviors. 

Behaviors can be used to modify the input and output of the controller, model or the data layer without having to modify the underlying code. This becomes incredibly powerful, as one can affect the way a system operates just by wiring in some behaviors, behaviors that can be re-used amongst lots of different projects too.

Nooku uses behaviors for all sorts of tasks, from automatic alias creation, to caching and activity moitoring.

## How it works

The initial request that is recieved by the web server is routed to a dispatcher within the application component. 

A dispatcher is responsible for: 

* Deconstructing the request into an internal route
* Handling authentication & authorization
* Dispatching the request to the appropriate controller
* Handling exceptions
* Returning the response, including the body and the appropriate headers, to the browser