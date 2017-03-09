# Learning Design Patterns

Helping me learn (only the ['best'](http://www.yegor256.com/2016/02/03/design-patterns-and-anti-patterns.html)) Object Orientated [design patterns](https://github.com/kamranahmedse/design-patterns-for-humans) with real world scenarios.

**Contents**
* [Creational](#creational)
  * [Abstract Factory B+](#abstract-factory-b)
  * [Factory Method B](#factory-method-b)
  * [Prototype A](#prototype-a)
* [Structural](#structural)
  * [Adapter A](#adapter-a)
  * [Bridge A](#bridge-a)
  * [Composite A](#composite-a)
  * [Decorator A++](#decorator-a)
  * [Proxy B](#proxy-b)
* [Behavioral](#behavioral)
  * [Chain of Responsibility B+](#chain-of-responsibility-b)
  * [Command B+](#command-b)
  * [Observer B](#observer-b)
  * [Strategy A](#strategy-a)
* [Architectural](#architectural)
  * [Interpreter B](#interpreter-b)
  * [Specification B](#specification-b)
* [Other](#other)
  * [Object Pool A](#object-pool-a)
  * [Lazy Initialization B+](#lazy-initialization-b)
  * [Null Object A](#null-object-a)
  * [RAII A+](#raii-a)




# Creational


## Abstract Factory [B+]
## Factory Method [B]
## Prototype [A]


# Structural

## Adapter [A]
## Bridge [A]
## Composite [A]
## Decorator [A++]

> You offer a base service with additional items, each one of these items extends a base class via an interface. You can create a service then add on additional items to build up a list of multiple functions by chaining them together.

The interface

```java
public interface Coffee {

    int getCost();

    String getDescription();

}
```

The base class. This sets the base variables for each service.

```java
public class BaseCoffee implements Coffee {

    String getDescription() { return "Base coffee"; }
    
    int getCost() {
      int base_cost = 10;
      return base_cost;
    }

}
```

The multiple addon items.

```java
public class MochaCoffee implements Coffee {
  
   private coffee;
   public Mocha(Coffee c) { coffee = c; }
   
   String getDescription() {
       return this.coffee.getDescription() + ", chocolate";
   }
   
   int getCost() {
       int chocolate_cost = 5;
       return this.coffee.getCost() + chocolate_cost;
   }

}

// Add more
public class XXXCoffee implements Coffee {
   
   // ... Same as above
   
}
```
Now lets make coffee!

```java
baseCoffee = new BaseCoffee();
System.out.println(baseCoffee.getCost()); // 10
System.out.println(baseCoffee.getDescription()); // Base coffee

baseCoffee = new MochaCoffee(baseCoffee);
System.out.println(baseCoffee.getCost()); // 15
System.out.println(baseCoffee.getDescription()); // Base coffee, chocolate

baseCoffee = new XXXCoffee(baseCoffee);
System.out.println(baseCoffee.getCost()); // 15 + ...
System.out.println(baseCoffee.getDescription()); // Base coffee, chocolate + ...
```


## Proxy [B]

# Behavioral

## Chain of Responsibility [B+]
## Command [B+]
## Observer [B]
## Strategy [A]


# Architectural

## Interpreter [B]
## Specification [B]


# Other

## Object Pool [A]
## Lazy Initialization [B+]
## Null Object [A]

```java
public interface Animal {
 	void makeSound();
}

public class Dog implements Animal {
	 public void makeSound() {
	 	 System.out.println("woof!");
	 }
}

public class NullAnimal implements Animal {
	 public void makeSound() { 
     // Silence
  }
}
```

To run

```java
String animalType = ''; // or 'dog'
Animal animal;
switch (animalType) {
   case 'dog':
       animal = new Dog();
       break;
   default:
       animal = new NullAnimal();
       break;
}

animal.makeSound(); // ..the null animal makes no sound
```

## RAII [A+]

