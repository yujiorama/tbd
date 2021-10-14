---
date: 2016-07-05T20:10:46+01:00
title: 並行開発と逐次的なリリース
weight: 75
---

## 並行開発とは

<!-- Your company wants a stream of major functionality to arrive in the application you are pushing live at a regular
cadence. Because you are good Extreme Programmers, you know that consecutive development of consecutive releases
is best. However, the effort and length of time needed to complete each major piece of functionality is
large enough to require different project teams cooperating towards that plan. Some of those teams will be within
the same codebase. Some may be dependent services that the application will invoke over the wire. Not everything 
is equal effort it seems, yet the business wants a specific rollout, including dates and can plan that even eighteen 
months ahead. They are very specific because there is an impact on the user community (staff, clients, customers or 
members of the public). Driving departments may include training, marketing, finance. -->
会社として、アプリケーションの一連の主要機能を、定期的にリリースしていきたい場合があるとします。
あなたは優れた XP プログラマーなので、「並行開発と逐次的なリリース」という戦略が最適だと確信しています。
しかし、規模が大きく時間もかかるそれぞれの主要機能を完成させていくには、何らかの計画に基づいて別々のプロジェクトが協調していなければなりません。
コードベースを共有するプロジェクトもあるでしょうし、ネットワークで連携する依存サービスやアプリケーションを開発する場合もあるでしょう。
それぞれに大変さはあるでしょうが、ビジネスとしては18カ月先を見据えた計画が立てられるよう、それぞれの機能のリリース期日を決めたいはずです。
ユーザーコミュニティ（社員、クライアント、顧客、公開メンバー）に影響するため、できるだけ具体的にしておきたいところです。
組織的な研修やマーケティングや財務管理も必要です。

## 問題発生

<!-- What you have got is the perfect setup for disaster born from the random bad news events. Things that can and do
happen in software development.  -->
考えられるあらゆる悪いニュースに備えた完璧な計画を立てました。
ソフトウェアを開発していたら起こりそうなことは全て考慮しています。

<!-- Or perhaps one thing was underestimated by 50% and that is realized later rather than sooner.
Should all of the following releases slip too, assuming the company did not attempt to throw bodies at it in an attempt 
to solve it? We all know of Fred Brook's Mythical Man-Month{{< ext url="https://books.google.com/books/about/The_Mythical_Man_Month_Anniversary_Editi.html?id=Yq35BY5Fk3gC" >}}
and Edward Yourdon's Death March{{< ext url="https://books.google.com/books/about/Death_March.html?id=FdAZUX9H_gAC" >}}. -->
50%以下と予測していた出来事が、後になってから現実化しました。
会社が全力で問題を解決しなければ、後に続く全てのリリースを延期しなければならないでしょう。
私たちは人月の神話{{< ext url="https://books.google.com/books/about/The_Mythical_Man_Month_Anniversary_Editi.html?id=Yq35BY5Fk3gC" >}} だけでなく、デスマーチ{{< ext url="https://books.google.com/books/about/Death_March.html?id=FdAZUX9H_gAC" >}} についても理解しているからです。

## リリースの入れ替え

<!-- One compelling answer is to change the order of releases. To the business, that could be a relief even if it requires
re-planning and problems around marketing/education given the impacted staff, clients, customers or members of the 
public. -->
総合的な判断として、リリースの順序を変更することが挙げられます。
計画の見直しが必要になるかもしれませんし、マーケティングや教育に関する問題が、社員、クライアント、顧客、公開メンバーに影響するかもしれませんが、ビジネスにとって他に軽減策がないからです。

## マージの取り消し

<!-- The trouble is that the development teams might have to face a selective un-merge or commenting-out frenzy to support that, depending on
what had merged already. Different branching models have different merge impacts and are either early or late in terms
of keenness for the act of merging. That in itself is disruptive to the business, as they fear and probably witness 
additional delays because of the retooling and new-found nerves. -->
マージを取り消したり、コードをコメントアウトしたりするのを開発チームに要求すると、彼らは怒り狂います。
ブランチモデルによってマージの影響は異なりますし、先にやるか後にやるかの違いでしかありません。
しかし、ビジネスにとっては破壊的な影響があります。
彼らが恐れているのは、道筋を作り直したり新たに整備することでさらに遅延することです。

## フラグ、抽象、パイプライン

<!-- If your team has institutionalized Trunk-Based Development, [Feature Flags](/feature-flags/) pluggable components based
on abstractions (not a world apart from [Branch by Abstraction](/branch-by-abstraction/)), it is in a perfect position 
to reorder releases, and only have a small impact on the throughput of the development team.  -->
トランクベース開発をやっていて、[抽象化によるブランチ](/branch-by-abstraction/) のように抽象を使用した、プラグイン可能なコンポーネントを [機能フラグ](/feature-flags/) で制御できるようになっているなら、リリースの順番を入れ替えるのに丁度いいですし、開発チームのスループットにもほとんど影響しません。

### 事例

<!-- In a real-life case study for an airline in 2012, late in development for the planned release a partner said that they could
not, in fact, meet that date. Their side of the integration was not going to be ready. The airline was code complete but now had to 
reorder releases. The development team's management feared some downtime while the mess was sorted out. The team in question
was able to spin up a new CI pipeline, with the flags/toggles flipped to show the new permutation of features. 
The new CI pipeline confirmed what they had already seen on the command-line build, that there were failures in the 
automated tests (and something in a page did not quite look right anyway). A couple of quick fixes later, and the development 
team assured the airline's management that the releases could reasonably happen in any order. -->
2012年のこと、航空券販売システムの計画リリースが間近になってから、パートナーは計画どおりにリリースできないと申告してきました。
連携機能の開発が終わらないそうです。
航空券販売システム自体の開発は完了しているけど、リリースの順番を入れ替えないといけなくなりました。
開発チームのマネージャーはいろんなことをまとめている期間の停止時間を気にしていました。
開発チームは、さまざまな機能フラグの組み合わせを確かめるCIパイプラインを用意しました。
新しいCIパイプラインは、ビルドは成功するけど、自動テストが失敗することを発見しました（一部の画面表示がおかしくなっていました）。
修正はすぐに完了し、開発チームのマネージャーにどの順番にリリースしても大丈夫であることを証明できました。

<!-- Choosing Trunk-Based Development, [Feature Flags](/feature-flags/) and [Branch by Abstraction](/branch-by-abstraction/)
could be said to be a **hedging strategy** against the costs of larger scheduling changes. -->
トランクベース開発、[機能フラグ](/feature-flags/)、[抽象化によるブランチ](/branch-by-abstraction/) を組み合わせるのは、大幅な計画変更に対する **予防的な戦略** です。

<!-- {{< warning title="Consecutive development of consecutive releases is by far superior!" >}}
Every high throughput Extreme Programming team will tell you that finishing and shipping a release before starting work
as a team on the next releasable slice of work is much better than attempting to do concurrent development of 
consecutive releases.  Sure, some teammates (PM, BA, tech leads) are looking a couple of weeks ahead to make sure that
everything is ready for development and QA automation on a just in time basis but the majority of the dev team will 
only pick up new release work as the previous one has been pushed into production.
{{< /warning >}}  -->
{{< warning title="並行開発と逐次的なリリースは極めて高度なプラクティスです" >}}
高スループットな XP チームなら、並行開発して逐次的にリリースするより、それぞれのリリース断面を順番に完了し、出荷するほうがよいと言うでしょう。
チームメンバーの中には、ちょうど数週間先に開発と品質チェックが完了し、出荷できる状態になるであろう機能に注目している人がいるかもしれませんが（PM、BA、テックリード）、大半のチームメンバーは、次にリリースする機能に集中していることでしょう。
{{< /warning >}}


# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
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
            <td style="padding: 2px" valign="top">14 Jul 2013, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/07/14/legacy-application-strangulation-case-studies/">Legacy Application Strangulation: Case Studies</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">10 Oct 2014, Conference Talk</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.perforce.com/resources/trunk-based-development-enterprise">Trunk-Based Development in the Enterprise - Its Relevance and Economics</a></td>
        </tr>
    </table>
</div> -->

- 19 Mar 2013, Blog Entry：[The Cost of Unmerge](http://paulhammant.com/2013/03/19/cost-of-unmerge/)
- 14 Jul 2013, Blog Entry：[Legacy Application Strangulation: Case Studies](http://paulhammant.com/2013/07/14/legacy-application-strangulation-case-studies/)
- 10 Oct 2014, Conference Talk：[Trunk-Based Development in the Enterprise - Its Relevance and Economics](https://www.perforce.com/resources/trunk-based-development-enterprise)
