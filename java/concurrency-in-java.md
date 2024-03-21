- producer Consumer pattern: Decouple #workers threads from number of tasks.
	- https://www.youtube.com/watch?v=tEwNXnAmc9c




---
- Semaphore (permits)
    - after declaring it we cannot increase the number of permits but we can decrease it. <br> has the methods in the Lock interface and additional ones:
      - u can get the #of waiting threads and reference to them: 	getQueuedThreads() ...
      - u can as for more than one permits at the time.
		- default -> unfair if multiple threads waiting for acquiring. -> chose one randomely.
---

- thread: smallest unit of programmed instructions that can be managed independently by an operating system scheduler.
   
---
- OS kernel
  - provides mechanisms to manage and access computing resource.
      - hardware sensors, transceivers(wifi), storage,
        General purpose Cpu, Special purpose Cpu, virtual mem,
        <br>processes(unit of resource allocation and protection that
        coordinate with the os virtual mem manager to ensure no
        <br> process can access other's data) and 
        threads management.

- System Libraries:  
  - implementation that provide wrapper functions for the
    underlying OS mechanisms.   pthreads lib
  - Available to java programs via JNI(Java native interface):
      - enable java code which run on vm to call and be called by
        programs specific to a hardware and os platform, as well
        as by libs written in other lang, C & C++.
- JVM

- Threading & Synchronization packages.

- Additional Frameworks and Langs
  - provide other capabilities and features that leverage
    java concurrency mechanism, e.g.:
      - Android concurrency frameworks[HaMeR]
        - handler run long duration tasks in background thread and the publish the result to the UI thread. 
  - Scala ->  Akka, ActiveObject

- Application  
---
- Good Performance Involve:
  - Minimizing Response time.
  - Maximizing throughput for computations or querying databases.
  - Reducing power utilization.
  - Ensuring availability of system services even in face of failure or cyber attacks.  
 
 
  - techniques:
    - multiple cores:
      - for embarrassingly parallel programs that require little or no interactions 
        between parallel task.
      - dividing large problem into smaller ones and then merge the solutions.
         - map reduce paradigm.
---
- Amdahl's Law:
  - the speedup of program using multiple processors in parallel computing is 
      limited by the time needed for the sequential fraction of the prograqm that
      <br> can't run in parallel.         
---
### Data Race Conditions Patterns:
- check-then-act
  - if (val == 0) print(val + 1);
  - sol: make sure only one thread can 'pick up' a data element.
  
- read-modify-write
  - value++
  - sol: use atomic operation

--- 

### Synchronization
- Data, not code.
- Monitor:
  - using synchronized keyword (structured lock: acquirind and releasing lock happen implicitly), <br> u can lock block of code: `synchronized(monitorObj){}` or the whole method `synchronized retType name(){}`
  - Considerations
    - Choose the right object for the lock
    - synchronize the bare minimum code necessary
- synchronization solve Visibility problem:
  - values in critical section are read from the memory and wrtitten to memory after the execution
      
