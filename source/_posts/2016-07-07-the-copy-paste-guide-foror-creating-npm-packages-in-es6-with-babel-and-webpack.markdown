---
layout: post
title: "The Copy Paste Guide for Creating NPM packages in ES6 with Babel and Webpack"
date: 2016-07-20 12:34
comments: true
author: Shidhin CR
categories:
- npm
- webpack
- ES6
- babel
- node modules
---

ES6 is lovely, right?

I believe "Yes" it is! However, ES6 is not quite ready yet. Present JavaScript environments (NODE and Browser ) cannot run all the ES6 proposed features. Browser vendors are slowly shipping the features to their JS engines. Hence, as of today, only ES5 is guaranteed to work seamlessly in every JS environment.
<!-- more -->
So, how can we write ES6 ?

Luckily, we have transpilers. They allow us to transpile ( a term for compiling from a language to the same language but a different version ) ES6 code to ES5. In this post, we'll see how to write a cross platform ( UMD ) module in ES6 and compile to ES5 using `webpack` and `babel`.

## Let's Start

In a nutshell, our project includes: 

1. [Webpack](https://webpack.github.io/) is a code bundling tool and will use for building our module.
2. [Babel-loader](https://github.com/babel/babel-loader) for transpiling ES6 code to ES5
3. Babel plugins and presets:
    - babel-preset-es2015
    - babel-preset-stage-0
    - babel-plugin-add-module-exports
4. [Ava](https://github.com/avajs/ava) for unit testing our module
5. NPM scripts for the build, test and  pre-publish tasks.

Let's start by creating a new directory for our project. In the terminal, navigate to the directory and run `npm init`. Follow the instructions to generate a valid `package.json` file.

Time to install the dependencies for the module. Run the following command to install the packages from npm:

```sh
npm install --save-dev webpack babel-loader babel-plugin-add-module-exports babel-preset-es2015 babel-preset-stage-0 ava
```

Create the following files and folder structure:

1. index.js ( This will be our main ES6 source file )
2. webpack.config.js ( The configurations required for webpack to build our module )
3. .babelrc ( babel configuration file )
4. `dist` directory ( Final compiled package goes here )

Let's start with the webpack configuration. Open `webpack.config.js` in your favorite editor. Copy and paste the below code:

```js webpack.config.js
    module.exports = {
        entry: './index.js',
        target: 'node',
        output: {
            path: './dist',
            filename: 'build.js',
            libraryTarget: 'umd'
        },
        module: {
            loaders: [{
                test: /\.js$/,
                loader: 'babel',
                exclude: /node_modules/
            }]
        }
    }
```

This is a simple configuration file required by webpack. The exported configuration file will be processed by webpack. Let's look at each item in the exported object:

- `entry` is the main file from where webpack will look for all other dependencies.
- We set **target='node',** so webpack knows how to mock/load the default node packages like "fs", "url" ..etc
- Output section contains information about our final package and configurations related to it. We set our `libraryTarget` to **UMD** so that webpack will generate the final module in UMD format ( universal module definition -- for commonJS, requireJS, and browser )  
- Module is the section where we can configure different loaders for our code. Since we write the code in ES6, we need webpack to use `babel-loader` to transform the file. Module can have different other options for configuring the module, however, we just need only the loader configuration for now.

Babel requires plugins to complete its job properly. For that, update the babel configurations in `.babelrc` file.

```js .babelrc
    {
        "moduleId": "myModule",
        "presets": ["es2015", "stage-0"],
        "plugins": ["add-module-exports"]
    }
```

Here we specify the moduleId to the final name of the module. We need to add the presets option to `["es2015", "stage-0"]` in order to transform the ES6 code. Find more about `es-2015` and `stage-0` features [here](https://github.com/hemanth/es-next#stage-0)

Babel 6 does not generate a default export inside the compiled file. Instead, the default export is exported as a property called `default`. Because of this, if we try `require('myModule')`, it doesn't work. Babel 6 export should be required using `require('myModule').default`.

That's why we use a plugin called `add-module-exports`. Its duty is to add the default export in the final babel generated output. You can find more about it [here](https://www.npmjs.com/package/babel-plugin-add-module-exports).

*Note: Make sure the `moduleId` in .babelrc is matching with your module's name.*

## Set up the tests

I find `ava` would be the best option for unit testing our module. Let's create a `tests` directory and add a new file `build.spec.js`. A basic test case is give below:

```js build.spec.js
var test = require('ava');
var myModule = require('../dist/build');

test('the log method', t => {
    t.is(myModule.log('hi'), 'Logging the message : hi');
});
```

Now make the tests pass by implementing the real code in the module.

```js index.js
const myModule = {
  log(message){
    return `Logging the message : ${message}`;
  }
}

export default myModule;
```

More about `ava` [here](https://github.com/avajs/ava).  

## Set up the NPM scripts to do the tasks

We need 5 npm tasks to make our life easier. All of them are self-explanatory; Just have look at the code in `package.json`.

1. **npm start:**  To run in development mode. This task will watch for any source file changes and output the build.
2. **npm run build:** Build the final module in production mode ( minified and with source maps ).
3. **npm test:** Run the `ava` tests.
4. **npm run test-w:** Run tests in continuous watch mode.
5. **npm prepublish:** Before publishing the package, this task run the `build` and `test` to make sure everything works fine.

Final scripts will look like this:

```js package.json
"scripts": {
    "start": "webpack -w",
    "build": "webpack -p",
    "test": "ava tests/**/*.js",
    "test-w": "ava -w tests/**/*.js",
    "prepublish": "npm build && npm test"
}
```

For developing in TDD fashion, run `npm start` and `npm run test-w` together.

## Make the `dist/build.js` as the main file for the module

Finally, we need to tell npm about where to load the module from. Edit the `package.json` and change the "main" property to `dist/build.js`

## Summary

Developers already adopted ES6 even before it's available. Thanks to all these tools and the people behind them.

Today we learnt the minimal set-up to start writing code in ES6. You can find the repo for the [seed project here](https://github.com/namshi/node-es6-seed). Fork, and create your next npm package in ES6.

I hope you enjoyed reading the article. Feedbacks are welcome!
