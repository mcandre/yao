# yao: a Common Lisp task runner

# ABOUT

yao enables Common Lisp developers to configure build tasks directly with Common Lisp code. This is a powerful advantage over the classic make utility. No longer do developers have to manage two completely different programming languages. With yao, Common Lisp devs have more expressiveness over their builds.

# EXAMPLE

```console
$ cd example

$ ./yao
Hello World!

$ ./yao test
Hello World!
```

# SETUP

yao demonstrates a basic task runner in Common Lisp. This example specifically targets CLISP, though you may write similar task runners for other Common Lisp implementations.

Inside the [example](example) directory, we have a simple `hello.lisp` application representing a Common Lisp project. At the same level of the project is a `yao` task runner script.

Create a `yao` Common Lisp script file at the top level of your project. Omit any file extensions. Ensure the file receives executable chmod bits.

## Shebang

```commonlisp
#!/bin/sh
#|
exec clisp -q -q $0 $0 ${1+"$@"}
|#
```

The complex shebang above provides a POSIX compliant way to load the Common Lisp interpreter and query CLI arguments.

The repetition `$0 $0` in the shebang works around a limitation in CLISP, which historically failed to present the name of the script for querying.

## Import libraries

```commonlisp
; https://www.quicklisp.org/beta/
(load "~/quicklisp/setup.lisp")
(ql:quickload 'uiop :silent t)
```

This example uses a portable, but not HyperSpec standardized, function `run-program` from the uiop third party package. uiop is available from the [Quicklisp](https://www.quicklisp.org/beta/) registry.

## Create a task

```commonlisp
(defun test ()
  (uiop:run-program '("clisp" "hello.lisp") :output t))
```

yao declares tasks in the form of simple Common Lisp functions.

Note the shell module `run` function, which takes a list of command and argument literals, and executes them as a subprocess.

This example merely executes the `hello.lisp` script. You will likely want to alter your project's `test` task to trigger a unit test suite.

A more typical Common Lisp project may feature `lint` tasks to detect coding quirks, `clean` tasks to remove build artifacts, and/or a `build` task to trigger compilation commands.

## Entrypoint

```scheme
(let ((default-task "test")
      (args (cdr *args*)))
  (if (eq (list-length args) 0)
      (funcall (intern (string-upcase default-task)))
      (mapcar #'(lambda (arg)
                  (funcall (intern (string-upcase arg))))
              args)))
```

Above, we have a Scheme entrypoint. The entrypoint declares the symbol `'test` as the default task, simililar to the `all` default task convention in the make build system. You may choose another task symbol, whichever task is most relevant for your project.

When the user executes the shell command `./yao` with no arguments, then the default task processes.

When the user supplies some task arguments like `./yao test`, then the default task is ignored, and only the list of named tasks in the command line arguments will process.

## Further Research

Use a Common Lisp parser to extract the function names from its own script, then generate a usage message as a `help` task.

Validate command line arguments against the list of task names.

Clean up some DRY violations, by having the `args` variable default like `(list <default task>)` when no CLI arguments are specified. (The DRY violation also deserves to be cleaned up in the related mian Scheme example.)

# SEE ALSO

* Inspiration from [nobuild](https://github.com/tsoding/nobuild), a convention for C/C++ build systems
* [bashate](https://github.com/openstack/bashate), a shell script style linter
* [beltaloada](https://github.com/mcandre/beltaloada), a shell build system
* [bb](https://github.com/mcandre/bb), a build system for (g)awk projects
* [Gradle](https://gradle.org/), a build system for JVM projects
* [jelly](https://github.com/mcandre/jelly), a JSON task runner
* [lake](https://luarocks.org/modules/steved/lake), a Lua task runner
* [Leiningen](https://leiningen.org/) + [lein-exec](https://github.com/kumarshantanu/lein-exec), a Clojure task runner
* [lichen](https://github.com/mcandre/lichen), a sed task runner
* [Mage](https://magefile.org/), a task runner for Go projects
* [mian](https://github.com/mcandre/mian), a task runner for (Chicken) Scheme projects
* [npm](https://www.npmjs.com/), [Grunt](https://gruntjs.com/), Node.js task runners
* [POSIX make](https://pubs.opengroup.org/onlinepubs/009695299/utilities/make.html), a task runner standard for C/C++ and various other software projects
* [Rake](https://ruby.github.io/rake/), a task runner for Ruby projects
* [Rebar3](https://www.rebar3.org/), a build system for Erlang projects
* [rez](https://github.com/mcandre/rez) builds C/C++ projects
* [sbt](https://www.scala-sbt.org/index.html), a build system for Scala projects
* [Shake](https://shakebuild.com/), a task runner for Haskell projects
* [ShellCheck](https://www.shellcheck.net/), a shell script linter with a rich collection of rules for promoting safer scripting
* [slick](https://github.com/mcandre/slick), a linter to enforce stricter, unextended POSIX sh syntax compliance
* [stank](https://github.com/mcandre/stank), a collection of POSIX-y shell script linters
* [tinyrick](https://github.com/mcandre/tinyrick) for Rust projects

😈
