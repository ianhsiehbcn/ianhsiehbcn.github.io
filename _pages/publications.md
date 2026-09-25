---
layout: page
permalink: /publications/
title: publications
nav: true
nav_order: 2
---

{% include custom-fonts.html %}
{% include theme-toggle.html %}

<style>
  .ct-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0.75rem;
    margin: 0.5rem 0 0.25rem;
  }
  .ct-row figure {
    margin: 0;
    text-align: center;
  }
  .ct-row img {
    width: 100%;
    aspect-ratio: 750 / 541;
    object-fit: cover;
    border-radius: 4px;
  }
  .ct-row figcaption {
    font-size: 0.85rem;
    margin-top: 0.25rem;
  }
  .ct-note {
    font-size: 0.85rem;
    color: var(--global-text-color-light);
    margin-bottom: 1.5rem;
  }
</style>

<div class="ct-row">
  <figure>
    <img src="{{ '/assets/img/sinkslot/source.jpg' | relative_url }}" alt="Source: Monet, Water Lilies, Evening Effect" loading="lazy">
    <figcaption>Source</figcaption>
  </figure>
  <figure>
    <img src="{{ '/assets/img/sinkslot/target.jpg' | relative_url }}" alt="Target: Monet, Haystacks at Giverny" loading="lazy">
    <figcaption>Target</figcaption>
  </figure>
  <figure>
    <img src="{{ '/assets/img/sinkslot/output.jpg' | relative_url }}" alt="Output: source recolored with the target's palette by SinkSLOT" loading="lazy">
    <figcaption>Output</figcaption>
  </figure>
</div>
<p class="ct-note">Color transfer with SinkSLOT takes fractions of a second.</p>

<div class="publications">

{% bibliography %}

</div>
