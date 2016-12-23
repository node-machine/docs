# Your First Machine

Your first machine will fetch the ID for a Klout user's account when given a `twitterScreenName` and an `apiKey`.  The newly-generated `get-klout-id` machine looks like this:

```javascript
module.exports = {
  friendlyName: 'Get Klout ID',
  description: 'Get the Klout ID from a Twitter screen name.',
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

First, define the inputs:

```javascript
  inputs: {
    twitterScreenName: {
      example: 'tuneyards',
      description: 'The Twitter screen name for a Klout user.',
      required: true
    },
    apiKey: {
      example: 'ODUfdisauPUdufsoUSF',
      description: 'Your Klout API key.',
      required: true
    }
  },
```

Next, define the exits:

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

Finally, implement the function:

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

Your first machine is ready to run! You can find the example code for the completed machine in [this gist](https://gist.github.com/rachaelshaw/937b44f40995edb44b947090930c46a2).



### Running the machine

Before running the machine, make sure its dependencies are installed:

```sh
$ npm install lodash --save
$ npm install machinepack-http --save
```

Then, to execute the machine:

```sh
$ machinepack exec get-klout-id
```

You'll be prompted for each of your machine's inputs, using the descriptions provided in the input definitions. Once you've entered them all, the machine will run.

```sh
? Please enter the Twitter screen name for a Klout user.
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

> **Hint:** Check out the "To run again:" message- it will save you time.

### Creating a second machine

Next, we'll make a machine that fetches a user's Klout score. Since this machine will have a lot of similar content to the "Get Klout Id" machine, we'll start off with a copy of that first machine:

```sh
$ machinepack cp get-klout-id get-klout-score
```

You should see something like:

```sh
Copied: `get-klout-id` to new machine: `get-klout-score`
```

Now, you can make the necessary changes to the machine definition to make your new machine. The example code for "Get Klout Score" can be found [in this Gist](https://gist.github.com/rachaelshaw/04ee1b767c2cd419558fd50a977ecd1f).
