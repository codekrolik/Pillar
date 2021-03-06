TODO: Advanced
----
define functions
	function(prm1, prm2, prm3)
	function prm1 prm2 prm3
	function prm1="" prm2=123 prm3=true
	function free form

Auto-generate usage info

To work similar to linux terminal:
	Figure out how to catch UP, DOWN, ctrl+shift+R
----


GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password';

**pillar.console**

Foundation classes for simple command-line program, that processes incoming commands line-by-line.

Simply extend <span style="color:#3BB9FF">ConsoleProcessor</span> and implement method:

	void processCommand(String command) throws Exception;

If you want to use a different *InputStream* than *System.in*, extend <span style="color:#3BB9FF">StringCommandProcessor</span>.

**pillar.exec**

Classes of pillar.exec provide long-running periodic tasks functionality.
There are 2 ways to run a periodic task.

1. Extend <span style="color:#3BB9FF">PeriodicTask</span>.

Extending PeriodicTask would mean 2 things:

a) Implementing *InternalTask.execute()* method that will contain task execution logic. Return value of this method or thrown *Exception* will be used to notify *ReportListeningTask* instances running in the same context.

	Object execute() throws Exception;
	
b) Implementing *SleepTimeProvider.getSleepTime()* method to manage delays between execution

	long getSleepTime();
	
*PeriodicTask* can be used as a self-contained context, which also supports self-reporting in case it's concrete subclass implements *ReportListeningTask*.
You can start self-contained context by calling *PeriodicTask.start(...)*, or run it as a child task in larger context of parent *ListTask*.

Please note, that depending on its configuration *ListTask* might use a different *SleepTimeProvider*.



2. Add task to list of tasks of <span style="color:#3BB9FF">ListTask</span>.

*ListTask* periodically executes a list of child tasks in sequence.
An instance of child task should implement either *Runnable* or *InternalTask*.
In case a child task implements *ReportListeningTask*, it will be supplied with execution report for all child tasks after execution of all tasks in the list.

Each report records will include a reference to child task, and either an Exception that occured during execution, or in case no Exception was thrown, an empty *Optional* for simple *Runnable*s or a return value of method *execute()* for instances of *InternalTask*.

If a child task implements both *Runnable* and *InternalTask* (like *PeriodicTask*), it will be treated as *InternalTask*.

You can start *ListTask* execution simply by calling its method *start(...)*.

**pillar.time**

Pillar.time provides an abstraction for serializable timestamps with millisecond precision.

**pillar.wait**

Pillar.wait provides *WaitStrategy* interface and various implementations of wait strategies.
It's a simplified part of retrying library https://github.com/rholder/guava-retrying.
