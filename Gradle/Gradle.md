# Gradle

* [Fat jar](#fat-jar)
* [Custom task](#custom-task)
* [Test task properties](#test-task-properties)
* [Run a Java main Method](#run-a-java-main-method)
* [Skip tests](#skip-tests)

## Fat jar
Basically, a fat jar (also known as uber-jar) is a self-sufficient archive which contains both classes and dependencies needed to run an application.

Let’s start with modifying the jar task from the Java Gradle plugin. By default, this task produces jars without any dependencies.
We can overwrite this behavior by adding a few lines of code. We need two things to make it work:
```
jar {
    manifest {
        attributes "Main-Class": "com.baeldung.fatjar.Application"
    }

    from {
        configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
    }
}
```

If we want to leave the original jar task as it is, we can create a separate one which will do the same job.

The following code will add a new task called customFatJar:
```
task customFatJar(type: Jar) {
    manifest {
        attributes 'Main-Class': 'com.baeldung.fatjar.Application'
    }
    archiveBaseName = 'all-in-one-jar'
    duplicatesStrategy = DuplicatesStrategy.EXCLUDE
    from { configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) } }
    with jar
}
```

## Custom task
[Custom task](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

For example copy SpringBoot properties file outside jar:
```
task copyProps (dependsOn: build, type: Copy) {
	from "src/main/resources/application.yml"
	into "build/libs/."
}
```

## Test task properties
[Test task](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.testing.Test.html)

## Run a Java main Method
[Full article](https://www.baeldung.com/gradle-run-java-main)
Let's create simple class
```
public class MainClass {
    public static void main(String[] args) {
        System.out.println("Goodbye cruel world ...");
    }
}
```
Next, let’s implement a custom task for running the main method with the help of the JavaExec task type:
```
task runWithJavaExec(type: JavaExec) {
    group = "Execution"
    description = "Run the main class with JavaExecTask"
    classpath = sourceSets.main.runtimeClasspath
    main = javaMainClass
}
```

## Skip tests
```shell
gradle build -x test
```