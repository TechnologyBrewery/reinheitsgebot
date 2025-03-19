# Reinheitsgebot #
[![Maven Central](https://img.shields.io/maven-central/v/org.technologybrewery.reinheitsgebot/reinheitsgebot.svg)](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22org.technologybrewery.reinheitsgebot%22%20AND%20a%3A%22reinheitsgdsebot%22)
[![License](https://img.shields.io/github/license/mashape/apistatus.svg)](https://opensource.org/licenses/mit)

In German history, Reinheitsgebot (pronounced RINE-HITES-KUH-BOWT) was a ["purity order"](https://en.wikipedia.org/wiki/Reinheitsgebot) regulating the ingredients that
could be used to make beer. Here, it is an extension to Maven Enforcer Plugin that
extends the standard ability to ban dependencies by checking dependency management in
addition to standard dependencies (that is using this rule simultaneously handles
banning regular and dependency management dependencies).

## Using Reinheitsgebot ##

### Adding to your Maven project ###
The following example highlights both the basic configuration of the Maven
Enforcer Plugin and how to layer Reinheitsgebot into it:
```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-enforcer-plugin</artifactId>
            <version>${version.enforcer}</version>
            <configuration>
                <rules>
                    <!-- TODO: ADD THIS RULE FOR BANNING IN DEPENDENCY MANAGEMENT: -->
                    <bannedDependenciesAndDependencyManagementDepenendencies implementation="org.technologybrewery.reinheitsgebot.BannedDependenciesIncludingDependencyManagementRule">
                    </bannedDependenciesAndDependencyManagementDepenendencies>
                </rules>
            </configuration>
            <executions>
                <execution>
                    <!-- TODO: ADD THIS RULE FOR BANNING IN DEPENDENCY MANAGEMENT: -->
                    <id>enforce-banned-dependencies-and-dependency-management-dependencies</id>
                    <goals>
                        <goal>enforce</goal>
                    </goals>
                    <configuration>
                        <rules>
                            <bannedDependenciesAndDependencyManagementDepenendencies>
                                <excludes>
                                    <!-- TODO: ADD GAV INFO AS YOU WOULD FOR NORMAL BANNED DEPENDENCIES: -->
                                    <exclude>org.technologybrewery.fermenter.stout:stout-java</exclude>
                                    ...
                                </excludes>
                            </bannedDependenciesAndDependencyManagementDepenendencies>
                        </rules>
                    </configuration>
                </execution>
            </executions>
            <dependencies>
                <!-- TODO: ADD THIS PROJECT'S DEPENDENCY FOR RULE RESOLUTION: -->
                <dependency>
                    <groupId>org.technologybrewery.reinheitsgebot</groupId>
                    <artifactId>reinheitsgebot-rules</artifactId>
                    <!-- TODO: UPDATE TO LATEST VERSION! -->
                    <version>2.0.0-SNAPSHOT</version>
                </dependency>
            </dependencies>
        </plugin>
        ...
    </plugins>
</build>            
```

*Example Output*

Simply building this project with a standard `mvn clean install` will
produce example output in the `reinheitsgebot-test` module.  It should look
like the following - please note the `(via dependencyManagement)` addition to
warning output when the banned dependency exists in dependency management:
```
[INFO] --- maven-enforcer-plugin:3.1.0:enforce (enforce-banned-dependencies-and-dependency-management-dependencies) @ reinheitsgebot-test ---
[WARNING] Rule 0: org.technologybrewery.reinheitsgebot.BannedDependenciesIncludingDependencyManagementRule failed with message:
Typically, you would like fail the build - but we just want to see warning here
Found Banned Dependency (via dependencyManagement): org.technologybrewery.fermenter.stout:stout-java:jar:1.0.0
Found Banned Dependency: org.technologybrewery.krausening:krausening:jar:10
Use 'mvn dependency:tree' to locate the source of the banned dependencies.
```

# Releasing to Maven Central Repository

Reinheitsgebot uses both the `maven-release-plugin` and the `nexus-staging-maven-plugin` to facilitate the release and deployment of new Reinheitsgebot builds. In order to perform a release, you must:

1.) Obtain a [JIRA](https://issues.sonatype.org/secure/Dashboard.jspa) account with Sonatype OSSRH and access to the `org.technologybrewery` project group

2.) Ensure that your Sonatype OSSRH JIRA account credentials are specified in your `settings.xml`:

```
#!xml
<settings>
  <servers>
    <server>
      <id>ossrh</id>
      <username>your-jira-id</username>
      <password>your-jira-pwd</password>
    </server>
  </servers>
</settings>
```

3.) Install `gpg` and distribute your key pair - see [here](http://central.sonatype.org/pages/working-with-pgp-signatures.html).  OS X users may need to execute:

```
#!bash
export GPG_TTY=`tty`;
```

4.) Execute `mvn release:clean release:prepare`, answer the prompts for the versions and tags, and perform `mvn release:perform`

## Licensing
Fermenter is available under the [MIT License](http://opensource.org/licenses/mit-license.php).