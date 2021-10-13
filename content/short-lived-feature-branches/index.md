---
date: 2016-06-03T20:10:46+01:00
title: 短命の機能ブランチ
weight: 63
---

<!-- sdfsdfsd -->

<!-- This branching model was facilitated with the advent of very lightweight branching that came with Git and Mercurial
in the mid-2000's, though there is evidence that Google were effectively doing the same in their Monorepo for some years before. -->
2000年代の中盤に登場した Git や Mercurial の軽量ブランチは、新しいブランチモデルを生み出しました。
何年も前から Google がモノリポで同じようなことをやっていたのがその証拠です。

<!-- Either as branching directly off master, or in a fork of the whole repository. These branches are destined to come
back as "pull requests" into the master/trunk. -->
master からブランチを作成するのも、リポジトリをフォークするのも、結局元のブランチへ「プルリクエスト」で統合するので同じことです。


<!-- wrweresd -->

<!-- With the Pull Request (and code review) advance, the cut-off point for team sizes that graduated from "direct to the trunk" to short lived feature branches moved lower.
While it was up to 100 before Git's lightweight branching, it is now up to 15 people. With 16 or more, the team is more
productive with short-lived feature branches, and corresponding CI daemons verifying those in advance of
commits landing in the trunk.  -->
プルリクエスト（およびコードレビュー）を応用すると、「trunk へ直接コミットする」モデルから短命の機能ブランチへ移行する、チームサイズの閾値（人数）を特定できます。
Git より前は 100 人でしたが、今は 15 人です。
16人以上のチームなら短命の機能ブランチでやっていくほうがより生産的ですし、ブランチに対応するCIデーモンで trunk へ統合する前のコミットを検証できます。

<!-- One key rule is the length of life of the branch before it gets merged and deleted. Simply put, the branch
should only last a couple of days. Any longer than two
days, and there is a risk of the branch becoming a long-lived feature branch (the antithesis of trunk-based development). -->
重要なルールの1つは、ブランチをマージしてから削除するまでの期間（時間的な長さ）です。
単純に、数日以内に削除する、ということにしておいてもいいでしょう。
2日以上そのままにしておくと、ブランチが長命化してしまうリスクがあります（トランクベース開発のアンチテーゼです）。

<!-- sdwerfhfsdfsd -->

<!-- Another key rule is how many developers are allowed congregate on a short-lived feature branch. Another simple answer:
the developer count should stay at one (or two if pair-programming). These short-lived feature branches are not shared
within a team for general development activity. They may be shared for the purposes of code review, but that is entirely
different to writing production code and tests. -->
また、短命の機能ブランチで作業できる開発者の人数を決めておくことも重要です。
単純に、1人だけ（あるいはペアプログラミングする2人だけ）にしておいてもいいでしょう。
機能ブランチをチーム全体で行う基本的な開発アクティビティの対象にするべきではありません。
あくまでもコードレビューのために共有するべきで、本番環境のためのコードやテストを作成するのとは全く違う話なのです。

<!-- hrhykj -->

![](/5-min-overview/trunk_pr.png)

<!-- Sometimes the community calls these 'task' or 'topic' branches, instead of long-lived feature branch. -->
コミュニティによっては、ブランチを「タスク」とか「トピック」とか呼ぶこともあるようです。

## マージする方向

<!-- Short-lived feature branches are real branches and merge is a first class concept. In the run up to completing work
on the short-lived feature branch, you will need to bring it up to date with master (trunk). That is an effective
merge whichever way you do it. Look at the branch at this moment, it may appear to be much younger than it was
before that operation. The changes have to now go back to master (trunk) in another merge operation. In GitHub, for
'pull requests' (or equivalent in other platforms), the user interface may handle that last merged back for you, and even
go as far as to delete the short-lived feature branch. -->
短命の機能ブランチは普通のブランチで、マージは通常の操作の一部です。
機能ブランチの作業を完了させるには、trunk へマージしなければなりません。
方法がなんであれ、本来やりたかったのはそういうことです。
マージ先のブランチは作業するときより若くなっているかもしれません（未来に進んでいる）。
だから、その差分は別の操作で trunk へマージしなければなりません。
GitHub なら「プルリクエスト」のユーザーインターフェイスで操作できますし、ブランチを削除したりできます。

<!-- rteghfhj -->

<!-- To recap: merges to the short-lived feature branch are allowed to bring it closer to HEAD of master (trunk). Merges
to master (trunk) are allowed only as part of closing out the short-lived feature branch (and just before deleting it). -->
覚書：短命の機能ブランチにマージしていいのは、trunk の HEAD に近づけるためだけです。
trunk へマージしていいのは、機能ブランチを完成させるためだけです（削除する前に）。

<!-- rteyyrt -->

## 2人の開発者が同時に別の機能ブランチで作業する

<!-- Say two features are being worked on concurrently: features X and Y. Both will take a day to complete, and two developers are
working independently on them. Or four developers if pair-programming is that team's way.  The reality of the merge back to
master/trunk, before the deletion of the short-lived feature branch, is that the a merge of changes **from** master/trunk
is often needed, before the merge **to** master/trunk. -->
2つの機能X と Yに関する作業が並行して行われているとしましょう。
どちらも完了するまで1日かかるものとします。
そして、2人の開発者が別々に作業するものとします（ペアプログラミングをしているなら4人です）。
機能ブランチには trunk **から** 頻繁に変更を統合しなければならないし、機能ブランチ **から** 変更を trunk に反映する前にもやらなければなりません。

![](slfb_pull-push.png)

<!-- Workflows include: -->
ワークフローには次のような手順が必要です。

<!-- * Attempt to merge to master/trunk and if that's blocked do a merge/pull from master/trunk before attempting the push again.
* Do a speculative merge/pull from master/trunk before attempting any push to master/trunk. -->
* 機能ブランチから trunk にマージする。また、trunk へプッシュする（マージする）前に trunk からマージ（プル）する
* 何かしら trunk へプッシュする前にとりあえず trunk からマージ（プル）する

<!-- dwewrerg -->

<!-- The latter leaves no trace if there's nothing to merge in from the other branch. -->
他のブランチから trunk へマージしていない限り、後者の手順では何も処理する必要がありません。

### 作業マシンで完結する

<!-- Really though Developers work on their own workstations. That is both for their "working copy" as well as their local Git clones complete with branches: -->
いわゆる「作業コピー」と、ブランチを含めて複製したローカルリポジトリの両方が対象になります。

![](slfb_working-copy.png)

<!-- fhtytrerwe -->

<!-- The white dots are just general development work (save files from the IDE), and the green dot is the same but validated by a local  build that passes and a maybe a local commit. -->
白点は一般的な作業（ファイルを保存するとか）、緑点は作業マシンでビルドできることを確認した、送信前のコミットです。

<!-- While each developer works, they maintain a local clone of the master/trunk and their own short-lived feature branch only.
Indeed for a period of time, their short-lived feature branch may not yet exist in the shared origin repo. -->
それぞれの開発者は複製したローカルリポジトリと短命ブランチだけを管理しています。
一定期間が過ぎたら、短命ブランチは共有リポジトリから消失します。

<!-- qetgrth -->

## 個人的な好み

<!-- At some point, the short-lived feature branch has to be brought right up to date with master (trunk) in a merge
operation before the result being merged back to trunk (and the branch deleted). There are a number of approaches
for this, and while teams may have a policy, some teams leave it to personal preference for the developer.  -->
機能ブランチはどこかの時点で trunk にマージするまでは、常に trunk を追従して最新化し続けなければなりません（最終的にブランチは削除しなければなりません）。
いろんな方法はありますが、チームとしてやり方を決めている場合もあるし、個人の好みに任せている場合もあるでしょう。

### Git stash

<!-- Some people do `git stash` before `git pull` before `git stash pop`. There's a chance that when you `pop` your
working copy may be in a merge clash situation that has to be resolved before you progress. This way will always
result in your change being a single commit, at the HEAD of the branch (as Subversion would always do). -->
`git stash` してから `git pull` して、`git stash pop` する人がそれなりにいると思います。
`pop` するときは、作業コピーと衝突して解決しなければならない場合があります。
関連する変更は最終的に1つのコミットになります（Subversion なら常に新しいコミットができます）。

<!-- werregg -->

### Git rebase

<!-- Some people do `git rebase`. Refer to a well written Atlassian document on this {{< ext url="https://www.atlassian.com/git/tutorials/merging-vs-rebasing" >}} as well as one from ThoughtBot {{< ext url="https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history" >}} that talks about `squash` too.  Even with this model,
you may encounter a merge clash, and have to resolve that locally before you can push the result anywhere, or do
further merges (to master hopefully). -->
`git rebase` を使う人もいると思います。
Atlassian のドキュメントは上手く書けているので参照してください {{< ext url="https://www.atlassian.com/git/tutorials/merging-vs-rebasing" >}}。
同じく、ThoughtBot {{< ext url="https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history" >}} でも `squash` について説明しています。
このやり方をしていると、どこかでマージが衝突し、変更をプッシュする前に解決しなければならない状態になります。

<!-- wqwedsfsg -->

## 落とし穴

<!-- Working towards a pull request can sometimes make you look past the power of streaming a series of small commits into trunk for
the benefit of all. High-throughput XP teams from the end of the 90's onward would stream tens of commits a day (per pair) 
into the trunk. Each commit would be an incremental step forward and able to go live as is, were the team to change plans
to do so.  A mistake in thinking for this way of working, is one pull-request for one Agile story/card (and no more). Getting 
out of that mind trap would be to practice (say) a pull-request for refactoring and see that integrated/merged into the trunk,
then a pull-request for a piece of new functionality (and integrated into trunk), then perhaps another refactoring (trunk 
integrated again). And for those three pull-requests they shared the same Story/card association, but perhaps had different 
short-lived branch names - each of which was deleted after merge/integration. -->
プルリクエストで作業していると、過去に小さなコミットを直接 trunk にプッシュしていた頃の良かった点を思い出すことが多くなります。
高スループットな XP チームでは、（ペアで）1日に10回はコミットしていたものでした。
それぞれのコミットは常に動作する段階的な変更になっているし、チームとしての計画そのものでした。
この作業の仕方における間違いは、ストーリーごとにプルリクエストを作ることです。
この落とし穴を避けるには、例えば同じストーリーについて、リファクタリングのプルリクエストを作成して trunk へマージし、新機能（の一部）を追加するプルリクエストを作成して trunk へマージし、新たなリファクタリングのプルリクエストを作成して trunk へマージしていくことです。
それぞれ別々の短命ブランチを作成して、マージしたら削除します。

## 契約違反

![](slfb_bad_sharing.png)

<!-- If you merged the part-complete short-lived feature branches to anywhere else, then you have broken the
contract of trunk-based development. For short-lived feature branches, these are **not** allowed: -->
部分的に完成した機能ブランチをマージするということは、トランクベース開発の契約に違反することになります。
短命の機能ブランチで **やってはいけないこと** は次のとおりです。

<!-- dgytjffdd -->

<!-- 1. intermediate merges to master (trunk) - at least where the commit was not able to go live on its own
2. merges (intermediate or not) to other people's short-lived feature branches
3. merges (intermediate or not) to any release branches (if you have them)
4. variations of #2 that are direct from/to the developers clone on their workstation
5. other developers joining you on your short-lived feature branches - at least who are not your pair-programming partner. -->
1. 途中で trunk へマージする。少なくとも本番環境で動作できる状態にしないといけません
2. 他の人が作業している機能ブランチへマージする
3. リリースブランチへマージする
4. #2の亜種。作業マシンに複製したローカルリポジトリで直接マージする
5. ペアプログラミングしてないのに他の人の機能ブランチで作業する

# 短命の機能ブランチとは別の方法

<!-- qweqwgtht -->

<!-- There is a more traditional alternative for smaller teams:
[Committing straight to the trunk](/committing-straight-to-the-trunk/). Many people still prefer this way of working, and if the whole team can do it without blowing up the build server, then great job team. -->
小規模なチームならより伝統的な [trunk へ直接コミットする](/committing-straight-to-the-trunk/) 方法があります。
今も多くの人々がこちらの方法で仕事しているし、ビルドサーバーを酷使しないで実現できるならそれが一番いいと思います。

<!-- There is also the patch-review way of working that was outlined in Google's use of Mondrian (2006), and consequentially delivered for non-Google teams with open source tools Gerrit and Rietveld (2007/8).  -->
Google が Mondrian (2006) でやっていたようなパッチをレビューする方法もありますし、OSS の Gerrit や Rietveld (2007/8) を使うこともできます。

<!-- These two alternatives, as well as short-lived feature branches are compared in [Styles and Trade-offs](/styles/).  -->
これら2つの方法と同じように、短命の機能ブランチでは [スタイルとトレードオフ](/styles/) を考慮しなければなりません。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">26 Apr, 2007, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://paulhammant.com/blog/branch_by_abstraction.html">Introducing Branch By Abstraction</a></td>
        </tr>
    </table>
</div>
 -->
- 26 Apr, 2007, Blog Entry：[Introducing Branch By Abstraction](https://paulhammant.com/blog/branch_by_abstraction.html)