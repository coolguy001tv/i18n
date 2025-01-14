# دليل الصناعة التحويلية (Ubuntu Software Center & أكثر)

يوفر هذا الدليل معلومات عن كيفية حزمة تطبيق إلكترون الخاص بك لأي بيئة Snapcraft ، بما في ذلك مركز برمجيات Ubuntu .

## ألف - الخلفية والاحتياجات

إلى جانب مجتمع لينوكس الأوسع نطاقا، يهدف الكنيه إلى إصلاح العديد من مشاكل التثبيت الشائعة في البرنامج مع مشروع [`سنابكو`](https://snapcraft.io/) . Snaps هي حزم برمجيات محوية تحتوي على التبعيات المطلوبة، التحديث التلقائي، والعمل على جميع التوزيعات الرئيسية لينكس بدون تعديل النظام.

هناك ثلاث طرق لإنشاء ملف `.snap`:

1) Using [`electron-forge`][electron-forge] or [`electron-builder`][electron-builder], both tools that come with `snap` support out of the box. وهذا هو أسهل خيار. 2) استخدام `electron-installer-sscp`، الذي يأخذ `إخراج الحافظة الإلكترونية`. 3) استخدام حزمة `.deb` التي تم إنشاؤها.

In all cases, you will need to have the `snapcraft` tool installed. We recommend building on Ubuntu 16.04 (or the current LTS).

```sh
التقط تثبيت snapcraft - كلاسيكي
```

While it _is possible_ to install `snapcraft` on macOS using Homebrew, it is not able to build `snap` packages and is focused on managing packages in the store.

## استخدام `electron-installer-sscp`

The module works like [`electron-winstaller`][electron-winstaller] and similar modules in that its scope is limited to building snap packages. يمكنك تثبيته مع:

```sh
npm تثبيت --save-dev electron-installer-sscp
```

### الخطوة 1: حزمة تطبيق إلكترون

Package the application using [electron-packager][electron-packager] (or a similar tool). تأكد من إزالة `node_modules` التي لا تحتاج إليها في تطبيق النهائي الخاص بك، بما أن أي وحدة لا تحتاجها في الواقع ستزيد حجم تطبيقك.

يجب أن يبدو المخرجات مثل هذا:

```plaintext
.
<unk> <unk> ', dist
    <unk> <unk> ', app-linux-x64
        <unk> <unk> ', LICENSE
        <unk> ', LICENSES. hromium.html
        <unk> <unk> ~ content_shell. ك
        <unk> <unk> <unk> <unk> <unk> <unk> <unk> <unk> <unk> app
        <unk> <unk> <unk> <unk> <unk> - icudtl. في
        <unk> <unk> ', libgcrypt.so.11
        <unk> <unk> ', libnode. o
        <unk> <unk> <unk> <unk> locales
        <unk> <unk> مــــن الموارد
        <unk> <unk> <unk> ', v8_context_snapلقة. في
        <unk> ، إصدار
```

### الخطوة 2: تشغيل `إلكترون - المثبت - لقطة`

من محطة طرفية تحتوي على `سنسناكرا` في `PATH`، قم بتشغيل `إلكترون المثبت-اللقطة` مع المعلمة الوحيدة المطلوبة `--src`، والذي هو موقع حزمتك تطبيق إلكترون الذي تم إنشاؤه في الخطوة الأولى.

```sh
npx electron-installer-snap --src=out/myappname-linux-x64
```

إذا كان لديك خط أنابيب بناء، فيمكنك استخدام `إلكترون - المثبت - اللمس` برمجيا. For more information, see the [Snapcraft API docs][snapcraft-syntax].

```js
قرص = مطلوب('electron-installer-snap')

snap(خيارات)
  .then(snapPath => console.log(`إنشاء لقطة في ${snapPath}!`))
```

## استخدام حزمة Debian الحالية

Snapcraft قادر على أخذ ملف `.deb` موجود وتحويله إلى ملف `.sscp`. تم تكوين إنشاء لقطة باستخدام `حرفة. ml` ملف يصف المصادر والتبعيات والوصف وغيرها من لبنات البناء الأساسية.

### الخطوة 1: إنشاء حزمة دبيان

إذا لم يكن لديك بالفعل حزمة `.deb` باستخدام `electron-installer-snap` قد تكون مسارا أسهل لإنشاء حزم التقطير. However, multiple solutions for creating Debian packages exist, including [`electron-forge`][electron-forge], [`electron-builder`][electron-builder] or [`electron-installer-debian`][electron-installer-debian].

### الخطوة 2: إنشاء snapcraft.yaml

For more information on the available configuration options, see the [documentation on the snapcraft syntax][snapcraft-syntax]. Let's look at an example:

```yaml
الاسم: إصدار myApp
: '2.0.0'
موجز: وصف صغير للتطبيق
الوصف: <unk>
 هل تعرفون ماذا؟ هذا التطبيق رائع! إنه يفعل كل الأشياء
 لك. البعض يقول أنه يبقيك شابا، ربما حتى سعيدا.

grade: stable
confinement: classic

parts:
  slack:
    plugin: dump
    source: my-deb.deb
    source-type: deb
    after:
      - desktop-gtk3
    stage-packages:
      - libasound2
      - libnotify4
      - libnspr4
      - libnss3
      - libpcre3
      - libpulse0
      - libxss1
      - libxtst6
  electron-launch:
    plugin: dump
    source: files/
    prepare: |
      chmod +x bin/electron-launch

apps:
  myApp:
    command: bin/electron-launch $SNAP/usr/lib/myApp/myApp
    desktop: usr/share/applications/myApp.desktop
    # Correct the TMPDIR path for Chromium Framework/Electron to ensure
    # libappindicator has readable resources.
    البيئة:
      TMPDIR: $XDG_RUNTIME_DIR
```

As you can see, the `snapcraft.yaml` instructs the system to launch a file called `electron-launch`. In this example, it passes information on to the app's binary:

```sh
#!/bin/sh

exec "$@" --executed-from="$(pwd)" --pid=$$ > /dev/null 2>&1 &
```

Alternatively, if you're building your `snap` with `strict` confinement, you can use the `desktop-launch` command:

```yaml
تطبيقات:
  myApp:
    # اصح مسار TMPDIR لإطار Chromium Framework/Electron لضمان أن
    # libappindicator لديه موارد للقراءة.
    الأمر: env TMPDIR=$XDG_RUNTIME_DIR PATH=/usr/local/bin:${PATH} ${SNAP}/bin/desktop-launch $SNAP/myApp/desktop
    سطح المكتب: usr/share/applications/desktop.desktop
```

[snapcraft-syntax]: https://docs.snapcraft.io/build-snaps/syntax
[snapcraft-syntax]: https://docs.snapcraft.io/build-snaps/syntax
[electron-packager]: https://github.com/electron/electron-packager
[electron-forge]: https://github.com/electron-userland/electron-forge
[electron-builder]: https://github.com/electron-userland/electron-builder
[electron-installer-debian]: https://github.com/unindented/electron-installer-debian
[electron-winstaller]: https://github.com/electron/windows-installer
