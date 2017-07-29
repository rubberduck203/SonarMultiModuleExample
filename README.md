# Sonar, Java, and multi-module Maven Example

Minimal working example project to demonstrate how to set up a java/maven project with multiple modules to send *code coverage* reports to a running [SonarQube][sonar] server.


## Getting started

This example assumes you have an existing SonarQube server running.
If you don't, the easiest way to get up and running is to use the [SonarQube docker image][docker].

```bash
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube
```

Then login to http://localhost:9000/ with admin/admin credentials.

## Project Structure

The root directory is a module itself that contains two other modules.
Module1 and Module2 contain a single class and test each.
The tests intentionally don't cover the entirety of the "production" code so we can see SonarQube at work.

All of the interesting bits are in the [parent pom.xml file in the root directory](pom.xml).

## Code Analysis and Test Coverage

Sonar can do a fair bit of static code analysis on its own, but in order to get test coverage, we need an external tool.
I'm using [Jacoco][jacoco] to generate the test coverage reports. 
Jacoco is installed in the parent pom as a plug-in. 
For this example, I've disabled all the rules that fail a build, so that we can continue on to publish the results to sonar.

I've also installed the maven-surefire plugin and registered the jacoco.Junit listener with surefire.
The `sonar` profile specifies our SonarQube server's IP Address.

### Generating and Publishing

```bash
mvn clean package && mvn sonar:sonar
```

#### Execution Flow

1. Surefire runs our tests and triggers Jacoco's test coverage analysis.
2. Jacoco places its results in the default location of `target/jacoco.exec`.
3. Running `mvn sonar:sonar` gathers up all the reports and sends them to the SonarQube server.

## Links & Resources

- [SonarQube][sonar]
- [SonarQube Docker Image][docker]
- [Sonar Maven Example](https://github.com/SonarSource/sonar-examples/tree/master/projects/languages/java/code-coverage/ut/ut-maven-jacoco)
- [Jacoco][jacoco]
- [Jacoco pom example](http://www.eclemma.org/jacoco/trunk/doc/examples/build/pom.xml)
- [Using Sonar with Jacoco](https://docs.sonarqube.org/display/PLUG/Usage+of+JaCoCo+with+Java+Plugin)
- [SonarQube Scanner for Maven](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Maven)

[sonar]: https://www.sonarqube.org/
[docker]: https://hub.docker.com/_/sonarqube/
[jacoco]: http://www.eclemma.org/jacoco/