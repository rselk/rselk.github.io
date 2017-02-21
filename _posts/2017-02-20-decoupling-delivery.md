---
layout: ryan
title: "Decoupling Delivery and Deployment"
description: ""
category: 
tags: []
---

<style>
img {
    display: block;
    margin: 0 auto;
}
</style>

# Decoupling Delivery and Deployment

When dealing with an application at scale, introducing new features can be risky. On smaller-scale applications, deployment is usually tightly coupled to feature delivery. When new code is put into production, the new features are live. Using feature flags can mitigate a substantial portion of risk.

<img src="http://i.imgur.com/jpKrUmt.jpg" width="50%" hieght="50%"/>

The tight coupling between deployment and delivery is incredibly restricting. By decoupling deployment and delivery, we can:

- Limit feature rollout to specific users of the system
- Gain the ability to toggle features which are behaving unexpectedly
- Easily A/B test new features
- Decrease the number of long-running, work in progress feature branches

### Real world example

Recently, I was working on a fairly typical SAAS application. The application in question was [multi-tenanted](https://en.wikipedia.org/wiki/Multitenancy), had a very globally distributed user base and was fully internationalized. A new data warehouse-backed analytics dashboard was created to provide users with near real-time information about how they use the application.

The feature was developed by a small team over a few weeks and an initial version was created. This initial feature was limited in scope and was not suitable for release to the entire user base. We still had to hire translators, inform major clients and stakeholders and analyze usage patterns of the feature. <Paste>

Through the use of feature flags, and in this case, heavy use of the [ Flipper Gem]( https://github.com/jnunemaker/flipper ), the following occurred:

- Code was deployed into production behind a feature flag
- We granted access to the feature internally, as well as to users who had enabled our “experimental” features
- We collected data on usage patterns
- We found several areas for query optimization
- We changed some of the copy based on real feedback

We had several deployments during this trial phase of the feature until we were happy with it. At this point, we hired translators and began to organize the rollout. We knew the exact date and time the feature would roll out, making it easy to notify stakeholders, inform the user base and promote the feature.

When the date finally came, we flipped the switch, which gave everyone access to the feature. We were fully confident that it was stable, tested and would be well received; the feature was a success.

### Strategy
Feature flags can add an additional layer of complexity to the application, and management of potentially thousands of feature flags can become overwhelming. I strongly advise that you strive to fully deliver the feature to 100% of the user base. Having features only available to 90% of your users will cause major complexity and confusion.

Essentially, all major web frameworks have libraries to easily add feature flags. While it is fairly simple to roll your own feature flag library, having a pre-rolled solution which includes a nice UI and some general default settings can bring the ability of feature toggles into your application very quickly.

