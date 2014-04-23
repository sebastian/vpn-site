---
layout: index
title: My Proxy
---

![My proxied laptop](/images/top.png)

More often than I would want, I see myself in need of a proxy server.
Maybe I need to access a website that only allows visitors from certain geographical regions, or maybe I am in a location
where I do not trust people to _not_ sniff my traffic. Whatever the reason, this service serves to solve the
problem.

Below are different scenarios in which I might need a proxy, along with an explanation of how to achieve it.

## Prerequisites

In order to use this proxy service you need an account. If you don't already have one, please get in touch
with me.

Rather than using a beast of a software like OpenVPN, I am instead using
[sshuttle](https://github.com/apenwarr/sshuttle). It tunnels whatever you need it to over SSH. Simple and
clean.
Most of the explanations following below are more or less direct copies of what you would find in the
__sshuttle__ documentation. In either case you should install the __sshuttle__ software locally before
continuing.

For when you are stuck on some paid WiFi and only want to quickly look up some details, you can also use
[iodine](https://github.com/yarrick/iodine). Please install __iodine__ as well, now that you do have an
internet connection. Further details are explained below.

# Usage

Below are the scenarios we are looking to support:

- Get access to regionally restricted sites
- Protect your traffic when in public
- Gain internet access for quick lookups when travelling

## Regionally restricted sites

![Accessing global content](/images/global.png)

Unfortunately there are still geographical boundaries influencing what websites and resources we have access
to. These restrictions at times seem more arbitrary than based on sound reasoning. When it is not legally
forbidden to do so, we can therefore connect to the internet through a proxy that makes it look as if we are
located somewhere else than where we are.

The proxy server runs from a data centre in New York. If you want to access a website that is restricted to US
visitors, you can use the proxy to make it look as if you are based in the states.

Using __sshuttle__, you would issue the following command from the command line:

{% highlight bash %}
./sshuttle -r <username>@vpn.kle.io:22000 0.0.0.0/0
{% endhighlight %}

The `<username>` should be replaced with what your username is on the proxy.

This will send __all__ your communication through the proxy. Sometimes you might only want to proxy certain IP
ranges. To do that, please restrict the `0.0.0.0/0` catch-all clause to only target the IPs of the
destination.


## In public

When you are in a public space, for example using an open WiFi network, you have no idea who is eavesdropping
on your communication. As long as the services you are communicating with are encrypted, you should be
fine, but more often than not, this isn't the case.

![When at a public space, your communication should be protected](/images/in-public.png)

In these scenarios, you should also proxy your DNS traffic:

{% highlight bash %}
./sshuttle --dns -r <username>@vpn.kle.io:22000 0.0.0.0/0
{% endhighlight %}

Like before, `<username>` should be replaced with your username on the proxy.
In this case you really want the `0.0.0.0/0` catch all clause to ensure that all your communication passes
through the proxy!

## Internet on the go

Often Airports and train stations, and other locations where you might find yourself in transit, do not offer
free internet (even though we live in the 21st century).

![Sometimes you need only a little internet](/images/airport.png)

If all you want to do is quickly check your emails, read the latest news, or look up some details related to
your flight or a reservation, paying large amounts of money for poor internet connectivity might be
intolerable.

In this case, we use __iodine__ to tunnel our communication over DNS. Furthermore we then use __sshuttle__
inside the __iodine__ tunnel in order to ensure that our traffic is encrypted.

{% highlight bash linenos %}
iodine -f -P <password> <DNS-server> i.kle.io
./sshuttle --dns -r <username>@<server-ip>:22000 0.0.0.0/0
{% endhighlight %}

When you have setup the iodine connection, you will be informed what IP address the server is accessible
under. Use this IP when setting up the __sshuttle__ connection.

### Troubleshotting

- The server is running __iodine__ version 0.6.0-rc1. You should be running the same!
- If running on a Mac, please make sure that you have
  [tuntap](http://tuntaposx.sourceforge.net/download.xhtml) installed.
- Contact me if all else fails
