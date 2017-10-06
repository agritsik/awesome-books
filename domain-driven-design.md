### Chapter Four. Isolating the Domain
- When the domain-related code is diffused through such a large amount of other code, it
becomes extremely difficult to see and to reason about. Superficial changes to the UI
can actually change business logic. To change a business rule may require meticulous
tracing of UI code, database code, or other program elements... Automated testing is awkward. _p73_
- Concentrate all the code related
to the domain model in one layer and isolate it from the user interface, application, and
infrastructure code. _p74_
- domain-driven design requires only one particular layer to exist. _p78_
- Isolating the domain implementation is a prerequisite for domain-driven
design. _p78_

### Chapter Five. A Model Expressed in Software
**Associations**. In real life, there are lots of many-to-many associations, and a great number are naturally
bidirectional. It is important to constrain relationships as much as possible. When application requirements do not call for
traversal in both directions, adding a traversal direction reduces interdependence and simplifies
the design. 

> _... I guess Evans simply suggests that you should add a traversal direction only when you need it and will use it in the code just after, as opposed to prematurely adding a traversal direction "in case we need it later"._  [stackoverflow](https://stackoverflow.com/a/9806142)

**Entities**. Some objects are not defined primarily by their attributes. They represent a thread of identity that runs through time and often across distinct representations _p92_

Each ENTITY must have an operational way of establishing its identity with another object. An identifying attribute must be guaranteed to be unique within the system however that system is defined—even if distributed, even when objects are archived. _p94_

**Value Object**. An object that represents a descriptive aspect of the domain with no conceptual identity. Treat the VALUE OBJECT as immutable. Don't give it any identity. _p98_

**Services**. When a significant process or transformation in the domain is not a natural responsibility of an ENTITY or VALUE OBJECT, add an operation to the model as a standalone interface declared as a SERVICE. Define the interface in terms of the language of the model and make sure the operation name is part of the UBIQUITOUS LANGUAGE. Make the SERVICE stateless. _p104_

### Chapter Six. The Life Cycle of a Domain Object
- AGGREGATES defines clear ownership and boundaries, this pattern is crucial to maintaining integrity in all phases of the life cycle. _p116_
- FACTORIES - the beginning of the life cycle that creates and reconstitutes complex objects and AGGREGATES, keeping their internal structure encapsulated. _p116_
- REPOSITORIES address the middle and end of the life cycle, providing the means of finding and retrieving persistent objects while encapsulating the immense infrastructure involved. _p116_
- The FACTORY makes new objects; the REPOSITORY finds old objects. _p142_
- AGGREGATES mark off the scope within which invariants have to be maintained at every stage of the life cycle. The following patterns, FACTORIES and REPOSITORIES , operate on AGGREGATES, encapsulating the complexity of specific life cycle transitions. _p125_

**AGGREGATE** is a cluster of associated objects that we treat as a unit for the purpose of data changes. Each AGGREGATE has a root and a boundary. The boundary defines what is inside the AGGREGATE . The root is a single, specific ENTITY contained in the AGGREGATE. The root is the only member of the AGGREGATE that outside objects are allowed to hold references to. _p119_

**FACTORY** Shift the responsibility for creating instances of complex objects and AGGREGATES to a separate object, which is still part of the domain design. Provide an interface that encapsulates all complex assembly and that does not require the client to reference the concrete classes of the objects being instantiated. Create entire AGGREGATES as a piece, enforcing their invariants. _p129_

**REPOSITORY** For each type of object that needs global access, create an object that can provide the illusion of an in-memory collection of all objects of that type. Provide REPOSITORIES only for AGGREGATE roots that actually need direct access. Keep the client focused on the model, delegating all object storage and access to the REPOSITORIES. _p138_

### Chapter Seven. Using the Language: An Extended Example
- Also, the concept of a **Customer** is not specific to Cargo. In a large system, the Customer may have roles to play with many objects. Best to keep it free of such specific responsibilities. If we need the ability to find Cargoes by Customer, this can be done through a database query
- Repositories are prohibited from interior of Aggregates
- By adding FACTORY METHODS to the base class (Handling Event) for each type, instance creation is abstracted, freeing the client from knowledge of the implementation. The FACTORY is responsible for knowing what class was to be instantiated and how
it should be initialized.

