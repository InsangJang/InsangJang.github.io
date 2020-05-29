---
title: "[Design Pattern] Prototype"
excerpt: 
last_modified_at: 2020-05-29

categories:
  - Design_Pattern

tags:
  - C++
  - Design Pattern
---
<br><br>
![prototype pattern](/assets/images/PatternPrototype02.jpg){:.aligncenter}
<center> Prototype Pattern Class Diagram </center>

<br><br><br>

### 객체가 값으로만 이루어져 있을 때 가장 쉬운 복제 방법 예시 코드
```cpp
#include <iostream>
#include <string>

struct Address
{
    std::string street;
    std::string city;
    int suite;
};

struct Contact
{
    std::string name;
    Address address; // no problem
    // Address* address // occur problem
};

int main()
{
    Contact worker{"Kim", Address{"Kangnam Samsungdong ", "Korea", 0}};
    Contact john = worker;

    std::cout << "name : " << john.name << " /street : " << john.address.street << " /city : " << john.address.city << " /suite : " << john.address.suite << std::endl;

    john.name = "Jang";
    john.address.suite = 13;

    std::cout << "name : " << john.name << " /street : " << john.address.street << " /city : " << john.address.city << " /suite : " << john.address.suite << std::endl;

    return 0;
}
```
<br><br><br>

### 복사 생성자를 통한 처리
```cpp
struct Contact
{
    string name;
    Address* address;

    Contact(string name, Address* address) : name{ name }, address{ address } { }

    
    // 1. Address의 항목이 추가 또는 삭제 되었을때 곤란해 진다.
    Contact(const Contact& other) : name{ other.name }
    {
        address = new Address{ other.address->street, other.address->city, other.address->suite };
    }

    // 2. Address 항목이 수정되어도 신경 쓸 것이 없다.
    Contact(const Contact& other) : name{ other.name }
    {
        address = new Address{ *other.address };
    }

    // 3. 이것 또한 마찬가지다.
    Contact(const Contact& other) : name{ other.name }, address{ new Address{*other.address} }
    {
    }
    
};
```
<br><br><br>

### prototype.clone() 형태의 호출로 가독성 측면을 향상 시킬 수 있다.
```cpp
template<typename T> struct Cloneable
{
    virtual T clone() const = 0;
};
 
struct Contact : public Cloneable<Contact>
{
    string name;
    Address* address;

    Contact clone() const
    {
        return Contact{ name, new Address{ *address } };
    }
};
 
Contact worker { "sss", new Address{ "123 East Dr", "London", 0 }};
Contact john = worker.clone();
```
<br><br><br>

### 프로토 타입 팩터리
```cpp
struct EmployeeFactory
{
    static Contact main;
    static Contact aux;
 
    static unique_ptr<Contact> NewMainOfficeEmployee(string name, int suite)
    {
        return NewEmployee(name, suite, main);
    }
 
    static unique_ptr<Contact> NewAuxOfficeEmployee(string name, int suite)
    {
        return NewEmployee(name, suite, aux);
    }
 
private:
    static unique_ptr<Contact> NewEmployee(string name, int suite, Contact& proto)
    {
        auto result = make_unique<Contact>(proto);
        result->name = name;
        result->address->suite = suite;
        return result;
    }
};
Contact EmployeeFactory::main{ "", new Address{ "123 East Dr", "London", 0 } };
Contact EmployeeFactory::aux{ "", new Address{ "123B East Dr", "London", 0 } };
 
int main()
{
    auto john = EmployeeFactory::NewAuxOfficeEmployee("John Doe", 123);
    auto jane = EmployeeFactory::NewMainOfficeEmployee("Jane Doe", 125);
 
    return 0;
}
```