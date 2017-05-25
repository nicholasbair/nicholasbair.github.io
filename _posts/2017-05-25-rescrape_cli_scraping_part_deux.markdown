---
layout: post
title:  Rescrape: CLI scraping, part deux
date:   2017-05-25 01:59:28 +0000
---

Over the past few weeks I completed my first portfolio project [ScrapeIndeed](https://github.com/nicholasbair/scrape_indeed), SQL, ORMs and ActiveRecord.
You can read about ScrapeIndeed [here](https://nicholasbair.github.io/2017/05/11/scrape_api/) or watch the walk through [here](https://vimeo.com/217057373).  ScrapeIndeed included poor man's data persistence by writing data to an xlsx file.  With SQL and ActiveRecord now in my tool box, I decided to refactor my CLI gem as a weekend project: [rescrape](https://github.com/nicholasbair/rescrape).

### Goals for Rescrape
First and foremost, I wanted to add data persistence using SQlite3 and ActiveRecord.  Doing so would also allow the user to create and save searches.  I won't be job hunting for a while, but I also wanted to collect location information on the job results and be able to answer:
- How many miles would my commute be?
- How long will that drive take?
- What about in traffic?

### Process
I used bundle to scaffold an empty gem project and pulled in most of the code from ScrapeIndeed:

```
   $ bundle gem rescrape
```

Next I wrote out some of my ideas for the project, including some user stories and translated
these into issues in the github repo using Waffle.io.  You can close issues with your commit messages like so:
```
git commit -m "Refactor scraper class, closes #1."
```
![waffle4](https://cloud.githubusercontent.com/assets/8889403/26418231/6e3c6678-4089-11e7-8b65-a0ad35edfcf0.gif)

### What worked?
I struggled a little getting ActiveRecord setup--in fact as of writing this, I have not gotten rake db:create to work.  Instead I am running migrations, then seeding the DB.  Once up and running, ActiveRecord makes life pretty darn easy.

Install the ActiveRecord gem, add a db registry and a connection adapter:
```ruby
# db_registry.rb
DBRegistry ||= OpenStruct.new(test: Rescrape::ConnectionAdapter.new("db/#{DBNAME}-test.db"),
  development: Rescrape::ConnectionAdapter.new("db/#{DBNAME}-development.db"),
  production: Rescrape::ConnectionAdapter.new("db#{DBNAME}-production.db")
  )
	
# connection_adapter.rb
class Rescrape::ConnectionAdapter
  attr_reader :adapter, :database

  def initialize(database, adapter="sqlite3")
    @adapter = adapter
    @database = database
  end

  def connect!
    ActiveRecord::Base.establish_connection(
      :adapter => self.adapter,
      :database => self.database
    )
  end
end
```

### What didn't work?
One of my initial goals was to drill into each job post while scraping, grab the full job post, and save to the db.
I was able to implement, but performance suffered--it took roughly 30 minutes to scrape 500 results.

*Performance == UX.*

Prior to adding the Google Maps API I had several searches saved, and was able to run all of the searches and persist close to 4,000 results in the database.  Unfortunately, one of the APIs has a daily request limit of 1000--so once I added that functionality it limited the amount of scraping I could do.

### Interesting stuff
To start collecting commute data, I used HTTParty to make a get request to the Google Maps Directions API with the
city/state of user's home and city/state of the job.  The response contains distance, duration, and duration with traffic.

*You can pass in a traffic configuration--I am using 'pessimistic', because well... Atlanta.*

This isn't a bad setup, but it there is a shortfall.  What if the city/state of the user and the job are the same?
Our data will say that commuting from north Atlanta to downtown takes zero time!

Enter the Google Maps Places API.  We can use it to run a text search to find the job's lat/lng. Then make a request to the directions API with the lat/lng of both the job and the user.

Check out the Google Maps API's [here](https://developers.google.com/maps/get-started/).  Once you have an API key, making a get request is relatively simple--just build out a uri with your parameters.

### What's next?
CLI apps are fun, but building a rich user experience is really the web app's domain.

