---
layout: default
title: Pages
permalink: /pages/
icon: list
type: page
---

<div class="page clearfix">
    <div class="left">
        <h1>All Pages</h1>
        <hr>
	<ul>
        {% for node in site.pages %}
	    {% if node.title != null %}
	        {% if page.url == node.url %}
        	    <li class="active"><a href="{{ BASE_PATH }}{{node.url}}" class="active">{{node.title}}</a></li>
		{% else %}
	            <li><a href="{{ BASE_PATH }}{{node.url}}">{{node.title}}</a></li>
	        {% endif %}
	    {% endif %}
	{% endfor %}
	</ul>
    </div>
</div>
<script src="{{ "/js/pageContent.js " | prepend: site.baseurl }}" charset="utf-8"></script>
