# Azure Devops CI/CD pipeline templates for UiPath Projects and Library

### The resouces section in project-pipeline.yaml and library-pipeline.yaml points to this repository. DO NOT use the UiPath template repository for anything other than testing. It may change without notice. Please fork/replicate this repository to host it yourself and make any required modifications.

## Templates and Usage

This repository contains two templates, on for projects, and one for libraries, as well as two yaml files implementing each of these templates. These templates serve as a starting point

**project-pipeline-template.yaml** is a pipeline to be used by standard UiPath projects. It will deploy to the test environement on commits to a release branch, and deploy to production, after an approval process, on commits to the master branch.

**library-pipeline-template.yaml** is a pipeline to be used by UiPath libraries. It will deploy to Test test on a commit to the master branch, as well as Production, after approval.

Approvals are not configured in these pipeline files. Instead, they reference an Azure DevOps environment (on a project, go to pipelines > environments), where you can configure approvals and even enforce that all pipelines yaml files extend one of these templates.

The also reference UiPath Orchestrators for building and deployment. Orchestrators are configured through a service connection. You will need a service connection for each instance and tenant.

If you are not hosting your UiPath repositories on github, you will also need to need to configure a service connection your git host (for examples, github).

**Creating a new pipeline**

1. Copy the coorresponding YAML file (project-pipeline.yaml for projects or library-pipeline.yaml for libraries) to the root of the project directory (containing the project.json file).
1. Optionally, rename this file if desired. Azure DevOps defaults to azure-pipelines.yml, but any name with extension yaml or yml will work.
1. Edit the YAML file. Update the variable values and the trigger. The last two section can be left alone.
1. Save, commit, and push the file.
1. In Azure DevOps, go to pipelines and create a new pipeline.
1. Find and select the repository containing your project.
1. On the Configure tab, select 'Existing Azure Pipeline YAML file'.
1. Leave the branch on 'master'. Click the dropdown for path and select the yaml file you just created. Click continue.
1. Click Run to verify the pipeline works.

## Configuration

**To add a Orchestrator Service Connection (tenant)**
1. In the Azure DevOps project, click on *Project Settings* on the bottom left of the page.
1. Click on *Service connections* under Pipelines.
1. On the top right of the page, click *New service connection*.
1. Choose *UiPath Orchestrator Connection* as the type and click next.
1. For on-premise Orchestrators, you will be using Basic Authentication with a username and password. Specify the Orchestrator URL and tenant as well.
1. Name the connection (it is a good idea to include the environment and tenant in the name) then click save.

**To add an agent to the build pool**
1. In the Azure DevOps project, click on *Project Settings* on the bottom left of the page.
1. Click on *Agent pools* under Pipelines.
1. Select or create your build pool.
1. Click on the *Agents* tab.
1. On the top right, select *New agent*.
1. Follow the instructions on screen to add an existing VM to the agent pool. It will involve running a script on the target machine.
1. If it is not installed already, install Studio on the build agent. A license is not needed for build agents.

**To change the list of approvers**
1. In Azure DevOps project, select *Environments* under *Pipelines*.
1. Select the environment you wish to edit.
1. On the top right, click on the ⋮ icon to expand the menu.
1. Select *Approvals and checks*.
1. Either select the existing approval or create a new one if none exist.
1. Add or remove approvers as needed.

## Troubleshooting

**Error on deploy: Failed to run the command: System.InvalidOperationException: Microsoft.Rest.HttpOperationException: Operation returned an invalid status code 'BadRequest'**
or
**Folder does not exist**
- Verify that the folder specified by the folderName is valid and accessible.

**##[error]Error deploying package: Error: The process 'C:\agent\_work\_tool\uipcli\1.0.7502-30726\x64\uipcli.exe' failed with exit code 1**
- Verify that the version of the package being deployed does not already exist on the target orchestrator.

**Failed to package project: Microsoft.Rest.HttpOperationException: Operation returned an invalid status code 'BadRequest'
Error packaging project: Error: The process 'C:\agent\_work\_tool\uipcli\1.0.7502-30726\x64\uipcli.exe' failed with exit code 1.**
- Verify that the account specified in the service connection has API access to the tenant/folder.
- A password reset may be required if a new account was created for the build agent.
