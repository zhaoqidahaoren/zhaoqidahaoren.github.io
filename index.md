---
title: titles.home
a-z: [atoz.home]
keywords: index.keywords
tags: [tags.getting_started]
sidebar: home_sidebar
permalink: index.html
toc: false
---

<div class="row">
   <div class="col-lg-12">
      <h2 class="page-header">{% t index.title %}</h2>

      <a href="https://git.it-services.ruhr-uni-bochum.de/FDM/ag-fdm.io" class="btn btn-lg btn-primary" target="_blank">{% t index.contribute %} </a>

{% capture page_note %}{% t index.description %}{% endcapture %}
{% include note.html content=page_note %}

   </div>
   <div class="col-md-3 col-sm-6">
       <div class="panel panel-default text-center">
           <div class="panel-heading">
               <span class="fa-stack fa-5x">
                     <i class="fa fa-circle fa-stack-2x text-primary"></i>
                     <i class="fa fa-lightbulb-o fa-stack-1x fa-inverse"></i>
               </span>
           </div>
           <div class="panel-body">
               <h4>{% t index.new_start_here.title %}</h4>
               <p>{% t index.new_start_here.description %}</p>
               <a href="tag_getting_started.html" class="btn btn-primary">{% t index.learn %}</a>
           </div>
       </div>
   </div>
   <div class="col-md-3 col-sm-6">
       <div class="panel panel-default text-center">
           <div class="panel-heading">
               <span class="fa-stack fa-5x">
                     <i class="fa fa-circle fa-stack-2x text-primary"></i>
                     <i class="fa fa-thumbs-o-up fa-stack-1x fa-inverse"></i>
               </span>
           </div>
           <div class="panel-body">
               <h4>{% t index.resources.title %}</h4>
               <p>{% t index.resources.description %}</p>
               <a href="tag_development_resources.html" class="btn btn-primary">{% t index.learn %}</a>
           </div>
       </div>
   </div>
   <div class="col-md-3 col-sm-6">
       <div class="panel panel-default text-center">
           <div class="panel-heading">
               <span class="fa-stack fa-5x">
                     <i class="fa fa-circle fa-stack-2x text-primary"></i>
                     <i class="fa fa-cogs fa-stack-1x fa-inverse"></i>
               </span>
           </div>
           <div class="panel-body">
               <h4> {% t index.products.title %}</h4>
               <p> {% t index.products.description %}</p>
               <a href="tag_running_in_production.html" class="btn btn-primary">{% t index.learn %}</a>
           </div>
       </div>
   </div>
   <div class="col-md-3 col-sm-6">
       <div class="panel panel-default text-center">
           <div class="panel-heading">
               <span class="fa-stack fa-5x">
                     <i class="fa fa-circle fa-stack-2x text-primary"></i>
                     <i class="fa fa-code-fork fa-stack-1x fa-inverse"></i>
               </span>
           </div>
           <div class="panel-body">
               <h4> {% t index.community.title %}</h4>
               <p>{% t index.community.description %}</p>
               <a href="tag_community.html" class="btn btn-primary">{% t index.learn %}</a>
           </div>
       </div>
   </div>
</div>

{% include links.html %}
