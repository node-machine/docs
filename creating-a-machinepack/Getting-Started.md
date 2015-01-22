##What are machinepacks?
**Machinepacks** are bundles of _Machines_ that make it easy to aggregate related tasks that each _Machine_ performs.  For example, [machinepack-github](http://node-machine.org/machinepack-github) contains seven _Machines_ ranging from fetching a particular repo to listing repo activity.  The `machinepack` can then be required like any other `npm` module.
    
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

_Command-line_ tools are provided in order to make _Machinepacks_ easier to create.

###Installing `machinepack` _command-line_ tools

The _command-line generator_ requires [Yoeman](http://yeoman.io/), so if you don't already have [Yoeman](http://yeoman.io/) installed, you can do so now by **_typing_**:

```sh
$ npm install -g yo
```

Next, install the `yoeman` `machinepack` generator by **_typing_**:

```sh
$ npm install -g generator-machinepack
```

Finally, install the `machinepack` _command-line tools_ by **_typing_**:

```sh
$ npm install -g machinepack
```

> **Note:** If you have an issue try `$ sudo `.

###Generating your first `machinepack`.
I ran across an interesting [API](https://klout.com/s/developers/v2) from [Klout](https://klout.com/). Klout uses social media analytics to rank people based upon their social influence via a Klout Score.  So we'll create a couple of _Machines_ that will return a person's _Klout ID_ and based upon that _id_, their _Klout Score_. 

To begin, we'll generate a _bare bones_ `machinepack` by **_typing_**:

```sh
$ yo machinepack
```

You'll be prompted for a friendly `name`.   By entering _Klout_, the `machinepack` _name_ will become `machinepack-klout`.

```sh
     _-----_
    |       |    .--------------------------.
    |--(o)--|    |      Welcome to the      |
   `---------´   |  Machinepack generator!  |
    ( _´U`_ )    '--------------------------'
    /___A___\    
     |  ~  |     
   __'.___.'__   
 ´   `  |° ´ Y ` 

? What would you like to use as the "friendly name" for your machinepack?
(e.g. "Passwords" or "Twitter")
 Klout
```

Next, you'll be asked to provide a `machinepack` `description`.  We'll enter, _Communicate with the Klout API to get Ids, Klout Scores, etc._

```sh
? Describe this machinepack in 80 characters or less.
(e.g. "Communicate with the Github API to get repos, commits, etc.")
 (Work with klout.) Communicate with the Klout API to get Ids, Klout Scores, etc.
```

The generator gives you the option to create an _example_ `machine`.  Let's go ahead and type `y` for yes and generate the _example_ `machine`.

```sh
? Want me to create an example machine (`say-hello.js`) to help you get started? (y/N) y
```

Finally, you'll be prompted whether to put this _example_ `machine` in a _new_ folder within the `machinepack` or at the **root** of the `machinepack`.  Let's use the default, and have the generator create a folder for us by **_pressing_** `enter`.

```sh
? Where should this new machinepack be generated? 
❯ In a new folder (/Users/jgalt/Development/machinepack-klout) 
  Within the current directory (/Users/jgalt/Development/) 
```

All of the necessary structure will be created including installing any _dependencies_.

###Exploring your first `machinepack`.

Change into the new `machinepack-klout` folder by **_typing_**:

`$ cd machinepack-klout`

We can take a look at the example `machine` by **_typing_**:

`$ machinepack ls`

You should see the following in your terminal window:

```sh
There is only 1 machine in this machinepack.
=============================================
 • say-hello
```

####So what does `say-hello` do?

To execute a _Machine_ from the command-line **type:**

```sh
$ machinepack exec say-hello
```

You'll be prompted for a value of the `input` of the _Machine_, in this case a `name` (e.g. John Galt).  The _Machine_ will then execute.

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
Adding a `machine` to an exisitng `machinepack` is simple.  From the root of the `machinepack` **_type_**:

`machinepack add`

You'll be prompted for a friendly name.  For example, _Get Klout id_ will produce a _Machine_ with the name _get-klout-id_.

```sh
? What would you like to use as the "friendly name" for your new machine?
(e.g. "Get auth token" or "Delete account")
 Get Klout id
```

> In userland, the friendly name will become camel-cased and therefore can be accessed using `.getCloudId()`.

- Sentence-case (like a normal sentence- only uppercase the first letter of the first word)
- No ending punctuation.
- Less than 50 characters (i.e. 2-4 words)

Next, you'll be asked for a `description`.  We'll use, _Get the Klout Id from a Twitter screen name._

 - Clear, 1 sentence description with in the imperative mood (e.g. "Delete all..." not "Deletes all..."). 
 - Ending punctuation is optional, but not necessary
 - Should be less than 80 characters.

```sh
? Describe this machine in 80 characters or less.
(e.g. "List all Twitter followers for a particular account.")
 Get the Klout Id from a Twitter screen name.
```

We're not going to provide an optional extended description, so we'll press **_enter_**.

```sh
Get the Klout Id from a Twitter screen name.
? Provide a more in-depth description of the machine (optional)
```

You should see the following message:
`New machine (`get-klout-id`) successfully added to machinepack.`

Now when you **type** `machinepack ls` you should see:

```sh
There are 2 machines in this machinepack:
=============================================
 • get-klout-id
 • say-hello
```

To get an expanded view of the `machinepack` **_type_**

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
###Implementing the `Get Klout Id` _Machine_

So this _Machine_ will return a `Klout Id` when it's given a `Twitter Screen name` and an `API Key`.  The generated `get-klout-id` machine looks like this:

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

First **_add_** the **inputs**:

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

Next **_add_** the **exits**:

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

Finally, implement the **function**:

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

Here's the complete code for the `Get Kout Id` _Machine_.

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

###Running the `Get Klout Id` _Machine_

To execute the _Machine_ **type**:

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

>**Note:** Check out the "To run again:" message, it will save you time :)

###Creating a second _Machine_ -- `Get Klout Score`

The _Get Klout Score Machine_ will share some code with the _Get Klout Id Machine_ so I'll use the copy command in the _Machinepack_ command-line tools to get started:

```sh
$ machinepack cp get-klout-id get-klout-score
```

You should see something like this displayed.

```sh
Copied: `get-klout-id` to new machine: `get-klout-score`
```

###Implementing the `Get Klout Score` _Machine_

So this _Machine_ will return a `Klout Score` when it's given a `Klout ID` and an `API Key`.  The copied `get-klout-score` machine looks like this:

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

First **_edit_** the `friendly name` and `description`:

```javascript
module.exports = {
  friendlyName: 'Get Klout Score',
  description: 'Get the Klout score of a Klout Id.',
  extendedDescription: '',
```

Next, **_edit_** the **inputs**.

>**Note:** The `apiKey` _input_ remains the same.

```javascript
inputs: {
    kloutId: {
      example: '32423423432342',
      description: 'A Klout Id.',
      required: true
    },
    apiKey: {
      example: 'ODUfdisauPUdufsoUSF',
      description: 'Your Klout API key.',
      required: true
    }
  },
```

Next, **_edit_** the **exits**.

>**Note:** The `wrongOrNoKey` exit remains the same.

```javascript
exits: {
    error: {
      description: 'Unexpected error occurred.'
    },
    wrongOrNoKey: {
      description: 'Invalid or unprovided API key. All calls must have a key.'
    },
    success: {
      description: 'Returns the Klout score of a Klout ID.',
      example: '54.342342332'
    }
  },
```

Next, **_edit_** the **function**.

```javascript
fn: function(inputs, exits) {

    var URL = require('url');
    var QS = require('querystring');
    var _ = require('lodash');
    var Http = require('machinepack-http');

    Http.sendHttpRequest({
      baseUrl: 'http://api.klout.com/v2/user.json/' + inputs.kloutId + '/score?key=' + inputs.apiKey,
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

        return exits.success(responseBody.score);

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

        return exits.error(result);

      },
      // An unexpected error occurred.
      error: function(err) {

        exits.error(err);
      },
    });
  },
};
```

Here's the complete code for the `Get Kout Score` _Machine_.

```javascript
module.exports = {
  friendlyName: 'Get Klout score',
  description: 'Get the Klout score of a Klout Id.',
  extendedDescription: '',
  inputs: {
    kloutId: {
      example: '32423423432342',
      description: 'A Klout Id.',
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
      description: 'Returns the Klout score of a Klout ID.',
      example: '54.342342332'
    }
  },
  fn: function(inputs, exits) {

    var URL = require('url');
    var QS = require('querystring');
    var _ = require('lodash');
    var Http = require('machinepack-http');

    Http.sendHttpRequest({
      baseUrl: 'http://api.klout.com/v2/user.json/' + inputs.kloutId + '/score?key=' + inputs.apiKey,
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

        return exits.success(responseBody.score);

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

        return exits.error(result);

      },
      // An unexpected error occurred.
      error: function(err) {

        exits.error(err);
      },
    });
  },
};
```

###Running the `Get Klout Score` _Machine_

To execute the _Machine_ **type**:

```sh
$ machinepack exec get-klout-score
```

You'll be prompted for a _Klout Id_ and an _API Key_.  The _machine_ should display something like this:

```sh
? Please enter a Klout Id.
kloutId:  52917300386935988
? Please enter your Klout API key.
apiKey:  bb8xy34;;dls9facob8ad44
________________________________________________________________˛
                                                                 
   Klout.getKloutScore()
  
   » kloutId "52917300386935988"
   » apiKey "bb8xy34;;dls9facob8ad44"

________________________________________________________________¸ 
  | 
  • 
  The machine triggered its success exit and returned a value:
   '40.15902258047049'


 To run again:
 machinepack exec get-klout-score --kloutId='52917300386935988' --apiKey='bb8xy34;;dls9facob8ad44'
```

>**Note:** Check out the "To run again:" message, it will save you time :)

###Final Clean-up

First remove the sample `say-hello` machine by **_typing:_**

```sh
$ machinepack rm say-hello
```

Also remove `DELETE_THIS_FILE.md` by **_typing:_**

```sh
$ rm -rf DELETE_THIS_FILE.md
```

Commit the project to github and then update the `package.json` with the resulting repo:

```javascript
"repository": {
    "type": "git",
    "url": "https://github.com/irlnathan/machinepack-klout.git"
  },
```

###Publishing `machinepack-klout`

```sh
$ npm publish
```

> **Note:** Of course since `machinepack-klout` already exists, you won't be able to publish it npmjs.org.  

Congrats on creating your first `machinepack`! There's a ton of _Machines_ that need to be created so go forth and code! :-)


