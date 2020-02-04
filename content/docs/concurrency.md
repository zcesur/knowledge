## Thread safety
Thread safety is a computer programming concept applicable to multi-threaded code. Thread-safe code only manipulates shared data structures in a manner that ensures that all threads behave properly and fulfill their design specifications without unintended interaction. There are various strategies for making thread-safe data structures.
A program may execute code in several threads simultaneously in a shared address space where each of those threads has access to virtually all of the memory of every other thread. Thread safety is a property that allows code to run in multithreaded environments by re-establishing some of the correspondences between the actual flow of control and the text of the program, by means of synchronization.

### Implementation approaches

The first class of approaches focuses on avoiding shared state, and includes:

***Re-entrancy***
Writing code in such a way that it can be partially executed by a thread, reexecuted by the same thread or simultaneously executed by another thread and still correctly complete the original execution. This requires the saving of state information in variables local to each execution, usually on a stack, instead of in static or global variables or other non-local state. All non-local state must be accessed through atomic operations and the data-structures must also be reentrant.

***Thread-local storage***
Variables are localized so that each thread has its own private copy. These variables retain their values across subroutine and other code boundaries, and are thread-safe since they are local to each thread, even though the code which accesses them might be executed simultaneously by another thread.

***Immutable objects***
The state of an object cannot be changed after construction. This implies both that only read-only data is shared and that inherent thread safety is attained. Mutable (non-const) operations can then be implemented in such a way that they create new objects instead of modifying existing ones. This approach is characteristic of functional programming and is also used by the string implementations in Java, C# and Python.

The second class of approaches are synchronization-related, and are used in situations where shared state cannot be avoided:

**Mutual exclusion**
Access to shared data is serialized using mechanisms that ensure only one thread reads or writes to the shared data at any time. Incorporation of mutual exclusion needs to be well thought out, since improper usage can lead to side-effects like deadlocks, livelocks and resource starvation.

***Atomic operations***
Shared data is accessed by using atomic operations which cannot be interrupted by other threads. This usually requires using special machine language instructions, which might be available in a runtime library. Since the operations are atomic, the shared data is always kept in a valid state, no matter how other threads access it. Atomic operations form the basis of many thread locking mechanisms, and are used to implement mutual exclusion primitives.

* [concurrency glossary](https://slikts.github.io/concurrency-glossary/)
