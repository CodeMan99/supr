# supr

:zap: Super Utility Project Runner :zap: A single entrypoint for all your personal project scripts.

## Why

Every project has six different ways to run "project scoped" commands. For some
examples:

- The language has a package manager with twenty sub-commands.
- The project has _scripts_ directory. Some of them require setup.
- The framework has yet another script runner.
- Sometimes you need to be inside the context of a container.
- For whatever reason there is a script burried three folders deep.
- There is always that one thing that requires a long pipe.

The solution I propose to you: Organize via subcommands!

## Installation

Written in pure bash. Just download the `supr` file to get going.

```shell
$ curl -LOJ https://raw.githubusercontent.com/CodeMan99/supr/main/bin/supr
$ install supr ~/.local/bin
$ supr --help
```

### Dependencies

- **bash**: v4.2+
- **git**: v2.x

## Usage

How you use `supr` is almost entirely up to you. Make your life easier.

### Example: SQL REPL in a Container

There are simply so many ways to get into a container. This is one I find to be
a PITA. You need the right compose stack. The right container. Which system
user. The command. The connection info.

No matter what database you picked, your `.env` file that your project uses just
is not in the right shape for that database's official cli tool.

You end up with six patterns to obtain a connection per project.

```shell
$ cat << 'EOF' > "$(supr --path sql)"
#!/usr/bin/env bash

cd "$SUPR_PROJECT_ROOT"

if docker compose --project-name backend-stack ps --format json | jq -e '.State == "running"' > /dev/null; then
    docker compose --project-name backend-stack exec --user postgres postgres psql --user $PG_USER $PG_DATABASE
else
    printf "The '%s' compose project is not running..." "backend-stack" >&2
    exit 1
fi
EOF
$ chmod +x "$(supr --path sql)"
$ supr sql
```

### Example: Testing

Create yourself a single command to facilatate the "red, green, refactor" loop.

```shell
$ supr test                # run the full test suite.
$ supr test --single       # run the top failure.
$ supr test --edit         # open an editor to mark a test as passed.
```

Yes, this example is just a command surface. Have your favorite AI buddy write
along with you! I'm sure you already have a test entrypoint. We both know there
is room for improvement. Make the process your own.

### Shared Boot Script

Just like any other script, but output will be `eval`'d before all commands
in that project.

```shell
$ supr --path -boot      # A script here will be executed before all commands
```

This is a great place to unlock secrets.
