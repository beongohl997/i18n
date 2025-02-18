# Руководство по стилю документации Electron

Это руководство для написания Electron документации.

## Названия

* Каждая страница должна иметь один заголовок `#` в верхней части.
* Главы на одной странице должны иметь `##` - уровень названий.
* В подзаголовках необходимо увеличить количество `#` в названии согласно их вложенной глубине.
* Все слова в названии страницы должны быть заглавными, за исключением союзов как «из» и «и».
* Только первое слово название главы должны быть заглавными.

Используйте `Быстрый старт` как пример:

```markdown
# Quick Start

...

## Main process

...

## Renderer process

...

## Run your app

...

### Run as a distribution

...

### Manually downloaded Electron binary

...
```

Для ссылок на API есть исключения из этого правила.

## Markdown правила

* Используйте `sh` вместо `cmd` в блоках кода (из-за синтаксической подсветки).
* Строки должны быть ограничены в 80 столбцов.
* Не делать вложенные списки более чем 2 уровня (из-за markdown отображения).
* Все блоки кода `js` и `javascript` проверяются линтером по [standard-markdown](http://npm.im/standard-markdown).

## Выбор слов

* Используйте "будет" вместо "был бы" при описании результатов.
* Предпочитайте "в ___ процессе" чем "в".

## Справочник по API

Следующие правила применяются только к документации по API.

### Название страницы

Каждая страница должна использовать имя фактического объекта, возвращенное `require('electron')` как название, такие как `BrowserWindow`, `autoUpdater` и `session`.

Под названием страницы должно быть однострочное описание, начинающееся с `>`.

Используйте `session` как пример:

```markdown
# session

> Управление сеансами браузера, куками, кешем, настройками прокси и т.д.
```

### Методы и события модуля

Для модулей, которые не являются классами, методы и события должны быть перечислены под главами `## Методы` и `## События` .

Используйте `autoUpdater` как пример:

```markdown
# autoUpdater

## События 

### Событие: 'error' 

## Методы 

### `autoUpdater.setFeedURL(url[, requestHeaders])`
```

### Классы

* Классы API или классы, которые являются частью модулей должны быть перечислены под главой `## Класс: НазваниеКласса`.
* Одна страница может иметь несколько классов.
* Конструкторы должны быть перечислены с `###`-уровнем названия.
* [Статические методы](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/static) должны быть перечислены под главой `### Статические методы`.
* [Методы экземпляра](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes#Prototype_methods) должны быть перечислены под главой `### Методы экземпляра`.
* All methods that have a return value must start their description with "Returns `[TYPE]` - Return description"
  * Если метод возвращает `Object`, его структуру можно указать с помощью двоеточия, следующие строки в виде неупорядоченного списка свойств в том же стиле параметров функции.
* События экземпляров, должны быть перечислены под главой `### События экземпляра`.
* Instance Properties must be listed under an `### Instance Properties` chapter.
  * Свойства экземпляра должно начинаться с "[Тип Свойства] ..."

Используйте классы `Session` и `Cookies` в качестве примера:

```markdown
# session

## Методы

### session.fromPartition(partition)

## Статические свойства

### session.defaultSession

## Class: Session

### События экземпляра

#### Event: 'will-download'

### Методы экземпляра

#### `ses.getCacheSize()`

### Свойства экземпляра

#### `ses.cookies`

## Class: Cookies

### Методы экземпляра

#### `cookies.get(filter, callback)`
```

### Методы

Методы главы должны быть в следующем виде:

```markdown
### `objectName.methodName(required[, optional]))`

* `required` String - A parameter description.
* `optional` Integer (optional) - Another parameter description.

...
```

Название может быть`###` или`####`-уровня в зависимости от того, является ли этот метод модуля или класса.

For modules, the `objectName` is the module's name. For classes, it must be the name of the instance of the class, and must not be the same as the module's name.

Например, методы класса `Session` под модулем `session` должны использовать `ses` как `objectName`.

Необязательные аргументы нотированы в квадратные скобки `[]`, окружают необязательный аргумент также запятая, если за необязательным аргументом следует еще один аргумент:

```sh
required[, optional]
```

Below the method is more detailed information on each of the arguments. The type of argument is notated by either the common types:

* [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
* [`Number`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
* [`Object`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)
* [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
* [`Boolean`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)
* Или пользовательский тип, например [`WebContent`](api/web-contents.md) Electron'а

Если аргумент или метод является уникальным для определенных платформ, эти платформы обозначаются списком, разделенных пробелами, после типа данных. Значения могут быть `macOS`, `Windows` или `Linux`.

```markdown
* `animate` Boolean (по усмотрению) _macOS_ _Windows_ - анимировать вещь.
```

Аргументы типа `Array` должны указывать, какие элементы может включать массив в описание ниже.

Описание аргументов типа `Function` должно дать понять, как это может быть вызвано и перечислить типы параметров, которые будут переданы ему.

### События

Глава события должна быть в следующем виде:

```markdown
### Событие: 'wake-up'

Возвращает:

* `time` String

...
```

Название может быть `###` или `####`-уровня в зависимости от того, является ли это событие модуля или класса.

Аргументы события следуют тем же правилам методов.

### Свойства

Глава свойства должна быть в следующем виде:

```markdown
### session.defaultSession

...
```

Название может быть `###` или `####`-уровня в зависимости от того, является ли это свойство модуля или класса.

## Переводы документации

См. [electron/i18n](https://github.com/electron/i18n#readme)
