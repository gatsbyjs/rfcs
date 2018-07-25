- Start Date: 2018-07-24
- RFC PR: 
- Gatsby Issue: 

# Summary

As a follow-up to the [Site Showcase](https://next.gatsbyjs.org/showcase/), we want to create individual & organization pages highlighting the individual community members and what they've built. 

We also want to create a free Job Board highlighting opportunities for employment building Gatsby websites at agencies and companies using Gatsby!

# Motivation

Some high-level motivations include:

* People are wanting to hire programmers who are skilled at using Gatsby already
* People are wanting to have someone build a Gatsby site for them
* Many individuals/agencies/companies are producing so much valuable OSS work and many beautiful websites and it'd be useful for them to market themselves to the audience that already wants them

Some goals for the different components include:

#### Individuals Pages

This will help highlight contributions made by community members (plugins built, code PRs) & give community members "official" landing pages to help community members in their personal website marketing efforts and allow companies interested in building Gatsby sites to find individual.

Users who visit this page should be able to:

* see what sites the individual has built on Gatsby and visit those sites / see details about them (probably link to the site showcase)
* hire the person (if available)
* see whether the person is open for Gatsby projects or not
* find a link to the person's portfolio / outside website

#### Agency Pages

This will help agencies hire for Gatsby-related positions, give the community a place to look for Gatsby-related jobs, and see high-quality Gatsby agencies. Give agencies "official" landing pages to help them in their website marketing efforts.

Users who visit this page should be able to:

* see what sites the agency has built on Gatsby and visit those sites / see details about them (probably link to the site showcase)
* get in touch with the agency
* see job postings the agency is putting out.

#### Company Pages

A list of companies using Gatsby will help demonstrate that it's "enterprise-ready" & show the wide variety of use-cases Gatsby is in.

Users who visit this page should be able to:

* Display list of companies using Gatsby to demonstrate it's "enterprise-ready" and fulfills whatever use-case they're looking for (e-commerce, authentication, etc). 
* Help companies hire for Gatsby-related positions by surfacing their postings to the community; give the community a place to look for Gatsby-related jobs.

# Detailed design

## Pages

#### All people / agencies / companies

One template with multiple URLs, each of which apply different filters:
  * /people/ (all people)
  * /agencies/ (all agencies)
  * /companies/ (all companies)

Filter buttons should be at the top and should be easy to toggle between them (select-one). Filters should include:
  * Individual 
  * Agency 
  * Company
  * Open to new contracts (people & agencies with this field toggled)
  * Now hiring (agencies & companies with this field toggled)

#### Person / agency / company page

One template with multiple URL patterns, depending on the Creator type:
  * /companies/[company-name]
  * /agencies/[agency-name]
  * /people/[person-name]


### Data Structures

Showcase sites ([listed here](https://github.com/gatsbyjs/gatsby/blob/master/docs/sites.yml)) can have multiple Creators.

**Creators** can either be an Individual, a Digital Agency, or a Company. . 

Creator has the following fields:
* an id, in slug format (eg mike-allanson)
* type -- Individual, a Digital Agency, or a Company. We use the word Organization below to refer to either a Digital Agency and Company.
* a description of them in the third person “Eg, Mike Allanson is a developer living in London"
* Personal website
* GH handle (?)
* Open for Work [boolean]
* A photo / logo (depending on Individual / Organization type)
* Org they are affiliated with (if Individual)
* People they are affiliated with (if Org)
* A list of open jobs (if they are Organization type)
* A list of all the sites they’ve built.
* A location with:
    * A description (“London, UK”)
    * Lat / lng

_Data Format_: all creators should live in a creators.yml file, similar to sites.yml.

**Jobs** have the following fields:
* Title
* Location
* Organization (FK)
* (Optional) salary low end
* (Optional) salary high end
* (Optional) salary currency
* Contact email or application link. 
* Excerpt (short version)
* Description

_Data Format_: Jobs should be stored in markdown files in a /jobs folder. Jobs should be stored in markdown files. Frontmatter should have all fields except description. Body of the file should be the description. 

### Notes on long-term data strategy
* Right now, the data is in YAML in the GitHub repo. At some point, we may move all of our data to a format with a UI making it easier for people to submit & edit their own data, without imposing a review burden on Gatsby maintainers
* Option 1: use Airtable or a CMS
    * Airtable doesn’t have an easy way to allow users to edit individual forms in a record. 
    * We could build something on top with Lambdas, or utilize the Zapier => Airtable integration: https://support.airtable.com/hc/en-us/articles/115007655568-Using-forms-to-edit-tables-with-Zapier
    * We could also make job postings go down after 60 days
* Option 2: Build an application ourselves
    * Could do this, but what’s the value here?
    * We probably don’t want to spin up an application to do this
    * Firebase as backend?
* **Option 3: Stick with YAML in Github for now (what we’re doing)**
    * We know this works, although it does add additional maintainer burden reviewing these files
    * If we find a good global “application” type solution, we probably want to migrate the sites in the site showcase here as well. 
    * **Almost certainly the right move for now.**
* Option 4: Use seeker.company or another TBD service
    * Upside: seeker.company provides a UI to allow people to create job postings, and a job board to list those postings. 
    * Downside: No control over look and feel, not built with Gatsby, no way to customize submissions or link to organizations, no way to edit postings.
# Drawbacks

Why should we *not* do this? Please consider:

- Time & effort
- It will add more pages to .org to maintain
- Maintainers will be getting PRs adding additional agencies / sites / companies
- We could get spam submissions that degrade the site.

# Alternatives

We could use 3rd-party services to build these pages (eg jobs board) & maybe individual and community pages. They would not be sufficiently customizable and would look weird (are we a website building tool or not!?)

# Adoption strategy

No adoption necessary, although it will be more _impactful_ if people / agencies / companies use and share their dedicated pages.

# Unresolved questions

Page design, types of filters we will want, especially how to cleverly handle filtering with the URL.

Any additional fields we might want.
