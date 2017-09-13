---
layout: page
permalink: ./index.html
---

<h2>Latest Posts</h2>
<ul class="posts cli-bullets">
    {% for post in site.categories.posts %}
        <li>
            <a class="reserved" href="{{ post.url }}">{{ post.title }}</a>
        </li>
    {% endfor %}
</ul>

