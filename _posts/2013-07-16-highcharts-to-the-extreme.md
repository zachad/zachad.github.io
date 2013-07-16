---
layout: default
title: "Highcharts to the Extreme!"
---

I'm a big fan of [Highcharts](http://www.highcharts.com) for getting high quality, 
powerful charts into whatever app or project I'm working on.  They are incredibly 
configurable, offering you options for pretty much every aspect of what you're looking for.

I recently was building a graph where the series might not actually have any data, or rather
the y-value of the data was 0 across the whole width of the chart.

Highcharts 3.0 handles this a little differently than it did back in 2.x.  Instead of leaving
the max and min of the yAxis where they had been previously, it tries to display the data a 
a little better, and centers the points that I have.  Thus, you end up with a straight line in 
the middle of the yAxis representing zero.

What I wanted was to keep my yAxis minimum at zero, and just display nothing (or a solid line 
across the bottom).

#### To StackOverflow!

Turns out I'm not the only one to ask this: [StackOverflow][1] has a few posts on the topic.

The fantastic solution there was to look at the `yAxis.dataMax` field and if it was zero,
then set the axis's extremes to the range I wanted to show.  The upper-bound didn't matter to me
as long as the bottom was zero.

{% highlight javascript %}
if(chart.yAxis[0].dataMax === 0) {
   chart.yAxis[0].setExtremes(0, 5);
}
{% endhighlight %}

Check that object after rendering your chart and you're good to go.

#### Multiple Series

What about in the instance where you have multiple series of points being displayed 
on the same graph?  Highcharts has the awesome feature that lets you toggle the visibility
on and off for different series by clicking their name in the legend.  This doesn't know about 
my `setExtremes()` fix since I only called it one time, after the graph is rendered.

Highcharts events to the rescue.

{% highlight ruby %}
plotOptions: {
  series: {
    events: {
      show: function() {
        fix_zero_axis(this);
      },
      hide: function() {
        fix_zero_axis(this);
      }
    }
  }
}

// helper function 
function fix_zero_axis(chart) {
  var xtremes = this.yAxis.getExtremes();
  if(xtremes.dataMax === 0) {
    this.yAxis.setExtremes(0,10);
  } else {
    this.yAxis.setExtremes(xtremes.dataMin, xtremes.dataMax);
  }
}
{% endhighlight %}

By registering an event for whenever a series is shown or hidden, we can check the `getExtremes()`
values and determine if we need to reset the range the axis is showing.

I wouldn't be surprised if this is considered a bug by Highcharts, but 
to fix it now, this seemed to be the quickest approach.


[1]: http://stackoverflow.com/a/17367605
