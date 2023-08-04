# Proposal 7: Steering Commitee application, terms, and role

*   Proposer: [Joshua Lock](https://github.com/joshuagl)
    (joshuagloe@gmail.com on behalf of joshua.lock@uk.verizon.com),
    [Mark Lodato](https://github.com/MarkLodato) (lodato@google.com)
*   GitHub Issue: [#937](https://github.com/slsa-framework/slsa/issues/937)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Original link:
    [Google docs](https://docs.google.com/document/d/1DBB-ziaM0yHyL1DDw1my-qs7tgKxluK25EIYTQltcgY/edit?usp=sharing)
    (see that doc for reviewer comments)

## Abstract

This document proposes a change of SLSA governance structure to reduce
redundancy, ensure transparency, and improve recognition for invested
contributors.

## Problem statement

The original SLSA Steering Committee (SC) has passed its two year term and, per
[current governance documents][current terms], must now select the next group
of Steering Committee members. This gives us a natural point for reflection on
the role and operation of the Steering Committee. Several project contributors
have raised justifiable concerns about the lack of transparency of the
committee, the ambiguity in how responsibilities differ for the role of a
Steering Committee member vs. Maintainers, and what the relationship is with
SLSA’s parent [Supply Chain Integrity Working Group (SCI WG)][SCI WG].

### Historical context

The SLSA Steering Committee was formed during the early days of both SLSA and
the OSSF to provide a diverse set of inputs and oversight to the specification.
Since the committee's original formation, the OpenSSF has developed more
[structure][OSSF structure] and [processes][OSSF processes] which could
support the SLSA project.

## Prior art

Many governance models exist for steering or umbrella projects, i.e.; the
current [SLSA Steering Committee], [Kubernetes Steering Council],
numerous [other models] within the CNCF.

## Goals

[goals]: #goals

*   High-level vision/strategy for the SLSA project should be shaped through
    diverse inputs, including from voices outside of the SLSA project’s active
    contributors.
*   Transparency at all levels of SLSA governance, with clearly defined roles
    and responsibilities.
*   Greater recognition of existing, and future, active contributors.
*   Strong sense of ownership for active contributors.
*   Diverse affiliation of community leadership, such that a single entity
    cannot unduly influence the project.
*   Lower cost of operations for SLSA governance. The SLSA organization is
    small, so a heavyweight process is not worth the effort. In addition, SLSA
    can lean on the [established organizational support][OSSF structure] in the
    OpenSSF if needed.


## Proposed solution

Below is a summary of the proposed changes, highlighting the important
differences in **bold**. The purpose of this proposal is to get general
agreement on the idea; exact wording will be worked out in the implementation
PR. See [current governance doc] for reference, including the list of example
duties for each role.

### Replace “terms” / “seats” with a lightweight re-application process

*   SC **no longer has 7 fixed seats**, but instead has a minimum of 3 and
    ideally about 5.
    *   Same guidelines for Maintainers, without the hard minimum.
*   Individuals **self-nominate** to become a Maintainer or SC Member by
    submitting a brief (1-2 paragraph) application consisting of:
    *   **Evidence** of performing some of the role's duties over the recent
        past.
        *   For SC Member applications, this should include evidence of being
            well-versed in SLSA.
    *   **Statement of intent** to continue performing duties over the next
        year.
    *   **Affiliation** / employer.
*   **SC Members decide** if the individual meets the desirable properties
    (described in the next section) and respond with an official determination
    and rationale. Decisions are effective immediately.
    *   SC Members should attempt to reach consensus. At a minimum:
        *   SC Member applications require explicit approval from at least
            50% of the existing SC Members, excluding the candidate.
        *   Maintainer applications require explicit approval from at least 2
            existing SC members (possibly including the candidate).
*   Existing SC Members and Maintainers **annually re-apply** for the position,
    using the same lightweight process above.
    *   Purpose: to provide a natural way to remove members who are no longer
        actively performing their duties.
    *   Exception: If there are only three SC Members, they remain indefinitely
        until another person applies.
*   Applications and decisions are **recorded publicly**, such as through
    GitHub Issues, for transparency and to provide examples for future
    applicants.
*   **OpenSSF may**, at their discretion and with due process and input from
    the SCI WG:
    *   appoint new SC Members to get up to the minimum three; or
    *   remove an SC Member for cause.
* **Appeals** may be made to the SC directly, and if needed, escalated to the
    OpenSSF.

### Clarify project roles

*   Participant: **no change**, but clarify language (it’s a bit confusing as
    written)
*   Contributor: **no change**, but clarify language
    *   A Maintainer may **delegate** some duties to a Contributor, such as
        permission to triage issues or approve PRs
*   Maintainer: an individual who is **trusted** and **expected to regularly
    perform** day-to-day duties **for a specific sub-project** within the
    slsa-framework organization.
    *   A **Core Maintainer** is a maintainer of the slsa-framework/slsa repo.
        This repository is special because it contains the core specification
        and the overall project issue tracker.
    *   A **Subproject Maintainer** is a maintainer of a different repo within
        the slsa-framework organization.
*   SC Member: a **senior leader** of the SLSA project who is **trusted** and
    **expected** to ensure the health of the project overall, drive the overall
    project strategy, and serve as an escalation point in the event of a
    disagreement.
    *   These duties are much less frequent than those of a Maintainer.
    *   We **expect an SC Member to be well versed in the project**. This often
        means that SC Members are also Core Maintainers, or maintainers of a
        subproject, but this is not required.
    *   Some Core Maintainers might not be SC Members.
    *   The SC, as a group, are responsible for maintaining the relationship
        with the SCI WG, which includes:
        *   Facilitating input from SCI WG into SLSA during significant
            developments, including: adding tracks and establishing roadmaps.
        *   Periodically reporting progress and developments to SCI WG.
        *   Sharing relevant information from the SCI WG with the SLSA community.
    *   As community leaders, SC Members are also responsible for maintaining
        the slsa-framework/governance repository.
    *	SC Members share updates on their activities in the Community Meeting.

### Clarify GitHub roles and how they maps to project roles

Currently it’s unclear how the GitHub org and repo membership and roles
correspond to the roles defined in the governance document. The proposal here
attempts to clarify.

*   GitHub Organization:
    *   Member:
        *   Used for Maintainers to allow them to be added to a team.
        *   Used as needed for Participants and Contributors who would like to
            set up [custom notification routing] and issue assignment across
            multiple repos.
        *   Does not grant any special repository permissions, i.e. Base
            permissions = “No permission”
    *   Owner:
        *   Used for SC Members, OpenSSF administrators, and (as needed)
            Maintainers
    *   Teams: (all with public visibility)
        *   [SLSA Steering Committee][SLSA Steering Committee Team]
        *   Core Maintainers
        *   \<subproject\> Maintainers
        *   Other teams as needed
    *   GitHub Repositories
        *   Read: may be granted to Participants and Contributors as needed,
            usually to allow the person to set up custom notification routing
            and/or be assigned issues
        *   Triage/Write: may be granted to Contributors who have been
            delegated this responsibility by a Maintainer
            *   Write permission grants ability to approve PRs
        *   Admin: granted to every Maintainer via the \<subproject\>
            Maintainers team
    *   These permissions should be reviewed and cleaned up regularly (e.g.
        annually)

Once this proposal is accepted, the following changes will be made to the
GitHub organisation:

*   Set up Maintainers teams and add Maintainers as members if needed
*   Change the org member base permissions to “No permissions” (from “Admin”)
    and check that everyone still has necessary access
*   Purge unnecessary permissions in the org and in each repo
	*	Purging of unnecessary permissions should happen on a regular cadence. At minimum
		after each SC Member nomination cycle.

### Other clarifications

*   Add SC Member duty: "selecting Maintainers and Steering Committee Members"
*   Add SC Member duty: "representing SLSA in the OpenSSF SCI WG"
*   Remove requirement for SC members to meet "no less then [sic] once per
    quarter".
*   Add requirement to publish minutes for every SC meeting.
*   State explicitly that SLSA lives within and takes broad direction from the
    OpenSSF SCI WG.

## Alternatives considered

*   Status quo
    *   see [goals]
*   Dissolve the SLSA Steering Committee in favor of steering from the SCI WG
    *   Con: Strategy may be driven by those without deep familiarity with
        the project.
    *   Con: SLSA community may not feel an appropriate sense of ownership and
        autonomy.
*   Merge the Core Maintainer and Steering Committee Member roles
    *   Pro: simplified structure
        *   Counterpoint: the simplification does not result in too significant
            a savings
    *   Con: prevents us from having Core Maintainers who are not SC Members or
        vice versa, which may be desirable
    *   Con: technically the two roles are distinct, even if the same
        individuals have typically performed them

<!-- References -->

[current governance doc]: https://github.com/slsa-framework/governance/blob/4faa482e1040bf71df73e4a7efa0eda9eeb646a1/5._Governance.md
[current terms]: https://github.com/slsa-framework/governance/blob/4faa482e1040bf71df73e4a7efa0eda9eeb646a1/5._Governance.md?plain=1#L42
[custom notification routing]: https://github.com/settings/notifications/custom_routing
[Kubernetes Steering Council]: https://github.com/kubernetes/steering/blob/main/charter.md
[OSSF structure]: https://github.com/ossf/tac/blob/main/organizational-structure-overview.md
[OSSF processes]: https://github.com/ossf/tac/tree/main/process
[other models]: https://contribute.cncf.io/maintainers/governance/leadership-selection/
[SCI WG]: https://github.com/ossf/wg-supply-chain-integrity/
[SLSA Steering Committee]: https://github.com/slsa-framework/governance/blob/4faa482e1040bf71df73e4a7efa0eda9eeb646a1/5._Governance.md?plain=1#L17
[SLSA Steering Committee Team]: https://github.com/orgs/slsa-framework/teams/slsa-steering-committee
