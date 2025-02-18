# Writing Your First Electron App

يمكنك الكترون من إنشاء تطبيقات لأجهزة الحاسب المكتبي باستخدام الجافا سكريبت (JavaScript) بشكل خالص عن طريق تزويد المطور ببيئة تشغيلية غنية بمكونات أصلية (API) لكل نظام تشغيل. تستطيع اعتبار إلكترون كنكهة مختلفة من نود. جي اس (Node.js) تركز على انشاء تطبيقات سطح المكتب بدلاً من سيرفرات الويب. سيرفرات الويب هي من اختصاص نود. جي اس، بينما تطبيقات سطح المكتب هي من اختصاص إلكترون.

هذا لا يعني أن Electron هو ربط جافا سكريبت لمكتبات واجهة المستخدم الرسومية (GUI). بدلاً من ذلك ، يستخدم Electron صفحات الويب كجهاز GUI ، لذا يمكنك أيضًا رؤيتها كمتصفح صغير على Chromium ، يتم التحكم فيه بواسطة جافا سكريبت.

**ملاحظة**: هذا المثال متاح أيضًا كمستودع يمكنك</a> تنزيله وتشغيله على الفور .

</p> 

وفيما يتعلق بالتطوير ، فإن تطبيق الإلكترون هو في الأساس تطبيق Node.js. نقطة البداية `package.json ` هي مماثلة لتلك الخاصة بوحدة Node.js. سيكون تطبيق الإلكترون على بنية المجلد التالية:



```plaintext
your-app/
├── package.json
├── main.js
└── index.html
```


أنشئ مجلدًا فارغًا جديدًا لتطبيقك الإلكتروني الجديد. و إفتح موجه الأوامر الخاص بك وقم بتشغيله و أكتب فيه ` npm init ` من هذا المجلد.



```sh
npm init
```


سوف يرشدك npm من خلال إنشاء `package.json` في الملف الأساسي . النص البرمجي المحدد بواسطة `main` الملف الأساسي لتشغيل تطبيقك ، والذي سيشغل العملية الرئيسية. An example of your `package.json` might look like this:



```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js"
}
```


__ملاحظة__ : إذا كان `main` الحقل غير موجود في `package.json` ، سيحاول الإلكترون تحميل `index.js` (كما يفعل Node.js) .

By default, `npm start` would run the main script with Node.js. in order to make it run with Electron, you can add a `start` script:



```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  }
}
```




## تثبيت إكترون

في هذه المرحلة ، ستحتاج إلى تثبيت `electron` نفسه. والطريقة الموصى بها للقيام بذلك هي تثبيته كإعتماد على التطوير في تطبيقك ، مما يسمح لك بالعمل على تطبيقات متعددة بنسخ إلكترون مختلفة. للقيام بذلك ، قم بتشغيل الأمر التالي من دليل التطبيق الخاص بك:



```sh
npm install --save-dev electron
```


توجد وسائل أخرى لتثبيت الكترون. يرجى الرجوع إلى </a>دليل التثبيت للتعرف على الاستخدام مع البروكسي والتخزين المؤقت المخصص.</p> 



## تطوير إلكترون باختصار

يتم تطوير تطبيقات الإلكترون في جافا سكريبت باستخدام نفس المبادئ والأساليب الموجودة في تطوير Node.js. يمكن الوصول إلى جميع واجهات برمجة التطبيقات والميزات الموجودة في Electron من خلال `electron` الوحدة النمطية ، والتي يمكن أن تكون مطلوبة مثل أي وحدة Node.js أخرى:



```javascript
const electron = require('electron')
```


تعرض الوحدة النمطية ` electron </ 0> ميزات في مساحات الأسماء. كأمثلة ، دورة الحياة
يتم إدارة التطبيق من خلال <code> electron.app </ 0> ، يمكن إنشاء النوافذ
باستخدام فئة <code> electron.BrowserWindow </ 0>. قد ينتظر ملف <code> main.js </ 0> بسيط
لكي يكون التطبيق جاهزًا وفتح نافذة:</p>

<pre><code class="javascript">const { app, BrowserWindow } = require('electron')
  
  function createWindow () {
    // إنشاء نافذة طولها 800 وعرضها 600.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // and load the index.html of the app.
  win.loadFile('index.html')
}

app.whenReady().then(createWindow)
`</pre> 

يجب أن تقوم ` main.js </ 0> بإنشاء النوافذ والتعامل مع جميع أحداث النظام الخاصة بك
قد يواجه التطبيق. نسخة أكثر اكتمالا من المثال أعلاه
قد يفتح أدوات مطوري البرامج أو يعالج النافذة المغلقة أو يعاد إنشاءها
النوافذ على نظام MacOS إذا نقر المستخدم على رمز التطبيق في قفص الاتهام.</p>

<pre><code class="javascript">const { app, BrowserWindow } = require('electron')
  
  function createWindow () {
    // إنشاء نافذة طولها 800 وعرضها 600.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // and load the index.html of the app.
  win.loadFile('index.html')

   // افتح DevTools.
  win.webContents.openDevTools()
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// لا يمكن استخدام بعض APIs إلا بعد حدوث هذا الحدث.
app.whenReady().then(createWindow)

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow()
  }
})

// In this file you can include the rest of your app's specific main process
// code. يمكنك أيضًا وضعها في ملفات منفصلة وطلبها هنا.
`</pre> 

وأخيرًا ، فإن ` index.html </ 0> هي صفحة الويب التي تريد إظهارها:</p>

<pre><code class="html"><!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
`</pre> 



## تشغيل تطبيقك

بمجرد إنشائك الأولية ` main.js </ 0> ، <code> index.html </ 0> ، و <code> package.json </ 0>
الملفات ، يمكنك تجربة التطبيق الخاص بك عن طريق تشغيل <code> npm start </ 0> من التطبيق الخاص بك
دليل.</p>

<p spaces-before="0"><strong x-id="1">Note</strong>: If you are building this project without downloading the example
repository, your <code>start` script in `package.json` should look like this



```json
  "scripts": {
    "start": "electron ."
  }
```




## جرب هذا المثال

استنساخ وتشغيل الكود في هذا البرنامج التعليمي باستخدام [`electron/electron-quick-start`](https://github.com/electron/electron-quick-start) repository.

**Note**: Running this requires [Git](https://git-scm.com) and [npm](https://www.npmjs.com/).



```sh
# Clone the repository
$ git clone https://github.com/electron/electron-quick-start
# Go into the repository
$ cd electron-quick-start
# Install dependencies
$ npm install
# Run the app
$ npm start
```


للحصول على قائمة بألواح الصفيح والأدوات لبدء عملية التطوير ، راجع [Boilerplates and CLIs documentation](./boilerplates-and-clis.md).
