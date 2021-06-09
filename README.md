## spring boot template

## Requirements
* Variable **GITLAB_MAVEN_PRIVATE_TOKEN** must be defined. It should contain your [personal_access_token](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html) with api scope.
* [Disable duplicate maven artifacts](https://docs.gitlab.com/ee/user/packages/maven_repository/index.html#do-not-allow-duplicate-maven-packages) with same version if you want to avoid duplicate release versions. By default it will be enabled. You must add exception for SNAPSHOTS. Add `.*-SNAPSHOT` in exceptions for same.
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
    <developerConnection>scm:git:${CI_PROJECT_URL}.git</developerConnection>
    <tag>HEAD</tag>
</scm>
```

## Use this group's dependency in your project
This can be done in two ways:
* **Using pom.xml** 
```
<repository>
    <id>YOUR-UNIQ-ID</id>
    <url>https://gitlab.com/api/v4/projects/<gitlabProjectID>/packages/maven</url>
  </repository>
</repositories>
```
where `gitlabProjectID` is project ID for this group on gitlab and YOUR-UNIQ-ID can be ID of your choice.

* **Using settings.xml/ci_settings.xml**
1. under `repositories`
```
<repository>
    <id>YOUR-UNIQ-ID</id>
    <!-->must use group ID of gitlab group not name<-->       
    <url>https://gitlab.com/api/v4/groups/<gitlabProjectID>/-/packages/maven</url>
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
    <name>gitlab-maven-plugins</name>
    <url>https://gitlab.com/api/v4/groups/<gitlabProjectID>/-/packages/maven</url>
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

where `gitlabProjectID` is project ID for this group on gitlab and YOUR-UNIQ-ID can be ID of your choice.

For both methods you need to add server config in `<servers>` tag of `settings.xml/ci_settings.xml`.
ID must be same for repositories added in pom.xml/settings.xml.
Here is an example using CI_JOB_TOKEN.
For other options please check [here](https://docs.gitlab.com/ee/user/packages/maven_repository/index.html#authenticate-to-the-package-registry-with-maven)
```
<server>
    <id>YOUR-UNIQ-ID</id>
    <configuration>
    <httpHeaders>
        <property>
        <name>Job-Token</name>
        <value>${CI_JOB_TOKEN}</value>
        </property>
    </httpHeaders>
    </configuration>
</server>
```
**If you dont want to add repsitories reference for whole group, you can use just url for this project instead of groups in `url` tag.
[reference](https://docs.gitlab.com/ee/user/packages/maven_repository/index.html#project-level-maven-endpoint)**
