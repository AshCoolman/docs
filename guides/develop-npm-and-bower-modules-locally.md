---
layout: page
type: guide
title: Develop npm and bower modules locally
permalink: /guides/develop-npm-and-bower-modules-locally
---

# Developing a node component locally

**feedback welcome!**

## node = bower ?
`node_modules` = `bower_components`
`npm install` = `bower install`
`package.json` = `bower.json`

Often when working on a project, we want to modify a node component locally. Perhaps a workmate has part of the app as a seperate module, or maybe we have forked an existing module to make our own variation. Or perhaps we created a seperate repository for some functionality to share and keep things decoupled.

This guide assumes you have your app in a folder on your drive, running the node component that was `npm i`'d. 

1. *Get the module on your harddrive*
Git clone the node module as per usual

2. _Make module project linkable_
We are going to tell the bower CLI about this project so we can link it to our main project later:
```sh
cd <module project>
npm link
```
Take note of output saying it has been linked

3. *Delete the `npm i`'d module*
Go into your project, and find the `node_modules/` folder (usually in the root of your project). Then find the module folder and delete it.

e.g.
```sh
cd <project>
rm -r <project>/node_modules/<module>
```


4. _Link the module project_
Now instead of using `npm install` (that uses the npm registry on the net), you can now use `npm link <package name>` locally.

Go to the path **above** `node_modules`
```sh
cd <project>/node_modules/
cd ..
```

Now you can link it:
```sh
npm link <module-name>
```

If you go into the `node_modules` folder, you will see a symlinked folder (which is linked to your module projec
t folder)


If it complains, ensure you're' using `"name"` as defined in the `package.json` file.

**Should I use bower?:**

No.

The strategy of maintaining a built flies and a seperate manifest (bower.json) ontop of npm does not seem to have worked. The solution that I like now is to transform the sources directly using something like Webpack or Gulp. We may get a community "blessed" solution using something like [npmcdn](https://npmcdn.com/) sometime in the future. And even further in the future, ES6 module syntax plus multiplexing will start to reduce the bundling/transformation requirements even further.

# Sources

* npm publish checklist: http://ericdouglas.github.io/2015/09/27/checklist-for-your-new-open-source-javascript-project/