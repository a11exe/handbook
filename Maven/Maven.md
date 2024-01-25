# Maven

* [Clear maven cache](#clear-maven-cache)
* [External settings](#external-settings)

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