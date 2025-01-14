## 类: TouchBarSpacer

> 在mac os应用中，为touch bar中的相邻项之间留白

进程：[主进程](../tutorial/application-architecture.md#main-and-renderer-processes)

### `new TouchBarSpacer(options)`

* `options` Object
  * `size` String (optional) - Size of spacer, possible values are:
    * ` small `-项之间的留较小间距。 Maps to `NSTouchBarItemIdentifierFixedSpaceSmall`. This is the default.
    * ` large `-项之间的留较大间距。 Maps to `NSTouchBarItemIdentifierFixedSpaceLarge`.
    * ` flexible `-补齐所有可用空间。 Maps to `NSTouchBarItemIdentifierFlexibleSpace`.

### 实例属性

The following properties are available on instances of `TouchBarSpacer`:

#### `touchBarSpacer.size`

A `String` representing the size of the spacer.  Can be `small`, `large` or `flexible`.
