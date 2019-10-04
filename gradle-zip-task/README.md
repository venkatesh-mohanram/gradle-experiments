## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-zip-task
```
```
$ cd gradle-zip-task
```
### Initialize a Gradle project
Executing 'gradle init' will initialize an empty project with all the necessary files

``` gradle
$ gradle init --type java-library

> Task :init
Get more help with your project: https://guides.gradle.org/creating-new-gradle-builds

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```
### Add new tasks in the build.gradle
``` sh
vi build.gradle
```
* Add below new tasks packageSource. Along with that introduce two new properties called archivesBaseName and version.
* This new task uses the Gradle's zip task and zips the source folder. 
* We can execute the packageSource separately 
* We have added build.dependsOn to ensure that packageSource excutes whenever build happens
* If we need to change the name of the zip file to have a dynamic name then we should copy task followed by zip task

``` vi
archivesBaseName  = 'java-api'
version = '1.0'

/*
 *  This task is used to package the resource folder in ZIP format
 */
task packageSource(type: Zip) {
    archiveFileName = "${archivesBaseName}-source-${version}.zip"	    
    destinationDirectory = file("$buildDir/libs")
    from "src"
}

build.dependsOn packageSource
```

### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew packageSource

> Configure project :
Packaging the source folder as zip

BUILD SUCCESSFUL in 1s
1 actionable task: 1 up-to-date

$ ./gradlew build

> Configure project :
Packaging the source folder as zip

BUILD SUCCESSFUL in 17s
5 actionable tasks: 4 executed, 1 up-to-date

$ ls build/libs/java-api-
java-api-1.0.jar         java-api-source-1.0.zip

```
