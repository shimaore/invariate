Invariate
---------

Given a function that takes two arguments (for example `(key,value)`), returns a new function that accepts as parameters:
- `(key,value)`, in which case the function is called once with the two parameters;
- an Object, in which case the function is called once for each entry in the object, with the object's key and value pair as parameters.
In all cases, returns a new Array containing the value(s) returned by the original function.

    module.exports = invariate = (f) ->
      (o,args...)->
        if typeof o is 'object'
          for k,v of o
            do (k,v) =>
              f.call this, k, v, args...
        else
          [f.apply this, arguments]

    module.exports.invariate = invariate

Promised
--------

Same as invariate, but properly handles Promise return values, returning a Promise that gets resolved with the Array containing all the resolved values.

    Promise = require 'bluebird'

    module.exports.promised = promised = (f) ->
      g = invariate f
      ->
        Promise.all g.apply this, arguments

Ack
---

Given a function that accepts `(key,value,ack)` as its paramaters (such as Socket.IO's `emit`), returns a new function that accepts either `(k,v)` or `(k,v,ack)` as its parameters, and behaves the same as the original function except that a Promise is also returned.
(Think of it as `promisify` for `ack`.)

    module.exports.ack = ack = (f) ->
      (k,v,ack) ->
        p = new Promise (resolve,reject) =>
          try
            f.call this, k,v, (data) ->
              resolve data
          catch error
            reject error
        p.bind this
        if ack?
          p.then =>
            ack.apply this, arguments
        return p

Acked
-----

Combines `ack` with `promised` for full effect: `promised(acked(f))` will return an invariate function that when used, returns a Promise that is resolved once all function calls are ack'ed.

    module.exports.acked = invariate_acked = (f) -> promised ack f
