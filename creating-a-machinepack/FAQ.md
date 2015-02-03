# FAQ _(for implementors)_

Writing machines?  Building a new machinepack?  Contributing to an existing pack? Here are the answers to some frequently asked questions from implementors like you.

### Can I use another machine from inside one of my machines?

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

