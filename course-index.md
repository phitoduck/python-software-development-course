# Course Index - Taking Python to Production: A Professional Onboarding Guide

> ⚠️ Code examples including config files and CLI tools may go out of date because tools
> change often. Check the documentation for the tool you are using if you run into problems.
> You should really get into the habit of referring to offical docs anyway!

## Unit x: 

### Pylint Part 3 - Configuring `pylint`

#### Notes

-  List of all Pylint rules **link**
-  "A foolish consistency is the hobgoblin of little minds." -PEP8 Every good rule has an exception. Follow linting rules in general, and take time to understand what they are trying to achieve. "Pragmatism is the rule." The beauty of Python is that you *can* "break rules" when you have a valid reason to.
- Ignore comments like `# pylint: disable=<some symbolic message or code>` statements, e.g. `pylint: disable=too-many-attributes` silences a single occurence of a Pylint error.
  - The fact that these go in your code makes it easy for code reviewers to see and therefore discuss why an exception was warranted. Additional comments *may* be desirable to preempt even those sorts of conversations.
  - Prefer using the human-readable "symbolic message" e.g. `too-many-attributes` over using the cryptic, error codes, e.g. `C0115` to save reviewers from having to look them up. 
  - Most linters support ignore comments, though the exact syntax varies

- You can disable rules for an entire project, too. Some pylint issues may not provide enough value to justify having developers spend time satisfying them.
  - Method 1: Pass arguments to `pylint`
    - `pylint --disable=too-many-attributes,missing-class-docstring <python files>` disable particular errors
    - `pylint --disable=C <python files>` disables all "convention violations" (error codes that start with `C`)
    - `pylint --enable=E <python files>` only enable errors
  - Method 2: A config file (`.pylintrc`)
    - You can generate a starter file with `pylint --generate-rcfile > .pylintrc`
    - `pylint` will pick up this config file is in the current working directory
    - Explicitly point `pylint` at a config file with `pylint --rcfile path/to/.pylintrc`
- Teams should have a standard config file for any linter they use, including `pylint` if they choose to use it.
- Avoid disabling too many rules. Try to use the defaults. This avoids "Holy War" arguments and encourages developers to learn best practices. Let tools be opinionated for you. But be pragmatic.
- There is no "magic" `pylint` ruleset. It's up for you or your team to decide. Start by enabling *every* `pylint` rule and taking the time to learn what they mean as they come up. You can incrementally remove rules if you or your team agree they are unreasonable. But you should always understand the *why* behind the rule before you do so. Books like *Clean Code* and *Refactoring* will go a long way toward that.

#### Assignments

##### Generate a `.pylintrc` file

1. Generate the file
2. Validate that `pylint` respects your file by disabling/enabling a specific rule
   and running it on some code you know has that violation

##### Configure VS Code for `pylint`

1. Install the `pylint` extension by Microsoft if you have not already done so.
2. Configure your `.vscode/settings.json`

```jsonc
{
    // ...
    "pylint.args": ["--rcfile=${workspaceFolder}/.pylintrc"],
}
```

##### Start creating your own `pylint` ruleset

Apply `pylint` to some code and refactor *every* violation.

This will be slow initially, but you will learn a lot. You will find that you remember the rules and in the future, you will naturally write code that does not have those violations.

### Flake8

#### Notes

- Pylint is not the only Python linter
- Pylint and Flake8 catch many of the same things, but each catch some the other does not so many projects and teams choose to run both
- Pylint supports plugins and there are *many*. Here is an open-source index of them **Link to awesome flake8 plugins**
- `pip install flake8`

Configuration options

Use the Flake8 support within the Python extension:

```jsonc
// .vscode/settings.json

{
    "python.linting.flake8Enabled": true
}
```

OR use the dedicated Flake8 extension. Installing the extension is enough to enable Flake8 hints in VS Code.

- Disable comments: 
  - `# noqa: E` ignore all errors
  - `# noqa: E305` ignore a specific error
  - Flake8 doesn't have a human-readable "symbolic message" like Pylint does! Very sad.
- The standard Flake8 config file name is `.flake8`
- `.flake8` uses the INI file format which has some major downsides over formats like TOML, YAML, JSON, etc. 
  - There are plugins, e.g. `Flake8-pyproject` that enable TOML support for `flake8`. Pylint supports TOML natively. A future section will explore configuring all of our code quality tools with TOML.

Example `.flake8` config file

```ini
# .flake8

[flake8]
extend-ignore =
    E305
```

- `flake8 --config path/to/.flake8 <python files>` directs Flake8 at this file

#### Assignments

##### Configure VS Code for Flake8

1. Install the `Flake8` extension by Microsoft if you have not already done so.
2. Configure your `.vscode/settings.json`

```jsonc
// .vscode/settings.json

{
    // ...
    "flake8.args": ["--config=${workspaceFolder}/.flake8"]
}
```

### Flake8 Plugin example: Darglint

#### Notes

- You can find lots of Flake8 plugins here **Link to awesome list**
- Installing plugins is easy. You just `pip install ...` the plugin and you are done!
- Flake8 Plugins may:
  - Add rules that Flake8 looks for. Since Flake8 integrates with VS Code, installing standalone tools as Flake8 plugins allows you to surface the rules of that tool in the VS Code `Problems` tab and get squiggly lines from that tool. For example,
    - `darglint` adds rules that make sure function arguments are documented in docstrings. `darglint` error codes start with `DAR`. It's common for tools to have a prefix for their rules.
    - `flake8-docstrings` is a wrapper around `pydocstrings` that adds rules that ensure docstrings
      follow a consistent format.
    - `flake8-spellcheck` adds spelling violation rules
  - Add command line arguments to `flake8` that are not available otherwise. For example, `Flake8-Pyproject` adds a `--toml-config` argument which allows you to point Flake8 at a file such as `pyproject.toml` or `flake8.toml`. TOML is superior to the INI format.
  - Add config options for the `.flake8` config file that would not otherwise do anything.
- Some tools, like one called `radon`, do not support `# noqa` statements, but since Flake8 does,
  if you install a tool as a Flake8 statement, you can take advantage of Flake8's `# noqa: <code>`
  comment engine to silence errors for that tool!

```ini
# .flake8

[flake8]
# config option only available when darglint is installed
docstring_style=google
```

#### Assignment

This is completely optional. If this assignment does not seem interesting, skip ahead.

1. Install `darglint` with `pip install darglint`. This automatically registers `darglint` as a Flake8 plugin.
2. Run `darglint` on some of your code and see if it catches any docstring errors.
3. Add an ignore statement for a `DAR` error in the `.flake8` config file to see that Flake8 respects Darglint's rules.

### isort

#### Notes

- `isort` is a standalone CLI tool for organizing imports
- the benefits of sorting imports include:
  - easier to find imports when there are many
  - easier to distinguish which types of imports are in a file (3rd party, 1st party, standard library, etc.)
  - reduces merge conflicts if imports are made to span multiple lines (`isort --fgw <python files>`)
- find the `isort` [config file reference here](https://pycqa.github.io/isort/docs/configuration/options.html#force-grid-wrap)
- `isort` is a code formatter, and so is `black`. `isort` might make some changes that `black` would undo. For example, if `isort` and `black` are configured with different line lengths, `isort` might put imports on multiple lines only to have `black` put them back. Manually specifying `--line-length` or `--profile=black` on `isort` to agree with black will fix this problem.

Example `.isort.cfg` file:

```ini
# .isort.cfg
[settings]
multi_line_output = VERTICAL_HANGING_INDENT
force_grid_wrap = 2
line_length = 99
profile = black
```

#### Assignments

##### Configure VS Code for isort

1. Install the `isort` extension by Microsoft if you have not already done so.
2. Write an `.isort.cfg` file
3. Configure your `.vscode/settings.json`

Example `.isort.cfg` file:

```jsonc
// .vscode/settings.json

{
    // ...
    "isort.args": ["--settings=${workspaceFolder}/.isort.cfg"],
    "[python]": {
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        },
        // enable black formatting
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "ms-python.black-formatter",
    },
}
```

4. Validate that `isort` respects your file by running it on some code you know has that violation

### Code Complexity: Radon, Xenon, McCabe

#### Notes

- Clean code is secure, performant, and maintainable. Maintainable might be the most important. If code is easy to change, you can iterate to the others. *Readability* is key for code being easy to change. You should keep the complexity of your code low to help it be readable.
- Radon and McCabe have code complexity metrics like cyclomatic complexity, maintainability index, etc. 
  - [This page is a great reference](https://radon.readthedocs.io/en/latest/intro.html) for how Radon calculates these code complexity metrics.
  ![](./assets/cyclomatic-complexity.png)
- These metrics can be used to alert you if code is getting too complex.
- You can use these tools to set a complexity threshold.
- Metrics can be "gamed". Just because you bring code beneath Radon's threshold does not mean it is readable. Metrics are just a tool. As always, pragmatism is the rule. Use your judgement.
- Radon is a standalone CLI tool, but it is also a Flake8 plugin. To install it, just run `pip install radon` and it will auto-register with Flake8. Then you can use `# noqa: <radon error>` in exceptional cases to disable particular block of code that violates your complexity threshold.
- Usage:
  - `pip install radon`
  - `radon cc --show-complexity <python files>` calculates the cyclomatic complexity, specifically
  - `radon raw <python files>` 
- You can add radon configuration settings in the `.flake8` file:

```ini
# .flake8

[settings]
# vanilla flake8 settings
extend-ignore =
    E305
max-line-length = 99

# radon settings
radon-max-cc = 10
```

#### Assignments

##### Configure VS Code for Radon

1. Install radon with `pip install radon`
2. Write some code and measure its cyclomatic complexity
   - > Note: I have had the best luck with this when I write the logic **inside of functions** rather than writing the code in the global scope/namespace.
3. Install the Flake8 for VS Code if you have not already, and `pip install flake8 radon` in the same virtual environment, pointed at by VS Code.
4. Verify that you see red squiggly lines for functions that have high complexity
5. Write a `.flake8` file that configures Radon with a max complexity threshold
6. Validate your configuration file by writing code that gets a red squiggly line to appear when you go over your max CC threshold

