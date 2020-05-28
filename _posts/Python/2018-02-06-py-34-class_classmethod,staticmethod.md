---
title: "[Python] Class - Class method / Static method"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - class method
  - static method

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Class method (클래스메서드)

- 클래스 내부의 전역함수  

- 인스턴스가 존재하지 않는 상태에서도 실행가능  

- `@classmethod` 를 통해 구현한다.  

- argument가 `self` 가 아닌, `cls` 이며, `cls`는 클래스자체를 받는다.
> `self` 는 인스턴스(객체) 를 인자로 받는다.

<br>

- 클래스 변수에 접근이 용이하다. (`cls` 인수로 인해)
> 인스턴스 변수로 접근이 불가능한 것은 아니다.

<br>

- 인스턴스 멤버에서 접근이 가능하다.  

<br>

## Example

```python
class Student:
    
    school_name = "Great High School"
    __class_name = "A"  # 클래스 변수 정보은닉 (Information hiding)
    
    @classmethod  # 클래스메서드로 지정
    def get_class_name(cls):  # 클래스변수 __class_name 의 접근자
        return cls.__class_name
    
    @classmethod  # 클래스메서드로 지정    
    def set_class_name(cls, class_name):  # 클래스변수 __class_name 의 설정자
        cls.__class_name = class_name
        return cls.__class_name
    
    def __init__(self):
        self.class_name = Student.get_class_name()
        print("One student entrance into school")
        print("Class : {}".format(self.class_name))
        
    def study(self):
        print("I have to study")
```
인스턴스 생성없이 클래스메서드를 호출해보겠다.

```python
Student.get_class_name()  # get_class_name 의 인수 cls 는 self 와 비슷한 기능이지만, 인스턴스가 아닌 클래스가 인자로 들어간다.
```
\>\>\> `'A'`

```python
Student.set_class_name("B")
```
\>\>\> `'B'`  

클래스메서드에 문제없이 접근했다.  
설정자를 통해 변수가 정상적으로 수정되었는지 확인해보겠다.

```python
Student.get_class_name()
```
\>\>\> `'B'`  

문제없이 수정되었다.  
이제 인스턴스를 만들어보자.

```python
st_first = Student()
```
\>\>\> `'One student entrance into school'`  
\>\>\> `'Class : B'`

```python
st_first.class_name
```
\>\>\> `'B'`  

인스턴스 변수의 값도 문제없이 적용되었음을 알 수 있다.  
이번엔 인스턴스 멤버를 통해 클래스메서드에 접근해보겠다.

```python
st_first.set_class_name("C")
```
\>\>\> `'C'`

```python
st_first.get_class_name()
```
\>\>\> `'C'`  

문제없이 접근된다.  
두번째 인스턴스를 만들어보겠다.

```python
st_second = Student()
```
\>\>\> `'One student entrance into school'`
\>\>\> `'Class : C'`  

<br>

두번째 인스턴스의 인스턴스 변수의 값을 확인해보자.

```python
st_second.class_name
```
\>\>\> `'C'`

<br><br>

# Static method (정적 메서드)

- 클래스 내부의 전역함수  

- 기능적으로 전역함수와 동일

- 함수에 네임스페이스를 주는 용도로도 사용된다. (`ClassName.function`)

- 인스턴스가 존재하지 않는 상태에서도 실행가능  

- `@staticmethod` 를 통해 구현한다.  

- `self` 나 `cls` 같은 인수를 필요로 하지 않는다.  

- 클래스 변수에 접근시, 클래스명을 수동적으로 작성한다. (`cls` 인수가 없다.)  

- 인스턴스 멤버에서 접근이 가능하다.  

<br>

## Example

(클래스메서드의 예시코드와 동일한 기능을 한다.)

```python
class Student:
    
    school_name = "Great High School"
    __class_name = "A"
    
    @staticmethod  # 스태틱메서드로 지정
    def get_class_name():  # 필수 인수가 없다.
        return Student.__class_name  # 클래스 변수에 접근하기위해 클래스명.변수 로 작성하였다.
    
    @staticmethod  # 스태틱메서드로 지정
    def set_class_name(class_name):
        Student.__class_name = class_name
        return Student.__class_name
    
    
    def __init__(self):
        self.class_name = Student.get_class_name()
        print("One student entrance into school")
        print("Class : {}".format(self.class_name))
        

    def study(self):
        print("I have to study")
```
인스턴스 생성없이 스태틱메서드를 호출해보겠다.

```python
Student.get_class_name()
```
\>\>\> `'A'`

```python
Student.set_class_name("B")
```
\>\>\> `'B'`  

스태틱메서드에 문제없이 접근했다.  
설정자를 통해 변수가 정상적으로 수정되었는지 확인해보겠다.

```python
Student.get_class_name()
```
\>\>\> `'B'`  

문제없이 수정되었다.  
이제 인스턴스를 만들어보자.

```python
st_first = Student()
```
\>\>\> `'One student entrance into school'`  
\>\>\> `'Class : B'`

```python
st_first.class_name
```
\>\>\> `'B'`  

인스턴스 변수의 값도 문제없이 적용되었음을 알 수 있다.  
이번엔 인스턴스 멤버를 통해 스태틱메서드에 접근해보겠다.

```python
st_first.set_class_name("C")
```
\>\>\> `'C'`

```python
st_first.get_class_name()
```
\>\>\> `'C'`  

문제없이 접근된다.  
두번째 인스턴스를 만들어보겠다.

```python
st_second = Student()
```
\>\>\> `'One student entrance into school'`  
\>\>\> `'Class : C'`  

두번째 인스턴스의 인스턴스 변수의 값을 확인해보자.

```python
st_second.class_name
```
\>\>\> `'C'`

<br><br>

# Conclusion

- classmethod 와 staticmethod 는 전역함수의 기능을 한다는 것은 동일하다.

- staticmethod 는 `self` 나 `cls` 와 같은 argument를 필요로 하지 않는다.

- staticmethod 는 정의된 클래스의 메서드로만 인식 (소속 변경이 안된다?)  
> 다른 클래스로 상속될 경우, 상속받은 자식의 클래스에서 해당 staticmethod를 호출시 소속 클래스가 정의되었던 클래스로 나타난다.
