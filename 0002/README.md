# Proposal 2: Project roadmap, May 2022

*   Proposer: [Mark Lodato][@MarkLodato] (lodato@google.com)
*   GitHub Issue: [#384](https://github.com/slsa-framework/slsa/issues/384)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Implementation: TODO

## Abstract

This document proposes long-term themes and short-term milestones for the SLSA
project.

> NOTE TO READER: If you know of committed projects that should be added to this
> roadmap, please send a pull request or file an issue in this repo.

## Implementation

Once this proposal is accepted, we will track progress through GitHub Projects
or Milestones.

## Themes (long-term)

The following open-ended themes encapsulate the main objectives of SLSA. They
are expected to remain stable long-term.

-   **Specification** is stable, practical, and useful for reducing risk—with a
    healthy surrounding community.
-   **Tooling**, services, and documentation make SLSA readily adoptable.
-   **Adoption** of SLSA by open-source projects reduces security risk for
    users.
-   **Government and industry** widely accept SLSA as the *lingua franca* of
    supply chain security.

## Milestones (short-term)

The following milestones are expected to be practical in the next few quarters.
Each item or sub-item has a priority and a main point of contact.

Priorities are to be interpreted as follows:

-   P0: High confidence it will happen mid-2022
-   P1: Expected mid-2022 but may slip
-   P2: Planned for 2022 but may slip or get de-prioritized
-   P3: Side project, moderate risk of de-prioritization

### Theme: Specification and community

#### Milestone: SLSA specification has a stable 1.0 release ([@MarkLodato], P1)

Version 1.0 of the [SLSA specification](https://slsa.dev/spec) provides a stable
foundation. Plans:

-   Pare SLSA v1.0 down to only cover build integrity, which is SLSA's core
    strength. This makes SLSA practical by removing the requirements that cannot
    be implemented today, notably the controversial two-party review requirement
    and the undefined "common" requirements.

-   Establish a convention for extending SLSA beyond build integrity, such as to
    vulnerability management or source integrity. This might be through multiple
    independent ladders, or a single ladder, or something else entirely. Note:
    we will validate the framework through at least one draft extension, but
    finalizing the first extension is not required for v1.0.

-   Incorporate the concept of policy or verification into the specification,
    which is necessary to realize the security guarantees of the levels.

    Policy here means something for which reviewable evidence that can be
    automatically mirrored is provided for any human to review.
    Verification, which we favor, means one which anyone can run a machine
    that will do that by itself (e.g. we'll make verified reproducible builds
    required in the highest level).
    We will try to avoid it but some policy might be indirect, meaning a
    requirement on the policy for contribution to the source.
    Only in lower levels will these categories be relaxed by trading in
    verifiability in favor of easy security gains.

-   Address known clarity issues from v0.1, including improved terminology
    ([#306](https://github.com/slsa-framework/slsa/issues/306)), platform vs.
    project requirements
    ([#188](https://github.com/slsa-framework/slsa/issues/188)), and others.

#### Milestone: Recommended SLSA provenance format has stable 1.0 release ([@MarkLodato], P2)

This includes all three layers in the suite: [DSSE], [attestation], and
[provenance]. DSSE is already 1.0, but attestation and provenance are not.
Requirements for v1.0 are still TBD.

[attestation]: https://github.com/in-toto/attestation
[dsse]: https://github.com/secure-systems-lab/dsse
[provenance]: https://slsa.dev/provenance

#### Milestone: SLSA community is healthy according to some agreed-upon definition ([@hepwori], P2)

The SLSA project has an agreed-upon definition of community health, with regular
measurements and a plan to improve it over time. This likely includes the number
of contributors and the lack of barriers for new contributions. Ideally there
should be a clear path for potential contributors to jump in and start helping
with SLSA.

### Theme: Tooling

The long-term goal is to make it practical for any software producer to adopt
SLSA. This means that the producer can build in a SLSA-compliant manner using
commonly available tools, that provenance gets distributed in a standard way,
and that the SLSA properties are verified automatically before the software is
delivered to the consumer.

In the short term, we focus on open-source packaging ecosystems, tentatively
starting with Python.[^python-buy-in] Open-source is a more tractable problem
than closed-source and serves as a good model by its open nature. We expect
equivalent solutions for closed-source in the future.

#### Milestone: SLSA 2-3 builds are possible for arbitrary open-source packages using several common build services

Producers of arbitrary open-source software artifacts have practical tools and
instructions to reach SLSA 2 or 3 using several different, commonly available
build services. The goal is to make it possible to reach SLSA 2 or 3 without
requiring major changes to existing build services, even if the user experience
is not smooth enough for broad adoption. This may involve building a layer on
top of an existing service that adds SLSA features. With this milestone,
provenance is only generated without a standard way to distribute it.

We plan to publish [Sigstore]-based solutions for build services that already
provide OpenID Connect (OIDC) tokens. Sigstore is not required for SLSA, but it
is attractive because it seamlessly integrates with OIDC. The benefit of this
approach is that it allows us to add SLSA without requiring upstream changes to
the build service, at the cost of increasing the size of the trusted computing
base and possibly worse user experience than if the builder had provided SLSA
directly. This allows motivated software producers to achieve SLSA immediately.
Planned Sigstore-based solutions include:

-   [GitHub Actions](https://github.com/features/actions) -
    [slsa-github-generator](https://github.com/slsa-framework/slsa-github-generator)
    ([@laurentsimon], P0)
-   [GitLab CI/CD](https://about.gitlab.com/topics/ci-cd/) -
    [issues/28940](https://gitlab.com/gitlab-org/gitlab-runner/-/issues/28940)
    ([@bobcallaway], [@samwhite-gl], P2)

Also, we will provide clear documentation on [slsa.dev](https://slsa.dev) to
guide software producers on how to use these solutions. ([@hepwori], P2)

[sigstore]: https://sigstore.dev

#### Milestone: SLSA 4 builds are possible for specific packaging ecosystems

Producers of specific ecosystem packages have practical, ecosystem-specific
tools and instructions to reach SLSA 4. Similar to above, the goal is to make
this possible but might not be smooth enough for broad adoption. A per-ecosystem
solution is likely necessary for several reasons: to achieve hermeticity for
SLSA 4, to generate a quality SBOM, to provide a better UX, and to make it
possible to rebuild on other platforms.

Work includes:

*   Proof-of-concept SLSA extensions to the Sigstore-based solutions above to
    reach SLSA 4 for specific languages (go, Python, etc.) These will hopefully
    prove ideas and provide more data, but we do not expect to support these
    long term. ([@laurentsimon], P1)
*   Proposal for how to generalize and scale ecosystem-specific solutions to
    achieve SLSA 4 and generate high quality SBOMs that can be maintained by
    each community independently, without risk of accidentally breaking SLSA
    guarantees. ([@laurentsimon], P2)

#### Milestone: Provenance distribution model is decided, with at least one representative implementation (tentatively Python)

Consumers have a standard mechanism for finding and retrieving provenance of the
packages they use within each packaging ecosystem. The tentative plan is to
distribute provenance in-band next to the artifact, plus have an out-of-band
mechanism for prototyping and supporting attestations from parties other than
the producer. This work also includes aligning with SBOM, since the requirements
are very similar. With this milestone, motivated consumers can act upon the
provenance themselves.

*   Q2: Design complete and approved within SLSA community ([@lumjjb], P1)
*   Q3: Prototype implementation without requiring changes to PyPI
*   Q4+: Python Enhancement Proposal (PEP) process, followed by implementation
    if needed

Issue: [#269](https://github.com/slsa-framework/slsa/issues/269)

#### Milestone: SLSA policy model is decided, to detect or prevent publishing of packages containing unauthorized changes, with at least one representative implementation (tentatively Python)

SLSA policies detect or prevent unauthorized changes for packages within the
ecosystem. When opted-in, package maintainers must upload provenance alongside
the package proving that the package was built directly from the canonical
source repository. At first this may just display results on a dashboard,
similar to [pyreadiness.org](https://pyreadiness.org/3.10/), but will hopefully
be integrated directly into PyPI eventually. With this milestone, software
producers can opt-in to automatically protect themselves against insider risk,
and software consumers can gain assurance for opted-in packages, but the package
registry is still trusted.

Design is P1 for [@var-sdk] and [@wietse-gmail] in Q2.

Sub-problems:

-   Policy model, which securely maps package name to canonical source
    repository. For example, [requests](https://pypi.org/project/requests/) maps
    to https://github.com/psf/requests. This metadata already exists for some
    packages, but (a) it is not structured and (b) package maintainers can
    mutate it at will. The solution must protect against malicious changes while
    silently allowing official changes in source repository. One idea is to have
    a Trust On First Use (TOFU) model with a mechanism for source repo
    migrations.

-   Implementation for each specific ecosystem, starting with Python/PyPI. This
    includes a design, prototype, dashboard, Python community outreach, PEP
    authoring and approval, and/or upload-time integration.

-   (optional) Toy implementation of a
    [mock package registry](https://github.com/jku/repository-playground) to
    show how SLSA works in practice, without having to change any existing
    system or to release any real package. This can also be used as examples for
    https://slsa.dev/threats. (P3)

#### Milestone: Client-side verification of SLSA policies in the Python ecosystem removes the need to trust the package registry (P3)

SLSA verification happens in client tools that fetch Python packages, reducing
or removing the need to trust the package registry (e.g. PyPI). This has a
substantial reliability risk, so it is expected to come much later. Even in this
model, the package registry enforces SLSA at upload time to prevent a maintainer
from accidentally releasing a version that no one can use.

This probably won't happen in 2022, but we list it here for completeness.

### Theme: Adoption

Widescale adoption of SLSA across open-source is the long-term goal, but it is
not practical in 2022 because it not only depends on the work above, but also
work to minimize or eliminate any friction for project maintainers.

#### Milestone: Early adoption by key projects raises awareness of SLSA (P2)

In the short term, we hope to have targeted adoption of key open-source projects
to both exemplify standards and to reduce risk for those projects. We can also
publish adoption stories to inform readers where SLSA is being applied. Finally,
we can reach out to open-source communities, working groups, conferences, etc.
to raise awareness of SLSA.

### Theme: Government and industry

#### Milestone: SLSA's relationship to government standards is clear

The community and readers understand how SLSA fits into various government
standards, such as SSDF, SBOM, etc. Exactly which government standards is TBD.

-   Relationship between SLSA and SBOM decided and clearly documented, including
    future plans for tooling. ([@lumjjb], P0)
-   Alignment between SLSA and SSDF is clearly documented through a compelling
    story of synergy. ([@hepwori], P2)

## Footnotes

[^python-buy-in]: We plan to reach out to the Python community to get buy-in and
    go through the official Python Enhancement Proposal (PEP)
    process. At the time of writing (May 2022), this has not yet
    started so any reference to Python or PyPI in this document
    should be considered a placeholder in case we choose a
    different ecosystem down the road.

<!-- Link definitions -->

[@MarkLodato]: https://github.com/MarkLodato
[@bobcallaway]: https://github.com/bobcallaway
[@hepwori]: https://github.com/hepwori
[@laurentsimon]: https://github.com/laurentsimon
[@lumjjb]: https://github.com/lumjjb
[@samwhite-gl]: https://github.com/samwhite-gl
[@var-sdk]: https://github.com/var-sdk
[@wietse-gmail]: https://github.com/wietse-gmail
