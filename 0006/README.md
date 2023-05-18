# Proposal 6: Restructure the Versioning System for SLSA

*   Proposer: [Tianyu Chen](https://github.com/chtiangg) (tianyuchen@google.com)
*   GitHub Issue: [#742](https://github.com/slsa-framework/slsa/issues/742)
*   Status: [DRAFT](../README.md#meaning-of-status-codes)
*   Original link:
    [Google docs](https://docs.google.com/document/d/1sTo6Lccu5I5mEY8oemF964KbKQvdvUHcbVRdcCxK24w/edit?usp=sharing)
    (see that doc for reviewer comments)

## Abstract

This doc propose a mechanism to improve the experience of maintaining the
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

## The Desired State of The Website and The Repo

NOTE: The examples in this section are preliminary and for illustrative
purposes.

### The Website

#### Example

Below is the directory structure of the deployed website.

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
-   Using
    [the proposed build process](#automate-building-and-deploying-the-website),
    a version directory may contain files that are specific to the version, for
    example `/images`.

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
│  ├─ images/  # contains shared images for the site.
│  ├─ vendor/
│  ├─ v0.1/    # a submodule points to the branch `release/v0.1`.
│  ├─ v1.0/    # a submodule points to the branch `release/v1.0`.
│  ├─ .ruby-version
│  ├─ _config.yml
│  ├─ Gemfile
│  ├─ Gemfile.lock
│  ├─ README.md
│  └─ <other-unversioned-files>.md
├─ .gitignore
├─ .gitmodules  # uses `url = ./` to point to the local repo.
├─ LICENSE.md
├─ README.md
└─ ...

# Files were formerly in main/docs/*/v1, such as:
# `docs/spec/v1.0/*`,
# `docs/provenance/v1/*`,
# `docs/verification_summary/v1/*`
slsa-framework/slsa:release/v1.0
├─ images/     # contains images specifically for this version.
└─ <v1.0-versioned-files>.md

# Files were formerly in main/docs/*/v0.1, such as:
# `docs/spec/v0.1/*`,
# `docs/provenance/v0.1/*`,
# `docs/verification_summary/v0.1/*`
slsa-framework/slsa:release/v0.1
├─ images/     # contains images specifically for this version.
└─ <v0.1-versioned-files>.md
```

#### Main Branch

The directory structure of the `main` branch stays the same as the repo has
right now.

*What is changing*

-   `/docs/spec`, `/docs/provenance`, `/docs/verification_summary`, and
    `/docs/github-actions-workflow` are removed from the main branch.
-   `/docs/images` doesn't contain versioned files and directories anymore. They
    are removed or moved to their branch.
-   `/docs/v1.0` and `/docs/v0.1` are two submodules that point to the
    [version branches](#version-branches). As a result, `.gitmodules` is added
    in the top level directory, and has `url = ./` for each submodule config to
    ensure referencing to the local repo. A future version should follow a
    similar setup in the repo.
-   All permalinks in file content use the dir structure described in
    [the website](#the-website) section.

NOTE: We admit the way of using the Git submodule is non-standard, but it is a
nice compromise and helps us to improve the maintenance experience.

#### Version Branches

Git branch is the way to manage versioned specifications.

*What is changing*

-   `release/<version|version-draft>` is the branch pattern for a prior or
    future `version`, where the `version` must contain both major and minor.
-   In addition to the shared images in the `main` branch, if an image is
    required for a specific version, the `/images` directory in the version
    branch may contain the image and let the versioned files reference it .
-   At the top level directory, a branch has all the versioned file from
    `/docs/spec/<version>/*`, `/docs/provenance/<version>.md`,
    `/docs/verification_summary/<version>.md`, and
    `/docs/github-actions-workflow/<version>.md`. All specification files in the
    same branch share the same version.

#### Code Contribution

Since the repo uses the Git submodule in a non-standard way and for easy
building website purposes, people SHOULD NOT use submodules to commit any
change.

When a community member needs to make a contribution, they fork the repo (and
clone it locally if they are familiar with git). If the change is for a
versioned file (a.k.a. spec file), they should make the change in the version
branch, and send a PR to the version branch. Meanwhile, if the change is for an
unversioned file or the website itself, they should make the change in `main`,
and send a PR to `main`.

However, if the change comes from one of the repo owners, and it has to modify
files in both `main` and version branches in one PR, we assume the owner
understands the risk and knows how to send a proper code update with Submodule
changes.

#### Submodule Pointer

A new change in a version branch makes the Submodule pointer in the `main`
branch stale. A Github Workflow script may be created to automatically update
the Submodule pointing to the latest commit, when the PR is merged. If desired,
the repo owner can also manually launch the Github Workflow.

## Automate Building and Deploying The Website

A shell script should assemble the website, instead of using any logic in the
Github Workflow script.

### Checkout repo, load all submodules, and build once

The GitHub Workflow clones the `main` branch once, pulls the latest files for
all the submodules, builds the website once, and deploys the website to Github
Pages.

*Disadvantages*

-   The usage of Git submodule is non-standard, and it leaves some versioned
    directories in the `main` branch.

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
    is a cod snapshot.
    
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

### Alternatives considered: Build each branch separately, then assemble

The GitHub Workflow clones the repo, builds for every branch besides `main`
separately, assembles the website with every build result, and deploys the
website to GitHub Pages.

*Disadvantages*

-   It requires assembling the final website of multiple branch builds, while
    each branch must have a full set of style, template, and Jekyll related
    files. It may also be an advantage, since we are able to deal with one
    branch at a time.
    -   Building the `main` branch produces the unversioned files.
    -   Building a version branch produces the versioned directory and files in
        it.
    -   However, using `baseurl: "/<version>" destination: "_site/<version>"` in
        `_config.yml` can avoid moving build results around.

*A variant of this option*

Clone the repo in different jobs, build a specific branch in each job, assemble
the website, and deploy it. This option requires sharing build results between
jobs in Github Workflow, so it is more complicated.

### Alternatives Comparison

|                     | Effort of assembling                                      | Effort of cloning repo                                  | Versioned files in main                                |
| :---                | :----                                                     | :---                                                    | :---                                                   |
| Proposed Solution   | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low    | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low  | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Yes |
| Alternative         | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) Medium | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) Low  | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) No  |
| Alternative Variant | ![#](https://placehold.co/15x15/f4cccc/f4cccc.png) High   | ![#](https://placehold.co/15x15/fff2cc/fff2cc.png) High | ![#](https://placehold.co/15x15/d9ead3/d9ead3.png) No  |
