# Hello Maven
This is a basic Maven configuration and gradually adds each functionality (dependency, remote repository etc.) per each commit.

## How to use this with JFrog Artifactory
From commit a90c12702f0d550fd391b069be54ec171ce03ad9,
1. Create basic Maven repos using Quick Setup on Artifactory
    -  2 local repos (libs-release-local, libs-snapshot-local) 
    -  1 remote repo (jcenter)
    -  2 virtual repos (libs-release, libs-snapshot)
2. Create a remote repo (e.g. "spring-snapshot") on Artifactory and migrate a repo (within `<repositories></repositories>`) in pom.xml to this remote repo
3. Add the remote repo above to the "libs-snaphost" virtual repo
4. Go to "libs-snapshot" -> Set Me Up -> Type password & Press "Generate Maven Settings" -> "Generate Settings" -> Download Snippet (settings.xml)
5. Comment out `<repositories></repositories>` in pom.xml
6. Run maven to resolve all the dependencies through Artifactory
```$ mvn -s settings.xml dependency:purge-local-repository clean package```
7. Go to "libs-snapshot" -> Set Me Up -> Copy a snippet in "Deploy" (like the following) to pom.xml
```xml
<distributionManagement>
    <snapshotRepository>
        <id>snapshots</id>
        <name>a7a0459832f7-snapshots</name>
        <url>http://localhost:8081/artifactory/libs-snapshot</url>
    </snapshotRepository>
</distributionManagement>
```
8. Run maven to deploy your artifacts to Artifactory
```$ mvn -s settings.xml dependency:purge-local-repository clean deploy```
