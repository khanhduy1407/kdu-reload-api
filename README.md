# kdu-reload-api

> Note: `kdu-reload-api@2.x` only works with `kdu@2.x`

Hot reload API for Kdu components. This is what [kdu-loader](https://github.com/khanhduy1407/kdu-loader) and [kdurial](https://github.com/khanhduy1407/kdurial) use under the hood.

## Usage

You will only be using this if you are writing some build toolchain based on Kdu components. For normal application usage, just use `kdu-loader` or `kdurial`.

``` js
// define a component as an options object
const myComponentOptions = {
  data () { ... },
  created () { ... },
  render () { ... }
}

// assuming Webpack's HMR API.
// https://webpack.js.org/guides/hot-module-replacement/
if (module.hot) {
  const api = require('kdu-reload-api')
  const Kdu = require('kdu')

  // make the API aware of the Kdu that you are using.
  // also checks compatibility.
  api.install(Kdu)

  // compatibility can be checked via api.compatible after installation
  if (!api.compatible) {
    throw new Error('kdu-reload-api is not compatible with the version of Kdu you are using.')
  }

  // indicate this module can be hot-reloaded
  module.hot.accept()

  if (!module.hot.data) {
    // for each component option object to be hot-reloaded,
    // you need to create a record for it with a unique id.
    // do this once on startup.
    api.createRecord('very-unique-id', myComponentOptions)
  } else {
    // if a component has only its template or render function changed,
    // you can force a re-render for all its active instances without
    // destroying/re-creating them. This keeps all current app state intact.
    api.rerender('very-unique-id', myComponentOptions)

    // --- OR ---

    // if a component has non-template/render options changed,
    // it needs to be fully reloaded. This will destroy and re-create all its
    // active instances (and their children).
    api.reload('very-unique-id', myComponentOptions)
  }
}
```
