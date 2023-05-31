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

## The Structure of slsa-framework/slsa

The following sections are the new proposed structure, including branches, for
the repo.

### Example

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
│  ├─ v0.1/    # a Git subtree which has the same content as the branch `release/v0.1`
│  ├─ v1.0/    # a Git subtree which has the same content as the branch `release/v1.0`
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
└─ ...

# Files were formerly in main/docs/*/v0.1, such as:
# `docs/spec/v0.1/*`,
# `docs/provenance/v0.1/*`,
# `docs/verification_summary/v0.1/*`
slsa-framework/slsa:release/v0.1
└─ <v0.1-versioned-files>.md
└─ ...
```

### Main Branch

The directory structure of `main` stays the same as the repo has right now.

*What is changing*

-   `/docs/spec`, `/docs/provenance`, and `/docs/verification_summary`, are
    removed from `main`.
-   `/docs/v1.0` and `/docs/v0.1` are two Git subtrees that have the same
    content as [version branches](#version-branches). Using Git subtree is to
    ensure a consistent user experience for the community, so that a contributor
    only needs to make changes in `main`.
-   When detecting a push in the versioned directories, a Github Actions
    workflow syncs the same change into the version branches. For example:

    ```yaml
    # sync_branch.yml lives in .github/workflows.
    on:
      push:
        branches: [ main ]
        paths: [ "docs/v[0-9.]+/**" ]
    ```

-   Every permalink is updated to use the dir structure described in
    [the website](#the-website) section.

### Version Branches

Every prior or future specification has a version branch.

*What is changing*

-   `release/<version|version-draft>` is the branch pattern for a prior or
    future `version`, where the `version` must contain both major and minor.
-   At the top level directory, a branch has all the specification files from
    `/docs/spec/<version>/*`, `/docs/provenance/<version>.md`, and
    `/docs/verification_summary/<version>.md`, and these files share the same
    version number.
    -   NOTE: a version branch and its subtree in `main` may contain files other
        than specification, such as images and protos.
-   When a new version branch is created, the repo owner is responsible to
    manage Git subtrees and other website configs for the branch in `main`.

### Code Contribution

The process should remain the same. Contributors fork, clone, and submit PRs to
the version directories in `main` just as they do today. Syncing Github subtrees
and version branches should be transparent to community contributors.

### Creating new versions

When a new version is desired, a project maintainer can create a new branch as
follows (using `v1.0` → `v1.1` as an example):

```bash
git branch release/v1.1 release/v1.0
git checkout main
git subtree add --prefix=docs/v1.1 release/v1.1
git push origin release/v1.1 main
```

The GitHub Actions workflow that syncs branches should automatically pick up the
new version, for example:

```bash
# If a change is committed into `main`, the workflow runs
git subtree push --prefix=docs/v1.1 https://github.com/slsa-framework/slsa.git release/v1.1

# if a change is committed into `release/v1.1`, the workflow runs
git subtree pull --prefix=docs/v1.1 https://github.com/slsa-framework/slsa.git release/v1.1 --squash
```

### Disadvantages

By using Git branches and subtress to manage the specification across versions,
a few disadvantages are introduced:

-   An additional Github Actions workflow is required to sync up the code
    between Git subtrees (or `main:docs/<version>`) and version branches.
-   Although syncing between a Git subtree and a branch is agnostic to community
    contributors, it adds more overhead and responsibility to the repo owners to
    maintain both.
-   It leaves some versioned directories in `main`.

## Benefits of This Proposal

-   To allow viewers to more easily see the revision history of the
    specification across SLSA versions.

    Currently it is difficult to view the history of changes to the
    specification across versions due to the directory copies. If you limit the
    git history to the `/docs/spec/<version>` directory, this history stops at
    the copy
    ([example](https://github.com/slsa-framework/slsa/commits/main?after=750dc05dc88c81aa9afc35beed1faaa9ed6aabde+0&branch=main&path%5B%5D=docs&path%5B%5D=spec&path%5B%5D=v1.0&qualified_name=refs%2Fheads%2Fmain)).
    If you look at the entire repo, it is hard to follow because there are
    changes to many parts of the site all mixed together. This makes it hard to
    actually follow how SLSA changed over time.

    With this proposal, one can view the history by simply viewing the git log
    of the `release/<version>` branch. This branch only contains commits that
    went into `<version>`, and it includes history from prior branches. It also
    allows ones to easily diff between two versions since they have a common
    ancestor.

-   To make it easier to develop and release new versions.

    Currently it is somewhat painful to develop a new version due to the
    directory copies. Changes from one branch cannot easily be merged into
    another branch, making it difficult to keep an upcoming version in sync with
    changes to the latest stable version. When it comes time to cut a release
    candidate, we have to do directory copies and hand-edit a bunch of files,
    and it's hard for reviewers to ensure that nothing has been changed
    accidentally.

    With this proposal, development of a new version is more natural. Creating a
    branch is done via `git branch` (plus `git subtree add`), merging between
    branches is possible (with `git subtree merge`), and cutting a release only
    involves creating a new subtree.

### Alternatives considered

#### Use Github submodules

Git branches are the way to manage the specification across versions.
Specification files that share the same version are stored in their own branch.
Git submodules are created in `main:docs/<version>` and point to version
branches. For example `main:docs/v1.0` points to `release/v1.0`, and
`main:docs/v0.1` point points to `release/v0.1`.

A Github Actions workflow clones `main` once, pulls the latest files for all the
submodules, builds the website once, and deploys the website to Github Pages.

*Disadvantages*

-   The usage of Git submodules is non-standard, and using submodules is
    error-prone and toilsome for both the repo owner and community contributors.
-   It leaves some versioned directories in `main`.

*What is changing*

-   To serve the website locally, step 2 in
    [/docs/README.md](https://github.com/slsa-framework/slsa/blob/main/docs/README.md)
    is updated to be:

    ```bash
    git clone --recurse-submodules https://github.com/chtiangg/slsa.git
    git submodule update --remote
    cd slsa/docs
    ```

-   A new Github Actions workflow script builds and deploys the website. The
    following is a code snapshot.

    ```yaml
    # deploy_website.yml lives in .github/workflows.
    # This script is preliminary and for illustrative purposes.

    # ... Prepares the environment for Github Actions workflow
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

#### Build every version branch separately

Git branches are the way to manage the specification across versions.
Specification files that share the same version are stored in their own branch.
Additionally, every branch also contains a full set of style, template, and
Jekyll related files. `main` contains unversioned files that are shared across
versions.

A Github Actions workflow clones the repo, builds for every branch besides
`main` separately, assembles the website with every build result, and deploys
the website to GitHub Pages.

*Disadvantages*

-   A community contributor needs to understand Git branches. When making a
    specification change, they need to work in a branch and send PR to the same
    branch.
-   It requires assembling the final website of multiple branch builds, while
    each branch must have a full set of style, template, and Jekyll related
    files. It may also be an advantage, since we are able to deal with one
    branch at a time.
    -   Building `main` produces the unversioned files.
    -   Building a version branch produces the versioned directory and files in
        it.
    -   However, using `baseurl: "/<version>" destination: "_site/<version>"` in
        `_config.yml` can avoid moving build results around.

*Variant 1*

Clone the repo in different jobs, build a specific branch in each job, assemble
the website, and deploy it. This option requires sharing build results between
jobs in Github Actions workflow, so it is more complicated.

*Variant 2*

Instead of using Git branches, create a separate repo to manage each SLSA
version. On top of the disadvantages, this variant is an overkill to manage
around 10~ markdown files.

#### Alternatives Comparison

|                   | Effort of assembling                                      | Effort of contribution                                  | Effort of maintenance                                     | Versioned files in main
|----------------- | :-------------------------------------------------------- | :------------------------------------------------------ | :-------------------------------------------------------- | :----------------------
|Git Subtrees       | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low  | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Yes
|Git Submodules     | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/f4cccc/f4cccc.png) high | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Yes
|Build Every Branch | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/f4cccc/f4cccc.png) high | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) No

## Appendix

The changes in this section are independent of this proposal which enabled us to
manage SLSA version with Git subtree and branches. However, we need to make
these changes first to be able to create subtree branches. For example, the
Website and URL change requires moving specification files into their own
version directory in `main`.

### The Website (Issue [#266](https://github.com/slsa-framework/slsa/issues/266))

NOTE: Issue 266 - Hosting the website on Netlify makes us config URL redirection
to ensure [old URLs](https://www.w3.org/Provider/Style/URI) still work.

#### Example

The following is the directory structure of the deployed website.

```
_site/
├─ v1.0/
│  ├─ faq.html
│  ├─ levels.html
│  ├─ onepage.html
│  ├─ requirement.html
│  └─ ...               # other specification files for v1.0
├─ v0.1/
│  ├─ faq.html
│  ├─ levels.html
│  ├─ onepage.html
│  ├─ requirement.html
│  └─ ...               # other specification files for v0.1
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
-   A specification file has a dropdownlist that can switch between versions at
    the top left corner of its rendered web page.
-   Versions don't share any specification file, nor URL, even if the content
    may be the same. For example, `_site/v1.0/levels` and `_site/v2.0/levels`
    are different.

*What is changing*

-   `https://slsa.dev/spec/v1.0/<page>` is renamed to
    `https://slsa.dev/v1.0/<page>`.
-   `https://slsa.dev/provenance/v1/<page>` is renamed to
    `https://slsa.dev/v1.0/provenance`, but the predicateType remains the same.
-   `https://slsa.dev/verification_summary/v1/<page>` is renamed to
    `https://slsa.dev/v1.0/verification_summary`, but the predicateType remains
    the same.
-   Redirects exist from old to new pages, so URLs don't break.
-   To build and deploy the website, use a Github Actions workflow in
    [the proposed build process](#automate-building-and-deploying-the-website).

### Automate Building and Deploying The Website

A new Github Actions workflow monitors the push and automates the building and
deployment process for the website. The repo owner can also manually launch the
Github Actions workflow.

Meanwhile, there is no change for serving the website locally.
[/docs/README.md](https://github.com/slsa-framework/slsa/blob/main/docs/README.md)
remains the same.

The following is a snapshot of the script:

```yaml
# deploy_website.yml lives in .github/workflows.
# This script is preliminary and for illustrative purposes.

# ... Prepares the environment for Github Actions workflow
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
