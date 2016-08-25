## android 权限判断那些事

由于各种原因我们的应用并没有使用运行时权限。

最近对应用权限相关的逻辑做了一些优化，以下为经验总结。

### 我们想达到什么目标？
- **准确**判断应用是否被允许某些权限（某个业务功能需要的权限）
- 在权限没有被允许时**按需**提示用户（dialog or toast，toast 用于拿不到 Activity 的情况）
- 弹框提醒用户设置权限时仅列出某个业务**没有被允许**的权限（如果有多个，一次性列出来）

### 我们关心的权限
- 位置信息
- 存储空间
- 照相
- 悬浮窗
- 通知

### 如何判断权限
判断权限的步骤

- 使用 [PermissionChecker](https://developer.android.com/reference/android/support/v4/content/PermissionChecker.html) 对权限进行判断（结果返回 true **不可靠**、返回 false **可靠**）
- 使用 [AppOpsManager](https://developer.android.com/reference/android/app/AppOpsManager.html) 对权限进行判断（这个和应用权限设置里面的开关是对应的，结果可靠，但有坑，需要用到**反射**）
- 使用 [NotificationManagerCompat](https://developer.android.com/reference/android/support/v4/app/NotificationManagerCompat.html) 判断用户是否允许通知

### 几个细节
- 如果仅仅是检查权限而不弹框提醒，那么在合适的时机对某项业务包含多个权限的情况可以 break 掉循环
- 使用 `AppOpsManager` 判断权限，如果手机没有某个权限的开关反射也会抛出异常，这时候需要判断异常的 cause，比如使用 [jOOR](https://github.com/jOOQ/jOOR)，最上面一层是 ReflectException，里面的 cause 是 InvocationTargetException（只要反射出问题都会抛），再往里就是真正的出错原因了
	- IllegalArgumentException 这个在某些手机设置里没有某个权限开关的时候会抛出，这种情况我们认为权限是被允许的（因为没有开关让用户改啊）
	- SecurityException 这个就是我们想要的，在 `checkOp` 函数中发现权限没有被允许就会抛出

### 坑
- 原生 Android 6.0系统（如 Nexus 5x）默认禁用悬浮窗权限，但没有地方打开，这种情况就算例外吧，直接 continue 循环

### 技术点
- jOOR 一行代码反射
```
  int mode = Reflect.on(object).call("checkOp", op, uid, packageName).get();
```
- 判断 Context 是不是 Activity
	- 简单判断 instanceOf Activity ？
- 去应用详情页
	- 需要 FLAG\_ACTIVITY\_NEW\_TASK 吗？为什么？
	- resolveActivity