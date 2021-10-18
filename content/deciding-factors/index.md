---
date: 2016-02-01T20:08:11+01:00
title: 決定に寄与する要因
weight: 21
---

## リリース周期

<!-- There are many factors that put pressure on the team to lengthen the interval between releases. Here are some. -->
次のリリースまでの期間を伸び縮みさせるさまざまな要因があります。

### イテレーションの期間

<!-- Different Agile teams focus on different iteration lengths. Some teams work at three-week iterations, some two,
and some one. Some teams do not have an iteration at all - particularly teams doing Continuous Delivery. -->
イテレーションの期間はチームによって異なります。
1イテレーションを3週間で実施するチームもいるし、2週間で実施するチームもいるのです。
特に、継続的デリバリーをやっているチームではイテレーションがない場合もあります。

<!-- If you are on a four week, or more iteration length, and each of those four weeks varies with proximity to the
release and cannot change that you may be in a bind. You may be able to follow the tenets of Trunk-Based Development, 
benefit from a Continuous Integration daemon (as all branching models can), but you are not going to be able to 
get all the way to Continuous Delivery (or Continuous Deployment). -->
イテレーションの期間が4週間以上、そして、その期間は変更できないリリース日までの残り日数で決まるようになっているとしたら。
トランクベース開発の考え方を参考にすることはできるでしょうし、（どんなブランチモデルであれ）継続的インテグレーションの利点を受け取ることはできるでしょう。
しかし、どうやっても継続的デリバリーや継続的デプロイメントができるようにはならないでしょう。

### ウォーターフォール

<!-- This one is easy. If you are doing waterfall, you are not close at all to the "do not break the build" mantra required
to do Trunk-Based Development. Consider a short-iteration Agile methodology like Extreme Programming. -->
ウォーターフォール型の開発プロセスでは、トランクベース開発に必須の考え方「ビルドを失敗させない」を守る必要がありません。
XP のように、短いイテレーションのアジャイル開発手法を検討してください。

### ストーリーの大きさ

<!-- Trunk-Based Development needs you to have small stories/tasks. Optimal is you starting work on a change, should only be a matter
of hours before completing and pushing it forward for code review. Longer than a couple of days, and there is going to be 
pressure to group a bunch of developers on a non-trunk branch and merge back later. Or worse, have developers make 
branches/forks from your in-progress branch. Or worse still, take intermediate merges from your branch, despite your 
change being incomplete.   -->
トランクベース開発では、ストーリーやタスクの大きさを小さくしなければなりません。
数時間で完了してレビューを始められるくらいが丁度良いでしょう。
2日以上かかるようだと、trunk でないブランチをマージしなければならない何人もの開発者が後に並んでしまうため、圧迫感に追いやられてしまいます。
もっとひどいのは、他の人たちがあなたの仕掛りブランチからブランチを作ったりし始めることです。
そうすると、作業が中途半端な状態で中間的なマージが必要になるかもしれません。

<!-- Generally speaking, the whole development team should do whatever it can to break stories/tasks into smaller stories/tasks.
In Agile, there is an INVEST mnemonic{{< ext url="https://en.wikipedia.org/wiki/INVEST_(mnemonic)" >}} that aids in the splitting
up of stories. -->
一般的に言われているのは、開発チームはストーリやタスクを出来るかぎり小さく分割するべきだということです。
アジャイル開発では INVEST{{< ext url="https://en.wikipedia.org/wiki/INVEST_(mnemonic)" >}} というニーモニックが有名で、ストーリーを分割する基準として役立ちます。

### ビルド回数

<!-- Keeping build times short is important in that it directly drives the number of commits a developer can do in a day.
If the build time is a couple of minutes, developers are likely to keep a high pace. If the build time is 30 minutes or
worse, developers change pace to match only a couple of commits a day and drop their throughput. -->
開発者が1日にできるコミット数に直接影響するため、ビルドの時間を短くするのは重要です。
ビルド時間が数分なら、開発者はまだ早いペースを保つことができるでしょう。
しかし、30分以上になると、1日に数回しかコミットできないくらいにまでスループットが低下してしまいます。

## バージョン管理ツール

<!-- Your VCS/source-control technology choice should facilitate update/pull/sync from the team's trunk many times
a day. The elapsed time for the update/pull/sync should be less than three seconds for the situation where you 
already had latest of everything. It should be no more than fifteen seconds the case of the shared trunk being ahead 
of you.  -->
バージョン管理ツールによっては、1日に何度も trunk を更新、取得、同期するのに役立つでしょう。
すでに最新になっている状態で trunk を更新、取得、同期するのにかかる時間は3秒以内にするべきです。
取得したソースコードが最新より遅れてしまっている場合を除いて、15秒以上かかるようにするべきではありません。

<!-- Older versions of ClearCase and PVCS Dimensions would be 30 minutes for the former and 45 minutes for the latter.
Double that if two team-mates were simultaneously trying to do the update/pull/sync operation. In that configuration, it 
was completely impossible for teams to practice Trunk-Based Development. -->
古い ClearCase なら30分かかることもあったし、PVCS Dimensions なら45分以上かかることもありました。
また、2人のチームメンバーが同時に更新、取得、同期すると、2倍時間がかかることもありました。
そんな状況でトランクベース開発をやるのは不可能です。

### リポジトリにバイナリファイルを格納しますか？

<!-- Depending on how many and how often they update, some SCM/VCS/source-control technologies are better than others.
Perforce can handle terabytes of binaries and textual source. Subversion aims to. Git can only do large binaries  if 
configured in Git-LFS mode{{< ext url="https://git-lfs.github.com/" >}}. -->
更新頻度にもよりますが、バージョン管理ツールによってはより短時間で処理できる場合があります。
Perforce は TB 級のバイナリファイルをテキストファイルと同じくらいの時間で処理できます。
Subversion も同じ方向を目指しています。
Git で巨大なバイナリファイルを扱うときは、Git-LFS {{< ext url="https://git-lfs.github.com/" >}} を使用します。

### リポジトリのサイズは気にしますか？

<!-- It is suggested that Git and Mercurial really should not have a history (ignoring Git-LFS) that exceeds 1GB. There are field reports of clones being
many times bigger than that and still working, but the development team suggests 1GB as the top limit. In order to use Git 
and push through that ceiling yearly, you might be in a situation where you have to keep archiving a repository, and starting 
a new one with no history to have more head room. Archiving might look like renaming the repository in GitHub, and turning it 
read-only so that all the history, issues, and code review comments are intact. Simpler clone-rationalization strategies might 
include recommending a "--shallow-since" date on cloning, or leveraging more recent partial clone capabilities to clone the full repo 
commit history without getting historical blobs until they are needed. -->
Git や Mercurial では 1GB 以上の履歴を持つべきではないと言われています（Git-LFS を除く）。
実際には 1GB よりずっと大きくなっても動作することは報告されていますが、Git の開発チームの推奨する上限値は 1GB です。
年に1度くらいしか使わないけど履歴が 1GB を越えている Git リポジトリがあるとしたら、そのリポジトリはアーカイブして、新しいリポジトリで履歴のない状態から始めたほうがいいでしょう。
GitHub のアーカイブ機能は、リポジトリ名を変更し、全ての履歴と課題とレビューコメントを読み取り専用に変更します。
単純にクローンするやり方を続けたければ、`--shallow-since` で日付を指定するか、より最近の部分クローン機能を活用して、完全なコミット履歴をクローンし、過去の blob は必要に応じて取得するようにするといいでしょう。

### ピーク時のコミット頻度

<!-- In "pure" Git, if a colleague beat you to a commit/push on a branch (their code-review and automated CI passed) when you
thought you were going to push, Git will inform you that you have to pull first. You pull, and you resolve your merge clashes 
(hopefully none), and then push again. On highly-active repos, you might struggle to find a window open long enough to push in 
this way without encountering the same problem. This is known as the "race to push". -->
「純粋な」Git は、あなたより先にチームメンバーがコミットをプッシュした場合（コードレビューも、自動化された CI も通過した場合）、あなたがプッシュしようとすると、先に最新化するように促します。
trunk を取得して、マージの衝突を解消して（運が良ければ衝突しません）、再びプッシュします。
リポジトリが活発に更新されている場合、他の更新と衝突せず安全にプッシュできるタイミングを見つけるのに苦労させられることになります。
これが「プッシュの競合」という問題です。

<!-- Fork-based "pull requests" and similar branch-based "merge requests" in hosted git services solve this to a degree, with robots
keeping pull-request branches abreast of `origin:master` automatically as long as no conflicts arise. -->
フォークによる「プルリクエスト」や、ブランチによる「マージリクエスト」なら、それらの機能を提供するサービスが、プルリクエストの対象ブランチと `origin:master` に解消不可能な衝突が発生するまでは、ある程度自動的に解決してくれます。

<!-- Even with Pull Requests, however, very high commit frequencies to the shared repo means contention and an artificial
serialization. Microsoft acknowledged this as one
of the motivations to their Git Virtual File System (~~GitVFS~~ ~~GVFS~~ VFS for Git{{< ext url="https://vfsforgit.org/" >}}). -->
プルリクエストでやっているとしても、コミット頻度の高い共有リポジトリでは、競合と人為的な逐次化が発生してしまいます。
Microsoft がこの問題を解決するために開発したのが Git Virtual File System です（~~GitVFS~~ ~~GVFS~~ VFS for Git{{< ext url="https://vfsforgit.org/" >}}）。

<!-- {{< quote title="Git has critical serialization points that will cause a queue to back up badly" >}}
<span>&mdash; Brian Harry</span><br>
<span style="margin-left: 30px">Refer to Brian's "More on GVFS" blog entry<span class="rref"><a class="inline-ref" style="border-bottom: 0" target="_blank" href="https://blogs.msdn.microsoft.com/bharry/2017/02/07/more-on-gvfs/"><img style="padding: 0 0 0 3px; width: 16px; height: 14px" src="/images/ext.png" alt=""></a></span></span>
{{< /quote >}} -->
{{< quote title="Git にはバックアップのキューを壊してしまう致命的な直列化位置があります" >}}
&mdash; Brian Harry（詳しくはブログ記事 ["More on GVFS"](https://blogs.msdn.microsoft.com/bharry/2017/02/07/more-on-gvfs/) を参照）
{{< /quote >}}

<!-- We're sure that within a few years, Git will be able to handle huge scale too. Whether with the Microsoft technologies, or
something else. -->
数年後には Git がより巨大な履歴を扱えるようになっていると考えています。
Microsoft が実現するかどうかはわかりません。

## コンウェイの法則

<!-- The organization creates applications and services that reflect the organization's own structure{{< ext url="https://en.wikipedia.org/wiki/Conway's_law" >}}.
If your organization feels like this, and a Monorepo does not feel right, then MicroServices could be the direction for you. -->
「ある組織の作成したアプリケーションやサービスは、組織構造を反映した構造になる」{{< ext url="https://en.wikipedia.org/wiki/Conway's_law" >}} という法則です。
自分の組織にそのような印象を抱いたら、そして、モノレポが合わないようなら、マイクロサービスへ変えてみるといいかもしれません。

## データベースマイグレーション

<!-- In order to manage database schemas in a Trunk-Based Development way you will need to find a way to handle table-shape changes under source control, and even
manage existing data where new/changed columns have happened. Pramod Sadlage and Scott Amber's book 
"Refactoring Databases: Evolutionary Database Design"{{< ext url="https://www.amazon.com/Refactoring-Databases-Evolutionary-paperback-Addison-Wesley/dp/0321774515" >}}
goes into that much more, as does the [Continuous Delivery](/continuous-delivery/) book. -->
トランクベース開発でデータベーススキーマを管理するには、ソースコードと同じ場所でテーブル定義を管理するだけでなく、カラムを追加したり変更したりするときに既存のデータを管理する方法が必要です。
Pramod Sadlage と Scott Amber の "Refactoring Databases: Evolutionary Database Design"{{< ext url="https://www.amazon.com/Refactoring-Databases-Evolutionary-paperback-Addison-Wesley/dp/0321774515" >}} はとても参考になるでしょう。
また、[継続的デリバリー](/continuous-delivery/) も参考になるでしょう。


## コードの共同所有

<!-- Trunk-Based Development teams typically have common code ownership rules around contributions to different parts
of the source tree. If they do not have a fully egalitarian system, they have objective rules for contributions to the tree.  -->
トランクベース開発では、コードベースのさまざまな部分に対する貢献に、共同所有権を要求します。
完全に平等な仕組みがないときは、貢献に関する客観的なルールを整備しているはずです。

<!-- Rules that focus on standards and come with a promise of a prioritized and fair code review. Trunk-Based Development
teams might have fine-grained write permissions for directories within the trunk, but **never** have any impediment 
to reading files in the trunk - everyone can see everything. -->
そんなルールは標準を意識しつつ、優先順位を決めて、正式なコードレビューを実施することを約束しているでしょう。
トランクベース開発ではディレクトリごとに適切な粒度の書き込み権限を割り当てるようになるでしょう。
しかし、ファイルの読み取りは **決して邪魔しません**。
誰もが全てを読み取れるのです。

<!-- ## Parallelization

## CI capacity

## QA style

## Environments

### Developer workstations

### Shared services infra

## Code review

## Live config changes

## Tech debt accumulation

## Incident handling

## Backlog management

-->
