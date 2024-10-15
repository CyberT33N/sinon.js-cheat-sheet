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
npm i -D @types/sinon sinon
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


# Matcher API
- https://sinonjs.org/releases/latest/matchers/
- Matchers can be passed as arguments to spy.calledOn, spy.calledWith, spy.returned and the corresponding sinon.assert functions as well as spy.withArgs. Matchers allow to be either more fuzzy or more specific about the expected value.
 
<details><summary>Click to expand..</summary>
    
<br><br>
<br><br>


<br><br>

## sinon.match.any
- https://sinonjs.org/releases/latest/matchers/#sinonmatchany
```javascript
mock.expects('myFunction').withExactArgs('foo', sinon.match.any, sinon.match.any).returns('foo');         
```

<br><br>

## sinon.match.object
```javascript
expect(kafkaSenderStub.calledOnceWith(headers, sinon.match.object)).to.be.true        
```





</details>

































<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>



# Methods
- https://sinonjs.org/releases/latest/spies/
- Should work with spies and stubs
 
<details><summary>Click to expand..</summary>
    
<br><br>
<br><br>



## Response
- The methods listed below will be related to the response of the spy/stub

<br><br>

### returnValues
```javascript
spy.returnValues[0]
```














<br><br>
<br><br>
<br><br>
<br><br>

## Expected state
- The methods listed below will be called on created stubs/spies


<br><br>
<br><br>

### calledWith
```javascript
expect(getTokenDetailsStub.calledWith(token0, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
expect(getTokenDetailsStub.calledWith(token1, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
expect(getTokenDetailsStub.calledWith(pair, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
```


<br><br>
<br><br>


### calledOnceWithExactly
```javascript
expect(axiosRequestStub.calledOnceWithExactly({ test: true )).toBe(true)
```

<br><br>

### calledOnceWith
```javascript
expect(getCurrentPriceStub.calledOnceWith('ETH', 'USD')).toBe(true)
```

<br><br>

### callCount
```javascript
expect(calculateWeiToUsdSpy.callCount).toBe(4)
```

<br><br>

### getCalls
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

### firstCall
```javascript
expect(acceptNewStateStub.firstCall.calledWithExactly({ newState })).to.be.true
```

<br><br>

### secondCall
```javascript
expect(acceptNewStateStub.secondCall.calledWithExactly({ newState2 })).to.be.true
```









<br><br>
<br><br>
<br><br>
<br><br>



## Returned state
- The methods below can be used to return something

<br><br>

### withArgs
```javascript
getTokenDetailsStub = sinon.stub(ethCoinManager.contract, 'getTokenDetails')
getTokenDetailsStub.withArgs(token0, ERC20_ABI_TOKEN_DETAILS).resolves(token0Details)
getTokenDetailsStub.withArgs(token1, ERC20_ABI_TOKEN_DETAILS).resolves(token1Details)
getTokenDetailsStub.withArgs(pair, ERC20_ABI_TOKEN_DETAILS).resolves(pairDetails)  
```



<br><br>
<br><br>

## onFirstCall 
```javascript
require("@fatso83/mini-mocha").install();

const sinon = require("sinon");
const referee = require("@sinonjs/referee");
const assert = referee.assert;

describe("stub", function () {
    it("should behave differently on consecutive calls with certain argument", function () {
        const callback = sinon.stub();
        callback
            .withArgs(42)
            .onFirstCall()
            .returns(1)
            .onSecondCall()
            .returns(2);
        callback.returns(0);

        assert.equals(callback(1), 0);
        assert.equals(callback(42), 1);
        assert.equals(callback(1), 0);
        assert.equals(callback(42), 2);
        assert.equals(callback(1), 0);
        assert.equals(callback(42), 0);
    });
});
```


<br><br>

## onSecondCall
```javascript
acceptNewStateStub.onSecondCall().resolves(docClone)
```







</details>



























<br><br>
<br><br>
_________________________________
_________________________________
<br><br>
<br><br>



# Stub
- https://sinonjs.org/releases/latest/stubs/
- This will be used to intercept functions and to return custom response
```javascript
innerFunctionStub = sinon.stub(nodemailer, 'createTransport').returns({
     sendMail: sinon.stub().resolves(emailResponseStub) // Stubbing sendMail method
});
```

<details><summary>Click to expand..</summary>







<br><br>
<br><br>

## return & resolve
- You use return for sync methods and resolve für async methods
```typescript
createSchemaStub = sinon.stub(
    MongooseUtils, 'createSchema'
).returns(mongooseSchema)
```
```typescript
getConnectionStub = sinon.stub(
    MongooseUtils.prototype, 'getConnection' as keyof MongooseUtils
).resolves(Promise.resolve(conn))
```



## module.exports
- Spy / Stub will not directly work with `module.exports = { fn }` and a default object destructering `const { fn } = require('./utils')`.
  - Instead you must use the object in your service `const utilsService = require('./utils')`

utils.js
```javascript
const applr = () => {
  console.log('applr()')
}

module.exports = { applr }
```

service.js
```javascript
const utilsService = require('./utils')

const fn = () => {
    utilsService.applr()
}

module.exports = { fn }
```

test.js
```javascript
const service = require(`/service.js`)
const utilsService = require('./utils')

const {
    fn
} = service

describe('[Task ID]', () => {
     let applrStub

     beforeEach(async () => {
         applrStub = sinon.stub(utilsService, 'applr')
     })

     it.only('should call applr()', async () => {
         await service.fn()
         expect(applrStub.called).to.be.equal(true)
     })
 })
```

<br><br>
<br><br>


### Export object with functions inside
- **It will only work when you export a object with functions inside that call each other with this. Or you create a class**

service.js
```javascript
const service = {
    a() {
        console.log('a()')
    },

    b() {
        console.log('a()')
        this.a()
    }
}

module.exports = service
```


test.js
```javascript
const service = require(`/service.js`)

const {
    a, b
} = service

describe('[Task ID]', () => {
     let aStub

     beforeEach(async () => {
         aStub = sinon.stub(service, 'a')
     })

     it.only('should call a()', async () => {
         await service.b()
         expect(aStub.called).to.be.equal(true)
     })
 })
```
- We must use service.b()

    
<br><br>
<br><br>

## Restore standalone stub
```javascript
beforeEach(async () => {
    repoStub = sinon.stub().returns({
        jsonSchema: {
            properties: {
                _updatedAt: true,
                _updatedBy: true,
                _createdAt: true,
                _createdBy: true,
                _deletedAt: true,
                _deletedBy: true
            }
        }
    })
})

afterEach(() => {
    repoStub.reset()
})
```







    
<br><br>
<br><br>

## Function


<br><br>
<br><br>

### Stub chained function calls
- https://stackoverflow.com/questions/37948135/how-do-i-stub-a-chain-of-methods-in-sinon
  
### Example #1
- IMHO, we can just use returns to do this. We don't need to use callsFake or mock it as function.
```javascript
// Cars.find().fetch()
sinon.stub(Cars, 'find').returns({
  fetch: sinon.stub().returns(anything)
});

// Cars.find().fetch().where()

sinon.stub(Cars, 'find').returns({
  fetch: sinon.stub().returnsThis(),
  where: sinon.stub().returns(anything)
});
```

<br>

### Example #2
```javascript
sandbox.stub(Cars, "find", () => {
    return {
        fetch: sinon.stub().returns(anything);
    };
});
```

<br>

### Example #3
```javascript
sandbox.stub(Cars, "find").callsFake(() => {
    return {
        fetch: sinon.stub().returns(anything);
    };
});
```

<br>

### Example #4
```javascript
beforeEach(() => {
    createContractStub = sinon.stub(ethCoinManager.contract, 'createContract').returns({
        methods: {
            name: () => ({ call: sinon.stub().resolves(mockTokenDetails.name) }),
            symbol: () => ({ call: sinon.stub().resolves(mockTokenDetails.symbol) }),
            decimals: () => ({ call: sinon.stub().resolves(mockTokenDetails.decimals) }),
            totalSupply: () => ({ call: sinon.stub().resolves(mockTokenDetails.totalSupply) })
        }
    } as any)
})
```




<br><br>
<br><br>

### Stub function call with different args
```javascript
const token0Details = await this.getTokenDetails(token0, ERC20_ABI_TOKEN_DETAILS)
```

```javascript
beforeEach(() => {
    getTokenDetailsStub = sinon.stub(ethCoinManager.contract, 'getTokenDetails')

    getTokenDetailsStub.withArgs(token0, ERC20_ABI_TOKEN_DETAILS).resolves(token0Details)
    getTokenDetailsStub.withArgs(token1, ERC20_ABI_TOKEN_DETAILS).resolves(token1Details)
    getTokenDetailsStub.withArgs(pair, ERC20_ABI_TOKEN_DETAILS).resolves(pairDetails)    
})

it('should listen for new pair events and save them to the database', async() => {
    expect(getTokenDetailsStub.calledWith(token0, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
    expect(getTokenDetailsStub.calledWith(token1, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
    expect(getTokenDetailsStub.calledWith(pair, ERC20_ABI_TOKEN_DETAILS)).toBe(true)
})
```













<br><br>
<br><br>
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
<br><br>
<br><br>

# Events
- The best approach is to simply outsource your callback of your event into a own method that you can easily export it and write unit tests for this method.
  - Then you only write 1x unit tests which makes sure that the correct callback function is called.

<br><br>
<br><br>


## Stub Service Event with Event Emitter
- Stub the event in your service with an event emitter which you are emitting in your test. This is how you can trigger your callback in this case `getNewPairsEventHandler()`
  - `getNewPairsEventHandler()` in this case is a class method which can be easily stubbed or spyed with sinon if needed.

service.ts:
```typescript
private getNewPairsEventHandler() {
    return async(event: pairEvent) => {
       //...
    }
}

// Listen for new pairs
public async getNewPairs() {
    const newPairsEvent = await this.uniswapPairCreatedFactory.events.PairCreated()

    newPairsEvent.on('data', this.getNewPairsEventHandler())

    newPairsEvent.on('error', (e: Error) => {
        throw new BaseError('Error fetching new pairs', e)
    })
}
```

test.ts:
```typescript
  describe('[SUCCESS]', () => {
      let uniswapPairCreatedFactoryStub: sinon.SinonStub
      let eventHandlerFn: any
      let newPairsEvent: EventEmitter
      let uniswapPairCreatedFactoryStub: sinon.SinonStub

      const expectedEventArgs = {
            returnValues: { token0, token1, pair }
        }

      beforeEach(() => {
          eventHandlerFn = (<any>ethCoinManager.contract).getNewPairsEventHandler()

          newPairsEvent = new EventEmitter()

          uniswapPairCreatedFactoryStub = sinon.stub(
              ethCoinManager.contract.uniswapPairCreatedFactory.events, 'PairCreated'
          ).resolves(newPairsEvent)
      })    

      afterEach(() => {
          uniswapPairCreatedFactoryStub.restore()
      })
  
      it.only('should listen for new pair events and save them to the database', async() => {
          await getNewPairs()

          await new Promise(resolve => {
              newPairsEvent.once('data', () => {
                  resolve(true)
              })
        
              newPairsEvent.emit('data', expectedEventArgs)
          })

          expect(anythinghere)
      })
  })
```

<br><br>
<br><br>


## Stub Service Event with Event Emitter and throw Error
- Same idea like above `Stub Service Event with Event Emitter`
  - However, in this example we will trigger the error Event. It is just a callback with a catch of the error and then pass it to a custom error logger.

service.ts
```javascript
// Listen for new pairs
public async getNewPairs() {
    const newPairsEvent = await this.uniswapPairCreatedFactory.events.PairCreated()

    newPairsEvent.on('data', this.getNewPairsEventHandler())

    newPairsEvent.on('error', (e: Error) => {
        throw new BaseError('Error fetching new pairs', e)
    })
}
```

test.js
```javascript
describe('[newPairsEvent]', () => {
    let newPairsEvent: EventEmitter
    let uniswapPairCreatedFactoryStub: sinon.SinonStub

    const errorMessage = 'Test error'

    beforeEach(() => {
        newPairsEvent = new EventEmitter()

        uniswapPairCreatedFactoryStub = sinon.stub(
            ethCoinManager.contract.uniswapPairCreatedFactory.events, 'PairCreated'
        ).resolves(newPairsEvent)
    })    

    afterEach(() => {
        uniswapPairCreatedFactoryStub.restore()
    })

    it('should throw an error if there is an error fetching new pairs', async() => {
        await ethCoinManager.contract.getNewPairs()

        try {
            newPairsEvent.emit('error', new Error(errorMessage))
            expect(true).toBe(false)
        } catch (e: any) {
            expect(e.name).toBe('BaseError')
            expect(e.message).toBe('Error fetching new pairs')
            expect(e.httpStatus).toBe(500)
            expect(e.e.message).toBe(errorMessage)
        }
    })
})
```















































<br><br>
<br><br>
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
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
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
<br><br>
<br><br>

## Dependencies
- https://sinonjs.org/how-to/stub-dependency/
  
dependencyModule.js:
```javascript
function getSecretNumber() {
  return 44;
}

module.exports = {
  getSecretNumber,
};
```

moduleUnderTest.js:
```javascript
const dependencyModule = require("./dependencyModule");

function getTheSecret() {
  return `The secret was: ${dependencyModule.getSecretNumber()}`;
}

module.exports = {
  getTheSecret,
};
```

test.js:
```javascript
const assert = require("assert");
const sinon = require("sinon");

const dependencyModule = require("./dependencyModule");
const { getTheSecret } = require("./moduleUnderTest");

describe("moduleUnderTest", function () {
  describe("when the secret is 3", function () {
    it("should be returned with a string prefix", function () {
      sinon.stub(dependencyModule, "getSecretNumber").returns(3);
      const result = getTheSecret();
      assert.equal(result, "The secret was: 3");
    });
  });
});
```









<br><br>
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
<br><br>
<br><br>





### Mongoose

<br><br>

#### Models
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

#### Queries
```javascript
sinon.stub(mongoose.Query.prototype, "exec").yields({ name: "MongoError" }, null);
```

























<br><br>
<br><br>
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
-- -- -- -- -- -- -- -- -- -- -- -- -- -- 
<br><br>
<br><br>


## Class


<br><br>
<br><br>

### Stub constructor
- As far as I read it is not possible

<br><br>
<br><br>

### Stub privat method
```javascript

describe('getInstance()', () => {
    let initStub: sinon.SinonStub

    beforeEach(() => {
        initStub = sinon.stub((<any>ModelManager).prototype, 'init').resolves()
    })

    afterEach(() => {
        initStub.restore()
    })

    it.only('should create new instance', async() => {
        const modelManager = await ModelManager.getInstance()

        expect(initStub.calledOnce).toBe(true)
        expect(modelManager.models).toEqual([])
    })
})
```



<br><br>
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





<br><br>
<br><br>

### Mock method properties (arrow functions)
For good writing style you should use arrow functions to avoid tseslint error unbount-methods.
```typescript
    /**
     * Creates a Mongoose model based on the given name, schema, and database name.
     * @template TMongooseSchema - The type of the mongoose schema.
     * @param modelDetails - An object containing the model's details.
     * @returns A promise that resolves to the created Mongoose Model instance.
     */
    public createModel = async({
        modelName,
        schema,
        dbName
    })  => {
        const mongooseUtils = MongooseUtils.getInstance(dbName)
        const Model = await mongooseUtils.createModel(schema, modelName)
        
        // Ensure indexes are created for the model
        await Model.createIndexes()
        return Model
    }
```

- **NOTICE that sinon stub/spies will not work anymore with the the class prototype when you use arrow functions and maybe other things also not working. You can only use the instance. For this reason I would recommend to not use arrow functions in cases where you can the function to e.g. a event handler you should bind the instance to it .bind(classInstacne)**
```typescript
// Will not work anymore
initStub = sinon.stub(
    ModelManager.prototype, 'init' as keyof ModelManager
).resolves()

// Will work
initStub = sinon.stub(
    modelManager, 'init' as keyof ModelManager
).resolves()
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






## module.exports
- Spy / Stub will not directly work with `module.exports = { fn }` and a default object destructering `const { fn } = require('./utils')`.
  - Instead you must use the object in your service `const utilsService = require('./utils')`

utils.js
```javascript
const applr = () => {
  console.log('applr()')
}

module.exports = { applr }
```

service.js
```javascript
const utilsService = require('./utils')

const fn = () => {
    utilsService.applr()
}

module.exports = { fn }
```


test.js
```javascript
const service = require(`/service.js`)
const utilsService = require('./utils')

const {
    fn
} = service

describe('[Task ID]', () => {
     let applrSpy

     beforeEach(async () => {
         applrSpy = sinon.spy(utilsService, 'applr')
     })

     it.only('should call applr()', async () => {
         await service.fn()
         expect(applrSpy.called).to.be.equal(true)
     })
 })
```

<br><br>
<br><br>


### Export object with functions inside
- **It will only work when you export a object with functions inside that call each other with this. Or you create a class**

service.js
```javascript
const service = {
    a() {
        console.log('a()')
    },

    b() {
        console.log('a()')
        this.a()
    }
}

module.exports = service
```


test.js
```javascript
const service = require(`/service.js`)

const {
    a, b
} = service

describe('[Task ID]', () => {
     let aSpy

     beforeEach(async () => {
         aSpy = sinon.spy(service, 'a')
     })

     it.only('should call a()', async () => {
         await service.b()
         expect(aSpy.called).to.be.equal(true)
     })
 })
```
- We must use service.b()





<br><br>
<br><br>

<br><br>
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

# firstCall
```shell
expect(spy.firstCall.calledWith(modelName)).to.be.true
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
<br><br>

### Spy constructor
- As far as I see there is now way to spy it

<br><br>

### Full class 
```javascript
const spy = sinon.spy(BrowserService.prototype)
```
- You can not use`spy.restore()` here on the class itself. You must restore the method e.g. `spy.methodName.restore()`
- This also means you can only 1x time create a spy on a class itself. When you restore the method stub then the class still is be spyed. So you can create a spy in your before() or youc reate it inside of a sandbox and then remove it

<br><br>

### method

<br><br>

#### Spy instance
```javascript
let dispatchSpy

beforeEach(async () => {
    const lifecycleValidator = new LifecycleValidator()  
    dispatchSpy = sinon.spy(lifecycleValidator.machine, 'dispatch')
})

 it.only('should change', async () => {
    expect(dispatchSpy.calledOnce).to.be.true
    expect(dispatchSpy.calledOnceWithExactly(ACTIVE, true, projectId, true)).to.be.true
})
```

<br><br>

#### Spy prototype
```javascript
let dispatchSpy

beforeEach(async () => {
    dispatchSpy = sinon.spy(LifecycleValidator.prototype, 'dispatch')
})

it.only('should change', async () => {
    expect(dispatchSpy.calledOnce).to.be.true
    expect(dispatchSpy.calledOnceWithExactly(ACTIVE, true, projectId, true)).to.be.true
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



