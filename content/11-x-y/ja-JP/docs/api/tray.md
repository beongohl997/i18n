## クラス: Tray

> システムの通知領域にアイコンやコンテキスト メニューを追加します。

プロセス: [Main](../glossary.md#main-process)

`Tray` は [EventEmitter][event-emitter] です。

```javascript
const { app, Menu, Tray } = require('electron')

let tray = null
app.whenReady().then(() => {
  tray = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    { label: 'Item1', type: 'radio' },
    { label: 'Item2', type: 'radio' },
    { label: 'Item3', type: 'radio', checked: true },
    { label: 'Item4', type: 'radio' }
  ])
  tray.setToolTip('This is my application.')
  tray.setContextMenu(contextMenu)
})
```

__プラットフォームによる制限:__

* Linux では、アプリインジゲータがサポートされている場合はそれが使用され、それ以外では `GtkStatusIcon` が代わりに使用されます。
* アプリインジゲータのみがある Linux ディストリビューションでは、tray アイコンを動かすために `libappindicator1` をインストールする必要があります。
* アプリインジゲータはコンテキストメニューがあるときのみ表示されます。
* Linux でアプリインジゲータが使用されるとき、`click` イベントは無視されます。
* Linux では、個々の `MenuItem` に加えられた変更を有効にするには、`setContextMenu` を再び呼ぶ必要があります。 例:

```javascript
const { app, Menu, Tray } = require('electron')

let appIcon = null
app.whenReady().then(() => {
  appIcon = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    { label: 'Item1', type: 'radio' },
    { label: 'Item2', type: 'radio' }
  ])

  // Make a change to the context menu
  contextMenu.items[1].checked = false

  // Call this again for Linux because we modified the context menu
  appIcon.setContextMenu(contextMenu)
})
```
* Windows では、最適な視覚効果を得るために `ICO` 形式のアイコンファイルを使用することが推奨されています。

すべてのプラットフォームでまったく同じ動作を維持したい場合は、`click` イベントに頼らず、tray アイコンに常にコンテキストメニューを適用して下さい。


### `new Tray(image, [guid])`

* `image` ([NativeImage](native-image.md) | String)
* `guid` String (optional) _Windows_ - Assigns a GUID to the tray icon. If the executable is signed and the signature contains an organization in the subject line then the GUID is permanently associated with that signature. OS level settings like the position of the tray icon in the system tray will persist even if the path to the executable changes. If the executable is not code-signed then the GUID is permanently associated with the path to the executable. Changing the path to the executable will break the creation of the tray icon and a new GUID must be used. However, it is highly recommended to use the GUID parameter only in conjunction with code-signed executable. If an App defines multiple tray icons then each icon must use a separate GUID.

`image` に関連する新しい tray アイコンを作成します。

### インスタンスイベント

`tray` モジュールには以下のイベントがあります。

#### イベント: 'click'

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rectangle](structures/rectangle.md) - tray アイコンの境界。
* `position` [Point](structures/point.md) - イベントの位置。

tray アイコンがクリックされたときに発行されます。

#### イベント: 'right-click' _macOS_ _Windows_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rectangle](structures/rectangle.md) - tray アイコンの境界。

tray アイコンが右クリックされたときに発行されます。

#### イベント: 'double-click' _macOS_ _Windows_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rectangle](structures/rectangle.md) - tray アイコンの境界。

tray アイコンがダブルクリックされたときに発行されます。

#### イベント: 'balloon-show' _Windows_

tray バルーンを表示するときに発行されます。

#### イベント: 'balloon-click' _Windows_

tray バルーンがクリックされたときに発行されます。

#### イベント: 'balloon-closed' _Windows_

tray バルーンが、タイムアウトかユーザの手動で、閉じられたときに発行されます。

#### イベント: 'drop' _macOS_

tray アイコン上に何かのドラッグされたアイテムがドロップされたときに発行されます。

#### イベント: 'drop-files' _macOS_

戻り値:

* `event` Event
* `files` String[] - ドロップされたファイルのパス。

tray アイコン上にドラッグされたファイルがドロップされたときに発行されます。

#### イベント: 'drop-text' _macOS_

戻り値:

* `event` Event
* `text` String - ドロップされたテキスト文字列。

tray アイコン上にドラッグされたテキストがドロップされたときに発行されます。

#### イベント: 'drag-enter' _macOS_

ドラッグ操作が tray アイコン内に入ったときに発行されます。

#### イベント: 'drag-leave' _macOS_

ドラッグ操作が tray アイコン内から出たときに発行されます。

#### イベント: 'drag-end' _macOS_

ドラッグ操作が、tray 上か他の場所で終了したときに発行されます。

#### Event: 'mouse-up' _macOS_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Point](structures/point.md) - イベントの位置。

Emitted when the mouse is released from clicking the tray icon.

Note: This will not be emitted if you have set a context menu for your Tray using `tray.setContextMenu`, as a result of macOS-level constraints.

#### Event: 'mouse-down' _macOS_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Point](structures/point.md) - イベントの位置。

Emitted when the mouse clicks the tray icon.

#### イベント: 'mouse-enter' _macOS_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Point](structures/point.md) - イベントの位置。

マウスが tray アイコン内に入ったときに発行されます。

#### イベント: 'mouse-leave' _macOS_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Point](structures/point.md) - イベントの位置。

マウスが tray アイコン内から出たときに発行されます。

#### イベント: 'mouse-move' _macOS_ _Windows_

戻り値:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Point](structures/point.md) - イベントの位置。

マウスが tray アイコン内で動いたときに発行されます。

### インスタンスメソッド

`Tray` クラスは以下のメソッドを持ちます。

#### `tray.destroy()`

tray アイコンを即座に削除します。

#### `tray.setImage(image)`

* `image` ([NativeImage](native-image.md) | String)

この tray アイコンに関連付けられた `image` を設定します。

#### `tray.setPressedImage(image)` _macOS_

* `image` ([NativeImage](native-image.md) | String)

macOS において、この tray アイコンが押されたときの関連付けられた `image` を設定します。

#### `tray.setToolTip(toolTip)`

* `toolTip` String

この tray アイコンのホバーテキストを設定します。

#### `tray.setTitle(title[, options])` _macOS_

* `title` String
* `options` Object (任意)
  * `fontType` String (optional) - The font family variant to display, can be `monospaced` or `monospacedDigit`. `monospaced` is available in macOS 10.15+ and `monospacedDigit` is available in macOS 10.11+.  When left blank, the title uses the default system font.

ステータスバー内の tray アイコンの隣に表示されるタイトル (ANSI カラーサポート) を設定します。

#### `tray.getTitle()` _macOS_

戻り値 `String` - ステータスバーの tray アイコンの隣に表示されるタイトル

#### `tray.setIgnoreDoubleClickEvents(ignore)` _macOS_

* `ignore` Boolean

ダブルクリックイベントを無視するオプションを設定します。 これらのイベントを無視することで tray アイコンそれぞれの独立したクリックを検知することを許可します。

この値はデフォルトで false にセットされます。

#### `tray.getIgnoreDoubleClickEvents()` _macOS_

戻り値 `Boolean` - ダブルクリックイベントが無視されているかどうか。

#### `tray.displayBalloon(options)` _Windows_

* `options` Object
  * `icon` ([NativeImage](native-image.md) | String) (任意) - `iconType` が `custom` のときに使うアイコン。
  * `iconType` String (任意) - `none`、`info`、`warning`、`error`、`custom` のいずれかにできます。 省略値は `custom` です。
  * `title` String
  * `content` String
  * `largeIcon` Boolean (任意) - 大きなバージョンのアイコン。できればこちらを使用します。 省略値は `true` です。 [`NIIF_LARGE_ICON`][NIIF_LARGE_ICON] に対応します。
  * `noSound` Boolean (任意) - 関連付けられたサウンドを再生しないようにします。 省略値は、`false` です。 [`NIIF_NOSOUND`][NIIF_NOSOUND] に対応します。
  * `respectQuietTime` Boolean (任意) - ユーザが現在 "おやすみモード" の場合、バルーン通知を表示しないようにします。 省略値は、`false` です。 [`NIIF_RESPECT_QUIET_TIME`][NIIF_RESPECT_QUIET_TIME] に対応します。

tray のバルーンを表示します。

#### `tray.removeBalloon()` _Windows_

tray のバルーンを除去します。

#### `tray.focus()` _Windows_

タスクバーの通知領域にフォーカスを戻します。 通知領域アイコンは、UI 操作が完了したときにこのメッセージを使う必要があります。 たとえば、アイコンがショートカットメニューを表示しているけれど、ユーザーが ESC を押してキャンセルする場合、`tray.focus()` を使用して通知領域にフォーカスを戻します。

#### `tray.popUpContextMenu([menu, position])` _macOS_ _Windows_

* `menu` Menu (任意)
* `position` [Point](structures/point.md) (任意) - ポップアップ位置。

tray アイコンのコンテキストメニューをポップアップ表示します。 `menu` が渡されると、tray アイコンのコンテキストメニューの代わりに `menu` を表示します。

`position` は Windows でのみ有効で、省略値は (0, 0) です。

#### `tray.closeContextMenu()` _macOS_ _Windows_

Closes an open context menu, as set by `tray.setContextMenu()`.

#### `tray.setContextMenu(menu)`

* `menu` Menu | null

このアイコンのコンテキストメニューを設定します。

#### `tray.getBounds()` _macOS_ _Windows_

戻り値 [`Rectangle`](structures/rectangle.md)

`Object` としてのこの tray アイコンの `bounds`。

#### `tray.isDestroyed()`

戻り値 `Boolean` - tray アイコンが破棄されたかどうか。

[NIIF_NOSOUND]: https://docs.microsoft.com/en-us/windows/win32/api/shellapi/ns-shellapi-notifyicondataa#niif_nosound-0x00000010
[NIIF_LARGE_ICON]: https://docs.microsoft.com/en-us/windows/win32/api/shellapi/ns-shellapi-notifyicondataa#niif_large_icon-0x00000020
[NIIF_RESPECT_QUIET_TIME]: https://docs.microsoft.com/en-us/windows/win32/api/shellapi/ns-shellapi-notifyicondataa#niif_respect_quiet_time-0x00000080

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
