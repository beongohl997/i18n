# कीबोर्ड शोर्टकट्स

> स्थानीय और वैश्विक कीबोर्ड शॉर्टकट्स कॉन्फ़िगर करें

## स्थानीय शॉर्टकट्स

आप [मेन्यु][] मोड्यूल का इस्तेमाल कर कीबोर्ड शोर्टकट्स को कॉन्फ़िगर कर सकते हैं जो तभी शुरू होंगी जब एप्प केन्द्रित होगी | To do so, specify an [`accelerator`][] property when creating a [MenuItem][].

```js
const { Menu, MenuItem } = require('electron')
const menu = new Menu()

menu.append(new MenuItem({
  label: 'Print',
  accelerator: 'CmdOrCtrl+P',
  click: () => { console.log('time to print stuff') }
}))
```

You can configure different key combinations based on the user's operating system.

```js
{
  accelerator: process.platform === 'darwin' ? 'Alt+Cmd+I' : 'Ctrl+Shift+I'
}
```

## वैश्विक शोर्टकट्स

आप [globalShortcut][] मोड्यूल का इस्तेमाल कर कीबोर्ड इवेंट्स का पता लगा सकते हैं, तब भी जब एप्लीकेशन के पास कीबोर्ड का ध्यान केन्द्रित न हो |

```js
const { app, globalShortcut } = require('electron')

app.whenReady().then(() => {
  globalShortcut.register('CommandOrControl+X', () => {
    console.log('CommandOrControl+X is pressed')
  })
})
```

## एक BrowserWindow के भीतर शॉर्टकट्स

अगर आप एक [ब्राउज़रविंडो][] के लिए कीबोर्ड शोर्टकट्स को संभालना चाहते हैं, तो आप रेंदेरेर प्रक्रिया में विंडो ऑब्जेक्ट के ऊपर `keyup` और `keydown` इवेंट लिसेनर्स का इस्तेमाल कर सकते हैं |

```js
window.addEventListener('keyup', doSomething, true)
```

तीसरे पैरामीटर `true` पर ध्यान दें, जिसका मतलब है कि लिस्नर को हमेशा दुसरे लिस्नर्स से पहले कुंजी दबाब प्राप्त होंगे, ताकि उनके ऊपर `stopPropagation()` न बुलाया जा सके |

[`before-input-event`](../api/web-contents.md#event-before-input-event) इवेंट पेज में `keydown` और `keyup` इवेंट्स को पहुँचाने से पहले छोड़ा जाता है | इसे उन कस्टम शोर्टकट्स को पकड़ने और संभालने के लिए इस्तेमाल किया जाता हैं, जो मेन्यु में अदृश्य होती हैं |

अगर आप मैन्युअल शोर्टकट पार्सिंग नहीं करना चाहते, तो उन्नत कुंजी खोज के लिए लाइब्रेरीज मौज़ूद हैं, जैसे कि [माउसट्रैप][] |

```js
Mousetrap.bind('4', () => { console.log('4') })
Mousetrap.bind('?', () => { console.log('show shortcuts!') })
Mousetrap.bind('esc', () => { console.log('escape') }, 'keyup')

// combinations
Mousetrap.bind('command+shift+k', () => { console.log('command shift k') })

// map multiple combinations to the same callback
Mousetrap.bind(['command+k', 'ctrl+k'], () => {
  console.log('command k or control k')

  // return false to prevent default behavior and stop event from bubbling
  return false
})

// gmail style sequences
Mousetrap.bind('g i', () => { console.log('go to inbox') })
Mousetrap.bind('* a', () => { console.log('select all') })

// konami code!
Mousetrap.bind('up up down down left right left right b a enter', () => {
  console.log('konami code')
})
```

[मेन्यु]: ../api/menu.md
[MenuItem]: ../api/menu-item.md
[globalShortcut]: ../api/global-shortcut.md
[`accelerator`]: ../api/accelerator.md
[ब्राउज़रविंडो]: ../api/browser-window.md
[माउसट्रैप]: https://github.com/ccampbell/mousetrap
