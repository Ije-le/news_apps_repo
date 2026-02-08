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

#### ADMISSIONS APP IDEAS



### Full-Text Search

Next, we'll try out full-text search in Datasette using SQLite's built-in full text search engine, using some recent congressional press releases. Let's make a table from those:

sqlite-utils insert extend.db releases data/releases.csv --csv

Serve the database so you can see what we're working with. Which columns should be make searchable? In order to set that up, we need to visit a specific URL in Datasette. Remove everything after .github.dev and paste this: /-/configure-fts/extend

Then scroll down to the releases table and pick "title" and "body" and then hit the configure button at the bottom.

Now let's do some searching across those tables and let's discuss how full-text search is different from filtering.

When we're done, let's check on our geocoder progress.