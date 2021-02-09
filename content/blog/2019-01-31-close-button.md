---
title: "Adding a 'close' button"
date: "2019-01-31"
tags: ["JavaScript"]
---

```js
function goBack() {
  if (document.referrer == "https://alesh.com/") {
    window.history.back()
  } else {
    window.location.href = "https://alesh.com/"
  }
}
```

<a class="close-button" ></a>

I wanted to create a button with a "close" icon for my <a href="https://alesh.com">website</a> that would make the feeling of going back to the home page feel light and easy. The homepage is fairly long, so I also wanted it to return to the same scroll position it was in when the user clicked. Solution, `<a href="javascript:history.back()">Back</a>`. Problem is, what if someone got to the page by following a link from another site, or what if I want to email someone a link to a page?

Then we want the button to be a regular link to the homepage. Result above, activated by `<a class="close-button" href="#" onclick="goBack(); return false;" ></a>`. This CSS makes a nice fixed circle with a close 'X' icon in the top right corner:

<!--more-->

```css
.close-button {
  position: fixed;
  top: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  border-radius: 25px;
  background-color: rgba(255, 255, 255, 0.9);
}

.close-button:before,
.close-button:after {
  content: "";
  position: absolute;
  width: 34px;
  height: 2px;
  background-color: black;
  top: 24px;
}

.close-button:before {
  -webkit-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  transform: rotate(45deg);
  left: 8px;
}

.close-button:after {
  -webkit-transform: rotate(-45deg);
  -moz-transform: rotate(-45deg);
  transform: rotate(-45deg);
  right: 8px;
}
```
