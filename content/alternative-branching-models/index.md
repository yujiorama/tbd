---
date: 2016-10-01T20:08:11+01:00
title: 他のブランチモデル
weight: 101
---

<!-- {{< quote title="Branch: only when necessary, on incompatible policy, late, and instead of freeze" >}}
<span>&mdash; Laura Wingerd & Christopher Seiwald</span><br>
<span style="margin-left: 30px">(1998's High-Level SCM Best Practices white paper from Perforce)</span>
{{< /quote >}} -->
{{< quote title="必要なときだけ作成する、基本的な方針に反するブランチ。コードフリーズやフリーズした後の作業に使用する" >}}
<span>&mdash; Laura Wingerd & Christopher Seiwald</span><br>
<span style="margin-left: 30px">(1998's High-Level SCM Best Practices white paper from Perforce)</span>
{{< /quote >}}

## 現代における高スループットなブランチモデル

### GitHub flow

<!-- This is sooooo close to PR-centric Trunk-Based Development. Why? Because it is a branching model that has individual
developers active concurrently in more than one (short-lived) branch (or fork). Or developer pairs, rather than
individuals. -->
PR中心のとても洗練されたトランクベース開発です。
それぞれの開発者が1つ以上の（短命の）ブランチ（あるいはフォーク）で作業できるモデルです。

<!-- The crucial difference is **where** the release is performed from. -->
本質的な違いは **どのブランチから** リリースするかという点です。

<!-- The GitHub flow thing showing a release-from-branch step, before the merge back to the master (trunk): -->
GitHub flow におけるリリース手順では、リリースブランチから trunk にマージする直前にリリースします。

![](githubflow1.png)
([key](/key/))

<!-- One problem with this is the small risk of regression in the following release - that would be if the release goes out, but
the branch is never merged back. Another is that it may indeed not have items in it from the trunk that were part of a 
previous release (actually causing a regression). -->
問題は、後続のリリースに回帰バグが残る可能性があることです。
リリースブランチからリリースしたけど、trunk にマージしなかった場合に発生します。
また、前回のリリースの一部であるはずの trunk に存在するべきコミットが存在しない場合も考えられます（回帰バグの原因です）。

<!-- As the GitHub documentation portrays, review comments are part of the process. They are the speech
bubbles in timeline above followed by another commit, which presumably addressed the review feedback.  -->
GitHub のドキュメントでは、一連の手順にレビューコメントも含まれます。
時系列の中で吹き出しになっているのがコメントです。最後のコミットはおそらくそれらのフィードバックを反映したものでしょう。

<!-- How Trunk-Based Development modifies the GitHub Flow model: -->
GitHub Flow モデルに合わせたトランクベース開発は次のようになるでしょう。

![](../5-min-overview/trunk_pr.png)
([key](/key/))

<!-- After the dust has settled, and the [short-lived feature branch](/short-lived-feature-branches/) has been deleted, the commits are not smushed together
in a bigger one (as would be the case Subversion and Perforce), they instead zip into their respective places in the
commit history, which is not as linear as we present here: -->
後始末が済んだら [短命の機能ブランチ](/short-lived-feature-branches/) は削除します。
複数のコミットを1つの大きなコミットに固め直す必要はありません（Subversion や Perforce の場合）。
それぞれのコミットは履歴の適切な位置に挿入されます。
図のようにきれいに並ばない場合もあります。


![](githubflow3.png)
([key](/key/))

<!-- Of course, if you rebase/squash your series of commits, they could land in the trunk as a single commit.  Also note that
the review commentary is still available after the branch is deleted, as it should be.   -->
rebase や squash すれば1つのコミットになるでしょう。
ブランチを削除してもレビューコメントが残っているのは重要です。

<!-- See the GitHub Flow landing page for more{{< ext url="https://guides.github.com/introduction/flow/" >}} -->
詳しくは GitHub Flow のランディングページ{{< ext url="https://guides.github.com/introduction/flow/" >}} を参照してください。

### Git Flow と類似のブランチモデル

<!-- Git Flow is incompatible with Trunk-Based Development. -->
Git Flow はトランクベース開発と互換性がありません。

<!-- There are plenty of people in the modern age that swear by this model, and feel it has plenty of room to scale with few
downsides. It is a branching model that has **groups** of developers active concurrently in more than one branch (or fork). -->
このモデルを信頼している人はたくさんいるし、少し欠点はあるけど拡張できそうなところがたくさんあります。
このモデルは、**開発者のグループ** が複数のブランチ（あるいはフォーク）で並行して作業するためのモデルです。

![](gitflow.png)
([key](/key/))

- Diagram copied from Vincent Driessen's 2010 article on GitFlow: "A successful Git branching model"{{< ext url="http://nvie.com/posts/a-successful-git-branching-model" >}}

<!-- It looks like you'll not be able to do [concurrent development of consecutive releases](/concurrent-development-of-consecutive-releases/)
with this branching model, or the hedging that [Feature Flags](/feature-flags/) and [Branch by Abstraction](/branch-by-abstraction/)
enable. -->
見てのとおり [並行開発と逐次的なリリース](/concurrent-development-of-consecutive-releases/) は不可能です。
[機能フラグ](/feature-flags/) や [抽象化によるブランチ](/branch-by-abstraction/) は利用できます。

## 従来のブランチモデル

## 1つ以上の trunk

<!-- This seems OK, but has many pitfalls. Consider **not** doing this model. -->
一見すると問題ありませんが、いろいろな落とし穴があります。
**決して採用しないでください**。

<!-- Some have a single repository with many trunks (and many branches - release or not). That is not uncommon with VCS
choices that do not have an upper limit on size (including history). You are at least allowing for atomic commits across many
of those trunks, which can happen if you have a large refactoring, and atomic commits are always desirable. -->
具体的には1つのリポジトリに複数の trunk ブランチを作成します（それ以外のブランチも作成します）。
リポジトリサイズ（履歴サイズ）に上限がないとか、使用できるバージョン管理ツールは限られます。
少なくとも複数の trunk に分離不可能なコミットができるようでないといけません。
大規模なリファクタリングをするときは分離不可能なコミットが必要不可欠です。

```
root/    
  module_one/
    branches/
      rel_1.0.0/
      rel_1.1.0/          
    trunk/      
      build_file.xml
      src/
        # prod source directory tree
        # test source directory tree
  module_two/
    branches/
      rel_1.0.0/
      rel_1.1.0/          
    trunk/      
      build_file.xml
      src/
        # prod source directory tree
        # test source directory tree
  module_three/
    branches/
      rel_1.0.0/
      rel_1.1.0/          
    trunk/      
      build_file.xml
      src/
        # prod source directory tree
        # test source directory tree
```

<!-- Many trunks becomes undesirable if you have lock-step release (same cadence) for all the separate trunks in the repo,
and for all of those you
are 'branching for release'. You have made it harder for the larger thing to be build from root. Better would be one
trunk with three modules, and a recursive build system, or one of the modern directed graph build systems like Buck or Bazel: -->
それぞれの trunk について足並みを揃えて（なんらかの周期に基づいて）リリースしていると、だんだん不適切な状態になっていきます。
全ての「リリースブランチ」も同じように不適切な状態になります。
リポジトリのルートディレクトリからビルドする対象が増えれば増えるほど、問題は難しくなっていきます。
良くても1つの trunk に3つのモジュールが限界でしょう。
また、Buck や Bazel のように再帰的なモジュール構造に対応したビルドシステムを使うべきです。

```
root/    
  branches/
    rel_1.0.0/
    rel_1.1.0/          
  trunk/      
    module_one/
      build_file.xml
      src/
        # prod source directory tree
        # test source directory tree
      module_two/
        build_file.xml
        src/
          # prod source directory tree
          # test source directory tree
      module_three/
        build_file.xml
        src/
          # prod source directory tree
          # test source directory tree
```



<!-- This way at least, you get to make one branch representing the release (with cherry-picks to it as bugs are eliminated). -->
いずれにせよ、少なくともリリース元を代表するブランチを1つ用意するべきです（バグ修正はチェリーピックしていきます）。

<!-- Even if you have different release cadences, you can still engineer a trunk this way, you just have to have a build
system that can reduce to the modules being tested and deployed, skipping those that won't be. See [Monorepos](/monorepos/)
and [expanding and contracting monorepos](/expanding-contracting-monorepos/). -->
リリースケイデンスが異なるモジュールをこのやり方で管理していくには、テスト済みあるいはデプロイ済みのモジュールを無視できるようなビルドシステムが必要です。
詳しくは [モノリポ](/monorepos/) や [モノリポの拡大および縮小](/expanding-contracting-monorepos/) を参照してください。

### メインライン

<!-- Mainline is diametrically opposite to Trunk-Based Development - do not do this. -->
メインラインはトランクベース開発と正反対のブランチモデルです。採用しないほうがいいでしょう。

<!-- Mainline is a branching model that was promoted for ClearCase implementations. It is the principal branching
model that Trunk-Based Development opposes. Mainline is a branch that will last forever&#10033;. Off that, branches are formed
for teams to do development work on. When that work is complete, a release may happen from that branch, and there is a
**big** merge down to the mainline. On the way to the release, the branch may be frozen. -->
メインラインは ClearCase の実装に基づく、トランクベース開発とは正反対のブランチモデルです。
メインラインはチームが作業するブランチの始点になる、最後まで残る[forever]ブランチです。
チームの作業が完了したら、その作業ブランチからリリースして、それが済んだらメインブランチに **大規模な** マージを実施します。
リリースの済んだブランチをそのまま凍結する場合もあります。

<!-- Companies that choose 'Mainline' wither and die, we claim, so there is no forever. -->
[forever]: メインラインを採用した企業はいずれ無くなるので、決して永遠に残るわけではありません。

<!-- So here is the intention, with Mainline: -->

![](mainline1.png)
([key](/key/))

<!-- When bugs inevitably happen: -->
バグを発見したときの様子です。

![](mainline2.png)
([key](/key/))

<!-- Whenever there is a bug fix there has to be a merge down to the mainline afterward. There's no 'wrong' in this modified
branch diagram, but you should be able to guess what the worst case branching/merging scenario is. In case you cannot: -->
どのブランチでバグを修正したとしても、最終的にメインラインへマージしなければなりません。
この図に「間違っている」ところはないのですが、常に最悪ケースを考えるようにしなければなりません。

![](mainline3.png)
([key](/key/))

**前の図におけるマージの順番の説明**

<!-- 1. Release 1.1 team persuades the release 1.0 team to bring something back to Mainline early (and incomplete) before they cut their branch
1. Release 1.1 team merges the release 1.0 work upon apparent completion
1. Release 1.0 team merges post-release bug fixes back to Mainline, and cross their fingers that the 1.0 branch can truly die now
1. Release 1.2 team persuades the release 1.1 team to bring something back to Mainline early (and incomplete) before they cut their branch
1. Release 1.1 team merges from Mainline, to pick up #3
1. Release 1.2 team merges the release 1.1 work upon apparent completion
1. Release 1.1 team merges post-release bug fixes back to Mainline, and cross their fingers that the 1.0 branch can truly die now
1. Release 1.2 team merges from Mainline, to pick up #7 -->
1. リリース1.1チームはメインラインからブランチを作成したいので、リリース1.0チームに未完成でもいいから早くメインブランチへマージするよう依頼します
2. リリース1.1チームは、リリース1.0チームの作業が完了するまでのコミットをマージします
3. リリース1.0チームはリリースした後に行ったバグ修正をメインラインにマージします。また、リリース1.0のブランチはそこでおしまいです
4. リリース1.2チームはメインラインからブランチを作成したいので、リリース1.1チームに未完成でもいいから早くメインブランチへマージするよう依頼します
5. リリース1.1チームはメインラインから#3の変更を取り込みます
6. リリース1.2チームは、リリース1.1チームの作業が完了するまでのコミットをマージします
7. リリース1.2チームはメインラインから#7の変更を取り込みます

<!-- All of these compromises versus the planned "consecutive development of consecutive releases". In many cases, it is worse,
particular when the numbers of developers go up. -->
こういった相手を慮る一連のやり取りと、計画的な「並行開発と逐次的なリリース」を比べてみましょう。
たいていの場合後者のほうが優れています。
開発者の人数が多い場合は特にそうです。

<!-- One key thing to note, versus Trunk-Based Development, teams doing the Mainline branching model, almost never do cherry
pick merges for any reason. Instead, they are doing a "merge everything which is not merged already" kind of merge.
Minimalistically the VCS they are using should have "merge point tracking". At the high end, that should include
"record only" merges, and normal merges even after that. -->
トランクベース開発と比較するときに重要なのは、ほとんどの場合チェリーピックをしないことです。
代わりに「マージしてないものは全部マージする」方法でマージします。
バージョン管理ツールには少なくともマージ点を追跡する機能が必要です。
高級な製品には、通常のマージ以外に「痕跡だけ」を残すマージができるものもある。

<!-- &#10033; Companies that choose 'Mainline' wither and die, we claim, so there is no forever. -->

#### マージについて

<!-- After the release, the code will be merged back en masse to the mainline. Those
merges may be hard and lengthy. It could be that the team **took merges from** mainline part way through the project. It
could also be that the team **pushed merges to** mainline part way through the project. -->
リリースが済んでから大量のコミットをメインラインにマージするのはとても大変で時間がかかります。
チームはプロジェクトの期間を通じて、**メインラインからマージ** し続けると同時に、**メインラインへプッシュ** し続けることになります。

#### ブランチはいくつあるのか

<!-- We've just described a two branch model - the mainline and a project branch. It could be that the application in
question has more that one project in flight at any one time. That would mean more than one project branch, and that
creates pressure for more intermediate merges, and consequentially greater merge difficulty. -->
このセクションではメインラインとプロジェクトごとのブランチを説明しました。
同じアプリケーションについて複数のプロジェクトが同時進行する場合があります。
そうすると、プロジェクトごとのブランチは複数になりますし、途中のマージがもっと増えることになるだけでなく、マージがもっと大変になってしまうのです。

#### 常にリリースできる状態になっているか

<!-- Not on your life! Planned work needs to complete, with estimates guiding when that will be. Defects need to be
eliminated, formal testing phases need to kick in. Here we take the first branch diagram, and overlay red and orange
and green to show known build-breaks, build passes missing automated tests will not catch hidden defects,
and green for could go live. At least for the worst performing with missing or ineffectual automated
testing run in the CI pipelines: -->
無理じゃないですか。
計画した作業がいつ完了するかは、見積もりから推定するしかありません。
不具合は全て解消しなければなりませんし、正式なテストフェーズを始めなければなりません。
1つ前の図に、壊れたビルドが正常化するまでの期間を赤色、オレンジ色、緑色で塗り分けてみましょう。
自動テストのないビルドは未発見の不具合を素通りさせて、そのまま本番環境に出ていってしまいます。
少なくとも CI パイプラインで自動テストを実行していない、実行してるけど意味がないのはよくないことです。

![](mainline4.png)
([key](/key/))

### 連鎖

<!-- Cascade is incompatible with Trunk-Based Development - do not do this. -->
連鎖もトランクベース開発と噛み合いません。採用しないほうがいいでしょう。

<!-- The idea is that each release has its own branch and that each release team merges from the 'upstream' branch daily.
They only do so if the CI server says the build is green for the upstream, of course. -->
リリースに対応するブランチを作成し、リリースを担当するチームは毎日「上流の」ブランチから変更を取り込みます。
CIサーバーは「上流」ブランチのビルドが成功しているかどうかだけを報告しています。

![](cascade1.png)
([key](/key/))

<!-- Problems compound with this model, the more releases being juggled concurrently there are. An upstream butterfly is
a downstream Tsunami of unmergability. Downstream merges begin to get skipped, or abandoned. Or the merge works, but the
code is not right so there is some in-branch fixing, which is not applicable to upstream. Here's the reality (breakages
overlaid again): -->
このモデルの弱点は、複数の並行するリリースを扱うのが難しいことです。
上流ブランチで蝶が羽ばたくと、下流ブランチにマージできない状態が津波のように押し寄せるのです。
下流ブランチでマージを後回しにする、あるいはマージしてもちゃんと動かない状態になってしまうと、上流ブランチと整合性が取れなくなってしまうのです。

![](cascade2.png)
([key](/key/))

<!-- Remember, the merges are never cherry-picks in this model - they are sweeps of everything
not merged yet (or up to an chosen commit number in order to make it more bite-sized). -->
このブランチモデルではチェリーピックしていません。
マージしていない変更を全てマージするのです（あるいはコミット番号を控えておきます）。

<!-- Of course only larger organizations have to worry
about the [concurrent development of consecutive releases](/concurrent-development-of-consecutive-releases/), and many
would argue that the application is too large anyway (and that microservices are the solution). -->
[並行開発と逐次的なリリース](/concurrent-development-of-consecutive-releases/) について検討するのは、大規模な会社や、アプリケーションの大きさに悩まされている会社だけでいいでしょう（マイクロサービスが解決策になるかもしれません）。

## CIはあなたのブランチモデルの正しさを証明（否定）します

<!-- Here's an idea. Configure your CI Server to focus on every branch, regardless of branching model. Specifically to do
per-commit builds, and do that speculative merge analysis described in [game changers](/game-changers/index.html#snap-ci-s-per-commit-speculative-mergeability-analysis-2013). -->
ブランチモデルに関わらず、CIサーバーは全てのブランチを対象にするようにしましょう。
特に、コミット前ビルドや、投機的なマージ可能性分析（[game changers](/game-changers/index.html#snap-ci-s-per-commit-speculative-mergeability-analysis-2013) を参照）をしているときはそうしましょう。

<!-- If everything is green everywhere, then you're in a position to always be release ready, but very few teams are going
to see green instead of red for this CI server enthusiasm -->
全てが成功しているなら、常にリリースできる状態を保っています。
ごく一部のチームは、失敗（赤色）より正常（緑色）になったビルドのほうを気にすることがあるようです。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">31 Aug 2011, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://scottchacon.com/2011/08/31/github-flow.html">GitHub Flow</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">04 Dec 2013, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/12/04/what_is_your_branching_model/">What is Your Branching Model?</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">05 Apr 2013, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/04/05/what-is-trunk-based-development/">What is Trunk-Based Development?</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">19 Mar 2013, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/03/19/cost-of-unmerge/">The Cost of Unmerge</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">15 Oct 2015, InfoQ Interview</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.infoq.com/news/2015/10/branching-continuous-integration">More Feature Branching Means Less Continuous Integration</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">03 May 2015, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://endoflineblog.com/gitflow-considered-harmful">GitFlow considered harmful</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">08 Jan 2016, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://dan.bodar.com/2016/01/08/gitflow-hmmmm/">GITFLOW HMMMM</a></td>
        </tr>
    </table>
</div> -->

- 31 Aug 2011, Blog Entry: [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html)
- 04 Dec 2013, Blog Entry: [What is Your Branching Model?](http://paulhammant.com/2013/12/04/what_is_your_branching_model/)
- 05 Apr 2013, Blog Entry: [What is Trunk-Based Development?](http://paulhammant.com/2013/04/05/what-is-trunk-based-development/)
- 19 Mar 2013, Blog Entry: [The Cost of Unmerge](http://paulhammant.com/2013/03/19/cost-of-unmerge/)
- 15 Oct 2015, InfoQ Interview: [More Feature Branching Means Less Continuous Integration](https://www.infoq.com/news/2015/10/branching-continuous-integration)
- 03 May 2015, Blog Entry: [GitFlow considered harmful](http://endoflineblog.com/gitflow-considered-harmful)
- 08 Jan 2016, Blog Entry: [GITFLOW HMMMM](http://dan.bodar.com/2016/01/08/gitflow-hmmmm/)
