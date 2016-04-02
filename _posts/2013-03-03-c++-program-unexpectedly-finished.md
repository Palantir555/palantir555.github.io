---
layout: post
title: C++ Program Unexpectedly Finished
tags:
- software development
- c++
- error
- qt
- solution
---

{% highlight c++ %}
Error: The program has unexpectedly finished
{% endhighlight %}

This is a common error while working with Qt (and C++ in general). You will write
some code, compile it without errors or warnings and when you run the application
it will crash and throw a “The program has unexpectedly finished.” in the
Application Output.

The most common cause of this is using an object you have declared a pointer for
but have not allocated.

This piece of code will make the application crash:


{% highlight c++ %}
QPushButton = *myButton;
myGridLayout->addWidget (myButton, 0, 0, Qt::AlignLeft);
{% endhighlight %}

But this one won't:

{% highlight c++ %}
QPushButton = *myButton;
myButton = new QPushButton ("button's text");
myGridLayout->addWidget (myButton, 0, 0, Qt::AlignLeft);
{% endhighlight %}

The first example is obviously wrong, but it’s easy to forget the memory
allocation when the code starts getting longer.
