# D3.js

[screencast](https://vimeo.com/133149838)

## Learning Objectives

- CRUD the DOM with d3
- Bind data to DOM elements
- Scale data to fit the viewport
- Use d3 helper methods like `min` and `max`

http://d3js.org/
[Documentation](https://github.com/d3/d3/blob/master/API.md)

## What is it?
D3 stands for Data-Driven Documents. The word "document" in D3 refers to the DOM. Javascript can manipulate the DOM, creating and deleting pieces of it. However! Building data visualizations with tools that are suited for GUI programming is difficult and verbose. Enter D3. More tools for building data visualizations.

## What it's not
D3 doesn't come with any prebuilt visualizations. D3 aims to be low-level and flexible. There are many excellent charting libraries out there, several of them built with D3. If your goal is to render a few standard charts as quickly as possible, you may want to consider using a library instead of writing D3 code by hand. Check out [highcartJS](http://www.highcharts.com/), [chartJS](http://www.chartjs.org/), or [dygraphs](http://dygraphs.com/).  


## The Possibilities are Endless

- http://www.jasondavies.com/animated-bezier/
- http://bl.ocks.org/mbostock/1136236
- http://animateddata.co.uk/lab/d3-tree/
- http://d3tetris.herokuapp.com/

>D3 allows you to bind arbitrary data to a Document Object Model (DOM), and then apply data-driven transformations to the document. For example, you can use D3 to generate an HTML table from an array of numbers. Or, use the same data to create an interactive SVG bar chart with smooth transitions and interaction.

## Quick Demo

Think of d3 like, jQuery+ for data vis.

```html
<body>
  <p>Lorem ipsum dolor sit amet.</p>
  <p>Cupiditate fugiat, quas quo sit.</p>
  <p>Vero distinctio, odit ut ab.</p>
  <p>Blanditiis odio ipsa cumque iure.</p>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min.js" charset="utf-8"></script>
  <script>
    d3.selectAll("p").style("color", function() {
      return "hsl(" + Math.random() * 360 + ",100%,50%)"
    })
    d3.selectAll("p").data([13, 42, 7, 33])
      .style("font-size", function(d) {
        return d + "px";
      });
  </script>
</body>
```

What would something like this look like in jQuery?

```js
var data = [13, 42, 7, 33]
data.forEach(function(num, index){
  var paragraph = $("p").eq(index)
  paragraph.css("color", "hsl(" + Math.random() * 360 + ",100%,50%)")
  paragraph.css("font-size", num + "px")
})
```

You could make the argument for one over the other. However, d3 just supports way more functionality then jQuery does with regard to data visualization. Check out the [docs](https://github.com/d3/d3/blob/master/API.md).

## Installation - Simple CDN

Make sure you link this script file, and code away in d3!

```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/4.2.2/d3.js" charset="utf-8"></script>
```

## You do: D3 experiments in the console:

http://ga-wdi-exercises.github.io/d3-console/

This seems a lot like... some other library.

## We do: Let's make a bar chart

http://github.com/ga-wdi-exercises/d3-bar-chart/

Cool, a barchart with just HTML and CSS. Let's see how we can make this same chart with d3.

## Using D3

1. Remove the hardcoded HTML
2. Select the chart element
3. Enter new data
4. Add divs to chart element
5. Set the width for each bar
6. Set the text for each bar

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

Even though `div.chart` starts empty, we still `selectAll` the divs within it. It isn't until we call the method `enter()`. `enter()` is a sub-selection representing the data that is yet to be mapped to an element. If there are more datums in our dataset than elements on the page, the “enter” sub-selection represents the yet-to-be-added elements. Then we actually append the divs we need that don't have a matching `div` element. In our case, all of them.


### Improving the bar Chart

>SVG (Scalable Vector Graphics) is an XML format used for drawing. You can think of SVG in a lot of the same terms as the DOM – there are elements with parents and children and attributes, and you can respond to the same mouse/touch events.

#### Scaling

d3 scales allow us to scale our data to fit into the space allotted.

The scaling functions provided by d3 map an input domain to an output range.

```js
var linearScale = d3.scale.linear()
                    .domain([0,42])
			              .range([0,100])
```

This allows us to now use `linearScale` as a function. Whatever value we pass into this function will get converted to a different number based on the domain and range passed in.

In the above case, numbers passed in would be divided by 42 then multiplied by 100. These are some examples of this function being used.

```js
linearScale(0) = 0
linearScale(42) = 100
linearScale(21) = 50
linearScale(1) = 2.38
linearScale(43) = 102.38
```

For this particular data visualization, we used linear scales max domain to be 42, because thats the highest number in our data set. In addition to that, we convert it to a range of 100 because we'll just use the return value to be a percentage width of its container. Now are code will look something like this:

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
    return linearScale(d) + "%"
  })
  .text(function(d){
    return d;
  })
```

and if you don’t know the max of a data set, use d3's `max` method:

```js
var max = d3.max(data)
var linearScale = d3.scale.linear()
                    .domain([0,max])
			              .range([0,100])
```

## Something Cool ...
Instead of the existing `.style()` try this:

```js
.style("width", "0px")
.transition()
  .delay(function(d, i) {return i * 1000})
  .duration(1000)
  .style("width", function(d){
    return linearScale(d) + "%"
  })
  .style("padding-right", "3px")
```
> Remove the padding in the styles!

Then play around with the duration and delay.

We're barely scratching the surface of D3 here, if you're interested check out the API docs. There are some really cool things you can do with D3.

## You do: Data Binding - More practice

Follow Square’s tutorial on d3 and data binding - https://square.github.io/intro-to-d3/data-binding/

Bonus! Add labels to your final interactive data visualization.
