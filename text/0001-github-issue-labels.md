- Start Date: 2018-03-21
- RFC PR: (leave this empty)
- Gatsby Issue: #4311

# Summary

Implement a labelling system for [issues on Gatsby's GitHub repo](https://github.com/gatsbyjs/gatsby/issues) and [pull requests on Gatsby's Github repo](https://github.com/gatsbyjs/gatsby/pulls). This is following up from discussion at the recent Gatsby Maintainer Summit.

Refs https://github.com/gatsbyjs/gatsby/issues/4311

# Motivation

People _want_ to contribute to Gatsby, but with (currently) over 500 open issues and 120 pull requests it's difficult to know where to start.

The aim is to make it easier for new and existing contributors to work on Gatsby without adding too much admin overhead. It's important for the labelling system to be quick to manage to ensure that it's used!

# Detailed design

The broad areas discussed were:

- Type: labels like `bug`, `discussion`, `feature request`
- Status: labels like `needs repro`, `ready`
- Misc: labels like `good first issue`
- Topics: labels like `blogpost`, `themes`, `starters`
- Target (area of responsibility): Use GitHub projects
- Roadmap: Use GitHub Milestones

At a minimum every new issue should have a _Type_ label applied to it, everything else is optional.  Labelling can be fluid - a lot of issues might start with the label 'type: discussion', before being changed to a more specific _type_.

Taking that information along with the existing repo structure gives a setup something like this:

### Type label

Every issue should have exactly one _Type_ label.

labels: `bug`, `discussion`, `feature`, `maintenance`, `documentation`

### Status label

Optional

labels: `needs repro`, `help wanted`

### Misc label

Optional

So far these are labels indicating that an issue is a well-defined piece of work or labels used by bots.

label: `good first issue`, `help wanted`, `hacktober`, `stale?`, `review`

### Topic label

Optional

labels: `topic: blogpost`, `topic: wordpress`, `topic: remark`, `topic: themes`

### Projects

Optional

Assign an issue to one of the existing [projects](https://github.com/gatsbyjs/gatsby/projects): docs, v2, UX (add projects as required)

> Question: Do we need to create any additional projects?

### Milestones

We talked about using GitHub's Milestones as a way of defining a roadmap however, Gatsby already has a [`v2` Project](https://github.com/gatsbyjs/gatsby/projects/2) which contains more info than a Milestone could. In the interest of keeping this plan as simple as possible, I'm going to drop Milestones for now (unless anyone has strong objections). This could be looked at again once the labelling and projects is in place.

### Existing labels

There are 20 existing labels. While we're adding new labels, it makes sense to revisit the existing ones. Having a small, clearly defined set of labels makes it much more likely that the labelling system will be used. Here's what I propose should happen to each of them:

| Label | Destiny | Notes |
|------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------------|
| API/Plugins | ❌delete | too specific (currently applied to 66 issues) |
| bug | ✍️rename | rename to `type: bug` or similar |
| blogpost | ✍️rename | rename to `topic: blogpost` |
| breaking change | ✅keep | It's a breaking change, might be consideration for the next major |
| Documentation | ✍️rename | rename to `type: documentation` or similar |
| DX | ❌delete | too specific (currently applied to 13 issues) |
| Feature Request | ✍️rename | rename to `type: feature or enhancement` or similar |
| good first issue | ✅keep | exact name is used by GitHub, don't rename |
| GraphQL | ❌delete | too specific (currently applied to 13 issues) |
| Hacktoberfest | ✅keep | exact name is used by Hacktoberfest, don't rename |
| Help Wanted for Plugins | ❌delete | too specific (currently applied to 4 issues) |
| Help Wanted in Core | ✍️rename | rename to `help wanted` exact name is used by GitHub, don't rename |
| needs-repro | ✍️rename | rename to `status: needs reproduction` or similar |
| no triage | ✅keep | rename to something that's more descriptive. We could also use `status: confirmed` |
| performance | ❌delete | too specific (currently applied to 4 issues) |
| question | ✍️rename | rename to `type: question or discussion` or similar |
| review | ❌delete | ~~~Used by wafflebot? Investigate this~~~ No longer used |
| stale? | ✅keep | Used by stalebot to close stale issues. Maybe rename to `bot: stale?` |
| status: assigned | ➕added | Mark issues as claimed for non contributors that can't use the assigned field in github |
| status: awaiting author response | ➕added | Additional information or changes have been requested from the author |
| status: awaiting reviewer response | ➕added | A pull request that is currently awaiting a reviewer's response |
| status: blocked | ➕added | Issue or pull request that can't be resolved at the moment |
| status: confirmed | ➕added | Issue with steps to reproduce the bug that’s been verified by at least one reviewer |
| status: duplicate | ❌delete | we're not using it as we just close and refer the issue to the original one so we can still keep track of all duplicates |
| status: inkteam to review | ✍️rename | rename to something that's more descriptive |
| status: needs more info | ➕added | Needs triaging and more information to be resolved |
| status: wip | ➕added | Work in progress ... inputs welcome! ❤️ |
| upstream-issue | ✍️rename | Rename to `type: upstream` as it indicates an issue out of our control |
| themes | ✍️rename | rename to `topic: themes` |
| UX Design | ✍️rename | rename to `type: design` |
| type: maintenance | ➕added | maintenance work that needs to be done, updating packages, tests, ... |
| v0 | ❌delete | it's never been used |
| v1 | ❌delete | We _could_ keep this but I don't think it adds much value (currently applied to 8 issues) |
| v2 | ❌delete | redundant - add all issues with this label to the 'v2' project before deleting the label (currently applied to 7 issues) |

### Implementation steps

- [x] collate any feedback on this RFC
- [x] for 'redundant' labels, add their issues to the appropriate projects
- [x] rename, delete and add labels
- [x] start labelling up _new_ and _recently updated_ issues

### Labels after implementing this RFC

See [Gatsby's labels list](https://github.com/gatsbyjs/gatsby/labels) for the live list of labels.

# Drawbacks

The time to implement is fairly short, but we may discover that the proposed labels don't cover all categories of issue.

Maybe this system turns out not to be useful? If that happens we can stop labelling issues and be in the same situation as now.

It's probably unreasonable to go back and label up _every_ issue. New and recently updated issues should be labelled.

# Alternatives

Alternatives are:

- not labelling issues
- using a different labelling system

# Adoption strategy

This is relevent to the Gatsby GitHub repo, so no adoption strategy is necessary.

# How we teach this

A few approaches:

- By example i.e. start labelling up issues
- Posting to the [Gatsby core maintainers discussion board](https://github.com/orgs/gatsbyjs/teams/gatsby-core-maintainers)
- Adding label descriptions to [GitHub's labels page](https://github.com/gatsbyjs/gatsby/labels)
- Adding labelling info to [CONTRIBUTING.MD](https://github.com/gatsbyjs/gatsby/blob/master/CONTRIBUTING.md)

# Unresolved questions

- Do we need to create any additional projects to assign issues to?
- Do we need to mark priorities? (can github projects help with ordering?)
- Do we want to mark difficulty or complexity to bugs and features making community contribution *easier*?
