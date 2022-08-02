## Bienvenue sur NetDevOps.fr

Un site de partage de connaissances sur les sujets DevOps, Réseaux et automatisation des infrastructures IT.

## Catégories :

<ul>
    {% for category in site.data.categories %}
    <li>
        <a href="/category/{{ category.file | slugify }}">
            {{ category.name }}
        </a>
    </li>
    {% endfor %}
</ul>