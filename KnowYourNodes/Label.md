Label 은 텍스트를 표시하기 위한 Control 노드다. 

## Node properties

많이 쓰이는 프로퍼티 몇개를 꼽으면 다음과 같다.

- text : 말 그대로 텍스트. 만약 숫자를 표시하고 싶다면, str 함수로 변환해주는것을 잊지마라.

```
func update_label(value):
    $Label.text = str(value)
```

- align : 텍스트의 오른쪽, 왼쪽, 중앙 정렬용

- percent_visible : 텍스트가 얼만큼 보이는지에 대한 것. 0.5 로 돼있으면 텍스트가 절반만 보인다. 이 프로퍼티를 이용해 "타자기 효과" 를 연출할 수 있다.


## 폰트 추가하기

TTF 나 OTF 파일을 프로젝트 폴더에 추가한다.

Label 의 프로퍼티중 Custom Fonts 에서 New DynamicFont 를 선택하고, Font/Font Data 에서 Load 를 눌러 원하는 폰트파일을 선택한다. 폰트의 사이즈도 설정해야한다.

