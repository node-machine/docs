# What are machinepacks?

Machinepacks are bundles of related machines.  They might be unified by the API they talk to (e.g. [Stripe](http://node-machine.org/machinepack-stripe)) or their purpose (e.g. [Passwords](http://node-machine.org/machinepack-passwords)).  It's really up to you!  The important thing to remember is that machinepacks are just a versioned, organizational unit you can publish and install.

For example, as of February 2015, [machinepack-github](http://node-machine.org/machinepack-github) contains 11 machines; it supports everything from fetching basic metadata about a particular GitHub user, to listing repo activity, to creating a new repo altogether.  The machinepack can be installed and required from your Node.js code like any other `npm` module:
    
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

### Installing the command-line tools

In the process of using machines for the past year or so, the core team behind this project and specification created a couple of tools to make machines easier to work with.  The two we'll be using in this walkthrough are `machinepack` and `generator-machinepack`.  Both are completely optional, but also readily available on NPM.

##### Yeoman generator

> The Yeoman generator (`generator-machinepack`) requires [Yeoman](http://yeoman.io/).  If you don't already have Yeoman globally installed, you'll want to do that before proceeding:
> 
> ```sh
> $ npm install -g yo
> ```

There is a Yeoman generator for machinepacks for your convenience.  You will likely only use this tool every now and then, but it's worth it to get you off the ground running.  To install:

```sh
$ npm install -g generator-machinepack
```

##### The `machinepack` CLI tool

The `machinepack` tool was designed to solve a few key annoyances of building machines.  It provides the capability of testing machines from the command-line with an interactive prompt, a readily-accesible manpage for checking a pack's metadata and machines, as well as shortcuts for simple operations like adding or removing machines to a pack.  If you're like me, and working with machines on a regular basis, you'll find it indispensible.

To install the `machinepack` command-line interface from NPM:

```sh
$ npm install -g machinepack
```

> **Note:** If you run into install issues, try `$ sudo`.

###Generating your first `machinepack`.
I ran across an interesting [API](https://klout.com/s/developers/v2) from [Klout](https://klout.com/). Klout uses social media analytics to rank people based upon their social influence via a Klout Score.  So we'll create a couple of machines that will return a person's _Klout ID_ and based upon that _id_, their _Klout Score_. 

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

Next, you'll be asked to provide a `machinepack` `description`.

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

We could create a node script that requires the machine programatically and then calls `.exec()` on it, but that sort of sucks.  Alternatively, we could use the node REPL, but.... still sort of sucks.

Instead, we'll take advantage of the most powerful feature of the `machinepack` command-line tool- the ability to execute a machine interactively. To run a machine from the command-line:

```sh
$ machinepack exec say-hello
```

If the machine has any required inputs, you'll be prompted to enter a value for each of them.  In this case, you'll be asked to enter a "name":

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


### Adding a new machine

Adding a machine to an exisitng pack is simple.  From the root directory of your machinepack, run:

```sh
$ machinepack add
```

You'll be prompted for a "friendly name": 

```sh
? What would you like to use as the "friendly name" for your new machine?
(e.g. "Get auth token" or "Delete account")
 Get Klout id
```

_(Don't worry if you mess up, you can always change this later)_


The string you enter will be used as the `friendlyName` for the new machine, and the machine's `identity` will also be inferred from it automatically. For example, if you enter "_Get Klout id_", you'll end up with a file in your machines folder named "_get-klout-id_".  In userland, you'll be able to call this machine as the `.getKloutId()` method on the machinepack.  This method name is determined by converting the dash-delimited `identity` into camel-case.


Next, you'll be asked for a `description`.  Make sure you type something less than 80 characters long, and in the imperative mood (e.g. "Delete all..." not "Deletes all...").  This is important for consistency and proper grammar and punctuation in the generated documentation.

```sh
? Describe this machine in 80 characters or less.
(e.g. "List all Twitter followers for a particular account.")
 Get the Klout id for a user given her Twitter screen name.
```

We're not going to provide an optional extended description, so we'll press **_enter_** to skip the prompt.  When we're all done, you should see the following message:

```
New machine (`get-klout-id`) successfully added to machinepack.
```

Not too shabby, right?  But before we celebrate, let's make sure it worked. When you run `machinepack ls` you should see:

```sh
There are 2 machines in this machinepack:
=============================================
 • get-klout-id
 • say-hello
```

### Inspecting your pack with `mp info`

Before we move on, I'd like to add one more useful strategy to your bag of tricks.  From the root of your machinepack, give this a go:

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

The `mp info` utliity is a great way to get a quick reminder of what's in the pack you're looking at.  It's particularly helpful when pulling down a pack you haven't worked with in a few weeks/months/years/etc.


### Implementing our first machine

You can follow along with a walkthrough where we implement a complete machine (`.getKloutId()`) [here](http://node-machine.org/implementing/Your-First-Machine).  By the end of that guide, you should have a solid mental model for how and why machines work the way they do, as well as a working machine you can immediately start using as a copy+paste example.  When you feel confident you've got a handle on how machines work and you're ready to learn how to publish a machinepack, continue on to the next section.

### Publishing a machinepack

First, remove the sample `say-hello` machine.  You could manually delete `machines/say-hello.js`, then remove the machine from the list in your package.json file.  But that's kind of annoying.  Luckily our old friend the command-line tool includes a helper script just for occasions like these:

```sh
$ mp rm say-hello
```

While we're at it, let's give our code one last pass to see if there's anything else we don't want to publish to the whole world.  Looks like the yeoman generator created an extraneous file, helpfully titled "DELETE_THIS_FILE.md".  Let's delete that:

```sh
$ rm DELETE_THIS_FILE.md
```

Next, we'll update the `package.json` file with the URL of the GitHub repo where our code is hosted:

```javascript
"repository": {
    "type": "git",
    "url": "https://github.com/irlnathan/machinepack-klout.git"
  },
```

This is a good idea with any node module, but for us it's especially important since it will allow node-machine.org to display a "View Source" button on the manpage for your machinepack, and for each of its machines.

Finally, now that we're all ready to go, we'll create a git tag and a commit message for the very first version of our machinepack, v1.0.0.  Once again, there is a helpful tool that does this for us; only this time, it's npm itself:

```sh
$ npm version major
```

> Confused about what's going on here?  You can learn more about semantic versioning, or "semver" on [semver.org](http://semver.org/).

Ok now push that up:

```sh
$ git push && git push --tags
```

And publish v1.0.0 of your machinepack to npm:

```sh
$ npm publish
```

Of course since `machinepack-klout` already exists, you won't be _actually_ be able to publish it to NPM.  But you get the idea.

> By the way, if you haven't published a module to NPM before, don't worry-- it's super easy.  Just [sign up on NPM's website](https://www.npmjs.org/), then log in from the command-line using `npm login`.  After that, if you try running `npm publish` again, you should have better results.

Congratulations on creating your first machinepack! There are hundreds of machines already published and available on NPM, but there is still plenty of work to do.  If you can imagine it implemented in a Node module, you can build it as a machine.  Go forth and code... and good luck.


