---
layout: post
title: Using HALBuilder with german characters
subtitle: How to make sure that umlauts are properly displayed
category: Howto
tags: [devops]
author: Filip Fiat
author_email: filip.fiat@haufe-lexware.com
header-img: "images/bg-post.jpg"
---

These days I was working on the Meinkonto RESTful API using this HAL implementation (http://www.theoryinpractice.net/ ) and like every time I work with some new technology I ended up indubitably at the following theme: "umlauts"

### TheoryInPractice's HalBuilder 4.x

For instances of `com.theoryinpractise.halbuilder.api.Representation` they provide this method `toString(RepresentationFactory.HAL_JSON)` for generating the JSON string from a `JsonRepresentation `object.
Unfortunately for strings with German characters the outcome is not quite the expected one, i.e. "ä" becomes "Ã¤" and so on.
Searching the documentation and codebase, I was not able to find a way to specify the correct encoding, thus I've ended using the following construction.

### "Good old JAVA" solves any problem

Create a String object from the outcome of this method with the right encoding (UTF-8), i.e. my code looks like this
` halRepresentationUTF8 = new String(halRepresentation.toString(RepresentationFactory.HAL_JSON).getBytes(), "UTF-8")`

And this does the trick;)
