Thanks everyone for checking this out. I've started on the code and am a happy to be the one to implement most/all of this. Thanks for the feedback all keep up the great work on gatsby.


- Start Date: 2018-09-28
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

I find it  tedious to have to do the same 3 steps every time I add a plugin to gatsby.

* `npm instal *plugin*`
* Check plugin docs for correct -`gatsby-config.js` syntax
* modify `gatsby-config.js`

I'm suggesting we create an additional command to add and remove plugins using the `gatsby-cli`.

# Basic example

A new work flow for installing a plugin could look something like this:

* `gatsby plugin add gatsby-transformer-x` (this would run npm install for the plugin and inject an initial config into `gatsby-config.js`)

* For plugins that don't require additional config we're done. If they do I at least have the basics that I can probably understand how to modify from a quick glance. ie. `path: /your/path/to/xyz.whatever` I know what this needs to point to and I can do that without checking docs.

* if needed then the dev can check docs.


A new work flow for removing a plugin could look like:
 
 * `gatsby plugin remove gatsby-transformer-x` (This would just remove the config for that plugin and remove the package using npm from the `package.json` and node_modules.)

# Motivation

The problem I'm trying to solve is simplifying and accelerating the gatsby work flow in relation to managing plugins over the course of development.

The major two use cases I can think to support would be installation and un-installation of plugins. Though I could see a potential use for disabling plugins (though commenting out code in the config isn't too difficult) and searching for plugins.

# Detailed design

## CLI Changes

`plugin [action] [plugin-name]`

### Actions

* add\* - Installs npm package, adds plugin to `gatsby-config.js`, warns if further config is needed. potential alias: install
* remove\* - Removes npm package form `node_modules` and `package.json` and removes config from `gatsby-config.js`. potential alias: uninstall
* disable - comments out config in `gatsby-config.js`.
* enable - un-comments config in `gatsby-config.js`.
* search - searches the [plugin database](https://www.gatsbyjs.org/plugins/) and returns relevant options


\*add/remove should/could be designed to handle gatsby plugin dependencies as well.

### Plugin-name

* **standard**: This should directly correspond to the npm package. 

* **non-standard**: For the sake of convenience it would be nice if I wanted to install (for example) `gatsby-plugin-netlify`, I could either type the full name or I could just type `plugin-netlify` or even `netlify` and the command will figure out what I mean. 

In the non-standard case  though there are potential for multiple plugin results. For example, if I typed just `netlify` [6 plugins](https://www.gatsbyjs.org/plugins/?=netlify) contain `netlify`. In this case it could return something saying "I'm not sure what you mean". Even better if it choose a top option I can say yes/no to or if it gave a list number 1-6 and asked me to enter the number of the one I want to install. This being said, this I'd make this a stretch goal, nice but not required.

## Config

Managing the config becomes the difficult part of this imo. For plugins that don't require any configuration it is easy to simply append and object containing the name. In the case of configurable plugins the cli has to get that "default" config from somewhere.

I have not spent a ton of time thinking of the *best* way to do this but my initial thought would be to add some kind of `default-config.json` in the root of plugins (alternatively maybe something that's part of the README.md). The absence or a parameter within could define whether further configuration is required beyond what is provided as well which is logged to console for the developers awareness on execution of `gatsby plugin`. For example:

    $ gatsby plugin add gatsby-plugin-netlify
    ...
    info: 'gatsby-plugin-netlify' successfully installed and configured.

    $ gatsby plugin add gatsby-plugin-netlify-cms
    ...
    info: 'gatsby-plugin-netlify-cms' successfully installed
    warning: further configuration required in `gatsby-config.js`
    info: see <https://www.gatsbyjs.org/packages/gatsby-plugin-netlify-cms/?=netlify> for more info on configuring this plugin.

# Drawbacks

- This should be a fairly simple implementation from a code perspective. The most difficult aspect would be updating plugins with a `default-config.json` to be compatible, but we could figure out a way to handle this for non-compliant repos that throws a warning to the dev that no default config was found so they'll have to deal with it manually and give them a link to docs.

- I think if anything this will simplify the dev process and take some tedium out of plugin management. It also won't eliminate the manual option for those already used to the current method.

- I'm assuming the search functionality would require the plugins page on the site to have an API that can be live queried. I'm not sure what this would require, but as stated earlier I think search can be a stretch goal.

# Alternatives

I haven't considered any other design. Not doing this just means more copy pasting and tabbing to a browser to reference docs.

# Adoption strategy

We would need to update documentation and make announcements that this is now an available feature. "official" gatsby plugins can be updated to contain default configs along side this pr. 

Announcements and docs can also be made for plugin maintainers on how to update their plugins. 

This will not be a breaking change.

# How we teach this

Gatsby and the cli are about simplifying through abstraction, I think this is a great continuation of that model which continues to enhance and accelerate the dev experience.

The Gatsby documentation around the CLI would have to be extended

Yes, it could change how we teach developers but because it's a cli option its not a required skill, devs could continue to function using `npm install` and manual config changes.

This can be taught as streamlined approach to plugin management. 

# Unresolved questions

I'm open for suggestions here, I modeled this after yarn/npm/apt-get.