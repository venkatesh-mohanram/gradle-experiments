## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-copy-tasks
```
```
$ cd gradle-copy-tasks
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
Add below new tasks (deriveDynamicVersion and buildWithVersion). Along with that introduce two new properties called archivesBaseName and version.
The DeriveDynamicVersionTask can have your own logic how you want to derive the dynamic version of the JAR, it could be based on semVer, or based on timestamp, or a sequence number etc. 
And then comes the import task buildWithVersion which is of type 'Copy' and copies the jar from build/libs folder into build/custom folder, when it copies it renames the jar name as well with dynamic value. This task is dependent on the build task and the above deriveDynamicVersion task

``` vi
archivesBaseName  = 'java-api'
version = '1.0'

class DeriveDynamicVersionTask extends DefaultTask {
	String dVersion
	String codeVersion
	
	@TaskAction
	void build() {
		// Have you own logic of deriving the dynamic version for the jar
		// One option is to use the look into the number of commits and add it as a third field
		dVersion = codeVersion.concat('.3')
		println "Building with code version $dVersion"
	}
}
task deriveDynamicVersion(type: DeriveDynamicVersionTask) {
	group 'Custom'
	description 'Derives the new version what should be used based on the number of commits'		
	dVersion 	 
	codeVersion 	
	
	doFirst {		
		codeVersion = version
	}
	
	doLast {
		// Assign the dynamically generated version number
		deriveDynamicVersion.ext.dynamicVersion = dVersion	
		println "Dynamic version is $deriveDynamicVersion.ext.dynamicVersion"
	}
}

task buildWithVersion(type: Copy) {
	group 'Custom'
	description 'Assembles with the version based on the number of commits'				
	dependsOn build
	dependsOn deriveDynamicVersion
	
	// Copying the jar and rename it with dynamic value
	from (file("$buildDir/libs/${archivesBaseName}-${version}.jar")) {
		rename ({
			String newJar = "${archivesBaseName}-${deriveDynamicVersion.dynamicVersion}.jar"
			return newJar
		})
	}	
	into ("$buildDir/custom")	
	doLast {		
		println "Renaming the files with dynamic versions"
	}
}
```

### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew buildWithVersion



```
