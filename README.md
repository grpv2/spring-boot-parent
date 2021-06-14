## spring boot template

## Requirements
* Variable **SVC_GITHUB_USER** must be defined. This user will be used to deploy maven artifact, read from maven repo and to do git operations in mvn release plugin.
* For your maven release plugin, make sure you configure default commit message with `[skip ci]`. This is to avoid chain of builds due to commits done by plugin.
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-release-plugin</artifactId>
    <version>${plugin.release.version}</version>
    <configuration>
        <scmCommentPrefix>[ci skip] [maven-release-plugin]</scmCommentPrefix>
    </configuration>
</plugin>
```
* scm connection must be defined in pom for mvn release
```
<scm>
    <developerConnection>scm:git:${GITHUB_SERVER_URL}/${GITHUB_REPOSITORY}.git</developerConnection>
    <tag>HEAD</tag>
</scm>
```

## Use this group's dependency in your project
This can be done in two ways:
* **Using pom.xml** 
```
<repository>
    <id>YOUR-UNIQ-ID</id>
    <url>https://maven.pkg.github.com/<githubGroupID>/*</url>
  </repository>
</repositories>
```
where `githubGroupID` is name for this group and YOUR-UNIQ-ID can be ID of your choice.

* **Using settings.xml/ci_settings.xml**
1. under `repositories`
```
<repository>
    <id>YOUR-UNIQ-ID</id>
    <!-->must use group ID of github group<-->       
    <url>https://maven.pkg.github.com/<githubGroupID>/*</url>
    <layout>default</layout>
    <releases>
        <enabled>true</enabled>
        <updatePolicy>daily</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
    </releases>
    <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
    </snapshots>
</repository>
```
2. under `pluginRepositories`
```
<pluginRepository>
    <id>YOUR-UNIQ-ID</id> 
    <name>github-maven-plugins</name>
    <url>https://maven.pkg.github.com/<githubGroupID>/*</url>
    <layout>default</layout>
    <releases>
        <enabled>true</enabled>
        <updatePolicy>daily</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
    </releases>
    <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
        <checksumPolicy>fail</checksumPolicy>
    </snapshots>
    </pluginRepository>
```

where `githubGroupID` is name for this group and YOUR-UNIQ-ID can be ID of your choice.

For both methods you need to add server config in `<servers>` tag of `settings.xml/ci_settings.xml`.
ID must be same for repositories added in pom.xml/settings.xml.
Here is an example.
```
<server>
      <id>YOUR-UNIQ-ID</id>
      <username>${env.maven_access_user}</username>
      <password>${env.maven_access_token}</password>
</server>
```
**If you dont want to add repsitories reference for whole group, you can use just url for this repository instead of groups in `url` tag.
