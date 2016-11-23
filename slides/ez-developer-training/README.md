# eZ Developer Training
### Learning how to use eZ as a developer


---

# Agenda

--

## Training target

- We will create a site that allows displaying and listing of bike ride routes from around the world
- Features
 - Custom content structure (routes, points of interest)
 - Custom layout (from pre-built HTML templates)
 - Search and display rides 
 - Import rides using a script
 - Understand the basics of layout management


--

## Structure of training

- The training task is split into 8 chunks
  1. Learn core concepts of eZ Platform
  2. Create Content Types and Structure
  3. Integrate main template and assets
  4. Create template for bike ride
  5. Create a custom content query
  6. Implement search functionality
  7. Create an import script
  8. Explore Landing Page management


--

## How will this work?

- Code will run in a virtual machine
- We will run the exercises together
- You can always catchup with a<br />branch from Git (steps)



---

# The core concepts of eZ Platform
#### (Step 1)

--

## Add stuff here, from other slide decks

 - a
 - b
 - c

#### (Step 1)

---

# Content Types<br />and Structure
#### (Step 2)

--

## Content Types and Structure

- Let’s begin by creating content types that will form the skeleton of our site
- We will be using
 - Folder: for containing items
 - Bike ride: for main routes
 - POI: for points of interest (sights, etc.)
- We’ll use the default "Folder" content type for containers

#### (Step 2)

--

## Content Types and Structure

- Let’s begin by creating content types that<br />will form the skeleton of our site
- We will be using
 - Folder: for containing items
 - Bike ride: for main routes
 - POI: for points of interest (sights, etc.)
- We’ll use the default "Folder"<br />content type for containers

#### (Step 2)

--

## Content Types and Structure

- Bike ride fields:
 - Title (text line)
 - Photo (image)
 - Description (Rich Text)
 - Difficulty (eZ Selection)
 - Starting Point (Location)
 - Ending Point (Location)
 - Length (Integer)
 - Author (Author)

#### (Step 2)

--

## Content Types and Structure

- Point of Interest (POI)
 - Title (text line)
 - Photo (image)
 - Description (Rich Text)
 - Location (Location)

#### (Step 2)

--

## Content Types and Structure

- Now let’s organize objects to form a structure:
- All Rides (folder)
  - Ride 1 (bike ride)
  - Ride 2 (bike ride)
  - Ride 3 (bike ride) 
- Points of Interest (folder)
 - POI 1 (point of interest)
 - POI 2 (point of interest)
 - POI 3 (point of interest)

#### (Step 2)

--

## Content Types and Structure

- Ok so now we’ve got:
 - Content types
 - Content objects
 - A good structure


<pre><code class="hljs" data-trim>
git checkout step-1
mysql -uroot ez_training_1 < data/REPLACE/THIS.SQL
</code>
</pre>

#### (Step 2)

---

# Integrate main template and assets
#### (Step 3)

--

## Integrate main template and assets

- We have pre-built HTML templates and assets:
 - training/static_assets
- Start by copying index.html to our main layout:
 - app/Resources/views/pagelayout.html.twig
- Then configure loading of main template:
 - Create app/config/views.yml and import from ezplatform.yml
 - Define default pagelayout to pagelayout.html.twig

#### (Step 3)

--

## Integrate main template and assets

- Once we have our main template in place, we can proceed with embedding assets
- Let’s copy all static assets to:
 - src/AppBundle/Resources/public
- After this is done, let’s run the linking command to create symlinks to the public web root:
 - php app/console assets:install --symlink


#### (Step 3)

--

## Integrate main template and assets

- Now that we’ve got access to our JavaScript, CSS and images, we can start embedding them in our main template:
 - {{ asset('bundles/app/images/favicon.ico') }}
 - {{ asset('bundles/app/css/bootstrap.min.css')}}
 - ...
- Minification, etc. best done using your standard toolkit (WebPack, Grunt, Gulp, ???)


#### (Step 3)

--

## Integrate main template and assets

- Next let’s define the dynamic content areaby creating a template that extends our main template:
 - app/Resources/views/full/all_rides.html.twig
- To match this specific template to a location, we’ll need to matching to our views.yml:
<pre>root_folder:
    template: "full/all_rides.html.twig"
    match:
        Id\Location: 2
</pre>

#### (Step 3)

--

## Integrate main template and assets

- Now that we’ve loaded a matching template, let’s make it extend our main template:
 - {% extends "pagelayout.html.twig" %}
- To view content in a specific section, we’ll define a block in our pagelayout.html template:
 - {% block content %}{% endblock %}
- In our page template, we will then output content with:
 - {% block content %}This is page content!{% endblock %} 

#### (Step 3)

--

## Integrate main template and assets

- Finally, let’s define the page’s title string from within the page template, by defining a variable: 
 - {% set page_title = 'All rides' %}
- This we will then simply print out in the main layout template:
 - &lt;title&gt;{{ page_title|default() }} &lt;/title&gt;

#### (Step 3)

--

## Integrate main template and assets

- Now we’ve got a complete page that is loading a specified template

<pre>
To catch up: git checkout step-2
</pre>


#### (Step 3)

---

# Create template for bike ride
#### (Step 4)

--

## FOO BAR

- a
- b
- c

#### (Step 4)

---

# Create a custom content query
#### (Step 5)

--

## FOO BAR

- a
- b
- c

#### (Step 5)

---

# Implement search functionality
#### (Step 6)

--

## FOO BAR

- a
- b
- c

#### (Step 6)

---


# Create an import script
#### (Step 7)

--

## FOO BAR

- a
- b
- c

#### (Step 7)

---


# Explore Landing Page management
#### (Step 8)

--

## FOO BAR

- a
- b
- c

#### (Step 8)

---
