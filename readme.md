# D3.js

[screencast](https://vimeo.com/133149838)

## Learning Objectives

- Manipulate the DOM using D3
- Bind data to DOM elements
- Scale data to fit the viewport
- Use D3 helper methods like `min` and `max`

http://d3js.org/
[Documentation](https://github.com/d3/d3/blob/master/API.md)

## Framing (5 minutes / 0:05)

### What Is D3?

D3 stands for Data-Driven Documents. The word "document" in D3 refers to the DOM. Javascript can manipulate the DOM, creating and deleting pieces of it. Building data visualizations, however, with tools that are suited for GUI programming is difficult and verbose. Enter D3.

### What It's Not

D3 doesn't come with any prebuilt visualizations. D3 aims to be low-level and flexible. There are many excellent charting libraries out there, several of them built with D3. If your goal is to render a few standard charts as quickly as possible, you may want to consider using a library instead of writing D3 code by hand. Check out [highcartJS](http://www.highcharts.com/), [chartJS](http://www.chartjs.org/) or [dygraphs](http://dygraphs.com/).  

### The Possibilities Are Endless

* http://www.jasondavies.com/animated-bezier/
* http://bl.ocks.org/mbostock/1136236
* http://animateddata.co.uk/lab/d3-tree/
* http://d3tetris.herokuapp.com/

> D3 allows you to bind arbitrary data to  the DOM and then apply data-driven transformations to the document. For example, you can use D3 to generate an HTML table from an array of numbers. Or, use the same data to create an interactive SVG bar chart with smooth transitions.

## You Do: [D3 Experiments in the Console](http://ga-wdi-exercises.github.io/d3-console/) (5 minutes / 0:10)

Before we go over D3 code as a class, you're going to dive in and play with some yourself. Click the link in the header and follow the instructions on the resulting page. You will find that D3 shares some similarities with another library you have encountered...

## Quick Demo (5 minutes / 0:15)

<!-- AM: Why are we using hsl in this example? Did it come from some other source? -->

It can help to think of D3 as jQuery for data visualization. Consider this example...

```html
<body>
  <p>Lorem ipsum dolor sit amet.</p>
  <p>Cupiditate fugiat, quas quo sit.</p>
  <p>Vero distinctio, odit ut ab.</p>
  <p>Blanditiis odio ipsa cumque iure.</p>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min.js" charset="utf-8"></script>
  <script>
    d3.selectAll("p").style("color", function() {
      return "hsl(" + Math.random() * 360 + ", 100%, 50%)"
    })
    d3.selectAll("p").data([13, 42, 7, 33])
      .style("font-size", function(d) {
        return d + "px";
      });
  </script>
</body>
```

> **hsl** stands for "hue saturation lightness" and is an alternate way of defining a color in CSS. More info [here](http://www.w3schools.com/colors/colors_hsl.asp).

What's going on in the above example?
* Select all `<p>`
* Assign each `<p>` a random text color value
* Assign each `<p>` a font-size from some existing data set

<details>

  <summary><strong>Hey, we can write this in jQuery! What would that look like?</strong></summary>

  ```js
  var data = [13, 42, 7, 33]
  data.forEach(function(num, index){
    var paragraph = $("p").eq(index)
    paragraph.css("color", "hsl(" + Math.random() * 360 + ",100%,50%)")
    paragraph.css("font-size", num + "px")
  })
  ```

</details>

You could make the argument for one over the other. D3, however, supports way more functionality than jQuery does when it comes to data visualization. Check out the [docs](https://github.com/d3/d3/blob/master/API.md).

## Installation

All you need to do is link to a CDN and you're good to go!

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/4.2.2/d3.js" charset="utf-8"></script>
```

## We Do: A Bar Chart (10 minutes)

Here's how we would go about creating a bar chart using just HTML and CSS. The data is hard-coded...

```html
<!doctype html>
<html>
  <head>
    <title>&hearts; D3</title>
    <style>
      .chart div {
      	font: 10px sans-serif;
      	background-color: steelblue;
      	text-align: right;
      	padding: 3px;
      	margin: 1px;
      	color: white;
      }
    </style>
  </head>
  <body>
    <div class="chart">
      <div style="width: 40px;">4</div>   <!-- width = data * 10 -->
      <div style="width: 80px;">8</div>   <!-- Each div contains text representing the data value -->
      <div style="width: 150px;">15</div>
      <div style="width: 160px;">16</div>
      <div style="width: 230px;">23</div>
      <div style="width: 420px;">42</div>
    </div>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min.js" charset="utf-8"></script>
    <script>
      // Our code will go here...
    </script>
  </body>
</html>
```

### Refactor with D3

#### Remove the hardcoded HTML

There should be nothing inside of `<div class="chart">`...

```html
<body>
  <div class="chart"></div>
</body>
```

#### Define the data

Our goal here is to generate a bar chart based on some arbitrary numbers. Let's define an array at the top of `<script>`...

```js
let data = [4, 10, 15, 16, 23, 42]
```


#### Select the chart

Instead of jQuery's selector syntax, we will use D3's `.select` method. In order to access this and other D3 methods, we need to call it on a `d3` object, which was made global when we included the CDN...

```js
let data = [4, 10, 15, 16, 23, 42]

d3.select(".chart")
```

Like our original HTML/CSS bar chart, we will be using `<div>`s to create the bars themselves. Even though the chart is initialized as empty, we need to tell D3 to select these bar `<div>`s as they are created. We'll do this using `.selectAll`...

```js
let data = [4, 10, 15, 16, 23, 42]

d3.select(".chart")
  .selectAll("div")   // Select all divs that currently and have yet to be placed in side of the chart div
```

#### Associate data with chart

We want to be able to incorporate our dataset into these D3 methods. Rather than have to reference the `data` variable at the top of our script, we can bring it into our D3 code for quick reference later using the `.data` method...

```js
let data = [4, 10, 15, 16, 23, 42]

d3.select(".chart")
  .selectAll("div")
  .data(data)
```

#### Enter `.enter`

In order for us to be able to add new "data-bound" elements to our chart, we need to use D3's `.enter` method. You'll need to use `.enter` when you have a piece(s) of data but it's not yet currently represented in the DOM. [[Documentation](https://github.com/d3/d3-selection/blob/master/README.md#selection_enter)]

```js
let data = [4, 10, 15, 16, 23, 42]

d3.select(".chart")
  .selectAll("div")
  .data(data)
  .enter()
```

> **tl;dr**: this is where we're **binding** the data to the DOM.

<!-- AM: Does order matter here? Does enter have to come after the selects and data? -->

#### Populate chart

Now we need to tell D3 how we want to represent our data in the DOM. In this case we want to generate some HTML that looks similar to the code we had at the start. Here are the steps we need to take...
* Generate a div
* Give each div a width of `datum * 10`
* Give each div a text value of `datum`

```js
var data = [4, 10, 15, 16, 23, 42];
d3.select(".chart")
  .selectAll("div")
  .data(data)
  .enter()
  .append("div")
  .style("width", function(d){
    return d * 10 + "px"
  })
  .text(function(d){
    return d;
  })
```

> The **`.style`** method takes two arguments: (1) a CSS property and (2) a callback. The callback takes a single argument that represents each item in our dataset. The return value of the callback will be whatever you want to set the CSS property of the div in question to. So in this case, `return` should be followed by whatever we want each div's width to be.
>
> The **`.text`** method takes on argument: a callback. Like the `.style` callback, it takes one argument representing each item in our dataset. The method should return whatever you want each div's text content to be.

<!-- AM: Add a You Do here where they do something similar with a different type of chart? -->

## We Do: Improving the Bar Chart (10 minutes)

Our bar chart works, but the bar widths are hard-coded to pixel values. Instead, it'd be nice if those widths were defined as percentages that correspond to some data domain.

<!-- AM: Elaborate on what we mean by "data domain" here. -->

### SVG

* "scalable vector graphics"
* render nicely no matter what the screen, resolution, zoom level is
  * confirm: defined by the path the image takes; programmaticaly manipulated shapes; key word: scalable
* defined in markup
* xml-based (i.e., can be self-closing)
* some pre-defined tags besides svg: rect, circle, ellipse, line, text, path
  * `<rect x="0" y="0" width="500" height="50"/>` where x and y are upper-left coordinates
  * `<circle cx="250" cy="25" r="25"/>` where cx and cy are center coordinates
  * `<line x1="0" y1="0" x2="500" y2="50" stroke="black"/>`
* pixel-based coordinates
* styling
  * can apply through attributes or stylesheet
    * need to use same properties when in stylesheet
  * fill - background color, any color value
  * stroke - border, color
  * stroke-width - border-width, numerical value (probs px)
  * opacity
* two-dimensional
  * no concept of z-index to layer shapes
  * layering determined by order in which elements are defined
  * make sure to create important things like graph axes first
* D3 is great for making svg elements dynamically


<!-- AM: Why are we using SVG? -->

### Scaling

That scaling comes in handy when we would like our data to dynamically fit into the domain constraints of our graph.

For this example, we are going to display our data according to a **linear scale**.
* The domain of our graph will be all numbers between and including the min and max values in our dataset
* The range will be from 0 to 100, meaning that the lowest possible data value will be represented as 0% in our graph, the highest 100%

<!-- AM: Rephrase this ^^^ -->

```js
var linearScale = d3.scale.linear()
                          .domain([0,42])
                          .range([0,100])
```

This allows us to now use `linearScale` as a function. Whatever value we pass into this function will get converted to a different number based on the domain and range passed in.

In the above case, numbers passed in would be divided by 42 then multiplied by 100...

```js
linearScale(0) = 0
linearScale(42) = 100
linearScale(21) = 50
linearScale(1) = 2.38
linearScale(43) = 102.38
```

For this particular data visualization, we used linear scales max domain to be 42, because thats the highest number in our data set. In addition to that, we convert it to a range of 100 because we'll just use the return value to be a percentage width of its container.

Let's use this scale to replace the code that currently generates pixel widths with percentage widths...

```js
var linearScale = d3.scale.linear()
                          .domain([0,42])
                          .range([0,100])

var data = [4, 10, 15, 16, 23, 42];
d3.select(".chart")
  .selectAll("div")
  .data(data)
  .enter()
  .append("div")
  .style("width", function(d){
    return linearScale(d) + "%"   // We call linearScale, pass in data and append "%" to its return value
  })
  .text(function(d){
    return d;
  })
```

We may not always know what the max value of a dataset might be. In that case, you can use D3's `.max` method to figure it out for you...

```js
var max = d3.max(data)
var linearScale = d3.scale.linear()
                    .domain([0,max])
                    .range([0,100])
```

### More Styling with D3 (5 minutes)

Instead of the existing `.style()` try this...

```js
.style("width", "0px")
.transition()
  .delay(function(d, i) {return i * 1000})    // Play around with this!
  .duration(1000)
.style("width", function(d){
    return linearScale(d) + "%"
  })
.style("padding-right", "3px")              // We can move styles from the stylesheet to D3 if we want
```

We're barely scratching the surface of D3 here, if you're interested check out the API docs. There are some really cool things you can do with D3.

<!-- AM: Any other cool things we can do here? -->

## You Do: More Fun with Data Binding

(Follow Square’s tutorial on D3 and data binding)[https://square.github.io/intro-to-d3/data-binding/].

#### Bonus

Add labels to your final interactive data visualization.
