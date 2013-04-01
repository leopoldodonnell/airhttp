Air Http Daemon
========

**Air Http** is a flex library that enables developers to add an HTTP Deamon inside an AIR application. This simple AIR webserver
makes it easy to extend access to functions and status within an application. This is especially useful if you've got to communicate
from a webpage (over localhost) without the use of FLASH communication. At the time this was written, for example, Chrome's sandbox
would prevent communication with other FLASH components that were in other processes.

The server, com.airhttp.HttpServer, is simple to incorporate and provides the ability to server files found under a specified
directory within your *applicationStorageDirectory*, or extend with *ActionControllers* that implement actions in code you
supply.

Version
-------

This is version 0.1.0

* Only supports GET requests
* Still needs to be fully tested
* Its working with Flash Build 4.7 and SDK 4.6.0

Adding the HTTP Server to Your Application
-----

Adding the **Air Http** server to your application is fairly simple (it must be an AIR application). All classes are found under
*com.airhttp*. To instantiate an start your server, add the following code to one of your application classes that will manage the
server.

    // import the package
    import com.airhttp.*;
    
    // declare your server
    private var weberv:HttpServer;
    
    // instantiate the server on port 4567
    webserv = (new HttpServer());
    var isListening:Boolean = webserv.listen(4567);
  
Extending your Application to HTTP with ActionControllers
---------------------------------------------------------

*com.airhttp.ActionController* is the base class for controllers. Extending this class to do something, or
respond with some data is very simple.

* Subclass com.airhttp.ActionController
* Provide a unique route string to lookup the controller
* Provide actions, named as methods, that provide some function and return a String or ByteArray response.
* Register this controller with your com.airhttp.HttpServer instance.

**Example - ** Display Application Status

In AppServiceController.as ...

    class AppServiceController extends com.airhttp.ActionController
    {
      private var _theApp:Object;
      
      public function AppServiceController(theApp:Object)
      {
        super('/app');
        _theApp = theApp;
      }
      
      /**
      * Respond with list of application stats, or a single
      * stat if it's name is passed in the URL with the 'name'
      * parameter.
      * 
      * ie. http://localhost:4567/app/status?name='uptime'
      */
      public function status(params:URLVariables):String
      {
          var items:String = "<ul>";
          var stats:Object = _theApp.statusValues;
          
          // Return one of the stats?
          if (params.hasOwnProperty('name')) {
            items += "<li>" + params.name + " : " + stats[params.name] + "</li></ul>";

            // Respond with success!
            return responseSuccess("<h1>Status</h1>" + items);
          }

          // Return all of the stats
          for (var itemName:String in stats) {
              items += "<li>" + itemName + " : " + stats[itemName] + "</li>";
          }
          items += "</ul>";
          
          // Respond with success!
          return responseSuccess("<h1>Status</h1>" + items);
      }
    }
    
Then in your webserver managing class ...

    // myApp is a pointer to a class that has "statusValues"
    webserv.registerCalback(new AppServiceController(myApp));
    
Then from a web browser

    http://localhost:4567/app/status
    
Building
--------

To build airhttp you need the flex sdk and ant. 1st edit FLEX_HOME in build/build.properties and then type the following commands from
the top level directory.

    > cd build
    > ant

License
-------

This project rocks and uses MIT-LICENSE. Copyright 2013 Leopold O'Donnell