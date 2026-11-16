# Java command-line utilities on Pyronaut 0.1.2

Read this when building a finite Java utility using the Micronaut context runner. This pattern uses named command-line configuration properties, a startup listener, and explicit process exit. It avoids relying on an arbitrary main-class option.

## Minimal project

Layout:

```text
pyproject.toml
src/main/java/addition/Add.java
src/main/python/.gitkeep
```

The empty Python source root prevents the default `src` Python scan from including Java sources or nested applications. Explicitly configured source roots must exist.

`pyproject.toml`:

```toml
[project]
name = "addition"
version = "0.1.0"

[tool.pyronaut]
repositories = ["mavenCentral"]

[tool.pyronaut.core]
version = "5.2.22"

[tool.pyronaut.platform]
version = "5.2.5"

[tool.pyronaut.toolchain]
type = "jvm"

[tool.pyronaut.sources]
python = "src/main/python"
java = "src/main/java"

[tool.pyronaut.dependencies]
runtime = []
build = []

[tool.pyronaut.test-resources]
enabled = false

[tool.pyronaut.run]
banner-enabled = false
```

These versions match the tested installation; use compatible values for the installed SDK. Empty dependency lists leave platform defaults in place without adding an application HTTP server dependency.

`src/main/java/addition/Add.java`:

```java
package addition;

import io.micronaut.context.env.Environment;
import io.micronaut.context.event.StartupEvent;
import io.micronaut.runtime.event.annotation.EventListener;
import jakarta.inject.Singleton;
import java.math.BigDecimal;

@Singleton
public class Add {
    private final Environment environment;

    public Add(Environment environment) {
        this.environment = environment;
    }

    @EventListener
    public void onStartup(StartupEvent event) {
        main(new String[] {
            environment.getProperty("left", String.class).orElse(""),
            environment.getProperty("right", String.class).orElse("")
        });
        System.exit(0);
    }

    public static void main(String[] args) {
        if (args.length != 2 || args[0].isBlank() || args[1].isBlank()) {
            System.err.println("Usage: pyronaut run --project-dir=. -- --left=<number1> --right=<number2>");
            System.exit(1);
        }
        try {
            BigDecimal sum = new BigDecimal(args[0]).add(new BigDecimal(args[1]));
            System.out.println(sum.stripTrailingZeros().toPlainString());
        } catch (NumberFormatException e) {
            System.err.println("Both arguments must be valid numbers.");
            System.exit(1);
        }
    }
}
```

`main` holds the calculation and validation. The startup listener is what the Pyronaut context invokes. Explicit exit is appropriate for a standalone CLI process; do not transplant it into a shared server or an in-process test runner.

From the project directory:

```sh
pyronaut install
pyronaut process
pyronaut run --project-dir=. -- --left=5 --right=7
```

The explicit project option matters: in the tested CLI implementation, `pyronaut run -- <args>` can enter direct-source mode and select a different launcher. Named values use `=` so negative numbers are part of the option value, for example `--left=-2.5`.

Verified locally on November 16, 2026 with Pyronaut 0.1.2, Micronaut Core 5.2.22, and Platform 5.2.5:

| Arguments | Result | Exit code |
| --- | --- | --- |
| `--left=5 --right=7` | `12` | 0 |
| `--left=-2.5 --right=3.7` | `1.2` | 0 |
| `--left=hello --right=7` | Validation error | 1 |
| `--left=5` | Usage message | 1 |

The launcher also emits processing/status output and a GraalPy initialization log. Do not claim stdout contains only the numerical result.

## Evidence and boundaries

- [Pyronaut documentation](https://pyronaut.io/docs/): application contexts, configuration injection, startup events, source layout, project commands, and packaging formats.
- [Pyronaut runner source](https://github.com/micronaut-projects/pyronaut/blob/0.1.x/pyronaut-run/src/main/java/io/micronaut/pyronaut/run/PyronautRunMain.java): the context startup path.
- [CLI implementation](https://github.com/micronaut-projects/pyronaut/tree/0.1.x/pyronaut): source/project routing and delegated launchers; inspect the locally installed version when behavior differs.
- [Oracle presentation, November 3, 2026](https://otube.oracle.com/media/Pyronaut+-+Micronaut+on+fire+with+GraalVM++Python/1_80fn9kju/102058562): reviewed through its attached transcript. It explains the precompiled Micronaut/GraalPy application model, project workflow, and Java/Python dependency injection. It does not demonstrate this addition utility or establish arbitrary Java-main execution support.

This pattern intentionally uses named values. If a user requires positional arguments specifically, verify a supported argument API or CLI integration rather than claiming that configuration properties preserve raw positional arguments.
