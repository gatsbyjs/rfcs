- Start Date: 2018-08-07
- RFC PR: n/a
- Gatsby Issue: n/a

# Summary

This RFC proposes an addition to native GraphQL sources, so that they're able to use configured plugins. 
It leverages schema DSL and schema directives to annotate fields of a third-party schema as an indicator for extended functionality.

# Basic example

```js
module.exports = {
  plugins: [
    'gatsby-transformer-remark',
    {
      resolve: 'gatsby-source-graphql',
      options: {
        typeName: "Contentful",
        fieldName: "contentful",
        url: `https://cdn.contentful.com/spaces/abc/graphql/alpha`,
        headers: {
          Authorization: `Bearer 123`,
        }
      }
    },
    {
      resolve: 'gatsby-transformer-directives',
      options: {
        schemas: {
          Contentful: graphql`
            type BlogPost {
              body: String @internal(mediaType: 'text/markdown')
            }
          `
        }
      }
    }
  ],
}
```

# Motivation

Gatsby's plugin system fueled the adoption of the project by providing data transformations like image sizes und querying Markdown as HTML. 
But whenever we add a third-party schema we opt-out of this system. This ultimately could yield a "REST is superior to GraphQL" mindset, and embraces source specific plugins over generic GraphQL.

Enriching your types manually is just a first step - it would enable type specific plugins (e.g. all `DateTime` scalars are automatically format-able via `moment`) and all of this should work without the remote GraphQL server providing this functionality.

# Detailed design

## Changes to `Node`

If you haven't read the `gatsby-source-graphql` RFC, head there first.

Its implementation introduces a new kind of nodes, further called "ignored nodes". The behavior differs in that they are not exposed directly as root level nodes, so that the following query is not possible:

```graphql
query {
  allGraphQLSource {
    ...
  }
}
```

This mechanism needs to further be expanded, so that a hidden sub-graph of nodes can be created. 
Every directive attached to an entity in GraphQL has full control of an "ignored node", created for this very instance. These nodes are kind of "shadow nodes" in the sense that:

1. they should not be queryable directly, as they are strict ancestors of already existing nodes (in the third party schema)
2. as an analogy: in DOM terminology a "shadow root" is an encapsulated part of a DOM

As hooks like `onCreateNode` as well as `setFieldsOnGraphQLNodeType` have to work, a full node lifecycle should be maintained, only the root-level connections shouldn't be established. If you visualize the graph, these nodes would "float around" without any links to other nodes. 
This is due to the fact that third-party schemas are only merged at the very end. 

Each directive is responsible for implementing a node instance that fullfills the needs of the plugins it intends to allow to be attached.

Example: the `gatsby-transformer-remark` attaches itself only to nodes with `internal.mediaType` being a Markdown flavor like `text/markdown` and expects its content to be in `internal.content`. 
So a plugin to mark a `String` field as Markdown would implement a node instance that fullfills these requirements.

## Changes to schema merging

Another change is how the Gatsby-created schema gets merged. Currently it's simply merged without further transforms applied. This works, because Gatsby's GraphQL source creates an empty node and everything further down the query is handled by the third-party schema.

As we now can have custom nodes at any nesting level, a schema transform/delegation needs to happen, so that an implicit link from one type in Gatsby schema to one in the third-party schema - via a **resolver**.

`graphql-tools` schema transforms make it easy to mark dependencies (in our example: if you query `externalFieldNameMarkdown`, it needs to fetch `externalFieldName` to be able to pass the content to the resolvers `gatsby-transform-remark` created) and delegate between the schemas. 

# Drawbacks

Depending on the implementation of the "shadow graph": the implementation costs could be quite high (for an end-of-beta state of product), if it's done in a clean way. A clear definition of "ignored nodes" should be created and its separation in code shall be extensible and explicit.

On the other hand, a quicker implementation with less core changes reduces the risk of unwanted compatibility issues.

In general the RFC aims to bring functionality to third-party schemas that were available in current plugin ecosystem. Depending on the directive implementation the details might slightly differ to what customers are used to, thus being a "perceived breaking change". 

# Alternatives

The other alternative would be directives that more or less copy the behavior of existing plugins. This would introduce code duplication, maintenance burden and slower adoption of native third-party schemas. 
Not doing any of both would harm the Gatsby ecosystem, as the GraphQL source would suffer significant functionality to it's REST/plugin couterparts.

# Adoption strategy

In best case plugin owners specify their dependencies on the `Node` interface in a common format. This way directive creators will have an easier job implementing it.

The implementation of this RFC is intended to introduce no breaking changes to existing Gatsby packages. 
Though it might be necessary to collaborate with plugin creators, on how to enable lazy evaluation of the nodes content. For example the remark plugin does the `frontmatter` magic on node creation time, rather than on resolution time. This requires the directive to take care of it while constructing the node.

# How we teach this

A detailed explanation of "schema directives" will help understanding the pattern. Apollo resources could be linked for more dev savy people.

The terminology "directive" comes from GraphQL itself.

The internal terminology for "shadow", "hidden", etc. is open for discussion to have consistent semantics in those words.

# Unresolved questions

- [ ] How should the hidden layer be implemented?
  - filtering / diffing with `internal.nodeType === 'internal`
  - adjust lifecycle of schema creation
- [ ] Should gatsby provide a "Directives API" on top, or expose ecosystem implementations directly? (i.e. `graphql-tools`)
