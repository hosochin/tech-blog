---
layout: default
title: アーカイブ
---

# アーカイブ

すべての記事を時系列順に表示しています。

## 記事一覧

<div class="archive-container">
    {% assign posts_by_year = site.posts | group_by_exp:"post", "post.date | date: '%Y'" %}
    
    {% for year in posts_by_year %}
    <div class="year-section">
        <h3 class="year-header">{{ year.name }}年</h3>
        
        {% assign posts_by_month = year.items | group_by_exp:"post", "post.date | date: '%Y-%m'" %}
        {% for month in posts_by_month %}
        <div class="month-section">
            <h4 class="month-header">{{ month.items.first.date | date: "%m月" }}</h4>
            
            <div class="posts-list">
                {% for post in month.items %}
                <div class="archive-post">
                    <div class="post-date">{{ post.date | date: "%m/%d" }}</div>
                    <div class="post-content">
                        <h5 class="post-title">
                            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
                        </h5>
                        {% if post.excerpt %}
                        <p class="post-excerpt">{{ post.excerpt | strip_html | strip_newlines | truncate: 120 }}</p>
                        {% endif %}
                    </div>
                </div>
                {% endfor %}
            </div>
        </div>
        {% endfor %}
    </div>
    {% endfor %}
    
    {% if site.posts.size == 0 %}
    <div class="no-posts">
        <p>まだ記事がありません。</p>
    </div>
    {% endif %}
</div>

<div class="archive-stats">
    <h3>統計情報</h3>
    <div class="stats-grid">
        <div class="stat-item">
            <div class="stat-number">{{ site.posts.size }}</div>
            <div class="stat-label">記事数</div>
        </div>
        <div class="stat-item">
            <div class="stat-number">{{ site.tags.size }}</div>
            <div class="stat-label">タグ数</div>
        </div>
        <div class="stat-item">
            <div class="stat-number">{{ posts_by_year.size }}</div>
            <div class="stat-label">投稿年数</div>
        </div>
    </div>
</div>

<style>
.archive-container {
    max-width: 800px;
    margin: 0 auto 40px auto;
}

.year-section {
    margin-bottom: 40px;
}

.year-header {
    color: #0066cc;
    font-size: 1.6em;
    font-weight: 600;
    margin: 0 0 20px 0;
    padding-bottom: 10px;
    border-bottom: 3px solid #0066cc;
}

.month-section {
    margin-bottom: 30px;
    background: white;
    border-radius: 8px;
    padding: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.month-header {
    color: #333;
    font-size: 1.2em;
    font-weight: 600;
    margin: 0 0 15px 0;
    color: #666;
}

.posts-list {
    display: flex;
    flex-direction: column;
    gap: 15px;
}

.archive-post {
    display: flex;
    gap: 20px;
    padding: 15px;
    border: 1px solid #eee;
    border-radius: 6px;
    transition: all 0.2s ease;
}

.archive-post:hover {
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
    border-color: #ddd;
}

.post-date {
    flex-shrink: 0;
    width: 60px;
    color: #0066cc;
    font-weight: 600;
    font-size: 0.9em;
    text-align: center;
    padding: 8px;
    background: #f0f8ff;
    border-radius: 4px;
}

.post-content {
    flex: 1;
}

.post-title {
    margin: 0 0 8px 0;
    font-size: 1.1em;
    font-weight: 600;
}

.post-title a {
    color: #333;
    text-decoration: none;
    transition: color 0.2s ease;
}

.post-title a:hover {
    color: #0066cc;
    text-decoration: none;
}

.post-excerpt {
    color: #666;
    font-size: 0.9em;
    line-height: 1.5;
    margin: 0;
}

.archive-stats {
    background: white;
    border-radius: 8px;
    padding: 25px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
    max-width: 800px;
    margin: 0 auto;
}

.archive-stats h3 {
    color: #0066cc;
    margin: 0 0 20px 0;
    text-align: center;
}

.stats-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}

.stat-item {
    text-align: center;
    padding: 20px;
    background: #f8f9fa;
    border-radius: 6px;
}

.stat-number {
    font-size: 2em;
    font-weight: bold;
    color: #0066cc;
    margin-bottom: 5px;
}

.stat-label {
    color: #666;
    font-size: 0.9em;
}

.no-posts {
    text-align: center;
    padding: 40px;
    color: #666;
    font-style: italic;
}

@media (max-width: 768px) {
    .archive-post {
        flex-direction: column;
        gap: 10px;
    }
    
    .post-date {
        width: auto;
        text-align: left;
    }
    
    .stats-grid {
        grid-template-columns: 1fr;
        gap: 15px;
    }
}
</style>