---
layout: page
title: "Talks"
permalink: /talks/
---

I sometimes give talks on a variety of subjects, this section contains descriptions of those talks, as well as the slides and videos when possible.

<ul class="posts">
    {% for post in site.categories.talks %}
        <li>
            <span class="post-date">{{ post.date | date: "%b %d, %Y" }}</span>
            :
            <a class="post-link" href="{{ post.url }}">{{ post.title }}</a>
        </li>
    {% endfor %}
</ul>
