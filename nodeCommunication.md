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

## Groups 사용 예제

Groups 는 일종의 태깅 시스템임 (노드 레퍼런스들을 포함하고 있는 오브젝트나 어레이가 아니라). 비슷한 역할을 하는 애들을 `add_to_group()` 이나 `remove_from_group()` 함수를 이용해 동적으로 추가할 수 있음. 

SceneTree 는 지속적으로 태그들을 추적하고, 또 get_nodes_in_group() 같은 함수로 특정 태그의 노드들을 찾아올 수 있음.

갤러그 스타일의 스페이스 슈터 게임을 만든다 치자. 날아다니는 여러 종류의 적들이 있음. 여러 종류의 적들이 "스마트 폭탄" 기능이 활성화되면 전부 파괴되게끔 하고 싶음. 이런 경우 Groups 를 사용하면 편함.

먼저 모든 적들을 "enemies" 그룹에 추가하자.

```
func _ready():
    add_to_group("enemies")
```

모든 적들이 explode() 라는 함수를 이미 갖고 있다고 가정하자. 그럼 다음과 같은 코드를 써준다.

```
func activate_smart_bomb():
    get_tree().call_group("enemies","explode")
```

## owner 사용하기

해당 씬의 루트 노드는 오너를 갖고 있다. 오너는 복잡한 계층구조에서 자식노드가 시그널을 루트노드로 쉽게 보내는 방법을 제공한다.

복잡한 UI 구조를 생각해보자. 엄청나게 네스팅돼있는 계층구조에서, 유저가 버튼을 눌렀고, 그것이 시그널을 UI씬의 루트노드로 보내야 되는 상황을 생각하자.

CenterContainer
-VBoxContainer
--Button1
--Button2
--Button3

CentorContainer
```
extends CenterContainer

func _on_button_pressed(button_name):
    print(button_name, " was pressed")
```

Button1
```
extends Button

func _ready():
    connect("pressed", owner, "_on_button_pressed", [name])
```



## reference

https://kidscancode.org/godot_recipes/basics/node_communication/