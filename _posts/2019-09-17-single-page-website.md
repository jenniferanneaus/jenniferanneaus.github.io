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

My project uses the [Grayscale](https://startbootstrap.com/themes/grayscale/) theme by Start Bootstrap. For complete beginners, there's a [tutorial on Medium](https://medium.com/@yli0607x/how-to-use-bootstrap-themes-on-ruby-on-rails-in-5-minutes-8e6f9542f6d8) that explains how to configure your Rails app to get started with themes. Once the basic structure was already in place, customising the theme was a lot of fun. The images in the website are all from [Pixabay](https://pixabay.com/) or [Pexels](https://www.pexels.com/), which both have excellent collections of free pictures.

After following the instructions in the tutorial, I created a file called ```custom.css.scss``` in the ```/app/assets/stylesheets``` folder. The ```.scss``` extension tells the asset pipeline to preprocess this file using Sass, and the ```.css``` extension indicates it is a CSS file. The first class I wrote in my custom CSS file was to define a column that takes up half the horizontal width of the page; the Grayscale theme has a contact section with three columns (address, email and phone) but I only wanted two. The code for this was quite simple:

```css
.column-half {
	float: left;
	width: 40%;
	margin-right: 5%;
	margin-left: 5%;
}
```

Overall I added just under a dozen CSS classes, to do minor things like change the masthead picture and the appearance of certain sections of text. Moving on to the HTML code, I needed to change the "project" images to my "tomatoes" images. Previously, the images were placed on the page using the code:
```html
<img class="img-fluid" src="img/demo-image-01.jpg" alt="">
```
Since I was working in Rails, I used embedded ruby to insert the images. Embedded ruby makes it easy to reference images in [the asset pipeline](https://guides.rubyonrails.org/asset_pipeline.html) as the path to the file does not need to be written in full. For example, after placing my ```tomatoes.jpg``` file in the ```app/assets/images``` folder, this code could be used to display the image:
```erb
<%= image_tag("tomatoes.jpg", :alt => "", :class => 'img-fluid') %>
```

Once I was happy with how the app looked, I deployed it to [Heroku](https://www.heroku.com/). This is a great way to test whether your app works in production; for those who want to learn more, Michael Hartl has written a very helpful [Rails tutorial](https://www.railstutorial.org/) that includes instructions for setting up and using Heroku.

It turned out my app had a few bugs; these weren't visible when viewed in development mode or in Firefox/Edge/IE, but the deployed website displayed incorrectly in Chrome. An invaluable resource in debugging these issues was [Validator.nu](https://html5.validator.nu/), an HTML validator which checks the source code for your site and will warn you of any tags that are stray or incorrectly placed. As HTML is still new and somewhat unfamiliar to me, I had placed some of my elements incorrectly. So, then, this was a good opportunity to learn.

After the app was debugged and successfully deployed, my first foray into customising a Bootstrap theme was complete. You can see the source code for my project [here](https://github.com/jenniferanne1991/grayscale_theme_app), or view the deployed website [here](http://tomatoes-of-thomastown.herokuapp.com/).