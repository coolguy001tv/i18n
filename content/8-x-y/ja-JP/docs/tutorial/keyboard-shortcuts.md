# キーボードショートカット

> ローカルおよびグローバルなキーボードショートカットを設定します。

## ローカルショートカット

[Menu][] モジュールを使用して、アプリケーションにフォーカスがあるときにのみ起動されるキーボードショートカットを設定できます。 これをするには、[MenuItem][] の作成時に [`accelerator`][] プロパティを指定します。

```js
const { Menu, MenuItem } = require('electron')
const menu = new Menu()

menu.append(new MenuItem({
  label: 'Print',
  accelerator: 'CmdOrCtrl+P',
  click: () => { console.log('time to print stuff') }
}))
```

ユーザーのオペレーティングシステムに基づいて、さまざまなキーの組み合わせを構成できます。

```js
{
  accelerator: process.platform === 'darwin' ? 'Alt+Cmd+I' : 'Ctrl+Shift+I'
}
```

## グローバルショートカット

アプリケーションからキーボードのフォーカスが外れている場合でも、 [globalShortcut][] モジュールを使用すればキーボード操作を検出できます。

```js
const { app, globalShortcut } = require('electron')

app.on('ready', () => {
  globalShortcut.register('CommandOrControl+X', () => {
    console.log('CommandOrControl+X is pressed')
  })
})
```

## BrowserWindow 内のショートカット

[BrowserWindow][] のキーボードショートカットを処理する場合は、レンダラープロセス内のそのウィンドウオブジェクトの `keyup` と `keydown` イベントリスナーを使用できます。

```js
window.addEventListener('keyup', doSomething, true)
```

第 3 引数の `true` に注意してください。これは、このリスナーが常に他のリスナーより先にキー押下を受け取ります。そのため、`stopPropagation()` を呼び出さないでください。

[`before-input-event`](../api/web-contents.md#event-before-input-event) イベントは `keydown` イベントと `keyup` イベントをディスパッチするより前に発生します。 メニューに表示されないカスタムショートカットをキャッチして処理するため使用することができます。

実行したくない場合は[mousetrap][]などキーを検出高度なマニュアル ショートカット解析を行うライブラリがあります。

```js
Mousetrap.bind('4', () => { console.log('4') })
Mousetrap.bind('?', () => { console.log('show shortcuts!') })
Mousetrap.bind('esc', () => { console.log('escape') }, 'keyup')

// 組み合わせ
Mousetrap.bind('command+shift+k', () => { console.log('command shift k') })

// 複数の組み合わせを同じコールバックに割り当て
Mousetrap.bind(['command+k', 'ctrl+k'], () => {
  console.log('command k or control k')

  // false を返すと規定の動作を抑制してイベントの伝播を止める
  return false
})

// Gmail 式シーケンス
Mousetrap.bind('g i', () => { console.log('go to inbox') })
Mousetrap.bind('* a', () => { console.log('select all') })

// コナミコマンド!
Mousetrap.bind('up up down down left right left right b a enter', () => {
  console.log('コナミコマンド')
})
```

[Menu]: ../api/menu.md
[MenuItem]: ../api/menu-item.md
[globalShortcut]: ../api/global-shortcut.md
[`accelerator`]: ../api/accelerator.md
[BrowserWindow]: ../api/browser-window.md
[mousetrap]: https://github.com/ccampbell/mousetrap
