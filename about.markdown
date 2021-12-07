---
layout: page
title: About
permalink: /about/
menu: main
---
<p align="center"><a href="url"><img src="{{ "/assets/1577711978477.jfif" | relative_url }}" height="auto" width="200" style="border-radius:50%"></a></p>  
<h2 align="center">Kelvin Valencio</h2>
<h3 align="center">Does Tech, Loves Learning, Plays Violin & Piano</h3>  
<p align="center"><a href="https://www.linkedin.com/in/{{ site.linkedin_username| cgi_escape | escape }}"><svg class="svg-icon"><use xlink:href="{{ '/assets/minima-social-icons.svg#linkedin' | relative_url }}"></use></svg> <span class="username">{{ site.linkedin_username| escape }}</span></a></p>
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

    "experience": 
    [
        {
            "company": "Larona Prima Solusi",
            "position": "ERP Development & Support Team",
            "role": [
                "Developed UI/UX using HTML, JavaScript, jQuery, proprietary template",
                "Maintained Cloud Infrastructure using AWS and GCP",
                "Maintained clients database using MySQL"
            ]
        }
    ],

    "skills":
    [
        "Front-End Web Development",
        "Linux Administration",
        "Object-Oriented Paradigm",
        "Computer Security"
    ]
}
```