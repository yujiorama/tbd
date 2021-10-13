---
date: 2016-06-02T20:10:46+01:00
title: trunk へ直接コミットする
weight: 62
---

<!-- Some teams will choose to commit/push straight to the trunk. Most likely it is because they are a small team with each
team member knowing what the others are up to. **Their build is probably fast and relatively exhaustive**, and they 
may well seldom experience a build breakage. If the build does break (post integration into trunk/master) then they most likely 'revert' the 
commit straight away, possibly locking the trunk for a short period of time while that is performed. If the team is 
really small (say three or four), in which case the team might allow someone to fix the build quickly and commit that 
in order to get the build green again. -->
trunk に直接コミットしているのは、ほとんどが小規模なチームで、チームメンバーがお互いを良く知っている場合が多いでしょう。
**おそらく高速で徹底的なビルドをしていることでしょう**し、ビルドが失敗した経験を積み重ねているでしょう。
コミットを trunk に統合した後でビルドが失敗したら、最後のコミットを revert するでしょう。
その間 trunk をロックするかもしれません。
せいぜい3人か4人のチームなら、ビルドを壊した原因を誰かがすぐ修正してくれるでしょう。

<!-- In the 2000's many Trunk-Based Development teams might have numbered up to 100 committers. They may have been extremely
rapid with their `reverts` (lock trunk, revert, kick off the CI daemon again, unlock trunk if green again). If there was no build-automation server, they would 
have performed check-in activities that 1997's C3 team{{< ext url="https://en.wikipedia.org/wiki/Chrysler_Comprehensive_Compensation_System" >}} 
would have recognized, because they wanted that human 
assurance that gated check-ins are all that is needed to keep the build green. Namely, developers holding an 
"I/we are checking in now, nobody else should be". They run the full build after bringing their checkout up to date 
and commit/push if green. Indeed that ceremony is a key part of the [Continuous Integration](/continuous-integration/) advances 
and is now part of Agile generally, and Extreme Programming in particular. These days teams doing this practice are likely 
to be much smaller (say less than 16) because of the advent of alternatives (see below). That said, there are still some large teams 
working this way. -->
2000年代にトランクベース開発をしているほとんどのチームには、およそ100人くらいのコミッターがいるようです。
高速に `revert` する仕組みを整えています（trunk をロックして、コミットを revert して、CI を実行して、成功したら trunk のロックを解除する）。
もしビルドサーバーがなかったら、1997年の C3 プロジェクト{{< ext url="https://en.wikipedia.org/wiki/Chrysler_Comprehensive_Compensation_System" >}} のようなチェックインアクティビティを実施するでしょう。
ビルドを正常に保つため、人間が保証しなければならないからです。
つまり、開発者としては「私がチェックインしているところなので他の人は触らないで」という考え方のままなのです。
ソースコードを最新化して自分の変更を統合して、ビルドが成功したらプッシュするのです。
この作法は [継続的インテグレーション](/continuous-integration/) を進めていく重要な要素ですし、アジャイル開発や XP において当たり前の要素でした。
今はこのプラクティスをやっているチームは少なくなりましたが、それは代替手法が利用できるようになったからです。
それでも、いくつかの大規模なチームではこのやり方を続けています。

# 利点

<!-- It is easier to objectively verity the correctness of your commits yourself (optimally with a pair-programming partner), then commit/push at moments of convenience. That is, easier than pushing into a code-review system for approval from a teammates that risks being interruptive to their workflow. Indeed there is a greater likelihood that this this style or working becomes a flow of small commits into the trunk, with each of those being an incremental step forward, and perfectly able to go-live itself while the larger story/card remains incomplete. -->
自分のコミットの正しさを客観的に証明して（ペアプログラミングしているならもっと良い）、都合のいいときにコミットをプッシュするのは簡単です。
コードレビューシステムにコミットをプッシュしてチームメンバーに承認してもらうよりも簡単です（彼らのワークフローに割り込んでしまうリスクがある）。
実際には、コミットの（変更の）規模を小さくする効果があります。
それぞれのコミット（変更）は段階的になるし、（相対的に大きな）ストーリーが未完了でも、コードとしては完全に動作する状態を維持できるからです。

# 課題

<!-- Committing (and pushing) straight to the trunk has a challenge. Principally, someone could commit/push code that breaks the build, and the server(s) setup to guard Continuous Integration don't catch that for some time after the commit is available for teammates to pull/sync to their dev-workstation for unrelated work. -->
基本的に誰でもビルドを壊してしまう可能性があるし、他のチームメンバーが間違ったコミットを取得してからCIサーバーがビルドを壊してしまうことに気付く場合もあるからです。

<!-- Risk mitigation is **everyone** running the full build (the same build the CI demon would do) before the commit/push, and only pushing to
trunk if that passes. **This is an essential integration activity**. This is the habit of the XP teams from the end of the 90's, and there's 
no reason any team would dispense with that in the years since. Indeed, it is valuable for ALL branching models. -->
リスク緩和策は **全員が** ビルドを実行して（CIと同じ操作）、成功した場合だけプッシュすることです。
**本質的な統合アクティビティです**。
1990年代のXPチームにおけるよい習慣でしたし、今でもそれをやらない理由はありません。
どんなブランチモデルでも共通して実施できる習慣なのです。

<!-- If this is locked in as a team requirement, your new challenge is to keep the full build fast. Fast is say one minute, and slow is ten or above. Compile and pure unit tests (no threads, sockets, file IO) is where good builds focus their development effort. Any following "integration test" build steps that use threads, listen on sockets, or do significant file IO should be minimized as far as possible without reducing meaningful coverage. The best trick for that is changing some integration tests into pure unit tests, which isn't always easy. -->
チームの要件になっているとしたら、次の問題はビルドをできるだけ早くすることです。
1分未満なら早いと言えますが、10分以上では遅すぎます。
開発に効果があるのはコンパイルと純粋なユニットテスト（シングルスレッドで、ソケットやファイル入出力を伴わない）です。
スレッドを使ったり、ソケットを待ち受けしたり、大量のファイル入出力を伴う「結合テスト」は、必要なカバレッジを残しつつできるだけ最小限に留めたほうがいいでしょう。
一部の結合テストを純粋なユニットテストへ変更できるなら一番いいのですが、簡単な場合ばかりではありません。

<!-- Some teams have revert policies for commits that land in trunk/master that are proven as "broken" later in CI. That could be an activity for a build-cop who's going to communicate with the dev team about locking the trunk to achieve that. Or it could be a bot activity and happen instantly, as Google do in-house (35K committers in one trunk). -->
コミットを trunk へ統合した後に実行した CI で「壊れた原因」として識別したコミットは revert する方針でやっているチームもあります。
ビルド警察（人間）の役割は、trunk をロックしていいか開発チームと相談することです。
ボット（プログラム）にその場で処理させることもできるでしょう（同じ trunk に 35,000 人のコミッターがいる Google の話）。

<!-- Some teams have some scripting in place to ensure that developers only pull/sync commits to their dev-workstations that CI has marked as passing. That could be as simple as keeping a commit ID (number or hash depending on your VCS tool) on a website somewhere, and writing a wrapper script for git-pull (or svn up) that ignores commits that happened after that one. Pushing back, with that way of working is harder in Git and Mercurial as they requires you to have pulled HEAD revison before you can push back. Subversion and Perforce don't have that limitation. -->
CIが問題なしと印を付けたコミットだけが、作業マシンに取得、同期されるようなスクリプトを使っているチームもあります。
単純にどこかに保管したコミットIDを（数値かハッシュ値かはバージョン管理ツールによって異なります）、git-pull や svn up のラッパースクリプトから参照するだけです。
コミットする前にローカルリポジトリのHEADを最新化しなければならない Git や Mercurial では難しいですが、Subversion や Perforce ならそんなことはありません。

# 他の方法

<!-- That modern alternative that allows development teams to scale up without having a bottleneck around check-ins or increased risk of broken builds: [Short-Lived Feature Branches](/short-lived-feature-branches/). -->
[短命の機能ブランチ](/short-lived-feature-branches/) は、チェックインのボトルネックを作ったり、ビルドを失敗させるリスクを増やすことなく、開発チームの規模を拡大できる近代的な手法です。

<!-- There are also teams that send patches to review systems like Gerrit and Rietveld, instead of committing/pushing straight to trunk/master. Google pioneered this with their in-house Mondrian system in 2006, and Gerrit and Rietveld were made in the image of that. Facebook's Phabricator is another that came later. As well as code review, build-automation systems objectively verify the correctness of the proposed changes, leaving you with high confidence that the following merge/integration into trunk/master will yield a similar positive result when the same infrastructure kicks in later for CI purposes. It is important to note that the automation you attach to commits/pushes to non-trunk branches (or patch queue/review systems) is not Continuous Integration itself. -->
trunk へコミットする代わりに、Gerrit や Rietveld のようなレビューシステムにパッチを送信しているチームもあります。
Google の社内では 2006 年から Mondrian というツールを使用しており、Gerrit や Rietveld はその後継ツールとして開発されたのでしょう。
Facebook では Phabricator というツールを開発していました。
コードレビューと、自動ビルドシステムから受信した客観的な変更の正確さに基づいて、コミットが trunk へ統合されてから同じCIシステムでビルドしても、良好な結果になることを、高精度に保証できます。
重要なのは、trunk 以外のブランチ（あるいはパッチをキューイングしたりレビューするシステム）へプッシュしたコミットを契機に実行する自動化処理は、継続的インテグレーションそのものではないということです。

<!-- These two alternatives, as well as committing straight to the trunk are compared in [Styles and Trade-offs](/styles/).   -->
これら2つの方法と同じように、trunk へ直接コミットする方法では [スタイルとトレードオフ](/styles/) を考慮しなければなりません。
