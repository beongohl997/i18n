# Electron FAQ

## Dlaczego mam problemy z instalacją Electrona?

Wywołując polecenie `npm install electron`, niektórzy użytkownicy napotykają okazjonalne błędy instalacji.

W więkoszości przypadków, błędy są efektem problemów z połączeniem internetowym, a nie błędami pakietu `electron`. Błędy typu `ELIFECYCLE`, `EAI_AGAIN`, `ECONNRESET`, i`ETIMEDOUT` są efeketem problemów z połączeniem internetowym. Najlepszym rozwiązaniem jest próba zmiany sieci lub odczekanie chwili i ponowienie próby instalacji.

Możesz także spróbować pobrać Electrona bezpośrednio z [electron/electron/releases](https://github.com/electron/electron/releases), jeśli instalacja poprzez `npm` zawodzi.

## Kiedy Electron zostanie zaktualizowany do najnowszej wersji Chrome?

Wersja Chrome w bibliotece Electron jest zazwyczaj aktualizowana 1 lub 2 tygodnie po wydaniu nowej, stabilnej wersji Chrome. Nie możemy jednak zagwarantować, iż zostanie to wykonane w powyższym czasie, gdyż zależy to głównie od nakładu pracy, jaki będziemy musieli włożyć przy aktualizacji.

Only the stable channel of Chrome is used. If an important fix is in beta or dev channel, we will back-port it.

Aby uzyskać więcej informacji zobacz [wprowadzenie do zabezpieczeń](tutorial/security.md).

## Kiedy Electron zostanie zaktualizowany do najnowszej wersji Node.js?

Kiedy zostanie wydana nowa wersja Node.js, zazwyczaj czekamy około miesiąc przed wprowadzeniem jej do Electrona. Dzięki temu jesteśmy wstanie zapobiec styczności z błędami z nowej wersji Node.js, a takowe występują bardzo często.

Nowe funkcje Node.js są zazwyczaj wdrażane przez aktualizacje silnika V8, dlatego, iż Electron używa silnika V8 wydawanego przez Przeglądarkę Chrome, nowe funkcje języka JavaScript są zazwyczaj już wdrożone w Electronie.

## Jak udostępniać dane między stronami internetowymi?

Aby udostępniać dane między stronami internetowymi (procesy renderowania) najlepiej jest użyć HTML5 API, które są dostępne w przeglądarkach. Good candidates are [Storage API][storage], [`localStorage`][local-storage], [`sessionStorage`][session-storage], and [IndexedDB][indexed-db].

Możesz również użyć systemu IPC, wyszczególnionego dla Electronu, aby przechowywać obiekty w głównym procesie jako zmienna globalna, a potem uzyskać do nich dostęp w procesie renderowania za pomocą `remote` property of `electron` module:

```javascript
//W głównym procesie.
global.sharedObject = {
  someProperty: 'default value'
}
```

```javascript
// Na stronie 1.
require('electron').remote.getGlobal('sharedObject').someProperty = 'new value'
```

```javascript
// Na stronie 2.
console.log(require('electron').remote.getGlobal('sharedObject').someProperty)
```

## Okno/pole mojej aplikacji zniknęło po kilku minutach.

Ma to miejsce w przypadku, gdy zmienna używana do przechowywania okna/pola jest poddawana automatycznej dealokacji.

Jeśli wystąpi ten problem, poniższe artykuły mogą okazać się pomocne:

* [Zarządzanie pamięcią][memory-management]
* [Zakres Zmiennej][variable-scope]

Jeśli chcesz szybkiej łatki, możesz zglobalizować zmienne poprzez zmianę kodu z tego:

```javascript
const { app, Tray } = require('electron')
app.on('ready', () => {
  const tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```

do tego:

```javascript
const { app, Tray } = require('electron')
let tray = null
app.on('ready', () => {
  tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```

## Nie mogę użyć jQuery/RequireJS/Meteor/AngularJS w Electronie.

Ze względu na integrację Node.js z Electron, występują pewne dodatkowe symbole wstawione do modelu DOM, takie jak `module`, `exports`, `require`. Tworzy to problemy dla niektórych bibliotek ponieważ chcą one wstawić symbole z tymi samymi nazwami.

Aby to rozwiązać, możesz wyłączyć integrację node w Electronie:

```javascript
//W głównym procesie.
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  webPreferences: {
    nodeIntegration: false
  }
})
win.show()
```

Lecz jeśli chcesz zachować możliwości używania Node.js i Electron API, musisz zmienić nazwę symboli na stronie zanim dodasz inne biblioteki:

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

## Nie zdefiniowano `require('electron').xxx`.

Gdy używasz wbudowanego modułu Electron możesz spotkać się z takim błędem:

```sh
> require('electron').webFrame.setZoomFactor(1.0)
Uncaught TypeError: Cannot read property 'setZoomLevel' of undefined
```

This is because you have the [npm `electron` module][electron-module] installed either locally or globally, which overrides Electron's built-in module.

Aby zweryfikować, czy używasz właściwego modułu wbudowanego, możesz wydrukować ścieżkę modułu `electron`:

```javascript
console.log(require.resolve('electron'))
```

a następnie sprawdzić, czy występuje w następującej postaci:

```sh
"/path/to/Electron.app/Contents/Resources/atom.asar/renderer/api/lib/exports/electron.js"
```

Jeżeli widzisz coś takiego jak `node_modules/electron/index.js`,musisz albo usunąć moduł npm `electron`, albo zmienić jego nazwę.

```sh
npm uninstall electron
npm uninstall -g electron
```

Jednak jeśli pomimo użycia wbudowanego modułu wciąż występuje ten błąd, bardzo prawdopodobnym jest to, że używasz modułu w niewłaściwym procesie. Na przykład `electron.app` może być używany tylko w głównym procesie, podczas gdy `electron.webFrame` dostępny jest tylko w procesach renderowania.

## Czcionka wygląda na rozmazaną, co to jest i jak to naprawić?

If [sub-pixel anti-aliasing](http://alienryderflex.com/sub_pixel/) is deactivated, then fonts on LCD screens can look blurry. Przykład:

![subpixel rendering example][]

Antyaliasing subpikseli wymaga nieprzezroczystego tła warstwy zawierającej tekst. (Zobacz [ problem ](https://github.com/electron/electron/issues/6344#issuecomment-420371918) by dowiedzieć się więcej).

To achieve this goal, set the background in the constructor for [BrowserWindow][browser-window]:

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({
  backgroundColor: '#fff'
})
```

The effect is visible only on (some?) LCD screens. Even if you don't see a difference, some of your users may. It is best to always set the background this way, unless you have reasons not to do so.

Zauważ, że sama zmiana tła w CSS nie przyniesie oczekiwanego efektu.

[memory-management]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management
[variable-scope]: https://msdn.microsoft.com/library/bzt2dkta(v=vs.94).aspx
[electron-module]: https://www.npmjs.com/package/electron
[storage]: https://developer.mozilla.org/en-US/docs/Web/API/Storage
[local-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
[session-storage]: https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage
[indexed-db]: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
[browser-window]: api/browser-window.md
[subpixel rendering example]: images/subpixel-rendering-screenshot.gif
