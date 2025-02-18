# Electron FAQ

## Warum habe ich Probleme bei der Installation von Electron?

Beim Ausführen von `npm install electron` können bei einigen Nutzern gelegentlich Installationsfehler auftreten.

In fast allen Fällen sind diese Fehler das Ergebnis von Netzwerkproblemen und nicht von tatsächlichen Problemen mit dem Paket `electron` npm. Fehler wie `ELIFECYCLE`, `EAI_AGAIN`, `ECONNRESET`, and `ETIMEDOUT` weisen alle auf ein Problem mit dem Netzwerk hin. Die beste Lösung ist es zu Versuchen die Netzwerkverbindung zu wechseln oder etwas zu warten und die Installation erneut zu versuchen.

Man kann auch versuchen, Electron direkt unter [electron/electron/releases](https://github.com/electron/electron/releases) herunterzuladen, falls die Installation über `npm` weiterhin fehlschlägt.

## Wann wird Electron auf die neueste Version von Chrome aktualisiert?

Die Chrome Version von Electron wird in der Regel innerhalb von ein oder zwei Wochen Implementiert nachdem eine neue stabile Version für Chrome veröffentlicht wird. Diese Schätzung kann nicht garantiert werden und hängt vom Arbeitsaufwand während der Aktualisierung ab.

Nur der stabile Kanal von Chrome wird verwendet. Wenn ein wichtiger Fix im Beta- oder Dev-Kanal ist, werden wir ihn zurückportieren.

Weitere Informationen finden Sie in der [Einführung zur Sicherheit](tutorial/security.md).

## Wann wird Electron auf die neueste Version von Node.js upgraden?

Wenn eine neue Version von Node.js veröffentlicht wird, warten wir in der Regel etwa einen Monat vor dem Upgrade in Elektron. So können wir vermeiden, die häufigen Bugs in neuen Node.js Versionen mit in Electron einzubinden.

Neue Funktionalitäten von Node.js werden in der Regel durch V8 Upgrades ermöglicht. Da Electron das vom Chrome Browser mitgelieferte V8 verwendet, stehen die neuen JavaScript Funktionalitäten der neuen Node.js Version in der Regel bereits in Electron zur Verfügung.

## Wie kann man Daten zwischen Webseiten austauschen?

Um Daten zwischen Web-Seiten (Renderer-Prozesse) zu teilen, ist der einfachste Weg, HTML5-APIs zu verwenden, die bereits in Browsern zur Verfügung stehen. Good candidates are [Storage API][storage], [`localStorage`][local-storage], [`sessionStorage`][session-storage], and [IndexedDB][indexed-db].

Oder Sie können das IPC-System verwenden, welches speziell für Electron ist, um Objekte in den Hauptprozess als eine globale Variable zu speichern und dann vom Renderer über die `remote`-Eigenschaft des `Elektrons` Moduls darauf zugreifen:

```javascript
// Im Hauptprozess.
global.sharedObject = {
  someProperty: 'default value'
}
```

```javascript
// Auf Seite 1.
require('electron').remote.getGlobal('sharedObject').someProperty = 'new value'
```

```javascript
// Auf Seite 2.
console.log(require('electron').remote.getGlobal('sharedObject').someProperty)
```

## My app's tray disappeared after a few minutes.

Dies geschieht, wenn die Variable die das Fenster speichert abgeräumt wird.

Wenn dieses Problem auftritt, könnten die folgenden Artikel hilfreich sein:

* [Speicherverwaltung][memory-management]
* [Geltungsbereich von Variablen][variable-scope]

Wenn Sie eine schnelle Lösung bevorzugen, machen Sie die Variablen global, indem Sie Ihren Code

```javascript
const { app, Tray } = require('electron')
app.whenReady().then(() => {
  const tray = new Tray('/path/to/icon.png')
  tray.setTitle('Hallo Welt')
})
```

wie folgt anpassen:

```javascript
const { app, Tray } = require('electron')
let tray = null
app.whenReady().then(() => {
  tray = new Tray('/path/to/icon.png')
  tray.setTitle('Hallo Welt')
})
```

## Ich kann jQuery/RequireJS/Meteor/AngularJS in Electron nicht verwenden.

Wegen der Node.js Integration in Electron gibt es einige zusätzlich eingefügte Symbole im DOM wie `module`, `exports`, `require`. Das verursacht Probleme für einige Bibliotheken, da diese versuchen, Symbole mit den gleichen Namen einzufügen.

Um dies zu beheben können Sie die Node-Integration in Electron deaktivieren:

```javascript
// Im Hauptprozess.
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  webPreferences: {
    nodeIntegration: false
  }
})
win.show()
```

Wenn Sie sich jedoch die Möglichkeit, Node.js und Electron APIs zu nutzen, offen halten möchten, müssen Sie die Symbole auf der Seite umbenennen, bevor Sie andere Bibliotheken einbinden:

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

## `require('electron').xxx` is undefined.

Während Sie das in Electron integrierte Modul nutzen, könnten Sie auf einen Fehler wie diesen stoßen:

```sh
> require('electron').webFrame.setZoomFactor(1.0)
Uncaught TypeError: Cannot read property 'setZoomLevel' of undefined
```

This is because you have the [npm `electron` module][electron-module] installed either locally or globally, which overrides Electron's built-in module.

Um zu überprüfen, ob Sie das richtige integrierte Modul verwenden, können Sie den Pfad des `electron`-Moduls ausgeben lassen:

```javascript
console.log(require.resolve('electron'))
```

und kontrollieren ob es folgende Form hat:

```sh
"/path/to/Electron.app/Contents/Resources/atom.asar/renderer/api/lib/exports/electron.js"
```

Wenn der Pfad wie `node_modules/electron/index.js` aussieht, dann können Sie entweder das npm `electron` Modul entfernen oder umbenennen.

```sh
npm uninstall electron
npm uninstall -g electron
```

Wenn Sie allerdings das integrierte Modul nutzen und trotzdem diesen Fehler bekommen, dann ist es sehr wahrscheinlich, dass Sie das Modul im falschen Prozess verwenden. Beispielsweise kann `electron.app` nur im Hauptprozess verwendet werden, während `electron.webFrame` nur im Renderer-Prozess verfügbar ist.

## Die Schrift sieht verschwommen aus, was ist das und was kann ich tun?

If [sub-pixel anti-aliasing](http://alienryderflex.com/sub_pixel/) is deactivated, then fonts on LCD screens can look blurry. Beispiel:

![subpixel rendering example][]

Subpixel Antialiasing benötigt einen nicht-transparenten Hintergrund der Ebene, die die Schrift-Glyphen enthält. (Siehe [dieses Problem](https://github.com/electron/electron/issues/6344#issuecomment-420371918) für weitere Informationen).

To achieve this goal, set the background in the constructor for [BrowserWindow][browser-window]:

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  backgroundColor: '#fff'
})
```

The effect is visible only on (some?) LCD screens. Even if you don't see a difference, some of your users may. It is best to always set the background this way, unless you have reasons not to do so.

Beachten Sie, dass nur die Einstellung des Hintergrunds in der CSS nicht den gewünschten Effekt hat.

[memory-management]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management
[variable-scope]: https://msdn.microsoft.com/library/bzt2dkta(v=vs.94).aspx
[electron-module]: https://www.npmjs.com/package/electron
[storage]: https://developer.mozilla.org/en-US/docs/Web/API/Storage
[local-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
[session-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage
[indexed-db]: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
[browser-window]: api/browser-window.md
[subpixel rendering example]: images/subpixel-rendering-screenshot.gif
