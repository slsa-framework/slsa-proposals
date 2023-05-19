# Proposal 6: Restructure the Versioning System for SLSA

*   Proposer: [Tianyu Chen](https://github.com/chtiangg) (tianyuchen@google.com)
*   GitHub Issue: [#742](https://github.com/slsa-framework/slsa/issues/742)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Original link:
    [Google docs](https://docs.google.com/document/d/1sTo6Lccu5I5mEY8oemF964KbKQvdvUHcbVRdcCxK24w/edit?usp=sharing)
    (see that doc for reviewer comments)

## Abstract

This doc proposes a mechanism to improve the experience of maintaining the
versioning system for SLSA specification in slsa-framework/slsa (the repo), and
to automate building and deploying slsa.dev (the website).

## Problem Statement

[slsa-framework/slsa](https://github.com/slsa-framework/slsa) is the Git repo
backing [slsa.dev](http://slsa.dev). Right now, this repo maintains the versions
of SLSA specification with directories in the main branch, for example,
[/docs/spec](https://github.com/slsa-framework/slsa/tree/main/docs/spec)
contains a few subdirectories including v0.1 and v1.0, while
[/docs/provenance](https://github.com/slsa-framework/slsa/tree/main/docs/provenance)
and
[docs/verification_summary](https://github.com/slsa-framework/slsa/tree/main/docs/verification_summary)
additionally have their own directories and versions. To
[switch](slsa_dev_dropdownlist.png) versions on the website, a reader can use
the dropdownlist on a web page that has other versioned files in the repo.

However, using a directory to maintain versioned files is non-trivial. The
content of a versioned file may be mostly duplicated from its previous version,
and the full commit history may not be available for the latest version due to
manually copying. Leveraging the existing Git mechanisms could ease the
maintenance pain and clean the directory structure, which gives us the
opportunity to further automate the process of building and deploying the
website as well. This doc is to propose the changes, so that we can improve the
maintenance experience.

## Document Requirements

Requirements:

-   MUST make it easy to build and preview the site locally.
-   MUST NOT affect the current approved version, when make a change in a prior
    or future version
-   SHOULD make developing a future version without manually copying existing
    code.
-   SHOULD make building and deploying the website happen together in one
    process.
-   SHOULD allow a reader to switch between versions on the deployed website.
-   MAY manually launch a deployment of the website by the repo owner.

Out of scope:

-   [out of scope] Change the static site generator used to build the website,
    such as upgrading to Jekyll v4~ or switching to Hugo, though some work here
    unblocks that.
-   [out of scope] Make major updates to the content of any files in the repo,
    although fixing URLs with the proposed website structure is in scope.

## Design Idea

NOTE: The examples in this section are preliminary and for illustrative
purposes.

### The Website

#### Example

The following is the directory structure of the deployed website.

```
_site/
├─ v1.0/
│  ├─ faq.html
│  ├─ levels.html
│  ├─ onepage.html
│  ├─ requirement.html
│  └─ ...               # other versioned specification files
├─ v0.1/
│  ├─ faq.html
│  ├─ levels.html
│  ├─ onepage.html
│  ├─ requirement.html
│  └─ ...               # other versioned specification files
├─ assets/
├─ fonts/
├─ images/
├─ vendor/
├─ blog.html            # unversioned file
├─ community.html       # unversioned file
├─ spec-stages.html     # unversioned file
├─ ...                  # other unversioned files
```

#### File and URL

*What remains the same*

-   An unversioned file lives in the top level directory of the website, and has
    no change in its URL, for example, [Blog](https://slsa.dev/blog) and
    [Community](https://slsa.dev/community).
-   A versioned file has a dropdownlist that can switch between versions at the
    top left corner of its rendered web page.
-   Versions don't share any versioned file, nor URL, even if the content may be
    the same. For example, `_site/v1.0/levels` and `_site/v2.0/levels` are
    different.

*What is changing*

-   `https://slsa.dev/spec/v1.0/<page>` is renamed to
    `https://slsa.dev/v1.0/<page>`.
-   `https://slsa.dev/provenance/v1/<page>` is renamed to
    `https://slsa.dev/v1.0/provenance`, but the predicateType remains the same.
-   `https://slsa.dev/verification_summary/v1/<page>` is renamed to
    `https://slsa.dev/v1.0/verification_summary`, but the predicateType remains
    the same.
-   Redirects exist from old to new pages, so URLs don't break.
-   To build and deploy the website, use a Github Workflow in
    [the proposed build process](#automate-building-and-deploying-the-website).

### The Repo

#### Example

```
slsa-framework/slsa:main
├─ docs/
│  ├─ _data/
│  ├─ _includes/
│  ├─ _layout/
│  ├─ _posts/
│  ├─ _sass/
│  ├─ fonts/
│  ├─ images/
│  ├─ vendor/
│  ├─ v0.1/    # a git subtree which has the same content as the branch `release/v0.1`
│  ├─ v1.0/    # a git subtree which has the same content as the branch `release/v1.0`
│  ├─ .ruby-version
│  ├─ _config.yml
│  ├─ Gemfile
│  ├─ Gemfile.lock
│  ├─ README.md
│  └─ <other-unversioned-files>.md
├─ .gitignore
├─ LICENSE.md
├─ README.md
└─ ...

# Files were formerly in main/docs/*/v1, such as:
# `docs/spec/v1.0/*`,
# `docs/provenance/v1/*`,
# `docs/verification_summary/v1/*`
slsa-framework/slsa:release/v1.0
└─ <v1.0-versioned-files>.md

# Files were formerly in main/docs/*/v0.1, such as:
# `docs/spec/v0.1/*`,
# `docs/provenance/v0.1/*`,
# `docs/verification_summary/v0.1/*`
slsa-framework/slsa:release/v0.1
└─ <v0.1-versioned-files>.md
```

#### Main Branch

The directory structure of `main` stays the same as the repo has right now.

*What is changing*

-   `/docs/spec`, `/docs/provenance`, `/docs/verification_summary`, and
    `/docs/github-actions-workflow` are removed from the main branch.
-   `/docs/v1.0` and `/docs/v0.1` are two Git subtrees that have the same
    content as [version branches](#version-branches). Using Git subtrees is to
    ensure a consistent user experience for the community, so that a contributor
    only needs to make changes in `main`.
-   When detecting a push in the versioned directories such as `/docs/v+/`, a
    Github Workflow syncs the same change into the version branches. For
    example:

    ```yaml
    # sync_branch.yml lives in .github/workflows.
    on:
      push:
        branches: [ main ]
        paths: [ docs/v+/ ]
    ```

-   Every permalink is updated to use the dir structure described in
    [the website](#the-website) section.

#### Version Branches

Every prior or future specification has a version branch.

*What is changing*

-   `release/<version|version-draft>` is the branch pattern for a prior or
    future `version`, where the `version` must contain both major and minor.
-   At the top level directory, a branch has all the versioned file from
    `/docs/spec/<version>/*`, `/docs/provenance/<version>.md`,
    `/docs/verification_summary/<version>.md`, and
    `/docs/github-actions-workflow/<version>.md`. All specification files in the
    same branch share the same version.
-   When a new version branch is created, the repo owner is responsible to
    manage Git subtrees and other website configs for the branch in `main`.

#### Code Contribution

The process should remain the same as it is for today. Github subtrees and
version branches are agnostic to community contributors.

-   If a new version is desired, a contributor should raise the request either
    in the SLSA weekly meeting or create an issue in the repo. The repo owner
    evaluates the request, and sets up the new version in the repo.
-   If a change is desired in the prior or future version, the contributor
    should fork the repo, clone it locally if they are familiar with git, make
    the change in the `main:docs/<version>` directory, and send a PR against
    `main`.

### Automate Building and Deploying The Website

A new Github Workflow monitors the push and automates the building and
deployment process for the website. The repo owner can also manually launch the
Github Workflow.

Meanwhile, there is no change for serving the website locally.
[/docs/README.md](https://github.com/slsa-framework/slsa/blob/main/docs/README.md)
is remain the same.

The following is a snapshot of the script:

```yaml
# deploy_website.yml lives in .github/workflows.
# This script is preliminary and for illustrative purposes.

# ... Prepares the environment for Github Workflow
jobs:
  build:
      runs-on: ubuntu-latest
      steps:
      - name: Checkout
          uses: actions/checkout@v3
      - name: Setup pages
          uses: actions/configure-pages@v3
      - name: Build
          uses: actions/jekyll-build-pages@v1
          with:
              source: ./docs
      - name: Upload artifacts
          uses: actions/upload-pages-artifact@v1
# ... Deploy to your own website.
```

### Disadvantages

-   An additional Github workflow is required to sync up the code between
    `main:docs/<version>` and version branches.
-   Although using Git subtrees is agnostic to community contributors, it add
    more overhead and responsibility to the repo owners.
-   It leaves some versioned directories in the `main` branch.

## Alternatives considered

### Use Github submodules

Git branches is the way to manage versioned specifications. Versioned files of
each specification are stored in their own branch. Git submodules are created in
`main:docs/<version>` and point to version branches. For example
`main:docs/v1.0` points to `release/v1.0`, and `main:docs/v0.1` point points to
`release/v0.1`.

A GitHub Workflow clones the `main` branch once, pulls the latest files for all
the submodules, builds the website once, and deploys the website to Github
Pages.

*Disadvantages*

-   The usage of Git submodules is non-standard, and using submodules is
    error-prone and toilsome for both the repo owner and community contributors.
-   It leaves some versioned directories in the `main` branch.

*What is changing*

-   To serve the website locally, step 2 in
    [/docs/README.md](https://github.com/slsa-framework/slsa/blob/main/docs/README.md)
    is updated to be:

    ```bash
    git clone --recurse-submodules https://github.com/chtiangg/slsa.git
    git submodule update --remote
    cd slsa/docs
    ```
-   A new Github Workflow script builds and deploys the website. The following
    is a code snapshot.
    
    ```yaml
    # deploy_website.yml lives in .github/workflows.
    # This script is preliminary and for illustrative purposes.

    # ... Prepares the environment for Github Workflow
    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - name: Checkout
            uses: actions/checkout@v3
            with:
                submodules: recursive
        - name: Setup pages
            uses: actions/configure-pages@v3
        - name: Build
            uses: actions/jekyll-build-pages@v1
            with:
                source: ./docs
        - name: Upload artifacts
            uses: actions/upload-pages-artifact@v1
    # ... Deploy to your own website.
    ```

### Build every version branch separately

Git branches is the way to manage versioned specifications. Versioned files of
each specification are stored in their own branch. Additionally, every branch
also contains a full set of style, template, and Jekyll related files. `main`
contains unversioned files that are shared across versions.

A GitHub Workflow clones the repo, builds for every branch besides `main`
separately, assembles the website with every build result, and deploys the
website to GitHub Pages.

*Disadvantages*

-   A community contributor needs to understand Git branches. When making a
    specification change, they need to work in a branch and send PR to the same
    branch.
-   It requires assembling the final website of multiple branch builds, while
    each branch must have a full set of style, template, and Jekyll related
    files. It may also be an advantage, since we are able to deal with one
    branch at a time.
    -   Building the `main` branch produces the unversioned files.
    -   Building a version branch produces the versioned directory and files in
        it.
    -   However, using `baseurl: "/<version>" destination: "_site/<version>"` in
        `_config.yml` can avoid moving build results around.

*Variant 1*

Clone the repo in different jobs, build a specific branch in each job, assemble
the website, and deploy it. This option requires sharing build results between
jobs in Github Workflow, so it is more complicated.

*Variant 2*

Instead of using Git branches, create a separate repo to manage each SLSA
version. On top of the disadvantages, this variant is an overkill to manage
around 10~ markdown files.

### Alternatives Comparison

|                   | Effort of assembling                                      | Effor of contribtion                                    | Effor of maintenance                                      | Versioned files in main
|:----------------- | :-------------------------------------------------------- | :------------------------------------------------------ | :-------------------------------------------------------- | :----------------------
|Git Subtrees       | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low  | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Yes
|Git Submodules     | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/f4cccc/f4cccc.png) high | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Yes
|Build Every Branch | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/f4cccc/f4cccc.png) high | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) No
