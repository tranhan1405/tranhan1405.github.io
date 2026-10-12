---
layout: page
permalink: /publications/
title: publications
description: Journal articles and conference papers in reverse chronological order. See also <a href="https://scholar.google.com/citations?user=FSIZYHkAAAAJ">Google Scholar</a>.
nav: true
nav_order: 1
---

<!-- _pages/publications.md -->

{% include bib_search.liquid %}

<div class="publications">

<h2 class="bibliography">Journal articles</h2>
{% bibliography --query @article %}

<h2 class="bibliography">Conference papers</h2>
{% bibliography --query @inproceedings %}

</div>
