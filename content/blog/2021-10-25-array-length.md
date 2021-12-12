---
title: "Checking the real length of an array"
date: "2021-10-25"
tags: ["git", "Shell"]
---

Consider this:

```
const x = [1, 2];
x.length = 3;
```

<!--more-->

Generally, if you try to do a `.map()` or something on this array it will work fine, the empty items added by setting an incorrect length will be ignored. But sometimes you need to use the length to calculate something, and receiving an array someone has monkeyed with like this might be dangerous.

Here's one way to get the actual number of items in an array:

```
const arrLength = (arr) => arr.reduce((x) => x + 1, 0);
```

This can introduce a little bit of sanity into your life:

```
const x = [1, 2];
x.length = 3;
console.log(arrLength(x)); // 2

```

Or perhaps things have not been going so great for you, and you'd benefit from double-scoop of sanity:

```
function checkArr(arr) {
  if ((arr) => arr.reduce((x) => x + 1, 0) !== arr.length) {
    throw new Error('Uhh, why u messing with my 🧠, 🐶?');
  }
}
```
