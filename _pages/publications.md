---
layout: page
permalink: /publications/
title: publications
description: Journal articles and conference papers in reverse chronological order.
nav: true
nav_order: 1
---

<!-- _pages/publications.md -->

Full list with citation counts on [Google Scholar](https://scholar.google.com/citations?user=FSIZYHkAAAAJ).

{% include bib_search.liquid %}

<div class="publications">

<h3 class="mt-4 font-weight-bold">Journal articles</h3>
{% bibliography --query @article %}

<h3 class="mt-5 font-weight-bold">Conference papers</h3>
{% bibliography --query @inproceedings %}

</div>
