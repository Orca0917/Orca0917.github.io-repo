---
title:  "[부스트캠프 Pre-Course] Python OOP"
excerpt: "Python Object Oriented Programming"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python]
toc: true
toc_sticky: true
 
date: 2021-12-04
last_modified_at: 2021-12-04
---

# <span style = "color: #00adb5">객체지향 프로그래밍 개요</span>
Object-Oriented Programming, OOP
객체: 실생활에서 일종의 물건. 속성(Attribute)와 행동(Action)을 가진다.
OOP는 이러한 객체 개념을 프로그램으로 표현한다. 속성은 변수(variable), 행동은 함수(method)로 표현된다.
python도 역시 객체 지향 프로그래밍 언어이다.

예를 들어서, 인공지능 축구 프로그램을 작성한다고 가정하면 객체, 행동, 속성은 다음과 같이 표현할 수 있다.
- 객체: `팀`, `선수`, `심판`, `공`
- 행동: `선수 : 공을 차다, 패스하다`, `심판 : 휘슬을 불다, 경고를 주다`
- 속성: `선수 : 선수이름, 포지션, 소속팀`, `팀: 팀이름, 팀소속 선수`

OOP는 설계도에 해당하는 `클래스`와 실제 구현체인 `인스턴스`로 나뉘어진다.
ex. 붕어빵 틀: 클래스 / 붕어빵: 인스턴스

<br>

# <span style = "color: #00adb5">class 구현하기 in Python</span>
축구 선수 정보를 Class로 구현하기
```python
class SoccerPlayer(object):
    def __init__(self, name, position, back_number):
        self.name = name
        self.position = position
        self.back_number = back_number
    
    def change_back_number(self, new_number):
        print("선수의 등번호를 변경합니다 : From %d to %d" % (self.back_number, new_number))
        self.back_number = new_number
```

```python
Class SoccerPlayer(object)
[클래스 예약어] [클래스 이름] [상속받는 객체명]
```

## 변수와 Class명 함수명 짓는 방식
- snake_case: 띄어쓰기 부분에 `_`를 추가한다. 파이썬의 함수, 변수명에 사용된다.
- CamelCase: 띄어쓰기 부분에 대문자를 사용한다. 파이썬 Class명에 사용된다.

## Attribute 추가
Attribute의 추가는 `__init__`과 `self`를 함께 사용한다. 여기서 `__init__`은 객체 초기화 예약 함수이다.

## Python에서 __의 의미
__는 특수한 예약함수나 변수, 그리고 함수명 변경(맨글링)으로 사용된다.
ex. `__main__`, `__add__`, `__str__`, `__eq__`
```python
class SoccerPlayer(object):
    def __str__(self): # SoccerPlayer 객체를 출력하려고 하면 이 부분을 출력한다.
        return "Hello, My name is %s. I play in %s in center " & \(self.name, self.position)

jinhyun = SoccerPlayer("Jinhyun", "MF", 10)
print(jinhyun)
```

## method 구현하기
method(Action)추가는 기존 함수와 같으나 반드시 `self`를 추가해야만 class함수로 인정된다. self는 생성된 인스턴스 자신을 의미한다.


## Objects(instance) 사용하기
Object 이름 선언과 함게 초기값 입력하기
```python
jinhyun = SoccerPlayer("Jinhyun", "MF", 10)
```
<br>

# <span style = "color: #00adb5">OOP Implementation Example (노트를 정리하는 프로그램)</span>
사용자는 Note에 뭔가를 적을 수 있다.
Note에는 Content:str이 있고, 내용을 제거할 수 있다.
2개의 노트북을 합쳐서 하나로 만들 수 있다.
Note는 Notebook에 삽입된다.
Notebook은 Note가 삽입 될 때, 페이지를 생성하며, 최고 300페이지 까지 저장 가능하다.
300페이지가 넘으면 더 이상 노트 삽입하지 못한다.

## class scheme
![image](https://user-images.githubusercontent.com/91870042/144715006-f0f147fb-78cf-4dc8-9a06-4a5fd4085696.png){: .align-center}

## Note class
```py
class Note(object):
    def __init__(self, content = None):
        self.content = content

    def write_content(self, content):
        self.content = content

    def remove_all(self):
        self.content = ""

    def __add__(self, other):
        return self.content + other.content

    def __str__(self):
        return self.content
```

## NoteBook class
```py
class NoteBook(object):
    def __init__(self, title):
        self.title = title
        self.page_number = 1
        self.notes = {}

    def add_note(self, note, page = 0):
        if self.page_number < 300:
            if page == 0:
                self.notes[self.page_number] = note
                self.page_number += 1
            else:
                self.notes = {page : note}
                self.page_number += 1
        else:
            print("Page가 모두 채워졌습니다.")

    def remove_note(self, page_number):
        if page_number in self.notes.keys():
            return self.notes.pop(page_number)
        else
            print("해당 페이지는 존재하지 않습니다")

    def get_number_of_pages(self):
        return len(self.notes.keys())

```
<br>

# <span style = "color: #00adb5">OOP characteristics</span>
객체지향 언어의 특징: 실제 세상을 모델링을 하였다.
`inheritance` `Polymorphism` `Visibility`

## 상속(inheritance)
부모 클래스로 부터 속성과 Method를 물려받은 자식 클래스를 생성하는 것
```py
class Person(object):
    def __init__(self, name, age, gender):
        self.name = name
        self.age = age
        self.gender = gender

    def about_me(self):
        print("저의 이름은 ", self.name, "이구요, 제 나이는 ", str(self.age), "살 입니다.")

    def __str__(self):
        return "저의 이름은 ", self.name, "이구요, 제 나이는 ", str(self.age), "살 입니다."
```
```py
class Employee(Person):
    def __init__(self, name, age, gender, salary, hire_date):
        super().__init__(name, age, gender)
        self.salary = salary
        self.hire_date = hire_date
    
    def do_work(self):
        print("열심히 일을 합니다")

    def about_me(self):
        super().about_me()
        print("제 급여는 ", self.salary, "원 이구요, 제 입사일은 ", self.hire_date, " 입니다.")
```
`super()`를 사용하여 부모클래스를 불러오는 것이 가능하다.

## 다형성 (Polymorphism)
같은 이름 메소드의 내부 로직을 다르게 작성한다. Dynamic Typing특성으로 인해 파이썬에서는 같은 부모클래스의 상속에서 주로 발생한다. 중요한 OOP의 개념이지만 너무 깊이 알 필요는 없다.

![image](https://user-images.githubusercontent.com/91870042/144715631-b06df976-def1-430b-9939-27c565f355bf.png){: .align-center}

```py
class Animal:
    def __init__(self, name):
        self.name = name
    
    def talk(self):
        raise NotImplementedError("Subclass must implement abstract method")

class Cat(Animal):
    def talk(self):
        return "Meow!"

class Dog(Animal):
    def talk(self):
        return "Woof! Woof!"
```

```py
animals = [Cat('A'), Cat('B'), Dog('C')]
for animal in animals
    print(animal.name + ": " + animal.talk())
```

## 가시성 (Visibility)
누구나 객체 안에 모든 변수를 볼 필요가 없기 때문에 객체의 정보를 볼 수 있는 레벨을 조절하는 것이다.
1. 객체를 사용하는 사용자가 임의로 정보 수정
2. 필요없는 정보에는 접근할 필요가 없음
3. 소스의 보호

> **Encapsulation**  
> 캡슐화 또는 정보 은닉 (Information Hiding)  
> Class를 설계할 때, 클래스 간 간섭/정보 공유를 최소화한다. 예를 들어, 심판 클래스가 축구선수 클래스 가족정보를 알아야 할 필요가 없는 것과 같다. 이렇듯 인터페이스만 알아서 사용하는 경우에 캡슐화를 사용한다.

```py
class Product(object):
    pass

class Inventory(object):
    def __init__(self):
        self.__items = [] # Private 변수로 선언함으로서 타 객체가 접근하지 못한다.
    
    def add_new_item(self, product):
        if type(product) == Product:
            self.__items.append(product)
            print("new item added")
        else:
            raise ValueError("Invalid Item")
    
    def get_number_of_items(self):
        return len(self.__items)
```
`__value_name`으로 선언하여 private변수로 만들 수 있다.

> **Decorator**
> private변수를 내부에서 접근할 수 있게 해준다.

```py
class Inventory(object):
    def __init__(self):
        self.__items = []

    @property # 함수명을 변수명처럼 사용하여 내부 속성에 접근할 수 있게 한다.
    def items(self):
        return self.__items
```
<br>

# <span style = "color: #00adb5">Decorate</span>
## first-class objects
일등함수 또는 일급 객체를 의미하며 변수나 데이터구조에 할당이 가능한 객체이다. parameter로 전달이 가능하고 리턴값으로 사용할 수 있다. 따라서 **파이썬의 모든 함수는 일급 함수이다.**

## Inner function
함수 내에 또다른 함수가 존재하는 것을 말한다.
```py
def print_msg(msg):
    def printer():
        print(msg)
    printer()
print_msg("Hello, python")
```
`closures`: inner function을 return값으로 반환하는 것
```py
def print_msg(msg):
    def printer():
        print(msg)
    return printer

another = print_msg("Hello, python")
another()
```
`closures`을 사용했을 때의 장점: 비슷한 목적을 가진 함수의 변형이 가능하다. javascript 에서 상당히 많이 나오는 개념인데 그 이유는 다음 코드를 보면 이해가 된다.
```py
def tag_func(tag, text):
    text = text
    tag = tag

    def inner_func():
        return '<{0}>{1}</{0}>'.format(tag, text)
    
    return inner_func

h1_func = tag_func('title', "This is Pyhon Class")
p_func = tag_func('p', "Data Academy")
```
## Decorator function
```python
def star(func):
    def inner(*args, **kwargs):
        print(args[1] * 30)
        func(*args, **kwargs)
        print(args[1] * 30)
    return inner

@star
def printer(msg, mark):
    print(msg)

printer("Hello!", "*")
```
### 응용1. 중첩 Decorator

```python
def star(func):
    def inner(*args, **kwargs):
        print(args[1] * 30)
        func(*args, **kwargs)
        print(args[1] * 30)
    return inner

def percent(func):
    def inner(*args, **kwargs):
        print(args[1] * 30)
        func(*args, **kwargs)
        print(args[1] * 30)
    return inner

@star
@percent
def printer(msg):
    print(msg)

printer("Hello!")
```
### 응용2. parameter가 전달된 Decorator

```python
def generate_power(exp):
    def wrapper(f): # f에는 raise_two가 들어간다.
        def inner(*args): # args에는 raise_two에 전달된 인자 n이 들어간다.
            result = f(*args) 
            return exp ** result
        return inner
    return wrapper

@generate_power(2)
def raise_two(n):
    return n**2

print(raise_two(7))
```

<br>

# <span style = "color: #00adb5">References</span>

[📘 부스트캠프 AI Tech 3기 Pre-Course: Python Object Oriented Programming](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203369/?isDesc=false)


