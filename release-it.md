### Notes
- team assignments are the first draft of the architecture. *p2*
- an architect who doesn’t bother to listen to the coders on the team doesn’t bother listening to the users either. *p5*
- once you accept that failures will happen, you have the ability to design your system’s reaction to specific failures. *p26*
- like building crumple zones into cars to absorb impacts and keep passengers safe, you can decide what features of the system are indispensable and build in failure modes that keep cracks away from those features. *p27*
- the more tightly coupled the architecture, the greater the chance this coding error can propagate. Conversely, the less coupled architectures act as shock absorbers, diminishing the effects of this error instead of amplifying them. *p28*
- one way to prepare for every possible failure is to look at every external call, every I/O, every use of resources, and every expected outcome and ask, “What are all the ways this can go wrong?” *p29*
- problems on the network, or with other servers, tend to last for a while. Thus, fast retries are very likely to fail again. *p93*
- This is sort of the software equivalent of the cook’s mise en place—gathering all the ingredients it will need to service the request before it begins. If any of the resources are not available, it can fail immediately, rather than getting partway through the work. *p108*
- Premature optimization is the root of all evil. *p177*
