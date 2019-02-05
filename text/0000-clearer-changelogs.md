- Start Date: 2019-02-04
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

The aim of this RFC is to make Gatsby's changelogs clearer and more concise, by improving their formatting.

> Note: See the [related RFC on changelog entries](https://github.com/gatsbyjs/rfcs/pull/27)

> Note: this RFC is specifically not about changing Gatsby's release process to semver (or something else). That's a discussion for another RFC.

# Basic Example

## Current changelog example:

---

## [2.0.112](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.111...gatsby@2.0.112) (2019-02-04)

### Bug Fixes

- **gatsby:** update terser-webpack-plugin to avoid webpack error ([#11542](https://github.com/gatsbyjs/gatsby/issues/11542)) ([f945fe8](https://github.com/gatsbyjs/gatsby/commit/f945fe8))

## [2.0.110](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.109...gatsby@2.0.110) (2019-02-01)

**Note:** Version bump only for package gatsby

## [2.0.107](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.106...gatsby@2.0.107) (2019-01-31)

### Bug Fixes

- **gatsby:** always respond with index html without checking path ([#11400](https://github.com/gatsbyjs/gatsby/issues/11400)) ([2f79efe](https://github.com/gatsbyjs/gatsby/commit/2f79efe))
- **gatsby:** check type when querying by ID ([#11448](https://github.com/gatsbyjs/gatsby/issues/11448)) ([1d95e67](https://github.com/gatsbyjs/gatsby/commit/1d95e67))

### Features

- **gatsby:** Allow specifying sort order per sort field ([#11381](https://github.com/gatsbyjs/gatsby/issues/11381)) ([11e8930](https://github.com/gatsbyjs/gatsby/commit/11e8930))

---

## Proposed changelog example:

---

## [2.0.112](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.111...gatsby@2.0.112) (2019-02-04)

- 🐛 **Bug Fix** - **gatsby:** update terser-webpack-plugin to avoid webpack error [#11542](https://github.com/gatsbyjs/gatsby/issues/11542) ([@this-excellent-contributor](https://github.com/this-excellent-contributor))

## [2.0.110](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.109...gatsby@2.0.110) (2019-02-01)

- 🧹 **Chore**: update packages peer-dependencies to discard gatsby-alpha [#10252](https://github.com/gatsbyjs/gatsby/pull/10252)

## [2.0.107](https://github.com/gatsbyjs/gatsby/compare/gatsby@2.0.106...gatsby@2.0.107) (2019-01-31)

- 🐛 **Bug Fix** - **gatsby:** always respond with index html without checking path [#11400](https://github.com/gatsbyjs/gatsby/issues/11400) ([@this-excellent-contributor](https://github.com/this-excellent-contributor))
- 🐛 **Bug Fix** - **gatsby:** check type when querying by ID [#11448](https://github.com/gatsbyjs/gatsby/issues/11448) ([@this-excellent-contributor](https://github.com/this-excellent-contributor))

  Fixes an `Expected value of type` error that could occur when running a GraphQL query that searches by Id.
- 🚀 **Feature** - **gatsby:** Allow specifying sort order per sort field
  [#11381](https://github.com/gatsbyjs/gatsby/issues/11381) ([@this-excellent-contributor](https://github.com/this-excellent-contributor))

  When writing a GraphQL query that sorts on multiple fields, allow the sort order to be individually specified for each field.

  See the [_Sort_ section of the GraphQL reference docs](https://www.gatsbyjs.org/docs/graphql-reference/#sort) for more information.

-------

# Motivation

Refer to the [Motivation section of sibling RFC 27 for more info](https://github.com/gatsbyjs/rfcs/pull/27)

The purpose of this RFC is to discuss making the information in Gatsby's changelogs more useful.

# Detailed design

Here's a list of changes implemented in the proposed example above.

### Add a (optional, but encouraged) short description to each item

Add a 'no context' description to each PR before merging it. This would allow us to automatically include these descriptions in the changelog.

A 'no context' description is a self contained description that can be understood without referencing the code or an external issue.

This could be opt-in, PRs that contain a _Description_ (or _Changelog_, or similar) heading could have that description automatically added to the appropriate changelog. Gatsby contributors or maintainers should add descriptions to PRs where necessary.

Descriptions could vary in length depending on the PR. A new feature might have a longer description (with a link out to related docs), where a typo fix might not need any description.

### Improve version bump message

Often a changelog entry will be the line "Note: Version bump only for package <packagename>", currently this will happen if the PR title prefix is not `feat`, `fix` or `perf`. 

Modify the changelog generation preset we're using to provide information in place of this message.

### Streamline category display

Most releases have less than five items (often, just one item) in them, making the category headings feel unnecessary. A couple of changes to improve readability:

- replace category headings (bug fix, feature) with a sorted, prefixed list of changes
- add distinct, per-category emojis for easy scanning :eyes: (todo: define an emoji for each category)

### Remove brackets around related PR link

A small change, but helps to separate it from the PR author link.

### Remove link to the specific commit

This doesn't provide any additional information over the PR link. Remove it.

### Credit pull request author(s)

Contributors are nice, let's credit them for their work.

### Update monorepo root changelog

There's a [(mostly unused) root changelog](https://github.com/gatsbyjs/gatsby/blob/master/CHANGELOG.md) which should be updated to point to the individual changelogs.

In the future it might be interesting to compile the individual package changelogs into the root changelog, to provide a nice overview of all changes in the Gatsby repo. This is probably a discussion for a separate issue.

# Drawbacks

All of the information presented in the changelog is already available by hunting around GitHub for it. Instead of spending time implementing this RFC, we could work on features and bug fixes instead.

The optional (but encouraged) descriptions require the most work to implement, and could be dropped without affecting the rest of this RFC.

# Alternatives

There are many ways to format a changelog, this is just one of them.

# Adoption strategy

The optional (but encouraged) descriptions would need some extra tooling support. In addition, maintainers (and contributors) would need to be informed how to write a good description, and how the description would be used. 

# How we teach this

Mostly N/A as this is related to updating tooling. For the parts that need to be taught, we could use a combination of gatsbot, an updated PR template, and documentation on gatsbyjs.org.

# Unresolved questions

Are the benefits of additional descriptions worth the extra work?
