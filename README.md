# extending-datasette

This assignment explores some of Datasette's features, including how it handles address data and 

Follow along, adding code and files as instructed.

### Setup

We'll need to install some things using pip, so run these commands:

* pip install sqlite_utils datasette geocode-sqlite
* datasette install datasette-codespaces
* datasette install datasette-cluster-map
* datasette install datasette-configure-fts

### Geocoding

One of the files in the data folder is called `refunds.csv`. It contains ~1,000 records of refunded political contributions from Maryland donors, and has street addresses. We'll geocode them using [geocode-sqlite](https://github.com/eyeseast/geocode-sqlite) and then display them on a map using the [Datasette Cluster Map plugin](https://datasette.io/plugins/datasette-cluster-map).

First, let's create a database using sqlite-utils:

sqlite-utils insert extend.db refunds data/refunds.csv --csv

then we'll look at:

datasette serve extend.db

It has multiple address-related columns: address_one, city, state and zip. We'll need to combine them when geocoding, and we'll use OpenStreetMap's free Nominatim geocoder, replacing "first-name" in the user-agent with your first name:

geocode-sqlite nominatim extend.db refunds \
 --location="{address_one}, {city}, {state} {zip}" \
 --delay=1 \
 --user-agent="newsapps-first-name"

While that's running, we'll look at some data that already has latitude and longtitude values.

### Admissions Data

Using the admissions.csv file in the data folder, load that file into our extend.db database. To that, you'll open a new terminal window using the + sign next to the word "bash", then run this command:

sqlite-utils insert extend.db admissions data/admissions.csv --csv

Then run datasette serve extend.db so that you can browse the data. What do you notice about the map? What do the pins actually represent?

Try faceting on different columns. The goal is to find something interesting and then describe in this file what information would be most useful and how you might organize it. In other words, draft a basic proposal for an app that accomplishes one or more tasks for its users, the potential features it would have (including visuals). You don't have to *build* those things, just describe them, and use examples of what a user might do. The more specific the better. If you find a particular view of the data useful, describe that (you should include the URL from your codespace). If there are things you would do with this data that requires additional information or data, tell me about that, too. Put that in the space below.

## Notes
The pins represent the data on the table: the number of students who applied and were admitted from various high schools over a period of time. This data can look different on the map if rearranged (top to bottom, filter).
When you click on the pin, it breaks down into sub-clusters, until it can show individual points for all the data included in the table below.
The facet button helps narrow the data down a lot more. If a user facets by school, for example, and clicks on what school they want to facet by, the map displays only information relating to the school.
I faceted by school and chose to look at Aberdeen High School. I filtered to get only cases where more than 40 people were admitted. Although the high school appeared 10 times, there were only two years where more than 40 students were admitted. I had only two points on the map: one for each year. Link here: https://musical-waddle-97qp9qwwvpgr27x7j-8001.app.github.dev/extend/admissions?_sort=rowid&_facet=school&admitted__gte=40&school__exact=Aberdeen+High+School

Over the ten-year period under review, only less than 10 Kenwood High students got admitted. In some cases, there were no applicants for the school at all. Link here: https://musical-waddle-97qp9qwwvpgr27x7j-8001.app.github.dev/extend/admissions?_sort=rowid&_facet=school&_facet_size=max&school=Kenwood+High


#### ADMISSIONS APP IDEAS
Using this dataset, one idea would be an app that pulls up acceptance rates for high schools in counties over a period of time. This could be useful for parents looking to make enrollement decision for their kids or wards, or guide them through the process.
The app would take the form of a search bar, with easy to recognize symbols so that users can navigate easily even when on autopilot.

### Full-Text Search

Next, we'll try out full-text search in Datasette using SQLite's built-in full text search engine, using some recent congressional press releases. Let's make a table from those:

sqlite-utils insert extend.db releases data/releases.csv --csv

Serve the database so you can see what we're working with. Which columns should be make searchable? In order to set that up, we need to visit a specific URL in Datasette. Remove everything after .github.dev and paste this: /-/configure-fts/extend

Then scroll down to the releases table and pick "title" and "body" and then hit the configure button at the bottom.

Now let's do some searching across those tables and let's discuss how full-text search is different from filtering.

When we're done, let's check on our geocoder progress.