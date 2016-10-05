---
layout: ryan
title: "Working with legacy Rails - Part 2"
description: ""
category: 
tags: []
---

# Working with legacy Rails

### Part 2

In [Part 1](http://ryanselk.com/2016/10/01/legacy-rails-p1) in this 2 part series of working with legacy rails applications I covered Metrics, Logging, Test suite optimization and the addition of a service layer. 

<img src="http://i.imgur.com/izErBeA.gif" width="80%"/>


*I personally think Dilbert worked on old Rails apps"* -- [Dilbert 2006-12-08](http://dilbert.com/strip/2006-12-08)


Today I am going to cover Query Optimization, Updating Ruby and Rails, and dealing with Gem dependencies. 


#### Query Optimization

The legacy application mentioned throughout this series had several queries used for analytics which would take so long that they timed out. We had the timeout set to 30 seconds via nginx, so this was obviously a major problem.

One short term solution to this is to throw the queries on a higher priority background processing queue and then use a gem like [Pusher](https://github.com/pusher/pusher-http-ruby) to notify the user when they are complete. It also is fairly easy to integrate a progress bar using this setup. 

N+1 queries are bad, everyone claims to know this, yet every large rails project a few seem to sneak in. When working with a legacy app often the models get far to large and fixing a few N+1 queries can be a massive performance boost. Become well read on #includes #preload and #eager_load. 

Indexes are usually the first thing covered in any blog about query optimization so I will be brief here. When deciding to add an index, it really needs to be thought out. Sure an index is all fine and good when you are dealing with small amounts of data, but using many indexes at very high scale you will run into problems with slow writes. It useful to look through significant amount of metrics and get very comfortible with [EXPLAIN](https://www.postgresql.org/docs/9.0/static/sql-explain.html). 

If you are just starting this step, I recomend looking into the [Bullet](https://github.com/flyerhzm/bullet) gem as a massive time saver. The great thing about the bullet gem is that not only can it find places to eager load data, it finds places where it was unnecessary loaded. 

Finally, as a general refactoring tool I am a huge fan of [Query objects](http://www.martinfowler.com/eaaCatalog/queryObject.html). Some queries are just going to be push the limits of ActiveRecord and may require raw SQL or at least dropping down to Arel. For these queries I think it's a great pattern to move them to query objects with sane APIs which can be used across the app. 


#### Update Ruby, Rails

First let's start with the Ruby upgrade. I am assuming MRI ruby at this point. Unfortunately sometimes there can be a few middle steps depending on how confident you want to be with the upgrades. 

Let's look at part of the [Travis build matrix for Rails 3.2](https://github.com/rails/rails/blob/3-2-stable/.travis.yml):

~~~yaml
language: ruby
sudo: false

script: 'ci/travis.rb'
before_install:
  - gem install bundler
rvm:
  - 1.8.7
  - 1.9.2
  - 1.9.3
  - 2.0.0
  - 2.1
  - 2.2
~~~

As you can see it was tested for up to Ruby 2.2 If you are on Rails 3.2 then an upgrade to 2.2 should in theory be fairly unsurprising. 

If you are on Rails 3.0 this can be quite a bit more complex. It's my opinion that if you are on Rails 3.0 you need to go to 3.2 first, then then 4.2, then finally 5.x.

This will require research on your part to determine how you can update Rails and Ruby without stepping on each other. An upgrade Path for a Rails 3.0 app on Ruby 1.9.3 might look like:

- Upgrade to Rails 3.2
- Upgrade to Ruby 2.2
- Upgrade to Rails 4.2
- Upgrade to Ruby 2.3

I would release each one of these upgrades independently. It’s generally good practice to bring software into a production environment with each change in infrastructure. 

_he actual upgrade process for Ruby and Rails should not be all that bad if you have enough test coverage. Some of it depends on how much of the application relied on features which have been deprecated. The good new is that you can do lots of this in steps as well using intermediate gems like [Protected Attributes](https://github.com/rails/protected_attributes) and [ActiveRecord Deprecated Finders](https://github.com/rails/activerecord-deprecated_finders).

This is likely something that is going to take you a few days at least to upgrade depending on the size of the application.


#### Gemfile updates

Updating the Gem dependencies on a legacy app is the most challenging part. It's not uncommon for an old Rails app to have over a two hundred gem dependencies (not counting, their dependencies). Often some of these old Gems can be of suboptimal quality and are no longer maintained.

You are going to have to audit your Gemfile, there is really no way around this. You will need to find out what Gems are no longer maintained, what Gems have moved repositories, and what Gems are no longer needed. 

My strategy, and this is likely controversial, is the following:

- Get test suite up to speed
- Update ALL the gems in a single go to their latest versions
- Get the app to boot doing whatever is needed. This will likely require reading through a few stack traces and modifying initializers.
- If a Gem is causing problems, bring the version back down.
- After you finally get the app to boot, run the suite and fix all of the errors. For reference the suite on the app referenced throughout had a failure rate of around 20%. 
- Get to work and don’t get discouraged.  

Once the test suite is passing, for a large upgrade like this you are likely going to want user testing. If you have access to a QA department they will be very useful here.

I recommend spinning up an instance of the application with the new versions of code on it and directing a small percentage of the traffic to this instance. If you are going to go this route I caution you to have a database which is extremely conservative on what it lets in. If your application relied mostly on model level validations, I would write some strict database level validations before attempting this. If the rollout is going well, eventually promote it to all the production servers.

#### Conclusion

I have covered some basic techniques for bringing a legacy Rails application up to speed. The major missing piece in this post is the front end. I feel that back end changes should be completed first as it's likely any sort of application already dealing with many users has multiple front ends it must support (IOS, Android, Web, and Public APIs).

<img src="http://i.imgur.com/Ldx0Gku.jpg" width="80%"/>

*You will make it. Its not that bad. Next time will be easier.*

My strongest advice on all of this, is to not get overwhelmed. Ruby is such a malleable language that each legacy Rails app you come across will have many different concerns and weirdness. Its often not even the fault of the previous developers that the application got into this state. Startups can move fast and demand features at a pace a development team cannot keep up with while not incurring major technical debt. An upgrade like this is a major case of paying off that debt. 
