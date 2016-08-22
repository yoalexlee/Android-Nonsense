## 动态模糊
- 有人用 java
- 有人用 renderscript
- 有人用 ndk

到底哪个好？没有最好，只有最适合。

我们项目里用 java，一个函数搞定模糊。

- 模糊算法放在新线程做
- renderscript v8 并非在所有手机上都正常运行（做了混淆处理）
- gradle skip 掉 renderscript 的 task 以提高构建速度