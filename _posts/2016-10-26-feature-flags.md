---
layout: ryan
title: "Feature flags for decoupled delivery"
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

# Feature flags for decoupled delivery

When dealing with an application at scale it can often be nerve wrecking introducing new features. Often times features are developed and ready to go, but the clients of the application are not ready for the new feature. We can use feature flags to mitigate a substantial portion of risk. At the most basic level, feature flags decouple delivery and deployment. We can get the new code on our servers, without launching new features.

<img src="http://i.imgur.com/04ILZ8V.jpg" width="70%"/>
<center>*This is a nice flag as well! If you find yourself near Calgary Canada, Hike Prairie Mountain!</center>

#### This may not initially seem like a huge gain, but it provides us with several benefits:
- The code in production is inclusive of all features which are ready, no more long running branches.
- The removal of these long running branches quickly leads to having smaller, more frequent, stress free deployments.
- We can easily roll back features which have issues.
- We can choose who we deploy to. 

<img src="http://i.imgur.com/NapmE5A.jpg" width="70%"/>
<center>*Keep your clients happy!*</center>

#### Releasing to a Subset of clients quickly has huge advantages:
- Easy A/B testing with new features.
- Ability to do Phased Rollouts
- We can release features which are only partially complete (Often the case for multi-language applications)
- Internal releases and Beta Testing

### Strategy
Feature flags can add an additional layer of complexity to the application and management of potentially 1000s of feature flags can become overwhelming. To deal with this added layer of complexity often a more comprehensive admin style panel for your application is needed. My advice is to strive to promote features to work globally with the full userbase when possible and avoiding having a large amount of feature flags in various states. 

### Libraries
Essentially all major web frameworks have libraries to easily add feature flags. While it is very simple to roll your own feature flag library, having a pre-rolled solution which includes a nice UI and some general default settings can bring the ability of feature toggles into your application. I can personally recomend the [Flipper](https://github.com/jnunemaker/flipper) gem for Ruby applications.
