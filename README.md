# Murder

*A native implementation of Lody's Flock protocol (as in birds).*

Murder will provide the Flock model and codecs that Youxiad needs for native
metadata replication. The first slice reads snapshots and deltas; later slices
add durable authoring. The [technical design](docs/murder-design.md) defines
the scope and its evidence from `leynos/lody-docs`.

______________________________________________________________________

## Current status

The project is at the design stage. This repository contains a proposed
technical design, host contract, fixture-manifest schema, and development
roadmap. It does not yet contain a Rust implementation or qualified codec. The
example manifest describes planned evidence, not passing fixtures.

______________________________________________________________________

## Why Murder?

Flock has its own tuple keys, clocks, tombstones, and wire formats. Murder will
implement those semantics in a native library while the host supplies Streams
transport, persistence, and scheduling. This boundary lets Murder, Youxia, and
Magitek develop in parallel, with explicit integration gates.

______________________________________________________________________

## Start here

- [Technical design](docs/murder-design.md): scope, ownership, compatibility
  evidence, read-only behaviour, and validation obligations.
- [GIST roadmap](docs/roadmap.md): review-sized tasks, dependencies, and
  measurable acceptance criteria.
- [Replica and host boundary](docs/contracts/replica-boundary.md): the proposed
  reader and later writer contract.
- [Documentation contents](docs/contents.md): schema, example, and supporting
  records.

Implementation contributions should follow the roadmap's qualified profiles and
update the contract when they change observable behaviour. Licence and upstream
reuse decisions form part of the initial qualification work.
