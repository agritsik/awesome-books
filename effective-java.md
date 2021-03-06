

# 2. Creating Objects


### Item 1. Static Factory method
1. **Static Factory** is not the same as Factory Method Pattern. 
1. **Static Factory** advantages: they have names, do not require to create a new object (flyweight), can return a subtype

> :point_right:   *"delegate object creation from constructor to static method" VS "lets a class defer instantiation to subclasses" [stack](https://stackoverflow.com/a/9914562)* 

> :point_right: Factory vs Creation method vs Static creation (or factory) method  vs Simple factory vs Factory Method  vs Abstract Factory [refactoring.guru](https://refactoring.guru/design-patterns/factory-comparison)

### Item 2. Builder Pattern
1. *Static Factory* method and *Telescoping Constructors* do not scale well to large numbers of params.
1. *JavaBeans Pattern* may be in an inconsistent state.
1. **Builder Pattern** should be considered if there are enough params, 4 or more.
1. **Builder Pattern** implementation details: static member class, keep all default values, enclosing class is immutable.

### Item 3. Singleton
1. Java <1.5 - static final member and static factory method. The latter is better, since you can change your mind later.
1. Java >=1.5 - the best way to implement Singleton using `Enum`. 

> :point_right:  there are other well known solutions such as 
[double check syncronization](https://en.wikipedia.org/wiki/Double-checked_locking#Usage_in_Java) and
[on-demand holder](https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom). 
But Enum-based approach looks like the simplest one. There is a good thread on 
[stackoverflow](https://stackoverflow.com/questions/11165852/java-singleton-and-synchronization).

### Item 4,5,6. Best Practice
1. Enforce noninstantiability with a *Private Constructor*. Useful for Utility Classes.
1. Avoid creating unnecessary classes. E.g. *Flyghtweitght Pattern*, immutable objects are reusable, prefer primitives to boxed primitives etc
1. Eliminate obsolete object reference, but this is rather an exceptional case. :thought_balloon: *confusing exmaple with arrays...*
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


# Exceptions


- exceptions are, as their name implies, to be used only for exceptional conditions; they should never be used for ordinary control flow. 
- A well-designed API must not force its clients to use exceptions for ordinary control flow (e.g. `Iterator`)
- The Java programming language provides __three kinds of throwables__: checked exceptions, runtime exceptions, and errors:
    - use _checked exceptions_ for conditions from which the caller can reasonably be expected to recover. 
    - use _runtime exceptions_ to indicate programming errors. The great majority of runtime exceptions indicate precondition violations. A precondition violation is simply a failure by the client of an API.
    - while the Java Language Specification does not require it, there is a strong convention that _errors_ are reserved for use by the JVM to indicate resource deficiencies, invariant failures, or other conditions that make it impossible to continue execution.
- To summarize, use checked exceptions for recoverable conditions and runtime exceptions for programming errors. Of course


# Generics


### Item23: Don't use raw types in new code
- you lose type safety if you use a raw type like List, but not if you use a parameterized type `List<Object>`
- unbounded wildcard types `<?>`

### Item24: Eliminate unchecked warnings
- always use the `SuppressWarnings` annotation on the smallest scope possible

### Item25: Prefer lists to arrays
- Arrays are covariant & reified (know element types at run-time)
- Generics are invariant & implemented by erasure (know element types at compile-time)

### Item26: Favor generic types
- the first solution - keep data in E[] and cast creation. Is used more commonly.
- the second solution - keep data in Object[] and cast elements retrieval (e.g. `ArrayList` and `return (E) elementData[index];`).
- bounded type parameter `class DelayQueue<E extends Delayed> implements BlockingQueue<E>;`

### Item27: Favor generic methods
- static utility methods are particularly good candidates for generification
- write a _generic static factory method_ corresponding to each constructor that you want to use.
- ? generic singleton factory
- _recursive type bound_ when a type parameter is bounded by some expression involving that type parameter itself
`public static <T extends Comparable<T>> T max(List<T> list) {...}`

### Item28: Use bounded wildcards to increase API flexibility
- ... parameterized types are invariant. In other words, for any two distinct types Type1 and Type2, List<Type1> is neither a subtype nor a supertype of List<Type2>
- PECS stands for producer-extends, consumer-super.
- do not use wildcard types as return types
- if a type parameter appears only once in a method declaration, replace it with a wildcard.

### Item29: Consider typesafe heterogeneous containers
- ... In all of these uses, it is the container that is parameterized. This limits you to a fixed number of type parameters per container.


# Concurrency


### Item 66, 67 Synchronization 
1. **Synchronization** guarantees that no method will observe the object in inconsistent state & ensures that each thread sees the effects of all previous modificaitons. :thought_balloon: *is it happens-before?*
1. **Synchronization** has no effect unless both read & write operations are synchronized
1. In the absence of Synchronization, VM can transform code (aka hoisting). :thought_balloon: a good example with the program that never terminates
1. Never call an alien method from within a sync region. Try to limit the amount of work that you do from within a sync region.
1. [ ] reentrant lock / p268

### 69,70 API
1. Utilities in `java.util.concurrent` fall into 3 categories: Executor Framework, Collections and Synchronizers
1. The most popular synchonizers are `CountDownLatch` and `Semaphore`. Less popular are `CyclicBarrier` and `Exchenger`
1. The synchronized method is an implementation rather than API

### 71 Lazy Initialization
1. For Static Fields use *initialize-on-demand holder* idiom
1. For Instance Fields use *double-check* idiom




