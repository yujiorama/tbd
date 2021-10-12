---
date: 2016-05-03T19:56:50+01:00
title: 抽象化によるブランチ
weight: 53
---

<!-- Branch by Abstraction is a set-piece technique to effect a 'longer to complete' change in the trunk. Say a developer
(or a pair of developers), has a change that is going to take five days to complete. There could easily be pressure to
create a branch for this - somewhere that can be unstable for a period of time before it completes (and gets merged
back somewhere). -->
抽象化によるブランチは、trunk で「完了するまで時間のかかる」変更を行うための緻密な手法です。
開発者は（あるいはペアの開発者たちは）変更が完了するまで5日間かかると教えてくれたとしましょう。
作業用にブランチを作るのは簡単ですが、変更作業を完了して trunk へマージするまで不安定な期間が出来てしまうことになります。

<!-- There may be some aspect of repetition to the coding activities that makes it longer to complete. No matter, the
change was predicted as being time-consuming, complex, destabilizing/disruptive to everyone else in the development team. -->
コーディングの完了を遅延させるさまざまな要因が発生するものです。
理由はともあれ、開発チームの全員が、変更は時間のかかる、複雑で、安定性を阻害する、断絶をもたらすものだと考えています。

<!-- **Rules:** -->
**ルール**
 
<!-- 1. There are also a lot of developers already depending on
the code that is subject of the 'longer to complete' change, and we do not want them to be slowed down in any way.
2. No commit pushed to the shared repository should jeopardize the ability to go live. -->
1. 「完了するまで時間のかかる」変更の対象になっているコードに依存している開発者がたくさん存在するが、決してその人たちの作業を遅らせたくない
2. 共有リポジトリにプッシュしていないコミット前のコードは、リリースできる状態を壊しかねない

## 理想敵な手順

<!-- For simplicity's sake, let us say there is code that is 'to be replaced', code 'to be introduced'. -->
説明を簡単にするため、「置き換えるコード」と「追加するコード」があることにします。

<!-- 1. Introduce an abstraction around the code that is to be replaced, and commit that for all to see.  If needed, this
can take multiple commits. None of those are allowed to break the build, and all of them could be pushed to the shared
repository in order, and as done.
2. Write a second implementation of the abstraction for the to-be-introduced code, and commit that, but maybe as 
'turned off' within the trunk so that other developers are not depending on it yet. If needed, this can take multiple 
commits as above. The abstraction from #1 may also be occasionally tweaked, but must follow the same rule - do not 
break the build.
3. Flip the software 'off' switch to 'on' for the rest of the team, and commit/push that.
4. Remove the to-be-replaced implementation
5. Remove the abstraction -->
1. 「置き換えるコード」を包み込む抽象を追加し、全員が参照できるところへコミットします（プッシュします）。必要に応じて複数のコミットをしますが、決してビルドを壊してはいけません。
2. 追加した抽象に対する第二の実装を「追加するコード」として追加し、コミットします。同じ trunk で作業している他の開発者から見えないよう「無効化」しておくこと。必要に応じて複数のコミットをします。手順1で追加した抽象を微調整することもあるでしょうが、決してビルドを壊してはいけません。
3. 同じ trunk で作業している他の開発者から「追加するコード」が見えるよう「有効化」し、コミットします（プッシュします）。
4. 「置き換えるコード」を削除します。
5. 抽象を削除します

<!-- Hopefully, your team uses an IDE that can perform complex refactorings on sets on checkouts, in a way that running the build
after each is an uneventful validation of the refactorings. -->
できれば、チェックアウトしたソースコードで複雑なリファクタリングを実行できる IDE を使うようにしましょう。
リファクタリングをした後、問題があっても騒ぎ立てずにビルドを実行し、確認できます。

## 不自然な具体例

<!-- Let's talk about a car having its wheels upgraded. We should never forget that software engineering is
nothing like conventional construction, and we want to ram that home. At least, it is nothing like conventional 
construction where we are not talking about a production line. -->
自動車のホイールを交換するときのことを考えてみましょう。
ソフトウェアエンジニアリングは従来通りの製造業とは異なるものであることを決して忘れてはいけません。
もし従来通りの製造業について話しているとしても、少なくとも工場の生産ラインのことではありません。

### ルール

<!-- 1. Mechanics must be able to simultaneously work on the upholstery, engine, etc.
2. The car must be drivable after every change.  -->
1. 複数の機械工は、内張りやエンジンなど別の仕事を同時に進められること
2. 全ての作業が完了してから自動車を出荷すること

### 手順

<!-- All steps are efficiently performed raised up on car jacks/lifts/ramps, before lowering the car to the ground again. -->
全ての手順を実施する前に、自動車のジャッキアップやリフトアップやランプアップをして、終わったら元に戻すこと。

<!-- 1. One wheel is removed, put in a container that looks pretty much like a wheel (rotates around an axis,
can bear weight) and replaced on the car. If driven this wheel functions exactly like the 
other three wheels.
2. The wheel-like container gains a second better/desired/alternate wheel, within exactly the same 
physical space (magically). A switch is added inside the car to allow the choice of wheel to be switched conveniently 
- perhaps only before the engine is started, though.
3. The same two operations (#1 and #2) are performed on the other three wheels. Or maybe #1 is done four times, 
followed by #2 four times. The Mechanics experience will guide which is most efficient.
4. After determining that the new wheels are better, the old wheels are removed from the wheel-like containers
and are sent for recycling.
5. The wheel-like containers are also removed from the new wheels, either one by one or all four simultaneously. -->
1. 1輪のホイールを取り外し、ホイールとそっくりの容器（車軸を中心に回転するし、車重にも堪えられます）に入れて、車体に取り付けます。残りの3輪のホイールと全く同じように回転します。
2. ホイール状容器は（魔法のように）物理空間における別のホイールのように扱うことができます。エンジンが停止しているなら、スイッチで自由にホイールを切り替えられるのです。
3. 手順1と手順2を残りのホイールにも行います。あるいは手順1を合計4回実施して、手順2を合計4回実施します。機械工はいろいろ試して最も効率のいいホイールを特定します。
4. 新しいホイールを特定できたら、ホイール状容器から元のホイールを取り外します。外したホイールはリサイクル業者に送付します。
5. 新しいホイールからホイール状容器を取り外します。全てのホイールに同じ作業をします。

<!-- At any stage, when lowered from the jacks/lift/ramps, the car could have been driven (a 'ready to go-live' metaphor). -->
作業がどの時点でも、ジャッキダウン、リフトダウン、ランプダウンすれば、自動車は普通に走行できるものとします（「リリースできる状態」のメタファーです）。

![](cars.png)

<!-- We said 'jacks' above, because that's what mechanics use in real life. Software, however, does not follow the rules
of gravity, or many of the costs of actual construction. With an IDE for a glove, a single finger could reposition 
the car in 3D space to allow easy replacement of the wheels. -->
実際の自動車で作業するときはジャッキアップしますが、ソフトウェアに重力だけでなくさまざまな物理的なコストがかかりません。
IDEという世界の中では、指一本で自動車の位置を動かせるし、ホイールを交換できてしまうのです。

## ソフトウェアの具体例

<!-- A documented case is ThoughtWorks' Go CI-daemon. They changed an Object-Relational mapping library (for persistence),
while not slowing down teammates development activities (rule 1), and not jeopardizing the ability to go live (rule 2). -->
ThoughtWorksで開発しているCIプログラムのGoに関するドキュメントが存在します。
開発の手を止めずに（ルール1）、リリース可能な状態を保ちつつ（ルール2）、ORM ライブラリを変更したのです。

<!-- Going from "iBatis" to "Hibernate" for a bunch of reasons, was their plan. -->
iBatis から Hibernate へ移行するときは次のような手順だったそうです。

<!-- They: -->

<!-- 1. Introduced an abstraction around the classes/components using iBatis directly, and ensured that all
classes/components indirectly referring to iBatis were changed to refer to the abstraction instead.
2. Wrote a second implementation of the abstraction, introducing Hibernate to the codebase, perhaps tweaking the 
abstraction where needed.
3. Did a tiny commit that turned on Hibernate for all teammates.
4. Removed iBatis, then the abstraction and the on/off old/new switch. -->
1. iBatis を直接使用しているクラスやコンポーネントを包み込む抽象を作成します。それらのクラスやコンポーネントを参照している部分が、全て抽象を参照するように書き換えます。
2. 抽象に対して Hibernate を使用する実装を作成します。必要に応じて抽象を修正します。
3. 小さいコミット（切り替えるコミット）をして、全てのチームメンバーが Hibernate を使用するようにします。
4. iBatis の実装を削除して、抽象と、実装を切り替えるスイッチを削除します。

<!-- As it happens you could leave the abstraction in place, if your unit tests are able to benefit because of the
possibility of another seam that can be mocked. -->
ユニットテストでモックするのに便利とか、そういう理由で抽象を残しておく場合もあるでしょう。

## 副次的な利点

### 「移行」を中断したり再開したりするのが簡単

<!-- The migration from old to new can be paused and resumed later casually. This is because the build guards the
second, incomplete, implementation. It does so merely because of a compile stage that turns the abstraction and somewhere 
between 1 to 2 implementation into object code.  If there are unit tests for the two alternates, then even more so. -->
簡単に移行を中断したり再開したりできます。
アーティファクトをビルドするとき、不完全な代替実装を隠蔽できるからです。
抽象はコンパイル時に1つか2つの実装に置き換えられます。
それぞれの実装候補にユニットテストがあってもそれは変わりません。

<!-- If on a real branch, the casual restart of the paused initiative is missing. There's possibly an exponential cost of
restart given the elapsed time since the initiative was paused.  -->
本物のブランチでは中断していた作業を簡単に再開することはできません。
中断した時点から再開しようとした時点までの期間に対する指数関数的なコストが発生する可能性があります。

<!-- Pause and resume is much more likely in an enterprise development organization that does not have limitless coffers.  -->
無限に出てくるコーヒーサーバーのないエンタープライズ組織のほうが、中断や再開を繰り返すことが多いようです。

### プロジェクトを中断するのも簡単
 
<!-- In the case of abandonment, deleting a real long running feature branch is cheaper, but deletion of a
branch by abstraction *thing* is only incrementally more expensive.  -->
プロジェクトが中止になったとき、本物の長命のブランチを削除するのは簡単ですが、抽象化によるブランチで作成した **抽象** は段階的に削除するしかないので、手間がかかります。

## 万能薬ではありません
 
<!-- Branch by Abstraction does not suit all 'change' situations.  -->
抽象化によるブランチはあらゆる「変更」に対応できるわけではありません。

<!-- One is when you have got to support old APIs and previous releases for more than a short period of time.  I.e. when your
dependent customers (or detached clients apps) can choose their own upgrade moment. -->
例えば、しばらくの間、前のリリースで提供した API に対応しなければならない場合に対応できるでしょう。
あなたのAPIを利用する顧客が、最新のAPIに対応してくれるまで待たなければならないような場合です。

<!-- Some years ago the KDE team was mulling their release 5.0 strategy, and wanting to remain parallel to changes in 4.0, so as not to make
mistakes that they had done 
previously{{< ext url="http://tech.slashdot.org/story/11/08/07/2128222/KDE-Frameworks-50-In-Development" >}}. TODO: circle back. -->
数年前、KDE チームはバージョン5.0のリリース戦略をどうしようか悩んでいました。
結果として、バージョン4.x系列と並行してリリースすることにしました。
かつての過ち{{< ext url="http://tech.slashdot.org/story/11/08/07/2128222/KDE-Frameworks-50-In-Development" >}} を繰り返さないためです。

## 手順を説明している独立した Web サイト
  
<!-- In early 2018, a Branch by abstraction was created to further drill into this concept{{< ext url="https://www.branchbyabstraction.com" >}}. -->
抽象化によるブランチの考え方を詳しく説明する Web サイト{{< ext url="https://www.branchbyabstraction.com" >}} が 2018年に作成されました。

## 経緯
 
<!-- Teams employed Branch by Abstraction many years before it got its name (Stacy Curl named it in 2007), but it is
unknown when the first implementation was. Before the adoption of BbA, teams **had to** make a branch for the 
big lengthy disruptive change, or do it with an incredible amount of choreography: "hey everyone, take a week of 
vacation now". -->
抽象化によるブランチ（BbA：Branch by Abstraction）という名前が付けられる前に採用していたチームはいると思いますが、最初に誰が実装したのかはわかりません（2007 年に Stacy Curl が命名しました）。
BbA が出来る前は、長期的に深い断絶をもたらす変更のためのブランチを作成しなければなりませんでした。
あるいは、極めて大きな調整コストを支払う必要がありました（「さあみんなこれから1週間休んでくれ」）。

<!-- With the Branch by Abstraction technique, Trunk-Based Development was less likely to be temporarily or permanently
abandoned for a multi-branch model. -->
抽象化によるブランチという手法があれば、トランクベース開発における複数のブランチモデルの採用を一時的にあるいは恒久的に止められるかもしれません。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">26 Apr 2007, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/blog/branch_by_abstraction.html">Introducing Branch by Abstraction</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">05 May 2011, ContinuousDelivery.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/">Make Large Scale Changes Incrementally with Branch By Abstraction</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">21 Jun 2013, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://www.slideshare.net/cb372/branching-strategies">Branching Strategies: Feature Branches vs Branch by Abstraction</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">14 Oct 2013, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://www.alwaysagileconsulting.com/articles/application-pattern-verify-branch-by-abstraction">Application Pattern: Verify Branch By Abstraction</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">07 Jan 2014, MartinFowler.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://martinfowler.com/bliki/BranchByAbstraction.html">BranchByAbstraction</a></td>
        </tr>
    </table>
</div> -->
- 26 Apr 2007, Blog Entry： [Introducing Branch by Abstraction](http://paulhammant.com/blog/branch_by_abstraction.html)
- 05 May 2011, ContinuousDelivery.com article： [Make Large Scale Changes Incrementally with Branch By Abstraction](https://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/)
- 21 Jun 2013, Blog Entry： [Branching Strategies: Feature Branches vs Branch by Abstraction](http://www.slideshare.net/cb372/branching-strategies)
- 14 Oct 2013, Blog entry： [Application Pattern: Verify Branch By Abstraction](http://www.alwaysagileconsulting.com/articles/application-pattern-verify-branch-by-abstraction)
- 07 Jan 2014, MartinFowler.com article： [BranchByAbstraction](https://martinfowler.com/bliki/BranchByAbstraction.html)

