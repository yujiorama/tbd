---
date: 2016-06-01T20:10:45+01:00
title: スタイルとトレードオフ
weight: 60
---

<style>
figure.floatleft {
	max-width: 30%;
	width: auto\9*0.3; /* ie8 */
	height: auto;
	float: left;
	margin-top: 6px;
}
figure.floatright {
	max-width: 30%;
	width: auto\9*0.3; /* ie8 */
	height: auto;
	float: right;
	margin-top: 6px;	
}
</style>

<!-- There are broadly three styles of trunk-based development as a daily developer activity. Depending on the number of
developers in the team, the release cadence, and the desired rate of commits (assuming story-sizes that support that), 
you have trade-offs for each of the three: -->
トランクベース開発には日々の開発者のアクティビティについて、大きく分けて3種類のスタイルがあります。
スタイルの選択は、チームに所属する開発者の数や、リリースケイデンス、目標とするコミット頻度（想定するストーリーの大きさ）によって決まるでしょう。
それぞれのスタイルにはトレードオフがあります。

![](/styles/styles-tradeoffs.png)

## trunk に直接コミットする

<!-- Suitable for active committer counts between 1 and 100. -->
活発なコミッターが1人から100人のチームに適しているスタイルです。

{{< figure src="/styles/styles-tradeoffs-l.png" class="floatleft" title="left hand side of the diagram above" >}}

<!-- Traditionally Trunk-Based Development meant committing straight to the shared trunk of the VCS in question. And doing so after
a bunch of steps that together with the commit we will call integration.   -->
共有リポジトリの trunk へコミットしていく伝統的なスタイルです。
コミットすると、もろもろのインテグレーションタスクが実行されるようになります。

<!-- This is a really high throughput way of working,
but it relies on developers being extremely sure their commit is not about the break the build. If they do, then a manual 
or automatic revert gets the trunk back to a good state, and you hope nobody did a git-pull or svn-up to bring that bad 
commit into their workstation. Or you've engineered it so that does not happen - you publish a known-passing latest commit 
number or hash, and make a wrapper for git-pull (svn-up, etc) to go get that instead of HEAD. -->
このスタイルは極めて高いスループットを発揮します。
ただし、開発者のコミットがどれだけビルドを壊さないかにかかっています。
もし壊してしまったら、手動で、あるいは、自動敵にリバートして（打ち消して）、trunk を健全な状態に戻さなければなりません。
また、誰かがビルドを壊したコミットを git-pull や svn-up で作業マシンに取得していないことを祈らないといけません。
そのためには、最新のコミット番号あるいはコミットハッシュを公開しておいて、git-pull (svn-up) のラッパーコマンドが HEAD の代わりに使用できるようにすればいいでしょう。

<!-- A related challenge is how long "the build" takes to execute by the CI service, versus how frequently the trunk is updated
with commits for all the committers that could. This is important because of the build fails (CI), there could be following
commits that would pass if it were not for the preceding failing commit. To pick that a part could be a challenge of the 
commit rate is high enough. Some build-cops lock the trunk at the first sign of a breakage. Best of all is a bot that 
reverts specific commits that failed the build, but again that is hard science.  If your build is 10 seconds (start to 
finish), and there is one commit every five minutes, then you are in a good position. If your build is five minutes and 
your team's commits arrive every ten seconds, then you're in hell, and should try something else. -->
関連する課題として挙げられるのは、CIサービスでビルドする時間と、trunk をどれだけの頻度で更新することになるのか、という点です。
これが重要なのは、CIが（ビルドが）失敗したとしても、前のコミットが無ければ成功していた可能性があるからです。
コミット頻度が高いと、そういうコミットを特定するのは困難になります。
trunk のビルドが失敗したら、書き込みを無効にしてしまうツールもあります。
一番いいのは、ビルドを失敗させたコミットを自動的に打ち消すことですが、それもまた困難な課題です。
ビルドが10秒で終わるなら、そして、5分に1回コミットがあるなら、良好な状態を保てるでしょう。
ビルドが5分で終わる、そして、10秒に1回誰かがコミットするなら、とても苦しい状態になるので、別のやり方を検討したほうがいいでしょう。

<!-- See [committing straight to the trunk](/committing-straight-to-the-trunk/) for more info. -->
詳しくは [trunk に直接コミットする](/committing-straight-to-the-trunk/) を参照してください。

## 短命の機能ブランチを使用する

<!-- Suitable for active committer counts between 2 and 1000. -->
活発なコミッターが2人から1,000人のチームに適しているスタイルです。

{{< figure src="/styles/styles-tradeoffs-c.png" class="floatleft" title="right hand side of the diagram above" >}}

<!-- Git and Mercurial delivered truly lightweight branching capability. What that meant was that branches could be very quickly
created and receive commits that are momentarily divergent from trunk or master (in our case) and then be merged back later
when ready. Then finally, and crucially, the branch that facilitated that short-lived divergence could be deleted quickly
leaving only the the commits added to the effective lined of commits culminating in HEAD for trunk or master.  In that
regard it is identically to the patch review way of working for trunk based development.  That was all just a small 
data point for Git/Mercurial usage, until GitHub launches and had pull-requests as a feature from launch. Built in to that 
was a code review tool. This is a very compelling setup for unsolicited code contributions - making SourceForce and the 
Apache Software Foundation appear ancient, by comparison. -->
Git や Mercurial は軽量なブランチ機能を提供しています。
つまり、簡単にブランチを作成してコミットを受信し、trunk や master から離れてしまってから、再び元のブランチにマージできる、ということです。
一時的に trunk から分離したブランチは、trunk の HEAD に必要な変更を（コミットを）残して、削除できるようになります。
トランクベース開発におけるパッチレビューの方法とは異なるやり方であることに注意しましょう。
Git/Mercurial の使い方としてパッチレビューくらいしかなかったのですが、GitHub が公開されてからコードレビューのためにプルリクエストが使えるようになりました。
押し付けられたコードを（貢献を）レビューするのに最適な機能でした。
おかげで SourceForge や Apache Software Foundation 等の競合は時代遅れになってしまいました。

<!-- Teams can form around the GitHub pull-request workflow, and still do Trunk-Based Development. What you get (if you've
attached build automation too), is a trunk or master that's never broken (or 1/1000th as likely to). The trade-off is that 
you have to persuade co-workers to review your commit(s) in a time frame that suits you.  There's a risk that you'd end up 
putting more commits in the pull-request that the straight-to-trunk experts would do. And you don't have to - you could 
stream four separate facilitating commits all the way into the trunk, and later the fifth that would complete/activate the 
feature you were trying to implement that the Agile story/card specified. Not only is there a risk of more-commits 
than you'd do if you could push directly, there's a risk of taking more time too. If your average story size should be 
one day, a slow-review and slow-build reality for the pull-request way, might push you into multi-day stories/cards, and 
from that be doing the opposite of getting to continuous delivery. -->
GitHub のプルリクエストを中心とするワークフローに基づいて構成したチームでも、トランクベース開発をやっていくことができます。
ビルドの自動化も組み合わせることで、trunk が決して壊れないようにできます（1000回に1回くらいは壊れるけど）。
あなたに都合のいい時間でコミットをレビューしてもらえるよう協力者を説得するのがトレードオフになります。
結果として、trunk へ直接コミットするのと同じように、プルリクエスト（のブランチ）へ複数のコミットを並べてしまうかもしれません。
また、そうしなくてもいいのですが、いくつかの部品を4つのコミットに分けて trunk へ送信してから、ストーリーに対応する機能を有効化する5つ目のコミットを送信してもいいのです。
trunk へ直接コミットできるようにしている場合と比べてコミットが増えてしまうリスクがあるだけでなく、時間も長くなってしまうリスクがあります。
ストーリーの平均的な大きさが1日で終わるくらいなら、プルリクエストにおけるレビューやビルドの遅れはストーリーの完了を複数日に遅らせることになってしまうので、継続的デリバリーの目的とは相反することになってしまいます。

<!-- See [short-lived feature branches](/short-lived-feature-branches/) for more info. -->
詳しくは [短命の機能ブランチ](/short-lived-feature-branches/) を参照してください。

## 「パッチレビュー」システムとの結合

<!-- Suitable for active committer counts between 2 and 40,000. -->
活発なコミッターが2人から40,000人のチームに適しているスタイルです。

<!-- "We do Trunk-Based Development" - Googler Rachel Potvin - @Scale keynote, Sept 2015 (14 mins in): -->
2015年9月の @Scale カンファレンスの講演 "Why Google Stores Billions of Lines of Code in a Single Repository" で、Google がやっているトランクベース開発を紹介していました。

![](/branch-for-release/atscale.png)

{{< figure src="/styles/styles-tradeoffs-r.png" class="floatright" title="center of the diagram above" >}}

<!-- Perhaps before others in the early 2000's, Google hit a ceiling on how many developers could commit to a trunk in a monorepo,
without tripping each other up. That would be build-breakages mostly, but also commits that wouldn't be up to coding standards
even if the build still passed.  Say Google managed to get 1000 developers and QA automators working in with commits straight 
into the trunk, before deciding that something needed to gate that. What resulted was a patch review system that would
ultimately be called Mondrian and be announced to the world in 2006 at a tech talk in Mountain View.  This was a system that
Google had written to augment Perforce (the VCS they used up to 2012), to provide a place where code could be reviewed per-commit, and
also build-automation results could collated.  -->
2000年代の初頭、Google はモノリポの trunk へコミットできる開発者人数の上限に誰よりも早く達しました。
ほとんどのビルドは失敗してしまいますが、たとえビルドが成功してもコーディング標準を守れるわけではありませんでした。
Google では 1,000 人規模の開発者と QA オートメーターが trunk へコミットできるようにするため、門のような仕組みが必要だと考えました。
それが、最終的に Mondrian と呼ばれるようになったパッチレビューシステムです。
2006 年にマウンテンビューの本社で行われたテックトークで世界中に紹介されていました。
Mondrian は Perfoce を拡張して作られており、コミットごとのレビューと自動化したビルドの結果を一緒に見れるようになっていました。

<!-- Today, patch review systems include Gerrit, Rietveld and Phabricator The latter by Facebook, and the first two with Googler
involvement.  These are not branches of course, they are held outside source-control in a relational schema. Their reason 
for existence is to marshal pending change, before that arrives in trunk/master and guarantee that it is good to be 
integrated.  -->
現在利用できるパッチレビューシステムには Gerrit や Rietveld や Phabricator があります。
前の2つは Googler が開発し、最後の1つは Facebook が開発しました。
もちろん、これらはブランチを使わないどころか、ソースコード管理システムを外部化して、リレーショナルスキーマで管理するようになっています。
これらのツールが活用されているのは、trunk に入る前の保留された変更を整理し、統合できる状態を保証するためです。

## ローカルビルドの重要性

<!-- In all variants of Trunk-Based Development teams run the full build locally (compile, unit tests, a range of integration tests) and see that pass, **before** declaring 'done' and committing/pushing the work to the eyes of teammates and bots (code review / pull-request), or directly into trunk/master. They do not **at all** use build automation as a crutch in order to determine whether their commit(s) were good or bad. Instead they determine that themselves on their dev workstation or containers/VMs that are dedicated to them, and do so **before** the push something towards code review and bot scrutiny.  As mentioned above keeping this build fast is very important, and not having a fast build is one of the key drivers to other branching models and repo sharding. Indeed it is one of the key drivers to slower release cadences too. -->
どのようなスタイルのトランクベース開発であれ、作業が「完了」したと判断し、コミットを他のチームメンバーが参照できるところ（コードレビューツールやプルリクエスト）や直接 trunk へプッシュする**前に**、作業マシンでビルドできること（コンパイル、ユニットテスト、結合テスト）を確かめなければなりません。
自動化ビルドと **同じように** 、ビルドが失敗したコミットを特定できるようにしなくても構いません。
手元の変更をプッシュする **前に** ビルドするだけでいいのです。
できるだけ短時間でビルドできるようにすることが重要です。
しかし、ビルド時間を短縮するために別のブランチモデルやリポジトリのシャーディングを検討するのは止めましょう。
それらはリリースケイデンスを低下させてしまうからです。

## スタイルの選択

<!-- While it is best to keep developer teams small, sometime there are business pressures to grow a dev to in order to do
more in parallel. Indeed, with monorepo configurations that could be more dev teams sharing one repo, even if they
have their own release cadences, and separate team organization to other teams. -->
開発チームを少人数に保つのが一番いいですが、いろいろな作業を並行してやらなければならない場合があります。
モノリポ構成では、それぞれが独自のリリースケイデンスを持つ、より細分化された開発チームが増えていくことになります。

<!-- If Google had (say) 1000 committers doing "straight to the trunk" for a single monorepo back in 2002, should you?  No, not
since alternates are now possible. If Google had the Mondrian of 2006 back in 2002, they would have moved to that way of working sooner.  -->
Google は 2002 年の時点で 1,000 人のコミッターが単一のモノリポの trunk に直接コミットしていると言っていました。
みんなそうするべきなんでしょうか。
そんなことはありません。
他にも選択肢があるからです。
Google も 2002 年に Mondrian があればそれを利用したワークフローに移行していたことでしょう。

<!-- What is the cut off point today?  Super skilled XP era developers who are in charge of dev teams and can train developers in the applicable
way of working, might say the cut-off is now 100 committers. People who've only ever know the pull-request way of working may 
suggest 10 committers is the cut off point. They may even make a case for 2 committers.  You could well be in a world 
where quorums naturally form within teams, leading to new development directions are effectively set.  In Google, it was 
employee #1 Craig Silverstein who initially held Google to the monorepo and trunk-based development. And he perhaps did 
that despite quorums forming and group wishes to do something else. -->
何人くらいが分岐点になるでしょう。
開発チームを牽引するスーパーXP開発者が、メンバーに仕事の仕方を教えてくれるとしても、100人が上限だと答えるでしょう。
プルリクエスト以外のワークフローを知らない人なら2人あるいは10人が上限だと答えるでしょう。
チーム内で自然に派閥が形成され、効果的な開発の方向性が決定されるような世界になっているのかもしれません。
Google で最初にモノリポとトランクベース開発を決定したのは Craig Silverstein でした。
彼はグループ内にいろんな意見があったにも関わらず、やり遂げたのです。

<!-- A list of trade-offs are: -->
次のようなトレードオフが考えられます。

<!-- * Whether your build technology needs to build 'everything' for every commit. Google's Blaze (Bazel in opensource-land) does not.
* Whether your source-control system has a push/pull bottleneck and whether you've reached that with all the committers in one repo
* The median build duration, versus that commit rate.
* How often your build-automation infra falls behind the commits/pull-requests that need to be compiled/tested.
* Whether your developers can avoid using the automated builds as a crutch
* Whether follow up commits are a workable way of addressing things that need improvements
* Whether the developers are good at separating refactoring commits from functional commits, and indeed "baby commits" generally.
* Whether your team can handle code-review feedback **after** commit/push to trunk/master or not -->
* 使用しているビルドツールがコミットごとに「全てを」ビルドしなければならないかどうか。Google の Blaze（Bazel という名前の OSS として公開されています）ではそんなことはありません。
* 使用しているソースコード管理システムにおいてプッシュやプルがボトルネックになるかどうか。そして1つのリポジトリで対応できるコミッター数の上限に達しているかどうか。
* ビルド時間の平均値と、コミット頻度の関係。
* 自動ビルド基盤がコンパイル、テストしなければならないコミットやプルリクエストが溜まってしまう頻度。
* 開発者は自動ビルドを最後の心の支えとしないようにできているか。
* 新しいコミットは何かを改善し、機能するようになっているか。
* 開発者たちはリファクタリングとそうでない修正を分けてコミットしているか。「ベイビーステップ」になっているか。
* コードレビューのフィードバックを反映しているのは trunk にコミット（プッシュ）した **後** か。
