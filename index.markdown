---
layout: default
---
{% for post in site.posts limit:5 %}
<div>
    <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
{{ post.excerpt }}
<br>
</div>
{% endfor %}
