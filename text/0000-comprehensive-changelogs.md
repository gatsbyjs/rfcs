- Start Date: 2019-02-04
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

The aim of this RFC is to make Gatsby's changelogs more _comprehensive_, by reducing the number of changes that don't get added to changelogs.

Note: this RFC is specifically not about changing Gatsby's release process to semver (or something else). That's a discussion for another RFC.

# Motivation

A changelog is a markdown file listing all the versions of a package. For each version there will be a list of entries, with one _changelog entry_ describing one change to the package. [Example changelog](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby/CHANGELOG.md).

For Gatsby, a useful changelog has the following qualities:

- _comprehensive_ - every pull request merged into the Gatsby repo should create a changelog entry in the relevant package
- _clear_ - each changelog entry should have a description about the change and what impact it has
- _concise_ - each changelog entry should be a maximum of one or two paragraphs
- _link to further information_ - each changelog entry should link to the related pull request, credit the author, and where necessary link to any further documentation

Providing useful changelogs means we have a human-readable and accurate summary of changes over time. This is useful for:

- developers using Gatsby - good changelogs give confidence when updating to newer versions of Gatsby (or official Gatsby packages)
- developers considering Gatsby for a project - good changelogs are often used as a quality indicator for open source projects
- Gatsby Inc - good changelogs serve as a public reference, making it easier to highlight new features to Gatsby users (both current and future)

The purpose of this RFC is to discuss making Gatsby's changelogs more _comprehensive_.

There will be a separate RFC for updating the formatting of Gatsby's changelogs, which will discuss making the changelogs more _clear_, _concise_, and _linking to further information_.

# Detailed design

## Ensure changelogs are comprehensive

Currently we generate changelogs based on [conventional commit style](https://www.conventionalcommits.org/en/v1.0.0-beta.3/) PR titles. This is good where it works but prone to human error, as GitHub doesn't provide a way to enforce the format of a PR title.

A pull request with a title like _chore(gatsby): Correct fieldOwners in joi schema_, will be added to the changelog for the `gatsby` package. Changing that title to remove the conventional commit prefix, _Correct fieldOwners in joi schema_, would result in the change not being added to any changelog.

The current approach works but there are limitations:

- maintainers must remember to change the title of each PR to match conventional commit style
- contributors have no obvious way to find out how to format their PR titles (We should add documentation about this expectation, but we shouldn't require people to read a lot of documentation before opening a PR.) 
- GitHub doesn't provide a way to enforce the format of a PR title

Here are a few approaches for fixing the limitations in our current process:

### 1. Don't change the existing process

Stick to the existing setup. Improve the process by socialising the responsibility to manually update the changelog if you merge a PR without the conventional commits compatible title.

Pros:

- No setup time!

Cons:

- Prone to human error

### 2. Add an 'uncategorised' category to the existing config

Stick to the existing setup, but add a backup category called 'Uncategorised' for PRs that are merged without a conventional commits compatible title.

This could potentially be enhanced by having gatsbot post a comment about 'how to title your PR' for PRs that don't match conventional commit format.

Pros:

- Minimal setup time

Cons:

- 'Uncategorised' is not a very useful category

### 3. Don't categorise commits

Drop usage of conventional commits style PR titles, and just list all PRs related to each package version. [See an example from artsy](https://github.com/artsy/emission/blob/master/CHANGELOG.md), along with [discussion of their implementation](https://github.com/artsy/reaction/issues/1095).

Pros:

- Minimal setup time
- All PRs will get added to the changelog

Cons:

- Potentially less useful than having a categorised changelog

### 4. Add a CI step that fails if a PR title doesn't match conventional commit style

Pros:

- Ensures all PRs are categorised
- Allows contributors to categorise their own commits

Cons:

- Adds an 'invisible' barrier to getting your PR merged
- Is there a way to trigger a new CI run after a PR title is updated?

### 5. Use gatsbot / Peril to categorise commits

gatsbot would comment on a PR with a list of options:

<img width="804" alt="gatsbot-example" src="https://user-images.githubusercontent.com/381801/52206647-c18ae980-2872-11e9-87ee-0905838206bd.png">

Choosing an option would change the title or apply a label (or do something else) resulting in the PR being added to the changelog with the selected category applied.

CI would be configured to fail until an option was chosen. Using a conventional commit title for your PR would result in a checkbox being preselected.

Pros:

- Allows contributors to categorise their own commits
- Doesn't require any special knowledge (you don't need to read the docs to discover that you should format your PR title like X to have it included in the changelog)

Cons:

- This option will take the most amount of time to research, design and implement
- Can we enforce this with CI?

# Drawbacks

The above options are mostly variations based on tradeoffs between time-to-implement and the quality of the end-result.

# Alternatives

Several alternatives are described above, including not making any changes.

# Adoption strategy

This RFC is mostly aimed at Gatsby maintainers and could be taught via the weekly maintainers meeting, automation / CI (where appropriate), and documentation on gatsbyjs.org.

# How we teach this

Similar to the adoption strategy. 

Whatever option is chosen, it should be documented in the contributing section of gatsbyjs.org.

# Unresolved questions

There are a few unanswered questions listed in options 4 and 5. 

### How to handle PR's with a single commit

If a PR has only one commit, GitHub will use the commit message for the merge commit instead of the PR title. Will we need to work around this?


