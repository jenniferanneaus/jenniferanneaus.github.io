---
layout: post
current: post
cover: assets/images/covers/twelve-dom.jpg
navigation: True
title: Twelve Days of Mechsmas
date: 2019-12-01 20:00:00
# tags: react css
class: post-template
subclass: "post tag-react tag-css"
---

A friend of mine had an interesting idea for an art project not long ago, and asked me to make a website for it. The project involved using graphic elements such as clip paths and blur, and the overall result was pleasing.

The project is called "Twelve Days of Mechsmas" and was designed to be a daily art challenge. Over the month of December, he planned to create twelve 3D mech (giant robot) models, each one incorporating an aspect of one of the twelve days of Christmas. He provided me with a visual design and some specifications for behaviour, and I created a [React app](https://github.com/jenniferanneaus/twelve_dom_app) to meet his needs.

One simple way to add interest to the design was to use CSS [clip paths](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path) to shape the thumbnails for each model. These allow you to clip an image according to a basic shape such as a circle or polygon. In the case of a polygon, the path is created by straight lines joining a sequence of points. Each pair of values in the clip path describes an x- and y-coordinate, where `0% 0%` is the top left corner of the image and `100% 100%` is the bottom right. So, setting the property

```css
clip-path: polygon(50% 0, 100% 50%, 50% 100%, 0 50%);
```

would create a diamond shaped clipping region like the one below.

![An image that was previously square but has been clipped in the shape of an isoceles triangle](assets/images/posts/twelve-dom-clip-path.png)

To make it easier to experiment with clip paths, [Bennett Feely](https://bennettfeely.com/clippy/) has created a tool you can use for free. One of the interesting things about clip paths is that they can be used to split an image into disjoint parts. For the Twelve Days of Mechsmas icons, the following clip path was used:

```css
clip-path: polygon(
  100% 20%,
  100% 90%,
  90% 100%,
  10% 100%,
  0 90%,
  0 20%,
  15% 5%,
  35% 5%,
  40% 0,
  80% 0,
  100% 20%,
  100% 15%,
  85% 0,
  90% 0,
  100% 10%
);
```

to produce the icon shown below.

![An image that has been clipped into two separate pieces](assets/images/posts/twelve-dom-clip-path2.png)

My friend did not end up pursuing the project so the icons on the site do not link to anything, but the final result of the website is shown below. You can [view the source code here](https://github.com/jenniferanneaus/twelve_dom_app).

![An animated image of the twelve days of Mechsmas website with robots and animated effects](assets/images/posts/twelve-dom-website.gif)
