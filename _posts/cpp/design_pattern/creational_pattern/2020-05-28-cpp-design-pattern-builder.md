---
title: "[Design Pattern] Builder"
excerpt: 
last_modified_at: 2020-05-28

categories:
  - Design_Pattern

tags:
  - C++
  - Design Pattern
---
<br><br>
![builder pattern](/assets/images/builder_pattern.png){: width="100%"}
<center> Builder Pattern Class Diagram </center>

<br><br>
![class and sequence diagram](/assets/images/W3sDesign_Builder_Design_Pattern_UML.jpg){:.aligncenter}
<center> Class and Sequence diagram </center>

<br><br><br>

### 기본적인 패턴
```cpp
// Product
class Product
{
};

// Builder interface
class Builder
{
public:
    virtual Product* getResult() = 0;
    virtual void buildPart() = 0;
};

// ConcreteBuilder
class ConcreteBuilder : public Builder
{
public:
    void buildPart() override { mProduct = new Product; }
    Product* getResult() { return mProduct; }

private:
    Product* mProduct;
};

// Director
class Director
{
public:
    Director(Builder* builder) : mBuilder(builder) {}
    ~Director() { if(mBuilder) delete mBuilder; }

public:
    Product* construct()
    {
        mBuilder->buildPart();
        return mBuilder->getResult();
    }

private:
    Builder* mBuilder;
};

int main()
{
    Director* pDirector = new Director(new ConcreteBuilder());
    Product* pProduct = pDirector->construct();

    delete pProduct;
    delete pDirector;

    return 0;
}
```
<br><br><br>

### 기본적인 패턴을 이용하여 자동차 빌더 구현
```cpp
#include <iostream>
#include <string>

// car part
class CEngine
{
public:
    CEngine(int mph) : mMPH(mph) {}
    int getMPH() const { return mMPH; }

private:
    int mMPH;
};

class CWheel
{
public:
    CWheel(int size) : mSize(size) {}
    int getSize() const { return mSize; }

private:
    int mSize;
};

class CBody
{
public:
    CBody(std::string shape) : mShape(shape) {}
    std::string getShape() const { return mShape; }

private:
    std::string mShape;
};

// product
class Car
{
public:
    Car() : mEngine(nullptr), mWheel(nullptr), mBody(nullptr) {}
    ~Car() { if(mEngine) delete mEngine; if(mWheel) delete mWheel; if(mBody) delete mBody; }

public:
    void setEngine(CEngine* engine) { mEngine = engine; }
    void setWheel(CWheel* wheel) { mWheel = wheel; }
    void setBody(CBody* body) { mBody = body; }
    void setManufacturer(std::string m) {  mManufacturer = m; }

    void print() const
    {
        std::cout << "product company : " << mManufacturer << std::endl;
        std::cout << "horse power : " << mEngine->getMPH() << std::endl;
        std::cout << "wheel size : " << mWheel->getSize() << std::endl;
        std::cout << "shape type : " << mBody->getShape() << std::endl;
    }

private:
    CEngine* mEngine;
    CWheel* mWheel;
    CBody* mBody;
    std::string mManufacturer;
};

// builder interface
class Builder
{
public:
    virtual CEngine* getEngine() = 0;
    virtual CWheel* getWheel() = 0;
    virtual CBody* getBody() = 0;
    virtual std::string getManufacturer() = 0;
};

// benz builder
class BenzBuilder : public Builder
{
public:
    CEngine* getEngine() override { return new CEngine(360); }
    CWheel* getWheel() override { return new CWheel(20); }
    CBody* getBody() override { return new CBody("Sedan"); }
    std::string getManufacturer() override { return "Benz"; }
};

// bmw builder
class BMWBuilder : public Builder
{
public:
    CEngine* getEngine() override { return new CEngine(400); }
    CWheel* getWheel() override { return new CWheel(22); }
    CBody* getBody() override { return new CBody("SUV"); }
    std::string getManufacturer() override { return "X6"; }
};

// director
class Director
{
public:
    Director(Builder* builder) : mBuilder(builder) {}
    ~Director() { if(mBuilder) delete mBuilder; }

public:
    Car* construct()
    {
        Car* mCar = new Car;

        mCar->setBody(mBuilder->getBody());
        mCar->setEngine(mBuilder->getEngine());
        mCar->setWheel(mBuilder->getWheel());
        mCar->setManufacturer(mBuilder->getManufacturer());

        return mCar;
    }

private:
    Builder* mBuilder;
};

int main()
{
    Director* pDirector = new Director(new BenzBuilder());
    Car* pCar = pDirector->construct();

    pCar->print();

    delete pCar;
    delete pDirector;
    
    return 0;
}
```
<br><br><br>

### 간단한 게임을 통해 빌더 패턴 구현
```cpp
#include <iostream>

struct Weapon
{
    int mPower;
};

struct Armor
{
    int mDefence;
};

// product
class Character
{
public:
    Weapon* mWeapon;
    Armor* mArmor;

    void displayParts()
    {
        std::cout << "Weapon Power : " << mWeapon->mPower << std::endl;
        std::cout << "Armor Defence : " << mArmor->mDefence << std::endl;
    }
};

// builder interface
class CharacterBuilder
{
public:
    virtual Weapon* createWeapon() = 0;
    virtual Armor* createArmor() = 0;    
};

// Director, responsible for product creation.
class CharacterDirector
{
public:
    void setBuilder(CharacterBuilder* newBuilder)
    {
        mBuilder = newBuilder;
    }

    Character* createCharacter()
    {
        Character* character = new Character();

        character->mWeapon = mBuilder->createWeapon();
        character->mArmor = mBuilder->createArmor();

        return character;
    };

private:
    CharacterBuilder* mBuilder;
};

// concrete archer builder
class ArcherBuilder : public CharacterBuilder
{
public:
    Weapon* createWeapon()
    {
        Weapon* weapon = new Weapon();
        weapon->mPower = 400;
        return weapon;
    }

    Armor* createArmor()
    {
        Armor* armor = new Armor();
        armor->mDefence = 100;
        return armor;
    }
};

// concrete warrior builder
class WarriorBuilder : public CharacterBuilder
{
public:
    Weapon* createWeapon()
    {
        Weapon* weapon = new Weapon();
        weapon->mPower = 100;
        return weapon;
    }

    Armor* createArmor()
    {
        Armor* armor = new Armor();
        armor->mDefence = 400;
        return armor;
    }
};

int main()
{
    Character* character;

    CharacterDirector director;

    ArcherBuilder archer;
    director.setBuilder(&archer);
    character = director.createCharacter();
    character->displayParts();

    WarriorBuilder warrior;
    director.setBuilder(&warrior);
    character = director.createCharacter();
    character->displayParts();

    return 0;
}
```
<br><br><br>

### 빌더 패턴을 통한 간단한 번역기 구현
```cpp
#include <iostream>
#include <fstream>
#include <string>

// product
class TranslationBook
{
public:
    std::string getContents() { return mContents; }
    void addContents(std::string str) { mContents += str; }

protected:
    std::string mContents;
};

// interface
class Translator
{
public:
    TranslationBook getResult() { return mResult; }
    virtual void transSentence(std::string str) = 0;

protected:
    TranslationBook mResult;
};

class EnglishTranslator : public Translator
{
public:
    void transSentence(std::string str)
    {
        std::string output;
        output = "english";
        return mResult.addContents(output);
    }
};

class JapaneseTranslator : public Translator
{
public:
    void transSentence(std::string str)
    {
        std::string output;
        output = "japanese";
        return mResult.addContents(output);
    }
};

class Director
{
public:
    void doTranslate(const char* pInFile, Translator& t)
    {
        std::ifstream ifs(pInFile);
        if(!ifs)
        {
            std::cout << "can't open book : " << pInFile << std::endl;
            return;
        }

        while(true)
        {
            std::string sentence = getSentence(ifs);

            if(sentence.empty())
                break;
            t.transSentence(sentence);
        }
    }

protected:
    std::string getSentence(std::ifstream& ifs)
    {
        std::string strSentence;

        // processing...

        return strSentence;
    }
};

int main()
{
    Director d;
    EnglishTranslator et;
    d.doTranslate("koreanBook", et);
    TranslationBook english = et.getResult();

    JapaneseTranslator jt;
    d.doTranslate("KoreanBook", jt);
    TranslationBook japanese = jt.getResult();

    return 0;
}
```
