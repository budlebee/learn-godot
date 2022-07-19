# Problem

게임 세션간의 로컬 데이터를 저장하고 불러오기

# Solution

고도엔진의 파일 입출력 시스템은 File 오브젝트에 기반하고 있다.

## 파일 열기

파일을 여는건 File 오브젝트를 생성하고 open() 함수를 호출하면 된다.

```
var file = File.new()
file.open("user://myfile.name", File.READ)
```

open 함수의 두번째 인자는 모드 플래그다. 

- File.READ - 읽기위해서 열기.
- File.WRITE - 작성하기 위해서 열기. 만약 파일이 존재하지 않는다면 생성한 다음 연다. 만약 파일이 존재한다면 truncate 한다.
- File.READ_WRITE - Open for reading and writing. Doesn’t truncate the file.
- File.WRITE_READ - Open for reading/writing. Creates the file if it doesn’t exist and truncates if it does.

## 파일 저장

파일저장은 store_float() 이나 store_string(), 또는 store_var() 를 쓴다. 고도엔진의 빌트인 시리얼라이저가 사용된다.

플레이어의 하이스코어를 저장하는 방식은 다음과 같다.

```
var score_file="user://score.save"

func save_score():
    var file = File.new()
    file.open(score_file, File.WRITE)
    file.store_var(highscore)
    file.close()
```

> 파일 접근이 끝나면 close() 함수를 쓰는 것을 잊지말자.

> 유저 데이터는 반드시 user:// 경로에 저장돼야 한다. res:// 경로는 프로젝트가 익스포트될때 읽기전용이 된다.

데이터를 게임 시작할때 불러오는건 다음과 같다.

```
func load_score():
    var file = File.new()
    if file.file_exists(score_file):
        file.open(score_file, File.READ)
        highscore = file.get_var()
        file.close()
    else:
        highscore = 0
```

불러올때는 존재성을 체크해주고, 없다면 디폴트 값을 사용하게 한다.

## object 저장하기

```
extends Node
class_name CustomObject

var a = 10

func say_hello():
    print("Hello")
```

```
var score_file="user://score.save"
var c = CustomObject.new()

func save_to_file():
    var file=File.new()
    file.open(score_file, File.WRITE)
    file.store_var(c,true)
    file.close()

func load_from_file():
    var file = File.new()
    if file.file_exists(score_file):
        file.open(score_file, File.READ)
        c = file.get_var(true)
        file.close()
```

```
var c

func _ready():
    load_from_file()
    c.say_hello()
```

## 데이터 저장용으로 JSON 은 쓰지마라

json 은 서로 호환되지 않는 시스템끼리 데이터를 주고 받기 위한 포맷이지, 무언가를 저장하기 위한 포맷이 아니다. 고도엔진이 json 을 지원하기는 하지만, json 은 int 나 float 같은 자료형을 구분해서 저장하지도 못하고 비효율적이다. json 쓰지말고 고도엔진의 빌트인 시리얼라이저를 써라.

## reference

https://kidscancode.org/godot_recipes/basics/file_io/