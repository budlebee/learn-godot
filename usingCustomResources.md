고도엔진의 Resource 클래스는 데이터를 저장하고 처리하는 유용한 도구임. animations, collision shapes, images 등 여러가지가 Resource 의 확장 타입임.

고도에 내장된 Resource 타입뿐만 아니라 자신만의 커스텀 Resource 타입을 만들어서 사용할 수 있음. 

## Example : Player Health

플랫포머 게임에서 플레이어의 체력 클래스를 만들것임.

- 플레이어가 장애물에 닿으면 데미지를 입음
- 적이 플레이어와 닿거나 발사한 총알에 닿으면 데미지를 입음
- 어떤 오브젝트를 잡거나 특정 위치에 서면 체력이 회복됨
- 플레이어의 체력과 체력변화를 UI 가 표시해줌.

스크립트 탭에서, File > New Script 를 택하고 이름을 PlayerHealth.gd 로 지어준다.

스크립트 맨 윗줄에선 Resource 클래스를 상속한다고 선언한다.

export 로 선언한 변수는 인스펙터에서 수정할 수 있다.

```
extends Resource
class_name PlayerHealth

signal health_changed

export (int) var max_value
var current_value = 0

func reset():
    current_value = max_value

func take_damage(amout):
    current_value = max(0, current_value-amount)
    emit_signal("health_changed",current_value)

func heal(amount):
    current_value = min(max_value, current_value+amount)
    emit_signal("health_changed",current_value)
```

PlayerHealth 클래스가 정의됐다면, 인스펙터 위에 New Resource 버튼을 눌러 새로운 인스턴스를 만들자.

### 리소스 사용하기

우리가 만든 리소스와 상호작용할 오브젝트들은 다음과 같다.

- 플레이어 : KinematicBody2D
- UI : ProgressTexture. 체력을 표시한다.
- 회복존 : Area2D. 서있으면 회복된다.
- 가시 : TileMap. 닿으면 데미지를 입는다.

플레이어 스크립트에는 다음과 같이 작성한다.
```
export (Resource) var health

func _ready():
    health.reset()

func hurt(amount):
    health.take_damage(amount)
```

회복존 스크립트에는 다음과 같이 작성한다.

```
func _physics_process(delta):
    for body in get_overlapping_bodies():
        if "health" in body:
            body.health.heal(heal_rate * delta)
```

UI 스크립트에는 health_changed 시그널과 연결하는 코드를 작성한다.

```
export (Resource) var player_health

func _ready():
    if player_health:
        player_health.connect("health_changed",self,"_on_player_health_changed")

func _on_player_health_changed(value):
    healthbar.value = float(value)/player_health.max_value * 100
```