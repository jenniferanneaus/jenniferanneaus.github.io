---
layout: post
current: post
cover:  assets/images/cherry-tomatoes.jpg
navigation: True
title: Creating a Single Page Website with Bootstrap
date: 2019-09-17 18:00:00
tags: [rails, bootstrap, html, css]
class: post-template
subclass: 'post tag-rails tag-bootstrap tag-html tag-css'
---

The goal for this project was to use a Bootstrap theme and apply it to a project of my own, gaining more of an appreciation of front end development.

My project uses the [Grayscale](https://startbootstrap.com/themes/grayscale/) theme by Start Bootstrap. For complete beginners, there's a [tutorial on Medium](https://medium.com/@yli0607x/how-to-use-bootstrap-themes-on-ruby-on-rails-in-5-minutes-8e6f9542f6d8) that explains how to configure your Rails app to get started with themes. Once the basic structure was already in place, customising the theme was a lot of fun. The images in the website are all from [Pixabay](https://pixabay.com/) and [Pexels](https://www.pexels.com/), which both have excellent collections of free pictures.

After following the instructions in the tutorial, I created a file called ```custom.css.scss``` in the ```/app/assets/stylesheets``` folder. The ```.scss``` extension tells the asset pipeline to preprocess this file using Sass, and the ```.css``` extension indicates it is a CSS file. The first class I wrote in my custom CSS file was to define a column that takes up half the horizontal width of the page; the Grayscale theme has a contact section with three columns (address, email and phone) but I only wanted two. The code for this was quite simple:

```css
.column-half {
	float: left;
	width: 40%;
	margin-right: 5%;
	margin-left: 5%;
}
```

Overall I added just under a dozen CSS classes, to do minor things like change the masthead picture and the appearance of certain sections of text. I then replaced the default html code for the "project" images:
```html
<img class="img-fluid" src="img/demo-image-01.jpg" alt="">
```
with embedded ruby code to display my "tomatoes" images:
```erb
<%= image_tag("tomatoes.jpg", :alt => "", :class => 'img-fluid mb-3 mb-lg-0') %>
```

Once I was happy with how the app looked, I deployed it to [Heroku](https://www.heroku.com/). This is a great way to test whether your app works in production; for those who want to learn more, Michael Hartl has written a very helpful [Rails tutorial](https://www.railstutorial.org/) that includes instructions for setting up and using Heroku.

As it turned out my app had a few bugs, which weren't present when viewed in Firefox/Edge/IE but broke the formatting in Chrome. An invaluable resource in debugging these issues was [Validator.nu](https://html5.validator.nu/), an HTML validator which checks the source code for your site and will warn you of any tags that are stray or incorrectly placed.

With the app debugged and successfully deployed, my first foray into customising a Bootstrap theme is complete. You can see the source code for my project [here](https://github.com/jenniferanne1991/grayscale_theme_app), or view the deployed website [here](http://tomatoes-of-thomastown.herokuapp.com/).