---
date: 2016-06-01T20:10:46+01:00
title: 継続的インテグレーション（CI：Continuous Integration）
weight: 61
---

<!-- {{< quote title="individuals practice Trunk-Based Development, and teams practice CI" >}}
&mdash; *Agile* Steve Smith
{{< /quote >}} -->
{{< quote title="CI はトランクベース開発とは独立した、チーム開発のプラクティスです" >}}
&mdash; *Agile* Steve Smith
{{< /quote >}}

## 継続的インテグレーションの定義

<!-- For many years CI has been accepted by a portion of software development community to mean a daemon process
that is watching the source-control repository for changes and verifying that they are correct, **regardless
of branching model**.  -->
ここ数年の間に、CIはソースコードリポジトリの変更を追跡して、正しい状態になっているか確かめるデーモンプロセスとして、一部のソフトウェア開発コミュニティに受け入れられるようになりました。**使用しているブランチモデルに関わらず使用できる仕組みです**。

<!-- However, the original intention was to focus on the verification **single integration point**
for the developer team. And do it daily if not more. The idea was for developers themselves to develop
habits to ensure everything going to that shared place many times a day was of high enough quality, 
and for the CI server to merely verify that quality, nightly. -->
しかし、本来は開発チームが **単一結合点** を検証することに注力するための考え方でした。
また、1日に1回以上実行するくらいのものでした。
開発者が自分たちのために考案した、共有しているソースコードが高品質であることを1日に何度も確認する習慣です。
CIサーバーにとって、はただ夜中に品質を確認するだけでした。

<!-- CI as we know it today was championed by Kent Beck, as one of the practices he included in
"Extreme Programming"{{< ext url="https://en.wikipedia.org/wiki/Extreme_programming" >}}
in the mid-nineties. Certainly in 1996, on the famous 'Chrysler Comprehensive Compensation System' (C3) 
project{{< ext url="https://en.wikipedia.org/wiki/Chrysler_Comprehensive_Compensation_System" >}} that Kent had all 
developers experiencing and enjoying the methodology - including the continuous integration aspect. The language for 
that project was Smalltalk and the single integration point was a Smalltalk image 
(a technology more advanced than a "mere" source-control systems that rule today). -->
現代のCIの達人と言えば Kent Beck です。
1990年の中期に公開された "Extreme Programming"{{< ext url="https://en.wikipedia.org/wiki/Extreme_programming" >}} のプラクティスにも含まれています。
1996年の 'Chrysler Comprehensive Compensation System' (C3) プロジェクト {{< ext url="https://en.wikipedia.org/wiki/Chrysler_Comprehensive_Compensation_System" >}} で、Kent Beck を含む全ての開発者は XP を実践し、楽しんでいました。
継続的インテグレーションもやっていたはずです。
プログラミング言語は Smalltalk で、単一結合点は Smalltalk イメージでした（現在主流になっているただのソースコード管理システムより、高度な技術でした）。

<!-- Thus, the intention of CI, as defined, was pretty much exactly the same as Trunk-Based Development, that emerged
elsewhere. Trunk-Based Development did not say anything about Continuous Integration daemons directly or indirectly, 
but there is an overlap today - the safety net around a mere branching model (and a bunch of techniques) is greatly 
valued. -->
このように、CIの目的や定義はトランクベース開発とほとんど一致しており、別々に発展したものと考えられます。
トランクベース開発では間接的にも直接的にもCIを実行するデーモンプロセスについて言及していませんが、重なる領域も出てきました。
ただのブランチモデル（および関連する手法）より優れた安全策になるからです。

<!-- Martin Fowler (with Matt Foemmel) called out Continuous Integration in an article in
2000{{< ext url="https://www.martinfowler.com/articles/originalContinuousIntegration.html" >}}, 
(rewritten in 2006{{< ext url="https://www.martinfowler.com/articles/continuousIntegration.html" >}}), and 
ThoughtWorks colleagues went on to build the then-dominant "Cruise Control"{{< ext url="http://cruisecontrol.sourceforge.net" >}} 
in early 2001. Cruise Control co-located the CI configuration on the branch being built next to the build script, as it 
should be. -->
Martin Fowler と Matt Foemmel は 2000 年に継続的インテグレーションの記事{{< ext url="https://www.martinfowler.com/articles/originalContinuousIntegration.html" >}} を書いているし（2006年にリライトされた {{< ext url="https://www.martinfowler.com/articles/continuousIntegration.html" >}}）、ThoughtWorks の中の人が 2001 年に "Cruise Control"{{< ext url="http://cruisecontrol.sourceforge.net" >}}  を開発した。
Cruise Control ではブランチに対する CI 構成とビルドスクリプトを同じ場所に配置します。

## 人間の行うインテグレーション

<!-- It is important to note that the build script which developers run prior to checking in, is the **same one** which is
followed by the CI service (see below). The build is broken into gated steps due to a need for succinct communication to 
you the developer as well as teammates. The classic steps would be: compile, test-compile, unit test invocation, service test 
invocation, functional test invocation. Those last two are part of the integration tests class. -->
開発者が変更をチェックインする前に、CIサーバーで実行するのと **同じビルドスクリプト** を実行するのは大事なことです。
チームメンバーとのやりとりを簡潔にするため、ビルドは複数の手順に分解します。
例えば、コンパイル、テストコードのコンパイル、ユニットテストの実行、サービステストの実行、機能テストの実行、というのが伝統的な手順です。
後ろの2つは結合テストの一部です。

<!-- If the build passed on the developer’s workstation, and the code is up to date with HEAD of what the team sees for
trunk, then the developer checks in the work (commit and push for Git). Where that goes to depends on which way of working 
the team is following. Some smaller teams will push straight to trunk, and may do so multiple times a day (see [Committing 
straight to the trunk](/committing-straight-to-the-trunk/)). Larger teams may push to a patch-review system, or use [short-lived feature branches](/short-lived-feature-branches/), even though this can be an impediment to throughput. -->
開発マシンでビルドが成功したら、チームで共有しているソースコードリポジトリへ変更をプッシュします。
実際にどこへプッシュするかはチームの方針によって異なります。
小規模なチームなら1日に何度も trunk へ直接プッシュするでしょう（[trunk へ直接プッシュする](/committing-straight-to-the-trunk/)を参照）。
大規模なチームならパッチレビューシステムや[短命の機能ブランチ](/short-lived-feature-branches/)にプッシュすることになりますが、スループットの向上を阻害する可能性があります。

## CIサービス：機械（ボット）が人間の作業を検証する

<!-- Every development team bigger than three people needs a CI daemon to guard the codebase against bad commits and timing mistakes.
Teams have engineered build scripts which execute quickly. This should apply from compilation through functional 
testing (perhaps leveraging mocking at several levels) and packaging. However, there is no guarantee that a developer would run the build script before 
committing. The CI daemon fulfils that role by verifying commits to be good once they land in the trunk. Enterprises 
have either built a larger scaled capability around their CI technology, so that it can keep up with the commits/pushes 
of the whole team or they use batching of commits which takes up less computing power to track and verify work. -->
間違った変更やコミットするタイミングの誤りからコードベースを守るため、開発者が3人以上いる開発チームにはCIデーモンが必要です。
まず、チームは簡単に実行できるビルドスクリプトを作成しなければなりません。
ビルドスクリプトは、ソースコードをコンパイルして、機能テスト（いろいろな段階でモックを活用することでしょう）を実行して、最終的にパッケージを作成します。
しかし、開発者がコミットする前にビルドスクリプトを実行してくれることは保証できません。
CIデーモンの役割は、あるコミットが trunk へ統合してよいかどうかを検証することです。
エンタープライズシステムを開発してきた会社では、大規模に展開できるCI技術を開発しています。
全ての開発チームのコミットやプッシュを追跡・検証したり、複数のコミットを束ねることで、全体的な計算機リソースを節約するためです。

### 情報ラジエーター

<!-- A popular
radiator-style visual indication of progress would be those shown as a left-to-right series of Green (passing) or Red 
(failing) icons/buttons with a suitably short legend: -->
ビルドスクリプトの状況をラジエーター形式で可視化するのが一般的です。
左から右に向かってそれぞれの段階の結果を表示します。
成功は緑色、失敗は赤色のアイコンやボタンで表現します。

![](pipelines1.png)

<!-- This should go up on TVs if developers are co-located. It should also be a click through from notification emails. -->
開発者が同じ場所で働いているならテレビ（のようによく見える場所）へ表示しましょう。
また、ビルド結果を通知する電子メールの本文中に記載したハイパーリンクからアクセスできるようにするべきです。

### 簡潔なビルド結果のお知らせ

<!-- The elapsed time between the commit and the "this commit broke the build" notification, is key. That is because the cost
to repair things in the case of a build breakage goes up when additional commits have been pushed to the branch. One of the 
facets of the 'distance' that we want to reduce (refer [five-minute overview](/5-min-overview/)) is the distance to break. -->
前のコミットからの経過時間と、「このコミットはビルドを壊しました」というメッセージを伝えるのが重要です。
ビルドが壊れた後に他のコミットが追加されていた場合、問題を解決するのが大変になるからです。
いろいろな「距離」がある中で私たちが短縮したいのは、ビルドを正常化するまでの時間なのです（[five-minute overview](/5-min-overview/) を参照）。

### パイプラインに関する参考情報

<!-- Note: Continuous Integration Pipelines are better described in the bestselling
[Continuous Delivery](/publications/index.html#continuous-delivery-july-27-2010) book. So are dozens of nuanced, 
lean inspired concepts for teams pushing that far. -->
[継続的デリバリー](/publications/index.html#continuous-delivery-july-27-2010) では、継続的インテグレーションのパイプラインについて詳しく説明されています。
リーンに着想を得たさまざまな考え方は、チームをより一層前進させてくれるでしょう。

## 高度な話題

### CIサービスはコミットごとにビルドするべきか、複数のコミットをまとめてビルドするべきか

<!-- Committing/pushing directly to the shared trunk may be fine for teams with only a few
commits a day. Fine too for teams that only have a few developers who trust each other to be rigorous on their 
workstation before committing (as it was for everyone in the 90's). -->
共有している trunk へ直接コミット（プッシュ）するやり方を許容できるのは、チームとして1日に数回しかコミットしない場合くらいです。
開発者の人数が少なくてお互いに信頼できている、かつ、それぞれの作業マシンでコミットする前にちゃんとビルドしている場合も許容できるでしょう（1990年代にはそれが当たり前でした）。

<!-- Setups having the CI server single threading on builds and the notification cycle around pass/fail will
occasion lead to the **batching** in a CI job. This is not a big problem for small teams. Batching is where one build is 
verifying two or more commits in one go. It is not going to be hard to pick 
apart a batch of two or three to know which one caused the failure. You can believe that with confidence because of the 
high probability the two commits were in different sections of the code base and are almost never entangled. -->
単一スレッドでビルドを実行するCIサーバーを用意して、**定期的にビルドを実行する** ジョブ（バッチジョブ）が成功と失敗を通知するようにします。
小規模なチームにとって大きな問題にはならないでしょう。
バッチジョブにするということは、1つ以上のコミットについてビルドを実行するということです。
コミットする人数が少なければ、ビルドを失敗させた原因を調査するのはそれほど難しいことではありません。
2つのコミットが、コードベース中の別々の場所を変更しており、絡み合っていないことを核心できるからです。

<!-- If teams are bigger, though, with more commits a day then pushing something incorrect/broken to trunk could be disruptive to
the team. Having the CI daemon deal with **every commit** separately is desirable. If the CI daemon is single-threading "jobs" there is a risk
that the thing could fall behind.  -->
チームの規模が大きくなると、1日のコミットも増えるため、ビルドの失敗はチームを断絶させてしまう可能性があります。
CIデーモンは **全てのコミットを** 別々にビルドするようにしておいたほうがいいでしょう。
CIデーモンが単一スレッドで「ジョブ」を実行するようになっていると、ビルド待ちのコミットが滞留するリスクがあります。

#### リーダーとフォロアーからなる CI 基盤

<!-- More advanced CI Server configurations have a master and many slaves setup so
that build jobs can be parallelized. That's more of an investment than the basic setup, and but is getting easier and 
easier in the modern era with evolved CI technologies and services.  -->
1つのリーダーと複数のフォロアーで並列にビルドを実行する、より高度なCIサーバーの構成が考えられます。
単純な構成よりも手間はかかりますが、最近の技術やサービスを活用すれば簡単に実現できるでしょう。

<!-- The likes of Docker means that teams can have build environments that are perfectly small representations of
prod infra for the purposes of testing.  -->
Docker のような技術があれば、本番環境とほとんど変わらないインフラをテストのために作成、実行できるのです。

#### 理由なくテストが成功する（緑色になる）ことはありません

<!-- Well written tests, that is - *there are fables of suites of 100% passing unit tests with no assertions in the early 2000's*. -->
ちゃんとテストを書いている、と言っても、2000年代の初頭には *アサーションの無いテストだけで構成された 100% 成功するテストスイート* みたいな都市伝説がありました。

<!-- Some teams focus 99.9% of their QA automation on functional correctness. You might note that for
a parallelized CI-driven *ephemeral* testing infrastructure, that response times for pages are around 500ms, where the
target for production is actually 100ms. Your functional tests are not going to catch that and fail - they're going to pass.  -->
QAオートメーションで、99.9% の機能正確性を目標にしている場合があります。
*一時的な* テスト環境で CI を並列実行している場合、画面の応答時間は 500 ミリ秒になる場合があります（本番環境では 100 ミリ秒なのに）。
いわゆる機能テストではこのような問題を発見することも、テストを失敗させることもできません。

<!-- If you had non-functional tests too (that 0.1% case) then you might catch that as a problem. Maybe it is best to move
such non-functional automated tests to a later pipeline phase, and be pleased that so many functional tests can run 
through so quickly and cheaply and often, on elastic (albeit ephemeral) CI infrastructure.   -->
非機能テストがあれば（全体の 0.1% くらいでしょうか）、問題を発見できます。
自動化した非機能テストはパイプラインの後ろの方に配置したほうがいいでしょう。
それ以外の多数の機能テストは、エラスティックな（一時的な）CIインフラで、短い時間で少ないリソースで実行できるはずです。

<!-- Here is a claim: Tests are never green (passing) incorrectly. The inverse - a test failing when the prod code it
is verifying actually good - is common. QA automators are eternally fixing (somehow) smaller numbers of flakey tests. -->
「理由なくテストが成功する（緑色になる）ことはありません」。
逆にいうと、本番環境でちゃんと動作しているコードでもテストが失敗する場合があるということです。
QAオートメーターはごく一部のフレーキーテストを永遠に修正し続けなければならないのです。

<!-- A CI build that is red/failing often, or an overnight CI job that tests everything that happened in the day - and is
always a percentage failing is of greatly reduced value. -->
CIによるビルドが頻繁に失敗するとか、夜間のCIジョブで昼間の変更を全てテストするとか、常に一定数のテストが失敗するようでは、CIの価値が損なわれてしまいます。

<!-- A regular CI build that by some definition is comprehensive, well written and **always green** unless there's a genuine
issue is extremely valuable. -->
素晴らしい価値のある標準的なCIビルドとは、本質的な問題を発見した場合以外では **常に成功する（緑色）** ものなのです。

<!-- Once you get to that trusted always green state, it is natural to run it as often as you can. -->
常に成功を維持していることが信用できるようになれば、できる限り実行するのが当たり前になるでしょう。

### CI はコミットの前に実行するべきか、コミットの後に実行するべきか

<!-- In terms of breakages, whether incorrect (say 'badly formatted'), or genuinely broken, finding that out **after the
commit** is undesirable. Fixing things while the rest of the team watches or waits is a team-throughput 
lowering practice.  -->
「ビルドが壊れた」理由には、何かが間違っている（書式が違う）とか、根本的に壊れているとかあるけれど、**コミットした後** に分かるようでは遅すぎます。
他のチームメンバーに原因が解消するのを待たせてしまうやり方は、チームとしてのスループットを低下させてしまうことになります。

![](ci_types.png)

<!-- Yellow = automated steps, Red = a potential to break build for everyone -->
黄色 = 自動化した処理、赤色 = ビルドの失敗が全員に影響する可能性がある場所

<!-- Note: for committing/pushing straight to the shared trunk, code review and CI verification can happen in parallel. Most
likely though is reviews happening after the CI daemon has cast its vote on the quality of the commit(s). -->
注意：共有している trunk へ変更をコミット（プッシュ）するのと、コードレビューやCIによる検証は同時に実行できます。
CIデーモンが変更内容の（コミットの）品質を通知してからレビューを開始する場合がよくあります。

<!-- Better setups have code-review and CI verification before the commit lands in the trunk for all to see: -->
コミットが trunk へ到達する前に、コードレビューと CI を実行できるようにするほうがいいでしょう。

![](ci_types2.png)

<!-- It is best to have a human agreement (the code review), and machine agreement (CI verification) before the commit lands in
the trunk.  There is still room for error based on timing, so CI needs to happen a second time **after** the push to the shared 
trunk, but the chance of the second build failing so small that an automated revert/roll-back is probably the best way 
to handle it (and a notification). -->
人間の合意（コードレビュー）と、機械の合意（CI）を保証できるのがいいところです。
タイミングの問題を完全に解消することはできません。
だから、コミットを trunk へ **プッシュした後**、もう一度 CI を実行しなければなりません。
二回目のビルドが失敗する可能性は極めて低いけど、revert やロールバックを自動化しておくといいでしょう。

### The high bar, today

<!-- Highest throughput teams have CI server setups that prevent breakage of the trunk. That means that commits are verified
before they land in the trunk to the extent where teammates can update/sync/pull. -->
高スループットなチームは trunk が壊れてしまうことを予防する CI サーバーを整備しています。
チームメンバーが更新、同期、取得できる trunk に入る前のコミットを検証できるようにしているということです。

<!-- The problem this solves is when the rate of commit into the trunk would be too high to have an auto-rollback on build
failure. In Google one commit lands in the trunk every 30 seconds. Few CI technologies (and source control systems) can 
keep up with that in a way that is not batching (to some degree of interpretation). You'd be stopping the line too often 
for humans to make sense of a train wreck of red builds, where only one two were actual breakages rather than just bad 
timing. -->
解決したいのは、ビルドが失敗したときの自動ロールバックが役に立たないくらいコミット頻度が高くなってしまった場合のことです。
Google では30秒に1回、新しいコミットが発生します。
バッチ化せずに（保留させずに）処理できるCIサービス（ソースコード管理システム）もあるようです。
ビルドの失敗が引き起こす問題を人間に認識してもらうため、頻繁に生産ラインを停止したくなるかもしれません。
ここでは、タイミングが悪かっただけでなく、実際に壊れたのは1回か2回だけでした。

<!-- It would not be computationally hard
to recreate a last-green-plus-this-commit contrived HEAD to verify a commit in isolation to the other 20 that arrived in 
the last ten minutes, but that would be a crazy amount of computing power to do so. Better to marshal the pending 
commit in a place where it looks like it is immediately following a previously known green (passing) commit and is not yet on 
the shared trunk. -->
過去10分間に到着した20のコミットと独立して今のコミットを検証するため、最後にビルドの成功したコミット系列に今のコミットを追加した履歴を HEAD として再作成するのは、計算量的にそれほど難しいことではないけど、膨大な計算機リソースを消費することになるでしょう。
滞留しているコミットを trunk へ追加する前に、最後にビルドが成功したコミットのすぐ後に行われたように整列させておくほうがいいでしょう。

<!-- That place has a name - a branch (or a branch of a fork the GitHub way). It is a perfect place to CI verify the commit
before auto-merging it to the shared trunk (if you want to auto-merge after code review approvals).  -->
そのための場がブランチ（GitHub なら fork も使えます）です。
CI サーバーが、trunk へ統合する前にコミットを検証するちょうどいい場所です（コードレビューが承認されてからマージさせたい場合）。

<!-- The new problem is how do you prevent that [short-lived feature branch](/short-lived-feature-branches/) from sleepwalking into a long-lived feature
branch with half a dozen developers keeping it from being 'complete' (somehow) and merged back. You cannot with tools
today, but it would be cool if you could have a ticking clock or count down on those branches at creation to enforce
its 'temporary' intention. -->
新たな問題は、半ダースもの開発者たちが、作業を「完了」して元の長命なブランチへマージする前の、中ぶらりな [短命な機能ブランチ](/short-lived-feature-branches/) をどんどん作ってしまうのをどうやって防ぐのか、ということです。
今のところちょうどいいツールはありません。
そういうブランチが「一時的」であることを強調するカウントダウンタイマーを設定できるといいですね。

<!-- Refer to [Game Changers - Google Home Grown CI and Tooling](/game-changers/index.html#google-s-internal-devops-2006-onwards) for more
information on the high commit rate CI stuff. Note too that they do not have a temp branch set up to facilitate that. -->
コミット頻度の高い環境における CI の役割について、[Game Changers - Google Home Grown CI and Tooling](/game-changers/index.html#google-s-internal-devops-2006-onwards) を参照してください。
見どころは、一時的なブランチを使っていないことです。

## ソフトウェア業界における CI デーモンに関する混乱

<!-- ThoughtWorks commissioned a survey - "No One Agrees How to Define CI or CD"{{< ext url="https://blog.snap-ci.com/blog/2016/07/26/continuous-delivery-integration-devops-research" >}}.
That the hypothesis of Continuous Integration being thought of as compatible with branching models other than Trunk-Based Development
was, unfortunately, shown to be true. Their chief scientist, Martin Fowler, writes about the general effect in his "Semantic Diffusion"
article{{< ext url="https://martinfowler.com/bliki/SemanticDiffusion.html" >}}.  -->
ThoughtWorks は "CI および CD の定義について誰一人合意に至ることはないだろう"{{< ext url="https://blog.snap-ci.com/blog/2016/07/26/continuous-delivery-integration-devops-research" >}} という調査を行いました。
調査では、CI とさまざまなブランチモデル（トランクベース開発は除く）に互換性があると考えている人が存在する、という仮説を立てていました。
そして、その仮説は肯定されました。
チーフサイエンティストの Martin Fowler は "Semantic Diffusion"{{< ext url="https://martinfowler.com/bliki/SemanticDiffusion.html" >}} でその一般的な効果？について解説しています。

<!-- Martin also wrote specifically on the
lamentable *pat on the back* that multi-active-branch teams give themselves when they set up a CI server/daemon for one
or all of those branches: "Continuous Integration Certification" 
{{< ext url="https://martinfowler.com/bliki/ContinuousIntegrationCertification.html" >}} and within that *a great
coin* "Daemonic Continuous Integration" for this effect. -->
また、複数の有効なブランチと、それぞれのブランチに対するCIサーバー・デーモンを準備していたチームについて苦言を呈する "Continuous Integration Certification"{{< ext url="https://martinfowler.com/bliki/ContinuousIntegrationCertification.html" >}} という文章を書いているし、*a great coin* では "悪魔の継続的インテグレーション" と呼んでいます。

{{< note title="This site's use of CI and Trunk-Based Development" >}} 
Given other popular branching models (that are not Trunk-Based Development) **also** benefit from CI servers watching 
for and verifying commits, this site is going to refer to the commit to a **enforced single shared source-control branch** practice as Trunk-Based Development.
{{< /note >}}

<!-- There are many CI technologies and services available for teams to use. Some are free, and some are open source.
Some store the configuration for a pipeline in VCS, and some store it somewhere else. In order to more smoothly support
[branch for release](/branch-for-release/), the best CI solutions co-locate the configuration for a pipeline in the same 
branch too. -->
今は開発チームが利用できるCIサービスがたくさんあります。
無償で使えるものすらあるし、OSSもあります。
パイプラインの設定をバージョン管理システムで管理するものもあるし、別のところで管理するものもあります。
柔軟に [リリースブランチ](/branch-for-release/) を扱えるのは、パイプラインの設定をバージョン管理システムの同じブランチで管理している場合です。

## サーバー（デーモン）の実装

* Jenkins{{< ext url="https://jenkins.io" >}} commercial service, for Jenkins Open Source{{< ext url="https://github.com/jenkinsci" >}} - on-premises installable
* Travis-CI{{< ext url="https://travis-ci.org" >}} - cloud
* Circle-CI{{< ext url="https://circleci.com" >}} - cloud
* ThoughtWorks' Go CD{{< ext url="https://www.gocd.io" >}} - cloud and on-premises install
* Codeship{{< ext url="https://codeship.com" >}} - cloud
* Atlassian's Bamboo{{< ext url="https://www.atlassian.com/software/bamboo" >}} - on-premises install
* JetBrains' TeamCity{{< ext url="https://www.jetbrains.com/teamcity" >}} - on-premises install
* Microsoft's TFS platform{{< ext url="https://www.visualstudio.com/tfs" >}} - on-premises install (built into larger platform)
* Codefresh{{< ext url="https://codefresh.io" >}} - cloud, on-premises and hybrid (bring your own runner) install

<!-- Note, for Jenkins, you can now use Pipeline DSL scripts (or Groovy) {{< ext url="https://jenkins.io/doc/book/pipeline/" >}} (formerly Workflow), or you can use Jenkins with GroupOn's DotCI{{< ext url="https://github.com/groupon/DotCi" >}} to co-locate the config
with the thing being built/verified in source-control. -->
Jenkins はパイプラインDSL（ワークフロー）を使えるようになっています{{< ext url="https://jenkins.io/doc/book/pipeline/" >}}。
また、GroupOn の DotCI{{< ext url="https://github.com/groupon/DotCi" >}} と組み合わせて、ソースコード管理システムに設定を格納することもできます。

# References elsewhere

<a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">10 Sep 2000, MartinFowler.com Article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.martinfowler.com/articles/originalContinuousIntegration.html">Continuous Integration - original version</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">18 May 2006, MartinFowler.com Article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.martinfowler.com/articles/continuousIntegration.html">Continuous Integration</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">18 May 2015, Hangout Debate</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.youtube.com/watch?v=30yN4hefrt0">Branching strategies and continuous delivery</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">02 Sep 2015, Conference Presentation</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.infoq.com/presentations/death-continuous-integration">The Death of Continuous Integration</a></td>
        </tr>
    </table>
</div>
