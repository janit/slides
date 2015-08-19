## Almost perfect caching with FosHttpCache

*[FrOSCon, Bonn - 22<small><sup>nd</sup></small> of August 2015](http://froscon.phpugdo.de/)*

<small><br>
<br>By [André R.](https://github.com/andrerom) / [@andrerom](http://twitter.com/andrerom)


Note:
Hi.. I'm André, and I'm honored to open this years FrOSCon!
There are a lot of interesting topics coming up so hope your all up and awake ;)
..I'm not :P


---

# About

--

## About [myself](https://github.com/ezsystems/ezplatform)

- Norwegian, lived in Germany and France
- Coding++
 - 1997: dHtml
 - 2005: PHP'er & Contributor 
 - 2006: eZ
 - Also knows a thing or two about Frontend, Agility, Testing Strategy, Hiring, ...
- Interests in Music, Bicycling, Skiing, Hiking, ..

<small>Can remember a 5 year conversation or code change, but not a name I learned 5 seconds ago</small>


Note:
Coding: Started by being curies about how web pages like 3dfx.com worked. 
PHP: Seriously since 2008 after unwilling mentoring by Derick Rethans & others at eZ, as I started to contribute to Core
Contribution: Besides eZ I currently try to contribute to Symfony, FriendsOfSymfony, ~~Node.js~~~
eZ: Been in various roles; Consultant, Project Lead, Engineer, Scrum/Kanban master, VP Engineering


--

## About [eZ Systems AS](http://ez.no)

- **1999**: Founded
- **8** Offices across the globe
- **200**+ community & business partners
- **500**+ small/large customers & growing
- **50,000** or so registered community users
- **300,000**+ installations of various versions


Note:
Notable customers: MadSack, RedBull, Economist, QuickSilver, several universities..


--

## About [eZ Platform](https://github.com/ezsystems/ezplatform)

- `eZ Platform` is next generation **`eZ Publish`**
 - A feature rich, highly extensible, open source CMS/CMF
- Introduced in 2012 as **`eZ Publish Platform 5.0`**
 - 5.x is hybrid offering both `Platform` and `Publish` in one
- Coming on it's own this fall together with **`eZ Studio`**
 - Commercial, adds features for editorial organizations


Note:
Platform has an arch. that brings increased performance and scalability, while also allowing us to 
further enhance this in the future without breaking the API's exposed to developer code.

---

# Big Picture


--

### TL;DR; We want cache to become transparent!

- Actively tagging cache items to easily expire relevant items on any operation <!-- .element: class="fragment" -->
- Across different cache systems enabling great performance & scalability  <!-- .element: class="fragment" -->

---

# Getting there


--

### Our Background: eZ Publish 3.x/4.x

```tpl
{* Cache block system, very flexible, but also no deep integrations with the content *}
{cache-block [ keys=keys                      ]
             [ expiry=expiry                  ]
             [ ignore_content_expiry          ]
             [ subtree_expiry=subtree_expiry  ]}

...

{/cache-block}
```


```ini
# Content View Cache, also a "block", but somewhat native one

[article]
DependentClassIdentifier[]=frontpage
AdditionalObjectIDs[]=55
ClearCacheMethod[]=object
ClearCacheMethod[]=parent
ClearCacheMethod[]=relating
```


Note:
View being the content of the current page you are one, while cache-blocks typically used for any other block on the page.

--

<!-- .slide: data-background="imgs/wtf_cat.jpg" data-background-transition="concave" data-transition="fade-in fade-out" -->
## WTF

- Weak integration with Content Repository <!-- .element: class="fragment" -->
- Weak integration with Reverse Proxies <!-- .element: class="fragment" -->


--

## Symfony2

In Köln in May 2012 with eZ, QaFoo & Benjamin Eberlei:

- Native ESI perfect fit for blocks <!-- .element: class="fragment" -->
- HTTPCache also for "view cache" natively <!-- .element: class="fragment" -->
- Varnish as "view cache" > Insane performance <!-- .element: class="fragment" -->

<p align="left">*But what about logged in users & content variants?*</p> <!-- .element: class="fragment" -->

--

## Extending Symfony2

Early 2013 after discussions with Symfony community:

- Content awareness > Tag Cache variants with id <!-- .element: class="fragment" -->
- User awareness > Vary Cache by User rights <!-- .element: class="fragment" -->


Note:
In parallel others worked on the same, & similar topics.
End of 2013 joining forces to create FOSHTTPCache by contributing several related solutions,
eZ joined in 2014 and contributed User awareness.

--

![Boom! Problem Solved](http://cdn.meme.am/instances/58459797.jpg)

## Or? <!-- .element: class="fragment" -->

--

### Houston, more problems!
#### Current state

- ESI on Symfony HTTPCache is notably slow <!-- .element: class="fragment" -->
- No muti tagging with Symfony HTTPCache <!-- .element: class="fragment" -->
- Varnish BAN not graceful > Dog-piling under load!  <!-- .element: class="fragment" -->

Note:
dog-piling aka Cache stamped when using BAN which is used for purging cache by tags.

---


# Next


--

### Tags

- Abstracting Tags\* from Varnish <!-- .element: class="fragment" -->
- Add support in Symfony HTTPCache <!-- .element: class="fragment" -->

<small class="fragment">\* [Abstracting tags PR]((https://github.com/FriendsOfSymfony/FOSHttpCache/pull/237)</small> 

--


### Soft Ban

- Varnish seems unwilling to add SoftBan <!-- .element: class="fragment" -->
- Potentially own tag handling & use PURGE over BAN <!-- .element: class="fragment" -->


--

## And then?

![Win!](http://www.cesim.com/Portals/104692/images/how-to-win-business-simulation.jpg) <!-- .element: class="fragment" -->


---

### The end

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


---

### Q&A

<div style="border: 1px solid #bbb">
  <div style="position: relative; left: -400px; top:-16px; font-size: 40%; color: #aaa">Shameless Advertising</div>
  ![eZ Conf promotion](http://share.ez.no/var/community/storage/images/box-ez-conference/876937-9-eng-GB/box-ez-conference.jpg)<br>
  <small style="color: #777">*In New York in November?*</small>
</div>