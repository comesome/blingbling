> JavaFX 桌面应用 - 撤销和恢复功能的实现思路


需求：
- 支持撤销最近 10 次操作
- 支持恢复最近 10 次操作

思路：
监控主画布，将每一次添加和删除事件存到撤销队列中


场景：
- 画一个弯道需要9个元素

![](https://cdn.jsdelivr.net/gh/easterfan/picgo/blingbling/2020/20201009173200.png)