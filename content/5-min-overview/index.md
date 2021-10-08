---
date: 2016-01-01T09:42:02+05:00
title: 5分で分かるトランクベース開発
weight: 11
---

## 距離

<!-- {{< quote title="Branches create distance between developers and we do not want that" >}}
&mdash; Frank Compagner, Guerrilla Games
{{< /quote >}} -->
{{< quote title="ブランチは開発者と私たちの間に意図しない隙間を作り出してしまう" >}}
&mdash; Frank Compagner, Guerrilla Games
{{< /quote >}}

<!-- Assuming any network-accessible source control, physical distance is mitigated by AV technologies including
screen sharing. So we will not worry about that so much these days. -->
ネットワーク経由でアクセスできるソースコード管理システムがあれば、画面共有を含む自律的な仮想化技術（AV technologies）で物理的な距離を越えられます。
しばらくの間、それが問題になるとは思っていませんでした。

<!-- Frank's 'distance' is about the distance to the integration of code from multiple components/modules/sub-teams for a
binary that could be deployed or shipped. The problematic distance is to code not yet in the single shared branch, 
that might: -->
Frank の言う「距離」とは、展開したり出荷したりできるバイナリを構成する複数のコンポーネント・モジュールのソースコードを統合するまでの距離のことです。
共有している単一のブランチ以外にソースコードがあると、この距離が問題になりやすいのです。

<!-- * break something unexpected once merged
* be difficult to merge in.
* not show that work was duplicated until it is merged
* not show problems of incompatibility/undesirability that does not break the build -->
* マージすると意図しないところが壊れる
* そもそもマージするのが難しい
* マージするまで重複する作業が明らかにならない
* ビルドは壊さないけど非互換性や望ましくない問題が明らかにならない

<!-- Trunk-Based Development is a branching model that reduces this distance to the minimum.  -->
トランクベース開発はこのような距離を最小化するブランチモデルです。

## トランクベース開発とは何か

{{< note title="Notes" >}}
<!-- * Use of "Developers" throughout this site, means "QA-automators" for the same buildable thing, too.
* When we say 'the trunk' on this site, it is just a branch in a single repository that developers in a team are focusing on 
for development. It may be called 'master'. That hints at the fact that the branch in question may literally not be 
called 'trunk' at all. -->
* このドキュメントで「開発者」が登場するところには、同じものをビルドできる存在として「QAオートメーター」も当てはまります
* このドキュメントでは「trunk」が、あるリポジトリの1つのブランチを指すことにします。
  「master」の場合もあります。
  実際には違う名前でもそういう役割をしているなら trunk と呼ぶことにします。
{{< /note >}}

<!-- There are many deciding factors before a development team settles on Trunk-Based Development, but here is a short overview
of the practices if they do: -->
開発チームがトランクベース開発を始めるにあたって、決定的な要因がたくさんあるでしょう。
このセクションでは具体的なプラクティスの概要を説明します。


### 「仕掛品」をリリースできるかどうか

<!-- Trunk-Based Development will always be **release ready** -->
トランクベース開発では常に **リリースできる状態** を保ちます。

<!-- If an executive manager visited the development team and commanded "Competitor X has launched feature Y, go
live now with what we have", the worst response would be "give us one hour". The development team might have been very 
busy with tricky or even time-consuming tasks (therefore partially complete), but in an hour, they are able to go live 
with something just stabilized from the trunk. Perhaps they can do it in less than an hour. The rule, though, is to **never break 
the build**, and **always be release ready** because the CIO or the business may surprise you. -->
開発チームの様子を見に来た役員がこう言いました。
「競合のX社は新機能Yをリリースしたばかりだ。私たちも最新版をリリースしなさい」

最悪の回答は「1時間待ってください」です。
開発チームは複雑で時間のかかるタスクに取り掛かっているので（部分的に完了している場合もあります）、空き時間がありません。
しかし、1時間あれば trunk から何か安定したものをリリースできるでしょう。
もしかしたら1時間以内に対応できるかもしれません。
ルールは**決してビルドを失敗させないこと**と、**常にリリースできる状態を保つこと**です。
そうしないと CIO やビジネス担当者に驚かされてしまいます。

#### どこからリリースするのか

<!-- A key facilitating rule is that Trunk-Based Development teams exclusively **either** release directly from the
trunk - see [release from trunk](/release-from-trunk/), **or** they make a branch from the trunk specifically for 
the actual release. See [Branch for release](/branch-for-release/).
Teams with a higher release cadence do the former, and those with a lower release cadence do the latter.  -->
トランクベース開発を促進する中心的なルールは、リリースするブランチをチームとして排他的に選択することです。
trunk から直接リリースする場合（[トランクからリリース](/release-from-trunk/) を参照）と、trunk から作成したリリース専用のブランチからリリースする場合（[リリースブランチ](/branch-for-release/) を参照）です。
前者のほうがリリース周期を短くできるでしょう（後者のほうがリリース周期は長くなるでしょう）。

### チェックアウト（クローン）

<!-- All developers in a team working on an application/service, clone and checkout from the trunk. They will
update/pull/sync from that branch many times a day, **knowing** that the build passes. Their fast 
source-control system means that their delays are a matter of a few seconds for this operation. They are now 
integrating their teammates' commits on an hour-by-hour basis. -->
全てのチームメンバーはアプリケーションやサービスの作業をするため trunk をチェックアウト（クローン）します。
ビルドが通ることは **分かっている** ので、1日の間に何度も更新、取得、同期します。
高速なソースコード管理システムを使用できるので、実際の操作にかかる時間はほんの数秒です。
たったそれだけで、常にチームメンバーのコミットを統合できるのです。

### コミット

<!-- Similarly, developers completing a piece of development work (changes to source code), that does not
break the build, will commit it back to the trunk. That it does not break the build should be provable. The granularity of that commit (how many a developer 
would implicitly do a day) can vary and is learned through experience, but commits are typically small. -->
何らかの作業（ソースコードの変更）が完了し、ビルドが失敗しないことを確認したら、trunk へ込みとtします。
ビルドが失敗しないことを確実に保証できるようにしなければなりません。
基本的にコミットの粒度は（1人の開発者が1日に行う作業量は）小さくなるのですが、経験を通じて学んでいきましょう。

<!-- The developer needs to run the build, to prove that they did not break anything with the commit **before** the commit
is pushed anywhere. They might have to do an update/pull/sync before they commit/push the changes back to the team's 
version control server, and additional builds too. There's a risk of a race condition there, but let us assume that is not 
going to happen for most teams. -->
開発者は、自身のコミットがビルドを失敗させないことを証明するため、どこかに **プッシュする前に** ビルドを実行しなければなりません。
チームのソースコード管理システムへコミットをプッシュしてしまう前に（そもそもコミットする前に）、更新、取得、同期してビルドも確認しておかなければなりません。
衝突のリスクは常に存在するのですが、めったに発生しないものと考えていいでしょう。

### コードレビュー

<!-- The developer needs to get the commit reviewed. Some teams will count the fact that the code was 'pair programmed'
as an automatic review. Other teams will follow a conventional design where the commit is marshaled
for review before landing in the trunk. In modern portal solutions, marshaled nearly always means a branch/fork (Pull
Request) that is visible to the team. -->
開発者は自分のコミットをレビューしてもらわなければなりません。
「ペアプログラミング」したコードを自動的にレビューしたコードとして扱うチームもあるようです。
コミットがレビューできる状態になったら、trunk へプッシュする前に形式的なレビューをするチームもあるそうです。
新しく始めやすいやり方として、レビューできる状態をブランチあるいはフォーク（プルリクエスト）として、チームから見える状態にする方法があります。

![](trunk_pr.png)
([key](/key/))

<!-- ^ the speech bubbles are stylized code review comments -->
^ 吹き出しは、自動的に生成されたレビューコメントです

<!-- Code review branches can (and should) be
deleted after the code review is complete and be very short-lived. This is tricky for teams new to Trunk Based 
Development.  -->
コードレビュー用のブランチは、レビューが完了したらすぐに削除しなければなりません（できるだけ短命にしなければなりません）。
トランクベース開発を始めたばかりのチームには少し難しいかもしれません。

<!-- Note: You want to keep
the commentary/approval/rejection that is part of the review for historical and auditing purposes, but you do not want to 
keep the branch. Specifically, you do not want the developers to focus on the branch after the code review and merge back
to the trunk. -->
注意：レビューの途中状態（コメントあり・承認・却下）は監査などのために記録しておきたいけど、ブランチは残したくないでしょう。
特に、レビューが完了し、trunk にマージしたブランチで、再び作業してもらいたくないはずです。

## 安全策

<!-- [Continuous Integration](/continuous-integration/) (CI) daemons are set up to watch the trunk (and the
[short-lived feature branches](/short-lived-feature-branches/) used in review), and as quickly and completely as possible 
loudly/visibly inform the team that the trunk is broken.  Some teams will lock the trunk and roll-back changes. Others 
will allow the CI server to do that automatically. -->
[CI](/continuous-integration/) ツールが trunk を監視するように設定します（レビュー用の [短命フィーチャブランチ](/short-lived-feature-branches/) も監視しましょう)。
そして、trunk のビルドが失敗したことをできるだけ早く、できるだけ正確に、騒々しく、分かりやすくチームに伝えるようにしてください。
trunk を変更不可能にして、ビルドを失敗させたコミットを取り除く場合もあるそうです。
そして、その処理を CI ツールで自動化している場合もあるそうです。

![](5trunk1.png)
([key](/key/))

<!-- The high bar is verifying the commit before it lands in the trunk. Short-lived Pull Request branches are the modern
place for that. -->
trunk へコミットする前に検証するやり方は敷居を高くしてしまいます。
短命のブランチ（プルリクエスト）で検証するのが最近のやり方です。

## 開発チームの約束

<!-- As stated, developers are pledging to be rigorous and not break the build. They're also going to need to consider
the impact of their potentially larger commits, especially where renames or moves were wholesale, and adopt techniques
to allow those changes to be more easily consumed by teammates. -->
前に述べたとおり、開発者にはビルドを失敗させないことを誓ってもらいます。
特に、名前の変更やファイルの移動を伴う場合は、コミットが大きくなる（変更量が多くなる）可能性があるので、影響の大きさを意識しなければなりません。
そして、チームメンバーがそのような変更を無理なく受け入れられるようにする便利な方法がないか、検討しなければなりません。

## 「距離」を深掘りする

<!-- Problematic 'distance' has a few tangible examples: -->
「距離」によって次のような症状が発生する場合があります。

<!-- * Late merges of development that happened more than a couple of days ago.
  * Difficult merges in particular
* A breaking build that lowers development team throughput, and diverts resources while it is being fixed -->
* 2日以上前の変更をマージしている
* ビルドの失敗が開発チームのスループットを低下させているし、修正のためにリソース（時間）を割かれている

# さまざまな参考文献

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">03 Sep 2009, MartinFowler.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://martinfowler.com/bliki/FeatureBranch.html">FeatureBranch</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">16 Jun 2015, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://www.alwaysagileconsulting.com/articles/organisation-pattern-trunk-based-development">Organization Pattern: Trunk-Based Development</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">04 Oct 2016, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.michielrook.nl/2016/10/branches-considered-harmful/">Branches considered harmful</a></td>
        </tr>
    </table>
</div>

 -->
- 2009年9月3日 [FeatureBranch](https://martinfowler.com/bliki/FeatureBranch.html)
- 2015年1月16日 [Organization Pattern: Trunk-Based Development](http://www.alwaysagileconsulting.com/articles/organisation-pattern-trunk-based-development)
- 2016年10月4日 [Branches considered harmful](https://www.michielrook.nl/2016/10/branches-considered-harmful/)
