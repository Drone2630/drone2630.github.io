---
layout: post
title:  "Maquina Blue Hackthebox (metaesploit)"
summary: "Guia para resolver la maquina Blue de la plataforma de hackthebox"
author: drone
date: '2023-09-18'
category: ['hacking','metaesploit', 'windows']
tags: hacking
thumbnail: /assets/img/posts/code.jpg
keywords: hackthebox blue machine guide
usemathjax: false
permalink: /blog/adding-categories-tags-in-posts/
---

## Maquina Blue

Este es un walkthrough para la resolucion de la **Maquina Blue**. Es muy importente tener en cuenta la vulnerabilidad tan critica que presenta usar un **OS** con **Windows 7** si no se tienen presente los riesggos de no securitizar los posibles vectores de ataque.

La vulnerabilidad que se presenta en esta maquina es **MS17-010** que se explota mediante SMB
<!--To add categories in blog posts all you have to do is add a **category** key with category values in frontmatter of the post :-->

1.-Ping al objetivo, Por el **ttl** podemos darnos cuenta de que es una maquina windows.

  ![Ping](/assets/maquinas/blue/01.jpg)


Then to render this category using link and pages. All we need to do is,

1. Create a new file with [your_category_name].md inside categories folder.

2. Copy categories/sample_category.md file and replace the content in [your_category_name].md in that. (Please don't copy the code below its just sample, since it renders the jekyll syntax dynamically)

```jsx
---
layout: page
title: Guides
permalink: /blog/categories/your_category_name/
---

<h5> Posts by Category : {{ page.title }} </h5>

<div class="card">
{% for post in site.categories.your_category_name %}
 <li class="category-posts"><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</div>
```

Using the category, all the posts associated with the category will be listed on
`http://localhost:4000/blog/categories/your_category_name`