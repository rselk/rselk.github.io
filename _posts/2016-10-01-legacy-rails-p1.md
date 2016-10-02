---
layout: ryan
title: "Working with legacy Rails - Part 1"
description: ""
category: 
tags: []
---

# Working with legacy Rails

Part 1

Ruby on Rails is over ten years old, with the huge surge of the language and framework in the late 2000s there are starting to be plenty of legacy rails apps out there. Dealing with a legacy rails app can be fairly frustrating to those who have not been working with the framework throughout that time. 

I recently spent 18 months working on a gigantic monolithic Rails app. Many developers with various skill levels worked on this application before me. The application had a large user base, multi local support, integrations, and was used with large enterprise clients. With large new clients on the horizon the application was in in dire need of some major updates. Following are some of the strategies which were used to bring the application up to speed. 

<img src="http://i.imgur.com/6gEb4QS.jpg=100x20" width="80%"/>


*"Programmers at work maintaining a Ruby on Rails application"* -- 
[Classic Programmer Paintings](http://classicprogrammerpaintings.com/post/142737403879/programmers-at-work-maintaining-a-ruby-on-rails)


#### Initial State of Application

- Test suite took over an hour to run, yet only had around 60% coverage.
- Very few, and very large models. Several with over 2000 lines of code.
- Heavy use of ActiveRecord callbacks
- Database missing many indexes
- High amount of raw SQL used throughout
- Behind Major Ruby and Rails versions
- Gemfile consisting of various unmaintained forks
- Average page load time over 5 seconds


#### Metrics and Logging

If you are going to deal with a legacy application which is already in production with many users, you need metrics to work off of. You are going to want to at least have access to the following:
- Page load times
- Traffic
- Slow Queries
- Most common queries
- Exception Reporting with stack traces
- What devices people are accessing your site with

Fortunately NewRelic, likely the most common tool for tracking Rails apps, does a pretty good job here. What will need to be added is additional logging. Adding substantial logging in the critical parts of the application is a very good idea. If you are planning major refactoring you are going to want to be made aware of potential errors as soon as possible. The ELK (Elasticsearch, Logstash, Kibana) combined with pagerduty is my go to here but beyond the scope of this post. 
I spent a good amount of time going through the data which was collected. Finding clients commonly used paths through the application can help to refactor. It is often surprising that parts of the application which you suspected to be very popular are hardly used and may be candidates for deprecation.


#### Deprecation
After you have a good number of analytics on the software, deprecating features is a great way to limit the scope of many refactorings. Determining deprecation candidates can be a difficult task with a large application as almost every user likely uses the application in a slightly different way. 

Often times looking into deprecating old versions of private APIs is a good place to start. If we can be certain that no other parts of the system are still consuming them, we may as well remove them to speed up our test suite and avoid dependencies they might leave on the rest of the application.

Deprecating features is no small task. This step will need serious buy in from non engineering team members. If you are hired to fix up a legacy application, my advice is to be overly cautious when proposing features to deprecate when communicating with the business side. Often the company paid a large sum of money for these features and they do not want to feel like it was wasted. 

#### Improve Test Suite

I am a big fan of improving the test suite very early in the process. This also might need a significant amount of buy in. I do not think it's possible to bring a legacy Rails app up to speed without a fairly good test suite. When dealing with a large application with minimal separation of concerns, it's difficult to have any faith in in much needed refactorings and dependency updates without a solid suite. This is a solid strategy:

- Remove all external calls to 3rd party APIs in the suite
- Have tests only hit the database when necessary. [FactoryGirl](https://github.com/thoughtbot/factory_girl) .build and .build_stubbed are lifesavers here.
- Removal of tests of deprecated features.
- Parallelization of test suite via [parallel_tests](https://github.com/grosser/parallel_tests)
- Use of managed CI server to allow split of test suite on multiple VMs ([TravisCI](https://travis-ci.org/), [CircleCi](https://circleci.com/))
- Writing missing specs, this will be an ongoing task. 

Eventually I ended up bringing the test suite from over 1 hour to around 8 minutes. The 8 minute test had far higher code coverage as well. While 8 minutes is not all that amazing, the reduction allowed for far more aggressive refactors. 


#### Addition of a Service Layer

When your User model has over 10 after_save callbacks, some of them creating new models (which may have their own callbacks!). It's almost impossible to determine what is actually going to happen when you reach for trusty ActiveRecord #save! . Combine this with callbacks which throw things on background queues and you have yourself a mess. 

By implementing a service layer into your application you can greatly reduce the coupling your models have to the actions which are performed when they are modified. The general strategy here was to do the following:

Determine the actions which the model in question is actually involved in. For instance the User model may need services such as:
- UserProvisioned
- UserAuthenticationViaSAML
- UserArchived

The addition of services like this also have the huge bonus of being far easier to test. No more dealing with after_commit hooks not firing in your test suite!

ActiveRecord callbacks are orden a huge smell in Rails codebases. They are very easy to add and often the first thing developers like to reach for. I need a very good reason to use a ActiveRecord callback. Why? Its 2016 Courage.


#### Stay Tuned

This concludes part 1 of this post. Stay tuned for part 2 which will get into Query Optimization, Updating Ruby, Updating Rails, and dealing with Gem dependencies. 

