## Bienvenue sur NetDevOps.fr

Un site de partage de connaissances sur les sujets DevOps, Réseaux et automatisation des infrastructures IT.

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>