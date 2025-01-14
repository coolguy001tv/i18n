# Electron Application Architecture

Before we can dive into Electron's APIs, we need to discuss the two process types available in Electron. They are fundamentally different and important to understand.

## العمليات الرئيسية والرابط

في إلكترون ، العملية التي تشغل `package.json`هو `الرئيسي` البرنامج النصي __العملية الرئيسية__. البرنامج النصي الذي يعمل في العملية الرئيسية يمكن أن يعرض واجهة المستخدم عن طريق إنشاء صفحات الويب. تطبيق إلكترون لديه دائما عملية رئيسية واحدة، ولكن لا أكثر من ذلك.

بما أن إلكترون يستخدم Chromium لعرض صفحات الويب، يتم أيضا استخدام هندسة المعالجة المتعددة في Chromium. تعمل كل صفحة ويب في إلكترون في العملية الخاصة بها، والتي تسمى __عملية العرض__.

In normal browsers, web pages usually run in a sandboxed environment and are not allowed access to native resources. Electron users, however, have the power to use Node.js APIs in web pages allowing lower level operating system interactions.

### الاختلافات بين العملية الرئيسية وعملية العارض

العملية الرئيسية تنشئ صفحات ويب عن طريق إنشاء مثيلات `متصفح النافذة`. كل مثيل `نافذة المتصفح` يدير صفحة الويب في عملية العارض الخاصة به. عندما يتم تدمير مثيل `نافذة المتصفح` ، يتم أيضا إنهاء عملية العارض المناظرة .

العملية الرئيسية تدير جميع صفحات الويب وعمليات المعرض المقابلة لها . كل عملية من عمليات العارض معزولة و تهتم فقط بصفحة الويب قيد التشغيل.

في صفحات الويب، مكالمة واجهة برمجة تطبيقات واجهة المستخدم الأصلية ذات الصلة غير مسموح بها لأن إدارة موارد واجهة المستخدم الأصلية في صفحات الويب خطيرة جدا ومن السهل تسريب الموارد. إذا كنت تريد تنفيذ عمليات واجهة المستخدم في صفحة الويب، يجب على المعرض عملية صفحة الويب التواصل مع العملية الرئيسية لطلب أن العملية الرئيسية تقوم بهذه العمليات.

> #### آسيا: الاتصال بين العمليات
> 
> في إلكترون، لدينا عدة طرق للتواصل بين العملية الرئيسية وعمليات العرض، مثل [`ipcRenderer`](../api/ipc-renderer.md) و [`ipcMain`](../api/ipc-main.md) وحدات لإرسال الرسائل، و [عن بعد](../api/remote.md) وحدة للاتصال على نمط RPC There is also an FAQ entry on [how to share data between web pages][share-data].

## استخدام تطبيقات إلكترون

يوفر إلكترون عددا من واجهات برمجة التطبيقات التي تدعم تطوير تطبيق سطح المكتب في كل من العملية الرئيسية وعملية العرض. في كلتا العمليتين ، يمكنك الوصول إلى واجهة برمجة تطبيقات Electron's باشتراط الوحدة المدرجة فيها:

```javascript
إلكترون = مطلوبة ('electron')
```

يتم تعيين كل واجهة برمجة تطبيقات إلكترون نوع العملية. العديد منها يمكن أن تستخدم فقط من العملية الرئيسية، وبعضها فقط من عملية العرض، بعضها من كليهما. الوثائق لكل واجهة برمجة التطبيقات ستحدد العملية التي يمكن استخدامها منها.

يتم إنشاء نافذة في إلكترون على سبيل المثال باستخدام صف `متصفح` . وهو متاح فقط في العملية الرئيسية.

```javascript
// هذا سوف يعمل في العملية الرئيسية، ولكن كن 'غير محدد` في عملية
// renderer:
const { BrowserWindow } = require('electron')

الفوز = متصفح جديد()
```

بما أن الاتصال بين العمليات ممكن ، يمكن لعملية العارض أن تستدعي العملية الرئيسية لأداء المهام. يأتي إلكترون مع وحدة تسمى `البعيد` التي تكشف عن APIs عادة ما تكون متاحة على العملية الرئيسية. من أجل إنشاء `نافذة المتصفح` من عملية العرض، سوف نستخدم عن بعد كرجل وسطي:

```javascript
// هذا سوف يعمل في عملية العرض، ولكن كن 'غير محدد` في العملية
// الرئيسية:
const { remote } = require('electron')
const { BrowserWindow } = بعد

const win = New BrowserWindow()
```

## باستخدام Node.js APIs

إلكترون يكشف إمكانية الوصول الكامل إلى Node.js في كل من العملية الرئيسية و العملية المعرضة. ويترتب على ذلك أثران هامان:

1) جميع APIs المتاحة في Node.js متاحة في Electron. الاتصال ب التعليمات البرمجية التالية من تطبيق إلكترون:

```javascript
const fs = مطلوبة ('fs')

الجذر = fs. eaddirSync('/')

// هذا سيطبع جميع الملفات على مستوى جذور القرص،
// إما '/' أو 'C:\'.
console.log(root)منصةشليلة ar 
```

كما قد تكون قادراً على التخمين مسبقاً، فإن لهذا تداعيات أمنية مهمة إذا حاولت تحميل محتوى عن بعد. You can find more information and guidance on loading remote content in our [security documentation][security].

2) يمكنك استخدام وحدات Node.js في تطبيقك. اختر وحدتك المفضلة npm npm يقدم حاليا أكبر مستودع في العالم للتعليمات البرمجية المفتوحة المصدر - القدرة على استخدام التعليمات البرمجية المحفوظة والمختبرة جيدا والتي كانت محجوزة لتطبيقات الخادم هي واحدة من الميزات الرئيسية لإلكترون.

على سبيل المثال، لاستخدام AWS SDK الرسمية في التطبيق الخاص بك، سوف تقوم أولاً بتثبيته كاعتماد:

```sh
npm تثبيت --حفظ aws-sdk
```

ثم في تطبيق إلكترون الخاص بك، يتطلب ويستخدم الوحدة كما لو كنت بناء تطبيق Node.js :

```javascript
// عميل S3 جاهز للاستخدام
const S3 = مطلوبة ('aws-sdk/clients/s3')
```

وهناك تحذير مهم واحد: العقدة الأصلية. s وحدات (أي الوحدات التي تتطلب تجميع التعليمات البرمجية الأصلية قبل استخدامها) سوف تحتاج إلى تجميعها لاستخدامها مع إلكترون.

The vast majority of Node.js modules are _not_ native. فقط 400 من وحدات ~650,000 هي وحدات أصلية. However, if you do need native modules, please consult [this guide on how to recompile them for Electron][native-node].

[security]: ./security.md
[native-node]: ./using-native-node-modules.md
[share-data]: ../faq.md#how-to-share-data-between-web-pages
