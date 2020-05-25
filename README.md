# Hello Maven
This is a basic Maven configuration and gradually adds each functionality (dependency, remote repository etc.) per each commit.

## How to use this with JFrog Artifactory
From commit 85f00b903eee3e8089ff9dc6ef66f4456046f965,

1. Build w/o JFrog Artifactory and check all dependencies are downloaded from https://repo.maven.apache.org/ and https://repo.spring.io/
```
$ mvn -U dependency:purge-local-repository clean package
```
2. Create the following Maven repos on Artifactory
    - 1 x Local Repository
        - Repository Key: maven-local
        - Check "Enable Indexing In Xray"
    - 1 x Remote Repository
        - Repository Key: maven-remote
        - URL: https://jcenter.bintray.com (default)
        - Check "Enable Indexing In Xray"
    - 1 x Virtual Repository
        - Repository Key: maven
        - Repositories: maven-local, maven-remote
        - Default Deployment Repository: maven-local
3. Go to "maven" -> Set Me Up -> Type password & Press "Generate Maven Settings" -> Fill all entries (Releases, Snapshots, Plugin Releases, Plugin Snapshots) with "maven" -> "Generate Settings" -> Download Snippet (settings.xml)
4. Build with JFrog Artifactory and check dependencies are downloaded from Artifactory and (still) https://repo.spring.io/
```
$ mvn -U -s settings.xml dependency:purge-local-repository clean package
```
5. Create a Remote Repository as follows
    - Repository Key: spring-snapshot
    - URL: https://repo.spring.io/libs-snapshot
    - Uncheck "Handle Releases"
    - Check "Enable Indexing In Xray"
6. Add the avobe remote repo to the "maven" virtual repo
7. Comment out `<repositories></repositories>` in pom.xml
8. Run maven to resolve all the dependencies through Artifactory
```
$ mvn -U -s settings.xml dependency:purge-local-repository clean package
```
9. Check repos on Artifactory. If you would like to filter a remote repository use the following Include Patterns
- for "maven-remote", Include Patterns: "org/apache/**"
- for "spring-snapshot", Include Patterns: "org/springframework/**"
10. Go to "maven" -> Set Me Up -> Copy a snippet in "Deploy" (like the following) to pom.xml
```xml
<distributionManagement>
    <repository>
        <id>central</id>
        <name>ip-10-0-1-168-releases</name>
        <url>http://artifactory1.tsuyo.org:8081/artifactory/maven</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <name>ip-10-0-1-168-snapshots</name>
        <url>http://artifactory1.tsuyo.org:8081/artifactory/maven</url>
    </snapshotRepository>
</distributionManagement>
```
11. Run maven to deploy your artifacts to Artifactory
```
$ mvn -U -s settings.xml dependency:purge-local-repository clean deploy
```
