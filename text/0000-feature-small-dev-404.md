- Start Date: 2019-02-13
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Add a configuration option to turn off page listing in Dev 404 page.

# Motivation

The current `Dev404Page` component pulls in a list of all pages on the local development site. We have experienced problems with large sites (over 100,000 pages) causing the 404 to either hang or take a long time to load.

# Detailed design

- Add a new config value for `gatsby-config` to disable the page listing
- Make the `Dev404Page` component skip rendering pages if this configuration option is off

# Drawbacks

This probably an issue with very few Gatsby users, so the biggest drawback might be implementation time vs. the edge case of users doing local development with huge sites.

# Alternatives

Implement a "custom 404" component that gives the developer complete control over the 404 page.

# Adoption strategy

This is not a breaking change, and it just requires a new configuration value and a little bit of documentation.

# How we teach this

Add a new section under [The Gatsby Config documentation page](https://www.gatsbyjs.org/docs/gatsby-config/#configuration-options).
