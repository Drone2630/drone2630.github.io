---
layout: post
title:  "Maquina Blue Hackthebox (metaesploit)"
summary: "Guia para resolver la maquina Blue de la plataforma de hackthebox"
author: drone
date: '2023-09-18'
category: ['hacking','metaesploit', 'windows']
tags: hacking
thumbnail:
keywords: hackthebox blue machine guide
usemathjax: false
permalink: /blog/adding-categories-tags-in-posts/
---

## Maquina Blue

Este es un walkthrough para la resolución de la **Maquina Blue**. Es muy importante tener en cuenta la vulnerabilidad tan crítica que presenta usar un **OS** con **Windows 7** si no se tienen presente los riesgos de no securitizar los posibles vectores de ataque.


La vulnerabilidad que se presenta en esta máquina es **MS17-010** que se explota mediante una debilidad en el protocolo **SMBv1**. es un protocolo de red utilizado para compartir archivos e impresoras en una red local del cual hace usao windows.


1.-Ping al objetivo, Por el **ttl** podemos darnos cuenta de que es una máquina windows.

  ![Ping](/assets/maquinas/01Blue.png)

  ```jsx
ping 10.10.10.40
```

2.-Escaneamos e identificamos todos los puertos abiertos del objetivo.

  ![Ping](/assets/maquinas/02Blue.png)

  ```jsx
nmap -p- --open --min-rate 5000 -vvv -n -Pn 10.10.10.40
```

3.-Identificamos los servicios que están corriendo en los puertos abiertos.

  ![Ping](/assets/maquinas/03Blue.png)

  ```jsx
nmap -sCV -p135,139,445,49152,49153,49156 10.10.10.40
```

4.-Vemos que el puerto **445** está expuesto, generalmente los servicios **SMB** se ejecutan en este puerto. Procederemos a analizar el servicio con una herramienta llamada **crackmapeexec** para obtener información más detallada del sistema objetivo. Podemos ver que es un sistema **Windows 7 de 64bits** y que está corriendo el servicio **SMBv1**.

<div style="width: 50%;">
    ![Ping](/assets/maquinas/04Blue.png)
</div>

  ```jsx
crackmapexec smb 10.10.10.40
```

5.-Con **nmap** procederemos a buscar vulnerabilidades sobre el puerto 445. Como podemos ver en el resultado del análisis el equipo objetivo es vulnerable al **ms17-10** del servicio **SMBv1** que nos permite hacer una ejecución remota de comandos.

  ![Ping](/assets/maquinas/05Blue.png)

  ```jsx
nmap --script "vuln and safe" -p445 10.10.10.40
```


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