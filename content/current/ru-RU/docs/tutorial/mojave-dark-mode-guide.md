# Поддержка Темного режима macOS

В macOS 10.14 Mojave, Apple представила новый [системный темный режим](https://developer.apple.com/design/human-interface-guidelines/macos/visual-design/dark-mode/) для всех компьютеров macOS.  Если у вашего Electron есть темный режим, вы можете сделать это следуйте настройкам общесистемного режима с помощью [ `родной темы` api](../api/native-theme.md).

В macOS 10.15 Catalina, Apple представила новую "автоматическую" опцию темного режима для всех компьютеров macOS. Для `родной темы. houldUseDarkColors` и `Tray` API для корректной работы в этом режиме на Катализация, у вас должно быть `NSRequiresAquaSystemAppear` значение `false` в вашей информации `. list` file, or be on Electron `>=7.0.0`. Оба [пакета Electron](https://github.com/electron/electron-packager) и [Electron Forge](https://www.electronforge.io/) имеют опцию [`darwinDarkModeПоддержка`](https://electron.github.io/electron-packager/master/interfaces/electronpackager.options.html#darwindarkmodesupport) для автоматизации `Информации. список` изменений во время сборки приложения.

## Автоматическое обновление нативных интерфейсов

"Родной интерфейсы" включают средство выбора файлов, границу окна, диалоги, контекстное меню и многое другое; В основном, - все, что приходит из macOS и не из вашего приложения. Начиная с версии 7.0.0 поведение по умолчанию состоит в том, чтобы выбрать эту автоматическую тему из ОС. Если вы хотите отказаться от Electron
&gt; 8.0. , вы должны установить ключ `NSRequiresAquaSystemAppearance` в файле `Info.plist` значение `true`. Обратите внимание, что Electron 8.0.0 и выше не позволит вам отказаться от этой темы из-за использования macOS 10.14 SDK.

## Автоматическое обновление ваших интерфейсов

Если ваше приложение имеет свой темный режим, вы должны включить и выключить его в системном тёмном режиме. Вы можете сделать это, прослушивая тему обновленное событие в модуле `родной темы`.

Например:

```javascript
const { nativeTheme } = require('electron')

nativeTheme.on('updated', function theThemeHasChanged () {
  updateMyAppTheme(nativeTheme.shouldUseDarkColors)
})
```
