---
layout: post
title:  "Fix broken lightdm"
date:   2013-03-29 00:00:00
---
After some system upgrade, lightdm broke on my computer (Ubuntu 12.10).

The symptom was the following: I can enter my password but when I hit `<Enter>`, I don’t log and I go back to lightdm screen.

As a workaround I logged with a tty (`<Ctrl>` + `<Alt>` + `<F1>`) and remplaced lightdm with gdm:

{% highlight sh %}
# Install gdm
sudo apt-get install gdm
# Set gdm as default display manager
sudo dpkg-reconfigure gdm
{% endhighlight %}

But the problem was just the read access to ~/.Xauthority, so the solution was simply to remove that file:

	rm ~/.Xauthority
