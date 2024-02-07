---
title: Minecraft Forge 更新物品图标
date: 2024-02-07 16:11:32
tags:
- Minecraft
- Java
- Modding
---
## 需求
我有一个需求: 
想要通过使用一个物品之后修改该物品中能力的数据去实时更新物品的textures
例如: 一个瓶子装水,瓶子有一个水的能力,这个能力有着水的数据水的数量,通过每次饮用更改数据从而更改物品textures
## 思路
但是现在很棘手,若我通过创建多个相同物品但不同的材质,则会造成代码冗余并且写起来十分复杂
那么我们该怎么办呢?
在这之前我们需要了解一下 物品标签谓词 这个名词的含义 [Minecraft 中文 Wiki](https://minecraft.fandom.com/zh/wiki/%E6%A8%A1%E5%9E%8B#%E7%89%A9%E5%93%81%E6%A0%87%E7%AD%BE%E8%B0%93%E8%AF%8D)

简单来说这个物品标签谓词中存储着多个键值对,满足键所对应的数值则会替换模型(数值的范围在0-1之间)

我在之前写弓箭物品的时候,我们应该记得 ItemProperties.register() 这个方法
```java 
public static void register(Item pItem, ResourceLocation pName, ItemPropertyFunction pProperty)
```
他接受几个参数, 
1. 需要修改的物品
2. 一个路径: 这个路径是一个自定义的
3. 一个函数式接口(回调函数),并为我们提供了一些参数,我们需要提供一个返回值,这个返回值就是物品标签谓词
    ```java
        @Deprecated
        @OnlyIn(Dist.CLIENT)
        public interface ItemPropertyFunction {
        float call(ItemStack pStack, @Nullable ClientLevel pLevel, @Nullable LivingEntity pEntity, int pSeed);
        }
    ```
## 操作
我们添加 `bottle_0.json, bottle_0.png....` 这些文件
```
assets
    └─mod
      ├─models
      │  ├─block
      │  └─item
      │     └─bottle.json
      │       bottle_0.json
      │       bottle_1.json
      │       bottle_2.json
      │       bottle_3.json
      │       bottle_4.json
      │       bottle_5.json
      └─textures
          ├─block
          └─item
             └─bottle.png
               bottle_0.png
               bottle_1.png
               bottle_2.png
               bottle_3.png
               bottle_4.png
               bottle_5.png
```
先改bottle_0等 带数字的json文件
把parent 改为 `"mod:item/bottle"`
```json
{
  "parent": "mod:item/bottle",
  "textures": {
    "layer0": "mod:item/bottle_0"
  }
}
```

我们需要修改我们的物品bottle.json模型文件
> 修改前
```json
{
  "parent": "minecraft:item/generated",
  "textures": {
    "layer0": "mod:item/bottle"
  },
}
```
> 修改后
```json
{
  "parent": "minecraft:item/generated",
  "textures": {
    "layer0": "mod:item/bottle"
  },
  //请注意这里必须要按照顺序,由小到大 否则不起作用 mojang FW
  "overrides": [
    { "predicate": { "mod:item_energy": 0.1 }, "model": "mod:item/bottle_5" },
    { "predicate": { "mod:item_energy": 0.3 }, "model": "mod:item/bottle_4" },
    { "predicate": { "mod:item_energy": 0.5 }, "model": "mod:item/bottle_3" },
    { "predicate": { "mod:item_energy": 0.7 }, "model": "mod:item/bottle_2" },
    { "predicate": { "mod:item_energy": 0.9 }, "model": "mod:item/bottle_1" },
    { "predicate": { "mod:item_energy": 1   }, "model": "mod:item/bottle_0" }
  ]
  //错误示例
  /*
   *"overrides": [
   *     { "predicate": { "mod:item_energy": 1   }, "model": "mod:item/bottle_0" },
   *     { "predicate": { "mod:item_energy": 0.9 }, "model": "mod:item/bottle_1" },
   *     { "predicate": { "mod:item_energy": 0.7 }, "model": "mod:item/bottle_2" },
   *     { "predicate": { "mod:item_energy": 0.5 }, "model": "mod:item/bottle_3" },
   *     { "predicate": { "mod:item_energy": 0.3 }, "model": "mod:item/bottle_4" },
   *     { "predicate": { "mod:item_energy": 0.1 }, "model": "mod:item/bottle_5" }
   * ]
   */
}
```
我们添加了overrides,这是一个数组,其中包含着多个对象,每个对象按照特定的方式存储着数据.

>这个数组实际可以理解为存储着这个模型的所有子模型和判断方式
>而这个predicate中则是满足条件,当mod:item_energy的值为0.1时更新模型为 "mod:item/bottle_5"
```json
{ 
    "predicate": { 
        //需要满足的条件 mod:item_energy的值为0.1
        "mod:item_energy": 0.1 
    }, 
    //这是子模型的路径
    "model": "mod:item/bottle_5" 
}
```
下面我们编写自定义的ItemProperties
```java
public class ModItemProperties {
    public static void customItemTexture(){
        //我自己的物品
        Item item = ItemRegister.MOD_BOTTLE.get()
        //这个key 就是上面predicate中的键值对中的"键"
        ResourceLocation key = new ResourceLocation(MOD.MOD_ID, "item_energy")
        ItemProperties.register(item, key,(ClampedItemPropertyFunction) (itemStack, clientLevel, livingEntity, seed) -> {
            //这里需要一个返回值 就是上面predicate中的键值对中的"值"
            return DataManager.get();
        });
    }
}
```
在FMLClientSetupEvent事件中注册
```java
private void clientSetup(final FMLClientSetupEvent event)
{
    ModItemProperties.customItemTexture();
}
```
启动游戏就OK了

## 总结
* 我们学习了 `物品标签谓词Predicate` 简单理解了其含义
* 我们学会了从之前学过的知识中提取我们所需要的知识,例如本次通过bow的ItemProperties 举一反三
## 感谢观看