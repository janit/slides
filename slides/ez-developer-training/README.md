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

## Create template for bike ride

- To load a specific template for a type of content object, we’ll need to define a rule in `views.yml`:
<pre>
template: "full/bike_ride.html.twig"
  match:
      Identifier\ContentType: [bike_ride]
</pre>
- Let’s add the template, define a content block and copy the the content area from:
training/static_assets/ride.html


#### (Step 4)

--

## Create template for bike ride

 - eZ automatically loads you content and location objects that you can dump with: 
  - {{ dump(content) }} and {{ dump(location) }}
 - Start embedding dynamic content to the template with the most common helper:
  - {{ ez_render_field(content,'title') }}
 - For complex values we might need to use:
  - {{ ez_field_value(content,'ending_point') }}


#### (Step 4)

--

## Create template for bike ride

- To use processed versions of our uploaded images we can use Image Aliases. They’re defined in configuration:
```
bike_ride_main:
   reference: ~
   filters:
      - { name: geometry/scaledownonly, params: [640, 480] }
```
- Desired version is defined as parameter for helper:
```
{{ ez_render_field(content, "photo",
  {
      "parameters": {"alias": 'bike_ride_main'}
  }) }}
```

#### (Step 4)

--

## Create template for bike ride

- To use processed versions of our uploaded images we can use Image Aliases. They’re defined in configuration:
```
bike_ride_main:
   reference: ~
   filters:
      - { name: geometry/scaledownonly, params: [640, 480] }
```
- Desired version is defined as parameter for helper:
```
{{ ez_render_field(content, "photo",
  {
      "parameters": {"alias": 'bike_ride_main'}
  }) }}
```

#### (Step 4)

--

## Create template for bike ride

- Let’s add a POI relation field to Bike Ride
- Create arbitrary connection between objects
- Define a custom controller in views config:
 - controller: "AppBundle:BikeRide:viewRideWithPOI”
- Controller will load the POIs using the PHP API:
 - src/AppBundle/Controller/BikeRideController.php


#### (Step 4)

--

## Create template for bike ride

- Load the POIs by using the API in the controller
 - $this->get('ezpublish.api.service.content');
 - $currentContent->getFieldValue('pois');
 - foreach ($pointOfInterestListId->destinationContentIds as $pointId)
- Add parameteters to view
 - $view->addParameters(['pois' => $pointOfInterestList]);


---

# Create a custom content query
#### (Step 5)

--

## Create a custom content query

- To load the list of rides we’ll use a Query Type
- To execute a query :
```
params:
  query:
      query_type: 'BikeRidesList'
      parameters:
          parentLocationId: "@=location.id"
      assign_results_to: 'bike_rides'
```
- This will execute a pre-configured query and assign it to variable bike_rides


#### (Step 5)

--

## Create a custom content query

- The query type is created using code:
 - src/AppBundle/QueryType/BikeRidesQueryType.php
- Queries are built with applying criterions
```
$filter = new Query\Criterion\LogicalAnd([
  new Query\Criterion\ContentTypeIdentifier('bike_ride')
]);
```
- Content Query results will be available in template:
```
return new LocationQuery([
{% for bike_ride in bike_rides.searchHits %}
```

#### (Step 5)

---

# Implement search functionality
#### (Step 6)

--

## Implement search functionality

- Let’s extend the query we created
- Add support for passing in a new parameter
 - SearchText
- Extend query to include criterion
 - Query\Criterion\FullText
- Also set default value to * when SearchText is blank


#### (Step 6)

--

## Implement search functionality

- Load routing from a YAML file in our bundle 
 - @AppBundle/Resources/config/routing.yml
- Add a custom controller
 - src/AppBundle/Controller/SearchController.php
- For templating we’ll create a custom template that extends our eZ - Platform page layout:
 - {% extends "pagelayout.html.twig" %}

#### (Step 6)

--

## Implement search functionality

- Load query type service
 - $this->get('ezpublish.query_type.registry');
- Load query type and load query type
 - $queryTypeService->getQueryType('BikeRidesList');
 - $queryType->getQuery(['searchText' => $searchText]);
- Execute
 - $this->get('ezpublish.api.service.search')->findContent($query);

#### (Step 6)

--

## Implement search functionality

- Loop in template level
 - {% for bike_ride in bike_rides.searchHits %}
- Set title
 - {% set page_title = 'Search for ' ~ search_text %}
- Remember to set form action
 - {{ path('gobike_search') }}


#### (Step 6)

--

## Implement search functionality

- Problem?
 - Two templates
- You could use
 - View templates and controllers
- For simplicity use include
 - {% include 'line/bike_ride.html.twig' with {'content': bike_ride.valueObject} %}

#### (Step 6)

---

# Create an import script
#### (Step 7)

--

## Create an import script

- CSV data file example:
 - training/data/rides.csv
- To use the league/csv component,
 - Add Composer.json to "league/csv": "^8.1"  
 - Composer install
- Use the regular Symfony Command method
 - AppBundle/Command/CreateCommand.php
- Accept parameters:
 - Root node
 - Data file

#### (Step 7)

--

## Create an import script

- Get parameters and load data file with league/csv:
 - $inputFile = $input->getArgument( 'file' );
 - $inputCsv = Reader::createFromPath($inputFile);
 - $rides = $inputCsv->fetchAll();
- Loop through just like anything
 - foreach($rides as $ride) { 


#### (Step 7)

--

## Create an import script

- Load container and services:
 - $this->getContainer()->get( 'ezpublish.api.repository' );
 - $repository->getContentService();
 - $repository->getLocationService();
 - $repository->getContentTypeService();
- Login as admin
 - $repository->setCurrentUser( $repository->getUserService()->loadUser( 14 ) );

#### (Step 7)

--

## Create an import script

- In the load content type and create struct
 - $contentTypeService->loadContentTypeByIdentifier('bike_ride');
 - $contentService->newContentCreateStruct($contentType, 'eng-GB');
- Set field values
 - $contentCreateStruct->setField('title', $ride[0]);
 - Note complex fields like starting_point, ending_point
- Create a location struct to define a location
 - $locationCreateStruct = $locationService->-newLocationCreateStruct($parentLocationId);


#### (Step 7)

--

## Create an import script

Create a draft with 
$contentService->createContent(
    $contentCreateStruct,
    array($locationCreateStruct)
);
Publish version
$contentService->publishVersion($draft->versionInfo);



#### (Step 7)

--

## Create an import script

- a
- b
- c

#### (Step 7)

--

## Create an import script

- a
- b
- c

#### (Step 7)

--

## Create an import script

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
