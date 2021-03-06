---
layout:     post
title:      "Introducing new GitLab Branch Source Plugin"
date:       2019-08-24 19:30:00
summary:    "Final summary for GSoC Evaluation"

categories: jenkins gitlab plugins pipeline multibranch gsoc gsoc2019
---

The GitLab Branch Source Plugin has come out of its beta stage and has been released to the Jenkins update center. It allows you to create job based on GitLab `user` or `group` or `subgroup` project(s). You can either:

* Import a single project's branches as jobs from a GitLab user/group/subgroup (Multibranch Pipeline Job)
* Import all or a subset of projects as jobs from a GitLab user/group/subgroup (GitLab Group Job or GitLab Folder Organization)

The GitLab Group project scans the projects, importing the pipeline jobs it identifies based on the criteria provided. After a project is imported, Jenkins immediately runs the jobs based on the `Jenkinsfile` pipeline script and notifies the status to GitLab Pipeline Status. This plugin unlike other Branch Source Plugins provides GitLab server configuration which can be configured in Configure System. Jenkins Configuration as Code (JCasC) can also be used to configure the server. To learn more about server configuration see my [previous blog post](https://jenkins.io/blog/2019/06/29/phase-1-multibranch-pipeline-support-for-gitlab/).

## Requirements

* Jenkins - 2.176.2 (LTS)

* GitLab - v11.0+

## Creating a Job

To create a Multibranch Pipeline Job (with GitLab branch source) or GitLab Group Job, you must have GitLab Personal Access Token added to the server configuration. The credentials is used to fetch meta data of the project(s) and to set up hooks on GitLab Server. If the token has admin access you can also set up `System Hooks` while `Web Hooks` can be set up from any user token.

## Create a Multibranch Pipeline Job:

Go to Jenkins > New Item > Multibranch Pipeline > Add Source > GitLab Project

![GitLab Project Branch Source](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/branch-source.png)

* `Server` - Select your desired GitLab server from the dropdown, needs to be configured before creating this job. 

* `Checkout Credentials` - Add credentials of type `SSHPrivateKey` or `Username/Password` if there are any private projects to be built by the plugin. If all projects are public then no checkout credentials required. Checkout credential is different from the credential (of type `GitLab Personal Access Token`) setup in GitLab server config.

* `Owner` - Can be a `user`, `group` or `subgroup`. Depending on this the `Projects` field is populated.

* `Projects` - Select the project you want to build from the dropdown.

* `Behaviours` - These traits are very powerful tool to configure the build logic and post build logic. We have defined new traits. You can see all the information in repository documentation.

Save and wait for the branches indexing. You are free to navigate from here, the job progress is displayed to the left hand side.

![Multibranch Pipeline Job Indexing](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/multibranch-indexing.png)

After the indexing, the imported project listed all the branches, merge requests and tags as jobs.

![Multibranch Pipeline Job Folder](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/multibranch-folder.png)

On visiting each job, you will find some action items on the left hand side:

* You can trigger the job manually by selecting `Build Now`.
* You can visiting the particular branch/merge request/tag on your GitLab Server by selecting the corresponding button.

![Build Actions](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/icon-tag.png)

### Create a GitLab Group Job Type

Go to Jenkins > New Item > GitLab Group

![GitLab Folder Organization](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/gitlab-group.png)

You can notice the configuration is very similar to Multibranch Pipeline Job with only `Projects` field missing. You can add all the projects inside your Owner i.e. User/Group/Subgroup. The form validation will check with your GitLab server if the owner is valid. You can add `Discover subgroup project` trait which allows you to discover this child projects of all subgroups inside a Group or Subgroup but this trait is not applicable to User. While indexing, web hook is created in each project. GitLab Api doesn't support creation of Group web hooks so this plugin doesn't support that feature which is only available in GitLab EE.

You can now explore your imported projects, configuring different settings on each of those folders if needed.

![GitLab Group Folder](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/gitlab-group-folder.png)

### GitLab Pipeline Status Notification

GitLab is notified about build status from the point of queuing of jobs.

* Success - the job was successful
* Failure - the job failed and the pull request is not ready to be merged
* Error - something unexpected happened; example: the job was aborted in Jenkins
* Pending - the job is waiting in the build queue

![GitLab Pipeline Status](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/pipeline-status.png)

On GitLab Pipeline status are hyperlinks to the corresponding Jenkins job build. To see the Pipeline Stages and the console output you will be required to visit your Jenkins server. We also planned to notify the pipeline stages to GitLab but it came with some drawbacks which has been addressed so far but there is future plan to add it as trait.

You can also skip notifying GitLab about the pipeline status by selecting `Skip pipeline status notifications` from the traits list.

### Merge Requests

Implementing support for Merge Requests for the projects was challenging. First, MRs are of 2 types i.e. Origin branches and Forked Project branches so there had to be different implementation for each head. Second, MRs from forks can be from untrusted sources, so a new strategy `Trust Members` was implemented which allows CI to build MRs only from trusted users who have accesslevel of `Developer`/`Maintainer`/`Owner`.

![Trusted Member Strategy](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/trusted-members.png)

Third, MRs from forks do not support pipeline status notification due to GitLab issue, see [this](https://docs.gitlab.com/ee/ci/merge_request_pipelines/#important-notes-about-merge-requests-from-forked-projects). You can add a trait `Log Build Status as Comment on GitLab` that allows you to add a sudo user (leave empty if you want owner user) to comment on the commit/tag/mrs the build result. To add a sudo user your token must have admin access. By default only failure/error are logged as comment but you can also enable logging of success build by ticking the checkbox.

![Build Status Comment Trait](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/log-comment-trait.png)

Sometimes, Merge Requests fail due to external errors so you want to trigger rebuild of mr by commenting `jenkins rebuild`. To enable this trigger add the trait `Trigger build on merge request comment`. The comment body can be changed in the trait. For security reasons, commentor should have `Developer`/`Maintainer`/`Owner` accesslevel in the project.

![Merge request build trigger](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/build-trigger-trait.png)

## Hooks

Web hooks are automatically created on your projects if configured to do so in server configuration. Web hooks are ensured to pass through a CSRF filter. Jenkins listens to web hooks on the path `/gitlab-webhook/post`. On GitLab web hooks are triggered on the following events:

* `Push Event` - when a commit or branch is pushed

* `Tag Event` - when a new tag is created

* `Merge Request Event` - when a merge request is created/updated

* `Note Event` - when a comment is made on a merge request

You can also set up System Hooks on your GitLab server if your token has admin access. System hooks are triggered when new projects are created, Jenkins triggers a rescan of the new project based on the configuration and sets up web hook on it. Jenkins listens to system hooks on the path `/gitlab-systemhook/post`. On GitLab system hooks are triigered on `Repository Update Events`.

You can also use `Override Hook Management mode` trait to override the default hook management and choose if you want to use a different context (say Item) or disable it altogether.

![Override Hook Management](/assets/2019-08-24-introducing-gitlab-branch-source-plugin/override-hook.png)

## Job DSL and JCasC

You can use `Job DSL` to create jobs. Here's an example of Job DSL script:

```groovy
organizationFolder('GitLab Organization Folder') {
    description("GitLab org folder created with Job DSL")
    displayName('My Project')
    // "Projects"
    organizations {
        gitLabSCMNavigator {
            projectOwner("baymac")
            credentialsId("i<3GitLab")
            serverName("gitlab-3214")
            // "Traits" ("Behaviours" in the GUI) that are "declarative-compatible"
            traits {
                subGroupProjectDiscoveryTrait() // discover projects inside subgroups
                gitLabBranchDiscovery {
                    strategyId(3) // discover all branches
                }
                originMergeRequestDiscoveryTrait {
                    strategyId(1) // discover MRs and merge them with target branch
                }
                gitLabTagDiscovery() // discover tags
            }
        }
    }
    // "Traits" ("Behaviours" in the GUI) that are NOT "declarative-compatible"
    // For some 'traits, we need to configure this stuff by hand until JobDSL handles it
    // https://issues.jenkins.io/browse/JENKINS-45504
    configure { 
        def traits = it / navigators / 'io.jenkins.plugins.gitlabbranchsource.GitLabSCMNavigator' / traits
        traits << 'io.jenkins.plugins.gitlabbranchsource.ForkMergeRequestDiscoveryTrait' {
            strategyId(2)
            trust(class: 'io.jenkins.plugins.gitlabbranchsource.ForkMergeRequestDiscoveryTrait$TrustPermission')
        }
    }
    // "Project Recognizers"
    projectFactories {
        workflowMultiBranchProjectFactory {
            scriptPath 'Jenkinsfile'
        }
    }
    // "Orphaned Item Strategy"
    orphanedItemStrategy {
        discardOldItems {
            daysToKeep(10)
            numToKeep(5)
        }
    }
    // "Scan Organization Folder Triggers" : 1 day
    // We need to configure this stuff by hand because JobDSL only allow 'periodic(int min)' for now
    triggers {
        periodicFolderTrigger {
            interval('1d')
        }
    }
}
```

You can also use `JCasC` to directly create job from a Job DSL script. For example see the plugin [repository](https://github.com/jenkinsci/gitlab-branch-source-plugin/blob/develop/README.md).

## How to talk to us about bugs or new features?

* This project uses [Jenkins JIRA](https://issues.jenkins-ci.org/) to track issues. You can file issues under [`gitlab-branch-source-plugin`](https://issues.jenkins-ci.org/issues/?jql=project+%3D+JENKINS+AND+component+%3D+gitlab-branch-source-plugin) component.

* Send your mail in the [Developer Mailing list](https://groups.google.com/forum/#!forum/jenkinsci-dev).

* Join our [Gitter channel](https://gitter.im/jenkinsci/gitlab-branch-source-plugin).

## Future work

* Actively maintain `GitLab Branch Source Plugin` and take feedbacks from users to improve the plugin's user experience.
* Extend support for GitLab Pipeline to Blueocean.

## Resources

* [GitLab API Plugin](https://github.com/jenkinsci/gitlab-api-plugin)
* [GitLab API Plugin Wiki](https://wiki.jenkins.io/display/JENKINS/GitLab+API+Plugin)
* [GitLab Branch Source Plugin](https://github.com/jenkinsci/gitlab-branch-source-plugin)
* [Project Summary](https://jenkins.io/projects/gsoc/2019/gitlab-support-for-multibranch-pipeline/)
* [GitHub Branch Source Plugin Release Blog](https://go.cloudbees.com/docs/plugins/github-branch-source/)

Thank you Jenkins and Google Summer of Code :)
