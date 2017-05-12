## RX-JAVA in Practice
<span style="font-family:Helvetica Neue; font-weight:bold">As a Knowledge Sharing session at <span style="color:#e49436">Networked Assets</span></span>
<br>
<span style="font-family:Helvetica Neue; font-weight:bold">By <span style="color:#e49436">JAROSŁAW WATRAL</span></span>


---

## What RxJava is
<span style="font-size:0.6em; color:gray">And what it isn't.</span>

+++

>RxJava is a Java VM implementation of Reactive Extensions: a library for composing asynchronous and event-based programs by using observable sequences.

+++

![Image-Relative](https://pbs.twimg.com/media/B5oIZCXCMAI_vTn.jpg:large)

+++

## Who uses RXJAVA?
![Image-Relative](assets/image/who-uses-reactivex.png)

---

## [OFFTOP] Will the name RXJAVA stay? ;-)

+++

## <a href="http://blog.vavr.io/javaslang-changes-name-to-vavr/" target="_blank">Sad story of JavaSlang</a>

+++

## Everything's working great - let's order some stickers!
![Image-Relative](https://pbs.twimg.com/media/C7qkDCEVwAA3T8A.jpg:large)

+++

## But then "all the evil" happened

+++

## And the bad guys arrived

+++

![Image-Relative](https://upload.wikimedia.org/wikipedia/commons/thumb/5/50/Oracle_logo.svg/2000px-Oracle_logo.svg.png)

+++

## The only sensible reaction...

+++

![Image-Relative](https://media.giphy.com/media/3og0INyCmHlNylks9O/giphy.gif)

+++

## Fortunately, Engineers always find easy solutions 

+++

![Image-Relative](http://blog.vavr.io/content/images/2017/04/vavr-sticker-1.png)

+++

## And that's how Vavr.io was born

+++

## Should we expect Rx Vavr?
<span style="font-size:0.6em; color:gray">Let's ask Oracle's Legal Department ;-)</span> <!-- .element: class="fragment" -->

---

## Story of Netflix and their adaptation of RX Java
<span style="font-size:0.6em; color:gray">See <a href="https://medium.com/netflix-techblog/reactive-programming-in-the-netflix-api-with-rxjava-7811c3a1496a" target="_blank">this article</a> for details.</span>

+++

## How it was
![Image-Relative](https://cdn-images-1.medium.com/max/1400/0*hH8iqS2HLJI9DUbz.png)

+++

## How they wanted it to be
![Image-Relative](https://cdn-images-1.medium.com/max/800/0*pjGnJ3RF8wdXOLrC.png)

+++

## Embrace Concurrency
Note:
Server-side concurrency is needed to effectively reduce network chattiness. Without concurrent execution on the server, a single “heavy” client request might not be much better than many “light” requests because each network request from a device naturally executes in parallel with other network requests. If the server-side execution of a collapsed “heavy” request does not achieve a similar level of parallel execution it may be slower than the multiple “light” requests even accounting for saved network latency.

+++

## Java Futures are Expensive to Compose
Note:
Java Futures are straight-forward to use for a single level of asynchronous execution but they start to add non-trivial complexity when they’re nested (prior to Java 8 CompletableFuture).
<br>
Conditional asynchronous execution flows become difficult to optimally compose (particularly as latencies of each request vary at runtime) using Futures. It can be done of course, but it quickly becomes complicated (and thus error prone) or prematurely blocks on ‘Future.get()’, eliminating the benefit of asynchronous execution.

+++

## Callbacks Have Their Own Problems
Note:
Callbacks offer a solution to the tendency to block on Future.get() by not allowing anything to block. They are naturally efficient because they execute when the response is ready.
Similar to Futures though, they are easy to use with a single level of asynchronous execution but become unwieldy with nested composition.

+++

## Some of the goals of Netflix were:
- Stay close to the original Rx.Net implementation while adjusting naming conventions and idioms to Java <!-- .element: class="fragment" -->
- All contracts of Rx should be the same <!-- .element: class="fragment" -->
- Target the JVM not a language. New language adapters can be contributed. <!-- .element: class="fragment" -->
- Support Java 6 (to include Android support) and higher with an eventual goal to target a build for Java 8 with its lambda support. (Update: Java 8 support was achieved without a separate build) <!-- .element: class="fragment" -->

+++

## Let's hear about an example of usage in netflix:

+++

## VideoService example
![YouTube Video](https://youtu.be/_t06LRX0DV0)

+++

```Groovy
  getListOfLists(userId).mapMany({ VideoList list ->
    list.getVideos() // for each VideoList we want to fetch the videos
      .take(10) // we only want the first 10 of each list
      .flatMap({ Video video -> 
        def m = video.getMetadata().map({ // for each video we want to fetch metadata
          Map<String, String> md -> 
          return [title: md.get("title"), // transform to the data and format we want
              length: md.get("duration")]
        })
        def b = video.getBookmark(userId).map({ 
          position -> return [bookmark: position]
        })
        def r = video.getRating(userId).map({ 
          VideoRating rating -> 
          return [rating: 
            [actual: rating.getActualStarRating(),
             average: rating.getAverageStarRating(),
             predicted: rating.getPredictedStarRating()]]
        })
        return Observable.zip(m, b, r, {
                metadata, bookmark, rating -> 
            return [id: video.videoId] << metadata << bookmark << rating
        })
      })   
  })
```
Note:
/**
 * Demonstrate how Rx is used to compose Observables together
 * such as how a web service would to generate a JSON response.
 * 
 * The simulated methods for the metadata represent different 
 * services that are often backed by network calls.
 * 
 * This will return a sequence of dictionaries such as this:
 * 
 *  [id:1000, title:video-1000-title, length:5428, bookmark:0, 
 *    rating:[actual:4, average:3, predicted:0]]
 */

---

## RX JAVA 2
<span style="font-size:0.6em; color:gray">See <a href="https://github.com/ReactiveX/RxJava/wiki/What's-different-in-2.0" target="_blank">What's different in 2.0</a> for details.</span>

---

## Let's learn some basic operators

<span style="font-size:0.6em; color:gray"><a href="http://rxmarbles.com/" target="_blank">RxMarbles</a></span>
<br>
<span style="font-size:0.6em; color:gray"><a href="http://reactivex.io/documentation/operators.html/" target="_blank">Docs</a></span>

---

## Exercises
<span style="font-size:0.6em; color:gray"><a href="https://github.com/jhusain/learnrxjava" target="_blank">GitHub - will use that</a></span>
<br>
<span style="font-size:0.6em; color:gray"><a href="https://github.com/nhpatt/RxInPractice" target="_blank">GitHub - and that</a></span>
<br>
<span style="font-size:0.6em; color:gray"><a href="https://github.com/xala3pa/rxjava-koans" target="_blank">GitHub</a></span>

---

## The end

---

## PS wanna also make such cool presentations?

+++

## JUST ADD <span style="color:#e49436; text-transform: none">PITCHME.md</span> ;)