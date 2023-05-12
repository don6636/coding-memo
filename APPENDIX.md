[TOC]

# KEEP ANDROID



## QUICK NOTES

- **cmd -> nexus.exe /run** 启动Nexus私服（初始用户名和密码分别为 `admin` 和 `admin123`）

- **win键 -> env** 编辑环境变量

- **Ctrl + Shift + Esc** 打开任务管理器

- 开/关服务：`net start/stop <SERVICE-NAME>`

- 服务状态：`sc query <SERVICE-NAME>`

- 查看TCP/IP活动连接：`netstat -an`

- 测试端口是否开放：`telnet <host> <port>`（黑屏时按 *Ctrl + ]*，进入后 *q* 退出）

- 关闭Windows系统 *Ctrl + Space* 快捷键（Win10上可通过 **PowerToys** 工具重新映射）:

  1. 定位至注册表 *HKEY_CURRENT_USER/Control Panel/Input Method/Hot Keys* 目录下；
  2. 选择 *00000070* (繁体) 或者 *00000010* (简体)；
  3. 将 *Key Modifiers* 的第一个字节设置为 **00** (*02c00000 -> 00c00000*)；
  4. 将 *Virtual Key* 的第一个字节设置为 **ff** (*20000000 -> ff000000*)； 
  5. 注销并重新登录或重启。

  ***ps:*** *HKEY_CURRENT_USER/Control Panel/Input Method/Hot Keys* 保存了当前用户的快捷键配置；

  　  *HKEY_USERS.DEFAULT/Control Panel/Input Method/Hot Keys* 保存了系统默认的快捷键配置。

- VS Code调试nodejs代码，不用设置 `launch.json` 文件：
  1. 打开设置搜索`deubg.node`；
  2. 打开 `debug.node:Auto Attach`；
  3. `node --inspect` 启动项目，然后在代码里添加断点即可。
  
- Windows系统查看文件校验值（在文件所在目录打开cmd，执行下列所需命令）:
  - `certutil -hashfile filename.extension MD5`
  - `certutil -hashfile filename.extension SHA1`
  - `certutil -hashfile filename.extension SHA256`
  
- Windows删除卸载残留/本地服务：以管理员权限运行cmd，执行命令 `sc delete "服务名"` (若服务名包含空格，需要用双引号包裹，否则可缺省)。

- cmd清除屏幕信息 `cls`

- CLI `cd` 指令切换盘符：

  - 不用 `cd` 指令，直接输入盘符。如直接输入 `d:` 即可切换至D盘
  - 在 `cd` 指令和路径之间加入 `/d` 。如 `cd /d d:`

  列出当前路径下的文件信息：`dir` 指令（与Linux的 `ls` 类似）

- CLI查看签名keystore详情：`keytool -list -keystore <keystore_path>`



## ERROR ANALYSIS

**Eclipse**

------

- **R** 文件丢失 :arrow_heading_down:

  检查并处理xml中的错误，然后重新编译项目（Properties - Android - Project Build Target中更换API）

- 导入android-support-design库报错 :arrow_heading_down:

  design库需要依赖support-v7-appcompat库

- 用 ApplicationContext 启动 standard 模式的Activity报错 :arrow_heading_down:

  为待启动Activity指定 FLAG_ACTIVITY_NEW_TASK 标记位，这样启动时会建立新栈（此时待启动Activity实际上是以singleTask模式启动的）



**Android Studio**

------

- Manifest merger failed：minSdkVersion 不能比依赖库中的小 :arrow_heading_down:

  Suggestion: use a compatible library with a minSdk of at most 16,

  or increase this project's minSdk version to at least 19,

  or use `tools:overrideLibrary="com.foxconn.aladdin"` to force usage (may lead to runtime failures),

  and don't forget to include `xmlns:tools="http://schemas.android.com/tools"` too, before the tag.

- Program type already present: xxx :arrow_heading_down:

  Clean and Rebuild project, then Resolved.

- The application could not be installed: INSTALL_FAILED_NO_MATCHING_ABIS :arrow_heading_down:

  在相应module的 **build.gradle** 文件中的 **android** 闭包下添加以下内容（详细释义请查看<a href="#gradle-tips">**Gradle配置**</a>中的build.gradle定义解析）
  
  ```groovy
  splits {
      abi {
          enable true
          reset()
          include 'x86', 'armeabi-v7a','x86_64'
          universalApk true
      }
  }
  ```

- Android Studio无法正常调试 :arrow_heading_down:

  请检查debug模式下minifyEnabled是否为false，若不是改成false后重试。
  
- Android Studio签名文件格式报警 :arrow_heading_down:

  `keytool -importkeystore -srckeystore F:\keystore\debug.jks -destkeystore F:\keystore\debug.jks -deststoretype pkcs12`
  
- Android Studio运行Java main方法报错：`> Could not create task ':app:JavaMain.main()'.`

  `> SourceSet with name 'main' not found.` :arrow_heading_down:

  在项目根目录下的 *.idea* 文件夹中，找到 **gradle.xml** 文件，并在其 `<GradleProjectSettings>` 节点下添加以下语句：

  `<option name="delegatedBuild" value="false" />`，防止Gradle将main方法当作task来构建而导致错误。

- Git远程仓库新增了一个module，拉取更新后该module未显示在Android项目结构下 :arrow_heading_down:

  这是因为module缺失iml文件(被git忽略)，只需删除并重新依赖该module即可(建议使用Project Structure操作)。
  
- This version of Android Studio cannot open this project, please retry with Android Studio 4.0 or newer. :arrow_heading_down:

  检查Gradle Plugin版本和相应的Gradle版本是否与Android Studio版本匹配，并更改项目对应配置即可。

- Failed to transform artifact 'xxx.aar (project :xxx)' to match attributes {artifactType=jar}. :arrow_heading_down:

  对应module缺失 xxx.aar 文件造成的

- Could not find method coreLibraryDesugaringEnabled() for arguments [true] on object of type com.android.build.gradle.internal.CompileOptions. :arrow_heading_down:

  貌似因为Gradle版本过低？



## Android Studio

[历史版本下载](https://developer.android.google.cn/studio/archive)

[**Build** 菜单中的编译选项](https://developer.android.com/studio/run/index.html#reference)

| Menu Item                                | Description                                                  |
| ---------------------------------------- | ------------------------------------------------------------ |
| Make Module                              | 编译自上次编译以来已修改的所选模块中的所有源文件，以及所选模块以递归方式依赖的所有模块。编译包括依赖源文件和所有关联的编译任务。您可以通过在 **Project** 窗口中选择模块名称或其中一个文件来选择要编译的模块。此命令不会生成 APK。 |
| Make Project                             | 生成所有模块。                                               |
| Clean Project                            | 删除所有中间/缓存的编译文件。                                |
| Rebuild Project                          | 针对所选编译变体运行 **Clean Project** 并生成 APK。          |
| Build Bundle(s)/APK(s) > Build APK(s)    | 为所选的变体编译当前项目中所有模块的 APK。 若选择的build variant是debug类型，则使用调试密钥为APK签名，然后就可以安装了。若选择了release variant，则APK默认处于未签名状态，必须手动[为 APK 签名](https://developer.android.com/studio/publish/app-signing.html)。或者从菜单栏中依次选择 **Build > Generate Signed Bundle/APK**。 Android Studio会将编译的APK保存在 `project-name/module-name/build/outputs/apk/` 中。 |
| Build Bundle(s)/APK(s) > Build Bundle(s) | 为所选的variant编译当前项目中所有模块的 [Android App Bundle](https://developer.android.com/guide/app-bundle/)。 若选择的build variant是debug类型，则使用调试密钥为app bundle签名，然后就可以[使用 `bundletool`](https://developer.android.com/studio/command-line/bundletool)通过app bundle将应用部署到连接的设备。若选择了release variant，则app bundle默认处于未签名状态，必须使用 [`jarsigner`](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html) 手动签名。或者从菜单栏中依次选择 **Build > Generate Signed Bundle/APK**。 Android Studio会将编译的APK保存在 `project-name/module-name/build/outputs/bundle/` 中。 |
| Generate Signed Bundle/APK               | 使用向导打开一个对话框以设置新的签名配置，并编译已签名的 app bundle 或 APK。您需要先使用发布密钥为应用签名，然后才能将其上传到 Play 管理中心。如需详细了解如何为您的应用签名，请参阅[为应用签名](https://developer.android.com/studio/publish/app-signing.html)。 |

***ps:***  **Run** 按钮使用 `testOnly="true"` 编译 APK，这意味着 APK 只能通过 `adb`（Android Studio 使用的 adb）安装。如果您想要无需 adb 即可安装的可调试 APK，请选择您的调试变体，然后依次点击 **Build Bundle(s)/APK(s) > Build APK(s)**。 



### Gradle

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/dependency-management-resolution.png" alt="Dependency management big picture" style="zoom: 50%;" />

- Gradle Sync：只关心本地是否有项目所依赖的库，若没有远程是否能够拉取下来，都不行就会报错。

  ==就算本地缓存有所需的依赖库，也不能将下载依赖的maven仓库注释掉（即使开启离线模式也不行），会报错。但是当本地缓存完整，单独开启离线模式，成功。？？==

- Make Project：本地是否有项目所依赖的库。

  - Gradle Sync成功后才能启动；
  - 也不能注释依赖的maven仓库（==Connection refused: connect？？==）



- Artifact reuse

  在下载工件之前，Gradle会尝试通过下载与该工件关联的sha文件来确定所需工件的校验和. 如果可以检索校验和，那么如果已经存在具有相同ID和校验和的工件，则不会下载工件. 如果无法从远程服务器检索校验和，则将下载工件（如果它与现有工件匹配，则将被忽略）。

  除了考虑从其他存储库下载的工件外，Gradle还将尝试重用在本地Maven存储库中找到的工件. 如果Maven已下载了候选工件，则Gradle将使用此工件，前提是可以对其进行验证以匹配远程服务器声明的校验和。



- Cache Cleanup

  Gradle跟踪访问依赖项缓存中的哪些工件。使用此信息，定期（最多每24小时）扫描缓存，以查找**未使用超过30天**的工件. 然后删除过时的工件，以确保高速缓存不会无限期增长。



- buildSrc

- [Composing builds](https://docs.gradle.org/current/userguide/composite_builds.html)



- Terminal 运行 `gradlew :app:dependencies` 或 `gradlew -q app:dependencies` 命令查看app模块依赖关系树。

  **+---** 是依赖分支库的开始

  **|**     表示仍在当前依赖库分支，显示其进一步依赖关系

  **\\---**  依赖分支的末尾

  **(*)**  在依赖库的末尾，意味着省略该库的进一步依赖关系，因为它们已经在其它某个子依赖树中列出

  **->**   Gradle检测到多个版本的相同依赖，此时将默认选择较新的版本

- Terminal 运行 `gradlew assembleDebug` 或 双击执行Gradle窗口app模块下的assembleDebug任务 构建所有Debug版本的apk。

- Terminal 运行 `gradlew <module>:assemble` 生成对应module的jar包。

- 双击运行 Gradle > \<module> > Tasks > build > assemble 生成aar包（module/build/outputs/aar/）。

- `gradlew buildEnvironment`



- [对构建进行性能剖析](https://developer.android.google.cn/studio/build/optimize-your-build#profile)

- [各个 Android Gradle 插件版本所需的 Gradle 版本](https://developer.android.google.cn/studio/releases/gradle-plugin?hl=zh_cn#updating-gradle)。要获得最佳性能，应同时使用两者的最新版本。

| 插件版本      | 所需的 Gradle 版本 |
| ------------- | ------------------ |
| 1.0.0 - 1.1.3 | 2.2.1 - 2.3        |
| 1.2.0 - 1.3.1 | 2.2.1 - 2.9        |
| 1.5.0         | 2.2.1 - 2.13       |
| 2.0.0 - 2.1.2 | 2.10 - 2.13        |
| 2.1.3 - 2.2.3 | 2.14.1+            |
| 2.3.0+        | 3.3+               |
| 3.0.0+        | 4.1+               |
| 3.1.0+        | 4.4+               |
| 3.2.0 - 3.2.1 | 4.6+               |
| 3.3.0 - 3.3.3 | 4.10.1+            |
| 3.4.0 - 3.4.3 | 5.1.1+             |
| 3.5.0 - 3.5.4 | 5.4.1+             |
| 3.6.0 - 3.6.4 | 5.6.4+             |
| 4.0.0+        | 6.1.1+             |
| 4.1.0+        | 6.5+               |
| ...           | ...                |



#### 配置<a name="gradle-tips"></a>

- Gradle中 **''**（单引号）和 **""**（双引号）之间的区别：**''** 只能声明固定字符串，**""** 还能引用其他变量。

```groovy
def name = 'Shaun'
def greeting = "Hello, $name."
```

- **build.gradle**（rootProject）

```groovy
// 引入其他gradle配置文件
apply from: "config.gradle"
// Gradle脚本执行所需配置
buildscript {
    repositories {
        google()
        jcenter()
    }
    // gradle插件依赖
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.2'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
// 对所有project的配置，包括rootProject
allprojects {
    repositories {
        google()
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
// 对所有childProject（module）的配置
subprojects {
    // 统一定义一些所有module都需要的公共配置
    afterEvaluate {
        android {
            resourcePrefix "${project.name.toLowerCase()}_"

            compileOptions {
                sourceCompatibility = JavaVersion.VERSION_1_8
                targetCompatibility = JavaVersion.VERSION_1_8
            }
        }
    }
}
// Incremental compilation (default after 4.10?)
tasks.withType(JavaCompile) {
    options.incremental = true
}
```

- **build.gradle**（module）

```groovy
android {
    defaultConfig {
        /*未定义flavor的默认声明/已定义所有flavor的公共声明
         （productFlavors支持与defaultConfig相同的属性）*/
        ...
        // 启用多dex打包
        multiDexEnabled true
        // 启用矢量图兼容库
        vectorDrawables.useSupportLibrary = true
        /*子module定义了flavorDimensions和productFlavors，其父module要同样声明，
          或者通过missingDimensionStrategy属性按先dimension后flavor格式列出来就行，
          但是多个dimension时，编译没问题，运行仍然会有问题？*/
        // missingDimensionStrategy 'dimension', 'flavorA', 'flavorB', ...

        // 注意不能和 splits.abi 同时使用
        ndk {
            // Specifies the ABI configurations of your native
            // libraries Gradle should build and package with your APK.
            abiFilters 'arm64-v8a', 'armeabi-v7a', 'armeabi'
        }

        // [仅应声明于子module中]
        // 用于将库自身的proguard配置 附加到主模块 or 嵌入到aar文件中
        // consumerProguardFiles 'consumer-rules.pro'
    }

    // signingConfigs闭包要位于buildTypes闭包之前，否则无法在具体的<build-Type>中引用
    signingConfigs {
        release {
            storeFile file("${rootDir}/xxx.jks")
            storePassword 'passphrase'
            keyAlias 'alias'
            keyPassword 'passphrase'
        }
    }

    buildTypes {
        release {
            /*代码混淆
              混淆详情位于<module>/build/outputs/mapping/release/mapping.txt文件中*/
            minifyEnabled true
            /*移除未使用资源，需要开启代码混淆才有用
              缩减详情位于<module>/build/outputs/mapping/release/resources.txt文件中,
              不会移除第三方依赖库资源*/
            shrinkResources true
            // 启用APK数据压缩对齐，减少运行应用时消耗的RAM容量
            zipAlignEnabled true

            signingConfig signingConfigs.release
            ext.enableCrashlytics = false
            // Disables PNG crunching (convert WebP) for the release build type. Default false in debug build type.
            // 由于构建类型或产品变种不定义此属性，因此在构建应用的发布版本时，您需要手动将此属性设置为 true?
            crunchPngs false
            ...
        }
        debug {
            // 若不需要Firebase Crashlytics插件，停用以提高构建速度
            ext.enableCrashlytics = false
            // 阻止Crashlytics在每次构建中更新build ID（应用资源变动导致Apply Code Changes失败）
            ext.alwaysUpdateBuildId = false
        }
        ...
    }

    /*若只定义了唯一的dimension，则所有flavor可以缺省dimension声明，默认就是唯一的dimension，
      但是只要声明了productFlavors，就必须声明至少一个flavorDimensions，不可缺省；
      另外，已定义的dimension必须使用，不可闲置*/
    flavorDimensions 'demo', 'test', ...
    productFlavors {
        flavorA {
            dimension 'demo'
            applicationId 'com.android.alpha'
            applicationIdSuffix '.demo'
            versionCode 1
            versionName '1.2.3'
            versionNameSuffix '-demo'
            // 在manifest文件中以"${APP_ICON}"格式使用
            manifestPlaceholders = [APP_ICON: '@mipmap/icon',
                                    APP_NAME: 'AppDemo']
            /*括号可缺省
              每次修改值后要先Make Project使其生效*/
            buildConfigField('String', 'TYPE', '"DEMO"')
        }
        flavorB {
            dimension 'test'
            ...
        }
        ...
    }

    /*lint会检查此处定义的资源前缀，与约束不符的资源会在编辑器中红线提示（仅提示，是否修改取决于开发者）。
      这实际上只是一个lint检查，不影响开发，但使用此功能可以降低module、依赖库等之间的资源同名冲突。
      注：此lint检查只适用于xml资源，图片资源仍需开发者自己注意约束。*/
    resourcePrefix "${project.name.toLowerCase()}_"

    /*更改源文件集目录*/
    sourceSets {
        main {
            // 更改Java源码目录
            java.srcDirs = ['other/java']
            // 若so库文件置于 <module>/libs/ 下，需在此配置资源集路径
            jniLibs.srcDirs = ['libs']
            // 更改资源文件目录，可定义多个目录，禁止定义嵌套目录，禁止在不同目录中定义相同资源
            res.srcDirs = ['other/res1', 'other/res2']
            // 一个source set只支持定义一个manifest文件
            manifest.srcFile 'src/main/debug/AndroidManifest.xml'
        }
    }

    // targetCompatibility >= sourceCompatibility
    compileOptions {
        /*Java version compatibility to use when compiling Java source.
          Default value: version of the current JVM in use.
          跟编译环境相关，关系到编程使用的Java语法特性。*/
        sourceCompatibility = JavaVersion.VERSION_1_8
        /*Java version to generate classes for.
          Default value: sourceCompatibility.*/
        targetCompatibility = JavaVersion.VERSION_1_8
    }

    // lint检查配置 (this is only checked for non-debug (release) builds)
    lintOptions {
        // Turns off checks for the issue IDs you specify.
        disable 'TypographyFractions', 'TypographyQuotes'
        // Turns on checks for the issue IDs you specify. These checks are in
        // addition to the default lint checks.
        enable 'StopShip', 'RtlHardcoded', 'RtlCompat', 'RtlEnabled'
        // To enable checks for only a subset of issue IDs and ignore all others,
        // list the issue IDs with the 'check' property instead. This property overrides
        // any issue IDs you enable or disable using the properties above.
        check 'NewApi', 'InlinedApi'
        // If set to true, turns off analysis progress reporting by lint.
        quiet true
        // if set to true (default), stops the build if errors are found.
        abortOnError false
        // if true, only report errors.
        ignoreWarnings true
    }

    // 打包apk配置
    packagingOptions {
        // 排除一些文件，不打包进apk
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/DEPENDENCIES.txt'
        exclude 'META-INF/LGPL2.1'
        exclude 'META-INF/ASL2.0'
        ...
    }

    splits {
        // Configures multiple APKs based on screen density.
        density {
            // Configures multiple APKs based on screen density.
            enable true
            // Specifies a list of screen densities Gradle should not create multiple APKs for.
            exclude "ldpi", "xxhdpi", "xxxhdpi"
            // Specifies a list of compatible screen size settings for the manifest.
            compatibleScreens 'small', 'normal', 'large', 'xlarge'
        }
    }

    // 注意不能和 ndk.abiFilters 同时使用
    splits {
        // Configures multiple APKs based on ABI.
        abi {
            // Enables building multiple APKs.
            enable true
            // By default all ABIs are included, so use reset() and include to specify that we only
            // want APKs for x86, armeabi-v7a, and mips.
            reset()
            // Specifies a list of ABIs that Gradle should create APKs for.
            include 'arm64-v8a', 'armeabi-v7a', 'armeabi'
            // Specify that we want to also generate a universal APK that includes all ABIs.
            universalApk false
        }
    }

    // 重命名生成apk
    applicationVariants.all { variant ->
        variant.outputs.all {
            if (outputFileName.endsWith('.apk') && variant.buildType.name == 'release') {
                outputFileName = "${variant.flavorName}-${variant.versionName}.apk"
            }
        }
    }
    
    configurations.all {
        // 强制指定第三方库依赖的类库版本
        resolutionStrategy {
            // 将强制'me.jessyan:arms:2.5.1'依赖3.12.10版okhttp
            force 'com.squareup.okhttp3:okhttp:3.12.10'
        }
    }
}

// 是否需要声明？
/*configurations {
      flavorA
      flavorB
  }*/
dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')

    implementation('me.jessyan:arms:2.5.1') {
        // 排除重复依赖
        // 排除同一group的所有依赖（groupId）
        exclude group: 'com.squareup.okhttp3'
        // 排除指定group中的指定module依赖（artifactId）
        exclude group: 'io.reactivex.rxjava2', module: 'rxjava'
    }

    // 排除module重复依赖，注意括号
    implementation(project(path: ':submodule')) {}

    // productFlavor依赖
    flavorAImplementation ..
    flavorBImplementation ..
}
```



- **source set**

  Gradle有一个source set的概念，因此不同的productFlavor可以设置不同的source set。AS新建项目后默认source set为main，即src/main。也可以设置src/flavorA，src/flavorB等，即使用productFlavor对应的名称，目录结构同main。这样编译时各个flavor就会优先使用自己source set下的资源。

- **resValue**

  >resValue提供了一种在Gradle中定义属性资源的简单方式，生成的属性位于\<module>/build/generated
  >
  >/res/resValues/\<product-Flavor>/\<build-Type>/values/gradleResValue.xml中。

  定义位置：

  ```groovy
  // defaultConfig闭包中
  defaultConfig {
      // 括号可缺省，注意与res/values/string.xml同名冲突
      resValue ('string', 'defaultConfig', 'test')
  }
  buildTypes {
      // 具体的buildType闭包中
      debug {
          resValue 'bool', 'buildTypes', 'false'
      }
  }
  productFlavors {
      // 具体的productFlavor闭包中
      flavorA {
          resValue 'integer', 'flavors', '1'
      }
  }
  ```

  使用方式：

  ```java
  Log.i("resValue", getResources().getString(R.string.defaultConfig));
  Log.i("resValue", String.valueOf(getResources().getBoolean(R.bool.buildTypes)));
  Log.i("resValue", String.valueOf(getResources().getInteger(R.integer.flavors)));
  ```

  ```xml
  android:text="@string/defaultConfig"
  ```



- **config.gradle**（依赖统一配置管理）

```groovy
// Common Settings
ext.versions = [
    android : [
        compileSdk: 29,
        targetSdk : 23,
        minSdk    : 19
    ],
    support : '27.1.1',
    retrofit: '2.8.1',
    glide   : '4.9.0',
    ...
]
// Running Module(s) as a Library(false) or an Application(true)
ext.runAlone = [
    login: false,
    mine : false,
    ...
]
// dependencies
ext.deps = [
    test : [
        'junit'     : 'junit:junit:4.12',
        'leakcanary': 'com.squareup.leakcanary:leakcanary-android:2.2',
        ...
    ],
    support : [
        'appcompatv7'     : "com.android.support:appcompat-v7:${versions.support}",
        'supportv4'       : "com.android.support:support-v4:${versions.support}",
        'constraintlayout': 'com.android.support.constraint:constraint-layout:1.1.3',
        ...
    ],
    // tools
    'okhttp'            : 'com.squareup.okhttp3:okhttp:3.14.2',
    'retrofit'          : "com.squareup.retrofit2:retrofit:${versions.retrofit}",
    'rxjava'            : 'io.reactivex.rxjava2:rxjava:2.2.10',
    'rxandroid'         : 'io.reactivex.rxjava2:rxandroid:2.1.1',
    'adapterRxjava2'    : "com.squareup.retrofit2:adapter-rxjava2:${versions.retrofit}",
    'converterGson'     : "com.squareup.retrofit2:converter-gson:${versions.retrofit}",
    'gson'              : 'com.google.code.gson:gson:2.8.5',
    'loggingInterceptor': 'com.squareup.okhttp3:logging-interceptor:4.2.2',
    'commonsLang3'      : 'org.apache.commons:commons-lang3:3.10',
    ...
]
```





### Proguard

- [混淆命令](https://www.guardsquare.com/en/products/proguard/manual/usage)

  **General options**

  ------

  `-dontwarn [class_filter]` 不警告'未解析的引用'等问题 *(危险选项，请仅在了解后果的情况下使用，可能会造成运行时错误 )*

  `-ignorewarnings` 仍然打印输出'未解析的引用'等问题，但会忽略它们继续执行过程 *(和 `-dontwarn` 一样是危险选项 )*

  

  **Input/Output options**

  ------

  `-dontskipnonpubliclibraryclasses`

  `-dontskipnonpubliclibraryclassmembers`

  

  **Keep options**

  ------

  `-keep [,modifier,...] class_specification` 保留类及成员（若只匹配到类，保留类；若同时匹配到类及成员，则同时保留）

  ```
  #preserve all classes extending android.app.Activity
  -keep public class * extends android.app.Activity
  ```

  `-keepclassmembers [,modifier,...] class_specification` **仅**保留类成员(如fields & methods)

  ```
  #preserve all member ( static field ) named CREATOR on the condition if they are implementing android.os.Parcelable
  -keepclassmembers class * implements android.os.Parcelable {
      static ** CREATOR;
  }
  ```

  `-keepclasseswithmembers [,modifier,...] class_specification` 保留类及成员（**必须满足**声明的成员条件，否则全部混淆）

  ```
  #preserve all classes and the constructor if they have constructor ( mentioned below as <init> ) with parameters( Context, AttributeSet , int )
  -keepclasseswithmembers class * {
      public <init>(android.content.Context, android.util.AttributeSet, int);
  }
  ```

  `-keepnames class_specification`

  `-keepclassmembernames class_specification`

  `-keepclasseswithmembernames class_specification`

  | Keep                                                | From being removed or renamed | From being renamed            |
  | --------------------------------------------------- | ----------------------------- | ----------------------------- |
  | Classes and class members                           | `-keep`                       | `-keepnames`                  |
  | Class members only                                  | `-keepclassmembers`           | `-keepclassmembernames`       |
  | Classes and class members, if class members present | `-keepclasseswithmembers`     | `-keepclasseswithmembernames` |

  ***ps:*** 不确定如何选择时，使用 `-keep` 能最大程度上保留类及成员。另外，缺省 `class_specification` 的 `-keepclasseswithmembers` 和 `-keep` 效果相同，并且在此情况下Proguard仅保留类及其无参构造器。

  ***extension:*** **Keep option modifiers**

  - `includedescriptorclasses` 指定 -keep 选项所保留的方法和字段的类型描述符中涉及的所有类也应保留

  

  **Shrinking options**

  ------

  `-dontshrink` 不压缩输入。默认情况下Proguard会移除所有未使用的类及成员，除了keep命令列举出来的以及它们的(直/间接)依赖。并且会受到optimization步骤的影响。

  

  **Optimization options**

  ------

  `-optimizations [optimization_filter]` 这是一个高级命令

  `-optimizationpasses n` 指定优化次数

  `-assumenosideeffects class_specification` 声明无任何副作用的方法，并在混淆中移除相关调用 (如Log打印方法)

  `-assumevalues class_specification` 向变量和方法基本类型返回值 指定一个值或范围（范围以 "**..**" 分隔，包括两端的值）

  `-allowaccessmodification` 在执行过程中放宽访问修饰符。在开发library API时不要使用此命令，防止本应设计为私有的API被公开。

  

  **Obfuscation options**

  ------

  `-dontusemixedcaseclassnames` 混淆过程中不要生成大小写混合的类名（Windows不区分文件名大小写，开发者想要在win系统中unpack jar包时，应使用此选项，但会使生成的jar包稍微大一些）

  `-keeppackagenames [package_filter]` 不混淆指定包名

  `-repackageclasses [package_name]` 将重命名的类文件移动到指定包中。不指定包名或指定为（**''**），将彻底的移除包名（配合 `-allowaccessmodification` 选项可以极致压缩混淆，但在Android上的activities, views, etc.慎用）

  `-renamesourcefileattribute [string]` 重命名源文件（使用前需要先保留源文件名 `-keepattributes SourceFile,LineNumberTable`）

  `-keepattributes [attribute_filter]` 保留任意可选属性。混淆默认会丢弃以下可选属性：

  - `SourceFile` 源文件名称
  - `InnerClasses` 外/内部类关联关系，外/内部类名称以'$'相连（可能用于反射）
  - `EnclosingMethod` 方法中定义的本地类或匿名类（可能用于反射）
  - `Deprecated` 废弃的类/变量/方法
  - `Synthetic` 编译器合成的类/变量/方法
  - `Signature` 泛型（可能用于反射）
  - `MethodParameters` 方法参数名称/修饰符（可能用于反射）
  - `Exceptions` 方法抛出的异常（编译器可能需要这些信息来捕获它们）
  - `LineNumberTable` 方法行号表，若存在将包含在stack traces中
  - `LocalVariableTable` 局部变量表，局部变量的名称和类型
  - `LocalVariableTypeTable` 局部变量泛型的名称和类型
  - `RuntimeVisibleAnnotations` 运行期可见的*类/变量/方法* 注解（可能用于反射）
  - `RuntimeInvisibleAnnotations` 编译期可见的*类/变量/方法* 注解
  - `RuntimeVisibleParameterAnnotations` 运行期可见的*方法参数* 注解（可能用于反射）
  - `RuntimeInvisibleParameterAnnotations` 编译期可见的*方法参数* 注解
  - `RuntimeVisibleTypeAnnotations` 运行期可见的*泛型/instructions等* 注解（可能用于反射）
  - `RuntimeInvisibleTypeAnnotations` 编译期可见的*泛型/instructions等* 注解
  - `AnnotationDefault` 注解默认值

  

- Class specifications *template*（用于各种 *keep* 和 `-assumenosideeffects` 选项）

```
[@annotationtype] [[!]public|final|abstract|@ ...] [!]interface|class|enum classname [extends|implements [@annotationtype] classname]
[{
    [@annotationtype] [[!]public|private|protected|static|volatile|transient ...] <fields> | (fieldtype fieldname [= values]);

    # 这里只是模板，实际中必须声明成单行
    [@annotationtype] [[!]public|private|protected|static|synchronized|native|abstract|strictfp ...]
    <methods> | <init>(argumenttype,...) | classname(argumenttype,...) | (returntype methodname(argumenttype,...) [return values]);
}]
```

***ps:*** 

- 关键字 *class* 可以表示类或接口，而关键字 *interface* 只能表示接口，*enum* 也只能表示枚举类，前缀 *!* 取反。
- 关键字 *extends* 和 *implements* 用于限制通配符匹配到指定类/接口的子类/接口，但不包含父类/接口本身。
- ***@*** 用于指定全限定注解类型，语法规则同类名。
- `<fields>` 关键词前缀不能是类型，只能是修饰符；要指定特定类型的变量，必须使用 `fieldtype fieldname` 格式（无需外括号）。
- 所有类名必须是全限定类名，内/外部类通过 ***$*** 拼接。类名也可以指定为包含以下通配符的正则表达式：

| Wildcard | Meaning                                                      |
| :------: | ------------------------------------------------------------ |
|    ?     | 匹配类名中的任意**单个**字符，但不匹配包名分隔符"**.**"。如 `com.example.Test?` 能匹配 `com.example.Test1` 但不能匹配 `com.example.Test12`。 |
|    *     | 匹配类名中的任意部分，但不能包含包名分隔符"**.**"。如 `com.example.*Test*` 能匹配 `com.example.MyTestDemo`，但不能匹配 `com.example.subpackage.MyTest`；又如 `com.example.*` 匹配所有 `com.example` 包下的类，但不能匹配其子包下的类。 |
|    **    | 匹配类名中的任意部分，包括任意数量的包名分隔符"**.**"。如 `**.Test` 能匹配到除root package外的所有含 `Test` 的类；又如 `com.example.**` 能匹配到 `com.example` 及其子包下的所有类。 |
|   \<n>   | ==？== 匹配当前模式下第**n**个匹配到的通配符。如 `com.example.*Foo<1>` 首次匹配的类可能是 `com.example.BarFooBar`。 |

- 变量和方法语法规则类似Java，但是方法签名不包含参数名。并且可以包含以下通配符：

|  Wildcard  | Meaning            |
| :--------: | ------------------ |
|  \<init>   | 匹配任意构造函数   |
| \<fields>  | 匹配任意变量       |
| \<methods> | 匹配任意方法       |
|     *      | 匹配任意变量或方法 |

***ps:*** 以上通配符没有返回类型，且只有构造器通配符包含参数列表。

- 变量和方法也可以使用正则表达式：

| Wildcard | Meaning                                        |
| :------: | ---------------------------------------------- |
|    ?     | 匹配方法名中的任意**单个**字符。               |
|    *     | 匹配方法名中的任意部分。                       |
|   \<n>   | ==？== 匹配当前模式下第**n**个匹配到的通配符。 |

- Types in descriptors can contain the following wildcards:

| Wildcard | Meaning                                                      |
| :------: | ------------------------------------------------------------ |
|    %     | 匹配任何基础类型*(int, boolean, etc, but not void)*          |
|   ***    | 匹配任意类型*(primitive or non-primitive, array or non-array)* |
|   ...    | 匹配任意数量/类型的参数                                      |

***ps:*** **?** **\*** **\*\*** 始终不会匹配 *primitive type*。并且只有 **\*\*\*** 能匹配任意维度的数组。如 `** get*()` 可以匹配 `java.lang.Object getObject()`，但不会匹配 `float getFloat()`，也不会匹配 `java.lang.Object[] getObjects()`。

- 访问修饰符用于限制类及成员的通配符，前缀 *!* 表示未设置相应的修饰符。在不冲突的情况下，可以组合使用。Proguard同样支持由编译器生成的修饰符如：*synthetic*、*bridge*、*varargs*。



- Android项目混淆基本配置总结 `proguard-rules.pro`

```
###region Project options
#保留实体model的变量及setter/getter
#Gson是通过变量名(反)序列化的，若只用了Gson可以像下面这样声明，而不是上面的
-keep class com.example.bean.** {
    <fields>;

    void set*(***);
    void set*(int, ***);

    *** get*();
    *** get*(int);

    boolean is*();
    boolean is*(int);
}

#保留自定义 Webview的公共成员
#-keepclassmembers class <fqcn.of.javascript.interface.for.webview> {
#    public *;
#}
#保留 @JavascriptInterface注解的方法
#-keepclassmembers class * {
#    @android.webkit.JavascriptInterface <methods>;
#}

#已在proguard-android-optimize.txt中声明, 但未使用includedescriptorclasses修饰
#此修饰符可同时保留 native方法的返回值、参数类型, 即整个方法签名 (可能用于NDK开发)
#-keepclasseswithmembernames,includedescriptorclasses class * {
#    native <methods>;
#}

#Your application may contain more items that need to be preserved;
#typically classes that are dynamically created using Class.forName:

#-keep public class com.example.MyClass
#-keep public interface com.example.MyInterface
#-keep public class * implements com.example.MyInterface

#声明支持的Android SDK版本范围 (minSdkVersion至maxSdkVersion)
#这将移除所有版本范围外的代码，如Android的各种support库
#关键字 '=' 和 'return' 是可互换的
-assumevalues class android.os.Build$VERSION {
    int SDK_INT return 21..2147483647;
}
#########endregion

###region 3rd library options
##Gson specific classes
-dontwarn sun.misc.**
-keep class sun.misc.Unsafe { *; }
#-keep class com.google.gson.stream.** { *; }

#Prevent R8 from leaving Data object members always null
-keepclassmembers,allowobfuscation class * {
    @com.google.gson.annotations.SerializedName <fields>;
}

#Prevent proguard from stripping interface information from TypeAdapter, TypeAdapterFactory,
#JsonSerializer, JsonDeserializer instances (so they can be used in @JsonAdapter)
-keep class * extends com.google.gson.TypeAdapter
-keep class * implements com.google.gson.TypeAdapterFactory
-keep class * implements com.google.gson.JsonSerializer
-keep class * implements com.google.gson.JsonDeserializer
#########endregion

###region General options
#已在proguard-android-optimize.txt中声明，这里只是示例 -dontwarn 指令
#-dontwarn android.support.**
#忽略警告
#-ignorewarnings
#########endregion

###region Input/Output options
#在程序类与库类处于同一包且引用了库类中的可见成员时，需声明此命令保持一致性
#-dontskipnonpubliclibraryclassmembers
#########endregion

###region Keep options
#AAPT2已经在<module-dir>/build/intermediates/proguard-rules/debug/aapt_rules.txt中
#配置了AndroidManifest.xml声明的四大组件、Application子类以及xml中使用的自定义View
#保留动态注册的 BroadcastReceiver
-keep public class * extends android.content.BroadcastReceiver { <init>(); }

##以下配置用于最大程度兼容(反)序列化(即使涉及的类缺少 serialVersionUID字段也能确保部分兼容性)
#保留 Serializable实现类及其成员原始名称
-keepnames class * implements java.io.Serializable
#保留 Serializable实现类以下成员
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    !private !static !transient <fields>;
    !private <methods>;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}
#极少情况下需要在Java 8或更高版本中序列化 lambda表达式
#保留 JVM自动合成的 lambda相关方法
-keepclassmembers class * {
    private static synthetic java.lang.Object $deserializeLambda$(java.lang.invoke.SerializedLambda);
}
-keepclassmembernames class * {
    private static synthetic *** lambda$*(...);
}
#混淆出现以上 lambda相关合成方法的类中的 “以类名称硬编码”的字符串常量
#-adaptclassstrings com.example.Test
##ps: 以上配置可能会保留比实际真正所需更多的类/成员, 因为并非所有 Serializable实现类会被真正序列化
###开发者应足够了解项目以作出最具兼容性的配置

#保留自定义 Context子类(通常是Activity)中可能的 android:onClick xml属性所引用的方法
#只有 android.view.View或 android.view.MenuItem一种参数类型
-keepclassmembers class * extends android.content.Context {
    public void *(android.view.View);
    public void *(android.view.MenuItem);
}

#保留带有回调函数onXXEvent、**On*Listener等的类
-keepclassmembers class * {
    void *(**On*Event);
    void *(**On*Listener);
}

#保留自定义View
-keepclasseswithmembers public class * extends android.view.View{
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
#########endregion

###region Optimization options
#avoid simplification of enum types to primitives
-optimizations !class/unboxing/enum

#声明无任何副作用的方法，并在混淆中移除相关调用 (如Log打印方法)
-assumenosideeffects class android.util.Log {
    public static boolean isLoggable(java.lang.String, int);
    public static int v(...);
    public static int i(...);
    public static int w(...);
    public static int d(...);
    public static int e(...);
}

#StringBuffer#append methods have side effects, but no external side effects.
-assumenoexternalsideeffects class java.lang.StringBuilder {
    public java.lang.StringBuilder();
    public java.lang.StringBuilder(int);
    public java.lang.StringBuilder(java.lang.String);
    public java.lang.StringBuilder append(java.lang.Object);
    public java.lang.StringBuilder append(java.lang.String);
    public java.lang.StringBuilder append(java.lang.StringBuffer);
    public java.lang.StringBuilder append(char[]);
    public java.lang.StringBuilder append(char[], int, int);
    public java.lang.StringBuilder append(boolean);
    public java.lang.StringBuilder append(char);
    public java.lang.StringBuilder append(int);
    public java.lang.StringBuilder append(long);
    public java.lang.StringBuilder append(float);
    public java.lang.StringBuilder append(double);
    public java.lang.String toString();
}
#Specifies methods that don't return reference values that were already on the heap when they are called.
-assumenoexternalreturnvalues public final class java.lang.StringBuilder {
    public java.lang.StringBuilder append(java.lang.Object);
    public java.lang.StringBuilder append(java.lang.String);
    public java.lang.StringBuilder append(java.lang.StringBuffer);
    public java.lang.StringBuilder append(char[]);
    public java.lang.StringBuilder append(char[], int, int);
    public java.lang.StringBuilder append(boolean);
    public java.lang.StringBuilder append(char);
    public java.lang.StringBuilder append(int);
    public java.lang.StringBuilder append(long);
    public java.lang.StringBuilder append(float);
    public java.lang.StringBuilder append(double);
}
#########endregion

###region Obfuscation options
#当配合-allowaccessmodification优化时才适用
#-repackageclasses

#保留源文件名/行号/泛型..
-keepattributes SourceFile,LineNumberTable,InnerClasses,EnclosingMethod,Deprecated,Signature,Exceptions
#重命名源文件（和保留行号配合使用）
-renamesourcefileattribute SourceFile
#########endregion
```



- 使用SDK工具追溯堆栈信息

1. 打开 `<sdk-dir>\tools\proguard\bin\proguardgui.bat` 脚本文件，并选中左侧功能栏 **ReTrace** 选项；
2. 选择Mapping file：`<module-dir>\build\outputs\mapping\[variant]\release\mapping.txt`；
3. 拷贝粘贴<u>Crash堆栈信息</u>至 *Obfuscated stack trace* 栏中；
4. 单击右下角 **ReTrace!** 按钮，完成堆栈信息追溯。

***ps:*** 混淆打包后记得保留对应的mapping文件。也可使用命令行：`java -jar retrace.jar [options..] mapping.txt stacktrace.txt` （*retrace.jar* 位于 `<sdk-dir>\tools\proguard\lib\` 目录下）。

- [mapping.txt 结构](https://www.guardsquare.com/en/products/proguard/manual/retrace)

```
// Specifications
classline
    fieldline *
    methodline *

// classline以冒号结尾，指定了类名及相应的混淆名
originalclassname -> obfuscatedclassname:
    // fieldline以4个空格开头，指定了变量类型、名称及相应的混淆名
    originalfieldtype originalfieldname -> obfuscatedfieldname
    // methodline同样以4个空格开头，指定了一个方法混淆前后的相关信息
    [startline:endline:]originalreturntype [originalclassname.]originalmethodname(originalargumenttype,...)[:originalstartline[:originalendline]] -> obfuscatedmethodname
```

***ps:*** "*" 表示当前行可能出现多次；"[]" 表示其内容是可选的；"..." 表示可指定任意数量的同类前述项目；":" "." "->" 是文字标记。

- 示例（增加注释说明）

```
com example.application.ArgumentWordReader -> com.example.a.a:
    java.lang.String[] arguments -> a
    int index -> a
    // 以String数组和File为形参的构造函数
    36:57:void <init>(java.lang.String[],java.io.File) -> <init>
    64:64:java.lang.String nextLine() -> a
    72:72:java.lang.String lineLocationDescription() -> b
com.example.application.Main -> com.example.application.Main:
    com.example.application.Configuration configuration -> a
    50:66:void <init>(com.example.application.Configuration) -> <init>
    // execute方法混淆后的方法体范围为74-228，混淆名称为 a（不同的变量和方法可以使用相同的混淆名，只要它们的描述符不同）
    74:228:void execute() -> a
    2039:2056:void com.example.application.GPL.check():39:56 -> a
    2039:2056:void execute():76 -> a
    2236:2252:void printConfiguration():236:252 -> a
    2236:2252:void execute():80 -> a
    // 内联方法：混淆文件中的一行可能对应于原异常堆栈中的多行（重复的行号范围）. 单独的尾部行号对应内联方法被调用的位置
    // 如下面3行表示execute方法调用并内联了printConfiguration方法，而printConfiguration方法又调用并内联了createPrintWriterOut方法
    // 原始方法名若包含了类名，表示该方法来自其它类，如createPrintWriterOut方法来自PrintWriterUtil类，而非Main类，且方法体范围为40-42
    // 混淆后的方法体范围3040-3042是为了确保在Main类中的唯一性而合成的(偏移1000的整数倍)，不过仍然和其在本来类(PrintWriterUtil)中的范围类似
    3040:3042:java.io.PrintWriter com.example.application.util.PrintWriterUtil.createPrintWriterOut(java.io.File):40:42 -> a
    // printConfiguration方法在第243行调用了createPrintWriterOut方法
    3040:3042:void printConfiguration():243 -> a
    // execute方法在第80行调用了printConfiguration方法
    3040:3042:void execute():80 -> a
    3260:3268:void readInput():260:268 -> a
    3260:3268:void execute():97 -> a
```

***ps:*** 应考虑 *保留行号* 使堆栈信息追溯起来更加清晰（如上面的例子，若未保留行号，则ReTrace可能无法确定a对应的方法而将它们都列出来）。

- *access$100* 代表什么？

  *access$XXX* 方法是编译器自动合成的*(synthetic)*内部类对外部类私有成员的隐式引用（不通过反射从一个类中访问其它类的private成员，个数由内部类要访问的外部类成员数决定）

  *OuterClass* ***$*** *InnerClass* 代表内部类（包括匿名内部类）如：

  ```
  com.demo.adapter.ExampleAdapter$ItemViewHolder -> com.demo.adapter.ExampleAdapter$a:
  ```

  lambda表达式是以 **lambda$** 开头，**$序号** 结尾的方法行





### 开发技巧

#### IDE

------

- [tools attributes](https://juejin.im/post/5d500b1a6fb9a06b1417d5c9)

> 即以**tools**命名空间开头的属性。如*tools:context=".MainActivity"*，指定与布局默认关联的Activity。
>
> Android Studio支持很多tools attributes，这些属性会在构建APP时擦除，对APK的大小和运行时行为无任何影响。[官方文档](https://developer.android.com/studio/write/tool-attributes)

- 生成代码文档（菜单栏 *Tools -> Generate JavaDoc*）

  Other command line arguments：**-encoding utf-8 -charset utf-8**（此项配置能避免编码问题）

- 上传图片后模拟器Gallery应用却显示No Media Found

  设置冷启动后重新打开模拟器

- 无法查看RecyclerView源码？
  - 网络允许时选择 Download Source
  - 否则可以配置 Google的离线maven库，选择其中的源码即可
  
- 关联Android SDK源码

  修改build.gradle中compileSdkVersion到指定API版本，即可查看对应版本的SDK源码 (请统一修改所有module，不然可能会关联多个版本)。
  
- 依赖so库

  - 在 \<module>/src/main/ 下新建 **jniLibs** 目录，将so库文件夹复制到该目录下即可（推荐）。
  - 将so库文件夹直接复制到 \<module>/libs/ 目录下，并在build.gradle文件中重定向jniLibs.srcDirs资源集至该目录。
  
- 当NDK项目调试无法启动LLDB时，在 **Edit Configuration** → **Debugger** → **Debug type** 中改选 **Java** 可以仅调试Java代码。

- R文件位置*(AS 3.6.3):* `app/build/generated/not_namespaced_r_class_sources/<build-type>/r/<package-name>/R.java`

- 使用 `mksdcard -l label size file` 命令创建SD卡共享磁盘映像（创建AVD时指定SD card为external file）

  如 `mksdcard -l sdcard 2048M shared_sdcard.img`



#### [Android Debug Bridge](https://developer.android.google.cn/studio/command-line/adb)

>adb 是一种功能多样的命令行工具，可让您与设备进行通信。adb 命令可用于执行各种设备操作（例如安装和调试应用），并提供对 Unix shell（可用来在设备上运行各种命令）的访问权限。它是一种客户端-服务器程序，包括以下三个组件：
>
>- **客户端**：用于发送命令。客户端在开发计算机上运行。您可以通过发出 adb 命令从命令行终端调用客户端。
>- **守护程序 (adbd)**：用于在设备上运行命令。守护程序在每个设备上作为后台进程运行。
>- **服务器**：用于管理客户端与守护程序之间的通信。服务器在开发机器上作为后台进程运行。
>
>工作原理：
>
>当启动某个 adb 客户端时，该客户端会先检查是否有 adb 服务器进程正在运行。若没有，它会启动服务器进程。服务器在启动后会与本地 TCP 端口 5037 绑定，并监听 adb 客户端发出的命令 - 所有 adb 客户端均通过端口 5037 与 adb 服务器通信。
>
>然后，服务器会与所有正在运行的设备建立连接。它通过扫描 5555 到 5585 之间（该范围供前 16 个模拟器使用）的奇数号端口查找模拟器。服务器一旦发现 adb 守护程序 (adbd)，便会与相应的端口建立连接。注意，每个模拟器都使用一对按顺序排列的端口 - 用于控制台连接的偶数号端口和用于 adb 连接的奇数号端口。例如：
>
>模拟器 1，控制台：5554
>模拟器 1，adb：5555
>模拟器 2，控制台：5556
>模拟器 2，adb：5557
>依此类推
>
>如上所示，在端口 5555 处与 adb 连接的模拟器与控制台监听端口为 5554 的模拟器是同一个。
>
>服务器与所有设备均建立连接后，便可以使用 adb 命令访问这些设备。由于服务器管理与设备的连接，并处理来自多个 adb 客户端的命令，因此可以从任意客户端（或从某个脚本）控制任意设备。



- `-a`

  ​									directs adb to listen on all interfaces for a connection

- `adb [-d | -e | -s <serial number>] <command>`

  ​									directs command to the specific device [ Overrides *ANDROID_SERIAL* environment variable ]

  ​									('-d' means **the only connected USB**)

  ​									('-e' means **the only running emulator**)

  ​									('-s' means **the device or emulator with the given serial number or qualifier**)

  ​									returns an error if more than one specific device is present.

  - `adb -s emulator-5554 shell`

- `-p <product name or path>`

  ​									simple product name like 'sooner', or

  ​									a relative/absolute path to a product

  ​									out directory like 'out/target/product/sooner'.

  ​									If -p is not specified, the ANDROID_PRODUCT_OUT

  ​									environment variable is used, which must

  ​									be an absolute path.

- `-H`

  ​									Name of adb server host (default: localhost)

- `-P`

  ​									Port of adb server (default: 5037)

- `adb devices [-l]`

  ​									list all connected devices [ device | offline | no device ]

  ​									('-l' will also list device qualifiers)

- `adb connect <host>[:<port>]`

  ​									connect to a device via TCP/IP

  ​									Port 5555 is used by default if no port number is specified.

- `adb disconnect [<host>[:<port>]]`

  ​									disconnect from a TCP/IP device.

  ​									Port 5555 is used by default if no port number is specified.

  ​									Using this command with no additional arguments

  ​									will disconnect from all connected TCP/IP devices.

***device commands:***

- `adb help [all]`
- `adb version`

- `adb push [-p] <local> <remote>`

  ​									copy file/dir to device

  ​									('-p' to display the transfer progress)

- `adb pull [-p] [-a] <remote> [<local>]`

  ​									copy file/dir from device

  ​									('-p' to display the transfer progress)

  ​									('-a' means copy timestamp and mode)

- <a href="#adb-shell">*adb shell*</a>

  ​									run remote shell interactively

  `adb shell <command>`

  ​									run remote shell command

- `adb logcat [ <filter-spec> ]`

  ​									view device log

- `adb install [-l] [-r] [-d] [-s] [--algo <algorithm name> --key <hex-encoded key> --iv <hex-encoded iv>] <file>`

  ​									push this package file to the device and install it

  ​									('-l' means forward-lock the app)

  ​									('-r' means reinstall the app, keeping its data)

  ​									('-d' means allow version code downgrade)

  ​									('-s' means install on SD card instead of internal storage)

  ​									('--algo', '--key', and '--iv' mean the file is encrypted already)

  - `adb install H:\keystore\Alpha.apk` // 只有一个AVD在运行
  - `adb -s emulator-5554 install H:\keystore\Alpha.apk` // 多个AVD同时运行时指定5554号AVD

- `adb uninstall [-k] <package>`

  ​									remove this app package from the device

  ​									('-k' means keep the data and cache directories)

- `adb bugreport`

  ​									return all information from the device that should be included in a bug report

***scripting:***

- `adb wait-for-device`

  ​									block until device is online

- `adb start-server`

  ​									ensure that there is a server running

- `adb kill-server`

  ​									kill the server if it is running

- `adb get-state`

  ​									prints: offline | bootloader | device

- `adb get-serialno`

  ​									prints: \<serial-number>

- `adb get-devpath`

  ​									prints: \<device-path>

- `adb status-window`

  ​									continuously print device status for a specified device

- `adb root`

  ​									restarts the adbd daemon with root permissions

  `adb shell setprop service.adb.root 0`

  `adb shell setprop ctl.restart adbd`

  *or*

  `adb unroot`

  ***ps:*** root模式下shell的命令行提示符为 **#**，非root模式下为 **$**。也可以在shell下执行 `su` 命令切换至root用户（无需重启adb）。

- `adb tcpip <port>`

  ​									restarts the adbd daemon listening on TCP on the specified port

***environmental variables:***

- *ADB_TRACE*

  ​									Print debug information. A comma (**,**) separated list of the following values 1 or all:

  ​									adb, sockets, packets, rwx, usb, sync, sysdeps, transport, jdwp.

- *ANDROID_SERIAL*

  ​									The serial number to connect to. -s takes priority over this if given.

- *ANDROID_LOG_TAGS*

  ​									When used with the logcat option, only these debug tags are printed.



- 自定义adb端口：新建环境变量 _ANDROID_ADB_SERVER_PORT_，自定义端口号即可。
- 修改文件权限：linux指令（chmod）`chmod 660 eg.txt`



**adb shell**<a name="adb-shell"></a>

- *command* 帮助

  `adb shell <command> --help`

- 获取activity栈（TaskRecord下Activities中就是当前activity栈）

  `adb shell "dumpsys activity activities|grep <applicationId>"`

- 获取栈顶activity

  `adb shell "dumpsys activity activities|grep mResumedActivity"`

- 获取进程状态

  `adb shell ps -A/e`

  ​									All processes

  *ps:* shell环境可使用 *grep* 指令过滤条件，DOS环境使用 *findstr* 替代。

- `adb shell getprop [-TZ] [NAME [DEFAULT]]`

  ​									Gets an Android system property, or lists them all.

  *e.g.* `adb shell getprop ro.zygote`	获取 *ro.zygote* 属性值

  `adb shell "getprop | grep init.svc"`	查看所有service的运行状态：[ running|stopped|restarting ]

  *ps:* ***ctl*.** 开头的属性表示控制消息，用于执行一些命令；***ro.*** 表示只读，不可更改；***persist.*** 用于将属性持久化到文件中，会带来额外的IO操作。

- `adb shell id [-GZgnru]`

  ​									Print user and group ID.

- Android 提供了大多数常见的 Unix 命令行工具。查看可用工具的列表：

  `adb shell ls /system/bin`
  
- 退出交互式shell

  Ctrl+D 或 `exit`（win7 Ctrl+C）






### 快捷键

- **代码自动完成**

  - 基本自动完成 **Ctrl+Space**

    显示对变量、类型、方法和表达式等的基本建议。如果 **连续两次** 调用基本自动完成，将显示更多结果，包括私有成员和非导入静态成员。

  - 智能自动完成 **Ctrl+Shift+Space**

    根据上下文显示相关选项。智能自动完成可识别预期类型和数据流。如果 **连续两次** 调用智能自动完成，将显示更多结果，包括链。

  - 语句自动完成 **Ctrl+Shift+Enter**

    为您自动完成当前语句，添加缺失的圆括号、大括号、花括号和格式化等。

- **Alt+Enter** 执行快速修复并显示建议的操作

- **Ctrl+Alt+L** 格式化代码

- **Ctrl+Shift+F12** 隐藏所有工具窗口，最大化代码编辑区

- **Alt+Shift+Up/Down** 上下移动行

- **Ctrl+Shift+Up/Down** 上下移动句段

- **Ctrl+Alt+Enter** 上开新行

- **Shift+Enter** 下开新行

- **Ctrl+[** 光标移至代码块开头

- **Ctrl+]** 光标移至代码块结尾

- **Ctrl+Alt+V** 自动生成变量 (Refactor | Extract | Variable...)

![快速生成变量](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/auto-generate-variable.gif)

- **Ctrl+Alt+F** 提取变量

- **Ctrl+Alt+C** 提取常量

- **Ctrl+Alt+M** 提取方法

- **Ctrl+/** 行注释

- **Ctrl+Shift+/** 块注释

- **Ctrl+Y** 删除行

- **Ctrl+X** 删除并复制行

- **Ctrl+D** 复制并粘贴行

- **Ctrl+P** 显示方法参数签名列表

- **Ctrl+B** 快速跳转到声明处

- **Ctrl+Alt+B** 快速跳转到实现（光标要点到接口、类名、方法或变量上）

- **Ctrl+U** 快速跳转到父类或接口（在类的任何地方都可以使用）

- **Ctrl+O/I** 覆写或实现方法

- **Ctrl+F12** *文件结构*：显示当前类的所有实例变量和方法

- **Ctrl+E** 最近文件

- **Ctrl+N** 查找类

- **Ctrl+Shift+N** *查找文件*   要搜索文件夹，但不搜索文件，请在表达式末尾添加“/”

- **Ctrl+Shift+Alt+N** 使用*查找符号* 操作按名称导航至方法或字段

- **Ctrl+Shift+I** 不离开当前文件直接弹窗预览光标处类或方法的实现

- **Ctrl+W** (extend selection) in the editor selects the word at the caret and then selects expanding areas of the source code.

- **Ctrl+Shift+J** shortcut joins two lines into one and removes unnecessary space to match your code style.

- **Ctrl+J** Complete any valid *Live Template* abbreviation.

- **Shift+F6** 重命名

- Use **Alt+Up** and **Alt+Down** keys to quickly move between methods in the editor.

- **Alt+数字** 开关工具窗口

- **Alt+`** VCS操作菜单

- **Alt+Insert** 快速生成类默认方法，如构造方法、Getter、Setter、toString等

- 同时选取多个相同元素进行操作：**Alt+J** 选取下一个；**Ctrl+Alt+Shift+J** 选取所有；**Alt+Shift+J** 依次反选

- 按 **Alt+F7** 查找引用当前光标位置处的类、方法、字段、参数或语句的所有代码片段

- **Ctrl+Shift+Alt+T** 重构(Refactor)操作列表

- **Ctrl+Alt+T** 包裹(Surround With)操作列表

  自定义代码折叠区域 ***2. region...endregion Comments***

- **Shift+F1** 用浏览器打开光标所在处元素的API文档（设置中搜索Browser配置默认浏览器）

- **Shift+鼠标滚轮** 水平滚动代码编辑区

- **Ctrl+Shift+Backspace** (Navigate | Last Edit Location) brings you back to the last place where you made changes in the code.

  Pressing Ctrl+Shift+Backspace a few times moves you deeper into your changes history.

- To quickly select the currently edited element (class, file, method or field) in any view (Project view, Structure view or other), press **Alt+F1**.

- **Ctrl+Shift+F7** 高亮光标处目标

- Use **Ctrl+Shift+F7** (Edit | Find | Highlight Usages in File) to quickly highlight usages of some variable in the current file.

  Use **F3** and **Shift+F3** keys to navigate through highlighted usages.

  Press **Esc** to remove highlighting.

- **Ctrl+Alt+H** 查看方法调用层次结构（光标要点选在方法名上）

- **Ctrl+H** 查看类的层次结构（光标要点选在类名上）

- **Ctrl+Alt+P** 提取局部变量到方法参数列表或重载方法

![提取参数](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/auto-extract-parameters.gif)

![重载方法](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/extract-parameters-by-overloading-method.gif)

- The **Esc** key in any tool window moves the focus to the editor.

  **Shift+Esc** moves the focus to the editor and also hides the current (or last active) tool window.

  The **F12** key moves the focus from the editor to the last focused tool window.

- 在 Android Studio **VCS** 菜单中点击 **Enable Version Control Integration**。



**DEBUG**

------

- 要启用内联调试，请在 **Debug** 窗口中点击 **Settings**，然后选中 **Show Values Inline** 复选框。

- 下断点
  - 临时断点 **Alt + 鼠标左键**
- **Shift + F7** Smart Step Into，Debug中一行代码包含多个方法调用时，选择其中一个方法进入。





## [开发者选项](https://developer.android.google.cn/studio/debug/dev-options)

### 绘图 > 显示布局边界

> 启用**显示布局边界**可以显示应用的裁剪边界、外边距和设备上的其他界面结构。



### 硬件加速渲染 > 调试GPU过度绘制

> 显示设备上的颜色编码，以便您可视化相同像素在同一帧中绘制的次数。可视化会显示您的应用可能在哪里进行了不必要的渲染。详情请参阅[可视化GPU过度绘制情况](https://developer.android.com/studio/profile/inspect-gpu-rendering.html#debug_overdraw)。



### 监控 > GPU呈现模式分析

> 依次点按**GPU渲染模式分析**和**在屏幕上显示为竖条**，以竖条形式显示GPU渲染模式分析。详情请参阅[GPU渲染模式分析](https://developer.android.com/studio/profile/inspect-gpu-rendering.html#profile_rendering)。