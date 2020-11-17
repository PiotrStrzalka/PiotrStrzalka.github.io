---
active: true
---
# Posts list:
<ul>
  {% for post in site.posts %}
    <li>
      {% assign p_start = post.content | split: '<p>' %}
      <h2><a href="{{ post.url }}">{{ p_start[0] | strip_html}}</a></h2> {{post.date | date_to_string}} 
      {% if post.tags %} | 
      {% for tag in post.tags %}
          <a href="{{ site.baseurl }}{{ site.tag_page }}#{{tag}}" class="post-tag">#{{ tag }}</a>
          <!-- #{{ tag | slugify }} -->
      {% endfor %}
      {% endif %}
      <hr/>
      
      {% assign p_end = p_start[1] | split: '</p>' %}
      {{ p_end[0] | truncatewords: 50, "... " }}
      


      <!-- {{ post.content | strip_html | truncatewords: 50, "..." | remove_first: "VESC - cost sensitive solution" }} -->
      <a href="{{ post.url }}">Read further</a>
      <br>
      <br>
      <br>
    </li>
  {% endfor %}
</ul>