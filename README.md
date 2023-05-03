Download Link: https://assignmentchef.com/product/solved-cs333-programming-project-2-threads-and-interprocess-communication
<br>
In this project you will learn about threads and gain familiarity writing programs involving concurrency control.  You will begin by studying the thread package, which implements multi-threading.  You will make modifications and additions to the existing code.  Then you will use the threads package to solve some traditional concurrency problems.

In addition, you will gain familiarity programming in the KPL language.

If you have difficulties with this project or want to go into more depth, take a look at the document called “The Thread Scheduler and Concurrency Control Primitives”:

<u>http://web.cecs.pdx.edu/~harry/Blitz/BlitzDoc/ThreadScheduler.htm</u>              <u>http://web.cecs.pdx.edu/~harry/Blitz/BlitzDoc/ThreadScheduler.pdf</u>

Step 1:  Download the Files

Start by creating a directory for the files you’ll need for this project.  You might call it:

<strong>    </strong><em>~YourUserName</em><strong>/cs333/p2 </strong>

Then download all the files from:

<strong>http://www.cs.pdx.edu/~harry/Blitz/OSProject/p2/</strong>

into your directory.  You should get the following files:

<strong>     makefile </strong>

<strong>     DISK </strong>

<strong>     System.h </strong>

<strong>     System.c </strong>

<strong>     Runtime.s </strong>

<strong>     Switch.s </strong>

<strong>     List.h </strong>

<strong>     List.c </strong>

<strong>     Thread.h </strong>

<strong>     Thread.c </strong>

<strong>     Main.h </strong>

<strong>     Main.c </strong>

<strong>     Synch.h </strong>

<strong>     Synch.c </strong>

In this project, you will modify and hand in the following files:

<strong>     Main.c </strong>

<strong>     Synch.h </strong>

<strong>     Synch.c </strong>

After getting the files, you should be able to compile all the code (as is) with the UNIX <strong>make</strong> command.  The program executable we are building will be called “os”.  You can execute the program by typing:

% make

% blitz –g os




In the course of your experimentation, you may modify other files besides <strong>Synch.h</strong>, <strong>Synch.c</strong> and

<strong>Main.c</strong>, but the code you are required to write and turn in doesn’t require any changes to the other files.  For example, you may wish to uncomment some of the print statements, to see what happens.  <em>However, your final versions of <strong>Synch.h</strong>, <strong>Synch.c </strong>and<strong> Main.c</strong> must work with the other files, exactly as they are distributed. </em>

Be sure you copy all files.  Even though there are similarities with some of the files used for the previous project, there may be some subtle but important differences.

Step 2:  Study the Existing Code

The code provided in this project provides the ability to create and run multiple threads, and to control concurrency through several synchronization methods.

Start by looking over the <strong>System</strong> package.  Focus on the material toward the beginning of file <strong>System.c</strong>, namely the functions:

<strong>     print       printInt       printHex       printChar       printBool </strong>

<strong>     nl </strong>

<strong>     MemoryEqual </strong>

<strong>     StrEqual </strong>

<strong>     StrCopy </strong>

<strong>     StrCmp </strong>

<strong>     Min </strong>

<strong>     Max </strong>

<strong>     printIntVar       printHexVar       printBoolVar       printCharVar  printPtr </strong>

Get familiar with the printing functions; you’ll be calling them a lot.  Some are implemented in assembly code and some are implemented in KPL in the <strong>System</strong> package.




The following functions are used to implement the heap in KPL:




<strong>     KPLSystemInitialize  </strong>

<strong>     KPLMemoryAlloc        KPLMemoryFree  </strong>




Objects can be allocated on the heap and freed with the <strong>alloc</strong> and <strong>free</strong> statements.  The HEAP implementation is very rudimentary in this implementation.  In your kernel, you may allocate objects during start-up but after that, <em>YOU MUST NOT ALLOCATE OBJECTS ON THE HEAP</em>!  Why?  Because the heap might fill up and then what is a kernel supposed to do?  Crash.




In this project, you should not allocate anything on the heap.




The following functions can be ignored since they concern aspects of the KPL language that we will not be using:




<strong>     KPLUncaughtThrow  </strong>

<strong>     UncaughtThrowError  </strong>

<strong>     KPLIsKindOf  </strong>

<strong>     KPLSystemError </strong>




The <strong>Runtime.s </strong>file contains a number of routines coded in assembly language.  It contains the program entry point and the interrupt vector in low memory.  Take a look at it.  Follow what happens when program execution begins at location 0x00000000 (the label “_entry”).  The code labeled “_mainEntry” is included in code the compiler produces.  The “_mainEntry” code will call the <strong>main</strong> function, which appears in the file <strong>Main.c</strong>.




In <strong>Runtime.s</strong>, follow what happens when a timer interrupt occurs.  It makes an “up-call” to a routine called <strong>_P_Thread_TimerInterruptHandler</strong>.  This name refers to “a function called

<strong>TimerInterruptHandler</strong> in a package called <strong>Thread</strong>.”  (<strong>_P_Thread_TimerInterruptHandler</strong> is the name the compiler will give this function.)




All the code in this project assumes that no other interrupt types (such as a <strong>DiskInterrupt</strong>) occur.  In <strong>Runtime.s</strong>, follow what would happen if another sort of interrupt should ever occur.




The KPL language will check for lots of error conditions, such as the use of a null pointer.  Try changing the program to make this error.  Follow in <strong>Runtime.s</strong> what happens when this occurs.




Next take a look at the <strong>List</strong> package.  Read the header file carefully.  This package provides code that implements a linked list.  We’ll use linked lists in this project.  For example, the threads that are ready to run (and waiting for time on the CPU) will be kept in a linked list called the “ready list”.  Threads that become BLOCKED will sit on other linked lists.  Also look over the <strong>List.c</strong> code file.




The key class in this project is named <strong>Thread</strong>, and it is located in the <strong>Thread</strong> package along with other stuff (see <strong>Thread.h</strong>, <strong>Thread.c</strong>).  For each thread, there will be a single <strong>Thread</strong> object.  <strong>Thread</strong> is a subclass of <strong>Listable</strong>, which means that each <strong>Thread</strong> object contains a <strong>next</strong> pointer and can be added to a linked list.




The <strong>Thread</strong> package is central and you should study this code thoroughly.  This package contains one class (called <strong>Thread</strong>) and several functions related to thread scheduling and time-slicing:




<strong>InitializeScheduler</strong> ()

<strong>IdleFunction</strong> (arg: int)

<strong>Run</strong> (nextThread: ptr to Thread)

<strong>PrintReadyList</strong> ()

<strong>ThreadStartMain</strong> ()

<strong>ThreadFinish</strong> ()

<strong>FatalError</strong> (errorMessage: ptr to array of char)

<strong>SetInterruptsTo</strong> (newStatus: int) returns int       <strong>TimerInterruptHandler</strong> ()




<strong>FatalError</strong> is the simplest function.  We will call <strong>FatalError</strong> whenever we wish to print an error message and abort the program.  Typically, we’ll call <strong>FatalError</strong> after making some check and finding that things are not as expected.  <strong>FatalError</strong> will print the name of the thread invoking it, print the message, and then shut down.  It will throw us into the BLITZ emulator command line mode.  Normally, the next thing to do might be to type the “st” command (short for “stack”), to see which functions and methods were active.




(Of course the information printed out by the emulator will pertain to only the thread that invoked <strong>FatalError</strong>.  The emulator does not know about threads, and it is pretty much impossible to extract information about other threads by examining bytes in memory.)




The next function to look at is <strong>SetInterruptsTo</strong>, which is used to change the “I” interrupt bit in the CPU.  We can use it to disable interrupts with code like this:




… = SetInterruptsTo (DISABLED)




and we can use it to enable interrupts:




… = SetInterruptsTo (ENABLED)




This function returns the previous status.  This is very useful because we often want to DISABLE interrupts (regardless of what they were before) and then later we want to return the interrupt status to whatever it was before.  In our kernel, we’ll often see code like:




<u>var</u> oldIntStat: int

…

oldIntStat = SetInterruptsTo (DISABLED)

…

oldIntStat = SetInterruptsTo (oldIntStat)




Next take a look at the <strong>Thread</strong> class.  Here are the fields of <strong>Thread</strong>:




<strong>name</strong>: <u>ptr</u> <u>to</u> <u>array</u> <u>of</u> <u>char</u>  <strong>status</strong>: <u>int</u>

<strong>systemStack</strong>: <u>array </u>[SYSTEM_STACK_SIZE] <u>of</u> <u>int</u>

<strong>regs</strong>: <u>array</u> [13] <u>of</u> <u>int</u>      <strong>stackTop</strong>: <u>ptr</u> <u>to</u> <u>void</u> <strong>initialFunction</strong>: <u>ptr</u> <u>to</u> <u>function</u> (<u>int</u>) <strong>initialArgument</strong>: <u>int</u>




Here are the operations (i.e., methods) you can do on a <strong>Thread</strong>:




<strong>Init</strong> (n: <u>ptr</u> <u>to</u> <u>array</u> <u>of</u> <u>char</u>)

<strong>Fork</strong> (fun: <u>ptr</u> <u>to</u> <u>function</u> (<u>int</u>), arg: <u>int</u>)

<strong>Yield</strong> ()

<strong>Sleep</strong> ()

<strong>CheckOverflow</strong> ()  <strong>Print</strong> ()




Each thread is in one of the following states:  JUST_CREATED, READY, RUNNING, BLOCKED, and UNUSED, and this is given in the <strong>status</strong> field.  (The UNUSED status is given to a <strong>Thread</strong> after it has terminated.  We’ll need this in later projects.)




Each thread has a <strong>name</strong>.  To create a thread, you’ll need a <strong>Thread</strong> variable.  First, use <strong>Init</strong> to initialize it, providing a name.




Each thread needs its own stack and space for this stack is placed directly in the <strong>Thread</strong> object in the field called <strong>systemStack</strong>.  Currently, this is an array of 1000 words, which should be enough.  (It is conceivable our code could overflow this limit; there is a check to make sure we don’t overflow this limited area.)




All threads in this project will run in System mode.  Therefore the stack is called the “system stack”.  In later projects, we’ll see that this stack is used only for kernel routines.  User programs will have their own stacks in their virtual address spaces, but we are getting ahead of ourselves.




The <strong>Thread</strong> object also has space to store the state of the CPU, namely the registers.  Whenever a thread switch occurs, the registers will be saved in the <strong>Thread</strong> object.  These fields (<strong>regs</strong> and <strong>stackTop</strong>) are used by the assembly code routine named <strong>Switch</strong>.




The <strong>Thread</strong> object also has space to store a pointer to a function (the <strong>initialFunction</strong> field) and an argument for this function (the <strong>initialArgument</strong> field).  This pointer will point to the “main” function of this thread; this is the function that will get executed when this thread begins execution.  We are storing a pointer to the function because this is a variable: different threads may execute different functions.




We are also able to supply an initial argument to this thread, through the <strong>initialArgument</strong> field.  This argument must be an integer.  Often there will be several threads executing the same “main” function.  The argument is a handy way to let each thread know what its role should be.  For example, we might create 10 threads each using the same “main” function, but passing each thread a different integer (say, between 1 and 10) to let it know which thread it is.




After initializing a new <strong>Thread</strong>, we can start it running with the <strong>Fork</strong> method.  This doesn’t immediately begin the thread execution; instead it makes the thread READY to run and places it on the <strong>readyList</strong>.  The <strong>readyList</strong> is a linked list of <strong>Threads</strong>.  All <strong>Threads</strong> on the <strong>readyList</strong> have status READY.  <strong>ReadyList </strong>is a global variable.  There is another global variable named <strong>currentThread</strong>, which points to the currently executing <strong>Thread</strong> object; i.e., the <strong>Thread</strong> whose status is RUNNING.




The <strong>Yield</strong> method should only be invoked on the currently running thread.  It will cause a switch to some other thread.




Follow the code in <strong>Yield</strong> closely to see what happens when a thread switch occurs.  First, interrupts are disabled; we don’t want any interference during a thread switch.  The <strong>readyList</strong> and <strong>currentThread</strong> are shared variables and, while switching threads, we want to be able to access and update them safely.  Then <strong>Yield</strong> will find the next thread from the <strong>readyList</strong>.  (If there is no other thread, then <strong>Yield</strong> is effectively a nop.)  Then <strong>Yield</strong> will make the currently running process READY (i.e., no longer RUNNING) and it will add the current thread to the tail end of the <strong>readyList</strong>.  Finally, it will call the <strong>Run</strong> function to do the thread switch.




The <strong>Run</strong> method will check for stack overflow on the current thread.  It will then call <strong>Switch</strong> to do the actual <strong>Switch</strong>.




<strong>Switch</strong> may be the most fascinating function you ever encounter!  It is located in the assembly code file <strong>Switch.s</strong>, which you should look at carefully.  Switch does not return to the function that called it.  Instead, it switches to another thread.  Then it returns.  Therefore, the return happens to another function in another thread!




The only place <strong>Switch</strong> is called is from the <strong>Run</strong> function, so <strong>Switch</strong> returns to some invocation of the <strong>Run</strong> function in some other thread.  That copy (i.e., invocation) of <strong>Run</strong> will then return to whoever called it.  This could have been some other call to <strong>Yield</strong>, so we’ll return to another <strong>Yield</strong> which will return to whoever called it.




And this is exactly the desired functionality of <strong>Yield</strong>!  A call to <strong>Yield</strong> should give up the processor for a while, and eventually return after other threads have had a chance to execute.




<strong>Run</strong> is also called from <strong>Sleep</strong>, so we might be returning from a call to <strong>Sleep</strong> after a thread switch.




How is everything set up when a thread is first created?  How can we “return to a function” when we have not ever called it?  Take a look at function <strong>ThreadStartMain</strong> in file <strong>Thread.c</strong> and look at function <strong>ThreadStartUp</strong> in file <strong>Switch.s</strong>.




What happens when a thread is terminated?  Take a look at <strong>ThreadFinish</strong> in file <strong>Thread.c</strong>.  Essentially, the thread is put to sleep with no hope of ever being awakened.  (No wonder they call it “Thread Death!”)




Next, take a look at what happens when a Timer interrupt occurs while some thread is executing.  This is an interrupt, so the CPU begins by interrupting the current routine’s execution and pushing some state onto its (system) stack.  Then it disables interrupts and jumps to the assembly code routine called <strong>TimerInterruptHandler</strong> in <strong>Runtime.s</strong>, which just calls the <strong>TimerInterruptHandler</strong> function in <strong>Thread.c</strong>.




In <strong>TimerInterruptHandler</strong>, we call <strong>Yield</strong>, which then switches to another thread.  Later, we’ll come back here, when this thread gets another chance to run.  Then, we’ll return to the assembly language routine which will execute a “reti” instruction.  This will restore the state to exactly what it was before and the interrupted routine (whatever it was) will get to continue.




Note that this code maintains a variable called <strong>currentInterruptStatus</strong>.  This is because it is rather difficult to query the “I” bit of the CPU.  It is easier to just change the variable whenever a change to the interrupt status changes.  We see this occurring in the <strong>TimerInterruptHandler </strong>function.  Clearly interrupts will be disabled immediately after the interrupt occurs.  And the <strong>Yield</strong> function will preserve the interrupt status.  So when we return from <strong>Yield</strong>, interrupts will once again be disabled.  Before returning to the interrupted thread, we set the <strong>currentInterruptStatus</strong> to ENABLED.  (They must have been enabled before the interrupt occurred—or else it could not have occurred—so after we execute the “reti” instruction, the status will revert to what it was before, namely ENABLED.)




Now you are ready to start playing with and modifying the code!  Please experiment with the code we have just discussed, as necessary to understand it.










<h1>Step 3:  Run the “SimpleThreadExample” Code</h1>




Execute and trace through the output of <strong>SimpleThreadExample</strong> in file <strong>Main.c</strong>.




In <strong>TimerInterruptHandler</strong> there is a statement




printChar (‘_’)




which is commented out.  Try uncommenting it.  Make sure you understand the output.




In <strong>TimerInterruptHandler</strong>, there is a call to <strong>Yield</strong>.  Why is this there?  Try commenting this statement?  What happens.  Make sure you understand how <strong>Yield</strong> works here.













<strong><u>Step 4:  Run the “MoreThreadExamples” Code</u></strong>




Trace through the output.  Try changing this code to see what happens.













<h1>Step 5:  Implement the “Mutex” Class</h1>




In this part, you must implement the class <strong>Mutex</strong>.  The class specification for <strong>Mutex</strong> is given to you in <strong>Synch.h</strong>:




<u>class</u> Mutex     <u>superclass</u> Object     <u>methods</u>       Init ()

Lock ()

Unlock ()

IsHeldByCurrentThread () <u>returns</u> <u>bool</u>   <u>endClass</u>




You will need to provide code for each of these methods.  In <strong>Synch.c </strong>you’ll see a <strong>behavior</strong> construct for <strong>Mutex</strong>.  There are methods for <strong>Init</strong>, <strong>Lock</strong>, <strong>Unlock</strong>, and <strong>IsHeldByCurrentThread</strong>, but these have dummy bodies.  You’ll need to write the code for these four methods.




You will also need to add a couple of fields to the <strong>class</strong> specification of <strong>Mutex</strong> to implement the desired functionality.




How can you implement the <strong>Mutex</strong> class?  Take a close look at the <strong>Semaphore</strong> class; your implementation of <strong>Mutex</strong> will be quite similar.




First consider the <strong>IsHeldByCurrentThread</strong> method, which may be invoked by any thread.  The code of this method will need to know which thread is holding a lock on the mutex; then it can compare that to the <strong>currentThread</strong> to see if they are the same.  So, you might consider adding a field (perhaps called <strong>heldBy</strong>) to the <strong>Mutex</strong> class, which will be a pointer to the thread holding the mutex.  Of course, you’ll need to set it to the current thread whenever the mutex is locked.  You might use a null value in this field to indicate that no thread is holding a lock on the mutex.




When a lock is requested on the mutex, you’ll need to see if any thread already has a lock on this mutex.  If so, you’ll need to put the current process to sleep.  For putting a thread to sleep, take a look at the method <strong>Semaphore.Down</strong>.  At any one time, there may be zero, one, or many threads waiting to acquire a lock on the mutex; you’ll need to keep a list of these threads so that when an <strong>Unlock</strong> is executed, you can wake up one of them.  As in the case of <strong>Semaphore</strong>s, you should use a FIFO queue, waking up the thread that has been waiting longest.




When a mutex lock is released (in the <strong>Unlock</strong> method), you’ll need to see if there are any threads waiting to acquire a lock on the mutex.  You can choose one and move it back onto the <strong>readyList</strong>.  Now the waiting thread will begin running when it gets a turn.  The code in <strong>Semaphore.Up</strong> does something similar.




It is also a good idea to add an error check in the <strong>Lock</strong> method to make sure that the current thread asking to lock the mutex doesn’t already hold a lock on the mutex.  If it does, you can simply invoke <strong>FatalError</strong>.  (This would probably indicate a logic error in the code using the mutex.  It would lead to a deadlock, with a thread frozen forever, waiting for itself to release the lock.)  Likewise, you should also add a check in <strong>Unlock</strong> to make sure the current thread really does hold the lock and call <strong>FatalError</strong> if not.  You’ll be using your <strong>Mutex</strong> class later, so these checks will help your debugging in later projects.




The function <strong>TestMutex</strong> in <strong>Main.c</strong> is provided to exercise your implementation of <strong>Mutex</strong>.  It creates 7 threads that compete vigorously for a single mutex lock.

Step 6:  Implement the Producer-Consumer Solution

Both the Tanenbaum and Silberschatz, et al. textbooks contain a discussion of the Producer-Consumer problem, including a solution.  Implement this in KPL using the classes <strong>Mutex</strong> and <strong>Semaphore</strong>.  Deal with multiple producers and multiple consumers, all sharing a single bounded buffer.

The <strong>Main</strong> package contains some code that will serve as a framework.  The buffer is called <strong>buffer</strong> and contains up to <strong>BUFFER_SIZE</strong> (e.g., 5) characters.  There are 5 producer processes, each modeled by a thread, and 3 consumer processes, each modeled by a thread.  Thus, there are 8 threads in addition to the main thread that creates the others.

Each producer will loop, adding 5 characters to the buffer.  The first producer will add five ‘A’ characters, the second producer will add five ‘B’s, etc.  However, since the execution of these threads will be interleaved, the characters will be added in a somewhat random order.




Step 7:  Implement the Dining Philosopher’s Solution Using a Monitor




A starting framework for your solution is provided in <strong>Main.c</strong>.  Each philosopher is modeled with a thread and the code we’ve provided sets up these threads.  The synchronization will be controlled by a “monitor” called <strong>ForkMonitor</strong>.




The code for each thread/philosopher is provided for you.  Look over the <strong>PhilosophizeAndEat</strong> method; you should not need to change this code.




The monitor to control synchronization between the threads is implemented with a class called <strong>ForkMonitor</strong>.  The following class specification of <strong>ForkMonitor</strong> is provided:




class ForkMonitor     superclass Object     fields

status: array [5] of int      — For each philosopher: HUNGRY,

— EATING, or THINKING     methods       Init ()

PickupForks (p: int)

PutDownForks (p: int)

PrintAllStatus ()   endClass




You’ll need to provide the code for the <strong>Init</strong>, <strong>PickupForks</strong> and <strong>PutDownForks</strong> methods.  You’ll also need to add additional fields and perhaps even add another method.




The code for <strong>PrintAllStatus</strong> is provided.  You should call this method whenever you change the status of any philosopher.  This method will print a line of output, so you can see what is happening.




How can you proceed?  You’ll need a mutex to protect the monitor itself.  There are two main methods (<strong>PickupForks</strong> and <strong>PutDownForks</strong>) which are called by the philosopher threads.  Upon beginning each of these methods, the first thing is to lock the monitor mutex.  This will ensure that only one thread at a time is executing within the monitor.  Just before each of these methods returns, it must unlock the monitor (by unlocking the monitor’s mutex) so that other threads can enter the monitor code.




You’ll also need to use the <strong>Condition</strong> class, which is provided in the <strong>Synch</strong> package.  (The <strong>Condition</strong> class uses the class <strong>Mutex</strong>, so it is assumed that you’ve finished and tested the <strong>Mutex</strong> class.)




The BLITZ emulator has a number of parameters and one of these is how often a timer interrupt occurs.  The default value is every 5000 instructions.  You might try changing this parameter to see how it affects your programs behavior.  To change the simulation parameters, type the <strong>sim</strong> command into the emulator.  This command will give you the option to create a file called




<strong>     .blitzrc </strong>




After creating this file, you can edit it by hand.  The next time you run the emulator, it will use this new value.  Also note that too small a value—like 1000—will cause the program to hang.  What do you suppose causes this effect?




<u>QUESTION:</u>  Both textbooks discuss the semantics of signaling a condition variable.  They mention “Hoare semantics.”  The comments in the code in <strong>Synch.c</strong> say that this version implements “Mesastyle” semantics.  Is this the same or different from Hoare semantics?




<strong> </strong>




<h1>An Example of Correct Output</h1>




The following files contain an example of what correct output should look like:




<strong>     DesiredOutput1.pdf</strong>

<strong>     DesiredOutput2.pdf</strong>

<strong>     DesiredOutput3.pdf</strong>




<strong> </strong>




<h1>What to Hand In</h1>




Complete all the above steps.




Please submit hardcopy of the following files:




<strong>     Synch.h </strong>

<strong>     Synch.c  Main.c </strong>




Also include hardcopy showing the output for steps 5, 6, and 7.




Please print both your code and the output using a fixed-width font like this, in this

and all future assignments.  Much of the output is designed to look nice when printed with a fixed-width font, but is more difficult to read in a standard variable-width font.


