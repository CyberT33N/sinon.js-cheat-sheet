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


# Stub



## Mongoose Models
```javascript
const sinon = require('sinon')
const mongoose = require('mongoose')

var UserSchema = new mongoose.Schema({name: String});
const Model = mongoose.model('User', UserSchema);

const find = () => {
    return {_id: '12345678912', name: 'test'}
}

sinon.stub(Model, 'find').callsFake(find)

const doc = Model.find()
console.log(doc)
```

<br><br>
<br><br>


## Dependencies

<br><br>

#### request
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
    const sandbox = sinon.createSandbox()

    beforeEach(done => {
        (async () => {
            spy = sandbox.spy(browserWrapper, "_onDisconnect")
            await browserWrapper.connect()
        })().then(done).catch(done)
    })

    afterEach(async () => {
        sandbox.restore()
        await browserWrapper.disconnect()
    })

   it('should fire disconnect listener', async () => {
        await browserWrapper.disconnect()
        expect(spy.called).to.equal(true)
    })

    it('simulate multiple connects and only one disconnect listener should be fired', async () => {
        await browserWrapper.connect()
        await browserWrapper.connect()
        await browserWrapper.connect()
        await browserWrapper.disconnect()
        expect(spy.calledOnce).to.equal(true)
    })
})





























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
