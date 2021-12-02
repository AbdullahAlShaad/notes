# Concurrency Notes

**Race Conditions :** When two or more operations required to execute in correct order but they are not executed in correct order, it is called race conditions

**Data Race :** It is a type of Race Conditions where one concurrent operation attempts to read a variable while another concurrent operations attempt to write the variable


**Atomicity :** A part of code is called atomic when it is uninterruptible during execution. Either the whole block executes or none

**Deadlock :** Process A is waiting for a resource held by Process B which is waiting for a resource held by Process A, it creates a infinite loop. The situation is called Deadlock.

**Livelocks :** Two programs doing concurrent operation but they are not moving the state or going to finish . It is called Livelocks. It is one tyep of Starvation

**Starvation :** Starvation is a situation where a concurrent process cannot get all the resource it needs to perform works. Other processes keeps using resource more to starve a 					 process.

### Three Questions to ask about concurrent code :

1. Who is responsible for the concurrency ?

2. How the problem space mapped onto concurrency primitives ?

3. Who is responsible for synchronization ?

Example :
```
// CalculatePi calculates digits of Pi between the begin and end
// place.
//
// Internally, CalculatePi will create FLOOR((end-begin)/2) concurrent
// processes which recursively call CalculatePi. Synchronization of
// writes to pi are handled internally by the Pi struct.


func CalculatePi(begin, end int64, pi *Pi)
```
So we need to document if we are providing safety or the caller should make sure of safety


`-- GoRoutines == Threads , Channels == Mutex`

-- Go Routines are lightweight and automatically managed by go runtime. We don't need to worry about thread pools and thread count or things like that


-- Should use primitives(mutex) when we have a critical section, and we are trying to guard internal state of a struct
-- Should use channels when we are trying to send/receive data and coordinate multiple piece of logic
-- Mutex are more lightweight than channels, but we should try to use channels whenever possible. If performance issue arise , we should try to restructure our code

**"Share memory by communicating, don't communicate by sharing memory."** 
Don't communicate by sharing memory. Don't allocate memory and dedicate that memory as a medium of sharing data. Rather communicate and use some memory to serve the sharing purpose. In channels, memory complexity is hidden from programmers. We only worry about communicating . And GoRoutines does that using shared memory.


**Non-Preemptive :** A routines that can not be interrupted

**Subroutines :** Functions, Closures or Methods

**Coroutines :** Concurrent Subroutines that are non-preemptive

-- Goroutines are preemptable. Go run time stops goroutine when it is blocked and resumes when the goroutines are unblocked again
-- time.Sleep(3s) to print hello using goroutines create a race condition cause the probability of goroutine execution is increased but not guaranteed

**Signal and Broadcast :** Signal finds the go routine waiting longest and notifies it. On the other hand Broadcast notifies every go routine waiting.

--*Once* package make sure Once.Do(func()) is called only once.

--*Pool* is used when we use something which is costly to instantiate. So we use pool and reuse the instantiated object. A fixed number of things are created in Pool and we use them and put them back

**Channels :** Communications between two goroutines. Channels can be unidirectional or bidirectional.

*--"If a goroutine is responsible for creating a goroutine, 
it is also responsible for ensuring it can stop the goroutine"*

**Preemptable :** Something that can be paused and resumed

**Confinement :**
- Go routines should not access the global data rather the should have each copy of data

**Select :**
- Blocks the code until one of its case is executed

**Error Handling :**
- Error should be passed along with result to a place where the scope has more knowledge about how to handle that error

### Pipeline :
1. Passing data through series of go routines/functions
2. Input of a go routine is feed to another one
3. Generator generates data at first
4. A stage consumes and returns the same type


### Fan Out Fan In
1. If a process takes much time and input of this function does not depends on output of prev iteration we can run this function on multiple go routine to speed up.
2. We multiplex outputs of different channels into one channel and stream them in one channel

### Bridge-Channel
- We take a channel of channels and output all the data from different channels to one channel


### Queueing :
1. Queuing will not help decrease the amount time spent in a system. It will either increase arrival rate of units or increase the average time spends in the system
2. Using queue most of the time we can not increase the performance but we can save the request from the client. So the clients will have lag but the won't have request denied
3. Queue improves performance when batch processing improves performance(example : IO Buffered Write). It collects the data and allocate memory at once since memory allocation is costly . If we do it for single data performance decreases

### Context Package :
1. Nice way to terminate go routines from any of its ancestor
2. Can set deadline for a go routine/function to complete its execution
3. Can pass some key-value pair
4. Enable Preemptability


