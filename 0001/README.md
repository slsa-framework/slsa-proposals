# Proposal 1: Creation of proposal process

*   Proposer: [Mark Lodato](https://github.com/MarkLodato) (lodato@google.com)
*   GitHub Issue: [#296](https://github.com/slsa-framework/slsa/issues/296)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Implementation: [#310](https://github.com/slsa-framework/slsa/pull/310)

## Abstract

<!-- Provide a very short summary of the proposal, ideally in 1-2 sentences. -->

Creates a new proposal process for SLSA, with this being the first proposal.

## Problem

<!-- Describe the problem to be solved and any necessary background. -->

There is currently no good option for proposing, refining, and getting agreement
on large changes that require some sort of design document.

*   GitHub Issues are sufficient for simple proposals, but larger changes
    sometimes can benefit from an external document that lays out the design and
    alternatives considered. Doing so within an Issue is awkward.
*   Pull Requests are sufficient for small changes that need no design, that can
    be comfortably reviewed in a single change, and that are fully implemented.
    But larger changes often need a high-level design to be reviewed first or
    are better spread across multiple PRs.
*   External documents such as Google Docs are a reasonable alternative,
    especially for early iteration and frequent commenting. However, once a
    design is relatively stable and ready for widespread review, a more
    permanent and easy-to-read option is preferred for posterity.

Furthermore, it would be valuable to have a place for contributors to write down
partially formed ideas that are not quite ready for acceptance but nevertheless
would benefit from community involvement.

## Prior art

<!-- If relevant, list any prior art or influences. -->

Other processes: [Go](https://github.com/golang/proposal),
[Kubernetes](https://github.com/kubernetes/enhancements/blob/master/keps/sig-architecture/0000-kep-process/README.md),
[Numpy](https://numpy.org/neps/nep-0000.html),
[Python](https://www.python.org/dev/peps/pep-0001/),
[TUF](https://github.com/theupdateframework/taps/blob/master/tap1.md),
[Tekton](https://github.com/tektoncd/community/tree/main/teps#tekton-enhancement-proposals-teps).

## Proposed solution

<!-- Describe the proposed change to solve the problem. -->

We set up and [document][CONTRIBUTING] the following process, modeled loosely
after [Go's](https://github.com/golang/proposal):

1.  (unchanged) Use GitHub Issues to discuss the problem and get agreement on
    the high-level solution. This step can be skipped for small changes where
    the full discussion can sufficiently happen in the PR.
2.  **(new)** For larger proposals, also use a **Proposal Document** linked to
    the GitHub Issue to fully describe the proposal and alternatives considered.
    This serves as a record of the decision.
3.  (unchanged) Use Pull Requests to agree upon the implementation.

Proposal documents all live in a single GitHub repo:
[slsa-framework/slsa-proposals](https://github.com/slsa-framework/slsa-proposals).
Having a separate repo from the main SLSA one allows us to have different access
control and also opens the possibility to publish via GitHub Pages in a
subdomain, since proposals likely shouldn't be part of the main SLSA website.
The name `slsa-proposals` was chosen to be obvious to readers and to be
unambiguous when forked/cloned.

Proposal documents only serve to get agreement on a solution. They are not
intended to serve as long-term documentation or to be updated after the solution
is implemented. In other words, there are no "informational" proposals, as there
are in Python and TUF, for example. Instead, such documentation should be stored
as either [blog posts](https://github.com/slsa-framework/slsa/issues/309)
(point-in-time articles, not intended to be updated) or maintained documentation
in the SLSA repo.

[CONTRIBUTING]: https://github.com/slsa-framework/slsa/blob/main/CONTRIBUTING.md

## Example

<!-- Describe one or more concrete examples showing how the proposal solves the
problem. -->

This proposal serves as a canonical example and template.

## Considerations

<!-- Describe any considerations or requirements that went into the proposed
solution and/or that would help decide among the alternatives considered. -->

*   The process should be lightweight and easy to follow. We don't want to add
    process without clear value.
*   The name and process should be clear without needing a lot of explanation.

## Alternatives considered

<!-- Describe alternative solutions that were considered. If possible, frame
them in light of the considerations above. -->

*   Maintain the proposal in the top comment of a GitHub Issue

    *   While this is a fine option for small changes, the UX is not as nice as
        a proper editor with real version control, especially when revising the
        proposal over multiple days.
    *   Some proposals address more than one issue.

*   Use the main SLSA repo instead of a separate one.

    *   SLSA proposals have a lower bar for quality and review. Having a
        separate repo allows us to set different controls.
    *   (minor) Could not host the proposals as a subdomain on GitHub Pages,
        since Pages only supports a single site per repo.

*   Other names for "proposal documents":

    *   Acronym, such as SEP (SLSA Enhancement Proposal) or SPICE (SLSA Process
        for Ideas, Clarifications, and Enhancements).
        *   More jargon. "Proposal 3" is obvious. "SEP 3" is not.
    *   "Design document", like Go uses.
        *   Too restrictive. Has a particular connotation, whereas proposals can
            be broader than pure "designs".
