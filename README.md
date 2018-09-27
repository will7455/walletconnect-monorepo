# WalletConnect

[![Build Status](https://travis-ci.org/WalletConnect/walletconnect-monorepo.svg?branch=master)](https://travis-ci.org/WalletConnect/walletconnect-monorepo)

#### Monorepo for WalletConnect Javascript Libraries

1.  js-walletconnect-core ............ WalletConnect Javascript Core library
2.  walletconnect .................... WalletConnect Browser SDK
3.  rn-walletconnect-wallet .......... WalletConnect React-Native SDK
4.  walletconnect-web3-subprovider ... WalletConnect Web3 Subprovider
5.  walletconnect-qrcode-modal ............. WalletConnect QR Code Modal

For more documentation go to: https://docs.walletconnect.org

### Getting Started

1.  [For Dapps (Browser SDK)](#for-dapps-browser-sdk)
2.  [For Wallets (React-Native SDK)](#for-wallets-react-native-sdk)
3.  [For Web3 Subprovider (web3.js)](#for-web3-subprovider-web3.js)
4.  [For QR Code Modal (Browsers only)](#for-qr-code-modal-browsers-only)

### For Dapps (Browser SDK)

1.  Setup

```bash
yarn add walletconnect

# OR

npm install --save walletconnect
```

2.  Implementation

```js
import WalletConnect from 'walletconnect'

/**
 *  Create a webConnector
 */
const webConnector = new WalletConnect(
  {
    bridgeUrl: 'https://bridge.walletconnect.org',  // Required
    dappName: 'INSERT_DAPP_NAME',                   // Required
  }
)

/**
 *  Create a new session
 */
const session = await webConnector.initSession()

if (session.new) {
  const { uri } = session; // Display QR code with URI string

  const sessionStatus = await webConnector.listenSessionStatus() // Listen to session status

  const { accounts } = sessionStatus // Get wallet accounts
} else {
  const { accounts } = session // Get wallet accounts
}

/**
 *  Draft transaction
 */
const tx = {from: '0xab12...1cd', to: '0x0', nonce: 1, gas: 100000, value: 0, data: '0x0'}

/**
 *  Send transaction
 */
try {
  // Submitted Transaction Hash
  const result = await webConnector.sendTransaction(tx)
} catch (error) {
  // Rejected Transaction
  console.error(error)
}

/**
 *  Draft message
 */
const msg = 'My email is john@doe.com - 1537836206101'

/**
 *  Sign message
 */
try {
  // Signed message
  const result = await webConnector.signMessage(msg)
} catch (error) {
  // Rejected signing
  console.error(error)
}

/**
 *  Draft Typed Data
 */
const msgParams = [
  {
    type: 'string',
    name: 'Message',
    value: 'My email is john@doe.com'
  },
  {
    type: 'uint32',
    name: 'A number',
    value: '1537836206101'
  }
]

/**
 *  Sign Typed Data
 */
try {
  // Signed typed data
  const result = await webConnector.signTypedData(msgParams)
} catch (error) {
  // Rejected signing
  console.error(error)
}
```

### For Wallets (React-Native SDK)

1.  Setup

```bash
/**
 *  Install NPM Package
 */

yarn add rn-walletconnect-wallet

# OR

npm install --save rn-walletconnect-wallet

/**
 *  Nodify 'crypto' package for cryptography
 */

# install "crypto" shims and run package-specific hacks
rn-nodeify --install "crypto" --hack
```

2.  Implementation

```js
import RNWalletConnect from 'rn-walletconnect-wallet'

/**
 *  Create WalletConnector (using the URI from scanning the QR code)
 */
const walletConnector = new RNWalletConnect({ uri: uri })

/**
 *  Send session data
 */
await walletConnector.sendSessionStatus({
  fcmToken: '12354...3adc',
  pushEndpoint: 'https://push.walletconnect.org/notification/new',  
  data: {
    accounts: [
      '0x4292...931B3',
      '0xa4a7...784E8',
      ...
    ]
  }
})

/**
 *  Handle push notification events & get call data
 */
FCM.on(FCMEvent.Notification, event => {
  const { sessionId, callId } = event;

  const callData = await walletConnector.getCallRequest(callId);

  // example callData
  {
    method: 'eth_sendTransaction',
    data: {
      from: '0xab12...1cd',
      to: '0x0',
      nonce: 1,
      gas: 100000,
      value: 0,
      data: '0x0'
    }
  }
});

/**
 *  Send call status
 */
await walletConnector.sendCallStatus(callId, {
  success: true,
  result: '0xabcd...873'
})

/**
 *  Get all calls from bridge
 */
const allCalls = await walletConnector.getAllCallRequests();

/**
 *  allCalls is a map from callId --> callData
 */
const callData = allCalls[callId];
```

### For Web3 Subprovider (web3.js)

1.  Setup

```bash
/**
 *  Install NPM Package
 */

yarn add web3 web3-provider-engine walletconnect-web3-subprovider

# OR

npm install --save web3 web3-provider-engine walletconnect-web3-subprovider
```

2.  Implementation

```js
import Web3 from 'web3'
import ProviderEngine from 'web3-provider-engine'
import RpcSubprovider from 'web3-provider-engine/subproviders/rpc'
import WalletConnectSubprovider from 'walletconnect-web3-subprovider'

const engine = new ProviderEngine()

engine.addProvider(new WalletConnectSubprovider({
  bridgeUrl: 'https://bridge.walletconnect.org',  // Required
  dappName: 'INSERT_DAPP_NAME',                   // Required
})
engine.addProvider(new RpcSubprovider({ rpcUrl:'http://localhost:8545' }))
engine.start()

const web3 = new Web3(engine)
```

### For QR Code Modal (Browsers only)

1.  Setup

```bash
/**
 *  Install NPM Package
 */

yarn add walletconnect-qrcode-modal

# OR

npm install --save walletconnect-qrcode-modal
```

2.  Implementation

```js
import WalletConnectQRCode from 'walletconnect-qrcode-modal'

/**
 *  Get URI from WalletConnect object
 */
const uri = webConnector.uri

/**
 *  Open QR Code Modal
 */
WalletConnectQRCode.openQRCode(uri)

/**
 *  Close QR Code Modal
 */
WalletConnectQRCode.closeQRCode()
```

### Development workflow

```bash
$ git clone https://github.com/WalletConnect/walletconnect-monorepo.git && cd $_

$ npm install

$ npm run bootstrap

$ npm run check-packages
```

### Publish packages

```bash
$ npm run publish
```

### License

LGPL-3.0
