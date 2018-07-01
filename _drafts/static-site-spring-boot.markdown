---
layout: post
title:  "Serving a JBake Static Site with Spring-Boot"
date:   2018-06-20 20:26:48 -0400
categories: jekyll update
---

## Why Serve Static Content From A Spring-Boot App?

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
