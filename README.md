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

# Typescript
```shell
npm i -D npm i --save-dev @types/sinon
```

```javascript
import sinon from 'sinon'
let axiosStub: sinon.SinonStub
```


















<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>


# Methods
- https://sinonjs.org/releases/latest/spies/
- Should work with spies and stubs

<br><br>
<br><br>


## calledOnceWithExactly
```javascript
expect(axiosRequestStub.calledOnceWithExactly({ test: true )).toBe(true)
```

<br><br>

## calledOnceWith
```javascript
expect(getCurrentPriceStub.calledOnceWith('ETH', 'USD')).toBe(true)
```

<br><br>

## callCount
```javascript
expect(calculateWeiToUsdSpy.callCount).toBe(4)
```

<br><br>

## getCalls
```javascript
const calls = calculateWeiToUsdSpy.getCalls()

expect(calls[0].args).toEqual([
BigInt(expectedTransactions.result[0].value),
currencyExchangeHistoryResponse.market_data.current_price.usd
])

expect(calls[1].args).toEqual([
// eslint-disable-next-line max-len
BigInt(expectedTransactions.result[0].gasPrice) * BigInt(expectedTransactions.result[0].gasUsed),
currencyExchangeHistoryResponse.market_data.current_price.usd
])
```































<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>



# Stub
- This will be used to intercept functions and to return custom response
  
<details><summary>Click to expand..</summary>
    
<br><br>
<br><br>

## Single exported function
- As far as I read it is not working out of the box with sinon.js














<br><br>
<br><br>

## Error
```javascript
describe('[ERROR]', () => {
let axiosRequestStub: sinon.SinonStub

const error = new Error('Request failed')

beforeEach(() => {
    axiosRequestStub = sinon.stub(axios, 'request').rejects(error)
})

afterEach(() => {
    axiosRequestStub.restore()
})
  
it.only('should throw an HttpClientError when the request fails and custom Error title', async() => {
    axiosRequestStub.rejects(error)

    const config = {
        url: 'https://example.com',
        method: 'GET',
        errorMessage: 'Any crazy error..'
    } as RequestParams

    try {
        await axiosRequestWrapper(config)
        expect('Should not reach this line').toBe(false)
    } catch (e: any) {
        expect(e.message).toBe('Any crazy error..')
        expect(e.originalError).toBe(error)
        expect(e.name).toBe('HttpClientError')
    }
})
})
```









<br><br>
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

### Stub internal method call of instance method
- resolves will not work here and result in a fullfilled promise. Use instead returns()
```javascript
public async getTokenDetails(tokenAddress: string, ABI: any) {
    const tokenContract = this.createContract(ABI, tokenAddress)
}
```

```javascript
let createContractStub: sinon.SinonStub
beforeEach(() => {
    createContractStub = sinon.stub(ethCoinManager.contract, 'createContract').returns({
        test: true
    })
})
```

<br><br>

### Stub class instance property
```javascript
describe('init()', function() {
    let connStub: sinon.SinonStub

    beforeEach(() => {
        connStub = sinon.stub(<any>mongooseUtils, 'conn').value(undefined)
    })

    afterEach(async() => {
        connStub.restore()
    })
})
```


<br><br>

### Stub non singleton class instance method which is called inside of function
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


Other example:
```typescript
 getBalanceStub = sinon.stub(BalanceManager.prototype, 'getBalance').callsFake(() => {
                return Promise.resolve(balanceWei)
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
</details>

























































<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>

# Spy (https://sinonjs.org/releases/latest/spies/)
- This will be used to spy functions

<details><summary>Click to expand..</summary>

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

<br><br
<br><br>

## Reset spy history
```javascript
before(async () => {
    browserServiceSpy = sinon.spy(BrowserService.prototype)
    kafkaKontrollerSpy = sinon.spy(KafkaKontroller.prototype)
})

after(async () => {
    browserServiceSpy.getThumbnailUrlFromHtml.restore()
    kafkaKontrollerSpy.sendAvro.restore()
})

afterEach(() => {
    browserServiceSpy.getThumbnailUrlFromHtml.resetHistory()
    kafkaKontrollerSpy.sendAvro.resetHistory()
})
```










<br><br>
<br><br>


# Javascript Functions
```typescript
it('should make a successful request and use timeout', async() => {
        const config = {
            url: 'https://example.com',
            method: 'GET',
            timeout: 1000
        } as RequestParams
        
        const result = await axiosRequestWrapper(config)
        
        expect(result).toEqual(responseBody)
        expect(axiosRequestStub.calledOnceWithExactly(_.omit(config, 'timeout'))).toBe(true)
        
        expect(setTimeoutSpy.called).toBe(true)
})
```
















<br><br>
<br><br>


## Class

<br><br>

### Full class 
```javascript
const spy = sinon.spy(BrowserService.prototype)
```
- You can not use`spy.restore()` here on the class itself. You must restore the method e.g. `spy.methodName.restore()`
- This also means you can only 1x time create a spy on a class itself. When you restore the method stub then the class still is be spyed. So you can create a spy in your before() or youc reate it inside of a sandbox and then remove it

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


## Spy private methods

### Example #1
```javascript
describe('getConnection', () => {
        let initSpy: sinon.SinonSpy

        beforeEach(() => {
            initSpy = sinon.spy((<any>MongooseUtils).prototype, 'init')
        })

        afterEach(() => {
            initSpy.restore()
        })

        it('should return a valid existing mongoose connection', async() => {
            const conn = await (<any>mongooseUtils).getConnection()
            expect(initSpy.calledOnce).toBe(false)
            expect(conn).toBeTruthy()
            expect(conn).toBeInstanceOf(mongoose.Connection)
        })

        it('should return a valid mongoose connection by creating one', async() => {
            delete (<any>mongooseUtils).conn

            const conn = await (<any>mongooseUtils).getConnection()
            expect(initSpy.calledOnce).toBe(true)
            expect(conn).toBeTruthy()
            expect(conn).toBeInstanceOf(mongoose.Connection)
        })
})
```

### Example #2
- Get Prototype
- Notice that you will create a new object here which does not include properties from your created instance
```typescript
 beforeEach(() => {
        const example = new Example();
        const exampleProto = Object.getPrototypeOf(example);
        initSpy = sinon.spy(exampleProto, 'init')
  })
```










<br><br>
<br><br>
--  --  --  --  --  --  --  --  --  --  --  --  
<br><br>
<br><br>

## Dependencies

<br><br>

### Axios

<br><br>

#### Method #1 - If you use axios.request(config)
```javascript
import axios from 'axios'

describe('[SUCCESS]', () => {
        let axiosStub: sinon.SinonStub

        const { WALLET_ETH_ADDRESS } = process.env

        const requestObj = {
            nextUrl: {
                searchParams: new URLSearchParams({ address: WALLET_ETH_ADDRESS })
            }
        } as NextRequest

        const responseBody = {
            status: 200,
            data: { test: true }
        }

        beforeEach(() => {
            axiosStub = sinon.stub(axios, 'request').resolves(responseBody)
        })
        
        afterEach(() => {
            sinon.restore()
        })
        
        test('should get current balance of eth address in USD', async() => {
            expect(axiosStub.calledOnce).toBe(false)

            const response = await GET(requestObj) as NextResponse & { data: object }
            const responseData = await response.json()
            expect(response.status).toBe(200)
            expect(responseData.balance).toEqual(responseBody.data)

            // ==== AXIOS MOCKS ====
            expect(axiosStub.calledOnce).toBe(true)
            expect(axiosStub.firstCall.args[0]).toEqual({
                url: `${process.env.COINMARKETCAP_API_COIN_LISTINGS_URL}?symbol=ETH&convert=USD`,
                method: 'GET',
                headers: {
                    'X-CMC_PRO_API_KEY': process.env.API_KEY_COINMARKETCAP
                }
            })
        })
    })
```

<br><br>

#### Method #2 - If you use axios[method]
```javascript
beforeEach(async () => {
    axiosSpy = sinon.spy(axios, 'get')
})

afterEach(async () => {
    axiosSpy.restore()
})

it.only('should execute datasource by id', async () => {
    expect(axiosSpy.calledOnce).to.be.false

    const res = await executeDatasourceById(datasourceIdWithoutHandlebarsFormularData, params)
    expect(res.status).to.equal(201)
    expect(res.data).to.equal(nockResponseBody)

    expect(axiosSpy.calledOnce).to.be.true

    // ==== URL ====
    expect(axiosSpy.firstCall.args[0]).to.be.equal(doc_DatasourceIdWithoutHandlebarsFormularData.url)

    // ==== PAYLOAD ====
    const urlSearchParams = urlSearchParamsToObject(axiosSpy.firstCall.args[1])

    expect(urlSearchParams).to.be.deep.equal(doc_DatasourceIdWithoutHandlebarsFormularData.data)

    // ==== HEADERS ====
    expect(doc_DatasourceIdWithoutHandlebarsFormularData.headers['Content-Type']).to.be.equal(mimetype)
    expect(axiosSpy.firstCall.args[2].headers['Content-Type']).to.be.equal(mimetype)

    expect(axiosSpy.firstCall.args[2].httpsAgent).to.be.an.instanceof(HttpsProxyAgent)
    expect(axiosSpy.firstCall.args[2].httpAgent).to.be.an.instanceof(HttpProxyAgent)

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












</details



