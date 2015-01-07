**Machinepacks** are simply bundles of machines that make it easy to aggregate related tasks that each machine performs.  For example, [machinepack-github](http://node-machine.org/machinepack-github) contains seven machines ranging from fetching a particular repo to listing repo activity.  The `machinepack` can then be required like any other `npm` module.
    
####Using machinepack-github's `.getRepo` machine.
```javascript
var Github = require('machinepack-github');

// Fetch metadata about a repository on GitHub.
Github.getRepo({
  repo: 'sails',
  owner: 'balderdashy',
}).exec({
  // An unexpected error occurred.
  error: function (){
  },
  // OK.
  success: function (result){
    console.log('Got:\n', result);
  },
});
```
