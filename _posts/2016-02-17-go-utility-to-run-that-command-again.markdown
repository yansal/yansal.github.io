---
layout: post
title:  "Go utility to run that command again"
date:   2016-02-17 00:00:00
---
These days, I often found myself waiting for a virtual machine to boot or for any remote service to be ready. While I'm waiting, I constantly run my command line to connect to this service until it works. Something like `ssh -v host` or `http -v host`.

This is boring. So I wrote [again](https://github.com/yansal/again), a small utility to do it automatically.

Install with:

    go get github.com/yansal/again

And add `again` before your boring command:

    again ssh -v host

It will run again and again until it succeeds.
