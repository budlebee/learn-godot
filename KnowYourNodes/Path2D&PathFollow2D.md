path 는 Curve2D 리소스로 정의된다. Path2D 노드는 에디터에서 새로운 패스를 만들 수 있게 한다.

## Curve2D

- add_point() / remove_point() / clear_points()

포인트를 코드로 제어하기 위한 함수들.

- get_closest_point()

주어진 점에서 가장 가까운 점을 반환하는 함수

- get_closest_offset()

해당 점에서 가장 가까운 좌표를 반환함. 대개의 경우 패스위에 정의된 두개의 점 사이를 반환하는듯

- tesselate()

곡선을 따르는 점들의 리스트를 반환함. 곡률이 높은 곳일수록 높은 밀도임.

## 패스 그리기

Path2D 노드를 선택하면 아이콘 바에 몇가지 새로운 아이콘이 나타난다.

- Select Points : 존재하는 노드를 클릭하고 드래그해서 움직일 수 있음
- Select Control Points : 점을 드래그해서 곡선을 만든다.
- Add Point : 빈 공간에 클릭하면 점을 추가한다
- Delete Point : 클릭한 점을 지운다
- Close Curve : 마지막 점과 첫 점을 연결해서 고리를 만든다.

## 패스 따라가기

패스 그자체로는 아무런 기능이 없다. PathFollow2D 노드를 사용하면 Path2D 패스를 따라 움직이게 만들 수 있다.

- offset / unit_offset : 해당 지점이 패스의 시작점으로 부터 얼마나 떨어졌는지 나타내는 프로퍼티. offset 은 픽셀단위고 unit_offset 은 0부터 1까지인 비율단위이다.

- rotate : 이 불리언 프로퍼티는 노드가 패스를 따라 움직일때 돌아야되는지 아닌지를 결정한다. true 면 패스를 따라서 돌때 스프라이트의 머리부분도 길 따라 같이 회전하는 듯.

- loop : 이 불리언 프로퍼티가 true 면, 오프셋이 패스길이보다 길때 wrap around 한다. 반복적인 패스를 만들고 싶다면 사용해라.

패스를 디버깅에서 보고싶다면 setting 에서 Debug>Visible Navigation 을 설정해라.

PathFollow2D 의 자식 Sprite 코드를 다음과 같이 작성하면
```
func _process(delta):
    $Path2D/PathFollow2D.offset += 250 * delta
```
offset 이 증가함에 따라 노드는 패스를 따라 움직인다.

## 길따라 움직이는 AI 만들기

```
func get_path_direction(pos):
    var offset = $Path2D.curve.get_closest_offset(pos)
    $Path2D/PathFollow2D.offset = offset
    return $Path2D/PathFollow2D.transform.x
```