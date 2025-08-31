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
        <h3>技術スタック</h3>
        <div class="tech-tags">
            <span class="tech-tag">JavaScript</span>
            <span class="tech-tag">Python</span>
            <span class="tech-tag">Go</span>
            <span class="tech-tag">React</span>
            <span class="tech-tag">Node.js</span>
            <span class="tech-tag">Django</span>
            <span class="tech-tag">PostgreSQL</span>
            <span class="tech-tag">Docker</span>
            <span class="tech-tag">AWS</span>
            <span class="tech-tag">GitHub Actions</span>
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
}
</style>