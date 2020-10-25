## Posts

<ul>
  {% for post in site.posts %}
    <li>
      <small>{{ post.date | date: "%m/%d/%Y" }}</small> - <b><a href="{{ post.url }}">{{ post.title }}</a></b>
    </li>
  {% endfor %}
</ul>
