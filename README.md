# Griffe

<p style="display: none">

[![ci](https://github.com/mkdocstrings/griffe/workflows/ci/badge.svg)](https://github.com/mkdocstrings/griffe/actions?query=workflow%3Aci)
[![documentation](https://img.shields.io/badge/docs-mkdocs%20material-blue.svg?style=flat)](https://mkdocstrings.github.io/griffe/)
[![pypi version](https://img.shields.io/pypi/v/griffe.svg)](https://pypi.org/project/griffe/)
[![gitpod](https://img.shields.io/badge/gitpod-workspace-blue.svg?style=flat)](https://gitpod.io/#https://github.com/mkdocstrings/griffe)
[![gitter](https://badges.gitter.im/join%20chat.svg)](https://gitter.im/mkdocstrings/griffe)

</p>

<img src="logo.svg" alt="Griffe logo, created by François Rozet &lt;francois.rozet@outlook.com&gt;">

Signatures for entire Python programs. Extract the structure, the frame, the skeleton of your project, to generate API documentation or find breaking changes in your API.

Griffe, pronounced "grif" (`/ɡʁif/`), is a french word that means "claw",
but also "signature" in a familiar way. "On reconnaît bien là sa griffe."

## Installation

With `pip`:
```bash
pip install griffe
```

With [`pipx`](https://github.com/pipxproject/pipx):
```bash
python3.8 -m pip install --user pipx
pipx install griffe
```

## Usage

**On the command line**, pass the names of packages to the `griffe dump` command:

```console
$ griffe dump httpx fastapi
{
  "httpx": {
    "name": "httpx",
    ...
  },
  "fastapi": {
    "name": "fastapi",
    ...
  }
}
```

See [the Dumping data section](https://mkdocstrings.github.io/griffe/dumping/) for more examples.

Or pass a relative path to the `griffe check` command:

```console
$ griffe check mypackage --verbose
mypackage/mymodule.py:10: MyClass.mymethod(myparam):
Parameter kind was changed:
  Old: positional or keyword
  New: keyword-only
```

For `src` layouts:

```console
$ griffe check --search src mypackage --verbose
src/mypackage/mymodule.py:10: MyClass.mymethod(myparam):
Parameter kind was changed:
  Old: positional or keyword
  New: keyword-only
```

See [the API breakage section](https://mkdocstrings.github.io/griffe/checking/) for more examples.

**With Python**, loading a package:

```python
import griffe

fastapi = griffe.load("fastapi")
```

Finding breaking changes:

```python
import griffe

previous = griffe.load_git("mypackage", ref="0.2.0")
current = griffe.load("mypackage")

for breakage in griffe.find_breaking_changes(previous, current):
    ...
```

See [the Loading data section](https://mkdocstrings.github.io/griffe/loading/) for more examples.

## Todo

- Extensions
    - Post-processing extensions
    - Third-party libraries we could provide support for:
        - Django support
        - Marshmallow support
        - Pydantic support
- Docstrings parsers
    - epydoc
    - New Markdown-based format? For graceful degradation
- Serializer:
    - Flat JSON
- API diff:
    - [ ] Mechanism to cache APIs? Should users version them, or store them somewhere (docs)?
    - [ ] Ability to return warnings (things that are not backward-compatibility-friendly)
    - List of things to consider for warnings
        - Multiple positional-or-keyword parameters
        - Public imports in public modules
        - Private things made public through imports/assignments
        - Too many public things? Generally annoying. Configuration?
    - [x] Ability to compare two APIs to return breaking changes
    - List of things to consider for breaking changes
        - [x] Changed position of positional only parameter
        - [x] Changed position of positional or keyword parameter
        - [ ] Changed type of parameter
        - [ ] Changed type of public module attribute
        - [ ] Changed return type of a public function/method
        - [x] Added parameter without a default value
        - [x] Removed keyword-only parameter without a default value, without **kwargs to swallow it
        - [x] Removed positional-only parameter without a default value, without *args to swallow it
        - [x] Removed positional-or_keyword argument without a default value, without *args and **kwargs to swallow it
        - [x] Removed public module/class/function/method/attribute
        - [ ] All of the previous even when parent is private (could be publicly imported or assigned somewhere),
            and later be smarter: public assign/import makes private things public!
        - [ ] Inheritance: removed, added or changed base that changes MRO
