# Proposed replica and host boundary

## Status and scope

Proposed, 2026-11-20. This contract defines the native Flock boundary for
Murder's read-only and later authoring slices. It specifies semantics rather
than released Rust signatures or a new network protocol. The
[technical design](../murder-design.md) supplies rationale and validation
obligations; upstream evidence governs the selected wire profile.

## 1. Ownership and values

Start with one library and concrete, synchronous APIs. Separate model, codec,
and synchronization-policy modules internally. The host supplies admitted input
and owns I/O. No API requires Tokio, a database transaction, a worker IPC
envelope, credentials, Magitek, or a Youxia dependency.

| Value             | Required meaning                                                                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Format profile    | Immutable identifier for the recovered key, clock, snapshot, delta, and version-summary rules, including unsupported cases                            |
| Replica binding   | Host-chosen document/stream identity, replica lineage, and owner generation; never a reusable global cursor                                           |
| Entry             | Exact supported tuple key, whole JSON value or tombstone, and comparison clock; preserve information needed by the selected profile                   |
| Import result     | Applied, valid duplicate/no semantic change, or a typed failure; never durable storage or remote-acknowledgement evidence                             |
| Change batch      | Bounded affected-key/value/tombstone information with source provenance; no command authority or distributed transaction                              |
| Progress evidence | Separate received, durably recoverable, applied/projected, and checkpoint positions with their binding                                                |
| Prepared mutation | Later authoring capability: stable operation identity, base revision, exact entries and encoded bytes, next authoring state, and resource reservation |

_Table 1: Semantic values at the library boundary._

Keep stream offsets opaque to the Flock model. The host's qualified Streams
adapter establishes range continuity; a clock vector does not establish it.
Version native local checkpoint and IPC formats independently of upstream Flock
bytes. The first reader can retain upstream snapshots and exact deltas without
inventing a native checkpoint or exporting a binary snapshot.

## 2. Read-only operations

The reader admits a supported binary snapshot or JSON delta, imports it into a
single-owner replica, exposes exact-key/prefix queries and version summaries,
and returns bounded incremental changes. The public read view has no local
`put`, `delete`, publisher, or command-execution authority. Remote imports may
change local memory and durable caches.

Decode and validate a complete admitted payload before installing its changes.
Reserve staging and result capacity first. A rejected import leaves the visible
replica unchanged and returns a typed reason; resource failure must not turn
into an empty successful import. Worker termination can interrupt installation,
in which case the host discards that instance and reconstructs it. No partial
projection or applied watermark may escape the failed attempt.

Distinguish malformed input, unsupported format/version, conflicting values or
tombstones for the same key and clock, exceeded limits, stale generation,
missing history, and storage failure. Storage failure originates in the host,
not in a fabricated library commit result. Keep unknown row families and fields
in generic state where the wire profile permits them. A typed Lody projection
can reject an invalid known row without deleting its underlying replicated
entry.

Every query reports the host's evidence status: partial bootstrap, current
through a known boundary, stale, unsupported, or blocked. A successful local
scan alone cannot establish that the cloud has no later records. Pagination
must bind one local revision or report invalidation; it must not silently
stitch inconsistent pages across imports.

## 3. Observer synchronization

Murder may expose a small transport-neutral transition component for bootstrap,
snapshot-plus-tail ingestion, resume, and explicit gap/error reporting. It
consumes supplied records and durability results and returns bounded decisions.
The host schedules those decisions and performs storage and network effects.
This component must not grow a second runtime, database abstraction, or retry
service.

An observer receives read authority only. Its decisions cannot request stream
creation/deletion, appends, snapshot publication, local up-sync, presence,
read-status writes, claims, or permission answers. Enforce this in the host
adapter as well as the library mode. A restored replica ahead of the server
remains read-only during bootstrap and resume; imported or restored local state
cannot enable the ordinary bidirectional initial-join upload path.

Use distinct results for no route, joined, caught up through a declared
boundary, stale data, and failure. Zero attempted transports cannot mean
confirmed synchronization. An unavailable required snapshot or uncovered
history returns a gap/unsupported outcome. A qualified complete-log bootstrap
can start without a snapshot. Rebootstrap requires proof of replacement
coverage; it cannot reset a cursor and present an incomplete replica as current.

## 4. Durability and recovery

The host binds each complete source record to its replica lineage, generation,
profile, stream interval, and digest. Two host integration forms are valid:

1. Apply the record, commit its exact recoverable representation, then commit
   the corresponding cursor after the durability barrier.
2. Commit the exact raw record and its contiguous received cursor atomically,
   then apply it and commit projection/events with a separate applied position.

The second form matches Youxia's proposed inbox architecture. It requires a
known recoverable starting state and the complete retained suffix. Its raw
cursor proves recoverability, not successful decoding or a current projection.
Malformed input can remain durably quarantined while applied progress stops.
Never call that room current or silently advance past the rejected record.

After worker replacement, replay every record needed to reconstruct native
state, even if an application projection already records a later applied
position. That position suppresses duplicate outward events; it does not
authorize skipping imports into an empty replacement replica. Reject callbacks
from old generations and checkpoints from a different lineage/profile.

Prune only after a committed checkpoint or retained upstream snapshot plus
suffix proves recovery coverage. A crash before cursor commit can cause
duplicate imports; a cursor ahead of recoverable data is invalid. A vector
maximum or `inclusiveVersion()` does not prove coverage. Preserve the explicit
case where peer P's clock-20 row exists but its clock-10 row for another key is
missing.

## 5. Later authoring and publication

Add authoring through an explicit writer capability after the clock and export
profiles qualify. The host supplies durable peer identity and the selected time
source; Murder owns the qualified clock-allocation rule. Importing values does
not automatically authorize local edits. Per-row authorization and command
policy remain host responsibilities.

Prepare a mutation against a local revision without changing the visible
replica. The prepared result contains a stable operation identity, exact
entries, outbound bytes, and the next authoring state. The host serializes all
imports and local mutations from preparation through durable disposition and
installation. It validates the base revision before committing that result with
its durable outbox/identity records, then installs it and publishes committed
changes. It must not reject a stale preparation only after those bytes become
publishable.

A confirmed rollback discards the preparation. An indeterminate commit result
blocks further authoring until the host reconciles the operation identity with
durable storage. It must neither reuse the clock nor generate replacement bytes
while the outcome remains unknown. A crash after commit but before installation
reconstructs from the durable record.

Bound the preparation lifetime and reserve its memory before admission. Do not
hold a database transaction open while waiting for an arbitrary caller. If the
native engine cannot support this preparation boundary, a revised contract must
explicitly invalidate and reconstruct a mutated instance after failed storage;
it cannot silently continue with uncommitted clocks or different retry bytes.

Retries transmit the committed bytes and stable producer identity rather than
re-authoring equivalent JSON. Keep local commit, transport attempt, server
acceptance, and peer observation separate. A synchronization attempt that races
a new mutation cannot clear that newer dirty generation. A restored outbox
provides unsent work after process restart; an in-memory dirty flag does not.

Binary snapshot export is a separate writer capability. No observer depends on
it. Tombstone pruning requires a qualified retention/frontier contract; local
age alone cannot authorize collection while offline replicas may return.

## 6. Compatibility and fixture records

The [fixture manifest schema](fixture-manifest.schema.json) records corpus
provenance and hashed input/expected artefacts. The
[example manifest](fixture-manifest.example.json) is deliberately `planned` and
contains no fixture bytes or claimed results. Schema validation proves its
shape only. A corpus validator must also verify unique case identifiers, file
containment and hashes, generator/lockfile identity, licence and sanitization
records, independent expected results, and the selected profile's mandatory
cases.

Track wire-format support, library resource evidence, consumer durability, and
deployment qualification separately. Fixture-qualified native reading may ship
before Youxia's integration is ready. The latter must pass its own adapter and
deployed-profile gates before advertising support. A fake adapter can establish
the consuming contract, but cannot qualify Murder's codecs.

The initial consumer contract includes binary snapshot import, JSON delta
import, merge, tuple/prefix reads, tombstones, both version summaries, bounded
changes, and typed failure. It excludes clock authoring, exports, and remote
mutation. Add supported capabilities through versioned profiles rather than
silently broadening a previously qualified subset.
