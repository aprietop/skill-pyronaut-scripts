---
name: pyronaut-scripts
description: Create and run scripts with Pyronaut whenever Codex needs a new script, especially requests normally solved with Python. Applies to standalone utilities, automation, data processing, and temporary helper scripts created during another task. Prefer Java; choose Python when Java would make the implementation substantially longer or more complex.
---

# Pyronaut Scripts

Use Pyronaut for newly created scripts, including temporary helpers needed to complete another task. Prefer Java source; use Python source when it keeps the implementation substantially shorter and clearer. An explicit user choice of language or runtime takes precedence.

## Choose the language

- Start with Java when its standard library or available JVM dependencies provide a concise solution.
- Choose Python when equivalent Java would require substantially more boilerplate, parsing, data manipulation, or support code. Judge the complete solution, including dependency setup, rather than applying a fixed line-count threshold.
- Run either language through Pyronaut. Choosing Python does not mean switching to CPython, `python`, `uv run`, or a virtual environment.
- Keep ordinary shell commands as commands; this skill does not require wrapping file reads, searches, or existing tool invocations in a new script. It applies when authoring a script, including an inline helper. Preserve existing application code, scripts, and build workflows unless the task calls for changing them.

## Check the installed runtime

Use `command -v pyronaut`, `pyronaut --version`, and `pyronaut run --help` to confirm availability and supported options. Consult local CLI help, installed implementation, or project configuration for unfamiliar features rather than assuming CPython, Java source-launcher, or Maven behavior.

Pyronaut executes Python on GraalPy with JVM integration. Verify required Python packages and native extensions are compatible before choosing them. Do not assume packages installed for system Python are available to Pyronaut.

If Pyronaut is missing or cannot launch, report the concrete blocker and keep the script ready for Pyronaut; do not silently substitute another runtime. Changes to a user's global installation are separate from writing the script.

## Write and run the smallest suitable script

For a standalone task, prefer direct source execution over scaffolding an application or HTTP server:

```sh
pyronaut run Script.java
pyronaut run script.py
```

A minimal Java starting point is:

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

Use a Java filename matching the public class. Add only the imports, argument handling, and dependencies the actual task needs. Use Python modules or multiple Java files when the task warrants them; Pyronaut accepts source directories as well as individual files.

For dependencies or a larger existing Pyronaut project, inspect its `pyproject.toml` and reuse its source layout and dependency configuration. CLI help exposes `--setup=<pyproject.toml>` for staging configuration during direct source execution. Determine dependency coordinates, versions, entry points, and application-argument forwarding from the installed tools or working project examples; do not invent option syntax. Never replace an unrelated project's configuration just to run a helper.

## Verify and deliver

Run a safe representative case through Pyronaut and check the result relevant to the task. For scripts that change external state, use a non-mutating path or a disposable fixture unless the real action is already authorized. A separate test suite is unnecessary for a trivial helper when its behavior can be checked directly.

If startup fails before the script executes, distinguish that runtime failure from a script failure. For example, a missing native launcher may trigger a download; HTTP 403 while fetching that launcher does not establish that the source code is invalid. The launcher may suggest `[tool.pyronaut.toolchain] type = "jvm"`; verify that this works with the installed runtime before recommending it, and keep configuration changes local to the task. Do not repeatedly retry an unchanged download failure.

Provide the script and exact Pyronaut command. Briefly explain the Python choice when it materially reduces length, and state any validation that remains blocked.
