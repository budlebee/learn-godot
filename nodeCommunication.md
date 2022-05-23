# godot 에서 씬과 노드끼리 제대로 호출하는법

일반적으로 부모노드가 자식노드를 관리하는게 원칙. get_parent()나 get_node() 쓰면 코드가 개판나기 쉽다. 

위 두가지 함수쓰면 다음같은 문제가 있음.

1. 각 씬을 개별로 테스팅하기 힘듬

2. 트리 순서를 바꾸거나 새로 디자인하면 노드 호출하는 코드가 유효하지 않게됨

3. 레디 오더는 칠드런이 먼저인데, 부모노드에 접근하다보면 꼬일수 있음. 준비되지 않은걸 호출하려는 등.

노드 커뮤니케이션의 황금률은 "호출은 아래로, 신호는 위로".

노드가 자식노드를 호출할때 get_node()쓰는건 적절함.

노드가 자기 위쪽이랑 소통하고 싶으면 시그널을 써라.

## calling down 예제

Player
-AnimatedSprite
-CollisionShape2D

플레이어 노드에서 AnimatedSprite 노드를 호출하려 get_node()를 쓰면 잘 동작함.

```
extends KinematicBody2D

func _process(delta):
    if speed > 0 :
        get_node("AnimatedSprite").play("run")
```

gdscript 에선 get_node()를 $ 심볼로 짧게 쓸 수 있음. $AnimatedSprite

## signal up 예제

내가 어떤 상위노드에 있는 함수를 이용하고 싶다면, 해당 노드와 상위노드의 공통 부모 노드에 시그널을 보내고, 공통 부모노드가 원하는 노드와 시그널을 커넥트 하도록 한다.

플레이어의 체력 변화를 UI 에 반영한다고 하자. 노드 트리는 다음과 같다.

World
-Player
-UI
--VBoxContainer
---HBoxContainer
----TextureRect
----Label

이 경우 UI 는 플레이어의 하위노드가 아니므로 플레이어 노드를 바로 호출하면 안된다. 

대신 emit_signal("health_changed",health) 라는 시그널을 보내고, 플레이어와 UI 의 공통 부모노드인 World 노드에서 

```
func _ready():
    $Player.connect("health_changed",$UI,"update_health")
```
이렇게 $UI 로부터 온 health_changed 라는 시그널을 $Player 의 "update_health" 함수와 연결한다.

UI 노드에 스크립트는 다음과 같다.

```
onready var label = $VBoxContainer/HBoxContainer/Label

func update_health(value):
    label.text = str(value)
```


## reference

https://kidscancode.org/godot_recipes/basics/node_communication/