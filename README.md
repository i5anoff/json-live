# json-live

[![experimental](http://badges.github.io/stability-badges/dist/experimental.svg)](http://github.com/badges/stability-badges)

[(demo)](https://www.youtube.com/watch?v=ylV7aqswHYg&feature=youtu.be)

Live-update JSON objects on file change, without destroying application state. This is ideal for animations and rapid UI development. 

This comes in the form of a server and browserify transform, but requires no frontend code changes. Example:

```js
var data = require('./model.json')

setInterval(function() {
    console.log("My name is", data.name)
}, 1000)
```

With `json-live` running, changing the `model.json` file will update the values of `data` during runtime.

```json
{
    "name": "mattdesl",
    "color": "blue"
}
```

Comments/suggestions/PRs/etc welcome. Thanks to @hughsk and [glslify-live](https://github.com/hughsk/glslify-live) which set the groundwork for most of the code here.

## Usage

[![NPM](https://nodei.co/npm/json-live.png)](https://www.npmjs.com/package/json-live)

First, install the tool locally:

```sh
npm install json-live --save-dev
```

You first need to run the `json-live` server. You can run it directly from shell, like so:  

```sh
./node_modules/.bin/json-live 
```

However, the preferred solution is to run the server with a `package.json` scripts field.

Then, include `-t json-live` as a transform argument when bundling.

```sh
browserify index.js -t json-live > bundle.js
```

For example, with [wzrd](https://github.com/maxogden/wzrd) your local scripts might look like this. You can optionally use [garnish](https://github.com/mattdesl/garnish) for pretty-printing in the terminal.

```json
{
    "scripts": {
        "live": "json-live | garnish",
        "start": "wzrd index.js:bundle.js -- -t json-live | garnish",
        "build": "browserify index.js | uglifyjs -cm > bundle.js"
    }
}
```

Now, first you would `npm run live` to initiate the json-live server. Then in another process, `npm run start` for the development server. Note the `build` script doesn't include the transform, since it is not needed for production.

You can open `localhost:9966` to see the resulting bundle, and start making changes to JSON files to see them updated during runtime.

## Event Emitter

The default browser export for this module returns an event emitter which sends `update` events when the JSON changes. It sends the parameters `(object, id)`, where `object` is the JSON data that has been updated, and `id` is a path to that JSON file.

```js
var data = require('./foo.json')

var live = require('json-live')
live.on('update', function(object) {
    console.log(object === data) // should be true  
})
```

Without the transform included, the event emitter will simply not emit anything.

## License

MIT, see [LICENSE.md](http://github.com/mattdesl/json-live/blob/master/LICENSE.md) for details.
