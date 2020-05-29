---
title: "[Design Pattern] Factory"
excerpt: 
last_modified_at: 2020-05-29

categories:
  - Design_Pattern

tags:
  - C++
  - Design Pattern
---
<br><br>
![factory pattern](/assets/images/W3sDesign_Factory_Method_Design_Pattern_UML.jpg){:.aligncenter}
<center> Factory Pattern Class Diagram </center>

<br><br><br>

### 간단한 팩터리 메서드의 예
```cpp
#include <cmath>

class Point
{
public:
    float x, y;

    static Point newCartesian(float x, float y)
    {
        return {x, y};
    }

    static Point newPolar(float r, float theta)
    {
        return {r*std::cos(theta), r*std::sin(theta)};
    }
};
```
<br><br><br>

### 간단한 팩터리의 예
> Point 클래스의 private 멤버와 friend 키워드에 주목하자.
 
<br>

```cpp
#include <cmath>

class Point
{
public:
    float x, y;
    friend class PointFactory;

private:
    Point(float x, float y) : x(x), y(y) {}
};

class PointFactory
{
private:
    static Point newCartesian(float x, float y)
    {
        return Point{x, y};
    }

    static Point newPolar(float r, float theta)
    {
        return Point{r*std::cos(theta), r*std::sin(theta)};
    }
};
```
<br><br><br>

### 추상 팩터리
```cpp
#include <iostream>
#include <memory>
#include <map>
#include <string>

struct HotDrink
{
    virtual void prepare(int volume) = 0;
};

struct Tea : HotDrink
{
    void prepare(int volume) override
    {
        std::cout << "take tea bag, boil water. pour" << volume << "ml, add some lemon" << std::endl;
    }
};

struct Coffee : HotDrink
{
    void prepare(int volume) override
    {
        std::cout << "grind some beans, boil water, pour " << volume << "ml, add cream, enjoy!" << std::endl;
    }
};

struct HotDrinkFactory
{
    virtual std::unique_ptr<HotDrink> make() const = 0;
};

struct CoffeeFactory : HotDrinkFactory
{
    std::unique_ptr<HotDrink> make() const override
    {
        return std::make_unique<Coffee>();
    }
};

struct TeaFactory : HotDrinkFactory
{
    std::unique_ptr<HotDrink> make() const override
    {
        return std::make_unique<Tea>();
    }
};

class DrinkFactory
{
public:
    DrinkFactory()
    {
        hot_factories["coffee"] = std::make_unique<CoffeeFactory>();
        hot_factories["tea"] = std::make_unique<TeaFactory>();
    }

    std::unique_ptr<HotDrink> make_drink(const std::string& name, int volume)
    {
        auto drink = hot_factories[name]->make();
        drink->prepare(volume);
        return drink;
    }

private:
    std::map<std::string, std::unique_ptr<HotDrinkFactory>> hot_factories;
};

int main()
{   
    DrinkFactory df;
    df.make_drink("coffee", 200);

    return 0;
}
```
<br><br><br>

### 함수형 팩터리
```cpp
#include <iostream>
#include <string>
#include <map>
#include <memory>
#include <functional>

struct HotDrink
{
    virtual void prepare(int volume) = 0;
};

struct Tea : HotDrink
{
    void prepare(int volume) override
    {
        std::cout << "Take tea bag, boil water, pour " << volume << "ml, add some lemon" << std::endl;
    }
};

struct Coffee : HotDrink
{
    void prepare(int volume) override
    {
        std::cout << "Grind some beans, boil water, pour " << volume << "ml, add cream, enjoy!" << std::endl;
    }
};

class DrinkWithVolumeFactory
{
public:
    DrinkWithVolumeFactory()
    {
        factories["tea"] = []{
            auto tea = std::make_unique<Tea>();
            tea->prepare(200);
            return tea;
        };
    }

    std::unique_ptr<HotDrink> make_drink(const std::string& name);

private:
    std::map<std::string, std::function<std::unique_ptr<HotDrink>()>> factories;
};

inline std::unique_ptr<HotDrink> DrinkWithVolumeFactory::make_drink(const std::string& name)
{
    return factories[name]();
}
```
<br><br><br>

### 기본적인 팩터리 메서드 패턴 1
```cpp
#include <iostream>

// product
class Character
{
public:
    virtual void attack() = 0;
};

class Archer : public Character
{
    void attack() override
    {
        std::cout << "bow." << std::endl;
    }
};

class Warrior : public Character
{
    void attack() override
    {
        std::cout << "sword." << std::endl;
    }
};

// creator
class CharacterCreator
{
public:
    Character* create()
    {
        return factoryMethod();
    }
    virtual Character* factoryMethod() = 0;
};

class ArcherCreator : public CharacterCreator
{
    // 서브 클래스의 메서드에서 만들 객체를 결정하기 때문에 팩터리 메서드 패턴이라 부른다.
    Character* factoryMethod() override
    {
        return new Archer;
    }
};

class WarriorCreator : public CharacterCreator
{
    Character* factoryMethod() override
    {
        return new Warrior;
    }
};

int main()
{
    ArcherCreator* archer = new ArcherCreator();
    Character* character = archer->create();
    character->attack();
    return 0;
}
```
<br><br><br>

### 기본적인 팩터리 메서드 패턴 2
```cpp
#include <iostream>

// product interface
class Product
{
public:
    virtual void print() = 0;
};

// concrete product
class ConcreteProduct : public Product
{
public:
    void print() override { std::cout << "Concrete Product" << std::endl; }
};

// creator
class Creator
{
public:
    Product* anOperation() { return factoryMethod(); }

protected:
    virtual Product* factoryMethod() = 0;
};

// concrete creator
class ConcreteCreator : public Creator
{
public:
    Product* factoryMethod() { return new ConcreteProduct; }
};

int main()
{
    ConcreteCreator pCreator;
    Product* pProduct = pCreator.anOperation();

    pProduct->print();

    delete pProduct;

    return 0;
}
```
<br><br><br>

### 문서 프로그램을 팩터리 메서드 패턴으로 구현
```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <map>

// 최상위 문서 클래스
class Document
{
public:
    virtual bool Open(const char* pFileName) = 0;
};

// 한글 문서 클래스
class HwpDocument : public Document
{
public:
    bool Open(const char* pFileName)
    {
        std::ifstream ifs(pFileName);
        if(!ifs)
            return false;
            // 한글 문서 프로세싱...
        return true;
    }
};

// 워드 문서 클래스
class MsWordDocument : public Document
{
public:
    bool Open(const char* pFileName)
    {
        std::ifstream ifs(pFileName);
        if(!ifs)
            return false;
            // MS Word 문서 프로세싱...
        return true;
    }
};

// 응용프로그램 최상위 클래스
class Application
{
public:
    void newDocument(const char* pFileName)
    {
        Document *pDoc = createDocument();
        mDocs[pFileName] = pDoc;
        pDoc->Open(pFileName);
    }

    virtual Document* createDocument() = 0;

private:
    std::map<std::string, Document*> mDocs;
};

// 한글 응용 프로그램 클래스
class HwpApplication : public Application
{
public:
    Document* createDocument()
    {
        return new HwpDocument;
    }
};

// 워드 응용 프로그램 클래스
class MsWordApplication : public Application
{
public:
    Document* createDocument()
    {
        return new MsWordDocument;
    }
};

int main()
{
    // 한글 응용프로그램 객체 생성
    HwpApplication hwp;

    // 한글 문서 객체 생성
    hwp.newDocument("test.hwp");

    return 0;
}

```
<br><br><br>

### 추상 팩터리 패턴
```cpp
#include <iostream>

class Weapon
{
public:
    virtual int power() = 0;
};

class Bow : public Weapon
{
private:
    int power() override
    {
        return 200;
    }
};

class Sword : public Weapon
{
private:
    int power() override
    {
        return 50;
    }
};

class Armor
{
public:
    virtual int defence() = 0;
};

class Leather : public Armor
{
private:
    int defence() override
    {
        return 50; 
    }
};

class Plate : public Armor
{
private:
    int defence() override
    {
        return 200;
    }
};

// interface
class EquipmentAbstractFactory
{
public:
    virtual Weapon* createWeapon() = 0;
    virtual Armor* createArmor() = 0;
};

// interface 구현
class ArcherEquipment : public EquipmentAbstractFactory
{
private:
    Weapon* createWeapon() override
    {
        return new Bow();
    }

    Armor* createArmor() override
    {
        return new Leather();
    }
};

class WarriorEquipment : public EquipmentAbstractFactory
{
private:
    Weapon* createWeapon() override
    {
        return new Sword();
    }

    Armor* createArmor() override
    {
        return new Plate();
    }
};

// composition class
class Character
{
public:
    Character(EquipmentAbstractFactory* equipment)
    {
        mWeapon = equipment->createWeapon();
        mArmor = equipment->createArmor();
    }

    virtual void attack() = 0;

protected:
    Weapon* mWeapon;
    Armor* mArmor;
};

class Archer : public Character
{
public:
    Archer(EquipmentAbstractFactory* equipment) : Character(equipment) {}

    void attack() override
    {
        std::cout << "bow attack " << mWeapon->power();
    }
};

class Warrior : public Character
{
public:
    Warrior(EquipmentAbstractFactory* equipment) : Character(equipment) {}

    void attack() override
    {
        std::cout << "sword attack " << mWeapon->power();
    }
};

int main()
{
    Character* archer = new Archer(new ArcherEquipment());
    Character* warrior = new Warrior(new WarriorEquipment());

    archer->attack();
    warrior->attack();
    
    return 0;
}
```
