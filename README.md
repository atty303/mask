# mask 🎭

[![build status](https://img.shields.io/circleci/build/github/jakedeichert/mask/master.svg)][circleci]
[![mask version](https://img.shields.io/crates/v/mask.svg)][crate]

`mask` is a CLI task runner which is defined by a simple markdown file. It searches for a `maskfile.md` in the current directory which it then parses for commands and arguments.

A `maskfile.md` is both a **human-readable document** and a **command definition**! Being documentation focused allows others to easily get started with your project's development setup by simply reading your `maskfile.md`. A nice advantage of using markdown is that syntax highlighting for code blocks is built-in to many editors and renderers like GitHub itself.

Here's the [maskfile.md](/maskfile.md) that `mask` itself uses as an example!

To get started, follow the guide below or check out the more [advanced features](#features) `mask` has like **positional args**, **optional flags**, **subcommands**, other **scripting runtimes** and more!





## Getting started

Currently, `mask` is only published to [crates.io][crate] which allows you to install it with `cargo`.

> `mask` is built with rust, so you'll need the [rust toolchain][rustup] installed if you don't have it already.

~~~sh
cargo install mask
~~~

> If you prefer to build from source, clone this repo and then run `cargo build --release`

Next, define a simple `maskfile.md` in your project.

```markdown
# Tasks For My Project


<!-- A heading defines the command's name -->
## build

<!-- A blockquote defines the command's description -->
> Builds my project

<!-- A code block defines the script to be executed -->
~~~sh
echo "building project..."
~~~


## test

> Tests my project

You can also write documentation anywhere you want. Only certain types of markdown patterns
are parsed to determine the command structure.

Note this code block below is defined as js. So far, mask supports node,
python, ruby and php as scripting runtimes!

~~~js
console.log("running project's tests")
~~~
```

And finally, try running one of your commands!

~~~sh
mask build
mask test
~~~





## Features

### Positional arguments

These are defined beside the command name within `(round_brackets)`. They are required arguments that must be supplied for the command to run. [Optional args][2] are coming soon. The argument name is injected into the script's scope as an environment variable.

**Example:**

```markdown
## test (file) (test_case)

> Run tests

~~~bash
echo "Testing $test_case in $file"
~~~
```

### Optional flags

You can define a list of optional flags for your commands. The flag name is injected into the script's scope as an environment variable.

Important to note that `mask` auto injects a very common `boolean` flag called `verbose` into every single command even if it's not used. This saves a bit of typing for you! This means every command implictly has a `-v` and `--verbose` flag already. The value of the `$verbose` environment variable is either `"true"` or simply unset/non-existent.

**Example:**

```markdown
## serve

> Serve this directory

<!-- You must define OPTIONS right before your list of flags -->
**OPTIONS**
* port
    * flags: -p --port
    * type: string
    * desc: Which port to serve on

~~~sh
# Set a fallback port
PORT=${port:-8080}

if [[ "$verbose" == "true" ]]; then
    echo "Starting an http server on PORT: $PORT"
fi
python -m SimpleHTTPServer $PORT
~~~
```

### Subcommands

Nested command structures can easily be created since they are simply defined by the level of markdown heading. H2 (`##`) is where you define your top-level commands. Every level after that is a subcommand. The only requirement is that subcommands must have all ancestor commands present in their heading.

**Example:**
```markdown
## services

> Commands related to starting, stopping, and restarting services

### services start (service_name)

> Start a service.

~~~bash
echo "Starting service $service_name"
~~~

### services stop (service_name)

> Stop a service.

~~~bash
echo "Stopping service $service_name"
~~~

#### services stop all

> Stop everything.

~~~bash
echo "Stopping everything"
~~~
```

### Support for other scripting runtimes

On top of shell/bash scripts, `mask` also supports using node, python, ruby and php as scripting runtimes. This gives you the freedom to choose the right tool for the specific task at hand. For example, let's say you have a `serve` command and a `snapshot` command. You could choose python to `serve` a simple directory and maybe node to run a puppeteer script that generates a png `snapshot` of each page.

**Example:**

```markdown
## shell (name)

> An example shell script

Valid lang codes: sh, bash, zsh, fish
The fallback is sh for unknown language codes.

~~~zsh
echo "Hello, $name!"
~~~


## node (name)

> An example node script

Valid lang codes: js, javascript

~~~js
const { name } = process.env;
console.log(`Hello, ${name}!`);
~~~


## python (name)

> An example python script

Valid lang codes: py, python

~~~python
import os

name = os.getenv("name", "WORLD")

print("Hello, " + name + "!")
~~~


## ruby (name)

> An example ruby script

Valid lang codes: rb, ruby

~~~ruby
name = ENV["name"] || "WORLD"

puts "Hello, #{name}!"
~~~


## php (name)

> An example php script

~~~php
$name = getenv("name") ?: "WORLD";

echo "Hello, " . $name . "!\n";
~~~
```

### Automatic help and usage output

You don't have to spend time writing out help info manually. `mask` uses your command descriptions and options to automatically generate help output. For every command, it adds `-h, --help` flags and an alternative `help <name>` command.

**Example:**
~~~sh
mask services start -h
mask services start --help
mask services help start
mask help services start
~~~

All output the same help info:

~~~txt
mask-services-start
Start or restart a service.

USAGE:
    mask services start [FLAGS] <service_name>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information
    -v, --verbose    Sets the level of verbosity
    -r, --restart    Restart this service if it's already running
    -w, --watch      Restart a service on file change

ARGS:
    <service_name>
~~~

### Running mask from within a script

You can easily call `mask` within scripts if you need to chain commands together.

**Example:**

```markdown
## bootstrap

> Installs deps, builds, links, migrates the db and then starts the app

~~~sh
mask install
mask build
mask link
mask db migrate
mask start
~~~
```





## Upcoming features

* [ ] [Optional (non-required) positional arguments][2]
* [ ] [Infinite positional args](https://github.com/jakedeichert/mask/issues/4)
* [ ] [Option flag `number` type for input validation purposes](https://github.com/jakedeichert/mask/issues/3)





## Use cases

Here's some example scenarios where `mask` might be handy.

### Project specific tasks

You have a project with a bunch of random build and development scripts or an unwieldy `Makefile`. You want to simplify by having a single, readable file for your team members to add and modify existing tasks.


### Global system utility

You want a global utility CLI for a variety of system tasks such as backing up directories or renaming a bunch of files. This is easily possible by making a bash alias for `mask --maskfile ~/my-global-maskfile.md $1`.





## FAQ

### Windows support?

Currently, this is [unknown][windows_issue]. I'm pretty sure the executor logic will need to be adjusted for Windows. Git Bash and Ubuntu on Windows have been reported to work but they are not actively being tested.

### Is `mask` available as a lib?

`mask` was designed as a lib from the beginning and is accessible. However, it's very undocumented and will need to be cleaned up before it's considered stable.

### Where did the inspiration come from?

I'm definitely not the first to come up with this idea of using markdown as a CLI structure definition.

My frustrations with `make`'s syntax is what led me to search for other options. I landed on [just][just] for awhile which was a pretty nice improvement. My favourite feature of `just` is its support for other language runtimes, which is why `mask` also has this ability! However, it still didn't have some features I wanted like nested subcommands and multiple optional flags.

At some point in my searching, I came across [maid][maid] which is where most of the inspiration for `mask` comes from. I thought it was brilliant that markdown could be used as a command definition format while still being so readable.

So why did I choose to rebuild the wheel instead of using `maid`? For one, I preferred installing a single binary, like `just` is, rather than installing an npm package with hundreds of deps. I also had a few ideas on how I could improve upon `maid` which is why `mask` supports multiple levels of nested subcommands as well as optional flags and positional args. Also... I just really wanted to build another thing with Rust :)

I also need to mention [clap][clap] and [pulldown-cmark][cmark] which are really the core parts of `mask` that made it so easy to create.





## Contributing

Please file an [issue][new_issue] for discussion of features or bugs, and we'll go from there :)

If you'd like to tackle one of the issues after a decision has been made, please leave a comment.





## Author

Jake Deichert with the help of contributors.

[@jakedeichert][twitter] on Twitter · [Website][website]





[crate]: https://crates.io/crates/mask
[circleci]: https://circleci.com/gh/jakedeichert/mask
[new_issue]: https://github.com/jakedeichert/mask/issues/new
[website]: https://jakedeichert.com
[twitter]: https://twitter.com/jakedeichert
[rustup]: https://rustup.rs
[2]: https://github.com/jakedeichert/mask/issues/5
[maid]: https://github.com/egoist/maid
[just]: https://github.com/casey/just
[clap]: https://github.com/clap-rs/clap
[cmark]: https://github.com/raphlinus/pulldown-cmark
[windows_issue]: https://github.com/jakedeichert/mask/issues/10
