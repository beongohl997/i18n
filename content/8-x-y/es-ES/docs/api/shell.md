# shell

> Administra los archivos y los URLs utilizando las aplicaciones por defecto.

Proceso: [Main](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)

El módulo `shell` proporciona las funciones relacionadas con la integración de escritorio.

Ejemplo de cómo abrir un URL en el navegador por defecto del usuario:

```javascript
const { shell } = require('electron')

shell.openExternal('https://github.com')
```

## Métodos

El módulo `shell` tiene los siguientes métodos:

### `shell.showItemInFolder(fullPath)`

* `fullPath` Cadena

Muestra el archivo dado en un gestor de archivos. Si es posible, seleccione el archivo.

### `shell.openItem(fullPath)`

* `fullPath` Cadena

Devuelve `Boolean` - Si el elemento se abrió o no con éxito.

Abre el archivo determinado en el escritorio por defecto.

### `shell.openExternal(url[, options])`

* `url` String - Máximo 2081 caracteres en windows.
* `options` Object (opcional)
  * `activate` Boolean (opcional) _macOs_ - `true` para llevar la aplicación abierta al primer plano. El valor por defecto es `true`.
  * `workingDirectory` String (opcional) _Windows_ - El directorio de trabajo.

Devuelve `Promise<void>`

Abre el protocolo URL externo dado de manera predeterminada en el escritorio. (Por ejemplo, mailto: URLs en el agente de correo predeterminado del usuario).

### `shell.moveItemToTrash(fullPath[, deleteOnFail])`

* `fullPath` Cadena
* `deleteOnFail` Boolean (opcional) - Si eliminar o no eliminar unilateralmente el elemento si la papelera está desactivada o no es soportada en el volumen. _macOS_

Retorna `Boolean` - Si el elemente fue satisfactoriamente movido a la papelera o por el contrario fue eliminado.

Mueve el archivo determinado a la papelera y devuelve un valor boleano para la operación.

### `shell.beep()`

Reproduce el sonido bip.

### `shell.writeShortcutLink(shortcutPath[, operation], options)` _Windows_

* `shortcutPath` String
* `operation` String (opcional) - El valor por defecto es `create`; puede ser uno de los siguientes:
  * `create` - Crea un nuevo acceso directo, sobrescribiendo si es necesario.
  * `update` - Actualiza las propiedades especificadas solo en un acceso directo existente.
  * `replace` - Sobrescribe un acceso directo existente. Y falla si el acceso directo no existe.
* `options` [ShortcutDetails](structures/shortcut-details.md)

Devuelve `Boolean` - Si el acceso directo fue creado con éxito.

Crea o actualiza un enlace de acceso directo a `shortcutPath`.

### `shell.readShortcutLink(shortcutPath)` _Windows_

* `shortcutPath` String

Devuelve [`ShortcutDetails`](structures/shortcut-details.md)

Resuelve el enlace de acceso directo a `shortcutPath`.

Aparecerá una excepción cuando ocurre cualquier error.
