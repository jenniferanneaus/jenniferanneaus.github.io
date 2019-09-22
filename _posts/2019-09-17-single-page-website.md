---
layout: post
current: post
cover:  assets/images/cherry-tomatoes.jpg
navigation: True
title: Creating a Single Page Website with Bootstrap
date: 2019-09-17 18:00:00
tags: [ruby, rails, bootstrap, css]
class: post-template
subclass: 'post tag-ruby tag-rails tag-bootstrap tag-css'
---

The goal for this project was to use a Bootstrap theme and apply it to a project of my own, gaining more of an appreciation of front end development.

[My project](https://github.com/jenniferanne1991/grayscale_theme_app) uses the [Grayscale](https://startbootstrap.com/themes/grayscale/) theme by Start Bootstrap. For complete beginners, there's a [tutorial on Medium](https://medium.com/@yli0607x/how-to-use-bootstrap-themes-on-ruby-on-rails-in-5-minutes-8e6f9542f6d8) that explains how to configure your Rails app to get started with themes. Once the basic structure was already in place, it was easier to work out how to customise the theme.

After following the instructions in the tutorial, I created a file called ```custom.css.scss``` in the ```/app/assets/stylesheets``` folder. The ```.scss``` extension tells the asset pipeline to preprocess this file using Sass, and the ```.css``` extension indicates it is a css file. The first example of this was defining a column that takes up half the horizontal width of the page; the Grayscale theme has a contact section with three columns (address, email and phone) but I didn't want my webpage to contain my phone number. So, I wrote a simple css class as follows:

```css
.column-half {
	float: left;
	width: 40%;
	margin-right: 5%;
	margin-left: 5%;
}
```