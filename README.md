# fundle [![Build Status](https://travis-ci.org/tuvistavie/fundle.svg?branch=master)](https://travis-ci.org/tuvistavie/fundle)

A minimalist package manager for [fish](http://fishshell.com/) inspired by [Vundle](https://github.com/VundleVim/Vundle.vim).


All plugins are installed/updated using git, so the only requirement is to have
git installed and on the path (and well, fish, obviously).

This package manager is compatible with [oh-my-fish plugins](https://github.com/oh-my-fish).
If you need the core functions of [oh-my-fish](https://github.com/oh-my-fish),
you can use the [tuvistavie/oh-my-fish-core](https://github.com/tuvistavie/oh-my-fish-core) plugin.

## Installation

Just drop [fundle.fish](functions/fundle.fish) in your `~/.config/fish/functions` directory and you are done.

```
mkdir -p ~/.config/fish/functions
wget https://raw.githubusercontent.com/tuvistavie/fundle/master/functions/fundle.fish -O ~/.config/fish/functions/fundle.fish
```

### ArchLinux

fundle is available on the AUR, so you can install it system wide with

```
yaourt -S fundle-git
```

### Updating

From fundle 0.2.0 and onwards, you can use `fundle self-update` to update fundle.

## Usage

### Sample `config.fish`

```
fundle plugin 'edc/bass'
fundle plugin 'oh-my-fish/plugin-php'
fundle plugin 'tuvistavie/fish-fastdir'
fundle plugin 'tuvistavie/fish-theme-afowler'

fundle init
```

This will source the four plugins listed and load all the functions and completions found.

After editing `config.fish`,

1. Reload your shell (you can run `exec fish` for example)
2. Run `fundle install`
3. That's it!

### In depth

To add a plugin, you simply need to add

```
fundle plugin 'repo_owner/repo_name'
```

somewhere in your configuration.

For example:

```
fundle plugin 'tuvistavie/fish-fastdir'
```

will install the repository at https://github.com/tuvistavie/fish-fastdir.

If you need to change the repository, you can pass it as a second argument and
it will be passed directly to `git clone`:

```
fundle plugin 'tuvistavie/fish-fastdir' 'git@github.com:tuvistavie/fish-fastdir.git'
```

You can also use a branch, tag or any [commit-ish](https://www.kernel.org/pub/software/scm/git/docs/gitrevisions.html#_specifying_revisions) by appending `#commit-ish` to the URL. For example:

```
fundle plugin 'tuvistavie/fish-fastdir' 'git@github.com:tuvistavie/fish-fastdir.git#my-branch'
```

will use `my-branch`. If no commit-ish is passed, it will default to `master`.

After having made all the calls to `fundle plugin`, you need to add

```
fundle init
```

in your configuration file for the plugins to be loaded.

IMPORTANT: When you add new plugins, you must restart your shell *before* running `fundle install`.
The simplest way to do this is probably to run `exec fish` in the running shell.

You can then run

```
fundle install
```

for fundle to download them.

You can also use

```
fundle install -u
```

to upgrade the plugins.

## Commands

* `fundle init`: Initialize fundle, loading all the available plugins
* `fundle install [-u]`: Install (or update) all plugins
* `fundle plugin PLUGIN [PLUGIN_URL]`: Add a plugin to fundle
* `fundle plugins [-s]`: List the currently installed plugins, including dependencies (-s gives a shorter version)
* `fundle self-update`: Updates fundle to the latest version
* `fundle version`: Displays the current version of fundle
* `fundle help`: Displays available commands

Completions are available in the [completions/fundle.fish](./completions/fundle.fish).
Note that you will need to install [fish-completion-helpers](https://github.com/tuvistavie/fish-completion-helpers)
to use them.

## Plugin structure

A plugin basically has the following structure.

```
.
├── completions
│   └── my_command.fish
├── functions
│   ├── __plugin_namespace_my_function.fish
│   └── my_public_function.fish
├── init.fish
└── README.md
```

* `init.fish` will be sourced directly, so it should not do anything that takes too long
  to avoid slowing down the shell startup. It is a good place to put aliases, for example.
* `functions` is the directory containing the plugin functions. This directory will
  be added to `fish_function_path`, and will therefore be auto loaded. I suggest you
  prefix your functions with `__plugin_name` if the user will not be using them explicitly.
* `completions` is the directory containing the plugin completions. This directory will
  be added to `fish_complete_path`.

NOTE: if no `init.fish` file is found, all the files with a `.fish` extensions in the
top directory of the plugin will be loaded. This is to make the plugins compatible with
[oh-my-fish plugins](https://github.com/oh-my-fish).

## Managing dependencies

fundle can manage dependencies for you very easily.
You just have to add

```
fundle plugin 'my/dependency'
```

in your plugin `init.fish` and fundle will automatically fetch and install the
missing dependencies when installing the plugin.

I created a minimal example in [fish-nvm](https://github.com/tuvistavie/fish-nvm),
which depends on [edc/bass](https://github.com/edc/bass).

## Profiling

Obviously, adding plugins makes the shell startup slower. It should usually be short enough,
but if you feel your shell is becoming to slow, fundle has a very basic profiling
mode to help you.

All you need to do is to change

```
fundle init
```

to

```
fundle init --profile
```

in your `config.fish` and fundle will print the time it took to load each plugin.

NOTE: this functionality simply uses the `date` command, so it prints the real time,
not the CPU time, but it should usually be enough to detect if something is wrong.
NOTE: when a plugin include dependencies, the load time for each dependency is added to the
parent plugin load time.

## Compatible plugins

Most [oh-my-fish plugins](https://github.com/oh-my-fish) should work out of the box
or with [tuvistavie/oh-my-fish-core](https://github.com/tuvistavie/oh-my-fish-core) installed.

Please feel free to edit the [wiki](https://github.com/tuvistavie/fundle/wiki) and add
your plugins, or plugins you know work with fundle.

## Contributing

Contributions are very appreciated. Please open an issue or create a PR if you
want to contribute.

If you created a package compatible with fundle, feel free to [add it to the Wiki](https://github.com/tuvistavie/fundle/wiki/Home/_edit).

## Motivations

I know that [oh-my-fish](https://github.com/oh-my-fish/oh-my-fish) has a utility to
install packages, but I wanted the simplest tool possible, not a whole framework.

## Changelog

* 2015-12-14 (v0.3.0): Fix dependency load order. Add profiling mode.
* 2015-12-14 (v0.2.2): Emit plugin initialization event
* 2015-12-7  (v0.2.1): Use `curl` instead of `wget` for `self-update`
* 2015-12-7  (v0.2.0): Add `self-update` command
* 2015-12-7  (v0.1.0): Fix bug with dependency loading in `fundle init`
* 2015-11-24: Allow the use of `#commit-ish` when using plugin repo. Checkout repository `commit-ish` instead of using master branch.
