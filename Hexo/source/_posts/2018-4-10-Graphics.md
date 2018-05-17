---
title: Graphics
date: 2018-4-10
categories: 3DGames
tags: 博客
---

# Graphics and Gameobject
* 参考Fantasty Skybox Free 构建一个自己的游戏场景
    ![](/img/blog/graphics_1.png)
* 写一个简单的总结，总结游戏对象的使用，（个人认为总结如何使用工具中的游戏对象构建游戏场景）
    基础游戏对象：  
        * Empty：空游戏对象，不显示，经常作为挂载载体使用。
        * Camera : 摄像机，主要显示游戏世界，是观察游戏世界的窗口
        * Light ： 光线，游戏中光的来源
        * 3D 物体： 构建3D物体
        * Audio：游戏中的声音元素
        *  粒子特效等

 1.Camera ： 
   * create： menu->create->camera
   * 属性：Transform,Flare layer(使用炫光镜头，处理物体炫光效果)，GUI layer（渲染GUI页面），Audio Listener(挂载收音器)
    *特殊属性：游戏景深试图：
    透视视图（Perspective）：默认的视图
    正交视图（Orthographic）：垂直视图，将立体平面化
    Depth：主相机的Depth一般设置为-1，其余的摄像机的Depth一般大于零
    ClippingPlanes:表示摄像机的作用范围【near，far】
    Filed of view 视野范围（只能是透视视图时可选择）
![](/img/blog/graphics_2.png)

2.Skybox：  
  * create： Assets->create->material.  
    Inspector->shader->skybox->6sided.  
  将前后上下，左右的图片拖入相应的位置，最后完成制作，拖入项目
  Gameobject->addcomponent->rendering->skybox
 
 3.Light：  
 *  create：menu->create->Light
 * 特殊属性：Type：parallel(平行光，太阳光)，spot(聚光灯)，point（点光源）

3.渲染：  
 材质与着色器（Materials and Shaders）  
   * 纹理（Texture）：位图，表示物体本身的色彩。
   * 材质（Material）：包含一个或一组 Texture，以及元 数据（meta-data）属性，着色程序（Shader）。  
   * 其中 meta-data 定义了 Texture 与 mesh 的映射关系， 材料的光线吸收、透明度、反射与漫反射、折射、自发 光、眩光等特性   
   * Shader 是着色程序。它能利用显卡硬件渲染特性，按 meta-data 将材质按物体纹理、光线特性，结合游戏场 景中的光线生成用户感知的位图（像素点）

4.地形系统：
* create ： men->3d object->Terrain（地形设计工具）
* 特殊属性：第一行：造山，等高地形，平缓，刷地，种树，种草
      第二行：对应的属性，刷子。
      第三行：刷子的参数
*可以使用Assets Store 来使用别人开发好的集成包，包含各种游戏组件，比如树草等。
![](/img/blog/graphics_3.png)