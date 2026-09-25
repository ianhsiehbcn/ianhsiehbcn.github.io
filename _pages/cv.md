---
layout: default
permalink: /cv/
title: CV
nav: true
nav_order: 3
cv_format: rendercv # options: rendercv, jsonresume
toc:
  sidebar: left
---

{% include custom-fonts.html %}
{% include theme-toggle.html %}

<style>
  .cv ul.list-group {
    list-style: none;
    padding-left: 0;
  }
</style>

{% al_folio_cv_render %}
