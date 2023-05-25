# Linux Admin 1

## Chapter 7 (Deadlocks)

### system model

- System has resources like : CPU cycles, memory locations, I/O devices, network and mutex locks.

- A set of threads is in a deadlocked state when every thread in the set is waiting for an event that can be caused only by another thread in the set. The events with which we are mainly concerned here are resource acquisition and release.

- A thread must request a resource before using it and must release the resource after using it.

- Under the normal mode of operation, a thread may utilize a resource in only the following sequence:
  - **Request** : The thread requests the resource. If the request cannot be granted immediately, then the requesting thread must wait until it can acquire the resource.
  - **Use** : The thread can operate on the resource (for example, if the resource is a mutex lock, the thread can access its critical section).
  - **Release** : The thread releases the resource.

- **A system table** : records whether each resource is free or allocated. It also records the thread to which it is allocated.

- **livelock** : similar to deadlock is both prevent proceeding, but in different reasons. Livelock occurs when a thread continuously attempts an action that fails.
  - Livelock typically occurs when threads retry failing operations at the same time.  It thus can generally be avoided by having each thread retry the failing operation at random times. (Like exponential backoff in ethernet so that avoid collision)

### deadlock characterization

- A deadlock situation can arise if the following four conditions hold simultaneously in a system.
  - **Mutual exclusion** : At least one resource must be held in a nonsharable mode; that is, only one thread at a time can use the resource. If another thread requests that resource, the requesting thread must be delayed until the resource has been released.
  - **Hold and wait** : A thread must be holding at least one resource and waiting to acquire additional resources that are currently being held by other threads.
  - **No preemption** : Resources cannot be preempted; that is, a resource can be released only voluntarily by the thread holding it, after that thread has completed its task.
  - **Circular wait** : A set {T~0~, T~1~, ..., T~n~} of waiting threads must exist such that T~0~ is waiting for a resource held by T~1~, T~1~ is waiting for a resource held by T~2~, ..., T~n−1~ is waiting for a resource held by T~n~, and T~n~ is waiting for a resource held by T~0~.

- The circular-wait condition implies the hold-and-wait condition, so the four conditions are not completely independent.

#### Resource-Allocation Graph

- This graph consists of a set of vertices V which represents threads T~i~ and resources R~i~ and a set of edges E which represents the relationship between threads and resources.
  - **Request edge** : A directed edge T~i~ → R~j~
  - **Assignment edge** : A directed edge R~i~ → T~j~
- number of instances in the resource are represented as dots.

- **Example**:-

![Resource alloc graph example](./imagesForOS/img1.JPG "Resource alloc graph example")

- The sets T(Threads), R(Resources), and E(Edges)
  - T = {T~1~, T~2~, T~3~}
  - R = {R~1~, R~2~, R~3~, R~4~}
  - E = {T~1~ → R~1~, T~2~ → R~3~, R~1~ → T~2~, R~2~ → T~2~, R~2~ → T~1~, R~3~ → T~3~}

- If the graph contains *no cycles*, then *no* thread in the system is deadlocked.
- If the graph *does contain a cycle*, then a deadlock *may exist*.
- If each resource type has exactly *one instance*, then a cycle implies that *a deadlock has occurred*. (In this case, a cycle in the graph is both a necessary and a sufficient condition for the existence of deadlock.)
- If each resource type has *several instances*, then a cycle *does not necessarily imply that a deadlock has occurred*. (In this case, a cycle in the graph is a necessary but not a sufficient condition for the existence of deadlock.)

- ==Methods for Handling Deadlocks==
  - We can use a protocol to prevent or avoid deadlocks, ensuring that the system will never enter a deadlocked state.
    - **Deadlock prevention** : provides a set of methods to ensure that at least one of the necessary conditions cannot hold.
    - **Deadlock avoidance** : requires that the operating system be given additional information in advance concerning which resources a thread will request and use during its lifetime. With this additional knowledge, the operating system can decide for each request whether or not the thread should wait.
  - We can allow the system to enter a deadlocked state, detect it, and recover.
  - We can ignore the problem altogether and pretend that deadlocks never occur in the system.

### deadlock prevention

- By ensuring that at least one of these conditions cannot hold, we can *prevent* the occurrence of a deadlock.
- we will examine each condition seperately
  - **Mutual exclusion :-**
    - Sharable resources like (Read-Only files) do not require mutually exclusive access and thus cannot be involved in a deadlock
    - we cannot prevent deadlocks by denying the mutual-exclusion condition, because some resources are intrinsically nonsharable (like Mutex locks).
  - **Hold and Wait :-**
    - One protocol that we can use requires each thread to request and be allocated all its resources before it begins execution. (impracticle due to dynamic nature of requesting resources).
    - An alternative protocol allows a thread to request resources only when it has none.
    - Both these protocols have two main disadvantages
      - **First**, resource utilization may be low, since resources may be allocated but unused for a long period.
      - **Second**, starvation is possible. A thread that needs several popular resources may have to wait indefinitely, because at least one of the resources that it needs is always allocated to some other thread.
  - **No Preemption :-**
    - one protocol is If a thread is holding some resources and requests another resource that cannot be immediately allocated to it, then all resources the thread is currently holding are preempted.
      - The preempted resources are added to the list of resources for which the thread is waiting.
    - Alternatively, if a thread requests some resources, we first check whether they are available. If they are, we allocate them. If they are not, we check whether they are allocated to some other thread that is waiting for additional resources. If so, we preempt the desired resources from the waiting thread and allocate them to the requesting thread. If the resources are neither available nor held by a waiting thread, the requesting thread must wait.
  - **Circular Wait:-**
    - presents an opportunity for a practical solution by invalidating one of the necessary conditions.
      -  One way to ensure that this condition never holds is to impose a total ordering of all resource types and to require that each thread requests resources in an increasing order of enumeration.

***

## Chapter 8 (Main Memory)

### pagging
