---
layout: collection
permalink: /splash-page/
title: "Splash About"

header:
  overlay_image: /assets/images/hilthart-pedersen-X3jymZ1oJ2E-unsplash.jpg
  overlay_filter: "0.5"
  actions:
    - label: "Download"
      url: "https://github.com/HeainLee/"
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"

intro: 
  - excerpt: 'Whom have I in heaven but thee? and there is none upon earth that I desire beside thee. My flesh and my heart faileth: but God is the strength of my heart, and my portion for ever. Psalms 73:25-26'

feature_row:
  - image_path: /assets/images/ales-nesetril-Im7lZjxeLhg-unsplash.jpg
    alt: "Developer"
    title: "Developer"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
  - image_path: /assets/images/anas-alshanti-feXpdV001o4-unsplash.jpg
    image_caption: "Image courtesy of [Unsplash](https://unsplash.com/)"
    alt: "Data Scientist"
    title: "Data Scientist"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--primary"
  - image_path: /assets/images/etienne-bosiger-WTkUYzNCu-A-unsplash.jpg
    alt: "Christian"
    title: "Christian"
    excerpt: "This is some sample content that goes here with **Markdown** formatting."

feature_row2:
  - image_path: /assets/images/coffee.jpg
    alt: "placeholder image 2"
    title: "What I like 01"
    excerpt: 'I like drinking a **Coffee**. I drink every mooorning & afternooon'
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--primary"

feature_row3:
  - image_path: /assets/images/hyunsang.jpg
    alt: "placeholder image 2"
    title: "What I like 01"
    excerpt: 'I like **Musicians**. Especially *HSS* & *Hoppipolla* ' 
    url: "#test-link"
    btn_label: "Read More"
    btn_class: "btn--primary"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

{% include feature_row caption="I love **breaking through** the wall. I love **learning** something new" type="center" %}

{% include feature_row id="feature_row2" type="left" %}

{% include feature_row id="feature_row3" type="right" %}