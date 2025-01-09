---
title: Scala.js
id: version-2.1.24-api-scala-js
original_id: api-scala-js
---

The core modules of coursier are cross-compiled and published for
[Scala.js](https://www.scala-js.org). This allows to run resolutions from
Node.js or from the browser, via Scala.js. Note that only the core module
of coursier, running resolutions, is cross-compiled. The cache module is not,
so that one cannot rely on the cache of coursier from Scala.js.

As a substitute to the cache module, a module named
[`fetch-js`](https://repo1.maven.org/maven2/io/get-coursier/coursier-fetch-js_sjs0.6_2.12)
is published for Scala.js, allowing one to fetch metadata and possibly
artifacts via XMLHttpRequests.

From sbt, one can depend on the core and fetch modules via
```scala
libraryDependencies ++= Seq(
  "io.get-coursier" %%% "coursier-core" % "2.1.24",
  "io.get-coursier" %%% "coursier-fetch-js" % "2.1.24"
)
```
or simply via the `coursier` module, which aggregates both,
```scala
libraryDependencies += "io.get-coursier" %%% "coursier" % "2.1.24"
```

As an illustration, coursier has [an in-browser demo](../demo), that allows one
to run resolutions entirely from the browser.
