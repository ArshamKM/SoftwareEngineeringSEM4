# PRC2 Study Book v2 — Full Course + Exam

This version folds in **all 11 weekly pages** from the course site on top of your friend's exam review. Structure per topic:
- **🧠 Concept** — what it is and *why*.
- **⚙️ Mechanics** — rules, syntax, the course details.
- **🎯 Exam layer** — the True/False traps from the review.

New full sections your friend's notes skipped are marked **★ GAP-FILL** (state machines, security, annotations, wildcards/PECS, IEEE-754 floats, i18n bundles).

> **Contradictions in the review notes — settled:**
> 1. **Closing a `Connection` closes its Statements/ResultSets → TRUE** (three pages agree + course confirms try-with-resources closes everything).
> 2. **`LocalDateTime` has no offset/zone → TRUE.** Read the exam's wording carefully.
> 3. Log-level ladder bottom rung is **ALL** (logs everything), not "OFF".

---

# 1. Testing fundamentals & TDD

### 🧠 Concept
Tests are an automated "English teacher" that checks your code so your own brain — which is biased to believe its own code works — doesn't have to. **Test-Driven Development** flips the order: write a *failing* test first (**Red**), make it pass with minimal code (**Green**), then **Refactor**. Writing the test first stops you marrying your implementation, and forces design decisions early: *if it's hard to test, you didn't build it right.*

### ⚙️ Mechanics
- **Arrange–Act–Assert (AAA / triple-A):** set up the SUT + dependencies (Arrange), call one method on the SUT (Act), check the result/new state (Assert).
- **SUT** = System Under Test (the protagonist). **DOC** = Dependent-On Component (collaborators).
- **F.I.R.S.T. principles:** Fast, Independent, Repeatable, Self-validating, Timely.
- **Where to start a class (TDD order):** constructor → getters → `toString()` → setters (only if needed) → business methods (easiest first) → refinements.
- **Test selection:** width-first (test all parts shallowly) vs depth-first (one part fully), and easy-first vs hard-first — heuristics, not rules.
- **equals/hashCode recipe:** equal objects must have equal hashCode (reverse not required). To fully cover a generated `equals`, test against: `this`, `null`, a different type, an equal object, and one object differing in *each* field.
- Golden rule: **never add a member without a test proving you need it.** And **CODE THAT ISN'T WRITTEN CAN'T BE WRONG.**

### 🎯 Exam layer
- TDD = test **before** code (Red→Green→Refactor).
- A unit test is **isolated**, **fast**, no external dependencies.
- Equal objects → equal hashCode (TRUE); equal hashCode → equal objects (**FALSE**).

---

# 2. JUnit 5 & AssertJ — assertions, parameterized, exceptions

### 🧠 Concept
JUnit runs tests; AssertJ provides a fluent, readable assertion API entered through `assertThat(...)`. A test with no real assertion is worthless.

### ⚙️ Mechanics
**AssertJ basics:** `assertThat(actual).isEqualTo(expected)`, `.isTrue()/.isFalse()` (booleans), `.contains(...)`, `.hasSize(...)`, `.extracting(...)`, `.containsExactlyInAnyOrder(...)`. Add `.as("description")` for better failure messages. Add a cheap `.isNotNull()` first to self-protect against NPEs. `isEqualTo` = equals; `isSameAs` = reference identity (`==`).

**Exception testing — three cases:**
1. Don't care about a checked exception → declare `throws` on the test method.
2. Expect **no** exception → `assertThatCode(code).doesNotThrowAnyException();`
3. Expect an exception → `assertThatThrownBy(code).isExactlyInstanceOf(X.class).hasMessageContaining(...)`.
   - JUnit's own form: `assertThrows(X.class, () -> ...)`.
   - The lambda type is `ThrowingCallable` (a functional interface).

**Soft assertions:** collect *all* failures, report at the end — needed when several related values must all be checked. `SoftAssertions.assertSoftly(softly -> { ... })` (or `assertAll()`). **Forget to close/assertAll → nothing is verified.**

**Assumptions:** skip a test when a precondition fails (`assumeTrue(...)`, `assumeThat(...)`). A skipped-by-assumption test shows as *skipped*, like `@Disabled`. Put an assumption in `@BeforeAll` to disable a whole class.

**Parameterized tests:** one method, many data rows.
- `@ParameterizedTest` + a source: `@ValueSource`, `@CsvSource` (inline rows, single-quote strings containing commas), `@CsvFileSource` (external CSV in `src/test/resources`), `@MethodSource` (a static method returning the data/stream).
- The method **takes arguments** matching the row; JUnit auto-converts strings to primitives/`String`/`LocalDate` (ISO-8601).
- General advice: **one logical assert per test** (a failure stops the rest of the method).

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| A parameterized test can be `private` | **FALSE** |
| It can run with no data source | **FALSE** (needs ≥1, e.g. `@ValueSource`) |
| It must take arguments | **TRUE** |
| Data can come from a static method (`@MethodSource`) | **TRUE** |
| `@Test` + `@ParameterizedTest` on one method | **FALSE** |
| Skip `assertAll()` with soft assertions | **FALSE** — nothing verified |
| Bare try-catch in a test (no `fail()`) | bad practice — use `assertThrows`/`assertThatThrownBy` |

---

# 3. Testability & Mocking

### 🧠 Concept
**Testability is a non-functional requirement.** Untestable code is unmaintainable. Three properties: **Controllability** (drive it into a state), **Observability** (see what it did), **Smallness** (small unit). The key design rule: **program against interfaces, not concrete classes**, and **never let business code create its own dependencies** — *inject* them. That's **Dependency Injection**: pass services via constructor/setter instead of `new`-ing them inside. This keeps the SUT testable and observable.

### ⚙️ Mechanics
**Test doubles** replace DOCs (never the SUT):
- **Stub** — holds pre-set data / canned answers.
- **Mock** — fake whose calls you **verify** (`verify(mock).method(...)`); train with `when(...).thenReturn(...)`.
- **Spy** — wraps a **real** object; observes calls, overrides some behaviour, keeps the rest real.

**Mockito details:**
- `mock(Printer.class)`, `@Mock` field, `verify(...)`, argument matchers `anyString()`, `any()`, `eq()`.
- **ArgumentCaptor** captures what was passed to a mock so you can assert on it.
- Mockito **warns about unnecessary stubbings** — every trained method should be used; an unused one is a smell.
- **Real-class config beats mocking:** e.g. stub input with `new Scanner("12\n13\n")`; redirect output with a custom `PrintStream`. **Don't mock entity classes** (Student/Product) — they're just data; use the real thing or a record.

**End-to-end testing:** **REST-assured** (given-when-then DSL) tests the whole REST API; **Testcontainers** spins up a real DB in Docker for isolated, reproducible tests. E2E tests are slow/flaky → prefer many isolated unit tests, which also pressure your design toward loose coupling.

### 🎯 Exam layer
- **Mock the SUT and still test its logic → FALSE.** You'd be testing the mock; none of the SUT's code runs.
- Stub = data/canned returns · Mock = verify interactions · Spy = real object partly overridden.
- Dependency injection = providing dependencies from outside (constructor/setter), not creating them inside.

---

# 4. Mutation Testing (PIT)

### 🧠 Concept
Tests check code; **mutation testing checks the tests.** PIT introduces small **mutations** (`+`→`-`, `>`→`<`, `true`→`false`) and reruns your tests. Good tests should fail on the broken code.

### ⚙️ Mechanics / 🎯 Exam layer
- **Mutant** = code with one small change. **Killed** = a test failed (good). **Survived** = all tests passed despite the bug (your tests are too weak). **Mutation score** = % killed (higher = better).
- Purpose = **assess test quality**, not code correctness.

---

# 5. Enums

### 🧠 Concept
A fixed, closed set of named constants that are also objects. Each constant is a single immutable instance created at class-load time — so the set of states can never grow.

### ⚙️ Mechanics
- Implicitly `final` (can't extend); each constant **is an instance**; can have fields, **private** constructors, static methods; can **implement interfaces** and declare **abstract methods** (each constant then implements them).
- Enum's own superclass: `Enum<E extends Enum<E>>` (a self-type — see Generics). Enums and records are **leaf classes** — not extendable.

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| An enum is always final | **TRUE** |
| Enum constructors can be public | **FALSE** (always private) |
| Enums can implement interfaces | **TRUE** |
| Enums can have abstract methods | **TRUE** (each constant implements) |
| Enums can extend a class | **FALSE** |

---

# 6. Generics

### 🧠 Concept
Generics give **compile-time type safety** without casts. They are mainly a **compiler** feature: after checking, the compiler **erases** the type info, so at runtime `List<String>` and `List<Integer>` are both raw `List`. Erasure (introduced Java 5, alongside autoboxing) explains every "gotcha": anything needing the type at runtime is impossible.

### ⚙️ Mechanics
```java
class Box<T> { T content; }        // generic class
<T> T firstOf(List<T> xs) { ... }  // generic method: <T> before return type
```
- ❌ `new T()`, ❌ `new T[10]`, ❌ `Box<int>` (use `Box<Integer>`), ❌ `static T field;`. ✅ generic interfaces.
- **Generics are invariant:** `List<Circle>` is **NOT** a `List<Shape>`, even though `Circle` is a `Shape`.
- **Raw type** (`ArrayList al = new ArrayList()`) compiles with an **unchecked warning**; casting through a raw alias can cause a runtime **`ClassCastException`**.

**★ Wildcards & bounds — PECS (gap-fill, not in your notes):**
- `? extends T` — **upper bound**, **covariant**. A *Producer* you read T's out of. You can **get** (as T) but **can't add** (except null). `List<? extends Shape>` accepts `List<Circle>`.
- `? super T` — **lower bound**, **contravariant**. A *Consumer* you put T's into. You can **add** T, but reads come out as `Object`. `List<? super Circle>` accepts `List<Shape>`.
- Mnemonic **PECS: Producer Extends, Consumer Super.**
- `?` (unbounded) — "of any type", e.g. `Class<?>`.

**Type tokens & runtime types:**
- A `Class<T>` passed around is a **type token**; `Collections.checkedList(list, Student.class)` guards insertions, throwing `ClassCastException` at the offending insert.
- `clazz.isAssignableFrom(other.getClass())` = runtime check "would a cast work?" (then `clazz.cast(obj)`). Contrast `instanceof`, which is a **compile-time** check needing a statically known type.
- **Self-types:** `Enum<E extends Enum<E>>`, `String implements Comparable<String>`, and fluent APIs (`duck.fly().swim().walk()`) use a `self()` returning the leaf type. Heavily used in AssertJ.

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| `new T()` / `new T[10]` allowed | **FALSE** (erasure / no generic arrays) |
| `Box<int>` valid | **FALSE** (use wrapper) |
| Static field can use class type parameter | **FALSE** |
| Generic interfaces exist | **TRUE** |
| Raw types fail to compile | **FALSE** (compile with warning) |
| Type available at runtime | **FALSE** (erasure) |
| `List<Circle>` is a `List<Shape>` | **FALSE** (invariant) |
| `? extends` = Producer/read; `? super` = Consumer/write | **PECS** |

---

# 7. Lambdas, Functional Interfaces & Streams

### 🧠 Concept
A **lambda** implements a **functional interface** (exactly one abstract method), letting you pass behaviour like data. Java 8 added this without breaking old code by allowing **default** and **static** methods on interfaces (e.g. Java 8 `Comparator` has 1 abstract + many default/static methods). A **Stream** is a conveyor belt: a source, lazy **intermediate** ops, and one eager **terminal** op.

### ⚙️ Mechanics
**Functional-interface SHAPE** (`java.util.function`), notation `input → output`:
- `Supplier<T>`: `() → T` · `Consumer<T>`: `T → {}` · `Function<T,R>`: `T → R` · `BiFunction<T,U,R>`: `(T,U) → R` · `Predicate<T>`: `T → boolean`.
- Primitive specializations (e.g. `ToIntFunction`, `LongConsumer`) exist for efficiency.
- Lambdas have **no useful `toString()`**. Checked exceptions in lambdas are awkward → wrap (e.g. `UncheckedIOException`).

**Method references** (`::`), 4 kinds: static `Class::m`, bound instance `obj::m`, unbound instance `Type::m`, constructor `Class::new` (also `this::m`). You can't omit the `::` or left side.

**Stream pipeline = source → intermediate(s) → terminal:**
- Intermediate (lazy, return Stream): `filter, map, mapToInt, sorted, distinct, limit, peek`.
- Terminal (eager): `collect, forEach, reduce, count, sum, anyMatch, findFirst`.
- Exactly **one** terminal, at the end. SQL-like: `people.stream().filter(...).mapToInt(...).sum()`.

**Optional:** like a stream of 0-or-1; many terminals return it. `map` on an Optional transforms its content; chain `.map(...).map(...)` to avoid null checks. Prefer returning `Optional<T>` or an empty collection over `null`.

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| Functional interface = exactly one abstract method | **TRUE** |
| Can also have default/static methods | **TRUE** |
| `@FunctionalInterface` required | **FALSE** (optional) |
| Intermediate ops run immediately | **FALSE** (lazy) |
| Terminal ops at the end | **TRUE** |
| Two terminal ops in one pipeline | **FALSE** (only one) |

---

# 8. Exceptions

### 🧠 Concept
An exception disrupts normal flow. The whole hierarchy descends from **`Throwable`**. `Error` = serious JVM/system problems (unchecked, don't handle). `Exception` = your program's problems; **`RuntimeException`** under it = programming bugs (unchecked). **Checked** (everything else) = the compiler forces handle-or-declare, for recoverable external issues.

### ⚙️ Mechanics
- **Checked:** `IOException`, `SQLException`, `ClassNotFoundException` — must `try/catch` or `throws`.
- **Unchecked:** `NullPointerException`, `IndexOutOfBoundsException`, `IllegalArgumentException`, `ArithmeticException`.
- `throw` = throw an object; `throws` = declare a method may throw.
- **`finally`** runs under all circumstances (cleanup) — almost always (not on `System.exit()` / JVM crash).
- **Compiler rule:** a `try` needs **at least one of** catch (many allowed) **or** finally. `try{}` alone is illegal; `try(resource){}` is legal (implicit finally).
- **Custom exceptions** extend `Exception` (or `RuntimeException`); pass a message to `super(...)`.
- **Chaining:** an exception can hold a `cause` (`Throwable`); walk it with `getCause()` in a loop.

**catch/finally flow:**
| Situation | catch | finally | code after finally |
|---|---|---|---|
| Thrown & caught | ✅ | ✅ | ✅ |
| Thrown, not caught | ❌ | ✅ | ❌ |
| No exception | ❌ | ✅ | ✅ |

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| All exceptions must be handled | **FALSE** (only checked) |
| Can declare `throws` for an unchecked exception | **TRUE** |
| `finally` always runs | **ALMOST** (not `System.exit`/crash) |
| A `try` must have a `catch` | **FALSE** (`try…finally` ok) |
| `catch (Throwable)` | **TRUE** but bad (catches `Error`s) |
| Two catches for same type / `IOException \| Exception` | **compile error** |
| Throw any object | **FALSE** (only `Throwable`/subclass) |

---

# 9. Immutability (+ BigInteger/BigDecimal)

### 🧠 Concept
An immutable object's fields never change after construction; "modifying" returns a **new** object. Safe to share/cache/use as keys. `String` is the classic case.

### ⚙️ Mechanics
- `s.concat("x")` returns a new String; `s` is unchanged unless you reassign `s = s.concat("x")`.
- **BigInteger** (arbitrary-precision integer) and **BigDecimal** (arbitrary-precision decimal) are **immutable**. Use BigDecimal for money/science.
- **Trap:** `new BigDecimal(0.1)` is **wrong** (inherits double's imprecision); `new BigDecimal("0.1")` (String) is **correct**.

### 🎯 Exam layer
- `s.concat(...)` without reassign → unchanged.
- BigInteger/BigDecimal immutable & arbitrary-precision; construct BigDecimal from a **String**.

---

# 10. Reflection & the `Object` class

### 🧠 Concept
**Reflection** inspects/manipulates code at **runtime** — classes, fields, methods, constructors, annotations — even private members. It powers frameworks (JUnit, Mockito, ORMs). Costs: slower (security checks + indirection), **less type-safe**, breaks encapsulation.

### ⚙️ Mechanics
**Get a `Class`** three ways: `obj.getClass()`, `Type.class`, `Class.forName("fully.qualified.Name")` (throws **ClassNotFoundException**).
- **Constructors:** `clazz.getConstructor(String.class).newInstance("x")`.
- **Methods:** `clazz.getMethod("getName").invoke(instance)`; static → `invoke(null)`; with params → `getMethod("m", String.class).invoke(null, "x")`.
- **Fields:** `clazz.getDeclaredField("name")`; private → `field.setAccessible(true)` then `field.get(instance)`.
- **Modifiers as bit flags:** `getModifiers()` returns an int; PUBLIC=1, PRIVATE=2, PROTECTED=4, STATIC=8, FINAL=16… check with `&`.
- **Hierarchy:** `getSuperclass()`, `getInterfaces()`, `isInterface()`, `field.getType()`, `field.getDeclaringClass()`.

**`Object` class:** every class implicitly extends `java.lang.Object` (root). Inherited: `toString()`, `equals(Object)`, `hashCode()`, `getClass()`. `getClass()` returns the **runtime** type.
**Correct Class references:** ✅ `Object.class`, `Class.forName("java.lang.Object")`, `new Object().getClass()` · ❌ `Object.getClass()` (instance method, not static).

### 🎯 Exam layer
- Every class implicitly extends `Object` → **TRUE**; `getClass()` = runtime type → **TRUE**; `Object.getClass()` → **FALSE**.
- Reflection can read private fields (with `setAccessible(true)`) → **TRUE**.
- `Class.forName` can throw **ClassNotFoundException**.

---

# 11. ★ Annotations (gap-fill)

### 🧠 Concept
Annotations (Java 5) attach **metadata** to code elements without changing behaviour. Frameworks read them via reflection (e.g. JUnit finds `@Test`).

### ⚙️ Mechanics
- Built-in: `@Override`, `@Deprecated`, `@SuppressWarnings`. Library: `@Test`, `@ParameterizedTest`.
- Define with `@interface`. Parameters are method-like: `String name();`, `int year();`, `String[] x() default {};`. A single `value()` lets callers omit `value=`.
- **`@Retention`**: `SOURCE` / `CLASS` / **`RUNTIME`** — only RUNTIME is readable by reflection.
- **`@Target`** (ElementType): `TYPE`, `METHOD`, `FIELD`, `PARAMETER`, `PACKAGE`.
- Read via reflection: `field.isAnnotationPresent(Hidden.class)`, `field.getAnnotation(Hidden.class).value()`, or loop `getAnnotations()` + `annotationType()`.

### 🎯 Exam layer
- To read an annotation at runtime it must be `@Retention(RUNTIME)`.
- `@Target` restricts where an annotation may be applied.
- Frameworks like JUnit use reflection + annotations to find and run tests.

---

# 12. JPMS — Java Platform Module System

### 🧠 Concept
Introduced in Java 9 (project **JigSaw**). Adds a layer above packages for **strong encapsulation**: a module declares what it **requires** (reads) and **exports**, so a `public` class in a non-exported package is invisible outside. Dependencies are checked **at JVM startup** — missing requirement → JVM refuses to start (clear early failure vs classpath's late `ClassNotFoundException`).

### ⚙️ Mechanics
- `module-info.java` in the source root; **no `import`** inside it. `java.base` is required by default.
- Keywords: `module`, `requires`, `requires transitive`, `exports`, `exports … to`, `opens`, `opens … to`, `open module` (whole module reflective), `uses`, `provides … with` (services / `ServiceLoader`), `to`.
- `exports` = compile/runtime access to a package. `opens` = **reflective** access (frameworks); `opens` ≠ `exports`. `requires transitive` re-exposes a dependency to your consumers (public API only).
- **Forbidden:** split packages (same package in two modules) and **cyclic** dependencies.
- **Reflection + JPMS access** (gap-fill table):

| Package | Normal public access | Reflective private access |
|---|---|---|
| encapsulated (not exported, not opened) | ✗ | ✗ |
| only-exported | ✓ | ✗ (`setAccessible` fails) |
| only-opens | ✗ | ✓ |
| full (exported + opened) | ✓ | ✓ |

- **Testing:** unit tests need package-private/private access (**white box**), which JPMS would block; Maven *surefire* patches the test classes into the module (and uses `--add-opens`). **Black box** = test only the exported API, like a normal client.

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| `requires com.myapp.api` (a package) | **FALSE** (requires names a **module**) |
| `exports java.sql` (another module's pkg) | **FALSE** (exports names a pkg in *this* module) |
| `opens` automatically exports | **FALSE** |
| `requires transitive` gives private-member access | **FALSE** (public API only) |
| `import` allowed in module-info | **FALSE** |
| Split packages / cyclic deps allowed | **FALSE** |

---

# 13. JDBC — database access

### 🧠 Concept
Standard API for relational DBs. The **API (`java.sql`) is vendor-neutral**; a **Driver** (vendor-specific) plugs in. The DB lives in the **persistence layer** of a layered architecture — SQL belongs nowhere else.

### ⚙️ Mechanics
- Flow: `DataSource`/`DriverManager` → `Connection` → `Statement`/`PreparedStatement` → `ResultSet`. `Connection` is `AutoCloseable` → use try-with-resources.
- **5 interfaces:** Connection, Statement/PreparedStatement/CallableStatement, ResultSet, ResultSetMetaData, DriverManager. **Vendor** = Oracle/MySQL/PostgreSQL.
- **ResultSet:** cursor starts **before** the first row; `next()` advances; rows fetched **lazily**; columns **1-based** (by index or name). `getInt("bad")` → SQLException.
- **PreparedStatement:** fixed SQL with `?` placeholders, set via `setObject(i, v)` (1-based), **compiled once / reused**, **prevents SQL injection**. `executeQuery()`→ResultSet, `executeUpdate()`→int. `returning *` to get back inserted rows/generated ids.
- **Credentials** go in a `.properties` file (host/port/user/password/dbname/schema), loaded via `getResourceAsStream` — never hard-coded, never committed.
- **Transactions:** all-or-nothing (`setAutoCommit(false)`, `commit()`, `rollback()`); locking → too coarse = slow, too fine = **deadlocks**.
- **"Never evers":** no SQL outside persistence layer · no `postgres` superuser in the app · no hard-coded credentials · don't commit credential files · don't leak `SQLException` to the business layer (wrap in a `PersistenceException`).

### 🎯 Exam layer
**Connection/driver**
| Statement | Answer |
|---|---|
| `getConnection()` uses JDBC URL to pick driver | **TRUE** |
| `jdbc:mysql://…` works for PostgreSQL | **FALSE** (must match vendor) |
| Always need `Class.forName(driver)` | **FALSE** (auto-loads) |
| Multiple connections from one app | **TRUE** |
| Closing Connection closes its Statements/ResultSets | **TRUE** |
| Must still close them manually after | **FALSE** |
| Not closing → DB memory leaks | **TRUE** |
| try-with-resources closes in reverse order | **TRUE** |

**Statement vs PreparedStatement**
| Statement | Answer |
|---|---|
| Pass dynamic SQL to a PreparedStatement | **FALSE** (SQL fixed, params change) |
| Compiled once, reused | **TRUE** |
| Immune to SQL injection | **TRUE** |
| Reusable after closed | **FALSE** |
| `executeQuery`→ResultSet, `executeUpdate`→int | **TRUE** |

**ResultSet / MetaData**
| Statement | Answer |
|---|---|
| Cursor starts at first row | **FALSE** (before it) |
| `getString(0)` = first column | **FALSE** (1-based) |
| `getInt("bad-col")` returns 0 | **FALSE** (SQLException) |
| MetaData gives column count | **TRUE** |
| `getColumnName(0)` | **FALSE** (≥1) |
| Must know column names to use MetaData | **FALSE** |

---

# 14. AutoCloseable & try-with-resources

### 🧠 Concept
Resources must be released or they leak. `AutoCloseable` + try-with-resources auto-calls `close()` at the end, **even on exception** — replacing the old finally-close dance.

### ⚙️ Mechanics
- `AutoCloseable` (java.lang); `close()` may throw. Multiple resources close in **reverse** order. Outside try-with-resources, **nothing** auto-closes.
- A `try(resource){}` needs no explicit catch/finally (implicit finally).

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| `close()` runs even on exception | **TRUE** |
| Reverse-order close | **TRUE** |
| AutoCloseable auto-closes without try-with-resources | **FALSE** |
| `ResultSet` is AutoCloseable | **TRUE** |
| `ResultSetMetaData` is AutoCloseable | **FALSE** |
| Must close `DatabaseMetaData` | **FALSE** |

---

# 15. REST / HTTP

### 🧠 Concept
REST fixes the inconsistent-URL problem of ad-hoc APIs with a **uniform interface**: resources are nouns identified by URIs, and **HTTP methods** express intent. **Stateless** by design — every request carries all needed info; the server keeps no session.

### ⚙️ Mechanics
**Six REST constraints:** Client-Server · **Statelessness** · **Cacheability** (via headers) · **Layered System** (proxies/load balancers) · Code-on-Demand (optional) · **Uniform Interface**.
- Uniform-interface details: resource identification (URIs, **nouns, plural** — `/users/123`, not `/getUsers`), manipulation via representations (**JSON**), self-descriptive messages (headers), **HATEOAS** (responses include links to related resources).
- **Request line:** `<method> <URI> <HTTP version>`. **Headers** = key-value metadata (Host, Accept, Content-Type, Authorization, Cookie…). **Body** for POST/PUT/PATCH (set `Content-Type`, e.g. `application/json`).
- **Response line:** `<HTTP version> <status code> <status message>`.

**Methods — safe & idempotent:**
| Method | Safe | Idempotent |
|---|---|---|
| GET | ✅ | ✅ |
| POST | ❌ | ❌ |
| PUT | ❌ | ✅ |
| PATCH | ❌ | ❌ |
| DELETE | ❌ | ✅ |

**Status codes:** 1xx info · 2xx success · 3xx redirect · 4xx client · 5xx server.
200 OK · 201 Created · 204 No Content · 301 Moved · 302 Found · 304 Not Modified · 400 Bad Request · 401 Unauthorized · 403 Forbidden · 404 Not Found · 405 Method Not Allowed · 415 Unsupported Media Type · 422 Unprocessable Entity · 429 Too Many Requests · 500 Internal Server Error · 502 Bad Gateway · 503 Service Unavailable · 504 Gateway Timeout.

### 🎯 Exam layer (the "NOT TRUE" traps)
| Statement | Answer |
|---|---|
| POST is idempotent | **NOT TRUE** |
| DELETE is not idempotent | **NOT TRUE** (it is) |
| PATCH always idempotent like PUT | **NOT TRUE** |
| PUT is not idempotent | **NOT TRUE** (it is) |
| GET may modify data | **NOT TRUE** (safe) |
| HTTP is stateful | **NOT TRUE** (stateless) |
| All methods use the same status code | **NOT TRUE** (POST→201, GET→200, PUT/PATCH→200/204, DELETE→204) |

---

# 16. Logging (java.util.logging — "JUL")

### 🧠 Concept
Logging records execution for diagnosis/auditing/monitoring **without a debugger**, and is **configurable, filterable, hierarchical** — unlike `System.out.println`.

### ⚙️ Mechanics
- **5 components:** **Logger** (entry point; hierarchical names; uses parent/root handlers if none) → **Handler** (where: **`ConsoleHandler`→`System.err`**, `FileHandler`→rotating file) → **Formatter** (how: `SimpleFormatter` default, `XMLFormatter`) → **Level** → **LogManager** (global config, reads `logging.properties`).
- **Double filter:** message must pass the **logger's** level then the **handler's** level.
- **Level ladder (high→low):** `OFF` → SEVERE → WARNING → INFO → CONFIG → FINE → FINER → FINEST → `ALL`. **Default = INFO** ⇒ INFO/WARNING/SEVERE print. A set level logs that level **and everything above (more severe)**.
- **Lazy logging:** pass a `Supplier` lambda — `logger.log(Level.FINE, () -> expensive())` — only evaluated if the level is active. Configure per-class level in `logging.properties` via `-Djava.util.logging.config.file=...`.

### 🎯 Exam layer
| Statement | Answer |
|---|---|
| Default level INFO | **TRUE** |
| At default, FINE prints | **FALSE** |
| ConsoleHandler → `System.out` | **FALSE** (`System.err`) |
| A logger can have multiple handlers | **TRUE** |
| `ALL` logs all, `OFF` logs nothing | **TRUE** |
| Message passes logger **and** handler level | **TRUE** |

---

# 17. Date/Time API & ★ Internationalisation (i18n)

### 🧠 Concept
`java.time` types are immutable; each carries a **different amount of context** — pick wrong and you get bugs. i18n separates locale-specific text from code so the same program speaks many languages.

### ⚙️ Mechanics
- **Instant** = a point on the timeline, **always UTC**; epoch = 1970-01-01 UTC (seconds as `long` + nanos as `int`). **Duration** = time between two Instants. **Period** = time between dates.
- **LocalDate / LocalTime / LocalDateTime** — local, **no zone/offset** (prefer these unless you truly need zones).
- **OffsetDateTime** — + offset, no region. **ZonedDateTime** — + offset + region (IANA tz db). Convert: `instant.atZone(ZoneId.of("Europe/Amsterdam"))`.
- **Offset** = difference from UTC: `2025-07-06T20:00+02:00` → UTC 18:00.
- **DateTimeFormatter** for formatting (locale-aware).
- **★ ResourceBundle + MessageFormat:** put strings in `.properties` files, one base + one per locale (`greetings.properties`, `greetings_nl_NL.properties`). `ResourceBundle.getBundle("greetings", Locale.getDefault())`; `MessageFormat.format(pattern, args)` fills `{0}`,`{1}`. **Fallback chain:** `_nl_NL` → `_nl` → base. Guard with `containsKey` to avoid `MissingResourceException`. Test localized exceptions via `.extracting(e -> e.getLocalizedMessage())`.

### 🎯 Exam layer
- `LocalDateTime` has no offset/zone → **TRUE** (watch wording).
- `ZonedDateTime` full zone → **TRUE**; `OffsetDateTime` offset, no region → **TRUE**.
- `Instant` is always UTC → can represent "any local zone" is **FALSE**; convertible to zoned → **TRUE**.
- i18n strings live in **`.properties`** bundles; `{0}` filled by `MessageFormat`; locale fallback to base.

---

# 18. ★ State Machines (gap-fill)

### 🧠 Concept
A **state machine** models behaviour that depends on **history/events over time** (stateful), unlike a pure function. Example: a switch — current only flows in the "on" state. Two tools: the **state diagram** and the **State pattern**.

### ⚙️ Mechanics
- **State (GoF) pattern:** a **context** holds a reference to an abstract **State**; multiple **concrete states** implement the same interface. On an **event**, the context swaps its current state object for another. Each state behaves like a pure function (same input → same output); switching states changes the path (railway-switch analogy).
- **Vocabulary:** state, transition, event/trigger, action.
- **Enum-based states (this course):** states as enum constants → no new states ever, immutable, shareable across contexts, easily tested with a **mocked** context. Downside: a functional state can't hold its own data (e.g. a counter) — store that in the context, or make counts their own states (traffic light: green/yellow/red).
- **Constructor pitfall:** don't call `state.enter(this)` inside the constructor (object not fully built). Use an `init()` method called after construction: `new StateMachine().init()`.
- State vs Strategy pattern (same structure, different intent): State changes behaviour as internal state changes; Strategy swaps an externally chosen algorithm.

### 🎯 Exam layer
- State pattern = context + abstract state + concrete states; current state object replaced on an event.
- Enum states are immutable/shareable, can't hold mutable data themselves.
- Don't call a state method taking `this` from the constructor — use `init()`.

---

# 19. Regular Expressions

### 🧠 Concept
A regex is a pattern matching text — and it **compiles to a state machine** (the railroad diagram). That's also why a bad regex can loop forever (**ReDoS** — a denial-of-service risk; don't accept untrusted regexes).

### ⚙️ Mechanics
- **Anchors:** `^` start, `$` end. **Any char:** `.`. **Escape:** `\` (in Java strings, double it: `\\.` = literal dot).
- **Quantifiers:** `?` 0–1, `+` ≥1, `*` ≥0, `{n}` exactly n, `{n,m}` range, `{n,}` at least n.
- **Char classes:** `[aeiou]`, `[a-z]`, `[A-Z]`, `[a-pA-P]`. **Predefined:** `\d` digit, `\D` non-digit, `\w` word `[a-zA-Z0-9_]`, `\W` non-word, `\s` whitespace.
- **Groups:** `( … )` capture; **group 0** = whole match, group 1 = first paren. **Named groups:** `(?<name>…)` → `m.group("name")`.
- **Pattern/Matcher:** `Pattern.compile(regex)` → `p.matcher(input)`. Match methods: **`find()`** (anywhere), **`matches()`** (whole string), **`lookingAt()`** (start). Example pattern: Dutch postcode `\d{4}\s[A-Z]{2}`.

### 🎯 Exam layer
- `^`/`$` anchor; `\d{8}` exactly 8 digits; `\.` literal dot; `[a-z]`/`[A-Z]` one lower/upper; `_` underscore.
- `find()` (substring) vs `matches()` (whole) vs `lookingAt()` (start).
- Group 0 = entire match; named group `(?<name>…)`.
- Regex ≈ state machine; untrusted regex → ReDoS risk.

---

# 20. ★ Security (gap-fill)

### 🧠 Concept
Security prevents data leakage, DoS, and remote code execution. This week revisits earlier topics through a security lens.

### ⚙️ Mechanics
**Class loaders** (load `.class` files into the JVM), three with a **delegation** model:
- **Bootstrap** (core/JDK base) → **Platform** (rest of platform) → **System/Application** (class path + module path). Loading delegates upward first; if nothing has it → **ClassNotFoundException**.
- **`URLClassLoader`** loads classes from a URL — a real risk (untrusted code can do anything). A legitimate custom-loader use: **decrypt encrypted `.class` files** before handing them to the JVM.

**Java cryptography (`javax.crypto`):**
- **Symmetric** (AES, ChaCha20): one shared key, **fast**, good for bulk data.
- **Asymmetric** (RSA, EC): key **pair**, **slow**, used to exchange a symmetric key.
- **AES-GCM** = authenticated encryption (ciphertext + tag detects tampering). Needs a key (16/24/32 bytes) and a unique **IV/nonce** (not secret).
- **Hashing ≠ encryption:** hashing is **one-way** (not reversible).

**Bytecode verifier:** before loading, the JVM verifies the class file (variables initialised before use, type-correct calls, access rules respected, stack bounds) → tampering throws **VerifyError**. (Java compiles to portable bytecode; `javap -c` shows it.)

**SQL injection** (the live example): concatenating user input into SQL lets `' or '1'='1` log in, and `UNION SELECT …` + `information_schema` enumerate tables/columns; `-- --` comments out the rest. **Fixes:** PreparedStatement (parameterized) **and** a **least-privilege DB user** (`CREATE ROLE app LOGIN; GRANT SELECT ON users TO app;`) — never the `postgres` superuser.

**Password management:** best is **not storing passwords** (OAuth 2.0). If you must: **hash** with a cryptographic hash (one-way, deterministic, diffusion, collision-resistant). Plain hashes are precomputable (rainbow tables) → add a **salt** (random, non-secret, per-password). Fast hashes are brute-forceable → use **slow** hashes: **bcrypt, argon2(id), scrypt, PBKDF2**.

**Transport security:** HTTP is unencrypted (sniffable, MITM-able). **HTTPS** adds a CA-signed **certificate** (proves identity) + **encryption** (session key). Let's Encrypt gives free certs.

**Error handling:** verbose errors leak system info (the SQL error revealed the query). Distinct "username not found" vs "wrong password" enables **username enumeration** → return a **generic** "username or password incorrect".

### 🎯 Exam layer
- Three class loaders (Bootstrap/Platform/System), delegate upward; missing class → ClassNotFoundException.
- Symmetric = one key/fast; asymmetric = key pair/slow. Hashing is one-way (≠ encryption).
- SQL injection fixed by PreparedStatement **and** least-privilege DB user (not the superuser).
- Salt defeats rainbow tables; use slow hashes (bcrypt/argon2/scrypt/PBKDF2).
- HTTPS = certificate (identity) + encryption. Generic error messages prevent enumeration.

---

# 21. Number Systems & Storage

### 🧠 Concept
Bits mean nothing until you choose an interpretation (the same 32 bits can be an int, a float, or ASCII text). Two exam staples: **base conversion** and **how numbers are stored** — two's complement for integers, **IEEE-754** for floats.

### ⚙️ Mechanics
**Bases:** place values are powers of the base. Decimal→base b: divide by b repeatedly, read remainders **bottom-to-top**. (45→octal = 55₈; 77→octal = 115₈.) Binary place values (byte): 128 64 32 16 8 4 2 1.

**Negative integers:**
- Naive **sign bit** has two zeros (+0/−0) and addition breaks → rejected.
- **Two's complement:** one zero, normal addition works, range **−2ⁿ⁻¹ … 2ⁿ⁻¹−1**; negatives still have MSB = 1. Convert: **flip all bits (one's complement) + 1**. (−6 in 8-bit: `00000110`→`11111001`→`11111010`.) This is what Java uses.

**★ Floating point (IEEE-754) — gap-fill (notes only had two's complement):**
- Based on binary scientific notation. **32-bit float:** 1 sign + 8 exponent + 23 mantissa. Formula: **(−1)^sign × (1 + mantissa) × 2^(exponent − 127)**.
- **Hidden/implicit bit:** the normalised leading `1.` isn't stored; the 23 bits hold only the fraction, and you add the `1` back.
- Bias = **127** for float. Example: `100.25` → `1.10010001 × 2⁶` → exponent `6+127=133`.
- **Special values** (reserved exponents): exponent all-0 + mantissa 0 → **±0**; exponent all-1 + mantissa 0 → **±Infinity**; exponent all-1 + mantissa ≠0 → **NaN**.
- **Why 0.1 isn't exact:** its binary fraction repeats forever (like 1/3 in decimal) → rounding error.
- **Double:** 64-bit = 1 sign + 11 exponent + 52 mantissa, bias **1023**.
- **BigDecimal fix:** `new BigDecimal("0.1")` (String, exact) — never `new BigDecimal(0.1)` (inherits the double error).

### 🎯 Exam layer
- Negatives stored in **two's complement** (flip + 1); range −2ⁿ⁻¹…2ⁿ⁻¹−1; conversions read remainders bottom-to-top.
- float = 1/8/23 (bias 127); double = 1/11/52 (bias 1023); implicit leading 1.
- exp all-1 + mantissa 0 = Infinity; exp all-1 + mantissa ≠0 = NaN; exp all-0 = (±)0.
- `0.1` not exactly representable; `BigDecimal` from a **String** for exactness.

---

## Weekend plan (Fri night → Sun)

- **Fri night:** read for 🧠 Concept + ⚙️ Mechanics — every "why" should click. Pay attention to the ★ gap-fill sections (state machines, security, annotations, PECS, IEEE-754, i18n) — they're newly covered and very examinable.
- **Sat:** cover the answer columns; quiz down every 🎯 table; note misses.
- **Sun:** redo misses, one clean pass, re-read the three flagged contradictions.
- **Exam:** the traps live in **NOT / always / never / only / cannot** — read each statement twice.

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
