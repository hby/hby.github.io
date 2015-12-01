---
layout: post
title: A REPL Story
comments: true
tags: [clojure, repl, workflow]
---

My apologies to Jean Shepherd.

As I've gone through the process of writing this over Thanksgiving weekend, the title has changed three times.
I believe I've finally landed on one that best descibes the content. It a rambling story about data and discovery.
Hmmm, that gives me an idea for a book, Clojure: One Story at a Time. Or some such nonsense.
There's nothing earth shattering here. Unless you're inclined to believe that earth shattering things start with
simple thoughts, questions, and explorations.

> "Great things are done by a series of small things brought together."<br>
  ― _Vincent Van Gogh_

Lately, I've had a number of project ideas that all have something to do with my iTunes library.
What do I know? I know that the information is stored in an XML file,
typically located at `~/Music/iTunes/iTunes Music Library.xml`, but I didn't really know the format.
I do have some experience with NeXT/Mac OS X/iOS development and figured it was
a [plist](https://en.wikipedia.org/wiki/Property_list) file.
Sure enough, taking a peek inside showed that was the case.
However, knowing it is a plist file conveys about the same information as saying it's a JSON file.
I need to know _what_ data is under _what_ keys and how is it all organized.
This seemed like a good job for starting a Clojure REPL and doing some exploring. So, a REPLing we will go ...

## Starting the REPL

There are a number of good sources for running a Clojure REPL ([clojure.org](http://clojure.org/repl_and_main),
[braveclojure.com](http://www.braveclojure.com/getting-started/#Using_the_REPL)).
I really like [Cursive Clojure](https://cursiveclojure.com) so I typically set up a [Leiningen](http://leiningen.org)
project and get a local REPL going with [these instructions](https://cursiveclojure.com/userguide/repl.html).

So, with a quick

```
$ lein new itq
$ cd itq
```

I am in the `itq` (iTunes query) project.
You can set up a REPL in your editor/environment of choice or just run

```
$ lein repl
```

## A way of _thinking_

REPL driven development (RDD) has been talked about a lot
(see [Google](https://www.google.com/search?client=safari&rls=en&q=repl+driven+development&ie=UTF-8&oe=UTF-8)).
This is good but I feel like I should nuance it just a little and, instead of using the phrase REPL Driven Development,
I want to suggest the phrase REPL _Enabled_ _Thinking_ (RET).

For me, _thinking_ undergirds development. And thinking continues during and remains after development.
I need to _think_ about the problem, the domain, the data, and anything else that comes to mind.
The Clojure language and its REPL are just tools that allow my mind to explore the space of the problem that
I'm interested in.
RDD seems to me to put the emphasis on _doing_ development.
With this mindset, the REPL environment then provides a way to get the code developed.
But RET puts the emphasis on the _thinking_ about your problem and the REPL is a means to that end.
It's a way to enable the thinking.

_This_ is not keyboard driven writing. I would humbly submit it is keyboard enabled storytelling.

One of the heroes of my technical formative years is [Richard A. O'Keefe](https://en.wikipedia.org/wiki/Richard_O%27Keefe).
In the introduction of his book [The Craft of Prolog](https://mitpress.mit.edu/index.php?q=books/craft-prolog) he says,

> "If your Prolog code is ugly, the chances are that you either don't understand your problem or you don't
understand your programming language, and in neither case does your code stand much a chance of being efficient."<br>
  ― _Richard A. O'Keefe, The Craft of Prolog_  

This points out that the key to success involves _understanding_.
We are in the business of thought work. But here's the rub, no two people have the same mind.
I think the hardest thing in the world is for one mind to understand another mind.
I'm probably naive in this but I often think that many of the debates within the software world could be less volatile
if we kept in focus the notion that different minds think differently.

And ... are you ready? ... that's ok.

It helps some people to write their tests first because something clicked in their thinking about ordering things this
way and they resonate with this way of working.
But for others, there are situations where this would be a roadblock to them understanding their problem.
Whenever the debate volume increases I want to whisper, "It's ok, you can do what works for you." But that's another story.

But this idea, that understanding and thinking is paramount, relates to my REPL story because REPL Enabled Thinking
most accurately describes what I'm setting out to do.
As I start to explore this problem and its data, I gain understanding by directly manipulating parts of the data,
enabled by the tools I have in the REPL.
And I can build on these tools by adding more functions, possibly saving some to use as part of the final solution.
Along the way, I can write tests, documentation, or whatever, to capture how I want things to work as I figure them out.
All in the same environment.
It's one fluid workflow where I can do all of these things at the time that best fits with how I'm thinking about the problem.
I don't focus on _doing_ as much as learning to _understand_ my problem space.
It's a subtle shift in thinking but I believe a meaningful one. It's also the difference
between assuming you know everything about your problem, banging out some code with passing tests, and then thinking you're
finished. Unlikely.

## Back to the REPL

After a quick search I found the [clj-plist](https://github.com/bdesham/clj-plist) library.
That was helpful. No need to write a plist parser. Just add the dependency to my `project.clj` file and I'm good to go.

{% highlight clojure %}
(defproject itq "0.1.0-SNAPSHOT"

  :dependencies [[com.github.bdesham/clj-plist "0.9.1"]])
{% endhighlight %}

In this case I know I'm going to want to save some of what I do in the REPL.
So, I start by creating a file to hold a namespace of things that I'll create while exploring the iTunes data.
Then I can define things in the file editor and evaluate them in the REPL.
A glance at the `clj-plist` docs and I know how to turn a plist file into Clojure data structures.
So, after copying the iTunes library I want to use into the project,
I start with these forms in a file that I evaluate in the REPL.

{% highlight clojure %}
(ns itq.parse
  (:require [com.github.bdesham.clj-plist :as pl])
  (:import [java.io File]))

(def itl
  (pl/parse-plist
    (File. "<path-to-project>/itq/iTunes Music Library.xml")))
{% endhighlight %}

Then, place the REPL in the `itq.parse` namespace.

{% highlight clojure %}
(in-ns 'itq.parse)
{% endhighlight %}

In theory, `itl` has my iTunes library as a Clojure data structure.
Finally (well, it really hasn't been that long), I can look at it. So, I type in the REPL

{% highlight clojure %}
itl
{% endhighlight %}

But before hitting return I start _thinking ... I have a pretty big iTunes library ...
I've been here before ... the REPL is going to take a while as it prints out my library data ...
and I'm not really going to learn much of anything ... I better check what it is and how big it is ..._

{% highlight clojure %}
(class itl)
=> clojure.lang.PersistentHashMap
(count itl)
=> 10
{% endhighlight %}

_Hmmm, only 10 keys, what are they?_

{% highlight clojure %}
(keys itl)
=>
("Major Version"
 "Show Content Ratings"
 "Playlists"
 "Music Folder"
 "Minor Version"
 "Date"
 "Tracks"
 "Application Version"
 "Library Persistent ID"
 "Features")
{% endhighlight %}

`"Tracks"` sounds promising but there's some large collections in there somewhere.
Easy enough to see what I'm dealing with ...

{% highlight clojure %}
(into {} 
      (map
        (fn [[k v]] [k [(class v) (if (coll? v) (str (count v) " items") v)]])
        itl))
=>
{"Major Version" [java.lang.Long 1],
 "Show Content Ratings" [java.lang.Boolean true],
 "Playlists" [clojure.lang.PersistentVector "195 items"],
 "Music Folder" [java.lang.String "file:///Volumes/mediaHD/iTunes/iTunes%20Media/"],
 "Minor Version" [java.lang.Long 1],
 "Date" [org.joda.time.DateTime #object[org.joda.time.DateTime 0x3d37e74a "2015-10-27T19:44:01.000-05:00"]],
 "Tracks" [clojure.lang.PersistentHashMap "23177 items"],
 "Application Version" [java.lang.String "12.3.1.23"],
 "Library Persistent ID" [java.lang.String "88ABD0BA83F503C5"],
 "Features" [java.lang.Long 5]}
 {% endhighlight %}

Yep, a lot of tracks. I should say that what I'm eventually going to want to do is read in my artist,
album, and track information into a [Datomic](http://www.datomic.com) database so I can play with the data even more.
To do that I want to know how the data is organized in this file. It looks like it's all in that large `"Tracks"` map.

I could continue this process of cautiously peeking into the structures to get an idea of their size before fully looking
at or a part of it.

{% highlight clojure %}
(def tracks (itl "Tracks"))
=> #'itq.parse/tracks

(let [[[tk tv]] (take 1 tracks)]
  [(class tk) (class tv)])
=> [java.lang.String clojure.lang.PersistentHashMap]

(let [[[tk tv]] (take 1 tracks)]
  [(class tk) (count tv)])
=> [java.lang.String 27]
{% endhighlight %}

Ok, 27 keys is not too bad but I don't know what the values are and this is getting a little tedious. I should be able
to write some functions to help speed this up. After a little playing (right in the REPL) I come up with this.

{% highlight clojure %}
(declare explore-map)
(declare explore-vector)
(defn explore
  "Safely explore possibly large map/vector structures in the repl.
   mv - map, vector
   threshold - if count is under threshold, recursively explore
   peekn - if over threshold, recursivley explore peekn entries
           and summarize the rest"
  [mv threshold peekn]
  (cond
    (map? mv) (explore-map mv threshold peekn)
    (vector? mv) (explore-vector mv threshold peekn)
    :else mv))

(declare big-map)
(defn explore-map
  [m t p]
  (if (< (count m) t)
    (into {} (map (fn [[key val]] [key (explore val t p)]) m))
    (big-map m t p)))

(defn big-map
  [m t p]
  (let [peeked (take p m)
        rest (drop p m)
        more (str (count rest) " more entries")]
    (assoc (into {}
                 (map (fn [[key val]] [key (explore val t p)]) peeked))
      :more more)))

(declare big-vector)
(defn explore-vector
  [v t p]
  (if (< (count v) t)
    (into [] (map (fn [e] (explore e t p)) v))
    (big-vector v t p)))

(defn big-vector
  [v t p]
  (let [peeked (take p v)
        rest (drop p v)
        more (str (count rest) " more entries")]
    (conj (mapv (fn [e] (explore e t p)) peeked)
          more)))
{% endhighlight %}

I'll let the appropriate function look inside of the structure and if the size is under a threshold I'll show it all,
otherwise I'll only show a part of it.

{% highlight clojure %}
(explore tracks 10 1)
=> {"30645" {"Library Folder Count" 1, :more "26 more entries"}, :more "23176 more entries"}
{% endhighlight %}

Oh yeah, I already know there are 27 keys in that map.

{% highlight clojure %}
(explore tracks 28 1)
=>
{"30645" {"Library Folder Count" 1,
          "Disc Number" 1,
          "Total Time" 332066,
          "Disc Count" 1,
          "Equalizer" "Rock",
          "Persistent ID" "5B219C7E2973CDCE",
          "Artist" "Joe Louis Walker",
          "Album" "Live At Slim's Volume 1",
          "Play Date" 3428123742,
          "Location" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/Music/Joe%20Louis%20Walker/Live%20At%20Slim's%20Volume%201/03%20Don't%20Play%20Games.m4a",
          "Track ID" 30645,
          "Track Count" 10,
          "Track Number" 3,
          "Date Modified" #object[org.joda.time.DateTime 0x592ec0f3 "2011-07-03T17:03:17.000-05:00"],
          "Bit Rate" 675,
          "Size" 28068234,
          "Date Added" #object[org.joda.time.DateTime 0x79eae4d "2011-07-03T17:02:51.000-05:00"],
          "Play Count" 1,
          "Year" 2008,
          "File Type" 1295270176,
          "Track Type" "File",
          "Name" "Don't Play Games",
          "Sample Rate" 44100,
          "Genre" "Blues",
          "Play Date UTC" #object[org.joda.time.DateTime 0x5b14aca "2012-08-18T08:35:42.000-05:00"],
          "File Folder Count" 5,
          "Kind" "Apple Lossless audio file"},
 :more "23176 more entries"}

 (explore tracks 28 3)
 =>
 {"30645" {"Library Folder Count" 1,
           "Disc Number" 1,
           "Total Time" 332066,
           "Disc Count" 1,
           "Equalizer" "Rock",
           "Persistent ID" "5B219C7E2973CDCE",
           "Artist" "Joe Louis Walker",
           "Album" "Live At Slim's Volume 1",
           "Play Date" 3428123742,
           "Location" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/Music/Joe%20Louis%20Walker/Live%20At%20Slim's%20Volume%201/03%20Don't%20Play%20Games.m4a",
           "Track ID" 30645,
           "Track Count" 10,
           "Track Number" 3,
           "Date Modified" #object[org.joda.time.DateTime 0x592ec0f3 "2011-07-03T17:03:17.000-05:00"],
           "Bit Rate" 675,
           "Size" 28068234,
           "Date Added" #object[org.joda.time.DateTime 0x79eae4d "2011-07-03T17:02:51.000-05:00"],
           "Play Count" 1,
           "Year" 2008,
           "File Type" 1295270176,
           "Track Type" "File",
           "Name" "Don't Play Games",
           "Sample Rate" 44100,
           "Genre" "Blues",
           "Play Date UTC" #object[org.joda.time.DateTime 0x5b14aca "2012-08-18T08:35:42.000-05:00"],
           "File Folder Count" 5,
           "Kind" "Apple Lossless audio file"},
  "43515" {"Library Folder Count" 1,
           "Total Time" 3539200,
           "Persistent ID" "4208E480BB2E44CC",
           "Comments" "Long before being recognized as an outstanding interviewer and talk show host, Dick Cavett started his career as a performer by doing magic.  On this episode of The Spirit of Magic podcast, Mr. Cavett talks about his lifelong passion for magic, his apprec",
           "Artist" "Dodd Vickers",
           "Album" "The Magic Newswire",
           "Play Date" 3475728561,
           "Location" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/Podcasts/The%20Magic%20Newswire/MNW%20%23195%20__%20DICK%20CAVETT.mp3",
           "Composer" "Wizard & The Vegas Showgirl",
           "Track ID" 43515,
           "Date Modified" #object[org.joda.time.DateTime 0x6de8524d "2013-09-16T09:39:16.000-05:00"],
           "Sort Album" "Magic Newswire",
           "Bit Rate" 128,
           "Size" 56898510,
           "Date Added" #object[org.joda.time.DateTime 0x68dc8150 "2013-09-16T09:39:16.000-05:00"],
           "Artwork Count" 1,
           "Release Date" #object[org.joda.time.DateTime 0x7d16387d "2010-06-22T11:27:40.000-05:00"],
           "Play Count" 1,
           "Year" 2010,
           "Track Type" "File",
           "Name" "MNW #195 :: DICK CAVETT",
           "Sample Rate" 44100,
           "Genre" "Podcast",
           "Play Date UTC" #object[org.joda.time.DateTime 0x213534ce "2014-02-20T08:09:21.000-06:00"],
           "File Folder Count" 4,
           "Podcast" true,
           "Kind" "MPEG audio file"},
  "9333" {"Library Folder Count" 1,
          "Disc Number" 1,
          "Total Time" 307400,
          "Disc Count" 1,
          "Persistent ID" "941D2F3691078CC6",
          "Artist" "Israel Kamakawiwo'ole",
          "Play Date" 3279050530,
          "Location" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/Music/Israel%20Kamakawiwo'ole/Unknown%20Album/01%20Somewhere%20Over%20The%20Rainbow%20-%20What%20a%20Wonderful%20World.m4a",
          "Track ID" 9333,
          "Track Count" 1,
          "Track Number" 1,
          "Date Modified" #object[org.joda.time.DateTime 0x5c4f6611 "2007-11-27T22:14:19.000-06:00"],
          "Bit Rate" 248,
          "Size" 9619241,
          "Date Added" #object[org.joda.time.DateTime 0x56117e25 "2007-11-27T22:13:27.000-06:00"],
          "Play Count" 1,
          "File Type" 1295270176,
          "Track Type" "File",
          "Name" "Somewhere Over The Rainbow - What a Wonderful World",
          "Sample Rate" 44100,
          "Play Date UTC" #object[org.joda.time.DateTime 0x4ef3ca4c "2007-11-27T23:22:10.000-06:00"],
          "File Folder Count" 5,
          "Kind" "AAC audio file"},
  :more "23174 more entries"}
{% endhighlight %}

It's track info keyed on the `"Track ID"`. I'll probably just want the tracks in a flat collection.
Also, It looks like there's some variation between track map keys. This kind of thing makes me curious (well, more curious).
I wonder what is the maximum set of track keys across all of the tracks?
Also, what is the full set of keys ever used? Should just be a function away. Actually, let's make it a few functions and
flatten down the track info while we're at it.

{% highlight clojure %}
(def tracks (into [] (vals (itl "Tracks"))))
=> #'itq.parse/tracks

(defn track-keys-count
  [tracks]
  (reduce (fn [ks m]
            (into ks
                  (map #(vector % (inc (get ks % 0)))
                       (keys m))))
          {}
          tracks))
=> #'itq.parse/track-keys-count

(sort (track-keys-count tracks))
=>
(["Album" 22680]
 ["Album Artist" 2287]
 ["Album Rating" 45]
 ["Album Rating Computed" 34]
 ["Artist" 22192]
 ["Artwork Count" 9654]
 ["BPM" 115]
 ["Bit Rate" 19606]
 ["Clean" 100]
 ["Comments" 2761]
 ["Compilation" 646]
 ["Composer" 11805]
 ["Content Rating" 546]
 ["Date Added" 23177]
 ["Date Modified" 20892]
 ["Disabled" 1]
 ["Disc Count" 13776]
 ["Disc Number" 14445]
 ["Episode" 312]
 ["Episode Order" 167]
 ["Equalizer" 14859]
 ["Explicit" 69]
 ["File Folder Count" 19635]
 ["File Type" 14792]
 ["Genre" 22710]
 ["Grouping" 542]
 ["Has Video" 1864]
 ["Kind" 21354]
 ["Library Folder Count" 19635]
 ["Location" 22632]
 ["Loved" 2]
 ["Movie" 1509]
 ["Music Video" 3]
 ["Name" 23177]
 ["Part Of Gapless Album" 195]
 ["Persistent ID" 23177]
 ["Play Count" 11997]
 ["Play Date" 11191]
 ["Play Date UTC" 11191]
 ["Podcast" 3131]
 ["Protected" 633]
 ["Purchased" 1287]
 ["Rating" 45]
 ["Rating Computed" 41]
 ["Release Date" 6625]
 ["Sample Rate" 18498]
 ["Season" 61]
 ["Series" 318]
 ["Size" 21973]
 ["Skip Count" 1677]
 ["Skip Date" 1638]
 ["Sort Album" 3142]
 ["Sort Album Artist" 182]
 ["Sort Artist" 2062]
 ["Sort Composer" 75]
 ["Sort Name" 1411]
 ["Sort Series" 21]
 ["Stop Time" 1]
 ["TV Show" 306]
 ["Total Time" 21323]
 ["Track Count" 15762]
 ["Track ID" 23177]
 ["Track Number" 16968]
 ["Track Type" 23177]
 ["Unplayed" 3287]
 ["Volume Adjustment" 5]
 ["Year" 18734]
 ["iTunesU" 2535])
{% endhighlight %}

That's the full set of keys and how many times each is used. I would like to know the type of values behind each key,
and make sure we're done with colletions.

{% highlight clojure %}
(defn track-keys-class
  [tracks]
  (reduce (fn [ks m]
            (into ks
                  (map #(vector % (class (get m %)))
                       (keys m))))
          {}
          tracks))
=> #'itq.parse/track-keys-class

(sort (track-keys-class tracks))
=>
(["Album" java.lang.String]
 ["Album Artist" java.lang.String]
 ["Album Rating" java.lang.Long]
 ["Album Rating Computed" java.lang.Boolean]
 ["Artist" java.lang.String]
 ["Artwork Count" java.lang.Long]
 ["BPM" java.lang.Long]
 ["Bit Rate" java.lang.Long]
 ["Clean" java.lang.Boolean]
 ["Comments" java.lang.String]
 ["Compilation" java.lang.Boolean]
 ["Composer" java.lang.String]
 ["Content Rating" java.lang.String]
 ["Date Added" org.joda.time.DateTime]
 ["Date Modified" org.joda.time.DateTime]
 ["Disabled" java.lang.Boolean]
 ["Disc Count" java.lang.Long]
 ["Disc Number" java.lang.Long]
 ["Episode" java.lang.String]
 ["Episode Order" java.lang.Long]
 ["Equalizer" java.lang.String]
 ["Explicit" java.lang.Boolean]
 ["File Folder Count" java.lang.Long]
 ["File Type" java.lang.Long]
 ["Genre" java.lang.String]
 ["Grouping" java.lang.String]
 ["Has Video" java.lang.Boolean]
 ["Kind" java.lang.String]
 ["Library Folder Count" java.lang.Long]
 ["Location" java.lang.String]
 ["Loved" java.lang.Boolean]
 ["Movie" java.lang.Boolean]
 ["Music Video" java.lang.Boolean]
 ["Name" java.lang.String]
 ["Part Of Gapless Album" java.lang.Boolean]
 ["Persistent ID" java.lang.String]
 ["Play Count" java.lang.Long]
 ["Play Date" java.lang.Long]
 ["Play Date UTC" org.joda.time.DateTime]
 ["Podcast" java.lang.Boolean]
 ["Protected" java.lang.Boolean]
 ["Purchased" java.lang.Boolean]
 ["Rating" java.lang.Long]
 ["Rating Computed" java.lang.Boolean]
 ["Release Date" org.joda.time.DateTime]
 ["Sample Rate" java.lang.Long]
 ["Season" java.lang.Long]
 ["Series" java.lang.String]
 ["Size" java.lang.Long]
 ["Skip Count" java.lang.Long]
 ["Skip Date" org.joda.time.DateTime]
 ["Sort Album" java.lang.String]
 ["Sort Album Artist" java.lang.String]
 ["Sort Artist" java.lang.String]
 ["Sort Composer" java.lang.String]
 ["Sort Name" java.lang.String]
 ["Sort Series" java.lang.String]
 ["Stop Time" java.lang.Long]
 ["TV Show" java.lang.Boolean]
 ["Total Time" java.lang.Long]
 ["Track Count" java.lang.Long]
 ["Track ID" java.lang.Long]
 ["Track Number" java.lang.Long]
 ["Track Type" java.lang.String]
 ["Unplayed" java.lang.Boolean]
 ["Volume Adjustment" java.lang.Long]
 ["Year" java.lang.Long]
 ["iTunesU" java.lang.Boolean])

(defn track-keys-info
  [tracks]
  (let [counts (track-keys-count tracks)
        classes (track-keys-class tracks)]
    (merge-with vector classes counts)))
=> #'itq.parse/track-keys-info

(sort (track-keys-info tracks))
=>
(["Album" [java.lang.String 22680]]
 ["Album Artist" [java.lang.String 2287]]
 ["Album Rating" [java.lang.Long 45]]
 ["Album Rating Computed" [java.lang.Boolean 34]]
 ["Artist" [java.lang.String 22192]]
 ["Artwork Count" [java.lang.Long 9654]]
 ["BPM" [java.lang.Long 115]]
 ["Bit Rate" [java.lang.Long 19606]]
 ["Clean" [java.lang.Boolean 100]]
 ["Comments" [java.lang.String 2761]]
 ["Compilation" [java.lang.Boolean 646]]
 ["Composer" [java.lang.String 11805]]
 ["Content Rating" [java.lang.String 546]]
 ["Date Added" [org.joda.time.DateTime 23177]]
 ["Date Modified" [org.joda.time.DateTime 20892]]
 ["Disabled" [java.lang.Boolean 1]]
 ["Disc Count" [java.lang.Long 13776]]
 ["Disc Number" [java.lang.Long 14445]]
 ["Episode" [java.lang.String 312]]
 ["Episode Order" [java.lang.Long 167]]
 ["Equalizer" [java.lang.String 14859]]
 ["Explicit" [java.lang.Boolean 69]]
 ["File Folder Count" [java.lang.Long 19635]]
 ["File Type" [java.lang.Long 14792]]
 ["Genre" [java.lang.String 22710]]
 ["Grouping" [java.lang.String 542]]
 ["Has Video" [java.lang.Boolean 1864]]
 ["Kind" [java.lang.String 21354]]
 ["Library Folder Count" [java.lang.Long 19635]]
 ["Location" [java.lang.String 22632]]
 ["Loved" [java.lang.Boolean 2]]
 ["Movie" [java.lang.Boolean 1509]]
 ["Music Video" [java.lang.Boolean 3]]
 ["Name" [java.lang.String 23177]]
 ["Part Of Gapless Album" [java.lang.Boolean 195]]
 ["Persistent ID" [java.lang.String 23177]]
 ["Play Count" [java.lang.Long 11997]]
 ["Play Date" [java.lang.Long 11191]]
 ["Play Date UTC" [org.joda.time.DateTime 11191]]
 ["Podcast" [java.lang.Boolean 3131]]
 ["Protected" [java.lang.Boolean 633]]
 ["Purchased" [java.lang.Boolean 1287]]
 ["Rating" [java.lang.Long 45]]
 ["Rating Computed" [java.lang.Boolean 41]]
 ["Release Date" [org.joda.time.DateTime 6625]]
 ["Sample Rate" [java.lang.Long 18498]]
 ["Season" [java.lang.Long 61]]
 ["Series" [java.lang.String 318]]
 ["Size" [java.lang.Long 21973]]
 ["Skip Count" [java.lang.Long 1677]]
 ["Skip Date" [org.joda.time.DateTime 1638]]
 ["Sort Album" [java.lang.String 3142]]
 ["Sort Album Artist" [java.lang.String 182]]
 ["Sort Artist" [java.lang.String 2062]]
 ["Sort Composer" [java.lang.String 75]]
 ["Sort Name" [java.lang.String 1411]]
 ["Sort Series" [java.lang.String 21]]
 ["Stop Time" [java.lang.Long 1]]
 ["TV Show" [java.lang.Boolean 306]]
 ["Total Time" [java.lang.Long 21323]]
 ["Track Count" [java.lang.Long 15762]]
 ["Track ID" [java.lang.Long 23177]]
 ["Track Number" [java.lang.Long 16968]]
 ["Track Type" [java.lang.String 23177]]
 ["Unplayed" [java.lang.Boolean 3287]]
 ["Volume Adjustment" [java.lang.Long 5]]
 ["Year" [java.lang.Long 18734]]
 ["iTunesU" [java.lang.Boolean 2535]])
{% endhighlight %}

Well, I'm happy with this for now. I have a good understanding of the data. I remember something about a `"Playlists"`
key so I better make sure that's nothing I care about. I'll just `explore` from the top.

{% highlight clojure %}
(explore itl 11 1)
=>
{"Major Version" 1,
 "Show Content Ratings" true,
 "Playlists" [{"Playlist Persistent ID" "7DFD01EA07FCC290",
               "All Items" true,
               "Visible" false,
               "Master" true,
               "Playlist Items" [{"Track ID" 6085} "20311 more entries"],
               "Playlist ID" 82971,
               "Name" "####!####"}
              "194 more entries"],
 "Music Folder" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/",
 "Minor Version" 1,
 "Date" #object[org.joda.time.DateTime 0x1b471e14 "2015-10-27T19:44:01.000-05:00"],
 "Tracks" {"30645" {"Library Folder Count" 1, :more "26 more entries"}, :more "23176 more entries"},
 "Application Version" "12.3.1.23",
 "Library Persistent ID" "88ABD0BA83F503C5",
 "Features" 5}

(explore itl 11 3)
=>
{"Major Version" 1,
 "Show Content Ratings" true,
 "Playlists" [{"Playlist Persistent ID" "7DFD01EA07FCC290",
               "All Items" true,
               "Visible" false,
               "Master" true,
               "Playlist Items" [{"Track ID" 6085} {"Track ID" 6083} {"Track ID" 6091} "20309 more entries"],
               "Playlist ID" 82971,
               "Name" "####!####"}
              {"Playlist Persistent ID" "73F3C095D7F3E012",
               "Music" true,
               "All Items" true,
               "Smart Info" #object["[B" 0x72669460 "[B@72669460"],
               "Distinguished Kind" 4,
               "Smart Criteria" #object["[B" 0x23eee98c "[B@23eee98c"],
               "Playlist Items" [{"Track ID" 8083} {"Track ID" 7031} {"Track ID" 5705} "15605 more entries"],
               "Playlist ID" 103282,
               "Name" "Music"}
              {"Playlist Persistent ID" "8BFA8F6E5F0B5084",
               "All Items" true,
               "Smart Info" #object["[B" 0x7a605286 "[B@7a605286"],
               "Distinguished Kind" 47,
               "Smart Criteria" #object["[B" 0x5f15adc4 "[B@5f15adc4"],
               "Playlist Items" [{"Track ID" 6697} {"Track ID" 6695} {"Track ID" 8293}],
               "Playlist ID" 118893,
               "Name" "Music Videos"}
              "192 more entries"],
 "Music Folder" "file:///Volumes/mediaHD/iTunes/iTunes%20Media/",
 "Minor Version" 1,
 "Date" #object[org.joda.time.DateTime 0x1b471e14 "2015-10-27T19:44:01.000-05:00"],
 "Tracks" {"30645" {"Library Folder Count" 1, "Disc Number" 1, "Total Time" 332066, :more "24 more entries"},
           "43515" {"Library Folder Count" 1,
                    "Total Time" 3539200,
                    "Persistent ID" "4208E480BB2E44CC",
                    :more "24 more entries"},
           "9333" {"Library Folder Count" 1, "Disc Number" 1, "Total Time" 307400, :more "20 more entries"},
           :more "23174 more entries"},
 "Application Version" "12.3.1.23",
 "Library Persistent ID" "88ABD0BA83F503C5",
 "Features" 5}
{% endhighlight %}

A playlist looks like some info accompanying a list of track ids. None of which I care about right now.

## Leftovers

I'm certain that I will someday do something similar with some other data so I want to save that `explore` function to
use later. I gathered up the definitions from my REPL history, added code for sets, sequences, and some tests.
The project is on github at [https://github.com/hby/repl](https://github.com/hby/repl).
Just pull the code down and follow the `README.md` instructions.

It's not very hard to begin to put together a bunch of functions that you can use for exploration in any REPL.

## Tell your story

If you're new to Clojure, or functional programming, or languages that have a REPL, I hope this encourages you to
crack open a REPL and start to think about your own story. It's a means to whatever end you have in mind.

> “I knew when I met you an adventure was going to happen.”<br>
  ― _A.A. Milne_

## But still, don't put your eye out

`clojure.core` defines the vars `*print-length*` and `*print-level*` that a REPL can use to limit its output.
You can set a var in the REPL by

{% highlight clojure %}
(set! *print-length* 25)
{% endhighlight %}

I have found that not all REPL environments honor this. Could be something I've done wrong, though.
But even if they do, I wanted more flexibility than they provide.
However, they are good to know about and setting values for them does make a good safety measure in case you do
stumble across some data that is larger than you thought.
