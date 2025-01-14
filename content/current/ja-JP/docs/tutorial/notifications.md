# 通知 (Windows、Linux、macOS)

## 概要

3つのオペレーティングシステムはすべて、アプリケーションが 通知をユーザーに送信する手段を提供します。 通知を表示する方法は Main と Renderer のプロセスでは と異なります。

For the Renderer process, Electron conveniently allows developers to send notifications with the [HTML5 Notification API](https://notifications.spec.whatwg.org/), using the currently running operating system's native notification APIs to display it.

メインプロセスに通知を表示するには、 [通知](../api/notification.md) モジュールを使用する必要があります。

## サンプル

### レンダラープロセスに通知を表示する

Assuming you have a working Electron application from the [Quick Start Guide](quick-start.md), add the following line to the `index.html` file before the closing `</body>` tag:

```html
<script src="renderer.js"></script>
```

`renderer.js` ファイルを追加します。

```js
const myNotification = new Notification('Title', {
  body: 'Notification from the Renderer process'
})

myNotification.onclick = () => {
  console.log('Notification clicked')
}
```

Electron アプリケーションを起動すると、通知が表示されます。

![レンダラープロセスの通知](../images/notification-renderer.png)

If you open the Console and then click the notification, you will see the message that was generated after triggering the `onclick` event:

![通知のオンクリックメッセージ](../images/message-notification-renderer.png)

### メインプロセスに通知を表示する

[クイックスタートガイド](quick-start.md)の動作アプリケーションから始めて、 `main.js` ファイルを次の行で更新します。

```js
const { Notification } = require('electron')

function showNotification () {
  const notification = {
    title: 'Basic Notification',
    body: 'Notification from the Main process'
  }
  new Notification(notification).show()
}

app.whenReady().then(createWindow).then(showNotification)
```

Electron アプリケーションを起動すると、通知が表示されます。

![メインプロセスでの通知](../images/notification-main.png)

## 追加情報

オペレーティングシステム間でのコードとユーザエクスペリエンスは似ていますが、微妙な違いがあります。

### Windows

* Windows 10 では、 [アプリケーション ユーザー モデル ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx) がインストールされている必要があります スタート メニュー. これは開発中にオーバーキルすることができるので、 `node_modules\electron\dist\electron.exe` を トリックに追加することもできます。 エクスプローラーでそのファイルを開き、右クリックして 'スタート メニューにピン留めする' を選択します。 You will then need to add the line `app.setAppUserModelId(process.execPath)` to your main process to see notifications.
* Windows 8.1 と Windows 8 では、スタート画面に [アプリケーションユーザーモデル ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx) でアプリへのショートカットをインストールしなければなりません。 注釈: ただし、スタート画面にピン留めする必要はありません。
* Windows 7 では、通知はカスタム実装を介して動作します。これは新しいシステムのネイティブのものと似た見た目になります。

Electronは、アプリケーションユーザーモデル ID の作業を自動化しようとしています。 Electron をインストール&アップデートフレームワーク Squirrel と共に使用すると、[ショートカットが自動的に正しく設定されます](https://github.com/electron/windows-installer/blob/master/README.md#handling-squirrel-events)。 さらに、Electron は Squirrel が使用されたことを検出し、正しい値を指定して自動的に `app.setAppUserModelId()` を呼び出します。 開発中では、[`app.setAppUserModelId()`](../api/app.md#appsetappusermodelidid-windows) を自身で呼び出す必要があります。

さらに、Windows 8では、通知本体の最大長は250文字で、Windowsチームは通知を200文字にすることを推奨しています。 この制限は Windows 10では削除されており、これは Windows チームは合理的にするために開発者の意見を聞いているということです。 巨大な量のテキスト (数千文字) を API に送信しようとすると、不安定になる可能性があります。

#### 高度な通知

最近のバージョンの Windows では、カスタムテンプレート、イメージ、その他の柔軟な要素を使用した高度な通知が可能です。 これらの通知を (メインプロセスやレンダラープロセスから) 送信するには、[electron-windows-notification](https://github.com/felixrieseberg/electron-windows-notifications) ユーザーランドモジュールを使用します。これは、`ToastNotification` と `TileNotification` オブジェクトを送るネイティブ Node アドオンです。

While notifications including buttons work with `electron-windows-notifications`, handling replies requires the use of [`electron-windows-interactive-notifications`](https://github.com/felixrieseberg/electron-windows-interactive-notifications), which helps with registering the required COM components and calling your Electron app with the entered user data.

#### 非通知 / プレゼンテーションモード

通知の送信を許可されているかどうかを検出するには、 userland モジュール [electron-notification-state](https://github.com/felixrieseberg/electron-notification-state) を使用します。

これにより、Windowsが 通知を黙って投げるかどうかを事前に判断することができます。

### macOS

macOS 上での通知は簡単ですが、[通知に関する Apple のヒューマンインタフェースガイドライン](https://developer.apple.com/macos/human-interface-guidelines/system-capabilities/notifications/) を理解しておく必要があります。

通知サイズは256バイトに制限されており、その制限を超えると切り捨てられることに注意してください。

#### 高度な通知

macOS の以降のバージョンでは、ユーザがすぐに通知に返信できるように、入力フィールドつきの通知を利用できます。 入力フィールドつきの通知を送信するためには、[node-mac-notifier](https://github.com/CharlieHess/node-mac-notifier) ユーザーランドモジュールを使用します。

#### おやすみモード / セッションステート

通知を送信することが許可されているかどうかを検出するには、[electron-notification-state](https://github.com/felixrieseberg/electron-notification-state) ユーザーランドモジュールを使用します。

これにより、通知が表示されるかどうかを事前に検出することができます。

### Linux

通知は、[デスクトップ通知仕様](https://developer.gnome.org/notification-spec/) (Cinnamon、Enlightenment、Unity、GNOME、KDE) に従ってデスクトップ環境の通知を表示できる `libnotify` を使用して送信されます。
