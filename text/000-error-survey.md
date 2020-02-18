- Start Date: 2019-04-25
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

We want to survey error messages and see where we can improve them.

# Motivation

Part of starting, developing, and producing a Gatsby site is resolving errors. Gatsby’s error messages have inconsistent formatting and don’t always help people resolve the errors. We can improve them!

# Detailed design

## Constraints in error message surveying
We probably cannot create and maintain helpful solutions to all error messages; however, we can track the most common errors and improve those that will have the biggest impact on both user's goals and Gatsby's goals.

### Step 1: Survey errors
- Identify all errors
- Number the errors
- Name each error
- Give each error a grade

### Step 2: How to give an error a grade
0: Silent error
1: Throw Error and show changed state when it is resolved
2: Error message with id, name, and description and overwrite error listing when it is resolved
3: Error message with id, name, and description + Prompt for fix (can be URL to docs page, code example, example site, etc) and overwrite error listing when it is resolved
4: Error message  with id, name, and description + Prompt for fix + Adaptive Prompting and overwrite error listing when it is resolved
5: Same as 4 except error also shows in both terminal AND browser

### Step 3: Generate a daily report
- Most common errors and their grades
- Most common errors that don’t have a number, name, or grade

### Step 4: Improve the errors
- Prioritize tasks by most common errors with lowest grades
- For the list of prioritized tasks
  - Fix the reason the error is occurring so it happens less often or not at all, if possible
  - For errors that can't be eliminated from happening, improve the grade of the error by at least 1 grade (take a grade 0 error to grade 1, for example)

# Drawbacks
- implementation cost, both in term of code size and complexity

# Alternatives

We have looked through quite a few documents on error design and surveyed other CLI’s to report on their error messaging. [RFC CLI Redesign here](https://github.com/gatsbyjs/rfcs/pull/38/files)

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

Naming conventions for errors are TBD.
