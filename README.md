# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam notes with links to questions, and solutions that
exemplify what's needed / asked in the Tableau 10 QA Exam.

Large part of the content from **@igponce**, really appreicate it.

This note mainly follow the structure of Exam Prep Guid. I also added some contents from course **"Data Visual and Communication"** by **Hettie Tabor**.

Hopefully enough to pass (75%).

***PLEASE REMEBER!!! GOOGLE IS ALOWED DURING THE TEST!!! SEARCH!***

Exam prep guide:
https://mkt.tableau.com/files/DesktopQA_ExamGuide.pdf

Certification homepage:
https://www.tableau.com/support/certification

# Some Hints and Usefull Webs!

Before taking the certification, pleaes go to these website to take some mock exams and get some tips. 

Just for warming up!

- https://learningtableau.com

- https://medium.com/@saahithisurapaneni/i-got-tableau-certified-you-can-too-9cd134c34068

- https://www.simplilearn.com/tableau-exam-questions-free-practice-test

- https://sarahlovesdata.co.uk/2018/08/06/the-tableau-desktop-qualified-associate-exam/

- https://www.thedataschool.co.uk/wiktoria-rudz/prepare-tableau-certificate-exam/

- https://biztory.com/2017/02/17/ace-tableau-desktop-qualified-associate-exam/


**All the Icons are listed here!**

https://onlinehelp.tableau.com/current/pro/desktop/en-us/tips_visualcues.htm

# Data Visual and Communication Part
----------------------------------------------------------------------------------------------------------------------------------------
Go through all the notes of **Data Visual and Communication**.

https://drive.google.com/drive/folders/15blyiQMiDzVUrunjUoAJsGFrR2Y_7ocu?usp=sharing

# Exam Prep Guid Part
----------------------------------------------------------------------------------------------------------------------------------------
# 0. Dimensions and Measures

Dimension: Qualitative values that cannot be aggregated(Blue).

Measures: Quantitative, numeric values that can be aggregated (Green).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles.htm


# 1. Data Connections (23%)

Many questions will come from the new features of tableau 9.0 and 10.0.

Tableau 9.0:

https://www.tableau.com/about/blog/2015/1/90-preview-query-performance-improvements-36406

Tableau 10.0:

https://www.tableau.com/about/blog/2016/8/ten-features-it-will-love-tableau-10-57937

https://www.tableau.com/new-features/10.0#tab-easier-1


## 1.1 Understand how to connect to Tableau Server

Check:
https://onlinehelp.tableau.com/current/pro/desktop/en-us/examples_tableauserver.htm

## 1.2 Understand Performance Optimization

### 1.2.1 Parallel query

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

### 1.2.2 Data engine vectorization

**WHAT?** A new feature in Tableau 9. Most articles don't go deep into this topic. It's a cpu calculation improve to make performance better.

```
Now tableau uses SIMD instructions (SSE4.1) to make calculations, thus better filling the
data and execution pipeline of the processor.
```

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf


### 1.2.3 Parallel aggregation

**WHAT?** A function to run multiple aggregations at the same time.

Tableau will use multiple cores where possible. The data engine can now run aggregations in parallel, splitting the work across multiple cores. 

By default, the maximum degree of parallelism is **(number of available logical processors) / 2**. This means that query operations on data extracts can run up to N times faster in Tableau 9 (where N is the number of cores in the machine).

From https://alanattableau.files.wordpress.com/2015/07/designing-efficient-workbooks-v92.pdf

### 1.2.4 External query caching

**WHAT?** A function to improve the cache performance.

Tableau will cache data in memory / disk to avoid to ask ("go to the network") or parse data that won't have changed (E.g. file-based data).

With extenal query caching, Tableau doesn't need to parse a **whole** file to show some data.
This data can be presented as cache goes on.

**LIMITS** when the cache is invalidated, it will need to re-parse the file again, so it's a good idea to maka an extract unless the data is really tiny or can change unexpectedly.

### 1.2.5 Query fusion

**WHAT?** A function to improve dashbord queries running performance.

Tableau will at all of dashboard queries and tries to eliminate any redundant queries.

It combining queries together and using the results.
E.g. If the same query is needeed in the same dashboard in several sheets, Tableau
tries to combine this multiple queryes into (just) one. 

**This works with queries with the same LOD.**


## 1.3 Understand how to use Automatic & Custom Split

**WHAT?** Split a single column to multiple columns (up to 10 new columns)

**WHEN?** Sometimes your data has multiple fields encoded inside string fields. Or u just want to split (e.g. full name & first name)

**WHY?** Denormalization, you cannot touch the schema, etc.

- Automatic split: happens when tableau can guess what kind of data is inside,separators, etc...
- Custom split: Mannually set the model of split. Similar to calculated field.

Data Split cannot happen automagicaly. Tableau will only split fields it's told to do so (Mannually do it).

You can split data from the Data pane, or right-click: Transform -> Split.

**Important: Data from splits cannot be used for Joing tables; but can be used for blending.**

https://onlinehelp.tableau.com/current/pro/desktop/en-us/split.html

## 1.4 Join tables from single and multiple databases

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

## 1.5 Data Preparation

### 1.5.1 Blending

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

### 1.5.2 Metadata Grid

**WHAT?** In Data Source page, Data Preview part, change the table to a list of field name

**IN MORE DETAILS:**

- Sometimes a list of fields is more useful than a preview of the data so we’ve added a “Metadata” view of your connection.

- It displays, the field name, table, and remote field name.

- For calculations and splits, there is no table field.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/environment_datasource_page.htm#Metadata

https://www.tableau.com/about/blog/2015/1/tableau-90-auto-data-prep-stay-flow-35980#ucsrKSAg7CtOuoBL.99

### 1.5.3 Pivot

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

### 1.5.4 Union

**WHAT?** Same as the union in SQL.

**Details:** 

- Connect to 2 data sources and then create a "New Union"

- "Wildcard" option is available to search for files to union together

- Mismatched fields in the union can be merged (or change name on all source files)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/union.htm

### 1.5.5 Data Interpreter

**WHAT?** A function to clean the data from titles, notes, footers, empty cells etc.

Data Interpreter will automatically generate a table from the source data with titles or notes.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/data_interpreter.htm


## 1.6 Understand connection options

https://onlinehelp.tableau.com/current/pro/desktop/en-us/basicconnectoverview.htm

## 1.7 Understand how to connect to different file types

https://onlinehelp.tableau.com/current/pro/desktop/en-us/examples_extract.htm

## 1.8 Understand data extract capabilities

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

## 1.9 Understand Shadow extracts

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

## 2.1 Filter data

- Filter data from views:

https://onlinehelp.tableau.com/current/pro/desktop/en-us/filtering.htm

- Filter data from data sources:

https://onlinehelp.tableau.com/current/pro/desktop/en-us/filtering_datasource.htm

## 2.2 Sort data

https://onlinehelp.tableau.com/current/reader/desktop/en-us/reader_sort.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_sorting_computed_howto.htm

## 2.3 Build groups

Create group to put dimensions into them (e.g. cities into US & Canada)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_groups_creating.htm


## 2.4 Build hierarchies

To build hierarchies, drag the sub-hierarchy field into the top level. (E.g. Country - State - City)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/qs_hierarchies.htm

## 2.5 Build sets

**WHAT?** A dynamic group.

**Why**?

Sets are useful in situations it's needed to test in-out membership (AB Testing).

Except mannually selecting members from a list, sets can be created by certain conditions or top.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_sets_create.htm

https://interworks.com/blog/kwagner/2014/06/30/when-use-filters-groups-sets-tableau/

# 3. Field & Chart Types (15%)
## 3.1 Understand discrete v. continuous

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

## 3.2 Understand measure names and measure values

We must understand: measures are a set of numbers, which have 2 attributes: **measure names** and **measure values**.

When we use (part of the) measures to create a visualization, the inner logic is:

- Show measure values on axis

- Show measure names as field headers

- Use measure names to filer out the measures we don't want.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_understanddatawindow_meavalues.htm

## 3.3 Understand generated fields

**WHAT** Generated fields are fields that Tableau brings as a convenience,
either by internal calculations, or by looking up an internal table / using an internal engine. Calculated fields are also a part of generated fields.

**INCLUDING: **

- Measure Names

- Measure Values

- Longitude and Latitude

- Number of Records

- Calculated fields

https://www.evolytics.com/blog/tableau-fundamentals-introduction-calculated-fields/

## 3.4 Understand how and when to build:
### 3.4.1 Histograms

| Data  | Requirments |
| ------------- | ------------- |
| Mark type  | Automatic  |
| Rows shelf:  | Continuous measure (aggregated by Count or Count Distinct)  |
| Columns shelf:  | Bin (continuous or discrete)  |

*What is bin? https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_bins.htm*

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_histogram.htm

### 3.4.2 Heat maps

| Data  | Requirments |
| ------------- | ------------- |
| Mark type  | Density  |
| Rows and Columns:  | At least one continuous measure, and at least one measure or dimension  |
| Marks card:  | At least one continuous measure  |

A data table that locate "hot spots" on data (use marks card to show data rather than numbers).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_density.htm

https://www.evolytics.com/blog/tableau-201-make-heat-map/

### 3.4.3 Tree maps

| Data  | Requirments |
| ------------- | ------------- |
| Mark type  | Automatic or Square  |
| Color:  | Dimension or Measure  |
| Size  | Measure  |
| Label or Detail:  | Dimension(s)  |

It fills a rectangle with data, using area size to display each category importance.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_treemap.htm

### 3.4.4 Bullet graphs

A variation of a bar graph to show goals (with different percentage) and actual number.

https://www.interworks.com/blog/ccapitula/2014/12/29/tableau-essentials-chart-types-bullet-graph

https://onlinehelp.tableau.com/current/pro/desktop/en-us/qs_bullet_graphs.htm


### 3.4.5 Combined Axis Charts

https://onlinehelp.tableau.com/current/pro/desktop/en-us/qs_combo_charts.htm

### 3.4.6 Dual Axis Charts

https://kb.tableau.com/articles/howto/dual-axis-bar-chart-multiple-measures

https://associationanalytics.com/2017/03/08/creating-a-dual-axis-chart-in-tableau/

### 3.4.7 Scatter Plots

Point graphs with trend lines (add from analytics).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_scatter.htm

### 3.4.8 Data Highlighter

https://onlinehelp.tableau.com/current/pro/desktop/en-us/actions_highlight_highlighter.htm

### 3.4.9 Crosstabs (Text Table)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_text.htm

### 3.4.10 Motion charts

Add the time line to page part.

https://tableautraininghq.com/tableau-motion-chart/

https://www.tutorialspoint.com/tableau/tableau_motion_charts.htm

### 3.4.11 Bar in bar charts

Add measure name to **Color** and trun off the **Stack Marks** on **Analysis**.

https://www.decisivedata.net/blog/building-bullet-chart-tableau

### 3.4.12 Box plots

| Data  | Requirments |
| ------------- | ------------- |
| Mark type  | Circle  |
| Columns shelf:  | Dimension  |
| Rows shelf:  | Measure  |
| Detail:  | Dimension  |

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_boxplot.htm

### 3.4.13 Gantt Bar Charts

Show duration on a calendar.

| Data  | Requirments |
| ------------- | ------------- |
| Mark type  | Automatic or Gantt Bar  |
| Columns shelf:  | Date or Time field (continuous measure)  |
| Rows shelf:  | Dimension(s)  |
| Size:  | Continuous measure  |

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_gantt.htm

https://www.tableau.com/about/blog/2017/6/using-gantt-charts-tableau-manage-project-72429

### 3.4.14 Paretos

A bar chart with a line chart. We can add table calculation on the bar (line).

https://www.tableau.com/learn/tutorials/on-demand/pareto-charts

https://onlinehelp.tableau.com/current/pro/desktop/en-us/pareto.htm

### 3.4.15 Sparklines

A sparkline is a compact trend line chart without axis that fits in a small area and shows values over time.

https://kb.tableau.com/articles/howto/creating-sparklines

https://kb.tableau.com/articles/howto/creating-sparklines

## 3.5 Understand how to effectively use titles, captions  and tooltips

https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_specific_titlecaption.htm

## 3.6 Understand how to edit axes

https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_editaxes.htm

## 3.7 Understand mark labels and annotations

Labels: https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_specific_fields.htm

Annotations: https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_editaxes.htm

# 4. Calculations (17%)
## 4.1 Manipulate string and date calculations

Get familiar with the important functions will be fine.

- String functions: https://onlinehelp.tableau.com/current/pro/desktop/en-us/functions_functions_string.htm

- Date functions: https://onlinehelp.tableau.com/current/pro/desktop/en-us/functions_functions_date.htm

## 4.2 Create quick table calculations

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

## 4.3 Use LOD calculations; types of LOD calculations

**What is LOD?**

- We know the information are in different levels (e.g. **[Sales] / [Profit]**, **SUM[ProfitRatio]** and **SUM(Sales) / SUM(Profit)**).

- When we use aggregated and unaggregated data into the same calculation (e.g. **[Sales] – AVG([Sales])**) , error happends:

> “Cannot mix aggregate and non-aggregate arguments with this function”

- So, we need to change the function to **[Sales] - {AVG([Sales])}**, to assign the LOD and solve the problem.

**What is LOD of Filter?**

Before we discussing about LOD Expressions, we must understand the running squence of Filters and their LOD.

There are different types of Flters in Tableau, and they will run in the following sequence:
1. Extract Filters
2. Data Source Filters
3. Context Filters
4. Dimension Filters
   - = Where in SQL
   - The place that FIXED LOD perform
5. Measure Filter
   - = Having in SQL
   - The place that INCLUDE/EXCLUDE LOD perform
6. Tablea Calculation Filters

For the filters in the same type, the running sequence will be decided by the position.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod_overview.htm

**What is LOD Expression?**

LOD expression is a exppression in calculatied fields to set the LOD of calcualtion. In more details, it allow users to run calculations on the root data, before any filters.

3 kinds of LOD calculations:

| LOD | Descr | Use in | Example |
|-----|-------|--------|---------|
| INCLUDE | Adds the data from the dimension, regardless of what's on the view. | Get the calculation to include a lower level | **AVG({INCLUDE [Customer Name] : SUM([Sales])})** Get sum of sales of a lower level, each customer. |
| EXCLUDE | Removes the data from the specified dimension, regardless of what's on the view. | Get the calculation to omit a lower level| **SUM({EXCLUDE [Month] : SUM([Sales])})** Omit level [Month], get sales sum of the higer level. |
| FIXED   | Uses the data in specified dimensions without reference to anything in the view. | Get the sum of a lower level | **{FIXED [Region] : SUM([Sales])}** Get sum of sales on the sepcific [region] level. |

 

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod_overview.htm

https://www.tableau.com/about/blog/LOD-expressions


# 5. Mapping (9%)
## 5.1 Map Manipulation
Different types of map:

- Filled maps
 
- Layered maps

- WMS server maps

- Symbol maps
### 5.1.1 Pan & Zoom

https://onlinehelp.tableau.com/current/pro/desktop/en-us/inspectdata_pan_zoom.htm

### 5.1.2 Filtering

http://www.storybench.org/build-map-use-filters-tableau-public/

### 5.1.3 Map layering

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_options.htm

### 5.1.4 Custom territories

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_custom_territories.htm

### 5.1.5 Lasso & Radial selection

https://onlinehelp.tableau.com/current/pro/desktop/en-us/inspectdata_selectiontools.htm

## 5.2 Understand how to modify locations within Tableau

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_editlocation.htm

## 5.3 Understand how to import and manage custom geocoding

**WHAT?** A Flexible way to plot data on a map. It's available for all workbooks on a computer. It's much better than blending a geo table.

| Capability | Data Blending | Custom Geocoding |
|------------|---------------|------------------|
| Plot your own locations on a map view | Yes | Yes |
| Use any data | Yes | No, text files only |
| Add new geographic roles | No | Yes|
| Add to an existing geographic role | No | Yes |
| Create new geographic hierarchies | No | Yes |
| Can be reused for other workbooks	| No |Yes |

**HOW?**

Map > Geocoding > Import Custom Geocoding

Map > Geocoding > Remove Custom Geocoding

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_customgeocode_datablend.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/custom_geocoding.htm

https://www.tableau.com/learn/tutorials/on-demand/custom-geocoding?signin=811711e0da62a84bc419d6e14f1ed646

## 5.4 Understand how to use a background image map

https://onlinehelp.tableau.com/current/pro/desktop/en-us/bkimages.htm

## 5.5 Understand how to use Geographic search

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_mapsearch.htm

## 5.6 Understand how to connect to spatial files

Spartial Files is a kind of special files that can be loaded into tableau by one connection (multiple files).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_shapefiles.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/examples_spatial_files.htm

# 6. Analytics (15%)

A lot of cool stuff here!

https://onlinehelp.tableau.com/current/pro/desktop/en-us/environ_workspace_analytics_pane.htm

## 6.1 Reference Lines

https://onlinehelp.tableau.com/current/pro/desktop/en-us/reference_lines.htm

## 6.2 Reference Bands

https://onlinehelp.tableau.com/current/pro/desktop/en-us/reference_lines.htm

## 6.3 Trend Lines

https://onlinehelp.tableau.com/current/pro/desktop/en-us/trendlines_add.htm

## 6.4 Trend Model

Can choose different regression models.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/trendlines_add.htm

## 6.5 Forecasting

https://onlinehelp.tableau.com/current/pro/desktop/en-us/forecast_how_it_works.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/forecast_create.htm

## 6.6 Drag & Drop Analytics

https://community.tableau.com/thread/215043

## 6.7 Box Plot

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_boxplot.htm

## 6.8 Reference distributions

https://onlinehelp.tableau.com/current/pro/desktop/en-us/reference_lines.htm

## 6.9 Statistical summary card

https://onlinehelp.tableau.com/current/pro/desktop/en-us/inspectdata_summary.htm

## 6.10 Instant Analytics

https://www.tableau.com/about/blog/2015/1/analytics-flow-36387

# 7. Dashboards (11%)
## 7.1 Understand publishing & sharing options

https://onlinehelp.tableau.com/current/pro/desktop/en-us/publish_workbooks_share.htm

https://kb.tableau.com/articles/howto/sharing-workbooks-without-tableau-desktop

## 7.2 Understand how to build dashboards

https://onlinehelp.tableau.com/current/pro/desktop/en-us/dashboards_create.htm

## 7.3 Understand dashboard actions

https://onlinehelp.tableau.com/current/pro/desktop/en-us/actions.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/actions_highlight_advanced.htm

## 7.4 Understand Device Designer

https://onlinehelp.tableau.com/current/pro/desktop/en-us/dashboards_dsd_create.htm

## 7.5 Understand how to create a drill down report

I.e. use a sheet as panel. Select the individual on the panel and another dashbord for that individual's details will show.

https://community.tableau.com/thread/154368

## 7.6 Understand how to utilize visual best practices for dashboard design


