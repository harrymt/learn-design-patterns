# Learning Design Patterns

Helping me learn Object Orientated [design patterns](https://github.com/kamranahmedse/design-patterns-for-humans) (['ranked by Yegor256'](http://www.yegor256.com/2016/02/03/design-patterns-and-anti-patterns.html)) with real world scenarios.

I have also gone through the [Elegant Objects](http://www.harrymt.com/learn-design-patterns/elegant-objects/) book. See everything [online](http://www.harrymt.com/learn-design-patterns/).

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
  * [Visitor C](#visitor-c)
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

Pros:
- hide implementation
- Easily test application
- Change design more easily (loose coupling)

Cons:
- Abstraction
- Tight coupling between layers
- Violates interface Segregation sometimes

![Factory Pattern UML](https://www.dotnettricks.com/img/designpatterns/factory.png)

```java
// Product Interface
interface IFactory {
  void drive(int miles);
}

// ConcreteProduct class
class Scooter : IFactory {
  void drive(int miles) { println("Driving " + miles + "km"); }
}
// ConcreteProduct class
class Scooter : IFactory {
  void drive(int miles) { println("Driving " + (miles * 2) + "km"); }
}

// Creator Abstract class
abstract class VehicleFactory { abstract IFactory getVehicle(string name); }

// ConcreteCreator class
class ConcreteVehicleFactory : VehicleFactory {
  override IFactory getVehicle(string name) {
    switch (name) {
    	case "Scooter": return new Scooter();
	case "Bike": return new Bike();
	default: throw new Exception("Vehicle cannot be created");
    }
  }
}
```

```java
// Usage
void main() {
  VehicleFactory factory = new ConcreteVehicleFactory();
  IFactory vehicle = factory.getVehicle("Scooter");
  vehicle.drive(10); // Driving 20km
  vehicle = factory.getVehicle("Bike");
  vehicle.drive(10); // Driving 10km
}
```


## Prototype [A]


# Structural

## Adapter [A]
## Bridge [A]
## Composite [A]
## Decorator [A++]

A wrapper, to wrap a method, intervene the method by applying other functionality before or after it happens.

![Decorator UML diagram](https://prashantbrall.files.wordpress.com/2011/02/decorator-pattern-1.png?w=595)


Allows you to easily chain lots of different classes together, with a history, so calling one method, calls the same method on all the other classes that are chained before it.

> You offer a base service with additional items, each one of these items extends a base class via an interface. You can create a service then add on additional items to build up a list of multiple functions by chaining them together.

**Is SOLID?**
Yes, supports Open for extension, closed for modification principle

**Pros**
- At RUNTIME we can create different combinations of functionality.
- So solves permutation issues, e.g. have to create N possible combinations of these classes 

**Cons**
- If you rely too much on lots of concrete decorators, results in lots of little classes with overuse being a problem


The interface:

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

This is like the base decorator:

```java
public abstract class CoffeeDecorator implements Coffee {
	Coffee decoratedCoffee;
	CoffeeDecorator(Coffee c) { this.decoratedCoffee = c; }
	
    	String getDescription() { return decoratedCoffee.getDescription(); }
	int getCost() { return decoratedCoffee.getCost(); }
}
```

Another decorator:
```java
public class MochaCoffee extends CoffeeDecorator {
   public Mocha(Coffee c) { super(c); }
   
   String getDescription() {
       return this.coffee.getDescription() + ", chocolate";
   }
   
   int getCost() {
       int chocolate_cost = 5;
       return this.coffee.getCost() + chocolate_cost;
   }
   
   int aNewMethod() {
   	// Additional functionality!
   }

}

// Add more decorators
public class XXXCoffee extends CoffeeDecorator {
   public XXXCoffee(Coffee c) { super(c); }
   
   // ... Same as above
   
}
```
Now lets make coffee with the decorators!

```java
Coffee coffee = new BaseCoffee();
System.out.println(coffee.getCost()); // 10
System.out.println(coffee.getDescription()); // Base coffee

coffee = new MochaCoffee(coffee);
System.out.println(coffee.getCost()); // 15
System.out.println(coffee.getDescription()); // Base coffee, chocolate

coffee = new XXXCoffee(coffee);
System.out.println(coffee.getCost()); // 15 + ...
System.out.println(coffee.getDescription()); // Base coffee, chocolate + ...
```


## Proxy [B]

# Behavioral

## Chain of Responsibility [B+]
## Command [B+]

> Use an object (a command) to encapsulate all information needed to perform an action at a later time.

Examples are event handlers, or add additional logging information.

![Command pattern UML](http://www.dofactory.com/images/diagrams/net/command.gif)


*Example:* The `Receiver` is a Light, the `ConcreteCommand` are actions, e.g. `TurnLightOn()`, `TurnLightOff()`. The `Invoker` is a Remote control.

```java
interface Command { void execute(); }

// Concrete Commands
class TurnLightOn : Command { 
  Light light;
  TurnLightOn(Light l) { this.light = l; }
  void execute() { l.on(); }
}

class TurnLightOff : Command {
  Light light;
  TurnLightOff(Light l) { this.light = l; }
  void execute() { l.off(); }
}

class OpenDoor : Command {
  Door door;
  OpenDoor(Door d) { this.door = d; }
  void execute() { d.open(); }
}
```

```java
// Reciever
class Light {
  void on() { print("on"); }
  void on() { print("off"); }
}

// Another reciever
class Door {
  void open() { print("open"); }
  void close() { print("closed"); }
}
```

```java
// Invoker
class RemoteControl {
    Command command;
    void set(Command c) { this.command = c; }
    void executeCommand() { command.execute(); }
}
```

**Usage**
```java
Light light = new Light();
Command switchOn = new TurnLightOn(light);
Command switchOff = new TurnLightOff(light);

RemoteControl remote = new RemoteControl();
remote.set(switchOn);

println("I can now control the lights whenever I want.");
remote.execute(); // light on

println("I can also control other unrelated things, like my door");
Door door = new Door():
Command openDoor = new OpenDoor();
remote.set(openDoor);
remote.execute(); // opens the door

remote.set(switchOff);
remote.execute(); // light off
```


## Observer [B]

> Describes a one to many relationship between objects, so one state changes, the others get notified and update their state automatically.

```java
void main() {
	JobSeeker harry = new JobSeeker("Harry"); // Observers
	JobPostings board = new JobPostings(); // Subject
	board.attach(harry);
	board.attach(new JobSeeker("Matt"));
	
	// This notifies harry and matt
	board.addJob(new JobPost("Software Engineer $$$"));
	
	board.detach(harry):

	// Only notifies matt
	board.addJob(new JobPost("SL engineer"))
}


class JobPost { Public String title; }
class JobSeeker implements Observer {
	String name;
	void onJobPosted(JobPost job) {
		System.out.println("Hello", name, "!", "New job posted", job.title);
	}
}

class JobPostings implements Observeral {
	protected List<Observer> observers = new ArrayList<>();
	void attach(JobSeeker seeker) { observers.add(seeker); }
	void detach(JobSeeker seeker) { observers.remove(seeker); }
	
	void addJob(JobPost job) {
		this.notify(job);
	}
	
	void notify(JobPost job) {
		for (Observer seekers : observers) {
			seekers.onJobPosted(job);
		}
	}
}
```


## Strategy [A]

> Allows easy switching of the algorithm or strategy.

```java
interface SortStrategy {
	int[] sort(int[] array);
}
class BubbleSort implements SortStrategy { int[] sort(int[] array) { ... } };
class QuickSort  implements SortStrategy { int[] sort(int[] array) { ... } };

class Sorter {
	Sorter(SortStrategy algo) {
		this.sorter = algo;
	}
	int[] sort(int[] array) { return this.sorter.sort(array); }
}

// Usage:
Sorter bubble = new Sorter(new BubbleSort())
bubble.sort([3,1,2]);

Sorter quick = new Sorter(new QuickSort());
quick.sort([3,1,2]);
```

Pros:
- Easy to test compared with if's and else's.
- Does not violate the open/close principle
- Removes condition based logic

## Visitor [C]

> Allows easy partitioning/sorting/manipulation of lists of different objects.

```java
Fruit fruits[] = new Fruit[] {
	new Orange(), new Apple(), new Orange(),
	new Apple(), new Orange()
};
```

Have list of Fruit/objects we want to partition into 2 lists, 1 containing apples, the other oranges.


Without visitor pattern, we would need to store new lists when we partition/manipulate them.
We dont get type safety and hard to catch runtime errors.

```java
// BAD WAY
List<Orange> oranges = new ArrayList<Orange>(); // You need these lists here
List<Apple> apples = new ArrayList<Apple>();
for (Fruit fruit : fruits) {
	if(fruit.equals(Orange.class)) oranges.add((Orange)fruit); // No type safety
	if(fruit.equals(Apple.class)) apples.add((Apple)fruit); // Messy
}
```

With Visitor pattern:

```java
// GOOD
FruitPartitioner p = new FruitPartitioner();
for (Fruit fruit : fruits) {
	fruit.accept(p);
}
```

The data is stored in the partitioner variable:

```java
out.println("# Oranges " + p.oranges.size());
out.println("# Apples " + p.apples.size());
```

FruitPartitioner.java
```java
// Actual visitor
class FruitPartitioner implements IFruitVisitor {
	List<Orange> oranges = new ArrayList<Orange>();
	List<Apple> apples = new ArrayList<Apple>();

	@Override
	public void visit(Orange f) {oranges.add(f); }

	@Override
	public void visit(Apple f) { apples.add(f); }
}

// Visitor interface
interface IFruitVisitor {
	public void visit(Orange f);
	public void visit(Apple f);
}
```

Fruit.java and Apple.java, Orange.java and more Fruit.
```java
interface Fruit {
	void accept(IFruitVisitor visitor);
}

class Orange implements Fruit {
	@Override
	public void accept(IFruitVisitor visitor) {
		visitor.visit(this);
	}
}

class Apple implements Fruit {
	@Override
	public void accept(IFruitVisitor visitor) {
		visitor.visit(this);
	}
}
```



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

