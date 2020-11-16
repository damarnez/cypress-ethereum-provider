# CYPRESS-ETHEREUM-PROVIDER

### Goals
* Follows [EIP 1193](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1193.md) Spec
* Support all transport types (websocket, http, ipc & injected)
* Attempt connection to an array of RPC endpoints until successful connection
* Reconnect when connection is lost
* Emit helpful status updates so apps can handle changes gracefully
* Can overwrite the address returned by the call eth_address

### Install
```
npm install cypress-ethereum-provider --save
```

### Use

```js
const provider = require('cypress-ethereum-provider')
const web3 = new Web3(provider('wss://rinkeby.infura.io/ws/v3/${INFURA_ID}))
```
* When passing in multiple RPC targets order them by priority
* When cypress-ethereum-provider fails to connect to a target it will automatically attempt to connect to the next priority target
* For example `['injected', 'wss://rinkeby.infura.io/ws/v3/${INFURA_ID}']` will first try to discover injected providers and if unsuccessful connect to the Infura endpoint
```js
const provider = require('cypress-ethereum-provider')
const web3 = new Web3(provider(['injected', 'wss://rinkeby.infura.io/ws/v3/${INFURA_ID}']))
```
* In Node and Electron you'll have access to IPC endpoints created by Geth or Parity that cannot be accessed by the Browser. You can connect to these by using the `'direct'` preset, or by passing custom IPC paths
```js
const provider = require('cypress-ethereum-provider')
const web3 = new Web3(provider('direct'))
```

* Yoy can force the address for special cases like ganache fork mainet with 
```js
  const provider = require('cypress-ethereum-provider')
  const web3 = new Web3(provider('http://localhost:8545'))
  web3.setFakeAccounts(["0x00000"])

```

### Presets
* **`injected`** - Discover providers injected by environment, usually by the browser or a browser extension
  * Browser
    * `['injected']`
* **`frame`** - Connect to [Frame](https://github.com/floating/frame) running on the user's device
  * Browser/Node/Electron
    * `['ws://127.0.0.1:1248', 'http://127.0.0.1:1248']`
* **`direct`** - Connect to local Ethereum nodes running on the user's device
  * Browser
    * `['ws://127.0.0.1:8546', 'http://127.0.0.1:8545']`
  * Node/Electron
    * `[/* Default IPC paths for platform */, 'ws://127.0.0.1:8546', 'http://127.0.0.1:8545']`
* **`infura`** - Connect to Mainnet Infura
  * Browser/Node/Electron
    * `['wss://mainnet.infura.io/ws/v3/${INFURA_ID}', 'https://mainnet.infura.io/v3/${INFURA_ID}']`
* **`infuraRinkeby`** - Connect to Rinkeby Infura
  * Browser/Node/Electron
    * `['wss://rinkeby.infura.io/ws/v3/${INFURA_ID}', 'https://rinkeby.infura.io/v3/${INFURA_ID}']`
* **`infuraRopsten`** - Connect to Ropsten Infura
  * Browser/Node/Electron
    * `['wss://ropsten.infura.io/ws/v3/${INFURA_ID}', 'https://ropsten.infura.io/v3/${INFURA_ID}']`
* **`infuraKovan`** - Connect to Kovan Infura
  * Browser/Node/Electron
    * `['wss://kovan.infura.io/ws/v3/${INFURA_ID}', 'https://kovan.infura.io/v3/${INFURA_ID}']`

If you do not pass any targets, cypress-ethereum-provider will use default targets `['injected', 'frame']` in the Browser and `['frame', 'direct']` in Node and Electron.

### Options

When creating the provider you can also pass an options object

* `infuraId` - Your projects Infura ID
* `origin` - Used when connecting from outside of a browser env to declare you're origin (this currently doesn't work with HTTP connections)

`provider('infura', { infuraId: '123abc' })` or `provider({ origin: 'DappName', infuraId: '123abc' })`

The origin setting will only be applied when a dapp is connecting to from outside of a browser env. 


### Notes

Based from : https://github.com/floating/eth-provider
