# documentation-theme-jekyll-multiple_language_pop

proof of principle adding i18n support to (samvera.github.io) documentation-theme-jekyll via jekyll-multiple-languages-plugin

Using parts from https://github.com/samvera/samvera.github.io and the https://github.com/Anthony-Gaudino/jekyll-multiple-languages-plugin 

The i18n integration is via "_plugins" using multiple-languages submodule. The translations are implemented as being described in  https://github.com/Anthony-Gaudino/jekyll-multiple-languages-plugin/blob/master/README.md 

## Missing (Todo)
- TOC "Table of contence" via bootstrap css 
- a-z index sort 
  - sort must run over translated tags, titles and pages. At the current stage  https://github.com/jmfrenzel/documentation-theme-jekyll-multiple_language_pop/commit/fdbc77ccc8f71ab9842972cb615455bb2205fb2f the i18n yaml variable names are sorted. 


------
...continue with the org. Readme 
## Our Theme

We use a Jekyll theme designed for [documentation](https://github.com/tomjohnson1492/documentation-theme-jekyll). We chose the Documentation Theme because of its excellent navigation and clear page layout, and for the ease of working in markdown.
<!--
[![Build Status]()

-->

## How Does This Work?

We use a Jekyll-based custom theme for markup and display, and pages are published to http://www.rub.de/researchdata/??? .

## Adding or Editing Content

These are community documents, so we rely on the pull request model. If you'd like to contribute content:

- clone this project   

(link on [FDM/ag-fdm.io](http://git.it-services.rub.de/FDM/ag-fdm.io)) 

- make a branch for your new documentation
- run `bundle install`
- create/edit pages within the agfdm directory (e.g. [/pages/agfdm/](https://git.it-services.ruhr-uni-bochum.de/FDM/ag-fdm.io/tree/master/pages/agfdm))
- add/update [front matter](#basic-front-matter)
- add links to the page in home_sidebar.yml
- write content ([notes on writing content](#notes-on-writing-content))
- [add versioning information](#versioning-information) for the gem version the document page describes
- [generate the A-Z Index](#generate-the-a-z-index-page), if needed
- submit a Pull Request

To Test in Jekyll:

* Run the jekyll server

```
bundle exec jekyll serve
```

* View the documentation in a browser at http://localhost:4000

### Basic Front Matter

<!--
Example front matter for page [Best Practices -> Coding Styles]()
```
-->

---
title: "Coding Style"
permalink: best-practices-coding-styles.html
folder: agfdm/how-to/best_practices/coding_styles.md
sidebar: home_sidebar
a-z: ['Coding Styles', 'Markdown']
keywords: Best Practices
tags: [development_resources]
categories: How to Do All the Things
version:
  id: 'fdm_io_1.0-stable'
---
```
where,
* **title** [String] - _(required)__ - the title displayed on the generated html page
* **permalink** [text] - _(required)_ - the name of the generated html file that will be part of the url accessed by users
* **folder** [text] - _(required)_ - the location of this *.md file under the pages directory
* **sidebar** [text] - _(required)_ - value is always `home_sidebar` at this point
* **a-z** [Array<Strings>] - _(recommended)_ - array of terms to link to this page in the A-Z Index
* **keywords** [comma separated list] - _(recommended)_ - keywords that get populated into the metadata of the page for SEO
* **tags** [Array<text>] - _(recommended)_ - array of tags where tags must be defined in your [_data/tags.yml](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/_data/tags.yml) list. You also need a corresponding tag file inside the [pages/tags](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/pages/tags) folder that follows the same pattern as the other tag files shown in the tags folder.  It is rare that a new tag would be created.  If you think you need a new tag, be sure to highlight it as requiring review in the issue.
* **categories** [text] - _(recommended)_ - represents the 4 major headings under which a page can reside
* **version** [hash] - _(recommended)_ - specifies the gem's name and version that this documentation page describes.  See [Versioning Information](#versioning-information) for details.

### Notes on writing content

You can highlight content with the following...

```
<ul class='info'><li>This shows an info icon and provides the user additional information in a blue box.</li></ul>
```
![info box](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/assets/images/readme_documentation/info_box.jpg "Info Box")
```
<ul class='warning'><li>This shows a warning icon and provides the user warning information in a red box.</li></ul>
```
![warning box](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/assets/images/readme_documentation/warning_box.jpg "Warning Box")

```
<ul class='question'><li>This shows a question icon and provides the user with information in a yellow box indicating that there may be some uncertainty about a particular piece of information in the documentation.</li></ul>
```
![question box](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/assets/images/readme_documentation/question_box.jpg "Question Box")


NOTE: You cannot use markdown in these boxes.  For example, if you want a link, you will have to use an html anchor tag.

### Versioning-information

The documentation on this site covers multiple gems and multiple versions of those gems.  The recommended best practice is to specify the gem's name and version, including a link to the applicable branch in github.  If there is documentation for multiple versions of the gem, then a selection list allows users to switch between versions.

#### Specifying gem name, version, and branch

##### Predefined version information

Common gems have version information encoded and can be referenced by id in front matter.  The predefined version information is defined in [_includes/version_info/](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/_includes/version_info)

Example for fdm_io v1.0 stable:
```
version:
  id: 'fdm_io_1.0-stable'
```

##### Custom version information

If the predefined version information does not exist.  You can specify custom values in the front matter.

Example custom version information:
```
version:
  label: 'AG FDM Management v0.2.2'
  branch:
    label: 'master'
    link: 'https://git.it-services.rub.de/FDM/ag-fdm.io/FDM/projectmanagement'
```

##### Adding predefined version information for a gem version

It is good practice to add predefined version information, as this allows reuse and consistent encoding of gem names and versions.  

Adding predefined version information is a two step process.

1. Add the version information to a new version file in directory [_includes/version_info/](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/_includes/version_info) naming the file with the gem and verion number (e.g. version_fdm_io_1.0-stable.html).  Include in the new file assignments for version_label, branch_label, and branch_link.

Example assignments for fdm_io v1.0:
```
{% assign version_label = 'fdm_io stable v1.0' %}
{% assign branch_label = '1.0-stable' %}
{% assign branch_link = 'https://git.it-services.rub.de/FDM/ag-fdm.io/FDM/projectmanagement/tree/1-0-stable' %}
```

2. Add a new condition in [_includes/versions.html](https://git.it-services.rub.de/FDM/ag-fdm.io/tree/master/_includes/versions.html) that includes the predefined version information if its id is specified in front matter for a document.  The condition in the case statement becomes the id that is used in front matter.

Example conditional include based on id:
```
{% case page.version.id %}
{% when 'fdm_io_1.0-stable' %}
  {% include version_info/version_fdm_io_1.0-stable.html %}
...
{% endcase %}
```

#### Selector for switching versions

Identifying multiple versions is done by specifying each version in front matter including its label and link.  If the version is the active version that the current page is describing, then also include `selected: 'true'`.  The multi-version information is repeated on every version's page for the documentation topic.

Example front matter for multiple versions:
```
version:
  versions:  
    - label: 'fdm_io 1.0'
      link:  'what-happens-deposit-1.0.html'
      selected: 'true'
    - label: 'fdm_io 2.0'
      link:  'what-happens-deposit-2.0.html'  
```

### Generate the A-Z Index page

```
bundle exec jekyll build
mv generated/atoz.md pages
```
Then commit pages/atoz.md to github

