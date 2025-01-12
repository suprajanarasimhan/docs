---
title: Optimize Query Performance
keywords: query language
tags: [query language, performance]
sidebar: doc_sidebar
permalink: query_language_performance.html
summary: Tricks for improving Wavefront Query Language performance.
---

The Wavefront Query Language lets you retrieve and display the data that has been ingested into Tanzu Observability by Wavefront and create alerts that use this data.

<table style="width: 100%;">
<tbody>
<tr>
<td width="15%">
<strong>Time series data</strong></td>
<td width="85%" markdown="span">The query language is particularly well suited to manipulating time series data because it accommodates the periodicity, potential irregularity, and streaming nature of that type of data. </td>
</tr>
<tr>
<td width="15%">
<strong>Histograms</strong></td>
<td width="85%" markdown="span">The query language includes functions for [manipulating histograms](query_language_reference.html#histogram-functions).</td>
</tr>
<tr>
<td width="15%">
<strong>Traces and spans</strong></td>
<td width="85%" markdown="span">Use the [tracing UI](tracing_traces_browser.html) to query traces and spans.</td>
</tr>
</tbody>
</table>

## Video: Optimize Dashboard Performance

Watch this video to learn how to optimize dashboard and query performance.

<p>
<iframe id="kmsembed-1_ynnxe6tn" width="608" height="402" src="https://vmwaretv.vmware.com/embed/secure/iframe/entryId/1_ynnxe6tn/uiConfId/49694343/pbc/252649793/st/0" class="kmsembed" allowfullscreen webkitallowfullscreen mozAllowFullScreen allow="autoplay *; fullscreen *; encrypted-media *" referrerPolicy="no-referrer-when-downgrade" frameborder="0"></iframe>
</p>

## Use Performance Statistics

You can see performance statistics for the whole chart and for each query of the chart. For the performance statistics, we measure the following characteristics:
- **Cardinality**: Number of unique time series. A unique time series has unique metric name, source name and point tags (key and value). For example, you might receive `networks_bytes_received` from multiple sources and with multiple point tags (e.g. `availability_zone`). You can lower cardinality for each query (and the chart) by filtering, for example, limiting the query to certain sources, certain availability zones, etc.
- **Points Scanned**: Number of data points that were queried to show the chart on the screen. You can affect this number by including the time window in the query or by changing the time window interactively.
- **Duration**: Time between query start and return of result.

### View Chart Statistics

<table style="width: 100%;">
<tbody>
<tr>
<td width="40%">
To see the overall performance statistics for a chart:
<ol>
<li>Navigate to the dashboard containing the chart and, optionally, open the chart in edit mode.</li>
<li>Click the ellipsis icon for the chart and select <strong>Show Chart Stats</strong>.</li></ol>
<p>The chart stats window opens. You can move the chart stats window within the chart borders.</p></td>
<td width="60%"><img src="/images/chart_stats.png" alt="screenshot shows the ellipsis drop-down menu and the chart stats"></td>
</tr>
</tbody>
</table>

### View Query Statistics

<table style="width: 100%;">
<tbody>
<tr>
<td width="40%">
To see the performance statistics for a particular query of a chart or alert:
<ol>
<li>Open the chart or alert in edit mode.</li>
<li>Click the lightbulb icon for the query.
<p>The query performance panel opens on the right. On the top of the panel, you can see the stats for the query.</p>
<p>For a chart query, you can see how much the query contributes to the <a href ="#view-chart-statistics">overall chart performance</a>.</p></li>
<li>For a chart query, hover over each characteristic in the stats and see the query contributions as percentages of the overall chart stats. </li>
<li>Check for <a href ="#use-performance-improvement-suggestions">suggestions</a> to improve the query performance.</li></ol>
</td>
<td width="60%"><img src="/images/query_stats_annotated.png" alt="screenshot highlighting the lightbulb icon and the query stats on the right"></td>
</tr>
</tbody>
</table>

## Use Performance Improvement Suggestions

If the query uses certain functions in ways that often cause performance degradation, Tanzu Observability shows actionable suggestions for improving the query performance. The suggestions also include links to documentation and videos for details.

<table style="width: 100%;">
<tbody>
<tr>
<td width="40%">
A dot symbol on the lightbulb icon for a query indicates that Tanzu Observability has suggestions for improving the query performance.</td>
<td width="60%"><img src="/images/lightbulb_w_dot.png" alt="screenshot highlighting the lightbulb icon with the dot"></td>
</tr>
<tr>
<td width="40%">
To see and, optionally, apply the performance improvement suggestions for a query:
<ol><li>Click the lightbulb icon with the dot symbol.
<p>The query performance panel opens on the right. You can see expandable suggestions for the functions that you can improve.</p></li>
<li>Examine the suggestions:
<ul><li>Expand a suggestion and see the function in the query to which it applies. The corresponding function is highlighted.</li>
<li>Click a function in the Query Builder or double-click a function in the Query Editor, and see the suggestion that applies to that function, if any. The corresponding suggestion expands.</li></ul></li>
<li>To apply a suggestion, click a value or function link in the suggestion.
<p>The query is updated and the suggestion disappears from the panel on the right. In the Query Builder a tooltip confirms that the suggestion is successfully applied to the query.</p></li></ol></td>
<td width="60%"><img src="/images/query_suggestions.png" alt="screenshot highlighting the lightbulb icon and suggestions on the right"></td>
</tr>
</tbody>
</table>

## Use Filters to Look at the Right Data

For best query language performance, it's important to look at just the right amount of data.

**Use filtering functions in queries** narrow down the query space. For example, if a query filters metrics by source or point tag, the query returns faster because the query engine knows which metrics to fetch. Here are some tips:


* **Filter by source**: By default, if you query a metric such as `cpu.loadavg.1m`, the query engine retrieves that metric for any source (host, container, etc.). To significantly improve query performance, query only for sources that you need to know about.

   Example:

   - **Faster**: `ts(~cpu.loadavg.1m, source="db-1")` narrows down the query to a specific time series.
   - **Slower**: `ts(~cpu.loadavg.1m)` returns all time series and is slower.


* **Filter by point tag**: If your data comes in with point tags, such as the availability zone, environment, or other attribute, you can change your query to filter by point tag.

   Example:

   - `ts(~cpu.loadavg.1m AND source=app-* AND env="production")` returns only metrics with sources that start with `app-` and that also have the value `production` for the `env` point tag.

* **Avoid NOT in filters**: With `AND NOT`, the query engine has to search through everything matching the metric, and then filter.

  Example:
  - **Faster**: `ts(~cpu.loadavg.1m, source="db-1" and env="prod")` narrows down the query to a specific time series.
  - **Slower**: `ts(~cpu.loadavg.1m AND NOT env="dev")` is more expensive. With `AND NOT` the query engine has to search through all instances of `~cpu.loadavg.1m` and extract instances that do not have the `env-"dev"` point tag.

* **Filter in the base query**: If possible filter in the base query instead of using advanced filtering functions.

  Example:
 
  - **Faster**: `sum(ts(user.metric, source=app-1)))`
  - **Slower**: `retainSeries(sum(ts(user.metric)), source=app-1))`

## Be Smart About Aggregation

Aggregation functions like sum() or avg() let you combine different time series, for example, by showing the sum or average of a set of time series. For optimal accuracy, the query engine uses interpolation. After interpolation, each time series has a value at each point in time which improves accuracy during aggregation, but affects performance. See [Aggregating Time Series](query_language_aggregate_functions.html) for background and a video.

You have these options to eliminate the overhead from interpolation:

### Use align() with Aggregation Functions

The align() function changes [how bucketing happens](query_language_align_function.html).

Example:

* **More precise**: `avg(ts(~sample.network.bytes.sent))` returns the average over all time series, inserting points so there's a value for each time series at any time there's a value for one time series.
* **Faster**: `align(1m, mean, ts("my.metric"))` returns the average over all time series, and uses the values at each 1 minute point in time.

In certain cases, the query engine [performs prealignment](query_language_align_function.html#the-pre-align-warning--when-the-query-engine-applies-align).

### Use Raw Aggregation Functions

Instead of using align(), you can avoid the overhead of interpolation with a raw aggregation function. [Aggregating Time Series](query_language_aggregate_functions.html) has details and a video.
* Standard aggregation functions (e.g. sum(), avg(), or max()) first interpolate the points of the underlying set of series, and then apply the aggregation function to the interpolated series. These functions aggregate multiple series down, usually to a single series.
* Raw aggregation functions (e.g. rawsum(), rawavg()) do not interpolate the underlying series before aggregation.

Example:

- **More precise**: `sum(ts(~sample.cpu.loadavg.1m, source=app-1*))` performs interpolation first, and then computes the sum.
- **Faster**: `rawsum(ts(~sample.cpu.loadavg.1m, source=app-1*))` does not perform interpolation and computes the sum from the raw data.


## Specify a Time Window with Missing Data Functions

Missing data functions such as last() support an optional `timeWindow` parameter. The default() function also supports a `delayTime` parameter. If you don’t specify those time parameters, the query engine applies the default value for every second and for gaps up to 28 days. This impacts performance of the query and the dashboard.

- **Faster**: `default([<timeWindow>,] [<delayTime>,] <defaultValue>, <tsExpression>)`
- **Slower**: `default(0, <tsExpression>)`

The time window is a measure of time expressed as an integer number of units. The default unit is minutes. You can specify:
<ul>
<li>Seconds, minutes, hours, days, or weeks (1s, 1m, 1h, 1d, 1w). For example, <strong>3h</strong> specifies 3 hours.</li>
<li>Time relative to the window length of the chart you are currently looking at (1vw).
If you are looking at a 30-minute window, <strong>1vw</strong> is one view-window length, and therefore equivalent to <strong>30m</strong>. </li>
<li>Time relative to the bucket size of the chart (1bw). The query engine calculates bucket size based on the view window length and screen resolution. You can see bucket size at the bottom left of each chart.</li>
</ul>

## Use Wildcard Characters with Care

WQL supports the asterisk (*) as a wildcard character.  [Wildcards](query_language_reference.html#partial-regex-wildcards-aliases-and-variables) in queries can result in many time series on a chart, which can be confusing and affect performance. If using a wildcard character make sense for your use case, use delimiters, and don't use a wildcard at the beginning of a query.

- **Faster**: `ts(‘abc.*.xyz’)` -- Using delimiters around wildcards.
- **Slower**: `ts(“abc*xyz”)` -- Not using a period as a delimiter.
- **Slower**: `ts("*abc.xyz")` -- Wildcard character at the beginning of a query.

## Learn More!

* [Optimizing the Data Shape to Improve Performance](optimize_data_shape.html)
* [Troubleshooting Missing Data](missing_data_troubleshooting.html)
* [Find Actionable Usage Information](wavefront_usage_info.html)
