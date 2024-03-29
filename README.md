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

## Reset spy
```javascript
let spy

beforeEach(() => {
    spy = sinon.spy(bootstrapObj, 'bootstrap')
})

afterEach(() => {
    spy.restore()
})

it('should throw error because app is missing', async () => {
    try {
        await spy();
    } catch (e) {
        expect(e.message).to.be.equal('Can not find app for bootstrapping')
        spy.called = false
    }

    expect(spy.called).to.be.equal(false)
})
```

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








<br><br>
<br><br>


## Express

<br><br>

### Middleware

<br><br>

#### Spy middleware

- Variant #1
```javascript
// routes.js
const proxy = require('express-http-proxy')

const routeBase = '/v1/Conditions'

// Die Middleware-Funktion, die getestet werden soll
const middleware = (req, res, next) => {
  // Mach etwas ...
  next();
};

module.exports = router => {
    router.use(routeBase, middleware)
}


// ---------------------------------------------------------------------------




describe('Bug test', () => {
   let router, app, masterRouter

   beforeEach(() => {
        app = express()
        router = require(`${process.cwd()}/src/conditions/conditions.routes`)
        masterRouter = express.Router()
        router(masterRouter)
   })

   it('should get targetgroup document and call route only 1 times because of wrong spelling', async () => {
        const spyMiddleware = sinon.spy(masterRouter)
        app.use(spyMiddleware)

        const server = http.createServer(app)
        server.listen(1337)// , host)

        const res = await axios.get('http://127.0.0.1:1337/v1/conditions')
        expect(res.status).to.be.equal(200)
        expect(spyMiddleware.callCount).to.eq(1)

        const res2 = await axios.get('http://127.0.0.1:1337/v1/conditions')
        expect(res2.status).to.be.equal(200)
        expect(spyMiddleware.callCount).to.eq(2)

        const res3 = await axios.get('http://127.0.0.1:1337/v1/Conditions')
        expect(res3.status).to.be.equal(200)
        expect(spyMiddleware.callCount).to.eq(3)
   })
})
```




- Variant #2
```javascript
const assert = require('assert');
const sinon = require('sinon');
const express = require('express');

const app = express();

// Die Middleware-Funktion, die getestet werden soll
const middleware = (req, res, next) => {
  // Mach etwas ...
  next();
};

describe('Middleware Test', () => {
  it('sollte einmal aufgerufen werden', () => {
    const spyMiddleware = sinon.spy(middleware);
    app.use(spyMiddleware);

    // Führe eine Anfrage an die App aus
    // Hier kannst du z.B. supertest verwenden, um eine HTTP-Anfrage zu simulieren
    // Für dieses Beispiel nehmen wir an, dass die Middleware einmal aufgerufen wird
    // Dies könnte z.B. bedeuten, dass der Endpunkt nur eine GET-Anfrage verarbeitet
    app.get('/test', (req, res) => {
      res.send('Test');
    });

    // Überprüfe, ob die Middleware genau einmal aufgerufen wurde
    assert(spyMiddleware.calledOnce);
  });

  it('sollte zweimal aufgerufen werden', () => {
    const spyMiddleware = sinon.spy(middleware);
    app.use(spyMiddleware);

    // Führe eine Anfrage an die App aus
    // Hier kannst du z.B. supertest verwenden, um eine HTTP-Anfrage zu simulieren
    // Für dieses Beispiel nehmen wir an, dass die Middleware zweimal aufgerufen wird
    // Dies könnte z.B. bedeuten, dass der Endpunkt sowohl GET- als auch POST-Anfragen verarbeitet
    app.get('/test', (req, res, next) => {
      next();
    });

    app.post('/test', (req, res) => {
      res.send('Test');
    });

    // Überprüfe, ob die Middleware genau zweimal aufgerufen wurde
    assert(spyMiddleware.calledTwice);
  });
});

```
