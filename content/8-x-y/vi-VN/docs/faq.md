# Danh sách các câu hỏi hay gặp của Electron

## Tại sao tôi lại gặp sự cố trong khi cài đặt Electron?

Khi chạy `npm install electron`, một số người dùng đôi khi gặp phải lỗi cài đặt.

Trong hầu hết các trường hợp, các lỗi này là kết quả của các vấn đề về mạng và không phải là vấn đề với gói npm của `electron`. Các lỗi như `ELIFECYCLE`, `EAI_AGAIN`, `ECONNRESET`, và `ETIMEDOUT` tất cả là biểu hiện của các vấn đề về mạng. Giải pháp tốt nhất là thử đổi mạng, hoặc chờ một lát sau đó thử cài lại sau.

Bạn cũng có thể tải Electron trực tiếp từ [electron/electron/releases](https://github.com/electron/electron/releases) nếu quá trình cài đặt `npm` bị lỗi.

## Khi nào Chrome được cập nhật phiên bản mới nhất vào Electron?

Phiên bản của Chrome trong Electron thường được cài đặt vào trong khoảng một hoặc hai tuần sau khi phiên bản ổn định mới nhất đó được phát hành. Ước tính này không được bảo đảm và phụ thuộc vào số lượng công việc liên quan đến nâng cấp.

Only the stable channel of Chrome is used. If an important fix is in beta or dev channel, we will back-port it.

Để biết thêm thông tin, vui lòng xem [thông tin bảo mật](tutorial/security.md).

## Khi nào thì Electron sử dụng phiên bản mới nhất của Node.js?

Khi một phiên bản mới nhất của Node.js được phát hành, chúng tôi thường chờ khoảng một tháng rồi mới cập nhật nó vô Electron. Nhờ vậy chúng tôi có thể tránh bị ảnh hưởng bởi các lỗi trong phiên bản Node.js mới, điều này xảy ra thường xuyên.

Các tính năng mới của Node.js thường được cung cấp bởi V8, kể từ khi Electron cũng sử dụng V8 từ trình duyệt Chrome, các tính năng mới của JavaScript trong các phiên bản mới nhất của Node.js thường đã sẳn sàng trong Electron.

## Làm thế nào để chia sẻ dữ liệu giữa các trang web?

Các đơn giản nhất để chia sẻ dữ liệu giữa các trang web (trong quá trình renderer) là sử dụng HTML5 API, đã có sẵn trong trình duyệt. Good candidates are [Storage API][storage], [`localStorage`][local-storage], [`sessionStorage`][session-storage], and [IndexedDB][indexed-db].

Hoặc bạn có thể sử dụng hệ thống IPC của Electron, để lưu trữ các đối tượng trong main process như là một biến toàn cầu, và sau đó truy cập chúng từ các renderer thông qua các property `điều khiển (remote)` của module `electron`:

```javascript
// Trong tiến trình main.
global.sharedObject = {
  someProperty: 'default value'
}
```

```javascript
// In page 1.
require('electron').remote.getGlobal('sharedObject').someProperty = 'new value'
```

```javascript
// In page 2.
console.log(require('electron').remote.getGlobal('sharedObject').someProperty)
```

## Cửa sổ của ứng dụng hoặc icon dưới taskbar (tray) của tôi đột nhiên biến mất chỉ sau một vài phút xuất hiện.

Điều này xảy ra khi các biến được sử dụng để lưu trữ các cửa sổ/tray được bộ dọn rác dọn đi.

Nếu bạn gặp vấn đề này, bài viết sau đây có thể hữu ích:

* [Memory Management][memory-management]
* [Variable Scope][variable-scope]

Nếu bạn chỉ muốn một sửa lỗi này nhanh chóng, bạn có thể tạo ra các biến toàn cầu bằng cách thay đổi code của bạn từ:

```javascript
const { app, Tray } = require('electron')
app.on('ready', () => {
  const tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```

đến điều này:

```javascript
const { app, Tray } = require('electron')
let tray = null
app.on('ready', () => {
  tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```

## Tại sao tôi không thể sử dụng jQuery/RequireJS/Meteor/AngularJS trong Electron.

Do Node.js được tích hợp trong Electron, do đó có một số symbol bổ sung được chèn vào DOM như `module`, `exports`, `require`. Điều này gây ra vấn đề cho một số thư viện khi họ muốn chèn các ký hiệu cùng với một tên.

Để giải quyết vấn đề này, bạn có thể tắt tích hợp Node trong Electron:

```javascript
// Trong tiến trình main.
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  webPreferences: {
    nodeIntegration: false
  }
})
win.show()
```

Tuy nhiên, nếu bạn muốn giữ lại khả năng sử dụng Node.js và các Electron API, bạn phải đổi tên những symbol trong các trang trước khi cài các thư viện khác vào ứng dụng:

```html
<head>
<script>
window.nodeRequire = require;
delete window.require;
delete window.exports;
delete window.module;
</script>
<script type="text/javascript" src="jquery.js"></script>
</head>
```

## `require('electron').xxx` bị undefined.

Khi sử dụng các mô đun được xây dựng sẵn trong Electron, bạn có thể gặp lỗi như sau:

```sh
> require('electron').webFrame.setZoomFactor(1.0)
Uncaught TypeError: Cannot read property 'setZoomLevel' of undefined
```

This is because you have the [npm `electron` module][electron-module] installed either locally or globally, which overrides Electron's built-in module.

Để chắc chắn, cho dù bạn đang sử dụng chính xác module được xây dựng sẳn đó, hãy xem thử đường dẫn của module `electron` đó nằm, ở đâu:

```javascript
console.log(require.resolve('electron'))
```

và sau đó kiểm tra nếu nó có kiểu giống dưới đây:

```sh
"/path/to/Electron.app/Contents/Resources/atom.asar/renderer/api/lib/exports/electron.js"
```

Nếu nó là một cái gì đó như `node_modules/electron/index.js`, thì bạn phải loại bỏ các module npm `electron`, hoặc đổi tên nó.

```sh
npm uninstall electron
npm uninstall -g electron
```

Tuy nhiên nếu bạn đang sử dụng các module được xây dựng sẵn nhưng vẫn nhận được lỗi này, rất có thể bạn đang sử dụng module của process khác. Ví dụ `electron.app` chỉ có thể sử dụng trong main process, giống như `electron.webFrame` chỉ có thể sử dụng trong các renderer process.

## Các ký tự rất mờ, tại sao và tôi phải làm gì ?

If [sub-pixel anti-aliasing](http://alienryderflex.com/sub_pixel/) is deactivated, then fonts on LCD screens can look blurry. Ví dụ:

![subpixel rendering example][]

Sub-pixel anti-aliasing cần một nền không trong suốt của lớp có chứa các font glyphs. ([Xem chi tiết ](https://github.com/electron/electron/issues/6344#issuecomment-420371918)).

To achieve this goal, set the background in the constructor for [BrowserWindow][browser-window]:

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  backgroundColor: '#fff'
})
```

The effect is visible only on (some?) LCD screens. Even if you don't see a difference, some of your users may. It is best to always set the background this way, unless you have reasons not to do so.

Lưu ý rằng chỉ thiết lập nền trong CSS sẽ không có hiệu ứng mong muốn.

[memory-management]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management
[variable-scope]: https://msdn.microsoft.com/library/bzt2dkta(v=vs.94).aspx
[electron-module]: https://www.npmjs.com/package/electron
[storage]: https://developer.mozilla.org/en-US/docs/Web/API/Storage
[local-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
[session-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage
[indexed-db]: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
[browser-window]: api/browser-window.md
[subpixel rendering example]: images/subpixel-rendering-screenshot.gif
