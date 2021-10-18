---
date: 2016-11-02T20:08:11+01:00
title: モノリポの拡大、縮小
weight: 112
---
 
<!-- At some point with a monorepo approach to source control (especially with binary dependencies in the source tree), your checkouts
could be bigger than your local workstation's hard drive. Or even if the checkout is not too big for your hard drive, 
then it might be too much for your IDE, and you do not want to have to abandon it for Vim/Emacs. Or maybe it is not your IDE that 
chokes but rather is something about the build that's too much locally, despite command-line arguments to attempt to
pare it down for a shorter elapsed time. -->
モノリポをやっていると、特にバイナリ形式の依存ライブラリをソースコードツリーで管理するとき、チェックアウトするデータサイズが開発マシンのディスク容量より大きくなってしまう場合があります。
そこまでじゃなくても、IDEで操作するには大きすぎる場合があります。
ビルドする時間が長くなりすぎる場合もあります。

<!-- There is a way to intelligently expand or contract the checkout on your developer workstation, to alleviate all of
the above. -->
チェックアウトするデータを柔軟に調整できれば、このような問題は解消します。

## Gcheckout.sh

<!-- Google's in-house DevOps uses some simple scripting to
modify the checkout on the developer's workstation to omit the source files/packages that are not needed for the 
current intentions of the developer. This Blaze related technology is a shell command called 'gcheckout'. It can modify the mappings between the multi-gigabyte HEAD 
revision of company-wide trunk (monorepo) and developer's own workstation. Thus the source-control tools maintain the 
**smallest possible subset** of the monorepo on the developer's workstation, for them to perform their daily work. 
Google and the industry refer to the general feature as 'sparse checkout'. -->
Google の DevOps チームは、開発者が不要だと思ったファイルやパッケージを除外してチェックアウトできるスクリプトを使用していました。
Blaze から発生したツールで、シェルからは `gcheckout` として実行します。
全社規模のモノリポの数 GB もある HEAD と、開発マシン上のソースコードとの対応付けを管理できるようになっていました。
開発マシンには、モノリポから日々の作業に必要な **最小限のデータ** を、ソースコード管理ツールで取得できるようになっていました。
この機能は、Google だけでなくコミュニティでも `sparse checkout` と呼ばれるようになりました。

<!-- You can run `gcheckout` at any time to modify your sparse checkout to be bigger or smaller (or wholly different) for
different reasons. All of those are operations on your local representation of a larger trunk. -->
`gcheckout` はいつでもチェックアウトしたソースコードを増やしたり減らしたりできるようになっていました。
これらの全ての操作は、巨大な trunk に対応する開発マシン上のソースコードについて実行する操作です。

## 不自然な具体例

<!-- We detailed two intentions for directed graph build systems above, using a contrived application. Here is one more: -->
別のページで、有向グラフに基づくビルドシステムを説明するために使用した、2つの具体例を再利用します。

<!-- * I now want to change `TheORMweBothDependOn`, because a change to `MyTeamsApplication` requires me to do that. -->
* `MyTeamsApplication` 用の変更が必要なので `TheORMweBothDependOn` を変更したい

<!-- In Google, rather than feed into the backlog of the team that maintains `TheORMweBothDependOn` (which may exist as a part-time
committee rather than a team), the developer in question would make the change themselves. Perhaps they had made 
it in the same commit as the first usage of it for `MyTeamsApplication`.  In the code review cycle (Google practices
common code ownership), the approvers for the `TheORMweBothDependOn` would see all the changes together. The larger change is
all accepted or rejected (to be remediated) atomically. -->
Google のやり方なら `TheORMweBothDependOn` をメンテナンスしてるチームのバックログに追加するのではなく（チームというよりパートタイムでコミッターをやっている人がいるでしょう）、それを必要とする開発者自身が変更することになります。
もしかしたら `MyTeamsApplication` と同じ変更内容になるかもしれません。
コードレビューの中で（Google ではコードの共同所有を実践しています）、レビューアは `TheORMweBothDependOn` に関する全ての変更を確認するでしょう。
巨大な変更は全部を承認するか、拒否するか（さらに修正するか）しかありません。分離できないからです。

<!-- So our developer was working on `MyTeamsApplication`, which depended on `TheORMweBothDependOn` (which probably transitively
depended on other things). Now that developer is going to change `TheORMweBothDependOn` and that impacts `TheirApplication` 
too. The Blaze related checkout-modifying technology 'gcheckout' performs an expansion to bring in `TheirApplication` to the
developer's checkout. From that moment on, the developer doing update/pull/sync will bring down minute by minute
changes to those three modules.  For free, the build expands to make sure that the `TheORMweBothDependOn` changes do not 
break either of `MyTeamsApplication` or `TheirApplication`. -->
私たちの開発者が作業している `MyTeamsApplication` は、`TheORMweBothDependOn` に依存しています（推移的依存関係なのかもしれませんし、そうではないかもしれません）。
`TheORMweBothDependOn` を変更することにしたのですが、`TheirApplication` にも影響することが分かっています。
Blaze のチェックアウト加工ツール `gcheckout` を使えば開発マシンに `TheirApplication` のソースコードをチェックアウトできます。
その時点から開発者は3種類のモジュールを取得、更新、同期できるようになります。
`MyTeamsApplication` と `TheirApplication` を壊すことなく、`TheORMweBothDependOn` を変更できるようにビルドを拡張できるのです。

### ディレクトリ構造と作業コピー

<!-- If I ran `gcheckout` with MyTeamsApplication+TheirApplication as the parameter I would get working copy that looked like: -->
`MyTeamsApplication` と `TheirApplication` を引数に `gcheckout` を実行すると、作業コピーは次のようになります。

```txt
root/
  java/
    BUILD
    com/
      BUILD
      google/
        BUILD
        myteamsapplication/
          BUILD
          MyTeamsApplication.java  // and hundreds of other packages and source files
        theirapplication/
          BUILD
          TheirApplication.java  // and hundreds of other packages and source files
        theormwebothdependon/
          BUILD
          TheORMweBothDependOn.java  // and hundreds of other packages and source files
  java_test/
    BUILD
    com/
      BUILD
      google/
        BUILD
        myteamsapplication/
          BUILD
          MyTeamsApplicationTest.java  // and hundreds of other packages and source files
        theirapplication/
          BUILD
          TheirApplicationTest.java  // and hundreds of other packages and source files
        theormwebothdependon/
          BUILD
          TheORMweBothDependOnTest.java  // and hundreds of other packages and source files
```

<!-- If I ran `gcheckout` with MyTeamsApplication as the parameter I would get working copy that looked like: -->
`MyTeamsApplication` だけを引数に `gcheckout` を実行した場合、作業コピーは次のようになります。

```txt
root/
  java/
    BUILD
    com/
      BUILD
      google/
        BUILD
        myteamsapplication/
          BUILD
          MyTeamsApplication.java  // and hundreds of other packages and source files
        theormwebothdependon/
          BUILD
          TheORMweBothDependOn.java  // and hundreds of other packages and source files
  java_test/
    BUILD
    com/
      BUILD
      google/
        BUILD
        myteamsapplication/
          BUILD
          MyTeamsApplicationTest.java  // and hundreds of other packages and source files
        theormwebothdependon/
          BUILD
          TheORMweBothDependOnTest.java  // and hundreds of other packages and source files
```

<!-- If I ran `gcheckout` with TheORMweBothDependOn as the parameter I would get working copy that looked like: -->
`TheORMweBothDependOn` を引数に `gcheckout` を実行すると、作業コピーは次のようになります。

```txt
root/
  java/
    BUILD
    com/
      BUILD
      google/
        BUILD
        theormwebothdependon/
          BUILD
          TheORMweBothDependOn.java  // and hundreds of other packages and source files
  java_test/
    BUILD
    com/
      BUILD
      google/
        BUILD
        theormwebothdependon/
          BUILD
          TheORMweBothDependOnTest.java  // and hundreds of other packages and source files
```

<!-- You can keep rerunning the `gcheckout` to expand or contract your working copy to meet your current goals. -->
`gcheckout` を実行するたびに、必要に応じて作業コピーを拡大したり縮小したりできるのです。

## 不自然な具体例（その2）

<!-- We used 'change the wheel on a car', on the [Branch By Abstraction](/branch-by-abstraction/) page for its contrived
example. It will serve us again here. The wheel is what we want to change. The other team using 'Wheel(s)' is making a 
Segway thing (two wheels and self-balancing via high-torque and very responsive motors). Here's the procedure: -->
[抽象化によるブランチ](/branch-by-abstraction/) を説明するための題材「車のホイール交換」を再び取り上げることにします。
ホイールとは私たちが変更したいモノを表しています。
あるチームはセグウェイ的な何か（高トルクで反応性のよいモーターで、自律的にバランスを取りながら2本のホイールを駆動する乗り物）を開発しているものとします。

![](car_segway.png)  
 
<!-- The starting position is two teams working separately, using 'Wheel' (4 for cars, 2 for Segways). Without any commits
happening the engineer changing 'Wheel' for everyone, runs gcheckout (or its equivalent) to modify the source in the 
IDE to the union of Car and Segway (and in-house dependencies). That is marked as step 0. Let us say the
change is quick/easy this time (not requiring Branch By Abstraction) step 1 shows the single commit that changes
the wheel implementation for everyone.  After the commit/push, running again shows the application focused team checkout - either 
'Car' or 'Segway'. -->
 初期位置では2つのチームの作業は独立しています（4輪の車を開発しているチームと、2輪のセグウェイを開発しているチーム）。
 誰かが全員の使用する「ホイール」を変更しなければ、車とセグウェイ（および自社開発の依存対象）のソースコードの変更を `gcheckout` で取得できます。
 つまり手順0です。
 この段階では抽象化によるブランチをしなくても、すぐに、簡単に変更できます。
 手順1は、1つのコミットでホイールの実装を変更している様子を表しています。
 コミットをプッシュしてから `gcheckout` を実行すると、車あるいはセグウェイがどのように変化するのか分かると思います。

## Git の疎なチェックアウト（スパースチェックアウト）

<!-- Git has a 'sparse checkout' capability, which exactly facilitates this sort of thing. Subversion and Mercurial do too.
Perforce has a 'client spec' capability that is more or less the same. A team wanting to have their own gcheckout equivalent
would have some scripting around sparse checkouts (or equivalent).  -->
Git の「疎なチェックアウト（sparse checkout）」は、このセクションで検討している機能そのものです。
Subversion や Mercurial にも同じような機能があります。
Perforce の「クライアント仕様」も同じような機能を提供します。
「疎なチェックアウト」機能を使えば、自分たちで `gcheckout` 相当のスクリプトを開発できます。

### 今風の Git の使い方

<!-- If you're willing to go a 'split history' maneuver on your monorepo once or twice a year, Git can do the expandable and
contractible monorepo setup today.  -->
モノリポをやっていると、1年に何回かは履歴を分割したい気持ちになるでしょう。
Git ならモノリポを柔軟に拡大、縮小できます。

## Perforce の「クライアント仕様」について

<!-- Perforce has a 'client spec' (alternatively 'view') that is accessed via the client command or UI. Amongst other things, it
allows a checkout to be a subset of the directories/files available within the branch. A list of globbed includes and 
excludes is the format. You would script this (as Google did until 2012) to have a directed graph driven 
expandable/contractible checkout. -->
Perforce の「クライアント仕様」（ビューと呼ばれています）はクライアントのコマンド（あるいはUI）です。
ブランチの一部のディレクトリやファイルを選択的にチェックアウトできます。
取得対象と除外対象をファイルグロブ形式で指定します。
Google が 2012 年までやっていたように、有向グラフに基づく拡大・縮小チェックアウトを実現するスクリプトを作成できるでしょう。

## PlasticSCM's cloaked.conf

As Perforce, but via 'cloaked.conf' file.

## Subversion's sparse-checkouts

Subversion has a 'sparse checkout' capability. You do a series of checkout operations at various directory levels in order
to create the mapping, so is less atomic or centrally configured than the others.  They have made also made a 'viewspec' script
{{< ext url="http://svn.apache.org/repos/asf/subversion/trunk/tools/client-side/svn-viewspec.py" >}}
to allow the reshaping of the working copy to happen in a more declarative way.
