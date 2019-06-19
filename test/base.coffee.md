    chai = require 'chai'
    chai.should()

    describe 'Invariate', ->
      invariate = require '../index'
      it 'should support two arguments', ->
        f = invariate (k,v) -> "#{k},#{v}"
        f(2,3).should.deep.equal ['2,3']
        f(2:3).should.deep.equal ['2,3']
      it 'should support more than two arguments', ->
        f = invariate (k,v,a) -> "#{k},#{v},#{a}"
        f(2,3,4).should.deep.equal ['2,3,4']
        f(2:3,4).should.deep.equal ['2,3,4']

    describe 'Promised', ->
      invariate = require '../index'
      it 'should support Promises', ->
        f = invariate.promised (k,v) -> Promise.resolve "#{k},#{v}"
        (await f 2,3).should.deep.equal ['2,3']
        (await f 2:3).should.deep.equal ['2,3']

    describe 'Ack', ->
      invariate = require '../index'
      it 'should return a Promise', ->
        f = invariate.ack (k,v,ack) -> ack "#{k},#{v}"
        (await f 2,3).should.equal '2,3'
      it 'should ignore the outcome of the ack parameter', ->
        f = invariate.ack (k,v,ack) -> ack "#{k},#{v}"
        (await f 2, 3, (r) -> null).should.equal '2,3'
        (await f 2, 3, (r) -> throw new Error 'foo').should.equal '2,3'
      it 'should support ack', (done) ->
        f = invariate.ack (k,v,ack) -> ack "#{k},#{v}"
        f 2, 3, (r) ->
          r.should.equal '2,3'
          done()
        return

    describe 'Acked', ->
      invariate = require '../index'
      it 'should return a Promise', ->
        f = invariate.acked (k,v,ack) -> ack "#{k},#{v}"
        (await f 4:5).should.deep.equal ['4,5']
        (await f 4:5, (r) -> null).should.deep.equal ['4,5']
      it 'should support ack', (done) ->
        f = invariate.acked (k,v,ack) -> ack "#{k},#{v}"
        f 2:3, (r) ->
          r.should.equal '2,3'
          done()
        return
      it 'should support callback and Promise', (done) ->
        f = invariate.acked (k,v,ack) -> ack "#{k},#{v}"
        count = 0
        f 2:3, (r) ->
          r.should.equal '2,3'
          count++
          done() if count is 2
        .then (r) ->
          r.should.deep.equal ['2,3']
          count++
          done() if count is 2
        return
