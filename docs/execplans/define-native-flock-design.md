# Define the native Flock design and delivery roadmap

## Outcome and scope

Produce a technical design and Goals, Ideas, Steps, and Tasks (GIST) roadmap
for Murder, the native Rust Flock implementation. Give Youxiad an initial
read-only integration contract and preserve independent development of Murder,
Youxia, and Magitek. The user authorized this documentation PR on 2026-11-20.

The repository starts at `921785fe70b3d7e27682105f548bc86f94a0b4c8` with a
README and ignore file. This change adds design artefacts; it does not
implement a codec, create a Rust package, choose a licence, or qualify a live
deployment.

## Evidence and working method

- Documentation skills: `leynos/df12-documentation-skills` at
  `9fa3a86996d6e904413afb7a34475de0c280bbb3`, technical design, GIST roadmap,
  README, PR, commit, and Oxford spelling guidance.
- Protocol evidence: `leynos/lody-docs` at
  `fce1be8092f7db763598e986fc96288fe94302c8`, especially the Flock data model,
  replication, observations, and finite-model assumptions.
- Youxia target: main `00eb09cc95f2d7b453a2433ef6439a4a96fcc9be` plus
  proposed PR 12 at `284127edaf6b78e9db7a504dc4215c7ac5ef016f`.
- Magitek target: main `2296f8ac5d550a2f5706952fb6a641179605dfa5` plus
  proposed PR 14 at `21ba7adb81e022f2700cd5a8ab17b2147aa5936b`.

Read repository sources directly through GitHub and pinned checkouts. The
source-derived documents establish client expectations, not running-service
verification. Independent evidence and consumer-boundary reviews inform the
draft; no upstream fixture generation or protocol execution occurs in this PR.

## Outline and artefacts

1. Problem, scope, evidence baseline, and explicit compatibility gaps.
2. Native model/codec ownership and transport-neutral host integration.
3. Read-only capability, cursor durability, recovery, and later authoring.
4. Resource accounting, named validation obligations, and evidence limits.
5. Parallel workstreams, consumer handoffs, and deferred features.

Draft the replica boundary and fixture-manifest schema before the design.
Validate the schema/example and the topology diagram, then connect the design
to review-sized GIST tasks and a concise README/index.

## Acceptance criteria

- Flock, native Loro history, and Loro Durable Streams remain distinct.
- The observer requires native snapshot import and explicitly suppresses all
  upstream publication, including ordinary initial-join uploads.
- Exact retained records, replica lineage, and distinct progress positions
  govern recovery; version maxima cannot certify a complete prefix.
- Format and clock gaps have explicit qualification work rather than invented
  compatibility rules. The oracle stays outside the production runtime.
- Internal library tasks need neither Youxia nor Magitek; real consumer
  acceptance is a separate named gate with no circular release prerequisite.
- Each invariant has a discriminating witness and a stated verification limit.
- Documentation, schema, diagram, links, and roadmap dependencies validate;
  the final PR records the checks actually performed.

## Progress

- [x] Inspect repositories, pinned evidence, and current authoring guidance.
- [x] Review protocol gaps and consumer ownership independently.
- [x] Present the scoped outline and draft external contracts.
- [x] Draft the technical design, GIST roadmap, and documentation index.
- [x] Complete cross-document review and validation.
- [x] Prepare the reviewed patch and draft PR description.

## Validation record

The documentation pass checks six Markdown files with mdtablefix 0.6.1 and
Markdownlint CLI2 0.24.1 / Markdownlint 0.42.1. The configuration comes from
the pinned documentation-skills checkout. The shared Oxford spelling/phrase
gate passes in a disposable staged copy with typos-config-builder 0.1.3 and
Typos 1.50.2; no spelling configuration enters this repository.

Nixie 1.1.0 validates the topology with its native Merman 0.10.0 renderer,
before incorporation and again in the design. This checks the supported
renderer, not GitHub's browser rendering. Relative-link and heading checks
resolve 89 targets. The roadmap has five phases, 16 steps, 33 unique tasks, and
53 explicit local dependency edges, including the conditional snapshot export
prerequisite. The graph is acyclic; every task records dependencies, source,
and success. Reader/writer publication has no real-Youxia integration ancestor.
A separate source review checks the cross-repository gates and keeps
authenticated live work after Youxia's native-reader integration.

Python jsonschema 4.26.0 validates the Draft 2020-12 schema and planned
example. Eighteen manifest probes behave as expected: two positive shapes, 13
negative cases, and three deliberately schema-valid semantic counterexamples.
The last group demonstrates that path containment, real artefacts/digests, and
unique case IDs still need the future semantic validator. No fabricated
test-only record is included as protocol evidence.

Independent review corrected snapshot-free bootstrap, same-key equal-clock
conflict scope, ambiguous commit reconciliation, adapter ownership, and the
consumer task mapping. The final pass also checks whitespace with
`git diff --check` and preserves first-use footnote order.

This branch contains no Rust implementation, generated upstream corpus, or
running-service result. Native conformance, TLA+/TLC models, resource
measurements, and real Youxiad acceptance remain roadmap obligations.
