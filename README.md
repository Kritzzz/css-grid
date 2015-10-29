# SCSS Grid

This SCSS grid is a a simple and flexible grid system. The number of columns and their margins is completely up to you. Of course, you can just use the default 12-column layout.

##### Wait what, another CSS grid?!

With millions of different CSS grids out there, it seems unnecessary and redundant to create another one. However, I was unsatisfied with most of the grids I tested and decided it was time to create a grid that fits my needs.

I wanted a responsive & adaptive grid with the ability to freely change the number of columns and the column gutter as well as to nest the columns without adding extra padding or margin to the element.

The implementation is kept as simple and basic as possible, the compiled and compressed .css file for a 12-column layout is just **447 Bytes** in size. Nice.

## How does it work?

TL;DR: *math!*

First of all, let's build a formula to determine the total width of our content.

```
totalWidth = (|columns| * singleColumnWidth) + (columMargin * (|columns| - 1)))
```

Where `|columns|` is the number of columns. 

So the total width of our content is the sum of our column-widths plus the margins in between all these columns. Assume you have 12 columns, that leaves you with 11 margins between these columns. So far so good.

But wait. How do we know how wide a single column is exactly, if we're aiming for 100% content-width?

Based on the number of columns and the column-gutter (or margin) specified in *_grid_config.scss*, we calculate the width of a single column like this: 

```
singleColumnWidth = (totalWidth - (columnMargin * (|columns| - 1))) / |columns|;
```

Many grid systems just go with `totalWidth / |columns|` to determine the size of a single column. However, that limits us to using a padding to seperate the columns, which in turn can lead to unwanted effects.

Instead, we take the totalWidth (in this case: 100%) and substract the margins corresponding to the number of columns and then divide by the amount of columns.

This leaves us with a width for a single column, which we can then use to calculate the widths of all the other columns:

```
@function calc-column-width($i) {
    @return (($single-column-width * $i) + ($column-margin * ($i - 1)));
}
```

Here, we simply multiply the width of a single column with the desired column-span and then account for the margins.

Finally, we just use the `calc-column-width` method in a simple SASS for-loop to generate the output of the respective class-names:

```
@for $i from 1 through $column-count {
	.col-#{$i} {
		width: calc-column-width($i);
	}
}
```

Thats it. *Science!*

## Using the grid

If you want to change the margin and/or number of columns, edit the _grid_config.scss file. Do not attempt to change the compiled .css file in any way as it would probably break your grid and make you very unhappy.

Always wrap your *.col* classes in a *.row*. 
Do not add more columns to a row than your *column-count*.


	<div class="row">
		<div class="col-4">.col-4</div>
		<div class="col-4">.col-4</div>
		<div class="col-4">.col-4</div>
	</div>


The grid supports pixel-based widths too, if you have to use them. Simply change the margin **and** the totalWidth to a pixel value. **Caution:** Nesting columns might not work in some cases if your columns are pixel based. It's best to stick to percentages!

#### No media queries?

I believe breakpoints should be defined by your content, not by the most popular device or mobile screen-size. That's why no media queries are included to change the layout of the grid. Naturally you are free to create your own media queries to suit your needs.

Further reading: [7 habits of hightly effective media-queries](http://bradfrost.com/blog/post/7-habits-of-highly-effective-media-queries/)

## Browser support & performance

This SCSS grid was tested in Chrome, Safari, Firefox, Opera, IE (+9), MS Edge as well as on the iOS Safari and Android Webbrowser. It's pretty good.

If you worry about the performance of the [class=*'col-'] attribute selector, read [this](http://benfrain.com/css-performance-revisited-selectors-bloat-expensive-styles/)

## License and Credits

Thanks to [Ryan](http://ryanmorr.com/) for the math-help.

```
Copyright (c) 2015 Christoph Müller

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
