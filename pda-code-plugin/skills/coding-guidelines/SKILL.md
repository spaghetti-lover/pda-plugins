---
name: coding-guidelines
description: Coding guidelines for languages used in our firm as well as the development workflow. Use when the user wants to write clean code, refactor for readability, review code quality, or asks about naming, functions, comments, or code smells.
disable-model-invocation: true
---

# Coding guideline

This document outlines coding guidelines for languages used in our firm as well as the development workflow.

Any contribution is welcome.

NOTE:

- New additions are marked with yellow background

- This is supposed to be applicable to the majority of the applications that we are developing only, not rule of thumb for software engineering in general.

# Configuration

Configuration should be via YAML file. Command line arguments should only include simple and likely to change one, for example: config_file, date, mode, etc

The config file must be supplied using --config\*\*\_\*\*file flag (please follow the exact naming).

Config file should be a single file when possible, e.g. avoid config file that links to another config file.

Per date data processing tools should have --date parameters.

There must be a sample, runnable configuration in a project.

Field names should be in snake_case.

Do NOT pass configuration via environment variables. They are essentially global mutable states, and the fact that it can be passed in an implicit manner makes it worse.

# Logging

Logging is used for debugging problems in production, so the aim is to be able to quickly identify problems from reading logs alone. Logs could be read by developer, operator or even normal user, so try to make it as clear as possible, as not only for the one that develops the application.

Logging is stored on a best-effort basis. So availability of logging should not cause the change to the program behavior.

Logging must includes below information

- Time (with timezone)
- Thread ID
- Source file and line number

Error log messages should contain as much context as possible, especially user facing ones, where logs are the only thing a user can read to figure out/report the problem. Below are some examples (bad/good format):

- Invalid format/Invalid uss path format: unknown attribute: vesrion. Please refer to USS docs at...
- Invalid toolchain/Invalid toolchain gcc-13-0.1. Supported toolchains are gcc-12.2.0, gcc-13.0.1
- Does not have permission/User data_oper does not have permission to create job on group hydra

# —specs

All program must support --specs, to declare the input/output/resource usage of a particular program. This will used for scheduling purpose.

The format is as below:

```javascript
{
    "inputs": [
        "uss://us_ob3.consolidated_level1{date=20260617, asset_type=equity}/consolidated.ob3"
    ],
    "outputs": [
        "uss://me{for=remote}"
    ],
    "cpu": {
        "cores": 2.1,
        "memory_mb": 2000
    },
    "gpu": {
        "memory_mb": 2000
    }
}
```

# Exit Code

0 for success run

non-0 for failure run

EX_CONFIG (or 78) for failure due to configuration mistake

# UI/UX

User interface includes both graphic user interface and command line interface. Below is the list of arbitrary rules of thumb, in random order.

- Date must be displayed in year - month - day order, e.g. %Y%m%d, %Y-%m-%d, etc
- There must be timezone information if you want to represent a time point. Do not assume SGT.
- The version of the software must be shown clearly.
- The point of contact in the website or when error happens can be shown clearly. For example
- - In the UI: ![](https://outline.dytechlab.com/api/files.get?sig=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJrZXkiOiJ1cGxvYWRzL2Y1OTVhNDIwLTM5NTEtNGJmNy04ZDU4LWQyZGZhY2Q1NGUxOS9kNmQ4M2I2My0xOThmLTQyMzUtODJkZi0zM2ZlYzg1NDBlY2IvMGpvaElBU0VnQkFRQW1PS2dBaVRNZFZkWXF3UUVBSkNRQWdJZ2ZGTjRQOEQ1NnVDRzJ6enRDVUFBQUFBU1VWT1JLNUNZSUk9IiwidHlwZSI6ImF0dGFjaG1lbnQiLCJpYXQiOjE3ODQ0NzExNDEsImV4cCI6MTc4NTA3NTk0MX0.Q-W74dWgCORSRgBlxMIhdVeSNAWs4nsIp-LZNlwzfik " =550x71")
  - In the logs: "2023-02-01 11:00 SGT \[INFO\] Failed to install package abc:xyz. Please contact **[sysadmin@dytechlab.com](mailto:sysadmin@dytechlab.com)** for more info"
- Error message must be clear (from user perspective) and include way to workaround if possible, e.g.
- - This error message: F20230224 04:42:35.454006 2835381 ref_data.cpp:187\] Check failed: status.ok() Load Error=10: Data \`specs.futures\` for region \`us\` is empty
  - should become:
  - "20230225 is a non-trading date", or "Data is not generated, please contact @data-team for more info".
- There must always be a way to reverse a manual action. In rare cases where an action is irreversible, users need to be asked to confirm again with something that cannot be bypassed quickly (e.g. Are you sure? Y/N? is very easy to mistype/misread).
- Instructions on how to use the website should be inside the website, except when you don't have enough space.
- Everything in a website, and across websites should be consistent: font-size, alignment, color, etc.
- Must/should work with below screen size (with decreasing level of importance):
  - 24 inch monitor (must)
  - Pixel 7 (must for any app that is required for live trading troubleshooting)
  - 32 inch monitor
  - half 24 inch monitor (i.e. divided into 2 windows vertically)

# Framework

- User error should be considered a failure of the framework.

# Project Documents

Each project must include:

- `README` to describe what the project does and how to set up the project to develop locally
- Sample configuration file
- Document on how to configure the application
- Sample data, enough to do meaningful development work.

# Versioning

We use a similar version as **[SemVer](https://semver.org/)** (Semantic Versioning): `<major>.<minor>.<patch>`, except that:

- major: mean large breaking changes
- minor: new features added, there could be breaking changes
- patch: bugs fixes

For example, Given that the current version of the package is 1.7.1:

- If you introduce breaking changes, or new features, _up to the maintainer_, the upcoming version can be either 1.8.0, or 2.0.0. The semantic of whether to upgrade the major or the minor version number is not yet defined solidly.
- If you introduce a version that patches some bug(s), without any breaking changes or feature additions, the next version should be 1.7.2.

That's not here to stay, or at least we hope not. Generally, the goal is to be able to **[live at HEAD](https://www.youtube.com/watch?v=tISy7EJQPzI)**.

# Testing

For each application, please identify the most critical flow and make sure we have a proper testing strategy before every release. Don't try to test everything, we are not at that stage where we can do it without slowing down the development quite a bit. The orders of important of features should be considered with regard to below properties:

- Whether a feature affects order sending logic, or computation that directly translate to PNL
- How many users use the feature (e.g. no one cares if this page is down: **<http://pandora.dtl/packages/stats>**, but if pandora cannot install packages, then suddenly a lot of people will be unhappy).
- Whether there is any workaround if the feature does not work.

When designing applications, it is best that you isolate the critical parts from others. Try to avoid mixing different logic with different levels of reliability requirements together.

To ensure the quality, we can use unit tests, integration tests, manual tests, and simulation. Use the one that makes sense instead of going for the easy one. Most of the time, simulation > integration > unit test, and automated test > manual test. Humans always make mistakes, machines rarely do.

Depending on the stage of a project, we can employ one or multiple of below strategies:

- No test needed. For early, POC kind of projects.
- Test needed when modifying old feature, no test needed for adding new feature
- Test needed on certain parts of the program.
- Every changes need to fail at least a test

One way to measure the effectiveness of a test is on how many tests failed for a new commit. If a test never fails, it probably adds very little value to the repos.

# C++

## Basic Requirements

- Use C++26 (with gcc-15)
- Use Pandora for package management: **<http://pandora.dtl/>**
- Use eidos as base library: **<http://pandora.dtl/packages/base/eidos/>**
- Enable "-Wall -Werror" to ensure new code is free of compiler warnings

## Coding Style

Follow Google Coding Styles: **<https://google.github.io/styleguide/cppguide.html>**, with below exceptions:

- Maximum line width is 100 spaces. We have monitors big enough for 2 code views with 100 columns each. Might need to reduce this in case our eyesight gets worse.
- Exceptions are okay, but try to avoid them, especially in base libraries.
- Use #pragma once
- Use const& for const parameters and pointer for modifiable parameters, without ownership transferring

Pros: On the caller side, there is a distinction between pass by value and by pointer.

Cons: There is no way to express non-null pointers.

Rationale: There are many tools to detect errors caused by null pointers, e.g., address sanitizer and valgrind. Crash caused by null pointers is also easy to debug. On the other hand, mistakes like accidentally copying objects instead of passing by reference are harder to debug, as there is no systematic way to detect them, and they might only affect performance without any effect on application result.

## Design Principles

- **Keep the logic locally**. Avoid scattering them across different places. i.e. most of the commits should change a small set of source files. Avoid updating a dozen files when adding or modifying a single feature.
- **Keep the data locally**. Kind of having similar benefits as the first rule. Besides that, keeping data local to the core can have cache benefits.

  For example, if we have below

```javascript
class A {
	int data1;
	int data2;
};

std::unordered_map<int, A> list_of_a;

# list_of_a is initialized at the beginning of the program
# thread1 only access and write to data1
# thread2 only access and write to data2
# then consider breaking this up:

std::unordered_map<int, int> list_of_data1;  # for thread 1
std::unordered_map<int, int> list_of_data2;  # for thread 2
```

```
**Keep the code explicitly on the hot path**. Avoid RAII, implicit memory allocation, magic, .. in the hot path. Make things as explicitly as possible, so it is easy to reason about performance.

For example,
```

```javascript
void HotPathCode() {
	if (x > 10) [[likely]] {
		...
	} else {
		SlowCode(); // Mark as NEVER_INLINE to prevent polluting
        // the hot path
	}
}
```

```
	Do note that hot path does not always mean the code that runs most frequently.
```

- **Avoid sharing state between different threads**. Sharing state is hard, and slow. Alternative is to use multi-thread queue, **[multiple buffering](https://en.wikipedia.org/wiki/Multiple_buffering)**, etc
- **Avoid dynamic ownership**. Ownership should be determined statically at compile time. Most of the time, there is no reason to have ownership that can be transferred between objects, except maybe the trivial one when constructing object.

## Some minor stuffs

- **Keep the inline code in the header**, and mark them as ALWAYS_INLINE. No reason messing around with link time optimization, we can always do better than that.

## Bazel Project Structure

```javascript
/path/to/target/
├── BUILD.bazel
├── <public header>.h
└── src
    ├── <private header>.h
    └── <private source>.h
```

\nand BUILD file should have something like below

```javascript
cc_binary(
  (name = "target_name"),
  (hdrs = glob(["*.h"])),
  (srcs = glob(["src/*.cpp", "src/*.h"])),
  (include_prefix = "..."),
);
```

Notes

- Use include_prefix instead of relying on folder layout. Having an include path that is different from the folder layout could help to detect BUILD error earlier. Sometimes BUILD file is wrong but build still works because folder structure works with the include path; but that might change with different bazel versions, or whether you are using it as the root workspace or as a deps of another workspace.
- Avoid putting any cpp target into root directory of the workspace

# Python

Use Python 3.6 or above

Do NOT use Python for time-sensitive applications or workloads that need to scale in the near future.

Follow PEP8: **<https://peps.python.org/pep-0008/>**

Libraries:

- click for argument parsing, or argparse, in case you don't want to depend only on python standard libraries
- pyyaml for YAML parsing
- black for code formatting

Use Sentry for error reporting.

# Go

Just use the language default coding convention

- Use **[gofmt](https://pkg.go.dev/cmd/gofmt)**
- Use **[goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports)** (it sometimes could get slow, so we can fallback to gofmt)
- **[Effective Go](https://go.dev/doc/effective_go)**

Common Go mistakes

- **<https://github.com/golang/go/wiki/CodeReviewComments>**

Use sentry for error reporting.

Use zerolog for logging, zerolog format:

```javascript
console := zerolog.ConsoleWriter{
	Out:        os.Stdout,
	TimeFormat: time.RFC3339,
	FormatLevel: func(i interface{}) string {
		return strings.ToUpper(fmt.Sprintf("|%-5s|", i))
	},
	FormatMessage: func(i interface{}) string {
		return fmt.Sprintf(" %s ", i)
	},
	FormatFieldName: func(i interface{}) string {
		return fmt.Sprintf("%s: ", i)
	},
	FormatFieldValue: func(i interface{}) string {
		return fmt.Sprintf("%s", i)
	},
}
```

Use -trimpath flag when build binary for better caller file path in log\n go build -trimpath <target>

# Bash

Follow Google Coding Styles: **<https://google.github.io/styleguide/shellguide.html>**, with below exceptions:

- Lowercase names for functions and variables.
- Uppercase names for constants and env variables.
- Use local for local variables.
- It is not necessary to declare constants with readonly, as the usage is quite uncommon and may be confusing.

# HDL

Following coding guideline for HDL design and testbench environment

- Use system verilog,verilog(preferable) for our language to implement logic.
- Use system verilog for testbench
- Use tab is 4 space to consistent tab space with C++ project
- Add posfix {}\_ff or {}_reg to implement flip flop, prefix mem_{} to implement memory (SDP/TDP)
- Use synchronous reset for all modules.
- FSM should use a completed case. For Moore FSM should use only one always block, Merly could use one more always(@\*) to define the variable update.
- The FSM state should be named conventionally by localparam or enum with specified value. Ex: localparam IDLE = 'D0, ENCODE = 'd1, ...
- Use = {DWIDTH{1'b1}}, to assign all bit 1, try to avoid -1.
- For logarit 2 use $clog2 ,for exponential \*\*.
- For the signal vector, it must be \[N-1:0\] signal_name, For the array it should be declared with array_name \[0:N-1\], avoid using a multidimensional array.
- Align and group code in a consistent way as much as possible.
- Create a helper module only if it feels natural to define its port signals. Otherwise, implement the needed functionality inside the module you are working on and properly group the code.
- For master-slave-like protocols, module ports should indicate with a m* or s* prefix. Do not use any m* or s* prefix for internal signals. For example, use m_axis_tready if the module drives a master AXI-stream interface, and axis_tready for internal usage.

# Javascript (or Frontend)

Use Node version 20 or above.

Packages manager PNPM: <https://pnpm.io/>

Try to use Typescript as much as possible.

Develop a React web application using Next.js framework: **<https://nextjs.org/>**

Use MSW for mocking: **<https://mswjs.io/>**

Use TailwindCSS for styling: **<https://tailwindcss.com/docs>**

Use React-Common for internal component: **<http://react.dtl>**

Follow Airbnb coding styles: **<https://github.com/airbnb/javascript>**

Base Dockerfile:

```bash
# base image
FROM registry.dtl/eng/docker-images/node:24-alpine AS base

RUN apk add --no-cache tzdata
RUN corepack enable
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
ARG NEXT_PUBLIC_BASE_PATH=""
ENV NEXT_PUBLIC_BASE_PATH="$NEXT_PUBLIC_BASE_PATH"


# install packages
FROM base AS packages

WORKDIR /app/web

COPY package.json pnpm-lock.yaml pnpm-workspace.yaml /app/web/

# Use packageManager from package.json
RUN corepack install

RUN pnpm install --frozen-lockfile
```

Also, please consider below points:

- Tab size is 2.
- Must have a trailing comma.
- Always add a semicolon to the end of the line.
- Use double quotes.

# Google Docs

- Do not use blank lines to add margins between paragraphs.
- Code blocks must be in mono font and in different colors.

# Git

Projects should not be created under your own namespace, e.g. `mhoang/project1`. Instead, it should be in a specific designated group, e.g. infra, eng, data, pricing, etc (please check with your team to decide). In case we don't have any designated group yet, or it is just something ad-hoc, please make a group with your own name: g\_<username> instead; for example, `g_mhoang/project1`

Each feature must be pushed to a separate branch.

To merge a feature, send a MR to the master branch. Include the **[TODO](http://todo.dtl)** issue number on the MR name. Use fast forward merge.

In some public repos, where everyone can access, we usually do not give permissions to individual users, so creating a branch is impossible. In that case you want to fork the repos instead.

Regarding MR/commit naming, please consider below points:

- If MR is associated with a **[TODO](http://todo.dtl)** task, start MR name with TODO issue shortcode, e.g. "TRDSYS-812: Add support for ABCXYZ"
- First letter is capitalized unless it is a name
- Bug fixes should start with Fix
- Avoid generic names or commit messages that do not give any information like "fix bug", "add features", etc.
- For non-trivial change, update the changelog file if your project has one
- Avoid large changes that make it difficult for reviewers to review unless they are new major features or when the project is in the early stages.
- Most of the time, we will squash the commits, so the MR name will be used as the commit name. If you want it otherwise, please mention it in the MR.

For example, some valid MR messages:

- "TODO-3: Fix user cannot access their own task"
- "Fix strategy cannot send target if the order is less than 1 lot"
- "FEED-CN-1: Use buffalo"

Some invalid MR messages:

- "Fix bug in ttime.h" - too generic, lacking information.
- "TODO-4: implement statistics::covariance()" - the first letter should be capitalized.

## Gitlab CICD

Some projects are mirrored from gitlab.dtl to gitlab.intern.dtl, and we don't want to run the release stage on the gitlab.intern to avoid double release. Hence, the .gitlab-ci.yml file should have a release block that looks like below:

```javascript
release:
  stage: release
  tags:
    - docker
  only:
    refs:
      - tags
    variables:
      - $CI_SERVER_HOST == "gitlab.dtl"
  script:
    - pandora package
```

Some pipelines should be applied in ci/cd. These scripts below can be applied to the .gitlab-ci.yml file.

1. ### Test

We should also add the script to show the test **[report](https://docs.gitlab.com/ee/ci/testing/unit_test_reports.html)** and **[coverage](https://docs.gitlab.com/ee/ci/testing/code_coverage.html)** of the test. Some example:

1. #### Go

```javascript
test:
  stage: test
  image: golang:1.18.7
  tags:
    - docker
  script:
    - make test-cover-ci
  coverage: '/Total:.*\d+.\d+%/'
  artifacts:
    reports:
    junit: report.xml
```

Script for make file:

```javascript
.PHONY: test-cover-html
test-cover-html: generate
   go test ./... -cover -bench=. -benchmem -coverprofile cover.out
   go tool cover -html=cover.out -o cover.html

.PHONY: test-cover-ci
test-cover-ci: install-go-tools generate
   go install
   gotestsum --junitfile report.xml --format testname
   go test -race -v -coverpkg=./... -coverprofile=cover.tmp.cov ./... -cover bench=. -benchmem
   cat cover.tmp.cov | grep -v "_mock.go" > cover.cov
   rm cover.tmp.cov
   go tool cover -func cover.cov

.PHONY: generate
generate:
   GOFLAGS=-mod=mod go generate ./...

.PHONY: install-go-tools
install-go-tools:
   go install github.com/vektra/mockery/v2@v2.22.1
   go install gotest.tools/gotestsum@latest
```

\nWe can also add the **[badges](https://docs.gitlab.com/ee/user/project/badges.html)** for the repo to show pipeline, test cover.

\n2. ### Code Quality

Should use **[code quality](https://docs.gitlab.com/ee/ci/testing/code_quality.html)** to help to review the code's quality and complexity.

```javascript
include:
 - template: Code-Quality.gitlab-ci.yml

code_quality:
  artifacts:
    paths: [gl-code-quality-report.json]
```

\n3. ### E2E Test

We can **[trigger](https://docs.gitlab.com/ee/ci/triggers/)** the pipeline on e2e test repo. It is called the **[child/downstream pipeline](https://docs.gitlab.com/ee/ci/pipelines/downstream_pipelines.html)**.

- The project should be {group name}/{e2e-test repo name}.
- Should include \`strategy: depend\` to make the parent pipeline fail if e2e pipeline fails.

```javascript
e2e-test:
  stage: e2e-test
  trigger:
    project: gimme/e2e-test
    strategy: depend
```

We should also **[set up](https://docs.gitlab.com/ee/ci/pipelines/schedules.html)** to run e2e tests periodically to get the bug (once a day).

## Mirroring

**NOTE:** Since PULL in mirroring is not supported currently by our gitlab server, we will only be able to configure PUSH mirroring.

To create PUSH mirroring:

1. Login to **<http://gitlab.dtl>** and go to your respective repository
2. On the left pane, go to Settings > Repository > Mirroring repositories
3. Set the Git repository URL and make sure the mirror direction is Push (use ssh://, e.g. ssh://gitlab.intern.dtl/<group>/<repo>)
4. On the Authentication method, you can set it to SSH public key which is the recommended method
5. Once created, there is a copy icon on the right side to copy the SSH public key
6. Login to **<http://gitlab.intern.dtl>** and go to the repository specified
7. Create a new deploy key using the copied SSH public key
8. Make sure that the key has write permission

If the deploy key cannot push to a protected branch, please add the deploy key to "Allowed to push and merge" under Settings > Repository > Protected Branches. This should be done in gitlab.intern.dtl.

![](https://outline.dytechlab.com/api/files.get?sig=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJrZXkiOiJ1cGxvYWRzL2Y1OTVhNDIwLTM5NTEtNGJmNy04ZDU4LWQyZGZhY2Q1NGUxOS9hM2FkZjE1MS1lYWQ4LTQ2ZWItODU4Ni04YTk5ZDFmMTYyZDQvRCtrZGdPNU9TRWlrQUFBQUFFbEZUa1N1UW1DQyIsInR5cGUiOiJhdHRhY2htZW50IiwiaWF0IjoxNzg0NDcxMTQxLCJleHAiOjE3ODUwNzU5NDF9.CxaHfMEsEv2D1QYHqXmVk5iPp61RySHJiJuv4521Bqw " =1071x147")

See **[GitLab docs](http://gitlab.intern.dtl/help/user/project/deploy_keys/index#deploy-key-cannot-push-to-a-protected-branch)** for more details.

\n
