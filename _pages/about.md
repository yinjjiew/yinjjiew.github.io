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

Before that, I graduated from the School of Gifted Young at USTC, ranking first in Statistics.

I love open-source projects during PhD. [OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) ![](https://img.shields.io/github/stars/Gen-Verse/OpenClaw-RL?style=social) is the first project to turn your agents' daily usage into real-time RL training. [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL) ![](https://img.shields.io/github/stars/Gen-Verse/dLLM-RL?style=social) is the first open-source post-training framework for DLMs. My research has been supported by ByteDance Seed, Thinking Machines, and Fireworks AI during my PhD.









<div class="publications" id="selected-projects">
  <div class="pubs-header">
    <h5 class="pubs-title">LLM & RL</h5>
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

