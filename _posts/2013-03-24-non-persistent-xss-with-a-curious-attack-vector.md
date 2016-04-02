For those of you who don’t know it, “memondo network” is the spanish company
behind a bunch of websites for memes, funny gifs, etc. And they have, according
to alexa, a quite large amount of traffic (a couple of their sites being in the
top 3000 and in the spanish top 200).

Well, a couple of days ago I found an XSS vulnerability in their search system
with a curious attack vector, so let’s take a look at it:

The vulnerable pages were `http://www.$(SITE).com/buscar/0/`

When you tried to search something -"DEVDEV" in our example- this GET request
was sent: `http://www.cuantocabron.com/busqueda/0/devdev`

![XSS output vector](http://i.imgur.com/Q9kp2jp.png)

After playing a bit with the search parameter, the first output of the value
(displayed to the user) seemed to be properly filtered, but the page navigation
buttons -prev, next- were not, so we should be able to inject code there:

![](http://i.imgur.com/8Rl5jA9.png)

But that injection is tricky... The vulnerable parameter is a link, and it’s
being processed by the server before echoing it to make it URL-friendly, which
means that any space would become %20 and any slash would screw the attack up.
That implies that you could get the code executed when you decoded those values
manually in the source code, but that would not be a feasible attack. 

As far as i got, I could not get a way to bypass that problem, so I had to think
about a different attack vector... I couldn’t use tags with parameters (because
of the space between the tag name and the first attribute) and I could not inject
a script because of the slash in </script>, so.... What about adding attributes
to the tag being injected?

It was dirty, it was anything but subtle and it required another step of social
engineering, but it did work and it might fool someone out there. Here’s a quick
example of how it could be done:

![Shitty example of social engineering](http://i.imgur.com/Q2yfpwx.png)

And when the victim’s mouse hovers over the link...

![Javascript successfully injected!](http://i.imgur.com/yckV2UT.png)

And that’s as far as I got. I did not wait to have anything prettier or better
and just reported it like that. I sent the email yesterday at 22.12, they were
very friendly about it and it had already been fixed by 12.00 today, so good job
on their part :)
