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
- **Associations** - In real life, there are lots of many-to-many associations, and a great number are naturally
bidirectional. It is important to constrain relationships as much as possible. When application requirements do not call for
traversal in both directions, adding a traversal direction reduces interdependence and simplifies
the design.
