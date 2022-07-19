프레임 레이트와 독립적인 움직임을 만들기 위해 쓰이는 delta 개념을 이해하자.

만약 화면이 600픽셀이고 화면을 가로지르는데 5초가 걸리게 하고 싶다면, 120픽셀/s 의 속도를 가져야 될것이다.

그리고 게임이 60프레임이라면, 1/60 s/frame 이므로, 120픽셀/s * 1/60 s/frame = 2 픽셀/frame 이 나온다.

하지만 이런방식이면 프레임이 바뀌면 속도도 들쭉날쭉 해지는 문제가 생긴다.

_process() 함수는 자동적으로 delta 라 불리는 파라미터를 가지는데, 이전프레임부터 다음 프레임까지 걸리는 시간/프레임 단위의 값이다. 그럼 픽셀/시간 값에 delta 를 곱해주면 픽셀/시간 * 시간/프레임 = 픽셀/프레임 값을 얻을 수 있다.

```
extends Node2D

# 원하는 pixels/second
var movement = Vector2(120,0)

func _process(delta):
    $Sprite.position += movement *delta
```

즉 픽셀/시간으로 게임을 설계하고 식을 세우고, 거기에 시간/프레임인 delta 를 곱해주기만 하면 게임 프레임에 무관하게 물체의 움직임을 생각한대로 만들 수 있다.

move_and_slide() 같은 kinematic function 들은 인자로 받은 vector 에 자동적으로 delta 를 적용하는 것에 유의.

```
# Sprite movement code:
velocity += gravity * delta
position += velocity * delta

# Kinematic body movement code:
velocity += gravity * delta
velocity = move_and_slide(velocity)
```