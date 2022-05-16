## Azure DevOps, semantic-release, Sonar and nuget publish…

In the last a few weeks, as part of my SDK maintainer transition, I made a few modifications to our builds, namely added JDK 11 to [Java SDK Core](https://github.com/IBM/java-sdk-core/pull/138) and [Platform Services Java SDK](https://github.com/IBM/platform-services-java-sdk/pull/116). Recently I reorganized my own pet project and turned out that I need to publish a nuget package. A good opportunity to use the gained experience from the build modifications.

I’m going to describe what and how I did in the following sections.

My development process is feature branch based, meaning new stuff is developed on a feature branch, also tested there and once it is done will be merged by PR.

During the feature branch phase I need restore, build, test and sonar data collection. In the main branch phase I need restore, build, test, sonar data collection, semantic versioning and release, nuget package creation and eventually push into a nuget repo.

The azure-pipeline.yaml can be found [here](https://github.com/EncyclopediaGalactica/Guards/blob/0.1.22/azure-pipelines.yml). The content below is based on version `0.1.22`.

## Let’s see the details.
The control flow consist of 3 stages. The first one runs unconditionally and tool installation can be placed here. The second one, called `Feature_branch`, runs when the `System.PullRequest.SourceBranch` variable value is [not empty](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml), meaning the PR event source is a branch and not main (if I understand correctly). The build will be executed against this branch. The third stage is against master when `System.PullRequest.SourceBranch` is empty (no feature branch) **and** `Build.SourceBranch` is `main`. That’s all.

```
stages:
  - stage: Setup

  - stage: Feature_branch
    condition: ne(variables['System.PullRequest.SourceBranch'], '')

  - stage: Main_branch
    condition: and(eq(variables['System.PullRequest.SourceBranch'], ''), eq(variables['Build.SourceBranch'],'refs/heads/main'))
```
[Gist](https://gist.github.com/Andras-Csanyi/aeb8f3f18ad03661c1d387cddedbc891)

## Setup stage
This stage runs unconditionally before both stages. You can put stuff here which is true for the remaining two stages. In my case install of [JetBrains.dotCover](https://www.jetbrains.com/dotcover/) is here.

In order to reinstall dotCover at every build you need to follow the procedure you an see in the example.

Why sonar is not placed here? Sonar can deal with branches, moreover, the code quality data displayed in Sonar is a quality gate. If it shows what is expected you can merge into main, if not… you have stuff to do. In my case there is no block built in, but you got the idea.

According to the concept in my thoughts what is needed for a DevOps process (build, test, package create and publish, deploy, etc.) should be independent from the build environment. Meaning, if I move the build process to another build machine it can install all the tools its needed if a basic OS is provided. For example, in my case the build machine runs an Ubuntu, where JDK, .NET SDK and nodejs installed. This is the basic necessity for being able to run a build, and install tools needed for the build specific stuff. This way it is easy to move the DevOps process to another build environment. The two extreme example would be 1, install everything by the build process including JDK, .NET SDK and so on. In this case a build would take 30 minutes. 2, install nothing, but provide by the OS. In this case I log in to the build machine and install everything there. Keeping them updated would be a pain. The keyword is **balance**.

```
stages:
  - stage: Setup
    jobs:
      - job: env_vars
        displayName: Env variables
        steps:
          - script: |
              echo $PATH
              env | sort
      - job: dotcover_install
        displayName: Install dotCover
        steps:
          - script: |
              dotnet new tool-manifest
              dotnet tool install --local JetBrains.dotCover.GlobalTool
              dotnet tool install --local dotnet-sonarscanner
```
[Gist](https://gist.github.com/Andras-Csanyi/d40aba1b78dfb1141f0b133c4a81e354)

## Feature_branch stage
The second stage manages all the tasks against feature branch.

Important parts are the following:

- order of the steps is defined by `dependsOn` keyword
- secret values have to be extracted in every step where they needed using `env` — for example: `SONAR_LOGIN_NAME`
- not secret values can be accessed easily as you can see in the `sonar_scan` section in case of `$(sonar.organization)`
- sonar is the first and the last step, you can put steps before it and after it, but the point is that build and test steps must be between sonar start and end

Further information about how Azure DevOps manages variables you can read [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch). At first a bit rocket science, but it is a powerful and robust solution.

```
  - stage: Feature_branch
    condition: ne(variables['System.PullRequest.SourceBranch'], '')
    jobs:
      - job: sonar_scan
        displayName: Sonar scan
        steps:
          - script: |
              dotnet sonarscanner begin \
              /k:"Guards" \
              /d:sonar.host.url=$(sonar.host.url) \
              /d:sonar.login=$SONAR_LOGIN_NAME \
              /o:$(sonar.organization) \
              /s:../s/sonar.xml \
              /d:sonar.pullrequest.key=$(System.PullRequest.PullRequestNumber) \
              /d:sonar.pullrequest.branch=$(System.PullRequest.SourceBranch) \
              /d:sonar.pullrequest.base=main \
              /d:sonar.pullrequest.provider=GitHub \
              /d:sonar.pullrequest.github.repository=EncyclopediaGalactica/Guards
            env:
              SONAR_LOGIN_NAME: $(sonar.login)
      - job: restore_solution
        displayName: Restore solution
        dependsOn: sonar_scan
        steps:
          - script: dotnet restore $solutionName

      - job: build_solution
        displayName: Build solution
        dependsOn: restore_solution
        steps:
          - script: dotnet build $solutionName $debugBuild

      - job: test_solution
        displayName: Test and Coverage
        dependsOn: build_solution
        steps:
          - script: |
              dotnet dotcover test \
              --no-build \
              --dotCoverReportType=html           
      - job: sonar_end
        dependsOn: test_solution
        displayName: Sonar End
        steps:
          - script: |
              dotnet sonarscanner end /d:sonar.login=$(sonar.login)
            env:
              SONAR_LOGIN_NAME: $(sonar.login)
```
[Gist](https://gist.github.com/Andras-Csanyi/08a8458aff8b67723cd38a4c2db58860)

## Main_branch stage
The third stage manages everything against `main` including build, test, Sonar, semantic release, and nuget publish.

Important details:

- execution order is managed here too using the `dependsOn` properties
- `PATH` is manipulated, but its scope only for the job. In my case `bump2version` required it.
- lot of `npm package install`, I need to figure out how to cache them…
- when nuget is created the job has the `workspace` property set up to clean which means start with `clean` including clone the repo again
- nuget package name contains the version number, I read it out from a [version.txt](https://github.com/EncyclopediaGalactica/Guards/blob/main/version.txt) file. This file is maintained by bump2version.
- whenever you need to `cd` into a directory, better to use `workingDirectory` property

```
  - stage: Main_branch
    condition: and(eq(variables['System.PullRequest.SourceBranch'], ''), eq(variables['Build.SourceBranch'],'refs/heads/main'))
    jobs:

      - job: sonar_scan
        displayName: Sonar scan
        steps:
          - script: |
              dotnet sonarscanner begin \
              /k:"Guards" \
              /d:sonar.host.url=$(sonar.host.url) \
              /d:sonar.login=$SONAR_LOGIN_NAME \
              /o:$(sonar.organization) \
              /s:../s/sonar.xml \
              /d:sonar.pullrequest.provider=GitHub \
              /d:sonar.pullrequest.github.repository=EncyclopediaGalactica/Guards
            env:
              SONAR_LOGIN_NAME: $(sonar.login)
      - job: restore_solution
        displayName: Restore solution
        steps:
          - script: dotnet restore $solutionName

      - job: build_solution
        dependsOn: restore_solution
        displayName: Build solution
        steps:
          - script: dotnet build $solutionName $debugBuild

      - job: test_solution
        dependsOn: build_solution
        displayName: Test solution
        steps:
          - script: dotnet test $solutionName

      - job: sonar_end
        dependsOn: test_solution
        displayName: Sonar End
        steps:
          - script: |
              dotnet sonarscanner end /d:sonar.login=$(sonar.login)
            env:
              SONAR_LOGIN_NAME: $(sonar.login)
      - job: install_semantic
        dependsOn: sonar_end
        displayName: Install Semantic
        steps:
          - script: |
              echo '##vso[task.prependpath]$(HOME)/.local/bin'
            displayName: PATH
          - script: |
              npm install npm@6
            displayName: npm@6
          - script: |
              npm install semantic-release
            displayName: semantic-release
          - script: |
              npm install @semantic-release/changelog
            displayName: semantic-release/changelog
          - script: |
              npm install @semantic-release/exec
            displayName: semantic-release/exec
          - script: |
              npm install @semantic-release/git
            displayName: semantic-release/git
          - script: |
              npm install @semantic-release/github
            displayName: semantic-release/github
          - script: |
              pip3 install --user bump2version
            displayName: bump2version
      - job: semantic_release
        dependsOn: install_semantic
        displayName: Semantic Release
        steps:
          - script: |
              echo '##vso[task.prependpath]$(HOME)/.local/bin'
            displayName: PATH
          - script: |
              npx semantic-release
            displayName: semantic-release
            env:
              GH_TOKEN: $(githubToken)
      - job: nuget
        dependsOn: semantic_release
        displayName: Nuget
        workspace:
          clean: all
        steps:
          - script: |
              dotnet pack Guards.csproj --configuration Release --include-symbols -p:NuspecFile=Guards.nuspec
            displayName: Nuget
            workingDirectory: src/Guards
          - script: |
              dotnet nuget push src/Guards/bin/Release/EncyclopediaGalactica.Guards.`cat version.txt`.nupkg --api-key $NUGET_TOKEN --source "https://nuget.pkg.github.com/EncyclopediaGalactica/index.json" --skip-duplicate
            displayName: Publish Nuget
            env:
              GH_TOKEN: $(githubToken)
              USERNAME: $(githubUserName)
              NUGET_TOKEN: $(nugetToken)    
```
[Gist](https://gist.github.com/Andras-Csanyi/3a03e8b157197ec0fef60db5e7f7f367)

In Azure DevOps looks like the following:

![adops1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652717182662/lL91BuYHA.png align="left")

![adops2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652717219925/NW4XAm7_s.png align="left")

There are points where this process can be enhanced:

- the already mentioned cached npm packages
- test result upload to Azure DevOps

Further configuration files like bump2version and semantic release configs can be found in the repo.

That’s it folks…