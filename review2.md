# Review

## Info

Reviewer Name: Desmond Roberts
Paper Title: Exokernel: An Operating System Architecture for Application-Level Resource Management

https://dl.acm.org/citation.cfm?id=224076

## Summary

Broviding application level management of physical resources in the form of an exokernel, we allow application-specific customization of of traditional operating system abstractions and can greaty increase the speed and efficiency of the system.

### Problem and why we care

Hardcoding the implementation of OS abstractions hurts application performance. As comuputer scientists, efficiency is among the most important metrics by which we judge our programs. Particulary in a systems context, where the performance of other applications relies of the design of the OS, poor design can lead to huge drops in performance. Furthermore, implementation freedom in applications is greatly hindered by too-general operating systems interfaces.

### Gap in current approaches

The current approach views the operating system as a complex and monolithic piece of software. It is the OS's job to provide to applications an interface through which to access the hardware. However, this interface must be uniform across all applications. "Fixed, high-level abstractions hurt applications performance because there is no single way to abstract physical resources or to implement ab abstraction that is best for all applications." They also hide information like low-level exceptions, interrupts, or raw device I/O from applications which also decreases performance. Finally, high-level abstractions limit growth and change because when the interface changes, all applications that relied of the previous version must change as well.

### Hypothesis, Key Idea, or Main Claim

An exokernel operating system will perform at a much greater efficiency than a traditional monolithic kernel OS. The exokernel will also afford the application programmers greater flexibilty when it comes to design. Finally, current OS design hinders growth due its large, complex, and inflexible nature and the implementation of a good exokernel will not sacrifice security for speed.

### Method for Proving the Claim

The paper implements their own exokernel and compares and contrasts its pros and cons vs what they call a "mature monolithic UNIX operating sytem" named Ultrix.

### Method for evaluating

The paper provides several benchmarks. The exokernel performanced worse on prot100 and uprot100 but perfored much better (even up to an order of magnitude more efficiently) on the appel2 operation (which protects 100 pages of physical memory). Also they offer one example where the chose to implement an inverted page table. In this case, they were able to get a 2x speedup on the "dirty" kernel call, a 37% improvement on the appel1 kernel call, and 17% improvement on the appel2 kernel call.

### Contributions: what we take away

Is it possible to securely multiplex available hardware among multiple applications. Application programmers are provided with LibOS, a library operating system that can be used as is, changed, or even extended to better tailor to the needs of that specific application. By separating management from protection using secure bindings, hardware mechanisms, software caches, and downloading code the kernel is able to export a secure a low-level interface to the applications. The Aegis and ExOS prototypes that are implemented proved that exokernels are very performance and traditional OS abstractions can be implemented at an application level. The authors conclude that the exokernel architecture is a viable structure for high-performance, extensible operating systems.

## Pros (3-6 bullets)

- excellent performance, the number of kernel crossings in reduced
- greater elegance of design
- security is not sacrificed for performance.
- small, lean kernel

## Cons (3-6 bullets)

- not many benchmarks are provided.
- the system is not very mature, so as with any new idea/system, many bugs/vulnerabilities are sure to emerge.
- most (if not all) exokernels that were implemented were research projects

### What is your analysis of the proposed?

It seems that this idea offloads a lot of complexity from a small group of experienced and vetted kernel developers onto the average application programmer. While there are undoubtedly very good efficiency benefits to be gained by a smaller operating system and better customized applications, programmers are known for being lazy and would often rather take the path of least resistance.

Furthermore, speed/efficiency has become less and less of a concern with the absolutely mind-boggling improvements in hardware. There are programming languages like Python that prioritize readability and ease-of-programming over efficiency at almost every turn. I think many developers have no interest in implementing a novel paging technique that increases the efficiency of their program because they simply don't consider the effort worth it. The history of computer science has been a history of abstracting away difficult details for each new generation of programmers and I don't think that is going to change.

It seems that this type of kernel would have uses in systems where speed and efficiency are of the utmost importance.

## Details Comments, Observations, Questions

- Exokernels are often associated with low-level resource management. Can you draw parallels between the principles of exokernels and concepts in virtualization, such as hypervisors and containers?
- How does the idea of exokernels connect with emerging trends in computer science, such as cloud computing and edge computing? Can you think of scenarios where exokernel concepts could be beneficial in these contexts?







### My Notes
Exokernel: An Operating System Architecture for Application level Resource Management

“Hardcoding the implementation of (OS) abstractions is inappropriate for three main reasons: it denies applications the advantages of domain-specific optimizations, it discourages changes to the implementations of existing applications, and restricts the flexibility of application builders.”

exokernel = traditional OS abstractions such as virtual memory and IPC are implemented at the application level by untrusted software

“Substantial evidence exists that applications can benefit greatly from having more control over how resources are used to implement higher-level abstractions.”

Application -> hardware interface
secure bindings = applications securely bind to machine resources and handle events
visible resource revocation = applications participate in a resource revocation protocol
abort protocol = exokernel can break secure bindings of uncooperative applications by force


“Fixed high-level abstractions hurt application performance because there is no single way to abstract physical resources or to implement an abstraction that is best for all applications.”

“A simple kernel improves reliability and ease of maintenance, consumes few resources, and enables quick adaptation to new requirements”

Applications can provide as much portability and compatibility as is desirable. Apps that use an exokernel interface directly will not be portable, but applications that that use library operating systems that implement standard interfaces (such as POSIX) will be portable across any other system that provides the same interface

Exokernel main tasks:
tracking ownership of resources
ensuring protection by guarding all resource usage or binding points
revoking access to resources 

Securely expose hardware. The central tenet of the exokernel architecture is that the kernel should provide secure low-level primitives that allow all hardware resources to be accessed as directly as possible.

Phrased negatively, this principle states that an exokernel should avoid resource management,

An exokernel should export physical names (efficient, because they remove a level of indirection) as well as freelists, disk arm positions, cached TLB entries.

the one requirement needed to support secure bindings is a set of primitives that application-level software can use to express protection checks.

when a library operating system allocates a physical memory page, the exokemel creates a secure binding for that page by recording the owner and the read and write capabilities specified by the library operating system. The owner of a page has the power to change the capabilities associated with it and to deallocate it

application specific safe handlers (ash) = downloaded into the kernel to participate in message processing

abort protocol = exokernel takes resources from library operating systems that fail to respond satisfactorily to revocation pages

Aegis

primitive operations = encapsulate privileged instructions and are guaranteed not to alter application-visible registers

CPU is represented as a vector of time slices, allocated round robin style

Events: exception, interrupt, protected control transfers, address translations

Processor environments contain the four contexts required to support these events
exception context = program counter for where to jump to, pointer to physical memory for saving registers
interrupt context = program counter, register save region
protected entry context = program counters for for synchronous and asynchronous protected control transfers from other applications, access control is managed by the application itself
addressing context = set of guaranteed mappings, 

Aegis is FAST, every operation/system call is at least an order of magnitude faster than Ultrix

Aegis caches TLB entries in the kernel by overlaying the hard-ware tlb with a large software tlb
STLB

protected control transfer 
= IPC abstractions
= a protected control transfer changes the program counter to an agreed-upon value in the callee, donates the current time slice to the callee’s processor environment, and installs the required elements of the callee’s processor context
= synchronous version - donate the current time and all future instantiations of it
= asynchronous version - donate only the remainder of the current time slice to the callee

Dynamic packet filter
determines which application an incoming message should be delivered to

LRPC = lightweight remote procedure call

Application-Specific Safe Handlers (ASH)
untrusted application-level message-handlers that are downloaded into the kernel, made safe by code inspection and sandboxing, and executed upon message arrival
fast because there are no intermediate copies of the message







