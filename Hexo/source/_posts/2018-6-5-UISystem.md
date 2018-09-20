---
title: UI System
date: 2018-6-5
categories: 3DGames
tags: 博客
---
# UI System 血条的制作 
作业内容：
血条（Health Bar）的预制设计。具体要求如下
* 分别使用 IMGUI 和 UGUI 实现
* 使用 UGUI，血条是游戏对象的一个子元素，任何时候需要面对主摄像机
* 分析两种实现的优缺点
* 给出预制的使用方法

## IMGUI
IMGUI ：Immediate Mode GUI system (IMGUI).主要特点所有组件的位置都是相对于屏幕。
使用IMGUI 实现血条功能最主要使用的是水平滚动条组件HorizontalScrollar。
```
GUI.HorizontalScrollbar ( Rect position, float value,float size,float leftValue,float rightValue);
```

* value : 当前的值
* size ： 大小
* leftValue: 最左值（最小值）
* rightValue:最右值（最大值）
通过改变的value的值实现血条的增加减少。
首先创建一个空对象，并将创建一个script组件.
定义float value, Max，Min分别代表当前的数值，最大值，最小值。
定义GUISkin skin ，定义GUI的样式
```
void OnGUI ()
	{
		GUI.skin = skin;
		if (GUI.Button (new Rect (200, 100, 80, 30), "Increase")) {
			if(value< Max)
				value++;
		}
		if (GUI.Button (new Rect (200, 200, 80, 30), "Decrease")) {
			if(value>Min)
			value--;
		}
		GUI.skin = skin;
		//GUI.Box (new Rect (300, 200, 100, 30), "good");
		GUI.HorizontalScrollbar (new Rect (300, 200, 100, 200), 0, value, Min, Max);

	}
```
![](/img/blog/IMGUI1.png)
![](/img/blog/IMGUI2.png)
## UGUI
使用UGUI 实现血条功能最主要使用的是Slider。
* Slider
  * Background
  * Fill Area  
      *  Fill
  * Handle Slide Area
我们需要将Handle Slide Area 中的 Image 给去除，新创建BloodImage在Slider上，这个新的滑块就是显示血条的主要部分

```
protected override void OnRectTransformDimensionsChange()  
	{  
		base.OnRectTransformDimensionsChange();  

 
		if (BloodSlider == null)  
			BloodSlider = transform.parent.parent.GetComponent<Slider>();  

		if (BloodSlider != null)  
		{  

			float value = BloodSlider.value;  
			uvRect = new Rect(0,0,value,1);  
		}  
	}  
```
这样slider 的MaxValue 就是血条分成的块数。value 就是当前血量。

为了使血条任何时候需要面对主摄像机，所以将canvas 的Render Mode 设置成Screen space - Camera ,并选中主相机为 Rander Camera 。
![](/img/blog/UGUI1.png)
![](/img/blog/UGUI2.png)
![](/img/blog/UGUI3.png)
