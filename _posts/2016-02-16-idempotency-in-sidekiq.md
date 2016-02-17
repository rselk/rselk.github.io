---
layout: ryan
title: "Idempotency in Sidekiq"
description: ""
category: 
tags: []
---

# Idempotency in Sidekiq

Sidekiq is a popular ruby gem used for background job processing. In a web application you often do not want to block the current thread while performing some task. It is commonly used for sending emails, notifications and performing work which the user is not expecting instant feedback. The important thing to remember that sidekiq jobs must be idempotent or you will run into problems.

For those unfamiliar with  idempotency, I will summarize. Sidekiq does not guarantee that it will only process a job a single time. That means that it is possible to get two identical jobs running at essentially the same time. We must make sure that any sidekiq job can be ran multiple times without side effects.

# Use transactions when appropriate
Transactions are not too scary and are often a simple solution for tightly scoped jobs. If you have a job such as this:

~~~ruby
class RedeemPointsWorker
    include Sidekiq::Worker

  def perform(user_id, points_to_redeem)
    user = User.find(user_id)
    user.redeem_points(points_to_redeem)
    user.notify!(points_to_redeem)
  end
end
~~~

In this situation, if the notification throws an exception, the job will retry. The reedeem_points method will be called again and this will leave us in a terrible position with a user having redeemed points twice.

A solution in a case like this is to wrap everything in a transaction. Only if everything is successful will we update any models. There are a few ways to right transactions in Rails, all ActiveRecord models you can start a transaction from. If you are not using an ActiveRecord model you can also pass a block to ActiveRecord::Base.Transaction.

Keep in mind that ActiveRecord::Base#save also opens a transaction. Changes you make in callbacks or even nested attributes are ran inside a transaction.

~~~ruby
class RedeemPointsWorker
  include Sidekiq::Worker
  def execute(user_id, points_to_redeem)
    User.transaction do
      user = User.find(user_id)
      user.redeem_points(points_to_redeem)
      user.notify!(points_to_redeem)
    end
  end
end
~~~


While transactions can have some performance drawbacks, if your jobs are scoped tightly and not doing very expensive queries over multiple models, this is often a solution which is good enough.

# Scope jobs tightly
Jobs should ideally be very small and scoped very tightly. If you have a job which is reaching out to several different models it will likely be wise to split the job up into a series of smaller jobs. Without thinking about refactoring the code at a later date, these monster jobs have serious downsides as it's often the case that part of the job will not actually be idempotent.

It is likely these complicated jobs will run into issues at the database level and errors such as: 

~~~bash
ActiveRecord::StatementInvalid: Mysql::Error: Lock wait timeout exceeded; try restarting transaction
~~~

are common. This is occurring because the database locks rows to ensure that changes are applied consistently. When 2 database connections try to lock the same rows, but in different orders you may encounter a deadlock.

# Sidekiq unique jobs

With sidekiq enterprise a unique job functionality is added. If you cannot afford Sidekiq Enterprise, there is an alternative for this called sidekiq unique jobs.
https://github.com/mhenrixon/sidekiq-unique-jobs

What this gem essentially does is ensure that the same job with the same parameters do not get processed twice. The gem provides several ways to ensure job uniqueness which are beyond the scope of this post. The gem is middleware for sidekiq and is not all that heavy. You can specify which jobs must be unique so your existing jobs can remain unchanged.

Sidekiq middleware is very easy to tap into and rolling your own basic version of this gem will likely not be above the ability of readers of this blog. 


