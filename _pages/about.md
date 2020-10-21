---
title: "このサイトについて"
permalink: /about/
author_profile: true
sidebar: false
---

Oracle Cloud Infrastructure 活用資料集は、Oracle Cloud Infrastructure (OCI) を使っていこうという人のための便利リンク集です。  
プレゼンテーションで利用したスライドや、セミナー動画などのコンテンツを中心に掲載しています。  
技術資料については定期的にアップデートしていますので、最新のものをご利用ください。

また、実際にOCIを触って学習したい! という方には Oracle Cloud Infrastructure チュートリアル も合わせてご活用ください。

このページは、日本オラクル株式会社 ソリューション・エンジニア によってメンテナンスされています。

{% assign authorCount = page.authors | size %}
{% if authorCount == 0 %}
  {% if page.author and site.data.authors[page.author] %}
    {% assign author = site.data.authors[page.author] %}
  {% else %}
    {% assign author = site.author %}
  {% endif %}
  
  {% assign authors = {{author.authorid | split: "," }} %}
{% else %}
  {% assign authors = page.authors %}
{% endif %}

<div itemscope itemtype="http://schema.org/Person">
  {% for authorid in authors %}
  <p>
...
  </p>
  {% endfor %}
</div>