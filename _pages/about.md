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

I am a PhD student at the University of Chicago, working with Professor [Victor Veitch](https://www.victorveitch.com/) and Professor [Mengdi Wang](https://ece.princeton.edu/people/mengdi-wang). I currently focus on language models, agents, and reinforcement learning methods for them, while collaborating closely with [Seed Pretrain Lab](https://seed.bytedance.com/en/) and [Princeton AI Lab](https://ai.princeton.edu/ai-lab).

Before that, I graduated from the School of the Gifted Young at USTC, where I majored in Mathematics. I published first-author paper in top theoretical statistics journal Biometrika.

I like to open-source my projects, see my RL frameworks for agentic AI, [OpenClaw-RL](https://github.com/Gen-Verse/OpenClaw-RL) ![](https://img.shields.io/github/stars/Gen-Verse/OpenClaw-RL?style=social) (first open-source RL framework supports CLI/GUI/SWE/tool-use agents) and [RLAnything](https://github.com/Gen-Verse/Open-AgentRL) ![](https://img.shields.io/github/stars/Gen-Verse/Open-AgentRL?style=social), LLM coders [CURE](https://github.com/Gen-Verse/CURE) ![](https://img.shields.io/github/stars/Gen-Verse/CURE?style=social), and diffusion large language models [dLLM-RL](https://github.com/Gen-Verse/dLLM-RL) ![](https://img.shields.io/github/stars/Gen-Verse/dLLM-RL?style=social) (first open-source post-training framework for DLM). My research has been supported by ByteDance Seed and Thinking Machines.









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
