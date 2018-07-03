---
layout: post
title:  "Serving a JBake Static-Site with Spring-Boot"
date:   2018-07-03 20:26:48 -0400
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

A ready-to-go Spring-Boot application can be generated using [Spring Initializr](https://start.spring.io/), while _Asciidoctor_ and _Groovy Templates_ will be installed as dependencies in the Spring-Boot project's ```pom.xml```.

##### Generating A Spring-Boot App With Spring Initializr
[Spring Initializr](https://start.spring.io/) provides an easy way to generate the boilerplate needed to spin up a quick Spring-Boot application.  For this project, I left the _Group_ ```com.example``` and changed the _Artifact_ name to ```jbake-spring-boot-app```.  Once you click _Generate Project_, a ```.zip``` folder is downloaded containing the basic Spring-Boot project structure and starter ```pom.xml```.

#### Adding In _JBake_, _Asciidoctor_, And _Groovy Templates_ To The ```pom.xml```

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

### Starter ```pom.xml```

{%highlight xml %}

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.sample-jbake-spring-boot</groupId>
  <artifactId>jbake-spring-boot</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <name>jbake-spring-boot-example</name>
  <!-- FIXME change it to the project's website -->
  <!-- <url>http://www.example.com</url> -->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.3.RELEASE</version>
  </parent>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
    <junit.jupiter.version>5.2.0</junit.jupiter.version>
    <junit.platform.version>1.2.0</junit.platform.version>
  </properties>

  <dependencies>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.0.3.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <version>2.0.3.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <version>2.0.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-launcher</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-runner</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-surefire-provider</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-engine</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-console</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-suite-api</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.platform</groupId>
      <artifactId>junit-platform-console-standalone</artifactId>
      <version>1.2.0</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-engine</artifactId>
      <version>5.2.0</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-params</artifactId>
      <version>5.2.0</version>
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

<!-- TEMPLATE ENGINE -->
  <!-- https://mvnrepository.com/artifact/org.codehaus.groovy/groovy-templates -->
  <dependency>
      <groupId>org.codehaus.groovy</groupId>
      <artifactId>groovy-templates</artifactId>
      <version>2.5.0</version>
    </dependency>

</dependencies>

  <build>
    <plugins>

      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.7.0</version>
        <configuration>
          <source>${java.version}</source>
          <target>${java.version}</target>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.22.0</version>
        <dependencies>
          <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-surefire-provider</artifactId>
            <version>${junit.platform.version}</version>
          </dependency>
        </dependencies>
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
              <goal>generate</goal>
              <!-- <goal>seed</goal> -->
              <!-- <goal>inline</goal> -->
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
</project>
{% endhighlight %}

#### A Note On the Commented-Out Goals: ```seed``` and ```inline```
