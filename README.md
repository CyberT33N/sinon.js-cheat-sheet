# Sinon.js Cheat Sheet
Sinon.js Cheat Sheet with the most needed stuff..


<br><br>

# Guides
- How to Stub Dependencies (https://www.youtube.com/watch?v=vXDbmrh0xDQ)

<br><br>

# Website
- https://sinonjs.org/



<br><br>
_________________________________
_________________________________
<br><br>


# Stub dependencies

<br><br>

## request
```javascript
describe('storeMessages()', () => {
  before(()=>{
    sinon.stub(request, 'get')
         .yields(null, null, JSON.stringify({id: 1}))
  });

  it('Should return id', done => {
    request(options, (e, r) => {
      expect(r).toStrictEqual({id: 1});
      done()
    });
  });
});
```
