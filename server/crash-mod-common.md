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
