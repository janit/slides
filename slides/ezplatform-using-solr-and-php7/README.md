## eZ Platform; Symfony CMS with Advance Querying capabilities
#### On using Symfony, Solr, PHP7 and future performance/search work

*[eZ Symfony meetup, Oslo - 15<small><sup>th</sup></small> of September 2015](http://www.meetup.com/eZ-Oslo-Meetup/events/223127262/)*

<small>
<br><br>
By [André R.](https://github.com/andrerom) / [@andrerom](http://twitter.com/andrerom)
</small>

<div style="font-size: 50%; text-align: left; color: #ddd; position: absolute; bottom: -40%; left: 0;">
<em>Press `?` for info on how to navigate these slides<em>
</div>


Note:
After some talks about optimizations, we could have gone further into what kind of optimizations we have done in
eZ Platform over the last year, but given the already excellent talk on the topics this talk is rather focusing on
performance from a different perspective. Focusing more on Search and macro optimizations.

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
 the same time building on top of it is 100% commercial for media companies




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
- **250,000**+ installations of various versions

Provides additional software, services, consulting, & training for users & partners.


Note:
Notable customers: MadSack, RedBull, Economist, QuickSilver, several universities..

---

# Big Picture

In case of a CMS, being able to do advance queries on the Content Repository easily and performant
is one of the main cornerstones to enable advance use cases.



--

### Challenges, *in order*

- Search backend <!-- .element: class="fragment" -->
- Cache\* <!-- .element: class="fragment" -->
- Symfony & Application Performance <!-- .element: class="fragment" -->

<br>
<br>
<br>
<div class="fragment">
\* *but see [separate talk](http://ezsystems.github.io/slides/slides/almost-perfect-caching-with-foshttpcache/#/) on that*
</div>

Note:
In the case of cache, we aim to try to make Cache transparent to users and developers alike.


---

# Search performance



--

### Search example



```php

$query = new LocationQuery();

$query->criterion = new LogicalAnd([
   new ParentLocationId(2),
   new ContentTypeIdentifier(['articles', 'user_post']),
   new DateMetadata('modified', Operator::GTE, time() -300),
   new Field('for_sale', Operator::EQ, true),
   new MapLocationDistance('geo', Operator::LT, $dist, $lat, $long),
   new LogicalOr([ ... ])
]);

$latestLocalNews = $searchService->findLocations($query);
```

<small>*Here using PHP, also exposed via REST, and in the future also more easily available directly from Twig*</small>

Note:
Show frontend for pointing out where search is done.


--

### Example of SQL


```sql
SELECT DISTINCT
  ezcontentobject.id,
  ezcontentobject.*,
  `main_tree`.`main_node_id` `main_tree_main_node_id`,
  `sort_table_0`.`sort_key_int` `sort_column_0`,
  `sort_table_0`.`sort_key_string` `sort_column_0_bis`
  FROM `ezcontentobject`
INNER JOIN ezcontentobject_version ON ezcontentobject.id = ezcontentobject_version.contentobject_id
LEFT JOIN `ezcontentobject_tree` `main_tree` ON
(
  `main_tree`.`contentobject_id` = `ezcontentobject`.`id` AND `main_tree`.`main_node_id` = `main_tree`.`node_id`
)
INNER JOIN `ezcontentobject_attribute` `sort_table_0_main_language` ON
(
  `sort_table_0_main_language`.`contentobject_id` = `ezcontentobject`.`id` AND
  `sort_table_0_main_language`.`version` = `ezcontentobject`.`current_version`
  -- initial_language_id is missing index
  -- AND ((`sort_table_0_main_language`.`language_id` & -2) & `ezcontentobject`.`initial_language_id`) > 0
)
LEFT JOIN `ezcontentobject_attribute` `sort_table_0` ON
(
  `sort_table_0_main_language`.`contentobject_id` = `sort_table_0`.`contentobject_id` AND
  `sort_table_0_main_language`.`contentclassattribute_id` = `sort_table_0`.`contentclassattribute_id` AND
  `sort_table_0_main_language`.`version` = `sort_table_0`.`version` AND
  ((`sort_table_0`.`language_id` & -2) & 8) > 0
)
INNER JOIN `ezcontentclass_attribute` `cc_attr_0` ON
(
  `sort_table_0_main_language`.`contentclassattribute_id` = `cc_attr_0`.`id` AND
  `cc_attr_0`.`identifier` = "date_airing_start" AND
  `cc_attr_0`.`version` = 0
)
INNER JOIN `ezcontentclass` `cc_0` ON
(
  `cc_attr_0`.`contentclass_id` = `cc_0`.`id` AND
  `cc_0`.`identifier` = "stvd_epg" AND `cc_0`.`version` = 0
)
WHERE
(
  (
    (
      `ezcontentobject`.`id` IN
      (
        SELECT `from_contentobject_id`
        FROM `ezcontentobject_link`
        INNER JOIN ezcontentclass_attribute ON ezcontentclass_attribute.id = ezcontentobject_link.contentclassattribute_id
        WHERE
        (
          `ezcontentobject_link`.`from_contentobject_version` = `ezcontentobject`.`current_version` AND
          `ezcontentclass_attribute`.`contentclass_id` = `ezcontentobject`.`contentclass_id` AND
          `ezcontentclass_attribute`.`identifier` = "broadcast" AND
          `ezcontentobject_link`.`to_contentobject_id` IN ( 2608, 2612, 2614, 2617, 2620, 6923, 26300, 10408, 10409, 22629, 34980, 6924, 36401, 34979 )
        )
      ) AND
      `ezcontentobject`.`id` IN
      (
        SELECT `contentobject_id`
        FROM `ezcontentobject_attribute`
        WHERE
        (
          `ezcontentobject_attribute`.`version` = `ezcontentobject`.`current_version` AND
          (
            `contentclassattribute_id` IN ( '596', '625', '1011' ) AND
            `sort_key_string` = "1"
          )
        )
      ) AND
      `ezcontentobject`.`id` IN
      (
        SELECT `contentobject_id`
        FROM `ezcontentobject_attribute`
        WHERE
        (
          `ezcontentobject_attribute`.`version` = `ezcontentobject`.`current_version` AND
          (
            `contentclassattribute_id` IN ( '528' ) AND
            `sort_key_int` > "1424250375"
          )
        )
      )
    ) AND
    (
      "1" AND
      (
        -- section id is missing index
        -- `ezcontentobject`.`section_id` IN ( '1' ) OR
        (
          `ezcontentobject`.`contentclass_id` IN ( '26', '27', '37', '46', '55', '58', '79', '85', '87', '89', '91', '93', '95' )
          -- section id is missing index
          -- AND `ezcontentobject`.`section_id` IN ( '3' )
        )
        -- section id is missing index
        -- OR `ezcontentobject`.`section_id` IN ( '1', '3' )
      )
    )
  ) AND
  ezcontentobject.status = 1 AND
  ezcontentobject_version.status = 1
)
ORDER BY `sort_column_0` ASC, `sort_column_0_bis` ASC LIMIT 10 OFFSET 0
```

TL;DR; Not very fast, and not much we can/want-to do about that. <!-- .element: class="fragment" -->


--

<!-- .slide: data-background="imgs/Solr_Logo_on_white.png" data-background-transition="concave" data-transition="fade-in fade-out" -->


### Solr Search Engine Bundle


<ul style="background-color: rgba(255, 255, 255, .66); padding: 1em;">
<!-- .element: class="fragment" -->
  <li>In beta, <a href="http://ez.no/Blog/eZ-to-launch-enhanced-search-engine-and-recommendation-service">publicly as of today</a></li>
  <li>Supports 99% of existing eZ Publish 5.x queries atm</li>
  <li>Thanks to Solr; already faster and far more scalable</li>
  <li>Step towards far more advance search/querying</li>
</ul>

<br>
<br>
<br>

Note:
Also of-course takes down load on database, with all load operations ideally cached in memcached already,
remaining dynamic search is now handled by Solr.




---

# Application / Symfony performance

*Besides profiling and getting insights into where you can make your application faster for knowning what to optimize,
there is also one small thing to remember..*


--

<!-- .slide: data-background="imgs/Logo_PHP7.png" data-background-transition="concave" data-transition="fade-in fade-out" -->

<br><br><br><br><br><br><br><br><br><br><br><br>
### Upgrade PHP!


--

### Some numbers

Rasmus Lerdorf has done quite some benchmarking as parts of [his php7 talks](http://talks.php.net/froscon15#/) this year.

7.0 vs 5.6:
- Wordpress: 2.3x <!-- .element: class="fragment" -->
- phpBB: 1.8x <!-- .element: class="fragment" -->
- Drupal8: 1.78x <!-- .element: class="fragment" -->


--

### Some numbers with eZ Platform

*We will post benchmark numbers soon, but numbers on UI browser load times, those your users will notice:*:

- Login page with container compilation: ~1.7x <!-- .element: class="fragment" -->
- Login page w/o container compilation: ~1.5x <!-- .element: class="fragment" -->
- Time to publish content: ~1.3x <!-- .element: class="fragment" -->

<br><br>
Publishing can further be improved by using Solr and in the future handle indexing asynchronously. <!-- .element: class="fragment" -->

Note:
Demo time for UI



--

### Next steps on application/Sf


- Http Cache multi tagging -> for inlining requests <!-- .element: class="fragment" -->
- Easier allow to set ttl time for long lived cache <!-- .element: class="fragment" -->
- Re add stale HTTPCache with Varnish <!-- .element: class="fragment" -->
- Smaller things: <!-- .element: class="fragment" -->
 - Optimize Persistence cache (Stash)
 - Optimize our Sf event use, including Session handling



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



---

### Q&A

<div style="border: 1px solid #bbb">
  <div style="position: relative; left: -400px; top:-16px; font-size: 40%; color: #aaa">Shameless Advertising</div>
  ![eZ Conf promotion](http://share.ez.no/var/community/storage/images/box-ez-conference/876937-9-eng-GB/box-ez-conference.jpg)<br>
  <small style="color: #777">*[In New York in November?](http://conference.ez.no/)*</small>
</div>


NOTE:
Final word:: topic for next meetup if not already said.