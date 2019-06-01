---
layout:     post
title:      "The beginning of coding period"
date:       2019-05-01 17:10:00
summary:    "Things accomplished in the first week of coding period"

categories: jenkins gsoc 
---

On Monday, May 27, the Google Summer of Code 2019 Coding Period started. Our team had been regularly doing sync ups to plan how to carry out the project successfully in the next 3 months. We do 2 meetings each week that motivates me to keep working throughout the week. The project workflow had been agreed upon. Jenkins community is immune to outside noise and everyone is very professional. It has been an exciting month since this is my first Google Summer of Code. I had fairly less experience in `Java` or `Object Oriented Design` per se but I wanted a new challenge. For the past 2 months I have been learning the Java 8 and its design pattern. I would highly recommend the following books:

1. `Java 8 in Action` by Raoul, Mario and Alan
2. `Core Java for Impatient` by Cay S. Horstmann
3. `Effective Java` by Joshua Bloch

Before actually buying these, a friend actually gifted this to me. A shout out to my friend Shibasis Pattnaik.

## Requirement 

We use JIRA `scrum boards`, `sprints` and `epics` to track our progress. Other resources include `mailing list` and `gitter channels`. For meetings, we use `Zoom` because it supports upto 100 users, has clients for desktop, browser, phone etc. Jenkins project page for gsoc 2019 is also live which give an overall idea of what's new.

## Community Bonding Work

Before the start of the coding period, I finished some of the nit things that will be helpful for our project. One such thing was the `GitLab API Plugin`. In Jenkins it is recommended to wrap any API inside a new plugin so that all the plugins depend on the same version of API. The API releases can be controlled by plugin owner himself. Basically it abstracts the API release testing for CVEs etc in one place rather than doing manually in each dependent plugin.

The problem with existing GitLab Plugin is that it doesn't have a separate API plugin and defines the API inside itself. This cause following problems:

1. Dedicated effort to maintain an API for GitLab would required which is different from the scope of our plugin goals

2. Other plugins might not be able to reuse the GitLab Java APIs defined inside GitLab Plugin. Even if they extend this plugin, they will also be inheriting excess functionality and that is not a great idea.

3. Keeping the APIs inside the plugin makes the codebase bigger. Plugins should be lightweight and limited to their functionality. 

This has been correctly implemented by GitHub Plugins in Jenkins. We plan to follow the similar convention of 3 SCM plugins:

1. GitHub API Plugin - Wraps GitHub Java API 

2. GitHub Plugin - Build Trigger and Webhook Management

3. GitHub Branch Source Plugin - To support Multibranch Pipeline Jobs and Folder organisation

So, I wanted to implement a Java API for GitLab or maybe extend one. But that would have taken a lot of time as Oleg Nenashev suggested. To our good luck, I found a well maintained GitLab Java API repository by gmessner. This guy has been doing excellent work to maintain the codebase:

1. Responds to Issues within 24 hours

2. Listen to user's requirements 

3. Releases frequent fixes and improvements

4. Implements things that are not part of GitLab API inherently e.g. creating `Personal Access Token` from Username/Password credentials.

This is the benefit of Open source that you get to reuse the work of others. So wrapping that API was fairly simple. Although we faced some problems with Maven because there were some dependencies that were targeting Java 9 and our Java level in Maven was set to Java 8. For this reason, `Maven Enforcer Plugin` caused an error during builds. After having discussions with Mentors and GitLab API owner, we decided to skip this enforcing check by adding the following to the POM:

```html
...
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-enforcer-plugin</artifactId>
                <version>3.0.0-M1</version>
                <executions>
                    <execution>
                        <id>enforce-bytecode-version</id>
                        <goals>
                            <goal>enforce</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <enforceBytecodeVersion>
                                    <maxJdkVersion>1.8</maxJdkVersion>
                                    <ignoreClasses>
                                        <ignoreClass>module-info</ignoreClass>
                                    </ignoreClasses>
                                </enforceBytecodeVersion>
                            </rules>
                        </configuration>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.codehaus.mojo</groupId>
                        <artifactId>extra-enforcer-rules</artifactId>
                        <version>1.0-beta-9</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </builds>
...
```




