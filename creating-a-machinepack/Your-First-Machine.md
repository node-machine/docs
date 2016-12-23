# Your First Machine

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

### Running the `Get Klout Id` _Machine_

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

> **Note:** Check out the "To run again:" message, it will save you time :)

### Creating a second _Machine_ -- `Get Klout Score`

The _Get Klout Score Machine_ will share some code with the _Get Klout Id Machine_ so I'll use the copy command in the _Machinepack_ command-line tools to get started:

```sh
$ machinepack cp get-klout-id get-klout-score
```

You should see something like this displayed.

```sh
Copied: `get-klout-id` to new machine: `get-klout-score`
```

### Implementing the `Get Klout Score` _Machine_

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

> **Note:** The `apiKey` _input_ remains the same.

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

> **Note:** The `wrongOrNoKey` exit remains the same.

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

### Running the `Get Klout Score` _Machine_

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

> **Note:** Check out the "To run again:" message, it will save you time :)
