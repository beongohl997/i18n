# powerMonitor

> 監視電源狀態變更。

處理序: [主處理序](../glossary.md#main-process)


This module cannot be used until the `ready` event of the `app` module is emitted.

For example:

```javascript
const { app, powerMonitor } = require('electron')

app.whenReady().then(() => {
  powerMonitor.on('suspend', () => {
    console.log('The system is going to sleep')
  })
})
```

## 事件

` powerMonitor ` 模組發出以下事件:

### Event: 'suspend' _macOS_ _Windows_

Emitted when the system is suspending.

### Event: 'resume' _macOS_ _Windows_

在系統復原時發出。

### Event: 'on-ac' _macOS_ _Windows_

當系統變為用交流電作電源時發出。

### Event: 'on-battery' _macOS_  _Windows_

當系統變為用電池作電源時發出。

### Event: 'shutdown' _Linux_ _macOS_

Emitted when the system is about to reboot or shut down. If the event handler invokes `e.preventDefault()`, Electron will attempt to delay system shutdown in order for the app to exit cleanly. If `e.preventDefault()` is called, the app should exit as soon as possible by calling something like `app.quit()`.

### Event: 'lock-screen' _macOS_ _Windows_

Emitted when the system is about to lock the screen.

### Event: 'unlock-screen' _macOS_ _Windows_

Emitted as soon as the systems screen is unlocked.

## 方法

The `powerMonitor` module has the following methods:

### `powerMonitor.getSystemIdleState(idleThreshold)`

* `idleThreshold` Integer

Returns `String` - The system's current state. Can be `active`, `idle`, `locked` or `unknown`.

Calculate the system idle state. `idleThreshold` is the amount of time (in seconds) before considered idle.  `locked` is available on supported systems only.

### `powerMonitor.getSystemIdleTime()`

Returns `Integer` - Idle time in seconds

Calculate system idle time in seconds.
