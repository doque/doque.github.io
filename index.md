---
layout: page
title: Recent Blog Posts
tagline: 
---
{% include JB/setup %}

<ul class="posts">
	{% unless site.posts != empty %}
		Nothing has been posted yet. Check back later!
	{% endunless %}

	{% for post in site.posts %}
		<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>