---
layout: about
title: about
permalink: /
profile:
  align: right
  image: photo.png
  image_circular: false # crops the image to make it circular
  image_width: 140px
  address: >

news: false  # includes a list of news items
selected_papers: false # includes a list of papers marked as "selected={true}"
social: false  # includes social icons at the bottom of the page
---

##### About Me

I am a Phd student in UChicago. My research interest is artificial intelligence, across language models, computer-use agents and their reinforcement learning methods.

Before that, I graduated from the School of the Gifted Young at USTC, where I majored in Mathematics.

I prefer fully open-sourced AI projects, see my RL frameworks for diffusion language models [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL), LLM coders [CURE](https://github.com/Gen-Verse/CURE), and agentic AI [RLAnything](https://github.com/Gen-Verse/Open-AgentRL).

##### Selected Projects

<div class="publications">
{%- assign years = "2026,2025" | split: "," -%}
{%- for y in years %}
  <h2 class="year">{{ y }}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{%- endfor %}
</div>
