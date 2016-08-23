Due to different inception dates of individual Spring Data modules, most of them carry different major and minor version numbers. The easiest way to find compatible ones is by relying on the Spring Data Release Train BOM we ship with the compatible versions defined. In a Maven project you’d declare this dependency in the <dependencyManagement />section of your POM:

Example 1. Using the Spring Data release train BOM

<dependencyManagement> <dependencies> <dependency> <groupId>org.springframework.data</groupId> <artifactId>spring-data-releasetrain</artifactId> <version>${release-train}</version> <scope>import</scope> <type>pom</type> </dependency> </dependencies> </dependencyManagement>

The current release train version is Hopper-SR2. The train names are ascending alphabetically and currently available ones are listed here. The version name follows the following pattern: ${name}-${release} where release can be one of the following:

BUILD-SNAPSHOT - current snapshots

M1, M2 etc. - milestones

RC1, RC2 etc. - release candidates

RELEASE - GA release

SR1, SR2 etc. - service releases

A working example of using the BOMs can be found in our Spring Data examples repository. If that’s in place declare the Spring Data modules you’d like to use without a version in the <dependencies /> block.

Example 2. Declaring a dependency to a Spring Data module

<dependencies> <dependency> <groupId>org.springframework.data</groupId> <artifactId>spring-data-jpa</artifactId> </dependency> <dependencies>

3.1. Dependency management with Spring Boot

Spring Boot already selects a very recent version of Spring Data modules for you. In case you want to upgrade to a newer version nonetheless, simply configure the property spring-data-releasetrain.version to the train name and iteration you’d like to use.

3.2. Spring Framework

The current version of Spring Data modules require Spring Framework in version 4.2.6.RELEASE or better. The modules might also work with an older bugfix version of that minor version. However, using the most recent version within that generation is highly recommended.
