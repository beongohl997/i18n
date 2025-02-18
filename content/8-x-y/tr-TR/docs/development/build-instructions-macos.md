# İnşaa Talimatları (macOS)

Electron'u macOS üzerinde kurmak için aşağıdaki yönergeleri takip edin.

## Ön gereklilikler

* macOS >= 10.11.6
* [Xcode](https://developer.apple.com/technologies/tools/) >= 9.0.0
* [node.js](https://nodejs.org) (harici)
* Python 2.7, TLS 1.2 desteği ile

## Python

Please also ensure that your system and Python version support at least TLS 1.2. This depends on both your version of macOS and Python. For a quick test, run:

```sh
$ npx @electron/check-python-tls
```

If the script returns that your configuration is using an outdated security protocol, you can either update macOS to High Sierra or install a new version of Python 2.7.x. To upgrade Python, use [Homebrew](https://brew.sh/):

```sh
$ brew install python@2 && brew link python@2 --force
```

Homebrew tarafından sağlanan Python kullanıyorsanız, ayrıca aşağıdaki Python modüllerini kurmanız gerekmektedir:

* [pyobjc](https://pypi.org/project/pyobjc/#description)

Yüklemek için `pip` kullanabilirsiniz:

```sh
$ pip pyobjc yükle
```

## macOS SDK

Electron geliştiriyor ve özel Electron derlemenizi dağıtmak gibi bir planınız yoksa bu bölümü atlayabilirsiniz.

Resmi Electron sürümleri'ni oluştururken MacOS 10.13 SDK tabanı üzerinde [Xcode 9.4.1](http://adcdownload.apple.com/Developer_Tools/Xcode_9.4.1/Xcode_9.4.1.xip) kullanılmaktadır.  Yeni bir SDK ile sistem çok çalışır, ama sürümler şu anda 10,13 SDK'yı kullanıyor.

## Electron'u Derlemek

Bakmak [Derleme Komutları: GN](build-instructions-gn.md).
