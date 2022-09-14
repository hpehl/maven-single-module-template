# Maven Single Module Template

Template for a single module project using Maven 3.2.x and Java 11+.

# Features

- [Maven Wrapper](https://maven.apache.org/wrapper/)
- Inherits from [JBoss Parent POM](https://github.com/jboss/jboss-parent-pom)
- [WildFly](https://github.com/wildfly/wildfly-checkstyle-config) [checkstyle](https://checkstyle.sourceforge.io/) configuration
- [Maven Enforcer Plugin](https://maven.apache.org/enforcer/maven-enforcer-plugin/) with rules enforcing 
  - secure repositories over HTTPS
  - Java >=11 
  - Maven >= 3.2.5
- [Maven License Plugin](https://mycila.carbou.me/license-maven-plugin/)
- [Maven Formatter Plugin](https://code.revelc.net/formatter-maven-plugin/)
- [Maven ImpSort Plugin](https://code.revelc.net/impsort-maven-plugin/)
- Sample dependencies [Eclipse Collections](https://www.eclipse.org/collections/) and [JUnit 5](https://junit.org/junit5/)
- [SLF4J](https://www.slf4j.org/) and [Logback](https://logback.qos.ch/) support
- Empty JUnit 5 unit test
- Support for [keeping a changelog](https://keepachangelog.com/en/1.0.0/)
- Sample code of conduct and contribution guide
- Release profile which generates and signs source and JavaDoc JARs
- Deployment to Maven Central using a GitHub workflow

# Get Started

1. Clone or copy the repo
2. Adjust Maven coordinates in `pom.xml`
3. Adjust `repo.scm.connection` and `repo.scm.url` in `pom.xml`
4. Adjust organization in `pom.xml`
5. Adjust or remove unnecessary plugins/configuration in `pom.xml`
6. Add dependencies, code, and tests

If you want to keep a changelog and the contribution guide, make the following adjustments: 

- Replace the URLs at the bottom of `CHANGELOG.md`
- Replace all URLs and paths containing `maven-single-module-template` in `CONTRIBUTING.md`

# Release Management & Deployment

There are many options for how to release and deploy new versions. This project takes an opinionated approach using a bash script to kick off a new release and a GitHub workflow for doing the actual release and deploying all artifacts to Maven Central. 

The project already fulfills many requirements and recommendations for deploying to Maven Central:

- the POM contains all required metadata
- source and Javadoc JARs are produced
- all artifacts are signed
- `org.sonatype.plugins:nexus-staging-maven-plugin` is used for deployment

## Release Script

The script `release.sh` starts a new release. To adjust the script to your needs, replace the variable `WORKFLOW_URL`.

```shell
./release <release-version> <next-version>
```

Both the `release-version` and the `next-version` have to be semantic versions. 

The release script 

1. bumps the project version to `<release-version>`
2. updates the header and links in the changelog (there should already be entries made by you!)
3. commits & pushes the changes
4. creates & pushes a new tag `v<release-version>`
5. bumps to the next snapshot version `<next-version>-SNAPSHOT`
6. commits & pushes changes

By pushing the tag to GitHub, the release workflow kicks in.

## GitHub Workflow

The GitHub workflow operates fully automated and relies on several secrets that have to be configured:

- `OSSRH_USERNAME`: The username for the Sonatype JIRA
- `OSSRH_PASSWORD`: The password for the Sonatype JIRA
- `MAVEN_GPG_PASSPHRASE`: The passphrase for your private GPG key
- `MAVEN_GPG_PRIVATE_KEY`: The private key in ASCII format. You can use a command like `gpg --armor --export-secret-keys 2A79C898 | pbcopy` to export and copy the private key to the clipboard (on macOS). 

The release workflow builds and deploys the project by running 

1. `mvn install` and then
2. `mvn deploy -P release`

Upon successful execution, a new GitHub release is created. The name of the release uses the env variable `PROJECT_NAME` defined for the job `release`. Adjust this variable to your needs. The body of the release is the latest section from the changelog.    

# Scripts

This repository contains various scripts to automate tasks.

## `format.sh`

Formats the codebase by applying the following maven goals:

- [`license-maven-plugin:format`](https://mycila.carbou.me/license-maven-plugin/#goals)
- [`formatter-maven-plugin:format`](https://code.revelc.net/formatter-maven-plugin/format-mojo.html)
- [`impsort-maven-plugin:sort`](https://code.revelc.net/impsort-maven-plugin/sort-mojo.html)

The goals use the plugin configuration in [pom.xml](pom.xml) and the resources in [etc](etc).

## `validate.sh`

Validates the codebase by applying the following maven goals:

- [`enforcer:enforce`](https://maven.apache.org/enforcer/maven-enforcer-plugin/enforce-mojo.html)
- [`checkstyle:check`](https://maven.apache.org/plugins/maven-checkstyle-plugin/check-mojo.html)
- [`license-maven-plugin:check`](https://mycila.carbou.me/license-maven-plugin/#goals)
- [`formatter-maven-plugin:validate`](https://code.revelc.net/formatter-maven-plugin/validate-mojo.html)
- [`impsort-maven-plugin:check`](https://code.revelc.net/impsort-maven-plugin/check-mojo.html)

The goals use the plugin configuration in [pom.xml](pom.xml) and the resources in [etc](etc).

## `release.sh`

Starts the release (see above). 
