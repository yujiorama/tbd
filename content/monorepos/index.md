---
date: 2016-11-01T20:08:11+01:00
title: モノリポ
weight: 111
---

<!-- A Monorepo is a specific Trunk-Based Development implementation where the organization in
question puts its source for all applications/services/libraries/frameworks into one trunk and forces developers
to commit together in that trunk - atomically. -->
モノリポはトランクベース開発に実装方法の1つです。
組織のアプリケーション、サービス、ライブラリ、フレームワークの全てのソースコードを、1つのリポジトリの trunk で管理し、全ての開発者は trunk へ分離不可能なコミットをプッシュします。

<!-- Google has the most famous monorepo and they do the above AND force teams to **share code at source level instead of
linking in previously built binaries**. Specifically, they have no version numbers for their own dependencies, just an
implicit 'HEAD'. Indeed, because of lock step upgrades for all, releases from their monorepos mean 'HEAD' **is** their
effective version number. Third-party libraries (like JUnit) will be checked into the repo with a specific version number
(like 4.11), and all teams will use that version if they use it at all. -->
モノリポを採用している会社で最も有名なのは Google です。
1つの trunk で全てのソースコードを管理するだけではなく、**依存ライブラリをリンクではなくソースコードレベルで共有する**ようにしています。
依存ライブラリを管理するバージョン番号は無くて、暗黙的に `HEAD` を参照するようになっています。
全体として足並みを揃えてアップグレードするので、モノリポからリリースするときの実質的なバージョン番号は `HEAD` になるからです。
JUnitのようなサードパーティライブラリも、特定のバージョン番号（例えば4.11）を指定してリポジトリに格納し、組織全員がそのバージョンを使用することになります。

<!-- The deployment and/or release cadences for each application/service/library/frameworks will probably be different
as will the team's structures, methodologies, priorities, story backlogs etc. -->
アプリケーションによってデプロイやリリースのケイデンスは異なります。
チームの構造も、方法論も、優先順位も、バックログも違うでしょう。

<!-- The name 'monorepo' is a newer name for a previously unnamed practice that is more than a decade old. -->
「モノリポ」とは、10年以上前から実践されていた無名のプラクティスに付けられた新しい名前なのです。

<!-- Monorepo implementations deliver a couple of principal goals: -->
モノリポが実現する主な目標は次のとおりです。

<!-- * Acquire as many third-party and in-house dependencies as possible for a build from the **same** source-control repository/branch, and in the
same update/pull/sync operation.
* Keep all teams in agreement on the versions of third-party and in-house dependencies via lock-step upgrades. -->
* 開発元を問わずできるだけ多くの依存ライブラリを、アプリケーションと **同一のリポジトリ** で管理できるようにする
* 全ての開発チームが、依存ライブラリを足並みを揃えてバージョンアップする方針に合意する

<!-- And some secondary goals: -->
次善の目標は次のとおりです。

<!-- * Allow changes to multiple modules via **atomic commits**.
* Allow the extraction of new common dependencies (from existing code) to be achieved in atomic commits.
* Force all developers to focus on the HEAD revisions of files in the trunk
* Allow bisecting towards the root cause of a prod bug to be effected on multiple (potentially dissimilar) modules at the same time -->
* 複数のモジュールに対する **分離不可能なコミット** をできるようにする
* 既存のコードから新たな共通依存ライブラリを抽出できるようにする（分離不可能なコミットで）
* 全ての開発者が常に `HEAD` を追跡するようにする
* 複数のモジュールに対する同時コミットについて、bisect でバグを混入したコミットを突き止められるようにする

<!-- Google and Facebook are the most famous organizations that rest development on a single company-wide trunk, that
fits the monorepo design. Netflix and Uber (iOS application) disclosed in 2017 that they do too.  -->
Google や Facebook は、会社全体で1つの trunk を対象に開発している会社の中で最も有名な会社です。
Netflix や Uber もそういう風に開発していることを発表したのは 2017 年でした。

<!-- {{< warning title="Risk of chaotic directory layout" >}}
Google's co-mingled applications and services site within highly structured and uniform source trees. A Java
developer from one project team instantly recognizes the directory structure for another team's application
or service. That goes across languages too. The design for the directory layout needs to be enforced globally. You can
see that in the way that Buck and Bazel layout trees for production and test code. If you
cannot overhaul the directory structure of your entire repository, you should not entertain a monorepo.
{{< /warning >}} -->
{{< warning title="めちゃめちゃなディレクトリ構造のリスク" >}}
Googleのいろいろ混ざってるアプリケーションおよびサービスサイトは、高度に組織化された統一ソースツリーに収まっています。
Java 開発者ならすぐに他のチームのアプリケーションを見つけられるようになっています。
他のプログラミング言語に慣れている人にもそうなって欲しい。
ディレクトリ構造の設計は全体性を高めるものでなければなりません。
Buck および Bazel のプロダクトコードとテストコードに関するレイアウトツリーを思い浮かべてください。
リポジトリ全体のディレクトリ構造を点検、修正できないなら、モノリポをやるべきではありません。
{{< /warning >}}

### 足並みを揃えてデプロイする

<!-- OK, so it is really important to note in a monorepo, that you don't have to do a lock-step deployment of a buildable/releaseable thing just because a dependency was upgraded. What is certain is the next deployment of that application/service will
contain the new dependency. "Next" is still a concern of the development team in
question. Monorepos only say "what" will be released, not "when".  -->
依存ライブラリが更新されたからというだけで、ビルドできるものやリリースできるもの全てを足並み揃えてデプロイするのは止めましょう。
次にデプロイするアプリケーション（あるいはサービス）は、間違いなく新しい依存ライブラリを使うようになっているからです。
「次に（Next）」という言葉にも気を付けてください。
モノリポでは「いつ」より、「何を」リリースするかが重要なのです。

## サードパーティライブラリ

<!-- With the monorepo model, there is a strong desire to have third-party binaries in source-control too.
You might think that it would be unmanageable for reasons of size. In terms of commit history, Perforce and Subversion do not
mind a terabyte of history of binary files (or more), and Git performed much better when Git-LFS was created. You
could still feel that the HEAD revision of thousands of fine-grained dependencies is too much for a workstation, but
that can be managed via an [expanding and contracting monorepo](/expanding-contracting-monorepos/). -->
モノリポではバイナリ形式のサードパーティライブラリをソースコード管理システムで一緒に管理することが強く推奨されています。
ストレージサイズが不安になると思います。
Perforce や Subversion ならバイナリファイルのコミット履歴がどれだけ大きくなっても問題ありませんし、Git なら上手く Git-LFS が活用できるなら問題ありません。
[モノリポの拡大と縮小](/expanding-contracting-monorepos/) のテクニックを活用しましょう。

<!-- Note:  Python, Java, C++ and other SDKs are installed the regular way on the developer workstation, and not acquired
from the source-control repository/branch. -->
注意：Python、Java、C++ などのSDKは開発マシンにインストールするもので、ソースコード管理システムから取得するものではありません。

## 自社開発ライブラリ

<!-- It could be that your application team depends on something that is made by colleagues from a different team. An
example could be an Object Relational Mapping (ORM) library. For monorepo teams there is a strong wish to depend on
the source of that ORM technology and not a binary. There are multiple reasons for that, but the principal one is that
source control update/pull/sync is the most efficient way for you to keep up with the HEAD of a library on a minute
by minute basis. Thus `MyTeamsApplication` and `TheORMweDepOn` should be in your source tree in your IDE at the same time.
Similarly, another team that depends on `TheORMweDepOn` should have it and `TheirApplication` checked out at the same
time. -->
モノリポで自社開発ライブラリを依存ライブラリとして使用するときは、バイナリファイルではなくソースコードとして依存するほうが推奨されています。
複数の理由はありますが、重要なのはソースコードを最新化するだけで `HEAD` （最新バージョン）に追従できることです。

## 有向グラフに基づくビルドシステム

<!-- To facilitate monorepos, it is important to have a build system that can omit otherwise buildable things/steps that are not
required for the individual developer's **current** build intention. -->
モノリポには、不必要な部分を対象から除外できるビルドシステムが必要です。

<!-- The general directory structure for directed graph build systems is like so: -->
有向グラフに基づくビルドシステムにおける一般的なディレクトリ構造は次のとおりです。

```
root/
  prod_code/
    build_file.xml
    (source files)
    a_directory/
      build_file
      (source files)
      another_directory/
        build_file.xml
        (source files)
    yet_another_directory/
      build_file.xml
      (source files)
  test_code/
    build_file.xml
    (source files)
    a_directory/
      build_file
      (source files)
      another_directory/
        build_file.xml
        (source files)
    yet_another_directory/
      build_file.xml
      (source files)
```

<!-- Obviously, YAML, JSON, TOML or custom grammars are alternatives to XML, for build files. -->
もちろん、ビルド定義ファイルは XML 形式の代わりに YAML 形式や JSON 形式や TOML 形式にできるでしょう。

### 不自然な具体例

<!-- Two examples: -->
2つ説明します。

<!-- * I want to run impacted tests locally, relating to the hair-color field I just added to the person page of `MyTeamsApplication`
* I want to run bring up `MyTeamsApplication` locally, so I can play with the hair-color field I just added to the person page -->
* `MyTeamsApplication` の個人ページに「髪の色」フィールドを追加したので、手元で徹底的なテストを実行したい
* `MyTeamsApplication` の個人ページに「髪の色」フィールドを追加したので、手元で実行して動かしてみたい

<!-- Not only do you want to omit unnecessary directories/files from your build's activities, you probably also want to omit
them from your IDE. -->
不要なディレクトリ（ファイル）をビルドの対象から除外するだけでなく、IDE からも除外してみましょう。

### Buck（Facebook）と Bazel（Google）

<!-- Google has Blaze internally. Ex-Googlers at Facebook (with newfound friends) missed that, wrote
Buck{{< ext url="https://buckbuild.com" >}} and then
open-sourced it. Google then open-sourced a cut-down Blaze as Bazel{{< ext url="https://bazel.build" >}}.
These are the two (three including Blaze) directed graph build systems that allow a large tree of sources to be speedily
subset in a compile/test/make-a-binary way. -->
Google は以前から社内で Blaze というビルドツールを使用していました。
Google から Facebook に転職した人はそれが残念だったので、Buck{{< ext url="https://buckbuild.com" >}} を開発し、OSS として公開しました。
そうしたら、Google も Blaze を Bazel{{< ext url="https://bazel.build" >}} という名前の OSS として公開しました。
大規模なソースコードツリーを対象に、高速にコンパイル、テスト、ビルドを実行できる、有向グラフに基づくビルドシステムが2つも利用できるようになりました。

<!-- The omitting of unnecessary compile/test actions achieved by Buck and Bazel works equally well on developer workstations
and in the CI infrastructure. -->
Buck や Bazel が不要なコンパイルやテストを避ける処理は、開発マシンでも CI 基盤でも同じように動作します。

<!-- There is also the ability to depend on recently compiled object code of colleagues. The recently compiled object code for
provable permutations of sources/dependencies, that is. And that code plucked from the ether (think of a LRU cache available to all
machines in the TCP/IP subnet). That is in place to shorten compile times for prod and test code. -->
他の人がコンパイルしたオブジェクトを依存対象にする機能もあります。
同じサブネットに属する全ての開発マシンがアクセスできる LRU キャッシュを介して、ソースコードや依存ライブラリの組み合わせに応じて無数に存在するコンパイルしたオブジェクトを受信します。
コンパイル時間が短縮できるのはそういう理由があります。

## 再帰的なビルドシステム

<!-- Java's Apache-Maven is the most widely used example. It's predecessor, Ant, is another. Maven more than Ant, pulls
third-party binaries from 'binary repositories', caching them locally. Maven also traverses its tree in a strict
depth first (then breadth) manner. Most recursive build systems can be configured to pull third-party dependencies
from a relative directory in the monorepo. A binary dependency cache outside of the VCS controlled working copy,
is more normal. -->
Apache Maven は最も広く使われている Java のビルドシステムです。
先行者としての Ant もありますが、それはそれとして。
Maven はバイナリ形式のサードパーティライブラリをアーティファクトリポジトリから取得し、ローカルのキャッシュに保存します。
Maven はソースコードツリーを深さ優先で探索します。
ほとんどの再帰的なビルドシステムは、サードパーティ依存ライブラリを、モノリポのディレクトリ階層における相対パスで指定できます。
しかし、バイナリ形式の依存ライブラリは、バージョン管理システムの管理外のキャッシュを参照する場合のほうが多いでしょう。

<!-- The general directory structure for recursive build systems is like so: -->
再帰的なビルドシステムにおける一般的なディレクトリ構造は次のとおりです。

```
root/
  build_file.xml
  module_one/
    build_file.xml
    src/
      # prod source directory tree
      # test source directory tree
    module_two/
      build_file.xml
      src/
        # prod source directory tree
        # test source directory tree
  module_three/
    build_file.xml
    src/
      # prod source directory tree
      # test source directory tree
  src/
    # prod source directory tree
    # test source directory tree
```

<!-- Again, YAML, JSON, TOML and custom grammars are alternatives to XML for build files. -->
もちろん、ビルド定義ファイルは XML 形式の代わりに YAML 形式や JSON 形式や TOML 形式にできるでしょう。

<!-- Recursive build systems mostly have the ability to choose a type of build. For example 'mvn test' to just run tests,
and not make a binary for distribution. -->
再帰的なビルドシステムでは実行するアクションの種類を指定できます。
例えば `mvn test` と実行すればテストを実行するだけで、バイナリ形式の配布物は作成しません。

## 菱形の依存関係

<!-- What happens when two apps need a different version of a dependency? -->
2種類のアプリケーションが、同じライブラリの別のバージョンに依存しているとどうなるでしょうか。

<!-- For in-house dependencies, where the source is in the same monorepo, then you will not have this situation, as the
team that first wanted the increased functionality, performed it for all teams, keeping everyone at HEAD revision
of it. The concept of version number disappears in this model. -->
自社開発ライブラリなら同じリポジトリでソースコードを管理しているのでそういう状況にはなりません。
最初に機能追加したいと思ったチームが他のチームを説得し、全員が `HEAD` を追跡する状態を保つようにしましょう。
そうすれば、バージョン番号について考えなくてもよくなります。

### サードパーティライブラリ

<!-- For third-party dependencies, the same rule applies, everyone upgrades in lock-step. Problems can ensue, of course,
if there are real reasons for team B to not upgrade and team A was insistent. Broken backward compatibility is
one problem. -->
サードパーティの依存ライブラリも同じルールで扱います。
全員が足並みを揃えるのです。
そうすると、チームAは新しくしたいけど、チームBは元のままにしたい、という状態が必ず発生します。
これは、後方互換性がないときに問題になります。

<!-- In 2007, Google tried to upgrade their JUnit from 3.8.x to 4.x and struggled as there was a subtle
backward incompatibility in a small percentage of their usages of it. The change-set became very large, and struggled
to keep up with the rate developers were adding tests. -->
2007 年のこと、Google は JUnit を 3.8.x から 4.x へアップグレードするとき、後方互換性のない使い方をしている部分が少しあったせいで、とても苦労したそうです。
変更セットはとても巨大になるし、開発者がテストを作成する早さを維持するのが大変だったとのこと。

<!-- Because you are doing lock-step upgrades, you only secondarily note the version of the third-party
dependencies, as you check them into source control without version numbers in the filename.  I.e. JUnit goes in as
`third_party/java_testing/junit.jar`. -->
足並みを揃えてアップグレードしているなら、サードパーティライブラリのバージョン番号について考えるのは必要になってからでいいでしょう。
ソースコード管理システムにはバージョン番号を含まないファイル名で格納するからです。
例えば、JUnit は `third_party/java_testing/junit.jar` のようになるでしょう。

## 価値観の衝突

<!-- Above we contrasted **directed graph** and **recursive** build systems. The former are naturally compatible
with expandable/contractible checkout technologies. The latter not necessarily so. -->
このセクションでは **有向グラフに基づくビルドシステム** と **再帰的なビルドシステム** を比較してきました。
前者は本質的に拡大および縮小の可能なチェックアウト技術と互換性がありますが、後者はそうではありません。

### Maven

<!-- Recursive build systems like maven, have a forward declaration of modules that should be built, like so: -->
再帰的なビルドシステムで、あらかじめ必要なモジュールを宣言するようになっています。

```xml
<modules>
  <module>moduleone</module>
  <module>moduletwo</module>
</modules>
```

<!-- Presently, though, these build technologies do not have the ability to follow
a changeable checkout that the likes of gcheckout can control. -->
Maven は gcheckout のようにチェックアウト対象を柔軟に制御できません。

<!-- Directories `moduleone` and `moduletwo` have to exist in order for the build to work. The idea of expandable/contractible
monorepos, is that trees of buildable things are **calculated or computed** not **explicitly declared**.
In order to deliver that, you would need a feature to be added Maven like so: -->
`moduleone` および `moduletwo` というディレクトリはビルドを実行する前に存在しなければなりません。
しかし、拡大・縮小可能なモノリポにおけるビルドツリーは **算出および計算する** もので、**宣言する** ものではないからです。
もし Maven で対応するなら、次のような宣言ができるようにしなければなりません。

```xml
<modules>
  <calculate/> <!--or--> <search/>
</modules>
```

<!-- Or you could "hack it" and rewrite your pom.xml files after every expansion or
contraction{{< ext url="http://paulhammant.com/2017/01/27/maven-in-a-google-style-monorepo/" >}}. -->
あるいは、ディレクトリを拡大および縮小してから pom.xml を加工する方法もあるでしょう{{< ext url="http://paulhammant.com/2017/01/27/maven-in-a-google-style-monorepo/" >}}。

## モノリポじゃなくてマルチリポに変更するとしたらどうするか

<!-- In this case, the repository separation should be **no more fine grained** than the applications and services which have separate deployment cadences. -->
リポジトリを分離する単位は、独立したケイデンスでデプロイできるアプリケーション（あるいはサービス）より **小さくするべきではありません** 。

<!-- Traditionally, when using microservices the result is exactly that case: a deployable microservice in its own repository. There is no reason why hundreds of microservices could not be in the same monorepo, but one repo per microservice is conventional with the microservices community. -->
いわゆるマイクロサービスアーキテクチャにする場合が該当すると思います。
それぞれのマイクロサービスは別々にデプロイできるようにしなければならないからです。
100以上のマイクロサービスを同じモノリポで管理してもいいのですが、マイクロサービス関連のコミュニティでは別々にするのが常識になっています。

<!-- That said, Googlers have made Android Repo{{< ext url="https://source.android.com/setup/develop/repo" >}} to deliver the best of both worlds. This technology works with Gerrit for code review. And there's a fork of that called Git-Repo{{< ext url="https://github.com/wavecomp/git-repo" >}}, that has additional Git workflow features. -->
Googlers は両方に対応できるように Android のリポジトリ{{< ext url="https://source.android.com/setup/develop/repo" >}} を構成しました。
Gerrit のコードレビューと協調するようになっています。
また、フォークのリポジトリ Git-Repo {{< ext url="https://github.com/wavecomp/git-repo" >}} は独自の Git ワークフローに対応しています。

<!-- Lastly, Microsoft in their movement away from legacy VCS choices and branching models, have made VFS For Git{{< ext url="https://github.com/microsoft/VFSForGit" >}} which purports to solve many of the same scale problems of Git. -->
Microsoft では、昔から使っているバージョン管理ツールとブランチモデルから移行する機運が高まっています。
Git を大規模に拡大したときの問題を解決するため、VFS For Git{{< ext url="https://github.com/microsoft/VFSForGit" >}} を開発しています。

# References elsewhere

<!-- <a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

<div>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">09 Apr 2013, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/04/09/scaling-trunk-based-development/">Scaling Trunk-Based Development</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">06 May 2013, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2013/05/06/googles-scaled-trunk-based-development/">Google's Scaled Trunk-Based Development</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">06 Jan 2014, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2014/01/06/googlers-subset-their-trunk/">Googlers Subset their Trunk</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">08 Jan 2014, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2014/01/08/googles-vs-facebooks-trunk-based-development/">Google's vs Facebook's Trunk-Based Development</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">10 Apr 2014, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2014/04/10/cd-the-price-of-admission/">Continuous Delivery: The price of admission..</a></td>
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
            <td style="padding: 2px" valign="top">18 May 2015, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://danluu.com/monorepo/">Advantages of monolithic version control</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">20 May 2015, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2015/05/20/turning-bazel-back-into-blaze-for-monorepo-nirvana/">Turning Bazel back into Blaze for monorepo nirvana</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">27 Jan 2017, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="http://paulhammant.com/2017/01/27/maven-in-a-google-style-monorepo/">Maven In A Google Style Monorepo</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">6 Mar 2017, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://eng.uber.com/ios-monorepo/">Faster Together: Uber Engineering’s iOS Monorepo</a></td>
        </tr>
    </table>
    <table style="border: 0; box-shadow: none">
        <tr>
            <td style="padding: 2px" valign="top">27 Apr 2017, Blog entry</td>
        </tr>
        <tr>
            <td style="border-top: 0px; padding: 2px" valign="top"><a href="https://medium.com/netflix-techblog/towards-true-continuous-integration-distributed-repositories-and-dependencies-2a2e3108c051">Towards true continuous integration: distributed repositories and dependencies</a></td>
        </tr>
    </table>
</div> -->

- 09 Apr 2013, Blog entry: [Scaling Trunk-Based Development](http://paulhammant.com/2013/04/09/scaling-trunk-based-development/)
- 06 May 2013, Blog entry: [Google's Scaled Trunk-Based Development](http://paulhammant.com/2013/05/06/googles-scaled-trunk-based-development/)
- 06 Jan 2014, Blog entry: [Googlers Subset their Trunk](http://paulhammant.com/2014/01/06/googlers-subset-their-trunk/)
- 08 Jan 2014, Blog entry: [Google's vs Facebook's Trunk-Based Development](http://paulhammant.com/2014/01/08/googles-vs-facebooks-trunk-based-development/)
- 10 Apr 2014, Blog entry: [Continuous Delivery: The price of admission..](http://paulhammant.com/2014/04/10/cd-the-price-of-admission/)
- 10 Oct 2014, Conference Talk: [Trunk-Based Development in the Enterprise - Its Relevance and Economics](https://www.perforce.com/merge/2014-sessions/trunk-based-development-enterprise-its-relevance-economics)
- 18 May 2015, Blog entry: [Advantages of monolithic version control](http://danluu.com/monorepo/)
- 20 May 2015, Blog entry: [Turning Bazel back into Blaze for monorepo nirvana](http://paulhammant.com/2015/05/20/turning-bazel-back-into-blaze-for-monorepo-nirvana/)
- 27 Jan 2017, Blog entry: [Maven In A Google Style Monorepo](http://paulhammant.com/2017/01/27/maven-in-a-google-style-monorepo/)
- 6 Mar 2017, Blog entry: [Faster Together: Uber Engineering’s iOS Monorepo](https://eng.uber.com/ios-monorepo/)
- 27 Apr 2017, Blog entry: [Towards true continuous integration: distributed repositories and dependencies](https://medium.com/netflix-techblog/towards-true-continuous-integration-distributed-repositories-and-dependencies-2a2e3108c051)
