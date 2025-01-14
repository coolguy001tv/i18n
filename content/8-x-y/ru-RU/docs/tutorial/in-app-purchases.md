# Встроенные покупки (macOS)

## Подготовка

### Соглашение об оплаченных заявках
Если вы еще не подписали Соглашение об оплате заявок и настроили свою банковскую и налоговую информацию в iTunes Connect.

[iTunes Connect Developer Help: Соглашения, обзор по налогам и банкам](https://help.apple.com/itunes-connect/developer/#/devb6df5ee51)

### Создавайте покупки в приложении
Затем, вам нужно настроить покупки в приложениях в iTunes Connect, и включить такие сведения, как имя, Цены и описание, которое выделяет возможности и функциональность вашей покупки в приложении.

[iTunes Connect Developer Help: Создать покупку в приложении](https://help.apple.com/itunes-connect/developer/#/devae49fb316)

### Изменить CFBundleIdentifier

Для проверки встроенных покупок в разработке с Electron вам нужно изменить `CFBundleIdentifier` в `node_modules/electron/dist/Electron.app/Contents/Info.plist`. Вы должны заменить `com.github.electron` идентификатором приложения, созданного с помощью iTunes Connect.

```xml
<key>CFBundleIdentifier</key>
<string>com.example.app</string>
```

## Пример кода

Вот пример, который показывает, как использовать покупки в приложении Electron. Вы должны заменить идентификаторы продукта на идентификаторы продуктов, созданных с помощью iTunes Connect (идентификатор `ком. xample.app.product1` это `product1`). Обратите внимание, что вы должны как можно скорее прослушать `обновленные транзакции` события.

```javascript
const { inAppPurchase } = require('electron').remote
const PRODUCT_IDS = ['id1', 'id2']

// Слушайте транзакции как можно скорее.
inAppPurchase.on('transactions-updated', (event, transactions) => {
  if (!Array.isArray(transactions)) {
    return
  }

  // Проверять каждую транзакцию.
  transactions.forEach(function (transaction) {
    var payment = transaction.payment

    switch (transaction.transactionState) {
      case 'purchasing':
        console.log(`Purchasing ${payment.productIdentifier}...`)
        break
      case 'purchased':

        console.log(`${payment.productIdentifier} purchased.`)

        // Get the receipt url.
        let receiptURL = inAppPurchase.getReceiptURL()

        console.log(`Receipt URL: ${receiptURL}`)

        // Передайте файл чека серверу и проверьте, является ли он действительным.
        // @see https://developer.apple.com/library/content/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html
        // ...
        // Если квитанция действительна, продукт приобретается
        // ...

        // Завершение операции.
        inAppPurchase.finishTransactionByDate(transaction.transactionDate)

        break
      case 'failed':

        console.log(`Failed to purchase ${payment.productIdentifier}.`)

        // Завершение транзакции.
        inAppPurchase.finishTransactionByDate(транзакция). ransactionDate)

        break
      case 'restored':

        консоль. og(`Покупка ${payment.productIdentifier} была восстановлена. )

        break
      регистр 'deferred':

        консоль. og(`Куплено ${payment.productIdentifier} было отложено. )

        перерыв
      по умолчанию:
        перерыв
    }
  })
})

// Проверьте, разрешено ли пользователю совершать покупку внутри приложения.
if (!inAppPurchase.canMakePayments()) {
  console.log('The user is not allowed to make in-app purchase.')
}

// Retrieve and display the product descriptions.
inAppPurchase.getProducts(PRODUCT_IDS).then(products => {
  // Проверьте параметры.
  if (!Array.isArray(products) || products.length <= 0) {
    console.log('Unable to retrieve the product informations.')
    return
  }

  // Display the name and price of each product.
  products.forEach(product => {
    console.log(`Цена ${product.localizedTitle} составляет ${product.formattedPrice}.`)
  })

  // Спросите пользователя, какой продукт он/она хочет купить.
  let selectedProduct = products[0]
  let selectedQuantity = 1

  // Купить выбранный продукт.
  inAppPurchase.purchaseProduct(selectedProduct.productIdentifier, selectedQuantity).then(isProductValid => {
    if (!isProductValid) {
      console.log('The product is not valid.')
      return
    }

    console.log('The payment has been added to the payment queue.')
  })
})
```
