---
date: 2016-06-05T20:10:46+01:00
title: 継続的コードレビュー
weight: 65
---

## 現代における壁

<!-- Continuous Code Review is where the team commits to processing proposed commits (to trunk) from teammates' trunk speedily.  -->
継続的コードレビューとは、trunk に向けて提案されたコミットを素早く処理する方法です。

<!-- The idea is that a system (the code portal probably) allows developers to package up commits for code review and get
that in front of peers quickly. And that peer developers make a commitment to do code reviews objectively and quickly.  -->
開発者がまとめたコードレビューの対象コミットを、すぐにピアレビューできるようにする仕組みを利用する、ということです（ソースコードリポジトリのポータルサイトのようなものかもしれません）。
レビューアが、客観的かつ素早いレビューができるようにします。

<!-- There is a cost to multi-tasking, so maybe someone in the dev team who is between work items at that moment should focus
on the review before they start new work. With a continuous review ethos, it is critical that code reviews are not 
allowed to back up. -->
マルチタスクにはコストがかかるので、開発チームのメンバーは、次の仕事に取り掛かる前にレビューをするように意識しなければなりません。
継続的レビューのエートス（本質的な考え方？）において、レビューを後回しにしないという考え方が重要です。

<!-- Companies doing Extreme Programming, often allow that pair of eyes to count as a review. Some companies require
multiple reviews of code. For "the pair as reviewers too" scenario, one might have been enough and that commit will 
land in the trunk, without others looking at it. Five minutes and 20 seconds into Guido van Rossum's famous 2006 Mondrian 
presentation, he states "code review is a best alternative to pair programming", and that it is "basically 
asynchronous pair-programming". -->
XP チームはペアを1人のレビューアと見做すことが多いようです。
複数人でレビューすることを条件にしているチームであっても、ペアでレビューしたら他の誰かがレビューしなくてもOKにしているところがあるようです。
Guido van Rossum は Mondrian に関する有名なプレゼンテーションで次のように述べています。

> コードレビューはペアプログラミングに関する最善の代替手段であり、基本的には非同期型のペアプログラミングなのです。

### プルリクエスト

<!-- The pull-request (PR) model introduced by GitHub is the dominant code review model today. The concept was available
from GitHub's launch in 2008 and has revolutionized both open source and enterprise software development. Google were
secretly doing the same thing with custom tooling around their Perforce install from about 2005, and Guido's
presentation on Mondrian in 2006 (as mentioned) leaked that to the world (see below). -->
GitHub の広めたプルリクエスト（PR：pull-request）モデルは、現代の中心的なコードレビューモデルです。
GitHubが創業した2008年から存在する考え方で、OSS 開発とエンタープライズ開発の両方に革命をもたらしました。
Google はそれより前から同じことを社内でやっていたけど、それが明らかになったのは 2006 年の Mondrian のプレゼンテーションでした（2005年時点で Perforce の拡張ツールを開発、使用していました）。

<!-- A PR is one
or more commits towards a goal described in an accompanying piece of text. The act of creating the PR from the branch
signals the end (or a pause) in work, and the wish for the reviewers to get busy (and the CI daemon to wake up and 
build/verify the branch). There are caveats though. -->
PR とは、目標と簡単な説明文と複数のコミットです。
PR を作成するということは、そのブランチで行っていた作業が完了した（あるいは中断した）ので、レビューアに仕事を依頼するということです（また、CIデーモンを起こしてビルドと検証をしてもらいます）。
いくつか補足説明が必要ですね。

#### プルリクエストによる OSS への貢献

<!-- These can come from anyone who has an account on GitHub (or equivalent). They will have forked your repository and the
PR will be about commits that would come back to your repository. They may delete their repository after you consume
their commits. If these are unsolicited you may well
take your time reviewing them. Indeed you may never consume them, if you don't like them. Hardly continuous, but open
source is mostly a volunteer activity. -->
GitHub にアカウントを登録した人なら、誰にでも OSS に貢献できます。
あなたのリポジトリをフォークして、いくつかのコミットと共に、あなたのリポジトリに対するPRを作成できます。
コミットが受け入れられたら、フォークしたリポジトリは削除することになるでしょう。
一方的にレビューを押し付けられることもあるでしょう。
嫌ならコミットを受け付けなければいいだけです。
OSS 開発は大半がボランティア活動で、継続するのは大変です。


#### 同僚からプルリクエストしてもらう

<!-- Regardless of branching model, the wish is for the PR to be reviewed fairly quickly. On GitHub (and possibly others) the PR
can come from a fork or a branch in the main repo. There is little difference to the processing of these. In Trunk-Based 
Development teams, the PR should be on a [short-lived feature branch](/short-lived-feature-branches/) and processed very 
quickly by reviews towards merging back to trunk/master. A few minutes for the review is best, and tens of minutes 
acceptable. More than a hour or two, and you are negatively affecting cycle times. -->
どのようなブランチモデルであれ、PRはできるだけ早くレビューしたほうがいいでしょう。
GitHub ではフォークしたリポジトリや、同じリポジトリのブランチから PR できます。
トランクベース開発では PR の扱いが少し独特です。
PR は [短命の機能ブランチ](/short-lived-feature-branches/) で作成するべきで、とても短時間でレビューします。
数分で終わるのが一番ですが、10分くらいまでなら許容範囲でしょう。
1時間（2時間）を越えるとサイクルタイムに悪影響が出ます。

<!-- The short-lived feature branch may have received many commits before the developer initiated the pull request. Some
developers will squash (rebase) the changes into a single commit before starting code review. Some teams have a policy 
in favor of or against squash/rebase.  -->
PR を作成する前なら、短命の機能ブランチにはたくさんのコミットをするでしょう。
コードレビューを始める前に、複数のコミットを1つのコミットに整理する（squash/rebase）場合もあります。
チームとして squash/rebase の方針を決めているところもあります。

<!-- {{< note title="Common Code Owners" >}}
Commits being reviewed are never rejected for "Only I am allowed to change source in this package" reasons. Rejections
must be for objective and published reasons.
{{< /note >}} -->
{{< note title="コードの共同所有" >}}
「そのパッケージのソースコードを変更していいのは私だけだから」みたいな理由でコミットを拒否してはいけません。
コミットを拒否するなら、客観的で公正な理由が必要です。
{{< /note >}}

## エンタープライズ開発におけるコードレビュー（過去の話）

<!-- In enterprises, if code review was done at all prior to 2008, it was done in a batch, and probably a group activity.
It was often abhorred as it gave a lead developer/architect a moment to set an agenda, round on a large portion of the 
attendees and make sure that their own code flubs were not discussed at all. -->
2008年より前のエンタープライズ開発におけるコードレビューは、グループでまとめて行うアクティビティでした。
リード開発者やアーキテクトがアジェンダを準備して、多数の参加者がしらみつぶしに間違い探しをする感じだったので、とても嫌われていました。

<!-- Historically, open source teams never had the luxury of procrastinating about code review. They either did code reviews
as they went (perhaps days were the review cadence, not hours or minutes), or they did not bother at all. -->
昔から、OSS の開発者たちはコードレビューを後回しにするべきではないと考えていました。
必要になったらすぐにレビューをするか（少なくとも毎時、毎分ではなく、毎日くらいの周期）、全然しないかのどちらかでした。

## 参考資料

<!-- See [Game Changers - Google's Mondrian](/game-changers/index.html#google-s-internal-devops-2006-onwards) and
[Game Changers - GitHub's Pull Requests](/game-changers/index.html#github-s-entire-platform-2008-onwards) for the industry impact of continuous code 
review. -->
[Game Changers - Google's Mondrian](/game-changers/index.html#google-s-internal-devops-2006-onwards) や [Game Changers - GitHub's Pull Requests](/game-changers/index.html#github-s-entire-platform-2008-onwards) を参照して、継続的レビューがソフトウェア業界に与えた影響を考察してください。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">13 Nov 2014, Blog Article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2014/11/13/code-review-the-unit-of-work-should-be-a-single-commit/">Code Review - the unit of work should be a single commit</a></td>
        </tr>
    </table>
</div> -->

- 13 Nov 2014, Blog Article：[Code Review - the unit of work should be a single commit](http://paulhammant.com/2014/11/13/code-review-the-unit-of-work-should-be-a-single-commit/)