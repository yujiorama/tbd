---
date: 2016-07-08T20:08:11+01:00
title: アプリケーションの絞殺
weight: 78
---

<!-- Strangulation is a mechanism by which a very large disruptive change is made in an application or service that, does not
disrupt its ability to go live, even while partially complete.  Martin Fowler named this practice (see references below)
after the strangler vines that creep up existing trees, in order to steal sunlight at canopy level of a jungle. -->
アプリケーションを絞殺するというのは、アプリケーション（あるいはサービス）を停止したり中断させることなく、部分的に完了した機能を展開しつつ、大規模で長期的な破壊的変更を行う取り組みのことです。
このプラクティスを命名したのは Martin Fowler です。
熱帯雨林で、木に巻き付くように成長した蔦が林冠に降り注ぐ日の光を全て吸収してしまう様子を表しています。

<!-- The trick is to have a mechanism to route invocations of logic between the old and new solutions for the same. Say
you are an Airline, and you had written your first online purchasing experience in Perl. You're now wanting to 
do 'Elixir' and its web framework 'Phoenix'.   -->
コツは、同じ経路制御機能で、元のアプリケーションと新しいアプリケーションの呼び出しを制御することです。
Perl で作成した航空券販売システムを、Elixir の Phoenix フレームワークで作り直したい場合を考えてみましょう。

<!-- Strangulation is where you would use the Apache server that you doubtless had
fronting Perl, to **conditionally** route HTTP requests to Erlang/Elixir/Phoenix. Say your first completed milestone
was 'Loyalty Account View/Edit' you would route based on the URLs the browser was seeking pages for. Obviously 
agreeing on URLs (and cookies) is key for the old Perl and new Elixr app. So is deployment in lockstep. -->
Perl の手前に配置したはずの Apache HTTP Server でストラングラーすることになるでしょう。
**何らかの条件に応じて**、HTTP リクエストを Erlang/Elixir/Phoenix のアプリケーションに転送するのです。
最初のマイルストーンを「優先顧客情報の閲覧・更新」機能にするとして、URLに基づいてリクエストを転送します。
元の Perl でも新しい Elixr でも、URL（あるいは Cookie）が画面を識別する重要な要素であることは間違いありません。

<!-- At some point in the strangulation, you might put Elixir in front Apache/Perl and have traffic drop through to it
instead. That is the residual situation before you delete the last lines of code of Perl and snip that delegation
when the strangulation is complete. -->
ストラングラーするときは、Perl で実装した機能を Elixir で代理する場合があると思います。
Perl の実装を削除する前にそうするべきですし、ストラングラーが完了するまで代理し続けることになるでしょう。

<!-- This relates a little to [Branch by Abstraction](/branch-by-abstraction/). Strangulation is a strategy for
incompatible languages (they are not in the same process), whereas Branch by Abstraction is where the 'from' and 'to' 
languages are the same (say Java -> Java), or compatible (Java -> Scala). -->
この仕組みは [抽象化によるブランチ](/branch-by-abstraction/) とよく似ており、互換性のないプログラミング言語（もちろん独立したプロセスです）へ移行するときも使用できる戦略です。
抽象化によるブランチは、同じプログラミング言語か（Java から Java）、互換性のないプログラミング言語（Java から Scala）への移行に役立ちます。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">29 Jun 2004, MartinFowler.com article</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.martinfowler.com/bliki/StranglerApplication.html">Application Strangulation</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">17 Jan 2006, Blog Entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://scottmark.wordpress.com/2006/01/17/great-architects-are-also-stranglers/">Great Architects Are Also Stranglers</a></td>
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
</div> -->
- 29 Jun 2004, MartinFowler.com article：[Application Strangulation](https://www.martinfowler.com/bliki/StranglerApplication.html)
- 17 Jan 2006, Blog Entry：[Great Architects Are Also Stranglers](https://scottmark.wordpress.com/2006/01/17/great-architects-are-also-stranglers/)
- 14 Jul 2013, Blog Entry：[Legacy Application Strangulation: Case Studies](http://paulhammant.com/2013/07/14/legacy-application-strangulation-case-studies/)
