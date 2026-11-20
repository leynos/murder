# Murder documentation

These documents propose a native Flock library and its integration with
Youxiad. They record future implementation and validation work; they do not
claim a working codec or deployed compatibility.

| Document                                                                | Purpose                                                                                                        |
| ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| [Technical design](murder-design.md)                                    | Evidence baseline, native architecture, semantics, recovery, resource bounds, and named validation obligations |
| [GIST roadmap](roadmap.md)                                              | Goals, Ideas, Steps, and Tasks with dependencies and acceptance criteria                                       |
| [Replica and host boundary](contracts/replica-boundary.md)              | Proposed semantic contract for the initial reader, recoverable observer, and later writer                      |
| [Fixture manifest schema](contracts/fixture-manifest.schema.json)       | Structure for pinned corpus provenance and hashed artefact records                                             |
| [Planned manifest example](contracts/fixture-manifest.example.json)     | Baseline pins with no fixture bytes or claimed results                                                         |
| [Documentation execution plan](execplans/define-native-flock-design.md) | Scope, evidence revisions, review decisions, and checks for this documentation change                          |

_Table 1: Murder design and delivery documents._

The design's [references](murder-design.md#references) pin `leynos/lody-docs`,
the proposed Youxia and Magitek integration changes, and the
`df12-documentation-skills` guidance used to prepare this set.
