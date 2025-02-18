# Dich

Trang này định nghĩa một số thuật ngữ thường được sử dụng trong Electron.

### ASAR

ASAR là viết tắt của Atom Shell Archive Format. Một tập tin [asar][asar] đơn giản như một tập tin `tar`, nó là một định dạng nối các file lại với nhau thành một file duy nhất. Electron có thể đọc bất kể tập tin nào trong file có định dạng này mà không cần giải nén toàn bộ tập tin.

The ASAR format was created primarily to improve performance on Windows... TODO

### CRT

Thư viện C Run-time (CRT) là một bộ phận của thư viện C++ tiêu chuẩn mà kết hợp chặt chẽ với thư viện ISO C99 tiêu chuẩn. The Visual C++ libraries that implement the CRT support native code development, and both mixed native and managed code, and pure managed code for .NET development.

### DMG

Apple Disk Image là một định dạng đóng gói được sử dụng bởi macOS. Tập tin DMG thường được sử dụng để phân phối ứng dụng "installers". [electron-builder][] hỗ trợ `dmg` như một build target.

### IME

Trình chỉnh sửa phương thức nhập liệu. Một chương trình cho phép người dùng nhập các chữ cái và kí tự mà không thể tìm thấy trên bàn phím của họ. Lấy ví dụ, nó cho phép người dùng bàn phím La-tinh nhập các chữ cái trong tiếng Trung, tiếng Nhật, tiếng Hàn và tiếng Ấn.

### IDL

Interface description language. Write function signatures and data types in a format that can be used to generate interfaces in Java, C++, JavaScript, etc.

### IPC

IPC stands for Inter-Process Communication. Electron uses IPC to send serialized JSON messages between the [main][] and [renderer][] processes.

### libchromiumcontent

A shared library that includes the [Chromium Content module][] and all its dependencies (e.g., Blink, [V8][], etc.). Cũng được gọi là "libcc".

- [github.com/electron/libchromiumcontent](https://github.com/electron/libchromiumcontent)

### main process

The main process, commonly a file named `main.js`, is the entry point to every Electron app. It controls the life of the app, from open to close. Nó cũng kiểm soát các phần tử bản địa như là Menu, Thanh Menu, Dock,Tray, vv.. The main process is responsible for creating each new renderer process in the app. The full Node API is built in.

Every app's main process file is specified in the `main` property in `package.json`. This is how `electron .` knows what file to execute at startup.

In Chromium, this process is referred to as the "browser process". It is renamed in Electron to avoid confusion with renderer processes.

Xem thêm: [process](#process), [renderer process](#renderer-process)

### MAS

Acronym for Apple's Mac App Store. For details on submitting your app to the MAS, see the [Mac App Store Submission Guide][].

### Mojo

An IPC system for communicating intra- or inter-process, and that's important because Chrome is keen on being able to split its work into separate processes or not, depending on memory pressures etc.

Tham khảo https://chromium.googlesource.com/chromium/src/+/master/mojo/README.md

### các module native

Native modules (also called [addons][] in Node.js) are modules written in C or C++ that can be loaded into Node.js or Electron using the require() function, and used as if they were an ordinary Node.js module. Chúng phần lớn được sử dụng để cung cấp khớp nối giữa JavaScript chạy trên Node.js và các thư viện C/C++.

Native Node modules are supported by Electron, but since Electron is very likely to use a different V8 version from the Node binary installed in your system, you have to manually specify the location of Electron’s headers when building native modules.

Xem thêm [Cách sử dụng các Module của Node][].

### NSIS

Nullsoft Scriptable Install System is a script-driven Installer authoring tool for Microsoft Windows. Nó được phát hành dưới sự kết hợp của nhiều loại giấy phép phần mềm miễn phí, và được sử dụng rộng rãi thay thế cho sản phẩm thương mại độc quyền như InstallShield. [electron-builder][] hỗ trợ NSIS như một build target.

### OSR

OSR (Off-screen rendering) can be used for loading heavy page in background and then displaying it after (it will be much faster). It allows you to render page without showing it on screen.

### process

A process is an instance of a computer program that is being executed. Electron apps that make use of the [main][] and one or many [renderer][] process are actually running several programs simultaneously.

In Node.js and Electron, each running process has a `process` object. This object is a global that provides information about, and control over, the current process. As a global, it is always available to applications without using require().

Xem thêm: [process](#main-process), [renderer process](#renderer-process)

### renderer process

The renderer process is a browser window in your app. Unlike the main process, there can be multiple of these and each is run in a separate process. They can also be hidden.

In normal browsers, web pages usually run in a sandboxed environment and are not allowed access to native resources. Electron users, however, have the power to use Node.js APIs in web pages allowing lower level operating system interactions.

Xem thêm: [process](#process), [main process](#main-process)

### Squirrel

Squirrel is an open-source framework that enables Electron apps to update automatically as new versions are released. See the [autoUpdater][] API for info about getting started with Squirrel.

### không gian người dùng

This term originated in the Unix community, where "userland" or "userspace" referred to programs that run outside of the operating system kernel. More recently, the term has been popularized in the Node and npm community to distinguish between the features available in "Node core" versus packages published to the npm registry by the much larger "user" community.

Like Node, Electron is focused on having a small set of APIs that provide all the necessary primitives for developing multi-platform desktop applications. This design philosophy allows Electron to remain a flexible tool without being overly prescriptive about how it should be used. Userland enables users to create and share tools that provide additional functionality on top of what is available in "core".

### V8

V8 is Google's open source JavaScript engine. It is written in C++ and is used in Google Chrome. V8 can run standalone, or can be embedded into any C++ application.

Electron builds V8 as part of Chromium and then points Node to that V8 when building it.

V8's version numbers always correspond to those of Google Chrome. Chrome 59 includes V8 5.9, Chrome 58 includes V8 5.8, etc.

- [developers.google.com/v8](https://developers.google.com/v8)
- [nodejs.org/api/v8.html](https://nodejs.org/api/v8.html)
- [docs/development/v8-development.md](development/v8-development.md)

### webview

`webview` tags are used to embed 'guest' content (such as external web pages) in your Electron app. They are similar to `iframe`s, but differ in that each webview runs in a separate process. Nó không có các quyền như những trang web của bạn và mọi tương tác giữa ứng dụng của bạn với nội dung được tích hợp đều là bất đồng bộ. Điều này giúp ứng dụng của bạn an toàn đối với những nội dung được tích hợp.

[addons]: https://nodejs.org/api/addons.html
[asar]: https://github.com/electron/asar
[autoUpdater]: api/auto-updater.md
[Chromium Content module]: https://www.chromium.org/developers/content-module
[electron-builder]: https://github.com/electron-userland/electron-builder
[Mac App Store Submission Guide]: tutorial/mac-app-store-submission-guide.md
[main]: #main-process
[renderer]: #renderer-process
[Cách sử dụng các Module của Node]: tutorial/using-native-node-modules.md
[V8]: #v8
