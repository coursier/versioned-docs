---
title: sbt plugin
id: version-1.1.0-M14-3-quick-start-sbt
original_id: quick-start-sbt
---

Enable the sbt plugin by adding

```scala
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.1.0-M13-4")
```

either
- to the `project/plugins.sbt` file of an sbt project, or
- to `~/.sbt/1.0/plugins/build.sbt` (globally, not recommended).

See [the dedicated page](sbt-coursier.md) for more details.

The sbt plugin only supports sbt 1.x. See the
[1.0.x versions](https://github.com/coursier/coursier/tree/series/1.0.x)
of coursier for sbt 0.13.x support.
