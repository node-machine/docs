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

You can follow along with a walkthrough of building this machine in the ["Your First Machine" tutorial]().

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


