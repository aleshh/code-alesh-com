---
title: "Slideshow with updating URI location"
date: "2019-01-28"
tags: ["JavaScript"]
---

```js
var fadeDuration = 2000

$(function () {
  var $slides = $("#slideshow > div")
  $slides.hide()

  var currentSlide = Number(location.hash.slice(1))
  $slides.eq(currentSlide).fadeIn(fadeDuration)

  $slides.click(function () {
    $slides.eq(currentSlide).fadeOut(fadeDuration)
    currentSlide++
    if (currentSlide == $slides.length) currentSlide = 0
    $slides.eq(currentSlide).fadeIn(fadeDuration)
    location = "#" + currentSlide
  })
})
```

This is pretty rough, but it does two notable things. First, a slideshow that advances on click with a fade from one div to another. More interestingly, it sets the URI with the slide number, and retrieves the number on load. In other words, it's an bare-bones implementation of a persistent URI system.
