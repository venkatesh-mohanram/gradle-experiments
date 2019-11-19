## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-buildxsd-task
```
```
$ cd gradle-buildxsd-task
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
* Add below new tasks (buildXSD and copyXSD). Along with that introduce two new properties called archivesBaseName and version.
* The buildXSD is a JavaExec task and it uses apache XmlBeans to create the JAXB classes from the xsd file. 
* The copyXSD task is used to copy the jar into build/libs folder

``` vi
archivesBaseName  = 'jaxb-api'
version = '1.0'

configurations {
    xmlbean
}

dependencies {
    xmlbean group: 'org.apache.xmlbeans', name: 'xmlbeans', version: '2.5.0'
}

task buildXSD(type: JavaExec) {
    classpath configurations.xmlbean
    main = "org.apache.xmlbeans.impl.tool.SchemaCompiler"
    args "-debug", "src/xsd"
}

task copyXSD(type: Copy) {
	dependsOn buildXSD
	from(file("xmltypes.jar")) {
		rename ({
			return "${archivesBaseName}-${version}.jar"
		})
	}
	into ("build/libs")
	doLast {
		println "Copied the jar into build/libs folder"
	}
}

build.dependsOn copyXSD
```

### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew build

> Task :buildXSD
Time to build schema type system: 0.483 seconds
Time to generate code: 0.094 seconds
Time to compile code: 1.355 seconds
Compiled types to: xmltypes.jar

> Task :copyXSD
Copied the jar into build/libs folder

BUILD SUCCESSFUL in 5s
6 actionable tasks: 2 executed, 4 up-to-date
```
