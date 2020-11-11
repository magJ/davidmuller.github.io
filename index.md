### Posts

<ul>
  {% for post in site.posts %}
    <li>
      {{ post.date | date: "%m/%d/%Y" }} - <b><a href="{{ post.url }}">{{ post.title }}</a></b>
    </li>
  {% endfor %}
</ul>
