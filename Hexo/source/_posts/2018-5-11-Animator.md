---
title: Patrol
date: 2018-5-10
categories: 3DGames
tags: 博客
---
# 巡逻兵
 
## 游戏需求，游戏规则
* 游戏规则   
使用WSAD或方向键上下左右移动player，进入巡逻兵的追捕后逃脱可积累一分，若与巡逻兵碰撞则游戏结束，收集完地图上的所有水晶即可获胜。

* 游戏设计要求  
创建一个地图和若干巡逻兵(使用动画)；
每个巡逻兵走一个3~5个边的凸多边型，位置数据是相对地址。即每次确定下一个目标位置，用自己当前位置为原点计算；
巡逻兵碰撞到障碍物，则会自动选下一个点为目标；
巡逻兵在设定范围内感知到玩家，会自动追击玩家；
失去玩家目标后，继续巡逻；
计分：玩家每次甩掉一个巡逻兵计一分，与巡逻兵碰撞游戏结束；
* 程序设计要求  
必须使用订阅与发布模式传消息
工厂模式生产巡逻兵

## UML图：
![运行结果](/img/blog/5-11.bmp)

## 视频：
[视频](http://www.iqiyi.com/w_19rxtorsoh.html)

## Patrol
* PatrolFactory：巡逻兵工厂，创建3个巡逻兵，设置巡逻兵的初始动画，位置，方向。在游戏结束时将所有对象清除。
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PatroFactory : MonoBehaviour {

	public 	GameObject patrolPrefab;
	//bao cun shiyong de xunluozhe
	//bao cun weishiyong de xunluozhe
	private List<GameObject> used = new List<GameObject>();
	private List<PatrolData> wait = new List<PatrolData>();
	private Vector3[] vector = new Vector3[3];
	public FirstSceneController sceneControler;
	//chushihua
	private void Awake(){
		Debug.Log (patrolPrefab);
	}



	//dedao xunluozhe ziyuan
	public List<GameObject> GetPatrol(){

		int[] pos_x = { 10, 8, -5 };
		int[] pos_y =  { 6, 3, -7};
		for (int i = 0; i < 3; i++) {
			vector[i] = new Vector3 (pos_x [i], 0, pos_y [i]);
			patrolPrefab = GameObject.Instantiate<GameObject> (Resources.Load<GameObject>("Prefabs/Patrol"), Vector3.zero, Quaternion.identity);
			patrolPrefab.transform.position = vector [i];
			patrolPrefab.GetComponent<PatrolData> ().sign = i + 1;
			patrolPrefab.GetComponent<PatrolData> ().position = vector [i];
			used.Add (patrolPrefab);
		}
			
		return used;
	}
	public void RemoveAllAnimator(){
		for (int i = 0; i > used.Count; i++) {
			used [i].gameObject.transform.GetComponent<Animator> ().SetBool ("walk", false);
		}
	
	}

	public void remove(){
		for (int i = 0; i > used.Count; i++) {
			used.Remove (used [i]);
		}
	}
}

```

* PatrolActionManager ：管理Patrol 的动作，基于SSActionManager。让巡逻兵开始巡逻，在遇到玩家时，可以追向玩家。游戏结束时能够停止。
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PatrolActionManager : SSActionManager {

	private GoPatrolAction go;
	public void GoPatrol(GameObject patrol){
		go = GoPatrolAction.GetSSAction (patrol.transform.position);
		this.RunAction (patrol, go, this);
	}

	public void DestroyAllAction(){
		DestoryAll ();
	}
}

```

* GoPatrolAction: 基于SSAction ，提供巡逻兵巡逻的动作，能够实现凸多边形的巡逻。
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GoPatrolAction :SSAction {
	private enum Direction
	{
		EAST,NORTH,WEST,SOUTH
	}
	private PatrolData PatrolData;

	private float speed = 1.0f;
	private Direction direction=Direction.NORTH;
	private float x, z;
	private float length;
	private bool canMove =true;

	private GoPatrolAction(){
		this.enable = true;
	}

	public static GoPatrolAction GetSSAction(Vector3 position){
		GoPatrolAction action = CreateInstance<GoPatrolAction> ();
		action.x = position.x;
		action.z = position.z;
		action.length = Random.Range (4, 7);
	
		return action;
	}


	public override void FixedUpdate(){
		if (transform.localEulerAngles.x != 0 || transform.localEulerAngles.z != 0)
		{
			transform.localEulerAngles = new Vector3(0, transform.localEulerAngles.y, 0);
		}            
		if (transform.position.y != 0)
		{
			transform.position = new Vector3(transform.position.x, 0, transform.position.z);
		}

		Go ();
		if (PatrolData.followedPlayer ) {

			this.destory = true;
			this.callback.SSActionEvent (this, 0, this.gameobject);
		}
	
	}

	public void Go(){
		if (canMove) {
			switch (direction) {
			case Direction.EAST:
				x += length;
				break;
			case Direction.NORTH:
				z += length;
				break;
			case Direction.WEST:
				x -= length;
				break;
			case Direction.SOUTH:
				z -= length;
				break;
			}
			canMove = false;
		}
		this.transform.LookAt (new Vector3(x,0,z));
		float distance = Vector3.Distance (this.transform.position,new Vector3(x,0,z) );
		if (distance > 0.9 ) {
			transform.position = Vector3.MoveTowards (this.transform.position, new Vector3(x,0,z),speed*Time.deltaTime);
		} else {
			direction++;
			if (direction > Direction.SOUTH) {
				direction = Direction.EAST;
			}
			canMove = true;
		}
	}
	public override  void Start(){
		this.gameobject.GetComponent<Animator>().SetBool("walk", true);
		PatrolData  = this.gameobject.GetComponent<PatrolData>();
		this.enable = true;
	}

}

```

* PatrolFollowAction:基于SSAction，提供巡逻兵跟踪的功能，能够跟随玩家，并获得加速效果。
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PatrolFollowAction : SSAction {

	private float speed = 1.2f;
	private GameObject player;
	private PatrolData patrolData;

	private PatrolFollowAction(){
		
	}
	public static PatrolFollowAction GetSSAction(GameObject player){
		PatrolFollowAction action = CreateInstance<PatrolFollowAction> ();
		action.player = player;

		return action;
	}

	public override void Start(){
		this.enable = true;
		patrolData = this.gameobject.GetComponent<PatrolData> ();
	}
	public override void FixedUpdate(){

		Follow ();
		if (!patrolData.followedPlayer) {
			this.destory = true;

			this.callback.SSActionEvent (this, 1, this.gameobject);
		}
	}

	public void Follow(){
		transform.position = Vector3.MoveTowards (this.transform.position, player.transform.position, speed * Time.deltaTime);
		transform.LookAt (player.transform.position);
	}
}

```

* GameEventManager： 游戏事件管理器，实现订阅模式的基础。使用委托功能。
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameEventManager : MonoBehaviour {

	public delegate void ScoreEvent();
	public static event ScoreEvent ScoreChange;

	public delegate void GameOverEvent();
	public static event GameOverEvent GameOverChange;


	public void PlayerEscape(){
		if(ScoreChange != null){
			ScoreChange();
		}
	
	}

	public void PlayerGameover(){
		if (GameOverChange != null) {
		
			GameOverChange();
		}
	}
}

```

* Patrolcollidle ： 碰撞的动作，以及产生的数据
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PatrolCollide : MonoBehaviour {

	void OnTriggerEnter(Collider collider){
		if (collider.gameObject.tag == "Player") {
			Debug.Log ("in");
			this.gameObject.transform.parent.GetComponent<PatrolData> ().followedPlayer = true;
			this.gameObject.transform.parent.GetComponent<PatrolData> ().player = collider.gameObject;
		}
	}

	void OnTriggerExit(Collider c){
		if (c.gameObject.tag == "Player") {
			Debug.Log ("out");
			this.gameObject.transform.parent.GetComponent<PatrolData> ().followedPlayer = false;
			this.gameObject.transform.parent.GetComponent<PatrolData> ().player = null;

		}
	}

	void OnCollisionEnter(Collision c){
		if (c.gameObject.tag == "Player") {
			Debug.Log ("death");
			Singleton<GameEventManager>.GetInstance.PlayerGameover();
		}
	}
}

```

完整项目[gihub](https://github.com/Tuid/3DGame)