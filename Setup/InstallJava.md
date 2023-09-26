# Install Java

[#setup]

The easiest JDK to install is [Eclipse's Adoptium project](https://adoptium.net/) (formerly AdoptOpenJDK).

Validate `JAVA_HOME` on Windows
To echo the `JAVA_HOME` variable in the command prompt, issue the following command:

```
C:\>echo %JAVA_HOME%
C:\_tools\jdk-17\
```

PowerShell command to echo JAVA_HOME
The command to echo `JAVA_HOME` in Windows Powershell is as follows:

```
PS C:\> echo $env:JAVA_HOME
C:\_tools\jdk-17\
```

Confirm the Java `PATH` configuration
After validation of `JAVA_HOME`, Java’s `PATH` setting should be confirmed.

As with `JAVA_HOME`, the Windows `PATH` setting can be echoed at the command line. Issue the following command to view the PATH in a command prompt:

```
C:\>echo %PATH%
C:\_tools\jdk-17\bin; C:\WINDOWS\system32; C:\WINDOWS;
```

The following commands outputs the Windows `PATH` in PowerShell:
```
PS C:\> echo $env:PATH
C:\_tools\jdk-17\bin;  C:\WINDOWS\system32;  C:\WINDOWS;
```

If the full path to the bin folder of the JDK install is listed, then the Windows PATH for Java has been configured correctly.

### Run Java on Windows
The final step to confirm the Java install on Windows was a success is to actually run a Java command and see if it works.

The easiest way to do this is to ask the JDK which version of Java is installed. In PowerShell it looks like this:

```
PS C:\> java -version
openjdk version "17.0.3" 2022-04-19
OpenJDK Runtime Environment Temurin-17.0.3+7 (build 17.0.3+7)
OpenJDK 64-Bit Server VM Temurin-17.0.3+7 (build 17.0.3+7, mixed mode, sharing)
```

As you can see, we have successfully setup and installed Java 17 on Windows.