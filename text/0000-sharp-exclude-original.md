- Start Date: 2019-01-06
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Right now when you're using `gatsby-plugin-sharp` and `gatsby-transformer-sharp` for GraphQL image queries you always get the original image returned in your `srcset` no matter which maxWidth is set. I want to be able to exclude the original image. So you can decide if you want to deploy the original image (which would be the default) or not (new flag in GraphQL query).

# Basic example

``` JavaScript
// current usage
export const query = graphql`
  query {
    myImage: file(
      relativePath: { eq: "shop/myimage.jpg" }
    ) {
      childImageSharp {
        fluid(maxWidth: 800) {
          ...GatsbyImageSharpFluid_noBase64
        }
      }
    }
  }
`
```

``` HTML
<!-- Current output -->
<div class=" gatsby-image-wrapper" style="position: relative; overflow: hidden;"><div style="width: 100%; padding-bottom: 56.25%;"></div><div style="background-color: rgb(230, 186, 104); position: absolute; top: 0px; bottom: 0px; opacity: 0; transition-delay: 0.35s; right: 0px; left: 0px;"></div><picture><source srcset="/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-23bdc.jpg 200w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-9cd8b.jpg 400w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-10e5e.jpg 800w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-28f35.jpg 1200w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-cf760.jpg 1600w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-2f15f.jpg 2400w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-b980c.jpg 5760w" sizes="(max-width: 800px) 100vw, 800px"><img alt="alt text" src="/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-10e5e.jpg" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%; object-fit: cover; object-position: center; opacity: 1; transition: opacity 0.5s;"></picture><noscript>{…}</noscript></div>
```

# Motivation

The default behaviour would be fine for image galleries. But if you don't want to deploy the original image you would have to use an image editor to change the image to your desired max size. This feature would allow to use big images for building your Gatsby page but not deploying them.

# Detailed design

The design can be seen here: https://github.com/gatsbyjs/gatsby/compare/master...jpavlicek:master?expand=1
It is just a feature flag for your query and you can decide for each query if you want to exclude the original image.



``` JavaScript
// Desired usage
export const query = graphql`
  query {
    myImage: file(
      relativePath: { eq: "shop/myimage.jpg" }
    ) {
      childImageSharp {
        fluid(maxWidth: 800, excludeOriginal: false) {
          ...GatsbyImageSharpFluid_noBase64
        }
      }
    }
  }
`
```

``` HTML
<!-- Desired output -->
<div class=" gatsby-image-wrapper" style="position: relative; overflow: hidden;"><div style="width: 100%; padding-bottom: 56.25%;"></div><div style="background-color: rgb(230, 186, 104); position: absolute; top: 0px; bottom: 0px; opacity: 0; transition-delay: 0.35s; right: 0px; left: 0px;"></div><picture><source srcset="/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-23bdc.jpg 200w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-9cd8b.jpg 400w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-10e5e.jpg 800w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-28f35.jpg 1200w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-cf760.jpg 1600w,
/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-2f15f.jpg 2400w" sizes="(max-width: 800px) 100vw, 800px"><img alt="alt text" src="/static/myimage-1f3ba89ea0b61feb1c2c71bb56d83307-10e5e.jpg" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%; object-fit: cover; object-position: center; opacity: 1; transition: opacity 0.5s;"></picture><noscript>{…}</noscript></div>
```


# Drawbacks

Right now I don't see any big drawbacks. The complexity and implementation cost is low and it does not touch the current behaviour, it just extends it.

# Alternatives

I don't see any alternative besides extending the plugins `gatsby-plugin-sharp` and `gatsby-transformer-sharp`.

# Adoption strategy

Existing Gatsby users can adopt it via upgrading their plugins and setting the feature flag.

# How we teach this

`excludeOriginal` as name of the GraphQL flag. Perhaps `excludeSourceImage` could be a better fit.

The documentation would get a few new lines but it does not change how Gatsby is taught to new developers.

# Unresolved questions

Right now the unit tests for this new feature are missing.
