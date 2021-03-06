# entry-loader

Based on https://github.com/sleexyz/dir-loader
========================

[![Build Status](https://travis-ci.org/sleep/dir-loader.svg)](https://travis-ci.org/sleep/dir-loader)

dir-loader lets you dynamically require a directory in [webpack](http://webpack.github.io).

In other words, dir-loader is a code-generation tool; it obviates the need to hard-code `require`'s for a given directory's modules and subdirectories.


## contents
- [install](#install)
- [use](#use)
- [api](#api)
- [examples](#examples)
- [alternatives](#alternatives)


## install
```
npm install --save-dev dir-loader
```

## use
Suppose you have a webpack project with a semantic directory structure. You want to require your content but still preserve the hierarchical information inherent to the filesystem.
```
.
├── modules
│   ├── accounts
│   │   ├── main.js
|   |
│   ├── cart
│   │   └── main.js
|   |
│   └──ignore-me.js
|
|
|
├── modules.config.js
└── entry.js
```

In a js file, specify the configuration for dir-loader:
```js
// ./modules.config.js

module.exports = {
  path: "./modules",
  filter: /main\.js$/
}
```

And then just require that configuration with **dir!** in your application code!
```js
// ./entry.js

var blog = require("dir!./blog.config.js");
...
```

This is equivalent to the following javascript:
```js
// (equivalent to ./entry.js)

var modules = {
  "accounts": require("./modules/accounts/main.js"),
  "cart": require("./modules/cart/main.js")
};
...
```






## api
```js
// ./entry.js
var modules = require("dir!./modules.config.js");
...
```

```js
// ./modules.config.js

module.exports = {

  // path :: String
  // Path to directory. Can be absolute or relative path.
  path: "./modules",
  
  // filter :: RegExp
  // (optional)
  // Regular expression to test entry filenames.
  filter: /main\.js$/,
  
  // dirFilter :: RegExp
  // (optional)
  // Regular expression to test directory names.
  dirFilter: /^(?!__private__).*/,
  
  // pathTransform :: (String) -> String
  // (optional)
  // Function to transform each generated require statement.
  pathTransform: (_) => "bundle!" + _
  
}
```

## examples
Code [here](https://github.com/sleep/dir-loader/tree/master/example).

To run it:
```shell
git clone https://github.com/sleep/dir-loader
cd dir-loader
npm install
npm run example
```

## alternatives
dir-loader was created due to insuffciencies with [`require.context`](http://webpack.github.io/docs/context.html#require-context),
webpack's built-in solution for dynamic requires.

`require.context` provides a flat array of matched modules. This is the easiest way to dynamically require modules if your modules are non-hierarchical. But in the case you want to use the hierarchical information implicit in the filesystem structure, `require.context` falls short.

