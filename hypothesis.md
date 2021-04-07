Hypothesis

* Summarize the project, what it is, what its goals are, and why it exists
  * From what we have gathered, v8 isolates are completely separated instances of the v8 engine, . Isolated-vm provides… They are best to use in situations such as web servers, where you have a server wanting to serve multiple client requests and run javascript code simultaneously and securely in an isolated manner.
  *This is ideal for companies which provide serverless computing where clients do not want to have to maintain their own hardware/servers. A user would just provide some javascript program and then the provider (Amazon is a good example) would use the isolates to provide some abstraction between the client’s JS code. 
* What is the target domain of the system? Where is it valuable, and where is it not a good fit? These are all implemented in an engineering domain, thus are the product of trade-offs. No system solves all problems (despite the claims of marketing material).
  * The target domain is for serverless infrastructure
  * It’s valuable because it allows developers to write JS code that they want to run, rather than a whole VM, and they can also have isolation
  * It’s also valuable for efficiency. Because isolates are within a single process, you don’t have the “cold start” of starting the new process to service a request; you already have an existing process and it takes much less time to create an Isolate from that
  * It wouldn’t be a good fit if you were going to run anything other than JS code for a website to run on a web server, because you don’t have isolation outside of the Isolate inside of the process. If you needed isolation of all the libraries or within the memory of the entire system or for doing system calls, you’d probably prefer a VM.
* What are the "modules" of the system (see early lectures), and how do they relate? Where are isolation boundaries present? How do the modules communicate with each other? What performance implications does this structure have?
  * There are multiple modules of the system. The first is, obviously, isolates. There can be multiple isolates within a thread, each having their own space of memory within the threads memory space. Within each isolates memory space, they have a copy of the shared heap (the original heap that the thread sees),  the new heap that is used for the javascript runtime environment and the rest of the memory space used for execution. Finally, isolates also have access to shared code within the thread, which would be things such as shared libraries and ahead-of-time compiled code used to create new isolates. Within isolates, we can compile scripts, and then run these scripts within contexts within the isolate. An isolate can only run in one thread at a time, 
* What are the core abstractions that the system aims to provide. How and why do they depart from other systems?
  * Isolates and the isolated-vm API
  * departs from another system, like a single node process by providing isolation within a process
  * departs from a VM, because the Isolates do share the same underlying kernel and can even share some aspects of userspace, like the JS compiler
* In what conditions is the performance of the system "good" and in which is it "bad"? How does its performance compare to a Linux baseline (this discussion can be quantitative or qualitative)?
  * compared to containers and virtual machines, Isolates are able to spin up a lot faster due to less kernel heavy operations, there are no context switches since isolates occupy the same process.
  * Since there’s often already an existing process you don’t have to deal with the “cold start” of creating a new process -- you’re just creating a new Isolate, so this is much faster than something 
* What are the core technologies involved, and how are they composed?
  * v8 is the Javascript engine. 
  * Node
  * ...?
* What are the security properties of the system? How does it adhere to the principles for secure system design? What is the reference monitor in the system, and how does it provide complete mediation, tamperproof-ness, and how does it argue trustworthiness?
  * Provides isolation between client scripts through setting up separate execution environments via isolates. They cannot access each other’s heaps, etc. 
* What optimizations exist in the system? What are the "key operations" that the system treats as a fast-path that deserve optimization? How does it go about optimizing them?
  * Instead of starting a new process and creating a container or virtual machine to handle every client request, we just have to create a new isolate, which is much faster and simpler to set up. Also, because there can be multiple isolates within one process, switching between isolates to serve multiple requests does not require any context switches (because they are all in one process) which is also extremely time efficient.
* Subjective: What do you like, and what don't you like about the system? What could be done better were you to re-design it?
  * It is a very specific design for users of JS and can be limiting if you want to do more with the system. 
  * It is great for doing quick computing on less complex JS applications. 
  * Still unsure about communication between Isolates, but I think this could be a bit of a limitation
  
We've been doing this work together, and mostly in [this Google Doc](https://docs.google.com/document/d/1_fvu2UjdfBc8cCkJM324q-ox6aFuMnEfTl3vz7-prjs/edit?usp=sharing)
