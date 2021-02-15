---
active: true
---

<div class="row">
  <div class="column left">
    <h1> Posts list:</h1>
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
  </div>
  <div class="column right" style="background-color:#DEF2F1;">
      <h2>Tags</h2>
      <ul class="ul-no-indent">
      {% assign sorted_tags = site.tags | sort %}
      {% for tag in sorted_tags %}
        <li><strong><a href="{{ site.baseurl }}{{ site.tag_page }}#{{tag[0]}}" class="post-tag">#{{ tag[0] }}</a></strong></li>
        
      {% endfor %}

      </ul>

  </div>
</div>
