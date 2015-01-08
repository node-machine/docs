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

You'll be prompted for a friendly `name`.   By entering _Tuneyards_, the name will become `machinepack-tuneyards`.

Next, you'll be asked to provide a `description`.  We'll enter, _Communicate with the Tuneyards API to get songs, tour dates, etc._

The generator gives you the option to create an example machine.  Let's go ahead and type `y` for yes and generate the machine.

Finally, you'll be prompted whether to put this example machine in a new folder within the machinepack or at the root of the machinepack.  Let's use the default, and have the generator create a folder for us by pressing `enter`.

All of the necessary structure will be created including installing any dependencies.

###Exploring your first `machinepack`.

Change into the new `machinepack-tuneyards` folder by **typing**:

`$cd machinepack-tuneyards`

We can take a look at the example `machine` by **typing**:

`$machinepack ls`

You should see the following in your terminal window:

```sh
There is only 1 machine in this machinepack.
=============================================
 • say-hello
 ```

 So what does `say-hello` do?

 <ask Mike about the CLI plans for this>


###Adding a `machine` to a `machinepack`
Adding a `machine` to an exisitng `machinepack` is simple.  From the root of the `machinepack` **type**:

`machinepack add`

You'll be prompted for a friendly name.  For example, _Get song_ will produce a machine with the name _get-song_.

> In userland, the friendly name will become camel-cased and therefore can be accessed using `.getSong()`.

- Sentence-case (like a normal sentence- only uppercase the first letter of the first word)
- No ending punctuation.
- Less than 50 characters (i.e. 2-4 words)

Next, you'll be asked for a `description`.  We'll use, _Fetch a Song from Tuneyards_

 - Clear, 1 sentence description with in the imperative mood (e.g. "Delete all..." not "Deletes all..."). 
 - Ending punctuation is optional, but not necessary
 - Should be less than 80 characters.

We're not going to provide an optional extended description, so we'll press **enter**.

You should see the following message:
`New machine (`get-song`) successfully added to machinepack.`

Now when you **type** `machinepack ls` you should see:

```sh
There are 2 machines in this machinepack:
=============================================
 • say-hello
 • get-song
```

