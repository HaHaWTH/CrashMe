## Forge/Fabric/Quilt(Mod端) 崩溃相关-通用

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

## 2. OptiFine 冲突

若看见了 `OptiFine ZIP file:`，说明您安装了 OptiFine，这一行后面是您的 OptiFine 路径

```
// 在示例中，您的 OptiFine 的路径为 C:\.minecraft\libraries\optifine\OptiFine\1.19.2_HD_U_I2\OptiFine-1.19.2_HD_U_I2.jar
OptiFine ZIP file: C:\.minecraft\libraries\optifine\OptiFine\1.19.2_HD_U_I2\OptiFine-1.19.2_HD_U_I2.jar

// 在示例中， Rubidium 发生了 Mixin 注入失败，您不能同时安装 Rubidium 和 OptiFine
Caused by: org.spongepowered.asm.mixin.injection.throwables.InjectionError: Critical injection failure: Redirector redirectFancyGraphicsVignette()Z in rubidium.mixins.json:features.options.MixinInGameHud failed injection check, (0/1) succeeded. Scanned 1 target(s). Using refmap rubidium-refmap.json
```

**~~Optifine 也干了~~**

注: 在现代的Minecraft版本中, 如果您想获得良好的Mod游玩体验, 请尽量避免安装Optifine, 可尝试以下模组, 具有更好的优化:

- (Forge/NeoForge/Fabric) [Embeddium](https://modrinth.com/mod/embeddium) (embeddet大神制作的稳定可靠的Sodium分支)
- (Fabric/Quilt) [Sodium](https://modrinth.com/mod/sodium) (卡密!)
- (Fabric/Quilt) [Nvidium](https://modrinth.com/mod/nvidium) (仅N卡可用的高性能渲染引擎, 不稳定)
- (Fabric/Quilt) [VulkanMod](https://modrinth.com/mod/vulkanmod) (为MC带来Vulkan! 不稳定)


## 3. 模组Mixin冲突

日志

```
Mixin apply for mod ommc-1_20_1 failed ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> net.minecraft.class_338: org.spongepowered.asm.mixin.injection.throwables.InvalidInjectionException @At("INVOKE") on net/minecraft/class_338::dontClearChatHistory with priority 1000 cannot inject into net/minecraft/class_338::method_1808(Z)V merged by coffee.waffle.dcch.mixin.DCCHMixin with priority 1000 [PREINJECT Applicator Phase -> ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> Prepare Injections ->  -> handler$fdd000$ommc-1_20_1$dontClearChatHistory(ZLorg/spongepowered/asm/mixin/injection/callback/CallbackInfo;)V -> Prepare]
org.spongepowered.asm.mixin.injection.throwables.InvalidInjectionException: @At("INVOKE") on net/minecraft/class_338::dontClearChatHistory with priority 1000 cannot inject into net/minecraft/class_338::method_1808(Z)V merged by coffee.waffle.dcch.mixin.DCCHMixin with priority 1000 [PREINJECT Applicator Phase -> ommc.mixins.json:feature.dontClearChatHistory.MixinChatHud from mod ommc-1_20_1 -> Prepare Injections ->  -> handler$fdd000$ommc-1_20_1$dontClearChatHistory(ZLorg/spongepowered/asm/mixin/injection/callback/CallbackInfo;)V -> Prepare]
```

解决方案：

删除掉其中任意一个模组