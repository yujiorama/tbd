---
date: 2016-07-01T20:10:46+01:00
title: 継続的デリバリー（CD：Continuous Delivery）
weight: 71
---

<!-- Continuous Delivery is the practice expanding your Continuous Integration (CI) usage to automatically
re-deploy a proven build to a QA or UAT environment. If the bounce time for a deployment is quick enough,
then it could be that you are doing that for every commit that lands in the shared trunk. The Radiator's pipeline view would 
become: -->
継続的デリバリーとは、継続的インテグレーションを拡張するプラクティスです。
正当性の証明されたビルドを QA 環境や UAT 環境へ自動的に（繰り返し）デプロイします。
デプロイが十分に短時間で終わるなら、共有リポジトリの trunk へコミットするたびにデプロイできるかもしれません。
パイプラインのラジエータは次のようになるでしょう。

![](pipelines2.png)

<!-- The [bestselling book of the same name](/publications/index.html#continuous-delivery-july-27-2010) by Jez Humble and Dave Farley,
details the 'marching orders' for many companies, where there is whole dev-team improvement agenda. -->
Jez Humble と Dave Farley による書籍の [継続的デリバリー](/publications/index.html#continuous-delivery-july-27-2010) では、さまざまな企業における「行進速度」を、開発チーム全体の改善度合いとして詳しく分析しています。

<!-- {{< warning title="A Layer above Trunk-Based Development" >}}
Continuous Delivery is a broad multifaceted subject, that sits on top of Trunk-Based Development as a practice. This
website and this page, in particular, is not going to give it justice. Head on over to 
`ContinuousDelivery.com`{{< ext url="https://continuousdelivery.com" >}} and understand too that "lean experiments" are the part 
of CD and not so much the concern of Trunk-Based Development.
{{< /warning >}} -->
{{< warning title="トランクベース開発の上位層" >}}
継続的デリバリーにはさまざまな側面の取り組みが関係します。
トランクベース開発に基づくプラクティスなのです。
ただし、このWebサイトやこのページが唯一の正解を与えるわけではありません。
`ContinuousDelivery.com`{{< ext url="https://continuousdelivery.com" >}} や、CD を構成する「リーン実験」など、トランクベース開発以外について勉強してください。
{{< /warning >}}

## 継続的デプロイ

<!-- **An automatic push all the way into production; Maybe every commit** -->
**可能なら、全てのコミットを自動的に本番環境へ配信する**

<!-- This is an extension of 'Continuous Delivery', but the deployment is to production. Certain types of startups like
Netflix, Etsy and GitHub deploy their major application to production with each commit. Companies that have 
applications/services where  clients/customers could lose money are much less likely to *firehose into production*. -->
継続的デリバリーの延長線上にある、本番環境へのデプロイに注力した考え方です。
Netflix や Etsy や Github のようなスタートアップ企業は、主力アプリケーションに対する1つ1つのコミットを本番環境にデプロイしています。
特定のクライアントや顧客がお金を無駄にする可能性のあるアプリケーション（あるいはサービス）を開発している企業のほうが、*本番環境で問題を起こす* 可能性は低くなります。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">2010, Jez Humble's Continuous Delivery portal</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://continuousdelivery.com/">ContinuousDelivery.com</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">5 Jan 2015, TheGuardian newspaper on their CD</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://www.theguardian.com/info/developer-blog/2015/jan/05/delivering-continuous-delivery-continuously">Delivering Continuous Delivery, continuously</a></td>
        </tr>
    </table>
</div> -->

- 2010, Jez Humble's Continuous Delivery portal：[ContinuousDelivery.com](https://continuousdelivery.com/)
- 5 Jan 2015, TheGuardian newspaper on their CD：[Delivering Continuous Delivery, continuously](https://www.theguardian.com/info/developer-blog/2015/jan/05/delivering-continuous-delivery-continuously)
