---
title: API
id: version-1.1.0-M14-5-quick-start-api
original_id: quick-start-api
---

Add to your `build.sbt`

```scala
libraryDependencies ++= Seq(
  "io.get-coursier" %% "coursier" % "1.1.0-M14-5",
  "io.get-coursier" %% "coursier-cache" % "1.1.0-M14-5"
)
```

Add an import for coursier,

```scala
import coursier._
```

To resolve dependencies, first create a `Resolution` case class with your dependencies in it,

```scala
val start = Resolution(
  Seq(
    Dependency(
      Module(org"org.scalaz", name"scalaz-core_2.11"), "7.2.3"
    ),
    Dependency(
      Module(org"org.typelevel", name"cats-core_2.11"), "0.6.0"
    )
  )
)
```

Create a fetch function able to get things from a few repositories via the local cache,

```scala
import coursier.cache.Cache
import coursier.LocalRepositories

val repositories = Seq(
  LocalRepositories.ivy2Local,
  MavenRepository("https://repo1.maven.org/maven2")
)

val fetch = ResolutionProcess.fetch(repositories, Cache.default.fetch)
```

Then run the resolution per-se,

```scala
import scala.concurrent.ExecutionContext.Implicits.global

val resolution = start.process.run(fetch).unsafeRun()
```

That will fetch and use metadata.

Check for errors in

```scala
val errors: Seq[((Module, String), Seq[String])] =
  resolution.errors
```

Any error would mean that the resolution wasn't able to get metadata about some dependencies.

Then fetch and get local copies of the artifacts themselves (the JARs) with

```scala
import java.io.File
import coursier.cache.ArtifactError
import coursier.util.{Gather, Task}

val localArtifacts: Seq[Either[ArtifactError, File]] =
  Gather[Task].gather(
    resolution
      .artifacts()
      .map(Cache.default.file(_).run)
  ).unsafeRun()
```

See [the dedicated page](api.md) for more details.

