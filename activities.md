---
layout: page
title: 活動報告
permalink: /activities/
---

これまでの活動の様子を紹介します。新しい活動報告は [_posts フォルダ](https://github.com) に
Markdown ファイルを追加するだけで、自動的にこの一覧に表示されます(追加方法は README.md 参照)。

<div class="card-grid">
{% for post in site.posts %}
  <a class="card" href="{{ post.url | relative_url }}">
    {% if post.image %}
      <img class="card-image" src="{{ post.image | relative_url }}" alt="{{ post.title }}">
    {% endif %}
    <div class="card-body">
      <p class="card-date">{{ post.date | date: "%Y年%-m月%-d日" }}</p>
      <h3 class="card-title">{{ post.title }}</h3>
      <p class="card-excerpt">{{ post.excerpt | strip_html | truncate: 70 }}</p>
    </div>
  </a>
{% endfor %}
</div>

{% if site.posts.size == 0 %}
<p class="empty-note">まだ活動報告がありません。</p>
{% endif %}
