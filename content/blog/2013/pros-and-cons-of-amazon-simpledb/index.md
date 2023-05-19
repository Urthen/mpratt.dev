---
date: "2013-04-29T00:00:00Z"
description: "Amazon's SimpleDB service: Pros and Cons"
tags:
- amazon
- simpledb
- python
title: Pros and cons of Amazon SimpleDB
summary: Amazon's SimpleDB service is a very easy to set up and maintain non-relational data store, hosted in their cloud infrastructure. However, it has quite a few limitations.
---

# Or, why the easiest solution isn't always the best

{{<tip "warning">}}
This article was written long ago and imported to this blog unedited. Some information may no longer be up to date. Read the [SimpleDB FAQ](https://aws.amazon.com/simpledb/faqs/) to verify any information here.
{{</tip>}}

Amazon's [SimpleDB](http://aws.amazon.com/simpledb/) service is a very easy to set up and maintain non-relational data store, hosted in their cloud infrastructure. I used it during the creation of a new project as I wanted something simple and easy to implement. SimpleDB was just that. However, it has quite a few limitations. Without getting into too much detail, make sure you take the following into account before you start coding:

## The Good

* Easy to set up - No special configuration required, just one command to initialize the store.
* No Maintenance - Assuming Amazon doesn't go under, your data is relatively safe
* Low cost - Nearly free for small data sets and throughput
* Query syntax based on SQL
* Exposed in the Python [Boto](https://github.com/boto/boto) package for Amazon services

## The Bad

* Slow to write - Can only write a maximum of 25 entries at a time
* Slow to read - Can only read ~1kb of data per request
* Flat structure - If you input nested JSON objects, you need to parse the deep objects out of packed strings

## Conclusions?

In short, it's a fine service - if you use it correctly. I was trying to use it as a caching service, however. At only 5000 entries, it takes several seconds to read the entire data set, even with no further calculations, just retrieving the data. This was after minimizing the data returned so that I could read the maximum amount before reaching the 1kb limit. I think I'll soon be switching to something more appropriate. I know Redis will be up to the task, I just need to figure out how to host it properly.

If you're writing data fairly slowly, and don't need to read the entire data set rapidly either, it might suit your purposes. This isn't a condemnation of SimpleDB by any means, just don't try and make it do things it wasn't meant to!
