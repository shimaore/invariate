    module.exports = invariate = (f) ->
      ->
        if typeof arguments[0] is 'object'
          for k,v of arguments[0]
            do (k,v) ->
              f.call this, k, v
        else
          [f.apply this, arguments]
