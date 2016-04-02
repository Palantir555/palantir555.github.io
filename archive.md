---
layout: page
title: Archive
---

## Embedded Systems

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "embedded" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.baseurl }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Systems Security

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "security" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.baseurl }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Software Development

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "software development" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.baseurl }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}
