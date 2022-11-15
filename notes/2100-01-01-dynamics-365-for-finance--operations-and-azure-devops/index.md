# Dynamics 365 for Finance Operations and Azure DevOps


[](https://ariste.info/en/msdyn365-azure-devops-alm/#dynamics-365-for-finance-operations-and-azure-devops)**Dynamics 365 for Finance & Operations and Azure DevOps**
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

### 0.1.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-devops)Azure DevOps

[Azure DevOps](https://dev.azure.com/) will be the service we will use for source control. Microsoft Dynamics 365 for Finance and Operations supports TFVC out of the box as its version-control system.

But Azure DevOps does not only offer a source control tool. Of course, developers will be the most benefited of using it, but from project management to the functional team and customers, everybody can be involved in using Azure DevOps. [BPM](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lifecycle-services/bpm-overview?WT.mc_id=BA-MVP-5003976) synchronization and task creation, team planning, source control, automated builds and [releases](https://ariste.info/en/2019/02/setting-up-release-pipeline-in-azure-devops-for-dynamics-365-for-finance-and-operations/), are some of the tools it offers. All these changes will need some learning from the team, but in the short-term all of this will help to better manage implementations.

As I said it looks like the technical team is the most affected by the addition of source control to Visual Studio, but it’s the most benefited too…

### 0.1.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#first-steps)First steps

To use all the features described in this guide we need to create an Azure DevOps project and connect it to LCS. This will be the first step and it’s mandatory so let’s see how we have to do everything.

#### 0.1.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-an-azure-devops-organization)Create an Azure DevOps organization

You might or might not have to do this. If you or your customer already have an account, you can use it and just create a new project in it. Otherwise head to [https://dev.azure.com](https://dev.azure.com/) and create a new organization:

![Azure DevOps sign up](https://static.ariste.info/wp-content/uploads/2020/05/AzureDevOpsSign.png.webp "MSDyn365 & Azure DevOps ALM 2")

Azure DevOps sign up

After creating it you need to create a new project with the following options:

![Create Azure DevOps project](https://static.ariste.info/wp-content/uploads/2020/05/ProjType.png.webp "MSDyn365 & Azure DevOps ALM 3")

Create Azure DevOps project

Press the “Create project” button and you’re done. Now let’s connect this Azure DevOps project to our LCS project.

When a customer signs up for Finance and Operations the LCS project is of type “Implementation project” is created automatically. Your customers need to invite you to their project. If you’re an ISV you can use the “Migrate, create solutions, and learn” projects.

In any of both cases you need to go to “Project settings” and select the “Visual Studio Team Services” Tab. Scroll down and you should see two fields. Fill the field with your DevOps URL without the project part. If you got a https://dev.azure.com/YOUR\_ORG URL type you need to change it to https://YOUR\_ORG.visualstudio.com:

![Azure DevOps setup on LCS](https://i0.wp.com/ariste.info/wp-content/uploads/2020/05/LCS1.png?fit=1024%2C462&ssl=1 "MSDyn365 & Azure DevOps ALM 4")

Azure DevOps setup on LCS

And to get the “Personal access token” we go back to our Azure DevOps project, click on the user settings icon, and then select “Personal access tokens”:

![MSDyn365 & Azure DevOps ALM 1](https://static.ariste.info/wp-content/uploads/2020/05/Azure-DevOps-personal-token.png.webp "MSDyn365 & Azure DevOps ALM 5")

We add a new token, set its expiration and give it full access. Finally press the “Create” button and a new dialog will appear with your token, copy it, and paste it in LCS.

![Azure DevOps personal token](https://static.ariste.info/wp-content/uploads/2020/05/Azure-DevOps-personal-token-2.png.webp "MSDyn365 & Azure DevOps ALM 6")

![Azure DevOps personal token](https://static.ariste.info/wp-content/uploads/2020/05/Azure-DevOps-personal-token-3.png.webp "MSDyn365 & Azure DevOps ALM 7")

Back to LCS, once you’ve pasted the token press the “Continue” button. On the next step just select your project, press “Continue” and finally “Save” on the last step.

If you have any problem you can take a look at the docs where everything is really well [documented](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lifecycle-services/support-experience?WT.mc_id=BA-MVP-5003976#configure-lcs).

### 0.1.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#the-build-server)The build server

Once we’ve linked LCS and Azure DevOps we’ll have to deploy the [build server](https://ariste.info/en/2019/02/unresponsive-builds-in-azure-devops/). This will be the heart of our CI/CD processes.

Even though the build virtual machine has the same topology as a developer box, it really isn’t a developer VM and should never be used as one, do not use it as a developer VM! It has Visual Studio installed in it, the AosService folder with all the standard packages and SQL Server with an AxDB, just like all other developer machines, but that’s not its purpose.

We won’t be using any of those features. The “heart” of the build machine is the build agent, an application which Azure DevOps uses to execute the build definition’s tasks from Azure DevOps.

We can also use Azure hosted build agents. Azure hosted agents allow us to run a build without a VM, the pipeline runs on Azure. We’ll see this later.

#### 0.1.3.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#the-build-vm)The build VM

This VM is usually the dev box on Microsoft’s subscription but you can also use a regular cloud-hosted environment as a build VM.

When this VM is deployed there’s two things happening: the basic source code structure and the default build definition are created.

![MSDyn365 & Azure DevOps ALM 2](https://static.ariste.info/wp-content/uploads/2020/05/Azure-DevOps-source-control.png.webp "MSDyn365 & Azure DevOps ALM 8")

![MSDyn365 & Azure DevOps ALM 3](https://static.ariste.info/wp-content/uploads/2020/05/Azure-DevOps-default-build-pipeline.png.webp "MSDyn365 & Azure DevOps ALM 9")

### 0.1.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#visual-studio)Visual Studio

We have the basics to start working. Log into your dev VM and start Visual Studio, we must map the Main folder to the development machine’s packages folder. Open the team explorer and select “Connect to a Project…”:

![MSDyn365 & Azure DevOps ALM 4](https://static.ariste.info/wp-content/uploads/2020/05/Visual-Studio.png.webp "MSDyn365 & Azure DevOps ALM 10")

It will ask for your credentials and then show all projects available with the account you’ve used. Select the project we have created in the steps earlier and click on “Connect”:

![MSDyn365 & Azure DevOps ALM 5](https://static.ariste.info/wp-content/uploads/2020/05/Visual-Studio-connect-DevOps.png.webp "MSDyn365 & Azure DevOps ALM 11")

Now open the “Source Control Explorer”, select the Main folder and click on the “Not mapped” text:

![MSDyn365 & Azure DevOps ALM 6](https://static.ariste.info/wp-content/uploads/2020/05/Map.png.webp "MSDyn365 & Azure DevOps ALM 12")

Map the Main folder to the K:\\AosService\\PackagesLocalDirectory folder on your service drive (this could be drive C if you’re using a local VM instead of a cloud-hosted environment):

![MSDyn365 & Azure DevOps ALM 7](https://static.ariste.info/wp-content/uploads/2020/05/Map-to-PackagesLocalDirectory.png.webp "MSDyn365 & Azure DevOps ALM 13")

What we’ve done in this step is telling Visual Studio that what’s in our Azure DevOps project, inside the Main folder, will go into the K:\\AosService\\PackagesLocalDirectory folder of our development VM.

The Main folder we have in our source control tree is a regular folder, but we can [convert it into a branch](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/branch-folders-files?view=azure-devops&WT.mc_id=DOP-MVP-5003976#convert-a-folder-to-a-branch) if we need it.

![MSDyn365 & Azure DevOps ALM 8](https://static.ariste.info/wp-content/uploads/2020/05/2020-05-16-12_00_27.png.webp "MSDyn365 & Azure DevOps ALM 14")

![MSDyn365 & Azure DevOps ALM 9](https://static.ariste.info/wp-content/uploads/2020/05/2020-05-16-12_00_45.png.webp "MSDyn365 & Azure DevOps ALM 15")

In the image above, you can see the icon for Main changes when it’s converted to a branch. Branches allow us to perform some actions that aren’t available to folders. Some differences can be seen in the context menu:

![Menú contextual carpeta](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-11-14_13_11-ie-dev01dev.northeurope.cloudapp.azure_.com_61466-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 16")

Folder context menu

![Menú contextual rama](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-11-14_13_28-ie-dev01dev.northeurope.cloudapp.azure_.com_61466-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 17")

Branch context menu

For instance, branches can display the hierarchy of all the project branches (in this case it’s only Main and Dev so it’s quite simple).

![Jerarquía de las ramas](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-11-14_14_11-ie-dev01dev.northeurope.cloudapp.azure_.com_61466-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 18")

Properties dialogs are different too. The folder one:

![MSDyn365 & Azure DevOps ALM 10](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-11-14_10_06-ie-dev01dev.northeurope.cloudapp.azure_.com_61466-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 19")

And the branch one, where we can see the different relationships between the other branches created from Main:

![Propiedades de la rama](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-11-14_09_23-ie-dev01dev.northeurope.cloudapp.azure_.com_61466-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 20")

This might be not that interesting or useful, but one of the things converting a folder into a branch is seeing where has a changeset been merge into.

### 0.1.5. [](https://ariste.info/en/msdyn365-azure-devops-alm/#some-advice)Some advice

I strongly recommend moving the Projects folder out of the Main branch (or whatever you call it) into the root of the project, at the same level as BuildProcessTemplates and Trunk. In fact, and this is my personal preference, I would keep anything that’s not code outside of a branch. By doing this you only need to take care of the code when merging and branching.

Those who have been working with AX for several years were used to not use version-control systems. MSDyn365FO has taken us to uncharted territory, so it is not uncommon for different teams to work in different ways, depending on their experience and what they’ve found in the path. Each team will need to invest some time to discover what’s better for them regarding code, branching and methodologies. Many times, this will be based on experimentation and trial and error, and with the pace of implementation projects trial and error turns out bad.

### 0.1.6. [](https://ariste.info/en/msdyn365-azure-devops-alm/#branching-strategies)Branching strategies

I want to make it clear in advance: I’m not an expert in managing code nor Azure DevOps, at all. All that I’ve written here is product of my experience (good and bad) of over 4 years working with Finance and Operations. In this [article on branching strategies from the docs](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops&WT.mc_id=DOP-MVP-5003976) there’s more information regarding branching and links to articles of the DevOps team. And there’s even more info in the [DevOps Rangers’ Library of tooling and guidance solutions](http://aka.ms/vsarsolutions)!

#### 0.1.6.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#main-release)Main-Release

One possible strategy is using a Main and a Release branch. We have already learnt that the Main branch is created when the Build VM is deployed. The usual is that in an implementation project all development will be done on that branch until the Go Live, and just before that a new Release branch will be created.

We will keep development work on the Main branch, and when that passes validation, we’ll move it to Release. This branching strategy is really simple and will keep us mostly worry-free.

#### 0.1.6.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#dev-main-release)Dev – Main – Release

This strategy is similar to the Main – Release one but includes a Dev branch for each developer. This dev branch must be maintained by the developer using it. He can do as many check-ins as he wants during a development, and when it’s done merge all these changes to the Main branch in a single changeset. Of course, this adds some _bureaucracy_ because we also need to forward integrate changes from Main into our Dev branch, but it will allow us to have a cleaner list of changesets when merging them from Main to the Release branch.

Whatever branching strategy you choose try to avoid having pending changesets to be merged for a long time. The amount of merge conflicts that will appear is directly proportional to the time the changeset has been waiting to be merged.

I wrote all of this based on my experience. It’s obviously not the same working for an ISV than for an implementation partner. An ISV has different needs, it must maintain different code versions to support all their customers and they don’t necessarily need to work in a Main – Release manner. They could have one (or more) branch for each version. However, since the end of overlayering this is not necessary. More ideas about this can be found in the article linked at the beginning.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-pipelines)Azure Pipelines
------------------------------------------------------------------------------------

### 0.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#builds)Builds

We’ve already seen that the default build definition has all the default steps active. We can disable (or remove) all the steps we’re not going to use. For example, the testing steps can be removed if we have no unit testing. We can also create new build definitions from scratch, however it’s easier to clone the default one and modify it to other branches or needs.

Since version 8.1 all the X++ hotfixes are gone, the updates are applied in a single deployable package as binaries. This implies that the source-controlled Metadata folder will only contain our custom packages and models, no standard packages anymore.

### 0.2.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#continuous-integration)Continuous Integration

Continuous Integration (CI) is the process of automating the build and testing of code every time a team member commits changes to version control. ([source](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-continuous-integration?WT.mc_id=DOP-MVP-5003976))

Should your project/team use CI? Yes, yes, yes. This is one of the key feature of using an automated build process.

This is how a build definition for CI that will only compile our codebase looks like:

![Definicion build continua](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-20-15_06_43-AXZ-Dev-Continuous-Build-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 21")

Only the prepare and build steps. Then we need to go to the “Triggers” tab and enable the CI option:

![DevOps continuous integration](https://static.ariste.info/wp-content/uploads/2019/02/2019-02-20-15_07_54-AXZ-Dev-Continuous-Build-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 22")

Right after each developer check-in, a build will be queued, and the code compiled. In case there’s a compilation error we’ll be notified about it. Of course, we all build the solutions before checking them in and don’t need this CI build. Right?

![tysonjaja](./MSDyn365 & Azure DevOps ALM - ariste.info_files/tysonjaja.gif "MSDyn365 & Azure DevOps ALM 23")

![tysonjaja](https://static.ariste.info/wp-content/uploads/2019/02/tysonjaja.gif "MSDyn365 & Azure DevOps ALM 23")

And because we all know that “Slow and steady wins the race”, but at some point during a project that’s not possible, so this kind of build definition can help us out. Especially when merging code between branches. This will allow us to be 100% sure when creating a DP to release to production that it’ll work. I can tell you that having to do a release to prod in a hurry and seeing the Main build failing is not nice.

### 0.2.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#gated-check-ins)Gated check-ins

A gated check-in is a bit different than a CI build. The gated check-in will trigger an automated build BEFORE checking-in the code. If it fails, the changeset is not cheked-in until the errors are fixed and checked-in again.

This option might seem perfect for the merge check-ins to the Main branch. I’ve found some issues trying to use it, for example:

*   If multiple merges & check-ins from the same development are done and the first fails but the second doesn’t, you’ll still have pending merges to be done. You can try batching the builds, but I haven’t tried that.
*   Issues with error notifications and pending code on dev VMs.
*   If many check-ins are made, you’ll end up with lots of queued builds (and we only have one available agent per DevOps project). This can also be solved using the “Batch changes while a build is in progress”.

I think the CI option is working perfectly to validate code. As I’ve already said several times, choose the strategy that better suits your team and your needs. Experiment with CI and Gated check-in builds and decide what is better for you.

### 0.2.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#set-up-the-new-azure-devops-tasks-for-packaging-and-model-versioning)Set up the new Azure DevOps tasks for Packaging and Model Versioning

Almost all the tasks of the default build definition use PowerShell scripts that run on the Build VM. We can change 3 of those steps for newer tasks. In order to use these newer tasks, we need to install the “Dynamics 365 Unified Operations Tools”. We’ll be using them to set up our release pipeline too so consider doing it now.

#### 0.2.4.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#update-model-version-task)Update Model Version task

This one is the easiest, just add it to your build definition under the current model versioning task, disable the original one and you’re done. If you have any filters in your current task, like excluding any model, you must add the filter in the _Descriptor Search Pattern_ field using [Azure DevOps pattern syntax](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/file-matching-patterns?view=azure-devops&WT.mc_id=DOP-MVP-5003976).

#### 0.2.4.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-deployable-package-task)Create Deployable Package task

This task will replace the _Generate packages_ from the current build definitions. To set it up we just need to do a pair of changes to the default values:

##### 0.2.4.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#x-tools-path)X++ Tools Path

![MSDyn365 & Azure DevOps ALM 11](https://static.ariste.info/wp-content/uploads/2019/09/2019-09-05-11_25_12-AXZ-Dev-Build-DP-test-new-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 24")

This is your build VM’s physical bin folder, the AosService folder is usually on the unit K for cloud-hosted VMs. I guess this will change when we go VM-less to do the builds.

**Update!:** the route to the unit can be changed for $(ServiceDrive), getting a path like $(ServiceDrive)\\AOSService\\PackagesLocalDirectory\\bin.

##### 0.2.4.2.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#location-of-the-x-binaries-to-package)Location of the X++ binaries to package

![MSDyn365 & Azure DevOps ALM 12](https://static.ariste.info/wp-content/uploads/2019/09/2019-09-05-11_29_55-AXZ-Dev-Build-DP-test-new-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 25")

The task comes with this field filled in as _$(Build.BinariesDirectory)_ but this didn’t work out for our build definitions, maybe the variable isn’t set up on the proj file. After changing this to _$(Agent.BuildDirectory)\\Bin_ the package is generated.

##### 0.2.4.2.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#filename-and-path-for-the-deployable-package)Filename and path for the deployable package

![MSDyn365 & Azure DevOps ALM 13](https://static.ariste.info/wp-content/uploads/2019/09/2019-09-05-11_33_28-AXZ-Dev-Build-DP-test-new-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 26")

The path on the image should be changed to _$(Build.ArtifactStagingDirectory)\\Packages\\AXDeployableRuntime\_$(Build.BuildNumber).zip_. You can leave it without the _Packages_ folder in the path, but if you do that you will need to change the _Path to Publish_ field in the _Publish Artifact: Package_ step of the definition.

#### 0.2.4.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#add-licenses-to-deployable-package-task)Add Licenses to Deployable Package task

This task will add the license files to an existing Deployable Package. Remember that the path of the deployable package must be the same as the one in the _Create Deployable Package_ task.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-hosted-build-for-dynamics-365-finance-scm)Azure hosted build for Dynamics 365 Finance & SCM
------------------------------------------------------------------------------------------------------------------------------------------------------

The day we’ve been waiting for has come! The [Azure hosted builds](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/dev-tools/hosted-build-automation?WT.mc_id=BA-MVP-5003976) are in public preview since PU35!! We can now stop asking [Joris](https://twitter.com/jorisdg) when will this be available, because it already is! **[Check the docs](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/dev-tools/hosted-build-automation?WT.mc_id=BA-MVP-5003976)!**

I’ve been able to write this because, thanks to [Antonio Gilabert](https://twitter.com/_Gilabert_), we’ve been testing this at [Axazure](https://www.axazure.com/) for a few months with access to the private preview. And of course thanks to Joris for inviting us to the preview!

![Azure hosted build](https://i1.wp.com/ariste.info/wp-content/uploads/2020/05/AzureDevOpsPipeline-2.png?fit=702%2C1024&ssl=1 "MSDyn365 & Azure DevOps ALM 27")

Riding the Azure Pipelines by [Caza Pelusas](https://cazapelusas.com/)

What does this mean? We no longer need a VM to run the build pipelines! **Nah, we still need!** If you’re **running tests or synchronizing the DB** as a part of your build pipeline you still need the VM. But we can move CI builds to the Azure hosted agent!

You can also read my full guide on [MSDyn365FO & Azure DevOps ALM](https://ariste.info/en/msdyn365-azure-devops-alm/).

Remember **this is a public preview**. If you want to join the preview you first need to be part of the Dynamics 365 [Insider Program](https://experience.dynamics.com/) where you can join the “**Dynamics 365 for Finance and Operations Insider Community**“. Once invited you should see a new LCS project called PEAP Assets, and inside its Asset Library you’ll find the nugets in the Nuget package section.

### 0.3.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-agents)Azure agents

With the capacity to run an **extra** Azure-hosted build we get another agent to run a pipeline and can run multiple pipelines at the same time. But it **still won’t be parallel pipelines**, because we only get one VM-less agent. This means **we can run a self-hosted and azure hosted pipeline at the same time, but we cannot run two of the same type in parallel**. If we want that we need to [purchase extra agents](https://docs.microsoft.com/en-us/azure/devops/organizations/billing/buy-more-build-vs?view=azure-devops&WT.mc_id=DOP-MVP-5003976#microsoft-hosted-cicd).

With a private Azure DevOps project we get 2GB of Artifacts space (we’ll see that later) and one self-hosted and one Microsoft hosted agent with 1800 free minutes:

![08CEA665 618A 4F15 B9EC F86A405FA7D8](https://i1.wp.com/ariste.info/wp-content/uploads/2020/04/08CEA665-618A-4F15-B9EC-F86A405FA7D8.jpeg?fit=909%2C1024&ssl=1 "MSDyn365 & Azure DevOps ALM 28")

Azure hosted build: Azure DevOps project pricing

**We’ll still keep the build VM**, so it’s difficult to tell a customer we need to pay extra money without getting rid of its cost. Plus we’ve been doing everything with one agent until now and it’s been fine, right? So **take this like extra capacity**, we can divide the build between both agents and leave the MS hosted one for short builds to squeeze the 1800 free minutes as much as possible.

### 0.3.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#how-does-it-work)How does it work?

There’s really no magic in this. We move from a self-hosted agent in the build VM to a [Microsoft-hosted agent.](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?WT.mc_id=DOP-MVP-5003976&view=azure-devops)

The Azure hosted build relies on [nuget packages](https://docs.microsoft.com/en-us/nuget/what-is-nuget?WT.mc_id=DOP-MVP-5003976) to compile our X++ code. The contents of the PackagesLocalDirectory folder, platform and the compiler tools have basically been put into nugets and what we have in the build VM is now on 3 nugets.

When the build runs it downloads & installs the nugets and uses them to compile our code on the Azure hosted build along the standard packages.

### 0.3.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#what-do-i-need)What do I need?

To configure the Azure hosted build we need:

*   The 3 nuget packages from [LCS](https://lcs.dynamics.com/): Compiler tools, Platform X++ and Application X++.

*   [nuget.exe](https://www.nuget.org/downloads)

*   A user with rights at the organization level to upload the nugets to Azure DevOps.

*   Some patience to get everything running 🙂

So the first step is going to the PEAP LCS’ Asset Library and downloading the 3 nuget packages:

![Nugets for the Azure Hosted Build](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-13_44_51.png.webp "MSDyn365 & Azure DevOps ALM 29")

Nugets for the Azure Hosted Build

### 0.3.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-devops-artifact)Azure DevOps artifact

All of this can be done on your PC or in a dev VM, but you’ll need to add some files and **a VS project to your source control** so you need to use the developer box for sure.

Head to your Azure DevOps project and go to the Artifacts section. Here we’ll create a new feed and give it a name:

![Azure DevOps artifact feed](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-09_24_19.png.webp "MSDyn365 & Azure DevOps ALM 30")

![Azure DevOps artifact feed](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-09_24_55.png.webp "MSDyn365 & Azure DevOps ALM 31")

You get 2GB for artifacts, the 3 nuget packages’ size is around 500MB, you should have no issues with space unless you have other artifacts in your project.

Now press the “Connect to feed” button and select nuget.exe. You’ll find the instructions to continue there but I’ll explain it anyway.

Then you need to [download nuget.exe](https://www.nuget.org/downloads) and put it in the Windows PATH. You can also get the nugets and nuget.exe in the same folder and forget about the PATH. Up to you. Finally, install the credential provider: download [this Powershell script](https://raw.githubusercontent.com/microsoft/artifacts-credprovider/master/helpers/installcredprovider.ps1) and run it. If the script keeps asking for your credentials and fails try adding -AddNetfx as a parameter. Thanks to Erik Norell for finding this and sharing in the [comments of the original post](https://ariste.info/en/2020/05/azure-hosted-build-dynamics365-finance-scm/#comment-1793)!

Create a new file called _nuget.config_ in the same folder where you’ve downloaded the nugets. It will have the content you can see in the “Connect to feed” page, something like this:

```xml
<?xml version\="1.0" encoding\="utf-8"?>
<configuration\>
 <packageSources\>
 <clear /\>
 <add key\="AASBuild" value\="https://pkgs.dev.azure.com/aariste/aariste365FO/\_packaging/AASBuild/nuget/v3/index.json" /\>
 </packageSources\>
</configuration\>
```

**This file’s content has to be exactly the same as what’s displayed in your “Connect to feed” page.**

And finally, we’ll push (upload) the nugets to our artifacts feed. We have to do this for each one of the 3 nugets we’ve downloaded:

```powershell
nuget.exe push -Source "AASBuild" -ApiKey az <packagePath>
```

You’ll get prompted for the user. Remember it needs to have enough rights on the project.

Of course, you need to change “AASBuild” for your artifact feed name. And we’re done with the artifacts.

### 0.3.5. [](https://ariste.info/en/msdyn365-azure-devops-alm/#prepare-azure-devops)Prepare Azure DevOps

This new agent needs a solution to build our packages. This means we have to create an empty solution in Visual Studio and set the package of the project to our main package. Like this:

![2020 04 24 14 20 58](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_20_58.png.webp "MSDyn365 & Azure DevOps ALM 32")

Visual Studio solution

If you have more than one package or models, you need to add a project to this solution for each separate model you have.

We have to create another file called _packages.config_ with the following content:

```xml
<?xml version\="1.0" encoding\="utf-8"?>
<packages>
 <package id\="Microsoft.Dynamics.AX.Platform.DevALM.BuildXpp" version\="7.0.5644.16778" targetFramework\="net40" />
 <package id\="Microsoft.Dynamics.AX.Application.DevALM.BuildXpp" version\="10.0.464.13" targetFramework\="net40" />
 <package id\="Microsoft.Dynamics.AX.Platform.CompilerPackage" version\="7.0.5644.16778" targetFramework\="net40" />
</packages>
```

The version tag will depend on when you’re reading this, but the one above is the correct one for PU35. **We’ll need to update this file each time a new version of the nugets is published.**

And, to end with this part, we need to add the solution, the nuget.config and the packages.config files to TFVC. This is what I’ve done:

![2020 04 24 14 29 01](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_29_01.png.webp "MSDyn365 & Azure DevOps ALM 33")

Azure DevOps

You can see I’ve created a Build folder in the root of my DevOps project. That’s only my preference, but I like to only have code in my branches, even the projects are outside of the branches, I only want the code to move between merges and branches. Place the files and solution inside the Build folder (or wherever you decide).

### 0.3.6. [](https://ariste.info/en/msdyn365-azure-devops-alm/#configure-pipeline)Configure pipeline

Now we need to create a new pipeline, you can just import [this template](https://github.com/microsoft/Dynamics365-Xpp-Samples-Tools/blob/master/CI-CD/Pipeline-Samples/xpp-classic-ci.json) from the newly created [X++ (Dynamics 365) Samples and Tools Github project](https://github.com/microsoft/Dynamics365-Xpp-Samples-Tools). After importing the template we’ll modify it a bit. Initially, it will look like this:

![2020 04 24 14 35 07 1](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_35_07-1.png.webp "MSDyn365 & Azure DevOps ALM 34")

Azure hosted build: Default imported pipeline

As you can see the pipeline has all the steps needed to generate the DP, but some of them, the ones contained in the Dynamics 365 tasks, won’t load correctly after the import. **You just need to add those steps to your pipeline manually and complete its setup.**

#### 0.3.6.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#pipeline-root)Pipeline root

![2020 04 24 14 38 27](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_38_27.png.webp "MSDyn365 & Azure DevOps ALM 35")

You need to select the Hosted Azure Pipelines for the Agent pool, and vs2017-win2016 as Agent Specification.

#### 0.3.6.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#get-sources)Get sources

![DevOps mappings](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_42_07.png.webp "MSDyn365 & Azure DevOps ALM 36")

Azure hosted build: Our mappings

I’ve mapped 2 things here: our codebase in the first mapping and the Build folder where I’ve added the solution and config files. If you’ve placed these files inside your Metadata folder you don’t need the extra mapping.

#### 0.3.6.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#nuget-install-packages)NuGet install Packages

This step gets the nugets from our artifacts feeds and the installs to be used in each pipeline execution.

![2020 04 25 12 41 47 1](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-25-12_41_47-1.png.webp "MSDyn365 & Azure DevOps ALM 37")

Azure hosted build: nuget install

The command uses the config files we have uploaded to the Build folder, and as you can see it’s fetching the files from the _$(build.sourcesDirectory)\\Build_ directory we’ve configured in the Get sources step. If you’ve placed those files in a diferent place you need to change the paths as needed.

#### 0.3.6.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#update-model-version)Update Model Version

This is one of the steps that are displaying issues even though I got the Dynamics 365 tools installed from the Azure DevOps marketplace. If you got it right you probably don’t need to change anything. If you have the same issue as me, just add a new step and select the “Update Model Version” task and change the fields so it looks like this:

![Update Model Version](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_53_56-1.png.webp "MSDyn365 & Azure DevOps ALM 38")

Azure hosted build: Update Model Version

#### 0.3.6.5. [](https://ariste.info/en/msdyn365-azure-devops-alm/#build-solution)Build solution

![MSDyn365 & Azure DevOps ALM 14](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-25-09_33_56.png.webp "MSDyn365 & Azure DevOps ALM 39")

Build solution step

In the build solution step, you have a wildcard in the solution field: \*\*\\\\\*.sln. If you leave this wildcard it will build all the projects you have in the repo and, depending on the number of projects you have, the build could time out.

I solve this by selecting a solution, that contains all the models I have, that I have placed in the Build folder in my repo, and update that solution if you add or remove any model.

Thanks to [Ievgen Miroshnikov](https://twitter.com/IevgenMir) for pointing this out!

There could be an additional issue with the rnrproj files as [Josh Williams points out in a comment](https://ariste.info/en/2020/05/azure-hosted-build-dynamics365-finance-scm/#comment-5584). If your project was created pre-PU27 try creating a new solution to avoid problems.

#### 0.3.6.6. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-deployable-package)Create Deployable Package

This is another one of the steps that are not loading correctly for me. Again, add it and change as needed:

![2020 04 24 14 55 32](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_55_32.png.webp "MSDyn365 & Azure DevOps ALM 40")

Azure hosted build: Create Deployable Package

#### 0.3.6.7. [](https://ariste.info/en/msdyn365-azure-devops-alm/#add-licenses-to-deployable-package)Add Licenses to Deployable Package

Another step with issues. Do the same as with the others:

![2020 04 24 14 57 35](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-24-14_57_35.png.webp "MSDyn365 & Azure DevOps ALM 41")

Azure hosted build: Add Licenses to Deployable Package

And that’s all. You can queue the build to test if it’s working. For the first runs you can disable the steps after the “Build solution” one to see if the nugets are downloaded correctly and your code built. After that try generating the DP and publishing the artifact.

You’ve configured your Azure hosted build, now it’s your turn to decide in which cases will you use the self-hosted or the azure hosted build.

### 0.3.7. [](https://ariste.info/en/msdyn365-azure-devops-alm/#update-for-version-10-0-18)Update for version 10.0.18

Since version 10.0.18 we’ll be getting 4 NuGet packages instead of 3 because of the Microsoft.Dynamics.AX.Application.DevALM.BuildXpp NuGet size is getting near or over the max size which is 500MB and will come as 2 NuGet packages from now on.

[You can read about this in the docs](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/dev-tools/pipeline-nuget-split?WT.mc_id=BA-MVP-5003976).

There just 2 small changes we need to do to the pipeline if we’re already using it, one to the packages.config file and another one to the pipeline.

#### 0.3.7.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#packages-config)packages.config

The packages.config file will have an additional line for the Application Suite NuGet.

```xml
<?xml version\="1.0" encoding\="utf-8"?>

<packages\>

 <package id\="Microsoft.Dynamics.AX.Platform.DevALM.BuildXpp" version\="7.0.5968.16973" targetFramework\="net40" /\>

 <package id\="Microsoft.Dynamics.AX.Application.DevALM.BuildXpp" version\="10.0.793.16" targetFramework\="net40" /\>

 <package id\="Microsoft.Dynamics.AX.ApplicationSuite.DevALM.BuildXpp" version\="10.0.793.16" targetFramework\="net40" /\>

 <package id\="Microsoft.Dynamics.AX.Platform.CompilerPackage" version\="7.0.5968.16973" targetFramework\="net40" /\>

</packages\>
```

#### 0.3.7.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#pipeline)Pipeline

We need to add a new variable to the pipeline variables called AppSuitePackage with the value Microsoft.Dynamics._AX.ApplicationSuite.DevALM.BuildXpp_.

![New Azure DevOps pipeline variable](https://static.ariste.info/wp-content/uploads/2021/05/image-1024x664.png.webp "MSDyn365 & Azure DevOps ALM 42")

New Azure DevOps pipeline variable

And then use it in the build step and change it to:

```powershell
/p:BuildTasksDirectory\="$(NugetsPath)\\$(ToolsPackage)\\DevAlm" /p:MetadataDirectory\="$(MetadataPath)" /p:FrameworkDirectory\="$(NuGetsPath)\\$(ToolsPackage)" /p:ReferenceFolder\="$(NuGetsPath)\\$(PlatPackage)\\ref\\net40;$(NuGetsPath)\\$(AppPackage)\\ref\\net40;$(MetadataPath);$(Build.BinariesDirectory);$(NuGetsPath)\\$(AppSuitePackage)\\ref\\net40" /p:ReferencePath\="$(NuGetsPath)\\$(ToolsPackage)" /p:OutputDirectory\="$(Build.BinariesDirectory)"
```

[](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-devtest-labs-powered-builds)Azure DevTest Labs powered builds
------------------------------------------------------------------------------------------------------------------------

The **[end of Tier-1 Microsoft-managed build VMs](https://ariste.info/en/2020/10/tier-1-microsoft-managed-removed/) is near**, and this will leave us without the capacity to synchronize the DB or run tests in a pipeline, unless we deploy a new build VM in our, or our customer’s, Azure subscription. Of course, there might be a cost concern with it, and there’s where **Azure DevTest Labs** can help us!

**This post has been written thanks to [Joris de Gruyter](https://twitter.com/jorisdg)‘s session in the past [DynamicsCon](https://dynamicscon.com/)**: [Azure Devops Automation for Finance and Operations Like You’ve Never Seen!](https://www.youtube.com/watch?v=VIib-m6Q8LQ) And there’s also been some investigation and (a lot of) trial-and-error from my side until everything has been working.

![Azure DevTest Labs](https://static.ariste.info/wp-content/uploads/2021/01/adtl-1024x1024.png.webp "MSDyn365 & Azure DevOps ALM 43")

Configuring the build VM in Azure DevTest Labs

If you want to know more about builds, releases, and the Dev ALM of Dynamics 365 you can read my full guide on [MSDyn365 & Azure DevOps ALM](https://ariste.info/en/msdyn365-azure-devops-alm/).

### 0.4.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#but-first)But first…

What I’m showing in this post is not a perfect blueprint. There’s a high probability that if you try exactly the same as I do here, you won’t get the same result. But it’s a good guide to get started and do some investigation on your own and learn.

### 0.4.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-devtest-labs)Azure DevTest Labs

[Azure DevTest Labs](https://azure.microsoft.com/en-us/services/devtest-lab?WT.mc_id=BA-MVP-5003976) is an Azure tool/service that allows us to **deploy virtual machines and integrate them with Azure DevOps pipelines**, and many other things, but what I’m going to explain is just the VM and pipeline part.

What will I show in this post? How to **prepare a Dynamics 365 Finance and Operations VHD image to be used as the base to create a build virtual machine from an Azure DevOps pipeline**, build our codebase, synchronize the DB, run tests, even deploy the reports, generate the deployable package and delete the VM.

### 0.4.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#getting-and-preparing-the-vhd)Getting and preparing the VHD

This is by far the most tedious part of all the process because you need to download 11 ZIP files from LCS’ Shared Asset Library, and we all know how fast things download from LCS.

![LCS download speed](https://static.ariste.info/wp-content/uploads/2020/12/fast.png.webp "MSDyn365 & Azure DevOps ALM 44")

How is LCS download speed?

And to speed it up we can create a blob storage account on Azure and once more turn to [Mötz Jensen](https://mobile.twitter.com/splaxi)‘s [d365fo.tools](https://github.com/d365collaborative/d365fo.tools) and use the Invoke-D365AzCopyTransfer cmdlet. Just go to LCS, click on the “Generate SAS link” button for each file, use it as the source parameter in the command and your blob SAS URL as the destination one. Once you have all the files in your blob you can download them to your local PC at a good speed.

Once you’ve unzipped the VHD you need to change it from Dynamic to Fixed using this PowerShell command:

```powershell
Convert-VHD –Path VHDLOCATION.vhd –DestinationPath NEWVHD.vhd –VHDType Fixed
```

The reason is **you can’t create an Azure VM from a dynamically-sized VHD**. And it took me several attempts to notice this 🙂

### 0.4.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-a-devtest-labs-account)Create a DevTest Labs account

To do this part **you need an Azure account**. If you don’t have one you can [sign up for a free Azure account](https://azure.microsoft.com/en-us/free?WT.mc_id=BA-MVP-5003976) with a credit of 180 Euros (200 US Dollars) to be spent during 30 days, plus many other free services during 12 months.

Search for DevTest Labs in the top bar and create a new DevTest Lab. Once it’s created open the details and you should see something like this:

![Azure DevTest Labs](https://static.ariste.info/wp-content/uploads/2020/12/DevTest1.png.webp "MSDyn365 & Azure DevOps ALM 45")

Azure DevTest Labs

Click on the “Configuration and policies” menu item at the bottom of the list and scroll down in the menu until you see the “Virtual machine bases” section:

![DevTest Labs custom VHD image](https://static.ariste.info/wp-content/uploads/2020/12/DevTest2.png.webp "MSDyn365 & Azure DevOps ALM 46")

DevTest Labs custom image

And now comes the second funniest part of the process: **we need to upload the 130GB VHD image to a blob storage account**! So, click the “Add” button on top and in the new dialog that will open click the “Upload a VHD using PowerShell”. This will generate a PowerShell script to upload the VHD to the DevTest Labs blob. For example:

```powershell

<# Generated script to upload a local VHD to Azure. WARNING: The destination will be publicly available for 24 hours, after which it will expire. Ensure you complete your upload by then. Run the following command in a Azure PowerShell console after entering the LocalFilePath to your VHD. #> 
Add-AzureRmVhd -Destination "https://YOURBLOB.blob.core.windows.net/uploads/tempImage.vhd?sv=2019-07-07&st=2020-12-27T09%3A08%3A26Z&se=2020-12-28T09%3A23%3A26Z&sr=b&sp=rcw&sig=YTeXpxpVEJdSM7KZle71w8NVw9oznNizSnYj8Q3hngI%3D" -LocalFilePath "<Enter VHD location here>"
```

Generated script to upload a local VHD to Azure.

WARNING: The destination will be publicly available for 24 hours, after which it will expire.

 Ensure you complete your upload by then.

Run the following command in a Azure PowerShell console after entering

the LocalFilePath to your VHD.

```powershell
Add-AzureRmVhd \-Destination "https://YOURBLOB.blob.core.windows.net/uploads/tempImage.vhd?sv=2019-07-07&st=2020-12-27T09%3A08%3A26Z&se=2020-12-28T09%3A23%3A26Z&sr=b&sp=rcw&sig=YTeXpxpVEJdSM7KZle71w8NVw9oznNizSnYj8Q3hngI%3D" \-LocalFilePath "<Enter VHD location here>"
```

![DevTest Labs custom image upload](https://static.ariste.info/wp-content/uploads/2020/12/DevTest3.png.webp "MSDyn365 & Azure DevOps ALM 47")

DevTest Labs custom image upload

An alternative to this is using the Azure Storage Explorer as you can see in the image on the left.

You should upload the VHD to the uploads blob.

Any of these methods is good to upload the VHD and I don’t really know which one is faster.

Once the VHD is uploaded open the “Custom images” option again and you should see the VHD in the drop-down:

![DevTest Labs custom image](https://static.ariste.info/wp-content/uploads/2020/12/DevTest4.png.webp "MSDyn365 & Azure DevOps ALM 48")

DevTest Labs custom image

Give the image a name and click OK.

What we have now is the **base for a Dynamics 365 Finance and Operations dev VM** which we need to prepare to use it as a build VM.

### 0.4.5. [](https://ariste.info/en/msdyn365-azure-devops-alm/#creating-the-vm)Creating the VM

We’ve got the essential, a VHD ready to be used as a base to create a virtual machine in Azure. Our next step is finding a way to make the deployment of this VM **predictable and automated**. We will attain this thanks to [Azure ARM templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview?WT.mc_id=BA-MVP-5003976).

Go back to your DevTest Labs overview page and click the “Add” button, on the “Choose base” page select the base you’ve just created, and on the next screen click on the “Add or Remove Artifacts” link:

![Add artifacts to the VM](https://static.ariste.info/wp-content/uploads/2020/12/DevTest5.png.webp "MSDyn365 & Azure DevOps ALM 49")

Add artifacts to the VM

Search for **WinRM**, select “Configure WinRM”, and on the next screen enter “Shared IP address” as the hostname box and click “Add”.

**Note**: if when the VM runs the artifacts can’t be installed check whether the [Azure VM Agent](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/agent-windows?WT.mc_id=BA-MVP-5003976#manual-installation) is installed on the base VHD. Thanks to Joris for pointing this out!

#### 0.4.5.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#configure-azure-devops-agent-service)Configure Azure DevOps Agent Service

##### 0.4.5.1.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#option-a-use-an-artifact)Option A: use an artifact

**Update**: thanks to **[Florian Hopfner](https://ariste.info/en/2021/02/azure-devtest-labs-build-dynamics-365-fno/#comment-7107)** for reminding me this because I forgot… If you choose Option A to install the agent service you need to do some things first!

The first thing we need to do is running some PowerShell scripts that create registry entries and environment variables in the VM, go to C:\\DynamicsSDK and run these:

```powershell
Import-Module $(Join-Path \-Path "C:\\DynamicsSDK" \-ChildPath "DynamicsSDKCommon.psm1") \-Function "Write-Message", "Set-AX7SdkRegistryValues", "Set-AX7SdkEnvironmentVariables"

Set\-AX7SdkEnvironmentVariables \-DynamicsSDK "C:\\DynamicsSDK"

Set\-AX7SdkRegistryValues \-DynamicsSDK "c:\\DynamicsSDK" \-TeamFoundationServerUrl "https://dev.azure.com/YOUR\_ORG" \-AosWebsiteName $AosWebsiteName "AosService"
```

The first one will load the functions and make them available in the command-line and the other two create the registry entries and environment variables.

Now we need to **add an artifact for the Azure DevOps agent service**. This will configure the agent service on the VM each time the VM is deployed. Search for “Azure Pipelines Agent” and click it. You will see this:

![DevTest Labs Azure DevOps Agent](https://static.ariste.info/wp-content/uploads/2021/01/DevTest1-311x1024.png.webp "MSDyn365 & Azure DevOps ALM 50")

DevTest Labs Azure DevOps Agent

We need to fill some information:

On “**Azure DevOps Organization Name**” you need to provide the name of your organization. For example if your AZDO URL is https://dev.azure.com/blackbeltcorp you need to use blackbeltcorp.

On “**AZDO Personal Access Token**” you need to provide a token generated from AZDO.

On “**Agent Name**” give your agent a name, like DevTestAgent. And on “Agent Pool” a name for your pool, a new like DevTestPool or an existing one as Default.

On “**Account Name**” use the same user that we’ll use in our pipeline later. Remember this. And on “**Account Password**” its password. Using secrets with a KeyVault is better, but I won’t explain this here.

And, finally, set “**Replace Agent**” to true.

##### 0.4.5.1.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#option-b-configure-azure-devops-agent-in-the-vm)Option B: Configure Azure DevOps Agent in the VM

To do this you have to create a VM from the base image you created before and then go to C:\\DynamicsSDK and run the SetupBuildAgent script with the needed parameters:

```powershell
SetupBuildAgent.ps1 \-VSO\_ProjectCollection "https://dev.azure.com/YOUR\_ORG" \-ServiceAccountName "myUser" \-ServiceAccountPassword "mYPassword" \-AgentName "DevTestAgent" \-AgentPoolName "DevTestPool" \-VSOAccessToken "YOUR\_VSTS\_TOKEN"
```

**WARNING**: If you choose option B you must create a new base image from the VM where you’ve run the script. Then repeat the WinRM steps to generate the new ARM template which we’ll see next.

#### 0.4.5.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#arm-template)ARM template

Then go to the “Advanced Settings” tab and click the “**View ARM template**” button:

![Get the ARM template](https://static.ariste.info/wp-content/uploads/2020/12/DevTest6.png.webp "MSDyn365 & Azure DevOps ALM 51")

Get the ARM template

This will display the ARM template to create the VM from our pipeline. It’s something like this:

```json
{

 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",

 "contentVersion": "1.0.0.0",

 "parameters": {

 "newVMName": {

 "type": "string",

 "defaultValue": "aariste001"

 },

 "labName": {

 "type": "string",

 "defaultValue": "aristeinfo"

 },

 "size": {

 "type": "string",

 "defaultValue": "Standard\_B4ms"

 },

 "userName": {

 "type": "string",

 "defaultValue": "myUser"

 },

 "password": {

 "type": "securestring",

 "defaultValue": "\[\[\[VmPassword\]\]"

 },

 "Configure\_WinRM\_hostName": {

 "type": "string",

 "defaultValue": "Public IP address"

 },

 "Azure\_Pipelines\_Agent\_vstsAccount": {

 "type": "string",

 "defaultValue": "ariste"

 },

 "Azure\_Pipelines\_Agent\_vstsPassword": {

 "type": "securestring"

 },

 "Azure\_Pipelines\_Agent\_agentName": {

 "type": "string",

 "defaultValue": "DevTestAgent"

 },

 "Azure\_Pipelines\_Agent\_agentNameSuffix": {

 "type": "string",

 "defaultValue": ""

 },

 "Azure\_Pipelines\_Agent\_poolName": {

 "type": "string",

 "defaultValue": "DevTestPool"

 },

 "Azure\_Pipelines\_Agent\_RunAsAutoLogon": {

 "type": "bool",

 "defaultValue": false

 },

 "Azure\_Pipelines\_Agent\_windowsLogonAccount": {

 "type": "string",

 "defaultValue": "aariste"

 },

 "Azure\_Pipelines\_Agent\_windowsLogonPassword": {

 "type": "securestring"

 },

 "Azure\_Pipelines\_Agent\_driveLetter": {

 "type": "string",

 "defaultValue": "C"

 },

 "Azure\_Pipelines\_Agent\_workDirectory": {

 "type": "string",

 "defaultValue": "DevTestAgent"

 },

 "Azure\_Pipelines\_Agent\_replaceAgent": {

 "type": "bool",

 "defaultValue": true

 }

 },

 "variables": {

 "labSubnetName": "\[concat(variables('labVirtualNetworkName'), 'Subnet')\]",

 "labVirtualNetworkId": "\[resourceId('Microsoft.DevTestLab/labs/virtualnetworks', parameters('labName'), variables('labVirtualNetworkName'))\]",

 "labVirtualNetworkName": "\[concat('Dtl', parameters('labName'))\]",

 "vmId": "\[resourceId ('Microsoft.DevTestLab/labs/virtualmachines', parameters('labName'), parameters('newVMName'))\]",

 "vmName": "\[concat(parameters('labName'), '/', parameters('newVMName'))\]"

 },

 "resources": \[

 {

 "apiVersion": "2018-10-15-preview",

 "type": "Microsoft.DevTestLab/labs/virtualmachines",

 "name": "\[variables('vmName')\]",

 "location": "\[resourceGroup().location\]",

 "properties": {

 "labVirtualNetworkId": "\[variables('labVirtualNetworkId')\]",

 "notes": "Dynamics365FnO10013AgentLessV2",

 "customImageId": "/subscriptions/6715778f-c852-453d-b6bb-907ac34f280f/resourcegroups/devtestlabs365/providers/microsoft.devtestlab/labs/devtestd365/customimages/dynamics365fno10013agentlessv2",

 "size": "\[parameters('size')\]",

 "userName": "\[parameters('userName')\]",

 "password": "\[parameters('password')\]",

 "isAuthenticationWithSshKey": false,

 "artifacts": \[

 {

 "artifactId": "\[resourceId('Microsoft.DevTestLab/labs/artifactSources/artifacts', parameters('labName'), 'public repo', 'windows-winrm')\]",

 "parameters": \[

 {

 "name": "hostName",

 "value": "\[parameters('Configure\_WinRM\_hostName')\]"

 }

 \]

 },

 {

 "artifactId": "\[resourceId('Microsoft.DevTestLab/labs/artifactSources/artifacts', parameters('labName'), 'public repo', 'windows-vsts-build-agent')\]",

 "parameters": \[

 {

 "name": "vstsAccount",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_vstsAccount')\]"

 },

 {

 "name": "vstsPassword",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_vstsPassword')\]"

 },

 {

 "name": "agentName",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_agentName')\]"

 },

 {

 "name": "agentNameSuffix",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_agentNameSuffix')\]"

 },

 {

 "name": "poolName",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_poolName')\]"

 },

 {

 "name": "RunAsAutoLogon",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_RunAsAutoLogon')\]"

 },

 {

 "name": "windowsLogonAccount",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_windowsLogonAccount')\]"

 },

 {

 "name": "windowsLogonPassword",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_windowsLogonPassword')\]"

 },

 {

 "name": "driveLetter",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_driveLetter')\]"

 },

 {

 "name": "workDirectory",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_workDirectory')\]"

 },

 {

 "name": "replaceAgent",

 "value": "\[parameters('Azure\_Pipelines\_Agent\_replaceAgent')\]"

 }

 \]

 }

 \],

 "labSubnetName": "\[variables('labSubnetName')\]",

 "disallowPublicIpAddress": true,

 "storageType": "Premium",

 "allowClaim": false,

 "networkInterface": {

 "sharedPublicIpAddressConfiguration": {

 "inboundNatRules": \[

 {

 "transportProtocol": "tcp",

 "backendPort": 3389

 }

 \]

 }

 }

 }

 }

 \],

 "outputs": {

 "labVMId": {

 "type": "string",

 "value": "\[variables('vmId')\]"

 }

 }

}
```

**NOTE**: if you’re using option B you won’t have the artifact node for the VSTS agent.

This JSON file will be used as the base to create our VMs from the Azure DevOps pipeline. This is known as [Infrastructure as Code](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-infrastructure-as-code?WT.mc_id=BA-MVP-5003976) (IaC) and it’s a way of defining our infrastructure in a file as it were code. It’s another part of the DevOps practice that should solve the “it works on my machine” issue.

If we take a look to the JSON’s parameters node there’s the following information:

*   newVMName and labName will be the name of the VM and the DevTest Labs lab we’re using. The VM name is not really important because we’ll set the name later in the pipeline.
*   size is the VM size, a D3 V2 in the example above, but we can change it and will do it later.
*   userName & passWord will be the credentials to access the VM and must be the same we’ve used to configure the Azure DevOps agent.
*   Configure\_WinRM\_hostName is the artifact we added to the VM template that will allow the pipelines to run in this machine.

To do it faster and for demo purposes I’m using a plain text password in the ARM template, changing the password node to something like this:

```json
"password": {
 "type": "string",
 "defaultValue": "yourPassword"
 },
 ```

I will do the same with all the _secureString_ nodes, but you shouldn’t and should instead use an Azure KeyVault which comes with the DevTest Labs account.

Of course **you would never upload this template to Azure DevOps with a password in plain text**. There’s plenty of resources online that teach how to use parameters, Azure KeyVault, etc. to accomplish this, for example this one: [6 Ways Passing Secrets to ARM Templates](https://devkimchi.com/2019/04/24/6-ways-passing-secrets-to-arm-templates/).

OK, now grab that file and save it to your Azure DevOps repo. I’ve created a folder in my repo’s root called ARM where I’m saving all the ARM templates:

![ARM templates on Azure DevOps](https://static.ariste.info/wp-content/uploads/2021/01/DevTestLabs2.png.webp "MSDyn365 & Azure DevOps ALM 52")

ARM templates on Azure DevOps

### 0.4.6. [](https://ariste.info/en/msdyn365-azure-devops-alm/#preparing-the-vm)Preparing the VM

The VHD image you download can be used as a developer VM with no additional work, just run Visual Studio, connect it to your AZDO project and done. But if you want to use it as a build box you need to do several things first.

Remember that the default user and password for these VHDs are Administrator and Pass@word1.

#### 0.4.6.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#disable-services)Disable services

First of all we will stop and disable services like the Batch, Management Reporter, SSAS, SSIS, etc. Anything you see that’s not needed to run a build.

#### 0.4.6.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-a-new-sql-user)Create a new SQL user

Open [SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&WT.mc_id=BA-MVP-5003976) (as an Administrator) and create a new SQL user as a copy of the axdbadmin one. Then open the web.config file and update the DB user and password to use the one you’ve just created.

#### 0.4.6.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#prepare-ssrs-optional)Prepare SSRS (optional)

If you want to deploy reports as part of your build pipeline you need to go to SSMS again (and as an Admin again), and open a new query in the reporting DB to execute the following query:

```sql
exec DeleteEncryptedContent
```

#### 0.4.6.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#powershell-scripts)PowerShell Scripts

The default build definition that runs on a build VM uses several PowerShell scripts to run some tasks. I’m adding an additional script called PrepareForAgent.

The scripts can be found in the C:\\DynamicsSDK folder of the VM.

##### 0.4.6.4.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#prepareforbuild)PrepareForBuild

This script comes with the VM and **we need to modify it** to avoid one thing: the PackagesLocalDirectory backup which is usually done in the first build. We need to get rid of this or we’ll waste around an hour per run until the files are copied.

We don’t need this because our VM will be new each time we run the pipeline!

So open the script, go to line 696 and look for this piece of code:

```PowerShell
# Create packages backup (if it does not exist).

$NewBackupCreated \= Backup-AX7Packages \-BackupPath $PackagesBackupPath \-DeploymentPackagesPath $DeploymentPackagesPath \-LogLocation $LogLocation

# Restore packages backup (unless a new backup was just created).

if (!$NewBackupCreated)

{

 Restore-AX7Packages \-BackupPath $PackagesBackupPath \-DeploymentPackagesPath $DeploymentPackagesPath \-LogLocation $LogLocation \-RestoreAllFiles:$RestorePackagesAllFiles

}

if (!$DatabaseBackupToRestore)

{
 $DatabaseBackupPath \= Get-BackupPath \-Purpose "Databases"
 Backup-AX7Database \-BackupPath $DatabaseBackupPath
}
else
{
 # Restore a database backup (if specified).
 Restore-AX7Database \-DatabaseBackupToRestore $DatabaseBackupToRestore
}
```

We need to modify it until we end up with this:

```PowerShell
if ($DatabaseBackupToRestore)
{
 Restore-AX7Database \-DatabaseBackupToRestore $DatabaseBackupToRestore
}
```

We just need the DB restore part and skip the backup, otherwise we’ll be losing 45 minutes in each run for something we don’t need because the VM will be deleted when the build is completed.

#### 0.4.6.5. [](https://ariste.info/en/msdyn365-azure-devops-alm/#optional-but-recommended-install-d365fo-tools)**Optional (but recommended)**: install [d365fo.tools](https://github.com/d365collaborative/d365fo.tools)

Just run this:

```PowerShell
Install-Module -Name d365fo.tools
```

We can use the tools to do a module sync, partial sync or deploy just our reports instead of all.

### 0.4.7. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-a-new-image)Create a new image

Once we’ve done all these prepare steps we can log out of this VM and stop it. **Do not delete it yet!** Go to “Create custom image”, give the new image a name, select “I have not generalized this virtual machine” and click the “OK” button.

This will generate a new image that you can use as a base image with all the changes you’ve done to the original VHD.

### 0.4.8. [](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-devops-pipelines)Azure DevOps Pipelines

We’re ready to setup our new build pipeline in Azure DevOps. This pipeline will consist of three steps: create a new VM, run all the build steps, and delete the VM:

![MSDyn365 & Azure DevOps ALM 15](https://static.ariste.info/wp-content/uploads/2021/01/DevTestLabs4.png.webp "MSDyn365 & Azure DevOps ALM 53")

First of all check that your pipeline runs on Azure pipelines (aka Azure-hosted):

![DevTest Labs Azure Pipelines](https://static.ariste.info/wp-content/uploads/2021/01/image-2.png.webp "MSDyn365 & Azure DevOps ALM 54")

DevTest Labs Azure Pipelines

The create and delete steps will run on the Azure Pipelines pool. The build step will run on our DevTestLabs pool, or the name you gave it when configuring the artifact on DevTest Labs or the script on the VM.

#### 0.4.8.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-azure-devtest-labs-vm)Create Azure DevTest Labs VM

Create a new pipeline and choose the “Use the classic editor” option. Make sure you’ve selected TFVC as your source and click “Continue” and “Empty job”. Add a new task to the pipeline, look for “Azure DevTest Labs Create VM”. We just need to fill in the missing parameters with our subscription, lab, etc.

![Create VM Azure DevTest Labs](https://static.ariste.info/wp-content/uploads/2021/01/DevTestLabs5.png.webp "MSDyn365 & Azure DevOps ALM 55")

Create VM Azure DevTest Labs

Remember this step must run on the Azure-hosted pipeline.

#### 0.4.8.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#build)Build

This is an easy one. Just export a working pipeline and import it. And this step needs to run on your self-hosted pool:

![Runs on self-hosted pool](https://static.ariste.info/wp-content/uploads/2021/01/image-3.png.webp "MSDyn365 & Azure DevOps ALM 56")

Runs on self-hosted pool

##### 0.4.8.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#optional-use-selectivesync-not-recommended-see-next-option)Optional: use SelectiveSync (not recommended, see next option)

You can replace the Database Sync task for a PowerShell script that will only sync the tables in your models:

![SelectiveSync.ps1](https://static.ariste.info/wp-content/uploads/2021/01/image-9.png.webp "MSDyn365 & Azure DevOps ALM 57")

SelectiveSync.ps1

Thanks Joris for the tip!

##### 0.4.8.2.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#optional-use-d365fo-tools-to-sync-your-packages-models)Optional: use d365fo.tools to sync your packages/models

This is a better option than the SelectiveSync above. You can synchronize your packages or models only to gain some time. This cmdlet uses sync.exe like Visual Studio does and should be better than SelectiveSync.

Add a new PowerShell task, select Inline Script and this is the command:

```PowerShell
Invoke-D365DbSyncModule -Module "Module1", "Module2" -ShowOriginalProgress -Verbose
```

##### 0.4.8.2.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#optional-use-d365fo-tools-to-deploy-ssrs-reports)Optional: use d365fo.tools to deploy SSRS reports

If you really want to add the report deployment step to your pipeline you can save some more extra time using [d365fo.tools](https://github.com/d365collaborative/d365fo.tools) and just deploy the reports in your models like we’ve done with the DB sync.

Run this in a new PowerShell task to do it:

```PowerShell
Publish-D365SsrsReport -Module YOUR\_MODULE -ReportName \*
```

#### 0.4.8.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#delete-azure-devtest-labs-vm)Delete Azure DevTest Labs VM

It’s almost the same as the create step, complete the subscription, lab and VM fields and done:

![Delete VM](https://static.ariste.info/wp-content/uploads/2021/01/image.png.webp "MSDyn365 & Azure DevOps ALM 58")

Delete VM

And this step, like the create one, will run on the Azure-hosted agent.

#### 0.4.8.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#dependencies-and-conditions)Dependencies and conditions

When all three steps are configured we need to add dependencies and conditions to some of them. For example, to make sure that the delete VM step runs when the build step fails, but it doesn’t when the create VM step fails.

##### 0.4.8.4.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#build)Build

The build step depends on the create VM step, and will only run if the previous step succeeds:

![Build step dependencies and conditions](https://static.ariste.info/wp-content/uploads/2021/01/image-4.png.webp "MSDyn365 & Azure DevOps ALM 59")

Build step dependencies and conditions

##### 0.4.8.4.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#delete-vm)Delete VM

The delete step depends on all previous steps and must run when the create VM step succeeds. If the create step fails there’s no VM and we don’t need to delete it:

![Dependencies and conditions on delete VM step](https://static.ariste.info/wp-content/uploads/2021/01/image-5.png.webp "MSDyn365 & Azure DevOps ALM 60")

Dependencies and conditions on delete VM step

This is the custom condition we’ll use:

```cs
and(always(), eq(dependencies.Job\_1.status, 'Succeeded'))
```

If you need to know your first step’s job name just export the pipeline to YAML and you’ll find it there:

![Export pipeline to YAML](https://static.ariste.info/wp-content/uploads/2021/01/image-6.png.webp "MSDyn365 & Azure DevOps ALM 61")

Export pipeline to YAML

![Job name on YAML](https://static.ariste.info/wp-content/uploads/2021/01/image-7.png.webp "MSDyn365 & Azure DevOps ALM 62")

Job name on YAML

If this step fails when the pipeline is run, wait to delete the VM manually, first change the VM name in the delete step, save your pipeline and then use the dropdown to show the VMs in the selected subscription, and save the pipeline.

### 0.4.9. [](https://ariste.info/en/msdyn365-azure-devops-alm/#run-the-build)Run the build

And, I think, we’re done and ready to run our Azure DevTest Labs pipeline for Dynamics 365 Finance and Operations… click “Run pipeline” and wait…

![MSDyn365 & Azure DevOps ALM 16](https://static.ariste.info/wp-content/uploads/2021/01/image-10-382x1024.png.webp "MSDyn365 & Azure DevOps ALM 63")

Tadaaaa!!

### 0.4.10. [](https://ariste.info/en/msdyn365-azure-devops-alm/#times)Times

The pipeline from the image above is one with real code from a customer but I can’t compare the times with the Azure-hosted builds because there’s no sync, or tests there. Regarding the build time the Azure-hosted takes one minute less, but it needs to install the nugets first.

But for example this is a comparison I did:

![Azure DevTest Labs B2ms vs B4ms](https://static.ariste.info/wp-content/uploads/2021/01/B2msVSB4ms.png.webp "MSDyn365 & Azure DevOps ALM 64")

Azure DevTest Labs B2ms vs B4ms

It takes around 1 hour to create the VM, build, do a full DB synch, deploy reports, run tests, generate a Deployable Package and, finally, delete the VM:

![MSDyn365 & Azure DevOps ALM 17](https://static.ariste.info/wp-content/uploads/2021/01/Full.jpg.webp "MSDyn365 & Azure DevOps ALM 65")

If you skip deploying the SSRS reports your build will run in 15 minutes less, that’s around 45 minutes.

If you use the partial sync process instead of a full DB sync it’ll be 5-7 minutes less.

This would leave us with a 35-40 minutes build.

#### 0.4.10.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#comparison-1)Comparison 1

![MSDyn365 & Azure DevOps ALM 18](https://static.ariste.info/wp-content/uploads/2021/01/Dtcomp1.png.webp "MSDyn365 & Azure DevOps ALM 66")

No DB Sync

The image above shows a simple package being compiled, without any table, so the selective sync goes really fast. The build times improve with VM size.

#### 0.4.10.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#comparison-2)Comparison 2

![MSDyn365 & Azure DevOps ALM 19](https://static.ariste.info/wp-content/uploads/2021/01/Dtcomp2.png.webp "MSDyn365 & Azure DevOps ALM 67")

Same code Full DB Sync

This one is compiling the same codebase but is doing a full DB sync. The sync time improves in the B4ms VM compared to the B2ms but it’s almost the same in the B8ms. Build times are better for larger VM sizes.

#### 0.4.10.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#comparison-3)Comparison 3

![MSDyn365 & Azure DevOps ALM 20](https://static.ariste.info/wp-content/uploads/2021/01/image-14.png.webp "MSDyn365 & Azure DevOps ALM 68")

Real code + full sync

And in the image above we see a more realistic build. The codebase is larger and we’re doing a full DB sync.

Similar as the comparison before there a good enhancement between a B2ms and a B4ms, but not between a B4ms and B8ms.

### 0.4.11. [](https://ariste.info/en/msdyn365-azure-devops-alm/#show-me-the-money)Show me the money!

I think this is the interesting comparison. How did a Tier-1 MS-hosted build VM cost? Around 400€? How does it compare to using the Azure DevTest Labs alternative?

There’s only one fix cost when using Azure DevTest Labs: the blob storage where the VHD is uploaded. The VHD’s size is around 130GB and this should have a cost of, more or less, 5 euros/month. Keep in mind that **you need to clean up your custom images when yours is prepared**, the new ones are created as snapshots and also take space in the storage account.

Then we have the variable costs that come with the deployment of a VM each build but it’s just **absurd**. Imagine we’re using a B4ms VM, with a 256GB Premium SSD disk, we would pay 0.18€/hour for the VM plus the proportional part of 35.26€/month of the SSD disk, which would be like 5 cents/hour?

But this can run on a B2ms VM too which is half the compute price of the VM, down to 9 cents per hour.

If we run this build once a day each month, 30 times, the cost of a B4ms would be like… 7€? Add the blob storage and we’ll be paying **12€ per month to run our builds with DB sync and tests**.

Is it cheaper than deploying a cloud-hosted environment, and starting and stopping it using [the new d365fo.tools Cmdlets](https://github.com/d365collaborative/d365fo.tools) each time we run the build? **Yes it is!** Because if we deploy a CHE we’ll be paying the price of the SSD disk for the whole month!

### 0.4.12. [](https://ariste.info/en/msdyn365-azure-devops-alm/#some-final-remarks)Some final remarks

1.  I have accomplished this **mostly through trial-and-error**. There’s lots of enhancements and best practices to be applied to all the process, specially using an [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/general/overview?WT.mc_id=BA-MVP-5003976) to store all the secrets to be used in the Azure DevOps Agent artifact and the pipeline.
2.  This in another clear example that [X++ developers need to step outside of X++](https://ariste.info/en/2019/07/do-you-want-to-become-a-better-x-developer/) and Dynamics 365 FnO. We’re not X++ only developers anymore, we’re very lucky to be working on a product that is using Azure.
3.  I’m sure there’s scenarios where using DevTest Labs to create a build VM is useful. Maybe not for an implementation partner, but maybe it is for an ISV partner. It’s just an additional option.
4.  The only bad thing to me is that we need to apply the version upgrades manually to the VHDs because they’re published only twice a year.
5.  As I said at the beginning of the post, it may have worked to me with all these steps, but if you try you maybe need to change some things. But it’s a good way to start.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#add-and-build-net-projects)Add and build .NET projects
-----------------------------------------------------------------------------------------------------------

I bet that most of us have had to develop some .NET class library to solve something in Dynamics 365 Finance and Operations. You create a C# project, build it, and add the DLL as a reference in your FnO project. Don’t do that anymore! You can add the .NET project to source control, build it in your pipeline, and the DLL gets added to the deployable package!

I’ve been trying this during the last days after a conversation on Yammer, and while I’ve managed to build .NET and X++ code in the same pipeline, I’ve found some issues or limitations.  

### 0.5.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#build-net-in-your-pipeline)Build .NET in your pipeline

**Note**: what I show in this post is done using the [Azure-hosted pipeline](https://ariste.info/en/2020/05/azure-hosted-build-dynamics365-finance-scm/) but it should also be possible to do it using a self-hosted agent (aka old build VM).

The build step of the pipeline invokes [msbuild.exe](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019&WT.mc_id=DOP-MVP-5003976) which can build .NET code. If we check the logs of the build step we will see it:

![msbuild.exe builds C# projects and our X++ ones too!](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-29-13_03_44.png.webp "MSDyn365 & Azure DevOps ALM 69")

msbuild.exe builds C# projects and our X++ ones too!

Remember that X++ is part of the .NET family after all… a second cousin or something like it.

![MSDyn365 & Azure DevOps ALM 21](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-29-13_21_54.png.webp "MSDyn365 & Azure DevOps ALM 70")

Build folder

If you’ve read the blog post about [Azure-hosted builds](https://ariste.info/en/2020/05/azure-hosted-build-dynamics365-finance-scm/) you must’ve seen I’m putting the solution that references all my models in a folder called Build at the root of my source control tree (left image).

That’s just a personal preference that helps me keep the .config files and the solution I use to build all the models in a single, separate place.

By using a solution and pointing the build process to use it I also keep control of what’s being built in a single place.

### 0.5.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#add-a-c-project-to-fno)Add a C# project to FnO

Our first step will usually be creating a Finance and Operations project. Once it’s created we right-click on the solution and select “Add new project”. Then we select a Visual C# Class Library project:

![MSDyn365 & Azure DevOps ALM 22](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-12_51_37.png.webp "MSDyn365 & Azure DevOps ALM 71")

C# project in Dynamics 365

![MSDyn365 & Azure DevOps ALM 23](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-12_54_25.png.webp "MSDyn365 & Azure DevOps ALM 72")

Now we should have a solution with a FnO Project and a C# project (right image).

To demo this I’ll create a class called Calculator with a single method that accepts two decimal values as parameters and returns it’s sum. An add method.

```cs
public class Calculator { public decimal Add(decimal a, decimal b) { return a + b; } }

public class Calculator

{

 public decimal Add(decimal a, decimal b)

 {

 return a + b;

 }

}
```

Now compile the C# project alone, not the whole solution. This will create the DLL in the bin folder of the project. We have to do this before adding the C# project as a reference to the FnO project.

Right click on the References node of the FnO project and select “Add Reference…”:

![MSDyn365 & Azure DevOps ALM 24](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_00_34.png.webp "MSDyn365 & Azure DevOps ALM 73")

Add reference to FnO project

A window will open and you should see the C# project in the “Projects” tab:

![MSDyn365 & Azure DevOps ALM 25](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_01_58.png.webp "MSDyn365 & Azure DevOps ALM 74")

Add C# project reference to FnO project

Select it and click the Ok button. That will add the C# project as a reference to our FnO project, but we still need to do something or this won’t compile in our pipeline. We have to manually add the reference to the project that has been created in the AOT. So, right-click on the reference and select “Add to source control”:

![MSDyn365 & Azure DevOps ALM 26](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_31_10.png.webp "MSDyn365 & Azure DevOps ALM 75")

Add the reference to source control

In the FnO project add a Runnable Class, we’ll call the C# library there:

```cs
using AASBuildNetDemoLibrary; 
class AASBuildNetTest 
{ 
    public static void main(Args \_args) 
    { 
        var calc = new Calculator(); 
        calc.Add(4, 5); 
    } 
}

using AASBuildNetDemoLibrary;

class AASBuildNetTest

{

 public static void main(Args \_args)

 {

 var calc \= new Calculator();

 calc.Add(4, 5);

 }

}
```

Add the solution to source control if you haven’t, make sure all the objects are also added and check it in.

### 0.5.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#build-pipeline)Build pipeline

If I go to my Azure DevOps repo we’ll see the following:

![MSDyn365 & Azure DevOps ALM 27](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_38_19.png.webp "MSDyn365 & Azure DevOps ALM 76")

Projects and objects

You can see I’ve checked-in the solution under the Build folder, as I said earlier this is my personal preference and I do that to keep the solutions I’ll use to build the code under control.

In my build pipeline I make sure I’m using this solution to build the code:

![MSDyn365 & Azure DevOps ALM 28](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_43_12.png.webp "MSDyn365 & Azure DevOps ALM 77")

Build Dynamics 365 solution

Run the pipeline and when it’s done you can check the build step and you’ll see a line that reads:

```powershell
Copying file from "D:\\a\\9\\s\\Build\\AASBuildNetDemo\\AASBuildNetDemoLibrary\\bin\\Debug\\AASBuildNetDemoLibrary.dll" to "D:\\a\\9\\b\\AASDemo\\bin\\AASBuildNetDemoLibrary.dll".
```

And if you download the DP, unzip it, navigate to AOSService\\Packages\\files and unzip the file in there, then open the bin folder, you’ll see our library’s DLL there:

![MSDyn365 & Azure DevOps ALM 29](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-30-13_52_47.png.webp "MSDyn365 & Azure DevOps ALM 78")

Victory!

### 0.5.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#things-i-dont-like-understand-need-to-investigate)Things I don’t like/understand/need to investigate

I’ve always done this with a single solution and only one C# project. I have some doubts about how this will work with many C# projects, models, solutions, etc.

For example, if a model has a dependency on the DLL but it’s built before the DLL the build will fail. I’m sure there’s a way to set an order to solve dependencies like there is for FnO projects within a solution.

Or maybe I could try building all the C#/.NET projects before, pack them in a nuget and use the DLLs later in the FnO build, [something similar to what Paul Heisterkamp explained in his blog](https://msdyn365fo.wordpress.com/2020/08/13/integration-of-nuget-packages-from-azure-devops-artifacts-into-the-dynalm-process/).

Anyway, it’s your choice how to manage your C# projects and what solution fits your workflow the best, but at least you’ve got an example here 🙂

[](https://ariste.info/en/msdyn365-azure-devops-alm/#setup-release-pipelines)Setup Release Pipelines
----------------------------------------------------------------------------------------------------

We’ve seen how the default build definition is created and how we can modify it. Now we’ll see how to configure our release pipelines!

The release pipelines allow us to automatically deploy our Deployable Packages to a Tier 2+ environment. This is part of the Continuous Delivery (CD) strategy. We can only do this for the UAT environments, it’s not possible to automate the deployment to the production environment.

### 0.6.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#setting-up-release-pipeline-in-azure-devops-for-dynamics-365-for-finance-and-operations)Setting up Release Pipeline in Azure DevOps for Dynamics 365 for Finance and Operations

To configure the release pipeline, we need:

*   AAD app registration
*   LCS project
*   An Azure DevOps project linked to the LCS project above
*   A service account

I recommend a service account to do this, with a non-expiring password and no MFA enabled. It must have enough privileges on LCS, Azure and Azure DevOps too. This is not mandatory and can be done even with your user (if it has enough rights) for testing purposes, but if you’re setting this up don’t use your user and go for a service account.

### 0.6.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#aad-app-creation)AAD app creation

The first step to take is creating an app registration on Azure Active Directory to upload the generated deployable package to LCS. Head to [Azure portal](https://portal.azure.com/)  and once logged in go to Azure ActiveDirectory, then App Registrations and create a new Native app:

![MSDyn365 & Azure DevOps ALM 30](https://static.ariste.info/wp-content/uploads/2020/05/New-AAD-App.png.webp "MSDyn365 & Azure DevOps ALM 79")

Next go to “Settings” and “Required permissions” to add the Dynamics Lifecycle Services API:

![MSDyn365 & Azure DevOps ALM 31](https://static.ariste.info/wp-content/uploads/2020/05/Add-permission.png.webp "MSDyn365 & Azure DevOps ALM 80")

In the dialog that will open change to the “APIs my organization uses” tab and select “Dynamics Lifecycle Services”:

![MSDyn365 & Azure DevOps ALM 32](https://static.ariste.info/wp-content/uploads/2020/05/LCS.png.webp "MSDyn365 & Azure DevOps ALM 81")

Select the only available permission in the next screen and click on the “Add permissions” button. Finally press the “Grant admin consent” button to apply the changes. This last step can be easily forgotten and the package upload to LCS cannot be done if not granted. Once done take note of the Application ID, we’ll use it later.

### 0.6.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-the-release-pipeline-in-devops)Create the release pipeline in DevOps

Go to Azure DevOps, and to Pipelines -> Releases to create the new release. Select “New release pipeline” and choose “Empty job” from the list.

On the artifact box select the build which will be used for this release definition:

![New release](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-0.33.40-1024x467.png.webp "MSDyn365 & Azure DevOps ALM 82")

Pick the build definition you want to use for the release in “Source”, “Latest” in “Default version” and push “Add”.

#### 0.6.3.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#upload-to-lcs)Upload to LCS

The next step we’ll take is adding a Task with the release pipeline for Dynamics. Go to the Tasks tab and press the plus button. A list with extension will appear, look for “Dynamics 365 Unified Operations Tools”:

![Dynamics 365 Unified Operations Tools](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-0.39.39-1024x279.png.webp "MSDyn365 & Azure DevOps ALM 83")

If the extension hasn’t been added previously it can be done in this screen. **In order to add it, the user used to create the release must have admin rights on the Azure DevOps account, not only in the project in which we’re creating the pipeline**.

When the task is created we need to fill some parameters:![Release Dynamics Operations](./MSDyn365 & Azure DevOps ALM - ariste.info_files/Captura-de-pantalla-2019-02-03-a-les-0.43.11-1024x508.png.webp "MSDyn365 & Azure DevOps ALM 84")

![Release Dynamics Operations](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-0.43.11-1024x508.png.webp "MSDyn365 & Azure DevOps ALM 84")

#### 0.6.3.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#apply-deployable-package)Apply deployable package

This step is finally available for self-service environments! If you already set this for a regular environment you can still change the task to the new version.

![Azure DevOps asset deployment](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-04-10_55_48.png.webp "MSDyn365 & Azure DevOps ALM 85")

Azure DevOps asset deployment

The new task version 1 works for both type of environments: Microsoft managed (regular environments) and self-service environments. The task version 0 is the old one and will only work with regular environments. You can safely switch your deploy tasks to version 1.

What’s different in task version 1? I guess that some work behind it that we don’t see to make it support self-service, but in the UI we only see a new field called “_Name for the update_“.

![MSDyn365 & Azure DevOps ALM 33](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-04-10_59_39.png.webp "MSDyn365 & Azure DevOps ALM 86")

Name for the update field

This field is needed only for the self-service environments deployments, it will be ignored for regular ones, and corresponds to the field with the same name that appears on LCS when we apply an update to a sandbox environment:

![MSDyn365 & Azure DevOps ALM 34](https://static.ariste.info/wp-content/uploads/2020/09/2020-09-04-11_05_10.png.webp "MSDyn365 & Azure DevOps ALM 87")

Name for this update in LCS

The default field’s value is the variable $(Release.ReleaseName) that is the name of the release, but you can change it, for example I’ll be using a pattern like _PREFIX BRANCH $(Build.BuildNumber)_ to have the same name we have for the builds and identifying what we’re deploying to prod quickier.

### 0.6.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#creating-the-lcs-connection)Creating the LCS connection

The first step in the task is setting up the link to LCS using the AAD app we created before. Press New and let’s fill the fields in the following screen:

![MSDyn365 & Azure DevOps ALM 35](https://static.ariste.info/wp-content/uploads/2020/05/LCS-in-Azure-DevOps.png.webp "MSDyn365 & Azure DevOps ALM 88")

It’s only necessary to fill in the connection name, username, password (from the user and Application (Client) ID fields. Use the App ID we got in the first step for the App ID field. The endpoint fields should be automatically filled in. Finally, press OK and the LCS connection is ready.

In the LCS Project Id field, use the ID from the LCS project URL, for example in https://lcs.dynamics.com/V2/ProjectOverview/1234567 the project is is 1234567.

Press the button next to “File to upload” and select the deployable package file generated by the build:

![DP Generado](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-0.53.30.png.webp "MSDyn365 & Azure DevOps ALM 89")

If the build definition hasn’t been modified, the output DP will have a name like AXDeployableRuntime\_VERSION\_BUILDNUMBER.zip. Change the fixed Build Number for the DevOps variable $(Build.BuildNumber) like in the image below:

![BUildNumber](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-0.56.20-1024x87.png.webp "MSDyn365 & Azure DevOps ALM 90")

The package name and description in LCS are defined in “LCS Asset Name” and “LCS Asset Description”. For these fields, Azure DevOps’ [build variables](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&WT.mc_id=DOP-MVP-5003976) and [release variables](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/variables?view=azure-devops&tabs=batch&WT.mc_id=DOP-MVP-5003976#default-variables) can be used. Use whatever fits your project, for example a prefix to distinguish between prod and pre-prod packages followed by $(Build.BuildNumber), will upload the DP to LCS with a name like Prod 2019.1.29.1, using the date as a DP name.

Save the task and release definition and let’s test it. In the Releases select the one we have just created and press the “Create a release” button, in the dialog just press OK. The release will start and, if everything is OK we’ll see the DP in LCS when it finishes:

![LCS Asset Library](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-1.05.05.png.webp "MSDyn365 & Azure DevOps ALM 91")

The release part can be automated, just press the lightning button on the artifact and enable the trigger:

![Release trigger](https://static.ariste.info/wp-content/uploads/2019/02/Captura-de-pantalla-2019-02-03-a-les-1.08.21.png.webp "MSDyn365 & Azure DevOps ALM 92")

And that’s all! Now the build and the releases are both configured. Once the deployment package is published the CI scenario will be complete.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#more-automation)More automation!
=====================================================================================

I’ve already explained in the past how to automate the builds, create the [CI builds](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-continuous-integration?WT.mc_id=DOP-MVP-5003976) and create the release pipelines on Azure DevOps, what I want to talk about in this post is about adding a little bit more automation.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#builds)Builds
------------------------------------------------------------------

In the build definition go to the “Triggers” tab and enable a scheduled build:

![MSDyn365 & Azure DevOps ALM 36](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-26-11_05_38-AXZ-Main-Build-DP-Daily-16.30-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 93")

This will automatically trigger the build at the time and days you select. In the example image, every weekday at 16.30h a new build will be launched. **But** everyday? Nope! What the “Only schedule builds if the source or pipeline has changed” checkbox below the time selector makes is **only triggering the build if there’s been any change to the codebase**, meaning that if there’s no changeset checked-in during that day no build will be triggered.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#releases)Releases
----------------------------------------------------------------------

First step done, let’s see what can we do with the releases:

![MSDyn365 & Azure DevOps ALM 37](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-26-11_14_04-AXZ-Prod-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 94")

The release pipeline in the image above is the one that launches after the build I’ve created in the first step. For this pipeline I’ve added the following:

![MSDyn365 & Azure DevOps ALM 38](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-26-11_16_53-AXZ-Prod-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 95")

The continuous deployment trigger has been enabled, meaning that after the build finishes this release will be automatically run. No need to define a schedule but you could also do that.

![MSDyn365 & Azure DevOps ALM 39](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-26-11_20_35-AXZ-Prod-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 96")

As you can see, the schedule screen is exactly the same as in the builds, even the changed pipeline checkbox is there.  You can use any of these two approaches, CD or scheduled release, it’s up to your project or team needs.

With these two small steps you can have your full CI and CD strategy automatized and update a UAT environment each night to have all the changes done during that day ready for testing, with no human interaction!

[](https://ariste.info/en/msdyn365-azure-devops-alm/#but-i-like-to-add-some-human-touch-to-it)But I like to add some human touch to it
--------------------------------------------------------------------------------------------------------------------------------------

If you don’t like not knowing if an environment is being updated… well **that’s IMPOSSIBLE** because LCS will SPAM you to make sure you know what’s going on. But if you don’t want to be completely replaced by robots you can add approvals to your release flow:

![MSDyn365 & Azure DevOps ALM 40](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-09_59_15-AXZ-Prod-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 97")

Clicking the left lightning + person button on your release you can set the approvers, a person or a group (which is quite practical), and the kind of approval (all or single approver) and the timeout. You will also receive an email with a link to the approval form:

![MSDyn365 & Azure DevOps ALM 41](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-09_52_47-AXZ-Dev-Release-Daily-16.30-Release-41-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 98")

And you can also postpone the deployment! [Everything is awesome!](https://www.youtube.com/watch?v=StTqXEQ2l-Y)  

[](https://ariste.info/en/msdyn365-azure-devops-alm/#extra-bonus)Extra bonus!
-----------------------------------------------------------------------------

A little tip. Imagine you have the following release:

![MSDyn365 & Azure DevOps ALM 42](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-11_09_39-AXZ-Golden-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 99")

This will update 3 environments, but will also upload the same Deployable Package three times to LCS. Wouldn’t it be nice to have a single upload and that all the deployments used that file? Yes, but we can’t pass the output variable from the upload to other stages 🙁 Yes that’s unfortunately right. But we can do something with a little help from our friend Powershell!

[](https://ariste.info/en/msdyn365-azure-devops-alm/#update-a-variable-in-a-release)Update a variable in a release
------------------------------------------------------------------------------------------------------------------

What we need to do is create a variable in the release definition and set its scope to “Release”:

![MSDyn365 & Azure DevOps ALM 43](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-11_14_57-AXZ-Dev-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 100")

Then, **for each stage**, we need to enable this checkbox in the agent job:

![MSDyn365 & Azure DevOps ALM 44](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-11_26_29-AXZ-Dev-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 101")

I explain later why we’re enabling this. We now only need to update this variable after uploading the DP to LCS. Add an inline Powershell step after the upload one and do this:

```cs
# Populate store value to update pipeline

$assetId\= "$(GoldenUpload.FileAssetId)"

Write\-Output ('##vso\[task.setvariable variable=localAsset\]{0}' \-f $assetId)

#region variables

$ReleaseVariableName \= 'axzfileid'

$releaseurl \= ('{0}{1}/\_apis/release/releases/{2}?api-version=5.0' \-f $($env:SYSTEM\_TEAMFOUNDATIONSERVERURI), $($env:SYSTEM\_TEAMPROJECTID), $($env:RELEASE\_RELEASEID) )

#endregion

#region Get Release Definition

Write\-Host "URL: $releaseurl"

$Release \= Invoke\-RestMethod \-Uri $releaseurl \-Headers @{

 Authorization \= "Bearer $env:SYSTEM\_ACCESSTOKEN"

}

#endregion

#region Output current Release Pipeline

#Write-Output ('Release Pipeline variables output: {0}' -f $($Release.variables | #ConvertTo-Json -Depth 10))

#endregion

#Update axzfileid with new value

$release.variables.($ReleaseVariableName).value \= $assetId

#region update release pipeline

Write\-Output ('Updating Release Definition')

$json \= @($release) | ConvertTo\-Json \-Depth 99

$enc \= \[System.Text.Encoding\]::UTF8

$json\= $enc.GetBytes($json)

Invoke\-RestMethod \-Uri $releaseurl \-Method Put \-Body $json \-ContentType "application/json" \-Headers @{Authorization \= "Bearer $env:SYSTEM\_ACCESSTOKEN" }

#endregion
```

You need to change the following:

*   Line 2: $assetId= “$(GoldenUpload.FileAssetId)”. Change $(GoldenUpload.FileAssetId) for your output variable name.
*   Line 6: $ReleaseVariableName = ‘axzfileid’. Change axzfileid for your Release variable name.

And you’re done. This script uses Azure DevOps’ REST API to update the variable value with the file id, and we enabled the OAuth token checkbox to allow the usage of this API without having to pass any user credentials. This is not my idea obviously, I’ve done [this thanks to this post from Stefan Stranger’s blog](https://stefanstranger.github.io/2019/06/26/PassingVariablesfromStagetoStage/).

Now, in the deploy stages you need to retrieve your variable’s value in the following way:

![MSDyn365 & Azure DevOps ALM 45](https://static.ariste.info/wp-content/uploads/2019/10/2019-10-27-11_21_02-AXZ-Dev-Release-Daily-16.30-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 102")

**Don’t forget the ( ) or it won’t work!**

And with these small changes you can have a release like this:

![MSDyn365 & Azure DevOps ALM 46](https://static.ariste.info/wp-content/uploads/2019/10/pipelines.png.webp "MSDyn365 & Azure DevOps ALM 103")

With a single DP upload to LCS and multiple deployments using the file uploaded in the first stage. With approvals, and delays, and emails, and everything!

[](https://ariste.info/en/msdyn365-azure-devops-alm/#lcs-db-api)LCS DB API
==========================================================================

[](https://ariste.info/en/msdyn365-azure-devops-alm/#call-the-lcs-database-movement-api-from-your-azure-devops-pipelines)Call the LCS Database Movement API from your Azure DevOps Pipelines
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### 2.1.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#what-for)What for?

Basically, [automation](https://ariste.info/en/2019/10/devops-alm-automation-in-microsoft-dynamics-365-for-finance-and-operations/). Right now **the API only allows the refresh from one Microsoft Dynamics 365 for Finance and Operations environment to another**, so the idea is having fresh data from production in our UAT environments **daily**. I don’t know which new operations the API will support in the future but another idea could be adding the DB export operation (creating a bacpac) to the pipeline and having a copy of prod ready to be restored in a Dev environment.

Don’t forget that the API has a limit of **3 refresh operations per environment per 24 hours**. Don’t do this on a CI build! (it makes no sense either). Probably the best idea is to run this nightly with all your tests, once a day.

### 2.1.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#calling-the-api)Calling the API

I’ll use PowerShell to call the API from a pipeline. PowerShell has a command called [Invoke-RestMethod](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-restmethod?view=powershell-7&WT.mc_id=DOP-MVP-5003976) that makes HTTP/HTTPS requests. It’s really easy and we just need to do the same we did to call the API in my post.

#### 2.1.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#getting-the-token)Getting the token

```cs
$projectId \= "1234567"

$tokenUrl \= "https://login.microsoftonline.com/common/oauth2/token"

$clientId \= "12345678-abcd-432a-0666-22de4c4321aa"

$clientSecret \= "superSeCrEt12345678"

$username \= "youruser@tenant.com"

$password \= "strongerThan123456"

$tokenBody \= @{

 grant\_type \= "password"

 client\_id \= $clientId

 client\_secret \= $clientSecret

 resource \= "https://lcsapi.lcs.dynamics.com"

 username \= $username

 password \= $password

}

$tokenResponse \= Invoke\-RestMethod \-Method 'POST' \-Uri $tokenUrl \-Body $tokenBody

$token \= $tokenResponse.access\_token
```

To get the token we’ll use this script. Just change the variables for the ones of your project, AAD App registration, user (remember it needs access to the preview) and password and run it. If everything is OK you’ll get the JSON response in the $tokenResponse variable and from there you can get the token’s value using dot notation.

#### 2.1.2.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#requesting-the-db-refresh)Requesting the DB refresh

```cs
$projectId \= "1234567"

$sourceEnvironmentId \= "fad26410-03cd-4c3e-89b8-85d2bddc4933"

$targetEnvironmentId \= "cab68410-cd13-9e48-12a3-32d585aaa548"

$refreshUrl \= "https://lcsapi.lcs.dynamics.com/databasemovement/v1/databases/project/$projectId/source/$sourceEnvironmentId/target/$targetEnvironmentId"

$refreshHeader \= @{

 Authorization \= "Bearer $token"

 "x-ms-version" \= '2017-09-15'

 "Content-Type" \= "application/json"

}

$refreshResponse \= Invoke\-RestMethod $refreshUrl&nbsp;\-Method 'POST' \-Headers $refreshHeader
```

This will be the call to trigger the refresh. We’ll need the token we’ve just obtained in the first step to use it in the header and the source and target environment Ids.

If it’s successful the response will be a 200 OK.

### 2.1.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#add-it-to-your-pipeline)Add it to your pipeline

Adding this to an Azure DevOps pipeline is no mistery. Select and edit your pipeline, I’m doing it on a nigthly build (it’s called continuous but it’s not…) that runs after the environment has been updated with code, and add a new PowerShell task:

![MSDyn365 & Azure DevOps ALM 47](https://static.ariste.info/wp-content/uploads/2020/01/2020-01-26-19_33_00-AXZ-Dev-Continuous-Build-Azure-DevOps-Services.png.webp "MSDyn365 & Azure DevOps ALM 104")

Select the task and change it to “Inline”:

![MSDyn365 & Azure DevOps ALM 48](https://static.ariste.info/wp-content/uploads/2020/01/2020-01-26-19_24_42-AXZ-Prod-Release-Daily-18h-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 105")

Then just paste the script we’ve created in the Script field and done! You’ll get a refresh after the tests!

You can also run this on your release pipeline **BUT** if you do it after the deploy step remember to mark the “Wait for Completion” option or the operation will fail because the environment will already be servicing! And even then it could fail if the servicing goes over the timeout time. So… **don’t run this on your release pipeline**!

And that’s all. Let’s which new operations will be added to the API and what we can do with them.

### 2.1.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#use-d365fo-tools-in-your-azure-pipeline)Use d365fo.tools in your Azure Pipeline

Thanks to [Mötz’s comment](https://ariste.info/en/2020/01/calling-the-lcs-database-movement-api-in-your-azure-devops-pipeline/#comment-77) pointing me to how to add d365fo.tools to a hosted pipeline I’ve created a pipeline which will install the tools and run the commands. It’s even easier to do than with the Invoke-RestMethod.

#### 2.1.4.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#but-first)But first…

Make sure that in your Azure Active Directory app registration you’ve selected “Treat application as a public client” under Authentication:

![MSDyn365 & Azure DevOps ALM 49](https://static.ariste.info/wp-content/uploads/2020/01/2020-02-06-15_56_47-LCSDBAPIPreview-Authentication-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 106")

#### 2.1.4.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#the-task)The task

First we need to install d365fo.tools and then we can use its commands to call the LCS API:

```powershell
Install\-PackageProvider nuget \-Scope CurrentUser \-Force \-Confirm:$false

Install\-Module \-Name AZ \-AllowClobber \-Scope CurrentUser \-Force \-Confirm:$False \-SkipPublisherCheck

Install\-Module \-Name d365fo.tools \-AllowClobber \-Scope CurrentUser \-Force \-Confirm:$false

Get\-D365LcsApiToken \-ClientId "{YOUR\_APP\_ID}" \-Username "{USERNAME}" \-Password "{PASSWORD}" \-LcsApiUri "https://lcsapi.lcs.dynamics.com" \-Verbose | Set\-D365LcsApiConfig \-ProjectId 1234567

Invoke\-D365LcsDatabaseRefresh \-SourceEnvironmentId "958ae597-f089-4811-abbd-c1190917eaae" \-TargetEnvironmentId "13cc7700-c13b-4ea3-81cd-2d26fa72ec5e" \-SkipInitialStatusFetch
```

As you can see it a bit easier to do the refresh using d365fo.tools. We get the token and pipeline the output to the Set-D365LcsApiConfig command which will store the token (and others). This also helps to not having to duplicate AppIds, users, etc. and as you can see to call the refresh operation we just need the source and target environment Ids!

[](https://ariste.info/en/msdyn365-azure-devops-alm/#automating-prod-to-dev-db-copies)Automating Prod to Dev DB copies
----------------------------------------------------------------------------------------------------------------------

The new LCS DB API endpoint to [create a database export](https://docs.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/database/api/v1/reference-create-export?WT.mc_id=BA-MVP-5003976) has been published! With it we now have a way of **automating and scheduling a database refresh from your Dynamics 365 FnO production environment to a developer or Tier 1 VM**.

![Using the LCS DB API](https://i2.wp.com/ariste.info/wp-content/uploads/2020/04/D96E7A98-2B35-4C5E-95F5-E1DF9091CE1E-scaled.jpeg?fit=1024%2C783&ssl=1 "MSDyn365 & Azure DevOps ALM 107")

Using the LCS DB API

### 2.2.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#the-bacpac-issue)The bacpac issue

One of the main setbacks we currently have with prod DB refreshes is that it’s not a quick thing to do because you need to:

*   Refresh a Tier 2+ environment with prod’s DB
*   Export a [bacpac](https://docs.microsoft.com/en-us/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-ver15&WT.mc_id=DOP-MVP-5003976#bacpac) from the Tier 2+ environment
*   Restore the bacpac on a Tier 1 VM.

This happens because Tier 2+ environments use Azure SQL as the DB engine and Tier 1 VMs use SQL Server.

The time it takes to complete the process depends on the size of the database and the performance of the VM you’ll restore it to. But **it’s not a fast process at all**. For a 60GB database you’ll get a bacpac around 7GB that will take:

*   1 to 2 hours to refresh to UAT
*   2 to 4 hours for the bacpac to be exported
*   At least 4 hours to restore it to a Tier 1 VM.

That’s between 7 and 11 hours until you have the DB on a developer machine. Once it’s there you can quickly get a BAK and share it. But you might need the time of a full working day to have that data available.

### 2.2.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#save-us-lcs-db-api)Save us LCS DB API!

Thanks to the new LCS DB API’s endpoint we can perform all these steps automatically, and with the help of [d365fo.tools](https://github.com/d365collaborative/d365fo.tools) it’ll be even easier. But first…

Due to the extensive time it takes to complete all the process, **we first have to decide a schedule** (daily, weekly, etc.) and then this schedule must be compatible with the release cadence to UAT/Prod, because **only one operation at a time can be done**.

There’s still another problem but I’ll talk about it after seeing the scripts.

### 2.2.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#my-proposal)My proposal

To do the last part of the LCS DB API flow from prod to dev, we need a Tier 1 VM where the bacpac will be restored. My idea is **using the build VM on Microsoft’s subscription** and an Azure DevOps pipeline to run all the scripts that will restore the DB in that VM. It’s an underused machine and it fits perfectly to this purpose.

I want to clarify why I’ve thought about doing this using the build VM. In most cases this VM will be doing nothing during the night, maybe only running some tests, and it’s during that period of time when I suggest doing all this. But be aware that depending on your DB size this won’t be possible or you’ll run out of space after 2 o 3 restores.

So think about deploying an extra VM and install an agent there to do this, whatever you do **don’t mess with the build VM if you don’t know what you’re doing**! Try this on a dev VM or anywhere else if you’re afraid of breaking something. **Remember you’ll lose the capacity to generate DPs and run pipelines if this environments breaks**!

This post is just an example of a possible solution, you need to decide what suits you best! **End of the update**.

As I said before I’ll be using [Mötz Jensen](https://twitter.com/splaxi)‘s d365fo.tools, we could do everything without them but that would be a bit stupid because using the tools is easier, faster and makes everything clearer.

I’ve separated all the steps in 3 Powershell scripts: execute the refresh, export the bacpac and restore the bacpac.

#### 2.2.3.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#refresh-database)Refresh database

This will refresh the prod environmnet to a Tier 2+:

```powershell
$clientId \= "ab12345-6220-4566-896a-19a4ad41783f"

$userName \= "admin@tenant"

$passWord \= "admin123456"

$projectId \= "1234567"

$sourceEnvId \= "958bc863-f089-4811-abbd-c1190917eaae"

$targetEnvId \= "13aa6872-c13b-4ea3-81cd-2d26fa72ec5e"

Get-D365LcsApiToken \-ClientId $clientId \-Username $userName \-Password $passWord \-LcsApiUri "https://lcsapi.lcs.dynamics.com" \-Verbose | Set\-D365LcsApiConfig \-ProjectId $projectId

Invoke-D365LcsDatabaseRefresh \-SourceEnvironmentId $sourceEnvId \-TargetEnvironmentId $targetEnvId \-SkipInitialStatusFetch
```

#### 2.2.3.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#export-database)Export database

This part will trigger the bacpac export from the Tier 2+ environment which we’ve just refreshed:

```powershell
$sourceEnvId \= "958bc863-f089-4811-abbd-c1190917eaae"

$targetEnvId \= "13aa6872-c13b-4ea3-81cd-2d26fa72ec5e"

Get-D365LcsApiConfig | Invoke-D365LcsApiRefreshToken | Set\-D365LcsApiConfig

Invoke-D365LcsDatabaseExport \-SourceEnvironmentId $targetEnvId \-BackupName $bacpacName
```

#### 2.2.3.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#restore-bacpac)Restore bacpac

And the final step will download the bacpac and restore it to a new database:

```powershell
$currentDate \= Get-Date \-Format yyyymmdd

$bacpacName \= "UAT{0}" \-f $currentDate

$downloadPath \= "D:\\UAT{0}.bacpac" \-f $currentDate

$newDBName \= "AxDB\_{0}" \-f $currentDate

Get-D365LcsApiConfig | Invoke-D365LcsApiRefreshToken | Set\-D365LcsApiConfig

$backups \= Get-D365LcsDatabaseBackups

$fileLocation \= $backups\[0\].FileLocation

Invoke-D365AzCopyTransfer \-SourceUri $fileLocation \-DestinationUri $downloadPath

Import-D365Bacpac \-ImportModeTier1 \-BacpacFile $downloadPath \-NewDatabaseName $newDBName
```

#### 2.2.3.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#using-it-in-an-azure-devops-pipeline)Using it in an Azure DevOps pipeline

![Azure DevOps pipeline](https://static.ariste.info/wp-content/uploads/2020/04/2020-04-19-13_04_40.png.webp "MSDyn365 & Azure DevOps ALM 108")

Azure DevOps pipeline

This is it. Create a Powershell script, place it in the Build VM and call it in your pipeline. This is only valid for the agent hosted in the build VM. Everything can probably be run in an Azure hosted agent, but I’ll not cover it here because I think that using the build VM, where we can restore the DB, is more useful to us.

### 2.2.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#timing)Timing

These 3 scripts will call the LCS DB API to refresh, export and restore the DB. But there’s the timing issue.

Refreshing the database takes some time and exporting it too. You need to find a way to control the status of the operations. The LCS DB API offers an operation you can use to get the status of the ongoing operation. Using d365fo.tools:

```powershell
Get-D365LcsDatabaseRefreshStatus \-OperationActivityId 123456789 \-EnvironmentId "99ac6587-c13b-4ea3-81cd-2d26fa72ec5e"
```

You can choose to control that inside your Powershell scripts, but if we use the agent on the build VM that means we cannot use it for anything else until everything is done.

That’s why I separated the process in 3 steps. You can manually schedule 3 pipelines, one for each step at the times **you know** each stage ends. Then you can choose the order: export, restore, refresh or refresh, export, restore.

You could also use [Windows Task Scheduler](https://docs.microsoft.com/en-us/windows/win32/taskschd/task-scheduler-start-page?WT.mc_id=DOP-MVP-5003976) and forget about AZDO Pipelines, but we’re not doing that because we love pipelines.

And that’s all, we finally have a way of moving data without having to do it manually, we can schedule it, but we need to take some decisions on how we’ll do things. And I’ll leave that up to you 🙂

[](https://ariste.info/en/msdyn365-azure-devops-alm/#secure-your-azure-pipelines-with-azure-key-vault)Secure your Azure Pipelines with Azure Key Vault
======================================================================================================================================================

But **creating a pipeline with a password in plain sight was not very secure**. How could we add extra security to a pipeline? Once again we can turn to an Azure tool to help us, the [Azure Key Vault](https://azure.microsoft.com/en-us/services/key-vault/).

[](https://ariste.info/en/msdyn365-azure-devops-alm/#azure-key-vault)Azure Key Vault
====================================================================================

A Key Vault is a service that allows us to **safely store certificates or secrets** and later use them in our applications and services. And like many other Azure services [it has a cost](https://azure.microsoft.com/en-us/pricing/details/key-vault/) but it’s really low and, for a normal use, you will be billed like a cent or none a month. Don’t be stingy with security!

You might already know about Azure Key Vault because we can use it in Microsoft Dynamics 365 for Finance and Operations under System Administration. For example it’s how the company certificates for the Spanish SII or Brazilian NF-e are stored and later retrieved to call the web services.

[](https://ariste.info/en/msdyn365-azure-devops-alm/#securing-your-azure-devops-pipelines)Securing your Azure DevOps Pipelines
------------------------------------------------------------------------------------------------------------------------------

Thanks to the [Azure Key Vault task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/deploy/azure-key-vault?view=azure-devops&WT.mc_id=DOP-MVP-5003976) (which is [open source like many other tasks](https://github.com/microsoft/azure-pipelines-tasks)) getting a secret from a Key Vault has no secret ([badum tssss](https://www.youtube.com/watch?v=ObpcGNCU944)).

### 4.1.1. [](https://ariste.info/en/msdyn365-azure-devops-alm/#create-a-key-vault)Create a Key Vault

Go to your Azure subscription and look for Key Vaults in the top search bar. If you don’t have an Azure subscription you can get one **free with a credit of 170€/200$ for 30 days** and try this or other things.

In the Key Vault page click on “Create key vault” and fill the fields

![MSDyn365 & Azure DevOps ALM 50](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-13_54_14-Create-key-vault-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 109")

You can go through other tabs but I will just click “Review & Create” to create the vault.

### 4.1.2. [](https://ariste.info/en/msdyn365-azure-devops-alm/#add-the-task-to-devops)Add the task to DevOps

Now go to Azure DevOps and create a new pipeline or edit an existing one. Add a task to the agent job and look for azure key vault:

![MSDyn365 & Azure DevOps ALM 51](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-13_57_08-New-release-pipeline-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 110")

It’s possible that you might need to get the task from the marketplace first, if so remember you need to have enough right on the organization and not only the AZDO project you’re in. Now go to the task and select your subscription:

![MSDyn365 & Azure DevOps ALM 52](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_00_18-New-release-pipeline-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 111")

Once selected click the “Authorize” button. **This will create a service principal in your subscription**, we’ll use it later. After authorizing you just need to select the key vault you’ve created in the first step. And back to Azure.

### 4.1.3. [](https://ariste.info/en/msdyn365-azure-devops-alm/#setup-and-secret-creation)Setup and secret creation

Go to your key vault, “Access policies” and click “Add Access Policy”:

![MSDyn365 & Azure DevOps ALM 53](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_05_51-aaristeDevOps-Access-policies-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 112")

When we authorized the task to access our Azure subscription it created a service principal now we need to select it to **list and get the secrets** to be able to use them in our pipeline. Click on “Select principal”:

![MSDyn365 & Azure DevOps ALM 54](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_08_17-Add-access-policy-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 113")

In the search bar type your subscription’s name, the principal should start with it and end with the same ID of your subscription. Select it and click the “Select” button at the bottom:

![MSDyn365 & Azure DevOps ALM 55](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_10_21-axz-dev01dev.northeurope.cloudapp.azure_.com_61012-Remote-Desktop-Connection.png.webp "MSDyn365 & Azure DevOps ALM 114")

Now click on the “Secret permissions” lookup and under “Secret Management Operations” select Get and List:

![MSDyn365 & Azure DevOps ALM 56](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_12_13-Add-access-policy-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 115")

If you want to also use certificates or keys you should do the same. Finally click the “Add” button and **don’t forget to click “Save”!!** Otherwise nothing will be saved:

![MSDyn365 & Azure DevOps ALM 57](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_13_42-aaristeDevOps-Access-policies-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 116")

Now we can create a secret in the key vault. Go to secrets and click on “Generate/Import”, complete the fields and finally click on the “Create” button:

![MSDyn365 & Azure DevOps ALM 58](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_15_55-Create-a-secret-Microsoft-Azure.png.webp "MSDyn365 & Azure DevOps ALM 117")

### 4.1.4. [](https://ariste.info/en/msdyn365-azure-devops-alm/#using-the-secrets-in-your-pipelines)Using the secrets in your pipelines

We’re ready to use the secret in our pipeline. I will add a PowerShell task to call the LCS DB API using d365fo.tools but I’ll change all the variables to the secrets:

```powershell
# Write your PowerShell commands here.
Install\-PackageProvider nuget \-Scope CurrentUser \-Force \-Confirm:$false
Install\-Module \-Name AZ \-AllowClobber \-Scope CurrentUser \-Force \-Confirm:$False \-SkipPublisherCheck
Install\-Module \-Name d365fo.tools \-AllowClobber \-Scope CurrentUser \-Force \-Confirm:$false
Get\-D365LcsApiToken \-ClientId "$(myAppId)" \-Username "$(myUserName)" \-Password "$(mySecretPassword)" \-LcsApiUri "https://lcsapi.lcs.dynamics.com" \-Verbose | Set\-D365LcsApiConfig \-ProjectId $(myProjectId)
Get\-D365LcsDatabaseBackups
```

As you can see now even the AAD App Id is masked.

What the Azure Key Vault task does is getting the secrets from Azure and storing them in variables when the pipeline runs:

![MSDyn365 & Azure DevOps ALM 59](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_23_26-New-release-pipeline-1-Release-1-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 118")

Then we can access it’s value with the $(variableName) notation in the PowerShell script. If you try to print the secrets’ values using the Write-Host command all you’ll get will be three asterisks, so you can see that using the Key Vault is more than safe. If we check the result of running the Get-D365LcsDatabaseBackups command we’ll see how good is this:

![MSDyn365 & Azure DevOps ALM 60](https://static.ariste.info/wp-content/uploads/2020/02/2020-02-08-14_28_01-New-release-pipeline-1-Release-1-Pipelines.png.webp "MSDyn365 & Azure DevOps ALM 119")

The ProjectId value **is not printed because it was one of our secret values**!

And this is how you can add extra security to your Dev ALM!
