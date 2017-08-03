---
published: true
tags: 'test, one, two'
---
I wanted to make Chai's documentation easier to scroll through.

I had a look around for API templates and the best seems to be Slate.

Slate's winning features are: 

- Everything on a single page
- Automatic, smoothly scrolling table of contents

It's not perfect - I've noticed it doesn't dynamically scroll into view the active table-of-contents section for second-layer items - but it's a good start.

## the original documentation

The documentation build is open source and hosted on Github. They use some kind of package that automatically generates documentation from the comments.

[You can see it here.](https://github.com/chaijs/chai-docs/blob/8620abe8ba41574bd849899d147701c5b0209d8d/chai.js#L2175)

It uses [codex, a static site generator](https://www.npmjs.com/package/codex) and [dox, a documentation generator](https://www.npmjs.com/package/dox).

First of all I had to figure out how the project is compiled. The makefile looked promising.

[Unix Guide](http://www.tutorialspoint.com/unix_commands/make.htm)

> The purpose of the make utility is to determine automatically which pieces of a large program need to be recompiled, and issue the commands to recompile them. you can use make with any programming language whose compiler can be run with a shell command. In fact, make is not limited to programs. You can use it to describe any task where some files must be updated automatically from others whenever the others change. 
To prepare to use make, you must write a file called the makefile that describes the relationships among files in your program, and the states the commands for updating each file. 

[building javascript with make](https://blog.jcoglan.com/2014/02/05/building-javascript-projects-with-make/)

> Make does not have any baked-in assumptions about what type of project you have or what languages you’re using, it’s simply a tool for organising sets of shell commands. It has a very simple model: you describe which files each build file in your project depends on, and how to regenerate build files if they are out of date. 

> For example, if file `a.txt` is generated by concatenating `b.txt` and `c.txt`, then we would write this rule in our Makefile:

```bash
a.txt: b.txt c.txt
	cat b.txt c.txt > a.txt
```

> The first line says that `a.txt` (the target) is generated from `b.txt` and `c.txt`(the dependencies). `a.txt` will only be rebuilt if one of its dependencies was changed since a.txt was last changed

> The second line (the recipe) says how to regenerate the target if it’s out of date, which in this case is a simple matter of piping cat into the target file.

> It’s customary to make a target called `all` that depends on all your project’s compiled files, and make this the first rule in the Makefile so that running make will run this rule.

So `all: generated_data docs-server` means that `all` is generated from `generated_data` and `docs-server`.

The documentation is generated with:

```bash
@./node_modules/.bin/dox --raw < ./node_modules/chai/chai.js > _data/chai.json
```

The arrows (`<`, `>`) are input/output redirections.
This command runs `dox` with the input of `chai.js` and the output of `data/chai.json`.
 
I don’t know how it’s using `codex`. It’s not found anywhere in the codebase, apart from listed as a dependency in the package json. For my purposes I don't really need to know. Now I know where the documentation json is, I can run the file and extract the data I need.

## Using JSON

Once I had run the program and generated the data, I had a JSON with all of the documentation.

Now I needed to figure out how to convert it to markdown. I found several tools to convert JSON to markdown, but then I realized I could do it myself quite easily.
 
The JSON I have already has markdown, so these tools aren’t that useful. 

```javascript
var jsonData = require('./_data/chai.json');

for (var i = 0; i < jsonData.length; i++) {
    
    const regex = /###/g;
    let description = jsonData[i].description.full;
    description = description.replace(regex, '##');
    console.log(description);
    }
```

I was able to interate through the JSON.

Each JSON Object contains the following:

```javascript
{ tags: 
   [ { type: 'name', string: 'isNotTrue' },
     { type: 'param',
       string: '{Mixed} value',
       name: 'value',
       description: '',
       types: [Object],
       typesDescription: '<a href="Mixed.html">Mixed</a>',
       optional: false,
       nullable: false,
       nonNullable: false,
       variable: false },
     { type: 'param',
       string: '{String} message',
       name: 'message',
       description: '',
       types: [Object],
       typesDescription: '<code>String</code>',
       optional: false,
       nullable: false,
       nonNullable: false,
       variable: false },
     { type: 'namespace', string: 'Assert' },
     { type: 'api', string: 'public', visibility: 'public' } ],
  description: 
   { full: '### .isNotTrue(value, [message])\n\nAsserts that `value` is not true.\n\n    var tea = \'tasty chai\';\n    assert.isNotTrue(tea, \'great, time for tea!\');',
     summary: '### .isNotTrue(value, [message])',
     body: 'Asserts that `value` is not true.\n\n    var tea = \'tasty chai\';\n    assert.isNotTrue(tea, \'great, time for tea!\');' },
  isPrivate: false,
  isConstructor: false,
  isClass: false,
  isEvent: false,
  ignore: false,
  line: 4425,
  codeStart: 4440,
  code: 'assert.isNotTrue = function (val, msg) {\n  new Assertion(val, msg, assert.isNotTrue, true).to.not.equal(true);\n};',
  ctx: 
   { type: 'method',
     receiver: 'assert',
     name: 'isNotTrue',
     string: 'assert.isNotTrue()' } }
```

JSON can start with either a left brace (an array)) or left curly brace (an object).

I think we can find the assert functions by the namespace tag. Yeah that works. There's three different types of namespace: Assert, BDD, and Utils.

The problem now is that there is far too many assert functions and they are not broken up into any categories.