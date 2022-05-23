# godot 엔진의 tree order 이해하기

일반적으로 트리 구조안의 노드들은 탑다운 방식으로 처리된다. 루트부터 시작한뒤 각 아래쪽 브랜치로 나아가는 식.

각 노드에 붙어있는 스크립트 함수들 설명

func _init(): 오브젝트가 처음 만들어질때 호출됨.

func enter_tree(): 노드가 처음 트리로 들어올때 호출됨. 

func _ready(): 해당 노드와 칠드런 노드들이 전부 트리에 들어오고 준비되면 호출됨

func _process(): 트리에 있는 모든 노드가 매 프레임마다 호출되는 함수.

testRoot
-testChild1
--testChild3
-testChild2

이런 트리구조라면, 해당 함수는 다음과 같이 호출됨.

testRoot init / testChild1 init / testChild3 init / testChild2 init

testRoot enter tree / testChild1 enter tree / testChild3 enter tree / testChild2 enter tree

testChild3 ready / testChild1 ready / testChild2 ready / testRoot ready

testRoot process / testChild1 process / testChild3 process / testChild2 process

트리구조를 탑다운 형식으로 훑어가며 호출된다는것을 확인할 수 있음.

그러니 일반적으로 부모노드가 칠드런 노드를 관리해야지, 그 반대로 하면 안됨. 부모노드는 자식노드의 모든 데이터에 접근 가능함. 

