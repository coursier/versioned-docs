---
title: sbt-coursier
id: version-1.1.0-M10-sbt-coursier
original_id: sbt-coursier
---

`sbt-coursier` ensures one's dependencies are fetched via coursier rather
than by sbt itself, that relies on its own custom version of Ivy. When
properly set up (see below), coursier can also be used to fetch most sbt plugins. Note that sbt-coursier itself is still fetched by sbt. Also note that the JARs of sbt, when one launches sbt for the first time, are also still fetched by sbt itself.

It currently requires sbt 1.x. See
[coursier versions 1.0.x](https://github.com/coursier/coursier/tree/series/1.0.x)
for sbt 0.13.x.

The sbt plugin can be enabled either on a per-project basis (recommended), or globally.

It mainly overrides the `update`, `updateClassifiers`, and `updateSbtClassifiers` tasks. It does
not handle publishing for now.

## Setup

### Per project

#### No other plugin

Directly add sbt-coursier to `project/plugins.sbt`, with

```scala
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.1.0-M10")
```

#### Along other plugins

Add sbt-coursier in the meta-build, by adding the following to `project/project/plugins.sbt`,

```scala
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.1.0-M10")
```

This will use sbt-coursier to fetch sbt plugins from `project/*.sbt`. Then add sbt-coursier along the other
plugins by adding the following to `project/plugins.sbt`,

```scala
addSbtCoursier
```

### Globally

This method is not recommended. Adding plugins globally makes your builds less reproducible, as their
behavior now depends on the global plugins set up on the machine they are run. On the other hand, this
method is more straightforward than the above, as it applies to all projects at once.

Add the following to `~/.sbt/1.0/plugins/build.sbt`,

```scala
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.1.0-M10")
```

