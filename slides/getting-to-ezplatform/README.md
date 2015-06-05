# Introducing eZ Platform
### What is eZ Platform, new, next?

*[Symfony Meetup Oslo - 2<small><sup>nd</sup></small> of June 2015](http://www.meetup.com/eZ-Oslo-Meetup/events/222305799/)*

<small><br>
<br>By [André Rømcke](https://github.com/andrerom) / [@andrerom](http://twitter.com/andrerom)

---

# About [eZ Systems AS](http://ez.no)

- Founded in 1999
 - Focus on Content management for 15 years
- Offices in Cologne,  Katowice, Lisbon, Lyon, New York, Oslo, Paris,  Skien, Tokyo
- 45,000+ members in eZ Community
- 300,000+ installations
- 500+ Enterprise customers
 - MadSack, QuickSilver, RedBull, Oslo Stock Exchange, ..
- 200+ partners worldwide

<br>

---

# eZ Platform/Studio?


--

## Background

`eZ Platform` is a next generation `eZ Publish`; *A feature rich, highly extensible, enterprise, open
source CMS/CMF solution.*

<br>
It will also be available as part of `eZ Studio`, a commercial solution that extends eZ Platform with features for
editorial teams, and organizations.


--

# Time-line

*2011*: Work started on building eZ Repository (API's)

*2012*: Adopt Symfony2 & start build Platform stack on top

*2012*: eZ Publish 5.0 (Platform + Repository + 4.x "legacy)

*2013-2014*: Develop Platform towards being standalone

*2015*: Launch eZ Platform & eZ Studio


<!--

![eZ Repository > Platform > Studio](ez_layers.png)
-->

--

## 5.x migration?


Migration from eZ Publish 5.x is built in, data migration is fully\* supported, and any\** code written against Platform stack will still work.


<small>
\* Depends on FieldTypes used, but even unsupported can be used with `NullType`.<br>
\** With exception of deprecated features / API's.<br>
For more details on BC and best practice migrations [see slides](http://lolautruche.github.io/ez/bc.html#/) from eZ Days Oslo 2014</small>

<!--

## Concepts

TODO: split out concepts (same as eZ Publish) and non-functional features (improvements)

eZ Platform aims to improve on concepts in eZ Publish, and non functional features like:

- Scalability - _Future DB to support far beyond 2 million objects_
- Extensibility - _Simple use cases simple, everything else possible_
- Multi channel - _Content can be used in _
- Modern *content* framework - _For devs, editors, designers_

-->

---

# Concepts

*Concepts in eZ Platform goes beyond those that existed in eZ Publish*

<aside class="notes">
    If audience is familiar with eZ Publish this can be skipped or gone true quickly.
</aside>

--

## "Everything" is Extensible

Provides several extension points, **some**:

- Extend Content model with new _FieldTypes_
- _Override_ the view of a given *Content Type*
 - template, *for pure view needs*
 - Content/Location ViewController, *for logic needs*
- Listen to API _Signals_ for micro workflows
- ...
- Future: Native indexing of external documents, ..

_+ Extension points available in Bundles we use_

<aside class="notes">
    "Everything" refers to the fact that eZ Platform aims to provide a lot of extension points out
    of the box, and what is not provided is still possible.
</aside>

--

## "Everything" is Content

- You model your Content in different kind of *Content Types*
 - _Article, Blog, Page, Forum post, Movie, Customer.._
- Each *Content Type* is modeled with *Fields* (attributes)
 - Pick among 27+ built-in _FieldTypes_
 - Or growing number of community *FieldTypes*
 - Or write your own using solid interfaces
- *Future:* Domain Types, Field Traits, Field T. Permissions, ..


<aside class="notes">
"Everything" refers to the fact that the content model can be used for anything, but best suited
for anything editorial, which benefits from versioning and fine grained permission support

Data which is very frequently updated or very active user generated content is use cases we
usually recommend to use custom data structures for within using standard Symfony practises.
</aside>

--

## Content is reusable

- Stored in a decoupled & semantic format
 - No HTML tagsoup in your database
 - Better security on what is allowed
 - Better control of styling your content
- Cable of targeting any device & any _channel_
 - _Desktop, mobile, tv, print, ..._
- *Coming:* Rich Text in docbook (XML) w/ transformation to/from html5, ..


<aside class="notes">
This point depends on implementation of FieldTypes, but everyone we bundle with eZ Platform
aims to allow for content reuse and responsive approaches.
</aside>

--

## Multi site, Multi Repository

By using a concept called _SiteAccess_ and _SiteGroups_:

- Several variants of the same web site
 - languages
 - designs
 - channels *(web, tv, mobile, app, ..)*
- Several web sites in same repository for content reuse
- Several repositories (databases) with full separation

--

## Full i18n support

- Content support up to 62 languages
- System has a advance language fallback system
 - Possible to fallback to english on french site if you want
 - Possible to fallback to fre-FR on fre-CA site
 - ...
- *Coming:* All Repository properties translatable

--

## Advance content permission

Fine grained permission system

- Users
- User Groups
- Roles Assignable to:
 - Users _and_ User Groups
 - Optionally with _Limitation_
- Roles consists of Policies
 - Optionally limited by _Limitation**s**_
- *Also:* Possible to extend with own Limitations *(logic)*

<small>*Permissions out of the box used for Varying HTTP Cache.*</small>



---

# Layers of eZ Platform/Studio

High level representation of the layers.


--

## [Symfony](http://fabien.potencier.org/article/49/what-is-symfony2)

- Modern _Components_ with very high code standard
 - Makes the developer a better programmer as it uses many enterprise code patterns
 - Highly inspired by the good parts of Spring (Java) Framework, adapted for the strengths of PHP
- It has <small>(in PHP)</small> a unique approach to <small>(HTTP)</small>Cache
 - A HTTP request/response framework
 - Splits page elements into sub requests, *allowing ESI use*


Sum: Solid base for eZ Platform and it's users.

*And then there is Bundles..*

--

## Platform Core/Kernel

On top of Symfony _Full stack_. As opposed to just
_Components_\* it enables use of all Symfony _Bundles_.

Notable bundles used in eZ Platform:

- FosHTTPCache - _Vary by user context hash, purge by Location id_
- DoctrineDBAL - _Support for most SQL Databases_
- Stash - _Data cache on disk, memcached, redis, .._
- FlySystem - _Binary files on disk, NFS, S3, .._
- _...and many more_

<br>
<small>_\* What __Drupal8__ will use, coming later in 2015_</small>

--

## Platform UI

Provides a modern backend UI for Developers and Admins to manage the installation and it's content.

Currently highly work in progress but can be tested in current alpha's.


--

## Studio UI

Extends eZ Platform to provide features for editor teams.

*Some planned features:*
- Landing page management
- Flex-workflow

More info and pre releases expected this summer.

---


# What's "new"?

What's new in the last few months.



--

## Platform UI

- Content CRUD (Edit, Move, Delete, Copy)
- Content Type (Group) management [WIP]
- Section management
- ...

Most Field Types already supported, more coming

<aside class="notes">
  Demo time?

  UI is main focus in this release, and a lot of work has gone into making a
  editing experience far better then in the past.
</aside>

--

## Platform Core


- Repository Forms built upon Symfony Forms
 - Used for Content Type editing
- Content download
- Image alias purging
- Solr search engine *(WIP)*
- Profiling [efforts](https://github.com/ezsystems/RepositoryProfilerBundle)
- Many other small fixes and improvements

---

# What's next?

- Symfony 2.7 LTS
- RichText/Online - Editor UI
- Permission management UI
- User Management UI
- Translations handling UI
- Content Module + Named Queries
- Finalize Solr Search engine
- ...
- and eZ Studio to be announced soon..


See [recent blog](http://share.ez.no/blogs/core-development-team/ez-platform-15.03-is-out-what-s-next) post for more details.

---

# Bonus
## Code examples

--

## Named Queries

```jinja
{{ render_esi( controller(
    'ez_content:view',
    { 'viewIdentifier': 'children', 'params': {'type': 'article' } }
) ) }}
```

*Involves: Search, sub request,  override, render, http cache*

No fetching or looping needed, use over overrides


<aside class="notes">
Pseudo code, will create spec for this in end of June.
</aside>


--

## Overriding template, [Doc](https://doc.ez.no/display/EZP/How+to+use+a+custom+controller+to+display+a+content+or+location)

Example overriding template used for a article
```yml
template: AcmeTestBundle:full:article.html.twig
match:
    Identifier\ContentType: [article]
```

Template:
```jinja
{% extends noLayout ? viewbaseLayout : "eZDemoBundle::pagelayout.html.twig" %}

{% block content %}
    <h1>{{ ez_content_name( content ) }}</h1>
    {{ ez_render_field( content, 'body' ) }}
    {# some custom logic here #}
{% endblock %}
```

<aside class="notes">
  Go over what is going on here, including fact that twig functions used takes current
  language and available language into account.
</aside>

--

## Micro workflows: [Signals](https://doc.ez.no/display/EZP/How+to+listen+to+Core+events)

Setting up a class to listen to Publishing signal:
```yml
services:
    my.onpublish_slot:
        class: %my.onpublish_slot.class%
        tags:
            - { name: ezpublish.api.slot,
                signal: ContentService\PublishVersionSignal }
```

Main part of the class:
```php
public function receive( Signal $signal )
{
    if ( !$signal instanceof ContentService\PublishVersionSignal ) {
        return;
    }

    // Do stuff with $signal->contentId & $signal->versionNo
}
```

---

# The end

*Go to [eZ Platform repository](https://github.com/ezsystems/ezplatform/blob/master/INSTALL.md) to quickly test it yourself.*


-------------------

Web: [ez.no](http://ez.no), [Community](http://share.ez.no), [eZ Conference NYC 2015](http://ez.no/Blog/eZ-Conference-Early-Bird-Extension-Upcoming-Speaker-Announcement)<br>
Twitter:  [@ezsystems](https://twitter.com/ezsystems), [@ezcommunity](https://twitter.com/ezcommunity), [@andrerom](https://twitter.com/andrerom), ..<br>
Mettups: [Oslo](http://www.meetup.com/eZ-Oslo-Meetup), [Paris](http://www.meetup.com/ezpublish-paris-meetup),
 [Lyon](http://www.meetup.com/Lyon-eZ-Publish-Meetup), [New York](http://www.meetup.com/newyorkezusercommunity),
 [Köln](http://www.meetup.com/Koln-eZ-Publish-Meetup/), [San Francisco](http://www.meetup.com/San-Francisco-eZ-Publish-Meetup/), ..<br>
Blogs: [What to expect from eZ Studio & eZ Platform](http://ez.no/Blog/What-to-Expect-from-eZ-Studio-and-eZ-Platform), [eZ Platform 15.03 is Out. What's Next?](http://ez.no/Blog/New-Release-eZ-Platform-Alpha-15.03)<br>


-------------------

See more slides on [http://ezsystems.github.io/slides/](http://ezsystems.github.io/slides/)
