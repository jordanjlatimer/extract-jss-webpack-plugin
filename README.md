[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]

<div align="center">
  <img width="200" height="200"
    src="https://cdn.rawgit.com/webpack-contrib/extract-text-webpack-plugin/574e3200/logo.svg">
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200"
      src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
  <h1>Extract JSS Plugin</h1>
  <p>Extract JSS from a bundle, or bundles, into a separate file.</p>
</div>

<h2 align="center">Install</h2>

```bash
# for webpack 2
npm install --save-dev extract-jss-webpack-plugin
# for webpack 1
npm install --save-dev extract-jss-webpack-plugin@1.0.1
```

<h2 align="center">Usage</h2>

> :warning: For webpack v1, see [the README in the webpack-1 branch](https://github.com/webpack/extract-text-webpack-plugin/blob/webpack-1/README.md).

```js
const ExtractJssPlugin = require("extract-jss-webpack-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractJssPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
    new ExtractJssPlugin("styles.css"),
  ]
}
```

It moves all the required `*.css` modules in entry chunks into a separate CSS file. So your styles are no longer inlined into the JS bundle, but in a separate CSS file (`styles.css`). If your total stylesheet volume is big, it will be faster because the CSS bundle is loaded in parallel to the JS bundle.

|Advantages|Caveats|
|:---------|:------|
| Fewer style tags (older IE has a limit) | Additional HTTP request |
| CSS SourceMap (with `devtool: "source-map"` and `extract-jss-webpack-plugin?sourceMap`) | Longer compilation time |
| CSS requested in parallel | No runtime public path modification |
| CSS cached separate | No Hot Module Replacement |
| Faster runtime (less code and DOM operations) | ... |

<h2 align="center">Options</h2>

```js
new ExtractJssPlugin(options: filename | object)
```

|Name|Type|Description|
|:--:|:--:|:----------|
|**`id`**|`{String}`|Unique ident for this plugin instance. (For advanced usage only, by default automatically generated)|
|**`filename`**|`{String\|Function}`|Name of the result file. May contain `[name]`, `[id]` and `[contenthash]`|
|**`allChunks`**|`{Boolean}`|Extract from all additional chunks too (by default it extracts only from the initial chunk(s))<br />When using `CommonsChunkPlugin` and there are extracted chunks (from `ExtractJssPlugin.extract`) in the commons chunk, `allChunks` **must** be set to `true`|
|**`disable`**|`{Boolean}`|Disables the plugin|
|**`ignoreOrder`**|`{Boolean}`|Disables order check (useful for CSS Modules!), `false` by default|

* `[name]` name of the chunk
* `[id]` number of the chunk
* `[contenthash]` hash of the content of the extracted file

> :warning: `ExtractJssPlugin` generates a file **per entry**, so you must use `[name]`, `[id]` or `[contenthash]` when using multiple entries.

#### `#extract`

```js
ExtractJssPlugin.extract(options: loader | object)
```

Creates an extracting loader from an existing loader. Supports loaders of type `{ loader: [name]-loader -> {String}, options: {} -> {Object} }`.

|Name|Type|Description|
|:--:|:--:|:----------|
|**`options.use`**|`{String}`/`{Array}`/`{Object}`|Loader(s) that should be used for converting the resource to a CSS exporting module _(required)_|
|**`options.fallback`**|`{String}`/`{Array}`/`{Object}`|loader(e.g `'style-loader'`) that should be used when the CSS is not extracted (i.e. in an additional chunk when `allChunks: false`)|
|**`options.publicPath`**|`{String}`|Override the `publicPath` setting for this loader|


#### Multiple Instances

There is also an `extract` function on the instance. You should use this if you have more than one instance of  `ExtractJssPlugin`.

```js
const ExtractJssPlugin = require('extract-jss-webpack-plugin');

// Create multiple instances
const extractCSS = new ExtractJssPlugin('stylesheets/[name]-one.css');
const extractLESS = new ExtractJssPlugin('stylesheets/[name]-two.css');

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract([ 'css-loader', 'postcss-loader' ])
      },
      {
        test: /\.less$/i,
        use: extractLESS.extract([ 'css-loader', 'less-loader' ])
      },
    ]
  },
  plugins: [
    extractCSS,
    extractLESS
  ]
};
```

### Extracting Sass or LESS

The configuration is the same, switch out `sass-loader` for `less-loader` when necessary.

```js
const ExtractJssPlugin = require('extract-jss-webpack-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ExtractJssPlugin.extract({
          fallback: 'style-loader',
          //resolve-url-loader may be chained before sass-loader if necessary
          use: ['css-loader', 'sass-loader']
        })
      }
    ]
  },
  plugins: [
    new ExtractJssPlugin('style.css')
    //if you want to pass in options, you can do so:
    //new ExtractJssPlugin({
    //  filename: 'style.css'
    //})
  ]
}
```

### Modify filename

`filename` parameter could be `Function`. It passes `getPath` to process the format like `css/[name].css` and returns the real file name, `css/js/a.css`. You can replace `css/js` with `css` then you will get the new path `css/a.css`.


```js
entry: {
  'js/a': "./a"
},
plugins: [
  new ExtractJssPlugin({
    filename:  (getPath) => {
      return getPath('css/[name].css').replace('css/js', 'css');
    },
    allChunks: true
  })
]
```

<h2 align="center">Maintainers</h2>

<table>
  <tbody>
    <tr>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/166921?v=3&s=150">
        </br>
        <a href="https://github.com/bebraw">Juho Vepsäläinen</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars2.githubusercontent.com/u/8420490?v=3&s=150">
        </br>
        <a href="https://github.com/d3viant0ne">Joshua Wiens</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/533616?v=3&s=150">
        </br>
        <a href="https://github.com/SpaceK33z">Kees Kluskens</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/3408176?v=3&s=150">
        </br>
        <a href="https://github.com/TheLarkInn">Sean Larkin</a>
      </td>
    </tr>
  <tbody>
</table>


[npm]: https://img.shields.io/npm/v/extract-text-webpack-plugin.svg
[npm-url]: https://npmjs.com/package/extract-text-webpack-plugin

[node]: https://img.shields.io/node/v/extract-text-webpack-plugin.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/webpack-contrib/extract-text-webpack-plugin.svg
[deps-url]: https://david-dm.org/webpack-contrib/extract-text-webpack-plugin

[tests]: http://img.shields.io/travis/webpack-contrib/extract-text-webpack-plugin.svg
[tests-url]: https://travis-ci.org/webpack-contrib/extract-text-webpack-plugin

[cover]: https://coveralls.io/repos/github/webpack-contrib/extract-text-webpack-plugin/badge.svg
[cover-url]: https://coveralls.io/github/webpack-contrib/extract-text-webpack-plugin

[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
