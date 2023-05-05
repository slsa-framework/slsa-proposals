# Proposal 4: SLSA Conformance Program
* Proposers: [Kris K](https://github.com/kpk47) (kkris@google.com), [Joshua Mulliken](https://github.com/JoshuaMulliken) (jmullike@redhat.com)
* GitHub issue: https://github.com/slsa-framework/slsa/issues/515
* Status: [DRAFT](../README.md#meaning-of-status-codes)

## Abstract

This document describes a mechanism for labeling build platforms as officially
SLSA conformant, the "SLSA Conformance Program." This proposal lays out the
program at a high level, including a draft of the survey that build platforms would
need to fill out to participate. Please focus review on the program as a whole
and the survey's general contents, reserving discussion of the survey's final
wording for implementation PRs.

## Problem statement
[guiding principles]: https://slsa.dev/spec/v1.0/principles#trust-platforms-verify-artifacts
[verifying build platforms]: https://slsa.dev/spec/v1.0/verifying-systems
[verifying artifacts]: https://slsa.dev/spec/v1.0/verifying-artifacts

[provenance generation]: https://slsa.dev/spec/v1.0/requirements#provenance-generation
[isolation strength]: https://slsa.dev/spec/v1.0/requirements#isolation-strength

One of SLSA's [guiding principles] is to "Trust platforms, verify artifacts." The
SLSA v1.0 specification reflects this principle's asymmetry through how it treats
[verifying build platforms] and [verifying artifacts] – the former is a
list of freeform questions a verifier might wish to ask of a build system, and
the latter is a detailed procedure to be implemented in a tool. It's easy enough
to determine whether a build platform is meeting the [provenance generation]
requirements (e.g. whether it is producing provenance and whether it is signed or
not), but it is difficult and expensive to determine whether a build platform
meets the [isolation strength] requirements.

While OpenSSF controls SLSA and could therefore be in a position to
determine which build platforms are SLSA conformant, it
doesn't have the desire or resources to do so. Furthermore, the SLSA
specification makes it the verifier's responsibility to determine whether a
build system is trustworthy, and that trustworthiness determines Build L3
conformance. Instead, OpenSSF should do what it does best: build a
community. In this case, that community would consist of build platforms,
verifiers, and independent auditors who hold each other accountable for build
platform security.   

OpenSSF's tool for building this community is a SLSA Conformance Program.
This document presents high-level objectives for the SLSA Conformance Program
and a design for the program based loosely on the CNCF's Certified Kubernetes
Conformance Program.

## Requirements

Required:

1. Set a baseline for Build L3 build platforms
2. Bootstrap an ecosystem/market of build platforms and auditors
3. Create a sensible process for managing SLSA trademarks
4. Limit or mitigate perverse incentives for auditors
5. Scale from new market entrants to existing large providers

Nice to have:

1. Distribute provenance verification materials (e.g. public cryptographic keys,
sigstore metadata)

Out of scope

1. Create a checklist definition of a "secure build platform"

## Prior art

CNCF's Certified Kubernetes Software Conformance -
[https://www.cncf.io/certification/software-conformance/](https://www.cncf.io/certification/software-conformance/)

## Proposed changes

### Overview

The OpenSSF maintains a SLSA Conformance badge and registry that contains build
platforms and auditors that take part in the SLSA Conformance Program.
Participating in the program allows build platforms to use the SLSA Conformance
badge when promoting themselves. Any build platform or auditor is free to
participate in the SLSA Conformance Program provided they are willing to meet its
requirements and follow its terms.  

The SLSA community provides a security questionnaire for build platforms. This
questionnaire is the minimum standard for evidence of SLSA conformance.
Verifiers and auditors are encouraged to seek evidence beyond the questionnaire
when making their trust determinations. For a draft of the questionnaire, see
Appendix A.

### Build platform participation

There are two tiers for a build platform to take part in the program:
self-attestation and third-party audit. Self-attestation is a weaker evidence of
conformance than a third-party audit, and is most useful while bootstrapping the
conformance program before auditors are available.

#### Tier one: Self-attestation

The self-attestation tier requires that a build platform:

-  Publish answers to the questionnaire on a domain registered to their
    organization. They may additionally publish further evidence of SLSA
    conformance (e.g. architecture documentation). The questionnaire answers must
    be updated at least once every twelve months and any time there is an
    architectural change to the build platform. If there are no architectural
    changes to the build platform between updates, then build platforms can attest
    to that fact by resubmitting the same answers with the date changed.
-  Publish any materials needed to verify provenance signed by the build
    system on a domain registered to their organization (e.g. public keys).
-  Publish vulnerability disclosures for all security incidents that
    potentially impact the integrity of the build platform. These disclosures
    must be displayed prominently on the build platform's website and released no
    more than thirty (30) days after resolution.
-  Sign and follow the SLSA Conformance Program terms.

The OpenSSF operates this tier through a community-maintained repository of
build platforms. The maintainers of this repository will be drawn from members
of the SLSA community who are spread across the industry.  
In order to participate in this tier, build platforms should 

1. Learn about the SLSA Specification and its requirements.
2. Create a PR for the repository containing
    1. Name of the build platform
    2. Build ID
    3. Desired SLSA level
    4. List of URIs that resolve to evidence of SLSA conformance
    5. List of URIs that resolve to materials for verifying provenance (e.g.
    public keys)
    6. List of URIs that resolve to the build platform's public
        vulnerability disclosures

3. Complete the terms and conditions form.
4. Send the PR for review.

The community will review the PR and merge it when a quorum of maintainers
approve it. When reviewing the PR, the community will assume that the
information in the evidence of conformance is truthful, and they will approve it if
the described build platform meets the requirements for the desired SLSA levels.

Once the PR is merged and the terms are signed, the OpenSSF grants the build
platform a limited license to use the SLSA logo on its website and other
promotional materials. Should the build platform fail to adhere to the program's
terms or meet its requirements, the OpenSSF will revoke that license and, if
necessary, enforce its trademarks. 

#### Tier two: Third-party audit

### Auditor participation

Auditors can take part in the program by:

1. Registering their intent with the OpenSSF.
2. Performing any training and/or certification required by the OpenSSF (TBD)
3. Completing the terms and conditions form.
4. Opening a PR against the auditor repository containing 
    1. Name of the auditor
    2. Auditor's website
    3. A URI that resolves to the auditor's auditing procedure. 

The community will review the PR and merge it once it has been approved by a
quorum of maintainers. It is not yet clear what, if any, criteria will be used
to evaluate auditors.   

Once the PR is merged and the terms and signed, the OpenSSF grants the auditor a
license to use the SLSA logo on its website and promotional materials. The
OpenSSF reserves the right to revoke the license if the auditor fails to
publicize audit reports,  performs insufficiently thorough audits, or otherwise
acts against the SLSA Community's interests.  

### Build platform participation

The third-party audit tier requires that a build platform:

-  Provide the auditor access to the build system and any supporting
    materials needed for the auditor to gain trust in the build system.
-  Accept that the audit report will be made public regardless of the
    audit's outcome. Audit reports may be delayed up to 90 days to respond to
    vulnerabilities in the build platform, but they must be published within 30
    days of patching the vulnerability.
-  Provide verifiers with any materials needed to verify provenance signed
    by the build system (e.g. public keys). Publishing these materials on a domain
    registered to the build system meets this requirement.
-  Publish vulnerability disclosures for all security incidents that
    potentially impact the integrity of the build platform. These disclosures
    must be displayed prominently on the build platform's website and released no
    more than thirty (30) days after resolution.

This tier's requirements are enforced by the auditor.   
The OpenSSF will run a community-maintained repository of approved auditors and
audited build platforms. This repository may be the same one used for the
self-attestation tier.  

Participation in this tier requires coordination between the auditor and the
build platform. The procedure is:
1. The build platform requests an audit from an approved auditor and gives the
   auditor any required access to their systems and documentation.
2. The auditor completes the audit. Regardless of its outcome, the auditor creates
   a PR for the repository containing
    1. Build platform name
    2. Attested SLSA levels
    3. URI that resolves to the public audit report
    4. List of URIs that resolve to the build platform's
        public vulnerability disclosures
    5. (Optional) Build ID
    6. (Optional) List of URIs that resolve to materials for verifying
        provenance (e.g. public keys)
3. The build platform completes the terms and conditions form.

The maintainers will review the PR and merge it after confirming the auditor is
a registered participant in the program..

Once the PR is merged and the terms are signed, the OpenSSF grants the build
platform a limited license to use the SLSA logo on its website and other
promotional materials. Should the build platform fail to adhere to the program's
terms or meet its requirements, the OpenSSF will revoke that license and, if
necessary, enforce its trademarks. 

## Alternatives considered

### Central repository

The OpenSSF would maintain a repository that stored all public evidence of
conformance and provenance verification materials for build platforms taking
part in the program. This option made enforcement easy since you could remove
build platforms that do not comply with the program's terms, but it puts too
much trust in the repository's maintainers. This option would also require
infrastructure for build platforms to rotate and revoke provenance verification
materials, which would complicate the design.

### Build platform attestations

Rather than maintain offline evidence of conformance that would be written and
interpreted by humans, this option would capture the state of the build platform
in an attestation and attach it to all provenance produced by the build
platform. We discarded this option because it would violate SLSA's guiding
principle of trusting systems by moving conformance checks into provenance
verification. It would also bloat the provenance size and require introducing
new, complicated logic in the provenance verifier.

## Appendix A: Conformance questionnaire

This questionnaire is derived from the
[Verifying build platforms](https://slsa.dev/spec/v1.0/verifying-systems) page
of the SLSA Specification v1.0.

## Supply-chain Levels for Software Artifacts (SLSA) Build Level 3 Conformance Program Certification Guide

SLSA (Supply-chain Levels for Software Artifacts) is a framework that defines a
set of security and reliability standards for software supply chains. The
framework aims to improve the overall security of the software ecosystem by
providing guidelines for various stages of the software development and delivery
process. The SLSA Build Level 3 Conformance Program is focused on ensuring that
software is built in a secure and trustworthy manner, following best practices
and adhering to stringent security requirements. To be certified under the
program, a builder must demonstrate that they meet the requirements for SLSA
Build L3. This document outlines the requirements for SLSA Build L3 and provides
guidance for how to demonstrate conformance.

## SLSA Build L3 Requirements

The requirements for SLSA Build L3 are based on a common threat model and
builder model for reasoning about builders' security. These requirements are
organized into sub-categories: Process and Management, Security Measures, and
Artifact Handling.

### Process and Management

1. **External Parameters**: Builders must implement processes for handling
    user-provided external parameters that include sanitizing, parsing, and
    representing all external parameters in the provenance.

> _Example_: Use input validation libraries or techniques to sanitize
user-provided external parameters before processing.

2. **Control Plane Administration**: Builders must have processes in place
    to detect and prevent privileged users from abusing access to influence a
    build or provenance generation.

> _Example_: Implement role-based access control (RBAC) and monitor
privileged user activities.

### Security Measures

1. **Provenance Generation**: Builders must observe the build to ensure the
    accuracy of the provenance and generate provenance for all completed builds.

> _Example_: Use tools like in-toto to generate and store provenance
information.

2. **Development Practices**: Builders must track the control plane's
    software and configuration, build confidence in the control plane's
    software supply chain, secure communications between builder components,
    and be able to perform forensic analysis on compromised executors.

> _Example_: Use software composition analysis tools to track dependencies
and secure communication protocols like TLS for communication between
components.

3. **Managing Cryptographic Secrets**: Builders must store cryptographic
    secrets securely, control access to the secrets, and rotate secrets frequently.

> _Example_: Use secret management solutions like a HSM or cloud secret
manager to store, control access to, and rotate secrets.

### Artifact Handling

1. **Executor Isolation**: Builders must ensure that each executor is
    isolated from the control plane and from all other executors, and that each
    executor has a means to fetch input artifacts.

> _Example_: Use technologies such as containers or virtual machines to
isolate executors and provide controlled access to input artifacts.

2. **Cache Management**: Builders may have zero or more caches to store
    frequently used dependencies and must validate cache contents before use.

> _Example_: Use content-addressable storage and verify checksums or
signatures of cached artifacts before use.

3. **Output Storage**: Builders must prevent builds from reading or
    overwriting files that belong to another build.

> _Example_: Implement proper access controls on output storage and use
unique identifiers for each build's output artifacts.

### Continuous Monitoring and Improvement

To ensure ongoing compliance with the SLSA Build L3 requirements, builders
should have a continuous monitoring and improvement plan in place. This plan
should include the following elements:

1. **Regular Security Reviews**: Conduct periodic reviews of security
    practices, threat models, and risk assessments to identify areas for
    improvement and ensure that the builder's security posture remains up-to-date.
    These reviews may be done internally and their results need not be published.

> _Example_: Schedule quarterly or biannual security reviews, with
additional reviews as needed in response to significant changes in the
builder's environment or practices.

2. **Incident Response and Recovery**: Develop and maintain an incident
    response plan to effectively handle security incidents, breaches, or
    vulnerabilities. Regularly review and update the plan to address evolving
    threats and lessons learned from past incidents.

> _Example_: Conduct tabletop exercises or simulations to test the incident
response plan and identify areas for improvement.

3. **Emerging Threats and Vulnerabilities**: Stay informed about emerging
    security threats and vulnerabilities relevant to the builder's environment.
    Regularly update security practices and tools to address these new risks.

> _Example_: Subscribe to industry newsletters, join relevant security
forums, and participate in conferences to stay current on the latest
threats and best practices.

4. **Updates to SLSA Requirements**: Monitor updates to the SLSA framework
    and requirements to ensure continued compliance. Update internal processes
    and practices as needed to align with the evolving standards.

> _Example_: Assign a responsible team or individual to track changes in
the SLSA requirements and communicate updates to the relevant stakeholders.

### Training and Awareness

To meet the Training and Awareness requirement, builders should implement the
following initiatives:

1. **Regular Security Training**: Provide ongoing security training for
    staff members, covering topics such as secure coding practices, threat
    modeling, and vulnerability management.

> _Example_: Schedule annual security training sessions, with additional
training for new hires and refresher courses as needed.

2. **Workshops**: Organize workshops focused on specific security topics or
    practices relevant to the builder's environment, allowing staff to deepen
    their understanding and improve their skills.

> _Example_: Conduct workshops on secure development methodologies, such as
DevSecOps, or on the implementation of specific security tools and processes.

3. **Awareness Campaigns**: Run awareness campaigns to reinforce the
    importance of building secure software and to promote a culture of security
    within the organization.

> _Example_: Use posters, newsletters, or internal communications to share
security tips, best practices, and updates on the latest threats and
vulnerabilities.

## Demonstrating Compliance

To demonstrate compliance with SLSA Build L3, builders must complete the
following steps:

1. **Self-Attestation Questionnaire**: Builders should answer a
    questionnaire that covers the requirements outlined in the SLSA Build L3
    Requirements section. This questionnaire should be published on the
    builder's website or another publicly accessible platform for transparency
    and self-attestation purposes.

> _Submission Process_: Builders should provide a URL to their completed
questionnaire in their self-attestation submission, along with any relevant
supporting documentation.

2. **Third-Party Audit/Certification (Optional)**: Builders may choose to
    seek an audit or certification from a third party to further validate their
    compliance. In this case, the questionnaire for third-party certification
    need not be published.

> _Audit Process_: Builders should contact an accredited third-party
auditor and provide the necessary documentation to demonstrate their
compliance with the SLSA Build L3 requirements. The auditor will review the
submission and provide a certification upon successful validation of compliance.  
_Accredited Auditors_: A list of accredited auditors will be available in
the future. This guide will be updated once the process for accrediting
auditors is in place. 

1. **Provenance Generation**: In addition to completing the questionnaire,
    builders must generate provenance for all completed builds that meets the
    requirements of SLSA Build L3.
2. **Attestation**: Builders must attest to their L3+ capabilities within a
    limited validity period. All provenance generated by L3+ builders must be
    technically and non-falsifiably linkable to the attesting party.

_Expected Timeline:_ Builders should allow for a reasonable time frame (e.g.,
4-6 weeks) for review and certification, taking into account the time required
for self-attestation, third-party audits (if applicable), and any necessary
follow-up actions.

## Conclusion

The SLSA Build L3 Conformance Program is designed to help builders of software
and their consumers ensure that the software they produce and use is built in a
secure and trustworthy manner. By meeting the requirements outlined in this
guide, builders can demonstrate their commitment to building software that meets
the highest standards of security and reliability. Implementing continuous
monitoring and improvement plans, as well as providing training and awareness
for staff, further strengthens the builder's security posture and contributes to
a safer software ecosystem.