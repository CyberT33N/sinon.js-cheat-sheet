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




























<br><br>
_________________________________
_________________________________
<br><br>


# Spy (https://sinonjs.org/releases/latest/spies/)

<br><br>

## Using a spy to wrap an existing method
```javascript
/*---- Example #1 ----*/
describe("Wrap existing method", function() {
    const sandbox = sinon.createSandbox();

    beforeEach(function() {
        sandbox.spy(jQuery, "ajax");
    });

    afterEach(function() {
        sandbox.restore();
    });

    it("should inspect jQuery.getJSON's usage of jQuery.ajax", function() {
        const url = "https://jsonplaceholder.typicode.com/todos/1";
        jQuery.getJSON(url);

        assert(jQuery.ajax.calledOnce);
        assert.equals(url, jQuery.ajax.getCall(0).args[0].url);
        assert.equals("json", jQuery.ajax.getCall(0).args[0].dataType);
    });
});





























/*---- Example #2 ----*/
// BrowserWrapper.js
module.exports = class BrowserWrapper {
    constructor(host, port){
        this.connectionService = new BrowserConnectionService(host, port)
    }
    
    async connect() {
        this.browser = await this.connectionService.connectToBrowser()
        this.createDisconnectListener()
    }

    async disconnect() {
        await this.browser.disconnect()
    }

    createDisconnectListener() {
        this.browser.on('disconnected', this._onDisconnect.bind(this) )
    }

    async _onDisconnect() {
        console.log('_onDisconnect')
    }
}

// test.js
describe.only('[PUPPETEER] BrowserWrapper Tests', function () {
    this.timeout(60000 * 5)
    let spy
    
    before(done => {
        (async()=>{
            spy = sinon.spy(browserWrapper, "_onDisconnect")
            await browserWrapper.connect()
        })().then(done).catch(done)
    })

    it('should fire disconnect listener', async () => {
        await browserWrapper.disconnect()
        spy // <-- contains details about the event if fired and so on..
        debugger
    })
})
```
