# Run Testim Tests on HyperExecute with TestMu AI (Formerly LambdaTest)

<p align="center">
  <a href="https://www.testmuai.com/"><img src="https://img.shields.io/badge/MADE%20BY%20TestMu%20AI-000000.svg?style=for-the-badge&labelColor=000" alt="Made by TestMu AI"></a>
  <a href="https://www.npmjs.com/package/@testim/testim-cli"><img src="https://img.shields.io/npm/v/@testim/testim-cli.svg?style=for-the-badge&labelColor=000000" alt="Testim CLI version"></a>
  <a href="https://community.testmuai.com/"><img src="https://img.shields.io/badge/Join%20the%20community-blueviolet.svg?style=for-the-badge&labelColor=000000" alt="Community"></a>
</p>

## Getting Started

[TestMu AI](https://www.testmuai.com/) (Formerly LambdaTest) is the world's first full-stack AI Agentic Quality Engineering platform that empowers teams to test intelligently, smarter, and ship faster. Built for scale, it offers a full-stack testing cloud with 10K+ real devices and 3,000+ browsers. With AI-native test management, MCP servers, and agent-based automation, TestMu AI supports Selenium, Appium, Playwright, and all major frameworks.

With TestMu AI (Formerly LambdaTest), you can run Testim automation tests at maximum speed using HyperExecute's intelligent test orchestration.

- [Sign up on TestMu AI](https://www.testmuai.com/register/) (Formerly LambdaTest).
- Follow the [TestMu AI documentation](https://www.testmuai.com/support/docs/) (Formerly LambdaTest) for the full setup walkthrough.

### Prerequisites

- [Node.js](https://nodejs.org/) installed
- A TestMu AI (Formerly LambdaTest) account with HyperExecute access
- HyperExecute CLI downloaded for your OS (see Setup below)

### Setup

Download the HyperExecute CLI binary for your platform into the project's parent directory:

- Mac: https://downloads.lambdatest.com/hyperexecute/darwin/hyperexecute
- Linux: https://downloads.lambdatest.com/hyperexecute/linux/hyperexecute
- Windows: https://downloads.lambdatest.com/hyperexecute/windows/hyperexecute.exe

Set your credentials as environment variables.

**macOS / Linux:**

```bash
export LT_USERNAME="YOUR_USERNAME"
export LT_ACCESS_KEY="YOUR_ACCESS_KEY"
```

**Windows:**

```bash
set LT_USERNAME="YOUR_USERNAME"
set LT_ACCESS_KEY="YOUR_ACCESS_KEY"
```

## Auto-Split Execution with Testim

Auto-split execution lets you run tests at predefined concurrency and distribute them over the available infrastructure. Concurrency can be achieved at different levels — file, module, test suite, test, scenario, etc.

For more information about auto-split execution, check out the [TestMu AI documentation](https://www.testmuai.com/support/docs/).

### Core

Auto-split YAML file (`yaml/testim_win_hyperexecute_autosplit_.yaml`) contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

Global timeout, testSuite timeout, and testSuite step timeout are set to 150 minutes.

The `runson` key determines the platform (or operating system) on which the tests are executed. Here it is set to Windows:

```yaml
runson: win
```

Auto-split is set to true in the YAML file:

```yaml
autosplit: true
```

`retryOnFailure` is set to true, instructing HyperExecute to retry failed command(s). The retry operation is carried out until the number of retries in `maxRetries` are exhausted or the command execution results in a Pass. The concurrency (number of parallel sessions) is set to 4:

```yaml
retryOnFailure: true
maxRetries: 5
concurrency: 4
```

### Pre Steps and Dependency Caching

Dependency caching is enabled in the YAML file to ensure that package dependencies are not downloaded in subsequent runs:

```yaml
# Dependency caching for Windows
cacheKey: '{{ checksum "package.json" }}'
cacheDirectories:
  - node_modules
```

Steps that must run before test execution are listed in the `pre` run step:

```yaml
pre:
  - npm i @testim/testim-cli -g
  - npm install
```

### Post Steps

```yaml
post:
  - cat yaml/win/testim_win_hyperexecute_autosplit.yaml
```

### Test Discovery

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

### Test Runner Command

```yaml
testRunnerCommand: testim run $test  --project <your_project_id> --token $env:testim_token --host 127.0.0.1 --protocol http --port 4449 --browser firefox --grid-username $env:LT_USERNAME --grid-password $env:LT_ACCESS_KEY --selenium-caps-file caps.json
```

### Run tests

#### Auto-split on Windows

```bash
./hyperexecute --config yaml/win/testim_win_hyperexecute_autosplit_.yaml --force-clean-artifacts --download-artifacts
```

#### Auto-split on Linux

```bash
./hyperexecute --config yaml/linux/testim_linux_hyperexecute_autosplit_.yaml --force-clean-artifacts --download-artifacts
```

## Matrix Execution with Testim

Matrix-based test execution runs the same tests across different input combinations. The Matrix directive in HyperExecute YAML file is a key:value pair where value is an array of strings.

For more information about matrix multiplexing, check out the [TestMu AI documentation](https://www.testmuai.com/support/docs/).

### Core

Matrix YAML file (`yaml/win/testim_win_hyperexecute_matrix_.yaml`) contains the following configuration:

```yaml
globalTimeout: 150
testSuiteTimeout: 150
testSuiteStep: 150
```

```yaml
runson: win
```

```yaml
matrix:
  test:
    [
      "tests/examples/01-simple-text-validation.test.js",
      "tests/examples/02-using-locators.test.js",
    ]
```

```yaml
testSuites:
  - testim run $test --project <your_project_id> --token ${env:testim_token} --host 127.0.0.1 --protocol http --port 4449 --browser firefox --grid-username ${env:LT_USERNAME} --grid-password ${env:LT_ACCESS_KEY} --selenium-caps-file caps.json
```

### Pre Steps and Dependency Caching

```yaml
# Dependency caching for Windows
cacheKey: '{{ checksum "package.json" }}'
cacheDirectories:
  - node_modules
```

```yaml
pre:
  - npm i @testim/testim-cli -g
  - npm install
```

### Post Steps

```yaml
post:
  - cat yaml/win/testim_win_hyperexecute_matrix_.yaml
```

### Run tests

#### Matrix on Windows

```bash
./hyperexecute --config yaml/win/testim_win_hyperexecute_matrix_.yaml --force-clean-artifacts --download-artifacts
```

#### Matrix on Linux

```bash
./hyperexecute --config yaml/linux/testim_linux_hyperexecute_matrix_.yaml --force-clean-artifacts --download-artifacts
```

View results on your TestMu AI dashboard.

## Contributions

Contributions are welcome. Open an issue to discuss your idea before submitting a pull request. When reporting bugs, include your Node.js version, OS, and Testim CLI version.

## TestMu AI (Formerly LambdaTest) Community

Connect with testers and developers in the [TestMu AI Community](https://community.testmuai.com/). Ask questions, share what you are building, and discuss best practices in test automation and DevOps.

## TestMu AI (Formerly LambdaTest) Certifications

Earn free [TestMu AI Certifications](https://www.testmuai.com/certifications/) for testers, developers, and QA engineers. Validate your skills in Selenium, Cypress, Playwright, Appium, Espresso and more. Industry-recognized, shareable on LinkedIn, and built by practitioners, not marketers.

## Learning Resources by TestMu AI (Formerly LambdaTest)

Learn modern testing through tutorials, guides, videos, and weekly updates:

* [TestMu AI Blog](https://www.testmuai.com/blog/)
* [TestMu AI Learning Hub](https://www.testmuai.com/learning-hub/)
* [TestMu AI on YouTube](https://www.youtube.com/@TestMuAI)
* [TestMu AI Newsletter](https://www.testmuai.com/newsletter/)

## LambdaTest is Now TestMu AI

On **January 12, 2026**, [LambdaTest evolved to TestMu AI](https://www.testmuai.com/lambdatest-is-now-testmuai/), the world's first fully autonomous **Agentic AI Quality Engineering Platform**.

Same team. Same infrastructure. Same customer accounts. All existing LambdaTest logins, scripts, capabilities, and integrations continue to work without change.

👉 Find the new home for [LambdaTest](https://www.testmuai.com).

### How LambdaTest Evolved into TestMu AI

In 2017, we launched LambdaTest with a simple mission: make testing fast, reliable, and accessible. As LambdaTest grew, we expanded into Test Intelligence, Visual Regression Testing, Accessibility Testing, API Testing, and Performance Testing, covering the full depth of the testing lifecycle.

As software development entered the AI era, testing had to evolve, too. We rebuilt the architecture to be AI-native from the ground up, with autonomous agents that **plan, author, execute, analyze, and optimize tests** while keeping humans in the loop. The platform integrates with your repos, CI, IDEs, and terminals, continuously learning from every code change and development signal.

That evolution earned a new name: **TestMu AI**, built for an AI-first future of quality engineering. TestMu is not a new name for us. It is the name of our annual community conference, which has brought together 100,000+ quality engineers to discuss how AI would reshape testing, long before that became an industry norm.

What started as a high-performance cloud testing platform has transformed into an AI-native, multi-agent system powering a connected, end-to-end quality layer. That evolution defined a new identity: LambdaTest evolved into TestMu AI, built for an AI-first future of quality engineering.

## Support

Got a question? Email [support@testmuai.com](mailto:support@testmuai.com) or chat with us 24x7 from our chat portal.
