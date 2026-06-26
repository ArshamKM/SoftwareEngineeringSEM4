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
 
