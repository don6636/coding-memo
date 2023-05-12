[TOC]
# ARCHIVE

- 工具类应声明为 **final** 的，并且私有化构造器。

- `DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);`

- DAO（Data Access Object）数据访问对象

- Model > Entity > DTO

  *DTO(data-transfer object):* 所有字段都是 public 和 final 的，所以没有 getter 和 setter 方法。也就是不可变的，只能通过构造器给它赋值，之后就只能读而不能修改它的值（字符串本身就是不可变的，因此无法修改字符串的内容，也无法给它的字段重新赋值）。若想要修改一个DTO，只能用一个新对象来替换。

- 当使用string常量作为switch语句条件时，必须符合 **编译时常量**，使用运行时常量会报错：*[Constant expression](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.28) required*

- 当使用string常量作为switch语句条件时，编译期会通过 `String#hashCode` 窄化成 *int*（JVM不支持String类型的case值，所以需要编译器调用String.hashCode并以此为case条件的值），因此<u>注意确保string条件非空</u>，避免NPE，不能确保时可改用 *if-else-if*。

- case数量多，使用 *switch*；

  case数量少，使用 *if-else-if*；

  有极大概率发生的情况，且case数量可以接受，也可使用 *if-else-if*，且将最容易命中的case放在上面





# KEEP ANDROID

- [What is API Level?](https://developer.android.google.cn/guide/topics/manifest/uses-sdk-element#ApiLevels)

- 设置UI布局 `setContentView(R.layout.main)` / `setContentView(view)` ..

- TextView中文字留白用 **全角** 空格填充，文字两端对齐也可以采取这种方式。

- TextView内容可滑动
     1. 设置xml属性 `android:scrollHorizontally="true"` 和 `android:maxLines="1"`，不要用singleLine，没用
     2. `tv.setMovementMethod(ScrollingMovementMethod.getInstance());`？为何是垂直滑动？
     
- 屏蔽EditText自动获取焦点，在EditText的父控件中设置：

     `android:focusable="true"` 和 `android:focusableInTouchMode="true"`

- EditText自定义光标颜色：`android:textCursorDrawable="@drawable/et_cursor"`，属性值为 **@null** 时表示光标和字体颜色一致。

- padding属性对 `android:background` 无效，但对ImageView的 `android:src` 是有效的。

- TableRow子级无需指定宽高，因为其layout_width和layout_height属性始终强制为MATCH_PARENT和WRAP_CONTENT

- images[++ currentImg % images.length]

- android:gravity                 组件中内容的对齐方式
  android:layout_gravity    组件在父布局中的对齐方式

- LinearLayout的baselineAligned属性：文字基线对齐。

- RelativeLayout中当控件引用相对控件的id时，该控件必须要定义在相对控件之后？

- View.VISIBLE        可见
  View.INVISIBLE    不可见，保留位置和大小
  View.GONE          不可见，不保留位置大小

- 打气筒 `View view = LayoutInflater.from(context).inflate(R.layout.xxx, parent, false);`

     ***ps:*** *attachToRoot*为false只会应用LayoutParams而不会添加到父view中。

- Context用 **this** 时是因为当前Activity是Context的子类，匿名内部类要用 **Activity.this**。

- **避免内存泄露：**所有与布局无关的Context都可以传入 **ApplicationContext**。

- *1*　Toast.LENGTH_LONG（3.5s）
  *0*　Toast.LENGTH_SHORT（2s）

- 继承Activity获取ActionBar调用 `getActionBar();`

     继承AppCompatActivity获取ActionBar调用 `getSupportActionBar();`

     ***ps:*** 部分兼容包的方法名较正式包多了Support前缀。

- 若自定义View是在Java代码中new实例化的，调用 **1** 参构造器，若是在xml文件中声明的，则调用 **2** 参构造器，自定义属性通过AttributeSet参数传递进来（反射）。

- **.9.png**：左上拉伸，右下内容。（不要压缩）

- ScrollView只能有一个直接的child。

- 开启子线程的方法
     - 定义一个类继承Thread类
     - 定义一个类实现Runnable接口
     
- ~~子线程不能Toast~~（未调用 *Looper.prepare();* 的线程不能Toast）。

- 不管什么API版本，耗时操作都应放进子线程中执行。

- 仅更新UI推荐用 `Activity#runOnUiThread` 若还需要携带数据就用 **Handler**
- 用xml为PopupWindow设置view时，xml中第一层view的大小属性将被忽略（通过多嵌套一层layout解决）?

- 同时 `setOutsideTouchable(true);` 和 `setBackgroundDrawable(new ColorDrawable(0x00000000));` _(argb)_ 可实现点击空白处让PopupWindow消失的效果。

- `System.currentTimeMillis()` 获取的是系统时间，是距离1970年1月1日开始计算的一个值，适合用于获取当前日期；`SystemClock.elapsedRealtime()` 获取从设备boot后经历的时间值，适合计算经过了多久。

- Notification必须 `setSmallIcon()`，否则不显示。

- Notification必须 `setContentIntent()`，才能使 `setAutoCancel(true)` 生效。

- 从API21开始，通知栏图标 `setSmallIcon()` 应只用alpha图层绘制（勿用RGB图层)，下拉通知栏后大图右下角小图圆形背景颜色使用 `setColor()` 自定义。

- 当只有application标签下配置了label属性时，APP中所有Activity的标题包括名称都会使用该属性值；但是当启动页Activity配置了label属性时，APP名称和启动页标题则会使用启动页label属性，其他Activity默认继续使用application的label属性或是各自配置的label属性。

- 内存泄露会导致 **GC** *stop-the-world*，从而可能引起 **ANR**。

- string resource中配置字符串占位符如 *%1\$s/%2$s*，使用时必须初始化，否则将显示占位符本身

  `getString(R.string.placeholder, "str1", "str2")` 内部通过 `String#format` 实现

- 属性 `android:foreground="?selectableItemBackground"` 自带简单ripple效果，可以省去写selector的工夫 *(API 23)*

- 指定属性 `style="?android:attr/borderlessButtonStyle"` 可移除Button默认elevation效果。

- 取消Button默认全大写：`android:textAllCaps="false"`

- `android:descendantFocusability` 属性用于指定ViewGroup和其子View获取焦点顺序关系：

  `beforeDescendants` ViewGroup优先于任何子View

  `afterDescendants`   ViewGroup会在所有子View都不想获取焦点时才获取

  `blocksDescendants` ViewGroup会阻拦子View获取焦点
  
- AlertDialog.Builder要传入能够获取parent's theme的context，如Activity。

- 即使同一天的Calendar实例，`calendar.getTimeInMillis()` ==每次返回结果也有微小差异？==需要重置毫秒 `calendar.set(Calendar.MILLISECOND, 0);` 消除差异。


- `CheckedTextView#toggle()`

- 获取setContentView中的view（DecorView的一部分）

  ```java
  ViewGroup content = findViewById(android.R.id.content);
  View view = content.getChildAt(0);
  ```

- `Window.getDecorView()` 和 `Window.peekDecorView()` 区别：

  `getDecorView()` 获取顶级窗口视图 DecorView，若为null会先去创建

  `peekDecorView()` 直接返回mDecor，可能为null

- **LayoutInflater.Factory2** 是通过LayoutInflater创建view时的一个 **回调**，能够改变view的创建过程，AppCompatActivity就是通过该方式完成了控件的版本兼容。

- 自定义*LayoutInflater.Factory2*必须在`super.onCreate`之前设置 `LayoutInflater.from(this).setFactory2();`，因为系统会在其中通过 `AppCompatDelegateImpl#installViewFactory` 设置默认的compat兼容factory，且factory只允许设置一次。

- 获取 **DisplayMetrics** 可以不用引用Context（`context.getResources().getDisplayMetrics()`）：`Resources.getSystem().getDisplayMetrics()`

- 使用 **elevation** 属性需要预留margin空间以显示阴影，并且必须开启hardwareAccelerated才会生效。

- 可以使用 `getCallingActivity()` 获取启动当前Activity的上一个Activity的ComponentName，但必须由 `startActivityForResult` 启动，仅用 `startActivity` 启动会一直返回null。

- 要改变滑动控件overscroll edge glow color，可以在主题中添加以下属性（始于API21）：

  `<item name="android:colorEdgeEffect">@color/colorDim</item>`

  而不用重设 `android:colorPrimary`，它还会影响ActionBar/ToolBar等控件的默认颜色。
  
- `TextUtils.isEmpty()` 判空

  `TextUtils.join()` 拼接字符串

  `TextUtils.concat()` 合并字符串

- ==EditText水平方向的gravity属性影响windowSoftInputMode属性？==

- 在Java代码中 `setInputType()`，必须结合输入类型 *位模式 (CLASS)* 与 *变体 (VARIATION)* / *标志 (FLAG)* 以指示所需的行为。如：

  - 密码文本

    `editText.setInputType(InputType.TYPE_CLASS_TEXT | InputType.TYPE_TEXT_VARIATION_PASSWORD);`

  - 多行文本

    `editText.setInputType(InputType.TYPE_CLASS_TEXT | InputType.TYPE_TEXT_FLAG_MULTI_LINE);`

  - 日期

    `editText.setInputType(InputType.TYPE_CLASS_DATETIME | InputType.TYPE_DATETIME_VARIATION_DATE);`

- EditText自动换行：`android:inputType="textMultiLine"`

- EditText设置 *textMultiLine* 同时实现 **imeOptions** 功能：

  >当EditText获取焦点时，会调用 `TextView#onCreateInputConnection(EditorInfo outAttrs)` 来为输入法创建一个可与此view交互的连接，还可使用 *outAttrs* 参数指定额外属性信息。注意该方法中的以下几行代码：
  >
  >```java
  >if (isMultilineInputType(outAttrs.inputType)) {
  >    // Multi-line text editors should always show an enter key.
  >    outAttrs.imeOptions |= EditorInfo.IME_FLAG_NO_ENTER_ACTION;
  >}
  >```
  >
  >这使得EditText的inputType包含textMultiLine时会自动添加 *EditorInfo.IME_FLAG_NO_ENTER_ACTION* 标记位，从而只显示Enter键。

  Workaround：

  代码中添加 `et.setRawInputType(EditorInfo.TYPE_CLASS_TEXT | EditorInfo.TYPE_TEXT_FLAG_IME_MULTI_LINE);`

  ***ps:*** 然后可使用 `et.setOnEditorActionListener(OnEditorActionListener)` 监听IME Action。且：

  - 必须调用 `setRawInputType` 而不是 `setInputType`；
  - `EditorInfo.TYPE_TEXT_FLAG_IME_MULTI_LINE` 标记位可缺省；
  - 在xml中设置 `android:inputType="textImeMultiLine"` 也无效，这相当于调用 `setInputType`，不符合第1条；
  - *imeOptions* 在代码或xml中设置都行。

- 使用 `rb.setChecked(boolean);` 或 `rb.toggle();` 代替 `rg.check(R.id.rb)` 避免多次回调 `RadioGroup.OnCheckedChangeListener#onCheckedChanged` 。

- Scrollview未完全滑到底部：`scrollView.post(() -> scrollView.fullScroll(View.FOCUS_DOWN));`

- `canScrollVertically(int direction)` 和 `canScrollHorizontally(int direction)`

  direction：*Negative to check scrolling up, positive to check scrolling down.*

  国外的思维：scrolling up指的是往上滑动列表内容，对应的手势为向下滑动；

  国内的思维：单纯指手势方向。

  ```java
  rv.setOnTouchListener((v, event) -> {
      // canScrollVertically(-1) 的值表示是否能向下滚动（手势），false表示已经滚动到顶部
      rv.getParent().requestDisallowInterceptTouchEvent(rv.canScrollVertically(-1));
      return false;
  })
  ```

- View/ViewGroup设置前景图片及其Gravity：

  `android:foreground="reference|color"`

  `android:foregroundGravity="center"`

- `Dialog.dismiss` 时异常：

  ```
  java.lang.IllegalArgumentException: View=DecorView@xxxxxxx[XxxActivity] not attached to window manager
      at android.view.WindowManagerGlobal.findViewLocked(WindowManagerGlobal.java:485)
      at android.view.WindowManagerGlobal.removeView(WindowManagerGlobal.java:394)
      at android.view.WindowManagerImpl.removeViewImmediate(WindowManagerImpl.java:124)
      at android.app.Dialog.dismissDialog(Dialog.java:375)
      at android.app.Dialog.dismiss(Dialog.java:358)
  ```

  复现：开启*开发者选项* **不保留任何活动** 选项，在Dialog弹出后快速点击home键，若Dialog延迟(Delay或网络)dismiss，即可触发。

  原因：DecorView因为Activity被回收(可能内存不足)，已从window中detach，此时再dismiss就会异常。

  解决方案：dismiss前判断Activity是否已被回收

  ```java
  // isFinishing()不能保证避免该问题
  if (!isDestroyed() && tipDialog.isShowing()) {
      tipDialog.dismiss();
  }
  ```

- 获取View的可视边界：`view.getGlobalVisibleRect(rect);`

- `View.callOnClick()` 和 `View.performClick()` 区别：

  `callOnClick()` 直接判空并回调 `View.OnClickListener#onClick`

  `performClick()` 除了判空回调 `View.OnClickListener#onClick`，还额外执行了一些关联动作如：无障碍事件，播放音效等

- ==outlineProvider==



- 可使用 `<xliff:g>` 标签标记本地化时不应被翻译的文本部分，注意引入xliff命名空间。

```xml
<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">
    <!-- 添加id说明用途，并提供示例 -->
    <string name="countdown"><xliff:g example="5 days" id="time">%1$s</xliff:g> until holiday</string>
</resources>
```

- Android平台提供了2种伪语言区域 ([检查本地化问题](https://developer.android.google.cn/guide/topics/resources/pseudolocales#spot_localization_issues))，分别表示从左到右 (LTR) 和从右到左 (RTL) 显示的语言：英语(XA) ^LTR^、AR(XB) ^RTL^。

  ***ps:*** 需要在应用的具体buildTypes (如debug) 中添加 `pseudoLocalesEnabled true` 配置启用伪语言区域。

- 通过在manifest文件中声明 `android:permission` 属性自定义四大组件权限，从而对调用进行限制。其中Activity、Service、ContentProvider在调用方没有权限时会抛出SecurityException，而BroadcastReceiver不会抛异常，只会接收不到。（只适用于不同应用间调用？）
- [URI权限](https://developer.android.google.cn/guide/topics/permissions/overview#uri)



- ***res/drawable*** 存放图片和可绘制xml

  ***res/mipmap*** 存放启动图标和图标可绘制xml

- [资源类型分组](https://developer.android.google.cn/guide/topics/resources/providing-resources#ResourceTypes)
  
  - [ ] 绘制表 **1**

- 在代码中引用资源的语法：`[`*`<package_name>.`*`]R`*`.<resource_type>.<resource_name>`*

  ***e.g.*** `android.R.layout.activity_list_item`

  *`<package_name>`* 是资源所在包的名称（若引用的资源来自您自己的资源包，则不需要）。

  *`<resource_type>`* 是资源类型的 **R** 子类。

  *`<resource_name>`* 是不带扩展名的资源文件名，或 XML 元素中的 `android:name` 属性值（若资源是简单值）。

- 在 XML 资源中引用资源的语法：`@[`*`<package_name>`*`:]`*`<resource_type>`*`/`*`<resource_name>`*

  ***e.g.*** `@android:color/secondary_text_dark`

  *`<package_name>`* 是资源所在包的名称（若引用的资源来自相同资源包，则不需要）。

  *`<resource_type>`* 是资源类型的 **R** 子类。

  *`<resource_name>`* 是不带扩展名的资源文件名，或 XML 元素中的 `android:name` 属性值（若资源是简单值）。

- 引用样式属性 *(style attributes)*：`?[`*`<package_name>`*`:][`*`<resource_type>`*`/]`*`<resource_name>`*

  ***e.g.*** `android:textColor=?android:attr/textColorSecondary`
  
  引用属性，匹配系统theme的“textColorSecondary”文本颜色。如上例，`android:textColor` 属性指定了当前theme中某个style属性的名称。系统会将应用于 `android:textColorSecondary` 样式属性的值用于 `android:textColor` 属性。由于系统确定当前Context中一定存在这个属性资源，因此无需显式声明类型 `attr`（可简化为 `?android:textColorSecondary`）。

> style attributes resource允许在当前应用的theme中引用某个属性的值。引用样式属性的实质是“在当前theme中使用此属性定义的style”。可以是系统内建属性，也可以在当前theme下未定义某个属性时覆写该属性。如AppCompat系列主题中没有 *?android:attr/elevation*（内建）属性，像 `android:elevation="?elevation"` 这样引用就会发生Error inflating class。此时在当前theme下定义 `<item name="elevation">@dimen/elevation</item>` 即可正常引用。

- 访问原始资源

  原始资源应保存于 `assets/` 目录，它们没有资源ID，无法通过 **R** 类或在xml资源中引用。可采用类似普通文件系统的方式查询 `assets/` 目录中的文件，并通过 **AssetManager** 读取原始数据。若只是需要读取（例如视频或音频文件）的原始数据，则可将文件保存在 `res/raw/` 目录中，并通过 `openRawResource()` 读取字节流。如采用比特流方式读取图片来转换成位图资源，可将图片存放于此目录（该目录下的图片资源不会被Android系统优化处理）。

- 访问Android平台资源

  Android系统包含了许多标准资源，例如style、theme和layout。若要访问这些资源，可通过 **android** 包名称限定资源引用。如：`android.R.layout.simple_list_item_1`

- 若未匹配到对应屏幕像素密度的限定符，Android系统偏向于缩小较大的原始图像，而非放大较小的原始图像。如当前屏幕像素为xhdpi，但是应用没有提供xhdpi资源，则系统会优先从xxhdpi中，而不是从hdpi中寻找。

  ***补充：***在根据屏幕尺寸限定符选择资源时，若没有更好的匹配资源，则系统将使用专为小屏而设计的资源（例如，必要时，大屏设备将使用标准尺寸的屏幕资源）。若唯一可用的资源是针对*大屏* 的，则小屏系统**不会**使用这些资源，并且若没有其他资源与当前小屏设备配置匹配，应用将会崩溃（例如，“若所有layout资源均使用 `xlarge` 限定符标记，但设备是标准尺寸的屏幕” 的情况）。
  
- 创建APP私有目录：`context.getDir("pri", Context.MODE_PRIVATE);`，默认添加 **app_** 前缀即 `/data/data/<package>/app_pri`。

- Tooltip兼容 `TooltipCompat.setTooltipText(view, "Tooltip");`

- 使用框架库或支持库以编程方式或在xml中设置 `TextView` 的自动调整大小。

  ***ps:*** 若在xml文件中设置自动调整大小，不建议对 `TextView` 的 `layout_width` 或 `layout_height` 属性应用“wrap_content”值，可能会产生意外结果。（以下示例展示了所有使用方式，具体使用时并非要调用多个方法或声明应用所有xml属性，需要根据需求主动调整组合）

  **API26开始**

  ------

  ```java
  //方式一：均匀缩放的默认尺寸为 minTextSize = 12sp、maxTextSize = 112sp 和 granularity = 1px
  textView.setAutoSizeTextTypeWithDefaults(TextView.AUTO_SIZE_TEXT_TYPE_UNIFORM);
  //方式二：自定义缩放范围和粒度(步长)，如下8 ~ 24sp，以2sp的粒度自动调整TextView大小
  textView.setAutoSizeTextTypeUniformWithConfiguration(
          8,
          24,
          2,
          TypedValue.COMPLEX_UNIT_SP);
  //方式三：定义预设尺寸指定TextView在自动调整大小时的所有可选值，如下TextView将根据布局空间自动应用8/14/18/24sp中的合适尺寸值
  textView.setAutoSizeTextTypeUniformWithPresetSizes(
          new int[]{8, 14, 18, 24},
          TypedValue.COMPLEX_UNIT_SP);
  ```

  或在xml中设置

  ```xml
  <TextView
      android:autoSizeTextType="uniform"
      android:autoSizeMinTextSize="8sp"
      android:autoSizeMaxTextSize="24sp"
      android:autoSizeStepGranularity="2sp"
      android:autoSizePresetSizes="@array/preset_auto_text_sizes"
      ... />
  ```

  ***ps:*** 使用 `android` 命名空间。

  **兼容旧版本**

  ------

  ```java
  TextViewCompat.setAutoSizeTextTypeWithDefaults(textView,
          TextViewCompat.AUTO_SIZE_TEXT_TYPE_UNIFORM);
  
  TextViewCompat.setAutoSizeTextTypeUniformWithConfiguration(textView,
          8,
          24,
          2,
          TypedValue.COMPLEX_UNIT_SP);
  
  TextViewCompat.setAutoSizeTextTypeUniformWithPresetSizes(textView,
          new int[]{8, 14, 18, 24},
          TypedValue.COMPLEX_UNIT_SP);
  ```

  或在xml中设置

  ```xml
  <TextView
      app:autoSizeTextType="uniform"
      app:autoSizeMinTextSize="8sp"
      app:autoSizeMaxTextSize="24sp"
      app:autoSizeStepGranularity="2sp"
      app:autoSizePresetSizes="@array/preset_auto_text_sizes"
      ... />
  ```

  ***ps:*** 使用 `app` 命名空间兼容:arrow_heading_up:

  定义尺寸资源 :arrow_heading_down:

  ```xml
  <!-- res/values/arrays.xml -->
  <array name="preset_auto_text_sizes">
      <item>8sp</item>
      <item>14sp</item>
      <item>18sp</item>
      <item>24sp</item>
  </array>
  ```

- TextView长按复制

  ```xml
  <TextView
      android:textIsSelectable="true"
      android:textSelectHandle="@"
      android:textSelectHandleLeft="@"
      android:textSelectHandleRight="@"
      ... />
  ```

  或者在代码中设置

  ```java
  textView.setTextIsSelectable(true);
  //文本字体间光标移动手柄，适用于EditText等可编辑控件
  textView.setTextSelectHandle();
  //长按复制时显示在可选择区域左右两端的光标移动手柄
  textView.setTextSelectHandleLeft();
  textView.setTextSelectHandleRight();
  
  //剪贴板内容变化监听
  ClipboardManager mgr = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);
  mgr.addPrimaryClipChangedListener(new ClipboardManager.OnPrimaryClipChangedListener() {
      @Override
      public void onPrimaryClipChanged() {
          if (!mgr.hasPrimaryClip()) return;
          ClipData primaryClip = mgr.getPrimaryClip();
          if (primaryClip != null) {
              ...
          }
      }
  ```

  ***ps:*** ==onPrimaryClipChanged回调两次？暂未找到答案==

- ScrollView设置 `android:fillViewport="true"` 将会以 拉伸子View高度来填充当前可视区域 的形式进行测量。





## 四大组件

> Android四大组件中除了BroadcastReceiver，其他三种都必须在manifest中注册，对于BroadcastReceiver来说，它既可以在manifest中静态注册，也可以在Java代码中动态注册。在调用方式上，Activity、Service和BroadcastReceiver需要借助Intent，而ContentProvider无须借助Intent。

### Activity

**_ps:_** **切记要在AndroidManifest中注册**

- 生命周期

   - `onCreate()`

   - `onStart()` / `onRestart()`

   - `onResume()`

   - `onPause()`

     勿做重量级操作，旧Activity的 `onPause()` 执行完成后，新Activity才能执行到 `onResume()`，接着执行旧Activity的 `onStop()`

   - `onStop()`

     也尽量勿做重量操作，若新打开的Activity采用了透明主题，旧Activity将不会回调 `onStop()`

   - `onDestroy()`

- 异常情况下的生命周期

   > 当系统配置发生改变时，Activity被异常终止，其 `onPause` `onStop` `onDestroy` 都会执行，同时会调用 `onSaveInstanceState` 保存当前状态，该方法执行于 `onStop` 之前，和 `onPause` 没有既定的时序关系？只在异常情况下调用。Activity重建后，`onCreate` 和 `onRestoreInstanceState` 都可以接收到保存状态的Bundle对象，`onCreate` 的Bundle参数只有在异常情况下才有值，正常启动时需要额外判空，因此建议重写 `onRestoreInstanceState` 来恢复数据，该方法调用时机在 `onStart` 之后。

   **情况一**：资源相关的系统配置发生改变导致Activity被杀死并重新创建，如旋转屏幕

   **情况二**：系统内存不足导致低优先级的Activity被杀死（保存/恢复过程同情况一。优先级：前台Activity > 可见非前台Activity > 后台Activity）

   ***ps:*** 系统配置改变时不重建Activity：在manifest文件中设置 `android:configChanges` 属性，多项之间以 **|** 连接，会回调 `onConfigurationChanged`。

- 移动当前Activity所在栈至后台（即返回桌面，生命周期回调至 *onStop*）`Activity#moveTaskToBack(true)`

- 销毁活动 `Activity#finish()`

- 跳转活动 Intent (系统内置动作)
   - 显式    `startActivity(new Intent(A.this, B.class));`

   - 隐式    （先判断有无匹配Activity，可跨应用匹配Action）

     PackageManager的 `resolveActivity` 或Intent的 `resolveActivity`/`resolveActivityInfo `方法，若无匹配Activity返回null
     
     扩展：PackageManager的 `getLaunchIntentForPackage` 和 `queryIntentActivities`

```java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_VIEW);
// intent.addCategory( );
intent.setData(Uri.parse(" "));
PackageManager manager = getPackageManager();
if (manager.resolveActivity(intent, PackageManager.MATCH_DEFAULT_ONLY) != null) {
    startActivity(intent);
}
// or
if (intent.resolveActivity(manager) != null) {
    startActivity(intent);
}
```
- 携带数据    Bundle < 0.5M
```java
//携带单条数据
Intent intent = new Intent(A.this, B.class);
intent.putExtra("key", value);
startActivity(intent);
```

```java
// 解析单条数据
Intent intent = getIntent();
String str = intent.getStringExtra("key");
// int num = intent.getIntExtra("key", 0);
```
```java
// 携带多条数据
Intent intent = new Intent(A.this, B.class);
Bundle bundle = new Bundle();
bundle.putInt("key1", 7);
bundle.putString("key2", "ANDROID")
...
intent.putExtras(bundle);
startActivity(intent);
```
```java
// 解析多条数据
Intent intent = getIntent();
Bundle bundle = intent.getExtras();
int num = bundle.getInt("key1");
String str = bundle.getString("key2");
...
```

- 返回数据

   - 用 `setResult(resultCode, intent)` 返回数据（`finish()` 之前调用）

   ```java
   Intent intent = new Intent();// 仅用于回传数据
   intent.putExtra("key", value);
   setResult(RESULT_OK, intent);//无需回传数据时可直接setResult(RESULT_OK);
   finish();
   ```

   - 用 `startActivityForResult(intent, requestCode)` 跳转，覆写 `onActivityResult()`处理数据

   ```java
   @Override
   protected void onActivityResult(int requestCode, int resultCode, Intent data) {
       //requestCode通常是自定义请求标识(区分请求者)
       switch (requestCode) {
       case 1:
           //resultCode通常使用系统默认的RESULT_OK、RESULT_CANCELED等(区分返回者)
           if (resultCode == RESULT_OK) {
               String str = data.getStringExtra("key");
               ...
           }
           break;
       default:
           super.onActivityResult(requestCode, resultCode, data);
           break;
       }
   }
   ```

- 保存状态

   >设备配置发生运行时变化时（*e.g.* 改变屏幕方向、键盘可用性、多窗口模式），Activity会先销毁然后重新创建，以此来自动加载与新配置相匹配的应用资源。要妥善处理这种行为，Activity必须恢复先前的状态，可使用 `onSaveInstanceState()`、`ViewModel`、持久存储等方式来完成[状态保存](https://developer.android.google.cn/topic/libraries/architecture/saving-states)。
   >

   然而，重启应用并恢复大量数据不仅成本高昂，而且会降低用户体验。在这种情况下有两种选择：

   1. 在配置变更期间保留对象

      允许Activity重启，但是需要将包含状态的对象传递给Activity的新实例。但Bundle并不适用于携带大型对象（如Bitmap），此时应结合ViewModel来处理。

      - 覆写 `onSaveInstanceState()` 保存数据

      ```java
      @Override
      protected void onSaveInstanceState(Bundle outState) {
          super.onSaveInstanceState(outState);
          outState.putString("key", value);
          ...
      }
      ```

      - 在 `onCreate()` 中恢复数据（官方建议在 `onRestoreInstanceState()` 中恢复数据，不用额外判空）

      ```java
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          if (savedInstanceState != null) {
              // TODO
          }
      }
      ```

      ```java
      @Override
      protected void onRestoreInstanceState(Bundle savedInstanceState) {
          super.onRestoreInstanceState(savedInstanceState);
          // TODO
      }
      ```

      - [ViewModel](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)
      - 持久存储

   2. 自行处理配置变更（建议只在必须避免Activity因配置变更而重启的无奈情况下，才考虑使用此方式，并且开发者有责任重置要为其提供备用资源的所有元素）

      若应用在特定配置变更期间无需更新资源，或因性能限制应尽量避免Activity重启而无法使用 *1.* 方式时，可声明Activity自行处理变更的配置，从而阻止Activity重启，然后在 `onConfigurationChanged()` 回调中进行手动处理（回调此方法时，Activity的 *Resource* 对象会进行相应的更新，并根据新配置返回资源）。

      ```xml
      <activity
          android:name=".ExampleActivity"
          android:configChanges="orientation|screenSize" />
      ```

      ```java
      @Override
      public void onConfigurationChanged(@NonNull Configuration newConfig) {
          super.onConfigurationChanged(newConfig);
          // Checks the orientation of the screen
          if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT) {
              // TODO
          } else if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
              // TODO 更新界面所用资源进行适当的更改，如通过 setImageResource() 重置任何 ImageView.
          }
      }
      ```

      ***ps:*** *newConfig* 代表<u>当前所有配置</u>，不仅仅是已变更的配置。

- 启动模式（注意不同模式生命周期差异）

   >可以分为两类：`standard` 和 `singleTop`（可多次进行实例化），`singleTask` 和 `singleInstance`（始终位于Activity堆栈顶，只能保留一个实例）。

   - `<activity android:launchMode="standard" >` **标准模式**：每次启动Activity都会在当前任务栈中创建一个新的实例（默认模式）<u>适用于大多数Activity</u>

   - `<activity android:launchMode="singleTop" >` **栈顶复用模式**：启动Activity时，若任务栈栈顶就是该Activity的实例则直接使用该实例（回调 `onNewIntent`，不会回调 `onCreate` 和 `onStart`），否则创建新实例

   - `<activity android:launchMode="singleTask" >` **栈内复用模式**：启动Activity时，若任务栈中已经存在该Activity的实例则直接使用该实例（也会回调 `onNewIntent`），并将栈中位于该实例之上的所有其他Activity实例出栈（clearTop），否则就创建新实例　<u>适用于主页</u>

     **_ps:_** `android:taskAffinity`

     > 标识了Activity启动后所在任务栈的名字，默认为包名。只在singleTask模式下（或Intent包含了FLAG_ACTIVITY_NEW_TASK标记）和 `android:allowTaskReparenting="true"` 时起作用。

     因此，当一个singleTask模式的Activity指定了非默认的affinity时，启动该Activity将先新建一个指定affinity的任务栈，再在新栈中创建该Activity的实例。

     ***特殊场景：***要求SampleActivity平时以standard模式启动，只有在特殊情况下才以指定affinity启动。这时可以先在manifest文件中声明affinity，平时正常启动，特殊情况下设置FLAG_ACTIVITY_NEW_TASK标记再启动（若原任务栈中已包含SampleActivity的实例，其与新栈中的SampleActivity实例彼此互不影响）

     ***扩展：***前台/后台任务栈（非官方概念，见《Android开发艺术探索》）

     - 不同应用中，如微信点击链接跳转到Chrome浏览器，则Chrome的任务栈就是前台任务栈，微信的任务栈就是后台任务栈（这里若Chrome的*WebViewActivity*声明了allowTaskReparenting为true，则此时点击Home键返回桌面，再点击Chrome图标，就不会启动Chrome的*MainActivity*，而是直接进入刚才的*WebViewActivity*。即taskAffinity==结合？== `android:allowTaskReparenting="true"` 使用时，Activity可以从其启动的任务栈移动到与其具有关联的任务栈（出现在前台时）则*WebViewActivity*会从微信任务栈转移到Chrome任务栈 ==为何测试中不是这样？==）
     - 同一应用中，如下面例子

     ***举例：***假设现有A、B、C (MAIN)、D四个Activity，A/B启动模式是standard，C/D启动模式是singleTask且为同一非默认affinity。现在打开APP，启动了C，再依次启动D、A、B，即C->D->A->B，当前B可见，则A/B处于同一任务栈，即前台任务栈，C/D处于同一任务栈，即后台任务栈。此时若再启动D，则C/D切换到前台任务栈；若启动C而不是D，C切换到前台任务栈，D被clearTop。

   ![](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/foreground&background-task-stack.png)

   - `<activity android:launchMode="singleInstance" >` **单例模式**：启动Activity的同时为其启用一个单独的任务栈(不会重复创建) <u>一般用不到</u>

     **_ps:_** 可用于和其他程序共享Activity实例

   - 指定方式

     1. AndroidManifest中 `<activity android:launchMode="" >`

     2. Intent中设置标记位 intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

        **_ps:_** 方式2优先级大于方式1，同时指定时以方式2为准；方式2无法为Activity指定singleInstance模式；方式1无法直接为Activity设定 FLAG_ACTIVITY_CLEAR_TOP 标识

- 常用FLAGS

   - FLAG_ACTIVITY_NEW_TASK    singleTask启动模式

   - FLAG_ACTIVITY_SINGLE_TOP    singleTop启动模式

   - FLAG_ACTIVITY_CLEAR_TOP    通常和FLAG_ACTIVITY_NEW_TASK配合使用

   - FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS    同xml属性 `android:excludeFromRecents`。当标记的Activity是任务栈的根Activity（栈底）时，置 true 会将该<u>任务栈</u>从最近多任务列表中排除（按下Home键回到桌面，属性就会起作用，不要退出APP）。

     ***ps:***

     1. 若APP只有一个任务栈，整个应用都会从多任务列表中排除，但并未被杀死（回调到 `onStop`），点击桌面图标仍然会回到原来的状态；

     2. 若APP运行多个任务栈，多任务列表会出现多个此APP的任务栈使用记录，点击不同记录会进入对应任务栈的栈顶Activity。若其中某个任务栈底Activity设置了排除标记，此时从桌面和多任务列表中都无法直接进入该标记栈，标记栈中的Activity都处于 `onStop`，只能从当前可见栈中再启动标记栈（并非重新创建，还是原来的栈），若标记栈中包含多个Activity，则除了根Activity都将被clearTop。

- **IntentFilter** 匹配规则

   - [action](https://developer.android.google.cn/guide/topics/manifest/action-element)

     Intent中的action存在且必须匹配IntentFilter中的其中一个action，区分大小写

   - [category](https://developer.android.google.cn/guide/topics/manifest/category-element)

     Intent可以不设置category，若设置，则每个category都必须是IntentFilter中已经声明了的；系统在调用startActivity或startActivityForResult时会默认为Intent加上 `android.intent.category.DEFAULT`，因此为了让Activity能接受隐式调用，必须在IntentFilter中声明 `android.intent.category.DEFAULT`

   - [data](https://developer.android.google.cn/guide/topics/manifest/data-element)（URI和mimeType两部分）

     Intent中的data存在且必须匹配IntentFilter中的其中一个data，即IntentFilter中出现的data部分也出现在了Intent的data中；另外，要指定完整的data需调用setDataAndType而不能单独调用setData和setType，因为两者会彼此清除对方的值；URI的schema默认值为 **content** 和 **file**。若定义data，必须至少设置一个scheme属性，否则其他URI属性都没有意义。
     
     `<scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]`
     
     如：
     
     `content://com.example.project:200/folder/subfolder/etc`
     
     `http://www.baidu.com:80/search/info`
     
     ```xml
     <intent-filter>
         ...
         <data android:mimeType="video/*" android:scheme="http" .. />
         <data android:mimeType="audio/mpeg" android:scheme="http" .. />
     </intent-filter>
     <!-- 另外，以下两种写法，效果是一致的 -->
     <intent-filter>
         ...
         <!-- 可以使用 * 匹配host中的字符，但必须是第一个字符 (host区分大小写) -->
         <data android:scheme="http" android:host="www.baidu.com" />
     </intent-filter>
     
     <intent-filter>
         ...
         <data android:scheme="http" />
         <data android:host="www.baidu.com" />
     </intent-filter>
     ```
     
     ```java
     intent.setDataAndType(Uri.parse("http://abc"), "video/mpeg");
     // or
     intent.setDataAndType(Uri.parse("http://abc"), "audio/mpeg");
     ```
     
     

- 界面布局

   - LinearLayout    `android:layout_weight`
   - RelativeLayout
   - FrameLayout
   - PercentFrameLayout
   - PercentRelativeLayout
   - ... Layout
```xml
<!-- 在Layout中声明，全限定类名及app命名空间 -->
<android.support.percent.PercentFrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <Button
        android:id="@+id/btn"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        android:text="BUTTON" />
</android.support.percent.PercentFrameLayout>
```
- 引用布局　`<include layout="@layout/title" />`

- 引用自定义Layout类(全限定类名)
```xml
<app.example.widget.TitleLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
```java
// 自定义Layout类
public class TitleLayout extends LinearLayout {
    public TitleLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        // 加载titlebar布局
        LayoutInflater.from(context).inflate(R.layout.view_title, this);
        // 实例titlebar按钮back
        Button btn_back = (Button) findViewById(R.id.btn_title_back);
        // 实例titlebar按钮edit
        Button btn_edit = (Button) findViewById(R.id.btn_title_edit);
        btn_back.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                ((Activity) getContext()).onBackPressed();
            }
        });

        btn_edit.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                // TODO
            }
        });
    }
}
```

- 关键源码启动方法：**ActivityThread#performLaunchActivity**

1. 从ActivityClientRecord中获取待启动Activity的ComponentName
2. 创建ContextImpl对象
3. 调用Instrumentation的newActivity方法（使用类加载器）创建Activity对象
4. 调用LoadedApk的makeApplication方法尝试创建Application对象（只会创建一次，也是通过类加载器）
5. 调用Activity的attach方法初始化一些重要数据（建立和Activity的关联）
6. 通过Instrumentation的callActivityOnCreate回调Activity的onCreated



### Service

> Service有两种状态：启动状态和绑定状态。启动状态下，主要用于执行后台计算；绑定状态下，主要用于和其他组件通信。Service可以同时处于两种状态。通过Context的 `startService` 启动，`bindService` 绑定；需要灵活地采用 `stopService` 和 `unBindService` 才能完全停止一个Service组件。
>
> ***ps:*** 启动Service时请务必使用<u>显式</u>Intent来确保应用的安全性，因为无法确定哪些Service将响应隐式Intent，且用户无法看到哪些Service已启动。从API21开始，使用隐式Intent调用 `bindService()`，系统会抛出异常。因此，请勿向Service声明intent filters。

- 切记在Service的 `onDestroy()` 方法中释放资源，防止Service被销毁后的内存占用
- 多次绑定同一个Service，只会执行一次 `onBind`，除非Service被终止了。
- 关键源码创建方法：**ActivityThread#handleCreateService**，与Activity流程类似。
- 关键源码绑定方法：**ActivityThread#handleBindService**，与Activity流程类似。



### BroadcastReceiver

- 标准广播

  - 静态注册    AndroidManifest.xml  **<receiver />**（不需要应用启动就可以接收广播）

  - 动态注册    `registerReceiver(receiver, filter);`（需要APP启动才能接收广播，未启动无法注册）

    **_ps:_** 动态注册广播最好在Activity的 `onResume()` 注册、`onPause()` 注销 `unregisterReceiver()`

  - 携带数据    setResultExtras(bundle);

  - 截断广播    abortBroadcast();

- 有序广播    OrderedBroadcast

- 本地广播

  - 只在应用内部接受和传递
  - 只能动态注册
  - `lbm = LocalBroadcastManager.getInstance(this);` 获取LocalBroadcastManager实例
  - `lbm.sendBroadcast(intent);` 发送本地广播
  
  ***ps:*** 若应用以API26或更高级别的平台为目标版本，则不能静态注册隐式广播（未明确针对您的应用的广播），但一些[不受此限制](https://developer.android.google.cn/guide/components/broadcast-exceptions?hl=zh_cn)的隐式广播除外。在大多数情况下，这可以使用[调度作业](https://developer.android.google.cn/topic/performance/scheduling?hl=zh_cn)来代替。



以下是有关收发广播的一些安全注意事项和最佳做法：

- 只要注册context有效，context注册的Receiver就可以接收广播。例如在Activity的context中注册，只要Activity没有被销毁，就会收到广播；在ApplicationContext注册，只要application在运行，就可以收到广播。

- 若无需向应用以外的组件发送广播，则应使用LocalBroadcastManager来收发本地广播。效率更高（无需进程间通信），并且无需考虑其他应用收发广播时带来的安全问题。本地广播可在应用中作为通用的发布/订阅事件总线，而不会产生任何系统级广播开销。

- 若有许多应用在其清单中注册接收相同的广播，可能会导致系统启动大量应用，从而对设备性能和用户体验造成严重影响。为避免发生这种情况，应优先使用context注册而不是在manifest中声明。有时，Android系统本身会强制使用context注册的接收器。例如，从API24开始，`CONNECTIVITY_ACTION` 广播只会传送给context注册的receiver。

- 请勿使用隐式Intent广播敏感信息。任何注册接收广播的应用都可以读取这些信息。可通过以下三种方式<u>控制哪些应用可以接收您的广播</u>：

  - 在发送广播时指定权限；
  - 从Android 4.0开始，在发送广播时指定[软件包](https://developer.android.google.cn/guide/topics/manifest/manifest-element?hl=zh_cn#package) `setPackage(String)`，系统会将广播限定到与该package匹配的一组应用；
  - 使用 `LocalBroadcastManager` 发送本地广播。

- 注册receiver时，任何应用都能向其发送潜在的恶意广播。可以通过以下三种方式<u>限制您的应用可以接收的广播</u>：

  - 在注册广播接收器时指定权限；
  - 对于静态注册的广播，将清单属性 [android:exported](https://developer.android.google.cn/guide/topics/manifest/receiver-element?hl=zh_cn#exported) 置为**false**，就不会接收来自应用外部的广播；
  - 使用 `LocalBroadcastManager` 限制应用只接收本地广播。

- ==广播操作的命名空间是全局性的。请确保在您自己的命名空间中编写操作名称和其他字符串，否则可能会无意中与其他应用发生冲突？==

- 当不再需要某个广播 或者 context 不再有效时，一定要注销接收器。**注意：**在何处注册及注销接收者，比如在Activity的`onCreate()`中注册了一个接收者，则应该在`onDestory()`中注销它，防止接收器泄露；若在`onResume()`中注册了一个接收器，则应该在`onPause()`中注销，防止多次注册（若不想在Activity暂停时接收广播，可以减少不必要的系统开销）。另外，不要在`onSaveInstanceState()`中注销，用户正常的回退操作不会调用此方法。

- 请勿从广播接收器启动 Activity，否则会影响用户体验，尤其是有多个接收器时。相反，可以考虑显示[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications?hl=zh_cn)。

- `onReceive()` 方法运行于主线程，因此不允许在其中执行耗时操作（避免ANR）或开启子线程（onReceive方法执行完成后，进程随时会被系统回收从而影响子线程的运行）。

  **解决方案：**

  - 在Receiver的 `onReceive()` 方法中调用 `goAsync()`，并将 `BroadcastReceiver.PendingResult` 传递给后台线程。这样，在从 `onReceive()` 返回后，广播仍可保持活跃状态。不过，即使采用这种方法，系统仍希望您快速地完成广播（**10秒** 以内）。为避免影响主线程，它允许您将工作移到另一个线程。
  - 使用JobScheduler从接收器调度JobService，则系统会知道该进程将继续执行工作。


```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "MyBroadcastReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {
        final PendingResult pendingResult = goAsync();
        Task asyncTask = new Task(pendingResult, intent);
        asyncTask.execute();
    }

    private static class Task extends AsyncTask<String, Integer, String> {
        private final PendingResult pendingResult;
        private final Intent intent;

        private Task(PendingResult pendingResult, Intent intent) {
            this.pendingResult = pendingResult;
            this.intent = intent;
        }

        @Override
        protected String doInBackground(String... strings) {
            StringBuilder sb = new StringBuilder();
            sb.append("Action:")
                .append(intent.getAction())
                .append("\n")
                .append("URI:")
                .append(intent.toUri(Intent.URI_INTENT_SCHEME).toString())
                .append("\n");
            String log = sb.toString();
            Log.d(TAG, log);
            return log;
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            // Must call finish() so the BroadcastReceiver can be recycled.
            pendingResult.finish();
        }
    }
}
```





### ContentProvider

> 一种是使用现有的内容提供器来操作相应程序中的数据；另一种是自定义内容提供器向其他程序提供数据访问接口。若一个应用通过内容提供器对外暴露数据，那么任何其他应用都可以对这部分数据进行访问。



- **ContentResolver**

访问内容提供器中共享的数据需要借助*ContentResolver*类，可通过*Context*的`getContentResolver()`方法获取其实例。*ContentResolver*类中提供了一系列操作数据的CRUD方法 `insert/update/delete/query`。这些方法都会接收一个URI参数，其主要由两部分组成：**authority** 和 **path**，为了避免冲突，*authority*通常以程序包名开头，*path*用于区分程序内不同的表，内容URI标准的格式为：

**content://com.example.app.provider/table**

内容URI字符串需要解析成URI对象才能作为参数传入，解析方式为：

```java
Uri uri = Uri.parse("content://com.example.app.provider/table1");
```

| query()方法参数 | 对应SQL部分               | 描述                             |
| :-------------- | ------------------------- | -------------------------------- |
| uri             | from table_name           | 指定查询某个应用程序下的某一张表 |
| projection      | select column1, column2   | 指定查询的列名                   |
| selection       | where column = value      | 指定where的约束条件              |
| selectionArgs   | --                        | 为where中的占位符提供具体的值    |
| sortOrder       | order by column1, column2 | 指定查询结果的排序方式           |

- `query()`

```java
Cursor cursor = getContentResolver().query(uri, 
                                           projection, 
                                           selection, 
                                           selectionArgs, 
                                           sortOrder);
if (cursor != null) {
    while(cursor.moveToNext()) {
        String column1 = cursor.getString(cursor.getColumnIndex("column1"));
        int column2 = cursor.getInt(cursor.getColumnIndex("column2"));
    }
    cursor.close();
}
```



- `insert()`

```java
ContentValues values = new ContentValues();
values.put("column1", "text");
values.put("column2", 1);
getContentResolver().insert(uri, values);
```



- `update()`

```java
ContentValues values = new ContentValues();
values.put("column1", "");
// selection和selectionArgs参数对想要更新的数据进行约束
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", 
                           new String[]{"text", "1"});
```



- `delete()`

```java
getContentResolver().delete(uri, "column2 = ?", new String[]{"1"});
```



- 读取系统联系人

1. *AndroidManifest.xml* 文件中声明通讯录权限；

```java
<uses-permission android:name="android.permission.READ_CONTACTS" />
```

2. 通过ContentProvider访问系统提供的URI，读取联系人信息。

```java
public class ContentProviderActivity extends AppCompatActivity {
    private ArrayAdapter<String> adapter;
    private List<String> contactList = new ArrayList<>();
    
    @Override
    protected void onCreate (@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 包含一个简单的ListView
        setContentView(R.layout.activity_content_provider);
        ListView lvContact = (ListView) findViewById(R.id.lvContact);
        adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, contactList);
        lvContact.setAdapter(adapter);
        // 动态权限申请
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS)
           != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, new String[] {
                Manifest.permission.READ_CONTACTS }, 1);
        } else {
            readContacts();
        }
    }
    
    private void readContacts() {
        Cursor cursor = null;
        try {
            // 查询联系人数据
            cursor = getContextResolver().query(ContactsContract
                .CommonDataKinds.Phone.CONTENT_URI, null, null, null, null);
            if (cursor != null) {
                while(cursor.moveToNext()) {
                    // 联系人姓名
                    String displayName = cursor.getString(cursor.getColumnIndex(
                    ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME));
                    // 联系人号码
                    String number = cursor.getString(cursor.getColumnIndex(
                    ContactsContract.CommonDataKinds.Phone.NUMBER));
                    contactList.add(displayName + "\n" + number);
                }
                adapter.notifyDataSetChanged();
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭cursor对象
            if (cursor != null) {
                cursor.close();
            }
        }
    }
    
    @Override
    public void onRequestPermissionsResult(int requestCode,
        @NonNull String[] permissions, @NonNull int[] grantResults) {
        // 动态权限申请回调
        switch(requestCode) {
            case 1:
                if (grantResults.length > 0 
                    && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    readContacts();
                } else {
                    Toast.makeText(this, "PERMISSION DENIED", Toast.LENGTH_SHORT).show();
                }
                break;
            default:
                break;
        }
    }
}
```



- 给应用创建自己的内容提供器

1. 自定义ContentProvider子类，重写全部6个方法。

```java
public class BookProvider extends ContentProvider {
    public static final int BOOK_DIR = 0; // Book表所有数据
    public static final int BOOK_ITEM = 1; // Book表单条数据
    public static final int CATEGORY_DIR = 2; // Category表所有数据
    public static final int CATEGORY_ITEM = 3; // Category表单条数据
    public static final String AUTHORITY = "app.alpha.lacosanostra.provider";
    private static UriMatcher uriMatcher;
    private MyDatabaseHelper dbHelper;
    // 初始化UriMatcher并添加匹配模板
    static {
        uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        uriMatcher.addURI(AUTHORITY, "book", BOOK_DIR);
        uriMatcher.addURI(AUTHORITY, "book/#", BOOK_ITEM);
        uriMatcher.addURI(AUTHORITY, "category", CATEGORY_DIR);
        uriMatcher.addURI(AUTHORITY, "category/#", CATEGORY_ITEM);
    }
     /*初始化内容提供器时调用，通常会在这里完成对数据库的创建和升级等操作；
       注意：只有当ContentResolver尝试访问我们程序中的数据时，内容提供器才会被初始化。*/
    @Override
    public boolean onCreate() {
        // 返回true表示内容提供器初始化成功，完成数据库的创建或升级
        dbHelper = new MyDatabaseHelper(getContext(), "BookStore.db", null, 1);
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
            String[] selectionArgs, String sortOrder) {
        SQLiteDatabase db = dbHelper.getReadableDatabase();
        Cursor cursor = null;
        switch (uriMatcher.match(uri)) {
        case BOOK_DIR:
            cursor = db.query("Book", projection, selection,
                              selectionArgs, null, null, sortOrder);
            break;

        case BOOK_ITEM:
            /*将内容URI的path/id部分以"/"分割放入一个字符串列表，
              所以get(0)是path，get(1)是id*/
            String bookId = uri.getPathSegments().get(1);
            // 查询id对应的行
            cursor = db.query("Book", projection, "id = ?",
                              new String[] { bookId }, null, null, sortOrder);
            break;

        case CATEGORY_DIR:
            cursor = db.query("Category", projection, selection,
                              selectionArgs, null, null, sortOrder);
            break;

        case CATEGORY_ITEM:
            String categoryId = uri.getPathSegments().get(1);
            cursor = db.query("Category", projection, "id = ?",
                              new String[] { categoryId }, null, null, sortOrder);
            break;

        default:
            break;
        }
        return cursor;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        SQLiteDatabase db = dbHelper.getWritableDatabase();
        // 表示这条新增数据的URI对象
        Uri uriReturn = null;
        switch (uriMatcher.match(uri)) {
        case BOOK_DIR:
        case BOOK_ITEM:
            long newBookId = db.insert("Book", null, values);
            uriReturn = Uri
                .parse("content://" + AUTHORITY + "/book/" + newBookId);
            break;

        case CATEGORY_DIR:
        case CATEGORY_ITEM:
            long newCategoryId = db.insert("Category", null, values);
            uriReturn = Uri
                .parse("content://" + AUTHORITY + "/category/" + newCategoryId);
            break;

        default:
            break;
        }
        return uriReturn;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        SQLiteDatabase db = dbHelper.getWritableDatabase();
        // 被删除的行数
        int deletedRows = 0;
        switch (uriMatcher.match(uri)) {
        case BOOK_DIR:
            deletedRows = db.delete("Book", selection, selectionArgs);
            break;

        case BOOK_ITEM:
            String bookId = uri.getPathSegments().get(1);
            deletedRows = db.delete("Book", "id = ?", new String[] { bookId });
            break;

        case CATEGORY_DIR:
            deletedRows = db.delete("Category", selection, selectionArgs);
            break;

        case CATEGORY_ITEM:
            String categoryId = uri.getPathSegments().get(1);
            deletedRows = db.delete("Category", "id = ?",
                                    new String[] { categoryId });
            break;

        default:
            break;
        }
        return deletedRows;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection,
            String[] selectionArgs) {
        SQLiteDatabase db = dbHelper.getWritableDatabase();
        // 受影响的行数
        int updatedRows = 0;
        switch (uriMatcher.match(uri)) {
        case BOOK_DIR:
            updatedRows = db.update("Book", values, selection, selectionArgs);
            break;

        case BOOK_ITEM:
            String bookId = uri.getPathSegments().get(1);
            updatedRows = db.update("Book", values, "id = ?",
                                    new String[] { bookId });
            break;

        case CATEGORY_DIR:
            updatedRows = db.update("Category", values, selection, selectionArgs);
            break;

        case CATEGORY_ITEM:
            String categoryId = uri.getPathSegments().get(1);
            updatedRows = db.update("Category", values, "id = ?",
                                    new String[] { categoryId });
            break;

        default:
            break;
        }
        return updatedRows;
    }
    /**
     * 根据传入的内容URI返回相应的MIME类型
     */
    @Override
    public String getType(Uri uri) {
        switch (uriMatcher.match(uri)) {
        case BOOK_DIR:
            return "vnd.android.cursor.dir/"
                + "vnd.app.alpha.lacosanostra.provider.book";
        case BOOK_ITEM:
            return "vnd.android.cursor.item/"
                + "vnd.app.alpha.lacosanostra.provider.book";
        case CATEGORY_DIR:
            return "vnd.android.cursor.dir/"
                + "vnd.app.alpha.lacosanostra.provider.category";
        case CATEGORY_ITEM:
            return "vnd.android.cursor.item/"
                + "vnd.app.alpha.lacosanostra.provider.category";
        }
        return null;
    }
}
```



2. 在manifest文件的application标签下注册自定义ContentProvider类。其中**authorities**属性即是BookProvider的*authority*，**enable**属性表示是否启用该内容提供器，**exported**属性表示是否允许外部程序访问该内容提供器。

```xml
<provider
    android:name="app.alpha.provider.BookProvider"
    android:authorities="${applicationId}.provider"
    android:enabled="true"
    android:exported="true" >
</provider>
```



- 方法中的Uri参数正是调用ContentResolver的CRUD方法时传递过来的。需要对其解析，从而分析出调用方期望访问的表和数据。内容URI的标准写法是 **content://com.example.app.provider/table**，表示该表中的所有数据，其实还可以在后面加上一个id如 **content://com.example.app.provider/table/1** 表示对应id的单条数据。此两种形式可以使用通配符来分别匹配：

  *****：表示匹配任意长度的任意字符；

  **#**：表示匹配任意长度的数字。

  如**content://com.example.app.provider/*** 能够匹配任意表；

  **content://com.example.app.provider/table/#** 能够匹配table表中任意一行。



- *UriMatcher*类可以轻松实现匹配内容URI的功能。

```java
/**
 * Add a URI to match, and the code to return when this URI is matched.
 * 这样，在调用UriMatcher的match()方法时，就可以通过返回的code判断对方期望访问的表。
 */
public void addURI(String authority, String path, int code) {}
```



- MIMIE格式规定：

  - 必须以 **vnd.** 开头；
  - 若内容URI以路径结尾，则后接 **android.cursor.dir/**，若以id结尾，则后接 **android.cursor.item/**；
  - 最后接上 **vnd.\<authority>.\<path>**。（id不用写）

  即：**vnd.android.cursor.dir/vnd.\<authority>.\<path>**

  或 **vnd.android.cursor.item/vnd.\<authority>.\<path>**



- 新建一个测试程序来访问上文定义的BookProvider。

```java
public class BookResolver extends AppCompatActivity {
    private ContentResolver resolver;
    // 记录最新行的id
    private String newId;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 该布局中有4个按钮，增删改查
        setContentView(R.layout.content_pr);
        resolver = getContentResolver();
        Button query = (Button) findViewById(R.id.btn_query);
        Button insert = (Button) findViewById(R.id.btn_insert);
        Button update = (Button) findViewById(R.id.btn_update);
        Button delete = (Button) findViewById(R.id.btn_delete);

        query.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    Uri uri = Uri
                        .parse("content://app.alpha.lacosanostra.provider/book");
                    Cursor cursor = resolver.query(uri, null, null, null, null);
                    if (cursor != null) {
                        while (cursor.moveToNext()) {
                            String name = cursor.getString(cursor
                                    .getColumnIndex("name"));
                            String author = cursor.getString(cursor
                                    .getColumnIndex("author"));
                            int pages = cursor.getInt(cursor
                                    .getColumnIndex("pages"));
                            double price = cursor.getDouble(cursor
                                    .getColumnIndex("price"));
                            Log.i("=-=-=SQLite3 Database=-=-=",
                                  "The book named '" + name
                                  + "' which written by " + author
                                  + " is price of $" + price
                                  + " and has " + pages + " pages.");
                        }
                        cursor.close();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });

        insert.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    Uri uri = Uri
                            .parse("content://app.alpha.lacosanostra.provider/book");
                    ContentValues values = new ContentValues();
                    values.put("name", "A Clash of Kings");
                    values.put("author", "George Martin");
                    values.put("pages", 1040);
                    values.put("price", 22.85);
                    Uri newUri = resolver.insert(uri, values);
                    newId = newUri.getPathSegments().get(1);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });

        update.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    Uri uri = Uri
                            .parse("content://app.alpha.lacosanostra.provider/book/"
                                   + newId);
                    ContentValues values = new ContentValues();
                    values.put("name", "A Storm of Swords");
                    values.put("pages", 1216);
                    values.put("price", 24.05);
                    resolver.update(uri, values, null, null);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });

        delete.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    Uri uri = Uri
                            .parse("content://app.alpha.lacosanostra.provider/book/"
                                   + newId);
                    resolver.delete(uri, null, null);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
```



#### FileProvider

>[FileProvider](https://developer.android.google.cn/reference/androidx/core/content/FileProvider)是一个通过content:// 替换file:/// URI来提升应用程序关联文件共享安全性的特殊ContentProvider子类。一个content:// URI允许授予应用对文件的临时读/写权限

1. [Defining a FileProvider](https://developer.android.google.cn/reference/androidx/core/content/FileProvider#ProviderDefinition)

```xml
<!--
name: 使用support库时应为 android.support.v4.content.FileProvider
authorities: 建议 包名 + ".fileprovider"，如下
exported: 设置 false 提升安全性
grantUriPermissions: 设置 true 允许授予文件临时访问权限
-->
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    ...
</provider>
```

2. [Specifying Available Files](https://developer.android.google.cn/reference/androidx/core/content/FileProvider#SpecifyFiles)

```xml
<!-- 文件res/xml/file_paths.xml
name: A URI path segment. 用于隐层真正的子目录 path
path: 真正的子目录（必须是子目录，不能是单个或多个文件，也不能是通配符指定的文件子集；使用 . 代表所有子目录）
-->
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <files-path name="name" path="path" /> <!-- Context#getFilesDir -->
    <cache-path name="name" path="path" /> <!-- Context#getCacheDir -->
    <external-path name="name" path="path" /> <!-- Environment#getExternalStorageDirectory -->
    <external-files-path name="name" path="path" /> <!-- Context#getExternalFilesDir -->
    <external-cache-path name="name" path="path" /> <!-- Context#getExternalCacheDir -->
    <external-media-path name="name" path="path" /> <!-- Context#getExternalMediaDirs -->
    <!-- 示例，注意 / -->
    <files-path name="my_images" path="images/"/>
</paths>
```

```xml
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <!-- 关联路径定义文件（name固定值）-->
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
```

3. [Retrieving the Content URI for a File](https://developer.android.google.cn/reference/androidx/core/content/FileProvider#GetUri)

```java
File imagePath = new File(Context.getFilesDir(), "images");
File newFile = new File(imagePath, "default_image.jpg");
Uri contentUri = FileProvider.getUriForFile(getContext(), "com.mydomain.fileprovider", newFile);
//结果为 content://com.mydomain.fileprovider/my_images/default_image.jpg，注意是my_images而不是images
```

***ps:*** `ParcelFileDescriptor fd = getContentResolver().openFileDescriptor(uri, "rw");`

4. [Granting Temporary Permissions to a URI](https://developer.android.google.cn/reference/androidx/core/content/FileProvider#Permissions)

```java
Uri contentUri = FileProvider.getUriForFile(..);
//方式一（指定package）
Context.grantUriPermission(); //申请授予临时访问权限（revokeUriPermission或重启设备后失效）
Context.revokeUriPermission(); //撤销权限
//方式二
Intent shareContentIntent = new Intent();
shareContentIntent.setData(contentUri);
// shareContentIntent.setClipData(ClipData.newRawUri("", contentUri)); //支持API16~22[包含]设备
shareContentIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION
        | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
shareContentIntent.resolve..
```

***ps:*** 使用Intent申请授予的权限，在接收Activity所在栈处于活跃状态时有效（若是Service，则处于运行状态时有效），并且会自动扩展至该APP的其它组件，Activity栈结束后自动撤销。==应用内也需要申请权限吗？==

5. [Serving a Content URI to Another App](https://developer.android.google.cn/reference/androidx/core/content/FileProvider#ServeUri)

```java
startActivityForResult();
//提供选择文件的UI并返回Content URI
setResult();
```



==如何获取其他应用程序暴露的内容URI？？==



 在Activity间共享数据的辅助类 `android.support.v4.app.ShareCompat`



## AndroidManifest.xml

[清单元素参考](https://developer.android.google.cn/guide/topics/manifest/manifest-intro#reference)

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="al.app.example"
    android:versionCode="1"
    android:versionName="1.0">
    <!-- Manifest package attribute (设置R类的命名空间并解析清单类名称) can be different from the Gradle applicationId,
           but it discarded and replaced by the Gradle applicationId at the end of build.
           The versionCode/Name is always overridden by the value specified in the Gradle build script when develop with Android Studio. -->

    <!-- Beware that these values are always overridden by the build.gradle file. -->
    <!-- <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="29" /> -->

    <!-- Declaring features required by the app.
           Android系统本身在安装应用之前(时)并不会检查设备是否提供了声明的功能，这些声明仅作为说明参考；
           不过，其他服务 (Googlel Play) 可能会在处理应用的过程中检查其<uses-feature>声明，并以此作为条件过滤不符合要求的设备. -->
    <uses-feature android:name="android.hardware.camera" android:required="true" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <!-- 同级别的元素通常不分先后顺序，但 <application> 必须是 <manifest> 中的最后一个元素 -->
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <meta-data
            android:name="al.app.example.application"
            android:value="meta-data value under label application." />

        <!-- This name is resolved to 'al.app.example.MainActivity' based upon the package attribute. -->
        <!-- configChanges属性可以在运行时发生配置变更后阻止Activity的销毁和重启, 但应避免使用该属性.
               另注意, 在面向API13或更高版本时, 应同时声明 'orientation|screenSize', 因为设备旋转后screenSize也会发生配置变更. -->
        <activity
            android:name=".MainActivity"
            android:configChanges="orientation|screenSize">
            <meta-data
            android:name="al.app.example.activity"
            android:resource="@string/app_name" />

            <!-- 使用android.intent.action.或android.intent.category.前缀引入系统定义的标准Action/Category.
                   另外, 自定义action/category最好也将包名作为前缀以确保唯一性. -->
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!-- <activity-alias> 元素必须跟在别名对应的 <activity> 之后 -->
        <!-- <activity-alias ... /> -->

    </application>
</manifest>
```

- [\<meta-data>](https://developer.android.google.cn/guide/topics/manifest/meta-data-element)

> android:value定义meta-data（硬编码或引用资源）时，通过 `Bundle.getXxx("name")` 即可获取对应类型的资源值；
>
> android:resource定义的meta-data（必须是资源引用），只能先通过 `Bundle.getInt("name")` 获取引用资源的ID（不能直接获取引用资源的值），然后再通过资源ID访问对应类型的资源。

```java
try {
    // application标签下的meta-data要使用ApplicationInfo解析
    ApplicationInfo appInfo = getPackageManager()
        .getApplicationInfo(getPackageName(), PackageManager.GET_META_DATA);
    if (appInfo.metaData != null) {
        Log.i("meta-data", appInfo.metaData.getString("al.app.example.application"));
    }
    // activity标签下的meta-data要使用ActivityInfo解析
    ActivityInfo actInfo = getPackageManager()
        .getActivityInfo(getComponentName(), PackageManager.GET_META_DATA);
    if (actInfo.metaData != null) {
        Log.i("meta-data", getResources().getString(actInfo.metaData.getInt("al.app.example.activity")));
    }
} catch (NameNotFoundException e) {
    e.printStackTrace();
}
```

- `android:windowSoftInputMode`

  Activity主窗口与包含屏幕软键盘的窗口之间的交互方式。该属性的设置会影响两点内容：

  - 当 Activity 获取焦点时*(when the activity becomes the focus of user attention.)*，软键盘的状态为隐藏or可见。
  - 是否将 Activity 主窗口尺寸<u>调小</u>*(Resize)*以为软键盘腾出空间；或当软键盘遮盖部分 Activity 窗口时，<u>平移</u>*(Pan)*其内容以使当前焦点可见。

  ***ps:*** 该设置必须是下表所列的其中一项值，或一个 ***state*** 和一个 ***adjust*** 的组合。在任一组中设置多个同类值（如多个 ***state*** 值）均会产生未定义的结果*(has undefined results)*。各值之间使用 **|** 进行分隔。如：

  `<activity android:windowSoftInputMode="stateVisible|adjustResize" .. >`

| 值                   | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| *stateUnspecified*   | *对软键盘行为的默认设置：*不指定软键盘的状态（隐藏/可见），由系统选择合适的状态或依赖主题中的设置。 |
| *stateUnchanged*     | 当 Activity 转至前台时保留软键盘最后所处的状态（隐藏/可见）。 |
| *stateHidden*        | ==当进入一个 Activity，而不是finish后返回上一个 Activity 时隐藏软键盘。==*(The soft keyboard is made visible when the user chooses the       activity — that is, when the user affirmatively navigates forward       to the activity, rather than backs into it because of leaving another activity.)* |
| *stateAlwaysHidden*  | 当 Activity 的主窗口有输入焦点时始终隐藏软键盘。             |
| *stateVisible*       | 在正常且适当的情况下*(when the user is navigating forward to the activity's main window)*显示软键盘。 |
| *stateAlwaysVisible* | ==当进入一个 Activity，而不是finish后返回上一个 Activity 时显示软键盘。== |
| *adjustUnspecified*  | *对 Activity 主窗口行为的默认设置：*不指定主窗口的默认行为*(resize or pan)*。由系统根据窗口中<u>是否存在可滚动的布局视图选择一种模式</u>。若存在可滚动视图，系统会选择Resize模式*(the window will be resized)*，但前提是可通过滚动操作在较小区域内看到原窗口内的所有内容。 |
| *adjustResize*       | 始终调整 Activity 主窗口的尺寸，以为屏幕上的软键盘腾出空间。 |
| *adjustPan*          | 始终自动平移窗口的内容避免软键盘遮挡，保持当前输入焦点始终可见。这通常不如Resize模式，因为需关闭软键盘后才能和被遮挡的部分进行交互。 |
| *adjustNothing*      | *Don't resize or pan the window to make room for the soft input area; the window is never adjusted for it.* |

- 解析manifest文件：`PackageParser#parseBaseApplication`



## Theme & Style

[样式和主题背景|Android Developers](https://developer.android.google.cn/guide/topics/ui/look-and-feel/themes)

[深入理解Android Style/Theme/Attr/Styleable/TypedArray|简书](https://www.jianshu.com/p/067500c4b634)

样式属性资源表：[framework themes](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes.xml) ^vpn^  [framework styles](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/styles.xml) ^vpn^  [support themes](https://chromium.googlesource.com/android_tools/+/HEAD/sdk/extras/android/support/v7/appcompat/res/values/themes.xml) ^vpn^  [support attrs](https://chromium.googlesource.com/android_tools/+/HEAD/sdk/extras/android/support/v7/appcompat/res/values/attrs.xml) ^vpn^

- 支持库中的属性名称不使用 `android:` 前缀，该前缀仅用于Android框架内的属性。
- 特定于版本的样式也可以扩展自基础版本样式，如 `res/values-v21/styles.xml` 中的样式可扩展自 `res/values/styles.xml` 中的样式。



**创建并应用样式**

------

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="GreenText" parent="TextAppearance.AppCompat">
        <!-- name指定原本会在布局中作为xml属性来使用的属性，<item>元素中的值即为该属性的值 -->
        <item name="android:textColor">#00FF00</item>
    </style>
</resources>
```

```xml
<TextView
    style="@style/GreenText"
    ... />
```

***ps:*** 只要View接受，该样式中指定的每个属性都会应用于该视图。对于不接受的属性，View会将其忽略。**注意**：只有添加了 *style* 属性的元素才会收到这些样式属性，任何子视图都不会应用这些样式。若希望子视图继承样式，需应用具有 `android:theme` 属性的样式。



**扩展和自定义样式**

------

- 扩展Android内建样式，必须使用parent属性 `<style name="MyStyle" parent="TextViewStyle">`。
- 扩展自定义样式，可使用parent属性或点分表示法 `<style name="MyStyle.Red">` // name以 _MyStyle._ 开头，则继承MyStyle样式。

```xml
<!-- 继承Android平台默认兼容文本外观，修改文本颜色 -->
<style name="GreenText" parent="TextAppearance.AppCompat">
    <item name="android:textColor">#00FF00</item>
</style>
<!-- 从GreenText样式中继承了所有样式属性，并扩展修改了文本大小属性 -->
<style name="GreenText.Large">
    <item name="android:textSize">20sp</item>
</style>

<style name="CustomStyle" >
    <!-- @符号 引用已经定义且存在的资源，直接引用：@string/ 、@drawable/ -->
    <item name="android:windowBackground">@drawable/screen_background_white</item>
    <item name="android:panelColorForeground">#FFFFFF</item>
    <!-- ?符号 引用当前theme中已定义的资源，通过"?+<item>中name的属性值"引用 -->
    <item name="panelBackground">?android:panelColorForeground</item>
</style>
```

***ps:*** 若使用点分表示法来扩展样式，且同时还包含 `parent` 属性，那么父样式将替换通过点分表示法继承的<u>任何</u>样式。查看所有Style或Theme可用样式属性的参考资料，可参阅 [R.attr](https://developer.android.google.cn/reference/android/R.attr) 或 style所对应的View类，所有视图都支持[基础View类中的XML属性](https://developer.android.google.cn/reference/android/view/View#lattrs)。



**将样式应用为主题**

------

主题和样式的创建方式相同，不同之处在于其应用方式：不再对视图应用具有 `style` 属性的样式，而是在AndroidManifest.xml文件中的 `<application>` 或 `<activity>` 标签应用具有 `android:theme` 属性的主题。

```xml
<manifest ... >
    <application android:theme="@style/Theme.AppCompat" ... >
    </application>
</manifest>
```

现在，应用或Activity的每个视图都会应用指定主题中定义的样式。若视图仅支持在样式中声明的部分属性，则仅应用这些属性，而忽略不支持的属性。从API21开始，还可以在布局文件中为视图指定 `android:theme` 属性，这将修改该视图及其任何子视图的主题。



**样式层次结构**

------

>考虑到Android的样式层次结构，应尽量使用主题和样式以保持UI一致性。若在多个位置指定了相同的属性，则依据较高优先级覆盖重复属性。

Android样式属性应用优先级从高到低依次为：

1. 通过文本span将字符或段落级样式应用到 `TextView`

   `text.setText(new SpannableString(..));`

2. 以编程方式应用样式属性

   `text.setTextColor();`

   `text.setTextAppearance();`

3. 将单独的属性直接应用到View

   `android:textColor`

   `android:textAppearance`

4. 将样式应用到View

   `style="@style/GreenText.Large"`

5. 默认样式

   `this(context, attrs, R.attr.defStyleAttr);`

   `this(context, attrs, 0, R.style.defStyleRes);`

   ***ps:*** 这里若view设置了style属性，即会传递到第3个参数==？==`R.attr.defStyleAttr` 即是个样式资源引用如 `@style/xxx`

   [深入理解Android View的构造函数](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0806/4575.html#defaultstyleattribute)

6. 将主题应用到View集合、Activity或整个Application

   `android:theme`

7. 应用某些特定于View的样式，例如为 `TextView` 的 `TextAppearance`

   `android:textAppearance`

***ps:*** 一个View同时应用了 `style` 和 `android:theme` 属性，`style` 属性优先级更高。*TextAppearance* 可用于定义特定于文本的样式，同时让 View的样式可用于其他用途。但请注意，若直接在View或样式中定义任何文本属性，这些值将会覆盖TextAppearance中重复定义的值。



**自定义默认主题和Widget样式**

------

创建项目时会在 `res/values/styles.xml` 中提供默认应用主题，并定义了应用内基础颜色设计。

```xml
<!-- Base application theme. -->
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <!-- Customize your theme here. -->
    <item name="colorPrimary">@color/colorPrimary</item>
    <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    <item name="colorAccent">@color/colorAccent</item>

    <!-- 另外，还可以替换所需的任何其他样式。如更改Activity窗口背景色 -->
    <item name="android:windowBackground">@color/activityBackground</item>
</style>
```

对应 `res/values/colors.xml` 中相应的颜色资源引用，更新这些值即可快速自定义应用的颜色设计。更改前可通过 [Material颜色工具](https://material.io/resources/color/#!/?view.left=0&view.right=0) 预览效果。

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!--   color for the app bar and other primary UI elements -->
    <color name="colorPrimary">#3F51B5</color>
    <!--   a darker variant of the primary color, used for
           the status bar (on Android 5.0+) and contextual app bars -->
    <color name="colorPrimaryDark">#303F9F</color>
    <!--   a secondary color for controls like checkboxes and text fields -->
    <color name="colorAccent">#FF4081</color>
</resources>
```



Android框架和支持库中的每个Widget都有一个默认样式。可使用布局文件中的 `style` 属性来自定义：

```xml
<Button
    style="@style/Widget.AppCompat.Button.Borderless"
    ... />
```

若要更改应用内所有按钮样式，可在应用主题中覆盖声明 `buttonStyle` 属性

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    ...
    <item name="buttonStyle">@style/Widget.AppCompat.Button.Borderless</item>
</style>
```



## Fragment

- 自定义Fragment子类，覆写 `onCreateView()` 等相关方法，如：
```java
/**
 * A simple {@link Fragment} subclass.
 * Use the {@link MyFragment#newInstance} factory method to
 * create an instance of this fragment.
 */
public class MyFragment extends Fragment {
    private static final String ARG_PARAM = "app.alpha.ARG_PARAM";
    private String mParam;

    // Required empty public constructor
    public MyFragment() {}

    /**
     * Use this factory method to create a new instance of
     * this fragment using the provided parameters.
     *
     * @param Parameter
     * @return A new instance of fragment MyFragment.
     */
    public static MyFragment newInstance(String param) {
        MyFragment fragment = new MyFragment();
        // 传递数据
        Bundle args = new Bundle();
        args.putString(ARG_PARAM, param);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onAttach(Context context) {
        super.onAttach(context);
        if (context instanceof OnXxxListener) {
            /*Fragment与Activity使用回调进行数据交互时，
            可以在此拿到接口实例，省去定义setOnXxxListener方法*/
        }
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mParam = getArguments().getString(ARG_PARAM);
        }
    }

    @Override
    public void onHiddenChanged(boolean hidden) {
        super.onHiddenChanged(hidden);
    }

    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        // 加载Fragment布局
        return inflater.inflate(R.layout.fragment, container, false);
    }

    @Override
    public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        initView();
        ...
    }
    
    @Override
    public void onDetach() {
        // TODO
        super.onDetach();
    }
}
```
- 静态加载
```xml
<!-- name属性值为自定义Fragment的全限定类名 -->
<fragment
    android:name="com.example.fragment.MyFragment"
    ... />
```

- 动态加载
```java
// 获取自定义Fragment类实例
MyFragment f = MyFragment.newInstance();
// 获取与activity关联的FragmentManager
FragmentManager mgr = getSupportFragmentManager();
// 事务FragmentTransaction
mgr.beginTransaction()
        // 动态加载Fragment(用自定义Fragment动态替换R.id.container布局) replace()/add()
        .replace(R.id.container, f)
        // 添加事务到返回栈，参数一般传null
        .addToBackStack(null)
        // 提交事务
        .commit();
```
***ps:*** 每个宿主都有一个与其关联的FragmentManager，用于管理其子fragments。

- 管理Fragment内部的子级fragment，使用 `Fragment#getChildFragmentManager`。

- FragmentManager可以执行add/replace等更改行为，每组更改作为一个单元（即FragmentTransaction）统一提交（<u>不能重复提交</u>）。

- add/replace的区别在于，replace相当于remove再add，因此popBackStack时前一个fragment会重新从 `onCreateView` 开始回调生命周期。并且若连续提交add和replace操作，add的fragment也不会回调 `onStart` 和 `onResume`，而是直接开启replace的fragment的生命周期。

- *AllowingStateLoss* 指可以在宿主Activity执行到 `onSaveInstanceState()`/`onStop()` 或之后进行提交操作。
  
  - `commit()`
  - `commitAllowingStateLoss()`
  - `commitNow()`
  - `commitNowAllowingStateLoss()`
  
  ***ps:*** `commit` 提交是异步操作（handler），立即提交需要使用 `commitNow`，而 `commitNow` 不允许操作回退栈。此时，可在事务已被提交但尚未执行时（如何判断？）调用 `FragmentManagerImpl#executePendingTransactions` 达到相同目的。
  
- show/hide 操作不影响fragment的生命周期，仅回调 `Fragment#onHiddenChanged`。

- 事务操作具有原子性，在同一事务中可通过 `setReorderingAllowed(true)` 优化相反的操作，如 detach/attach 相互抵消避免UI重建。另外，注意 attach/detach 并不对应fragment的 `onAttach` 和 `onDetach` 生命周期，而是与 `onCreateView` 和 `onDestroyView` 相关联。

- `FragmentManager#getFragments` 返回一个包含所有added的fragment list，数量不一定和 `FragmentManager#getBackStackEntryCount` 相等。

- Fragment没有onShowListener之类的监听器，要实现类似功能：

  - 普通fragment container：可覆写 `onHiddenChanged(boolean)`，并在fragment added后同时hide，以便能够在首次显示时触发回调，否则只会在显隐状态发生改变时回调。
  - ViewPager：可覆写 `setUserVisibleHint(boolean)` 进行相应判断。



- 数据传递

  **Fragment与Activity之间**

  ------

  - `getActivity()` 获取Activity实例，调用Activity方法
  - `getSupportFragmentManager().findFragmentById(R.id.fragment);` 获取Fragment实例，调用Fragment方法
  - Activity向Fragment传递数据（Bundle，与Fragment之间向后传递数据方式相同）
  - Fragment向Activity传递数据（接口回调，在fragment中定义接口，并在activity中实现它）
  - 限定符？

  **Fragment与Fragment之间**

  ------

  - 向后传递

  ```java
  //FirstFragment中添加数据
  Bundle args = new Bundle();
  args.putString("key", "value");
  firstFragment.setArguments(args);
  ```

  ```java
  //SecondFragment中接收数据
  if (getArguments() != null) {
      String value = getArguments().getString("key");
  }
  ```

  - **向前传递**（像Activity一样处理BackStackRecord中fragments的回调数据：`onActivityResult`）

  ```java
  //启动并入栈SecondFragment前，先将SecondFragment的target设置为FirstFragment
  secondFragment.setTargetFragment(this, REQUEST_CODE);
  getFragmentManager()
          .beginTransaction()
          .add(R.id.container, secondFragment, SecondFragment.TAG)
          .addToBackStack(null)
          .commit();
  ```

  ```java
  //在SecondFragment将自身出栈(popBackStack)之前，回调数据至FirstFragment的onActivityResult中
  if (getTargetFragment() != null) {
      getTargetFragment().onActivityResult(getTargetRequestCode(), RESULT_OK, null);
      FragmentManager mgr = getFragmentManager();
      if (mgr != null) mgr.popBackStack();
  }
  ```

  ```java
  //覆写FirstFragment#onActivityResult接收回调数据
  @Override
  public void onActivityResult(int requestCode, int resultCode, Intent data) {
      super.onActivityResult(requestCode, resultCode, data);
      if (REQUEST_CODE == requestCode && RESULT_OK == resultCode) {
          // TODO
      }
  }
  ```

  - [Fragment的新特性 - FragmentResult APIs](https://juejin.cn/post/6844904151508320263)



- 转场效果

  **动画** animation/animator

  ------

  - `FragmentTransaction#setCustomAnimations`

  ```java
  getSupportFragmentManager()
          .beginTransaction()
          // 自定义转场动画 (支持R.anim/R.animator)
          .setCustomAnimations(
              R.anim.slide_in,  // enter
              R.anim.fade_out,  // exit
              R.anim.fade_in,   // popEnter
              R.anim.slide_out  // popExit
          )
          .add()
          ...
          .commit();
  ```
  
  ***ps:*** `setCustomAnimations` 只会对其之后的操作应用动画（可多次设置，注意顺序），但在==内存重启？==后所有设置的动画效果都将失效。replace操作也会执行前一个fragment的exit动画。
  
  - `Fragment#onCreateAnimation` / `Fragment#onCreateAnimator`
  
  以下两个覆写方法的nextAnim参数资源类型由setCustomAnimations方法的传入资源类型决定（*R.anim* 或 *R.animator*）
  
  ```java
  @Override
  public Animation onCreateAnimation(int transit, boolean enter, int nextAnim) {
      Animation animation;
      switch (transit) {
          case FragmentTransaction.TRANSIT_FRAGMENT_OPEN:
              animation = AnimationUtils.loadAnimation(getContext(),
                      enter ? R.anim.open_enter : R.anim.open_exit);
              break;
          case FragmentTransaction.TRANSIT_FRAGMENT_CLOSE:
              animation = AnimationUtils.loadAnimation(getContext(),
                      enter ? R.anim.close_enter : R.anim.close_exit);
              break;
          case FragmentTransaction.TRANSIT_FRAGMENT_FADE:
              animation = AnimationUtils.loadAnimation(getContext(),
                      enter ? R.anim.fade_enter : R.anim.fade_exit);
              break;
          default:
              animation = super.onCreateAnimation(transit, enter, nextAnim);
      }
      return animation;
  }
  ```
  
  ```java
  @Override
  public Animator onCreateAnimator(int transit, boolean enter, int nextAnim) {
      Animator animator;
      switch (transit) {
          case FragmentTransaction.TRANSIT_FRAGMENT_OPEN:
              animator = AnimatorInflater.loadAnimator(getContext(),
                      enter ? R.animator.open_enter : R.animator.open_exit);
              break;
          case FragmentTransaction.TRANSIT_FRAGMENT_CLOSE:
              animator = AnimatorInflater.loadAnimator(getContext(),
                      enter ? R.animator.close_enter : R.animator.close_exit);
              break;
          case FragmentTransaction.TRANSIT_FRAGMENT_FADE:
              animator = AnimatorInflater.loadAnimator(getContext(),
                      enter ? R.animator.fade_enter : R.animator.fade_exit);
              break;
          default:
              animator = super.onCreateAnimator(transit, enter, nextAnim);
      }
      return animator;
  }
  ```
  
  ***ps:*** 自定义/标准转场动画都对 show/hide 操作生效，并且不受内存重启影响。
  
  
  
  **过渡** transition
  
  ------
  
  不要同时使用动画和过渡两种效果。
  
  - `FragmentTransaction#setTransition`
  
  ```java
  getSupportFragmentManager()
          .beginTransaction()
          /* 预设标准转场动画之后，添加和移除Fragment都会有效
              FragmentTransaction.TRANSIT_NONE 无动画
              FragmentTransaction.TRANSIT_FRAGMENT_OPEN 标准开启动画
              FragmentTransaction.TRANSIT_FRAGMENT_CLOSE 标准关闭动画
              FragmentTransaction.TRANSIT_FRAGMENT_FADE 淡入淡出动画 */
          .setTransition(@Transit int transit)
          ...
          .commit();
  ```
  
  - `Fragment#setEnterTransition` / `Fragment#setExitTransition`
  
  ```java
  public class FragmentA extends Fragment {
      @Override
      public View onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          TransitionInflater inflater = TransitionInflater.from(requireContext());
          setExitTransition(inflater.inflateTransition(R.transition.fade));
      }
  }
  ```
  
  ```java
  public class FragmentB extends Fragment {
      @Override
      public View onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          TransitionInflater inflater = TransitionInflater.from(requireContext());
          setEnterTransition(inflater.inflateTransition(R.transition.slide_right));
      }
  }
  ```
  
  - `Fragment#setSharedElementEnterTransition` / `Fragment#setSharedElementReturnTransition`
  
  1. 为每个共享View元素分配唯一的过渡名称
  
     ```java
     public class FragmentA extends Fragment {
         @Override
         public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
             ...
             ImageView itemImageView = view.findViewById(R.id.item_image);
             ViewCompat.setTransitionName(itemImageView, "item_image");
         }
     }
     ```
  
     ```java
     public class FragmentB extends Fragment {
         @Override
         public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
             ...
             ImageView heroImageView = view.findViewById(R.id.hero_image);
             ViewCompat.setTransitionName(heroImageView, "hero_image");
         }
     }
     ```
  
     ***ps:*** API21开始还可以通过在xml中声明 `android:transitionName` 属性分配过渡名称。
  
  2. 向FragmentTransaction分别添加每个共享元素view及其在下一个fragment中对应view的transaction name
  
     ```java
     getSupportFragmentManager()
             .beginTransaction()
             .addSharedElement(itemImageView, "hero_image")
             .addSharedElement(..)
             ...
             .replace(R.id.container, FragmentB.newInstance())
             .addToBackStack(null)
             .commit();
     ```
  
  3. 设置共享元素过渡效果
  
     ```java
     public class FragmentB extends Fragment {
         @Override
         public View onCreate(Bundle savedInstanceState) {
             super.onCreate(savedInstanceState);
             Transition transition = TransitionInflater.from(requireContext())
                 .inflateTransition(R.transition.shared_image);
             setSharedElementEnterTransition(transition);
             setSharedElementReturnTransition();
         }
     }
     ```
  
     ```xml
     <!-- res/transition/shared_image.xml -->
     <transitionSet>
         <changeImageTransform />
     </transitionSet>
     ```
  
     ***ps:*** ChangeImageTransform是系统内建的默认过渡效果之一，可以继承 `Transition` 自定义过渡效果。默认情况下，Enter Transition也会作为Return Transition共用，可以通过 `Fragment#setSharedElementReturnTransition` 指定不同的返回过渡效果。
  
  - 延迟过渡效果
  
    若过渡元素从网络上异步加载，过渡可能会受到阻碍，此时需要延迟过渡，前提是允许事务执行优化操作。
  
    ```java
    getSupportFragmentManager()
            .beginTransaction()
            .setReorderingAllowed(true)
            ...
            .commit();
    ```
  
    然后在过渡目的fragment的 `onViewCreated()` 中调用 `Fragment#postponeEnterTransition`。
  
    ```java
    public class FragmentB extends Fragment {
        @Override
        public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
            ...
            postponeEnterTransition();
        }
    }
    ```
  
    一旦共享元素数据准备完毕，再调用 `Fragment#startPostponedEnterTransition` 重启过渡。
  
    ```java
    public class FragmentB extends Fragment {
        @Override
        public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
            ...
            Glide.with(this)
                .load(url)
                .listener(new RequestListener<Drawable>() {
                    @Override
                    public boolean onLoadFailed(...) {
                        startPostponedEnterTransition();
                        return false;
                    }
    
                    @Override
                    public boolean onResourceReady(...) {
                        startPostponedEnterTransition();
                        return false;
                    }
                }).into(headerImage)
        }
    }
    ```
  
    ***ps:*** `androidx.fragment.app.Fragment#postponeEnterTransition(long, TimeUnit)` 可以延迟指定时长。
    
  - Fragment包含RecyclerView时使用共享元素过渡
  
    ```java
    public class FragmentA extends Fragment {
        @Override
        public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
            postponeEnterTransition();
    
            final ViewGroup parentView = (ViewGroup) view.getParent();
            // Wait for the data to load
            viewModel.getData()
                .observe(getViewLifecycleOwner(), new Observer<List<String>>() {
                    @Override
                    public void onChanged(List<String> list) {
                        // Set the data on the RecyclerView adapter
                        adapter.setData(list);
                        // Start the transition once all views have been
                        // measured and laid out
                        parentView.getViewTreeObserver()
                            .addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
                                parentView.getViewTreeObserver()
                                    .removeOnPreDrawListener(this);
                                startPostponedEnterTransition();
                                return true;
                            });
                    }
            });
        }
    }
    ```
  
    ***ps:*** `ViewTreeObserver.OnPreDrawListener` 被注册给父view以确保fragment's views已经完成测量与布局过程并且准备好在启动延迟过渡之前开始绘制。
  
    注意，当从一个使用RecyclerView和共享元素的fragment过渡到另一个fragment时，前者必须postpone，以确保后者出栈时能够正确执行Return Transition。[:link:](https://developer.android.google.cn/guide/fragments/animate#recyclerview)
  
    此外，也不能在RecyclerView子项xml中声明transition name属性（复用问题），可以在ViewHolder绑定视图时指定唯一个过渡名称：
  
    ```java
    public class ExampleViewHolder extends RecyclerView.ViewHolder {
        private final ImageView image;
        ExampleViewHolder(View itemView) {
            super(itemView);
            image = itemView.findViewById(R.id.item_image);
        }
    
        public void bind(String id) {
            ViewCompat.setTransitionName(image, id);
            ...
        }
    }
    ```
  
    示例：[Android Fragment Transitions: RecyclerView to ViewPager Sample](https://github.com/android/animation-samples/tree/main/GridToPager)
  
- 生命周期





## 事件监听

- 基于监听的事件处理（为界面组件绑定事件监听器）
  - 匿名内部类(最常用)/内部类/外部类(实现View.OnClickListener接口)作为事件监听器类
  ```java
  btn.setOnClickListener(new OnClickListener() {
      @Override
      public void onClick(View v){
          // TODO
      }
  });
  ```
  - Activity本身作为事件监听器类（界面按钮较多时常用）
  
  ```java
  // 让Activity实现OnClickListener接口
  pubic class MainActivity extends AppCompatActivity implements OnClickListener {
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          ..
          btn.setOnClickListener(this);
      }
  
      // 实现接口中的方法
      @Override
      public void onClick(View v) {
          switch (v.getId()) {
              case R.id.btn:
                  // TODO
                  break;
              ..
              default:
                  break;
          }
      }
  }
  ```
  
  - 直接绑定到标签
  
    xml文件中，在需要进行事件处理的组件标签下声明 `android:onClick="方法名"` 属性
  
    Java文件中，自定义一个方法，传入view参数 `public void 方法名 (View source) {}`
  
  ***ps:*** 该方法必须满足： _public修饰_　_返回类型为 void_　_参数唯一 (View类型，代表被点击的视图 )_
- 基于回调的事件处理（重写组件或Activity的回调方法：`onTouchEvent` / `onKeyXxx`）



## 接口回调

1. 定义接口
```java
pubic interface ITopbarClickListener {
    void leftClick();
    void rightClick();
}
```
2. 暴露接口给调用者

```java
// 暴露一个方法给调用者来注册接口回调，通过接口来获得回调者对接口方法的实现
public void setOnTopbarClickListener(ITopbarClickListener listener) {
    this.listener = listener;
}
// 调用接口方法
leftbtn.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        if (listener != null) {
            listener.leftClick();
        }
    }
});

rightbtn.setOnClickListener(new OnClickListener(){
    @Override
    public void onClick(View v) {
        if (listener != null) {
            listener.rightClick();
        }
    }
});
```

3. 实现接口回调

```java
// 调用 2. 中暴露的方法，并传入接口对象参数
mTopbar.setOnTopbarClickListener(new TopBar.ITopbarClickListener(){
    @Override
    public void leftClick() {
        // TODO
    }
    
    @Override
    public void rightClick() {
        // TODO
    }
});
```



## Layout优化

- &lt;include&gt;
  - 此标签中仅可以覆写layout属性，且覆写layout_width和layout_height之外的属性时，也必须连带覆写这两个属性，否则覆写效果不生效。
  - 当引用布局以&lt;merge&gt;为根结点时，覆写不生效。
- &lt;merge&gt;

  - 必须声明于布局的根结点。
  - merge的布局会受到外部布局类型的影响。例如，merge标签中使用了 `android:layout_below` 时，当外部父标签是LinearLayout时，就会失效。
- &lt;ViewStub&gt;
  - 不占用布局空间，但定义时必须指定宽高属性。
  - ViewStub中的布局不允许使用&lt;merge&gt;标签。
  - ViewStub对象inflate成功后，将会被置空（id将失效），因此使用时要进行非空判断。



- [内嵌复杂的 XML 资源](https://developer.android.google.cn/guide/topics/resources/complex-xml-resources)

  有时某些资源类型是由xml文件表示的多个复杂资源合成的。如：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/vectordrawable" >
    <target
        android:name="rotationGroup"
        android:animation="@anim/rotation" />
</animated-vector>
```

这里使用了 *res/drawable/vectordrawable.xml* 和 *res/anim/rotation.xml*，目的是为了合成 *res/drawable/avd.xml*。若 *vectordrawable.xml* 和 *rotation.xml* 需要复用，则分开创建即是最佳方式。但若这两个文件仅用于合成 *avd.xml*，则可以使用AAPT的内嵌资源格式：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt" >

    <aapt:attr name="android:drawable" >
        <!-- 原 vectordrawable.xml 中的内容 -->
    </aapt:attr>

    <target android:name="rotationGroup">
        <aapt:attr name="android:animation" >
            <!-- 原 rotation.xml 中的内容 -->
        </aapt:attr>
    </target>
</animated-vector>
```

`<aapt:attr >` 标记的子标记应被视为资源并提取到其自己的资源文件中。属性名称中的值用于指定在父标记内使用内嵌资源的位置。==AAPT会为所有内嵌资源生成资源文件和名称？==使用此内嵌格式构建的应用可与所有Android版本兼容。



## 动画

Android动画主要分为两大类（三种）：

- 视图动画：补间动画、帧动画（以xml方式定义时置于*res/anim*目录下，包含scale、rotate、translate、alpha、set等标签）
- 属性动画（以xml方式定义时置于*res/animator*目录下，包含animator、objectAnimator、set、propertyValuesHolder 4个标签）

***ps:*** 调用 `View#startAnimation` 时请确保view是可见的，不可见的view会执行 `View#skipInvalidate`。



### 视图动画

- 作用对象：仅视图控件（View）

#### 补间动画

- 应用场景

  - 以下4种类型的基础动画效果
  - Activity/Fragment的切换效果、ViewGroup子元素的出场效果

- 类型

  - 平移动画（Translate）[以下注释不合法，仅作说明用，使用时请删除]

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <translate xmlns:android="http://schemas.android.com/apk/res/android"
      // 以下参数是4种动画效果的公共属性
      // 持续时间/ms，必须设置才会有动画效果
      android:duration="3000"
      // 延迟开始时间/ms
      android:startOffset="1000"
      // 是否停留在动画结束视图状态，优先级高于fillBefore，默认false
      android:fillAfter="false"
      // 是否停留在动画开始视图状态，默认true
      android:fillBefore="true"
      // 是否应用fillBefore值，对fillAfter无影响，默认true
      android:fillEnabled="true"
      // 动画播放次数 = 重放次数 + 1，infinite无限重复
      android:repeatCount="0"
      // 重复模式，restart正序播放，reverse倒序回放
      android:repeatMode="restart"
      // 插值器
      android:interpolator="@[package:]anim/interpolator_resource"
  
      // 以下参数是平移动画特有属性
      android:fromXDelta="0"// 水平方向x的起始值
      android:toXDelta="500"// 水平方向x的结束值
      android:fromYDelta="0"// 竖直方向y的起始值
      android:toYDelta="500"// 竖直方向y的结束值 />
  ```

  ```java
  Button btn = (Button) findViewById(R.id.btnTranslate);
  // 加载xml配置的动画
  Animation translate = AnimationUtils.loadAnimation(this, R.anim.anim_translate);
  // Java动态配置动画
  // Animation translate = new TranslateAnimation(0, 500, 0, 500);
  // translate.setDuration(3000L);// 固定属性都具有setter方法
  // 播放动画
  btn.startAnimation(translate);
  ```

  - 缩放动画（Scale）

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <scale xmlns:android="http://schemas.android.com/apk/res/android"
      // 公共属性部分省略..
      // 以下参数是缩放动画特有属性
      // x起始缩放倍数，小于1.0收缩，大于1.0放大
      android:fromXScale="1.0"
      // x结束缩放倍数
      android:toXScale="2.0"
      android:fromYScale="1.0"
      android:toYScale="2.0"
      // 缩放轴点坐标
      android:pivotX="50%"
      android:pivotY="50%" />
  <!-- 轴点可取值为:
   数字(50)，距离View左上角50px的点，对应Java中的pivotType为Animation.ABSOLUTE
   百分比(50%)，距离View左上角50%自身宽/高的点，Animation.RELATIVE_TO_SELF
   百分比p(50%p)，距离View左上角50%父控件宽/高的点，Animation.RELATIVE_TO_PARENT -->
  ```

  ```java
  // 加载xml配置的动画
  Animation scale = AnimationUtils.loadAnimation(this, R.anim.anim_scale);
  // Java动态配置动画
  // Animation scale = new ScaleAnimation(1.0f, 2.0f, 1.0f, 2.0f,
  // Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
  // scale.setDuration(3000L);
  ```

  - 旋转动画（Rotate）

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <rotate xmlns:android="http://schemas.android.com/apk/res/android"
      // 公共属性部分省略..
      // 以下参数是旋转动画特有属性
      // 起始旋转角度，从x轴负方向开始，正数顺时针，负数逆时针
      android:fromDegrees="0"
      // 结束旋转角度
      android:toDegrees="360"
      // 含义同缩放动画
    android:pivotX="50%"
      android:pivotY="50%" />
  ```

  ```java
  // 加载xml配置的动画
  Animation rotate = AnimationUtils.loadAnimation(this, R.anim.anim_rotate);
  // Java动态配置动画
  // Animation rotate = new RotateAnimation(0f, 360f,
  // Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
  // rotate.setDuration(3000L);
  ```

  - 透明度动画（Alpha）

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <alpha xmlns:android="http://schemas.android.com/apk/res/android"
      // 公共属性部分省略..
      // 以下参数是透明度动画特有属性
      // View起始透明度
      android:fromAlpha="1.0"
      // View结束透明度
      android:toAlpha="0.0" />
  ```

  ```java
  // 加载xml配置的动画
  Animation alpha = AnimationUtils.loadAnimation(this, R.anim.anim_alpha);
  // Java动态配置动画
  // Animation alpha = new AlphaAnimation(1.0f, 0.0f);
  // alpha.setDuration(3000L);
  ```



- 高级用法

  组合动画

  - XML设置（以下注释不合法，仅作说明用，使用时请删除）

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <set xmlns:android="http://schemas.android.com/apk/res/android"
      // 公共属性部分同基础动画，省略..
      // 集合特有属性
      // 子动画和集合共享插值器
      android:shareinterpolator="true" >
      <translate .. />
      <scale .. />
      <rotate .. />
      <alpha .. />
  </set>
  ```
  
  ***ps:***
  
  1. 组合动画的所有子动画是同时开始播放的，可通过 `android:startOffset` 属性自定义子动画不同延迟；
  2. 组合动画中*scale*的 `repeatCount` 和 `fillAfter` 属性失效，需要在set标签中设置。
  
  - Java设置
  
  ```java
  AnimationSet animSet = new AnimationSet(boolean shareInterpolator);
  /* AnimationSet animSet = (AnimationSet) AnimationUtils.loadAnimation(this, R.anim.anim_item); */
  // 各种setter
  // 各种子动画初始化
  animSet.addAnimation(translate);
  // animSet.addAnimation(..);
  btn.startAnimation(animSet);
  ```
  
  
  
  监听动画
  
  ```java
  Animation.setAnimationListener(new Animation.AnimationListener() {
      @Override
      public void onAnimationStart(Animation animation) { }
      @Override
      public void onAnimationEnd(Animation animation) { }
      @Override
      public void onAnimationRepeat(Animation animation) { }
  });
  ```
  
  

#### 帧动画

[动画资源|帧动画](https://developer.android.google.cn/guide/topics/resources/animation-resource#Frame)

\<animation-list ..>

1. `res/drawable/rocket.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/rocket_thrust1" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust2" android:duration="200" />
    <item android:drawable="@drawable/rocket_thrust3" android:duration="200" />
</animation-list>
```

2. Java代码中播放

```java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = rocketImage.getBackground();
if (rocketAnimation instanceof Animatable) {
    ((Animatable)rocketAnimation).start();
}
```



### 属性动画

[动画资源|属性动画](https://developer.android.google.cn/guide/topics/resources/animation-resource#Property)

- ViewPropertyAnimator

  `view.animate().translationX(100);`

- ObjectAnimator

- ValueAnimator



### 应用

#### Activity转场过渡

启动/退出动效，如淡入淡出、左滑右滑等

- 系统预设

```java
startActivity(new Intent(A.this, B.class));
/*overridePendingTransition(int enterAnim, int exitAnim)
  必须在startActivity之后立即调用；
  enterAnim：A跳转到B，进入B的动效资源ID；
  exitAnim：A跳转到B，离开A时的动效资源ID*/
overridePendingTransition(android.R.anim.slide_in_left, android.R.anim.slide_out_right);
```

```java
// API16开始使用ActivityOptions实现同样效果
startActivity(new Intent(A.this, B.class),
        ActivityOptionsCompat.makeCustomAnimation(this,
                android.R.anim.slide_in_left,
                android.R.anim.slide_out_right)
                .toBundle());
```

```java
@Override
public void finish() {
    super.finish();
    /*必须在finish之后立即调用；
      enterAnim：B退回A，即进入A的动效资源；
      exitAnim：B跳转到A，即离开B时的动效资源。
      注意：Toolbar的返回按钮不回调finish()，请在onOptionsItemSelected(MenuItem item)中
      自主监听android.R.id.home并调用finish()方法。*/
    overridePendingTransition(android.R.anim.fade_in, android.R.anim.fade_out);
}
```

***ps:*** 单独设置其中一个动画，另一个设置为0无动画，这样可能出现==问题？==（同时为0无问题），需要将0替换为一个无动画效果的动画。

- 自定义切换效果（使用方法同上，只是将enterAnim和exitAnim替换成自定义的动画资源，如下左右滑动效果）

```xml
<translate xmlns:android="http://schemas.android.com/apk/res/android"
      android:duration="500"
      android:fromXDelta="0%p"
      android:toXDelta="-100%p" />
```

```xml
<translate xmlns:android="http://schemas.android.com/apk/res/android"
      android:duration="500"
      android:fromXDelta="100%p"
      android:toXDelta="0%p" />
```

![自定义进出动画位置参考](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/custom-enter-exit-animation-position-reference.png)

- 当Activity在x轴-100%p时，刚好完全超出屏幕到左边（位置1）
- 当Activity在x轴0%p时，刚好完全在屏幕内(位置2）
- 当Activity在x轴100%p时，刚好完全超出屏幕到右边（位置3）



**API21+转场动画**

------

[Android 中的转场动画及兼容处理](http://wl9739.github.io/2016/10/16/Android-中的转场动画及兼容处理/)



#### ViewGroup子元素出场动画

常用需求场景：为ListView/RecyclerView的item设置出场动画

1. 设置子元素出场动画 *res/anim/anim_item.xml*

```xml
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000">
    <alpha
        android:duration="800"
        android:fromAlpha="1.0"
        android:toAlpha="0.0" />
    <translate
        android:fromXDelta="400"
        android:toXDelta="0" />
</set>
```

2. 设置ViewGroup动画文件 *res/anim/anim_layout.xml*（以下注释不符合xml语法，仅作说明用，使用时请删除）

```xml
<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
    android:animation="@anim/anim_item"// 子元素动画效果
    android:animationOrder="normal"// 子元素动画播放顺序，默认normal
    // normal：顺序，排在前面的子元素先播放入场动画
    // reverse：倒序，排在后面的子元素先播放入场动画
    // random：随机顺序播放入场动画

    android:delay="0.5"// 子元素动画开始延迟
    // 如子元素入场动画总时长300ms，那么delay="0.5"表示每个子元素都会延迟150ms才会播放动画

    android:interpolator="@[package:]anim/interpolator_resource" />
```

3. 为ViewGroup设置 `android:layoutAnimation` 属性

   - xml中设置

   ```xml
   <android.support.v7.widget.RecyclerView
       android:layoutAnimation="@anim/anim_layout"
       ... />
   ```

   - Java代码设置（无需第 **2.** 步）

   ```java
   RecyclerView recycler = findViewById(R.id.recycler);
     Animation itemAnim = AnimationUtils.loadAnimation(this, R.anim.anim_item);
     //设置layoutAnimation属性
     LayoutAnimationController controller = new LayoutAnimationController(itemAnim);
     controller.setOrder(LayoutAnimationController.ORDER_NORMAL);
     controller.setDelay(.5f);
     controller.setInterpolator(new AccelerateInterpolator());
     recycler.setLayoutAnimation(controller);
   ```





## Window和WindowManager

>Window表示一个窗口，是个抽象类，其唯一实现类是 **PhoneWindow**，可以通过WindowManager创建。WindowManager是外界访问Window的入口，Window的具体实现位于WindowManagerService(WMS)中，WindowManager和WindowManagerService的交互是一个IPC过程。

- 





## Android多线程

- [Java多线程基础](./REVIEW JAVA.md)
- 常用方式
  - 继承Thread类
  - 实现Runnable接口
  - Handler
  - AsyncTask
  - HandlerThread

### Handler

- UI线程中，直接创建Handler即可使用

  ***tip:*** `Looper.myLooper() == Looper.getMainLooper()` 可用于判断是否处于主线程。

- 子线程中

```java
//创建子线程Looper，只能创建一次
Looper.prepare();
mHandler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        if (msg.what == 0x123) {
            // TODO
        }
    }
};
//启动消息队列
Looper.loop();
```

- 发送消息到消息队列的方式
   - `Handler.sendMessage()`
     - 新建Handler子类(内部类)
     - 匿名内部类

   - `Handler.post()`

   ```java
   handler.post(new Runnable() {
    　@Override
    　public void run(){
    　　　// TODO
    　}
   });
   ```

   - `Message.obtain(handler).sendToTarget()`
   
- `Looper.loop()` 中的死循环为何不会卡死主线程？[:link:](https://www.zhihu.com/question/34652589)

- 如何避免Activity内存泄漏？

   - 移除所有静态引用
   - 考虑用EventBus解耦Listener
   - 不再需要Listener时，记得解除绑定
   - 尽量用静态内部类
   - 使用类似MAT，LeakCanary工具分析内存
   - 在Callback里打印Log？
   - Code Review
   - 了解程序的结构



### AsyncTask

- [AsyncTask的实现原理 = 线程池 + Handler](https://www.jianshu.com/p/37502bbbb25a)；**线程池用于线程调度、复用&执行任务；Handler用于异步通信。**

  ```java
  /* 3种泛型参数用于控制AsyncTask子类执行线程任务时各个阶段的返回类型
     Params 启动异步任务时的入参类型，对应execute的参数类型
     Progress 异步任务执行中的进度值类型
     Result 异步任务执行完成后的形参类型，同时也是doInBackground的返回值类型
     ps:未使用参数可声明为Void；若有不同业务，需额外再写1个AsyncTask的子类处理 */
  public abstract class AsyncTask<Params, Progress, Result> {
      ...
  }
  ```

- 注意

  - AsyncTask不与任何组件绑定生命周期。在Activity或Fragment中使用时，最好在onDestroy()中调用cancel()。
  - 若AsyncTask被声明为Activity的非静态内部类，当Activity销毁时，因AsyncTask保留着Activity的引用从而引起内存泄露。所以AsyncTask应当被声明为静态内部类。
  - 当Activity重新创建时（屏幕旋转 / Activity被意外销毁时后恢复），之前运行的AsyncTask（非静态的内部类）持有的之前Activity引用已无效，故覆写的onPostExecute也将失效，即无法更新UI。建议在Activity恢复时的对应方法重启任务线程。

- 使用步骤

  1. 创建AsyncTask的子类 `public class MyAsyncTask extends AsyncTask<>{}`

  2. 覆写方法：`onPreExecute()`
                          `doInBackground()` // 后台任务(子线程)
                          `//publishProgress()`
                          `//onProgressUpdate()`
                          `onPostExecute()`
  

**_ps: 执行顺序_**
       
- 基础使用
       
  `（手动调用）execute() ->> onPreExecute() ->> doInBackground() ->> onPostExecute()`
  
- 需要显示进度
       
  `（手动调用）execute() ->> onPreExecute() ->> doInBackground() ->> publishProgress() ->>  onPostExecute() ->> onProgressUpdate()`
  
- 执行线程任务时需终止
       
  `（手动调用）execute() ->> onPreExecute() ->> doInBackground() ->> onCancelled()`
       
3. 调用AsyncTask子类实例执行任务
  
     `MyAsyncTask mTask = new MyAsyncTask().execute();`
  
     **_ps:_**
  
     - 必须在UI线程中创建AsyncTask的实例
     - 必须在UI线程中调用 `AsyncTask#execute`
     - 必须由系统负责调用 *Step2.* 中的方法，不能手动调用
     - 每个AsyncTask只能执行一次
     - 可调用 `AsyncTask#executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR, null);` 实现多任务并发

```java
  /**
 * 步骤1：创建AsyncTask子类
   * 注：
   *  a.继承AsyncTask类，声明3个泛型类型（不使用时声明为Void）
   *  b.根据需求，在AsyncTask子类内实现核心方法
   */
  private class MyTask extends AsyncTask<Params, Progress, Result> {
      ...
  
      // 作用：执行 线程任务前的操作
      // 注：根据需求复写
      @Override
      protected void onPreExecute() { }
  
      // 作用：接收输入参数、执行任务中的耗时操作、返回 线程任务执行的结果
      // 注：必须复写，从而自定义子线程任务
      @Override
      protected String doInBackground(String... params) {
          // TODO 自定义的线程任务
          // 发布进度至onProgressUpdate
          publishProgress(count);
      }
  
      // 作用：在主线程 显示线程任务执行的进度
      // 注：根据需求复写
      @Override
      protected void onProgressUpdate(Integer... progresses) { }
  
      // 作用：接收线程任务执行结果、更新UI
      // 注：必须复写，从而自定义UI操作
      @Override
      protected void onPostExecute(String result) { }
  
      // 作用：取消状态的异步任务，执行完子线程后将回调此方法而不是onPostExecute
      @Override
      protected void onCancelled() { }
  }
  
  /**
    * 步骤2：创建AsyncTask子类对象（即任务实例，必须在UI线程中创建）
    */
  MyTask mTask = new MyTask();
  
  /**
    * 步骤3：调用execute执行异步线程任务
    * 注：
    *  a.必须在UI线程中调用
    *  b.同一个AsyncTask实例对象只能执行1次，重复执行将抛出异常
    *  c.执行任务中，系统会自动回调AsyncTask的一系列方法（在步骤1中覆写，切忌手动调用）
    */
  mTask.execute()；
```

- AsyncTask核心 & 常用方法

<table style="zoom:90%">
    <thead>
        <tr><th style="text-align:center;">核心方法</th>
            <th style="text-align:center;">作用</th>
            <th style="text-align:center;">调用时刻</th>
            <th style="text-align:center;">备注</th></tr>
    </thead>
    <tbody>
        <tr><td><code>execute(Params... params)</code></td>
            <td style="text-align:center;"><font size="2">启动 异步线程任务</font></td>
            <td style="text-align:center;"><font size="2">手动调用</font></td>
            <td><font size="2"><ul>
                <li><span style="color:red">必须在UI线程中调用</span></li>
                <li>运行在主线程</li>
                </ul></font></td></tr>
        <tr><td><code>onPreExecute()</code></td>
            <td style="text-align:center;">
                <font size="2">执行线程任务前的操作</font><br/>
                <font size="1" color="#68b3fc">(根据需求覆写)</font></td>
            <td><font size="2"><ul>
                <li>执行线程任务<span style="color:red">前</span>自动调用</li>
                <li>即执行 <code>execute()</code> 前 自动调用<br><font size="1" color=red>(不要手动调用，系统会自动调用)</font></li>
                </ul></font></td>
            <td style="text-align:center;">
                <font size="2">用于UI的初始化操作</font><br>
                <font size="1" color="#68b3fc">(如显示进度条对话框)</font></td></tr>
        <tr><td><code>doInBackground(Params params)</code></td>
            <td><font size="2"><ul>
                <li>接收输入参数</li>
                <li>执行任务中的耗时操作<br><font size="1" color=red>(必须覆写，从而自定义线程任务)</font></li>
                <li>返回 线程任务执行的结果</li>
                </ul></font></td>
            <td><font size="2"><ul>
                <li>执行线程任务<span style="color:red">时</span>自动调用</li>
                <li>即 <code>onPreExecute()</code> 执行完成后 自动调用<br><font size="1" color=red>(不要手动调用，系统会自动调用)</font></li>
                </ul></font></td>
            <td><font size="2"><ul>
                <li><span style="color:red">运行在子线程</span>，不要操作UI</li>
                <li>执行过程中，可调用 <code>publishProgress()</code> 向UI线程更新进度</li>
                </ul></font></td></tr>
        <tr><td><code>onProgressUpdate(Progress values)</code></td>
            <td style="text-align:center;">
                <font size="2">在主线程 显示线程任务执行的进度</font><br/>
                <font size="1" color="#68b3fc">(根据需求覆写)</font></td>
            <td style="text-align:center;">
                <font size="2">调用 <code>publishProgress()</code> 时 自动调用</font><br/>
                <font size="1" color=red>(不要手动调用，系统会自动调用)</font></td>
            <td style="text-align:center;">/</td></tr>
        <tr><td><code>onPostExecute(Result result)</code></td>
            <td><font size="2"><ul>
                <li>接收线程任务执行 结果</li>
                <li>将执行结果显示到UI上<br><font size="1" color=red>(必须覆写，从而自定义UI操作)</font></li>
                </ul></font></td>
            <td style="text-align:center;">
                <font size="2">线程任务结束时 自动调用</font><br/>
                <font size="1" color=red>(不要手动调用，系统会自动调用)</font></td>
            <td style="text-align:center;">/</td></tr>
        <tr><td><code>onCancelled()</code></td>
            <td style="text-align:center;">
                <font size="2">取消状态的异步任务回调方法</font><br/>
                <font size="1" color=red>(并非真正的取消任务)</font></td>
            <td style="text-align:center;">
                <font size="2">异步任务被取消时 即自动调用</font><br/>
                <font size="1" color=red>(需在 <code>doInBackground()</code> 中判断)</font></td>
            <td style="text-align:center;">
                <font size="2">回调此方法时，就不会回调<code>onPostExecute()</code></font></td></tr>
    </tbody>
</table>





## IPC（Inter-Process Communication）

|            类型            |                      定义                       |                             作用                             |                          资源拥有性                          |                      地址空间                      |                           系统开销                           |                             通信                             |
| :------------------------: | :---------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <font size="2">进程</font> |    <font size="2">资源拥有的基本单位</font>     | <font size="2">使多个程序可并发执行，以提高系统的资源利用率和吞吐量</font> |                <font size="2">拥有资源</font>                |       <font size="2">进程之间相互独立</font>       | <font size="2">大</font><br/><font size="1" color="#68b3fc">(创建/回收PCB，系统资源等)</font> | <font size="2">复杂</font><br/><font size="1" color="#68b3fc">(需进程同步 & 互斥手段辅助)</font> |
| <font size="2">线程</font> | <font size="2">独立调度 & 分派的基本单位</font> | <font size="2">减少程序在并发执行时所付出的时空开销，提高操作系统的并发性能</font> | <font size="2">拥有很少的基本资源</font><br><font size="1" color="#68b3fc">(但可访问其隶属进程的系统资源)</font> | <font size="2">同一进程的各线程共享进程资源</font> | <font size="2">小</font><br/><font size="1" color="#68b3fc">(保存/恢复少量的寄存器)</font> | <font size="2">简单</font><br/><font size="1" color="#68b3fc">(可通过直接读写进程数据实现通信)</font> |

- Android中IPC方式

  - Bundle
  - 文件共享
  - Messenger
  - AIDL
  - ContentProvider
  - Socket

- Parcelable接口（Android的序列化方式）

  主要用于内存序列化，将对象序列化后存储到设备中 或 通过网络传输（建议还是使用Serializable）

### 多进程

  - 在AndroidManifest中为四大组件声明 `android:process` 属性

    属性值以**：**开头为私有进程；否则为全局进程，其他应用通过ShareUID方式可以共享全局进程

  - 通过JNI在native层去fork一个新的进程（特殊情况，一般不用）

### [Binder][图文详解Binder跨进程通信原理]

- Binder连接池



### [Socket][Socket使用攻略]



### 适用场景



## I/O

### 文件存储

- 读：

```java
private String read() {
    StringBuilder builder = new StringBuilder();
    try (FileInputStream fileInput = openFileInput("filename");
         InputStreamReader inputReader = new InputStreamReader(fileInput);
         BufferedReader reader = new BufferedReader(inputReader)) {
        String line;
        while ((line = reader.readLine()) != null) {
            builder.append(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return builder.toString();
}
```

- 写：

```java
private void write(String data) {
    //MODE_APPEND追加/MODE_PRIVATE覆盖
    try (FileOutputStream fileOutput = openFileOutput("filename", MODE_APPEND);
         OutputStreamWriter outputWriter = new OutputStreamWriter(fileOutput);
         // 缓冲流基于已存在流
         BufferedWriter writer = new BufferedWriter(outputWriter)) {
        writer.write(data);
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```



### SharedPreferences

- 读

```java
SharedPreferences sprefs = getSharedPreferences("filename", MODE_PRIVATE);
// SharedPreferences sprefs = getPreferences(MODE_PRIVATE);// 类名作为文件名
String str = sprefs.getString("key1", "defValue");
int num = sprefs.getInt("key2", 0);
...
```

- 写

```java
SharedPreferences.Editor editor = getSharedPreferences("filename",MODE_PRIVATE).edit();
editor.putString("key1", "value");
editor.putInt("key2", value);
...
// 异步提交，无返回值，效率较高，不关心提交结果时优先使用
editor.apply();
// 同步提交，有返回值，效率较低
// editor.commit();
```

***ps:*** 系统会自动为filename添加.xml文件扩展名。

- 清空 `editor.clear().apply()`



### SQLite

- 自定义SQLiteOpenHelper子类

```java
/**
  * 自定义一个类继承SQLiteOpenHelper类，覆写onCreate()和onUpgrade()
  */
public class MyDatabaseHelper extends SQLiteOpenHelper {
    private Context context;
    // 建表语句
    public static final String CREATE_BOOK = "CREATE TABLE Book("
        + "id INTEGER PRIMARY KEY AUTOINCREMENT, "
        + "author TEXT, "
        + "price REAL, "
        + "pages INTEGER, "
        + "name TEXT)";
    
    public MyDatabaseHelper(Context context, String name,
                            SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
        this.context = context;
    }
    // 数据库第一次创建时调用
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_BOOK);
    }
    // 升级version时调用
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 若表已存在，先删掉，因为重复创建表会报错。
        db.execSQL("DROP TABLE IF EXISTS Book");
        onCreate(db);
    }
}
```



- SQLite的数据类型：**integer**（整型）、**real**（浮点型）、**text**（文本类型）、**blob**（二进制类型）。
- 使用 **PRIMARY KEY** 将列设为主键，**AUTOINCREMENT** 关键字表示自增长。
- ==升级数据库，drop table时如何保留数据？？==



- **CRUD**

```java
public class MainActivity extends AppCompatActivity {
    private MyDatabaseHelper dbHelper;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        /**
         * dbHelper.getWritableDatabase/getReadableDatabase
         * 同：回调SQLiteOpenHelper类的onCreate()创建或打开数据库（若已存在，不会重复创建）;
         * 异：当数据库无法写入时(如磁盘空间满了)，getReadableDatabase()将以只读方式打开数据库，而getWritableDatabase()会抛异常。
         */
        SQLiteDatabase db = dbHelper.getWritableDatabase();// 获取SQLiteDatabase实例
        
        /**
         * SQL语法
         */
        // 增：向Book表中插入数据
        db.execSQL("INSERT INTO Book (name, author, pages, price) values(?,?,?,?)",
                   new String[] {"The Da Vinci Code", "Dan Brown", "454", "16.86"});
        // 删：删除Book表中pages大于500的项
        db.execSQL("DELETE FROM Book WHERE pages > ?",
                   new String[] { "500" });
        // 改：将Book表中name为The Da Vinci Code的项的price值改为10.99
        db.execSQL("UPDATE Book SET price = ? WHERE name = ?",
                   new String[] {"10.99", "The Da Vinci Code"});
        // 查：遍历Book表
        Cursor cursor = db.rawQuery("SELECT * FROM Book", null);
        // 先移动到第1行，存在数据才会返回true
        if (cursor.moveToFirst()) {
            do {
                String name = cursor.getString(cursor.getColumnIndex("name"));
                String author = cursor.getString(cursor.getColumnIndex("author"));
                int pages = cursor.getInt(cursor.getColumnIndex("pages"));
                double price = cursor.getDouble(cursor.getColumnIndex("price"));
            } while (cursor.moveToNext());
        }
        cursor.close();

        /**
         * Android API
         */
        // 增：向Book表中插入数据
        ContentValues values = new ContentValues();        
        values.put("name", "The Lost Symbol");
        values.put("author", "Dan Brown");
        values.put("pages", 510);
        values.put("price", 19.95);
        db.insert("Book", null, values);
        
        values.clear();
        // 插入第二条数据，先clear一下
        // 同上..
        
        // 删：删除Book表中pages大于500的项
        db.delete("Book", "pages > ?", new String[] {"500"});
        
        // 改：将Book表中name为The Da Vinci Code的项的price值改为10.99
        ContentValues values = new ContentValues();
        values.put("price", 10.99);
        db.update("Book", values, "name = ?", new String[] {"The Da Vinci Code"});
        
        // 查：遍历Book表
        Cursor cursor = db.query("Book", null, null, null, null, null, null);
        // 先移动到第1行，存在数据才会返回true
        if (cursor.moveToFirst()) {
            do {
                String name = cursor.getString(cursor.getColumnIndex("name"));
                String author = cursor.getString(cursor.getColumnIndex("author"));
                int pages = cursor.getInt(cursor.getColumnIndex("pages"));
                double price = cursor.getDouble(cursor.getColumnIndex("price"));
            } while (cursor.moveToNext());
        }
        cursor.close();
    }
}
```



| query()方法参数 | 对应SQL部分               | 描述                          |
| :-------------- | ------------------------- | ----------------------------- |
| table           | from table_name           | 指定查询的表名                |
| columns         | select column1, column2   | 指定查询的列名                |
| selection       | where column = value      | 指定where的约束条件           |
| selectionArgs   | --                        | 为where中的占位符提供具体的值 |
| groupBy         | group by column           | 指定需要group by的列          |
| having          | having column = value     | 对group by后的结果进一步约束  |
| orderBy         | order by column1, column2 | 指定查询结果的排序方式        |



- ADB查看数据库
  1. 环境变量 PATH 的值中加入 platform-tools 的路径
  2. 键入 adb shell 进入设备控制台
  3. 通过 `cd data/data/com.alpha.lacosanostra/databases/` 命令进入到数据库文件所在目录（ls 命令可以罗列出所在目录下所有项，`cd ..` 返回上一级）
  4. 通过 `sqlite3 BookStore.db` 命令进入BookStore数据库（<u>数据库名称区分大小写</u>）
     - `.table` 命令可以查询当前数据库中有哪些表
     - `.schema` 命令可以查看数据库的建表语句
     - `.exit/quit` 命令可以退出数据库的编辑
  5. `select * from Book;` 列出Book表中所有数据（注意 **;** 号，<u>表名不区分大小写</u>）
  6. 键入 exit 退出设备控制台





# [APPENDIX](./APPENDIX.md)

## Indexs

### [View体系](./VIEW SYSTEM.md)

### [原生组件札记](./NATIVE NOTE.md)

### [主流开源库解析](./OPEN SOURCE.md)

### [技术解决方案](./RESOLUTIONS.md)



## Links

[资源类型格式、语法说明]: https://developer.android.google.cn/guide/topics/resources/available-resources
[多线程：AsyncTask的原理及源码分析]: https://www.jianshu.com/p/37502bbbb25a
[图文详解Binder跨进程通信原理]: https://www.jianshu.com/p/4ee3fd07da14
[Socket使用攻略]: https://www.jianshu.com/p/089fb79e308b