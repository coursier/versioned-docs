---
title: TTL
id: version-2.1.0-M2-19-g5a34ba7c1-ttl
original_id: ttl
---

Changing things in cache are given a time-to-live (TTL) of **24 hours** by default.
Changing things are artifacts for versions ending with `-SNAPSHOT`, Maven metadata files listing available versions, etc.

There are two ways to change the default value:
1. set `COURSIER_TTL` the environment variable
2. set `coursier.ttl` JVM property \
(NOTE: if you use Coursier via SBT set `lmcoursier.internal.shaded.coursier.ttl` property)

Both settings are parsed with `scala.concurrent.duration.Duration`, so that things like `24 hours`, `5 min`, `10s`, or `0s`, are fine, and it accepts infinity (`Inf`) as a duration.

