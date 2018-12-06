- Start Date: 2018-12-05
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Add a command to Gatsby that serves the site data as a public GraphQL API.

The core functionality is already existing, in the form of the internal
`data-exporer` command. If we could allow the user to take that API and serve it
publicly, they would be able to query their site dynamically with exactly the
same schema they use for their static queries, and with minimal additional
configuration.

# Basic example

See [demo repository](https://github.com/jgierer12/gatsby-api-proposal-demo).
`server.js` contains the functionality that this new feature will provide
natively. `src/pages/index.js` contains an example of dynamically querying
against the API.

# Motivation

This enables many use cases. For example, complex sorting or filtering like in
the [demo project](https://gatsby-api-proposal-demo.netlify.com/). It also
allows implementing search functionalities without relying on a third-party SaaS
and managing its index. Other use cases are (extremely basic) password-protected
content (`filter: { password: { eq: ${password} } }`), filtering based on time
or location, dynamic pagination and probably many more.

It can even be used as a source for a different Gatsby site via
gatsby-source-graphql!

Another big opportunity, as
[pointed out](https://github.com/gatsbyjs/rfcs/pull/22#issuecomment-444414605)
by [@sedubois](https://github.com/sedubois), is using Gatsby as a middleware to
unify multiple data sources into a single, consistent GraphQL API and using that
as a content source for different front ends (e.g. an iOS app, an Android app,
and a Gatsby site all get their content from the same Gatsby API).

# Detailed design

The Gatsby API server is entirely optional and additional to the existing static
build.

## User Interface

The server can be started by a new command &mdash; `gatsby api-server`. To
enable advanced configuration and setups, as well as support serverless PaaS
like [Zeit Now](https://zeit.co/now), it is also exported as a function from
`gatsby`:

```js
import { apiServer } from "gatsby";

apiServer({ ...options });
```

Port and host can be configured via an `options` object passed
to `apiServer`, or CLI options:

```sh
gatsby api-server --port 1234 --host 1.2.3.4
```

There should also be sensible defaults, such as:

```js
apiServer({
  port: `process.env.PORT || 8080`,
  host: `0.0.0.0`
});
```

There will be opt-in CORS support. The JavaScript API also supports passing
through [express CORS middleware](https://github.com/expressjs/cors) options
instead of a boolean to this option:

```js
apiServer({ cors: false }); // default: don't allow cross-origin requests
apiServer({ cors: true }); // allow all cross-origin requests
apiServer({ cors: { origin: `https://gatsbyjs.org` } }); // allow cross-origin requests from https://gatsbyjs.org
```

## Implementation

The API server functionality is already existing in
[`packages/gatsby/src/commands/data-explorer.js`](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby/src/commands/data-explorer.js).
This file has to be modified to add CORS support via `express`'
[`cors`](https://www.npmjs.com/package/cors) package. It also needs fallback
values for `host` and `port`.

This script can then be used to build a command in the `gatsby-cli` package,
similar to how existing commands are created.

For the JavaScript API, an additional helper function has to be created, which
emulates the `program` object similar to
[`scripts/www-data-explorer.js`](https://github.com/gatsbyjs/gatsby/blob/master/scripts/www-data-explorer.js).
If the function is imported in one of the site source files processed by Gatsby,
a warning or error should be thrown since it must only be used in server-side
code.
This function should be exported as a named export by the `gatsby` package.

# Drawbacks

- It may add confusion as to whether Gatsby is still a static site generator
  when people hear that it now also features an API server.
- Similarly, the difference between static and dynamic queries and what is and
  isn't supported between them might be confusing.
- Unless we also build an 'official' GraphQL client, the wide spectrum of
  available clients can be both overwhelming for users as well as harder to
  maintain and support for the Gatsby team.
- The same limitations of the development GraphQL schema also apply to this,
  namely that it doesn't support fragments provided by plugins yet (popular
  example: `gatsby-image`'s numerous fragments).

# Alternatives

Much of this functionality does not necessarily need to live in Gatsby core. It
might be better to only integrate CORS support into `data-explorer.js` and leave
the rest of the implementation to a separate package.

# Adoption strategy

This would be an entirely additive feature, so there are no breaking changes.

If developers want to integrate dynamic queries into their app, they need to
choose a GraphQL client (such as `graphql-request` or `apollo-client`) and use
it to query for their dynamic data. They also need to set up a host and deploy
the API server.

# How we teach this

A large part of the work on this will be documentation. We need to teach about
what the API is, how to set it up and deploy it, how to use it in Gatsby sites
and how to use it as a middleware for other types of apps. I imagine this will
result in an entire new "Guides" section, containing at least 5 articles.

It is especially important to teach the difference between static and dynamic
queries, their limitations, and when to use one or the other.

# Unresolved questions

- Should we implement any authentication, and if so, how?
