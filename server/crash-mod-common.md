## Forge/Fabric/Quilt(Mod服) 崩溃相关-通用

**本部分主要针对各个Mod加载器通用的崩溃及解决方案(大多数为Mixin)**

## Mixin 是什么？

> [SpongePowered Mixin](https://github.com/SpongePowered/Mixin) (下称 Mixin) 是一套用于在程序运行时通过代码注入实现对程序行为进行修改的一套支持库  
> Mixin is a trait/mixin and bytecode weaving framework for Java using ASM  
> SpongePowered Mixin 在各个 Mod 加载器中都有广泛应用

## BYD, 我怎么知道我的Mod加载器有没有Mixin?

在日志中查找 `SpongePowered MIXIN Subsystem` 这段文字，如果找到说明您的 Mod 加载器中使用了 Mixin ~~(海绵充电 混入子系统)~~

## 1. Mixin注入失败(英杰克特•菲尔德)

解释：由于一个或多个 Mod 的 Mixin 注入失败，导致游戏出错。

解决方案：查看日志了解是哪一个或多个 Mod 出错，然后移除它们。

关键词：

```
Mixin apply for mod XXX
failed
```

## 2. 模组Mixin冲突

日志

```
Mixin apply for mod ommc-1_20_1 failed ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> net.minecraft.class_338: org.spongepowered.asm.mixin.injection.throwables.InvalidInjectionException @At("INVOKE") on net/minecraft/class_338::dontClearChatHistory with priority 1000 cannot inject into net/minecraft/class_338::method_1808(Z)V merged by coffee.waffle.dcch.mixin.DCCHMixin with priority 1000 [PREINJECT Applicator Phase -> ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> Prepare Injections ->  -> handler$fdd000$ommc-1_20_1$dontClearChatHistory(ZLorg/spongepowered/asm/mixin/injection/callback/CallbackInfo;)V -> Prepare]
org.spongepowered.asm.mixin.injection.throwables.InvalidInjectionException: @At("INVOKE") on net/minecraft/class_338::dontClearChatHistory with priority 1000 cannot inject into net/minecraft/class_338::method_1808(Z)V merged by coffee.waffle.dcch.mixin.DCCHMixin with priority 1000 [PREINJECT Applicator Phase -> ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> Prepare Injections ->  -> handler$fdd000$ommc-1_20_1$dontClearChatHistory(ZLorg/spongepowered/asm/mixin/injection/callback/CallbackInfo;)V -> Prepare]
```

解决方案：

删除掉其中任意一个模组

## 案例1: Mixin的侵入式修改导致崩溃

在本案例中, 服务端软件为**CatServer 1.16.5**, 混合端通常不会受到模组作者的支持, 因此使用风险请自行承担

崩溃日志: [点击前往](https://paste.gg/p/anonymous/fea245eca0e644d09865284281685006)

首先来到第5行, 看到`Exception in server tick loop`, 这表明服务器在进行tick循环时出现了错误. 第7行指明了是在生成新区块时产生的异常, 为什么呢? 让我们继续往下看: 
```
Caused by: java.lang.NullPointerException: Cannot store to object array because "this.field_227054_f_" is null
```

这段表明了一切问题的来源都是因为这个空指针异常, 这个field(字段)是什么在这里并不重要, 接着往下看第一行at:

```
at net.minecraft.world.biome.BiomeContainer.setBiome(BiomeContainer.java:110) ~[?:?] {re:mixin,pl:runtimedistcleaner:A,re:classloading,pl:mixin:APP:modernfix-common.mixins.json:perf.compress_biome_container.MixinBiomeContainer,pl:mixin:A,pl:runtimedistcleaner:}
```

有没有注意到什么? 没注意到也没关系, 大括号内的mixin中出现了`modernfix`. 到这基本就可以确定是ModernFix中的mixin引起的崩溃了.

通过对照混淆映射表可以得知, `field_227054_f_`的真面目是

```java
private final Biome[] biomes; // Mojang mapping
```

移除ModernFix后, 成功解决了问题