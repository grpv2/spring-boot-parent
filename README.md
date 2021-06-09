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
