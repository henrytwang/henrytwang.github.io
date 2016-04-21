---
layout: post
title: Highcharts in 60 seconds
---

At SurveyMonkey, we use Highcharts to show the graphs on our Instant Results page. Let’s see if we can figure out how to use this <a href="http://api.highcharts.com/highcharts" target="_blank">API</a>.

Want to resize a chart directly on the page? You can. In your browser console, try this:

  Highcharts

This is the namespace in which we’ll find all the Highcharts variables we need. Next:

  Highcharts.charts

This returns an array of chart objects on the page. Next:

  Highcharts.charts[0]

I know I want the chart at the top of the page, so I selected the first one. Now, that we have it in our grasp, let’s resize it:

  Highcharts.charts[0].setSize(600, 300, doAnimation = true);

I wanted to made the chart bigger, so I set the width at 600px, the height at 300px, and doAnimation to true in order to see the parts slide around in an interesting way.

---

If you want to grab the data from a chart, here’s what you do:

  Highcharts.charts[0].series

Each chart has its own series of data, which is contained in this array. To get the first chart:

  Highcharts.charts[0].series[0].data

There’s your start. Now you know it’s not necessary to dig into the SVG object rendered on the DOM. Everything you need is within the Highcharts namespace.