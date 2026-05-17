# Run Testim Tests on TestMu AI (Formerly LambdaTest) HyperExecute

<p align="center">
  <a href="https://www.testmuai.com/"><img src="https://img.shields.io/badge/MADE%20BY%20TestMu%20AI-000000.svg?style=for-the-badge&labelColor=000" alt="Made by TestMu AI"></a>
  <a href="https://community.testmuai.com/"><img src="https://img.shields.io/badge/Join%20the%20community-blueviolet.svg?style=for-the-badge&labelColor=000000" alt="Community"></a>
</p>

HyperExecute is a smart test orchestration platform to run end-to-end Selenium tests at the fastest speed possible. HyperExecute lets you achieve an accelerated time to market by providing a test infrastructure that offers optimal speed, test orchestration, and detailed execution logs.

The overall experience helps teams test code and fix issues at a much faster pace. HyperExecute is configured using a YAML file. Instead of moving the Hub close to you, HyperExecute brings the test scripts close to the Hub!

## Getting Started

TestMu AI (Formerly LambdaTest) is an AI-native, multi-agent quality engineering platform. HyperExecute, part of the TestMu AI (Formerly LambdaTest) platform, enables you to run Testim automation tests at maximum speed with intelligent test orchestration.

- [Sign up on TestMu AI](https://www.testmuai.com/register/) (Formerly LambdaTest).
- Follow the [TestMu AI Documentation](https://www.testmuai.com/support/docs/) for the full setup walkthrough.

## Gitpod

Follow the below steps to run Gitpod button:

1. Click '**Open in Gitpod**' button (You will be redirected to Login/Signup page).
2. Login with TestMu AI (Formerly LambdaTest) credentials and it will be redirected to Gitpod editor in new tab and current tab will show hyperexecute dashboard.

# How to run Automation tests on HyperExecute (using Testim)

- [Pre-requisites](#pre-requisites)

  - [Download HyperExecute CLI](#download-hyperexecute-cli)
  - [Configure Environment Variables](#configure-environment-variables)

- [Auto-Split Execution with Testim](#auto-split-execution-with-testim)

  - [Core](#core)
  - [Pre Steps and Dependency Caching](#pre-steps-and-dependency-caching)
  - [Post Steps](#post-steps)
  - [Artifacts Management](#artifacts-management)
  - [Test Execution](#test-execution)

- [Matrix Execution with testim](#matrix-execution-with-testim)

  - [Core](#core-1)
  - [Pre Steps and Dependency Caching](#pre-steps-and-dependency-caching-1)
  - [Post Steps](#post-steps-1)
  - [Artifacts Management](#artifacts-management-1)
  - [Test Execution](#test-execution-1)

- [Secrets Management](#secrets-management)
- [Navigation in Automation Dashboard](#navigation-in-automation-dashboard)

# Pre-requisites

Before using HyperExecute, you have to download HyperExecute CLI corresponding to the host OS. Along with it, you also need to export the environment variables _LT_USERNAME_ and _LT_ACCESS_KEY_ that are available in the TestMu AI (Formerly LambdaTest) Profile page.

## Download HyperExecute CLI

HyperExecute CLI is the CLI for interacting and running the tests on the HyperExecute Grid. The CLI provides a host of other useful features that accelerate test execution. In order to trigger tests using the CLI, you need to download the HyperExecute CLI binary corresponding to the platform (or OS) from where the tests are triggered:

Also, it is recommended to download the binary in the project's parent directory. Shown below is the location from where you can download the HyperExecute CLI binary:

- Mac: https://downloads.lambdatest.com/hyperexecute/darwin/hyperexecute
- Linux: https://downloads.lambdatest.com/hyperexecute/linux/hyperexecute
- Windows: https://downloads.lambdatest.com/hyperexecute/windows/hyperexecute.exe

## Configure Environment Variables

Before the tests are run, please set the environment variables LT_USERNAME & LT_ACCESS_KEY from the terminal. The account details are available on your TestMu AI (Formerly LambdaTest) Profile page.

For macOS:

```bash
export LT_USERNAME=LT_USERNAME
export LT_ACCESS_KEY=LT_ACCESS_KEY
```

For Linux:

```bash
export LT_USERNAME=LT_USERNAME
export LT_ACCESS_KEY=LT_ACCESS_KEY
```

For Windows:

```bash
set LT_USERNAME=LT_USERNAME
set LT_ACCESS_KEY=LT_ACCESS_KEY
```

## Auto-Split Execution with testim

Auto-split execution mechanism lets you run tests at predefined concurrency and distribute the tests over the available infrastructure. Concurrency can be achieved at different levels - file, module, test suite, test, scenario, etc.

For more information about auto-split execution, check out the [TestMu AI Documentation](https://www.testmuai.com/support/docs/)

### Core

Auto-split YAML file (_yaml/testim_win_hyperexecute_autosplit_.yaml\_) in the repo contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

Global timeout, testSuite timeout, and testSuite timeout are set to 150 minutes.

The _runson_ key determines the platform (or operating system) on which the tests are executed. Here we have set the target OS as Windows.

```yaml
runson: win
```

Auto-split is set to true in the YAML file.

```yaml
 autosplit: true
```

_retryOnFailure_ is set to true, instructing HyperExecute to retry failed command(s). The retry operation is carried out till the number of retries mentioned in _maxRetries_ are exhausted or the command execution results in a _Pass_. In addition, the concurrency (i.e. number of parallel sessions) is set to 4.

```yaml
retryOnFailure: true
maxRetries: 5
concurrency: 4
```

### Pre Steps and Dependency Caching

Dependency caching is enabled in the YAML file to ensure that the package dependencies are not downloaded in subsequent runs. The first step is to set the Key used to cache directories. The directory _node_modules_ is created in the project's root directory.

```yaml
# Dependency caching for Windows
cacheKey: '{{ checksum "package.json" }}'
cacheDirectories:
  - node_modules
```

Steps (or commands) that must run before the test execution are listed in the _pre_ run step. In the example, the Node packages are downloaded in the _node_modules_. 

```yaml
pre:
  - npm i @testim/testim-cli -g
  - npm install
```

### Post Steps

Steps (or commands) that need to run after the test execution are listed in the _post_ step. In the example, we _cat_ the contents of _yaml/win/testim_win_hyperexecute_autosplit_.yaml\_

```yaml
post:
  - cat yaml/win/testim_win_hyperexecute_autosplit.yaml
```

The _testDiscovery_ directive contains the command that gives details of the mode of execution, along with detailing the command that is used for test execution. Here, we are fetching the list of js test files that would be further passed in the _testRunnerCommand_

```yaml
testDiscovery:
  type: raw
  mode: dynamic
  command: grep -lr 'test' tests
```

Running the above command on the terminal will give a list of scenarios present in the tests directory:

- tests/examples/03-using-hooks.test.js
- tests/examples/05-multiple-windows.test.js
- tests/examples/01-simple-text-validation.test.js
- tests/examples/02-using-locators.test.js
- tests/examples/04-skip-and-only.test.js

The _testRunnerCommand_ contains the command that is used for triggering the test. The output fetched from the _testDiscoverer_ command acts as an input to the _testRunner_ command.

```yaml
testRunnerCommand: testim run $test  --project <your_project_id> --token $env:testim_token --host 127.0.0.1 --protocol http --port 4449 --browser firefox --grid-username $env:LT_USERNAME --grid-password $env:LT_ACCESS_KEY --selenium-caps-file caps.json
```

### Test Execution

The CLI option _--config_ is used for providing the custom HyperExecute YAML file (i.e. _yaml/win/testim_win_hyperexecute_autosplit_.yaml* for Windows and \_yaml/linux/testim_linux_hyperexecute_autosplit*.yaml\_ for Linux).

#### Execute testim tests using Autosplit mechanism on Windows platform

Run the following command on the terminal to trigger the testim tests with HyperExecute platform set to Windows. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/win/testim_win_hyperexecute_autosplit_.yaml --force-clean-artifacts --download-artifacts
```

#### Execute testim tests using Autosplit mechanism on Linux platform

Run the following command on the terminal to trigger the testim tests with HyperExecute platform set to Linux. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/linux/testim_linux_hyperexecute_autosplit_.yaml --force-clean-artifacts --download-artifacts
```

<img width="1414" alt="testim_autosplit_execution" src="images/dashboard.png">

Shown below is the execution screenshot when the YAML file is triggered from the terminal:

<img width="1412" alt="testim_autosplit_cli1_execution" src="images/cli.png">

# Matrix Execution with testim

Matrix-based test execution is used for running the same tests across different test (or input) combinations. The Matrix directive in HyperExecute YAML file is a _key:value_ pair where value is an array of strings.

Also, the _key:value_ pairs are opaque strings for HyperExecute. For more information about matrix multiplexing, check out the [TestMu AI Documentation](https://www.testmuai.com/support/docs/)

### Core

In the current example, matrix YAML file (_yaml/win/testim_win_hyperexecute_matrix_.yaml\_) in the repo contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

Global timeout, testSuite timeout, and testSuite timeout are set to 150 minutes.

The target platform is set to win. Please set the _[runson]_ key to _[mac]_ if the tests have to be executed on the macOS platform.

```yaml
runson: win
```

The _matrix_ constitutes of the following entries - _tests_. The entries represent the class names in the test code.

```yaml
matrix:
  test:
    [
      "tests/examples/01-simple-text-validation.test.js",
      "tests/examples/02-using-locators.test.js",
    ]
```

The _testSuites_ object contains a list of commands (that can be presented in an array). In the current YAML file, commands for executing the tests are put in an array (with a '-' preceding each item).

```yaml
testSuites:
  - testim run $test --project <your_project_id> --token ${env:testim_token} --host 127.0.0.1 --protocol http --port 4449 --browser firefox --grid-username ${env:LT_USERNAME} --grid-password ${env:LT_ACCESS_KEY} --selenium-caps-file caps.json
```

### Pre Steps and Dependency Caching

Dependency caching is enabled in the YAML file to ensure that the package dependencies are not downloaded in subsequent runs. The first step is to set the Key used to cache directories. The directory _node_modules_ is created in the project's root directory.

```yaml
# Dependency caching for Windows
cacheKey: '{{ checksum "package.json" }}'
cacheDirectories:
  - node_modules
```

Steps (or commands) that must run before the test execution are listed in the _pre_ run step. In the example, the Node packages are downloaded in the _node_modules_. 

```yaml
pre:
  - npm i @testim/testim-cli -g
  - npm install
```

### Post Steps

Steps (or commands) that need to run after the test execution are listed in the _post_ step. In the example, we _cat_ the contents of _yaml/win/testim_win_hyperexecute_matrix_.yaml\_

```yaml
post:
  - cat yaml/win/testim_win_hyperexecute_matrix_.yaml
```

## Test Execution

The CLI option _--config_ is used for providing the custom HyperExecute YAML file (i.e. _yaml/matrix/testim_win_hyperexecute_matrix_.yaml* on Windows and \_yaml/linux/testim_linux_hyperexecute_matrix*.yaml\_ on Linux).

#### Execute testim tests using Matrix mechanism on Windows platform

Run the following command on the terminal to trigger the testim tests with HyperExecute platform set to Windows. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/win/testim_win_hyperexecute_matrix_.yaml --force-clean-artifacts --download-artifacts
```

#### Execute testim tests using Autosplit mechanism on Linux platform

Run the following command on the terminal to trigger the testim tests with HyperExecute platform set to Linux. The _--download-artifacts_ option is used to inform HyperExecute to download the artifacts for the job. The _--force-clean-artifacts_ option force cleans any existing artifacts for the project.

```bash
./hyperexecute --config yaml/linux/testim_linux_hyperexecute_matrix_.yaml --force-clean-artifacts --download-artifacts
```

<img width="1414" alt="testim_matrix_execution" src="images/linux_dashboard.png">

Shown below is the execution screenshot when the YAML file is triggered from the terminal:

<img width="1413" alt="testim_cli1_execution" src="images/cli.png">

## Secrets Management

In case you want to use any secret keys in the YAML file, the same can be set by clicking on the _Secrets_ button the dashboard.

<img width="703" alt="testim_secrets_key_1" src="https://user-images.githubusercontent.com/1688653/152540968-90e4e8bc-3eb4-4259-856b-5e513cbd19b5.png">

Now create a _secret_ key that you can use in the HyperExecute YAML file.

<img width="359" alt="testim_management_1" src="https://user-images.githubusercontent.com/1688653/153250877-e58445d1-2735-409a-970d-14253991c69e.png">

All you need to do is create an environment variable that uses the secret key:

```yaml
env:
  PAT: ${{ .secrets.testKey }}
```

## Navigation in Automation Dashboard

HyperExecute lets you navigate from/to _Test Logs_ in Automation Dashboard from/to _HyperExecute Logs_. You also get relevant get relevant Selenium test details like video, network log, commands, Exceptions & more in the Dashboard. Effortlessly navigate from the automation dashboard to HyperExecute logs (and vice-versa) to get more details of the test execution.

Shown below is the HyperExecute Automation dashboard which also lists the tests that were executed as a part of the test suite:

<img width="1429" alt="testim_hyperexecute_automation_dashboard" src="https://user-images.githubusercontent.com/1688653/160457830-2dd0789b-5f6d-4cb1-9826-910fcddf6986.png">

Here is a screenshot that lists the automation test that was executed on the HyperExecute grid:

<img width="1429" alt="testim_testing_automation_dashboard" src="https://user-images.githubusercontent.com/1688653/159757703-4dfb2778-df76-4a4c-9991-b3ecfc52bf85.png">

## LambdaTest is Now TestMu AI

On **January 12, 2026**, [LambdaTest evolved to TestMu AI](https://www.testmuai.com/lambdatest-is-now-testmuai/), the world's first fully autonomous **Agentic AI Quality Engineering Platform**.

Same team. Same infrastructure. Same customer accounts. All existing LambdaTest logins, scripts, capabilities, and integrations continue to work without change.

ð Find the new home for [LambdaTest](https://www.testmuai.com).

### How LambdaTest Evolved into TestMu AI

In 2017, we launched LambdaTest with a simple mission: make testing fast, reliable, and accessible. As LambdaTest grew, we expanded into Test Intelligence, Visual Regression Testing, Accessibility Testing, API Testing, and Performance Testing, covering the full depth of the testing lifecycle.

As software development entered the AI era, testing had to evolve, too. We rebuilt the architecture to be AI-native from the ground up, with autonomous agents that **plan, author, execute, analyze, and optimize tests** while keeping humans in the loop. The platform integrates with your repos, CI, IDEs, and terminals, continuously learning from every code change and development signal.

That evolution earned a new name: **TestMu AI**, built for an AI-first future of quality engineering. TestMu is not a new name for us. It is the name of our annual community conference, which has brought together 100,000+ quality engineers to discuss how AI would reshape testing, long before that became an industry norm. 

What started as a high-performance cloud testing platform has transformed into an AI-native, multi-agent system powering a connected, end-to-end quality layer. That evolution defined a new identity: LambdaTest evolved into TestMu AI, built for an AI-first future of quality engineering.

## Support

Got a question? Email [support@testmuai.com](mailto:support@testmuai.com) or chat with us 24x7 from our chat portal.
