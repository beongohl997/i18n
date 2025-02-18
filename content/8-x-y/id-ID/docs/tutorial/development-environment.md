# Lingkungan pengembang

Pengembangan elektron pada dasarnya adalah pengembangan node. js. Untuk mengubah sistem operasi milikmu untuk membangun sebuah aplikasi menggunakan Electron, kamu hanya memerlukan Node.js, npm, kode editor, dan pemahaman dasar baris perintah (command line client) sistem operasi milikmu.

## Menyiapkan di macOS

> Electron mendukung macOS versi 10,10 (Yosemite) hingga yang terbaru. Apple does not allow running macOS in virtual machines unless the host computer is already an Apple computer, so if you find yourself in need of a Mac, consider using a cloud service that rents access to Macs (like [MacInCloud][macincloud] or [xcloud](https://xcloud.me)).

Pertama, pasang Node.js versi terbaru. Kami merekomendasikan untuk memasang versi `LTS` terkini atau versi `Current` yang tersedia sekarang. Visit [the Node.js download page][node-download] and select the `macOS Installer`. While Homebrew is an offered option, but we recommend against it - many tools will be incompatible with the way Homebrew installs Node.js.

Setelah diunduh, jalankan installer dan ikuti panduan untuk pemasangannya.

Once installed, confirm that everything works as expected. Find the macOS `Terminal` application in your `/Applications/Utilities` folder (or by searching for the word `Terminal` in Spotlight). Open up `Terminal` or another command line client of your choice and confirm that both `node` and `npm` are available:

```sh
# This command should print the version of Node.js
node -v

# This command should print the version of npm
npm -v
```

If both commands printed a version number, you are all set! Before you get started, you might want to install a [code editor](#a-good-editor) suited for JavaScript development.

## Menyiapkan Windows

> Electron supports Windows 7 and later versions – attempting to develop Electron applications on earlier versions of Windows will not work. Microsoft provides free [virtual machine images with Windows 10][windows-vm] for developers.

Pertama, pasang Node.js versi terbaru. Kami merekomendasikan untuk memasang versi `LTS` terkini atau versi `Current` yang tersedia sekarang. Kunjungi [halaman unduh Node.js][node-download] dan pilih `Windows Installer`. Setelah diunduh, jalankan installer dan ikuti panduan untuk pemasangannya.

On the screen that allows you to configure the installation, make sure to select the `Node.js runtime`, `npm package manager`, and `Add to PATH` options.

Once installed, confirm that everything works as expected. Find the Windows PowerShell by opening the Start Menu and typing `PowerShell`. Open up `PowerShell` or another command line client of your choice and confirm that both `node` and `npm` are available:

```powershell
# This command should print the version of Node.js
node -v

# This command should print the version of npm
npm -v
```

If both commands printed a version number, you are all set! Before you get started, you might want to install a [code editor](#a-good-editor) suited for JavaScript development.

## Menyiapkan Linux

> Generally speaking, Electron supports Ubuntu 12.04, Fedora 21, Debian 8 and later.

Pertama, pasang Node.js versi terbaru. Depending on your Linux distribution, the installation steps might differ. Assuming that you normally install software using a package manager like `apt` or `pacman`, use the official [Node.js guidance on installing on Linux][node-package].

You're running Linux, so you likely already know how to operate a command line client. Open up your favorite client and confirm that both `node` and `npm` are available globally:

```sh
# This command should print the version of Node.js
node -v

# This command should print the version of npm
npm -v
```

If both commands printed a version number, you are all set! Before you get started, you might want to install a [code editor](#a-good-editor) suited for JavaScript development.

## A Good Editor

We might suggest two free popular editors built in Electron: GitHub's [Atom][atom] and Microsoft's [Visual Studio Code][code]. Both of them have excellent JavaScript support.

If you are one of the many developers with a strong preference, know that virtually all code editors and IDEs these days support JavaScript.

[macincloud]: https://www.macincloud.com/
[node-download]: https://nodejs.org/en/download/
[node-download]: https://nodejs.org/en/download/
[node-package]: https://nodejs.org/en/download/package-manager/
[atom]: https://atom.io/
[code]: https://code.visualstudio.com/
[windows-vm]: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines
