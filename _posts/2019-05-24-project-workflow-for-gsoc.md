---
layout:     post
title:      "Project Workflow for GSoC"
date:       2019-05-23 13:48:00
summary:    "Details of development workflow setup for Google Summer of Code (GSoC) project under Jenkins"

categories: jira scrumboards git jenkins git-flow
---

For my GSoC project, I will be developing plugins for Jenkins Community. We required two things:

1. Track objectives and issues in one place
2. Manage code reviews and plugin releases

### Issue Tracker

For the first problem, we started using `GitHub Project boards` for objectives and `GitHub issues` for issues. Project boards were a bit clumsy so we preferred using a Google Doc instead. For issues, the GitHub issues are just okay but we felt keeping discussions and codes separate is a better idea.

For a good reason, Jenkins community hosts it's own [JIRA server](https://issues.jenkins-ci.org) where this can be managed quite well. JIRA issues can be more verbose while being separate from the code itself. JIRA issues can take various forms, namely:

1. `Bug` - For filing bug in the code
2. `Epic` - For categorising issues in one place
3. `Task` - For creating a task for the future
4. `Story` - For stories about a user's experience with product or feature
5. `New Feature` - For adding a new feature
6. `Improvement` - For improving an existing feature

There are multiple other configurations that can help organising issues, to learn more see [here](https://issues.jenkins-ci.org/secure/ShowConstantsHelp.jspa?decorator=popup#IssueTypes).

JIRA also provides support for project boards to track progress in the form of scrum boards or kanban boards. Scrum boards are basically like trello that lets use divide a virtual board into various sections such as TODO, In Progress, In Review, Done etc. and place issues, which take the form of cards, in the stage depending on their progress. Also allows you to create sprints which are bounded by time. More about it later. Kanban boards are also similar but with higher level of customisation that can lead to ambiguity in case of small team projects. It comes down to your preference. You can learn about the differences [here](https://www.atlassian.com/agile/kanban/kanban-vs-scrum).

For our project, we decided to use:

1. `JIRA Issues` - These can exist in all possible forms (task, story, bug, improvement etc) based on the requirement

2. `EPIC` - To organise issues of each GSoC phases in separate categories. So we created four EPICs, namely,

    * `Phase 0: Community Bonding` - [link](https://issues.jenkins-ci.org/browse/JENKINS-57541)
    * `Phase 1: Multibranch Pipeline Support For GitLab` - [link](https://issues.jenkins-ci.org/browse/JENKINS-57445)
    * `Phase 2: Multibranch Pipeline Support For GitLab` - [link](https://issues.jenkins-ci.org/browse/JENKINS-57540)
    * `Phase 3: Multibranch Pipeline Support For GitLab` - [link](https://issues.jenkins-ci.org/browse/JENKINS-57538)
<br/><br/>
3. `Scrum Board` and `Sprints` - To keep a track of our progress by both mentors and students.

    A scrum was created with the name:
    
    * `Google Summer of Code 2019` - [link](https://issues.jenkins-ci.org/secure/RapidBoard.jspa?rapidView=591)<br/><br/>
  
    Four sprints were created to track the issues of all GSoC 2019 projects under Jenkins, namely

    * `GSoC 2019. Community Bonding`
    * `GSoC 2019. Code Phase 1`
    * `GSoC 2019. Code Phase 2`
    * `GSoC 2019. Code Phase 3`

JIRA Scrum boards comes with another section called `Backlog` which has a list of features, defects, experiment, enhancement etc that needs to be done in the future which is not part of a time bound sprint. 

![Backlog Section](/assets/2019-05-24-project-workflow-for-gsoc/backlog-scrum-board.png)
`The Backlog Section of Scrum Board`

![Active Sprints Section](/assets/2019-05-24-project-workflow-for-gsoc/active-sprint-scrum-board.png)
`The Active Sprints Section of Scrum Board`

To add issues to the sprints you can either add the desired sprint's link at the time of issue creation or simply drag and drop from the backlog.

There are also other sections that can be helpful like releases, tests, components etc.

### Git Workflow



