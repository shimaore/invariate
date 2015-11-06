    chai = require 'chai'
    chai.should()

    describe 'Invariate', ->
      invariate = require '../index'
      it 'should support two arguments', ->
        f = invariate (k,v) -> "#{k},#{v}"
        f(2,3).should.deep.equal ['2,3']
        f(2:3).should.deep.equal ['2,3']
