---
layout: platform_item
title: FAQs
slug: faqs
js_asset: "editor"
---
## Tables and data

### Which databases are supported by CartoDB?

One of the main components of CartoDB is its geospatial database built on PostgreSQL and PostGIS. This means that by default the CartoDB platform works over PostgreSQL due to making heavy use of PostGIS advanced capabilities. This way, if the CartoDB built-in features are not enough to perform your analysis, you can take advantage of the full power of PostGIS.

We offer the option of connecting your own database to CartoDB as part of our Enterprise features in order to adapt the platform to your specific needs. If you are interested, let us know at sales@cartodb.com.

### How can I export my data from CartoDB?

After you have created, updated, or deleted data from your CartoDB tables, you may want to export them for sharing or use offline. We make that easy for you by providing one-click data export.

<p class="wrap-border"><img src="{{ '/img/layout/faqs/export-data.png' | prepend: site.baseurl }}" alt="How can I export my data from CartoDB" /></p>

1. From any table you have created, click **Options** in the upper right corner.
2. Select **Export**  
  From there, you should see a menu of export formats that CartoDB supports, including Comma separated (CSV), KML, ESRI Shape Files (SHP), and SQL.

3. Click the format you would like to download.

There is a pro-tip for accessing downloads directly using your table URL. You can make use of the SQL API to run any query and ask for the results to be retrieved in different formats. For example:

<div class="code-title notitle code-request"></div>
{% highlight bash %}
http://{USERNAME}.cartodb.com/api/v2/sql?format=csv&q=SELECT+*+FROM+tm_world_borders_sim
{% endhighlight %}

### Are there any licenses on the data I upload to CartoDB?

For general rights on content, see: [http://cartodb.com/terms#subscriber](http://cartodb.com/terms#subscriber).

### I have a column with the coordinates but my data is not georeferenced

​This may be happening because you have both coordinates in the same column, and as you see in the wizard for georeferencing, it asks about two columns, one with longitude and another with the latitude.
​
​If you split this unique coordinates column in two, and apply again the georeferencing process by taking into account the both columns, your data will be georeferenced.

You can achieve this easily by following these steps:

1. Create two new columns in your table, of type string, with names "latitude" and "longitude".

2. Use the following SQL queries that will split your unique coordinates column into two:

{% highlight sql %}
UPDATE _tablename_ SET _latitude_ = split_part(_coordinates_, ', ', 1)

UPDATE _tablename_ SET _longitude_ = split_part(_coordinates_, ', ', 2)
{% endhighlight %}

Notice that these SQL functions are expecting the value of the coordinates column to be similar to: "lat, long". If you use a different separator or your coordinates are in different order, you will have to adapt the previous queries to your specific syntax.

### Why the size of my tables has increased after uploading them to CartoDB?

This has a couple reasons. First and foremost is simply that a database table has a lot more storage considerations than a CSV. For example, indexes. On top of that though, the actual way that data is stored on disk is optimized for lookup and retrieval speed over storage space. This makes sense because a CSV is made so you can optimally store data and if you open it load it all into a program for a limited amout of time while you edit it, then save it and be done. The database has things like data types etc. 

### Can I synchronize my tables in real time?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/force-sync.png' | prepend: site.baseurl }}" alt="Can I synchronize my tables in real time" /></p>

By using the feature of sync tables, the shortest automatic syncing interval is 1 hour, but you can force manually a synchronization each 15 minutes.

### I have a sync table, how can I modify the column types?

When you are working with a sync table, your data is not editable while your table is still connected to the source. If you need your columns to have some specific data type, you can achieve that by using an SQL query that casts your columns to the type you need to use.

The following query works by selecting all the columns you need and casting the ones you need to change the type using the syntax: `CAST (column_name as type)`. 

Notice that the different types that you can use to cast your columns are: text, int, date or boolean.

{% highlight sql %} 
SELECT cartodb_id, the_geom_webmercator, the_geom, CAST (number_column AS text), CAST (text_column as int) FROM tablename
{% endhighlight %}

Alternativelly, you can also use the syntax `column_name::type`, which could be specially useful when casting date columns:

{% highlight sql %} 
SELECT cartodb_id, the_geom_webmercator, the_geom, my_date_column::timestamp FROM tablename
{% endhighlight %}

###How do I geocode IP addresses that are included in my data?

CartoDB requires that IP addresses be in an isolated column in order to be able to recognize and geocode them.

A simple way to isolate IPs is to run a couple of SQL queries and store the values in a new column.

You can use a query like:

{% highlight sql %}UPDATE tablename SET new_ip_column = substring(old_ip_address_column_name, '(?:\d{1,3}\.){3}\d{1,3}'){% endhighlight %} 

While still in table view, you will then click on Edit and then choose Georeference layer. You will then choose by IP Addresses and select the column where your IP Addresses are located. 

### How can I lock a table/visualization?

To prevent your tables and visualizations from undesired changes you can lock your tables and visualizations. To lock a table or a visualization, just go to your dashboard, put the cursor over the desired item and click on the small L icon. You can lock and unlock any item as many times as you would like.

For visualizing your locked items, go to either your visualizations or tables dashboard and click on the View your X locked tables link at the bottom of your list.

### Why isn’t my shapefile importing?

CartoDB creates tables from shapefiles by importing a single zipped file. If your shapefile is not importing, make sure that:

1. You’re uploading a zipped file and not just one of the files it contains, such as a .shp file.
2. Your zipped file contains .shp, .dbf, .shx, and .prj files.
3. Your file names all have the same prefix, for example myshapefile.zip, myshapefile.shp, myshapefile.dbf, myshapefile.shx, and myshapefile.prj.

You can check this tutorial about importing shapefiles for more detail: [http://docs.cartodb.com/tutorials/import_shapefile_in_cartodb.html](http://docs.cartodb.com/tutorials/import_shapefile_in_cartodb.html).

### Why is my URL-imported table empty?

When you create a table in the CartoDB Editor by importing data from a public URL, in some cases an empty table will be generated if the proper files are not imported from that data.

Download the URL file and check that it contains information. If the URL provides you with a .zip containing more than one file, CartoDB will only upload one of them. To create your table properly you can import only the data file you need via CartoDB’s Import window (keep in mind that if you’re working with a shapefile it’s components must be uploaded in one [zip](http://docs.cartodb.com/tutorials/import_shapefile_in_cartodb.html). A list of data file formats that CartoDB accepts for upload is [here](http://docs.cartodb.com/cartodb-editor.html#supported-file-formats). We also recommend checking our [Common Data](http://docs.cartodb.com/cartodb-editor.html#common-data) section to see if your public URL’s data is already easily available through our site.

## Visualizations

### How to add my own images to infowindows?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/image-infowindow.png' | prepend: site.baseurl }}" alt="How to add my own images to infowindows" /></p>

In order to use your own images for customizing your infowindows at CartoDB, go to the infowindow preferences menu and select "image header" in the Design option. Now you have to be sure you have a column in your dataset of type 'string' whose content must be the URL of the image you want to show in the header. Then, you have to place this column at first place in the infowindow preferences and, of course, you should also activate the specific column of the URL to be shown.

### How to customize infowindows?

The HTML code of the infowindows can be edited directly in the "Custom HTML" option.  This lets you tweak content layout, write static content, and embed external resources. 

You can check our blogpost about this topic here: http://blog.cartodb.com/post/61664564416/full-editing-of-infowindow-html

### How can I delete legends or combine them?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/delete-legends.png' | prepend: site.baseurl }}" alt="How can I delete legends" /></p>

You can just disable a legend from a layer by applying to it the "none" template. If you want to merge the legends, you will have to perform a "Custom legend". This option is available in the Template selector of the legends wizard.

### How to modify the size of the marker in a non-bubble map?

​First of all, select on the wizard the Category map or the one you are interested in, and customize it to correspond to what you want to do referring categories.
​
​With respect of the size, you just should add this line to the CartoCSS window inside the general parameters of your table:
​
{% highlight scss %}
#your_table_name {
  marker-width: [your_number_column]/1000;
}
{% endhighlight %}
​
​Notice that in the code it is divided by 1000 because the values of the column are very big. You can adjust this to your data by applying divisions or multiplications depending on how you want to visualize the points.

### How to create custom filters for a visualization?

In order to create your own filter for interacting with your visualizations you would need to use our CartoDB.js Javascript API.

You can find an example [here](http://cartodb.github.io/cartodb.js/examples/filters-template/) ([source code](https://github.com/CartoDB/cartodb.js/tree/develop/examples/filters-template))

### How many layers can my visualization have?

The maximum number of layers per map depends on your CartoDB plan. For Magellan plan, this maximum is 4. You can get up to 5 layers with the John Snow and Coronelli plans, and 6 with the Mercator plan. 

If you have special needs for layers, contact us at support@cartodb.com and we'll see how we can help you.

### How to create dotted lines in CartoDB?

It is possible with the property 'line-dasharray', example:

{% highlight scss %}
#your_table_name {
  polygon-opacity: 0;
  line-color: #FF6600;
  line-width: 1;
  line-opacity: 0.8;
  line-dasharray: 10,4;
}
{% endhighlight %}

[Documentation](https://www.mapbox.com/carto/api/2.3.0/#line-dasharray)

### How can I get rid of the white border of the points in the map?

Just go to the CartoCSS tag and look for this line:

{% highlight scss %}
#your_table_name {
  marker-line-width: 0;
}
{% endhighlight %}

​In your case, it will have a value different than 0, so you should only give to it a zero-value. 

### Can I have different geometries in the same layer?

No. Each layer is related to some kind of geometry, so if you need to map polygons and points in the same map, you should use two different layers for each one of them.

### How do I remove the CartoDB logo from my map?

All maps created with CartoDB have our “Powered by CartoDB” logo in the bottom left corner by default. If your account uses our Coronelli, Mercator, or Enterprise plans, you have the option to remove this branding. Just click on the Options button in your Map View and toggle CartoDB Logo to it’s gray OFF state. "Powered by CartoDB" will no longer appear in your visualization.

<p class="wrap-border"><img src="{{ '/img/layout/faqs/remove-logo.png' | prepend: site.baseurl }}" alt="Remove CartoDB logo" /></p>

### Why is my infowindow showing an error?

If you're working on your visualization through a connection which is behind a firewall or proxy, some requests may be blocked by it. If requests are blocked some parts of your map will not load. In some cases this can mean that the information you’re trying to show in your infowindow will not appear.

A solution for this is to use an HTTPS connection. HTTPS will encrypt your data, so that your firewall or proxy won’t block those specific CartoDB requests.

### Why are my map labels cut off?

If some of your label's words are appearing cut off at tile edges, this can be caused by too small of a buffer area in your visualization. To fix this you need to increase the buffer-size value. Click on the CartoCSS button in Map View at the top of the CartoCSS code include:

{% highlight scss %}
Map {
  buffer-size:128;
}
{% endhighlight %}

Take into account that in order to be valid, the buffer-size value needs to be a power of 2.

## Manipulating your data

### How do I create an animated map?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/torque.png' | prepend: site.baseurl }}" alt="Torque" /></p>

If you have a dataset that contains a column which describes the date when an event occured, you can map this dinamically by using the Torque option that appears in the wizard of the visualization. Just make sure that in the configuration of the Torque map you select the correct column with respect to which you want the map to be changing over time.

### How can I show only one country/area/region on a map?

If you would like to have a map featuring only one region or a given set of boundaries, the easiest place to start is by using the SQL wizard in your editor. You could use a SQL statement like: 

{% highlight sql %} 
SELECT * FROM tablename WHERE name = 'country/region/area'
{% endhighlight %}

<p class="wrap-border"><img src="{{ 'img/layout/isolate_country.png' | prepend: site.baseurl }}" alt="How to isolate country/area/region" /></p>

You would then create a new dataset from query and choose a solid color as your base map so that only your selected area will be displayed. 

If you are comfortable working with the the viz.json object, you can add some custom code to your JS file to set the zoom level and disable panning. An example of this can be found [here.](http://bl.ocks.org/iriberri/3c1b32c58a5fe4f4a886)

### How can I have interactivity in a torque layer?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/torque-interactivity.png' | prepend: site.baseurl }}" alt="How can I have interactivity in a torque layer" /></p>

For the moment, torque layers have no interactivity. The trick that you can do is to have two layers:

- One static layer where the marker opacity is really low (almost invisible) with infowindow enabled.
- One torque layer

In order to keep all points on the map, just use the cumulative option.

### Can I calculate from/to routes with CartoDB?

CartoDB doesn't include a functionality for performing routing between different locations nor getting real driving distances for those routes. If you need to include or construct that kind of information for your spatial application, we recommend you to use external APIs as the ones from [Project OSM](http://project-osrm.org/) or [HERE Maps](http://developer.nokia.com/community/wiki/HERE_Maps_API_-_Advanced_Routing) and integrate their results with CartoDB.

## Basemaps

### How can I use NASA imagery?

Using NASA imagery as your basemap is as simple as any other feature on CartoDB. It will take you just seconds to be up and running. In Map View, click on the basemap selector, select “Add yours”, and there you’ll see a NASA tab. In this tab you will have a date selector, where you will be able to choose the day you want to use, as well as a day/night option.

### I have topographical maps in JPG and pdf formats. How can I convert these to add as base layers on the map?

there is not a direct or easy way to do that from CartoDB. What I recommend you is to use CartoDB.js in combination with Leaflet to create overlays with images.

Here you can find an example:

- [Leaflet](http://leafletjs.com/reference.html#imageoverlay)

## Sharing maps

### How do I share a visualization?

Once you have created and customized your visualization, you just should click on the "Share" option.

There are different ways of sharing a visualization:

- **Get the link**
  Used in order to share directly your map as it appears in your CartoDB public profile.

- **Embed it**
  HTML code to embed the map in your site. This is very useful for putting interactive maps of your data in your website or blog.

- **Get a simple URL**
  Used in order to share the map itself.

- **API**  
  Gives you a link to your viz.json. You will need this URL if you are working on a more advanced way using CartoDB.js.

### How do I embed a map in my site/blog?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/share-site.png' | prepend: site.baseurl }}" alt="How do I embed a map in my site/blog" /></p>

The easiest way is embedding a visualization by means of copying the HTML code that is provided in the "Share" option. 

You will be able to insert CartoDB maps in WordPress, Joomla, Drupal, etc. if you just include the iframe in a HTML code editor.

### How to print maps in CartoDB?

In the map view of your visualization, the option "Export image" will allow you to get a .png image from your map. Don't forget to add proper attributions if you publish your map as an image! You can find more information about our Static Maps in [this blogpost](http://blog.cartodb.com/static-maps/).

### How can I set the position of an embedded visualization?

In order to set the initial position of a map that you want to embed, you just need to adjust this position and the zoom value in the  map of the visualization. If you want to edit the way that your links and embed maps are centered/zoomed after sharing, you can add the zoom, latitude and longitude parameters to the simple URL as follows:

{% highlight bash %}
http://<username>.cartodb.com/viz/<viz_id>/embed_map?zoom=3&center_lat=0&center_lon=0
{% endhighlight %}

You can also use the same trick in the HTML code that is given to embed your maps.

In case you're using [CartoDB.js](http://docs.cartodb.com/cartodb-platform/cartodb-js.html), you can include these values in the options of the createVis method:

{% highlight javascript %}
cartodb.createVis('map', 'http://documentation.cartodb.com/api/v2/viz/2b13c956-e7c1-11e2-806b-5404a6a683d5/viz.json', {
  center_lat: 43.90,
  center_lon: -97.55,
  zoom: 5
});
{% endhighlight %}

## Your account

### What are the geocoding credits?

The geocoding credits are the quota for performing geocodings. With our geoocoding service you can translate addresses into coordinates for points that will be shown in your map. Each geocoding wastes one geocoding credit, so if you geocode a table with 100 rows, 100 credits will be wasted.

You should take into account that the georeference tool that converts ZIP codes, IP addresses, counties, countries or cities doesn't count as geocoding but as georeferencing, so this way, you will be able to georeference your data in the map without using the credits.

Each one of our plans include a free quota of geocodings, but extra charges will be applied if this quota is exceeded according to our Terms of Service.

### What does the table quota mean for the different plans?

Several tiers of usage are available for CartoDB, ranging from a free account to a fully dedicated instance. The difference between these accounts is in their physical storage size, memory size, and several CartoDB features. You can find full details on these differences over on the [pricing](http://cartodb.com/pricing) page.

### What is a map view?

A new map view happens everytime a visualization is shown. In a very simple way: map views count how many times your map has been visited, regardless of if it was shown in your public CartoDB page, in the CartoDB Editor, or if the map was embedded in your own site.

### How can I know how many map views has a specific visualization?

In the "Visualizations" page you can find graphs of the individual map views for each of your visualizations. If you hover the graph, a popup will appear showing the total map views of the month and of the day for a specific visualization. 

### I'm a student/researcher, do you offer discounts for education?

In CartoDB we have a special pricing for academic purposes. We offer a FREE Academy plan that allows you to have , as well as a discount of 20% in the rest of our plans. Pricing page for education can be found [here]({{ '/industries/education-and-research/' | prepend: site.cartodb-baseurl }}).

### Which are the special plans for journalism of CartoDB?

If you are a journalist and you have special needs about CartoDB, let us know at sales@cartodb.com

### What does the "Removable brand" feature mean?

<p class="wrap-border"><img src="{{ '/img/layout/faqs/share-logo.png' | prepend: site.baseurl }}" alt="What does the Removable brand" /></p>

From the Coronelli plan you will be able to remove CartoDB logo and the "Create your own custom maps with CartoDB" text will not appear below your embedded visualizations if you don't want to. For removing the logo, just deselect the option "Logo" in the sharing wizard. Take into account that this option will be only available if your current plan includes the "Removable brand" feature.

## GDrive

### How can I unlink my GDrive account from CartoDB?

In order to unlink your Google Drive account from the importing tool of CartoDB, you will have to deny the permissions in the account settings of your Google account.

### I'm getting "There was an error trying to get your service token or you didn't finish the oAuth process. Try again please." errors

That error is caused by the permission configuration at your GDrive Google account. Try this:

* Make sure that pop-up windows are enabled in your browser as the authorization window is opened in one of them. Your browser will raise a warning about this if you have pop-ups blocked: enabling them temporarily for the CartoDB site will solve the issue.
* If you're using a corporate account:
  * Please check with your administrator that you have Drive enabled.
  * Ask your administrator whether "Allow users to install Google Drive apps" is enabled at "Google Apps > Settings for Drive".

## Other technical questions

### Can CartoDB display real time data from a SQL database connection?

Unfortunately, CartoDB doesn't allow reading data from a SQL connection in real time. Nevertheless, you can use the SQL API for writing data into CartoDB.

### How can I use CartoDB.js and Mapbox.js together

If you want to use all the features of CartoDB (infowindows, legends, dynamic layers) with Mapbox basemaps take a look at the following example:

- [http://bl.ocks.org/javisantana/7200781](http://bl.ocks.org/javisantana/7200781)

If you want to use Mapbox.js features and show a CartoDB layer (without any cartodb feature) take a look at the following example:

- [http://bl.ocks.org/javisantana/f485d193884983820cd3](http://bl.ocks.org/javisantana/f485d193884983820cd3)

### I have a column with a GeoJSON, how can I set the_geom value to this?

Just modify the following query with your values:

{% highlight sql %}
UPDATE your_table SET the_geom = st_setsrid(ST_GeomFromGeoJSON(your_GeoJSON_column), 4326)
{% endhighlight %}

### Does CartoDB work offline?

Right now CartoDB cannot work without connection to the internet. The application uses several services and libraries that cannot be hosted locally.

All we can recommend you regarding this is to install the software yourself as it is open source.

### Which are the supported browsers?

You have to differentiate between the authoring tool which is the place where you perform the maps, which requires modern browsers, and the published maps, which people consume. Those maps actually work down to IE7 in most cases. 

### Has CartoDB multi-user functionalities?

If you are interested in multi-user accounts, we offer Teams through our enterprise plans, just let us know at sales@cartodb.com.

### Do Torque maps move polygons/polylines?

The Torque wizard is only available for **points**. So, Torque doesn't work with polygons. 

But, fortunately, there are other options that you could use to show different polygons with respect of time:
We offer a Javascript API, [CartoDB.js](http://docs.cartodb.com/cartodb-platform/cartodb-js.html), that you can use to add interactivity to your visualizations. In combination with our [SQL API](http://docs.cartodb.com/cartodb-platform/sql-api.html), you could build a time-slider and show different polygons depending on how they evolve with respect to time.
You can check an example in this [link](https://github.com/CartoDB/cartodb.js/blob/develop/examples/time_slider.html).


