---
title: Getting Things Done using ZIM Wiki
description: How to Get Things Done using ZIM Wiki
draft: false
tags:
  - getting-things-done
  - zim-wiki
  - guide
date: 2024-02-01
---
## Introduction 

Staying productive AND at ease in busy times is hard. [Getting Things Done](https://gettingthingsdone.com/) (GTD) is a great method to organise your thoughts, notes and lists and will enable you to create that ease and to be in control. It's not about doing more, but doing what you need to do at the right time in a stress-free manner.

You can support this method using any tool: a physical notepad and pen, online applications like [Evernote](https://evernote.com/), desktop applications like OneNote or [Obsidian](https://obsidian.md) and several more. I've found that these tools are often too complicated or over-powered for the task we're aiming at. The tool should not get in the way of the work: a simple notepad with a few additions should do the trick.

> [!NOTE]
> Since writing this post some time ago I *did* switch to Obsidian for my GTD workflow. I will write a post about that in the future.

Several years ago I found a great multi-platform note-taking application called [Zim](https://zim-wiki.org/). It includes plugins to work with lists (the root of GTD), tags, dates and much more. The help file even [includes a section](https://zim-wiki.org/manual/Usage/Getting_Things_Done.html) on the GTD method. It explains much of the setup. In this post however, I will talk about how I expanded this setup a little bit, and I will talk about usage in everyday life.

As Zim is not specifically created as a GTD tool it will require some setup steps and discipline to use Zim for GTD. So this is a bit of a technical explanation but I hope it will also help you with the challenges on HOW to support GTD to your daily life, and keeping your mind at ease.

> [!NOTE]
> This article assumes you already know about GTD and how it works.

Also, if you are already using Zim for GTD, then I am very interested in hearing how you do it. Let me know in the comments. Thanks!

## Zim Setup  

First we need to get the technical bits taken care of. We need to configure Zim and a few plugins. We will use the Task List and Journal plugins. These are included in the main download, which you can find [here](https://zim-wiki.org/downloads.html). At this moment in time the Zim version is `0.75.2`.  The dialog and options might look a little bit different when you use an older, or maybe newer version of Zim. Also, I am using Zim on Linux Mint which shows in my screenshots. 

### Task List plugin setup

The task list will be the most important part of our daily work, so let's get that set up. Go to **Edit / Preferences** and select the **Plugins** tab in the window. Scroll down to the **Task List** plugin and ensure that it is enabled. Select it and then click on **Configure**.

Enable these settings:

- Show tasklist button in headerbar
- Show "GTD-style" inbox & next actions lists
- Show due date
- Show start date
- Show page column in the sidepane  


![[task-plugin-settings1.png]]


I'm not using the _Show task in sidepane_ option, as I don't like what it is doing to the pane layout. Press **OK**, and then select **Properties**.

Enable these settings:

- Consider all check-boxes as tasks
- Show page names in selection pane
- Don't count Saturday and Sunday as working days

_Section(s) to index_ is a text field and should contain: Inbox,Projects,Single Actions,Meeting Notes


![[task-plugin-setting-2.png]]


I'm not using labels, but it is fine to leave the **Label** fields as-is. Click **OK**.

### Journal plugin setup  

We use the _Journal_ plugin to make meeting notes. I use a page per week, so this is selected in the Journal plugin under **Edit / Preferences**. I also change the **Section** name to **Meeting Notes**.


![[content/images/zim/journal.png]]


Click **OK**.  

### Project Template

Defining your work is important, and a project should explain what it is about and what the intended outcome is. GTD calls this outcome focused. So, ideally this should be included in each project page. For this we create a project page template which can be selected when creating a new sub-page in the _Projects_ or _Someday_ section. We're going to copy an existing template to suit our purposes: select **Edit / Templates**. Scroll down to the **wiki** templates, select the **Default** template and then click on **Copy**.  Give it the name _Project_. 

Click on **Edit** and a text editor will start. Add the description and outcome lines to the template and save it:

```md
======= [% page.basename %] =======  
[% gettext("Created") %] [% strftime("%A %d %B %Y") %]  
  
===== Description =====  
Describe the project and the question behind it (why?).  
  
===== Outcome =====  
Describe the intended outcome of the project.
```


When creating a new project we can select this new template to remind us to define the work.

## Notebook Setup  

The Zim global settings are now taken care of. Let's get our GTD notebook set up.

### Create a new notebook

Create a new notebook and add the following pages in the root of the notebook, using `Ctrl-N`, or **File / New Page** for each page:

- Archive
- Inbox
- Projects
- Reference Notes 
- Single Actions
- Someday

These pages will act as sections in the notebook. The sections to index for tasks have been specified in the task-list plugin setting earlier.

### Setting Notebook Properties

The Inbox page is the 'home' of the notebook, so let's define that. Go into **File / Properties** and select the Notebook option.

_Home Page_ is a text field and should contain :Inbox. This will allow you to press Alt-HOME to return to the Inbox page instantaneously 

I also select **Prefer short names for page links** to keep link text cleaner.


![[content/images/zim/notebook_props.png]]


### Action Statuses  

The technical setup is now done: GTD style action lists will now generate automatically but it's important to understand the action statuses, how to mark them in Zim and why they show up in which list. According to GTD, an action can have five statuses:

- Undefined
- Postponed
- Planned
- Delegated
- Actionable

Here is how we define these statuses in Zim:


|Action Status|Start Date|End Date|Specific Tag|Appears In task-list|Appear when|
|---|---|---|---|---|---|
|Undefined|None|None||Inbox|Now|
|Postponed|Future date|Optional||Next Actions|After start date|
|Planned|Future date|Optional||Next Actions|After start date|
|Delegated|Date of delegation|Optional|waiting|Waiting|Now, as start date is in the past|
|Project Task|Optional|Optional||- Next Actions  <br>- Projects|- Now, or after start date.  <br>- Now, greyed out if start date is not yet reached.|


> [!NOTE]
> *Actionable* in itself is not a status in the table, but is a result of several parameters. Any action that shows up in the **Next Actions** list is therefore Actionable according to GTD.  

I added *Project Task* because it is a special case as it can appear in two lists, **Next Actions** and **Projects**. A project task is any sub-item in a checkbox list and, even with no start/end date or priority defined, it will NOT appear in the **Inbox** list, but in the **Next Actions** list. The fact that it is a sub-item means that it is defined. Of course, a project task can also be postponed, planned or delegated, but it is never undefined.

Also important to remember is that the Inbox *page* (section) in the notebook is different from the Inbox *list* in the task-list plugin. ANY task in any indexed section which has no date and priority and is not part of a project will show up in the Inbox *list*. This is incredibly powerful, and to make it work you need to know how to classify the tasks properly so that they show up where and when you expect them.

## GTD Workflow

Here I would like to describe how I work with this Zim setup in my day-to-day routine.

### Catch

I can add actions to every section that is indexed (see task-list plugin setup). For now it's important that the action status stays Undefined, although it is fine to add tags or any other meta-date you prefer. I normally add new actions to the Inbox page.  

In meetings, I usually add Undefined actions to the Journal weekly page, or to the project page if it is already created. Having the action in a meeting note helps with context, which is often lost after it becomes a one-liner on a list.  

Also, do not accidentally create a project list by adding sub-items to a checkbox list while catching actions. This will result in them not showing up in the inbox list.

Tip: if the action has a link with meeting notes, you can press Ctrl-D and select the **Link to Date** option in the date requester that pops up. This will convert the pasted date into a link to the Journal page containing meeting notes for that week. Also, make sure to select the **year-month-day** notation (the final item in the Format list) to ensure that all dates are compatible with the start/enddate format of Zim.


![[date format.png]]


### Process / Define  

Select the Inbox **list** (not the page) from the task-list plugin and process each item you see there. Determine the next action and perform it if it takes less than two minutes to complete. If not, move the action on the appropriate list and give it a status (postpone, plan, delegate) as described in the table above.

This Zim [help page](https://zim-wiki.org/manual/Usage/Getting_Things_Done.html) contains a great example flow.  

When I delegate a task to someone I add a name-tag (if relevant) and the `@waiting` tag. I also press `Ctrl-D` to add the current date, so I will know when I started waiting.

If the action belongs to an already existing project, I move the action to that page, as a sub-item in the project list. If the action is part of a new project, I create a new project sub-page, define the work and start adding the actions to that page. Depending on my current workload I move the project to the _Someday_ section for consideration in the Weekly Review.  

### Doing it!  

Early in the day I'm using my work calendar to see what I have got planned for that day. I also look at my **Next Actions** list to mark any actions I want to perform this day with a Prio (adding a ! in the action) to make them stand out in the list.

In between planned work I select the **Next Actions** list and see which are marked (sorting by due date also is an option) and perform those actions looking at the time available and my current energy level.  

### Resolving Actions

I check the checkbox and let the action remain on the page. This is for later reference, if needed.  

### Meetings

This is the workflow I use when performing meetings. The idea behind it is that adding actions and comments about the meeting inside the action and project pages will make things harder to find in the long run, so I separate them by making use of the Journal plugin.

Note: always be aware that meeting notes can be confidential. Using Zim for meeting notes in this case can be an issue if you sync your notebook to a cloud service.

#### Preparing for the meeting  

This depends on the type of meeting. If the meeting has no project focus then I select the person's tag name in the tasklist **Tags** section and see which topics pop-up. Using a dedicated page for each person is also an option but I personally find name-tags to be more powerful as these can come from any indexed section in the notebook.

For project meetings, I select the project page and see what now needs attention. Marking meeting notes with a project name tag helps with Searching to review meeting notes before the meeting.  

#### Making Meeting Notes  

Pressing `Alt-D` will send me to the Journal page for this week. Go to the correct day, add a header for this meeting and start making notes. I use a standard format for the header, and I have this available as a template in the **Reference Notes** section of the notebook:  

```
Meeting: @project_name

Present:
 
Subject:
 
Purpose:
 
Notes:
 
Actions:
```

I try to keep actions Undefined (see table) which will make them show up in the **Inbox** task-list for easy process after the meeting. Adding a tag for the project name will make it available for notebook Search (`Ctrl-Shift-F`).  Add more tags if the meeting has multiple subjects.  

#### After a Meeting

Perform the process steps as described above on actions added during the meeting. They should show up in the **Inbox** task-list.

### Weekly Review  

An essential part of GTD is the *Weekly Review*. Here you make sure that nothing has fallen through the cracks during the week, and we clean up current lists and tasks.  I combine this with my other weekly tasks, like booking my worked hours, making declarations and such. Boring, but it has to be done.

I have a sub-page under the **Reference Notes** section with a checklist to remind me of the things I need to do in the review. The review is planned every Friday afternoon but I also do parts of this process during downtime in the work week.

## Closing Thoughts

It has been a long article but I hope you liked what I was trying to explain to you. Zim works for me, it might not for you, but the principles should be the same for any tool that you choose to use to support GTD. The power of dynamic lists and the efficient editing and notation that Zim enables makes it work for me.

My motto using Zim: **Live by the List, Not the Page.** By this I mean: use the power of the dynamic lists to find the action that is most suited to perform now: don't endlessly browse the pages looking for tasks.

No tool and setup is perfect, but I find this one to be really good.
