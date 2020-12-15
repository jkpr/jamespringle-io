+++
title = "Automating Python projects with make"
date = "2020-12-13T22:36:37-06:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["make", "python"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

# Introduction to `make`

On Unix and Mac, software called `make` has been an indispensable tool for building compiled programs, such as those written in C. The command `make` takes as input a `makefile`, a text file that defines dependencies and actions.
The key elements in a `makefile` are the rules that describes an output and what that output depends on.
This makes sense when a binary depends on object files and libraries.

But how can this framework be applied to Python, which is not a compiled language?
Despite its connections to the compiled languages world, `make` can have great utility for Python by automating command line tasks.

# Anatomy of a rule

Here is a classic makefile rule, taken from the [manual][rule-example]:

```
foo.o : foo.c defs.h       # module for twiddling the frobs
	cc -c -g foo.c
```

This rule describes how to create the file `foo.o`, and it says that `foo.o` depends on `foo.c` and `defs.h`. What comes after the colon are called the prerequisites.

The recipe is what follows on the next line, i.e. `cc -c -g foo.c`. Very importantly (!), each line of a recipe must be indented with a tab (`<TAB>`) and not with spaces. Recipes can be multiple lines.

If `foo.o` does not exist, or if any of `foo.c` and `defs.h` are updated more recently than `foo.o`, then `make` knows it needs to run the recipe to create `foo.o`.

You can tell `make` evaluate the rule for `foo.o` and run the recipe if it needs to by typing on the command line

```
$ make foo.o
```

The target for this rule is `foo.o`, and we run `make` by typing `make [TARGET]` and substituting `foo.o` for `[TARGET]`. Rules and targets are nearly synonyms.

[rule-example]: https://www.gnu.org/software/make/manual/html_node/Rule-Example.html

# Example `makefile` for a Python project

The following is an example `makefile` for a Python package named `ppp`. These are the full contents of a file whose file name is `makefile` (no extension). See a slightly different example of mine on [github][github-make]. I'll describe interesting aspects later.

[github-make]: https://github.com/jkpr/pmix/blob/master/makefile

```make
SRC = ./ppp
PROMPT = ppp


## List of makefile targets
## - help       : show this help documentation
.PHONY: help
help: makefile
	@sed -n 's/^.*##[ ]//p' $<

## - lint       : Run pylint on the source code
.PHONY: lint
lint: env
	. env/bin/activate && python3 -m pylint ${SRC}

## - black      : Run black on the source code
.PHONY: black
black: env
	. env/bin/activate && python3 -m black ${SRC}

## - env        : Set up the virtual environment
env: env/bin/activate

env/bin/activate: requirements.txt
	test -d env || python3 -m venv --prompt ${PROMPT} env/
	. env/bin/activate && python3 -m pip install -r requirements.txt
	touch env/bin/activate

## - test       : Run unit tests
.PHONY: test
test:
	. env/bin/activate && python3 -m unittest discover -v

## - pypi       : Upload packages to PyPI
.PHONY: pypi
pypi: build
	. env/bin/activate && twine upload --repository-url https://upload.pypi.org/legacy/ dist/*;

## - pypi_test  : Upload packages to PyPI
.PHONY: pypi_test
pypi_test: build
	. env/bin/activate && twine upload --repository-url https://test.pypi.org/legacy/ dist/*

## - build      : Build package artifacts
.PHONY: build
build: clean
	. env/bin/activate && python3 setup.py sdist bdist_wheel

## - clean      : Remove artifacts from package building process
.PHONY: clean
clean:
	rm -rf ./dist;
	rm -rf ./build;
	rm -rf ./*.egg-info
```

Makefiles for Python are a little different. We do not worry about creating binaries or object files, usually. We just want to do things. So we mostly use phony targets (described next), and the `help` target is our first phony target.

## Phony targets and showing help usage

I always put my first target as `help`. This is a ["phony" target][phony], meaning it is not a rule for creating a file named `help`, but rather it is a recipe to run whenever I request it. I declare it to be phony by typing `.PHONY: help` above the rule.

I can run the recipe for `help` by typing

```
$ make help
```

Alternatively, since running `make` by itself evaluates the first rule in the makefile, we could type `make` by itself instead of `make help`.

The `help` recipe in the above makefile is the best way to show usage that I have found. We declare that `help` depends on its own file `makefile` with `help: makefile`. Then a simple [`sed` program][sed] finds all lines with `## ` (two [octothorps][octothorp] and a space) and prints only what comes after.

```
help: makefile
	@sed -n 's/^.*##[ ]//p' $<
```

- The `@` means the command is not [echoed][echo].
- The `$<` is a [make automatic variable][auto] that means "the first prerequisite." In this case, it is always `makefile`.

To understand the rest, we look at what remains: `sed -n 's/^.*##[ ]//p' makefile`

The stream editor `sed` takes its input `makefile` and applies the full command `'s/^.*##[ ]//p'` to each line. The `s/.../.../` and `/p` are [individual commands][sed-commands].

- `s/^.*##[ ]//` means to substitute a match to the regular expression `^.*##[ ]` with the empty string. We use `[ ]` instead of ` ` (one space) so that this line with this `sed` command is not matched.
- `p` means to print that result

With option `-n` on `sed` we do not echo back every line of the input, which would be the default.

With this recipe in place, each target is documented with a line that contains `## `. Since `make` treats `#` as the comment marker, this works great with `make` and for our own purposes!

[phony]: https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html
[sed]: https://www.gnu.org/software/sed/manual/sed.html
[octothorp]: https://en.wikipedia.org/wiki/Number_sign
[echo]: https://www.gnu.org/software/make/manual/html_node/Echoing.html
[auto]: https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html
[sed-commands]: https://www.gnu.org/software/sed/manual/sed.html#sed-commands-list

## Variables and building the virtual environment

First, we have some [variables][make-variables] declared at the top of the file.

[make-variables]: https://www.gnu.org/software/make/manual/html_node/Using-Variables.html

```
SRC = ./ppp
PROMPT = ppp
```

These are referenced later as `${SRC}` and `${PROMPT}`.

The following rules build our environment with `make env`. Note that `env` is not phony! This is because it is a real directory.

```
env: env/bin/activate

env/bin/activate: requirements.txt
	test -d env || python3 -m venv --prompt ${PROMPT} env/
	. env/bin/activate && python3 -m pip install -r requirements.txt
	touch env/bin/activate
```

These two rules are related to each other, because `env` depends on `env/bin/activate`, which depends on `requirements.txt`. Typing `make env` immediately gets to the second rule.

Here it is important to remember that `env/bin/activate` is a script that makes sure the first `python` in your path is the one in the virtual environment.

In the recipe, the things to note are that

- `test -d env || ...` tests for the existence of an `env` directory, and if it doesn't exist, then `...` runs. That is what `||` does.
- `python3 -m venv --prompt ${PROMPT} env/` is the standard virtual environment creation, except that it also sets the prompt to `${PROMPT}`. Otherwise the prompt would just be `env` by default (boring).
- `. env/bin/activate && ...` runs the `activate` script, and then `...` runs (here, `...` is `python3 -m pip install -r requirements.txt` so the correct packages are installed with `pip`). 
- `touch env/bin/activate` is how we make sure that `env/bin/activate` is more recent than `requirements.txt`. Running `make env` again won't do anything until `requirements.txt` is updated.

## Useful Python phony targets

The rest of the makefile has useful phony targets.

We can run unit tests with

```
.PHONY: test
test:
	. env/bin/activate && python3 -m unittest discover -v
```

and `make test`. We can format our code with Black with this rule:

```
.PHONY: black
black: env
	. env/bin/activate && python3 -m black ${SRC}
```

and `make black`.

The targets for `clean`, `build`, and `pypi` handle packaging and upload to PyPI.

Anytime there is some administrative task that has to be entered precisely on the command line, it may as well go into a `makefile`!

# Conclusion

Using the command `make` and accompanying `makefile` is a great way to simplify pipelines and quickly get things done for Python projects. Phony targets are most commonly used, but there are reasons to build things, too, like a virtual environment. Adding a `help` target is useful to document what the makefile is intended to do. This only scratches the surface, and there are certainly other ways to use a `makefile` to streamline development. Good luck!
