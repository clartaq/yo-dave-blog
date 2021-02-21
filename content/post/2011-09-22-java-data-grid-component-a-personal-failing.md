---
title: Java Data Grid Component -- A Personal Failing
tags:
  - improvement
  - java
  - literate programming
  - perfectionism
  - statistics
  - swing
id: 469
comment: false
categories:
  - programming
date: 2011-09-22 08:06:10
modified: 2018-03-25T15:45:34.728098-04:00
---

Many of the programs I write need a way to enter and edit a two-dimensional grid of data in the user interface. Such a grid doesn't need to be a full-fledged spreadsheet, just provide flexible data entry and editing. Alas, there doesn't seem to be such a thing and I haven't created one that I'm satisfied with.

<!--more-->

Most of what you can find on the web is closed source or doesn't really fit the need. It all seems to be based on the [JTable](http://download.oracle.com/javase/tutorial/uiswing/components/table.html) component. I tend to go back and forth between getting fed up extending JTable going off to implement something better, which is just too hard, and going back to extending JTable. Rinse, lather, repeat. Aargh!

It turns out that I'm not the only one to consider this. In a <del>[post](http://stuffthathappens.com/blog/2008/01/17/jtable-time-to-rethink-nih/)</del> (**Broken Link**) back in 2008 Eric Burke considers a similar problem. And yet, three years later the problem persists. <del>[JXTable](http://download.java.net/javadesktop/swinglabs/releases/0.8/docs/api/org/jdesktop/swingx/JXTable.html)</del> (**Broken Link**) addresses some of the issues, but not all.

The issues with most of the attempts so far seem to fall into two categories: presentation and functionality. Some examples:

# Presentation #

**Use the active look and feel**. How should the preferences of a look and feel be propagated into the component? There should also be API to allow overriding those settings when needed.

**Component Sizing**. What should the component do if the space available to it is larger than the space needed? For example, suppose the particular application needs only two columns of data. When given more space than needed, the component should enforce its space requirements. Otherwise you are likely to get bit ugly gray areas of no man's land like this:

[![Image of a Basic Data Grid with Empty Space](/static/img/2011-09-22-DataGrid.PNG "A Basic Data Grid with Empty Space") <br><small>A Basic Data Grid with Empty Space</small>](/static/img/2011-09-22-DataGrid.PNG)

And what's with the insets on the row labels?

# Functionality #

**Sorting**. Many of the attempts include sorting, but not always a useful type of sorting. Consider a set of data that consists of results from matched pairs of experiments, the control in the first column, the treatment in the second. If you sort on only one of those columns, you have broken the matching. It should be possible to "lock" columns together when sorting.

**Toolbar**. A data entry grid is not just a rectangular area for entering the data. It should also be associated with a useful toolbar to do numeric and text formatting. The usual undo, cut, copy, paste, and delete operations should be there. What should be in that toolbar and how should it be associated with the grid. Should it be possible to merge it into the toolbar used by the main program somehow? Should the grid have a save operation separate from the main program that saves analysis results? Interesting questions without obvious solutions.

**Data File Format**. Many of the different grid components let the user adjust the appearance of the grid, for example, changing the width of the column header to accommodate a long column title. Some allow changing the border appearance or text justification in a cell. How are those preference adjustments persisted? In many cases, not at all -- reloading the data file will require re-adjustment of the appearance. Full-blown spreadsheet programs like Excel persist those changes in the data file. I don't think the component should be created without also designing a persistent data format.

# What Do We Really Want? #

Well, Excel is often used as a model, but it isn't really adequate either. I think we want something like the data grids in statistical programs like [Statistica](http://www.statsoft.com/#) (Statistica has pretty much vanished through a seris of acquisitions), [MiniTab](http://www.minitab.com/en-US/default.aspx), and others. These are not spreadsheets in the Excel sense (though they have some ability to calculate the contents of some cells based on the values in others.) They are dedicated data entry/editing components. And it seems like every manufacturer has developed their own, all with incompatible data formats.

I think we need something inspired by JTable, but independent of it. But this is a hard problem. Look at how long it took the SwingLabs team to come up with the changes in JXTable.

# Why Am I Bringing this Up Now? #

I'm doing some clean up on some statistical tools that include unsatisfactory data grids in the interface. I'll put these up in public repositories and be embarrassed about the quality of data entry. (Basically, you have to load specially formatted CSV data files and can't really do much editing or saving from within the programs.)

It's just one of those continuing areas of dissatisfaction where I haven't been able to create or steal a good solution.