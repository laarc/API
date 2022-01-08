# [laarc](https://www.laarc.io/) API

## Overview

In partnership with [Firebase](https://firebase.google.com/), we're making the public [laarc.io](https://www.laarc.io/) data available in near real time. Firebase enables easy access from [Android](https://firebase.google.com/docs/android/setup), [iOS](https://firebase.google.com/docs/ios/setup) and the [web](https://firebase.google.com/docs/web/setup). [Servers](https://firebase.google.com/docs/server/setup) aren't left out.

If you can use one of the many [Firebase client libraries](https://firebase.google.com/docs/libraries/), you really should. The libraries handle networking efficiently and can raise events when things change. Be sure to check them out.

Please email shawnpresser@gmail.com if you find any bugs.

## URI and Versioning

We hope to improve the API over time. The changes won't always be backward compatible, so we're going to use versioning. This first iteration will have URIs prefixed with `https://laarrc.firebaseio.com/v0/` and is structured as described below. There is currently no rate limit.

For versioning purposes, only removal of a non-optional field or alteration of an existing field will be considered incompatible changes. *Clients should gracefully handle additional fields they don't expect, and simply ignore them.*

## Design

The v0 API is essentially a dump of our in-memory data structures. We know, what works great locally in memory isn't so hot over the network. Many of the awkward things are just the way laarc works internally. Want to know the total number of comments on an article? Traverse the tree and count. Want to know the children of an item? Load the item and get their IDs, then load them. The newest page? Starts at item maxid and walks backward, keeping only the top level stories. Same for Ask, Show, etc.

I'm not saying this to defend it - It's not the ideal public API, but it's the one we could release in the time we had. While awkward, it's possible to implement most of laarc using it.

## Items

Stories, comments, and Ask laarcs are just items. They're identified by their ids, which are unique integers, and live under `/v0/item/<id>`.

All items have some of the following properties, with required properties in bold:

Field | Description
------|------------
**id** | The item's unique id.
deleted | `true` if the item is deleted.
type | The type of item. One of "story", "comment", "poll", or "pollopt".
by | The username of the item's author.
time | Creation date of the item, in [Unix Time](http://en.wikipedia.org/wiki/Unix_time).
text | The comment, story or poll text. HTML.
dead | `true` if the item is dead.
parent | The comment's parent: either another comment or the relevant story.
poll | The pollopt's associated poll.
kids | The ids of the item's comments, in ranked display order.
url | The URL of the story.
score | The story's score, or the votes for a pollopt.
title | The title of the story or poll. HTML.
parts | A list of related pollopts, in display order.
descendants | In the case of stories or polls, the total comment count.

For example, a story: https://laarrc.firebaseio.com/v0/item/1223.json?print=pretty

```javascript
{
  "by" : "phyllis",
  "descendants" : 5,
  "id" : 1223,
  "kids" : [ 1320, 1235, 1234, 1233 ],
  "score" : 5,
  "text" : "",
  "time" : 1549628553,
  "title" : "Conversation between German physicists upon hearing the atomic bomb was dropped",
  "type" : "story",
  "url" : "http://germanhistorydocs.ghi-dc.org/pdf/eng/English101.pdf"
}
```

comment: https://laarrc.firebaseio.com/v0/item/2272.json?print=pretty

```javascript
{
  "by" : "RiderOfGiraffes",
  "id" : 2272,
  "kids" : [ 2274 ],
  "parent" : 2271,
  "score" : 2,
  "text" : "Hi,<p>I'm a PhD in Pure Math (Graph Theory), non-exec director of a company that does the maritime equivalent of air traffic control, and now a full-time torturer of adults and confuser of children.  I do enrichment and enhancement for math and science, giving 150 to 200 talks and workshops a year.<p>Although probably not this year.  8-(",
  "time" : 1586345197,
  "type" : "comment"
}
```

ask: https://laarrc.firebaseio.com/v0/item/2428.json?print=pretty

```javascript
{
  "by" : "emily",
  "descendants" : 14,
  "id" : 1609,
  "kids" : [ 1682, 1660, 1638, 1630, 1625, 1619, 1617, 1610 ],
  "score" : 7,
  "text" : "",
  "time" : 1550548104,
  "title" : "Ask laarc: What apps do you love and/or use habitually?",
  "type" : "story",
  "url" : ""
}
```

## Users

Users are identified by case-sensitive ids, and live under `/v0/user/`. Only users that have public activity (comments or story submissions) on the site are available through the API.

Field | Description
------|------------
**id** | The user's unique username. Case-sensitive. Required.
**created** | Creation date of the user, in [Unix Time](http://en.wikipedia.org/wiki/Unix_time).
**karma** | The user's karma.
about | The user's optional self-description. HTML.
submitted | List of the user's stories, polls and comments.

For example: https://laarrc.firebaseio.com/v0/user/rain1.json?print=pretty

```javascript
{
  "about" : "",
  "created" : 1546298537,
  "id" : "rain1",
  "karma" : 494,
  "submitted" : [ 2693, 2691, 2686, 2682, 2677, 2675, 2662, 2659, 2658, 2657, 2655, 2654, 2653, 2652, 2651, 2650, 2649, 2648, 2646, 2645, 2640, 2639, 2637, 2634, 2633, 2632, 2629, 2628, 2627, 2626, 2620, 2619, 2618, 2617, 2558, 2556, 2554, 2550, 2547, 2546, 2545, 2542, 2541, 2540, 2537, 2536, 2524, 2520, 2517, 2516, 2514, 2508, 2507, 2505, 2502, 2500, 2498, 2487, 2475, 2462, 2461, 2460, 2459, 2455, 2454, 2453, 2452, 2451, 2450, 2449, 2448, 2447, 2439, 2437, 2427, 2424, 2414, 2409, 2408, 2404, 2402, 2400, 2399, 2398, 2397, 2396, 2395, 2290, 2279, 2274, 2273, 2263, 2252, 2248, 2247, 2235, 2234, 2233, 2232, 2230, 2223, 2222, 2221, 2220, 2195, 2185, 2182, 2180, 2179, 2178, 2174, 2173, 2172, 2171, 2170, 2169, 2168, 2167, 2165, 2164, 2160, 2159, 2157, 2156, 2155, 2153, 2152, 2151, 2150, 2149, 2148, 2147, 2146, 2145, 2144, 2141, 2140, 2135, 2128, 2126, 2124, 2123, 2115, 2113, 2112, 2109, 2108, 2103, 2102, 2101, 2100, 2099, 2098, 2097, 2094, 2092, 2091, 2088, 2087, 2084, 2083, 2081, 2080, 2078, 2077, 2074, 2073, 2071, 2068, 2066, 2057, 2055, 2053, 2052, 2050, 2048, 2046, 2045, 2042, 2039, 2038, 2034, 2030, 2029, 2025, 2021, 2020, 2019, 2018, 2010, 2009, 2006, 2003, 2002, 2000, 1999, 1996, 1995, 1994, 1993, 1991, 1990, 1988, 1987, 1981, 1980, 1972, 1970, 1969, 1968, 1967, 1966, 1965, 1964, 1962, 1961, 1960, 1958, 1938, 1936, 1932, 1924, 1920, 1919, 1916, 1902, 1900, 1892, 1883, 1873, 1870, 1867, 1860, 1858, 1855, 1853, 1852, 1849, 1841, 1840, 1839, 1829, 1828, 1827, 1826, 1824, 1806, 1805, 1797, 1792, 1791, 1790, 1784, 1781, 1780, 1775, 1774, 1762, 1755, 1749, 1727, 1705, 1704, 1703, 1677, 1675, 1674, 1659, 1652, 1646, 1643, 1641, 1640, 1626, 1620, 1611, 1610, 1578, 1574, 1572, 1570, 1541, 1483, 1476, 1460, 1445, 1444, 1433, 1430, 1422, 1401, 1400, 1380, 1361, 1360, 1359, 1354, 1352, 1350, 1349, 1348, 1347, 1341, 1280, 1278, 1277, 1274, 1273, 1272, 1271, 1270, 1269, 1242, 1237, 1236, 1235, 1234, 1233, 1232, 1231, 1221, 1220, 1210, 1189, 1146, 1121, 1115, 1114, 1066, 1065, 1051, 1040, 1039, 1032, 1031, 1030, 1029, 975, 972, 969, 967, 965, 956, 920, 909, 880, 878, 876, 873, 864, 843, 841, 839, 838, 830, 822, 820, 819, 818, 812, 811, 810, 800, 786, 785, 784, 783, 755, 754, 752, 733, 726, 722, 712, 711, 710, 708, 706, 705, 675, 667, 666, 649, 621, 608, 584, 583, 578, 565, 564, 560, 518, 516, 496, 494, 493, 492, 491, 483, 478, 469, 456, 455, 446, 429, 412, 411, 409, 396, 395, 394, 357, 356, 355, 316, 303, 276, 247, 246, 236, 233, 232 ]
}
```

## Live Data

The coolest part of Firebase is its support for change notifications. While you can subscribe to individual items and profiles, you'll need to use the following to observe front page ranking, new items, and new profiles.

### Max Item ID

The current largest item id is at `/v0/maxitem`. You can walk backward from here to discover all items.

Example: https://laarrc.firebaseio.com/v0/maxitem.json?print=pretty

```javascript
2701
```

### New and Top Stories

Up to 500 top and new stories are at `/v0/topstories` and `/v0/newstories`.

Example: https://laarrc.firebaseio.com/v0/topstories.json?print=pretty

```javascript
[ 2700, 2698, 2699, 2697, 2695, 2696, 2693, 2692, 2691, ..., 2018 ]
```

