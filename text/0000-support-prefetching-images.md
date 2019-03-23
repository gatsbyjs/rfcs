- Start Date: (fill me in with today's date, YYYY-MM-DD)
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

Web sites should load quickly and smoothly. Quick meaning pixels appear on the
screen as quickly as physics will allow. Smooth meaning as many page elements
as possible load together and later arriving elements (e.g. images/fonts) load
in a way to avoid dramatic changes to layout (jank).

Gatsby already does a great deal to help with this — we encourage serving sites
as static assets from CDNs, we inline CSS, we leverage advanced image loading
techniques so the smallest possible images are loaded and their positions are
held during loading so the layout doesn't jump as images come in.

But there's more we can do. One common source of visual jank is when visiting
pages in Gatsby sites with large above-the-fold images as the surrounding page
is rendered immediately but since Gatsby and the browser doesn't know about the
image until the page is rendered, the image always loads 1/2 second+ later.

This RFC proposes a method for prefetching critical images for pages the user is
likely to visit to ensure that as often as possible, a page can render with
its critical images already loaded.

# Basic example

This would require _no_ user-facing changes to gatsby-image.

A [`critical` prop was added last
year](https://github.com/gatsbyjs/gatsby/pull/7083) to tell gatsby-image not to
lazy load an image — generally one above the fold. This is the same semantics
this RFC needs.

So when creating a page with a large background image writing it as `<Img fluid={...backgroundImage.fluid} crticial />`
would both opt it into immediate loading but also prefetching.

# Motivation

Gatsby sites should be quick and smooth and we should automate this whenever
it is possible. Prefetching images will go a long ways towards creating
the perception that Gatsby sites are quick and smooth.

# Detailed design

Gatsby already includes a mechnism for prefetching resources for a page.
We prefetch code and data and ensure they're loaded before rendering a
page. The code for doing this is generic and we could easily extend it
to support prefetching images.

There's two steps to doing this.

1) collect which images need prefetched

To do this we will create a server version of gatsby-image that uses
react-side-effects to capture on each page render the critical images for that
page.

This will included in the page metadata alongside the code/data files as
a required resource.

Other React components that use react-side-effects like react-helmet & emotion
require a Gatsby plugin to power similar sorts of server extraction of data.

To make this simple to start adopting and since gatsby-image is an official
part of the Gatsby framework, we'll add support directly into Gatsby
for extracting the gatsby-image side effects.

2) prefetch the image file

gatsby-image leverages a newer browser API `srcset` which lets you pass
a list of image files with hints to the browser as to which file should be used.
The browser then decides which file is best based on its pixel density, screen
width, etc.

This presents a problem for prefetching as we're not the browser and can't look
at a `srcset` and know which image the browser will download.

If we prefetch the wrong file, that'll be a wasted work.

Luckily https://scottjehl.github.io/picturefill/ has already implemented this
algorithm for us so we can extract that and use it to figure out which image to
prefetch — whether the site is being shown in a mobile or desktop computer.

We wouldn't want to load the prefetching algorithm if a site isn't using
critical images so we'd async load the module on demand.

Unlike with code & data, we won't block rendering the page until the image
loads. This could cause trouble if the image takes a long time to load.

When Suspense is ready, we'll have a lot more control over loading behaviors
and can let users specify as a prop to gatsby-image how long they want to wait
for the image to load before rendering.

# Drawbacks

There's not a lot. Implementation cost should be minimal as we're following
established Gatsby patterns and borrowing from a well-used open source project.

This again also doesn't require any user changes to take advantage of the
behavior. Sites using the `critical` prop on gatsby-image will just get faster.

# Alternatives

Suspense would solve this but it isn't ready yet :-P

Not having this is causing more visual jank which hurts Gatsby's appeal
for image-heavy websites.

# Adoption strategy

We'd need to promote the `critical` prop more. It's not clear how many people
are using it or know about it.

One way to promote it will be to create a demo website that shows the effects
of using `critical` and not alongside a blog post.

# How we teach this

The `critical` prop is already explained in the gatsby-image docs. We'll want
to revisit that to explain prefetching. That and the above mentioned blog
post and demo site will effectively teach the concept.

# Unresolved questions

Optional, but suggested for first drafts. What parts of the design are still
TBD?
