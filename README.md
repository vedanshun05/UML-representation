![[Pasted image 20260408103513.png]]
Inheritance       →  solid line + hollow triangle (you have it)
Implements        →  dashed line + hollow triangle (you have it)  
Association/HAS-A →  solid line + arrow (you have it)
Multiplicity      →  numbers on the ends of lines (1, *, 0..1)

- Does this class belong to a family? → Inheritance
- Does this class follow a contract? → Implements

- What data does this class hold as fields? → HAS-A association
- What other object does this class need to do its job? → Association


1. Enums
	Connect them to the class that uses them with a plain association arrow. Every system has 4-5 enums minimum.
2. How to represent an Abstract class
	Use when classes share common fields AND some common behavior, not just a contract.
3. Access modifiers on attributes and methods
	Every attribute should be `-` private. Every method should be `+` public. Inherited fields should be `#` protected.
4. How to show a Junction/Bridge class properly
	Patient ——1————*——> Appointment <——*————1—— Doctor
	Appointment sits in the middle with its own attributes (date, time, status) and two associations pointing outward. Both arrows have multiplicities.


How to structure the diagram spatially
[Actors/Users]          ← top layer
      ↓
[Core transaction]      ← middle layer (Order, Appointment, Booking)
      ↓
[Supporting objects]    ← bottom layer (Payment, Notification, Review)

[Enums]                 ← right side, connected to whoever uses them
[Interfaces/Abstract]   ← left side or top of their hierarchy


□ All actors/users identified
□ Core transaction object present
□ Supporting objects (Payment, Notification) present
□ All enums drawn and connected
□ Abstract classes marked «abstract»
□ Interfaces marked «interface»
□ Every attribute has access modifier and type
□ Every method has access modifier and return type
□ Every relationship line has multiplicity at both ends
□ Every association line has a verb label
□ Many-to-many resolved into junction class
□ At least one design pattern identified and noted
□ Spatial layout follows top-to-bottom flow
□ Notes added for non-obvious business rules


|Pattern|Core Idea|
|---|---|
|Singleton|One instance|
|Immutable|No state change|
|Builder|Step-by-step creation|
|Factory|Hide object creation|
|Prototype|Copy objects|
|Proxy|Control access|
|Flyweight|Share memory|
|Decorator|Add behavior dynamically|
|Adapter|Convert interface|
|Strategy|Swap algorithms|

Problem → Ask:
1. Is it about object creation?
   ├── Only one instance → Singleton
   ├── Too many params → Builder
   ├── Based on type → Factory
   └── Copy object → Prototype

2. Is it about structure?
   ├── Incompatible interface → Adapter
   ├── Add features → Decorator
   ├── Control access → Proxy
   └── Reduce memory → Flyweight

3. Is it about behavior?
   └── Multiple algorithms → Strategy


![[Pasted image 20260408101329.png]]
# Creational
Singleton - SRP OCP DIP
- Eager init: creates even if unused
- Lazy unsafe: two threads may create two objects
- Synchronized: correct but slow on every call
- DCL without volatile: half-constructed object visible to other threads
- Holder idiom: JVM classloading guarantees thread-safety for free
- Enum: safest (reflection + serialization proof) but no lazy init

Builder - SRP OCP ISP
- Telescoping constructors: unreadable, error-prone parameter order
- Setters on the object: breaks immutability
- Builder enforces required fields early (in Builder constructor or build())
- Fluent API: each setter returns 'this', enabling chaining
- Defensive copy of collections in build() preserves immutability
- Separates construction from representation → SRP

Prototype - OCP DIP
- new() requires knowing the concrete class; clone() doesn't
- If constructor is heavy, copying a pre-built exemplar is faster
- Registry lets plugins self-register at runtime without touching client code
- Shallow copy: safe only for immutable fields; mutable fields need deep copy
- Deep copy: copy all nested mutable objects to ensure independence
- copy() is preferred over Java's Cloneable (which is fragile)

Factory - SRP OCP DIP
- Simple Factory: just centralizes 'new'; no per-instance policy variation
- Factory Method: algorithm is fixed, creation varies per subclass
- New policy = new subclass, not new switch branch → OCP
- Creator depends on product interface, not concrete product → DIP
- Each concrete creator owns one creation policy → SRP
- Client selects spawner at composition time, not at call time

# Structural
Proxy - SRP OCP LSP DIP
- Eager load: wastes resources if real object never used
- Client lazy-load: violates SRP (client manages lifecycle it shouldn't)
- Proxy centralizes lazy init in one place → SRP, OCP
- Virtual Proxy: defers expensive creation until first use
- Remote Proxy: hides network call behind local interface
- Protection Proxy: checks permissions before forwarding
- Smart Reference: adds ref-counting, locking, logging around access

Flyweight - SRP OCP
- Without flyweight: intrinsic data duplicated per instance = memory bloat
- With flyweight: N instances share 1 immutable type object → huge savings
- Factory ensures same key → same object (no accidental duplication)
- Flyweights must be immutable; mutable shared state changes ALL instances
- Extrinsic state stays outside flyweight (position, velocity, owner)
- Pairs well with Object Pool (pool reduces alloc churn; flyweight reduces size)

Adapter - SRP OCP LSP ISP DIP
- Changing legacy service: breaks other callers, violates OCP
- if/else in client: pollutes high-level module with low-level details → SRP, DIP
- God mapper: still a switchyard; OCP weak, hard to test
- Adapter: SRP (ranking ranks, adapter adapts), OCP (new provider = new class)
- Object adapter (composition) preferred in Java over class adapter (inheritance)
- Unit/field translation (paise→rupees, 0-100 score→0-5 rating) lives in adapter

Decorator - SRP OCP LSP ISP DIP
- Subclass-per-combination: 2^N classes for N optional behaviors
- One big class with booleans: SRP/OCP violation, growing branch thickets
- Decorator wraps same interface → LSP preserved, client is unaware
- Order matters: metrics outermost measures everything; cache before retry caches failures
- Each decorator has one responsibility: retry OR cache OR auth → SRP
- Add/remove behavior by recomposing at runtime, no new classes → OCP

# Behavioral
Strategy - SRP OCP LSP ISP DIP
- Inheritance for each combination: combinatorial explosion, SRP violation
- Giant if/else in sort(): SRP/OCP violation, hard to test each branch
- Strategy: one class per algorithm → each owns one responsibility (SRP)
- New algorithm = new class, context untouched → OCP
- Client depends on SortStrategy interface, not concrete class → DIP
- Runtime hot-swap enables A/B tests, tenant policies, data-shape optimization