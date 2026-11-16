---
name: pyronaut-scripts
description: Create and run scripts in a configured Pyronaut project whenever Codex needs a new script, especially requests normally solved with Python. Applies to standalone utilities, automation, data processing, and temporary helper scripts created during another task. Prefer Java; choose Python when Java would make the implementation substantially longer or more complex.
---

# Pyronaut Scripts

Use Pyronaut for newly created scripts, including temporary helpers needed to complete another task. Create a configured Pyronaut project before executing the script, or reuse an appropriate existing Pyronaut project. Prefer Java source; use Python source when it keeps the implementation substantially shorter and clearer. An explicit user choice of language or runtime takes precedence.

## Choose the language

- Start with Java when its standard library or available JVM dependencies provide a concise solution.
- Choose Python when equivalent Java would require substantially more boilerplate, parsing, data manipulation, or support code. Judge the complete solution, including dependency setup, rather than applying a fixed line-count threshold.
- Run either language through Pyronaut. Choosing Python does not mean switching to CPython, `python`, `uv run`, or a virtual environment.
- Keep ordinary shell commands as commands; this skill does not require wrapping file reads, searches, or existing tool invocations in a new script. It applies when authoring a script, including an inline helper. Preserve existing application code, scripts, and build workflows unless the task calls for changing them.

## Check the installed runtime

Use `command -v pyronaut`, `pyronaut --version`, and `pyronaut run --help` to confirm availability and supported options. Consult local CLI help, installed implementation, or project configuration for unfamiliar features rather than assuming CPython, Java source-launcher, or Maven behavior.

Pyronaut executes Python on GraalPy with JVM integration. Verify required Python packages and native extensions are compatible before choosing them. Do not assume packages installed for system Python are available to Pyronaut.

If Pyronaut is missing or cannot launch, report the concrete blocker and keep the script ready for Pyronaut; do not silently substitute another runtime. Changes to a user's global installation are separate from writing the script.

## Create or reuse a Pyronaut project

Every script needs a Pyronaut project with a `pyproject.toml` containing its Pyronaut configuration. A bare `.java` or `.py` file is not a complete deliverable. Do not use direct source execution as a substitute for project creation.

1. Inspect the destination for an existing Pyronaut project. Reuse it when appropriate; an unrelated `pyproject.toml` alone does not make a directory a Pyronaut project.
2. Otherwise create a dedicated project directory. Use `pyronaut create <project-name>` when its generated project is suitable. Check `pyronaut create --help` first: the installed generator may create a Python application even when Java is preferred. Adapt the generated project for the chosen language, or construct a minimal project from a working local Pyronaut example and the installed configuration schema. Do not invent Java generation flags.
3. Configure the source roots for the chosen language, resources if needed, toolchain, compatible core/platform versions, dependencies, and executable entry point. Keep application logic suitable for a command-line utility; a script does not require an HTTP server. Declare only dependencies the task needs. Preserve an existing project's conventions and never overwrite unrelated configuration.
4. Put source files in the configured roots and ensure the selected entry point runs the script's work and exits. For Java, use a class with `public static void main(String[] args)`; for Python, use the entry-point conventions supported by the installed Pyronaut project. Resolve unfamiliar entry-point settings from installed tools or working examples.
5. Install dependencies and process the project before running it. From the project directory, use the supported project workflow:

```sh
pyronaut install
pyronaut process
pyronaut run
```

For Java, `pyronaut run --main-class=<fully-qualified-class-name>` can select the script's entry point when needed; verify support in `pyronaut run --help`. Determine application-argument forwarding from installed help or working examples rather than inventing syntax.

For temporary helpers, put the complete project in a disposable task directory. For a deliverable, keep the project configuration alongside the sources so others can reproduce execution. Reuse the configured project for related helpers instead of generating a project for each file.

## Write the script inside the project

A minimal Java main class is:

```java
public class Script {
    public static void main(String[] args) throws Exception {
        System.out.println("Hello from Pyronaut");
    }
}
```

A minimal Python starting point is:

```python
print("Hello from Pyronaut")
```

Use a Java filename matching the public class and place it under the configured Java source root. Place Python code under the configured Python source root. Add only the imports, argument handling, and dependencies the actual task needs. Use modules or multiple files when the task warrants them.

## Verify and deliver

Run a safe representative case through Pyronaut and check the result relevant to the task. For scripts that change external state, use a non-mutating path or a disposable fixture unless the real action is already authorized. A separate test suite is unnecessary for a trivial helper when its behavior can be checked directly.

If startup fails before the script executes, distinguish that runtime failure from a script failure. For example, a missing native launcher may trigger a download; HTTP 403 while fetching that launcher does not establish that the source code is invalid. The launcher may suggest `[tool.pyronaut.toolchain] type = "jvm"`; verify that this works with the installed runtime before recommending it, and keep configuration changes local to the task. Do not repeatedly retry an unchanged download failure.

Provide the project directory, source files, `pyproject.toml`, and exact commands to install, process, and run the project. State the working directory for those commands. Briefly explain the Python choice when it materially reduces length, and state any validation that remains blocked.
