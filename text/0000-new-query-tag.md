- Start Date: 2018-04-21
- RFC PR: 
- Gatsby Issue: 

# Summary

Create interpeted graphql queries by creating a new template literal tag.

# Basic example

This RFC introduces a new template literal tag `graphqltemplate`.
This can be used to let the query compiler know that this query needs to be interpeted before executing it as a graphql query.
For example:
```es6
export const query = graphqltemplate`
query data{
  allMarkdownRemark(
    ${ process.env.NODE_ENV === "production" ?  "filter:{frontmatter:{published:{eq:true}}}" : ""){
    edges {
        id
      }
    }
  }
}
`;
```

# Motivation

By introducing this new tag developers will get a new way to write graphql queries.
People now can write queries that change based on the build process.
As seen in the example this will allow for filtering when in production enviroment, splitting queries into parts.
But it will also allow fine control over how queries are build.
You can be sure that when you use a normal `graphql` tagged query only the query is executed and no part of it will be evaluated.

# Detailed design

This is the bulk of the RFC. Explain the design in enough detail for somebody
familiar with Gatsby to understand, and for somebody familiar with the
implementation to implement. This should get into specifics and corner-cases,
and include examples of how the feature is used. Any new terminology should be
defined here.

# Drawbacks

- The current implementation creates an AST to find graphql tags. The parsing of the AST will be more complicated with this new tag, it can cause dependencies troughout the code.
- Queries that change based on which contexts they are processed in can be confusing for end users
- It could be implemented by scripting and moving files around. Tough this will create more confusion for the end user and will create more hassle for the programmer.
- This RFC will create two different API's for graphql queries. It will increase complexity of writing those programs. And might cause confusion.


# Alternatives

Another way to do this is by interpeting queries tagged with the normal graphql tag that uses template parts. This will create an more unified API but it doesn't show the intent of the programmer to the reader of the code as clear. By introducing a new tag you clearly show that this special tagged query will be executed at buildtime.

# Adoption strategy

This RFC will introduce a new way to write queries, but doesn't change anything about old API's. It will recuer some documentation changes and maybe a new tutorial to notify developers about this new api. But will not break existing ways to write gatsby projects.

# Unresolved questions

## Naming

> There are two hard things in computer science: cache invalidation, naming things, and off-by-one errors. **Phil Karlton**

The name of the new tag is not what I like it to be but is all I could come up with at the time.
