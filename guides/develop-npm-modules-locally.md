## Intro

This is how you develop node modules locally, while retaining npm tooling/linking behavior for your a _Main project_.

This guide assumes you have your app in a folder on your drive, running the node componengt that was `npm i`'d. 

1. *Git clone the node module onto your hard drive*

2. _Make module project linkable_

This tells npm about the module so other local repositories can use it:

```sh
cd <module project>
npm link
```
(ensure you get a confirmation message)

3. *Delete the existing `npm i`'d module*

Go into your _Main project_, and find and delete the `node_modules/<module>/` folder
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

If you go into the `node_modules` folder, you will see a symlinked folder (which is linked to your module project folder)

If it complains, ensure you're' using `"name"` as defined in the `package.json` file.

# Sources

* npm publish checklist: http://ericdouglas.github.io/2015/09/27/checklist-for-your-new-open-source-javascript-project/
