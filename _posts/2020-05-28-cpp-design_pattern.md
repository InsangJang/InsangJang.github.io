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

### 기본적인 패턴
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

