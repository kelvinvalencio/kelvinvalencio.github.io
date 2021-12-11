---
layout: page
title: About Me
permalink: /about/
menu: main
---
<p align="center"><a href="url"><img src="{{ "/assets/1577711978477.jfif" | relative_url }}" height="auto" width="200" style="border-radius:50%"></a></p>  
<h2 align="center">Kelvin Valencio</h2>
<h3 align="center">Does Tech, Loves Learning</h3>  
<p align="center"><a href="https://www.linkedin.com/in/{{ site.linkedin_username| cgi_escape | escape }}"><svg class="svg-icon"><use xlink:href="{{ '/assets/minima-social-icons.svg#linkedin' | relative_url }}"></use></svg> <span class="username">More about me on LinkedIn</span></a></p>
```json
{
    "education":
    [
        {
            "name": "Universitas Bunda Mulia",
            "major": "Information Systems",
        },

        {
            "name": "Yayasan Musik Jakarta",
            "major": "Classical Music",
        }
    ],

    "skills":
    [
        "Front-End Web Development",
        "Linux Administration",
        "Object-Oriented Paradigm"
    ]
}
```
