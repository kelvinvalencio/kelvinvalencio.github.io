---
layout: page
title: About Me
permalink: /about/
menu: main
---
<p align="center"><a href="url"><img src="{{ "/assets/1577711978477.jfif" | relative_url }}" height="auto" width="200" style="border-radius:50%"></a></p>  
<h2 align="center">Kelvin Valencio</h2>
<h4>Tech enthusiast with several years of experience running MSP infrastructure for banking clients (GRC), a good foundation in web development & security, and an insatiable appetite for learning</h4>  
<p align="center"><a href="https://www.linkedin.com/in/{{ site.linkedin_username| cgi_escape | escape }}"><svg class="svg-icon"><use xlink:href="{{ '/assets/minima-social-icons.svg#linkedin' | relative_url }}"></use></svg> <span class="username">More about me</span></a></p>
```yaml
person:
  name: Kelvin Valencio
  role: 
    - Linux Engineer
    - DevOps Engineer
    - SysOps Engineer
  location: Tangerang Selatan, Banten, Indonesia

experiences:
  - title: Linux System Engineer
    company: Hostinger
    period: { from: 2026-02, to: Present }

  - title: System Operations & Administrator
    company: Fortress Digital Services
    period: { from: 2023-08, to: 2026-02 }

  - title: IT Development & Support
    company: Larona Prima Solusi
    period: { from: 2019-07, to: 2021-03 }

education:
  - institution: Universitas Bunda Mulia
    major: Information Systems
    graduated: 2022-08
    gpa: 3.47/4.0

skills:
  cloud_platforms:
    - AWS
    - Alibaba Cloud
    - BytePlus Cloud
    - Huawei Cloud
  iaas_and_devops:
    - Terraform
    - Ansible
    - Kubernetes
    - Docker
    - Jenkins
  monitoring_logging:
    - Zabbix
    - Grafana
    - Loki
  scripting_languages:
    - Python
    - Shell Scripting
  web_servers_vpn:
    - Nginx
    - Apache
    - Firezone
```
