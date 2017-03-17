---
permalink: /elegant-objects/
---
## Elegant Objects notes

Notes about Object Orientated Language constructs from the book [Elegant Objects](http://www.yegor256.com/elegant-objects.html).


## Summary

- [GOOD File.write(), BAD writer](#never-use-er-names)
- [GOOD Cash.usd(), BAD CashFormatter](#object-is-defined-by-what-it-is-rather-than-what-it-does)
- [Few methods, many constructors that call the one below](#have-many-fall-through-constructors-and-few-methods)
- [< 4 properties in classes](#encapsulate-as-little-as-possible-max-4-min-2)
- [Always use interfaces](#always-use-interfaces)
- [Know distinction between builders and manipulators](#builders-and-manipulators)
- [Think about Builder return content, rather than action](#think-about-return-content-rather-than-action-when-naming-builder-methods)
- [Don't use constants, use methods that are specific to that constant value](#dont-use-constants)
- [BAD mutable classes, 4 reasons](#never-make-classes-mutable-for-4-reasons)
   - [1. Unexpected property values](#1-if-objects-can-change-mutable-then-we-end-up-with-situations-like-this)
   - [2. Cant return a partyl modified object](#2-another-advantage-with-immutable-objects-is-failure-atomicity)
   - [3. They don't have setProperty methods](#3-immutable-objects-dont-have-setxxx-methods-therefore-are-not-affected-by-temporal-coupling-and-bad-null)
   - [4. Cannot be edited on the fly](#4-if-an-object-is-mutable-people-can-edit-it-on-the-fly)
- [Max 250 Source LOC per class](#only-use-250-sloc-source-lines-of-code-for-a-class)
- [Instead of documentation write a test](#tests-should-show-people-how-to-use-your-class-not-documentation)
- [Classes should have little public methods](#classes-should-only-have-a-few-public-methods)
- [Never Mock, add Fake test objects into the class interface](#never-use-mock-tests-ie-just-only-create-the-result-use-fake-objects)
- [Add classes inside of interfaces for easy refactor](#use-smart-classes-inside-of-interfaces-so-we-can-easily-refactor-wo-too-much-upset)



### Never use er names

- BAD writer
- GOOD File.write()

### Object is defined by what it IS rather than what it DOES

- `Cash.usd()`, rather `CashFormatter`

### Have many fall through constructors and few methods

- `Cash(12.0)`
- `Cash("12.0")`
- `Cash(12)`

```java
class Cash {
    // Secondary Constructors
    public Cash(double amount) { this((int)amount); }
    public Cash(String amount) { this(Cash.parse(amount)); }
    
    // 1 Primary Constructor
    public Cash(int amount) { 
        // This is where we actually set our variable (this.dollars)
        this.dollars = amount;
    }
}
```

### Code free constructors

- Just assign the items in constructors, don't actually have any logic, otherwise everytime we create an object we will have to perform that logic and its hard to refactor code later and leads to unmaintainable code.

### Encapsulate as little as possible (max 4), min 2

```java
class Cash {
  private Integer digits;
  private Integer cents;
  private String currency;
  // More than 4 properties, is a codesmell and should be refactored into max 4 samller objects.
}
```

### Always use interfaces

```java
interface Cash {
    Cash multiply(float mul);
    int gbp();
}

class DefaultCash implements Cash {
    private int dollars;
    
    DefaultCash(int dollars) {
        this.dollars = dollars;
    }
    @Override
    public int gbp() {
    	return this.dollars;
    }
    
    @Override
	public
    Cash multiply(float mul) {
        return new DefaultCash((int) (this.dollars * mul));
    }
}
```

Run like:

```java
Cash salary = new DefaultCash(10);
System.out.println(salary.multiply(2).multiply(10).gbp());
```

### Builders and Manipulators

```java
int sum(int x, int y); // Builder, because it returns something
void paint(Color c); // Manipulator, because it performs an action
```

### Think about return content rather than action, when naming Builder methods

```java
InputStream load(URL url); // BAD
InputStream stream(URL url); // GOOD!!!
```

```java
String read(File f); // BAD
String content(File f); // GOOD!!
```

```java
int add(int x, int y); // BAD
int sum(int x, int y); // GOOD!!!
```

### Don't use constants

```java
new HttpRequest().method("POST").fetch(); // Bad

class HttpRequest { final String Post = "POST"; } // Bad
new HttpRequest().method(HttpRequest.Post).fetch(); // Bad

class PostRequest { } // Now any changes are specific to the action, rather than generic POST string.
new PostRequest(new HttpRequest()).fetch(); // Good
```

### Never make classes Mutable for 4 reasons

#### 1. If objects can change (Mutable) then we end up with situations like this:

```java
Cash five = new Cash(5);
five.mul(10);
System.out.println(five); // oops its $50, not 5
```

or

```java
Map<Cash, String> map = new HashMap<>();
Cash five = new Cash(5);
Cash ten = new Cash(10);
map.put(five, "five"); map.put(ten, "ten");
five.mul(10);
System.out.println(map); // {10 => "five", 10 => "ten"} WRONG
```


#### 2. Another Advantage with Immutable objects is Failure Atomicity.

If an object is mutable, then half of the object can be altered and the other is not altered, this can be a nightmare to debug!

```java
// BAD
class Cash {
  private int dollars;
  private int cents;
  public void mul(int factor) {
    this.dollars *= factor;
    if (/* something is wrong */) {
      throw new RuntimeException("oops..");
    }
    this.cents *= factor;
  }
}
```

This is better because I cannot return a half edited object.

```java
// GOOD
class Cash {
  private final int dollars;
  private final int cents;
  public void mul(int factor) {
    if (/* something is wrong */) {
      throw new RuntimeException("oops..");
    }
    return new Cash(
       this.dollars * factor,
       this.cents * factor
    );
  }
}
```

#### 3. Immutable objects don't have `setXXX()` methods, therefore are not affected by temporal coupling and bad NULL.

Because they don't have set methods, they rarely use NULL fields, which is good.

```java
// BAD
Cash price = new Cash();
// 50 lines to calculate 29 dollars
price.setDollars(29);

// If I just try and check what price is here, its gonna be an issue:
System.out.println(price); // 29.??? probably 00 but we dont know that!

// 30 lines to calculate 95 cents
price.setCents(95);
// 25 lines to calculate other stuff
System.out.println(price); // 29.95
```

Immutable objects are not affected by temporal coupling:

```java
int dollars = 29;
// 100 lines
int cents = 95;
// 30 lines
Cash price = new Cash(dollars, cents);
System.out.println(price); // 29.95
```

#### 4. If an object is mutable, people can edit it on the fly.

This is also good for Thread Safety, if one thread modifies something then we get unexpected behaviour in the other thread.

```java
// BAD
void harrysprintfunction(Cash price) {
  System.out.println("Todays price is " + price);
  price.mul(2);
  System.out.println("Tomorrow the price is :" + price);
}

// Calling this method has side affects
Cash five = new Cash(5);
harrysprintfunction(five);
System.out.println(five); // 10, not 5!!!
```

### Only use 250 SLOC Source Lines of Code for a CLASS

To make things simpler.


### Tests should show people how to use your class, not documentation

```java
// Perfect code w/o need for documentation
Employee jeff = department.employee("Jeff");
jeff.giveRaise(new Cash(200));
if (jeff.performance() < 3.5) {
  jeff.fire();
}
```

Perfect unit tests for our Cash class, uses Hamcrest and JUnit. This explains how to use our Cash class simply.

```java
class CashTest {
  @Test
  public void summarizes() {
    assertThat(
      new Cash(5).plus(new Cash(3)),
      equalTo(new Cash(8))
    );
  }
  @Test
  public void deducts() {
    assertThat(
      new Cash(7).plus(new Cash(-11)),
      equalTo(new Cash(-4))
    );
  }
  @Test
  public void multiplies() {
    assertThat(
      new Cash(2).mul(3),
      equalTo(new Cash(6))
    )
  }
}
```

### Classes should only have a few public methods

Again to make things simpler, if we find we need more, try and break down into smaller objects.

### Never use Mock tests, i.e. just only create the result, use fake objects!

This is an example of how to encourperate your Fake objects into an interface, so the tests are in the same place.

```java
interface Exchange {
  float rate(String target);
  float rate(String origin, String target);
  
  float class Fake implements Exchange {
    @Override
    float rate(String target) {
      return this.rate("USD", target); // Our fake currency
    }
    @Override
    float rate(String origin, String target) {
      return 1.2345; // Our fake rate.
  }
}
```

### Use 'Smart' classes inside of interfaces, so we can easily refactor w/o too much upset.

```java
interface Exchange {
  float rate(String origin, String target);
  
  // Smart Class
  final class Fast implements Exchange {
    private final Exchange origin;
    
    @Override
    public float rate(String source, String target) {
      final float rate;
      if (source.equals(target)) { // If we are converting USD == USD
        rate = 1.0f;
      } else {
        rate = this.origin.rate(source, target); // the one at the top.
      }
      return rate;
    }
    
    // Smart method
    public float toUsd(String source) {
      return this.origin.rate(source, "USD");
    }
  }
}
```

### Don't use (Static methods / Utility functions / Singleton Pattern)

The reason we don't use these, is so we don't have to perform any calculations until we actually want the content, and call `.content()` on the `WebPage` object.

```java
// BAD
class WebPage {
   public static String read(String uri) {
      // Make HTTP req & convert to UTF8 string
   }
}

// BAD
String html = WebPage.read("http://harrymt.com");
```

```java
// GOOD
class WebPage {
  private final String url;
  public String content() {
      // Make HTTP req & convert to UTF8 string
  }
}

// GOOD
String html = new WebPage("http://harrymt.com").content();
```

#### Use 'Composable Decorators'

```java
// GOOD
names = new Sorted(
   new Unique(
       new Capitalized(
           new FileNames (
	       new Directory("/var/users/*.xml")
	   ),
	   "([^.]+\\).xml"
       )
   )
);
```

- Stray away from long procedural code using whiles, ifs and switch statements

```java
// BAD
float rate;
if(client.age() > 65) {
  rate = 2.5;
} else {
  rate = 3.0;
}
```

```java
// Better
float rate = new If(
  client.age() > 65,
  2.5, 3.0
);

// Even better
float rate = new If(
  new Greater(client.age(), 65),
  2.5, 3.0
);

// GOOD
float rate = new If(
  new GreaterThan(
    new AgeOf(client),
    65
  ),
  2.5, 3.0
);

```


### Never accept NULL arguments

> Cause lots of bad problems.

```java
// BAD
public Iterable<File> find(String mask) {
   // Find all files that match the mask
   // Retreive all files, if mask == NULL
}

// BAD because you need this in your code
public Iterable<File> find(String mask) {
  // We are litearlly doing 2 things here, because mask could be NULL!!!
  if(mask == NULL) {
     // Find all files
  } else {
     // Find files by mask
  }
}

// GOOD
public Iterable<File> findAll();
public Iterable<File< find(String mask);

```

- Be ignorant when accepting NULL if people give it to your methods, if it fails, the JVM should through a NullPointerException, which is a really good exception for people using your class to see.

```java
// EVEN BETTER
public Iterable<File> find(Mask mask) {
  Collection<File> files = new LinkedList<>();
  for (File file : /* All files */) {
    if(mask.matches(file)) { // if mask == NULL, will throw NullPointerException!! GOOD!
      files.add(file);
    }
  }
  return files;
}

```


