# Proposal 8: Common CI/CD buildType

*   Proposer: [Mark Lodato](https://github.com/MarkLodato) (lodato@google.com)
*   GitHub Issue: [#940](https://github.com/slsa-framework/slsa/issues/940)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Implementation: (not started)

## Abstract

Standardize the `externalParameters` for CI/CD buildTypes in SLSA Provenance.

## Problem

Right now every CI/CD system defines its own `buildType`, but we're starting to
see a common pattern. Every CI/CD system seems to have the following model, and
it would be nice if they had a common schema so that consumers can handle them
the same.

## Prior art

- [GitHub Actions buildType]
- [Google Cloud Build (GCB) buildType]

[GitHub Actions buildType]: https://github.com/slsa-framework/github-actions-buildtypes/blob/main/workflow/v1/README.md)
[Google Cloud Build (GCB) buildType]: https://github.com/slsa-framework/gcb-buildtypes/blob/main/triggered-build/v1/README.md)

## Proposed solution

**This is a very early draft.** Below is a proposed list of common fields, based
on our experience from the [GitHub Actions buildType] and the [Google Cloud
Build (GCB) buildType]. It likely requires further iteration.

Next steps are to propose a concrete schema and validate with many different
CI/CD systems.

### Strawman schema

-   `buildConfigSource` / `workflow` = Reference to the top-level build
    configuration, for cases when the build platform resolved and fetched the
    build configuration from a source repository. Consists of (not necessarily
    separate fields):
    -   `type` = Type of source repository: git, hg, oci, etc.
    -   `repository` = URL of the source repository.
    -   `ref` / `label` / `version` = Label or reference within the repository
        to resolve to a specific artifact (commit, image, etc.). Could be a
        mutable label or an immutable digest—whichever the tenant specified.
    -   `path` / `entryPoint` / `target` = The file or target label within the
        resolved artifact to find the top-level build configuration.
-   `buildConfig` = Inlined top-level build configuration, for cases when it is
    provided directly by the caller. (Mutually exclusive with
    `buildConfigSource`.)
-   `sourceToBuild` = Source artifact to be initially checked out, for cases
    when it is an independent input (and different) from `buildConfigSource` /
    `buildConfig`. This might be unique to Google Cloud Build; I'm unaware of
    other platforms that do this. But it is an important input (more important
    than `parameters`) which is why I call it out. Consists of:
    -   `repository`
    -   `ref` / `label` / `version`
-   `parameters` = Additional independent parameters beyond those above.
    Examples:
    -   `inputs` (GitHub Actions) / `params` (Tekton) / `substitutions` (Google
        Cloud Build) = parameters provided by the user via some UI / CLI / API
    -   `vars` (GitHub Actions) = variables passed in via the repository or
        organization
    -   `directory` (Google Cloud Build) = initial working directory in which to
        start the build

## Example

**TODO:** Map existing CI/CD systems to this schema.

## Considerations

-   How should we indicate that the provenance conforms to this common schema,
    while still indicating how to interpret (i.e. is it a GitHub Actions YAML vs
    Google Cloud Build YAML vs ...)?
    -   Separate `buildType` per platform (status quo) and use duck typing to
        indicate that it fits the common schema. (Seem fragile?)
    -   Separate `buildType` per platform (status quo) + new `buildTypeCategory`
        = `cicd` (we could define others in the future; field name and value
        TBD). I'm leaning towards this.
    -   One common `buildType` + new `buildSubtype` per platform.
-   How many fields should we use for `buildConfigSource`? We could merge some
    or all of the fields together into a single URI, but that comes with some
    trade-offs, including:
    -   Extensibility
    -   Alignment with `resolvedDependencies`
    -   Ease of construction and parsing
    -   Ambiguity
-   What field names should we use that make sense for a wide array of CI/CD
    systems?
-   Does this fit most CI/CD systems well? We'll need to do a broad survey to
    make sure.
-   Should `parameters` be standardized or type-specific? Maybe define standard
    names but allow other ones?
-   Where do we define this?
    -   Within the existing Provenance spec (my inclination)
    -   As a separate page under slsa.dev
    -   As a separate git repo

## Alternatives considered

TODO
