[![Build Status](https://travis-ci.org/keplerproject/luacov.svg?branch=master)](https://travis-ci.org/keplerproject/luacov)

## Overview

LuaCov is a simple coverage analyzer for [Lua](http://www.lua.org) scripts.
When a Lua script is run with the `luacov` module loaded, it generates a stats
file with the number of executions of each line of the script and its loaded
modules. The `luacov` command-line script then processes this file generating
a report file which allows one to visualize which code paths were not
traversed, which is useful for verifying the effectiveness of a test suite.

LuaCov is free software and, like Lua, is released under the [MIT
License](http://www.lua.org/license.html).

## Download and Installation

LuaCov can be downloaded from its [Github downloads
page](https://github.com/keplerproject/luacov/releases).

It can also be installed using Luarocks:

    luarocks install luacov

LuaCov is written in pure Lua and has no external dependencies.

## Instructions

Using LuaCov consists of two steps: running your script to collect coverage
data, and then running `luacov` on the collected data to generate a report
(see [configuration](#configuration) below for other options).

To collect coverage data, your script needs to load the `luacov` Lua module.
This can be done from the command-line, without modifying your script, like
this:

    lua -lluacov test.lua

Alternatively, you can add `require("luacov")` to the first line of your
script.

Once the script is run, a file called `luacov.stats.out` is generated. If the
file already exists, statistics are _added_ to it. This is useful, for
example, for making a series of runs with different input parameters in a test
suite. To start the accounting from scratch, just delete the stats file.

To generate a report, just run the `luacov` command-line script. It expects to
find a file named `luacov.stats.out` in the current directory, and outputs a
file named `luacov.report.out`. The script takes the following parameters:

    luacov [-c=configfile] [filename...]

For the `-c` option see below at [configuration](#configuration). The filenames
(actually Lua patterns) indicate the files to include in the report, specifying them here
equals to adding them to the `include` list in the configuration file, with `.lua`
extension stripped.

This is an example output of the report file:

    ============================================================
    ../test.lua
    ============================================================

            -- Which branch will run?
    1       if 10 > 100 then
    0          print("I don't think this line will execute.")
    0       else
    1          print("Hello, LuaCov!")
    1       end

Note that to generate this report, `luacov` reads the source files. Therefore,
it expects to find them in the same location they were when the `luacov`
module ran (the stats file stores the filenames, but not the sources
themselves).

LuaCov saves its stats upon normal program termination. If your program is a
daemon -- in other words, if it does not terminate normally -- you can use the
`luacov.tick` module, which periodically saves the stats file. For example, to
run (on Unix systems) LuaCov on
[Xavante](http://keplerproject.github.io/xavante/), just modify the first line of
`xavante_start.lua` so it reads:

    #!/usr/bin/env lua -lluacov.tick


## Configuration

LuaCov includes several configuration options, which have their defaults
stored in `src/luacov/defaults.lua`. These are the global defaults. To use
project specific configuration, create a Lua script returning a table
with some options and store it as `.luacov` in the project directory from
where `luacov` is being run. For example, this config informs LuaCov that
only `foo` module and its submodules should be covered and that they are
located inside `src` directory:

```lua
return {
    modules = {
        ["foo"] = "src/foo/init.lua",
        ["foo.*"] = "src"
    }
}
```

For a full list of options, see
[`luacov.defaults` documentation](http://keplerproject.github.io/luacov/doc/modules/luacov.defaults.html).

## Custom reporter engines

LuaCov supports custom reporter engines, which are distributed as separate
packages. Check them out!

* Cobertura: https://github.com/britzl/luacov-cobertura
* Coveralls: https://github.com/moteus/luacov-coveralls

## Using development version

After cloning this repo, these commands may be useful:

* `luarocks make` to install LuaCov from local sources;
* `make test` to run tests (does not require installing beforehand);
* `ldoc .` to regenerate documentation using [LDoc](https://github.com/stevedonovan/LDoc).

## Credits

LuaCov was designed and implemented by Hisham Muhammad as a tool for testing
[LuaRocks](http://www.luarocks.org).
