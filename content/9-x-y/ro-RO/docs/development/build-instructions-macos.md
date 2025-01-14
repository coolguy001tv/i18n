# Build Instructions (macOS)

Follow the guidelines below for building Electron on macOS.

## Cerințe preliminare

* macOS >= 10.11.6
* [Xcode](https://developer.apple.com/technologies/tools/) >= 9.0.0
* [node.js](https://nodejs.org) (external)
* Python 2.7 with support for TLS 1.2

## Python

Please also ensure that your system and Python version support at least TLS 1.2. This depends on both your version of macOS and Python. For a quick test, run:

```sh
$ npx @electron/check-python-tls
```

If the script returns that your configuration is using an outdated security protocol, you can either update macOS to High Sierra or install a new version of Python 2.7.x. To upgrade Python, use [Homebrew](https://brew.sh/):

```sh
$ brew install python@2 && brew link python@2 --force
```

If you are using Python as provided by Homebrew, you also need to install the following Python modules:

* [pyobjc](https://pypi.org/project/pyobjc/#description)

You can use `pip` to install it:

```sh
$ pip install pyobjc
```

## SDK macOS

Dacă dezvoltați Electron și nu intenționați să redistribuiți Electron personalizat construit, puteți sări peste această secțiune.

Versiunile Oficiale Electron sunt construite cu [Xcode 9.4.1](http://adcdownload.apple.com/Developer_Tools/Xcode_9.4.1/Xcode_9.4.1.xip), și macOS 10.13 SDK.  Clădirea cu un SDK mai nou funcţionează, dar versiunile folosesc în prezent SDK 10.13.

## Construirea Electron

Vezi [Instrucțiuni de construcție: GN](build-instructions-gn.md).
