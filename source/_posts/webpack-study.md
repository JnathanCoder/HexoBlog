---
title: webpack-study
date: 2019-05-23 15:29:00
tags: [webpack]
---

### webpack-dev-server-3

```js
var devMiddleware = require('webpack-dev-middleware')(compiler, {
    publicPath: webpackConfig.output.publicPath,
    hot: true,
    stats: {
        colors: true,
        chunks: false
    }
})

// use express
app.use(devMiddleware)

app.listen(port, host, function (err) {
    if (err) {
        console.log(err)
        return
    }
    console.log('Listening at ' + host + ':' + port + '\n')
})
```

<!-- More -->

### CommonsChunkPlugin

```js
// webpack.config.js
var webpack = require(‘webpack’)

entry: {
    vendor: ['jquery', 'lodash'],
    vuecore: './vuecore.js'
}, 
plugins: [
    // new webpack.optimize.CommonsChunkPlugin('vendor', 'vendor.bundle.js'),
    // new webpack.optimize.CommonsChunkPlugin({
    //     name: 'vuecore',
    //     filename: 'vuecore.js'
    // }),
    // Error:  CommonsChunkPlugins: While running in normal mode its not allowed to use a non-entry chunk (vuecore)

    // doing this instaed
    new webpack.optimize.CommonsChunkPlugin({
        names: ['vuecore','vendor']
    }),
    new webpack.optimize.CommonsChunkPlugin('vendor', 'vendor.bundle.js')
]
```