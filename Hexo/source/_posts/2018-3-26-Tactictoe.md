---
title: Tactictoe
date: 2018-3-26
categories: 3DGames
tags: 博客
---
# 井字棋
---

## 开发环境  
   使用Unity3D 作为开发软件。[Unity3D官网](https://unity3d.com) ，根据官网描述“
Unity作为全球领先的游戏行业软件,Unity对蓬勃发展的全球游戏市场至关重要。相比其它游戏开发技术，更多游戏使用Unity引擎制作。使用Unity制作的游戏拥有更多玩家，也有更多的开发者依赖Unity引擎与服务来开展业务。每天有上千万开发者使用Unity进行创作。2016年第一季度，全球范围内使用Unity制作的游戏被安装到接近20亿独立设备上。”  对于初学者来说使用Unity3D来作为游戏编程的叩门选择还是很不错的。Unity 同时也提供了对于初学者很友好的官方教程（官网就有）以及比较推荐[官方文档](https://docs.unity3d.com/Manual/index.html)

## Tictactoe
  游戏内容：井字棋（一种两人对战游戏，双方使用不同的标记在一个3*3的九宫格内标记属于自已的位置，直到有一方的三个标记点能够连接成一条线：横线，竖线，斜线）。  
  游戏技术限制： 只能使用IMGUI构建UI（IMGUI：Immediate Mode GUI，立即显示的GUI），只能以OnGUI（）函数为主体构建游戏。[IMGUI的简介](https://docs.unity3d.com/Manual/GUIScriptingGuide.html)


首先新建一个GameObject ，并且对将其命名为“Tictactoe”，对这个游戏对象添加script组件，命名为“Tictactoe”。打开C#文件。首先声明一个二维 int 数组mice。这个二维数组就是控制九宫格状态的数据。这个mice将有三种值：0（代表最初状态，即空白状态）、1（代表第一位玩家的标记值）、2（代表第二位为玩家的标记值）。

  使用OnGU的方法来动态生成每一帧的九宫格的按钮，IMGUI 提供了多种控件。  
  首先是Button控件：` GUI.Button(position , string); `  
  例如：` GUI.Button (new Rect (50,50, 50, 50), ""); `就是在X:50,Y:50的地方创建一个长宽都为50的Button，Button中的Content为“”。
  使用Label控件显示欢迎信息和游戏结果。`GUI.Label(position,string);`  
  例如：`GUI.Label(new Rect(100,50,50,50),"welcome");`就是在X:100,Y50的地方创建一个长宽都为50的Label，Label中的Content为“welcome”。  

  创建九宫格，在OnGUI（）函数中
```
for (i = 0; i < 3; i++) {
			for (j = 0; j < 3; j++) {

				if (mice [i, j] == 1) {
					GUI.Button (new Rect (200 + i * 50, 200 + j * 50, 50, 50), "1");
				}
				if (mice [i, j] == 2) {
					GUI.Button (new Rect (200 + i * 50, 200 + j * 50, 50, 50), "2");
			}
		}
}
```

GameOver的判断，即有一行，或一列，或斜线被标记，不为原状态。
```
int GameOver(){
		for (int i = 0; i < 3; i++) {
			if (mice [i, 0] != 0 && mice [i, 1]==mice [i, 2] && mice[i,0] ==mice [i, 2])
				return mice[i,0];
		}
		for (int j = 0; j < 3; j++) {
			if (mice [0, j] != 0 && mice [0, j] == mice [1, j] && mice [0, j] == mice [2, j])
				return mice [0, j];
		}
		if (mice [0, 0] != 0 && mice [0, 0] == mice [1, 1] && mice [0, 0] == mice [2, 2] || mice [0, 2] != 0 && mice [0, 2] == mice [1, 1] && mice [0, 2] == mice [2, 0])
			return mice[1,1];
		return 0;
	}
```
GameOver()函数返回值有三个0（代表游戏没有结束），1（代表一号玩家获胜且游戏结束），2（代表二号玩家获胜且游戏结束）。

GameOver（）的返回值来控制下一步该是谁来游戏，或者游戏结束。
```
if (Result == 0 ) {
   if (Turn == 1)
    	mice [i, j] = 1;
	  else
    	mice [i, j] = 2;
	  Turn = -Turn;
}
```
