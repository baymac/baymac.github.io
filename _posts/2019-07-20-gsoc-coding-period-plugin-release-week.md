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

2) Fix Jenkins CI build error

Jenkins CI build on GitLab Branch Source Plugin returned the following error:
```
[2019-07-14T03:45:08.934Z] [ERROR] Make sure `git status -s` is empty before using -Dset.changelist: [docs/img/gitlab-credentials.png, docs/img/gitlab-section.png, docs/img/gitlab-server.png, docs/img/gitlab-token-creator.png, src/main/webapp/images/16x16/icon-branch.png, src/main/webapp/images/16x16/icon-commit.png, src/main/webapp/images/16x16/icon-fork.png, src/main/webapp/images/16x16/icon-gitlab.png, src/main/webapp/images/16x16/icon-merge-request.png, src/main/webapp/images/16x16/icon-project.png, src/main/webapp/images/16x16/icon-tag.png, src/main/webapp/images/24x24/icon-branch.png, src/main/webapp/images/24x24/icon-commit.png, src/main/webapp/images/24x24/icon-fork.png, src/main/webapp/images/24x24/icon-gitlab.png, src/main/webapp/images/24x24/icon-merge-request.png, src/main/webapp/images/24x24/icon-project.png, src/main/webapp/images/24x24/icon-tag.png, src/main/webapp/images/32x32/icon-branch.png, src/main/webapp/images/32x32/icon-commit.png, src/main/webapp/images/32x32/icon-fork.png, src/main/webapp/images/32x32/icon-gitlab.png, src/main/webapp/images/32x32/icon-merge-request.png, src/main/webapp/images/32x32/icon-project.png, src/main/webapp/images/32x32/icon-tag.png, src/main/webapp/images/48x48/icon-branch.png, src/main/webapp/images/48x48/icon-commit.png, src/main/webapp/images/48x48/icon-fork.png, src/main/webapp/images/48x48/icon-gitlab.png, src/main/webapp/images/48x48/icon-merge-request.png, src/main/webapp/images/48x48/icon-project.png, src/main/webapp/images/48x48/icon-tag.png] (use -Dignore.dirt to make this nonfatal) -> [Help 1]
```

For more info, see this discussion [thread](https://groups.google.com/forum/#!topic/jenkinsci-dev/Df_hT_qeINo).

Remove `webapp/` from repository and added to `.gitignore`

When the plugin is compiled, it generates the webapp images of different sizes from the SVGs. So Jenkins CI builds failed. Fixed it in [`513150c`](https://github.com/jenkinsci/gitlab-branch-source-plugin/commit/513150cf4a5f3c62c7b5143525997046cae6e671) & [`d3982fb`](https://github.com/jenkinsci/gitlab-branch-source-plugin/commit/d3982fb56905e800a0340b01618fb4afda5fe11b). 

The above assumption was false. Previously, I used `SVN Rasterizer` Maven Plugin which generate the images from SVGs. But that is not the case anymore since I removed it after adding once. Another thing is that nothing in `src/` should ever be in `.gitignore`. So the idea of adding webapp directory to `.gitignore` was scraped.

We can also ignore the error by adding a flag `-Dignore.dirt` in our `maven.config` file. But still not the best way to fix this.

There were still confusions with what caused the build to fail. I tried readding the SVGs and PNGs. In the process removed additional SVGs of Merge Request, Tags etc. Kept only logo, branch and project. Still that didn't fix the issue. 

Later, Gavin suggested maybe the `.gitattributes` caused the changes to the binary files during build. So we need to specify the binary files (by globbing) in `.gitattributes` to skip modifying. Based on this [doc](https://help.github.com/en/articles/configuring-git-to-handle-line-endings) added the following to `.gitattributes`:

```
# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```

Currently the deploy stage fails as the archives cannot be deployed incrementally. Haven't been able to figure it out yet. Raise a ticket and awaiting response.

|  Issue 	|   Pull Request	| 
|---	    |---	            |
|[JENKINS-58510](https://issues.jenkins-ci.org/browse/JENKINS-58510), [INFRA-2183](https://issues.jenkins-ci.org/browse/INFRA-2183) | [#1](https://github.com/jenkinsci/gitlab-branch-source-plugin/pull/1)
