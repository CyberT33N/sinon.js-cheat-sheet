# Sinon.js Cheat Sheet
Sinon.js Cheat Sheet with the most needed stuff..


<br><br>

# Guides
- How to Stub Dependencies (https://www.youtube.com/watch?v=vXDbmrh0xDQ)

<br><br>

# Website
- https://sinonjs.org/
















<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>



# Stub
- This will be used to intercept functions and to return custom response

<br><br>

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

### External functions
```javascript
innerFunctionStub = sinon.stub(nodemailer, 'createTransport').returns({
     sendMail: sinon.stub().resolves(emailResponseStub) // Stubbing sendMail method
});

```

<br><br>

### request
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
<br><br>

## Class

<br><br>

### Mock non singleton class instance method which is called inside of function
```javascript
comst kafkaHelper = new KafkaHelper()

const checkTaskId = async (body, topic, headers) => {
     await kafkaHelper.sendMessageAvro(body, topic, headers)
}
```

<br><br>

Here we mock method sendMessageAvro of the class KafkaHelper
- **We must use .prototype for classes**
```javascript
describe('[KafkaHelper send avro message]', () => {
    let sendMessageAvroStub

    const { KafkaHelper } = require('./KafkaHelper')

    beforeEach(() => {
        sendMessageAvroStub = sinon.stub(KafkaHelper.prototype, "sendMessageAvro").resolves({})
    })

    afterEach(() => {
        sinon.restore()
    })

    it('should call sendMessageAvro method with correct arguments', async () => {
        const { taskId } = doc_emailMailingWithTaskIdScheduled

        const expectedBody = {
            'test': true
        }

        const expectedHeaders = {
            'test': projectId,
        }

        await checkTaskId(body, topic, headers)

        sinon.assert.calledWith(sendMessageAvroStub, expectedBody, 'any topic', expectedHeaders)
        expect(sendMessageAvroStub.calledOnce).to.be.true
    })
})
```


































<br><br>
<br><br>

### Mock method response of class with args
```javascript
/**
 * Represents a SlackBot.
 */
class SlackBot {
    /**
     * Constructs a new SlackBot instance. Singleton
     */
    constructor() {}

    /**
     * Sends a message to a channel.
     * @param {string} channelId - The ID of the channel.
     * @param {string} text - The text of the message.
     */
    async sendMessage(channelId, text) {
        try {
            const res = await this.app.client.chat.postMessage({
                token: this.botToken,
                channel: channelId,
                text
            })

            return res
        } catch (e) {
            throw new BaseError('Error while sending message to Slack channel.', e)
        }
    }
}
```

```javascript
let sendMessageStub

beforeEach(async() => {
    const slackBot = new SlackBot()
    sendMessageStub = sinon.stub(slackBot, 'sendMessage')
})

afterEach(() => {
    sendMessageStub.restore()
})
```

```javascript
it('should send a message to a channel', async () => {
    const channelId = 'channel-id'
    const text = 'Hello, world!'

    const expectedResponse = {
        channel: channelId,
        text: text,
    }

    sendMessageStub.withArgs(channelId, text).resolves(expectedResponse)

    const res = await slackBot.sendMessage(channelId, text)
    expect(res.channel).to.equal(channelId)
    expect(res.text).to.equal(text)
    expect(sendMessageStub.calledOnceWith(channelId, text)).to.be.true
})
```

<br><br>
<br><br>


### Throw error inclass instance method
- In this case the instance is kafkaHelper and the method is connect
```javascript
const error = new Error('Connection failed')
sinon.stub(kafkaHelper, 'connect').throws(error)
```










<br><br>
<br><br>

### Mock class instance property
- In this case we mock this.producer of our class instance kafkaHelper
```javascript
describe('sendMessage', () => {
    let producerMock

    const msg = 'Test message'
    const topic = 'test'

    beforeEach(() => {
        producerMock = {
            connect: sinon.stub().resolves(),
            send: sinon.stub().resolves()
        }

        kafkaHelper.producer = producerMock
    })

    it('should send message to topic', async () => {
        await kafkaHelper.sendMessage(msg, topic)
        expect(producerMock.send.calledOnce).to.be.true
        expect(producerMock.send.firstCall.args[0]).to.deep.equal({
            topic,
            messages: [{ value: msg }]
        })
    })

     it('should throw an error if sending message fails', async () => {
          const error = new Error('Message sending failed')
          sinon.stub(kafkaHelper, 'connect').resolves()
          producerMock.send.rejects(error)

          try {
              await kafkaHelper.sendMessage(msg, topic)
          } catch (e) {
              expect(e).to.equal(error)
          }
      })
})
```

















































<br><br>
_________________________________
_________________________________
<br><br>


# Spy (https://sinonjs.org/releases/latest/spies/)
- This will be used to spy functions


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

## Class

<br><br>

### Full class 
```javascript
const spy = sinon.spy(BrowserService.prototype)
```
- You can not use`spy.restore()` here on the class itself. You must restore the method e.g. `spy.methodName.restore()`

<br><br>

### Using a spy to wrap an existing method
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
<br><br>
<br><br>

## Dependencies

<br><br>

### Axios

```javascript
beforeEach(async () => {
    axiosStub = sinon.spy(axios, doc_DatasourceIdWithoutHandlebarsFormularData.method.toLowerCase())
})

afterEach(async () => {
    axiosStub.restore()
})

it.only('should execute datasource by id', async () => {
    expect(axiosStub.calledOnce).to.be.false

    const res = await executeDatasourceById(datasourceIdWithoutHandlebarsFormularData, params)
    expect(res.status).to.equal(201)
    expect(res.data).to.equal(nockResponseBody)

    expect(axiosStub.calledOnce).to.be.true

    // ==== URL ====
    expect(axiosStub.firstCall.args[0]).to.be.equal(doc_DatasourceIdWithoutHandlebarsFormularData.url)

    // ==== PAYLOAD ====
    const urlSearchParams = urlSearchParamsToObject(axiosStub.firstCall.args[1])

    expect(urlSearchParams).to.be.deep.equal(doc_DatasourceIdWithoutHandlebarsFormularData.data)

    // ==== HEADERS ====
    expect(doc_DatasourceIdWithoutHandlebarsFormularData.headers['Content-Type']).to.be.equal(mimetype)
    expect(axiosStub.firstCall.args[2].headers['Content-Type']).to.be.equal(mimetype)

    expect(axiosStub.firstCall.args[2].httpsAgent).to.be.an.instanceof(HttpsProxyAgent)
    expect(axiosStub.firstCall.args[2].httpAgent).to.be.an.instanceof(HttpProxyAgent)

    expect(res.data.additional?.handlebarsError).to.not.exists
})
```








































<br><br>
<br><br>





## Class

<br><br>

### Check if method was called
- kafkaHelper is the instance of the class and connect is the method
```javascript
const connectSpy = sinon.spy(kafkaHelper, 'connect')
await kafkaHelper.connect()
expect(connectSpy.calledOnce).to.be.true

afterEach(() => {
    sinon.restore()
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
