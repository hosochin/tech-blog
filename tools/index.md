---
layout: default
title: ツール
---

# 便利ツール

開発・日常で役立つWebツールを集めました。すべてブラウザ上で動作します。

<div class="tools-grid">
    <div class="tool-card">
        <h3>URLエンコード・デコード</h3>
        <p>URLエンコード・デコードを簡単に行えるツールです。</p>
        <a href="url-encoder.html" class="tool-link">使ってみる</a>
    </div>

    <div class="tool-card">
        <h3>Base64エンコード・デコード</h3>
        <p>Base64形式のエンコード・デコードツールです。</p>
        <a href="base64.html" class="tool-link">使ってみる</a>
    </div>

    <div class="tool-card">
        <h3>JSON整形・圧縮</h3>
        <p>JSONデータの整形・圧縮・バリデーションツールです。</p>
        <a href="json-formatter.html" class="tool-link">使ってみる</a>
    </div>

    <div class="tool-card">
        <h3>パスワード生成</h3>
        <p>セキュアなパスワードを生成するツールです。</p>
        <a href="password-generator.html" class="tool-link">使ってみる</a>
    </div>
</div>

<style>
.tools-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 20px;
    margin-top: 30px;
}

.tool-card {
    background: #fdfdfd;
    border: 1px solid #eee;
    border-radius: 8px;
    padding: 20px;
    transition: all 0.2s ease;
}

.tool-card:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    border-color: #ddd;
}

.tool-card h3 {
    color: #0066cc;
    margin: 0 0 10px 0;
    font-size: 1.2em;
}

.tool-card p {
    color: #666;
    font-size: 0.9em;
    margin-bottom: 15px;
}

.tool-link {
    display: inline-block;
    background: #0066cc;
    color: white;
    padding: 8px 16px;
    border-radius: 20px;
    text-decoration: none;
    font-size: 0.9em;
    transition: background 0.2s ease;
}

.tool-link:hover {
    background: #0052a3;
    text-decoration: none;
    color: white;
}
</style>