

# 2. Creating Objects


### Item 1. Static Factory method
1. [ ] **Static Factory** is not the same as Factory Method Pattern. :thought_balloon: ... [stack](https://stackoverflow.com/a/9914562)
1. **Static Factory** advantages: have names, do not require to create a new object, can return a subtype

### Item 2. Builder Pattern
1. *Static Factory* method and *Telescoping Constructors* do not scale well to large numbers of params.
1. *JavaBeans Pattern* may be in an inconsistent state.
1. **Builder Pattern** implementation details: static member class, keep all default values, enclosing class is immutable.
1. **Builder Pattern** should be considered if there are enough params, 4 or more.

### Item 3. Singleton
1. Java <1.5 - static final member and static factory method. The latter is better, since you can change your mind later.
1. [ ] Java >=1.5 - the best way to implement Singleton using `Enum`. :thought_balloon: check lazy initialization and holder

### Item 4,5,6. Best Practice
1. Enforce noninstantiability with a *Private Constructor*. Useful for Utility Classes.
1. Avoid creating unnecessary classes. E.g. *Flyghtweitght Pattern*, immutable objects are reusable, prefer primitives to boxed primitives etc
[ ]. Eliminate obsolete object reference, but this is rather an exceptional case. 
1. Avoid _Finalizers_. They are unpredictable. Finalizer thread is running at a lower priority. If needed, consider custom explicit termination method and try-finally construct. 


# 3. Object Methods


1. `equals()` recipe. Use `==` and `instanceof` operators, cast the argument to the correct type, check significant fields only. 
:warning: *remember to use `Float.compare()` and `Double.compare` because of Nan and 0.0*
1. `hashCode()`. You must override hashCode in every class that overrides equals. Or you can't use hash-based collections.
1. equal objects must have equal hash codes.
1. [ ] HashMap has an optimization that cashes the hashCode / p46
1. `toString()`. Provide programmatic access to all fields returned by toString.
1. `clone()` requires `implements Cloneable` and creates an object without calling a constructor. :warning: remember to copy all mutable objects.
1. *Copy Constructor* and *Copy Factory* better than `clone()` method.
1. `equals()` & `compareTo()` issue. There is no way to extend an instantiable class (abstract doesn't have this problem) and add a value component while preserving the contract. There is a workaround - *Composition* + *view method*


# 4. Classes and Interfaces


### Item 13,14,15. Encapsulation
1. Information hiding - allowing modules to be developed, tested, optimized, used, understood in isolation.
1. The need for protected members is rare. A protected member is part of the exported API and must be supported forever.
1. Minimize mutability. Make *defensive copies* in constructors and accessors. Make every field final unless there is a compelling reason against.

### Item 16,17. Inheritance vs Composition
1. **Inheritance** violates encapsulation. It is appropriate only in case of *is-a*.
1. [ ] Composition - *wrapper+forwarding* class / p85
1. [ ] There are two ways to prohibit subclassing - final classes and private constructor. The latter is the most flexible.

> :point_right: prefer composition and use inheritance only in case of *is-a*. Consider *wrapper+forwarding* pattern.

### Item 18,19. Interfaces or Abstract classes
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
