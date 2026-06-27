# PRC2 — Everything You Need To Pass Monday

Built from: your friend's 29 notebook pages + teacher's 42 practice questions + GitHub repo (11 weekly pages) + the 13 topics you remember from the real exam.

**Target: 5.5 = 17/30 correct. You need to be right on 17 questions.**

---

# THE STRATEGY

The exam has multi-select questions where you can pick more than one answer. The key rule: **only tick what you're genuinely sure about**. One wrong tick on a multi-select costs you the whole question. So:

- Confident? → tick it.
- 50/50? → leave it.
- Guessing? → skip it.

17 solid correct answers beats 25 half-guessed ones.

---

# TOPIC 1 — TDD & Testing basics

**What TDD is:**
- Write a FAILING test first → make it pass → refactor. That order. Always.
- Red → Green → Refactor

**What is TRUE:**
- TDD cycle starts with a test that FAILS ✅
- A test that never failed is a BAD test ✅ (how do you know it works?)
- TDD automatically gives high code coverage ✅
- Triple-A = Arrange – Act – Assert ✅
- Unit tests test that a REQUIREMENT is fulfilled ✅
- AssertJ = library for fluent assertions (NOT a testing framework like JUnit) ✅
- Maven = build automation tool, uses central Maven Repository ✅

**What is FALSE:**
- "Write code first, then test" → NOT TDD ❌
- "Testing without TDD is bad" → testing is always good ❌
- "Code coverage measures code quality" → it measures execution, not quality ❌
- "Code coverage guarantees bug-free code" → absolutely not ❌

**SUT** = System Under Test. DOC = Dependent-On Component.

---

# TOPIC 2 — JUnit 5 & Parameterized Tests

**JUnit process (what it does):**
- Searches for methods annotated with @Test using Reflection ✅
- Creates a NEW instance per test method (NOT one instance for all) ✅

**Parameterized tests:**
- Use @ParameterizedTest (NOT @Test — never combine them) ✅
- Must have at least one data source: @ValueSource, @CsvSource, @MethodSource ✅
- @MethodSource = static method providing data ✅
- Must take arguments ✅
- Must NOT be private ✅

**Soft assertions:**
- Records all failures instead of stopping at first ✅
- Must call assertAll() at the end — without it NOTHING is verified ✅

**Exception testing:**
- assertThrows(ExceptionType.class, () -> method()) = correct way ✅
- Bare try-catch without fail() = bad — test passes silently if no exception thrown ❌
- Golden rule: never write a bare try-catch in a test ✅

---

# TOPIC 3 — Testability & Mocking

**Testability = NON-functional requirement**

Three properties:
- Controllability — can you put the system in the state you want?
- Observability — can you see what it did?
- Smallness — is the unit small enough?

**Test doubles:**
- Stub = returns predefined values, no verification
- Mock = fake whose INTERACTIONS you verify (verify() in Mockito)
- Spy = wraps a REAL object, tracks calls, overrides some behaviour

**The big trap:**
- "You can mock the SUT and still test its logic" → FALSE ❌
- Mock the DEPENDENCIES, never the SUT itself

**Dependency Injection** = passing dependencies in from outside (constructor/setter) instead of creating them inside with `new`.

---

# TOPIC 4 — Mutation Testing (PIT)

- PIT mutates code (+→-, >→<, true→false) and reruns your tests
- Purpose = assess the QUALITY of your tests (not the code)
- Mutant KILLED = test caught it = tests are STRONG ✅
- Mutant SURVIVED = test missed it = tests are too WEAK ❌
- Mutation score = % killed. Higher = better.

---

# TOPIC 5 — Enums

**TRUE statements:**
- Always implicitly final — cannot be extended ✅
- Each constant IS an instance of the enum type ✅
- Can implement interfaces ✅
- Can have abstract methods (each constant must implement) ✅
- Can have static methods ✅
- Constructors always PRIVATE ✅

**FALSE statements:**
- Can extend other classes ❌ (already extends java.lang.Enum)
- Constructors can be public ❌

---

# TOPIC 6 — Generics

**The core concept: TYPE ERASURE**
Generics are compile-time only. At runtime, List<String> and List<Integer> are both just List.

**ILLEGAL (remember these):**
- new T() ❌ — can't instantiate type parameter
- new T[10] ❌ — can't create generic array
- Box<int> ❌ — use Box<Integer> (no primitives)
- static T value ❌ — static can't use class type parameter

**LEGAL:**
- Generic interfaces ✅ (interface Processor<T> { T process(T in); })
- Raw type compiles but gives UNCHECKED WARNING (not illegal) ✅

**Generics are INVARIANT:**
- List<Circle> is NOT a List<Shape> even if Circle extends Shape ❌

**PECS for wildcards:**
- ? extends T = Producer = READ from it
- ? super T = Consumer = WRITE to it

**equals/hashCode:**
- Equal objects → MUST have equal hashCode ✅
- Equal hashCode → objects are equal → FALSE ❌ (collisions exist)

---

# TOPIC 7 — Lambdas, Functional Interfaces & Streams

**Functional interface:**
- Exactly ONE abstract method ✅
- Can have ANY NUMBER of default/static methods ✅
- @FunctionalInterface is OPTIONAL (just makes compiler check) ✅

**Streams:**
- Intermediate ops (filter, map, sorted, peek) = LAZY — nothing runs until terminal ✅
- Terminal ops (collect, forEach, count, reduce) = EAGER — triggers everything ✅
- Pipeline has EXACTLY ONE terminal operation ✅
- Terminal op is ALWAYS at the end ✅

---

# TOPIC 8 — Exceptions

**Hierarchy:** Throwable → Error (don't catch) / Exception → RuntimeException (unchecked)

**Checked** (must handle): IOException, SQLException, ClassNotFoundException
**Unchecked** (optional): NullPointerException, ArrayIndexOutOfBounds, ArithmeticException

**TRUE statements:**
- RuntimeException extends Exception ✅
- IOException must be caught or declared ✅
- try-with-resources must implement AutoCloseable ✅
- Goal of exceptions = avoid uncontrolled crash ✅
- finally runs ALMOST ALWAYS (not on System.exit / JVM crash) ✅
- throw and throws can appear in the same method ✅

**FALSE statements:**
- All exceptions must be handled ❌ (only checked)
- catch(Exception e) is good practice ❌ (too broad)
- You can throw any object ❌ (only Throwable subclasses)
- finally ALWAYS runs ❌ (ALMOST always)

**Exception flow — memorise the middle row:**
| Situation | catch | finally | code after |
|---|---|---|---|
| Thrown & caught | ✅ | ✅ | ✅ |
| Thrown NOT caught | ❌ | ✅ | ❌ |
| No exception | ❌ | ✅ | ✅ |

**Implementing interface exceptions:**
- Can declare same exception type ✅
- Can declare a SUBTYPE ✅
- Can declare NO exception ✅
- CANNOT declare a SUPERTYPE ❌ (would broaden the contract)

---

# TOPIC 9 — JPMS

**Core keywords:**
- requires = depends on a MODULE (not a package)
- requires transitive = also re-exposes to consumers (PUBLIC API only — not private)
- exports = gives other modules NORMAL access to a package
- opens = gives REFLECTIVE access to a package (frameworks need this)
- opens does NOT automatically export ✅
- No import allowed in module-info.java ✅

**Syntax traps:**
- requires java.sql ✅ (module name)
- requires com.myapp.api ❌ (that's a package)
- exports com.myapp.api ✅ (package in YOUR module)
- exports java.sql ❌ (not your package)

**Access table:**
| Package | Normal access | Reflective access |
|---|---|---|
| Not exported, not opened | ✗ | ✗ |
| Only exported | ✓ | ✗ |
| Only opened | ✗ | ✓ |
| Exported + opened | ✓ | ✓ |

**Three-layer module scenario:**
buslog_module has `requires transitive datarec_module`
- gui_module (requires buslog) → does NOT need requires datarec ✅
- persist_module (does NOT require buslog) → still needs its own requires datarec ✅

**JPMS benefits:**
- Provides extra level of encapsulation ✅
- Makes public classes invisible if package not exported ✅
- Uses module path (not classpath) ✅

**Service via module-info:**
- provides <interface> with <implementing class> ✅

---

# TOPIC 10 — Reflection

**What it is:**
- Allows a program to inspect itself at RUNTIME ✅
- Heavily used in frameworks (JUnit, ORMs, Spring) ✅
- Mainly useful at RUNTIME (not compile time) ✅

**FALSE:**
- "Use reflection whenever you can" ❌ (use sparingly)
- "Mainly useful at compile time" ❌

**Getting a Class object (all THREE are valid):**
- obj.getClass() ✅
- MyClass.class ✅
- Class.forName("fully.qualified.Name") ✅ (can throw ClassNotFoundException)

**Object class:**
- Every class implicitly extends Object ✅
- Inherited methods: toString(), equals(), hashCode(), getClass() ✅
- Object.getClass() = WRONG ❌ (instance method, not static)

**getField vs getDeclaredField:**
- getField("name") = public fields including INHERITED ✅
- getDeclaredField("name") = any visibility but OWN CLASS ONLY ✅
- Private field via getDeclaredField → need setAccessible(true) ✅
- getDeclaredField includes private fields from superclasses ❌

**invoke(null, args) = static method** ✅
**Compiler does NOT check reflection calls** ✅ (that's the whole point)
**String.class in getMethod = parameter type, NOT return type** ✅

---

# TOPIC 11 — Accessibility (protected / private / public)

| Modifier | Same class | Same package | Subclass | Anywhere |
|---|---|---|---|---|
| private | ✅ | ❌ | ❌ | ❌ |
| (none) package | ✅ | ✅ | ❌ | ❌ |
| protected | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ |

**Key trap:** protected = same package AND any subclass (even outside the package).

---

# TOPIC 12 — State Machines

**State** = a condition or situation in which an object exists ✅
**Transition** = a change of state triggered by an EVENT ✅
**Event** = an action that triggers a transition ✅
**Purpose** = model behaviour as a finite set of states and transitions ✅

**State vs Strategy pattern:**
- Same structure, different intent
- State: behaviour changes as INTERNAL state changes
- Strategy: swaps an algorithm chosen EXTERNALLY

---

# TOPIC 13 — Regular Expressions

**Key symbols:**
- \d = digit · \d{4} = exactly 4 digits
- \s = whitespace · [A-Z] = uppercase · [a-z] = lowercase
- ^ = start · $ = end · \. = literal dot
- {n} = exactly n times · + = 1 or more · * = 0 or more

**Dutch postcode "1234 SD":**
- Correct: \d{4}\s[A-Z]{2} ✅

**Methods:**
- matches() = WHOLE string must match ✅
- find() = match anywhere in string ✅
- Pattern.compile(regex).matcher(input).matches() = correct usage ✅

---

# TOPIC 14 — Logging (JUL)

**Level ladder high → low:**
OFF → SEVERE → WARNING → INFO → CONFIG → FINE → FINER → FINEST → ALL

**Default = INFO** → only INFO, WARNING, SEVERE printed

**Rule:** selected level AND everything MORE SEVERE (above) gets logged
- Set FINE → prints FINE, CONFIG, INFO, WARNING, SEVERE

**TRUE statements:**
- Logging = logger + one or more handlers ✅
- By default, logging.properties from JVM is applied ✅
- ConsoleHandler writes to System.ERR (not System.out) ✅
- A logger can have multiple handlers ✅
- Double filter: message must pass BOTH logger level AND handler level ✅

**FALSE statements:**
- Default only WARNING and SEVERE printed ❌ (INFO is also default)
- ConsoleHandler writes to System.out ❌ (it's System.ERR)
- Config must use a properties file ❌ (can be programmatic too)

**Logger level WARNING, Handler level INFO:**
- SEVERE → passes both → PRINTED ✅
- WARNING → passes both → PRINTED ✅
- INFO → FAILS logger (INFO < WARNING) → NOT printed ❌
- FINE → FAILS logger → NOT printed ❌

---

# TOPIC 15 — java.time & I18N

**The four types:**
| Type | Stores |
|---|---|
| LocalDateTime | date + time, NO zone, NO offset |
| OffsetDateTime | date + time + offset (no region name) |
| ZonedDateTime | date + time + offset + region (full) |
| Instant | UTC only — always UTC |

**TRUE:**
- Instant is a specific moment in UTC ✅
- A Locale determines date/time and number formatting ✅
- Instant can be converted to ZonedDateTime with a zone ✅
- Offset +02:00 means 2 hours ahead of UTC ✅

**FALSE:**
- LocalTime is in your default timezone ❌ (no timezone at all)
- Instant can represent any local timezone ❌ (always UTC)
- Instant stores seconds since 1900 ❌ (since 1970-01-01, Unix epoch)

**I18N:**
- Strings in .properties files (messages.properties, messages_nl_NL.properties)
- Fallback chain: nl_NL → nl → base
- MessageFormat.format("Hello {0}!", name) fills {0}

---

# TOPIC 16 — JDBC & Database

**Architecture:**
My Code → JDBC API (java.sql, vendor-neutral) → Driver (vendor-specific) → Database

**5 interfaces:** Connection, Statement/PreparedStatement, ResultSet, ResultSetMetaData, DriverManager

**ResultSet:**
- Cursor starts BEFORE first row ✅
- Columns are 1-BASED ✅
- rs.getString(0) → SQLException ✅
- getInt("bad column") → SQLException ✅
- ResultSet implements AutoCloseable ✅
- Closing Connection closes everything ✅

**PreparedStatement vs Statement:**
- PreparedStatement = fixed SQL with ? params, compiled once, immune to SQL injection ✅
- Statement is preferable ❌ (PreparedStatement is always better)
- returning * in INSERT gives back generated id ✅
- executeQuery() → ResultSet · executeUpdate() → int ✅

**Good practice (ALL TRUE):**
- Never use postgres superuser in app ✅
- Use limited-rights DB user ✅
- Never hardcode credentials in source code ✅
- Never write SQL in UI or Business Logic layer ✅
- Never let SQLException leak to the UI layer ✅

**Database facts:**
- USER in Postgres = ROLE with login password ✅
- System catalog contains table/view definitions ✅
- A schema can have many databases ❌ (WRONG — database has many schemas)
- A view hides complex queries ✅
- A view does NOT contain data ❌ (it's virtual)
- A view is NOT always updatable ❌

**AutoCloseable:**
- close() called even if exception occurs ✅
- Multiple resources close in REVERSE order ✅
- Without try-with-resources, AutoCloseable does NOT auto-close ✅
- ResultSetMetaData is NOT AutoCloseable ❌

---

# TOPIC 17 — REST & HTTP

**Safe & Idempotent:**
| Method | Safe | Idempotent |
|---|---|---|
| GET | ✅ | ✅ |
| POST | ❌ | ❌ |
| PUT | ❌ | ✅ |
| PATCH | ❌ | ❌ |
| DELETE | ❌ | ✅ |

**NOT TRUE statements (all are false):**
- POST is idempotent ❌
- DELETE is not idempotent ❌ (it IS)
- PATCH is always idempotent like PUT ❌
- PUT is not idempotent ❌ (it IS)
- GET can modify data ❌
- HTTP is stateful ❌ (stateless)
- All methods use same status code ❌

**Status codes:**
- POST → 201 · GET → 200 · PUT/PATCH → 200 or 204 · DELETE → 204
- 401 = not authenticated · 403 = authenticated but forbidden
- 404 = not found · 500 = server error

**HTTP is STATELESS** — every request carries all info needed. No server-side session.

---

# TOPIC 18 — Threads (Concurrency)

**TRUE:**
- start() starts a thread in a NEW execution path ✅
- sleep(ms) pauses the current thread ✅
- synchronized = only ONE thread at a time executes that block ✅
- volatile = variable always read/written from MAIN MEMORY ✅
- notifyAll() wakes ALL threads waiting on the monitor ✅

**FALSE:**
- run() starts a thread ❌ (just calls the method on the CURRENT thread)
- notify() wakes ALL threads ❌ (wakes only ONE)

---

# THE SHAPE DIAGRAM — Supertypes of Circle

**Supertypes of Circle (select ALL of these):**
- Shape ✅ (Circle extends Shape)
- Object ✅ (Shape extends Object)
- Serializable ✅ (Shape implements it, Circle inherits)
- Surface ✅ (Circle implements it directly)
- ArcLike ✅ (Circle implements it directly)

**NOT supertypes (never select):**
- Disk ❌ (subtype — below Circle)
- Triangle ❌ (sibling)
- Line ❌ (sibling)
- Arc ❌ (unrelated branch)

---

# WEEKEND PLAN

**Today (what's left):**
Read through this document once. Just read — no pressure. Focus on the topics you feel weakest on. The tables are designed to be scannable.

**Sunday — two sessions:**

Morning: Go through each topic and for each one, cover the right column and say the answers out loud. Takes about 90 minutes.

Afternoon: Take the mock exam widget. Score yourself. Every miss → go back to that topic in this document. Fix it. Do the exam again.

Stop by 4pm Sunday. Rest. Sleep properly.

**Monday morning:**
- Don't study. Eat. Arrive calm.
- First pass: answer everything you know instantly.
- Second pass: return to uncertain questions, eliminate obvious wrongs first.
- On multi-select: only tick what you're sure about.
- Never leave a single-answer question blank.

You've done the work. Monday is just showing what you know.
