# webpack-plugin-hash-output

Plugin to replace webpack chunkhash with an md5 hash of the final file conent.

## Installation

With npm
```
npm install webpack-plugin-hash-output --save-dev
```

With yarn
```
yarn add webpack-plugin-hash-output --dev
```

## Why?

There are other webpack plugins for hashing out there. But when they run, they don't "see" the final form of the code, because they run
*before* plugins like `webpack.optimize.UglifyJsPlugin`. In other words, if you change `webpack.optimize.UglifyJsPlugin` config, your
hashes won't change, creating potential conflicts with cached resources.

The main difference is that `webpack-plugin-hash-output` runs in the last compilation step. So any change in webpack or any other plugin
that actually changes the output, will be "seen" by this plugin, and therefore that change will be reflected in the hash.


## Usage

Just add this plugin as usual.

```
// webpack.config.js
var HashOutput = require('webpack-plugin-hash-output');

module.exports = {
    // ...
    output: {
        //...
        filename: '[name].[chunkhash].js',
    },
    plugins: [
        new HashOutput(options)
    ]
};
```

## Options


### `options.hashSize`

The prefix length of the hash to use, defaults to 20.

### `options.manifestFiles=[]`

Array of files that act like a manifest: files that has references to other files. You need to use this option if, for example, you are generating a common chunk or an external webpack manifest. In general, if a file references other files, it needs to be here.

Note: If you are using `html-webpack-plugin`, you don't need to include the html files here, it will work automatically.

Examples

```
module.exports = {
    entry: {
        entry1: './entry1.js',
        entry2: './entry2.js',
        vendor: './vendor.js',
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: "vendor",
        }),
        new OutputHash({
            // Because 'vendor' will contain the webpack manifest that references
            // other entry points
            manifestFiles: ['vendor']
        }),
    ]
};
```



```
$ md5 entry.32f1718dd08eccda2791.js

MD5 (entry.32f1718dd08eccda2791.js) = 32f1718dd08eccda2791ff7ed466bd98
```

All other assets (common files, manifest files, HTML output...) will use the new md5 hash to reference