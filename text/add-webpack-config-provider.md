- Start Date: 2020-04-18
- RFC PR:
- Gatsby Issue:

# Summary

As many gatsby recipes gets built those recipes might need to override the webpack config to support features like LESS support or may want to modify options to a loader to accomplish it.

# Basic example

```mdx
# Add Ant Deisgn

Installing necessary packages

<NPMPackage
  name={"atd"}
/>

<NPMPackage
  name={"less-loader"}
/>

Updating webpack config

<WebpackLoader test={/\.less$/} use={
  (loaders) => ([
    loaders.miniCssExtract(),
    loaders.css({ importLoaders: 1 }),
    loaders.postcss(),
    `less-loader`, 
  ])
} />

<WebpackPlugin 
  plugin={{
    __DEVELOPMENT__: stage === `develop` || stage === `develop-html`,
  }}
/>

---

# Motivation

This would help in building the recipes easier which require to update the webpack config by adding loaders, plugins etc.

# Detailed design

The design will be very similar to other providers and will be overriding the webpack config

# Drawbacks

- We need to update the API of the component whenever there are changes to the Webpack API 

Why should we *not* do this? Please consider:

- Could not think about any but love to hear if I missed anything here

# Alternatives

We can also just put up a single component saying 

```mdx
import config from './webpack.config';

<WebpackConfig config={config}>
```

With the above design the API of the provider does not change with API of webpack but it takes away the declarative nature of JSX.

# Adoption strategy

- We need to follow the same strategy as we do for other providers

# How we teach this

- With simple examples for building recipes
- Comparison doc between what config gets generated when we use a particular JSX tag

# Unresolved questions

- How far can we provide the same composability and flexiblity that we can provide through JSX that users are used to when writting webpack config in plain JavaScript.
