# IntelliJ IDEA Settings

[#setup]

* [Increase the memory heap of the IDE](#increase-the-memory-heap-of-the-ide)

### Increase the memory heap of the IDE
The Java Virtual Machine (JVM) running IntelliJ IDEA allocates some predefined amount of memory. 
The default value depends on the platform. If you are experiencing slowdowns, you may want to increase the memory heap.

The heap size allocated for running the IDE is not the same as the heap size for compiling your application. 
If you want to configure the heap size for the build process 
that compiles your code, `open Settings, select Build, Execution, Deployment | Compiler`, 
and specify the necessary amount of memory in the Shared build process heap size field.

`Go to Help | Change Memory Settings.`
Set the necessary amount of memory that you want to allocate and click Save and Restart.
This action changes the value of the -Xmx option used by the JVM to run IntelliJ IDEA. 
Restart IntelliJ IDEA for the new setting to take effect.