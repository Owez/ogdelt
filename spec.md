---
title: OGDELT Specification v0.0
subtitle: Simplified GDELT for ease of use
author: Owen Griffiths
---

<!-- This document is intended to be converted with mdx <https://github.com/Owez/mdx> -->

# Table of Contents

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [Datatypes](#datatypes)
  - [CAMEOs](#cameos)
  - [Integer](#integer)
  - [Float](#float)
  - [Date](#date)
  - [Classification](#classification)
  - [URL](#url)
  - [GeoType](#geotype)
  - [String](#string)
- [Event](#event)
- [Mentions](#mentions)

# Introduction

OGDELT is a simplified version of the GDELT 2.0 format made with developers in mind. It aims to contain all of the original raw data but in a more concise and manageable package. This document is the specification for OGDELT.

# Datatypes

Before going any further into the specification, this section defines the basic datatypes used commonly inside of GDELT regardless of the language used to parse through it.

You can skip this section if you wish because all datatypes specified in this document will link here.

## CAMEOs

CAMEO codes are prevalent inside of GDELT and are *up to* 4-length strings which encode a lot of information about any given [Event](#event) or [Actor](#actor).

Any time a CAMEO code is mentioned as a datatype, make sure you put it as a string with it's maximum length set as 4 and its minimum length set to 0. <!-- TODO: 0 may not be the best -->

<!-- TODO: redo with better decoding -->

## Integer

This is a simple datatype which just represents a 32-bit integer. This is equivalent to an [`i32`](https://doc.rust-lang.org/std/primitive.i32.html) inside of Rust and can be mimicked by just using an [`int`](https://docs.python.org/3/library/functions.html#int) in Python.

## Float

This float is a typical 32-bit float you'd see in programming languages. Similar to the [Integer](#integer), this can be emulated as a generic [`float`](https://docs.python.org/3/library/functions.html#float) in Python or a precise [`f32`](https://doc.rust-lang.org/std/primitive.f32.html) in the case of Rust. 

## Date

Dates are *without* any times attached to them (they're not a datetime) and always represent a UTC+00 timezone. This is ideally represented by using a date library in the language used to parse GDELT, such as [`chrono`](https://docs.rs/chrono/latest/chrono/) in Rust or [`datetime`](https://docs.python.org/3/library/datetime.html) in Python.

This is translated from the data provided by GDELT such as the `Day`, `MonthYear`, or `Year`.

## Classification

This is a small enum which is used soley inside of the [Event](#event). It represents what broad classification the event falls under. It can be:

1. Verbal Cooperation, e.g. security guarantee
2. Material Cooperation, e.g. an arms deal
3. Verbal Conflict, e.g. an argument
4. Material Conflict, e.g. a war

The numbered items correspond to the integer GDELT provides.

## URL

This is just a string with a minimum length of 1 character and a maximum length of 2048 characters. There's not much more to URLs and you might not need to enforce maximum lengths unless it's more data-compact or performant to do so.

## GeoType

This is another enum which defines the "geographic zoom" of an actor attached to some [Event](#event) which took place, the options make this clearer:

1. Country
2. District (USA)
3. City (USA)
4. City
5. District

<!-- NOTE: may rename to old GDELT for familiarity, their terms are bad though -->

The numbered items correspond to the integer GDELT provides.

## String

TODO

# Event

Events are the core element, wrapping up something that happened into a single package. To represent an event itself, you need:

- ID `id` ([Integer](#integer)): This is the primary key identifier of the event
- Date `date` ([Date](#date)): The date this event occurred on <!--NOTE: using DATEADDED this could be a datetime?-->
- Code `code` ([CAEMO](#cameos)): The CAMEO descriptor of this event
- Classification `class` ([Classification](#classification)): The broad outcome of this event
- Goldstein `goldstein` ([Float](#float)): The -10 to +10 [Goldstein scale](https://www.jstor.org/stable/174480) calculated for this event
- Mentioned `mentioned` ([Integer](#integer)): Total mentions of this event in the first 15 mins
- Sources `sources` ([Integer](#integer)): Number of sources who mentioned this event at least once in the first 15 mins
- Articles `articles` ([Integer](#integer)): Number of articles which mentioned this event at least once in the first 15 mins
- Tone `tone` ([Integer](#integer)): The -100 to +100 tone rating of this event; -10 to +10 is neutral-ish
- Source `source` ([URL](#url)): First online article this event was picked up on

In addition to these elements, there are also two actors which the event is molded around. Paste all of these elements twice and replace `x` with `1` and `2` to finish the event information:

- Actor Code `actor_x_code` (Optional, [CAEMO](#cameos)): Complete CAMEO descriptor for the actor
- Actor Name `actor_x_name` (Optional, [String](#string)): Actor's name, will be a person/location
- Actor Geo Type `actor_x_geo_type` ([GeoType](#GeoType)): Defines if it was a city or country or country
- Actor Geo Name `actor_x_geo_name` ([String](#string)): Full location name the actor was in
- Actor Geo Latitude `actor_x_geo_lat` ([Float](#float)): Closest location latitude
- Actor Geo Longitude `actor_x_geo_long` ([Float](#float)): Closest location longitude
- Actor Geo Country `actor_x_geo_country` ([String](#string)): Country code of the location
- Actor Geo ADM1 `actor_x_geo_adm_1` ([String](#string)): ADM1[^adm] (district) code of the location
- Actor Geo ADM2 `actor_x_geo_adm_2` (Optional, [String](#string)): ADM2[^adm] (city/town) code of the location[^admbad]

[^adm]: The term "ADM" is a shortening of "administrative division" from the [GNS](http://www.dlib.org/dlib/september12/leetaru/09leetaru.html) system
[^admbad]: This may contain ADM1 depending on how it's codified due to the [GNS](http://www.dlib.org/dlib/september12/leetaru/09leetaru.html) system apparently

# Mentions

TODO
