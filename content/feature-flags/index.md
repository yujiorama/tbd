---
date: 2016-04-01T20:08:11+01:00
title: 機能フラグ（Feature flags）
weight: 41
---

<!-- Feature Flags are a time-honored way to control the capabilities of an application or service in a large decisive way.  -->
大規模なアプリケーションやサービスの能力を、明示的に制御する伝統的な方法が、機能フラグです。

### 具体例

<!-- Say you have
an application or service that launches from the command-line that has a `main` method or function. Your feature flag 
could be `--withOneClickPurchase` passed in as a command-line argument. That could activate lines of code in the app to 
do with Amazon's patented one-click purchasing 
experience.  Without that command-line argument, the application would run with a shopping cart component. At least
that's the way the developers coded that application. The 'One Click Purchase' and 'Shopping Cart' alternates are 
probably also the same language that the business people associated with the project use. It gets complicated in 
that flags need not be implicitly a/b or new/old, they could be additive. In our case here, there could also be a
`--allowUsersToUseShoppingCartInsteadOfOneClick` capability. Flags can be additive, you see. -->
`main` メソッド（あるいは関数）を定義した、コマンドラインから起動できるアプリケーション（あるいはサービス）があるとします。
そして、機能フラグは `--withOneClickPurchase` のように指定できることにします。
このフラグは、Amazonが特許を取得している1クリック購入を実装したコードを有効にします。
フラグを指定しなければ、アプリケーションは指定した商品をショッピングカートに移動するでしょう。
プロジェクトに参加している業務担当者の中には、「1クリック購入」と「ショッピングカート」を同じ意味で使う人がいるかもしれません。
フラグは「A と B」あるいは「古い　と　新しい」を区別できるような名前にする必要はないのですが、他のフラグが増えるかもしれないので、このままではより複雑化してしまいます。
ここでは、`--allowUsersToUseShoppingCartInsteadOfOneClick` という名前に変更するとより追加しやすくなります。

{{< note title="フラグとはトグルのことです" >}}
<!-- Industry Luminary, Martin Fowler, calls this technique 'Feature Toggles', and wrote a foundational definition (see refs below).
Feature Flags is in wider use by the industry, though, so we're going with that. -->
業界を代表する賢人 Martin Fowler はこの手法を「機能トグル」と呼んでいます。
しかし、機能フラグという呼び方も広く使われているので、私たちは機能トグルと呼ぶことにしました。
{{< /note >}}

## 粒度

<!-- It could be that the flag controls something large like the UI of a component. In our case above we could say that
`OneClickPurchasing` and `ShoppingCart` are the names of components.  It could be that the granularity of the flag
is much smaller - Say Americans want to see temperatures in degrees Fahrenheit and other nationalities would 
prefer degrees Centigrade/Celsius. We could have a flag `--temp=F` and `--temp=C`. For fun, the developers also added
`--temp=K` (Kelvins). -->
フラグは、UI要素やコンポーネントのようにある程度の大きさを持つ要素を制御するのに使うようにしましょう。
前の例では `OneClickPurchasing` と `ShoppingCart` コンポーネントを使い分けていました。
フラグの対象部分はもっと小さくなる場合もあります。
例えば、アメリカでは気温を華氏で表示するのに、他の国では摂氏で表示します。
そういう場合はフラグで `--temp=F` とか `--temp=C` とかすればいいですし、開発者は `--temp=K` （ケルビン）を追加できます。

## 実現方法

<!-- For the `OneClickPurchasing` and `ShoppingCart` alternates, it could be that a `PurchasingCompleting`
abstraction was created. Then at the most primordial boot place that's code controlled, the `--withOneClickPurchase` flag
is acted upon: -->
`OneClickPurchasing` や `ShoppingCart` というコンポーネントを代表する `PurchasingCompleting` という抽象を作成します。
そうしたら、最初に初期化するところで `--withOneClickPurchase` フラグを参照します。

<!-- Java, by hand: -->
自分でコンポーネントを組み立てている場合は次のように記述します。

```java
if (args.contains("--withOneClickPurchase")) {
  purchasingCompleting = new OneClickPurchasing();
}
```

<!-- Java Dependency Injection via config: -->
DIコンテナでコンポーネントを組み立ている場合は次のように記述します。

```java
bootContainer.addComponent(classFromName(config.get("purchasingCompleting")));
```

<!-- There are many more ways of passing flag intentions (or any config) to a runtime.  If you at all can, you want to
 avoid if/else conditions in the code where a path choice would be made. Hence our emphasis on an abstraction. -->
フラグを（設定値を）実行時に参照する方法はいろいろあります。
どれでも利用できる場合は、コード中に `if` 文を追加する方法はよくありません。
抽象度を高くする方法がいいでしょう。

## 継続的インテグレーションとパイプライン

<!-- It is important to have CI guard your reasonable expected permutations of flags. That means tests that happen on an
application or service after launching it, should also be adaptable and test what is meaningful for those flag 
permutations. It also means that in terms of CI pipelines there is a fan-out **after** unit tests, for each meaningful
flag permutation. A crude equivalent is to run the whole CI pipeline in parallel for each meaningful flag permutation.
That would mean that each commit in the trunk kicks off more than one build - hopefully from elastic 
infrastructure. -->
適切なフラグの組み合わせに関する期待値を担保するためにCIを用意するのは重要です。
起動したアプリケーション（あるいはサービス）について、フラグの組み合わせの妥当性や有意性をテストするためです。
つまり、ユニットテストの**後**にファンアウトして、フラグの組み合わせごとにテストするCIパイプラインが必要だということです。
フラグの組み合わせごとに並列でテストを実行するパイプラインに相当する内容になります。
trunk へコミットするたびに複数のビルドを開始することになるので、弾力性のある（必要に応じて稼働できる）インフラがあるといいでしょう。

## 実行時に切り替え可能なフラグ

<!-- Sometimes flags set at app launch time is not enough. Say you are an Airline, selling tickets for flights online.
You might also rent out cars in conjunction with a partner - say 'Really Cool Rental Cars' (RCRC). The connection to 
any partner or their up/down status is outside your control, so you might want a switch in the software that works 
without relaunch, to turn "RCRC partner bookings" on or off, and allow the 24&#47;7 support team to flip it if certain 'Runbook' conditions
have been met.  In this case, the end users may not notice if Hertz, Avis, Enterprise, etc are all still amongst
the offerings for that airport at the flight arrival time. -->
起動時にフラグを指定できるだけでは不十分な場合があります。
オンラインで飛行機の旅券を販売するシステムがあるとしましょう。
代理店によっては自動車を借りられるようになっているとします。
連携する代理店（のシステム）や、連携先システムが稼働しているかどうかは、あなたのシステムから制御できません。
だから、アプリケーションを再起動することなく、スイッチを切り替えられるようにしたくなるはずです。
また、24時間365日対応している監視チームには、必要に応じてスイッチを切り替える「手順書」が必要になるでしょう。
そうしないと、利用者は飛行機が到着してから Hertz や Avis や Enterprise 等、どこで車を借りられるのか気付けない可能性があります。

<!-- Key for Runtime switchable flags is the need for the state to persist. A restart of the application or service should
not set that flag choice back to default - it should retain the previous choice. It gets complicated when you think
about the need for the flag to permeate multiple nodes in a cluster of horizontally scaled sibling processes. For
that last, then holding the flag state in Consul{{< ext url="https://www.consul.io" >}}, 
Etcd{{< ext url="https://github.com/coreos/etcd" >}} (or equivalent) is the modern way. -->
実行時に切り替え可能なフラグを実現するには、状態を永続化しなければなりません。
アプリケーション（あるいはサービス）を再起動しても、フラグの状態を元に戻してはいけないからです）。
クラスタノードや、水平方向にスケールアウトした複数のプロセスへ状態を伝達するための仕組みは複雑になってしまうかもしれません。
Consul{{< ext url="https://www.consul.io" >}} や Etcd{{< ext url="https://github.com/coreos/etcd" >}} のようなミドルウェアを使用するのが今風のやり方です。

## ビルド時にフラグを設定する

<!-- Build flags affect the application or service as it is being built. With respect to the `--withOneClickPurchase` flag again,
the application would be incapable at runtime of having that capability if the build were not invoked with the suitable
flag somehow. -->
アプリケーション（あるいはサービス）をビルドするときにフラグを設定するのがビルドフラグです。
フラグを指定してビルドしたアプリケーションでなければ、実行時にフラグを参照できない場合もあるでしょう。

## A/B テスト、あるいはベータテスト

<!-- Pushing code that's turned off into production, allows you to turn it on for ephemeral reasons - you want a subset of
users to knowingly or unknowingly try it out. A/B testing (driven by marketing) is possible with runtime flags. So is 
having beta versions of functionality/features available to groups. -->
一部を無効化したソースコードを本番環境に展開しておくと、一時的に有効化できる場合があります。
既知の、あるいは未知の、一部のユーザーを対象に実験できるのです。
実行時に切り替え可能なフラグがあると、A/B テスト（マーケット駆動）を実施できます。
つまり、一部のグループを対象に、ベータ版の機能を提供できるのです。

## 技術的負債という落とし穴

<!-- Flags get put into codebases over time and often get forgotten as development teams pivot towards new business deliverables.
Of course, you want to wait a while until it is certain that you are fixed on a toggle state, and that's where the 
problem lies - the application works just fine with the toggle left in place, and the business only really cares
about new priorities. The only saving grace is the fact that you had unit tests for everything, even for code that
is effectively turned off in production. Try to get the business to allow the remediation of flags (and the code
they apply to) a month after the release. Maybe add them to the project's readme with a "review for delete" date. -->
フラグはコードベースのどこかにいつでも追加できるのですが、別のビジネス目標にピボットすると忘れられがちです。
もちろん、トグルの状態を固定できるようになるまで待っていたいのはわかるけど、逆にそれが問題になります。
トグルをそのままにしておいてもアプリケーションは正常に動作するのだから、ビジネスとして新しい優先順位を設定することはほとんどありません。
唯一の救いは、実質的に本番環境ではトグルがオフになっているとしても、全ての部分をユニットテストしていることです。
翌月のリリースでフラグ（と関連するコード）を改善できるよう、ビジネス担当者を説得してください。
プロジェクトの README には「削除するかどうかレビューする」ことを日付と共に記述しておいてもいいでしょう。

## 経緯

<!-- Some historical predecessors of feature toggles/flags as we know it today:   -->
機能トグル（フラグ）についてはいろいろな歴史的経緯があります。

<!-- - Unified Versioning through Feature Logic (Andreas Zeller and Gregor Snelting, 1996){{< ext url="http://www.cs.tufts.edu/~nr/cs257/archive/andreas-zeller/tr-96-01.pdf" >}} - white paper.
- Configuration Management with Version Sets: A Unified Software Versioning Model and its Applications (Andreas Zeller's, 1997){{< ext url="https://www.st.cs.uni-saarland.de/publications/files/zeller-thesis-1997.pdf" >}} - Ph.D. thesis. -->
- 機能制御ロジックに基づく統一バージョン管理 (Andreas Zeller and Gregor Snelting, 1996){{< ext url="http://www.cs.tufts.edu/~nr/cs257/archive/andreas-zeller/tr-96-01.pdf" >}} - white paper.
- バージョン集合に基づく構成管理：統一バージョン管理モデルとその応用 (Andreas Zeller's, 1997){{< ext url="https://www.st.cs.uni-saarland.de/publications/files/zeller-thesis-1997.pdf" >}} - Ph.D. thesis.

<!-- There's a warning too:  -->
警句めいた文章も公開されています。

- "#ifdef considered harmful" (Henry Spencer and Geoff Collyer, 1992){{< ext url="http://www.literateprogramming.com/ifdefs.pdf" >}} - white paper.

<!-- Brad Appleton says: -->
Brad Appleton は次のように述べています。

<!-- <br><div style="padding-left: 45px; padding-right: 45px"><span style="font-size: 150%">&ldquo;</span>
The thing I do not like about feature-toggles/flags is when they end up NOT being short-lived as intended, 
and we end up having to revisit Spencer and Collyer's famous paper. The funny thing is feature-branches 
started out the same way. When they were first introduced it was for feature-teams using very large features, and the 
purpose of the separate branches was because too many people were trying to commit at the same time to the same branch. 
So the idea was use separate branches (for scale) and teams would integrate to their team-branch daily or more often 
WITH at least nightly integration across all feature-branches [sigh].
</div> -->
<br><div style="padding-left: 45px; padding-right: 45px"><span style="font-size: 150%">&ldquo;</span>
機能トグル・フラグの好きじゃないところは、当初の意図と違って「短命で終わらない」場合があるのと、Spencer と Collyers の有名な論文を見直さないといけないところです。

機能ブランチも同じように使われだしたのは面白いですね。
最初は大きな機能を機能チームが開発するために使い始めたのですが、ブランチを分けたのは多数のメンバーが同時に同じブランチへコミットしようとしていたからです。
より多くコミットできるよう別のブランチを用意し、夜間ジョブで全ての機能ブランチを毎日、あるいはそれより短期間に統合しようとしていたのでした。[sign]
</div>

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">29 Oct 2010, MartinFowler.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://martinfowler.com/bliki/FeatureToggle.html">Feature Toggle</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">30 May 2011, TechCrunch article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://techcrunch.com/2011/05/30/facebook-source-code">The Next 6 Months Worth Of Features Are In Facebook's Code Right Now, But We Can't See</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">19 Jun 2013, Slides from a talk</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://www.slideshare.net/cb372/branching-strategies">Branching Strategies: Feature Branches vs Branch by Abstraction</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">10 Oct 2014, Conference Talk</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.perforce.com/merge/2014-sessions/trunk-based-development-enterprise-its-relevance-economics">Trunk-Based Development in the Enterprise - Its Relevance and Economics</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">08 Feb 2016, MartinFowler.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://martinfowler.com/articles/feature-toggles.html">Feature Toggles</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">23 May 2017, DevOps.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://devops.com/feature-branching-vs-feature-flags-whats-right-tool-job/">Feature Branching vs. Feature Flags: What’s the Right Tool for the Job?</a></td>
        </tr>
    </table>
    
</div> -->

- 29 Oct 2010, MartinFowler.com article: [Feature Toggle](https://martinfowler.com/bliki/FeatureToggle.html")
- 30 May 2011, TechCrunch article: [The Next 6 Months Worth Of Features Are In Facebook's Code Right Now, But We Can't See](http://techcrunch.com/2011/05/30/facebook-source-code")
- 19 Jun 2013, Slides from a talk: [Branching Strategies: Feature Branches vs Branch by Abstraction](http://www.slideshare.net/cb372/branching-strategies")
- 10 Oct 2014, Conference Talk: [Trunk-Based Development in the Enterprise - Its Relevance and Economics](https://www.perforce.com/merge/2014-sessions/trunk-based-development-enterprise-its-relevance-economics")
- 08 Feb 2016, MartinFowler.com article: [Feature Toggles](https://martinfowler.com/articles/feature-toggles.html")
- 23 May 2017, DevOps.com article: [Feature Branching vs. Feature Flags: What’s the Right Tool for the Job?](https://devops.com/feature-branching-vs-feature-flags-whats-right-tool-job/")
