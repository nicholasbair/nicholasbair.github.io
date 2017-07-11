---
layout: post
title:  "Astrid Clone... maybe"
date:   2017-07-11 19:56:23 +0000
---


At the start of my first rails portfolio project, I set out to make a clone of the now shut down task app Astrid.  Yahoo bought it several years ago and shut it down--although it looks like someone recently resurrected the project.  As I started building out the basic app, I realize that I find a kanban more useful and decided to head that direction instead of task assignment a la Astrid.

Further, I decided it would be cool if the user had the option of providing their time zone and cell phone to recieve text reminders of an impending deadline.  To start I created the requisite table and model for a deadline, and built the association to the list.  The user will create a deadline when a task is created, in the same form.  So I added a writer to create a deadline with nested attributes and a helper method to parse the time value and convert to UTC (server time).

![screen shot 2017-07-11 at 3 47 34 pm](https://user-images.githubusercontent.com/8889403/28087538-c9f882d4-6650-11e7-8b7f-6b4655a692a1.png)

Next in the deadline model, I created a method to send a text message view the Twilio API.  The only problem is that there is no scheduling functionality.  Enter delayed job, one of several gems that allow tasks to be handled asnchronously.  After a deadline is created the reminder method is called, serialized and put into a job que to be run at the time specified in a helper function.

![screen shot 2017-07-11 at 3 48 22 pm](https://user-images.githubusercontent.com/8889403/28087552-ddc01534-6650-11e7-8a97-a1e9d30b2e0e.png)

Sounds great so far, but what if a user sets a deadline in the past?  They would immediately recieve a text reminder.  I added two validations on the deadline model, one checks if the deadline is in the future, and the other checks if the reminder time is in the future.  A deadline is always created by an instance of a list via the custom writer, so list will need to know to run the validations.

![screen shot 2017-07-11 at 3 48 57 pm](https://user-images.githubusercontent.com/8889403/28087591-ff346cce-6650-11e7-8192-c7d57446d55d.png)

![screen shot 2017-07-11 at 3 49 40 pm](https://user-images.githubusercontent.com/8889403/28087615-16bc4218-6651-11e7-9f28-5b98388bdb52.png)




