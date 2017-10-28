
## 4 Classes and Interfaces

### 16, 17 Inheritance vs Composition
1. **Inheritance** violates encapsulation. It is appropriate only in case of *is-a*.
1. [ ] Composition - *wrapper+forwarding* class / p85
1. 2 ways to prohibit subclassing - final and private constructor

### Item 18, 19. Interfaces or Abstract classes
1. **interfaces** are ideal for *mixins* e.g. singers are also songwriters. 
:thought_balloon: - *hello SOLID & interface segregation principle :)*
1. use **interfaces** only to define type
1. it is far easier to evolve an **abstract class** than an interface e.g. to add a new method with a default implementation in a subsequent release
:thought_balloon: - *haha, Bloch finally implemented it in 8th version :)*
1. combine **interfaces** and **abstract classes** by providing an abstract *skeletal implementation* (e.g. `AbstractSet`, `AbstractList`)
:thought_balloon: - *btw skeletal could be concrete*

### Item 21. Function Objects
1. An example of the *Strategy Pattern*

### Item 22. Nested Classes
1. [ ] helper e.g. builder / TODO p106
1. [ ] if independent then static / TODO p106
1. **nonstatic member class** - returns *collection views* and defines an *Adapter pattern* (e.g. `Map.values()`, `Map.entrySet()`, `List.iterator`)
1. **static member class** - represents components of the object that do not require access ot an enclosing instance (e.g. `Map.Entry`)
