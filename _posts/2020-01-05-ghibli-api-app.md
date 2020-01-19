---
layout: post
current: post
cover:  assets/images/covers/ghibli.jpg
navigation: True
title: Studio Ghibli API App
date: 2020-01-05 08:00:00
tags: [react, css]
class: post-template
subclass: 'post tag-react tag-css'
---

I have been learning React and CSS over the last few months, so recently set myself a small project. I had three main goals in mind: to use JavaScript to connect with an API, to learn more about React promises, and to create a responsive app that looks good on both wide and narrow screens.

We won't cover the fundamentals of React or CSS in this post, but I've found [Scrimba](https://scrimba.com/) a useful tool in getting started. In particular, the [free React course](https://scrimba.com/g/glearnreact) by Bob Ziroll contains a video about [fetching data from an API](https://scrimba.com/p/p7P5Hd/c79Jask), which inspired this project.

To begin, we look at the basic call to fetch data from an API. As someone who enjoys anime, I was excited to learn that a [Studio Ghibli API](https://ghibliapi.herokuapp.com/) exists. This catalogs various aspects of the Ghibli universe, including films and characters. To retrieve data from the API we can use ```fetch()```, passing the url as a parameter. So, if we want the characters, we would use the following:
```javascript
fetch("https://ghibliapi.herokuapp.com/people")
```

An important thing to note is that retrieving data takes time. This presents us with two problems. First, we want to let the user know the app is loading. Second, we cannot make the call to store or process the data until after the data has been retrieved. This is where promises come in.

Promises in JavaScript are objects which represent the future completion of an operation. If the event has not yet started or is still in progress, the promise is **pending**. If the event has already finished, the promise is **settled** and is in one of two states: **fulfilled** or **rejected**. 

If a promise is **fulfilled**, this means the operation completed successfully and the promise will return the resolved value. A **rejected** state means that the operation failed, and the promise will return an error. For a more in-depth explanation of promises, there are plenty of resources online such as [this simple guide](https://codeburst.io/a-simple-guide-to-es6-promises-d71bacd2e13a) and the [official MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise). For now, we will only discuss the ```then()``` method of promises.

Each promise has a ```then()``` function that can take two arguments. These arguments are callback functions for the fulfilment and rejection cases respectively. If only one argument is provided, it will be called when the promise is fulfilled. The ```then()``` method returns another promise, which means ```then()``` methods can be chained.

We now bring our attention back to the ```fetch()``` method. This returns a promise, which is settled once the response is available. If fulfilled, ```fetch()``` will return the response object. Note that a response object will be returned even if HTTP errors have occurred (e.g. 404 Not Found or 401 Unauthorised), so it must be checked to ensure the response is as expected. If ```fetch()``` encounters a network error, it will reject.

For the Studio Ghibli API App, the method for retrieving data is written below. The variable ```category``` is a string corresponding to a valid category of information, i.e. ```"people"``` or ```"films"```.


```javascript
getData(category) {
    this.setState({loading: true})
    let cat = category

    let infComponents = []
    let url = this.baseURL + cat
    fetch(url)
    .then((response) => {
      /* If the response is not successful, throw an error */
      if (!response.ok) 
      {
        throw new Error('Network response was not ok')
      }
      /* Otherwise, parse the data */
      return response.json()
    })
    .then(result => {
      /* If data was parsed, save it in the current state */
      this.setState({
        data: result,
      })
    })
    .then( () => {
      /* Create components from the data */
      switch(cat) {
        case "people":
          infComponents = this.createPeopleComponents()
          break;
        case "films":
          infComponents = this.createFilmComponents()
          break;
        default:
          infComponents = [<div>Sorry, an error occurred.</div>]
          break;
      }
      this.setState({
        infoComponents: infComponents,
        loading: false
      })
    })
    .catch( err => {
      /* If any errors were thrown, display a message to the user */
      console.log(err)
      this.displayError()
    })
  }
```

With the data safely retrieved and stored, we will now focus on the appearance of the page. I am by no means a designer, so the actual design is nothing mindblowing. For me the focus was to create a layout that is responsive. That is, the page should render well on a range of window sizes across different devices. We want to avoid things like overflowing or overlapping elements, or

In creating a responsive design, CSS flexbox is an essential tool. Along with media queries, flexboxes are what makes my design change from this:

![A header at full screen, with nav on the right and a title on the left](assets/images/posts/ghibli-header-full.jpg)

to this:

![A header at medium width screen, with the title changed to wrap over multiple lines](assets/images/posts/ghibli-header-med.jpg)

to this:

![A header at narrow screen, with nav displaying below the title](assets/images/posts/ghibli-header-narrow.jpg)

as the app's width changes. Otherwise, the website may end up breaking on smaller screens or having a lot of empty space on larger screens. Below is an image of what my widescreen header code would look like on a narrow screen, if it were not changed using media queries.

![A header at narrow screen, with the title all smooshed in and overlapping](assets/images/posts/ghibli-header-broken.jpg)

So what is flexbox? By setting ```display: flex;``` on an element, it becomes what is called a **flex container**. The children of a flex container are known as **flex items**. For example, if the header below is given the property ```display: flex;``` it becomes a flex container, so ```title``` and ```nav-list``` become flex items.

```html
<header>
  <div className="title">
    <h1><em>Studio Ghibli</em> API App</h1>
    <p><em>株式会社スタジオジブリ</em> エーピーアイ アプリ</p>
  </div>
  <ul className="nav-list">
    <li><button className="btn" onClick={() => this.homePage()}>Home</button></li>
    <li><button className="btn" onClick={() => this.getData("people")}>Characters</button></li>
    <li><button className="btn" onClick={() => this.getData("films")}>Films</button></li>
  </ul>
</header>
```

Note that children refers to *direct* descendants only, so the heading, paragraph and list items are not flex items.

On a flex container and its items, we can easily set properties such as:

+ Whether the flex items should be laid out in rows or columns.
+ How the flex items should be aligned, both vertically and horizontally.
+ Whether the flex items should occupy one row/column or be allowed to wrap as needed.
+ At what rate the flex items should grow and/or shrink as the size of the container changes.

These properties work either along the **main axis** (horizontal or by default) or the **cross axis** (vertical by default).

There is so much you can do with flexbox, and it is used to style most parts of the Studio Ghibli API app. However, for the purposes of this explanation we will focus on the header we saw before, and will discuss only as much flexbox as is needed to make it responsive. 

Below, we have a picture of the default flex properties. The flex items are outlined in red, and the flex container in blue.

![Two flex items on the left of a flex container](assets/images/posts/ghibli-flex-1.jpg)

By default, the items in the flex container are laid out in one row from left to right, with no space between them except the margins on each item. Margin boxes on all items will have the same height, but the items will not grow wider to occupy empty space within the flex container. If we set a ```margin-right``` on ```title``` and a ```margin-left``` on ```nav-list```, the margins would add together; this is unlike in regular CSS elements where the distance between the elements would become the larger of the two.

For wide screens, I gave the header the property ```justify-content: space-between;``` which works along the main axis. This will add space between each child to fill the container, but will not put space before the first child or after the last. In our case, it will push ```title``` all the way to the left and ```nav-list``` all the way to the right, respecting existing margins on the elements:

![One flex item on the left of a container, and one on the right](assets/images/posts/ghibli-flex-2.jpg)

To allow some space between the title and the edge of the viewport, I gave ```title``` a ```margin-left``` of ```3.5rem```, shown below.

![One flex item on the left of a container but with a margin, and one on the right](assets/images/posts/ghibli-flex-3.jpg)

One of the ways we will make the page responsive is using media queries. These are used to apply different styles according to variables such as the orientation, device size, viewport size or resolution of the screen. Below is an exmple of a media query that will aplly styles only when the window is between 720px and 900px wide.

```css
@media (max-width: 900px) and (min-width: 720px) { 

  /* CSS goes here */

}
```

Note that the above example combines two media queries, but you can use as few or as many as you like. For more information about media queries, visit the page on [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries).

For the header in the Studio Ghibli API App, we saw there were three main styles according to the window width. For medium widths, I retained the flex settings from the wide screen but made the title narrower and taller, using the following:

```css
@media (max-width: 900px) {

  .title {
    width: 18em;
    line-height: 1;
  }

}
```

At narrower screens, the layout changes more dramatically. The nav list can no longer fit comfortably beside the title, so we want these elements to display on top of one another rather than side by side. This is where flexbox makes life easier - all we have to do is change the ```flex-direction``` from ```row``` to ```column```, and our items will be arranged in one column. Recalling our flex axes, the main axis is now vertical and the cross axis is now horizontal. The result of changing the flex direction is below.

![One flex item above the other in the container, but not centred horizontally](assets/images/posts/ghibli-flex-4.jpg)

Although this is an improvement, we also want the items to be centred. To achieve this we use the ```align-items``` property, which works along the cross axis. After setting its value to ```center```, the result is below.

![Flex items almost centred horizontally, but not quite](assets/images/posts/ghibli-flex-5.jpg)

The problem we have here is that the title still does not appear to be centred. This is because of the ```margin-left``` we added to make the header look good at larger viewport widths. The final step in centering our elements is to adjust the margins so that our items are centred. The media query, and a screenshot of the resulting appearance, can be seen below.

```css
@media (max-width: 760px) {

  header {
    flex-direction: column;
    align-items: center;
  }

  .title {
    margin: 0;
  }

  .link-nav {
    margin: 0 1em;
  }

}
```
![Flex items centred horizontally](assets/images/posts/ghibli-flex-6.jpg)

Now, we have one last thing to add to make sure the page displays as we intend it to. To prevent problems from rendering pages that are not optimised for small screens, phones and tablets will automatically render pages in a virtual window and then scale down the result to fit the device's viewport. This means, for example, that a phone might have an actual screen width of 600px but render the 960px version of a website and scale it down to fit the phone's screen. Since we have already designed our page to accomodate different viewport sizes, we want to ensure that the page gets rendered according to the actual width of the device being used. To do this, we place the following HTML code in our header:

```css
<meta name="viewport" content="width=device-width, initial-scale=1">
```

With that done, we now have a functioning and responsive Studio Ghibli API App! You can view the [source code here](https://github.com/jenniferanneaus/ghibli_api_app) or visit the [deployed verion on Heroku](http://ghibli-api-app.herokuapp.com/).