---
layout: about
title: about
permalink: /
profile:
  align: right
  image: photo.png
  image_circular: false # crops the image to make it circular
  address: >

news: false  # includes a list of news items
selected_papers: false # includes a list of papers marked as "selected={true}"
social: false  # includes social icons at the bottom of the page
---

I am a Statistics Phd student in UChicago. My research interest turns to AI this year, across reinforcement learning, diffusion language models, LLMs, and GUI agents.

Before that, I graduated from the School of the Gifted Young at USTC, where I majored in Mathematics.

## Selected Publications

<div class="publications">
{%- assign years = "2025" | split: "," -%}
{%- for y in years %}
  <h2 class="year">{{ y }}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{%- endfor %}
</div>
