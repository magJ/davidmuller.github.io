## Posts

<ul>
  {% for post in site.posts %}
    <li>
      {{ post.date | date: "%m/%d/%Y" }} - <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
