# Tableau Desktop 10 Qualified Associate - Exam Outline

Exam notes with links to questions, and solutions that exemplify what's needed / asked in the Tableau 10 QA Exam.

Large part of the content from **@igponce**, really appreciate it.

This note mainly follow the structure of Exam Prep Guide. I also added some contents from course **"Data Visual and Communication"** by **Hettie Tabor**.

Hopefully enough to pass (75%). (And I did, it's not that hard...)

***PLEASE REMEBER!!! GOOGLE IS ALOWED DURING THE TEST!!! SEARCH!***

Exam prep guide:
https://mkt.tableau.com/files/DesktopQA_ExamGuide.pdf

Certification homepage:
https://www.tableau.com/support/certification

# Some Hints and Useful Webs!

Before taking the certification, please go to these website to take some mock exams and get some tips. 

Just for warming up!

- https://learningtableau.com

- https://medium.com/@saahithisurapaneni/i-got-tableau-certified-you-can-too-9cd134c34068

- http://www.tableauinfo.com/tableau-online-mock-tests.html

- https://sarahlovesdata.co.uk/2018/08/06/the-tableau-desktop-qualified-associate-exam/

- https://www.thedataschool.co.uk/wiktoria-rudz/prepare-tableau-certificate-exam/

- https://biztory.com/2017/02/17/ace-tableau-desktop-qualified-associate-exam/


**All the Icons are listed here!**

https://onlinehelp.tableau.com/current/pro/desktop/en-us/tips_visualcues.htm

# Data Visual and Communication Part
----------------------------------------------------------------------------------------------------------------------------------------
Go through all the notes of **Data Visual and Communication**.

https://drive.google.com/drive/folders/15blyiQMiDzVUrunjUoAJsGFrR2Y_7ocu?usp=sharing

# Exam Prep Guide Part
----------------------------------------------------------------------------------------------------------------------------------------
# 0. Dimensions and Measures

Dimension: Qualitative values that cannot be aggregated (Blue).

Measures: Quantitative, numeric values that can be aggregated (Green).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_typesandroles.htm

- How to dis-aggregate the data:

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_aggregation.htm#DisAggData

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

In more details, if we're using data from different data sources, Tableau will make queries in parallel without waiting one data source query to finish before it makes the second query.

*Before version 9.0, tableau desktop and tableau server can only build one connection to one data source, and perform the queries sequentially. So, the totally running time <= sum (single running times). After set the Parallel Query features, multiple queries will run at the same time to improve the performance.*

**LIMITS**
- **Parallel queries will run only when possible.**
If a query is based on a field already queried, then the parallel query will not run and will wait until the previous query will end.
The parallel queries will run in parallel only if there are not concurrent queries on the same field/table.

- **if too many queries (or resource-intensive queries) are happening in parallel, the performance may be slower than expected.**

The level of parallelism is configurable on the 'connection-configs.xml' file (located in the Tableau installation dir.)

From: http://kb.tableau.com/articles/howto/Configuring-Parallel-Queries-in-Tableau-Desktop

### 1.2.2 Data engine vectorization

**WHAT?** A new feature in Tableau 9. Most articles don't go deep into this topic. It's a CPU calculation improve to make performance better.

```
Now tableau uses SIMD instructions (SSE4.1) to make calculations, thus better filling the data and execution pipeline of the processor.
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

With external query caching, Tableau doesn't need to parse a **whole** file to show some data.
This data can be presented as cache goes on.

**LIMITS** when the cache is invalidated, it will need to re-parse the file again, so it's a good idea to make an extract unless the data is really tiny or can change unexpectedly.

### 1.2.5 Query fusion

**WHAT?** A function to improve dashboard queries running performance.

Tableau will at all of dashboard queries and tries to eliminate any redundant queries.

It combining queries together and using the results.
E.g. If the same query is needed in the same dashboard in several sheets, Tableau tries to combine this multiple queries into (just) one. 

**This works with queries with the same LOD.**


## 1.3 Understand how to use Automatic & Custom Split

**WHAT?** Split a single column to multiple columns (up to 10 new columns)

**WHEN?** Sometimes your data has multiple fields encoded inside string fields. Or u just want to split (e.g. full name & first name)

**WHY?** Demoralization, you cannot touch the schema, etc.

- Automatic split: happens when tableau can guess what kind of data is inside, separators, etc...
- Custom split: Manually set the model of split. Similar to calculated field.

Data Split cannot happen automatically. Tableau will only split fields it's told to do so (Manually do it).

You can split data from the Data pane, or right-click: Transform -> Split.

**Important: Data from splits cannot be used for Join tables; but can be used for blending.**

https://onlinehelp.tableau.com/current/pro/desktop/en-us/split.html

## 1.4 Join tables from single and multiple databases

Same joins (inner, left, right, full & union) with SQL.

**2 Types:**

- Join tables from same data source: just drag tables and set key (and other configurations)

- Join tables from different data sources: build 2 difference connections, and then drag tables.

**Important**

- Null Value: if we use simple join, the Null value will be excluded (deleted). If we use **"Join null values to null values"**, Null value will be used in the join process.

- Collation problems (like having fields on UTF-8 and CP850 on different connections).

- Problems with different date formats (be careful to use the same Level of Detail for all data sources).

Seems like lot of questions are from this part. Read the following page carefully before taking the certification.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/joining_tables.html

## 1.5 Data Preparation

**Important Topic:** when to use join and when to use blending?

If you want a single data source, join; multiple data sources, blending.


### 1.5.1 Blending

**WHAT?** Manually build connections between different data sources

**WHEN?** You don't want to put all the data together (using join or union), perhaps because different details level, join causes duplicate or too much data.

- A left join-like operation between different data sources. (That means the link will ensure all the records from main data sources exist, and then try to get information from other data sources based on relationship).
- Local to the view (the relationship is between sheet and sheet.)
- You can amend some data (using change alias option) on the _primary_ data source (you cannot do this on a Join operation).

Look at the data sources:

Blue check -> Primary data source (the left part of the left join)
Orange check -> Secondary source (the right part of the left join)

https://onlinehelp.tableau.com/current/pro/desktop/en-us/multiple_connections.htm

**Automatic Blend**

Do not manually set keys in relationship.

Tableau guesses the blend looking for a field in both data sources with the same name.

**Manual Blend**

Manually set keys in relationship because you do not like the default one.

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

https://www.theinformationlab.co.uk/2011/01/20/tableau-extracts-what-why-how-etc/

https://www.tableau.com/about/blog/2014/7/why-use-tableau-data-extracts-32187

https://www.tableau.com/tableau-data-extracts-part3

**TDE vs. Live Connections**

-  An 'extract' connection is a connection with a static database, which is refreshed every day/hour/period. All data is copied from the data source to Tableau Server. Refreshing the data source will not give you the latest data - you have to refresh the extract to get these.

-  A live connection will query the underlying data in the data source/database - refreshing the visualization will give you the latest results. No data is copied to Tableau Server for pre-processing.

- Some of the calculated functions are limited to TDE

**Important!** what does TDE and live connection cannot do?

- TDE cannot cross join to Live

- Live cannot use several functions

http://drawingwithnumbers.artisart.org/tde-or-live-when-to-use-tableau-data-extracts/

https://onlinehelp.tableau.com/current/pro/desktop/en-us/extracting_data.htm

## 1.9 Understand Shadow extracts

**WHAT?** Shadow extracts is data that Tableau stores when using file (like non-legacy excel) to make loading data faster. I.e. a temporary extract file, will be deleted after then.

**WHEN?** when processing non-legacy excel files, statistical files, or text files.

**WHY?** Processing some file formats is CPU/IO intensive.

**MORE DETAILS: **

- They have .ttde extension.

- Tableau will store up to 5 files with .ttde extension.

- Although they're named extracts, its file format is not the same as .tde (extract) files.

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

Except manually selecting members from a list, sets can be created by certain conditions or top.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/sortgroup_sets_create.htm

https://interworks.com/blog/kwagner/2014/06/30/when-use-filters-groups-sets-tableau/

# 3. Field & Chart Types (15%)
## 3.1 Understand discrete v. continuous

**Distinguish them:**

- Blue pill (#) = Discrete

- Green pill (#) = continuous

**Discrete values**

- Take a limited number of values (size: XS,S,M,L,XL - City: Madrid, Barcelona, Chicago), Could be numeric (customer ID).

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

- Use measure names to filter out the measures we don't want.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/datafields_understanddatawindow_meavalues.htm

## 3.3 Understand generated fields

**WHAT** Generated fields are fields that Tableau brings as a convenience, either by internal calculations, or by looking up an internal table / using an internal engine. Calculated fields are also a part of generated fields.

**INCLUDING: **

- Measure Names

- Measure Values

- Longitude and Latitude

- Number of Records

- Calculated fields

https://www.evolytics.com/blog/tableau-fundamentals-introduction-calculated-fields/

## 3.4 Understand how and when to build:
### 3.4.1 Histograms

| Data | Requirements |
| ------------- | ------------- |
| Mark type | Automatic |
| Rows shelf:  | Continuous measure (aggregated by Count or Count Distinct) |
| Columns shelf:  | Bin (continuous or discrete) |

*What is bin? https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_bins.htm*

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_histogram.htm

### 3.4.2 Heat maps

| Data | Requirements |
| ------------- | ------------- |
| Mark type | Density |
| Rows and Columns:  | At least one continuous measure, and at least one measure or dimension |
| Marks card:  | At least one continuous measure |

A data table that locate "hot spots" on data (use marks card to show data rather than numbers).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_density.htm

https://www.evolytics.com/blog/tableau-201-make-heat-map/

### 3.4.3 Tree maps

| Data | Requirements |
| ------------- | ------------- |
| Mark type | Automatic or Square |
| Color:  | Dimension or Measure |
| Size | Measure |
| Label or Detail:  | Dimension(s) |

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

Add measure name to **Color** and turn off the **Stack Marks** on **Analysis**.

https://www.decisivedata.net/blog/building-bullet-chart-tableau

### Important!!! 3.4.12 Box plots

| Data | Requirements |
| ------------- | ------------- |
| Mark type | Circle |
| Columns shelf:  | Dimension |
| Rows shelf:  | Measure |
| Detail:  | Dimension |

**Important**: How to interpret box plots?

- Boxes indicate the middle 50 percent of the data (that is, the middle two quartiles of the data's distribution).

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_boxplot.htm

### 3.4.13 Gantt bar Charts

Show duration on a calendar.

| Data | Requirements |
| ------------- | ------------- |
| Mark type | Automatic or Gantt Bar |
| Columns shelf:  | Date or Time field (continuous measure) |
| Rows shelf:  | Dimension(s) |
| Size:  | Continuous measure |

https://onlinehelp.tableau.com/current/pro/desktop/en-us/buildexamples_gantt.htm

https://www.tableau.com/about/blog/2017/6/using-gantt-charts-tableau-manage-project-72429

### 3.4.14 Pateros

A bar chart with a line chart. We can add table calculation on the bar (line).
The bar will be automatically sorted descending.

https://www.tableau.com/learn/tutorials/on-demand/pareto-charts

https://onlinehelp.tableau.com/current/pro/desktop/en-us/pareto.htm

### 3.4.15 Sparkline

A Sparkline is a compact trend line chart without axis that fits in a small area and shows values over time.

https://kb.tableau.com/articles/howto/creating-sparklines

https://kb.tableau.com/articles/howto/creating-sparklines

## 3.5 Understand how to effectively use titles, captions and tooltips

https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_specific_titlecaption.htm

## 3.6 Understand how to edit axes

https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_editaxes.htm

## 3.7 Understand mark labels and annotations

Labels: https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_specific_fields.htm

Annotations: https://onlinehelp.tableau.com/current/pro/desktop/en-us/formatting_editaxes.htm

# 4. Calculations (17%)
## 4.1 Manipulate string and date calculations

Just open the page to find the function you want. Or just Google it.

Get familiar with the important functions will be fine.

- String functions: https://onlinehelp.tableau.com/current/pro/desktop/en-us/functions_functions_string.htm

- Date functions: https://onlinehelp.tableau.com/current/pro/desktop/en-us/functions_functions_date.htm

## 4.2 Create quick table calculations

A table calculation is a transformation you apply to the values of a single measure in your view, based on the dimensions in the level of detail.

Quick table calculations are the easiest way to make table calculations.

Just right click on a can be done right clicking a measure. There are some pre-fixed table calculations available:
- Running total (great for displaying goals and Cumulative Distribution Functions - RUNNING_SUM (SUM ([Sales])))
- Difference (ZN (SUM ([measure])) - LOOKUP (ZN (SUM ([measure])), -1))
- Percent difference : (ZN(SUM([measure])) - LOOKUP(ZN(SUM([measure])), -1)) / ABS(LOOKUP(ZN(SUM([measure])), -1))
- Percent of total: SUM ([measure]) / TOTAL (SUM ([measure]))
- Rank (quicker to type than RANK ([measure]).
- Percentile (Equals to: RANK_PERCENTILE (SUM ([measure])))
- Moving average (Equals to: WINDOW_AVG (SUM ([measure]), -2, 0)
- (*) YTD Total
- (*) Compound growth rate
- (*) Year over Year growth
- (*) YTD growth

(*) Need a time dimension in order to be calculated.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_tablecalculations.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/functions_functions_tablecalculation.htm

## 4.3 Use LOD calculations; types of LOD calculations

**What is LOD?**

- We know the information are in different levels (e.g. **[Sales] / [Profit]**, **SUM[ProfitRatio]** and **SUM(Sales) / SUM(Profit)**).

- When we use aggregated and in-aggregated data into the same calculation (e.g. **[Sales] – AVG([Sales])**) , error happens:

> “Cannot mix aggregate and non-aggregate arguments with this function”

- So, we need to change the function to **[Sales] - {AVG([Sales])}**, to assign the LOD and solve the problem.

**What is LOD of Filter?**

Before us discussing about LOD Expressions, we must understand the running sequence of Filters and their LOD.

There are different types of Filters in Tableau, and they will run in the following sequence:
1. Extract Filters
2. Data Source Filters
3. Context Filters
4. Dimension Filters
   - = Where in SQL
   - The place that FIXED LOD perform
5. Measure Filter
   - = Having in SQL
   - The place that INCLUDE/EXCLUDE LOD perform
6. Tableau Calculation Filters

For the filters in the same type, the running sequence will be decided by the position.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod_overview.htm

**What is LOD Expression?**

LOD expression is an expression in calculated fields to set the LOD of calculation. In more details, it allow users to run calculations on the root data, before any filters.

3 kinds of LOD calculations:

| LOD | Descr | Use in | Example |
|-----|-------|--------|---------|
| INCLUDE | Adds the data from the dimension, regardless of what's on the view. | Get the calculation to include a lower level | **AVG({INCLUDE [Customer Name] : SUM([Sales])})** Get sum of sales of a lower level, each customer. |
| EXCLUDE | Removes the data from the specified dimension, regardless of what's on the view. | Get the calculation to omit a lower level| **SUM ({EXCLUDE [Month]: SUM ([Sales])}) ** Omit level [Month], get sales sum of the higher level. |
| FIXED   | Uses the data in specified dimensions without reference to anything in the view. | Get the sum of a lower level | **{FIXED [Region] : SUM([Sales])}** Get sum of sales on the specific [region] level. |

 

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod.htm

https://onlinehelp.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod_overview.htm

https://www.tableau.com/about/blog/LOD-expressions


# 5. Mapping (9%)
## 5.1 Map Manipulation
Different types of map:

- Filled maps

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_howto_filledpiechart.htm
 
- Layered maps

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_options.htm#add-us-data-layers

- WMS server maps

https://onlinehelp.tableau.com/current/pro/desktop/en-us/maps_mapsources_wms.htm

- Symbol maps

https://www.theinformationlab.co.uk/2014/12/11/show-symbol-maps/

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

Spatial Files is a kind of special files that can be loaded into tableau by one connection (multiple files).

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

- Understand how to compare and choose models

- meaning of R^2 and P-value.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/trendlines_add.htm

## 6.4 Trend Model

Can choose different regression models.

https://onlinehelp.tableau.com/current/pro/desktop/en-us/trendlines_add.htm

## 6.5 Forecasting

- Understand how to find Upper and Lower forecasting margins

- How to change confidence level

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

I.e. use a sheet as panel. Select the individual on the panel and another dashboard for that individual's details will show.

https://community.tableau.com/thread/154368

## 7.6 Understand how to utilize visual best practices for dashboard design
----------------------------------------------------------------------------------------------------------------------------------------

# Knowledge Base Questions

- To connect to multiple tables in a single data source at one time, what must be specified?  (A join)

- Tableau can create worksheet-specific filters. (True)

- What does the box in a box plot represent? (The range of the middle half of the data points)

- Where can find the Saved Data Source option in Tableau? (Start Page)

- Reference Line Uses Parameter (fixed numbers)? (True)

- How to label multiple fields in a graph? (Drag both the field into label)

- The tableau desktop automatically creates a hierarchy of data part. Can we change the order without using any field? (Yes)

- If we create a set, then how and where it will save? (In the data pane as a calculated field)

- When you drag a measure and a dimension in the view, what is the order of the tree map? (Lower value in the bottom right and higher value in the top left)

- How do we save ad-hoc calculation? (Drag it in the measure or dimension and rename)

- In data blending, relational data field is required in visualization of the data (No)

- Which of the following charts types always includes bars sorted in descending order? (Pareto Chart)

- Which of the following charts uses binned data? (Histogram)

- If a field has a blue background that means the field is? (Discrete)

- When might you want to use a context filter? (When you want to FIRST apply a filter and THEN show the Top N or Bottom N elements)

- This type level of detail expression computes total sales for the region, regardless of what dimensions are shown in the view. ({FIXED [Region]: SUM ([Sales])})

- A dimension is a field that typically holds (discrete qualitative data)

- Dates are typically treated as (Dimension)

- A Reference Band cannot be based on two fixed points. (False)

- A Reference Distribution plot cannot be along a continuous axis. (False)

- Which of the following is not a Trend Line model? (Binomial Trend Line)

- Is it possible to deploy a URL action on a dashboard object to open a Web Page within a dashboard rather than opening the system's web browser? (True, with the use of a Web Page object)

- The Highlighting action can be disabled for the entire workbook. (True)

- A sheet cannot be used within a story directly. Either sheets should be used within a dashboard, or a dashboard should be used within a story. (False)

- How do you identify a continuous field in Tableau? (It is identified by a green pill in a visualization.)

-  Is it possible to use measures in the same view multiple times (e.g. SUM of the measure and AVG of the measure)? (Yes)

- Sets can be created on Measures. (False)

- For creating variable size bins we use (Calculated Fields)

- A good reason to use a bullet graph is (Comparing the actual against the target sales)

- Disaggregation returns all records in the underlying data source (True)

- By definition, Tableau displays measures over time as a (Line)

- A Reference Band can't be founded on two settled focuses. (False)

- A Reference Dissemination plot can't be along a consistent hub. (False)

- Which of the accompanying isn't a Pattern Line display? (Binomial Pattern Line)

- The picture beneath utilizes which delineate? (WMS server maps)

- Is it conceivable to convey a URL activity on a dashboard question open a Site page inside a dashboard as opposed to opening the framework's internet browser?  (True, with the utilization of a Site page protest)

- The Featuring activity can be crippled for the whole exercise manual. (True)

- A sheet can't be utilized inside a story straightforwardly. Either sheet ought to be utilized inside a dashboard, or a dashboard ought to be utilized inside a story. (False)

- How would you recognize a ceaseless field in Scene? (It is recognized by a green pill in a perception)

- Is it conceivable to utilize measures in a similar view various circumstances (e.g. Total of the measure and AVG of the measure)?  (Yes)

- Sets can be made on Measures. (False)

- For making variable size canisters we utilize (Calculated Fields)

- A justifiable reason motivation to utilize a projectile chart. (Looking at the genuine against the objective deals)

- Disaggregation restores all records in the hidden information source. (True)

- By definition, Scene shows measures after some time as a (Line)

- Can we perform all kinds of joins using Data blending?  (Yes)

- Can we display top five and last five sales in the same view? (Yes, using set)

- Can parameters have a dropdown list? (Yes)

- Increase filter numbers will optimize the performance of dashboard? (False)

- Can we remove the All options from a Tableau auto-filter?  (Yes)

- Is performance testing available in tableau? (Yes)

- We can combine database and flat file data in Tableau desktop (True)

- Definition of a fact table? (The measurements, metrics or facts of a business process. It is located at the center of a star schema or a snowflake schema surrounded by dimension tables. )

- Which of the following is a new Tableau 10 feature? (Subscribe others)

- New connector in Tableau 10? (Web Data Connector & MemSQL Names Connector)

- What is the color of discrete Measures in Tableau? (Blue)

- For Bullet Graphs we need at least?? Measures (2)

- Which aggregate function is used to calculate distinct count (COUNTD ())

- What are the Tableau actions? (Filter, Highlight, Go to URL, Go to Sheet, Change Set Values)

