## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-cutom-tasks
```
```
$ cd gradle-cutom-tasks
```
### Initialize a Gradle project
Executing 'gradle init' will initialize an empty project with all the necessary files

``` gradle
$ gradle init

> Task :init
Get more help with your project: https://guides.gradle.org/creating-new-gradle-builds

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```
### Listing the available tasks
Running the below command will list all the available tasks in the build.gradle file
``` gradle
$ ./gradlew tasks
```
### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew custom

> Configure project :
customParent:Initialization
custom:Initialization

> Task :customParent
customParent:doFirst
CustomParentTask:build() World
customParent:doLast

> Task :custom
custom:doFirst hello World
CustomTask:build()
custom:doLast

BUILD SUCCESSFUL in 2s
2 actionable tasks: 2 executed

```
