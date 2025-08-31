---
layout: default
---

# 最新記事

<!-- 検索ボックス -->
<div class="search-container">
  <input type="text" id="search-input" placeholder="記事を検索...">
  <div id="search-results"></div>
</div>

<ul class="post-list">
{% for post in site.posts %}
  <li class="post-item">
    <div class="post-title">
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </div>
    <div class="post-meta">{{ post.date | date: "%Y年%m月%d日" }}</div>
    {% if post.excerpt %}
      <p class="post-excerpt">{{ post.excerpt | strip_html | strip_newlines | truncate: 180 }}</p>
    {% endif %}
  </li>
{% endfor %}
</ul>

<div class="about-section">
  <h3>このサイトについて</h3>
  <p>hosochin の技術ブログです。Web開発、プログラミング、ツール紹介などを書いています。</p>
</div>
