---
title: 在Minecraft中使用openGL渲染出一个圆角矩形
date: 2023-04-16 16:43:34
tags: Minecraft
---
## 声明
本文章是观看某些Minecraft 模组源代码 发布的,并不是自己的想法,博主也是个skidder.
大家可以通过自己的想法实现

全部代码在文章最底部你可以选择复制粘贴
## 前提
你需要有

- 三角函数
- java基础
- openGL基础
- Minecraft 模组编写

## 准备环境

mcp forge fabric任选其一

## 开始编写
1. 首先你需要创建一个方法
```java
public static void drawRoundedRect(int xStart, float yStart, float xEnd, float yEnd, int radius, Color color)
```
2. 初始化处理

先进行一些初始化 将 左上,左下,右上,右下角的坐标进行处理
```java
float z = 0;
if (xStart > xEnd) {
    z = xStart;
    xStart = xEnd;
    xEnd = z;
}

if (yStart > yEnd) {
    z = yStart;
    yStart = yEnd;
    yEnd = z;
}
double left = xStart + radius;
double top = yStart + radius;
double right = xEnd - radius;
double bottom = yEnd - radius;
```
3. 渲染之前的准备
```java
//创建控制渲染的对象
Tessellator tessellator = Tessellator.getInstance();
WorldRenderer worldrenderer = tessellator.getWorldRenderer();
```
4. 开始渲染
```java
GlStateManager.enableBlend(); //启动混合
GlStateManager.disableTexture2D(); //关闭2D纹理模式
GlStateManager.tryBlendFuncSeparate(770, 771, 1, 0); //混合模式的类型
GlStateManager.color(color.getRed(), color.getGreen(), color.gerBlue(), color.getAlpha());// 圆角矩形颜色
worldrenderer.begin(GL_POLYGON, DefaultVertexFormats.POSITION);//开始多边形点位渲染
double degree = Math.PI / 180; //将 圆周率 转换为 弧度

//右下角
for (double i = 0; i <= 90; i += 1)
    worldrenderer.pos(right + Math.sin(i * degree) * radius, bottom + Math.cos(i * degree) * radius, 0.0D).endVertex();
//右上角
for (double i = 90; i <= 180; i += 1)
    worldrenderer.pos(right + Math.sin(i * degree) * radius, top + Math.cos(i * degree) * radius, 0.0D).endVertex();
//左上角
for (double i = 180; i <= 270; i += 1)
    worldrenderer.pos(left + Math.sin(i * degree) * radius, top + Math.cos(i * degree) * radius, 0.0D).endVertex();
//左下角
for (double i = 270; i <= 360; i += 1)
    worldrenderer.pos(left + Math.sin(i * degree) * radius, bottom + Math.cos(i * degree) * radius, 0.0D).endVertex();

tessellator.draw(); //渲染
GlStateManager.enableTexture2D();//开启2D纹理
GlStateManager.disableBlend();//关闭混合

```

通过以上方式,你就可以进行圆角矩形的渲染了

## 全部代码
```java
public static void drawRoundedRect(int xStart, float yStart, float xEnd, float yEnd, int radius, Color color){
    float z = 0;
    if (xStart > xEnd) {
        z = xStart;
        xStart = xEnd;
        xEnd = z;
    }

    if (yStart > yEnd) {
        z = yStart;
        yStart = yEnd;
        yEnd = z;
    }
    double left = xStart + radius;
    double top = yStart + radius;
    double right = xEnd - radius;
    double bottom = yEnd - radius;

    Tessellator tessellator = Tessellator.getInstance();
    WorldRenderer worldrenderer = tessellator.getWorldRenderer();

    GlStateManager.enableBlend(); //启动混合
    GlStateManager.disableTexture2D(); //关闭2D纹理模式
    GlStateManager.tryBlendFuncSeparate(770, 771, 1, 0); //混合模式的类型
    GlStateManager.color(color.getRed(), color.getGreen(), color.gerBlue(), color.getAlpha());// 圆角矩形颜色
    worldrenderer.begin(GL_POLYGON, DefaultVertexFormats.POSITION);//开始多边形点位渲染
    double degree = Math.PI / 180; //将 圆周率 转换为 弧度

    //右下角
    for (double i = 0; i <= 90; i += 1)
        worldrenderer.pos(right + Math.sin(i * degree) * radius, bottom + Math.cos(i * degree) * radius, 0.0D).endVertex();
    //右上角
    for (double i = 90; i <= 180; i += 1)
        worldrenderer.pos(right + Math.sin(i * degree) * radius, top + Math.cos(i * degree) * radius, 0.0D).endVertex();
    //左上角
    for (double i = 180; i <= 270; i += 1)
        worldrenderer.pos(left + Math.sin(i * degree) * radius, top + Math.cos(i * degree) * radius, 0.0D).endVertex();
    //左下角
    for (double i = 270; i <= 360; i += 1)
        worldrenderer.pos(left + Math.sin(i * degree) * radius, bottom + Math.cos(i * degree) * radius, 0.0D).endVertex();

    tessellator.draw(); //渲染
    GlStateManager.enableTexture2D();//开启2D纹理
    GlStateManager.disableBlend();//关闭混合
}
```

## 结束
感谢!!!
欢迎您下次再来