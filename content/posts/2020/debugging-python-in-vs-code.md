+++
title = "Debugging Python in Visual Studio Code"
date = "2021-01-15T15:47:53-06:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["vs-code", "python", "debugging"]
keywords = ["python", "vs-code", "debugging"]
description = ""
showFullContent = false
+++

There comes a time when a programmer needs to step line by line to understand what code is doing. This is debugging, and Visual Studio Code has your back.

{{< image src="/static/img/2021/red-black-bug.jpg" alt="A red and black striped bug" position="center" style="border-radius: 8px;" >}}

In this post, I will explain how to debug a Python project in Visual Studio Code and a few of the useful features of the debugger.

# The problem to explore

Recently, I ran into a problem with [`pyxform`][pyxform], a Python library that converts a specially-formatted XLSX file into a specially-formatted XML file for questionnaires (called Xform). The way to run the code is to invoke a command line script, [`xls2xform`][xls2xform], such as

```bash
python -m pyxform.xls2xform path/to/file.xlsx
```

# Getting things ready

Our first goal is to download the code, create a virtual environment, download the code's dependencies, and install the package as a local, editable package. In the terminal, run

```bash
git clone https://github.com/XLSForm/pyxform.git
cd pyxform/
python3 -m venv env/
source env/bin/activate
python3 -m pip install -e .
```

[pyxform]: https://github.com/XLSForm/pyxform
[xls2xform]: https://github.com/XLSForm/pyxform/blob/v1.3.4/setup.py#L41

Finally, download the [delivery outcome sample form][sample-form], taken from [xlform.org](https://xlsform.org).

Now, resuming in the terminal where we left off, this should work to convert a form:

```bash
python3 -m pyxform.xls2xform ~/Downloads/delivery_outcome.xlsx
```

NOTE: For the `xls2xform` script to work, the virtual environment needs to be activated with the package installed as a local, editable package.

[sample-form]: /static/data/delivery_outcome.xlsx

# Create a breakpoint

Open up the root `pyxform` directory in Visual Studio Code. Resuming in the terminal where we left off, we just need to open the current directory:

```bash
code .
```

Make sure that the `code` command is already in your `PATH` (see [docs][code-docs] here).

[code-docs]: https://code.visualstudio.com/docs/editor/command-line

Open up `xls2xform.py` and click in the left gutter so that a red dot appears. This sets a [breakpoint][breakpoint]. When you debug the code, the code will run until it reaches a line with a breakpoint and then give you access to the debug controls.

[breakpoint]: https://code.visualstudio.com/docs/editor/debugging#_breakpoints

{{< figure src="/static/img/2021/vs-code-debug-1.png" alt="Visual Studio Code debug with breakpoint" position="center" style="border-radius: 8px;" caption="Setting a breakpoint in Visual Studio Code." >}}

# Create a launch configuration

First click the Run view in the sidebar, then click "create a launch.json file". We need a [launch configuration][launch-config].

[launch-config]: https://code.visualstudio.com/docs/editor/debugging#_launch-configurations

By default, Visual Studio Code can give a launch configuration to run the current file. In the background, it would run

```bash
python3 path/to/current/file.py
```

and enter the debugger.

In our case. we want to use the other option of "Module". In the background, it would run

```bash
python3 -m package.path.to.module
```

The only thing that matters is the `launch.json` file, though, and the final result is shown in this screenshot.

{{< figure src="/static/img/2021/vs-code-debug-2.png" alt="Visual Studio Code launch configuration" position="center" style="border-radius: 8px;" caption="A launch configuration for debugging xls2xform." >}}

Once the debugger is running, you have the [standard debug controls][actions] for stepping over / into / out of, etc.

Two things to point out that are very helpful are the "Watch" (center left) panel and the "Debug console" (bottom right, a specific tab). In the "Watch" panel you can enter an expression that will be evaluated on every change in state. In the debug console, you get a Python REPL at the current debug state. You can inspect variables and do other useful REPL things.

[actions]: https://code.visualstudio.com/docs/editor/debugging#_debug-actions

{{< figure src="/static/img/2021/vs-code-debug-3.png" alt="Visual Studio Code debug interface" position="center" style="border-radius: 8px;" caption="A launch configuration for debugging xls2xform." >}}