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
    {% if post.tags.size > 0 %}
      <div class="post-tags">
        {% for tag in post.tags limit:4 %}
          <span class="tag">{{ tag }}</span>
        {% endfor %}
      </div>
    {% endif %}
  </li>
{% endfor %}
</ul>

<div class="about-section">
  <h3>このサイトについて</h3>
  <p>hosochin の技術ブログです。自分が躓いたところやハマったところについて分かりやすく書いていければと思います。</p>
</div>
