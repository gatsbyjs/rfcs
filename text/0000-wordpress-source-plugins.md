- Start Date: 2018-06-08
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty) <!-- #5796? -->

# Summary

Brief explanation of the feature.

# Basic example

> If the proposal involves a new or changed API, include a basic code example.
Omit this section if it's not applicable.

As suggested by @m-allanson in https://github.com/gatsbyjs/gatsby/issues/5796#issue-330604409, configuring subplugins might look like this:

```js
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-wordpress`,
      options: {
        plugins: [
          {
            resolve: `gatsby-wordpress-woocommerce`,
            options: {
              // options here
            },
          },
          {
            resolve: "gatsby-wordpress-gravity-forms",
            options: {
              // more options
            },
          },
        ],
      },
    },
  ],
}
```

A subplugin reads the WordPress plugin’s REST API, and translates the output into something usable by GraphQL and Gatsby. This makes it possible for developers building with Gatsby to query access the WordPress plugin’s endpoints through GraphQL.

For example, after adding `gatsby-wordpress-gravity-forms`, `allWordpressGfForms` and `wordpressGfForms` are available to query.

# Motivation

> Why are we doing this? What use cases does it support? What is the expected
outcome?
> Please focus on explaining the motivation so that if this RFC is not accepted,
the motivation could be used to develop alternative solutions. In other words,
enumerate the constraints you are trying to solve without coupling them too
closely to the solution you have in mind.

- Headless WordPress is a great CMS for Gatsby
- The WordPress plugin ecosystem is huge, and extends WordPress with many useful features
- Some plugins, including some of the most used, are taking advantage of the WP REST API to expose plugin functionality. Ex. [Gravity Forms REST API](https://github.com/gravityforms/gravityformsrestapi) for comprehensive forms, [WooCommerce REST API](https://woocommerce.github.io/woocommerce-rest-api-docs/) for ecommerce systems

Supporting every available WordPress (WP) plugin within `gatsby-source-wordpress` is already becoming unwieldy, as we discussed adding support for a TK fifth plugin, Gravity Forms.

The existing list of plugins that gatsby-wordpress-source has built-in for is short, and it should stay that way. While a plugin like Advanced Custom Fields (ACF) is useful in almost any WordPress/Gatsby project, Gravity Forms probably isn’t. Even more obscure WordPress plugins could still be supported, but not everyone using `gatsby-wordpress-source` needs the wrappers for these WP plugins.

# Detailed design

> This is the bulk of the RFC. Explain the design in enough detail for somebody
familiar with Gatsby to understand, and for somebody familiar with the
implementation to implement. This should get into specifics and corner-cases,
and include examples of how the feature is used. Any new terminology should be
defined here.

## Terms

- _Subplugins_ – What I am considering calling `gatsby-source-wordpress` plugins, at least in this document (better than plugin plugins)
- WP – WordPress (note that the “P” in WordPress is uppercase, but within GraphQL queries it’s lowercase, ex. ``)
- _ACF_ - The Advanced Custom Fields WordPress plugin, already implemented directly into `gatsby-source-wordpress`

# Drawbacks

> Why should we *not* do this? Please consider:
>
> - implementation cost, both in term of code size and complexity
> - whether the proposed feature can be implemented in user space
> - the impact on teaching people Gatsby
> - integration of this feature with other existing and planned features
> - cost of migrating existing Gatsby applications (is it a breaking change?)
> There are tradeoffs to choosing any path. Attempt to identify them here.

Why should we *not* do this?

- Do this in WordPress (discussed in Alternatives)
- **Expectations of support.** This feature could potentially creates more expectation of support. Someone who has installed a plugin like Gravity Forms might need help configuring Gravity Forms WordPress plugin, sending the form back to WordPress from the front-end of the site, or actually getting the form data from WordPress into `gatsby-source-wordpress`, but only the final example is directly relevant to Gatsby. Ideally, some of this would be solved by documentation in each individual subplugin README.

# Alternatives

> What other designs have been considered? What is the impact of not doing this?

- **In theory, this could be fixed at the WP REST API level.** It may be possible to support any WordPress plugin with sufficient API hooks by re-writing its WP REST API implementation so that it works with GraphQL. In practice, this is completely impractical: for most WordPress plugins, this would mean maintaining an set of API endpoints just for GraphQL. Every WordPress we wanted to support would require a companion plugin to make this happen.

Finding the right balance for this is ongoing with ACF: a companion plugin is installed in WordPress to add support for the REST API, but it’s `gatsby-source-wordpress` that’s responsible for translating the dat


# Adoption strategy

> If we implement this proposal, how will existing Gatsby developers adopt it? Is
this a breaking change? Can we write a codemod? Should we coordinate with
other projects or libraries?

This approach gives us the opportunity to coordinate with the WordPress plugin community, without the requirement of doing so.

The WordPress community appears to be moving towards a similar goal:

> Take Every Opportunity to really beef up your JavaScript Chops
> <footer>—Mall Mullenweg, co-creator of WordPress</footer>

…as summarised by Wes Bos in [<cite>Learn JavaScript</cite>](https://wesbos.com/learn-javascript/)).

Gravity Forms team has written about on [Gutenberg](https://wordpress.org/plugins/gutenberg/) (WordPress’ re-build of their content editor in React, etc.):

> Not only is Gutenberg is changing the editing experience, but it'll also change how WordPress plugins are used and developed. … Instead of clicking the Add Form button above the editor to place a shortcode the page, a form can be placed just like any other content by simply adding a block.
> <footer>—Jeff Matson, Gravity Forms, [<cite>Gutenberg is Coming. Are You Ready?</cite>](https://www.gravityforms.com/gutenberg-coming/)</footer>

…you can see the community is going to be moving in our direction too.

TK Discussing what WordPress Gutenberg is in this RFC may be an unnecessary tangent. The point is that the Gutenberg editor / WP REST API is the way forward for WordPress core, and that approach works well with the kind of data Gatsby needs. WordPress plugins are and will continue to move to this approach, meaning more and more WordPress plugins could work with Gatsby with a subplugin wrapper like we are proposing here.

With this change, a WordPress plugin authors might opt to maintain their own subplugn as well, as it would provide a clear way of using their REST API implementation. If not, this approach still allows a member of the community who has a direct need for the plugin to maintain the subplugin, and can do so without needing to coordinate with the WordPress plugin author.

# How we teach this

> What names and terminology work best for these concepts and why? How is this
idea best presented? As a continuation of existing Gatsby patterns?
> Would the acceptance of this proposal mean the Gatsby documentation must be
re-organized or altered? Does it change how Gatsby is taught to new developers
at any level?
> How should this feature be taught to existing Gatsby developers?

# Unresolved questions

> Optional, but suggested for first drafts. What parts of the design are still
TBD?

- **Would this mean moving the existing plugins supported out of `gatsby-source-wordpress`?** Right now I think we should keep ACF support in `gatsby-source-wordpress`, but I can also see the case for moving all WordPress plugin…plugins out of it.
- **Should the plugins be responsible for re-formatting data for Gatsby when needed?** Often, the WP REST API returns nothing or `false` when you need it to return `null` for GraphQL. This also happens within the WP REST API implementations of each individual WordPress plugin. It can be solved with another WordPress plugin, but then you’d need a the original WP plugin, the companion WordPress plugin, and the companion Gatsby plugin to be able to support any WP plugins. Alternatively, the Gatsby plugin can it. I believe there is a proposal (from @pieh?) about mitigating this at a higher level for other Gatsby source plugins too, not sure if that would extend to this.
