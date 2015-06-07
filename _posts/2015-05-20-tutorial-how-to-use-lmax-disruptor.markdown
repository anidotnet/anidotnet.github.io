---
layout: post
title: "Tutorial: How to use LMAX Disruptor"
date: "2015-05-20"
categories: [tutorial, java]
tags: [tutorial, java, lmax, disruptor, concurrency]
description: A tutorial on how to use LMAX disruptor library in your java application. This tutorial explains it with an example project.
---

The [LMAX Disruptor](https://github.com/LMAX-Exchange/disruptor) is a high performance inter-thread messaging library. When I had to use it for a particular use case where high rate of concurrency was required, I could not find any suitable 'Getting Started' guide regarding this library. I had to do a bit of a dig down to get it to work but it was worth the effort.

So, in this tutorial I'll show you how to use it. Here in a sample project we will try to write messages in the console in a concurrent manner using the disruptor library.

First create a simple maven project and add this dependency to your pom.xml

{% highlight xml %}
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.3.0</version>
</dependency>
{% endhighlight %}

Now create an event class which will hold the string data we want to write to console concurrently. We will call this string data as **message**.

{% gist anidotnet/4ff41c7a8c7d53400709 WriteEvent.java %}

Next create the event factory class which will generate the `WriteEvent` on demand.

{% gist anidotnet/4ff41c7a8c7d53400709 WriteEventFactory.java %}

You need to create an event handler class, which will handle the event and execute the business logic associated with the event, here the logic is to print the message.

{% gist anidotnet/4ff41c7a8c7d53400709 WriteEventHandler.java %}

Create an event producer class which will publish the event data to the disruptor.

{% gist anidotnet/225d4e5600103b1b8f1c WriteEventProducer.java %}

Now you need to create a class e.g. `LMAXWriter` from which you will actually initialize and invoke the disruptor engine from your code. In that class, let's initialize the disruptor first.

{% gist anidotnet/4ff41c7a8c7d53400709 LMAXWriter.java %}

Also create a method to shutdown the disruptor after your work is done.

{% highlight java %}
public void close() {
    if (disruptor != null) {
        disruptor.shutdown();
    }
}
{% endhighlight %}

Next write a helper method to submit the message we want to write in console, to the disruptor.

{% highlight java %}
if (writeEventProducer != null ) {
    // publish the messages via event producer
    writeEventProducer.onData(message);
}
{% endhighlight %}

Now your basic framework is ready. You can now call the methods of the `LMAXWriter` class from a main method and see the disruptor play.

{% gist anidotnet/4ff41c7a8c7d53400709 App.java %}

<a class="btn btn-default" href="{{site.BASE_PATH}}/assets/artifacts/java/disruptor-demo.zip">Grab the full source code now!</a>
