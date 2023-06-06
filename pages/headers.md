---
layout: page-fullwidth
subheadline: "Java Development"
title: "Guides in Java technologies!"
teaser: "The followings are my personal learning notes when studying Spring Family sets. The technologies includes: <em>Spring Framework, Mybatis, Maven, SpringBoot, Git</em>. The posts are taken from official sites of the technologies, and is just for reference and review. The latest version may be different from the contents of those posts."
header:
   image_fullwidth: "header_unsplash_5.jpg"
permalink: "/headers/"
---
<ul>
    {% for post in site.tags.java %}
    <li><a href="{{ site.url }}{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>