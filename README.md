Invariate
---------

If

    var f = invariate(function(name,value){})

then `f` can be called as

    f(name,value)

or as

    f({name1:value1,name2:value2,...})

in which case the function will be called for each (name,value) pair.

Additional parameters are passed as-is.
