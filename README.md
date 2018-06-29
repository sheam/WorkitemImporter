# WorkitemImporter

## What is this project?

This is a simple project to help migrate Jira tickets to VSTS as workitems. Provide a list of JQL to be sync'd to VSTS, it will automatically find the Sprints and Epics associated with the issues and sync these too. _Note: Sub-tasks should be sync'd after the other issue-types are processed._

## What does this fork change?
Basically the original project is geared towards projects which use sprints (Scrum). We work more in a more Kanban fashion where we just continually pull from the backlog as needed and add them to our board. For a release, in Jira, we would create a 'release', and assign all the done tasks to it. Our board query removed items from the board that were done, and belonged to a 'release' that was released. VSTS actually works better for this, as every release gets it's own board. So when we do a release, we just create a new iteration and start working off that iterations board.

So in my fork, I ignored the 'sprint' field, and created an iteration for every release/version in Jira and linked the work items added to that iteration. I also enhanced it a bit in the sense that I took the dates from each Jira release, and applied them to the iteration. I think I found a few other field too which you can see from my commits. Then I ran the tool. I ran it a number of times until I got it how I wanted it, as it does a proper upsert by matching issue title, so as long as you don't change the formula for work item title you can keep tweaking and running it.

## Required

* Visual Studio 2017
* .NET Framework 4.7

## Usage

After cloning the source and building let's leverage the command-line. 

```
-vsts-url=[company-name] or [https://company-name.visualstudio.com]
-vsts-project=[vsts-project-name]
-vsts-pat=[vsts-personal-access-token] 
-jira-url=[company-name] or [https://company-name.atlassian.net] 
-jira-user=[jira-username] 
-jira-password=[jira-password] 
-jira-project=[jira-project]
-preview
```

Extra configuration via the `appSettings` in the `app.config`.

```
<!-- List of Jira queries to iterate and sync. Queries can be commented out. Epics are automatically identified and created -->
<add key="Jira.Queries" value="
  //project = 'projectname' and status not in (done) and type = epic and sprint is empty
  project = 'projectname' and status not in (done) and type not in (epic, subtask)
" />
```

```
<!-- Map key/value pairs from Jira to VSTS for Priority -->
<add key="Jira.Map.Priority" value="
  P1, 1
  P2, 2
  P3, 3
  P4, 4
  P5, 4
  Critical, 1
  Major, 2
  Minor, 3
  Trivial, 4
" />
```

``` 
<!-- Map key/value pairs from Jira to VSTS for Status -->
<add key="Jira.Map.Status" value="
  To do, New
  In Progress, Active
  Dev Complete, Active
  In Testing, Active
  Done, Resolved
" />
```

```
<!-- Map key/value pairs from Jira to VSTS for Status -->
<add key="Jira.Map.Type" value="
  Story, User Story
  Epic, Feature
  Bug, Bug
  Sub-task, Task
  Task, Task
" />
```

```
<!-- Map key/value pairs from Jira to VSTS for Users -->
<add key="Jira.Map.Users" value="
  username, email@address.com
" />
```
