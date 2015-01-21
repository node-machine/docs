**Machinepacks** are simply bundles of machines that make it easy to aggregate related tasks that each machine performs.  For example, [machinepack-github](http://node-machine.org/machinepack-github) contains seven machines ranging from fetching a particular repo to listing repo activity.  The `machinepack` can then be required like any other `npm` module.
    
###Using machinepack-github's `.getRepo` machine.
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

###Installing the `machinepack` _command-line_ tools

If you don't already have [Yoeman](http://yeoman.io/) installed, you can do so now by typing:

```sh
$npm install -g yo
```

Now, install the `yoeman` generator by **typing**:

```sh
$npm install -g machinepack
```

###Generating your first `machinepack`.
Let's say you're going to create a machinepack that will contain machines to access an api you've built called Tuneyards. 

To get started **type**:

```sh
$yo machinepack
```

You'll be prompted for a friendly `name`.   By entering _Klout_, the name will become `machinepack-klout`.

Next, you'll be asked to provide a `description`.  We'll enter, _Communicate with the Klout API to get Ids, Klout Scores, etc._

The generator gives you the option to create an example machine.  Let's go ahead and type `y` for yes and generate the machine.

Finally, you'll be prompted whether to put this example machine in a new folder within the machinepack or at the root of the machinepack.  Let's use the default, and have the generator create a folder for us by pressing `enter`.

All of the necessary structure will be created including installing any dependencies.

###Exploring your first `machinepack`.

Change into the new `machinepack-Klout` folder by **typing**:

`$cd machinepack-klout`

We can take a look at the example `machine` by **typing**:

`$machinepack ls`

You should see the following in your terminal window:

```sh
There is only 1 machine in this machinepack.
=============================================
 • say-hello
 ```

####So what does `say-hello` do?

To execute a machine from the command-line **type:**

```sh
$ machinepack exec say-hello
```

You'll be prompted for a value of the `input` of the _Machine_, in this case a `name` (e.g. John Galt).  The _Machine_ will then  execute.

```sh
? Please enter the name of the person that will be sent the hello message.
name:  John
Hello John, your secret code is 0.8229460262227803
________________________________________________________________˛
                                                                 
   Klout.sayHello()
  
   » name "John"

________________________________________________________________¸ 
  | 
  • 
  The machine triggered its success exit and returned a value:
   { numLettersInName: 4, secretCode: '0.8229460262227803' }
```


###Adding a `machine` to a `machinepack` 
Adding a `machine` to an exisitng `machinepack` is simple.  From the root of the `machinepack` **type**:

`machinepack add`

You'll be prompted for a friendly name.  For example, _Get Klout id_ will produce a machine with the name _get-klout-id_.

> In userland, the friendly name will become camel-cased and therefore can be accessed using `.getCloudId()`.

- Sentence-case (like a normal sentence- only uppercase the first letter of the first word)
- No ending punctuation.
- Less than 50 characters (i.e. 2-4 words)

Next, you'll be asked for a `description`.  We'll use, _Get the Klout Id from a Twitter screen name._

 - Clear, 1 sentence description with in the imperative mood (e.g. "Delete all..." not "Deletes all..."). 
 - Ending punctuation is optional, but not necessary
 - Should be less than 80 characters.

We're not going to provide an optional extended description, so we'll press **enter**.

You should see the following message:
`New machine (`get-klout-id`) successfully added to machinepack.`

Now when you **type** `machinepack ls` you should see:

```sh
There are 2 machines in this machinepack:
=============================================
 • get-klout-id
 • say-hello
```

To get an expanded view of the `machinepack` **type:**

```sh
$ machinepack info
```

The following information should be displayed:

```sh
Klout -- Communicate with the Klout API to get Ids, Klout Scores, etc.


INSTALLATION
     npm install machinepack-klout@^0.1.0 --save


USAGE
     var Klout = require('machinepack-klout');


AVAILABLE METHODS
     Klout.sayHello()   (say-hello)
     Klout.getKloutId()   (get-klout-id)
```
###Implementing the `Get Klout Id` machine

So this `machine` will return a `Klout Id` when it's given a `Twitter Screen name` and an `API Key`.  The generated `get-klout-id` machine looks like this:

```javascript
module.exports = {
  friendlyName: 'Get Klout Id',
  description: 'Get the Klout Id from a Twitter screen name.',
  extendedDescription: '',
  inputs: {},
  defaultExit: 'success',
  exits: { error: { description: 'Unexpected error occurred.' },
    success: { description: 'Done.', example: 'TODO' } },
  fn: function (inputs,exits) {
    return exits.success();
  },

};
```    

So let's first add the **inputs**:

```javascript
  inputs: {
    twitterScreenName: {
      example: 'tuneyards',
      description: 'The Twitter screen name of Klout Id',
      required: true
    },
    apiKey: {
      example: 'ODUfdisauPUdufsoUSF',
      description: 'Your Klout API key.',
      required: true
    }
  },
```

Next let's add the **exits**:

```javascript
exits: {
  error: {
    description: 'Unexpected error occurred.'
  },
  wrongOrNoKey: {
    description: 'Invalid or unprovided API key. All calls must have a key.'
  },
  success: {
    description: 'Returns a Klout ID.',
    example: '234234239472379'
  }
},
```

Finally, let's implement the **function**:

```javascript
  fn: function(inputs, exits) {

    var URL = require('url');
    var QS = require('querystring');
    var _ = require('lodash');
    var Http = require('machinepack-http');

    Http.sendHttpRequest({
      baseUrl: 'http://api.klout.com/v2/identity.json/twitter?screenName=' + inputs.twitterScreenName + '&key=' + inputs.apiKey,
      url: '',
      method: 'get',
    }).exec({
      // OK.
      success: function(result) {

        try {
          var responseBody = JSON.parse(result.body);
        } catch (e) {
          return exits.error('An error occurred while parsing the body.');
        }

        return exits.success(responseBody.id);

      },
      // Non-2xx status code returned from server
      notOk: function(result) {

        try {
          if (result.status === 403) {
            return exits.wrongOrNoKey("Invalid or unprovided API key. All calls must have a key.");
          }
        } catch (e) {
          return exits.error(e);
        }

      },
      // An unexpected error occurred.
      error: function(err) {

        exits.error(err);
      },
    });
  },
```

Here's the complete code for the machine.

```javascript
module.exports = {
  friendlyName: 'Get Klout id',
  description: 'Get the Klout Id from a Twitter screen name.',
  extendedDescription: '',
  inputs: {
    twitterScreenName: {
      example: 'tuneyards',
      description: 'The Twitter screen name of Klout Id',
      required: true
    },
    apiKey: {
      example: 'ODUfdisauPUdufsoUSF',
      description: 'Your Klout API key.',
      required: true
    }
  },
  defaultExit: 'success',
  exits: {
    error: {
      description: 'Unexpected error occurred.'
    },
    wrongOrNoKey: {
      description: 'Invalid or unprovided API key. All calls must have a key.'
    },
    success: {
      description: 'Returns a Klout ID.',
      example: '234234239472379'
    }
  },
  fn: function(inputs, exits) {

    var URL = require('url');
    var QS = require('querystring');
    var _ = require('lodash');
    var Http = require('machinepack-http');

    Http.sendHttpRequest({
      baseUrl: 'http://api.klout.com/v2/identity.json/twitter?screenName=' + inputs.twitterScreenName + '&key=' + inputs.apiKey,
      url: '',
      method: 'get',
    }).exec({
      // OK.
      success: function(result) {

        try {
          var responseBody = JSON.parse(result.body);
        } catch (e) {
          return exits.error('An error occurred while parsing the body.');
        }

        return exits.success(responseBody.id);

      },
      // Non-2xx status code returned from server
      notOk: function(result) {

        try {
          if (result.status === 403) {
            return exits.wrongOrNoKey("Invalid or unprovided API key. All calls must have a key.");
          }
        } catch (e) {
          return exits.error(e);
        }

      },
      // An unexpected error occurred.
      error: function(err) {

        exits.error(err);
      },
    });
  },
};
```

There are two dependencies to install:

```sh
$ npm install lodash --save
$ npm install machinepack-http --save
```

###Running the `Get Klout Id` machine

To execute the machine **type**:

```sh
$ machinepack exec get-klout-id
```

You'll be prompted for a _Twitter Screen Name_ and an _API Key_.  The _machine_ should display something like this:

```sh
? Please enter the Twitter screen name of Klout Id
twitterScreenName:  irlnathan
? Please enter your Klout API key.
apiKey:  bb8xy34;;dls9facob8ad44
________________________________________________________________˛
                                                                 
   Klout.getKloutId()
  
   » twitterScreenName "irlnathan"
   » apiKey "bb8xy34;;dls9facob8ad44"

________________________________________________________________¸ 
  | 
  • 
  The machine triggered its success exit and returned a value:
   '52917300386935988'


 To run again:
 machinepack exec get-klout-id --twitterScreenName='irlnathan' --apiKey='bb8xy34;;dls9facob8ad44'
```

###Final Clean-up

Let's remove the sample `say-hello` machine by **typing:**

```sh
$ machinepack rm say-hello
```
Let's also remove `DELETE_THIS_FILE.md` by **typing:**

```sh
$ rm -rf DELETE_THIS_FILE.md
```

###Publishing `machinepack-klout`

```sh
$ npm publish
```

Congrats, you've created your first machinepack!
