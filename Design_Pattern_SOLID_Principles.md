# Design Pattern - SOLID Principles

A design pattern in software engineering is a general solution to a frequently occurring problem in software design. It is a description or template for solving an issue that can be used in a several kinds of situations. 

> **NOTE:** Design patterns are not finished solution or a code or a class or a library that you can import in your project. They are a generic solution for solving a problem. Each project will have a slightly different way to solve it.

### Why learn design pattern?

Here is a definition of software engineering taken from [Wikipedia](https://en.wikipedia.org/wiki/Software_engineering):

>*Software engineering is the application of a systematic, disciplined, quantifiable approach to the development, operation, and maintenance of software, and the study of these approaches; that is, the application of engineering to software".*

At the initial stage of software development, the code looks clean, elegant, and compelling. It has a basic elegance that draws the same interest of designers and implementers. These applications manage to keep their design integrity throughout the development process and into the first release. But then an anomaly unfolds and the program begins to deteriorate. The program becomes a growing mess of code that the developer find increasingly difficult to manage. Even the simplest of the change in application demands a fruitless effort, that developers cry for a new design.

Design patterns can speed up the development process by providing a tested and proven development paradigm. Knowing how to tackle a problem in a new scenario during the software’s development phase can help you design more effective software.

### Coupling and Cohesion

Two words describe how easy or difficult it is to change a piece of software. 

![Coupling](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Fcoup_dia.jpg?alt=media&token=3b1dac83-332d-472c-b4be-2d05201a9c59 "Coupling")

Coupling in software development refers to the extent to which one component or module depends on another. A high degree of coupling indicates that the components are closely related and that changes in one can have a significant impact on others. A low degree of coupling, on the other hand, means that components are independent of one another and can be changed or reused without significantly affecting other components. 



>*Coupling describes two things changing together – a movement in one induces a movement in another*


![Cohesion](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Fcoh_dia.jpg?alt=media&token=0aca6082-d7bf-48cb-b788-9ac3dbf35009 "Cohesion")


Cohesion is a measure of the degree to which the elements of the module are functionally
related. It is the degree to which all elements directed towards performing a single task are
contained in the component. Cohesion represents the clarity of the responsibilities of a module. So, cohesion focuses on how single module/class is designed. Higher the cohensiveness of the module/class, better is the OO design.

>*Cohesion defines the relationships within a module*



### SOLID Principles

In object-oriented programming, SOLID is an acronym for Five important design principles. These Five principles were introduced by Robert C. Martin, in his 2000 paper [Design Principles and Design Patterns](https://web.archive.org/web/20150906155800/http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf). SOLID is a popular set of design principles with the goals to reduce dependencies so that we can change one area of software without impacting others; they are intended to make designs easier to understand, maintain, and extend. Following the guidelines of these principle generally leads to writing longer and more complex code; However, this extra effort makes software easier to maintain, test and extend. To fully realize the benefits of the SOLID principles, the application must be large; the advantages are not very visible in a small application.

Let's identify these principles. 

#### Principle 1: S - Single Responsibility Principle (SRP)

According to the Single Responsibility Principle (SRP), a software entities (like class, modules, functions, etc) should never be changed because of more than one reason; when the code does have to change, it should do so by seperating the responsibility to different entities, it should not be a victim of collateral damage. It helps keep classes and methods small and maintainable. In addition to keeping classes small and focused it also makes them easier to understand.

>*A class should have one, and only one, reason to change. – Robert C Martin*

Let's analyze the example that violates this principle.

```C++
class Bill
{
    Menu menu;
    std::vector<std::string> Items;
    unsigned int customer_id;
    unsigned int dateNtime;
    unsigned int total_price;

    Bill(Menu _menu, std::vector<string> items, unsigned int cust_id, unsigned int dNt, std::vector<unsigned int> item_price)
    {
        this->menu = _menu;
        std::copy(items.begin(), items.end(), std::back_iterator(Items));
        this->customer_id = cust_id;
        this->dateNtime = dNt;
        std::for_each(item_price.begin(), item_price.end(), [this](unsigned int item_p)
                      { total_price += item_p; })
    }

    void printBill() {
        // Print the bill
    }

    void saveBill() {
        // Save the Bill in File or DB
    }
};
```

##### How does it violate SRP?

SRP states that classes should have one responsibility, here, we can draw out three responsibilities: Bill Management, Printing the Bill and Bill storage management. These three responsibilities are coupled in a single class which voilates SRP.

![SRP Violate](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Fsrp1_diff.webp?alt=media&token=2f8ab4ec-a23f-40ee-93e5-566096513ea1 "SRP Violate")

##### How will this design cause issues in the future?

If the application changes in a way that it affects database management functions. The classes that make use of Bill management will have to be touched and recompiled to compensate for the new changes. This makes code rigid, like a domino effect, touch one card it affects all the other card in line.

To make this obey SRP, we create two more classes that will handle the sole responsibility of storing the bill to a database and printing the bill.

![SRP](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2FSRP_Resp.webp?alt=media&token=37eb2c5b-60ed-426b-ba11-6f87b5669294 "SRP")

``` C++

class Bill
{
    Menu menu;
    std::vector<std::string> Items;
    unsigned int customer_id;
    unsigned int dateNtime;
    unsigned int total_price;

    Bill(Menu _menu, std::vector<string> items, unsigned int cust_id, unsigned int dNt, std::vector<unsigned int> item_price)
    {
        this->menu = _menu;
        std::copy(items.begin(), items.end(), std::back_iterator(Items));
        this->customer_id = cust_id;
        this->dateNtime = dNt;
        std::for_each(item_price.begin(), item_price.end(), [this](unsigned int item_p)
                      { total_price += item_p; })
    }
};

class StoreBill {
    void saveToFile(Bill& bill) {
        // Save bill to file
    }

    void saveToDB(Bill& bill) {
        // Save bill to DB
    }
};

class PrintBill {
    void printMenu(Bill& bill) {
        // Print Menu
    }

    void printBill(Bill& bill) {
        // Print the bill
    }
};
```

The fact that we split our Bill class into 3 classes doesn't necessarily mean that we should have a separate class for every method that doesn't suit the class's responsibility. No, we should instead have a separate class for every group of methods that change for different reasons. Now, the three classes are decoupled and if any updates take place on any of them, the other classes won’t be affected.

#### Principle 2: O - Open-Closed Principle (OCP)

OCP says that a class should be open for extension and closed for modification. The "closed" part of the rule states that once a class has been developed and tested, the class code shouldn’t change. The "open" part of the rule states that you should be able to extend existing code in order to introduce new functionality.

>*Software entities should be open for extension but closed for modification.*

When a single change to a program causes a chain of changes to dependent modules (i.e, coupled modules); the program becomes fragile, rigid, unpredictable and unreusable. For these causes, OCP says that you should design
modules that *never change*. This may sound contradictory, but there are several techniques for achieving the OCP
on a large scale. All of these techniques are based upon abstraction.

##### Abstraction is the Key

The abstractions are abstract base classes, and all possible derivative classes represent the inﬁnite group of possible behaviors. An abstraction can be manipulated by a module. Since it is based on a fixed abstraction, such a module can be closed for modification. However, the behavior of that module can be extended by creating new abstraction derivatives.

Let's look at the Example

```C++
enum BillType
{
    electricity,
    water,
    broadband
};

struct Bill
{
    BillType itsType;
};

struct ElectricityBill
{
    BillType itsType;
    unsigned int units_consumed;
    unsigned int time_period;
};

struct WaterBill
{
    BillType itsType;
    unsigned int units_consumed;
    unsigned int time_period;
};

struct BroadbandBill
{
    BillType itsType;
    unsigned int units_consumed;
    unsigned int time_period;
};

//
// These functions are implemented elsewhere
//
void CalculateElectricityBill(struct ElectricityBill *);
void CalculateWaterBill(struct WaterBill *);
void CalculateBroadbandBill(struct BroadbandBill *);

typedef struct Bill *BillPointer;

void CalculateAllBills(BillPointer list[], int n)
{
    for (int i = 0; i < n; i++)
    {
        struct Bill *s = list[i];
        switch (s->itsType)
        {
        case electricity:
            CalculateElectricityBill((struct ElectricityBill *)s);
            break;
        case water:
            CalculateWaterBill((struct WaterBill *)s);
            break;
        case broadband:
            CalculateBroadbandBill((struct BroadbandBill *)s);
            break;
        }
    }
}
```

The function `CalculateAllBills` does not conform to the open-closed principle because it cannot be closed against new kinds of bills. If we want to extend this function to calculate the bill for groceries along with other bills, we are forced to modify the function. If other functions similar to `CalculateAllBills` exists (for insance, `PayAllBills`); then switch statement would be repeated over and over again in various functions all over the application.

![OCP](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Focp.jpg?alt=media&token=d94ac893-1e17-4434-8580-c866fab29f21 "OCP")

The below code provides solution to the different bills problem that conforms to the open-closed principle.

```C++
class Bill
{
public:
    virtual void CallculateBill() const = 0;
};

class ElectricityBill : public Bill
{
// member variables for the class
public:
    virtual void CallculateBill() const;
};

class WaterBill : public Bill
{
// member variables for the class
public:
    virtual void CallculateBill() const;
};

class BroadbandBill : public Bill
{
// member variables for the class
public:
    virtual void CallculateBill() const;
};

// This is the new extended class to calculate grocery bill without modifying CalculateAllBills
class GroceryBill : public Bill
{
// member variables for the class
public:
    virtual void CallculateBill() const;
};

void CalculateAllBills(std::set<Bill *> &list)
{
    for (Iterator<Bill *> i(list); i; i++)
        (*i)->CallculateBill();
}
```

Note that if we want to extend the behavior of the `CalculateAllBills` function to calculate a total for a new kind of bill, all we need to do is add a new derivative of the `Bill` class. The `CalculateAllBills` function does not need to change. Thus `CalculateAllBills` conforms to the open-closed principle. Its behavior can be extended without modifying it.


#### Principle 3: L - Liskov Substitution Principle (LSP)

Liskov's principle tends to be the most difficult to understand. The principle states that you should be able to replace any instances of a parent/base class with an instance of one of its children/derived without creating any unexpected or incorrect behaviors.

>*Derived types must be substitutable for their base types - Barbar Liskov*

Derived classes may have different functionality, but their behavior must be in line with that of the base class. It should also follow the implied behavior of the base class, in order to be considered a true behavioral derived type. LSP is generally violated if a derived type of a base type does something the base type's client does not expect.

Let's look at an example that violates LSP

```C++
class Vehicle {
public:
    void startEngine() {
        // start the engine
    }

    void pressAccelerate() {
        // accelerate
    }

    void pressBreak() {
        // break or slow down
    }
};

class Car: public Vehicle {

};

class MotorCycle: public Vehicle {

};

class Bicycle: public Vehicle {
public:

    // start engine not possible in bicycle
    void startEngine() {
        // throw exception
    }
};


void startEngineAllVehicle(std::vector<Vehicle*> allVehicle)
{
    // iterate through all objects and start the engine
}


int main()
{
    std::vector<Vehicle*> allVehicle;
    allVehicle.push_back(new Car);
    allVehicle.push_back(new MotorCycle);

    // Well - all works as of now.
    startEngineAllVehicle(allVehicle);

    // Now adding new object of Bicycle
    allVehicle.push_back(new Bicycle);

    // Again start engine for all vehicle & Oops it broke the program.
    // Why we got unexpected behavior in client? --- Because LSP is violated in class Bicycle, 
    // as it changed the original behavior of base class Vehicle
    playVideoInAllMediaPlayers(allPlayers);
}
```

The above example violates the Liskov Substitution principle because the `Bicycle` class implements the `startEngine` interface but it throws exception because the `Bicycle` class doesn't have an engine to start. LSP suggests that the subtype must be substitutable for the base class or base interface.

![LSP](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Flsp.jpg?alt=media&token=7194e302-c0ad-460d-bd07-1044c04b0427 "LSP")

##### Conforming to the Liskov Substitution Principle

Let's refactor the code to make "good" design using LSP.

```C++
class Vehicle {
public:
    void pressAccelerate() {
        // accelerate
    }

    void pressBreak() {
        // break or slow down
    }
};

class FuelableVehicle: public Vehicle {
public:
    void startEngine() {
        // start the engine
    }
}

class Car: public FuelableVehicle {

};

class MotorCycle: public FuelableVehicle {

};

class Bicycle: public Vehicle {
public:

};
```

The `Bicycle` remains a direct subclass of the `Vehicle` class unless we are able to identify a subgroup of vehicles with specific functionality that the `Bicycle` class belongs. Now our abstraction hierarchy is logically sound because the `Vehicle` class only contains functionality that all the concrete vehicles implement. The same is true for `FuelableVehicle`.


#### Principle 4: I - Interface Segregation Principle (ISP)

The ISP states that no client should be forced to depend on methods it does not use. If you have a class that has several clients, rather than loading the class with all the methods that the clients need, create specific interfaces for each client and multiply inherit them into the class.

>*Many client specific interfaces are better than one general purpose interface*

ISP guide us to creating multiple, smaller, cohesive interfaces when there are non-cohesive interfaces. The implementation of smaller interfaces improves flexibility and reuse ability. By sharing interfaces among fewer classes, the number of changes required to respond to a change in an interface is reduced, resulting in higher robustness.

Let’s look at the below `Vehicle` interface

```C++
class Vehicle {
public:
    void pressAccelerate() {
        // accelerate
    }

    void pressBreak() {
        // break or slow down
    }

    void openDoor() {
        // open the Doors
    }

    void moveSideStand() {
        // move sidestand
    }
};


class Car: public Vehicle {
public:
    void moveSideStand() {
        // can not be implemented
    }
};

class MotorCycle: public Vehicle {
public:
    void openDoor() {
        // can not be implemented
    }
};
```

As you can see, it does not make sense for a `MotorCycle` class to implement the `openDoor()` method as a Motor Cycle does not have any doors! To fix this, ISP proposes that the interfaces be broken down into multiple, small cohesive interfaces so that no class is forced to implement any interface, and therefore methods, that it does not need. Also, ISP states that interfaces should perform only one job (just like the SRP principle) any extra grouping of behavior should be abstracted away to another interface.

![ISP](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Fisp.jpg?alt=media&token=5685d170-d420-4f6b-98ca-479ffbe903cc "ISP")

Here, our `Vehicle` interface performs actions that should be handled independently by other interfaces. To make our `Vehicle` interface conform to the ISP principle, we segregate the actions to different interfaces.

```C++
class Vehicle {
public:
    void pressAccelerate() {
        // accelerate
    }

    void pressBreak() {
        // break or slow down
    }
};


class Car: public Vehicle {
public:
    void openDoor() {
        // open the Doors
    }
};

class MotorCycle: public Vehicle {
public:
    void moveSideStand() {
        // move side Stand
    }
};
```
If any class might need both the `openDoor()` method and the `moveSideStand()` method, it will implement both interfaces.

#### Principle 5: D - Dependency Inversion Principle (DIP)

Robert Martin defines the Dependency Inversion Principle as:

>* *High-level modules should not depend on low-level modules. Both should depend on abstractions.*
>* *Abstractions should not depend on details. Details should depend on abstractions.*

In traditional software architecture we design lower level components to be used/consumed by higher level components. In other words, higher level components depend on lower level components. This dependency causes tight coupling in the software. we strive to achieve loose coupling to make it easier to develop, maintain and change code in the future.

I know this sounds a bit complex, but if you apply the open-closed and Liskov substitution principles to your application, it will already follow the dependency inversion principle. The goal is not to have a tightly-coupled system where every module directly references lower-level modules. That's why you need to abstract that out to get to a more loosely-coupled system. 

Let's look at Bad design that violets DIP

```C++
class Light {  // Low-Level
public:
    bool currentState = false; 
    
    void turnOn() {
        currentState = true;
    }
    
    void turnOff() {
       currentState = false;
    }
    
    bool getState() {
        return currentState;
    }
    
    std::string getStateString() {
        if (currentState) {
            return std::string("On");
        } else {
            return std::string("Off");
        }
    }
};

class SwitchButton { // High-Level
public:
    Light* light;

    SwitchButton(Light* lig) {
        this->light = lig;
    }
    
    void On() {
        this->light->turnOn();
    }
    
    void Off() {
        this->light->turnOff();
    }
};

int main() {
    Light* lig = new Light();
    SwitchButton* sw = new SwitchButton(lig);

    std::cout << lig->getStateString() << std::endl; // OFF

    sw->On();
    std::cout << lig->getStateString() << std::endl; // ON
}
```

At this point, we would want to know which classes are high level, and which are low level? - In general, the class that uses another class is the high level class. So our `SwitchButton` class is the high level class and the `Light` class is the low level class.

##### How Does This Violate DIP?

- The `SwitchButton` class is dependent on the `Light` class.
- We cannot re-use the `SwitchButton` class without also including the `Light` class.
- Any update, like creating a new class which needs to be turned on or off by our `SwitchButton` cannot be added without modifying the `SwitchButton` class itself.

![DIP](https://firebasestorage.googleapis.com/v0/b/pratik-blog10.appspot.com/o/CPP%2FDesign%20Patter%2FSOLID%2Fdip.jpg?alt=media&token=cd2342c5-ae4e-4454-8ee7-47e6cfd48482 "DIP")

##### How Do We Conform To The DIP?

To conform with the DIP, we must invert the dependancies of our code.

- `SwitchButton` must not depend on `Light`, both must depend on abstractions. 
- We must be able to easily re-use `SwitchButton` and `Light`,  `SwitchButton` must not reference `Light` directly.

```C++
class DeviceInterface {
   void turnOn();
   void turnOff();
};

class Light: public DeviceInterface {
public:
    bool currentState = false;

    void turnOn() {
        currentState = true;
    }

    void turnOff() {
        currentState = false;
    }

    bool getState() {
        return currentState;
    }

    std::string getStateString() {
        if(currentState) {
            return std::string("On");
        } else {
            return std::string("Off");
        }
    }
};

class ButtonInterface {
public:
    void On();
    void Off();
};


class SwitchButton: public ButtonInterface {
public:
    DeviceInterface* d_inf;

    SwitchButton(DeviceInterface* dev_inf) {
        this->d_inf = dev_inf;
    }

    void On() {
        this->d_inf->turnOn();
    }
    
    void Off() {
        this->d_inf->turnOff();
    }
}

int main() {
    Light* lgt = new Light();
    SwitchButton* sw = new SwitchButton(lgt);

    std::cout << lgt->getStateString() << std::endl; // OFF

    sw->On();

    std::cout << lgt->getStateString() << std::endl; // ON
}
```

Our updated `SwitchButton` and `Light` now both depend on abstractions, `ButtonInterface` and `DeviceInterface` respectively. The code works for any implementation and we can pass the implementation when creating the object. This gives us lot of flexibility and it will be easy to maintain.

### Conclusion

Implementing SOLID design principles during development will result in more maintainable, scalable, testable, and reusable systems. Engineers worldwide use these principles in today's environment. As a result, it's critical to use these principles when writing good code and applying design principles that are competitive while meeting industry standards. All what we need to do is to **start thinking about the design a little bit before rushing into coding**.
