$ mkdir gradle-cutom-tasks

$ cd gradle-cutom-tasks

$ gradle init

> Task :init
Get more help with your project: https://guides.gradle.org/creating-new-gradle-builds

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed

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

