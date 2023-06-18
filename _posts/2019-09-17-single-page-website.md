---
layout: post
current: post
cover: assets/images/covers/cherry-tomatoes.jpg
navigation: True
title: Creating a Single Page Website with Bootstrap
date: 2019-09-17 18:00:00
tags: rails bootstrap
class: post-template
subclass: "post tag-rails tag-bootstrap tag-html tag-css"
---

The goal for this project was to use a Bootstrap theme and apply it to a project of my own, gaining more of an appreciation of front end development.

Although Rails has been interesting to learn, there's something unsatisfying about creating apps that consist of plain text and unstyled buttons. Having used some basic Bootstrap features in tutorials, I wanted to learn more about how to integrate and customise Bootstrap themes in Rails apps. My project uses the [Grayscale](https://startbootstrap.com/themes/grayscale/) theme by Start Bootstrap.

There are a few tutorials out there to help you get started, and I used [this one](https://medium.com/@yli0607x/how-to-use-bootstrap-themes-on-ruby-on-rails-in-5-minutes-8e6f9542f6d8). The basic idea is that, once your app has the necessary gems in place, you can use a theme by copying the HTML from an existing example into either your `app/views/layouts/application.html.erb` file (for global styles) or one of the `.html.erb` files in the `app/views` folder (for page specific styles). Once the basic structure is already in place, customising the theme is a lot of fun and doesn't require any in-depth knowledge of HTML or CSS. The images I used are from [Pixabay](https://pixabay.com/) and [Pexels](https://www.pexels.com/), which both have excellent collections of free pictures. Some screenshots from my app are below.

![Tomatoes of Thomastown header: seedlings and lots of different coloured tomatoes](assets/images/posts/tomatoes_1.jpg)
![Tomatoes of Thomastown main body: descriptions about the tomato varieties I am currently growing](assets/images/posts/tomatoes_2.jpg)
![Tomatoes of Thomastown footer](assets/images/posts/tomatoes_3.jpg)

To add custom css to your project, you can create a `custom.css.scss` file in the `/app/assets/stylesheets` folder and write your custom code there. The `.scss` extension tells the asset pipeline to preprocess the file using Sass, which gets compiled back to standard CSS but offers additional syntax such as functions and nested rules. For more about what Sass can do, check out [this article](https://www.freecodecamp.org/news/the-complete-guide-to-scss-sass-30053c266b23/).

In your `custom.css.scss` file, it is fairly straightforward to tweak the appearance of your chosen theme. The first class I wrote was to define a column that takes up half the horizontal width of the page, since the Grayscale theme has a contact section with three columns (address, email and phone) but I didn't want the third one. The code for this was as follows:

```css
.column-half {
  float: left;
  width: 40%;
  margin-right: 5%;
  margin-left: 5%;
}
```

Overall I added just under a dozen CSS classes. I also made minor changes to the HTML code to ensure the custom classes were being used and, of course, to change the text on the webpage. For the images, I changed the existing HTML code:

```html
<img class="img-fluid" src="img/demo-image-01.jpg" alt="" />
```

to embedded ruby code:

```erb
<%= image_tag("tomatoes.jpg", :alt => "", :class => 'img-fluid') %>
```

which makes it easier to reference images in [the asset pipeline](https://guides.rubyonrails.org/asset_pipeline.html). All I needed to do was make sure my `tomatoes.jpg` image was in the `app/assets/images` folder, and then the embedded ruby code works without having to write the full path to the file.

Once I was happy with how the app looked, I deployed it to [Heroku](https://www.heroku.com/). This is a great way to test whether your app displays and behaves correctly in production. For those who want to learn more, Michael Hartl has written a very helpful [Rails tutorial](https://www.railstutorial.org/) that includes instructions for setting up and using Heroku. It turned out my deployed app was not displaying correctly in Chrome, due to some incorrectly placed tags. To help identify my mistakes, [Validator.nu](https://html5.validator.nu/) was very helpful - it is an online HTML validator which checks the source code for your site, and warns you of stray or incorrectly placed tags.

After the app was debugged and successfully deployed, my first foray into customising a Bootstrap theme was complete. You can see the source code for my project [here](https://github.com/jenniferanneaus/grayscale_theme_app) (the deployed version on Heroku is no longer active).
