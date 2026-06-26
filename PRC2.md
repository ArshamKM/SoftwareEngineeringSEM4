# PRC2 — Let Me Actually Explain This To You

This isn't a reference doc you skim. Read it like someone's talking to you. Each topic has a real explanation first, then the exam traps at the end. After each topic there are 2-3 quick questions — answer them mentally before moving on. If you can answer them, you're done with that topic. If you can't, re-read it.

> **Three things settled up front because the notebook contradicts itself:**
> 1. Closing a `Connection` closes its Statements and ResultSets → **TRUE**
> 2. `LocalDateTime` has no offset/zone → **TRUE**
> 3. The bottom of the logging level ladder is **ALL** (logs everything), not OFF

---

# 1. Testing & TDD

**Why does any of this exist?** Your brain is terrible at finding bugs in code you wrote yourself. You're biased — you think it works because you built it. Tests are an automated second opinion that doesn't care about your feelings.

**TDD flips the normal order.** Instead of writing code then testing it, you write the test first. The cycle is:
- **Red** — write a test that fails (it has to fail, the code doesn't exist yet)
- **Green** — write the minimum code to make it pass
- **Refactor** — clean up, knowing the test will catch if you break something

Why write the test first? Because it forces you to think about what the code *should do* before you get lost in *how* to write it. If you can't write a test for something, you probably don't understand what it should do yet.

**A unit test has one job** — test one small, isolated piece of code (usually one method or class). No database. No network. No external systems. Those make tests slow and unpredictable. A unit test should run in milliseconds and always give the same result.

**AAA is how every test is structured:**
- **Arrange** — set up the thing you're testing and its dependencies
- **Act** — call the one method you're testing
- **Assert** — check the result

The golden rule: *code that isn't written can't be wrong.* Don't add a method just because it seems useful — add it when a failing test demands it.

One trap on equals/hashCode: if two objects are equal, they must have the same hashCode. The reverse is NOT true — two objects can have the same hashCode without being equal. Think of it like postcodes: two people in the same street have the same postcode, but the postcode alone doesn't identify the person.

**Quick check:**
1. In TDD, do you write the test before or after the code?
2. What makes a test a "unit" test — what does it NOT depend on?
3. If `a.equals(b)` is true, what must also be true about their hashCodes?

---

# 2. JUnit 5 & AssertJ

**JUnit just finds and runs your tests.** Put `@Test` on a method and JUnit will run it. That's the core of it. If an assertion inside throws an exception, the test fails. If nothing throws, it passes.

**AssertJ makes assertions readable.** The pattern is always `assertThat(what you got).someCheck()`:

```java
assertThat(result).isEqualTo(5);           // equals check
assertThat(name).contains("hello");         // string contains
assertThat(list).hasSize(3);               // collection size
assertThat(value).isNotNull();             // null check
assertThat(flag).isTrue();                 // boolean
```

`isEqualTo` uses `.equals()`. `isSameAs` checks reference identity (same object in memory, like `==`). Those are different things.

**Parameterized tests exist so you don't copy-paste.** Instead of writing the same test 10 times with different inputs, you write it once and feed it data:

```java
@ParameterizedTest
@ValueSource(ints = {2, 4, 6, 8})
void isEven(int n) {
    assertThat(n % 2).isEqualTo(0);
}
```

JUnit runs this 4 times. Data sources you need to know: `@ValueSource` (simple values), `@CsvSource` (multiple columns, inline), `@MethodSource` (data comes from a static method in the class).

Three rules for parameterized tests that the exam loves:
- Must use `@ParameterizedTest`, NOT `@Test` — you can't combine them
- Must have at least one data source annotation — it can't run with no data
- Must NOT be private — JUnit needs to call it

**Soft assertions solve a specific problem.** Normal assertions stop at the first failure. If you have 5 checks and the first fails, you never see the other 4. Soft assertions collect all failures and report them at the end. But — and this is the trap — **you must call `softly.assertAll()` at the end**. Forget it and literally nothing is verified. The test passes even if everything is wrong.

**Testing that an exception IS thrown:**

```java
// Clean way:
assertThrows(ArithmeticException.class, () -> divide(10, 0));

// Old way — only OK if you add fail():
try {
    divide(10, 0);
    fail("Expected ArithmeticException"); // ← this line is crucial
} catch (ArithmeticException e) {
    assertEquals("/ by zero", e.getMessage());
}
```

Why is the bare try-catch without `fail()` bad? If `divide(10, 0)` somehow doesn't throw, execution just skips past the catch block and the test passes. You think you tested something but you tested nothing. The `fail()` call is the safety net that catches this.

Golden rule: **never write a bare try-catch in a test.** Use `assertThrows`, or include `fail()`.

**Assumptions** let you skip a test when a precondition isn't met. `assumeTrue(someCondition)` — if the condition is false, the test is skipped (not failed). Shows up as skipped in the results, like `@Disabled`.

**Quick check:**
1. What's wrong with: `@Test @ParameterizedTest void myTest(int x) {}`
2. You write soft assertions but forget `assertAll()`. The test passes. Is that good?
3. Why does a bare try-catch without `fail()` give you a false sense of security?

---

# 3. Testability & Mocking

**Testability is a design quality, not a testing quality.** If your code is hard to test, it's not a testing problem — it's a design problem. Hard-to-test code is usually hard-to-change code too.

Three things make code testable:
- **Controllability** — can you put the system into the exact state you need to test? If everything depends on a live database, you can't.
- **Observability** — can you see what the code did? If a method returns void and writes to a log file, how do you check it worked?
- **Smallness** — is the thing you're testing small enough to reason about? A 500-line class with 20 responsibilities is nearly impossible to test well.

**The big design rule: inject dependencies, don't create them.** If a class creates its own database connection with `new Connection(...)` inside a method, you can never test it without a real database. But if the connection is passed in from outside (through the constructor or a setter), you can pass a fake one in your tests. That's **dependency injection** — passing dependencies in from outside instead of creating them inside.

**Test doubles are the fakes you pass in.** There are three types and the exam really wants you to know the difference:

- **Stub** — returns fake data when called. You just need it to return something so your code doesn't crash. You don't care if it was called or how many times.
- **Mock** — a fake that you *verify* was used correctly. You check "was this method called? With what arguments?" Mockito's `verify()` is for mocks.
- **Spy** — wraps a real object. It behaves normally by default, but you can track its calls or override specific methods. The real code runs for everything you haven't overridden.

The biggest exam trap: **never mock the SUT (the thing you're testing)**. You mock the *dependencies* of the SUT. If you mock the SUT itself, you're not testing any real code — you're testing the mock. It's like testing whether a stunt double can act — it tells you nothing about the real actor.

**Mockito in one paragraph:** `mock(SomeClass.class)` creates a mock. `when(mock.method()).thenReturn(value)` trains it. `verify(mock).method()` checks it was called. Argument matchers like `any()`, `anyString()`, `eq("specific")` let you be flexible or precise about what arguments you're checking for.

**Quick check:**
1. What's the difference between a stub and a mock?
2. You mock the class you're trying to test. What's wrong with this?
3. What does "dependency injection" mean in one sentence?

---

# 4. Mutation Testing (PIT)

**Here's the problem:** you can write 100 tests and still have terrible tests. Tests that always pass, even on broken code, are worthless. But how do you know if your tests are any good?

**Mutation testing answers this.** PIT takes your code, makes a tiny change to it (a *mutation* — flips `+` to `-`, changes `>` to `<`, changes `true` to `false`), and then runs your tests against the broken code. If your tests catch the change, the mutant is **killed** — good. If your tests all still pass despite the code being wrong, the mutant **survived** — your tests have a blind spot.

The **mutation score** is the percentage of mutants killed. Higher is better. A 90% mutation score means 90% of the small bugs PIT introduced were caught by your tests.

The purpose isn't to test your code's correctness — it's to **assess the quality of your tests**. That distinction matters for the exam.

**Quick check:**
1. A mutant survived. What does that tell you?
2. What's the mutation score and why does higher = better?
3. PIT assesses the quality of your \_\_\_\_\_, not your \_\_\_\_\_.

---

# 5. Enums

**An enum is a fixed, closed set of constants that are also real objects.** Think of the days of the week — there are exactly 7, you'll never add an 8th, and each one is a specific named thing. That's what enums are for.

Because each constant is an actual object (an instance of the enum type), enums can do a lot more than you might expect:

```java
enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6);

    private final double mass;
    Planet(double mass, double radius) {  // constructor — always private
        this.mass = mass;
    }
}
```

Things enums CAN do: have fields, have private constructors, declare static methods, implement interfaces, declare abstract methods (but then each constant must implement them).

Things enums CANNOT do: be extended (they're implicitly `final`), extend a class (they already extend `java.lang.Enum` implicitly).

That last point is a classic trap: **enums cannot extend a class**. They can implement interfaces — that's different.

**Quick check:**
1. Can an enum implement an interface?
2. Can an enum extend another class?
3. Enum constructors must always be \_\_\_\_\_.

---

# 6. Generics

**Generics exist because Java is strongly typed but you want reusable code.** Without generics, a list that holds anything has to hold `Object`, and you'd need casts everywhere and could accidentally add the wrong type. With generics, `List<String>` tells the compiler "this only holds Strings" and it enforces that at compile time.

```java
class Box<T> {        // T is a placeholder for any type
    T content;
}
Box<String> b = new Box<>();  // T becomes String
Box<Integer> c = new Box<>(); // T becomes Integer
```

**The catch: type erasure.** Generics are a compile-time feature only. After the compiler checks everything, it removes the type info. At runtime, `List<String>` and `List<Integer>` are both just `List` — the `<String>` and `<Integer>` are gone. This is called erasure, and it explains every "gotcha" in generics:

- `new T()` — **ILLEGAL**. At runtime the JVM doesn't know what T is.
- `new T[10]` — **ILLEGAL**. Same reason.
- `Box<int>` — **ILLEGAL**. Primitives aren't allowed; use `Box<Integer>` instead.
- `static T value` — **ILLEGAL**. Static members are shared across all instances of `Box`, so "which T?" has no answer.
- Raw type (`ArrayList al = new ArrayList()`) — **compiles with a warning**. Not illegal, just unsafe.

**Generics are invariant** — this trips people up constantly. Even though `Circle` is a `Shape`, `List<Circle>` is NOT a `List<Shape>`. They're completely different types. You can't pass a `List<Circle>` where a `List<Shape>` is expected.

**Wildcards fix this (PECS is the memory trick):**
- `? extends Shape` — you can READ from this list (get Shapes out), but you can't ADD to it. Think of it as a **Producer** of Shapes. **P**roducer **E**xtends.
- `? super Circle` — you can ADD Circles to this list, but reading gives you `Object`. Think of it as a **Consumer** of Circles. **C**onsumer **S**uper.
- **PECS: Producer Extends, Consumer Super.**

**Quick check:**
1. Why is `new T()` illegal in a generic class?
2. `Circle extends Shape`. Is `List<Circle>` a subtype of `List<Shape>`?
3. You want to read elements out of a list. Should you use `? extends T` or `? super T`?

---

# 7. Lambdas, Functional Interfaces & Streams

**A lambda is just a short way of writing a method that you pass around.** It works because of functional interfaces — an interface with exactly one abstract method. The lambda provides the body of that one method.

```java
// These do the same thing:
Comparator<String> c1 = new Comparator<String>() {
    public int compare(String a, String b) { return a.compareTo(b); }
};
Comparator<String> c2 = (a, b) -> a.compareTo(b);  // lambda version
```

A functional interface has **exactly one abstract method**. It CAN have any number of `default` and `static` methods — those don't count. The `@FunctionalInterface` annotation is optional — it just tells the compiler to enforce the rule. Without it, you can still use an interface as a functional interface as long as it has exactly one abstract method.

**Common functional interfaces from `java.util.function`:**
- `Supplier<T>` — takes nothing, returns T: `() -> "hello"`
- `Consumer<T>` — takes T, returns nothing: `s -> System.out.println(s)`
- `Function<T,R>` — takes T, returns R: `s -> s.length()`
- `Predicate<T>` — takes T, returns boolean: `s -> s.isEmpty()`

**Streams are a pipeline for processing data.** Three parts: a source, some intermediate operations, a terminal operation.

```java
list.stream()                    // source
    .filter(s -> s.length() > 3) // intermediate (lazy)
    .map(String::toUpperCase)     // intermediate (lazy)
    .collect(toList());           // terminal (triggers everything)
```

The key thing about streams: **intermediate operations are lazy**. Nothing actually runs until you hit a terminal operation. You can chain 10 `filter` and `map` calls and nothing has happened yet. The terminal operation is what pulls data through the pipeline and produces a result.

**Intermediate ops** (lazy, return a Stream): `filter`, `map`, `sorted`, `distinct`, `limit`, `peek`.
**Terminal ops** (eager, return a result): `collect`, `forEach`, `reduce`, `count`, `findFirst`, `anyMatch`.

A pipeline has **exactly one terminal operation**, always at the end. You can't have two terminal operations on the same stream — after a terminal op the stream is consumed and gone.

**Quick check:**
1. `@FunctionalInterface` is required for an interface to work as a functional interface. True or false?
2. You add 5 `filter()` calls to a stream. When do they actually run?
3. Can a stream pipeline have two terminal operations?

---

# 8. Exceptions

**Exceptions exist because sometimes things go wrong that aren't your fault.** A file doesn't exist. A network connection drops. A database is down. Java gives you a formal way to handle these situations instead of returning weird null values or magic numbers.

**The hierarchy matters:**
```
Throwable
├── Error              (JVM problems — OutOfMemoryError, StackOverflowError)
│                       Don't catch these. You can't recover.
└── Exception
    ├── RuntimeException   (unchecked — NullPointer, ArrayIndexOutOfBounds)
    │                       Programming mistakes. Fix the code, don't catch it.
    └── Everything else    (checked — IOException, SQLException)
                            External problems. Compiler forces you to handle.
```

**Checked vs unchecked is about who's responsible:**
- **Checked** exceptions are things that can go wrong in the outside world that you should anticipate — file not found, network error, SQL failure. The compiler forces you to either catch them or declare them with `throws`. Examples: `IOException`, `SQLException`, `ClassNotFoundException`.
- **Unchecked** exceptions are programming bugs — you tried to use a null reference, accessed an array out of bounds, divided by zero. The right fix is to fix the code, not wrap it in a try-catch. Examples: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`.

**`throw` vs `throws` — completely different things:**
- `throw` is an action: `throw new IOException("file not found");` — actually throws it right now.
- `throws` is a declaration: `void readFile() throws IOException` — warns callers that this method might throw.

You can have both in the same method. That's perfectly legal.

**`finally` always runs** — its whole point is cleanup that must happen regardless of what went wrong. The only exceptions (ironically) are `System.exit()` being called, or the JVM crashing. So the exam answer is "almost always."

**A try block needs at least one of:** a catch block OR a finally block. `try` alone with nothing after it is a compile error. `try-with-resources` has an implicit finally, so it works without an explicit catch.

**The catch/finally flow — memorise the middle row:**

| What happens | catch runs? | finally runs? | code after? |
|---|---|---|---|
| Exception thrown & caught | ✅ | ✅ | ✅ |
| Exception thrown, NOT caught | ❌ | ✅ | ❌ |
| No exception | ❌ | ✅ | ✅ |

The middle row is the sneaky one. Exception not caught → finally still runs → but code after the try-catch doesn't.

**Multi-catch traps:**
- Two catches for the same type → compile error
- `catch (IOException | Exception e)` → compile error, because `Exception` already covers `IOException` — you're catching the same thing twice

**Quick check:**
1. `NullPointerException` — checked or unchecked?
2. Exception is thrown but not caught. Does `finally` run?
3. `catch (IOException | Exception e)` — does this compile?

---

# 9. Immutability + BigInteger/BigDecimal

**Immutable means the object's state can never change after it's created.** You can't modify it — you can only create a new one. Strings are the classic Java example.

This trips people up constantly:

```java
String s = "hello";
s.concat("world");         // This creates a new String but THROWS IT AWAY
System.out.println(s);     // Still prints: hello

s = s.concat("world");     // NOW you're reassigning s to the new String
System.out.println(s);     // Prints: hello world
```

`s.concat("world")` doesn't change `s`. It returns a new String. If you don't assign it back, it's gone. `s` still points to "hello."

**BigInteger and BigDecimal** are immutable too, and they exist because `int`/`long` have size limits and `float`/`double` have rounding errors.

- `BigInteger` — for integers too large for `long`. No decimal point. Immutable.
- `BigDecimal` — for decimal numbers that need to be exact. Use for money — never use `double` for money.

One trap: `new BigDecimal(0.1)` is WRONG — it inherits the floating-point imprecision of `0.1` as a `double`. Use `new BigDecimal("0.1")` (pass a String) to get the exact value.

**Quick check:**
1. What does `s.concat("world")` return, and what happens to `s`?
2. Which type would you use for a bank balance — `double` or `BigDecimal`?
3. `new BigDecimal(0.1)` vs `new BigDecimal("0.1")` — which is correct and why?

---

# 10. Reflection & the Object class

**Reflection lets your code examine itself at runtime.** Normally you know everything at compile time — you call `dog.bark()` and the compiler checks that `Dog` has a `bark()` method. With reflection, you can discover and call methods dynamically at runtime, even if you didn't know they existed when you wrote the code.

This is how frameworks work. JUnit finds all your `@Test` methods without you telling it what they are — it uses reflection to scan the class. Mockito creates mock objects by inspecting the class structure at runtime.

**Every class in Java implicitly extends `Object`** — it's the root of the entire class hierarchy. Because of this, every object has these methods for free: `toString()`, `equals()`, `hashCode()`, and `getClass()`.

`getClass()` is important for reflection — it returns the **runtime type** of the object as a `Class<?>`:

```java
Object obj = new String("hello");
System.out.println(obj.getClass()); // prints: class java.lang.String
```

Even though `obj` is declared as `Object`, at runtime it's actually a `String`. `getClass()` tells you the real type.

**Three ways to get a `Class` object:**
- `obj.getClass()` — instance method, gives runtime type
- `String.class` — compile-time literal
- `Class.forName("java.lang.String")` — by name, can throw `ClassNotFoundException`

**Exam trap:** `Object.getClass()` — this looks valid but it's **wrong**. `getClass()` is an instance method, not a static one. You can't call it on the class itself, only on an instance.

✅ `Object.class` — valid (class literal)
✅ `new Object().getClass()` — valid (instance method on an instance)
❌ `Object.getClass()` — invalid (treating an instance method like a static one)

**Risks of reflection:** it's slower than normal code, it can break encapsulation (you can access private fields with `setAccessible(true)`), and it bypasses compile-time type checking — meaning bugs that the compiler would have caught now only show up at runtime.

**Quick check:**
1. Every class in Java implicitly extends what?
2. What does `getClass()` return?
3. Is `Object.getClass()` valid? Why or why not?

---

# 11. Annotations

**Annotations are labels you attach to code that carry extra information.** They don't change what your code does on their own — they're metadata. The magic happens when something (usually a framework using reflection) reads those labels and acts on them.

`@Test` on a method is just a label. JUnit then uses reflection to find all methods with that label and run them. Without reflection, the annotation does nothing.

**Built-in ones you know:** `@Override` (tells compiler you're overriding a method), `@Deprecated` (warns that something is old), `@SuppressWarnings`.

**You can define your own:**

```java
@Retention(RetentionPolicy.RUNTIME)  // keep it at runtime so reflection can read it
@Target(ElementType.FIELD)           // only allowed on fields
public @interface Hidden {
    String reason() default "not specified";
}
```

**`@Retention` is critical.** Three options:
- `SOURCE` — only in source code, gone after compilation
- `CLASS` — in the bytecode but not at runtime
- `RUNTIME` — stays at runtime, readable by reflection

If you want a framework to read your annotation, it **must be `RUNTIME`**. If it's `SOURCE` or `CLASS`, reflection can't see it.

**`@Target`** restricts where the annotation can be placed — on types, methods, fields, parameters, etc.

**Quick check:**
1. You create an annotation without `@Retention`. Can JUnit read it at runtime?
2. What does `@Target(ElementType.METHOD)` do?
3. Why don't annotations change behaviour on their own?

---

# 12. JPMS — the Module System

**Before Java 9, any public class was visible to everything on the classpath.** Big projects became a mess — internal implementation classes were technically accessible to anyone. JPMS (Java Platform Module System) fixed this by adding a layer above packages.

A **module** is a named group of packages with explicit rules about what it needs and what it shares. You define this in one file: `module-info.java`, placed in the source root.

**The keywords — learn what each one does:**

- `requires X` — "I need module X to compile and run." The JVM checks this at startup; if X is missing, it refuses to start immediately instead of failing later.
- `requires transitive X` — same as requires, but I also pass X through to anyone who depends on me. They get X's **public API**, not private members.
- `exports mypackage` — "Other modules can use the classes in this package." Without this, even public classes in this package are invisible to other modules.
- `opens mypackage` — "Other modules can use reflection on this package." Needed for frameworks like JUnit or Spring that use reflection at runtime. Does NOT mean the same as exports.
- `import` — **not allowed** in module-info.java at all.

**The difference between exports and opens:**
- `exports` = normal access (compile-time, calling methods)
- `opens` = reflective access only (runtime, frameworks inspecting your code)
- They are completely independent. `opens` does NOT automatically `exports`.

**Syntax rules the exam tests:**
- `requires` names a **module**: ✅ `requires java.sql` ❌ `requires com.myapp.api` (that's a package)
- `exports` names a **package in your own module**: ✅ `exports com.myapp.api` ❌ `exports java.sql` (that's someone else's package)
- Same rule for `opens`.
- Modules are not classes — you can't extend them.
- Split packages (same package in two modules) → **forbidden**.
- Cyclic dependencies → **forbidden**.

**The access table:**

| Package is... | Normal access | Reflective access |
|---|---|---|
| Not exported, not opened | ✗ | ✗ |
| Only exported | ✓ | ✗ |
| Only opened | ✗ | ✓ |
| Exported AND opened | ✓ | ✓ |

**Quick check:**
1. `requires com.myapp.api` — what's wrong with this?
2. Does `opens mypackage` also give normal (non-reflective) access to that package?
3. `requires transitive` gives you access to private members of the transitive dependency. True or false?

---

# 13. JDBC

**JDBC is Java's standard way to talk to a relational database.** The clever part is the split: the API (`java.sql`) is vendor-neutral — your code always looks the same. The Driver underneath is vendor-specific — swap MySQL for PostgreSQL by changing the driver, not your code.

```
Your Code
    ↓
JDBC API (java.sql) — always the same
    ↓
Driver (MySQL, PostgreSQL, Oracle…) — swap this to change databases
    ↓
Database
```

**5 interfaces you need to know:**
1. **DriverManager** — opens connections using a JDBC URL
2. **Connection** — one session with the database, AutoCloseable
3. **Statement / PreparedStatement / CallableStatement** — sends SQL
4. **ResultSet** — the rows returned by a SELECT
5. **ResultSetMetaData** — info about the columns (names, types, count)

**The "Vendor"** just means the company that made the database software — Oracle, MySQL, PostgreSQL, etc.

**PreparedStatement vs Statement** — this is a big deal in both security and the exam:

Statement builds SQL by string concatenation — dangerous:
```java
stmt.execute("SELECT * FROM users WHERE name = '" + userInput + "'"); // vulnerable!
```

PreparedStatement has placeholders — safe:
```java
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE name = ?");
ps.setString(1, userInput);  // 1-based!
ps.executeQuery();
```

PreparedStatement: SQL is **fixed** (only `?` parameters change), **compiled once then reused**, **immune to SQL injection**, **1-based** parameter indexes.

**ResultSet — the cursor:** starts **before** the first row. You call `next()` to advance to the first row. Rows come lazily — not all loaded at once. Column indexes are **1-based** (not 0). You can access by name or index.

```java
while (rs.next()) {
    String name = rs.getString(1);     // first column
    String email = rs.getString("email"); // by name
}
```

If you call `getInt("column_that_doesnt_exist")` → throws `SQLException`, doesn't return 0.

**Closing cascade:** closing a Statement closes its ResultSet. Closing a Connection closes everything — all its Statements and ResultSets. Use try-with-resources and they close in reverse order automatically.

**The rules that count as "never evers" in this course:**
- No hard-coded credentials in code — use a `.properties` file
- Never use the superuser (`postgres`) for the app — use a least-privilege role
- Never let `SQLException` leak out of the persistence layer — wrap it

**Quick check:**
1. The ResultSet cursor starts at the first row. True or false?
2. You call `rs.getString(0)`. What happens?
3. You close the Connection. What happens to the open Statements and ResultSets?

---

# 14. AutoCloseable & try-with-resources

**The problem:** resources like database connections, files, and streams must be closed or they leak. Forgetting to close a connection keeps it open on the database server — if you do that thousands of times, the server runs out of connections.

**The solution:** `AutoCloseable`. Any class implementing it can go in a try-with-resources block, and Java guarantees `close()` is called at the end — even if an exception is thrown. No finally block needed.

```java
try (Connection conn = DriverManager.getConnection(url);
     PreparedStatement ps = conn.prepareStatement(sql)) {
    // use them
}
// both are closed automatically here, even if an exception occurred
```

Multiple resources close in **reverse order** — the last one opened closes first. So `ps` closes before `conn` in the example above.

**Critical trap:** an object implementing `AutoCloseable` does NOT close itself automatically. It only closes automatically when used in a try-with-resources block. If you just create it normally and forget to close it, it stays open.

**What's AutoCloseable and what isn't:**
- `Connection` → AutoCloseable ✅
- `Statement` → AutoCloseable ✅
- `ResultSet` → AutoCloseable ✅
- `ResultSetMetaData` → **NOT** AutoCloseable ❌
- `DatabaseMetaData` → **NOT** AutoCloseable, you don't need to close it ❌

**Quick check:**
1. You create a `ResultSet` but don't use try-with-resources. It closes automatically anyway. True or false?
2. You have two resources in try-with-resources: A opened first, B opened second. Which closes first?
3. Is `ResultSetMetaData` AutoCloseable?

---

# 15. REST & HTTP

**REST is a style for building APIs that makes URLs meaningful and consistent.** Instead of random URLs like `/doThing` or `/getData`, REST says: resources are nouns, URLs identify them, HTTP methods say what to do with them.

`/users/42` → the user with ID 42. GET it to read, PUT it to replace, DELETE it to remove.

**The two properties every exam question about methods tests:**

**Safe** = does NOT modify server data. GET is safe — it's a read-only operation.

**Idempotent** = doing it multiple times = doing it once. The result is the same whether you do it 1 time or 100 times.

| Method | Safe | Idempotent | What it does |
|---|---|---|---|
| GET | ✅ | ✅ | Read a resource |
| POST | ❌ | ❌ | Create (repeat → multiple created) |
| PUT | ❌ | ✅ | Replace fully (repeat → same result) |
| PATCH | ❌ | ❌ | Partial update |
| DELETE | ❌ | ✅ | Delete (repeat → still gone) |

**Why is POST not idempotent?** Because each POST creates a new resource. POST `/orders` three times → three orders. Three separate things happened.

**Why is PUT idempotent?** Because PUT `/users/42` with the same data three times → the user still has the same data. You replaced the same thing with the same thing.

**Why is DELETE idempotent?** After the first DELETE, the resource is gone. The second and third DELETE find nothing to delete — but the end state is the same: it's gone. No harm done.

**HTTP is stateless.** Every request must contain all the information the server needs. The server doesn't remember you between requests. No session stored server-side by design. This is a fundamental REST constraint.

**Request line format:** `GET /images/logo.png HTTP/1.1`
**Response line format:** `HTTP/1.1 200 OK`

**Status codes — the ones to know cold:**

2xx Success: `200 OK`, `201 Created`, `204 No Content`
3xx Redirect: `301 Moved Permanently`, `302 Found (temp)`, `304 Not Modified (use cache)`
4xx Client error: `400 Bad Request`, `401 Unauthorized (not logged in)`, `403 Forbidden (logged in but no permission)`, `404 Not Found`, `405 Method Not Allowed`, `415 Unsupported Media Type`, `422 Unprocessable Entity`, `429 Too Many Requests`
5xx Server error: `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`, `504 Gateway Timeout`

**401 vs 403:** 401 = you haven't authenticated (not logged in). 403 = you're authenticated but not allowed (logged in but don't have permission).

**Status codes per method:** POST → 201, GET → 200, PUT/PATCH → 200 or 204, DELETE → 204.

**Quick check:**
1. POST is idempotent. True or false?
2. DELETE is not idempotent because the second delete fails. True or false?
3. You're logged in but not allowed to see this page. Is that 401 or 403?

---

# 16. Logging (JUL)

**Why not just use `System.out.println`?** Because you can't turn it off. In production you might want only errors. In development you want everything. `System.out.println` is either on or off — you'd have to delete them all before releasing. Logging gives you levels you can dial up or down without changing code.

Java's built-in logging is in `java.util.logging` — called **JUL**. It has 5 components:

1. **Logger** — what you call in your code (`logger.info("Server started")`). Has a name (usually the class name). Names are hierarchical — `com.fontys` is the parent of `com.fontys.prc2`. If a logger has no handlers, it passes to its parent.
2. **Handler** — where logs go. **`ConsoleHandler` goes to `System.err`** (not System.out — exam trap). `FileHandler` writes to a rotating file.
3. **Formatter** — how logs look. `SimpleFormatter` = human readable. `XMLFormatter` = XML.
4. **Level** — severity. Controls what gets through.
5. **LogManager** — global config, reads `logging.properties`.

**The level ladder from highest to lowest:**
```
OFF       ← highest (logs nothing)
SEVERE    ← default printed
WARNING   ← default printed
INFO      ← default level, default printed
CONFIG
FINE
FINER
FINEST
ALL       ← lowest (logs everything)
```

**Default level is INFO.** That means only INFO, WARNING, and SEVERE get printed by default. FINE, FINER, FINEST, CONFIG are all invisible unless you change the level.

**The rule:** setting a level prints that level AND everything above it (more severe). Set to FINE → you get FINE, CONFIG, INFO, WARNING, SEVERE. Set to ALL → you get everything.

**Double filter:** a message has to pass two checks. First the logger's level, then the handler's level. A logger can have multiple handlers, each with its own level. A message filtered by the logger never reaches any handler.

**Quick check:**
1. ConsoleHandler writes to `System.out`. True or false?
2. Logger level is INFO. Does a FINE message get logged?
3. Logger level is FINE, but the Handler level is INFO. A FINE message is sent. Does it get logged?

---

# 17. Date/Time API & i18n

**`java.time` replaced the old `Date` and `Calendar` classes** because those were terrible. The new types are immutable and each one carries a specific amount of information — pick the wrong one and you get timezone bugs.

**The four types:**

- **`LocalDateTime`** — date + time, **no timezone, no offset**. "2025-07-06T20:00" — we don't know what timezone this is.
- **`OffsetDateTime`** — date + time + offset from UTC. "2025-07-06T20:00+02:00" — we know it's 2 hours ahead of UTC.
- **`ZonedDateTime`** — date + time + offset + named region. "2025-07-06T20:00+02:00[Europe/Amsterdam]" — we know the timezone rule set too (handles daylight saving automatically).
- **`Instant`** — a single point on the timeline, **always UTC**. No timezone, no offset. `Instant.now()` tells you when right now is as a UTC timestamp.

**Offset explained:** `2025-07-06T20:00+02:00` means local time is 20:00 and it's 2 hours ahead of UTC, so UTC is 18:00.

`Instant` cannot represent a "local" time — it's purely UTC. But you CAN convert it: `instant.atZone(ZoneId.of("Europe/Amsterdam"))` gives you a `ZonedDateTime`.

**i18n (internationalisation):** instead of hardcoding text like "Welcome" in your code, you put it in a `.properties` file. One file per language:

- `messages.properties` — base/English
- `messages_nl_NL.properties` — Dutch (Netherlands)

Java looks for the most specific file first, then falls back. Can't find Dutch Netherlands? Try Dutch. Can't find Dutch? Use base. This is the **fallback chain**.

`MessageFormat.format("Hello {0}!", name)` fills in the `{0}` placeholder.

**Quick check:**
1. `LocalDateTime` — does it store timezone info?
2. `Instant` can represent time in any local timezone. True or false?
3. `2025-07-06T20:00+02:00` — what is the UTC time?

---

# 18. State Machines

**A state machine models something that behaves differently depending on its history.** A traffic light is always in one state — Red, Yellow, or Green — and transitions between them based on events (timer tick). The same event (timer tick) does different things depending on the current state.

**The State pattern (GoF)** is how you implement this in code:
- A **Context** holds a reference to the current state object
- Each **concrete state** implements the same interface
- When an event happens, the context calls a method on the current state, which may swap the context's state to a new one

```java
// Instead of a giant switch statement:
context.setState(new GreenState());
context.handleTick();   // Green decides what to do, may change to Yellow
```

**Enum-based states** (what this course uses): states are enum constants. Benefits: the set of states is fixed, they're immutable, shareable across contexts, easy to mock in tests. Downside: they can't hold mutable data — store counters in the context instead.

**Constructor pitfall:** don't call `state.enter(this)` inside the constructor. The object isn't fully built yet, so passing `this` can lead to bugs. Use an `init()` method that you call after the object is created.

**State vs Strategy pattern:** same structure (context + interchangeable objects), different intent. State changes behaviour because the object's internal state changed. Strategy swaps an algorithm that was chosen externally by the caller.

**Quick check:**
1. In the State pattern, who holds the reference to the current state?
2. Why is calling `state.enter(this)` in the constructor a problem?
3. What's the difference between State and Strategy pattern?

---

# 19. Regular Expressions

**A regex is a pattern that matches text.** Think of it as a very precise search.

**The symbols you need:**

| Symbol | Means |
|---|---|
| `^` | Start of string |
| `$` | End of string |
| `.` | Any single character |
| `\.` | A literal dot (escaped) |
| `[a-z]` | One lowercase letter |
| `[A-Z]` | One uppercase letter |
| `\d` | One digit (0–9) |
| `\w` | One word character (letter, digit, underscore) |
| `\s` | One whitespace character |
| `?` | 0 or 1 times |
| `+` | 1 or more times |
| `*` | 0 or more times |
| `{n}` | Exactly n times |
| `{n,m}` | Between n and m times |

**Anchoring matters:** `\d{4}` matches 4 digits anywhere in the string. `^\d{4}$` matches a string that is ONLY 4 digits.

**Pattern and Matcher in Java:**
```java
Pattern p = Pattern.compile("\\d{4}");  // double backslash in Java strings
Matcher m = p.matcher("hello 1234 world");
m.find();    // finds the pattern anywhere in the string → true
m.matches(); // checks if the WHOLE string matches the pattern → false
```

`find()` vs `matches()` vs `lookingAt()`:
- `find()` — searches anywhere in the string
- `matches()` — the whole string must match the pattern
- `lookingAt()` — the beginning of the string must match

Groups: `(pattern)` captures a group. Group 0 = entire match. Group 1 = first set of parentheses. Named groups: `(?<name>pattern)` then `m.group("name")`.

**Quick check:**
1. `\d{8}` — what does this match?
2. `find()` vs `matches()` — what's the difference?
3. In a Java string, how do you write a regex for a literal dot?

---

# 20. Security

**Security is about stopping people from doing things they shouldn't.** This week connects several earlier topics through a security lens.

**Class loaders** load `.class` files into the JVM. There are three, and they work by delegation — ask the parent first, only load it yourself if the parent can't:
- **Bootstrap** — loads core Java (like `java.lang`)
- **Platform** — loads the rest of the platform
- **System/Application** — loads your code from the classpath

If a class can't be found anywhere in the chain → `ClassNotFoundException`.

**Encryption basics:**
- **Symmetric** (AES) — one key, used for both encrypting and decrypting. Fast. Good for bulk data.
- **Asymmetric** (RSA) — two keys: public key encrypts, private key decrypts (or vice versa). Slow. Used to exchange a symmetric key securely.
- **Hashing** — one-way, NOT encryption. You can't reverse a hash. Used for passwords.

Why is hashing not enough for passwords? Rainbow tables — precomputed hashes for common passwords. Fix: add a **salt** (random value mixed in before hashing, unique per password). Even then, fast hashes (MD5, SHA-256) can be brute-forced. Use **slow hashes**: bcrypt, argon2, scrypt, PBKDF2.

**SQL injection:** user types `' OR '1'='1` into a login form. Concatenated into SQL, this bypasses authentication. Fix: use PreparedStatement (parameterized queries) so user input is always treated as data, never as SQL. Also use a least-privilege database user — don't run your app as the database superuser.

**HTTPS** = HTTP + encryption. Adds a certificate (proves the server is who it says it is) + an encrypted connection. Without it, anyone on the network can read your traffic.

**Error messages:** "username not found" vs "wrong password" tells an attacker which usernames are valid. Always return a generic message: "username or password incorrect."

**Quick check:**
1. Symmetric vs asymmetric — which uses one key and which uses a key pair?
2. Hashing is reversible. True or false?
3. Why is a salt added to a password before hashing?

---

# 21. Number Systems & Storage

**The same bits mean different things depending on how you interpret them.** 32 bits could be an integer, a float, or four ASCII characters. The interpretation is what gives them meaning.

**Converting decimal to another base:** divide by the base repeatedly and read the remainders from bottom to top.

```
45 ÷ 8 = 5, remainder 5  ← read this second
 5 ÷ 8 = 0, remainder 5  ← read this first
→ 45 in octal = 55₈

77 ÷ 8 = 9, remainder 5  ← read third
 9 ÷ 8 = 1, remainder 1  ← read second
 1 ÷ 8 = 0, remainder 1  ← read first
→ 77 in octal = 115₈
```

Binary place values for one byte: 128 · 64 · 32 · 16 · 8 · 4 · 2 · 1

19 in binary: 16+2+1 = `00010011`

**Two's complement — how Java stores negative integers:**

Why not just use a sign bit? Because +0 and -0 would be different bit patterns and addition gets complicated. Two's complement solves this.

To get -6 in 8-bit:
```
Step 1: Write 6:          0000 0110
Step 2: Flip all bits:    1111 1001
Step 3: Add 1:            1111 1010  ← this is -6
```

Range for n-bit two's complement: −2^(n-1) to 2^(n-1)−1. For a byte (8-bit): −128 to 127.

**IEEE-754 — how Java stores floats and doubles:**

Floats use binary scientific notation. A 32-bit float has:
- 1 bit: sign (0=positive, 1=negative)
- 8 bits: exponent (stored with bias 127 — add 127 to the real exponent)
- 23 bits: mantissa (the fraction part — the leading 1 is implicit and not stored)

Special values (when exponent bits are all 1):
- All 1s exponent + mantissa = 0 → **Infinity**
- All 1s exponent + mantissa ≠ 0 → **NaN**

Why can't you represent 0.1 exactly? Because in binary, 0.1 repeats forever (like 1/3 in decimal repeats as 0.333…). It gets rounded, which is why `0.1 + 0.2 != 0.3` in floating point. Use `BigDecimal("0.1")` for exactness.

Double is the same idea but 64 bits: 1 sign + 11 exponent + 52 mantissa, bias 1023.

**Quick check:**
1. Convert 19 to binary.
2. To get -6 in two's complement: flip bits and then \_\_\_\_.
3. Why is `0.1 + 0.2 != 0.3` in Java?

---

## How to use this book

**Tonight:** read topics 1–16 all the way through. Don't quiz yourself yet — just get the explanations into your head. Answer the quick-check questions mentally; if you blank on one, re-read that section.

**Saturday:** go through the notebook section (Part 2 below) with the answers covered. Treat it as a mock exam. Every statement — say your answer before you look. Mark every miss.

**Sunday:** redo only your misses. One final pass. Stop by the afternoon.

---
# 📒 NOTEBOOK — Exam Questions Reconstructed

> These are your friend's notes written **after** sitting the exam — the questions reproduced from memory in roughly the order they appeared on the paper, with the correct answer and justification next to each one. They jump between topics because that's how the 30-question MC paper is built.
> Every statement below is a direct transcription from the notebook pages (7588 → 7616 in camera order). Use this section on **Saturday** with the answers covered — read each statement, decide TRUE/FALSE/NOT TRUE, then uncover.

---

## 📄 Page 1 (IMG 7588) — JPMS / module-info.java

**Topic: Java Platform Module System**

A module must have a `module-info.java`.
→ **TRUE.**

`requires` means the module **depends on** another module.
→ **TRUE.**

`requires transitive` also exposes that dependency to modules depending on this module.
→ **TRUE.** But it only exposes the **public API**, not private members.

`exports` allows (other) modules to access the package.
→ **TRUE.**

`opens` allows **runtime reflection** access.
→ **TRUE.** (Different from `exports` — `opens` is for reflective access, not normal compile-time access.)

`cannot import` in module-info.
→ **TRUE.** `import` is not a valid keyword inside `module-info.java`.

**Edge cases your friend flagged:**

`requires transitive` does NOT mean you have access to private members — only the **public API** of the transitive dependency.
→ **TRUE.**

`opens` does **not** automatically `exports`.
→ **TRUE.** They are independent.

You cannot extend multiple modules — modules are not classes or interfaces.
→ **TRUE.**

Module names are usually **reverse domain names** (e.g. `nl.fontys.prc2`).
→ **TRUE.**

---

## 📄 Page 2 (IMG 7589) — Incorrect module-info syntax + Functional Interfaces + HTTP GET/POST

**Topic: JPMS — syntax traps**

Invalid keywords in `module-info.java`: `import`, `extends`, `implements`, `package`.
→ **TRUE** — none of these belong in module-info.

Check syntax errors:
- `requires` must name a **module**, not a package.
  - ✅ CORRECT: `requires java.sql`
  - ❌ WRONG: `requires com.myapp.api` (that's a package)

- `exports` must name a **package in this module**.
  - ✅ CORRECT: `exports com.myapp.api`
  - ❌ WRONG: `exports java.sql` (that's another module's package)

- `opens` must name a **package in this module**.

---

**Topic: Functional Interfaces**

A functional interface is an interface with **exactly one** abstract method.
→ **TRUE.**

It can have any number of `default` or `static` methods.
→ **TRUE.**

The abstract method can return **any type** (void, int, String…).
→ **TRUE.**

The abstract method doesn't need the `abstract` keyword.
→ **TRUE.**

`@FunctionalInterface` is optional — it just tells the compiler to **check**.
→ **TRUE.**

**Not functional:** if there is **more than one abstract method**.
→ **TRUE.**

---

**Topic: HTTP — GET and POST**

**Common HTTP request methods:**

1. **GET**
   - Retrieves data from the server.
   - No body in request.
   - Should not change server state.

2. **POST**
   - Sends data to the server (e.g. create resource, submit form).
   - Request body often contains data.

---

## 📄 Page 3 (IMG 7590) — HTTP PUT/DELETE/PATCH/HEAD/OPTIONS/TRACE + AutoCloseable

**Topic: REST HTTP methods (continued)**

3. **PUT**
   - Update (or create) a resource at a specific URL.
   - Idempotent (multiple calls = same effect).

4. **DELETE**
   - Remove a resource.
   - Idempotent.

5. **PATCH**
   - Partially update a resource.
   - (NOT idempotent — see trap page.)

6. **HEAD**
   - Like GET — no body in the (response), just headers.

7. **OPTIONS**
   - Ask the server what HTTP methods are supported.

8. **TRACE**
   - Echo the received request (rare, debugging tool).

---

**Topic: AutoCloseable**

`AutoCloseable` is an interface in `java.lang`.
→ **TRUE.**

Any class that implements `AutoCloseable` can be used in a **try-with-resource** block.
→ **TRUE.**

It will automatically call `close()` at the end of the block.
→ **TRUE.**

**Why it matters:**
- Prevents resource leaks — no forgetting to call `close()` manually.
- `try-with-resources` guarantees `close()` is called even if an **exception** occurs.

**Common Traps (your friend explicitly flagged these):**

`ResultSetMetaData` is AutoCloseable → **WRONG (it isn't).**

You must close `DatabaseMetaData` → **WRONG (you don't).**

Closing a `Connection` automatically closes its Statements and ResultSets → **NO!!!**
*(Note: this was flagged as FALSE here. Page 7612 and 7614 later flag it TRUE with ✓. The settled answer is **TRUE** — closing Connection does close everything. This was a note slip on this early page.)*

You must close these separately (or use try-with-resource).
*(This is best practice advice, not a technical requirement.)*

---

## 📄 Page 4 (IMG 7591) — ResultSet + MetaData

**Topic: ResultSet**

`ResultSet` is an interface in `java.sql`.
→ **TRUE.**

It represents the result of executing a SQL query (usually a SELECT statement).
→ **TRUE.**

**Key facts:**

It starts **before** the first row — you must call `next()` to move to the first row.
→ **TRUE.**

It is `AutoCloseable` — you can (and should) use it in try-with-resource.
→ **TRUE.**

`ResultSet` can be **forward only** (default) — you can only move forward through rows.
→ **TRUE.**

**Scrollable**: if configured, you can go backward or jump to a row.
→ **TRUE.**

**Traps:**

`ResultSet` is automatically closed when you close the `Connection` → **FALSE (you should close it yourself).**
*(Note: this was the wording in the notes here. Later pages confirm TRUE. See contradiction note at top of document.)*

`ResultSet` columns are 0-based → **FALSE (1-based).**

`ResultSetMetaData` is AutoCloseable → **FALSE (it isn't).**

`ResultSet` implements `AutoCloseable` → **TRUE.**

`ResultSet` can be used in a try-with-resources → **TRUE.**

---

**Topic: MetaData**

Metadata = **data about data**.

It describes the **structure** of your data, not the actual data itself.

It's like a blueprint or schema of your database.

**What is `information_schema`?**
- A special system database/schema found in many SQL-compliant databases.
- It stores metadata about: **tables, columns, views, indexes, constraints, users/permissions**.

**AutoCloseable (MetaData side):**

`AutoCloseable` itself does not automatically close anything.

You must use try-with-resources (or manually call `close()`).

The try-with-resource triggers `close()` **behind the scenes**.

---

## 📄 Page 5 (IMG 7592) — Enums + Parameterized Tests

**Topic: Enums**

Is an enum always final?
→ **Yes, they are. You cannot extend an enum.**

Can an enum have instances?
→ **Yes — each enum constant is an instance of the enum type.**

Can enums declare static methods?
→ **Yes.**

Can enums declare constructors?
→ **Yes, but they are always private.**

Can enums implement interfaces?
→ **Yes.**

Can enums have abstract methods?
→ **Yes — but then each enum constant must implement that method.**

Enums can extend other classes → **FALSE.**

---

**Topic: Parameterized Tests**

Parameterized tests:
- Run the same test logic **multiple times**.
- With **different sets of input data**.
- Check that the result was as expected for each set.

Can parameterized tests be `private`?
→ **No — like normal test methods, they must be accessible.**

Can parameterized tests work without supplying any source of data?
→ **No — they need at least one data source annotation (e.g. `@ValueSource`).**

Parameterized test methods must take arguments → **True.**

Parameterized tests can take data from a static method (`@MethodSource`) → **True.**

Using `@Test` and `@ParameterizedTest` together on the same method → **False.**

---

## 📄 Page 6 (IMG 7593) — PIT Mutation Testing + Generics

**Topic: PIT — Mutation Testing**

PIT is a technique that:
- **Modifies (mutates)** your code in small ways.
- **Runs your tests** against the mutated code.
- **Checks if the tests failed** as they should.

What is the purpose of mutation testing?
→ **To assess the quality of your tests.**
→ If your tests don't catch a mutant, they're probably not good enough.

What happens if a mutant **survives**?
→ Your tests did not detect the change.
→ The mutant represents a **potential weakness** in your tests.

What happens if a mutant is **killed**?
→ Your tests failed (as they should) when the code was mutated.
→ The tests **successfully detected the bug**.

---

**Topic: Generics**

Can a generic class have type parameters?
→ **Yes.** `class Box<T> { T content; }` — `T` is a type placeholder.

Can generic methods have their own type parameters?
→ **Yes** — generic methods declare type params **before the return type**.

Can you create an instance of a type parameter?
→ **No.** `T obj = new T(); // illegal`
→ **Type erasure** means the actual type isn't available at runtime.

Can you create arrays of generic type parameters?
→ **No, it is illegal.** `T[] array = new T[10]; // illegal`

---

## 📄 Page 7 (IMG 7594) — Generics continued + Stream Operations

**Topic: Generics (continued)**

Can you use primitive types as type parameters?
→ **No, this is illegal!!!**
→ `Box<int>` — ILLEGAL.
→ Instead use: `Box<Integer>` — correct (wrapper type).

Can you create generic interfaces?
→ **Yes!!!**
→ `interface Processor<T> { T process(T input); }`

Can you have static members that use type parameters of the class?
→ **No** — static members **cannot** use the class's type parameters.
→ Because the static context doesn't know the type.
→ `class Box<T> { static T value; // ILLEGAL }`

---

**Topic: Two types of Stream Operations**

**1. Intermediate Operations (Lazy):**
- They set up the stream pipeline but **don't start processing yet**.
- Don't actually filter/map/process until a terminal operation runs.
- Examples: `filter, map, sorted, distinct, limit, peek`

**2. Terminal Operations (Eager):**
- They **actually process** the stream, pulling data through the pipeline.
- They **trigger execution** of all prior intermediate steps.
- Examples: `collect, forEach, sum, reduce, count`

---

## 📄 Page 8 (IMG 7595) — Stream rules + Mockito + Number conversions

**Topic: Streams (continued)**

Are terminal operations usually at the end of the pipeline?
→ **Yes — terminal operations are ALWAYS at the end.**

Can there be two terminal operations in one pipeline?
→ **No — a stream pipeline can only have ONE terminal operation.**

---

**Topic: Mockito**

Mockito is a framework used for **creating mock objects** in unit tests.

A mock is a fake object that lets you:
- **Simulate dependencies.**
- **Verify interactions.**
- **Control return values.**

You can train the mock by defining its behaviour — telling the mock what to do when a certain method is called (e.g. `when(...)`).

**Trap:**
You can mock the SUT itself and still meaningfully test its logic → **FALSE!!!**
→ If you mock the SUT, you are **not testing its real logic**.

---

**Topic: Number Systems — worked examples**

**Binary conversion (decimal → binary):**
- 19 in binary → place values 128 64 32 16 8 4 2 1 → `00010011`

**Decimal → Octal (divide by 8, read remainders bottom-to-top):**
```
45 / 8 = 5  remainder 5  ↑
 5 / 8 = 0  remainder 5  ↑  → read up: 55 in octal
```
```
77 / 8 = 9  remainder 5  ↑
 9 / 8 = 1  remainder 1  ↑
 1 / 8 = 0  remainder 1  ↑  → read up: 115 in octal
```

**Two's complement — negative number (-6 in 8-bit):**
```
Step 1: Write 6 in binary:   0000 0110
Step 2: Invert all bits:     1111 1001
Step 3: Add 1:               1111 1010  ← this is -6
```

---

## 📄 Page 9 (IMG 7596) — Java Exception Basics

**Topic: Exceptions**

An exception is an event that **disrupts the normal flow** of the program.

**Checked Exceptions** (compile-time):
- `IOException`, `SQLException`, `ClassNotFoundException`
- You **must** handle them (try-catch or declare `throws`).

**Unchecked Exceptions** (Runtime Exceptions):
- `NullPointerException`, `IndexOutOfBoundsException`, `IllegalArgumentException`, `ArithmeticException`
- Compiler doesn't check — your choice whether to handle.

Checked = **compile-time.**
Unchecked = **runtime.**

**`throw` vs `throws`:**
- `throw` — actually throws an exception object.
- `throws` — declares that a method might throw an exception.

---

**True/False bank from the notebook:**

All exceptions must be handled in Java → **FALSE.**
→ Only **checked** exceptions must be handled (or declared with `throws`).
→ Unchecked exceptions don't have to be handled.

A method can declare `throws` for an unchecked exception → **TRUE.**
→ `public void doSomething() throws NullPointerException { }`
→ No compiler requirement, but you **can** declare it.

`finally` always runs → **ALMOST ALWAYS.**
→ Except: if `System.exit(0)` is called before the finally, or if the **JVM crashes**.

A try block must have at least one catch block → **FALSE.**
→ `try { } finally { /* cleanup */ }` — no catch is required if finally is present.

---

## 📄 Page 10 (IMG 7597) — Exceptions continued

**Topic: Exceptions (continued)**

You can catch `Throwable` → **TRUE.**
→ **Bad practice** — you will catch `Error`s too (e.g. `OutOfMemoryError`).

You cannot have multiple catch blocks for the same exception type → **TRUE** (it's a compile error).
```java
catch (IOException e) {}
catch (IOException e2) {} // compile error
```

Multi-catch can't have exception types that have a subclass-superclass relationship → **TRUE** (compile error).
```java
catch (IOException | Exception e) {} // compile error — Exception already covers IOException
```

`throw` and `throws` can appear together in a method → **TRUE.**
```java
public void foo() throws IOException { throw new IOException("fail"); }
```

You can throw any object → **FALSE.**
→ Only objects that are `Throwable` or subclasses.

---

**Topic: BigInteger and BigDecimal**

**BigInteger:**
- Used for very large whole numbers (integers).
- No decimal point.
- Arbitrary-precision integer.
- **Immutable.**

**BigDecimal:**
- Used for very precise decimal numbers.
- Great for **currency, science**, or any case where `float`/`double` rounding errors are unacceptable.
- Arbitrary-precision decimal.
- **Immutable.**

---

## 📄 Page 11 (IMG 7598) — Immutability + Reflection

**Topic: Immutability**

What does immutable mean?
→ An immutable object is an object whose **state (data/fields) cannot change** once it's created.
→ When you "modify" it, you actually **create a new object**.
→ The original object stays unchanged.

**Example — String is immutable:**
```java
String s = "hello";
s.concat("world");         // doesn't change s!
System.out.println(s);     // prints: hello

s = s.concat("world");     // NOW s points to new String
System.out.println(s);     // prints: hello world
```

---

**Topic: Reflection**

Reflection is a powerful API in Java that lets your code **examine and manipulate itself at runtime**.

It allows you to:
- Inspect classes, interfaces, fields, methods, constructors.
- **Access private** fields/methods.
- Dynamically **create objects**.
- Dynamically **invoke methods**.
- **Bypass compile-time type checking.**

Example Use Cases: Unit testing, frameworks, etc.

**Limitations/risks:**
- Can break **encapsulation** (access private stuff).
- **Slower performance** (runtime overhead).
- **Security risks.**

---

## 📄 Page 12 (IMG 7599) — java.time types + Unit Testing definition

**Topic: java.time**

`LocalDateTime` only stores date + time — no offset, no zone → **(see contradiction note at top — TRUE in reality; LocalDateTime has NO offset/zone.)**

`ZonedDateTime` contains **full** time zone info → **TRUE** (includes date, time, offset and region).

`OffsetDateTime` contains offset info but **no region** → **TRUE** (no zone region like Europe/Amsterdam).

`Instant` can represent a date and time in **any local time zone** → **FALSE.**
→ Instant is always **UTC** (a point on the timeline, no local zone).

`Instant` can be converted to `ZonedDateTime` with a zone → **TRUE.**
```java
Instant now = Instant.now();
ZonedDateTime zdt = now.atZone(ZoneId.of("Europe/Amsterdam"));
```

---

**Offset explained:**
Offset = the difference (in hours and minutes) between a given time and UTC.

Example: `2025-07-06T20:00+02:00`
- Date = 2025-07-06
- Time = 20:00
- Offset = +02:00 → this means 2 hours ahead of UTC → **UTC is 18:00**

---

**Topic: Unit Testing (definition)**

Unit Testing:
- Tests a **small, isolated** piece of code (usually a method or class).
- **No dependency** on external systems (DB, network).

---

## 📄 Page 13 (IMG 7600) — Testability + Mock/Stub/Spy + REST HTTP types

**Topic: Testability**

Testability → **Non-functional requirement.**

1. **Controllability** — How easy is it to bring the system into a state where you can test it?
2. **Observability** — How easy is it to see what the system is doing?
3. **Smallness** — How small is the system you're testing?

---

**Topic: Test doubles**

**Mock** — (defined on this page without full detail, covered more elsewhere)

**Stub:**
- Provides **predefined responses** to method calls.
- Just wants to return something when called.

**Spy:**
- Wraps **real objects** but lets you track calls.
- Can override **some** behaviours while keeping the rest real.

---

**Topic: REST API HTTP Types**

**Idempotent** = repeated identical requests → same (1) effect as 1 request.

1. **GET** — Is safe, and is idempotent. Retrieve resource (no modification).
2. **POST** — Is NOT safe, and is NOT idempotent. Creates new resources (multiple calls → multiple creations).
3. **PUT** — Is NOT safe, but IS idempotent. Replace resource fully.

---

## 📄 Page 14 (IMG 7601) — HTTP PATCH/DELETE + Status codes

**Topic: REST HTTP methods (continued)**

4. **PATCH** — Is NOT safe, and is NOT idempotent. Partially updates resource.
5. **DELETE** — Is NOT safe, but IS idempotent. Deletes resource.

---

**Topic: HTTP Status Codes**

Categories:
1. Informational (100–199)
2. Successful (200–299)
3. Redirection (300–399)
4. Client error (400–499)
5. Server error (500–599)

**Most Common Per Category:**

**2xx — Success:**
- **200 OK** — The request was successful.
- **201 Created** — The request resulted in a new resource being created.
- **204 No Content** — The request was successful, but there is no content to return.

**3xx — Redirection:**
- **301 Moved Permanently** — The resource has been permanently moved to a new URL.
- **302 Found (Temporary Redirect)** — The resource is temporarily at a different URL.
- **304 Not Modified** — The resource hasn't changed; use the cached version.

**4xx — Client Errors:**
- **400 Bad Request** — Server cannot process the request due to client error.
- **401 Unauthorized** — Authentication is required but has not been provided or failed.

---

## 📄 Page 15 (IMG 7602) — More status codes + HTTP request structure

**Topic: Status Codes (continued)**

- **403 Forbidden** — The client is not allowed to access the requested resource.
- **404 Not Found** — The requested resource could not be found.
- **405 Method Not Allowed** — The request method (GET, POST, etc.) is not supported for the resource.
- **415 Unsupported Media Type** — The server does not support the media format of the request.
- **422 Unprocessable Entity** — Syntactically correct but cannot be processed due to semantic errors (e.g. validation failure in API requests).
- **429 Too Many Requests** — The user has sent too many requests in a given amount of time.

**5xx — Server Errors:**
- **500 Internal Server Error** — Something unexpected happened on the server.
- **502 Bad Gateway** — The server received an invalid response from an upstream server.
- **503 Service Unavailable** — Temporarily unable to handle requests (overloaded or under maintenance).
- **504 Gateway Timeout** — The server did not receive a timely response from an upstream server.

---

**Topic: HTTP Request structure**

The basic form of an HTTP request:
```
<request method> <URI> <HTTP version>
```
1. **Request method**: GET, POST, PUT, PATCH, DELETE
2. **URI**: the resource you are requesting
3. **HTTP version**: HTTP/1.1, HTTP/2.0, HTTP/3.0

---

## 📄 Page 16 (IMG 7603) — HTTP examples + HTTP Response + PUT/GET traps

**Topic: HTTP examples**

```
GET /images/logo.png HTTP/1.1
POST /event/create.php HTTP/2.0
```

**HTTP Basics:**
- Hyper Text Transfer Protocol (HTTP).
- A protocol for transferring data between a **client** (browser, mobile app, etc.) and a **server** (Apache, node, etc.).
- The **client always initiates** the connection.
- The protocol is **stateless** — every request should contain all the content for the server to know what to do.

**HTTP Response:**
```
<HTTP version> <status code> <status message>
```
1. HTTP version — same as the request.
2. Status code — indicates what happened.
3. Status message — a short message explaining the status code.

---

**Traps — NOT TRUE statements:**

PUT is not idempotent because repeated PUTs will modify the resource multiple times → **NOT TRUE.**
→ PUT IS idempotent — sending the same PUT multiple times = same result.
→ The resource is replaced each time with the same content.

GET request can modify data if needed → **NOT TRUE.**
→ GET is safe — it **must not** modify data — only retrieve.

---

## 📄 Page 17 (IMG 7604) — REST traps continued + Logging intro

**Topic: REST traps (continued)**

POST is idempotent because it always creates the same resource when repeated → **NOT TRUE.**
→ POST is NOT idempotent.
→ Repeating POST usually creates **multiple new resources** (e.g. multiple users, multiple orders).

DELETE is not idempotent because repeated DELETE will break the system → **NOT TRUE.**
→ DELETE IS idempotent — deleting the same resource repeatedly = resource is gone; no change after it's deleted.

PATCH is always idempotent like PUT → **NOT TRUE.**

HTTP is stateful — the server keeps client session info across requests → **NOT TRUE.**
→ HTTP is **stateless** — each request has all info needed; no server-side session by design.

GET, POST, PUT, PATCH, DELETE all use the same HTTP status code → **NOT TRUE.**
→ Different methods use specific codes:
- POST → 201 Created
- GET → 200 OK
- PUT/PATCH → 200 OK or 204 No Content
- DELETE → 204 No Content

---

**Topic: Logging (intro)**

Logging is the process of **recording information** about a program's execution.

It is primarily used to:
- **Diagnose issues** (e.g. why a crash happened).
- **Audit system behavior** (security, business logic).
- **Debug during development.**
- **Track system health in production** (without a debugger).

---

## 📄 Page 18 (IMG 7605) — Logging components + levels

**Topic: Logging (components)**

Logging is **configurable, filterable, and hierarchical.**

Java has a built-in logging API in `java.util.logging` — often referred to as **JUL** (Java Util Logging).

**5 components of Java logging:**

1. **Logger** — the object your code logs through (`logger.info(...)`, `logger.warning(...)` etc.).
2. **Handler** — determines **where** logs go (console, file, etc.).
3. **Formatter** — defines **how** logs are formatted.
4. **Level** — defines severity levels (INFO, WARNING, etc.).
5. **Log Manager** — global config manager.

**Logger by default:**
- Comes with a `ConsoleHandler`.
- Uses `Level.INFO`.
- The logger's level is **INFO by default**.
- So only **INFO, WARNING, SEVERE** are printed.

**All levels (non-default ones noted):**
- **ALL** — log everything. Not default.
- **FINEST** — extreme detail (variable-by-variable tracing). Not default.
- **FINER** — detailed flow between methods. Not default.
- **FINE** — general debug information. Not default.
- **CONFIG** — configuration info during app setup. Not default.

---

## 📄 Page 19 (IMG 7606) — Logging level priority ranking

**Topic: Logging levels (priority)**

- **INFO** (default) — normal operations (e.g. "Server started"). **Default level.**
- **WARNING** (default) — unexpected situation, but app can still continue. **Default level.**
- **SEVERE** (default) — serious problem (e.g. database unreachable). **Default level.**
- **OFF** (highest) — disables logging entirely. Not default.

**Priority rank (highest → lowest):**
```
OFF      ← highest priority (logs nothing)
SEVERE
WARNING
INFO     ← default level in Java
CONFIG
FINE
FINER
FINEST
ALL      ← lowest priority (logs everything)
```

**The rule — always the selected level AND ABOVE:**
- If INFO → prints: INFO, WARNING, SEVERE
- If FINE → prints: FINE, CONFIG, INFO, WARNING, SEVERE
- Logger level = ALL → logs **absolutely everything.**
- Logger level = OFF → logs **absolutely nothing.**

---

## 📄 Page 20 (IMG 7607) — Logger components in depth

**Topic: Logging (deep dive)**

**1. Logger:**
- Main entry point: `logger.info(...)`, `logger.warning(...)` etc.
- **Decides first** whether a message should be logged based on its level.
- Logger names are **hierarchical** (e.g. `com.fontys` is parent of `com.fontys.prc2`).
- If no handler attached, logger uses **parent handlers** (often the root logger).

**2. Handler:**
- Responsible for **where** the log is sent: console, file, socket etc.
- Java has built-in handlers:
  - `ConsoleHandler` → outputs to **`System.err`** by default.
  - `FileHandler` → writes logs to a rotating file.
  - `SocketHandler`, `StreamHandler`, etc.
- A logger may have **multiple handlers**, each with its own level and formatter.

**Double filter system:**
1. Message must pass the **logger's level filter.**
2. Then it must pass the **handler's level filter.**

**3. Formatter:**
- Defines the **format** of each log message.
- Java provides:
  - `SimpleFormatter` (default for ConsoleHandler) → **human-readable.**
  - `XMLFormatter` → logs in XML format.

**4. Log Manager:**
- Manages configuration of **all loggers** in the JVM.
- Reads from `logging.properties` by default.
- Handles: default levels, default handlers, formatter configurations.

---

## 📄 Page 21 (IMG 7608) — Object class + Reflection basics + Raw types

**Topic: `Object` class**

Every class in Java **implicitly extends `Object`.**
→ **TRUE.**

`Object` is the **top of the Java class hierarchy.**

Every class — even your custom ones like `public class User` — implicitly extends `java.lang.Object`.

That means you always get:
- `toString()`
- `equals(Object obj)`
- `hashCode()`
- `getClass()`

---

**`getClass()` — runtime class information:**
```java
Object obj = new String("hello");
System.out.println(obj.getClass()); // prints: class java.lang.String
```
- `getClass()` is a method **inherited from `Object`.**
- It returns an instance of `Class<?>` representing the **runtime type.**
- Useful for reflection or checking type at runtime.

---

**`Class.class` — compile-time Class object:**
```java
Class<String> cls = String.class;
```

---

**Correct ways to reference the Object class:**
- ✅ `Object.class`
- ✅ `Class.forName("java.lang.Object")`
- ✅ `new Object().getClass()`

**Wrong:**
- ❌ `Object.getClass()` — `getClass()` is an **instance method**, not static.

---

**Topic: Raw types**

```java
ArrayList al = new ArrayList();
```
- This is a **raw type** usage of a generic class.
- It **does compile**, but it produces an **unchecked warning**.

---

## 📄 Page 22 (IMG 7609) — Exceptions in testing

**Topic: Testing exceptions**

**Method under test:**
```java
public int divide(int a, int b) { return a/b; }
```

**1. Good Practice — use `assertThrows()`:**
```java
@Test
void testDivideByZero() {
    assertThrows(ArithmeticException.class, () -> {
        divide(10, 0);
    });
}
```
- Clearly shows you **expect an exception.**
- Test **fails automatically** if no exception is thrown.
- **No unnecessary try-catch.**

**2. Good Practice — normal test (exception NOT expected):**
```java
@Test
void testDivideNormally() {
    assertEquals(5, divide(10, 2));
}
```
- If `divide()` throws anything, the test will **fail automatically.**
- This is a good practice — the test is clean.

---

## 📄 Page 23 (IMG 7610) — try-catch with fail() + Golden Rule

**Topic: Exception testing (try-catch form)**

**3. Acceptable — try-catch ONLY with `fail()` inside:**
```java
@Test
void testDivideByZeroManually() {
    try {
        divide(10, 0);
        fail("Expected ArithmeticException"); // only reached if NO exception thrown
    } catch (ArithmeticException e) {
        assertEquals("/ by zero", e.getMessage());
    }
}
```
- `fail()` ensures the test **doesn't pass silently** if the exception is not thrown.

**Why do we use `fail()` in a try-catch block?**
→ Because if `divide(10, 0)` somehow doesn't throw, execution reaches `fail(...)` which **immediately fails the test.**
→ Without `fail()`, if no exception is thrown, the catch block is simply skipped and the test **passes silently — BAD.**

---

**Bad practices:**
- NOT using `fail()` in try-catch.
- Trying to catch the **wrong exception type.**

**Golden Rule:**
→ **Never write a bare try-catch in a test** — always use `assertThrows`, or include `fail()`.

---

## 📄 Page 24 (IMG 7611) — JDBC: The Big Picture

**Topic: JDBC architecture**

```
My Code
   ↓
JDBC API (java.sql package) ← vendor-neutral
   ↓
JDBC Driver (vendor-specific) e.g. MySQL, PostgreSQL
   ↓
Database
```

**JDBC Interfaces (the 5 main ones):**
1. **Connection** — represents a connection/session with a DB.
2. **Statement / PreparedStatement / CallableStatement** — used to send SQL commands.
3. **ResultSet** — holds data returned from a query.
4. **ResultSetMetaData** — info about columns in a ResultSet.
5. **DriverManager** — finds/loads drivers and opens connections.

---

**ResultSet key facts:**
- Cursor starts **before the first row.**
- `rs.next()` moves forward.
- Rows are retrieved **lazily** (not all loaded at once).
- Access by column name or **index (1-based!).**

**Closing cascade:**
- If you close the **Statement**, the ResultSet is also closed.
- If you close the **Connection**, **everything is closed.**

---

**What is the JDBC "Vendor"?**
→ The company or group that makes the database software you're connecting to.
→ Like: Oracle, MySQL, PostgreSQL, etc.

---

## 📄 Page 25 (IMG 7612) — JDBC Tricky Questions / Edge Cases

**Topic: JDBC traps (your friend's explicit TRUE/FALSE bank)**

**1. Connection & DriverManager:**

`DriverManager.getConnection(...)` uses the JDBC URL to find the right driver → **TRUE ✓**

JDBC URL `jdbc:mysql://...` can be used for PostgreSQL → **FALSE — it must match the vendor ✗**

You always need to call `Class.forName(driver)` → **FALSE — modern JDBC uses auto-loading ✗**

You can open multiple connections from the same app → **TRUE — each is its own DB session ✓**

Closing a **Connection** also closes all related Statements and ResultSets → **TRUE ✓**

---

**2. Statements vs PreparedStatements:**

You can pass dynamic SQL to a PreparedStatement → **FALSE — SQL is fixed, only parameters change ✗**

PreparedStatement is compiled once, then reused with different values → **TRUE ✓**

PreparedStatement is immune to SQL injection → **TRUE.**

You can reuse a PreparedStatement after it's closed → **FALSE — Must create a new one ✗**

`Statement.executeQuery()` returns ResultSet; `executeUpdate()` returns int → **TRUE.**

---

## 📄 Page 26 (IMG 7613) — ResultSet edge cases + ResultSetMetaData traps

**Topic: ResultSet edge cases**

ResultSet cursor starts **at** the first row → **FALSE — Starts BEFORE the first row ✗**

Calling `rs.next()` moves the cursor to the next row → **TRUE.**

`rs.getString(0)` returns the first column → **FALSE — column indexes are 1-based ✗**

You can get a column by name or index → **TRUE.**

`rs.getInt("non-existing-column")` returns 0 → **FALSE — throws SQLException ✗**

ResultSet can be used in a try-with-resources → **TRUE.**

---

**Topic: ResultSetMetaData traps**

You can get column count from `ResultSetMetaData` → **TRUE.**

`getColumnName(0)` gives the first column name → **FALSE — index must be 1 or higher ✗**

`getColumnTypeName()` gives you the DB's column type (e.g. VARCHAR, INT) → **TRUE.**

You need to know column names to use `ResultSetMetaData` → **FALSE — it helps when you DON'T know them ✗**

---

## 📄 Page 27 (IMG 7614) — JDBC Resource Cleanup + Regex + Soft Assertions

**Topic: JDBC Resource Cleanup**

Not closing ResultSet or a Statement may cause **memory leaks on the DB server** → **TRUE.**

`try-with-resources` auto-closes JDBC resources in **reverse order** → **TRUE.**

Closing the **Connection** will close the associated ResultSet and Statement → **TRUE.**

You need to close both ResultSet and Statement manually even after closing Connection → **FALSE — closing Connection closes everything.**

---

**Topic: Regex (filename pattern)**

- `^` = start of the filename
- `[a-z]` = One lowercase letter
- `[A-Z]` = One uppercase letter
- `\d{8}` = Exactly 8 digits
- `\.` = literal dot (not any character)
- `$` = end of filename
- `_` = underscore (literal)

---

**Topic: Soft Assertions (AssertJ)**

- **Record all failures** during the test (don't stop at the first one).
- Don't immediately stop when one assertion fails.
- Allow **multiple assertions** to be checked in one run.
- `softly.assertAll();` ← **Must call this.**
- Without `assertAll()`, you've checked **nothing** effectively.

---

## 📄 Page 28 (IMG 7615) — Mutation Testing key terms + AutoCloseable deep-dive

**Topic: PIT Mutation Testing (key terms)**

What it does:
1. Modifies your code (mutates) slightly (e.g. `+`→`-`, `>`→`<`, `true`→`false`).
2. Then runs your existing unit tests.
3. If your test **fails** → mutation is **killed** ✓
4. If your test **passes** → mutation **survived** ✗ (bad).

**Mutation Outcomes:**

*Mutation Survived:*
→ Your test **didn't detect** the bug — it's **too weak**.

*Mutation Killed:*
→ Your test **correctly caught** the issue — it's **strong**.

**Key Terms:**
- **Mutant** — a version of your code with a small change.
- **Killed** — the test caught the mutant (test fails as expected).
- **Survived** — the test missed the mutant (test still passes — bad).
- **Mutation score** — % of mutants killed. **Higher is better.**

---

**Topic: AutoCloseable (detailed rules)**

AutoCloseable **only closes automatically** if used in a try-with-resource.

1. In a try-with-resource, `close()` is called **even if an exception occurs.**
2. Multiple resources are closed in **reverse order** (b.close() first, then a.close()).
3. `AutoCloseable` can throw **any exception.**
4. If you don't use try-with-resources, the object **won't close automatically** — even if it implements `AutoCloseable`.

---

## 📄 Page 29 (IMG 7616) — Exception flow: catch/finally scenarios

**Topic: Exception flow (the three scenarios)**

**1. If exception is thrown AND caught:**
- catch block **runs** ✓
- finally **runs** ✓
- code after finally **runs** ✓

**2. If exception is thrown and NOT caught:**
- catch block does **NOT run** ✗
- finally **DOES run** ✓
- code after finally does **NOT run** ✗

**3. If NO exception happens at all:**
- catch **NOT run** ✗
- finally **runs** ✓
- code after finally **runs** ✓

---

> **End of notebook section.** All 29 pages transcribed in camera order (IMG 7588 → 7616), which roughly follows the exam question sequence as your friend remembered it.
