# FAQ _(for implementors)_

Writing machines?  Building a new machinepack?  Contributing to an existing pack?  Want to use this stuff internally for closed-source code? Here are the answers to some frequently asked questions from implementors like you.

### Can I build machines without open-sourcing them?

Yep!

### How do I use my one-off/custom/closed-source machines that aren't part of a pack?

You'll want to use the `.build()` function in the machine runner:

```js
// First use the Node.js machine runner to build the machine from its definition:
var myMachine = require('machine').build(yourMachineDefinitionObject);

// Then just use it like any other machine:
myMachine({ someInput: 'foo' }).execSync();
```

### How do I use a custom/closed-source machinepack that isn't on NPM?

Just require it from wherever it's located, e.g.:

```js
var pack = require('../../path/to/that/sexy/sexy/pack/folder');
```

### Why should I build my API or web app with machines?

Machines make your app more stable and maintainable by encouraging thin controllers, encapsulation, and reusability.  It protects your app from errors by providing strongly typed inputs and return values, and by catching errors that would normally crash your app.  Not to mention that it nurtures an outcome-oriented approach, allowing function users _and_ implementors to think exit conditions, not error codes.  Finally, you can generate detailed documentation pages for any logic you choose to implement as machines.

You don't have to implement your entire app as machines from the beginning-- in fact, you can start whenever you like, and use as few or as many machines as makes sense.  We've found that machines are a lot like any other new programming paradigm in that it's best to start casually (_when you have a hammer, everything looks like a nail_ and all that).  So maybe think about it like this: next time you're thinking "hey I need to pull this out into a function...", try writing a machine instead.


### Can I use a machine from _inside_ the `fn` of one of my machines?

Indeed, just npm install desired machinepack like you would anywhere else:

```bash
npm install machinepack-other --save
```

And then use it normally:

```js
var Other = require('machinepack-other');
Other.doStuff({someInput: 'stuff'}).exec({
  error: function (err){
    
  },
  success: function (){
  
  }
});
```


### What about another machine from the same pack?  Or a machine that needs to call itself recursively?

If you want to use another machine in the _same pack_, or you want a machine to call itself recursively, you should `require('machine')` and use it to build a new instance of the machine from its "dehydrated" definition object.  Don't worry, that sounds terrible and complicated, but it's really pretty dern simple.

Take for example the `sanitize` machine in `machinepack-urls`.  It uses another machine from the same pack (`validate`) to ensure that the sanitized URL is valid before returning it.  Something like this:

```js
// The raw machine definition we're looking for is in the machines directory of this machinepack.
// But since the current code file is in the machines folder too, we can get the raw definition for
// the `validate` machine simply requiring it using a relative path based on its "identity".
var validateUrl = require('machine').build(require('./validate'));

// Then we can use the hydrated machine just like we would if we had gotten it out of a machinepack:
validateUrl({string: 'stuff'}).exec({
  error: function (err){ /* ... */},
  invalid: function (){ /* ... */ },
  success: function (){ /* ... */},
});

```
> To view this example in context, take a look at the [relevant lines in the source code for machinepack-url/sanitize](https://github.com/mikermcneil/machinepack-urls/blob/5153f138280b2385cc35e1bee54c50c8e155fb70/machines/sanitize.js#L29)




### Can I build custom machines with... `<INSERT_PLATFORM_HERE>`

You can build and use machines anywhere you like!  And you can use the related NPM modules (like `machine`) from any environment that supports Node.js.  For instance, you might want to implement a one-off machine in your app to take advantage of caching or type-checking this module provides.  We've found that the larger your project, the more important it is to have strong conventions for using, implementing, and sharing code.  The machine spec is designed for any situation where you are writing code you intend to reuse.

Whether you're using Sails, another framework like Connect/Express/Hapi/Kraken, or just building something with raw Node.js, you can install and require machinepacks just like any other node modules.  The big difference between a machinepack and any other node module is that there is a generated web page on node-machine.org that thoroughly documents each piece of functionality.

### Can I build custom machines in my Sails app that use my models and config and such?

If you're working on a large Sails app, check out [`sails-hook-machines`](https://github.com/node-machine/sails-hook-machines), a hook which allows you to use custom closed-source machines in your Sails app by dropping files into the `api/machines/` folder.

These machines will have access to any globals you might want to expose (e.g. conventionally you'll have access to the `sails` object as well as all of your models), which means you can use them as a more structured alternative to custom model methods and services.  If you have globals disabled, you can use the machine `environment` to provide context (ask in the Google Group if you have questions on that)

> **Warning**
>
> If your machine relies on globals or an `env`, it will not necessarily be reusable in other applications, or to other developers.  If you're writing a machine that you plan to reuse outside of a particular app, don't rely on `env` or globals... and please consider contributing it to a new or existing open-source machinepack :)



### Can I run my own version of node-machine.org?

Indeed!  The public machine registry (i.e. node-machine.org) is an MIT licensed Node.js app.  That means you can host your own version of the registry anywhere that supports Node.js.

Here are some use-cases for standing up your own registry:

+ quick way to set up documentation pages for the methods in your open-source module
+ create public-facing documentation pages for a developer tool-- e.g. the Node.js SDK for your company's API.  You can also deploy the registry behind your existing user account system to allow access based on roles/permissions.
+ a solution for automatic documentation of private/closed-source modules internally within your organization. A popular option is installing the registry as a quick way to view documentation when you're behind your organization's firewall/VPN/moat/hedgemaze/etc.  This works particularly well for organizations who are also running a private NPM registry.

Hit up @mikermcneil on Twitter or in the Google Group if you have questions or need help getting set up.


### Can I build machines using CoffeeScript?

Yes, but since machines are reusable and likely to be shared with other developers, I'd say should consider using JavaScript instead. But that's just my 2¢ :)

### Can I use machines in the browser?

Sort of.  We need to make it easier with some tooling on top of browserify and a Gulp/Grunt task.

If you're not using `require()` or other Node.js-specific globals (e.g. `process` or `module`), your machines should work in the browser already (use browserify).  If you _are_ using those things, depending on what they do, your machines _might_ work in the browser thanks to Browserify.  Whether or not browser usage is _appropriate_ is an entirely different issue-- for instance, you shouldn't use an ElasticSearch machine the browser if your ElasticSearch instance requires authentication, because then that would mean you're exposing your credentials to browser-side JavaScript, which means anyone with half a brain could grab them using Chrome inspector and wreak havoc in your database.

### Can I build machines in other languages / for other platforms?

The machine specification currently only supports JavaScript, but the topic of expanding to other languages is under active discussion.  We need to make some backwards-compatible changes to the spec to allow for separating machine metadata into separate JSON files.  This would allow machines to be written in C, C++, Lisp, C#, Java, PHP, Python, Ruby, or any other Turing-complete language.

If you have ideas on how to do this, or just want to keep up with the conversation, join the Google Group and say hello.
