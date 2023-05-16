# SLSA Proposal Documents

This repository contains "proposal documents" that supplement larger changes to
[SLSA].

**Proposals represent a snapshot in time.** They are intended to explain a
proposed change and its rationale. They are **not** intended to serve as
long-term documentation once the change is implemented. Long-term documentation
should instead live in the [main SLSA repo].

## Proposal index

Number                 | Title                                      | [Status]
---------------------- | ------------------------------------------ | -----------
[0001](0001/README.md) | Creation of proposal process               | IMPLEMENTED
[0002](0002/README.md) | Project roadmap, May 2022                  | ACCEPTED
[0003](0003/README.md) | SLSA v1.0 proposal                         | ACCEPTED
[0006](0006/README.md) | Restructure the Versioning System for SLSA | DRAFT

## How to create a proposal document

See [CONTRIBUTING] for a description of the process. Proposal documents are not
needed for all changes.

1.  Find or create a relevant [GitHub Issue] in the main SLSA repo to discuss
    this idea.
2.  Fork this repository and create a PR containing the new document:
    *   Create `####/README.md` using [0001/README.md](0001/README.md) as a
        template:
        *   `####` is the proposal number; use the next available integer.
        *   Update metadata at the top:
            *   Page title: Proposal number and title
            *   Proposer: Full name with link to GitHub username, plus email
            *   GitHub Issue: Link to GitHub Issue(s) that this proposal
                addresses
            *   Status: `DRAFT`
            *   Implementation: Point to any implementations that are ready, or
                `(none)` if none are available yet.
        *   Fill out the rest of the document as appropriate. We recommend
            following the same document structure unless you have a reason to
            deviate. Delete or comment-out unused sections.
    *   Update the [proposal index] with the new proposal.
    *   Send a pull request to submit the proposal. You may gather and
        incorporate initial feedback in the PR, but prefer to submit it once
        activity subsides rather than having a long-lived PR.
3.  As needed, iterate on the proposal with more PRs.
4.  Once agreement is reached on the Issue, send a PR to update the [status].

## Meaning of status codes

*   **DRAFT:** Still being refined and/or undergoing review. All proposals start
    in this state.
*   **ACCEPTED:** Accepted as the plan of record but not yet implemented.
*   **IMPLEMENTED:** Implemented and final. The doc should be updated to point
    to the implementation PRs in the Implementation metadata field.
*   **REJECTED:** Will not be implemented. The doc should explain the decision.
*   **ABANDONED:** Proposer has stopped work on the proposal, without an
    explicit approval or rejection decision. The doc should explain why it was
    abandoned.

[CONTRIBUTING]: https://github.com/slsa-framework/slsa/blob/main/CONTRIBUTING.md
[SLSA]: https://slsa.dev
[main SLSA repo]: https://github.com/slsa-framework/slsa
[GitHub Issue]: https://github.com/slsa-framework/slsa/issues
[status]: #meaning-of-status-codes
[proposal index]: #proposal-index
