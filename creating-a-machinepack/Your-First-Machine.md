# Your First Machine

Your first machine will return a Klout Id when it's given a `twitterScreenName` and an `apiKey`.  The generated `get-klout-id` machine looks like this:

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

First define the inputs:

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

Next define the exits:

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

Here's the complete code for the `Get Klout Id` _Machine_.

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

### Running the machine

To execute the machine:

```sh
$ machinepack exec get-klout-id
```

You'll be prompted for a Twitter screen name and your API key:

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

> **Hint:** Check out the "To run again:" message- it will save you time.

### Creating a second machine

The "Get Klout Score" machine will have a lot of similar content to the "Get Klout Id" machine. For occasions like this, it's useful to uses the CLI's copy command:

```sh
$ machinepack cp get-klout-id get-klout-score
```

You should see something like:

```sh
Copied: `get-klout-id` to new machine: `get-klout-score`
```

Now, you can make the necessary changes to the machine definition to make your second machine. The example code for the "Get Klout Score" machine can be found [in this Gist](https://gist.github.com/rachaelshaw/04ee1b767c2cd419558fd50a977ecd1f).
