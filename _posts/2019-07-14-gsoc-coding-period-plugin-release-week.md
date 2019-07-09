---
layout:     post
title:      "GSoC coding period: The plugin release week"
date:       2019-07-14 19:15:00
summary:    "Details of work done in the seventh week on GitLab Branch Source"

categories: jenkins gsoc gitlab
---

This week the branch source part of the plugin has taken some shape, a few more important bugs were fixed and features were added. GitLab Branch Source Plugin was also hosted on `jenkinsci` GitHub organisation.

## Issues Fixed:

1) Request to host our plugin on Jenkins official GitHub organisation:

To initiate the process to move our plugin into `jenkinsci` org, I raised a ticket. There is a conflict due existence of another plugin with the same name but we are expecting it to be fixed as the old plugin is not maintained and also not released to Jenkins update centre.

|  Issue 	|
|---	    |
|[HOSTING-795](https://issues.jenkins-ci.org/browse/HOSTING-795) |

2) Fixed issues in GitLab Group job type

There were couple of bug fixes in this pr:

i. `GitLabSCMNavigator` now takes `serverName` instead of `serverUrl`.

ii. `GitLabSCMBuilder` also takes `serverName` to create a `GitLabSCMSource` object.

iii. In `GitLabSCMNavigator` the `retrieve(c, o, e, l)` method was called for branch indexing directly. Since the `gitlabProject` was fetched in `retrieveActions(e, l)`, it was giving null pointer exception. So refetched the GitLab project:

```java
if(gitlabProject == null) {
    gitlabProject = gitLabApi.getProjectApi().getProject(projectOwner+"/"+project);
}
```

iv. There is another issue that I am trying to fix here is that default SCM Traits are not getting populated for `GitLab Group` Job type. See pr for more info.

|  Issue 	|   Pull Request	| 
|---	    |---	            |
|[JENKINS-58318](https://issues.jenkins-ci.org/browse/JENKINS-58318) | [#21](https://github.com/baymac/gitlab-branch-source-plugin/pull/21)

3) Added Build Status Notifier feature

Added Impl of `GitLabBuildStatusNotifier` class that contains a method `sendNotifications` which communicates with the GitLab Server to publish the commit status (either `SUCCESS`, `FAILED`, `PENDING` etc.). There are 3 other extensions:

`JobCompletedListener`, `JobCheckOutListener` - that call `sendNotification` method based on the build status 

`JobScheduledListener` - that sends PENDING status notification to GitLabServer.

|  Issue 	|   Pull Request	| 
|---	    |---	            |
|[JENKINS-58400](https://issues.jenkins-ci.org/browse/JENKINS-58400) | [#22](https://github.com/baymac/gitlab-branch-source-plugin/pull/22)
