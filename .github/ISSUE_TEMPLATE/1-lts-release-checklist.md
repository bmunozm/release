---
name: "🚤 New LTS release checklist"
labels: lts
about: Track work required for a new LTS release
---

# Next LTS release

## Release Lead

<!-- 
The release lead is the person who makes sure that all steps are completed
Not necessarily the person doing all the work

This role should rotate between LTS releases
-->

@<github-username of release lead>

## Prep work

- [ ] LTS baseline discussed and selected in the [Jenkins developers mailing list](https://groups.google.com/g/jenkinsci-dev)

- [ ] Create or update release branch in [jenkinsci/jenkins](https://github.com/jenkinsci/jenkins), e.g. `stable-2.277`, use the [init-lts-line](https://github.com/jenkins-infra/backend-commit-history-parser/blob/master/bin/init-lts-line) script

- [ ] Create or update release branch in [jenkins-infra/release](https://github.com/jenkins-infra/release), e.g. `stable-2.277`

- [ ] Create or update release branch in [jenkinsci/packaging](https://github.com/jenkinsci/packaging), e.g. `stable-2.277`

- [ ] Create pull request to update [bom](https://github.com/jenkinsci/bom) to the weekly version that will be the base of the release line (strike this out for new point release)

- [ ] Create pull request to update [configuration-as-code integration tests](https://github.com/jenkinsci/configuration-as-code-plugin/blob/master/integrations/pom.xml) to the weekly version that will be the base of the release line (strike this out for new point release)

- [ ] Review Jira and GitHub pull requests for additional LTS candidates, adding the 'lts-candidate' label, and ensure that all tickets are resolved in jira

- [ ] Backporting announcement email - [generate-backporting-announcement script](https://github.com/jenkins-infra/backend-commit-history-parser/blob/master/bin/generate-backporting-announcement)

- [ ] Update jira labels with the selected issues, e.g. `2.277.2-fixed`, `2.277.2-rejected`

- [ ] Backport changes, create a local branch in jenkinsci/jenkins, run the [list-issue-commits script](https://github.com/jenkins-infra/backend-commit-history-parser/blob/master/bin/list-issue-commits) to locate commits via jira ID, some manual work is required to locate them if the issue ID wasn't present at merge time, backport with `git cherry-pick -x $commit`

- [ ] Open backporting PR with into-lts label and summary of changes in description from [lts-candidate-stats script](https://github.com/jenkins-infra/backend-commit-history-parser/blob/master/bin/lts-candidate-stats) and the selected [Jira lts-candidates](https://issues.jenkins-ci.org/issues/?filter=12146)

- [ ] Review ATH, bom and configuration-as-code integration tests results

- [ ] Prepare [LTS changelog](https://www.jenkins.io/changelog-stable/) based on the [style guide](https://github.com/jenkins-infra/jenkins.io/blob/master/content/_data/changelogs/_STYLEGUIDE.adoc) using the [changelog generator](https://github.com/jenkinsci/core-changelog-generator/blob/master/README.md)

- [ ] Prepare [LTS upgrade guide](https://www.jenkins.io/doc/upgrade-guide/) based on [previous upgrade guides](https://github.com/jenkins-infra/jenkins.io/tree/master/content/_data/upgrades)

## RC creation

- [ ] Merge backporting PR in jenkinci/jenkins using a merge commit (do not squash)

- [ ] Retrieve the url for the RC from the commit status (continuous-integration/jenkins/incrementals) of the last build on the stable branch (requires a passing build). You will get something like https://repo.jenkins-ci.org/incrementals/org/jenkins-ci/main/cli/2.303.2-rc31385.6eec6c02fc3d/, replace `cli` with `jenkins-war`, visit the page and copy the URL to the war file, for the earlier example it would be https://repo.jenkins-ci.org/incrementals/org/jenkins-ci/main/jenkins-war/2.303.2-rc31385.6eec6c02fc3d/jenkins-war-2.303.2-rc31385.6eec6c02fc3d.war. If incrementals are broken you can deploy a build from your own machine with `mvn -e clean deploy -DskipTests=true`.

- [ ] Publish a pre-release [Github release](https://github.com/jenkinsci/jenkins/releases), e.g. [sample](https://github.com/jenkinsci/jenkins/releases/tag/jenkins-2.289.2-rc) currently we don't have a changelog for RCs

- [ ] Send announcement email, [example](https://groups.google.com/g/jenkinsci-dev/c/ox6SCyOQLuE/m/C-dsLZ4vBwAJ)

- [ ] Check with security team that no security update is planned.  If a security update is planned, revise the checklist after the public pre-announcement to the [jenkinsci-advisories mailing list](https://groups.google.com/g/jenkinsci-advisories)

## LTS release

- [ ] Publish changelog (one day prior to the release in case of a security update)

- [ ] Assure that contents of master branch have been merged to the stable branch in the [release repository](https://github.com/jenkins-infra/release), e.g. `stable-2.277`

- [ ] Assure that contents of master branch have been merged to the stable branch in the [packaging repository](https://github.com/jenkinsci/packaging), e.g. `stable-2.277`

- [ ] Run job on [release.ci.jenkins.io](https://release.ci.jenkins.io/blue/organizations/jenkins/core%2Fstable%2Frelease/branches/)

- [ ] Check [LTS changelog](https://www.jenkins.io/changelog-stable/) is visible on the downloads site

- [ ] Publish [GitHub release](https://github.com/jenkinsci/jenkins/releases) pointing to LTS changelog

- [ ] Confirm [Datadog checks](https://p.datadoghq.com/sb/0Igb9a-e6849e5e019250ef5aaea3589297fe8b) are passing

- [ ] Confirm the [Debian installer acceptance test](https://ci.jenkins.io/job/Infra/job/acceptance-tests/job/install-lts-debian-package/) is passing

- [ ] Confirm the [Red Hat installer acceptance test](https://ci.jenkins.io/job/Infra/job/acceptance-tests/job/install-lts-redhat-rpm/) is passing

- [ ] Adjust state of [Jira issues](https://issues.jenkins.io/) fixed in the release (see the [changelog](https://www.jenkins.io/changelog-stable) for issue links) and remove the `lts-candidate` label from [Jira issues resolved in the release](https://issues.jenkins.io/issues/?jql=labels%20%3D%20lts-candidate%20and%20status%20in%20(closed%2C%20resolved)%20ORDER%20BY%20status%20DESC%2C%20key%20ASC)

- [ ] Create pull request to update [bom](https://github.com/jenkinsci/bom) to the newly released version

- [ ] Create pull request to update [configuration-as-code integration tests](https://github.com/jenkinsci/configuration-as-code-plugin/blob/master/integrations/pom.xml) to the newly released version

- [ ] Run trusted.ci.jenkins.io [Docker image creation job](https://trusted.ci.jenkins.io:1443/job/Containers/job/Core%20Release%20Containers/job/master/)

- [ ] [Update ci.jenkins.io](https://github.com/jenkins-infra/runbooks/tree/master/ci#upgrading-jenkins) to the new LTS release (note: repo is private, requires infra team membership)
