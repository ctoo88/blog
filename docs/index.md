## 最新文章
{% for post in site.posts %}
  <ul>
    <li>
      {{ post.title }}
      <a href="{{ post.url }}">&nbsp;&nbsp;read more</a>
    </li>
  </ul>
{% endfor %}
