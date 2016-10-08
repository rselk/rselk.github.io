---
layout: ryan
title: "A GraphQL Primer"
description: ""
category: 
tags: []
---


# A GraphQL Primer

I have been researching different methods for architecting APIs lately and alternatives for the now ubiquitous REST style. I came across [GraphQL](http://graphql.org/) is a project by Facebook which aims to make API development far more friendly to consumers. 

<style>
img {
    display: block;
    margin: 0 auto;
}
</style>
<img src="http://i.imgur.com/X9d5dxz.png" width="50%"/>


### The Problem

Let's say we are writing a new Android app for a fictional Twitter clone and we are working on a view which displays all tweets and comments for those tweets. To get the data which we need, we likely have to send requests to multiple endpoints. In a simple example, we might have 2 requests which may look like this:

- One request to get the most recent tweets
- One request to get the comments for each tweet

We would make these requests to the Twitter API, parse them, and use the data gathered in the way which we saw fit. With only 2 requests this is quite simple but often 10+ requests can be required on large dashboard type views. This step can quickly become very complicated and also difficult to maintain.

The multiple request paradigm leaves both the consumer and provider of the API in a difficult position. The consumer and provider of the api are tightly coupled, one cannot change without significant changes in the other. If Twitter wants to change any of the responses on these endpoints they are forced to version their API. If in the future the Android application wants to consume some data only available in these new versioned endpoints, it often is the case the Android app starts dealing with multiple API versions. Pretty gross.

### The Solution

What’s if instead of sending the API multiple requests, We could just tell the API exactly what we want the response data to look like? Enter GraphQL. 

GraphQL allows us to provide the API with a representation of data which we would like and have the API return just that. Previously we had to send multiple requests, parse the responses, and move the data around so that it could be represented easily in our views. With GraphQL we simply give the server a representation of our data and it can provide a response. 

We will end up sending the API server something like this:

~~~ javascript
{
  tweets {
    id,
    body,
    author {
      id
      name
    },
    comments {
      id,
      body,
      author {
        id
        name
      }
    }
  }
}

~~~

And we will receive a response something like this:


~~~ javascript
{
  "tweets": [
    {
      "id": 123,
      "body": "example tweet body1",
      "author": {
        "id": 123,
        "name": "JillSmith"
      }
    }
  ]
  "comments" [
    {
      "id": 123
      "body" "example comment1",
      "author": {
        "id": 123,
        "name": "BobJohnson"
      }
    }
  ]
}

~~~

Being able to declare what structure we want the returning data to have is a major deviation to traditional API design. This change of design can allow consumers to write far more efficient queries, send less requests, and let the server handle more of the problem. The time between sending the query and displaying the data to the user is vastly decreased. 

The future of GraphQL is interesting and I think there are still some open ended questions on how people are going to implement it. It seems likely that users of a GraphQL API will eventually create very inefficient queries which could put substantial load on the server. If these concerns can be mitigated through use of timeouts, server optimization or even a more sophisticated query validator then I think we will see many APIs produced in this manner in the future. 

