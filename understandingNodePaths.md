고도 엔진에서 가장 흔하게 보는 에러 Invalid get index 'position' (on base: 'null instance'). 

get_node("AnimatedSprite).play() 또는 $AnimatedSprite.play() 를 호출하면 AnimatedSprite 라는 이름을 가진 하위노드의 play() 함수를 호출함. 즉 get_node() 함수는 원하는 노드의 레퍼런스를 반환함.

get_node("../Main/UI/HealthBar").text = str(health)

노드 패스는 상대경로뿐만 아니라 절대경로로 선언할 수도 있음. root 노드부터 시작해서 선언해주면 됨.

get_node("/root/Main/Player)

그러나 노드패스를 씬트리에서 위쪽으로 타고올라가는 것은 하지 말것. 노드 트리 구조를 바뀌면 에러를 뿜을테니까. 그러니 시그널을 사용할 것.