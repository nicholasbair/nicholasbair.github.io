---
layout: post
title:  Scrape > API
date:   2017-05-11 17:35:49 +0000
---


This is my second attempt at a career change.  Naturally, I've been thinking a lot about the process for finding a good job.

In 3 months, when I am roughly half way through the program, my goal is to have a list of companies to cold call, network, and job hunt.  My current process for gathering this data is multiple job alerts on job sites like Indeed.  Every day I skim the results and keep a list of interesting companies in Google Keep.

*Super manual, inefficient and well...gross.*

This was the catalyst for my CLI data gem project.  What if I could scrape job postings from Indeed and write them to a spreadsheet? That would not only save time, but also allow me to do fun things in excel like pivot tables.  

*Yes, I am aware Indeed has an API--but scraping is fun.*

I used bundle to generate the gem boilerplate code to start.  Next, I wrote out a general descriptions of what I wanted to accomplish, the UI, data, and a list of gems for writing data to a spreadsheet (notes.md).  I broke up my next few steps into issues and posted them to Github using waffle.io (great kanban board for keeping organized).

Breaking the project down into small pieces helped me to iterate often, have achievable goals, and keep on track.  Its easy to google an issue and get distracted by a cool idea.

The biggest technical hurdle was scraping more than just the first page of search results from Indeed.  To accomplish multipage scraping, each scrape instance is instantiated with a counter variable.  During the first scrape, query terms gathered from the user are parsed and appended to the url, the site is scraped including the href for the next page using the counter variable as part of the search.  The scraper is called with the next page's href until there are not anymore pages (hello recursion! => ./lib/scrape_indeed/scrape.rb).

View the project [here](https://github.com/nicholasbair/scrape_indeed).
