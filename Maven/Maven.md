# Maven

* [Clear maven cache](#clear-maven-cache)
* [External settings](#external-settings)
* [Build specific module](#build-specific-module)
* [Skip tests](#skip-tests)
* [Offline mode](#offline-mode)
* [Debug build](#debug-build)

## Clear maven cache
Our local Maven repositories are stored in different locations based on the operating system. Also, as the .m2 directory is likely to be hidden, we’ll need to change the directory properties in order to display it.

In Windows, the default location is:
```
C:\Users\<user_name>\.m2
```
And on the Mac:
```
/Users/<user_name>/.m2
```
Linux:
```
/home/<user_name>/.m2
```

## External settings
IntelljIdea -> Settings -> Maven -> Use settings file

## Build specific module
```shell
$ mvn clean install -pl module1,module2 -am
```
or exclude
```shell
$ mvn clean install -pl '!module1, !module2'
```

## Skip tests
The Maven `-DskipTests` option skips the test execution while still compiling the test folder:
```shell
$ mvn clean install -DskipTests
```
Additionally, we can skip the test execution and not even compile them by using the `-Dmaven.test.skip=true` option:
```shell
$ mvn clean install -Dmaven.test.skip=true
```

## Offline mode
Maven makes several round trips to the server during a build, for instance, for dependency resolution and plugin download. 
In particular, it will check for new snapshot updates every time. We can avoid that by going into offline mode:
```shell
$ mvn clean install -o
```

## Debug build
[How to Speed Up Maven Build](https://www.baeldung.com/maven-fast-build)
