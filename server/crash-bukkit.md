## Bukkit(插件服) 崩溃相关

### 开始前, 我们先看一个例子:

```
---- Minecraft Crash Report ----
Description: Watching Server
java.lang.Error:Watchdog
... ...
```

### 报错原因：

这是由于MC原版自带的 watchdog 机制，在滞后/卡顿超过 server.properties 所规定的时间后(默认60000ms)，认为服务端已崩溃，从而结束进程。

### 解决办法：

将 `server.properties` 中的 `max-tick-time` 改为更大的数值或-1(不会完美解决问题，如频繁出现内存溢出或卡顿问题，应考虑升级您的配置。)

**如果您能立刻看出崩溃原因, 那么请继续>v<**

## 1. 数据包加载失败

**看这看这!!! [崩溃报告](https://paste.gg/p/anonymous/cf7d059d9d994103b887e060289ed6d7)**

### 崩溃原因:

多发于删除地形生成类数据包后或仅level.dat被删除

### 解决方案:

数据包删除崩溃: 重新装回数据包, 或使用NBT编辑器将所有相关数据全部删除

level.dat删除崩溃: 检查是否还有level.dat_old文件存在, 如果有, 可将dat_old后缀改为dat。如果没有, 尝试重新生成存档并将数据覆盖回去

(安装数据包请三思)

## 2. 无法加载世界存档

崩溃报告切片:

```
java.lang.RuntimeException: Server attempted to load chunk saved with newer version of minecraft! 3337 > 3218
// 这是一个使用 Paper 1.19.4 (3337) 加载 1.19.3 (3218) 世界存档的示例。
```

### 崩溃原因:

服务端尝试加载较新版本的存档

### 解决方案:

更新服务端核心版本(关于dataVersion数据版本, 请查看[Minecraft Wiki](https://minecraft.fandom.com/zh/wiki/%E6%95%B0%E6%8D%AE%E7%89%88%E6%9C%AC#%E6%95%B0%E6%8D%AE%E7%89%88%E6%9C%AC%E5%88%97%E8%A1%A8))



