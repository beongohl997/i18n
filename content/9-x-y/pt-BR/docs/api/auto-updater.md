# autoUpdater

> Habilita aplicações a se atualizarem automaticamente.

Processo: [Main](../glossary.md#main-process)

**See also: [A detailed guide about how to implement updates in your application](../tutorial/updates.md).**

`autoUpdater` is an [EventEmitter][event-emitter].

## Avisos de plataforma

Currently, only macOS and Windows are supported. There is no built-in support for auto-updater on Linux, so it is recommended to use the distribution's package manager to update your app.

Além disso, existem algumas diferenças sutis em cada plataforma:

### macOS

No macOS, o módulo `autoUpdater` é construído em cima do [Squirrel.Mac][squirrel-mac], ou seja, você não precisa de nenhuma configuração especial para que funcione. Para os requisitos do lado do servidor, você pode ler o [Suporte do servidor][server-support]. Observe que o [App Transport Security](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) (ATS) aplica-se a todas as solicitações feitas como parte do processo de atualização. Aplicações que precisam desativar o ATS podem adicionar a chave `NSAllowsArbitraryLoads` no plist de sua aplicação.

**Note:** Your application must be signed for automatic updates on macOS. This is a requirement of `Squirrel.Mac`.

### Windows

On Windows, you have to install your app into a user's machine before you can use the `autoUpdater`, so it is recommended that you use the [electron-winstaller][installer-lib], [electron-forge][electron-forge-lib] or the [grunt-electron-installer][installer] package to generate a Windows installer.

When using [electron-winstaller][installer-lib] or [electron-forge][electron-forge-lib] make sure you do not try to update your app [the first time it runs](https://github.com/electron/windows-installer#handling-squirrel-events) (Also see [this issue for more info](https://github.com/electron/electron/issues/7155)). Também é recomendado utilizar o [electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup) para obter atalhos da área de trabalho para sua aplicação.

The installer generated with Squirrel will create a shortcut icon with an [Application User Model ID][app-user-model-id] in the format of `com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE`, examples are `com.squirrel.slack.Slack` and `com.squirrel.code.Code`. Você deve usar o mesmo ID para sua aplicação com a API `app.setAppUserModelId`, caso contrário, o Windows não será capaz de fixar sua aplicação corretamente na barra de tarefas.

Ao contrário do Squirrel.Mac, o Windows pode hospedar atualizações em S3 ou qualquer outro arquivo estático de hospedagem. You can read the documents of [Squirrel.Windows][squirrel-windows] to get more details about how Squirrel.Windows works.

## Eventos

O objeto `autoUpdater` emite os seguintes eventos:

### Evento: 'error'

Retorna:

* `error` Error

Emitido quando há um erro durante a atualização.

### Evento: 'checking-for-update'

Emitido ao verificar se uma atualização começou.

### Evento: 'update-available'

Emitted when there is an available update. The update is downloaded automatically.

### Evento: 'update-not-available'

Emitido quando não há atualização disponível.

### Evento: 'update-downloaded'

Retorna:

* `event` Event
* String `releaseNotes`
* String `releaseName`
* Data `releaseDate`
* String `updateURL`

Emitido quando uma atualização foi baixada.

No Windows apenas o `releaseName` está disponível.

**Note:** It is not strictly necessary to handle this event. A successfully downloaded update will still be applied the next time the application starts.

### Evento: 'before-quit-for-update'

Este evento é emitido depois de um usuário chamar `quitAndInstall()`.

Quando esta API é chamada, o evento `before-quit` não é emitido antes de todas as janelas serem fechadas. Como resultado, você deve ouvir este evento se você deseja executar ações antes das janelas serem fechadas enquanto um processo é encerrado, Além de ouvir `before-quit`.

## Métodos

O objeto `autoUpdater` possui os seguintes métodos:

### `autoUpdater.setFeedURL(options)`

* `options` Object
  * String `url`
  * `headers` Record<String, String> (optional) _macOS_ - HTTP request headers.
  * `serverType` String (optional) _macOS_ - Either `json` or `default`, see the [Squirrel.Mac][squirrel-mac] README for more information.

Define a `url` e inicializa a atualização automática.

### `autoUpdater.getFeedURL()`

Returns `String` - The current update feed URL.

### `autoUpdater.checkForUpdates()`

Asks the server whether there is an update. You must call `setFeedURL` before using this API.

### `autoUpdater.quitAndInstall()`

Restarts the app and installs the update after it has been downloaded. It should only be called after `update-downloaded` has been emitted.

Sob o capô chamando `autoUpdater.quitAndInstall()` fechará todos os aplicativos janelas primeiro, e automaticamente chamar `app.quit()` depois de todas as janelas foram Fechado.

**Note:** It is not strictly necessary to call this function to apply an update, as a successfully downloaded update will always be applied the next time the application starts.

[squirrel-mac]: https://github.com/Squirrel/Squirrel.Mac
[server-support]: https://github.com/Squirrel/Squirrel.Mac#server-support
[squirrel-windows]: https://github.com/Squirrel/Squirrel.Windows
[installer]: https://github.com/electron/grunt-electron-installer
[installer-lib]: https://github.com/electron/windows-installer
[electron-forge-lib]: https://github.com/electron-userland/electron-forge
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
