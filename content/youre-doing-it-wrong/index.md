---
date: 2016-09-01T20:08:11+01:00
title: そのやり方は間違っています
weight: 91
---

## trunk というブランチ名がただの名前でしかない

<!-- Say you are using Subversion, and you accepted its default directory design, when you made a new repository. That will
give you 'trunk', 'tags' and 'branches' as directory names. The mere fact that you have a branch called trunk does not
mean you are doing Trunk-Based Development. "We merge branches back to trunk often" can be heard a lot in the industry,
and if you are grouping multiple developers on those branches or they are not deleted after a couple of days, then it is
not the Trunk-Based Development branching model. -->
Subversion で新しいリポジトリを作るときは、標準的なディレクトリ構成にしましょう。
つまり、最上位に `trunk` と `tags` と `branches` を作るのです。
`trunk` というブランチがあるだけで、トランクベース開発をやっていることにはなりません。
「私たちは頻繁に `trunk` へマージしているから」という話をよく聞きます。
複数の開発者を同じブランチにしばりつけて、作業が完了してから何日も削除しないようなら、それはトランクベース開発におけるブランチモデルでありません。

## リリースブランチにしたバグ修正コミットを trunk へチェリーピックする

<!-- All your developers are using a trunk and they are doing the right thing with respect to not breaking the build. Your release
cadence is infrequent enough to allow you to cut a release branch on a just in time basis and then harden that in the run
up to the actual release. -->
開発者たちは全員 trunk で作業し、ビルドを壊さないようにしているとします。
リリース周期が比較的長いため、必要に応じてリリースブランチを作成しハードニングする時間があるとします。

<!-- But, if you are fixing bugs on the release branch and merging them down to the trunk you are doing
it wrong. There is a chance you might forget to merge it down, and then there is going to be a regression at the next
release moment (fresh branch cut from the trunk). Then egg on face, and recriminations. -->
しかし、リリースブランチで行ったバグ修正を trunk へマージしているとしたら、それは誤りです。
マージするのを忘れてしまう可能性があるし、次のリリースにおける回帰バグとなってしまう可能性があるのです。

<!-- Bugs should be reproduced and fixed on the trunk, and then **cherry-picked** to the release branch. A build should
happen from that, a second confirmation that the issue has been remediated, and that deployment should go live (perhaps
a point release).  If you can not reproduce bugs on the trunk (truly rare), then you have permission to go ahead and
reproduce it on the release branch, fix it there, and merge back. -->
バグは trunk で再現して修正しなければなりません。
リリースブランチにマージ（ **cherry-pick** ）するのはそれからです。
何より先にビルドして、問題が解消していることを確認し、リリースしましょう（点リリースになります）。
滅多にありませんが、trunk でバグを再現できなかったら、リリースブランチで再現させて、修正し、trunk へマージするしかありません。

## マージじゃなくてリリースブランチから（へ）チェリーピックする

<!-- The developers cut a release branch because their release cadence is low, and they're hardening and certifying the release
there. BUT in the days that lead up to the release, they are also doing general merges up to the release branch from
the trunk. That is not right - it seems like they cut the branch on the wrong day. Maybe the business people on the team
are pushing too hard to make a date. -->
リリースケイデンスが低いと、開発者はリリースブランチを作成して、リリースのためのハードニングをします。
しかし、リリース当日になっても trunk からリリースブランチへマージすることがよくあります。
これは誤りです。
リリースブランチを作成するタイミングが早すぎたのです。
もしかしたら業務担当者が厳しい予定を設定したのかもしれません。

<!-- Cherry-picking every commit since the branch-cut to the branch from the trunk is the same thing of course. -->
リリースブランチへのコミットを1つ1つ trunk へチェリーピックしなければならないとしたら、たぶん同じような原因があります。

## 短命の機能ブランチの寿命

<!-- The [short-lived feature branch](/short-lived-feature-branches/) should only last a day or two and never diverge from the trunk enough so that a
merge back is problematic. After the seal of approval from code reviewers and CI
daemons, it should be merged back into the trunk. It should be deleted, as proof of convergence.
The developer in question may then go ahead and make the next short-lived feature branch for the next story/task they're doing. -->
[短命の機能ブランチ](/short-lived-feature-branches/) は文字通り1日あるいは2日以上 trunk から離れるべきではありません。
マージが困難になるからです。
レビューアとCIデーモンに承認されたら、すぐに trunk へマージしなければなりません。
また、問題が解決したことを示すためすぐに削除しなければなりません。
そうすれば、開発者は次のストーリー（あるいはタスク）のために、新しい機能ブランチを作成できます。

## 短命の長期ブランチで作業する開発者の人数

<!-- If there is more that one developer (and the developer's pairing partner) on the same [short-lived feature branch](/short-lived-feature-branches/),
then that branch is at risk of not being short-lived. It is at risk of being more and more like a release branch
under active development, and not short at all. -->
同じ[機能ブランチ](/short-lived-feature-branches/) で1人以上（1ペア以上）の開発者が作業していると、寿命が長くなりがちです。
より多くの作業中のリリースブランチが出来てしまうリスクになるのです。

<!-- There is a risk too, that a developer may choose to pull changes to their workstation **from a short-lived feature
branch** rather from the trunk. They may think that the code review for that short-lived feature branch is going to take
too long, or they need the changes before they are ready. Unfortunately, there is no way that the current generation of
code portals can prevent people pulling changes from non-trunk branches. -->
開発者の作業マシンに、trunk ではなく **機能ブランチ** から変更をプルしがちになってしまうリスクもあります。
機能ブランチのレビューが長くなることを予想しているのかもしれませんし、もう少し変更が必要なのかもしれません。
残念ながら、今のソースコードポータルシステムには、開発者たちが trunk 以外のブランチから変更を取得するのを制限する機能はありません。

## 毎日何かが変化している

<!-- Developers take stories or tasks from the backlog, implement them with tests, and push them through code review and quality checks
into the shared trunk. They don't slow down that activity as they get closer to a release date. At least the majority do not. A very
small subset of the development team may focus on the release candidates being made from the release branch (if that
branch exists at all), and towards fixes in the trunk that will be cherry-picked into that branch. For most of the team, though,
every day looks the same. Also proximity to a release does not slow down the rate at which changes are being pushed into the
trunk. **There is certainly no "code freeze" with Trunk-Based Development**, as team leadership focuses on protecting the
majority of developers from the distraction of release preparation. If that protection is missing, something needs
fine-tuning, and the branching model could be it. -->
開発者はバックログからストーリー（あるいはタスク）を取り出して、実装し（テストも）、コードレビューと品質チェックが完了したらコミットを共有 trunk へプッシュします。
リリース日時が近くなったからといって活動がゆっくりになることはありません。
ごく一部のメンバーだけが、リリースブランチでリリース候補の作業（trunk のバグ修正コミットをチェリーピックするなど）をします。
チームメンバーの大半は普段通りです。
同様に、trunk にプッシュされるコミットも減りません。
**トランクベース開発には「コードフリーズ」がないのです**。
チームのリーダシップは、大半のメンバーがリリースに伴う作業を気にしないで済むようにすることに注力します。
そういうリーダーシップが無いなら別のブランチモデルを検討するなどの改善が必要です。

## 1つのリリースブランチを維持し続ける

<!-- If you are making a release branch, you should not keep it alive for a series of major releases. You principal
mechanism to land code on that branch is the branch creation itself. After that only cherry picks for bug
fixes as found. Even then they should diminish over time. If release branches is your model then the reality should
be that pressure mounts to create a new release branch (from trunk), and evidence is that there is no more
cherry-picks from trunk to the old release branch. In terms of release you might have witnessed releases 1.1, 1.1.1 and 1.1.2 be done from tags on that release branch, but the cherry picks should diminish to zero over time, and activity reconvene around an newer release branch - for the 1.2 release probably. -->
リリースブランチを作成するとしても、メジャーリリースのブランチが並ばないようにしたほうがいいでしょう。
そもそも、ブランチを作成するからそういうことになってしまうのです。
そういうブランチにはバグ修正コミットのチェリーピックが並んでいるだけになるはずですし、時間が経てばそれも減ってくるでしょう。
あなたがリリースブランチを理想的な状態に保つのが一番よいと考えているなら、現実は trunk から新たなリリースブランチの作成を要求するでしょう（圧力をかけるでしょう）。
それが目に見える形となって現れるのは、前のリリースブランチにチェリーピックするバグ修正コミットがほとんど無くなった場合です。
1.1 とか 1.1.1 とか 1.1.2 みたいなリリースをするとき、リリースブランチにタグを作成する場合があります。
いずれにしても時間が経てばチェリーピックするコミットは無くなるので、新しいリリースブランチで作業をすることになります。
そのときのバージョン番号は 1.2 とかになるでしょう。

## リリースブランチから別のリリースブランチにマージする

<!-- We only merge from trunk to release branches (if we branch at all), and then only a small percentage of the
commits to trunk, and only using the cherry-pick method of merging.  Sure, two release branches could be in
play for short periods of time, but the team should cherry-pick merge from trunk to two different branches.
In some VCS tools (Perforce and Subversion) those can be in the same atomic commit, but that is not
strictly necessary. -->
私たち（トランクベース開発）は trunk からリリースブランチにマージします。
新しいコミットが少しだけならチェリーピックする場合もあります。
たまたま2つのリリースブランチがあるとしても、リリースブランチから別のリリースブランチへコミットをチェリーピックしてはいけません。
バージョン管理ツールによっては完全に同一のコミットを作成できますが（Subversion や Subversion）、必ずしも必要な機能ではありません。

## リリースブランチに対する変更を全て trunk へマージする

<!-- Bug fixing on the release branch? If yes, then that is covered above. If you are not doing that then you have no need
to merge anything back from the release branch - you can just delete it after you're sure there will not be any more
releases from it. -->
もしかしてリリースブランチでバグ修正しているんですか。
もしそうなら、前の方に書いてあることを参照してください。
そうでなければ、リリースブランチから trunk へマージするコミットは存在しません。
もうリリースしないことが明らかなブランチは削除してしまえばいいのです。
