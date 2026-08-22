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

I was a PhD student at UChicago, but I recently left to devote myself to building interesting things.

Before that, I graduated from the School of Gifted Young at USTC, where I majored in Math, ranked first in Probability and Statistics Track. I published first-author paper in top theoretical statistics journal Biometrika.

I love open-source projects during PhD, see my real-time RL project, [OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) ![](https://img.shields.io/github/stars/Gen-Verse/OpenClaw-RL?style=social) (also first open-source RL framework supports CLI/GUI/SWE/tool-use agents) and [RLAnything](https://github.com/Gen-Verse/Open-AgentRL) ![](https://img.shields.io/github/stars/Gen-Verse/Open-AgentRL?style=social), co-evolve RL for LLM coders [CURE](https://github.com/Gen-Verse/CURE) ![](https://img.shields.io/github/stars/Gen-Verse/CURE?style=social), and RL for diffusion large language models [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL) ![](https://img.shields.io/github/stars/Gen-Verse/dLLM-RL?style=social) (first open-source post-training framework for DLM). My research has been supported by ByteDance Seed, Thinking Machines and Fireworks AI.









<div class="publications" id="selected-projects">
  <div class="pubs-header">
    <h5 class="pubs-title">Agentic RL / Post-training</h5>
    <a href="#" class="pubs-toggle" data-action="toggle-full">View full</a>
  </div>

  {% bibliography -f papers %}
</div>

<script>
(function () {
  function setShowAll(block, showAll) {
    block.classList.toggle('show-all', showAll);

    var btn = block.querySelector('[data-action="toggle-full"]');
    if (btn) btn.textContent = showAll ? 'Show selected' : 'View full';

    // hide/show non-selected entries by hiding their <li>
    var rows = block.querySelectorAll('.row[data-selected="false"]');
    rows.forEach(function (row) {
      var li = row.closest('li');
      if (li) li.style.display = showAll ? '' : 'none';
    });
  }

  document.addEventListener('DOMContentLoaded', function () {
    var block = document.getElementById('selected-projects');
    if (!block) return;

    // default: selected only
    setShowAll(block, false);

    var btn = block.querySelector('[data-action="toggle-full"]');
    if (!btn) return;

    btn.addEventListener('click', function (e) {
      e.preventDefault();
      setShowAll(block, !block.classList.contains('show-all'));
    });
  });
})();
</script>

<div class="publications" id="stats-papers">
  <div class="pubs-header">
    <h5 class="pubs-title">Theoretical Statistics</h5>
  </div>

  {% bibliography -f stats %}
</div>

