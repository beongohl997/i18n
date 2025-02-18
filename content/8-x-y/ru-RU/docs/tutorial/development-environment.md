# Среда разработчика

Разработка на Electron, по сути, является разработкой на Node.js. Что бы превратить вашу операционную систему в окружение для создания Electron приложений, вам необходим Node.js, npm, любой редактор кода, и базовые знания работы с командной строкой вашей системы.

## Настройка macOS

> Electon поддерживает Mac Os 10.10 (Yosemite) и выше. Обычно, компания Apple не позволяет запускать macOS на виртуальных машинах, если ваш компьютер не является компьютером Apple. Если вам необходим Mac, то вы можете найти инструкции в сети как запустить MacOS в виртуальной машине или использовать облачные сервисы, которые позволяют получить доступ к  компьютерам на MacOS  (например [MacInCloud][macincloud] или [Xcloud](https://xcloud.me)).

Первым делом нужно установить последнюю версию Node.js. Мы рекомендуем устанавливать последнюю `LTS` или `Current` доступную версию. Посетите [страницу загрузки Node.js][node-download] и выберите `macOS установщик`. Скорее всего для установки вам предложат Homebrew, но мы его не рекомендуем - многие инструменты не будут работать с Node.js установленным через Homebrew.

После скачивания, запустите установщик, и следуйте инструкциям.

После установки, убедитесь в том, что все работает так, как надо. Найдите утилиту `Terminal` в папке `/Applications/Utilities` (или ищите слово `Terminal` в Spotlight). Откройте `Terminal` или другой клиент командной строки и убедитесь в том, что `node` и `npm` установлены:

```sh
# Эта команда должны вывести текущую версию Node.js
node -v

# Эта команда должны вывести текущую версию npm
npm -v
```

Если после выполнения команд выше, вы увидели версию Node.js и npm, то все готово! Перед тем, как начать, было бы неплохо установить [редактор кода](#a-good-editor), подходящий для разработки на JavaScript.

## Настройка Windows

> Electron поддерживает Windows 7 и выше - разрабатывать Electron приложения на предыдущих версиях Windows не получится. Microsoft бесплатно предоставляeт  [виртуальные машины с Windows 10][windows-vm] для разработчиков.

Первым делом нужно установить последнюю версию Node.js. Мы рекомендуем Вам установить последнюю доступную `LTS` или `Current` версию. Перейдите на [страницу загрузки Node.js][node-download], и выберите `Windows установщик`. После скачивания, запустите установщик, и следуйте инструкциям.

На экране, который позволяет настроить установку, убедитесь, что на опциях `Node.js runtime`, `npm package manager`, и `Add to PATH` установлен флажок.

После установки, убедитесь в том, что все работает так, как надо. Найдите Windows PowerShell, открыв меню "Пуск" и набрав `PowerShell`. Откройте `PowerShell` или другой клиент командной строки и убедитесь в том, что `node` и `npm` установлены:

```powershell
# Эта команда должны вывести текущую версию Node.js
node -v

# Эта команда должны вывести текущую версию npm
npm -v
```

Если после выполнения команд выше, вы увидели версию Node.js и npm, то все готово! Перед тем, как начать, было бы неплохо установить [редактор кода](#a-good-editor), подходящий для разработки на JavaScript.

## Настройка Linux

> В целом, Electron поддерживает Ubuntu 12.04, Fedora 21, Debian 8 и более поздние версии.

Первым делом нужно установить последнюю версию Node.js. В зависимости от вашего Linux дистрибутива, процесс установки может немного отличаться. Если вы устанавливаете программы используя пакетные менеджеры, такие как `apt` или `pacman`, то воспользуйтесь оффициальным [руководством по установке Node.js на Linux][node-package].

Если вы пользователь Linux, то вы скорее всего знаете, как работать с командной строкой. Откройте ваш любимый клиент командной строки и убедитесь, что `node`, а также `npm` установленны глобально:

```sh
# Эта команда должны вывести текущую версию Node.js
node -v

# Эта команда должны вывести текущую версию npm
npm -v
```

Если после выполнения команд выше, вы увидели версию Node.js и npm, то все готово! Перед тем, как начать, было бы неплохо установить [редактор кода](#a-good-editor), подходящий для разработки на JavaScript.

## Хороший редактор кода

Мы предлагаем вам выбрать один из двух популярных редакторов:  [Atom][atom] и Microsoft's [Visual Studio Code][code]. Оба идеальны для JavaScript.

Если вы являетесь одним из многих разработчиков с "повышенными требованиями", то знайте, что в наши дни практически все редакторы кода и IDE поддерживают JavaScript.

[macincloud]: https://www.macincloud.com/
[node-download]: https://nodejs.org/en/download/
[node-package]: https://nodejs.org/en/download/package-manager/
[atom]: https://atom.io/
[code]: https://code.visualstudio.com/
[windows-vm]: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines
