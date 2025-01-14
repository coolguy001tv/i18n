# Detecção de Evento Online/Offline

[A detecção de eventos online e offline](https://developer.mozilla.org/en-US/docs/Online_and_offline_events) pode ser implementada no processo de renderização usando o [`navegador. nLine`](http://html5index.org/Offline%20-%20NavigatorOnLine.html) atributo, parte da API padrão HTML5. O atributo</code> navigator.onLine `retorna <code>falso` se algum pedido de rede for garantido que falhe, ou seja, definitivamente offline (desconectado da rede). Ele retorna `verdadeiro` em todos os outros casos. Uma vez que todas as outras condições retornam `verdadeiro`, é preciso estar atento a receber falsos positivos, já que não podemos assumir um valor `verdadeiro` necessariamente significa que o Electron pode acessar a internet. Such as in cases where the computer is running a virtualization software that has virtual ethernet adapters that are always “connected.” Therefore, if you really want to determine the internet access status of Electron, you should develop additional means for checking.

Exemplo:

_main.js_

```javascript
const { app, BrowserWindow } = require('electron')

let onlineStatusWindow

app.on('ready', () => {
  onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
  onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
})
```

_online-status.html_

```html
<!DOCTYPE html>
<html>
<body>
<script>
  const alertOnlineStatus = () => {
    window.alert(navigator.onLine ? 'online' : 'offline')
  }

  window.addEventListener('online', alertOnlineStatus)
  window.addEventListener('offline', alertOnlineStatus)

  alertOnlineStatus()
</script>
</body>
</html>
```

Pode haver instâncias onde você deseja responder a esses eventos no processo principal também. O processo principal, no entanto, não tem um objeto `navigator` e portanto não pode detectar esses eventos diretamente. Usando os utilitários de comunicação entre processos do Electron, os eventos podem ser encaminhados para o processo principal e tratados conforme necessário, conforme mostrado no exemplo a seguir.

_main.js_

```javascript
const { app, BrowserWindow, ipcMain } = require('electron')
let onlineStatusWindow

app.on('ready', () => {
  onlineStatusWindow = new BrowserWindow({ width: 0, height: 0, show: false })
  onlineStatusWindow.loadURL(`file://${__dirname}/online-status.html`)
})

ipcMain.on('online-status-changed', (event, status) => {
  console.log(status)
})
```

_online-status.html_

```html
<! OCTYPE html>
<html>
<body>
<script>
  const { ipcRenderer } = require('electron')
  const updateOnlineStatus = () => {
    ipcRenderer. end('online-status-changed', navigator.onLine ? 'online' : 'offline')
  }

  window.addEventListener('online', updateOnlineStatus)
  window.addEventListener('offline', updateOnlineStatus)

  updateOnlineStatus()
</script>
</body>
</html>
```
