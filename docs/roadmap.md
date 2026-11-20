# Murder roadmap

This roadmap turns the [technical design](murder-design.md) and
[replica boundary](contracts/replica-boundary.md) into a native Flock reader, a
recoverable read-only embedding, and later qualified authoring. The pinned
[Lody Flock documentation][flock-docs] supplies protocol evidence; its
observations and this roadmap do not establish implemented compatibility. The
design records the current decisions, evidence gaps, and deferred scope.

Goals, Ideas, Steps, and Tasks (GIST) organize the work. Each phase states a
falsifiable idea, each step answers a delivery question, and each task defines
a review-sized change with observable acceptance criteria. All tasks remain
unchecked. Unit, behavioural, property, and small-model checks accompany the
implementation they validate. Shared corpora and cross-boundary fault suites
are separate deliverables. The roadmap makes no calendar commitments.

Murder develops independently of Youxia, Magitek, and their runtime delivery.
Tasks 1.3.1 and 3.2.2 give consumers a fake contract and then a qualified
native reader. Steps 3.3 and 4.4 require real Youxia integration; no Murder
library release or internal authoring task depends on those steps. Magitek is
not a Murder dependency. An unresolved format or licence blocks the affected
profile, not unrelated model, harness, or consumer work.

## 1. Establish an evidence-backed native implementation route

Idea: a pinned profile, independent corpus, and small embedding contract can
expose incompatible assumptions before native implementation depends on them.
The idea fails if essential reader semantics remain unavailable, or the only
usable route requires JavaScript or WebAssembly in production.

This phase delivers a native build spine and reproducible evidence. It does not
select a licence on the maintainer's behalf, invent missing wire rules, or
claim that a fake qualifies a codec.

### 1.1. Resolve the profile and access questions that constrain native reading

Which Flock bytes and semantics can Murder implement and distribute? The answer
defines the initial profile and isolates gaps that must block particular
capabilities. See
[design §2](murder-design.md#2-evidence-and-compatibility-profiles).

- [ ] 1.1.1. Record the initial reader profile and its evidence ledger.
  - [ ] Requires no earlier task.
  - [ ] See [design §§1–2](murder-design.md#1-purpose-and-outcomes).
  - [ ] Success: the record pins Lody, `lody-docs`, Flock, `loro-repo`, and
    Streams adapter versions; enumerates required snapshots, deltas, keys,
    clocks, and version summaries; and distinguishes known rules, assumptions,
    and unsupported cases. Reading and authoring have separate support claims.
- [ ] 1.1.2. Record the native source or format route and licence decision.
  - [ ] Requires 1.1.1.
  - [ ] See [design §2](murder-design.md#2-evidence-and-compatibility-profiles)
    and [design §11](murder-design.md#11-decisions-and-deferred-scope).
  - [ ] Success: a reviewed decision identifies usable source or sufficient
    format evidence, attribution and redistribution obligations, and unresolved
    reader gaps. It separately records authoring-clock and export gaps; those
    gaps do not block a complete reader profile. No inaccessible source or
    undocumented binary grammar appears as an assumed dependency.

### 1.2. Make compatibility evidence reproducible outside the production runtime

Can a contributor rebuild and inspect the same native comparison without a live
service or paid agent? This work establishes the corpus that later reader and
writer claims must name. See
[design §9](murder-design.md#9-validation-obligations).

- [ ] 1.2.1. Establish the single-library Rust build and verification spine.
  - [ ] Requires 1.1.1.
  - [ ] See [design §3](murder-design.md#3-architecture-and-ownership).
  - [ ] Success: documented build, formatting, lint, and test commands run in
    continuous integration (CI); fixture-driven compilation and tests support
    offline execution after declared provisioning. Model, codec, and policy
    modules introduce no async runtime, service executable, database, or consumer
    dependency. Record the selected toolchain and dependency policy.
- [ ] 1.2.2. Implement corpus-manifest and artefact validation.
  - [ ] Requires 1.1.1 and 1.2.1.
  - [ ] See [design §9](murder-design.md#9-validation-obligations) and the
    [fixture manifest schema](contracts/fixture-manifest.schema.json).
  - [ ] Success: validation rejects duplicate case IDs, escaping paths, missing
    files, bad hashes, incomplete provenance, and missing mandatory case groups.
    Planned manifests cannot pass a recorded-evidence gate. Schema shape,
    semantic coverage, licence review, and actual fixture execution produce
    distinct results.
- [ ] 1.2.3. Capture the pinned upstream reader corpus and independent results.
  - [ ] Requires 1.1.2 and 1.2.2.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs) and
    [design §9](murder-design.md#9-validation-obligations).
  - [ ] Success: a reproducible development-only oracle records snapshots,
    JSON deltas, tuple/prefix reads, clocks, tombstones, and both version views
    with generator, lockfile, licence, and sanitization evidence. Cover Machine
    and Workspace Flock, overwritten peers, duplicate/reordered input,
    same-key equal-clock conflicts, and malformed or unsupported encodings.
    Expected state comes from independent evidence, not Murder output copied
    into fixtures.

### 1.3. Let consumers develop before the native codecs are ready

Can Youxia exercise its replica owner without importing an unfinished Murder
implementation? The result fixes a narrow handoff and reveals accidental host
responsibilities. See
[design §10](murder-design.md#10-parallel-development-and-integration-gates).

- [ ] 1.3.1. Publish a versioned replica contract and deterministic fake.
  - [ ] Requires 1.1.1 and 1.2.1.
  - [ ] See [design §3](murder-design.md#3-architecture-and-ownership) and
    [replica boundary §§1–4](contracts/replica-boundary.md#1-ownership-and-values).
  - [ ] Success: a standalone consumer exercises supplied bounded records,
    queries, change batches, stale generations, and durability outcomes without
    Tokio, SQLite, Streams credentials, or an ACP engine. Failure injection
    preserves the same observable contract planned for the native adapter.
    Fake results are labelled as contract evidence, never codec qualification.

## 2. Read a pinned Flock catalogue natively

Idea: native snapshot and delta readers can reproduce a pinned upstream
catalogue while preserving deletion, clock, and query semantics. The idea fails
if matching visible JSON hides divergent tombstones or version summaries, or a
required snapshot still needs a non-native decoder.

This slice delivers useful offline reading through the library and an embedding
example. JSON and binary decoder work can proceed independently after their
shared model and corpus prerequisites. Local clock authoring and binary export
remain outside the reader gate.

### 2.1. Preserve entries and conflicts without inventing authoring rules

Do tuple ordering, clock comparison, and merge produce the same state under
different import orders? This step supplies the semantic model for both codecs.
See [design §4](murder-design.md#4-data-model-and-codecs).

- [ ] 2.1.1. Implement tuple-key and comparison-clock values.
  - [ ] Requires 1.1.1, 1.2.1, and 1.2.3.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs).
  - [ ] Success: upstream boundary cases establish supported key-part equality,
    ordering, prefix relations, and physical/logical/peer clock comparison.
    Property generators check those relations; unsupported representations and
    overflow fail explicitly. Comparison does not claim a local clock allocator.
- [ ] 2.1.2. Implement whole-row merge, tombstones, and version summaries.
  - [ ] Requires 2.1.1.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs).
  - [ ] Success: permuted, duplicated, and regrouped imports establish
    commutativity, idempotence, and associativity under the stated same-key
    equal-clock premise. Conflicting entries for that key and clock produce the
    selected explicit failure. Tombstones compete with writes, cancellation
    markers remain ordinary values. `version()` and `inclusiveVersion()` retain
    their distinct upstream meanings. Tests retain the same-vector hole case.

### 2.2. Decode snapshots and deltas into the same admitted state

Can both required wire forms feed that model without unbounded intermediate
allocation or partial visible imports? This determines the first supported
format profile. See [design §4](murder-design.md#4-data-model-and-codecs) and
[design §8](murder-design.md#8-resource-limits-and-observability).

- [ ] 2.2.1. Implement the bounded JSON delta decoder and atomic import.
  - [ ] Requires 1.2.3 and 2.1.2.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs) and
    [replica boundary §2](contracts/replica-boundary.md#2-read-only-operations).
  - [ ] Success: required fields, numbers, nesting, entry counts, and retained
    values obey the selected profile and limits. The complete admitted batch
    validates before installation; malformed, conflicting, and over-limit
    input leaves the visible replica unchanged. Boundary cases compare with
    the oracle and shrinking retains the failing key/clock relationship.
- [ ] 2.2.2. Implement the selected binary snapshot envelope reader.
  - [ ] Requires 1.1.2, 1.2.3, and 2.1.1.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs) and
    [design §8](murder-design.md#8-resource-limits-and-observability).
  - [ ] Success: native code recognizes the recovered version and length rules
    before allocating payload structures. Truncation, inconsistent lengths,
    unsupported versions, and expansion limits fail at the declared boundary.
    This task can progress independently of the JSON delta decoder; the binary
    path requires no upstream runtime.
- [ ] 2.2.3. Import binary entries and summaries through the native merge model.
  - [ ] Requires 2.2.2 and 2.1.2.
  - [ ] See [design §4](murder-design.md#4-data-model-and-codecs) and
    [design §5](murder-design.md#5-the-initial-read-only-slice).
  - [ ] Success: independent upstream snapshots restore values, tuple keys,
    clocks, tombstones, and required version metadata. Snapshot-plus-delta
    traces match uninterrupted reference state; rejected snapshots leave the
    previous visible state intact. Import needs no native snapshot exporter.

### 2.3. Deliver bounded queries with explicit compatibility evidence

Can an embedding read a useful catalogue without mistaking partial data for an
empty or current one? The answer defines the initial public reader surface. See
[design §5](murder-design.md#5-the-initial-read-only-slice).

- [ ] 2.3.1. Expose exact-key/prefix queries and bounded change batches.
  - [ ] Requires 2.2.1 and 2.2.3.
  - [ ] See [design §5](murder-design.md#5-the-initial-read-only-slice) and
    [replica boundary §2](contracts/replica-boundary.md#2-read-only-operations).
  - [ ] Success: reads preserve unknown permitted rows and fields; invalid
    typed Lody rows do not erase generic state. Pagination binds a local
    revision or reports invalidation. Imports produce bounded affected-key
    changes, with duplicate/no-change distinct from failure. Query evidence
    cannot promote a partial host view to current by scanning successfully.
- [ ] 2.3.2. Enforce retained-state limits and expose bounded diagnostic facts.
  - [ ] Requires 2.3.1.
  - [ ] See [design §8](murder-design.md#8-resource-limits-and-observability)
    and [design §9](murder-design.md#9-validation-obligations).
  - [ ] Success: residency and result budgets account for old state, staging,
    peer summaries, tombstones, changes, and caller-retained query results.
    Repeated imports and held results reject before exceeding declared limits.
    Bounded statistics expose cost and rejection without payload logging or
    arbitrary keys/peers as metric labels. Peak-memory evidence names exclusions.
- [ ] 2.3.3. Qualify the native reader through the embedding example.
  - [ ] Requires 1.3.1 and 2.3.2.
  - [ ] See [design §§5 and 9](murder-design.md#5-the-initial-read-only-slice).
  - [ ] Success: the same contract corpus runs through the fake and real
    reader, while independent fixtures separately prove codec semantics.
    A native executable reads snapshots and retained deltas without a browser,
    TypeScript, or WebAssembly dependency. The support record names passed,
    unsupported, failed, and unrun cases instead of declaring universal Flock
    compatibility.

## 3. Keep a read-only embedding recoverable through interruption

Idea: supplied stream records and explicit durability outcomes suffice for a
recoverable observer without giving Murder network or storage ownership. The
idea fails if cursor advancement can outrun recoverable bytes, a restart loses
state behind a version summary, or observer bootstrap publishes local data.

The standalone host harness qualifies the library boundary before Youxia is
available. Real Youxia adoption is a separate consumer gate in step 3.3; it
does not gate the reader handoff or the authoring work in phase 4.

### 3.1. Preserve state and evidence across bootstrap and replay

Can the observer distinguish received input, recoverable input, applied state,
and a confirmed catch-up boundary? This determines its resume and failure
semantics. See [design §§5–6](murder-design.md#5-the-initial-read-only-slice).

- [ ] 3.1.1. Implement transport-neutral read-only synchronization decisions.
  - [ ] Requires 1.3.1 and 2.3.3.
  - [ ] See [design §5](murder-design.md#5-the-initial-read-only-slice) and
    [replica boundary §3](contracts/replica-boundary.md#3-observer-synchronization).
  - [ ] Success: bootstrap accepts a complete retained log or a qualified
    snapshot plus covered suffix; missing required history produces an explicit
    gap. No-route, joined, caught-up, stale, and failed remain distinct.
    Exhaustive finite-state exploration checks that observer decisions cannot
    create a stream, append, publish a snapshot, or upload restored local state.
    Transport spies in 3.2.1 check the embedding also enforces that boundary.
- [ ] 3.1.2. Integrate replica-bound durability barriers and progress positions.
  - [ ] Requires 3.1.1.
  - [ ] See [design §6](murder-design.md#6-durability-and-recovery) and
    [replica boundary §4](contracts/replica-boundary.md#4-durability-and-recovery).
  - [ ] Success: a fake host exercises both declared commit orderings and
    rejects cursor/checkpoint evidence from another profile, lineage, or owner
    generation. A small TLA+/TLC host model covers commit, failure, and
    replacement, with raw and applied positions distinct; cursor-first
    ablation fails. No cursor covers unavailable input, and a raw durable
    cursor never implies successful projection or catch-up.
- [ ] 3.1.3. Reconstruct replicas from an exact retained base and suffix.
  - [ ] Requires 3.1.2.
  - [ ] See [design §6](murder-design.md#6-durability-and-recovery).
  - [ ] Success: two successive reconstructions preserve tombstones, overwritten
    peer summaries, and the clock-20/clock-10 vector-hole witness. A stored
    projection watermark cannot suppress imports into a fresh replica.
    Duplicate outward events remain suppressed; poison records stop applied
    progress, and pruning refuses a checkpoint without proved coverage.

### 3.2. Publish a qualified reader handoff independently of the real daemon

Can consumers reproduce the reader's limits and failure semantics from its
release record? This step fixes the supported library subset and the evidence
that remains the host's responsibility. See
[design §§8–10](murder-design.md#8-resource-limits-and-observability).

- [ ] 3.2.1. Add the read-only host fault and recovery qualification harness.
  - [ ] Requires 3.1.3.
  - [ ] See [design §9](murder-design.md#9-validation-obligations).
  - [ ] Success: end-to-end traces combine partial transport records, delayed
    durability, failed commits, corrupt snapshots, duplicate delivery, cursor
    expiry, and worker-only replacement. The harness checks exact recoverable
    prefixes and no remote writes, including initial join with local state
    ahead of the supplied server state. Failure injection targets the boundary
    that owns each promise; fake storage results do not claim real disk safety.
- [ ] 3.2.2. Publish the versioned native reader profile and consumer example.
  - [ ] Requires 3.2.1 and 1.1.2.
  - [ ] See [design §8](murder-design.md#8-resource-limits-and-observability)
    and [design §10](murder-design.md#10-parallel-development-and-integration-gates).
  - [ ] Success: a reproducible record names exact dependencies, fixture
    provenance, format support, measured import/query costs and amplification,
    retained-buffer limits, and explicit host preconditions. Oversized records,
    retained query results, and slow consumers have declared outcomes. The
    package and example contain only the qualified native path; release claims
    exclude deployed service compatibility, host durability, and fleet liveness.

### 3.3. Qualify the real Youxia reader as an external integration

Does the real daemon preserve the same contract across worker and storage
boundaries? This step qualifies a consumer deployment, without becoming a
prerequisite for Murder's library release. See
[design §10](murder-design.md#10-parallel-development-and-integration-gates).

- [ ] 3.3.1. Qualify Youxia's Murder adapter and worker recovery.
  - [ ] Requires 3.2.2.
  - [ ] External gate: [Youxia's roadmap][youxia-roadmap] tasks 1.1.1–1.1.2,
    1.2.3, and 2.2.1–2.2.3 supply the deployed profile, native-route review,
    worker boundary, and recoverable store. This work extends the component
    evidence for Youxia task 1.1.3. Its initial fixture-qualified reader
    integration can use Murder 3.2.2 without waiting for this joint worker/store
    gate. The adapter implementation stays in Youxia.
  - [ ] See [design §§6 and 10](murder-design.md#6-durability-and-recovery).
  - [ ] Success: a pinned real worker imports the qualified corpus, restarts
    while its parent/store remain open, and reconstructs before readiness.
    Real commit failure and stale replies cannot advance another generation's
    cursor or publish duplicate projection events.
- [ ] 3.3.2. Compare an authorized live shadow observer with the selected peer.
  - [ ] Requires 3.3.1.
  - [ ] External gate: Youxia 2.1.5 supplies workspace Streams credentials,
    2.2.4 supplies the selected native read transport, and 2.3.1 supplies
    watch-before-list catalogue acquisition. The operator supplies a disposable
    or explicitly authorized observation environment. Complete session-catalogue
    claims additionally require qualification of the actual `:meta` store
    representation; named-Flock conformance cannot establish it.
  - [ ] See [design §§5 and 10](murder-design.md#5-the-initial-read-only-slice).
  - [ ] Success: independent local state agrees on the selected profile's
    catalogue, clocks, and deletion evidence through bootstrap and reconnect;
    captured traffic establishes no source mutation. Record deployed versions
    and unsupported protection/backends. This contributes to Youxia 2.5.1;
    Youxia's combined observation acceptance in 2.5.2 remains separate.

## 4. Author compatible changes without losing durable intent

Idea: exact prepared changes and explicit commit evidence can preserve native
clock identity and eventual replication through failed storage and lost network
acknowledgements. The idea fails if retry re-authors a different operation,
uncertain commit permits clock reuse, or upstream rejects native output.

Clock and encoder work can begin after native reader qualification. No task in
steps 4.1–4.3 depends on real Youxia adoption in step 3.3. Native binary
snapshot export is a separate capability and never gates the read-only slice.

### 4.1. Produce entries that the upstream peer interprets identically

Can native local edits reproduce the observed clock and delta rules? This
determines whether Murder may offer writer authority. See
[design §7](murder-design.md#7-authoring-and-synchronization).

- [ ] 4.1.1. Qualify and implement the local authoring-clock allocator.
  - [ ] Requires 2.3.3.
  - [ ] See [design §§4 and 7](murder-design.md#4-data-model-and-codecs).
  - [ ] Success: independent oracle probes settle imported future clocks, old
    explicit timestamps, repeated timestamps, peer ties, and exhausted ranges.
    A recorded clock profile precedes implementation; generated edit/import
    sequences preserve the qualified rule. An unresolved rule leaves authoring
    unsupported without changing reader support.
- [ ] 4.1.2. Encode local writes, tombstones, and version-filtered JSON deltas.
  - [ ] Requires 4.1.1 and 2.2.1.
  - [ ] See [design §7](murder-design.md#7-authoring-and-synchronization).
  - [ ] Success: upstream reads native entries with identical supported keys,
    values, clocks, deletion results, and version summaries. Missing/already
    deleted keys follow the qualified no-op rule. Delta selection preserves
    the documented visible/inclusive-vector distinction without promising to
    repair an unrepresented version-vector hole.

### 4.2. Preserve prepared edits through commit uncertainty and resynchronization

Can a host persist a mutation once and reconcile every later outcome without
minting another change? This step establishes authoring and synchronization
semantics before real storage or network adoption. See
[design §§6–7](murder-design.md#6-durability-and-recovery).

- [ ] 4.2.1. Implement prepared mutations and explicit durable disposition.
  - [ ] Requires 4.1.2 and 3.1.2.
  - [ ] See
        [replica boundary §5](contracts/replica-boundary.md#5-later-authoring-and-publication).
  - [ ] Success: preparation binds a base revision, stable operation identity,
    exact bytes, next authoring state, and reservation. The owner serializes
    imports and edits through base validation, durable disposition, and install.
    Definite rollback discards preparation; indeterminate commit blocks new
    authoring until the durable record resolves it. Crash/acknowledgement-loss
    witnesses prove no conflicting install, clock reuse, or re-authored retry.
    Extend the host model with preparation and indeterminate-disposition states.
- [ ] 4.2.2. Implement bounded writer synchronization decisions.
  - [ ] Requires 4.2.1 and 3.1.1.
  - [ ] See [design §7](murder-design.md#7-authoring-and-synchronization).
  - [ ] Success: only committed entries become publication candidates; attempts
    retain exact bytes and host-supplied producer identity. Finite-state
    exploration preserves a newer dirty generation during synchronization;
    removing its fence fails the write-during-sync witness. No-route, local
    commit, transport acceptance, and peer evidence stay distinct. Observer-mode
    traces continue to produce no publication decisions.
- [ ] 4.2.3. Qualify writer restart and ambiguous-send recovery in the host
  harness.
  - [ ] Requires 4.2.2 and 3.1.3.
  - [ ] See [design §§6–7](murder-design.md#6-durability-and-recovery) and
    [design §9](murder-design.md#9-validation-obligations).
  - [ ] Success: combined faults across prepare, commit, install, send, and
    acknowledgement retain every accepted operation and its identity. Restored
    outboxes supply unsent work without an in-memory dirty flag. Recreated
    streams, old database images, and unresolved peer horizons require an
    explicit qualified identity/reconciliation decision rather than blind send.

### 4.3. Qualify a native writer profile with independent peer evidence

Can upstream consume authored snapshots and deltas through the declared fault
surface? The answer defines a writer release separately from the reader. See
[design §§7–9](murder-design.md#7-authoring-and-synchronization).

- [ ] 4.3.1. Implement the qualified native binary snapshot exporter.
  - [ ] Requires 2.2.3 and 4.1.2.
  - [ ] See [design §§4 and 7](murder-design.md#4-data-model-and-codecs).
  - [ ] Success: upstream imports native snapshots with equivalent entries,
    tombstones, clocks, and required summaries; native reads also accept them.
    Export observes one revision and obeys staging/output limits. Compare
    semantic state and required identities rather than unstable binary layout
    where the profile permits more than one encoding.
- [ ] 4.3.2. Publish writer conformance, resource, and compatibility evidence.
  - [ ] Requires 4.2.3 and 3.2.2. A profile advertising binary snapshot export
    additionally requires 4.3.1.
  - [ ] See [design §9](murder-design.md#9-validation-obligations) and
    [design §10](murder-design.md#10-parallel-development-and-integration-gates).
  - [ ] Success: a pinned upstream peer and native embedding agree across
    concurrent edits, deletion, delayed deltas, restarts, and lost responses.
    The versioned writer profile records actual resource measurements and
    excluded cases. Failure, retained preparations, and slow publication remain
    within declared library limits without claiming host disk or server safety.

### 4.4. Integrate native authoring with Youxia's durable outbox

Does the consumer preserve the writer contract when storage and Streams own the
effects? This supplies Youxia's authoring evidence while keeping its production
hosting gate distinct. See
[design §10](murder-design.md#10-parallel-development-and-integration-gates).

- [ ] 4.4.1. Qualify the Youxia-owned Murder writer adapter.
  - [ ] Requires 3.3.1 and 4.3.2.
  - [ ] External gate: Youxia tasks 3.1.1–3.1.2 provide atomic local records,
    the durable outbox, and qualified producer/acknowledgement handling. This
    integration supplies evidence for Youxia task 3.1.4.
  - [ ] See
        [design §§7 and 10](murder-design.md#7-authoring-and-synchronization).
  - [ ] Success: real storage failures, worker replacement, lost replies, and
    native-authored metadata preserve committed bytes and identities; the
    selected reference reader accepts them. No Magitek dependency enters
    Murder, and passing metadata authoring does not claim ACP hosting,
    exactly-once execution, or full daemon liveness.

## 5. Admit further profiles only when a consumer needs them

Idea: a qualified reader and writer let later protocol features justify their
own cost without destabilizing existing consumers. The idea fails if an
extension silently changes a previously qualified wire or recovery contract.

These are conditional workstreams. An unsupported feature stays explicit until
its own evidence passes; none is a prerequisite for the initial read-only
profile. See [design §11](murder-design.md#11-decisions-and-deferred-scope).

### 5.1. Qualify a demanded local peer transport

Does a concrete consumer need the local Flock exchange in addition to supplied
Durable Streams records? The result determines whether a separate adapter earns
its maintenance cost. See
[design §11](murder-design.md#11-decisions-and-deferred-scope).

- [ ] 5.1.1. Qualify the selected local-peer profile through an external
  adapter.
  - [ ] Requires 4.3.2.
  - [ ] See
        [design §§7 and 11](murder-design.md#7-authoring-and-synchronization).
  - [ ] Success: pinned handshake, framing, vectors, live updates, and rejoin
    behaviour pass native/reference comparisons with explicit missing-history
    limits. Host authentication and sockets stay outside the library. Local
    protocol qualification cannot substitute for cloud transport evidence.

### 5.2. Extend the supported input envelope without acquiring cloud authority

Does an essential deployment require a protected or differently wrapped stream
profile? The answer controls an explicit profile extension, not a silent change
to the plaintext decoder. See
[design §11](murder-design.md#11-decisions-and-deferred-scope).

- [ ] 5.2.1. Qualify a demanded protected-input profile with its host adapter.
  - [ ] Requires 3.2.2.
  - [ ] External gate: the consuming host supplies the actual protection,
    credential, and envelope contract with authorized fixtures.
  - [ ] See
        [design §§2 and 11](murder-design.md#2-evidence-and-compatibility-profiles).
  - [ ] Success: the adapter supplies verified admitted Flock content or an
    explicit unsupported/failure result. Corrupt, truncated, and wrong-profile
    inputs cannot become a current empty replica. The original reader corpus
    still passes; Murder acquires no login or key-discovery service.

### 5.3. Reclaim history only under a qualified coverage contract

Can demanded compaction reduce retained state without changing deletion
semantics or losing offline replicas' required evidence? The answer determines
whether a checkpoint or collection policy is supportable. See
[design §§6 and 11](murder-design.md#6-durability-and-recovery).

- [ ] 5.3.1. Qualify a demanded native checkpoint and compaction profile.
  - [ ] Requires 3.2.2.
  - [ ] See [design §§6 and 11](murder-design.md#6-durability-and-recovery).
  - [ ] Success: a versioned local checkpoint proves exact recovery coverage
    before the host prunes its retained base/suffix. Interrupted installation,
    old-generation checkpoints, unknown rows, and vector holes preserve the
    original reader corpus. The profile remains distinct from upstream binary
    export and cannot make a snapshot writer an implicit reader dependency.
- [ ] 5.3.2. Qualify a demanded tombstone-collection policy.
  - [ ] Requires 4.3.2.
  - [ ] See [design §11](murder-design.md#11-decisions-and-deferred-scope).
  - [ ] Success: a recorded retention/frontier contract and adversarial offline
    peer traces justify every discarded record. Local age, vector maxima, and
    successful flush cannot authorize collection. Offline-peer rejoin and
    restart cannot resurrect a row solely because its tombstone was collected.
    A valid later write may recreate the row under the qualified
    last-writer-wins semantics.
    Otherwise collection remains unsupported.

[flock-docs]: https://github.com/leynos/lody-docs/tree/fce1be8092f7db763598e986fc96288fe94302c8/docs/flock-protocol
[youxia-roadmap]: https://github.com/leynos/youxia/blob/284127edaf6b78e9db7a504dc4215c7ac5ef016f/docs/roadmap.md
