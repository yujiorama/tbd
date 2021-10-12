---
date: 2016-05-06T19:56:50+01:00
title: trunk からリリース
weight: 56
---

<!-- Teams with a very high release cadence do not need (and cannot use) release branches at all. They have to release from
the trunk. -->
リリースケイデンスがとても早いチームにはリリースブランチが必要ありません。
（だから）trunk からリリースしなければなりません。

![](release_from_trunk.png)
([key](/key/))

<!-- It is most likely that such teams do not use a Dewey-decimal release numbering scheme, and instead have something
referent to the commit number or date and time.  They probably also choose to roll forward and fix the bug on the
trunk as if it were a feature, albeit as quickly as possible. -->
そんなチームが、リリース番号体系としてデューイ十進分類法を使うことはありません。
代わりにコミット番号や日時から生成する場合があります。
また、ロールフォワード戦略を採用し、バグを普通の機能と同じように trunk で修正し、できるだけ早くリリースしようとします。

<!-- Here's what stylized commits look like: -->
典型的なコミットの系列は次のようになります。

![](release_from_trunk2.png)
([key](/key/))

<!-- No slow down around a release and bug fixes inline. -->
リリースがあってもバグの修正があっても遅くなったりしません。

<!-- Teams with one release a day (or less) **might** still make a branch, to cherry-pick the bug-fix to
and release from: -->
1日に1回以下しかリリースしないチームなら **おそらく** リリースブランチを作成して trunk のバグ修正をチェリーピックしたほうがいいでしょう。

![](release_from_trunk3.png)
([key](/key/))

<!-- {{< note title="Branches can be made retroactively" >}}
Newbies to source-control systems often forget that you don't have to make a branch because you think you might need
it in the future. For any source-control technology made today, you can choose the revision in the past to branch
from. The outcome is exactly the same as if you had made it at the time.
{{< /note >}} -->
{{< note title="ブランチは遡って作成できる" >}}
ソースコード管理システムの初心者はあまり自分でブランチを作成する機会がないので、ブランチの作り方を忘れがちです。
最近のソースコード管理システムなら、指定した過去の履歴からブランチを作成できるようになっています。
最新のブランチから作成した場合と同じです。
{{< /note >}}
