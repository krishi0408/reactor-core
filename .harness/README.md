Build & Test reactor-core on Harness CI
=======================================
This is a fork of [reactor/reactor-core](https://github.com/reactor/reactor-core). This project is used for testing Harness CI's capabilities by the CME team at Harness. This file contains instructions on how to run reactor/reactor-core on Harness CI.


- [Harness Fast CI Blog Announcement](https://harness.io/blog/announcing-speed-enhancements-and-hosted-builds-for-harness-ci)
- [Get Started with Harness CI](https://harness.io/products/continuous-integration)

## Setting up this pipeline on Harness CI Hosted Builds

1. Fork [this repository](https://github.com/reactor/reactor-core) into your GitHub account. 

2. If you are new to Harness CI, signup for [Harness CI](https://app.harness.io/auth/#/signup)
 * Select the `Continuous Integration` module and choose the `Starter pipeline` wizard to create your first pipeline using the forked repo from #2.
 * Follow the below steps to set up your pipeline:-
 
   1. Click on `Create Pipeline`. 
   2. Give a `Name` to your pipeline.
   3. You can provide `Description` and `Tags`.(Optional)
   4. Select `Remote` option on `How do you want to setup your pipeline`.
   5. Provide your `Git Connector`. Learn more about [Connectors](https://developer.harness.io/docs/platform/connectors/add-a-git-hub-connector/).
   6. Select your `Repository` here `reactor-core`.
   7. `Git Branch` will automatically be fetched. 
   8. Provide your `YAML Path`as `.harness/{PIPELINE_NAME}.yml`.The root folder `.harness` is required. 
   9. Select `Start`.
   
 * Go to the newly created pipeline and hit the `Triggers` tab. If everything went well, you should see two triggers auto-created. A `Pull Request` trigger and a `Push` trigger. For this exercise, we only need `Pull Request` trigger to be enabled. So, please disable or delete the `Push` trigger.

3. If you are an existing Harness CI user, create a new pipeline to use the cloud option for infrastructure and set up the PR trigger.

4. Add the pipeline.yaml stages in the YAML editor:

```yaml
  tags: {}
  stages:
    - stage:
        name: prepare
        identifier: prepare
        description: ""
        type: CI
        spec:
          cloneCodebase: true
          platform:
            os: Linux
            arch: Amd64
          runtime:
            type: Cloud
            spec: {}
          execution:
            steps:
              - step:
                  type: Run
                  name: "interpret version and run checks"
                  identifier: interpret_version
                  spec:
                    connectorRef: <+input>
                    image: openjdk:8-jdk
                    shell: Sh
                    command: |-
                      ./gradlew qualifyVersionGha
                      ./gradlew check
  properties:
    ci:
      codebase:
        connectorRef: <+input>
        repoName: reactor-core
        build: <+input>
```


> _NOTE: Make sure you modify the connectors with the connectors you create._

5. Create a Pull Request in a new branch by updating the project. (e.g. add a comment or new line). This should invoke a build-in Harness CI

6. Merge the PR after the pipeline execution is successful.

7. Enable GitHub Actions: The repository forked in Step 2 already has a GitHub Actions workflow file added. You can choose to enable this workflow from the Actions tab on GitHub.

8. Create any other Pull Request with a few source or test file changes. You can consider cherry-picking any of the commits from the main repository.

9. This PR will trigger the Harness CI pipeline (as well as GitHub Actions workflow if enabled in Step-9). Here we are implementing a single workflow for GitHub Actions. (Get_Deps_Mac)
