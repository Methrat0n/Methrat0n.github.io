---
permalink: a-comparaison-between-promise-future
---

# A comparison between Promise and Future

About two months ago a co-worker asks me why the native Promise has so bad performance and what he should use instead. It's just now that I remember the Future type of the library [Fluture](https://github.com/fluture-js/Fluture). But, I could not just say "Hey dude, use this it's awesome". Benchmarking was required.

To properly bench Promise against Future I tested two different cases: a simple resolve and an API call. For each one, I run a thousand Promises and Futures to average processing time.

If you want to play around with the examples, visit [the companion repo of this article](https://github.com/Methrat0n/Promise-vs-Future).

## The resolve case
The code here is simple, it can either be used in NodeJS or the Browser. I used Chrome 61 on OSX.

```javascript
for(let i = 0; i < 1000; i++)
  new Promise((resolve, reject) => echoTime(resolve))
```
The average time is **0.031 ms** in NodeJS and **0.188 ms** in the Browser.

Using a similar code :

```javascript
for(let i = 0; i < 1000; i++)
  Future.node(echoTime).fork(noop, noop)
```  

I found **0.0365 ms** in NodeJS and **0.160 ms** in the Browser.

Promises seems to have better performances in NodeJS and Future in the Browser, but it is not far appart.

## The API call case
Ones again, nothing overly complicated. I use the same code in both environment.

```javascript
for(let i = 0; i < 1000; i++)
  fetch("https://jsonplaceholder.typicode.com").then(echoTime)
```

Which give me an average time of **4.887 ms** in NodeJS and **2.958 ms** in the Browser.
With some minor changes I can run the bench for Future:

```javascript
const fetchJson = fetchf("https://jsonplaceholder.typicode.com")

for(let i = 0; i < 1000; i++)
  fetchJson.map(echoTime).fork(noop, noop)
```
The results seem very alike as I found **4.828 ms** in NodeJS and **3.001 ms** in the Browser.

## Conclusion
After these benches, I can't say Fluture is **the** new library everyone must use. It doesn't appear we get any boost in performance in most cases.

To be fair, I have to mention the lazy nature of Future. Every callback passed to a Future will only be executed when the fork function is called. This can be a gain if your use case can benefit from it.

I could also see things the other way around: this library gives us new features and a cool syntax without any performance drawback. It's up to you now to see if you like it.