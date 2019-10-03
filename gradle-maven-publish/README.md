## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-maven-publish
```
```
$ cd gradle-maven-publish
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
* Add the maven-publish plugin in the top
* Add the below publishing task

``` vi
archivesBaseName  = 'java-api'
version = '1.0'

publishing {
		publications {
			maven(MavenPublication) {
				from(components.java)
			}
		}
		repositories {
			maven {
				name = 'myRepo'
				url = 'http://maven-repo'
				// Keep your credentials in ~/.gradle/gradle.properties
				credentials {
					username = 'artifactory.username'
					password = 'artifactory.password'
				}
			}
		}
	}
```

### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew publish
```
