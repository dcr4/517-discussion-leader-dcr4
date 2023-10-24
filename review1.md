# Review

## Info

Reviewer Name: Desmond Roberts
Paper Title: Exterminate all operating system abstractions

https://ieeexplore.ieee.org/abstract/document/513459

## Summary

"The defining tradgedy of the operating systems community has been the definition of an OS as a software that both multiplexes and abstracts physical resources."

The authors of "Extermindate All Operating System Abstractions" believe that primary goal of OSs as a layer of abstraction between the applications and the hardware has been misguided. They believe that the key to addressing many of the problems that have plagued kernel developers is simply exporting the hardware as a low level interface. In this way, application level code is able to better optimize itself and the kernel serves a much smaller role in maintaining code and data structures that are absolutely critical to system operation.

### Problem and why we care

"It is FUNDAMENTALLY impossible to abstract resources in a way that is useful to all applications and to implement those abstractions in a way that is efficient across disparate needs."

The authors list a "jeremiad" (which I learned is a 'long, mournful complaint or lamentation; a list of woes') of problems with current operating system design.  First, authors believe that providing a full virtual memory system requires a large amount of complex multi-threaded code and decreases overall code reliability.  Also, the authors seem frustrated with the poor adaptability of large and complicated OSs.  Many good OS ideas are ignored due to the sheer difficulty of their implementation and a change is never localized, but rather will affect other parts of the system.  Furthermore, OS abstractions affect performance by providing overly general abstractions or paying the overhead of managing things like garbage collectors.  This makes the OS unable to optimize each specific program.  

### Gap in current approaches

"Operating system designers should learn what hardware designers learned a decade ago during the transition from CISC to RISC: hardware should provide primitives, not high-level abstractions."

The current approach makes research and improvement difficult.  The authors list frustrations about how good OS ideas are never implemented and researchers are forced into manipulating a overly complex kernel.  They believe that putting more kernel capabilities into application space will allow many more researchers to make forward movement.

### Hypothesis, Key Idea, or Main Claim

"We believe, therefore, that the attempt to provide OS abstractions is the root of all operating system problems. We contend that these problems can be solved directly by the systematic elimination of OS abstractions, lowering the interface enforced by the OS to a level close to the raw hardware."

### Method for Proving the Claim

The authors provide various implementation roadmaps for how their exokernel deals with address spaces, processes, and IPC.  For managing address spaces, the exokernel wraps priveleged instructions in system calls and writes to priveleged states are associated with access checks.  For IPC, one process can transfer a PC from protection domain to a agreed upon value in the other, donate their time-slice, install the new domain's exception context, and indicate which process initiated the call.  The authors then acknowledge that this is far from a complete enumeration of all the problems.

### Method for evaluating

They use general operating system knowledge to inform their opinions and give one benchmark: "our prototype exokernel performs 10-100 times faster than a mature monolithic system in operations such as as IPC, exception forwarding,
and virtual memory manipulations."  They also address several common questions and provide their rebuttal.

### Contributions: what we take away

The current monolithic operating system design leaves much to be desired.  Systems are large and overly complicated making research and change difficult.  Applications are forced to adapt to an interface that is too general to be consistently useful and precious CPU cycles are wasted providing overhead for the OS's abstractions.

## Pros (3-6 bullets)

- By implementing an exokernel, applications are given more fine-tuned control.  This allows them to further optimize their program.  For example, traditional OS would have to make choices such as optimizing for frequent reads or random writes that every application would be subject to while a exokernel program
- easier research
- if, for example, an application gave incorrect arguments to a library function, it is not the kernel's job to check those args because those incorrect args could only affect the application that provided them

## Cons (3-6 bullets)

- very little commercial success, many problems will likely arise and have to be fixed before an exokernel is viable for a production level product
- may put more burden on application programmers
- giving each application the ability to define their own page tables, GUIs, and other very important functions which could give rise to a chaotic system

### What is your analysis of the proposed?

I think that this paper is very dramatic in some ways. Its full of very powerful statements that are a little inflamatory or exaggerated in nature. However, a feeling of frustration can be felt through the paper. Researchers that have chosen OS as their dedicated field of research want more freedom to conduct research. They feel that this exokernel design is more elegant, performance, and also facilitates improvement of current OS design through easier research. The only question is whether application programmers (who far outnumber kernel developers) actually want this change.

## Details Comments, Observations, Questions

- What are some similarities and differences between the microkernel and exokernel design?
- Because application developers make up a much more significant proportion of the developer population than kernel architects, they should be prioritized. Is this design something that application developers actually want? If you were writing an application, how often do you forsee yourself wanting the kind of fine-grained control that the exokernel offers?
- Is it simply too late to transition to a new OS design? Doing so would require hardware to write drivers for it and application developers to restructure their code to work with LibOS. Is extra performance worth all of this effort?




### My Notes

multiplex = system involving simultaneous transmission of several messages along a single channel of communication

jeremiad = a long mournful complaint or lamentation; a list of woes

application-level software vs user-level

"The standard definition of an operating system is software that securely multiplexes and abstracts physical resources. We believe that this definition, specifically its view of the OS as an abstractor of hardware, is crippling and wrong. The basic intuition behind our arguments is that no OS abstraction can fit all applications."

"It is FUNDAMENTALLY impossible to abstract resources in a way that is useful to all applications and to implement those abstractions in a way that is efficient across disparate needs."

"We believe, therefore, that the attempt to provide OS abstractions is the root of all operating system problems."

"We define the operating system as any piece of software that the application cannot either change or avoid"

"OS abstractions are reduntant or of little value when compared to the cost of providing them."

jeremiad:
- poor reliability = abstractions need a lot of complicated multi-threaded code
- poor adaptability = OSs are very slow to change, a new ideas are rarely incorporated due to their complicated nature
- poor performance = OS abstractions cost CPU cycles and are often overly general
- poor flexibility = apps are forced to go through the OSs inefficient abstractions

Solution

We contend that the solution to all of these difficulties is straightforward: eliminate operating system abstractions. The OS should only export physical resources in a secure manner; it should not be in the business of presenting a pretty, machine-independent interface to applications.

exokernel
- OS that embodies an abstraction free, low-level interface

"Security is enforced by associating every resource usage or binding point with a guard that checks access privileges."

Address Space
- priveleged instructions are wrapped in system calls
- calls that write to priveleged state are associated with access checks
Process
- set of exception program counter, associated address space, prologue and epilogue code to be called when the process' time slice expires
IPC
- 

BAD BENCHMARK (end of slide 80)

Discussion

Reliability
A general-purpose OS is constantly in the business of copying user data, guarding against illegal addresses, and checking for validity.
A simpler kernel enables easy modification. Most of this OSs code is just used to track access and ownership rights.

Resource management has been put into application space, allowing implementations to exploit application-specific knowledge in making tradeoffs (e.g., optimizing for reads or random writes, sparse address spaces, etc.)

From an engineering standpoint, this structure allows a broad pool of non-kernel architects to implement alternative implementations. Since the entire system does not depend on these implementations, application-level operating systems can be readily experimented with and altered by non-privileged implementors

The ability of application-level operating systems to support powerful, efficient, and unusual abstractions cannot be overemphasized. By allowing any application writer to implement fundamental system objects, the degree, ease, and pervasiveness of experimentation and utilization of the results of this experimentation can dramatically increase

Related Work
It should become small not by enforcing a limited set of high-level operations, but instead, through the systematic elimination of all operating system abstractions in order to expose the hardware to application-level software; from this primal mud, applications can craft their own abstractions, chosen for appropriateness and efficiency, rather than make do with abstractions force-fed under duress

export hardware resources rather than emulating them