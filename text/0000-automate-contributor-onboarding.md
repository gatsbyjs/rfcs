- Start Date: 2018-04-18
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Encourage new contributors to become repeat contributors by automatically inviting them to a _Gatsby Maintainers_ GitHub team. Gatsby has over 700 contributors, but the majority of them have made one or two commits.

This RFC proposes adding automated contributor onboarding, along with clear 'new contributor' documentation. Automate contributor onboarding with the following steps:

* Provide write access to [the main gatsby repository](https://github.com/gatsbyjs/gatsby) to anyone who has a Pull Request accepted
* Provide clear instructions about the expectations for contributors
* Provide clear 'next step' documentation for contributors

This RFC is heavily based on the Styled Components onboarding flow and a discussion with @theadactyl a few weeks ago. Read the following before continuing:

* The Styled Components [CONTRIBUTING.md](https://github.com/styled-components/styled-components/blob/2274c05aedba5fef806ac3983674ef9542117a77/CONTRIBUTING.md)
* An example of the [Styled Components onboarding message](https://github.com/styled-components/styled-components/pull/1662#issuecomment-379713256)

# Motivation

Gatsby is a large popular project, there's more work available than there are people to do it. In order to make Gatsby sustainable over the long term, we should do everything possible to encourage repeat contributors.

This should help create a sense of ownership amongst contributors, strengthening their relationship with the project.

The onboarding docs will list some 'low risk' ways to contribute to the project - like labeling and closing issues, merging pull requests, picking up `help wanted` issues - providing a clear next step after having their first Pull Request accepted.

Note that being added to the _Gatsby Maintainers_ team is completely opt-in. People should never feel guilty for not contributing to open source.

# Detailed design

* Rename the _Gatsby core maintainers_ team to _Gatsby Maintainers_
* Review existing team permissions to ensure that new contributors can't be overly destructive
* Setup a bot like the [Probot invite-contributors bot](https://probot.github.io/apps/invite-contributors/), or review existing bot functionality
* Setup a bot to post ['welcome, new contributor' messages](https://github.com/styled-components/styled-components/pull/1662#issuecomment-379713256) on PRs
* Write additional 'new contributor' documentation
* Update rules for ['required reviews'](https://help.github.com/articles/about-required-reviews-for-pull-requests/)
* Create a new 'Core' team with more permissions. Contributors can be manually invited to this team when needed.

Regarding 'required reviews', GitHub can be configured to ensure that all PRs have a number of accepted reviews before being mergeable. e.g.

* one approved review from a Core team member OR
* two approved reviews from Contributing team members

# Drawbacks

There's a risk of attracting 'destructive' contributors. This should be contained by sensible limits on contributor permissions. Destructive contributors can be kicked out and their contributions reverted. In the case of repeated destructive contributors this RFC can be reverted.


# Adoption strategy

Adoption wil be automated and opt-in. Once implemented this could be written about in Gatsby's newsletter, blog or Twitter account.

# How we teach this

This can be taught with documentation updates and automated comments posted on Pull Requests.

# Unresolved questions

@KyleAMathews mentioned there's a bot to invite contributors already. I'm not sure if it's running at the moment? Investigate.

Decide on which bots to use - Probot is currently used for checking for stale commits, so that seems like the first thing to try. [Danger JS](http://danger.systems/js/) is currently configured for the Gatsby repo, maybe this could be used.


