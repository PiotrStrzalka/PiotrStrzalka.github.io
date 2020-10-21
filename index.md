---
active: true
---
# Posts list
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> {{post.date | date_to_string}}
      {% if post.tags %} | 
      {% for tag in post.tags %}
          <a href="{{ site.baseurl }}{{ site.tag_page }}#{{ tag | slugify }}" class="post-tag">#{{ tag }}</a>
      {% endfor %}
      {% endif %}
      {{ post.excerpt  }}
      <a href="{{ post.url }}">Read further</a>
      <br>
      <br>
      <br>
    </li>
  {% endfor %}
</ul>