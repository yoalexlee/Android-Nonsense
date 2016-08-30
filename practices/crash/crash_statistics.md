## Crash 统计

用到现在发现最好用的还是 fabric 的 [Crashlytics](https://docs.fabric.io/android/crashlytics/overview.html)

### 优点
- 日活、在线人数和 crash 上报实时
- 自动上传 mapping 文件

### 缺点
- 时区和国内不同，统计数据上会有偏差

### 配置
- `apply plugin: 'io.fabric'`
- `classpath "io.fabric.tools:gradle:$rootProject.ext.dependency.fabricgradle"`
- `repository maven url 'https://maven.fabric.io/public'`
- `compile "com.crashlytics.sdk.android:crashlytics:$rootProject.ext.dependency.crashlytics"`
- debug compile 时通过 `ext.enableCrashlytics = false` 来提高打包速度

### 混淆
add

```
-keepattributes *Annotation*
-keepattributes SourceFile,LineNumberTable
-keep public class * extends java.lang.Exception
```

remove

```
-printmapping mapping.txt
```

### crash issue 处理
- close 关闭后如果在新版本出现，会 reopen
- lock 关闭后可加锁防止 reopen 比如一些无法修复且概率非常小的系统 crash

### 自定义字段
这些字段会显示在 crash 的详细信息中（面板右上方）

- setUserIdentifier
	- setUserName
	- setUserEmail
- setInt setBool 等等

网上看有人提出过 timezone 的需求，fabric 团队也说了在规划中，具体什么时候有这个功能就不知道了。