# 辅助功能

Making accessible applications is important and we're happy to provide functionality to [Devtron](https://electronjs.org/devtron) and [Spectron](https://electronjs.org/spectron) that gives developers the opportunity to make their apps better for everyone.

---

Electron 应用中有关辅助功能的开发和网站是相似的，因为两者最终使用的都是HTML. 然而, 对于Electron应用, 你不能使用在线的辅助功能审查者, 因为你的应用没有一个URL可以提供给审查者.

These features bring those auditing tools to your Electron app. You can choose to add audits to your tests with Spectron or use them within DevTools with Devtron. 详见各工具的文档.

## Spectron

In the testing framework Spectron, you can now audit each window and `<webview>` tag in your application. 例如：

```javascript
app.client.auditAccessibility().then(function (audit) {
  if (audit.failed) {
    console.error(audit.message)
  }
})
```

你可以从这里[Spectron文档](https://github.com/electron/spectron#accessibility-testing)阅读到更多有关于这个功能的信息。

## Devtron

In Devtron, there is an accessibility tab which will allow you to audit a page in your app, sort and filter the results.

![devtron 截图](https://cloud.githubusercontent.com/assets/1305617/17156618/9f9bcd72-533f-11e6-880d-389115f40a2a.png)

这两种工具都使用了Google 为 Chrome 所构建的 [ 辅助功能开发工具 ](https://github.com/GoogleChrome/accessibility-developer-tools) 库。 您可以在 [ repository's wiki ](https://github.com/GoogleChrome/accessibility-developer-tools/wiki/Audit-Rules) 上了解到更加详细的辅助功能审核规则。

如果你知道其他适用于Electron的辅助功能开发工具, 请通过pull request添加到本文档中.

## Manually enabling accessibility features

Electron applications will automatically enable accessibility features in the presence of assistive technology (e.g. [JAWS](https://www.freedomscientific.com/products/software/jaws/) on Windows or [VoiceOver](https://help.apple.com/voiceover/mac/10.15/) on macOS). 有关详细信息, 请参阅 Chrome 的 [ 辅助功能文档 ](https://www.chromium.org/developers/design-documents/accessibility#TOC-How-Chrome-detects-the-presence-of-Assistive-Technology)。

You can also manually toggle these features either within your Electron application or by setting flags in third-party native software.

### Using Electron's API

By using the [`app.setAccessibilitySupportEnabled(enabled)`](../api/app.md#appsetaccessibilitysupportenabledenabled-macos-windows) API, you can manually expose Chrome's accessibility tree to users in the application preferences. Note that the user's system assistive utilities have priority over this setting and will override it.

### Within third-party software

#### macOS

On macOS, third-party assistive technology can toggle accessibility features inside Electron applications by setting the `AXManualAccessibility` attribute programmatically:

```objc
CFStringRef kAXManualAccessibility = CFSTR("AXManualAccessibility");

+ (void)enableAccessibility:(BOOL)enable inElectronApplication:(NSRunningApplication *)app
{
    AXUIElementRef appRef = AXUIElementCreateApplication(app.processIdentifier);
    if (appRef == nil)
        return;

    CFBooleanRef value = enable ? kCFBooleanTrue : kCFBooleanFalse;
    AXUIElementSetAttributeValue(appRef, kAXManualAccessibility, value);
    CFRelease(appRef);
}
```
