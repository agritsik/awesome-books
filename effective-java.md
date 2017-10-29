

# 2. Creating Objects

### 1 Static Factory method
1. [ ] **Static Factory** is not the same as Factory Method Pattern. :thought_balloon: ... [stack](https://stackoverflow.com/a/9914562)
1. **Static Factory** advantages: have names, do not require to create a new object, can return a subtype

### 2 Builder Pattern
1. Static Factory method and Telescoping Constructors do not scale well to large numbers of params
1. JavaBeans Pattern may be in an inconsistent state.
1. **Builder Pattern** implementation details: static member class, keep all default values, enclosing class is immutable.
1. **Builder Pattern** should be considered if there are enough params, 4 or more.

### 3 Singleton
1. Java <1.5 - static final member and static factory method. The latter is better, since you can change your mind later.
1. [ ] Java >=1.5 - the best way to implement Singleton using `Enum`. :thought_balloon: check lazy initialization and holder

### 4,5,6 Best Practice
1. Enforce noninstantiability with a private constructor. Useful for Utility Classes.
1. Avoid creating unnecessary classes - e.g. immutable objects are reusable, prefer primitives to boxed primitives.
1. Eliminate obsolete object reference, but this is rather an exceptional case.
1. Finalizers are unpredictable, finalizer thread is running at a lower priority. If needed, consider custom explicit termination method and try-finally construct. 


# 4. Classes and Interfaces

### 16, 17 Inheritance vs Composition
1. **Inheritance** violates encapsulation. It is appropriate only in case of *is-a*.
1. [ ] Composition - *wrapper+forwarding* class / p85
1. 2 ways to prohibit subclassing - final and private constructor
> :point_right: prefer composition and use inheritance only in case of *is-a*. Consider *wrapper+forwarding* pattern.

### Item 18, 19. Interfaces or Abstract classes
1. **interfaces** are ideal for *mixins* e.g. singers are also songwriters. :thought_balloon: *hey, [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))!*
1. use **interfaces** only to define type
1. it is far easier to evolve an **abstract class** than an interface e.g. to add a new method with a default implementation in a subsequent release :thought_balloon: *not relevant with regards to java8 default methods?*
1. combine **interfaces** and **abstract classes** by providing an abstract *skeletal implementation* (e.g. `AbstractSet`, `AbstractList`) :thought_balloon: *it gives you the best of both worlds in that [stack](https://stackoverflow.com/a/13437007)* 
### Item 21. Function Objects
1. An example of the *Strategy Pattern*

### Item 22. Nested Classes
1. [ ] helper e.g. builder / TODO p106
1. [ ] if independent then static / TODO p106
1. **nonstatic member class** - returns *collection views* and defines an *Adapter pattern* (e.g. `Map.values()`, `Map.entrySet()`, `List.iterator`)
1. **static member class** - represents components of the object that do not require access ot an enclosing instance (e.g. `Map.Entry`)
