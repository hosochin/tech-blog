---
layout: default
title: About
---

# About hosochin

<div class="about-container">
    <div class="about-profile">
        <div class="about-avatar">
            <img src="{{ '/icon_.jpg' | relative_url }}" alt="hosochin">
        </div>
        <div class="about-info">
            <h2>hosochin</h2>
            <p class="about-subtitle">Web Developer</p>
            <p class="about-description">
                IT技術を中心にゆるく発信している開発者です。<br>
                アルコール駆動開発でゆるゆると。
            </p>
            <div class="about-links">
                <a href="https://github.com/hosochin" target="_blank" class="about-link">
                    <span>GitHub</span>
                </a>
            </div>
        </div>
    </div>

    <div class="about-section">
        <h3>投稿記事カテゴリ</h3>
        <div class="tag-chart">
            {% assign sorted_tags = site.tags | sort %}
            {% assign tag_array = "" | split: "" %}
            {% for tag in sorted_tags %}
                {% assign tag_data = tag[0] | append: "||" | append: tag[1].size %}
                {% assign tag_array = tag_array | push: tag_data %}
            {% endfor %}
            {% assign tag_array = tag_array | sort | reverse %}
            
            {% assign max_count = 0 %}
            {% for tag_data in tag_array %}
                {% assign tag_parts = tag_data | split: "||" %}
                {% assign count = tag_parts[1] | plus: 0 %}
                {% if count > max_count %}
                    {% assign max_count = count %}
                {% endif %}
            {% endfor %}
            
            {% for tag_data in tag_array %}
                {% assign tag_parts = tag_data | split: "||" %}
                {% assign tag_name = tag_parts[0] %}
                {% assign tag_count = tag_parts[1] | plus: 0 %}
            <div class="chart-item">
                <div class="chart-label">{{ tag_name }}</div>
                <div class="chart-bar-container">
                    <div class="chart-bar" style="width: {% if max_count > 0 %}{{ tag_count | times: 100 | divided_by: max_count }}%{% else %}0%{% endif %}"></div>
                    <span class="chart-count">{{ tag_count }}</span>
                </div>
            </div>
            {% endfor %}
        </div>
    </div>

    <div class="about-section">
        <h3>このブログについて</h3>
        <p>
            自分が躓いたところやハマったところについて分かりやすく書いていければと思っています。<br>
            技術的な内容から日常的に使える便利ツールまで、幅広く発信していく予定です。
        </p>
        
        <h4>主なコンテンツ</h4>
        <ul>
            <li>Web開発の技術記事</li>
            <li>プログラミング Tips & Tricks</li>
            <li>開発で使える便利ツール</li>
            <li>ハマりやすいポイントの解説</li>
        </ul>
    </div>

    <div class="about-section">
        <h3>お問い合わせ</h3>
        <p>
            記事の内容についてのご質問や、お仕事のご相談などがございましたら、
            <a href="https://github.com/hosochin" target="_blank">GitHub</a>
            までお気軽にご連絡ください。
        </p>
    </div>
</div>

<style>
.about-container {
    max-width: 700px;
    margin: 0 auto;
}

.about-profile {
    display: flex;
    align-items: center;
    gap: 30px;
    margin-bottom: 40px;
    padding: 30px;
    background: #fdfdfd;
    border-radius: 12px;
    border: 1px solid #eee;
}

.about-avatar {
    flex-shrink: 0;
}

.about-avatar img {
    width: 120px;
    height: 120px;
    border-radius: 50%;
    object-fit: cover;
    border: 4px solid #0066cc;
    box-shadow: 0 4px 12px rgba(0, 102, 204, 0.2);
}

.about-info h2 {
    margin: 0 0 8px 0;
    color: #0066cc;
    font-size: 2em;
}

.about-subtitle {
    color: #666;
    font-size: 1.1em;
    margin: 0 0 15px 0;
    font-weight: 500;
}

.about-description {
    color: #333;
    line-height: 1.6;
    margin-bottom: 20px;
}

.about-links {
    display: flex;
    gap: 15px;
}

.about-link {
    display: inline-flex;
    align-items: center;
    padding: 8px 16px;
    background: #0066cc;
    color: white;
    text-decoration: none;
    border-radius: 20px;
    font-size: 0.9em;
    transition: all 0.2s ease;
}

.about-link:hover {
    background: #0052a3;
    text-decoration: none;
    color: white;
    transform: translateY(-1px);
}

.about-section {
    margin-bottom: 40px;
    padding: 25px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.about-section h3 {
    color: #0066cc;
    margin: 0 0 20px 0;
    font-size: 1.4em;
    font-weight: 600;
    border-bottom: 2px solid #f0f8ff;
    padding-bottom: 10px;
}

.about-section h4 {
    color: #333;
    margin: 25px 0 15px 0;
    font-size: 1.1em;
}

.about-section p {
    line-height: 1.7;
    color: #555;
}

.about-section ul {
    padding-left: 20px;
    color: #555;
}

.about-section li {
    margin-bottom: 8px;
    line-height: 1.6;
}

.tech-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.tech-tag {
    background: #f0f8ff;
    color: #0066cc;
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 0.9em;
    font-weight: 500;
    border: 1px solid #e6f2ff;
}

.tag-chart {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-top: 15px;
}

.chart-item {
    display: flex;
    align-items: center;
    gap: 15px;
}

.chart-label {
    min-width: 120px;
    font-size: 0.9em;
    font-weight: 500;
    color: #333;
    text-align: right;
}

.chart-bar-container {
    flex: 1;
    display: flex;
    align-items: center;
    gap: 10px;
    position: relative;
}

.chart-bar {
    height: 24px;
    background: linear-gradient(90deg, #0066cc, #4da3e0);
    border-radius: 12px;
    min-width: 4px;
    transition: all 0.3s ease;
    box-shadow: 0 2px 4px rgba(0, 102, 204, 0.2);
}

.chart-bar:hover {
    transform: scaleY(1.1);
    box-shadow: 0 4px 8px rgba(0, 102, 204, 0.3);
}

.chart-count {
    font-size: 0.85em;
    font-weight: 600;
    color: #0066cc;
    min-width: 20px;
    text-align: center;
}

@media (max-width: 768px) {
    .about-profile {
        flex-direction: column;
        text-align: center;
        gap: 20px;
    }
    
    .about-avatar img {
        width: 100px;
        height: 100px;
    }
    
    .about-info h2 {
        font-size: 1.6em;
    }
    
    .tech-tags {
        justify-content: center;
    }
    
    .chart-label {
        min-width: 80px;
        font-size: 0.8em;
    }
    
    .chart-bar {
        height: 20px;
    }
    
    .chart-count {
        font-size: 0.8em;
    }
}
</style>