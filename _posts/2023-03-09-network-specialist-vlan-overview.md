---
layout: post
title: 【ネスペ対策】【ポートベースVLAN/タグVLAN】VLANについて概要を理解する
date: 2023-03-09
tags: [network, vlan, port-based-vlan, tag-vlan, network-specialist, ipa, switching]
---

# はじめに

お世話になります、hosochinです  
今回はIPAのネットワークスペシャリスト対策ってことでVLANについてです

# 目次

- [VLANとは](#vlanとは)
- [ポートベースVLAN](#ポートベースvlan)
- [タグVLAN](#タグvlan)

# VLANとは

**VLAN = Virtual LAN** とは仮想的なLANを構成する仕組みです  
VLANの特徴としては以下が挙げられます

- スイッチ内部で論理的にLANセグメントを分割する
- ネットワークの柔軟性が上がる
  - 負荷分散
  - 部署や席の入れ替え時に、配線を変えることなくネットワークの構造を変更できる
  - パケットが流れる範囲を限定することで、効率的な運用とセキュリティ向上がつながる

VLANには大きく **「ポートベースVLAN」「タグVLAN」** の2種類があります

# ポートベースVLAN

**ポートベースVLAN** では、L2スイッチの物理的なポートごとにVLANグループを設定します  
同じスイッチに接続していても、異なるVLANグループ間ではブロードキャストなどの通信は届きません

<img src="{{ '/assets/images/2023-03-09_1.png' | relative_url }}" alt="ポートベースVLAN構成" class="img-small">

物理ポートとVLANが1対1になっているのでシンプルでわかりやすいですね  
しかし、ポートベースVLANは、スイッチを跨いでVLANを繋ぎたい場合に問題が発生します  
それは **VLANの数だけ接続用のポートが必要** になることです

<img src="{{ '/assets/images/2023-03-09_2.png' | relative_url }}" alt="ポートベースVLAN スイッチ跨ぎ" class="img-large">

↑のイメージ図ではスイッチ1とスイッチ2に跨いでVLANを繋ぐために、VLAN1, VLAN2でそれぞれ接続用ポートが必要になっています  
**VLANの数が増えれば、その分だけ接続用のポートを消費** することとなり、PCなどの端末に使えるポートが少なくなってしまいます  
この問題を解決するのがもう1つのVLAN方式 **「タグVLAN」** です

# タグVLAN

**タグVLAN** は、通信パケット中にVLANタグを埋め込むことでVLANグループの設定が可能です  
VLANタグを埋め込んだフレームの構成は以下のようになります

<img src="{{ '/assets/images/2023-03-09_3.png' | relative_url }}" alt="タグVLANフレーム構成" class="img-medium">

送信元MACアドレスとタイプの間に挿入されているのがVLANタグですね  
VLANタグの中身の概要は以下です

- **TPID = Tag Protocol Identifier** （16ビット）
  - IEEE802.1Qフレームであることを受信側に示す情報
- **TCI = Tag Control Information** （16ビット）
  - **PCP = Priority Code Point** （3ビット）
    - フレームの優先度を示す情報
  - **CFI = Canonical Format Indicator** （1ビット）
    - アドレス形式を示す情報で、例えばイーサネットの場合は0が入る
  - **VID = VLAN Identifier** （12ビット）
    - VLAN IDを示す情報
    - 0 ~ 4095の値が入る

また、タグVLANは1つのポートを複数のVLANで使用できるため、ポートベースVLANの **「VLANの数が増えれば、その分だけ接続用のポートが必要になる」** といった問題が発生しません  
先ほどのポートベースVLANのスイッチ跨ぎの構成を、タグVLANにした場合以下のようになります

<img src="{{ '/assets/images/2023-03-09_4.png' | relative_url }}" alt="タグVLAN スイッチ跨ぎ" class="img-medium">

VLANの数が増えていっても、スイッチ1とスイッチ2の接続用ポートは1つのままとなります

# まとめ

VLAN技術により、物理的なネットワーク構成を変更することなく論理的なネットワークセグメントを構築できます。ポートベースVLANはシンプルですが拡張性に課題があり、タグVLAN（IEEE 802.1Q）を使用することで効率的なマルチVLAN環境を実現できます。ネットワークスペシャリスト試験では、両方式の特徴と適用場面を理解しておくことが重要です。
