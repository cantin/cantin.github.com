---
layout: post
title: "delayed-job"
date: 2012-08-18 11:49
comments: true
categories: rails gem
---

##delayed-job

delayed-job是一个开源的延时执行任务的后台程序，在web应用上主要可以用来
异步发送邮件，异步更新数据等等

##requirement

delayed_job是用数据库来记录所要执行的异步任务的，所以他需要配合sql_db与onsql_db一起使用。

###work with activerecord
delayed_job默认是与activerecord在一起使用的

安装：
```ruby
  gem 'delayed_job'
```

generate：

```sh
rails generate delayed_job:active_record
rake db:migrate
```

###work with mongoid

如果要跟mongoid配合使用的话，可以安装以下这个

```ruby
 gem 'delayed_job_mongoid'
```

create_index:

```sh
script/rails runner 'Delayed::Backend::Mongoid::Job.create_indexes'
```

##usage

###invoke

普通调用delayed_job的方法是在调用对象的delay方法

```ruby
@user.dealy.send_email
```
这样子delayed_job就会在后台异步处理send_email这个动作了

另外，也可以在class中定义handle_asynchronously来标明方法是否异步


```ruby
class User
  def show
  end
  handle_asynchronously :show
end
```

这样调用show的时候就会进入后台处理。
另外handle_asynchronously还有几个参数，如:priority，:run_at
:priority指明了这个任务的优先级别
:run_at指明了这个任务在什么时候执行


###queues

delayed_job还可以指明这个任务是在那个queue里面执行，后面将介绍可以启动多个jobs来执行不同的queues

```ruby
object.delay(:queue => 'tracking').method

Delayed::Job.enqueue job, :queue => 'tracking'

handle_asynchronously :tweet_later, :queue => 'tweets'
```

这样可以指明这个任务是在那个queue里面

###running jobs

我们需要启动jobs才可以执行后台任务

```sh
rake jobs:work
```
或者
```sh
QUEUE=tracking rake jobs:work
```

另外，如果想以daemons的形式启动job

可以加上daemons这个gem

```ruby
gem 'daemons'
```

generate：

```sh
rails generate delayed_job
```

然后执行

```sh
RAILS_ENV=production script/delayed_job start
```

这样就在production环境下启动job了

另外还有其他选项如下

```sh
RAILS_ENV=production script/delayed_job stop

RAILS_ENV=production script/delayed_job -n 2 start
//启动两个job

//启动queues
RAILS_ENV=production script/delayed_job --queue=tracking start
RAILS_ENV=production script/delayed_job --queues=mailers,tasks start
```

###callback
  delayed_job还有call_back可以在任务调用过程中对其进行操控


###config
我们可以在config/initializers/delayed_job_config.rb中配置如下：

```ruby
Delayed::Worker.destroy_failed_jobs = false
Delayed::Worker.sleep_delay = 60
Delayed::Worker.max_attempts = 3
Delayed::Worker.max_run_time = 5.minutes
Delayed::Worker.read_ahead = 10
Delayed::Worker.delay_jobs = !Rails.env.test?
```

##work with devise
  delayed_job一般跟devise配合是用来异步发送邮件，这里可以使用一个gem
叫'devise-async', 可以跟不同的background program一起运作

  但是这里要介绍一种hack的方法，就是使用handle_asynchronously
在config/initializers/delayed_job_config.rb中加入

```ruby
module Devise
  module Models
    module Confirmable
      handle_asynchronously :send_confirmation_instructions
    end

    module Recoverable
      handle_asynchronously :send_reset_password_instructions
    end 

    module Lockable
      handle_asynchronously :send_unlock_instructions
    end 
  end 
end
```

这样子更改了devise发送邮件的处理方式，从而使邮件异步发送。

##work with exception_notification

当delayed_job处理任务失败，我们想让他发送一封邮件提醒我们出错了，这时候就可以使用
exception_notification

```ruby

# In config/environments/production.rb or config/initializers/delayed_job.rb

# Optional but recommended for less future surprises.
# Fail at startup if method does not exist instead of later in a background job.
[[ExceptionNotifier::Notifier, :background_exception_notification]].each do |object, method_name|
  raise NoMethodError, "undefined method `#{method_name}' for #{object.inspect}" unless object.respond_to?(method_name, true)
end

# Chain delayed job's handle_failed_job method to do exception notification
Delayed::Worker.class_eval do. 
  def handle_failed_job_with_notification(job, error)
    handle_failed_job_without_notification(job, error)
    # only actually send mail in production
    if Rails.env.production?
      # rescue if ExceptionNotifier fails for some reason
      begin
        ExceptionNotifier::Notifier.background_exception_notification(error)
      rescue Exception => e
        Rails.logger.error "ExceptionNotifier failed: #{e.class.name}: #{e.message}"
        e.backtrace.each do |f| 
          Rails.logger.error "  #{f}"
        end 
        Rails.logger.flush
      end 
    end 
  end.
  alias_method_chain :handle_failed_job, :notification.
end
```
这样就可以出错时发送邮件提醒，另外也可以使用delayed_job自带的了handle_failed_exception方法
