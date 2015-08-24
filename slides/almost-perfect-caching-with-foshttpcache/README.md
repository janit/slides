## Almost perfect caching with FOSHttpCache

*[FrOSCon, Bonn - 22<small><sup>nd</sup></small> of August 2015](http://froscon.phpugdo.de/)*

<small>
<br><br>
By [André R.](https://github.com/andrerom) / [@andrerom](http://twitter.com/andrerom)
<br><br>
*With special thanks to creators of FOSHttpCache: [@dbu](https://github.com/dbu), [@ddeboer](https://github.com/ddeboer), [@lolautruche](github.com/lolautruche), others [\[1\]](https://github.com/FriendsOfSymfony/FOSHttpCache/graphs/contributors) [\[2\]](https://github.com/FriendsOfSymfony/FOSHttpCacheBundle/graphs/contributors)*
</small>

<div style="font-size: 50%; text-align: left; color: #999; position: absolute; bottom: -40%; left: 0;">
<em>Press `?` for info on how to navigate these slides<em>
</div>


Note:
Hi.. I'm André, and I'm honored to open this years FrOSCon!
There are a lot of interesting topics coming up so hope your all up and awake ;)

---

# About

--

## About [myself](https://github.com/ezsystems/ezplatform)

- Norwegian, lived in Germany and France
- Coding++
 - 1997: dHtml
 - 2005: PHP & Open Source 
 - 2006: eZ
 - Contributing* to: eZ, Symfony, Composer, FOS, PHP-FIG, ..
 - Somewhat familiar w/ Frontend, Agility, Test Strategy, Hiring..
- Other interests: Music, Bicycling, Skiing, Hiking, ..

<br>

<small>\* or "Trying to" to be more correct</small>


Note:
PHP: Seriously since 2008 after unwilling mentoring by Derick Rethans & others at eZ, as I started to contribute to Core
Contribution: Besides eZ I currently try to contribute to Symfony, FriendsOfSymfony, ~~Node.js~~~
eZ: Been in various roles; Consultant, Project Lead, Engineer, Scrum/Kanban master, VP Engineering
Trivia: Remembers 5 year old conversation/code-change, but not a name 30 seconds later.

--

## About [eZ Platform](https://github.com/ezsystems/ezplatform)

- `eZ Platform`, earlier reffered to as  **`eZ Publish 6.0`**
- Introduced as part of **`eZ Publish Platform 5.0`**
- Builds upon, extends, & contributes to Symfony
- Coming with new UI and on it's own this fall
 - Aims to be a 100% open source project, while **`eZ Studio`** launching at
 the same time building on top of it, and is 100% commercial for media companies




Note:
A feature rich, highly extensible, open source CMS/CMF
5.0 came in 2012, 5.x is hybrid offering both `Platform` and `Publish` in one
Commercial, adds features for editorial organizations

--

## About [eZ Systems AS](http://ez.no)

- **1999** Founded
- **8** Offices across the globe
- **200**+ community & business partners
- **500**+ small to large customers & growing
- **45,000** or so registered community users
- **300,000**+ installations of various versions

Provides additional software, services, consulting, & training for users & partners.


Note:
Notable customers: MadSack, RedBull, Economist, QuickSilver, several universities..

---

# Big Picture


--

### TL;DR; We want cache to become transparent & scalable!

- In an application like a CMS, cache is needed <!-- .element: class="fragment" -->
- Cache is complex, but we want to "hide" that <!-- .element: class="fragment" -->
 - While still giving developers full control to customize
- All while also trying to make it easy to scale <!-- .element: class="fragment" -->



--

### How??

- Actively tagging cache items with entity meta data <!-- .element: class="fragment" -->
- Supporting several cache systems, for different needs <!-- .element: class="fragment" -->
- Stale/Graceful cache handling by default in HTTP layer <!-- .element: class="fragment" -->


Note:
Tagging: to easily expire relevant items on any operation.
Cache systems: to enable tailored performance & scalability.
Stale: to avoid cache dog-piling on high load urls


---

# Getting there


--

<!-- .slide: data-background="imgs/Cache_blocks.png" data-background-transition="concave" data-transition="fade-in fade-out" -->
### Example web site


<br><br><br>
<br><br><br>


--

### Background: eZ Publish 3.x/4.x

```tpl
{* Cache block system, very flexible, but also no deep integrations with the content *}
{cache-block [ keys=keys                      ]
             [ expiry=expiry                  ]
             [ ignore_content_expiry          ]
             [ subtree_expiry=subtree_expiry  ]}

... for example generating site menu ...

{/cache-block}
```


```ini
# Content View Cache, for the block representing current page (Far far away)

[blog_post]
DependentClassIdentifier[]=frontpage
DependentClassIdentifier[]=blog
AdditionalObjectIDs[]=55
ClearCacheMethod[]=object
ClearCacheMethod[]=parent
ClearCacheMethod[]=relating
```


Note:
View being the content of the current page you are one, while cache-blocks typically used for any other block on the page.
Far Far away on previous slide.

--

<!-- .slide: data-background="imgs/wtf_cat.jpg" data-background-transition="concave" data-transition="fade-in fade-out" -->
## Architecture WTF

Very flexible, but:

- Weak integration with Content Repository <!-- .element: class="fragment" -->
- Weak integration with Reverse Proxies <!-- .element: class="fragment" -->


--

## Symfony2

In Köln in May 2012 with eZ, QaFoo & Benjamin Eberlei:

*"How to have a cache system with same, or better flexibility, while better integrated?"*

--

## Symfony2

- Standardizes on HTTP for cache, fully customizable <!-- .element: class="fragment" -->
- Native ESI fragments theoretically perfect for blocks <!-- .element: class="fragment" -->
- Varnish powering these > Insane performance <!-- .element: class="fragment" -->


--

## HttpCache "issues"

- Logged in users <!-- .element: class="fragment" -->
- Same content/entities on multiple urls <!-- .element: class="fragment" -->
- Slow perf. with many ESI requests on Sf HttpCache <!-- .element: class="fragment" -->

--

## Extending Symfony2

In 2014 FosHttpCache enters based on similar ideas @ Liip, Driebit, eZ, JoliCode and others. Mainly:

- Entity awareness > Tag Cache with entity identifier(s) <!-- .element: class="fragment" -->
 - Example header: ```X-Cache-Tags: location-44, ..``` <!-- .element: class="fragment" -->
- User awareness > Vary Cache by User rights/context <!-- .element: class="fragment" -->
 - Example header: ```Vary: X-User-Context-Hash``` <!-- .element: class="fragment" -->
- Most featurs across Varnish, Sf HttpCache, & Nginx <!-- .element: class="fragment" -->


Note:
In parallel others worked on the same, & similar topics.
End of 2013 joining forces to create FOSHttpCache by contributing several related solutions,
eZ joined in 2014 and contributed User awareness.

--

<!-- .slide: data-background="imgs/Cache_request1.png" data-background-size="1280px" data-background-transition="concave" data-transition="fade-in fade-out" -->
<br><br><br><br><br><br><br><br><br><br><br><br><br>
### 1/4 "Normal" HTTP Uncached

--

<!-- .slide: data-background="imgs/Cache_request2.png" data-background-size="1280px" data-background-transition="concave" data-transition="fade-in fade-out" -->
<br><br><br><br><br><br><br><br><br><br><br><br><br>
### 2/4 "Normal" HTTP Cached

--

<!-- .slide: data-background="imgs/Cache_request3.png" data-background-size="1280px" data-background-transition="concave" data-transition="fade-in fade-out" -->
<br><br><br><br><br><br><br><br><br><br><br><br><br>
### 3/4 User Context aware


--

<!-- .slide: data-background="imgs/Cache_ban.png" data-background-size="1280px" data-background-transition="concave" data-transition="fade-in fade-out" -->
<br><br><br><br><br><br><br><br><br><br><br><br><br>
### 4/4 "Entity Aware": Ban

Note:
Yes, Varnish allows BAN using custom VCL:
```
    if (req.method == "BAN") {
        if (!client.ip ~ invalidators) {
            return (synth(405, "Not allowed"));
        }

        if (req.http.X-Cache-Tags) {
            ban("obj.http.X-Host ~ " + req.http.X-Host
                + " && obj.http.X-Url ~ " + req.http.X-Url
                + " && obj.http.content-type ~ " + req.http.X-Content-Type
                + " && obj.http.X-Cache-Tags ~ " + req.http.X-Cache-Tags
            );
        } else {
            ban("obj.http.X-Host ~ " + req.http.X-Host
                + " && obj.http.X-Url ~ " + req.http.X-Url
                + " && obj.http.content-type ~ " + req.http.X-Content-Type
            );
        }

        return (synth(200, "Banned"));
```


--

![Boom! Problem Solved](http://cdn.meme.am/instances/58459797.jpg)


## Or? <!-- .element: class="fragment" -->

--

### Houston, more problems!
#### Current state:

- Multi tagging only with Varnish <!-- .element: class="fragment" -->
- Varnish BAN not graceful > Dog-piling under load!  <!-- .element: class="fragment" -->

Note:
no multi tagging makes it hard to reliably inline several esi requests to avoid the ESI perf issue.
dog-piling aka Cache stamped when using BAN which is used for purging cache by tags.

---


# Next


--

### Tags

<small>*To be able to avoid to many ESI request when using Symfony HttpCache we need multi tagging to clear cache representing several entities.*</small>

- Abstracting Tags\* from Varnish <!-- .element: class="fragment" -->
- Add support in Symfony HttpCache <!-- .element: class="fragment" -->
- Should also be possible for Nginx <!-- .element: class="fragment" -->

<small class="fragment">\* [Abstracting tags PR](https://github.com/FriendsOfSymfony/FOSHttpCache/pull/237)</small>

--


### Graceful Ban

<small>*To be able to have graceful cache also when purging/expiring by tag, currently using: BAN*</small>

- Varnish is not adding "SoftBan" anytime soon <!-- .element: class="fragment" -->
- Potentially own tag handling & use PURGE per URL? <!-- .element: class="fragment" -->
 - Setup Symfony HttpCache to just cache tags, expiry & urls? <!-- .element: class="fragment" -->
 - Use this for PURGEs, & custom VCL rules to expire URLS? <!-- .element: class="fragment" -->

Note:
SoftBan: If you have connections, make sure to put pressure on Varnish ;)

--

<!-- .slide: data-background="imgs/Cache_tag_purge.png" data-background-size="1280px" data-background-transition="concave" data-transition="fade-in fade-out" -->
<br><br><br><br><br><br><br><br><br><br><br><br><br>
### Tags Purging

Note:
Async since number of url's across number of Varnish servers might be a low, Async in this case refer to allowing ques
to handle the purging, can be Symfony kernel.terminate out of the box allowing other bundles to extend that.
Varnish here can be any Reverse Proxy cache supporting purging/expiry.


--

## And then?

![Win!](http://www.cesim.com/Portals/104692/images/how-to-win-business-simulation.jpg) <!-- .element: class="fragment" -->

### ?? <!-- .element: class="fragment" -->

---

### The end


-------------------

#### eZ

<p align="left">
[eZ Platform](https://github.com/ezsystems/ezplatform)<br>
Twitter:  [@andrerom](https://twitter.com/andrerom)<br>
Mettups: [Oslo](http://www.meetup.com/eZ-Oslo-Meetup), [Paris](http://www.meetup.com/ezpublish-paris-meetup),
 [Lyon](http://www.meetup.com/Lyon-eZ-Publish-Meetup), [New York](http://www.meetup.com/newyorkezusercommunity),
 [Köln](http://www.meetup.com/Koln-eZ-Publish-Meetup/), [San Francisco](http://www.meetup.com/San-Francisco-eZ-Publish-Meetup/)<br>
See more slides on [http://ezsystems.github.io/slides/](http://ezsystems.github.io/slides/)
</p>

-------------------

#### FOSHttpCache

<p align="left">
[FOSHttpCache](https://github.com/FriendsOfSymfony/FOSHttpCache)<br>
[FOSHttpCacheBundle](https://github.com/FriendsOfSymfony/FOSHttpCacheBundle)
</p>



---

### Q&A

<div style="border: 1px solid #bbb">
  <div style="position: relative; left: -400px; top:-16px; font-size: 40%; color: #aaa">Shameless Advertising</div>
  ![eZ Conf promotion](http://share.ez.no/var/community/storage/images/box-ez-conference/876937-9-eng-GB/box-ez-conference.jpg)<br>
  <small style="color: #777">*[In New York in November?](http://conference.ez.no/)*</small>
</div>


NOTE:
@todo Add note on that we are hiring!