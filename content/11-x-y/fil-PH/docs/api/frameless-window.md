# Window na Walang Frame

> Magbukas ng window na walang mga toolbar, mga border, o iba pang graphical na "chrome".

Ang isang frameless window ay isang window na walang

 chrome </ 0>, ang mga bahagi ng window, tulad ng mga toolbar, na hindi bahagi ng web page. Ang mga ito ay mga pagpipilian sa klase ng ` BrowserWindow </ 0>.</p>

<h2 spaces-before="0">Gumawa ng isang frameless window</h2>

<p spaces-before="0">Upang lumikha ng isang frameless window, kailangan mong itakda ang <code> frame </ 0> sa <code> false </ 0> sa
<a href="browser-window.md"> BrowserWindow </ 1> 's <code> options </ 0>:</p>

<pre><code class="javascript">const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ width: 800, height: 600, frame: false })
win.show()
`</pre> 



### Mga alternatibo sa macOS

There's an alternative way to specify a chromeless window. Instead of setting `frame` to `false` which disables both the titlebar and window controls, you may want to have the title bar hidden and your content extend to the full window size, yet still preserve the window controls ("traffic lights") for standard window actions. Magagawa mo ito sa pamamagitan ng pagtukoy sa pagpipiliang ` titleBarStyle </ 0>:</p>

<h4 spaces-before="0"><code>nakatago`</h4> 

Mga resulta sa isang nakatagong title bar at isang full size content window , gayon pa man ang title bar ay mayroon ding mga karaniwang mga kontrol ng window ("traffic lights") sa kaliwang tuktok.



```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ titleBarStyle: 'hidden' })
win.show()
```




#### `hiddenInset`

Mga resulta sa isang nakatagong title bar  na may isang alternatibong hitsura kung saan ang mga pindutan ng traffic lights ay bahagyang higit pa mula sa gilid ng window.



```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ titleBarStyle: 'hiddenInset' })
win.show()
```




#### `customButtonsOnHover
`

Uses custom drawn close, and miniaturize buttons that display when hovering in the top left of the window. The fullscreen button is not available due to restrictions of frameless windows as they interface with Apple's macOS window masks. These custom buttons prevent issues with mouse events that occur with the standard window toolbar buttons. This option is only applicable for frameless windows.



```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ titleBarStyle: 'customButtonsOnHover', frame: false })
win.show()
```




## Transparent window 

Sa pamamagitan ng pagtatakda ng opsyon na ` transparent </ 0> sa <code> true </ 0>, maaari mo ring gawin ang frameless
transparent window:</p>

<pre><code class="javascript">const { BrowserWindow } = require('electron')
const win = new BrowserWindow({ transparent: true, frame: false })
win.show()
`</pre> 



### Mga limitasyon

* Hindi ka maaaring mag-click sa transparent area. Kami ay magpapakilala ng isang API upang itakda ang window shape upang malutas ito, kita n'yo  ang aming isyu </ 0> para sa mga detalye.</p></li> 
  
  * Transparent windows are not resizable. Setting `resizable` to `true` may make a transparent window stop working on some platforms.

* Nalalapat lamang ang filter na ` lumabo </ 0> sa web page, kaya walang paraan upang mag-apply
ng blur effect sa nilalaman sa ibaba ng window (ibig sabihin, iba pang mga application bukas sa
ang sistema ng gumagamit).</p></li>
<li><p spaces-before="0">Sa mga operating system ng Windows, ang mga transparent windows ay hindi gagana kapag ang DWM ay
hindi pinagana.</p></li>
<li><p spaces-before="0">On Linux, users have to put <code>--enable-transparent-visuals --disable-gpu` in the command line to disable GPU and allow ARGB to make transparent window, this is caused by an upstream bug that [alpha channel doesn't work on some NVidia drivers](https://code.google.com/p/chromium/issues/detail?id=369209) on Linux.

* On Mac, the native window shadow will not be shown on a transparent window.</ul> 



## Click-through window 

Upang lumikha ng isang click-through window, i.e. paggawa ng window huwag pansinin ang lahat ng mouse mga kaganapan, maaari mong tawagan ang  win.setIgnoreMouseEvents (ignore) </ 0> API:</p> 



```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow()
win.setIgnoreMouseEvents(true)
```




### Forwarding

Ignoring mouse messages makes the web page oblivious to mouse movement, meaning that mouse movement events will not be emitted. On Windows operating systems an optional parameter can be used to forward mouse move messages to the web page, allowing events such as `mouseleave` to be emitted:



```javascript
const win = require('electron').remote.getCurrentWindow()
const el = document.getElementById('clickThroughElement')
el.addEventListener('mouseenter', () => {
  win.setIgnoreMouseEvents(true, { forward: true })
})
el.addEventListener('mouseleave', () => {
  win.setIgnoreMouseEvents(false)
})
```


This makes the web page click-through when over `el`, and returns to normal outside it.



## Draggable region 

Bilang default, ang frameless window ay hindi draggable. Kailangan ng mga app na tukuyin ` -webkit-app-region: drag </ 0> sa CSS upang sabihin sa Electron kung saan ang mga rehiyon ay draggable
(tulad ng OS's standard titlebar), at maaari ring gamitin ang apps
<code> -webkit-app-region: no-drag </ 0> upang ibukod ang hindi draggable na lugar mula sa
 draggable region. Tandaan na ang tanging hugis-parihaba na hugis ay kasalukuyang sinusuportahan.</p>

<p spaces-before="0">Tandaan: <code> -webkit-app-region: drag </ 0> ay kilala na mayroong mga problema habang bukas ang mga tool ng developer. Tingnan ang <a href="https://github.com/electron/electron/issues/3647"> GitHub isyu </ 0> para sa iba pang mga impormasyon kabilang na ang isang workaround.</p>

<p spaces-before="0">Upang gawing draggable ang buong window, maaari kang magdagdag ng <code> -webkit-app-region: drag </ 0> as
<code>body`'s style:



```html
<body style="-webkit-app-region: drag">
</body>
```


At tandaan na kung ginawa mo ang buong window draggable, kailangan mo ring markahan ang mga pindutan bilang hindi draggable, kung hindi, imposible para sa mga gumagamit na mag-click sa kanila:



```css
button {
  -webkit-app-region: no-drag;
}
```


If you're only setting a custom titlebar as draggable, you also need to make all buttons in titlebar non-draggable.



## Text selection 

In a frameless window the dragging behavior may conflict with selecting text. Halimbawa, kapag nag-drag ka sa titlebar maaari mong aksidenteng piliin ang teksto sa titlebar. Upang maiwasan ito, kailangan mong huwag paganahin ang pagpili ng teksto sa loob ng isang draggable na lugar tulad nito:



```css
.titlebar {
  -webkit-user-select: none;
  -webkit-app-region: drag;
}
```




## Context menu 

Sa ilang mga platform, ang draggable area ay ituturing bilang isang non-client frame, kaya kapag nag-right click ka dito ang isang sistema ng menu ay magpa-pop up. Upang gawin ang menu ng konteksto kumilos ng tama sa lahat ng mga platform na hindi ka dapat gumamit ng custom context menu sa draggable areas.
