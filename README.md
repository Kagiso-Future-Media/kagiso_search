# kagiso_search
Postgres fulltext search for Wagtail

## Installation
`pip install kagiso_search`

Add `kagiso_search` to your `INSTALLED_APPS` in your `settings.py`.

`python manage.py migrate`

Add the following to `urls.py`:
```py
from kagiso_search.views import search as search_view

url(r'^search/', search_view, name='search'),
```
Make sure that there is a search template at
`kagiso_search/search_results.html` in your templates folder
(see a sample below).

Add the following required configuration settings:

`ITEMS_PER_PAGE` (The number of results per page in the pager)

## Sample 404 Page (from our CMS)
```html
{% extends 'core/base.html' %}

{% load staticfiles %}
{% load wagtailimages_tags %}
{% load wagtailcore_tags %}

{% block title %}Search{% endblock %}

{% block content %}
  <div class="row">
    <div class="col-sm-8 col-left pad-top">
      <div class="block-title-sm bg-pink-on-white margin-bot-lg">
        <h4>Search results:</h4>
      </div>

      <form action="{% url 'search' %}" method="get"  class="form-horizontal">
        <div class="form-group">
          <div class="col-xs-10">
            <input type="text"
              name="query"
              class="form-control"
              value="{{ search_query|default_if_none:'' }}"
              placeholder="Search for:">
          </div>
          <div class="col-xs-2">
            <button class="btn btn-primary btn-block">Go</button>
          </div>
        </div>
      </form>

      {% if search_results %}
        <div class="colour-grey-lighter margin-bot-md">
          {{ search_results.paginator.count }} result{{ search_results.paginator.count|pluralize }} found
        </div>
        <ul class="card-items cards-wide margin-bot-xxl">
          {% for result in search_results %}
            <li class="card card-wide card-no-thumb">
              <a href="{% pageurl result %}">
                {% if result.cover_image %}
                  {% image result.cover_image width-140 %}
                {% else %}
                  {% include 'core/includes/placeholder_thumbnail.html' %}
                {% endif %}
                <div class="title">
                  <h6>{{ result.headline|richtext }}</h6>
                  {% if result.first_published_at %}
                    <div class="date">{{ result.first_published_at|date:'d F Y' }}</div>
                  {% endif %}
                  {% if result.summary %}
                    <div class="excerpt">
                      {{ result.summary|richtext }}
                    </div>
                  {% endif %}
                </div>
              </a>
            </li>
          {% endfor %}
        </ul>
      {% elif search_query %}
        No results found
      {% else %}
        Please type something into the search box
      {% endif %}

      <nav>
        <ul class="pager">
          {% if search_results.paginator.num_pages %}
            <li><a href="{% url 'search' %}">â€¹â€¹ First</a></li>
          {% else %}
            <li class="disabled"><a>â€¹â€¹ First</a></li>
          {% endif %}

          {% if search_results.has_previous %}
            <li>
              <a href="{% url 'search' %}?query={{ search_query }}
                &page={{ search_results.previous_page_number }}">
                â€¹ Previous
              </a>
            </li>
          {% else %}
            <li class="disabled"><a>â€¹ Previous</a></li>
          {% endif %}

          {% if search_results.has_next %}
            <li>
              <a href="{% url 'search' %}?query={{ search_query }}
                &page={{ search_results.next_page_number }}">
                Next â€º
              </a>
            </li>
          {% else %}
            <li class="disabled"><a>Next â€º</a></li>
          {% endif %}

          {% if search_results.paginator.count > 0 %}
            <li>
              <a href="{% url 'search' %}?query={{ search_query }}
                &page={{ search_results.paginator.num_pages }}">
                Last â€ºâ€º
              </a>
            </li>
          {% else %}
            <li class="disabled"><a>Last â€ºâ€º</a></li>
          {% endif %}
        </ul>
      </nav>

    </div>
    <div class="col-sm-4 col-right sidebar">
      {% include 'core/includes/sidebar.html' %}
    </div>
  </div>
{% endblock %}

```

## Running the tests
```
py.test
```
