# Design Pattern - Builder

The Gang of Four authors, namely [Erich Gamma](https://en.wikipedia.org/wiki/Erich_Gamma), [John Vlissides](https://en.wikipedia.org/wiki/John_Vlissides), [Ralph Johnson](https://en.wikipedia.org/wiki/Ralph_Johnson_(computer_scientist)), and [Richard Helm](https://en.wikipedia.org/wiki/Richard_Helm), formally documented the Software Design Patterns in 1995 after discovering and describing them. They divided design patterns into three main categories based on their purpose and functionality: Creational, Structural, and Behavioral patterns.

### Creational Design Pattern

The Creational Design Pattern deals with object creation mechanisms and trying to create objects in a manner suitable to the situation. It focuses on how the objects are created and utilized in an application. They provide ways to create objects while hiding the creation logic, abstracting it from the client code. The `new` operator is often considered harmful as it scatters objects all over the application; over time it can become challenging to change an implementation because classes become tightly coupled. Creational Design Patterns address this issue by decoupling the client entirely from the actual initialization process.

We'll go over each of the creational design patterns, which, as we'll see, all deal with a specific implementation task and add a higher degree of abstraction to the code base.

#### Builder 

The intent of the Builder pattern is to separate the construction of a complex object from its representation so that the same construction process can create different representations and in turn, provides better control over the construction process. The design pattern includes two key participants: the **builder** and the **director**. The builder is tasked with constructing the different components of the intricate object, while the director manages the construction process by utilizing an instance of the builder.

>*The builder pattern constructs complex objects using step-by-step approach.*


![Builder Design Patter UML](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FBuilder%2Fbuild_pattern_uml.jpg?alt=media&token=a67f9677-4198-4c32-9b6b-cdd138ec2655 "Builder Design Patter UML")

The UML diagram above describes an implementation of the builder design pattern. This diagram consists of four classes:

- **Product**: Represents the complex object that is being built.
- **Builder**: This is base class (or interface) for all builders and defines a steps that must be taken in order to correctly create an complex object (product). Generally each step is an abstract method that is overriden by concrete implementation.
- **ConcreteBuilder**: Provides implementation for builder. Builder is an object able to create other complex objects (products).
- **Director**: Represents class that controls algorithm used for creation of complex object. 

```C++
#include <algorithm>
#include <list>
#include <string>
#include <iostream>

class Product
{
private:
    std::list<std::string> _parts;

public:
    void Add(std::string part)
    {
        _parts.push_back(part);
    }

    void Show()
    {
        std::cout << "Parts: ";
        for (auto p : _parts)
        {
            std::cout << "\t" << p;
        }
    }
};

class Builder
{
public:
    virtual void BuildPartA() = 0;
    virtual void BuildPartB() = 0;
    virtual Product GetResult() = 0;
};

class Director
{
public:
    void Construct(Builder *builder)
    {
        builder->BuildPartA();
        builder->BuildPartB();
    }
};

class ConcreteBuilder1 : public Builder
{
private:
    Product _product;

public:
    void BuildPartA() override
    {
        _product.Add("Part A");
    }

    void BuildPartB() override
    {
        _product.Add("Part B");
    }

    Product GetResult() override
    {
        return _product;
    }
};

int main(void)
{
    Builder *b1 = new ConcreteBuilder1();
    Director *dir = new Director();

    dir->Construct(b1);
    Product p1 = b1->GetResult();
    p1.Show();
}

/* OUTPUT
Parts: 	Part A	Part B
*/

```

##### Motivation

Imagine a complex object that requires a step-by-step initialization of many fields and nested objects. Such initialization code is usually buried inside a monstrous constructor with lots of parameters.

Suppose you are tasked with designing a software application that allows users to customize and order their own smartphone. The smartphone can be customized with different brands, models, operating systems, RAM, storage, camera, and price. As the number of customization options increases, it becomes increasingly difficult to manage and maintain the code responsible for creating and initializing the smartphone object. This can lead to code that is difficult to read, understand, and modify, as well as bugs and errors that can affect the performance and functionality of the application. Additionally, different users may want to order smartphones with different customizations, so it can be challenging to keep track of all the different possible combinations of customization options. 

```C++
#include <iostream>
#include <string>

class Smartphone {
public:
    Smartphone(std::string brand, std::string model, std::string os, int ram, int storage, int camera, double price)
        : m_brand(brand), m_model(model), m_os(os), m_ram(ram), m_storage(storage), m_camera(camera), m_price(price) {
    }
    
    void printDetails() {
        std::cout << "Brand: " << m_brand << std::endl;
        std::cout << "Model: " << m_model << std::endl;
        std::cout << "Operating System: " << m_os << std::endl;
        std::cout << "RAM: " << m_ram << " GB" << std::endl;
        std::cout << "Storage: " << m_storage << " GB" << std::endl;
        std::cout << "Camera: " << m_camera << " MP" << std::endl;
        std::cout << "Price: $" << m_price << std::endl;
    }
    
private:
    std::string m_brand;
    std::string m_model;
    std::string m_os;
    int m_ram;
    int m_storage;
    int m_camera;
    double m_price;
};

// Client code
int main() {
    Smartphone samsungPhone("Samsung", "Galaxy S21", "Android 11", 8, 128, 64, 799.99);
    samsungPhone.printDetails();
    
    Smartphone applePhone("Apple", "iPhone 12", "iOS 14", 4, 64, 12, 1099.99);
    applePhone.printDetails();
    
    // ...
    
    // More code for handling user input and creating smartphones with customizations
}
```

As you can see, the constructor of the Smartphone class is becoming increasingly complex as more customization options are added. This can make it difficult to read, understand, and modify the code, as well as track all the different possible combinations of customization options. The problem, therefore, is to design a software application that is flexible and easy to maintain, while still allowing users to customize their smartphones with different options. The Builder design pattern provides a solution to this problem by separating the construction of complex objects from their representation, allowing for more flexibility and ease of maintenance.

##### How builder design pattern solve this problem?

The Builder pattern suggests that you extract the object construction code out of its own class and move it to separate objects called builders.

The pattern divides the process of creating an object into a number of steps. You carry out a series of these actions on a builder object to create an object. The key fact is that not all of the steps must be called. Only the steps required to create a specific configuration of an object may be called. Some of the construction steps might require different implementation when you need to build various representations of the product. Fo example: Camera of the Smart phone may use Sony IMX700 or Samsung HM2 sensors for converting light into electric signals.

![Builder Design Pattern Example](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FBuilder%2Fbuild_phone.jpg?alt=media&token=36b64473-7a1e-40f0-a532-c6afddcb5970 "Builder Design Pattern Example")

In this case, you can create several different builder classes that implement the same set of building steps, but in a different manner. Then you can use these builders in the construction process (i.e., an ordered set of calls to the building steps) to produce different kinds of objects.

```C++
#include <iostream>
#include <string>

// Product class
class Smartphone
{
public:
    void setBrand(const std::string &brand)
    {
        m_brand = brand;
    }

    void setModel(const std::string &model)
    {
        m_model = model;
    }

    void setOS(const std::string &os)
    {
        m_os = os;
    }

    void setRAM(const int &ram)
    {
        m_ram = ram;
    }

    void setStorage(const int &storage)
    {
        m_storage = storage;
    }

    void setCamera(const int &camera)
    {
        m_camera = camera;
    }

    void setPrice(const double &price)
    {
        m_price = price;
    }

    void printDetails()
    {
        std::cout << "Brand: " << m_brand << std::endl;
        std::cout << "Model: " << m_model << std::endl;
        std::cout << "Operating System: " << m_os << std::endl;
        std::cout << "RAM: " << m_ram << " GB" << std::endl;
        std::cout << "Storage: " << m_storage << " GB" << std::endl;
        std::cout << "Camera: " << m_camera << " MP" << std::endl;
        std::cout << "Price: $" << m_price << std::endl;
    }

private:
    std::string m_brand;
    std::string m_model;
    std::string m_os;
    int m_ram;
    int m_storage;
    int m_camera;
    double m_price;
};

// Abstract builder class
class SmartphoneBuilder
{
public:
    virtual void setBrand() = 0;
    virtual void setModel() = 0;
    virtual void setOS() = 0;
    virtual void setRAM() = 0;
    virtual void setStorage() = 0;
    virtual void setCamera() = 0;
    virtual void setPrice() = 0;

    virtual Smartphone *getSmartphone() = 0;
};

// Concrete builder class
class SamsungBuilder : public SmartphoneBuilder
{
public:
    SamsungBuilder()
    {
        m_phone = new Smartphone();
    }

    void setBrand()
    {
        m_phone->setBrand("Samsung");
    }

    void setModel()
    {
        m_phone->setModel("Galaxy S21");
    }

    void setOS()
    {
        m_phone->setOS("Android 11");
    }

    void setRAM()
    {
        m_phone->setRAM(8);
    }

    void setStorage()
    {
        m_phone->setStorage(128);
    }

    void setCamera()
    {
        m_phone->setCamera(64);
    }

    void setPrice()
    {
        m_phone->setPrice(799.99);
    }

    Smartphone *getSmartphone()
    {
        return m_phone;
    }

private:
    Smartphone *m_phone;
};

// Concrete builder class
class IPhoneBuilder : public SmartphoneBuilder
{
public:
    IPhoneBuilder()
    {
        m_phone = new Smartphone();
    }

    void setBrand()
    {
        m_phone->setBrand("Apple");
    }

    void setModel()
    {
        m_phone->setModel("IPhone 12");
    }

    void setOS()
    {
        m_phone->setOS("iOS 14");
    }

    void setRAM()
    {
        m_phone->setRAM(4);
    }

    void setStorage()
    {
        m_phone->setStorage(64);
    }

    void setCamera()
    {
        m_phone->setCamera(12);
    }

    void setPrice()
    {
        m_phone->setPrice(1099.99);
    }

    Smartphone *getSmartphone()
    {
        return m_phone;
    }

private:
    Smartphone *m_phone;
};

// Director class
class PhoneManufacturer
{
public:
    Smartphone *createSmartphone(SmartphoneBuilder *builder)
    {
        builder->setBrand();
        builder->setModel();
        builder->setOS();
        builder->setRAM();
        builder->setStorage();
        builder->setCamera();
        builder->setPrice();
        return builder->getSmartphone();
    }
};

// Client code
int main()
{
    PhoneManufacturer manufacturer;
    SamsungBuilder samsungBuilder;
    Smartphone *samsungPhone = manufacturer.createSmartphone(&samsungBuilder);
    std::cout << "\nSamsung Spec: \n";
    samsungPhone->printDetails();

    IPhoneBuilder iphoneBuilder;
    Smartphone *iphone = manufacturer.createSmartphone(&iphoneBuilder);
    std::cout << "\nIphone Spec: \n";
    iphone->printDetails();
}

/* OUTPUT

Samsung Spec: 
Brand: Samsung
Model: Galaxy S21
Operating System: Android 11
RAM: 8 GB
Storage: 128 GB
Camera: 64 MP
Price: $799.99

Iphone Spec: 
Brand: Apple
Model: IPhone 12
Operating System: iOS 14
RAM: 4 GB
Storage: 64 GB
Camera: 12 MP
Price: $1099.99

*/
```

##### When to use Builder Design Pattern

- Use the Builder pattern to get rid of a [telescoping constructor](https://en.wikipedia.org/wiki/Talk%3AAnti-pattern#Telescoping_constructor).
- Use Builder pattern when you want your code to be able to create different representations of some product.

##### How to implement builder design pattern

- Define a Builder interface that specifies the methods for constructing the complex object. These methods should return the builder object itself to allow for method chaining.

- Create a ConcreteBuilder class that implements the Builder interface. This class will have all the methods needed to construct the complex object and store its attributes.

- Define a Director class that uses the builder object to construct the final object. The Director class should not be responsible for constructing the object directly, but should delegate this task to the builder object.

- Create a Product class that represents the final object to be constructed. This class should have all the necessary attributes and methods to represent the complex object.

- Use the builder object to construct the final object in the Director class, by calling the appropriate methods on the builder object.

##### Pros and Cons

Pros:

- Encapsulates the construction of complex objects and separates it from the object's representation.
Provides a clear and readable interface for constructing complex objects with many attributes.
- Allows for the creation of different representations of the same object using the same construction process.
- Can improve code maintainability and flexibility by reducing the coupling between the client code and the constructed objects.
- Can help enforce design principles such as the Single Responsibility Principle (SRP) and the Open/Closed Principle (OCP).
  
Cons:

- Requires the creation of additional classes for the builder and director, which can add complexity to the code.
- Can lead to an increase in code size and complexity, especially if the objects being constructed are not very complex.
- Can make it more difficult to refactor the code if the construction process changes significantly.
- Can add some runtime overhead compared to direct object construction, due to the additional object creation and method calls required by the builder pattern.


Overall, the Builder design pattern can be a useful tool for creating complex objects with many attributes, especially when you need to create different representations of the same object or enforce design principles such as SRP and OCP. However, it may not be the best choice for simpler objects or situations where flexibility and maintainability are not a high priority.









