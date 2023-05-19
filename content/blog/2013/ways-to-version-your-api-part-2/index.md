---
date: "2013-05-16T00:00:00Z"
description: "Part two of a series on API versioning - how to write a version-less REST API"
tags:
- api
- rest
series:
- API Versioning
title: Ways to version your API, Part 2
image: version-api-part-2.png
summary: Part two of a series on API versioning - how to write a version-less REST API
---

{{<tip "warning">}}
This article was written long ago and imported to this blog unedited. Some information may no longer be up to date. I will be writing an updated post soon.
{{</tip>}}

# Part two: How to architect a version-less API

This is part two of a series on API versioning. [Part One](/blog/2013-05-09-ways-to-version-your-api/) covered the prevailing methods for versioning your API and allowing clients to code against one version specificically. I suggest you read that post (at least skim the intro!) before this one as it provides some neccesary context. In it, I hinted to another method utilizing some little-known concepts of the Accepts header spec might allow you to make a version-less API. I'll admit right up front, however, it's a bit of a misnomer, but I think it's still important. You can still version things in the API, but you aren't versioning the interface itself. You're merely versioning the resource representations.

The most common change in any REST architected API is adding, changing, or removing fields from the resources. New information is added or may be in a different format. Information may become unavailable. You may also add, move, or remove resources themselves. Depending on your API architecture, the HTTP verbs may mean slightly different things. All of these typically would require an API version increment if they would cause existing applications to break. With a little forethought, however, your API can change without requiring a whole API versioning strategy.

Architect a versionless interface:

* Use sane URI schemes: `/api/collection_name/resource_id`
* Follow definitions of HTTP methods
* Adding resources should always be backwards compatible
* Removing resources would have to be done across versions anyway
* Moving resources can be handled by redirect HTTP codes

Version resource representations:

* Use the Accepts header
* Adding information should always be backwards compatible
* Removing information would have to be done across versions anyway
* Moving information can be done with Accepts versioning

## Architecting a version-less API

Let me begin by saying that I acknowledge that a version-less API is a bit of an ideal state, and an API with a well-thought versioning strategy is far preferable to an API with a poorly thought out attempt to not version. There's no shame in saying that you think you need versioning, nor can every possible API fit into this versionless strategy. It really only works for REST-architected, resource manipulation based APIs that follow prevailing standards. That said, a great deal of APIs can be reworked to fit into this style. Additionally, this is intended for backend APIs, not APIs interfacing with your web front-end. In my opinion, these should almost always be separate APIs so that you can update the web front end functionality at your own pace without worrying about any users of the API. I know I personally am a lot more lax in my following of standards for front-end APIs. 

The first step of understanding how to create a versionless API is knowing that you don't want to version the interface itself. That means, you don't want to change how and where information is stored and accessed. In practical terms, this is the URI structure. Even this rule can be bent, of course - built right into the HTTP spec are redirects! If you've moved information, simply provide a permanent redirect to the new URI. Now, what happens if you remove a resource?

There's two reasons a particular URI might be removed from a typical API: Either the resource type itself has been removed, or more commonly, that particular representation of the resource is no longer supported. In the latter case, you're not really adhering to REST architecture: The resource representation should be decoupled from the URI. In the former case, if the entire resource type no longer is accessible, it's probably for a business reason, in which case you'd want to remove it from all previous versions of the API as well. In that case, versioning didn't really get you anything, did it?

Adding resources, or information to resources, should always be a backwards-compatible change. If you're using JSON, adding elements to existing resources should be a breeze. Even if you have an XML schema, you can simply add them to the defined schema and alert clients who may have downloaded their own copy of the schema to update it. If you'd like to add an entirely new resource, simply add it! Nobody should be affected.

Probably the most difficult thing to make versionless is any changes to what happens when you perform a particular method on a particular API. For example, if you used to be able to POST to `/api/foo/` and have it create a new `foo` resource for you, but now you want it to edit an existing `foo` resource. The best I can tell you is dont, instead follow the [recommended HTTP spec method definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) explicitly:

* `GET /api/foo/` or `GET /api/foo/1` retrieves resources or resource collections and is idempotent (repeatable with the same results)
* `POST /api/foo/` creates new resources and is NOT idempotent (repeating creates more resources)
* `PUT /api/foo/1` updates or creates an entire specific resource and is idempotent
* `PATCH /api/foo/1` ([proposed standard](http://tools.ietf.org/html/rfc5789)) updates fields of a specific resource and is idempotent
* `DELETE /api/foo/1` deletes a resource (sometimes resource collections) and is idempotent

Technically, I think DELETE should notify somehow if the resource didn't exist in the first place and a proper way to do that would be an error, but I'll defer on that one. OPTIONS and HEAD are used less and if you're using them, you probably know what you're doing anyway. If you're using PATCH, be aware it isn't a well supported standard, and many APIs will accept incomplete PUT requests and only update changed fields. I think this is fine so long as it's a well understdood and documented behavior given spotty PATCH support, at least until it's more widely acceptable.

A lot of times, resources are modified by POST requests. A form submits, you interpret the data and change a resource. This is a common pattern in front-end APIs, and as I mentioned above, that's fine. Not perfect, but fine. In a back-end API, this shouldn't happen! Use a PUT or PATCH request and explicitly define what you want the new resource to be. A common excuse is old versions of IE don't support PUT or PATCH, but this is a back-end API, it's fine! Every major library I know of at least supports PUT - if you're using one that doesn't, you should probably look elsewhere.

In short, a prerequisite to versionlessness is that every resource you have should be able to be accessed and manipulated in a consistent fasion, with the only directives being the URI to the resource, HTTP method, and the data representing the resource itself. If you're manipulating a single logical resource - say, a user profile - from multiple URIs, you're likely going to encounter situations where you need to version.

## Versioning resource representations

As I mentioned in the intro, the resource representations themselves transferred to the client can and probably should be versioned. The beauty of this approach is that each resource can be versioned independently. If you change one resource, then a month later decide to change a different resource, you don't have to increment an API version counter twice. Each resource version is incremented individually. Note that I'm not talking about versioning the resources themselves, just the representation of the resource. If you have a versioned resource, for example a document that has previous revisions available, these should be accessed separately than the method I'm describing here.

Familiarizing yourself with the [Accept header spec](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) will probably help you understand the true depth of how far specs can go towards future-proofing themselves. Almost everyone knows the Accepts header specifies what kind of MIME-Type the requestor expects, like `application/json`. Fewer know that it can not only specify one type, but it can specify multiple acceptable types as well as parameters on each type. In order to version resource representations, we're going to take advantage of the type parameters.

I'll jump in and have you consider the following:

`application/vnd.example.foo+json;version=2,application/vnd.example.foo+json`

Even without fully understanding the Accepts header, you can probably guess this string implies that it expects the `application/vnd.example.foo` type in `json` format, version 2 if available, and any version otherwise. Parsing the Accepts header in a manner consistent with the spec can be difficult only because many libraries do not parse it out of the box. 

So, when should you version resource representations? As I mentioned above, if you're removing information about a resource, it's probably for a business reason that you don't want it exposed anymore. In today's age of compressed transmission, there's little gain to be had from removing information simply because you don't feel it is useful anymore. Adding information should always be possible to be done in a backwards-compatible manner. You might want to version in cases of changing the name and/or type of a field, for example if you want to (real world example time!) repurpose a boolean field labeled "enabled" to a more generic "status" enum type.

## Now, how do I do this?

Unfortunately, much of what I've discussed here has little to no broad support in the community of people actually building widely used APIs. I suspect no small part of this is due to the difficulty implementing these in a real world application. Depending on your platform it may be easy or difficult, and few libraries will support a versioning strategy like this out of the box. The closest I know if is [node-restify](http://mcavage.github.io/node-restify/#Routing) which supports versioned routing based on an `Accepts-version` header. 

I'm going to be going through some libraries and attempting to extend them to support versioning in the future. Possibly attempting my own library that bakes a lot of this in for free. The easier it is for a developer to write standards-compliant code, the more likely they will adopt it, because in the end if it comes down to ease of development vs adherence to standards, I think we all know ease will win out every time.
