[TOC]

# Android Hybrid WebView

- 关键类

`android.webkit.WebView` 用于展示网页的View

`android.webkit.WebSettings` 管理WebView各项配置状态

`android.webkit.WebViewClient` 用于接收WebView的各种通知和请求

`android.webkit.WebChromeClient` 用于处理JavaScript对话框，标题，图标和进度等



## 用法简单示例

1. 声明网络权限

```xml
<manifest ... >
    <uses-permission android:name="android.permission.INTERNET" />
    ...
</manifest>
```

2. 在xml布局中声明WebView控件

```xml
<WebView
    android:id="@+id/webview"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

3. 调用 `loadXxx` 方法加载内容（网址/HTML/资源文件）

```java
WebView webView = findViewById(R.id.webview);
webView.loadUrl(String url);
webView.loadUrl(String url, Map<String, String> additionalHttpHeaders);
webView.loadData(String data, @Nullable String mimeType, @Nullable String encoding);
webView.loadDataWithBaseURL(@Nullable String baseUrl, String data, @Nullable String mimeType, @Nullable String encoding, @Nullable String historyUrl);
webView.postUrl(@NonNull String url, @NonNull byte[] postData);
```

***ps:*** `loadData` 加载中文数据乱码，可使用 **"text/html;charset=UTF-8"** 作为 *mimeType* 入参解决；或改用 `loadDataWithBaseURL` 加载。

自定义WebView子类时，建议每个构造器都调用 `super();` 保留WebView原生样式。而非前3个构造器调用 `this();`，最后一个调用 `super();`（已知问题如：滑动指示条消失）。





## 基础功能

### 获取设备当前WebView内核*PackageInfo*

可选，用于调试，注意与当前应用程序 *PackageInfo* 区分。

```java
PackageInfo pkgInfo = WebViewCompat.getCurrentWebViewPackage(@NonNull Context context);
if (pkgInfo != null) {
    Log.d(TAG, "WebViewVersion: " + pkgInfo.versionName);
}
```



### 分配渲染器进程优先级策略

可选，无特殊需求请保持默认。以下配置可用于提高默认优先级。

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    webView.setRendererPriorityPolicy(WebView.RENDERER_PRIORITY_IMPORTANT, false);
}
```



### 设置Web Clients

根据开发需求选择。

```java
webView.setWebViewClient(new WebViewClient());
webView.setWebChromeClient(new WebChromeClient());
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
    setWebViewRenderProcessClient(new WebViewRenderProcessClient() {..});
}
```



### 设置初始缩放比

可选，默认行为取决于 `{@link WebSettings#getUseWideViewPort()}` 和 `{@link WebSettings#getLoadWithOverviewMode()}` 的状态。

```java
webView.setInitialScale(int scaleInPercent);
```



### 缩放控制

```java
//放大
webView.zoomIn();
//缩小
webView.zoomOut();
//相对倍数缩放(0.01f <= zoomFactor <= 100.0f)
webView.zoomBy((float zoomFactor));
```

***ps:*** 缩放监听 `WebViewClient#onScaleChanged` 。



### 导航控制

- 后退控制

```java
@Override
public void onBackPressed() {
    //在Back按键回调中检查 back/forward list history
    if (webView != null && webView.canGoBack()) {
        webView.goBack();
    } else {
        //若非Back事件或没有 web page history，默认系统行为
        super.onBackPressed();
    }
}
```

- 前进控制

  `webView.canGoForward();`

  `webView.goForward();`

- 多步导航（*steps* 大于0前进，小于0后退）

  `webView.canGoBackOrForward(int steps);`

- 复制 back/forward list history 备份

  `webView.copyBackForwardList();`

- 清空 web page history

  `webView.clearHistory();`



### 翻页控制

```java
//向上翻页，top：false 向上滚动半个WebView高度；true 向上滚动到顶部
webView.pageUp(boolean top);
//向下翻页，bottom：false 向下滚动半个WebView高度；true 向下滚动到底部
webView.pageDown(boolean bottom);
```



### 重新加载

```java
if (webView != null) {
    if (webView.getProgress() < 100) webView.stopLoading();
    webView.reload();
}
```



### 网络状态

通知WebView网络是否可用

```java
webView.setNetworkAvailable(boolean networkUp);
```

用于设置JavaScript属性 `window.navigator.onLine` 的值，并触发 *online/offline* 事件

```javascript
<head>
    <!-- ... -->
    window.ononline = (event) => { console.log(`online=${window.navigator.onLine}`); }
    window.onoffline = (event) => { console.log(`online=${window.navigator.onLine}`); }
</head>
```

***ps:*** 需要<u>变化</u>才会产生事件，即若当前已是online，*networkUp* 设置 **true** 并不会触发online事件，而此时设置 **false** 则会触发offline事件。



### 清理资源

- `webView.clearFormData();` 清除表单数据
- `webView.clearHistory();` 清除 back/forward list，注意具体使用细节
- `webView.clearSslPreferences();` 清除SSL/TLS偏好
- `webView.clearCache(boolean includeDiskFiles);` 清除缓存
- `WebView.clearClientCertPreferences(null);` 清除客户端证书偏好



### 生命周期资源控制

```java
@Override
protected void onResume() {
    super.onResume();
    if (webView != null) {
        webView.onResume();
        webView.resumeTimers();
    }
}

@Override
protected void onPause() {
    if (webView != null) {
        webView.onPause();
        webView.pauseTimers();
    }
    super.onPause();
}

@Override
protected void onDestroy() {
    if (webView != null) {
        webView.stopLoading();
        //加载空白页，注意解决GoBack问题
        // webView.loadDataWithBaseURL(null, "", null, "utf-8", null);
        
        //region 清除资源
        webView.clearFormData();
        //清除 back/forward list，注意具体使用细节
        webView.clearHistory();
        webView.clearSslPreferences();
        webView.clearCache(false);
        WebView.clearClientCertPreferences(null);
        //endregion

        webView.setWebViewClient(null);
        webView.setWebChromeClient(null);

        //先从布局结构中移除WebView，再destroy
        ViewParent rootView = webView.getParent();
        if (rootView instanceof ViewGroup) {
            ((ViewGroup) rootView).removeView(webView);
        }
        webView.destroy();
        webView = null;
    }
    super.onDestroy();
}
```



### 网页查找(字符串)

1. 设置查找结果监听

```java
webView.setFindListener(new WebView.FindListener() {
    @Override
    public void onFindResultReceived(int activeMatchOrdinal, int numberOfMatches, boolean isDoneCounting) {
    }
});
```

2. 异步执行查找，并高亮显示所有结果

```java
webView.findAllAsync(String find);
```

3. 在<u>上一步的基础上</u>向前/后查找（`forward: false/true` 上一个/ 下一个）

```java
webView.findNext(boolean forward);
```

4. 清除高亮匹配结果

```java
webView.clearMatches();
```



**实现方式**

------

- 方式一（内置方式，使用ActionMode实现，已废弃但仍可方便地使用）

```java
webView.showFindDialog(@Nullable String text, boolean showIme);
```

- 方式二（自定义ActionMode）

```java
toolbar.startActionMode(new ActionMode.Callback() {
    @Override
    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
        mode.getMenuInflater().inflate(R.menu.find_0n_page, menu);
        MenuItem findOnPage = menu.findItem(R.id.findOnPage);
        //注意这里若使用SearchView会有一些坑，如不能声明于menu xml中(无效，暂未深入研究原因)。当然也可以采用其他方式。
        findOnPage.setActionView(new SearchView(mContext));
        // SearchView剩余配置.. 在其中会调用 findAllAsync() 进行查找
        return true;
    }

    @Override
    public boolean onPrepareActionMode(ActionMode mode, Menu menu) { return false; }

    @Override
    public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
        if (webView == null) return false;
        switch (item.getItemId()) {
            case R.id.findPrev:
                webView.findNext(false);//向前匹配
                break;
            case R.id.findNext:
                webView.findNext(true);//向后匹配
                break;
            default:
                return false;
        }
        return true;
    }

    @Override
    public void onDestroyActionMode(ActionMode mode) {
        if (web != null) web.clearMatches();
    }
});
```

- 其他方式..



### 保存网页

保存当前网页到文件

```java
webView.saveWebArchive(String filename);
webView.saveWebArchive(String basename, boolean autoname, @Nullable ValueCallback<String> callback);
```

**实现**

------

```java
File docDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOCUMENTS);
if (webView == null || (!docDir.exists() && !docDir.mkdirs())) return false;
webView.saveWebArchive(new File(docDir
            + File.separator
            + URLUtil.guessFileName(webView.getUrl(), null, null))
            .getPath(),
            false,
            new ValueCallback<String>() {
                @Override
                public void onReceiveValue(String path) {
                    Toast.makeText(mContext,
                        TextUtils.isEmpty(path) ? "保存失败" : "已保存到 " + path,
                        Toast.LENGTH_LONG)
                        .show();
                    }
                });
```



### 滑动监听

API23 之前覆写 `onScrollChanged` 方法实现；从 API23 开始，使用 `setOnScrollChangeListener` 方法监听。

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    webView.setOnScrollChangeListener(new View.OnScrollChangeListener() {
        @Override
        public void onScrollChange(View v, int scrollX, int scrollY, int oldScrollX, int oldScrollY) {
            if (scrolledAtTop((WebView) v)) {
                Log.d(TAG, "already at top");
            } else if (WebViewHelper.scrolledAtBottom((WebView) v)) {
                Log.d(TAG, "already at bottom");
            }
        }
    });
}
```

```java
/** {@param webView}已处于最顶端 */
public boolean scrolledAtTop(@NonNull WebView webView) {
    return webView.getScrollY() == 0;
}
```

```java
/**
 * {@param webView}已处于最底端 :
 * webView.getContentHeight() * webView.getScale() == webView.getHeight() + webView.getScrollY()
 * ps:{@link WebView#getScale()}因为容易出现误差已被废弃，以下实现认为小于一定误差即处于底端
 */
public static boolean scrolledAtBottom(@NonNull WebView webView) {
    return Math.abs(webView.getContentHeight() * webView.getScale()
            - (webView.getHeight() + webView.getScrollY())) <= 2;// 误差小于2px
}
```

***ps:*** `WebView#flingScroll(int vx, int vy);` 模拟滑动, 参数为x/y方向的速度, 正数上/左滑, 负数下/右滑



### 下载监听

当网页内容无法被渲染引擎处理，将会执行下载。

```java
webView.setDownloadListener(DownloadListener listener);
```

***ps:*** 下载文件时，若文件名称中包含 **../** 字符，则WebView需要对此类文件名进行过滤，否则会暴露 :warning: [目录穿越漏洞](https://websec.readthedocs.io/zh/latest/vuln/pathtraversal.html) 。

**实现方式**

------

- 方式一（转交给浏览器等具备下载功能的第三方应用）

```java
webView.setDownloadListener(new DownloadListener() {
    @Override
    public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype, long contentLength) {
        Intent it = new Intent(Intent.ACTION_VIEW);
        it.addCategory(Intent.CATEGORY_BROWSABLE);
        it.setData(Uri.parse(url));
        startActivity(it);
    }
});
```

- 其他方式..



### 保存/打印PDF

[打印文件|Android 开发者|Android Developers (google.cn)](https://developer.android.google.cn/training/printing)

```java
webView.createPrintDocumentAdapter(@NonNull String documentName);
```

**实现**

------

```java
PrintManager printMgr = (PrintManager) getSystemService(Context.PRINT_SERVICE);
if (webView == null || printMgr == null) return;
PrintDocumentAdapter printAdapter = webView.createPrintDocumentAdapter(
        URLUtil.guessFileName(web.getUrl(), null, "application/pdf"));
PrintJob webViewPrintJob = printMgr.print("WebViewPrintJob", printAdapter, new PrintAttributes.Builder().build());
```

***ps:*** 要在页面加载完成后再打印，否则可能不完整或空白。



### 其它方法

```java
private Handler mH = new Handler(Looper.getMainLooper()) {
    @Override
    public void handleMessage(@NonNull Message msg) {
        super.handleMessage(msg);
        String imageRef = msg.getData().getString("url");
        if (!TextUtils.isEmpty(imageRef)) Log.d(TAG, "ImageRef: " + imageRef);
    }
};

//长按保存图片?
webView.setOnLongClickListener(new OnLongClickListener() {
    @Override
    public boolean onLongClick(View v) {
        //请求最近触摸的图像元素URL
        webView.requestImageRef(Message.obtain(mH));
        return false;
    }
});
//文档中是否包含图片
//webView.documentHasImages(Message.obtain(mH));
```

```java
@Override
public void onLoadResource(WebView view, String url) {
    super.onLoadResource(view, url);
    //命中的 HTML tag类型如：HTML::img <5> HTML::a <7>等
    WebView.HitTestResult result = view.getHitTestResult();
    if (result.getType() == WebView.HitTestResult.UNKNOWN_TYPE) {
        view.requestFocusNodeHref(Message.obtain(mH));
    }
}
```





## 功能配置

`WebSettings settings = webView.getSettings();`

以下功能均基于 *settings* 设置。

### JS功能支持

- JS交互功能控制，默认false

```java
settings.setJavaScriptEnabled(boolean flag);
```

***ps:*** 建议在 `onResume` 中启用，在 `onPause` 中禁用，防止后台执行JavaScript。

- 能否通过 JS 函数 `window.open()` 打开新网址，默认false

```java
settings.setJavaScriptCanOpenWindowsAutomatically(boolean flag)
```



### 布局/视口/缩放配置

- 宽度自适应

```java
//是否使用 viewport meta tag，建议根据开发配置
settings.setUseWideViewPort(boolean use);
//是否在网页较宽时全览显示，默认false，建议取决于settings.getUseWideViewPort()
settings.setLoadWithOverviewMode(boolean overview);
//设置底层layout算法，默认值已从API19开始废弃，建议设置 WebSettings.LayoutAlgorithm.NORMAL 保持最大兼容性
settings.setLayoutAlgorithm(LayoutAlgorithm l);
```

- 缩放控制

```java
//支持缩放功能(控件/手势)，默认true
settings.setSupportZoom(boolean support);
//是否使用WebView内置缩放机制，默认false，建议 settings.supportZoom() 或 true
settings.setBuiltInZoomControls(boolean enabled);
//是否在使用内置缩放机制时显示缩放控件，默认true，建议false
settings.setDisplayZoomControls(boolean enabled);
```

- 可选，初始焦点，默认true

```java
settings.setNeedInitialFocus(boolean flag);
```

- 可选，禁用网页纯文本长按菜单选项（API24开始支持，默认长按菜单选项：复制/粘贴/全选/搜索）

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
    settings.setDisabledActionModeMenuItems(@MenuItemFlags int menuItems);
}
```

- 可选，强制暗黑模式（API29开始支持）

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
    settings.setForceDark(WebSettings.FORCE_DARK_AUTO);
}
```



### 文本字体控制

以下方法示例设置的均为默认值。

- 默认文本编码格式

```java
settings.setDefaultTextEncodingName("UTF-8");
```

- 文本大小缩放比，默认值100

```java
settings.setTextZoom(100);
```

- 字体尺寸

```java
settings.setDefaultFontSize(16);//优先级更高
settings.setDefaultFixedFontSize(16);//效果未知？
settings.setMinimumFontSize(8);//影响整体文本最小尺寸，包括精确值如8px
settings.setMinimumLogicalFontSize(8);//只影响非精确文本最小尺寸，如smaller/80%
```

- 字体样式系列

```java
settings.setStandardFontFamily("sans-serif");
settings.setSerifFontFamily("sans-serif");
settings.setSansSerifFontFamily("sans-serif");
settings.setCursiveFontFamily("cursive");
settings.setFixedFontFamily("monospace");
settings.setFantasyFontFamily("fantasy");
```



### 自定义User Agent

可选，建议保留默认UA，同时根据开发需求追加自定义UA，即 `WebSettings.getDefaultUserAgent(context) + "Custom UA"`。

```java
settings.setUserAgentString(@Nullable String ua);
```

***ps:*** 从API19开始，在加载过程中改变UA将导致重新加载。



### 网络资源加载控制

- 屏蔽任何网络资源加载，若已声明 *android.permission.INTERNET* 权限，默认false，否则默认true

```java
settings.setBlockNetworkLoads(boolean flag);
```

***ps:*** 动态更改 *flag* 值由 *true* 到 *false* 并不会立即加载所屏蔽的网络资源，直到下次调用 `webView.reload();` 后才会重新加载。

- 屏蔽所有图片加载（无图模式），包括来自网络和使用 *data URI scheme* 嵌入的，默认true，建议 `!settings.getBlockNetworkLoads()`

```java
settings.setLoadsImagesAutomatically(boolean flag);
```

- 仅屏蔽网络图片加载，即通过 *http/https URI scheme* 访问的图片资源（节流模式），默认false，建议 `settings.getBlockNetworkLoads()`

```java
settings.setBlockNetworkImage(boolean flag);
```

***ps:*** 仅在 `settings.getLoadsImagesAutomatically()` 返回 true 时配置才有效。



### Https网页混合加载Http资源

从target API21开始默认禁止混合加载 *MIXED_CONTENT_NEVER_ALLOW*，建议设置 *MIXED_CONTENT_COMPATIBILITY_MODE* 以提高兼容性（此模式会尝试安全加载部分类型的http资源，具体类型根据版本变化，未具体定义），另可根据开发需求调整。

```java
settings.setMixedContentMode(int mode);
```



### 缓存配置

- 可实现 **离线加载** 功能，默认 *LOAD_DEFAULT*，无特殊需求建议保持默认，或监听网络情况动态配置

```java
settings.setCacheMode(@CacheMode int mode);
```

- 是否启用DOM storage API *(store key/value pairs)*，默认false，建议true

```java
settings.setDomStorageEnabled(boolean flag);
```

***ps:*** 已知一些网页<u>加载不出</u>或<u>加载不全</u>的情况，开启此设置会得到改善。

- 是否启用Application Caches API

```java
settings.setAppCacheEnabled(boolean flag);//默认false
settings.setAppCachePath(String appCachePath);//为了开启App Cache，必须设置缓存路径
settings.setAppCacheMaxSize(long appCacheMaxSize);//已废弃，建议无特殊需求保持默认，系统会自动管理
```

***ps:*** `WebStorage.getInstance();` 用于管理Application Caches/Web SQL Database/Web Storage。



### Cookie管理

- 是否允许cookies，默认true（*data/data/package_name/app_webview/Cookies.db*）

```java
CookieManager.getInstance().setAcceptCookie(boolean accept);
```

- 是否允许 *file* 协议cookies，默认阻止（false），建议保持false（`androidx.webkit.WebViewAssetLoader`）

```java
CookieManager.setAcceptFileSchemeCookies(boolean accept);
```

- 是否允许第三方cookies，从API21开始默认阻止（false），建议保持false（只针对单个WebView对象）

```java
CookieManager.getInstance().setAcceptThirdPartyCookies(WebView webview, boolean accept);
```

- 是否存在已存储cookies

```java
CookieManager.getInstance().hasCookies();
```

- 设置cookies

```java
CookieManager.getInstance().setCookie(String url, String value);
CookieManager.getInstance().setCookie(String url, String value, @Nullable ValueCallback<Boolean> callback);
```

- 同步cookies

```java
CookieManager.getInstance().flush();
```

```java
CookieManager.getInstance().setCookie(url, "_test=cookies", new ValueCallback<Boolean>() {
    @Override
    public void onReceiveValue(Boolean set) {
        if (set) CookieManager.getInstance().flush();
    }
});
```

- 获取cookies

```java
CookieManager.getInstance().getCookie(String url);
```

- 清除cookies

```java
CookieManager.getInstance().removeSessionCookies(@Nullable ValueCallback<Boolean> callback);
CookieManager.getInstance().removeAllCookies(@Nullable ValueCallback<Boolean> callback);
```



### 文件访问控制

:warning: File域控制不严格漏洞

- 控制 *content://* 协议资源访问（来自Content Provider），默认true，建议保持默认

```java
settings.setAllowContentAccess(boolean allow);
```

- 控制 *file://* 协议资源访问，默认true

```java
settings.setAllowFileAccess(boolean allow);
```

***ps:*** 只影响文件系统（外存、SD卡..）资源访问，不影响访问来自*assets*和*res*的资源，即 `file:///android_asset` 和 `file:///android_res`。

- 控制通过 *file://* 协议加载的 JS 能否访问其他任何协议*(content/http/https..)*的资源，从target API16开始默认false，由于安全策略，无特殊需求建议保持默认false

```java
settings.setAllowUniversalAccessFromFileURLs(boolean flag);
```

***ps:*** 仅影响JavaScript，不影响HTML。（同源策略跨域访问）

- 控制通过 *file://* 协议加载的 JS 能否访问 *file://* 协议的其他资源，从target API16开始默认false，由于安全策略，无特殊需求建议保持默认false

```java
settings.setAllowFileAccessFromFileURLs(boolean flag);
```

***ps:*** 仅影响JavaScript访问 *file://* 协议资源，不影响HTML，且在 `settings.getAllowUniversalAccessFromFileURLs()` 返回 true 时会被忽略。



### 安全浏览

安全浏览使WebView能够通过验证链接来防止恶意软件和网络钓鱼攻击。建议优先尝试安全浏览加载，启用失败再降级加载，或参照第 *4.* 步处理。

1. 尝试在支持安全浏览的设备上启用安全浏览功能

```java
if (WebViewFeature.isFeatureSupported(WebViewFeature.SAFE_BROWSING_ENABLE)) {
    WebSettingsCompat.setSafeBrowsingEnabled(settings, true);
}
```

***ps:*** 另外，在manifest文件中也可停用所有WebView对象的安全浏览功能，但优先级低于代码设置。（==只能控制停用？是否需要在代码中判断？还是只要声明了就行？==）

```xml
<manifest>
    <application>
    <meta-data
        android:name="android.webkit.WebView.EnableSafeBrowsing"
        android:value="false" />
    ...
    </application>
</manifest>
```

2. 判断设备是否支持安全浏览功能，以及开发者是否通过第 *1.* 步启用了该功能

```java
@SuppressLint("RequiresFeature")
public boolean canSafeBrowsing(@NonNull WebView webView) {
    boolean safeBrowsingSupported =
            WebViewFeature.isFeatureSupported(WebViewFeature.SAFE_BROWSING_ENABLE)
            && WebViewFeature.isFeatureSupported(WebViewFeature.START_SAFE_BROWSING);
    return safeBrowsingSupported && WebSettingsCompat.getSafeBrowsingEnabled(webView.getSettings());
}
```

3. 优先尝试以安全浏览模式加载

```java
//若不进行判断，则不管标记位是否enabled，都会尝试发起安全浏览(设备支持的情况下)
if (canSafeBrowsing(webView)) {
    WebViewCompat.startSafeBrowsing(getApplicationContext(),
            success -> {
                webView.loadXxx..
                if (!success) Log.e(TAG, "Unable to initialize Safe Browsing!");
            });
} else {
    webView.loadXxx..
    Log.e(TAG, "NOT be protected by Safe Browsing!");
}
```

4. 通过 `WebViewClient#onSafeBrowsingHit` 处理恶意网址

```java
@Override
public void onSafeBrowsingHit(WebView view, WebResourceRequest request, int threatType, SafeBrowsingResponse callback) {
    //处理方式1：显示插页提示让用户决定
    super.onSafeBrowsingHit(view, request, threatType, callback);//callback.showInterstitial(boolean report);
    //处理方式2：回退到之前页面
    callback.backToSafety(boolean report);
    //处理方式3：忽略警告，继续浏览
    callback.proceed(boolean report);
}
```

***ps:*** 只有当安全浏览功能初始化成功后，才会在浏览恶意网址时回调此方法。



### 停止将匿名诊断数据上传至Google

```xml
<manifest>
    <application>
    <meta-data
        android:name="android.webkit.WebView.MetricsOptOut"
        android:value="true" />
    ...
    </application>
</manifest>
```



### 支持多窗口

可选，默认false。若要支持多窗口，需实现 `{@link WebChromeClient#onCreateWindow}`。

```java
settings.setSupportMultipleWindows(boolean support);
```

```java
@Override
public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
    if (isUserGesture) {
        WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
        transport.setWebView(new WebView(view.getContext()));
        resultMsg.sendToTarget();
        return true;
    }
    return super.onCreateWindow(view, isDialog, isUserGesture, resultMsg);
}
```



### Web Geolocation

默认true，建议无定位需求时禁用。

```java
settings.setGeolocationEnabled(boolean flag);
```

- 启用 JavaScript Geolocation API 需要以下条件：
  - APP需要申请定位权限
  - 需要实现 `WebChromeClient#onGeolocationPermissionsShowPrompt` 和 `WebChromeClient#onGeolocationPermissionsHidePrompt` 处理来自JS的定位请求

***ps:*** 从API24开始，仅支持安全源*(https)*的定位请求，非安全源将自动拒绝并且不会回调以上位置请求相关方法。



### 媒体播放手势控制

可选，默认true，建议保持默认。

```java
settings.setMediaPlaybackRequiresUserGesture(boolean require);
```



### 离屏动画渲染行为

可选，API23新增，默认false。高级选项，无特殊需求建议保持默认。[Android WebView Off-screen Rendering|Stack Overflow](https://stackoverflow.com/questions/29797590/android-webview-offscreen-rendering)

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    settings.setOffscreenPreRaster(boolean enabled);
}
```



### postVisualStateCallback

>Posts a {@link VisualStateCallback}, which will be called when the current state of the WebView is ready to be drawn.
>
>DOM状态更新是异步的，WebView在准备好绘制DOM时会post一个通知回调。

- 此API涵盖的DOM状态包括：

  - primitive HTML elements (div, img, span, etc..)
  - images
  - CSS animations
  - WebGL
  - canvas

  不包括：

  - the video tag

- 为了保证WebView在首次 `VisualStateCallback#onComplete` 回调后能够成功渲染，需要满足以下条件：

  - 若WebView的可见性为 *VISIBLE*，则它必须已附加到View视图层次结构中；
  - 若WebView的可见性为 *INVISIBLE*，则它必须已附加到View视图层次结构中，且必须在 `VisualStateCallback#onComplete` 中使其可见；
  - 若WebView的可见性为 *GONE*，则它必须已附加到View视图层次结构中，{@link AbsoluteLayout.LayoutParams LayoutParams}的宽高需要设置为固定值（AbsoluteLayout.LayoutParams？*MATCH_PARENT* 是固定值吗？），且必须在 `VisualStateCallback#onComplete` 中使其可见。

  ***ps:*** *WebView extends AbsoluteLayout for backward compatibility reasons.*

- 经测试，需要在 `loadXxx` 方法之后post才有效：

```java
if (WebViewFeature.isFeatureSupported(WebViewFeature.VISUAL_STATE_CALLBACK)) {
    WebViewCompat.postVisualStateCallback(web, 1000L,
            new WebViewCompat.VisualStateCallback() {
                @Override
                public void onComplete(long requestId) {
                    if (webView.getVisibility() != View.VISIBLE) {
                        webView.setVisibility(View.VISIBLE);
                    }
                }
            });
}
```

***ps:*** 使用此API时，若WebView *off screen*，建议启用 [pre-rasterization](#离屏动画渲染行为) 以避免闪烁。



### ~~保存密码~~

:warning: 密码明文存储漏洞

已废弃API，默认true，强烈建议false（开启此配置会导致密码明文存储于 */data/data/com.package.name/databases/webview.db*）。

```java
settings.setSavePassword(boolean save);
```



### ~~保存表单数据~~

可选，已废弃API，默认true，建议保持默认。从API26开始被 *autofill* 功能替代。

```java
settings.setSaveFormData(boolean save);
```





## 进阶功能实现

### JavaScript交互

请确保已经启用JavaScript交互功能 `settings.setJavaScriptEnabled(true);`

#### Android调用JS

- ~~方式一：API19之前，`webView.loadUrl("javascript:callJS(\"参数\")");`（不易获取返回值，==且会引起页面刷新？==）~~
- 方式二：API19开始，`webView.evaluateJavascript(String script, @Nullable ValueCallback<String> resultCallback);`（异步执行）

**注意事项：**

- 所调用的function要存在于页面JavaScript代码中，注意正确传递方法签名列表，参数要使用引号包裹（单/双引号均可，双引号要进行转义）；
- 只能在UI线程调用，并且 *resultCallback* 也会在UI线程回调；
- JavaScript代码必须等页面加载完成后才能有效调用（在 `WebViewClient#onPageFinished` 中监听页面加载结束事件）。



**示例**

------

- 调用JS `function alert(message?:any): void`（可在 `WebChromeClient#onJsAlert` 中对alert dialog进行拦截处理）

```java
webView.evaluateJavascript("javascript:callJSAlert(\"JS Alert\")", null);
```

- 调用JS `function confirm(message?:string): boolean`（可在 `WebChromeClient#onJsConfirm` 中对confirm dialog进行拦截处理）

```java
webView.evaluateJavascript("javascript:callJSConfirm(\"JS Confirm\")",
        new ValueCallback<String>() {
            @Override
            public void onReceiveValue(String value) {
                Log.d(TAG, "ConfirmResult: " + value);
            }
        });
```

- 调用JS `function prompt(message?:string, _default:string): string`（可在 `WebChromeClient#onJsPrompt` 中对prompt dialog进行拦截处理）

```java
webView.evaluateJavascript("javascript:callJSPrompt(\"JS Prompt\")",
        new ValueCallback<String>() {
            @Override
            public void onReceiveValue(String value) {
                Log.d(TAG, "PromptMessage: " + value);
            }
        });
```

- 调用JS `(method) open(url?:string, target?:string, features?:string, replace?:boolean): Window` 开启新网页（请确保已允许JS执行 `window.open()` 函数：`settings.setJavaScriptCanOpenWindowsAutomatically(true);`）



示例中所用的JavaScript代码（无需立即载入的JS声明于body尾部加速页面加载）

```javascript
<body>
    <!-- ... -->
    <script type="text/javascript" title="Android调用JS的方法">
        function callJSAlert(message) {
            alert(message);
        }
        function callJSConfirm(message) {
            return confirm(message);
        }
        function callJSPrompt(message) {
            return prompt(message, 'default');
        }
        function openWindow() {
            //replace?: Optional.
            //Specifies whether the URL creates a new entry
            //or replaces the current entry in the history list.
            return window
                .open(document.getElementById('labelForText').value,
                    'Window Name', 'resizable,scrollbars,status', true)
                .name;
        }
    </script>
</body>
```



#### JS调用Android

- 方式一：调用 `WebView#addJavascriptInterface` 方法向JS注入Java对象（:warning: 在API17之前存在任意代码执行漏洞）

```java
/**
 * @param object 注入JS的Java对象
 * @param name Java对象在JS中暴露的句柄名称，用于调用Java对象的方法(需声明 @JavascriptInterface 注解)
 */
webView.addJavascriptInterface(Object object, String name);
```

如：

```java
private void injectJsBridgeInterface() {
    webView.addJavascriptInterface(new WebAppInterface(this), "Android");
}
```

```java
public final class WebAppInterface {
    private Context ctx;

    public WebAppInterface(@NonNull Context context) {
        ctx = context;
    }

    //无参有返回值
    @JavascriptInterface
    public String android() {
        return "injected.";
    }

    //有参无返回值
    @JavascriptInterface
    public void showToast(String toast) {
        Toast.makeText(ctx,
                TextUtils.isEmpty(toast) ? "null" : toast,
                Toast.LENGTH_LONG)
                .show();
    }

    //有参有返回值
    @JavascriptInterface
    public String returnValue(String string) {
        return TextUtils.isEmpty(string) ? "null" : string;
    }
}
```

***ps:*** 从API17开始，注入JS的Java对象只能调用声明了 `@JavascriptInterface` 注解的方法。~~因此仅注解一个用于获取类对象实例的方法，拿到对象实例后再去调用未注解的类实例方法是不行的~~。

```javascript
<body>
    <!-- ... -->
    <script type="text/javascript" title="注入JS调用Android的方法">
        function showAndroidToast() {
            Android.showToast("JS::Test");
        }
        function returnValue() {
            var value = Android.android();
            var value1 = Android.returnValue(message);
            //JS ES6'模板字符串'新语法，使用反引号 ` 代替单/双引号，使用占位符 ${expression} 嵌入表达式或变量（支持多(换)行）。
            //若模板字符串包含反引号 `，需要使用反斜杠 \ 进行转义。
            console.log(`returned value: ${value} ${value1}`);
        }
    </script>
</body>
```

- 方式二：拦截 `WebViewClient#shouldOverrideUrlLoading` 回调，处理自约定格式的URI（更安全，但不易获取返回值，需要额外调用一个专门用于回传返回值的JS方法）

  WebView加载的所有链接都会经过此回调，因此我们和前端约定一种自定义URL用于携带信息，并在此拦截处理，实现JS与Android端通信。

  假如已约定URL为：*js://webview?arg0=a&arg1=b&arg2=c*

```java
@Override
public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
    Uri uri = request.getUrl();
    if ("js".equals(uri.getScheme())) {
        if ("webview".equals(uri.getAuthority())) {
            String arg0 = uri.getQueryParameter("arg0");
            String arg1 = uri.getQueryParameter("arg1");
            String arg2 = uri.getQueryParameter("arg2");
            //调用Android端方法
            String result = android(arg0, arg1, arg2);
            //额外的专用回传返回值的JS方法
            view.evaluateJavascript("javascript:returnResult(\"result\")", null);
        }
        return true;
    }
    return false;
}
```

- 方式三：拦截 `WebChromeClient#onJsAlert/onJsConfirm/onJsPrompt` 回调，处理自约定格式的URI（更安全，其中*onJsAlert*不能返回值，*onJsConfirm*只能返回*boolean*值，*onJsPrompt*可返回任意String值，请根据情况决定具体拦截方法）

  此方式和方式二原理相似，仍然需要和前端约定一种自定义URL用于携带信息，并在此处拦截处理特定dialog事件，实现JS与Android端通信。

  假如已约定URL仍然为：*js://webview?arg0=a&arg1=b&arg2=c*

```java
//此处仅展示拦截onJsPrompt的处理，onJsAlert和onJsConfirm类似
@Override
public boolean
onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
    Uri uri = Uri.parse(message);
    if ("js".equals(uri.getScheme())) {
        if ("webview".equals(uri.getAuthority())) {
            String arg0 = uri.getQueryParameter("arg0");
            String arg1 = uri.getQueryParameter("arg1");
            String arg2 = uri.getQueryParameter("arg2");
            //调用Android端方法
            String result = android(defaultValue, arg0, arg1, arg2);
            //回传返回值
            result.confirm(result);
        }
        return true;
    }
    return false;
}
```

方式二/三的JS端处理：

```javascript
<body>
    <!-- ... -->
    <script type="text/javascript" title="非注入JS调用Android的方法(比JS注入更安全)">
        const agreedUri = 'js://webview?arg0=a&arg1=b&arg2=c';
        //返回值获取麻烦，需要通过调用JS方法回传
        function nonInjectedJS() {
            //约定的url协议为，数组可通过重复参数实现arg0=a1&arg0=a2
            document.location = agreedUri;
        }
        //能满足大多数的互调需求
        function callAndroidByPrompt() {
            var promptResult = prompt(agreedUri, '_default');
            console.log(promptResult);
        }
    </script>
</body>
```



#### 探究另一种JS交互方式：Web Message Channel

1. 创建Web Message Channel，给其中一个端口设置回调等待接收Web端回传数据

```java
if (WebViewFeature.isFeatureSupported(WebViewFeature.CREATE_WEB_MESSAGE_CHANNEL)) {
    //创建Web Message Channel
    WebMessagePortCompat[] channelPort = WebViewCompat.createWebMessageChannel(webView);
    if (WebViewFeature.isFeatureSupported(WebViewFeature.WEB_MESSAGE_PORT_SET_MESSAGE_CALLBACK)) {
        //设置端口回调
        channelPort[0].setWebMessageCallback(
                new WebMessagePortCompat.WebMessageCallbackCompat() {
                    @Override
                    public void onMessage(@NonNull WebMessagePortCompat port,
                                          @Nullable WebMessageCompat message) {
                        super.onMessage(port, message);
                        if (message != null && message.getData() != null) {
                            Log.d(TAG, message.getData());
                        }
                    }
                });
    }
}
```

2. 从Android端向Web端发送消息，并同时携带另一个端口（用于Web端向Android端发送消息）

```java
if (WebViewFeature.isFeatureSupported(WebViewFeature.POST_WEB_MESSAGE)) {
    // Post a message to main frame.
    WebViewCompat.postWebMessage(webView,
            new WebMessageCompat("web message::ping from android",
                    new WebMessagePortCompat[]{channelPort[1]}),//channelPort[0] port is already started
            Uri.parse(webView.getUrl()));
}
```

***ps:*** 向一个 *file://* 协议网页发送web message，*targetOrigin* 参数必须为通配符 "*****"（经测试 *Uri.EMPTY* 也行）。这样将会非常不安全，并且此限制未来可能被修改。

3. Web端监听 *message* 事件，并作出响应

```javascript
<head>
    <!-- ... -->
    <script type="text/javascript">
        // web message channel
        window.addEventListener("message", (event) => {
            // if (event.origin !== "http://example.org:8080")
            //     return;
            console.log('[' + event.origin + '::' + event.data + '] <' + event.ports.length + '>');
            if (event.ports.length > 0) {
                //此处即为Android端发送消息时携带的端口
                event.ports[0].postMessage('web message callback::pong from js');
            }
        }, false);
    </script>
</head>
```

[How Do You Use WebMessagePort As An Alternative to addJavascriptInterface()? - Stack Overflow](https://stackoverflow.com/questions/41753104/how-do-you-use-webmessageport-as-an-alternative-to-addjavascriptinterface)

[Window.postMessage() | MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)

[HTML Standard | whatwg.org](https://html.spec.whatwg.org/multipage/web-messaging.html#posting-messages)



### 页面变动保留提示

- 若Web端实现了 ***beforeunload*** 事件监听，APP端需要实现 `onJsBeforeUnload` 配合

```java
@Override
public boolean onJsBeforeUnload(WebView view, String url, String message, JsResult result) {
    //JS默认弹窗提示处理
    return false;

    //result.cancel();//留在本页(保留页面数据状态)
    //result.confirm();//离开或重载(丢弃页面数据状态)
    // confirm result and return true to temporary ignore 'beforeunload' event inside window
    //return true;//自行处理
}
```

- Web端 *beforeunload* 事件监听

```javascript
<head>
    <!-- ... -->
    <script type="text/javascript">
        window.addEventListener('beforeunload', (event) => {
            // Cancel the event as stated by the standard.
            event.preventDefault();
            // Chrome requires returnValue to be set.
            event.returnValue = '';
        });
    </script>
</head>
```



### 网页加载进度

- 通过 `WebChromeClient#onProgressChanged` 回调加载进度

```java
//此处省略了 liOnProgressChanged 回调接口的创建与注册，请自行补充
@Override
public void onProgressChanged(WebView view, int newProgress) {
    super.onProgressChanged(view, newProgress);
    // newProgress在加载过程中，进度可能落后于webView.getProgress()，这里可以作一些额外处理（可选）。至于进度不同步的原因待进一步研究。
    if (liOnProgressChanged != null && newProgress == view.getProgress()) {
        liOnProgressChanged.onChanged(view, newProgress);
    }
}
```

- 配合 `WebViewClient#onPageStarted` 和 `WebViewClient#onPageFinished` 以及 `WebViewClient#onReceivedError` 处理加载进度标识

```java
//此处同样省略了 liOnPageStateChanged 回调接口的创建与注册，请自行补充
@Override
public void onPageStarted(WebView view, String url, Bitmap favicon) {
    super.onPageStarted(view, url, favicon);
    if (liOnPageStateChanged != null) {
        liOnPageStateChanged.onPageStarted(view, url, favicon);
    }
}

@Override
public void onPageFinished(WebView view, String url) {
    super.onPageFinished(view, url);
    //由于重定向onPageFinished可能回调多次，判断进度100%时才是最终加载完成
    if (liOnPageStateChanged != null && view.getProgress() == 100) {
        liOnPageStateChanged.onPageFinished(view, url);
    }
}

@Override
public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {
    //只在当前页面内容加载发生错误时回调(不包括图片加载、CSS等错误，API23之前)
    if (liOnPageStateChanged != null) {
        liOnPageStateChanged.onPageReceivedError(view, errorCode, description, failingUrl);
    }
}

@Override
public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error) {
    super.onReceivedError(view, request, error);
    //在页面加载发成任何错误时回调, 请注意super方法的实现（API23新增，注意版本兼容处理）
    if (!request.isForMainFrame()) {
        if (liOnPageStateChanged != null) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                liOnPageStateChanged.onPageReceivedError(view,
                        error.getErrorCode(), error.getDescription().toString(),
                        request.getUrl().toString());
            } else {
                liOnPageStateChanged.onPageReceivedError(view,
                        0, "unknown",
                        request.getUrl().toString());
            }
        }
    }
}
```

**ps:** 重定向会多次回调 `onPageStarted` 和 `onPageFinished`；勿忘记加载错误时的处理。

- 最后就可以通过WebViewClient对象注册页面状态加载回调，以及通过WebChromeClient对象注册页面加载进度回调

```java
webClient.setOnPageStateChanged(..);
chromeClient.setOnProgressChanged(..);
```



### URL拦截与重定向

关键方法：`WebViewClient#shouldOverrideUrlLoading`

```java
@Override
public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
    Uri uri = request.getUrl();
    if (WebViewFeature.isFeatureSupported(WebViewFeature.SHOULD_OVERRIDE_WITH_REDIRECTS)
            && "hit_host".equals(uri.getHost())) {
        // This is my website, so do not override; let my WebView load the page. 自己加载
        return false;
    } else if ("tel".equals(uri.getScheme())) {
        //重定向至电话
        view.getContext().startActivity(new Intent(Intent.ACTION_DIAL, uri));
        return true;
    } else {
        // Otherwise, the link is not for a page on my site, so launch another Activity that handles URLs. 重定向至浏览器
        Intent intent = new Intent(Intent.ACTION_VIEW, uri);
        view.getContext().startActivity(intent);
        return true;
    }
}
```



### 静态资源拦截与替换

关键方法：`WebViewClient#shouldInterceptRequest`

```java
@Nullable
@Override
public WebResourceResponse
shouldInterceptRequest(WebView view, WebResourceRequest request) {
    Log.d(TAG, "ShouldInterceptRequest: " + request.getUrl());
    //匹配需要拦截的网络资源，并使用本地资源替换，提升加载速度，减少流量消耗
    if (request.getUrl().toString().endsWith("~300x300.image")) {
        String pendingReplaceFileName = "feather_bookmark.png";
        InputStream is = null;
        try {
            is = view.getContext().getApplicationContext().getAssets().open(pendingReplaceFileName);
        } catch (IOException e) {
            Log.e(TAG, "Failed to intercept request: " + request.getUrl(), e);
        }
        return new WebResourceResponse(
            MimeTypeMap.getSingleton().getMimeTypeFromExtension(pendingReplaceFileName),
            "utf-8", is);
    }
    return super.shouldInterceptRequest(view, request);
}
```

**ps:** 该方法回调于子线程。



### 访问文件系统

关键方法：`WebChromeClient#onShowFileChooser`

1. 通过自定义接口将文件访问事件回调出去

```java
//此处省略了 liOnShowFileChooser 回调接口的创建与注册，请自行补充
@Override
public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, FileChooserParams fileChooserParams) {
    return liOnShowFileChooser == null
            ? super.onShowFileChooser(webView, filePathCallback, fileChooserParams)
            : liOnShowFileChooser.onShowFileChooser(webView, filePathCallback, fileChooserParams);
}
```

2. 通过WebChromeClient对象注册回调，进行后续处理（导航至文件管理器或取消请求）

```java
webChromeClient.setOnShowFileChooser(new IOnShowFileChooser() {
    @Override
    public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback,
                      WebChromeClient.FileChooserParams fileChooserParams) {
        //保存filePathCallback句柄，用于在onActivityResult中进行文件选择结果回调
        filePathCallbackRef = filePathCallback;
        //导航至文件管理器
        Intent it = fileChooserParams.createIntent();
        if (it.resolveActivity(getPackageManager()) != null) {
            startActivityForResult(it, REQUEST_CODE_FILE_CHOOSER);
        }
        //或传递 null 以取消请求
        // filePathCallback.onReceiveValue(null);
        return true;// to avoid java.lang.IllegalStateException: Duplicate showFileChooser result
    }
});
```

***ps:*** 访问文件系统前先进行文件访问权限检查，在未授权时要先申请权限。

3. 解析并回调文件选择结果

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == RESULT_OK && requestCode == REQUEST_CODE_FILE_CHOOSER) {
        Uri[] uris = WebChromeClient.FileChooserParams.parseResult(resultCode, data);
        if (filePathCallbackRef != null) {
            filePathCallbackRef.onReceiveValue(uris);
        }
    }
}
```



### 权限请求

`WebChromeClient#onPermissionRequest`

`WebChromeClient#onPermissionRequestCanceled`



### Http验证请求处理

```java
@Override
public void onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host, String realm) {
    String username = null;
    String passphrase = null;
    WebViewDatabase db = WebViewDatabase.getInstance(view.getContext());
    //本地已存储了当前加载host/realm的验证信息
    if (db.hasHttpAuthUsernamePassword() && handler.useHttpAuthUsernamePassword()) {
        String[] basicHttpAuthUsrPwd;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            basicHttpAuthUsrPwd = db.getHttpAuthUsernamePassword(host, realm);
        } else {
            basicHttpAuthUsrPwd = view.getHttpAuthUsernamePassword(host, realm);
        }
        if (basicHttpAuthUsrPwd != null) {
            username = basicHttpAuthUsrPwd[0];
            passphrase = basicHttpAuthUsrPwd[1];
        }
    }

    if (username == null || passphrase == null) {//未成功取回验证信息
        // retrieves username/passphrase in any other way and stores them for next auth request
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            db.setHttpAuthUsernamePassword(host, realm,
                    "username", "passphrase");
        } else {
            view.setHttpAuthUsernamePassword(host, realm,
                    "username", "passphrase");
        }
        super.onReceivedHttpAuthRequest(view, handler, host, realm);//handler.cancel();
    } else {
        handler.proceed(username, passphrase);
    }
    //清除所有已存储验证信息
    //db.clearHttpAuthUsernamePassword();
}
```

***ps:*** 验证方式，Basic or Bearer？



### SSL/TLS证书验证请求处理

实现参考：

[AOSP Browser](https://android.googlesource.com/platform/packages/apps/Browser/+/android-5.1.1_r1/src/com/android/browser/Tab.java)

[refs/heads/master - platform/packages/apps/Browser - Git at Google (googlesource.com)](https://android.googlesource.com/platform/packages/apps/Browser/+/refs/heads/master)

```java
@Override
public void onReceivedClientCertRequest(WebView view, ClientCertRequest request) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        KeyChain.choosePrivateKeyAlias((Activity) view.getContext(),
                new KeyChainAliasCallback() {
                    @Override
                    public void alias(@Nullable String alias) {
                        if (alias == null) {
                            request.cancel();
                            return;
                        }
                        // create an async task to call KeyChain#getPrivateKey to receive the key
                        new KeyChainLookup(view.getContext(), request, alias).execute();
                    }
                }, request.getKeyTypes(), request.getPrincipals(), request.getHost(),
                request.getPort(), null);
    }
}
```

```java
private final class KeyChainLookup extends AsyncTask<Void, Void, Void> {
    private final Context mContext;
    private final ClientCertRequest mHandler;
    private final String mAlias;
    KeyChainLookup(Context context, ClientCertRequest handler, String alias) {
        mContext = context.getApplicationContext();
        mHandler = handler;
        mAlias = alias;
    }

    @Override
    protected Void doInBackground(Void... params) {
        PrivateKey privateKey;
        X509Certificate[] certificateChain;
        try {
            privateKey = KeyChain.getPrivateKey(mContext, mAlias);
            certificateChain = KeyChain.getCertificateChain(mContext, mAlias);
        } catch (InterruptedException | KeyChainException e) {
            mHandler.ignore();
            return null;
        }
        mHandler.proceed(privateKey, certificateChain);
        return null;
    }
}
```



### 自动登录请求处理

`WebViewClient#onReceivedLoginRequest`



### 网页错误处理

`WebViewClient#onReceivedError`

- [网页加载错误时对加载进度标识的处理](#网页加载进度)



`WebViewClient#onReceivedHttpError`

- 回调 *状态码 >=400* 的HTTP错误



`WebViewClient#onReceivedSslError`（不推荐向用户展示此类错误，建议保持默认，即取消当前及后续所有发生此类错误的请求）

- 可恢复的SSL/TLS连接错误，无法恢复的连接错误将回调 `WebViewClient#onReceivedError`（*ERROR_FAILED_SSL_HANDSHAKE*）

```java
@Override
public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
    //取消请求
    super.onReceivedSslError(view, handler, error);//handler.cancel();
    //或 等待证书响应, 接收所有网站证书, 避免https加载失败
    //handler.proceed();
}
```



### 历史访问记录

前置条件，WebView设置了以下2个client：

`webView.setWebViewClient();`

`webView.setWebChromeClient();`

- WebView加载URL会回调 `WebViewClient#doUpdateVisitedHistory` 通知应用程序更新历史访问记录

```java
@Override
public void doUpdateVisitedHistory(WebView view, String url, boolean isReload) {
    super.doUpdateVisitedHistory(view, url, isReload);
    if (!isReload) {
        // update visited history
    }
}
```

- 覆写 `WebChromeClient#getVisitedHistory` 回调已保存的历史访问记录

```java
@Override
public void getVisitedHistory(ValueCallback<String[]> callback) {
    super.getVisitedHistory(callback);
    if (callback == null) return;
    // get visited history..
    callback.onReceiveValue(visitedHistory..);
}
```

- 通过WebChromeClient对象获取历史访问记录

```java
webView.getWebChromeClient().getVisitedHistory(new ValueCallback<String[]>() {
    @Override
    public void onReceiveValue(String[] value) {
        Log.d(TAG, Arrays.toString(value));
    }
});
```



### KeyEvent拦截处理

`WebViewClient#shouldOverrideKeyEvent`

`WebViewClient#onUnhandledKeyEvent`



### 全屏支持

for video or other HTML content. *ps:* video tag 必须开启硬件加速。

```java
private View mCustomView;
private int mOriginalOrientation;
private FullscreenContainer mFullscreenContainer;
private static final FrameLayout.LayoutParams COVER_SCREEN_PARAMS
        = new FrameLayout.LayoutParams(
        ViewGroup.LayoutParams.MATCH_PARENT,
        ViewGroup.LayoutParams.MATCH_PARENT);

@SuppressLint("SourceLockedOrientationActivity")
@Override
public void onShowCustomView(View view, CustomViewCallback callback) {
    super.onShowCustomView(view, callback);
    // if a view already exists then immediately terminate the new one
    if (mCustomView != null) return;
    mOriginalOrientation = mActivity.getRequestedOrientation();
    FrameLayout decor = (FrameLayout) mActivity.getWindow().getDecorView();
    mFullscreenContainer = new FullscreenContainer(mActivity);
    mFullscreenContainer.addView(view, COVER_SCREEN_PARAMS);
    decor.addView(mFullscreenContainer, COVER_SCREEN_PARAMS);
    mCustomView = view;
    setFullscreen(mActivity, true);
    mWebView.setVisibility(View.INVISIBLE);
    mActivity.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
}

@Override
public void onHideCustomView() {
    super.onHideCustomView();
    mWebView.setVisibility(View.VISIBLE);
    if (mCustomView == null) return;
    setFullscreen(mActivity, false);
    FrameLayout decor = (FrameLayout) mActivity.getWindow().getDecorView();
    decor.removeView(mFullscreenContainer);
    mFullscreenContainer = null;
    mCustomView = null;
    // Show the content view.
    mActivity.setRequestedOrientation(mOriginalOrientation);
}

private void setFullscreen(@NonNull Activity activity, boolean enabled) {
    Window win = activity.getWindow();
    WindowManager.LayoutParams winParams = win.getAttributes();
    int bits = WindowManager.LayoutParams.FLAG_FULLSCREEN;
    if (enabled) {
        winParams.flags |= bits;
    } else {
        winParams.flags &= ~bits;
        if (mCustomView != null) {
            mCustomView.setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
        }
    }
    win.setAttributes(winParams);
}

static class FullscreenContainer extends FrameLayout {
    public FullscreenContainer(Context ctx) {
        super(ctx);
        setBackgroundColor(Color.BLACK);
    }
    @SuppressLint("ClickableViewAccessibility")
    @Override
    public boolean onTouchEvent(MotionEvent evt) {
        return true;
    }
}
```

***ps:*** 实现 `WebChromeClient#getVideoLoadingProgressView` 设置自定义loading view。



### WebViewRenderProcessClient

`WebView#setWebViewRenderProcessClient`

`WebView#getWebViewRenderProcessClient`

`WebViewRenderProcessClient`

`WebViewClient#onRenderProcessGone`





## 优化/Workaround

 在以上功能介绍时以简述部分优化技巧，在此仅作补充、列举或展开详述。以下优化仅作建议用，具体请结合开发需求实施。

### 生命周期资源合理控制

[生命周期资源合理控制](#生命周期资源控制)



### 提高渲染进程优先级

[提高渲染进程优先级](#分配渲染器进程优先级策略)



### 启用硬件加速后加载过程出现闪烁

关闭硬件加速：`webView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);`



### 网页加载不出/不全

- [开启 `settings.setDomStorageEnabled(true);`](#缓存配置)
- 声明WebView宽高均为 *MATCH_PARENT*（xml 或 `addView` 动态添加）



### onJsAlert/Confirm/Prompt/BeforeUnload不弹窗

实例化WebView对象关联的Context必须为Activity Context，即要么在xml中声明WebView，要么 `new WebView(activity)` 时 *context* 入参必须为activity实例（注意可能的内存泄漏问题，因为关联了activity引用）。



### 内存泄漏

- 不要在xml中声明WebView，动态实例化 `new WebView(getApplicationContext());`（目前已知的问题：Dialog异常（即上一个问题）、使用第三方应用打开网页链接异常、WebView中使用Flash异常）。

- 仍以activity实例作为 *context* 入参，但让WebView Activity运行于独立进程，如 `:browser`（可防止OOM影响主进程，但可能存在进程间通信问题）。

  *java.lang.RuntimeException: Using WebView from more than one process at once with the same data directory is not supported. https://crbug.com/558377*

- `new WebView(new WeakReference<Context>(activity).get());`（:smirk: 人才？）



### :warning:任意代码执行漏洞

- [禁用JavaScript后台执行](#JS功能支持)
- [JS调用Android方式优化](#JS调用Android)
- 移除谷歌内置导出的JS注入对象

```java
webView.removeJavascriptInterface("searchBoxJavaBridge_");
webView.removeJavascriptInterface("accessibility");
webView.removeJavascriptInterface("accessibilityTraversal");
```



### :warning:密码明文存储漏洞

[显式禁用密码保存功能](#保存密码)



### :warning:域控制不严格漏洞

- [根据开发需求合理控制文件访问API开关](#文件访问控制)

- 禁用外部 *file* 协议链接的 JS交互功能（建议放在 `onPageStarted` 中，如下）

```java
@Override
public void onPageStarted(WebView view, String url, Bitmap favicon) {
    super.onPageStarted(view, url, favicon);
    ...
    //禁用外部 file协议链接的 JS交互功能, 阻止域控制不严格漏洞
    view.getSettings().setJavaScriptEnabled(!URLUtil.isFileUrl(url));
}
```



### 使用Chromium浏览器调试WebView内容

可选，默认false。可调试当前应用任何WebView加载的HTML/CSS/JavaScript。

1. 开启调试配置

```java
static {
    WebView.setWebContentsDebuggingEnabled(BuildConfig.DEBUG);
}
```

2. 打开浏览器，进入 `chrome://inspect/` 或 `edge://inspect/`，Devices选项卡中会列出当前打开的所有WebView对象，点击 **inspect** 进入具体调试页面（同浏览器F12页面）。

***ps:*** `WebChromeClient#onConsoleMessage` 可以打印web console log。



### enableSlowWholeDocumentDraw

>对于 targeting the L release 的应用，WebView具有新的默认行为，可通过智能地选择HTML文档中需要绘制的部分来减少内存占用并提高性能。这些优化对开发人员是透明的。

但是，在某些情况下开发者可能希望禁用此选项：

- 当应用使用WebView#onDraw进行绘制并访问页面的可见部分之外的部分页面时；
- 当应用使用WebView#capturePicture捕获非常大的HTML文档时（WebView#capturePicture现在是已弃用的API）。

```java
static {
    // For apps targeting the L release
    WebView.enableSlowWholeDocumentDraw();
}
```

***ps:*** 应在任何WebView对象创建之前配置。





## 相关链接

[基于网络的内容|Android 开发者|Android Developers](https://developer.android.google.cn/guide/webapps)

[WebView|Android SDK|Android Developers](file:///F:/ASDKits/docs/reference/android/webkit/WebView.html)

[WebView相关源码|Git at Google (googlesource.com) :airplane:](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/java/android/webkit/)

[AOSP Browser|Git at Google (googlesource.com) :airplane:](https://android.googlesource.com/platform/packages/apps/Browser/+/refs/heads/master/src/com/android/browser)

[Webview混合开发|Carson_Ho](https://www.jianshu.com/nb/23644548)

[Android WebView 详解|reezy](https://www.jianshu.com/p/a6f7b391a0b8)

[ncmon博客 Tag : WebView|ncmon.com](http://ncmon.com/tags/WebView)