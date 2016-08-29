## logcat

本地打印调试日志最重要的就是轻量化。

- 区分 **debug** 和 **release**
- 底层使用 [Timber](https://github.com/JakeWharton/timber) 
- 封装一个新的类叫 `JLog`（对，一个类就够了， Timber Tree 使用内部类）用来作对外调用
- 如果你要封装多个类，就新建1个包
- 日志的末尾加入文件名和方法数，方便快速定位

够用就行。