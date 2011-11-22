

&ltdiv class="hero-unit">

<a class="hiddenLink" id="storing-local-data"></a>

## Storing Local Data
<span class="cite">by Charlie McConnell (Last Updated: Aug 26 2011)</span>


Storing your Node.js application's configuration data is quite simple. Every object in Javascript can be easily rendered as [JSON](#what-is-json), which in turn is just string data that can be sent or saved any way you'd like. The simplest way to do this involves the built-in `JSON.parse()` and `JSON.stringify()` methods.

Let's take a look at a very simple (and contrived) example.  First, to save some very simple data:

    // always require('fs') when using the file system  
    var fs = require('fs');

    var myOptions = {
        name: 'Avian',
        dessert: 'cake'
        flavor: 'chocolate',
        beverage: 'coffee'
    };

    var data = JSON.stringify(myOptions);

    fs.writeFile('./config.json', data, function (err) {
        if (err) {
          console.log('There has been an error saving your configuration data.');
          console.log(err.message);
          return;
        }
        console.log('Configuration saved successfully.')
  });

It's really that simple: just `JSON.stringify()`, and then save it however you'd like.

Now, let's load some configuration data, by reading from the file:

    var fs = require('fs');

    var data = fs.readFileSync('./config.json'),
        myObj;

    try {
        myObj = JSON.parse(data);
        console.dir(myObj);
      } catch (err) {
        console.log('There was an error parsing your JSON.')
        console.log(err);
    }

Even if you don't like using `try/catch`, this is a place to use it.  `JSON.parse()` is a very strict JSON parser, and errors are common&mdash;most importantly, though, `JSON.parse()` uses the `throw` statement rather than giving a callback, so `try/catch` is the only way to guard against the error. In addition, since we're again loading conifiguration data, we need to rely on the synchronous `fs.readFileSync()`.

Using the built-in `JSON` methods can take you far, but as with so many other problems you might be looking to solve with Node.js, there is already a solution in Userland that can take you much further. Written by Charlie Robbins, `nconf` is a configuration manager for Node.js, supporting in-memory storage and local file storage. There's also support for a `redis` backend, provided in a separate module.

Let's take a look now at how we'd perform some local configuration access with `nconf`.  First, you'll need to install it to your project's working directory:

    npm install nconf

After that, the syntax is a breeze. Have a look at an example:

    var nconf = require('nconf');

    nconf.use('file', { file: './config.json' });
    nconf.load();
    nconf.set('name', 'Avian');
    nconf.set('dessert:name', 'Ice Cream');
    nconf.set('dessert:flavor', 'chocolate');

    console.log(nconf.get('dessert'));

    nconf.save(function (err) {
        if (err) {
            console.error(err.message);
            return;
        }
        console.log('Configuration saved successfully.');
    });

The only tricky thing to notice here is the `:` delimiter. When accessing nested properties with `nconf`, a colon is used to delimit the namespaces of key names.  If a specific sub-key is not provided, the whole object is set or returned.

When using `nconf` to store your configuration data to a file, `nconf.save()` and `nconf.load()` are the only times that any actual file interaction will happen.  All other access is performed on an in-memory copy of your data, which will not persist without a call to `nconf.save()`.  Similarly, if you're trying to bring back configuration data from the last time your application ran, it will not exist in memory without a call to `nconf.load()`, as shown above.&lt/div>