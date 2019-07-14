---
layout:     post
title:      "GSoC coding period: The plugin release week"
date:       2019-07-20 08:09:00
summary:    "Details of work done in the eigth week on GitLab Branch Source"

categories: jenkins gsoc gitlab
---

This week we are planning to cut a release to Jenkins Update Center. This really depends on the progress we make in this week. We have some important bugs to fix before we make the release.

I have requested help from ArgelBargel and his plugin users in this [issue](https://github.com/Argelbargel/gitlab-branch-source-plugin/issues/105).

## Alpha release

I cut an alpha release which can be found [here](https://github.com/jenkinsci/gitlab-branch-source-plugin/releases/tag/gitlab-branch-source-v0.0.4-SNAPSHOT).

## Issues

1) Web Hooks support:

Currently I am try to add Web Hooks support to the plugin. Webhooks registration on GitLab Server for `Navigator` and `Source` is working

![Screenshot from 2019-07-13 17-09-05](https://user-images.githubusercontent.com/23079344/61171176-8fa05080-a591-11e9-91af-3beb13519ed0.png)

Although we have a few issues here:

* Unable to listen to post request on Jenkins instance. Any idea how to implement this?

* GitLab API doesn't support webhooks creation on a group. Although Group webhooks are available in Enterprise Edition but cannot take advantage of it. Currently it is a WIP in GitLab. So for Navigator, individually setting webhooks on all projects (so it takes a longer period to process).

|  Issue 	|   Pull Request	| 
|---	    |---	            |
|[JENKINS-57760](https://issues.jenkins-ci.org/browse/JENKINS-57760) | [#25](https://github.com/baymac/gitlab-branch-source-plugin/pull/25)

2) Remove webapp/ from repository and added to .gitignore

When the plugin is compiled, it generates the webapp images of different sizes from the SVGs. So Jenkins CI builds failed. Fixed it in [`513150c`](https://github.com/jenkinsci/gitlab-branch-source-plugin/commit/513150cf4a5f3c62c7b5143525997046cae6e671) & [`d3982fb`](https://github.com/jenkinsci/gitlab-branch-source-plugin/commit/d3982fb56905e800a0340b01618fb4afda5fe11b).


