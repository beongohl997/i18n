# BrowserWindow

> Создавайте окна браузера и управляйте ими.

Процесс: [Основной](../glossary.md#main-process)

```javascript
// В основном процессе.
const { BrowserWindow } = require('electron')

// Или используйте 'remote' в графическом процессе.
// const { BrowserWindow } = require('electron').remote

const win = new BrowserWindow({ width: 800, height: 600 })

// Загрузка удаленного URL
win.loadURL('https://github.com')

// Или загрузка локального HTML файла
win.loadURL(`file://${__dirname}/app/index.html`)
```

## Окно без рамки

Для создания окна без хрома или прозрачного окна произвольной формы, можно использовать API [окна без рамки](frameless-window.md).

## Изящный показ окон

Когда страница загружается в окно напрямую, пользователь может увидеть постепенную загрузку страницы, что не является хорошей практикой для нативных приложений. Чтобы заставить окно показываться без визуальных скачков, есть два решения для разных ситуаций.

## Использование `ready-to-show` события

При загрузке страницы, после отрисовки страницы будет происходить событие `ready-to-show`, которое будет происходить первый раз, если окно до этого еще не было показано. Окно, показанное после этого события, не будет иметь визуальной ступенчатой подгрузки:

```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ show: false })
win.once('ready-to-show', () => {
  win.show()
})
```

Обычно это событие происходит после события `did-finish-load`. Однако, страницы, включающие в себя удаленные ресурсы, могут продолжать подгружаться после происхождения события `did-finish-load`.

Пожалуйста, обратите внимание, что использование этого события означает, что рендерер будет считаться "видимым" и отрисуется, даже если `show` является false.  Это событие никогда не сработает, если вы используете `paintWhenInitiallyHidden: false`

## Настройка `backgroundColor`

Для больших приложений событие `ready-to-show` может вызываться слишком поздно, что может замедлить приложение. В этом случае рекомендуется показать окно немедленно, и использовать `backgroundColor`, задающий цвет фона Вашего приложения:

```javascript
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({ backgroundColor: '#2e2c29' })
win.loadURL('https://github.com')
```

Обратите внимание, что даже для приложений, использующих `ready-to-show` события, по-прежнему рекомендуется установить `backgroundColor`, чтобы сделать приложение более нативным.

## Родительские и дочерние окна

С помощью параметра `parent`, Вы можете создавать дочерние окна:

```javascript
const { BrowserWindow } = require('electron')

const top = new BrowserWindow()
const child = new BrowserWindow({ parent: top })
child.show()
top.show()
```

Окно `child` будет всегда показано поверх окна `top`.

## Модальные окна

Модальное окно - дочернее окно, которое делает недоступным родительское окно. Чтобы создать модальное окно, Вы должны установить два параметра `parent` и `modal`:

```javascript
const { BrowserWindow } = require('electron')

const child = new BrowserWindow({ parent: top, modal: true, show: false })
child.loadURL('https://github.com')
child.once('ready-to-show', () => {
  child.show()
})
```

## Видимость страниц

[API видимости страниц][page-visibility-api] работает следующим образом:

* На всех платформах состояние видимости отслеживает скрыто/уменьшено окно или нет.
* Кроме того, на macOS, состояние видимости также отслеживает состояние перекрытия окна. Если окно перекрыто (т.е. полностью покрыто) другим окном, состояние видимости будет `hidden`. На других платформах состояние видимости будет `hidden`, только когда окно уменьшено или явно скрыто при помощи `win.hide()`.
* Если `BrowserWindow` создано с `show: false`, первоначальное состояние видимости будет `visible`, несмотря на фактически скрытое окно.
* Если `backgroundThrottling` отключено, состояние видимости останется `visible`, даже если окно уменьшено, закрыто или скрыто.

Рекомендуется приостановить дорогостоящие операции, когда состояние видимости `hidden`, для того чтобы свести к минимуму потребление энергии.

## Платформа заметок

* На macOS модальные окна будут отображены в виде страниц, прикрепленных к родительскому окну.
* На macOS дочерние окна будут находиться относительно родительского окна, во время передвижения родительского окна, тем временем на Windows и Linux дочерние окна не будут двигаться.
* На Linux тип модального окна будет поменян на `dialog`.
* На Linux многие среды рабочего стола не поддерживают скрытие модального окна.

## Класс: BrowserWindow

> Создавайте окна браузера и управляйте ими.

Процесс: [Основной](../glossary.md#main-process)

`BrowserWindow` это[EventEmitter][event-emitter].

Так создается новый экземпляр `BrowserWindow` с нативными свойствами, установленными в `options`.

### `new BrowserWindow([options])`

* `options` Object (опционально)
  * `width` Integer (опционально) - ширина окна в пикселях. По умолчанию `800`.
  * `height` Integer (опционально) - высота окна в пикселях. По умолчанию `600`.
  * `x` Integer (опционально) - (**обязателен**, если используется y) отступ окна слева от экрана. Значение по умолчанию центрирует окно.
  * `y` Integer (опционально) - (**обязателен**, если используется x) отступ окна сверху от экрана. Значение по умолчанию центрирует окно.
  * `useContentSize` Boolean (опционально) - `width` и `height` могут использоваться как размеры веб-страницы, это значит, что актуальный размер окна будет включать размер фрейма и будет немного крупнее. По умолчанию - `false`.
  * `center` Boolean (опционально) - показывает окно в центре экрана.
  * `minWidth` Integer (опционально) - минимальная ширина окна. По умолчанию `0`.
  * `minHeight` Integer (опционально) - минимальная высота окна. По умолчанию `0`.
  * `maxWidth` Integer (опционально) - максимальная ширина окна. По умолчанию нет ограничения.
  * `maxHeight` Integer (опционально) - максимальная высота окна. По умолчанию нет ограничения.
  * `resizable` Boolean (опционально) - будет ли окно изменять размеры. По умолчанию - `true`.
  * `movable` Boolean (optional) - можно ли будет перемещать окно. Не реализовано в Linux. По умолчанию - `true`.
  * `minimizable` Boolean (optional) - Whether window is minimizable. Не реализовано в Linux. По умолчанию - `true`.
  * `maximizable` Boolean (optional) - Whether window is maximizable. Не реализовано в Linux. По умолчанию - `true`.
  * `closable` Boolean (optional) - Whether window is closable. Не реализовано в Linux. По умолчанию - `true`.
  * `focusable` Boolean (опционально) - может ли быть окно в фокусе. По умолчанию - `true`. На Windows настройка `focusable: false` также подразумевает настройку `skipTaskbar: true`. На Linux настройка `focusable: false` прекращает взаимодействие окна с оконным менеджером, на Windows же всегда остается поверх всех рабочих областей.
  * `alwaysOnTop` Boolean (опционально) - будет ли окно всегда оставаться поверх других окон. По умолчанию - `false`.
  * `fullscreen` Boolean (опционально) - будет ли окно показываться во весь экран. Когда явно установлено `false`, на macOS кнопка полноэкранного режима будет скрыта или отключена. По умолчанию - `false`.
  * `fullscreenable` Boolean (опционально) - может ли окно быть в полноэкранном режиме. На macOS также кнопка увеличить/зумировать должна переключить в полноэкранный режим или увеличить окно. По умолчанию - `true`.
  * `simpleFullscreen` Boolean (optional) - Use pre-Lion fullscreen on macOS. По умолчанию - `false`.
  * `skipTaskbar` Boolean (optional) - Whether to show the window in taskbar. Default is `false`.
  * `kiosk` Boolean (optional) - Whether the window is in kiosk mode. По умолчанию - `false`.
  * `title` String (опционально) - заголовок окна по умолчанию. По умолчанию `"Electron"`. Если HTML-тег `<title>` определен в HTML-файле, загруженном с помощью `loadURL()`, то это свойство будет игнорироваться.
  * `icon` ([NativeImage](native-image.md) | String) (опционально) - иконка окна. На Windows рекомендуется использовать иконки `ICO`, чтобы получить лучший визуальный эффект, Вы также можете оставить неопределенным, чтобы был использован значок исполняемого файла.
  * `show` Boolean (необязательно) - Будет ли показано окно, когда будет создано. По умолчанию - `true`.
  * `paintWhenInitiallyHidden` Boolean (опционально) - Должен ли рендерер быть активным, когда `show` равен `false` и он только что создан.  Для `document.visibilityState` для корректной работы при первой загрузке с `show: false` необходимо установить значение `false`.  Установка этого в `false` приведёт к тому, что события `ready-to-show` не будут запускаться.  По умолчанию - `true`.
  * `frame` Boolean (optional) - Specify `false` to create a [Frameless Window](frameless-window.md). По умолчанию - `true`.
  * `parent` BrowserWindow (optional) - Specify parent window. Default is `null`.
  * `modal` Boolean (optional) - Whether this is a modal window. This only works when the window is a child window. По умолчанию - `false`.
  * `acceptFirstMouse` Boolean (optional) - Whether the web view accepts a single mouse-down event that simultaneously activates the window. Default is `false`.
  * `disableAutoHideCursor` Boolean (optional) - Whether to hide cursor when typing. По умолчанию - `false`.
  * `autoHideMenuBar` Boolean (optional) - Auto hide the menu bar unless the `Alt` key is pressed. По умолчанию - `false`.
  * `enableLargerThanScreen` Boolean (опционально) - позволяет окну изменять размер больше, чем экран. Относится только к macOS, так как другие ОС по умолчанию разрешают окна больше экрана. По умолчанию - `false`.
  * `backgroundColor` String (опционально) - фоновый цвет окна в HEX-формате, например `#66CD00`, `#FFF` или `#80FFFFFF` (альфа в формате #AARRGGBB поддерживается, если `transparent` установлено `true`). По умолчанию `#FFF` (белый).
  * `hasShadow` Boolean (optional) - Whether window should have a shadow. По умолчанию - `true`.
  * `opacity` Number (optional) - Set the initial opacity of the window, between 0.0 (fully transparent) and 1.0 (fully opaque). This is only implemented on Windows and macOS.
  * `darkTheme` Boolean (optional) - Forces using dark theme for the window, only works on some GTK+3 desktop environments. По умолчанию - `false`.
  * `transparent` Boolean (опционально) - делает окно [прозрачным](frameless-window.md#transparent-window). По умолчанию - `false`. В Windows не работает до тех пор, пока окно не будет без фрейма.
  * `type` String (optional) - The type of window, default is normal window. See more about this below.
  * `visualEffectState` String (optional) - Specify how the material appearance should reflect window activity state on macOS. Must be used with the `vibrancy` property. Возможные значения:
    * `followWindow` - The backdrop should automatically appear active when the window is active, and inactive when it is not. This is the default.
    * `active` - The backdrop should always appear active.
    * `inactive` - The backdrop should always appear inactive.
  * `titleBarStyle` String (optional) - The style of window title bar. Default is `default`. Возможные значения:
    * `default` - в результате стандартный, серый, непрозрачный Mac заголовок.
    * `hidden` - в результате скрытый заголовок и содержимое во все окно, но заголовок по-прежнему имеет стандартное окно контроля ("светофоры") сверху слева.
    * `hiddenInset` - В результате скрытый заголовок с альтернативным видом, где кнопки контролирования немного больше вставки от края окна.
    * `customButtonsOnHover` Boolean (опционально) - отобразить пользовательские кнопки закрыть и свернуть на macOS окнах без рамки. Эти кнопки будут отображены только при наведении на верхний левый угол окна. Эти кастомные кнопки предотвращают проблемы с методами мыши, случающиеся со стандартными кнопками панели инструментов. **Заметка:** Этот параметр в настоящее время экспериментален.
  * `trafficLightPosition` [Point](structures/point.md) (optional) - Set a custom position for the traffic light buttons. Can only be used with `titleBarStyle` set to `hidden`
  * `fullscreenWindowTitle` Boolean (optional) - Shows the title in the title bar in full screen mode on macOS for all `titleBarStyle` options. По умолчанию - `false`.
  * `thickFrame` Boolenan (необязательно) - Использовать стиль `WS_THICKFRAME` на окнах с отсутствием рамок на Windows, добавляющий стандартные рамки окна. Установив значение `false`, тень окна и анимация окна будут удалены. По умолчанию - `true`.
  * `vibrancy` String (опционально) - добавить тип эффекта вибрации к окну, только на macOS. Может быть `appearance-based`, `light`, `dark`, `titlebar`, `selection`, `menu`, `popover`, `sidebar`, `medium-light`, `ultra-dark`, `header`, `sheet`, `window`, `hud`, `fullscreen-ui`, `tooltip`, `content`, `under-window` или `under-page`.  Обратите внимание, что использование `frame: false` в комбинации с меняющимся значением, требует так же указывать `titleBarStyle`. Также обратите внимание, что `appearance-based`, `light`, `dark`, `medium-light` и `ultra-dark` устарела и будет удалена в следующей версии macOS.
  * `zoomToPageWidth` Boolean (optional) - Controls the behavior on macOS when option-clicking the green stoplight button on the toolbar or by clicking the Window > Zoom menu item. Если `true`, окно будет увеличиваться до предпочтительной ширины веб-страницы при увеличении, `false` приведет к увеличению масштаба до ширины экрана. Это также повлияет на поведение при вызове `maximize()` напрямую. По умолчанию - `false`.
  * `tabbingIdentifier` String (опционально) - название группы вкладок, позволяет открывать окно как нативную вкладку в macOS 10.12+. Окна с одинаковым идентификатором вкладки будут сгруппированы вместе. Это также добавляет новую нативную кнопку вкладки в панель вкладок вашего окна и позволяет Вашему `приложению` и окну получать событие `new-window-for-tab`.
  * `webPreferences` Object (optional) - Settings of web page's features.
    * `devTools` Boolean (опционально) - включает инструменты разработчика. Если значение `false`, нельзя будет использовать `BrowserWindow.webContents.openDevTools()`, чтобы открыть инструменты разработчика. По умолчанию - `true`.
    * `nodeIntegration` Boolean (optional) - Whether node integration is enabled. По умолчанию - `false`.
    * `nodeIntegrationInWorker` Boolean (опционально) - включает интеграцию NodeJS в веб-воркерах. По умолчанию - `false`. Больше об этом можно найти в [многопоточности](../tutorial/multithreading.md).
    * `nodeIntegrationInSubFrames` Boolean (опционально) - экспериментальная опция для включения поддержки NodeJS в подфреймах, таких как iframes и дочерних окнах. Все Ваши предварительные загрузки будут загружены для каждого iframe, Вы можете использовать `process.isMainFrame`, чтобы определить в главном фрейме Вы или нет.
    * `preload` String (опционально) - определяет скрипт, который будет загружен до того, как остальные скрипты запустятся на странице. Этот скрипт будет всегда иметь доступ к API NodeJS, вне зависимости включена или выключена интеграция NodeJS. Значение должно быть абсолютным путем к файлу скрипта. Когда интеграция NodeJS отключена, предварительно загруженный скрипт может повторно ввести глобальные символы NodeJS в глобальную область. Посмотреть пример [здесь](process.md#event-loaded).
    * `sandbox` Boolean (опционально) - если установлено true, то в окне будет запущена песочница, что делает ее совместимой с песочницей Chromium на уровне операционной системы, и отключает движок NodeJS. Это не тоже самое, что параметр `nodeIntegration`, доступные API для предзагруженных скриптов более ограничены. Узнать больше об этой опции можно [здесь](sandbox-option.md).
    * `enableRemoteModule` Boolean (optional) - Whether to enable the [`remote`](remote.md) module. По умолчанию - `false`.
    * `session` [Session](session.md#class-session) (опционально) - устанавливает сессию, которая используется страницей. Вместо передачи экземпляр Session напрямую, вместо этого Вы можете также выбрать использование опции `partition`, которая принимает строку раздела. Когда оба `session` и `partition` определены, `session` будет предпочтительней. По умолчанию используется сессия по умолчанию.
    * `partition` String (опционально) - устанавливает сессию, используемую на странице в соответствии со строкой раздела сессии. Если `partition` начинается с `persist:`, страница будет использовать постоянную сессию, которая доступна всем страницам в приложении с тем же `разделом`. Если нет префикса `persist:`, страница будет использовать сессию в памяти. При присваивании одинаковой `partition`, разные страницы могут иметь одинаковую сессию. По умолчанию используется сессия по умолчанию.
    * `affinity` String (опционально) - когда определено, веб-страницы с одинаковым `родством` будут работать в том же графическом процессе. Обратите внимание, что из-за повторного использования графического процесса, некоторые параметры `webPreferences` также будут доступны между веб-страницами, даже если Вы указали для них разные значения, включая, но не ограничиваясь, `preload`, `sandbox` и `nodeIntegration`. Поэтому рекомендуется использовать те же `webPreferences` для веб-страниц с одинаковым `родством`. _Deprecated_
    * `zoomFactor` Number (optional) - The default zoom factor of the page, `3.0` represents `300%`. Default is `1.0`.
    * `javascript` Boolean (optional) - Enables JavaScript support. По умолчанию - `true`.
    * `webSecurity` Boolean (опционально) - когда `false`, отключается политика same-origin (обычно используется при тестировании веб-сайтов людьми), и устанавливается `allowRunningInsecureContent` в `true`, если параметр не был установлен пользователем. По умолчанию - `true`.
    * `allowRunningInsecureContent` Boolean (optional) - Allow an https page to run JavaScript, CSS or plugins from http URLs. По умолчанию - `false`.
    * `images` Boolean (optional) - Enables image support. По умолчанию - `true`.
    * `textAreasAreResizable` Boolean (optional) - Make TextArea elements resizable. Default is `true`.
    * `webgl` Boolean (optional) - Enables WebGL support. По умолчанию - `true`.
    * `plugins` Boolean (optional) - Whether plugins should be enabled. По умолчанию - `false`.
    * `experimentalFeatures` Boolean (optional) - Enables Chromium's experimental features. По умолчанию - `false`.
    * `scrollBounce` Boolean (optional) - Enables scroll bounce (rubber banding) effect on macOS. По умолчанию - `false`.
    * `enableBlinkFeatures` String (optional) - A list of feature strings separated by `,`, like `CSSVariables,KeyboardEventKey` to enable. Полный список поддерживаемых возможностей можно найти в файле [RuntimeEnabledFeatures.json5][runtime-enabled-features].
    * `disableBlinkFeatures` String (опционально) - Список функциональных возможностей для выключения, разделяются `','`, например `CSSVariables,KeyboardEventKey`. Полный список поддерживаемых возможностей можно найти в файле [RuntimeEnabledFeatures.json5][runtime-enabled-features].
    * `defaultFontFamily` Object (optional) - Sets the default font for the font-family.
      * `standard` String (опционально) - По умолчанию `Times New Roman`.
      * `serif` String (опционально) - По умолчанию `Times New Roman`.
      * `sansSerif` String (опционально) - По умолчанию `Arial`.
      * `monospace` String (опционально) - По умолчанию `Courier New`.
      * `cursive` String (optional) - Defaults to `Script`.
      * `fantasy` String (optional) - Defaults to `Impact`.
    * `defaultFontSize` Integer (optional) - Defaults to `16`.
    * `defaultMonospaceFontSize` Integer (optional) - Defaults to `13`.
    * `minimumFontSize` Integer (optional) - Defaults to `0`.
    * `defaultEncoding` String (optional) - Defaults to `ISO-8859-1`.
    * `backgroundThrottling` Boolean (optional) - Whether to throttle animations and timers when the page becomes background. This also affects the [Page Visibility API](#page-visibility). Defaults to `true`.
    * `offscreen` Boolean (optional) - Whether to enable offscreen rendering for the browser window. Defaults to `false`. See the [offscreen rendering tutorial](../tutorial/offscreen-rendering.md) for more details.
    * `contextIsolation` Boolean (optional) - Whether to run Electron APIs and the specified `preload` script in a separate JavaScript context. Defaults to `false`. The context that the `preload` script runs in will still have full access to the `document` and `window` globals but it will use its own set of JavaScript builtins (`Array`, `Object`, `JSON`, etc.) and will be isolated from any changes made to the global environment by the loaded page. The Electron API will only be available in the `preload` script and not the loaded page. This option should be used when loading potentially untrusted remote content to ensure the loaded content cannot tamper with the `preload` script and any Electron APIs being used. This option uses the same technique used by [Chrome Content Scripts][chrome-content-scripts]. Вы можете получить доступ к этому контексту в инструментах разработчика, при выборе пункта 'Изолированный Контекст Elctron' в списке на верхней части консольной вкладки.
    * `worldSafeExecuteJavaScript` Boolean (optional) - If true, values returned from `webFrame.executeJavaScript` will be sanitized to ensure JS values can't unsafely cross between worlds when using `contextIsolation`.  The default is `false`. In Electron 12, the default will be changed to `true`. _Deprecated_
    * `nativeWindowOpen` Boolean (опционально) - использовать нативную функцию `window.open()`. Defaults to `false`. Дочерние окна всегда будут отключены для интеграции узлов, если `nodeIntegrationInSubFrames` будет true. **Примечание:** Эта опция в настоящее время экспериментальная.
    * `webviewTag` Boolean (опционально) - включает [`<webview>`-тег](webview-tag.md). Defaults to `false`. **Примечание:** Cкрипт `предварительной загрузки`, настроенный для `<webview>`, будет иметь интеграцию NodeJS, когда будет запущен, так что Вы должны убедиться, что удаленный/непроверенный контент не может создавать тег `<webview>` с возможно вредоносным скриптом `предварительной загрузки`. Вы можете использовать событие `will-attach-webview` на [webContents](web-contents.md), чтобы снять скрипт `предварительной загрузки` и проверить или изменить начальные настройки `<webview>`.
    * `additionalArguments` String[] (optional) - A list of strings that will be appended to `process.argv` in the renderer process of this app.  Useful for passing small bits of data down to renderer process preload scripts.
    * `safeDialogs` Boolean (optional) - Whether to enable browser style consecutive dialog protection. По умолчанию - `false`.
    * `safeDialogsMessage` String (optional) - The message to display when consecutive dialog protection is triggered. If not defined the default message would be used, note that currently the default message is in English and not localized.
    * `disableDialogs` Boolean (optional) - Whether to disable dialogs completely. Overrides `safeDialogs`. По умолчанию - `false`.
    * `navigateOnDragDrop` Boolean (optional) - Whether dragging and dropping a file or link onto the page causes a navigation. По умолчанию - `false`.
    * `autoplayPolicy` String (опционально) - политика автовоспроизведения для применения к содержимому в окне, может быть `no-user-gesture-required`, `user-gesture-required` или `document-user-activation-required`. По умолчанию `no-user-gesture-required`.
    * `disableHtmlFullscreenWindowResize` Boolean (optional) - Whether to prevent the window from resizing when entering HTML Fullscreen. Default is `false`.
    * `accessibleTitle` String (optional) - An alternative title string provided only to accessibility tools such as screen readers. This string is not directly visible to users.
    * `spellcheck` Boolean (optional) - Whether to enable the builtin spellchecker. По умолчанию - `true`.
    * `enableWebSQL` Boolean (optional) - Whether to enable the [WebSQL api](https://www.w3.org/TR/webdatabase/). По умолчанию - `true`.
    * `v8CacheOptions` String (optional) - Enforces the v8 code caching policy used by blink. Accepted values are
      * `none` - Disables code caching
      * `code` - Heuristic based code caching
      * `bypassHeatCheck` - Bypass code caching heuristics but with lazy compilation
      * `bypassHeatCheckAndEagerCompile` - Same as above except compilation is eager. Default policy is `code`.

Когда установлен минимальный или максимальный размер окна, при помощи `minWidth`/`maxWidth`/`minHeight`/`maxHeight`, это ограничивает только пользователей. Это не позволит Вам установить размер, который не будет следовать ограничениям размера, в `setBounds`/`setSize` или в конструкторе `BrowserWindow`.

The possible values and behaviors of the `type` option are platform dependent. Возможные значения:

* На Linux возможны типы `desktop`, `dock`, `toolbar`, `splash`, `notification`.
* On macOS, possible types are `desktop`, `textured`.
  * Тип `textured` добавляет вид металлического градиента (`NSTexturedBackgroundWindowMask`).
  * Тип `desktop` размещает окно на уровень фонового окна рабочего стола (`kCGDesktopWindowLevel - 1`). Обратите внимание, что окно рабочего стола не будет получить события фокуса, клавиатуры или мыши, но Вы можете использовать `globalShortcut`, чтобы получать ввод.
* На Windows возможен тип `toolbar`.

### События экземпляра

Объекты созданные с помощью `new BrowserWindow` имеют следующие события:

**Примечание:** Некоторые методы доступны только в определенных операционных системах и помечены как таковые.

#### Событие: 'page-title-updated'

Возвращает:

* `event` Event
* `title` String
* `explicitSet` Boolean

Вызывается, когда документ меняет свой заголовок, вызов `event.preventDefault()` предотвратит изменение заголовка родного окна. `explicitSet` является false, когда заголовок синтезирован из url файла.

#### Событие: 'close'

Возвращает:

* `event` Event

Происходит при закрытии окна. Оно происходит перед событиями `beforeunload` и `unload` в DOM. Вызов `event.preventDefault()` предотвратит закрытие.

Скорее всего, Вы захотите использовать обработчик `beforeunload`, чтобы решить, когда окно должно быть закрыто, который также будет вызываться, когда окно перезагружается. В Electron возврат любого значения, отличного от `undefined`, предотвратит закрытие. Например:

```javascript
window.onbeforeunload = (e) => {
  console.log('Я не хочу быть закрыт')

  // В отличие от браузеров, пользователю будет показано окно с сообщением.
  // Возврат любого значения незаметно отменит закрытие.
  // Рекомендуется использовать dialog API, чтобы дать пользователям
  // возможность подтвердить закрытие приложения.
  e.returnValue = false // идентично `return false`, но в использовании не рекомендуется
}
```
_**Примечание**: Существует тонкая разница между поведением `window.onbeforeunload = handler` и `window.addEventListener('beforeunload', handler)`. Рекомендуется всегда устанавливать `event.returnValue` явно, вместо того, чтобы просто возвращать значение, поскольку первое работает более последовательно в Electron._

#### Событие: 'closed'

Возникает, когда окно будет закрыто. After you have received this event you should remove the reference to the window and avoid using it any more.

#### Событие: 'session-end' _Windows_

Происходит, когда сеанс окна заканчивается из-за выключения, перезагрузки компьютера или отключения сеанса.

#### Событие: 'unresponsive'

Происходит, когда страница "не отвечает".

#### Событие: 'responsive'

Происходит, когда страница, которая "не отвечала", снова реагирует.

#### Событие: 'blur'

Происходит, когда окно теряет фокус.

#### Событие: 'focus'

Происходит, когда на окне фокусируются.

#### Событие: 'show'

Происходит, когда отображается окно.

#### Событие: 'hide'

Происходит, когда окно спрятано.

#### Событие: 'ready-to-show'

Происходит, когда веб-страница была отрисована (пока не отображена) и окно может быть отображено без визуального мерцания.

Пожалуйста, обратите внимание, что использование этого события означает, что рендерер будет считаться "видимым" и отрисуется, даже если `show` является false.  Это событие никогда не сработает, если вы используете `paintWhenInitiallyHidden: false`

#### Событие: 'maximize'

Происходит, когда окно увеличивается до предела.

#### Событие: 'unmaximize'

Происходит, когда окно выходит из увеличенного состояния.

#### Событие: 'minimize'

Происходит, когда окно было свернуто.

#### Событие: 'restore'

Происходит, когда окно восстанавливается из свернутого состояния.

#### Событие: 'will-resize' _macOS_ _Windows_

Возвращает:

* `event` Event
* `newBounds` [Rectangle](structures/rectangle.md) - Размер окна, на который будет изменено.

Emitted before the window is resized. Calling `event.preventDefault()` will prevent the window from being resized.

Note that this is only emitted when the window is being resized manually. Resizing the window with `setBounds`/`setSize` will not emit this event.

#### Событие: 'resize'

Происходит после того, как изменился размер окна.

#### Event: 'will-move' _macOS_ _Windows_

Возвращает:

* `event` Event
* `newBounds` [Rectangle](structures/rectangle.md) - Расположение, куда окно будет перемещено.

Emitted before the window is moved. On Windows, calling `event.preventDefault()` will prevent the window from being moved.

Note that this is only emitted when the window is being resized manually. Resizing the window with `setBounds`/`setSize` will not emit this event.

#### Событие: 'move'

Вызывается, когда окно перемещено на новое место.

__Примечание__: На macOS это событие является псевдонимом `moved`.

#### Событие: 'moved' _macOS_

Вызывается единожды, когда окно перемещается в новое положение.

#### Событие: 'enter-full-screen'

Вызывается, когда окно переходит в полноэкранный режим.

#### Событие: 'leave-full-screen'

Вызывается, когда окно выходит из полноэкранного режима.

#### Событие: 'enter-html-full-screen'

Происходит, когда окно входит в полноэкранный режим с помощью HTML API.

#### Событие: 'leave-html-full-screen'

Происходит, когда окно выходит из полноэкранного режима с помощью HTML API.

#### Событие: 'always-on-top-changed'

Возвращает:

* `event` Event
* `isAlwaysOnTop` Boolean

Происходит, когда окно переключает режим отображения поверх всех окон.

#### Событие: 'app-command' _Windows_ _Linux_

Возвращает:

* `event` Event
* `command` String

Вызывается, когда вызван [App Command](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx). Обычно это касается клавиатурных медиа-клавиш или команд браузера, а также кнопки "Назад", встроенной в некоторые мыши на Windows.

Команды в нижнем регистре, подчеркивание заменено на дефисы, а префикс `APPCOMMAND_` обрезан. например `APPCOMMAND_BROWSER_BACKWARD` происходит как `browser-backward`.

```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow()
win.on('app-command', (e, cmd) => {
  // Navigate the window back when the user hits their mouse back button
  if (cmd === 'browser-backward' && win.webContents.canGoBack()) {
    win.webContents.goBack()
  }
})
```

Следующие команды приложения явно поддерживаются на Linux:

* `browser-backward`
* `browser-forward`

#### Событие: 'scroll-touch-begin' _macOS_

Происходит, когда начинается прокрутка колеса.

#### Событие: 'scroll-touch-end' _macOS_

Происходит, когда заканчивается прокрутка колеса.

#### Событие: 'scroll-touch-edge' _macOS_

Происходит, когда прокрутка колеса достигает края элемента.

#### Событие: 'swipe' _macOS_

Возвращает:

* `event` Event
* `direction` String

Emitted on 3-finger swipe. Possible directions are `up`, `right`, `down`, `left`.

Метод, лежащий в основе этого события, создан для обработки событий смахивания на устаревших трекпадах в стиле macOS, где содержимое на экране нельзя переместить смахнув. Большинство трекпадов macOS по умолчанию не настроены под этот вид смахивания, поэтому для того чтобы правильно работал 'Переход между страницами смахиванием' перейдите в `Системные настройки > Трекпад > Другие жесты ` и включите "Смахивание двумя или тремя пальцами".

#### Событие: 'rotate-gesture' _macOS_

Возвращает:

* `event` Event
* `rotation` Float

Генерируется жестом вращения трекпада. Непрерывно генерируется до тех пор, пока жест поворота не будет завершен. Значение `rotation` при каждом выбросе - угол поворота в градусах, отсчитанный от последнего выброса. Последнее событие на жесте вращения всегда будет иметь значение `0`. Значения вращения против часовой стрелки положительные, по часовой стрелке отрицательные.

#### Событие: 'sheet-begin' _macOS_

Происходит, когда окно открывает лист.

#### Событие: 'sheet-end' _macOS_

Происходит, когда окно закрыло лист.

#### Событие: 'new-window-for-tab' _macOS_

Происходит, когда нажимается нативная кнопка новой вкладки.

### Статические методы

Класс `BrowserWindow` имеет следующие статические методы:

#### `BrowserWindow.getAllWindows()`

Возвращает `BrowserWindow[]` - массив всех открытых окон браузера.

#### `BrowserWindow.getFocusedWindow()`

Возвращает `BrowserWindow | null` - окно, которое сфокусировано в этом приложении, иначе возвращает `null`.

#### `BrowserWindow.fromWebContents(webContents)`

* `webContents` [WebContents](web-contents.md)

Возвращает `BrowserWindow | null` - окно, которое владеет объектом `webContents`, или имеет `null` если не содержит контент.

#### `BrowserWindow.fromBrowserView(browserView)`

* `browserView` [BrowserView](browser-view.md)

Возвращает `BrowserWindow` - окно, которое владеет объектом `webContents`. If the given view is not attached to any window, returns `null`.

#### `BrowserWindow.fromId(id)`

* `id` Integer

Returns `BrowserWindow | null` - The window with the given `id`.

#### `BrowserWindow.addExtension(path)` _Deprecated_

* `path` String

Добавляет расширение Chrome, расположенное в `path`, и возвращает имя расширения.

The method will also not return if the extension's manifest is missing or incomplete.

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.loadExtension(path)`](session.md#sesloadextensionpath).

#### `BrowserWindow.removeExtension(name)` _Deprecated_

* `name` String

Удаляет расширение Chrome с указанным именем.

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.removeExtension(extension_id)`](session.md#sesremoveextensionextensionid).

#### `BrowserWindow.getExtensions()` _Deprecated_

Возвращает `Record<String, ExtensionInfo>` - ключи это имена расширений, а каждое значение это объект, содержащий свойства `name` и `version`.

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.getAllExtensions()`](session.md#sesgetallextensions).

#### `BrowserWindow.addDevToolsExtension(path)` _Deprecated_

* `path` String

Adds DevTools extension located at `path`, and returns extension's name.

The extension will be remembered so you only need to call this API once, this API is not for programming use. If you try to add an extension that has already been loaded, this method will not return and instead log a warning to the console.

The method will also not return if the extension's manifest is missing or incomplete.

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.loadExtension(path)`](session.md#sesloadextensionpath).

#### `BrowserWindow.removeDevToolsExtension(name)` _Deprecated_

* `name` String

Удаляет расширение DevTools с указанным именем.

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.removeExtension(extension_id)`](session.md#sesremoveextensionextensionid).

#### `BrowserWindow.getDevToolsExtensions()` _Deprecated_

Возвращает `Record<string, ExtensionInfo>` - ключи это имена расширений, а каждое значение это объект, содержащий свойства `name` и `version`.

To check if a DevTools extension is installed you can run the following:

```javascript
const { BrowserWindow } = require('electron')

const installed = 'devtron' in BrowserWindow.getDevToolsExtensions()
console.log(installed)
```

**Примечание:** Этот метод не может быть вызван до тех пор, пока событие `ready` модуля `app` не произойдет.

**Note:** This method is deprecated. Instead, use [`ses.getAllExtensions()`](session.md#sesgetallextensions).

### Свойства экземпляра

Objects created with `new BrowserWindow` have the following properties:

```javascript
const { BrowserWindow } = require('electron')
// In this example `win` is our instance
const win = new BrowserWindow({ width: 800, height: 600 })
win.loadURL('https://github.com')
```

#### `win.webContents` _Только чтение_

A `WebContents` object this window owns. All web page related events and operations will be done via it.

See the [`webContents` documentation](web-contents.md) for its methods and events.

#### `win.id` _Только чтение_

Свойство `Integer` представляющее уникальный идентификатор окна. Each ID is unique among all `BrowserWindow` instances of the entire Electron application.

#### `win.autoHideMenuBar`

A `Boolean` property that determines whether the window menu bar should hide itself automatically. Once set, the menu bar will only show when users press the single `Alt` key.

Если панель меню уже видна, установка этого свойства в `true` не будет скрывать ее немедленно.

#### `win.simpleFullScreen`

A `Boolean` property that determines whether the window is in simple (pre-Lion) fullscreen mode.

#### `win.fullScreen`

A `Boolean` property that determines whether the window is in fullscreen mode.

#### `win.visibleOnAllWorkspaces`

A `Boolean` property that determines whether the window is visible on all workspaces.

**Note:** Always returns false on Windows.

#### `win.shadow`

A `Boolean` property that determines whether the window has a shadow.

#### `win.menuBarVisible` _Windows_ _Linux_

A `Boolean` property that determines whether the menu bar should be visible.

**Note:** If the menu bar is auto-hide, users can still bring up the menu bar by pressing the single `Alt` key.

#### `win.kiosk`

A `Boolean` property that determines whether the window is in kiosk mode.

#### `win.documentEdited` _macOS_

A `Boolean` property that specifies whether the window’s document has been edited.

The icon in title bar will become gray when set to `true`.

#### `win.representedFilename` _macOS_

A `String` property that determines the pathname of the file the window represents, and the icon of the file will show in window's title bar.

#### `win.title`

A `String` property that determines the title of the native window.

**Note:** The title of the web page can be different from the title of the native window.

#### `win.minimizable`

Свойство `Boolean` определяет, может ли окно быть вручную свернуто пользователем.

В Linux setter это невозможно, хотя getter возвращает `true`.

#### `win.maximizable`

Свойство `Boolean` определяет, может ли окно быть вручную развернуто пользователем.

В Linux setter это невозможно, хотя getter возвращает `true`.

#### `win.fullScreenable`

Свойство `Boolean` которое определяет, может ли кнопка увеличить/зумировать окно переключать полноэкранный режим или увеличивать до предела окно.

#### `win.resizable`

Свойство `Boolean` определяет, может ли окно быть вручную изменено пользователем.

#### `win.closable`

Свойство `Boolean` определяет, может ли окно быть вручную закрыто пользователем.

В Linux setter это невозможно, хотя getter возвращает `true`.

#### `win.movable`

Свойство `Boolean` определяет, может ли окно перемещаться пользователем.

В Linux setter это невозможно, хотя getter возвращает `true`.

#### `win.excludedFromShownWindowsMenu` _macOS_

A `Boolean` property that determines whether the window is excluded from the application’s Windows menu. `false` by default.

```js
const win = new BrowserWindow({ height: 600, width: 600 })

const template = [
  {
    role: 'windowmenu'
  }
]

win.excludedFromShownWindowsMenu = true

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

#### `win.accessibleTitle`

A `String` property that defines an alternative title provided only to accessibility tools such as screen readers. This string is not directly visible to users.

### Методы экземпляра

Объекты, созданные с помощью `new BrowserWindow`, имеют следующие методы экземпляра:

**Примечание:** Некоторые методы доступны только в определенных операционных системах и помечены как таковые.

#### `win.destroy()`

Force closing the window, the `unload` and `beforeunload` event won't be emitted for the web page, and `close` event will also not be emitted for this window, but it guarantees the `closed` event will be emitted.

#### `win.close()`

Try to close the window. This has the same effect as a user manually clicking the close button of the window. The web page may cancel the close though. See the [close event](#event-close).

#### `win.focus()`

Focuses on the window.

#### `win.blur()`

Убирает фокус с окна.

#### `win.isFocused()`

Returns `Boolean` - Whether the window is focused.

#### `win.isDestroyed()`

Возвращает `Boolean` - уничтожено окно или нет.

#### `win.show()`

Показывает и фокусирует окно.

#### `win.showInactive()`

Shows the window but doesn't focus on it.

#### `win.hide()`

Скрывает окно.

#### `win.isVisible()`

Возвращает `Boolean` - видно окно для пользователя или нет.

#### `win.isModal()`

Returns `Boolean` - Whether current window is a modal window.

#### `win.maximize()`

Maximizes the window. This will also show (but not focus) the window if it isn't being displayed already.

#### `win.unmaximize()`

Unmaximizes the window.

#### `win.isMaximized()`

Возвращает `Boolean` - увеличено окно до предела или нет.

#### `win.minimize()`

Minimizes the window. On some platforms the minimized window will be shown in the Dock.

#### `win.restore()`

Восстанавливает окно из свернутого состояния до его предыдущего состояния.

#### `win.isMinimized()`

Возвращает `Boolean` - свернуто окно или нет.

#### `win.setFullScreen(flag)`

* `flag` Boolean

Sets whether the window should be in fullscreen mode.

#### `win.isFullScreen()`

Возвращает `Boolean` - в полноэкранном режиме окно или нет.

#### `win.setSimpleFullScreen(flag)` _macOS_

* `flag` Boolean

Enters or leaves simple fullscreen mode.

Simple fullscreen mode emulates the native fullscreen behavior found in versions of macOS prior to Lion (10.7).

#### `win.isSimpleFullScreen()` _macOS_

Возвращает `Boolean` - в простом полноэкранном режиме окно или нет.

#### `win.isNormal()`

Returns `Boolean` - Whether the window is in normal state (not maximized, not minimized, not in fullscreen mode).

#### `win.setAspectRatio(aspectRatio[, extraSize])` _macOS_ _Linux_

* `aspectRatio` Float - The aspect ratio to maintain for some portion of the content view.
 * `extraSize` [Size](structures/size.md) (optional) _macOS_ - The extra size not to be included while maintaining the aspect ratio.

Это заставит окно поддерживать соотношение сторон. Дополнительный размер позволяет разработчику иметь пространство, указанное в пикселях, которое не входит в расчеты соотношения сторон. This API already takes into account the difference between a window's size and its content size.

Consider a normal window with an HD video player and associated controls. Perhaps there are 15 pixels of controls on the left edge, 25 pixels of controls on the right edge and 50 pixels of controls below the player. In order to maintain a 16:9 aspect ratio (standard aspect ratio for HD @1920x1080) within the player itself we would call this function with arguments of 16/9 and
{ width: 40, height: 50 }. The second argument doesn't care where the extra width and height are within the content view--only that they exist. Суммируйте любые области дополнительной ширины и высоты, которые у Вас есть, в общем представлении содержимого.

#### `win.setBackgroundColor(backgroundColor)`

* `backgroundColor` String - Window's background color as a hexadecimal value, like `#66CD00` or `#FFF` or `#80FFFFFF` (alpha is supported if `transparent` is `true`). По умолчанию `#FFF` (белый).

Sets the background color of the window. See [Setting `backgroundColor`](#setting-backgroundcolor).

#### `win.previewFile(path[, displayName])` _macOS_

* `path` String - абсолютный путь до файла, для предпросмотра в QuickLook. Это важно, так как QuickLook использует имя файла и расширение файла из пути, чтобы определить тип содержимого файла для открытия.
* `displayName` String (опционально) - имя файла, для отображения в модальном виде QuickLook. Это чисто визуально и не влияет на тип содержимого файла. По умолчанию `path`.

Использует [QuickLook][quick-look] для предпросмотра файла, по данному пути.

#### `win.closeFilePreview()` _macOS_

Закрывает текущую открытую панель [QuickLook][quick-look].

#### `win.setBounds(bounds[, animate])`

* `bounds` Partial<[Rectangle](structures/rectangle.md)>
* `animate` Boolean (опционально) _macOS_

Resizes and moves the window to the supplied bounds. Any properties that are not supplied will default to their current values.

```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow()

// Установить все свойства границы
win.setBounds({ x: 440, y: 225, width: 800, height: 600 })

// Установить одно свойство границы
win.setBounds({ width: 100 })

// { x: 440, y: 225, width: 100, height: 600 }
console.log(win.getBounds())
```

#### `win.getBounds()`

Возвращает [`Rectangle`](structures/rectangle.md) - `границы` окна как `объект`.

#### `win.getBackgroundColor()`

Returns `String` - Gets the background color of the window. See [Setting `backgroundColor`](#setting-backgroundcolor).

#### `win.setContentBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (опционально) _macOS_

Меняет размеры и перемещает клиентскую область окна (например, веб-страницу) на заданные границы.

#### `win.getContentBounds()`

Возвращает [`Rectangle`](structures/rectangle.md) - `границы` области клиентского окна как `объект`.

#### `win.getNormalBounds()`

Возвращает [`Rectangle`](structures/rectangle.md) - содержит границы окна в нормальном состоянии

**Примечание:** Независимо от текущего состояния окна: увеличено до предела, свернуто или в полноэкранном режиме, эта функция всегда возвратит позицию и размер окна в нормальном состоянии. В нормальном состоянии, getBounds и getNormalBounds возвращают тот же [`Rectangle`](structures/rectangle.md).

#### `win.setEnabled(enable)`

* `enable` Boolean

Включает или выключает окно.

#### `win.isEnabled()`

Returns `Boolean` - whether the window is enabled.

#### `win.setSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (опционально) _macOS_

Resizes the window to `width` and `height`. If `width` or `height` are below any set minimum size constraints the window will snap to its minimum size.

#### `win.getSize()`

Возвращает `Integer[]` - Содержит высоту и ширину окна.

#### `win.setContentSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (опционально) _macOS_

Меняет размер клиентской области окна (например, веб-страница) на `width` и `height`.

#### `win.getContentSize()`

Возвращает `Integer[]` - содержит ширину и высоту клиентской области окна.

#### `win.setMinimumSize(width, height)`

* `width` Integer
* `height` Integer

Устанавливает минимальный размер окна на `width` и `height`.

#### `win.getMinimumSize()`

Возвращает `Integer[]` - содержит минимальную ширину и высоту окна.

#### `win.setMaximumSize(width, height)`

* `width` Integer
* `height` Integer

Устанавливает максимальный размер окна на `width` и `height`.

#### `win.getMaximumSize()`

Возвращает `Integer[]` - содержит максимальную ширину и высоту окна.

#### `win.setResizable(resizable)`

* `resizable` Boolean

Sets whether the window can be manually resized by the user.

#### `win.isResizable()`

Returns `Boolean` - Whether the window can be manually resized by the user.

#### `win.setMovable(movable)` _macOS_ _Windows_

* `movable` Boolean

Sets whether the window can be moved by user. On Linux does nothing.

#### `win.isMovable()` _macOS_ _Windows_

Возвращает `Boolean` - может пользователь перемещать окно или нет.

На Linux всегда возвращает `true`.

#### `win.setMinimizable(minimizable)` _macOS_ _Windows_

* `minimizable` Boolean

Sets whether the window can be manually minimized by user. On Linux does nothing.

#### `win.isMinimizable()` _macOS_ _Windows_

Returns `Boolean` - Whether the window can be manually minimized by the user.

На Linux всегда возвращает `true`.

#### `win.setMaximizable(maximizable)` _macOS_ _Windows_

* `maximizable` Boolean

Sets whether the window can be manually maximized by user. On Linux does nothing.

#### `win.isMaximizable()` _macOS_ _Windows_

Возвращает `Boolean` - может пользователь вручную увеличивать до предела окно или нет.

На Linux всегда возвращает `true`.

#### `win.setFullScreenable(fullscreenable)`

* `fullscreenable` Boolean

Sets whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.isFullScreenable()`

Returns `Boolean` - Whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.setClosable(closable)` _macOS_ _Windows_

* `closable` Boolean

Sets whether the window can be manually closed by user. On Linux does nothing.

#### `win.isClosable()` _macOS_ _Windows_

Возвращает `Boolean` - может пользователь вручную закрывать окно или нет.

На Linux всегда возвращает `true`.

#### `win.setAlwaysOnTop(flag[, level][, relativeLevel])`

* `flag` Boolean
* `level` String (опционально) _macOS_ _Windows_ - Значения включают `normal`, `floating`, `torn-off-menu`, `modal-panel`, `main-menu`, `status`, `pop-up-menu`, `screen-saver` и ~~`dock`~~ (Устарело). По умолчанию `floating`, когда `flag` установлен true. `level` сбрасывается на `normal`, когда флаг устанавливается false. Обратите внимание, что от `floating` до `status` включительно, окно находится под Dock в macOS и под панелью задач в Windows. От `pop-up-menu` и выше отображается над Dock на macOS и выше панели задач на Windows. Смотрите [документацию macOS][window-levels] для подробностей.
* `relativeLevel` Integer (опционально) _macOS_ - количество слоев выше, чтобы установить окно относительно заданного `level`. По умолчанию - `0`. Обратите внимание, что Apple не рекомендует устанавливать уровни выше, чем 1 верхнего `screen-saver`.

Sets whether the window should show always on top of other windows. After setting this, the window is still a normal window, not a toolbox window which can not be focused on.

#### `win.isAlwaysOnTop()`

Возвращает `Boolean` - всегда ли окно поверх остальных окон.

#### `win.moveAbove(mediaSourceId)`

* `mediaSourceId` String - Window id in the format of DesktopCapturerSource's id. For example "window:1869:0".

Moves window above the source window in the sense of z-order. If the `mediaSourceId` is not of type window or if the window does not exist then this method throws an error.

#### `win.moveTop()`

Перемещает окно на верх(z-order) независимо от фокуса

#### `win.center()`

Перемещает окно в центр экрана.

#### `win.setPosition(x, y[, animate])`

* `x` Integer
* `y` Integer
* `animate` Boolean (опционально) _macOS_

Перемещает окно на `x` и `y`.

#### `win.getPosition()`

Возвращает `Integer[]` - содержит текущую позицию окна.

#### `win.setTitle(title)`

* `title` String

Изменяет название нативного окна на `title`.

#### `win.getTitle()`

Возвращает `String` - название нативного окна.

**Примечание:** Название веб-страницы может отличаться от названия нативного окна.

#### `win.setSheetOffset(offsetY[, offsetX])` _macOS_

* `offsetY` Float
* `offsetX` Float (опционально)

Changes the attachment point for sheets on macOS. By default, sheets are attached just below the window frame, but you may want to display them beneath a HTML-rendered toolbar. Например:

```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow()

const toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

#### `win.flashFrame(flag)`

* `flag` Boolean

Начинает или останавливает мерцание окна, чтобы привлечь внимание пользователя.

#### `win.setSkipTaskbar(skip)`

* `skip` Boolean

Не отображает окно в панели задач.

#### `win.setKiosk(flag)`

* `flag` Boolean

Enters or leaves kiosk mode.

#### `win.isKiosk()`

Returns `Boolean` - Whether the window is in kiosk mode.

#### `win.getMediaSourceId()`

Returns `String` - Window id in the format of DesktopCapturerSource's id. For example "window:1234:0".

More precisely the format is `window:id:other_id` where `id` is `HWND` on Windows, `CGWindowID` (`uint64_t`) on macOS and `Window` (`unsigned long`) on Linux. `other_id` is used to identify web contents (tabs) so within the same top level window.

#### `win.getNativeWindowHandle()`

Returns `Buffer` - The platform-specific handle of the window.

The native type of the handle is `HWND` on Windows, `NSView*` on macOS, and `Window` (`unsigned long`) on Linux.

#### `win.hookWindowMessage(message, callback)` _Windows_

* `message` Integer
* `callback` Function

Hooks a windows message. The `callback` is called when the message is received in the WndProc.

#### `win.isWindowMessageHooked(message)` _Windows_

* `message` Integer

Returns `Boolean` - `true` or `false` depending on whether the message is hooked.

#### `win.unhookWindowMessage(message)` _Windows_

* `message` Integer

Unhook the window message.

#### `win.unhookAllWindowMessages()` _Windows_

Unhooks all of the window messages.

#### `win.setRepresentedFilename(filename)` _macOS_

* `filename` String

Sets the pathname of the file the window represents, and the icon of the file will show in window's title bar.

#### `win.getRepresentedFilename()` _macOS_

Возвращает `String` - путь до файла, который представляет окно.

#### `win.setDocumentEdited(edited)` _macOS_

* `edited` Boolean

Определяет, был ли отредактирован документ окна, иконка в заголовке станет серой, когда установлено `true`.

#### `win.isDocumentEdited()` _macOS_

Returns `Boolean` - Whether the window's document has been edited.

#### `win.focusOnWebView()`

#### `win.blurWebView()`

#### `win.capturePage([rect])`

* `rect` [Rectangle](structures/rectangle.md) (опционально) - границы захвата

Возвращает `Promise<NativeImage>` - разрешается с [NativeImage](native-image.md)

Захватывает снимок страницы в границах `rect`. Пропустив `rect`, будет сделан захват всей видимой страницы.

#### `win.loadURL(url[, options])`

* `url` String
* `options` Object (опционально)
  * `httpReferrer` (String | [Referrer](structures/referrer.md)) (опционально) - HTTP Referrer.
  * `userAgent` String (опционально) - user-agent, создающий запрос.
  * `extraHeaders` String (опционально) - Дополнительные заголовки, разделенные "\n"
  * `postData` ([UploadRawData[]](structures/upload-raw-data.md) | [UploadFile[]](structures/upload-file.md) | [UploadBlob[]](structures/upload-blob.md)) (опционально)
  * `baseURLForDataURL` String (опционально) - Базовый URL (с разделителем пути), для файлов, которые будут загружены по URL данных. Это необходимо, только если указанный `url` это URL данных и необходимо загрузить другие файлы.

Возвращает `Promise<void>` - промис будет разрешен, когда страница завершит загрузку (см. [`did-finish-load`](web-contents.md#event-did-finish-load)), и отклоняет, если страница не удачно загрузилась (см. [`did-fail-load`](web-contents.md#event-did-fail-load)).

Тоже, что и [`webContents.loadURL(url[, options])`](web-contents.md#contentsloadurlurl-options).

The `url` can be a remote address (e.g. `http://`) or a path to a local HTML file using the `file://` protocol.

To ensure that file URLs are properly formatted, it is recommended to use Node's [`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject) method:

```javascript
const url = require('url').format({
  protocol: 'file',
  slashes: true,
  pathname: require('path').join(__dirname, 'index.html')
})

win.loadURL(url)
```

Вы можете загрузить URL, используя `POST`-запрос с зашифрованными URL данными, сделав следующее:

```javascript
win.loadURL('http://localhost:8000/post', {
  postData: [{
    type: 'rawData',
    bytes: Buffer.from('hello=world')
  }],
  extraHeaders: 'Content-Type: application/x-www-form-urlencoded'
})
```

#### `win.loadFile(filePath[, options])`

* `filePath` String
* `options` Object (опционально)
  * `query` Record<String, String> (опционально) - переданная в `url.format()`.
  * `search` String (optional) - Passed to `url.format()`.
  * `hash` String (optional) - Passed to `url.format()`.

Возвращает `Promise<void>` - промис будет разрешен, когда страница завершит загрузку (см. [`did-finish-load`](web-contents.md#event-did-finish-load)), и отклоняет, если страница не удачно загрузилась (см. [`did-fail-load`](web-contents.md#event-did-fail-load)).

Same as `webContents.loadFile`, `filePath` should be a path to an HTML file relative to the root of your application.  See the `webContents` docs for more information.

#### `win.reload()`

Same as `webContents.reload`.

#### `win.setMenu(menu)` _Linux_ _Windows_

* `menu` Menu | null

Устанавливает `menu` в меню окна.

#### `win.removeMenu()` _Linux_ _Windows_

Удаляет меню окна.

#### `win.setProgressBar(progress[, options])`

* `progress` Double
* `options` Object (опционально)
  * `mode` String _Windows_ - Mode for the progress bar. Can be `none`, `normal`, `indeterminate`, `error` or `paused`.

Устанавливает значение прогресса на шкале прогресса. Valid range is [0, 1.0].

Удаляет индикатор прогресса, когда прогресс меньше 0; Изменяет в режим indeterminate, когда прогресс больше 1.

На платформе Linux поддерживается только рабочая среда Unity, Вам необходимо указать имя файла `*.desktop` в поле `desktopName` в `package.json`. По умолчанию будет предполагаться `{app.name}.desktop`.

На Windows режим может быть передан. Принимаемые значения: `none`, `normal`, `indeterminate`, `error` и `paused`. Если Вы вызовете `setProgressBar` без установленного режима (но со значением в пределах допустимого диапозона), будет предполагаться `normal`.

#### `win.setOverlayIcon(overlay, description)` _Windows_

* `overlay` [NativeImage](native-image.md) | null - иконка, которая будет отображаться в правом краю иконки на панели задач. Если параметр `null`, оверлей будет очищен
* `description` String - описание, которое будет представлено для доступности чтения с экрана

Устанавливает 16x16 пиксельный оверлей поверх текущей иконки в панели задач, обычно используется для передачи какого-либо статуса приложения или пассивного уведомления пользователя.

#### `win.setHasShadow(hasShadow)`

* `hasShadow` Boolean

Устанавливает, будет ли окно иметь тень.

#### `win.hasShadow()`

Возвращает `Boolean` - был ли вызов успешным.

#### `win.setOpacity(opacity)` _Windows_ _macOS_

* `opacity` Number - между 0.0 (полная прозрачность) и 1.0 (полная видимость)

Sets the opacity of the window. On Linux, does nothing. Out of bound number values are clamped to the [0, 1] range.

#### `win.getOpacity()`

Возвращает `Number` - между 0.0 (полная прозрачность) и 1.0 (полная видимость). On Linux, always returns 1.

#### `win.setShape(rects)` _Windows_ _Linux_ _Экспериментально_

* `rects` [Rectangle[]](structures/rectangle.md) - Sets a shape on the window. Passing an empty list reverts the window to being rectangular.

Установка формы окна, которая определяет область в окне, где система разрешает отрисовку и взаимодействие пользователя. Вне данного региона ни один пиксель не отрисуется и ни одно событие мыши не будет зарегистрировано. Вне региона события мыши не будут получены этим окном, но будет передаваться чему-либо позади окна.

#### `win.setThumbarButtons(buttons)` _Windows_

* `buttons` [ThumbarButton[]](structures/thumbar-button.md)

Возвращает `Boolean` - успешно ли добавлены кнопки

Добавляет панель миниатюр, с определенным набором кнопок, на слой кнопок в изображении эскиза окна в панели задач. Возвращает объект `Boolean`, который указывает успешно ли добавлена панель миниатюр.

Количество кнопок в панели миниатюр не должно быть больше, чем 7, из-за ограничений. После установки панели миниатюр, панель не может быть удалена, из-за ограничений платформы. Но Вы можете вызвать метод с пустым массивом, чтобы убрать кнопки.

`buttons` является массивом объектов `Button`:

* `Button` Object
  * `icon` [NativeImage](native-image.md) - иконка, отображаемая на панели миниатюр.
  * `click` Function
  * `tooltip` String (опционально) - текст всплывающей подсказки на кнопке.
  * `flags` String[] (опционально) - Управление конкретными состояниями и поведением кнопки. По умолчанию, является `['enabled']`.

`flags` — это массив, который может включать следующие `строки`:

* `enabled` - кнопка активна и доступна пользователю.
* `disabled` - Кнопка отключена. Она присутствует, но имеет неактивное визуальное состояние и не будет реагировать на действия пользователя.
* `dismissonclick` - когда кнопка нажата, окно миниатюры закрывается немедленно.
* `nobackground` - не рисует границы кнопок, использует только изображение.
* `hidden` - кнопка не отображается пользователю.
* `noninteractive` - Кнопка включена, но не интерактивна; ни одно состояние кнопки не отображается. Это значение предназначено для случаев, когда кнопка используется в уведомлении.

#### `win.setThumbnailClip(region)` _Windows_

* `region` [Rectangle](structures/rectangle.md) - Область окна

Устанавливает область окна, которая будет показана в панели миниатюр, при наведении мыши на окно в панели задач. Вы можете сбросить панель миниатюры, чтобы показывалось окно полностью, указав пустую область: `{ x: 0, y: 0, width: 0, height: 0 }`.

#### `win.setThumbnailToolTip(toolTip)` _Windows_

* `toolTip` String

Устанавливает всплывающую подсказку, которая будет отображена, при наведении мыши на панель миниатюры окна в панели задач.

#### `win.setAppDetails(options)` _Windows_

* `options` Object
  * `appId` String (опционально) - [App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v=vs.85).aspx) окна. Это должно быть установлено, иначе остальные параметры не будут иметь никакого эффекта.
  * `appIconPath` String (опционально) - [иконка перезапуска](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v=vs.85).aspx) окна.
  * `appIconIndex` Integer (optional) - Index of the icon in `appIconPath`. Ignored when `appIconPath` is not set. Default is `0`.
  * `relaunchCommand` String (опционально) - [команда перезапуска](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v=vs.85).aspx) окна.
  * `relaunchDisplayName` String (опционально) - [отображаемое имя перезапуска](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v=vs.85).aspx) окна.

Устанавливает свойства для кнопки окна на панели задач.

**Note:** `relaunchCommand` and `relaunchDisplayName` must always be set together. If one of those properties is not set, then neither will be used.

#### `win.showDefinitionForSelection()` _macOS_

Тоже, что и `webContents.showDefinitionForSelection()`.

#### `win.setIcon(icon)` _Windows_ _Linux_

* `icon` [NativeImage](native-image.md) | String

Меняет иконку окна.

#### `win.setWindowButtonVisibility(visible)` _macOS_

* `visible` Boolean

Устанавливает, должны ли быть видны кнопки контроля окна.

Этот метод невозможно вызвать, когда `titleBarStyle` установлено в `customButtonsOnHover`.

#### `win.setAutoHideMenuBar(hide)`

* `hide` Boolean

Sets whether the window menu bar should hide itself automatically. Once set the menu bar will only show when users press the single `Alt` key.

If the menu bar is already visible, calling `setAutoHideMenuBar(true)` won't hide it immediately.

#### `win.isMenuBarAutoHide()`

Возвращает `Boolean` - прячет ли меню себя автоматически.

#### `win.setMenuBarVisibility(visible)` _Windows_ _Linux_

* `visible` Boolean

Sets whether the menu bar should be visible. If the menu bar is auto-hide, users can still bring up the menu bar by pressing the single `Alt` key.

#### `win.isMenuBarVisible()`

Возвращает `Boolean` - видна ли панель меню.

#### `win.setVisibleOnAllWorkspaces(visible[, options])`

* `visible` Boolean
* `options` Object (опционально)
  * `visibleOnFullScreen` Boolean (опционально) _macOS_ - устанавливает видимость панели меню в полноэкранном режиме окна

Устанавливает видимость окна на всех рабочих местах.

**Примечание:** Этот метод ничего не делает Windows.

#### `win.isVisibleOnAllWorkspaces()`

Returns `Boolean` - Whether the window is visible on all workspaces.

**Примечание:** Данный API всегда возвращает false в Windows.

#### `win.setIgnoreMouseEvents(ignore[, options])`

* `ignore` Boolean
* `options` Object (опционально)
  * `forward` Boolean (опционально) _macOS_ _Windows_ - Если true, перенаправляет сообщения о передвижение мыши в Chromium, включая события мыши, такие как `mouseleave`. Only used when `ignore` is true. If `ignore` is false, forwarding is always disabled regardless of this value.

Включает для окна игнорирование событий от мыши.

Все события мыши, произошедшие в этом окне, будут переданы окну позади этого окна, но, если это окно сфокусировано, оно все еще будет получать события клавиатуры.

#### `win.setContentProtection(enable)` _macOS_ _Windows_

* `enable` Boolean

Предотвращает захват содержимого окна другими приложениями.

On macOS it sets the NSWindow's sharingType to NSWindowSharingNone. On Windows it calls SetWindowDisplayAffinity with `WDA_MONITOR`.

#### `win.setFocusable(focusable)` _macOS_ _Windows_

* `focusable` Boolean

Changes whether the window can be focused.

На macOS он не снимает фокус с окна.

#### `win.setParentWindow(parent)`

* `parent` BrowserWindow | null

Устанавливает `parent` как родителя текущего окна, передав `null` превратит текущее окно в окно верхнего уровня.

#### `win.getParentWindow()`

Возвращает `BrowserWindow` - родительское окно.

#### `win.getChildWindows()`

Возвращает `BrowserWindow[]` - все дочерние окна.

#### `win.setAutoHideCursor(autoHide)` _macOS_

* `autoHide` Boolean

Controls whether to hide cursor when typing.

#### `win.selectPreviousTab()` _macOS_

Выбирает предыдущую вкладку, когда нативные вкладки включены и в окне присутствуют другие вкладки.

#### `win.selectNextTab()` _macOS_

Selects the next tab when native tabs are enabled and there are other tabs in the window.

#### `win.mergeAllWindows()` _macOS_

Объединяет все окна в одно окно с множественными вкладками, когда нативные вкладки включены и в присутствуют открытые окна больше, чем 1.

#### `win.moveTabToNewWindow()` _macOS_

Moves the current tab into a new window if native tabs are enabled and there is more than one tab in the current window.

#### `win.toggleTabBar()` _macOS_

Переключает видимость вкладки, если включены нативные вкладки и присутствует только одна вкладка в текущем окне.

#### `win.addTabbedWindow(browserWindow)` _macOS_

* `browserWindow` BrowserWindow

Adds a window as a tab on this window, after the tab for the window instance.

#### `win.setVibrancy(type)` _macOS_

* `type` String | null - Может быть `appearance-based`, `light`, `dark`, `titlebar`, `selection`, `menu`, `popover`, `sidebar`, `medium-light`, `ultra-dark`, `header`, `sheet`, `window`, `hud`, `fullscreen-ui`, `tooltip`, `content`, `under-window` и `under-page`. Смотрите [документацию macOS][vibrancy-docs] для подробностей.

Adds a vibrancy effect to the browser window. Passing `null` or an empty string will remove the vibrancy effect on the window.

Обратите внимание, что `appearance-based`, `light`, `dark`, `medium-light` и `ultra-dark` устарела и будет удалена в следующей версии macOS.

#### `win.setTrafficLightPosition(position)` _macOS_

* `position` [Point](structures/point.md)

Set a custom position for the traffic light buttons. Can only be used with `titleBarStyle` set to `hidden`.

#### `win.getTrafficLightPosition()` _macOS_

Returns `Point` - The current position for the traffic light buttons. Can only be used with `titleBarStyle` set to `hidden`.

#### `win.setTouchBar(touchBar)` _macOS_

* `touchBar` TouchBar | null

Sets the touchBar layout for the current window. Указав `null` или `undefined` очистит сенсорную панель. Этот метод имеет эффект только, если машина имеет сенсорную панель и запускается на macOS 10.12.1+.

**Примечание:** TouchBar API в настоящее время является экспериментальным и может быть изменен или удален в будущих версиях Electron.

#### `win.setBrowserView(browserView)` _Экспериментально_

* `browserView` [BrowserView](browser-view.md) | null - Attach `browserView` to `win`. If there are other `BrowserView`s attached, they will be removed from this window.

#### `win.getBrowserView()` _Экспериментально_

Returns `BrowserView | null` - The `BrowserView` attached to `win`. Returns `null` if one is not attached. Throws an error if multiple `BrowserView`s are attached.

#### `win.addBrowserView(browserView)` _Экспериментально_

* `browserView` [BrowserView](browser-view.md)

Заменяет метод setBrowserView, для поддержки работы с множественными видами браузера.

#### `win.removeBrowserView(browserView)` _Экспериментально_

* `browserView` [BrowserView](browser-view.md)

#### `win.getBrowserViews()` _Экспериментально_

Возвращает `BrowserView[]` - массив всех BrowserViews которые были присоединены с `addBrowserView` или `setBrowserView`.

**Примечание:** BrowserView API в настоящее время экспериментально и может измениться или быть удалено в будущих релизах Electron.

[runtime-enabled-features]: https://cs.chromium.org/chromium/src/third_party/blink/renderer/platform/runtime_enabled_features.json5?l=70
[page-visibility-api]: https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API
[quick-look]: https://en.wikipedia.org/wiki/Quick_Look
[vibrancy-docs]: https://developer.apple.com/documentation/appkit/nsvisualeffectview?preferredLanguage=objc
[window-levels]: https://developer.apple.com/documentation/appkit/nswindow/level
[chrome-content-scripts]: https://developer.chrome.com/extensions/content_scripts#execution-environment
[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
