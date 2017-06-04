---
layout: post
title:  DogTracker => for a quantified dog
date:   2017-06-04 16:27:41 +0000
---


I remember when the first Fitbit came out--I thought it was neat,
but the novelty wore off pretty quickly.  Great, I walked 8k steps today,
what does that mean?!  A few years ago before activity/gps trackers for dogs
came out, I thought it would be neat to build a web app that takes data from
the Fitbit API and asks the user if they took their dog on x minutes of walking etc.

## Data Modeling

Naturally this made an interesting idea for my Sinatra project (minus the Fitbit API).
The data modeling is straight forward if you assume the user only has one dog--but that's not very realistic.
Instead a user can have many dogs and activities.  Both dogs and activities belong to users.
An activity can have many dogs, so I needed a join table.

![screen shot 2017-06-04 at 12 21 29 pm](https://cloud.githubusercontent.com/assets/8889403/26763418/a2cc0656-4920-11e7-9a5f-e6992a9b2762.png)
![screen shot 2017-06-04 at 12 20 21 pm](https://cloud.githubusercontent.com/assets/8889403/26763416/95ac1b96-4920-11e7-897c-dcd8f3d066cc.png)
![screen shot 2017-06-04 at 12 21 39 pm](https://cloud.githubusercontent.com/assets/8889403/26763422/adc473a4-4920-11e7-9472-3a8b868b6632.png)

## Validations

Next hurdle, a dog name should be unique--easy enough, ActiveRecord provides a
uniqueness validation.  Validations default to application scope, so if user1 has a dog named Bob, and
user2 also has a dog named Bob it will throw an error.  Instead the validation must be
scoped to the user.

![screen shot 2017-06-04 at 12 20 21 pm](https://cloud.githubusercontent.com/assets/8889403/26763416/95ac1b96-4920-11e7-897c-dcd8f3d066cc.png)

## Adding ActiveSupport
In preparation for moving on to the rails section for the curriculum,
I've been poking around the rails documentation.  ActiveSupport is a rails
sub-gem that provides language utilities.  For example, how does ActiveRecord know
that if a table is called activities the model will be activity?  I added ActiveSupport
for one method: humanize (not efficient!). From rails docs:
![screen shot 2017-06-04 at 12 19 37 pm](https://cloud.githubusercontent.com/assets/8889403/26763410/83bc8808-4920-11e7-8a32-a5ffa33b3c3d.png)

