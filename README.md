# prm
A minimal project manager for the terminal.

![Demo](https://github.com/eivind88/prm/blob/demo/prm.gif)

This script **must** be sourced, *not* run in a subshell.
See [usage](#usage) for more information.

At present, prm is mostly developed and tested with `bash`, as this is what I personally use and have the most experience with.
I'm very much open to contributions that fix issues with `zsh` (provided that they don't break `bash` support), and I try to test prm using it every once in a while to the best of my abilities.
As far as I know, prm currently works with `zsh`.

Regrettably, `fish` is not supported because of syntax incompatibilities.
See [this issue](https://github.com/eivind88/prm/issues/2) for some details.
However, Fred Deschenes has made a [port](https://github.com/FredDeschenes/prm-fish) for `fish` that you could check out.

## What?
This program basically lets you CRUD projects. Upon activation, each projects runs its associated start-script; on deactivation, it runs the project stop-script.

These bash-scripts can be used for things like changing directories, setting environment variables, cleanup, etc.

There is basic prompt integration in the form of `[PROJECT] <prompt>`, which can be seen in the animated .gif demo above.

You can have several projects active at once in different shells, as prm associates active instances with the shell PID.
Currently active projects can be listed (as described in [usage](#usage)).

Dead project instances (i.e. project instances that are still active on shell exit) will be automatically deactivated the next time you run prm – without running their stop-scripts.

## How?
Adding and editing projects will open the associated start- and stop-scripts in your editor (as defined by the `$EDITOR` environment variable).

A project start-script might for instance look something like this:

```bash
# cd to project directory
cd $HOME/src/Python/hello-world

# activate conda env
source activate hello-world

# show current git status
git status
```

The same project's stop-script might look like this:

```bash
# deactivate conda env
source deactivate hello-world

# clean up
rm *.log *.tmp
```

When you activate a new project, prm automatically stops any active project in the current shell.

When a project is deactivated, prm changes the working directory back to the path you were originally on before starting your first project.

### Reusability
If you often create projects similar to one you already have, you can load custom scripts from your projects' `start.sh` and `stop.sh`.
For instance, if you'd like some python-based projects to list the number of outdated packages in their conda envs, you can save

```bash
# count outdated packages in conda env
echo "$((($(conda search --outdated --names-only | wc -l)-1))) outdated packages in env"
```

as e.g. `conda-list-outdated.sh` in `$PRM_PATH/.common/` (this environment variable is detailed in [usage](#usage)).
You can then load this script in your start- and stop-scripts like so:

```bash
prm_load conda-list-outdated
```

Additionally, if you need the name of the currently active project, this is available via the `$PRM_ACTIVE_PROJECT` environment variable.

Command line arguments are available in scripts, with `$3` being the first argument after your project name.

## Why?
I found myself missing project management features (like those seen in text editors and IDEs) on the terminal.

Instead of remembering what projects I am working on these days or switching between loads of terminal windows or tabs, I now use prm.

## Usage
In order to work properly, prm **must** be sourced, *not* run in a subshell; i.e. `. ./prm`.

The easiest way to do this is probably to add an alias to prm in your `~/.bashrc` (or wherever you keep your aliases), like so:

```bash
alias prm=". path/to/prm.sh"
```

From the help option screen:

```bash
usage: prm <option> [<args>] ...

Options:
  active                   List active project instances.
  add <project name>       Add project(s).
  copy <old> <new>         Copy project.
  edit <project name>      Edit project(s).
  list                     List all projects.
  remove <project name>    Remove project(s).
  rename <old> <new>       Rename project.
  start <project name>     Start project.
  stop                     Stop active project.
  -h --help                Display this information.
  -v --version             Display version info.
```

You can set the prm data directory with the `$PRM_PATH` environment variable.
By default all prm data is written to `~/.prm`.

## Contributing
Feedback is strongly encouraged. If you run into a bug or would like to see a new feature, please open a new issue.

Contributions in the form of code (e.g. implementing new features, bug-fixes) are also appreciated. For information about this, see the [Wiki page on Contributing](https://github.com/eivind88/prm/wiki/Contributing).

## License
This software is released under the terms of the 3-clause New BSD License. See the [license](LICENSE.txt) file for details.
