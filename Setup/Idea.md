# IntelliJ IDEA Settings

[#setup]

* [Increase the memory heap of the IDE](#increase-the-memory-heap-of-the-ide)
* [Disable wild card import](#disable-wild-card-import)
* [Setting JVM Arguments With Run/Debug Configurations](#setting-jvm-arguments-with-rundebug-configurations)
* [Reload all dependencies](#reload-all-dependencies)

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

### Disable wild card import
* open the IDE settings and then select Editor | Code Style | Java.
* Make sure that the Use single class import option is enabled.
* In the Class count to use import with '*' and Names count to use static import with '*' fields, 
specify values that definitely exceed the number of classes in a package and the number of names in a class 
(for example, 999).

### Setting JVM Arguments With Run/Debug Configurations
[https://www.baeldung.com/intellij-idea-set-jvm-arguments](https://www.baeldung.com/intellij-idea-set-jvm-arguments)
Let’s launch IntelliJ IDEA and open an existing project or a new one for which we’ll configure JVM arguments. We continue by clicking “Run” and selecting “Edit Configurations…”.

From there, we can create a run/debug configuration for our application by clicking the plus symbol and selecting “Application”:
We’ll add the text field for adding JVM arguments by selecting “Add VM options” in the “Modify options” dropdown and add all the required JVM arguments to the newly added text field.

VM Options example
```shell
-Xms64m -Xmx256m
```

- Memory allocation – such as -Xms (initial heap size) or -Xmx (maximum heap size)
- Garbage collection – such as -XX:+UseConcMarkSweepGC (enables the concurrent mark-sweep garbage collector) or -XX:+UseParallelGC (enables the parallel garbage collector)
- Debugging – such as -XX:+HeapDumpOnOutOfMemoryError (heap dump when an OutOfMemoryError occurs) or -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005 for remote debugging through JDWP on port 5005.
- System properties – such as -Djava.version (Java version) or -Dcustom.property=value (defines a custom property with its value).

### Reload all dependencies
Press Ctrl+Shift+A and type `Reload All .. Projects`
