---
date: 2016-03-01T20:08:11+01:00
title: バージョン管理システムの機能
weight: 31
---

<!-- **Desirable VCS features, that is** -->
**バージョン管理システムの理想的な機能**

<!-- To great degree, Trunk-Based Development is possible on any Version Control System (VCS) that does atomic commits (spoiler: all do
that came after CVS).  Productivity and governance are what divides them, though. -->
分割不能なコミットができるようになった CVS 以降のあらゆるバージョン管理システムなら、それなりにトランクベース開発ができるでしょう。
生産性と統治性が差別化要因になります。

## 生産性

<!-- It turns out there are many
productivity related reasons that teams quit one technology and go to another. It does not matter whether the tool is a commercial
or an open source one. At least to its end users it does not. -->
あるチームが別のツールを使うようになった理由として、生産性に関連するさまざまな問題が挙げられていることが分かっています。
商用製品とOSSの区別はありません。
エンドユーザーとしての判断です。

<!-- By speed we mean two things primarily: -->
速度には主に2つの意味があります。

<!-- 1. The speed at which we can pull/update/sync changes out of a remote server repository
2. The speed at which we can commit/push changes back to that remote server -->
1. リモートリポジトリから取得、更新、同期する速度
2. コミットをリモートリポジトリにプッシュする速度

<!-- And three other secondary things that support little and often: -->
そして、3つの派生的な意味があります。

<!-- 1. Advanced Merging
2. Code Review
3. Continuous Integration -->
1. 高度なマージ機能
2. コードレビュー
3. 継続的インテグレーション

### 取得、更新、同期の速度

<!-- If you have at least once done a checkout of the source of the project, any subsequent pull/update/sync is going to bring down
differences from the previous checkout or update.  If you do two updates back to back quickly, the second one is likely to bring 
down nothing.  The time taken for the version control tool to determine that nothing is due from the remote master 
repository is the biggest clue as to how intrinsically fast it is.  -->
一度でもソースコード全体をチェックアウトしていれば、その後は差分だけを取得、更新、同期すればよくなります。
立て続けに最新化を繰り返しても、二回目の最新化では何も取得しません。
リモートリポジトリから取得するデータが存在しないことを、バージョン管理ツールが判断するのにかかる時間は、主観的な速度にとって重要な手がかりになります。

<!-- Technologies that only keep head revision on the checkout, ordinarily have to walk the entire directory structure
looking for changed files and do handshaking to the server for each one. That is definitely Subversion and its predecessor CVS. 
Batching of those exchanges speeds it up, but there is still a slowdown related to the breadth and depth of the source
tree. Perforce makes the operation faster because the server-side is poised for the sync operation at all times, by 
keeping your tree and which revision you have for each file in RAM. It does this at the cost of maintaining read-only
bits for files (be sure and use an IDE that silently handles the Perforce interactions). Perforce can effectively be much
faster for this back-to-back pull/update/sync test of speed because it kinda already knows the answer to the question. -->
最新バージョンだけをチェックアウトするには、リモートリポジトリの全てのディレクトリを探索して変更されたファイルを発見しなければなりません。
それをやっていたのは Subversion とその祖先に相当する CVS です。

その処理を高速化するためにバッチ化したけど、結局ソースツリーの深さや分岐の多さに応じて遅くなってしまう問題は解消できませんでした。
Perforce はクライアントの取得したファイルとバージョンをメモリに記録しておくことで当該操作を高速に実行できるようにしていたため、サーバー側で常に同期操作を受け付けられるようになっていました。
実際には、ファイルの読み取り専用ビットの管理コストを支払うことで実現されていました（Perforce と連携する IDE が背後で使用していました）。
つまり、Perfoce ではすでに答えを知っている状態だったから立て続けの最新化を高速に処理できるようになっていたのです。

<!-- CVS, Subversion and Perforce offer you the choice of checking out a subdirectory. In a monorepo situation, you would
consider that a nice feature. At least if you've recursively laid out services and applications within the trunk. -->
CVS、Subversion、Perfoce ではサブディレクトリを指定してチェックアウトできます。
モノレポで役に立つ機能ですし、trunk に並んでいる複数のサービスやアプリケーションを再帰的に取得するときにも役立つでしょう。

<!-- Git and Mercurial have a single point of checkout (Git's 'clone' operation) for the whole repository.  There are no sub-directory checkouts
for these two. All commits since that 
last 'pull' will be pulled down. This happens before the directory walk to determine what has changed locally. As such, that 
pull operation if very fast - there's no chit chat over the wire things, and the stuff on the server-side was already 
zipped and pretty much ready for transfer. This will be the case even for situations where a particular 
file has been changed a dozen times since you last pulled it down via a sync operation.  You might think 
this is costly, but in practice, Git is incredibly fast. -->
Git や Mercurial はリポジトリに対してチェックアウトできるポイントは1つだけです。
サブディレクトリを指定してチェックアウトする機能はありません。
取得するときは、最後に取得した後に増えた全てのコミットを取得することになります。
これは、ローカルのディレクトリを探索して変更したファイルを発見する前に行われます。
ネットワーク経由で不要なやり取りをせず、サーバー側でもこれから転送するデータを用意できているなら、取得操作はとても高速になります。
最後に同期操作をしてから、一部のファイルが何度も変更された場合でも効率的に処理できます。
コストの高い処理のように思われるかもしれませんが、実際に Git はとても高速に処理できます。

### コミットとプッシュの速度

<!-- Things are more equal here between the tools we highlight as viable.   -->
このセクションで紹介しているどのツールにも同じような問題が当てはまります。

<!-- Subversion and Perforce send up deltas of changed files to the server. Some directory walking can slow this down. There
is inevitably a lot of chit-chat on the wire for these operations.  -->
Subversion と Perforce は変更したファイルの差分をリモートリポジトリに送信します。
ディレクトリの探索はこの処理を遅くします。
ネットワーク経由で多数のやりとりをしなければならないからです。

<!-- Git and Mercurial do the same, but before you push to the remote repo it will make you commit locally which is incredibly
fast. Before you push to the shared remote repository, these two will make you pull first.   -->
Git と Mercurial についても同様ですが、ローカルのコミットは極めて高速です。
これらのツールは共有リモートリポジトリにプッシュする前に最新情報を取得しなければなりません。

<!-- Perforce and Subversion will allow you to commit/push changes to the remote, without necessarily having the latest versions (and
incidentally all the intervening ones) locally first.  It will only allow that if there was no clash on the lines changed. -->
Perforce と Subversion ではローカルデータを最新化する前にリモートリポジトリへコミットをプッシュできます（たまたま順序が前後することもあります）。
ただし、同じファイルへの変更が衝突しない場合だけです。

<!-- Perforce can cheat again, but taking advantage of the read-only bit, and therefore already knows which files definitely
have **not** changed between the remote master repo and local working copy. It still feels slower than it should be, though. -->
Perfoce は読み取り専用ビットの情報を使用して、リモートリポジトリとローカルの作業コピーでどのファイルが **変更されていない** のか把握できます。
そのため、少し遅く感じる場合があります。

### 3方向マージツール

<!-- Developers (hopefully in pairs) are going to have to become skilled in arbitrating over merges for the commits they
are trying to promote to the remote trunk, as well as the changes they are updating from that it in the case that they 
have work in progress in their working-copy. Trunk-Based Development teams, you see, are merging more often. Albeit 
those are smaller merges, and they are implicitly merged to your working copy. -->
開発者は、作業途中の作業コピーを変更するのと同じように、リモートリポジトリのコミットをマージすることにも慣れていかないといけません。
トランクベース開発では頻繁にマージする機会があるからです。
とはいえ、小規模なマージなので、作業コピーには静かにマージされるでしょう。

<!-- Perforce's three-way merge tool (P4Merge) is good enough on its own to be attractive to teams using other VCS
technologies. For those other technologies, P4Merge  is just a config setting away from being usable.   -->
Perforce の3方向マージツール（P4Merge）は、単独でも他のバージョン管理ツールから利用できる優れたツールです。

<!-- Semantic merge is the next step up in the science of source-control. See [Plastic SCM](/vcs-choices/index.html#plasticscm). -->
セマンティックマージはソースコード管理システムにとって次世代の技術です。
詳しくは [Plastic SCM](/vcs-choices/index.html#plasticscm) を参照してください。

### コードレビュー

<!-- Integrated code review turned out to to be the killer feature of VCS tools. This should have been clear from the moment Mondrian
was unveiled by Guido van Rossum (Mr. Python) in a [publicized Google 'tech talk' in 2006](https://www.youtube.com/watch?v=sMql3Di4Kgc). It delivered pre-commit
code reviews to developers and gamified the activity of code review to some degree. Google was
using Perforce back then (they changed to an in-house technology in 2012), and it did not have code review built in, so 
they had to make Mondrian (which was the final form of years of intermediate deliverables for the same). Thus
Mondrian being created tightly coupled code review to the hourly activities of developers on the trunk.  -->
バージョン管理ツールに統合されたコードレビュー機能は目玉機能になるでしょう。
一般的な知識として明らかになったのは、Guido van Rossum が 2006 年に [Google のテックトーク](https://www.youtube.com/watch?v=sMql3Di4Kgc) で講演した Mondrian からです。
開発者がコミットする前にコードレビューすること、コードレビューという活動をゲーム化したことがポイントでした。
このとき、Google ではバックエンドに Perforce を使っていましたが（2012年には自作のツールに置き換えられました）、Perforce 自体にコードレビュー機能はありませんでした。
だから Mondrian を開発したのです（中間成果物の最終形態と同じものでした）。
Mondrian は trunk に対する開発者の時間単位の活動に注目したコードレビュー機能を提供していました。

<!-- GitHub (not Git) was next for the non-Google dev world with a built in code-review tool (and workflow). Again this was
effectively pre-commit - or at least commit to the master. -->
GitHub （Git じゃありません）は Google 以外の開発者たちにとって不可欠のコードレビューツール（ワークフロー）になりました。
実質的に master へ反映する前の、最新のコミットを対象にレビューします。

<!-- There were (and are) other technologies for code review such as Crucible (Atlassian), UpSource (JetBrains), Gerrit,
Phabricator, but integration into a platform experience is key.  GitLab and RhodeCode are emerging platforms. -->
Crusible (Atlassian) や UpSource (JetBrains) や Gertit や Phabricator などいろいろなレビューツールはあるのですが、プラットフォームに統合されている体験が重要です。
GitLab や RhodeCode のように急成長しているプラットフォームもあります。

<!-- Read more in [Game Changers - Google's Mondrian](/game-changers/index.html#google-s-internal-devops-2006-onwards) and
[Game Changers - GitHub's Pull Requests](/game-changers/index.html#github-s-entire-platform-2008-onwards). -->
Mondrian や GitHub について詳しくは [Game Changers - Google's Mondrian](/game-changers/index.html#google-s-internal-devops-2006-onwards) や [Game Changers - GitHub's Pull Requests](/game-changers/index.html#github-s-entire-platform-2008-onwards) を参照してください。

### 継続的インテグレーションとテスト

<!-- This goes hand in hand with the Code Review capability. CI Servers kick in for commits are pushed up code review branches,
or at least against change sets that are entering code review and validate them. The regular build, and maybe some 
additional steps are executed against them and the results of those made available to code reviewers, Facebook has a 
Service Level Agreement to have those complete ten minutes after the commit has been placed in the 'needs code review'
queue. -->
コードレビュー機能と連携することで効果を発揮する機能です。
レビュー対象のブランチにプッシュされたコミットに反応して、あるいは、レビュー対象の変更セットに反応して CI サーバーで実行し、妥当性を検証します。
通常のビルドに加えて何らかの処理を実行し、レビューアがその結果を参照できるようにします。
Facebook では、コミットしてから（プッシュしてから）10分以内に、ビルドの結果を（およびテストの結果を）「レビュー待ちキュー」で参照できるようにすることが、SLAとして定義されているそうです。

<!-- The reality of VCS platforms today is that they only provided hooks (web-hooks most likely) into other CI servers. The
best combinations of VCS platform and CI server do so for any of the branches in play, including code-review branches 
(forks). -->
最近の基盤としてのバージョン管理システムは、実際のところさまざまなCIサービスのためのフックを提供しているだけになっています（主にWebフック）。
バージョン管理システムとCIサービスを上手く連携すれば、レビュー対象のブランチを含む任意のブランチを上手く扱うことができるでしょう。

## 統治性

<!-- It turns out that enterprises like the ability to carve up permissions, and set read and write permissions throughout
their repositories. -->
業務システムで行われる権限管理のように、リポジトリに読み取りや書き込みの権限を設定する必要があります。

### きめ細やかな権限

<!-- All VCS usages need users accounts to be able to accept changes back, even if they don't all require accounts in order
to be able to read. In the enterprise user accounts will be required to be able to read too. -->
どんなバージョン管理システムでも、ユーザーアカウントを登録していない送信者からの変更は受け付けられません（たとえ読み取りにアクセス情報が不要だったとしても）。
業務システムなら、情報を読み取るためにユーザーアカウントが必要です。

<!-- The larger the enterprise the more likely it will be that they have permissions carved up in more fine grained ways.
For example "can commit to the trunk, but cannot create release branches" will be common for Trunk-Based Development
teams that make release branches.  Not all VCS technologies support that. Git, for example, does not, but some of the 
portal experiences around it, add the feature. In reality, though, you cannot stop people from making branches in a DVCS,
but you can prevent them from pushing them to the shared repository. -->
大規模な業務システムのほうが、権限をより細かく管理している可能性があります。
例えば、トランクベース開発でリリースブランチを作成する方針のチームでは、「trunk へのコミットは許可、リリースブランチの作成は不許可」という権限になるのが一般的です。
全てのバージョン管理システムが対応しているわけではありません。
実際、Git には権限管理機能がありませんが、機能追加する余地は残されています。
本質的に DVCS において人々がブランチを作成するのは止められないのです。
共有リポジトリへプッシュするのを防ぐことはできます。

<!-- Deeper still, some enterprises will want to set permissions down the directory level within a branch within a repository.
This could be as simple as a gate, to ensure that process is adhered to around checkins, but could also be to guard
some secrets in a Monorepo implementation. That last is counter to some of the "common code ownership" ideals of
modern software engineering though. -->
もう少し掘り下げると、業務システムによってはブランチやディレクトリに対する権限管理を要求する場合があります。
まさに玄関として、ルールに従ってチェックインされていることを保証し、モノレポに格納された一部の機密情報を保護します。
後者は、近代的なソフトウェアエンジニアリングの考え方「コードの共同所有」に反しています。

### 大きさと大規模化

<!-- Many teams, particularly those with Monorepo configurations or large binary files (like Games companies), want to have
limitless server-side storage for their repository.  Git and Mercurial are inching towards bigger and bigger 
capacity, but there are still a few snafus to work through - how to cleanly/safely reduce the size of the client side clone
history (while still being able to push changes back to the server) is a challenge. -->
モノレポをやっているチームや巨大なバイナリファイルを扱っているチーム（例えばゲーム会社）は、リポジトリを保持する無限のストレージを要求するものです。
Git や Mercurial はより大きなリポジトリを扱えるように進化していますが、多少の混乱もあります。
クライアント側に複製した（リモートリポジトリへ変更をプッシュできる）履歴を簡単に、安全に縮小するのは困難な課題です。

<!-- Microsoft released Git Virtual File System (~~GitVFS~~ ~~GVFS~~ VFS for Git{{< ext url="https://vfsforgit.org/" >}}) in early 2017, to layer on some of the things they had
become used to in the in-house recompilation of Perforce (SourceDepot - used from 1998 to 201x), as well as native VCS 
of their commercial offering Team Foundation Server (TFS). -->
Microsoft が 2017 年に公開した GVFS (~~GitVFS~~ ~~GVFS~~ VFS for Git{{< ext url="https://vfsforgit.org/" >}}) は、それ以前に社内で使用していた SourceDepot （Perforce を再コンパイルした）と、商用サービスの TFS (Team Foundation Server) に基づいて開発しました。
