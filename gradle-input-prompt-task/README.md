## Sequence of Steps
### Create a directory
Create a directory and get into the directory
``` sh
$ mkdir gradle-input-prompt-task
```
```
$ cd gradle-input-prompt-task
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
* Add below new task publishToArtifactory. This task will be uploading the jar into the artifactory. 
* This task accepts the credientials of the artifactory account and uses it for invoking the artifactory REST API.
* We can have the credentials stored in the build.gradle or gradle.properties but will make the credentials exposed to everyone.
* This task can either accept it as a part of the command line args or it will prompt the user to enter the credentials if it is not passed as a command line arguments

``` vi
archivesBaseName  = 'java-api'
version = '1.0'

class PublishToArtifactoryTask extends DefaultTask {
	@Input
	String username
	@Input
	String password
	String artifactory	
	String buildDir
	
	PublishToArtifactoryTask() {
		setUsername("default")
		setPassword("default")
	}
	@TaskAction
	void build() {		
		if (username == "default") {
			BufferedReader br = new BufferedReader(new InputStreamReader(System.in))
			println "> Enter the username:"
			username = br.readLine()
		}
		if (password == "default") {
			BufferedReader br = new BufferedReader(new InputStreamReader(System.in))
			println "> Enter the password:"
			password = br.readLine()
		}
		println "Username is $username and the password is $password"
		File customFolder = new File(buildDir)
		if (customFolder.exists()) {
			// TODO: Upload the jar to the artifactory
			println "Folder exists and carrying out the operations"			
		} else {
			println "No jar present in $buildDir for deployment"
		}
	}
	
	@Option(option = 'username', description = 'Username/email account having permission to publish to artifactory')
	void setUsername(final String username) {
		this.username = username
	}
	
	@Option(option = 'password', description = 'Password of the given username/email')
	void setPassword(final String password) {
		this.password = password
	}
	
}

 
task publishToArtifactory(type: PublishToArtifactoryTask) {
	group 'Custom'			
	description 'Publishes the jar to the artifactory'		
	artifactory = 'http://localhost/artifactory/repo'	
	buildDir = 'build/libs'
	
	doFirst() {			
		
	}
}
```

### Listing down all the tasks
We have grouped this task under the custom task and when we list all the tasks we will see it under the custom

``` gradle
$ ./gradlew tasks

> Task :tasks

------------------------------------------------------------
Tasks runnable from root project
------------------------------------------------------------

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Custom tasks
------------
publishToArtifactory - Publishes the jar to the artifactory

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'gradle-input-prompt-task'.
components - Displays the components produced by root project 'gradle-input-prompt-task'. [incubating]
dependencies - Displays all dependencies declared in root project 'gradle-input-prompt-task'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gradle-input-prompt-task'.
dependentComponents - Displays the dependent components of components in root project 'gradle-input-prompt-task'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'gradle-input-prompt-task'. [incubating]
projects - Displays the sub-projects of root project 'gradle-input-prompt-task'.
properties - Displays the properties of root project 'gradle-input-prompt-task'.
tasks - Displays the tasks runnable from root project 'gradle-input-prompt-task'.

Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.

Rules
-----
Pattern: clean<TaskName>: Cleans the output files of a task.
Pattern: build<ConfigurationName>: Assembles the artifacts of a configuration.
Pattern: upload<ConfigurationName>: Assembles and uploads the artifacts belonging to a configuration.

To see all tasks and more detail, run gradlew tasks --all

To see more detail about a task, run gradlew help --task <task>

BUILD SUCCESSFUL in 2s
1 actionable task: 1 executed
```

If we want to know the usages of a task, we can use the help task. It will explain what are the parameters we need to pass when we execute it

```sh
$ ./gradlew help --task publishToArtifactory

> Task :help
Detailed task information for publishToArtifactory

Path
     :publishToArtifactory

Type
     PublishToArtifactoryTask (PublishToArtifactoryTask)

Options
     --password     Password of the given username/email

     --username     Username/email account having permission to publish to artifactory

Description
     Publishes the jar to the artifactory

Group
     Custom

BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```
### Executing the tasks
Executing the task via gradlew (gradle wrapper) gives the advantage of executing anywhere even if we do not have gradle installed

``` gradle
$ ./gradlew publishToArtifactory --username venkatesh --password abcd1234

> Task :publishToArtifactory
Username is venkatesh and the password is abcd1234
Folder exists and carrying out the operations

BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```

If we do not pass the arguments in the command line then it will prompt you to enter it

```sh
$ ./gradlew publishToArtifactory

> Task :publishToArtifactory
> Enter the username:
<--<-----<-------------> 0% EXECUTING [6s]
> Enter the password:
<---<---<-------------> 0% EXECUTING [10s]
Username is venkatesh and the password is abcd1234
Folder exists and carrying out the operations

BUILD SUCCESSFUL in 12s
1 actionable task: 1 executed
```