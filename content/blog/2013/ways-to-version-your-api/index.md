---
date: "2013-05-09T00:00:00Z"
description: Suggested methods to version your RESTful API, part one of a two-part
  API versioning series
tags:
- api
- rest
series:
- API Versioning
title: Ways to version your API
image: version-api-part-1.png
summary: Part one on a series of API versioning - different ways to version a REST API.
---

{{<tip "warning">}}
This article was written long ago and imported to this blog unedited. Some information may no longer be up to date. I will be writing an updated post soon.
{{</tip>}}

# Part one: Pros and cons of various methods to version your API

Anyone designing an HTTP API with versioning in mind for the first time might be surprised to learn there is no universally accepted method, nor any standard explaining how. This post will be part one of a two part series on API versioning. In this post, I'll cover some API versioning methods used today and the pros and cons of each. In [part two](/blog/2013/ways-to-version-your-api-part-2/), I'll be describing some strategies you can keep in mind when designing your API to obviate the need to version at all.

There’s a lot of ways to version your API, and if you’ve ever had to change your API drastically, you’ve probably been glad you had a versioning methodology in place. If you didn’t, you probably WISH you did. However, anyone designing such a methodology for the first time might be surprised to know there is no universally accepted method for versioning your API, nor does any standard specify one.

Here's what I have found to be the most common methods of API versioning, in order of least to most desirable in my opinion:

* Changing hostnames
* Adding /v1/ to the URI
* Adding ?version=1 to the query parameters
* Passing a custom header
* Specifying a parameter in the accepts header

Before I can do that, I must explain what the standards say and explain my interpretation of what the web was envisioned to be.

A URI - Uniform Resource Identifier - is defined in [RFC 3986](http://tools.ietf.org/html/rfc3986) as a compact sequence of characters that identifies an abstract or physical resource. URIs are, for practical purposes, a superset of URLs (Uniform Resource Locators) and the terms are often used interchangably, if incorrectly. Any API which operates over HTTP at one point has to deal with what URIs point to what resource; whether that is one global resource which all operations flow through or a subnet of resources in a logical arrangement. However, it is important to note that the URI does not specify whatsoever the representation of the resource. This will be important later.

The specification itself does not indicate a URI always has to point to the same resource, though you tend to break client applications if you move resources without warning. As such, if you change your URI scheme, you should either leave the existing URI scheme in place, or leave breadcrumbs to find the new resources. Leaving breadcrumbs will be covered in the next post, so let's talk about how to change your API while letting people still use it the old way. Keep in mind that these are all general opinions: Sometimes your specific API needs will make one choice drastically better than another that I said was better, so always use your own judgement!

## Changing Hostnames

I'm going to barely touch upon this method, since it's barely used, and even then only in the most extensive of API revamps. Essentially, you're moving the API from one hostname to another. You might even just call this building a new API to the same resources. Downsides of this method are above and beyond all others - in addition to changing what URIs are being used, clients may have to change their security settings to access the new hostname. The only upside is that you can completely revamp the enture URI structure - including adding a version parameter if you didn't have one before. You can also easily route it to a completely different server, so the old one can continue serving just the old API. But then you've got two servers - Expensive!

**Short story:** Don't consider this unless you *really* messed up the first time and want to completely start over.

## Adding version to the URI path

This is by far the most common method currently in use by APIs today. It's fairly straightforward from an initial implementation standpoint. Simply add a /v1/ or similar to the URI, like this: `http://example.com/api/v1/foo/bar`. Alternately, you can avoid putting v1 in the URI initially, and if you have to version, change the URI to `http://example.com/api_v2/foo/bar`.

It's very easy to code into the first API, and it's very easy for users to understand what that number means. You can change the entire path after the version number between revisions and not break existing clients. Depending on what server technology you are using, it may be easy or hard to increment the version. If you need to copy and paste code to make this method work, you might want to stay away - it makes the separation very easy, but maintenance can be difficult. Imagine if you had to fix a bug for all versions of the API, and you're already on version ten! Find-and-replace nightmare.

Deprecating a version - if you do it nicely - can be straightforward, and even easy in some technologies. Redirect all requests to the old, deprecated version resources to a newer version. You'll still want to notify users ahead of time, but this way their clients might not break when you deprecate a version. Since nobody can *not* use a version, you'll potentially break everyone if you don't give them enough time.

**Short story:** If you read this entire article (and the followup) and still have no idea what you're doing, just stick /v1/ in the URI somewhere and figure it out later. This method works for everyone else in the end, you can probably get it to work too - even if you may regret it later.

## Version in the query parameters

This one is, in my experience, the second most common versioning method. Again, it is straightforward from an initial implementation standpoint: Add version query parameter, even if it is completely ignored. URIs might look like this: `http://example.com/api/foo/bar/?version=1`

One advantage of this option is it can be optional or required depending on how you want the API to be used. Some APIs require every request to include the version, others assume the latest version if the request left it off. Like adding it to the URI path, this is very easy to see, understand, and initial code implementation is trivial. In fact, you can probably just ignore it until you actually have a v2!

However, you can't as easily change the path, so moving resources can be painful. Additionally, you're often forced to put in place transform layers to change the data representation between versions if the representation has changed. You generally don't redirect solely on a query parameter being wrong, so redirecting existing users on a deprecated version to a supported version generally can't be done. However, you can do this transparently by silently serving up the newer version if you think that's more appropriate than simply breaking.

If you have an API that actually versions resources themselves through query parameters, you'll probably want to stay away from specifying API version in the query parameters as well. That could get confusing! Use either of the methods below instead.

**Short story:** An acceptable method when you only want to version the resource representations. Versioning paths will be harder, so keep that in mind. Few benefits over adding it to the URI path.

## Passing a custom header

This method is similar to the previous; instead of specifying it in the query parameters you specify it in the headers. For example, to use cURL to get from an API like this, you would specify `curl -H "Accepts-version: 1.0" http://www.example.com/api/foo/bar`

Largest advantage of this scheme is mostly semantics: You aren't cluttering the URI with anything to do with the versioning. The URI should be specifying what the resource is, not how you want it represented. However, you also are making up headers. While from a technology standpoint it will work, it loses some purism points. 

Other benefits include easily being able to ignore it or silently upgrade if the user does not specify, or specifies a deprecated version. Downsides are similar to version in the query parameters. Additionally, depending on what tools you have it can be difficult to specify. Hint: Get [Postman for Chrome](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?hl=en), it makes specifying headers easy!

**Short story:** A neater way to version than with query parameters, and avoids some complications. However, it can be difficult from a usability standpoint.

## Specifying a parameter in the Accept header

For now, I'll simply mention that the [Accept header spec](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) allows for custom vendor media types, and for parameters to be passed as part of the content negotiation. This is a perfectly valid Accepts header: "application/vnd.urthen.example?version=1.0"

I'll be going over this method in much more detail in [part two of API Versioning](/blog/2013/ways-to-version-your-api-part-2/). To understand why this method is ideal in my opinion, you'll also need to understand my thoughts on how to create an unversioned API. Make sure to follow me on twitter to know when it is posted.

