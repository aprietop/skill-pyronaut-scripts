# Pyronaut Scripts

A Codex skill for creating and running scripts with [Pyronaut](https://github.com/micronaut-projects/pyronaut). Prefers Java and uses Python when it makes the implementation substantially shorter or simpler.

## Behavior

- Uses Pyronaut for new standalone scripts, automation, data processing, and temporary helpers created during other tasks.
- Prefers Java when the implementation remains concise; chooses Python when Java would require substantially more boilerplate or complexity.
- Creates a configured Pyronaut project, or reuses an appropriate existing one, before executing any script.
- Runs both languages through the project's install, process, and run workflow.
- Respects explicit user choices of language or runtime.
- Checks the installed runtime and verifies scripts with a safe representative case when execution is available.

The skill contains instructions for Codex. Pyronaut must be installed separately.

## Requirements

- Codex with support for skills.
- Pyronaut installed and available on your `PATH`.
- Any runtime components and dependencies required by your scripts.

Confirm Pyronaut is available:

```sh
command -v pyronaut
pyronaut --version
pyronaut run --help
```

See the [Pyronaut project](https://github.com/micronaut-projects/pyronaut) for runtime installation and setup.

## Install

Ask Codex:

```text
Use $skill-installer to install the skill from the GitHub repository
aprietop/skill-pyronaut-scripts at path ., with the name pyronaut-scripts.
```

Alternatively, clone the repository into your personal skills directory:

```sh
mkdir -p ~/.codex/skills
git clone https://github.com/aprietop/skill-pyronaut-scripts.git \
  ~/.codex/skills/pyronaut-scripts
```

If you use a custom `CODEX_HOME`, use its `skills` directory instead. Choose one installation method; the destination should not already contain another installation.

## Usage

The skill allows automatic selection when Codex needs to create a script. You can also invoke it explicitly:

```text
Use $pyronaut-scripts to create a script that summarizes a CSV file by category.
```

```text
Use $pyronaut-scripts to create a utility that finds duplicate files by content.
```

Generated scripts include a Pyronaut project with `pyproject.toml`, configured source roots, dependencies, and an executable entry point. From the generated project directory, run:

```sh
pyronaut install
pyronaut process
pyronaut run
```

Codex provides the exact project directory and commands, including `--main-class` when needed for a Java entry point. A bare source file run directly is not the project's execution workflow.

Python scripts use Pyronaut's GraalPy runtime. Packages installed for system Python may not be available or compatible.

## Update

For an installation made with `git clone`:

```sh
git -C ~/.codex/skills/pyronaut-scripts pull --ff-only
```

The skill installer copies files locally; those copies do not automatically track repository updates.

## Repository contents

- [SKILL.md](SKILL.md): Skill selection description and execution instructions.
- [agents/openai.yaml](agents/openai.yaml): Codex display metadata.
- [LICENSE](LICENSE): Apache License 2.0.

## License

Licensed under the [Apache License, Version 2.0](LICENSE).
