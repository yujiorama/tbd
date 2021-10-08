---
date: 2015-01-01T21:07:33+01:00
title: はじめに
type: index
weight: 1
---

## 概要

<!-- A source-control branching model, where developers collaborate on code in a single branch called 'trunk' *,
resist any pressure to create other long-lived development branches by employing documented techniques. They
therefore avoid merge hell, do not break the build, and live happily ever after.

 &ast; *main* or *master*, in Git nomenclature -->
バージョン管理システムのブランチモデルでは、開発者がコラボレーションする単独のブランチを `trunk`[&ast;] と呼びます。
このドキュメントで紹介する手法を活用して、長期間残り続ける開発用ブランチの作成を促すあらゆる圧力に抵抗しましょう。
マージ地獄を撲滅し、ビルドを壊さず、今までにない幸せな生活を取り戻すのです。

[&ast;]: 文脈によっては *main* や *master* が使われます

## mainline/master/trunk から共有ブランチを作成するのはどんなリリース周期にも噛み合いません

![](trunk1a.png)

## 小規模チーム向けのトランクベース開発（Trunk-Based Development）

![](trunk1b.png)

## 中規模以上のチーム向けのトランクベース開発（Trunk-Based Development）

![](trunk1c.png)

## 詳細化あるいは主張や警告

![](ix_key.png)

<!-- Trunk-Based Development is a key enabler of [Continuous Integration](/continuous-integration/) and by extension
[Continuous Delivery](/continuous-delivery/). When individuals on a team are committing their changes to the trunk
multiple times a day it becomes easy to satisfy the core requirement of Continuous Integration that all team
members commit to trunk at least once every 24 hours. This ensures the codebase is always releasable on demand
and helps to make Continuous Delivery a reality. -->
トランクベース開発は [継続的インテグレーション](/continuous-integration/)、ひいては [継続的デリバリー](/continuous-delivery/) を可能にする、重要な要素です。
チームメンバーが1日に何回も `trunk` へ変更をコミットするようになれば、全てのチームメンバーが24時間以内に少なくとも1回は `trunk` へコミットする、という継続的インテグレーションの中心的な要件を満足させるのは簡単です。
そうすれば、コードベースを必要に応じて常にリリースできる状態に保つことができるし、継続的デリバリーを実現するのに役立ちます。

<!-- The dividing line between small team Trunk-Based Development and scaled Trunk-Based Development is a subject to team size and commit rate consideration. The precise moment a dev team is no longer "small" and has transitioned to "scaled" is subject to practitioner debate.  Regardless, teams perform a full "pre integrate" build (compile, unit tests, integration tests) on their dev workstations before committing/pushing for others (or bots) to see. -->
小規模チームのトランクベース開発と、中規模以上のチーム向けのトランクベース開発を区別するのは、チームの大きさとコミットの頻度次第です。
開発チームが「小規模」から「中規模以上」になる正確な瞬間を決めるのは、実践者の議論次第です。
いずれにしても、誰かがコミットをプッシュして他の誰か（ボットを含む）が参照できるようにする前に、開発用のワークステーションで完全な「事前結合」ビルド（コンパイル、ユニットテスト、結合テスト）を実施していることでしょう。

### 主張

<!-- - You should do Trunk-Based Development instead of GitFlow and other branching models that feature multiple long-running branches
- You can either do a direct to trunk commit/push (v small teams) or a Pull-Request workflow as long as those feature branches
  are short-lived and the product of a single person. -->
- 複数の長命ブランチを許容する GitFlow のようなブランチモデルではなく、トランクベース開発をするべきです
- 小規模チームなら `trunk` へ直接コミット（プッシュ）してもいいし、一人で開発しているプロダクトなら短命のフィーチャブランチと同じくらいの期間のプルリクエストワークフローを使用してもいいでしょう

### 警告

<!-- - Depending on the team size, and the rate of commits,
  [short-lived feature branches](/short-lived-feature-branches/) are used for
  code-review and build checking (CI), but not artifact creation or publication, to happen before commits land in the trunk for other developers to depend on.
  Such branches allow developers to engage in [eager and continuous code review](/continuous-review/) of contributions
  before their code is integrated into the trunk. Very small teams may [commit direct to the trunk](/committing-straight-to-the-trunk/). -->
- チームサイズやコミット頻度に依存しますが、コードレビューやビルドの確認（CI）のために [短命のフィーチャブランチ](/short-lived-feature-branches/) を使うことはあると思います。
  アーティファクトの作成、公開をどうするか、また、他の開発者が参照できる `trunk` へコミットする前にやるかどうかは、状況次第です。
  こういうブランチがあると、開発者はコードを `trunk` へ統合する前に行う [貪欲で継続的なコードレビュー](/continuous-review/) で貢献しやすくなります。
  少人数のチームなら [`trunk` へ直接コミット](/committing-straight-to-the-trunk/) してもいいでしょう。

<!-- - Depending on the intended release cadence, there may be [release branches](/branch-for-release/) that are cut from the trunk on
  a just-in-time basis, are 'hardened' before a release (without that being a team activity), and **those branches are deleted** some time after release. Alternatively, there
  may also be no release branches if the team is [releasing from Trunk](/release-from-trunk/), and choosing a "fix
  forward" strategy for bug fixes. Releasing from trunk is also for high-throughput teams, too. -->
- リリース周期によって異なりますが、リリース直前に「ハードニング（hardend）」するため `trunk` から [リリースブランチ](/branch-for-release/) を作成する場合があります。
  チームアクティビティとは無関係ですし、リリースしてしばらくしたら **削除します**。
  一方、[`trunk` からリリース] しているチームや、バグ修正を「修正して前進する戦略（fix forward strategy）」でやっているチームにはリリースブランチが不要です。
  `trunk` からリリースしていると、チームのスループットは高くなる傾向があります。

<!-- - Teams should become adept with the related [branch by abstraction](/branch-by-abstraction/) technique for longer
  to achieve changes, and use [feature flags](/feature-flags/) in day to day development to allow for hedging on
  the order of releases (and other good things - see [concurrent development of consecutive releases](/concurrent-development-of-consecutive-releases/)) -->
- チームは [抽象化によるブランチ](/branch-by-abstraction/) の関連手法で長期的な変更を実現したり、日常的に [フィーチャフラグ](/feature-flags/) を利用して、リリースの順序を保てるようにしなければなりません。他にも見返りがあります。[並行開発と継続的なリリース](/concurrent-development-of-consecutive-releases/) を参照してください。

<!-- - If you have more than a couple of developers on the project, you are going to need to hook up a
  [build server](/continuous-integration/) to verify that their commits have **not broken the build**
  after they land in the trunk, and also when they are ready to be merged back into the trunk from a
  short-lived feature branch. -->
- プロジェクトに2人以上の開発者がいるなら、彼らのコミットが `trunk` に入る前に、**ビルドを壊していない** ことを確かめるための [ビルドサーバー](/continuous-integration/) を用意しなければなりません。短命のフィーチャブランチから `trunk` へマージするときも同様です。

<!-- - Development teams can casually flex up or down in size (in the trunk) without affecting throughput or quality.
  Proof? [Google do Trunk-Based Development](/game-changers/index.html#google-revealing-their-monorepo-trunk-2016) and
  have **35000 developers and QA automators** in that single [monorepo](/monorepos/) trunk, that in their case can
  [expand or contract](/expanding-contracting-monorepos/) to suit the developer in question. -->
- `trunk` で開発していると、スループットや品質に影響を与えることなく、開発チームの規模を簡単に拡大したり縮小したりできます。
  証拠は [Google でトランクベース開発が行われていること](/game-changers/index.html#google-revealing-their-monorepo-trunk-2016) です。
  **35,000人の開発者とQAオートメーター** が単一の [モノレポ](/monorepos/) の `trunk` で作業していますし、必要に応じてチームの規模を [拡大したり縮小したり](/expanding-contracting-monorepos/) できます。

<!-- - People who practice the [GitHub-flow branching model](/alternative-branching-models/index.html#modern-claimed-high-throughput-branching-models) will feel
  that this is quite similar, but there is one small difference around where to release from. -->
- [GitHub-flow ブランチモデル](/alternative-branching-models/index.html#modern-claimed-high-throughput-branching-models) を実践している人々は、リリースするブランチ以外はほとんど同じような印象を抱いています。

<!-- - People who practice the Gitflow branching model will find this **very different**, as will many developers used to
  the popular ClearCase, Subversion, Perforce, StarTeam, VCS [branching models of the past](/alternative-branching-models/index.html#legacy-branching-models). -->
- Gitflow ブランチモデルを実践している人々は **とても大きな違い** を感じています。
  ClearCase や Subversion や Perforce や StarTeam など、[歴史的なブランチモデル](/alternative-branching-models/index.html#legacy-branching-models) を使ったことのある開発者に多いようです。

<!-- - [Many publications](/publications/) promote Trunk-Based Development as we describe it here. Those include the best-selling 'Continuous Delivery' and 'DevOps Handbook'. This should not even be controversial anymore! -->
- トランクベース開発を推奨する [多数の出版物](/publications/) が存在します。
  中には「継続的デリバリー」は「DevOps ハンドブック」も含まれます。
  これらの書籍の価値は今さら議論するまでもないでしょう。

## 経緯

<!-- Trunk-Based Development is not a new branching model. The word 'trunk' is referent to the concept of a growing tree,
where the fattest and longest span is the trunk, not the branches that radiate from it and are of more limited length. -->
トランクベース開発は新しいブランチモデルではありません。
`trunk` という言葉は、木が成長していく様子に関連する概念です。
最も太く長くなるのが `trunk` です。
トランクから分かれるのがブランチ（枝）で、ブランチはそれほど長くなりません。

<!-- It has been a lesser known branching model of choice since the mid-nineties and considered tactically since the eighties.
The largest of development organizations, like Google (as mentioned) and Facebook practice it at scale. -->
トランクベース開発は1980年代から検討されていた戦術ですが、1990年の中盤にはほとんど知られていないブランチモデルでした。
Google や Facebook のような最大級の開発組織で、大規模に使用されています。

<!-- Over 30 years different [advances to source-control technologies and related tools/techniques](/game-changers/) have made
Trunk-Based Development more (and occasionally less) prevalent, but it has been a branching model that many have stuck
with through the years. -->
この30年間でさまざまな [ソースコード管理技術や関連するツールや手法が発展し](/game-changers/)、トランクベース開発がより広く（あるいは狭く）使われるようになりました。
さまざまなブランチモデルが何年も行き詰ってきた経験を反映した手法なのです。

## This site

This site attempts to collect all the related facts, rationale and techniques for Trunk-Based Development together
in one place, complete with twenty-five diagrams to help explain things. All without using TBD as an acronym
even ~~once~~ twice.
