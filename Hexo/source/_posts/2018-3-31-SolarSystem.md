---
title: Solar System
date: 2018-3-31
categories: 3DGames
tags: 博客
---
# Solar System
太阳系
## 运动（抛物运动）
请用三种方法以上方法，实现物体的抛物线运动。（如，修改Transform属性，使用向量Vector3的方法…）
```
		//1。通过修改Transform.position移动物体，将gameobject赋予向右，向下的速度，使得gameobject 进行抛物线运动
		sun.position+=Vector3.right*speed*(time+=time);
		sun.position += Vector3.down * g * time * time;

		//2。使用public void Translate(Vector3 translation, Space relativeTo = Space.Self)方法，使得gameobject获得速度，改变位置
		moon.Translate(Vector3.right*speed*(time+=time),Space.World);
		moon.Translate (Vector3.down * speed * (time) * (time), Space.World);

		//3。先给gameobject一个水平的速度，在让gameobject绕中心点旋转。
		earth.Translate(Vector3.right*speed*time);
		earth.RotateAround(Vector3.zero,Vector3.down,speed);
```
## 太阳系　　
　　写一个程序，实现一个完整的太阳系， 其他星球围绕太阳的转速必须不一样，且不在一个法平面上。
![运行结果](/img/blog/3-31_1.png)

![运行结果](/img/blog/3-31_2.png)

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class galexly : MonoBehaviour {

	public Transform sun;//taiyang 
	public Transform mercury;//shuixin
	public Transform venus;//jinxin
	public Transform earth;//diqiu
	public Transform moon;//yueliang
	public Transform mars;//huoxin
	public Transform jupiter;//tuxin
	public Transform saturn;//muxin
	public Transform uranus;//tianwanxin
	public Transform neptune;//hiawangxin


	void Start () {

	}

	// Update is called once per frame
	void Update () {
		

		sun.position = Vector3.zero;
		sun.Rotate (Vector3.up*1*Time.deltaTime);

		Vector3 axis_1 = new Vector3 (2,3, 0);//fapinmian
		mercury.RotateAround (sun.position, axis_1, 200 * Time.deltaTime);
		mercury.Rotate (Vector3.right * 10 * Time.deltaTime);

		Vector3 axis_2 = new Vector3 (6,6, 0);
		venus.RotateAround (sun.position, axis_2, 100 * Time.deltaTime);
		venus.Rotate (Vector3.up*12*Time.deltaTime);

		Vector3 axis_3 = new Vector3 (0,1, 0);
		earth.RotateAround (sun.position, axis_3, 50 * Time.deltaTime);
		earth.Rotate (Vector3.up * 30 * Time.deltaTime);
		moon.RotateAround (earth.position,Vector3.up ,20*Time.deltaTime);

		Vector3 axis_4 = new Vector3 (1,0, 0);
		mars.RotateAround (sun.position, axis_4, 15 * Time.deltaTime);
	

		Vector3 axis_5 = new Vector3 (1,3, 0);
		jupiter.RotateAround (sun.position, axis_5, 11 * Time.deltaTime);
	

		Vector3 axis_6 = new Vector3 (2,1, 0);
		saturn.RotateAround (sun.position, axis_6, 6 * Time.deltaTime);
	

		Vector3 axis_7 = new Vector3 (2,3, 0);
		uranus.RotateAround (sun.position, axis_7, 3 * Time.deltaTime);
	

		Vector3 axis_8 = new Vector3 (2,3, 0);
		neptune.RotateAround (sun.position, axis_8, 1 * Time.deltaTime);

	}
}


```