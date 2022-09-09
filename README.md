# bybit-api
[![Tests](https://circleci.com/gh/tiagosiebler/bybit-api.svg?style=shield)](https://circleci.com/gh/tiagosiebler/bybit-api)
[![npm version](https://img.shields.io/npm/v/bybit-api)][1] [![npm size](https://img.shields.io/bundlephobia/min/bybit-api/latest)][1] [![npm downloads](https://img.shields.io/npm/dt/bybit-api)][1]
[![last commit](https://img.shields.io/github/last-commit/tiagosiebler/bybit-api)][1]
[![CodeFactor](https://www.codefactor.io/repository/github/tiagosiebler/bybit-api/badge)](https://www.codefactor.io/repository/github/tiagosiebler/bybit-api)

[![connector logo](https://cdn.builder.io/api/v1/image/assets%2Ffddee401a9284ab792b271538c28932d%2F5190c99403ac474699a1959fdee1ab6c?format=webp&width=2000)][1]

[1]: https://www.npmjs.com/package/bybit-api

Node.js connector for the Bybit APIs and WebSockets:
- TypeScript support (with type declarations for most API requests & responses).
- Integration tests with real API calls validating any changes before they reach npm.
- Robust WebSocket integration with connection heartbeats & automatic reconnection.
- Browser support (via webpack bundle - see "Browser Usage" below).

## Installation
`npm install --save bybit-api`

## Issues & Discussion
- Issues? Check the [issues tab](https://github.com/tiagosiebler/bybit-api/issues).
- Discuss & collaborate with other node devs? Join our [Node.js Algo Traders](https://t.me/nodetraders) engineering community on telegram.

## Related projects
Check out my related projects:
- Try my connectors:
  - [ftx-api](https://www.npmjs.com/package/ftx-api)
  - [bybit-api](https://www.npmjs.com/package/bybit-api)
  - [binance](https://www.npmjs.com/package/binance)
  - [okx-api](https://www.npmjs.com/package/okx-api)
- Try my misc utilities:
  - [orderbooks](https://www.npmjs.com/package/orderbooks)
- Check out my examples:
  - [awesome-crypto-examples](https://github.com/tiagosiebler/awesome-crypto-examples)

## Documentation
Most methods accept JS objects. These can be populated using parameters specified by Bybit's API documentation, or check the type definition in each class within this repository (see table below for convenient links to each class).
- [Bybit API Docs (choose API category from the tabs at the top)](https://bybit-exchange.github.io/docs/futuresV2/inverse/#t-introduction).

## Structure
The connector is written in TypeScript. A pure JavaScript version can be built using `npm run build`, which is also the version published to [npm](https://www.npmjs.com/package/bybit-api).

This connector is fully compatible with both TypeScript and pure JavaScript projects. The version on npm is the output from the `build` command and can be used in projects without TypeScript (although TypeScript is definitely recommended).

- [src](./src) - the whole connector written in TypeScript
- [lib](./lib) - the JavaScript version of the project (built from TypeScript). This should not be edited directly, as it will be overwritten with each release.
- [dist](./dist) - the packed bundle of the project for use in browser environments.
- [examples](./examples) - some implementation examples & demonstrations. Contributions are welcome!

---
## REST API Clients
Each REST API group has a dedicated REST client. To avoid confusion, here are the available REST clients and the corresponding API groups:
|                         Class                         	|                                                Description                                       	           |
|:-----------------------------------------------------:	|:-----------------------------------------------------------------------------------------------------------: |
| [InverseClient](src/inverse-client.ts)                	| [Inverse Perpetual Futures (v2) APIs](https://bybit-exchange.github.io/docs/futuresV2/inverse/)              |
| [LinearClient](src/linear-client.ts)                  	| [USDT Perpetual Futures (v2) APIs](https://bybit-exchange.github.io/docs/futuresV2/linear/#t-introduction)   |
| [InverseFuturesClient](src/inverse-futures-client.ts) 	| [Inverse Futures (v2) APIs](https://bybit-exchange.github.io/docs/futuresV2/inverse_futures/#t-introduction) |
| [SpotClient](src/spot-client.ts)                      	| [Spot Market (v1) APIs](https://bybit-exchange.github.io/docs/spot/v1/#t-introduction)                   	     |
| [AccountAssetClient](src/account-asset-client.ts)       | [Account Asset APIs](https://bybit-exchange.github.io/docs/account_asset/#t-introduction)                    |
| [USDCPerpetualClient](src/usdc-perpetual-client.ts)     | [USDC Perpetual APIs](https://bybit-exchange.github.io/docs/usdc/option/?console#t-querydeliverylog)         |
| [USDCOptionClient](src/usdc-option-client.ts)           | [USDC Option APIs](https://bybit-exchange.github.io/docs/usdc/option/#t-introduction)                        |
| Copy Trading                                           	| Under Development              	                        	                        	                         |
| Spot v3                                               	| Under Development              	                        	                        	                         |
| Derivatives V3 unified margin                         	| Under Development              	                        	                        	                         |
| [WebsocketClient](src/websocket-client.ts)              | All WebSocket Events (Public & Private for all API categories)                                               |

Examples for using each client can be found in:
- the [examples](./examples) folder.
- the [awesome-crypto-examples](https://github.com/tiagosiebler/awesome-crypto-examples) repository.

If you're missing an example, you're welcome to request one. Priority will be given to [github sponsors](https://github.com/sponsors/tiagosiebler).

### Usage
Create API credentials on Bybit's website:
- [Livenet](https://bybit.com/app/user/api-management?affiliate_id=9410&language=en-US&group_id=0&group_type=1)
- [Testnet](https://testnet.bybit.com/app/user/api-management)

All REST clients have can be used in a similar way. However, method names, parameters and responses may vary depending on the API category you're using!

```javascript
const {
  InverseClient,
  LinearClient,
  InverseFuturesClient,
  SpotClient,
  USDCOptionClient,
  USDCPerpetualClient,
  AccountAssetClient,
} = require('bybit-api');

const restClientOptions = {
  // override the max size of the request window (in ms)
  recv_window?: number;

  // how often to sync time drift with bybit servers
  sync_interval_ms?: number;

  // Default: false. Disable above sync mechanism if true.
  enable_time_sync?: boolean;

  // Default: false. If true, we'll throw errors if any params are undefined
  strict_param_validation?: boolean;

  // Optionally override API protocol + domain
  // e.g 'https://api.bytick.com'
  baseUrl?: string;

  // Default: true. whether to try and post-process request exceptions.
  parse_exceptions?: boolean;
};

const API_KEY = 'xxx';
const PRIVATE_KEY = 'yyy';
const useLivenet = false;

const client = new InverseClient(
  API_KEY,
  PRIVATE_KEY,

  // optional, uses testnet by default. Set to 'true' to use livenet.
  useLivenet,

  // restClientOptions,
  // requestLibraryOptions
);

client.getApiKeyInfo()
  .then(result => {
    console.log("getApiKeyInfo result: ", result);
  })
  .catch(err => {
    console.error("getApiKeyInfo error: ", err);
  });

client.getOrderBook({ symbol: 'BTCUSD' })
  .then(result => {
    console.log("getOrderBook result: ", result);
  })
  .catch(err => {
    console.error("getOrderBook error: ", err);
  });
```

## WebSockets
All API groups can be used via a shared `WebsocketClient`. However, make sure to make one instance of the WebsocketClient per API group (spot vs inverse vs linear vs linearfutures etc):

```javascript
const { WebsocketClient } = require('bybit-api');

const API_KEY = 'xxx';
const PRIVATE_KEY = 'yyy';

const wsConfig = {
  key: API_KEY,
  secret: PRIVATE_KEY,

  /*
    The following parameters are optional:
  */

  // defaults to false == testnet. Set to true for livenet.
  // livenet: true

  // NOTE: to listen to multiple markets (spot vs inverse vs linear vs linearfutures) at once, make one WebsocketClient instance per market

  // defaults to inverse:
  // market: 'inverse'
  // market: 'linear'
  // market: 'spot'

  // how long to wait (in ms) before deciding the connection should be terminated & reconnected
  // pongTimeout: 1000,

  // how often to check (in ms) that WS connection is still alive
  // pingInterval: 10000,

  // how long to wait before attempting to reconnect (in ms) after connection is closed
  // reconnectTimeout: 500,

  // config options sent to RestClient (used for time sync). See RestClient docs.
  // restOptions: { },

  // config for axios used for HTTP requests. E.g for proxy support
  // requestOptions: { }

  // override which URL to use for websocket connections
  // wsUrl: 'wss://stream.bytick.com/realtime'
};

const ws = new WebsocketClient(wsConfig);

// subscribe to multiple topics at once
ws.subscribe(['position', 'execution', 'trade']);

// and/or subscribe to individual topics on demand
ws.subscribe('kline.BTCUSD.1m');

// Listen to events coming from websockets. This is the primary data source
ws.on('update', data => {
  console.log('update', data);
});

// Optional: Listen to websocket connection open event (automatic after subscribing to one or more topics)
ws.on('open', ({ wsKey, event }) => {
  console.log('connection open for websocket with ID: ' + wsKey);
});

// Optional: Listen to responses to websocket queries (e.g. the response after subscribing to a topic)
ws.on('response', response => {
  console.log('response', response);
});

// Optional: Listen to connection close event. Unexpected connection closes are automatically reconnected.
ws.on('close', () => {
  console.log('connection closed');
});

// Optional: Listen to raw error events.
// Note: responses to invalid topics are currently only sent in the "response" event.
ws.on('error', err => {
  console.error('ERR', err);
});
```

See [websocket-client.ts](./src/websocket-client.ts) for further information.

---

## Customise Logging
Pass a custom logger which supports the log methods `silly`, `debug`, `notice`, `info`, `warning` and `error`, or override methods from the default logger as desired.

```javascript
const { WebsocketClient, DefaultLogger } = require('bybit-api');

// Disable all logging on the silly level
DefaultLogger.silly = () => {};

const ws = new WebsocketClient(
  { key: 'xxx', secret: 'yyy' },
  DefaultLogger
);
```

## Browser Usage
Build a bundle using webpack:
- `npm install`
- `npm build`
- `npm pack`

The bundle can be found in `dist/`. Altough usage should be largely consistent, smaller differences will exist. Documentation is still TODO.

However, note that browser usage will lead to CORS errors due Bybit. See [issue #79](#79) for more information & alternative suggestions.

---

## Contributions & Thanks
### Donations
#### tiagosiebler
If you found this project interesting or useful, create accounts with my referral links:
- [Bybit](https://www.bybit.com/en-US/register?affiliate_id=9410&language=en-US&group_id=0&group_type=1)
- [Binance](https://www.binance.com/en/register?ref=20983262)

Or buy me a coffee using any of these:
- BTC: `1C6GWZL1XW3jrjpPTS863XtZiXL1aTK7Jk`
- ETH (ERC20): `0xd773d8e6a50758e1ada699bb6c4f98bb4abf82da`

#### pixtron
An early generation of this library was started by @pixtron. If this library helps you to trade better on bybit, feel free to donate a coffee to @pixtron:
- BTC `1Fh1158pXXudfM6ZrPJJMR7Y5SgZUz4EdF`
- ETH `0x21aEdeC53ab7593b77C9558942f0c9E78131e8d7`
- LTC `LNdHSVtG6UWsriMYLJR3qLdfVNKwJ6GSLF`

### Contributions & Pull Requests
Contributions are encouraged, I will review any incoming pull requests. See the issues tab for todo items.
