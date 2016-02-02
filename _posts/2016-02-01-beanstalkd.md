---
layout: post
title: "Beanstalkd 队列"
image: queue.jpg
video: false
---


Beanstalk，一个高性能、轻量级的分布式内存队列系统，最初设计的目的是想通过后台异步执行耗时的任务来降低高容量Web应用系统的页面访问延迟，支持过有9.5 million用户的Facebook Causes应用。


![Beanstalkd Image]({{ site.baseurl }}/content/images/beanstalkd-20131226202107875.png)

 * **READY:** 需要立即处理的任务，当延时 (DELAYED) 任务到期后会自动成为当前任务
 * **DELAYED** 延迟执行的任务, 当消费者处理任务后, 可以用将消息再次放回 DELAYED 队列延迟执行
 * **RESERVED** 已经被消费者获取, 正在执行的任务(将不会被别的消费者拿到)。Beanstalkd 负责检查任务是否在 TTR(time-to-run) 内完成
 * **BURIED** 保留的任务: 任务不会被执行，也不会消失，除非有人把它 "踢" 回队列
 * **DELETED** 消息被彻底删除。Beanstalkd 不再维持这些消息

#### 安装

```
$ yum install beanstalkd
$ aptitude install beanstalkd
```

#### 启动
```
$ beanstalkd -l 0.0.0.0  -p 11300 -b /var/log/binlog -F
```

#### EXample For PHP

{% highlight php linenos %}
<?php

// Hopefully you're using Composer autoloading.

use Pheanstalk\Pheanstalk;

$pheanstalk = new Pheanstalk('127.0.0.1');

// ----------------------------------------
// producer (queues jobs)

$pheanstalk
  ->useTube('testtube')
  ->put("job payload goes here\n");

// ----------------------------------------
// worker (performs jobs)

$job = $pheanstalk
  ->watch('testtube')
  ->ignore('default')
  ->reserve();

echo $job->getData();

$pheanstalk->delete($job);

// ----------------------------------------
// check server availability

$pheanstalk->getConnection()->isServiceListening(); // true or false
{% endhighlight %}