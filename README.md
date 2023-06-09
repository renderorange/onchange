# NAME

onchange - run commands when files change

# SYNOPSIS

    onchange [--run <command|set>]
             [--help]

# DESCRIPTION

`onchange` is a program that watches for changes to files and directories
then runs configured commands.

`onchange` detects changes recursively within the current working directory.
Files/directories to ignore and commands to run are read from the `.onchange`
file within the current working directory.

# OPTIONS

- --run &lt;command|set>

    Defines which command or set to run on changes.

    The `command` or `set` specified must be defined in the `.onchange` configuration file.

    Multiple `--run` options may be defined and are run in the order they're passed.

- --help

    Print the help menu.

# CONFIGURATION

## ignore

The `ignore` section key.

Values within this section define which file and directory names to ignore.

Multiple file and directory names can be defined per line if separated by
comma.

    # updates to tmp, t, scratch, and testing, will not trigger running commands
    [ignore]
    dirs=tmp, t
    files=scratch, testing

## command

The `command` section key.

Keys within this section name the command to run when that command argument
is passed.

The value for each command key defines the command to run.

    [command]
    example=echo 'this is an example command'
    another_example=echo 'this is another example command'

Defined commands are run as arguments to `onchange`. Multiple commands are
run in the order they're passed.

    $ onchange --run example --run another_example
    (will run example, then another_example)

## set

The `set` section key.

Keys within this section name the sets of commands to run when that set
argument is passed.

The value of each set key defines the command or list of commands to run.

    [command]
    example=echo 'this is an example command'
    another_example=echo 'this is another example command'
    yet_another_example=echo 'this is not included in set examples'

    [set]
    examples=example, another_example

Multiple commands can be defined per line if separated by comma.

Defined sets are run as arguments to `onchange`. Multiple set are run
in the order they're passed.

Sets and commands can both be passed to be run.

    $ onchange --run examples --run yet_another_example
    (will run example, then another_example, then yet_another_example)

## EXAMPLES

### Run two specific commands when changes are detected

    # create a .onchange file in the directory to watch
    ~/git/example $ vi .onchange
    [ignore]
    dirs=tmp, t
    files=scratch, testing

    [command]
    example=echo 'this is an example command'
    another_example=echo 'this is another example command'
    yet_another_example=echo 'this is not included in set examples'

    [set]
    examples=example, another_example

    # run onchange, specifying both commands to run as defined in the .onchange file
    ~/git/example $ onchange --run example --run another_example
    [1617588530][ignore]: scratch,testing
    [1617588530][ignore]: tmp,t
    [1617588530][watch]: /home/blaine/git/example

    # when any non-ignored files or directories change (or new are created),
    # each of the specified commands are run (but not the yet_another_example command)

    [1617588661][modify]: /home/blaine/git/example/lib/example.pm
    [1617588661][run example]: echo 'this is an example command'
    this is an example command
    [1617588661][run another_example]: echo 'this is another example command'
    this is another example command
    [1617588707][create]: /home/blaine/git/example/puppies
    [1617588707][run example]: echo 'this is an example command'
    this is an example command
    [1617588707][run another_example]: echo 'this is another example command'
    this is another example command

### Run a set of commands and one specific command when changes are detected

    # with the following commands and set in the .onchange file
    [command]
    example=echo 'this is an example command'
    another_example=echo 'this is another example command'
    yet_another_example=echo 'this is not included in set examples'

    [set]
    examples=example, another_example

    # run onchange, specifying the set and additional command to run
    ~/git/example $ onchange --run examples --run yet_another_example
    (will run example, then another_example, then yet_another_example)
