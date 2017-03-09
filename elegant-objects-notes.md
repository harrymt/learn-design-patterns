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

### Never make classes Mutable

- If objects can change (Mutable) then we end up with situations like this:

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


Another dissadvantage with imutable objects is Failure Atomicity.

```java
// BAD
2.6.2!
```

This is better
```java
// GOOD

```


###
