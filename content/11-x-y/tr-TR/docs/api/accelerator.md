# Hızlandırıcı

> Klavye kısayolları tanımlamak.

Accelerators are Strings that can contain multiple modifiers and a single key code, combined by the `+` character, and are used to define keyboard shortcuts throughout your application.

Örnekler:

* `CommandOrControl + A`
* `CommandOrControl + Üst Krkt + Z`

Kısayollar, [`register`](global-shortcut.md#globalshortcutregisteraccelerator-callback) yöntemini kullanarak [`globalShortcut`](global-shortcut.md) modülüyle kaydedilir.

```javascript
const { app, globalShortcut } = require('electron')

app.whenReady().then(() => {
  // Bir 'CommandOrControl+Y' kısayolu dinleyici kaydet.
  globalShortcut.register('CommandOrControl+Y', () => {     // Y ve Command/Control'a basıldığında bir şeyler yapar.
  }) })
```

## Platform bildirimi

Linux ve Windows'ta `Command` tuşunun hiç bir etkisi yok, bu yüzden bazı hızlandırıcıları tanımlamak için macOS'taki `Command` tuşunu ve Linux ve Windows'taki `Control` tuşunu temsil eden `CommandOrControl`'u kullanın.

`Option tuşu` yerine `Alt` tuşunu kullanın. `Option` tuşu sadece macOS'te bulunur, fakat `Alt` tuşu bütün platformlarda bulunur.

`Super` tuşu, Windows ve Linux'ta `Windows`, macOS'ta ise `Cmd` tuşuyla eşleştirilmiştir.

## Kullanılabilir düzenleyiciler

* `Command` (yada kısaca `Cmd`)
* `Control` (yada kısaca `Ctrl`)
* `CommandOrControl` (yada kısaca `CmdOrCtrl`)
* `Alt`
* `Seçenek`
* `AltGr`
* `ÜstKrkt`
* `Süper`

## Kullanılabilir anahtar kodları

* `0`'dan `9`'a
* `A`'dan `Z`'ye
* `F1`'den `F24`'e
* `~`, `!`, `@`, `#`, `$`, vesaire gibi noktalama işaretleri.
* `Artı`
* `Boşluk`
* `Sekme`
* `Capslock`
* `Numlock`
* `Scrolllock`
* `Geri silme`
* `Sil`
* `Ekleme`
* `Return` (yada diğer adıyla `Enter`)
* `Up`, `Down`, `Left` ve `Right`
* `Home` ve `End`
* `PageUp` ve `PageDown`
* `Çıkış` (veya `Esc` kısaca)
* `Sesi Aç`, `Sesi Kıs` ve `Sesi Kapat`
* `MediaNextTrack`, `MediaPreviousTrack`, `MediaStop` ve `MediaPlayPause`
* `Ekran Görüntüsü`
* NumPad Tuşları
  * `num0` - `num9`
  * `numdec` - ondalık anahtar
  * `numadd` - numpad `+` tuşu
  * `numsub` - numpad `-` tuş
  * `nummult` - numpad `*` tuş
  * `numdiv` - numpad `÷` tuşu
