---
date: 2016-05-05T19:56:50+01:00
title: リリースブランチ
weight: 55
---

<!-- {{< quote title="Branch: only when necessary, on incompatible policy, late, and instead of freeze" >}}
<span>&mdash; Laura Wingerd & Christopher Seiwald</span><br>
<span style="margin-left: 30px">(1998's High-Level SCM Best Practices white paper from Perforce)</span>
{{< /quote >}} -->
{{< quote title="必要なときだけ作成する、基本的な方針に反するブランチ。コードフリーズやフリーズした後の作業に使用する" >}}
<span>&mdash; Laura Wingerd & Christopher Seiwald</span><br>
<span style="margin-left: 30px">(1998's High-Level SCM Best Practices white paper from Perforce)</span>
{{< /quote >}}

<!-- If a team is pushing production releases monthly, then they are also going to have to push bug-fix releases
between planned releases. To facilitate that, it is common for Trunk-Based Development Teams to make a release
branch on a just in time basis - say a few days before the release. That becomes a stable place, given the developers
are still streaming their commits into the trunk at full speed. -->
あるチームが毎月本番環境にリリースしているとして、計画リリースの前にバグ修正リリースをしたいときはどうすればいいでしょう。
トランクベース開発では、必要になったらリリースブランチを作成します（リリースの数日前に作成するブランチです）。
リリースのための安定した作業場所を提供しつつ、trunk の開発を全速力で続けられるようになります。

<!-- The incompatible policy (ref Wingerd & Seiwald above), that the release branch "should not receive continued development work". -->
基本的な方針に反する、というのは、リリースブランチが「継続的に作業を受け付ける」場所ではないということです。

![](branch_for_release.png)
([key](/key/))

<!-- ^ Trunk, two and a half release branches, five releases (two planned, three unplanned), and two cherry-pick bug fixes -->
^ trunk と2つのリリースブランチ（と1つの中途半端なブランチ）。リリースは5回（2つはピン留めされていて、3つはピン留めされていません）。2つのバグ修正がチェリーピックされています。

<!-- {{< note title="CD teams do not do release branches" >}}
High throughput, [Continuous Delivery](/continuous-delivery/) teams can ignore this - if they had a lemon in production, they choose a
roll-forward strategy for solving it, meaning the fix for a bug is in the trunk, and the release to production is from the trunk.
{{< /note >}} -->
{{< note title="CD チームはリリースブランチを作りません" >}}
高スループットの [継続的デリバリー](/continuous-delivery/) をしているチームはリリースブランチを作成しません。
本番環境で些細な問題があれば、trunk で修正してロールフォワードします。
つまり trunk からリリースします。
{{< /note >}}

## 誰がリリースブランチにコミットするのか

<!-- Developers are committing (green dots) at the highest throughput rate to the trunk, and do not slow up or freeze around a
branch-cut or with proximity to a release. Developers as a group are **not** commiting to the release branch (see below). -->
trunk と同じ速さで任意の開発者がコミットします（緑の点）。
ブランチはリリースに近いところから作成し、ブランチを滞留させたりフリーズさせたりしません。
グループとしての開発者はリリースブランチにコミット **しません** 。
![](branch_for_release2.png)
([key](/key/))

<!-- The branch cut itself is a commit. Subversion and Perforce would technically have a bigger commit here, but all
VCS systems in use today would count the commit as 'lightweight' in terms of its impact on the history/storage,
and the time taken to create. -->
ブランチはあるコミットから作成します。
Subversion や Perforce ならより大きなコミットができることになります。
しかし、近年使用されているバージョン管理ツールのコミットは履歴としてもストレージオブジェクトとしても「軽量」で、ほとんど作成する時間がかかりません。

<!-- That red dot is an accidental build break that was fixed (somehow) soon after. -->
赤い点はビルドを壊した（事故を起こした）ところで、すぐに修正されているのが分かります。

## 後からリリースブランチを作成する場合

<!-- Some teams [release from a tag on the trunk](/release-from-trunk/) and do not create a branch at that time. That in
itself is **an alternate practice to this one, "branch for release"**. -->
チームによっては [trunk のタグからリリースするので](/release-from-trunk/)、リリースした時点のブランチを作成しない場合があります。
それ自体が **「リリースブランチ」の代替プラクティス** なのです。

<!-- Those teams wait for a bug that needs fixing for a released, before creating a branch from the release tag (if they are
not going to just issue another release from the trunk).   -->
このようなチームでは、リリースタグからブランチを作成する前に、バグ修正がリリースされるのを待つことになります（その修正だけを trunk からリリースするつもりがない場合）。

<!-- Brad Appleton points out that many do not realize that branches can be created **retroactively**. That is taken advantage
of here in the case of bugs after "release from a tag", or even changes for point releases. -->
Brad Appleton は、**遡って** ブランチを作成できることを知らないチームがそれなりに存在することを指摘しました。
それができるなら、「タグからリリース」した後に発生したバグを上手く扱えることになります。
点のリリースに修正を追加する方法でもいいでしょう。

## trunk で本番環境のバグを修正する

<!-- The best practice for Trunk-Based Development teams is to reproduce the bug on the trunk, fix it there with a test,
watch that be verified by the CI server, then cherry-pick that to the release branch and wait for a CI server
focusing on the release branch to verify it there too. Yes, the CI pipeline that guards the trunk is going to
be duplicated to guard active release branches too. -->
トランクベース開発におけるベストプラクティスは、trunk で再現させたバグを修正し、追加したテストをCIで実行して修正を確認し、リリースブランチにコミットをチェリーピックしてCIで同じ修正を確認することです。
trunk を保護するCIパイプラインを複製して、有効なリリースブランチも保護するのです。

<!-- {{< warning title="A cherry-pick is not a regular merge" >}}
A cherry-pick merge takes a specific commit (or commits) and merges that to the destination branch. It skips
one or more commits that happened before it, but after the branch was cut. All VCS tools track which commits
 have been merged and which ones not, so you can do more cherry picks later.
{{< /warning >}} -->
{{< warning title="チェリーピックは通常のマージとは違います" >}}
チェリーピックマージとは、指定したコミットを目標のブランチへマージすることです。
ブランチを作成した後に、指定したコミットより前のいくつかのコミットを無視できます。
どんなバージョン管理ツールでも、どのコミットをマージしたのか追跡できるようになっています。
だから、将来的に何回でもチェリーピックできるのです。
{{< /warning >}}

### trunk からリリースブランチにだけチェリーピックする

<!-- You should not fix bugs on the release branch in the expectation of cherry-picking them back to the trunk.
Why? Well in case you forget to do that in the heat of the moment. Forgetting means a regression in production some
weeks later (and someone getting fired). It can happen if things are being fixed in the night by a tired developer who
wants to get back to bed. -->
リリースブランチでバグを修正して、そのコミットを trunk にチェリーピックするのは止めたほうがいいです。
その場の勢いで作業して、忘れてしまう場合があるからです。
忘れてしまうということは、数週間もしたら本番環境に同じバグが流れ込むということです（誰かが発見するでしょう）。
夜中に作業していて、早く眠りたい人がやってしまう可能性があります。

![](branch_for_release3.png)
([key](/key/))

<!-- This rule for Trunk Based Development remains difficult to accept, even within teams practicing everything else about
Trunk-Based Development. It takes just one regression though for a policy change to be made for the team. -->
他のプラクティスは問題ないけど、このルールがトランクベース開発を受け入れられない理由になっている場合もあるようです。
チームの方針を変えるには、リグレッションテストを1つ追加するだけです。

<!-- Of course, sometimes you **absolutely cannot** reproduce the bug on trunk. In that case you have to do it the other way round, despite
everything mentioned above, but understand you have introduced risk of regression. -->
trunk で **絶対に再現できない** バグもあるでしょう。
このセクションで説明した方法を全て迂回しなければならない場合もあるでしょうが、バグを再発させてしまう可能性について注意しなければなりません。

### Google の Rachel Potvin によるチェリーピックの解説

<!-- In a talk at the @Scale conference in Sept 2015, "Why Google Stores Billions of Lines of Code in a Single Repository",
there was a slide that depicts cherry-picks in a branch diagram: -->
2015年9月の @Scale カンファレンスの講演 "Why Google Stores Billions of Lines of Code in a Single Repository" では、ブランチとチェリーピックの関係を次のように説明していました。

![](atscale.png)

<!-- The presenter, Rachel Potvin, said (14 mins in): -->

<!-- "So at Google we do what's called Trunk-Based Development. I should note that it is the combination of Trunk-Based Development with a centralized repository that really defines the monolithic model of source code management. So
what Trunk-Based Development means for us that typically Piper users all work from HEAD or a single copy of the most recent version of the codebase. When developers commit to Piper their changes are immediately visible and usable by other engineers. Branching for development at Google is exceedingly rare and Trunk-Based Development is beneficial partly because you avoid the painful merges that often occur when you need to reconcile long lived branches.  Branches however are used for releases. **So a release branch is typically a snapshot from trunk with an 
optional number of cherry picks that are developed on trunk and then pulled into the branch**." -->
> 私たち Google ではトランクベース開発をやっています。
> いわゆるトランクベース開発と中央集権型のリポジトリを組み合わせた、モノリシック型のモデルによるソースコード管理をしているのです。
> 私たちのトランクベース開発では、基本的に Piper ユーザーが作業するのは、常に HEAD かコードベースの最新バージョンの単純コピーです。
> 開発者が Piper にコミットすると、変更はすぐに他のエンジニアから参照できるようになります。
> 滅多に開発用のブランチを作成することはありませんし、長命ブランチを安定させるためにマージで苦労する必要もないのです。
> ただし、リリースブランチは作成しています。
> **リリースブランチとは、trunk の断面に trunk へ行った任意の数の修正をチェリーピックしたものなのです**。

<!-- We've bolded the cherry-pick bit ourselves.  Readers with beady eyes will note that Rachel alludes to
dev branches other than trunk for 'rare' reasons. We may cheekily suggest that Google should learn a little more about [Branch by Abstraction](/branch-by-abstraction/). -->
チェリーピックに関する説明を強調してみました。
trunk 以外に開発用ブランチを作ることは「滅多にない」という話には、とても驚かされます。
生意気なことを言わせてもらうとすれば、Google は [抽象化によるブランチ](/branch-by-abstraction/) についても学んだほうがいいでしょう。

### マージ職人という役割

<!-- The process of merging commits from trunk to the release branch using 'cherry pick' is a role for a single developer
in a team. Or dev pair, if you are doing Extreme Programming. Even then, it is a part time activity. The dev or pair
probably needs to police a list of rules before doing the cherry pick. Rules like which business representative
signed off on the merge. Perhaps the role should also rotate each day. -->
trunk からリリースブランチに「チェリーピック」でコミットをマージする役割は、チームメンバーの誰か1人が請け負うことになります。
XP をやっているならペアでもいいでしょう。
いずれにしても、それは一時的なアクティビティです。
チェリーピックをする前に一連のルールを見直しておいたほうがいいでしょう。
マージするときはビジネスステークホルダーが署名するルールになっている場合もあります。
また、役割を日替わりの当番制にする場合もあるようです。

<!-- Some teams update a wiki to audit what made it to the release branch after branch cut, and some use ticket system as
this by its nature interrupting and requiring of an audit trail of approvals. -->
チームによってはリリースブランチを作成した後に行った作業をWikiに記録しているところもあるそうです。
あるいは、チケット管理システムに登録して、後から承認履歴を追跡できるようにしているところもあるそうです。

## パッチリリース

<!-- It could be that your team has pushed a release out from a release branch, and now has a bug to remediate in
production. If the release cadence suits it, a cherry-pick of a bug fix from the trunk to the release branch
and a point release from the same branch is fine. -->
リリースブランチからリリースした後に、本番環境でバグが見つかったとしましょう。
リリースケイデンスを一定に保てるなら、trunk の修正をリリースブランチにチェリーピックして、同じブランチから点リリースをしてもいいと思います。

### ブランチの代わりにタグを使用する

<!-- Releasing from a tag on the trunk is a decent optimization for many teams, if possible. The tag could be numbered for
the release (say v1.1.1), and the branch can be avoided completely. Perhaps if there is a bug in production and a branch
is retroactively created from that tag, and the patch release (see above) can happen from there. -->
多くのチームが trunk のタグからリリースするのはまともな最適化の結果だと思います。
タグはリリースしたバージョン番号（v1.1.1）になるでしょうし、ブランチは不要です。
しかし、本番環境でバグを発見したら、タグからブランチを作成し、パッチリリースをすることになるでしょう。

## リリースブランチを削除する

<!-- Release branches are deleted some time after release activity from them ceases. Not immediately, but when it is clear release is no longer in production. Release branches are NOT merged back into trunk.
That is usually when releases from succeeding release branches have gone live. This is a
harmless tidying activity - branches can be undeleted again easily enough in all VCS choices. In git, a tag needs to be created from the released commit before deleting the release branch, since dangling commits will be garbage collected. -->
リリースブランチは、リリースしてからある程度時間が過ぎてから削除します。
そのリリースが本番環境に存在しないことが明らかになっている場合でなければ、すぐに削除するわけではありません。
リリースブランチから trunk にマージすることはありません。
より新しいリリースブランチがリリースされているのが自然だからです。
従って、リリースブランチの削除は誰も傷つけない後始末作業なのです。
バージョン管理ツールによっては削除を取り消す（元に戻す）こともできるでしょう。
Git では、リリースブランチを削除する前に、そのブランチの先頭のコミットについてタグを作成しなければなりません。
宙ぶらりんになったコミットはゴミとして回収されてしまうからです。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">1998, White Paper</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.perforce.com/sites/default/files/pdf/perforce-best-practices.pdf">High-level Best Practices in Software Configuration Management</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">2018, Edward Thompson on MSDN</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://blogs.msdn.microsoft.com/devops/2018/04/19/release-flow-how-we-do-branching-on-the-vsts-team/">Release Flow: How We Do Branching on the VSTS Team</a></td>
        </tr>
    </table>
</div> -->

- 1998, White Paper：[High-level Best Practices in Software Configuration Management](https://www.perforce.com/sites/default/files/pdf/perforce-best-practices.pdf)
- 2018, Edward Thompson on MSDN：[Release Flow: How We Do Branching on the VSTS Team](https://blogs.msdn.microsoft.com/devops/2018/04/19/release-flow-how-we-do-branching-on-the-vsts-team/)
