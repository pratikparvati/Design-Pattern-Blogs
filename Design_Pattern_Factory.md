# Design Pattern - Factory

The factory method design pattern is one of the well-known [GoF](http://wiki.c2.com/?GangOfFour) design patterns. It is a creational design pattern that uses factory methods to deal with creating instances without specifying the exact class of the object that will be created.

>*Factory Method is a creational design pattern that defines an interface or abstract class for creating an object, but allows subclasses to decide the type of objects that will be instantiated.*

This pattern is named as a **factory** as it is responsible for manufacturing an Object. Factory design pattern is predicated upon an object-oriented encapsulation idea. In general, Object creation code is written on client side of the program, but with the factory design, we encapsulate object creation code inside the factory method. Therefore, the factory may return an object of one of the various classes mentioned within a program, based on the data supplied to it.

### Motivation

Let's look at a scenario where there is a need to create different types of objects with varying configurations, but the exact type of object needed may not be known until runtime.

In the case of a computer game, the game may need to create different types of characters with varying abilities and characteristics. However, the exact type of character needed may not be known until runtime, as it may depend on factors such as the player's progress in the game or the level they are currently playing.

If we were to directly create objects using concrete classes for each character type, we would end up with code duplication and tightly coupled code. As the number of character types increases, managing the creation logic becomes cumbersome and error-prone. Additionally, modifying or adding new character types would require modifying existing client code, violating the principle of Open-Closed principle.

To address these challenges, we can apply the Factory Design Pattern. The Factory Design Pattern provides a structured approach to object creation, encapsulating the creation process within a factory class and promoting loose coupling between the client code and the concrete classes.

###  !Factory Design Pattern

Let's look at the below C++ example that doesn't use factory design pattern and analyze the potential problems.

```C++
#include <iostream>
#include <string>

// Character base class
class Character
{
public:
    virtual void displayInfo() const = 0;
    virtual void attack() const = 0;
    virtual void defend() const = 0;
    virtual ~Character() {}
};

// Concrete character classes
class Warrior : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Warrior: A strong melee fighter with high stamina." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Warrior attacks with a mighty swing!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Warrior raises a sturdy shield to block incoming attacks!" << std::endl;
    }
};

class Mage : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Mage: A magical spellcaster with powerful elemental abilities." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Mage casts a powerful fireball!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Mage conjures a protective barrier to deflect enemy spells!" << std::endl;
    }
};

class Archer : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Archer: A skilled marksman with exceptional ranged attacks." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Archer fires a precise arrow at the target!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Archer swiftly evades incoming attacks with precise movements!" << std::endl;
    }
};

// Client code
int main()
{
    Character *warrior = new Warrior();
    Character *mage = new Mage();
    Character *archer = new Archer();

    warrior->displayInfo();
    warrior->attack();
    warrior->defend();

    mage->displayInfo();
    mage->attack();
    mage->defend();

    archer->displayInfo();
    archer->attack();
    archer->defend();

    delete warrior;
    delete mage;
    delete archer;

    return 0;
}
```
In this example, characters are created directly in the client code without using the Factory Design Pattern. Each character type (Ex, `Warrior`, `Mage`, `Archer`) is instantiated using the concrete class constructors. The member functions simulate the specific actions that each character type can perform during the game.

#### Problems in the example

The potential problems include:

1. Code Duplication: Without the Factory Design Pattern, we end up duplicating the code for creating characters in multiple places throughout the client code. This can lead to inconsistencies and makes it harder to manage and modify the codebase.
2. Lack of Abstraction: There is no clear abstraction or separation between the client code and the creation of character objects. This can make the code harder to understand, maintain, and extend, especially as the number of character types grows.
3. Tight Coupling: The client code is tightly coupled to the concrete character classes. It directly creates objects using their constructors, making it harder to introduce new character types or modify the creation process without modifying the client code.
4. Limited Flexibility and Extensibility: Adding new character types or modifying the creation process requires modifying the client code. This violates the Open-Closed Principle, which states that code should be open for extension but closed for modification.

By using the Factory Design Pattern, we can address these problems by encapsulating the creation logic within a factory class, promoting loose coupling, providing an abstraction layer, and facilitating extensibility without modifying the client code.

### The Solution

The Factory Method pattern suggests substituting direct object construction calls (made with the `new` operator) with invocations of a dedicated factory method. This provides a level of indirection between the client code and the actual creation of objects.

```C++
#include <iostream>
#include <string>

// Character base class
class Character
{
public:
    virtual void displayInfo() const = 0;
    virtual void attack() const = 0;
    virtual void defend() const = 0;
    virtual ~Character() {}
};

// Concrete character classes
class Warrior : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Warrior: A strong melee fighter with high stamina." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Warrior attacks with a mighty swing!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Warrior raises a sturdy shield to block incoming attacks!" << std::endl;
    }
};

class Mage : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Mage: A magical spellcaster with powerful elemental abilities." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Mage casts a powerful fireball!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Mage conjures a protective barrier to deflect enemy spells!" << std::endl;
    }
};

class Archer : public Character
{
public:
    void displayInfo() const override
    {
        std::cout << "Archer: A skilled marksman with exceptional ranged attacks." << std::endl;
    }

    void attack() const override
    {
        std::cout << "Archer fires a precise arrow at the target!" << std::endl;
    }

    void defend() const override
    {
        std::cout << "Archer swiftly evades incoming attacks with precise movements!" << std::endl;
    }
};

// CharacterFactory class
class CharacterFactory
{
public:
    static Character *createCharacter(const std::string &characterType)
    {
        if (characterType == "Warrior")
        {
            return new Warrior();
        }
        else if (characterType == "Mage")
        {
            return new Mage();
        }
        else if (characterType == "Archer")
        {
            return new Archer();
        }
        else
        {
            std::cout << "Invalid character type. Creating a default character." << std::endl;
            return new Warrior(); // Default to a Warrior character
        }
    }
};

// Client code
int main()
{
    // Create a Warrior character
    Character *warrior = CharacterFactory::createCharacter("Warrior");
    warrior->displayInfo();
    warrior->attack();
    warrior->defend();

    // Create a Mage character
    Character *mage = CharacterFactory::createCharacter("Mage");
    mage->displayInfo();
    mage->attack();
    mage->defend();

    // Create an Archer character
    Character *archer = CharacterFactory::createCharacter("Archer");
    archer->displayInfo();
    archer->attack();
    archer->defend();

    // Clean up the memory
    delete warrior;
    delete mage;
    delete archer;

    return 0;
}
```
This code demonstrates the use of the Factory Design Pattern to create different types of characters (`Warrior`, `Mage`, `Archer`) based on the character type provided. The `CharacterFactory` class acts as the factory responsible for creating character objects. The `createCharacter` static method in the factory takes a character type as input and returns a dynamically allocated instance of the corresponding concrete character class.

In the client code, various character objects are created using the `CharacterFactory::createCharacter` method and then utilized by calling their respective member functions (`displayInfo`, `attack`, `defend`). Finally, the dynamically allocated character objects are deleted to release the memory.

Here is a UML class diagram representing the example code:

![UML Factory design pattern](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FFactory%2FFactroy_design_pattern2.drawio.png?alt=media&token=3c310998-ca0e-4809-bff5-2cc94846f524 "UML Factory design pattern")

In this UML class diagram:

* The `Character` class is the abstract base class defining the common interface for all character types.
* The `Warrior`, `Mage`, and `Archer` classes are concrete classes that inherit from `Character` and provide specific implementations of the character behaviors.
* The `CharacterFactory` class is responsible for creating character objects. It has a `createCharacter` method that returns a `Character*` pointer to the created character object.
* The client code asks `CharacterFactory` to create an object of `Warrior`, `Mage`, and `Archer`.

This approach offers several advantages. 

1. Instead of tightly coupling the client code to specific concrete classes, the client code relies on the factory method to create the objects. This promotes loose coupling and enhances flexibility in the system.
2. The factory method encapsulates the object creation process, allowing for potential changes or variations in the way objects are instantiated. By centralizing the creation logic within the factory method, it becomes easier to modify or extend the object creation process without impacting the client code.
3. The factory method serves as a single entry point for creating objects, allowing for centralized control and consistency in the creation process. This can be particularly beneficial in scenarios where additional steps or validations are required during object creation.
4. The objects created by the factory method are commonly referred to as products. This distinction helps to highlight the fact that the factory method is responsible for creating specific types of objects and provides a clear abstraction for clients to interact with.

### How to implement?

1. **Identify the common interface**: Determine the common interface that all the product classes will implement. This interface defines the operations that can be performed on the objects created by the factory.

2. **Create an abstract base class**: Declare an abstract base class (or interface) representing the common interface identified in the previous step. This class should have pure virtual methods that define the common operations.

3. **Define concrete classes**: Create concrete classes that inherit from the abstract base class. Each concrete class represents a specific product that can be created by the factory. Implement the virtual methods of the base class in these concrete classes.

4. **Implement the factory class**: Create a factory class that is responsible for creating objects of the concrete classes. The factory class should have a method that takes in parameters (such as a type or configuration) and returns a pointer to the base class (the abstract type). Within this method, use conditional statements or other logic to determine which concrete class to instantiate.

5. **Utilize the factory class**: In the client code, use the factory class to create objects by invoking the appropriate factory method. The client code should work with the abstract base class and the objects returned by the factory method, without being aware of the specific concrete classes.

6. **Test and iterate**: Test the implementation to ensure that objects are being created correctly and that the factory is functioning as expected. Make any necessary adjustments or enhancements based on the specific requirements of your application.

### Conclusion

The Factory Design Pattern provides a way to create objects without specifying their exact types upfront. By using a factory class, you can centralize object creation, enhance code modularity, and facilitate runtime determination of object types. The pattern is valuable for scenarios where object creation involves complex logic, varying dependencies, or the need to conserve system resources. Overall, the Factory Design Pattern improves code flexibility, maintainability, and scalability.





