# 开发工具扩展程序

Electron支持[Chrome  DevTools 扩展程序][devtools-extension]，可增强开发工具调试流行web框架的能力

## 如何加载一个 DevTools 扩展程序

本文简要描述了手动加载一个扩展程序的过程 你也可以尝试一下[electron-devtools-installer](https://github.com/GPMDP/electron-devtools-installer)，这个第三方工具可以直接从Chrome的WebStore下载扩展程序

为了在Electron中加载一个扩展程序，你需要在Chrome浏览器中下载它，找到它在系统目录中位置，然后调用`BrowserWindow.addDevToolsExtension(extension)`API 加载它

下面以[React Developer Tools][react-devtools]为例：

1. 在 Chrome 中安装React Developer Tools 。
1. 打开`chrome://extensions`，找到扩展程序的ID，形如`fmkadmapgofadopljbjfkapdkoienihi`的hash字符串。
1. 找到Chrome 扩展程序 的存放目录：
   * 在Ｗindows 下为 `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Extensions`;
   * 在 Linux下为：
     * `~/.config/google-chrome/Default/Extensions/`
     * `~/.config/google-chrome-beta/Default/Extensions/`
     * `~/.config/google-chrome-canary/Default/Extensions/`
     * `~/.config/chromium/Default/Extensions/`
   * 在 macOS下为`~/Library/Application Support/Google/Chrome/Default/Extensions`。
1. Pass the location of the extension to `BrowserWindow.addDevToolsExtension` API, for the React Developer Tools, it is something like:
   ```javascript
   const path = require('path')
   const os = require('os')

   BrowserWindow.addDevToolsExtension(
      path.join(os.homedir(), '/Library/Application Support/Google/Chrome/Default/Extensions/fmkadmapgofadopljbjfkapdkoienihi/4.3.0_0')
   )
   ```

**注意：**只有在app模块的ready事件触发之后，才可以调用`BrowserWindow.addDevToolsExtension` API

The extension will be remembered so you only need to call this API once per extension. If you try to add an extension that has already been loaded, this method will not return and instead log a warning to the console.

### 如何移除一个 DevTools 扩展程序

您可以传递扩展程序的名称到 `BrowserWindow.removeDevToolsExtension` API 移除它。 The name of the extension is returned by `BrowserWindow.addDevToolsExtension` and you can get the names of all installed DevTools Extensions using the `BrowserWindow.getDevToolsExtensions` API.

## 支持的 DevTools 扩展程序

Electron 只支持有限的`chrome.*` API，所以，一些扩展程序如果使用了不支持的`chrome.*` API，它可能会无法正常工作。 以下  DevTools 扩展程序已经通过测试，可以在Electron中正常工作：

* [Ember Inspector](https://chrome.google.com/webstore/detail/ember-inspector/bmdblncegkenkacieihfhpjfppoconhi)
* [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
* [Backbone Debugger](https://chrome.google.com/webstore/detail/backbone-debugger/bhljhndlimiafopmmhjlgfpnnchjjbhd)
* [jQuery Debugger](https://chrome.google.com/webstore/detail/jquery-debugger/dbhhnnnpaeobfddmlalhnehgclcmjimi)
* [AngularJS Batarang](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk)
* [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
* [Cerebral Debugger](https://cerebraljs.com/docs/introduction/debugger.html)
* [Redux DevTools Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)
* [MobX Developer Tools](https://chrome.google.com/webstore/detail/mobx-developer-tools/pfgnfdagidkfgccljigdamigbcnndkod)

### 如果 DevTools 扩展不工作, 我该怎么办？

首先请确保扩展仍在维护中, 有些扩展甚至不支持 Chrome 浏览器的最新版本, 对此我们也无能为力。

然后在Electron的问题列表中提交一个 bug, 并描述扩展程序的哪个部分没有按预期的方式工作。

[devtools-extension]: https://developer.chrome.com/extensions/devtools
[react-devtools]: https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi
