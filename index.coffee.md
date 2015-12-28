    module.exports = invariate = (f) ->
      (o,args...)->
        if typeof o is 'object'
          for k,v of o
            do (k,v) =>
              f.call this, k, v, args...
        else
          [f.apply this, arguments]
