---
layout: post
title:  "Serving a JBake Static-Site with Spring-Boot"
date:   2018-07-13 20:26:48 -0400
categories: jekyll update
---

## Why Serve Static Content From A Spring-Boot App?
While deploying static-site content is easier through a content delivery network (CDN) such as [GitHub Pages](https://pages.github.com/) or [surge](https://surge.sh/), I wanted to take a crack at hosting it from
my own webapp.

I decided to take a static-site generated with [JBake](https://jbake.org/) and run it from a [Spring-Boot](https://spring.io/projects/spring-boot) application. JBake and Spring-Boot make an ideal combination For
three reasons:
1.  They both run on the Java Virtual Machine (JVM).
2.  Since they both run on the JVM, it's even easier to incorporate either [Maven](https://maven.apache.org/) or [Gradle](https://gradle.org/).
3.  [JBake](https://jbake.org/docs/2.6.1/#build_tool_integration) has _Maven/Gradle_ plugins for the build process.

## Project Setup
For this particular HOWTO guide, I'm using the following tools:
* [Maven](https://maven.apache.org/) as the build tool
* [Asciidoctor](https://asciidoctor.org/) to parse ```asciidoc``` content
* [Groovy Template Engine](http://docs.groovy-lang.org/latest/html/documentation/template-engines.html)

### Installation Instructions
While each tool has their own installation instructions, the quickest/easiest way is to use [SDKMAN!](https://sdkman.io/). Check out the [SDMAN! Install Docs](https://sdkman.io/install) for installation instructions for your specific platform.

#### Installing Tools With SDKMAN!
Once SDKMAN! is installed, all of the tools can be installed through the command-line-interface (CLI).

1.  Java 8 (OpenJDK): ```sdk install java```
2.  Maven: ```sdk install maven```
3.  JBake ```sdk install jbake```

A ready-to-go Spring-Boot application can be generated using [Spring Initializr](https://start.spring.io/), while _Asciidoctor_ and _Groovy Templates_ will be installed as dependencies in the Spring-Boot project's ```pom.xml```.

##### Generating A Spring-Boot App With Spring Initializr
[Spring Initializr](https://start.spring.io/) provides an easy way to generate the boilerplate needed to spin up a quick Spring-Boot application.  For this project, I left the _Group_ ```com.example``` and changed the _Artifact_ name to ```jbake-spring-boot-app```.  Once you click _Generate Project_, a ```.zip``` folder is downloaded containing the basic Spring-Boot project structure and starter ```pom.xml```.

#### Adding In _JBake_, _Asciidoctor_, And _Groovy Templates_  Dependencies To The ```pom.xml```
Modify the existing ```pom.xml``` file to include the necessary dependency entries to get the project up and running. See below:

{%highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>jbake-spring-boot-app</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>jbake-spring-boot-app</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
      <groupId>org.jbake</groupId>
      <artifactId>jbake-maven-plugin</artifactId>
      <version>0.3.1</version>
    </dependency>
    <dependency>
      <groupId>org.jbake</groupId>
      <artifactId>jbake-core</artifactId>
      <version>2.6.1</version>
    </dependency>

    <dependency>
    	<groupId>org.asciidoctor</groupId>
    	<artifactId>asciidoctorj</artifactId>
    	<version>1.5.6</version>
    </dependency>
    <dependency>
    	<groupId>org.asciidoctor</groupId>
    	<artifactId>asciidoctor-maven-plugin</artifactId>
    	<version>1.5.6</version>
    </dependency>

  <dependency>
      <groupId>org.codehaus.groovy</groupId>
      <artifactId>groovy-templates</artifactId>
      <version>2.5.0</version>
    </dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
{% endhighlight %}

#### Configuring the _JBake Plugin_
Since we're going to configure the ```pom.xml``` _JBake Plugin_, we will be able to generate the static-site content
as part of the Maven build process.

Configure the Spring-Boot ```<build></build>``` section to look like the following:

{%highlight xml %}
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
		<plugin>
			<groupId>org.jbake</groupId>
			<artifactId>jbake-maven-plugin</artifactId>
			<version>0.3.1</version>
			<executions>
				<execution>
					<id>default-generate</id>
					<phase>generate-resources</phase>
					<goals>
					<!--<goal>generate</goal>
					<goal>inline</goal> -->
					</goals>
				</execution>
			</executions>
			<configuration>
				<inputDirectory>${project.basedir}/src/main/resources/static</inputDirectory>
				<outputDirectory>${project.build.directory}/classes/static</outputDirectory>
			</configuration>
			<dependencies>
				<dependency>
					<groupId>org.asciidoctor</groupId>
					<artifactId>asciidoctorj</artifactId>
					<version>1.5.6</version>
				</dependency>
				<dependency>
						<groupId>org.codehaus.groovy</groupId>
						<artifactId>groovy-templates</artifactId>
						<version>2.5.0</version>
					</dependency>
			</dependencies>
		</plugin>
	</plugins>
</build>
{% endhighlight %}

Next create a folder according to the directory structure outlined in `<inputDirectory></inputDirectory>`, which in this case is `src/main/resources/static`. This is where the `asciidoc` content will go.

#### Installing Maven Dependencies
Run: `mvn install` to install the Maven dependencies outlined in the `pom.xml`.

#### Seeding The Default JBake Theme
Now change to the `static` directory, and run the JBake command
`jbake -i -t groovy` to initialize the boilerplate code for a static-site using _Groovy Templates_.

This will generate the appropriate JBake project structure, along with some example blog post content in the `content` directory. Since we're only working with `asciidoc` files in this project, the newly generated `2013` folder under the `src/main/resources/static/content/blog` directory can be deleted.  It's okay to leave it, the only thing is the non `asciidoc` examples will not be parsed/rendered when JBake runs.

#### Creating A Test Blog Post
From the `content/blog` directory, create an empty `asciidoc` file such as `test-blog.asciidoc`, and add the default JBake header:

```
title=Test Post
date=2018-07-13
type=post
tags=blog, asciidoc
status=published
~~~~~~
```

Next remove the comments in  the `<goals></goals` section of the `pom.xml` file like so:

```
<goal>generate</goal>
<goal>inline</goal>
```

The `generate` goal simply builds the static-site content based on the `asciidoc` files contained within the `content/blog` directory, while the `inline` goal spins up a local development web server for testing any changes.

#### Running The Application

Switch back to the root directory of the Maven project and test out the JBake plugin by running `mvn jbake:inline`, which should start a local development server on port 8080. If everything works out, this should go right to the application's landing page, which should list the newly rendered `test-blog.asciidoc`.  Running `mvn spring-boot:run` will achieve a similar result.

Now that the Spring-Boot/JBake application is up and running, we can host this on something like [Heroku](https://heroku.com).  
