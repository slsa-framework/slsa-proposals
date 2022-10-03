# Proposal 3: SLSA v1.0

*   Proposer: [Mark Lodato](https://github.com/MarkLodato) (lodato@google.com)
*   GitHub Issue: [#497](https://github.com/slsa-framework/slsa/issues/497)
*   Status: [ACCEPTED](../README.md#meaning-of-status-codes)
*   Original link: [Google docs](https://docs.google.com/document/d/13i2f-1SPogsuOCRDBFqQVRx0ieMBc7B-XxHsST-tx5c/r/0-PRIrYGqLqLfqzEmx2eQ5_Q/edit) (see that doc for reviewer comments and rejected ideas)

## Abstract

This document lays out the broad strokes of the SLSA v1.0 release so that the
community can understand and reach agreement on the high-level ideas. Review of
detailed wording of the specification will be left to the implementation PRs.

## Problem statement

There are several SLSA specifications needing a path forward and agreement in
order to mature SLSA enough to release it as 1.0. Without addressing the high
and low-level issues/unanswered questions here, SLSA has too much ambiguity for
implementation and sustainment.

## Document Requirements

Define SLSA specification version 1.0 so that parties can rely on a stable
release.

Requirements:

-   MUST support evolution and versioning of requirements over time, without
    breaking existing written/verbal references to the levels.
-   MUST fully define, replace, or remove the Common Requirements, which are
    still undefined after more than a year. The replacement MUST provide a
    means for consumers to gain confidence in the security of build systems and
    other trusted systems.
-   MUST ensure that all [threats](https://slsa.dev/threats) are actually
    addressed by the requirements at each level. For example, simply publishing
    provenance is not sufficient; a downstream system must also verify it to
    address the corresponding threats.

Nice-to-haves:

-   SHOULD recognize build integrity protections even if two-party review or
    other source protections are not yet implemented.
-   SHOULD address the fact that the jump from SLSA 3 to SLSA 4 is so huge
    that many (or most?) find SLSA 4 unattainable. For example, would there be
    value in defining an intermediate milestone?
-   SHOULD align SLSA to SBOM and other relevant standards to reduce
    confusion and duplication.
-   SHOULD communicate the levels in terms of expected security outcomes, not
    just a checkbox-style list of requirements.
-   SHOULD be difficult for implementers and verifiers to misinterpret the
    specification.
-   SHOULD address the major points of confusion listed as
    [issues](https://github.com/slsa-framework/slsa/issues).

Out of scope (deferred until after v1.0):

-   [out of scope] Expanding SLSA to cover other aspects of supply chain
    security, particularly vulnerability management, which is arguably the
    biggest software supply chain problem facing most organizations.
-   [out of scope] Expanding SLSA to cover transitive properties of the
    supply chain, not just the properties of an individual build step.

More detailed requirements for specific components can be found in the
corresponding sections.

## Proposed high-level changes

### Versioning

_<ins>Problem:</ins> How do we support versioning of the SLSA specification
while keeping existing references to the levels unambiguous? What if we want to
expand to vulnerability management or other areas in the future?_

**<ins>Decision:</ins>** The specification is versioned but we agree to never
change the gist of each level ("hybrid" below). Effectively, the "SLSA 1/2/3/4"
namespace is taken. It would cause too much confusion to change the meanings
significantly, and requiring everyone to always specify a revision number is
impractical. We can still make minor revisions to the levels, so long as we do
not believe it would cause too much confusion. When precision is not critical,
unversioned references are OK, e.g. "SLSA 3." When precision is required,
references MUST include a revision number, preferably using the syntax "SLSA 3
(v1.0)," and ideally backed by [evidence of security claims].

If we want to expand to other areas of supply chain security, such as
vulnerability management, we either:

-   Limit ourselves to properties that align with the existing levels. For
    example, for vulnerability management we could limit SLSA to just
    generating a high quality SBOM. Other aspects of security that don't fall
    neatly into levels may be a better fit for
    [Scorecards](https://github.com/ossf/scorecard).
-   Use a different namespace for the new levels, e.g. "SLSA Correctness L3"
    or "SLSA Gold". If we stick with 1/2/3, we would probably rename "SLSA 3"
    to "SLSA Core L3" or similar.

<ins>Background:</ins> The SLSA levels will likely need further revision over
time. For example, we may wish to add or remove a requirement for an existing
level, add a new level, or expand SLSA to cover vulnerability management or
other aspects of supply chain security. Meanwhile, there will be an ever growing
number of informal references to old versions of the specification. For example,
a build system may claim "SLSA 3" compatibility at one time, but this claim
could become invalid if we add a new requirement to "SLSA 3". Such invalidation
is undesirable because it creates confusion and dilutes the meaning of SLSA. It
even makes having conversations difficult because both parties may not agree on
what "SLSA 3" means, and they may not even realize that they don't agree.

<ins>Ideas considered:</ins>

-   **Versioned and mutable:** Levels are versioned, and the meaning of each
    level can change significantly between versions. Require everyone to always
    include the version number in the level reference, such as "SLSA 3 (rev
    1.0)", "SLSA 3 (2022)", or "[SLSA 3](https://slsa.dev/spec/v0.1)" [with
    link to spec].

    -   Good: Allows for major changes to the specification, so that we
        do not need 100% confidence in the design before releasing v1.0
    -   Good: Allows for minor iterations of the specification
    -   OK: References are completely unambiguous when used correctly,
        but completely ambiguous if used incorrectly (no version number)
    -   Bad: Unlikely to work in practice. Only a fraction of users will
        remember to include the version, especially during informal conversations.

-   **Hybrid (chosen):** Levels are versioned, but the gist of each level
    never changes. Users can either accept ambiguity by referring to an
    unversioned level, e.g. "SLSA 3", or avoid ambiguity by including a version
    number, e.g. "SLSA 3 (rev 1.0)" and/or listing [evidence of security
    claims]. If we need to make significant changes to
    the meaning of levels, we use a different namespace, e.g. "SLSA
    Bronze/Silver/Gold" or "SLSA Foo L1".

    -   Bad: Does not allow for many major changes to the specification
        because namespaces are limited, so we need high confidence in gist of
        each level before releasing v1.0
    -   Good: Allows for minor iterations of the specification
    -   OK: Some ambiguity will still occur, though it can be avoided if desired
    -   OK: Likely to work reasonably well in practice

-   **Immutable:** Levels are unversioned and immutable. If we want to make
    any change after v1.0, we use a different namespace, e.g. "SLSA
    Bronze/Silver/Gold"

    -   Bad: Does not allow for many major changes to the specification
        because namespaces are limited, so we need high confidence in gist of
        each level before releasing v1.0
    -   Bad: Does not allow for minor iterations of the specification;
        each one is a major change, thus limiting the rate of possible minor changes.
    -   Good: Completely unambiguous
    -   Good: Will definitely work in practice

<ins>Ideas for revision specifiers:</ins>

-   SLSA 3 (rev 1.0)
-   **SLSA 3 (v1.0)** — this was the overwhelming preference at the
    specification meeting
-   SLSA 3 (2022)
-   SLSA 2022, L3
-   SLSA ‘22, L3
-   SLSA Avocado L3 [each revision has a unique name, A＿, B＿, C＿, etc.]

<ins>Out of scope:</ins> Frequency of versioning updates and specifications of
minor or major versions.

### Evidence of build service security claims

_<ins>Problem:</ins> The Common Requirements in v0.1 are still undefined. How do
we gain confidence that the trusted services are deserving of our trust?_

<ins>Background:</ins> The Common Requirements in SLSA v0.1 were intended to set
a security baseline to give confidence that the other requirements—e.g.,
isolated, ephemeral environment, and hermetic build requirements—are being met.
However, it is almost impossible to come up with a concrete set of requirements
that is both (a) sufficient, i.e., if followed, will in fact ensure a strong
security posture and not just ticking a checkbox; and (b) general enough to
accommodate a variety of reasonable architectures.

<ins>Proposal:</ins> Shift the focus to operational guidelines (not
requirements) plus evidence of the security claims made by the builder. More
specifically:

1.  Clarify that there is no centralized authority to attest to a universal
    SLSA rating. Instead, each downstream party makes its own decisions on how
    much to trust each build service based on the documentation and evidence
    provided. This also leaves room for accreditation and transfer of trust,
    e.g. "I'll trust whatever Company X trusts."

2.  Convert the common requirements to _guidelines_ that establish the
    expected security guarantees made by a SLSA 3+ builder. These are not hard
    requirements. This would include things like:

    -   Two-party review on all administrative operations. (SUSE found
        this valuable.)
    -   Reference to other standards that establish security baselines.
    -   Particular threats that most consumers would like to see addressed.
    -   SAST, DAST, SCA scans (bare minimum)

3.  Add a requirement that the build service provides evidence of security
    claims, in the form of a targeted survey with a recommended template
    provided by SLSA. The build service is expected to provide downstream
    parties with an explanation of how its design, implementation, and
    operational processes satisfy SLSA requirements and uphold key security
    invariants. This explanation should be sufficiently detailed for the
    downstream party to be convinced of the build service's claims. Here the
    "downstream party" is whoever uses the builder's provenance to make
    security decisions. This could be org-internal, or an external customer, or
    public.

    The SLSA specification sets expectations for what such a document should
    include, such as:

    -   Explanation of how the system meets the various SLSA requirements, e.g.
        Isolated.
    -   A full enumeration of all trusted components of the build service, its
        trusted dependencies (like key/secret stores), and the platform the
        build service runs on (i.e., all components that if misbehaving or
        compromised could allow an invalid provenance to be issued).
    -   A detailed explanation how trusted components are isolated/defended from
        interference by non-trusted components.
    -   A full assessment of the paths by which someone can override security
        guarantees described in the document, including number of people who
        have access and what approvals they must go through, for all trusted
        components. Examples: access to provenance signing keys; physical access
        to machines; ability to tamper with the storage layer; administrator
        access over the IAM system.

This model may be extended to source control systems and other trusted entities
as needed.

### Postpone definition of SLSA 4

_<ins>Problem:</ins> SLSA 4 (two-person reviewed + hermetic + optionally
reproducible) generates a lot of confusion, questions, and difficulty for
implementers. We need to redefine SLSA 4 to address these issues, but doing so
will take a significant amount of time._

<ins>Proposal:</ins>

-   Do not define SLSA 4 in v1.0. (Only define SLSA 1-3, with a footnote
    about SLSA 4.)
    -   Benefit: unblocks the release of v1.0.
    -   Benefit: avoids locking us into the definition of SLSA 4 (see
        [Versioning]).
-   List prospective requirements for future levels—including two-party
    reviewed, hermetic, and reproducible—with a note that these are subject to
    change in a future revision.
    -   Benefit: gives readers an indication of what's to come.
    -   Benefit: provides a common language for other properties that
        don't fit neatly into levels yet.
    -   Note: We will likely need to revise these three definitions,
        since they generate a lot of confusion and disagreement today. For
        example, for Hermetic, we may want to decompose into Pinned (list of
        all build-time transitive dependencies is checked in to version
        control, including a cryptographic hash of each dependency that is
        verified before building) and Dependencies complete (builder guarantees
        that controls were in place to prevent use of build-time dependencies
        not listed in the provenance.)
-   List tentative plans for SLSA 4 somewhere so people can know how to take
    the next step, with clear messaging that it's not formalized yet.
-   Define SLSA 4 in a future release.

### Separate "build" levels vs "source" levels

_<ins>Problem:</ins> The source requirements of SLSA 3 (retained + verified
history) don't fit well with the other requirements. The rest are about build
integrity and can be achieved purely by adopting an appropriate builder, while
the two source requirements are about source availability and require changes to
the source control system. Therefore, it is worth considering splitting these
out to allow build integrity to be achieved independently._

<ins>Proposal:</ins>

1.  Rename the current levels to "Build" levels and remove the source
    requirements. For example, "SLSA 3" becomes "SLSA Build L3."
    -   Build levels are only about the trustworthiness of the build
        process and the resulting provenance.
        -   Note: The builder still _identifies_ the source (via the
            provenance), but it need not make claims _about_ the source.
        -   Note: The level makes no claims about what processes were
            followed, such as linting, vulnerability scans, or software
            composition analysis.
        -   The docs need to make it very clear to readers what this
            does and does not provide, e.g. it is not a general claim that a
            package is "safe" or "secure."
    -   Only meaningful for built artifacts (called "packages" in our
        [model](https://slsa.dev/terminology)). Source artifacts trivially meet
        the highest Build level.
    -   Naming ideas (to be decided during broader public proposal):
        -   Build Level [Mark, Emmy, Marcela]
        -   Build Integrity Level [Mark, Marcela]
        -   Provenance Level [Michael]
        -   Core Level
2.  In a future version, add a set of "Source" levels that cover properties
    of a source.
    -   This can include the existing source requirements, though likely
        with modifications to make them more practical. We may also want to add
        other supply chain security properties, such as
        [commit signing](https://github.com/slsa-framework/slsa-proposals/pull/5#issuecomment-1209489154).
    -   Only meaningful for source artifacts. (TBD whether one can say a
        package has a source level.)
    -   Include this in some "draft" form in v1.0 to give an indication
        that it is coming down the road.
    -   Naming ideas (to be decided later):
        -   Source Level (may be misleading)
        -   Source Integrity Level
3.  In the docs, caution against use of a bare "SLSA 3" because it is ambiguous.
    -   OK for sources, e.g. git commits, because only Source levels apply.
    -   OK for builders because only Build levels apply. For example,
        "SLSA 3 builder" is OK, though "SLSA 3 compatible builder" would be better.
    -   OK for builds, e.g. "This package uses a SLSA 3 build," because
        only Build levels apply.
    -   Discouraged for packages because it's unclear whether this
        includes Source levels, and if so, which sources those levels apply to.
        Please use more precise terminology instead.
    -   Note: This is a mini version of the "transitive SLSA" question.
        Perhaps we can solve them both at the same time.
    -   Alternatives considered:
        -   "SLSA 3" means "Build L3 for the package + Source L3 for
            all of the package's sources and build configs"?
            -   Pros: convenient, what people may end up doing in practice
            -   Cons: unclear that this was the intended usage,
                unclear what sources this referred to, might rarely come up in
                practice (i.e. Build Level == Source Level)

<ins>Benefits:</ins>

-   Decouples build and source integrity, allowing a package to recognize
    progress in each area independently.
    -   In practice this was being done anyway, e.g. the GitHub Actions
        "SLSA 3 builder", which is not technically correct in v0.1.
    -   However, we want to make it clear that a Source level does not
        make sense for a package unless there is a sufficient Build level.
-   Makes SLSA 3 actually practical, since there are no public source control
    systems available today that satisfy the SLSA 3 source requirements. (There
    are build systems that satisfy SLSA 3 build requirements.)
-   Naturally allows us to extend SLSA to other areas of supply chain
    security by creating a third ladder. (Though this may create extra complexity.)
-   Gives us an out to redefine the levels entirely by creating a new name,
    should we need one. (See [Versioning].)

<ins>Drawbacks:</ins>

-   Added complexity and confusion of having two different leveling systems.

<ins>Alternatives considered:</ins>

-   Remove source requirements from the levels and instead describe them as
    additional properties independent of the level. For example, one could
    claim "SLSA 3 + two-party review".
    -   Worry about exploding complexity.
    -   Might end up needing some sort of bundling or levels anyway.

-   Remove source requirements from SLSA forever. Instead, recommend an
    approach such as Scorecards to record these properties. This might work for
    other supply chain security aspects that do not naturally have levels.
    -   Undesirable. Would like SLSA to cover more aspects of supply
        chain security, not less.

### Policy verification

_<ins>Problem:</ins> How do we introduce the concept of policy verification into
the specification?_

<ins>Background:</ins> Provenance alone does not achieve any of the security
pillar objectives or address any of the [threats](slsa.dev/threats); it is some
downstream system acting upon that provenance that provides value. For example,
suppose an adversary uploads a new release that is fully SLSA 4 with provenance,
but built from an unofficial source repository. The only way to address this
risk is to have a policy verification downstream that only accepts the official
source repository, whatever that means. This was a critical concept of the
inspiration for SLSA
([BAB](https://cloud.google.com/docs/security/binary-authorization-for-borg))
but was omitted in v0.1 to defer complexity. This needs to be addressed in v1.0.

<ins>Proposal:</ins> Will be decided during implementation.

## Proposed low-level changes

### Clarifications

Review issues with
[clarification](https://github.com/slsa-framework/slsa/labels/clarification) and
[spec-change](https://github.com/slsa-framework/slsa/labels/spec-change) labels,
tag all potential issues to the [v1.0
milestone](https://github.com/slsa-framework/slsa/milestone/2).

### Model

These changes are summarizing what will go into the draft. They have not
necessarily been agreed upon.

-   Dependency:
    -   Separate into "included" (e.g. statically linked), "build time"
        (but not included), "runtime" (but not build time). Say that SLSA is
        only concerned about included and build-time, but not run-time. Update
        provenance spec to make this distinction.
-   Source:
    -   Define as the "main" input.
    -   Add additional "build config" input.
-   Package:
    -   Flesh out model for packages, needed for policy work.
    -   Clarify that "source package" is still a package, where the
        "build" is transforming a source revision into an archive.
-   Provenance:
    -   Rename to SBOM, along with clear guidance on what's required.
        Work with SBOM community to get required formats added.
-   Distribution / consumption model
    -   Git -> consumer
    -   Git -> source tarball -> consumer
    -   Git -> binary -> consumer
    -   Git -> source tarball -> binary -> consumer
    -   Git -> internal mirror -> consumer [kind of orthogonal above]

## Parallel efforts, related to but not blocking v1.0

The following changes are desired around the v1.0 time frame, but it is not yet
clear if these are blockers for the v1.0 release.

### Revamp "get started" page

Rewrite the [Get Started](https://slsa.dev/get-started) flow to be more focused
on user journeys, possibly split between open- and closed-source:

-   For software producers: How to use various source and build systems to
    achieve SLSA for their project
-   For software consumers: How to use SLSA to achieve security for the
    software you consume
-   For system implementers: Guidance on how to implement SLSA-aware systems
    -   Source systems (e.g. GitHub)
    -   Build systems (e.g. GitHub Actions)
    -   Packaging ecosystems (e.g. PyPI)
    -   Execution systems (e.g. Kubernetes)

### Provenance format

We need a stable v1.0 release of the recommended SLSA provenance format, but
that will be done as a separate, parallel effort.

Problems:

-   Very unclear how the Provenance format maps to requirements.
    -   Need to explain that level is determined by builder, except for
        "materials complete" (hermetic) and parameterless
    -   Use the same names
-   Unclear that Provenance and Build both apply to build. Provenance
    Requirements actually could apply to other types of attestations as well.
    -   Differentiate between properties of the builder vs developer.
        -   SLSA 3 is service oriented
        -   SLSA 4 is developer oriented
    -   In general, figure out how better to present this
-   nit: Requirements table should present more as a pyramid?
    -   Maybe provenance at the top?
-   nit: Parameterless vs Config-as-code is confusing
    -   Idea from Laurent: all parameters look up into table
-   nit: isolated and ephemeral environment go hand-in-hand
-   nit: build-as-code is unclear

## Deferred changes

While we recognize the value in the following potential changes, we have decided
to defer them until after v1.0:

-   Definition of the source levels
-   Expansion to vulnerability management and other security properties
    -   This is too big of a bite to chew for v1.0. For v1.0, we just need
        confidence that the [Versioning] can support such future changes.
-   Transitive SLSA level, i.e. properties of the whole supply chain, not just
    an individual step
-   SLSA for services, i.e.g how one represents the aggregate SLSA level for a
    service or environment composed of multiple pieces of software

## Implementation plan

See [#497](https://github.com/slsa-framework/slsa/issues/497), which describes
the process and latest status.

<!-- Link definitions -->

[evidence of security claims]: #evidence-of-build-service-security-claims
[versioning]: #versioning
