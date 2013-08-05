---
title: Update a scheduled job in Sidekiq
layout: post
category: Tech
tags: [Rails, Sidekiq]
---

In a recent rails project, I use [Sidekiq](http://sidekiq.org/) for asynchronous processes.

Sidekiq is a full-featured background processing framework for Ruby. It has much higher performance than other existing solutions.
As it described in [official website](http://sidekiq.org/), 1 Sidekiq process could do the work of 20 [Resque](https://github.com/resque/resque)  or [DelayedJob](https://github.com/collectiveidea/delayed_job) processes. 

I used DelayedJob two yeas ago, It stores jobs in RDBMS and polls for jobs, which is a terribly unscalable idea. It uses processs instead of threads. Now it supports multiple backends for storing the job queue, such as ActiveRecord, Mongoid.

I never use Resque, but as it described in [README](https://github.com/resque/resque#workers), it forks a new process for every worker, safe but easy memory hungry.

Sidekiq runs thread per worker and has less forking, so it uses much less memory and works faster.

To integrate Sidekiq in Rails application, it only need simple [3-steps](http://sidekiq.org/) to setup. But after starting a new job with Sidekiq, I find it is difficult to change it's scheuled time. Below what I want to cover is how to update a scheduled job in Sidekiq.

To change the scheduled job, the first thing is to locate it. The easiest way is using random generated [jid](https://github.com/mperham/sidekiq/blob/master/lib/sidekiq/client.rb#L135), then update it with [sidekiq-scheduler](https://github.com/Moove-it/sidekiq-scheduler/blob/develop/lib/sidekiq/scheduler.rb#L145) or [sidekiq-status](https://github.com/utgarda/sidekiq-status#unscheduling). But both of them are in favor of plugins and need to store job id in database. 

There is another way to change the scheduled job without plugins. From the [source code](https://github.com/mperham/sidekiq/blob/master/lib/sidekiq/api.rb#L220) of Sidekiq, a SortedEntry instance, which inherits from Job, are easy to be rescheduled. So what we need is to find and tell it running at another time.

    class DialWorker
      include Sidekiq::Worker

      def perform(schedule_id)
        schedule = Schedule.find schedule_id
        Tropo::Client.dial schedule
      end

      def self.reschedule_job(schedule_id, call_at)
        r = Sidekiq::ScheduledSet.new
        job = r.select do |s|
          s.item['args'].first == schedule_id
        end.pop # Sidekiq::SortedEntry

        job.reschedule call_at.to_i
      end
    end

In `reschedule_job` method, use unique identity args to scan `ScheduledSet` and find the matched job to reschedule.


