[TOC]

# KEEP ANDROID



## RuntimePermission

```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
    // 若未授权，请求权限
    ActivityCompat.requestPermissions(this, new String[] { Manifest.permission
        .CALL_PHONE }, 1);
} else {
    // TODO 已授权处理逻辑
}
/**
 * 运行时权限处理回调
 */
@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    switch (requestCode) {
    case 1:
        if (grantResults.length > 0 && grantResults[0] == PackageManager
            .PERMISSION_GRANTED) {
            // TODO 运行时权限申请被允许处理逻辑(同 已授权处理逻辑，一般封装成方法在两处调用)
        } else {
            // TODO 运行时权限申请被拒绝处理逻辑
        }
        break;
    default:
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        break;
    }
}
```



- 申请相机权限时，保证Manifest.permission.CAMERA和Manifest.permission.WRITE_EXTERNAL_STORAGE权限同时申请，不要漏掉后者。

- 部分6.0设备报CHANGE_NETWORK_STATE和WRITE_SETTINGS SecurityException（6.0.1修复）

  > java.lang.SecurityException: xxx was not granted either of these permissions: android.permission.CHANGE_NETWORK_STATE, android.permission.WRITE_SETTINGS.

  跳转至系统设置页让用户设置（此问题只要授予了WRITE_SETTINGS就解决了）

```java
if (Build.VERSION.SDK_INT == Build.VERSION_CODES.M) {
    if(!Settings.System.canWrite(context)) {
        Intent goToSettings = new Intent(Settings.ACTION_MANAGE_WRITE_SETTINGS);
        goToSettings.setData(Uri.parse("package:" + Context.getPackageName()));
        goToSettings.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(goToSettings);
    }
}
```



### Android完整权限列表

> 危险权限不仅需要在AndroidManifest.xml文件中声明，还需要在代码中动态申请。每个危险权限都属于一个权限组，在进行运行时权限申请时，一旦用户同意授权，那么该权限所对应权限组的所有其他权限也会同时被授权。

<table style="zoom:90%;">
    <thead>
        <tr><th style="text-align:center;">权限组名</th>
            <th style="text-align:center;">权限名</th></tr>
    </thead>
    <tbody>
        <tr><td rowspan="2" style="text-align:center;"><strong>CALENDAR</strong></td>
            <td>READ_CALENDAR</td></tr>
        <tr><td>WRITE_CALENDAR</td></tr>
        <tr><td style="text-align:center;"><strong>CAMERA</strong></td>
            <td>CAMERA</td></tr>
        <tr><td rowspan="3" style="text-align:center;"><strong>CONTACTS</strong></td>
            <td>READ_CONTACTS</td></tr>
        <tr><td>WRITE_CONTACTS</td></tr>
        <tr><td>GET_ACCOUNTS</td></tr>
        <tr><td rowspan="2" style="text-align:center;"><strong>LOCATION</strong></td>
            <td>ACCESS_FINE_LOCATION</td></tr>
        <tr><td>ACCESS_COARSE_LOCATION</td></tr>
        <tr><td style="text-align:center;"><strong>MICROPHONE</strong></td>
            <td>RECORD_AUDIO</td></tr>
        <tr><td rowspan="7" style="text-align:center;"><strong>PHONE</strong></td>
            <td>READ_PHONE_STATE</td></tr>
        <tr><td>CALL_PHONE</td></tr>
        <tr><td>READ_CALL_LOG</td></tr>
        <tr><td>WRITE_CALL_LOG</td></tr>
        <tr><td>ADD_VOICEMAIL</td></tr>
        <tr><td>USE_SIP</td></tr>
        <tr><td>PROCESS_OUTGOING_CALLS</td></tr>
        <tr><td style="text-align:center;"><strong>SENSORS</strong></td>
            <td>BODY_SENSORS</td></tr>
        <tr><td rowspan="5" style="text-align:center;"><strong>SMS</strong></td>
            <td>SEND_SMS</td></tr>
        <tr><td>RECEIVE_SMS</td></tr>
        <tr><td>READ_SMS</td></tr>
        <tr><td>RECEIVE_WAP_PUSH</td></tr>
        <tr><td>RECEIVE_MMS</td></tr>
        <tr><td rowspan="2" style="text-align:center;"><strong>STORAGE</strong></td>
            <td>READ_EXTERNAL_STORAGE</td></tr>
        <tr><td>WRITE_EXTERNAL_STORAGE</td></tr>
    </tbody>
</table>
**附**：

[正常权限和危险权限](https://developer.android.com/guide/topics/security/permissions?hl=zh_cn#normal-dangerous)

[完整权限清单](https://developer.android.google.cn/reference/android/Manifest.permission.html)（检索关键词：*Protection level: dangerous*）



## 屏幕适配

- [Size限定符](https://developer.android.com/guide/practices/compatibility)

  ***eg:*** `res/layout-large/main.xml` 被定义为大屏的设备使用此布局。

- Smallest-width限定符（API13添加）

  ***eg:*** `res/layout-sw600dp/main.xml` 宽度在600dp以上的屏幕使用此布局。

- 布局别名（兼容 < API13，Deprecated）

- [Orientation限定符](https://blog.csdn.net/guolin_blog/article/details/8830286)
- Nine-Patch 9图：**左上拉伸，右下内容**

![建议的宽度断点以支持不同的屏幕尺寸](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/layout-adaptive-breakpoints.png)

![不同密度下位图的相对尺寸](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/devices-density.png)

- 适用于不同像素密度的配置限定符：

| 密度限定符 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| ldpi       | 适用于低密度 (ldpi) 屏幕 (~ 120dpi) 的资源。                 |
| mdpi       | 适用于中密度 (mdpi) 屏幕 (~ 160dpi) 的资源（**这是基准密度**）。 |
| hdpi       | 适用于高密度 (hdpi) 屏幕 (~ 240dpi) 的资源。                 |
| xhdpi      | 适用于加高 (xhdpi) 密度屏幕 (~ 320dpi) 的资源。              |
| xxhdpi     | 适用于超超高密度 (xxhdpi) 屏幕 (~ 480dpi) 的资源。           |
| xxxhdpi    | 适用于超超超高密度 (xxxhdpi) 屏幕 (~ 640dpi) 的资源。        |
| nodpi      | 适用于所有密度的资源。这些是与密度无关的资源。无论当前屏幕的密度是多少，系统都不会缩放以此限定符标记的资源。 |
| tvdpi      | 适用于密度介于 mdpi 和 hdpi 之间的屏幕（约 213dpi）的资源。这不属于“主要”密度组，它主要用于电视。若有必要提供 tvdpi 资源，应按系数 1.33*mdpi 来确定其大小。例如，若一张图片在 mdpi 屏幕上的大小为 100px x 100px，那么它在 tvdpi 屏幕上的大小应该为 133px x 133px。 |

- 要针对不同的密度创建备用可绘制位图资源，您应遵循六种主要密度之间的 **3:4:6:8:12:16 缩放比**。例如，如果您有一个可绘制位图资源，它在中密度屏幕上的大小为 48x48 像素，那么它在其他各种密度的屏幕上的大小应该为：
  - 36x36 (0.75x) - 低密度 (ldpi)
  - 48x48（1.0x 基准）- 中密度 (mdpi)
  - 72x72 (1.5x) - 高密度 (hdpi)
  - 96x96 (2.0x) - 超高密度 (xhdpi)
  - 144x144 (3.0x) - 超超高密度 (xxhdpi)
  - 192x192 (4.0x) - 超超超高密度 (xxxhdpi)



## 组件化

- **项目结构** [ *图例*：**+---** 目录  **\\---** 文件 ]

+--- Aladdin
|    +--- *aladdin*                                                              # 宿主module（application）
|    |    +--- src
|    |    |    +--- main
|    |    |          +--- java
|    |    |          +--- res
|    |    |           \\--- AndroidManifest.xml
|    |    +--- libs                                                               # so库
|    |     \\--- build.gradle
|    +--- **core**                                                                   # 核心库module（library）
|    +--- **components**                                                   # 组件统一位置，便于管理（需在settings.gradle中配置）
|    |    +--- ***login***                                                            # login组件
|    |    |    +--- src
|    |    |    |    +--- main
|    |    |    |          +--- java
|    |    |    |          +--- res
|    |    |    |          +--- debug
|    |    |    |          |     \\--- AndroidManifest.xml     # 组件module单独运行manifest文件（application）
|    |    |    |           \\--- AndroidManifest.xml           # 组件module集成运行manifest文件（library）
|    |    |     \\--- build.gradle
|    |    +--- ***mine***                                                          # mine组件（结构同login组件）
|    |    +--- ***...***                                                                # 后续新增开发的module
|    |     \\--- README.md                                            # 业务层组件说明文档
|    +--- images                                                            # README文档资源
|     \\--- build.gradle
|     \\--- *config.gradle*                                                  # 项目依赖库版本统一配置文件
|     \\--- settings.gradle                                              # 项目模块定义（自动）模块自定义路径（手动）
|     \\--- gradle.properties                                         # Gradle settings
|     \\--- README.md                                                 # 组件化项目架构说明文档



- **项目配置**

1. 新建Android项目（注意是否要依赖androidx库）

2. 以 **Android Library** 格式新建核心基础module，如 *core*

3. 以 **Phone & Tablet Module** 格式新建组件module，如 *login*、*mine* 等

4. 在项目根目录中新建 **components** 目录，并在 **settings.gradle** 文件中配置组件路径，将组件移至components目录下，便于统一管理，
   格式如 `project(':login').projectDir = new File("$rootDir/components/login")`

5. 在项目根目录下新建 **config.gradle** 文件，并在项目级 **build.gradle** 文件中通过 `apply from: "config.gradle"` 引入。在 **config.gradle** 中定义 **所有依赖库及其版本**，并且定义 **runAlone**（自定义名称）<u>控制各组件是否单独以application运行调试</u>

6. 在组件module的 **src/main/** 目录下新建 **debug** 目录，并在其中创建一个传统Android application应用的 **AndroidManifest.xml** 文件，以便组件module在单独运行调试时使用；原 **src/main/** 下的manifest文件则用于组件module以library格式集成运行

7. 若module是应用，则其 **builde.gradle** 文件第一行为

   `apply plugin:'com.android.application'`，

   若是库，则为 `apply plugin:'com.android.library'`。因此，为了让组件module能夠兼容切换两种运行模式，需要利用 **步骤5.** 中定义的 **runAlone** 将组件module的第一行改成：

   ```groovy
   // 配置运行状态 (APP or Lib)
   if (runAlone.login) {
       apply plugin: 'com.android.application'
   } else {
       apply plugin: 'com.android.library'
   }
   ```

   并且 **defaultConfig** 闭包下的 **applicationId** 属性要改成：

   ```groovy
   defaultConfig {
       // 注意：APP状态下才有applicationId属性
       if (runAlone.login) {
           applicationId "com.foxconn.android.component.login"
       }
       ...
   }
   ```

   在 **步骤6.** 中定义了组件module在两种运行模式下的manifest文件，使用方法是在 **android** 闭包下定义 **sourceSets** 闭包指定不同模式所需资源文件：

   ```groovy
   // 注意：不同状态下应用不同的AndroidManifest文件
   // 置于src/main/路径下，否则无效，因为声明在main闭包下，代表main资源集
   sourceSets {
       main {
           if (runAlone.login) {
               manifest.srcFile 'src/main/debug/AndroidManifest.xml'
           } else {
               // Lib状态下的AndroidManifest文件中，<application>标签下不要配置属性
               // 但是要声明四大组件，防止ARouter找不到路径
               manifest.srcFile 'src/main/AndroidManifest.xml'
           }
       }
   }
   ```

   另外 **dependencies** 闭包下以如下格式引用 **步骤5.** 中定义的依赖库：

   `implementation deps.support.appcompatv7`

   最后，除了基础库（核心库）本身的所有module都要依赖基础库，作为组件间的通信桥梁

   `implementation project(path: ':core')`

8. 在主module的 **build.gradle** 文件中依赖组件module

   ```groovy
   // 使用runtimeOnly依赖业务组件，可实现各组件代码在编译时对宿主APP隐藏，在运行时才对宿主APP可见
   if (!runAlone.login) {
       runtimeOnly project(path: ':login')
   }
   if (!runAlone.mine) {
       runtimeOnly project(path: ':mine')
   }
   ```

   

- **FAQ**

1. **AndroidManifest.xml文件合并冲突**

   *R*：Android Studio项目在最终打包时，会合并所有module的AndroidManifest.xml文件，若不同manifest文件中包含相同属性，IDE无法自行决定使用谁，就会造成冲突

   *A*：检查各module manifest文件中的重复元素，通常是 `<application />` 的属性，如每个module都有自己的 `<application android:name="." android:allowBackup="false"/>` 等。解决方法是在要保留相应属性的manifest文件中声明 `<application tools:replace="android:name,android:allowBackup" />` 。

2. **同名资源文件冲突**

   *R*：Android Studio项目在最终打包时，会对所有module的res文件分配ID，若不同module的资源文件重名，就会造成冲突

   *A*：利用Gradle *resourcePrefix* 语法，启用资源前缀lint检查，提醒开发者按照约束修改资源名称，降低同名冲突概率，`resourcePrefix "${project.name.toLowerCase()}_"`（声明于 **android** 闭包下）

3. **依赖冲突**

   *R*：三方库和项目依赖了相同的其他三方库

   *A*：如排除arms库中的gson和rxjava

   ```groovy
   implementation(rootProject.ext.dependencies['arms']) {
       // 排除相同group的所有依赖
       exclude group: 'com.google.code.gson'
       // 排除指定group中的指定module依赖
       exclude group: 'io.reactivex.rxjava2', module: 'rxjava'
   }
   ```

   



## Workarounds

### Splash启动页最佳实践

[目前最佳 :accept:](https://www.jianshu.com/p/2a66d1dabbd1)



### 再按一次退出程序

```java
private long lastExitTime;

@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    // 判断是否点击了“返回键”
    if (keyCode == KeyEvent.KEYCODE_BACK) {
        long thisExitTime = SystemClock.elapsedRealtime();
        // 与上次点击返回键时刻作差，大于2s判定为误操作，使用Toast进行提示
        if (thisExitTime - lastExitTime > 2000) {
            Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_LONG).show();
            // 并记录下本次点击“返回键”的时刻，以便下次进行判断
            lastExitTime = thisExitTime;
        } else {
            // 小于2s判定为用户确实希望退出程序
            // System.exit(0);// 动画略显生硬
            finish();
        }
        return true;
    }
    return super.onKeyDown(keyCode, event);
}
```



### 安装APK文件

```java
File apkFile = new File(apkFilePath);

Intent intent = new Intent();
intent.setAction(Intent.ACTION_VIEW);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.addCategory(Intent.CATEGORY_DEFAULT);
Uri apkUri;
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
    intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION | Intent.FLAG_GRANT_WRITE_URI_PERMISSION);
    apkUri = FileProvider.getUriForFile(context, BuildConfig.APPLICATION_ID + ".fileprovider", apkFile);
} else {
    apkUri = Uri.fromFile(apkFile);
}
if (apkUri == null) return;
intent.setDataAndType(apkUri, "application/vnd.android.package-archive");
if (intent.resolveActivity(context.getPackageManager()) != null) {
    context.startActivity(intent);
}
```

***ps:*** 注意 [FileProvider](https://developer.android.google.cn/reference/androidx/core/content/FileProvider) 的适配和 *authority* 的拼接。

```xml
<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="${applicationId}.fileprovider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/filepaths" />
</provider>
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path
        name="external_path"
        path="." />
</paths>
```



### 获取Android设备唯一标识

- 常见的设备唯一标识符
  - DeviceID（IMEI etc.）
  - MAC Address
  - SSAID（i.e. ANDROID_ID）
  - Serial Number
  - GUID（i.e. UUID）



- API29之前

```java
//需要READ_PHONE_STATE权限
TelephonyManager mgr = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
if (mgr != null) {
    String deviceId = mgr.getDeviceId();
    if (deviceId != null) {
        ...
    }
}
```

- API29开始无法获取DeviceID，返回null。可使用AndroidID替代。

```java
String androidId = Settings.System.getString(getContentResolver(), Settings.Secure.ANDROID_ID);
if (deviceId != null) {
    ...
}
```

***ps:*** 单设备单用户单签名唯一？恢复出厂设置会重置。从API26起，SSAID提供了一个在由同一开发者签名密钥签名的应用之间通用的标识符。

- 其他

  - GUID

  ```java
  String uniqueId = UUID.randomUUID().toString();
  ```

  - SN

  ```java
  Build.getSerial();
  ```



[唯一标识符最佳做法|Android Developers](https://developer.android.com/training/articles/user-data-ids)

[Android获取设备唯一标识方案](https://www.jianshu.com/p/471df87af749)



### 倒计时

android.os.CountDownTimer

```java
/**
 * Schedule a countdown until a time in the future, with
 * regular notifications on intervals along the way.
 *
 * Example of showing a 30 second countdown in a text field:
 *
 * <pre class="prettyprint">
 * new CountDownTimer(30000, 1000) {
 *
 *     public void onTick(long millisUntilFinished) {
 *         mTextField.setText("seconds remaining: " + millisUntilFinished / 1000);
 *     }
 *
 *     public void onFinish() {
 *         mTextField.setText("done!");
 *     }
 *  }.start();
 * </pre>
 *
 * The calls to {@link #onTick(long)} are synchronized to this object so that
 * one call to {@link #onTick(long)} won't ever occur before the previous
 * callback is complete.  This is only relevant when the implementation of
 * {@link #onTick(long)} takes an amount of time to execute that is significant
 * compared to the countdown interval.
 */
```



### 避免快速重复点击

```java
/**
 * 处理重复点击事件,默认1s,可自定义时间间隔
 */
public abstract class SingleClickListener implements OnClickListener {
    private long mLastClickTime;
    private long timeInterval = 1000L;

    public SingleClickListener() {}

    public SingleClickListener(long interval) {
        this.timeInterval = interval;
    }

    @Override
    public void onClick(View v) {
        long nowTime = SystemClock.elapsedRealtime();
        if (nowTime - mLastClickTime > timeInterval) {
            // 单次点击事件
            onSingleClick(v);
            mLastClickTime = nowTime;
        } else {
            // 快速点击事件
            onMultiClick(v);
        }
    }

    protected abstract void onSingleClick(View v);
    protected abstract void onMultiClick(View v);
}
```



### 避免从桌面启动APP重新实例化Launch Activity

一般出现在**首次**安装打开应用，点击home键返回桌面，又点击图标进入应用，却重新实例化了另一个Launch Activity。完全退出应用后，之后再重复上述操作就不会复现了（跟手机好像也有关系，不一定会100%出现）

```java
if (!isTaskRoot()) {
    Intent intent = getIntent();
    String action = intent.getAction();
    if (Intent.ACTION_MAIN.equals(action)
       && intent.hasCategory(Intent.CATEGORY_LAUNCHER)) {
        finish();
        return;
    }
}
```



### EditText密码显隐

```java
/**
 * 切换密码显隐状态
 * 
 * @param editText 密码输入框
 */
private void togglePasswordVisibility(EditText editText) {
    final int variation = editText.getInputType()
        & (EditorInfo.TYPE_MASK_CLASS | EditorInfo.TYPE_MASK_VARIATION);
    final boolean visible =
        variation == (EditorInfo.TYPE_CLASS_TEXT
                      | EditorInfo.TYPE_TEXT_VARIATION_PASSWORD);
    if (visible) {
        editText.setTransformationMethod(HideReturnsTransformationMethod.getInstance());
        editText.setInputType(EditorInfo.TYPE_CLASS_TEXT
                              | EditorInfo.TYPE_TEXT_VARIATION_VISIBLE_PASSWORD);
    } else {
        editText.setTransformationMethod(PasswordTransformationMethod.getInstance());
        editText.setInputType(EditorInfo.TYPE_CLASS_TEXT
                              | EditorInfo.TYPE_TEXT_VARIATION_PASSWORD);
    }
}
```



### EditText键盘事件

```xml
<EditText
    ...
    android:imeActionLabel="xxx"
    android:imeOptions="xxx" />
```

```java
et.setOnEditorActionListener(new OnEditorActionListener() {
    @Override
    public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
        switch (actionId) {
        case EditorInfo.IME_ACTION_GO:
            // do something
            break;
        default:
            break;
        }
        // Return true if you have consumed the action, else false.
        return true;
    }
});
```

用途：

```java
etQuery.setOnEditorActionListener((v, actionId, event) -> {
    if (EditorInfo.IME_ACTION_DONE == actionId) {
        if (etQuery.hasFocus()) {
            // 清除焦点并不会关闭软键盘，需监听焦点变化事件并作出响应
            etQuery.clearFocus();
        }
        return true;
    }
    return false;
});

etQuery.setOnFocusChangeListener((v, hasFocus) -> {
    if (!hasFocus) {
        KeyboardUtils.hideKeyboard(v);
    }
});
```

***ps:*** 实测，在模拟器中，使用外置键盘向EditText输入内容，点击软键盘完成按钮*(ACTION_DONE)*，清除焦点并隐藏软键盘，但界面有时会无故变暗，而软键盘输入或真机测试却不会出现此情况，因此推测为模拟器BUG。另外，不管测试机器/输入方式，使用返回键隐藏软键盘也可以避免此问题，但是无法同时清除焦点。



### 优化EditText网络搜索

使用TextWatcher监控EditText，在 `afterTextChanged` 中发送延迟消息进行网络搜索。当文本不断变动时，在 `onTextChanged` 中移除尚未发送的延迟消息，最终只有文本变动间隔超过延迟时间，才能成功发送消息，从而降低消息发送频率，避免短时间内进行多次网络请求。

```java
private static final long SEARCH_DELAY_MILLIS = 800L;
private boolean inSearching
private Handler searchHandler;

editText.addTextChangedListener(new TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {
        if (searchHandler == null) {
            searchHandler = new Handler(Looper.getMainLooper()) {
                @Override
                public void handleMessage(@NonNull Message msg) {
                    super.handleMessage(msg);
                    if (MSG_SEARCH == msg.what) {
                        String condition = (String) msg.obj;
                        // TODO 重置列表数据及页码、网络搜索请求..
                    }
                    inSearching = false;
                }
            };
        }
    }

    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {
        inSearching = true;
        searchHandler.removeMessages(MSG_SEARCH);
    }

    @Override
    public void afterTextChanged(Editable s) {
        Message message = Message.obtain();
        message.what = MSG_SEARCH;
        message.obj = s.toString().trim();
        searchHandler.sendMessageDelayed(message, SEARCH_DELAY_MILLIS);
    }
});

@Override
protected void onDestroy() {
    if (searchHandler != null) {
        searchHandler.removeCallbacksAndMessages(null);
    }
    super.onDestroy();
}
```



### 清除界面焦点

如Activity界面 (或RecyclerView) 中包含EditText等控件时，需要在其他操作期间自动清除EditText焦点并隐藏软键盘。可覆写Activity的 `dispatchTouchEvent()` 来获取当前焦点view，然后清除其焦点（可能需要手动隐藏软键盘）。如下：

```java
@Override
public boolean dispatchTouchEvent(MotionEvent ev) {
    if (ev.getAction() == MotionEvent.ACTION_DOWN) {
        View currFocus = getCurrentFocus();
        if (currFocus != null) {
            currFocus.clearFocus();
        }
    }
    return super.dispatchTouchEvent(ev);
}
```

***ps:*** `KeyboardUtils.hideKeyboard(view);`



### Android文字样式扩展

***ps:*** **[** *start, end* **)** ? 取决于 *Spanned.SPAN_EXCLUSIVE_EXCLUSIVE* 等flags。

- 前景 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/foreground-color-span.png" alt="foreground color" style="zoom: 40%;" /> Changes the color of the text to which the span is attached.

```java
SpannableString string = new SpannableString("Text with a foreground color span");
string.setSpan(new ForegroundColorSpan(color), 12, 28, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 背景 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/background-color-span.png" alt="background color" style="zoom: 40%;" /> Changes the background color of the text to which the span is attached.

```java
SpannableString string = new SpannableString("Text with a background color span");
string.setSpan(new BackgroundColorSpan(color), 12, 28, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 加粗、斜体 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/style-span.png" alt="style" style="zoom: 50%;" /> Span that allows setting the style of the text it's attached to.

```java
SpannableString string = new SpannableString("Bold and italic text");
string.setSpan(new StyleSpan(Typeface.BOLD), 0, 4, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new StyleSpan(Typeface.ITALIC), 9, 15, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

***ps:*** Note that styles are cumulative -- if both bold and italic are set in separate spans, or if the base style is bold and a span calls for italic, you get bold italic. You can't turn off a style from the base style.

- 绝对大小(直接设置dp/px) <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/absolute-size-span.png" alt="absolute size" style="zoom: 35%;" /> A span that changes the size of the text it's attached to.

```java
SpannableString string = new SpannableString("Text with absolute size span");
// change the size of the text to 55dp like this:
string.setSpan(new AbsoluteSizeSpan(55, true), 10, 23, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 相对大小(缩放倍数) <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/relative-size-span.png" alt="relative size" style="zoom: 40%;" /> Uniformly scales the size of the text to which it's attached by a certain proportion.

```java
SpannableString string = new SpannableString("Text with relative size span");
// a RelativeSizeSpan that increases the text size by 50% can be constructed like this:
string.setSpan(new RelativeSizeSpan(1.5f), 10, 24, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 字体 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/typeface-span.png" alt="typeface" style="zoom: 50%;" /> Span that updates the typeface of the text it's attached to.

```java
Typeface mTypeface = Typeface.create(ResourcesCompat.getFont(context, R.font.acme), Typeface.BOLD);
SpannableString string = new SpannableString("Text with typeface span.");
// TheTypefaceSpan can be constructed either based on a font family or based on a Typeface resource.
string.setSpan(new TypefaceSpan(mTypeface), 10, 18, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new TypefaceSpan("monospace"), 19, 22, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
```

***ps:*** The `TypefaceSpan` can be constructed either based on a font family or based on a *Typeface* resource. When `TypefaceSpan(String)` is used, the previous style of the TextView is kept. When `TypefaceSpan(Typeface)` is used, the typeface style replaces the TextView's style. For example, let's consider a *TextView* with `android:textStyle="italic"` and a typeface created based on a font from resources, with a bold style. When applying a *TypefaceSpan* based the typeface, the text will only keep the bold style, overriding the *TextView*'s textStyle. When applying a *TypefaceSpan* based on a font family: *"monospace"*, the resulted text will keep the italic style.

- 字体横向缩放 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/scale-x-span.png" alt="scale x" style="zoom: 40%;" /> Scales horizontally the size of the text to which it's attached by a certain factor.

```java
SpannableString string = new SpannableString("Text with ScaleX span");
// Values > 1.0 will stretch the text wider. Values < 1.0 will stretch the text narrower.
string.setSpan(new ScaleXSpan(2.0f), 10, 16, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 下划线 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/underline-span.png" alt="underline" style="zoom: 50%;" /> A span that underlines the text it's attached to.


```java
SpannableString string = new SpannableString("Text with underline span");
string.setSpan(new UnderlineSpan(), 10, 19, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

- 删除线 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/strikethrough-span.png" alt="strikethrough" style="zoom: 40%;" /> A span that strikes through the text it's attached to.

```java
SpannableString string = new SpannableString("Text with strikethrough span");
string.setSpan(new StrikethroughSpan(), 10, 23, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 滤镜 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/mask-filter-span.png" alt="mask filter" style="zoom: 50%;" /> Span that allows setting a `MaskFilter` to the text it's attached to.


```java
// For example, to blur a text, a BlurMaskFilter can be used:
MaskFilter blurMask = new BlurMaskFilter(5f, BlurMaskFilter.Blur.NORMAL);
SpannableString string = new SpannableString("Text with blur mask");
string.setSpan(new MaskFilterSpan(blurMask), 10, 15, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

- 可点击 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/clickable-span.png" alt="clickable" style="zoom: 50%;" />

> If an object of this type is attached to the text of a TextView with a movement method of LinkMovementMethod, the affected spans of text can be selected. If selected and clicked, the `onClick` method will be called. The text with a *ClickableSpan* attached will be underlined and the link color will be used as a text color. The default link color is the theme's accent color or `android:textColorLink` if this attribute is defined in the theme.
>
> For example, considering that we have a `CustomClickableSpan` that extends `ClickableSpan`, it can be used like this:


```java
SpannableString string = new SpannableString("Text with clickable text");
string.setSpan(new CustomClickableSpan(), 10, 19, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
textView.setMovementMethod(LinkMovementMethod.getInstance());
textView.setHighlightColor(Color.TRANSPARENT);// 隐藏点击后的背景高亮色
textView.setText(string);
```

- Url跳转 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/url-span.png" alt="url" style="zoom: 50%;" />

>Implementation of the *ClickableSpan* that allows setting a url string. When selecting and clicking on the text to which the span is attached, the **URLSpan** will try to open the url, by launching an an Activity with an *Intent#ACTION_VIEW* intent.


```java
SpannableString string = new SpannableString("Text with a url span");
string.setSpan(new URLSpan("https://developer.android.google.cn/"), 12, 15, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
textView.setMovementMethod(LinkMovementMethod.getInstance());
textView.setText(string);
```

- 引用

> A span which styles paragraphs by adding a vertical stripe at the beginning of the text (respecting layout direction). A QuoteSpan must be attached from the first character to the last character of a single paragraph, otherwise the span will not be displayed.
>
> **QuoteSpan** allow configuring the following elements:
>
> - **color** - the vertical stripe color. By default, the stripe color is 0xff0000ff.
> - **gap width** - the distance, in pixels, between the stripe and the paragraph. Default value is 2px.
> - **stripe width** - the width, in pixels, of the stripe. Default value is 2px.

For example, a QuoteSpan using the default values can be constructed like this: <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/default-quote-span.png" alt="quote" style="zoom: 40%;" />

```java
SpannableString string = new SpannableString("Text with quote span on a long line");
string.setSpan(new QuoteSpan(), 0, string.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

To construct a QuoteSpan with a green stripe, of 20px in width and a gap width of 40px: <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/custom-quote-span.png" alt="quote" style="zoom: 40%;" />

```java
SpannableString string = new SpannableString("Text with quote span on a long line");
// requires API 28
string.setSpan(new QuoteSpan(Color.GREEN, 20, 40), 0, string.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- Bullet

>A span which styles paragraphs as bullet points (respecting layout direction). BulletSpans must be attached from the first character to the last character of a single paragraph, otherwise the bullet point will not be displayed but the first paragraph encountered will have a leading margin.
>
>**BulletSpans** allow configuring the following elements:
>
>- **gap width** - the distance, in pixels, between the bullet point and the paragraph. Default value is 2px.
>- **color** - the bullet point color. By default, the bullet point color is 0 - no color, so it uses the TextView's text color.
>- **bullet radius** - the radius, in pixels, of the bullet point. Default value is 4px.

For example, a BulletSpan using the default values can be constructed like this: <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/default-bullet-span.png" alt="bullet" style="zoom: 40%;" />

```java
SpannableString string = new SpannableString("Text with\nBullet point");
string.setSpan(new BulletSpan(), 10, 22, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

To construct a BulletSpan with a gap width of 40px, green bullet point and bullet radius of 20px: <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/custom-bullet-span.png" alt="bullet" style="zoom: 40%;" />

```java
SpannableString string = new SpannableString("Text with\nBullet point");
// requires API 28
string.setSpan(new BulletSpan(40, Color.GREEN, 20), 10, 22, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 上标 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/superscript-span.png" alt="superscript" style="zoom: 40%;" /> The span that moves the position of the text baseline higher.

```java
SpannableString string = new SpannableString("1st example");
string.setSpan(new SuperscriptSpan(), 1, 3, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

***Note:*** Since the span affects the position of the text, if the text is on the first line of a TextView, it may appear cut. This can be avoided by decreasing the text size with an *AbsoluteSizeSpan*.


- 下标 <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/subscript-span.png" alt="subscript" style="zoom: 40%;" /> The span that moves the position of the text baseline lower.

```java
SpannableString string = new SpannableString("☕- C8H10N4O2\n");
string.setSpan(new SubscriptSpan(), 4, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new SubscriptSpan(), 6, 8, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new SubscriptSpan(), 9, 10, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new SubscriptSpan(), 11, 12, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

***Note:*** Since the span affects the position of the text, if the text is on the last line of a TextView, it may appear cut.


- Drawable Margin <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/drawable-margin-span.png" alt="drawable margin" style="zoom: 40%;" /> A span which adds a drawable and a padding to the paragraph it's attached to.

>If the height of the drawable is bigger than the height of the line it's attached to then the line height is increased to fit the drawable. **DrawableMarginSpan** allows setting a padding between the drawable and the text. The default value is **0**. The span must be set from the beginning of the text, otherwise either the span won't be rendered or it will be rendered incorrectly.
>
>For example, a drawable and a padding of 20px can be added like this:

```java
SpannableString string = new SpannableString("Text with a drawable.");
string.setSpan(new DrawableMarginSpan(drawable, 20), 0, string.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

***see:*** **IconMarginSpan** for working with a *Bitmap* instead of a *Drawable*.


- Icon Margin <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/icon-margin-span.png" alt="icon margin" style="zoom: 40%;" />

>Paragraph affecting span, that draws a bitmap at the beginning of a text. The span also allows setting a padding between the bitmap and the text. The default value of the padding is 0px. The span should be attached from the first character of the text.
>
>For example, an **IconMarginSpan** with a bitmap and a padding of 30px can be set like this:

```java
SpannableString string = new SpannableString("Text with icon and padding");
string.setSpan(new IconMarginSpan(bitmap, 30), 0, string.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- Dynamic Drawable <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/dynamic-drawable-span.png" alt="dynamic drawable" style="zoom: 40%;" />

>Span that replaces the text it's attached to with a *Drawable* that can be aligned with the bottom or with the baseline of the surrounding text. For an implementation that constructs the drawable from various sources (*Bitmap*, *Drawable*, resource id or *Uri*) use ImageSpan.
>
>A simple implementation of **DynamicDrawableSpan** that uses drawables from resources looks like this:

```java
class MyDynamicDrawableSpan extends DynamicDrawableSpan {
    private final Context mContext;
    private final int mResourceId;
    public MyDynamicDrawableSpan(Context context, @DrawableRes int resourceId) {
        mContext = context;
        mResourceId = resourceId;
    }
    @Override
    public Drawable getDrawable() {
        Drawable drawable = mContext.getDrawable(mResourceId);
        drawable.setBounds(0, 0, drawable.getIntrinsicWidth(), drawable.getIntrinsicHeight());
        return drawable;
    }
}
```

The class can be used to replace text with a drawable like this:

```java
SpannableString string = new SpannableString("Text with a drawable span");
string.setSpan(new MyDynamicDrawableSpan(this, R.mipmap.ic_launcher), 12, 20, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- Image <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/image-span.png" alt="image" style="zoom: 30%;" />

>Span that replaces the text it's attached to with a *Drawable* that can be aligned with the bottom or with the baseline of the surrounding text. The drawable can be constructed from varied sources:
>
>- **Bitmap** - `ImageSpan(Context, Bitmap)` and `ImageSpan(Context, Bitmap, int)`
>- **Drawable** - `ImageSpan(Drawable, int)`
>- **resource id** - `ImageSpan(Context, int, int)`
>- **Uri** - `ImageSpan(Context, Uri, int)`
>
>The default value for the vertical alignment is *DynamicDrawableSpan#ALIGN_BOTTOM*. For example, an **ImagedSpan** can be used like this:

```java
SpannableString string = new SpannableString("Bottom: span.\nBaseline: span.");
// using the default alignment: ALIGN_BOTTOM
string.setSpan(new ImageSpan(this, R.mipmap.ic_launcher), 7, 8, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
string.setSpan(new ImageSpan(this, R.mipmap.ic_launcher, DynamicDrawableSpan.ALIGN_BASELINE), 22, 23, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
```


- 叠加多种效果：**SpannableStringBuilder**，如《用户协议》前景高亮并下划线

```java
TextView tvProtocol = findViewById(R.id.tvProtocol);
SpannableStringBuilder builder = new SpannableStringBuilder(tvProtocol.getText());
builder.setSpan(new ForegroundColorSpan(Color.BLUE), 1, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
builder.setSpan(new UnderlineSpan(), 1, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
tvProtocol.setText(builder);
```



### 搜索过滤并高亮显示

1. 在Adapter中声明私有内部Filter子类，自定义过滤逻辑
2. Adapter实现Filterable接口，返回Filter子类实例
3. 调用 `adapter.getFilter().filter(..);` 过滤

[搜索过滤已加载列表数据并高亮显示结果](https://www.jianshu.com/p/72b13f27bb11)

[模糊搜索](https://www.jianshu.com/p/62be70e527f0)

[输入过滤器](https://www.jianshu.com/p/5b5cef2ffff2) android.text.InputFilter

`String filtered = new InputFilter.AllCaps().filter(..).toString();`

`String filtered = new InputFilter.LengthFilter(3).filter(..).toString();`

- 示例1

```java
private class ListFilter extends Filter {
    private List<Entity> mDataSetBackup;

    {
        // 备份原始数据(只需执行一次)
        mDataSetBackup = new ArrayList<>();
        mDataSetBackup.addAll(dataSet);
    }

    @Override
    protected FilterResults performFiltering(CharSequence constraint) {
        FilterResults results = new FilterResults();
        // 搜索关键词
        String keyword = constraint.toString().trim().toLowerCase();
        if (TextUtils.isEmpty(keyword)) {
            // 空搜索时直接使用备份数据
            results.values = mDataSetBackup;
            results.count = mDataSetBackup.size();
        } else {
            // 根据搜索条件筛选过滤
            List<Entity> filteredDataSet = new ArrayList<>();
            for (int i = 0, len = mDataSetBackup.size(); i < len; i++) {
                if (mDataSetBackup.get(i).getName().toLowerCase().startsWith(keyword)) {
                    filteredDataSet.add(mDataSetBackup.get(i));
                }
            }
            results.values = filteredDataSet;
            results.count = filteredDataSet.size();
        }
        return results;
    }

    @Override
    protected void publishResults(CharSequence constraint, FilterResults results) {
        // 将筛选后数据设置给列表
        setDataSet((List<Entity>) results.values);
    }
}
```

- 示例2（必须加锁同步？是多线程吗？不是吧）

```java
public FilterAdapter extends RecyclerView.Adapter implements Filterable {
    private ArrayList<Bean> mDataSet;
    /*
     * Filter relevant
     */
    private final Object mLock = new Object();
    private MyFilter mFilter;
    private String keyword;
    
    /* RecyclerViewAdapter implementations
    e.g. onCreateViewHolder/onBindViewHolder/RecyclerView.ViewHolder etc. */
    @Override
    public void onBindViewHolder(@NonNull ItemViewHolder holder, int position) {
        final Bean data = mDataSet.get(position);
        /* There is no expected effect that use SpannableString
           as 'String.format's argument,
           e.g. String.format(getString(R.string.text), match(data.getField()));
           And we should use 'String.format's return value as SpannableString's
           constructor argument in order to be effected properly. */
        holder.tv.setText(
            TextUtils.isEmpty(keyword) ?
            String.format(context.getString(R.string.text), data.getField()) :
            match(
                String.format(context.getString(R.string.text), data.getField()),
                keyword,
                context.getResources().getColor(R.color.colorAccent),
                true));
    }
    
    @Override
    public Filter getFilter() {
        if (mFilter == null) {
            mFiletr = new MyFilter();
        }
        return mFilter;
    }

    // This should not be static, otherwise we cannot use outer class's non-static field
    private class MyFilter extends Filter {
        private ArrayList<Bean> mDataSetBackup;

        MyFilter() {}
        
        @Override
        protected FilterResults performFiltering(CharSequence constraint) {
            /* how to do after DataSet changed? Maybe DiffUtil.
               If that, perfect the condition. */
            if (mDataSetBackup == null) {
                synchronized(mLock) {
                    mDataSetBackup = mDataSet;
                }
            }
            keyword = constraint.toString().trim().toLowerCase();
            ArrayList<Bean> filteredDataSet = new ArrayList<>();
            if (TextUtils.isEmpty(constraint)) {
                synchronized(mLock) {
                    filteredDataSet = mDataSetBackup;
                }
            } else {
                for (int i = 0, len = mDataSetBackup.size(); i < len; i++) {
                    String field =
                        mDataSetBackup.get(i).getField().toLowerCase();
                    // startWith or contains keyword?
                    if (field.contains(keyword)) {
                        filteredDataSet.add(mDataSetBackup.get(i));
                    }
                }
            }
            FilterResults results = new FilterResults();
            results.values = filteredDataSet;
            return results;
        }
        
        @Override
        protected void publishResults(CharSequence constraint, FilterResults results) {
            mDataSet = (ArrayList<Bean>) results.values;
            notifyDataSetChanged();
        }
    }
    
    /**
     * match keyword and return spannable string
     *
     * @param source     source string
     * @param keyword    keyword
     * @param color      fill color
     * @param ignoreCase whether ignore case
     * @return spannable string
     */
    public SpannableString match(String source, String keyword,
                                 int color, boolean ignoreCase) {
        SpannableString spannable = new SpannableString(source);
        Matcher matcher = Pattern
            .compile(ignoreCase ? keyword.trim().toLowerCase() : keyword.trim())
            .matcher(ignoreCase ? spannable.toString().trim().toLowerCase() :
                     spannable.toString().trim());
        while (matcher.find()) {
            spannable.setSpan(
                new ForegroundColorSpan(color),
                matcher.start(),
                matcher.end(),
                Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
        }
        return spannable;
    }
}
```

- 示例3（来自ArrayAdapter#ArrayFilter）

```java
public class FilterAdapter<T>
        extends BaseAdapter
        implements Filterable {
    private final Object mLock = new Object();
    private List<T> dataSet;
    private ArrayList<T> originalDataSet;
    private ArrayFilter mFilter;

    public void addDataSet(@NonNull List<T> dataSet) {
        if (dataSet.size() == 0) return;
        synchronized (mLock) {
            if (originalDataSet != null) {
                originalDataSet.addAll(dataSet);
            } else {
                this.dataSet.addAll(dataSet);
            }
        }
        notifyDataSetChanged();
    }

    public List<T> getDataSet() {
        return dataSet == null ? new ArrayList<>() : dataSet;
    }

    public void resetDataSet() {
        if (dataSet.size() == 0) return;
        synchronized (mLock) {
            if (originalDataSet != null) {
                originalDataSet.clear();
            } else {
                dataSet.clear();
            }
        }
        notifyDataSetChanged();
    }

    public void updateItemAt(int position, @Nullable T payload) {
        synchronized (mLock) {
            if (originalDataSet != null) {
                originalDataSet.set(position, payload);
            } else {
                dataSet.set(position, payload);
            }
        }
        notifyDataSetChanged();
    }

    public void removeItemAt(int position) {
        synchronized (mLock) {
            if (originalDataSet != null) {
                originalDataSet.remove(position);
            } else {
                dataSet.remove(position);
            }
        }
        notifyDataSetChanged();
    }

    public void sortDataSet(@NonNull Comparator<? super T> comparator) {
        synchronized (mLock) {
            if (originalDataSet != null) {
                Collections.sort(originalDataSet, comparator);
            } else {
                Collections.sort(dataSet, comparator);
            }
        }
        notifyDataSetChanged();
    }

    @Override
    public int getCount() { return 0; }

    @Override
    public Object getItem(int position) { return null; }

    @Override
    public long getItemId(int position) { return 0; }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) { return null; }

    @Override
    public Filter getFilter() {
        if (mFilter == null) {
            mFilter = new ArrayFilter();
        }
        return mFilter;
    }

    private class ArrayFilter extends Filter {
        @Override
        protected FilterResults performFiltering(CharSequence prefix) {
            FilterResults results = new FilterResults();

            if (originalDataSet == null) {
                synchronized (mLock) {
                    originalDataSet = new ArrayList<>(dataSet);
                }
            }

            if (prefix == null || prefix.length() == 0) {
                ArrayList<T> list;
                synchronized (mLock) {
                    list = new ArrayList<>(originalDataSet);
                }
                results.values = list;
                results.count = list.size();
            } else {
                String prefixString = prefix.toString().toLowerCase();

                ArrayList<T> values;
                synchronized (mLock) {
                    values = new ArrayList<>(originalDataSet);
                }

                int count = values.size();
                ArrayList<T> newValues = new ArrayList<>();

                for (int i = 0; i < count; i++) {
                    T value = values.get(i);
                    String valueText = value.toString().toLowerCase();

                    // First match against the whole, non-splitted value
                    if (valueText.startsWith(prefixString)) {
                        newValues.add(value);
                    } else {
                        String[] words = valueText.split(" ");
                        for (String word : words) {
                            if (word.startsWith(prefixString)) {
                                newValues.add(value);
                                break;
                            }
                        }
                    }
                }

                results.values = newValues;
                results.count = newValues.size();
            }

            return results;
        }

        @Override
        protected void publishResults(CharSequence constraint, FilterResults results) {
            dataSet = (List<T>) results.values;
            if (results.count > 0) {
                notifyDataSetChanged();
            } else {
                notifyDataSetInvalidated();
            }
        }
    }
}
```



### 圆角图片的原生实现方式

```java
RoundedBitmapDrawable rounded = RoundedBitmapDrawableFactory.create(
    getResources(),
    BitmapFactory.decodeResource(getResources(), R.drawable.drawable));
rounded.setCircular(true);
```



### [不要在Application对象中缓存数据](https://www.jianshu.com/p/83f0046bc310)



### ListView/GridView空数据时的友好提示

[:accept:](https://likfe.com/2017/07/07/android-empty-view/) `setEmptyView();`



### 状态栏高度

- 老方式：

```java
int identifier = Resources.getSystem().getIdentifier("status_bar_height", "dimen", "android");
int statusBarHeight = Resources.getSystem().getDimensionPixelSize(identifier);
```

- 新方式：

```java
Rect windowVisibleDisplayFrameRect = new Rect();
getWindow().getDecorView().getWindowVisibleDisplayFrame(windowVisibleDisplayFrameRect);
int statusBarHeight = windowVisibleDisplayFrameRect.top;
```



### 软键盘相关

#### 隐藏软键盘

```java
public static boolean hideKeyboard(final View view) {
    if (view == null) return false;
    InputMethodManager mgr = (InputMethodManager) view.getContext()
                .getApplicationContext().getSystemService(Context.INPUT_METHOD_SERVICE);
    // 即使当前焦点不在editText上，也可以隐藏
    return mgr != null && mgr.hideSoftInputFromWindow(view.getWindowToken(),
            InputMethodManager.HIDE_NOT_ALWAYS);
}
```

#### 监听软键盘可见性

```java
/**
 * 监听软键盘可见性
 *
 * @param v        Activity中任意非空view
 * @param observer 监听接口
 */
public static void observeVisibility(@NonNull View v, @NonNull OnVisibilityObserver observer) {
    View root = v.getRootView();
    ViewTreeObserver viewTreeObserver = root.getViewTreeObserver();
    viewTreeObserver.addOnGlobalLayoutListener(
            new ViewTreeObserver.OnGlobalLayoutListener() {
                private final Rect r = new Rect();
                private final int estimatedVisibleThreshold = Math.round(PixelUtils.dip2px(100));
                private boolean wasVisible = false;

                @Override
                public void onGlobalLayout() {
                    root.getWindowVisibleDisplayFrame(r);
                    int heightDiff = root.getRootView().getHeight() - r.height();
                    boolean visible = heightDiff > estimatedVisibleThreshold;
                    if (visible == wasVisible) return; // keyboard state has not changed
                    wasVisible = visible;
                    boolean remove = observer.onChanged(visible, heightDiff);
                    if (remove) {
                        viewTreeObserver.removeOnGlobalLayoutListener(this);
                    }
                }
            });
}
```

***ps:*** This approach will cause issues if there is a floating soft keyboard.

```java
public interface OnVisibilityObserver {
    /**
     * 软键盘可见性变化
     *
     * @param visible    是否可见
     * @param heightDiff 高度差
     * @return true to remove global listener
     */
    boolean onChanged(boolean visible, int heightDiff);
}
```

[Android软件盘顶起底部控件的解决方案|jishudog.com](https://www.jishudog.com/14370/html)

[监听软键盘的显示与隐藏|xlee00.github.io](https://xlee00.github.io/2017/01/20/监听软键盘的显示与隐藏/)



### Fragment返回

[:accept:](https://www.jianshu.com/p/fff1ef649fc0)



### Home键/多任务键点击、锁屏监听

[监听相应广播](https://www.jianshu.com/p/b1ef4a6d52b0)



### 应用前后台切换监听

[Android松耦合监听前后台切换框架](https://juejin.cn/post/6844903797345484807)

- 重写Application的 `onTrimMemory`

  ```java
  @Override
  public void onTrimMemory(int level) {
      super.onTrimMemory(level);
      if (TRIM_MEMORY_UI_HIDDEN == level) {
          Log.d(TAG, "Backgrounded.");
      }
  }
  ```

- ProcessLifecycleOwner

  ```java
  ProcessLifecycleOwner.get().getLifecycle().addObserver(new LifecycleObserver() {
      @OnLifecycleEvent(Lifecycle.Event.ON_START)
      void onForeground() {
          Log.d(TAG, "Foregrounded.");
      }
      @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
      void onBackground() {
          Log.d(TAG, "Backgrounded.");
      }
  });
  ```

- `Application#registerActivityLifecycleCallbacks` 使用一个int变量统计处于前后台Activity的数量，当为0时说明处于后台？



### 监控FPS

关键：`Choreographer.getInstance().postFrameCallback();`

```java
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    ...
    getWindow().getDecorView().getViewTreeObserver()
            .addOnDrawListener(new ViewTreeObserver.OnDrawListener() {
                @Override
                public void onDraw() {
                    Choreographer.getInstance()
                            .postFrameCallback(new Choreographer.FrameCallback() {
                                @Override
                                public void doFrame(long frameTimeNanos) {
                                    // TODO: calculate fps
                                }
                            });
                }
            });
}
```



## 品牌设备特殊方案

### 华为

#### 移除全屏显示提示

- 在AndroidManifest.xml中的application节点下添加：

  `<meta-data android:name="android.max_aspect" android:value="2.4" />` (API26: android:maxAspectRatio="2.4" 无效 ?) *2.4* > 设备横纵比。

- 在AndroidManifest.xml中对应的Activity中添加属性 `android:resizeableActivity ="true"` 注意：此设置只针对Activity生效，且添加了此属性的Activity还会支持分屏显示。

- 设置 *targetSdkVersion>=26*（注：若应用已适配到API26，并已通过meta-data的 `android.max_aspect` 属性或者application的 `android:maxAspectRatio` 属性支持了最大纵横比，同时又通过 `android:resizeableActivity="false"` 禁用了分屏，此时系统会按照应用自己所设置的最大纵横比决定Activity是否能全屏显示，若应用设置的最大纵横仍小于手机屏幕比例，则应用仍无法全屏显示）。



#### 不打印log

部分华为设备工程模式下log是关闭的

1. 华为手机：拨号界面输入 **`*#*#2846579#*#*`** 进入设置；
2. 华为pad：计算机界面输入 **`()()2846579()()=`** 进入设置。