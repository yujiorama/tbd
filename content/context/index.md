---
date: 2015-01-05T09:42:02+05:00
title: コンテキスト
weight: 5
---

<!-- A development team wanting to go ahead with Trunk-Based Development have prerequisite achievements. Once the team has
done a few commits in the correct style, other things are now facilitated. We can show this as a layer cake: -->
トランクベース開発をやってみたい開発チームには、先に整えなければならない条件があります。
正しい作法で数回コミットできれば、他の要素も自然に整うことでしょう。
ケーキのように（層状に）表現すると次のようになります。

![](layer_cake.png)

<!-- DevOps is encompassing too. At the very least, the expansion of development best practices into operations heartlands. -->
DevOps は包括的な考え方です。
少なくとも、ソフトウェア開発のベストプラクティスを運用の真っ只中に拡張していくことになるでしょう。

## トランクベース開発の必要条件

<!-- (layers below it) -->
（一つ下の層）

### 安定した開発基盤

<!-- Installation of your VCS technology is part of a foundational development infrastructure that includes developer
workstations or laptops that are appropriate for build testing and running of the application or service being made.
Developers running the application only need it to be functionally viable. It doesn't have to match the performance
of the expected production environment, and it is OK for it to be non-functionally different in other ways too. -->
開発基盤の中心的な要素はバージョン管理ツールです。
開発者の作業マシンやラップトップPCで、アプリケーションやサービスをビルドし、テストし、実行できるようにしなければなりません。
開発者がアプリケーションを実行するのは、機能的な妥当性を確認する場合だけです。
本番環境に期待する性能を確認するのは不適切ですし、非機能特性を確認する別の方法を準備すればいいからです。

<!-- In the modern DevOps era, this probably means Infrastructure as Code.  -->
DevOps の時代では、IaC（Infrastructure as Code）になるかもしれません。

## トランクベース開発を促進する要素

<!-- (layers above it) -->
（一つ上の層）

### 継続的インテグレーション

<!-- [Continuous Integration](/continuous-integration/) (CI) has been in practice since the
mid-nineties in its modern incarnation (integrating to a shared code line frequently and 
testing that). -->
[継続的インテグレーション（CI）](/continuous-integration/) は 1990 年代中盤に誕生したプラクティスです（ソースコードを共有し、頻繁に統合してテストする）。

<!-- Importantly, the reader should understand that there is a large overlap between Trunk-Based Development and
Continuous Integration, as defined by its definers and documenters. Whereas Trunk-Based Development focuses on a
pure source-control workflow and an individual contributor's obligations to that, Continuous Integration focuses 
equally on that and the need to have machines issue early warnings on breakages and incompatibilities. -->
それぞれの概念を定義した人やドキュメントを作成した人から明らかなのですが、トランクベース開発と継続的インテグレーションは、多くの部分が重なっていることを理解しておくことが重要です。
トランクベース開発は、純粋にソースコード管理ツールのワークフローや、個々の開発者の貢献に注目しています。
継続的インテグレーションではそれ以外にビルドの失敗や非互換性を早い段階で警告、通知するマシンが必要になります。

### 継続的デリバリー

<!-- [Continuous Delivery](/continuous-delivery/) (CD) is a layer on top of that, has been practiced since the mid-2000's, and
documented in Jez Humble and Dave Farley's book of the same name in 2010.  This site gives a 5% summary of the 
practice. The reader should dive into the Book and associated site, without delay. -->
CI の上には [継続的デリバリー（CD）](/continuous-delivery/) があります。
2000年代中盤に始まったプラクティスで、Jez Humble と Dave Farley は 2010 年に「継続的デリバリー」という書籍を出版しています。
このドキュメントでは CD というプラクティスの 5% くらいしか説明していません。
詳しくは書籍や関連するWebサイトで学んでください。

### リーン実験

<!-- With CD locked in, continual improvement experiments can happen with a focus on time through "the machine" that is your
development and delivery operation. The experiments should draw off the field of science that is "Lean" so that the 
impact of each experiment can be measured against predictions and decisions made appropriately following it. -->
CD ができるようになれば、開発や配信作業をする「マシン」で、時間に注目した、継続的な改善のための実験ができるようになります。
実験は科学的に行わなければなりません。つまり「リーン」でなければならないのです。
つまり、それぞれの実験の効果は、計測値に対する予測と判断に基づいて行わなければならないのです。

<!-- Lean Experiments can happen in any development team on any project but work **best** on foundations that
are solid. Specifically, the solid foundations of Trunk-Based Development, CI, and CD.  -->
リーン実験はどんなチームのどんなプロジェクトでも実行できますが、堅固な基盤の整ったチームでないと **最高の成果** を残すことはできません。
特に重要なのは、トランクベース開発における堅固な基盤である CI と CD です。

<!-- This site does not touch on Lean Experiments beyond this section, but the reader should strive to understand that field
of science when the lower layers of the stylized cake are solid. -->
このセクション以外では、特にリーン実験について説明していません。
しかし、ケーキの土台が整ったなら、科学的な実験の作法を学んだほうがいいでしょう。
