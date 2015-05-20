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

{% highlight java %}
public class WriteEvent {

    private String message;

    public void set(String message){
        this.message = message;
    }

    public String get() {
        return this.message;
    }
}
{% endhighlight %}

Next create the event factory class which will generate the `WriteEvent` on demand.

{% highlight java %}
public class WriteEventFactory implements EventFactory<WriteEvent> {

    public WriteEvent newInstance() {
        return new WriteEvent();
    }
}
{% endhighlight %}

You need to create an event handler class, which will handle the event and execute the business logic associated with the event, here the logic is to print the message.

{% highlight java %}
public class WriteEventHandler implements EventHandler<WriteEvent> {

    private Logger logger = LoggerFactory.getLogger(getClass());

    public void onEvent(WriteEvent writeEvent, long sequence, boolean endOfBatch)
      throws Exception {
        if (writeEvent != null && writeEvent.get() != null) {
            String message = writeEvent.get();

            // Put you business logic here.
            // here it will print only the submitted message.
            logger.error(message + " processed.");
        }
    }
}
{% endhighlight %}

Create an event producer class which will publish the event data to the disruptor.

{% highlight java %}
public class WriteEventProducer {

    private static Logger logger = LoggerFactory.getLogger(WriteEventProducer.class);

    private final Disruptor<WriteEvent> disruptor;

    public WriteEventProducer(Disruptor<WriteEvent> disruptor) {
        this.disruptor = disruptor;
    }

    private static final EventTranslatorOneArg<WriteEvent, String> TRANSLATOR_ONE_ARG =
     new EventTranslatorOneArg<WriteEvent, String>() {
        public void translateTo(WriteEvent writeEvent, long sequence, String message) {
            logger.debug("Inside translator");
            writeEvent.set(message);
        }
    };

    public void onData(String message) {
        logger.info("Publishing " + message);
        // publish the message to disruptor
        disruptor.publishEvent(TRANSLATOR_ONE_ARG, message);
    }
}
{% endhighlight %}

Now you need to create a class e.g. `LMAXWriter` from which you will actually initialize and invoke the disruptor engine from your code. In that class, let's initialize the disruptor first.

{% highlight java %}
public void init() {
    // create a thread pool executor to be used by disruptor
    Executor executor = Executors.newCachedThreadPool();

    // initialize our event factory
    WriteEventFactory factory = new WriteEventFactory();

    if (ringBufferSize == 0) {
        ringBufferSize = 1024;
    }

    // ring buffer size always has to be the power of 2.
    // so if it is not, make it equal to the nearest integer.
    double power = Math.log(ringBufferSize) / Math.log(2);
    if (power % 1 != 0) {
        power = Math.ceil(power);
        ringBufferSize = (int) Math.pow(2, power);
        logger.info("New ring buffer size = " + ringBufferSize);
    }

    // initialize our event handler.
    WriteEventHandler handler = new WriteEventHandler();

    // initialize the disruptor
    disruptor = new Disruptor<WriteEvent>(factory, ringBufferSize, executor);
    disruptor.handleEventsWith(handler);

    // set our custom exception handler
    ExceptionHandler exceptionHandler = new WriteExceptionHandler();
    disruptor.handleExceptionsFor(handler).with(exceptionHandler);

    // start the disruptor
    disruptor.start();

    // initialize the event producer to submit messages
    writeEventProducer = new WriteEventProducer(disruptor);

    logger.info("Disruptor engine started successfully.");
}
{% endhighlight %}

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

{% highlight java %}
public static void main(String[] args) {
    LMAXWriter lmaxWriter = new LMAXWriter();
    logger.info("Initializing lmax disruptor.");
    lmaxWriter.setRingBufferSize(7); //deliberately set. Final ring buffer size would be 8.
    lmaxWriter.init();

    // submit messages to write concurrently using disruptor
    for (int i = 0; i < 10; i++) {
        lmaxWriter.submitMessage("Message Sequence " + i);
    }

    logger.info("All message submitted.");

    lmaxWriter.close();
    logger.info("Program executed successfully.");
}
{% endhighlight %}

<a class="btn btn-default" href="{{site.BASE_PATH}}/assets/artifacts/java/disruptor-demo.zip">Grab the full source code now!</a>
