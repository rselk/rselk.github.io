---
layout: ryan
title: "Background Jobs in Rails 4.2 with Active Job"
description: ""
category: 
tags: []
---

# Background Jobs in Rails 4.2 with Active Job 

Later today the second beta of Rails 4.2 will be released. Rails 4.2 introduces Active Job. Background jobs have been a part of Rails apps for years, but they have usually been implemented through a gem such as [Sidekiq](https://github.com/mperham/sidekiq "Sidekiq"), [Resque](https://github.com/resque/resque/ "Resque"), or [Delayed Job](https://github.com/collectiveidea/delayed_job "Delayed Job"). These gems can have differing APIs and the rails team has decided to implement a standard interface. Active Job is this uniform interface for interacting with these job runners. 

### Why do I need Background Jobs?
One common situation for needing background jobs is consuming external APIs. It is unrealistic to think that all of your external APIs will have 100% uptime. By allowing API interaction to happen in a background job we can manage failures more effectively. Instead of having the user wait for a task to end, we send that task to the background queue. The User can then continue interacting with the app. Background jobs also give us the opportunity to prioritize jobs which are waiting in out background job queue. In many cases using background jobs can significantly reduce the required resources of the app.

### Getting Started
Jumping into background jobs can be a little daunting for someone new to rails. Right off the bat you are urged to learn about about several new gems, key-value stores, queues, asynchronous programming, thread safety, and error handling. The good news is that learning background jobs will be very worthwhile. Many Rails apps today are, or should be, using background jobs. With Active Job now in the rails core its likely we will start to see more people using background jobs. Below contains a brief example of implementing a background job with Active Job.

### Background Job Candidates
We want our background jobs to have no additional effects if we call them multiple times with the same parameters. This is the concept of idempotency. If we are choosing to use Sidekiq we must also consider if the gems we are using are threadsafe.

##### Common Background Job Use Cases
- 3rd Party APIs - Working with external services. 
- Computational Intensive Work - Such as Solving equations or image processing.
- Sending Emails - We don't want to keep the user waiting for an email to send.

### An Active Job Example
In this example we will open an external image from a provided url, resize this image, and save it to disk. I will also be using the minimalistic and quick MiniMagick gem.

#### Setting the adapter
We need to specify which queuing backend we want to use. This choice will depend on your apps needs. If we leave this option unset it will default to :inline which will simply process the jobs as it gets them.

```ruby
    Rails.application.config.active_job.queue_adapter = :sidekiq
```

We can use the generator to create a new job. We can add the --queue quantifier if we wish to specific a non default queue.

```ruby
    rails generate job ResizeImage 
```


##### #app/jobs/resize_image.rb
```ruby
  class ResizeImage < ActiveJob::Base
    # Set the Queue as Default
    queue_as :default 

    def perform(img_url)
      # Open the image into memory
      image = MiniMagick::Image.open(img_url)

      # Change the image size
      image.resize "100x100"

      # Write the resulting image
      image.write output_path(img_url)
    end
  end
```

#### Enqueue the Job

Jobs can be added to the job queue from anywhere. We can add a job to the queue by:

~~~ ruby
    ResizeImage.perform_later "http://example.com/ex.png"
~~~

At this point, sidekiq will run the job for us. If the job for some reason fails, sidekiq will keep on trying.

### Conclusion
With the addition of Active Job into Rails 4.2 we can expect to see more Gems taking advantage of background jobs. They are a very useful feature which can make your apps faster and more manageable. 
