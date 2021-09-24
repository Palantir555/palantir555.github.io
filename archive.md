---
layout: page
title: Archive
---

## Reverse Engineering

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "reverse engineering" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.url }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Embedded Systems

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "embedded" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.url }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Systems Security

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "security" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.url }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Software Development

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "software development" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.url }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}

## Privacy

{% for post in site.posts %}
  {% for tag in post.tags %}
    {% if tag == "privacy" %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ site.url }}{{ post.url }})
    {% break %}
    {% endif %}
  {% endfor %}
{% endfor %}
