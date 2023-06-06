---
layout: page-fullwidth
title: "All About Cloud Native Technologies"
subheadline: "Containers and PaaS, KaaS, DevOps"
teaser: "Recently containerized application has become a trend in software industry, Continuous Integration (CI) and Continuous Deployment (CD) and Platform-as-a-Service (PaaS) have been adopted across the world. These post demonstrates the guides to those technologies including Docker, Kubernetes, Helm"
permalink: "/cloudnative/"
header:
    image_fullwidth: "header_drop.jpg"
---
<ul>
    {% for post in site.tags.cloudnative %}
    <li><a href="{{ site.url }}{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>