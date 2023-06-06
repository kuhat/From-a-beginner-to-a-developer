---
layout: page-fullwidth
show_meta: true
title: "Web Developement"
subheadline: "Popular Frontend and Backend Frameworks"
teaser: "Frontend and backend development are essential building blocks of web system, this categories contains popular tools and frameworks used in web development: Django, React."
header:
   image_fullwidth: "header_unsplash_7.jpg"
permalink: "/webdev/"
---
<ul>
    {% for post in site.categories.design %}
    <li><a href="{{ site.url }}{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>