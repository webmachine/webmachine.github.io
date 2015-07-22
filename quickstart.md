---
layout: default
title: getting started quickly with Webmachine
---
# getting started quickly with Webmachine</h3>

Make sure that you have a working Erlang/OTP release, R12B5 or later (Preferably **MUCH** later).

A [rebar3](http://rebar3.org) template is provided for users quickly
and easily create a new `webmachine` application.

{% highlight text %}
$ mkdir -p ~/.config/rebar3/templates
$ git clone git://github.com/webmachine/webmachine-rebar3-template.git ~/.config/rebar3/templates
$ rebar3 new webmachine your_app_here
{% endhighlight %}

Once a new application has been created it can be built and started.

{% highlight text %}
$ cd your_app_here
$ rebar3 release
$ \_build/default/rel/your_app_here/bin/your_app_here console
{% endhighlight %}

The application will be available at [http://localhost:8080](http://localhost:8080).

To make this resource handle URI paths other than /, add more
[dispatch](dispatcher.html) terms in
/tmp/mywebdemo/priv/dispatch.conf; to make that resource to more
interesting things, modify the
[resource](resources.html) itself
at /tmp/mywebdemo/src/mywebdemo_resource.erl.</p>

To learn how to do more interesting things, check
out [some examples](example_resources.html) or
read [more documentation](docs.html).
