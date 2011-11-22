

&ltdiv class="hero-unit">

<a class="hiddenLink" id="using-timer-functions-correctly"></a>

## Using Timer Functions Correctly
<span class="cite">by Nico Reed (Last Updated: Aug 26 2011)</span>


There are two built-in timer functions: `setTimeout()` and `setInterval()`. They can be used to call a function at a later time. For example:

    setTimeout(function() { console.log("setTimeout: It's been one second!"); }, 1000);
    setInterval(function() { console.log("setInterval: It's been another second!"); }, 1000);

The output of this code is:

    setTimeout: It's been one second!
    setInterval: It's been another second!
    setInterval: It's been another second!
    setInterval: It's been another second!

As you can see, the parameters for both are the same. The second parameter indicates how long to wait (in milliseconds) before calling the function passed into the first parameter. The difference between the two functions is that `setTimeout()` calls the callback only once while, `setInterval()` calls it over and over again.

Obviously, you want to be careful with `setInterval()`, since it can cause some undesirable effects.  If, for example, you want to make sure your server was up by pinging it every second, you might think to try something like this:

    setInterval(ping, 1000);

This can cause problems, however, if your server is slow and it takes, say, three seconds to respond to the first request. In the time it takes to get back the response, you would have sent off three more requests&mdash;not exactly desirable!  This isn't a problem when serving small static files, but if you're doing an expensive operation, like a database query or a complicated computation, this can have some undiserable results. 

A more common solution looks like this:

    var recursive = function () {
        console.log("It has been one second!");
        setTimeout(recursive,1000);
    }
    recursive();

This code makes a call to the `recursive()` function which, as it completes, makes a call to `setTimeout(recursive, 1000)` which makes it call `recursive()` again after one second. This has nearly the same effect as `setInterval()`, while being resilient to the unintended errors that can pile up.

You can clear the timers you set using `clearTimeout()` and `clearInterval()`. Their usages are very simple:

    function never_call() {
        console.log("You should never call this function");
    }

    var id1 = setTimeout(never_call,1000);
    var id2 = setInterval(never_call,1000);

    clearTimeout(id1);
    clearInterval(id2);

If you keep track of the return values of the timers, you can easily unhook the timers. 

The final trick for the timer objects is passing parameters to the callback. This is done by passing more parameters to `setTimeout()` and `setInterval()`, like this:

    setTimeout(console.log, 1000, "This", "has", 4, "parameters");
    setInterval(console.log, 1000, "This only has one");


The output then becomes:

    This has 4 parameters
    This only has one
    This only has one
    This only has one
    This only has one
    This only has one
    ...&lt/div>