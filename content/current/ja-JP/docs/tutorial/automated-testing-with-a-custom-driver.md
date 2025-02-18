# カスタムドライバを使った自動テスト

Electron アプリの自動テストを作成するには、アプリケーションを「ドライブ」する方法が必要になります。 [Spectron](https://electronjs.org/spectron) は、[WebDriver](http://webdriver.io/) を介してユーザの操作をエミュレートできる、よく使用される解決方法です。 ただし、Node に組み込まれている IPC 越し標準入出力を使用して独自のカスタムドライバを書くことも可能です。 カスタムドライバの利点は、Spectron よりもオーバーヘッドが少なくて済むことです。また、カスタムメソッドをあなたのテストスイートに公開できます。

カスタムドライバを作成するには、Node.js の [child_process](https://nodejs.org/api/child_process.html) API を使用します。 テストスイートは、以下のように Electron プロセスを spawn してから、簡単なメッセージングプロトコルを確立します。

```js
const childProcess = require('child_process')
const electronPath = require('electron')

// spawn the process
const env = { /* ... */ }
const stdio = ['inherit', 'inherit', 'inherit', 'ipc']
const appProcess = childProcess.spawn(electronPath, ['./app'], { stdio, env })

// listen for IPC messages from the app
appProcess.on('message', (msg) => {
  // ...
})

// アプリへ IPC メッセージを送る
appProcess.send({ my: 'message' })
```

Electron アプリケーション内からは、Node.js の [process](https://nodejs.org/api/process.html) API を使用して、メッセージをリッスンして返信を送信できます。

```js
// テストスイートからの IPC メッセージをリッスンする
process.on('message', (msg) => {
  // ...
})

// テストスイートへ IPC メッセージを送る
process.send({ my: 'message' })
```

これで、`appProcess` オブジェクトを使用してテストスイートから Electron アプリケーションに通信できます。

利便性のために、より高度な機能を提供するドライバオブジェクトで `appProcess` をラップすることをお勧めします。 以下は、これをどのようにするかの例です。

```js
class TestDriver {
  constructor ({ path, args, env }) {
    this.rpcCalls = []

    // start child process
    env.APP_TEST_DRIVER = 1 // let the app know it should listen for messages
    this.process = childProcess.spawn(path, args, { stdio: ['inherit', 'inherit', 'inherit', 'ipc'], env })

    // handle rpc responses
    this.process.on('message', (message) => {
      // pop the handler
      const rpcCall = this.rpcCalls[message.msgId]
      if (!rpcCall) return
      this.rpcCalls[message.msgId] = null
      // reject/resolve
      if (message.reject) rpcCall.reject(message.reject)
      else rpcCall.resolve(message.resolve)
    })

    // wait for ready
    this.isReady = this.rpc('isReady').catch((err) => {
      console.error('Application failed to start', err)
      this.stop()
      process.exit(1)
    })
  }

  // simple RPC call
  // to use: driver.rpc('method', 1, 2, 3).then(...)
  async rpc (cmd, ...args) {
    // send rpc request
    const msgId = this.rpcCalls.length
    this.process.send({ msgId, cmd, args })
    return new Promise((resolve, reject) => this.rpcCalls.push({ resolve, reject }))
  }

  stop () {
    this.process.kill()
  }
}
```

このアプリでは、RPC 呼び出しのために簡単なハンドラを作成する必要があります。

```js
if (process.env.APP_TEST_DRIVER) {
  process.on('message', onMessage)
}

async function onMessage ({ msgId, cmd, args }) {
  let method = METHODS[cmd]
  if (!method) method = () => new Error('Invalid method: ' + cmd)
  try {
    const resolve = await method(...args)
    process.send({ msgId, resolve })
  } catch (err) {
    const reject = {
      message: err.message,
      stack: err.stack,
      name: err.name
    }
    process.send({ msgId, reject })
  }
}

const METHODS = {
  isReady () {
    // do any setup needed
    return true
  }
  // define your RPC-able methods here
}
```

すると、テストスイート内では、以下のようにテストドライバを使用できます。

```js
const test = require('ava')
const electronPath = require('electron')

const app = new TestDriver({
  path: electronPath,
  args: ['./app'],
  env: {
    NODE_ENV: 'test'
  }
})
test.before(async t => {
  await app.isReady
})
test.after.always('cleanup', async t => {
  await app.stop()
})
```
