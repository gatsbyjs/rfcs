- Start Date: 2019-01-25
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Currently schema in Gatsby is created twice, before and after `createPages`. This is done so that custom context can be added to the pages. This RFCs suggest eliminating the second schema creation and putting context to a generic list structure instead.

# Basic example

Given a `createPages` callback that creates pages with context, eg:

```js
exports.createPages = ({ actions }) => {
  const { createPage } = actions
  for (let i = 0; i < 10; i++) {
    createPage({
      // Each page is required to have a `path` as well
      // as a template component. The `context` is
      // optional but is often necessary so the template
      // can query data specific to each page.
      path: `/${slug(i)}/`,
      component: slash(someTemplate),
      context: {
        id: i,
        someOtherField: 'foo'
      },
    })
  }

  for (let i = 0; i < 10; i++) {
    createPage({
      // Each page is required to have a `path` as well
      // as a template component. The `context` is
      // optional but is often necessary so the template
      // can query data specific to each page.
      path: `/${slug(i)}/`,
      component: slash(someOtherTemplate),
      context: {
        id: i,
        someOtherField2: 'foo'
      },
    })
  }
}

```

One can query all pages with context as follows:

```graphql
{
  allSitePage {
    edges {
      node {
        context {
          id
          someOtherField
          someOtherField2
        }
      }
    }
  }
}
```

The proposal would mean that the pages with context are to be queried as follows:

```graphql
{
  allSitePage {
    edges {
      node {
        context {
          key
          value
        }
      }
    }
  }
}
```

# Motivation

The gatsby build structure can be divided into 4 steps.

1. Collect nodes
2. Create schema
3. Generate pages
4. Render pages

Currently 3) can feed into 2) by defining additional page context. This means that we have a "cycle" in our logic, with pages optionally affecting the schema. Not only it's confusing directly (schema before 3 and after 3 is different), it also causes weird naming issues (all things being `someGeneratedType_2` because we generated it twice). In addition this makes doing incremental and/or distributed builds harder, because schema is tied to page generation. Thus ideally we would have a directly data flow, with pages not affecting the schema. (well technically we have pages feed into the nodes when they are created, but we can consider it to be a special case).

# Detailed design

We change `context` field on `SitePage` to be of type `[PageContext!]!`.

```
type PageContext {
  key: String!
  value: JSON
}
```

We then remove the second schema recreation.

# Drawbacks

* It is a breaking change
* Having value as `JSON` means less type safety
* Handling list might be harder than handling an object on client side

# Alternatives

Instead of replacing the API as above, we could make context uninferable, but allow defining context type using `addTypeDefs` (when it ships). This is still a breaking change, but keeps the GraphQL queries the same, as long as type def is added. This will solve the JSON value type safety issue.

In addition, with explicit typeDefs, context can be a Union, which will further increase type safety.

# Adoption strategy

It's doubtful the codemod is possible. However, GraphQL schema will provide helpful errors so that update is as painless as possible. It might be possible to add additional errors related to this to make it simpler for people to find.

# How we teach this

This is a relatively minor change, but is still a breaking one. It's unknown how many people actually query for page context (as opposed to just querying for pages). A blog post and updated documentation is a must. This would be part of major upgrade, so breaking changes are expected.
