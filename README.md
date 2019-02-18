# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam notes with links to questions, and solutions that
exemplify what's needed / asked in the Tableau 10 QA Exam.

Large part of the content from **@igponce**, really appreicate it.

This note mainly follow the structure of Exam Prep Guid. I also added some contents from course **"Data Visual and Communication"** by **Hettie Tabor**.

Hopefully enough to pass (75%).

Exam prep guide:
https://mkt.tableau.com/files/DesktopQA_ExamGuide.pdf

Certification homepage:
https://www.tableau.com/support/certification

# Exam Prep Guid Part
----------------------------------------------------------------------------------------------------------------------------------------
# 0. Dimensions and Measures

Dimension: Qualitative values that cannot be aggregated(Blue).

Measures: Quantitative, numeric values that can be aggregated (Green).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles.htm


# 1. Data Connections (23%)
## Understand how to connect to Tableau Server

Check:
https://onlinehelp.tableau.com/current/pro/desktop/en-us/examples_tableauserver.htm

## Understand Performance Optimization

### Parallel query

**WHAT?** Tableau will perform queries in parallel **whenever possible**.That is, multiple queries in the same time.

In more details, if we're using data from different data sources, Tableau will make queries in parallel without waiting one datasource query to finish before it makes the second query.

*Before version 9.0, tableau desktop and tableau server can only build one connection to one data source, and perform the queries sequentially. So, the totally running time <= sum(single runnning times). After set the Parallel Query features, multiple queries will run at the same time to improve the performance.*

**LIMITS**
- **Parallel queries will run only when possible.**
If a query is based on a field already queried, then the parallel query will not run and will wait until the previous query will end.
The parallel queries will run in parallel only if there are not concurrent queries on the same field/table.

- **if too many queries (or resource-intensive queries) are happening in parallel, the performance may be slower than expected.**

The level of paralelism is configurable on the 'connection-configs.xml' file (located in the Tableau instalation dir)

From: http://kb.tableau.com/articles/howto/Configuring-Parallel-Queries-in-Tableau-Desktop

### Data engine vectorization

**WHAT?** A new feature in Tableau 9. Most articles don't go deep into this topic. It's a cpu calculation improve to make performance better.

```
Now tableau uses SIMD instructions (SSE4.1) to make calculations, thus better filling the
data and execution pipeline of the processor.
```

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf


### Parallel aggregation

**WHAT?** A function to run multiple aggregations at the same time.

Tableau will use multiple cores where possible. The data engine can now run aggregations in parallel, splitting the work across multiple cores. 

By default, the maximum degree of parallelism is **(number of available logical processors) / 2**. This means that query operations on data extracts can run up to N times faster in Tableau 9 (where N is the number of cores in the machine).

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf

### External query caching

**WHAT?** A function to improve the cache performance.

Tableau will cache data in memory / disk to avoid to ask ("go to the network") or parse data that won't have changed (E.g. file-based data).

With extenal query caching, Tableau doesn't need to parse a **whole** file to show some data.
This data can be presented as cache goes on.

**LIMITS** when the cache is invalidated, it will need to re-parse the file again, so it's a good idea to maka an extract unless the data is really tiny or can change unexpectedly.

### Query fusion

**WHAT?** A function to improve dashbord queries running performance.

Tableau will at all of dashboard queries and tries to eliminate any redundant queries.

It combining queries together and using the results.
E.g. If the same query is needeed in the same dashboard in several sheets, Tableau
tries to combine this multiple queryes into (just) one. 

**This works with queries with the same LOD.**


## Understand how to use Automatic & Custom Split

**WHAT?** Split a single column to multiple columns (up to 10 new columns)

**WHEN?** Sometimes your data has multiple fields encoded inside string fields. Or u just want to split (e.g. full name & first name)

**WHY?** Denormalization, you cannot touch the schema, etc.

- Automatic split: happens when tableau can guess what kind of data is inside,separators, etc...
- Custom split: Mannually set the model of split. Similar to calculated field.

Data Split cannot happen automagicaly. Tableau will only split fields it's told to do so (Mannually do it).

You can split data from the Data pane, or right-click: Transform -> Split.

**Important: Data from splits cannot be used for Joing tables; but can be used for blending.**

https://onlinehelp.tableau.com/current/pro/desktop/en-us/split.html

## Join tables from single and multiple databases

Same joins (inner, left, right, full & union) with SQL.

**2 Types:**

- Join tables from same data source: just drag tables and set key (and other configerations)

- Join tables from different data sources: build 2 difference connections, and then drag tables.

**Important**

- Null Value: if we use simple join, the Null value will be excluded (deleted). If we use **"Join null values to null values"**, Null value will be used in the join process.

- Colation problems (like having fields on UTF-8 and CP850 on different connections).

- Problems with different date formats (be careful to use the same Level of Detail for all data sources).

Seems like lot of questions are from this part. Read the following page carefully before taking the certification.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/joining_tables.html

## Data Preparation

### Blending

**WHAT?** Mannually build connections between different data sources

**WHEN?** You don't want to put all the data together (using join or union), perhaps because different details level, join causes duplicate or too much data.

- A left join-like operation between different datasources.
- Local to the view
- You can amend some data (using change alias option) on the _primary_ data source (you cannot do this on a Join operation).

Look at the datasources:

Blue check -> Primary data source (the left part of the left join)
Orange check -> Secondary source (the ritht part of the left join)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/multiple_connections.htm

**Automatic Blend**

Do not mannually set keys in relationship.

Tableau guesses the blend looking for a field in both data sources with the same name.

**Manual Blend**

Mannually set keys in relationship cause you do not like the default one.

"Data" -> "Edit Relationship".

### Metadata Grid

**WHAT?** In Data Source page, Data Preview part, change the table to a list of field name

**IN MORE DETAILS:**

- Sometimes a list of fields is more useful than a preview of the data so we’ve added a “Metadata” view of your connection.

- It displays, the field name, table, and remote field name.

- For calculations and splits, there is no table field.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/environment_datasource_page.htm#Metadata

https://www.tableau.com/about/blog/2015/1/tableau-90-auto-data-prep-stay-flow-35980#ucsrKSAg7CtOuoBL.99

### Pivot

2 ways to create Pivot tables:

- "Pivot" from grid

- Pivot using custom SQL (can only do for Excel or text files)

**More Details: **

- The Pivot option is available from the grid and metadata grid.

- All fields in the pivot must be from the same connection.

- Only one pivot is allowed per data source.

- Pivot fields can be used as the join key.

- The Pivot option cannot be used in calculated fields.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/pivot.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/customsql.htm

### Union

**WHAT?** Same as the union in SQL.

**Details:** 

- Connect to 2 data sources and then create a "New Union"

- "Wildcard" option is available to search for files to union together

- Mismatched fields in the union can be merged (or change name on all source files)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/union.htm

### Data Interpreter

**WHAT?** A function to clean the data from titles, notes, footers, empty cells etc.

Data Interpreter will automatically generate a table from the source data with titles or notes.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/data_interpreter.htm


## Understand connection options

https://onlinehelp.tableau.com/current/pro/desktop/en-us/basicconnectoverview.htm

## Understand how to connect to different file types

https://onlinehelp.tableau.com/current/pro/desktop/en-us/examples_extract.htm

## Understand data extract capabilities

**WHAT?** Data Extract (TDE) is a data snapshot that created and stored on local or server.

https://www.tableau.com/about/blog/2014/7/understanding-tableau-data-extracts-part1

**WHY USE IT?** 3 main reasons:

- Improve performance to make data loading faster

- Pre-aggregations: create aggregated extract to make the data smaller

https://www.tableau.com/about/blog/2014/7/why-use-tableau-data-extracts-32187

https://www.tableau.com/tableau-data-extracts-part3

**TDE vs. Live Connections**

-  An 'extract' connection is a connection with a static database, which is refreshed every day/hour/period. All data is copied from the datasource to Tableau Server. Refreshing the datasource will not give you the latest data - you have to refresh the extract to get these.

-  A live connection will query the underlying data in the datasource/database - refreshing the visualisation will give you the latest results. No data is copied to Tableau Server for pre-processing.

- Some of the calculated functions are limited to TDE

http://drawingwithnumbers.artisart.org/tde-or-live-when-to-use-tableau-data-extracts/

https://onlinehelp.tableau.com/current/pro/desktop/en-us/extracting_data.htm

## Understand Shadow extracts

**WHAT?** Shadow extracts is data that Tableau stores when using file (like non-legacy excel) to make loading data faster.

**WHEN?** when processing non-legacy excel files, statistical files, or text files.

**WHY?** Processing some file formats is CPU/IO intensive.

**MORE DETAILS: **

- They have .ttde extension.

- Tableau will store up to 5 files with .ttde extension.

- Altough they're named extracts, its file format is not the same as .tde (extract) files.

Location:
    - For Windows: Users\AppData\Local\Tableau\Caching\TemporaryExtracts
    - For Mac: ~Library/Caches/com.tableau.Caching/TemporaryExtracts
    - Sometimes also in the "My Tableau Repository\Shadow Extras" folder.

http://kb.tableau.com/articles/issue/low-disk-space-because-of-ttde-files

http://www.icancrack.com/index.php/52/what-is-shadow-extract-in-tableau

https://community.tableau.com/thread/201115

# 2. Organizing & Simplifying Data (10%)

## Filter data

- Filter data from views:

https://onlinehelp.tableau.com/current/pro/desktop/en-us/filtering.htm

- Filter data from data sources:

https://onlinehelp.tableau.com/current/pro/desktop/en-us/filtering_datasource.htm

## Sort data

https://onlinehelp.tableau.com/current/reader/desktop/en-us/reader_sort.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_sorting_computed_howto.htm

## Build groups

Create group to put dimensions into them (e.g. cities into US & Canada)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_groups_creating.htm


## Build hierarchies

To build hierarchies, drag the sub-hierarchy field into the top level. (E.g. Country - State - City)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/qs_hierarchies.htm

## Build sets

**WHAT?** A dynamic group.

**Why**?

Sets are useful in situations it's needed to test in-out membership (AB Testing).

Except mannually selecting members from a list, sets can be created by certain conditions or top.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_sets_create.htm

https://interworks.com/blog/kwagner/2014/06/30/when-use-filters-groups-sets-tableau/

# 3. Field & Chart Types (15%)
## Understand discrete v. continuous

**Distinguish them:**

- Blue pill (#) = Discrete

- Green pill (#) = continuous

**Discrete values**

- Take a limited number of values (size: XS,S,M,L,XL - City: Madrid, Barcelona, Chigago), Could be numeric (customer ID).

- Add headers to the view.

- Discrete values are "individually separate and distinct".

- Filtering by choosing members.

- Color by color palette

**Continuous values**

- "forming an unbroken whole, without interruption"

- Add axes (values) to the view

- Filtering by range

- Color by color gradient

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles.htm

## Understand measure names and measure values

We must understand: measures are a set of numbers, which have 2 attributes: **measure names** and **measure values**.

When we use (part of the) measures to create a visualization, the inner logic is:

- Show measure values on axis

- Show measure names as field headers

- Use measure names to filer out the measures we don't want.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_understanddatawindow_meavalues.htm

## Understand generated fields

**WHAT** Generated fields are fields that Tableau brings as a convenience,
either by internal calculations, or by looking up an internal table / using an internal engine. Calculated fields are also a part of generated fields.

**INCLUDING: **

- Measure Names

- Measure Values

- Longitude and Latitude

- Number of Records

- Calculated fields

https://www.evolytics.com/blog/tableau-fundamentals-introduction-calculated-fields/

## Understand how and when to build:
### Histograms
### Heat maps

Heat maps are like "histograms seen from above", or 2D histograms.
They're usefult to locate "hot spots" on data.

Variables in both axes must be discrete (or made discrete by using calculations, like DATEPART('weekday',[Purchase Date]) ).
Color comes from aggregating continuous [#num records], SUM([Sales]).

Size can also be useful for heat maps: playing with sizes can give you more white space that might make a heatmap easier to understand. Also a combination of color and size can highlight "big" hot spots that could be hidden when showing just one variable.

### Tree maps

This kind of graphics does not show a tree.
Instead it fills a rectangle with data, using area size to display each category importance.

Tree maps can use two measures for coloring hierarchies. In this case, hierarchies will get its Chroma component from the fist dimension and the HUE/brigthness from the second.

### Bullet graphs

This graphs don't resemble a bullet at first sight.
They are like barcharts with a distribution showing progress towards a goal behing the bar.

https://www.interworks.com/blog/ccapitula/2014/12/29/tableau-essentials-chart-types-bullet-graph

How to do it:

There is a Bullet Graph quick access.
https://onlinehelp.tableau.com/current/pro/desktop/en-us/qs_bullet_graphs.html


### Combined Axis Charts


### Dual Axis Charts


### Scatter Plots
### Data Highlighter
### Cross tabs
### Motion charts
### Bar in bar charts
### Box plots

### Gantt Bar Charts

Show duration in a calendar.


### Paretos

Similar to having an ordered barchart with the data, plus a cumulative distribution function overlaid on the chart.
This is done with a dual axis, and a table calculation:

- First make the barchart. Order the bars by value (descending).
- Make a line chart with the same data as the barchart.
  - Make a table calculation to compute the CDF: Running sum percentage.
  - Change the CDF axis to go from 0 to 100%.

https://www.tableau.com/learn/tutorials/on-demand/pareto-charts
http://onlinehelp.tableau.com/current/pro/desktop/en-us/help.html#pareto.html

### Sparklines

## Understand how to effectively use titles, captions  and tooltips
### Understand how to edit axes
### Understand mark labels and annotations


# 4. Calculations (17%)
## Manipulate string and date calculations
## Create quick table calculations

A table calculation is a transformation you apply to the values of a single measure in your view, based on the dimensions in the level of detail.

Quick table calculations are the easiest way to make table calculations.

Just right click on a can be done right clicking a measure. There are some pre-fixed table calculations available:
- Running total (great for displaying goals and Cummulative Distribution Functions - RUNNING_SUM(SUM([Sales])))
- Difference ( ZN(SUM([measure])) - LOOKUP(ZN(SUM([measure])), -1) )
- Percent difference : (ZN(SUM([measure])) - LOOKUP(ZN(SUM([measure])), -1)) / ABS(LOOKUP(ZN(SUM([measure])), -1))
- Percent of total : SUM([measure]) / TOTAL(SUM([measure]))
- Rank (quicker to type than RANK([measure]).
- Percentile ( Equals to: RANK_PERCENTILE(SUM([measure])) )
- Moving average (Equals to: WINDOW_AVG(SUM([measure]), -2, 0)
- (*) YTD Total
- (*) Compound growt rate
- (*) Year over Year growth
- (*) YTD growth

(*) Need a time dimension in order to be calculated.

## Use LOD calculations; types of LOD calculations

When you add/remove a dimension from a graph, the quantities change.
LOD calculations help us set the level of detail that we want for a particular field regardless of what is in the view.

Three kinds of LOD calculations:

| LOD | Descr |
|-----|-------|
| INCLUDE | Adds the data from the dimension, regardless of what's on the view. |
| EXCLUDE | Removes the data from the specified dimension, regardless of what's on the view. |
| FIXED   | Uses the data specified without any reference about anything in the view |

 
Stuff to try (from the superstore sample):

- Sales contribution by city / region (with respect to country totals)
 
Syntax

```{R}
{ FIXED : avg ({fixed [Product Sub-Category]  : sum([Profit]) }) 
```

{ FIXED: something_that_will_condition the outcome : calculation_that_depends_on_the_previous_result }





