---
name: pyronaut-scripts
description: Create and run scripts in configured Pyronaut projects, including standalone utilities, automation, data processing, and temporary helpers. Prefer Java; choose Python when it substantially simplifies the complete solution.
---

# Pyronaut Scripts

Use Pyronaut for newly authored scripts. Create a configured Pyronaut project or reuse an appropriate existing project, and verify that its application entry point performs the requested work and exits. Prefer Java when the complete solution is concise; choose Python when equivalent Java needs substantially more parsing, data manipulation, or support code. An explicit user language or runtime choice takes precedence.

Ordinary shell commands and existing tools remain commands; do not wrap file reads, searches, or existing application workflows in new scripts.

## Understand the runtime before choosing an entry point

Check `command -v pyronaut`, `pyronaut --version`, and `pyronaut run --help`. Use the installed implementation, generated schemas, and [official documentation](https://pyronaut.io/docs/) to resolve unfamiliar behavior. Help describes intended options; a successful representative execution establishes that the installed delegated launcher actually supports them.

Pyronaut is a Micronaut application platform with source processing, dependency injection, and GraalPy integration. It is not equivalent to the Java source launcher or CPython. Processing a class containing `public static void main(String[] args)` does not imply that `pyronaut run` invokes it. Likewise, a Python file is not automatically a standalone script entry point just because its sources process successfully.

For a Java command-line utility, use a supported Micronaut startup bean when the project runner boots an application context. The bean performs the work and explicitly exits. Named application arguments can be read as configuration properties. For a tested Java pattern and version-specific argument forwarding, read [references/java-cli.md](references/java-cli.md).

Use an arbitrary main-class override only after verifying that the installed runner accepts it. Pyronaut 0.1.2 installations can advertise `run --main-class` in orchestrator help while the delegated runner rejects it. Do not repeatedly retry that option or switch to wheel packaging merely to run a local utility.

Python runs on embedded GraalPy with JVM integration. Verify required packages and native extensions against that runtime; system CPython packages are not evidence that those packages are available to Pyronaut. Keep Python execution inside the supported Pyronaut workflow.

## Configure the project

Inspect the destination before writing. Preserve unrelated applications and configuration. A standalone deliverable needs its source and `pyproject.toml` together; temporary helpers belong in a disposable task project.

Check `pyronaut create --help` before using the generator; it may generate a Python HTTP application. For a small Java utility, construct a minimal project from a working local example and the installed configuration schema rather than adding an unnecessary HTTP server.

Configure:

- Project name and version, repositories, and compatible core/platform versions from the installed runtime or a working project.
- JVM toolchain when using the JVM launcher, and only the dependencies the task needs.
- Explicit source roots for mixed-language or nested workspaces. Ensure explicitly configured source directories exist. Avoid letting the default Python root accidentally include unrelated Java sources or neighboring applications.
- A runnable startup path appropriate to the chosen language. Ordinary static Java main methods may remain reusable logic, but need a supported application entry point.

Generated schemas and dependency manifests are build state, not handwritten application code. Use the generated-directory name actually produced by the installed version.

## Install, process, and execute

Run from the project directory:

```sh
pyronaut install
pyronaut process
pyronaut run --project-dir=. -- <application-arguments>
```

Verify argument forwarding with the installed CLI. In the tested 0.1.2 implementation, an initial `--` followed by arguments can be classified as direct-source mode. An explicit `--project-dir=.` before the separator selects project execution. See the Java reference for a concrete working invocation.

Use `run` for a finite utility; `dev` starts a watcher. Disable the banner when useful for command-line output. Ensure success and error paths terminate with appropriate exit codes. Do not scaffold an HTTP route solely to execute a calculation.

## Diagnose and verify

Run a representative case through Pyronaut, check the actual result and exit code, and exercise relevant input failures. A trivial helper needs no separate test suite. For state-changing scripts, use a non-mutating path or disposable fixture unless the real action is authorized.

Distinguish installation, dependency resolution, processing, launcher initialization, and application failures. A source that processes successfully is not yet a verified executable utility.

For runtime problems, consult `pyronaut doctor` and the failing delegated tool. A sandbox denial on a Maven or Pyronaut cache lock is an access issue, not an unresolved dependency. Use the normal approval mechanism for required cache access.

If native-launcher provisioning fails, do not repeatedly retry an unchanged download. A local `[tool.pyronaut.toolchain] type = "jvm"` can select the JVM workflow, but verify it with the actual project command. Keep configuration changes local. Global runtime upgrades, repairs, or package installation are separate from writing the script; do not silently modify the user's installation or substitute another runtime.

Deliver the project directory, source/configuration links, exact install/process/run commands with their working directory, and observed results. Clearly state any remaining blocker. Explain a Python choice when it materially simplifies the solution.
