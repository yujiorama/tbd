---
date: 2016-08-01T09:42:02+05:00
title: 一般的な慣習
weight: 81
---

## コードフリーズしない

<!-- Developers living in a Trunk-Based Development reality, mostly do not experience variance in their days or weeks on the
trunk. In particular, there is no "we're close to a release so let's freeze code", and generally there is no indication 
of a slowdown in proximity to a release.  Sure, a couple of developers out of a team might be assigned to bug-fixing closer
to the release but everyone else is going to work at full speed.  -->
トランクベース開発をしていると、数週間どころか数日も trunk から離れることは滅多にありません。
「そろそろリリースするからコードフリーズしよう」みたいな意見が出てくることはなく、たとえリリースが近づいてもそれによるスローダウンが生じることはありません。
リリースが近づくと何人かがバグ修正に専念するようになりますが、他の人は元の開発速度を保ちます。

<!-- Generally speaking, the trunk is a place to firehose commits into, and the habits of the developers are such that
everything is ready to go live. If a team is doing 12 releases a year, then a release branch that is cut on the just in time 
basis and is the one that is observed to be 'frozen' because of the absence of developers. Refer [branch for release](/branch-for-release/). -->
一般的に trunk は緊急対応をコミットするところであって、常にリリースできる状態にしておくのがよい作法だと言われています。
チームが1年に12回リリースするなら、リリースブランチはその時になってから作ればよいし、開発者は誰もコミットしないので「コードフリーズ」したのと同じことになるでしょう。
詳しくは [リリースブランチ](/branch-for-release/) を参照してください。

### 特別な日なんてない

<!-- Ignoring meetings, developers commit/push at the same rate and to the same place regardless of the day of the week or
the week of the month. This is a reinforcement of the "No Code Freeze" rule above. Sure a small subset of the team
may focus on an imminent release (bug fixes in the trunk, cherry-picked to the release branch), but the majority
should be focusing on 'business as usual' implementation of functionality for a future release. -->
打ち合わせを無視すれば、何月何日何曜日だろうと、開発者は同じペースでコミット（プッシュ）し続けられるでしょう。
「コードフリーズしない」ルールを促進する効果があります。
チームの一部は差し迫ったリリースの作業（trunk でバグ修正して、リリースブランチに cherri-pick する）をすることになりますが、残りのメンバーは将来のリリースに向けて「普段通り」に仕事を進めます。

## レビューは素早く

<!-- Teams doing Trunk-Based Development know that their commits/pushes will be scrutinized by others, as soon
as they have landed on the shared trunk. They are keen on bringing that forward, not delaying it, so they may prefer to 
pair-program on code changes. Or they may ask colleagues for a code review at the time the change is submitted to be 
merged into the trunk. -->
トランクベース開発では、trunk にプッシュした自分のコミットを他のメンバーが詳しく調べることになります。
後退させることではなく、前進させることに集中するため、ペアプログラミングを好みます。
コミットを trunk にマージしてから同僚にコードレビューを依頼する場合もあります。

## HEAD を追跡する

<!-- Trunk-Based Development teams update/pull/sync from the shared trunk often. Many times a day in fact.  -->
トランクベース開発では、共有 trunk から頻繁に更新、取得、同期しなければなりません。1日に何回もやりましょう。

## 開発マシンでビルドを実行する

<!-- Developers practicing Trunk-Based Development run the build before a commit/push in order to not break the build.
This one practice, for very small teams, allows them to not set up a CI server until later. If they cannot push their 
commits to the shared trunk because someone else beat them to it then they have to do another update/sync/pull and
another build and push their revised commit(s). "It worked on my machine" says the developer that does not 
want to confess to breaking the build (assuming quick reliable idempotent builds). -->
トランクベース開発では、コミットをプッシュする前に開発マシンでビルドを壊していないか確認します。
小規模なチームにとって、CIサーバーの準備を後回しにさせてくれるプラクティスです。
誰かのコミットでビルドできないことが分かったら、自分の変更をコミットする前に、ビルドを正常化する修正を（コミットを）しなければなりません。
ビルドを壊してしまったことを認めなくない開発者は「自分のマシンでは大丈夫だったよ」と言いがちです（信頼性のある短時間で実行できるビルドが前提です）。

## コミットを整理する

<!-- Because everyone in a team is chasing HEAD of the trunk, there could be a moment where one change is imagined that
could inconvenience everyone else in some way. Say a big directory rename as part of an extensive rework of the 
source with it. It could be that the developer performing those two changes does the rename first and pushes it through to 
the trunk where everyone will get it on the next pull/sync/update. It might be that if separated somehow, the rename
on its own is easier to consume by teammates, with the second being a relatively smaller commit later. -->
チームメンバーは全員 trunk の HEAD を追跡します。
この場合、他の全員に悪影響を及ぼすような変更が考えられます。
それは、大規模なリファクタリングに伴う大量のディレクトリ名の変更です。
開発者はこの2種類の変更をするにあたって、ファイル名の変更だけをコミットしてプッシュする可能性があります。
ファイル名の変更だけなら受け取るのは容易ですし、残りの変更（コミット）は比較的小さくなる場合が多いからです。

<!-- Git and Mercurial track files through their content rather than by directory and file name, so they make light of the
situation described above, anyway. -->
Git や Mercurial はディレクトリとファイル名ではなく、ファイルの内容を追跡しています。
だから、そういう状況に対する配慮を忘れがちなのです。

## ビルドが壊れたら前より壊れにくくする

<!-- So because of that lazy developer, or the flaky build, or pure accident of timing (Google has a commit every 30
seconds into their monorepo - there must be quantum entangled commits on a 0.0001% basis), the trunk will be observed 
to be broken occasionally.  -->
開発者が怠慢なのか、ビルドがフレーキーなのか、純粋にタイミングの問題なのか（Google では30秒に1回新しいコミットが到着します。0.0001%のコミットには量子もつれ効果が観測されるでしょう）、いつかは trunk が壊れる場合があります。

<!-- The best implementations are going to perform automatic rollback of a broken commit that lands in the trunk. The
developer gets notified and they get to fix it quietly on their workstation. -->
ビルドを壊したコミットを自動的にロールバックする仕組みを作るのが一番いいでしょう。
通知に気付いた開発者はすぐに修正を始められます。

<!-- A developer wanting to update/pull/sync from the shared trunk often runs the risk of encountering that
statistically improbable broken build. They do not want to have the commits that broke the trunk, on their workstation
if they are developing. So what they do is update/pull/sync to the last known good commit, and only go further
ahead when the trunk build is officially repaired. This way they know they can stay 'green' on their workstation. Some 
companies engineer a system where the last known good commit hash/number is stored in a network share, and a shell 
script used for update/pull/sync does so to that instead of HEAD revision. -->
開発者に共有 trunk の更新、取得、同期を待たせるのが当たり前になっていると、統計的に証明できないビルドの失敗というリスクが発生するかもしれません。
自分が作業している開発マシンに、trunk のビルドを壊してしまうコミットを取得したくないから、最後にビルドの成功したコミットだけを取得するのです。
もしビルドが壊れていたら、それが直るまで待ち続けます。
そのためには、最後にビルドの成功したコミットのコミットハッシュやコミット番号をネットワークの共有フォルダなどに記録しておなければなりません。
また、HEAD を追跡する操作を実行するシェルスクリプトも必要になるでしょう。


### ビルド警察

<!-- If the Continuous Integration server is batching commits to trunk in each build, or the elapsed time for a build is
long then a "build cop" role might be required within the team to help sort out build breakages. Sadly that 
means that locking the trunk to prevent further checkins on top of the broken one might be necessary as some form
of bisecting is performed to work out which commit broke the build and should be rolled back. Obviously a Continuous
Integration server setup that can run one build per commit is best. -->
継続的インテグレーションサーバーが複数のコミットをまとめてビルドしているか、そもそもビルドに長い時間がかかる場合、リポジトリを交通整理する「ビルド警察」が必要になります。
つまり、ビルドを壊す可能性のあるチェックインを防ぐため trunk をロックして、ビルドを壊したロールバックしなければならないコミットを特定できるようにするのです。
なお、継続的インテグレーションサーバーでは、コミットごとにビルドするのが一番いいです。

## 何も共有しない

<!-- Developers on their workstations rely on a 'microcosm' setup for the application or service
they are developing. They can: -->
開発者は、自分の作業マシンに作成した「小世界」に閉じてアプリケーション（あるいはサービス）を開発します。

<!-- * bring up the application on their workstation and play with it.
* run all unit, integration and functional tests against it locally -->
* アプリケーションを起動して、動かす
* ユニットテスト、結合テスト、機能テストを実行する

<!-- Shared nothing requires significant discipline to achieve. It generally means that no TCP-IP leaves the developers
box, and being able to prove that by running those operations while disconnected from the network.  The 
implementing of the wire mocking (service virtualization) of dependent tiers outside the team, is a given. The highest 
accomplished Trunk-Based Development teams employ mocking of tiers within the same application, in order to make 
tests fast and stable. Technologies such as Mountebank{{< ext url="http://www.mbtest.org" >}} make 
programming working with wire mocking easy. Tiers refer to a layer-cake view of an applications construction, of course. -->
「何も共有しない」を実現するにはいろいろな条件を達成しなければなりません。
基本的に作業マシンをTCPネットワークから切り離したとしても、アプリケーションを実行できるようにしなければならないからです。
チームの外側に存在する依存層はモックで実装します（サービスの仮想化）。
高度に成熟したチームなら、アプリケーションの内部に依存層のモックを同梱しているかもしれません。
テストを高速化し、安定化するためです。
Mountebank{{< ext url="http://www.mbtest.org" >}} のようなライブラリを利用しましょう。

<!-- With a Microcosm strategy which delivers shared nothing for a developer workstation, it is acknowledged that
non-functional consistency with production has been thrown out of the window and that only functional correctness
is being honored.  This is only really any good for the act of development on a workstation, and the verification of 
that (per commit) by a Continuous Integration daemon.   -->
小世界戦略は、開発者の作業マシンで完結する「何も共有しない」を実現します。
これは、本番環境に求められる非機能特性を諦めて、機能面の正確さを追求する戦略として知られています。
作業マシンでの開発作業、CIデーモンによるコミットの検証にのみ有効です。

<!-- Your team will need many named QA environments, and many named
user acceptance testing (UAT) environments. Each of those with different rules about the frequency of deployment, and even perhaps even 
a temporarily reservation for different reasons. Those environments pull together **real** dependent services 
and integrated applications. As much as possible those environs should not have shared services.  -->
いくつものQA環境や、いくつものUAT環境が必要な場合もあります。
それぞれデプロイの周期は異なるし、いろいろな理由で占有されることがあります。
そして、こういう環境では、アプリケーションが **本物の外部サービスと** 連携しなければなりません。
共有サービスを別々にするのがせいぜいです。

<!-- Companies often make a classic mistake when buying software in that they (say) buy one license for prod, and another
for all dev, QA, and UAT, meaning the DevOps team had configured it as shared for all those environments, with a 
wide-ranging negative impact on productivity and quality for innumerable and sometimes subtle psychological reasons. -->
会社は、外部のソフトウェア製品を購入するのに、本番環境用だけでなく、それ以外の全ての環境用（開発、QA、UAT）にライセンスを購入してしまう、みたいな単純ミスをする場合があります。
DevOps チームは全ての環境の面倒を見なければなりません。
広い範囲に、生産性や数値化できない品質、心理的な負担として影響する可能性があります。


## コードの共同所有

<!-- Committing to the trunk many times a day requires a broad sense of ownership to code, and a willingness to allow
developers to contribute changes to sections of an application or service that they have not previously be involved 
with. This privilege does come with responsibilities and checks. The former is to standards, and the checks are by the CI server, 
and by humans who should honor to do a speedy code review. That last, for the highest performing teams, means as soon
as the proposed commit is ready. -->
1日に trunk へ何度もコミットするには、広い範囲のコードの所有権が必要になります。
また、アプリケーション（あるいはサービス）の自分が関与したことのない部分に関する貢献を認めなければなりません。
権利には責任と確認が伴います。
責任は当たり前のものとして、確認はCIサーバーの実行するCIと、人間の行う素早いコードレビューによって保証しなければなりません。
最高の成果を発揮するチームに必要なのは、コミットがすぐにリリースできる状態になる仕組みです。

## 常にリリース可能にする

<!-- Not only do developers practicing Trunk-Based Development not break the build with any commit, they also sign up to
being able to go live at short notice. For example, one hour, if the CIO visits and says it is going to happen. That
means there is a bunch of automated tests that come with the build. -->
トランクベース開発では、ビルドを壊すコミットをしないだけでなく、少しの手間でリリースできる状態を保たなければなりません。
例えば、CIOが指示してから1時間以内にリリースできるようにしなければなりません。
そのためには、ビルドするときにたくさんの自動テストを実行するようにしなければなりません。

## 垂直方向の薄い断面

<!-- Where possible stories or tasks that have been pulled from the backlog should be achievable by a developer or pair of
developers in a short period of time, and in a small number of commits. They should also transcend all the apparent 
tiers of the stack, and not have to jump between developers with specialized knowledge in order to be able to 
completed. The Agile industry donates the INVEST{{< ext url="https://en.wikipedia.org/wiki/INVEST_(mnemonic)" >}} principle 
as well as "Thin Vertical Slices"{{< ext url="http://www.scruminc.com/wp-content/uploads/2015/06/User-Stories-2.0.pdf" >}} 
for this purpose, and that are great enablers of high throughput commits to the trunk, and always being release ready. -->
バックログからプルするストーリー（あるいはタスク）は、開発者（あるいはペアになった開発者）が短期間に、少数のコミットで実現できるようにしなければなりません。
全ての技術スタックを通過するようにし、かつ、特殊な知識で途中の層をスキップできないようにしなければなりません。
アジャイル開発のコミュニティでは、INVEST{{< ext url="https://en.wikipedia.org/wiki/INVEST_(mnemonic)" >}} というストーリーに関する原則が知られています。
「垂直方向の薄い断面」{{< ext url="http://www.scruminc.com/wp-content/uploads/2015/06/User-Stories-2.0.pdf" >}} とも呼ばれています。
この考え方は、リリース可能な状態を保ちながら、trunk への高スループットなコミットを実現するために欠かせません。
