# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam notes with links to questions, and solutions that
exemplify what's needed / asked in the Tableau 10 QA Exam.

Large part of the content from **@igponce**, really appreicate it.

This note mainly follow the structure of Exam Prep Guid. I also added some contents from course **"Data Visual and Communication"** by **Hettie Tabor**.

Hopefully enough to pass (75%).

Exam prep guide:
http://mkt.tableau.com/files/Desktop-10-QA-Exam-Prep-Guide.pdf

Certification homepage:
https://www.tableau.com/support/certification

# Exam Prep Guid Part
----------------------------------------------------------------------------------------------------------------------------------------


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

Sometimes a list of fields is more useful than a preview of the data so we’ve added a “Metadata” view of your connection.

It displays, the field name, table, and remote field name.
In the case of splits, there is no remote field name.
For calculations, there is no table field.

https://www.tableau.com/about/blog/2015/1/tableau-90-auto-data-prep-stay-flow-35980#ucsrKSAg7CtOuoBL.99

### Pivot

Example:

| dimension1 | dimension2 | year 2015 | year 2016 |year 2017 |
|------------|------------|-----------|-----------|----------|
| data |data |data |data |data |

The fields "year201[567]" are pivoted.
Tableau cannot work easily with this kind of data unless you make an analytical table form it.

How? Using the pivot option.

- The Pivot option is available from the grid and metadata grid.
- All fields in the pivot must be from the same connection.
- Only one pivot is allowed per data source.
- Pivot fields can be used as the join key.
- The Pivot option cannot be used in calculated fields.


### Union

This is similar to database partitioning:

You have part of your data in a datasource, like for example, the fist half of 2016,
and in another datasource you have the second half of 2016.

Potential problem with unions: changed names in fields from file-to-file.
In this case you'll need to Merge the columns into one (with right click - the option is over the Pivol command).

https://www.tableau.com/about/blog/2016/1/combine-your-data-files-union-tableau-93-48891

### Data Interpreter

Sometimes the data from files does not start at row1.
Maybe it's displaced several rows with disparate content before (like metadata about when the data was collected, copyright, etc...).
This is could happen with data published by official statistics agencies (EUROSTAT, INE, etc.)

Example:

```{text}
EUROPEAN CLIMATE ASSESSMENT & DATASET (ECA&D), file created on 26-06-2017
THESE DATA CAN BE USED FREELY PROVIDED THAT THE FOLLOWING SOURCE IS ACKNOWLEDGED:

Klein Tank, A.M.G. and Coauthors, 2002. Daily dataset of 20th-century surface
air temperature and precipitation series for the European Climate Assessment.
Int. J. of Climatol., 22, 1441-1453.
Data and metadata available at http://www.ecad.eu

FILE FORMAT (MISSING VALUE CODE IS -9999):

01-06 SOUID: Source identifier
08-15 DATE : Date YYYYMMDD
17-21 HU   : humidity in 1 %
23-27 Q_HU : Quality code for HU (0='valid'; 1='suspect'; 9='missing')

This is the blended series of station SPAIN, BARCELONA-EL RAVAL (STAID: 11014).
Blended and updated with sources: 155989 
See file sources.txt and stations.txt for more info.

 SOUID,    DATE,   HU, Q_HU
155989,20080101,   v1,    v2
...
```

## Understand connection options
## Understand how to connect to different file types

## Understand data extract capabilities



## Understand Shadow extracts

When used: when processing non-legacy excel files, statistical files, or text files.
Why? Processing some file formats is CPU/IO intensive.

Location:
    - For Windows: Users\AppData\Local\Tableau\Caching\TemporaryExtracts
    - For Mac: ~Library/Caches/com.tableau.Caching/TemporaryExtracts
    - Sometimes also in the "My Tableau Repository\Shadow Extras" folder.

Shadow extracts is data that Tableau stores when using file (like non-legacy excel) to make loading data faster.
They have .ttde extension.
Tableau will store up to 5 files with .ttde extension.
Altough they're named extracts, its file format is not the same as .tde (extract) files.

http://kb.tableau.com/articles/issue/low-disk-space-because-of-ttde-files
http://www.icancrack.com/index.php/52/what-is-shadow-extract-in-tableau

# Organizing & Simplifying Data
Understand how to:

## Filter data

## Sort data

## Build groups



## Build hierarchies

To build hierarchies, drag the sub-hierarchy field into the top level.
For instacne if you have product and product category fields, to create a Product Category - Product hierarchy, you can drag Product over Product Category to create a hierarchy.

## Build sets

Why sets?

Sets are useful in situations it's needed to test in-out membership.
They can built by hand selecting members, using an expression, or *taking the top XX* members by aggregating a measure.

Some complicated questions like, finding the minimum ticket amounts from the top 6 customers can be easily calculated filtering with sets.

# Field & Chart Types
## Understand discrete v. continuous

How to tell from data:
Blue pill = Discrete
Green pill = continuous

Discrete values -> Take a limited number of values (size: XS,S,M,L,XL - City: Madrid, Barcelona, Chigago).
Could be numeric (customer ID).
Discrete values are "individually separate and distinct".

Continuous values "forming an unbroken whole, without interruption"

Do not mistake with Dimensions and Measures, altough Dimensions are usually Discrete and Measures continuous.

Filtering and color are special for Dimensions and Measures.

Filtering discrete values: choose members (by hand, or with regex).
Filtering continuous: by range.

Coloring discrete values: Color palette.
Coloring continuous values: Using a color gradient.

See http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles_dataroles_dimensionmeasure.html

## Understand measure names and measure values

This is important in charts that show different measures, like parallel coordinates charts.

In this case you can drag to a row/column "measure names" and "measure values".

To select different measures, you have to filter "measure names" in the filter card.

http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_understanddatawindow_meavalues.html

## Understand generated fields

Generated fields are fields that Tableau brings as a convenience,
either by internal calculations, or by looking up an internal table / using an internal engine.

- Measure Names
- Measure Values
- Longitude and Latitude
- Number of Records

For instance longitude and latitude are generated by Tableau using its geolocation module (warning! be careful to match localization of geographical points like cities, states, postal codes, etc.. This can be configured in Tableau, but be warned when plotting stuff outside your country).

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


# Calculations
## Understand how to:
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

# Dimensions and Measures

Hint: If it makes sense to add it up, it's likely a measure.

Dimension: The stuff you use to ask questions. The "who" you restrict the answer to.
Measures: The numbers / answers you get.

Measures usually are aggregated. It makes sense to see the total/max/min under the point of view of a Dimension.
By default, Tableau will interpret numerical fields as Measures.

Source: http://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles_dataroles_dimensionmeasure.html



