- Start Date: 2019-04-25
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

We want to survey error messages and see where we can improve them.

# Basic example

If the proposal involves a new or changed API, include a basic code example.
Omit this section if it's not applicable.

# Motivation

Part of starting, developing, and producing a Gatsby site is resolving errors. Gatsby’s error messages have inconsistent formatting and don’t always help people resolve the errors. We can improve them!

# Detailed design

## Constraints in error message surveying
We probably cannot create and maintain helpful solutions to all error messages; however, we can track the most common errors and improve those that will have the biggest impact on both user's goals and Gatsby's goals.

1. Survey
- Identify all errors
- Number the errors
- Name each error
- Give each error a grade
2. Generate a daily report
- Most common errors and their grades
- Most common errors that don’t have a number, name, or grade
3. Improve the errors
- Prioritize tasks by most common errors with lowest grades
- For the list of prioritized tasks
  - Fix the reason the error is occurring, if possible
  - For errors whose messages don’t point to the root cause, identify the root cause of the error and warn or error at the root cause
  - Improve error design, including consistent formatting

## Special note about data model
Beware of paying too much attention to the following numbers. This kind of data could seem negative and could be neutral because they could be part of learning:
- Increase in error messages
- No activity in a site
- Abandoning a project

This kind of data could seem positive but is actually neutral:
- Lots of activity in a site (it could be a problematic site or be multiple people working on the same site?
- Lots of hot reloading

# Drawbacks

[I need help with this section!]
Why should we *not* do this? Please consider:

- implementation cost, both in term of code size and complexity
- whether the proposed feature can be implemented in user space
- the impact on teaching people Gatsby
- integration of this feature with other existing and planned features
- cost of migrating existing Gatsby applications (is it a breaking change?)

There are tradeoffs to choosing any path. Attempt to identify them here.

# Alternatives

We have looked through quite a few documents on error design and surveyed other CLI’s to report on their error messaging. [RFC CLI Redesign here]()

The impact of not doing this is that Gatsby users continue to struggle to resolve errors with unhelpful messages and the Github repo serves as a sorting ground for issues dealing with known errors that could be fixed closer to their source.

# Adoption strategy

Gatsby users will not need to do anything differently to adopt this except update their version of Gatsby.

Is this a breaking change? Can we write a codemod? Should we coordinate with
other projects or libraries?

# How we teach this

We would publish an error design style guide and an error survey and improvement plan. 
The rest will be taught on the fly as developers run into errors and read the new messages. The goal is that they won’t have to do anything differently and will only be served with more helpful error messages (and sometimes, they won’t even run into an error if we fixed a bug).

# Unresolved questions

We aren’t sure how many errors are being generated, so we don’t know how many of the most common we can improve each day. We don’t have a baseline velocity to measure our performance against.
Naming conventions for errors are TBD
