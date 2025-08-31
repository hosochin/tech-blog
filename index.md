---
layout: default
---

# 記事一覧

<ul class="post-list">
{% for post in site.posts %}
  <li>
    <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
    <div class="post-meta">{{ post.date | date: "%Y年%m月%d日" }}</div>
    {% if post.excerpt %}
      <p>{{ post.excerpt | strip_html | truncate: 200 }}</p>
    {% endif %}
  </li>
{% endfor %}
</ul>

---

## このサイトについて

hosochin の技術ブログです。Web開発、プログラミング、ツール紹介などを書いています。