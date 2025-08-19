---
title: sbt-pgp-coursier
id: version-2.1.25-M17-sbt-pgp-coursier
original_id: sbt-pgp-coursier
---

This plugin is meant to be used in conjunction with [sbt-pgp](https://github.com/sbt/sbt-pgp). [sbt-pgp](https://github.com/sbt/sbt-pgp) has a `checkPgpSignatures` command, that can validate
the PGP keys of your dependencies. sbt-pgp-coursier ensures coursier is used
during the resolution triggered by `checkPgpSignatures`, that downloads
PGP signatures in particular.

## Setup

Add sbt-pgp-coursier to `project/plugins.sbt`, with
```scala
//
addSbtPlugin("io.get-coursier" % "sbt-pgp-coursier" % "2.1.4")
```

## Usage

Running the `checkPgpSignatures` from [sbt-pgp](https://github.com/sbt/sbt-pgp) then relies on coursier
to download artifacts,
```bash
$ sbt checkPgpSignatures
```
