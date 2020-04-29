# Hello Maven
This is a basic Maven configuration and gradually adds each functionality (dependency, remote repository etc.) per each commit.

## How to use this with JFrog Artifactory
From commit a90c12702f0d550fd391b069be54ec171ce03ad9,

1. Build w/o JFrog Artifactory and check all dependencies are downloaded from https://repo.maven.apache.org/ and https://repo.spring.io/
```
$ mvn -U dependency:purge-local-repository clean package
```
2. Create basic Maven repos using Quick Setup (or manually) on Artifactory
    -  2 local repos (libs-release-local (check "Handle Releases" only), libs-snapshot-local (check "Handle Snapshots" only)) 
    -  1 remote repo (jcenter)
    -  2 virtual repos (libs-release with [libs-release-local, jcenter], libs-snapshot with [libs-snapshot-local, jcenter])
3. Go to "libs-snapshot" -> Set Me Up -> Type password & Press "Generate Maven Settings" -> "Generate Settings" -> Download Snippet (settings.xml)
4. Build with JFrog Artifactory and check dependencies are downloaded from Artifactory and (still) https://repo.spring.io/
```
$ mvn -U -s settings.xml dependency:purge-local-repository clean package
```
5. Create a remote repo (e.g. "spring-snapshot") on Artifactory and migrate a repo (within `<repositories></repositories>`) in pom.xml to this remote repo
6. Add the remote repo above to the "libs-snaphost" virtual repo
7. Comment out `<repositories></repositories>` in pom.xml
8. Run maven to resolve all the dependencies through Artifactory
```
$ mvn -U -s settings.xml dependency:purge-local-repository clean package
```
9. Check repos on Artifactory. If you would like to filter a remote repository use the following Include Patterns
- for "jcenter", Include Patterns: "org/apache/**"
- for "spring-snapshot", Include Patterns: "org/springframework/**"
10. Go to "libs-snapshot" -> Set Me Up -> Copy a snippet in "Deploy" (like the following) to pom.xml
```xml
<distributionManagement>
    <snapshotRepository>
        <id>snapshots</id>
        <name>a7a0459832f7-snapshots</name>
        <url>http://localhost:8081/artifactory/libs-snapshot</url>
    </snapshotRepository>
</distributionManagement>
```
11. Run maven to deploy your artifacts to Artifactory
```$ mvn -U -s settings.xml dependency:purge-local-repository clean deploy```
