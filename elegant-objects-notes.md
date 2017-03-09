## Elegant Objects notes

Notes about Object Orientated Language constructs from the book [Elegant Objects](http://www.yegor256.com/elegant-objects.html).

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

### Never make classes Mutable for 5 reasons

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

### Classes should only have a few public methods!!!!!!

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

