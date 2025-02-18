# Depuración en macOS

Si usted tiene bloqueos o problemas en Electron que cree que no son causados ​​por la aplicación de JavaScript, sino por Electron, la depuración puede ser un poco difícil, especialmente para desarrolladores que no están acostumbrados a la depuración nativa/C++. Sin embargo, usando lldb y el código fuente de Electron, puede habilitar la depuración paso a paso con puntos de interrupción dentro del código fuente de Electron. También puede usar [XCode for debugging](debugging-instructions-macos-xcode.md) si prefiere una interfaz gráfica.

## Requisitos

* **Construcción de una depuración de Electron**: La manera más fácil usualmente es construirlo usted mismo, usando las herramientas y prerrequisitos especificados en el [Instrucciones de construcción para macOS](build-instructions-macos.md). Aunque puede adjuntar y depurar Electrón a medida que lo descarga directamente, encontrará que está muy optimizado, lo que dificulta considerablemente la depuración: el depurador no podrá mostrarle el contenido de todas las variables y la ruta de ejecución puede parecer extraña debido a las llamadas en línea, las llamadas de cola y otras optimizaciones del compilador.

* **Código X**: adicional al código X, también instala las herramientas del comando de linea de este. Incluyen LLDB, el depurador predeterminado en Xcode en macOS. Admite depuración de C, Objective-C y C++ en el escritorio y los dispositivos y simuladores iOS.

* **.lldbinit**: Crear o editar `~/.lldbinit` para permitir que el código de Chromium sea correctamente mapeado de fuentes.
   ```text
   importar script ~/electron/src/tools/lldb/lldbinit.py
   ```

## A y depuración Electron

Para empezar una sesión de depuración, abra el terminal e inicie `lldb`, pasando al constructo de depuración de Electron como un parámetro.

```sh
$ lldb ./out/Testing/Electron.app
(lldb) target create "./out/Testing/Electron.app"
Ejecutable actual establecido en './out/Testing/Electron.app' (x86_64).
```

### Establecer puntos de interrupción

LLDB es una herramienta poderosa y soporta múltiples estrategias para la inspección de código. Para esta instrucción básica, asumamos que está llamando un comando para JavaScript que no se está comportando correctamente - así que a usted le gustaría separarlo en la contraparte de esos comandos en C++ dentro de la fuente Electron.

Relevant code files can be found in `./shell/`.

Asumamos que quiere depurar `app.setName()`, el cual está definido en `browser.cc` como `Browser::SetName()`. Configure un punto de separación usando el comando `breakpoint`, especificando el archivo y la linea que quiere separar:

```sh
(lldb) breakpoint set --file browser.cc --line 117
Breakpoint 1: where = Electron Framework`atom::Browser::SetName(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) + 20 at browser.cc:118, address = 0x000000000015fdb4
```

Luego, inicie Electron:

```sh
(lldb) run
```

La aplicación será pausada inmediatamente, mientras Electron configura el nombre de esta en el lanzador:

```sh
(lldb) run
Process 25244 launched: '/Users/fr/Code/electron/out/Testing/Electron.app/Contents/MacOS/Electron' (x86_64)
Process 25244 stopped
* thread #1: tid = 0x839a4c, 0x0000000100162db4 Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 20 at browser.cc:118, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
    frame #0: 0x0000000100162db4 Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 20 at browser.cc:118
   115  }
   116
   117  void Browser::SetName(const std::string& name) {
-> 118    name_override_ = name;
   119  }
   120
   121  int Browser::GetBadgeCount() {
(lldb)
```

Para mostrar los argumentos y las variables locales para la estructura actual, corra `variable de estructura` (o `fr v`), que le mostrará que la aplicación está siendo configurada con el nombre de "Electron".

```sh
(lldb) frame variable
(atom::Browser *) this = 0x0000000108b14f20
(const string &) name = "Electron": {
    [...]
}
 
Context | Edit Context
```

Para hacer un solo paso en el nivel de la fuente en el hilo seleccionado, ejecute `paso` (o `s`). Esto lo llevará dentro de `name_override_.empty()`. Para proceder y dar otro paso, corra `siguiente` (o `n`).

```sh
(lldb) step
Process 25244 stopped
* thread #1: tid = 0x839a4c, 0x0000000100162dcc Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 44 at browser.cc:119, queue = 'com.apple.main-thread', stop reason = step in
    frame #0: 0x0000000100162dcc Electron Framework`atom::Browser::SetName(this=0x0000000108b14f20, name="Electron") + 44 at browser.cc:119
   116
   117  void Browser::SetName(const std::string& name) {
   118    name_override_ = name;
-> 119  }
   120
   121  int Browser::GetBadgeCount() {
   122    return badge_count_;
```

**NOTE:** If you don't see source code when you think you should, you may not have added the `~/.lldbinit` file above.

Para finalizar la depuración en este punto, corra `continuar proceso`. También puede continuar hasta cierta linea es tocada en este hilo (`hilo hasta 100`). Este comando correrá el hilo en la estructura actual hasta que alcance la linea 100 en este, o se detiene si deja la estructura en la que se encuentra.

Ahora, si abre las herramientas para desarrolladores y llama `setName`, alcanzará de nuevo el punto de interrupción.

### Leer más
LLDB es una herramienta poderosa con una gran documentación. Para aprender más acerca de ella, tome en consideración la documentación sobre la depuración de Apple, La [La documentación sobre la estructura de referencia de LLDB](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-basics.html#//apple_ref/doc/uid/TP40012917-CH2-SW2) o la introducción a [usar LLDB como un depurador independiente](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-terminal-workflow-tutorial.html).

También puede consultar el fantástico [manual y tutorial](http://lldb.llvm.org/tutorial.html) de LLDB, que explicará escenarios de depuración más complejos.
