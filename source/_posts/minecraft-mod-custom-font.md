---
title: Minecraft Forge 自定义TTF或其他字体
date: 2024-02-07 16:11:32
tags:
- Minecraft
- Java
- Modding
---
# 前言
>在编写模组中，我们经常会使用到Minecraft自带的点阵字体渲染引擎 ，在1.16.5等高版本中，渲染字体通常是
```java
GuiComponet.drawString(poseStack,Minecraft.getInstance().font,"文本",x,y,color)
//或者
Minecraft.getInstance.font.draw(poseStack,"文本",x,y,color);
```
尽管Minecraft的字体和游戏整体画面很合适
但我们总会有疲劳的时候，想要更换一个新字体
使用材质包是一个不错的解决办法
但是,若是想在游戏中使用多种字体那该怎么办呢?

## 使用Minecraft的ttf字体渲染引擎

更改字体可以通过加入数据包的方式
我们开发模组也可以这么办

新建一个类 FontLoader

```java
public class FontLoader{
    //这里使用思源黑体
    public final ResourceLocation siyuanResource = new ResourceLocation("siyuan");
    public final Font siyuan;
    //加载字体
    public FontLoader(){
        siyuan = getFontRenderer(siyuanResource);
    }

    public Font getFontRenderer(ResourceLocation fontId) {
        //通过fontManager获取mc数据包目录下的字体文件
        //注意: 部分字段需要通过 accesstransformer 修改访问权限
        FontManager fontManager = Minecraft.getInstance().fontManager;
        return new Font((resourceLocation) ->
                fontManager.fontSets.getOrDefault(fontId, fontManager.missingFontSet)
        );
    }
}
```

```java
@Mod("mod")
public class Mod{
    
    public static FontLoader fontLoader;

    public Mod(){
        IEventBus modEventBus = FMLJavaModLoadingContext.get().getModEventBus();
        modEventBus.addListener(registerManager::clientSetup);
    }
   
    public void commonSetup(final FMLCommonSetupEvent event) {
        //在 FMLCommonSetupEvent 事件中去加载FontLoader
        event.enqueueWork(()->{
            Morota.fontLoader = new FontLoader();
        });
    }
}
```

这样就成功的初始化完成了

下面你只需要进行普通的渲染操作

## 绘制字体
```java
//在渲染事件中绘制,像平常渲染普通的点阵字体一样
//当然你也可以在FontLoader中定义更多字体
Mod.fontLoader.siyuan.draw(poseStack,"文本",x,y,color);
```

## 总结
这就是MC中渲染自定义字体的方法了
>通过间接使用使用数据包的方式,我们成功在MC中绘制了自定义的字体,体会MC的一些底层逻辑,更能让我们快速开发
## 感谢观看