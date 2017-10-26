
...

### Inheritance vs Composition
- **Inheritance** violates encapsulation. It is appropriate only in case of *is-a*.
- **Composition** - wrapper+forwarding class / TODO p85
- 2 ways to prohibit subclassing - final and private constructor

### Item 18, 19. Interfaces or Abstract classes
- **interfaces** are ideal for mixins e.g. singers are also songwriters
- use **interfaces** only to define type
- it is far easier to evolve an **abstract class** than an interface e.g. to add a new method with a default implementation in a subsequent release
- combine **interfaces** and **abstract classes** by providing an abstract *skeletal implementation* (`AbstractSet`, `AbstractList`)

### Item 21. Function Objects
- An example of the *Strategy Pattern*

### Item 22. Nested Classes
- helper e.g. builder / TODO p106
- if independent then static / TODO p106
- **nonstatic member class** - returns *collection views* and defines an *Adapter pattern* (`Map.values()`, `Map.entrySet()`, `List.iterator`)
- **static member class** - represents components of the object that do not require access ot an enclosing instance (`Map.Entry`)
