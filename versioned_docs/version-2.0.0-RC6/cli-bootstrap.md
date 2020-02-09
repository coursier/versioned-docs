---
title: bootstrap
id: version-2.0.0-RC6-cli-bootstrap
original_id: cli-bootstrap
---

Like the [`fetch` command](cli-fetch.md), `bootstrap` resolves and fetches the
JARs of one or more dependencies. Like the [`launch` command](cli-launch.md),
it tries to find a [main class](cli-launch.md#main-class) in those JARs
(unless `-M` specifies one already). But unlike [`launch`](cli-launch.md), the
`bootstrap` command doesn't launch this main class straightaway. Instead, it
generates a (usually tiny) JAR, that can be renamed, copied, moved to another
machine or OS, and is able to download and launch the corresponding application.

For example,
```bash
$ coursier bootstrap com.geirsson:scalafmt-cli_2.12:1.5.1 -o scalafmt
$ ./scalafmt --version
scalafmt 1.5.1
```

## bootstrap content

The generated bootstraps are tiny Java apps, that upon launch, successively
- read a list of URLs for one of their resource files,
- read a main class name from another of their resource files,
- download the URLs missing from the coursier cache,
- get all the files corresponding to these URLs from the coursier cache,
- load those files in a class loader,
- find the main class by reflection,
- call the main method of the main class, which actually runs the application.

Overall, this "bootstraps" the application, hence the name.

All of that logic is handled by a small Java application, which currently
corresponds to the [`bootstrap-launcher` module](https://github.com/coursier/coursier/tree/bf9925778096eb24a3d3018079688d4255499457/modules/bootstrap-launcher)
of the coursier sources. A bootstrap consists of that module classes,
along with resource files (for the artifact URLs and main class to load) that
are specific to each application to launch.

## Java options

The generated bootstraps automatically pass their arguments starting with
`-J` to Java, stripping the leading `-J`, e.g.
```bash
$ coursier bootstrap com.lihaoyi:ammonite_2.12.8:1.6.0 -M ammonite.Main -o amm
$ ./amm -J-Dfoo=bar
Loading...
Welcome to the Ammonite Repl 1.6.0
(Scala 2.12.8 Java 1.8.0_121)
@ sys.props("foo")
res0: String = "bar"
```

Alternatively, one can hard-code Java options when generating the bootstrap,
by passing `--java-opt` options, like
```bash
$ coursier bootstrap com.lihaoyi:ammonite_2.12.8:1.6.0 -M ammonite.Main -o amm \
    --java-opt -Dfoo=bar
$ ./amm
Loading...
Welcome to the Ammonite Repl 1.6.0
(Scala 2.12.8 Java 1.8.0_121)
@ sys.props("foo")
res0: String = "bar"
```

## Windows

The launchers generated by the `bootstrap` command contain a shell preamble,
and are made executable, so that even though they are JARs, they can be run
straightaway on Linux and OS X. On Windows, the `bootstrap` command also
generates a `.bat` file along with the launcher (simply appending `.bat` to the
launcher file name). This `.bat` file allows one to run the launcher on Windows
nonetheless, almost as on Linux / OS X.

To generate this `.bat` file on Linux or OS X (to offer to download it for
example), pass `--bat` to the bootstrap command, like
```bash
$ coursier bootstrap com.geirsson:scalafmt-cli_2.12:1.5.1 -o scalafmt --bat
$ ls -lh scalafmt*
-rwxr-xr-x  1 user  group    18K  8 jan 15:24 scalafmt
-rw-r--r--  1 user  group   2,0K  8 jan 15:24 scalafmt.bat
```

Inversely, to disable generating it on Windows, pass `--bat=false` to the
`bootstrap` command.

## Standalone bootstraps

Rather than having the launcher download its JARs upon first launch, one may
prefer to ship those JARs along with the launcher. The `--standalone` option
allows just that. This options adds the application JARs as resources in the
launcher. This inflates the launcher size, but that allows it to run without
relying on the coursier cache or downloading things.

Use like
```bash
$ coursier bootstrap com.geirsson:scalafmt-cli_2.12:1.5.1 -o scalafmt --standalone
$ ls -lh scalafmt
-rwxr-xr-x  1 alexandre  staff    15M  8 jan 15:31 scalafmt
$ ./scalafmt --version
scalafmt 1.5.1
```

Standalone bootstraps should be similar to JARs generated by
[One-JAR](http://one-jar.sourceforge.net). Note that although they ship with
all their dependencies like so-called [uber JARs](http://maven.apache.org/plugins/maven-shade-plugin/) or [assemblies](https://github.com/sbt/sbt-assembly),
they differ from them in the sense that:
- dependencies are still kept separate (each dependency stays in its own JAR, themselves added as JARs to the launcher - these are JARs in JARs), so that no
conflict has to be resolved,
- the launchers can't be added as is to a classpath, like standard JARs, or
assemblies / uber JARs. This prevents using them as is to package spark jobs
in particular.

## Local artifacts

Some of the JARs of the dependencies passed to the `bootstrap` command may
correspond to local files (e.g. from the local `~/.ivy2/local` repository)
rather than files from remote repositories. In that case, these local files
are going to be included as _resources_, rather than via a URL, in the
generated launchers. That allows these launchers to be copied to other
machines, and work fine even without the original local repositories.
To disable that behavior, pass `--embed-files=false` to the `bootstrap`
command. Note that this only applies when the `--standalone` option is
not passed - if it is, JARs are added as resources to the launcher
nevertheless.